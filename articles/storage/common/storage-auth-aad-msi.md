---
title: 관리 ID를 사용하여 데이터에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure 리소스에 대한 관리 ID를 사용하여 Azure VM, 함수 앱 등에서 실행되는 애플리케이션에서 Blob 및 큐 데이터에 대한 액세스 권한을 부여합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: a41cd57e64d1c3b9e6a88924805b1f2a6e77361a
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726899"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 사용하여 Blob 및 큐 데이터에 대한 액세스 권한 부여

Azure Blob 및 Queue Storage는 [Azure 리소스에 대한 관리 ID를 사용](../../active-directory/managed-identities-azure-resources/overview.md)하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스의 관리 ID를 사용하여 Azure VM(가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 중인 애플리케이션의 Azure AD 자격 증명을 사용하여 Blob 및 큐 데이터에 대한 액세스 권한을 부여할 수 있습니다. Azure AD 인증과 함께 Azure 리소스의 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다.  

이 문서에서는 Azure 리소스에 대한 관리 ID를 사용하여 Azure VM에서 Blob 또는 큐 데이터에 대한 액세스 권한을 부여하는 방법을 보여 줍니다. 개발 환경에서 코드를 테스트하는 방법에 대해서도 설명합니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure 리소스에 대한 관리 ID를 사용하여 VM에서 Blob 및 큐에 대한 액세스 권한을 부여하려면 먼저 VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

관리 ID에 대한 자세한 내용은 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

## <a name="authenticate-with-the-azure-identity-library"></a>Azure ID 라이브러리로 인증

Azure ID 클라이언트 라이브러리는 [Azure SDK](https://github.com/Azure/azure-sdk)에 대한 Azure AD 토큰 인증 지원을 제공합니다. .NET, Java, Python 및 JavaScript용 최신 버전의 Azure Storage 클라이언트 라이브러리는 Azure ID 라이브러리와 통합되어 Azure Storage 요청 인증을 위한 OAuth 2.0 토큰을 획득하는 간단하고 안전한 수단을 제공합니다.

Azure ID 클라이언트 라이브러리의 장점은 동일한 코드를 사용하여 애플리케이션이 개발 환경에서 실행 중인지 아니면 Azure에서 실행 중인지 인증할 수 있다는 것입니다. .NET용 Azure ID 클라이언트 라이브러리는 보안 주체를 인증합니다. 코드가 Azure에서 실행되는 경우 보안 주체는 Azure 리소스에 대한 관리 ID입니다. 개발 환경에서 관리 ID가 존재하지 않으므로 클라이언트 라이브러리는 테스트를 위해 사용자 또는 서비스 주체를 인증합니다.

인증 후에 Azure ID 클라이언트 라이브러리는 토큰 자격 증명을 가져옵니다. 그런 다음 이 토큰 자격 증명은 Azure Storage에 대해 작업을 수행하기 위해 만드는 서비스 클라이언트 개체에 캡슐화됩니다. 라이브러리는 적절한 토큰 자격 증명을 가져와 이를 원활하게 처리합니다.

.NET용 Azure ID 클라이언트 라이브러리에 대한 자세한 내용은 [.NET용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)를 참조하세요. Azure ID 클라이언트 라이브러리에 대한 참조 설명서는 [Azure ID 네임스페이스](/dotnet/api/azure.identity)를 참조하세요.

### <a name="assign-azure-roles-for-access-to-data"></a>데이터 액세스를 위한 Azure 역할 할당

Azure AD 보안 주체가 Blob 또는 큐 데이터에 액세스하려는 경우 해당 보안 주체에게 리소스에 대한 권한이 있어야 합니다. 보안 주체가 Azure의 관리 ID 또는 개발 환경에서 코드를 실행하는 Azure AD 사용자 계정이면 보안 주체는 Azure Storage의 Blob 또는 큐 데이터에 대한 액세스 권한을 부여하는 Azure 역할을 할당받아야 합니다. Azure RBAC를 통해 권한을 할당하는 방법에 관한 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)을 참조하세요.

> [!NOTE]
> Azure Storage 계정을 만들면 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. Azure Storage에 Azure 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.
>
> Azure Portal에서 데이터 액세스를 위한 계정 키를 사용할 수도 있으므로 사용자가 데이터 액세스 역할을 자신에게 할당하기 전에 Azure Portal을 통해 스토리지 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택](../blobs/authorize-data-operations-portal.md)을 참조하세요.

### <a name="authenticate-the-user-in-the-development-environment"></a>개발 환경에서 사용자 인증

코드가 개발 환경에서 실행 중이면 사용 중인 도구에 따라 인증이 자동으로 처리되거나 브라우저 로그인이 필요할 수 있습니다. 예를 들어 Microsoft Visual Studio는 SSO(Single Sign-On)를 지원하므로 활성 Azure AD 사용자 계정이 인증에 자동으로 사용됩니다. SSO에 대한 자세한 내용은 [애플리케이션에 Single Sign-On](../../active-directory/manage-apps/what-is-single-sign-on.md)을 참조하세요.

다른 개발 도구에서는 웹 브라우저를 통해 로그인하라는 메시지가 표시될 수 있습니다.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>개발 환경에서 서비스 주체 인증

개발 환경이 Single Sign-On 또는 웹 브라우저를 통한 로그인을 지원하지 않는 경우 서비스 주체를 사용하여 개발 환경에서 인증할 수 있습니다.

#### <a name="create-the-service-principal"></a>서비스 주체 만들기

Azure CLI로 서비스 주체를 만들고 Azure 역할을 할당하려면 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 호출합니다. 새 서비스 주체에 할당하는 Azure Storage 데이터 액세스 역할을 제공합니다. 또한 역할 할당의 범위를 제공합니다. Azure Storage에 대해 제공되는 기본 제공 역할에 대한 자세한 내용은 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

서비스 주체에 역할을 할당할 수 있는 권한이 없는 경우 계정 소유자 또는 관리자에게 역할 할당을 수행하도록 요청해야 할 수 있습니다.

다음 예에서는 Azure CLI를 사용하여 새 서비스 주체를 만들고 계정 범위와 함께 **스토리지 Blob 데이터 기여자** 역할을 할당합니다.

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

> [!IMPORTANT]
> Azure 역할 할당을 전파하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="set-environment-variables"></a>환경 변수 설정

Azure ID 클라이언트 라이브러리는 런타임에 세 가지 환경 변수에서 값을 읽어서 서비스 주체를 인증합니다. 다음 표에서는 각 환경 변수에 대해 설정할 값을 설명합니다.

|환경 변수|값
|-|-
|`AZURE_CLIENT_ID`|서비스 주체의 앱 ID
|`AZURE_TENANT_ID`|서비스 주체의 Azure AD 테넌트 ID
|`AZURE_CLIENT_SECRET`|서비스 사용자에 대해 생성된 암호

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫았다가 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용하는 경우 새 환경 변수를 등록하기 위해 개발 환경을 다시 시작해야 할 수 있습니다.

자세한 내용은 [포털에서 Azure 앱에 대한 ID 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

Azure ID 및 Azure Storage 클라이언트 라이브러리를 사용하려면 코드에 다음 `using` 지시문을 추가합니다.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

코드에서 Azure Storage 요청에 권한을 부여하는 데 사용할 수 있는 토큰 자격 증명을 가져오려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. 다음 코드 예에서는 인증된 토큰 자격 증명을 가져와서 이를 사용하여 서비스 클라이언트 개체를 만든 다음 서비스 클라이언트를 사용하여 새 Blob을 업로드하는 방법을 보여 줍니다.

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Azure AD를 사용하여 Blob 또는 큐 데이터에 대한 요청에 권한을 부여하려면 해당 요청에 대해 HTTPS를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)
- [스토리지 애플리케이션과 함께 Azure AD를 사용](storage-auth-aad-app.md)
- [Azure AD 자격 증명을 통해 PowerShell 명령을 실행하여 Blob 데이터에 액세스하기](../blobs/authorize-data-operations-powershell.md)
- [자습서: 관리 ID를 사용하여 App Service의 스토리지에 액세스](../../app-service/scenario-secure-app-access-storage.md)
