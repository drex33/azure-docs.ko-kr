---
title: 템플릿을 사용하여 게스트 구성 할당을 만드는 방법
description: Azure Resource Manager에서 직접 머신에 구성을 배포하는 방법을 알아봅니다.
ms.date: 08/09/2021
ms.topic: how-to
ms.openlocfilehash: aa7938a9421a9e7680af34af475585c4dc1c818a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773100"
---
# <a name="how-to-create-a-guest-configuration-assignment-using-templates"></a>템플릿을 사용하여 게스트 구성 할당을 만드는 방법

[게스트 구성 패키지](../concepts/guest-configuration-assignments.md)를 여러 머신에 할당하는 가장 좋은 방법은 [Azure Policy](./guest-configuration-create-definition.md)를 사용하는 것입니다. 단일 머신에 게스트 구성 패키지를 할당할 수도 있습니다.

## <a name="built-in-and-custom-configurations"></a>기본 제공 구성 및 사용자 지정 구성

단일 머신에 게스트 구성 패키지를 할당하려면 다음 예제를 수정합니다. 다음과 같은 두 가지 시나리오가 있습니다.

- [게시한](./guest-configuration-create-publish.md) 패키지에 대한 링크를 사용하여 머신에 사용자 지정 구성을 적용합니다.
- 머신에 Azure 기준선과 같은 [ 기본 제공](../samples/built-in-packages.md) 구성을 적용합니다.

## <a name="extending-other-resource-types-such-as-arc-enabled-servers"></a>Arc 지원 서버와 같은 기타 리소스 종류 확장

다음 각 섹션의 예제에는 이름이 `Microsoft.Compute/virtualMachines`로 시작하는 **type** 속성이 포함되어 있습니다. 게스트 구성 리소스 공급자 `Microsoft.GuestConfiguration`은 부모 유형을 참조해야 하는 [확장 리소스](../../../azure-resource-manager/management/extension-resource-types.md)입니다.

[Arc 지원 서버](../../../azure-arc/servers/overview.md)와 같은 다른 리소스 종류에 대한 예제를 수정하려면 부모 유형을 리소스 공급자의 이름으로 변경합니다.
Arc 지원 서버에서 리소스 공급자는 `Microsoft.HybridCompute/machines`입니다.

다음 “<>” 필드를 사용자 환경에 맞는 값으로 바꿉니다.

- **<vm_name>** : 구성이 적용될 머신 리소스의 이름
- **<configuration_name>** : 적용할 구성의 이름
- **<vm_location>** : 게스트 구성 할당이 만들어질 Azure 지역
- **<Url_to_Package.zip>** : 사용자 지정 콘텐츠 패키지의 경우 .zip 파일에 대한 HTTPS 링크
- **<SHA256_hash_of_package.zip>** : 사용자 지정 콘텐츠 패키지의 경우 .zip 파일의 SHA256 해시

## <a name="assign-a-configuration-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용한 구성 할당

게스트 구성 할당 리소스를 포함하는 [Azure Resource Manager 템플릿](../../../azure-resource-manager/templates/deployment-tutorial-local-template.md?tabs=azure-powershell)을 배포할 수 있습니다.

다음 예제에서는 고객 지정 구성을 할당합니다.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "<configuration_name>",
                    "contentUri": "<Url_to_Package.zip>",
                    "contentHash": "<SHA256_hash_of_package.zip>",
                    "assignmentType": "ApplyAndMonitor"
                }
            }
        }
```

다음 예제에서는 `AzureWindowBaseline` 기본 제공 구성을 할당합니다.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "AzureWindowsBaseline",
                    "version": "1.*",
                    "assignmentType": "ApplyAndMonitor",
                    "configurationParameter": [
                    {
                        "name": "Minimum Password Length;ExpectedValue",
                        "value": "16"
                    },
                    {
                        "name": "Minimum Password Length;RemediateValue",
                        "value": "16"
                    },
                    {
                        "name": "Maximum Password Age;ExpectedValue",
                        "value": "75"
                    },
                    {
                        "name": "Maximum Password Age;RemediateValue",
                        "value": "75"
                    }
                ]
                }
            }
        }
```

## <a name="assign-a-configuration-using-bicep"></a>Bicep을 사용한 구성 할당

[Azure Bicep](../../../azure-resource-manager/bicep/overview.md)을 사용하여 게스트 구성 할당을 배포할 수 있습니다.

다음 예제에서는 고객 지정 구성을 할당합니다.

```Bicep
resource myVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource myConfiguration 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: '<configuration_name>'
  scope: myVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: '<configuration_name>'
      contentUri: '<Url_to_Package.zip>'
      contentHash: '<SHA256_hash_of_package.zip>'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
    }
  }
}
```

다음 예제에서는 `AzureWindowBaseline` 기본 제공 구성을 할당합니다.

```Bicep
resource myWindowsVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource AzureWindowsBaseline 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: 'AzureWindowsBaseline'
  scope: myWindowsVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: 'AzureWindowsBaseline'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
      configurationParameter: [
        {
          name: 'Minimum Password Length;ExpectedValue'
          value: '16'
        }
        {
          name: 'Minimum Password Length;RemediateValue'
          value: '16'
        }
        {
          name: 'Maximum Password Age;ExpectedValue'
          value: '75'
        }
        {
          name: 'Maximum Password Age;RemediateValue'
          value: '75'
        }
      ]
    }
  }
}
```

## <a name="assign-a-configuration-using-terraform"></a>Terraform을 사용한 구성 할당

[Terraform](https://www.terraform.io/)을 사용하여 게스트 구성 할당을 [배포](/azure/developer/terraform/get-started-windows-powershell)할 수 있습니다.

> [!IMPORTANT]
> Terraform 공급자 [azurerm_policy_virtual_machine_configuration_assignment](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine_configuration_policy_assignment)가 `assignmentType` 속성을 지원하도록 업데이트되지 않아 감사하는 구성만 지원됩니다.

다음 예제에서는 고객 지정 구성을 할당합니다.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "<configuration_name>" {
  name               = "<configuration_name>"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name            = "<configuration_name>"
    contentUri      =  '<Url_to_Package.zip>'
    contentHash     =  '<SHA256_hash_of_package.zip>'
    version         = "1.*"
    assignmentType  = "ApplyAndMonitor
  }
}
```

다음 예제에서는 `AzureWindowBaseline` 기본 제공 구성을 할당합니다.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "AzureWindowsBaseline" {
  name               = "AzureWindowsBaseline"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name    = "AzureWindowsBaseline"
    version = "1.*"
    parameter {
      name  = "Minimum Password Length;ExpectedValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Length;RemediateValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Age;ExpectedValue"
      value = "75"
    }
    parameter {
      name  = "Minimum Password Age;RemediateValue"
      value = "75"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

- [게스트 구성 개요](../concepts/guest-configuration.md)를 읽어봅니다.
- 사용자 지정 게스트 구성 패키지 [개발 환경](../how-to/guest-configuration-create-setup.md)을 설치합니다.
- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](../how-to/guest-configuration-create.md).
- 사용자 개발 환경에서 [패키지 아티팩트 테스트](../how-to/guest-configuration-create-test.md)합니다.
- 머신에서 액세스할 수 있도록 [패키지 아티팩트를 게시](./guest-configuration-create-publish.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](../how-to/guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
