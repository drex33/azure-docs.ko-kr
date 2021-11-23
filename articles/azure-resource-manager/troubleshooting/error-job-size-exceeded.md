---
title: 작업 크기 초과 오류
description: 작업 크기나 템플릿이 너무 클 경우 발생하는 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/22/2021
ms.openlocfilehash: dfd797153649a887853e1848f379b894356d5f56
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132941855"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>작업 크기 초과에 대한 오류 해결

이 문서에서는 및 오류를 해결하는 방법을 `JobSizeExceededException` `DeploymentJobSizeExceededException` 설명합니다. ARM 템플릿(Azure Resource Manager 템플릿) 또는 Bicep 파일을 배포할 때 오류가 발생할 수 있습니다.

## <a name="symptom"></a>증상

템플릿을 배포할 때 배포 제한이 초과되었다는 오류가 표시됩니다.

## <a name="cause"></a>원인

배포가 허용된 제한을 초과하면 이 오류가 발생합니다. 일반적으로 이 오류는 배포를 실행하는 작업 또는 템플릿 중 하나가 너무 클 때 표시됩니다.

배포 작업은 1MB를 초과할 수 없으며 요청에 대한 메타데이터를 포함합니다. 큰 템플릿의 경우 템플릿과 결합된 메타데이터가 작업의 허용 크기를 초과할 수 있습니다.

템플릿은 4MB를 초과할 수 없습니다. 4MB 제한은 루프를 사용하여 많은 인스턴스를 만드는 리소스 정의에 대해 확장된 후 템플릿의 최종 상태에 적용됩니다. 최종 상태에는 변수 및 매개 변수에 대해 확인된 값도 포함됩니다.

다른 템플릿 제한은 다음과 같습니다.

- 매개 변수 256개
- 변수 256개
- 리소스 800개(인쇄 매수 포함)
- 출력 값 64개
- 템플릿 식의 문자 24,576자

## <a name="solution-1---use-dependencies-carefully"></a>해결 방법 1 - 신중하게 의존성 사용

# <a name="bicep"></a>[Bicep](#tab/bicep)

리소스가 기호 이름으로 다른 리소스를 참조할 때 생성되는 [암시적 종속성을](../bicep/resource-declaration.md#implicit-dependency) 사용합니다. 대부분의 배포에서는 를 사용하고 `dependsOn` [명시적 종속성을](../bicep/resource-declaration.md#explicit-dependency)만들 필요가 없습니다.

# <a name="json"></a>[JSON](#tab/json)

[복사](../templates/copy-resources.md) 루프를 사용하여 리소스를 배포하는 경우 루프 이름을 종속성으로 사용하지 마세요.

```json
dependsOn: [ "nicLoop" ]
```

대신 종속되어야 하는 루프에서 리소스의 인스턴스를 사용합니다. 예를 들면 다음과 같습니다.

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

---

## <a name="solution-2---simplify-template"></a>솔루션 2 - 템플릿 단순화

# <a name="bicep"></a>[Bicep](#tab/bicep)

파일이 다양한 리소스 종류를 배포하는 경우 [모듈로](../bicep/modules.md)분할하는 것이 좋습니다. 리소스 종류를 논리 그룹으로 나누고 각 그룹에 대한 모듈을 추가합니다. 예를 들어 네트워킹 리소스를 많이 배포해야 하는 경우 해당 리소스를 모듈로 이동할 수 있습니다.

다른 리소스를 암시적 의존성으로 설정하고 [모듈의 출력에서 값을 얻을](../bicep/outputs.md#outputs-from-modules)수 있습니다.


# <a name="json"></a>[JSON](#tab/json)

템플릿이 다양한 리소스 종류를 배포하는 경우 [연결된 템플릿으로](../templates/linked-templates.md)나누는 것이 좋습니다. 리소스 종류를 논리 그룹으로 나누고 각 그룹에 대해 연결된 템플릿을 추가합니다. 예를 들어 많은 네트워킹 리소스를 배포해야 하는 경우 해당 리소스를 연결된 템플릿으로 이동할 수 있습니다.

다른 리소스를 연결된 템플릿에 종속된 것으로 설정하고 [연결된 템플릿의 출력에서 값](../templates/linked-templates.md#get-values-from-linked-template) 가져올 수 있습니다.

---

## <a name="solution-3---reduce-name-size"></a>솔루션 3 - 이름 크기 줄이기

# <a name="bicep"></a>[Bicep](#tab/bicep)

[매개 변수](../bicep/parameters.md), [변수](../bicep/variables.md), [출력](../bicep/outputs.md)에 사용하는 이름의 길이를 줄여 보세요. 이러한 값이 루프에서 반복되면 긴 이름을 여러 번 곱합니다.

# <a name="json"></a>[JSON](#tab/json)

[매개 변수](../templates/parameters.md), [변수](../templates/variables.md), [출력](../templates/outputs.md)에 사용하는 이름의 길이를 줄여 보세요. 이러한 값이 복사 루프를 통해 반복되면 긴 이름을 여러 번 곱합니다.

---
