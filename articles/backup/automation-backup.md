---
title: Azure Backup의 자동화
description: Azure Backup에서 제공 하는 자동화 기능의 요약을 제공 합니다.
ms.topic: conceptual
ms.date: 11/26/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: a4c26ad3fb1ccf478c42ce1078d060a2c7d53fc1
ms.sourcegitcommit: 5c1cd21464e8165b16eb8d63ab31ab7b1a8f3675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133206908"
---
# <a name="automation-in-azure-backup"></a>Azure Backup의 자동화

Azure estate가 확장 되어 광범위 하 고 다양 한 워크 로드를 확장 하 게 되 면 중요 한 작업 (예: 올바른 컴퓨터의 백업 구성, 상태 모니터링, 적시에 정정 작업 수행)을 자동화 하 여 백업 목표가 충족 되는지 확인 해야 합니다. 백업 작업을 위해 강력한 자동화를 사용 하면 규모에 맞게 관리 하는 동안 효율성을 달성할 수 있습니다. 또한 사용자 오류가 발생할 가능성을 줄입니다.

Azure Backup를 사용 하면 프로그래밍 방식 메서드를 사용 하 여 대부분의 백업 관련 작업을 자동화할 수 있습니다. 이 문서에서는 Azure Backup에서 지 원하는 다양 한 자동화 클라이언트에 대해 설명 합니다. 또한 엔터프라이즈 규모 배포에서 일반적으로 표시 되는 일부 종단 간 Azure Backup 자동화 시나리오를 안내 합니다.

## <a name="automation-methods"></a>자동화 메서드

Azure Backup 기능에 액세스 하려면 Azure에서 지 원하는 다음과 같은 표준 자동화 메서드를 사용할 수 있습니다.

- PowerShell
- CLI
- REST API
- Python SDK
- Go SDK
- Terraform
- Ansible
- ARM 템플릿
- Bicep

Logic Apps, runbook, 작업 그룹 및 azure 리소스 Graph와 같은 다른 azure 서비스와 연결 된 Azure Backup를 사용 하 여 종단 간 자동화 워크플로를 설정할 수도 있습니다.

자동화 클라이언트에서 지 원하는 다양 한 시나리오 및 해당 문서 참조에 대 한 자세한 내용은 [Azure Backup에 대해 지원 되는 자동화 솔루션](#supported-automation-methods-by-operation-types) 섹션을 참조 하십시오.

## <a name="sample-automation-scenarios"></a>샘플 자동화 시나리오

이 섹션에서는 백업 관리자로 발생할 수 있는 몇 가지 일반적인 자동화 사용 사례를 제공 합니다. 또한 시작 하기 위한 지침을 제공 합니다.

### <a name="configure-backups"></a>백업 구성

백업 관리자는 정기적으로 추가 되는 새 인프라를 처리 하 고 합의 된 요구 사항에 따라 보호 되는지 확인 해야 합니다. PowerShell/CLI와 같은 자동화 클라이언트는 모든 VM 세부 정보를 가져오고, 각 VM의 백업 상태를 확인 하 고, 보호 되지 않는 Vm에 대 한 적절 한 조치를 취하는 데 도움이 됩니다.

그러나이는 확장 가능 해야 합니다. 또한 정기적으로 예약 하 고 각 실행을 모니터링 해야 합니다. 자동화 작업을 용이 하 게 하기 위해 Azure Backup 이제 Azure Policy를 사용 하 고 백업 공간을 관리 하는 [기본 제공 백업 관련 Azure 정책을](backup-center-govern-environment.md#azure-policies-for-backup) 제공 합니다.

범위에 Azure Policy를 할당 하면 해당 조건을 충족 하는 모든 Vm이 자동으로 백업 되 고 최신 Vm은 Azure Policy에 의해 주기적으로 검색 되 고 보호 됩니다. 또한 호환 되지 않는 리소스에 대 한 가시성을 제공 하는 백업 센터를 통해 규정 준수 보고서를 볼 수 있습니다.

[백업에 대 한 기본 제공 Azure 정책에 대해 자세히 알아보세요](backup-azure-auto-enable-backup.md).

다음 비디오는 Azure Policy 백업에 대해 작동 하는 방법을 보여 줍니다.  <br><br>

> [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Configure-backups-at-scale-using-Azure-Policy/player]

### <a name="export-backup-operational-data"></a>백업 내보내기-운영 데이터

전체 공간에 대 한 백업 작업 데이터를 추출 하 고 모니터링 시스템/대시보드로 정기적으로 가져와야 할 수 있습니다. 규모가 큰 경우에는 데이터를 신속 하 게 검색 해야 합니다 (큰 레코드를 쿼리 하는 동안). 리소스, 구독 및 테 넌 트 간 쿼리를 수행 해야 할 수 있습니다. 다른 클라이언트를 사용 하 여 데이터를 쿼리 하는 유연성이 필요할 수도 있습니다 (Azure Portal/PowerShell/CLI/any SDK/REST API). 또한 출력 형식의 유연성 (테이블 vs 배열)도 필요할 수 있습니다.

Azure 리소스 Graph (ARG)를 사용 하면 이러한 작업과 쿼리를 대규모로 수행할 수 있습니다. Azure Backup는 인수를 사용 하 여 최소한의 쿼리로 필요한 데이터를 인출 하는 최적화 된 방법으로 인수를 사용 합니다. 예를 들어 단일 쿼리는 모든 구독 및 모든 테 넌 트의 모든 자격 증명 모음에서 실패 한 모든 작업을 페치할 수 있습니다. 또한 쿼리는 azure RBAC (역할 기반 액세스 제어)와 호환 됩니다.

[샘플 ARG 쿼리](query-backups-using-azure-resource-graph.md#sample-queries)를 참조 하세요.

### <a name="automate-responsesactions"></a>응답/작업 자동화

임시 백업 작업 오류에 대 한 응답을 자동화 하면 복원할 수 있는 신뢰할 수 있는 백업 수를 적절 하 게 유지할 수 있습니다. 이는 [RPO](azure-backup-glossary.md#rpo-recovery-point-objective)에서 의도 하지 않은 위반을 방지 하는 데도 도움이 됩니다.

Azure Automation Runbook, PowerShell 및 ARG의 조합을 사용 하 여 백업 작업을 다시 시도 하도록 워크플로를 설정할 수 있습니다. 이는 일시적인 오류 또는 계획 되거나 계획 되지 않은 중단으로 인해 백업 작업이 실패 한 경우에 유용 합니다.

이 runbook을 설정 하는 방법에 대 한 자세한 내용은 [실패 한 백업 작업 자동 다시 시도](/azure/architecture/framework/resiliency/auto-retry)를 참조 하세요.

다음 비디오는 시나리오의 종단 간 연습을 제공 합니다. <br><br>

   > [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Automatically-retry-failed-backup-jobs-using-Azure-Resource-Graph-and-Azure-Automation-Runbooks/player]

## <a name="supported-automation-methods-by-operation-types"></a>작업 형식으로 지원 되는 자동화 메서드

### <a name="azure-vm"></a>Azure VM

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** | **Bicep** | **Terraform** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원 여부 <br><br> [예제를 참조](./backup-azure-vms-automation.md#back-up-azure-vms)하세요. | 지원됨 <br><br> [예제 참조](./quick-backup-vm-cli.md#enable-backup-for-an-azure-vm) | 지원됨  <br><br>  [예제를 참조](./backup-azure-arm-userestapi-backupazurevms.md)하세요. | 지원됨  <br><br> [예제를 참조](./backup-azure-auto-enable-backup.md)하세요. | 지원됨  <br><br> [예제를 참조](./backup-rm-template-samples.md)하세요. | 지원됨 <br><br> [예제를 참조](./quick-backup-vm-bicep-template.md)하세요. | 지원됨 <br><br> [예제를 참조](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/backup_protected_vm)하세요. | 
| Backup | 선택적 디스크 백업 | 지원 여부  <br><br> [예제 참조](./selective-disk-backup-restore.md#using-powershell) | 지원됨  <br><br> [예제를 참조](./selective-disk-backup-restore.md#using-azure-cli)하세요. | 지원됨  <br><br> [예제를 참조](./backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)하세요. | 해당 없음 | 해당 없음 | 해당 없음 | 현재 지원되지 않습니다. | 
| Backup | 주문형 백업 실행 | 지원 여부   <br><br> [예제를 참조](./quick-backup-vm-powershell.md#start-a-backup-job)하세요. | 되지  <br><br> [예제를 참조](./quick-backup-vm-cli.md#start-a-backup-job)하세요. | 지원 여부   <br><br> [예제를 참조](./backup-azure-arm-userestapi-backupazurevms.md#trigger-an-on-demand-backup-for-a-protected-azure-vm)하세요. | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 
| 복원 | 주 지역으로 디스크 복원 | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#restore-an-azure-vm) | 지원 여부  <br><br> [예제를 참조하세요.](./tutorial-restore-disk.md#restore-a-vm-disk) | 지원됨  <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-restoreazurevms.md) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 지원 여부   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#restore-disks-to-a-secondary-region) | 지원됨   <br><br> [예제를 참조하세요.](/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks&preserve-view=true) | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-restoreazurevms.md#cross-region-restore) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 선택적 디스크 복원 | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#restore-selective-disks) | 지원됨   <br><br> [예제를 참조하세요.](./selective-disk-backup-restore.md#restore-disks-with-azure-cli) | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-restoreazurevms.md#restore-disks-selectively) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 복원된 디스크에서 VM 만들기 | 지원 여부   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#using-managed-identity-to-restore-disks) | 지원됨   <br><br> [예제를 참조하세요.](./tutorial-restore-disk.md#using-managed-identity-to-restore-disks) | 지원됨   <br><br> [예제를 참조하세요.](/rest/api/backup/restores/trigger) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 파일 복원 | 지원 여부   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) | 지원됨   <br><br> [예제를 참조하세요.](./tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-restoreazurevms.md#restore-disks) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) | 지원됨   <br><br> [예제를 참조하세요.](./tutorial-restore-files.md) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#monitoring-a-backup-job) | 지원됨   <br><br> [예제를 참조하세요.](./quick-backup-vm-cli.md#monitor-the-backup-job) | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#retain-data) | 지원됨   <br><br> [예제를 참조하세요.](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true) | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-backupazurevms.md#stop-protection-but-retain-existing-data) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨   <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#delete-backup-data) | 지원됨   <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true)하세요. | 지원 여부   <br><br> [예제를 참조](./backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)하세요. | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원됨   <br><br> [예제를 참조](./backup-azure-vms-automation.md#resume-backup)하세요.    | 지원됨    <br><br> [예제를 참조](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_resume&preserve-view=true)하세요. | 지원됨    <br><br> [예제 참조](./backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |

### <a name="sql-in-azure-vm"></a>Azure VM의 SQL

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** | **Bicep** | **Terraform** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조](./backup-azure-sql-automation.md#configure-a-backup-policy)하세요. | 지원됨 | 지원됨 | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조](./backup-rm-template-samples.md)하세요. | 지원됨 | 현재 지원되지 않음 |
| Backup | 자동 보호 사용 | 지원됨    <br><br> [예제 참조](./backup-azure-sql-automation.md#enable-autoprotection) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 현재 지원되지 않음 |
| Backup | 주문형 백업 실행 | 지원 여부    <br><br> [예제를 참조](./backup-azure-sql-automation.md#on-demand-backup)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 현재 지원되지 않음 |
| 복원 | 고유한 전체/차등 복구 지점으로 복원 | 지원됨    <br><br> [예제를 참조](./backup-azure-sql-automation.md#original-restore-with-distinct-recovery-point)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 지정 시간으로 복원 | 지원됨    <br><br> [예제를 참조](./backup-azure-sql-automation.md#original-restore-with-log-point-in-time)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 지원됨    <br><br> [예제를 참조](./backup-azure-sql-automation.md#alternate-workload-restore-to-a-vault-in-secondary-region)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조](./backup-azure-sql-automation.md#track-azure-backup-jobs)하세요. | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 경고 관리(미리 보기) | 지원됨    <br><br> [예제를 참조하세요.](../azure-monitor/powershell-samples.md) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 메트릭 관리(미리 보기) | 지원 여부    <br><br> [예제를 참조하세요.](../azure-monitor/powershell-samples.md) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#change-policy-for-backup-items) | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#retain-data) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#delete-backup-data) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 등록 취소 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#unregister-sql-vm) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 다시 등록 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-sql-automation.md#re-register-sql-vms) | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |

### <a name="sap-hana-in-azure-vm"></a>Azure VM의 SAP HANA

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** | **Bicep** | **Terraform** | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) | 지원됨 | 현재 지원되지 않음 | 해당 없음 | 지원 여부 | 현재 지원되지 않음 | 
| Backup | 주문형 백업 실행 | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-backup-cli.md#trigger-an-on-demand-backup) | 지원됨 | 해당 없음 | 지원됨 – 예제   <br><br> [예제를 참조하세요.](./backup-rm-template-samples.md) | 해당 없음 | 현재 지원되지 않음 |
| 복원 | 고유한 전체/차등/증분 복구 지점으로 복원 | 현재 지원되지 않음 | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-restore-cli.md#restore-a-database) | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 지정 시간으로 복원 | 현재 지원되지 않음  | 지원 여부    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-restore-cli.md#restore-a-database) | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 지역 간 복원 | 현재 지원되지 않음 | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 현재 지원되지 않음  | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-manage-cli.md#change-policy) | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 현재 지원되지 않음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 현재 지원되지 않음  | 지원됨    <br><br> [예제 참조](./tutorial-sap-hana-manage-cli.md#stop-protection-with-retain-data) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-createorupdatepolicy.md) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 현재 지원되지 않음  | 지원됨    <br><br> [예제를 참조하세요.](./tutorial-sap-hana-manage-cli.md#stop-protection-without-retain-data) | 지원됨    <br><br> [예제를 참조하세요.](/rest/api/backup/protected-items/delete) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 등록 취소 | 현재 지원되지 않음  | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 인스턴스 다시 등록 | 현재 지원되지 않음  | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |

### <a name="azure-files"></a>Azure 파일

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** | **Bicep** | **Terraform** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조](./backup-azure-afs-automation.md#configure-a-backup-policy)하세요. | 지원됨    <br><br> [예제를 참조](./backup-afs-cli.md#enable-backup-for-azure-file-shares)하세요. | 지원됨    <br><br> [예제를 참조](./backup-azure-file-share-rest-api.md#configure-backup-for-an-unprotected-azure-file-share-using-rest-api)하세요. | 현재 지원되지 않음 | 지원 여부    <br><br> [예제를 참조](./backup-rm-template-samples.md)하세요. | 지원됨 | 지원 여부 <br><br> [예제를 참조](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/backup_protected_file_share)하세요. |
| Backup | 주문형 백업 실행 | 지원 여부    <br><br> [예제를 참조](./backup-azure-afs-automation.md#trigger-an-on-demand-backup)하세요. | 지원 여부    <br><br> [예제를 참조](./backup-afs-cli.md#trigger-an-on-demand-backup-for-file-share)하세요. | 지원 여부    <br><br> [예제를 참조](./backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share)하세요. | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 원본 또는 대체 위치로 복원 | 지원됨    <br><br> [예제를 참조](./restore-afs-powershell.md)하세요. | 지원됨    <br><br> [예제를 참조](./restore-afs-cli.md)하세요. | 지원 여부    <br><br> [예제를 참조](./restore-azure-file-share-rest-api.md)하세요. | 해당 없음 | 해당 없음 |해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조](./manage-afs-powershell.md#track-backup-and-restore-jobs)하세요. | 지원됨    <br><br> [예제를 참조](./manage-afs-backup-cli.md#monitor-jobs)하세요. | 지원됨    <br><br> [예제를 참조](./manage-azure-file-share-rest-api.md#monitor-jobs)하세요. | 해당 없음 | 해당 없음 |해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원 여부    <br><br> [예제를 참조](./manage-afs-powershell.md#modify-the-protection-policy)하세요. | 지원됨    <br><br> [예제를 참조](./manage-afs-backup-cli.md#modify-policy)하세요. | 지원됨    <br><br> [예제를 참조](./manage-azure-file-share-rest-api.md#modify-policy)하세요. | 해당 없음 | 해당 없음 |해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨    <br><br> [예제를 참조](./manage-afs-powershell.md#stop-protection-and-retain-recovery-points)하세요. | 지원 여부    <br><br> [예제를 참조](./manage-afs-backup-cli.md#stop-protection-and-retain-recovery-points)하세요. | 지원됨    <br><br> [예제를 참조](./manage-azure-file-share-rest-api.md#stop-protection-but-retain-existing-data)하세요. | 해당 없음 | 해당 없음 |해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨    <br><br> [예제를 참조](./manage-afs-powershell.md#stop-protection-without-retaining-recovery-points)하세요. | 지원 여부    <br><br> [예제를 참조](./manage-afs-backup-cli.md#stop-protection-without-retaining-recovery-points)하세요. | 지원됨    <br><br> [예제를 참조](./manage-azure-file-share-rest-api.md#stop-protection-and-delete-data)하세요. | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |

### <a name="azure-blobs"></a>Azure Blob

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** | **Bicep** | **Terraform** | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-ps.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-cli.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-backup-blobs.md) | 현재 지원되지 않음 | 지원 여부 | 지원 여부    <br><br> [예제를 참조하세요.](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.dataprotection/backup-create-storage-account-enable-protection) | 지원 여부    <br><br> [예제를 참조하세요.](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/data_protection_backup_instance_blob_storage) |
| 복원 | Blob 복원 | 지원됨    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-ps.md) | 지원됨    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-cli.md) | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-restore-blobs.md) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-ps.md#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](./restore-blobs-storage-account-cli.md#tracking-job) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-restore-blobs.md#tracking-jobs) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |

### <a name="azure-disks"></a>Azure 디스크

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** | **Bicep** | **Terraform** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원됨   <br><br> [예제를 참조하세요.](./backup-managed-disks-ps.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-managed-disks-cli.md) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-backup-disks.md) | 현재 지원되지 않음 | 지원됨 | 해당 없음 | 해당 없음 | 지원 여부    <br><br> [예제를 참조하세요.](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.dataprotection/backup-create-disk-enable-protection) | 지원됨    <br><br> [예제를 참조하세요.](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/data_protection_backup_instance_disk) |
| Backup | 주문형 백업 실행 | 지원됨    <br><br> [예제를 참조](./backup-managed-disks-ps.md#run-an-on-demand-backup)하세요. | 지원됨    <br><br> [예제를 참조](./backup-managed-disks-cli.md#run-an-on-demand-backup)하세요. |  해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 새 디스크로 복원 | 지원됨    <br><br> [예제를 참조](./restore-managed-disks-ps.md)하세요. | 지원됨    <br><br> [예제를 참조](./restore-managed-disks-cli.md)하세요. | 지원 여부    <br><br> [예제를 참조](./backup-azure-dataprotection-use-rest-api-restore-disks.md)하세요. | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 작업 모니터링 | 지원 여부    <br><br> [예제를 참조](./restore-managed-disks-ps.md#tracking-job)하세요. | 지원됨    <br><br> [예제를 참조](./restore-managed-disks-cli.md#tracking-job)하세요. | 지원됨    <br><br> [예제를 참조](./backup-azure-dataprotection-use-rest-api-restore-disks.md#track-jobs)하세요. | 해당 없음 | 해당 없음 |  해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 현재 지원되지 않음 | 현재 지원되지 않음 | 현재 지원되지 않음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 보존 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 백업 데이터 삭제 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |

### <a name="azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** | **Bicep** | **Terraform** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | 백업 정책 만들기 및 백업 구성 | 지원 여부 | 지원됨 | 지원됨 | 현재 여기에 없음 | 지원됨 | 지원됨 | 지원됨    <br><br> [예제를 참조하세요.](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/data_protection_backup_instance_postgresql) |
| Backup | 주문형 백업 실행 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 복원 | 대상 스토리지 계정에서 데이터베이스 복원 | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 백업 정책 수정 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 데이터 삭제 | 지원 여부 | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 중지 및 데이터 보존 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리 | 보호 다시 시작 | 지원 여부 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |

### <a name="vault-level-configurations"></a>자격 증명 모음 수준 구성

| **범주** | **연산** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM 템플릿** |
| --- | --- | --- | --- | --- | --- | --- |
| 관리 | Recovery Services 자격 증명 모음 만들기 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-vms-automation.md#create-a-recovery-services-vault) | 지원 여부    <br><br> [예제를 참조하세요.](./quick-backup-vm-cli.md#create-a-recovery-services-vault) | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-arm-userestapi-createorupdatevault.md) | 해당 없음 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-rm-template-samples.md) |
| 관리 | Backup 자격 증명 모음 만들기 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-ps.md#create-a-backup-vault) | 지원 여부    <br><br> [예제를 참조하세요.](./backup-blobs-storage-account-cli.md#create-a-backup-vault) | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md) | 해당 없음 | 지원됨 |
| 관리 | Recovery Services 자격 증명 모음 이동 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-move-recovery-services-vault.md#use-powershell-to-move-recovery-services-vault) | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-move-recovery-services-vault.md#use-powershell-to-move-recovery-services-vault) | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Backup 자격 증명 모음 이동 | 지원 여부 | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Recovery Services 자격 증명 모음 삭제 | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-powershell) | 지원됨    <br><br> [예제를 참조하세요.](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-cli) | 지원 여부    <br><br> [예제를 참조하세요.](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-azure-resource-manager) | 해당 없음 | 해당 없음 |
| 관리 | Backup 자격 증명 모음 삭제 | 지원됨 | 여기 | 여기 | 해당 없음 | 해당 없음 |
| 관리 | 진단 설정 구성 | 지원됨 | 지원됨 | 지원됨 | 지원됨    <br><br> [예제를 참조하세요.](./azure-policy-configure-diagnostics.md) | 지원됨 |
| 관리 | Azure Monitor 경고 관리(미리 보기) | 지원됨 | 지원됨 | 지원됨 | 해당 없음 | 해당 없음 |
| 관리 | Azure Monitor 메트릭 관리(미리 보기) | 지원 여부 | 지원 여부 | 지원됨 | 해당 없음 | 해당 없음 |
| 보안 | Recovery Services 자격 증명 모음에 프라이빗 엔드포인트 사용 | 지원 여부 | 지원됨 | 지원됨 | 현재 지원되는 감사 정책만 | 지원됨 |
| 보안 | Recovery Services 자격 증명 모음에 고객 관리형 키를 사용하도록 설정합니다. | 지원됨 | 지원됨 | 지원됨 | 현재 지원되는 감사 정책만 | 지원됨 |
| 보안 | Recovery Services 자격 증명 모음에 대해 일시 삭제 사용 | 지원됨 | 지원됨 | 지원됨 | 현재 지원되지 않음 | 지원됨 |
| 복원력 | Recovery Services 자격 증명 모음에 지역 간 복원 사용 | 지원됨 | 지원 여부 | 지원 여부 | 현재 지원되지 않음 | 지원 여부 |

## <a name="additional-resources"></a>추가 리소스

- [Azure Quickstart 템플릿](https://github.com/Azure/azure-quickstart-templates)
- [Terraform 샘플](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples)
- [Ansible 샘플](https://docs.ansible.com/ansible/latest/collections/azure/azcollection/azure_rm_backupazurevm_module.html#examples)
