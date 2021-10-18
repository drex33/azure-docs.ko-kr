---
title: Azure HDInsight 클러스터에서 개인 링크를 사용 하도록 설정
description: Azure 개인 링크를 사용 하 여 HDInsight 클러스터 외부에서 연결 하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 25db2af44a133bd3cab98070b29497f089e923f0
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129190"
---
# <a name="enable-private-link-on-hdinsight-cluster"></a>HDInsight 클러스터에서 개인 링크를 사용 하도록 설정

## <a name="overview"></a>개요
이 문서에서는 Azure 개인 링크를 활용 하 여 Microsoft 백본 네트워크를 통해 네트워크에서 개별적으로 HDInsight 클러스터에 연결 하는 방법을 알아봅니다. 이 문서는 공용 연결을 제한 하는 데 초점을 맞춘 [Azure HDInsight에서 클러스터 연결을 제한](./hdinsight-restrict-public-connectivity.md) 하는 주 문서를 확장 한 것입니다. HDInsight 클러스터와 종속 리소스에 대 한 공용 연결을 옵트인 (opt in) 할 수 있는 경우 [Azure HDInsight에서 네트워크 트래픽 제어](./control-network-traffic.md) 의 지침에 따라 클러스터의 연결을 제한 하는 것이 좋습니다.

개인 링크는 가상 네트워크 피어 링을 사용할 수 없거나 사용할 수 없는 가상 네트워크 간 시나리오에서 활용할 수 있습니다.

> [!NOTE]
> 공용 연결을 제한 하는 것은 개인 링크를 사용 하기 위한 필수 구성 요소 이며 동일한 기능으로 간주 되어서는 안 됩니다.

개인 링크는 선택적 기능이 며 기본적으로 사용 하지 않도록 설정 되어 있습니다. 이 기능은 `resourceProviderConnection` [Azure HDInsight에서 클러스터 연결 제한](./hdinsight-restrict-public-connectivity.md)문서에 설명 된 대로 네트워크 속성이 *아웃 바운드* 로 설정 된 경우에만 사용할 수 있습니다.

`privateLink`이 *사용* 으로 설정 되 면 내부 [표준 부하 분산 장치](../load-balancer/load-balancer-overview.md) (SLB)가 만들어지고 각 Slb에 대해 Azure 개인 링크 서비스가 프로 비전 됩니다. Private Link 서비스를 사용하면 프라이빗 엔드포인트에서 HDInsight 클러스터에 액세스할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

표준 부하 분산 장치는 기본 부하 분산 장치에서 자동으로 [공용 아웃 바운드 NAT](../load-balancer/load-balancer-outbound-connections.md) 를 제공 하지 않습니다. 아웃 바운드 공용 HDInsight 종속성에 연결 하기 위해 [방화벽](./hdinsight-restrict-outbound-traffic.md)에서 제공 하는 nat 게이트웨이 또는 nat와 같은 고유한 nat 솔루션을 제공 해야 합니다. HDInsight 클러스터는 여전히 아웃바운드 종속성에 액세스해야 합니다. 이러한 아웃 바운드 종속성을 사용할 수 없는 경우 클러스터 만들기가 실패할 수 있습니다. 아웃 바운드 연결을 사용 하도록 설정 하려면 서브넷에 네트워크 보안 그룹도 구성 해야 합니다.

### <a name="1--configure-a-default-network-security-group-nsg-on-the-subnet"></a>1. 서브넷에서 기본 NSG (네트워크 보안 그룹) 구성

HDInsight 클러스터를 배포 하려는 서브넷에서 네트워크 보안 그룹을 만들고 추가 합니다.

### <a name="2--disable-network-policies-for-private-link-service"></a>2. 개인 링크 서비스에 대 한 네트워크 정책을 사용 하지 않도록 설정

개인 링크 서비스를 성공적으로 만들려면 [개인 링크 서비스에 대 한 네트워크 정책을 명시적으로 사용 하지 않도록 설정](../private-link/disable-private-link-service-network-policy.md)해야 합니다.

### <a name="3--configure-a-nat-gateway-on-the-subnet"></a>3. 서브넷에서 NAT 게이트웨이 구성

NAT에 대 한 방화벽 또는 Nva (네트워크 가상 어플라이언스)를 구성 하지 않으려는 경우 NAT 게이트웨이를 사용 하도록 선택할 수 있습니다. 그렇지 않으면 다음 필수 구성 요소로 건너뜁니다.

시작 하려면 가상 네트워크의 구성 된 서브넷에 NAT 게이트웨이 (가상 네트워크에 새 공용 IP 주소 포함)를 추가 하기만 하면 됩니다. 이 게이트웨이는 트래픽이 가상 네트워크 외부로 이동 해야 하는 경우 개인 내부 IP 주소를 공용 주소로 변환 하는 일을 담당 합니다.

### <a name="4--using-firewall-or-network-virtual-appliance-nvas-for-nat-optional"></a>4. NAT에 방화벽 또는 Nva (네트워크 가상 어플라이언스) 사용 (선택 사항)
기본 설치를 시작 하려면 먼저 가상 네트워크에 "AzureFirewallSubnet" 라는 새 서브넷을 추가 합니다. 만든 후에는이 서브넷을 사용 하 여 새 방화벽을 구성 하 고 방화벽 정책을 추가 합니다. 방화벽이 설정 된 후에는이 방화벽의 개인 IP를 `nextHopIpAddress` 경로 테이블에서로 사용 합니다. 가상 네트워크의 구성 된 서브넷에이 경로 테이블을 추가 합니다.
방화벽 설정에 대 한 자세한 내용은 [Azure HDInsight에서 네트워크 트래픽 제어](./control-network-traffic.md) 를 참조 하세요.

다음 다이어그램에서는 클러스터를 만들기 전에 필요한 네트워킹 구성의 예를 보여 줍니다. 이 예제에서 모든 아웃바운드 트래픽은 UDR을 사용하여 Azure Firewall로 강제되며 클러스터를 만들기 전에 방화벽에서 필요한 아웃바운드 종속성을 “허용”해야 합니다. Enterprise Security Package 클러스터의 경우 VNet 피어링을 통해 Azure Active Directory Domain Services 대한 네트워크 연결을 제공할 수 있습니다.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="클러스터를 만들기 전의 프라이빗 링크 환경 다이어그램":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Azure HDInsight에 대 한 개인 끝점 관리

Azure HDInsight 클러스터에 대 한 [개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 VNet (가상 네트워크)의 클라이언트가 [개인 링크](../private-link/private-link-overview.md)를 통해 클러스터에 안전 하 게 액세스할 수 있도록 할 수 있습니다. VNet의 클라이언트와 HDInsight 클러스터 간의 네트워크 트래픽은 Microsoft 백본 네트워크를 통해 이동 하 여 공용 인터넷에서 노출 되는 것을 제거 합니다.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="개인 끝점 관리 환경 다이어그램":::

Private Link 서비스 소비자가 선택할 수 있는 두 가지 연결 승인 방법이 있습니다.
* **자동**: 서비스 소비자에 게 HDInsight 리소스에 대 한 Azure RBAC 권한이 있는 경우 소비자는 자동 승인 방법을 선택할 수 있습니다. 이 경우 요청이 HDInsight 리소스에 도달 하면 HDInsight 리소스에서 조치가 필요 하지 않으며 연결이 자동으로 승인 됩니다.
* **수동**: 반대로 서비스 소비자에 게 HDInsight 리소스에 대 한 Azure RBAC 권한이 없는 경우 소비자는 수동 승인 방법을 선택할 수 있습니다. 이 경우 연결 요청은 HDInsight 리소스에 보류 중으로 표시 됩니다. 연결을 설정 하려면 먼저 HDInsight 리소스에서 요청을 수동으로 승인 해야 합니다. 

개인 끝점을 관리 하려면 Azure Portal의 클러스터 보기에서 보안 + 네트워킹 아래의 네트워킹 섹션으로 이동 합니다. 여기에서 기존 연결, 연결 상태 및 개인 끝점 세부 정보를 모두 볼 수 있습니다.
기존 연결을 승인, 거부 또는 제거할 수도 있습니다. 개인 연결을 만들 때 HDInsight 하위 리소스 (게이트웨이, 헤드 노드)를 지정할 수 있습니다. 등) 에도 연결 하려고 합니다.

아래 표에서는 다양 한 HDInsight 리소스 작업 및 개인 끝점에 대 한 결과 연결 상태를 보여 줍니다. HDInsight 리소스는 나중에 소비자 개입 없이 개인 끝점 연결의 연결 상태를 변경할 수도 있습니다. 이 작업은 소비자 측 엔드포인트의 상태를 업데이트합니다.

| 서비스 공급자 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | 설명 |
| --------- | --------- | --------- |
| None | Pending | 연결이 수동으로 만들어지며 개인 링크 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | 개인 링크 리소스 소유자가 연결을 제거 했습니다. 개인 끝점은 정보를 제공 하 고 정리를 위해 삭제 해야 합니다. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>개인 끝점을 통해 연결 하도록 DNS 구성

네트워킹을 설정한 후에는 다음 그림과 같이 아웃바운드 리소스 공급자 연결 및 프라이빗 링크가 활성화된 클러스터를 만들 수 있습니다.
개인 클러스터에 액세스 하려면 개인 링크 DNS 확장 및 개인 끝점을 사용할 수 있습니다. `privateLink`설정이 사용으로 설정 되 면 개인 끝점을 만들고 개인 dns 영역을 통해 dns 확인을 구성할 수 있습니다.
`azurehdinsight.net`이라는 Azure 관리형 퍼블릭 DNS 영역에서 만든 Private Link 항목은 다음과 같습니다.

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
다음 이미지는 피어 링 되지 않거나 클러스터에 직접적인 시야를 갖지 않는 가상 네트워크에서 클러스터에 액세스할 수 있도록 구성 된 개인 DNS 항목의 예를 보여 줍니다. Azure 개인 영역을 사용 하 여 fqdn을 재정의 하 `*.privatelink.azurehdinsight.net` 고 클라이언트 네트워크에서 개인 끝점 IP 주소를 확인할 수 있습니다.
이는에 대해서만 표시 `<clustername>.azurehdinsight.net` 되지만 다른 클러스터 끝점으로도 확장 됩니다.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="프라이빗 링크 아키텍처 다이어그램":::

## <a name="how-to-create-clusters"></a>클러스터를 만드는 방법
### <a name="use-arm-template-properties"></a>ARM 템플릿 속성 사용

다음 JSON 코드 조각에는 프라이빗 HDInsight 클러스터를 만들기 위해 ARM 템플릿에서 구성해야 하는 두 가지 네트워크 속성이 있습니다.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled"
}
```

Private Link를 포함하여 많은 HDInsight 엔터프라이즈 보안 기능이 있는 전체 템플릿은 [HDInsight 엔터프라이즈 보안 템플릿](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)을 참조하세요.

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

PowerShell을 사용하려면 [여기](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)에서 예제를 참조하세요.

### <a name="use-azure-cli"></a>Azure CLI 사용
Azure CLI를 사용하려면 [여기](/cli/azure/hdinsight#az_hdinsight_create-examples)에서 예제를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight용 Enterprise Security Package](enterprise-security-package.md)
* [Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침](./domain-joined/general-guidelines.md)