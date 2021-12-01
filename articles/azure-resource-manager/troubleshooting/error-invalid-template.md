---
title: 잘못된 템플릿 오류
description: Bicep 파일 또는 ARM 템플릿(Azure Resource Manager 템플릿)을 배포할 때 잘못된 템플릿 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.openlocfilehash: fe1cec024cee191575529afc8d9095bad741d8a6
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133364606"
---
# <a name="resolve-errors-for-invalid-template"></a>잘못된 템플릿 오류 해결

이 문서에서는 Bicep 파일 및 ARM 템플릿(Azure Resource Manager 템플릿)에 대한 잘못된 템플릿 오류를 해결하는 방법을 설명합니다.

## <a name="symptom"></a>증상

템플릿이 배포되면 다음을 나타내는 오류가 표시됩니다.

```Output
Code=InvalidTemplate
Message=<varies>
```

오류 메시지는 오류 유형에 따라 다릅니다.

## <a name="cause"></a>원인

이 오류로 인해 별도의 몇 가지 유형의 오류가 발생할 수 있습니다. 일반적으로 템플릿에는 구문 또는 구조 오류가 있습니다.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>해결 방법 1 - 구문 오류

템플릿 유효성 검사 실패를 나타내는 오류 메시지가 표시되면 템플릿 구문에 문제가 있습니다.

```Output
Code=InvalidTemplate
Message=Deployment template validation failed
```

템플릿 식에는 많은 요소가 있으므로 구문 오류가 발생할 수 있습니다. 예를 들어 스토리지 계정에 대한 이름 할당에는 작은 따옴표 또는 큰 따옴표, 중괄호, 대괄호 및 괄호 쌍이 포함됩니다. 식에는 달러 기호, 쉼표 및 점과 같은 함수와 문자도 포함됩니다.


# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

---

이러한 유형의 오류가 발생하면 식의 구문을 검토합니다. 템플릿 오류를 식별하려면 최신 [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) 또는 [Azure Resource Manager](https://code.visualstudio.com) Tools 확장과 [함께 Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)사용할 수 있습니다.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>해결 방법 2 - 잘못된 세그먼트 길이

리소스 이름이 올바른 형식이 아닐 경우 다른 잘못된 템플릿 오류가 발생합니다. 이 오류를 해결하려면 [이름 및 형식 불일치 오류 해결을](error-invalid-name-segments.md)참조하세요.

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-isnt-valid"></a>솔루션 3 - 매개 변수가 잘못되었습니다.

템플릿에서 매개 변수의 허용되는 값을 지정할 수 있습니다. 배포하는 동안 허용되는 값이 아닌 값을 제공하면 다음 오류와 유사한 메시지가 표시됩니다.

```Output
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

매개 변수의 허용되는 값에 대한 템플릿을 확인하고 배포 중에 허용되는 값을 사용합니다. 자세한 내용은 [Bicep](../bicep/parameters.md#allowed-values) 또는 ARM 템플릿에 허용되는 값을 [참조하세요.](../templates/parameters.md#allowed-values)

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>솔루션 4 - 대상 리소스 그룹이 너무 많습니다.

단일 배포에서는 5개의 대상 리소스 그룹으로 제한되어 있기 때문에 이전 배포에서 이 오류가 표시될 수 있습니다. 2020년 5월, 이 제한이 800개의 리소스 그룹으로 증가되었습니다. 자세한 내용은 [Bicep](../bicep/deploy-to-resource-group.md#deploy-to-multiple-resource-groups) 또는 ARM 템플릿에 대한 여러 리소스 그룹에 배포하는 방법을 [참조하세요.](../templates/deploy-to-resource-group.md#deploy-to-multiple-resource-groups)

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>해결 방법 5 - 순환 종속성이 발견됨

이 오류 메시지는 배포가 시작될 수 없도록 리소스가 서로 종속되어 있는 경우에 표시됩니다. 상호 종속성의 조합은 둘 이상의 리소스가 이미 대기 중인 다른 리소스를 기다리게 만듭니다. 예를 들어 리소스1은 리소스3에 종속되고, 리소스2는 리소스1에 종속되고, 리소스3은 리소스2에 종속됩니다. 일반적으로 이런 문제는 불필요한 종속성을 제거하여 해결할 수 있습니다.

Bicep은 한 리소스가 다른 리소스의 기호 이름을 사용하는 경우 암시적 종속성을 만듭니다. 를 사용하는 명시적 종속성은 `dependsOn` 일반적으로 필요하지 않습니다. 자세한 내용은 Bicep [dependencies를 참조하세요.](../bicep/resource-declaration.md#dependencies)

순환 종속성을 해결하려면:

1. 템플릿에서 순환 종속성 내에 식별된 리소스를 찾습니다.
1. 해당 리소스의 경우 `dependsOn` 속성 및 함수의 사용 방법을 검사하여 함수가 `reference` 의존하는 리소스를 확인합니다.
1. 해당 리소스를 검토하여 어떤 리소스에 종속되는지 확인합니다. 원래 리소스에 종속되는 리소스를 확인할 때까지 종속성을 추적합니다.
1. 순환 종속성과 관련된 리소스의 경우 속성의 모든 사용을 신중하게 `dependsOn` 검토하여 필요하지 않은 종속성을 식별합니다. 그러한 종속성을 제거합니다. 종속성이 필요한지 확신이 안 되면 해당 종속성을 제거해 봅니다.
1. 템플릿을 다시 배포합니다.

속성에서 값을 `dependsOn` 제거하면 템플릿을 배포할 때 오류가 발생할 수 있습니다. 오류가 발생하면 해당 종속성을 템플릿에 다시 추가합니다.

이러한 방법으로 순환 종속성 문제가 해결되지 않으면 일부 배포 논리를 자식 리소스(예: 확장 또는 구성 설정)로 이동하는 것이 좋습니다. 순환 종속성에 관련된 리소스를 배포한 후에 자식 리소스를 배포하도록 구성합니다. 예를 들어 두 개의 가상 머신을 배포하지만 각 컴퓨터에 서로를 참조하는 속성을 설정해야 한다고 가정합니다. 이런 경우 다음과 같은 순서로 배포할 수 있습니다.

1. vm1
1. vm2
1. vm1의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm2에서 얻은 값을 vm1에 설정합니다.
1. vm2의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm1에서 얻은 값을 vm2에 설정합니다.

동일한 방식이 App Service 앱에 적합합니다. 구성 값을 앱 리소스의 자식 리소스로 이동하는 것이 좋습니다. 두 개의 웹앱을 다음과 같은 순서로 배포할 수 있습니다.

1. webapp1
1. webapp2
1. webapp1에 대한 구성은 webapp1 및 webapp2에 따라 달라집니다. webapp2의 값을 사용하는 앱 설정이 포함됩니다.
1. webapp2에 대한 구성은 webapp1 및 webapp2에 따라 달라집니다. webapp1의 값을 사용하는 앱 설정이 포함됩니다.
