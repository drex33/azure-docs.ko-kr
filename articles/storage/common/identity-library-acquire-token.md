---
title: Azure ID 라이브러리를 사용하여 권한 부여를 위한 액세스 토큰을 얻습니다.
titleSuffix: Azure Storage
description: Azure ID 클라이언트 라이브러리를 사용하여 애플리케이션이 Azure Storage 데이터에 대한 액세스 권한을 부여하는 데 사용할 수 있는 액세스 토큰을 얻는 방법을 알아봅니다. Azure ID 라이브러리를 사용하면 동일한 코드를 사용하여 개발 환경 또는 Azure에서 액세스 토큰을 얻을 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 62033ff6c76c2c16a6a3b0d442fec843ae2e86af
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724020"
---
# <a name="use-the-azure-identity-library-to-get-an-access-token-for-authorization"></a>Azure ID 라이브러리를 사용하여 권한 부여를 위한 액세스 토큰을 얻습니다.

Azure ID 클라이언트 라이브러리는 [Azure SDK를](https://github.com/Azure/azure-sdk)통해 azure AD(Azure Active Directory)로 권한 부여를 위한 OAuth 2.0 액세스 토큰을 얻는 프로세스를 간소화합니다. .NET, Java, Python, JavaScript 및 Go용 Azure Storage 클라이언트 라이브러리의 최신 버전은 이러한 각 언어에 대한 Azure ID 라이브러리와 통합되어 Azure Storage 요청의 권한 부여를 위한 액세스 토큰을 획득하는 간단하고 안전한 방법을 제공합니다.

Azure ID 클라이언트 라이브러리의 장점은 애플리케이션이 개발 환경 또는 Azure에서 실행 중인지에 관계없이 동일한 코드를 사용하여 액세스 토큰을 획득할 수 있다는 것입니다. Azure ID 클라이언트 라이브러리는 보안 주체에 대한 액세스 토큰을 반환합니다. Azure에서 코드를 실행하는 경우 보안 주체는 Azure 리소스, 서비스 주체 또는 사용자 또는 그룹에 대한 관리 ID일 수 있습니다. 개발 환경에서 클라이언트 라이브러리는 테스트 목적으로 사용자 또는 서비스 주체에 대한 액세스 토큰을 제공합니다.

Azure ID 클라이언트 라이브러리에서 반환된 액세스 토큰은 토큰 자격 증명에 캡슐화됩니다. 그런 다음 토큰 자격 증명을 사용하여 Azure Storage 대해 권한 있는 작업을 수행하는 데 사용할 서비스 클라이언트 개체를 얻을 수 있습니다. 액세스 토큰 및 토큰 자격 증명을 얻는 간단한 방법은 Azure ID 클라이언트 라이브러리에서 제공하는 **DefaultAzureCredential** 클래스를 사용하는 것입니다. 이 클래스의 인스턴스는 다양한 일반적인 방법으로 토큰 자격 증명을 구하려고 시도하며 개발 환경과 Azure 모두에서 작동합니다.

언어에 대한 Azure ID 클라이언트 라이브러리에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

- [.NET용 Azure ID 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme)
- [Java용 Azure ID 클라이언트 라이브러리](/java/api/overview/azure/identity-readme)
- [Python용 Azure ID 클라이언트 라이브러리](/python/api/overview/azure/identity-readme)
- [JavaScript용 Azure ID 클라이언트 라이브러리](/javascript/api/overview/azure/identity-readme)
- [Go용 Azure ID 클라이언트 라이브러리](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/azidentity)

## <a name="assign-azure-roles-for-access-to-data"></a>데이터 액세스를 위한 Azure 역할 할당

Azure AD 보안 주체가 Azure Storage 계정의 데이터에 액세스하려고 할 때 해당 보안 주체에는 데이터 리소스에 대한 권한이 있어야 합니다. 보안 주체가 Azure의 관리 ID이든 개발 환경에서 코드를 실행하는 Azure AD 사용자 계정이든 관계없이 보안 주체에는 Azure Storage 데이터에 대한 액세스 권한을 부여하는 Azure 역할이 할당되어야 합니다. Azure RBAC를 통해 데이터 액세스 권한을 할당하는 자세한 내용은 다음 문서 중 하나를 참조하세요.

- [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)
- [ 데이터에 액세스하기 위한 Azure 역할 할당](../queues/assign-azure-role-data-access.md)
- [테이블 데이터에 액세스하기 위한 Azure 역할 할당(미리 보기)](../tables/assign-azure-role-data-access.md)

> [!NOTE]
> Azure Storage 계정을 만들면 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. Azure Storage에 Azure 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너, 큐 또는 테이블 수준에서 할당할 수 있습니다.

## <a name="authenticate-the-user-in-the-development-environment"></a>개발 환경에서 사용자 인증

코드가 개발 환경에서 실행 중이면 사용 중인 도구에 따라 인증이 자동으로 처리되거나 브라우저 로그인이 필요할 수 있습니다. 예를 들어 Microsoft Visual Studio 및 Microsoft Visual Studio Code는 SSO(Single Sign-On)를 지원하므로 활성 Azure AD 사용자 계정이 인증에 자동으로 사용됩니다. SSO에 대한 자세한 내용은 [애플리케이션에 Single Sign-On](../../active-directory/manage-apps/what-is-single-sign-on.md)을 참조하세요.

서비스 주체를 만들고 런타임에 개발 환경에서 읽을 수 있는 환경 변수를 설정할 수도 있습니다.

## <a name="authenticate-a-service-principal-in-the-development-environment"></a>개발 환경에서 서비스 주체 인증

개발 환경이 Single Sign-On 또는 웹 브라우저를 통한 로그인을 지원하지 않는 경우 서비스 주체를 사용하여 개발 환경에서 인증할 수 있습니다. 서비스 주체를 만든 후 서비스 주체에 대해 반환된 값을 환경 변수에 추가합니다.

### <a name="create-the-service-principal"></a>서비스 주체 만들기

Azure CLI로 서비스 주체를 만들고 Azure 역할을 할당하려면 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 호출합니다. 새 서비스 주체에 할당하는 Azure Storage 데이터 액세스 역할을 제공합니다. 또한 역할 할당의 범위를 제공합니다. Azure Storage에 대해 제공되는 기본 제공 역할에 대한 자세한 내용은 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

서비스 주체에 역할을 할당할 수 있는 권한이 없는 경우 계정 소유자 또는 관리자에게 역할 할당을 수행하도록 요청해야 할 수 있습니다.

다음 예제에서는 Azure CLI 사용하여 새 서비스 주체를 만들고 스토리지 계정으로 범위가 지정된 **Storage Blob 데이터 기여자** 역할을 할당합니다.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac` 명령은 JSON 형식으로 서비스 주체 속성 목록을 반환합니다. 이러한 값을 복사하여 다음 단계에서 필요한 환경 변수를 만드는 데 사용할 수 있습니다.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

서비스 주체를 만드는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

- [포털에서 Azure AD 앱 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)
- [Azure PowerShell을 사용하여 Azure 서비스 주체 만들기](/powershell/azure/create-azure-service-principal-azureps)
- [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)

> [!IMPORTANT]
> Azure 역할 할당이 전파하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="set-environment-variables"></a>환경 변수 설정

Azure ID 클라이언트 라이브러리는 런타임에 세 가지 환경 변수에서 값을 읽어서 서비스 주체를 인증합니다. 다음 표에서는 각 환경 변수에 대해 설정할 값을 설명합니다.

|환경 변수|값
|-|-
|`AZURE_CLIENT_ID`|서비스 주체의 앱 ID
|`AZURE_TENANT_ID`|서비스 주체의 Azure AD 테넌트 ID
|`AZURE_CLIENT_SECRET`|서비스 사용자에 대해 생성된 암호

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫았다가 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용하는 경우 새 환경 변수를 등록하기 위해 개발 환경을 다시 시작해야 할 수 있습니다.

자세한 내용은 [포털에서 Azure 앱에 대한 ID 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

## <a name="get-an-access-token-for-authorization"></a>권한 부여를 위한 액세스 토큰 얻기

Azure ID 클라이언트 라이브러리는 Azure AD를 사용하여 요청에 권한을 부여하기 위한 액세스 토큰을 얻는 데 사용할 수 있는 클래스를 제공합니다. Azure Storage 클라이언트 라이브러리에는 토큰 자격 증명을 매개 변수로 사용하여 서비스 클라이언트 개체에 대한 생성자가 포함됩니다. 이 두 가지를 함께 사용하여 Azure AD 자격 증명으로 Azure Storage 요청에 쉽게 권한을 부여할 수 있습니다.

**DefaultAzureCredential** 클래스를 사용하는 것은 애플리케이션이 개발 환경과 Azure 모두에서 액세스 토큰을 얻어야 하는 대부분의 간단한 시나리오에 권장됩니다. 다른 시나리오에서도 다양한 유형의 토큰 자격 증명을 사용할 수 있습니다.

다음 예제에서는 DefaultAzureCredential을 사용하여 .NET에서 토큰을 얻는 방법을 보여줍니다. 그런 다음, 애플리케이션은 토큰을 사용하여 새 서비스 클라이언트를 만든 다음 Blob 컨테이너를 만드는 데 사용됩니다. 이 예제에서는 .NET 및 Blob Storage 서비스를 사용하지만 **DefaultAzureCredential** 클래스는 다른 언어 및 다른 Azure 서비스와 유사하게 동작합니다.

```csharp
static void CreateBlobContainer(string accountName, string containerName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a token credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                  new DefaultAzureCredential());

    // Create the container if it does not exist.
    containerClient.CreateIfNotExists();
}
```  

DefaultAzureCredential 클래스를 사용하여 관리 ID에 Azure Storage 액세스 권한을 부여하는 자세한 내용은 [.NET용 Azure ID 클라이언트 라이브러리를](/dotnet/api/overview/azure/identity-readme)참조하세요.

## <a name="see-also"></a>추가 정보

- [Azure AD의 앱 & 서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)
- [Microsoft ID 플랫폼 인증 라이브러리](../../active-directory/develop/reference-v2-libraries.md)