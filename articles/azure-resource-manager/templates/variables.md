---
title: 템플릿의 변수
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 변수를 정의하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: 0a9b49c36f25295ab06e724773e1494c10eb1f08
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893117"
---
# <a name="variables-in-arm-templates"></a>ARM 템플릿의 변수

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에서 변수를 정의하고 사용하는 방법을 설명합니다. 변수를 사용하여 템플릿을 단순화합니다. 템플릿 전체에서 복잡한 식을 반복하는 대신 복잡한 식을 포함하는 변수를 정의합니다. 그런 다음, 템플릿 전체에서 필요에 따라 해당 변수를 사용합니다.

Resource Manager는 배포 작업을 시작하기 전에 변수를 확인합니다. 템플릿에서 변수를 사용하는 모든 경우에는 Resource Manager는 변수를 확인된 값으로 바꿉니다.

## <a name="define-variable"></a>변수 정의

변수를 정의할 때 변수에 대한 [데이터 형식](data-types.md)을 지정하지 않습니다. 대신 값 또는 템플릿 식을 제공합니다. 변수 형식은 확인된 값에서 유추됩니다. 다음 예제에서는 변수를 문자열로 설정합니다.

```json
"variables": {
  "stringVar": "example value"
},
```

변수를 생성하려면 매개 변수 또는 다른 변수의 값을 사용할 수 있습니다.

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

[템플릿 함수](template-functions.md)를 사용하여 변수 값을 생성할 수 있습니다.

다음 예제에서는 스토리지 계정 이름에 대한 문자열 값을 만듭니다. 여러 템플릿 함수를 사용하여 매개 변수 값을 가져오고 고유 문자열에 연결합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

[reference](template-functions-resource.md#reference) 함수 또는 [list](template-functions-resource.md#list) 함수를 변수 선언에 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가져오며 변수가 확인되면 배포 전에 실행할 수 없습니다.

## <a name="use-variable"></a>변수 사용

다음 예제에서는 리소스 속성에 대한 변수를 사용하는 방법을 설명합니다.

변수의 값을 참조하려면 [variables](template-functions-deployment.md#variables) 함수를 사용합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>예제 템플릿

다음 템플릿은 리소스를 배포하지 않습니다. 다양한 형식의 변수를 선언하는 몇 가지 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>구성 변수

환경을 구성하기 위한 관련된 값을 포함하는 변수를 정의할 수 있습니다. 값이 있는 개체로 변수를 정의합니다. 다음 예제에서는 **test** 와 **prod** 의 두 환경에 대한 값을 보유하는 개체를 보여 줍니다. 배포하는 동안 해당 값 중 하나를 전달합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>다음 단계

* 변수에 사용할 수 있는 속성에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
* 변수 만들기에 대한 권장 사항은 [모범 사례 - 변수](./best-practices.md#variables)를 참조하세요.
* 네트워크 보안 그룹에 보안 규칙을 할당하는 예제 템플릿은 [네트워크 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 및 [매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)을 참조하세요.