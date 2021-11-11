---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 976edb8a7e06dcb917fe36ad12324b6b4a518a35
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252283"
---
:::row:::
    :::column span="3":::
        Android용 Java SDK는 필요한 라이브러리와 필요한 Android 권한을 포함하는 <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR(Android 라이브러리)</a>로 패키지됩니다. `https://csspeechstorage.blob.core.windows.net/maven/`의 Maven 리포지토리에서 패키지 `com.microsoft.cognitiveservices.speech:client-sdk:1.19.0`로 호스트됩니다. [GitHub 리포지토리를 검색](https://github.com/Azure-Samples/cognitive-services-speech-sdk/search?q=com.microsoft.cognitiveservices.speech%3Aclient-sdk)하 여 1.19.0이 최신 버전 인지 확인 합니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Android Studio 프로젝트의 패키지를 사용하는 경우 다음과 같이 변경합니다.

1. 프로젝트 수준 *build.gradle* 파일에서 `repositories` 섹션에 다음을 추가합니다.
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. 모듈 수준 *build.gradle* 파일에서 `dependencies` 섹션에 다음을 추가합니다.
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.19.0'
  ```

Java SDK는 [Speech Devices SDK](../speech-devices-sdk.md)에도 포함됩니다.

#### <a name="additional-resources"></a>추가 리소스

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 특정 Java 빠른 시작 소스 코드</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">JRE(Java Runtime) 빠른 시작 소스 코드</a>
