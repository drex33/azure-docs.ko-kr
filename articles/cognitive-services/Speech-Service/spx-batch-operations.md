---
title: Speech CLI 일괄 처리 옵션 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech CLI를 사용하여 음성을 텍스트로 변환 일괄 처리(음성 인식), 텍스트를 음성으로 변환 일괄 처리(음성 합성)하는 방법을 알아봅니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: eur
ms.custom: mode-other
ms.openlocfilehash: 8e05bb4441bb1d1b89c826b2714ac0ff95f99dcc
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133075644"
---
# <a name="speech-cli-batch-operations"></a>Speech CLI 일괄 처리 작업

Azure Speech Service를 사용할 때 일반적인 작업은 일괄 처리 작업입니다. 이 문서에서는 Speech CLI를 사용하여 음성을 텍스트로 변환 일괄 처리(음성 인식), 텍스트를 음성으로 변환 일괄 처리(음성 합성)하는 방법을 알아봅니다. 특히 다음 방법을 학습합니다.

* 오디오 파일의 디렉터리에서 일괄 처리 음성 인식 실행
* `.tsv` 파일을 반복하여 일괄 처리 음성 합성 실행

## <a name="batch-speech-to-text-speech-recognition"></a>음성을 텍스트로 변환 일괄 처리(음성 인식)

Speech Service는 오디오 파일의 음성을 인식하는 데 종종 사용됩니다. 이 예제에서는 Speech CLI를 사용하여 디렉터리를 반복하여 각 `.wav` 파일의 인식 출력을 캡처하는 방법을 알아봅니다. `--files` 플래그는 오디오 파일이 저장된 디렉터리를 가리키는 데 사용되며, 와일드 카드 `*.wav`는 확장명이 `.wav`인 모든 파일에서 인식을 실행하도록 Speech CLI에 지시하는 데 사용됩니다. 각 인식 파일의 출력은 `speech_output.tsv`에서 탭으로 구분된 값으로 기록됩니다.

> [!NOTE]
> `--threads` 인수는 다음 섹션의 `spx synthesize` 명령에도 사용할 수 있으며, 사용 가능한 스레드는 CPU 및 현재 로드 비율에 따라 달라집니다.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

다음은 출력 파일 구조의 예입니다.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>텍스트를 음성으로 변환 일괄 처리(음성 합성)

일괄 처리 텍스트 음성 변환을 실행하는 가장 쉬운 방법은 새 `.tsv`(탭으로 구분된 값) 파일을 만들고, Speech CLI에서 `--foreach` 명령을 사용하는 것입니다. 자주 사용하는 텍스트 편집기를 사용하여 `.tsv` 파일을 만들 수 있습니다. 이 예에서는 `text_synthesis.tsv`라고 하겠습니다.

>[!IMPORTANT]
> 이 텍스트 파일의 콘텐츠를 복사할 때 파일 위치와 텍스트 사이에 공백이 아닌 **탭** 이 있는지 확인합니다. 이 예에서 콘텐츠를 복사할 때 탭이 공백으로 변환되어 실행 시 `spx` 명령이 실패하는 경우도 있습니다.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

그런 다음, 명령을 실행하여 `text_synthesis.tsv`를 가리키고, 각 `text` 필드에서 합성을 수행하고, 결과를 해당 `audio.output` 경로에 `.wav` 파일로 작성합니다.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

이 명령은 `.tsv` 파일의 **각** 레코드에 대해 `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav`를 실행하는 것과 동일한 효과가 있습니다.

다음 사항에 주의해야 합니다.

* 열 헤더 `audio.output` 및 `text`는 각각 명령줄 인수 `--audio output` 및 `--text`에 해당합니다. `--audio output`처럼 여러 부분으로 구성된 명령줄 인수는 파일에서 공백, 선행 대시, 문자열을 구분하는 마침표 없이 서식을 지정해야 합니다(예: `audio.output`). 그 외의 기존 명령줄 인수는 이 패턴을 사용하여 파일에 추가 열로 추가할 수 있습니다.
* 이 방법으로 파일의 형식을 지정하면 `--foreach`에 추가 인수를 전달할 필요가 없습니다.
* `.tsv`의 각 값을 **탭** 으로 구분해야 합니다.

그러나 다음 예제와 같은 `.tsv` 파일이 있고 열 헤더가 명령줄 인수와 **일치하지 않는** 경우:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

`--foreach` 호출에서 다음 구문을 사용하여 이러한 필드 이름을 올바른 인수로 재정의할 수 있습니다. 이 호출은 위와 동일한 호출입니다.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>다음 단계

* [Speech CLI 개요](./spx-overview.md)
* [Speech CLI 빠른 시작](./spx-basics.md)
