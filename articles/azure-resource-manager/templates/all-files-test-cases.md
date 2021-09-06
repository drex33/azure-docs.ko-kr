---
title: Azure Resource Manager 테스트 도구 키트에 대한 모든 파일 테스트 사례
description: Azure Resource Manager 템플릿 테스트 도구 키트에서 모든 파일에 대해 실행되는 테스트를 설명합니다.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 6af9a7c149aacbd50537086ae70a8d845604d2ef
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393577"
---
# <a name="test-cases-for-all-files"></a>모든 파일에 대한 테스트 사례

이 문서에서는 모든 JSON(JavaScript Object Notation) 파일에 대해 [템플릿 테스트 도구 키트](test-toolkit.md)로 실행되는 테스트에 대해 설명합니다. 이 예제에는 테스트 이름과 테스트를 **통과** 하거나 **실패** 하는 코드 샘플이 포함됩니다. 테스트를 실행하는 방법 또는 특정 테스트를 실행하는 방법에 대한 자세한 내용은 [테스트 매개 변수](test-toolkit.md#test-parameters)를 참조하세요.

## <a name="use-valid-json-syntax"></a>유효한 JSON 구문 사용

테스트 이름: JSONFiles가 유효해야 합니다.

이 테스트는 모든 JSON 파일에 유효한 구문이 포함되어 있는지 확인합니다. 예를 들어 _azuredeploy.json_, _azuredeploy.parameters.json_ 또는 _createUiDefinition.json_ 파일이 있습니다. 테스트가 **실패** 하면 다른 테스트에 대한 오류 또는 경고가 표시되거나 JSON 구문 분석이 표시됩니다.

### <a name="template-file-example"></a>템플릿 파일 예제

다음 예제는 `parameters`, `comboBox` 및 `location`에서 _azuredeploy.json_ 선행 중괄호(`{`)가 없기 때문에 실패합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters":
    "comboBox":
      "type": "string"
    },
    "location":
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

다음 예제는 **통과** 합니다.

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

### <a name="parameter-file-example"></a>매개 변수 파일 예제

다음 예제는 _azuredeploy.parameters.json_ 이 `value` 없이 매개 변수를 사용하므로 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value":
    }
  }
}
```

다음 예제는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value": "westus"
    }
  }
}
```

### <a name="createuidefintion-example"></a>CreateUiDefintion 예제

_createUiDefinition.json_ 의 `outputs` 섹션에 선행 중괄호(`{`)가 없기 때문에 **실패** 합니다.

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
    "outputs":
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

다음 예제는 **통과** 합니다.

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

## <a name="next-steps"></a>다음 단계

- 테스트 도구 키트에 대한 자세한 내용은 [ARM 템플릿 테스트 도구 키트 사용](test-toolkit.md)을 참조하세요.
- ARM 템플릿 테스트는 [ARM 템플릿에 대한 테스트 사례](template-test-cases.md)를 참조하세요.
- 매개 변수 파일을 테스트하려면 [매개 변수 파일에 대한 테스트 사례](parameters.md)를 참조하세요.
- createUiDefinition 테스트는 [createUiDefinition.json에 대한 테스트 사례](createUiDefinition-test-cases.md)를 참조하세요.
