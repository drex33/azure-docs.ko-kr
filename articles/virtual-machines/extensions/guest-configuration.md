---
title: 게스트 구성 확장
description: 가상 머신 내에서 설정을 감사/구성하는 데 사용되는 확장에 대해 알아봅니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e795f01b198ee2daf16a2321a4a02b2c06dbac1a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767809"
---
# <a name="overview-of-the-guest-configuration-extension"></a>게스트 구성 확장 개요

게스트 구성 확장은 가상 머신 내에서 감사 및 구성 작업을 수행하는 Azure Policy 구성 요소입니다.
[Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) 및 [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc)에 대한 보안 기준 정의와 같은 정책은 확장이 설치된 다음에야 머신 내에서 설정을 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

머신이 게스트 구성 서비스에 인증하려면 머신에 [시스템이 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)가 있어야 합니다.
다음 속성이 설정된 경우 가상 머신의 ID 요구 사항이 충족됩니다.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>운영 체제

게스트 구성 확장 지원은 [엔드투엔드 솔루션에 대해 문서화](../../governance/policy/concepts/guest-configuration.md#supported-client-types)된 운영 체제 지원과 동일합니다.

### <a name="internet-connectivity"></a>인터넷 연결

게스트 구성 확장에서 설치한 에이전트는 게스트 구성 할당에 나열된 콘텐츠 패키지에 연결하고 게스트 구성 서비스에 상태를 보고할 수 있어야 합니다.
머신은 TCP 포트 443을 통해 아웃바운드 HTTPS를 사용하여 연결하거나, 연결이 개인 네트워킹을 통해 제공된 경우 연결할 수 있습니다.
개인 네트워킹에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure의 프라이빗 링크를 통해 통신하는 게스트 구성](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Azure Storage에 프라이빗 엔드포인트 사용](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>확장을 설치하는 방법

확장의 인스턴스 이름은 “AzurePolicyforWindows” 또는 “AzurePolicyforLinux”로 설정해야 하는데, 위에 언급된 정책에 이러한 특정 문자열이 필요하기 때문입니다.

기본적으로 모든 배포가 최신 버전으로 업데이트됩니다. _autoUpgradeMinorVersion_ 속성의 값은 달리 지정되지 않은 경우 “true”로 기본 설정됩니다. 새 버전의 확장을 릴리스할 때 코드 업데이트에 대해서는 신경 쓰지 않아도 됩니다.

### <a name="azure-policy"></a>Azure Policy

ID 요구 사항을 포함하여 최신 버전의 확장을 규모에 맞게 배포하려면 Azure Policy를 [할당](../../governance/policy/assign-policy-portal.md)합니다.

[필수 구성 요소를 배포하여 가상 머신에서 게스트 구성 정책을 사용하도록 설정합니다](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json).

### <a name="azure-cli"></a>Azure CLI

Linux용 확장을 배포하려면 다음을 수행합니다.


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Windows용 확장을 배포하려면 다음을 수행합니다.

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Linux용 확장을 배포하려면 다음을 수행합니다.

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Windows용 확장을 배포하려면 다음을 수행합니다.

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager 템플릿

Linux용 확장을 배포하려면 다음을 수행합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Windows용 확장을 배포하려면 다음을 수행합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Linux용 확장을 배포하려면 다음을 수행합니다.

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforLinux"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforLinux"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

Windows용 확장을 배포하려면 다음을 수행합니다.

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforWindows"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforWindows"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

## <a name="settings"></a>설정

확장에 설정 또는 보호된 설정 속성을 포함할 필요가 없습니다.
이러한 모든 정보는 [게스트 구성 할당](/rest/api/guestconfiguration/guestconfigurationassignments) 리소스의 에이전트에 의해 검색됩니다. 예를 들어 [ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode) 및 [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) 속성은 VM 확장이 아니라 구성에 따라 각각 관리됩니다.

## <a name="next-steps"></a>다음 단계

* Azure Policy 게스트 구성에 대한 자세한 내용은 [Azure Policy의 게스트 구성 이해](../../governance/policy/concepts/guest-configuration.md)를 참조하세요.
* Linux 에이전트 및 확장의 작동 방식에 대한 자세한 내용은 [Linux용 Azure VM 확장 및 기능](features-linux.md)을 참조하세요.
* Windows 게스트 에이전트 및 확장의 작동 방식에 대한 자세한 내용은 [Windows용 Azure VM 확장 및 기능](features-windows.md)을 참조하세요.  
* Windows 게스트 에이전트를 설치하려면 [Azure Windows Virtual Machine 에이전트 개요](agent-windows.md)를 참조하세요.  
* Linux 에이전트를 설치하려면 [Azure Linux Virtual Machine 에이전트 개요](agent-linux.md)를 참조하세요.  
