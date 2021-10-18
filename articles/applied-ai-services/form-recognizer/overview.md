---
title: Azure Form Recognizer란? (업데이트됨)
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer 서비스를 사용하면 양식 문서에서 키/값 쌍 및 테이블 데이터를 식별 및 추출할 수 있을 뿐만 아니라 판매 영수증 및 비즈니스 카드에서 주요 정보를 추출할 수 있습니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
keywords: 자동화된 데이터 처리, 문서 처리, 자동화된 데이터 입력, 양식 처리
ms.openlocfilehash: 272104444ca3389f69c639fba0984552b93c5893
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712103"
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD024 -->
# <a name="what-is-azure-form-recognizer"></a>Azure Form Recognizer란?

Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 문서 처리 소프트웨어를 빌드할 수 있게 해주는 [Azure Applied AI Services](../../applied-ai-services/index.yml)입니다. Form Recognizer는 폼과 문서를 분석하고, 텍스트와 데이터를 추출하고, 필드 관계를 키-값 쌍으로 매핑하고, 구조화된 JSON 출력을 반환합니다. 많은 수동 작업 또는 광범위한 데이터 과학 전문 지식 없이도 특정 콘텐츠에 맞게 조정된 정확한 결과를 빠르게 얻을 수 있습니다. Form Recognizer를 사용하여 애플리케이션 및 워크플로에서 데이터 처리를 자동화하고, 데이터 기반 전략을 개선하고, 문서 검색 기능을 보강합니다.

Form Recognizer는 다음 문서 데이터를 쉽게 식별, 추출, 분석합니다.

* 테이블 구조 및 내용.
* 폼 요소와 필드 값.
* 형식화되고 필기된 영숫자 텍스트.
* 요소 간의 관계.
* 키/값 쌍
* 경계 상자 좌표가 있는 요소 위치.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* [**개념**](concept-layout.md)은 서비스 기능에 대한 심층적인 설명을 제공합니다.
* [**빠른 시작**](quickstarts/try-sdk-rest-api.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](build-training-data-set.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [**자습서**](tutorial-ai-builder.md)는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

## <a name="form-recognizer-features-and-development-options"></a>Form Recognizer 기능 및 개발 옵션

### <a name="form-recognizer-ga-v21"></a>[Form Recognizer GA(v2.1)](#tab/v2-1)

Form Recognizer 서비스 v2.1에서 지원하는 기능은 다음과 같습니다. 이 표의 링크를 사용하여 각 기능에 대해 자세히 알아보고 API 참조를 찾아보세요.

| 기능 | 설명 | 개발 옵션 |
|----------|--------------|-------------------------|
|[**레이아웃 API**](concept-layout.md) | 폼과 문서에서 해당 경계 상자 좌표와 함께 텍스트, 선택 표시 및 테이블 구조를 추출 및 분석합니다. | <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/layout-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-layout)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**비즈니스 카드 모델**](concept-business-card.md) | 명함에서 주요 정보를 자동으로 처리하고 추출합니다.| <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-business-cards)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**ID 문서 모델**](concept-id-document.md) | 미국 운전 면허증 및 국제 여권의 핵심 정보에 대한 자동화된 데이터 처리 및 추출.| <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**청구서 모델**](concept-invoice.md) | 판매 청구서에서 주요 정보를 자동으로 처리하고 추출합니다. | <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**영수증 모델**](concept-receipt.md) | 판매 영수증에서 주요 정보를 자동으로 처리하고 추출합니다.| <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**사용자 지정 모델**](concept-business-card.md) | 고유한 비즈니스 데이터 및 사용 사례와 관련된 양식과 문서에서 데이터를 추출하고 분석합니다.| <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|

### <a name="form-recognizer-preview-v30"></a>[Form Recognizer 미리 보기(v3.0)](#tab/v3-0)

>[!NOTE]
> Form Recognizer Studio는 현재 공개 미리 보기에 있습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다.

Form Recognizer 서비스 v3.0에서 지원하는 기능 및 개발 옵션은 다음과 같습니다. 이 표의 링크를 사용하여 각 기능에 대해 자세히 알아보고 API 참조를 찾아보세요.

| 기능 | 설명 | 개발 옵션 |
|----------|--------------|-------------------------|
|[🆕 **일반 문서 모델**](concept-general-document.md)|텍스트, 테이블, 구조, 키-값 쌍 및 명명된 엔터티를 추출합니다.|<ul ><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul> |
|[**레이아웃 모델**](concept-layout.md) | 양식 및 문서에서 경계 상자 좌표와 함께 텍스트, 선택 표시 및 테이블 구조를 추출합니다.</br></br> 레이아웃 API가 미리 빌드된 모델로 업데이트되었습니다. | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**사용자 지정 모델(업데이트됨)** ](concept-custom.md) | 고유한 비즈니스 데이터 및 사용 사례와 관련된 양식과 문서에서 데이터를 추출하고 분석합니다.</br></br>사용자 지정 모델 API v3.0은 **사용자 지정 양식에 대한 서명 검색** 을 지원합니다.</li></ul>| <ul><li>[**Form Recognizer Studio**](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**영수증 모델(업데이트됨)** ](concept-receipt.md) | 판매 영수증에서 주요 정보를 자동으로 처리하고 추출합니다.</br></br>영수증 모델 v3.0은 **단일 페이지 호텔 영수증** 의 처리를 지원합니다.| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**ID 문서 모델(업데이트됨)** ](concept-id-document.md) |미국 운전 면허증 및 국제 여권의 핵심 정보에 대한 자동화된 데이터 처리 및 추출.</br></br>미리 빌드된 ID 문서 API는 **미국 운전 면허증에서 인증, 제한 및 차량 분류 추출** 을 지원합니다. |<ul><li> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**청구서 모델**](concept-invoice.md) | 판매 청구서에서 주요 정보를 자동으로 처리하고 추출합니다. | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**비즈니스 카드 모델**](concept-business-card.md) |명함에서 주요 정보를 자동으로 처리하고 추출합니다.| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|

---

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 필요합니다. [**체험 구독을 만드세요**](https://azure.microsoft.com/free/cognitive-services).

* 구독이 있으면 Azure Portal에서 [**Form Recognizer 리소스**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)를 만듭니다. 체험 가격 책정 계층(F0)을 통해 서비스를 사용해 보고, 나중에 프로덕션을 위한 유료 계층으로 업그레이드할 수 있습니다.

* 마지막으로, Form Recognizer 서비스를 사용해 보려면 Azure Portal에서 리소스 **엔드포인트 URL** 및 **API 키** 를 검색해야 합니다.

  :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 창.":::

 ## <a name="form-recognizer-workflows"></a>Form Recognizer 워크플로

| 문서 형식 | 고려 사항 | 해결 방법 |
| -----------------|-------------------| ----------|
|<ul><li>**청구서**</li><li>**Receipt**</li><li>**명함**</li></ul>| 청구서, 영수증 또는 명함 문서는 영문으로 구성되어 있나요? | <ul><li>네 → [**청구서**](concept-invoice.md), [**영수증**](concept-receipt.md) 또는 [**명함**](concept-business-card.md) 모델</li><li>아니요 → [**레이아웃**](concept-layout.md) 또는 [**일반 문서(미리 보기)** ](concept-general-document.md) 모델</li></ul>|
|<ul><li>**ID 문서**</li></ul>| ID 문서는 미국 운전 면허 또는 국제 여권인가요?| <ul><li>예 → [**ID 문서**](concept-id-document.md) 모델</li><li>아니요 → [**레이아웃**](concept-layout.md) 또는 [**일반 문서(미리 보기)** ](concept-general-document.md) 모델</li></ul>|
|<ul><li>**양식** 또는 **문서**</li></ul>| 양식이나 문서가 회사 또는 업계에서 일반적으로 사용되는 업계 표준 형식인가요?| <ul><li>예 → [**레이아웃**](concept-id-document.md) 또는 [**일반 문서(미리 보기)** ](concept-general-document.md) 모델</li><li>아니요 → [**사용자 지정 모델 학습 및 빌드**](concept-layout.md) 

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

 모든 Cognitive Services와 마찬가지로 Form Recognizer 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. [Form Recognizer에 대한 데이터, 개인 정보 보호 및 보안](/legal/cognitive-services/form-recognizer/fr-data-privacy-security) 페이지를 참조하세요. 

## <a name="next-steps"></a>다음 단계

### <a name="form-recognizer-v21"></a>[Form Recognizer v2.1](#tab/v2-1)

> [!div class="checklist"]
>
> * [**샘플 레이블 지정 온라인 도구**](https://aka.ms/fott-2.1-ga/) 사용해 보기
> * [**클라이언트 라이브러리/REST API 빠른 시작**](./quickstarts/try-sdk-rest-api.md)에 따라 문서에서 데이터 추출을 시작합니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.
> * 자세히 알아보려면 [**REST API 참조 설명서**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)를 확인하세요. 
> * 이전 버전의 API에 대해 잘 알고 있는 경우에는 [**새로운 기능**](./whats-new.md) 문서를 참조하여 최신 변경 내용에 대해 알아보세요.

### <a name="form-recognizer-v30"></a>[Form Recognizer v3.0](#tab/v3-0)

> [!div class="checklist"]
>
> * [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com) 사용해 보기
> * 자세히 알아보려면 [**REST API 참조 설명서**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)를 확인하세요. 
> * 이전 버전의 API에 대해 잘 알고 있는 경우에는 [**새로운 기능**](./whats-new.md) 문서를 참조하여 최신 변경 내용에 대해 알아보세요.

---
