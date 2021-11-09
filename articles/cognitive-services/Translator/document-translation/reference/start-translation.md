---
title: 번역 시작
titleSuffix: Azure Cognitive Services
description: 문서 번역 서비스를 사용하는 문서 번역 요청을 시작합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.openlocfilehash: eaa12d4fff9331f5170a2a7b7d28b5ebb4d06a74
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061006"
---
# <a name="start-translation"></a>번역 시작

이 API로 문서 번역 서비스를 사용하는 번역 요청을 시작합니다. 개별 요청에 여러 문서가 포함될 수 있으며 각 문서에 대한 원본 및 대상 컨테이너가 포함되어야 합니다.

접두사 및 접미사 필터(제공된 경우)는 폴더를 필터링하는 데 사용됩니다. 접두사는 컨테이너 이름 뒤의 하위 경로에 적용됩니다.

용어집/번역 메모리가 요청에 포함될 수 있으며 문서 번역 시 이 용어집/번역 메모리가 서비스에서 적용됩니다.

용어집이 잘못되었거나 번역 중에 연결할 수 없는 경우 문서 상태에 오류가 표시됩니다. 이름이 같은 파일이 대상에 이미 있으면 작업이 실패합니다. 각 대상 언어의 targetUrl은 고유해야 합니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
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

## <a name="request-body-batch-submission-request"></a>요청 본문: 일괄 처리 제출 요청

|Name|Type|Description|
|--- |--- |--- |
|입력|BatchRequest[]|BatchRequest는 아래와 같습니다. 문서 또는 문서가 포함된 폴더의 입력 목록입니다. 미디어 유형에는 “application/json”, “text/json”, “application/*+json”이 있습니다.|

### <a name="inputs"></a>입력

입력 일괄 처리 번역 요청에 대한 정의입니다.

|Name|Type|필수|Description|
|--- |--- |--- |--- |
|source|SourceInput[]|True|inputs.source는 아래와 같습니다. 입력 문서의 원본입니다.|
|storageType|StorageInputType[]|거짓|inputs.storageType은 아래와 같습니다. 입력 문서 원본 문자열의 스토리지 유형입니다. 단일 문서 변환에만 필요합니다.|
|대상|TargetInput[]|True|inputs.target은 아래와 같습니다. 출력 대상의 위치입니다.|

**inputs.source**

입력 문서의 원본입니다.

|Name|Type|필수|Description|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|거짓|DocumentFilter[]는 아래와 같습니다.|
|filter.prefix|문자열|거짓|번역을 위해 원본 경로의 문서를 필터링하며 대/소문자를 구분하는 접두사 문자열입니다. 예를 들어 Azure Storage Blob URI를 사용하는 경우 접두사를 사용하여 번역할 하위 폴더를 제한합니다.|
|filter.suffix|문자열|거짓|번역을 위해 원본 경로의 문서를 필터링하며 대/소문자를 구분하는 접미사 문자열입니다. 대부분 파일 확장명에 사용됩니다.|
|언어|문자열|거짓|언어 코드를 지정하지 않으면 문서에서 자동 검색을 수행합니다.|
|sourceUrl|문자열|True|문서가 있는 폴더/컨테이너 또는 단일 파일의 위치입니다.|
|storageSource|StorageSource|거짓|StorageSource는 아래와 같습니다.|
|storageSource.AzureBlob|문자열|거짓||

**inputs.storageType**

입력 문서 원본 문자열의 스토리지 유형입니다.

|Name|유형|
|--- |--- |
|파일|문자열|
|폴더|문자열|

**inputs.target**

완성된 번역 문서의 대상입니다.

|Name|Type|필수|Description|
|--- |--- |--- |--- |
|category|문자열|거짓|번역 요청을 위한 범주/사용자 지정 시스템입니다.|
|glossaries|Glossary[]|거짓|Glossary는 아래와 같습니다. 용어집의 목록입니다.|
|glossaries.format|문자열|거짓|형식.|
|glossaries.glossaryUrl|문자열|True(용어집을 사용할 경우)|용어집의 위치입니다. 서식 매개 변수가 제공되지 않으면 파일 확장명을 사용하여 서식을 추출합니다. 용어집에 번역 언어 쌍이 없으면 적용되지 않습니다.|
|glossaries.storageSource|StorageSource|거짓|StorageSource는 위와 같습니다.|
|glossaries.version|문자열|거짓|선택적 버전입니다. 지정하지 않으면 기본값이 사용됩니다.|
|targetUrl|문자열|True|문서가 있는 폴더/컨테이너의 위치입니다.|
|언어|문자열|True|두 문자로 된 대상 언어 코드입니다. [언어 코드 목록](../../language-support.md)을 참조하세요.|
|storageSource|StorageSource []|거짓|StorageSource []는 위와 같습니다.|

## <a name="example-request"></a>요청 예

다음은 일괄 처리 요청의 예입니다.

**컨테이너의 모든 문서 번역**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**용어집을 적용하여 컨테이너의 모든 문서 번역**

컨테이너가 아닌 특정 Blob/문서용 용어집 URL 및 SAS 토큰을 만들었는지 확인합니다.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr",
                    "glossaries": [
                        {
                            "glossaryUrl": "https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D",
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**컨테이너의 특정 폴더 번역**

SAS 토큰은 컨테이너용이지만 폴더 이름(대/소문자 구분)을 필터의 접두사로 지정했는지 확인합니다.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D",
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**컨테이너의 특정 문서 번역**

* “storageType”: “File”을 지정했는지 확인합니다.
* 특정 Blob/문서(컨테이너 아님)에 대한 원본 URL 및 SAS 토큰을 생성했는지 확인합니다.
* SAS 토큰은 컨테이너용이지만 대상 URL의 일부로 대상 파일 이름을 지정했는지 확인합니다.
* 아래 샘플 요청은 두 개의 대상 언어로 번역되는 단일 문서를 보여줍니다.

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|Description|
|--- |--- |
|202|수락됨 서비스에 의해 성공적인 요청 및 일괄 처리 요청이 만들어졌습니다. 헤더 Operation-Location은 작업 ID.HeadersOperation-Location: string을 통해 상태 URL을 나타냅니다.|
|400|잘못된 요청. 잘못된 요청입니다. 입력 매개 변수를 확인합니다.|
|401|권한이 없습니다. 자격 증명을 확인하세요.|
|429|요청 속도가 너무 높습니다.|
|500|내부 서버 오류.|
|503|서비스를 현재 사용할 수 없습니다.  나중에 다시 시도하세요.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버를 일시적으로 사용할 수 없음</li></ul>|

## <a name="error-response"></a>오류 응답

|Name|Type|설명|
|--- |--- |--- |
|code|문자열|간략한 오류 코드가 포함된 열거형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|간략한 오류 메시지를 가져옵니다.|
|innerError|InnerTranslationError|Cognitive Services API 지침을 준수하는 새로운 내부 오류 형식입니다. 여기에는 필수 속성 ErrorCode, 메시지 및 선택적 속성 대상, 세부 정보(키 값 쌍), 내부 오류(중첩될 수 있음)가 포함됩니다.|
|inner.Errorcode|문자열|코드 오류 문자열을 가져옵니다.|
|innerError.message|문자열|간략한 오류 메시지를 가져옵니다.|
|innerError.target|문자열|오류 출처를 가져옵니다. 예를 들어 잘못된 문서의 경우 "documents" 또는 "document id"가 됩니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예제

성공적인 응답에서 반환되는 정보는 다음과 같습니다.

POST 메서드 응답 헤더 Operation-Location URL 값에서 작업 ID를 찾을 수 있습니다. URL의 마지막 매개 변수는 작업(operation)의 작업(job) ID(“/operation/” 뒤에 오는 문자열)입니다.

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>오류 응답 예제

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

문서 번역 및 클라이언트 라이브러리를 사용하는 방법에 대해 자세히 알아보려면 빠른 시작을 따르세요.

> [!div class="nextstepaction"]
> [문서 번역 시작](../get-started-with-document-translation.md)
