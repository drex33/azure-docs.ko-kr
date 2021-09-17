---
title: 긴 오디오 API - Speech Service
titleSuffix: Azure Cognitive Services
description: 긴 오디오 API가 긴 형식의 텍스트를 음성으로 통합하기 위해 어떻게 설계되었는지 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.openlocfilehash: 006ccace3cbaeefbb2c330c8ef29e7b19ba4151f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538618"
---
# <a name="long-audio-api"></a>긴 오디오 API

긴 오디오 API는 긴 형식 텍스트 음성 변환의 비동기 합성을 제공합니다(예: 오디오 북, 뉴스 기사 및 설명서). 이 API는 합성된 오디오를 실시간으로 반환하지 않습니다. 대신 응답을 폴링하고 서비스를 사용할 수 있도록 하는 출력을 사용합니다. 음성 SDK에서 사용하는 텍스트 음성 변환 API와는 달리 긴 오디오 API는 10분보다 긴 합성된 오디오를 만들 수 있습니다. 따라서 게시자 및 오디오 콘텐츠 플랫폼에서 오디오 서적 같은 긴 오디오 콘텐츠를 일괄 처리로 만드는 데 적합합니다.

긴 오디오 API의 추가 이점은 다음과 같습니다.

* 서비스가 반환하는 합성된 음성은 최상의 인공신경망 음성을 사용합니다.
* 음성 엔드포인트를 배포할 필요가 없습니다.

> [!NOTE]
> 긴 오디오 API는 [공용 신경망 음성](./language-support.md#neural-voices) 및 [사용자 지정 신경망 음성](./how-to-custom-voice.md)을 모두 지원합니다.

## <a name="workflow"></a>워크플로

긴 오디오 API를 사용하는 경우 일반적으로 합성될 텍스트 파일 또는 파일을 제출하고, 상태를 폴링하고, 상태가 성공을 나타내면 오디오 출력을 다운로드합니다.

이 다이어그램에서는 워크플로의 개략적인 개요를 제공합니다.

![긴 오디오 API 워크플로 다이어그램](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>합성을 위한 콘텐츠 준비

텍스트 파일을 준비하는 경우 다음에 해당하는지 확인합니다.

* 일반 텍스트(.txt) 또는 SSML 텍스트(.txt) 중 하나입니다.
* [BOM(바이트 순서 표시)을 사용하는 UTF-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)로 인코딩됩니다.
* zip이 아닌 단일 파일입니다.
* 일반 텍스트의 경우 400자 또는 SSML 텍스트의 경우 400 [청구 가능 문자](./text-to-speech.md#pricing-note) 초과 및 1만 단락 이하를 포함합니다.
  * 일반 텍스트의 경우 각 단락은 **Enter/Return** 을 눌러 구분됩니다. [일반 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)를 참조하세요.
  * SSML 텍스트의 경우 각 SSML 조각이 단락으로 간주됩니다. 다른 단락으로 SSML 부분을 구분합니다. [SSML 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)를 참조하세요.

## <a name="sample-code"></a>예제 코드

이 페이지의 나머지 부분에서는 Python에 집중하지만 긴 오디오 API에 대한 샘플 코드는 GitHub에서 다음과 같은 프로그래밍 언어에 대해 사용할 수 있습니다.

* [샘플 코드: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [샘플 코드: C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [샘플 코드: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Python 예제

이 섹션에는 긴 오디오 API의 기본 사용을 보여 주는 Python 예제가 포함됩니다. 즐겨찾는 IDE 또는 편집기를 사용하여 새 Python 프로젝트를 만듭니다. 그런 다음, 이 코드 조각을 `long_audio_synthesis_client.py`라는 파일에 복사합니다.

```python
import json
import ntpath
import requests
```

이러한 라이브러리는 HTTP 요청을 생성하고 텍스트 음성 변환 긴 오디오 합성 REST API를 호출하는 데 사용됩니다.

### <a name="get-a-list-of-supported-voices"></a>지원되는 음성 목록 가져오기

지원되는 음성 목록을 가져오려면 `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices`에 GET 요청을 보냅니다.

이 코드는 특정 지역/엔드포인트에서 사용할 수 있는 전체 음성 목록을 가져옵니다.

```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

다음 값을 바꿉니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대한 **개요** 섹션에서 사용할 수 있습니다.
* `<region>`을 음성 리소스가 생성된 지역으로 바꿉니다(예: `eastus` 또는 `westus`). 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대한 **개요** 섹션에서 사용할 수 있습니다.

다음과 같은 출력이 표시됩니다.

```json
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

**properties.publicAvailable** 이 **true** 인 경우 음성은 공용 인공신경망 음성입니다. 그렇지 않은 경우에는 사용자 지정 인공신경망 음성입니다.

### <a name="convert-text-to-speech"></a>텍스트 음성 변환

일반 텍스트 또는 SSML 텍스트에서 입력 텍스트 파일을 준비한 다음, 다음 코드를 `long_audio_synthesis_client.py`에 추가합니다.

> [!NOTE]
> `concatenateResult` 는 선택적 매개 변수입니다. 이 매개 변수를 설정하지 않으면 오디오 출력이 단락 단위로 생성됩니다. 매개 변수를 포함하여 오디오를 1개의 출력으로 연결할 수도 있습니다. 
> `outputFormat`도 선택 사항입니다. 기본적으로 오디오 출력은 `riff-16khz-16bit-mono-pcm`으로 설정됩니다. 지원되는 오디오 출력 형식에 대한 자세한 내용은 [오디오 출력 형식](#audio-output-formats)을 참조하세요.

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

다음 값을 바꿉니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대한 **개요** 섹션에서 사용할 수 있습니다.
* `<region>`을 음성 리소스가 생성된 지역으로 바꿉니다(예: `eastus` 또는 `westus`). 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대한 **개요** 섹션에서 사용할 수 있습니다.
* `<input_file_path>`를 텍스트 음성 변환을 위해 준비한 텍스트 파일에 대한 경로로 바꿉니다.
* `<locale>`을 원하는 출력 로캘로 바꿉니다. 자세한 내용은 [언어 지원](language-support.md#neural-voices)을 참조하세요.

`/voices` 엔드포인트에 대한 이전 호출에서 반환된 음성 중 하나를 사용합니다.

* 공용 인공신경망 음성을 사용하는 경우 `<voice_name>`을 원하는 출력 음성으로 바꿉니다.
* 사용자 지정 인공신경망 음성을 사용하려면 `voice_identities` 변수를 다음으로 바꾸고, `<voice_id>`를 사용자 지정 인공신경망 음성의 `id`로 바꿉니다.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

다음과 같은 출력이 표시됩니다.

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> 2개보다 많은 입력 파일이 있는 경우 여러 요청을 제출해야 하며, 고려해야 할 제한이 있습니다.
> * 클라이언트는 각 Azure 구독 계정에 대해 초당 최대 **5개** 의 요청을 제출할 수 있습니다. 제한이 초과되면 **429 오류 코드(너무 많은 요청)** 가 반환됩니다. 이 제한을 방지하려면 제출 비율을 줄입니다.
> * 서버는 각 Azure 구독 계정에 대해 최대 **120개** 의 요청을 큐에 추가할 수 있습니다. 큐가 이 제한을 초과하면 서버는 **429 오류 코드(너무 많은 요청)** 를 반환합니다. 추가 요청을 제출하기 전에 완료된 요청을 기다립니다.

출력에서 URL을 사용하여 요청 상태를 가져올 수 있습니다.

### <a name="get-details-about-a-submitted-request"></a>제출된 요청에 대한 세부 정보 가져오기

제출된 합성 요청의 상태를 가져오려면 이전 단계에서 반환된 URL에 GET 요청을 보냅니다.

```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```

출력은 다음과 같습니다.

```json
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

`status` 속성은 `NotStarted` 상태에서 `Running`으로 마지막으로 `Succeeded` 또는 `Failed`로 변경됩니다. 상태가 `Succeeded` 또는 `Failed`가 될 때까지 루프에서 이 API를 폴링할 수 있습니다.

### <a name="download-audio-result"></a>오디오 결과 다운로드

합성 요청이 성공하면 GET `/files` API를 호출하여 오디오 결과를 다운로드할 수 있습니다.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```

`<request_id>`를 결과를 다운로드하려는 요청 ID로 바꿉니다. 이는 이전 단계의 응답에서 찾을 수 있습니다.

출력은 다음과 같습니다.

```json
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
이 예제 출력에는 두 파일에 대한 정보가 포함되어 있습니다. `"kind": "LongAudioSynthesisScript"`가 포함된 하나는 제출된 입력 스크립트입니다. `"kind": "LongAudioSynthesisResult"`가 포함된 다른 하나는 이 요청의 결과입니다.

결과는 입력 텍스트의 복사본과 함께 생성된 오디오 출력 파일을 포함하는 zip입니다.

두 파일은 모두 `links.contentUrl` 속성의 URL에서 다운로드할 수 있습니다.

### <a name="get-all-synthesis-requests"></a>모든 합성 요청 가져오기

다음 코드는 제출된 모든 요청을 나열합니다.

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

출력은 다음과 같습니다.

```json
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` 속성은 합성 요청을 나열합니다. 최대 100페이지가 포함된 목록의 페이지가 매겨집니다. 요청이 100개가 넘을 경우 페이지가 매겨진 목록의 다음 페이지를 가져오도록 `"@nextLink"` 속성이 제공됩니다.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

URL 매개 변수에서 `skip` 및 `top`을 입력하여 페이지 크기를 사용자 지정하고 번호를 건너뛸 수도 있습니다.

### <a name="remove-previous-requests"></a>이전 요청 제거

서비스는 각 Azure 구독 계정에 대해 최대 **2만** 개의 요청을 유지합니다. 요청 금액이 이 제한을 초과하는 경우 새 요청을 만들기 전에 이전 요청을 제거합니다. 기존 요청을 제거하지 않으면 오류 알림이 수신됩니다.

다음 코드는 특정 합성 요청을 제거하는 방법을 보여 줍니다.

```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

요청이 성공적으로 제거되면 응답 상태 코드는 HTTP 204(콘텐츠 없음)입니다.

```console
response.status_code: 204
```

> [!NOTE]
> 상태가 `NotStarted` 또는 `Running`인 요청은 제거하거나 삭제할 수 없습니다.

[GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)에서 완료된 `long_audio_synthesis_client.py`를 사용할 수 있습니다.

## <a name="http-status-codes"></a>HTTP 상태 코드

다음 표에 HTTP 응답 코드 및 REST API의 메시지가 자세히 설명되어 있습니다.

| API | HTTP 상태 코드 | Description | 솔루션 |
|-----|------------------|-------------|----------|
| 생성 | 400 | 이 지역에서는 음성 합성을 사용할 수 없습니다. | 지원되는 지역으로 음성 구독 키를 변경합니다. |
|        | 400 | 이 영역에 대한 **표준** 음성 구독만 유효합니다. | 음성 구독 키를 “표준” 가격 책정 계층으로 변경합니다. |
|        | 400 | Azure 계정에 대한 2만 개 요청 제한을 초과합니다. 새 요청을 제출하기 전에 일부 요청을 제거합니다. | 서버는 각 Azure 계정에 대해 최대 2만 개의 요청을 유지합니다. 새 요청을 제출하기 전에 일부 요청을 삭제합니다. |
|        | 400 | {modelID} 모델은 음성 합성에서 사용할 수 없습니다. | {modelID}의 상태가 올바른지 확인합니다. |
|        | 400 | 요청에 대한 지역이 {modelID} 모델에 대한 지역과 일치하지 않습니다. | {modelID}의 지역이 요청 지역과 일치하는지 확인합니다. |
|        | 400 | 음성 합성은 바이트 순서 표시를 사용한 UTF-8 인코딩의 텍스트 파일만 지원합니다. | 입력 파일이 바이트 순서 표시를 사용한 UTF-8 인코딩인지 확인합니다. |
|        | 400 | 음성 합성 요청에서는 유효한 SSML 입력만 허용됩니다. | 입력 SSML 식이 올바른지 확인합니다. |
|        | 400 | 입력 파일에 음성 이름 {voiceName}이(가) 없습니다. | 입력 SSML 음성 이름이 모델 ID와 맞지 않습니다. |
|        | 400 | 입력 파일의 단락 수는 1만 개 미만이어야 합니다. | 파일의 단락 수가 1만 개 미만인지 확인합니다. |
|        | 400 | 입력 파일은 400자 이하여야 합니다. | 입력 파일이 400자를 초과하는지 확인합니다. |
|        | 404 | 음성 합성 정의에 선언된 모델 {modelID}을(를) 찾을 수 없습니다. | {modelID}이(가) 올바른지 확인합니다. |
|        | 429 | 활성 음성 합성 제한을 초과합니다. 일부 요청이 완료될 때까지 기다립니다. | 서버는 각 Azure 계정에 대해 최대 120개 요청을 실행하고 큐에 추가할 수 있습니다. 잠시 기다렸다가 일부 요청이 완료될 때까지 새 요청을 제출하지 마십시오. |
| 모두       | 429 | 요청이 너무 많습니다. | 클라이언트는 각 Azure 계정에 대해 초당 최대 5개 요청을 서버에 제출할 수 있습니다. 초당 요청 크기를 줄입니다. |
| DELETE    | 400 | 음성 합성 작업을 계속 사용하고 있습니다. | **완료** 또는 **실패** 한 요청만 삭제할 수 있습니다. |
| GetByID   | 404 | 지정한 엔터티를 찾을 수 없습니다. | 합성 ID가 올바른지 확인합니다. |

## <a name="regions-and-endpoints"></a>지역 및 엔드포인트

긴 오디오 API는 고유한 엔드포인트를 사용하여 여러 지역에서 사용할 수 있습니다.

| 지역 | 엔드포인트 |
|--------|----------|
| 미국 동부 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 인도 중부 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 동남아시아 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 영국 남부 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 서유럽 | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>오디오 출력 형식

유연한 오디오 출력 형식을 지원합니다. 단락당 오디오 출력을 생성하거나 `concatenateResult` 매개 변수를 설정하여 오디오 출력을 단일 출력에 연결할 수 있습니다. 긴 오디오 API에서 지원하는 오디오 출력 형식은 다음과 같습니다.

> [!NOTE]
> 기본 오디오 형식은 riff-16khz-16bit-mono-pcm입니다.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3
