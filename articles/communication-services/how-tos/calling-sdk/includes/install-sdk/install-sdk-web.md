---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a671eb00f2108b96220e72a32f8dd9f5654a4224
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128634100"
---
## <a name="install-the-sdk"></a>SDK 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 통화 SDK 및 일반 SDK를 설치합니다.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

## <a name="initialize-required-objects"></a>필요한 개체 초기화

CallClient 인스턴스는 대부분의 호출 작업에 필요합니다. 새 인스턴스를 만들어 `CallClient` 보겠습니다. 로거 인스턴스와 같은 사용자 지정 옵션을 사용하여 구성할 수 있습니다.

`CallClient` 인스턴스가 있으면 `CallClient` 인스턴스에서 `createCallAgent` 메서드를 호출하여 `CallAgent` 인스턴스를 만들 수 있습니다. 이 메서드는 `CallAgent` 인스턴스 개체를 비동기적으로 반환됩니다.

`createCallAgent` 메서드는 `CommunicationTokenCredential`을 인수로 사용합니다. [사용자 액세스 토큰](../../../../quickstarts/access-tokens.md)이 허용됩니다.

`CallClient` 인스턴스에서 `getDeviceManager` 메서드를 사용하여 `deviceManager`에 액세스할 수 있습니다.

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");

// Set the logger's log level
setLogLevel('verbose');

// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};

const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```