---
title: Azure Data Lake Storage Gen2의 액세스 제어 목록 | Microsoft Docs
description: Azure Data Lake Storage Gen2에서 POSIX와 유사한 ACL 액세스 제어 목록을 사용하는 방법을 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 41afd3cf3720c5fa86acf75acadc07e60fa2f3cc
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968848"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2의 ACL(액세스 제어 목록)

Azure Data Lake Storage Gen2는 Azure RBAC(Azure 역할 기반 액세스 제어)와 POSIX 같은 ACL(액세스 제어 목록)을 모두 지원하는 액세스 제어 모델을 구현합니다. 이 문서에서는 Data Lake Storage Gen2의 액세스 제어 목록에 대해 설명합니다. Azure RBAC와 ACL을 어떻게 통합하는지 및 시스템에서 권한 부여 결정을 내리기 위해 이를 어떻게 평가하는지 알아보려면 [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control-model.md)을 참조하세요.

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>ACL 정보

[보안 주체](../../role-based-access-control/overview.md#security-principal)를 파일 및 디렉터리에 대한 액세스 수준과 연결할 수 있습니다. 각 연결은 ‘ACL(액세스 제어 목록)’에 항목으로 캡처됩니다. 스토리지 계정의 각 파일과 디렉터리에는 액세스 제어 목록이 있습니다. 보안 주체가 파일이나 디렉터리에 대한 작업을 시도하는 경우 ACL 검사는 해당 보안 주체(사용자, 그룹, 서비스 주체 또는 관리 ID)에게 작업을 수행할 수 있는 올바른 권한 수준이 있는지 확인합니다.

> [!NOTE]
> ACL은 동일한 테넌트의 보안 주체에만 적용되며 공유 키 또는 SAS(공유 액세스 서명) 토큰 인증을 사용하는 사용자에게는 적용되지 않습니다. 이는 ID가 호출자와 연결되어 있지 않으며 따라서 보안 주체 권한 기반 권한 부여를 수행할 수 없기 때문입니다.  

<a id="set-access-control-lists"></a>

## <a name="how-to-set-acls"></a>ACL 설정 방법

파일 및 디렉터리 수준 권한을 설정하려면 다음 문서 중 하나를 참조하세요.

| 환경 | 아티클 |
|--------|-----------|
|Azure Storage Explorer |[Azure Storage Explorer를 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리](data-lake-storage-explorer-acl.md)|
|Azure portal |[Azure Portal을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리](data-lake-storage-acl-azure-portal.md)|
|.NET |[.NET을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리](data-lake-storage-acl-dotnet.md)|
|Java|[Java를 사용해 Azure Data Lake Storage Gen2에서 ACL 관리](data-lake-storage-acl-java.md)|
|Python|[Python을 사용한 Azure Data Lake Storage Gen2의 ACL 관리](data-lake-storage-acl-python.md)|
|JavaScript(Node.js)|[Node.js에서 JavaScript SDK를 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리](data-lake-storage-directory-file-acl-javascript.md)|
|PowerShell|[PowerShell을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리](data-lake-storage-acl-powershell.md)|
|Azure CLI|[Azure CLI를 사용한 Azure Data Lake Storage Gen2의 ACL 관리](data-lake-storage-acl-cli.md)|
|REST API |[경로 - 업데이트](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> 보안 주체가 *서비스* 주체인 경우 관련 앱 등록의 개체 ID가 아니라 서비스 주체의 개체 ID를 사용하는 것이 중요합니다. 서비스 주체의 개체 ID를 가져오려면 Azure CLI를 연 후 `az ad sp show --id <Your App ID> --query objectId` 명령을 사용합니다. `<Your App ID>` 자리 표시자를 앱 등록의 앱 ID로 바꾸어야 합니다.

## <a name="types-of-acls"></a>ACL 유형

두 가지 종류의 액세스 제어 목록, 즉 *액세스 ACL* 및 *기본 ACL* 이 있습니다.

액세스 ACL은 개체에 대한 액세스를 제어합니다. 파일과 디렉터리 모두에 액세스 ACL이 있습니다.

기본 ACL은 디렉터리에 생성된 모든 자식 항목의 액세스 ACL을 결정하는 디렉터리와 연결된 ACL 템플릿입니다. 파일에는 기본 ACL이 없습니다.

액세스 ACL 및 기본 ACL의 구조는 모두 동일합니다.

> [!NOTE]
> 부모 항목에서 기본 ACL을 변경해도 이미 존재하는 자식 항목의 액세스 ACL 또는 기본 ACL에는 영향을 주지 않습니다.

## <a name="levels-of-permission"></a>권한 수준

컨테이너에 있는 디렉터리와 파일에 대한 권한은 **읽기**, **쓰기**, **실행** 이며 다음 표와 같이 파일 및 디렉터리에서 사용할 수 있습니다.

|            |    파일     |   디렉터리 |
|------------|-------------|----------|
| **읽기(R)** | 파일의 내용을 읽을 수 있습니다. | 디렉터리의 내용을 나열하려면 **읽기** 및 **실행** 이 필요합니다. |
| **쓰기(W)** | 쓰거나 파일에 추가할 수 있습니다. | 디렉터리에 자식 항목을 만들려면 **쓰기** 및 **실행** 이 필요합니다. |
| **실행(X)** | Data Lake Storage Gen2의 컨텍스트에서는 의미가 없습니다. | 디렉터리의 자식 항목을 트래버스하는 데 필요합니다. |

> [!NOTE]
> ACL만 사용하여 권한을 부여하는 경우(Azure RBAC 없음) 보안 주체에 파일에 대한 읽기 또는 쓰기 액세스 권한을 부여하려면 컨테이너의 루트 폴더와 파일을 받는 폴더 계층 구조의 각 폴더에 보안 주체 **실행** 권한을 부여해야 합니다.

### <a name="short-forms-for-permissions"></a>사용 권한에 대한 짧은 형식

**RWX** 는 **읽기 + 쓰기 + 실행** 을 나타내는 데 사용됩니다. **읽기=4**, **쓰기=2** 및 **실행=1** 의 압축된 숫자 형식이 있으며, 그 합계는 권한을 나타냅니다. 다음은 몇 가지 예입니다.

| 숫자 형식 | 약식 |      의미     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 읽기 + 쓰기 + 실행 |
| 5            | `R-X`        | 읽기 + 실행         |
| 4            | `R--`        | 읽기                   |
| 0            | `---`        | 사용 권한 없음         |

### <a name="permissions-inheritance"></a>권한 상속

Data Lake Storage Gen2에서 사용하는 POSIX 스타일 모델에서 항목에 대한 권한은 항목 자체에 저장됩니다. 즉 자식 항목이 이미 만들어진 후에 권한이 설정되면 항목에 대한 권한은 부모 항목에서 상속할 수 없습니다. 자식 항목이 만들어지기 전에 부모 항목에 기본 권한이 설정된 경우에만 권한이 상속됩니다.

## <a name="common-scenarios-related-to-acl-permissions"></a>ACL 권한과 관련된 일반적인 시나리오

다음 표에서는 보안 주체가 **작업** 열에 나열된 작업을 수행할 수 있도록 하는 데 필요한 ACL 항목을 보여 줍니다. 

다음 표의 열은 가상 디렉터리 계층 구조의 각 수준을 나타냅니다. 컨테이너(`/`)의 루트 디렉터리에 대한 열, **Oregon** 이라는 하위 디렉터리, Oregon 디렉터리의 **Portland** 라는 하위 디렉터리, Portland 디렉터리의 **Data.txt** 라는 텍스트 파일이 있습니다. 

> [!IMPORTANT]
> 이 표에서는 Azure 역할 할당 없이 ACL **만** 사용한다고 가정합니다. Azure RBAC와 ACL을 결합하는 비슷한 표를 보려면 [권한 표: AZURE RBAC와 ACL 결합](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl)을 참조하세요.

|    작업             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> 위의 두 조건이 참(true)이면 파일을 삭제하는 데 파일에 대한 쓰기 권한이 필요하지 않습니다.

## <a name="users-and-identities"></a>사용자 및 ID

모든 파일 및 디렉터리에는 이러한 ID에 대한 고유 권한이 있습니다.

- 소유 그룹
- 소유 그룹
- 명명된 사용자
- 명명된 그룹
- 명명된 서비스 주체
- 명명된 관리 ID
- 기타 모든 사용자

사용자 및 그룹의 ID는 Azure AD(Azure Active Directory) ID입니다. 따라서 달리 언급하지 않는 한 Data Lake Storage Gen2의 컨텍스트에서 *사용자* 는 Azure AD 사용자, 서비스 주체, 관리 ID 또는 보안 그룹을 나타낼 수 있습니다.

### <a name="the-owning-user"></a>소유 그룹

항목을 만든 사용자는 자동으로 항목의 소유 사용자가 됩니다. 소유 사용자는 다음을 수행할 수 있습니다.

* 소유한 파일의 권한을 변경합니다.
* 소유 사용자가 대상 그룹의 멤버이면 소유한 파일의 소유 그룹을 변경합니다.

> [!NOTE]
> 소유 사용자는 파일 또는 디렉터리의 소유 사용자를 *변경할 수 없습니다*. 슈퍼 사용자만 파일 또는 디렉터리의 소유 사용자를 변경할 수 있습니다.

### <a name="the-owning-group"></a>소유 그룹

POSIX ACL에서 모든 사용자는 *주 그룹* 과 연결됩니다. 예를 들어 "Alice" 사용자는 "finance" 그룹에 속할 수 있습니다. 또한 Alice는 여러 그룹에 속할 수 있지만 항상 한 그룹을 주 그룹으로 지정합니다. POSIX에서 Alice가 파일을 만들 때는 해당 파일의 소유 그룹이 자신의 주 그룹(여기서는 "finance"임)으로 설정됩니다. 그렇지 않으면 소유 그룹은 다른 사용자/그룹에 할당된 사용 권한과 유사하게 동작합니다.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>새 파일 또는 디렉터리에 대한 소유 그룹 할당

* **사례 1**: 루트 디렉터리("/")입니다. 이 디렉터리는 Data Lake Storage Gen2 컨테이너를 만들 때 만들어집니다. 이 경우 소유 그룹은 OAuth를 사용하여 컨테이너를 만든 사용자로 설정됩니다. 공유 키, 계정 SAS 또는 서비스 SAS를 사용하여 컨테이너를 만드는 경우 소유자 및 소유 그룹은 **$superuser** 로 설정됩니다.
* **사례 2**(기타 모든 경우): 새 항목을 만들 때 소유 그룹이 부모 디렉터리에서 복사됩니다.

#### <a name="changing-the-owning-group"></a>소유 그룹 변경

소유 그룹은 다음에 의해 변경될 수 있습니다.
* 모든 슈퍼 사용자
* 소유 사용자가 대상 그룹의 구성원이기도 한 경우 소유 사용자입니다.

> [!NOTE]
> 소유 그룹은 파일 또는 디렉터리의 ACL을 변경할 수 없습니다.  루트 디렉터리의 경우 소유 그룹은 계정을 만든 사용자로 설정되지만, 위의 **사례 1** 에서 단일 사용자 계정은 소유 그룹을 통해 권한을 제공하는 데 적합하지 않습니다. 해당하는 경우 올바른 사용자 그룹에 이 권한을 할당할 수 있습니다.

## <a name="how-permissions-are-evaluated"></a>권한 평가 방법

ID는 다음 순서로 평가됩니다. 

1. 슈퍼 사용자
2. 소유 사용자
3. 명명된 사용자, 서비스 주체 또는 관리 ID
4. 소유 그룹 또는 명명된 그룹
5. 기타 모든 사용자

둘 이상의 ID가 보안 주체에 적용되는 경우 첫 번째 ID와 연결된 권한 수준이 부여됩니다. 예를 들어 보안 주체가 소유 사용자이자 명명된 사용자인 경우 소유 사용자와 연결된 권한 수준이 적용됩니다.

다음 의사 코드는 스토리지 계정에 대한 액세스 검사 알고리즘을 나타냅니다. 이 알고리즘은 ID가 평가되는 순서를 보여 줍니다.

```python
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
  # Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask isn't used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>마스크

액세스 검사 알고리즘에서 설명한 대로 마스크는 명명된 사용자, 소유 그룹 및 명명된 그룹에 대한 액세스를 제한합니다.  

새 Data Lake Storage Gen2 컨테이너의 경우 루트 디렉터리("/")의 액세스 ACL에 대한 마스크는 기본적으로 디텍터리에 대해 **750**, 파일에 대해 **640** 으로 설정됩니다. 다음 표에서는 이러한 권한 수준의 기호 표기법을 보여 줍니다.

|엔터티|디렉터리|파일|
|--|--|--|
|소유 사용자|`rwx`|`rw-`|
|소유 그룹|`r-x`|`r--`|
|기타|`---`|`---`|

저장소 전용 시스템의 파일과는 관련이 없으므로 파일은 X비트를 받지 않습니다. 

마스크는 호출별로 지정할 수 있습니다. 이렇게 하면 클러스터와 같은 다양한 소비 시스템에서 파일 작업에 대한 다른 유효한 마스크를 갖출 수 있습니다. 지정된 요청에 마스크가 지정되면 기본 마스크를 완전히 재정의합니다.

### <a name="the-sticky-bit"></a>고정 비트

고정 비트는 POSIX 컨테이너의 고급 기능입니다. Data Lake Storage Gen2의 컨텍스트에서는 고정 비트가 필요하지 않을 수 있습니다. 요약하면, 디렉터리에서 고정 비트를 사용하도록 설정되는 경우 자식 항목의 소유 사용자만 자식 항목을 삭제하거나 이름을 바꿀 수 있습니다.

고정 비트는 Azure Portal에서 표시하지 않습니다.

## <a name="default-permissions-on-new-files-and-directories"></a>새 파일 및 디렉터리에 대한 기본 권한

기존 디렉터리 아래에 새 파일 또는 디렉터리를 만들어지면 부모 디렉터리에 대한 기본 ACL에서 다음 항목을 결정합니다.

- 자식 디렉터리의 기본 ACL 및 액세스 ACL
- 자식 파일의 액세스 ACL(파일에 기본 ACL이 없는 경우)

### <a name="umask"></a>umask

파일 또는 디렉터리를 만들 때는 umask를 사용하여 자식 항목에 기본 ACL이 설정되는 방식을 수정합니다. umask는 **소유 사용자**, **소유 그룹** 및 **기타** 에 대한 RWX 값이 포함된 부모 디렉터리의 9비트 값입니다.

Azure Data Lake Storage Gen2에 대한 umask는 007로 설정된 상수 값입니다. 이 값은 다음과 같이 변환됩니다.

| umask 구성 요소     | 숫자 형식 | 약식 | 의미 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 소유 사용자의 경우 부모 항목의 기본 ACL을 자식 항목의 액세스 ACL에 복사합니다. | 
| umask.owning_group  |    0         |   `---`      | 소유 그룹의 경우 부모 항목의 기본 ACL을 자식 항목의 액세스 ACL에 복사합니다. | 
| umask.other         |    7         |   `RWX`      | 기타의 경우 자식 항목의 액세스 ACL에 있는 모든 권한을 제거합니다. |

Azure Data Lake Storage Gen2에서 효과적으로 사용되는 umask 값은 기본 ACL이 부모 디렉터리에 정의되어 있지 않은 한 **기타** 에 대한 값이 기본적으로 새 자식 항목에 전송되지 않는다는 것을 의미합니다. 이 경우 umask는 사실상 무시되고 기본 ACL에서 정의한 권한은 자식 항목에 적용됩니다. 

다음 의사 코드는 하위 항목의 ACL을 만들 때 umask가 적용되는 방식을 보여 줍니다.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="faq"></a>FAQ

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL에 대한 지원을 사용하도록 설정해야 하나요?

아니요. HNS(계층 구조 네임스페이스) 기능이 설정되어 있는 한 ACL을 통한 액세스 제어는 스토리지 계정에 대해 활성화됩니다.

HNS가 해제된 경우에도 Azure RBAC 권한 부여 규칙이 여전히 적용됩니다.

### <a name="what-is-the-best-way-to-apply-acls"></a>ACL을 적용하는 가장 좋은 방법은 무엇인가요?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Azure RBAC 및 ACL 권한은 어떻게 평가되나요?

시스템에서 Azure RBAC 및 ACL을 평가하여 스토리지 계정 리소스에 대한 권한 부여 결정을 내리는 방법을 알아보려면 [권한 평가 방법](data-lake-storage-access-control-model.md#how-permissions-are-evaluated)을 참조하세요.

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Azure 역할 할당 및 ACL 항목에 대해 어떤 제한이 있나요?

다음 표에서는 Azure RBAC를 사용하여 "정교하지 않은" 권한(스토리지 계정 또는 컨테이너에 적용되는 권한)을 관리하고 ACL을 사용하여 "세분화된" 권한(파일 및 디렉터리에 적용되는 권한)을 관리할 때 고려해야 하는 제한 사항에 대한 요약 보기를 제공합니다. ACL 할당에 보안 그룹을 사용합니다. 그룹을 사용하면 구독 당 최대 역할 할당 수와 파일 또는 디렉터리 당 ACL 항목의 최대 수를 초과할 가능성이 줄어듭니다. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Data Lake Storage Gen2에서 Azure RBAC의 상속을 지원하나요?

Azure 역할 할당은 상속됩니다. 할당은 구독, 리소스 그룹 및 스토리지 계정 리소스에서 컨테이너 리소스로 전달됩니다.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2에서 ACL의 상속을 지원하나요?

기본 ACL을 사용하여 부모 디렉터리 아래에 만들어진 새로운 자식 하위 디렉터리 및 파일에 대한 ACL을 설정할 수 있습니다. 기존 하위 항목에 대한 ACL을 업데이트하려면 원하는 디렉터리 계층 구조에 대한 ACL을 재귀적으로 추가, 업데이트 또는 제거해야 합니다. 지침은 이 문서의 [ACL 설정 방법](#set-access-control-lists) 섹션에서 참조하세요. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>디렉터리 및 해당 내용을 재귀적으로 삭제하는 데 필요한 권한은 무엇인가요?

- 호출자에게 '슈퍼 사용자' 권한이 있어야 합니다.

또는

- 부모 디렉터리에 쓰기 + 실행 권한이 있어야 합니다.
- 삭제할 디렉터리와 그 안의 모든 디렉터리에 읽기 + 쓰기 + 실행 권한이 필요합니다.

> [!NOTE]
> 디렉터리의 파일을 삭제하는 데에는 쓰기 권한이 필요하지 않습니다. 또한 루트 디렉터리("/")는 절대로 삭제할 수 없습니다.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>파일 또는 디렉터리의 소유자는 누구인가요?

파일 또는 디렉터리의 작성자는 소유자가 됩니다. 루트 디렉터리의 경우 소유자는 컨테이너를 만든 사용자의 ID입니다.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>만드는 파일 또는 디렉터리의 소유 그룹으로 설정되는 그룹은 무엇인가요?

소유 그룹은 새 파일 또는 디렉터리가 만들어지는 부모 디렉터리의 소유 그룹에서 복사됩니다.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>파일의 소유 사용자이지만 필요한 RWX 권한이 없습니다. 어떻게 해야 합니까?

소유 사용자는 파일의 권한을 변경하여 필요한 RWX 권한을 부여할 수 있습니다.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>ACL에 GUID가 표시되는 경우가 있습니다. 왜 그런가요?

항목이 사용자를 나타내고 해당 사용자가 Azure AD에 더 이상 존재하지 않으면 GUID가 표시됩니다. 일반적으로 사용자가 퇴사하거나 Azure AD에서 해당 계정이 삭제될 때 이러한 현상이 발생합니다. 또한 서비스 주체와 보안 그룹은 이를 식별할 UPN(사용자 계정 이름)을 가지고 있지 않으므로 OID 특성(GUID)으로 표시됩니다.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>서비스 주체에 대해 ACL을 올바르게 설정하려면 어떻게 하나요?

서비스 주체에 대한 ACL을 정의하는 경우 만든 앱 등록에 대한 *서비스 주체* 의 OID(개체 ID)를 사용하는 것이 중요합니다. 등록된 앱에는 특정 Azure AD 테넌트에 별도의 서비스 주체가 있다는 점에 유의해야 합니다. 등록된 앱은 Azure Portal에서 볼 수 있는 OID가 있지만 *서비스 주체* 에는 다른 OID가 있습니다.

앱 등록에 해당하는 서비스 주체의 OID를 가져오기 위해 `az ad sp show` 명령을 사용할 수 있습니다. 애플리케이션 ID를 매개 변수로 지정합니다. 앱 ID가 18218b12-1895-43e9-ad80-6e8fc1ea88ce인 앱 등록에 해당하는 서비스 주체의 OID를 가져오는 예제는 다음과 같습니다. Azure CLI에서 다음 명령을 실행합니다.

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID가 표시됩니다.

서비스 주체에 대한 올바른 OID가 있는 경우 Storage Explorer의 **액세스 관리** 페이지로 이동하여 OID를 추가하고 OID에 대한 적절한 권한을 할당합니다. **저장** 을 선택해야 합니다.

### <a name="can-i-set-the-acl-of-a-container"></a>컨테이너의 ACL을 설정할 수 있나요?

아니요. 컨테이너에는 ACL이 없습니다. 그러나 컨테이너의 루트 디렉터리에 대한 ACL을 설정할 수 있습니다. 모든 컨테이너에는 루트 디렉터리가 있으며 컨테이너와 같은 이름을 공유합니다. 예를 들어 컨테이너 이름이 `my-container`인 경우 루트 디렉터리의 이름은 `my-container/`입니다. 

Azure Storage REST API에는 [Set Container ACL](/rest/api/storageservices/set-container-acl)이라는 작업이 포함되어 있지만 해당 작업은 컨테이너 또는 컨테이너의 루트 디렉터리에 대한 ACL을 설정하는 데 사용할 수 없습니다. 대신 이 작업을 사용하여 컨테이너의 Blob에 [공개적으로 액세스할 수 있는지](anonymous-read-access-configure.md) 여부를 나타낼 수 있습니다. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>POSIX 액세스 제어 모델에 대한 어디서 자세히 알아볼 수 있나요?

* [Linux의 POSIX Access Control 목록](https://www.linux.com/news/posix-acls-linux)(영문)
* [HDFS 권한 가이드](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)(영문)
* [POSIX FAQ](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu의 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Linux의 액세스 제어 목록 사용](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)(영문)

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control-model.md)
