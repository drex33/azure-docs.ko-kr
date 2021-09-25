---
title: Azure Video Analyzer for Media(이전의 Video Indexer) API 사용
description: 이 문서에서는 Azure Video Analyzer for Media(이전의 Video Indexer) API를 시작하는 방법을 설명합니다.
ms.date: 01/07/2021
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 83aa673625ad2119adc35571a8aaa98d3a3736a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658643"
---
# <a name="tutorial-use-the-video-analyzer-for-media-api"></a>자습서: Video Analyzer for Media API 사용

Azure Video Analyzer for Media(이전의 Video Indexer)는 Microsoft에서 제공하는 다양한 오디오 및 비디오 AI(인공 지능) 기술을 하나의 통합 서비스로 통합하여 더 쉽게 개발할 수 있도록 합니다. 이 API는 개발자가 클라우드 플랫폼의 규모, 글로벌 범위, 가용성 및 안정성에 대해 걱정하지 않고 미디어 AI 기술 사용에 집중할 수 있도록 설계되었습니다. 이 API를 사용하여 파일을 업로드하고, 자세한 비디오 인사이트를 얻고, 포함 가능한 인사이트 및 플레이어 위젯의 URL을 가져오는 등의 작업을 수행할 수 있습니다.

Video Analyzer for Media 계정을 만들 때 평가판 계정(특정의 체험 인덱싱 시간(분)을 가져오는 경우) 또는 유료 옵션(할당량으로 제한되지 않은 경우)을 선택할 수 있습니다. 평가판을 사용하면 Video Analyzer for Media에서 웹 사이트 사용자에게 최대 600분의 체험 인덱싱을 제공하고, API 사용자에게는 최대 2,400분의 체험 인덱싱을 제공합니다. 유료 옵션을 사용하면 [Azure 구독 및 Azure Media Services 계정에 연결되는](connect-to-azure.md) Video Analyzer for Media 계정을 만듭니다. 인덱싱된 시간(분)에 대한 비용을 지불하는 경우 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하세요.

이 문서에서는 개발자가 [Video Analyzer for Media API](https://api-portal.videoindexer.ai/)를 활용하는 방법을 보여 줍니다.

## <a name="subscribe-to-the-api"></a>API 구독

1. [Video Analyzer for Media 개발자 포털](https://api-portal.videoindexer.ai/)에 로그인합니다.

    [로그인 정보](release-notes.md#october-2020)에 대한 릴리스 정보를 검토합니다.
    
     ![Video Analyzer for Media 개발자 포털에 로그인](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Video Analyzer for Media에 가입할 때 사용한 것과 동일한 공급자를 사용해야 합니다.
   > * 개인 Google 및 Microsoft(Outlook/Live) 계정은 평가판 계정에만 사용할 수 있습니다. Azure에 연결되는 계정에는 Azure AD가 필요합니다.
   > * 이메일당 하나의 활성 계정만 있을 수 있습니다. 사용자가 LinkedIn에서 user@gmail.com을 사용하여 로그인하고 나중에 Google에서 user@gmail.com을 사용하여 로그인하려고 하면 사용자가 이미 존재한다는 오류 페이지가 표시됩니다.

2. 가입합니다.

   [제품](https://api-portal.videoindexer.ai/products) 탭을 선택합니다. 그런 다음, [권한 부여]를 선택하고 가입합니다.
    
   ![Video Indexer 개발자 포털의 제품 탭](./media/video-indexer-use-apis/authorization.png)

   > [!NOTE]
   > 새 사용자는 자동으로 권한 부여에 가입됩니다.
    
   구독 후 **제품** -> **권한 부여** 에서 구독을 찾을 수 있습니다. 구독 페이지에서 기본 및 보조 키를 찾을 수 있습니다. 키는 보호해야 하고, 서버 코드에서만 사용할 수 있습니다. 클라이언트 쪽(.js, .html 등)에서 사용할 수 없습니다.

   ![Video Indexer 개발자 포털의 구독 및 키](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Video Analyzer for Media 사용자는 단일 구독 키를 사용하여 여러 Video Analyzer for Media 계정에 연결할 수 있습니다. 그런 다음 이러한 Video Analyzer for Media 계정을 다른 Media Services 계정에 연결할 수 있습니다.

## <a name="obtain-access-token-using-the-authorization-api"></a>권한 부여 API를 사용하여 액세스 토큰 가져오기

권한 부여 API를 구독하면 액세스 토큰을 가져올 수 있습니다. 이러한 액세스 토큰은 작업 API와 대조하여 인증하는 데 사용됩니다.

작업 API에 대한 각 호출은 호출의 권한 부여 범위와 일치하는 액세스 토큰과 연결되어야 합니다.

- 사용자 수준: 사용자 수준 액세스 토큰을 사용하여 **사용자** 수준에서 작업을 수행할 수 있습니다. 예를 들어 연결된 계정 가져오기가 있습니다.
- 계정 수준: 계정 수준 액세스 토큰을 사용하여 **계정** 수준 또는 **비디오** 수준에서 작업을 수행할 수 있습니다. 예를 들어 비디오 업로드, 모든 비디오 나열, 비디오 인사이트 가져오기 등이 있습니다.
- 비디오 수준: 비디오 수준 액세스 토큰을 사용하여 특정 **비디오** 에서 작업을 수행할 수 있습니다. 예를 들어 비디오 인사이트 가져오기, 자막 다운로드, 위젯 가져오기 등이 있습니다.

다음 두 가지 방법으로 토큰의 권한 수준을 제어할 수 있습니다.

* **계정** 토큰의 경우 **권한 있는 계정 액세스 토큰 가져오기** API를 사용하고 권한 유형(**읽기 권한자**/**기여자** / **MyAccessManager**/**소유자**)을 지정할 수 있습니다.
* 모든 토큰 유형(**계정** 토큰 포함)에 대해 **allowedit=true/false** 를 지정할 수 있습니다. **false** 는 **읽기 권한자** 권한(읽기 전용)에 해당하고 **true** 는 **기여자** 권한(읽기-쓰기)에 해당합니다.

대부분의 서버 간 시나리오에서는 **계정** 작업 및 **비디오** 작업을 모두 처리하므로 동일한 **계정** 토큰을 사용합니다. 그러나 클라이언트 쪽에서(예: JavaScript에서) Video Analyzer for Media를 호출하려는 경우 클라이언트에서 전체 계정에 액세스하지 못하도록 **비디오** 액세스 토큰을 사용하려고 합니다. 또한 Video Analyzer for Media 클라이언트 코드를 클라이언트에 포함할 때(예: **인사이트 위젯 가져오기** 또는 **플레이어 위젯 가져오기** 사용) **비디오** 토큰을 제공해야 하는 이유이기도 합니다.

작업을 더 쉽게 수행하려면 **권한 부여** API > **GetAccounts** 를 사용하여 먼저 사용자 토큰을 가져오지 않고 계정을 가져올 수 있습니다. 유효한 토큰이 있는 계정을 가져오도록 요청하여 추가 호출을 건너뛰고 계정 토큰을 가져올 수 있습니다.

액세스 토큰은 1시간 후에 만료됩니다. 작업 API를 사용하기 전에 먼저 액세스 토큰이 유효한지 확인해야 합니다. 만료되면 권한 부여 API를 다시 호출하여 새 액세스 토큰을 받으세요.

이제 API와의 통합을 시작할 준비가 되었습니다. [각 Video Analyzer for Media REST API에 대한 자세한 설명](https://api-portal.videoindexer.ai/)을 찾아봅니다.

## <a name="account-id"></a>계정 ID

계정 ID 매개 변수는 모든 작업 API 호출에 필요합니다. 계정 ID는 다음 방법 중 하나를 사용하여 얻을 수 있는 GUID입니다.

* 계정 ID를 얻으려면 **Video Analyzer for Media 웹사이트** 를 사용합니다.

    1. [Video Analyzer for Media](https://www.videoindexer.ai/) 웹 사이트로 이동하여 로그인합니다.
    2. **설정** 페이지로 이동합니다.
    3. 계정 ID를 복사합니다.

        ![Video Analyzer for Media 설정 및 계정 ID](./media/video-indexer-use-apis/account-id.png)

* **Video Analyzer for Media 개발자 포털** 을 사용하여 프로그래밍 방식으로 계정 ID를 가져옵니다.

    [계정 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) API를 사용합니다.

    > [!TIP]
    > `generateAccessTokens=true`를 정의하여 계정에 대한 액세스 토큰을 생성할 수 있습니다.

* 계정에 있는 플레이어 페이지의 URL에서 계정 ID를 가져옵니다.

    비디오를 시청할 때 ID는 `accounts` 섹션 뒤 및 `videos` 섹션 앞에 표시됩니다.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>권장 사항

이 섹션에서는 Video Analyzer for Media API를 사용할 때 몇 가지 권장 사항을 나열합니다.

- 비디오를 업로드하려는 경우 일부 공용 네트워크 위치(예: Azure Blob Storage 계정)에 파일을 배치하는 것이 좋습니다. 비디오에 대한 링크를 가져오고, URL을 파일 업로드 매개 변수로 제공합니다.

    Video Analyzer for Media에 제공된 URL은 미디어(오디오 또는 비디오) 파일을 가리켜야 합니다. URL(또는 SAS URL)은 브라우저에 붙여넣어 쉽게 확인할 수 있습니다. 파일 재생/다운로드가 시작되면 좋은 URL일 가능성이 높습니다. 브라우저에서 일부 시각화를 렌더링하는 경우 파일이 아니라 HTML 페이지에 대한 링크일 수 있습니다.

- 지정된 비디오에 대한 비디오 인사이트를 가져오는 API를 호출하면 자세한 JSON 출력을 응답 콘텐츠로 가져옵니다. [이 항목에서 반환된 JSON에 대한 자세한 내용을 참조하세요](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>코드 샘플

다음 C# 코드 조각은 모든 Video Analyzer for Media API를 함께 사용하는 방법을 보여 줍니다.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료한 후 사용하지 않을 리소스를 삭제합니다.

## <a name="see-also"></a>참고 항목

- [Video Analyzer for Media 개요](video-indexer-overview.md)
- [지역](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>다음 단계

- [출력 JSON에 대한 세부 정보 검사](video-indexer-output-json-v2.md)
- 비디오 업로드 및 인덱싱의 중요한 측면을 보여 주는 [샘플 코드](https://github.com/Azure-Samples/media-services-video-indexer)를 확인하세요. 코드를 따라가면 기본 기능에 API를 사용하는 방법을 이해할 수 있습니다. 인라인 주석을 읽고 모범 사례를 확인하세요.

