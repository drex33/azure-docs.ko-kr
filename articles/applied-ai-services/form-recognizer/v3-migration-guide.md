---
title: '방법: 폼 인식기 v 2.1에서 v 3.0으로 응용 프로그램을 마이그레이션합니다.'
titleSuffix: Azure Applied AI Services
description: 이 방법에서는 새 버전의 API로 이동 하기 위해 변경 해야 하는 양식 인식기 API의 v 2.1과 v 3.0 간의 차이점에 대해 알아봅니다.
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: vikurpad
recommendations: false
ms.openlocfilehash: 019756369ec80a30eb9f6cfb33301dbf820cf5d6
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717701"
---
# <a name="form-recognizer-v30-migration--preview"></a>폼 인식기 v 3.0 마이그레이션 | 미리 보기

> [!IMPORTANT]
>
> REST API v 3.0의 폼 인식기에는 REST API 요청 및 분석 응답 JSON에 대 한 주요 변경 내용이 도입 되었습니다.

양식 인식기 v 3.0 (미리 보기)에는 몇 가지 새로운 기능 및 기능이 도입 되었습니다.

* 더 나은 유용성을 위해 [양식 인식기 REST API](quickstarts/try-v3-rest-api.md) 다시 디자인 되었습니다.
* [**일반 문서 (v 3.0)**](concept-general-document.md) 모델은 폼과 문서에서 텍스트, 테이블, 구조, 키-값 쌍 및 명명 된 엔터티를 추출 하는 새로운 API입니다.
* [**수신 (v 3.0)**](concept-receipt.md) 모델은 단일 페이지 호텔 수신 처리를 지원 합니다.
* [**ID 문서 (v 3.0)**](concept-id-document.md) 모델은 미국 드라이버 라이선스에서 인증, 제한 및 차량 분류 추출을 지원 합니다.
* [**사용자 지정 모델 API (v 3.0)**](concept-custom.md) 는 사용자 지정 폼에 대 한 서명 검색을 지원 합니다.

이 문서에서는 폼 인식기 v 2.1과 v 3.0 간의 차이점 및 최신 버전의 API로 이동 하는 방법에 대해 알아봅니다.

## <a name="changes-to-the-rest-api-endpoints"></a>REST API 끝점에 대 한 변경 내용

 V 3.0 REST API 레이아웃 분석, 미리 작성 된 모델 및 사용자 지정 모델에 대 한 분석 작업을 **`documentModels`**  **`modelId`** 레이아웃 분석 (미리 작성 된 레이아웃) 및 미리 작성 된 모델에 할당 하 여 단일 작업 쌍으로 결합 합니다.

### <a name="post-request"></a>POST 요청

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview

```

### <a name="get-request"></a>GET 요청

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}/AnalyzeResult/{resultId}?api-version=2021-07-30-preview
```

### <a name="analyze-operation"></a>작업 분석

* 요청 페이로드 및 호출 패턴은 변경 되지 않은 상태로 유지 됩니다.
* 분석 작업은 입력 문서 및 콘텐츠 관련 구성을 지정 하 고 응답의 Operation-Location 헤더를 통해 분석 결과 URL을 반환 합니다.
* GET 요청을 통해이 분석 결과 URL을 폴링하여 분석 작업 상태를 확인 합니다. 요청 간 최소 권장 간격은 1 초입니다.
* 성공 하면 상태가 succeeded로 설정 되 고 응답 본문에 [analyzeResult](#changes-to-analyze-result) 이 반환 됩니다. 오류가 발생 하면 status가 failed로 설정 되 고 오류가 반환 됩니다.

| 모델 | v2.1 | v3.0 |
|:--| :--| :--|
| **요청 URL 접두사**| **https://{폼 내 인식자-끝점}/폼 인식기/v 2.1**  | **https://{폼 내 인식자-끝점}/폼 인식기** |
|🆕 **일반 문서**|N/A|/documentModels/prebuilt-document: 분석 |
| **레이아웃**| /layout/analyze |/documentModels/prebuilt-layout: 분석|
|**사용자 지정**| /Scustom/sd    |/documentModels/{modelId}: 분석 |
| **청구서** | /prebuilt/invoice/analyze    | /documentModels/prebuilt-invoice: 분석 |
| **Receipt** | /prebuilt/receipt/analyze    | /documentModels/prebuilt-receipt: 분석 |
| **ID 문서** | /prebuilt/idDocument/analyze |  /documentModels/prebuilt-idDocument: 분석 |
|**명함**| /prebuilt/businessCard/analyze| /documentModels/prebuilt-businessCard: 분석|

### <a name="analyze-request-body"></a>요청 본문 분석

분석할 콘텐츠는 요청 본문을 통해 제공 됩니다. URL 또는 b a s e 64로 인코딩된 데이터는 사용자가 요청을 생성할 수 있습니다.

  공개적으로 액세스할 수 있는 웹 URL을 지정 하려면 콘텐츠 형식을 **application/json** 으로 설정 하   고 다음 json 본문을 보냅니다.

  ```json
  {
    "urlSource": "{urlPath}"
  }
  ```

Base64 인코딩은 Recognzer v 3.0 양식 에서도 지원 됩니다.

```json
{
  "base64Source": "{base64EncodedContent}"
}
```

### <a name="additional-parameters"></a>추가 매개 변수

계속 지원 되는 매개 변수:

* 페이지
* locale

더 이상 지원 되지 않는 매개 변수: 

* includeTextDetails

새 응답 형식은 더 간결 하 고 전체 출력이 항상 반환 됩니다.

## <a name="changes-to-analyze-result"></a>분석 결과에 대 한 변경 내용

분석 응답이 다중 페이지 요소를 지원 하기 위해 다음과 같은 최상위 수준 결과로 리팩터링 되었습니다.
* 페이지
* 테이블
* keyValuePairs
* 엔터티
* 스타일
* 문서

> [!NOTE]
>
> AnalyzeResult response 변경 내용에는 페이지 속성에서 analyzeResult 내의 top 레버 속성으로 이동 하는 것과 같은 다양 한 변경 내용이 포함 됩니다.


## <a name="build-or-train-model"></a>모델 빌드 또는 학습

모델 개체에 새 API의 두 업데이트가 있습니다.
* ```modelId``` 는 이제 사용자가 읽을 수 있는 이름에 대해 모델에 설정할 수 있는 속성입니다.
* ```modelName```는 ```description```로 이름이 변경되었습니다.

```build```모델을 학습 하기 위해 작업이 호출 됩니다. 요청 페이로드 및 호출 패턴은 변경 되지 않은 상태로 유지 됩니다. 빌드 작업은 모델 및 학습 데이터 집합을 지정 하 고 응답의 Operation-Location 헤더를 통해 결과를 반환 합니다. GET 요청을 통해이 모델 작업 URL을 폴링하여 빌드 작업의 상태를 확인 합니다. 요청 간의 최소 권장 간격은 1 초입니다. V 2.1과 달리이 URL은 모델의 리소스 위치가 아닙니다. 대신, 지정 된 modelId에서 모델 URL을 생성할 수 있습니다 .이 경우 응답의 resourceLocation 속성 에서도 검색 됩니다. 성공 하면 상태가로 설정 되 ```succeeded``` 고 결과에 사용자 지정 모델 정보가 포함 됩니다. 오류가 발생 하면 상태가로 설정 되 ```failed``` 고 오류가 반환 됩니다.

SAS 토큰을 사용 하는 샘플 빌드 요청입니다. 접두사 또는 폴더 경로를 설정할 때 후행 슬래시를 적어둡니다.

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
## <a name="changes-to-compose-model"></a>작성 모델의 변경 내용

이제 모델 작성은 단일 중첩 수준으로 제한 됩니다. 구성 된 모델은 이제 및 속성을 추가 하 여 사용자 지정 모델과 일치 ```modelId``` ```description``` 합니다.

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

## <a name="changes-to-copy-model"></a>모델 복사에 대 한 변경 내용

모델 복사에 대 한 호출 패턴은 변경 되지 않은 상태로 유지 됩니다.

* 을 호출 하는 대상 리소스를 사용 하 여 복사 작업에 권한을 부여 ```authorizeCopy``` 합니다. 이제 POST 요청입니다.
* 소스 리소스에 권한 부여를 제출 하 여를 호출 하는 모델을 복사 합니다. ```copy-to```
* 반환 된 작업을 폴링하여 작업이 성공적으로 완료 되었는지 확인 합니다.

모델 복사 함수도 다음과 같이 변경 됩니다.

* 이제에 대 한 HTTP 동작이 ```authorizeCopy``` POST 요청입니다.
* 권한 부여 페이로드에는 복사 요청을 제출 하는 데 필요한 모든 정보가 포함 되어 있습니다.

복사본에 권한을 부여 합니다.
```json
POST https://{targetHost}/formrecognizer/documentModels:authorizeCopy?api-version=2021-09-30-preview
{
  "modelId": "{targetModelId}",
  "description": "{targetModelDescription}",
}
```
권한 부여 작업에서 응답 본문을 사용 하 여 복사본에 대 한 요청을 생성 합니다.

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

## <a name="changes-to-list-models"></a>목록 모델의 변경 내용

목록 모델은 이제 미리 작성 된 모델 및 사용자 지정 모델을 반환 하도록 확장 되었습니다. 미리 작성 된 모든 모델 이름은로 시작 ```prebuilt-``` 합니다. 상태가 succeeded 인 모델만 반환 됩니다. 실패 했거나 진행 중인 모델을 나열 하려면 [작업 나열](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels)을 참조 하세요.

샘플 목록 모델 요청
```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels?api-version=2021-09-30-preview
```

## <a name="change-to-get-model"></a>모델 가져오기로 변경

이제 get model에는 미리 빌드된 모델이 포함 되어 있으므로 get 작업은 사전을 반환 합니다 ```docTypes``` . 각 문서 유형은 이름, 선택적 설명, 필드 스키마 및 선택적 필드 신뢰도로 설명 됩니다. 필드 스키마는 문서 유형과 함께 반환 될 수 있는 필드 목록을 설명 합니다.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-09-30-preview
```

## <a name="new-get-info-operation"></a>새 정보 가져오기 작업
```info```서비스에 대 한 작업은 사용자 지정 모델 수 및 사용자 지정 모델 제한을 반환 합니다.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/info? api-version=2021-09-30-preview
```
샘플 응답
```json
{
  "customDocumentModels": {
    "count": 5,
    "limit": 100
  }
}
```

## <a name="next-steps"></a>다음 단계

이 마이그레이션 가이드에서는 기존 양식 인식기 응용 프로그램을 업그레이드 하 여 v 3.0 Api를 사용 하는 방법을 알아보았습니다. 모든 GA 기능에 대해 2.1 API를 계속 사용 하 고 미리 보기 기능 중 하나를 사용 하는 경우 3.0 API를 사용 합니다.

* [새 REST API 검토](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
* [Form Recognizer란?](overview.md)
* [Form Recognizer 빠른 시작](quickstarts/client-library.md)
