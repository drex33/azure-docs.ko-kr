---
title: 정기 백업을 사용하여 Azure Cosmos DB 계정 구성
description: 이 문서에서는 백업 간격과 보존 기간이 지정된 정기 백업을 사용하여 Azure Cosmos DB 계정을 구성하는 방법을 설명합니다. 데이터 복원을 위해 고객 지원팀에 연락하는 방법도 설명합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/16/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ac901e375c5d3d5dd2b5c1a67a161bd564dcab1e
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440413"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>정기 백업을 사용하여 Azure Cosmos DB 계정 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 자동으로 데이터의 백업을 정기적으로 수행합니다. 자동 백업은 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. 모든 백업은 스토리지 서비스에서 개별적으로 저장되고 이러한 백업은 지역 재해에 대비한 복원을 위해 전역적으로 복제됩니다. Azure Cosmos DB를 사용하면 데이터뿐만 아니라 데이터의 백업도 지역 재해에 대해 중복성 및 복원력이 뛰어납니다. 다음 단계에서는 Azure Cosmos DB가 데이터 백업을 수행하는 방법을 보여줍니다.

* Azure Cosmos DB는 4시간마다 데이터베이스의 전체 백업을 자동으로 수행하며 기본적으로 항상 2개의 최신 백업만 저장됩니다. 기본 간격이 워크로드에 충분하지 않은 경우 Azure Portal에서 백업 간격과 보존 기간을 변경할 수 있습니다. Azure Cosmos 계정을 만드는 동안 또는 만든 후에 백업 구성을 변경할 수 있습니다. 컨테이너나 데이터베이스가 삭제되면 Azure Cosmos DB는 지정된 컨테이너나 데이터베이스의 기존 스냅샷을 30일간 유지합니다.

* Azure Cosmos DB에서 실제 데이터는 Azure Cosmos DB 내에 로컬로 상주하는 반면, 이러한 백업은 Azure Blob Storage에 저장됩니다.

* 낮은 대기 시간을 보장하기 위해 백업 스냅샷이 현재 쓰기 지역(또는 다중 지역 쓰기 구성이 있는 경우 쓰기 지역 중 **한 곳**)과 동일한 지역에 있는 Azure Blob Storage에 저장됩니다. 지역적 재해에 대비한 복구를 위해 Azure Blob Storage에 있는 백업 데이터의 각 스냅샷은 GRS(지역 중복 스토리지)를 통해 다른 지역으로 다시 복제됩니다. 백업이 복제되는 지역은 원본 지역 및 원본 지역에 연결된 지역 쌍을 기반으로 합니다. 자세한 내용은 [Azure 지역의 지역 중복 쌍 목록](../availability-zones/cross-region-replication-azure.md) 문서를 참조하세요. 이 백업에 직접 액세스할 수 없습니다. Azure Cosmos DB 팀은 지원 요청을 통해 요청하는 경우 백업을 복원합니다.

  다음 이미지는 미국 서부의 기본 실제 파티션 3개를 모두 포함하는 Azure Cosmos 컨테이너가 미국 서부의 원격 Azure Blob Storage 계정에 백업된 다음, 미국 동부에 복제되는 과정을 보여줍니다.

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="GRS Azure Storage에 있는 모든 Cosmos DB 엔터티의 정기적인 전체 백업" lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* 백업은 애플리케이션의 성능이나 가용성에 영향을 주지 않고 수행됩니다. Azure Cosmos DB는 추가로 프로비전된 처리량(RU)을 사용하지 않으며 데이터베이스의 성능 및 가용성에 영향을 주지 않고 백그라운드에서 데이터 백업을 수행합니다.

> [!Note]
> Azure Synapse Link 사용 계정의 경우 분석 저장소 데이터는 백업 및 복원에 포함되지 않습니다. Synapse Link를 사용하도록 설정하면 Azure Cosmos DB는 예약된 백업 간격에 따라 컨테이너의 트랜잭션 저장소에 있는 데이터를 자동으로 계속 백업합니다. 현재, 분석 저장소의 데이터 자동 백업 및 복원이 지원되지 않습니다.

## <a name="backup-storage-redundancy"></a><a id="backup-storage-redundancy"></a>백업 스토리지 중복성

기본적으로 Azure Cosmos DB는 [쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md)으로 복제되는 지역 중복 [Blob Storage](../storage/common/storage-redundancy.md)에 정기 모드 백업 데이터를 저장합니다. Azure PowerShell 또는 CLI를 사용 하 여이 기본값을 업데이트 하 고 Azure 정책을 정의 하 여 특정 저장소 중복성 옵션을 적용할 수 있습니다. 자세히 알아보려면 [백업 저장소 중복성 업데이트](update-backup-storage-redundancy.md) 문서를 참조 하세요.

백업 데이터가 Azure Cosmos DB 계정이 프로비전된 동일한 지역 내에 유지되도록 하려면 기본 지역 중복 백업 스토리지를 변경하고 로컬 중복 또는 영역 중복 스토리지를 구성할 수 있습니다. 스토리지 중복성 메커니즘은 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되지 않은 이벤트로부터 백업을 보호하기 위해 여러 복사본을 저장합니다.

계정을 만들 때 정기 백업 모드에 대한 스토리지 중복을 구성하거나 기존 계정에 대한 스토리지 중복을 업데이트할 수 있습니다. 정기 백업 모드에서 다음 3가지 데이터 중복 옵션을 사용할 수 있습니다.

* **지역 중복 백업 스토리지:** 이 옵션은 쌍을 이루는 지역 간에 데이터를 비동기적으로 복사합니다.

* **영역 중복 백업 저장소:** 이 옵션은 주 지역의 세 Azure 가용성 영역에 걸쳐 동기식으로 데이터를 복사 합니다. 자세한 내용은 [영역 중복 저장소](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) 를 참조 하세요.

* **로컬 중복 백업 저장소:** 이 옵션은 주 지역의 단일 물리적 위치 내에서 데이터를 동기적으로 세 번 복사 합니다. 자세한 내용은 [로컬 중복 저장소](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) 를 참조 하세요.

> [!NOTE]
> 영역 중복 스토리지는 현재 [특정 지역](high-availability.md#availability-zone-support)에서만 사용할 수 있습니다. 새 계정에 대해 선택한 지역 또는 기존 계정에 대해 보유한 지역에 따라 영역 중복 옵션을 사용할 수 없습니다.
>
> 백업 스토리지 중복성을 업데이트해도 백업 스토리지 가격 책정에는 영향이 없습니다.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>백업 간격 및 보존 기간 수정

Azure Cosmos DB는 4시간마다 자동으로 데이터의 전체 백업을 수행하고 항상 2개의 최신 백업이 저장됩니다. 이 구성은 기본 옵션이며 추가 비용 없이 제공됩니다. Azure Cosmos 계정을 만드는 중이거나 계정을 만든 후에 이러한 기본 백업 간격과 보존 기간을 변경할 수 있습니다. 백업 구성은 Azure Cosmos 계정 수준에서 설정되며, 각 계정에 대해 구성해야 합니다. 계정에 대한 백업 옵션을 구성한 후에는 해당 계정 내의 모든 컨테이너에 적용됩니다. 현재는 Azure Portal에서만 백업 옵션을 변경할 수 있습니다.

데이터를 실수로 삭제하거나 손상한 경우 **데이터 복원에 대한 지원 요청을 만들기 전에 계정의 백업 보존 기간을 7일 이상으로 늘려야 합니다. 이 이벤트의 8시간 이내에 보존 기간을 늘리는 것이 가장 좋습니다.** 이렇게 하면 Azure Cosmos DB 팀이 계정을 복원할 충분한 시간을 갖게 됩니다.

### <a name="modify-backup-options-for-an-existing-account"></a>기존 계정에 대한 백업 옵션 수정

다음 단계를 사용하여 기존 Azure Cosmos 계정에 대한 기본 백업 옵션을 변경합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Cosmos 계정으로 이동하여 **백업 및 복원** 창을 엽니다. 필요에 따라 백업 간격과 백업 보존 기간을 업데이트합니다.

   * **백업 간격** - Azure Cosmos DB가 데이터 백업을 시도하는 간격입니다. 백업은 0이 아닌 시간이 소요되며 경우에 따라 다운스트림 종속성으로 인해 잠재적으로 실패할 수 있습니다. Azure Cosmos DB는 구성된 간격으로 백업을 수행하기 위해 최선을 다하지만 해당 시간 간격 내에 백업이 완료된다는 보장은 없습니다. 이 값은 시간 또는 분 단위로 구성할 수 있습니다. 백업 간격은 1~24시간입니다. 이 간격을 변경하면 마지막 백업이 수행된 시간부터 새 간격이 적용됩니다.

   * **백업 보존** - 각 백업이 보존되는 기간을 나타냅니다. 시간 또는 일 단위로 구성할 수 있습니다. 최소 보존 기간은 백업 간격(시간)의 2배 이상, 720시간 이하여야 합니다.

   * **보존된 데이터 복사본** - 기본적으로 데이터의 백업 복사본 2개가 무료로 제공됩니다. 복사본이 2개보다 많이 필요한 경우 추가 요금이 부과됩니다. 추가 복사본의 정확한 가격을 알아보려면 [가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/) 페이지에서 사용된 스토리지 섹션을 참조하세요.

   * **백업 스토리지 중복성** -필요한 스토리지 중복성 옵션을 선택합니다. 사용 가능한 옵션은 [백업 스토리지 중복성](#backup-storage-redundancy) 섹션을 참조하세요. 기본적으로 기존 정기 백업 모드 계정에는 계정이 프로 비전 되는 지역에서 지원할 경우 지역 중복 저장소가 있습니다. 그렇지 않으면 계정에서 사용 가능한 가장 높은 중복성 옵션으로 대체 합니다. 백업이 다른 지역으로 복제되지 않도록 로컬 중복 등의 다른 스토리지를 선택할 수 있습니다. 기존 계정의 변경 내용은 이후 백업에만 적용됩니다. 기존 계정의 백업 스토리지 중복성이 업데이트된 후에는 변경 내용이 적용되는 데 백업 간격 시간을 2배까지 걸릴 수 있으며 **이전 백업을 즉시 복원하는 액세스 권한을 잃게 됩니다.**

   > [!NOTE]
   > 백업 저장소 중복성을 구성 하려면 구독 수준에서 Azure [Cosmos DB Operator 역할](../role-based-access-control/built-in-roles.md#cosmos-db-operator) 역할이 할당 되어야 합니다.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-existing-accounts.png" alt-text="기존 Azure Cosmos 계정에 대한 백업 간격, 보존 및 스토리지 중복성을 구성합니다." border="true":::

### <a name="modify-backup-options-for-a-new-account"></a>새 계정에 대한 백업 옵션 수정

새 계정을 프로비저닝할 때 **백업 정책** 탭에서 **Periodic(정기)** _ 백업 정책을 선택합니다. 정기 정책을 사용하면 백업 간격, 백업 보존 및 백업 스토리지 중복성을 구성할 수 있습니다. 예를 들어 *_로컬 중복 백업 스토리지** 또는 **영역 중복 백업 스토리지** 옵션을 선택하여 지역 외부에서 백업 데이터 복제를 방지할 수 있습니다.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-new-accounts.png" alt-text="새 Azure Cosmos 계정에 대한 정기 또는 지속적인 백업 정책을 구성합니다." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>백업에서 데이터 복원 요청

데이터베이스 또는 컨테이너를 실수로 삭제한 경우 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하거나 [Azure 지원에 문의](https://azure.microsoft.com/support/options/)하여 자동 온라인 백업에서 데이터를 복원할 수 있습니다. Azure 지원은 **Standard**, **Developer** 및 상위 플랜과 같은 지정된 플랜에만 사용할 수 있습니다. Azure 지원은 **Basic** 플랜에는 사용할 수 없습니다. 여러 지원 플랜에 대해 자세히 알아보려면 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 페이지를 참조하세요.

백업의 특정 스냅샷을 복원하려면 Azure Cosmos DB에서 해당 스냅샷의 백업 주기 동안 데이터를 사용할 수 있어야 합니다.
복원을 요청하려면 다음 세부 정보가 필요합니다.

* 구독 ID를 준비합니다.

* 데이터가 어떻게 실수로 삭제되거나 수정되었는지에 따라 추가 정보를 준비해야 합니다. 일부 시간에 구애 받는 경우 불리할 수 있는 전후 과정을 최소화하기 위해 사전에 사용 가능한 정보를 확보하는 것이 좋습니다.

* 전체 Azure Cosmos DB 계정이 삭제된 경우 삭제된 계정의 이름을 제공해야 합니다. 삭제된 계정과 동일한 이름을 가진 다른 계정을 만들 경우 올바른 계정을 선택하는 데 도움이 되므로 이를 지원팀과 공유하세요. 복원 상태의 혼란을 최소화하기 위해 삭제된 각 계정에 대한 다양한 지원 티켓을 제출하는 것이 좋습니다.

* 하나 이상의 데이터베이스가 삭제된 경우 Azure Cosmos 계정과 Azure Cosmos 데이터베이스 이름을 제공하고 동일한 이름의 새 데이터베이스가 있는지 여부를 지정해야 합니다.

* 하나 이상의 컨테이너가 삭제된 경우 Azure Cosmos 계정 이름, 데이터베이스 이름 및 컨테이너 이름을 제공해야 합니다. 또한 같은 이름의 컨테이너가 존재하는지 여부를 지정합니다.

* 실수로 데이터가 삭제되거나 손상된 경우 Azure Cosmos DB 팀이 백업에서 데이터를 복구하는 데 도움을 드릴 수 있도록 8시간 이내에 [Azure 지원](https://azure.microsoft.com/support/options/)에 연락해야 합니다. **데이터 복원에 대한 지원 요청을 만들기 전에 계정의 [백업 보존 기간을 7일 이상으로 늘려야](#configure-backup-interval-retention) 합니다. 이 이벤트의 8시간 이내에 보존 기간을 늘리는 것이 가장 좋습니다.** 이렇게 하면 Azure Cosmos DB 지원 팀이 계정을 복원할 시간이 충분합니다.

Azure Cosmos 계정 이름, 데이터베이스 이름, 컨테이너 이름 외에도 데이터를 복원할 수 있는 시점을 지정해야 합니다. 가능한 한 정확하게 해야 해당 시점에 가장 적합한 백업을 결정할 수 있습니다. **또한 시간을 UTC로 지정하는 것도 중요합니다.**

다음 스크린샷은 Azure Portal을 사용하여 데이터를 복원하기 위해 컨테이너(컬렉션/그래프/테이블)에 대한 지원 요청을 만드는 방법을 보여줍니다. 요청의 우선 순위를 지정하는 데 도움이 되도록 데이터의 유형, 복원의 목적, 데이터가 삭제된 시간 등의 다른 정보를 제공합니다.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Azure Portal을 사용하여 백업 지원 요청 만들기" border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>백업에서 데이터를 복원하기 위한 고려 사항

다음 시나리오 중 하나에서 실수로 데이터를 삭제하거나 수정할 수 있습니다.  

* 전체 Azure Cosmos 계정을 삭제합니다.

* 하나 이상의 Azure Cosmos 데이터베이스를 삭제합니다.

* 하나 이상의 Azure Cosmos 컨테이너를 삭제합니다.

* 컨테이너 내 Azure Cosmos 항목(예: 문서)을 삭제하거나 수정합니다. 이 특정 사례를 일반적으로 데이터 손상이라고 합니다.

* 공유 제품 데이터베이스나 공유 제품 데이터베이스 내 컨테이너가 삭제되거나 손상됩니다.

Azure Cosmos DB는 위의 모든 시나리오에서 데이터를 복원할 수 있습니다. 복원할 때 복원된 데이터를 보관하기 위해 새 Azure Cosmos 계정이 만들어집니다. 새 계정의 이름이 지정되지 않은 경우 `<Azure_Cosmos_account_original_name>-restored1` 형식이 됩니다. 복원을 여러 번 시도하면 마지막 숫자가 증분됩니다. 미리 생성된 Azure Cosmos 계정으로는 데이터를 복원할 수 없습니다.

Azure Cosmos 계정을 실수로 삭제하는 경우 계정 이름이 사용 중이 아니라면 동일한 이름의 새 계정으로 데이터를 복원할 수 있습니다. 따라서 계정을 삭제한 후에는 계정을 다시 만들지 않는 것이 좋습니다. 이는 복원된 데이터가 동일한 이름을 사용하는 것을 방지할 뿐만 아니라 복원할 올바른 계정을 찾기 어려워지기 때문입니다.

Azure Cosmos 데이터베이스를 실수로 삭제하는 경우 전체 데이터베이스 또는 해당 데이터베이스 내 컨테이너의 하위 집합을 복원할 수 있습니다. 또한 데이터베이스에서 특정 컨테이너를 선택하고 새 Azure Cosmos 계정으로 복원할 수 있습니다.

컨테이너 내에서 하나 이상의 항목을 실수로 삭제하거나 수정하는 경우(데이터 손상 사례) 복원할 시간을 지정해야 합니다. 데이터 손상 시 시간이 중요합니다. 컨테이너는 실시간이기 때문에 백업은 여전히 실행 중이므로 보존 기간(기본값은 8시간) 이상 대기하는 경우 백업은 덮어쓰기됩니다. 백업을 덮어쓰지 않도록 하려면 계정의 백업 보존 기간을 최소 7일로 늘립니다. 데이터 손상으로부터 8시간 내에 보존 기간을 늘리는 것이 가장 좋습니다.

실수로 데이터가 삭제되거나 손상된 경우 Azure Cosmos DB 팀이 백업에서 데이터를 복구하는 데 도움을 드릴 수 있도록 8시간 이내에 [Azure 지원](https://azure.microsoft.com/support/options/)에 연락해야 합니다. 이렇게 하면 Azure Cosmos DB 지원 팀이 계정을 복원할 시간이 충분합니다.

> [!NOTE]
> 데이터를 복원한 후에는 모든 원본 기능 또는 설정이 복원된 계정으로 넘어가지 않습니다. 다음 설정은 새 계정으로 넘어가지 않습니다.
> * VNET 액세스 제어 목록
> * 저장 프로시저, 트리거 및 사용자 정의 함수
> * 다중 지역 설정  

데이터베이스 수준에서 처리량을 프로비전하는 경우 이 경우의 백업 및 복원 프로세스는 개별 컨테이너 수준이 아닌 전체 데이터베이스 수준에서 수행됩니다. 이러한 경우 복원할 컨테이너의 하위 집합을 선택할 수 없습니다.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>포털에서 보존 또는 복원을 변경하는 데 필요한 권한
[CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), 소유자 또는 기여자 역할의 일부인 주체는 복원을 요청하거나 보존 기간을 변경할 수 있습니다.

## <a name="understanding-costs-of-extra-backups"></a>추가 백업의 비용 이해
2개의 백업이 무료로 제공되며 [백업 스토리지 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)에 설명된 백업 스토리지에 대한 지역 기반 가격 책정에 따라 추가 백업 요금이 청구됩니다. 백업 보존이 240시간, 즉 10일로 구성되고 백업 간격이 24시간으로 구성된 경우를 예로 들겠습니다. 이는 백업 데이터의 복사본 10개를 의미합니다. 미국 서부 2에 1TB의 데이터가 있다고 가정하면 해당 월의 백업 스토리지 비용은 0.12 * 1000 * 8이 됩니다.

## <a name="options-to-manage-your-own-backups"></a>사용자 고유의 백업을 관리하기 위한 옵션

Azure Cosmos DB SQL API 계정을 통해 다음 방법 중 하나를 사용하면 백업을 직접 유지할 수도 있습니다.

* [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)를 사용하여 데이터를 주기적으로 선택한 스토리지로 이동합니다.

* Azure Cosmos DB [변경 피드](change-feed.md)를 사용하여 증분 변경 또는 전체 백업에 대한 데이터를 정기적으로 읽고, 사용자 고유의 스토리지에 저장합니다.

## <a name="post-restore-actions"></a>복원 후 작업

데이터 복원의 주 목표는 실수로 삭제하거나 수정한 데이터를 복구하는 것입니다. 따라서 예상하는 것을 포함하도록 하려면 복구된 데이터의 콘텐츠를 먼저 검사하는 것이 좋습니다. 모두 정상이면 데이터를 다시 기본 계정으로 마이그레이션할 수 있습니다. 복원된 계정을 새 활성 계정으로 사용할 수 있지만, 프로덕션 워크로드가 있는 경우에는 권장되지 않습니다. 

데이터를 복원한 후 새 계정 이름(일반적으로 `<original-name>-restored1` 형식임) 및 계정이 복구된 시간에 대한 알림을 받습니다. 복원된 계정은 프로비저닝된 처리량과 인덱싱 정책이 동일하며 원래 계정과 동일한 영역에 있습니다. 구독 관리자 또는 공동 관리자인 사용자는 복원된 계정을 볼 수 있습니다.

### <a name="migrate-data-to-the-original-account"></a>원래 계정으로 데이터 마이그레이션

다음은 원래 계정으로 다시 데이터를 마이그레이션하는 다양한 방법입니다.

* [Azure Cosmos DB 데이터 마이그레이션 도구](import-data.md)를 사용합니다.
* [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)를 사용합니다.
* Azure Cosmos DB에서 [변경 피드](change-feed.md)를 사용합니다.
* 사용자 지정 코드를 직접 작성합니다.

데이터를 마이그레이션하는 즉시 컨테이너 또는 데이터베이스를 삭제하는 것이 좋습니다. 복원된 데이터베이스 또는 컨테이너를 삭제하지 않으면 요청 단위, 스토리지 및 송신 관련 비용이 발생합니다.

## <a name="next-steps"></a>다음 단계

* 복원 요청을 수행하려면 Azure 지원에 문의하여 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
* [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli) 또는 [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)를 사용하여 지속적인 백업을 프로비저닝합니다.
* [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), [CLI](restore-account-continuous-backup.md#restore-account-cli) 또는 [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)를 사용하여 지속적인 백업 계정을 복원합니다.
* [정기 백업에서 연속 백업으로 계정에 마이그레이션](migrate-continuous-backup.md)합니다.
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md)합니다.
