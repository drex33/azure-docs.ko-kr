---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 717eefe0795809a9dd974ce6557d39486fbf9dd7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461194"
---
## <a name="set-up-prerequisites"></a>필수 조건 설정

- 운영 체제에 대한 최신 버전의 [.NET SDK](https://dotnet.microsoft.com/download/dotnet)

## <a name="set-up"></a>설정

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `CommunicationAccessTokensQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o CommunicationAccessTokensQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd CommunicationAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `dotnet add package` 명령을 사용하여 .NET 패키지용 Azure Communication Services ID 라이브러리를 설치합니다.

```console
dotnet add package Azure.Communication.Identity --prerelease
dotnet add package Microsoft.Identity.Client --version 4.36.2
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 텍스트 편집기에서 **Program.cs** 파일 열기
1. `Azure.Communication.Identity` 네임스페이스를 포함하는 `using` 지시문 추가
1. 비동기 코드를 지원하도록 `Main` 메서드 선언 업데이트

시작하려면 다음 코드를 사용합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace CommunicationAccessTokensQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Teams Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>1단계: MSAL 라이브러리를 통해 Azure AD 사용자 토큰 받기

토큰 교환 흐름의 첫 번째 단계는 [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md)를 사용하여 Teams 사용자에 대한 토큰을 가져오는 것입니다.

```csharp
string appId = "<contoso_application_id>";
string tenantId = "<contoso_tenant_id>";
string authority = $"https://login.microsoftonline.com/{tenantId}";
string redirectUri = "http://localhost";

var aadClient = PublicClientApplicationBuilder
                .Create(appId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

string scope = "https://auth.msft.communication.azure.com/VoIP";

var teamsUserAadToken = await aadClient
                        .AcquireTokenInteractive(new List<string> { scope })
                        .ExecuteAsync();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>2단계: CommunicationIdentityClient 초기화

연결 문자열로 `CommunicationIdentityClient`를 초기화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

`Main` 메서드에 다음 코드를 추가합니다.

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>3단계: Teams 액세스 토큰에 대한 Azure AD 사용자 토큰 교환

`GetTokenForTeamsUser` 메서드를 사용하여 Azure Communication Services SDK와 함께 사용할 수 있는 Teams 사용자에 대한 액세스 토큰을 발급합니다.

```csharp
var accessToken = await client.GetTokenForTeamsUser(teamsUserAadToken.AccessToken);
Console.WriteLine($"Token: {accessToken.Value.Token}");
```

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```
