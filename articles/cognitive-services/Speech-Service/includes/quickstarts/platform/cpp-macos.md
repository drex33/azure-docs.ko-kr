---
title: '빠른 시작: Speech SDK C++(macOS) 플랫폼 설정 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 통해 Speech Service SDK를 사용하여 macOS에서 C++용 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.openlocfilehash: 73a6c99b3f35fa43852981edb3343cf9644e9662
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252214"
---
이 가이드에서는 macOS 10.14 이상에서 C++용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여줍니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>시스템 요구 사항

macOS 10.14 이상

## <a name="install-speech-sdk"></a>Speech SDK 설치

1. Speech SDK 파일을 추출할 디렉터리를 선택하고, 해당 디렉터리를 가리키도록 `SPEECHSDK_ROOT` 환경 변수를 설정합니다. 이 변수는 이후 명령에서 디렉터리를 쉽게 참조할 수 있게 해줍니다. 예를 들어 홈 디렉터리에서 `speechsdk` 디렉터리를 사용하려는 경우 다음과 비슷한 명령을 사용합니다.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 아직 디렉터리가 없으면 디렉터리를 만듭니다.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK xcframework가 포함된 `.zip` 아카이브를 다운로드하여 추출합니다.

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. 추출된 패키지의 최상위 디렉터리의 내용을 확인합니다.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   디렉터리 목록에는 타사 통지와 라이선스 파일은 물론 `MicrosoftCognitiveServicesSpeech.xcframework` 디렉터리도 포함해야 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
