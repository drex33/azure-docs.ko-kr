---
title: Speech CLI 빠른 시작 - Speech Service
titleSuffix: Azure Cognitive Services
description: Azure Speech CLI를 시작하세요. 코드를 작성하지 않고도 음성 텍스트 변환, 텍스트 음성 변환, 음성 번역 등의 Speech Service와 상호 작용할 수 있습니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/28/2021
ms.author: eur
ms.custom: mode-other
ms.openlocfilehash: d6e18183fc96bf88258b29ab1104c620cc76dc0f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133072530"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Azure Speech CLI 시작

이 문서에서는 코드를 작성하지 않고 Azure Speech CLI(명령줄 인터페이스)를 사용하여 음성 텍스트 변환, 텍스트 음성 변환, 음성 번역 등의 Speech Service에 액세스하는 방법에 대해 알아봅니다. Speech CLI는 프로덕션 환경에 즉시 사용할 수 있으며, `.bat` 또는 셸 스크립트를 사용하여 Speech Service에서 간단한 워크플로를 자동화할 수 있습니다.

이 문서에서는 사용자가 명령 프롬프트, 터미널 또는 PowerShell에 대한 실무 지식이 있다고 가정합니다.

> [!NOTE]
> PowerShell에서 [stop-parsing 토큰](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_special_characters?view=powershell-7.2#stop-parsing-token---)(`--%`)은 `spx` 다음에 와야 합니다. 예를 들어 `spx --% config @region`을 실행하여 현재 지역 구성 값을 확인합니다.

[!INCLUDE [](includes/spx-setup.md)]


## <a name="create-subscription-config"></a>구독 구성 만들기

# <a name="terminal"></a>[터미널](#tab/terminal)

시작하려면 Azure 구독 키 및 지역 식별자(예: `eastus`, `westus`)가 필요합니다. 이러한 자격 증명을 가져오는 단계는 [Speech Service 개요](overview.md#find-keys-and-locationregion) 설명서를 참조하세요.

터미널에서 다음 명령을 실행하여 구독 키와 지역 식별자를 구성합니다. 

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

키와 지역은 이후 Speech CLI 명령에 대해 저장됩니다. 다음 명령을 실행하여 현재 구성을 확인합니다.

```console
spx config @key
spx config @region
```

필요에 따라 저장된 값 중 하나를 제거하는 `clear` 옵션을 포함합니다.

```console
spx config @key --clear
spx config @region --clear
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

시작하려면 Azure 구독 키 및 지역 식별자(예: `eastus`, `westus`)가 필요합니다. 이러한 자격 증명을 가져오는 단계는 [Speech Service 개요](overview.md#find-keys-and-locationregion) 설명서를 참조하세요.

PowerShell에서 다음 명령을 실행하여 구독 키와 지역 식별자를 구성합니다. 

```powershell
spx --% config @key --set SUBSCRIPTION-KEY
spx --% config @region --set REGION
```

키와 지역은 이후 Speech CLI 명령에 대해 저장됩니다. 다음 명령을 실행하여 현재 구성을 확인합니다.

```powershell
spx --% config @key
spx --% config @region
```

필요에 따라 저장된 값 중 하나를 제거하는 `clear` 옵션을 포함합니다.

```powershell
spx --% config @key --clear
spx --% config @region --clear
```

***

## <a name="basic-usage"></a>기본 사용법

이 섹션에서는 처음으로 테스트 또는 실험을 수행할 때 도움이 되는 몇 가지 기본 SPX 명령을 보여줍니다. 다음 명령을 실행하여 도구에 기본 제공되는 도움말을 확인하세요.

```console
spx
```

키워드를 기준으로 도움말 항목을 검색할 수 있습니다. 예를 들어 다음 명령을 실행하여 Speech CLI 사용 예제 목록을 확인합니다.

```console
spx help find --topics "examples"
```

다음 명령을 실행하여 recognize 명령에 대한 옵션을 표시합니다.

```console
spx help recognize
```

추가 도움말 명령이 콘솔 출력에 나열됩니다. 이러한 명령을 입력하여 하위 명령에 대한 자세한 도움말을 가져올 수 있습니다.

## <a name="speech-to-text-speech-recognition"></a>음성 - 텍스트 변환(음성 인식)

이 명령을 실행하여 시스템의 기본 마이크를 통해 음성을 텍스트로 변환(음성 인식)합니다. 

```console
spx recognize --microphone
```

명령을 입력하면 SPX는 현재 활성 입력 디바이스에서 오디오 수신 대기를 시작하고, 사용자가 **ENTER** 를 누르면 중지됩니다. 그러면 음성 오디오가 인식되고 콘솔 출력의 텍스트로 변환됩니다.

Speech CLI를 사용하면 오디오 파일의 음성을 인식할 수도 있습니다.

```console
spx recognize --file /path/to/file.wav
```

> [!NOTE]
> Docker 컨테이너를 사용하는 경우에는 `--microphone` 명령이 작동하지 않습니다.
> 
> Docker 컨테이너의 오디오 파일에서 음성을 인식하는 경우 오디오 파일이 이전 단계에서 탑재한 디렉터리에 있는지 확인합니다.

> [!TIP]
> 문제가 있거나 Speech CLI의 인식 옵션에 대해 자세히 알아보려면 ```spx help recognize```를 실행하면 됩니다.

## <a name="text-to-speech-speech-synthesis"></a>텍스트 음성 변환(음성 합성)

다음 명령을 실행하면 입력한 텍스트가 입력으로 사용되고, 합성된 음성이 현재 활성 출력 디바이스(예: 컴퓨터 스피커)로 출력됩니다.

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

합성된 출력을 파일에 저장할 수도 있습니다. 이 예제에서는 명령이 실행되는 디렉터리에 `my-sample.wav`라는 파일을 만들겠습니다.

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

이 예제에서는 영어로 테스트하는 것으로 가정합니다. 그러나 대부분의 언어에서 음성 합성이 지원됩니다. 이 명령을 사용하거나 [언어 지원 페이지](./language-support.md)를 방문하여 음성 전체 목록을 가져올 수 있습니다.

```console
spx synthesize --voices
```

검색한 음성 중 하나를 사용하는 방법은 다음과 같습니다.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

> [!TIP]
> 문제가 있거나 Speech CLI의 인식 옵션에 대해 자세히 알아보려면 ```spx help synthesize```를 실행하면 됩니다.

## <a name="speech-to-text-translation"></a>음성을 텍스트로 번역

Speech CLI를 사용하여 음성을 텍스트로 번역할 수도 있습니다. 이 명령을 실행하여 기본 마이크로 오디오를 캡처하고, 번역된 내용을 텍스트로 출력합니다. `translate` 명령을 사용하여 `source` 및 `target` 언어를 제공해야 한다는 점을 기억하세요.

```console
spx translate --microphone --source en-US --target ru-RU
```

여러 언어로 번역하는 경우 언어 코드를 `;`으로 구분합니다.

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

번역 출력을 저장하려면 `--output` 플래그를 사용합니다. 이 예제에서는 파일의 음성도 읽을 것입니다.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> 지원되는 언어와 해당 로캘 코드 목록은 [언어 및 로캘 문서](language-support.md)를 참조하세요.

> [!TIP]
> 문제가 있거나 Speech CLI의 인식 옵션에 대해 자세히 알아보려면 ```spx help translate```를 실행하면 됩니다.


## <a name="next-steps"></a>다음 단계

* [MP3 및 기타 형식에서 Speech CLI를 사용하도록 GStreamer 설치](./how-to-use-codec-compressed-audio-input-streams.md)
* [Speech CLI 구성 옵션](./spx-data-store-configuration.md)
* [Speech CLI를 사용한 일괄 처리 작업](./spx-batch-operations.md)
