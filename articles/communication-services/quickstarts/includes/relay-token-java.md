---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/20/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 9e55af5bed17c1701a443c934fc6e356fa63c032
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893247"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/relay-token-quickstart)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites-for-java"></a>Java에 대한 필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-install), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- 배포된 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 엽니다. Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 maven-archetype-quickstart 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 작업에서 `artifactId`와 동일한 이름으로 디렉터리를 만들었습니다. 이 디렉터리 아래에서 src/main/java 디렉터리는 프로젝트 소스 코드를 포함하고, `src/test/java directory`는 테스트 원본을 포함하고, `pom.xml` 파일은 프로젝트의 프로젝트 개체 모델 또는 POM입니다.

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 **pom.xml** 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정 

프로젝트 디렉터리에서 다음을 수행합니다.

1. `/src/main/java/com/communication/quickstart` 디렉터리로 이동합니다.
2. 편집기에서 `App.java` 파일을 엽니다.
3. `System.out.println("Hello world!");` 문 바꾸기
4. `import` 지시문 추가

시작하려면 다음 코드를 사용합니다.

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.communication.network.traversal.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Network Credentials Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>클라이언트 인증

리소스의 액세스 키 및 엔드포인트를 사용하여 `CommunicationIdentityClient`를 인스턴스화합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다. 또한 `com.azure.core.http.HttpClient` 인터페이스를 구현하는 사용자 지정 HTTP 클라이언트를 사용하여 클라이언트를 초기화할 수 있습니다.

`main` 메서드에 다음 코드를 추가합니다.

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationRelayClient communicationRelayClient = new CommunicationRelayClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();

```

엔드포인트 및 액세스 키를 제공하는 대신 `connectionString()` 함수를 사용하여 전체 연결 문자열을 제공할 수도 있습니다.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationRelayClient communicationRelayClient = new CommunicationRelayClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

## <a name="create-an-identity"></a>ID 만들기

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `createUser` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션 사용자에게 매핑하여 수신된 ID를 저장합니다. 예를 들어 애플리케이션 서버의 데이터베이스에 저장합니다. ID는 나중에 액세스 토큰을 발급하는 데 필요합니다.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="getting-the-relay-configuration"></a>릴레이 구성 가져오기
Azure Communication 토큰 서비스를 호출하여 TURN 서비스 토큰의 사용자 액세스 토큰을 교환합니다.

```java
CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
            .connectionString(connectionString)
            .buildClient();

CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("User id: " + user.getId());

CommunicationRelayConfiguration config = communicationRelayClient.getRelayConfiguration(user);
        
        System.out.println("Expires on:" + config.getExpiresOn());
        List<CommunicationIceServer> iceServers = config.getIceServers();

        for (CommunicationIceServer iceS : iceServers) {
            System.out.println("URLS: " + iceS.getUrls());
            System.out.println("Username: " + iceS.getUsername());
            System.out.println("Credential: " + iceS.getCredential());
        } 
```

## <a name="run-the-code"></a>코드 실행

*pom.xml* 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

```console
mvn compile
```

그런 다음, 패키지를 빌드합니다.

```console
mvn package
```

다음 `mvn` 명령을 실행하여 앱을 실행합니다.

*pom.xml* 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

```console
mvn compile
```

그런 다음, 패키지를 빌드합니다.

```console
mvn package
```

다음 `mvn` 명령을 실행하여 앱을 실행합니다.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
