---
title: 기본 답변 가져오기-사용자 지정 질문 응답
description: 질문과 일치하는 항목이 없는 경우 기본 대답이 반환됩니다. 사용자 지정 질문 응답의 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
author: mrbullwinkle
ms:author: mbullwin
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 44f2ba3d3a1ae3e05cace797349a2680584a9aff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476569"
---
# <a name="change-default-answer-for-question-answering"></a>질문 응답의 기본 대답 변경

프로젝트에 대 한 기본 대답은 답변이 없는 경우 반환 됩니다. [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna)와 같은 클라이언트 애플리케이션을 사용하는 경우 점수 임계값을 충족하는 대답이 없음을 나타내는 별도의 기본 대답이 있을 수도 있습니다.

## <a name="default-answer"></a>기본 답변


|기본 답변|대답에 대한 설명|
|--|--|
|대답을 결정할 수 없는 경우의 KB 대답|`No good match found in KB.` -질문 응답 API에서 질문에 대 한 일치 하는 대답을 찾지 못하면 기본 텍스트 응답이 표시 됩니다. 질문에 대 한 답변에서 프로젝트의 **설정** 에이 텍스트를 설정할 수 있습니다. |

### <a name="client-application-integration"></a>클라이언트 애플리케이션 통합

**Azure Bot Service** 와 같은 클라이언트 애플리케이션의 경우 다음과 같은 일반적인 시나리오에서 선택할 수 있습니다.

* 프로젝트/기술 자료의 설정 사용
* 클라이언트 애플리케이션에서 다른 텍스트를 사용하여 대답이 반환되었지만 점수 임계값을 충족하지 못하는 경우를 구별합니다. 이 텍스트는 코드에 저장된 정적 텍스트이거나 클라이언트 애플리케이션의 설정 목록에 저장될 수 있습니다.

## <a name="change-default-answer-in-language-studio"></a>언어 스튜디오에서 기본 대답 변경

질문 응답에서 응답이 반환 되지 않으면 기술 자료 기본 응답이 반환 됩니다.

1. [Language Studio](https://language.azure.com) 에 로그인 하 고 목록에서 프로젝트를 선택 합니다.
1. 왼쪽 탐색 모음에서 **설정** 를 선택 합니다.
1. **응답을 찾을 수 없을 때 기본 응답** 값을 변경 > **저장** 을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![기본 대답 주위에 빨간색 상자가 있는 프로젝트 설정의 스크린샷](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>다음 단계

* [기술 자료 만들기](manage-knowledge-base.md)
