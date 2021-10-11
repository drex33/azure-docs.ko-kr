---
title: '참조: 폼 인식기 오류 (2021-09-30-미리 보기)'
titleSuffix: Azure Applied AI Services
description: 폼 인식기에서 오류가 표시 되는 방법에 대해 알아보고 서비스에서 반환 되는 가능한 오류 목록을 찾습니다.
author: paulhsu
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/30/2021
ms.author: paulhsu
ms.custom: cog-serv-seo-aug-2020
keywords: 문서 처리
ms.openlocfilehash: a67c98189198cf8df0f8f7e6fa88c9a552812bfa
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129731933"
---
# <a name="form-recognizer-error-guide-v30-preview"></a>폼 인식기 오류 가이드 v 3.0 (미리 보기)

양식 인식기는 통합 된 디자인을 사용 하 여 REST Api에서 발생 한 모든 오류를 나타냅니다.  API 작업에서 4xx 또는 5xx 상태 코드를 반환할 때마다 다음과 같이 오류에 대 한 추가 정보가 응답 JSON 본문에서 반환 됩니다.

```json
{
  "error": {
    "code": "InvalidRequest",
    "message": "Invalid request.",
    "innererror": {
      "code": "InvalidContent",
      "message": "The file format is unsupported or corrupted. Refer to documentation for the list of supported formats."
    }
  }
}
```

여러 오류가 발생할 수 있는 장기 실행 작업의 경우 최상위 오류 코드가 가장 심각한 오류로 설정 되 고 *오류. details* 속성에 나열 된 개별 오류가 발생 합니다.  이러한 시나리오에서 각 개별 오류의 *target* 속성은 오류의 트리거를 지정 합니다.

```json
{
    "status": "failed",
    "createdDateTime": "2021-07-14T10:17:51Z",
    "lastUpdatedDateTime": "2021-07-14T10:17:51Z",
    "error": {
        "code": "InternalServerError",
        "message": "An unexpected error occurred.",
        "details": [
            {
                "code": "InternalServerError",
                "message": "An unexpected error occurred."
            },
            {
                "code": "InvalidContentDimensions",
                "message": "The input image dimensions are out of range. Refer to documentation for supported image dimensions.",
                "target": "2"
            }
        ]
    }
}
```

최상위 *오류입니다. code* 속성은 다음과 같은 오류 코드 메시지 중 하나일 수 있습니다.

| 오류 코드           | 메시지                                                | HTTP 상태 |
| -------------------- | ------------------------------------------------------ | ----------- |
| InvalidRequest       | 잘못된 요청입니다.                                       | 400         |
| InvalidArgument      | 인수가 잘못되었습니다.                                      | 400         |
| 사용할 수 없음            | 정책 또는 기타 구성으로 인해 액세스가 금지 되었습니다. | 403         |
| NotFound             | 리소스를 찾을 수 없습니다.                                    | 404         |
| MethodNotAllowed     | 요청 된 HTTP 메서드를 사용할 수 없습니다.              | 405         |
| 충돌             | 충돌로 인해 요청을 완료할 수 없습니다.  | 409         |
| UnsupportedMediaType | 요청 콘텐츠 형식이 지원 되지 않습니다.                 | 415         |
| InternalServerError  | 예기치 않은 오류가 발생했습니다.                          | 500         |
| ServiceUnavailable   | 일시적인 오류가 발생 했습니다. 다시 시도하세요.      | 503         |

가능 하면 *내부 오류* 속성에 자세한 내용이 지정 되어 있습니다.

| 상위 오류 코드 | 내부 오류 코드 | 메시지 |
| -------------- | ---------------- | ------- |
| 충돌 | ModelExists | 제공 된 이름의 모델이 이미 있습니다. |
| 사용할 수 없음 | AuthorizationFailed | 권한 부여 실패: {details} |
| 사용할 수 없음 | InvalidDataProtectionKey | 데이터 보호 키가 잘못 되었습니다. {details} |
| 사용할 수 없음 | OutboundAccessForbidden | 요청에 현재 액세스 제어 정책에서 허용 하지 않는 도메인 이름이 포함 되어 있습니다. |
| InternalServerError | Unknown | 알 수 없는 오류입니다. |
| InvalidArgument | InvalidContentSourceFormat | 잘못 된 콘텐츠 원본: {details} |
| InvalidArgument | InvalidParameter | {ParameterName} 매개 변수가 잘못 되었습니다: {details} |
| InvalidArgument | InvalidParameterLength | 매개 변수 {parameterName}의 길이는 {maxChars} 자를 초과 하면 안 됩니다. |
| InvalidArgument | InvalidSasToken | SAS (공유 액세스 서명)가 잘못 되었습니다. {details} |
| InvalidArgument | ParameterMissing | {ParameterName} 매개 변수가 필요 합니다. |
| InvalidRequest | ContentSourceNotAccessible | 콘텐츠에 액세스할 수 없음: {details} |
| InvalidRequest | ContentSourceTimeout | 클라이언트에서 파일을 받는 동안 시간이 초과 되었습니다. |
| InvalidRequest | DocumentModelLimit | 계정이 {maximumModels} 개 이상의 모델을 만들 수 없습니다. |
| InvalidRequest | Documentmodel한가 구성 되었습니다. | 계정에서 {details} 개 이상의 구성 요소 모델을 사용 하 여 모델을 만들 수 없습니다. |
| InvalidRequest | InvalidContent | 파일이 손상 되었거나 형식이 지원 되지 않습니다. 지원 되는 형식 목록은 설명서를 참조 하세요. |
| InvalidRequest | InvalidContentDimensions | 입력 이미지 크기가 범위를 벗어났습니다. 지원 되는 이미지 차원에 대 한 설명서를 참조 하세요. |
| InvalidRequest | InvalidContentLength | 입력 이미지가 너무 깁니다. 최대 파일 크기는 설명서를 참조 하세요. |
| InvalidRequest | InvalidFieldsDefinition | 잘못 된 필드: {details} |
| InvalidRequest | InvalidTrainingContentLength | 학습 콘텐츠에 {bytes} 바이트가 포함 되어 있습니다. 교육은 {maxBytes} 바이트로 제한 됩니다. |
| InvalidRequest | InvalidTrainingContentPageCount | 학습 콘텐츠에 {pages} 페이지가 포함 되어 있습니다. 학습은 {pages} 페이지로 제한 됩니다. |
| InvalidRequest | ModelAnalyzeError | 사용자 지정 모델을 사용 하 여 분석할 수 없음: {details} |
| InvalidRequest | ModelBuildError | {Details} 모델을 작성할 수 없습니다. |
| InvalidRequest | ModelComposeError | {Details} 모델을 작성할 수 없습니다. |
| InvalidRequest | ModelNotReady | 모델이 요청한 작업에 대해 준비 되지 않았습니다. 교육이 완료 될 때까지 기다리거나 작업 오류를 확인 하십시오. |
| InvalidRequest | ModelReadOnly | 요청 된 모델은 읽기 전용입니다. |
| InvalidRequest | NotSupportedApiVersion | 요청한 작업을 수행 하려면 {minimumApiVersion} 이상이 필요 합니다. |
| InvalidRequest | OperationNotCancellable | 더 이상 작업을 취소할 수 없습니다. |
| InvalidRequest | TrainingContentMissing | 학습 데이터가 누락 됨: {details} |
| InvalidRequest | UnsupportedContent | 콘텐츠가 지원 되지 않음: {details} |
| NotFound | ModelNotFound | 요청 된 모델을 찾을 수 없습니다. 삭제 되었거나 아직 빌드 중입니다. |
| NotFound | OperationNotFound | 요청 된 작업을 찾을 수 없습니다. 식별자가 잘못 되었거나 작업이 만료 되었을 수 있습니다. |