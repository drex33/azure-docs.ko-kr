---
title: Speech SDK를 사용하여 코덱 압축 오디오 스트리밍 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 압축된 오디오를 Speech Service로 스트리밍하는 방법에 대해 알아봅니다. C++, C# 및 Linux용 Java, Android의 Java 및 iOS의 Objective-C에서 사용할 수 있습니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-eight
ms.openlocfilehash: c84b4c31d4e2e87781d13675f4d45fb1c22edb7a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156681"
---
# <a name="use-codec-compressed-audio-input"></a>코덱 압축 오디오 입력 사용

Speech SDK 및 Speech CLI는 GStreamer를 사용하여 압축된 오디오 형식을 수락할 수 있습니다. GStreamer는 원시 PCM으로 유선을 통해 Speech Service에 전송되기 전에 오디오의 압축을 해제합니다.

플랫폼 | 언어 | 지원되는 GStreamer 버전
| :--- | ---: | :---:
Linux  | C++, C#, Java, Python, Go | [지원되는 Linux 배포판 및 대상 아키텍처](~/articles/cognitive-services/speech-service/speech-sdk.md) 
Windows(UWP 제외) | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi) 
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/) 

## <a name="installing-gstreamer-on-linux"></a>Linux에 GStreamer 설치

자세한 내용은 [Linux 설치 지침 를 참조하세요.](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)  

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```
## <a name="installing-gstreamer-on-windows"></a>Windows GStreamer 설치

자세한 내용은 [Windows 설치 지침을 참조하세요.](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) 

* c:\gstreamer 폴더 만들기
* [설치 관리자 다운로드](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi) 
* c:\gstreamer에 설치 관리자 복사
* 관리자 권한으로 PowerShell을 엽니다.
* PowerShell에서 다음 명령을 실행합니다.

```powershell
cd c:\gstreamer
msiexec /passive INSTALLLEVEL=1000 INSTALLDIR=C:\gstreamer /i gstreamer-1.0-msvc-x86_64-1.18.3.msi
```
* 값이 C:\gstreamer\1.0\msvc_x86_64\lib\gstreamer-1.0인 GST_PLUGIN_PATH 시스템 변수를 추가합니다.
* 값이 C:\gstreamer\1.0\GSTREAMER_ROOT_X86_64 시스템 변수를 추가합니다msvc_x86_64
* 경로 변수에 다른 항목을 C:\gstreamer\1.0\msvc_x86_64\bin으로 추가합니다.
* 컴퓨터를 다시 부팅합니다.

## <a name="using-gstreamer-in-android"></a>Android에서 GStreamer 사용
libgstreamer_android 빌드에 대한 자세한 내용은 위의 Java 탭을 참조하세요. 

자세한 내용은 [Android 설치 지침을 참조하세요.](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c) 

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>압축 오디오 입력에 필요한 Speech SDK 버전
* RHEL 8 및 CentOS 8에는 Speech SDK 버전 1.10.0 이상이 필요합니다.
* speech SDK 버전 1.11.0 이상은 Windows 필요합니다.
* Windows 및 Android의 최신 GStreamer용 Speech SDK 버전 1.16.0 이상

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>압축된 오디오를 처리하는 데 필요한 GStreamer

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용하는 예제 코드

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/examples.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 인식 방법 알아보기](./get-started-speech-to-text.md)
