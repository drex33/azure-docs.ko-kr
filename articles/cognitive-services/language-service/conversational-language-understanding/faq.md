---
title: 질문과 대답
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 대화형 Language Understanding에 대한 FAQ에 대한 답변을 빠르게 얻으세요.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e699beb4bae6c31c73e2e5eebc508306deeb262
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053567"
---
# <a name="frequently-asked-questions-for-conversational-language-understanding"></a>대화형 Language Understanding을 위한 질문과 대답

이 문서를 사용하여 대화형 Language Understanding에 대한 일반적인 질문에 대한 답변을 빠르게 얻으세요.

## <a name="how-do-i-create-a-project"></a>프로젝트를 만들려면 어떻게 해야 하나요?

첫 번째 프로젝트를 빠르게 만들려면 [빠른 시작](./quickstart.md)을 참조하고 자세한 내용은 [방법 문서](./how-to/create-project.md)를 참조하세요. 

## <a name="how-do-i-connect-other-service-applications-in-orchestration-workflow-projects"></a>오케스트레이션 워크플로 프로젝트에서 다른 서비스 애플리케이션을 어떻게 연결하나요?

다른 프로젝트를 의도로 연결하는 방법에 대한 정보는 [스키마 빌드 문서](./how-to/build-schema.md#build-project-schema-for-orchestration-workflow-projects)를 참조하세요.

## <a name="which-luis-applications-can-i-connect-to-in-orchestration-workflow-projects"></a>오케스트레이션 워크플로 프로젝트에서 어떤 LUIS 애플리케이션에 연결할 수 있나요?

언어 리소스를 작성 리소스로 사용하는 LUIS 애플리케이션을 연결할 수 있습니다. 동일한 리소스가 소유한 LUIS 애플리케이션에만 연결할 수 있습니다. 이 옵션은 LUIS와 CLU 간에 사용 가능한 유일한 공통 지역이므로 서유럽의 리소스에만 사용할 수 있습니다. 자세한 내용은 [지역 제한](./service-limits.md#region-limits)을 참조하세요. 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>학습 시간이 오래 걸리는데, 예상되는 건가요?

대화 프로젝트의 경우 긴 교육 시간이 예상됩니다. 예제의 수에 따라 학습 시간은 5분에서 1시간 이상까지 다양할 수 있습니다. 

## <a name="can-i-add-entities-to-orchestration-workflow-projects"></a>오케스트레이션 워크플로 프로젝트에 엔터티를 추가할 수 있나요?

아니요. 오케스트레이션 프로젝트는 라우팅을 위해 다른 프로젝트에 연결할 수 있는 의도에 대해서만 사용하도록 설정됩니다. 

## <a name="how-do-i-use-entity-components"></a>엔터티 구성 요소는 어떻게 사용하나요?

[엔터티 구성 요소](./concepts/entity-components.md) 문서를 참조하세요.

## <a name="which-languages-are-supported-in-this-feature"></a>이 기능은 어떤 언어를 지원하나요?

[언어 지원](./language-support.md) 문서를 참조하세요.

## <a name="how-do-i-get-more-accurate-results-for-my-project"></a>내 프로젝트에 대해 더 정확한 결과를 얻으려면 어떻게 해야 하나요?

정확도 향상에 대한 정보는 [권장 지침](./how-to/build-schema.md#guidelines-and-recommendations)을 참조하세요.

## <a name="how-do-i-get-predictions-in-different-languages"></a>다른 언어로 예측을 얻으려면 어떻게 해야 하나요?

어떤 언어로든 대화 프로젝트를 교육하고 배포할 때 즉시 [여러 언어](./concepts/multiple-languages.md)로 쿼리를 시도할 수 있습니다. 다른 언어에 대해 다양한 결과를 얻을 수 있습니다. 모든 언어의 정확도를 높이려면 해당 언어의 프로젝트에 발화를 추가하여 해당 언어의 더 많은 구문에 학습된 모델을 도입합니다.

## <a name="how-many-intents-entities-utterances-can-i-add-to-a-project"></a>프로젝트에 몇 개의 의도, 엔터티, 발화를 추가할 수 있나요?

[서비스 제한](./service-limits.md) 도움말을 참조하세요. 

## <a name="can-i-label-the-same-word-as-2-different-entities"></a>동일한 단어에 2개의 다른 엔터티에 레이블을 지정할 수 있나요?

LUIS와 달리 동일한 텍스트에 2개의 다른 엔터티로 레이블을 지정할 수 없습니다. 다른 엔터티의 학습된 구성 요소는 상호 배타적이며 각 문자 집합에 대해 하나의 학습된 범위만 예측됩니다.

## <a name="can-i-import-a-luis-json-file-into-conversational-language-understanding"></a>LUIS JSON 파일을 대화형 Language Understanding으로 가져올 수 있나요?

예, 서비스의 최신 버전에서 JSON 파일을 [가져오기](./concepts/backwards-compatibility.md)할 수 있습니다.

## <a name="can-i-import-a-luis-lu-file-into-conversational-language-understanding"></a>LUIS `.LU` 파일을 대화형 Language Understanding으로 가져올 수 있나요?

아니요, 서비스는 JSON 형식만 지원합니다. LUIS로 이동하여 `.LU` 파일을 가져와 JSON 파일로 내보낼 수 있습니다. 

## <a name="is-there-any-sdk-support"></a>SDK 지원이 있나요?

예, 예측에만 해당하며 [샘플을 사용할 수 있습니다](https://aka.ms/cluSampleCode). 현재 SDK에 대한 제작 지원은 없습니다.

## <a name="are-there-apis-for-this-feature"></a>이 기능에 대한 API가 있나요?

예, 모든 API를 [사용할 수 있습니다](https://aka.ms/clu-apis).

## <a name="next-steps"></a>다음 단계

[대화형 Language Understanding 개요](overview.md)
