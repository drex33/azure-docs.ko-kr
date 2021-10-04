---
title: Text Analytics API의 새로운 기능
titleSuffix: Text Analytics - Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Text Analytics의 새 릴리스와 기능에 관한 정보를 제공합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 64ac913bb8f3e29dd3a99ab5abde5db3326a1a29
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423861"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Text Analytics API의 새로운 기능

Text Analytics API는 지속적으로 업데이트됩니다. 최신 개발 정보를 항상 파악할 수 있게 이 문서에서는 새 릴리스 및 기능 관련 정보를 제공합니다.

## <a name="october-2021"></a>2021년 10월

* 모델 버전의 [Extractive 요약](how-tos/extractive-summarization.md) 기능의 품질 향상 `2021-08-01`

## <a name="september-2021"></a>2021년 9월

* 버전부터 `3.0.017010001-onprem-amd64` 상태 컨테이너에 대 한 Text Analytics는 이제 클라이언트 라이브러리를 사용 하 여 호출할 수 있습니다. 자세한 내용은 [Text Analytics 컨테이너를 설치 하 고 실행 하는 방법을](how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare#run-the-container-with-client-library-support) 참조 하세요.


## <a name="august-2021"></a>2021년 8월

* [추출 요약](how-tos/extractive-summarization.md)의 퍼블릭 미리 보기를 포함하는 버전 `3.2-preview.1`.
* 이제 Azure Government 및 Azure 중국 지역에서 [비동기 작업](how-tos/text-analytics-how-to-call-api.md?tabs=asynchronous)을 사용할 수 있음
* 추출 요약을 지원하는 클라이언트 라이브러리의 새 미리 보기 버전 자세한 내용은 [빠른 시작을 참조](quickstarts/client-libraries-rest-api.md)하세요.

## <a name="july-2021"></a>2021년 7월

### <a name="ga-release-updates"></a>GA 릴리스 업데이트

* 컨테이너 및 호스트된 API(/health) 모두에 대한 [Text Analytics for Health](how-tos/text-analytics-for-health.md?tabs=ner) 일반 공급
* [오피니언 마이닝](how-tos/text-analytics-how-to-sentiment-analysis.md?tabs=version-3-1#opinion-mining) 일반 공급
* [PII 추출 및 교정](how-tos/text-analytics-how-to-entity-linking.md?tabs=version-3-1#personally-identifiable-information-pii) 일반 공급
* [비동기(`/analyze`) 엔드포인트](how-tos/text-analytics-how-to-call-api.md?tabs=synchronous#using-the-api-asynchronously) 일반 공급
* 새 SDK를 사용하여 [빠른 시작](quickstarts/client-libraries-rest-api.md) 예제가 업데이트되었습니다. 

## <a name="june-2021"></a>2021년 6월

### <a name="general-api-updates"></a>일반 API 업데이트

* 변환기를 기반으로 하여 핵심 구를 추출하는 새 모델 버전(`2021-06-01`). 이 콘솔은 다음과 같은 기능을 제공합니다.
  * 10개 언어(라틴어 및 CJK) 지원 
  * 향상된 핵심 구 추출
* 다음을 제공하는 [명명된 엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md) v3.x의 `2021-06-01` 모델 버전 
  * *기술* 엔터티 범주에 대한 AI 품질 개선 및 언어 지원 확대. 
  * *기술* 엔터티 범주에 대한 스페인어, 프랑스어, 독일어, 이탈리아어 및 포르투갈어 지원이 추가되었습니다.
* 모든 지역에서 비동기(/analyze) 작업 및 Text Analytics for Health(업데이트된 미리 보기)를 사용할 수 있습니다. 

### <a name="text-analytics-for-health-updates"></a>Text Analytics for Health 업데이트

* 상태에 대한 미리 보기 Text Analytics에 액세스를 더 이상 적용할 필요가 없습니다.
* 다음을 제공하는 `/health` 엔드포인트 및 온-프레미스 컨테이너의 새 모델 버전 `2021-05-15`
    * `ALLERGEN`, `CONDITION_SCALE`, `COURSE`, `EXPRESSION` 및 `MUTATION_TYPE`의 5가지 새 엔터티 형식
    * 14개의 새 관계 유형
    * 새 엔터티 형식에 대해 어설션 검색 확장 및
    * ALLERGEN 엔터티 형식에 대한 연결 지원
* 태그가 `3.0.016230002-onprem-amd64`이고 모델 버전이 `2021-05-15`인 Text Analytics for Health 컨테이너에 대한 새 이미지. 이 컨테이너는 Microsoft Container Registry에서 다운로드할 수 있습니다.
 
## <a name="may-2021"></a>2021년 5월

* 이제 Text Analytics 리소스를 사용하여 [사용자 지정 질문 답변](../qnamaker/custom-question-answering.md)(이전의 QnA maker)에 액세스할 수 있습니다. 

### <a name="general-api-updates"></a>일반 API 업데이트

* 다음을 포함하는 새 API v3.1-preview.5 릴리스 
  * 이제 비동기 [분석 API](how-tos/text-analytics-how-to-call-api.md?tabs=asynchronous)는 SA(감정 분석)와 OM(의견 마이닝)을 지원합니다.
  * 이제 인시던트 보고서에 대한 입력 텍스트 로깅을 옵트아웃하려는 고객은 새 쿼리 매개 변수 `LoggingOptOut`을 사용할 수 있습니다.  [데이터 개인 정보 보호](/legal/cognitive-services/text-analytics/data-privacy?context=/azure/cognitive-services/text-analytics/context/context) 문서에서 이 매개 변수에 대해 자세히 알아봅니다.
* 이제 모든 지역에서 상태에 대한 Text Analytics 및 분석 비동기 작업을 사용할 수 있습니다.

## <a name="march-2021"></a>2021년 3월

### <a name="general-api-updates"></a>일반 API 업데이트
* 다음을 포함하는 새 API v3.1-preview.4 릴리스 
   * 오피니언 마이닝 JSON 응답 본문의 변경: 
      * 이제 `aspects`는 `targets`, `opinions`는 `assessments`입니다. 
   * Text Analytics for Health의 호스트된 웹 API에서 JSON 응답 본문 변경: 
      * 부정에 대해 검색된 엔터티 개체의 `isNegated` 부울 이름은 이제 사용되지 않으며 어설션 검색으로 바뀝니다.
      * `role`이라는 새 속성이 이제 특성과 엔터티 간의 추출된 관계와 엔터티 간의 관계에 속합니다.  이로 인해 검색된 관계 유형에 특이성이 추가됩니다.
   * 이제 `/analyze` 엔드포인트에서 엔터티 링크 설정을 비동기 작업으로 사용할 수 있습니다.
   * 이제 `/pii` 엔드포인트에서 새 `pii-categories` 매개 변수를 사용할 수 있습니다.
      * 이 매개 변수를 사용하여 PII 엔터티 선택과, 기본적으로 입력 언어로 지원되지 않는 엔터티를 지정할 수 있습니다.
* 비동기 분석 및 Text Analytics for Health를 포함하는 클라이언트 라이브러리가 업데이트되었습니다. GitHub에서 예를 찾을 수 있습니다.

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript)
    
> [!div class="nextstepaction"]
> [Text Analytics API v3.1-Preview.4에 대한 자세한 정보](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>Text Analytics for Health 업데이트

* 다음을 제공하는 `/health` 엔드포인트 및 온-프레미스 컨테이너의 새 모델 버전 `2021-03-01`
    * `Gene` 엔터티 유형의 이름이 `GeneOrProtein`으로 변경되었습니다.
    * 새 `Date` 엔터티 유형
    * 부정 검색을 바꾸는 어설션 검색(API v3.1-preview.4에서만 사용 가능)
    * 다양한 온톨로지 및 코딩 시스템에서 정규화된 연결된 엔터티에 대한 새로운 기본 설정 `name` 속성(API v3.1-preview.4에서만 사용 가능) 
* 새 모델-버전 `2021-03-01` 및 `3.0.015490002-onprem-amd64` 태그가 있는 새 컨테이너 이미지가 컨테이너 미리 보기 리포지토리에 릴리스되었습니다. 
    * 이 컨테이너 이미지는 2021년 4월 26일 이후 더 이상 `containerpreview.azurecr.io`에서 다운로드할 수 없습니다.
* 이제 동일한 모델-버전을 사용하는 새 Text Analytics for Health 컨테이너 이미지를 `mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare`에서 사용할 수 있습니다. 4월 26일부터 이 리포지토리에서 컨테이너를 다운로드할 수 있습니다.

> [!div class="nextstepaction"]
> [Text Analytics for Health에 대한 자세한 정보](how-tos/text-analytics-for-health.md)

### <a name="text-analytics-resource-portal-update"></a>Text Analytics 리소스 포털 업데이트
* **처리된 텍스트 레코드** 는 이제 Azure Portal에서 Text Analytics 리소스에 대한 **모니터링** 섹션의 메트릭으로 사용할 수 있습니다.  

## <a name="february-2021"></a>2021년 2월

* 다음을 제공하는 [명명된 엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md) v3.1-preview.x의 PII 엔드포인트에 대한 `2021-01-15` 모델 버전 
  * 9개의 새 언어에 대한 지원 확장
  * 지원되는 언어에 대한 명명된 엔터티 범주의 AI 품질 향상
* S0~S4 가격 책정 계층은 2021년 3월 8일에 사용 중지됩니다. S0~S4 가격 책정 계층을 사용하는 기존 Text Analytics 리소스가 있는 경우 표준(S) [가격 책정 계층](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)을 사용하도록 업데이트해야 합니다.
* [언어 검색 컨테이너](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment)가 이제 일반 공급됩니다.
* API의 v2.1을 사용 중지하는 과정에 있습니다. 

## <a name="january-2021"></a>2021년 1월

* 다음을 제공하는 [명명된 엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md) v3.x의 `2021-01-15` 모델 버전 
  * [몇 가지 일반 엔터티 범주](named-entity-types.md)에 대한 언어 지원 확장 
  * 지원되는 모든 v3 언어에 대한 일반 엔터티 범주의 AI 품질 향상 

* [언어 검색](how-tos/text-analytics-how-to-language-detection.md)에 대한 `2021-01-05` 모델 버전으로, 추가적인 [언어 지원](language-support.md?tabs=language-detection)을 제공합니다.

이 모델 버전은 현재 미국 동부 지역에서 사용할 수 없습니다. 

> [!div class="nextstepaction"]
> [새 NER 모델에 대한 자세한 정보](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>2020년 12월

* Text Analytics API에 대한 [가격 정보가 업데이트되었습니다](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

## <a name="november-2020"></a>2020년 11월

* NER, PII, 핵심 구 추출 작업의 일괄 처리를 지원하는 비동기 [분석 API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)에 대한 Text Analytics API v3.1-preview.3의 [새 엔드포인트](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze)
* 일괄 처리를 지원하는 새 비동기 [Text Analytics for Health](how-tos/text-analytics-for-health.md) 호스트 API에 대한 Text Analytics API v3.1-preview.3이 포함된 [새 엔드포인트](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health)
* 위 목록의 새 기능은 `West US 2`, `East US 2`, `Central US`, `North Europe` 및 `West Europe` 지역에서만 사용할 수 있습니다.
* 이제 모델 버전 `2020-04-01`부터 [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md) v3.x에서 포르투갈어(브라질)`pt-BR`가 지원됩니다. 이는 포르투갈어에 대한 기존 `pt-PT` 지원을 추가합니다.
* 비동기 분석 및 Text Analytics for Health를 포함하는 클라이언트 라이브러리가 업데이트되었습니다. GitHub에서 예를 찾을 수 있습니다.

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [Text Analytics API v3.1-Preview.3에 대한 자세한 정보](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>2020년 10월

* 모델 버전 `2020-04-01`부터 감정 분석 v3.x에서 힌디어 지원 
* 언어 검색 및 정확도가 향상된 v3/언어 엔드포인트의 모델 버전 `2020-09-01`
* 인도 중부 및 아랍에미리트 북부의 v3 가용성

## <a name="september-2020"></a>2020년 9월

### <a name="general-api-updates"></a>일반 API 업데이트

* 다음 명명된 엔터티 인식 v3 엔드포인트에 대한 업데이트를 지원하기 위한 Text Analytics v3.1 공개 미리 보기의 새 URL 릴리스 
    * 이제 `/pii` 엔드포인트의 응답 JSON에 새 `redactedText` 속성이 포함되어, 입력 텍스트의 검색된 PII 엔터티가 엔터티의 각 문자에 대해 `*`로 바뀝니다.
    * `/linking` 엔드포인트는 이제 연결된 엔터티에 대해 응답 JSON에 `bingID` 속성을 포함합니다.
* 다음 Text Analytics 미리 보기 API 엔드포인트는 2020년 9월 4일에 사용이 중지되었습니다.
    * v2.1-preview
    * v3.0-미리 보기
    * v3.0-preview.1
    
> [!div class="nextstepaction"]
> [Text Analytics API v3.1-Preview.2에 대한 자세한 정보](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Text Analytics for Health 컨테이너 업데이트

다음 업데이트는 Text Analytics for Health 컨테이너에 대한 9월 릴리스에만 적용됩니다.
* 새 모델 버전 `2020-09-03` 및 `1.1.013530001-amd64-preview` 태그가 있는 새 컨테이너 이미지가 컨테이너 미리 보기 리포지토리에 릴리스되었습니다. 
* 이 모델 버전은 엔터티 인식, 약어 검색 및 대기 시간 단축에서 향상된 기능을 제공합니다.

> [!div class="nextstepaction"]
> [Text Analytics for Health에 대한 자세한 정보](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>2020년 8월

### <a name="general-api-updates"></a>일반 API 업데이트

* 다음을 추가하는 v3 `/keyphrases`, `/pii` 및 `/languages` 엔드포인트에 대한 모델 버전 `2020-07-01`
    * 명명된 엔터티 인식에서 추가적인 정부 및 국가별 [엔터티 범주](named-entity-types.md?tabs=personal)
    * 감정 분석 v3에서 노르웨이어 및 터키어 지원
* 이제 게시된 [데이터 한도](concepts/data-limits.md)를 초과하는 v3 API 요청에 대해 HTTP 400 오류가 반환됩니다. 
* 이제 오프셋을 반환하는 엔드포인트는 선택적 `stringIndexType` 매개 변수를 지원하며, 이 매개 변수는 반환된 `offset` 및 `length` 값을 지원되는 [문자열 인덱스 체계](concepts/text-offsets.md)에 맞게 조정합니다.

### <a name="text-analytics-for-health-container-updates"></a>Text Analytics for Health 컨테이너 업데이트

다음 업데이트는 Text Analytics for Health 컨테이너에 대한 8월 릴리스에만 적용됩니다.

* Text Analytics for Health의 새 모델-버전: `2020-07-24`
* Text Analytics for Health 요청을 보내기 위한 새 URL: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health`(이 새 컨테이너 이미지에 포함된 데모 웹 앱을 사용하려면 브라우저 캐시 지우기 필요)

JSON 응답에서 다음 속성이 변경되었습니다.

* `type`는 `category`로 이름이 변경되었습니다. 
* `score`는 `confidenceScore`로 이름이 변경되었습니다.
* JSON 출력의 `category` 필드에 있는 엔터티는 이제 파스칼식 대/소문자입니다. 다음 클래스의 이름이 변경되었습니다.
    * `EXAMINATION_RELATION` 이름이 `RelationalOperator`으로 바뀌었습니다.
    * `EXAMINATION_UNIT` 이름이 `MeasurementUnit`으로 바뀌었습니다.
    * `EXAMINATION_VALUE` 이름이 `MeasurementValue`으로 바뀌었습니다.
    * `ROUTE_OR_MODE` 이름이 `MedicationRoute`로 바뀌었습니다.
    * 관계형 엔터티 `ROUTE_OR_MODE_OF_MEDICATION`의 이름이 `RouteOfMedication`으로 바뀌었습니다.

다음과 같은 엔터티가 추가되었습니다.

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* 관계 추출
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Text Analytics for Health 컨테이너에 대한 자세한 정보](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>2020년 7월 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Text Analytics for Health - 제어된 공개 미리 보기

Text Analytics for Health 컨테이너는 이제 제어된 공개 미리 보기 상태로, 환자 문진 양식, 의사 메모, 연구 논문, 퇴원서 등의 의료 문서에 사용된 구조화되지 않은 영어 텍스트에서 정보를 추출할 수 있습니다. 현재 Text Analytics for Health 컨테이너 사용에 대해서는 요금이 청구되지 않습니다.

이 컨테이너는 다음과 같은 기능을 제공합니다.

* 명명된 엔터티 인식
* 관계 추출
* 엔터티 연결
* 부정

## <a name="may-2020"></a>2020년 5월

### <a name="text-analytics-api-v3-general-availability"></a>Text Analytics API v3 일반 공급

이제 Text Analytics API v3은 다음과 같은 업데이트와 함께 일반 공급됩니다.

* 모델 버전 `2020-04-01`
* 각 기능에 대한 새로운 [데이터 제한](concepts/data-limits.md)
* [SA(감정 분석) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)의 [언어 지원](language-support.md) 업데이트
* 엔터티 링크 설정을 위한 별도의 엔드포인트 
* [NER(명명된 엔터티 인식) v3](how-tos/text-analytics-how-to-entity-linking.md)의 새 "Address" 엔터티 범주
* NER v3의 새 하위 범주:
   * 위치 - 지리적 위치
   * 위치 - 구조적
   * 조직 - 증권 거래
   * 조직 - 의료
   * 조직 - 스포츠
   * 이벤트 - 문화
   * 이벤트 - 자연
   * 이벤트 - 스포츠

JSON 응답에서 다음 속성이 추가되었습니다.
   * 감정 분석의 `SentenceText`
   * 각 문서에 대한 `Warnings` 

JSON 응답에서 다음 속성의 이름이 변경되었습니다(해당하는 경우).

* `score`는 `confidenceScore`로 이름이 변경되었습니다.
    * `confidenceScore`의 소수점 자릿수는 2자리입니다. 
* `type`는 `category`로 이름이 변경되었습니다.
* `subtype`는 `subcategory`로 이름이 변경되었습니다.

> [!div class="nextstepaction"]
> [Text Analytics API v3에 대한 자세한 정보](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Text Analytics API v3.1 공개 미리 보기
   * 새 감정 분석 기능 - [오피니언 마이닝](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * 보호 대상 의료 정보(`PHI`)에 대한 새 개인(`PII`) 도메인 필터

> [!div class="nextstepaction"]
> [Text Analytics API v3.1 미리 보기에 대한 자세한 정보](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020년 2월

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Text Analytics API v3 공개 미리 보기의 SDK 지원

[통합 Azure SDK 릴리스](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)의 일부로, Text Analytics API v3 SDK가 이제 다음 프로그래밍 언어에 대해 공개 미리 보기로 제공됩니다.
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript(Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Text Analytics API v3 SDK에 대한 자세한 정보](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>명명된 엔터티 인식 v3 공개 미리 보기

이제 텍스트에 있는 일반 및 개인 정보 엔터티 검색을 확장할 때 NER(명명된 엔터티 인식) v3 공개 미리 보기 서비스에서 추가적인 엔터티 유형을 사용할 수 있습니다. 이 업데이트에는 다음을 포함하는 [모델 버전 ](concepts/model-versioning.md) `2020-02-01`이 도입되었습니다.

* 다음과 같은 일반 엔터티 유형 인식(영어만 해당):
    * PersonType
    * Product
    * 이벤트
    * GPE(지정학적 엔터티)를 위치 아래의 하위 유형으로
    * 기술

* 다음 개인 정보 엔터티 유형 인식(영어만 해당):
    * 사람
    * 조직
    * 사용 기간을 수량 아래의 하위 유형으로
    * 날짜를 DateTime 아래의 하위 유형으로
    * 메일 
    * 전화 번호(미국에만 해당)
    * URL
    * IP 주소

### <a name="october-2019"></a>2019년 10월

#### <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

* 개인 정보 엔터티 유형 인식을 위한 [새 엔드포인트](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)(영어만 해당)

* [엔터티 인식](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) 및 [엔터티 링크 설정](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)을 위한 별도의 엔드포인트

* 다음을 포함하는 [모델 버전](concepts/model-versioning.md) `2019-10-01`:
    * 텍스트에 있는 엔터티의 확장된 검색 및 분류 
    * 다음과 같은 새로운 엔터티 유형 인식:
        * 전화 번호
        * IP 주소

엔터티 링크 설정에서 영어와 스페인어를 지원합니다. NER 언어 지원은 엔터티 형식에 따라 다릅니다.

#### <a name="sentiment-analysis-v3-public-preview"></a>감정 분석 v3 공개 미리 보기

* 감정 분석을 위한 [새 엔드포인트](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment).
* 다음을 포함하는 [모델 버전](concepts/model-versioning.md) `2019-10-01`:

    * API의 텍스트 분류 및 채점에 대한 정확성과 세부 정보가 크게 향상되었습니다.
    * 텍스트에서 다양한 감정에 대해 자동으로 레이블을 지정합니다.
    * 문서 및 문장 수준에서 감정 분석 및 출력을 수행합니다. 

영어(`en`), 일본어(`ja`), 중국어 간체(`zh-Hans`), 중국어 번체(`zh-Hant`), 프랑스어(`fr`), 이탈리아어(`it`), 스페인어(`es`), 네덜란드어(`nl`), 포르투갈어(`pt`) 및 독일어(`de`)를 지원하며 `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` 및 `West US 2` 등의 지역에서 사용할 수 있습니다. 

> [!div class="nextstepaction"]
> [감정 분석 v3에 대한 자세한 정보](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>다음 단계

* [텍스트 분석 API란?](overview.md)  
* [사용자 시나리오 예](text-analytics-user-scenarios.md)
* [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [언어 감지](how-tos/text-analytics-how-to-language-detection.md)
* [엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md)
* [핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md)
