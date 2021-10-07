---
title: '자습서: Azure 이미지에 대한 메타데이터 생성'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Azure Computer Vision 서비스를 웹앱에 통합하여 이미지에 대한 메타데이터를 생성하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 07/06/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: f97f01ecb1b11af6ca4292c8b2bfd3ebdb50e943
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360260"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>자습서: Computer Vision을 사용하여 Azure Storage에서 이미지 메타데이터 생성

이 자습서에서는 Azure Computer Vision 서비스를 웹앱에 통합하여 업로드된 이미지에 대한 메타데이터를 생성하는 방법에 대해 알아봅니다. 이 기능은 회사가 모든 이미지에 대한 설명 캡션 또는 검색 가능한 키워드를 빠르게 생성하려는 경우와 같이 [DAM(디지털 자산 관리)](../overview.md#computer-vision-for-digital-asset-management) 시나리오에 유용합니다.

Visual Studio를 사용하여 사용자가 업로드한 이미지를 수락하고 Azure Blob Storage에 이미지를 저장하는 MVC 웹앱을 작성합니다. C#에서 Blob을 읽고 쓰는 방법과 Blob 메타데이터를 사용하여 만든 Blob에 추가 정보를 연결하는 방법을 배웁니다. 그런 다음 사용자가 업로드한 각 이미지를 Computer Vision API에 제출하여 이미지에 대한 캡션 및 검색 메타데이터를 생성합니다. 마지막으로 Visual Studio를 사용하여 클라우드에 앱을 배포할 수 있습니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 스토리지 계정 및 스토리지 컨테이너 만들기
> * Visual Studio에서 웹앱을 만들고 Azure에 배포
> * Computer Vision API를 사용하여 이미지에서 정보 추출
> * Azure Storage 이미지에 메타데이터 연결
> * [Azure Storage Explorer](http://storageexplorer.com/)를 사용하여 이미지 메타데이터 확인

> [!TIP]
> [Computer Vision을 사용하여 메타데이터 생성](#Exercise5) 섹션은 이미지 분석과 가장 관련이 있습니다. 이미지 분석이 기존 애플리케이션에 통합되는 방식을 확인하려면 여기로 건너뜁니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2017 Community 버전](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) 이상("ASP.NET 및 웹 개발" 및 "Azure 개발" 워크로드가 설치되어 있음)
- [Azure Storage Explorer](http://storageexplorer.com/) 도구가 설치되어 있습니다.

<a name="Exercise1"></a>
## <a name="create-a-storage-account"></a>스토리지 계정 만들기

이 섹션에서는 [Azure Portal](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa)을 사용하여 스토리지 계정을 만듭니다. 그런 다음 한 쌍의 컨테이너를 만듭니다. 하나는 사용자가 업로드한 이미지를 저장하고 다른 하나는 업로드된 이미지에서 생성된 이미지 썸네일을 저장합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa)을 엽니다. 로그인하라는 메시지가 표시되면 Microsoft 계정을 사용하여 로그인합니다.
1. 스토리지 계정을 만들려면 왼쪽 리본에서 **+ 리소스 만들기** 를 클릭합니다. 그런 다음 **스토리지** 를 클릭한 다음 **스토리지 계정** 을 클릭합니다.

    ![스토리지 계정 만들기](Images/new-storage-account.png)
1. **이름** 필드에 스토리지 계정의 고유한 이름을 입력하고 그 옆에 녹색 확인 표시가 나타나는지 확인합니다. 이 이름은 이 계정에서 생성된 Blob이 액세스되는 URL의 일부를 형성하기 때문에 중요합니다. "IntellipixResources"라는 새 리소스 그룹에 스토리지 계정을 배치하고 가장 가까운 지역을 선택합니다. 새 스토리지 계정을 만들려면 화면 하단의 **검토 + 만들기** 단추를 클릭하여 마칩니다.
    > [!NOTE]
    > 스토리지 계정 이름의 길이는 3~24자이며 숫자와 소문자만 포함할 수 있습니다. 또한 입력하는 이름은 Azure 내에서 고유해야 합니다. 다른 사람이 같은 이름을 선택한 경우 **이름** 입력란에 빨간색 느낌표와 함께 해당 이름을 사용할 수 없다는 알림이 표시됩니다.
   
    ![새 스토리지 계정에 대한 매개 변수 지정](Images/create-storage-account.png)
1. 왼쪽 리본에서 **리소스 그룹** 을 클릭합니다. 그런 다음 "IntellipixResources" 리소스 그룹을 클릭합니다.

    ![리소스 그룹 열기](Images/open-resource-group.png)
1. 리소스 그룹에 대해 열리는 탭에서 만든 스토리지 계정을 클릭합니다. 스토리지 계정이 아직 없는 경우 탭 상단에 나타날 때까지 **새로 고침** 을 클릭할 수 있습니다.

    ![새 스토리지 계정 열기](Images/open-storage-account.png)
1. 스토리지 계정 탭에서 **Blob** 을 클릭하여 이 계정과 연결된 컨테이너 목록을 봅니다.

    ![Blob 보기 단추](Images/view-containers.png)

1. 스토리지 계정에는 현재 컨테이너가 없습니다. Blob을 만들려면 먼저 Blob을 저장할 컨테이너를 만들어야 합니다. **+ 컨테이너** 를 클릭하여 새 컨테이너를 만듭니다. **이름** 필드에 `photos`를 입력하고 **공용 액세스 수준** 으로 **Blob** 을 선택합니다. 그런 다음 **확인** 을 클릭하여 "photos"라는 컨테이너를 만듭니다.

    > 기본적으로 컨테이너와 해당 콘텐츠는 비공개입니다. 액세스 수준으로 **Blob** 을 선택하면 "photos" 컨테이너의 Blob에 공개적으로 액세스할 수 있지만 컨테이너 자체는 공개되지 않습니다. "photos" 컨테이너에 저장된 이미지가 웹앱에서 연결되기 때문에 이를 수행하는 것이 좋습니다. 

    !["photos" 컨테이너 만들기](Images/create-photos-container.png)

1. 이전 단계를 반복하여 "thumbnails"라는 컨테이너를 만들고 컨테이너의 **공용 액세스 수준** 이 **Blob** 으로 설정되었는지 다시 한 번 확인합니다.
1. 두 컨테이너가 이 스토리지 계정의 컨테이너 목록에 표시되고 이름의 철자가 올바른지 확인합니다.

    ![새로운 컨테이너](Images/new-containers.png)

1. "Blob service" 화면을 닫습니다. 스토리지 계정 화면 왼쪽의 메뉴에서 **액세스 키** 를 클릭한 다음 **key1** 의 **KEY** 옆에 있는 **복사** 단추를 클릭합니다. 나중에 사용할 수 있도록 이 액세스 키를 즐겨 사용하는 텍스트 편집기에 붙여넣습니다.

    ![액세스 키 복사](Images/copy-storage-account-access-key.png)

이제 빌드하려는 앱에 업로드된 이미지와 이미지를 저장할 컨테이너를 보관할 스토리지 계정을 만들었습니다. 

<a name="Exercise2"></a>
## <a name="run-azure-storage-explorer"></a>Azure Storage Explorer 실행

[Azure Storage Explorer](http://storageexplorer.com/)는 Windows, macOS, Linux가 실행되는 PC에서 Azure Storage 작업을 위한 그래픽 인터페이스를 제공하는 무료 도구입니다. Azure Portal과 거의 동일한 기능을 제공하며 Blob 메타데이터를 보는 기능과 같은 기타 기능을 제공합니다. 이 섹션에서는 Microsoft Azure Storage Explorer를 사용하여 이전 섹션에서 만든 컨테이너를 봅니다.

1. Storage Explorer를 설치하지 않았거나 최신 버전을 실행하고 있는지 확인하려면 http://storageexplorer.com/ 으로 이동하여 이를 다운로드하여 설치합니다.
1. Storage Explorer를 시작합니다. 로그인하라는 메시지가 표시되면 Azure Portal에 로그인할 때 사용한 것과&mdash;동일한 Microsoft 계정을 사용하여 로그인합니다. Storage Explorer의 왼쪽 창에 스토리지 계정이 표시되지 않으면 아래 강조 표시된 **계정 관리** 단추를 클릭하고 Microsoft 계정과 스토리지 계정을 만드는 데 사용된 구독이 모두 Storage Explorer에 추가되었는지 확인합니다.

    ![Storage Explorer에서 계정 관리](Images/add-account.png)

1. 스토리지 계정 옆에 있는 작은 화살표를 클릭하여 해당 콘텐츠를 표시한 다음 **Blob 컨테이너** 옆에 있는 화살표를 클릭합니다. 생성한 컨테이너가 목록에 나타나는지 확인합니다.

    ![Blob 컨테이너 보기](Images/storage-explorer.png)

컨테이너는 현재 비어 있지만 앱이 배포되고 사진 업로드를 시작하면 컨테이너가 변경됩니다. Storage Explorer를 설치하면 앱이 Blob Storage에 쓰는 내용을 쉽게 확인할 수 있습니다.

<a name="Exercise3"></a>
## <a name="create-a-new-web-app-in-visual-studio"></a>Visual Studio에서 새 웹앱 만들기

이 섹션에서는 Visual Studio에서 새 웹앱을 만들고, 이미지를 업로드하고, Blob Storage에 쓰고, 웹 페이지에 표시하는 데 필요한 기본 기능을 구현하는 코드를 추가합니다.

1. Visual Studio를 시작하고 **파일 -> 새로 만들기 -> 프로젝트** 명령을 사용하여 "Intellipix"("Intelligent Pictures"의 약자)라는 새 Visual C# **ASP.NET 웹 애플리케이션** 프로젝트를 만듭니다.

    ![새 웹 애플리케이션 프로젝트 만들기](Images/new-web-app.png)

1. "새 ASP.NET 웹 애플리케이션" 대화 상자에서 **MVC** 가 선택되어 있는지 확인합니다. 그런 후 **OK** 를 클릭합니다.

    ![새 ASP.NET MVC 프로젝트 만들기](Images/new-mvc-project.png)

1. 잠시 시간을 내어 솔루션 탐색기에서 프로젝트 구조를 검토하세요. 무엇보다도 프로젝트의 MVC 컨트롤러를 포함하는 **Controllers** 라는 폴더와 프로젝트의 보기를 포함하는 **Views** 라는 폴더가 있습니다. 애플리케이션을 구현할 때 이러한 폴더 및 기타 폴더의 자산을 사용하게 됩니다.

    ![솔루션 탐색기에 표시된 프로젝트](Images/project-structure.png)

1. Visual Studio의 **디버그 -> 디버깅 없이 시작** 명령(또는 **Ctrl+F5** 누름)을 사용하여 브라우저에서 애플리케이션을 시작합니다. 애플리케이션이 현재 상태에서 어떻게 보이는지 보여 줍니다.

    ![초기 애플리케이션](Images/initial-application.png)

1. 브라우저를 닫고 Visual Studio로 돌아갑니다. 솔루션 탐색기에서 **Intellipix** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리...** 를 선택합니다. **찾아보기** 를 클릭합니다. 그런 다음 검색 상자에 `imageresizer`를 입력하고 **ImageResizer** 라는 NuGet 패키지를 선택합니다. 마지막으로 **설치** 를 클릭하여 안정적인 최신 버전의 패키지를 설치합니다. ImageResizer에는 앱에 업로드된 이미지에서 이미지 썸네일을 만드는 데 사용할 API가 포함되어 있습니다. 변경 사항을 확인하고 제공된 라이선스를 수락합니다.

    ![ImageResizer 설치](Images/install-image-resizer.png)

1. 이 프로세스를 반복하여 **WindowsAzure.Storage** 라는 NuGet 패키지를 프로젝트에 추가합니다. 이 패키지에는 .NET 애플리케이션에서 Azure Storage에 액세스하기 위한 API가 포함되어 있습니다. 변경 사항을 확인하고 제공된 라이선스를 수락합니다.

    ![WindowsAzure.Storage 설치](Images/install-storage-package.png)

1. _Web.config_ 를 열고 다음 문을 ```<appSettings>``` 섹션에 추가합니다. 여기서 ACCOUNT_NAME은 첫 번째 섹션에서 만든 스토리지 계정 이름으로, ACCOUNT_KEY는 저장한 액세스 키로 바꿉니다.

    ```xml
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=ACCOUNT_NAME;AccountKey=ACCOUNT_KEY" />
    ```

1. 프로젝트의 **Views/Shared** 폴더에서 *_Layout.cshtml* 이라는 파일을 엽니다. 19번 줄에서 "Application name"을 "Intellipix"로 변경합니다. 해당 줄은 다음과 같아야 합니다.

    ```C#
    @Html.ActionLink("Intellipix", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    > [!NOTE]
    > ASP.NET MVC 프로젝트에서 *_Layout.cshtml* 은 다른 보기의 템플릿 역할을 하는 특수 보기입니다. 일반적으로 이 파일의 모든 보기에 공통적인 머리글 및 바닥글 콘텐츠를 정의합니다.

1. 프로젝트의 **Models** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 -> 클래스...** 명령을 사용하여 *BlobInfo.cs* 라는 클래스 파일을 폴더에 추가합니다. 그런 다음 빈 **BlobInfo** 클래스를 다음 클래스 정의로 바꿉니다.

    ```C#
    public class BlobInfo
    {
        public string ImageUri { get; set; }
        public string ThumbnailUri { get; set; }
        public string Caption { get; set; }
    }
    ```

1. 프로젝트의 **Controllers** 폴더에서 *HomeController.cs* 를 열고, 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```C#
    using ImageResizer;
    using Intellipix.Models;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Configuration;
    using System.Threading.Tasks;
    using System.IO;
    ```

1. *HomeController.cs* 의 **Index** 메서드를 다음 구현으로 바꿉니다.

    ```C#
    public ActionResult Index()
    {
        // Pass a list of blob URIs in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();
    
        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;
    
            if (blob != null)
            {
                blobs.Add(new BlobInfo()
                {
                    ImageUri = blob.Uri.ToString(),
                    ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/")
                });
            }
        }
    
        ViewBag.Blobs = blobs.ToArray();
        return View();
    }
    ```

    새로운 **Index** 메서드는 `"photos"` 컨테이너의 Blob을 열거하고 ASP.NET MVC의 **ViewBag** 속성을 통해 해당 Blob을 나타내는 **BlobInfo** 개체의 배열을 보기에 전달합니다. 나중에 이러한 개체를 열거하고 사진 썸네일 컬렉션을 표시하도록 보기를 수정합니다. 스토리지 계정에 액세스하고 Blob(&mdash; **[CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet&preserve-view=true)** , **[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient?view=azure-dotnet-legacy&preserve-view=true)** 및 **[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer?view=azure-dotnet-legacy&preserve-view=true)** &mdash;)을 열거하는 데 사용할 클래스는 NuGet을 통해 설치한 **WindowsAzure.Storage** 패키지에서 가져옵니다.

1. *HomeController.cs* 의 **HomeController** 클래스에 다음 메서드를 추가합니다.

    ```C#
    [HttpPost]
    public async Task<ActionResult> Upload(HttpPostedFileBase file)
    {
        if (file != null && file.ContentLength > 0)
        {
            // Make sure the user selected an image file
            if (!file.ContentType.StartsWith("image"))
            {
                TempData["Message"] = "Only image files may be uploaded";
            }
            else
            {
                try
                {
                    // Save the original image in the "photos" container
                    CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
                    CloudBlobClient client = account.CreateCloudBlobClient();
                    CloudBlobContainer container = client.GetContainerReference("photos");
                    CloudBlockBlob photo = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                    await photo.UploadFromStreamAsync(file.InputStream);
    
                    // Generate a thumbnail and save it in the "thumbnails" container
                    using (var outputStream = new MemoryStream())
                    {
                        file.InputStream.Seek(0L, SeekOrigin.Begin);
                        var settings = new ResizeSettings { MaxWidth = 192 };
                        ImageBuilder.Current.Build(file.InputStream, outputStream, settings);
                        outputStream.Seek(0L, SeekOrigin.Begin);
                        container = client.GetContainerReference("thumbnails");
                        CloudBlockBlob thumbnail = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                        await thumbnail.UploadFromStreamAsync(outputStream);
                    }
                }
                catch (Exception ex)
                {
                    // In case something goes wrong
                    TempData["Message"] = ex.Message;
                }
            }
        }
    
        return RedirectToAction("Index");
    }
    ```

    이는 사진을 업로드할 때 호출되는 메서드입니다. 업로드된 각 이미지를 `"photos"` 컨테이너에 Blob으로 저장하고, `ImageResizer` 패키지를 사용하여 원본 이미지에서 썸네일 이미지를 만들고, `"thumbnails"` 컨테이너에 Blob으로 썸네일 이미지를 저장합니다.

1. 프로젝트의 **Views/Home** 폴더의 *Index.cshmtl* 을 열고 해당 콘텐츠를 다음 코드 및 태그로 바꿉니다.

    ```HTML
    @{
        ViewBag.Title = "Intellipix Home Page";
    }
    
    @using Intellipix.Models
    
    <div class="container" style="padding-top: 24px">
        <div class="row">
            <div class="col-sm-8">
                @using (Html.BeginForm("Upload", "Home", FormMethod.Post, new { enctype = "multipart/form-data" }))
                {
                    <input type="file" name="file" id="upload" style="display: none" onchange="$('#submit').click();" />
                    <input type="button" value="Upload a Photo" class="btn btn-primary btn-lg" onclick="$('#upload').click();" />
                    <input type="submit" id="submit" style="display: none" />
                }
            </div>
            <div class="col-sm-4 pull-right">
            </div>
        </div>
    
        <hr />
    
        <div class="row">
            <div class="col-sm-12">
                @foreach (BlobInfo blob in ViewBag.Blobs)
                {
                    <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
                }
            </div>
        </div>
    </div>
    
    @section scripts
    {
        <script type="text/javascript" language="javascript">
            if ("@TempData["Message"]" !== "") {
                alert("@TempData["Message"]");
            }
        </script>
    }
    ```

    여기에 사용된 언어는 HTML 태그에 실행 코드를 포함할 수 있는 [Razor](http://www.asp.net/web-pages/overview/getting-started/introducing-razor-syntax-c)입니다. 파일 중간에 있는 ```@foreach``` 문은 **ViewBag** 의 컨트롤러에서 전달된 **BlobInfo** 개체를 열거하고 이 개체에서 HTML ```<img>``` 요소를 만듭니다. 각 요소의 ```src``` 속성은 이미지 썸네일을 포함하는 Blob의 URI로 초기화됩니다.

1. [GitHub 샘플 데이터 리포지토리](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial)에서 _photos.zip_ 파일을 다운로드하고 압축을 풉니다. 이는 앱을 테스트하는 데 사용할 수 있는 다양한 사진 모음입니다.

1. 변경 내용을 저장하고 **Ctrl+F5** 를 눌러 브라우저에서 애플리케이션을 시작합니다. 그런 다음 **사진 업로드** 를 클릭하고 다운로드한 이미지 중 하나를 업로드합니다. 사진의 썸네일 버전이 페이지에 나타나는지 확인합니다.

    ![한 장의 사진이 업로드된 Intellipix](Images/one-photo-uploaded.png)

1. **photos** 폴더에서 이미지를 몇 개 더 업로드합니다. 이 페이지에도 표시되는지 확인합니다.

    ![세 장의 사진이 업로드된 Intellipix](Images/three-photos-uploaded.png)

1. 브라우저에서 마우스 오른쪽 단추를 클릭하고 **페이지 원본 보기** 를 선택하여 페이지의 소스 코드를 봅니다. 이미지 썸네일을 나타내는 ```<img>``` 요소를 찾습니다. 이미지에 할당된 URL은 Blob Storage의 Blob을 직접 참조하세요. 이는 컨테이너의 **공용 액세스 수준** 을 **Blob** 으로 설정하여 내부의 Blob을 공개적으로 액세스할 수 있게 하기 때문입니다.

1. Azure Storage Explorer로 돌아가서(또는 실행 중인 상태가 아닌 경우 다시 시작) 스토리지 계정에서 `"photos"` 컨테이너를 선택합니다. 컨테이너의 Blob 수는 업로드한 사진 수와 같아야 합니다. Blob 중 하나를 두 번 클릭하여 다운로드하고 Blob에 저장된 이미지를 확인합니다.

    !["photos" 컨테이너의 콘텐츠](Images/photos-container.png)

1. Storage Explorer에서 `"thumbnails"` 컨테이너를 엽니다. Blob 중 하나를 열어 이미지 업로드에서 생성된 썸네일 이미지를 봅니다.

앱은 아직 업로드한 원본 이미지를 볼 수 있는 방법을 제공하지 않습니다. 이상적으로는 이미지 썸네일을 클릭하면 원본 이미지가 표시되어야 합니다. 다음에 해당 기능을 추가합니다.

<a name="Exercise4"></a>
## <a name="add-a-lightbox-for-viewing-photos"></a>사진을 보기 위한 lightbox 추가

이 섹션에서는 무료 오픈 소스 JavaScript 라이브러리를 사용하여 사용자가 업로드한 원본 이미지(단순한 이미지 썸네일이 아님)를 볼 수 있도록 해주는 lightbox 뷰어를 추가합니다. 파일이 제공됩니다. 이를 프로젝트에 통합하고 *Index.cshtml* 을 약간 수정하기만 하면 됩니다.

1. [GitHub 코드 리포지토리](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/storage-lab-tutorial)에서 _lightbox.css_ 및 _lightbox.js_ 파일을 다운로드합니다.
1. 솔루션 탐색기에서 프로젝트의 **Scripts** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 -> 새 항목...** 명령을 사용하여 *lightbox.js* 파일을 만듭니다. [GitHub 코드 리포지토리](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/scripts/lightbox.js)에 파일 예의 내용을 붙여넣습니다.

1. 프로젝트의 "Content" 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 -> 새 항목...** 명령을 사용하여 *lightbox.css* 파일을 만듭니다. [GitHub 코드 리포지토리](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/css/lightbox.css)에 파일 예의 내용을 붙여넣습니다.
1. GitHub 데이터 파일 리포지토리(https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial )에서 _buttons.zip_ 파일을 다운로드하고 압축을 풉니다. 4개의 단추 이미지가 있어야 합니다.

1. 솔루션 탐색기에서 Intellipix 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 -> 새 폴더** 명령을 사용하여 "Images"라는 폴더를 프로젝트에 추가합니다.

1. **Images** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 -> 기존 항목...** 명령을 사용하여 다운로드한 4개의 이미지를 가져옵니다.

1. 프로젝트의 "App_Start" 폴더에서 *BundleConfig.cs* 를 엽니다. **BundleConfig.cs** 의 ```RegisterBundles``` 메서드에 다음 문을 추가합니다.

    ```C#
    bundles.Add(new ScriptBundle("~/bundles/lightbox").Include(
              "~/Scripts/lightbox.js"));
    ```

1. 같은 방법으로 "~/Content/css"에서 ```StyleBundle```을 생성하는 문을 찾아 번들의 스타일 시트 목록에 *lightbox.css* 를 추가합니다. 수정된 문장은 다음과 같습니다.

    ```C#
    bundles.Add(new StyleBundle("~/Content/css").Include(
              "~/Content/bootstrap.css",
              "~/Content/site.css",
              "~/Content/lightbox.css"));
    ```

1. 프로젝트의 **Views/Shared** 폴더에서 *_Layout.cshtml* 을 열고 하단 근처의 ```@RenderSection``` 문 바로 앞에 다음 문을 추가합니다.

    ```C#
    @Scripts.Render("~/bundles/lightbox")
    ```

1. 마지막 작업은 lightbox 뷰어를 홈 페이지에 통합하는 것입니다. 그렇게 하려면 *Index.cshtml*(프로젝트의 **Views/Home** 폴더에 있음)을 열고 ```@foreach``` 루프를 다음 루프로 바꿉니다.

    ```HTML
    @foreach (BlobInfo blob in ViewBag.Blobs)
    {
        <a href="@blob.ImageUri" rel="lightbox" title="@blob.Caption">
            <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
        </a>
    }
    ```

1. 변경 내용을 저장하고 **Ctrl+F5** 를 눌러 브라우저에서 애플리케이션을 시작합니다. 그런 다음 이전에 업로드한 이미지 중 하나를 클릭합니다. lightbox가 나타나고 이미지의 확대 보기가 표시되는지 확인합니다.

    ![확대된 이미지](Images/lightbox-image.png)

1. lightbox를 닫으려면 lightbox의 오른쪽 하단에 있는 **X** 를 클릭합니다.

이제 업로드한 이미지를 볼 수 있습니다. 다음 단계는 해당 이미지로 더 많은 작업을 수행하는 것입니다.

<a name="Exercise5"></a>
## <a name="use-computer-vision-to-generate-metadata"></a>Computer Vision을 사용하여 메타데이터 생성

### <a name="create-a-computer-vision-resource"></a>Computer Vision 리소스 만들기

Azure 계정에 대한 Computer Vision 리소스를 만들어야 합니다. 이 리소스는 Azure의 Computer Vision 서비스에 대한 액세스를 관리합니다. 

1. [Azure Cognitive Services 리소스 만들기](../../cognitive-services-apis-create-account.md)의 지침에 따라 Computer Vision 리소스를 만듭니다.

1. 그런 다음, 리소스 그룹의 메뉴로 이동하여 만든 Computer Vision API 구독을 클릭합니다. **엔드포인트** 아래의 URL을 잠시 후에 쉽게 검색할 수 있는 위치에 복사합니다. 그런 다음, **액세스 키 표시** 를 클릭합니다.

    ![설명된 엔드포인트 URL 및 액세스 키 링크가 있는 Azure Portal 페이지](Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. 다음 창에서 **키 1** 의 값을 클립보드에 복사합니다.

    ![키 관리 대화 상자(설명된 복사 단추 포함)](Images/copy-vision-key.png)

### <a name="add-computer-vision-credentials"></a>Computer Vision 자격 증명 추가

다음으로, Computer Vision 리소스에 액세스할 수 있도록 필요한 자격 증명을 앱에 추가합니다.

프로젝트 루트에서 *Web.config* 파일로 이동합니다. 다음 명령문을 파일의 `<appSettings>` 섹션에 추가하고, `VISION_KEY`를 이전 단계에서 복사한 키로 바꾸고, `VISION_ENDPOINT`를 이전 단계에서 저장한 URL로 바꿉니다.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

그런 다음, 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, **NuGet 패키지 관리** 명령을 사용하여 **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** 패키지를 설치합니다. 이 패키지에는 Computer Vision API를 호출하는 데 필요한 형식이 포함되어 있습니다.

### <a name="add-metadata-generation-code"></a>메타데이터 생성 코드 추가

다음으로, Computer Vision 서비스를 실제로 사용하여 이미지에 대한 메타데이터를 만드는 코드를 추가합니다.

1. 프로젝트의 **Controllers** 폴더에서 *HomeController.cs* 파일을 열고, 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. 다음으로, **Upload** 메서드로 이동합니다. 이 메서드는 이미지를 변환하고 Blob 스토리지에 업로드합니다. `// Generate a thumbnail`로 시작하는 블록 바로 뒤(또는 image-blob-creation 프로세스의 끝)에 다음 코드를 추가합니다. 이 코드는 이미지(`photo`)가 포함된 Blob을 가져오고, Computer Vision을 사용하여 해당 이미지에 대한 설명을 생성합니다. 또한 Computer Vision API에서 이미지에 적용되는 키워드 목록도 생성합니다. 생성된 설명과 키워드는 나중에 검색할 수 있도록 Blob의 메타데이터에 저장됩니다.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>() { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. 다음으로, 동일한 파일에서 **Index** 메서드로 이동합니다. 이 메서드는 대상 Blob 컨테이너(**IListBlobItem** 인스턴스)에 저장된 이미지 Blob을 열거하고, 이를 애플리케이션 보기로 전달합니다. 이 메서드의 `foreach` 블록을 다음 코드로 바꿉니다. 이 코드는 **CloudBlockBlob.FetchAttributes** 를 호출하여 각 Blob의 연결된 메타데이터를 가져옵니다. 컴퓨터에서 생성된 메타데이터의 설명(`caption`)을 추출하여 보기에 전달되는 **BlobInfo** 개체에 추가합니다.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

### <a name="test-the-app"></a>앱 테스트

Visual Studio에서 변경 내용을 저장하고 **Ctrl+F5** 를 눌러 브라우저에서 애플리케이션을 시작합니다. 앱을 사용하여 다운로드한 사진 세트 또는 자신의 폴더에서 몇 개의 이미지를 더 업로드합니다. 보기의 이미지 위를 커서로 가리키면 도구 설명 창이 나타나고 컴퓨터에서 생성된 새 이미지에 대한 캡션이 표시됩니다.

![컴퓨터에서 생성된 캡션](Images/thumbnail-with-tooltip.png)

연결된 모든 메타데이터를 보려면 Azure Storage Explorer를 사용하여 이미지에 사용하고 있는 스토리지 컨테이너를 봅니다. 컨테이너에서 임의의 Blob을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 대화 상자에서 키-값 쌍 목록이 표시됩니다. 컴퓨터에서 생성된 이미지 설명은 `Caption` 항목에 저장되고, 검색 키워드는 `Tag0`, `Tag1` 등에 저장됩니다. 완료되면 **취소** 를 클릭하여 대화 상자를 닫습니다.

![메타데이터 태그가 나열된 이미지 속성 대화 상자 창](Images/blob-metadata.png)

<a name="Exercise6"></a>
## <a name="add-search-to-the-app"></a>앱에 검색 추가

이 섹션에서는 사용자가 업로드한 이미지에서 키워드 검색을 수행할 수 있도록 홈페이지에 검색 상자를 추가합니다. 키워드는 Computer Vision API에 의해 생성되고 Blob 메타데이터에 저장된 키워드입니다.

1. 프로젝트의 **Views/Home** 폴더에서 *Index.cshtml* 을 열고 ```class="col-sm-4 pull-right"``` 특성이 있는 빈 ```<div>``` 요소에 다음 문을 추가합니다.

    ```HTML
    @using (Html.BeginForm("Search", "Home", FormMethod.Post, new { enctype = "multipart/form-data", @class = "navbar-form" }))
    {
        <div class="input-group">
            <input type="text" class="form-control" placeholder="Search photos" name="term" value="@ViewBag.Search" style="max-width: 800px">
            <span class="input-group-btn">
                <button class="btn btn-primary" type="submit">
                    <i class="glyphicon glyphicon-search"></i>
                </button>
            </span>
        </div>
    }
    ```

    이 코드와 태그는 검색 상자와 **검색** 단추를 홈페이지에 추가합니다.

1. 프로젝트의 **Controllers** 폴더에서 *HomeController.cs* 를 열고 다음 메서드를 **HomeController** 클래스에 추가합니다.

    ```C#
    [HttpPost]
    public ActionResult Search(string term)
    {
        return RedirectToAction("Index", new { id = term });
    }
    ```

    이는 사용자가 이전 단계에서 추가한 **검색** 단추를 클릭할 때 호출되는 메서드입니다. 페이지를 새로 고치고 URL에 검색 매개 변수를 포함합니다.

1. **Index** 메서드를 다음 구현으로 바꿉니다.

    ```C#
    public ActionResult Index(string id)
    {
        // Pass a list of blob URIs and captions in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();

        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;

            if (blob != null)
            {
                blob.FetchAttributes(); // Get blob metadata

                if (String.IsNullOrEmpty(id) || HasMatchingMetadata(blob, id))
                {
                    var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;

                    blobs.Add(new BlobInfo()
                    {
                        ImageUri = blob.Uri.ToString(),
                        ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                        Caption = caption
                    });
                }
            }
        }

        ViewBag.Blobs = blobs.ToArray();
        ViewBag.Search = id; // Prevent search box from losing its content
        return View();
    }
    ```

    이제 **Index** 메서드가 사용자가 검색 상자에 입력한 값을 포함하는 매개 변수 _id_ 를 허용하는지 확인합니다. 비어 있거나 누락된 _id_ 매개 변수는 모든 사진이 표시되어야 함을 나타냅니다.

1. **HomeController** 클래스에 다음 도우미 메서드를 추가합니다.

    ```C#
    private bool HasMatchingMetadata(CloudBlockBlob blob, string term)
    {
        foreach (var item in blob.Metadata)
        {
            if (item.Key.StartsWith("Tag") && item.Value.Equals(term, StringComparison.InvariantCultureIgnoreCase))
                return true;
        }

        return false;
    }
    ```

    이 메서드는 지정된 이미지 Blob에 첨부된 메타데이터 키워드에 사용자가 입력한 검색어가 포함되어 있는지 확인하기 위해 **Index** 메서드에 의해 호출됩니다.

1. 애플리케이션을 다시 실행하고 여러 장의 사진을 업로드합니다. 자습서와 함께 제공된 사진뿐만 아니라 자신의 사진을 자유롭게 사용합니다.

1. 검색 상자에 "river"와 같은 키워드를 입력합니다. 그런 다음 **검색** 단추를 클릭합니다.

    ![검색 수행](Images/enter-search-term.png)

1. 검색 결과는 입력한 내용과 업로드한 이미지에 따라 달라집니다. 그러나 결과는 메타데이터 키워드에 입력한 키워드의 전체 또는 일부가 포함된 필터링된 이미지 목록이어야 합니다.

    ![검색 결과](Images/search-results.png)

1. 브라우저의 뒤로 단추를 클릭하면 모든 이미지가 다시 표시됩니다.

거의 완료되었습니다. 이제 앱을 클라우드에 배포할 때입니다.

<a name="Exercise7"></a>
## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

이 섹션에서는 Visual Studio에서 Azure로 앱을 배포합니다. Visual Studio에서 Azure 웹앱을 만들도록 허용하면 Azure Portal로 이동하여 별도로 만들 필요가 없습니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시...** 를 선택합니다. **Microsoft Azure App Service** 및 **새로 만들기** 가 선택되어 있는지 확인한 다음 **게시** 단추를 클릭합니다.

    ![앱 게시](Images/publish-1.png)

1. 다음 대화 상자에서 **리소스 그룹** 아래의 "IntellipixResources" 리소스 그룹을 선택합니다. "App Service 계획" 옆에 있는 **새로 만들기...** 단추를 클릭하고 [스토리지 계정 만들기](#Exercise1)에서 스토리지 계정에 대해 선택한 동일한 위치에 새 App Service 계획을 만들어 다른 모든 곳에서 기본값을 적용합니다. **만들기** 단추를 클릭하여 마칩니다.

    ![Azure 웹앱 만들기](Images/publish-2.png)

1. 잠시 후 앱이 브라우저 창에 나타납니다. 주소 표시줄의 URL을 적어 둡니다. 앱이 더 이상 로컬에서 실행되지 않습니다. 공개적으로 접근할 수 있는 웹에 있습니다.

    ![완성된 제품입니다!](Images/vs-intellipix.png)

앱을 변경하고 변경 사항을 웹에 푸시하려면 게시 프로세스를 다시 진행합니다. 웹에 게시하기 전에 변경 사항을 로컬에서 테스트할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

웹앱에서 계속 작업하려면 [다음 단계](#next-steps) 섹션을 참조하세요. 이 애플리케이션을 계속 사용하지 않으려면 모든 앱 관련 리소스를 삭제해야 합니다. 리소스를 삭제하려면 단순히 Azure Storage 구독 및 Computer Vision 리소스가 포함된 리소스 그룹을 삭제할 수 있습니다. 그러면 스토리지 계정, 이 계정에 업로드된 Blob 및 ASP.NET 웹앱에 연결하는 데 필요한 App Service 리소스가 제거됩니다. 

리소스 그룹을 삭제하려면 포털에서 **리소스 그룹** 탭을 열고, 이 프로젝트에 사용한 리소스 그룹으로 이동한 다음, 보기의 위쪽에서 **리소스 그룹 삭제** 를 클릭합니다. 삭제할 것인지 확인하기 위해 리소스 그룹의 이름을 입력하라는 메시지가 표시됩니다. 삭제된 리소스 그룹은 복구할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Azure를 사용하고 Intellipix 앱을 추가로 개발하기 위해 수행할 수 있는 작업이 많이 있습니다. 예를 들어 사용자 인증 및 사진 삭제에 대한 지원을 추가할 수 있으며 업로드 후 Cognitive Services에서 사진을 처리할 때까지 사용자를 강제로 기다리게 하지 않고 [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=academiccontent-github-cxa)를 사용하여 이미지가 Blob Storage에 추가될 때마다 Computer Vision API를 비동기적으로 호출할 수 있습니다. 개요에 설명된 대로 이미지에 대해 원하는 만큼 다른 이미지 분석 작업을 수행할 수도 있습니다.

> [!div class="nextstepaction"]
> [이미지 분석 개요](../overview-image-analysis.md)
