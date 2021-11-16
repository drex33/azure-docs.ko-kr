---
title: 키워드 인식 권장 사항 및 지침 - Speech Service
titleSuffix: Azure Cognitive Services
description: 키워드 인식 사용 시 권장 사항 및 지침에 대한 개요입니다.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: a6b7a9c072951a7010403c9e54dff844eb910d95
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490673"
---
# <a name="recommendations-and-guidelines-for-keyword-recognition"></a>키워드 인식에 대한 권장 사항 및 지침

이 문서에서는 키워드를 선택하여 정확성 특성을 최적화하는 방법과 키워드 확인을 통해 사용자 환경을 설계하는 방법을 설명합니다. 

## <a name="choosing-an-effective-keyword"></a>효과적인 키워드 선택

효과적인 키워드 만들기는 제품이 일관되고 정확하게 응답하도록 하는 것이 중요합니다. 키워드를 선택하는 경우 다음 지침을 고려합니다.

> [!NOTE]
> 아래 예제는 영어로 제공되지만 지침은 사용자 지정 키워드에서 지원되는 모든 언어에 적용됩니다. 지원되는 모든 언어 목록은 [언어 지원](language-support.md#custom-keyword-and-keyword-verification)을 참조하세요.

- 말하는 데 2초 이상 걸리지 않아야 합니다.
- 4-7개 음절의 단어가 가장 적합합니다. 예를 들어 "Hey, Computer"는 좋은 키워드이지만, 그냥 "Hey"만 사용하는 것은 그렇지 않습니다.
- 키워드는 최종 사용자의 네이티브 언어와 관련된 일반적인 발음 규칙을 따라야 합니다.
- 일반적인 발음 규칙을 따르는 고유하거나 심지어 합성된 단어는 가양성을 줄일 수 있습니다. 예를 들어 "computerama"는 좋은 키워드일 수 있습니다.
- 일반적인 단어는 선택하지 않도록 합니다. 예를 들어, "eat"과 “go”는 사람들이 평상시 대화에서 자주 말하는 단어입니다. 사용자의 제품에 대한 잘못된 허용률이 원하는 것보다 높을 수 있습니다.
- 대체 발음이 있을 수 있는 키워드는 사용하지 않는 것이 좋습니다. 사용자는 제품을 음성으로 활성화하기 위해 "올바른" 발음을 알고 있어야 합니다. 예를 들어 "509"는 "five zero nine", "five oh nine" 또는 "five hundred and nine"으로 발음할 수 있습니다. "R.E.I." "R E I" 또는 "Ray"로, "Live"는 "/līv/" 또는 "/liv/"로 발음할 수 있습니다.
- 특수 문자, 기호 또는 숫자는 사용하지 않도록 합니다. 예를 들어 "Go#" 및 "20+ cats"는 바람직하지 못한 키워드입니다. 그러나 "go sharp" 또는 "twenty plus cats"는 적합할 수 있습니다. 여전히 브랜딩에 기호를 사용하고, 마케팅 및 설명서를 사용하여 적절한 발음을 강화할 수 있습니다.


## <a name="user-experience-recommendations-with-keyword-verification"></a>키워드 확인을 통한 사용자 환경 권장 사항

[키워드 확인](keyword-recognition-overview.md#keyword-verification)을 사용하는 다단계 키워드 인식 시나리오에서 애플리케이션은 최종 사용자에게 키워드 검색 알림을 보낼 시기를 선택할 수 있습니다. 시각적 개체 또는 가청 표시기를 렌더링하기 위한 권장 사항은 키워드 확인 서비스의 응답을 사용하는 것입니다.

![정확성 최적화 시 사용자 환경 지침.](media/custom-keyword/kw-verification-ux-accuracy.png)

이를 통해 정확성 측면에서 최적의 환경을 보장하여 사용자에게 인식되는 잘못된 수락의 영향을 최소화하지만 추가 대기 시간이 발생합니다.

대기 시간 최적화가 필요한 애플리케이션의 경우, 애플리케이션은 디바이스 내 키워드 인식에 따라 최종 사용자에게 간편하고 눈에 잘 띄지 않는 표시기를 제공할 수 있습니다. 예를 들어 LED 패턴을 조명하거나 아이콘을 펄스합니다. 키워드 확인이 키워드 수락으로 응답하는 경우 표시기가 계속 존재하거나 응답이 키워드 거부인 경우 해제될 수 있습니다.

![대기 시간 최적화 시 사용자 환경 지침.](media/custom-keyword/kw-verification-ux-latency.png)

## <a name="next-steps"></a>다음 단계

* [Speech SDK를 가져옵니다.](speech-sdk.md)
* [음성 도우미에 대해 자세히 알아봅니다.](voice-assistants.md)
