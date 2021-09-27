---
title: .NET - 서비스 간 인증 - Data Lake Storage Gen1
description: Azure Active Directory를 사용하여 .NET SDK로 Azure Data Lake Storage Gen1 서비스 간 인증을 수행하는 방법을 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: b415717f8478600ba62b2f995941e57293f6ee1c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617738"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>.NET SDK를 사용하여 Azure Data Lake Storage Gen1로 서비스 간 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK 사용](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API 사용](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

이 문서에서는 .NET SDK를 사용하여 Azure Data Lake Storage Gen1로 서비스 간 인증을 수행하는 방법을 배웁니다. .NET SDK를 사용한 Data Lake Storage Gen1 최종 사용자 인증의 경우 [.NET SDK를 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-net-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
* **Visual Studio 2013 혹은 그 이상**. 아래 지침에서는 Visual Studio 2019를 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "웹" 애플리케이션 만들기**. [Azure Active Directory를 사용하여 Data Lake Storage Gen1로 서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

## <a name="create-a-net-application"></a>.NET 애플리케이션 만들기
1. Visual Studio에서 **파일** 메뉴, **새로 만들기**, **프로젝트** 를 차례로 선택합니다.
2. **콘솔 앱(.NET Framework)** , **다음** 을 차례로 선택합니다.
3. **프로젝트 이름** 에 `CreateADLApplication`를 입력하고 **만들기** 를 선택합니다.

4. NuGet 패키지를 프로젝트에 추가합니다.

   1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리** 를 클릭합니다.
   2. **NuGet 패키지 관리자** 탭에서 **패키지 원본** 이 **nuget.org** 로 설정되어 있고 **시험판 포함** 확인란이 선택되어 있는지 확인합니다.
   3. 다음 NuGet 패키지를 검색하고 설치합니다.

      * `Microsoft.Azure.Management.DataLake.Store` - 이 자습서에서는 v2.1.3-미리 보기를 사용합니다.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 이 자습서는 v2.2.12를 사용합니다.

        ![NuGet 원본 추가](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "새 Azure Data Lake 계정 만들기")
   4. **NuGet 패키지 관리자** 를 닫습니다.

5. **Program.cs** 를 열고 기존 코드를 삭제한 후 다음 문을 포함시켜서 네임스페이스에 대한 참조를 추가합니다.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>클라이언트 암호를 사용하여 서비스 간 인증
.NET 클라이언트 애플리케이션에서 이 코드 조각을 추가합니다. 자리 표시자 값을 Microsoft Azure Active Directory 웹 애플리케이션에서 검색된 값(필수 구성 요소로 나열된)으로 바꿉니다. 이 코드 조각을 사용하면 Azure AD 웹 애플리케이션의 클라이언트 비밀/키를 사용하여 애플리케이션을 Data Lake Storage Gen1에 **비 대화형으로** 인증할 수 있습니다.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

앞의 코드 조각은 도우미 함수 `GetCreds_SPI_SecretKey`를 사용합니다. 이 도우미 함수에 대한 코드는 [여기 GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey)에서 사용할 수 있습니다.

## <a name="service-to-service-authentication-with-certificate"></a>인증서를 사용하여 서비스 간 인증

.NET 클라이언트 애플리케이션에서 이 코드 조각을 추가합니다. 자리 표시자 값을 Microsoft Azure Active Directory 웹 애플리케이션에서 검색된 값(필수 구성 요소로 나열된)으로 바꿉니다. 이 코드 조각을 사용하면 Azure AD 웹 애플리케이션의 인증서를 사용하여 애플리케이션을 Data Lake Storage Gen1에 **비 대화형으로** 인증할 수 있습니다. Microsoft Azure Active Directory 애플리케이션을 만드는 방법에 대한 지침은 [인증서로 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)를 참조하세요.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

앞의 코드 조각은 도우미 함수 `GetCreds_SPI_Cert`를 사용합니다. 이 도우미 함수에 대한 코드는 [여기 GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 .NET SDK를 사용하여 Data Lake Storage Gen1로 인증하는 서비스 간 인증을 사용하는 방법을 배웠습니다. 이제 다음 문서를 통해 .NET SDK를 Data Lake Storage Gen1과 함께 사용하는 방법을 살펴볼 수 있습니다.

* [.NET SDK를 사용한 Data Lake Storage Gen1의 계정 관리 작업](data-lake-store-get-started-net-sdk.md)
* [.NET SDK를 사용한 Data Lake Storage Gen1의 데이터 작업](data-lake-store-data-operations-net-sdk.md)
