---
title: .NET을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리
description: .NET을 이용해 HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정에서 ACL(액세스 제어 목록)을 관리합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: c3f0d0dd82201a3a339f05929ffefa988e1c8692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564945"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>.NET을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리

이 문서에서는 .NET을 사용하여 디렉터리와 파일의 액세스 제어 목록을 가져오고, 설정하고, 업데이트하는 방법을 보여 줍니다.

부모 디렉터리 아래에 만들어진 새 자식 항목에서는 이미 ACL 상속을 사용할 수 있는 상태입니다. 그러나 각 자식 항목을 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 ACL을 재귀적으로 추가, 업데이트, 제거할 수도 있습니다.

[패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [재귀 ACL 샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0) | [API 참조](/dotnet/api/azure.storage.files.datalake) | [Gen1에서 Gen2로 매핑](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>필수 조건

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

- 다음 보안 권한 중 하나입니다.

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [스토리지 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당된 프로비저닝된 Azure AD(Active Directory) [보안 주체](../../role-based-access-control/overview.md#security-principal).

  - ACL 설정을 적용하려는 대상 컨테이너 또는 디렉터리를 소유한 사용자. ACL을 재귀적으로 설정하기 위해 대상 컨테이너 또는 디렉터리의 모든 자식 항목을 포함합니다.

  - 스토리지 계정 키.

## <a name="set-up-your-project"></a>프로젝트 설정

시작하려면 [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 패키지를 설치합니다.

1. 명령 창을 엽니다(예: Windows PowerShell).

2. 프로젝트 디렉터리에서 `dotnet add package` 명령을 사용하여 Azure.Storage.Files.DataLake 미리 보기 패키지를 설치합니다.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   코드 파일 맨 위에 다음 using 문을 추가합니다.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용하려면 스토리지 계정을 나타내는 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들어야 합니다.

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure AD(Active Directory)를 사용하여 연결

> [!NOTE]
> Azure AD(Azure Active Directory)를 사용하여 액세스에 권한을 부여하는 경우 보안 주체가 [스토리지 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당되었는지 확인합니다. ACL 권한이 적용되는 방식과 권한 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)을 참조하세요.

[.NET용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)를 사용하여 Azure AD로 애플리케이션을 인증할 수 있습니다.

패키지를 설치한 후에는 이 using 문을 코드 파일의 맨 위에 추가합니다.

```csharp
using Azure.Identity;
```

클라이언트 ID, 클라이언트 암호, 테넌트 ID를 가져옵니다. 이 과정을 수행하려면 [클라이언트 애플리케이션의 요청에 대한 권한 부여를 위해 Azure AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조하세요. 해당 프로세스의 일부로 다음 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어) 역할 중 하나를 보안 주체에 할당해야 합니다.

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유하는 디렉터리와 파일만 해당.|

이 예시에서는 클라이언트 ID, 클라이언트 암호, 테넌트 ID를 사용하여 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만듭니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 더 많은 예제는 [.NET용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 설명서를 참조하세요.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용한 연결

계정에 연결하는 가장 쉬운 방법입니다.

이 예시에서는 계정 키를 사용하여 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만듭니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>ACL 설정

ACL을 *설정* 하는 경우 모든 항목을 포함하여 전체 ACL을 **바꿉니다**. 보안 주체의 권한 수준을 변경하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 바꾸는 대신 업데이트하려면 이 문서의 [ACL 업데이트](#update-acls) 섹션을 참조하세요.

ACL을 *설정* 하도록 선택한 경우 소유 사용자에 대한 항목, 소유 그룹에 대한 항목, 다른 모든 사용자에 대한 항목을 추가해야 합니다. 소유 사용자, 소유 그룹, 다른 모든 사용자에 대한 자세한 내용은 [사용자 및 ID](data-lake-storage-access-control.md#users-and-identities)를 참조하세요.

이 섹션에서는 다음을 사용하는 방법을 보여 줍니다.

- 디렉터리의 ACL 설정
- 파일의 ACL 설정
- 반복적으로 ACL 설정

### <a name="set-the-acl-of-a-directory"></a>디렉터리의 ACL 설정

[DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 메서드를 호출하여 디렉터리의 ACL(액세스 제어 목록)을 가져오고, [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 메서드를 호출하여 ACL을 설정합니다.

이 예시에서는 `my-directory`라는 디렉터리의 ACL을 가져오고 설정합니다. `user::rwx,group::r-x,other::rw-` 문자열은 소유 사용자에게 읽기, 쓰기, 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 대상에는 읽기 및 쓰기 권한을 부여합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

컨테이너의 루트 디렉터리의 ACL을 가져오고 설정할 수도 있습니다. 루트 디렉터리를 가져오려면 [DataLakeFileSystemClient.GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) 메서드에 빈 문자열(`""`)을 전달합니다.

### <a name="set-the-acl-of-a-file"></a>파일의 ACL 설정

[DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) 메서드를 호출하여 파일의 ACL(액세스 제어 목록)을 가져오고, [DataLakeFileClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) 메서드를 호출하여 ACL을 설정합니다.

이 예시에서는 `my-file.txt`라는 파일의 ACL을 가져오고 설정합니다. `user::rwx,group::r-x,other::rw-` 문자열은 소유 사용자에게 읽기, 쓰기, 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 대상에는 읽기 및 쓰기 권한을 부여합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>반복적으로 ACL 설정

**DataLakeDirectoryClient.SetAccessControlRecursiveAsync** 메서드를 호출하여 ACL을 재귀적으로 설정합니다. 이 메서드를 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [목록](/dotnet/api/system.collections.generic.list-1)으로 전달합니다. 각 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)은 ACL 항목을 정의합니다.

**기본** ACL 항목을 설정하려는 경우 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 속성을 **true** 로 설정할 수 있습니다.

다음 예시에서는 `my-parent-directory`라는 디렉터리의 ACL을 설정합니다. 이 메서드는 기본 ACL을 설정할지 여부를 지정하는 `isDefaultScope`라는 부울 매개 변수를 허용합니다. 이 매개 변수는 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 생성자에서 사용됩니다. ACL의 항목은 소유 사용자에 읽기, 쓰기, 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 사용자에게는 액세스 권한을 부여하지 않습니다. 이 예시의 마지막 ACL 항목은 개체 ID가 ““xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”인 특정 사용자에게 읽기 및 실행 권한을 제공합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 설정하는 예시를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조하세요.

## <a name="update-acls"></a>ACL 업데이트

ACL을 *업데이트* 할 때 ACL을 바꾸는 대신 ACL을 수정합니다. 예를 들어 ACL에 나열된 다른 보안 주체에 영향을 주지 않고 ACL에 새 보안 주체를 추가할 수 있습니다.  ACL을 업데이트하지 않고 바꾸려면 이 문서의 [ACL 설정](#set-acls) 섹션을 참조하세요.

이 섹션에서는 다음을 사용하는 방법을 보여 줍니다.

- ACL 업데이트
- ACL의 재귀적 업데이트

### <a name="update-an-acl"></a>ACL 업데이트

먼저 [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 메서드를 호출하여 디렉터리의 ACL을 가져옵니다. ACL 항목 목록을 [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) 개체의 새 [목록](/dotnet/api/system.collections.generic.list-1)에 복사합니다. 그런 다음 업데이트하려는 항목을 찾아 목록에서 바꿉니다. [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 메서드를 호출하여 ACL을 설정합니다.

이 예시에서는 다른 모든 사용자의 ACL 항목을 바꿔서 컨테이너의 루트 ACL을 업데이트합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>ACL의 재귀적 업데이트

ACL을 재귀적으로 업데이트하려면 업데이트할 ACL 항목을 사용하여 새 ACL 개체를 만든 다음 ACL 업데이트 작업에서 해당 개체를 사용합니다. 기존 ACL을 가져오지 않고 업데이트할 ACL 항목만 제공하면 됩니다.

**DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync** 메서드를 호출하여 ACL을 재귀적으로 업데이트합니다.  이 메서드를 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [목록](/dotnet/api/system.collections.generic.list-1)으로 전달합니다. 각 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)은 ACL 항목을 정의합니다.

**기본** ACL 항목을 업데이트하려는 경우 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 속성을 **true** 로 설정할 수 있습니다.

이 예시에서는 쓰기 권한이 있는 ACL 항목을 업데이트합니다. 이 메서드는 기본 ACL의 업데이트 여부를 지정하는 `isDefaultScope`라는 부울 매개 변수를 허용합니다. 이 매개 변수는 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 생성자에서 사용됩니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 업데이트하는 예제를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조하세요.

## <a name="remove-acl-entries"></a>ACL 항목 제거

하나 이상의 ACL 항목을 제거할 수 있습니다. 이 섹션에서는 다음을 사용하는 방법을 보여 줍니다.

- ACL 항목 제거
- ACL 항목의 재귀적 제거

### <a name="remove-an-acl-entry"></a>ACL 항목 제거

먼저 [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 메서드를 호출하여 디렉터리의 ACL을 가져옵니다. ACL 항목 목록을 [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) 개체의 새 [목록](/dotnet/api/system.collections.generic.list-1)에 복사합니다. 그런 다음 제거하려는 항목을 찾아 컬렉션의 [Remove](/dotnet/api/system.collections.ilist.remove) 메서드를 호출합니다. [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 메서드를 호출하여 업데이트된 ACL을 설정합니다.

이 예시에서는 다른 모든 사용자의 ACL 항목을 바꿔서 컨테이너의 루트 ACL을 업데이트합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL 항목의 재귀적 제거

ACL 항목을 재귀적으로 제거하려면 제거할 ACL 항목에 대한 새 ACL 개체를 만든 다음 ACL 제거 작업에서 해당 개체를 사용합니다. 기존 ACL을 가져오지 않고 제거할 ACL 항목만 제공합니다.

**DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync** 메서드를 호출하여 ACL 항목을 제거합니다. 이 메서드를 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [목록](/dotnet/api/system.collections.generic.list-1)으로 전달합니다. 각 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)은 ACL 항목을 정의합니다.

**기본** ACL 항목을 제거하려는 경우 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 속성을 **true** 로 설정할 수 있습니다.

이 예시에서는 `my-parent-directory`라는 디렉터리의 ACL에서 ACL 항목을 제거합니다. 이 메서드는 기본 ACL에서 항목을 제거할지 여부를 지정하는 `isDefaultScope`라는 부울 매개 변수를 허용합니다. 이 매개 변수는 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 생성자에서 사용됩니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 제거하는 예시를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조하세요.

## <a name="recover-from-failures"></a>오류에서 복구

ACL을 재귀적으로 수정할 때 런타임 또는 권한 오류가 발생할 수 있습니다. 런타임 오류의 경우 처음부터 프로세스를 다시 시작합니다. 수정되는 디렉터리 계층 구조에 있는 디렉터리 또는 파일의 ACL을 수정할 수 있는 권한이 보안 주체에게 없는 경우에 권한 오류가 발생할 수 있습니다. 권한 문제를 해결한 다음 연속 토큰을 사용하여 오류 지점에서 프로세스를 다시 시작하거나 프로세스를 처음부터 다시 시작하도록 선택합니다. 처음부터 다시 시작하기를 선호하는 경우 연속 토큰을 사용할 필요는 없습니다. 부정적인 영향 없이 ACL 항목을 다시 적용할 수 있습니다.

이 예시에서는 오류가 발생한 경우 연속 토큰을 반환합니다. 애플리케이션은 오류가 해결된 후에 이 예시 메서드를 다시 호출하고 연속 토큰을 전달할 수 있습니다. 이 예시 메서드를 처음 호출하는 경우 애플리케이션은 연속 토큰 매개 변수에 대한 `null` 값을 전달할 수 있습니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 설정하는 예시를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조하세요.

권한 오류가 발생해도 프로세스가 중단 없이 완료되도록 하기 위해 이를 지정할 수 있습니다.

프로세스가 중단 없이 완료되도록 하려면 **AccessControlChangedOptions** 개체를 전달하고 해당 개체의 **ContinueOnFailure** 속성을 ``true``로 설정합니다.

이 예시에서는 ACL 항목을 재귀적으로 설정합니다. 이 코드에 사용 권한 오류가 발생하면 해당 오류를 기록하고 계속해서 실행합니다. 이 예에서는 실패 횟수를 콘솔에 출력합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 설정하는 예시를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>참고 항목

- [API 참조 설명서](/dotnet/api/azure.storage.files.datalake)
- [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [피드백 제공](https://github.com/Azure/azure-sdk-for-net/issues)
- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2의 ACL](data-lake-storage-access-control.md)(액세스 제어 목록)
