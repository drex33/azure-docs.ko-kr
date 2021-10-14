---
title: 백 엔드 v1에 연결
description: App Service Environment에서 백 엔드 리소스에 안전하게 연결하는 방법에 대해 알아봅니다. 이 문서는 레거시 v1 ASE를 사용하는 고객에게만 제공됩니다.
author: madsd
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: eb022d72dbbf433c8a91f092cb2337fc7e3a6e55
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004676"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>App Service Environment에서 백 엔드 리소스에 안전하게 연결
App Service 환경은 **항상** Azure Resource Manager 가상 네트워크 **또는** 클래식 배포 모델 [가상 네트워크][virtualnetwork]의 서브넷에 만들어지므로 App Service 환경에서 다른 백 엔드 리소스로의 아웃바운드 연결은 가상 네트워크를 통해서만 이동할 수 있습니다. 2016년 6월 기준으로, 퍼블릭 주소 범위 또는 RFC1918 주소 공간(프라이빗 주소) 중 하나를 사용하는 가상 네트워크에 ASE를 배포할 수도 있습니다.  

예를 들어 잠긴 포트 1433을 통해 가상 머신의 클러스터에서 실행되는 SQL Server가 있을 수 있습니다.  엔드포인트는 동일한 가상 네트워크에 있는 다른 리소스의 액세스만 허용하도록 ACL에 포함될 수 있습니다.  

또 다른 예로, 중요한 엔드포인트는 온-프레미스에서 실행되고 [사이트 간][SiteToSite] 또는 [Azure ExpressRoute][ExpressRoute] 연결을 통해 Azure에 연결될 수 있습니다.  따라서 사이트 간 또는 ExpressRoute 터널에 연결된 가상 네트워크의 리소스만 온-프레미스 엔드포인트에 액세스할 수 있습니다.

모든 시나리오에서, App Service Environment에서 실행되는 앱은 다양한 서버 및 리소스에 안전하게 연결할 수 있습니다. App Service Environment에 실행되는 앱에서 동일한 가상 네트워크에 있거나 동일한 가상 네트워크에 연결된 프라이빗 엔드포인트로의 아웃바운드 트래픽은 가상 네트워크를 통해서만 이동합니다.  프라이빗 엔드포인트로의 아웃바운드 트래픽은 퍼블릭 인터넷을 통해 이동하지 않습니다.

한 가지 이슈는 App Service Environment에서 가상 네트워크 내 엔드포인트로의 아웃바운드 트래픽에 적용됩니다. App Service Environment는 App Service Environment와 **동일한** 서브넷에 있는 가상 머신의 엔드포인트에 연결할 수 없습니다. App Service Environment에만 사용하도록 예약된 서브넷에 App Service Environment를 배포하는 경우 이 제한은 일반적으로 문제가 되지 않습니다.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>아웃 바운드 연결 및 DNS 요구 사항
App Service Environment가 제대로 작동하려면 다양한 엔드포인트에 대한 아웃바운드 액세스가 필요합니다. ASE에서 사용하는 외부 엔드포인트의 전체 목록은 [ExpressRoute에 대한 네트워크 구성](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) 문서의 "필수 네트워크 연결" 섹션에 있습니다.

App Service Environment에는 가상 네트워크에 대해 구성된 유효한 DNS 인프라가 필요합니다.  App Service Environment를 만든 후에 DNS 구성이 변경되면 개발자는 App Service Environment에서 새 DNS 구성을 선택하도록 강제 적용할 수 있습니다. 포털의 App Service Environment 관리 블레이드 맨 위에서 **다시 시작** 아이콘을 선택하여 롤링 환경 다시 부팅을 트리거하면 환경에서 새 DNS 구성을 선택합니다.

또한 App Service Environment를 만들기 전에 VNET의 모든 사용자 지정 DNS 서버를 미리 설정하는 것이 좋습니다.  App Service Environment를 만드는 동안 가상 네트워크의 DNS 구성이 변경되면 App Service Environment 생성 프로세스가 실패합니다. VPN 게이트웨이의 반대쪽 끝에서 연결하거나 사용할 수 없는 사용자 지정 DNS 서버가 있는 경우에도 App Service Environment 생성 프로세스가 실패합니다.

## <a name="connecting-to-a-sql-server"></a>SQL Server 가상 컴퓨터에 연결
일반적인 SQL Server 구성에는 포트 1433에서 수신 대기하는 엔드포인트가 있습니다.

![SQL Server 엔드포인트][SqlServerEndpoint]

이 엔드포인트로 트래픽을 제한하는 두 가지 방법이 있습니다.

* [네트워크 Access Control 목록][NetworkAccessControlLists](네트워크 ACL)
* [네트워크 보안 그룹][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>네트워크 ACL을 사용하여 액세스 제한
네트워크 액세스 제어 목록을 사용하여 포트 1433의 보안을 유지할 수 있습니다.  아래 예제에서는 가상 네트워크 내에서 시작되는 클라이언트 주소를 할당 권한에 추가하고 다른 모든 클라이언트에 대한 액세스를 차단합니다.

![네트워크 Access Control 목록 예][NetworkAccessControlListExample]

SQL Server와 동일한 가상 네트워크의 App Service Environment에서 실행되는 애플리케이션은 SQL Server 인스턴스에 연결할 수 있습니다. SQL Server 가상 머신의 **VNet 내부** IP 주소를 사용합니다.  

아래 예제 연결 문자열은 해당 개인 IP 주소를 사용하여 SQL Server를 참조합니다.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

가상 머신에 퍼블릭 엔드포인트도 있지만 공용 IP 주소를 사용한 연결 시도는 네트워크 ACL 때문에 거부됩니다. 

## <a name="restricting-access-with-a-network-security-group"></a>네트워크 보안 그룹을 사용하여 액세스 제한
액세스를 보호하는 또 다른 방법은 네트워크 보안 그룹을 사용하는 것입니다.  개별 가상 머신 또는 가상 머신이 포함된 서브넷에 네트워크 보안 그룹을 적용할 수 있습니다.

먼저 네트워크 보안 그룹을 만들어야 합니다.

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

네트워크 보안 그룹을 사용하면 간단하게 액세스를 VNet 내부 트래픽만으로 제한할 수 있습니다.  네트워크 보안 그룹의 기본 규칙은 동일한 가상 네트워크에 있는 다른 네트워크 클라이언트의 액세스만 허용합니다.

따라서 SQL Server에 대한 액세스 잠금이 간단합니다. SQL Server를 실행하는 가상 머신이나 가상 머신이 포함된 서브넷에 네트워크 보안 그룹 및 해당 기본 규칙을 적용하면 됩니다.

아래 샘플에서는 서브넷에 네트워크 보안 그룹을 적용합니다.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

최종 결과로 VNet 내부 액세스는 허용하면서 외부 액세스를 차단하는 보안 규칙 집합이 생성됩니다.

![기본 네트워크 보안 규칙][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>시작
App Service 환경을 시작하려면 [App Service 환경 소개][IntroToAppServiceEnvironment]

App Service 환경으로의 인바운드 트래픽을 제어하는 방법에 대한 자세한 내용은 [App Service 환경으로의 인바운드 트래픽 제어][ControlInboundASE]를 참조하세요.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png