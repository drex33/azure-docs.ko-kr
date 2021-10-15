---
title: Azure HDInsight 클러스터에서 Private Link 사용
description: Azure Private Link 사용하여 HDInsight 클러스터 외부에 연결하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: a9461f70f896e50b1ed82c7b301ea03dde1cd7ec
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063073"
---
# <a name="enable-private-link-on-hdinsight-cluster"></a>HDInsight 클러스터에서 Private Link 사용

## <a name="overview"></a>개요
이 문서에서는 Azure Private Link 활용하여 Microsoft 백본 네트워크를 통해 네트워크를 통해 HDInsight 클러스터에 비공개로 연결하는 방법을 알아봅니다. 이 문서는 공용 [연결 제한에](./hdinsight-restrict-public-connectivity.md) 초점을 맞춘 Azure HDInsight 클러스터 연결 제한 기본 문서의 확장입니다. HDInsight 클러스터 및 종속 리소스에 대한 공용 연결을 선택할 수 있는 경우 의 네트워크 트래픽 제어 지침에 따라 클러스터의 연결을 제한하는 [것이 좋습니다Azure HDInsight](./control-network-traffic.md)

Private Link VNet 피어링을 사용할 수 없거나 사용하도록 설정되지 않은 VNet 간 시나리오에서 활용할 수 있습니다. 예를 들어 Azure Data Factory Azure HDInsight 통합하려는 경우 규정 준수 및 보안상의 이유로 프라이빗 네트워크(즉, 프라이빗 링크)를 통해 HDInsight 클러스터에 Azure Data Factory 연결해야 합니다.

> [!NOTE]
> 공용 연결을 제한하는 것은 Private Link 사용하도록 설정하기 위한 필수 조건이며 동일한 기능으로 간주해서는 안 됩니다.

Private Link 선택적 기능이며 기본적으로 사용하지 않도록 설정됩니다. 이 기능은 Azure HDInsight `resourceProviderConnection` 클러스터 연결 제한 문서에 설명된 대로 네트워크 속성이 *아웃바운드로* 설정된 경우에만 사용할 [수](./hdinsight-restrict-public-connectivity.md)있습니다.

`privateLink`를 ‘사용하도록 설정’하면 내부 [SLB](../load-balancer/load-balancer-overview.md)(표준 부하 분산 장치)가 생성되고 SLB마다 Azure Private Link 서비스가 프로비저닝됩니다. Private Link 서비스를 사용하면 프라이빗 엔드포인트에서 HDInsight 클러스터에 액세스할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

표준 부하 분산은 기본 부하 분산과 마찬가지로 [퍼블릭 아웃바운드 NAT를](../load-balancer/load-balancer-outbound-connections.md) 자동으로 제공하지 않습니다. 아웃바운드 공용 HDInsight에 연결하려면 NAT 게이트웨이 또는 [방화벽에서](./hdinsight-restrict-outbound-traffic.md)제공하는 NAT와 같은 고유한 NAT 솔루션을 제공해야 합니다. HDInsight 클러스터는 여전히 아웃바운드 종속성에 액세스해야 합니다. 이러한 아웃바운드 dependencies가 허용되지 않으면 클러스터 만들기가 실패할 수 있습니다. 아웃바운드 연결을 사용하려면 서브넷에 네트워크 보안 그룹도 구성해야 합니다.

### <a name="1--configure-a-default-network-security-group-nsg-on-the-subnet"></a>1. 서브넷에서 기본 NSG(네트워크 보안 그룹) 구성

HDInsight 클러스터를 배포하려는 서브넷에 네트워크 보안 그룹을 만들고 추가합니다.

### <a name="2--disable-network-policies-for-private-link-service"></a>2. 프라이빗 링크 서비스에 대한 네트워크 정책 사용 안 함

프라이빗 링크 서비스를 성공적으로 만들려면 프라이빗 링크 서비스에 대한 네트워크 정책을 명시적으로 [사용하지 않도록 설정해야 합니다.](../private-link/disable-private-link-service-network-policy.md)

### <a name="3--configure-a-nat-gateway-on-the-subnet"></a>3. 서브넷에서 NAT Gateway 구성

NAT에 대해 방화벽 또는 NLA(네트워크 가상 어플라이언스)를 구성하지 않으려면 NAT 게이트웨이를 사용하도록 선택할 수 있습니다. 그렇지 않으면 다음 필수 조건으로 건너뜁니다.

시작하려면 가상 네트워크의 구성된 서브넷에 NAT 게이트웨이(가상 네트워크에 새 공용 IP 주소가 있는)를 추가하기만 하면 됩니다. 이 게이트웨이는 트래픽이 가상 네트워크 외부로 이동해야 하는 경우 개인 내부 IP 주소를 공용 주소로 변환하는 작업을 담당합니다.

### <a name="4--using-firewall-or-network-virtual-appliance-nvas-for-nat-optional"></a>4. NAT에 방화벽 또는 NLA(네트워크 가상 어플라이언스) 사용(선택 사항)
기본 설정을 시작하려면 먼저 가상 네트워크에 새 서브넷 "AzureFirewallSubnet"을 추가합니다. 만든 후에는 이 서브넷을 사용하여 새 방화벽을 구성하고 방화벽 정책을 추가합니다. 방화벽이 설정되면 이 방화벽의 개인 IP를 `nextHopIpAddress` 경로 테이블의 로 사용합니다. 가상 네트워크의 구성된 서브넷에 이 경로 테이블을 추가합니다.
방화벽 설정에 대한 자세한 내용은 [Azure HDInsight 네트워크 트래픽 제어를](./control-network-traffic.md) 참조하세요.

다음 다이어그램에서는 클러스터를 만들기 전에 필요한 네트워킹 구성의 예를 보여 줍니다. 이 예제에서 모든 아웃바운드 트래픽은 UDR을 사용하여 Azure Firewall로 강제되며 클러스터를 만들기 전에 방화벽에서 필요한 아웃바운드 종속성을 “허용”해야 합니다. Enterprise Security Package 클러스터의 경우 VNet 피어링을 통해 Azure Active Directory Domain Services 대한 네트워크 연결을 제공할 수 있습니다.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="클러스터를 만들기 전의 프라이빗 링크 환경 다이어그램":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Azure HDInsight 대한 프라이빗 엔드포인트 관리

Azure HDInsight 클러스터에 [프라이빗 엔드포인트를](../private-link/private-endpoint-overview.md) 사용하여 VNet(가상 네트워크)의 클라이언트가 [프라이빗 링크를](../private-link/private-link-overview.md)통해 클러스터에 안전하게 액세스할 수 있도록 할 수 있습니다. VNet의 클라이언트와 HDInsight 클러스터 간의 네트워크 트래픽은 Microsoft 백본 네트워크를 통과하여 공용 인터넷에서 노출을 제거합니다.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="프라이빗 엔드포인트 관리 환경 다이어그램":::

Private Link 서비스 소비자(예: Azure Data Factory)가 선택할 수 있는 두 가지 연결 승인 방법이 있습니다.
* **자동:** 서비스 소비자에게 HDInsight 리소스에 대한 Azure RBAC 권한이 있는 경우 소비자는 자동 승인 방법을 선택할 수 있습니다. 이 경우 요청이 HDInsight 리소스에 도달하면 HDInsight 리소스에서 아무 작업도 필요하지 않으며 연결이 자동으로 승인됩니다.
* **수동:** 반면, 서비스 소비자에게 HDInsight 리소스에 대한 Azure RBAC 권한이 없는 경우 소비자는 수동 승인 방법을 선택할 수 있습니다. 이 경우 연결 요청이 HDInsight 리소스에 보류 중으로 표시됩니다. 연결을 설정하려면 HDInsight 리소스에서 요청을 수동으로 승인해야 합니다. 

프라이빗 엔드포인트를 관리하려면 Azure Portal의 클러스터 보기에서 보안 + 네트워킹 아래의 네트워킹 섹션으로 이동합니다. 여기서는 모든 기존 연결, 연결 상태 및 프라이빗 엔드포인트 세부 정보를 볼 수 있습니다.
기존 연결을 승인, 거부 또는 제거할 수도 있습니다. 프라이빗 연결을 만들 때 HDInsight 하위 리소스(게이트웨이, 헤드 노드)를 지정할 수 있습니다. 등) 에 연결하려고 합니다.

아래 표에서는 다양한 HDInsight 리소스 작업 및 프라이빗 엔드포인트에 대한 결과 연결 상태를 보여줍니다. HDInsight 리소스는 나중에 소비자 개입 없이 프라이빗 엔드포인트 연결의 연결 상태를 변경할 수도 있습니다. 이 작업은 소비자 측 엔드포인트의 상태를 업데이트합니다.

| 서비스 공급자 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | 설명 |
| --------- | --------- | --------- |
| None | Pending | 연결이 수동으로 만들어지고 Private Link 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | 프라이빗 링크 리소스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하고 정리를 위해 삭제해야 합니다. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>프라이빗 엔드포인트를 통해 연결하도록 DNS 구성

네트워킹을 설정한 후에는 다음 그림과 같이 아웃바운드 리소스 공급자 연결 및 프라이빗 링크가 활성화된 클러스터를 만들 수 있습니다.
프라이빗 클러스터에 액세스하려면 Private Link DNS 확장 및 프라이빗 엔드포인트를 사용할 수 있습니다. `privateLink`설정이 사용으로 설정된 경우 프라이빗 엔드포인트를 만들고 프라이빗 DNS 영역을 통해 DNS 확인 을 구성할 수 있습니다.
`azurehdinsight.net`이라는 Azure 관리형 퍼블릭 DNS 영역에서 만든 Private Link 항목은 다음과 같습니다.

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
다음 이미지에서는 피어되지 않았거나 클러스터에 직접 연결되지 않은 가상 네트워크에서 클러스터에 액세스할 수 있도록 구성된 프라이빗 DNS 항목의 예를 보여 줍니다. Azure Private Zone을 사용하여 `*.privatelink.azurehdinsight.net` FQDN을 재정의하고 클라이언트 네트워크의 프라이빗 엔드포인트 IP 주소를 확인할 수 있습니다.
이는 에 대해서만 `<clustername>.azurehdinsight.net` 표시되지만 다른 클러스터 엔드포인트로도 확장됩니다.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="프라이빗 링크 아키텍처 다이어그램":::

## <a name="how-to-create-clusters"></a>클러스터를 만드는 방법
### <a name="use-arm-template-properties"></a>ARM 템플릿 속성 사용

다음 JSON 코드 조각에는 프라이빗 HDInsight 클러스터를 만들기 위해 ARM 템플릿에서 구성해야 하는 두 가지 네트워크 속성이 있습니다.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
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