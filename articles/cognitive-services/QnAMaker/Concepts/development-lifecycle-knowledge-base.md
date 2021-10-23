---
title: 기술 자료 수명 주기 - QnA Maker
description: QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 6a41a2a522d082fa04c60cd58d095ab9b20401f9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229921"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker의 기술 자료 수명 주기
QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다.

![제작 주기](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기
QnA Maker KB(기술 자료) 엔드포인트는 KB의 콘텐츠를 기반으로 사용자 쿼리와 가장 일치하는 답변을 제공합니다. 기술 자료 만들기는 질문, 대답, 관련 메타데이터의 콘텐츠 리포지토리를 설정하는 일회성 작업입니다. KB는 다음과 같은 기존 콘텐츠를 크롤링하여 만들 수 있습니다.

- FAQ 페이지
- 제품 설명서
- Q-A 쌍

[기술 자료 만들기](../quickstarts/create-publish-knowledge-base.md)에 대해 알아보세요.

## <a name="testing-and-updating-the-knowledge-base"></a>기술 자료 테스트 및 업데이트

편집 또는 자동 추출을 통해 기술 자료에 콘텐츠가 채워지면 기술 자료를 테스트할 수 있습니다. 대화형 테스트는 **테스트** 패널을 통해 QnA Maker 포털에서 수행할 수 있습니다. 일반적인 사용자 쿼리를 입력합니다. 그런 다음 올바른 대답과 충분한 신뢰도 점수를 모두 사용하여 대답이 반환되었는지 확인합니다.


* **낮은 신뢰도 점수를 수정하려면**: 다른 질문을 추가합니다.
* **쿼리가 [기본 대답](../How-to/change-default-answer.md)을 잘못 반환하는 경우**: 올바른 질문에 새 대답을 추가합니다.

이  테스트-업데이트 반복 과정은 사용자가 결과에 만족할 때까지 계속됩니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

대용량 KB의 경우 게시된 기술 자료 대신 `test` 기술 자료를 쿼리하는 [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) 및 `isTest` body 속성과 함께 자동 테스트를 사용합니다.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>기술 자료 게시
기술 자료 테스트를 마쳤으면 기술 자료를 게시할 수 있습니다. 게시는 테스트를 마친 최신 버전의 기술 자료를 **게시된** 기술 자료를 나타내는 전용 Azure Cognitive Search 인덱스에 푸시합니다. 또한 애플리케이션 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

게시 작업으로 인해 기술 자료의 테스트 버전이 추가로 변경되어도 게시된 버전에는 영향을 주지 않습니다. 게시된 버전은 프로덕션 애플리케이션에서 라이브 상태일 수 있습니다.

각각의 기술 자료를 테스트 대상으로 지정할 수 있습니다. API를 사용하면 generateAnswer 호출에서 `isTest` body 속성을 사용하여 기술 자료의 테스트 버전을 대상으로 지정할 수 있습니다.

[기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="monitor-usage"></a>사용량 모니터링
서비스의 채팅 로그를 기록하려면 [QnA Maker 서비스를 만들 때](../How-To/set-up-qnamaker-service-azure.md) Application Insights를 사용하도록 설정해야 합니다.

다양한 서비스 사용 분석 자료를 얻을 수 있습니다. Application Insights를 사용하여 [QnA Maker 서비스에 대한 분석](../How-To/get-analytics-knowledge-base.md) 자료를 얻는 방법을 자세히 알아보세요.

분석 자료의 내용에 따라 [기술 자료를 적절하게 업데이트](../How-To/edit-knowledge-base.md)하세요.

## <a name="version-control-for-data-in-your-knowledge-base"></a>기술 자료의 데이터에 대한 버전 제어

데이터에 대한 버전 제어는 QnA Maker 포털의 **설정** 페이지에 있는 가져오기/내보내기 기능을 통해 제공됩니다.

기술 자료를 `.tsv` 또는 `.xls` 형식으로 내보내서 기술 자료를 백업할 수 있습니다. 내보낸 후에는 이 파일을 일반 원본 제어 검사의 일부로 포함합니다.

특정 버전으로 돌아가야 할 경우 로컬 시스템에서 해당 파일을 가져와야 합니다. 내보낸 기술 자료는 **설정** 페이지에서 가져오기를 통해 **사용해야** 합니다. 파일 또는 URL 문서 데이터 원본으로 사용할 수는 없습니다. 이렇게 하면 기술 자료에 있는 질문과 대답이 가져온 파일의 내용으로 바뀝니다.

## <a name="test-and-production-knowledge-base"></a>테스트 및 프로덕션 기술 자료
기술 자료는 QnA Maker 통해 생성, 유지 관리 및 사용되는 질문과 대답 집합의 리포지토리입니다. 각 QnA Maker 리소스는 여러 기술 자료를 보유할 수 있습니다.

기술 자료에는 두 가지 상태(*테스트* 및 *게시됨*)가 있습니다.

### <a name="test-knowledge-base"></a>테스트 기술 자료

*테스트 기술 자료* 는 현재 편집 및 저장된 버전입니다. 테스트 버전은 대답의 정확성과 완전성을 위해 테스트되었습니다. 테스트 기술 자료의 변경 내용은 애플리케이션 또는 챗봇의 최종 사용자에게 영향을 주지 않습니다. 테스트 기술 자료를 HTTP 요청에서는 `test`라고 합니다. `test` 지식은 QnA Maker의 포털 대화형 **테스트** 창에서 사용할 수 있습니다.

### <a name="production-knowledge-base"></a>프로덕션 기술 자료

*게시된 기술 자료* 는 챗봇 또는 애플리케이션에서 사용되는 버전입니다. 기술 자료를 게시하면 테스트 버전의 콘텐츠가 게시된 버전에 추가됩니다. 게시된 기술 자료는 애플리케이션이 엔드포인트를 통해 사용하는 버전입니다. 콘텐츠가 올바르고 잘 테스트되었는지 확인합니다. 게시된 기술 자료를 HTTP 요청에서는 `prod`라고 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [활성 학습 제안](../index.yml)