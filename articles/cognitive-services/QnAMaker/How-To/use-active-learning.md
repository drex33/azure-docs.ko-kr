---
title: 기술 자료에 학습 활용 - QnA Maker
description: 활성 학습을 통한 기술 자료의 품질 향상을 알아봅니다. 기존 질문을 삭제하거나 변경하지 않고 검토, 수락, 거부 또는 추가합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d7a7eee1540690cf5d18516eeb602318e6d2c76
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005894"
---
# <a name="active-learning"></a>능동적 학습

_활성 학습 제안_ 기능을 사용하면 사용자가 제출한 정보에 따라 질문과 대답 쌍에 대체 질문을 제안하여 기술 자료 품질을 개선할 수 있습니다. 이 제안을 검토한 다음 기존 질문에 추가하거나 거부합니다.

기술 자료가 자동으로 변경되지는 않습니다. 모든 변경 내용을 적용하려면 제안을 수락해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="what-is-active-learning"></a>활성 학습의 개념

QnA Maker는 암시적/명시적 피드백을 사용하여 새로운 질문 변형을 학습합니다.

* [암시적 피드백](#how-qna-makers-implicit-feedback-works) – 사용자 질문에 점수가 거의 같은 대답이 여러 개 있으면 순위매기기 기능은 해당 대답을 파악하여 피드백으로 간주합니다. 사용자는 아무 작업도 할 필요가 없습니다.
* [명시적 피드백](#how-you-give-explicit-feedback-with-the-train-api) – 점수가 약간씩 다른 여러 대답이 기술 자료에서 반환되면 클라이언트 애플리케이션은 올바른 질문을 사용자에게 질문합니다. 이 경우 사용자의 명시적 피드백이 [학습 API](../How-To/improve-knowledge-base.md#train-api)를 통해 QnA Maker로 전송됩니다.

둘 중 어떤 방법을 사용하든 순위매기기 기능에 클러스터된 유사 쿼리가 제공됩니다.

## <a name="how-active-learning-works"></a>활성 학습의 작동 방식

활성 학습은 QnA Maker에서 반환하는 몇 가지 상위 대답 점수를 기준으로 트리거됩니다. 쿼리와 일치하는 QnA 쌍 간의 점수에 큰 차이가 없으면 쿼리는 가능한 각 QnA 쌍에 대해 가능한 제안(대체 질문)으로 간주됩니다. 특정 QnA 쌍에 대해 제안된 질문을 수락한 경우 다른 쌍에 대해서는 거부됩니다. 제안을 수락한 후에는 저장하고 학습해야 합니다.

엔드포인트가 적절한 수와 종류의 사용량 쿼리를 수신하면 활성 학습에서는 가능한 최적의 제안을 제공합니다. 5개 이상의 유사한 쿼리가 클러스터된 경우 30분마다 QnA Maker 기술 자료 디자이너에게 수락하거나 거부할 사용자 기반 질문을 제안합니다. 모든 제안은 유사성을 기준으로 클러스터되며, 최종 사용자가 특정 쿼리를 전송한 빈도에 따라 대체 질문의 상위 제안이 표시됩니다.

QnA Maker 포털에서 질문이 제안되면 해당 제안을 검토하고 수락 또는 거부해야 합니다. 제안을 관리하는 API는 없습니다.

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker의 암시적 피드백 작동 방식

QnA Maker의 암시적 피드백은 알고리즘을 사용하여 근접 점수를 결정한 후 활성 학습 제안을 만듭니다. 점수 범위를 결정하는 알고리즘은 단순한 계산이 아닙니다. 다음 예제의 범위는 고정할 수 없지만 알고리즘의 효과만 이해하기 위한 지침으로 사용해야 합니다.

질문의 점수 신뢰도가 80% 등으로 높은 경우 활성 학습용으로 간주되는 점수의 범위도 넓어집니다(대략 10% 이내). 반면 신뢰도 점수가 40% 등으로 낮아지면 점수 범위도 좁아집니다(대략 4% 이내).

QnA Maker의 generateAnswer에 대한 쿼리의 다음 JSON 대답에서 A, B, C의 점수는 거의 비슷하므로 제안으로 간주됩니다.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker는 어떤 대답이 가장 좋은 대답인지 알 수 없습니다. QnA Maker 포털의 제안 목록을 사용하여 최상의 대답을 선택하고 다시 학습합니다.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>학습 API를 사용하여 명시적 피드백을 제공하는 방법

QnA Maker에는 어떤 대답이 가장 좋은 대답인지에 대한 명시적인 피드백이 필요합니다. 가장 적합한 답을 결정하는 방법은 다음을 포함할 수 있습니다.

* 사용자 피드백: 대답 중 하나 선택
* 비즈니스 논리: 허용 가능한 점수 범위 등 결정
* 사용자 피드백과 비즈니스 논리의 조합

사용자가 선택하면 [학습 API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train)를 사용하여 QnA Maker로 올바른 대답을 보냅니다.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>활성 학습 사용을 위한 버전 업그레이드

활성 학습은 런타임 버전 4.4.0 이상에서 지원됩니다. 기술 자료가 이전 버전에서 작성된 경우 이 기능을 사용하려면 [런타임을 업그레이드](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)합니다.

## <a name="turn-on-active-learning-for-alternate-questions"></a>대체 질문에 대해 활성 학습 설정

활성 학습은 기본적으로 해제되어 있습니다. 제안된 질문을 확인하려면 활성 학습을 설정합니다. 활성 학습을 설정한 이후 클라이언트 앱에서 QnA Maker로 정보를 보내야 합니다. 자세한 내용은 [봇에서 GenerateAnswer 및 교육 API 사용을 위한 아키텍처 흐름](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)을 참조하세요.

1. **게시** 를 선택하여 기술 자료를 게시합니다. 활성 학습 쿼리는 GenerateAnswer API 예측 엔드포인트에서만 수집됩니다. QnA Maker 포털의 테스트 창에 대한 쿼리는 활성 학습에 영향을 미치지 않습니다.

1. QnA Maker 포털에서 활성 학습을 설정하려면 오른쪽 상단으로 이동하고, **이름** 을 선택하고, [**서비스 설정**](https://www.qnamaker.ai/UserSettings)으로 이동합니다.

    ![서비스 설정 페이지에서 활성 학습의 제안된 질문을 사용하도록 설정합니다. 오른쪽 상단 메뉴에서 사용자 이름을 선택하고 서비스 설정을 선택합니다.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker 서비스를 찾은 다음 **활성 학습** 을 설정 상태로 전환합니다.

    > [!div class="mx-imgBorder"]
    > [![서비스 설정 페이지에서 활성 학습 기능을 설정합니다. 기능을 설정할 수 없는 경우 서비스를 업그레이드해야 합니다.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 이전 이미지의 정확한 버전은 예시용입니다. 실제 버전은 다를 수 있습니다.

    **활성 학습** 이 사용하도록 설정되면 사용자가 제출한 질문에 따라 기술 자료에서 일정한 간격으로 새 질문을 제안합니다. 설정을 다시 전환하여 **활성 학습** 을 사용하지 않도록 설정할 수 있습니다.

## <a name="review-suggested-alternate-questions"></a>제안된 대체 질문 검토

각 기술 자료의 **편집** 페이지에서 [제안된 대체 질문을 검토](improve-knowledge-base.md)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](./manage-knowledge-bases.md)
