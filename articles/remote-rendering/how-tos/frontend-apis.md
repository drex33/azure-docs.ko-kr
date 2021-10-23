---
title: 인증용 Azure Frontend API
description: 인증용 C# 프런트 엔드 API를 사용하는 방법을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f0519b60d3b02c8312e15861441060ca89ab002
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234135"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>인증을 위해 Azure Frontend API 사용

이 섹션에서는 인증 및 세션 관리에 API를 사용하는 방법을 설명합니다.

> [!CAUTION]
> 이 장에서 설명하는 함수는 내부적으로 서버에서 REST 호출을 실행합니다. 모든 REST 호출과 마찬가지로 이러한 명령을 너무 자주 보내면 서버가 제한을 초과하여 결국 오류가 반환됩니다. `SessionGeneralContext.HttpResponseCode`이 경우 멤버의 값은 429("요청이 너무 많음")입니다. 일반적으로 **후속 호출 간에 5~10초** 지연이 발생합니다.

또한 일부 함수는 다시 시도하기 위해 저장할 때 정보를 반환합니다. 예를 들어 `RenderingSessionPropertiesResult.MinimumRetryDelay` 다른 검사를 시도하기 전에 대기할 시간(초)을 지정합니다. 사용 가능한 경우 제한 없이 가능한 한 자주 검사를 수행할 수 있기 때문에 이러한 반환된 값을 사용하는 것이 가장 좋습니다.

## <a name="sessionconfiguration"></a>SessionConfiguration

SessionConfiguration은 SDK의 ```RemoteRenderingClient``` 인스턴스에 대한 인증 정보를 설정하는 데 사용됩니다.

중요한 필드는 다음과 같습니다.

```cs
public class SessionConfiguration
{
    // Domain that will be used for account authentication for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be set to the domain of the Azure Remote Rendering account.
    public string AccountDomain;
    // Domain that will be used to generate sessions for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be selected based on the region closest to the user. For example, westus2.mixedreality.azure.com or westeurope.mixedreality.azure.com.
    public string RemoteRenderingDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

해당하는 C++는 다음과 같습니다.

```cpp
struct SessionConfiguration
{
    std::string AccountDomain{};
    std::string RemoteRenderingDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};
```

도메인의 _지역_ 부분에 대해 [가까운 지역](../reference/regions.md)을 사용합니다.

계정 정보는 [계정 정보 검색](create-an-account.md#retrieve-the-account-information) 단락에 설명된 대로 포털에서 가져올 수 있습니다.

## <a name="azure-frontend"></a>Azure 프런트 엔드

관련 클래스는 ```RemoteRenderingClient``` 및 ```RenderingSession```입니다. ```RemoteRenderingClient```는 계정 관리 및 계정 수준 기능에 사용됩니다. 여기에는 자산 변환 및 렌더링 세션 생성이 포함됩니다. ```RenderingSession```은 세션 수준 기능에 사용되며 세션 업데이트, 쿼리, 갱신 및 서비스 해제가 포함됩니다.

열려 있거나 생성된 각 ```RenderingSession```은 생성된 프런트 엔드에 대한 참조를 유지합니다. 완전히 종료하려면 프런트 엔드를 할당 취소하기 전에 모든 세션을 할당 취소해야 합니다.

세션의 할당을 취소해도 Azure에서 서버가 중지되지 않으며 `RenderingSession.StopAsync`를 명시적으로 호출해야 합니다.

세션이 생성되고 해당 상태가 준비된 것으로 표시되면 `RenderingSession.ConnectAsync`을 사용하여 원격 렌더링 런타임에 연결할 수 있습니다.

### <a name="threading"></a>스레딩

모든 RenderingSession 및 RemoteRenderingClient 비동기 호출은 주 애플리케이션 스레드가 아닌 백그라운드 스레드에서 완료됩니다.

### <a name="conversion-apis"></a>변환 API

변환 서비스에 대한 자세한 내용은 [모델 변환 REST API](conversion/conversion-rest-api.md)를 참조하세요.

#### <a name="start-asset-conversion"></a>자산 변환 시작

```cs
async void StartAssetConversion(RemoteRenderingClient client, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    var result = await client.StartAssetConversionAsync(
        new AssetConversionInputOptions(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputOptions(storageContainer, bloboutpath, "", outputName)
        );
}
```

```cpp
void StartAssetConversion(ApiHandle<RemoteRenderingClient> client, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputOptions input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputOptions output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    client->StartAssetConversionAsync(input, output, [](Status status, ApiHandle<AssetConversionResult> result) {
        if (status == Status::OK)
        {
            //use result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    });
}
```

#### <a name="get-conversion-status"></a>변환 상태 가져오기

```cs
async void GetConversionStatus(RemoteRenderingClient client, string assetId)
{
    AssetConversionStatusResult status = await client.GetAssetConversionStatusAsync(assetId);
    // do something with status (e.g. check current status etc.)
}
```

```cpp
void GetConversionStatus(ApiHandle<RemoteRenderingClient> client, std::string assetId)
{
    client->GetAssetConversionStatusAsync(assetId, [](Status status, ApiHandle<AssetConversionStatusResult> result) {
        if (status == Status::OK)
        {
            // do something with result (e.g. check current status etc.)
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }
    });
}

```


### <a name="rendering-apis"></a>렌더링 API

세션 관리에 대한 자세한 내용은 [세션 관리 REST API](session-rest-api.md)를 참조하세요.

서비스에서 렌더링 세션을 동적으로 만들거나 기존 세션 ID를 RenderingSession 개체로 '열 수' 있습니다.

#### <a name="create-rendering-session"></a>렌더링 세션 만들기

```cs
async void CreateRenderingSession(RemoteRenderingClient client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationOptions(vmSize, maxLeaseInMinutes / 60, maxLeaseInMinutes % 60));

    // if the call was successful, result.Session holds a valid session reference, otherwise check result.Context for error information
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    RenderingSessionCreationOptions params;
    params.MaxLeaseInMinutes = maxLeaseInMinutes;
    params.Size = vmSize;
    client->CreateNewRenderingSessionAsync(params, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            result->GetSession();
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>기존 렌더링 세션 열기

기존 세션을 여는 것은 동기식 호출입니다.

```cs
async void CreateRenderingSession(RemoteRenderingClient client, string sessionId)
{
    CreateRenderingSessionResult result = await client.OpenRenderingSessionAsync(sessionId);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // Query session status, etc.
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, std::string sessionId)
{
    client->OpenRenderingSessionAsync(sessionId, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode()==Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // Query session status, etc.
        }
    });
}
```


#### <a name="get-current-rendering-sessions"></a>현재 렌더링 세션 가져오기

```cs
async void GetCurrentRenderingSessions(RemoteRenderingClient client)
{
    RenderingSessionPropertiesArrayResult result = await client.GetCurrentRenderingSessionsAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties[] properties = result.SessionProperties;
        // Query session status, etc.
    }
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<RemoteRenderingClient> client)
{
    client->GetCurrentRenderingSessionsAsync([](Status status, ApiHandle<RenderingSessionPropertiesArrayResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            std::vector<RenderingSessionProperties> properties;
            result->GetSessionProperties(properties);
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>세션 API

#### <a name="get-rendering-session-properties"></a>렌더링 세션 속성 가져오기

```cs
async void GetRenderingSessionProperties(RenderingSession session)
{
    RenderingSessionPropertiesResult result = await session.GetPropertiesAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties properties = result.SessionProperties;
    }
    else
    {
        Console.WriteLine("Failed to get properties of session!");
    }
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<RenderingSession> session)
{
    session->GetPropertiesAsync([](Status status, ApiHandle<RenderingSessionPropertiesResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            RenderingSessionProperties properties = result->GetSessionProperties();
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>렌더링 세션 업데이트

```cs
async void UpdateRenderingSession(RenderingSession session, int updatedLeaseInMinutes)
{
    SessionContextResult result = await session.RenewAsync(
        new RenderingSessionUpdateOptions(updatedLeaseInMinutes / 60, updatedLeaseInMinutes % 60));
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session renewed succeeded!");
    }
    else
    {
        Console.WriteLine("Failed to renew rendering session!");
    }
}
```

```cpp
void UpdateRenderingSession(ApiHandle<RenderingSession> session, int updatedLeaseInMinutes)
{
    RenderingSessionUpdateOptions params;
    params.MaxLeaseInMinutes = updatedLeaseInMinutes;
    session->RenewAsync(params, [](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>렌더링 세션 중지

```cs
async void StopRenderingSession(RenderingSession session)
{
    SessionContextResult result = await session.StopAsync();
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session stopped successfully!");
    }
    else
    {
        Console.WriteLine("Failed to stop rendering session!");
    }
}
```

```cpp
void StopRenderingSession(ApiHandle<RenderingSession> session)
{
    session->StopAsync([](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>ARR 검사자에 연결

```cs
async void ConnectToArrInspector(RenderingSession session)
{
    string htmlPath = await session.ConnectToArrInspectorAsync();
#if WINDOWS_UWP
    UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
    {
        var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
        await Windows.System.Launcher.LaunchFileAsync(file);
    }, true);
#else
    InvokeOnAppThreadAsync(() =>
        {
            System.Diagnostics.Process.Start("file:///" + htmlPath);
        });
#endif
}
```

```cpp
void ConnectToArrInspector(ApiHandle<RenderingSession> session)
{
    session->ConnectToArrInspectorAsync([](Status status, std::string result) {
        if (status == Status::OK)
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + result;
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>다음 단계

* [계정 만들기](create-an-account.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
