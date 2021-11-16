---
title: Azure Backup의 자동화
description: 에서 제공 하는 자동화 기능의 요약을 제공 Azure Backup
ms.topic: conceptual
ms.date: 11/16/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: ec19a2f887971a3a159b8b6ca155b33cd9af52e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493006"
---
# <a name="automation-in-azure-backup"></a>Azure Backup의 자동화

Azure에서 작업을 관리 하는 경우 백업 공간을 확장 하 여 볼륨의 다양 한 워크 로드 및 규모를 확장할 수 있습니다.

중소 규모에서 다음 작업은 백업 하는 데 적합 한 컴퓨터를 식별 하 고, 백업을 구성 하 고, 상태를 모니터링 하 고, 데이터를 추출 하는 등의 작업을 쉽게 수행할 수 있습니다. 그러나 대규모 작업의 경우 이러한 작업은 어려울 수 있으며 오류가 발생할 수 있습니다.

Azure Backup를 사용 하면 프로그래밍 방식의 메서드를 사용 하 여 대부분의 백업 작업을 자동화할 수 있습니다. 이 문서에서는 Azure Backup에서 지 원하는 다양 한 자동화 클라이언트를 제공 합니다. 또한 엔터프라이즈 규모의 백업 배포에 일반적으로 사용 되는 일부 종단 간 자동화 시나리오를 안내 합니다.

## <a name="automation-methods"></a>자동화 메서드

Azure Backup 기능에 액세스 하려면 Azure에서 지 원하는 다음과 같은 표준 자동화 메서드를 사용할 수 있습니다.

- PowerShell
- CLI
- REST API
- Python SDK
- Go SDK
- Terraform
- ARM 템플릿
- Bicep

Logic Apps, Runbook 및 작업 그룹과 같은 다른 Azure 서비스와 연결 된 Azure Backup를 사용 하 여 종단 간 자동화 워크플로를 설정할 수도 있습니다.

Automation 클라이언트에서 지 원하는 다양 한 시나리오 및 해당 문서 참조에 대 한 자세한 내용은 [Azure Backup에 대해 지원 되는 자동화 솔루션](#supported-automation-methods-by-operation-types) 섹션을 참조 하십시오.

## <a name="sample-automation-scenarios"></a>샘플 자동화 시나리오

이 섹션에서는 백업 관리자로 발생할 수 있는 몇 가지 일반적인 자동화 사용 사례를 제공 합니다. 또한 시작 하기에 대 한 지침을 제공 합니다.

### <a name="configure-backups"></a>백업 구성

백업 관리자는 정기적으로 추가 되는 새 인프라를 처리 하 고 합의 된 요구 사항에 따라 보호 되는지 확인 해야 합니다. PowerShell/CLI와 같은 자동화 클라이언트는 모든 VM 세부 정보를 가져오고, 각 VM의 백업 상태를 확인 하 고, 보호 되지 않는 Vm에 적절 한 조치를 취하는 데 도움이 됩니다.

그러나이는 확장 가능 해야 합니다. 또한 정기적으로 예약 하 고 각 실행을 모니터링 해야 합니다. 자동화 작업을 용이 하 게 하기 위해 Azure Backup 이제 Azure Policy를 사용 하 고 백업 공간을 관리 하는 [기본 제공 백업 관련 Azure 정책을](backup-center-govern-environment.md#azure-policies-for-backup) 제공 합니다.

범위에 Azure 정책을 할당 하면 사용자의 조건을 충족 하는 모든 Vm이 자동으로 백업 되 고 최신 Vm이 Azure Policy에 의해 주기적으로 검색 되 고 보호 됩니다. 비준수 리소스에 대해 경고 하는 규정 준수 보고서를 볼 수도 있습니다.

[백업에 대 한 기본 제공 Azure 정책에 대해 자세히 알아보세요](backup-azure-auto-enable-backup.md).

다음 비디오는 Azure Policy 백업에 대해 작동 하는 방법을 보여 줍니다.  <br><br>

> [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Configure-backups-at-scale-using-Azure-Policy/player]

### <a name="export-backup-operational-data"></a>백업 내보내기-운영 데이터

전체 공간에 대 한 백업 작업 데이터를 추출 하 고 모니터링 시스템/대시보드로 정기적으로 가져와야 할 수 있습니다. 큰 규모에서 데이터는 빠르게 검색 되어야 합니다 (큰 레코드를 쿼리 하는 동안). 리소스, 구독 및 테 넌 트 간 쿼리를 수행 해야 합니다. 클라이언트 (Azure Portal/PowerShell/CLI/any SDK/REST API) 에서도 쿼리해야 합니다. 출력 형식 (테이블 vs 배열) 에서도 유연성이 있어야 합니다.

Azure 리소스 Graph (ARG)를 사용 하 여 이러한 작업과 쿼리를 대규모로 수행할 수 있습니다. Azure Backup는 인수를 사용 하 여 최소한의 쿼리로 필요한 데이터를 인출 하는 최적화 된 방법으로 인수를 사용 합니다. 예를 들어 단일 쿼리는 모든 구독 및 모든 테 넌 트의 모든 자격 증명 모음에서 실패 한 모든 작업을 페치할 수 있습니다. 또한 쿼리는 azure RBAC (역할 기반 액세스 제어)와 호환 됩니다.

[샘플 ARG 쿼리](query-backups-using-azure-resource-graph.md#sample-queries)를 참조 하세요.

### <a name="automate-responsesactions"></a>응답/작업 자동화

임시 백업 작업 오류에 대 한 응답을 자동화 하면 복원에 적합 한 수의 신뢰할 수 있는 백업을 유지 하는 데 도움이 됩니다. 이는 [RPO](azure-backup-glossary.md#rpo-recovery-point-objective)에서 의도 하지 않은 위반을 방지 하는 데도 도움이 됩니다.

Azure Automation Runbook, PowerShell 및 Azure 리소스 Graph의 조합을 사용 하 여 백업 작업을 다시 시도 하도록 워크플로를 설정할 수 있습니다. 이는 일시적인 오류 또는 계획 되거나 계획 되지 않은 중단으로 인해 백업 작업이 실패 한 경우에 유용 합니다.

시작하려면 이 단계를 따르세요.

1. Automation 계정 및 Automation 계정 내에서 새 PowerShell Runbook을 만듭니다. [자세히 알아보기](/azure/automation/learn/powershell-runbook-managed-identity).

2. Runbook의 본문에 다음 스크립트를 삽입 합니다. 

   이 스크립트는 ARG 쿼리를 실행 하 여 최근 작업이 실패 한 모든 Vm의 목록을 가져오고 ( _startTime_ 에 필터를 쿼리에 추가할 수 있음) 각 vm에 대 한 요청 시 백업을 트리거합니다. 유사한 쿼리를 만들어 백업 중인 모든 SQL 데이터베이스, HANA 데이터베이스, Azure Files 및 기타 Azure 워크 로드의 목록을 가져올 수 있습니다.

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

3. Runbook에서 다음 모듈을 가져와 스크립트가 올바르게 실행 되는지 확인 `Az.Accounts` 합니다., `Az.RecoveryServices` , `Az.Graph` .

   [Runbook에서 모듈을 가져오는 방법에 대해 알아봅니다](/azure/automation/shared-resources/modules).

4. [Runbook을 일정에 연결](/azure/automation/shared-resources/schedules) 하 여 정기적으로 자동 실행 되도록 스크립트를 구성 합니다.

   시나리오의 종단 간 연습을 보려면 다음 비디오를 참조 하세요. <br><br>

   > [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Automatically-retry-failed-backup-jobs-using-Azure-Resource-Graph-and-Azure-Automation-Runbooks/player]

## <a name="supported-automation-methods-by-operation-types"></a>작업 형식으로 지원 되는 자동화 메서드

### <a name="azure-vm"></a>Azure VM

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원 여부 <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#back-up-azure-vms)하세요. | 지원됨 <br><br> [예제 참조](/azure/backup/quick-backup-vm-cli#enable-backup-for-an-azure-vm) | 지원됨  <br><br>  [예제를 참조](/azure/backup/backup-azure-arm-userestapi-backupazurevms)하세요. | 지원됨  <br><br> [예제를 참조](/azure/backup/backup-azure-auto-enable-backup)하세요. | 지원됨  <br><br> [예제를 참조](/azure/backup/backup-rm-template-samples)하세요. |
| Backup | 선택적 디스크 백업 | 지원 여부  <br><br> [예제 참조](/azure/backup/selective-disk-backup-restore#using-powershell) | 지원 여부  <br><br> [예제를 참조](/azure/backup/selective-disk-backup-restore#using-azure-cli)하세요. | 지원됨  <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-backupazurevms#excluding-disks-in-azure-vm-backup)하세요. | 해당 없음 | 해당 없음 |
| Backup | 주문형 백업 실행 | 지원 여부   <br><br> [예제를 참조](/azure/backup/quick-backup-vm-powershell#start-a-backup-job)하세요. | 되지  <br><br> [예제를 참조](/azure/backup/quick-backup-vm-cli#start-a-backup-job)하세요. | 지원 여부   <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-backupazurevms#trigger-an-on-demand-backup-for-a-protected-azure-vm)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 주 지역으로 디스크 복원 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#restore-an-azure-vm)하세요. | 지원됨  <br><br> [예제를 참조](/azure/backup/tutorial-restore-disk#restore-a-vm-disk)하세요. | 지원됨  <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-restoreazurevms)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#restore-disks-to-a-secondary-region)하세요. | 지원됨   <br><br> [예제를 참조](/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks&preserve-view=true)하세요. | 지원 여부   <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#cross-region-restore)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 선택적 디스크 복원 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#restore-selective-disks)하세요. | 지원 여부   <br><br> [예제를 참조](/azure/backup/selective-disk-backup-restore#restore-disks-with-azure-cli)하세요. | 지원 여부   <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks-selectively)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 복원된 디스크에서 VM 만들기 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#using-managed-identity-to-restore-disks)하세요. | 지원됨   <br><br> [예제를 참조](/azure/backup/tutorial-restore-disk#using-managed-identity-to-restore-disks)하세요. | 지원됨   <br><br> [예제를 참조](/rest/api/backup/restores/trigger)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 파일 복원 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)하세요. | 지원됨   <br><br> [예제를 참조](/azure/backup/tutorial-restore-disk#create-a-vm-from-the-restored-disk)하세요. | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)하세요. | 지원됨   <br><br> [예제를 참조](/azure/backup/tutorial-restore-files)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#monitoring-a-backup-job)하세요. | 지원됨   <br><br> [예제를 참조](/azure/backup/quick-backup-vm-cli#monitor-the-backup-job)하세요. | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#retain-data)하세요. | 지원 여부   <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true)하세요. | 지원 여부   <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-but-retain-existing-data)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#delete-backup-data)하세요. | 지원됨   <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true)하세요. | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-and-delete-data)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원됨   <br><br> [예제를 참조](/azure/backup/backup-azure-vms-automation#resume-backup)하세요.    | 지원됨    <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_resume&preserve-view=true)하세요. | 지원됨    <br><br> [예제 참조](/azure/backup/backup-azure-arm-userestapi-backupazurevms#undo-the-deletion) | 해당 없음 | 해당 없음 |

### <a name="sql-in-azure-vm"></a>Azure VM의 SQL

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#configure-a-backup-policy)하세요. | 지원됨 | 지원됨 | 현재 여기에 없음 | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-rm-template-samples)하세요. |
| Backup | 자동 보호 사용 | 지원 여부    <br><br> [예제 참조](/azure/backup/backup-azure-sql-automation#enable-autoprotection) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| Backup | 주문형 백업 실행 | 지원 여부    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#on-demand-backup)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 고유한 전체/차등 복구 지점으로 복원 | 지원 여부    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#original-restore-with-distinct-recovery-point)하세요. | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 지정 시간으로 복원 | 지원 여부    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#original-restore-with-log-point-in-time)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 지원 여부    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#alternate-workload-restore-to-a-vault-in-secondary-region)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#track-azure-backup-jobs)하세요. | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 경고 관리 (미리 보기) | 지원 여부    <br><br> [예제를 참조](/azure/azure-monitor/powershell-samples)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 메트릭 관리 (미리 보기) | 지원 여부    <br><br> [예제를 참조](/azure/azure-monitor/powershell-samples)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#change-policy-for-backup-items)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#retain-data)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-azure-sql-automation#delete-backup-data)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 등록 취소 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-sql-automation#unregister-sql-vm) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 다시 등록 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-sql-automation#re-register-sql-vms) | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="sap-hana-in-azure-vm"></a>Azure VM의 SAP HANA

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/tutorial-sap-hana-backup-cli#register-and-protect-the-sap-hana-instance) | 지원됨 | 현재 지원되지 않음 | 해당 없음 |
| Backup | 주문형 백업 실행 | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/tutorial-sap-hana-backup-cli#trigger-an-on-demand-backup) | 지원됨 | 해당 없음 | 지원됨 – 예제   <br><br> [예제를 참조하세요.](/azure/backup/backup-rm-template-samples) |
| 복원 | 고유한 전체/차등/증분 복구 지점으로 복원 | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database) | 지원됨 | 해당 없음 |    |
| 복원 | 지정 시간으로 복원 | 현재 지원되지 않음  | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database) | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 현재 지원되지 않음 | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 현재 지원되지 않음  | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/tutorial-sap-hana-manage-cli#change-policy) | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 현재 지원되지 않음  | 지원 여부    <br><br> [예제 참조](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-with-retain-data) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 현재 지원되지 않음  | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-without-retain-data) | 지원됨    <br><br> [예제를 참조하세요.](/rest/api/backup/protected-items/delete) | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 등록 취소 | 현재 지원되지 않음  | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 다시 등록 | 현재 지원되지 않음  | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="azure-files"></a>Azure 파일

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-azure-afs-automation#configure-a-backup-policy)하세요. | 지원 여부    <br><br> [예제를 참조](/azure/backup/backup-afs-cli#enable-backup-for-azure-file-shares)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-azure-file-share-rest-api#configure-backup-for-an-unprotected-azure-file-share-using-rest-api)하세요. | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-rm-template-samples)하세요. |
| Backup | 주문형 백업 실행 | 지원 여부    <br><br> [예제를 참조](/azure/backup/backup-azure-afs-automation#trigger-an-on-demand-backup)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-afs-cli#trigger-an-on-demand-backup-for-file-share)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/backup-azure-file-share-rest-api#trigger-an-on-demand-backup-for-file-share)하세요. | 해당 없음 | 해당 없음 |
| 복원 | 원본 또는 대체 위치로 복원 | 지원됨    <br><br> [예제를 참조](/azure/backup/restore-afs-powershell)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/restore-afs-cli)하세요. | 지원 여부    <br><br> [예제를 참조](/azure/backup/restore-azure-file-share-rest-api)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조](/azure/backup/manage-afs-powershell#track-backup-and-restore-jobs)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-afs-backup-cli#monitor-jobs)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-azure-file-share-rest-api#monitor-jobs)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원 여부    <br><br> [예제를 참조](/azure/backup/manage-afs-powershell#modify-the-protection-policy)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-afs-backup-cli#modify-policy)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-azure-file-share-rest-api#modify-policy)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-afs-powershell#stop-protection-and-retain-recovery-points)하세요. | 지원 여부    <br><br> [예제를 참조](/azure/backup/manage-afs-backup-cli#stop-protection-and-retain-recovery-points)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-azure-file-share-rest-api#stop-protection-but-retain-existing-data)하세요. | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-afs-powershell#stop-protection-without-retaining-recovery-points)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-afs-backup-cli#stop-protection-without-retaining-recovery-points)하세요. | 지원됨    <br><br> [예제를 참조](/azure/backup/manage-azure-file-share-rest-api#stop-protection-and-delete-data)하세요. | 해당 없음 | 해당 없음 |

### <a name="azure-blobs"></a>Azure Blob

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-blobs-storage-account-ps) | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/backup-blobs-storage-account-cli) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-blobs) | 현재 지원되지 않음 | 지원 여부 |
| 복원 | Blob 복원 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/restore-blobs-storage-account-ps) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/restore-blobs-storage-account-cli) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs) | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/restore-blobs-storage-account-ps#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/restore-blobs-storage-account-cli#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs#tracking-jobs) | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |

### <a name="azure-disks"></a>Azure 디스크

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원 여부   <br><br> [예제를 참조하세요.](/azure/backup/backup-managed-disks-ps) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-managed-disks-cli) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-disks) | 현재 지원되지 않음 | 지원 여부 |
| Backup | 주문형 백업 실행 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/backup-managed-disks-ps#run-an-on-demand-backup) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-managed-disks-cli#run-an-on-demand-backup) |  해당 없음 | 해당 없음 |
| 복원 | 새 디스크로 복원 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/restore-managed-disks-ps) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/restore-managed-disks-cli) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks) | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/restore-managed-disks-ps#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/restore-managed-disks-cli#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks#track-jobs) | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨 | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원 여부 | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨 | 지원됨 | 지원됨 | 현재는 여기에 없습니다. | 지원됨 |
| Backup | 주문형 백업 실행 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 복원 | 대상 스토리지 계정에서 데이터베이스 복원 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 데이터 삭제 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 데이터 보존 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |

### <a name="vault-level-configurations"></a>자격 증명 모음 수준 구성

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| 관리 | Recovery Services 자격 증명 모음 만들기 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-vms-automation#create-a-recovery-services-vault) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/quick-backup-vm-cli#create-a-recovery-services-vault) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-arm-userestapi-createorupdatevault) | 해당 없음 | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-rm-template-samples) |
| 관리 | Backup 자격 증명 모음 만들기 | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-blobs-storage-account-ps#create-a-backup-vault) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-blobs-storage-account-cli#create-a-backup-vault) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-dataprotection-use-rest-api-create-update-backup-vault) | 해당 없음 | 지원 여부 |
| 관리 | Recovery Services 자격 증명 모음 이동 | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault) | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Backup 자격 증명 모음 이동 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Recovery Services 자격 증명 모음 삭제 | 지원 여부    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-powershell) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-cli) | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-azure-resource-manager) | 해당 없음 | 해당 없음 |
| 관리 | Backup 자격 증명 모음 삭제 | 지원 여부 | 여기 | 여기 | 해당 없음 | 해당 없음 |
| 관리 | 진단 설정 구성 | 지원됨 | 지원됨 | 지원됨 | 지원됨    <br><br> [예제를 참조하세요.](/azure/backup/azure-policy-configure-diagnostics) | 지원됨 |
| 관리 | Azure Monitor 경고 관리(미리 보기) | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 메트릭 관리(미리 보기) | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 보안 | Recovery Services 자격 증명 모음에 프라이빗 엔드포인트 사용 | 지원 여부 | 지원됨 | 지원됨 | 현재 지원되는 감사 정책만 | 지원됨 |
| 보안 | Recovery Services 자격 증명 모음에 고객 관리형 키를 사용하도록 설정합니다. | 지원 여부 | 지원 여부 | 지원 여부 | 현재 지원되는 감사 정책만 | 지원 여부 |
| 보안 | Recovery Services 자격 증명 모음에 대해 일시 삭제 사용 | 지원 여부 | 지원 여부 | 지원 여부 | 현재 지원되지 않음 | 지원됨 |
| 복원력 | Recovery Services 자격 증명 모음에 지역 간 복원 사용 | 지원됨 | 지원됨 | 지원됨 | 현재 지원되지 않음 | 지원됨 |
