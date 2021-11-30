---
title: 음성 텍스트 세션 ID 및 기록 ID를 가져오는 방법
titleSuffix: Azure Cognitive Services
description: 음성 서비스 음성-텍스트 세션 ID 및 기록 ID를 가져오는 방법 알아보기
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/19/2021
ms.author: alexeyo
ms.openlocfilehash: 0e6ade1a0e5bee9e9751663fa2ac7675dbe60c7e
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271956"
---
# <a name="how-to-get-speech-to-text-session-id-and-transcription-id"></a>음성 텍스트 세션 ID 및 기록 ID를 가져오는 방법

[음성 텍스트](speech-to-text.md) 를 사용 하 여 지원 사례를 열어야 하는 경우 문제를 디버그 하기 위해 문제를 디버그 하는 데 문제가 있는 *세션 ID* 또는 기록 *id* 를 제공 해야 하는 경우가 종종 있습니다. 이 문서에서는 이러한 Id를 가져오는 방법을 설명 합니다. 

> [!NOTE]
> * *세션 ID* 는 [온라인](get-started-speech-to-text.md) 기록 및 [번역](speech-translation.md)에 사용 됩니다.
> * 기록 *ID* 는 [일괄 처리](batch-transcription.md)기록에 사용 됩니다.

## <a name="getting-session-id-for-online-transcription-and-translation-speech-sdk-and-rest-api-for-short-audio"></a>온라인 기록 및 변환에 대 한 세션 ID를 가져오는 중입니다. (음성 SDK 및 짧은 오디오의 경우 REST API).

[온라인](get-started-speech-to-text.md) 기록 및 [번역](speech-translation.md) 은 [음성 SDK](speech-sdk.md) 또는 [짧은 오디오에 대 한 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)를 사용 합니다.

세션 ID를 가져오려면 SDK를 사용 하는 경우 다음을 수행 해야 합니다.

1. 응용 프로그램 로깅을 사용 하도록 설정 합니다.
1. 로그 내에서 세션 ID를 찾습니다.

[음성 CLI](spx-overview.md)를 사용 하는 경우 세션 ID를 대화형으로 가져올 수도 있습니다. [아래](#get-session-id-using-speech-cli)세부 정보를 참조 하세요.

[짧은 오디오에 대 한 음성 텍스트 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 의 경우 요청에서 세션 정보를 "삽입" 해야 합니다. [아래](#provide-session-id-using-rest-api-for-short-audio)세부 정보를 참조 하세요.

### <a name="enable-logging-in-the-speech-sdk"></a>Speech SDK에서 로깅 사용

[이 문서](how-to-use-logging.md)에 설명 된 대로 응용 프로그램에 대 한 로깅을 사용 하도록 설정 합니다.

### <a name="get-session-id-from-the-log"></a>로그에서 세션 ID 가져오기

응용 프로그램에서 생성 된 로그 파일을 열고를 찾습니다 `SessionId:` . 이 숫자는 필요한 세션 ID입니다. 다음 로그 발췌 예에서는 `0b734c41faf8430380d493127bd44631` 세션 ID입니다.

```
[874193]: 218ms SPX_DBG_TRACE_VERBOSE:  audio_stream_session.cpp:1238 [0000023981752A40]CSpxAudioStreamSession::FireSessionStartedEvent: Firing SessionStarted event: SessionId: 0b734c41faf8430380d493127bd44631
```

### <a name="get-session-id-using-speech-cli"></a>Speech CLI를 사용 하 여 세션 ID 가져오기

[음성 CLI](spx-overview.md)를 사용 하는 경우 `SESSION STARTED` 및 콘솔 메시지에 세션 ID가 표시 됩니다 `SESSION STOPPED` .

세션에 대 한 로깅을 사용 하도록 설정 하 고 위에 설명 된 대로 로그 파일에서 세션 ID를 가져올 수도 있습니다. 적절 한 음성 CLI 명령을 실행 하 여 로그를 사용 하는 방법에 대 한 정보를 가져옵니다.

```console
spx help recognize log
```
```console
spx help translate log
```


### <a name="provide-session-id-using-rest-api-for-short-audio"></a>짧은 오디오에 REST API를 사용 하 여 세션 ID 제공

음성 SDK와 달리 [짧은 오디오의 음성 텍스트 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 는 세션 ID를 자동으로 생성 하지 않습니다. 사용자가 직접 생성 하 고 REST 요청 내에서 제공 해야 합니다.

코드 내에서 또는 표준 도구를 사용 하 여 GUID를 생성 합니다. *대시 또는 다른 분할자 없이* GUID 값을 사용 합니다. 예를 들어를 사용 `9f4ffa5113a846eba289aa98b28e766f` 합니다.

REST 요청 사용 식의 일부로 사용 `X-ConnectionId=<GUID>` 됩니다. 예제에서 샘플 요청은 다음과 같습니다.
```http
https://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&X-ConnectionId=9f4ffa5113a846eba289aa98b28e766f
```
`9f4ffa5113a846eba289aa98b28e766f` 은 (는) 세션 ID가 됩니다.

## <a name="getting-transcription-id-for-batch-transcription-rest-api-v30"></a>일괄 처리 기록을 위한 기록 ID를 가져오는 중입니다. (REST API v 3.0).

[일괄 처리](batch-transcription.md) 는 [음성 텍스트 REST API v 3.0을](rest-speech-to-text.md#speech-to-text-rest-api-v30)사용 합니다. 

필요한 기록 ID는 기록 `self` [만들기](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateTranscription)와 같이 요청에서 반환 하는 응답 본문의 주요 요소에 포함 된 GUID 값입니다.

아래 예제는 요청에 대 한 응답 본문입니다 `Create Transcription` . `537216f8-0620-4a10-ae2d-00bdb423b36f`첫 번째 요소에 있는 GUID 값 `self` 은 기록 ID입니다.

```json
{
  "self": "https://japaneast.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/537216f8-0620-4a10-ae2d-00bdb423b36f",
  "model": {
    "self": "https://japaneast.api.cognitive.microsoft.com/speechtotext/v3.0/models/base/824bd685-2d45-424d-bb65-c3fe99e32927"
  },
  "links": {
    "files": "https://japaneast.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/537216f8-0620-4a10-ae2d-00bdb423b36f/files"
  },
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "channels": [
      0,
      1
    ],
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "lastActionDateTime": "2021-11-19T14:09:51Z",
  "status": "NotStarted",
  "createdDateTime": "2021-11-19T14:09:51Z",
  "locale": "ru-RU",
  "displayName": "transcriptiontest"
}
```
> [!NOTE]
> [데이터 집합 만들기](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset) 요청을 사용 하 여 데이터 집합을 업로드 하는 것과 같은 [Custom Speech](custom-speech-overview.md)관련 된 문제를 디버깅 하는 데 필요한 다양 한 id를 확인 하려면 동일한 기술을 사용

> [!NOTE]
> 또한 [Get a get-help](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions) request를 사용 하 여 지정 된 음성 리소스에 대 한 모든 기존 음성 스크립트가 나 해당 기록 id를 볼 수 있습니다.
