---
title: Azure HDInsight 클러스터에서 개인 링크를 사용 하도록 설정
description: Azure 개인 링크를 사용 하 여 외부 HDInsight 클러스터에 연결 하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 5ed9587c0c8bdb378206db70ca459cc8cd004b45
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215562"
---
# <a name="enable-private-link-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 개인 링크를 사용 하도록 설정

이 문서에서는 Azure 개인 링크를 사용 하 여 Microsoft 백본 네트워크를 통해 네트워크에서 개인용으로 HDInsight 클러스터에 연결 하는 방법을 알아봅니다. 이 문서는 [Azure HDInsight에서 클러스터 연결 제한](./hdinsight-restrict-public-connectivity.md)문서를 확장 한 것으로, 공용 연결을 제한 하는 방법을 중점적으로 설명 합니다. HDInsight 클러스터와 종속 리소스에 대 한 공용 연결을 원하는 경우 [Azure HDInsight에서 네트워크 트래픽 제어](./control-network-traffic.md)의 지침에 따라 클러스터의 연결을 제한 하는 것이 좋습니다.

개인 링크는 가상 네트워크 피어 링을 사용할 수 없거나 사용 하도록 설정 되지 않은 네트워크 간 시나리오에서 사용할 수 있습니다.

> [!NOTE]
> 공용 연결을 제한 하는 것은 개인 링크를 사용 하기 위한 필수 구성 요소 이며 동일한 기능으로 간주 해서는 안 됩니다.

개인 링크를 사용 하 여 HDInsight 클러스터에 연결 하는 기능은 선택적 기능이 며 기본적으로 사용 하지 않도록 설정 되어 있습니다. 이 기능은 `resourceProviderConnection` [Azure HDInsight에서 클러스터 연결 제한](./hdinsight-restrict-public-connectivity.md)문서에 설명 된 대로 네트워크 속성이 *아웃 바운드* 로 설정 된 경우에만 사용할 수 있습니다.

`privateLink`이 *사용* 으로 설정 되 면 내부 [표준 부하 분산 장치](../load-balancer/load-balancer-overview.md) (slbs)가 만들어지고 각 SLB에 대해 Azure 개인 링크 서비스가 프로 비전 됩니다. 개인 링크 서비스를 사용 하면 개인 끝점에서 HDInsight 클러스터에 액세스할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

표준 부하 분산 장치는 기본 부하 분산 장치에서 자동으로 [공용 아웃 바운드 NAT](../load-balancer/load-balancer-outbound-connections.md) 를 제공 하지 않습니다. 아웃 바운드 공용 HDInsight 종속성에 연결 하려면 [방화벽](./hdinsight-restrict-outbound-traffic.md)에서 제공 하는 nat 게이트웨이 또는 nat와 같은 고유한 nat 솔루션을 제공 해야 합니다. 

HDInsight 클러스터는 여전히 아웃바운드 종속성에 액세스해야 합니다. 이러한 아웃 바운드 종속성을 사용할 수 없는 경우 클러스터 만들기가 실패할 수 있습니다. 

### <a name="configure-a-default-network-security-group-on-the-subnet"></a>서브넷에서 기본 네트워크 보안 그룹 구성

HDInsight 클러스터를 배포 하려는 서브넷에 NSG (네트워크 보안 그룹)를 만들고 추가 합니다. 아웃 바운드 연결을 사용 하려면 NSG가 필요 합니다.

### <a name="disable-network-policies-for-the-private-link-service"></a>개인 링크 서비스에 대 한 네트워크 정책 사용 안 함

개인 링크 서비스를 성공적으로 만들려면 [개인 링크 서비스에 대 한 네트워크 정책을 명시적으로 사용 하지 않도록 설정](../private-link/disable-private-link-service-network-policy.md)해야 합니다.

### <a name="configure-a-nat-gateway-on-the-subnet"></a>서브넷에서 NAT 게이트웨이 구성

NAT에 대 한 방화벽 또는 NVA (네트워크 가상 어플라이언스)를 구성 하지 않으려는 경우에는 NAT 게이트웨이를 사용 하도록 선택할 수 있습니다. 그렇지 않으면 다음 필수 구성 요소로 건너뜁니다.

시작 하려면 virtual network의 새 공용 IP 주소를 사용 하 여 NAT 게이트웨이를 가상 네트워크의 구성 된 서브넷에 추가 합니다. 이 게이트웨이는 트래픽이 가상 네트워크 외부로 이동 해야 하는 경우 개인 내부 IP 주소를 공용 주소로 변환 하는 일을 담당 합니다.

### <a name="configure-a-firewall-optional"></a>방화벽 구성 (선택 사항)
기본 설치를 시작 하려면 다음을 수행 하세요.

1. 가상 네트워크에 *AzureFirewallSubnet* 라는 새 서브넷을 추가 합니다. 
1. 새 서브넷을 사용 하 여 새 방화벽을 구성 하 고 방화벽 정책을 추가 합니다. 
1. 새 방화벽의 개인 IP 주소를 `nextHopIpAddress` 경로 테이블의 값으로 사용 합니다. 
1. 가상 네트워크의 구성 된 서브넷에 경로 테이블을 추가 합니다.

방화벽 설정에 대 한 자세한 내용은 [Azure HDInsight에서 네트워크 트래픽 제어](./control-network-traffic.md)를 참조 하세요.

다음 다이어그램에서는 클러스터를 만들기 전에 필요한 네트워킹 구성의 예를 보여 줍니다. 이 예제에서 모든 아웃 바운드 트래픽은 사용자 정의 경로를 통해 Azure 방화벽에 강제 적용 됩니다. 필요한 아웃 바운드 종속성은 클러스터를 만들기 전에 방화벽에서 허용 되어야 합니다. Enterprise Security Package 클러스터의 경우 가상 네트워크 피어 링은 Azure Active Directory 도메인 서비스에 대 한 네트워크 연결을 제공할 수 있습니다.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="클러스터를 만들기 전의 개인 링크 환경 다이어그램.":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Azure HDInsight에 대 한 개인 끝점 관리

Azure HDInsight 클러스터에 대 한 [개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 가상 네트워크의 클라이언트가 [개인 링크](../private-link/private-link-overview.md)를 통해 클러스터에 안전 하 게 액세스할 수 있도록 할 수 있습니다. 가상 네트워크와 HDInsight 클러스터의 클라이언트 간 네트워크 트래픽은 Microsoft 백본 네트워크를 통해 이동 하 여 공용 인터넷에서 노출을 제거 합니다.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="개인 끝점 관리 환경을 다이어그램으로 제공 합니다.":::

개인 링크 서비스 소비자 (예: Azure Data Factory)는 두 가지 연결 승인 방법 중에서 선택할 수 있습니다.

* **자동**: 서비스 소비자에 게 HDInsight 리소스에 대 한 Azure RBAC (역할 기반 액세스 제어) 권한이 있는 경우 소비자가 자동 승인 방법을 선택할 수 있습니다. 이 경우 요청이 HDInsight 리소스에 도달 하면 HDInsight 리소스에서 조치가 필요 하지 않으며 연결이 자동으로 승인 됩니다.
* **수동**: 서비스 소비자에 게 HDInsight 리소스에 대 한 Azure RBAC 권한이 없는 경우 소비자는 수동 승인 방법을 선택할 수 있습니다. 이 경우 연결 요청은 HDInsight 리소스에 **보류 중** 으로 표시 됩니다. HDInsight 리소스에서 연결을 설정 하려면 먼저 요청을 수동으로 승인 해야 합니다. 

개인 끝점을 관리 하려면 Azure Portal의 클러스터 보기에서 **보안 + 네트워킹** 아래의 **네트워킹** 섹션으로 이동 합니다. 여기에서 기존 연결, 연결 상태 및 개인 끝점 세부 정보를 모두 볼 수 있습니다.

기존 연결을 승인, 거부 또는 제거할 수도 있습니다. 개인 연결을 만들 때 연결 하려는 HDInsight 하위 리소스 (예: 게이트웨이 또는 헤드 노드)를 지정할 수 있습니다.

다음 표에서는 다양 한 HDInsight 리소스 작업 및 개인 끝점에 대 한 결과 연결 상태를 보여 줍니다. An HDInsight 리소스는 소비자 개입 없이 나중에 개인 끝점 연결의 연결 상태를 변경할 수도 있습니다. 이 작업은 소비자 측 엔드포인트의 상태를 업데이트합니다.

| 서비스 공급자 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | Description |
| --------- | --------- | --------- |
| None | Pending | 연결이 수동으로 만들어지며 개인 링크 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | 개인 링크 리소스 소유자가 연결을 거부 했습니다. |
| 제거 | 연결 끊김 | 개인 링크 리소스 소유자가 연결을 제거 했습니다. 프라이빗 엔드포인트는 정보형으로 지정되며 정리를 위해 삭제해야 합니다. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>개인 끝점을 통해 연결 하도록 DNS 구성

네트워킹을 설정한 후 아웃 바운드 리소스 공급자 연결 및 개인 링크를 사용 하는 클러스터를 만들 수 있습니다.

개인 클러스터에 액세스 하려면 개인 링크 DNS 확장 및 개인 끝점을 사용할 수 있습니다. `privateLink`이 *사용* 으로 설정 되 면 개인 끝점을 만들고 개인 dns 영역을 통해 DNS 확인을 구성할 수 있습니다.

Azure에서 관리 되는 공용 DNS 영역에서 만든 개인 링크 항목은 다음과 같습니다 `azurehdinsight.net` .

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
다음 이미지는 피어 링 되지 않거나 클러스터에 직접적인 시야를 갖지 않는 가상 네트워크에서 클러스터에 액세스할 수 있도록 구성 된 개인 DNS 항목의 예를 보여 줍니다. Azure DNS 개인 영역을 사용 하 여 `*.privatelink.azurehdinsight.net` fqdn (정규화 된 도메인 이름)을 재정의 하 고 클라이언트 네트워크에서 개인 끝점의 IP 주소를 확인할 수 있습니다. 이 예에서는에 대 한 구성 `<clustername>.azurehdinsight.net` 이지만 다른 클러스터 끝점으로도 확장 됩니다.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="개인 링크 아키텍처의 다이어그램입니다.":::

## <a name="create-clusters"></a>클러스터 만들기

다음 JSON 코드 조각에는 사설 HDInsight 클러스터를 만들기 위해 Azure Resource Manager 템플릿에서 구성 해야 하는 두 가지 네트워크 속성이 포함 되어 있습니다.

```json
networkProperties: {
    "resourceProviderConnection": "Outbound",
    "privateLink": "Enabled"
}
```

Private Link를 포함하여 많은 HDInsight 엔터프라이즈 보안 기능이 있는 전체 템플릿은 [HDInsight 엔터프라이즈 보안 템플릿](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)을 참조하세요.

PowerShell을 사용 하 여 클러스터를 만들려면 [예](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)를 참조 하세요.

Azure CLI를 사용 하 여 클러스터를 만들려면 [예제](/cli/azure/hdinsight#az_hdinsight_create-examples)를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight용 Enterprise Security Package](enterprise-security-package.md)
* [Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침](./domain-joined/general-guidelines.md)