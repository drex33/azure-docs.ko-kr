---
title: 학습 동작 구성
description: 실습생 모드는 Personalizer 서비스 및 기계 학습 기능에 대한 확신을 제공하고, 서비스가 온라인 트래픽의 위험 없이 학습 가능한 정보를 전송하는 메트릭을 제공합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 7678d6ebf35391ad50b91a6d597c65637f33d8d9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046376"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Personalizer 학습 동작 구성

[실습생 모드](concept-apprentice-mode.md)를 통해 Personalizer 서비스 및 기계 학습 기능에 대한 신뢰와 확신을 가질 수 있으며, 서비스가 온라인 트래픽의 위험 없이 학습 가능한 정보가 전송되는 것을 확인할 수 있습니다.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>실습생 모드 구성

1. Personalizer 리소스를 위해 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **구성** 페이지의 **학습 동작** 탭에서 **기준선 작업 반환, 실습생으로 학습** 을 선택한 다음 **저장** 을 선택합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal에서 실습생 모드 학습 동작을 구성하는 스크린샷](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>기존 애플리케이션 변경

기존 애플리케이션이 현재 표시할 작업을 선택하는 방식이나 애플리케이션이 해당 작업의 **보상** 값을 결정하는 방식을 변경해서는 안 됩니다. 애플리케이션의 유일한 변경 내용은 Personalizer 순위 API에 전송된 작업의 순서일 수 있습니다. 애플리케이션에 현재 표시된 작업이 작업 목록에서 _첫 번째 작업_ 으로 전송됩니다. [순위 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)는 이 첫 번째 작업을 사용하여 Personalizer 모델을 교육합니다.

### <a name="configure-your-application-to-call-the-rank-api"></a>순위 API를 호출하도록 애플리케이션 구성

애플리케이션에 Personalizer를 추가하려면 순위 API 및 보상 API를 호출해야 합니다.

1. 작업 및 기능 목록을 결정하는 기존 애플리케이션 논리의 포인트 뒤에 [순위 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 호출을 추가합니다. 작업 목록의 첫 번째 작업은 기존 논리에서 선택된 작업이어야 합니다.

1. 순위 API 응답의 **보상 작업 ID** 와 관련된 작업을 표시하도록 코드를 구성합니다.

### <a name="configure-your-application-to-call-reward-api"></a>보상 API를 호출하도록 애플리케이션 구성

1. 기존 비즈니스 논리를 사용하여 표시된 작업의 **보상** 을 계산합니다. 값은 0에서 1 사이의 범위에 있어야 합니다. [보상 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)를 사용하여 Personalizer에 이 보상을 보냅니다. 보상 값은 즉시 예상되지 않으며 비즈니스 논리에 따라 일정 시간 동안 지연될 수 있습니다.

1. 구성된 **보상 대기 시간** 내에 보상을 반환하지 않으면 기본 보상이 대신 사용됩니다.

## <a name="evaluate-apprentice-mode"></a>실습생 모드 평가

Azure Portal의 Personalizer 리소스에 대한 **평가** 페이지에서 **현재 학습 동작 성능** 을 검토합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal의 실습생 모드 학습 동작 평가를 검토하는 스크린샷](media/settings/evaluate-apprentice-mode.png)

실습생 모드는 다음과 같은 **평가 메트릭** 을 제공합니다.
* **기준 – 평균 보상**: 애플리케이션 기본값(기준)의 평균 보상입니다.
* **Personalizer – 평균 보상**: Personalizer가 잠재적으로 도달할 수 있는 총 보상의 평균입니다.
* **가장 최근의 1,000개 이벤트에서 보상 업적 비율**: 최근 1000개의 이벤트에 대해 정규화된 기준 및 Personalizer 보상 비율입니다.

## <a name="switch-behavior-to-online-mode"></a>온라인 모드로 동작 전환

Personalizer가 평균 75~85%의 롤링 평균으로 학습되었다고 판단되면 모델이 온라인 모드로 전환할 준비가 된 것입니다.

Personalizer 리소스에 대한 Azure Portal의 **구성** 페이지에 있는 **학습 동작** 탭에서 **최상의 작업 반환** 을 선택한 다음 **저장** 을 선택합니다.

순위 API 및 보상 API 호출을 변경할 필요는 없습니다.

## <a name="next-steps"></a>다음 단계

* [모델 및 학습 설정 관리](how-to-manage-model.md)
