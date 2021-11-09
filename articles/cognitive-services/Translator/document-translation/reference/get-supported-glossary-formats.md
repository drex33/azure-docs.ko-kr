---
title: 지원되는 용어집 형식 가져오기 메서드
titleSuffix: Azure Cognitive Services
description: 지원되는 용어집 형식 가져오기 메서드는 지원되는 용어집 형식 목록을 반환합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: 7e81f333b9291d58bd014f91a9d22b38d844466b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057255"
---
# <a name="get-supported-glossary-formats"></a>지원되는 용어집 형식 가져오기

용어집 형식 가져오기 메서드는 문서 번역 서비스에서 지원되는 용어집 형식 목록을 반환합니다. 이 목록에는 사용되는 공통 파일 확장명이 포함됩니다.

## <a name="request-url"></a>요청 URL

다음에 `GET` 요청을 보냅니다.
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/glossaries/formats
```

[사용자 지정 도메인 이름](../get-started-with-document-translation.md#find-your-custom-domain-name)을 찾는 방법에 대해 알아봅니다.

> [!IMPORTANT]
>
> * **문서 번역 서비스에 대한 모든 API 요청에는 사용자 지정 도메인 엔드포인트가 필요합니다.**
> * Azure Portal 리소스 _키 및 엔드포인트_ 페이지에 있는 엔드포인트나 전역 번역기 엔드포인트(`api.cognitive.microsofttranslator.com`)를 사용하여 문서 번역에 대한 HTTP 요청을 수행할 수 없습니다.

## <a name="request-headers"></a>요청 헤더

요청 헤더는 다음과 같습니다.

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|필수 요청 헤더|

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|Description|
|--- |--- |
|200|OK. 지원되는 용어집 파일 형식 목록을 반환합니다.|
|500|내부 서버 오류.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버를 일시적으로 사용할 수 없음</li></ul>|


## <a name="get-supported-glossary-formats-response"></a>지원되는 용어집 형식 가져오기 응답

지원되는 용어집 형식 가져오기 API의 목록 반환을 위한 기본 유형입니다.

### <a name="successful-get-supported-glossary-formats-response"></a>지원되는 용어집 형식 가져오기 성공적인 응답

지원되는 용어집 형식 가져오기 API의 목록 반환을 위한 기본 유형입니다.

|속성|Type|Description|
|--- |--- |--- |
|값|FileFormat []|FileFormat[]에는 아래 나열된 세부 정보가 포함되어 있습니다.|
|value.contentTypes|string []|이 형식에 지원되는 콘텐츠 형식입니다.|
|value.defaultVersion|문자열|지정되지 않은 경우 기본 버전|
|value.fileExtensions|string []| 이 형식에 지원되는 파일 확장명입니다.|
|value.format|문자열|형식 이름입니다.|
|value.versions|string []| 지원되는 버전입니다.|

### <a name="error-response"></a>오류 응답

|Name|Type|설명|
|--- |--- |--- |
|code|문자열|간략한 오류 코드가 포함된 열거형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|간략한 오류 메시지를 가져옵니다.|
|innerError|InnerTranslationError|Cognitive Services API 지침을 준수하는 새로운 내부 오류 형식입니다. 여기에는 필수 속성 ErrorCode, 메시지 및 선택적 속성 대상, 세부 정보(키 값 쌍), 내부 오류(중첩될 수 있음)가 포함됩니다.|
|innerError.code|문자열|코드 오류 문자열을 가져옵니다.|
|innerError.message|문자열|간략한 오류 메시지를 가져옵니다.|
|innerError.target|문자열|오류 출처를 가져옵니다. 예를 들어 잘못된 문서의 경우 "documents" 또는 "document id"가 됩니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예

다음은 성공한 응답 예제입니다.

```JSON
{
    "value": [
        {
            "format": "XLIFF",
            "fileExtensions": [
                ".xlf"
            ],
            "contentTypes": [
                "application/xliff+xml"
            ],
            "defaultVersion": "1.2",
            "versions": [
                "1.0",
                "1.1",
                "1.2"
            ]
        },
        {
            "format": "TSV",
            "fileExtensions": [
                ".tsv",
                ".tab"
            ],
            "contentTypes": [
                "text/tab-separated-values"
            ]
        },
        {
            "format": "CSV",
            "fileExtensions": [
                ".csv"
            ],
            "contentTypes": [
                "text/csv"
            ]
        }
    ]
}

```

### <a name="example-error-response"></a>오류 응답 예
다음은 오류 응답의 예입니다. 다른 오류 코드에 대한 스키마는 동일합니다.

상태 코드: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
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
