---
title: Azure Backup 자동화
description: Azure Backup 제공하는 자동화 기능에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 11/16/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: a836f7e92c5de839ac093816e5935b08a8283e92
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716736"
---
# <a name="automation-in-azure-backup"></a>Azure Backup 자동화

Azure에서 작업을 관리할 때 새로운 다양한 워크로드 세트/규모 확장을 포함하여 백업 자산이 확장될 수 있습니다.

소규모에서는 백업할 올바른 컴퓨터를 식별하고, 백업을 구성하고, 상태를 모니터링하고, 데이터를 추출하는 등의 작업을 수동으로 수행하거나 쉽게 수행할 수 있습니다. 그러나 대규모에서는 이러한 작업이 어렵고 복잡할 수 있으며 오류가 발생하기 쉬울 수 있습니다.

Azure Backup 사용하면 프로그래밍 방식으로 대부분의 백업 작업을 자동화할 수 있습니다. 이 문서에서는 Azure Backup 지원하는 다양한 자동화 클라이언트를 제공합니다. 또한 엔터프라이즈 규모 백업 배포에 일반적으로 사용되는 몇 가지 엔드투엔드 자동화 시나리오를 안내합니다.

## <a name="automation-methods"></a>Automation 메서드

Azure Backup 기능에 액세스하려면 Azure에서 지원하는 다음 표준 자동화 방법을 사용할 수 있습니다.

- PowerShell
- CLI
- REST API
- Python SDK
- Go SDK
- Terraform
- ARM 템플릿
- Bicep

Logic Apps, Runbook 및 작업 그룹과 같은 다른 Azure 서비스와 연결된 Azure Backup 사용하여 엔드투엔드 자동화 워크플로를 설정할 수도 있습니다.

자동화 클라이언트가 지원하는 다양한 시나리오 및 해당 문서 참조에 대한 자세한 내용은 [Azure Backup 지원되는 자동화 솔루션 섹션을](#supported-automation-methods-by-operation-types) 참조하세요.

## <a name="sample-automation-scenarios"></a>샘플 자동화 시나리오

이 섹션에서는 백업 관리자로 발생할 수 있는 몇 가지 일반적인 자동화 사용 사례를 제공합니다. 또한 시작에 대한 지침을 제공합니다.

### <a name="configure-backups"></a>백업 구성

백업 관리자는 정기적으로 추가되는 새 인프라를 처리하고 합의된 요구 사항에 따라 보호되어야 합니다. PowerShell/CLI와 같은 자동화 클라이언트는 모든 VM 세부 정보를 가져오고 각 VM의 백업 상태를 확인한 다음 보호되지 않는 VM에 대해 적절한 조치를 취하는 데 도움이 됩니다.

그러나 이는 대규모로 수행되어야 합니다. 또한 주기적으로 예약하고 각 실행을 모니터링해야 합니다. 자동화 작업을 용이하게 하기 위해 Azure Backup 이제 Azure Policy 사용하고 [백업](backup-center-govern-environment.md#azure-policies-for-backup) 자산 관리를 위한 기본 제공 백업 특정 Azure 정책을 제공합니다.

Azure 정책을 범위에 할당하면 조건을 충족하는 모든 VM이 자동으로 백업되고, 최신 VM은 Azure Policy 의해 주기적으로 검사되고 보호됩니다. 비준수 리소스에 대해 경고하는 준수 보고서를 볼 수도 있습니다.

[백업을 위한 기본 제공 Azure 정책에 대해 자세히 알아봅니다.](backup-azure-auto-enable-backup.md)

다음 비디오에서는 백업에 Azure Policy 작동하는 방법을 보여 줍니다.  <br><br>

> [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Configure-backups-at-scale-using-Azure-Policy/player]

### <a name="export-backup-operational-data"></a>백업 작업 데이터 내보내기

전체 자산에 대한 백업 작업 데이터를 추출하고 정기적으로 모니터링 시스템/대시보드로 가져와야 할 수 있습니다. 대규모로 데이터를 빠르게 검색해야 합니다(대용량 레코드를 쿼리하는 동안). 리소스, 구독 및 테넌트 간에 쿼리해야 합니다. 또한 클라이언트에서 쿼리해야 합니다(Azure Portal/PowerShell/CLI/모든 SDK/REST API). 출력 형식(테이블 및 배열)도 유연해야 합니다.

ARG(Azure Resource Graph)를 사용하면 이러한 작업을 수행하고 대규모로 쿼리할 수 있습니다. Azure Backup 최소 쿼리(하나의 시나리오에 대해 하나의 쿼리)로 필요한 데이터를 가져오는 최적화된 방법으로 ARG를 사용합니다. 예를 들어 단일 쿼리는 모든 구독 및 모든 테넌트에서 모든 자격 증명 모음에서 실패한 모든 작업을 가져올 수 있습니다. 또한 쿼리는 Azure RBAC(Azure 역할 기반 액세스 제어) 규격입니다.

샘플 [ARG 쿼리를 참조하세요.](query-backups-using-azure-resource-graph.md#sample-queries)

### <a name="automate-responsesactions"></a>응답/작업 자동화

일시적인 백업 작업 실패에 대한 응답을 자동화하면 복원할 신뢰할 수 있는 백업 수가 올바른지 확인할 수 있습니다. 이렇게 하면 [RPO](azure-backup-glossary.md#rpo-recovery-point-objective)에서 의도하지 않은 위반을 방지할 수도 있습니다.

Azure Automation Runbook, PowerShell 및 Azure Resource Graph 조합을 사용하여 백업 작업을 다시 시도하도록 워크플로를 설정할 수 있습니다. 이는 일시적인 오류 또는 계획된/계획되지 않은 중단으로 인해 백업 작업이 실패한 시나리오에 도움이 됩니다.

시작하려면 이 단계를 따르세요.

1. Automation 계정 내에 Automation 계정 및 새 PowerShell Runbook을 만듭니다. [자세히 알아보기](../automation/learn/powershell-runbook-managed-identity.md).

2. Runbook 본문에 다음 스크립트를 삽입합니다. 

   스크립트는 ARG 쿼리를 실행하여 최근 작업 실패가 발생한 모든 VM 목록을 가져온 _다음(startTime에_ 필터를 쿼리에 추가할 수 있음) 각 VM에 대한 주문형 백업을 트리거합니다. 비슷한 쿼리를 만들어 백업 중인 모든 SQL 데이터베이스, HANA 데이터베이스, Azure Files 및 기타 Azure 워크로드 목록을 가져올 수 있습니다.

    ```azurepowershell
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    $connectionResult = Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $connection.TenantID `
    -ApplicationId $connection.ApplicationID `
    -CertificateThumbprint $connection.CertificateThumbprint
    "Login successful.."

    $query = "RecoveryServicesResources
    | where type in~ ('microsoft.recoveryservices/vaults/backupjobs')
    | extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
    | extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
    | extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
    | extend protectedItemName = split(split(properties.backupInstanceId, 'protectedItems')[1],'/')[1]
    | extend vaultId = tostring(split(id, '/backupJobs')[0])
    | extend vaultSub = tostring( split(id, '/')[2])
    | extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'), startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration 
    | where (dataSourceType in~ ('AzureIaasVM'))
    | where jobStatus=='Failed'
    | where operation == 'backup' or operation == 'adhocBackup'
    | project vaultSub, vaultId, protectedItemName, startTime, endTime, jobStatus, operation
    | sort by vaultSub"

    $subscriptions = Get-AzSubscription | foreach {$_.SubscriptionId}
    $result = Search-AzGraph -Subscription $subscriptions -Query $query -First 5
    $result = $result.data
    $prevsub = ""
    foreach($jobresponse in $result)
    {
                if($jobresponse.vaultSub -ne $prevsub)
                {
                            Set-AzContext -SubscriptionId $jobresponse.vaultSub
                            $prevsub = $jobresponse.vaultSub
                }

                $item = Get-AzRecoveryServicesBackupItem -VaultId $jobresponse.vaultId -BackupManagementType AzureVM -WorkloadType AzureVM -Name $jobresponse.protectedItemName

                Backup-AzRecoveryServicesBackupItem -ExpiryDateTimeUTC (get-date).AddDays(10) -Item $item -VaultId $jobresponse.vaultId
    }
    ```

3. Runbook에서 다음 모듈을 가져와 스크립트가 올바르게 실행되도록 합니다. `Az.Accounts` , `Az.RecoveryServices` , `Az.Graph` .

   [Runbook 에서 모듈을 가져오는 방법을 알아봅니다.](../automation/shared-resources/modules.md)

4. 일정에 따라 자동으로 실행하도록 스크립트를 구성하려면 [Runbook을 일정에 연결합니다.](../automation/shared-resources/schedules.md)

   시나리오에 대한 엔드 투 엔드 워크는 다음 비디오를 참조하세요. <br><br>

   > [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Automatically-retry-failed-backup-jobs-using-Azure-Resource-Graph-and-Azure-Automation-Runbooks/player]

## <a name="supported-automation-methods-by-operation-types"></a>작업 유형별로 지원되는 자동화 방법

### <a name="azure-vm"></a>Azure VM

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨 <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#back-up-azure-vms) | 지원됨 <br><br> [예제 참조](./quick-backup-vm-cli.md#enable-backup-for-an-azure-vm) | 지원됨  <br><br>  [예제를 참조하세요.](./backup-azure-arm-userestapi-backupazurevms.md) | 지원됨  <br><br> [예제를 참조하세요.](./backup-azure-auto-enable-backup.md) | 지원됨  <br><br> [예제를 참조하세요.](./backup-rm-template-samples.md) |
| Backup | 선택적 디스크 백업 | 지원됨  <br><br> [예제 참조](./selective-disk-backup-restore.md#using-powershell) | 지원됨  <br><br> [예제를 참조하세요.](./selective-disk-backup-restore.md#using-azure-cli) | 지원됨  <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup) | 해당 없음 | 해당 없음 |
| Backup | 주문형 백업 실행 | 지원됨   <br><br> [예제를 참조하세요.](./quick-backup-vm-powershell.md#start-a-backup-job) | 지원됨 -  <br><br> [예제를 참조하세요.](./quick-backup-vm-cli.md#start-a-backup-job) | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-backupazurevms.md#trigger-an-on-demand-backup-for-a-protected-azure-vm) | 해당 없음 | 해당 없음 |
| 복원 | 주 지역으로 디스크 복원 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#restore-an-azure-vm)하세요. | 지원됨  <br><br> [예제를 참조](./tutorial-restore-disk.md#restore-a-vm-disk)하세요. | 지원됨  <br><br> [예제를 참조](./backup-azure-arm-userestapi-restoreazurevms.md)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#restore-disks-to-a-secondary-region)하세요. | 지원됨   <br><br> [예제를 참조](/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks&preserve-view=true)하세요. | 지원됨   <br><br> [예제를 참조](./backup-azure-arm-userestapi-restoreazurevms.md#cross-region-restore)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 선택적 디스크 복원 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#restore-selective-disks)하세요. | 지원됨   <br><br> [예제를 참조](./selective-disk-backup-restore.md#restore-disks-with-azure-cli)하세요. | 지원됨   <br><br> [예제를 참조](./backup-azure-arm-userestapi-restoreazurevms.md#restore-disks-selectively)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 복원된 디스크에서 VM 만들기 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#using-managed-identity-to-restore-disks)하세요. | 지원됨   <br><br> [예제를 참조](./tutorial-restore-disk.md#using-managed-identity-to-restore-disks)하세요. | 지원됨   <br><br> [예제를 참조](/rest/api/backup/restores/trigger)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 파일 복원 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks)하세요. | 지원됨   <br><br> [예제를 참조](./tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)하세요. | 지원됨   <br><br> [예제를 참조](./backup-azure-arm-userestapi-restoreazurevms.md#restore-disks)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)하세요. | 지원됨   <br><br> [예제를 참조](./tutorial-restore-files.md)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#monitoring-a-backup-job)하세요. | 지원됨   <br><br> [예제를 참조](./quick-backup-vm-cli.md#monitor-the-backup-job)하세요. | 지원됨   <br><br> [예제를 참조](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#retain-data)하세요. | 지원됨   <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true)하세요. | 지원됨   <br><br> [예제를 참조](./backup-azure-arm-userestapi-backupazurevms.md#stop-protection-but-retain-existing-data)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#delete-backup-data)하세요. | 지원됨   <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true)하세요. | 지원됨   <br><br> [예제를 참조](./backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#resume-backup)하세요.    | 지원됨    <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_resume&preserve-view=true)하세요. | 지원됨    <br><br> [예제 참조](./backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion) | 해당 없음 | 해당 없음 |

### <a name="sql-in-azure-vm"></a>Azure VM의 SQL

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#configure-a-backup-policy) | 지원됨 | 지원됨 | 현재는 여기에 없습니다. | 지원됨    <br><br> [예제를 참조하세요.](./backup-rm-template-samples.md) |
| Backup | 자동 보호 사용 | 지원됨    <br><br> [예제 참조](./backup-azure-sql-automation.md#enable-autoprotection) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| Backup | 주문형 백업 실행 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#on-demand-backup) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 고유한 전체/차등 복구 지점으로 복원 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#original-restore-with-distinct-recovery-point) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 지정 시간으로 복원 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#original-restore-with-log-point-in-time) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#alternate-workload-restore-to-a-vault-in-secondary-region) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#track-azure-backup-jobs) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 경고 관리(미리 보기) | 지원됨    <br><br> [예제를 참조하세요.](../azure-monitor/powershell-samples.md) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 메트릭 관리(미리 보기) | 지원됨    <br><br> [예제를 참조하세요.](../azure-monitor/powershell-samples.md) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#change-policy-for-backup-items) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#retain-data) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#delete-backup-data) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 등록 취소 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#unregister-sql-vm) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 다시 등록 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#re-register-sql-vms) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="sap-hana-in-azure-vm"></a>Azure VM의 SAP HANA

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) | 지원됨 | 현재 지원되지 않음 | 해당 없음 |
| Backup | 주문형 백업 실행 | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-backup-cli.md#trigger-an-on-demand-backup) | 지원됨 | 해당 없음 | 지원됨 – 예제   <br><br> [예제를 참조하세요.](./backup-rm-template-samples.md) |
| 복원 | 고유한 전체/차등/증분 복구 지점으로 복원 | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-restore-cli.md#restore-a-database) | 지원됨 | 해당 없음 |    |
| 복원 | 지정 시간으로 복원 | 현재 지원되지 않음  | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-restore-cli.md#restore-a-database) | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 현재 지원되지 않음 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 현재 지원되지 않음  | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-manage-cli.md#change-policy) | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 현재 지원되지 않음  | 지원됨    <br><br> [예제 참조](./tutorial-sap-hana-manage-cli.md#stop-protection-with-retain-data) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-createorupdatepolicy.md) | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 현재 지원되지 않음  | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-manage-cli.md#stop-protection-without-retain-data) | 지원됨    <br><br> [예제를 참조하세요.](/rest/api/backup/protected-items/delete) | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 등록 취소 | 현재 지원되지 않음  | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 다시 등록 | 현재 지원되지 않음  | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="azure-files"></a>Azure 파일

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-afs-automation.md#configure-a-backup-policy) | 지원됨    <br><br> [예제를 참조하세요.](./backup-afs-cli.md#enable-backup-for-azure-file-shares) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-file-share-rest-api.md#configure-backup-for-an-unprotected-azure-file-share-using-rest-api) | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조하세요.](./backup-rm-template-samples.md) |
| Backup | 주문형 백업 실행 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-afs-automation.md#trigger-an-on-demand-backup) | 지원됨    <br><br> [예제를 참조하세요.](./backup-afs-cli.md#trigger-an-on-demand-backup-for-file-share) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) | 해당 없음 | 해당 없음 |
| 복원 | 원래 위치 또는 대체 위치로 복원 | 지원됨    <br><br> [예제를 참조하세요.](./restore-afs-powershell.md) | 지원됨    <br><br> [예제를 참조하세요.](./restore-afs-cli.md) | 지원됨    <br><br> [예제를 참조하세요.](./restore-azure-file-share-rest-api.md) | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-powershell.md#track-backup-and-restore-jobs) | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-backup-cli.md#monitor-jobs) | 지원됨    <br><br> [예제를 참조하세요.](./manage-azure-file-share-rest-api.md#monitor-jobs) | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-powershell.md#modify-the-protection-policy) | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-backup-cli.md#modify-policy) | 지원됨    <br><br> [예제를 참조하세요.](./manage-azure-file-share-rest-api.md#modify-policy) | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-powershell.md#stop-protection-and-retain-recovery-points) | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-backup-cli.md#stop-protection-and-retain-recovery-points) | 지원됨    <br><br> [예제를 참조하세요.](./manage-azure-file-share-rest-api.md#stop-protection-but-retain-existing-data) | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-powershell.md#stop-protection-without-retaining-recovery-points) | 지원됨    <br><br> [예제를 참조하세요.](./manage-afs-backup-cli.md#stop-protection-without-retaining-recovery-points) | 지원됨    <br><br> [예제를 참조하세요.](./manage-azure-file-share-rest-api.md#stop-protection-and-delete-data) | 해당 없음 | 해당 없음 |

### <a name="azure-blobs"></a>Azure Blob

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-ps.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-cli.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-backup-blobs.md) | 현재 지원되지 않음 | 지원됨 |
| 복원 | Blob 복원 | 지원됨    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-ps.md) | 지원됨    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-cli.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-restore-blobs.md) | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원됨    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-ps.md#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-cli.md#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-restore-blobs.md#tracking-jobs) | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |

### <a name="azure-disks"></a>Azure 디스크

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨   <br><br> [예제를 참조하세요.](./backup-managed-disks-ps.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-managed-disks-cli.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-backup-disks.md) | 현재 지원되지 않음 | 지원됨 |
| Backup | 주문형 백업 실행 | 지원됨    <br><br> [예제를 참조하세요.](./backup-managed-disks-ps.md#run-an-on-demand-backup) | 지원됨    <br><br> [예제를 참조하세요.](./backup-managed-disks-cli.md#run-an-on-demand-backup) |  해당 없음 | 해당 없음 |
| 복원 | 새 디스크로 복원 | 지원됨    <br><br> [예제를 참조하세요.](./restore-managed-disks-ps.md) | 지원됨    <br><br> [예제를 참조하세요.](./restore-managed-disks-cli.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-restore-disks.md) | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원됨    <br><br> [예제를 참조하세요.](./restore-managed-disks-ps.md#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](./restore-managed-disks-cli.md#tracking-job) | 지원됨    <br><br> [예제를 참조](./backup-azure-dataprotection-use-rest-api-restore-disks.md#track-jobs)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨 | 지원됨 | 지원됨 | 현재 여기에 없음 | 지원됨 |
| Backup | 주문형 백업 실행 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 대상 저장소 계정에서 데이터베이스 복원 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 데이터 삭제 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 데이터 보존 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="vault-level-configurations"></a>자격 증명 모음 수준 구성

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| 관리 | Recovery Services 자격 증명 모음 만들기 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#create-a-recovery-services-vault) | 지원됨    <br><br> [예제를 참조하세요.](./quick-backup-vm-cli.md#create-a-recovery-services-vault) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-createorupdatevault.md) | 해당 없음 | 지원됨    <br><br> [예제를 참조하세요.](./backup-rm-template-samples.md) |
| 관리 | Backup 자격 증명 모음 만들기 | 지원됨    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-ps.md#create-a-backup-vault) | 지원됨    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-cli.md#create-a-backup-vault) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md) | 해당 없음 | 지원됨 |
| 관리 | Recovery Services 자격 증명 모음 이동 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-move-recovery-services-vault.md#use-powershell-to-move-recovery-services-vault) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-move-recovery-services-vault.md#use-powershell-to-move-recovery-services-vault) | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Backup 자격 증명 모음 이동 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Recovery Services 자격 증명 모음 삭제 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-powershell) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-cli) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-azure-resource-manager) | 해당 없음 | 해당 없음 |
| 관리 | Backup 자격 증명 모음 삭제 | 지원됨 | 여기 | 여기 | 해당 없음 | 해당 없음 |
| 관리 | 진단 설정 구성 | 지원됨 | 지원됨 | 지원됨 | 지원됨    <br><br> [예제를 참조하세요.](./azure-policy-configure-diagnostics.md) | 지원됨 |
| 관리 | Azure Monitor 경고 관리(미리 보기) | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 메트릭 관리(미리 보기) | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 보안 | Recovery Services 자격 증명 모음에 프라이빗 엔드포인트 사용 | 지원됨 | 지원됨 | 지원됨 | 현재 지원되는 감사 정책만 | 지원됨 |
| 보안 | Recovery Services 자격 증명 모음에 고객 관리형 키를 사용하도록 설정합니다. | 지원됨 | 지원됨 | 지원됨 | 현재 지원되는 감사 정책만 | 지원됨 |
| 보안 | Recovery Services 자격 증명 모음에 대해 일시 삭제 사용 | 지원됨 | 지원됨 | 지원됨 | 현재 지원되지 않음 | 지원됨 |
| 복원력 | Recovery Services 자격 증명 모음에 지역 간 복원 사용 | 지원됨 | 지원됨 | 지원됨 | 현재 지원되지 않음 | 지원됨 |