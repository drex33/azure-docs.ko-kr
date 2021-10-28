---
title: Azure Cloud Service(추가 지원) 배포 - PowerShell
description: PowerShell을 사용하여 Azure Cloud Service(추가 지원)를 배포합니다.
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: e157fcb3dd3c25d2724d9f0a190596549913edb3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229878"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Azure PowerShell을 사용하여 Cloud Service(추가 지원) 배포

이 문서에서는 `Az.CloudService` PowerShell 모듈을 사용하여 Azure에서 여러 역할(WebRole 및 WorkerRole)이 있는 Cloud Services(추가 지원)를 배포하는 방법에 대해 설명합니다.

## <a name="pre-requisites"></a>필수 구성 요소

1. Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다. 
2. Az.CloudService PowerShell 모듈을 설치합니다.

    ```azurepowershell-interactive
    Install-Module -Name Az.CloudService 
    ```

3. 새 리소스 그룹 만들기 기존 리소스 그룹을 사용하는 경우 이 단계는 선택 사항입니다.   

    ```azurepowershell-interactive
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

4. Cloud Service 패키지(.cspkg) 및 서비스 구성(.cscfg) 파일을 저장하는 데 사용할 스토리지 계정 및 컨테이너를 만듭니다. 고유한 이름을 스토리지 계정 이름에 사용해야 합니다. 기존 스토리지 계정을 사용하는 경우 이 단계는 선택 사항입니다.

    ```azurepowershell-interactive
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```
    
## <a name="deploy-a-cloud-services-extended-support"></a>Cloud Services(추가 지원) 배포

다음 PowerShell cmdlet을 사용하여 Cloud Services(추가 지원)를 배포합니다.

1. [**Storage 계정을 사용하여 빠른 클라우드 서비스 만들기**](#quick-create-cloud-service-using-a-storage-account)

    - 이 매개 변수 집합은 입력 형태로 .cscfg, .cspkg 및 .csdef 파일을 스토리지 계정과 함께 입력합니다. 
    - 클라우드 서비스 역할 프로필, 네트워크 프로필 및 OS 프로필은 최소한의 사용자 입력으로 cmdlet을 통해 만듭니다. 
    - 인증서 입력의 경우 keyvault 이름을 지정해야 합니다. keyvault의 인증서 지문은 .cscfg 파일에 지정된 지문에 대해 유효성이 검사됩니다.
    
 2. [**SAS URI를 사용하여 빠른 클라우드 서비스 만들기**](#quick-create-cloud-service-using-a-sas-uri)
 
    - 이 매개 변수 집합은 .csdef 및 .cscfg 파일의 로컬 경로와 함께 .cspkg의 SAS URI를 입력합니다. 스토리지 계정 입력이 필요하지 않습니다. 
    - 클라우드 서비스 역할 프로필, 네트워크 프로필 및 OS 프로필은 최소한의 사용자 입력으로 cmdlet을 통해 만듭니다. 
    - 인증서 입력의 경우 keyvault 이름을 지정해야 합니다. keyvault의 인증서 지문은 .cscfg 파일에 지정된 지문에 대해 유효성이 검사됩니다.
    
3. [**역할, OS, 네트워크 및 확장 프로필과 SAS URI를 사용하여 클라우드 서비스 만들기**](#create-cloud-service-using-profile-objects--sas-uris)

    - 이 매개 변수 집합은 .cscfg 및 .cspkg 파일의 SAS URI를 입력합니다.
    - 역할, 네트워크, OS 및 확장 프로필은 사용자가 지정해야 하며 .cscfg 및 .csdef의 값과 일치해야 합니다. 

### <a name="quick-create-cloud-service-using-a-storage-account"></a>Storage 계정을 사용하여 빠른 클라우드 서비스 만들기

.cscfg, .csdef 및 .cspkg 파일을 사용하여 클라우드 서비스 배포를 만듭니다.

```azurepowershell-interactive
$cspkgFilePath = "<Path to cspkg file>"
$cscfgFilePath = "<Path to cscfg file>"
$csdefFilePath = "<Path to csdef file>"
      
# Create Cloud Service       
New-AzCloudService
-Name "ContosoCS" `
-ResourceGroupName "ContosOrg" `
-Location "EastUS" `
-ConfigurationFile $cscfgFilePath `
-DefinitionFile $csdefFilePath `
-PackageFile $cspkgFilePath `
-StorageAccount $storageAccount `
[-KeyVaultName <string>]
```

### <a name="quick-create-cloud-service-using-a-sas-uri"></a>SAS URI를 사용하여 빠른 클라우드 서비스 만들기

1. Cloud Service 패키지(cspkg)를 스토리지 계정에 업로드합니다.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    $cscfgFilePath = "<Path to cscfg file>"
    $csdefFilePath = "<Path to csdef file>"
    ```

 2. .cscfg, .csdef 및 .cspkg SAS URI를 사용하여 클라우드 서비스 배포를 만듭니다.

    ```azurepowershell-interactive
    New-AzCloudService
        -Name "ContosoCS" `
        -ResourceGroupName "ContosOrg" `
        -Location "EastUS" `
        -ConfigurationFile $cspkgFilePath `
        -DefinitionFile $csdefFilePath `
        -PackageURL $cspkgUrl `
        [-KeyVaultName <string>]
    ```
    
### <a name="create-cloud-service-using-profile-objects--sas-uris"></a>프로필 개체 및 SAS URI를 사용하여 클라우드 서비스를 만듭니다.

1.  클라우드 서비스 구성(cscfg)을 스토리지 계정에 업로드합니다. 

    ```azurepowershell-interactive
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```
2. Cloud Service 패키지(cspkg)를 스토리지 계정에 업로드합니다.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
    
3. 가상 네트워크와 서브넷을 만듭니다. 기존 네트워크 및 서브넷을 사용하는 경우 이 단계는 선택 사항입니다. 다음 예제에서는 단일 가상 네트워크 및 서브넷을 두 클라우드 서비스 역할(WebRole 및 WorkerRole)에 사용합니다. 

    ```azurepowershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
4. 공용 IP 주소를 만들고 공용 IP 주소의 DNS 레이블 속성을 설정합니다. Cloud Services(확장 지원)는 [기본](../virtual-network/ip-services/public-ip-addresses.md#basic) SKU 공용 IP 주소만 지원합니다. 표준 SKU 공용 IP는 Cloud Services에서 작동하지 않습니다.
예약된 IP를 사용하는 경우 서비스 구성(.cscfg) 파일에서 예약된 IP로 참조해야 합니다.

    ```azurepowershell-interactive
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

5. 네트워크 프로필 개체를 만들고, 공용 IP 주소를 부하 분산 장치의 프런트 엔드에 연결합니다. Azure 플랫폼은 클라우드 서비스 리소스와 동일한 구독에서 '클래식' SKU 부하 분산 장치 리소스를 자동으로 만듭니다. 부하 분산 장치 리소스는 Azure Resource Manager의 읽기 전용 리소스입니다. 리소스에 대한 모든 업데이트는 클라우드 서비스 배포 파일(.cscfg & .csdef)을 통해서만 지원됩니다.

    ```azurepowershell-interactive
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
6. Key Vault를 만듭니다. 이 Key Vault는 클라우드 서비스(추가 지원) 역할에 연결된 인증서를 저장하는 데 사용됩니다. Key Vault는 클라우드 서비스와 동일한 지역 및 구독에 있어야 하며 고유한 이름을 사용해야 합니다. 자세한 내용은 [Azure Cloud Services(추가 지원)에서 인증서 사용](certificates-and-key-vault.md)을 참조하세요.

    ```azurepowershell-interactive
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

7. Key Vault 액세스 정책을 업데이트하고, 인증서 권한을 사용자 계정에 부여합니다. 

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    또는 ObjectId(`Get-AzADUser`를 실행하여 가져올 수 있음)를 통해 액세스 정책을 설정합니다. 
    
    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

8. 이 예제에서는 Key Vault에 자체 서명된 인증서를 추가합니다. 클라우드 서비스 역할에 배포하려면 인증서 지문을 Cloud Service 구성(.cscfg) 파일에 추가해야 합니다. 

    ```azurepowershell-interactive
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
9. OS 프로필 메모리 내 개체를 만듭니다. OS 프로필은 클라우드 서비스 역할에 연결된 인증서를 지정합니다. 이는 이전 단계에서 만든 것과 동일한 인증서입니다. 

    ```azurepowershell-interactive
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

10. 역할 프로필 메모리 내 개체를 만듭니다. 역할 프로필은 이름, 용량, 계층 등의 역할 SKU 특정 속성을 정의합니다. 이 예제에서는 frontendRole 및 backendRole의 두 가지 역할을 정의했습니다. 역할 프로필 정보는 구성(.cscfg) 파일 및 서비스 정의(.csdef) 파일에 정의된 역할 구성과 일치해야 합니다. 

    ```azurepowershell-interactive
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

11. (선택 사항) 클라우드 서비스에 추가하려는 확장 프로필 메모리 내 개체를 만듭니다. 다음 예제에서는 RDP 확장을 추가합니다. 

    ```azurepowershell-interactive
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    
    ConfigFile에는 PublicConfig 태그만 있어야 하며, 다음과 같은 네임스페이스를 포함해야 합니다.
   
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
    
12. (선택 사항) 태그를 클라우드 서비스에 추가하려는 PowerShell 해시 테이블로 정의합니다. 

    ```azurepowershell-interactive
    $tag=@{"Owner" = "Contoso"} 
    ```

13. 프로필 개체 및 SAS URL을 사용하여 Cloud Service 배포를 만듭니다.

    ```azurepowershell-interactive
    $cloudService = New-AzCloudService ` 
        -Name “ContosoCS” ` 
        -ResourceGroupName “ContosOrg” ` 
        -Location “East US” ` 
        -PackageUrl $cspkgUrl ` 
        -ConfigurationUrl $cscfgUrl ` 
        -UpgradeMode 'Auto' ` 
        -RoleProfile $roleProfile ` 
        -NetworkProfile $networkProfile  ` 
        -ExtensionProfile $extensionProfile ` 
        -OSProfile $osProfile `
        -Tag $tag 
    ```

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support)를 방문합니다.