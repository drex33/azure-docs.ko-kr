---
title: v1에서 v2로 마이그레이션 - Azure Application Gateway
description: 이 문서에서는 v1에서 v2로 Azure Application Gateway와 Web Application Firewall을 마이그레이션하는 방법을 보여 줍니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 000daf7c60d0bc823aacdab85de42af3b6cbbf55
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419053"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Azure Application Gateway와 Web Application Firewall을 v1에서 v2로 마이그레이션

이제 [Azure Application Gateway 및 WAF(Web Application Firewall) v2](application-gateway-autoscaling-zone-redundant.md)를 사용할 수 있으며 자동 스케일링과 가용성 영역 중복성과 같은 추가 기능을 제공합니다. 그러나 기존 v1 게이트웨이는 v2로 자동 업그레이드되지 않습니다. v1에서 v2로 마이그레이션하려면 이 문서의 단계를 따릅니다.

마이그레이션에는 두 단계가 있습니다.

1. 구성 마이그레이션
2. 클라이언트 트래픽 마이그레이션

이 문서에서는 구성 마이그레이션에 대해 설명합니다. 클라이언트 트래픽 마이그레이션은 특정 환경에 따라 달라집니다. 그러나 일부 상위 수준의 일반 권장 사항이 [제공됩니다](#migrate-client-traffic).

## <a name="migration-overview"></a>마이그레이션 개요

다음을 수행하는 Azure PowerShell 스크립트를 사용할 수 있습니다.

* 지정하는 가상 네트워크 서브넷에 새 Standard_v2 또는 WAF_v2 게이트웨이를 만듭니다.
* v1 Standard 또는 WAF 게이트웨이와 연결된 구성을 새로 만든 Standard_V2 또는 WAF_V2 게이트웨이에 원활하게 복사합니다.

### <a name="caveatslimitations"></a>주의 사항/제한 사항

* 새 v2 게이트웨이에는 새 공용 및 개인 IP 주소가 있습니다. 기존 v1 게이트웨이와 연결된 IP 주소를 v2로 원활하게 이동할 수 없습니다. 그러나 새 v2 게이트웨이에 (할당되지 않은) 기존 공용 또는 개인 IP 주소를 할당할 수 있습니다.
* 가상 네트워크 내에서 v1 게이트웨이가 있는 다른 서브넷에 IP 주소 공간을 제공해야 합니다. 스크립트는 이미 v1 게이트웨이가 있는 기존 서브넷에서 v2 게이트웨이를 만들 수 없습니다. 그러나 기존 서브넷에 이미 v2 게이트웨이가 있는 경우에는 충분한 IP 주소 공간만 있다면 v2 게이트웨이를 만들 수 있습니다.
* v2 게이트웨이 서브넷에 연결된 네트워크 보안 그룹 또는 사용자 정의 경로가 있는 경우 성공적인 마이그레이션을 위해 [NSG 요구 사항](../application-gateway/configuration-infrastructure.md#network-security-groups)과 [UDR 요구 사항](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes)을 준수하는지 확인합니다.
* [가상 네트워크 서비스 엔드포인트 정책](../virtual-network/virtual-network-service-endpoint-policies-overview.md)은 현재 Application Gateway 서브넷에서 지원되지 않습니다.
* TLS/SSL 구성을 마이그레이션하려면 v1 게이트웨이에서 사용되는 모든 TLS/SSL 인증서를 지정해야 합니다.
* v1 게이트웨이에 대해 FIPS 모드를 사용한 경우 새 v2 게이트웨이로 마이그레이션되지 않습니다. v2에서는 FIPS 모드가 지원되지 않습니다.
* v2는 IPv6을 지원하지 않으므로 IPv6 사용 v1 게이트웨이는 마이그레이션되지 않습니다. 스크립트를 실행하면 완료되지 않을 수 있습니다.
* v1 게이트웨이에 개인 IP 주소만 있는 경우 스크립트는 새 v2 게이트웨이에 대한 공용 IP 주소와 개인 IP 주소를 만듭니다. v2 게이트웨이는 현재 개인 IP 주소만 지원하지 않습니다.
* 문자, 숫자, 하이픈, 밑줄 이외의 것을 포함하는 이름의 헤더는 애플리케이션에 전달되지 않습니다. 이는 헤더 값이 아닌 헤더 이름에만 적용됩니다. 이는 v1의 호환성이 손상되는 변경입니다.

## <a name="download-the-script"></a>스크립트 다운로드

[PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAppGWMigration)에서 마이그레이션 스크립트를 다운로드합니다.

## <a name="use-the-script"></a>스크립트 사용

로컬 PowerShell 환경 설정 및 기본 설정에 따라 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

* Azure Az 모듈이 설치되어 있지 않거나 Azure Az 모듈을 제거해도 상관없는 경우 `Install-Script` 옵션을 사용하여 스크립트를 실행하는 것이 가장 좋습니다.
* Azure Az 모듈을 유지해야 하는 경우에는 스크립트를 다운로드하여 직접 실행하는 것이 가장 좋습니다.

Azure Az 모듈이 설치되어 있는지 확인하려면 `Get-InstalledModule -Name az`를 실행합니다. 설치된 Az modules가 표시되지 않으면 `Install-Script` 메서드를 사용할 수 있습니다.

### <a name="install-using-the-install-script-method"></a>Install-Script 메서드를 사용한 설치

이 옵션을 사용하려면 컴퓨터에 Azure Az 모듈이 설치되어 있지 않아야 합니다. 설치되어 있는 경우 다음 명령이 오류를 표시합니다. Azure Az 모듈을 제거하거나 다른 옵션을 사용하여 스크립트를 수동으로 다운로드하여 실행할 수 있습니다.
  
다음 명령을 실행하여 스크립트를 실행하고 최신 버전을 다운로드합니다.

`Install-Script -Name AzureAppGWMigration -Force`

또한 이 명령은 필요한 Az 모듈을 설치합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 직접 사용한 설치

일부 Azure Az 모듈이 설치되어 있고 제거할 수 없는 경우(또는 제거하지 않으려는 경우) 스크립트 다운로드 링크의 **수동 다운로드** 탭을 사용하여 스크립트를 수동으로 다운로드할 수 있습니다. 이 스크립트는 원시 nupkg 파일로 다운로드됩니다. 이 nupkg 파일에서 스크립트를 설치하려면 [수동 패키지 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)를 참조하세요.

스크립트를 실행하려면

1. `Connect-AzAccount`를 사용하여 Azure에 연결합니다.

1. `Import-Module Az`를 사용하여 Az 모듈을 가져옵니다.

1. `Get-Help AzureAppGWMigration.ps1`을 실행하여 필요한 매개 변수를 검사합니다.

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -AppGwResourceGroupName <resource group name you want to use>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   스크립트 매개 변수:
   * **resourceId: [String]: 필수** - 기존 표준 v1 또는 WAF v1 게이트웨이에 대한 Azure 리소스 ID입니다. 이 문자열 값을 찾으려면 Azure Portal로 이동하여 Application Gateway 또는 WAF 리소스를 선택하고 게이트웨이에 대한 **속성** 링크를 클릭합니다. 리소스 ID는 해당 페이지에 있습니다.

     다음 Azure PowerShell 명령을 실행하여 리소스 ID를 얻을 수도 있습니다.

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: 필수** - 새 v2 게이트웨이를 포함하는 새 서브넷에 할당했거나 할당하려는 IP 주소 공간입니다. 이 IP 주소 공간은 CIDR 표기법으로 지정해야 합니다. 예를 들어 10.0.0.0/24입니다. 이 서브넷은 미리 만들 필요가 없지만 CIDR은 VNET 주소 공간의 일부 여야 합니다. 스크립트가 존재 하지 않는 경우 새로 만들고, 존재 하는 경우 기존 항목을 사용 합니다. 즉, 서브넷이 비어 있고, 해당 하는 경우 v2 Gateway만 포함 하 고, 사용 가능한 Ip가 충분 한지 확인 합니다.
   * **appgwName: [String]: 선택 사항**. 새 Standard_v2 또는 WAF_v2 게이트웨이의 이름으로 사용하도록 지정하는 문자열입니다. 이 매개 변수를 제공하지 않으면 접미사 *_v2* 가 추가된 기존 v1 게이트웨이의 이름이 사용됩니다.
   * **AppGwResourceGroupName: [String]: 선택 사항** 입니다. V2 Application Gateway 리소스를 만들 리소스 그룹의 이름 (기본값은 `<v1-app-gw-rgname>` )
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: 선택 사항**.  v1 게이트웨이에서 TLS/SSL 인증서를 나타내기 위해 만드는 PSApplicationGatewaySslCertificate 개체의 쉼표로 구분된 목록은 새 v2 게이트웨이에 업로드해야 합니다. 여기에 표시된 `New-AzApplicationGatewaySslCertificate` 명령을 통해 표준 v1 또는 WAF v1 게이트웨이에 대해 구성된 각 TLS/SSL 인증서에 새 PSApplicationGatewaySslCertificate 개체를 만들 수 있습니다. TLS/SSL 인증서 파일의 경로와 암호를 입력해야 합니다.

     이 매개 변수는 v1 게이트웨이 또는 WAF에 대해 구성된 HTTPS 수신기가 없는 경우에만 선택 사항입니다. 하나 이상의 HTTPS 수신기가 설정된 이 매개 변수를 지정해야 합니다.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     이전 예의 (쉼표로 구분된) `$mySslCert1, $mySslCert2`을 스크립트의 매개 변수에 대한 값으로 전달할 수 있습니다.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: 선택 사항**. v2 게이트웨이에서 백 엔드 인스턴스를 인증하기 위한 [신뢰할 수 있는 루트 인증서](ssl-overview.md)를 나타내기 위해 만드는 PSApplicationGatewayTrustedRootCertificate 개체의 쉼표로 구분된 목록입니다.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      PSApplicationGatewayTrustedRootCertificate 개체 목록을 만들려면 [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate)를 참조하세요.
   * **privateIpAddress: [String]: 선택 사항**. 새 v2 게이트웨이에 연결할 특정 개인 IP 주소입니다.  새 v2 게이트웨이에 할당하는 것과 동일한 VNet에서 가져와야 합니다. 이를 지정하지 않으면 스크립트에서 v2 게이트웨이에 개인 IP 주소를 할당합니다.
   * **publicIpResourceId: [String]: 선택 사항**. 새 v2 게이트웨이에 할당하려는 구독에 있는 기존 공용 IP 주소(표준 SKU) 리소스의 resourceId입니다. 이를 지정하지 않으면 스크립트는 동일한 리소스 그룹에 새 공용 IP를 할당합니다. 이름은 *-IP* 가 추가된 v2 게이트웨이의 이름입니다.
   * **validateMigration: [switch]: 선택 사항**. v2 게이트웨이 만들기와 구성 복사 후에 스크립트에서 몇 가지 기본적인 구성 비교 유효성 검사를 수행하도록 하려면 이 매개 변수를 사용합니다. 기본적으로 유효성 검사는 수행되지 않습니다.
   * **enableAutoScale: [switch]: 선택 사항**. 새 v2 게이트웨이를 만든 후 스크립트에서 자동 스케일링을 사용하려면 이 매개 변수를 사용합니다. 기본적으로 자동 스케일링은 사용되지 않습니다. 나중에 새로 만든 v2 게이트웨이에서 언제든지 자동 스케일링을 사용하도록 수동으로 설정할 수 있습니다.

1. 적절한 매개 변수를 사용하여 스크립트를 실행합니다. 완료하는 데 5~7분 정도 걸릴 수 있습니다.

    **예제**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -AppGwResourceGroupName "MyResourceGroup" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>클라이언트 트래픽 마이그레이션

먼저 스크립트가 v1 게이트웨이에서 마이그레이션된 정확한 구성을 사용하여 성공적으로 새 v2 게이트웨이를 만들었는지 다시 한번 확인합니다. Azure Portal에서 이를 확인할 수 있습니다.

또한 v2 게이트웨이를 통해 적은 양의 트래픽을 수동 테스트로 보냅니다.
  
현재 Application Gateway(표준)가 클라이언트 트래픽을 수신할 수 있는 몇 가지 시나리오와 각 시나리오에 대한 권장 사항은 다음과 같습니다.

* **표준 v1 또는 WAF v1 게이트웨이와 연결된 (A 레코드를 사용하는) 프런트 엔드 IP 주소를 가리키는 사용자 지정 DNS 영역(예: contoso.com)입니다**.

    Standard_v2 Application Gateway와 연결된 프런트 엔드 IP 또는 DNS 레이블을 가리키도록 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성된 TTL에 따라 모든 클라이언트 트래픽이 새 v2 게이트웨이로 마이그레이션하는 데 시간이 걸릴 수 있습니다.
* **v1 게이트웨이와 연결된 DNS 레이블(예: CNAME 레코드를 사용하는 *myappgw.eastus.cloudapp.azure.com*)을 가리키는 사용자 지정 DNS 영역(예: contoso.com)입니다**.

   두 가지 선택 사항이 있습니다.

  * Application Gateway에서 공용 IP 주소를 사용하는 경우 Traffic Manager 프로필을 사용하여 제어된 세분화된 마이그레이션을 수행하여 트래픽(가중 트래픽 라우팅 방법)을 새 v2 게이트웨이로 증분 라우팅할 수 있습니다.

    v1 및 v2 Application Gateway의 DNS 레이블을 [Traffic Manager 프로필](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)에 추가하고 사용자 지정 DNS 레코드(예: `www.contoso.com`)를 Traffic Manager 도메인(예: contoso.trafficmanager.net)에 CNAME을 지정하여 이 작업을 수행할 수 있습니다.
  * 또는 새 v2 Application Gateway의 DNS 레이블을 가리키도록 사용자 지정 도메인 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성된 TTL에 따라 모든 클라이언트 트래픽이 새 v2 게이트웨이로 마이그레이션하는 데 시간이 걸릴 수 있습니다.
* **클라이언트는 Application Gateway의 프런트 엔드 IP 주소에 연결합니다**.

   새로 만든 v2 Application Gateway와 연결된 IP 주소를 사용하도록 클라이언트를 업데이트합니다. IP 주소를 직접 사용하지 않는 것이 좋습니다. 사용자 지정 DNS 영역(예: contoso.com)에 CNAME 할 수 있는 Application Gateway와 연결된 DNS 이름 레이블(예: yourgateway.eastus.cloudapp.azure.com)을 사용하는 것이 좋습니다.

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>V1에서 V2로 구성을 마이그레이션하기 위한 Azure PowerShell 스크립트에 제한이 있나요?

예. [주의 사항/제한 사항](#caveatslimitations)을 참조하세요.

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>이 문서와 Azure PowerShell 스크립트는 Application Gateway WAF 제품에도 적용되나요? 

예.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell 스크립트도 v1 게이트웨이의 트래픽을 새로 만든 v2 게이트웨이로 전환하나요?

아니요. Azure PowerShell 스크립트는 구성만 마이그레이션합니다. 실제 트래픽 마이그레이션은 사용자의 책임이며 사용자가 컨트롤합니다.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 스크립트에서 만든 새 v2 게이트웨이의 크기가 현재 v1 게이트웨이의 모든 트래픽을 처리할 수 있도록 적절하게 조정되나요?

Azure PowerShell 스크립트는 기존 v1 게이트웨이의 트래픽을 처리할 수 있는 적절한 크기의 새 v2 게이트웨이를 만듭니다. 자동 스케일링은 기본적으로 사용하지 않지만 스크립트를 실행할 때 자동 스케일링을 사용할 수 있습니다.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Azure storage에 로그를 전송하도록 v1 gateway를 구성했습니다. 스크립트가 v2에 이 구성도 복제하나요?

아니요. 스크립트는 v2에 이 구성을 복제하지 않습니다. 로그 구성은 마이그레이션된 v2 게이트웨이에 별도로 추가해야 합니다.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>스크립트는 Azure KeyVault에 업로드된 인증서를 지원하나요?

아니요. 현재 스크립트는 KeyVault의 인증서를 지원하지 않습니다. 그러나 이는 이후 버전에서 고려 대상입니다.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>이 스크립트를 사용하다가 몇 가지 문제가 발생했습니다. 도움을 받으려면 어떻게 해야 하나요?
  
Azure 지원에 문의하려면 “구성 및 설정/v2 SKU로 마이그레이션” 항목을 참조하세요. [여기에서 Azure 지원](https://azure.microsoft.com/support/options/)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[Application Gateway v2에 대해 알아보기](application-gateway-autoscaling-zone-redundant.md)
