---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 2efbb9772e70afd699d467668fd1eadd69502b04
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461213"
---
## <a name="set-up-prerequisites"></a>필수 조건 설정

- [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-install), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).

## <a name="set-up"></a>설정

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 엽니다. Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 `maven-archetype-quickstart` 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 작업에서 `artifactId`와 동일한 이름으로 디렉터리를 만들었습니다. 이 디렉터리 아래의 `src/main/java` 디렉터리는 프로젝트 소스 코드를 포함하고, `src/test/java directory` 디렉터리는 테스트 원본을 포함하고, `pom.xml` 파일은 프로젝트의 프로젝트 개체 모델 또는 POM입니다.

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 `pom.xml` 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-communication-identity</artifactId>
        <version>1.2.0-beta.1</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>msal4j</artifactId>
      <version>1.11.0</version>
    </dependency>
</dependencies>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. `/src/main/java/com/communication/quickstart` 디렉터리로 이동합니다.
1. 편집기에서 `App.java` 파일을 엽니다.
1. `System.out.println("Hello world!");` 문 바꾸기
1. `import` 지시문 추가

시작하려면 다음 코드를 사용합니다.

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.InteractiveRequestParameters;
import com.microsoft.aad.msal4j.PublicClientApplication;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Communication access token Quickstart");
        // Quickstart code goes here
    }
}
```

### <a name="step-1-receive-the-azure-active-directory-user-token-via-the-msal-library"></a>1단계: MSAL 라이브러리를 통해 Azure Active Directory 사용자 토큰 받기

토큰 교환 흐름의 첫 번째 단계는 [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md)를 사용하여 Teams 사용자에 대한 토큰을 가져오는 것입니다.

```java
String appId = "<contoso_application_id>";
String authority = "https://login.microsoftonline.com/<contoso_tenant_id>";

PublicClientApplication pca = PublicClientApplication.builder(appId)
        .authority(authority)
        .build();

String redirectUri = "http://localhost";
Set<String> scope = new HashSet<String>();
scope.add("https://auth.msft.communication.azure.com/VoIP");

InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI(redirectUri))
                    .scopes(scope)
                    .build();

IAuthenticationResult result = pca.acquireToken(parameters).get();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>2단계: CommunicationIdentityClient 초기화

리소스의 액세스 키 및 엔드포인트를 사용하여 `CommunicationIdentityClient`를 인스턴스화합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다. 또한 `com.azure.core.http.HttpClient` 인터페이스를 구현하는 사용자 지정 HTTP 클라이언트를 사용하여 클라이언트를 초기화할 수 있습니다.

`main` 메서드에 다음 코드를 추가합니다.

```java
//You can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>3단계: Teams 액세스 토큰에 대한 Azure AD 사용자 토큰 교환

`getTokenForTeamsUser` 메서드를 사용하여 Azure Communication Services SDK와 함께 사용할 수 있는 Teams 사용자에 대한 액세스 토큰을 발급합니다.

```java
var accessToken = communicationIdentityClient.getTokenForTeamsUser(result.accessToken());
System.out.println("Token: " + accessToken.getToken());
```

## <a name="run-the-code"></a>코드 실행

`pom.xml` 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

그런 다음, 패키지를 빌드합니다.

```console
mvn package
```

다음 `mvn` 명령을 실행하여 앱을 실행합니다.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
