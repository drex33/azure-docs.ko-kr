---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 41f8fc2adefd29bb7cfda31e3dbaa83de599b154
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838733"
---
## <a name="install-the-sdk"></a>SDK 설치

프로젝트 수준 build.gradle을 찾고, `mavenCentral()`을 `buildscript` 및 `allprojects` 아래의 리포지토리 목록에 추가해야 합니다.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
그런 다음, 모듈 수준 build.gradle에서 dependencies 섹션에 다음 줄을 추가합니다.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```

### <a name="initialize-the-required-objects"></a>필요한 개체 초기화

`CallAgent` 인스턴스를 만들려면 `CallClient` 인스턴스에서 `createCallAgent` 메서드를 호출해야 합니다. 이렇게 하면 `CallAgent` 인스턴스 개체가 비동기적으로 반환됩니다.
`createCallAgent` 메서드는 `CommunicationUserCredential`을 인수로 사용하여 [액세스 토큰](../../../../quickstarts/access-tokens.md)을 캡슐화합니다.
`DeviceManager`에 액세스하려면 먼저 callAgent 인스턴스를 만든 다음, `CallClient.getDeviceManager` 메서드를 사용하여 DeviceManager를 가져오면 됩니다.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
호출자의 표시 이름을 설정하려면 다음과 같은 대체 방법을 사용합니다.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```