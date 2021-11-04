---
title: 작업 크기 초과 오류
description: 작업 크기나 템플릿이 너무 클 경우 발생하는 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: a818cb2f19e929262a124bc094438564f4732e15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053642"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>작업 크기 초과에 대한 오류 해결

이 문서는 **JobSizeExceededException** 및 **DeploymentJobSizeExceededException** 오류를 해결하는 방법을 설명합니다.

## <a name="symptom"></a>증상

템플릿을 배포할 때 배포 제한이 초과되었다는 오류가 표시됩니다.

## <a name="cause"></a>원인

배포가 허용되는 한도 중 하나를 초과하면 이 오류가 발생합니다. 일반적으로 이 오류는 배포를 실행하는 작업 또는 템플릿 중 하나가 너무 클 때 표시됩니다.

배포 작업은 1MB를 초과할 수 없습니다. 작업에는 요청에 대한 메타데이터가 포함됩니다. 큰 템플릿의 경우 템플릿과 결합된 메타데이터가 작업에 허용되는 크기를 초과할 수 있습니다.

템플릿은 4MB를 초과할 수 없습니다. 4MB 제한은 [복사본](../templates/copy-resources.md)을 사용하여 많은 인스턴스를 만드는 리소스 정의에 대해 확장된 템플릿의 최종 상태에 적용됩니다. 최종 상태에는 변수 및 매개 변수에 대해 확인된 값도 포함됩니다.

템플릿의 다른 한도는 다음과 같습니다.

* 매개 변수 256개
* 변수 256개
* 리소스 800개(인쇄 매수 포함)
* 출력 값 64개
* 템플릿 식의 문자 24,576자

복사 루프를 사용하여 리소스를 배포할 경우 루프 이름을 종속성으로 사용하지 마세요.

```json
dependsOn: [ "nicLoop" ]
```

대신 종속되어야 하는 루프에서 리소스의 인스턴스를 사용합니다. 예를 들면 다음과 같습니다.

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>해결 방법 1 - 템플릿 단순화

첫 번째 옵션은 템플릿을 단순화하는 것입니다. 이 옵션은 템플릿이 다양한 리소스 형식을 배포하는 경우에 작동합니다. 템플릿을 [연결된 템플릿](../templates/linked-templates.md)으로 분할하는 것이 좋습니다. 리소스 종류를 논리 그룹으로 나누고 각 그룹에 대해 연결된 템플릿을 추가합니다. 예를 들어 많은 네트워킹 리소스를 배포해야 하는 경우 해당 리소스를 연결된 템플릿으로 이동할 수 있습니다.

다른 리소스를 연결된 템플릿에 종속된 것으로 설정하고 [연결된 템플릿의 출력에서 값](../templates/linked-templates.md#get-values-from-linked-template) 가져올 수 있습니다.

## <a name="solution-2---reduce-name-size"></a>해결 방법 2 - 이름 크기 줄이기

[매개 변수](../templates/parameters.md), [변수](../templates/variables.md), [출력](../templates/outputs.md)에 사용하는 이름의 길이를 줄여 보세요. 이러한 값이 복사 루프를 통해 반복되면 많은 이름이 여러 배로 곱해집니다.
