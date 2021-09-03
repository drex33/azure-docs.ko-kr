---
title: Azure Resource Manager 테스트 도구 키트에 대한 createUiDefinition.json 테스트 사례
description: Azure Resource Manager 템플릿 테스트 도구 키트에서 실행되는 createUiDefinition.json 테스트에 대해 설명합니다.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 573f7db7c1f6fd3d45531e8be15db3d8affc8eba
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392037"
---
# <a name="test-cases-for-createuidefinitionjson"></a>createUiDefinition.json에 대한 테스트 사례

이 문서에서는 [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md) 파일에 대해 [템플릿 테스트 도구 키트](test-toolkit.md)로 실행되는 테스트에 대해 설명합니다. 이 예에는 테스트 이름과 테스트를 **통과** 하거나 **실패** 하는 코드 샘플이 포함됩니다.

이 도구 키트에는 ARM 템플릿(Azure Resource Manager 템플릿)에 대한 [테스트 사례](template-test-cases.md)와 _azuredeploy.json_ 또는 _maintemplate.json_ 이라는 기본 템플릿 파일이 포함되어 있습니다. 디렉터리에 _createUiDefinition.json_ 파일이 포함된 경우 UI 컨트롤에 대해 특정 테스트가 실행됩니다. 테스트를 실행하는 방법 또는 특정 테스트를 실행하는 방법에 대한 자세한 내용은 [테스트 매개 변수](test-toolkit.md#test-parameters)를 참조하세요.

_createUiDefinition.json_ 파일은 [요소](../managed-applications/create-uidefinition-elements.md) 및 [함수](../managed-applications/create-uidefinition-functions.md)를 사용하여 사용자 지정 UI(사용자 인터페이스) 컨트롤을 만듭니다.

## <a name="verify-template-parameter-allows-values"></a>템플릿 매개 변수에서 값을 허용하는지 확인

테스트 이름: **허용되는 값은 실제로 허용되어야 함**

이 테스트는 _createUiDefinition.json_ 의 각 컨트롤 값이 기본 템플릿의 매개 변수에서 허용되는지 확인합니다. 매개 변수는 기본 템플릿과 _createUiDefinition.json_ 파일 사이의 이름으로 매핑됩니다.

기본 템플릿의 매개 변수는 컨트롤의 `allowedValues` 값을 수락해야 합니다. 테스트는 또한 _createUiDefinition.json_ `outputs` 섹션에서 컨트롤이 참조되는지 확인합니다.

이 테스트는 기본 템플릿과 _createUiDefinition.json_ 파일을 확인합니다. _createUiDefinition.json_ 파일의 예는 기본 템플릿 예 다음에 표시됩니다.

다음 예는 기본 템플릿의 매개 변수 이름 `combo`가 컨트롤의 매개 변수 이름 `comboBox`와 일치하지 않기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "combo": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

다음 예는 기본 템플릿의 매개 변수 유형 `int`가 컨트롤의 `string` 값을 허용하지 않기 때문에 **실패** 합니다. 그리고 기본 템플릿의 매개 변수가 `defaultValue`를 정의하는 경우 컨트롤의 `allowedValues`에서 유효한 `value`여야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

다음 예는 기본 템플릿의 매개 변수 이름이 컨트롤의 매개 변수 이름과 일치하기 때문에 **통과** 합니다. 그리고 템플릿의 매개 변수 유형은 컨트롤의 `allowedValues`에 지정된 `defaultValue`가 있는 `string`입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    }
  }
}
```

이 예의 _createUiDefinition.json_ 파일:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "demoComboBox",
        "label": "demoComboBoxLabel",
        "elements": [
          {
            "name": "comboBox",
            "type": "Microsoft.Common.DropDown",
            "label": "Example drop down",
            "defaultValue": "Value two",
            "toolTip": "This is a tool tip",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Value one",
                  "description": "The value to select for option 1.",
                  "value": "one"
                },
                {
                  "label": "Value two",
                  "description": "The value to select for option 2.",
                  "value": "two"
                }
              ],
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "comboBox": "[steps('demoComboBox').comboBox]"
    }
  }
}
```

## <a name="output-controls-must-exist"></a>출력 컨트롤이 있어야 합니다.

테스트 이름: **출력의 컨트롤이 있어야 함**

`outputs` 섹션에서 사용되는 컨트롤은 _createUiDefinition.json_ 의 다른 위치에 있는 요소에 있어야 합니다. `outputs`에서 참조된 이름은 `basics[]` 또는 `steps[]`에 사용된 이름과 일치해야 합니다.

다음 예는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "nameDoesNotMatchOutput",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="properties-must-include-values"></a>속성에는 값이 포함되어야 합니다.

테스트 이름: **CreateUIDefinition에 공백이 없어야 함**

속성에는 값이 포함되어야 합니다. 필수 속성은 유효한 값을 사용해야 합니다. 비어 있는 선택적 속성은 제거해야 합니다. 테스트는 공백 `"basics": []`, `"steps": []` 또는 `defaultValue`를 허용합니다.

다음 예는 `label`, `placeholder` 및 `toolTip`이 비어 있기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "",
        "placeholder": "",
        "defaultValue": "",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

다음 예는 `label` 및 `toolTip`에 값이 있고 `placeholder`가 제거되었기 때문에 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "defaultValue": "",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="use-valid-schema-and-version"></a>유효한 스키마 및 버전 사용

테스트 이름: **CreateUIDefinition에 스키마가 있어야 함**

_createUiDefinition.json_ 파일은 `$schema` 속성을 포함하고 유효한 `$schema` 및 `version`을 사용해야 합니다. `$schema` 및 `version`의 버전 번호는 일치해야 합니다.

다음 예는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.9.9-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.9.9-preview"
}
```

다음 예는 최신 `$schema` 및 `version`을 사용하기 때문에 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-credential-confirmation"></a>자격 증명을 숨기지 마세요.

테스트 이름: **자격 증명 확인을 숨겨서는 안 됨**

이 테스트는 [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 또는 [Microsoft.Compute.CredentialsCombo](../managed-applications/microsoft-compute-credentialscombo.md)에 대한 자격 증명을 확인합니다. 확인이 표시되도록 `hideConfirmation` 속성을 `false`로 설정해야 합니다.

다음 예는 `hideConfirmation`이 `true`이기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": true
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

다음 예는 `hideConfirmation`이 `false`이므로 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": false
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

## <a name="use-correct-handler"></a>올바른 처리기 사용

테스트 이름: **처리기가 정확해야 함**

_createUiDefinition.json_ 파일의 `Microsoft.Azure.CreateUIDef` 또는 `Microsoft.Compute.MultiVm`을 사용합니다.

다음 예는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.",
  "version": "0.1.2-preview"
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-existing-resources"></a>기존 리소스를 숨기지 마세요.

테스트 이름: **HideExisting을 올바르게 처리해야 함**

`hideExisting`이 `false`로 설정되거나 생략된 경우 `outputs`에는 `resourceGroup` 및 `newOrExisting`이 포함되어야 합니다. `hideExisting`의 기본값은 `false`입니다.

`hideExisting`을 포함하는 컨트롤 유형의 예로는 [Microsoft.Storage.StorageAccountSelector](../managed-applications/microsoft-storage-storageaccountselector.md), [Microsoft.Network.PublicIpAddressCombo](../managed-applications/microsoft-network-publicipaddresscombo.md) 또는 [Microsoft.Network.VirtualNetworkCombo](../managed-applications/microsoft-network-virtualnetworkcombo.md)가 있습니다.

다음 예는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": false
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "resourceGroup": "[basics('storage').resourceGroup]",
      "newOrExisting": "[basics('storage').newOrExisting]"
    }
  }
}
```

## <a name="use-location-in-outputs"></a>출력에서 위치 사용

테스트 이름: **위치는 출력에 있어야 함**

`outputs` 섹션에는 [location](../managed-applications/create-ui-definition-referencing-functions.md#location) 함수를 사용하는 위치가 포함되어야 합니다.

다음 예는 `outputs`에 위치가 포함되어 있지 않기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="include-control-outputs-in-template-parameters"></a>템플릿 매개 변수에 제어 출력 포함

테스트 이름: **출력은 템플릿 매개 변수에 있어야 함**

테스트는 _createUiDefinition.json_ 에 `outputs` 섹션이 포함되어 있는지 확인합니다. 테스트는 또한 해당 `outputs`이 기본 템플릿의 `parameters` 섹션에 정의되어 있는지도 확인합니다. 매개 변수는 _createUiDefinition.json_ 과 기본 템플릿 사이의 이름으로 매핑되므로 이름이 일치해야 합니다.

이 테스트는 기본 템플릿과 _createUiDefinition.json_ 파일을 확인합니다. _createUiDefinition.json_ 파일의 예는 기본 템플릿 예 다음에 표시됩니다.

다음 예는 기본 템플릿에 _createUiDefinition.json_ 파일의 `outputs` 섹션에 있는 `comboBox` 매개 변수가 포함되어 있지 않기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

다음 예에서는 기본 템플릿에 `comboBox` 매개 변수가 포함되어 있으므로 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

이 예의 _createUiDefinition.json_ 파일:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="parameters-without-default-must-exist-in-outputs"></a>기본값이 없는 매개 변수는 출력에 있어야 합니다.

테스트 이름: **CreateUIDefinition에 기본값이 없는 매개 변수가 있어야 함**

기본값이 없는 기본 템플릿의 매개 변수는 _createUiDefinition.json_ 파일의 `outputs` 섹션에 있어야 합니다.

이 테스트는 기본 템플릿과 _createUiDefinition.json_ 파일을 확인합니다. _azuredeploy.json_ 파일의 예는 컨트롤의 예 다음에 표시됩니다.

다음 예는 _createUiDefinition.json_ 파일의 `outputs`에 기본 템플릿의 매개 변수 `comboBox`가 포함되어 있지 않기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

다음 예는 _createUiDefinition.json_ 이 `outputs`에 `comboBox`를 포함하기 때문에 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

이 예의 _azuredeploy.json_ 파일입니다. `comboBox` 매개 변수에는 기본값이 없습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

## <a name="use-secure-parameter-with-password-box"></a>암호 상자와 함께 보안 매개 변수 사용

테스트 이름: **암호 매개 변수에 암호 텍스트 상자를 사용해야 함**

이 테스트는 [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 요소가 기본 템플릿의 `parameters` 및 _createUiDefinition.json_ `outputs`에 정의되어 있는지 확인합니다. 암호 상자에 대한 기본 템플릿의 매개 변수 유형은 `secureString` 또는 `secureObject`여야 합니다.

이 테스트는 기본 템플릿과 _createUiDefinition.json_ 파일을 확인합니다. _createUiDefinition.json_ 파일의 예는 기본 템플릿 예 다음에 표시됩니다.

다음 예는 기본 템플릿의 `passwordBox` 매개 변수가 `string`이기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

다음 예는 기본 템플릿의 `passwordBox` 매개 변수가 `secureString`이기 때문에 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "secureString"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

이 예의 _createUiDefinition.json_ 파일:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="password-box-requires-minimum-length"></a>암호 상자에는 최소 길이가 필요합니다.

테스트 이름: **PasswordBox에는 최소 길이가 있어야 함**

이 테스트는 [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 요소가 12자 이상을 요구하는 `regex`와 함께 `constraints`를 사용하는지 확인합니다.

다음 예는 `constraints`가 없기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

다음 예는 `regex`에 12자 이상이 필요하기 때문에 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password",
        "constraints": {
          "required": true,
          "regex": "^[a-zA-Z0-9]{12,}$",
          "validationMessage": "Password must be at least 12 characters long, contain only numbers and letters"
        }
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="text-box-must-use-validation"></a>텍스트 상자는 유효성 검사를 사용해야 합니다.

테스트 이름: **텍스트 상자가 잘 구성됨**

텍스트 상자에서 유효성 검사를 사용하여 `regex` 및 `message`가 포함된 `constraints`를 확인합니다.

다음 예는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="tooltip-must-exist-with-a-value"></a>toolTip은 값과 함께 있어야 합니다.

테스트 이름: **도구 설명이 있어야 함**

이 테스트는 `toolTip` 속성이 존재하고 값을 포함하는지 확인합니다.

다음 예는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="dont-set-a-default-user-name"></a>기본 사용자 이름을 설정하지 마세요.

테스트 이름: **사용자 이름에 기본값이 없어야 함**

테스트는 [Microsoft.Compute.UserNameTextBox](../managed-applications/microsoft-compute-usernametextbox.md)에 대해 `defaultValue`가 설정되어 있는지 확인합니다.

다음 예는 `defaultValue`가 제공되었기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "defaultValue": "admin",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

## <a name="use-message-with-validations"></a>유효성 검사와 함께 메시지 사용

테스트 이름: **검증에 메시지가 있어야 함**

이 테스트는 _createUiDefinition.json_ 의 모든 `validations`에 `message`가 포함되어 있는지 확인합니다.

다음 예는 `regex` 유효성 검사에 `message`가 없기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$"
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

다음 예는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="virtual-machine-sizes-must-match"></a>가상 머신 크기는 일치해야 합니다.

테스트 이름: **VM 크기는 템플릿과 일치해야 함**

이 테스트는 [Microsoft.Compute.SizeSelector](../managed-applications/microsoft-compute-sizeselector.md)가 _createUiDefinition.json_ `outputs` 및 기본 템플릿의 `parameters` 섹션에 있는지 확인합니다. `defaultValue`를 지정하는 기본 템플릿 매개 변수는 컨트롤의 `allowedSizes`에 있는 값과 일치해야 합니다.

이 테스트는 기본 템플릿과 _createUiDefinition.json_ 파일을 확인합니다. _createUiDefinition.json_ 파일의 예는 기본 템플릿 예 다음에 표시됩니다.

다음 예는 기본 템플릿의 `defaultValue`가 `allowedSizes`의 값과 일치하지 않기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D9"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

다음 예는 기본 템플릿의 `defaultValue`가 `allowedSizes`의 값과 일치하기 때문에 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D3"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

이 예의 _createUiDefinition.json_ 파일:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "vmSize",
        "type": "Microsoft.Compute.SizeSelector",
        "label": "VM Size",
        "toolTip": "Select a virtual machine size",
        "recommendedSizes": [
          "Standard_D1"
        ],
        "constraints": {
          "allowedSizes": [
            "Standard_D1",
            "Standard_D2",
            "Standard_D3"
          ]
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "vmSize": "[basics('vmSize')]"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

- Azure Portal 사용자 인터페이스를 만들려면 [Azure 관리 애플리케이션의 만들기 환경을 위한 CreateUiDefinition.json](../managed-applications/create-uidefinition-overview.md)을 참조하세요.
- UI 정의 샌드박스 만들기를 사용하려면 [Azure Managed Applications에 대한 포털 인터페이스 테스트](../managed-applications/test-createuidefinition.md)를 참조하세요.
- UI 컨트롤에 대한 자세한 내용은 [CreateUiDefinition 요소](../managed-applications/create-uidefinition-elements.md) 및 [CreateUiDefinition 함수](../managed-applications/create-uidefinition-functions.md)를 참조하세요.
- ARM 템플릿 테스트에 대한 자세한 내용은 [ARM 템플릿에 대한 테스트 사례](template-test-cases.md)를 참조하세요.
