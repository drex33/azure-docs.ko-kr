---
title: 사용자 지정 질문에 응답 하는 기술 자료에 chitchat 추가
titleSuffix: Azure Cognitive Services
description: Bot에 개인 chitchat를 추가 하면 프로젝트를 만들 때 더 많은 정보를 사용 하 여 대화형으로 연결할 수 있습니다. 사용자 지정 질문 답변을 사용 하면 상위 chitchat의 미리 채워진 집합을 기술 자료에 쉽게 추가할 수 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2e46b7b877e3ce8e82782356339261ff5204bf26
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052931"
---
# <a name="use-chitchat-with-a-knowledge-base"></a>기술 자료에서 chitchat 사용

Bot에 chitchat를 추가 하면 더 자세히 이야기 하 고 유용 합니다. 사용자 지정 질문 응답의 chitchat 기능을 사용 하면 상위 chitchat의 미리 채워진 집합을 기술 자료 (KB)에 쉽게 추가할 수 있습니다. 이는 봇 성격의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다.

이 데이터 집합에는 Professional, 친숙 한 및 Witty 같은 여러 가상 사용자의 음성에서 chitchat의 약 100 시나리오가 있습니다. 봇의 음성과 가장 유사한 가상 사용자를 선택하세요. 사용자 쿼리가 지정 된 경우 질문 응답은 가장 가까운 알려진 chitchat 질문과 대답을 사용 하 여 일치 하려고 합니다.

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

Chitchat 데이터 집합은 다음 언어로 지원 됩니다.

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

## <a name="add-chitchat-source"></a>Chitchat 원본 추가
프로젝트를 만든 후에는 **소스 관리** 창에서 url, 파일 및 chitchat의 소스를 추가할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![원본 chitchat 추가](../media/chit-chat/add-source.png)

Chitchat 밑으로 사용할 개성을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![여러 chitchat 개성의 메뉴](../media/chit-chat/personality.png)

## <a name="edit-your-chitchat-questions-and-answers"></a>Chitchat 질문 및 답변 편집

기술 자료를 편집할 때 선택한 사용자의 취향에 따라 chitchat의 새 원본이 표시 됩니다. 이제 다른 모든 원본과 마찬가지로 변경된 질문을 추가하거나 응답을 편집할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![Chitchat 질문 쌍 편집](../media/chit-chat/edit-chit-chat.png)

컨텍스트 및 메타 데이터에 대 한 뷰를 설정 및 해제 하려면 도구 모음에서 **열 표시** 를 선택 합니다.

## <a name="add-more-chitchat-questions-and-answers"></a>Chitchat 질문 및 답변 추가

미리 정의 된 데이터 집합에 없는 새 chitchat 질문 쌍을 추가할 수 있습니다. Chitchat 집합에 이미 포함 되어 있는 질문 쌍을 복제 하지 않았는지 확인 합니다. 새 chitchat 질문 쌍을 추가 하면 **편집** 원본에 추가 됩니다. Ranker가 chitchat 임을 이해 하려면 다음 이미지에 표시 된 것 처럼 메타 데이터 키/값 쌍 "편집: chitchat"을 추가 합니다.

:::image type="content" source="../media/chit-chat/add-new-chit-chat.png" alt-text="Chitchat 질문 대답 쌍 추가" lightbox="../media/chit-chat/add-new-chit-chat.png":::

## <a name="delete-chitchat-from-your-project"></a>프로젝트에서 chitchat 삭제

**원본 관리** 창을 선택 하 고 chitchat 원본을 선택 합니다. 특정 chitchat 원본은 선택한 사용자 이름으로 tsv 파일로 나열 됩니다. 도구 모음에서 **삭제** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![Chitchat 원본 삭제](../media/chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 가져오기](./migrate-knowledge-base.md)
