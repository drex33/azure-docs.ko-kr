---
title: 프라이빗 엔드포인트를 사용하여 비공개로 Azure 웹앱에 연결
description: Azure 프라이빗 엔드포인트를 사용하여 비공개로 웹앱에 연결
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 07/01/2021
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 8bd454d7f49b0dce46ba827c8cc5c133c56ad0d3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227248"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>Azure 웹앱용 프라이빗 엔드포인트 사용

> [!IMPORTANT]
> 프라이빗 엔드포인트는 **PremiumV2**, **PremiumV3**, **Functions Premium**(Elastic Premium 요금제라고도 함) 등의 App Service 요금제에서 컨테이너화 여부에 상관없이 호스트되는 Windows 및 Linux 웹앱에서 사용할 수 있습니다. 

Azure 웹앱용 프라이빗 엔드포인트를 사용하여 프라이빗 네트워크에 있는 클라이언트가 Private Link를 통해 앱에 안전하게 액세스할 수 있도록 할 수 있습니다. 프라이빗 엔드포인트는 Azure VNet 주소 공간의 IP 주소를 사용합니다. 프라이빗 네트워크에 있는 클라이언트와 웹앱 간의 네트워크 트래픽은 VNet을 통해 이동하고 Microsoft 백본 네트워크의 Private Link를 통해 공용 인터넷 노출을 방지합니다.

웹앱용 프라이빗 엔드포인트를 사용하면 다음을 수행할 수 있습니다.

- 프라이빗 엔드포인트를 구성하여 웹앱을 보호함으로써 공개 노출을 방지합니다.
- VPN 또는 ExpressRoute 프라이빗 피어링을 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 웹앱에 안전하게 연결합니다.
- VNet에서 데이터 반출을 방지합니다. 

VNet과 웹앱 사이에 보안 연결이 필요한 경우 서비스 엔드포인트가 가장 간단한 솔루션입니다. Azure 게이트웨이, 지역 피어링 VNet 또는 전역 피어링 VNet을 통해 온-프레미스에서 웹앱에 연결해야 하는 경우 프라이빗 엔드포인트가 그 솔루션입니다.  

자세한 내용은 [서비스 엔드포인트][serviceendpoint]를 참조하세요.

## <a name="conceptual-overview"></a>개념적 개요

프라이빗 엔드포인트는 VNet(가상 네트워크)의 서브넷에 있는 Azure 웹앱을 위한 특수 NIC(네트워크 인터페이스)입니다.
웹앱용 프라이빗 엔드포인트를 만들면 프라이빗 네트워크의 클라이언트와 웹앱 간에 보안 연결을 제공합니다. 프라이빗 엔드포인트에는 VNet의 IP 주소 범위에서 IP 주소가 할당됩니다.
프라이빗 엔드포인트와 웹앱 간의 연결은 보안 [Private Link][privatelink]를 사용합니다. 프라이빗 엔드포인트는 웹앱으로 들어오는 흐름에만 사용됩니다. 나가는 흐름은 이 프라이빗 엔드포인트를 사용하지 않지만 [VNet 통합 기능][vnetintegrationfeature]을 통해 다른 서브넷의 네트워크에 나가는 흐름을 주입할 수 있습니다.

앱의 각 슬롯은 별도로 구성됩니다. 슬롯당 최대 100개의 프라이빗 엔드포인트를 연결할 수 있습니다. 슬롯 간에 프라이빗 엔드포인트를 공유할 수는 없습니다.

프라이빗 엔드포인트를 연결하는 서브넷에 다른 리소스가 있을 수 있고 빈 전용 서브넷이 필요하지 않습니다.
웹앱과는 다른 지역에 프라이빗 엔드포인트를 배포할 수도 있습니다. 

> [!Note]
>VNet 통합 기능은 프라이빗 엔드포인트와 동일한 서브넷을 사용할 수 없습니다. 이는 VNet 통합 기능의 제한 사항입니다.

보안 관점에서

- 웹앱용 프라이빗 엔드포인트를 사용하도록 설정하면 모든 공용 액세스가 사용 안 함으로 설정됩니다.
- 다른 지역의 VNet을 포함하여 다른 VNet 및 서브넷에서 여러 프라이빗 엔드포인트를 사용하도록 설정할 수 있습니다.
- 프라이빗 엔드포인트 NIC의 IP 주소는 동적이어야 하지만 프라이빗 엔드포인트를 삭제할 때까지 동일하게 유지됩니다.
- 프라이빗 엔드포인트의 NIC에는 연결된 NSG가 있을 수 없습니다.
- 프라이빗 엔드포인트를 호스트하는 서브넷에는 연결된 NSG가 있을 수 있지만 프라이빗 엔드포인트에 대한 네트워크 정책 적용을 해제해야 합니다. [프라이빗 엔드포인트에 대한 네트워크 정책 사용 안 함][disablesecuritype]을 참조하세요. 따라서 NSG를 기준으로 프라이빗 엔드포인트에 대한 액세스를 필터링할 수 없습니다.
- 웹앱용 프라이빗 엔드포인트를 사용하도록 설정하면 웹앱의 [액세스 제한][accessrestrictions] 구성이 평가되지 않습니다.
- 대상이 태그 인터넷 또는 Azure 서비스인 모든 NSG 규칙을 제거하여 VNet에서 데이터 반출 위험을 제거할 수 있습니다. 웹앱용 프라이빗 엔드포인트를 배포하는 경우 프라이빗 엔드포인트를 통해서만 이 특정 웹앱에 연결할 수 있습니다. 다른 웹앱이 있는 경우 이 다른 웹앱에 대한 다른 전용 프라이빗 엔드포인트를 배포해야 합니다.

웹앱의 웹 HTTP 로그에서 클라이언트 원본 IP를 찾을 수 있습니다. 이 기능은 클라이언트 IP 속성을 웹앱까지 전달하는 TCP 프록시 프로토콜을 사용하여 구현됩니다. 자세한 내용은 [TCP 프록시 v2를 사용하여 연결 정보 가져오기][tcpproxy]를 참조하세요.


  > [!div class="mx-imgBorder"]
  > ![웹앱 프라이빗 엔드포인트 전체 개요](media/private-endpoint/global-schema-web-app.png)


## <a name="dns"></a>DNS

웹 앱에 프라이빗 엔드포인트를 사용하는 경우 요청된 URL은 웹 앱의 이름과 일치해야 합니다. 기본값은 mywebappname.azurewebsites.net입니다.

기본적으로 프라이빗 엔드포인트를 사용하지 않는 웹 앱의 공개 이름은 클러스터에 대한 정식 이름입니다.
예를 들어 이름 확인은 다음과 같습니다.

|이름 |Type |값 |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


프라이빗 엔드포인트를 배포할 때 정식 이름 mywebapp.privatelink.azurewebsites.net을 가리키도록 DNS 항목을 업데이트합니다.
예를 들어 이름 확인은 다음과 같습니다.

|이름 |Type |값 |설명 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<--이 공용 IP는 프라이빗 엔드포인트가 아니므로 403오류를 받게 됩니다|

프라이빗 DNS 서버 또는 Azure DNS 프라이빗 영역을 설정해야 합니다. 테스트를 위해 테스트 컴퓨터의 호스트 항목을 수정할 수 있습니다.
만들어야 하는 DNS 영역은 **privatelink.azurewebsites.net** 입니다. A 레코드와 프라이빗 엔드포인트 IP를 사용하여 웹 앱에 대한 레코드를 등록합니다.
예를 들어 이름 확인은 다음과 같습니다.

|이름 |Type |값 |설명 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|<--Azure 공용 DNS에서 이 항목을 만들어 앱 서비스가 privatelink를 가리키도록 합니다. 이 항목은 Microsoft에서 관리합니다.|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<--프라이빗 엔드포인트 IP 주소를 가리키도록 DNS 시스템 에서 이 항목을 관리합니다.|

이 DNS 구성 후에는 기본 이름인 mywebappname.azurewebsites.net을 사용하여 비공개로 웹 앱에 연결할 수 있습니다. 기본 인증서가 *.azurewebsites.net에 대해 발급되었으므로 이 이름을 사용해야 합니다.


사용자 지정 DNS 이름을 사용해야 하는 경우 웹앱에 사용자 지정 이름을 추가해야 합니다. 사용자 지정 이름은 모든 사용자 지정 이름과 마찬가지로 퍼블릭 DNS 확인을 사용하여 유효성을 검사해야 합니다. 자세한 내용은 [사용자 지정 DNS 유효성 검사][dnsvalidation]를 참조하세요.

Kudu 콘솔 또는 Kudu REST API(예: Azure DevOps 자체 호스팅 에이전트로 배포)의 경우, Azure DNS 프라이빗 영역 또는 사용자 지정 DNS 서버에서 두 개의 레코드를 만들어야 합니다. 

| 이름 | Type | 값 |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정][pricing]을 참조하세요.

## <a name="limitations"></a>제한 사항

탄력적 프리미엄 요금제에서 Azure 함수를 프라이빗 엔드포인트와 함께 사용하는 경우 Azure 웹 포털에서 함수를 실행하려면 직접 네트워크에 액세스할 수 있어야 합니다. 그렇지 않으면 HTTP 403 오류가 발생합니다. 즉, 브라우저가 Azure 웹 포털에서 함수를 실행하기 위해 프라이빗 엔드포인트에 연결할 수 있어야 합니다. 

최대 100개의 프라이빗 엔드포인트를 특정 웹 앱에 연결할 수 있습니다.

웹 앱에 대해 프라이빗 엔드포인트를 사용하는 경우 원격 디버깅 기능을 사용할 수 없습니다. 슬롯에 코드를 배포하고 원격으로 디버그하는 것이 좋습니다.

FTP 액세스는 인바운드 공용 IP 주소를 통해 제공됩니다. 프라이빗 엔드포인트는 웹 앱에 대한 FTP 액세스를 지원하지 않습니다.

Private Link 기능 및 프라이빗 엔드포인트는 정기적으로 개선되고 있습니다. 제한 사항에 대한 최신 정보는 [이 문서][pllimitations]를 확인하세요.

## <a name="next-steps"></a>다음 단계

- 포털을 통해 웹앱용 프라이빗 엔드포인트를 배포하려면 [포털로 웹앱에 비공개로 연결하는 방법][howtoguide1]을 참조하세요.
- Azure CLI를 사용하여 웹앱용 프라이빗 엔드포인트를 배포하려면 [Azure CLI로 웹앱에 비공개로 연결하는 방법][howtoguide2]을 참조하세요.
- PowerShell을 사용하여 웹앱용 프라이빗 엔드포인트를 배포하려면 [PowerShell로 웹앱에 비공개로 연결하는 방법][howtoguide3]을 참조하세요.
- Azure 템플릿을 사용하여 웹앱용 프라이빗 엔드포인트를 배포하려면 [Azure 템플릿으로 웹앱에 비공개로 연결하는 방법][howtoguide4]을 참조하세요.
- 엔드투엔드 예제, ARM 템플릿으로 VNet 삽입 및 프라이빗 엔드포인트를 사용하여 프런트 엔드 웹앱을 보안 백 엔드 웹앱에 연결하는 방법을 확인하려면 이 [빠른 시작][howtoguide5]을 확인하세요.
- 엔드투엔드 예제, Terraform으로 VNet 삽입 및 프라이빗 엔드포인트를 사용하여 프런트 엔드 웹앱을 보안 백 엔드 웹앱에 연결하는 방법을 확인하려면 이 [샘플][howtoguide6]을 확인하세요.


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../overview-vnet-integration.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.web/webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md
[TiP]: ../deploy-staging-slots.md#route-traffic