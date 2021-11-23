---
title: Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하여 비디오 업로드 및 인덱싱
description: 미디어에 대 한 Azure Video Analyzer (이전의 Video Indexer)를 사용 하 여 비디오를 업로드 하 고 인덱싱하는 두 가지 방법을 알아봅니다.
ms.service: azure-video-analyzer
ms.topic: article
ms.date: 11/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ae2b2ed2e3e363eef29ce6e7c61346b46665875
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935570"
---
# <a name="upload-and-index-your-videos"></a>비디오 업로드 및 인덱싱  

이 문서에서는 미디어 (이전의 Video Indexer) 웹 사이트 및 업로드 video API 용 Azure Video Analyzer를 사용 하 여 비디오를 업로드 하 고 인덱싱하는 방법을 보여 줍니다. 

미디어 계정에 대 한 비디오 분석기를 만들 때 다음 중에서 선택 합니다.

- 무료 평가판 계정. 미디어 용 비디오 분석기는 웹 사이트 사용자에 게 최대 600 분의 무료 인덱싱을 제공 하 고 API 사용자에 대 한 무료 인덱싱을 최대 2400 분까지 제공 합니다. 
- 할당량으로 제한 되지 않는 유료 옵션입니다. [Azure 구독 및 Azure Media Services 계정에 연결](connect-to-azure.md)된 미디어 계정에 대 한 비디오 분석기를 만듭니다. 인덱싱된 시간 (분)에 대 한 비용을 지불 합니다. 

계정 유형에 대 한 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조 하세요.

API를 사용 하 여 비디오를 업로드 하는 경우 다음과 같은 옵션을 사용할 수 있습니다. 

* URL에서 비디오를 업로드 합니다 (기본 설정).
* 비디오 파일을 요청 본문에서 바이트 배열로 보냅니다.
* [자산 ID](../../media-services/latest/assets-concept.md)를 제공 하 여 기존 Azure Media Services 자산을 사용 합니다. 이 옵션은 유료 계정 에서만 지원 됩니다.

## <a name="supported-file-formats"></a>지원되는 파일 형식

미디어 용 비디오 분석기에서 사용할 수 있는 파일 형식 목록은 [표준 인코더 형식 및 코덱](../../media-services/latest/encode-media-encoder-standard-formats-reference.md)을 참조 하세요.

## <a name="storage-of-video-files"></a>비디오 파일 Storage

미디어 용 비디오 분석기를 사용 하는 경우 비디오 파일이 Media Services를 통해 Azure Storage에 저장 됩니다. 제한 크기는 30GB이 고 길이는 4 시간입니다.

미디어에 대 한 비디오 분석기에서 추출한 메타 데이터 및 정보를 비롯 하 여 비디오 및 오디오 파일을 언제 든 지 삭제할 수 있습니다. 미디어에 대 한 비디오 분석기에서 파일을 삭제 하면 파일 및 해당 메타 데이터와 정보가 미디어 용 비디오 분석기에서 영구적으로 제거 됩니다. 그러나 Azure Storage에서 자체 백업 솔루션을 구현한 경우 파일은 Azure Storage에 그대로 남아 있습니다.

비디오의 지 속성은 미디어 웹 사이트 용 비디오 분석기를 사용 하 여 업로드 하거나 업로드 video API를 사용 하 여 업로드 하는 경우와 동일 합니다.
   
## <a name="upload-and-index-a-video-by-using-the-website"></a>웹 사이트를 사용 하 여 비디오 업로드 및 인덱싱

[미디어 용 비디오 분석기](https://www.videoindexer.ai/) 웹 사이트에서 로그인 한 다음 **업로드** 를 선택 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="업로드 단추를 표시 하는 스크린샷":::

비디오가 업로드 되 면 미디어 용 비디오 분석기는 비디오 인덱싱 및 분석을 시작 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="업로드 진행률을 보여 주는 스크린샷":::

미디어에 대 한 비디오 분석기 분석이 완료 되 면 비디오에 대 한 링크가 포함 된 전자 메일을 받게 됩니다. 또한 전자 메일에는 비디오에 있는 항목에 대 한 간략 한 설명이 포함 됩니다 (예: 사람, 토픽, 광학 문자 인식).

## <a name="upload-and-index-a-video-by-using-the-api"></a>API를 사용 하 여 비디오 업로드 및 인덱싱

[업로드 Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API를 사용 하 여 URL을 기준으로 비디오를 업로드 하 고 인덱싱할 수 있습니다. 다음 코드 샘플에는 바이트 배열을 업로드 하는 방법을 보여 주는 주석 처리 된 코드가 포함 되어 있습니다. 

### <a name="configurations-and-parameters"></a>구성 및 매개 변수

이 섹션에서는 선택적 매개 변수 중 일부와 이러한 매개 변수를 설정 하는 시기에 대해 설명 합니다. 매개 변수에 대 한 최신 정보는 [미디어 포털에 대 한 비디오 분석기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)를 참조 하세요.

#### <a name="externalid"></a>externalID 

이 매개 변수를 사용 하 여 비디오와 연결 되는 ID를 지정 합니다. ID는 외부 VCM (비디오 콘텐츠 관리) 시스템에 통합 하는 데 적용할 수 있습니다. 미디어 포털에 대 한 비디오 분석기의 비디오는 지정 된 외부 ID를 통해 검색할 수 있습니다.

#### <a name="callbackurl"></a>callbackUrl

콜백 URL을 지정 하려면이 매개 변수를 사용 합니다. 

[!INCLUDE [callback url](./includes/callback-url.md)]

Video Analyzer for Media는 원래 URL에 제공된 기존 매개 변수를 반환합니다. URL을 인코딩해야 합니다.

#### <a name="indexingpreset"></a>indexingPreset

이 매개 변수를 사용 하 여 오디오 또는 비디오 파일에 적용 하려는 AI 번들을 정의 합니다. 이 매개 변수는 인덱싱 프로세스를 구성하는 데 사용됩니다. 다음 값을 지정할 수 있습니다.

- `AudioOnly`: 오디오만 사용 하 여 정보를 인덱싱하고 추출 합니다 (비디오 무시).
- `VideoOnly`: 비디오 전용 (오디오 무시)을 사용 하 여 정보를 인덱싱하고 추출 합니다.
- `Default`: 오디오와 비디오를 모두 사용 하 여 정보를 인덱싱하고 추출 합니다.
- `DefaultWithNoiseReduction`: 오디오 및 비디오에서 정보를 인덱싱하고 추출할 때 오디오 스트림에 노이즈 감소 알고리즘을 적용 합니다.

    `DefaultWithNoiseReduction`이제 값이 기본 사전 설정 (사용 되지 않음)에 매핑됩니다.
- `BasicAudio`: 오디오만 사용 하 여 정보를 인덱싱하고 추출 합니다 (비디오 무시). 기본 오디오 기능 (녹음, 번역, 출력 캡션 및 자막 서식 지정)만 포함 합니다.
- `AdvancedAudio`: 오디오만 사용 하 여 정보를 인덱싱하고 추출 합니다 (비디오 무시). 표준 오디오 분석 외에도 고급 오디오 기능 (예: 오디오 이벤트 검색)을 포함 합니다.
- `AdvancedVideo`: 비디오 전용 (오디오 무시)을 사용 하 여 정보를 인덱싱하고 추출 합니다. 표준 비디오 분석 외에도 고급 비디오 기능 (예: 관찰 된 사람 추적)을 포함 합니다.
- `AdvancedVideoAndAudio`: 고급 오디오 및 고급 비디오 분석을 모두 사용 하 여 정보를 인덱싱하고 추출 합니다. 

> [!NOTE]
> 위의 고급 사전 설정에는 공개 미리 보기로 제공 되는 모델이 포함 되어 있습니다. 이러한 모델이 일반 공급에 도달 하면 가격에 영향을 미칠 수 있습니다. 

미디어에 대 한 비디오 분석기는 최대 두 개의 오디오 트랙을 포함 합니다. 파일에 더 많은 오디오 트랙이 있으면 하나의 트랙으로 처리 됩니다. 트랙을 별도로 인덱싱 하려면 관련 오디오 파일을 추출 하 고로 인덱스를 만들어야 `AudioOnly` 합니다.

가격은 선택한 인덱싱 옵션에 따라 달라집니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조 하세요.

#### <a name="priority"></a>priority

미디어에 대 한 비디오 분석기는 우선 순위에 따라 비디오를 인덱싱합니다. `priority`매개 변수를 사용 하 여 인덱스 우선 순위를 지정 합니다. 유효한 값은 `Low` , `Normal` (기본값) 및 `High` 입니다.

이 매개 변수는 유료 계정에 대해서만 지원 됩니다.

#### <a name="streamingpreset"></a>streamingPreset

비디오를 업로드 한 후에는 미디어 용 비디오 분석기에서 필요에 따라 비디오를 인코딩합니다. 그런 다음 비디오 인덱싱 및 분석을 진행 합니다. 미디어에 대 한 비디오 분석기 분석이 완료 되 면 비디오 ID로 알림을 받게 됩니다.  

비디오 API [업로드 비디오](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) 또는 [인덱스 다시](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) 사용 하는 경우 선택적 매개 변수 중 하나는 `streamingPreset` 입니다. `streamingPreset`을 `Default`, `SingleBitrate` 또는 `AdaptiveBitrate`로 설정하면 인코딩 프로세스가 트리거됩니다. 

인덱싱 및 인코딩 작업이 완료 되 면 비디오를 스트리밍할 수 있도록 비디오가 게시 됩니다. 비디오를 스트리밍 하려는 스트리밍 끝점은 **실행** 상태에 있어야 합니다.

의 경우 `SingleBitrate` 표준 인코더 비용이 출력에 적용 됩니다. 비디오 높이가 720 보다 크거나 같은 경우 Media Video Analyzer는이를 1280 x 720로 인코딩합니다. 그렇지 않으면 640 x 468로 인코딩됩니다.
기본 설정은 [내용 인식 인코딩입니다](../../media-services/latest/encode-content-aware-concept.md).

비디오를 인덱싱하고 인코딩하지 않으려는 경우 `streamingPreset` 로 설정 `NoStreaming` 합니다.

#### <a name="videourl"></a>videoUrl

이 매개 변수는 인덱싱할 비디오 또는 오디오 파일의 URL을 지정 합니다. `videoUrl`매개 변수를 지정 하지 않으면 미디어에 대 한 비디오 분석기에서 파일을 다중 파트/양식 본문 콘텐츠로 전달 해야 합니다.

### <a name="code-sample"></a>코드 샘플

다음 c # 코드 조각에서는 미디어 Api에 대 한 모든 비디오 분석기를 함께 사용 하는 방법을 보여 줍니다.

### <a name="classic-account"></a>[클래식 계정](#tab/With-classic-account/)

다음 코드를 개발 플랫폼에 복사한 후에는 두 개의 매개 변수를 제공 해야 합니다.

* API 키 ( `apiKey` ): 개인 api 관리 구독 키입니다. 미디어 계정에 대 한 비디오 분석기에서 작업을 수행 하기 위해 액세스 토큰을 가져올 수 있습니다. 

  API 키를 가져오려면:

  1. [미디어 포털로 비디오 분석기](https://api-portal.videoindexer.ai/)로 이동 합니다.
  1. 로그인합니다.
  1. **제품**  >  **권한**  >  **부여 권한 부여 구독** 으로 이동 합니다.
  1. **기본 키** 값을 복사합니다.

* 비디오 URL ( `videoUrl` ): 인덱싱할 비디오 또는 오디오 파일의 url입니다. 요구 사항은 다음과 같습니다.

  - URL은 미디어 파일을 가리켜야 합니다. HTML 페이지는 지원 되지 않습니다. 
  - 이 파일은 URI의 일부로 제공 되는 액세스 토큰으로 보호할 수 있습니다. 이 파일을 사용 하는 끝점은 TLS 1.2 이상으로 보호 되어야 합니다.
  - URL을 인코딩해야 합니다.  

코드 샘플을 성공적으로 실행 한 결과에는 정보 위젯 URL 및 플레이어 위젯 URL이 포함 됩니다. 이를 통해 정보 및 업로드 된 비디오를 각각 검사할 수 있습니다. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // Replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // Create the HTTP client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // Obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // Take the relevant account. Here we simply take the first. 
    // You can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // We'll use the access token from here on, so there's no need for the APIM key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // Upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // Get the video from URL
    var videoUrl = "VIDEO_URL"; // Replace with the video URL

    // As an alternative to specifying video URL, you can upload a file.
    // Remove the videoUrl parameter from the query parameters below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // Get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // Wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // Job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // Search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // Get insights widget URL
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // Get player widget URL
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="azure-resource-manager-account"></a>[Azure Resource Manager 계정](#tab/with-arm-account-account/)

이 c # 프로젝트를 개발 플랫폼에 복사한 후에는 다음 단계를 수행 해야 합니다. 

1. 프로그램 .cs로 이동 하 여 다음을 채웁니다.

   - ```SubscriptionId``` 구독 ID를 사용 합니다.
   - ```ResourceGroup``` 리소스 그룹을 사용 합니다.
   - ```AccountName``` 계정 이름으로 바꿉니다.
   - ```VideoUrl``` 비디오 URL을 사용 합니다.
1. .NET 6.0이 설치 되어 있는지 확인 합니다. 그렇지 않은 경우 [설치](https://dotnet.microsoft.com/download/dotnet/6.0)합니다.
1. Azure CLI 설치 되어 있는지 확인 합니다. 그렇지 않은 경우 [설치](/cli/azure/install-azure-cli)합니다.
1. 터미널을 열고 *Videoindexerarm* 폴더로 이동 합니다.
1. Azure에 로그인 ```az login --use-device``` 합니다.
1. 프로젝트를 빌드합니다 ```dotnet build``` .
1. 프로젝트를 실행 ```dotnet run``` 합니다.

```csharp
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Azure.Identity" Version="1.4.1" />
    <PackageReference Include="Microsoft.Identity.Client" Version="4.36.2" />
  </ItemGroup>

</Project>
```

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using System.Web;
using Azure.Core;
using Azure.Identity;


namespace VideoIndexerArm
{
    public class Program
    {
        private const string AzureResourceManager = "https://management.azure.com";
        private const string SubscriptionId = ""; // Your Azure subscription
        private const string ResourceGroup = ""; // Your resource group
        private const string AccountName = ""; // Your account name
        private const string VideoUrl = ""; // The video URL you want to index

        public static async Task Main(string[] args)
        {
            // Build Azure Video Analyzer for Media resource provider client that has access token through Azure Resource Manager
            var videoIndexerResourceProviderClient = await VideoIndexerResourceProviderClient.BuildVideoIndexerResourceProviderClient();

            // Get account details
            var account = await videoIndexerResourceProviderClient.GetAccount();
            var accountId = account.Properties.Id;
            var accountLocation = account.Location;
            Console.WriteLine($"account id: {accountId}");
            Console.WriteLine($"account location: {accountLocation}");

            // Get account-level access token for Azure Video Analyzer for Media 
            var accessTokenRequest = new AccessTokenRequest
            {
                PermissionType = AccessTokenPermission.Contributor,
                Scope = ArmAccessTokenScope.Account
            };

            var accessToken = await videoIndexerResourceProviderClient.GetAccessToken(accessTokenRequest);
            var apiUrl = "https://api.videoindexer.ai";
            System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;


            // Create the HTTP client
            var handler = new HttpClientHandler();
            handler.AllowAutoRedirect = false;
            var client = new HttpClient(handler);

            // Upload a video
            var content = new MultipartFormDataContent();
            Console.WriteLine("Uploading...");
            // Get the video from URL

            // As an alternative to specifying video URL, you can upload a file.
            // Remove the videoUrl parameter from the query parameters below and add the following lines:
            // FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
            // byte[] buffer =new byte[video.Length];
            // video.Read(buffer, 0, buffer.Length);
            // content.Add(new ByteArrayContent(buffer));

            var queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"name", "video sample"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", VideoUrl},
                });
            var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos?{queryParams}", content);
            var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

            // Get the video ID from the upload result
            string videoId = JsonSerializer.Deserialize<Video>(uploadResult).Id;
            Console.WriteLine("Uploaded");
            Console.WriteLine("Video ID:");
            Console.WriteLine(videoId);

            // Wait for the video index to finish
            while (true)
            {
                await Task.Delay(10000);

                queryParams = CreateQueryString(
                    new Dictionary<string, string>()
                    {
                {"accessToken", accessToken},
                {"language", "English"},
                    });

                var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/Index?{queryParams}");
                var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

                string processingState = JsonSerializer.Deserialize<Video>(videoGetIndexResult).State;

                Console.WriteLine("");
                Console.WriteLine("State:");
                Console.WriteLine(processingState);

                // Job is finished
                if (processingState != "Uploaded" && processingState != "Processing")
                {
                    Console.WriteLine("");
                    Console.WriteLine("Full JSON:");
                    Console.WriteLine(videoGetIndexResult);
                    break;
                }
            }

            // Search for the video
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"id", videoId},
                });

            var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/Search?{queryParams}");
            var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
            Console.WriteLine("");
            Console.WriteLine("Search:");
            Console.WriteLine(searchResult);

            // Get insights widget URL
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
                });
            var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?{queryParams}");
            var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
            Console.WriteLine("Insights Widget url:");
            Console.WriteLine(insightsWidgetLink);

            // Get player widget URL
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
                });
            var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?{queryParams}");
            var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
            Console.WriteLine("");
            Console.WriteLine("Player Widget url:");
            Console.WriteLine(playerWidgetLink);
            Console.WriteLine("\nPress Enter to exit...");
            String line = Console.ReadLine();
            if (line == "enter")
            {
                System.Environment.Exit(0);
            }

        }

        static string CreateQueryString(IDictionary<string, string> parameters)
        {
            var queryParameters = HttpUtility.ParseQueryString(string.Empty);
            foreach (var parameter in parameters)
            {
                queryParameters[parameter.Key] = parameter.Value;
            }

            return queryParameters.ToString();
        }

        public class VideoIndexerResourceProviderClient
        {
            private readonly string armAaccessToken;

            async public static Task<VideoIndexerResourceProviderClient> BuildVideoIndexerResourceProviderClient()
            {
                var tokenRequestContext = new TokenRequestContext(new[] { $"{AzureResourceManager}/.default" });
                var tokenRequestResult = await new DefaultAzureCredential().GetTokenAsync(tokenRequestContext);
                return new VideoIndexerResourceProviderClient(tokenRequestResult.Token);
            }
            public VideoIndexerResourceProviderClient(string armAaccessToken)
            {
                this.armAaccessToken = armAaccessToken;
            }

            public async Task<string> GetAccessToken(AccessTokenRequest accessTokenRequest)
            {
                Console.WriteLine($"Getting access token. {JsonSerializer.Serialize(accessTokenRequest)}");
                // Set the generateAccessToken (from video indexer) HTTP request content
                var jsonRequestBody = JsonSerializer.Serialize(accessTokenRequest);
                var httpContent = new StringContent(jsonRequestBody, System.Text.Encoding.UTF8, "application/json");

                // Set request URI
                var requestUri = $"{AzureResourceManager}/subscriptions/{SubscriptionId}/resourcegroups/{ResourceGroup}/providers/Microsoft.VideoIndexer/accounts/{AccountName}/generateAccessToken?api-version=2021-08-16-preview";

                // Generate access token from video indexer
                var client = new HttpClient(new HttpClientHandler());
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", armAaccessToken);
                var result = await client.PostAsync(requestUri, httpContent);
                var jsonResponseBody = await result.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<GenerateAccessTokenResponse>(jsonResponseBody).AccessToken;
            }

            public async Task<Account> GetAccount()
            {

                Console.WriteLine($"Getting account.");
                // Set request URI
                var requestUri = $"{AzureResourceManager}/subscriptions/{SubscriptionId}/resourcegroups/{ResourceGroup}/providers/Microsoft.VideoIndexer/accounts/{AccountName}/?api-version=2021-08-16-preview";

                // Get account
                var client = new HttpClient(new HttpClientHandler());
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", armAaccessToken);
                var result = await client.GetAsync(requestUri);
                var jsonResponseBody = await result.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<Account>(jsonResponseBody);
            }
        }

        public class AccessTokenRequest
        {
            [JsonPropertyName("permissionType")]
            public AccessTokenPermission PermissionType { get; set; }

            [JsonPropertyName("scope")]
            public ArmAccessTokenScope Scope { get; set; }

            [JsonPropertyName("projectId")]
            public string ProjectId { get; set; }
            
            [JsonPropertyName("videoId")]
            public string VideoId { get; set; }
        }

        [JsonConverter(typeof(JsonStringEnumConverter))]
        public enum AccessTokenPermission
        {
            Reader,
            Contributor,
            MyAccessAdministrator,
            Owner,
        }

        [JsonConverter(typeof(JsonStringEnumConverter))]
        public enum ArmAccessTokenScope
        {
            Account,
            Project,
            Video
        }

        public class GenerateAccessTokenResponse
        {
            [JsonPropertyName("accessToken")]
            public string AccessToken { get; set; }

        }
        public class AccountProperties
        {
            [JsonPropertyName("accountId")]
            public string Id { get; set; }
        }

        public class Account
        {
            [JsonPropertyName("properties")]
            public AccountProperties Properties { get; set; }

            [JsonPropertyName("location")]
            public string Location { get; set; }

        }

        public class Video
        {
            [JsonPropertyName("id")]
            public string Id { get; set; }

            [JsonPropertyName("state")]
            public string State { get; set; }
        }
    }
}

```

### <a name="common-errors"></a>일반 오류

업로드 작업은 다음 상태 코드를 반환할 수 있습니다.

|상태 코드|ErrorType(응답 본문 내)|설명|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|동일한 비디오가이 계정에서 이미 처리 되 고 있습니다.|
|400|VIDEO_ALREADY_FAILED|이 계정에서 2 시간 전에 동일한 비디오를 처리 하지 못했습니다. API 클라이언트는 비디오를 다시 업로드 하기 전에 2 시간 이상 기다려야 합니다.|
|429||평가판 계정에는 분당 5개의 업로드가 허용됩니다. 유료 계정에는 분당 50개의 업로드가 허용됩니다.|

## <a name="uploading-considerations-and-limitations"></a>고려 사항 및 제한 사항 업로드
 
- 비디오 이름은 80 자이 하 여야 합니다.
- URL (기본 설정)에 따라 비디오를 업로드 하는 경우에는 TLS 1.2 이상으로 끝점을 보호 해야 합니다.
- URL 옵션을 사용 하는 업로드 크기는 30GB로 제한 됩니다.
- 요청 URL의 길이는 6144 자로 제한 됩니다. 쿼리 문자열 URL의 길이는 4096 자로 제한 됩니다.
- 바이트 배열이 포함 된 업로드 크기 옵션은 2gb로 제한 됩니다.
- 바이트 배열 옵션은 30 분 후에 시간 초과 됩니다.
- 매개 변수에 제공 된 URL은 `videoURL` 인코딩해야 합니다.
- 인덱싱 Media Services 자산에는 URL의 인덱싱과 동일한 제한이 적용 됩니다.
- 미디어에 대 한 비디오 분석기는 단일 파일에 대해 기간이 4 시간으로 제한 됩니다.
- URL에 액세스할 수 있어야 합니다 (예: 공용 URL). 

  개인 URL 인 경우 요청에서 액세스 토큰을 제공 해야 합니다.
- URL은 페이지에 대 한 링크와 같은 웹 페이지가 아닌 유효한 미디어 파일을 가리켜야 합니다 `www.youtube.com` .
- 유료 계정에서는 분당 최대 50 영화를 업로드할 수 있습니다. 평가판 계정에서는 분당 최대 5 개의 영화를 업로드할 수 있습니다.

> [!Tip]
> .NET Framework 버전 4.6.2을 사용 하는 것이 좋습니다. 이전 .NET Framework 버전은 기본적으로 TLS 1.2이 아닙니다.
>
> 이전 .NET Framework 버전을 사용 해야 하는 경우 REST API를 호출 하기 전에 코드에 한 줄을 추가 합니다.  
>
> `System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;`

## <a name="firewall"></a>방화벽

방화벽 뒤에 있는 저장소 계정에 대 한 자세한 내용은 [FAQ](faq.yml#can-a-storage-account-connected-to-the-media-services-account-be-behind-a-firewall)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[API에 의해 생성 된 미디어 출력에 대 한 Azure Video Analyzer를 검사 합니다.](video-indexer-output-json-v2.md)
