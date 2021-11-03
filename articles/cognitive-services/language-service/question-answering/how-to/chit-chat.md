---
title: 기술 자료의 사용자 지정 질문에 chitchat 추가
titleSuffix: Azure Cognitive Services
description: 봇에 개인 chitchat을 추가하면 프로젝트를 만들 때 대화와 참여도를 높입니다. 사용자 지정 질문 답변을 사용하면 미리 채워진 상위 chitchat 집합을 기술 자료로 쉽게 추가할 수 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: c925fdf1b5b53f703d7c996110fe1b6b5f52cde8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476588"
---
# <a name="use-chitchat-with-a-project"></a>프로젝트에서 chitchat 사용

봇에 chitchat을 추가하면 대화 및 참여도를 높입니다. 사용자 지정 질문 답변의 chitchat 기능을 사용하면 미리 채워진 상위 chitchat 집합을 기술 자료(KB)에 쉽게 추가할 수 있습니다. 이는 봇 성격의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다.

이 데이터 세트에는 Professional, 친숙한 및 을 같게 하는 여러 개인의 음성으로 chitchat의 약 100개 시나리오가 있습니다. 봇의 음성과 가장 유사한 가상 사용자를 선택하세요. 사용자 쿼리가 제공되면 질문 답변은 가장 가까운 알려진 chitchat 질문 및 답변과 일치시키려고 시도합니다.

다음은 다른 성격의 몇 가지 예입니다. 모든 퍼스낼리티 [데이터 집합](https://github.com/microsoft/botframework-cli/blob/main/packages/qnamaker/docs/chit-chat-dataset.md)을 퍼스낼리티 세부 정보와 함께 볼 수 있습니다.

`When is your birthday?`의 사용자 쿼리에 대해 각 퍼스낼리티에는 다음과 같은 스타일의 응답이 있습니다.

<!-- added quotes so acrolinx doesn't score these sentences -->
|개성|예제|
|--|--|
|Professional|나이가 저에게 적용되지 않습니다.|
|친숙한|저는 나이가 정말 없어요.|
|재치있는|저는 나이와는 관계가 없어요.|
|배려하는|저는 나이가 없어요.|
|열정적인|저는 봇입니다. 따라서 나이가 없습니다.|
||

## <a name="language-support"></a>언어 지원

Chitchat 데이터 집합은 다음 언어로 지원됩니다.

|언어|
|--|
|중국어|
|영어|
|프랑스어|
|독일|
|이탈리아어|
|일본어|
|한국어|
|포르투갈어|
|스페인어|

## <a name="add-chitchat-source"></a>chitchat 원본 추가
프로젝트를 만든 후에는 원본 관리 창에서 URL, 파일 및 chitchat의 원본을 추가할 수 **있습니다.**

> [!div class="mx-imgBorder"]
> ![원본 chitchat 추가](../media/chit-chat/add-source.png)

원하는 개성을 chitchat 베이스로 선택합니다.

> [!div class="mx-imgBorder"]
> ![다양한 chitchat 개성 메뉴](../media/chit-chat/personality.png)

## <a name="edit-your-chitchat-questions-and-answers"></a>chitchat 질문 및 답변 편집

기술 자료가 편집되면 선택한 성격에 따라 chitchat에 대한 새 원본이 표시됩니다. 이제 다른 모든 원본과 마찬가지로 변경된 질문을 추가하거나 응답을 편집할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![chitchat 질문 쌍 편집](../media/chit-chat/edit-chit-chat.png)

컨텍스트 및 메타데이터에 대한 보기를 켜고 끄려면 도구 모음에서 **열 표시를** 선택합니다.

## <a name="add-more-chitchat-questions-and-answers"></a>더 많은 chitchat 질문 및 답변 추가

미리 정의된 데이터 집합에 없는 새 chitchat 질문 쌍을 추가할 수 있습니다. chitchat 집합에서 이미 다루는 질문 쌍을 복제하지 않는지 확인합니다. 새 잡담 질문 쌍을 추가하면 **편집** 원본에 추가됩니다. 순위가 chitchat임을 이해하려면 다음 이미지와 같이 메타데이터 키/값 쌍 "편집: chitchat"을 추가합니다.

:::image type="content" source="../media/chit-chat/add-new-chit-chat.png" alt-text="chitchat 질문 답변 쌍 추가" lightbox="../media/chit-chat/add-new-chit-chat.png":::

## <a name="delete-chitchat-from-your-project"></a>프로젝트에서 chitchat 삭제

원본 **관리** 창을 선택하고 chitchat 원본을 선택합니다. 특정 chitchat 원본은 선택한 성격 이름을 가진 tsv 파일로 나열됩니다. 도구 모음에서 **삭제** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![chitchat 원본 삭제](../media/chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 가져오기](./migrate-knowledge-base.md)
