---
title: Azure CLI를 사용한 Azure Data Lake Storage Gen2의 ACL 관리
description: Azure CLI를 사용하여 계층 구조 네임스페이스가 있는 스토리지 계정에서 ACL(액세스 제어 목록)을 관리합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 84410ee16080c338e40c026326ef2a2945ba0e3c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555804"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure CLI를 사용한 Azure Data Lake Storage Gen2의 ACL 관리

이 문서에서는 [Azure CLI(명령줄 인터페이스)](/cli/azure/)를 사용하여 디렉터리 및 파일의 액세스 제어 목록을 가져오고, 설정하고, 업데이트하는 방법을 보여 줍니다.

부모 디렉터리 아래에 만들어진 새 자식 항목에서는 이미 ACL 상속을 사용할 수 있는 상태입니다. 그러나 각 자식 항목을 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 ACL을 재귀적으로 추가, 업데이트, 제거할 수도 있습니다.

[참조](/cli/azure/storage/fs/access) | [샘플](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [피드백 제공](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 자세한 내용은 [Azure 평가판 받기를 참조하세요.](https://azure.microsoft.com/pricing/free-trial/)

- 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.14.0` 이상.

- 다음 보안 권한 중 하나입니다.

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당된 프로비전된 Azure AD(Azure Active Directory) [보안 주체](../../role-based-access-control/overview.md#security-principal).

  - ACL 설정을 적용하려는 대상 컨테이너 또는 디렉터리를 소유한 사용자. ACL을 재귀적으로 설정하기 위해 대상 컨테이너 또는 디렉터리의 모든 자식 항목을 포함합니다.

  - 스토리지 계정 키.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>올바른 버전의 Azure CLI를 설치했는지 확인

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. 다음 명령을 사용하여 설치된 Azure CLI 버전이 `2.14.0` 이상인지 확인합니다.

   ```azurecli
    az --version
   ```

   Azure CLI 버전이 `2.14.0`보다 낮은 경우 이후 버전을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="connect-to-the-account"></a>계정에 연결

1. Azure CLI를 로컬로 사용하는 경우 로그인 명령을 실행합니다.

   ```azurecli
   az login
   ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인합니다.

   다른 인증 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여](./authorize-data-operations-cli.md)를 참조하세요.

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

> [!NOTE]
> 이 문서에 제공된 예제는 Azure AD(Active Directory) 권한 부여를 보여 줍니다. 권한 부여 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여I](./authorize-data-operations-cli.md)를 참조하세요.

## <a name="get-acls"></a>ACL 가져오기

[az storage fs access show](/cli/azure/storage/fs#az_storage_fs_show) 명령을 사용하여 **디렉터리** 의 ACL을 가져옵니다.

다음 예제에서는 디렉터리의 ACL을 가져온 다음 해당 ACL을 콘솔에 출력합니다.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

[az storage fs access show](/cli/azure/storage/fs#az_storage_fs_show) 명령을 사용하여 **파일** 의 액세스 권한을 가져옵니다.

다음 예제에서는 파일의 ACL을 가져온 다음 해당 ACL을 콘솔에 출력합니다.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

다음 이미지는 디렉터리의 ACL을 가져온 후 출력을 보여 줍니다.

![ACL 가져오기 출력](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

이 예제에서 소유 사용자에게 읽기, 쓰기 및 실행 권한이 있습니다. 소유 그룹에는 읽기 및 실행 권한만 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)를 참조하세요.

## <a name="set-acls"></a>ACL 설정

ACL을 *설정* 하는 경우 모든 항목을 포함하여 전체 ACL을 **바꿉니다**. 보안 주체의 권한 수준을 변경하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 바꾸는 대신 업데이트하려면 이 문서의 [ACL 업데이트](#update-acls) 섹션을 참조하세요.

ACL을 *설정* 하도록 선택한 경우 소유 사용자에 대한 항목, 소유 그룹에 대한 항목, 다른 모든 사용자에 대한 항목을 추가해야 합니다. 소유 사용자, 소유 그룹, 다른 모든 사용자에 대한 자세한 내용은 [사용자 및 ID](data-lake-storage-access-control.md#users-and-identities)를 참조하세요.

이 섹션에서는 다음을 사용하는 방법을 보여 줍니다.

- ACL 설정
- 반복적으로 ACL 설정

### <a name="set-an-acl"></a>ACL 설정

[az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set) 명령을 사용하여 **디렉터리** 의 ACL을 설정합니다.

다음 예제에서는 디렉터리의 소유 사용자, 소유 그룹 또는 기타 사용자에 대한 ACL을 설정하고 콘솔에 해당 ACL을 출력합니다.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

이 예재에서는 디렉터리의 소유 사용자, 소유 그룹 또는 기타 사용자에 대한 기본 ACL을 설정하고 콘솔에 해당 ACL을 출력합니다.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

[az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set) 명령을 사용하여 **파일** 의 acl을 설정합니다.

다음 예제에서는 파일의 소유 사용자, 소유 그룹 또는 기타 사용자에 대한 ACL을 설정하고 콘솔에 해당 ACL을 출력합니다.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 특정 그룹 또는 사용자의 ACL을 보려면 해당 개체 ID를 사용합니다. 예를 들어 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 또는 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다.

다음 이미지는 파일의 ACL을 설정한 후 출력을 보여 줍니다.

![ACL 가져오기 출력 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

다음 예제에서는 소유 사용자 및 소유 그룹에 읽기 및 쓰기 권한만 있습니다. 다른 모든 사용자에게는 쓰기 및 실행 권한이 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)를 참조하세요.

### <a name="set-acls-recursively"></a>반복적으로 ACL 설정

[az storage fs access set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) 명령을 사용하여 ACL을 재귀적으로 설정합니다.

다음 예시에서는 `my-parent-directory`라는 디렉터리의 ACL을 설정합니다. 이러한 항목은 소유 사용자에게 읽기, 쓰기 및 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 부여하고, 다른 모든 사용자에게는 액세스 권한을 부여하지 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID가 “xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”인 특정 사용자에 읽기와 실행 권한을 부여합니다.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **기본** ACL 항목을 설정하려면 각 항목에 `default:` 접두사를 추가합니다. 예를 들어 `default:user::rwx` 또는 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`입니다.

## <a name="update-acls"></a>ACL 업데이트

ACL을 *업데이트* 할 때 ACL을 바꾸는 대신 ACL을 수정합니다. 예를 들어 ACL에 나열된 다른 보안 주체에 영향을 주지 않고 ACL에 새 보안 주체를 추가할 수 있습니다. ACL을 업데이트하지 않고 바꾸려면 이 문서의 [ACL 설정](#set-acls) 섹션을 참조하세요.

ACL을 업데이트하려면 업데이트하려는 ACL 항목으로 새 ACL 객체를 만든 다음, ACL 업데이트 작업에 이 개체를 사용합니다. 기존 ACL을 가져오지 않고 업데이트할 ACL 항목만 제공하면 됩니다.

이 섹션에서는 다음을 사용하는 방법을 보여 줍니다.

- ACL 업데이트
- ACL의 재귀적 업데이트

### <a name="update-an-acl"></a>ACL 업데이트

이 권한을 설정하는 또 다른 방법은 [az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set) 명령을 사용하는 것입니다.

`-permissions` 매개 변수를 약식 형식의 ACL로 설정하여 디렉터리 또는 파일의 ACL을 업데이트합니다.

다음 예제에서는 **디렉터리** 의 ACL을 업데이트합니다.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

다음 예제에서는 **파일** 의 ACL을 업데이트합니다.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 특정 그룹 또는 사용자의 ACL을 업데이트하려면 해당 개체 ID를 사용합니다. 예를 들어 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 또는 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다.

`--owner` 또는 `group` 매개 변수를 사용자의 엔터티 ID 또는 UPN(사용자 계정 이름)으로 설정하여 디렉터리 또는 파일의 소유 사용자 및 그룹을 업데이트할 수도 있습니다.

다음 예제에서는 디렉터리의 소유자를 변경합니다.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

다음 예제에서는 파일의 소유자를 변경합니다.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>ACL의 재귀적 업데이트

[az storage fs access update-recursive 명령을 사용하여 ACL을 재귀적으로 업데이트합니다.](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive)

이 예시에서는 쓰기 권한이 있는 ACL 항목을 업데이트합니다.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **기본** ACL 항목을 업데이트하려면 각 항목에 `default:` 접두사를 추가합니다. 예들 들어 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`입니다.

## <a name="remove-acl-entries-recursively"></a>ACL 항목의 재귀적 제거

하나 이상의 ACL 항목을 재귀적으로 제거할 수 있습니다. ACL 항목을 제거하려면 제거할 ACL 항목에 대해 새 ACL 개체를 만든 다음, ACL 제거 작업에 이 개체를 사용합니다. 기존 ACL을 가져오지 않고 제거할 ACL 항목만 제공합니다.

[az storage fs access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) 명령을 사용하여 ACL 항목을 제거합니다.

이 예제에서는 컨테이너의 루트 디렉터리에서 ACL 항목을 제거합니다.

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **기본** ACL 항목을 제거하려면 각 항목에 `default:` 접두사를 추가합니다. 예들 들어 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다.

## <a name="recover-from-failures"></a>오류에서 복구

ACL을 재귀적으로 수정할 때 런타임 또는 권한 오류가 발생할 수 있습니다. 런타임 오류의 경우 처음부터 프로세스를 다시 시작합니다. 수정되는 디렉터리 계층 구조에 있는 디렉터리 또는 파일의 ACL을 수정할 수 있는 권한이 보안 주체에게 없는 경우에 권한 오류가 발생할 수 있습니다. 권한 문제를 해결한 다음 연속 토큰을 사용하여 오류 지점에서 프로세스를 다시 시작하거나 프로세스를 처음부터 다시 시작하도록 선택합니다. 처음부터 다시 시작하기를 선호하는 경우 연속 토큰을 사용할 필요는 없습니다. 부정적인 영향 없이 ACL 항목을 다시 적용할 수 있습니다.

오류가 발생하는 경우 `--continue-on-failure` 매개 변수를 `false`로 설정하여 연속 토큰을 반환할 수 있습니다. 오류를 해결한 후 명령을 다시 실행한 다음, `--continuation` 매개 변수를 연속 토큰으로 설정하여 오류 발생 지점에서 프로세스를 다시 시작할 수 있습니다.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

권한 오류가 발생해도 프로세스가 중단 없이 완료되도록 하기 위해 이를 지정할 수 있습니다.

프로세스가 중단 없이 완료되게 하려면 `--continue-on-failure` 매개 변수를 `true`로 설정합니다.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>참고 항목

- [샘플](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [사용자 의견 제공](https://github.com/Azure/azure-cli-extensions/issues)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2의 ACL](data-lake-storage-access-control.md)(액세스 제어 목록)
