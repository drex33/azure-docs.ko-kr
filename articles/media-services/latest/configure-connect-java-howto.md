---
title: Azure Media Services v3 API에 연결 - Java
description: 이 문서에서는 Java를 사용하여 Azure Media Services v3 API에 연결하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java, mode-other
ms.author: inhenkel
ms.openlocfilehash: 01e2321512180039ed4a9152bb6ecafea5051f28
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055960"
---
# <a name="connect-to-media-services-v3-api---java"></a>Media Services v3 API에 연결 - Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 사용자 로그인 메서드를 사용하여 Azure Media Services v3 Java SDK에 연결하는 방법을 보여줍니다.

이 문서에서 Visual Studio Code는 샘플 앱을 개발하는 데 사용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio Code를 사용하여 Java 작성](https://code.visualstudio.com/docs/java/java-tutorial)을 따라 설치합니다.

   - JDK
   - Apache Maven
   - Java 확장 팩
- `JAVA_HOME` 및 `PATH` 환경 변수를 설정해야 합니다.
- [Media Services 계정 만들기](./account-create-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.
- [액세스 API](./access-api-howto.md) 항목의 단계를 따릅니다. 이후 단계에서 필요한 구독 ID, 애플리케이션 ID(클라이언트 ID), 인증 키(비밀) 및 테넌트 ID를 기록합니다.

또한 다음을 검토합니다.

- [Visual Studio Code의 Java](https://code.visualstudio.com/docs/languages/java)
- [VS Code의 Java 프로젝트 관리](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토합니다.

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

명령줄 도구 및 프로젝트를 생성할 디렉터리로 `cd`를 엽니다.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

명령을 실행하면 `pom.xml`, `App.java` 및 기타 파일이 만들어집니다. 

## <a name="add-dependencies"></a>종속성 추가

1. Visual Studio Code에서 프로젝트가 있는 폴더를 엽니다.
1. `pom.xml`을 찾아 엽니다.
1. 필요한 종속성을 추가합니다.

   [비디오 인코딩](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml) 샘플에서 `pom.xml`을 참조하세요.

## <a name="connect-to-the-java-client"></a>Java 클라이언트에 연결

1. `src\main\java\com\azure\ams`에서 `App.java` 파일을 열고 패키지가 상단에 포함되어 있는지 확인합니다.

    ```java
    package com.azure.ams;
    ```
1. 패키지 문에서 다음과 같은 import 문을 추가합니다.
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. 요청을 해야 하는 Active Directory 자격 증명을 만들려면 App 클래스의 주 메서드에 다음 코드를 추가하고 [액세스 API](./access-api-howto.md)에서 가져온 값을 설정합니다.
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. 앱을 실행합니다.

## <a name="see-also"></a>추가 정보

- [Media Services 개념](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java 참조](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>다음 단계

이제 `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;`를 포함하고 엔터티 조작을 시작할 수 있습니다.

더 많은 코드 예제는 [Java SDK 샘플](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) 리포지토리를 참조하세요.
