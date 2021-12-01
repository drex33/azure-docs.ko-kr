---
title: Windows용 Azure Key Vault VM 확장
description: 가상 머신 확장을 사용하여 가상 머신에 Key Vault 비밀의 자동 새로 고침을 수행하는 에이전트를 배포합니다.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eaeecc4d08d28939ba6930662f01ed65971c1c8
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133356655"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Windows용 Key Vault 가상 머신 확장

Key Vault VM 확장은 Azure Key Vault에 저장된 인증서의 자동 새로 고침을 제공합니다. 특히 확장은 키 자격 증명 모음에 저장된 인증서에 대해 관찰된 목록을 모니터링하고, 변경 내용이 검색되면 해당 인증서를 검색 및 설치합니다. 이 문서에서는 Windows용 Key Vault VM 확장에 지원되는 플랫폼, 구성 및 배포 옵션에 대해 자세히 설명합니다. 

### <a name="operating-system"></a>운영 체제

Key Vault VM 확장은 다음 Windows 버전을 지원합니다.

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

Key Vault VM 확장은 Windows Server 2019 core 설치를 사용하여 Azure에서 사용하기 위해 업로드되고 특수 이미지로 변환되는 사용자 지정 로컬 VM에서도 지원됩니다.

### <a name="supported-certificate-content-types"></a>지원되는 인증서 콘텐츠 형식

- PKCS #12
- PEM

## <a name="prerequisites"></a>필수 구성 요소

  - 인증서를 사용하는 Key Vault 인스턴스. [키 자격 증명 모음](../../key-vault/general/quick-create-portal.md) 참조
  - VM에서 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 할당해야 함
  - Key Vault 액세스 정책은 암호의 인증서 부분을 검색하기 위해 VM/VMSS 관리 ID에 대한 비밀 `get` 및 `list` 권한으로 설정해야 합니다. [Key Vault 인증](../../key-vault/general/authentication.md) 및 [Key Vault 액세스 정책 할당 방법](../../key-vault/general/assign-access-policy-cli.md)을 참조하세요.
  -  Virtual Machine Scale Sets에는 다음과 같은 ID 설정이 있어야 합니다.

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - AKV 확장에는 다음 설정이 있어야 합니다.

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Key Vault VM 확장에 대한 스키마를 보여 줍니다. 해당 확장에는 보호됨 설정이 필요 없습니다. 모든 설정이 공개 정보로 간주됩니다. 해당 확장에는 모니터링되는 인증서, 폴링 빈도, 대상 인증서 저장소 목록이 필요합니다. 특히 다음에 대한 내용을 설명합니다.  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> 관찰된 인증서 URL은 `https://myVaultName.vault.azure.net/secrets/myCertName` 형식이어야 합니다.
> 
> `/secrets` 경로가 프라이빗 키를 포함하여 전체 인증서를 반환하지만 `/certificates` 경로에서는 반환하지 않기 때문입니다. 인증서에 대한 자세한 내용은 여기에서 찾을 수 있습니다. [Key Vault 인증서](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> 'authenticationSettings' 속성은 **사용자 할당 ID** 가 있는 VM에만 **필요** 합니다.
> 이 속성은 Key Vault에 대한 인증에 사용할 ID를 지정합니다.

> [!IMPORTANT]
> 'msiClientId'를 지정하는 경우 'msiEndpoint' 속성이 **필요합니다.** 일반적으로 값은 로 설정해야 `http://169.254.169.254/metadata/identity/oauth2/token` 합니다.

### <a name="property-values"></a>속성 값

| Name | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | 문자열 |
| type | KeyVaultForWindows | 문자열 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 문자열 |
| certificateStoreName | MY | 문자열 |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine 또는 CurrentUser(대/소문자 구분) | 문자열 |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | 문자열 배열
| msiEndpoint | http://169.254.169.254/metadata/identity | 문자열 |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | 문자열 |


## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 배포 후에 인증서를 새로 고칠 필요가 있는 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다. 확장은 개별 VM 또는 가상 머신 확장 집합에 배포할 수 있습니다. 스키마와 구성은 두 템플릿 형식 모두에 공통적으로 적용됩니다. 

가상 머신 확장에 대한 JSON 구성은 템플릿의 가상 머신 리소스 조각, 특히 가상 머신 템플릿의 `"resources": []` 개체 및 `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` 개체의 가상 머신 확장 집합 내에 중첩되어야 합니다.

 > [!NOTE]
> VM 확장에는 Key Vault를 인증하기 위해 할당되는 시스템 또는 사용자 관리 ID가 필요합니다.  [Key Vault 인증 및 Key Vault 액세스 정책 할당 방법](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)을 참조하세요.
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>확장 종속성 순서 지정
Key Vault VM 확장은 구성된 경우 확장 순서 지정을 지원합니다. 기본적으로 확장은 폴링을 시작하자마자 성공적으로 시작되었음을 보고합니다. 그러나 성공적인 시작을 보고하기 전에 인증서의 전체 목록이 성공적으로 다운로드될 때까지 기다리도록 구성할 수 있습니다. 다른 확장이 시작 전에 전체 인증서 집합이 설치되었는지에 의존하는 경우 이 설정을 사용하도록 설정하면 해당 확장이 Key Vault 확장에 대한 종속성을 선언할 수 있습니다. 이렇게 하면 해당 확장이 의존하는 모든 인증서가 설치될 때까지 해당 확장을 시작할 수 없습니다. 확장은 초기 다운로드를 무기한 다시 시도하고 `Transitioning` 상태로 유지됩니다.

이를 설정하려면 다음을 설정합니다.
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```

> [!Note] 
> 이 기능의 사용은 시스템 할당 ID를 만들고 해당 ID를 사용하여 Key Vault 액세스 정책을 업데이트하는 ARM 템플릿과 호환되지 않습니다. 이렇게 하면 모든 확장이 시작될 때까지 자격 증명 모음 액세스 정책을 업데이트할 수 없으므로 교착 상태가 발생합니다. 대신 *단일 사용자 할당 MSI ID* 를 사용하고 배포하기 전에 해당 ID를 사용하여 자격 증명 모음을 사전 ACL해야 합니다.

## <a name="azure-powershell-deployment"></a>Azure PowerShell 배포

> [!WARNING]
> PowerShell 클라이언트는 settings.json에서 `\`를 `"`에 추가하는 경우가 많습니다, 이로 인해 akvvm_service가 오류 `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`와 함께 실패하게 됩니다. 추가 `\` 및 `"` 문자는 포털의 **설정** 아래의 **확장** 에서 볼 수 있습니다. 이를 방지하려면 `$settings`를 PowerShell `HashTable`로 초기화합니다.
> 
> ```powershell
> $settings = @{
>     "secretsManagementSettings" = @{ 
>         "pollingIntervalInS"       = "<pollingInterval>"; 
>         "certificateStoreName"     = "<certStoreName>"; 
>         "certificateStoreLocation" = "<certStoreLoc>"; 
>         "observedCertificates"     = @("<observedCert1>", "<observedCert2>") } }
> ```
>
  
Azure PowerShell은 기존 가상 머신 또는 가상 머신 확장 집합에 Key Vault VM 확장을 배포하는 데 사용할 수 있습니다. 

* VM에 확장을 배포하려면 다음과 같습니다.
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 가상 머신 확장 집합에 확장 배포:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI는 기존 가상 머신 또는 가상 머신 확장 집합에 Key Vault VM 확장을 배포하는 데 사용할 수 있습니다. 
 
* VM에 확장을 배포하려면 다음과 같습니다.
    
    ```azurecli
       # Start the deployment
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* 가상 머신 확장 집합에 확장 배포:

   ```azurecli
        # Start the deployment
        az vmss extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

다음 제한 사항/요구 사항에 주의하세요.
- Key Vault 제한 사항:
  - 배포 시점에 있어야 합니다. 
  - 관리 ID를 사용하여 VM/VMSS ID에 대한 Key Vault 액세스 정책을 설정해야 합니다. [Key Vault 인증](../../key-vault/general/authentication.md) 및 [Key Vault 액세스 정책 할당 방법](../../key-vault/general/assign-access-policy-cli.md)을 참조하세요.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="frequently-asked-questions"></a>질문과 대답

* 설정할 수 있는 observedCertificates 수에 제한이 있나요?
  아니요, Key Vault VM 확장은 observedCertificates 수에 제한이 없습니다.

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 또는 Azure Portal을 통해 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell을 사용하여 다음 명령을 실행합니다.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>로그 및 구성
Key Vault VM 확장 로그는 VM에 로컬로만 존재하며 문제 해결과 관련하여 가장 유용한 정보입니다.

|위치|Description|
|--|--|
| C:\WindowsAzure\Logs\WaAppAgent.log | 확장에 대한 업데이트가 발생한 시간을 표시합니다. |
| C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<most recent version\>\ | 인증서 다운로드 상태를 표시합니다. 다운로드 위치는 항상 Windows 컴퓨터의 MY 저장소(certlm.msc)입니다. |
| C:\Packages\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<most recent version\>\RuntimeSettings\ | Key Vault VM 확장 서비스 로그는 akvvm_service 서비스의 상태를 표시합니다. |
| C:\Packages\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<most recent version\>\Status\    | Key Vault VM 확장 서비스의 구성 및 이진 파일 |
|||  


### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
