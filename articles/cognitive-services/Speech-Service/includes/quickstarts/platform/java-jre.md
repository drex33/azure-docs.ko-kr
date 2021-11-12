---
title: '빠른 시작: Java(Windows, Linux, macOS)용 Speech SDK 플랫폼 설정 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 사용하여 Speech Service SDK와 함께 Java(Windows, Linux, macOS)를 사용하기 위한 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: eur
ms.openlocfilehash: 9f0323d92a32938fd8850a06d0f86b034a59dd4b
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252507"
---
이 가이드에서는 64비트 Java 8 JRE용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다. 패키지 이름을 직접 시작하려는 경우에는 Maven 중앙 리포지토리에 Java SDK가 없습니다. Gradle 또는 `pom.xml` 종속성 파일을 사용하는 경우 `https://csspeechstorage.blob.core.windows.net/maven/`을 가리키는 사용자 지정 리포지토리를 추가해야 합니다(패키지 이름은 아래 참조).

> [!NOTE]
> Speech Devices SDK 및 Roobo 디바이스에 대한 내용은 [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md)를 참조하세요.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>지원되는 운영 체제

- Java Speech SDK 패키지는 다음과 같은 운영 체 제용으로 제공됩니다.
  - Windows: 64비트 전용
  - Mac: macOS X 버전 10.14 이상
  - Linux; [지원되는 Linux 배포 및 대상 아키텍처](~/articles/cognitive-services/speech-service/speech-sdk.md)의 목록을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 또는 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/)(Java 사전 설치 필요)

- Windows의 경우 플랫폼에 맞는 [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)가 필요합니다. 처음 설치하려면 다시 시작해야 할 수 있습니다.

- Linux의 경우 [시스템 요구 사항 및 설치 지침](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)을 참조하세요.

## <a name="gradle-config"></a>Gradle 구성

Gradle 구성에는 사용자 지정 리포지토리와 종속성 확장 `.jar`에 대한 명시적 참조가 모두 필요합니다.

```groovy
// build.gradle

repositories {
    maven {
        url "https://csspeechstorage.blob.core.windows.net/maven/"
    }
}

dependencies {
    implementation group: 'com.microsoft.cognitiveservices.speech', name: 'client-sdk', version: "1.18.0", ext: "jar"
}
```

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Eclipse 프로젝트 만들기 및 Speech SDK 설치

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
