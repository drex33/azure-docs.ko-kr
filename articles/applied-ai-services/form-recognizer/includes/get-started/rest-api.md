---
title: '시작: Azure Form Recognizer REST API v 2.1'
description: Form Recognizer REST API v 2.1을 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: c889cb0db92a4b9df9df07ddad9cb136e147b466
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132875430"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> 이 빠른 시작은 cURL을 사용하여 REST API 호출을 실행하는 Azure Form Recognizer REST API 버전 **2.1** 을 대상으로 합니다.

| [Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Azure REST API 참조](/rest/api/azure/) |

이 빠른 시작에서는 다음 API를 사용하여 양식과 문서에서 구조화된 데이터를 추출합니다.

* [레이아웃](#try-it-layout-model)

* [청구서](#try-it-prebuilt-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

* [cURL](https://curl.haxx.se/windows/) 설치

* [PowerShell 버전 6.0 이상](/powershell/scripting/install/installing-powershell-core-on-windows) 또는 유사한 명령줄 애플리케이션.

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

  > [!TIP]
  > 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](../../../../active-directory/authentication/overview-authentication.md)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스를 배포한 후 **리소스로 이동** 을 선택합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>애플리케이션에 복사하여 붙여넣을 코드 샘플을 선택합니다.

* [**Layout**](#try-it-layout-model)

* [**미리 빌드된 청구서**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 Cognitive Services [보안](../../../../cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `\"{your-document-url}`을(를) 샘플 양식 URL로 바꿉니다.

```http
https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf
```

#### <a name="request"></a>요청

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{resultId}** .

다음 예제에서 URL의 일부로 `analyzeResults/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>레이아웃 결과 가져오기

**[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** API를 호출한 후 **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>결과 검사

JSON 콘텐츠가 포함된 `200 (success)` 응답을 받게 됩니다.

다음 청구서 이미지와 해당 JSON 출력을 참조하세요.

* `"readResults"` 노드에는 페이지의 각 경계 상자 배치가 있는 모든 텍스트 줄이 포함되어 있습니다.
* `selectionMarks` 노드는 모든 선택 표시(확인란, 라디오 표시) 및 상태가 “선택됨” 또는 “선택되지 않음”인지 여부를 표시합니다.
* `"pageResults"` 섹션에는 추출된 표가 포함됩니다. 각 표에서 텍스트, 행 및 열 인덱스, 행 및 열 스패닝, 경계 상자 등이 추출됩니다.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="테이블이 포함된 Contoso 프로젝트 문 문서.":::

#### <a name="response-body"></a>응답 본문

[GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)을 확인할 수 있습니다.

## <a name="try-it-prebuilt-model"></a>**체험해 보기**: 미리 빌드된 모델

> [!div class="checklist"]
>
> * 이 예제에서는 미리 빌드된 모델을 사용하여 청구서 문서를 분석합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.

### <a name="choose-a-prebuilt-model"></a>미리 빌드된 모델 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. Form Recognizer 서비스에서 현재 지원되는 미리 빌드된 모델은 다음과 같습니다.

* [**청구서**](../../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 필드 및 키 정보를 추출합니다.
* [**영수증**](../../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**ID 문서**](../../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**명함**](../../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `{subscription key}`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `\"{your-document-url}`을(를) 샘플 청구서 URL로 바꿉니다.

    ```http
    https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf
    ```

#### <a name="request"></a>요청

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 결과 ID가 포함되어 있습니다.

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{resultId}**_

다음 예제에서 URL의 일부로 `analyzeResults/` 뒤에 있는 문자열이 결과 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>청구서 결과 가져오기

**Analyze Invoice** API를 호출한 후 **[Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `{endpoint}`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `{resultId}`를 이전 단계의 결과 ID로 바꿉니다.
1. `{subscription key}`를 구독 키로 바꿉니다.

#### <a name="request"></a>요청

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다.

* `"readResults"` 필드는 청구서에서 추출된 모든 텍스트 줄이 포함되어 있습니다.
* `"pageResults"`는 청구서에서 추출된 표 및 선택 표시를 포함합니다.
* `"documentResults"` 필드에는 청구서의 가장 관련성이 높은 부분에 대한 키/값 정보가 포함되어 있습니다.

[샘플 청구서](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf) 문서를 참조하세요.

#### <a name="response-body"></a>응답 본문

[GitHub의 전체 샘플 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

축하합니다! 이 빠른 시작에서는 Form Recognizer REST API를 사용하여 다양한 형태를 다양한 방식으로 분석했습니다. 다음으로, 참조 설명서를 살펴보고서 Form Recognizer v3.0 API에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [REST API v2.1 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)