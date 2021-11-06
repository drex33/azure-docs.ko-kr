---
title: 포함 파일
description: 포함 파일
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: d41f9c56cfa9b2a41cb77355af8a35a0499adb7a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893240"
---
### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `dotnet` 명령을 실행하여 .NET SDK가 설치되어 있는지 확인합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `RelayTokenQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o RelayTokenQuickstart
```

2. 디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd RelayTokenQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `dotnet add package` 명령을 사용하여 .NET 패키지용 Azure Communication Services ID 및 NetworkTraversal 라이브러리를 설치합니다.

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.NetworkTraversal
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 텍스트 편집기에서 **Program.cs** 파일 열기
2. `Azure.Communication.Identity`, `System.Threading.Tasks` 및 `System.Net.Http` 네임스페이스를 포함할 `using` 지시문 추가
3. 비동기 코드를 지원하도록 `Main` 메서드 선언 업데이트

코드는 다음과 같습니다.

```csharp
using System;
using Azure.Communication.Identity;
using Azure;
using System.Collections.Generic;
using Azure.Communication.NetworkTraversal;

namespace RelayTokenQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Relay Token Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `CommunicationIdentityClient`를 초기화합니다. 아래 코드는 환경 변수에서 리소스의 연결 문자열을 검색합니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

`Main` 메서드에 다음 코드를 추가합니다.

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>ID 만들기

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `createUser` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션 사용자에게 매핑하여 수신된 ID를 저장합니다. 예를 들어 애플리케이션 서버의 데이터베이스에 저장합니다. ID는 나중에 액세스 토큰을 발급하는 데 필요합니다.

```csharp
var identityResponse = await client.CreateUserAsync().Result;
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>릴레이 토큰에 대한 사용자 액세스 토큰 교환

Azure Communication 토큰 서비스를 호출하여 TURN 서비스 토큰의 사용자 액세스 토큰을 교환합니다.

```csharp
var relayClient = new CommunicationRelayClient("COMMUNICATION_SERVICES_CONNECTION_STRING");

Response<CommunicationRelayConfiguration> turnTokenResponse = await relayClient.GetRelayConfigurationAsync(identity).Result;
DateTimeOffset turnTokenExpiresOn = turnTokenResponse.Value.ExpiresOn;
IReadOnlyList<CommunicationIceServer> iceServers = turnTokenResponse.Value.IceServers;
Console.WriteLine($"Expires On: {turnTokenExpiresOn}");
foreach (CommunicationIceServer iceServer in iceServers)
{
    foreach (string url in iceServer.Urls)
    {
        Console.WriteLine($"TURN Url: {url}");
    }
    Console.WriteLine($"TURN Username: {iceServer.Username}");
    Console.WriteLine($"TURN Credential: {iceServer.Credential}");
}
```

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```
