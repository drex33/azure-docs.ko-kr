---
title: Azure Private Link 서비스는 무엇입니까?
description: Azure Private Link 서비스에 대해 알아봅니다.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: a5eeb8fbadda24a811893a02f42b428a8365c5aa
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110262"
---
# <a name="what-is-azure-private-link-service"></a>Azure Private Link 서비스는 무엇입니까?

Azure Private Link 서비스는 Azure Private Link에서 제공하는 자체 서비스에 대한 참조입니다. [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) 뒤에서 실행되는 서비스는 서비스에 대한 소비자가 자신의 VNet에서 비공개로 액세스할 수 있도록 Private Link 액세스를 사용하도록 설정할 수 있습니다. 고객은 VNet 내에서 프라이빗 엔드포인트를 만들고 이 서비스에 매핑할 수 있습니다. 이 문서에서는 서비스 공급자 쪽과 관련된 개념을 설명합니다. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Private Link 서비스 워크플로" border="true":::

*그림: Azure Private Link 서비스*

## <a name="workflow"></a>워크플로

![Private Link 서비스 워크플로](media/private-link-service-overview/private-link-service-workflow.png)


*그림: Azure Private Link 서비스 워크플로*

### <a name="create-your-private-link-service"></a>Private Link 서비스 만들기

- 가상 네트워크의 표준 부하 분산 장치 뒤에서 실행되도록 애플리케이션을 구성합니다. 표준 부하 분산 장치 뒤에 애플리케이션을 이미 구성한 경우 이 단계를 건너뛸 수 있습니다.   
- 위의 부하 분산 장치를 참조하는 Private Link 서비스를 만듭니다. 부하 분산 장치 선택 프로세스에서 트래픽을 수신하려는 프런트 엔드 IP 구성을 선택합니다. Private Link 서비스의 NAT IP 주소에 대한 서브넷을 선택합니다. 서브넷에서 사용 가능한 NAT IP 주소를 8개 이상 사용하는 것이 좋습니다. 모든 소비자 트래픽은 서비스 공급자에 대한 이 개인 IP 주소 풀에서 시작된 것으로 나타납니다. Private Link 서비스에 대한 적절한 속성/설정을 선택합니다.    

    > [!NOTE]
    > Azure Private Link 서비스는 표준 Load Balancer에서만 지원됩니다. 
    
### <a name="share-your-service"></a>서비스 공유

Private Link 서비스를 만든 후 Azure는 서비스에 제공한 이름을 기반으로 "별칭"이라는 전역적으로 고유한 이름을 지정한 모니커를 생성합니다. 서비스의 별칭 또는 리소스 URI를 오프라인으로 고객과 공유할 수 있습니다. 소비자는 별칭 또는 리소스 URI를 사용하여 Private Link 연결을 시작할 수 있습니다.
 
### <a name="manage-your-connection-requests"></a>연결 요청 관리

소비자가 연결을 시작한 후 서비스 공급자는 연결 요청을 수락하거나 거부할 수 있습니다. 모든 연결 요청은 Private Link 서비스의 **privateendpointconnections** 속성 아래에 나열됩니다.
 
### <a name="delete-your-service"></a>서비스 삭제

Private Link 서비스가 더 이상 사용되지 않는 경우 삭제할 수 있습니다. 그러나 서비스를 삭제하기 전에 연결된 프라이빗 엔드포인트 연결이 없는지 확인합니다. 모든 연결을 거부하고 서비스를 삭제할 수 있습니다.

## <a name="properties"></a>속성

Private Link 서비스는 다음 속성을 지정합니다. 

|속성 |설명  |
|---------|---------|
|프로비저닝 상태(provisioningState)  |Private Link 서비스의 현재 프로비저닝 상태를 나열하는 읽기 전용 속성입니다. 적용 가능한 프로비저닝 상태는 "삭제 중, 실패, 성공, 업데이트 중"입니다. 프로비저닝 상태가 "성공"이면 Private Link 서비스를 성공적으로 프로비저닝한 것입니다.        |
|별칭(alias)     | 별칭은 서비스에 대한 전역적으로 고유한 읽기 전용 문자열입니다. 서비스에 대한 고객 데이터를 마스킹하는 데 도움이 되며 동시에 서비스에 대해 공유하기 쉬운 이름을 만듭니다. Private Link 서비스를 만들 때 Azure는 고객과 공유할 수 있는 서비스에 대한 별칭을 생성합니다. 고객은 이 별칭을 사용하여 서비스에 대한 연결을 요청할 수 있습니다.          |
|표시 유형(visibility)     | 표시 유형은 Private Link 서비스에 대한 노출 설정을 제어하는 속성입니다. 서비스 공급자는 Azure RBAC(역할 기반 액세스 제어) 권한이 있는 구독, 제한된 구독 집합 또는 모든 Azure 구독을 사용하여 서비스에 대한 노출을 제한하도록 선택할 수 있습니다.          |
|자동 승인(autoApproval)    |   자동 승인은 Private Link 서비스에 대한 자동화된 액세스를 제어합니다. 자동 승인 목록에 지정된 구독은 해당 구독의 프라이빗 엔드포인트에서 연결이 요청될 때 자동으로 승인됩니다.          |
|Load Balancer 프런트 엔드 IP 구성(loadBalancerFrontendIpConfigurations)    |    Private Link 서비스는 표준 Load Balancer의 프런트 엔드 IP 주소에 연결됩니다. 서비스를 대상으로 하는 모든 트래픽은 SLB의 프런트 엔드에 도달합니다. 애플리케이션이 실행되고 있는 적절한 백 엔드 풀로 이 트래픽을 보내도록 SLB 규칙을 구성할 수 있습니다. 부하 분산 장치 프런트 엔드 IP 구성은 NAT IP 구성과 다릅니다.      |
|NAT IP 구성(ipConfigurations)    |    이 속성은 Private Link 서비스에 대한 NAT(네트워크 주소 변환) IP 구성을 참조하세요. NAT IP는 서비스 공급자의 가상 네트워크에 있는 모든 서브넷에서 선택할 수 있습니다. Private Link 서비스는 프라이빗 링크 트래픽에 대한 대상 측 NAT를 수행합니다. 이렇게 하면 원본(소비자)와 대상(서비스 공급자)의 주소 공간 간에 IP 충돌이 발생하지 않습니다. 대상 쪽(서비스 공급자)에서 NAT IP 주소는 서비스에서 수신한 모든 패킷에 대해 원본 IP로 표시되고 서비스에서 전송된 모든 패킷에 대해 대상 IP로 표시됩니다.       |
|프라이빗 엔드포인트 연결(privateEndpointConnections)     |  이 속성은 Private Link 서비스에 연결하는 프라이빗 엔드포인트를 나열합니다. 여러 프라이빗 엔드포인트는 동일한 Private Link 서비스에 연결할 수 있으며, 서비스 공급자는 개별 프라이빗 엔드포인트의 상태를 제어할 수 있습니다.        |
|TCP 프록시 V2(EnableProxyProtocol)     |  이 속성을 통해 서비스 공급자는 TCP 프록시 V2를 사용하여 서비스 소비자에 대한 연결 정보를 검색할 수 있습니다. 서비스 공급자는 프록시 프로토콜 V2 헤더를 구문 분석할 수 있도록 수신자 구성을 설정해야 합니다.        |
|||


### <a name="details"></a>세부 정보

- Private Link 서비스는 모든 공용 지역의 승인된 프라이빗 엔드포인트에서 액세스할 수 있습니다. 프라이빗 엔드포인트는 프라이빗 VPN 또는 ExpressRoute 연결을 사용하여 동일한 가상 네트워크, 지역적으로 피어된 VNet, 전역적으로 피어된 VNet 및 온-프레미스에서 연결할 수 있습니다. 
 
- Private Link 서비스를 생성할 때 리소스의 수명 주기 동안 네트워크 인터페이스가 생성됩니다. 이 인터페이스는 고객이 관리할 수 없습니다.
 
- Private Link 서비스는 가상 네트워크 및 표준 Load Balancer와 동일한 지역에 배포해야 합니다.  
 
- 단일 Private Link 서비스는 다른 VNet, 구독 및/또는 Active Directory 테넌트에서 속한 여러 프라이빗 엔드포인트에서 액세스할 수 있습니다. 연결은 연결 워크플로를 통해 설정됩니다. 
 
- 여러 프런트 엔드 IP 구성을 사용하여 동일한 표준 Load Balancer에 여러 Private Link 서비스를 만들 수 있습니다. 표준 Load Balancer 및 구독당 만들 수 있는 Private Link 서비스 수에는 제한이 있습니다. 자세한 내용은  [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.
 
- Private Link 서비스에는 연결된 NAT IP 구성이 두 개 이상 있을 수 있습니다. NAT IP 구성을 두 개 이상 선택하면 서비스 공급자의 크기를 조정하는 데 도움이 될 수 있습니다. 현재 서비스 공급자는 Private Link 서비스당 최대 8개의 NAT IP 주소를 할당할 수 있습니다. 각 NAT IP 주소를 사용하여 TCP 연결에 더 많은 포트를 할당하여 스케일 아웃할 수 있습니다. Private Link 서비스에 여러 NAT IP 주소를 추가한 후에는 NAT IP 주소를 삭제할 수 없습니다. 이렇게 하면 NAT IP 주소를 삭제하는 동안 활성 연결이 영향을 받지 않습니다.


## <a name="alias"></a>Alias

**별칭** 은 서비스에 대해 전역적으로 고유한 이름입니다. 서비스에 대한 고객 데이터를 마스킹하는 데 도움이 되며 동시에 서비스에 대해 공유하기 쉬운 이름을 만듭니다. Private Link 서비스를 만들 때 Azure는 고객과 공유할 수 있는 서비스에 대한 별칭을 생성합니다. 고객은 이 별칭을 사용하여 서비스에 대한 연결을 요청할 수 있습니다.

별칭은 *접두사*.*GUID*.*접미사* 의 세 부분으로 구성됩니다.

- 접두사는 서비스 이름입니다. 고유한 접두사를 선택할 수 있습니다. "별칭"이 생성된 후에는 변경할 수 없으므로 적절한 접두사를 선택합니다.  
- GUID는 플랫폼에서 제공됩니다. 이렇게 하면 이름을 전역적으로 고유하게 만들 수 있습니다. 
- 접미사는 Azure에 의해 추가됩니다(예: *region*.azure.privatelinkservice). 

완전한 별칭: *접두사*. {GUID}.*region*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>서비스 노출 제어

Private Link 서비스는 **표시 유형** 설정에서 서비스 노출을 제어할 수 있는 세 가지 옵션을 제공합니다. 표시 유형 설정은 소비자가 서비스에 연결할 수 있는지 여부를 결정합니다. 최대 제한에서 최소 제한까지 표시 유형 설정 옵션은 다음과 같습니다.
 
- **역할 기반 액세스 제어만**: 소유한 다양한 VNet에서 서비스가 프라이빗 소비용으로 제공되는 경우 동일한 Active Directory 테넌트와 연결된 구독 내의 액세스 제어 메커니즘으로 RBAC를 사용할 수 있습니다. 
- **구독에 의해 제한됨**: 여러 테넌트에서 서비스를 사용하는 경우 신뢰하는 제한된 구독 세트로 노출을 제한할 수 있습니다. 권한 부여를 미리 승인할 수 있습니다.
- **별칭이 있는 모든 사용자**: 서비스를 퍼블릭으로 설정하고 Private Link 서비스 별칭이 있는 모든 사용자가 연결을 요청할 수 있게 하려면 이 옵션을 선택합니다. 

## <a name="control-service-access"></a>서비스 액세스 제어

Private Link 서비스에 노출된 소비자(표시 유형 설정에 의해 제어됨)는 자신의 VNet에 프라이빗 엔드포인트를 만들고 Private Link 서비스에 대한 연결을 요청할 수 있습니다. 프라이빗 엔드포인트 연결은 Private Link 서비스 개체에서 "보류 중"상태로 생성됩니다. 서비스 공급자는 연결 요청에 대한 동작을 담당합니다. 연결을 승인하거나, 연결을 거부하거나, 연결을 삭제할 수 있습니다. 승인된 연결만 Private Link 서비스로 트래픽을 보낼 수 있습니다.

Private Link 서비스의 자동 승인 속성을 사용하여 연결 승인 작업을 자동화할 수 있습니다. 자동 승인은 서비스 공급자가 서비스에 대한 자동화된 액세스를 위해 구독 집합을 사전 승인하는 기능입니다. 서비스 공급자가 자동 승인 목록에 추가하려면 고객이 구독을 오프라인으로 공유해야 합니다. 자동 승인은 표시 유형 배열의 하위 집합입니다. 표시 유형은 노출 설정을 제어하는 반면 자동 승인은 서비스에 대한 승인 설정을 제어합니다. 고객이 자동 승인 목록의 구독에서 연결을 요청하면 연결이 자동으로 승인되고 연결이 설정됩니다. 서비스 공급자는 더 이상 요청을 수동으로 승인할 필요가 없습니다. 반면, 고객이 자동 승인 배열이 아닌 표시 유형 배열의 구독에서 연결을 요청하는 경우 요청은 서비스 공급자에게 연결되지만 서비스 공급자는 연결을 수동으로 승인해야 합니다.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>TCP 프록시 v2를 사용하여 연결 정보 얻기

Private Link 서비스를 사용하는 경우 프라이빗 엔드포인트에서 들어오는 패킷의 원본 IP 주소는 공급자의 가상 네트워크에서 할당된 NAT IP를 사용하여 서비스 공급자 쪽에서 NAT(네트워크 주소 변환)입니다. 따라서 애플리케이션은 서비스 소비자의 실제 원본 IP 주소 대신 할당된 NAT IP 주소를 받습니다. 애플리케이션에 소비자 쪽의 실제 원본 IP 주소가 필요한 경우 서비스에서 프록시 프로토콜을 사용하도록 설정하고 프록시 프로토콜 헤더에서 정보를 검색할 수 있습니다. 원본 IP 주소 외에도 프록시 프로토콜 헤더는 프라이빗 엔드포인트의 LinkID를 전달합니다. 원본 IP 주소와 LinkID의 조합은 서비스 공급자가 소비자를 고유하게 식별하는 데 도움이 될 수 있습니다. 프록시 프로토콜에 대한 자세한 내용은 [여기](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)를 참조하세요. 

이 정보는 다음과 같이 사용자 지정 TLV(Type-Length-Value) 벡터를 사용하여 인코딩됩니다.

사용자 지정 TLV 세부 정보:

|필드 |길이(8진수)  |설명  |
|---------|---------|----------|
|형식  |1        |PP2_TYPE_AZURE (0xEE)|
|길이  |2      |값의 길이|
|값  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |프라이빗 엔드포인트의 LINKID를 나타내는 UINT32(4바이트)입니다. little endian 형식으로 인코딩됩니다.|

 > [!NOTE]
 > 서비스 공급자는 Private Link 서비스에서 프록시 프로토콜을 사용하는 경우 [사양](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)에 따라 프록시 프로토콜 헤더를 구문 분석하도록 표준 Load Balancer 뒤에 있는 서비스를 구성할 책임이 있습니다. Private Link 서비스에서 프록시 프로토콜 설정을 사용하도록 설정했지만 서비스 공급자의 서비스가 헤더를 구문 분석하도록 구성되지 않은 경우 요청이 실패합니다. 마찬가지로 Private Link 서비스에서 설정을 사용하도록 설정하지 않은 상태에서 서비스 공급자의 서비스가 프록시 프로토콜 헤더를 필요로 하는 경우 요청이 실패합니다. 프록시 프로토콜 설정을 사용하도록 설정하면 헤더에 클라이언트 정보가 없는 경우에도 프록시 프로토콜 헤더가 호스트에서 백 엔드 가상 머신으로 HTTP/TCP 상태 프로브에 포함됩니다. 

## <a name="limitations"></a>제한 사항

다음은 Private Link 서비스를 사용할 때의 알려진 제한 사항입니다.
- 표준 Load Balancer에서만 지원됩니다. 기본 Load Balancer에서는 지원되지 않습니다.  
- VM/VMSS를 사용할 때 NIC에서 백 엔드 풀이 구성된 표준 Load Balancer에서만 지원됩니다.
- IPv4 트래픽만 지원합니다.
- TCP 및 UDP 트래픽만 지원합니다.


## <a name="next-steps"></a>다음 단계
- [Azure PowerShell을 사용하여 Private Link 서비스 만들기](create-private-link-service-powershell.md)
- [Azure CLI를 사용하여 Private Link 서비스 만들기](create-private-link-service-cli.md)
