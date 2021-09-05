---
ms.openlocfilehash: 80522448fa77cb8f07ae89a28f78ce6417edf17d
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656956"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/use-managed-Identity)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>SDK 패키지 설치

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>새 파일 만들기

텍스트 편집기를 사용하여 새 파일을 열고 `index.js`로 저장하면 이 파일 내에 코드가 배치됩니다.

### <a name="use-the-sdk-packages"></a>SDK 패키지 사용

다음 `require` 지시문을 `index.js` 위에 추가하여 Azure ID 및 Azure Storage SDK를 사용합니다.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential 만들기

이 빠른 시작에서는 [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential)을 사용합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합합니다. 각 작업에 필요하므로 `index.js` 파일 위에서 만들겠습니다. 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>ID 만들기 및 서비스 주체로 토큰 발급

다음으로, 새 ID 만들고 이 ID에 대한 토큰을 발급하는 함수를 작성합니다. 나중에 이를 사용하여 서비스 주체 설정을 테스트합니다.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-service-principals"></a>서비스 주체와 SMS 보내기

이제 서비스 주체를 사용하여 SMS를 전송하는 함수를 작성해 보겠습니다.

```JavaScript
async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}
```

## <a name="write-the-main-function"></a>main 함수 작성

함수를 만든 후에는 주요 함수를 작성하여 이를 호출하고 서비스 주체의 사용을 보여줄 수 있습니다.
```JavaScript
async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Service Principals");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

최종 `index.js` 파일은 다음과 같이 표시됩니다.
```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");

const credential = new DefaultAzureCredential();

async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}

async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}

async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Service Principals");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

## <a name="run-the-program"></a>프로그램 실행

모든 작업이 완료되면 프로젝트의 디렉터리에서 `node index.js`를 입력하여 파일을 실행할 수 있습니다. 모든 것이 순조롭게 진행되면 다음과 같은 페이지가 표시됩니다.

```Bash
    $ node index.js
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
