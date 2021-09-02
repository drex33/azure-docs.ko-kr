---
title: 활성 및 비활성 이벤트 - Personalizer
description: 이 문서에서는 Personalizer 서비스 내에서 활성 및 비활성 이벤트를 사용하는 방법을 설명합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: ba95c22a773a382a3c03aab18f8f885e6a2791d8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831011"
---
# <a name="active-and-inactive-events"></a>활성 및 비활성 이벤트

**활성** 이벤트는 고객에게 결과를 표시하고 보상 점수를 결정하고 있는 것을 확인할 수 있는 순위에 대한 호출입니다. 이것은 기본적인 동작입니다.

**비활성** 이벤트는 비즈니스 논리로 인해 사용자에게 권장 작업이 표시되는지 확신할 수 없는 순위에 대한 호출입니다. 이를 통해 Personalizer가 기본 보상으로 교육되지 않도록 이벤트를 무시할 수 있습니다. 비활성 이벤트는 보상 API를 호출해서는 안 됩니다.

학습 루프는 실제 이벤트 유형을 알고 있어야 합니다. 비활성 이벤트에는 보상 호출이 없습니다. 활성 이벤트에는 보상 호출이 있어야 하지만 API 호출을 수행하지 않으면 기본 보상 점수가 적용됩니다. 사용자 환경에 영향을 주는 것이 확인되는 즉시 이벤트 상태를 비활성에서 활성으로 변경합니다.

## <a name="typical-active-events-scenario"></a>일반적인 활성 이벤트 시나리오

애플리케이션이 순위 API를 호출하면 해당 애플리케이션이 **rewardActionId** 필드에 표시해야 하는 작업이 수신됩니다.  이 순간부터 Personalizer는 동일한 eventId의 보상 점수가 있는 보상 호출을 예상합니다. 보상 점수는 향후 순위 호출을 위해 모델을 학습하는 데 사용됩니다. eventId에 대한 보상 호출이 수신되지 않으면 기본 보상이 적용됩니다. [기본 보상](how-to-settings.md#configure-rewards-for-the-feedback-loop)은 Azure Portal의 Personalizer 리소스에서 설정됩니다.

## <a name="other-event-type-scenarios"></a>기타 이벤트 유형 시나리오

일부 시나리오에서 애플리케이션은 결과가 사용자에게 사용될지 또는 표시될지를 알기 전에 순위를 호출해야 할 수 있습니다. 예를 들어 마케팅 캠페인이 홍보된 콘텐츠의 페이지 렌더링을 덮어쓰는 경우 이러한 상황이 발생할 수 있습니다. 순위 호출의 결과가 사용되지 않았고 사용자가 이를 확인하지 않은 경우에는 해당 보상 호출을 보내지 마세요.

일반적으로 이러한 시나리오는 다음과 같은 경우에 발생합니다.

* 사용자가 볼 수 있거나 볼 수 없는 UI를 미리 렌더링하는 경우
* 애플리케이션에서 실시간 컨텍스트가 거의 없이 순위 호출이 수행되고 애플리케이션이 출력을 사용하거나 사용하지 않을 수도 있는 예측 개인 설정을 수행하는 경우

이러한 경우 Personalizer를 사용하여 순위를 호출하고 이벤트를 _비활성화_ 하도록 요청합니다. Personalizer는 이 이벤트에 대한 보상을 예상할 수 없으며 기본 보상이 적용되지 않습니다.

비즈니스 논리에서 나중에 애플리케이션이 순위 호출 정보를 사용하는 경우 이벤트를 _활성화_ 하면 됩니다. 이벤트가 활성화되는 즉시 Personalizer는 이벤트 보상을 예상할 수 있습니다. 보상 API에 대한 명시적 호출이 없으면 Personalizer가 기본 보상을 적용합니다.

## <a name="inactive-events"></a>비활성 이벤트

이벤트에 대한 학습을 사용하지 않도록 설정하려면 `learningEnabled = False`를 사용하여 순위를 호출합니다.

비활성 이벤트의 경우 eventId에 대한 보상을 보내거나 해당 eventId에 대한 `activate` API를 호출하면 학습이 암시적으로 활성화됩니다.

## <a name="next-steps"></a>다음 단계

* [보상 점수 결정 방법 및 고려할 데이터](concept-rewards.md) 알아보기
