---
ms.openlocfilehash: 4ca3be985b0f4821e18607a5815c0237ad3bf0f0
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656983"
---
## <a name="additional-prerequisites-for-java"></a>Java에 대한 추가 필수 구성 요소
Java의 경우 다음도 필요합니다.
- [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-install), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/use-managed-Identity)에서 이 빠른 시작에 대한 최종 코드 칮기

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
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-sdk-packages"></a>SDK 패키지 사용

Azure ID 및 Azure Communication SDK를 사용하려면 다음 `import` 지시문을 코드에 추가합니다.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;
```

## <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential 만들기

이 빠른 시작에서는 [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential)을 사용합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합합니다. 각 작업에 필요하므로 `App.java` 클래스 내에서 만들겠습니다. `App.java` 클래스의 맨 위에 다음을 추가합니다.

```java
private TokenCredential credential = new DefaultAzureCredentialBuilder().build();
```

## <a name="issue-a-token-with-service-principals"></a>서비스 주체를 사용하여 토큰 발급

이제 생성된 자격 증명을 사용하는 코드를 추가하여 VoIP 액세스 토큰을 발급합니다. 나중에 이 코드를 호출합니다.

```java
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }
```

## <a name="send-an-sms-with-service-principals"></a>서비스 주체를 사용하여 SMS 보내기

서비스 주체를 사용하는 또 다른 예로, 동일한 자격 증명을 사용하여 SMS를 보내는 다음 코드를 추가합니다.

```java
     public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
     }
```
## <a name="write-the-main-method"></a>Main 메서드 쓰기

`App.java`에는 이미 Main 메서드가 있어야 합니다. 이전에 만든 코드를 호출하여 서비스 주체를 사용하는 방법을 보여 주는 코드를 추가해 보겠습니다.
```java
    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Service Principals");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Service Principals");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
```

최종 `App.java`는 다음과 같아야 합니다.

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;

public class App 
{

    private TokenCredential credential = new DefaultAzureCredentialBuilder().build();

    public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(this.credential)
               .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
    }
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }

    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Service Principals");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Service Principals");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
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

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

최종 출력은 다음과 유사합니다.
```
Retrieving new Access Token, using Service Principals
Retrieved Access Token: ey..A
Sending SMS using using Service Principals
Sms id: Outgoing_202104...33f8ae1f_noam
Send Result Successful: true
```