---
title: 조건 및 사용자 지정 보안 특성 (미리 보기)을 사용 하 여 Azure 역할 할당 관리 확장-Azure ABAC
description: Azure 특성 기반 액세스 제어 (Azure ABAC) 조건 및 보안 주체에 대 한 Azure AD 사용자 지정 보안 특성을 사용 하 여 Azure 역할 할당의 관리를 확장 합니다.
services: active-directory
author: rolyon
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 11/16/2021
ms.author: rolyon
ms.openlocfilehash: a45d8cc6f84cf086615dd8ab4cdd64eddaabce67
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132765150"
---
# <a name="scale-the-management-of-azure-role-assignments-by-using-conditions-and-custom-security-attributes-preview"></a>조건 및 사용자 지정 보안 특성을 사용 하 여 Azure 역할 할당의 관리 크기 조정 (미리 보기)

> [!IMPORTANT]
> 사용자 지정 보안 특성은 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure RBAC (역할 기반 액세스 제어)는 현재 구독에서 2000 역할 할당을 지원 합니다. 수백 또는 심지어 수천 개의 Azure 역할 할당을 만들어야 하는 경우이 제한이 발생할 수 있습니다. 수백 또는 수천 개의 역할 할당을 관리 하는 것은 어려울 수 있습니다. 시나리오에 따라 역할 할당 수를 줄이고 액세스를 보다 쉽게 관리할 수 있습니다.

이 문서에서는 azure [특성 기반 액세스 제어 (azure ABAC)](conditions-overview.md) 조건 및 보안 주체에 대 한 [azure AD 사용자 지정 보안 특성](../active-directory/fundamentals/custom-security-attributes-overview.md) 을 사용 하 여 역할 할당의 관리를 확장 하는 솔루션을 설명 합니다.

## <a name="example-scenario"></a>예제 시나리오

수천 명의 고객이 다음 구성을 설정 하려는 Contoso 라는 회사를 생각해 보세요.

- 보안 및 성능상의 이유로 128 저장소 계정에서 고객 데이터를 배포 합니다.
- 각 고객에 대 한 컨테이너가 있는 각 저장소 계정에 2000 컨테이너를 추가 합니다.
- 각 고객에 게 고유한 Azure AD 서비스 주체를 나타냅니다.
- 각 고객이 컨테이너의 개체에 액세스할 수 있지만 다른 컨테이너에는 액세스할 수 없습니다.

이 구성으로 인해 구독에서 256000 [Storage Blob 데이터 소유자](built-in-roles.md#storage-blob-data-owner) 역할 할당은 2000 역할 할당 제한 보다 훨씬 더 필요할 수 있습니다. 이러한 많은 역할 할당을 유지 하는 것은 불가능 한 경우 유지 관리 하기가 어렵습니다.

![역할 할당에 대 한 수천 개를 보여 주는 다이어그램](./media/conditions-custom-security-attributes-example/role-assignments-multiple.png)

## <a name="example-solution"></a>예제 솔루션

이 시나리오를 유지 관리 가능한 방식으로 처리 하는 방법은 역할 할당 조건을 사용 하는 것입니다. 다음 다이어그램에서는 조건을 사용 하 여 하나의 역할 할당에만 256000 역할 할당을 줄이는 솔루션을 보여 줍니다. 역할 할당은 더 높은 리소스 그룹 범위에 있으며, 조건은 컨테이너에 대 한 액세스를 제어 하는 데 도움이 됩니다. 조건은 컨테이너 이름이 고객의 서비스 사용자에 대 한 사용자 지정 보안 특성과 일치 하는지 여부를 확인 합니다.

![하나의 역할 할당과 조건을 보여 주는 다이어그램](./media/conditions-custom-security-attributes-example/role-assignment-condition.png)

다음은이 솔루션이 작동 하 게 하는 조건의 식입니다.

```
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name]
  StringEquals
  @Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Contosocustomer_name]
```

전체 조건은 다음과 유사 합니다. 작업 목록은 필요한 [작업](conditions-format.md#actions) 으로만 조정 될 수 있습니다.

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals @Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Contosocustomer_name]
 )
)
```

## <a name="why-use-this-solution"></a>이 솔루션을 사용 하는 이유

데이터 평면 리소스에 대 한 액세스를 제공 하는 데 사용할 수 있는 몇 가지 액세스 제어 메커니즘이 있습니다.

액세스 키는 데이터 평면 리소스에 대 한 액세스를 제공 하는 일반적인 방법입니다. 액세스 키는 액세스 키를 소유 하는 사람이 누구에 게 읽기, 쓰기 및 삭제 권한을 제공 합니다. 이는 사용자가 액세스 키를 얻을 수 있는 경우 공격자가 중요 한 데이터에 액세스할 수 있음을 의미 합니다. 액세스 키에는 id 바인딩이 없고 만료 되는 것이 없으며 저장할 보안 위험이 있습니다.

액세스 키와 마찬가지로 SAS (공유 액세스 서명) 토큰에는 id 바인딩이 없지만 정기적으로 만료 됩니다. Id 바인딩 부족은 액세스 키와 동일한 보안 위험을 나타냅니다. 클라이언트에서 오류가 발생 하지 않도록 만료를 관리 해야 합니다. SAS 토큰은 매일 관리 및 작동 하는 추가 코드가 필요 하며 DevOps 팀에 상당한 오버 헤드가 발생할 수 있습니다.

리소스 기반 Acl (액세스 제어 목록)은 일반적으로 특정 리소스에 대 한 사용자 및 허용 된 작업 목록으로 리소스에 저장 됩니다. Acl은 세분화 된 액세스 제어를 제공 하며 id 바인딩이 있습니다. 그러나 Acl을 사용 하 여 각 개체에 대 한 액세스를 관리 해야 하 고, Acl을 재귀적으로 관리 하는 것과 관련 된 성능 영향을 만들며, 사용자 지정 스크립트 및 사용 권한 드리프트 수정에 대 한 관리

Azure RBAC는 중앙의 세분화 된 액세스 제어를 제공 합니다. Azure RBAC에는 보안 위험을 줄여주는 id 바인딩이 있습니다. 조건을 사용 하면 유연 하 고 동적인 특성을 기반으로 액세스 하므로 역할 할당의 관리를 확장 하 고 액세스 제어를 더 쉽게 유지할 수 있습니다.

이 솔루션의 몇 가지 이점은 다음과 같습니다.

- 중앙 액세스 제어
- 유지 관리가 용이 합니다.
- 액세스 키 또는 SAS 토큰을 사용 하지 않습니다.
- 각 개체에 대 한 액세스를 관리 하지 않아도 됩니다.
- 잠재적으로 보안 상태를 향상할 수 있습니다.

## <a name="can-you-use-this-solution"></a>이 솔루션을 사용할 수 있나요?

비슷한 시나리오를 사용 하는 경우 다음 단계에 따라이 솔루션을 잠재적으로 사용할 수 있는지 확인 합니다.

#### <a name="step-1-determine-if-you-meet-the-prerequisites"></a>1 단계: 필수 구성 요소를 충족 하는지 확인

이 솔루션을 사용 하려면 다음이 있어야 합니다.

- [저장소 blob 데이터 작업](../storage/common/storage-auth-abac-attributes.md)을 포함 하는 여러 기본 제공 또는 사용자 지정 역할 할당 여기에는 다음과 같은 기본 제공 역할이 포함됩니다.

    - [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor)
    - [Storage Blob 데이터 소유자](built-in-roles.md#storage-blob-data-owner)
    - [Storage Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader)

#### <a name="step-2-identify-the-attributes-you-could-use-in-your-condition"></a>2 단계: 조건에 사용할 수 있는 특성 확인

조건에 사용할 수 있는 몇 가지 특성은 다음과 같습니다.

- 컨테이너 이름
- Blob 경로
- Blob 인덱스 태그 [키]
- Blob 인덱스 태그 [키의 값]

사용자, 엔터프라이즈 응용 프로그램 및 관리 되는 id에 대 한 사용자 지정 보안 특성을 정의할 수도 있습니다.

자세한 내용은 [azure 역할 할당 조건 형식 및 구문](conditions-format.md#attributes) 및 [azure AD의 사용자 지정 보안 특성 이란?](../active-directory/fundamentals/custom-security-attributes-overview.md)을 참조 하세요.

#### <a name="step-3-create-a-condition-at-a-higher-scope"></a>3 단계: 더 높은 범위에서 조건 만들기

상위 범위에서 조건을 사용 하 여 액세스를 관리 하는 역할 할당을 하나 이상 만듭니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 조건 추가 또는 편집](conditions-role-assignments-portal.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure ABAC(Azure 특성 기반 액세스 제어)는 무엇인가요?](conditions-overview.md)
- [Azure AD의 사용자 지정 보안 특성은 무엇 인가요?](../active-directory/fundamentals/custom-security-attributes-overview.md)
- [태그 및 사용자 지정 보안 특성을 기반으로 blob에 대 한 읽기 액세스 허용](conditions-custom-security-attributes.md)
