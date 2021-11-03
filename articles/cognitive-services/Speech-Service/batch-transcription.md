---
title: Batch Transcription을 사용하는 방법 - Speech Service
titleSuffix: Azure Cognitive Services
description: Batch Transcription은 Azure Blob과 같이 스토리지에 많은 양의 오디오를 전사하려는 경우에 이상적입니다. 전용 REST API를 사용하면 SAS(공유 액세스 서명) URI가 있는 오디오 파일을 가리키고 비동기식으로 전사를 수신할 수 있습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: f5f8533c5962b21ad1c23e3b618b66fae73bb8ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056726"
---
# <a name="how-to-use-batch-transcription"></a>Batch Transcription을 사용하는 방법

Batch Transcription은 스토리지에 있는 많은 양의 오디오를 전사할 수 있는 REST API 작업 집합입니다. 일반 URI 또는 [SAS(공유 액세스 서명)](../../storage/common/storage-sas-overview.md) URI를 사용하여 오디오 파일을 가리키고 비동기적으로 전사 결과를 받을 수 있습니다. v3.0 API를 사용하면 하나 이상의 오디오 파일을 전사하거나 전체 스토리지 컨테이너를 처리할 수 있습니다.

Batch Transcription REST API를 사용하여 다음 메서드를 호출할 수 있습니다.

|    Batch Transcription 작업                                             |    방법    |    REST API 호출                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    새 전사를 만듭니다.                                              |    POST      |    speechtotext/v3.0/transcriptions            |
|    인증된 구독의 전사 목록을 검색합니다.    |    GET       |    speechtotext/v3.0/transcriptions            |
|    오프라인 전사에 지원되는 로캘 목록을 가져옵니다.              |    GET       |    speechtotext/v3.0/transcriptions/locales    |
|    ID로 식별되는 전사의 변경 가능한 세부 정보를 업데이트합니다.    |    패치     |    speechtotext/v3.0/transcriptions/{id}       |
|    지정된 전사 작업을 삭제합니다.                                 |    Delete    |    speechtotext/v3.0/transcriptions/{id}       |
|    지정된 ID로 식별되는 전사를 가져옵니다.                        |    GET       |    speechtotext/v3.0/transcriptions/{id}       |
|    지정된 ID로 식별되는 전사의 결과 파일을 가져옵니다.    |    GET       |    speechtotext/v3.0/transcriptions/{id}/files |

[Swagger 문서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)로 제공되는 자세한 API를 검토하고 테스트할 수 있습니다.

Batch Transcription 작업은 최선의 노력으로 예약됩니다.
작업이 실행 중 상태로 변경되는 시기를 예측할 수는 없지만 정상적인 시스템 로드에서 몇 분 내에 발생해야 합니다.
실행 중 상태가 되면 전사가 오디오 런타임 재생 속도보다 빠르게 발생합니다.

## <a name="prerequisites"></a>필수 구성 요소

Speech Service의 모든 기능과 마찬가지로, [시작 가이드](overview.md#try-the-speech-service-for-free)에 따라 [Azure Portal](https://portal.azure.com)에서 구독 키를 만듭니다.

>[!NOTE]
> Batch Transcription을 사용하려면 Speech Service를 위한 표준 구독(S0)이 필요합니다. 체험 구독 키(F0)는 작동하지 않습니다. 자세한 내용은 [가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

모델을 사용자 지정하려는 경우 [음향 사용자 지정](./how-to-custom-speech-train-model.md) 및 [언어 사용자 지정](./how-to-custom-speech-train-model.md) 단계를 따르세요. 생성된 모델을 Batch Transcription에 사용하려면 모델 위치가 필요합니다. 모델의 세부 정보를 검사할 때 모델 위치를 검색할 수 있습니다(`self` 속성). Batch Transcription 서비스에는 배포된 사용자 지정 엔드포인트가 *필요하지 않습니다*.

>[!NOTE]
> REST API의 일부인 Batch Transcription에는 일련의 [할당량 및 제한](speech-services-quotas-and-limits.md#batch-transcription)이 있으므로 이를 검토하는 것이 좋습니다. 대량의 오디오 파일을 효율적으로 전사하는 Batch Transcription 기능을 최대한 활용하려면 항상 요청당 여러 파일을 전송하거나 전사할 오디오 파일이 있는 Blob Storage 컨테이너를 가리키는 것이 좋습니다. 이 서비스는 파일을 동시에 전사하여 처리 시간을 줄입니다. 단일 요청에서 여러 파일을 사용하는 것은 매우 쉽고 간단합니다. [구성](#configuration) 섹션을 참조하세요.

## <a name="batch-transcription-api"></a>Batch Transcription API

Batch Transcription API에서 지원하는 형식은 다음과 같습니다.

| 형식 | Codec | 샘플당 비트 수 | 샘플링 주기             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16비트  | 8kHz 또는 16kHz, 모노 또는 스테레오 |
| MP3    | PCM   | 16비트  | 8kHz 또는 16kHz, 모노 또는 스테레오 |
| OGG    | OPUS  | 16비트  | 8kHz 또는 16kHz, 모노 또는 스테레오 |

스테레오 오디오 스트림의 경우 전사 중 왼쪽 및 오른쪽 채널이 분할됩니다. 각 채널에 대해 JSON 결과 파일이 생성됩니다.
정렬된 최종 전사를 만들려면 발화당 생성된 타임스탬프를 사용합니다.

### <a name="configuration"></a>구성

구성 매개 변수는 JSON으로 제공됩니다.

**하나 이상의 개별 파일 전사.** 전사할 파일이 두 개 이상 있는 경우 하나의 요청으로 여러 파일을 보내는 것이 좋습니다. 다음 예에서는 세 개의 파일을 사용합니다.

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**전체 스토리지 컨테이너 처리.** 컨테이너 [SAS](../../storage/common/storage-sas-overview.md)에는 `r`(읽기) 및 `l`(나열) 권한이 있어야 합니다.

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**Batch Transcription에서 사용자 지정 학습된 모델 사용.** 예에서는 세 개의 파일을 사용합니다.

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>구성 속성

다음과 같은 선택적 속성을 사용하여 전사를 구성합니다.

:::row:::
   :::column span="1":::
      **매개 변수**
   :::column-end:::
   :::column span="2":::
      **설명**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      선택 사항이며, 기본값은 `Masked`입니다. 인식 결과에서 욕설의 처리 방법을 지정합니다. 허용되는 값은 욕설 필터링을 사용하지 않으려면 `None`, 욕설을 별표로 바꾸려면 `Masked`, 결과에서 모든 욕설을 제거하려면 `Removed`, "욕설" 태그를 추가하려면 `Tags`입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      선택 사항이며, 기본값은 `DictatedAndAutomatic`입니다. 인식 결과에서 문장 부호의 처리 방법을 지정합니다. 허용되는 값은 문장 부호를 사용하지 않으려면 `None`, 명시적(음성) 문장 부호를 암시하려면 `Dictated`, 디코더가 문장 부호를 처리하도록 허용하려면 `Automatic`, 받아쓰기 및 자동 구두점을 사용하려면 `DictatedAndAutomatic`입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      선택 사항이며, 기본값은 `false`입니다. 단어 수준 타임스탬프를 출력에 추가할지 여부를 지정합니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      선택 사항이며, 기본값은 `false`입니다. 입력에서 분할 분석을 수행하도록 지정합니다. 이렇게 하면 두 개의 음성을 포함하는 모노 채널이 됩니다. 참고: `wordLevelTimestampsEnabled`를 `true`로 설정해야 합니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      선택 사항이며, 기본적으로 `0` 및 `1`이 전사됩니다. 처리할 채널 번호의 배열입니다. 여기에서 오디오 파일에서 사용 가능한 채널의 하위 집합을 처리하도록 지정할 수 있습니다(예: `0`만 해당).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      선택 사항이며, 기본적으로 삭제되지 않습니다. 전사 완료 후 전사를 자동으로 삭제하는 기간입니다. `timeToLive`는 전사를 대량 처리하여 최종적으로 삭제되도록 하는 데 유용합니다(예: 12시간 동안 `PT12H`).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure의 쓰기 가능한 컨테이너에 대한 [임시 SAS](../../storage/common/storage-sas-overview.md)가 포함된 선택적 URL입니다. 결과는 이 컨테이너에 저장됩니다. 저장된 액세스 정책을 사용하는 SAS는 지원되지 **않습니다**. 지정되지 않은 경우 Microsoft에서 관리하는 스토리지 컨테이너에 결과가 저장됩니다. [전사 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)를 호출하여 전사를 삭제하면 결과 데이터도 삭제됩니다.
:::row-end:::

### <a name="storage"></a>스토리지

Batch Transcription은 공개적으로 표시되는 인터넷 URI에서 오디오를 읽을 수 있으며 [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md)에서 SAS URI를 사용하여 오디오를 읽거나 전사를 작성할 수 있습니다.

## <a name="batch-transcription-result"></a>Batch Transcription 결과

각 오디오 입력에 대해 하나의 전사 결과 파일이 생성됩니다.
[전사 파일 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) 작업은 이 전사에 대한 결과 파일 목록을 반환합니다.
특정 입력 파일에 대한 전사 파일을 찾으려면 반환된 모든 파일을 `kind` == `Transcription` 및 `name` == `{originalInputName.suffix}.json`으로 필터링합니다.

각 전사 결과 파일의 형식은 다음과 같습니다.

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"
      "speaker": 1,                     // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

결과에는 다음 필드가 포함됩니다.

:::row:::
   :::column span="1":::
      **필드**
   :::column-end:::
   :::column span="2":::
      **콘텐츠**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      실제 단어를 인식합니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      인식된 텍스트의 역 텍스트 정규화 형식입니다. 약어("doctor smith"에서 "dr smith"로), 전화 번호 및 기타 변환이 적용됩니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      욕설 마스킹이 적용된 ITN 형태입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      인식된 텍스트의 표시 형식입니다. 추가된 문장 부호와 대문자 표시가 포함됩니다.
:::row-end:::

## <a name="speaker-separation-diarization"></a>화자 분리(분할)

분할은 화자를 오디오 조각으로 분리하는 프로세스입니다. 일괄 처리 파이프라인은 분할을 지원하며 모노 채널 녹음에서 두 명의 화자를 인식할 수 있습니다. 스테레오 녹음에서는 이 기능을 사용할 수 없습니다.

분할이 사용하도록 설정된 전사의 출력에는 각 전사 구문에 대한 `Speaker` 항목이 포함됩니다. 분할이 사용되지 않는 경우 `Speaker` 속성이 JSON 출력에 표시되지 않습니다. 분할을 위해 두 가지 음성을 지원하므로 화자는 `1` 또는 `2`로 식별됩니다.

분할을 요청하려면 아래에 표시된 HTTP 요청과 같이 `diarizationEnabled` 속성을 `true`로 설정합니다.

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

위 요청의 매개 변수가 나타내는 대로 단어 수준 타임스탬프를 사용하도록 설정해야 합니다.

## <a name="best-practices"></a>모범 사례

Batch Transcription 서비스는 제출된 많은 전사를 처리할 수 있습니다. [전사 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)를 사용하여 전사 상태를 쿼리할 수 있습니다.
결과를 검색한 후 서비스에서 정기적으로 [전사 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)를 호출합니다. 또는 결과가 최종적으로 삭제되도록 `timeToLive` 속성을 설정합니다.

> [!TIP]
> [수집 클라이언트](ingestion-client.md) 도구와 그에 따른 솔루션을 사용하여 대용량 오디오를 처리할 수 있습니다.

## <a name="sample-code"></a>샘플 코드

전체 샘플은 `samples/batch` 하위 디렉터리의 [GitHub 샘플 스토리지](https://aka.ms/csspeech/samples)에서 사용할 수 있습니다.

사용자 지정 모델을 사용하는 경우 구독 정보, 서비스 지역, 전사할 오디오 파일을 가리키는 URI 및 모델 위치로 샘플 코드를 업데이트합니다.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/batchclient/program.cs#transcriptiondefinition)]

샘플 코드는 클라이언트를 설정하고 전사 요청을 제출합니다. 그런 다음, 상태 정보를 폴링하고 전사 진행 상황에 대한 세부 정보를 출력합니다.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

이전 호출에 대한 자세한 내용은 [Swagger 문서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)를 참조하세요. 여기에 표시된 전체 샘플을 보려면 [GitHub](https://aka.ms/csspeech/samples)의 `samples/batch` 하위 디렉터리로 이동합니다.

이 샘플은 비동기 설정을 사용하여 오디오를 게시하고 전사 상태를 수신합니다.
`PostTranscriptions` 메서드는 오디오 파일 세부 정보를 전송하고 `GetTranscriptions` 메서드는 상태를 수신합니다.
`PostTranscriptions`는 핸들을 반환하고, `GetTranscriptions`는 이 핸들을 사용하여 전사 상태를 가져오기 위한 핸들을 만듭니다.

이 샘플 코드는 사용자 지정 모델을 지정하지 않습니다. 이 서비스는 파일을 전사하기 위해 기준 모델을 사용합니다. 모델을 지정하려면 사용자 지정 모델에 대한 모델 참조와 동일한 메서드를 전달하면 됩니다.

> [!NOTE]
> 기준 전사의 경우 기준 모델의 ID를 선언할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [Speech-to-Text v3 API 참조](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
