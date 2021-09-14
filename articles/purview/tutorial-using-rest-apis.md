---
title: '자습서: REST API 사용'
description: 이 자습서에서는 Azure Purview REST API를 사용하여 카탈로그의 콘텐츠에 액세스하는 방법을 설명합니다.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: de49c820100140f0822149bba84f7a38f034323b
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473283"
---
# <a name="tutorial-use-the-rest-apis"></a>자습서: REST API 사용

이 자습서에서는 Azure Purview REST API를 사용하는 방법을 알아봅니다. Azure Purview 카탈로그에 데이터를 제출하거나, 자동화된 프로세스에 카탈로그를 포함하거나, 카탈로그에서 자체 사용자 환경을 구축하려는 개발자는 REST API를 사용하여 이를 수행할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 서비스 주체(애플리케이션) 만들기
> * 서비스 주체(애플리케이션)를 신뢰하도록 카탈로그 구성
> * REST API 설명서 보기
> * REST API를 사용하는 데 필요한 값 수집
> * Postman 클라이언트를 사용하여 REST API 호출
> * .NET 클라이언트를 생성하여 REST API 호출

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 시작하려면 기존 Azure Purview 계정이 있어야 합니다. 카탈로그가 없는 경우 [Azure Purview 계정 만들기 빠른 시작](create-catalog-portal.md)을 참조하세요.

* 카탈로그에 데이터를 추가해야 하는 경우 [시작 키트를 실행하고 데이터를 스캔하는 방법에 대한 자습서](tutorial-scan-data.md)를 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-service-principal-application"></a>서비스 주체(애플리케이션) 만들기

REST API 클라이언트가 카탈로그에 액세스하려면 카탈로그에서 인식하고 신뢰하도록 구성된 ID와 서비스 주체(애플리케이션)가 클라이언트에 있어야 합니다. 카탈로그에 대한 REST API 호출을 수행하면 해당 호출에 서비스 주체의 ID가 사용됩니다.

기존 서비스 주체(애플리케이션 ID)를 사용하는 고객은 실패율이 높습니다. 따라서 API 호출에 사용할 새 서비스 주체를 만드는 것이 좋습니다.

새 서비스 주체를 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 검색하여 선택합니다.
1. **Azure Active Directory** 페이지의 왼쪽 창에서 **앱 등록** 을 선택합니다.
1. **새 등록** 을 선택합니다.
1. **애플리케이션 등록** 페이지에서 다음을 수행합니다.
    1. 애플리케이션 **이름**(서비스 주체 이름)을 입력합니다.
    1. **이 조직 디렉터리의 계정만 해당( _&lt;테넌트 이름만&gt;_ - 단일 테넌트)** 을 선택합니다.
    1. **리디렉션 URI(선택 사항)** 로 **웹** 을 선택하고 값을 입력합니다. 이 값은 유효한 URI가 아니어도 됩니다.
    1. **등록** 을 선택합니다.
1. 나중에 사용할 수 있도록 새 서비스 주체 페이지에서 **표시 이름** 및 **애플리케이션(클라이언트) ID** 값을 복사하여 저장합니다.

   애플리케이션 ID는 샘플 코드의 `client_id` 값입니다.

서비스 주체(애플리케이션)를 사용하려면 해당 암호를 가져와야 합니다. 방법은 다음과 같습니다.

1. Azure Portal에서 **Azure Active Directory** 를 검색하여 선택한 다음, 왼쪽 창에서 **앱 등록** 을 선택합니다.
1. 목록에서 서비스 주체(애플리케이션)를 선택합니다.
1. 왼쪽 창에서 **인증서 및 비밀** 을 선택합니다.
1. **새 클라이언트 비밀** 을 선택합니다.
1. **클라이언트 암호 추가** 페이지에서 **설명** 을 입력하고 **만료** 에서 만료 시간을 선택한 다음, **추가** 를 선택합니다.

   **클라이언트 암호** 페이지에서 새 비밀의 **값** 열에 있는 문자열이 암호입니다. 이 값을 저장합니다.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="클라이언트 암호를 보여주는 스크린샷":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>서비스 주체(애플리케이션)를 신뢰하도록 카탈로그 구성

새 서비스 주체를 신뢰하도록 Azure Purview를 구성하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.

1. **Purview 계정** 페이지에서 **Purview Studio** 를 엽니다.

1. 왼쪽 메뉴에서 **데이터 맵** 을 선택합니다.

1. **컬렉션** 을 선택합니다.

1. 컬렉션 메뉴에서 루트 컬렉션을 선택합니다. 이 컬렉션은 목록에서 최상위 컬렉션이 되며 Purview 계정과 동일한 이름을 갖습니다.

1. **역할 할당** 탭을 선택합니다.

1. **데이터 큐레이터** 로 아래로 스크롤하고 **+** 사용자 단추를 선택합니다.

    > [!Note]
    > 컬렉션 관리자만 컬렉션에 대한 권한을 편집할 수 있습니다. 컬렉션 관리자가 아닌 경우 루트 컬렉션에 나열된 관리자 중 하나에 문의합니다. 자세한 내용은 [Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

1. 할당하려는 이전에 생성된 서비스 주체의 이름을 검색한 다음, 결과 창에서 해당 이름을 클릭합니다.

1. 설정 메뉴에서 **저장**

서비스 주체를 애플리케이션 관리자로 구성했으므로, 이제 서비스 주체가 카탈로그에 콘텐츠를 보낼 수 있습니다.

## <a name="view-the-rest-apis-documentation"></a>REST API 설명서 보기

API Swagger 설명서를 보려면 [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip) 파일을 다운로드하고, 파일의 압축을 풀고, index.html을 엽니다.

Azure Purview에서 제공하는 고급 검색/제안 API에 대한 자세한 내용은 Rest API 검색 필터 설명서를 참조하세요. AutoRest에서 생성하는 클라이언트는 현재 사용자 지정된 검색 매개 변수를 지원하지 않습니다. 이 문제를 해결하려면 검색-필터 문서에 따라 코드의 필터 클래스를 API 호출 매개 변수로 정의합니다. index.html 문서에는 이러한 API 예제가 포함되어 있습니다.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>REST API를 사용하는 데 필요한 값 수집

다음 값을 찾아서 저장합니다.

* 테넌트 ID:
  * [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 검색하여 선택합니다.
  * 왼쪽 창의 **관리** 섹션에서 **속성** 을 선택하고 **테넌트 ID** 를 찾은 다음, **클립보드로 복사** 아이콘을 선택하여 값을 저장합니다.
* Atlas 엔드포인트:
  * Azure Portal의 [Azure Purview 계정 페이지](https://aka.ms/purviewportal)에 표시되는 목록에서 Azure Purview 계정을 찾아 선택합니다.
  * **속성** 을 선택하고 **Atlas 엔드포인트** 를 찾은 다음, **클립보드에 복사** 아이콘을 선택하여 값을 저장합니다. 나중에 사용할 때 문자열의 *https://* 부분을 제거하세요.
* 계정 이름:
  * Atlas 엔드포인트 문자열에서 카탈로그 이름을 추출합니다. 예를 들어 Atlas 엔드포인트가 `https://ThisIsMyCatalog.catalog.purview.azure.com`이면 계정 이름은 `ThisIsMyCatalog`입니다.

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Postman 클라이언트를 사용하여 REST API 호출

1. [Postman 클라이언트](https://www.getpostman.com/)를 설치합니다.
1. 클라이언트에서 **가져오기** 를 선택하고 [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json)을 사용합니다.
1. **컬렉션** 을 선택한 다음, **테스트** 를 선택합니다.
1. **토큰 가져오기** 를 선택합니다.
    1. POST 옆에 있는 URL에서 *&lt;your-tenant-id&gt;* 를 이전 섹션에서 복사한 테넌트 ID로 바꿉니다.
    1. **본문** 탭을 선택하고, 이전 단계의 경로와 본문의 자리 표시자를 바꿉니다.

       **보내기** 를 선택하면 이름 *access_token* 및 따옴표가 붙은 문자열 값을 비롯한 JSON 구조가 응답 본문에 포함됩니다. 
    1. 다음 단계에서 사용할 전달자 토큰 값을 복사합니다(따옴표 제외).

1. 다음과 같이 **/v2/types/typedefs** 를 선택합니다.
    1. 경로의 자리 표시자를 atlas 엔드포인트 값으로 바꿉니다. 이 값은 Ibiza 포털에서 카탈로그 인스턴스를 찾아 개요를 클릭하면 얻을 수 있습니다. 

       전달자 토큰은 첫 번째 단계에서 설정됩니다(또는 "권한 부여" 탭에서 수동으로 복사 가능).

    1. 응답을 받으려면 **보내기** 를 선택합니다.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>.NET 클라이언트를 생성하여 REST API 호출

### <a name="install-autorest"></a>AutoRest 설치



1. [Node.js를 설치](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md)합니다.
1. PowerShell을 열고 다음 명령을 실행합니다.

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>rest-api-specs.zip을 다운로드하고 클라이언트 만들기

1. [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip)을 다운로드하고 파일의 압축을 풉니다.
1. 압축을 푼 rest-api-specs 폴더의 PowerShell에서 다음 명령을 실행합니다.

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   이 프로세스의 출력은 rest-api-specs 폴더에 PurviewCatalogClient라는 폴더와 CSharp라는 하위 폴더를 만듭니다.

### <a name="create-a-sample-net-console-application"></a>샘플 .NET 콘솔 애플리케이션 만들기

1. Visual Studio 2019를 엽니다. 다음 지침은 무료로 제공되는 커뮤니티 버전에서 테스트를 마쳤습니다.
1. **새 프로젝트 만들기** 페이지에서 C#으로 **콘솔 앱(.NET Core)** 프로젝트를 만듭니다.
1. 제공된 [샘플 코드](#sample-code-for-the-console-application)를 복사하여 붙여 넣습니다.
1. *accountName*, *servicePrincipalId*, *servicePrincipalKey* 및 *tenantId* 를 앞에서 수집한 값으로 바꿉니다.
1. **솔루션 탐색기** 를 사용하여 Visual Studio 프로젝트에서 **Generated** 라는 폴더를 추가합니다.
1. 앞에서 만든 rest-api-specs\PurviewCatalogClient\CSharp 폴더를 \Generated 폴더에 복사합니다. 파일 탐색기 또는 명령줄 프롬프트를 사용하여 복사하세요. 그러면 Visual Studio에서 자동으로 프로젝트에 파일을 추가합니다.
1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리** 를 선택합니다.
1. **찾아보기** 탭을 선택합니다. **Microsoft.Rest.ClientRuntime** 을 찾아 선택합니다.
1. 버전이 2.3.21 이상인지 확인한 다음, **설치** 를 선택합니다.
1. 애플리케이션을 빌드 및 실행합니다.

샘플 코드는 카탈로그에 있는 typedefs 수를 반환하고 역할 할당을 처리하는 방법을 보여줍니다. 자세한 내용은 샘플 코드의 `DoRoleAssignmentOperations()`를 참조하세요. 프로젝트에 대한 자세한 내용은 [프로젝트 설정](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md)을 참조하세요.

### <a name="sample-code-for-the-console-application"></a>콘솔 애플리케이션의 샘플 코드

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데이터 원본 관리](manage-data-sources.md)
