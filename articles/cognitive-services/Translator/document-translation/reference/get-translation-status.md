---
title: 번역 상태 가져오기
titleSuffix: Azure Cognitive Services
description: 번역 상태 가져오기 메서드는 문서 번역 요청의 상태를 반환합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: f892f9d5e681b7895f7e3d9dc053a9abf0ff00e1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061985"
---
# <a name="get-translation-status"></a>번역 상태 가져오기

번역 상태 가져오기 메서드는 문서 번역 요청의 상태를 반환합니다. 상태에는 전체 요청 상태와 해당 요청의 일부로 번역되는 문서의 상태가 포함됩니다.

## <a name="request-url"></a>요청 URL

다음에 `GET` 요청을 보냅니다.
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}
```

[사용자 지정 도메인 이름](../get-started-with-document-translation.md#find-your-custom-domain-name)을 찾는 방법에 대해 알아봅니다.

> [!IMPORTANT]
>
> * **문서 번역 서비스에 대한 모든 API 요청에는 사용자 지정 도메인 엔드포인트가 필요합니다.**
> * Azure Portal 리소스 _키 및 엔드포인트_ 페이지에 있는 엔드포인트나 전역 번역기 엔드포인트(`api.cognitive.microsofttranslator.com`)를 사용하여 문서 번역에 대한 HTTP 요청을 수행할 수 없습니다.


## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

|쿼리 매개 변수|필수|Description|
|--- |--- |--- |
|id|True|작업 ID입니다.|

## <a name="request-headers"></a>요청 헤더

요청 헤더는 다음과 같습니다.

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|필수 요청 헤더|

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|Description|
|--- |--- |
|200|OK. 성공적으로 요청하고 일괄 처리 번역 작업의 상태를 반환합니다. HeadersRetry-After: integerETag: string|
|401|권한이 없습니다. 자격 증명을 확인하세요.|
|404|리소스를 찾을 수 없습니다.|
|500|내부 서버 오류.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버를 일시적으로 사용할 수 없음</li></ul>|

## <a name="get-translation-status-response"></a>작업 상태 가져오기 응답

### <a name="successful-get-translation-status-response"></a>작업 상태 가져오기 성공적인 응답

성공적인 응답에서 반환되는 정보는 다음과 같습니다.

|Name|Type|Description|
|--- |--- |--- |
|id|문자열|작업의 ID입니다.|
|createdDateTimeUtc|문자열|작업을 만든 날짜와 시간입니다.|
|lastActionDateTimeUtc|문자열|작업 상태가 업데이트된 날짜와 시간입니다.|
|상태|String|작업 또는 문서의 가능한 상태 목록: <ul><li>취소됨</li><li>Cancelling</li><li>실패</li><li>NotStarted</li><li>실행 중</li><li>성공</li><li>ValidationFailed</li></ul>|
|요약|StatusSummary|세부 정보를 포함하는 요약은 아래와 같습니다.|
|summary.total|integer|총 개수입니다.|
|summary.failed|integer|실패 횟수입니다.|
|summary.success|integer|성공한 실행 수입니다.|
|summary.inProgress|integer|진행 중인 개수입니다.|
|summary.notYetStarted|integer|아직 시작되지 않은 개수입니다.|
|summary.cancelled|integer|취소된 개수입니다.|
|summary.totalCharacterCharged|integer|API로 청구되는 총 문자 수입니다.|

### <a name="error-response"></a>오류 응답

|Name|Type|설명|
|--- |--- |--- |
|code|문자열|간략한 오류 코드가 포함된 열거형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|간략한 오류 메시지를 가져옵니다.|
|대상|문자열|오류의 출처입니다. 예를 들어 유효하지 않은 문서의 경우 "문서" 또는 "문서 ID"가 됩니다.|
|innerError|InnerTranslationError|Cognitive Services API 지침을 준수하는 새로운 내부 오류 형식입니다. 여기에는 필수 속성 ErrorCode, 메시지 및 선택적 속성 대상, 세부 정보(키 값 쌍), 내부 오류(중첩될 수 있음)가 포함됩니다.|
|innerError.code|문자열|코드 오류 문자열을 가져옵니다.|
|innerError.message|문자열|간략한 오류 메시지를 가져옵니다.|
|innerError.target|문자열|오류 출처를 가져옵니다. 예를 들어 잘못된 문서의 경우 "documents" 또는 "document id"가 됩니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예

다음 JSON 개체는 성공적인 응답의 예입니다.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>오류 응답 예

다음 JSON 객체는 오류 응답의 예입니다. 다른 오류 코드에 대한 스키마는 동일합니다.

상태 코드: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

문서 번역 및 클라이언트 라이브러리를 사용하는 방법에 대해 자세히 알아보려면 빠른 시작을 따르세요.

> [!div class="nextstepaction"]
> [문서 번역 시작](../get-started-with-document-translation.md)
