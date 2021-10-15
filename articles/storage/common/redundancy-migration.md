---
title: Change how a storage account is replicated(스토리지 계정이 복제되는 방식 변경)
titleSuffix: Azure Storage
description: 기존 스토리지 계정의 데이터가 복제되는 방식을 변경하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 187c4c1808133308f607bf9cb70b851a16d50e57
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039128"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Change how a storage account is replicated(스토리지 계정이 복제되는 방식 변경)

Azure Storage는 항상 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 항상 여러 복사본을 저장합니다. 중복성을 사용하면 저장소 계정은 오류가 발생하는 경우에도 [Azure Storage용 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/)를 충족하게 됩니다.

Azure Storage는 다음과 같은 복제 유형을 제공합니다.

- LRS(로컬 중복 스토리지)
- ZRS(영역 중복 스토리지)
- GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)
- GZRS(지역 영역 중복 스토리지) 또는 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)

이러한 각 옵션에 대한 개요는 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.

## <a name="switch-between-types-of-replication"></a>복제 유형 간 전환

스토리지 계정을 하나의 복제 유형에서 다른 복제 유형으로 전환할 수 있지만 일부 시나리오는 다른 시나리오보다 좀 더 간단합니다. 보조 지역에 대한 지역 복제 또는 읽기 권한을 추가하거나 제거하려면 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 복제 설정을 업데이트할 수 있습니다. 그러나 LRS에서 ZRS로 이동하거나 그 반대로 이동하여 주 지역에서 데이터를 복제하는 방법을 변경하려면 수동 마이그레이션을 수행해야 합니다.

다음 표에서는 각 복제 유형에서 다른 복제 유형으로 전환하는 방법에 대한 개요를 제공합니다.

| 전환 | LRS로 | GRS/RA-GRS로 | ZRS로 | GZRS/RA-GZRS로 |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>LRS에서</b> | 해당 없음 | Azure Portal, PowerShell 또는 CLI를 사용하여 복제 설정 변경<sup>1, 2</sup> | 수동 마이그레이션 수행 <br /><br /> OR <br /><br /> 실시간 마이그레이션 요청 | 수동 마이그레이션 수행 <br /><br /> OR <br /><br /> 먼저 GRS/RA-GRS로 전환한 후 실시간 마이그레이션 요청<sup>3</sup> |
| <b>GRS/RA-GRS에서</b> | Azure Portal, PowerShell 또는 CLI를 사용하여 복제 설정 변경 | 해당 없음 | 수동 마이그레이션 수행 <br /><br /> OR <br /><br /> 먼저 LRS로 전환한 후 실시간 마이그레이션 요청<sup>3</sup> | 수동 마이그레이션 수행 <br /><br /> OR <br /><br /> 실시간 마이그레이션 요청<sup>3</sup> |
| <b>ZRS에서</b> | 수동 마이그레이션 수행 | 수동 마이그레이션 수행 | 해당 없음 | 실시간 마이그레이션 요청<sup>3</sup> |
| <b>GZRS/RA-GZRS에서</b> | 수동 마이그레이션 수행 | 수동 마이그레이션 수행 | Azure Portal, PowerShell 또는 CLI를 사용하여 복제 설정 변경 | 해당 없음 |

<sup>1</sup> 일회성 송신 요금이 발생합니다.<br />
<sup>2</sup> 스토리지 계정에 보관 계층의 BLOB이 포함된 경우 LRS에서 GRS로 마이그레이션할 수 없습니다.<br />
<sup>3</sup> 실시간 마이그레이션은 표준 범용 v2 및 프리미엄 파일 공유 스토리지 계정에 대해 지원됩니다. 프리미엄 블록 Blob 또는 페이지 Blob 스토리지 계정에 대한 실시간 마이그레이션은 지원되지 않습니다.

> [!CAUTION]
> (RA-)GRS 또는 (RA-)GZRS 계정에 대해 [계정 장애 조치(failover)](storage-disaster-recovery-guidance.md)를 수행한 경우 해당 계정은 장애 조치 후 새 주 지역에 로컬로 중복(LRS)됩니다. 장애 조치로 인한 LRS 계정의 ZRS 또는 GZRS에 대한 실시간 마이그레이션은 지원되지 않습니다. 이는 장애 복구(failback) 작업의 경우에도 마찬가지입니다. 예를 들어, 보조 지역의 RA-GZRS에서 LRS로 계정 장애 조치를 수행한 다음 RA-GRS로 다시 구성하고 원래 주 지역에 대한 다른 계정 장애 조치를 수행하면 주 지역의 RA-GZRS의 원래 실시간 마이그레이션에 대해 고객 지원팀에 문의할 수 없습니다. 대신 ZRS 또는 GZRS로 수동 마이그레이션을 수행해야 합니다.

## <a name="change-the-replication-setting"></a>복제 설정 변경

주 지역에서 데이터 복제 방법을 변경하지 않는 한 Azure Portal, PowerShell, Azure CLI를 사용하여 스토리지 계정에 대한 복제 설정을 변경할 수 있습니다. 주 지역의 LRS에서 주 지역의 ZRS로 또는 그 반대로 마이그레이션하는 경우 수동 마이그레이션 또는 실시간 마이그레이션을 수행해야 합니다.

스토리지 계정 복제 방법을 변경해도 애플리케이션의 가동 중지 시간은 발생하지 않습니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 스토리지 계정에 대한 중복성 옵션을 변경하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 에서 **구성** 을 선택합니다.
1. **복제** 설정을 업데이트합니다.

    :::image type="content" source="media/redundancy-migration/change-replication-option.png" alt-text="포털에서 복제 옵션을 변경하는 방법을 보여주는 스크린샷." lightbox="media/redundancy-migration/change-replication-option.png":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정에 대한 중복성 옵션을 변경하려면 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 호출하고 `-SkuName` 매개 변수를 지정합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대한 중복성 옵션을 변경하려면 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 호출하고 `--sku` 매개 변수를 지정합니다.

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>ZRS, GZRS 또는 RA-GZRS로 수동 마이그레이션 수행

LRS에서 ZRS로 이동하거나 그 반대로 이동하여 주 지역에서 스토리지 계정의 데이터를 복제하는 방법을 변경하려면 수동 마이그레이션을 수행할 것을 선택할 수 있습니다. 수동 마이그레이션은 실시간 마이그레이션보다 더 유연합니다. 수동 마이그레이션의 시기를 조정하므로 특정 날짜까지 마이그레이션을 완료해야 하는 경우 이 옵션을 사용합니다.

주 지역의 LRS에서 ZRS로 수동 마이그레이션을 수행하거나 그 반대의 경우 대상 스토리지 계정은 지역 중복이 될 수 있고 보조 지역에 대한 읽기 권한을 위해 구성될 수도 있습니다. 예를 들어 LRS 계정을 GZRS 또는 RA-GZRS 계정으로 한 번에 마이그레이션할 수 있습니다.

수동 마이그레이션을 사용하면 결국 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 애플리케이션에 고가용성이 필요한 경우 Microsoft에서는 실시간 마이그레이션 옵션도 제공합니다. 실시간 마이그레이션은 가동 중지 시간이 없는 즉각적인 마이그레이션입니다.

수동 마이그레이션을 사용하면 기존 스토리지 계정의 데이터를 주 지역에서 ZRS를 사용하는 새 스토리지 계정으로 복사할 수 있습니다. 수동 마이그레이션을 수행하려면 다음 옵션 중 하나를 사용할 수 있습니다.

- AzCopy, Azure Storage 클라이언트 라이브러리 중 하나 또는 신뢰할 수 있는 타사 도구와 같은 기존 도구를 사용하여 데이터를 복사합니다.
- Hadoop 또는 HDInsight에 익숙한 경우 소스 스토리지 계정과 대상 스토리지 계정을 모두 클러스터에 연결할 수 있습니다. 그런 다음, DistCp와 같은 도구를 사용하여 데이터 복사 프로세스를 병렬 처리합니다.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>ZRS, GZRS 또는 RA-GZRS로 실시간 마이그레이션 요청

애플리케이션 가동 중지 시간 없이 주 지역의 LRS에서 ZRS로 스토리지 계정을 마이그레이션해야 하는 경우 Microsoft에서 실시간 마이그레이션을 요청할 수 있습니다. LRS에서 GZRS 또는 RA-GZRS로 마이그레이션하려면 먼저 GRS 또는 RA-GRS로 전환한 다음 실시간 마이그레이션을 요청합니다. 마찬가지로 GRS 또는 RA-GRS에서 GZRS 또는 RA-GZRS로 실시간 마이그레이션을 요청할 수 있습니다. GRS 또는 RA-GRS에서 ZRS로 마이그레이션하려면 먼저 LRS로 전환한 다음 실시간 마이그레이션을 요청합니다.

실시간 마이그레이션 중에는 내구성 또는 가용성 손실 없이 스토리지 계정의 데이터에 액세스할 수 있습니다. Azure Storage SLA는 마이그레이션 프로세스 중에 유지 관리됩니다. 실시간 마이그레이션과 관련된 데이터 손실은 없습니다. 서비스 엔드포인트, 액세스 키, 공유 액세스 서명, 기타 계정 옵션은 마이그레이션 후에도 변경되지 않습니다.

표준 성능의 경우 ZRS는 범용 v2 계정만 지원 하므로 실시간 마이그레이션에 대 한 요청을 ZRS로 제출 하기 전에 범용 v1 계정인 경우 저장소 계정을 업그레이드 해야 합니다. 자세한 내용은 [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)를 참조하세요. 스토리지 계정에는 실시간 마이그레이션을 통해 마이그레이션할 데이터가 포함되어야 합니다.

프리미엄 성능을 위해 실시간 마이그레이션은 프리미엄 파일 공유 계정에 대해서는 지원 되지만 프리미엄 블록 blob 또는 프리미엄 페이지 blob 계정에 대해서는 지원 되지 않습니다.

계정이 RA-GRS를 사용하는 경우 실시간 마이그레이션을 진행하기 전에 먼저 계정의 복제 유형을 LRS 또는 GRS로 변경해야 합니다. 이 중간 단계에서는 RA-GRS에서 제공한 보조 읽기 전용 엔드포인트가 제거됩니다.

Microsoft에서는 실시간 마이그레이션에 대한 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 특정 날짜에 ZRS로 마이그레이션된 데이터가 필요한 경우에는 대신 수동 마이그레이션을 수행하는 것이 좋습니다. 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 시간이 더 오래 걸립니다.

다음과 같은 경우 수동 마이그레이션을 수행해야 합니다.

- 소스 계정과 다른 지역에 있는 ZRS 스토리지 계정으로 데이터를 마이그레이션하려고 합니다.
- 스토리지 계정은 프리미엄 페이지 BLOB 또는 블록 BLOB 계정입니다.
- ZRS에서 LRS, GRS 또는 RA-GRS로 데이터를 마이그레이션하려고 합니다.
- 스토리지 계정에는 보관 계층의 데이터가 포함됩니다.

실시간 마이그레이션은 [Azure 지원 포털](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)을 통해 요청할 수 있습니다.

> [!IMPORTANT]
> 둘 이상의 스토리지 계정을 마이그레이션해야 하는 경우 단일 지원 티켓을 만들고 **세부 정보** 탭에서 변환할 계정의 이름을 지정합니다.

실시간 마이그레이션을 요청하려면 다음 단계를 수행합니다.

1. Azure Portal에서 마이그레이션할 스토리지 계정으로 이동합니다.
1. **지원 + 문제 해결** 아래의 **새 지원 요청** 을 선택합니다.
1. 사용자 계정 정보를 기반으로 **기본** 탭을 완료합니다.
    - **문제 유형**: **기술** 을 선택합니다.
    - **서비스**: **내 서비스** 를 선택한 다음 **스토리지 계정 관리** 를 선택합니다.
    - **리소스**: 마이그레이션할 스토리지 계정을 선택합니다. 여러 스토리지 계정을 지정해야 하는 경우 **세부 정보** 섹션에서 지정할 수 있습니다.
    - **문제 유형**: **데이터 마이그레이션** 을 선택합니다.
    - **문제 하위 유형**: **Migrate to ZRS, GZRS, or RA-GZRS(ZRS, GZRS 또는 RA-GZRS로 마이그레이션)** 를 선택합니다.

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="실시간 마이그레이션을 요청하는 방법을 보여 주는 스크린샷 - 기본 탭":::

1. **다음** 을 선택합니다. **솔루션** 탭에서 마이그레이션에 대한 스토리지 계정의 자격을 확인할 수 있습니다.
1. **다음** 을 선택합니다. 마이그레이션할 스토리지 계정이 둘 이상 있는 경우 **세부 정보** 탭에서 각 계정의 이름을 세미콜론으로 구분하여 지정합니다.

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="실시간 마이그레이션을 요청하는 방법을 보여 주는 스크린샷 - 세부 정보 탭":::

1. **세부 정보** 탭에 필요한 추가 정보를 입력한 다음 **검토 + 만들기** 를 선택하여 지원 티켓을 검토하고 제출합니다. 지원 담당자가 연락하여 필요한 모든 지원을 제공합니다.

> [!NOTE]
> 프리미엄 파일 공유는 LRS 및 ZRS에서만 사용할 수 있습니다.
>
> GZRS 스토리지 계정은 현재 보관 계층을 지원하지 않습니다. 자세한 내용은 [blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층](../blobs/access-tiers-overview.md) 을 참조 하세요.
>
> 관리 디스크는 LRS에만 사용할 수 있으며 ZRS로 마이그레이션할 수 없습니다. 표준 SSD 관리 디스크에 대한 스냅샷 및 이미지를 표준 HDD 스토리지에 저장할 수 있으며 [LRS와 ZRS 옵션 중 하나를 선택할 수 있습니다](https://azure.microsoft.com/pricing/details/managed-disks/). 가용성 집합과의 통합에 대한 자세한 내용은 [Azure 관리 디스크 소개](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)를 참조하세요.

## <a name="switch-from-zrs-classic"></a>ZRS 클래식에서 전환

> [!IMPORTANT]
> Microsoft에서는 2021년 3월 31일에 ZRS 클래식 계정의 사용을 중단하고 마이그레이션할 예정입니다. 사용이 중단되기 전에 ZRS 클래식 고객에게 자세한 내용이 제공됩니다.
>
> ZRS가 특정 지역에서 일반 공급되면 고객은 해당 지역의 Azure Portal에서 ZRS 클래식 계정을 더 이상 만들 수 없습니다. ZRS 클래식의 사용이 중단될 때까지 Microsoft PowerShell 및 Azure CLI를 사용하여 ZRS 클래식 계정을 만드는 것은 옵션입니다. ZRS를 사용할 수 있는 위치에 대한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.

ZRS 클래식은 1~2개의 지역 내의 데이터 센터에서 데이터를 비동기적으로 복제합니다. 복제된 데이터는 Microsoft가 보조 지역에 장애 조치(failover)를 시작하지 않는 한 사용할 수 없습니다. ZRS 클래식 계정을 LRS, GRS 또는 RA-GRS 계정으로 변환하거나, 이러한 계정에서 ZRS 클래식 계정으로 변환할 수 없습니다. 또한 ZRS 클래식 계정은 메트릭이나 로깅을 지원하지 않습니다.

ZRS 클래식은 GPv1(범용 V1) 스토리지 계정의 **블록 Blob** 에서만 사용할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

ZRS 계정 데이터를 LRS, GRS, RA-GRS, ZRS 클래식 계정과 수동으로 마이그레이션하려면 AzCopy, Azure Storage Explorer, PowerShell, Azure CLI와 같은 도구 중 하나를 사용합니다. Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 마이그레이션 솔루션을 빌드할 수도 있습니다.

ZRS를 사용할 수 있는 지역에서 Azure Portal, PowerShell, Azure CLI를 사용하여 ZRS 클래식 스토리지 계정을 ZRS로 업그레이드할 수도 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 ZRS로 업그레이드하려면 계정의 **구성** 설정으로 이동하여 **업그레이드** 를 선택합니다.

![포털에서 ZRS 클래식을 ZRS로 업그레이드](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 ZRS로 업그레이드하려면 다음 명령을 호출합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 ZRS로 업그레이드하려면 다음 명령을 호출합니다.

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>데이터 복제 방법 변경과 관련된 비용

데이터 복제 방법 변경과 관련된 비용은 변환 경로에 따라 달라집니다. Azure Storage 중복성 서비스에는 가장 저렴한 비용에서 가장 비싼 비용이 드는 순서로 LRS, ZRS, GRS, RA-GRS, GZRS, RA-GZRS가 포함됩니다.

예를 들어 LRS‘에서’ 다른 복제 유형으로 전환하면 보다 정교한 중복성 수준으로 이동하기 때문에 추가 비용이 발생합니다. GRS 또는 RA-GRS *로* 마이그레이션하면 전체 스토리지 계정이 보조 지역에 복제되므로 마이그레이션 시 송신 대역폭 요금이 발생합니다. 기본 지역에 대한 모든 후속 쓰기에는 보조 지역에 쓰기를 복제하기 위한 송신 대역폭 요금도 발생합니다. 대역폭 요금에 대한 자세한 내용은 [Azure Storage 가격 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

GRS에서 LRS로 스토리지 계정을 마이그레이션하는 경우 추가 비용은 없지만 복제된 데이터가 보조 위치에서 삭제됩니다.

> [!IMPORTANT]
> 스토리지 계정을 RA-GRS에서 GRS 또는 LRS로 마이그레이션하면 해당 계정은 변환된 날짜로부터 추가 30일에 대한 요금이 RA-GRS로 청구됩니다.

## <a name="see-also"></a>참고 항목

- [Azure Storage 중복성](storage-redundancy.md)
- [저장소 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)
- [지리적 중복성을 사용하여 고가용성 애플리케이션 설계](geo-redundant-design.md)
