---
title: Blob 데이터의 변경이 불가능한 스토리지 개요
titleSuffix: Azure Storage
description: Azure Storage는 사용자가 지울 수 없고 수정할 수 없는 상태로 데이터를 저장할 수 있는 Blob Storage에 대한 WORM(Write Once, Read Many) 지원을 제공합니다. 시간 기반 보존 정책은 Blob 데이터를 지정된 간격 동안 WORM 상태로 저장하고, 법적 보존은 명시적으로 제거될 때까지 유효한 상태로 유지됩니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: c923af2d4260afba8ed8347c6723119278ab1c62
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278636"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장

변경이 불가능한 스토리지를 Azure Blob Storage에 사용하면 사용자가 중요 비즈니스용 데이터를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. WORM 상태에 있는 동안에는 사용자가 데이터를 지정한 간격 동안 수정하거나 삭제할 수 없습니다. Blob 데이터에 대한 불변성 정책을 구성하면 데이터를 덮어쓰거나 삭제하지 못하게 방지할 수 있습니다.

Azure Blob 스토리지에 대한 변경이 불가능한 스토리지는 다음과 같은 두 가지 유형의 불변성 정책을 지원합니다.

- **시간 기반 보존 정책**: 시간 기반 보존 정책을 사용하면 사용자는 지정한 간격 동안 데이터를 저장하는 정책을 설정할 수 있습니다. 시간 기반 보존 정책을 설정하면 개체를 만들고 읽을 수 있지만, 수정하거나 삭제할 수는 없습니다. 보존 기간이 만료된 후에는 개체를 삭제할 수는 있지만 덮어쓸 수는 없습니다. 시간 기반 보존 정책에 대한 자세한 내용은 [변경이 불가능한 Blob 데이터에 대한 시간 기반 보존 정책](immutable-time-based-retention-policy-overview.md)을 참조하세요.

- **법적 보존 정책**: 법적 보존은 명시적으로 제거될 때까지 변경이 불가능한 데이터를 저장합니다. 법적 보존을 설정하면 개체를 만들고 읽을 수 있지만, 수정하거나 삭제할 수는 없습니다. 법적 보존 정책에 대한 자세한 내용은 [변경이 불가능한 Blob 데이터에 대한 법적 보존](immutable-legal-hold-overview.md)을 참조하세요.

다음 다이어그램은 시간 기반 보존 정책 및 법적 보존이 적용되는 동안 쓰기 및 삭제 작업을 방지하는 방법을 보여 줍니다.

:::image type="content" source="media/immutable-storage-overview/worm-diagram.png" alt-text="보존 정책 및 법적 보존이 쓰기 및 삭제 작업을 방지하는 방법을 보여 주는 다이어그램":::

## <a name="about-immutable-storage-for-blobs"></a>Blob에 대한 변경이 불가능한 스토리지

변경이 불가능한 스토리지는 의료 기관, 금융 기관 및 관련 산업, &mdash;특히 중개인 및 딜러 조직&mdash;이 데이터를 안전하게 저장하는 데 도움이 됩니다. 변경이 불가능한 스토리지는 모든 시나리오에 활용하여 수정 또는 삭제로부터 중요한 데이터를 보호할 수 있습니다.

일반적인 적용 분야는 다음과 같습니다.

- **규정 준수**: Azure Blob Storage에 대한 변경 불가능한 스토리지를 사용하면 SEC 17a-4(f), CFTC 1.31(d), FINRA 및 기타 규정을 처리할 수 있습니다.

- **보안 문서 보존**: Blob에 대한 변경이 불가능한 스토리지를 사용하면 계정 관리 권한이 있는 사용자를 포함하여 그 어떤 사용자도 데이터를 수정하거나 삭제할 수 없습니다.

- **법적 보존**: Blob에 대한 변경이 불가능한 스토리지를 사용하면 사용자는 법적 보존이 제거되기 전에는 소송 또는 비즈니스 사용에 중요한 정보를 원하는 기간 동안 변조가 불가능한 상태로 저장할 수 있습니다. 이 기능은 법률 사용 사례로 국한되지 않지만 이벤트 트리거 또는 엔터프라이즈 정책을 기반으로 데이터를 보호해야 하는 이벤트 기반 보존 또는 엔터프라이즈 잠금으로 간주할 수도 있습니다.

## <a name="regulatory-compliance"></a>규정 준수

Microsoft는 레코드 관리 및 정보 거버넌스를 전문으로 하는 선도적인 독립 평가 회사인 Cohasset Associates를 유지하여 변경이 불가능한 Blob Storage와 금융 서비스 산업에 특정한 요구 사항을 준수하는지 평가했습니다. Cohasset에서는 Blob을 WORM 상태로 유지하는 데 사용되는 변경이 불가능한 스토리지가 CFTC Rule 1.31(c)-(d), FINRA Rule 4511 및 SEC Rule 17a-4의 관련 스토리지 요구 사항을 충족하는 것을 확인했습니다. Microsoft는 금융 기관의 기록 보존과 관련하여 전 세계적으로 가장 규범적인 지침을 제시하는 이 규칙 세트를 목표로 삼았습니다.

Cohasset 보고서는 [Microsoft 서비스 보안 센터](https://aka.ms/AzureWormStorage)에서 확인할 수 있습니다. [Azure 보안 센터](https://www.microsoft.com/trustcenter/compliance/compliance-overview)에는 Microsoft의 규정 준수 인증에 대한 자세한 정보가 포함되어 있습니다. WORM 불변성 준수와 관련하여 Microsoft로부터 증명 서신을 요청하려면 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="immutability-policy-scope"></a>불변성 정책 범위

불변성 정책의 범위는 Blob 버전(미리 보기) 또는 컨테이너로 지정할 수 있습니다. 불변성 정책 하에서 개체가 작동하는 방식은 정책의 범위에 따라 다릅니다. 각 불변성 정책 유형의 정책 범위에 대한 자세한 내용은 다음 섹션을 참조하십시오.

- [시간 기반 보존 정책 범위](immutable-time-based-retention-policy-overview.md#time-based-retention-policy-scope)
- [법적 보존 범위](immutable-legal-hold-overview.md#legal-hold-scope)

범위에 따라 리소스(컨테이너 또는 Blob 버전)에 대한 시간 기반 보존 정책 및 법적 보존을 모두 구성할 수 있습니다. 다음 표에는 각 리소스 범위에 대해 지원되는 불변성 정책이 요약되어 있습니다.

| Scope | 컨테이너가 버전 수준 불변성 정책을 지원합니다. | 컨테이너가 버전 수준 불변성 정책을 지원하지 않습니다. |
|--|--|--|
| 컨테이너 | 하나의 기본 버전 수준 불변성 정책을 지원합니다. 구성된 기본 정책은 컨테이너에 생성된 모든 새 버전에 적용됩니다.<br /><br /> 법적 보존을 지원하지 않습니다. | 하나의 컨테이너 수준 불변성 정책과 하나의 법적 보존을 지원합니다. Blob 버전의 정책은 컨테이너에 지정된 기본 정책을 재정의할 수 있습니다. |
| Blob 버전 | 하나의 버전 수준 불변성 정책과 하나의 법적 보존을 지원합니다. | 해당 없음 |

### <a name="about-the-preview"></a>미리 보기 정보

버전 수준 불변성 정책 미리 보기는 다음 지역에서 사용할 수 있습니다.

- 캐나다 중부
- 캐나다 동부
- 프랑스 중부
- 프랑스 남부

> [!IMPORTANT]
> 버전 수준 불변성 정책은 현재 **미리 보기** 로 제공됩니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="summary-of-immutability-scenarios"></a>불변성 시나리오 요약

불변성 정책에서 제공하는 보호는 불변성 정책의 범위, 정책의 잠금 여부(시간 기반 보존 정책인), 정책의 활성화 또는 만료 여부에 따라 달라집니다.

### <a name="scenarios-with-version-level-scope"></a>버전 수준 범위를 사용하는 시나리오

다음 표에는 버전 수준 불변성 정책에서 제공하는 보호 기능이 요약되어 있습니다.

| 시나리오 | 금지된 작업 | Blob 보호 | 컨테이너 보호 | 계정 보호 |
|--|--|--|--|--|
| Blob 버전이 *활성* 보존 정책을 통해 보호되고/되거나 법적 보존이 적용되고 있습니다. | [Blob 삭제](/rest/api/storageservices/delete-blob), [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata), [페이지 배치](/rest/api/storageservices/put-page) 및 [블록 추가](/rest/api/storageservices/append-block)<sup>1</sup> | Blob 버전을 삭제할 수 없습니다. 사용자 메타데이터를 쓸 수 없습니다. <br /><br /> Blob을 [Blob 배치](/rest/api/storageservices/put-blob), [블록 목록 배치](/rest/api/storageservices/put-block-list) 또는 [Blob 복사](/rest/api/storageservices/copy-blob)로 덮어쓰면 새 버전이 생성됩니다.<sup>2</sup> | 정책 잠금 또는 잠금 해제 여부에 관계 없이 하나 이상의 Blob이 컨테이너에 있는 경우 컨테이너를 삭제할 수 없습니다. | 버전 수준 불변 스토리지를 사용하는 컨테이너가 하나 이상 있는 경우 스토리지 계정 삭제가 실패합니다. |
| Blob 버전이 *만료된* 보존 정책을 통해 보호되고 있으며 법적 보존이 적용되고 있지 않습니다. | [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata), [페이지 배치](/rest/api/storageservices/put-page) 및 [블록 추가](/rest/api/storageservices/append-block)<sup>1</sup> | Blob 버전을 삭제할 수 있습니다. 사용자 메타데이터를 쓸 수 없습니다. <br /><br /> Blob을 [Blob 배치](/rest/api/storageservices/put-blob), [블록 목록 배치](/rest/api/storageservices/put-block-list) 또는 [Blob 복사](/rest/api/storageservices/copy-blob)로 덮어쓰면 새 버전이 생성됩니다.<sup>2</sup> | 정책 잠금 또는 잠금 해제 여부에 관계 없이 하나 이상의 Blob이 컨테이너에 있는 경우 컨테이너를 삭제할 수 없습니다. | 시간 기반 보존 정책이 잠긴 Blob 버전을 포함하고 있는 컨테이너가 하나 이상 있는 경우 스토리지 계정 삭제가 실패합니다.<br /><br />잠금 해제된 정책은 삭제 방지 기능을 제공하지 않습니다. |

<sup>1</sup> [블록 추가](/rest/api/storageservices/append-block) 작업은 **allowProtectedAppendWrites** 속성을 사용하도록 설정된 시간 기반 보존 정책에만 허용됩니다. 자세한 내용은 [보호된 추가 Blob 쓰기 허용](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes) 섹션을 참조하세요.
<sup>2</sup> Blob 버전은 항상 콘텐츠에 대해 변경할 수 없습니다. 스토리지 계정에 대해 버전 관리를 사용하도록 설정하면 블록 Blob에 대한 쓰기 작업이 새 버전을 만듭니다. 단, [블록 배치](/rest/api/storageservices/put-block) 작업은 예외입니다.

### <a name="scenarios-with-container-level-scope"></a>컨테이너 수준 범위를 사용하는 시나리오

다음 표에는 컨테이너 수준 불변성 정책에서 제공하는 보호 기능이 요약되어 있습니다.

| 시나리오 | 금지된 작업 | Blob 보호 | 컨테이너 보호 | 계정 보호 |
|--|--|--|--|--|
| 컨테이너가 *활성* 시간 기반 보존 정책을 통해 보호되고/되거나 법적 보존이 적용되고 있습니다. | [Blob 삭제](/rest/api/storageservices/delete-blob), [Blob 배치](/rest/api/storageservices/put-blob)<sup>1</sup>, [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata), [페이지 배치](/rest/api/storageservices/put-page), [Blob 속성 설정](/rest/api/storageservices/set-blob-properties), [Blob 스냅샷 생성](/rest/api/storageservices/snapshot-blob), [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob), [블록 추가](/rest/api/storageservices/append-block)<sup>2</sup> | 컨테이너의 모든 Blob은 콘텐츠 및 사용자 메타데이터에 대해 변경할 수 없습니다. | 컨테이너 수준 정책이 적용되면 컨테이너 삭제가 실패합니다. | 하나 이상의 Blob을 포함하고 있는 컨테이너가 있으면 스토리지 계정 삭제가 실패합니다. |
| 컨테이너 범위가 지정된 *만료된* 시간 기반 보존 정책을 통해 컨테이너가 보호되고 있으며 법적 보존이 적용되고 있습니다. | [Blob 배치](/rest/api/storageservices/put-blob)<sup>1</sup>, [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata), [페이지 배치](/rest/api/storageservices/put-page), [Blob 속성 설정](/rest/api/storageservices/set-blob-properties), [Blob 스냅샷 생성](/rest/api/storageservices/snapshot-blob), [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob), [블록 추가](/rest/api/storageservices/append-block)<sup>2</sup> | 삭제 작업이 허용됩니다. 덮어쓰기 작업은 허용되지 않습니다. | 정책 잠금 또는 잠금 해제 여부에 관계 없이 하나 이상의 Blob이 컨테이너에 있는 경우 컨테이너를 삭제할 수 없습니다. | 시간 기반 보존 정책이 잠긴 컨테이너가 하나 이상 있으면 스토리지 계정 삭제가 실패합니다.<br /><br />잠금 해제된 정책은 삭제 방지 기능을 제공하지 않습니다. |

<sup>1</sup> Azure Storage는 [Blob 배치](/rest/api/storageservices/put-blob) 작업에서 새 Blob을 만드는 것을 허용합니다. 변경할 수 없는 컨테이너의 기존 Blob 경로에 대한 후속 덮어쓰기 작업은 허용되지 않습니다.

<sup>2</sup> [블록 추가](/rest/api/storageservices/append-block) 작업은 **allowProtectedAppendWrites** 속성을 사용하도록 설정된 시간 기반 보존 정책에만 허용됩니다. 자세한 내용은 [보호된 추가 Blob 쓰기 허용](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes) 섹션을 참조하세요.

> [!NOTE]
> [URL에 SQL 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url)과 같은 일부 워크로드는 Blob을 만든 다음 추가합니다. 컨테이너에 활성 시간 기반 보존 정책 또는 법적 보존이 있는 경우 이 패턴은 성공하지 못합니다.

## <a name="supported-account-configurations"></a>지원되는 계정 구성

불변성 정책은 신규 및 기존 스토리지 계정 모두에 대해 지원됩니다. 다음 표에서는 각 유형의 정책에 대해 지원되는 스토리지 계정 유형을 보여줍니다.

| 불변성 정책 유형 | 정책 범위 | 지원되는 스토리지 계정 유형 | 계층 구조 네임스페이스 지원(미리 보기) |
|--|--|--|--|
| 시간 기반 보존 정책 | 버전 수준 범위(미리 보기) | 범용 v2<br />프리미엄 블록 Blob | No |
| 시간 기반 보존 정책 | 컨테이너 수준 범위 | 범용 v2<br />프리미엄 블록 Blob<br />범용 v1(레거시)<sup>1</sup><br> Blob 스토리지(레거시) | Yes |
| 법적 보존 | 버전 수준 범위(미리 보기) | 범용 v2<br />프리미엄 블록 Blob | No |
| 법적 보존 | 컨테이너 수준 범위 | 범용 v2<br />프리미엄 블록 Blob<br />범용 v1(레거시)<sup>1</sup><br> Blob 스토리지(레거시) | Yes |

<sup>1</sup> 더 많은 기능을 활용할 수 있도록 범용 v1 계정을 범용 v2로 업그레이드하는 것이 좋습니다. 기존 범용 v1 스토리지 계정 업그레이드에 대한 자세한 내용은 [스토리지 계정 업그레이드](../common/storage-account-upgrade.md)를 참조하세요.

### <a name="access-tiers"></a>액세스 계층

모든 Blob 액세스 계층은 변경이 불가능한 스토리지를 지원합니다. Blob 계층 설정 작업을 사용하여 Blob의 액세스 계층을 변경할 수 있습니다. 자세한 내용은 [blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층](access-tiers-overview.md)을 참조 하세요.

### <a name="redundancy-configurations"></a>중복성 구성

모든 중복성 구성은 변경이 불가능한 스토리지를 지원합니다. 지역 중복 구성의 경우 고객 관리형 장애 조치(failover)가 지원되지 않습니다. 중복성 구성에 대한 자세한 내용은 [Azure Storage 중복성](../common/storage-redundancy.md)을 참조하세요.

### <a name="hierarchical-namespace-support"></a>계층 구조 네임스페이스 지원

계층 구조 네임스페이스를 사용하는 계정에 대한 변경이 불가능한 스토리지는 현재 미리 보기로 제공됩니다. 미리 보기에 등록하려면 [Azure Data Lake Storage의 미리 보기 기능](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u)을 참조하세요.

Blob이 변경할 수 없는 상태이고 계정에서 계층 구조 네임스페이스를 사용하도록 설정한 경우에는 Blob 이름을 바꾸거나 Blob을 이동할 수 없습니다. Blob 이름과 디렉터리 구조는 변경이 불가능한 정책이 적용되면 수정할 수 없는 필수적인 컨테이너 수준 데이터를 제공합니다.

> [!IMPORTANT]
> 계층 구조 네임스페이스 기능을 사용하도록 설정된 계정에서 Azure Blob 스토리지에 대한 변경이 불가능한 스토리지는 현재 미리 보기로 제공됩니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="recommended-blob-types"></a>권장하는 Blob 유형

주로 블록 Blob 및 Blob 추가에 대해 불변성 정책을 구성하는 것이 좋습니다. 활성 가상 머신의 VHD 디스크를 저장하는 페이지 Blob에 대한 불변성 정책을 구성하면 디스크에 대한 쓰기가 차단되므로 권장하지 않습니다. 시간 기반 정책을 잠그기 전에 설명서를 철저히 검토하고 시나리오를 테스트하는 것이 좋습니다.

## <a name="immutable-storage-with-blob-soft-delete"></a>Blob 일시 삭제를 사용하는 변경이 불가능한 스토리지

스토리지 계정에 대해 Blob 일시 삭제를 구성하면 법적 보존 또는 시간 기반 보존 정책이 적용되는지 여부와 관계없이 계정 내의 모든 Blob에 일시 삭제가 적용됩니다. 불변성 정책을 적용하기 전에 추가 보호를 위해 일시 삭제를 사용하도록 설정하는 것이 좋습니다.

Blob 일시 삭제를 사용하도록 설정한 후에 불변성 정책을 구성하면 일시 삭제 보존 정책이 만료된 후에도 일시 삭제된 Blob이 영구적으로 삭제됩니다. 일시 삭제된 Blob은 일시 삭제 보존 기간 동안 복원할 수 있습니다. 아직 일시 삭제되지 않은 Blob 또는 버전은 불변성 정책을 통해 보호되며, 시간 기반 보존 정책이 만료되거나 법적 보존이 제거될 때까지 일시 삭제할 수 없습니다.

## <a name="use-blob-inventory-to-track-immutability-policies"></a>Blob 인벤토리를 사용하여 불변성 정책 추적

Azure Storage Blob 인벤토리는 스토리지 계정의 컨테이너와 그 안에 들어 있는 Blob, 스냅샷 및 Blob 버전의 개요를 제공합니다. Blob 인벤토리 보고서를 사용하여 리소스에 대한 불변성 정책이 구성되었는지 여부를 포함하여 Blob 및 컨테이너의 특성을 이해할 수 있습니다.

Blob 인벤토리를 사용하도록 설정하면 Azure Storage는 매일 인벤토리 보고서를 생성합니다. 이 보고서는 비즈니스 및 규정 준수 요구 사항에 대한 데이터 개요를 제공합니다.

Blob 인벤토리에 대한 자세한 내용은 [Azure Storage Blob 인벤토리(미리 보기)](blob-inventory.md)를 참조하세요.

## <a name="pricing"></a>가격 책정

변경이 불가능한 스토리지를 사용할 때 추가 용량 요금이 부과되지 않습니다. 변경할 수 없는 데이터는 변경할 수 있는 데이터와 동일한 방식으로 가격이 책정됩니다. Azure Blob Storage의 가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

Blob 버전에 대한 시간 기반 보존 정책 또는 법적 보존을 만들거나, 수정하거나, 삭제하면 쓰기 트랜잭션 요금이 발생합니다.

요금을 지불하지 않고 계정에서 활성 시간 기반 보존 정책이 적용되면 Microsoft와 맺은 계약조건에 규정된 대로 일반 데이터 보존 정책이 적용됩니다. 일반 정보는 [Microsoft의 데이터 관리](https://www.microsoft.com/trust-center/privacy/data-management)를 참조하세요.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)  <sup>2</sup>              | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

<sup>2</sup>    기능은 미리 보기 수준에서 지원 됩니다.

## <a name="next-steps"></a>다음 단계

- [데이터 보호 개요](data-protection-overview.md)
- [변경이 불가능한 Blob 데이터에 대한 시간 기반 보존 정책](immutable-time-based-retention-policy-overview.md)
- [변경이 불가능한 Blob 데이터에 대한 법적 보존](immutable-legal-hold-overview.md)
- [Blob 버전에 대한 불변성 정책 구성(미리 보기)](immutable-policy-configure-version-scope.md)
- [컨테이너에 대한 불변성 정책 구성](immutable-policy-configure-container-scope.md)
