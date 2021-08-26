---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: lajanuar
ms.openlocfilehash: 0d00d9d9d5190cf5d593088669af3f3fabdac10c
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069100"
---
이 빠른 시작에서는 Speech SDK를 사용하여 텍스트 음성 변환을 수행하기 위한 일반적인 디자인 패턴에 대해 알아봅니다. 먼저 기본 구성 및 합성을 수행하고 다음과 같은 사용자 지정 애플리케이션 개발을 위한 고급 예제로 이동합니다.

* 메모리 내 스트림으로 응답 가져오기
* 출력 샘플 속도 및 비트 전송률 사용자 지정
* SSML(음성 합성 마크업 언어)을 사용하여 합성 요청 제출
* 인공신경망 음성 사용

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>스피커로 음성 합성

이제 Speech CLI를 실행하여 텍스트에서 음성을 합성할 준비가 되었습니다. 명령줄에서 Speech CLI 이진 파일이 포함된 디렉터리로 변경합니다. 그런 후, 다음 명령을 실행합니다.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Speech CLI는 컴퓨터 스피커를 통해 영어로 자연어를 생성합니다.

## <a name="synthesize-speech-to-a-file"></a>음성을 파일로 합성

다음 명령을 실행하여 스피커의 출력을 `.wav` 파일로 변경합니다.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Speech CLI는 `greetings.wav` 오디오 파일에 영어로 자연어를 생성합니다.
Windows에서는 `start greetings.wav`를 입력하여 오디오 파일을 재생할 수 있습니다.