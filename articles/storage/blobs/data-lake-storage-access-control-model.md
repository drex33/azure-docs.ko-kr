---
title: Azure Data Lake Storage Gen2에 대 한 액세스 제어 모델
description: 계층 네임 스페이스가 있는 계정에서 컨테이너, 디렉터리 및 파일 수준 액세스를 구성 하는 방법에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: 5861d97f1adffc6d92bb702b4ce59a34c103d9f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600269"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2의 액세스 제어 모델

Data Lake Storage Gen2는 다음과 같은 권한 부여 메커니즘을 지원 합니다.

- 공유 키 권한 부여
- SAS (공유 액세스 서명) 권한 부여
- Azure RBAC (역할 기반 액세스 제어)
- ACL(액세스 제어 목록)

[공유 키 및 SAS 권한 부여](#shared-key-and-shared-access-signature-sas-authorization) 는 Azure Active Directory (Azure AD)에서 id를 갖도록 요구 하지 않고 사용자 (또는 응용 프로그램)에 대 한 액세스 권한을 부여 합니다. 이러한 두 가지 형태의 인증을 사용 하는 경우 Azure RBAC 및 Acl은 아무런 영향을 주지 않습니다.

Azure RBAC 및 ACL은 모두 사용자 (또는 응용 프로그램)가 Azure AD에서 id를 갖도록 요구 합니다. Azure RBAC를 사용 하면 저장소 계정에 있는 **모든** 데이터에 대 한 읽기 또는 쓰기 액세스와 같은 저장소 계정 데이터에 대 한 "성긴" 액세스 권한을 부여할 수 있습니다. 반면 acl을 사용 하면 특정 디렉터리 또는 파일에 대 한 쓰기 액세스와 같은 "세분화 된" 액세스 권한을 부여할 수 있습니다.

이 문서에서는 Azure RBAC 및 Acl에 대해 집중적으로 설명 하 고, 시스템에서 저장소 계정 리소스에 대 한 권한 부여 결정을 내리는 데 이러한 항목을 평가 하는 방법을

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Azure RBAC (역할 기반 액세스 제어)

Azure RBAC는 역할 할당을 사용 하 여 [보안 주체](../../role-based-access-control/overview.md#security-principal)에 권한 집합을 적용 합니다. 보안 주체는 Azure AD(Active Directory)에 정의된 사용자, 그룹, 서비스 주체 또는 관리 ID를 나타내는 개체입니다. 권한 집합은 보안 주체에 게 저장소 계정의 **모든** 데이터에 대 한 읽기 또는 쓰기 액세스, 컨테이너의 **모든** 데이터에 대 한 읽기 또는 쓰기 액세스와 같은 "정교 하지 않은" 수준의 액세스를 제공할 수 있습니다.

다음 역할은 보안 주체가 저장소 계정의 데이터에 액세스할 수 있도록 허용 합니다.

|역할|Description|
|--|--|
| [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Blob storage 컨테이너 및 데이터에 대 한 모든 권한 이 액세스는 보안 주체가 항목의 소유자를 설정 하 고 모든 항목의 Acl을 수정할 수 있도록 허용 합니다. |
| [Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Blob storage 컨테이너 및 blob에 대 한 읽기, 쓰기 및 삭제 권한입니다. 이 액세스는 보안 주체가 항목의 소유권을 설정 하는 것을 허용 하지 않지만 보안 주체가 소유 하는 항목의 ACL을 수정할 수 있습니다. |
| [Storage Blob 데이터 읽기 권한자](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Blob storage 컨테이너 및 blob을 읽고 나열 합니다. |

[소유자](../../role-based-access-control/built-in-roles.md#owner), [참가자](../../role-based-access-control/built-in-roles.md#contributor), [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader)및 [Storage 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 와 같은 역할은 보안 주체가 저장소 계정을 관리 하는 것을 허용 하지만 해당 계정 내의 데이터에 대 한 액세스 권한은 제공 하지 않습니다. 그러나 이러한 역할 ( **판독기** 제외)은 다양 한 클라이언트 도구에서 데이터에 액세스 하는 데 사용할 수 있는 저장소 키에 대 한 액세스 권한을 얻을 수 있습니다.

## <a name="access-control-lists-acls"></a>ACL(액세스 제어 목록)

Acl을 사용 하면 디렉터리 및 파일에 대 한 "세분화 된" 수준의 액세스를 적용할 수 있습니다. *Acl* 은 일련의 *acl 항목* 을 포함 하는 사용 권한 구문입니다. 각 ACL 항목은 보안 주체를 액세스 수준과 연결 합니다. 자세히 알아보려면 [Azure Data Lake Storage Gen2의 acl (액세스 제어 목록)](data-lake-storage-access-control.md)을 참조 하세요.

## <a name="how-permissions-are-evaluated"></a>권한 평가 방법

보안 주체 기반 권한 부여 중에는 다음과 같은 순서로 권한이 평가 됩니다.

: 1: &nbsp; &nbsp; Azure 역할 할당이 먼저 평가 되 고 ACL 할당 보다 우선 합니다.

: 2: &nbsp; &nbsp; Azure 역할 할당에 따라 작업이 완전히 승인 된 경우 acl은 전혀 평가 되지 않습니다.

: 3: &nbsp; &nbsp; 작업이 완전히 승인 되지 않은 경우 acl이 평가 됩니다.

> [!div class="mx-imgBorder"]
> ![data lake storage 권한 흐름](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

시스템에서 액세스 권한을 평가 하는 방식 때문에 역할 할당에 의해 이미 부여 된 액세스는 ACL을 사용 하 여 **제한할** **수 없습니다** . 시스템이 Azure 역할 할당을 먼저 평가 하 고 할당에서 충분 한 액세스 권한을 부여 하는 경우 Acl은 무시 됩니다.

다음 다이어그램에서는 디렉터리 내용 나열, 파일 읽기 및 파일 쓰기와 같은 세 가지 일반적인 작업에 대 한 사용 권한 흐름을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![data lake storage 사용 권한 흐름 예제](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>사용 권한 테이블: Azure RBAC 및 ACL 결합

다음 표에서는 보안 주체가 **작업** 열에 나열 된 작업을 수행할 수 있도록 Azure 역할 및 ACL 항목을 결합 하는 방법을 보여 줍니다. 다음 표의 열은 가상 디렉터리 계층 구조의 각 수준을 나타냅니다. 컨테이너(`/`)의 루트 디렉터리에 대한 열, **Oregon** 이라는 하위 디렉터리, Oregon 디렉터리의 **Portland** 라는 하위 디렉터리, Portland 디렉터리의 **Data.txt** 라는 텍스트 파일이 있습니다. 이러한 열에는 사용 권한을 부여 하는 데 필요한 ACL 항목의 [약식](data-lake-storage-access-control.md#short-forms-for-permissions) 표현이 표시 됩니다.  작업을 수행 하는 데 ACL 항목이 필요 하지 않은 경우 _해당 없음 (해당 없음_)이 열에 표시 됩니다.

|    작업             | 할당 된 Azure 역할               |    /        | Oregon/     | Portland/ | Data.txt |
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Read Data.txt            |   Storage Blob 데이터 소유자        | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 기여자  | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 읽기 권한자       | 해당 없음      | 해당 없음      | 해당 없음       | N/A    |
|                          |   없음                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Append to Data.txt       |   Storage Blob 데이터 소유자        | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 기여자  | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 읽기 권한자       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   없음                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Delete Data.txt          |   Storage Blob 데이터 소유자        | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 기여자  | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 읽기 권한자       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   없음                           | `--X`    | `--X`    | `-WX`     | 해당 없음    |
| Create Data.txt          |   Storage Blob 데이터 소유자        | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 기여자  | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 읽기 권한자       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   없음                           | `--X`    | `--X`    | `-WX`     | 해당 없음    |
| List /                   |   Storage Blob 데이터 소유자        | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 기여자  | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 읽기 권한자       | 해당 없음      | 해당 없음      | 해당 없음       | N/A    |
|                          |   없음                           | `R-X`    | 해당 없음      | 해당 없음       | 해당 없음    |
| List /Oregon/            |   Storage Blob 데이터 소유자        | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 기여자  | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 읽기 권한자       | 해당 없음      | 해당 없음      | 해당 없음       | N/A    |
|                          |   없음                           | `--X`    | `R-X`    | 해당 없음       | 해당 없음    |
| List /Oregon/Portland/   |   Storage Blob 데이터 소유자        | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 기여자  | 해당 없음      | 해당 없음      | 해당 없음       | 해당 없음    |
|                          |   Storage Blob 데이터 읽기 권한자       | 해당 없음      | 해당 없음      | 해당 없음       | N/A    |
|                          |   없음                           | `--X`    | `--X`    | `R-X`     | 해당 없음    |

> [!NOTE]
> Azure Storage Explorer에서 컨테이너의 콘텐츠를 보려면 보안 주체가 [Azure AD를 사용 하 여 Storage Explorer에 로그인](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)해야 하 고 (최소한) 컨테이너의 루트 폴더 ()에 대 한 읽기 액세스 (R--)가 있어야 합니다 `\` . 이 권한 수준은 루트 폴더의 콘텐츠를 나열 하는 기능을 제공 합니다. 루트 폴더의 내용을 표시 하지 않으려면 [판독기](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당할 수 있습니다. 해당 역할을 사용 하 여 계정의 컨테이너를 나열할 수 있지만 컨테이너 내용은 나열할 수 없습니다. 그런 다음 Acl을 사용 하 여 특정 디렉터리 및 파일에 대 한 액세스 권한을 부여할 수 있습니다.

## <a name="security-groups"></a>보안 그룹

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Azure 역할 할당 및 ACL 항목에 대 한 제한

그룹을 사용하면 구독 당 최대 역할 할당 수와 파일 또는 디렉터리 당 ACL 항목의 최대 수를 초과할 가능성이 줄어듭니다. 다음 표에서는 이러한 제한에 대해 설명 합니다.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)]

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>공유 키 및 SAS (공유 액세스 서명) 권한 부여

Azure Data Lake Storage Gen2는 인증을 위한 [공유 키](/rest/api/storageservices/authorize-with-shared-key) 및 [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 방법도 지원 합니다. 이러한 인증 방법의 특징은 호출자와 연결 된 id가 없으므로 보안 주체 권한 기반 권한 부여를 수행할 수 없다는 것입니다.

공유 키의 경우 호출자는 ' 슈퍼 사용자 ' 액세스를 효율적으로 수행 합니다. 즉, 데이터를 포함 하는 모든 리소스에 대 한 모든 작업에 대 한 모든 액세스 권한, 소유자 설정 및 Acl 변경이 있습니다.

SAS 토큰에는 토큰의 일부로 허용된 권한이 포함됩니다. SAS 토큰에 포함된 권한은 모든 권한 부여 결정에 효과적으로 적용되지만, 추가적인 ACL 검사는 수행되지 않습니다.

## <a name="next-steps"></a>다음 단계

액세스 제어 목록에 대해 자세히 알아보려면 [Azure Data Lake Storage Gen2의 acl (액세스 제어 목록)](data-lake-storage-access-control.md)을 참조 하세요.
