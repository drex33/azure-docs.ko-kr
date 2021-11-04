---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: b40ba648cc35cf4c4900c6c0c504afdcd05fcd29
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502521"
---
## <a name="install-speech-sdk"></a>Speech SDK 설치

Linux용 Speech SDK는 64비트 및 32비트 애플리케이션을 빌드하는 데 사용할 수 있습니다. 필요한 라이브러리 및 헤더 파일은 https://aka.ms/csspeech/linuxbinary에서 tar 파일로 다운로드할 수 있습니다.

SDK를 다음과 같이 다운로드하고 설치합니다.

1. Speech SDK 파일을 추출할 디렉터리를 선택하고, 해당 디렉터리를 가리키도록 `SPEECHSDK_ROOT` 환경 변수를 설정합니다. 이 변수는 이후 명령에서 디렉터리를 쉽게 참조할 수 있게 해줍니다. 예를 들어 홈 디렉터리에서 `speechsdk` 디렉터리를 사용하려는 경우 다음과 비슷한 명령을 사용합니다.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 아직 디렉터리가 없으면 디렉터리를 만듭니다.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK 이진 파일이 들어 있는 `.tar.gz` 아카이브를 다운로드하여 추출합니다.

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 추출된 패키지의 최상위 디렉터리의 내용을 확인합니다.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   디렉터리 목록에는 헤더(`.h`) 파일을 포함하고 있는 `include` 디렉터리와 라이브러리를 포함하고 있는 `lib` 디렉터리뿐 아니라 타사 통지 및 라이선스 파일이 들어 있습니다.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]
