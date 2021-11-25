---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 11/17/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: fd79bcf1b288cf2fb8a63b9f94966f9447ef52cb
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132870427"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-install), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- 배포된 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).

## <a name="final-code"></a>최종 코드
[GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/access-token-quickstart)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 엽니다. Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 maven-archetype-quickstart 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 작업에서 `artifactId`와 동일한 이름으로 디렉터리를 만들었습니다. 이 디렉터리 아래에서 src/main/java 디렉터리는 프로젝트 소스 코드를 포함하고, `src/test/java directory`는 테스트 원본을 포함하고, `pom.xml` 파일은 프로젝트의 프로젝트 개체 모델 또는 POM입니다. 이 파일은 프로젝트 구성 매개 변수에 사용됩니다.

### <a name="install-the-acs-packages"></a>ACS 패키지 설치

텍스트 편집기에서 **pom.xml** 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
```

그러면 나중에 사용할 때 Maven에 ACS ID SDK를 설치하도록 지시합니다.

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

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>클라이언트 인증

리소스의 액세스 키 및 엔드포인트를 사용하여 `CommunicationIdentityClient`를 인스턴스화합니다. [리소스의 연결 문자열을 관리](../../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다. 또한 `com.azure.core.http.HttpClient` 인터페이스를 구현하는 사용자 지정 HTTP 클라이언트를 사용하여 클라이언트를 초기화할 수 있습니다.

다음 코드를 `App.java` 내의 `main` 메서드에 추가합니다.

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .buildClient();
```

엔드포인트 및 액세스 키를 제공하는 대신 `connectionString()` 메서드를 사용하여 전체 연결 문자열을 제공할 수도 있습니다.
```java
// Your can find your connection string from your ACS resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

Azure AD(Azure Active Directory) 애플리케이션이 설정되어 있는 경우 [서비스 주체 사용](../../identity/service-principal.md)을 참조하여 Azure AD로 인증할 수도 있습니다.
```java
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
TokenCredential credential = new DefaultAzureCredentialBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(credential)
        .buildClient();
```

## <a name="create-an-identity"></a>ID 만들기

액세스 토큰을 만들려면 ID가 필요합니다. Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `createUser` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다.
```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

생성된 ID는 나중에 액세스 토큰을 발급하는 데 필요합니다. 따라서 받은 ID는 애플리케이션의 사용자에 대한 매핑과 함께 저장해야 합니다. 예를 들어 애플리케이션 서버의 데이터베이스에 저장합니다. 

## <a name="issue-access-tokens"></a>액세스 토큰 발급

`getToken` 메서드를 사용하여 이미 존재하는 Communication Services ID에 대한 액세스 토큰을 발급합니다. `scopes` 매개 변수는 이 토큰이 수행할 수 있는 권한 및 기능 세트를 정의합니다. 유효한 값은 [지원되는 작업 목록](../../../concepts/authentication.md)을 참조하세요. 이 경우 이전 단계에서 만든 사용자 변수를 사용하여 토큰을 가져옵니다.

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-request"></a>한 번의 요청으로 ID 생성 및 토큰 발급

또는 'createUserAndToken' 메서드를 사용하여 디렉터리에서 고유한 `Id`로 새 항목을 만들고 액세스 토큰을 발급할 수 있습니다.

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

액세스 토큰은 다시 발급해야 하는 단기 자격 증명입니다. 이렇게 하지 않으면 애플리케이션의 사용자 환경이 중단될 수 있습니다. `expiresAt` 속성은 액세스 토큰의 수명을 나타냅니다.

## <a name="refresh-access-tokens"></a>액세스 토큰 새로 고침

액세스 토큰을 새로 고치려면 `CommunicationUserIdentifier` 개체를 사용하여 다시 발급합니다.

```java
// existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity.getId());
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-an-access-token"></a>액세스 토큰 해지

경우에 따라 액세스 토큰을 명시적으로 취소할 수 있습니다. 예를 들어 애플리케이션의 사용자가 서비스에 인증하는 데 사용하는 암호를 변경하는 경우입니다. `revokeTokens` 메서드는 특정 사용자에 대한 모든 활성 액세스 토큰을 무효화합니다. 이 경우 이전에 만든 사용자를 다시 사용합니다.

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>ID 삭제

ID를 삭제하면 모든 활성 액세스 토큰이 취소되고 ID에 대한 액세스 토큰을 발급할 수 없게 됩니다. 또한 ID와 연결된 모든 지속형 콘텐츠를 제거합니다.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>코드 실행

`pom.xml` 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

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
