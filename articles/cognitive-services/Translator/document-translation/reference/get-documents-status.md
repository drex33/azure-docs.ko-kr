---
title: 문서 상태 가져오기
titleSuffix: Azure Cognitive Services
description: 문서 상태 가져오기 메서드는 일괄 처리 문서 번역 요청의 모든 문서에 대한 상태를 반환합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: 4da15c603028524c0501819c0ecbc3884080ca6f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053465"
---
# <a name="get-documents-status"></a>문서 상태 가져오기

응답의 문서 수가 페이징 제한을 초과하면 서버 측 페이징이 사용됩니다. 페이지가 매겨진 응답은 부분 결과를 나타내며 응답에 연속 토큰을 포함합니다. 연속 토큰이 없다는 것은 다른 페이지를 사용할 수 없음을 의미합니다.

$top, $skip 및 $maxpagesize 쿼리 매개 변수를 사용하여 반환할 결과 수와 컬렉션 오프셋을 지정할 수 있습니다.

$top은 사용자가 모든 페이지에서 반환하려는 총 레코드 수를 나타냅니다. $skip은 지정된 정렬 방법에 따라 서버가 보유한 문서 상태 목록에서 건너뛸 레코드 수를 나타냅니다. 기본적으로는 시작 시간을 내림차순으로 정렬합니다. $maxpagesize는 한 페이지에서 반환되는 최대 항목입니다. $top을 통해 추가 항목을 요청하는 경우(또는 $top이 지정되지 않고 반환될 항목이 더 많은 경우) @nextLink에는 다음 페이지로 연결되는 링크가 포함됩니다.

$orderBy 쿼리 매개 변수를 사용하여 반환된 목록을 정렬할 수 있습니다(예: "$orderBy=createdDateTimeUtc asc" 또는 "$orderBy=createdDateTimeUtc desc"). 기본 정렬은 createdDateTimeUtc를 기준으로 내림차순입니다. 일부 쿼리 매개 변수는 반환된 목록을 필터링하는 데 사용할 수 있습니다(예: "status=Succeeded,Cancelled"는 성공 및 취소된 문서만 반환함). createdDateTimeUtcStart 및 createdDateTimeUtcEnd를 조합하거나 개별적으로 사용하여 반환된 목록을 필터링할 날짜/시간 범위를 지정할 수 있습니다. 지원되는 필터링 쿼리 매개 변수는 (status, IDs, createdDateTimeUtcStart, createdDateTimeUtcEnd)입니다.

$top 및 $skip이 모두 포함된 경우 서버는 먼저 $skip을 적용한 다음, 컬렉션에 $top을 적용해야 합니다. 

> [!NOTE]
> 서버에서 $top 및/또는 $skip을 준수할 수 없는 경우 서버는 쿼리 옵션을 무시하는 대신 이에 대한 정보를 클라이언트에게 알려주는 오류를 반환해야 합니다. 이렇게 하면 반환된 데이터에 대해 클라이언트가 가정할 위험이 줄어듭니다.

## <a name="request-url"></a>요청 URL

다음에 `GET` 요청을 보냅니다.
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}/documents
```

[사용자 지정 도메인 이름](../get-started-with-document-translation.md#find-your-custom-domain-name)을 찾는 방법에 대해 알아봅니다.

> [!IMPORTANT]
>
> * **문서 번역 서비스에 대한 모든 API 요청에는 사용자 지정 도메인 엔드포인트가 필요합니다.**
> * Azure Portal 리소스 _키 및 엔드포인트_ 페이지에 있는 엔드포인트나 전역 번역기 엔드포인트(`api.cognitive.microsofttranslator.com`)를 사용하여 문서 번역에 대한 HTTP 요청을 수행할 수 없습니다.

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

|쿼리 매개 변수|In(다음 안에)|필수|Type|Description|
|--- |--- |--- |--- |--- |
|id|path|True|문자열|작업 ID입니다.|
|$maxpagesize|Query|False|정수 int32|$maxpagesize는 한 페이지에서 반환되는 최대 항목입니다. $top을 통해 추가 항목을 요청하는 경우(또는 $top이 지정되지 않고 반환될 항목이 더 많은 경우) @nextLink에는 다음 페이지로 연결되는 링크가 포함됩니다. 클라이언트는 $maxpagesize 기본 설정을 지정하여 특정 페이지 크기의 서버 기반 페이징을 요청할 수 있습니다. 지정된 페이지 크기가 서버의 기본 페이지 크기보다 작은 경우 서버는 이 기본 설정을 준수해야 합니다.|
|$orderBy|Query|False|array|컬렉션에 대한 정렬 쿼리(예: 'CreatedDateTimeUtc asc', 'CreatedDateTimeUtc desc').|
|$skip|Query|False|정수 int32|$skip은 지정된 정렬 방법에 따라 서버가 보유한 레코드 목록에서 건너뛸 레코드 수를 나타냅니다. 기본적으로는 시작 시간을 내림차순으로 정렬합니다. 클라이언트는 $top 및 $skip 쿼리 매개 변수를 사용하여 반환할 결과 수와 컬렉션 오프셋을 지정할 수 있습니다. 클라이언트에서 $top 및 $skip을 모두 제공하는 경우 서버는 먼저 $skip을 적용한 다음, 컬렉션에 $top을 적용해야 합니다. 참고: 서버에서 $top 및/또는 $skip을 준수할 수 없는 경우 서버는 쿼리 옵션을 무시하는 대신 이에 대한 정보를 클라이언트에게 알려주는 오류를 반환해야 합니다.|
|$top|Query|False|정수 int32|$top은 사용자가 모든 페이지에서 반환하려는 총 레코드 수를 나타냅니다. 클라이언트는 $top 및 $skip 쿼리 매개 변수를 사용하여 반환할 결과 수와 컬렉션 오프셋을 지정할 수 있습니다. 클라이언트에서 $top 및 $skip을 모두 제공하는 경우 서버는 먼저 $skip을 적용한 다음, 컬렉션에 $top을 적용해야 합니다. 참고: 서버에서 $top 및/또는 $skip을 준수할 수 없는 경우 서버는 쿼리 옵션을 무시하는 대신 이에 대한 정보를 클라이언트에게 알려주는 오류를 반환해야 합니다.|
|createdDateTimeUtcEnd|Query|False|문자열 날짜-시간|이전 항목을 가져올 종료 날짜/시간입니다.|
|createdDateTimeUtcStart|Query|False|문자열 날짜-시간|이후 항목을 가져올 시작 날짜/시간입니다.|
|ids|Query|False|array|필터링에 사용할 ID입니다.|
|statuses|Query|False|array|필터링에 사용할 상태입니다.|

## <a name="request-headers"></a>요청 헤더

요청 헤더는 다음과 같습니다.

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|필수 요청 헤더|

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|Description|
|--- |--- |
|200|OK. 성공적으로 요청하고 문서의 상태를 반환합니다. HeadersRetry-After: integerETag: string|
|400|잘못된 요청입니다. 입력 매개 변수를 확인합니다.|
|401|권한이 없습니다. 자격 증명을 확인하세요.|
|404|리소스를 찾을 수 없습니다.|
|500|내부 서버 오류.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버를 일시적으로 사용할 수 없음</li></ul>|


## <a name="get-documents-status-response"></a>문서 상태 가져오기 응답

### <a name="successful-get-documents-status-response"></a>문서 상태 가져오기 성공적인 응답

성공적인 응답에서 반환되는 정보는 다음과 같습니다.

|Name|Type|Description|
|--- |--- |--- |
|@nextLink|문자열|다음 페이지의 URL입니다. 사용 가능한 페이지가 더 이상 없으면 Null입니다.|
|값|DocumentStatus []|아래 나열된 개별 문서의 세부 상태입니다.|
|value.path|문자열|문서 또는 폴더의 위치입니다.|
|value.sourcePath|문자열|원본 문서의 위치입니다.|
|value.createdDateTimeUtc|문자열|작업을 만든 날짜와 시간입니다.|
|value.lastActionDateTimeUtc|문자열|작업 상태가 업데이트된 날짜/시간입니다.|
|value.status|상태|작업 또는 문서의 가능한 상태 목록입니다.<ul><li>취소됨</li><li>Cancelling</li><li>실패</li><li>NotStarted</li><li>실행 중</li><li>성공</li><li>ValidationFailed</li></ul>|
|value.to|문자열|대상 언어입니다.|
|value.progress|number|사용 가능한 경우 번역의 진행률입니다.|
|value.id|문자열|문서 ID입니다.|
|value.characterCharged|integer|API로 청구되는 문자 수입니다.|

### <a name="error-response"></a>오류 응답

|Name|Type|설명|
|--- |--- |--- |
|code|문자열|간략한 오류 코드가 포함된 열거형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|간략한 오류 메시지를 가져옵니다.|
|대상|문자열|오류 출처를 가져옵니다. 예를 들어 잘못된 문서의 경우 "documents" 또는 "document ID"가 됩니다.|
|innerError|InnerTranslationError|Cognitive Services API 지침을 준수하는 새로운 내부 오류 형식입니다. 여기에는 필수 속성 ErrorCode, 메시지 및 선택적 속성 대상, 세부 정보(키 값 쌍), 내부 오류(중첩될 수 있음)가 포함됩니다.|
|innerError.code|문자열|코드 오류 문자열을 가져옵니다.|
|innerError.message|문자열|간략한 오류 메시지를 가져옵니다.|
|innerError.target|문자열|오류 출처를 가져옵니다. 예를 들어 잘못된 문서가 있는 경우 "documents" 또는 "document id"가 됩니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예

다음은 성공한 응답 예제입니다.

```JSON
{
  "value": [
    {
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "sourcePath": "https://myblob.blob.core.windows.net/sourceContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
}
```

### <a name="example-error-response"></a>오류 응답 예

다음은 오류 응답 예제입니다. 다른 오류 코드에 대한 스키마는 동일합니다.

상태 코드: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

문서 번역 및 클라이언트 라이브러리를 사용하는 방법에 대해 자세히 알아보려면 빠른 시작을 따르세요.

> [!div class="nextstepaction"]
> [문서 번역 시작](../get-started-with-document-translation.md)
