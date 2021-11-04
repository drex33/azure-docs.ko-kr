---
title: 앱 계획 - QnA Maker
description: QnA Maker 앱을 계획하는 방법에 대해 알아봅니다. QnA Maker가 작동하고 다른 Azure 서비스와 상호 작용하는 방법과 몇 가지 기술 자료 개념을 이해합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b5810d7d69322793afea76bfbe29fb49b5dc13a3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086776"
---
# <a name="plan-your-qna-maker-app"></a>QnA Maker 앱 계획

QnA Maker 앱을 계획하려면 QnA Maker가 작동하고 다른 Azure 서비스와 상호 작용하는 방식을 이해해야 합니다. 또한 기술 자료 개념에 대한 확실한 이해가 필요합니다.

## <a name="azure-resources"></a>Azure 리소스

QnA Maker를 사용하여 만든 각 [Azure 리소스](azure-resources.md#resource-purposes)에는 특정 용도가 있습니다. 각 리소스에는 고유한 용도, 제한 및 [가격 책정 계층](azure-resources.md#pricing-tier-considerations)이 있습니다. 지식을 계획 프로세스에 사용할 수 있도록 이러한 리소스의 기능을 이해하는 것이 중요합니다.

| 리소스 | 목적 |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) 리소스 | 작성 및 쿼리 예측 |
| [Cognitive Search](azure-resources.md#cognitive-search-resource) 리소스 | 데이터 스토리지 및 검색 |
| [App Service 리소스 및 앱 계획 서비스](azure-resources.md#app-service-and-app-service-plan) 리소스 | 예측 엔드포인트 쿼리 |
| [Application Insights](azure-resources.md#application-insights) 리소스 | 쿼리 예측 원격 분석 |

### <a name="resource-planning"></a>리소스 계획

각 리소스의 무료 계층인 `F0`이 작동하며 작성 및 쿼리 예측 경험을 모두 제공할 수 있습니다. 이 계층을 사용하여 작성 및 쿼리 예측을 학습할 수 있습니다. 프로덕션 또는 라이브 시나리오로 이동할 때 리소스 선택을 재평가합니다.

### <a name="knowledge-base-size-and-throughput"></a>기술 자료 크기 및 처리량

실제 앱을 빌드할 때 기술 자료의 크기와 예상되는 쿼리 예측 요청에 대해 충분한 리소스를 계획합니다.

기술 자료 크기는 다음에 의해 제어됩니다.
* [Cognitive Search 리소스](../../../search/search-limits-quotas-capacity.md) 가격 책정 계층 제한
* [QnA Maker 제한](../limits.md)

기술 자료 쿼리 예측 요청은 웹앱 계획과 웹앱에 의해 제어됩니다. 가격 책정 계층을 계획하려면 [권장 설정](azure-resources.md#recommended-settings)을 참조하세요.

### <a name="resource-sharing"></a>리소스 공유

이러한 리소스 중 일부를 이미 사용 중인 경우 리소스 공유를 고려할 수 있습니다. 리소스 공유가 고급 시나리오임을 이해하고 [공유 가능](azure-resources.md#share-services-with-qna-maker)한 리소스를 확인합니다.

동일한 QnA Maker 리소스에서 생성된 모든 기술 자료가 동일한 **테스트** 쿼리 예측 엔드포인트를 공유합니다.

### <a name="understand-the-impact-of-resource-selection"></a>리소스 선택의 영향 이해

적절한 리소스 선택은 기술 자료가 쿼리 예측에 성공적으로 답함을 의미합니다.

기술 자료가 제대로 작동하지 않는 경우 일반적으로 부적절한 리소스 관리의 문제입니다.

리소스를 잘못 선택하면 어떤 [리소스를 변경해야](azure-resources.md#when-to-change-a-pricing-tier) 하는지 결정하기 위한 조사가 필요합니다.

## <a name="knowledge-bases"></a>기술 자료

기술 자료는 해당 QnA Maker 리소스에 직접 연결됩니다. 여기에는 쿼리 예측 요청에 답하는 데 사용되는 QnA(질문 및 대답) 쌍이 포함됩니다.

### <a name="language-considerations"></a>언어 관련 고려 사항

QnA Maker 리소스에서 생성된 첫 번째 기술 자료는 리소스의 언어를 설정합니다. QnA Maker 리소스에 대해 하나의 언어만 설정할 수 있습니다.

언어별로 QnA Maker 리소스를 구조화하거나, 쿼리 예측 엔드포인트에 보내기 전에 [번역기](../../translator/translator-overview.md)를 사용하여 다른 언어의 쿼리를 기술 자료의 언어로 변경할 수 있습니다.

### <a name="ingest-data-sources"></a>데이터 원본 수집

수집된 다음 [데이터 원본](../Concepts/data-sources-and-content.md) 중 하나를 사용하여 기술 자료를 만들 수 있습니다.

* 공용 URL
* 비공개 SharePoint URL
* 파일

수집 프로세스는 [지원되는 콘텐츠 형식](../reference-document-format-guidelines.md)을 markdown으로 변환합니다. *대답* 의 모든 추가 편집은 markdown으로 수행됩니다. 기술 자료를 만든 후 [서식 있는 텍스트 작성](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer) 기능을 사용하여 QnA Maker 포털에서 [QnA 쌍](question-answer-set.md)을 편집할 수 있습니다.

### <a name="data-format-considerations"></a>데이터 형식 고려 사항

QnA 쌍의 최종 형식이 markdown이므로 [markdown 지원](../reference-markdown-format.md)을 이해하는 것이 중요합니다.

연결된 이미지는 QnA Maker 포털 또는 클라이언트 애플리케이션의 테스트 창에 표시되는 공용 URL에서 사용할 수 있어야 합니다. QnA Maker는 이미지를 포함하여 콘텐츠에 대한 인증을 제공하지 않습니다.

### <a name="bot-personality"></a>봇 개성

[잡담](../how-to/chit-chat-knowledge-base.md)으로 기술 자료에 봇 개성을 추가합니다. 이 개성은 *전문*, *친숙* 과 같은 특정 대화 톤의 답을 통해 제공됩니다. 이 잡담은 대화 세트로 제공되며 추가, 편집 및 제거할 수 있는 전체 제어 권한이 있습니다.

봇이 기술 자료에 연결하는 경우 봇 개성이 권장됩니다. 다른 서비스에도 연결하더라도 기술 자료에서 잡담을 사용하도록 선택할 수 있지만 봇 서비스가 상호 작용하는 방식을 검토하여 이것이 용도에 적합한 아키텍처 설계인지 확인해야 합니다.

### <a name="conversation-flow-with-a-knowledge-base"></a>기술 자료를 통한 대화 흐름

일반적으로 대화 흐름은 `Hi` 또는 `Hello`와 같은 사용자의 인사말로 시작합니다. 기술 자료는 `Hi, how can I help you`와 같은 일반적인 대답으로 응답할 수 있으며 대화를 계속하기 위한 후속 프롬프트를 선택을 제공할 수도 있습니다.

사용자가 봇 사용 방법을 파악하고 대화 중 봇에 의해 중단되지 않도록 루프를 염두에 두고 대화 흐름을 디자인해야 합니다. [후속 프롬프트](../how-to/multiturn-conversation.md)는 QnA 쌍 간의 연결을 제공하여 대화 흐름을 허용합니다.

### <a name="authoring-with-collaborators"></a>협력자와 함께 작성

협력자는 기술 자료 애플리케이션의 전체 개발 스택을 공유하는 다른 개발자이거나 기술 자료 작성으로만 제한될 수 있습니다.

기술 자료 작성은 협력자의 기능 범위를 제한하기 위해 Azure Portal에서 적용하는 여러 역할 기반 액세스 권한을 지원합니다.

## <a name="integration-with-client-applications"></a>클라이언트 애플리케이션과 통합

클라이언트 애플리케이션과의 통합은 예측 런타임 엔드포인트에 쿼리를 보내서 수행됩니다. 쿼리는 QnA Maker의 웹앱 엔드포인트에 대한 SDK 또는 REST 기반 요청과 함께 특정 기술 자료에 보내집니다.

클라이언트 요청을 제대로 인증 하려면 클라이언트 애플리케이션에서 올바른 자격 증명과 기술 자료 ID를 보내야 합니다. Azure Bot Service를 사용하는 경우 Azure Portal에서 봇 구성의 일부로 이러한 설정을 구성합니다.

### <a name="conversation-flow-in-a-client-application"></a>클라이언트 애플리케이션의 대화 흐름

Azure 봇과 같은 클라이언트 애플리케이션의 대화 흐름에는 기술 자료와 상호 작용하기 전후에 기능이 필요할 수 있습니다.

클라이언트 애플리케이션이 후속 프롬프트를 처리하는 대체 수단을 제공하거나 잡담을 포함하여 대화 흐름을 지원하나요? 그렇다면 이들을 초기에 디자인하고 클라이언트 애플리케이션 쿼리가 기술 자료에 보내질 때 다른 서비스에 의해 제대로 처리되는지 확인합니다.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>QnA Maker와 LUIS(Language Understanding) 간 디스패치

클라이언트 애플리케이션은 여러 기능을 제공할 수 있으며, 이 중 하나만 기술 자료에서 대답합니다. 다른 기능은 여전히 대화 텍스트를 이해하고 의미를 추출해야 합니다.

일반적인 클라이언트 애플리케이션 아키텍처는 QnA Maker와 [LUIS(Language Understanding)](../../LUIS/what-is-luis.md)를 함께 사용하는 것입니다. LUIS는 다른 서비스를 포함하여 모든 쿼리에 대한 텍스트 분류 및 추출을 제공합니다. QnA Maker는 기술 자료에서 대답을 제공합니다.

이러한 [공유 아키텍처](../choose-natural-language-processing-service.md) 시나리오에서 두 서비스 간의 디스패치는 Bot Framework의 [디스패치](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) 도구를 통해 수행됩니다.

### <a name="active-learning-from-a-client-application"></a>클라이언트 애플리케이션에서의 활성 학습

QnA Maker는 _활성 학습_ 을 사용하여 대답에 대한 대체 질문을 제안함으로써 기술 자료를 개선합니다. 클라이언트 애플리케이션이 이 [활성 학습](../How-To/use-active-learning.md)의 일부를 담당합니다. 대화형 프롬프트를 통해 클라이언트 애플리케이션은 기술 자료가 사용자에게 유용하지 않은 대답을 반환했는지 확인할 수 있으며 더 나은 대답을 결정할 수 있습니다. 클라이언트 애플리케이션은 예측 품질 개선을 위해 해당 정보를 기술 자료로 다시 보내야 합니다.

### <a name="providing-a-default-answer"></a>기본 대답 제공

기술 자료가 대답을 찾지 못하면 _기본 대답_ 을 반환합니다. 이 대답은 QnA Maker 포털의 **설정** 페이지나 [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)에서 구성할 수 있습니다.

이 기본 대답은 Azure Bot 기본 대답과 다릅니다. 구성 설정의 일부로 Azure Portal에서 Azure Bot의 기본 대답을 구성합니다. 점수 임계값이 충족되지 않는 경우 기본 대답이 반환됩니다.

## <a name="prediction"></a>예측

예측은 기술 자료의 응답이며, 단순한 대답 이상의 정보를 포함합니다. 쿼리 예측 응답을 가져오려면 [GenerateAnswer API](query-knowledge-base.md)를 사용합니다.

### <a name="prediction-score-fluctuations"></a>예측 점수 변동

점수는 다음과 같은 여러 가지 요인에 따라 변경될 수 있습니다.

* `top` 속성과 함께 [GenerateAnswer](query-knowledge-base.md)에 대한 응답으로 요청한 대답 수
* 사용 가능한 다양한 대체 질문
* 메타데이터 필터링
* `test` 또는 `production` 기술 자료로 보내진 쿼리

[2단계 대답 순위](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)가 있습니다.
- Cognitive Search - 첫 번째 순위. Cognitive Search에서 가장 적합한 대답을 반환하고 QnA Maker 순위매기기에 전달할 만큼 충분히 많이 _허용되는 대답_ 수를 설정합니다.
- QnA Maker - 두 번째 순위. 기능화와 기계 학습을 적용하여 가장 적합한 대답을 결정합니다.

### <a name="service-updates"></a>서비스 업데이트

[최신 런타임 업데이트](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)를 적용하여 서비스 업데이트를 자동으로 관리합니다.

### <a name="scaling-throughput-and-resiliency"></a>크기 조정, 처리량 및 복원력

크기 조정, 처리량 및 복원력은 [Azure 리소스](../how-to/set-up-qnamaker-service-azure.md), 해당 가격 책정 계층 및 [Traffic Manager](../how-to/configure-QnA-Maker-resources.md#business-continuity-with-traffic-manager)와 같은 주변 아키텍처에 의해 결정됩니다.

### <a name="analytics-with-application-insights"></a>Application Insights로 분석

기술 자료에 대한 모든 쿼리는 Application Insights에 저장됩니다. [가장 많이](../how-to/get-analytics-knowledge-base.md) 사용되는 쿼리를 사용하여 메트릭을 파악합니다.

## <a name="development-lifecycle"></a>개발 수명 주기

기술 자료의 [개발 수명 주기](development-lifecycle-knowledge-base.md)는 기술 자료 편집, 테스트 및 게시를 거쳐 계속 진행됩니다.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 쌍의 기술 자료 개발

QnA 쌍은 클라이언트 애플리케이션 사용에 따라 설계하고 개발해야 합니다.

각 쌍에는 다음이 포함될 수 있습니다.
* 메타데이터 - 쿼리할 때 필터링하여 데이터의 원본, 콘텐츠, 형식 및 목적에 대한 추가 정보로 QnA 쌍에 태그를 지정할 수 있습니다.
* 후속 프롬프트 - 사용자가 정답에 도달할 수 있도록 기술 자료를 통해 경로를 결정하는 데 도움이 됩니다.
* 대체 질문 - 검색 시 다양한 형태의 질문에 대한 대답과 일치하도록 허용하는 것이 중요합니다. [활성 학습 제안](../How-To/use-active-learning.md)은 대체 질문으로 전환됩니다.

### <a name="devops-development"></a>DevOps 개발

DevOps 파이프라인에 삽입할 기술 자료를 개발하려면 일괄 처리 테스트 중 기술 자료가 격리되어야 합니다.

기술 자료는 Cognitive Search 인덱스를 QnA Maker 리소스의 다른 모든 기술 자료와 공유합니다. 기술 자료가 파티션에 의해 격리되는 동안 인덱스를 공유하면 게시된 기술 자료와 비교할 때 점수가 다를 수 있습니다.

`test` 및 `production` 기술 자료에서 _동일한 점수_ 를 얻으려면 QnA Maker 리소스를 단일 기술 자료로 격리합니다. 이 아키텍처에서 리소스는 격리된 일괄 처리 테스트만큼만 유지되어야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스](../how-to/set-up-qnamaker-service-azure.md)
* [질문 및 대답 쌍](question-answer-set.md)
