---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: c63a3859f9ea2fcec88ee220c0abca8429d9b86e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132530219"
---
Communication Services JavaScript SMS SDK를 사용하여 SMS 메시지를 보내 Azure Communication Services를 시작합니다.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/send-sms)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- SMS 지원 전화 번호입니다. [전화 번호를 가져옵니다](../get-phone-number.md).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `node --version`를 실행하여 Node.js가 설치되어 있는지 확인합니다.
- Communication Services 리소스와 연결된 전화 번호를 보려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 Communication Services 리소스를 찾아 왼쪽 탐색 창에서 **전화 번호** 탭을 엽니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

먼저 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir sms-quickstart && cd sms-quickstart
```

`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **send-sms.js** 라는 파일을 만듭니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

### <a name="install-the-package"></a>패키지 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services SMS SDK를 설치합니다.

```console
npm install @azure/communication-sms --save
```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Node.js용 Azure Communication Services SMS SDK의 주요 기능 중 일부를 처리합니다.

| Name                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | 이 클래스는 모든 SMS 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 SMS 메시지를 보내는 데 사용합니다. |
| SmsSendRequest | 이 인터페이스는 sms 요청을 빌드하기 위한 모델입니다(예: 전화 번호와 sms 콘텐츠를 주고 받도록 구성). |
| SmsSendOptions | 이 인터페이스는 전달 보고를 구성하는 옵션을 제공합니다. `enableDeliveryReport`가 `true`로 설정된 경우 전달이 성공하면 이벤트를 내보냅니다. |
| SmsSendResult               | 이 클래스는 SMS 서비스의 결과를 포함합니다.                                          |

## <a name="authenticate-the-client"></a>클라이언트 인증

SDK에서 **SmsClient** 를 가져와서 연결 문자열로 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

**send-sms.js** 라는 파일을 만들고, 열고 다음 코드를 추가합니다.

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>1:N SMS 메시지 보내기

SMS 메시지를 수신자 목록에 보내려면 수신자 전화 번호 목록을 사용하여 SmsClient에서 `send` 함수를 호출합니다(단일 수신자에게 메시지를 보내려면 목록에 하나의 번호만 포함). 다음 코드를 **send-sms.js** 끝에 추가합니다.

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
`<from-phone-number>`를 Communication Services 리소스와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number-1>` 및 `<to-phone-number-2>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

## <a name="send-a-1n-sms-message-with-options"></a>옵션이 포함된 1:N SMS 메시지 보내기

옵션 개체를 전달하여 전달 보고서를 사용하도록 설정할지 여부를 지정하고 사용자 지정 태그를 설정할 수도 있습니다.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

`<from-phone-number>`를 Communication Services 리소스와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number-1>` 및 `<to-phone-number-2>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

`enableDeliveryReport` 매개 변수는 전달 보고를 구성하는 데 사용할 수 있는 선택적 매개 변수입니다. 이 기능은 SMS 메시지가 전달될 때 이벤트를 내보내려는 시나리오에 유용합니다. SMS 메시지에 대한 전달 보고를 구성하려면 [SMS 이벤트 처리](../handle-sms-events.md)를 참조하세요.
`tag`는 전달 보고서에 태그를 적용하는 데 사용할 수 있는 선택적 매개 변수입니다.

## <a name="run-the-code"></a>코드 실행

`node` 명령을 사용하여 **send-sms.js** 파일에 추가한 코드를 실행합니다.

```console

node ./send-sms.js

```
