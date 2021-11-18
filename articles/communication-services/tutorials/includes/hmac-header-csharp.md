---
title: C#에 HTTP 요청 서명
description: 이 자습서에서는 Azure Communication Services에 대한 HMAC 서명을 사용하여 HTTP 요청에 서명하는 C# 버전에 대해 설명합니다.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 06/30/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 1ed686906a8233d0300b43a03d03127f870e3ae5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132430007"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Visual Studio](https://visualstudio.microsoft.com/downloads/)를 설치합니다.
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../quickstarts/create-communication-resource.md)를 참조하세요. 이 자습서에서는 **resourceEndpoint** 및 **resourceAccessKey** 를 기록해야 합니다.

## <a name="sign-an-http-request-with-c"></a>C#에 HTTP 요청 서명

액세스 키 인증은 공유 비밀 키를 사용하여 각 HTTP 요청에 대해 HMAC 서명을 생성합니다. 이 서명은 SHA256 알고리즘을 사용하여 생성되며 `HMAC-SHA256` 체계를 사용하여 `Authorization` 헤더에 전송됩니다. 예를 들어 다음과 같습니다.

```
Authorization: "HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature`는 다음으로 구성됩니다.

- HTTP 동사(예: `GET` 또는 `PUT` )
- HTTP 요청 경로
- x-ms-date
- 호스트
- x-ms-content-sha256

## <a name="setup"></a>설정

다음 단계에서는 인증 헤더를 생성하는 방법을 설명합니다.

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `SignHmacTutorial`이라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o SignHmacTutorial
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>패키지 설치

본문 직렬화에 사용되는 패키지 `Newtonsoft.Json`을 설치합니다.

```console
dotnet add package Newtonsoft.Json
```

비동기 코드를 지원하도록 `Main` 메서드 선언을 업데이트합니다. 시작하려면 다음 코드를 사용합니다.

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>요청 메시지 만들기

이 예제에서는 Communication Services Authentication API(버전 `2021-03-07`)를 사용하여 새 ID를 만들도록 요청에 서명합니다.

`Main` 메서드에 다음 코드를 추가합니다.

```csharp
string resourceEndpoint = "resourceEndpoint";
// Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
// Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

`resourceEndpoint`를 실제 리소스 엔드포인트 값으로 바꿉니다.

## <a name="create-a-content-hash"></a>콘텐츠 해시 만들기

콘텐츠 해시는 HMAC 서명의 일부입니다. 다음 코드를 추가하여 콘텐츠 해시를 컴퓨팅합니다. 이 메서드를 `Main` 메서드 아래 `Progam.cs`에 추가할 수 있습니다.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>서명 컴퓨팅

다음 코드를 사용하여 HMAC 서명을 계산하는 메서드를 만듭니다.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

`resourceAccessKey`를 실제 Communication Services 리소스의 액세스 키로 바꿉니다.

## <a name="create-an-authorization-header-string"></a>권한 부여 헤더 문자열 만들기

이제 권한 부여 헤더에 추가할 문자열을 구성합니다.

1. 서명할 헤더의 값을 준비합니다.
   1. 협정 세계시(UTC) 시간대를 사용하여 현재 타임스탬프를 지정합니다.
   1. 요청 기관(DNS 호스트 이름 또는 IP 주소 및 포트 번호)을 가져옵니다.
   1. 콘텐츠 해시를 계산합니다.
1. 서명할 문자열을 준비합니다.
1. 서명을 계산합니다.
1. 권한 부여 헤더에 사용되는 문자열을 연결합니다.
 
`Main` 메서드에 다음 코드를 추가합니다.

```csharp
// Specify the 'x-ms-date' header as the current UTC timestamp according to the RFC1123 standard
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
// Get the host name corresponding with the 'host' header.
var host = requestUri.Authority;
// Compute a content hash for the 'x-ms-content-sha256' header.
var contentHash = ComputeContentHash(serializedBody);

// Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{host};{contentHash}";
// Compute the signature.
var signature = ComputeSignature(stringToSign);
// Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>requestMessage에 헤더 추가

다음 코드를 사용하여 `requestMessage`에 필요한 헤더를 추가합니다.

```csharp
// Add a date header.
requestMessage.Headers.Add("x-ms-date", date);

// Add a host header.
// In C#, the 'host' header is added automatically by the 'HttpClient'. However, this step may be required on other platforms such as Node.js.

// Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);

// Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>클라이언트의 테스트

`HttpClient`를 사용하여 엔드포인트를 호출하고 응답을 확인합니다.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
