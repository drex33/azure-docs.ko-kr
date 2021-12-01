---
ms.openlocfilehash: 692bbe5a713128203c612bc65b1b308da2cb528b
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385395"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 운영 체제에 대한 최신 버전의 [.NET Core 클라이언트 라이브러리](https://dotnet.microsoft.com/download/dotnet-core)
- 활성 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `dotnet` 명령을 실행하여 .NET 클라이언트 라이브러리가 설치되어 있는지 확인합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `PhoneNumbersQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o PhoneNumbersQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `dotnet add package` 명령을 사용하여 .NET 패키지용 Azure Communication PhoneNumbers 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0
```

**Program.cs** 의 맨 위에 `using` 지시어를 추가하여 네임스페이스를 포함합니다.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

`Main` 함수 시그니처를 비동기화하도록 업데이트합니다.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>클라이언트 인증

전화 번호 클라이언트는 [Azure Portal][azure_portal]의 Azure Communication Services 리소스에서 가져온 연결 문자열을 사용하여 인증할 수 있습니다.

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

전화 번호 클라이언트는 Azure Active Directory 인증을 사용하여 인증하는 옵션도 있습니다. 이 옵션을 사용하는 경우 인증을 위해 `AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` 및 `AZURE_TENANT_ID` 환경 변수를 설정해야 합니다.

```csharp
// Get an endpoint to our Azure Communication Services resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>전화 번호 관리

### <a name="search-for-available-phone-numbers"></a>사용 가능한 전화 번호 검색

전화 번호를 구매하려면 먼저 사용 가능한 전화 번호를 검색해야 합니다. 전화 번호를 검색하려면 지역 번호, 할당 유형, [전화 번호 기능](../../../concepts/telephony/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [전화 번호 유형](../../../concepts/telephony/plan-solution.md#phone-number-types-in-azure-communication-services) 및 수량을 제공합니다. 수신자 부담 전화 번호 형식의 경우 지역 코드 제공은 선택 사항입니다.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>전화 번호 구매

전화 번호 검색의 결과는 `PhoneNumberSearchResult`입니다. 여기에는 검색 시 숫자를 얻기 위해 번호 구매 API에 전달할 수 있는 `SearchId`가 포함됩니다. 전화 번호 구매 API를 호출하면 Azure 계정에 요금이 부과됩니다.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>전화 번호 가져오기

번호 구매 후에 클라이언트에서 검색할 수 있습니다.
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

구매한 전화 번호를 모두 검색할 수도 있습니다.
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>전화 번호 기능 업데이트

구매한 번호를 사용하면 기능을 업데이트할 수 있습니다.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>전화 번호 해제

구매한 전화 번호를 해제할 수 있습니다.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```

## <a name="sample-code"></a>샘플 코드

샘플 앱은 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)에서 다운로드할 수 있습니다.
