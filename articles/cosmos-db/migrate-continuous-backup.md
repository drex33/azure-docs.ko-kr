---
title: 정기적 백업 모드에서 지속적인 백업 모드로 Azure Cosmos DB 계정 마이그레이션
description: Azure Cosmos DB는 현재 정기적 백업 모드에서 지속적인 백업 모드로의 단방향 마이그레이션을 지원하며 이 마이그레이션은 되돌릴 수 없습니다. 정기적 백업 모드에서 지속적인 백업 모드로 마이그레이션한 후에는 지속적인 모드의 이점을 활용할 수 있습니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/26/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: f9708a9287ee8ff40de6044249e35ad26b938316
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039309"
---
# <a name="migrate-an-azure-cosmos-db-account-from-periodic-to-continuous-backup-mode"></a>정기적 백업 모드에서 지속적인 백업 모드로 Azure Cosmos DB 계정 마이그레이션
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

정기적 모드 백업 정책을 사용하는 Azure CosmosDB 계정은 [Azure Portal](#portal), [CLI](#cli), [PowerShell](#powershell) 또는 [Resource Manager 템플릿](#ARM-template)을 사용하여 지속적인 모드로 마이그레이션할 수 있습니다. 정기적 모드에서 지속적인 모드로의 마이그레이션은 단방향 마이그레이션이며 되돌릴 수 없습니다. 정기적 백업 모드에서 지속적인 백업 모드로 마이그레이션한 후에는 지속적인 모드의 이점을 활용할 수 있습니다.

지속적인 모드로 마이그레이션하는 주요 이유는 다음과 같습니다.

* Azure Portal, CLI 또는 PowerShell을 사용하여 셀프 서비스 복원을 수행할 수 있습니다.
* 마지막 30일 기간 내에 초 단위로 복원할 수 있습니다.
* 백업이 일정 기간 내에 분할된 데이터베이스 또는 파티션 키 범위에서 일관되는지 확인할 수 있습니다.
* 컨테이너, 데이터베이스 또는 전체 계정이 삭제되거나 수정된 경우 복원할 수 있습니다.
* 컨테이너, 데이터베이스 또는 계정에 대한 이벤트를 선택하고 복원의 시작 시기를 결정할 수 있습니다.

> [!NOTE]
> 마이그레이션 기능은 단방향이며 되돌릴 수 없는 동작입니다. 즉, 정기적 모드에서 지속적인 모드로 마이그레이션한 후에는 정기적 모드로 다시 전환할 수 없습니다.
>
> 다음 조건에 해당되는 경우에만 계정을 지속적인 백업 모드로 마이그레이션할 수 있습니다.
>
> * 계정이 SQL API 또는 MongoDB용 API 유형인 경우
> * 계정에 단일 쓰기 지역이 있는 경우
> * 계정이 CMK(고객 관리형 키)로 사용하도록 설정되지 않은 경우
> * 계정이 분석 저장소에서 사용하도록 설정되지 않은 경우

## <a name="permissions"></a>사용 권한

마이그레이션을 수행하려면 마이그레이션되는 계정에 대한 `Microsoft.DocumentDB/databaseAccounts/write` 권한이 필요합니다.  

## <a name="pricing-after-migration"></a>마이그레이션 후 가격 책정

계정을 지속적인 백업 모드로 마이그레이션한 후에는 이 모드 사용 비용이 정기적 백업 모드와 비교했을 때 차이가 있습니다. 지속적인 모드 백업 비용은 정기적 모드보다 훨씬 저렴합니다. 자세히 알아보려면 [지속적인 백업 모드 가격 책정](continuous-backup-restore-introduction.md#continuous-backup-pricing) 예제를 참조하세요.

## <a name="migrate-using-portal"></a><a id="portal"></a> Portal을 사용하여 마이그레이션

다음 단계를 사용하여 정기적 백업에서 지속적인 백업 모드로 계정을 마이그레이션합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Cosmos DB 계정으로 이동하여 **기능** 창을 엽니다. **지속적인 백업** 을 선택하고 **사용** 을 선택합니다.

   :::image type="content" source="./media/migrate-continuous-backup/enable-backup-migration.png" alt-text="Azure Portal을 사용하여 지속적인 모드로 마이그레이션" lightbox="./media/migrate-continuous-backup/enable-backup-migration.png":::

1. 마이그레이션이 진행 중인 경우 상태에 **보류 중** 이 표시됩니다. 완료되면 상태가 **켜짐** 으로 변경됩니다. 마이그레이션 시간은 계정에 있는 데이터의 크기에 따라 달라집니다.

   :::image type="content" source="./media/migrate-continuous-backup/migration-status.png" alt-text="Azure Portal에서 마이그레이션 상태 확인" lightbox="./media/migrate-continuous-backup/migration-status.png":::

## <a name="migrate-using-powershell"></a><a id="powershell"></a>Powershell을 사용하여 마이그레이션

[최신 버전의 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) 또는 6.2.0 이상 버전을 설치합니다. 그런 후 다음 단계를 실행합니다.

1. Azure 계정에 연결합니다.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

1. 계정을 정기적 백업 모드에서 지속적인 백업 모드로 마이그레이션합니다.

   ```azurepowershell-interactive
   Update-AzCosmosDBAccount ` 
     -ResourceGroupName "myrg" ` 
     -Name "myAccount" `
     -BackupPolicyType Continuous
   ```

## <a name="migrate-using-cli"></a><a id="cli"></a>CLI를 사용하여 마이그레이션

1. 최신 버전의 Azure CLI를 설치합니다.

   * CLI가 없는 경우 최신 버전의 Azure CLI 또는 2.26.0 이상 버전을 [설치합니다](/cli/azure/).
   * Azure CLI가 이미 설치되어 있는 경우 `az upgrade` 명령을 사용하여 최신 버전으로 업그레이드합니다.
   * 또는 Azure Portal에서 Cloud Shell을 사용할 수도 있습니다.

1. Azure 계정에 로그인하고 다음 명령을 실행하여 계정을 지속적인 모드로 마이그레이션합니다.

   ```azurecli-interactive
   az login

   az cosmosdb update -n <myaccount> -g <myresourcegroup> --backup-policy-type continuous
   ```

1. 마이그레이션이 성공적으로 완료되면 출력은 backupPolicy 개체의 type 속성이 Continuous로 설정되었다고 표시합니다.

   ```console
    {
      "apiProperties": null,
      "backupPolicy": {
        "type": "Continuous"
      },
      "capabilities": [],
      "connectorOffer": null,
      "consistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxIntervalInSeconds": 5,
        "maxStalenessPrefix": 100
      },
    …
    }
   ```

## <a name="migrate-using-resource-manager-template"></a><a id="ARM-template"></a> Resource Manager 템플릿을 사용하여 마이그레이션

ARM 템플릿을 사용하여 지속적인 백업 모드로 마이그레이션하려면 템플릿의 backupPolicy 섹션을 찾아 `type` 속성을 업데이트합니다. 예를 들어 기존 템플릿에 다음 JSON 개체와 같은 백업 정책이 있는 경우

```json
"backupPolicy": {
   "type": "Periodic",
   "periodicModeProperties": {
   "backupIntervalInMinutes": 240,
   "backupRetentionIntervalInHours": 8
   }
},
```

다음 JSON 개체로 바꿉니다.

```json
"backupPolicy": {
   "type": "Continuous"
},
```

다음으로 Azure PowerShell 또는 CLI를 사용하여 템플릿을 배포합니다. 다음 예제에서는 CLI 명령을 사용하여 템플릿을 배포하는 방법을 보여줍니다.

```azurecli
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="what-to-expect-during-and-after-migration"></a>마이그레이션 도중 및 후에는 어떻게 되나요?

정기적 모드에서 지속적인 모드로 마이그레이션하는 경우 계정 수준 업데이트 또는 삭제를 수행하는 컨트롤 플레인 작업을 실행할 수 없습니다. 예를 들어, 마이그레이션을 진행하는 동안에는 지역 추가 또는 제거, 계정 장애 조치(failover), 백업 정책 업데이트 등의 작업을 실행할 수 없습니다. 마이그레이션 시간은 계정에 있는 데이터의 크기 및 지역 수에 따라 달라집니다. 마이그레이션된 계정에 대한 복원 작업은 마이그레이션이 성공적으로 완료된 이후부터만 성공합니다.

마이그레이션이 완료된 후 계정을 복원할 수 있습니다. 마이그레이션이 오후 1시(PST)에 완료되는 경우 오후 1시(PST)부터 지정 시간 복원을 수행할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

#### <a name="does-the-migration-only-happen-at-the-account-level"></a>마이그레이션은 계정 수준에서만 발생하나요?
예.

#### <a name="which-accounts-can-be-targeted-for-backup-migration"></a>어떤 계정이 백업 마이그레이션의 대상이 될 수 있나요?
현재는 공유, 프로비저닝 또는 자동 스케일링 프로비저닝 처리량이 있는 단일 쓰기 지역의 SQL API 및 MongoDB용 API 계정이 마이그레이션을 지원합니다.

분석 스토리지, 다중 쓰기 지역 및 CMK(고객 관리형 키)가 사용하도록 설정된 계정은 마이그레이션이 지원됩니다.

#### <a name="does-the-migration-take-time-what-is-the-typical-time"></a>마이그레이션에 시간이 걸리나요? 일반적인 시간은 언제인가요?
마이그레이션은 시간이 걸리고 사용자 계정에 있는 데이터의 크기와 지역의 수에 따라 달라집니다. Azure CLI 또는 PowerShell 명령을 사용하여 마이그레이션 상태를 가져올 수 있습니다. 수십 테라바이트 데이터가 있는 대규모 계정의 경우 마이그레이션을 완료하는 데 며칠까지 걸릴 수 있습니다.

#### <a name="does-the-migration-cause-any-availability-impactdowntime"></a>마이그레이션이 가용성 영향/가동 중지 시간을 유발하나요?
아니요, 마이그레이션 작업은 백그라운드에서 수행되므로 클라이언트 요청에 영향을 주지 않습니다. 그러나 마이그레이션하는 동안 몇 가지 백 엔드 작업을 수행해야 하며 계정에 부하가 많은 경우에는 추가 시간이 소요될 수 있습니다.

#### <a name="what-happens-if-the-migration-fails-will-i-still-get-the-periodic-backups-or-get-the-continuous-backups"></a>마이그레이션이 실패하면 어떻게 되나요? 정기적 백업 또는 지속적인 백업 중에서 어떤 백업이 진행되나요?
마이그레이션 프로세스가 시작되면 계정이 지속적인 모드가 되기 시작합니다.  마이그레이션이 실패하면 성공할 때까지 마이그레이션을 다시 시작해야 합니다.

#### <a name="how-do-i-perform-a-restore-to-a-timestamp-beforeduringafter-the-migration"></a>마이그레이션 전/도중/이후의 타임스탬프로 복원하려면 어떻게 하나요?
t1에서 마이그레이션을 시작하고 t5에서 완료했다고 가정할 경우 t1과 t5 사이의 복원 타임스탬프를 사용할 수 없습니다.

계정이 이제 지속적인 모드이기 때문에 t5 이후의 시간으로 복원하려면, 일반적으로 지속적인 계정으로 수행하는 것처럼 Azure Portal, CLI 또는 PowerShell을 사용하여 복원을 수행할 수 있습니다. 이 셀프 서비스 복원 요청은 마이그레이션이 완료된 후에만 수행할 수 있습니다.

t1 이전 시간으로 복원하려면 일반적으로 정기적 백업 계정으로 수행하는 것처럼 지원 티켓을 열 수 있습니다. 마이그레이션 후 주기적 복원을 수행하는 데 최대 30일의 여유 기간이 있습니다.  이러한 30일 동안에는 마이그레이션 전에 계정의 백업 보존/간격에 따라 복원할 수 있습니다.  예를 들어, 백업 구성이 1시간 간격으로 24개의 복사본을 유지해야 하는 경우 [t1 – 24시간]과 [t1] 사이에 언제로든 복원할 수 있습니다.

#### <a name="which-account-level-control-plane-operations-are-blocked-during-migration"></a>마이그레이션하는 동안 차단되는 계정 수준 컨트롤 플레인 작업은 무엇인가요?
지역 추가/제거, 장애 조치, 복제, 백업 정책 변경, 처리량 변경으로 인한 데이터 이동 등의 작업은 마이그레이션하는 동안 차단됩니다.

#### <a name="if-the-migration-fails-for-some-underlying-issue-would-it-still-block-the-control-plane-operation-until-it-is-retried-and-completed-successfully"></a>기본적인 일부 문제로 인해 마이그레이션이 실패하는 경우 다시 시도한 후 성공적으로 완료될 때까지 컨트롤 플레인 작업이 계속 차단되나요?
실패한 마이그레이션은 컨트롤 플레인 작업을 차단하지 않습니다. 마이그레이션이 실패하면 다른 컨트롤 플레인 작업을 수행하기 전에 성공할 때까지 다시 시도하는 것이 좋습니다.

#### <a name="is-it-possible-to-cancel-the-migration"></a>마이그레이션을 취소할 수 있나요?
되돌릴 수 있는 작업이 아니므로 마이그레이션을 취소할 수 없습니다.

#### <a name="is-there-a-tool-that-can-help-estimate-migration-time-based-on-the-data-usage-and-number-of-regions"></a>데이터 사용량 및 지역 수를 기준으로 마이그레이션 시간을 예측하는 데 도움이 되는 도구가 있나요?
시간을 예측하는 도구는 없습니다. 그러나 스케일링 실행은 1TB의 데이터가 있는 단일 지역에서 약 1시간 30분이 소요됨을 나타냅니다.

다중 지역 계정의 경우 총 데이터 크기를 `Number_of_regions * Data_in_single_region`으로 계산합니다.

#### <a name="since-the-continuous-backup-mode-is-now-ga-would-you-still-recommend-restoring-a-copy-of-your-account-and-try-migration-on-the-copy-before-deciding-to-migrate-the-production-account"></a>이제 지속적인 백업 모드가 GA됩니다. 그래도 프로덕션 계정을 마이그레이션하도록 결정하기 전에 계정 복사본을 복원하고 복사본에 대해 마이그레이션을 시도하는 것이 권장되나요?
프로덕션 계정을 마이그레이션하기 전에 지속적인 백업 모드 기능을 테스트하여 예상대로 작동하는지 확인하는 것이 좋습니다. 마이그레이션은 단방향 작업이며 되돌릴 수 없습니다.

## <a name="next-steps"></a>다음 단계

지속적인 백업 모드에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [지정 시간 복원을 사용하는 지속적인 백업 모드 소개](continuous-backup-restore-introduction.md)

* [지속적인 백업 모드 리소스 모델](continuous-backup-restore-resource-model.md)

* [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), [CLI](restore-account-continuous-backup.md#restore-account-cli) 또는 [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)를 사용하여 계정을 복원합니다.

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
   * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어 보세요. 
   * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
