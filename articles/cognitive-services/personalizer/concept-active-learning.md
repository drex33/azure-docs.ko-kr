---
title: 학습 정책 - Personalizer
description: 학습 정책 설정은 모델 학습의 *하이퍼 매개 변수* 를 결정합니다. 서로 다른 학습 설정에서 학습된 동일한 데이터의 두 모델은 서로 다르게 작동합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aad256981001d3b115570ddac0c3cf4993bc2da1
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830741"
---
# <a name="learning-policy-and-settings"></a>학습 정책 및 설정

학습 정책 설정은 모델 학습의 *하이퍼 매개 변수* 를 결정합니다. 서로 다른 학습 설정에서 학습된 동일한 데이터의 두 모델은 서로 다르게 작동합니다.

[학습 정책 및 설정](how-to-settings.md#configure-rewards-for-the-feedback-loop)은 Azure Portal의 Personalizer 리소스에 대해 설정됩니다.

## <a name="import-and-export-learning-policies"></a>학습 정책 가져오기 및 내보내기

Azure Portal에서 학습 정책 파일을 가져오고 내보낼 수 있습니다. 이 방법을 사용하면 기존 정책을 저장하고 테스트하고 바꾸고 나중에 참조 및 감사하기 위한 아티팩트로 원본 코드 제어에 보관할 수 있습니다.

Personalizer 리소스에 대해 Azure Portal에서 학습 정책을 가져오고 내보내는 [방법](how-to-manage-model.md#import-a-new-learning-policy)에 대해 알아봅니다.

## <a name="understand-learning-policy-settings"></a>학습 정책 설정 이해

학습 정책의 설정은 변경할 수 없도록 되어 있습니다. Personalizer에 미치는 영향을 이해하는 경우에만 설정을 변경해야 합니다. 이를 알지 못하면 Personalizer 모델 무효화 등의 문제가 발생할 수 있습니다.

Personalizer는 [vowpalwabbit](https://github.com/VowpalWabbit)를 사용하여 이벤트를 학습하고 점수를 계산합니다. vowpalwabbit를 사용하여 학습 설정을 편집하는 방법은 [vowpalwabbit 설명서](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)를 참조하세요. 올바른 명령줄 인수를 사용하는 경우 명령을 다음 형식(인수 속성 값을 원하는 명령으로 바꿈)으로 파일에 저장하고 Personalizer 리소스에 대한 Azure Portal의 **모델 및 학습 설정** 창에서 학습 설정을 가져올 수 있도록 파일을 업로드합니다.

다음 `.json`은 학습 정책의 예입니다.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>학습 정책 비교

[오프라인 평가](concepts-offline-evaluation.md)를 수행하여 Personalizer 로그의 과거 데이터를 기준으로 서로 다른 학습 정책이 수행된 방법을 비교할 수 있습니다.

[자신의 학습 정책을 업로드](how-to-manage-model.md)하여 현재 학습 정책과 비교합니다.

## <a name="optimize-learning-policies"></a>학습 정책 최적화

Personalizer는 [오프라인 평가](how-to-offline-evaluation.md)에서 최적화된 학습 정책을 만들 수 있습니다. 오프라인 평가에 더 나은 보상을 제공하는 최적화된 학습 정책은 Personalizer에서 온라인으로 사용되는 경우 더 나은 결과를 얻습니다.

학습 정책을 최적화한 후에는 이 정책을 Personalizer에 직접 적용할 수 있으므로 현재 정책이 즉시 바뀝니다. 또는 추가 평가를 위해 최적화된 정책을 저장하고 나중에 이를 삭제, 저장 또는 적용할지 여부를 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [활성 및 비활성 이벤트](concept-active-inactive-events.md) 알아보기
