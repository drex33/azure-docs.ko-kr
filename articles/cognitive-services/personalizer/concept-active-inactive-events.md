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
ms.openlocfilehash: 948d375c0f580a71dbd27fa10660c0c7e0046a10
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219157"
---
# <a name="defer-event-activation"></a>이벤트 활성화 지연

이벤트의 지연 된 활성화를 사용 하 여 개인 설정 된 웹 사이트 또는 메일 캠페인을 만들 수 있습니다 .이 경우 사용자가 실제로 페이지를 보거나 전자 메일을 열 수 없습니다. 이러한 시나리오에서 응용 프로그램은 결과를 사용 하거나 사용자에 게 표시 하는 경우에도 알 수 있도록 순위를 먼저 호출 해야 합니다. 콘텐츠를 사용자에 게 표시 되지 않는 경우에는 기본 보상 (일반적으로 0)을 사용 하 여 학습할 수 없습니다.
지연 된 활성화를 사용 하면 한 시점에서 순위 호출의 결과를 사용 하 고 나중에 또는 코드의 다른 위치에서 이벤트를 파악 해야 하는지를 결정할 수 있습니다.

## <a name="typical-scenarios-for-deferred-activation"></a>지연 된 정품 인증에 대 한 일반적인 시나리오

이벤트 활성화 지연은 다음 예제 시나리오에서 유용 합니다.

* 사용자가 개인 설정 된 웹 페이지를 미리 렌더링 하지만 일부 비즈니스 논리가 Personalizer의 작업 선택을 재정의할 수 있으므로 사용자가이를 확인 하지 못할 수 있습니다.
* 웹 페이지에서 콘텐츠를 "접기" 아래에 개인 설정 하 고 있으며 사용자가 콘텐츠를 볼 수 없는 경우도 있습니다.
* 마케팅 전자 메일을 개인화 하 고 사용자가 열지 않은 전자 메일의 교육을 방지 해야 합니다.
* 동적 미디어 채널을 개인 설정 하 고 사용자가 Personalizer에서 선택 하는 노래 또는 비디오에 가져오기 전에 채널 재생을 중지할 수 있습니다. 

일반적으로 이러한 시나리오는 다음과 같은 경우에 발생 합니다.

* UI 또는 시간 제약 조건으로 인해 사용자에 게 표시 되거나 표시 되지 않을 수 있는 미리 렌더링 UI가 있습니다.
* 응용 프로그램에서 출력을 사용할지 여부를 확인 하기 전에 순위를 지정 하는 예측 개인 설정을 수행 하 고 있습니다.

## <a name="how-to-defer-activation-and-later-activate-events"></a>활성화를 지연 하 고 나중에 이벤트를 활성화 하는 방법

이벤트에 대 한 활성화를 지연 시키려면 bequest 본문에서를 사용 하 여 [Rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 를 호출 `deferActivation = True` 합니다.

사용자가 개인 설정 된 콘텐츠나 미디어를 표시 하 고 보상을 받을 것으로 예상 되는 즉시 해당 이벤트를 활성화 해야 합니다. 이렇게 하려면 eventId를 사용 하 여 [ACTIVATE API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) 를 호출 합니다.


보상 대기 시간 기간이 만료 되기 전에 해당 EventID 호출에 대 한 [활성화 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) 호출을 받아야 합니다.

### <a name="behavior-with-deferred-activation"></a>지연 된 활성화의 동작 

Personalizer는 다음과 같이 이벤트와 보상을 배웁니다.
* 를 사용 하 여 Rank를 호출 하 `deferActivation = True` 고, 해당 eventId에 대해 API를 호출 *하지 않고* , Personalizer를 호출 하는 경우에는 `Activate` 이벤트에서 학습할 수 없습니다.
* 를 사용 하 여 Rank를 호출 하 `deferActivation = True`  고, `Activate` 해당 eventId에 대 한 API를 호출 하 고, 보상을 호출 하는 경우 Personalizer는 지정 된 보상 점수를 사용 하 여 이벤트에서 학습 합니다.
* 를 사용 하 여 Rank를 호출 하 `deferActivation = True`  고 `Activate` 해당 eventId에 대해 API를 호출 하지만 보상을 생략 하는 경우 Personalizer는 구성에 설정 된 기본 보상 점수를 사용 하 여 이벤트에서 학습 합니다.

## <a name="next-steps"></a>다음 단계
* 로드하우를 통해 [기본 보상](how-to-settings.md#configure-rewards-for-the-feedback-loop)을 구성할 수 있습니다.
* [보상 점수 결정 방법 및 고려할 데이터](concept-rewards.md) 알아보기
