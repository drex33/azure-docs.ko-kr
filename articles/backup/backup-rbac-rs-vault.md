---
title: Azure RBAC(Role Based Access Control)를 사용하여 백업 관리
description: Azure RBAC(Role Based Access Control)를 사용하여 Recovery Services 자격 증명 모음의 백업 관리 작업에 대한 액세스를 관리합니다.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: fdde385ca49a61a8fb2c2bba81311035dca3e324
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215189"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Azure RBAC(Role Based Access Control)를 사용하여 Azure Backup 복구 지점 관리

Azure RBAC(Role Based Access Control)를 사용하면 Azure에 대한 세분화된 액세스 관리가 가능합니다. Azure RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다.

> [!IMPORTANT]
> Azure Backup에서 제공하는 역할은 Azure Portal에서 또는 REST API나 Recovery Services 자격 증명 모음의 PowerShell 또는 CLI cmdlet을 통해 수행할 수 있는 작업으로 제한됩니다. Azure Backup 에이전트 클라이언트 UI 또는 System Center Data Protection Manager UI 또는 Azure Backup Server UI에서 수행되는 작업은 해당 역할을 제어할 수 없습니다.

Azure Backup은 백업 관리 작업을 제어할 수 있는 세 가지 기본 제공 역할을 제공합니다. [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)에 대한 자세한 정보

* [Backup Contributor](../role-based-access-control/built-in-roles.md#backup-contributor) - 이 역할은 Recovery Services 자격 증명 모음을 삭제하고 다른 사용자에게 액세스 권한을 제공하는 권한을 제외하고, 백업을 만들고 관리하는 모든 권한을 갖습니다. 모든 백업 관리 작업을 수행할 수 있는 백업 관리 관리자 역할로 생각하시면 됩니다.
* [Backup 운영자](../role-based-access-control/built-in-roles.md#backup-operator) - 이 역할은 백업을 제거하고 백업 정책을 관리하는 권한을 제외하고, 참여자가 할 수 있는 모든 일을 할 수 있습니다. 이 역할은 온-프레미스 리소스의 데이터 삭제나 등록 제거를 통해 백업을 중지하는 작업처럼 안전하지 않은 작업을 수행할 수 없다는 점만 빼면 참여자와 똑같습니다.
* [Backup 읽기 권한자](../role-based-access-control/built-in-roles.md#backup-reader) - 이 역할은 모든 백업 관리 작업을 볼 수 있는 권한을 갖습니다. 이 역할을 모니터링 요원으로 생각하시면 됩니다.

더 많은 제어를 위해 사용자 고유의 역할을 정의하려는 경우 Azure RBAC에서 [사용자 지정 역할을 빌드](../role-based-access-control/custom-roles.md)하는 방법을 참조하세요.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Backup 기본 제공 역할을 관리 작업에 매핑

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Azure VM 백업에 대한 최소 역할 요구 사항

다음 표는 Backup 관리 작업과 해당 작업을 수행하는 데 필요한 최소 Azure 역할을 캡처한 것입니다.

| 관리 작업 | 필요한 최소 Azure 역할 | 필요한 범위 | 대체 |
| --- | --- | --- | --- |
| Recovery Services 자격 증명 모음 만들기 | Backup 참가자 | 자격 증명 모음을 포함하는 리소스 그룹 |   |
| Azure VM의 백업 활성화 | Backup 운영자 | 자격 증명 모음을 포함하는 리소스 그룹 |   |
| | 가상 머신 참가자 | VM 리소스 |  또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| VM의 주문형 백업 | Backup 운영자 | Recovery Services 자격 증명 모음 |   |
| VM 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |   |
| | 참가자 | VM이 배포될 리소스 그룹 |   또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다.  Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.DomainRegistration/domains/write, Microsoft.Compute/virtualMachines/write  Microsoft.Network/virtualNetworks/read Microsoft.Network/virtualNetworks/subnets/join/action |
| | 가상 머신 참가자 | 백업된 원본 VM |   또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| 관리되지 않는 디스크 VM 백업 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 가상 머신 참가자 | 백업된 원본 VM | 또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| | Storage 계정 참가자 | 디스크가 복원되는 Storage 계정 리소스 |   또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Storage/storageAccounts/write |
| VM 백업에서 관리 디스크 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 가상 머신 참가자 | 백업된 원본 VM |    또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| | Storage 계정 참가자 | 복원 과정의 일부로 선택한 관리 디스크로 전환하기 전에 자격 증명 모음에서 데이터를 보관하도록 선택된 임시 Storage 계정 |   또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Storage/storageAccounts/write |
| | 참가자 | 관리 디스크가 복원될 리소스 그룹 | 또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Resources/subscriptions/resourceGroups/write|
| VM 백업에서 개별 파일 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 가상 머신 참가자 | 백업된 원본 VM | 또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| 지역 간 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 구독 | 이는 위에서 언급한 복원 권한이 추가된 것입니다. 특히 CRR의 경우 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다.   “Microsoft.RecoveryServices/locations/backupAadProperties/rea” “Microsoft.RecoveryServices/locations/backupCrrJobs/action”         “Microsoft.RecoveryServices/locations/backupCrrJob/action" "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action”          “Microsoft.RecoveryServices/locations/backupCrrOperationResults/read” “Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read” |
| Azure VM 백업에 대한 백업 정책 만들기 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| Azure VM 백업의 백업 정책 수정 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| Azure VM 백업의 백업 정책 삭제 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| VM 백업에서 백업 중지(데이터 보존 또는 데이터 삭제를 통해) | Backup 참가자 | Recovery Services 자격 증명 모음 |
| 온-프레미스 Windows 서버/클라이언트/SCDPM 또는 Azure Backup Server 등록 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| 등록된 온-프레미스 Windows 서버/클라이언트/SCDPM 또는 Azure Backup Server 삭제 | Backup 참가자 | Recovery Services 자격 증명 모음 |

> [!IMPORTANT]
> VM 리소스 범위에서 VM 참가자를 지정하고 VM 설정의 일부로 **백업** 을 선택하면 VM이 이미 백업된 경우에도 **백업 사용** 화면이 열립니다. 백업 상태를 확인하는 호출은 구독 수준에서만 작동하기 때문입니다. 이를 방지하려면 자격 증명 모음으로 이동하여 VM의 백업 항목 보기를 열거나 구독 수준에서 VM 참가자 역할을 지정합니다.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Azure 워크로드 백업에 대한 최소 역할 요구 사항(SQL 및 HANA DB 백업)

다음 표는 Backup 관리 작업과 해당 작업을 수행하는 데 필요한 최소 Azure 역할을 캡처한 것입니다.

| 관리 작업 | 필요한 최소 Azure 역할 | 필요한 범위 | 대체 |
| --- | --- | --- | --- |
| Recovery Services 자격 증명 모음 만들기 | Backup 참가자 | 자격 증명 모음을 포함하는 리소스 그룹 |   |
| SQL 및/또는 HANA 데이터베이스의 백업 사용 | Backup 운영자 | 자격 증명 모음을 포함하는 리소스 그룹 |   |
| | 가상 머신 참가자 | DB가 설치된 VM 리소스 |  또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| DB의 주문형 백업 | Backup 운영자 | Recovery Services 자격 증명 모음 |   |
| 데이터베이스 복원 또는 파일로 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |   |
| | 가상 머신 참가자 | 백업된 원본 VM |   또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| | 가상 머신 참가자 | DB가 복원되거나 파일이 생성되는 대상 VM |   또는 기본 제공 역할 대신 다음과 같은 권한이 있는 사용자 지정 역할을 고려할 수 있습니다. Microsoft.Compute/virtualMachines/write |
| Azure VM 백업에 대한 백업 정책 만들기 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| Azure VM 백업의 백업 정책 수정 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| Azure VM 백업의 백업 정책 삭제 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| VM 백업에서 백업 중지(데이터 보존 또는 데이터 삭제를 통해) | Backup 참가자 | Recovery Services 자격 증명 모음 |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure 파일 공유 백업에 대한 최소 역할 요구 사항

다음 표는 Backup 관리 작업과 Azure 파일 공유 작업을 수행하는 데 필요한 역할을 캡처한 것입니다.

| 관리 작업 | 필요 역할 | 리소스 |
| --- | --- | --- |
| Azure 파일 공유의 백업 사용 | Backup 참가자 |Recovery Services 자격 증명 모음 |
| | 스토리지 계정 백업 기여자 | 스토리지 계정 리소스 |
| VM의 주문형 백업 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| 파일 공유 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 스토리지 계정 백업 기여자 | 복원 원본 및 대상 파일 공유가 있는 스토리지 계정 리소스 |
| 개별 파일 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| |Storage 계정 참가자|복원 원본 및 대상 파일 공유가 있는 스토리지 계정 리소스 |
| 보호 중지 |Backup 참가자 | Recovery Services 자격 증명 모음 |
| 자격 증명 모음에서 스토리지 계정 등록 취소 |Backup 참가자 | Recovery Services 자격 증명 모음 |
| |Storage 계정 참가자 | 스토리지 계정 리소스|

## <a name="next-steps"></a>다음 단계

* [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md): Azure Portal에서 Azure RBAC를 시작합니다.
* 다음을 사용하여 액세스를 관리하는 방법에 대해 알아봅니다.
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Azure RBAC(Role Based Access Control) 문제 해결](../role-based-access-control/troubleshooting.md): 일반적인 문제를 해결하기 위한 제안 사항을 가져옵니다.
