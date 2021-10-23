---
title: 서비스 구성 - QnA Maker
description: 리소스를 구성하는 방법과 위치를 이해합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: fe949aef996169f6993623fa9c4716a602f32e93
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260344"
---
# <a name="service-configuration"></a>서비스 구성

각 버전의 QnA Maker는 서로 다른 Azure 리소스(서비스) 세트를 사용합니다. 이 문서에서는 이러한 서비스에 대해 지원되는 사용자 지정에 대해 설명합니다. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

QnA Maker는 App Service를 사용하여 [generateAnswer API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer)에서 사용하는 쿼리 런타임을 제공합니다.

이러한 설정은 App Service의 Azure Portal에서 사용할 수 있습니다. **설정**, **구성** 을 차례로 선택하여 설정을 사용할 수 있습니다.

애플리케이션 설정 목록을 통해 개별 설정을 설정하거나 **고급 편집** 을 선택하여 여러 설정을 수정할 수 있습니다.

|리소스|설정|
|--|--|
|AzureSearchAdminKey|Cognitive Search - QnA 쌍 스토리지 및 Ranker #1에 사용|
|AzureSearchName|Cognitive Search - QnA 쌍 스토리지 및 Ranker #1에 사용|
|DefaultAnswer|일치하는 항목을 찾을 수 없는 경우의 응답 텍스트|
|UserAppInsightsAppId|채팅 로그 및 원격 분석|
|UserAppInsightsKey|채팅 로그 및 원격 분석|
|UserAppInsightsName|채팅 로그 및 원격 분석|
|QNAMAKER_EXTENSION_VERSION|항상 _최신_ 으로 설정합니다. 이 설정은 App Service에서 QnAMaker 사이트 확장을 초기화합니다.|

변경을 완료한 후에는 Azure Portal의 **개요** 페이지에서 서비스를 **다시 시작** 해야 합니다.

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

App Service 사용자 지정은 사용자 지정 질문 답변(미리 보기)에 적용되지 않습니다.

---

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker 서비스는 다음 사용자가 단일 QnA Maker 서비스와 모든 해당 기술 자료에서 협업할 수 있도록 구성을 제공합니다.

서비스에 [협력자를 추가하는 방법](./index.yml)에 대해 알아봅니다.

## <a name="change-azure-cognitive-search"></a>Azure Cognitive Search 변경

QnA Maker 서비스에 연결된 [Cognitive Search 서비스를 변경하는 방법](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)에 대해 알아봅니다.

## <a name="change-default-answer"></a>기본 답변 변경

[기본 답변의 텍스트를 변경하는 방법](How-To/change-default-answer.md)에 대해 알아봅니다. 

## <a name="telemetry"></a>원격 분석

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

Application Insights는 QnA Maker GA를 통해 원격 분석을 모니터링하는 데 사용됩니다. QnA Maker와 관련된 구성 설정은 없습니다.

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

[사용자 지정 질문 답변(미리 보기) 서비스에 원격 분석을 추가하는 방법](How-To/get-analytics-knowledge-base.md)에 대해 알아봅니다. 

---

## <a name="app-service-plan"></a>App Service 계획

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA(안정적인 릴리스)](#tab/v1)

App Service 계획에는 QnA Maker와 관련된 구성 설정이 없습니다.

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

App Service 계획은 사용자 지정 질문 답변(미리 보기)과 함께 사용되지 않습니다.

---

## <a name="next-steps"></a>다음 단계

기술 자료로 가져올 문서와 URL의 [형식](reference-document-format-guidelines.md)에 대해 자세히 알아봅니다.