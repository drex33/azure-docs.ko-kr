---
title: 개체 복제 개요
titleSuffix: Azure Storage
description: 개체 복제는 원본 스토리지 계정과 대상 계정 간에 블록 Blob을 비동기적으로 복사합니다. 개체 복제를 사용하여 읽기 요청에 대한 대기 시간을 최소화하고, 컴퓨팅 워크로드에 대한 효율성을 높이고, 데이터 배포를 최적화하며 비용을 최소화합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63b5339b0652a164056bff1e8c41c55afeca8b84
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278446"
---
# <a name="object-replication-for-block-blobs"></a>블록 Blob에 대한 개체 복제

개체 복제는 원본 스토리지 계정과 대상 계정 간에 블록 Blob을 비동기적으로 복사합니다. 개체 복제에서 지원하는 몇 가지 시나리오는 다음과 같습니다.

- **대기 시간 최소화.** 개체 복제를 사용하면 클라이언트가 물리적으로 더 근접한 지역의 데이터를 사용할 수 있으므로 읽기 요청 대기 시간을 줄일 수 있습니다.
- **컴퓨팅 작업의 효율성 제고.** 개체 복제를 사용하는 경우 컴퓨팅 워크로드는 서로 다른 지역에서 동일한 블록 Blob 집합을 처리할 수 있습니다.
- **데이터 배포 최적화.** 단일 위치의 데이터를 처리하거나 분석한 후 추가 지역에만 결과를 복제할 수 있습니다.
- **비용 최적화.** 데이터가 복제된 후에는 수명 주기 관리 정책을 사용해 해당 데이터를 보관 계층으로 이동하여 비용을 줄일 수 있습니다.

다음 다이어그램에서는 개체 복제에서 한 지역의 원본 스토리지 계정으로부터 블록 Blob을 두 개의 다른 지역에 있는 대상 계정으로 복제하는 방법을 보여 줍니다.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="개체 복제 작동 방식을 보여 주는 다이어그램":::

개체 복제를 구성하는 방법에 대한 자세한 내용은 [개체 복제 구성](object-replication-configure.md)을 참조하세요.

## <a name="prerequisites-for-object-replication"></a>개체 복제를 위한 필수 구성 요소

개체를 복제하려면 다음 Azure Storage 기능을 사용하도록 설정해야 합니다.

- [피드 변경](storage-blob-change-feed.md): 소스 계정에서 사용하도록 설정해야 합니다. 변경 피드를 사용하도록 설정하는 방법을 알아보려면 [변경 피드 사용 및 사용 안 함](storage-blob-change-feed.md#enable-and-disable-the-change-feed)을 참조하세요.
- [Blob 버전 관리](versioning-overview.md): 원본 및 대상 계정 모두에서 사용하도록 설정해야 합니다. 버전 관리를 사용하는 방법을 알아보려면 [Blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조하세요.

변경 피드 및 Blob 버전 관리를 사용하도록 설정하면 추가 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/)를 참조하세요.

개체 복제는 범용 v2 스토리지 계정 및 미리 보기의 프리미엄 블록 Blob 계정에 대해 지원됩니다. 원본 및 대상 계정은 모두 범용 v2 또는 프리미엄 블록 Blob 계정이어야 합니다. 개체 복제는 블록 blob만 지원합니다. 추가 Blob 및 페이지 Blob은 지원되지 않습니다.

> [!IMPORTANT]
> 프리미엄 블록 Blob 계정에 대한 개체 복제는 현재 **미리 보기로** 제공됩니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-object-replication-works"></a>개체 복제 작동 방법

개체 복제는 구성하는 규칙에 따라 컨테이너의 블록 Blob을 비동기적으로 복사합니다. Blob의 콘텐츠, Blob과 연결된 모든 버전, Blob의 메타데이터 및 속성은 모두 원본 컨테이너에서 대상 컨테이너로 복사됩니다.

> [!IMPORTANT]
> 블록 Blob 데이터는 비동기식으로 복제되므로 원본 계정 및 대상 계정이 즉시 동기화되지는 않습니다. 현재 데이터를 대상 계정에 복제하는 데 걸리는 시간에 대한 SLA는 없습니다. 원본 Blob에서 복제 상태를 확인하여 복제가 완료되었는지 여부를 확인할 수 있습니다. 자세한 내용은 [Blob의 복제 상태 확인](object-replication-configure.md#check-the-replication-status-of-a-blob)을 참조하세요.

### <a name="blob-versioning"></a>Blob 버전 관리

개체 복제를 사용하려면 원본 계정과 대상 계정 모두에서 Blob 버전 지정을 사용하도록 설정해야 합니다. 원본 계정에서 복제된 Blob이 수정되면 수정하기 전에 Blob의 이전 상태를 반영하는 새 버전의 Blob이 원본 계정에 만들어집니다. 원본 계정의 현재 버전은 최신 업데이트를 반영합니다. 현재 버전과 이전 버전이 모두 대상 계정에 복제됩니다. 쓰기 작업이 Blob 버전에 미치는 영향에 대한 자세한 내용은 [쓰기 작업 버전 관리](versioning-overview.md#versioning-on-write-operations)를 참조하세요.

원본 계정의 Blob이 삭제되면 Blob의 현재 버전이 이전 버전이 되고 더 이상 이전 버전이 없습니다. Blob의 모든 기존 이전 버전은 유지됩니다. 이 상태는 대상 계정에 복제됩니다. 삭제 작업이 Blob 버전에 미치는 영향에 대한 자세한 내용은 [삭제 작업 버전 관리](versioning-overview.md#versioning-on-delete-operations)를 참조하세요.

### <a name="snapshots"></a>스냅샷

개체 복제는 Blob 스냅샷을 지원하지 않습니다. 원본 계정의 Blob에 대한 스냅샷은 대상 계정에 복제되지 않습니다.

### <a name="blob-tiering"></a>Blob 계층화

원본 및 대상 계정이 핫 또는 쿨 계층에 있는 경우 개체 복제가 지원됩니다. 원본 및 대상 계정이 모두 다른 계층에 있을 수 있습니다. 그러나 원본 또는 대상 계정의 Blob이 보관 계층으로 이동된 경우에는 개체 복제가 실패합니다. Blob 계층에 대한 자세한 내용은 [Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층을 참조하세요.](access-tiers-overview.md)

### <a name="immutable-blobs"></a>변경이 불가능한 Blob

Azure Blob Storage에 대한 불변성 정책에는 시간 기반 보존 정책 및 법적 보존이 포함됩니다. 대상 계정에 불변성 정책이 적용되면 개체 복제가 영향을 받을 수 있습니다. 불변성 정책에 대한 자세한 내용은 [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)을 참조하세요.

컨테이너 수준의 불변성 정책이 대상 계정의 컨테이너에 적용되고 원본 컨테이너의 개체가 업데이트되거나 삭제되는 경우, 원본 컨테이너에 대한 작업은 성공할 수 있지만 대상 컨테이너에 대한 해당 작업의 복제는 실패합니다. 컨테이너로 범위가 지정된 불변성 정책으로 금지되는 작업에 대한 자세한 내용은 [컨테이너 수준 범위의 시나리오](immutable-storage-overview.md#scenarios-with-container-level-scope)를 참조하세요.

버전 수준 불변성 정책이 대상 계정의 Blob 버전에 적용되고 원본 컨테이너의 Blob 버전에 대한 삭제 또는 업데이트 작업이 수행되는 경우 원본 개체에 대한 작업은 성공할 수 있지만 대상 개체에 대한 해당 작업의 복제는 실패합니다. 컨테이너로 범위가 지정된 불변성 정책으로 금지되는 작업에 대한 자세한 내용은 [버전 수준 범위의 시나리오](immutable-storage-overview.md#scenarios-with-version-level-scope)를 참조하세요.

## <a name="object-replication-policies-and-rules"></a>개체 복제 정책 및 규칙

개체 복제를 구성하는 경우 원본 스토리지 계정 및 대상 계정을 지정하는 복제 정책을 만듭니다. 복제 정책에는 원본 컨테이너와 대상 컨테이너를 지정하고 원본 컨테이너에서 복제할 블록 Blob을 나타내는 하나 이상의 규칙이 포함되어 있습니다.

개체 복제를 구성하고 나면 Azure Storage에서 원본 계정에 대한 변경 피드를 정기적으로 확인하고 대상 계정에 대한 쓰기 또는 삭제 작업을 비동기적으로 복제합니다. 복제 대기 시간은 복제 중인 블록 Blob의 크기에 따라 달라집니다.

### <a name="replication-policies"></a>복제 정책

개체 복제를 구성하는 경우 Azure Storage 리소스 공급자를 통해 대상 계정에 대한 복제 정책을 만듭니다. 복제 정책이 만들어지면 Azure Storage에서 정책 ID를 해당 복제 정책에 할당합니다. 그런 다음, 정책 ID를 사용하여 해당 복제 정책을 원본 계정에 연결해야 합니다. 복제가 수행되려면 원본 및 대상 계정의 정책 ID가 동일해야 합니다.

원본 계정은 각 대상 계정에 대해 하나의 정책을 사용하여 세 개 이상의 대상 계정으로 복제할 수 있습니다. 마찬가지로, 계정이 세 개 이상의 복제 정책에 대한 대상 계정으로 사용될 수 있습니다.

원본 및 대상 계정이 동일한 지역 또는 다른 지역에 있을 수 있습니다. 동일한 구독 또는 다른 구독에 상주할 수도 있습니다. 필요에 따라 원본 및 대상 계정은 다른 Azure AD(Azure Active Directory) 테넌트에서 상주할 수 있습니다. 각 원본 계정/대상 계정 쌍에 대해 하나의 복제 정책만 만들 수 있습니다.

### <a name="replication-rules"></a>복제 규칙

복제 규칙은 Azure Storage 원본 컨테이너에서 대상 컨테이너로 Blob을 복제하는 방법을 지정합니다. 각 복제 정책에 대해 최대 10개의 복제 규칙을 지정할 수 있습니다. 각 복제 규칙은 단일 원본 및 대상 컨테이너를 정의하며, 각 원본 및 대상 컨테이너는 하나의 규칙에만 사용할 수 있습니다. 즉, 최대 10개의 원본 컨테이너와 10개의 대상 컨테이너가 단일 복제 정책에 참여할 수 있습니다.

복제 규칙을 만드는 경우 기본적으로 이후에 원본 컨테이너에 추가되는 새 블록 Blob만 복사됩니다. 신규 및 기존 블록 Blob을 모두 복사하도록 지정하거나 지정된 시간 이후로 생성된 블록 Blob을 복사하도록 사용자 지정 복사 범위를 정의할 수도 있습니다.

하나 이상의 필터를 복제 규칙의 일부로 지정하여 접두사를 기준으로 블록 Blob을 필터링할 수도 있습니다. 접두사를 지정하면 원본 컨테이너에서 해당 접두사와 일치하는 Blob만 대상 컨테이너에 복사됩니다.

원본 및 대상 컨테이너가 모두 있어야 규칙에서 지정할 수 있습니다. 복제 정책을 만든 후에는 대상 컨테이너에 대한 쓰기 작업이 허용되지 않습니다. 대상 컨테이너에 대한 쓰기 시도가 실패하고 오류 코드 409(충돌)가 발생합니다. 복제 규칙이 구성된 대상 컨테이너에 쓰려면 해당 컨테이너에 대해 구성된 규칙을 삭제하거나 복제 정책을 제거해야 합니다. 복제 정책이 활성화되어 있으면 대상 컨테이너에 대한 읽기 및 삭제 작업이 허용됩니다.

대상 컨테이너의 Blob에 대해 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출하여 보관 계층으로 이동할 수 있습니다. 보관 계층에 대한 자세한 내용은 [Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층을 참조하세요.](access-tiers-overview.md#archive-access-tier)

## <a name="policy-definition-file"></a>정책 정의 파일

개체 복제 정책은 JSON 파일로 정의됩니다. 기존 개체 복제 정책에서 정책 정의 파일을 얻을 수 있습니다. 정책 정의 파일을 업로드하여 개체 복제 정책을 만들 수도 있습니다.

### <a name="sample-policy-definition-file"></a>샘플 정책 정의 파일

다음 예제에서는 *b* 라는 접두사와 일치하는 단일 규칙을 사용하여 대상 계정에 대한 복제 정책을 정의하고, 복제할 Blob에 대한 최소 만들기 시간을 설정합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "destinationAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2021-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

### <a name="specify-full-resource-ids-for-the-source-and-destination-accounts"></a>원본 및 대상 계정에 대한 전체 리소스 ID 지정

정책 정의 파일을 만들 때 이전 섹션의 예제와 같이 **sourceAccount** 및 **destinationAccount** 항목에 대한 전체 Azure Resource Manager 리소스 ID를 지정합니다. 스토리지 계정의 리소스 ID를 찾는 방법을 알아보려면 스토리지 [계정의 리소스 ID 얻기를 참조하세요.](../common/storage-account-get-info.md#get-the-resource-id-for-a-storage-account)

전체 리소스 ID는 다음과 같은 형식입니다.

```http
/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

이전에 정책 정의 파일에는 스토리지 계정의 전체 리소스 ID 대신 계정 이름만 필요했습니다. Azure Storage 리소스 공급자 REST API 버전 2021-02-01의 **AllowCrossTenantReplication** 보안 속성이 도입되면서 이제 복제 정책에 참여하는 스토리지 계정에 대해 테넌트 간 복제가 허용되지 않을 때 생성되는 모든 개체 복제 정책에 대한 전체 리소스 ID를 제공해야 합니다. Azure Storage 전체 리소스 ID를 사용하여 원본 및 대상 계정이 동일한 테넌트 내에 있는지 확인합니다. 테넌트 간 복제 정책을 허용되지 않는 방법에 대한 자세한 내용은 [Azure AD 테넌트 간 복제 방지를 참조하세요.](#prevent-replication-across-azure-ad-tenants)

스토리지 계정에 대해 테넌트 간 복제가 허용되는 경우에도 계정 이름만 계속 지원되지만 항상 전체 리소스 ID를 제공하는 것이 좋습니다. Azure Storage 리소스 공급자의 모든 이전 버전은 개체 복제 정책에서 전체 리소스 ID 경로를 사용할 REST API 있습니다.

다음 표에서는 스토리지 계정에 대해 테넌트 간 복제가 허용되거나 허용되지 않는 시나리오에서 전체 리소스 ID가 지정된 복제 정책을 만들 때 발생하는 상황과 계정 이름을 비교한 내용을 설명합니다.

| 정책 정의에서 계정 식별자 Storage | 테넌트 간 복제 허용 | 테넌트 간 복제가 허용되지 않습니다. |
|--|--|--|
| 전체 리소스 ID | 동일한 테넌트 정책을 만들 수 있습니다.<br /><br /> 테넌트 간 정책을 만들 수 있습니다. | 동일한 테넌트 정책을 만들 수 있습니다.<br /><br /> 테넌트 간 정책은 만들 수 없습니다. |
| 계정 이름만 | 동일한 테넌트 정책을 만들 수 있습니다.<br /><br /> 테넌트 간 정책을 만들 수 있습니다. | 동일한 테넌트 또는 교차 테넌트 정책을 만들 수 없습니다. Azure Storage 원본 및 대상 계정이 동일한 테넌트 안에 있는지 확인할 수 없기 때문에 오류가 발생합니다. 오류는 정책 정의 파일에서 **sourceAccount** 및 **destinationAccount** 항목의 전체 리소스 ID를 지정해야 함을 나타냅니다. |

### <a name="specify-the-policy-and-rule-ids"></a>정책 및 규칙 ID 지정

다음 표에는 각 시나리오에서 정책 정의 파일의 **policyId** 및 **ruleId** 항목에 사용할 값이 요약되어 있습니다.

| 이 계정에 대한 정책 정의 파일을 만드는 경우... | 정책 ID를 이 값으로 설정 | 규칙 ID를 이 값으로 설정 |
|-|-|-|
| 대상 계정 | 문자열 값(*default*)입니다. Azure Storage에서 정책 ID 값을 만듭니다. | 빈 문자열입니다. Azure Storage에서 규칙 ID 값을 만듭니다. |
| 원본 계정 | 대상 계정에 대한 정책 정의 파일을 다운로드할 때 반환된 정책 ID의 값입니다. | 대상 계정에 대한 정책 정의 파일을 다운로드할 때 반환된 규칙의 값입니다. |

## <a name="prevent-replication-across-azure-ad-tenants"></a>Azure AD 테넌트 간 복제 방지

azure AD(Azure Active Directory) 테넌트는 ID 및 액세스 관리 목적으로 조직을 나타내는 Azure AD의 전용 인스턴스입니다. 각 Azure 구독은 단일 Azure AD 테넌트와 트러스트 관계를 맺습니다. 스토리지 계정을 포함한 구독의 모든 리소스는 동일한 Azure AD 테넌트와 연결됩니다. 자세한 내용은 [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)을 참조하세요.

기본적으로 적절한 권한이 있는 사용자는 하나의 Azure AD 테넌트 및 다른 테넌트에서 대상 계정에 있는 원본 스토리지 계정으로 개체 복제를 구성할 수 있습니다. 보안 정책에서 개체 복제를 동일한 테넌트 내에만 있는 스토리지 계정으로 제한해야 하는 경우 보안 속성 **AllowCrossTenantReplication** 속성(미리 보기)을 설정하여 테넌트 간에 복제를 허용하지 않도록 할 수 있습니다. 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 경우 해당 스토리지 계정을 원본 또는 대상 계정으로 구성된 모든 개체 복제 정책에 대해 Azure Storage 원본 및 대상 계정이 모두 동일한 Azure AD 테넌트 내에 있어야 합니다. 교차 테넌트 개체 복제를 허용되지 않는 것에 대한 자세한 내용은 [Azure Active Directory 테넌트 간에 개체 복제 방지를 참조하세요.](object-replication-prevent-cross-tenant-policies.md)

스토리지 계정에 대해 교차 테넌트 개체 복제를 허용하지 않도록 **하려면 AllowCrossTenantReplication** 속성을 *false로* 설정합니다. 스토리지 계정이 현재 테넌트 간 개체 복제 정책에 참여하지 않는 경우 **AllowCrossTenantReplication** 속성을 *false로* 설정하면 이 스토리지 계정을 원본 또는 대상으로 사용하여 테넌트 간 개체 복제 정책을 나중에 구성할 수 없습니다.

스토리지 계정이 현재 하나 이상의 교차 테넌트 개체 복제 정책에 참여하는 경우 **AllowCrossTenantReplication** 속성을 *false로* 설정하는 것은 허용되지 않습니다. 테넌트 간 복제를 허용되지 않으면 기존 테넌트 간 정책을 삭제해야 합니다.

기본적으로 **AllowCrossTenantReplication** 속성은 스토리지 계정에 대해 설정되지 않으며 해당 값은 *null이며* *true* 에 해당합니다. 스토리지 계정에 대한 **AllowCrossTenantReplication** 속성 값이 *null* 또는 *true이면* 권한 있는 사용자는 이 계정을 원본 또는 대상으로 사용하는 테넌트 간 개체 복제 정책을 구성할 수 있습니다. 테넌트 간 정책을 구성하는 방법에 대한 자세한 내용은 [블록 Blob에 대한 개체 복제 구성을 참조하세요.](object-replication-configure.md)

Azure Policy 사용하여 스토리지 계정 집합을 감사하여 **AllowCrossTenantReplication** 속성이 교차 테넌트 개체 복제를 방지하도록 설정되어 있는지 확인할 수 있습니다. Azure Policy 사용하여 스토리지 계정 집합에 대한 거버넌스를 적용할 수도 있습니다. 예를 들어 사용자가 **AllowCrossTenantReplication** 속성이 true 로 설정된 스토리지 계정을 만들거나 기존 스토리지 계정을 수정하여 속성 값을 *true* 로 변경하지 못하도록 거부 효과가 있는 정책을 만들 수 *있습니다.*

## <a name="replication-status"></a>복제 상태

원본 계정에서 Blob에 대한 복제 상태를 확인할 수 있습니다. 자세한 내용은 [Blob의 복제 상태 확인](object-replication-configure.md#check-the-replication-status-of-a-blob)을 참조하세요.

원본 계정의 Blob에 대한 복제 상태가 실패를 나타내는 경우 다음과 같은 가능한 원인을 조사합니다.

- 개체 복제 정책이 대상 계정에 구성되어 있는지 확인합니다.
- 대상 컨테이너가 여전히 있는지 확인합니다.
- 원본 Blob이 쓰기 작업의 일부로 고객이 제공한 키로 암호화된 경우 개체 복제가 실패합니다. 고객 제공 키에 대한 자세한 내용은 [Blob Storage에 요청 시 암호화 키 제공](encryption-customer-provided-keys.md)을 참조하세요.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![아니요](../media/icons/no-icon.png)              | ![아니요](../media/icons/no-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![아니요](../media/icons/no-icon.png)              | ![아니요](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

## <a name="billing"></a>결제

개체 복제는 원본 및 대상 계정에 대한 읽기 및 쓰기 트랜잭션에 대한 추가 비용을 발생시킬 뿐만 아니라 원본 계정에서 대상 계정으로의 데이터 복제에 대한 송신 비용과 변경 피드를 처리하기 위한 읽기 비용을 발생시킵니다.

## <a name="next-steps"></a>다음 단계

- [개체 복제 구성](object-replication-configure.md)
- [Azure Active Directory 테넌트에서 개체 복제 방지](object-replication-prevent-cross-tenant-policies.md)
- [Blob 버전 관리](versioning-overview.md)
- [Azure Blob Storage의 변경 피드 지원](storage-blob-change-feed.md)
