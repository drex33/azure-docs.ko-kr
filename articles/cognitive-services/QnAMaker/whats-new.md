---
title: QnA Maker 서비스의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 이 문서에는 QnA Maker에 대한 뉴스가 포함되어 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 07/16/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 959a6d5c5ed4b606c5a5850264422b6e460eb8f5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020448"
---
# <a name="whats-new-in-qna-maker"></a>QnA Maker의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목은 메모, 비디오, 블로그 게시물 및 기타 유형의 정보를 릴리스할 수 있습니다. 서비스를 최신 상태로 유지하려면 이 페이지에 책갈피를 설정합니다.

[!INCLUDE [Custom question answering](./includes/new-version.md)]

## <a name="release-notes"></a>릴리스 정보

QnA Maker의 새로운 기능에 대해 알아봅니다.

### <a name="may-2021"></a>2021년 5월

* QnA Maker 관리형이 [Text Analytics 리소스](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)의 사용자 지정 질문 응답 기능으로 다시 도입되었습니다.
* 사용자 지정 질문 답변은 비정형 문서를 지원합니다.
* [미리 빌드된 API](how-to/using-prebuilt-api.md)는 API를 통해 전달된 문서 텍스트에서 사용자 쿼리에 대한 답변을 생성하기 위해 도입되었습니다.

### <a name="november-2020"></a>2020년 11월

* QnA Maker의 새 버전이 무료 공개 미리 보기로 출시되었습니다. 자세한 내용은 [여기](https://techcommunity.microsoft.com/t5/azure-ai/introducing-qna-maker-managed-now-in-public-preview/ba-p/1845575)를 읽어보세요.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-QnA-managed-Now-in-Public-Preview/player]
* 단순화된 리소스 만들기
* 엔드투엔드 지역 지원
* 심층 학습 순위 모델
* 정확한 답변에 대한 기계 읽기 이해력
  
### <a name="july-2020"></a>2020년 7월

* [메타데이터: `OR` 여러 메타데이터 쌍의 논리적 조합](how-to/query-knowledge-base-with-metadata.md#logical-or-using-strictfilterscompoundoperationtype-property)
* Cognitive Search 엔드포인트를 프라이빗으로 구성하는 [단계](how-to/network-isolation.md)이지만 QnA Maker에는 액세스할 수 있습니다.
* [90일 동안 비활성 상태](how-to/set-up-qnamaker-service-azure.md#inactivity-policy-for-free-search-resources)일 경우 무료 Cognitive Search 리소스가 제거됩니다.

### <a name="june-2020"></a>2020년 6월

* 더 빠르고 쉬운 단계를 위해 [Power Virtual Agent](tutorials/integrate-with-power-virtual-assistant-fallback-topic.md) 자습서를 업데이트했습니다.

### <a name="may-2020"></a>2020년 5월

* [Azure RBAC(Azure 역할 기반 액세스 제어)](concepts/role-based-access-control.md)
* 답변에 대한 [서식 있는 텍스트 편집](how-to/edit-knowledge-base.md#rich-text-editing-for-answer)

### <a name="march-2020"></a>2020년 3월

* 이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

### <a name="february-2020"></a>2020년 2월

* GenerateAnswer API가 포함된 [NPM 패키지](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)

### <a name="november-2019"></a>2019년 11월

* QnA Maker에 대한 [미국 정부 클라우드 지원](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)
* GA의 [다중 턴](./how-to/multiturn-conversation.md) 기능
* 계층 1 언어로 제공되는 [Chit-chat 지원](./how-to/chit-chat-knowledge-base.md#language-support)

### <a name="october-2019"></a>2019년 10월

* QnA Maker 서비스의 모든 기술 자료에 대해 명시적으로 언어를 설정합니다.

### <a name="september-2019"></a>2019년 9월

* XLS 파일 형식을 사용하여 가져오기 및 내보내기

### <a name="june-2019"></a>2019년 6월

* 프랑스어, 이탈리아어, 독일어, 스페인어, 포르투갈어용 [순위지정(ranker) 모델](concepts/query-knowledge-base.md#ranker-process)이 향상됨

### <a name="april-2019"></a>2019년 4월

* 웹 사이트 콘텐츠 추출 지원
* 인증된 액세스에서 [SharePoint 문서](how-to/add-sharepoint-datasources.md) 지원

### <a name="march-2019"></a>2019년 3월

* [활성 학습](how-to/improve-knowledge-base.md)은 실제 사용자 질문에 따라 새 질문에 대한 제안을 제공합니다.
* 영어에 대한 향상된 NLP(자연어 처리) [우선순위(ranker)](concepts/query-knowledge-base.md#ranker-process) 모델

> [!div class="nextstepaction"]
> [QnA Maker 서비스 만들기](how-to/set-up-qnamaker-service-azure.md)

## <a name="cognitive-service-updates"></a>Cognitive Service 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)
