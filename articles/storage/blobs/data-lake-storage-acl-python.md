---
title: Python을 사용한 Azure Data Lake Storage Gen2의 ACL 관리
description: Python을 사용하여 HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정에서 ACL(액세스 제어 목록)을 관리합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 25ee9ba5983bdcd59962d99384fbb7232860b825
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555492"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Python을 사용한 Azure Data Lake Storage Gen2의 ACL 관리

이 문서에서는 Python을 사용해 디렉터리 및 파일의 액세스 제어 목록을 가져오고, 설정하고, 업데이트하는 방법을 보여 줍니다.

부모 디렉터리 아래에 만들어진 새 자식 항목에서는 이미 ACL 상속을 사용할 수 있는 상태입니다. 그러나 각 자식 항목을 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 ACL을 재귀적으로 추가, 업데이트, 제거할 수도 있습니다.

[패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [재귀 ACL 샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) | [API 참조](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Gen1에서 Gen2로 매핑](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 자세한 내용은 [Azure 무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/)를 참조 하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

- 다음 보안 권한 중 하나입니다.

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [스토리지 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당된 프로비저닝된 Azure AD(Active Directory) [보안 주체](../../role-based-access-control/overview.md#security-principal).

  - ACL 설정을 적용하려는 대상 컨테이너 또는 디렉터리를 소유한 사용자. ACL을 재귀적으로 설정하기 위해 대상 컨테이너 또는 디렉터리의 모든 자식 항목을 포함합니다.

  - 스토리지 계정 키.

## <a name="set-up-your-project"></a>프로젝트 설정

[pip](https://pypi.org/project/pip/)를 사용하여 Python용 Azure Data Lake Storage 클라이언트 라이브러리를 설치합니다.

```
pip install azure-storage-file-datalake
```

코드 파일 맨 위에 다음 import 문을 추가합니다.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용하려면 스토리지 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다.

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure AD(Active Directory)를 사용하여 연결

> [!NOTE]
> Azure AD(Azure Active Directory)를 사용하여 액세스에 권한을 부여하는 경우 보안 주체가 [스토리지 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당되었는지 확인합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)을 참조 하세요.

[Python용 Azure ID 클라이언트 라이브러리](https://pypi.org/project/azure-identity/)를 사용하여 Azure AD로 애플리케이션을 인증할 수 있습니다.

클라이언트 ID, 클라이언트 암호, 테넌트 ID를 가져옵니다. 이 과정을 수행하려면 [클라이언트 애플리케이션의 요청에 대한 권한 부여를 위해 Azure AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조하세요. 해당 프로세스의 일부로 다음 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어) 역할 중 하나를 보안 주체에 할당해야 합니다.

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유하는 디렉터리와 파일만 해당.|

이 예시에서는 클라이언트 ID, 클라이언트 암호, 테넌트 ID를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 더 많은 예제는 [Python용 Azure ID 클라이언트 라이브러리](https://pypi.org/project/azure-identity/) 설명서를 참조하세요.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용한 연결

계정에 연결하는 가장 쉬운 방법입니다.

이 예제에서는 계정 키를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage_account_key` 자리 표시자 값을 스토리지 계정 액세스 키로 바꿉니다.

## <a name="set-acls"></a>ACL 설정

ACL을 *설정* 하는 경우 모든 항목을 포함하여 전체 ACL을 **바꿉니다**. 보안 주체의 권한 수준을 변경하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 바꾸는 대신 업데이트하려면 이 문서의 [ACL 업데이트](#update-acls-recursively) 섹션을 참조하세요.

이 섹션에서는 다음을 사용하는 방법을 보여 줍니다.

- 디렉터리의 ACL 설정
- 파일의 ACL 설정

### <a name="set-the-acl-of-a-directory"></a>디렉터리의 ACL 설정

**DataLakeDirectoryClient.get_access_control** 메서드를 호출하여 디렉터리의 ACL(액세스 제어 목록)을 가져오고 **DataLakeDirectoryClient.set_access_control** 메서드를 호출하여 ACL을 설정합니다.

이 예시에서는 `my-directory`라는 디렉터리의 ACL을 가져오고 설정합니다. `rwxr-xrw-` 문자열은 소유 사용자에게 읽기, 쓰기, 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 대상에는 읽기 및 쓰기 권한을 부여합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

컨테이너의 루트 디렉터리의 ACL을 가져오고 설정할 수도 있습니다. 루트 디렉터리를 가져오려면 **FileSystemClient._get_root_directory_client** 메서드를 호출합니다.

### <a name="set-the-acl-of-a-file"></a>파일의 ACL 설정

**DataLakeFileClient.get_access_control** 메서드를 호출하여 파일의 ACL(액세스 제어 목록)을 가져오고 **DataLakeFileClient.set_access_control** 메서드를 호출하여 ACL을 설정합니다.

이 예시에서는 `my-file.txt`라는 파일의 ACL을 가져오고 설정합니다. `rwxr-xrw-` 문자열은 소유 사용자에게 읽기, 쓰기, 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 대상에는 읽기 및 쓰기 권한을 부여합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>반복적으로 ACL 설정

ACL을 *설정* 하는 경우 모든 항목을 포함하여 전체 ACL을 **바꿉니다**. 보안 주체의 권한 수준을 변경하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 바꾸는 대신 업데이트하려면 이 문서의 [ACL의 재귀적 업데이트](#update-acls-recursively) 섹션을 참조하세요.

**DataLakeDirectoryClient.set_access_control_recursive** 메서드를 호출하여 ACL을 재귀적으로 설정합니다.

**기본** ACL 항목을 설정하려면 각 ACL 항목 문자열의 시작 부분에 `default:` 문자열을 추가합니다.

다음 예시에서는 `my-parent-directory`라는 디렉터리의 ACL을 설정합니다.

이 메서드는 기본 ACL을 설정할지 여부를 지정하는 `is_default_scope`라는 부울 매개 변수를 허용합니다. 해당 매개 변수가 `True`이면 ACL 항목의 목록 앞에 `default:` 문자열을 표시합니다.

ACL의 항목은 소유 사용자에 읽기, 쓰기, 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 사용자에게는 액세스 권한을 부여하지 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID가 ““xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”인 특정 사용자에게 읽기와 실행 권한을 제공합니다. 이러한 항목은 소유 사용자에 읽기, 쓰기, 실행 권한을 부여하고, 소유 그룹에는 읽기 및 실행 권한만 부여하며, 다른 모든 사용자에게는 액세스 권한을 부여하지 않습니다. 이 예시의 마지막 ACL 항목은 개체 ID가 ““xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”인 특정 사용자에게 읽기 및 실행 권한을 제공합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 처리하는 예시를 보려면 Python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조하세요.

## <a name="update-acls-recursively"></a>ACL의 재귀적 업데이트

ACL을 *업데이트* 할 때 ACL을 바꾸는 대신 ACL을 수정합니다. 예를 들어 ACL에 나열된 다른 보안 주체에 영향을 주지 않고 ACL에 새 보안 주체를 추가할 수 있습니다. ACL을 업데이트하지 않고 바꾸려면 이 문서의 [ACL 설정](#set-acls) 섹션을 참조하세요.

ACL을 재귀적으로 업데이트하려면 업데이트할 ACL 항목을 사용하여 새 ACL 개체를 만든 다음 ACL 업데이트 작업에서 해당 개체를 사용합니다. 기존 ACL을 가져오지 않고 업데이트할 ACL 항목만 제공하면 됩니다. **DataLakeDirectoryClient.update_access_control_recursive** 메서드를 호출하여 ACL을 재귀적으로 업데이트합니다. **기본** ACL 항목을 업데이트하려면 각 ACL 항목 문자열의 시작 부분에 `default:` 문자열을 추가합니다.

이 예시에서는 쓰기 권한이 있는 ACL 항목을 업데이트합니다.

다음 예시에서는 `my-parent-directory`라는 디렉터리의 ACL을 설정합니다. 이 메서드는 기본 ACL의 업데이트 여부를 지정하는 `is_default_scope`라는 부울 매개 변수를 허용합니다. 해당 매개 변수가 `True`이면 업데이트된 ACL 항목 앞에 `default:` 문자열을 입력합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 처리하는 예시를 보려면 Python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조하세요.

## <a name="remove-acl-entries-recursively"></a>ACL 항목의 재귀적 제거

하나 이상의 ACL 항목을 제거할 수 있습니다. ACL 항목을 재귀적으로 제거하려면 제거할 ACL 항목에 대한 새 ACL 개체를 만든 다음 ACL 제거 작업에서 해당 개체를 사용합니다. 기존 ACL을 가져오지 않고 제거할 ACL 항목만 제공합니다.

**DataLakeDirectoryClient.remove_access_control_recursive** 메서드를 호출하여 ACL 항목을 제거합니다. **기본** ACL 항목을 제거하려면 ACL 항목 문자열의 시작 부분에 `default:` 문자열을 추가합니다.

이 예시에서는 `my-parent-directory`라는 디렉터리의 ACL에서 ACL 항목을 제거합니다. 이 메서드는 기본 ACL에서 항목을 제거할지 여부를 지정하는 `is_default_scope`라는 부울 매개 변수를 허용합니다. 해당 매개 변수가 `True`이면 업데이트된 ACL 항목 앞에 `default:` 문자열을 입력합니다.

```python
def remove_permission_recursively(is_default_scope):

    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 처리하는 예시를 보려면 Python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조하세요.

## <a name="recover-from-failures"></a>오류에서 복구

런타임 또는 사용 권한 오류가 발생할 수 있습니다. 런타임 오류의 경우 처음부터 프로세스를 다시 시작합니다. 수정되는 디렉터리 계층 구조에 있는 디렉터리 또는 파일의 ACL을 수정할 수 있는 권한이 보안 주체에게 없는 경우에 권한 오류가 발생할 수 있습니다. 권한 문제를 해결한 다음 연속 토큰을 사용하여 오류 지점에서 프로세스를 다시 시작하거나 프로세스를 처음부터 다시 시작하도록 선택합니다. 처음부터 다시 시작하기를 선호하는 경우 연속 토큰을 사용할 필요는 없습니다. 부정적인 영향 없이 ACL 항목을 다시 적용할 수 있습니다.

이 예시에서는 오류가 발생한 경우 연속 토큰을 반환합니다. 애플리케이션은 오류가 해결된 후에 이 예시 메서드를 다시 호출하고 연속 토큰을 전달할 수 있습니다. 이 예시 메서드를 처음 호출하는 경우 애플리케이션은 연속 토큰 매개 변수에 대한 `None` 값을 전달할 수 있습니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 처리하는 예시를 보려면 Python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조하세요.

권한 오류가 발생해도 프로세스가 중단 없이 완료되도록 하기 위해 이를 지정할 수 있습니다.

프로세스가 중단 없이 완료되도록 하려면 연속 토큰을 **DataLakeDirectoryClient.set_access_control_recursive** 메서드에 전달하지 않습니다.

이 예시에서는 ACL 항목을 재귀적으로 설정합니다. 이 코드에 사용 권한 오류가 발생하면 해당 오류를 기록하고 계속해서 실행합니다. 이 예에서는 실패 횟수를 콘솔에 출력합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

일괄 처리 크기를 지정하여 일괄 처리에서 ACL을 재귀적으로 처리하는 예시를 보려면 Python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>참고 항목

- [API 참조 설명서](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)
- [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [피드백 제공](https://github.com/Azure/azure-sdk-for-python/issues)
- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2의 ACL](data-lake-storage-access-control.md)(액세스 제어 목록)
