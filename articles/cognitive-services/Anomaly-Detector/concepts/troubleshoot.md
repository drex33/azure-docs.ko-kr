---
title: Anomaly Detector 다변량 API 문제 해결
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API를 사용하는 경우 일반적인 오류 코드를 수정하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 변칙 검색, 기계 학습, 알고리즘
ms.openlocfilehash: 46b67e3f465bfb481389049e4c45e5c2edbefbfc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292691"
---
# <a name="troubleshooting-the-multivariate-api"></a>다변량 API 문제 해결

이 문서에서는 다변량 API를 사용하는 경우 일반적인 오류 메시지와 관련된 문제를 해결하고 해당 메시지를 수정하는 방법에 관한 지침을 제공합니다.

## <a name="multivariate-error-codes"></a>다변량 오류 코드

### <a name="common-errors"></a>일반 오류

| 오류 코드                 | HTTP 오류 코드 | 오류 메시지                                  | 의견                                                      |
| -------------------------- | --------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| `SubscriptionNotInHeaders` | 400             | 헤더에서 apim-subscription-id를 찾을 수 없습니다. | 헤더에 APIM 구독 ID를 추가하세요. 예제 헤더: `{"apim-subscription-id": <Your Subscription ID>}` |
| `FileNotExist`             | 400             | \<source> 파일이 없습니다.                  | Blob SAS(공유 액세스 서명)의 유효성을 확인하세요. 만료되지 않았는지 확인합니다. |
| `InvalidBlobURL`           | 400             |                                                | Blob SAS(공유 액세스 서명)가 유효한 SAS가 아닙니다.                            |
| `StorageWriteError`        | 403             |                                                | 이 오류의 원인은 권한 문제일 수 있습니다. 이 서비스는 CMK(고객 관리형 키)로 암호화된 Blob에 데이터를 쓸 수 없습니다. CMK를 제거하거나 서비스에 대한 액세스 권한을 다시 부여합니다. 자세한 내용은 [이 페이지](../../encryption/cognitive-services-encryption-keys-portal.md)를 참조하세요. |
| `StorageReadError`         | 403             |                                                | `StorageWriteError`와 동일합니다.                                 |
| `UnexpectedError`          | 500             |                                                | 자세한 오류 정보를 포함해서 문의해 주세요. [이 문서](../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fanomaly-detector%2fcontext%2fcontext)에서 지원 옵션을 사용하거나 [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com)을 메일을 보내 주세요.           |


### <a name="train-a-multivariate-anomaly-detection-model"></a>다변량 변칙 검색 모델 학습

| 오류 코드               | HTTP 오류 코드 | 오류 메시지                                                | 의견                                                      |
| ------------------------ | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `TooManyModels`          | 400             | 이 구독이 최대 모델 수에 도달했습니다.  | 각 APIM 구독 ID는 300개의 활성 모델을 포함할 수 있습니다. 새 모델을 학습시키기 전에 사용되지 않는 모델을 삭제하세요. |
| `TooManyRunningModels`   | 400             | 이 구독이 실행 중인 모델의 최대 수에 도달했습니다. | 각 APIM 구독 ID는 5개의 모델을 동시에 학습시킬 수 있습니다. 이전 모델이 학습 프로세스를 완료한 후 새 모델을 학습시키세요. |
| `InvalidJsonFormat`      | 400             | JSON 형식이 잘못되었습니다.                                         | 학습 요청이 유효한 JSON이 아닙니다.                        |
| `InvalidAlignMode`       | 400             | `'alignMode'` 필드는 `'Inner'` 또는 `'Outer'` 중 하나여야 합니다. | `'alignMode'` 값이 `'Inner'` 또는 `'Outer'`(대/소문자 구분) 중 하나인지 확인하세요. |
| `InvalidFillNAMethod`    | 400             | `'fillNAMethod'` 필드는 `'Previous'`, `'Subsequent'`, `'Linear'`, `'Zero'`, `'Fixed'`, `'NotFill'` 중 하나여야 하며, `'alignMode'`가 `'Outer'`인 경우 `'NotFill'`일 수 없습니다. | `'fillNAMethod'` 값을 확인하세요. 자세한 내용은 [이 섹션](./best-practices-multivariate.md#optional-parameters-for-training-api)을 참조하세요. |
| `RequiredPaddingValue`   | 400             | `'fillNAMethod'`가 `'Fixed'`인 경우 요청에 `'paddingValue'` 필드가 필요합니다. | `'fillNAMethod'`가 `'Fixed'`인 경우 유효한 패딩 값을 제공해야 합니다. 자세한 내용은 [이 섹션](./best-practices-multivariate.md#optional-parameters-for-training-api)을 참조하세요. |
| `RequiredSource`         | 400             | 요청에 `'source'` 필드가 필요합니다.             | 학습 요청에서 `'source'` 필드에 대한 값을 지정하지 않았습니다. 예: `{"source": <Your Blob SAS>}`. |
| `RequiredStartTime`      | 400             | 요청에 `'startTime'` 필드가 필요합니다.          | 학습 요청에서 `'startTime'` 필드에 대한 값을 지정하지 않았습니다. 예: `{"startTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidTimestampFormat` | 400             | 타임스탬프 형식이 잘못되었습니다. `<timestamp>`가 유효한 형식이 아닙니다. | 요청 본문의 타임스탬프 형식이 올바르지 않습니다. `import pandas as pd; pd.to_datetime(timestamp)`을 시도하여 확인할 수 있습니다. |
| `RequiredEndTime`        | 400             | 요청에 `'endTime'` 필드가 필요합니다.            | 학습 요청에서 `'startTime'` 필드에 대한 값을 지정하지 않았습니다. 예: `{"endTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidSlidingWindow`   | 400             | `'slidingWindow'` 필드는 28~2880 사이의 정수여야 합니다. | `'slidingWindow'`는 28~2880(포함) 사이의 정수여야 합니다. |

### <a name="get-multivariate-model-with-model-id"></a>모델 ID를 사용하여 다변량 모델 가져오기

| 오류 코드      | HTTP 오류 코드 | 오류 메시지             | 의견                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
| `ModelNotExist` | 404             | 모델이 없습니다. | 해당 모델 ID를 가진 모델이 없습니다. 요청 URL에서 모델 ID를 확인하세요. |

### <a name="list-multivariate-models"></a>다변량 모델 나열

| 오류 코드      | HTTP 오류 코드 | 오류 메시지             | 의견                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
|`InvalidRequestParameterError`| 400             | $skip 또는 $top 값이 잘못되었습니다... | 두 매개 변수의 값이 숫자인지 확인하세요. $skip 및 $top은 페이지 매김이 있는 모델을 나열하는 데 사용됩니다. API는 가장 최근 업데이트된 10개의 모델만 반환하므로 $skip 및 $top을 사용하여 이전에 업데이트된 모델을 가져올 수 있습니다. | 

### <a name="anomaly-detection-with-a-trained-model"></a>학습된 모델을 통한 변칙 검색

| 오류 코드        | HTTP 오류 코드 | 오류 메시지                                                | 의견                                                      |
| ----------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ModelNotExist`   | 404             | 모델이 없습니다.                                    | 유추에 사용되는 모델이 없습니다. 요청 URL에서 모델 ID를 확인하세요. |
| `ModelFailed`     | 400             | 모델이 학습되지 않았습니다.                                  | 모델이 성공적으로 학습되지 않았습니다. 모델 ID가 있는 모델을 가져와서 자세한 정보를 가져오세요. |
| `ModelNotReady`   | 400             | 모델이 아직 준비되지 않았습니다.                                  | 모델이 아직 준비되지 않았습니다. 학습 프로세스가 완료될 때까지 잠시 기다려 주세요. |
| `InvalidFileSize` | 413             | \<file> 파일이 파일 크기 한도(\<size limit>바이트)를 초과합니다. | 유추 데이터의 크기가 상한(현재 2GB)을 초과합니다. 유추에 더 적은 데이터를 사용하세요. |

### <a name="get-detection-results"></a>검색 결과 가져오기

| 오류 코드       | HTTP 오류 코드 | 오류 메시지              | 의견                                                      |
| ---------------- | --------------- | -------------------------- | ------------------------------------------------------------ |
| `ResultNotExist` | 404             | 결과가 없습니다. | 요청당 결과가 없습니다. 유추가 완료되지 않았거나 결과가 만료되었습니다(7일). |

### <a name="data-processing-errors"></a>데이터 처리 오류

다음 오류 코드에 연결된 HTTP 오류 코드가 없습니다.

| 오류 코드             | 오류 메시지                                                | 의견                                                      |
| ---------------------  | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `NoVariablesFound`     | 변수를 찾을 수 없습니다. 파일이 지침에 따라 구성되었는지 확인하세요. | 데이터 원본에서 csv 파일을 찾을 수 없습니다. 이는 일반적으로 잘못된 파일 구성으로 인해 발생합니다. 필요한 구조에 관해서는 샘플 데이터를 참조하세요. |
| `DuplicatedVariables`  | 동일한 이름의 여러 변수가 있습니다.             | 중복된 변수 이름이 있습니다.                         |
| `FileNotExist`         | \<filename> 파일이 없습니다.                              | 이 오류는 일반적으로 유추 중에 발생합니다. 변수가 학습 데이터에 나타났지만 유추 데이터에는 없습니다. |
| `RedundantFile`        | \<filename> 파일이 중복됩니다.                                | 이 오류는 일반적으로 유추 중에 발생합니다. 변수가 학습 데이터에 없지만 유추 데이터에 나타났습니다. |
| `FileSizeTooLarge`     | \<filename> 파일 크기가 너무 큽니다.                    | 단일 csv 파일 \<filename>의 크기가 한도를 초과합니다. 더 적은 데이터로 학습시키세요. |
| `ReadingFileError`     | \<filename>을 읽을 때 오류가 발생했습니다. \<error messages>    | \<filename> 파일을 읽지 못했습니다. 자세한 내용은 \<error messages>를 참조하거나 로컬 환경에서 `pd.read_csv(filename)`를 확인할 수 있습니다. |
| `FileColumnsNotExist`  | \<filename> 파일에 열 타임스탬프나 값이 없습니다.  | 각 csv 파일에는 이름이 **timestamp** 및 **value**(대/소문자 구분)인 두 개의 열이 있어야 합니다. |
| `VariableParseError`   | 변수 \<variable> 구문 분석 \<error message> 오류입니다.             | 런타임 오류로 인해 \<variable>을 처리할 수 없습니다. 자세한 내용은 \<error message>를 참조하거나 \<error message>를 사용하여 문의하세요. |
| `MergeDataFailed`      | 데이터를 병합하지 못했습니다. 데이터 형식을 확인하세요.              | 데이터 병합에 실패했습니다. 이는 잘못된 데이터 형식, 파일 구성 등으로 인해 발생할 수 있습니다. 현재 파일 구조에 관해서는 샘플 데이터를 참조하세요. |
| `ColumnNotFound`       | 병합된 데이터에서 \<column> 열을 찾을 수 없습니다.          | 병합 후 열이 누락되었습니다. 데이터를 확인하세요.     |
| `NumColumnsMismatch`   | 병합된 데이터의 열 수가 변수 수와 일치하지 않습니다. | 데이터를 확인하세요.                                      |
| `TooManyData`          | 데이터 포인트가 너무 많습니다. 최대 개수는 변수당 1,000,000개입니다.       | 입력 데이터의 크기를 줄이세요.                        |
| `NoData`               | 유효 데이터가 없습니다.                                   | 처리 후 학습/유추할 데이터가 없습니다. 시작 시간과 종료 시간을 선택하세요. |
| `DataExceedsLimit`     | 타임스탬프가 `startTime`~`endTime` 사이에 있는 데이터 길이가 한도(\<limit>)를 초과합니다. | 처리 후 데이터 크기가 한도를 초과합니다. (현재 처리된 데이터에 대한 한도는 없습니다.) |
| `NotEnoughInput`       | 데이터가 충분하지 않습니다. 데이터 길이는 \<data length>이지만, 최소 길이는 \<sliding window size>인 슬라이딩 윈도우보다 커야 합니다. | 유추할 데이터 포인트의 최소 수는 슬라이딩 윈도우의 크기입니다. 유추를 위해 더 많은 데이터를 제공해 보세요. |