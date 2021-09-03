---
ms.openlocfilehash: f34320c0ba70da94315858ed09ffd1ca3e90d846
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656974"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/use-managed-Identity)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `ActiveDirectoryQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 `Program.cs`라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o ActiveDirectoryAuthenticationQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd ActiveDirectoryAuthenticationQuickstart
dotnet build
```

### <a name="install-the-sdk-packages"></a>SDK 패키지 설치

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>SDK 패키지 사용

다음 `using` 지시문을 `Program.cs`에 추가하여 Azure ID 및 Azure Storage SDK를 사용합니다.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
using Azure;
```

## <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential 만들기

이 빠른 시작에서는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)을 사용합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합합니다. 각 작업에 필요하므로 `Program.cs` 클래스 내에서 만들겠습니다. 파일의 맨 위에 다음을 추가합니다.

```csharp
private DefaultAzureCredential credential = new DefaultAzureCredential();
```

## <a name="issue-a-token-with-service-principals"></a>서비스 주체로 토큰 발행

이제 생성된 자격 증명을 사용하는 코드를 추가하여 VoIP 액세스 토큰을 발급합니다. 나중에 이 코드를 호출합니다.

```csharp
public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
{
    var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
    var identityResponse = client.CreateUser();
    var identity = identityResponse.Value;

    var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

    return tokenResponse;
}
```

## <a name="send-an-sms-with-service-principals"></a>서비스 주체와 SMS 보내기

서비스 주체를 사용하는 또 다른 예로, 동일한 자격 증명을 사용하여 SMS를 보내는 다음 코드를 추가합니다.

```csharp
public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
{
    SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
    SmsSendResult sendResult = smsClient.Send(
            from: from,
            to: to,
            message: message,
            new SmsSendOptions(enableDeliveryReport: true) // optional
        );

    return sendResult;
}
```

## <a name="write-the-main-method"></a>Main 메서드 쓰기

`Program.cs`에는 이미 Main 메서드가 있어야 합니다. 이전에 만든 코드를 호출하여 서비스 주체를 사용하는 방법을 보여주는 코드를 추가해 보겠습니다.

```csharp
static void Main(string[] args)
{
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    Uri endpoint = new("https://<RESOURCENAME>.communication.azure.com/");

    // We need an instance of the program class to use within this method.
    Program instance = new();

    Console.WriteLine("Retrieving new Access Token, using Service Principals");
    Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
    Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

    Console.WriteLine("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from using Service Principals");
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

최종 `Program.cs` 파일은 다음과 같이 표시됩니다.

```csharp
class Program
     {
          private DefaultAzureCredential credential = new DefaultAzureCredential();
          static void Main(string[] args)
          {
               // You can find your endpoint and access key from your resource in the Azure portal
               // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
               Uri endpoint = new("https://acstestingrifox.communication.azure.com/");

               // We need an instance of the program class to use within this method.
               Program instance = new();

               Console.WriteLine("Retrieving new Access Token, using Service Principals");
               Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
               Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

               Console.WriteLine("Sending SMS using Service Principals");

               // You will need a phone number from your resource to send an SMS.
               SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from Service Principals");
               Console.WriteLine($"Sms id: {result.MessageId}");
               Console.WriteLine($"Send Result Successful: {result.Successful}");
          }
          public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
          {
               var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
               var identityResponse = client.CreateUser();
               var identity = identityResponse.Value;

               var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

               return tokenResponse;
          }
          public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
          {
               SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
               SmsSendResult sendResult = smsClient.Send(
                    from: from,
                    to: to,
                    message: message,
                    new SmsSendOptions(enableDeliveryReport: true) // optional
               );

               return sendResult;
          }
    }
```

## <a name="run-the-program"></a>프로그램 실행

이제 애플리케이션 폴더에서 `dotnet run`을 사용하여 애플리케이션을 실행할 수 있습니다. 출력은 다음과 유사합니다.
```
Retrieving new Access Token, using Service Principals
Retrieved Access Token: ey....
Sending SMS using Service Principals
Sms id: Outgoing_..._noam
Send Result Successful: True
```
