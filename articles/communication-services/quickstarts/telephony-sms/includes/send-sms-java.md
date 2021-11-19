---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: c0225c1193cd7e3664b9a7f21fa0337be8cb384a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132530111"
---
Communication Services Java SMS SDK를 사용하여 SMS 메시지를 보내 Azure Communication Services를 시작합니다.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/send-sms-quickstart)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- SMS 지원 전화 번호입니다. [전화 번호를 가져옵니다](../get-phone-number.md).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `mvn -v`를 실행하여 maven이 설치되어 있는지 확인합니다.
- Communication Services 리소스와 연결된 전화 번호를 보려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 Communication Services 리소스를 찾아 왼쪽 탐색 창에서 **전화 번호** 탭을 엽니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 열고 Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 maven-archetype-quickstart 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 목표는 artifactId와 같은 이름으로 디렉터리를 만듭니다. 이 디렉터리 아래에서 **src/main/java** 디렉터리는 프로젝트 소스 코드를 포함하고, **src/test/java 디렉터리** 는 테스트 원본을 포함하고, **pom.xml** 파일은 프로젝트의 프로젝트 개체 모델 또는 POM입니다.

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 **pom.xml** 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

텍스트 편집기에서 **/src/main/java/com/communication/quickstart/App.java** 를 열고, 가져오기 지시문을 추가하고, `System.out.println("Hello world!");` 문을 제거합니다.

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Java용 Azure Communication Services SMS SDK의 주요 기능 중 일부를 처리합니다.

| Name                                                             | 설명                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | 이 클래스는 SmsClient를 만듭니다. 엔드포인트, 자격 증명 및 http 클라이언트를 제공합니다. |
| SmsClient                    | 이 클래스는 모든 SMS 기능에 필요합니다. SMS 메시지를 보내는 데 사용합니다.                |
| SmsSendOptions               | 이 클래스는 사용자 지정 태그를 추가하고 전달 보고를 구성하는 옵션을 제공합니다. deliveryReportEnabled가 true로 설정되면 전달이 성공했을 때 이벤트를 내보냅니다. |        
| SmsSendResult                | 이 클래스는 SMS 서비스의 결과를 포함합니다.                                          |

## <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `SmsClient`를 인스턴스화합니다. (자격 증명은 Azure Portal의 `Key`입니다. [리소스의 연결 문자열을 관리](../../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다. 또한 `com.azure.core.http.HttpClient` 인터페이스를 구현하는 사용자 지정 HTTP 클라이언트를 사용하여 클라이언트를 초기화할 수 있습니다.

`main` 메서드에 다음 코드를 추가합니다.

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .buildClient();
```

엔드포인트 및 액세스 키를 제공하는 대신 connectionString() 함수를 사용하여 전체 연결 문자열을 제공할 수도 있습니다.
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "endpoint=https://<resource-name>.communication.azure.com/;accesskey=<access-key>";

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>1:1 SMS 메시지 보내기

단일 수신자에게 SMS 메시지를 보내려면 단일 수신자 전화 번호로 SmsClient에서 `send` 메서드를 호출합니다. 선택적 매개 변수를 전달하여 전달 보고서를 사용하도록 설정할지 여부를 지정하고 사용자 지정 태그를 설정할 수도 있습니다.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

`<from-phone-number>`를 Communication Services 리소스와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

## <a name="send-a-1n-sms-message-with-options"></a>옵션이 포함된 1:N SMS 메시지 보내기
SMS 메시지를 수신자 목록에 보내려면 수신자 전화 번호 목록을 사용하여 `send` 메서드를 호출합니다. 선택적 매개 변수를 전달하여 전달 보고서를 사용하도록 설정할지 여부를 지정하고 사용자 지정 태그를 설정할 수도 있습니다.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.sendWithResponse(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE).getValue();

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

`<from-phone-number>`를 Communication Services 리소스와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number-1>` 및 `<to-phone-number-2>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

`setDeliveryReportEnabled` 메서드는 배달 보고를 구성하는 데 사용됩니다. 이 기능은 SMS 메시지가 전달될 때 이벤트를 내보내려는 시나리오에 유용합니다. SMS 메시지에 대한 전달 보고를 구성하려면 [SMS 이벤트 처리](../handle-sms-events.md)를 참조하세요.

`setTag` 메서드는 배달 보고서에 태그를 적용하는 데 사용됩니다.

## <a name="run-the-code"></a>코드 실행

**pom.xml** 파일이 포함된 디렉터리로 이동하고 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

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
