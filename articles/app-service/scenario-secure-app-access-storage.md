---
title: 자습서 - 관리 ID를 사용하여 웹앱에서 스토리지 액세스 | Azure
description: 이 자습서에서는 관리 ID를 사용하여 앱용 Azure Storage에 액세스하는 방법을 알아봅니다.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurecli, devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: be170a07340fdea84b9b4af03bd329fcdf91483d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065554"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>자습서: 웹앱에서 Azure Storage 액세스

관리 ID를 사용하여 Azure App Service에서 실행되는 웹앱(로그인한 사용자가 아님)용 Azure Storage에 액세스하는 방법을 알아봅니다.

:::image type="content" alt-text="스토리지에 액세스하는 방법을 보여주는 다이어그램." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

웹앱에서 Azure 데이터 평면(Azure Storage, Azure SQL Database, Azure Key Vault 또는 다른 서비스)에 대한 액세스 권한을 추가하려고 합니다. 공유 키를 사용할 수도 있지만, 그렇게 할 경우 비밀을 만들고 배포하고 관리할 수 있는 사람의 작업 보안을 걱정해야 합니다. 뿐만 아니라 키가 GitHub에 체크 인되어 해커가 키를 검색하는 방법을 알게 될 가능성이 있습니다. 웹앱에 데이터 액세스 권한을 부여하는 보다 안전한 방법은 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하는 것입니다.

Azure AD(Azure Active Directory)의 관리 ID를 사용하면 앱 자격 증명 없이 App Services가 RBAC(역할 기반 액세스 제어)를 통해 리소스에 액세스할 수 있습니다. 웹앱에 관리 ID를 할당한 후에는 인증서를 만들고 배포하는 작업을 Azure가 처리합니다. 사용자는 비밀 또는 앱 자격 증명 관리에 대해 신경 쓸 필요가 없습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * 웹앱에서 시스템이 할당한 관리 ID를 만듭니다.
> * 스토리지 계정 및 Azure Blob Storage 컨테이너를 만듭니다.
> * 관리 ID를 사용하여 웹앱에서 스토리지에 액세스합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [App Service 인증/권한 부여 모듈을 사용하도록 설정된](scenario-secure-app-authentication-app-service.md) Azure App Service에서 실행되는 웹 애플리케이션

## <a name="enable-managed-identity-on-an-app"></a>앱에서 관리 ID 사용

Visual Studio를 통해 웹앱을 만들고 게시하는 경우 앱에서 관리 ID를 사용하도록 자동으로 설정되었습니다. 앱 서비스의 왼쪽 창에서 **ID** 를 선택한 다음, **시스템 할당** 을 선택합니다. **상태** 가 **켜기** 로 설정되어 있는지 확인합니다. 설정되지 않았으면 **저장** 을 선택한 다음, **예** 를 선택하여 시스템이 할당한 관리 ID를 사용하도록 설정합니다. 관리 ID를 사용하도록 설정하면 상태가 **켜기** 로 설정되고 개체 ID를 사용할 수 있습니다.

:::image type="content" alt-text="시스템 할당 ID 옵션을 보여주는 스크린샷." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

이 단계에서는 **인증/권한 부여** 창에서 만든 앱 ID와 다른 새 개체 ID를 만듭니다. 시스템이 할당한 관리 ID의 개체 ID를 복사합니다. 나중에 필요합니다.

## <a name="create-a-storage-account-and-blob-storage-container"></a>스토리지 계정 및 Blob Storage 컨테이너 만들기

이제 스토리지 계정 및 Blob Storage 컨테이너를 만들 수 있습니다.

모든 스토리지 계정은 Azure 리소스 그룹에 속해야 합니다. 리소스 그룹은 Azure 리소스를 그룹화하기 위한 논리적 컨테이너입니다. 스토리지 계정을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있는 옵션이 있습니다. 이 문서에서는 새 리소스 그룹을 만드는 방법을 보여줍니다.

범용 v2 저장소 계정은 모든 Azure Storage 서비스(Blob, 파일, 큐, 테이블 및 디스크)에 대한 액세스를 제공합니다. 여기서 설명하는 단계에서는 범용 v2 스토리지 계정을 만들지만, 모든 유형의 스토리지 계정을 만드는 단계가 비슷합니다.

Azure Storage의 BLOB은 컨테이너로 구성됩니다. 이 자습서의 뒷부분에서 BLOB을 업로드하려면 먼저 컨테이너를 만들어야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 범용 v2 스토리지 계정을 만들려면 다음 단계를 수행합니다.

1. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다. 리소스 목록에 **스토리지 계정** 을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Storage 계정** 을 선택합니다.

1. 표시되는 **스토리지 계정** 창에서 **추가** 를 선택합니다.

1. 스토리지 계정을 만들 구독을 선택합니다.

1. **리소스 그룹** 필드 아래의 드롭다운 메뉴에서 웹앱이 포함된 리소스 그룹을 선택합니다.

1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.

1. 스토리지 계정의 위치를 선택하거나 기본 위치를 사용합니다.

1. 다음 필드는 기본값으로 유지합니다.

    |필드|값|
    |--|--|
    |배포 모델|리소스 관리자|
    |성능|표준|
    |계정 종류|StorageV2(범용 v2)|
    |복제|RA-GRS(읽기 액세스 지역 중복 스토리지)|
    |액세스 계층|핫|

1. **검토 + 만들기** 를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.

1. **만들기** 를 선택합니다.

Azure Storage에서 Blob Storage 컨테이너를 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 새 스토리지 계정으로 이동합니다.

1. 스토리지 계정의 왼쪽 메뉴에서 **Blob 서비스** 섹션이 나올 때까지 스크롤한 다음, **컨테이너** 를 선택합니다.

1. **+ 컨테이너** 단추를 선택합니다.

1. 새 컨테이너의 이름을 입력합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다.

1. 컨테이너에 대한 공용 액세스 수준을 설정합니다. 기본 수준은 **프라이빗(익명 액세스 없음)** 입니다.

1. **확인** 을 선택하여 컨테이너를 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

범용 v2 스토리지 계정 및 Blob Storage 컨테이너를 만들려면 다음 스크립트를 실행합니다. 웹앱을 포함하고 있는 리소스 그룹의 이름을 지정합니다. 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.

스토리지 계정의 위치를 지정합니다. 구독에 유효한 위치 목록을 보려면 ```Get-AzLocation | select Location```을 실행합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

범용 v2 스토리지 계정 및 Blob Storage 컨테이너를 만들려면 다음 스크립트를 실행합니다. 웹앱을 포함하고 있는 리소스 그룹의 이름을 지정합니다. 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다. 

스토리지 계정의 위치를 지정합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다.

다음 예제에서는 Azure AD 계정을 사용하여 컨테이너를 만드는 작업에 권한을 부여합니다. 컨테이너를 만들기 전에 Storage Blob 데이터 참가자 역할을 자신에게 할당합니다. 계정 소유자인 경우에도 스토리지 계정에 대한 데이터 작업을 수행하려면 명시적 권한이 필요합니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>스토리지 계정에 대한 액세스 권한 부여

Blob을 만들거나 읽거나 삭제하려면 먼저 스토리지 계정에 대한 액세스 권한을 웹앱에 부여해야 합니다. 이전 단계에서 관리 ID를 사용하여 App Service에서 실행되는 웹앱을 구성했습니다. Azure RBAC를 사용하면 보안 주체와 마찬가지로 관리 ID에 다른 리소스 액세스 권한을 제공할 수 있습니다. Storage Blob 데이터 기여자 역할은 Blob 컨테이너 및 데이터에 대한 읽기, 쓰기 및 삭제 권한을 웹앱(시스템이 할당한 관리 ID로 표시됨)에 제공합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동하여 웹앱에 액세스 권한을 부여합니다. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택한 다음, **역할 할당** 을 선택합니다. 스토리지 계정에 액세스할 수 있는 사용자 목록이 표시됩니다. 이제 스토리지 계정에 액세스해야 하는 앱 서비스인 로봇에 역할 할당을 추가해야 합니다. **추가** > **역할 할당 추가** 를 선택하여 **역할 할당 추가** 페이지를 엽니다.

구독 범위에서 **App Service** 에 **Storage Blob 데이터 기여자** 역할을 할당합니다.  세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

이제 웹앱이 스토리지 계정에 액세스할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 스크립트를 실행하여 스토리지 계정에 대한 Storage Blob 데이터 기여자 역할을 웹앱(시스템이 할당한 관리 ID로 표시됨)에 할당합니다.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 스크립트를 실행하여 스토리지 계정에 대한 Storage Blob 데이터 기여자 역할을 웹앱(시스템이 할당한 관리 ID로 표시됨)에 할당합니다.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Blob Storage에 액세스(.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스는 Azure Storage에 대한 요청에 권한을 부여하기 위해 코드의 토큰 자격 증명을 가져오는 데 사용됩니다. 관리 ID를 사용하여 토큰을 가져오고 서비스 클라이언트에 연결하는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. 다음 코드 예제는 인증된 토큰 자격 증명을 가져와서 서비스 클라이언트 개체를 만드는 데 사용합니다. 이 개체는 새 BLOB을 업로드합니다.

이 코드를 샘플 애플리케이션의 일부로 보려면 [GitHub의 샘플](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity)을 참조하세요.

### <a name="install-client-library-packages"></a>클라이언트 라이브러리 패키지 설치

Blob Storage와 함께 작동하도록 [Blob Storage NuGet 패키지](https://www.nuget.org/packages/Azure.Storage.Blobs/)를 설치하고, Azure AD 자격 증명으로 인증하도록 [.NET NuGet 패키지용 Azure ID 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Identity/)를 설치합니다. .NET Core 명령줄 인터페이스 또는 Visual Studio의 패키지 관리자 콘솔을 사용하여 클라이언트 라이브러리를 설치합니다.

# <a name="command-line"></a>[명령줄](#tab/command-line)

명령줄을 열고 프로젝트 파일이 포함된 디렉터리로 전환합니다.

설치 명령을 실행합니다.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[패키지 관리자](#tab/package-manager)

Visual Studio에서 프로젝트 또는 솔루션을 열고 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 명령을 사용하여 콘솔을 엽니다.

설치 명령을 실행합니다.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>예

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
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
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료하고 웹앱 또는 관련 리소스가 더 이상 필요하지 않으면 [만든 리소스를 정리](scenario-secure-app-clean-up-resources.md)합니다.

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
>
> * 시스템이 할당한 관리 ID를 만듭니다.
> * 스토리지 계정 및 Blob Storage 컨테이너를 만듭니다.
> * 관리 ID를 사용하여 웹앱에서 스토리지에 액세스합니다.

> [!div class="nextstepaction"]
> [자습서: Virtual Network 통합을 통해 백 엔드 통신 격리](tutorial-networking-isolate-vnet.md)

> [!div class="nextstepaction"]
> [사용자를 대신하여 App Service에서 Microsoft Graph에 액세스](scenario-secure-app-access-microsoft-graph-as-user.md)

> [!div class="nextstepaction"]
> [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
