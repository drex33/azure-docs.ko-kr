---
title: 앱 계획 - 질문 답변
description: 질문 응답 앱을 계획하는 방법을 알아봅니다. 질문 답변이 작동하며 다른 Azure 서비스 및 일부 기술 자료 개념과 상호 작용하는 방식을 이해합니다.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 0dbd8e670f652ff598c9b65c07687ab38a097050
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482905"
---
# <a name="plan-your-question-answering-app"></a>질문 응답 앱 계획

질문 답변 앱을 계획하려면 질문 답변이 작동하고 다른 Azure 서비스와 상호 작용하는 방식을 이해해야 합니다. 또한 기술 자료 개념에 대한 확실한 이해가 필요합니다.

## <a name="azure-resources"></a>Azure 리소스

질문 답변을 통해 만든 각 [Azure 리소스에는](azure-resources.md#resource-purposes) 특정 목적이 있습니다. 각 리소스에는 고유한 용도, 제한 및 [가격 책정 계층](azure-resources.md#pricing-tier-considerations)이 있습니다. 지식을 계획 프로세스에 사용할 수 있도록 이러한 리소스의 기능을 이해하는 것이 중요합니다.

| 리소스 | 목적 |
|--|--|
| [언어 리소스 리소스](azure-resources.md) | 작성, 쿼리 예측 엔드포인트 및 원격 분석|
| [Cognitive Search](azure-resources.md#azure-cognitive-search-resource) 리소스 | 데이터 스토리지 및 검색 |

### <a name="resource-planning"></a>리소스 계획

응답 처리량에 대한 질문은 현재 관리 API 및 예측 API 모두에 대해 초당 10개의 트랜잭션으로 제한됩니다. 서비스에 대해 초당 10개 트랜잭션을 대상으로 하려면 Azure Cognitive Search의 S1(1개 인스턴스) SKU를 선택하는 것이 좋습니다.

### <a name="language-resource"></a>언어 리소스

사용자 지정 질문 답변 기능이 활성화된 단일 언어 리소스는 두 개 이상의 프로젝트/기술 자료를 호스팅할 수 있습니다. 프로젝트/기술 자료 수는 Cognitive Search 가격 책정 계층의 지원되는 인덱스 수량에 따라 결정됩니다. [인덱스와 기술 자료의 관계](azure-resources.md#index-usage)에 대해 자세히 알아보세요.

### <a name="knowledge-base-size-and-throughput"></a>기술 자료 크기 및 처리량

실제 앱을 빌드할 때 기술 자료의 크기와 예상되는 쿼리 예측 요청에 대해 충분한 리소스를 계획합니다.

기술 자료 크기는 다음에 의해 제어됩니다.
* [Cognitive Search 리소스](../../../../search/search-limits-quotas-capacity.md) 가격 책정 계층 제한
* [질문 답변 제한](./limits.md)

기술 자료 쿼리 예측 요청은 웹앱 계획과 웹앱에 의해 제어됩니다. 가격 책정 계층을 계획하려면 [권장 설정](azure-resources.md#recommended-settings)을 참조하세요.

### <a name="understand-the-impact-of-resource-selection"></a>리소스 선택의 영향 이해

적절한 리소스 선택은 기술 자료가 쿼리 예측에 성공적으로 답함을 의미합니다.

기술 자료가 제대로 작동하지 않는 경우 일반적으로 부적절한 리소스 관리의 문제입니다.

리소스를 잘못 선택하면 어떤 [리소스를 변경해야](azure-resources.md#pricing-tier-considerations) 하는지 결정하기 위한 조사가 필요합니다.

## <a name="project"></a>Project

프로젝트/기술 자료는 해당 언어 리소스와 직접 연결됩니다. 여기에는 쿼리 예측 요청에 답하는 데 사용되는 QnA(질문 및 대답) 쌍이 포함됩니다.

### <a name="language-considerations"></a>언어 관련 고려 사항

이제 사용자 지정 질문 답변 기능을 사용하는 동일한 언어 리소스 내에서 다른 언어로 프로젝트를 사용할 수 있습니다. 첫 번째 프로젝트를 만들 때 모든 후속 프로젝트에 적용되는 단일 언어로 프로젝트/기술 자료에 리소스를 사용할지 아니면 프로젝트를 만들 때마다 언어를 선택할지 선택할 수 있습니다.

### <a name="ingest-data-sources"></a>데이터 원본 수집

질문 답변은 구조화되지 않은 콘텐츠도 지원합니다. 비정형 콘텐츠가 있는 파일을 업로드할 수 있습니다.

현재는 비정형 콘텐츠에 대한 URL을 지원하지 않습니다.

수집 프로세스는 지원되는 콘텐츠 형식을 markdown으로 변환합니다. *대답* 의 모든 추가 편집은 markdown으로 수행됩니다. 기술 자료가 만들어지면 서식 있는 텍스트 작성을 사용하여 Language Studio 포털에서 QnA 쌍을 편집할 수 있습니다.

### <a name="data-format-considerations"></a>데이터 형식 고려 사항

QnA 쌍의 최종 형식이 markdown이므로 markdown 지원을 이해하는 것이 중요합니다.

### <a name="bot-personality"></a>봇 개성

잡담을 사용하여 프로젝트/기술 자료 에 [봇 개성을](../how-to/chit-chat.md)추가합니다. 이 개성은 *전문*, *친숙* 과 같은 특정 대화 톤의 답을 통해 제공됩니다. 이 잡담은 대화 세트로 제공되며 추가, 편집 및 제거할 수 있는 전체 제어 권한이 있습니다.

봇이 기술 자료에 연결하는 경우 봇 개성이 권장됩니다. 다른 서비스에도 연결하더라도 기술 자료에서 잡담을 사용하도록 선택할 수 있지만 봇 서비스가 상호 작용하는 방식을 검토하여 이것이 용도에 적합한 아키텍처 설계인지 확인해야 합니다.

### <a name="conversation-flow-with-a-project"></a>프로젝트를 통해 대화 흐름

일반적으로 대화 흐름은 `Hi` 또는 `Hello`와 같은 사용자의 인사말로 시작합니다. 기술 자료는 `Hi, how can I help you`와 같은 일반적인 대답으로 응답할 수 있으며 대화를 계속하기 위한 후속 프롬프트를 선택을 제공할 수도 있습니다.

사용자가 봇 사용 방법을 파악하고 대화 중 봇에 의해 중단되지 않도록 루프를 염두에 두고 대화 흐름을 디자인해야 합니다. [후속 프롬프트](../tutorials/guided-conversations.md)는 QnA 쌍 간의 연결을 제공하여 대화 흐름을 허용합니다.

### <a name="authoring-with-collaborators"></a>협력자와 함께 작성

협력자는 기술 자료 애플리케이션의 전체 개발 스택을 공유하는 다른 개발자이거나 기술 자료 작성으로만 제한될 수 있습니다.

기술 자료 작성은 협력자의 기능 범위를 제한하기 위해 Azure Portal에서 적용하는 여러 역할 기반 액세스 권한을 지원합니다.

## <a name="integration-with-client-applications"></a>클라이언트 애플리케이션과 통합

클라이언트 애플리케이션과의 통합은 예측 런타임 엔드포인트에 쿼리를 보내서 수행됩니다. 쿼리는 SDK 또는 REST 기반 요청을 사용하여 특정 프로젝트/기술 자료로 웹앱 엔드포인트에 응답하는 질문으로 전송됩니다.

클라이언트 요청을 제대로 인증 하려면 클라이언트 애플리케이션에서 올바른 자격 증명과 기술 자료 ID를 보내야 합니다. Azure Bot Service를 사용하는 경우 Azure Portal에서 봇 구성의 일부로 이러한 설정을 구성합니다.

### <a name="conversation-flow-in-a-client-application"></a>클라이언트 애플리케이션의 대화 흐름

Azure 봇과 같은 클라이언트 애플리케이션의 대화 흐름에는 기술 자료와 상호 작용하기 전후에 기능이 필요할 수 있습니다.

클라이언트 애플리케이션이 후속 프롬프트를 처리하는 대체 수단을 제공하거나 잡담을 포함하여 대화 흐름을 지원하나요? 그렇다면 이들을 초기에 디자인하고 클라이언트 애플리케이션 쿼리가 기술 자료에 보내질 때 다른 서비스에 의해 제대로 처리되는지 확인합니다.

### <a name="active-learning-from-a-client-application"></a>클라이언트 애플리케이션에서의 활성 학습

질문 답변은 _활성 학습을_ 사용하여 답변에 대한 대체 질문을 제안하여 기술 자료의 향상을 위해 사용됩니다. 클라이언트 애플리케이션이 이 [활성 학습](../tutorials/active-learning.md)의 일부를 담당합니다. 대화형 프롬프트를 통해 클라이언트 애플리케이션은 기술 자료가 사용자에게 유용하지 않은 대답을 반환했는지 확인할 수 있으며 더 나은 대답을 결정할 수 있습니다. 클라이언트 애플리케이션은 예측 품질 개선을 위해 해당 정보를 기술 자료로 다시 보내야 합니다.

### <a name="providing-a-default-answer"></a>기본 대답 제공

기술 자료가 대답을 찾지 못하면 _기본 대답_ 을 반환합니다. 이 답변은 **설정** 페이지에서 구성할 수 있습니다.)

이 기본 대답은 Azure Bot 기본 대답과 다릅니다. 구성 설정의 일부로 Azure Portal에서 Azure Bot의 기본 대답을 구성합니다. 점수 임계값이 충족되지 않는 경우 기본 대답이 반환됩니다.

## <a name="prediction"></a>예측

예측은 기술 자료의 응답이며, 단순한 대답 이상의 정보를 포함합니다. 쿼리 예측 응답을 얻으려면 질문 응답 API를 사용합니다.

### <a name="prediction-score-fluctuations"></a>예측 점수 변동

점수는 다음과 같은 여러 가지 요인에 따라 변경될 수 있습니다.

* 속성에 대한 응답으로 요청한 응답 수 `top`
* 사용 가능한 다양한 대체 질문
* 메타데이터 필터링
* `test`또는 `production` 프로젝트/기술 자료로 전송된 쿼리입니다.

### <a name="analytics-with-azure-monitor"></a>Azure Monitor 사용하여 분석

질문에 대한 답변에서 원격 분석은 [Azure Monitor 서비스를](../../../../azure-monitor/index.yml)통해 제공됩니다. [가장 많이](../how-to/analytics.md) 사용되는 쿼리를 사용하여 메트릭을 파악합니다.

## <a name="development-lifecycle"></a>개발 수명 주기

기술 자료의 개발 수명 주기는 기술 자료 편집, 테스트 및 게시를 거쳐 계속 진행됩니다.

### <a name="knowledge-base-development-of-question-answer-pairs"></a>질문 답변 쌍의 기술 자료 개발

QnA 쌍은 클라이언트 애플리케이션 사용에 따라 설계하고 개발해야 합니다.

각 쌍에는 다음이 포함될 수 있습니다.
* 메타데이터 - 쿼리할 때 필터링하여 데이터의 원본, 콘텐츠, 형식 및 목적에 대한 추가 정보로 QnA 쌍에 태그를 지정할 수 있습니다.
* 후속 프롬프트 - 사용자가 정답에 도달할 수 있도록 기술 자료를 통해 경로를 결정하는 데 도움이 됩니다.
* 대체 질문 - 검색 시 다양한 형태의 질문에 대한 대답과 일치하도록 허용하는 것이 중요합니다. [활성 학습 제안](../tutorials/active-learning.md)은 대체 질문으로 전환됩니다.

### <a name="devops-development"></a>DevOps 개발

DevOps 파이프라인에 삽입할 기술 자료를 개발하려면 일괄 처리 테스트 중 기술 자료가 격리되어야 합니다.

기술 자료는 Cognitive Search 인덱스를 언어 리소스에 있는 다른 모든 기술 자료와 공유 합니다. 기술 자료가 파티션에 의해 격리되는 동안 인덱스를 공유하면 게시된 기술 자료와 비교할 때 점수가 다를 수 있습니다.

및 기술 자료에 대해 _동일한 점수_ 를 갖도록 `test` `production` 언어 리소스를 단일 기술 자료로 격리 합니다. 이 아키텍처에서 리소스는 격리된 일괄 처리 테스트만큼만 유지되어야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스](./azure-resources.md)
