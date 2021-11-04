---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.openlocfilehash: 1159fac39df141e92f539063254d5166fba53bc9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502556"
---
이 가이드에서는 Python용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다. 패키지 이름을 직접 시작하려면 `pip install azure-cognitiveservices-speech`를 실행합니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>지원되는 운영 체제

- Python Speech SDK 패키지는 다음과 같은 운영 체제용으로 제공됩니다.
  - Windows: x64 및 x86
  - Mac: macOS X 버전 10.12 이상
  - Linux; [지원되는 Linux 배포 및 대상 아키텍처](~/articles/cognitive-services/speech-service/speech-sdk.md)의 목록을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Windows의 경우 플랫폼에 맞는 [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)가 필요합니다. 처음 설치하려면 다시 시작해야 할 수 있습니다.

- Linux의 경우 [시스템 요구 사항 및 설치 지침](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)을 참조하세요.

- 마지막으로 [Python 3.6~3.9](https://www.python.org/downloads/)가 필요합니다. 설치를 확인하려면 명령 프롬프트를 열어 `python --version` 명령을 입력하고 결과를 확인합니다. 올바르게 설치된 경우 "Python 3.8.8"과 같은 응답을 얻게 됩니다.

## <a name="install-the-speech-sdk-from-pypi"></a>PyPI에서 Speech SDK 설치

고유한 환경 또는 빌드 도구를 사용하는 경우 다음 명령을 실행하여 [PyPI](https://pypi.org/)에서 Speech SDK를 설치합니다. Visual Studio Code 사용자의 경우 가이드가 안내하는 설치에 대한 다음 하위 섹션으로 건너뜁니다.

```sh
pip install azure-cognitiveservices-speech
```

macOS를 사용하는 경우 위의 `pip` 명령을 실행하려면 다음 명령을 실행해야 할 수 있습니다.

```sh
python3 -m pip install --upgrade pip
```

`pip`를 사용하여 `azure-cognitiveservices-speech`를 성공적으로 설치한 후에는 Python 프로젝트에 네임스페이스를 가져와서 Speech SDK를 사용할 수 있습니다.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Visual Studio Code를 사용하여 Speech SDK 설치

1. 현재 플랫폼에 지원되는 최신 버전의 [Python](https://www.python.org/downloads/)(3.6~3.9)을 다운로드하여 설치합니다.
   - Windows 사용자는 설치 프로세스 중 "경로에 Python 추가"를 선택해야 합니다.
1. [Visual Studio Code](https://code.visualstudio.com/Download)를 다운로드하여 설치합니다.
1. Visual Studio Code를 열고 Python 확장을 설치합니다. 메뉴에서 **파일** > **기본 설정** > **확장** 을 선택합니다. **Python** 을 검색하고 **설치** 를 클릭합니다.

   ![Python 확장 설치](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Visual Studio Code 내에서도 통합 명령줄을 통해 Speech SDK Python 패키지를 설치합니다.
   1. 터미널을 엽니다(드롭다운 메뉴에서 **터미널** > **새 터미널**).
   1. 열리는 터미널에서 `python -m pip install azure-cognitiveservices-speech` 명령을 입력합니다.

Visual Studio Code를 처음 접하는 경우 더 광범위한 [Visual Studio Code 설명서](https://code.visualstudio.com/docs)를 참조하세요. Visual Studio Code 및 Python에 대한 자세한 내용은 [Visual Studio Code Python 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하세요.

## <a name="support-and-updates"></a>지원 및 업데이트

Speech SDK Python 패키지에 대한 업데이트는 PyPI를 통해 배포되며, [릴리스 정보](~/articles/cognitive-services/speech-service/releasenotes.md)에서 발표됩니다.
새 버전을 사용할 수 있는 경우 `pip install --upgrade azure-cognitiveservices-speech` 명령을 사용하여 업데이트할 수 있습니다.
`azure.cognitiveservices.speech.__version__` 변수를 검사하여 현재 설치된 버전을 확인하세요.

문제가 있거나 기능이 누락된 경우 [지원 및 도움말 옵션](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
