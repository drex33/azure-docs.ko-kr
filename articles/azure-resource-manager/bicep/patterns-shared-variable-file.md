---
title: 공유 변수 파일 패턴
description: 공유 변수 파일 패턴을 설명합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 812cb0b861418d3bd3d13959cf074442d9a81538
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539422"
---
# <a name="shared-variable-file-pattern"></a>공유 변수 파일 패턴

Bicep 파일에서 공유 값 반복을 줄입니다. 대신 Bicep 파일 내의 공유 JSON 파일에서 해당 값을 로드합니다. 배열을 사용하는 경우 공유 값을 Bicep 코드의 배포 관련 값과 연결합니다.

## <a name="context-and-problem"></a>컨텍스트 및 문제점

Bicep 코드를 작성하는 경우 Bicep 파일 세트에서 다시 사용하는 공통 변수가 있을 수 있습니다. Bicep 파일 간에 값을 복사하여 붙여넣는 등의 방법으로 리소스를 선언할 때마다 값을 복제할 수 있습니다. 그러나 이 접근 방식은 오류가 발생하기 쉬우며 변경해야 하는 경우 다른 항목과 계속 동기화되도록 각 리소스 정의를 업데이트해야 합니다.

또한 배열로 정의된 변수를 사용하는 경우 여러 Bicep 파일에 공통 값 세트가 있을 수 있으며 배포 중인 리소스의 특정 값도 추가해야 합니다. 공유 변수를 리소스 관련 변수와 혼합할 경우 사용자가 두 범주의 변수 간 차이를 파악하기가 더 어렵습니다.

## <a name="solution"></a>솔루션

공유해야 하는 변수가 포함된 JSON 파일을 만듭니다. `json()` 및 `loadTextContent()` Bicep 함수를 사용하여 파일을 로드하고 변수에 액세스합니다. 배열 변수의 경우 `concat()` 함수를 사용하여 공유 값을 특정 리소스의 사용자 지정 값과 결합합니다.

## <a name="example-1-naming-prefixes"></a>예제 1: 명명 접두사

리소스를 정의하는 여러 Bicep 파일이 있다고 가정합니다. 모든 리소스에 대해 일관된 명명 접두사를 사용해야 합니다.

회사 전체에 적용되는 공통 명명 접두사를 포함하는 JSON 파일을 정의합니다.

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-prefixes.json" :::

Bicep 파일에서 공유 명명 접두사를 가져오는 변수를 선언합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="1" :::

리소스 이름을 정의하는 경우 문자열 보간을 사용하여 공유 이름 접두사를 고유한 이름 접미사와 연결합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="3-4" :::

## <a name="example-2-network-security-group-rules"></a>예제 2: 네트워크 보안 그룹 규칙

고유한 NSG(네트워크 보안 그룹)를 정의하는 여러 Bicep 파일이 있다고 가정합니다. 각 NSG에 적용해야 하는 일반적인 보안 규칙 세트가 있으며 추가해야 하는 애플리케이션 관련 규칙도 있습니다.

회사 전체에 적용되는 공통 보안 규칙을 포함하는 JSON 파일을 정의합니다.

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-rules.json" :::

Bicep 파일에서 공유 보안 규칙을 가져오는 변수를 선언합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="5" :::

이 특정 NSG에 대한 사용자 지정 규칙을 나타내는 변수 배열을 만듭니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="6-21" :::

NSG 리소스를 정의합니다. `concat()` 함수를 사용하여 두 개의 배열을 함께 결합하고 `securityRules` 속성을 설정합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="23-29" highlight="5" :::

## <a name="considerations"></a>고려 사항

- 이 접근 방식을 사용하는 경우 JSON 파일은 Bicep에서 생성된 ARM 템플릿 내에 포함됩니다. Bicep에서 생성된 JSON ARM 템플릿의 파일 한도는 4MB이므로 큰 공유 변수 파일 사용을 피해야 합니다.
- 공유 변수 배열이 각 Bicep 파일에 지정된 배열 값과 충돌하지 않는지 확인합니다. 예를 들어, 구성 세트 패턴을 사용하여 네트워크 보안 그룹을 정의하는 경우 동일한 우선 순위와 방향을 정의하는 여러 규칙이 없는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[구성 세트 패턴에 관해 알아봅니다.](patterns-configuration-set.md)
