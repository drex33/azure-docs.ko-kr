---
title: 구성 집합 패턴
description: 구성 집합 패턴에 대해 설명합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 11/30/2021
ms.openlocfilehash: cdb890437fee5fe16f4e7b6b38120fd109e7c5ab
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133268556"
---
# <a name="configuration-set-pattern"></a>구성 집합 패턴

많은 개별 매개 변수를 정의하는 대신, 미리 정의된 값 세트를 만듭니다. 배포하는 동안 사용할 값 세트를 선택합니다.

## <a name="context-and-problem"></a>컨텍스트 및 문제점

단일 Bicep 파일은 종종 많은 리소스를 정의합니다. 각 리소스는 배포하는 환경에 따라 다른 구성을 사용해야 할 수 있습니다. 예를 들어, App Service 플랜 및 앱과 스토리지 계정을 배포하는 Bicep 파일을 빌드할 수 있습니다. 이러한 각 리소스에는 비용, 가용성 및 복원력에 영향을 미치는 여러 옵션이 있습니다. 프로덕션 환경의 경우 고가용성 및 복원력을 우선적으로 고려하는 하나의 구성 집합을 사용할 수 있습니다. 비프로덕션 환경의 경우 비용 절감을 우선적으로 고려하는 다른 구성 집합을 사용할 수 있습니다.

각 구성 설정에 대한 매개 변수를 만들 수 있지만 이렇게 할 경우 다음과 같은 몇 가지 단점이 있습니다.

- 이 방법은 각 리소스에 사용할 값과 각 매개 변수 설정이 미치는 영향을 이해해야 하므로 템플릿 사용자에게 부담을 줍니다.
- 템플릿의 매개 변수 수는 정의하는 리소스마다 증가합니다.
- 사용자가 테스트되지 않았거나 제대로 작동하지 않는 매개 변수 값의 조합을 선택할 수 있습니다.

## <a name="solution"></a>솔루션

환경 유형을 지정하는 단일 매개 변수를 만듭니다. 변수를 사용하여 매개 변수 값에 따라 각 리소스에 대한 구성을 자동으로 선택합니다.

> [!NOTE]
> 이 방식을 _티셔츠 크기 조정_ 이라고도 합니다. 티셔츠를 구입할 때 길이, 폭, 소매 등에 대한 많은 옵션이 제공되지는 않습니다. 소형, 중형 및 대형 중에서 선택할 수만 있으며 티셔츠 디자이너가 해당 크기에 따라 이러한 측정값을 미리 정의했을 것입니다.

## <a name="example"></a>예

두 가지 환경 유형인 비프로덕션 환경과 프로덕션 환경에 배포할 수 있는 템플릿이 있다고 가정합니다. 다음과 같이 환경 유형에 따라 필요한 구성이 다릅니다.

| 속성 | 비프로덕션 환경 | 프로덕션 환경 |
|-|-|-|
| **App Service 계획** |
| SKU name | S2 | P2V3 |
| 용량(인스턴스 수) | 1 | 3 |
| **App Service 앱** |
| Always On | 사용 안 함 | 사용 |
| **스토리지 계정** |
| SKU name | Standard_LRS | Standard_ZRS |

이 템플릿의 구성 집합 패턴을 사용할 수 있습니다.

프로덕션 또는 비프로덕션과 같은 환경 유형을 나타내는 단일 매개 변수를 허용합니다. `@allowed` 매개 변수 데코레이터를 사용하여 템플릿의 사용자가 예상한 값만 제공하도록 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="5-9" :::

그런 다음, 환경 유형에 따라 특정 구성을 정의하는 개체인 _맵 변수_ 를 만듭니다. 변수에는 `Production` 및 `NonProduction`이라는 두 개의 개체가 있습니다. 이러한 이름은 앞의 예제에 나오는 매개 변수에 허용되는 값과 일치합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="16-49" :::

리소스를 정의할 때 다음과 같이 구성 맵을 사용하여 리소스 속성을 정의합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="51-74" highlight="4, 14, 23" :::

## <a name="considerations"></a>고려 사항

- 맵 변수에서 리소스별로 속성을 그룹화하여 정의를 간소화하는 것이 좋습니다.
- 맵 변수에서 개별 속성 값(예제의 `alwaysOn` 속성) 또는 개체 속성(예제의 SKU 속성)을 설정하는 개체 변수를 둘 다 정의할 수 있습니다.
- [리소스 조건](conditional-resource-deployment.md)에 구성 집합을 사용하는 것이 좋습니다. 이렇게 하면 Bicep 코드가 특정 리소스를 특정 환경에만 배포하고 다른 환경에는 배포할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[공유 변수 파일 패턴에 대해 알아봅니다.](patterns-shared-variable-file.md)
