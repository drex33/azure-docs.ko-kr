---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: aabbeab268c9b1ab45dc011dee41fcf088f01126
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385336"
---
Communication Services C# SMS SDK를 사용하여 SMS 메시지를 보내 Azure Communication Services를 시작합니다.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 운영 체제에 대한 최신 버전의 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- SMS 지원 전화 번호입니다. [전화 번호를 가져옵니다](../../telephony/get-phone-number.md).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `dotnet` 명령을 실행하여 .NET SDK가 설치되어 있는지 확인합니다.
- Communication Services 리소스와 연결된 전화 번호를 보려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 Communication Services 리소스를 찾아 왼쪽 탐색 창에서 **전화 번호** 탭을 엽니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `SmsQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o SmsQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `dotnet add package` 명령을 사용하여 .NET 패키지용 Azure Communication Services SMS SDK를 설치합니다.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

**Program.cs** 의 맨 위에 `using` 지시어를 추가하여 `Azure.Communication` 네임스페이스를 포함합니다.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 C#용 Azure Communication Services SMS SDK의 주요 기능 중 일부를 처리합니다.

| Name                                       | 설명                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | 이 클래스는 모든 SMS 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 SMS 메시지를 보내는 데 사용합니다.                           |
| SmsSendOptions | 이 클래스는 전달 보고를 구성하는 옵션을 제공합니다. enable_delivery_report가 True로 설정되면 전달이 성공했을 때 이벤트를 내보냅니다. |
| SmsSendResult               | 이 클래스는 SMS 서비스의 결과를 포함합니다.                                          |

## <a name="authenticate-the-client"></a>클라이언트 인증

 텍스트 편집기에서 **Program.cs** 를 열고 `Main` 메서드의 본문을 코드로 바꿔 연결 문자열로 `SmsClient`를 초기화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>1:1 SMS 메시지 보내기

SMS 메시지를 단일 수신자에게 보내려면 SmsClient에서 `Send` 또는 `SendAsync` 함수를 호출합니다. 다음 코드를 **Program.cs** 에서 `Main` 메서드의 끝에 추가합니다.

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
`<from-phone-number>`를 Communication Services 리소스와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

## <a name="send-a-1n-sms-message-with-options"></a>옵션이 포함된 1:N SMS 메시지 보내기
SMS 메시지를 수신자 목록에 보내려면 수신자의 전화 번호 목록이 포함된 SmsClient에서 `Send` 또는 `SendAsync` 함수를 호출합니다. 선택적 매개 변수를 전달하여 전달 보고서를 사용하도록 설정할지 여부를 지정하고 사용자 지정 태그를 설정할 수도 있습니다.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

`<from-phone-number>`를 Communication Services 리소스와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number-1>` 및 `<to-phone-number-2>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식(예: +14255550123)으로 제공되어야 합니다. **발신** 전화번호도 단축 코드일 수 있습니다(예: 23456).

`enableDeliveryReport` 매개 변수는 전달 보고를 구성하는 데 사용할 수 있는 선택적 매개 변수입니다. 이 기능은 SMS 메시지가 전달될 때 이벤트를 내보내려는 시나리오에 유용합니다. SMS 메시지에 대한 전달 보고를 구성하려면 [SMS 이벤트 처리](../handle-sms-events.md)를 참조하세요.

`Tag`는 배달 보고서에 태그를 적용하는 데 사용됩니다.

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```

## <a name="sample-code"></a>샘플 코드

샘플 앱은 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)에서 다운로드할 수 있습니다.
