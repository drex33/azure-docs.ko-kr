---
title: 가상 네트워크에서 Azure Application Gateway와 함께 API Management를 사용하는 방법
titleSuffix: Azure API Management
description: Application Gateway(웹 애플리케이션 방화벽)를 프런트 엔드로 사용하여 내부 가상 네트워크에서 Azure API Management 설정 및 구성
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.author: danlep
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell,contperf-fy21q4
ms.openlocfilehash: 793216ac8c411fbde6db5d044d345f1de98af17a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710347"
---
# <a name="integrate-api-management-in-an-internal-virtual-network-with-application-gateway"></a>내부 가상 네트워크의 API Management를 Application Gateway와 통합

[내부 모드의 가상 네트워크](api-management-using-with-internal-vnet.md)에서 API Management 서비스를 구성하여 가상 네트워크 내에서만 액세스하도록 할 수 있습니다. [Azure Application Gateway](../application-gateway/overview.md)는 계층 7 부하 분산 장치 역할을 하는 PaaS 서비스입니다. 역방향 프록시 서비스 역할을 하고 제품에 WAF(웹 애플리케이션 방화벽)를 제공합니다.

내부 가상 네트워크에 프로비저닝된 API Management를 Application Gateway 프런트 엔드에 결합하여 다음을 수행할 수 있습니다.

* 내부 소비자 및 외부 소비자의 소비에 대해 동일한 API Management 리소스를 사용합니다.
* 단일 API Management 리소스를 사용하며 외부 소비자가 사용할 수 있는 API Management에서 정의된 API의 하위 집합을 갖습니다.
* 퍼블릭 인터넷에서 API Management에 대한 액세스를 켜기 및 끄기로 전환하는 턴키 방법을 제공합니다.

> [!NOTE]
> 이 문서는 [Application Gateway WAF_V2 SKU](../application-gateway/application-gateway-autoscaling-zone-redundant.md)를 사용하도록 업데이트되었습니다.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에 설명한 단계를 따르려면 다음 항목이 있어야 합니다.

* 활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 인증서
     - API Management 서비스의 사용자 지정 호스트 이름에 대한 PFX 파일: 게이트웨이, 개발자 포털 및 관리 엔드포인트 
     - PFX 인증서의 루트 인증서에 대한 CER 파일. 
     
    자세한 내용은 [백 엔드에 대한 인증서](../application-gateway/certificates-for-backend-authentication.md)를 참조하세요. 테스트를 위해 필요에 따라 [자체 서명된 인증서](../application-gateway/self-signed-certificates.md)를 생성합니다.
* 최신 버전의 Azure PowerShell. 아직 설치하지 않은 경우 [Azure PowerShell을 설치](/powershell/azure/install-az-ps)합니다.

## <a name="scenario"></a>시나리오

이 문서에서는 내부 및 외부 소비자가 단일 API Management 서비스를 사용하여 온-프레미스 및 클라우드 API에서 단일 프런트 엔드 역할을 하도록 만드는 방법을 알아봅니다. Application Gateway에서 사용 가능한 라우팅 기능을 사용하여 외부 소비에 대해 API(예제에서 녹색으로 강조 표시됨)의 하위 집합만을 노출하는 방법도 알아봅니다.

첫 번째 설정 예제에서 모든 API는 가상 네트워크 내에서만 관리됩니다. 내부 소비자(주황색으로 강조 표시됨)는 모든 내부 및 외부 API에 액세스할 수 있습니다. 트래픽은 인터넷으로 나가지 않습니다. Express Route 회로를 통해 고성능 연결이 제공될 수 있습니다.

![url 경로](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

### <a name="what-is-required-to-integrate-api-management-and-application-gateway"></a>API Management 및 Application Gateway 통합을 위해 무엇이 필요한가요?

* **백 엔드 서버 풀:** API Management 서비스의 내부 가상 IP 주소입니다.
* **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀 내의 모든 서버에 적용됩니다.
* **프런트 엔드 포트:** Application Gateway에 열려 있는 공용 포트입니다. 이 포트에 도달한 트래픽은 백 엔드 서버 중의 하나로 리디렉션됩니다.
* **수신기**: 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 TLS/SSL 인증서 이름(TLS 오프로드를 구성하는 경우)이 있습니다.
* **규칙:** 규칙은 수신기를 백 엔드 서버 풀에 바인딩합니다.
* **사용자 정의 상태 프로브:** 기본적으로 Application Gateway는 IP 주소 기반 프로브를 사용하여 BackendAddressPool의 어떤 서버가 활성 상태인지 파악합니다. API Management 서비스는 올바른 호스트 헤더가 있는 요청에만 응답하므로 기본 프로브는 실패합니다. 서비스가 활성 상태이고 요청을 전달해야 한다는 것을 Application Gateway가 결정할 수 있도록 사용자 지정 상태 프로브를 정의합니다.
* **사용자 지정 도메인 인증서:** 인터넷에서 API Management에 액세스하려면 Application Gateway 프런트 엔드 DNS 이름에 대한 해당 호스트 이름의 CNAME을 매핑해야 합니다. 이렇게 하면 Application Gateway에 전송되고 API Management에 전달되는 호스트 이름 헤더 및 인증서를 API Management에서 유효한 것으로 인식할 수 있습니다. 이 예제에서는 API Management 서비스의 게이트웨이(백 엔드), 개발자 포털 및 관리 엔드포인트에 대해 세 가지 인증서를 사용합니다.  

## <a name="steps-required-to-integrate-api-management-and-application-gateway"></a>API Management 및 Application Gateway 통합에 필요한 단계

1. Resource Manager에 대한 리소스 그룹을 만듭니다.
1. 애플리케이션 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP를 만듭니다. API Management에 대한 다른 서브넷을 만듭니다.
1. 이전 단계에서 만든 가상 네트워크 서브넷 내에 API Management 서비스를 만듭니다. 내부 모드를 사용하는지 확인합니다.
1. API Management 서비스에서 사용자 지정 도메인 이름을 설정합니다.
1. 가상 네트워크에서 DNS 확인을 위한 프라이빗 DNS 영역 구성
1. Application Gateway 구성 개체를 만듭니다.
1. Application Gateway 리소스를 만듭니다.
1. CNAME을 Application Gateway 리소스의 공용 DNS 이름에서 API Management 프록시 호스트 이름으로 만듭니다.

### <a name="expose-the-developer-portal-and-management-endpoint-externally-through-application-gateway"></a>Application Gateway를 통해 외부에서 개발자 포털 및 관리 엔드포인트 노출

이 가이드에서는 Application Gateway를 통해 외부 대상에게 **개발자 포털** 및 **관리 엔드포인트** 도 노출합니다. 각 엔드포인트에 대한 수신기, 프로브, 설정 및 규칙을 만들려면 추가 단계가 필요합니다. 모든 세부 정보는 해당 단계에서 제공됩니다.

> [!WARNING]
> Azure AD 또는 타사 인증을 사용하는 경우 Application Gateway에서 [쿠키 기반 세션 선호도](../application-gateway/features.md#session-affinity) 기능을 사용하도록 설정하세요.

> [!WARNING]
> Application Gateway WAF가 개발자 포털에서 OpenAPI 사양 다운로드를 중단하지 않도록 하려면 방화벽 규칙 `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`을 사용하지 않도록 설정해야 합니다.
> 
> 포털의 기능을 손상시킬 수 있는 Application Gateway WAF 규칙은 다음과 같습니다.
> 
> - 관리 모드의 경우 `920300`, `920330`, `931130`, `942100`, `942110`, `942180`, `942200`, `942260`, `942340`, `942370`
> - 게시된 포털의 경우 `942200`, `942260`, `942370`, `942430`, `942440`

## <a name="create-a-resource-group-for-resource-manager"></a>Resource Manager에 대한 리소스 그룹 만들기

### <a name="step-1"></a>1단계

Azure에 로그인

```powershell
Connect-AzAccount
```

자격 증명을 사용하여 인증합니다.

### <a name="step-2"></a>2단계

원하는 구독을 선택합니다.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>3단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 애플리케이션 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>애플리케이션 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 Resource Manager를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다. 이 예제의 가상 네트워크는 Application Gateway 및 API Management에 대한 별도의 서브넷으로 구성됩니다.

### <a name="step-1"></a>1단계

Application Gateway 및 API Management 서브넷에 대한 네트워크 보안 그룹 및 NSG 규칙을 만듭니다.

```powershell
$appGwRule1 = New-AzNetworkSecurityRuleConfig -Name appgw-in -Description "AppGw inbound" `
    -Access Allow -Protocol * -Direction Inbound -Priority 100 -SourceAddressPrefix `
    GatewayManager -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 65200-65535
$appGwRule2 = New-AzNetworkSecurityRuleConfig -Name appgw-in-internet -Description "AppGw inbound Internet" `
    -Access Allow -Protocol "TCP" -Direction Inbound -Priority 110 -SourceAddressPrefix `
    Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 443
$appGwNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APPGW" -SecurityRules $appGwRule1, $appGwRule2

$apimRule1 = New-AzNetworkSecurityRuleConfig -Name apim-in -Description "APIM inbound" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix `
    ApiManagement -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 3443
$apimNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APIM" -SecurityRules $apimRule1
```

### <a name="step-2"></a>2단계

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 동안 Application Gateway에 사용할 서브넷 변수에 할당합니다.

```powershell
$appGatewaySubnet = New-AzVirtualNetworkSubnetConfig -Name "appGatewaySubnet" -NetworkSecurityGroup $appGwNsg -AddressPrefix "10.0.0.0/24"
```

### <a name="step-3"></a>3단계

가상 네트워크를 만드는 동안 주소 범위 10.0.1.0/24를 API Management에 사용할 서브넷 변수에 할당합니다.

```powershell
$apimSubnet = New-AzVirtualNetworkSubnetConfig -Name "apimSubnet" -NetworkSecurityGroup $apimNsg -AddressPrefix "10.0.1.0/24"
```

### <a name="step-4"></a>4단계

미국 서부 지역에 리소스 그룹 **apim-appGw-RG** 에서 **appgwvnet** 이라는 가상 네트워크를 만듭니다. 접두사 10.0.0.0/16과 서브넷 10.0.0.0/24 및 10.0.1.0/24를 사용합니다.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName `
  -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appGatewaySubnet,$apimSubnet
```

### <a name="step-5"></a>5단계

다음 단계에 대한 서브넷 변수 할당

```powershell
$appGatewaySubnetData = $vnet.Subnets[0]
$apimSubnetData = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-virtual-network-configured-in-internal-mode"></a>내부 모드로 구성된 가상 네트워크 내에서 API Management 서비스 만들기

다음 예제에서는 가상 네트워크에서 내부 액세스 전용으로 구성된 API Management 서비스를 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계

위에서 만든 `$apimSubnetData` 서브넷을 사용하여 API Management 가상 네트워크 개체를 만듭니다.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimSubnetData.Id
```

### <a name="step-2"></a>2단계

가상 네트워크 내부에 API Management 서비스를 만듭니다. 이 예제에서는 개발자 서비스 계층에 이 서비스를 만듭니다. API Management 서비스에 고유한 이름을 지정합니다.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name, must be globally unique
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

이 계층에서 API Management 서비스를 만들고 활성화하는 데 30~40분 정도 걸릴 수 있습니다. 이전 명령이 성공하면 [내부 가상 네트워크 API Management 서비스에 액세스하는 데 필요한 DNS 구성을](api-management-using-with-internal-vnet.md#dns-configuration) 참조하여 액세스를 확인합니다. 

## <a name="set-up-custom-domain-names-in-api-management"></a>API Management에서 사용자 지정 도메인 이름 설정

### <a name="step-1"></a>1단계

도메인에 대한 프라이빗 키가 있는 인증서 및 신뢰할 수 있는 루트 인증서의 세부 정보를 사용하여 다음 변수를 초기화합니다. 이 예제에서는 `api.contoso.net`, `portal.contoso.net` 및 `management.contoso.net`을 사용합니다.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$managementHostname = "management.contoso.net"               # API management endpoint host
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$managementCertPfxPath = "C:\Users\Contoso\management.pfx"   # full path to management.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate
$managementCertPfxPassword = "certificatePassword123"    # password for management.contoso.net pfx certificate
# Path to trusted root CER file used in Application Gateway HTTP settings
$trustedRootCertCerPath = "C:\Users\Contoso\trustedroot.cer" # full path to contoso.net trusted root .cer file

$certGatewayPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
$certManagementPwd = ConvertTo-SecureString -String $managementCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>2단계

API Management 엔드포인트에 대한 호스트 이름 구성 개체를 만들고 설정합니다.  

```powershell
$gatewayHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname `
  -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certGatewayPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname `
  -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd
$managementHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $managementHostname `
  -HostnameType Management -PfxPath $managementCertPfxPath -PfxPassword $certManagementPwd

$apimService.ProxyCustomHostnameConfiguration = $gatewayHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
$apimService.ManagementCustomHostnameConfiguration = $managementHostnameConfig

Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> 레거시 개발자 포털 연결을 구성하려면 `-HostnameType DeveloperPortal`을 `-HostnameType Portal`로 바꾸어야 합니다.

## <a name="configure-a-private-zone-for-dns-resolution-in-the-virtual-network"></a>가상 네트워크에서 DNS 확인을 위한 프라이빗 영역 구성

### <a name="step-1"></a>1단계

프라이빗 DNS 영역을 만들고 가상 네트워크를 연결합니다.

```powershell
$myZone = New-AzPrivateDnsZone -Name "contoso.net" -ResourceGroupName $resGroupName 
$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Name "mylink" `
  -VirtualNetworkId $vnet.id
```

### <a name="step-2"></a>2단계

사용자 지정 도메인 호스트 이름에 대한 A 레코드를 만들고 API Management 서비스의 개인 IP 주소에 매핑합니다.

```powershell
$apimIP = $apimService.PrivateIPAddresses[0]

New-AzPrivateDnsRecordSet -Name api -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name portal -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name management -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

리소스 그룹에 표준 공용 IP 리소스 **publicIP01** 을 만듭니다.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName `
  -name "publicIP01" -location $location -AllocationMethod Static -Sku Standard
```

서비스를 시작할 때 애플리케이션 게이트웨이에 IP 주소가 할당됩니다.

## <a name="create-application-gateway-configuration"></a>애플리케이션 게이트웨이 구성 만들기

애플리케이션 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 애플리케이션 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### <a name="step-1"></a>1단계

**gatewayIP01** 이라는 애플리케이션 게이트웨이 IP 구성을 만듭니다. Application Gateway는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appGatewaySubnetData
```

### <a name="step-2"></a>2단계

공용 IP 엔드포인트에 대한 프런트 엔드 IP 포트를 구성합니다. 이 포트는 최종 사용자가 연결하는 포트입니다.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>3단계

공용 IP 엔드포인트로 프런트 엔드 IP를 구성합니다.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4단계

Application Gateway의 인증서가 전달되는 트래픽을 암호화하고 해독하는 데 사용되도록 구성합니다.

> [!NOTE]
> Application Gateway는 사용자 지정 TLS 옵션 정의, 특정 TLS 프로토콜 버전을 사용하지 않도록 설정, 암호 그룹 및 기본 설정 순서 지정을 지원합니다. 구성 가능한 TLS 옵션에 대한 자세한 내용은 [TLS 정책 개요](../application-gateway/application-gateway-ssl-policy-overview.md)를 참조하세요.

```powershell
$certGateway = New-AzApplicationGatewaySslCertificate -Name "gatewaycert" `
  -CertificateFile $gatewayCertPfxPath -Password $certGatewayPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "portalcert" `
  -CertificateFile $portalCertPfxPath -Password $certPortalPwd
$certManagement = New-AzApplicationGatewaySslCertificate -Name "managementcert" `
  -CertificateFile $managementCertPfxPath -Password $certManagementPwd
```

### <a name="step-5"></a>5단계

Application Gateway에 대한 HTTP 수신기를 만듭니다. 프런트 엔드 IP 구성, 포트 및 TLS/SSL 인증서를 할당합니다.

```powershell
$gatewayListener = New-AzApplicationGatewayHttpListener -Name "gatewaylistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certGateway -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "portallistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
$managementListener = New-AzApplicationGatewayHttpListener -Name "managementlistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certManagement -HostName $managementHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>6단계

API Management 서비스 `ContosoApi` 게이트웨이 도메인 엔드포인트에 사용자 지정 프로브를 만듭니다. 경로 `/status-0123456789abcdef`는 모든 API Management 서비스에서 호스트되는 기본 상태 엔드포인트입니다. `api.contoso.net`을 사용자 지정 프로브 호스트 이름으로 지정하여 TLS/SSL 인증서로 보호합니다.

> [!NOTE]
> 호스트 이름 `contosoapi.azure-api.net`은 서비스 `contosoapi`가 공용 Azure에서 생성될 때 구성된 기본 프록시 호스트 이름입니다.
>

```powershell
$apimGatewayProbe = New-AzApplicationGatewayProbeConfig -Name "apimgatewayprobe" `
  -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" `
  -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" `
  -Protocol "Https" -HostName $portalHostname -Path "/signin" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
$apimManagementProbe = New-AzApplicationGatewayProbeConfig -Name "apimmanagementprobe" `
  -Protocol "Https" -HostName $managementHostname -Path "/ServiceStatus" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7단계

HTTP 설정에서 구성할 신뢰할 수 있는 루트 인증서를 업로드합니다.

```powershell
$trustedRootCert = New-AzApplicationGatewayTrustedRootCertificate -Name "whitelistcert1" -CertificateFile $trustedRootCertCerPath
```

### <a name="step-8"></a>8단계

백 엔드 요청이 취소되기까지의 제한 시간을 포함하여 Application Gateway에 대한 HTTP 백 엔드 설정을 구성합니다. 이 값은 프로브 제한 시간과 다릅니다.

```powershell
$apimPoolGatewaySetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolGatewaySetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimGatewayProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolManagementSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolManagementSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimManagementProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
```

### <a name="step-9"></a>9단계

각 API Management 엔드포인트에 대해 해당 도메인 이름을 사용하여 백 엔드 IP 주소 풀을 구성합니다.

```powershell
$apimGatewayBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "gatewaybackend" `
  -BackendFqdns $gatewayHostname
$apimPortalBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "portalbackend" `
  -BackendFqdns $portalHostname
$apimManagementBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "managementbackend" `
  -BackendFqdns $managementHostname
```

### <a name="step-10"></a>10단계

기본 라우팅을 사용하도록 Application Gateway에 대한 규칙을 만듭니다.

```powershell
$gatewayRule = New-AzApplicationGatewayRequestRoutingRule -Name "gatewayrule" `
  -RuleType Basic -HttpListener $gatewayListener -BackendAddressPool $apimGatewayBackendPool `
  -BackendHttpSettings $apimPoolGatewaySetting
$portalRule = New-AzApplicationGatewayRequestRoutingRule -Name "portalrule" `
  -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimPortalBackendPool `
  -BackendHttpSettings $apimPoolPortalSetting
$managementRule = New-AzApplicationGatewayRequestRoutingRule -Name "managementrule" `
  -RuleType Basic -HttpListener $managementListener -BackendAddressPool $apimManagementBackendPool `
  -BackendHttpSettings $apimPoolManagementSetting
```

> [!TIP]
> `-RuleType` 및 라우팅을 변경하여 개발자 포털의 특정 페이지에 대한 액세스를 제한합니다.

### <a name="step-11"></a>11단계

애플리케이션 게이트웨이에 대한 크기 및 인스턴스 수를 구성합니다. 이 예제에서는 [WAF_v2 SKU](../web-application-firewall/ag/ag-overview.md)를 사용하여 API Management 리소스의 보안을 강화합니다.

프로덕션 워크로드에는 최소 두 개의 _인스턴스(용량)를_ 사용하는 것이 좋습니다. 그러나 비프로덕션 시나리오 또는 일반 실험에는 인스턴스를 하나만 사용할 수 있습니다. 자세한 내용은 [Azure Application Gateway 가격 책정을 참조하세요.](../application-gateway/understanding-pricing.md#instance-count)

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_v2" -Tier "WAF_v2" -Capacity 2
```

### <a name="step-12"></a>12단계

WAF를 "방지" 모드로 구성합니다.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-13"></a>13단계

TLS 1.0은 현재 기본값이므로 최신 [TLS 1.2 정책을](../application-gateway/application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)사용하도록 애플리케이션 게이트웨이를 설정하는 것이 좋습니다.

```powershell
$policy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
```

## <a name="create-application-gateway"></a>Application Gateway 만들기

이전 단계의 모든 구성 개체를 사용하여 Application Gateway를 만듭니다.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location `
  -BackendAddressPools $apimGatewayBackendPool,$apimPortalBackendPool,$apimManagementBackendPool `
  -BackendHttpSettingsCollection $apimPoolGatewaySetting, $apimPoolPortalSetting, $apimPoolManagementSetting `
  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 `
  -HttpListeners $gatewayListener,$portalListener,$managementListener `
  -RequestRoutingRules $gatewayRule,$portalRule,$managementRule `
  -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $certGateway,$certPortal,$certManagement `
  -TrustedRootCertificate $trustedRootCert -Probes $apimGatewayProbe,$apimPortalProbe,$apimManagementProbe `
  -SslPolicy $policy
```

Application Gateway 배포가 완료된 후 포털에서 API Management 백 엔드의 상태를 확인하거나 다음 명령을 실행하여 확인합니다.

```powershell
Get-AzApplicationGatewayBackendHealth -Name $appgwName -ResourceGroupName $resGroupName
```

각 백 엔드 풀의 상태가 정상인지 확인합니다. 비정상 백 엔드 또는 상태를 알 수 없는 백 엔드 문제를 해결해야 하는 경우 [Application Gateway의 백 엔드 상태 문제 해결](../application-gateway/application-gateway-backend-health-troubleshooting.md)을 참조하세요.

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>API Management 프록시 호스트 이름을 Application Gateway 리소스의 공용 DNS 이름으로 CNAME합니다.

게이트웨이가 생성되면 통신을 위해 프런트 엔드를 구성합니다. 공용 IP 주소를 사용할 때 Application Gateway는 쉽게 사용할 수 없는 동적으로 할당된 DNS 이름이 필요합니다.

Application Gateway의 DNS 이름을 사용하여 API Management 게이트웨이 호스트 이름(앞의 예제에서 `api.contoso.net`)으로 이 DNS 이름을 가리키는 CNAME 레코드를 만듭니다. 프런트 엔드 IP CNAME 레코드를 구성하려면 `PublicIPAddress` 요소를 사용하여 Application Gateway 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 게이트웨이를 다시 시작할 때 VIP가 변경될 수 있으므로 A 레코드를 사용하지 않는 것이 좋습니다.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

테스트를 위해 Application Gateway의 공용 IP 주소를 구성된 각 API Management 엔드포인트 호스트 이름(`api.contoso.net`, `portal.contoso.net`, `management.contoso.net`)에 매핑하는 항목으로 로컬 머신의 hosts 파일을 업데이트할 수 있습니다.

## <a name="summary"></a>요약

가상 네트워크에서 구성된 Azure API Management는 온-프레미스 또는 클라우드에서 호스트되었는지 여부에 상관 없이 구성된 모든 API에 대한 단일 게이트웨이 인터페이스를 제공합니다. Application Gateway와 API Management의 통합을 통해 특정 API를 인터넷에 액세스할 수 있도록 선택적으로 유연성을 향상시키고 API Management 인스턴스에 대한 프런트 엔드로 WAF를 제공합니다.

## <a name="next-steps"></a>다음 단계

* Azure Application Gateway에 대한 자세한 정보
  * [Application Gateway 개요](../application-gateway/overview.md)
  * [Application Gateway 웹 애플리케이션 방화벽](../web-application-firewall/ag/ag-overview.md)
  * [경로 기반 라우팅을 사용하는 Application Gateway](../application-gateway/tutorial-url-route-powershell.md)
* API Management 및 가상 네트워크에 대한 자세한 정보
  * [내부 가상 네트워크에서 API Management 사용](api-management-using-with-internal-vnet.md)
  * [가상 네트워크에서 API Management를 사용하는 방법](api-management-using-with-vnet.md)
