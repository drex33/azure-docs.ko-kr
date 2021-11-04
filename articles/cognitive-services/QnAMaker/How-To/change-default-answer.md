---
title: 기본 대답 가져오기 - QnA Maker
description: 질문과 일치하는 항목이 없는 경우 기본 대답이 반환됩니다. 표준 기본 대답에서 기본 대답을 변경하려고 할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7c73b08a634d1b68c3d9275dc39cc7cd95d51a6f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069276"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker 리소스의 기본 대답 변경

대답을 찾을 수 없는 경우 기술 자료의 기본 대답이 반환됩니다. [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna)와 같은 클라이언트 애플리케이션을 사용하는 경우 점수 임계값을 충족하는 대답이 없음을 나타내는 별도의 기본 대답이 있을 수도 있습니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="types-of-default-answer"></a>기본 대답 유형

기술 자료에는 두 가지 유형의 기본 대답이 있습니다. 예측 쿼리에서 각 대답이 반환되는 방법과 시기를 이해하는 것이 중요합니다.

|기본 대답 유형|대답에 대한 설명|
|--|--|
|대답을 결정할 수 없는 경우의 KB 대답|`No good match found in KB.` - [GenerateAnswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)가 질문에 일치하는 대답을 찾지 못하면 앱 서비스의 `DefaultAnswer` 설정이 반환됩니다. 동일한 QnA Maker 리소스의 모든 기술 자료는 동일한 기본 대답 텍스트를 공유합니다.<br>설정을 [가져오거나](/rest/api/appservice/webapps/listapplicationsettings) [업데이트](/rest/api/appservice/webapps/updateapplicationsettings)하기 위해 앱 서비스를 통하거나 REST API를 사용하여 Azure Portal에서 설정을 관리할 수 있습니다.|
|후속 프롬프트 지침 텍스트|대화 흐름에서 후속 프롬프트를 사용할 경우 사용자가 후속 프롬프트에서 선택하기를 원하기 때문에 QnA 쌍에 대답이 필요하지 않을 수 있습니다. 이 경우 후속 프롬프트에 대한 각 예측과 함께 반환되는 기본 대답 텍스트를 설정하여 특정 텍스트를 설정합니다. 텍스트는 후속 프롬프트 선택에 대한 지침 텍스트로 표시됩니다. 이 기본 대답 텍스트의 예는 `Please select from the following choices`입니다. 이 문서의 다음 섹션에서 이 구성에 대해 설명합니다. [REST API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)를 사용하여 `defaultAnswerUsedForExtraction`의 기술 자료 정의의 일부로 설정할 수도 있습니다.|

### <a name="client-application-integration"></a>클라이언트 애플리케이션 통합

**Azure Bot Service** 와 같은 클라이언트 애플리케이션의 경우 다음과 같은 일반적인 시나리오에서 선택할 수 있습니다.

* 기술 자료의 설정 사용
* 클라이언트 애플리케이션에서 다른 텍스트를 사용하여 대답이 반환되었지만 점수 임계값을 충족하지 못하는 경우를 구별합니다. 이 텍스트는 코드에 저장된 정적 텍스트이거나 클라이언트 애플리케이션의 설정 목록에 저장될 수 있습니다.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>기술 자료를 만들 때 후속 프롬프트의 기본 대답 설정

기본 대답 텍스트는 새 기술 자료를 만들 때 지정하는 설정 중 하나입니다. 생성 프로세스 중에 기본 대답을 설정하지 않도록 선택한 경우 나중에 다음 절차를 수행하여 변경할 수 있습니다.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>QnA Maker 포털에서 후속 프롬프트의 기본 대답 변경

QnA Maker 서비스에서 대답이 반환되지 않으면 기술 자료 기본 대답이 반환됩니다.

1. [QnA Maker 포털](https://www.qnamaker.ai/)에 로그인하고 목록에서 기술 자료를 선택합니다.
1. 탐색 모음에서 **설정** 을 선택합니다.
1. **기술 자료 관리** 섹션에서 **기본 대답 텍스트** 의 값을 변경합니다.

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="기본 대답 텍스트 상자가 강조 표시된 QnA Maker 포털, 설정 페이지의 스크린샷":::

1. **저장 후 학습** 을 선택하여 변경 사항을 저장합니다.

## <a name="next-steps"></a>다음 단계

* [기술 자료 만들기](../How-to/manage-knowledge-bases.md)
