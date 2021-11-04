---
title: 답변 범위 검색을 사용하여 정확한 답변 - 질문 답변
description: 질문 답변에서 사용할 수 있는 정확한 답변 기능을 이해합니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 432e65d3d2f3dc3d4bc71157f165311e7fad87e9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476664"
---
# <a name="precise-answering"></a>정확한 대답

도입된 정확한 응답 기능을 사용하면 모든 사용자 쿼리에 대한 기술 자료의 최적 후보 답변 통과에서 정확한 짧은 답변을 얻을 수 있습니다. 이 기능은 런타임에 딥 러닝 모델을 사용합니다. 이 모델은 사용자 쿼리의 의도를 이해하고 답변 통과에 팩트로서 짧은 답변이 있는 경우 답변 통과에서 정확한 짧은 답변을 검색합니다.

이 기능은 최종 사용자뿐만 아니라 콘텐츠 개발자에게 유용합니다. 이제 콘텐츠 개발자는 기술 자료의 모든 팩트에서 특정 질문 답변 쌍을 수동으로 큐레이팅할 필요가 없으며, 최종 사용자는 서비스에서 반환된 전체 답변 흐름을 검토하여 사용자의 쿼리에 응답하는 실제 사실을 찾을 필요가 없습니다.

## <a name="precise-answering-via-the-portal"></a>포털을 통한 정확한 응답

Language [Studio 포털](https://aka.ms/languageStudio)에서 테스트 창을 열면 위의 맨 위에 있는 **짧은 응답 응답 포함** 옵션이 고급 옵션 표시로 표시됩니다.

테스트 창에 쿼리를 입력할 때 대답 통로에 짧은 답변이 있는 경우 대답 통로와 함께 짧은 답변이 표시됩니다.

>[!div class="mx-imgBorder"]
>[![짧은 답변이 선택되고 짧은 답변 응답이 포함된 질문이 있는 테스트 창의 스크린샷.](../media/precise-answering/short-answer.png)](../media/precise-answering/short-answer.png#lightbox)

테스트 창에 긴 **응답** 통과만 표시하려면 짧은 **응답** 응답 포함 옵션을 선택 취소할 수 있습니다.

또한 이 서비스는 **검사** 옵션을 선택한 **다음, 추가 정보를** 선택하여 확인할 수 있는 응답 **범위 신뢰도 점수로** 정확한 답변의 신뢰도 점수를 반환합니다.

>[!div class="mx-imgBorder"]
>[![응답 범위 신뢰도 점수가 표시된 검사 창의 스크린샷.](../media/precise-answering/answer-confidence-score.png)](../media/precise-answering/answer-confidence-score.png#lightbox)

## <a name="deploying-a-bot"></a>봇 배포

봇을 게시하면 애플리케이션에서 기본적으로 정확한 답변 사용 환경을 가져올 수 있으며, 여기에서 대답 통로와 함께 짧은 답변이 표시됩니다. 응답에서 정확한 답변(AnswerSpan이라고 함)을 사용하는 방법을 보려면 REST API API 참조를 참조하세요. 사용자는 Bot 앱 서비스를 통해 템플릿을 업데이트하여 다른 환경을 유연하게 선택할 수 있습니다.
