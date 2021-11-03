---
title: 기본 답변 가져오기-사용자 지정 질문 응답
description: 질문과 일치하는 항목이 없는 경우 기본 대답이 반환됩니다. 사용자 지정 질문 응답의 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 94925a2a6cadc80a576470fc1d0a585bd4bb4a01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103038"
---
# <a name="change-default-answer-for-question-answering"></a>질문 응답의 기본 대답 변경

프로젝트에 대 한 기본 대답은 답변이 없는 경우 반환 됩니다. [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna)와 같은 클라이언트 애플리케이션을 사용하는 경우 점수 임계값을 충족하는 대답이 없음을 나타내는 별도의 기본 대답이 있을 수도 있습니다.

## <a name="types-of-default-answer"></a>기본 대답 유형

기술 자료에는 두 가지 유형의 기본 대답이 있습니다. 예측 쿼리에서 각 대답이 반환되는 방법과 시기를 이해하는 것이 중요합니다.

|기본 대답 유형|대답에 대한 설명|
|--|--|
|대답을 결정할 수 없는 경우의 KB 대답|`No good match found in KB.` -질문 응답 API에서 질문에 대 한 일치 하는 대답을 찾지 못하면 기본 텍스트 응답이 표시 됩니다. 사용자 지정 질문 응답에서 프로젝트의 **설정** 에이 텍스트를 설정할 수 있습니다. |
|후속 프롬프트 지침 텍스트|대화 흐름에서 후속 프롬프트를 사용할 경우 사용자가 후속 프롬프트에서 선택하기를 원하기 때문에 QnA 쌍에 대답이 필요하지 않을 수 있습니다. 이 경우 후속 프롬프트에 대한 각 예측과 함께 반환되는 기본 대답 텍스트를 설정하여 특정 텍스트를 설정합니다. 텍스트는 후속 프롬프트 선택에 대한 지침 텍스트로 표시됩니다. 이 기본 대답 텍스트의 예는 `Please select from the following choices`입니다. 이 문서의 다음 섹션에서 이 구성에 대해 설명합니다. REST API 사용 하 여이를 기술 자료 정의의 일부로 설정할 수도 있습니다 `defaultAnswerUsedForExtraction` .|

### <a name="client-application-integration"></a>클라이언트 애플리케이션 통합

**Azure Bot Service** 와 같은 클라이언트 애플리케이션의 경우 다음과 같은 일반적인 시나리오에서 선택할 수 있습니다.

* 프로젝트/기술 자료의 설정 사용
* 클라이언트 애플리케이션에서 다른 텍스트를 사용하여 대답이 반환되었지만 점수 임계값을 충족하지 못하는 경우를 구별합니다. 이 텍스트는 코드에 저장된 정적 텍스트이거나 클라이언트 애플리케이션의 설정 목록에 저장될 수 있습니다.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>기술 자료를 만들 때 후속 프롬프트의 기본 대답 설정

기본 대답 텍스트는 새 기술 자료를 만들 때 지정하는 설정 중 하나입니다. 생성 프로세스 중에 기본 대답을 설정하지 않도록 선택한 경우 나중에 다음 절차를 수행하여 변경할 수 있습니다.

## <a name="change-follow-up-prompts-default-answer-in-language-studio"></a>언어 스튜디오에서 추가 작업 프롬프트의 기본 대답 변경

질문 응답에서 응답이 반환 되지 않으면 기술 자료 기본 응답이 반환 됩니다.

1. [Language Studio](https://language.azure.com) 에 로그인 하 고 목록에서 프로젝트를 선택 합니다.
1. 왼쪽 탐색 모음에서 **설정** 를 선택 합니다.
1. **응답을 찾을 수 없을 때 기본 응답** 값을 변경 > **저장** 을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![기본 대답 주위에 빨간색 상자가 있는 프로젝트 설정의 스크린샷](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>다음 단계

* [기술 자료 만들기](manage-knowledge-base.md)
