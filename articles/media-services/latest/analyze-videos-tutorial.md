---
title: Media Services v3으로 비디오 분석
description: Azure Media Services를 사용하여 비디오를 분석하는 방법을 알아봅니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 38586e2cc60186c929b5ca43990019e768a1ed13
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720472"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>자습서: Media Services v3으로 비디오 분석

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 자습서는 Azure Media Services로 비디오를 분석하는 방법을 보여줍니다. 녹음/녹화한 비디오나 오디오 콘텐츠를 심층적으로 파악해야 하는 다양한 경우가 있을 수 있습니다. 예를 들어, 고객 만족도를 높이 달성하기 위해 음성을 텍스트로 변환하는 프로세스를 실행하여 고객 지원 기록을 인덱스 및 대시보드와 함께 검색 가능한 카탈로그로 변환할 수 있습니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 토픽에 설명된 샘플 앱 다운로드
> * 지정된 비디오를 분석하는 코드 검사
> * 앱을 실행합니다.
> * 출력 내용 검사
> * 리소스를 정리합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안
 
다시 강조하지만 Azure Video Analyzer for Media(이전의 Video Indexer)를 사용할 때 모든 관련 법률을 준수해야 합니다. 타인의 권리를 침해하는 방식으로 Video Analyzer for Media 또는 다른 Azure 서비스를 사용하면 안 됩니다. 생체 인식 데이터를 비롯한 동영상을 처리하고 저장하기 위해 Video Analyzer for Media 서비스에 업로드하려면 동영상에 나오는 모든 사람의 적절한 동의를 포함하여 적절한 권한이 모두 있어야 합니다. Video Analyzer for Media의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Azure [Cognitive Services 사용 약관](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)을 참조하세요. Microsoft의 개인 정보 보호 의무 및 데이터 처리에 대한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [OST(온라인 서비스 사용 약관)](https://www.microsoft.com/licensing/product-licensing/products) 및 [“DPA”(데이터 처리 추록)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)를 검토하세요. 데이터 보존, 삭제/소멸을 비롯한 추가 개인 정보 취급 방침은 OST 및 [여기](../../azure-video-analyzer/video-analyzer-for-media-docs/faq.md)에서 확인할 수 있습니다. Video Analyzer for Media를 사용하는 것은 Cognitive Services 사용 약관, OST, DPA 및 개인정보처리방침을 따르는 것에 동의하는 것입니다.

## <a name="prerequisites"></a>필수 조건

- [Windows/macOS/Linux용 Visual Studio Code](https://code.visualstudio.com/)를 설치하거나 [Windows 또는 Mac용 Visual Studio 2019](https://visualstudio.microsoft.com/)를 설치합니다.
- [.NET 5.0 SDK](https://dotnet.microsoft.com/download)를 설치합니다.
- [Media Services 계정 만들기](./account-create-how-to.md) **API 액세스** 세부 정보를 JSON 형식으로 복사하거나 Media Services 계정에 연결하는 데 필요한 값을 이 샘플에 사용된 *.env* 파일 형식으로 저장해야 합니다.
- [Azure CLI를 사용하여 Azure Media Services API에 액세스](./access-api-howto.md)의 단계를 따르고 자격 증명을 저장합니다. 이 샘플에서 API에 액세스하려면 이를 사용하거나 *.env* 파일 형식으로 입력해야 합니다.

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

샘플은 [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/main/AMSV3Tutorials/AnalyzeVideos) 폴더에 있습니다.

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>지정된 비디오를 분석하는 코드 검사

이 섹션에서는 *AnalyzeVideos* 프로젝트의 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/AnalyzeVideos/Program.cs) 파일에 정의된 함수를 살펴봅니다.

샘플은 다음 작업을 완료합니다.

1. 비디오를 분석하는 **변환** 및 **작업** 을 만듭니다.
2. 입력 **자산** 을 만들고 비디오를 업로드합니다. 자산은 작업의 입력으로 사용됩니다.
3. 작업의 출력을 저장하는 출력 자산을 만듭니다.
4. 작업을 제출합니다.
5. 작업의 상태를 확인합니다.
6. 작업 실행에서 생성된 파일을 다운로드합니다.

### <a name="start-using-media-services-apis-with-the-net-sdk"></a>.NET SDK로 Media Services API 사용 시작

.NET으로 Media Services API를 사용하려면 `AzureMediaServicesClient` 개체를 만들어야 합니다. 개체를 만들려면 Azure Active Directory를 사용하여 클라이언트가 Azure에 연결할 수 있는 자격 증명을 제공해야 합니다. 또 다른 옵션은 `GetCredentialsInteractiveAuthAsync`에서 구현된 대화형 인증을 사용하는 것입니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

문서 시작 부분에서 복제한 코드에 있는 `GetCredentialsAsync` 함수는 로컬 구성 파일(*appsettings.json)* 에 제공된 자격 증명을 기반으로 또는 리포지토리의 루트에 있는 *.env* 환경 변수 파일을 통해 `ServiceClientCredentials` 개체를 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsAsync)]

대화형 인증의 경우 `GetCredentialsInteractiveAuthAsync` 함수는 대화형 인증 및 로컬 구성 파일(*appsettings.json*)에 제공된 연결 매개 변수를 기반으로 또는 리포지토리의 루트에 있는 *.env* 환경 변수 파일을 통해 `ServiceClientCredentials` 개체를 만듭니다. 이 경우 구성 또는 환경 변수 파일에 AADCLIENTID 및 AADSECRET가 필요하지 않습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>입력 자산을 만들고 여기에 로컬 파일 업로드 

**CreateInputAsset** 함수는 새로운 입력 [Asset](/rest/api/media/assets)을 만들고 이 자산에 지정된 로컬 비디오 파일을 업로드합니다. 이 Asset은 인코딩 Job에 대한 입력으로 사용됩니다. Media Services v3에서 Job에 대한 입력은 Asset이거나 HTTPS URL을 통해 Media Services 계정에서 사용할 수 있는 콘텐츠일 수도 있습니다. HTTPS URL에서 인코딩하는 방법은 [이](job-input-from-http-how-to.md) 문서를 참조하세요.  

Media Services v3에서는 Azure Storage API를 사용하여 파일을 업로드합니다. 다음 .NET 코드 조각에서 방법을 참조하세요.

다음 함수는 아래와 같은 작업을 완료합니다.

* 자산 만들기
* [스토리지에 있는 자산 컨테이너](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)에 대해 쓰기가 가능한 [SAS URL](../../storage/common/storage-sas-overview.md) 가져오기

    자산의 [ListContainerSas](/rest/api/media/assets/listcontainersas) 함수를 사용하여 SAS URL을 가져오는 경우 각 스토리지 계정에 대해 두 개의 스토리지 계정 키가 있으므로 이 함수는 여러 SAS URL을 반환합니다. 스토리지 계정에는 스토리지 계정 키를 원활하게 회전할 수 있도록 두 개의 키가 있습니다(예: 다른 키를 사용하는 동안 하나를 변경한 다음, 새 키를 사용하여 다른 키를 회전). 첫 번째 SAS URL은 스토리지 key1 및 두 번째 스토리지 key2를 나타냅니다.
* SAS URL을 사용하여 스토리지의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>작업 결과를 저장할 출력 자산 만들기

출력 [Asset](/rest/api/media/assets)은 작업의 결과를 저장합니다. 프로젝트는 출력 자산의 결과를 "output" 폴더로 저장하는 **DownloadResults** 함수를 정의합니다. 따라서 무엇을 다운로드했는지 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>비디오를 분석하는 Transform 및 Job 만들기

Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job** 을 제출합니다. 각각의 새 비디오에 대한 새 작업을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform** 이라고 합니다. 자세한 내용은 [Transform 및 Job](./transform-jobs-concept.md)을 참조하세요. 이 자습서에 설명된 샘플은 지정된 비디오를 분석하는 레시피를 정의합니다.

#### <a name="transform"></a>변환

새로운 [Transform](/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. **TransformOutput** 은 필수 매개 변수입니다. 각 **TransformOutput** 에는 **Preset** 이 포함됩니다. **Preset** 은 원하는 **TransformOutput** 을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 예제에서는 **VideoAnalyzerPreset** preset이 사용되며 해당 생성자에게 언어("en-US")가 전달됩니다(`new VideoAnalyzerPreset("en-US")`). 이 preset을 통해 비디오에서 여러 개의 오디오 및 비디오 인사이트를 추출할 수 있습니다. 비디오에서 여러 오디오 인사이트를 추출해야 하는 경우 **AudioAnalyzerPreset** preset을 사용할 수 있습니다.

**Transform** 을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인합니다. 아래 코드를 참조하세요. Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null** 을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>작업

위에서 언급했듯이 [Transform](/rest/api/media/transforms) 개체는 레시피이며 [Job](/rest/api/media/jobs)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform** 을 적용하라는 Media Services에 대한 실제 요청입니다. **Job** 은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. Media Service 계정에 있는 HTTPS URL, SAS URL 또는 자산을 사용하여 비디오 위치를 지정할 수 있습니다.

이 예에서 작업 입력은 로컬 비디오입니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업이 완료될 때까지 시간이 조금 걸립니다. 적업이 완료될 때 알림을 받을 수 있습니다. [Job](/rest/api/media/jobs) 완료에 대한 알림을 받는 옵션은 여러 가지입니다. 가장 간단한 옵션(여기에 표시됨)은 폴링을 사용하는 것입니다.

폴링은 대기 시간이 발생할 가능성이 있기 때문에 프로덕션 앱에 권장하는 방법이 아닙니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다.

Event Grid는 고가용성, 일관된 성능 및 동적 확장을 위해 설계되었습니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. 간단한 HTTP 기반 반응형 이벤트 처리는 이벤트의 지능형 필터링 및 라우팅을 통해 효율적인 솔루션을 구축하는 데 도움이 됩니다. 자세한 내용은 [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](monitoring/job-state-events-cli-how-to.md)을 참조하세요.

**작업** 은 일반적으로 **예약됨**, **대기**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중** 이 표시되고, 취소가 완료되면 **취소됨** 이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

### <a name="download-the-result-of-the-job"></a>작업 결과 다운로드

다음 함수는 출력 [Asset](/rest/api/media/assets)의 결과를 "output" 폴더로 다운로드하기 때문에 작업의 결과를 검사할 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. 일반적으로 Transform을 재사용하고 StreamingLocator를 유지합니다. 실험 후 계정을 정리하려면 재사용하지 않을 리소스를 삭제해야 합니다. 예를 들어 다음 코드는 작업 및 출력 자산을 삭제합니다.

### <a name="delete-resources-with-code"></a>코드를 사용하여 리소스 삭제

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

CLI를 사용할 수도 있습니다.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>샘플 앱 실행

Ctrl+F5 키를 눌러 *AnalyzeVideos* 앱을 실행합니다.

프로그램을 실행하면 Job은 동영상에서 찾은 각 얼굴의 썸네일을 생성합니다. insights.json 파일도 생성합니다.

## <a name="examine-the-output"></a>출력 내용 검사

분석 비디오의 출력 파일은 insights.json입니다. 이 파일에는 비디오에 대한 인사이트가 포함됩니다. json 파일에 있는 요소에 대한 설명은 [미디어 인텔리전스](./analyze-video-audio-files-concept.md) 문서를 참조하세요.

## <a name="multithreading"></a>다중 스레딩

> [!WARNING]
> Azure Media Services v3 SDK는 스레드로부터 안전하지 않습니다. 다중 스레드 앱으로 작업하는 경우 스레드마다 새로운 AzureMediaServicesClient 개체를 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
