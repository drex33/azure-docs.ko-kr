---
title: Azure ABAC(Azure 특성 기반 액세스 제어)는 무엇인가요? (미리 보기)
description: Azure ABAC(Azure 특성 기반 액세스 제어) 개요를 가져옵니다. 조건이 있는 역할 할당을 사용하여 Azure 리소스에 대한 액세스를 제어합니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: overview
ms.workload: identity
ms.date: 05/13/2021
ms.author: rolyon
ms.openlocfilehash: 84bcac43d22d1c90ccfe4f674c495d8439fe9536
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398329"
---
# <a name="what-is-azure-attribute-based-access-control-azure-abac-preview"></a>Azure ABAC(Azure 특성 기반 액세스 제어)는 무엇인가요? (미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

ABAC(특성 기반 액세스 제어)는 보안 주체, 리소스 및 환경과 관련된 특성에 따라 액세스를 정의하는 권한 부여 시스템입니다. ABAC를 사용하면 특성을 기반으로 리소스에 대한 보안 주체 액세스 권한을 부여할 수 있습니다. Azure ABAC는 Azure용 ABAC의 구현을 의미합니다.

## <a name="what-are-role-assignment-conditions"></a>역할 할당 조건이란 무엇인가요?

Azure RBAC(Azure 역할 기반 액세스 제어)는 Azure 리소스에 액세스할 수 있는 사용자, 해당 리소스로 수행할 수 있는 작업 및 액세스 권한이 있는 영역을 관리하는 데 도움이 되는 권한 부여 시스템입니다. 대부분의 경우 Azure RBAC는 역할 정의 및 역할 할당을 사용하여 필요한 액세스 관리를 제공합니다. 그러나 경우에 따라 더 세분화된 액세스 관리를 제공하거나 수백 가지 역할 할당 관리를 단순화할 수 있습니다.

Azure ABAC는 특정 작업의 맥락에서 특성을 기반으로 역할 할당 조건을 추가하여 Azure RBAC에서 빌드됩니다. *역할 할당 조건은* 필요에 따라 역할 할당에 추가하여 보다 세분화된 액세스 제어를 제공할 수 있는 추가 검사입니다. 조건은 역할 정의 및 역할 할당의 일부로 부여된 권한을 필터링합니다. 예를 들어 개체를 읽을 특정 태그를 포함하는 개체를 필요로 하는 조건을 추가할 수 있습니다. 조건을 사용하여 특정 리소스에 대한 액세스를 명시적으로 거부할 수 없습니다.

## <a name="why-use-conditions"></a>조건을 사용하는 이유는 무엇인가요?

역할 할당 조건을 사용할 경우 세 가지 주요 이점이 있습니다.

- **보다 세분화된 액세스 제어 제공** - 역할 할당은 작업 및 데이터 작업에 역할 정의를 사용하여 보안 주체 권한을 부여합니다. 보다 세분화된 액세스 제어를 위해 이러한 권한을 필터링하는 조건을 작성할 수 있습니다. 특정 작업에 조건을 추가할 수도 있습니다. 예를 들어 Blob이 Project = Blue로 태그가 지정된 경우에만 구독의 Blob에 대한 읽기 권한을 John에게 부여할 수 있습니다. 
- **역할 할당 수를 줄이는 데 도움이 됨** - 각 Azure 구독에는 현재 역할 할당 한도가 있습니다. 수천 개의 역할 할당이 필요한 시나리오가 있습니다. 이러한 모든 역할 할당을 관리해야 합니다. 이러한 시나리오에서는 역할 할당을 훨씬 적게 사용하는 조건을 추가할 수 있습니다. 
- **특정 비즈니스 의미가 있는 특성 사용** - 조건을 사용하면 액세스 제어에서 특정 비즈니스 의미가 있는 특성을 사용할 수 있습니다. 특성의 몇 가지 예로는 프로젝트 이름, 소프트웨어 개발 단계 및 분류 수준이 있습니다. 이러한 리소스 특성의 값은 동적이며 사용자가 팀 및 프로젝트 간에 이동함에 따라 변경됩니다.

## <a name="example-scenarios-for-conditions"></a>조건에 대한 시나리오 예

역할 할당에 조건을 추가할 수 있는 몇 가지 시나리오가 있습니다. 다음은 몇 가지 예제입니다.

- Project=Cascade 태그를 사용하여 Blob에 대한 읽기 권한
- 새 Blob은 Project=Cascade 태그를 포함해야 함
- 기존 Blob에 하나 이상의 Project 키 또는 Program 키로 태그를 지정해야 함
- 기존 Blob에는 Project 키와 Cascade, Baker 또는 Skagit 값으로 태그를 지정해야 함
- blobs-example-container라는 컨테이너의 Blob 읽기, 쓰기 또는 삭제
- 읽기 전용 경로가 있는 blobs-example-container라는 컨테이너의 Blob에 대한 읽기 권한
- uploads/contoso 경로를 사용하여 Contosocorp라는 컨테이너의 Blob에 대한 쓰기 액세스 권한
- Program=Alpine 태그와 로그 경로를 사용하여 Blob에 대한 읽기 권한

이러한 예를 만드는 방법에 대한 자세한 내용은 [Azure 역할 할당 조건의 예](../storage/common/storage-auth-abac-examples.md)를 참조하세요.

## <a name="where-can-conditions-be-added"></a>조건을 추가할 수 있는 위치는 어디인가요?

현재 [스토리지 Blob 데이터 작업](conditions-format.md#actions)을 포함하는 기본 제공 또는 사용자 지정 역할 할당에 조건을 추가할 수 있습니다. 여기에는 다음과 같은 기본 제공 역할이 포함됩니다.

- [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor)
- [Storage Blob 데이터 소유자](built-in-roles.md#storage-blob-data-owner)
- [Storage Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader)

조건은 역할 할당과 동일한 범위에 추가됩니다. 역할 할당과 마찬가지로 조건을 추가하려면 `Microsoft.Authorization/roleAssignments/write` 권한이 있어야 합니다.

다음은 조건에서 사용할 수 있는 스토리지 특성입니다.

- 컨테이너 이름
- Blob 경로
- Blob 인덱스 태그 키
- Blob 인덱스 태그

> [!TIP]
> 또한 Blob은 임의의 사용자 정의 키 값 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../storage/blobs/storage-manage-find-blobs.md)를 참조하세요.

## <a name="what-does-a-condition-look-like"></a>조건은 어떤 모양인가요?

새 역할 할당 또는 기존 역할 할당에 조건을 추가할 수 있습니다. 리소스 그룹 범위에서 Chandra라는 사용자에게 할당된 [Storage Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader) 역할이 있습니다. 또한 Project = Cascade 태그를 사용하여 Blob에 대한 읽기 액세스만 허용하는 조건이 추가되었습니다.

![조건이 있는 역할 할당의 다이어그램.](./media/conditions-overview/condition-role-assignment-rg.png)

Chandra가 Project = Cascade 태그 없이 Blob을 읽으려고 하면 액세스가 허용되지 않습니다.

![조건에서 허용되지 않는 액세스의 다이어그램.](./media/conditions-overview/condition-access-multiple.png)

Azure Portal에서 조건은 다음과 같습니다.

![Blob 인덱스 태그 값이 포함된 식 섹션을 빌드합니다.](./media/shared/condition-expressions.png)

코드에서 조건의 모양은 다음과 같습니다.

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

조건 형식에 대한 자세한 내용은 [Azure 역할 할당 조건 형식 및 구문](conditions-format.md)을 참조하세요.

## <a name="conditions-and-privileged-identity-management-pim"></a>조건 및 PIM(Privileged Identity Management)

PIM(Privileged Identity Management)을 사용하여 적격 역할 할당에 조건을 추가할 수도 있습니다. PIM을 사용하는 경우 최종 사용자가 특정 작업을 수행할 수 있는 권한을 얻기 위해 적격 역할 할당을 활성화해야 합니다. PIM의 조건을 통해 세분화된 조건을 사용하여 리소스에 대한 사용자의 액세스를 제한할 수 있을 뿐만 아니라 PIM을 사용하여 시간 제한 설정, 승인 워크플로, 감사 내역 등을 통해 리소스를 보호할 수 있습니다. 자세한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 할당](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)을 참조하세요.

## <a name="terminology"></a>용어

다음에 나오는 용어 목록은 Azure RBAC 및 Azure ABAC를 더 잘 이해하기 위해 다시 참조할 수 있습니다.

| 용어 | 정의 |
| --- | --- |
| ABAC(특성 기반 액세스 제어) | 보안 주체, 리소스 및 환경과 관련된 특성에 따라 액세스를 정의하는 권한 부여 시스템입니다. ABAC를 사용하면 특성을 기반으로 리소스에 대한 보안 주체 액세스 권한을 부여할 수 있습니다. |
| Azure ABAC | Azure용 ABAC의 구현을 의미합니다. |
| 역할 할당 조건 | 필요에 따라 역할 할당에 추가하여 보다 세분화된 액세스 제어를 제공할 수 있는 추가 검사입니다. |
| 특성 | 이 컨텍스트에서 Project = Blue와 같은 키 값 쌍입니다. 여기서 Project는 특성 키이고 Blue는 특성 값입니다. 특성 및 태그는 액세스 제어를 위한 동의어입니다. |
| 식 | true 또는 false로 평가되는 조건의 구문입니다. 식의 형식은 &lt;특성&gt; &lt;연산자&gt; &lt;값&gt;입니다. |

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건에 대한 FAQ(미리 보기)](conditions-faq.md)
- [Azure 역할 할당 조건 예(미리 보기)](../storage/common/storage-auth-abac-examples.md)
- [자습서: Azure Portal을 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)](../storage/common/storage-auth-abac-portal.md)
