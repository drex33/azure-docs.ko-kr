---
title: Project 수명 주기 - 질문 답변
description: 질문 답변은 모델 변경, 발화 예제, 배포 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 잘 알아봅니다.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 193d01adac85681c39554ec1f6cab24dce9082f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483128"
---
# <a name="question-answering-project-lifecycle"></a>프로젝트 수명 주기에 대한 질문 답변

질문 답변은 모델 변경, 발화 예제, 배포 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 잘 알아봅니다.

## <a name="creating-a-project"></a>프로젝트 만들기

질문 답변 프로젝트/기술 자료는 프로젝트 내용에 따라 사용자 쿼리에 가장 일치하는 답변을 제공합니다. 프로젝트 만들기는 질문, 답변 및 관련 메타데이터의 콘텐츠 리포지토리를 설정하는 일회성 작업입니다. 다음 소스와 같은 기존 콘텐츠를 크롤링하여 프로젝트를 만들 수 있습니다.

- FAQ 페이지
- 제품 설명서
- Q-A 쌍

[기술 자료 만들기](../how-to/create-test-deploy.md)에 대해 알아보세요.

## <a name="testing-and-updating-your-project"></a>프로젝트 테스트 및 업데이트

프로젝트가 편집 또는 자동 추출을 통해 콘텐츠로 채워지면 테스트할 준비가 된 것입니다. 대화형 테스트는 Language Studio 포털의 테스트 **패널을** 통해 사용자 지정 질문 답변 메뉴에서 수행할 수 있습니다. 일반적인 사용자 쿼리를 입력합니다. 그런 다음 올바른 대답과 충분한 신뢰도 점수를 모두 사용하여 대답이 반환되었는지 확인합니다.

* **낮은 신뢰도 점수를 수정하려면**: 다른 질문을 추가합니다.
* **쿼리가 [기본 대답](../How-to/change-default-answer.md)을 잘못 반환하는 경우**: 올바른 질문에 새 대답을 추가합니다.

이  테스트-업데이트 반복 과정은 사용자가 결과에 만족할 때까지 계속됩니다.

## <a name="deploy-your-project"></a>프로젝트 배포

프로젝트 테스트가 완료되면 프로덕션에 배포할 수 있습니다. 배포는 테스트된 프로젝트의 최신 버전을 **게시된** 프로젝트/기술 자료가 나타내는 전용 Azure Cognitive Search 인덱스로 푸시합니다. 또한 애플리케이션 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

배포 작업으로 인해 기술 자료의 테스트 버전을 추가로 변경하면 게시된 버전이 영향을 받지 않습니다. 게시된 버전은 프로덕션 애플리케이션에서 라이브로 사용할 수 있습니다.

이러한 각 프로젝트/기술 자료는 별도로 테스트 대상으로 지정할 수 있습니다.

## <a name="monitor-usage"></a>사용량 모니터링

서비스의 채팅 로그를 기록하고 추가 분석을 얻으려면 언어 리소스를 만든 후 [Azure Monitor 진단 로그를](../how-to/analytics.md) 사용하도록 설정해야 합니다.

분석에서 배운 내용에 따라 프로젝트를 적절하게 업데이트합니다.

## <a name="version-control-for-data-in-your-knowledge-base"></a>기술 자료의 데이터에 대한 버전 제어

데이터에 대한 버전 제어는 Language Studio 포털의 질문 답변 섹션에 있는 프로젝트 페이지의 가져오기/내보내기 기능을 통해 제공됩니다.

프로젝트를 또는 형식으로 내보내 프로젝트/기술 자료 백업할 수 `.tsv` `.xls` 있습니다. 내보낸 후에는 이 파일을 일반 원본 제어 검사의 일부로 포함합니다.

특정 버전으로 돌아가야 할 경우 로컬 시스템에서 해당 파일을 가져와야 합니다. 내보낸 는 프로젝트 페이지의 가져오기를 통해서만 사용해야 **합니다.** 파일 또는 URL 문서 데이터 원본으로 사용할 수는 없습니다. 이렇게 하면 기술 자료에 있는 질문과 대답이 가져온 파일의 내용으로 바뀝니다.

## <a name="test-and-production-project"></a>테스트 및 프로덕션 프로젝트

프로젝트/기술 자료는 질문 대답을 통해 생성, 유지 관리 및 사용되는 질문 및 답변 집합의 리포지토리입니다. 각 언어 리소스는 여러 프로젝트/기술 자료를 보유할 수 있습니다.

프로젝트/기술 자료는 *테스트* 및 *게시된* 의 두 가지 상태입니다.

### <a name="test-projectknowledge-base"></a>테스트 프로젝트/기술 자료

*테스트 기술 자료* 는 현재 편집 및 저장된 버전입니다. 테스트 버전은 대답의 정확성과 완전성을 위해 테스트되었습니다. 테스트 기술 자료의 변경 내용은 애플리케이션 또는 챗봇의 최종 사용자에게 영향을 주지 않습니다. 테스트 기술 자료를 HTTP 요청에서는 `test`라고 합니다. 이 `test` 지식은 Language Studio의 대화형 **테스트** 창에서 사용할 수 있습니다.

### <a name="production-projectknowledge-base"></a>프로덕션 프로젝트/기술 자료

*게시된 기술 자료* 는 챗봇 또는 애플리케이션에서 사용되는 버전입니다. 기술 자료를 게시하면 테스트 버전의 콘텐츠가 게시된 버전에 추가됩니다. 게시된 기술 자료는 애플리케이션이 엔드포인트를 통해 사용하는 버전입니다. 콘텐츠가 올바르고 잘 테스트되었는지 확인합니다. 게시된 기술 자료를 HTTP 요청에서는 `prod`라고 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [활성 학습 제안](../tutorials/active-learning.md)