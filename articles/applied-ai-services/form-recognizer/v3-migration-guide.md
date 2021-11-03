---
title: '방법: Form Recognizer v2.1에서 v3.0으로 애플리케이션을 마이그레이션합니다.'
titleSuffix: Azure Applied AI Services
description: 이 방법 가이드에서는 Form Recognizer API v2.1과 v3.0의 차이점을 알아봅니다. 최신 버전의 API로 이동하는 데 필요한 변경 내용도 알아봅니다.
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: vikurpad
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: fd0c9c1ebaf177f6f10698631b96e2c27825603f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020828"
---
# <a name="form-recognizer-v30-migration--preview"></a>v3.0 마이그레이션 | Form Recognizer 미리 보기

> [!IMPORTANT]
>
> Form Recognizer REST API v3.0에서는 REST API 요청의 주요 변경 내용을 소개하고 응답 JSON을 분석합니다.

Form Recognizer v3.0(미리 보기)에는 다음과 같은 몇 가지 새로운 기능이 도입되었습니다.

* [Form Recognizer REST API](quickstarts/try-v3-rest-api.md) 더 나은 유용성을 위해 다시 디자인되었습니다.
* [**일반 문서(v3.0)**](concept-general-document.md) 모델은 양식 및 문서에서 텍스트, 테이블, 구조체, 키-값 쌍 및 명명된 엔터티를 추출하는 새로운 API입니다.
* [**영수증(v3.0)**](concept-receipt.md) 모델은 단일 페이지 호텔 영수증 처리를 지원합니다.
* [**ID 문서(v3.0)**](concept-id-document.md) 모델은 미국 운전면허에서 보증, 제한 및 차량 분류 추출을 지원합니다.
* [**사용자 지정 모델 API(v3.0)는**](concept-custom.md) 사용자 지정 양식에 대한 서명 검색을 지원합니다.

이 문서에서는 Form Recognizer v2.1과 v3.0 간의 차이점과 최신 버전의 API로 이동하는 방법을 알아봅니다.

## <a name="changes-to-the-rest-api-endpoints"></a>REST API 엔드포인트에 대한 변경 내용

 v3.0 REST API 레이아웃 분석, 미리 작성한 모델 및 사용자 지정 모델에 대한 분석 작업을 **`documentModels`**  **`modelId`** 레이아웃 분석(미리 작성한 레이아웃) 및 미리 작성한 모델에 할당하여 단일 작업 쌍으로 결합합니다.

### <a name="post-request"></a>POST 요청

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview

```

### <a name="get-request"></a>GET 요청

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}/AnalyzeResult/{resultId}?api-version=2021-07-30-preview
```

### <a name="analyze-operation"></a>분석 작업

* 요청 페이로드 및 호출 패턴은 변경되지 않은 상태로 유지됩니다.
* 분석 작업은 입력 문서 및 콘텐츠별 구성을 지정하고 응답의 Operation-Location 헤더를 통해 분석 결과 URL을 반환합니다.
* GET 요청을 통해 이 분석 결과 URL을 폴링하여 분석 작업의 상태를 확인합니다(요청 간의 최소 권장 간격은 1초임).
* 성공하면 상태가 성공으로 설정되고 [analyzeResult가](#changes-to-analyze-result) 응답 본문에 반환됩니다. 오류가 발생하면 상태가 실패로 설정되고 오류가 반환됩니다.

| 모델 | v2.1 | v3.0 |
|:--| :--| :--|
| **요청 URL 접두사**| **https://{your-form-recognizer-endpoint}/formrecognizer/v2.1**  | **https://{your-form-recognizer-endpoint}/formrecognizer** |
|🆕 일반 **문서**|해당 없음|/documentModels/prebuilt-document:analyze |
| **레이아웃**| /layout/analyze |/documentModels/prebuilt-layout:analyze|
|**사용자 지정**| /custom/{modelId}/analyze    |/documentModels/{modelId}:analyze |
| **청구서** | /prebuilt/invoice/analyze    | /documentModels/prebuilt-invoice:analyze |
| **Receipt** | /prebuilt/receipt/analyze    | /documentModels/prebuilt-receipt:analyze |
| **ID 문서** | /prebuilt/idDocument/analyze |  /documentModels/prebuilt-idDocument:analyze |
|**명함**| /prebuilt/businessCard/analyze| /documentModels/prebuilt-businessCard:analyze|

### <a name="analyze-request-body"></a>요청 본문 분석

분석할 콘텐츠는 요청 본문을 통해 제공됩니다. URL 또는 base64로 인코딩된 데이터는 사용자가 요청을 생성할 수 있습니다.

  공개적으로 액세스할 수 있는 웹 URL을 지정하려면 Content-Type을 **application/json으로** 설정하고   다음 JSON 본문을 보냅니다.

  ```json
  {
    "urlSource": "{urlPath}"
  }
  ```

Base64 인코딩은 Form Recognizer v3.0에서도 지원됩니다.

```json
{
  "base64Source": "{base64EncodedContent}"
}
```

### <a name="additional-parameters"></a>추가 매개 변수

계속 지원되는 매개 변수:

* 페이지
* locale

매개 변수는 더 이상 지원되지 않습니다. 

* includeTextDetails

새 응답 형식이 더 간결하며 전체 출력이 항상 반환됩니다.

## <a name="changes-to-analyze-result"></a>결과를 분석하기 위한 변경 내용

분석 응답은 다중 페이지 요소를 지원하기 위해 다음과 같은 최상위 결과로 리팩터되었습니다.

* 페이지
* 테이블
* keyValuePairs
* 엔터티
* 스타일
* 문서

> [!NOTE]
>
> analyzeResult 응답 변경에는 pages의 속성에서 analyzeResult 내의 최상위 레버 속성으로 이동하는 것과 같은 다양한 변경 내용이 포함됩니다.

```json

{
// Basic analyze result metadata
"apiVersion": "2021-07-30-preview", // REST API version used
"modelId": "prebuilt-invoice", // ModelId used
"stringIndexType": "textElements", // Character unit used for string offsets and lengths:
// textElements, unicodeCodePoint, utf16CodeUnit // Concatenated content in global reading order across pages.
// Words are generally delimited by space, except CJK (Chinese, Japanese, Korean) characters.
// Lines and selection marks are generally delimited by newline character.
// Selection marks are represented in Markdown emoji syntax (:selected:, :unselected:).
"content": "CONTOSO LTD.\nINVOICE\nContoso Headquarters...", "pages": [ // List of pages analyzed
{
// Basic page metadata
"pageNumber": 1, // 1-indexed page number
"angle": 0, // Orientation of content in clockwise direction (degree)
"width": 0, // Page width
"height": 0, // Page height
"unit": "pixel", // Unit for width, height, and bounding box coordinates
"spans": [ // Parts of top-level content covered by page
{
"offset": 0, // Offset in content
"length": 7 // Length in content
}
], // List of words in page
"words": [
{
"text": "CONTOSO", // Equivalent to $.content.Substring(span.offset, span.length)
"boundingBox": [ ... ], // Position in page
"confidence": 0.99, // Extraction confidence
"span": { ... } // Part of top-level content covered by word
}, ...
], // List of selectionMarks in page
"selectionMarks": [
{
"state": "selected", // Selection state: selected, unselected
"boundingBox": [ ... ], // Position in page
"confidence": 0.95, // Extraction confidence
"span": { ... } // Part of top-level content covered by selection mark
}, ...
], // List of lines in page
"lines": [
{
"content": "CONTOSO LTD.", // Concatenated content of line (may contain both words and selectionMarks)
"boundingBox": [ ... ], // Position in page
"spans": [ ... ], // Parts of top-level content covered by line
}, ...
]
}, ...
], // List of extracted tables
"tables": [
{
"rowCount": 1, // Number of rows in table
"columnCount": 1, // Number of columns in table
"boundingRegions": [ // Bounding boxes potentially across pages covered by table
{
"pageNumber": 1, // 1-indexed page number
"boundingBox": [ ... ], // Bounding box
}
],
"spans": [ ... ], // Parts of top-level content covered by table // List of cells in table
"cells": [
{
"kind": "stub", // Cell kind: content (default), rowHeader, columnHeader, stub, description
"rowIndex": 0, // 0-indexed row position of cell
"columnIndex": 0, // 0-indexed column position of cell
"rowSpan": 1, // Number of rows spanned by cell (default=1)
"columnSpan": 1, // Number of columns spanned by cell (default=1)
"content": "SALESPERSON", // Concatenated content of cell
"boundingRegions": [ ... ], // Bounding regions covered by cell
"spans": [ ... ] // Parts of top-level content covered by cell
}, ...
]
}, ...
], // List of extracted key-value pairs
"keyValuePairs": [
{
"key": { // Extracted key
"content": "INVOICE:", // Key content
"boundingRegions": [ ... ], // Key bounding regions
"spans": [ ... ] // Key spans
},
"value": { // Extracted value corresponding to key, if any
"content": "INV-100", // Value content
"boundingRegions": [ ... ], // Value bounding regions
"spans": [ ... ] // Value spans
},
"confidence": 0.95 // Extraction confidence
}, ...
], // List of extracted entities
"entities": [
{
"category": "DateTime", // Primary entity category
"subCategory": "Date", // Secondary entity category
"content": "11/15/2019", // Entity content
"boundingRegions": [ ... ], // Entity bounding regions
"spans": [ ... ], // Entity spans
"confidence": 0.99 // Extraction confidence
}, ...
], // List of extracted styles
"styles": [
{
"isHandwritten": true, // Is content in this style handwritten?
"spans": [ ... ], // Spans covered by this style
"confidence": 0.95 // Detection confidence
}, ...
], // List of extracted documents
"documents": [
{
"docType": "prebuilt-invoice", // Classified document type (model dependent)
"boundingRegions": [ ... ], // Document bounding regions
"spans": [ ... ], // Document spans
"confidence": 0.99, // Document splitting/classification confidence // List of extracted fields
"fields": {
"VendorName": { // Field name (docType dependent)
"type": "string", // Field value type: string, number, array, object, ...
"valueString": "CONTOSO LTD.",// Normalized field value
"content": "CONTOSO LTD.", // Raw extracted field content
"boundingRegions": [ ... ], // Field bounding regions
"spans": [ ... ], // Field spans
"confidence": 0.99 // Extraction confidence
}, ...
}
}, ...
]
}



```

## <a name="build-or-train-model"></a>모델 빌드 또는 학습

모델 개체에는 새 API의 두 가지 업데이트가 있습니다.

* ```modelId``` 는 이제 사람이 읽을 수 있는 이름에 대해 모델에서 설정할 수 있는 속성입니다.
* ```modelName```는 ```description```로 이름이 변경되었습니다.

```build```작업을 호출하여 모델을 학습합니다. 요청 페이로드 및 호출 패턴은 변경되지 않은 상태로 유지됩니다. 빌드 작업은 모델 및 학습 데이터 세트를 지정하고 응답의 Operation-Location 헤더를 통해 결과를 반환합니다. GET 요청을 통해 이 모델 작업 URL을 폴링하여 빌드 작업의 상태를 확인합니다(요청 간의 최소 권장 간격은 1초임). v2.1과 달리 이 URL은 모델의 리소스 위치가 아닙니다. 대신 지정된 modelId에서 모델 URL을 생성할 수 있으며 응답의 resourceLocation 속성에서도 검색할 수 있습니다. 성공하면 상태가 로 ```succeeded``` 설정되고 결과에 사용자 지정 모델 정보가 포함됩니다. 오류가 발생하면 상태가 로 ```failed``` 설정되고 오류가 반환됩니다.

다음 코드는 SAS 토큰을 사용하는 샘플 빌드 요청입니다. 접두사 또는 폴더 경로를 설정하는 경우 후행 슬래시를 적어 둡다.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:build?api-version=2021-09-30-preview

{
  "modelId": {modelId},
  "description": "Sample model",
  "azureBlobSource": {
    "containerUrl": "https://{storageAccount}.blob.core.windows.net/{containerName}?{sasToken}",
    "prefix": "{folderName/}"
  }
}
```

## <a name="changes-to-compose-model"></a>모델 작성에 대한 변경 내용

이제 모델 작성이 단일 수준의 중첩으로 제한됩니다. 이제 구성된 모델은 및 속성을 추가하여 사용자 지정 ```modelId``` 모델과 ```description``` 일치합니다.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:compose?api-version=2021-09-30-preview
{
  "modelId": "{composedModelId}",
  "description": "{composedModelDescription}",
  "componentModels": [
    { "modelId": "{modelId1}" },
    { "modelId": "{modelId2}" },
  ]
}

```

## <a name="changes-to-copy-model"></a>모델 복사에 대한 변경 내용

복사 모델에 대한 호출 패턴은 변경되지 않습니다.

* 를 호출하는 대상 리소스를 통해 복사 작업에 권한을 ```authorizeCopy``` 부여합니다. 이제 POST 요청입니다.
* 원본 리소스에 권한 부여를 제출하여 모델 호출을 복사합니다. ```copy-to```
* 반환된 작업을 폴링하여 작업이 성공적으로 완료되었는지 확인합니다.

복사 모델 함수의 유일한 변경 사항은 다음과 같습니다.

* 의 HTTP 작업은 ```authorizeCopy``` 이제 POST 요청입니다.
* 권한 부여 페이로드에는 복사 요청을 제출하는 데 필요한 모든 정보가 포함됩니다.

***복사본에 권한 부여***

```json
POST https://{targetHost}/formrecognizer/documentModels:authorizeCopy?api-version=2021-09-30-preview
{
  "modelId": "{targetModelId}",
  "description": "{targetModelDescription}",
}
```

권한 부여 작업의 응답 본문을 사용하여 복사에 대한 요청을 생성합니다.

```json
POST https://{sourceHost}/formrecognizer/documentModels/{sourceModelId}:copy-to?api-version=2021-09-30-preview
{
  "targetResourceId": "{targetResourceId}",
  "targetResourceRegion": "{targetResourceRegion}",
  "targetModelId": "{targetModelId}",
  "targetModelLocation": "https://{targetHost}/formrecognizer/documentModels/{targetModelId}",
  "accessToken": "{accessToken}",
  "expirationDateTime": "2021-08-02T03:56:11Z"
}
```

## <a name="changes-to-list-models"></a>목록 모델에 대한 변경 내용

목록 모델은 이제 미리 작성되고 사용자 지정 모델을 반환하도록 확장되었습니다. 미리 붙는 모든 모델 이름은 로 ```prebuilt-``` 시작합니다. 성공 상태의 모델만 반환됩니다. 실패했거나 진행 중인 모델을 나열하려면 [작업 나열을 참조하세요.](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels)

***샘플 목록 모델 요청***

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels?api-version=2021-09-30-preview
```

## <a name="change-to-get-model"></a>모델을 얻으려면 변경

이제 get 모델에 미리 구성된 모델이 포함됨에 따라 get 작업은 ```docTypes``` 사전을 반환합니다. 각 문서 유형은 이름, 선택적 설명, 필드 스키마 및 선택적 필드 신뢰도로 설명됩니다. 필드 스키마는 문서 유형과 함께 반환될 수 있는 필드 목록을 설명합니다.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-09-30-preview
```

## <a name="new-get-info-operation"></a>새 정보 얻기 작업

```info```서비스에 대한 작업은 사용자 지정 모델 수 및 사용자 지정 모델 제한을 반환합니다.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/info? api-version=2021-09-30-preview
```

***샘플 응답***

```json
{
  "customDocumentModels": {
    "count": 5,
    "limit": 100
  }
}
```

## <a name="next-steps"></a>다음 단계

이 마이그레이션 가이드에서는 v3.0 API를 사용하도록 기존 Form Recognizer 애플리케이션을 업그레이드하는 방법을 배웠습니다. 모든 GA 기능에 2.1 API를 계속 사용하고 미리 보기 기능에 3.0 API를 사용합니다.

* [새 REST API 검토](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
* [Form Recognizer란?](overview.md)
* [Form Recognizer 빠른 시작](./quickstarts/try-sdk-rest-api.md)
