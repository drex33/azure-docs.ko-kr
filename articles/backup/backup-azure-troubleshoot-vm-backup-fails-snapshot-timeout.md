---
title: 에이전트 및 확장 문제 해결
description: 에이전트, 확장명 및 디스크와 관련된 Azure Backup 오류의 증상, 원인 및 해결 방법
ms.topic: troubleshooting
ms.date: 05/25/2021
ms.service: backup
ms.openlocfilehash: 72e2ce2c32a1be1e6f116ef5513c1c24a42284bc
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438198"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup 오류 문제 해결: 에이전트 또는 확장 관련 문제

이 문서에서는 VM 에이전트 및 확장과의 통신에 관련된 Azure Backup 오류를 해결하는 데 도움이 될 수 있는 문제 해결 단계를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>백업 오류를 해결하는 단계별 가이드

대부분의 일반적인 백업 오류는 아래 나열된 문제 해결 단계에 따라 자체적으로 해결할 수 있습니다.

### <a name="step-1-check-azure-vm-health"></a>1단계: Azure VM 상태 확인

- **Azure VM 프로비저닝 상태가 '실행 중'인지 확인합니다**. [VM 프로비저닝 상태](../virtual-machines/states-billing.md)가 **중지됨/할당 취소됨/업데이트 중** 상태인 경우 백업 작업에 방해가 될 수 있습니다. *Azure Portal > VM > 개요* 를 열고 VM이 **실행 중** 상태인지 확인한 다음, 백업 작업을 다시 시도합니다.
- **보류 중인 OS 업데이트 또는 재부팅을 검토합니다**. VM에 보류 중인 OS 업데이트나 재부팅이 없는지 확인합니다.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>2단계: Azure VM 게스트 에이전트 서비스 상태 확인

- **Azure VM 게스트 에이전트 서비스가 시작되고 최신 상태인지 확인합니다**.
  - Windows VM에서
    - **services.msc** 로 이동하여 **Windows Azure VM 게스트 에이전트 서비스** 가 실행 중인지 확인합니다. 또한 [최신 버전](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)이 설치되어 있는지 확인합니다. 자세한 내용은 [Windows VM 게스트 에이전트 문제](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)를 참조하세요.
    - Azure VM 에이전트는 포털, PowerShell, 명령줄 인터페이스 또는 Azure Resource Manager 템플릿의 Azure Marketplace 이미지에서 배포된 모든 Windows VM에 기본적으로 설치됩니다. Azure에 배포된 사용자 지정 VM 이미지를 만들 때 [에이전트의 수동 설치](../virtual-machines/extensions/agent-windows.md#manual-installation)가 필요할 수 있습니다.
    - 지원 매트릭스를 검토하여 [지원되는 Windows 운영 체제](backup-support-matrix-iaas.md#operating-system-support-windows)에서 VM이 실행되는지 확인합니다.
  - Linux VM에서
    - `ps -e` 명령을 실행하여 Azure VM 게스트 에이전트 서비스가 실행 중인지 확인합니다. 또한 [최신 버전](../virtual-machines/extensions/update-linux-agent.md)이 설치되어 있는지 확인합니다. 자세한 내용은 [Linux VM 게스트 에이전트 문제](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)를 참조하세요.
    - [시스템 패키지에 대한 Linux VM 에이전트 종속성](../virtual-machines/extensions/agent-linux.md#requirements)에 지원되는 구성이 있는지 확인합니다. 예를 들어 지원되는 Python 버전은 2.6 이상입니다.
    - 지원 매트릭스를 검토하여 [지원되는 Linux 운영 체제](backup-support-matrix-iaas.md#operating-system-support-linux)에서 VM이 실행되는지 확인합니다.

### <a name="step-3-check-azure-vm-extension-health"></a>3단계: Azure VM 확장 상태 확인

- **모든 Azure VM 확장이 '프로비저닝 성공'상태인지 확인** 합니다. 확장이 실패 상태이면 백업에 방해가 될 수 있습니다.
- *Azure Portal > VM > 설정 > 확장 > 확장 상태* 를 열고 모든 확장이 **프로비저닝 성공** 상태인지 확인합니다.
- 모든 [확장 문제](../virtual-machines/extensions/overview.md#troubleshoot-extensions)가 해결되었는지 확인하고 백업 작업을 다시 시도합니다.
- **COM+ 시스템 애플리케이션** 이 실행 중인지 확인합니다. 또한 **DTC(Distributed Transaction Coordinator) 서비스** 가 **네트워크 서비스 계정** 으로 실행 중이어야 합니다. 이 문서의 단계에 따라 [COM+ 및 MSDTC 문제를 해결](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error)합니다.

### <a name="step-4-check-azure-backup-extension-health"></a>4 단계: Azure Backup 확장 상태 확인

Azure Backup은 VM 스냅샷 확장을 사용하여 Azure 가상 머신의 애플리케이션 일치 백업을 수행합니다. Azure Backup은 백업을 사용하도록 설정한 후 트리거되는 첫 번째 예약된 백업의 일부로 확장을 설치합니다.

- **VMSnapshot 확장이 실패 상태가 아닌지 확인합니다**. 이 [섹션](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state)에 나열된 단계에 따라 Azure Backup 확장이 정상인지 확인합니다.

- **바이러스 백신이 확장을 차단하고 있는지 확인합니다**. 특정 바이러스 백신 소프트웨어가 확장의 실행을 차단할 수 있습니다.
  
  백업 오류가 발생하는 경우 ***이벤트 뷰어 애플리케이션 로그** _에 _*_오류 있는 애플리케이션 이름이 IaaSBcdrExtension.exe_**인 로그 항목이 있는지 확인합니다. 항목이 있으면 VM에 구성된 바이러스 백신이 백업 확장의 실행을 제한하고 있는 것일 수 있습니다. 바이러스 백신 구성에서 다음 디렉터리를 제외하여 테스트하고 백업 작업을 다시 시도합니다.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **네트워크 액세스가 필요한지 확인합니다**. 확장 패키지는 Azure Storage 확장 리포지토리에서 다운로드되고, 확장 상태 업로드는 Azure Storage에 게시됩니다. [자세히 알아보기](../virtual-machines/extensions/features-windows.md#network-access).
  - 지원되지 않는 버전의 에이전트가 설치된 경우 VM의 해당 지역에서 Azure Storage에 대한 아웃바운드 액세스 권한을 허용해야 합니다.
  - 게스트 방화벽 또는 프록시를 사용하여 `168.63.129.16`에 대한 액세스를 차단한 경우 위와 관계없이 확장이 실패합니다. 포트 80, 443 및 32526이 필요합니다. [자세히 알아보세요](../virtual-machines/extensions/features-windows.md#network-access).

- **게스트 VM 내에서 DHCP를 사용할 수 있는지 확인합니다**. 이는 IaaS VM 백업 작동을 위해 DHCP에서 호스트 또는 패브릭 주소를 가져오는 데 필요합니다. 고정 개인 IP가 필요한 경우 Azure Portal 또는 PowerShell을 통해 구성해야 하며 VM 내 DHCP 옵션을 사용할 수 있는지 확인합니다. [자세히 알아보세요](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **VSS 기록기 서비스가 실행되고 있는지 확인합니다**. 다음 단계에 따라 [VSS 기록기 문제를 해결합니다](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state).
- **백업 모범 사례 지침을 따릅니다**. [모범 사례를 검토하여 Azure VM 백업을 사용](backup-azure-vms-introduction.md#best-practices)합니다.
- **암호화된 디스크에 대한 지침을 검토합니다**. 암호화된 디스크가 있는 VM에 백업을 사용하는 경우 필요한 모든 권한을 제공했는지 확인합니다. 자세한 내용은 [암호화된 Azure VM 백업 및 복원](backup-azure-vms-encryption.md)을 참조하세요.

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 에이전트가 Azure Backup과 통신할 수 없습니다.

**오류 코드**: UserErrorGuestAgentStatusUnavailable <br>
**오류 메시지**: VM 에이전트가 Azure Backup과 통신할 수 없습니다.<br>

Azure VM 에이전트가 중지되었거나 오래되었거나 일관성이 없는 상태이거나 설치되지 않았을 수 있습니다. 이러한 경우 Azure Backup 서비스에서 스냅샷을 트리거할 수 없습니다.

- **Azure Portal > VM > 설정 > 속성 창을 열고** VM **상태** 가 **실행 중** 이고 **에이전트 상태** 가 **준비** 인지 확인합니다. VM 에이전트가 중지되었거나 일관성이 없는 상태이면 에이전트를 다시 시작합니다.<br>
  - Windows VM의 경우 다음 [단계](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)에 따라 게스트 에이전트를 다시 시작합니다.<br>
  - Linux VM의 경우 다음 [단계](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)에 따라 게스트 에이전트를 다시 시작합니다.
- **Azure Portal > VM > 설정 > 확장** 을 열고 모든 확장이 **프로비저닝 성공** 상태인지 확인합니다. 그렇지 않으면 다음 [단계](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state)에 따라 문제를 해결합니다.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - 스냅샷 상태에 대해 VM 에이전트와 통신할 수 없습니다.

**오류 코드**: GuestAgentSnapshotTaskStatusError<br>
**오류 메시지**: 스냅샷 상태에 대해 VM 에이전트와 통신할 수 없습니다. <br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  

**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**원인 3: [스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**원인 4: [VM 에이전트 구성 옵션이 설정되지 않았습니다(Linux VM의 경우).](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**원인 5: [애플리케이션 제어 솔루션이 IaaSBcdrExtension.exe를 차단하고 있습니다.](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVMProvisioningStateFailed - VM이 실패한 프로비저닝 상태입니다.

**오류 코드**: UserErrorVmProvisioningStateFailed<br>
**오류 메시지**: VM이 실패한 프로비저닝 상태입니다.<br>

이 오류는 확장 오류 중 하나로 인해 VM이 프로비저닝 실패 상태가 될 때 발생합니다.<br>**Azure Portal > VM > 설정 > 확장 > 확장 상태** 를 열고 모든 확장이 **프로비저닝 성공** 상태인지 확인합니다. 자세한 내용은 [프로비저닝 상태](../virtual-machines/states-billing.md)를 참조하세요.

- 확장이 실패 상태이면 백업에 방해가 될 수 있습니다. 이러한 확장 문제가 해결되었는지 확인하고 백업 작업을 다시 시도합니다.
- VM 프로비저닝 상태가 업데이트 중 상태이면 백업에 방해가 될 수 있습니다. 정상인지 확인하고 백업 작업을 다시 시도합니다.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - 복원 지점 컬렉션이 최대 한도에 도달했습니다.

**오류 코드**: UserErrorRpCollectionLimitReached <br>
**오류 메시지**: 복원 지점 컬렉션이 최대 한도에 도달했습니다. <br>

- 이 문제는 복구 지점 리소스 그룹에 잠금이 있어 복구 지점의 자동 정리를 방해하는 경우 발생할 수 있습니다.
- 하루에 여러 개의 백업이 트리거되는 경우에도 이 문제가 발생할 수 있습니다. 인스턴트 복원 지점은 구성된 스냅샷 보존에 따라 1~5일 동안 보존되고 지정된 시간에 18개의 인스턴트 RP만 VM에 연결할 수 있으므로 현재는 하루에 하나의 백업만 권장합니다. <br>
- VM에 대한 복원 지점 컬렉션 및 리소스 그룹의 복원 지점 수는 18개를 초과할 수 없습니다. 새 복원 지점을 만들려면 기존 복원 지점을 삭제합니다.

권장 작업:<br>
이 문제를 해결하려면 VM의 리소스 그룹에 대한 잠금을 제거하고 정리를 트리거하는 작업을 다시 시도합니다.
> [!NOTE]
> 백업 서비스는 VM의 리소스 그룹과 별개의 리소스 그룹을 만들어 복원 지점 컬렉션을 저장합니다. 백업 서비스에서 사용하기 위해 생성된 리소스 그룹은 잠그지 않는 것이 좋습니다. 백업 서비스가 만드는 리소스 그룹의 명명 형식은 AzureBackupRG_`<Geo>`_`<number>`입니다. 예: *AzureBackupRG_northeurope_1*

**1단계: [복원 지점 리소스 그룹에서 잠금 제거](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2단계: [복원 지점 컬렉션 정리](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Backup에는 암호화된 VM을 백업할 수 있는 키 자격 증명 모음에 대한 충분한 사용 권한이 없습니다.

**오류 코드**: UserErrorKeyvaultPermissionsNotConfigured <br>
**오류 메시지**: Backup에는 암호화된 VM을 백업하기 위한 키 자격 증명 모음에 대한 충분한 사용 권한이 없습니다. <br>

백업 작업이 암호화된 VM에서 성공할 수 있도록 키 자격 증명 모음에 액세스할 수 있는 사용 권한이 있어야 합니다. 사용 권한은 [Azure Portal](./backup-azure-vms-encryption.md) 또는 [PowerShell](./backup-azure-vms-automation.md#enable-protection)을 통해 설정할 수 있습니다.

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 가상 머신에 네트워크 연결이 없으므로 스냅샷 작업이 실패했습니다.

**오류 코드**: ExtensionSnapshotFailedNoNetwork<br>
**오류 메시지**: 가상 머신에 네트워크 연결이 없으므로 스냅샷 작업이 실패했습니다.<br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 수행하고 작업을 다시 시도하세요.

**[스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - VMSnapshot 확장 작업이 실패했습니다.

**오류 코드**: ExtensionOperationFailedForManagedDisks <br>
**오류 메시지**: VMSnapshot 확장 작업이 실패했습니다.<br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 2: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 3: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - 내부 오류가 발생하여 백업하지 못했습니다.

**오류 코드**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**오류 메시지**: 내부 오류가 발생하여 백업하지 못했습니다. 몇 분 후에 작업을 다시 시도하세요. <br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 4: [리소스 그룹이 잠겨 있으므로 Backup 서비스에는 이전 복원 지점을 삭제할 수 있는 사용 권한이 없습니다.](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize - 구성된 디스크 크기는 현재 Azure Backup에서 지원되지 않습니다.

**오류 코드**: UserErrorUnsupportedDiskSize <br>
**오류 메시지**: 구성된 디스크 크기는 현재 Azure Backup에서 지원되지 않습니다. <br>

디스크 크기가 32TB를 초과하는 VM을 백업할 때 백업 작업이 실패할 수 있습니다. 또한 크기가 4TB를 초과하는 암호화된 디스크의 백업은 현재 지원되지 않습니다. 지원되는 제한보다 작거나 같은 크기로 디스크를 분할하세요.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 다른 백업 작업이 현재 진행 중이어서 백업을 시작할 수 없습니다.

**오류 코드**: UserErrorBackupOperationInProgress <br>
**오류 메시지**: 다른 백업 작업이 현재 진행 중이어서 백업을 시작할 수 없습니다.<br>

기존 백업 작업이 진행 중이어서 최근 백업 작업이 실패했습니다. 현재 작업이 완료될 때까지 새 백업 작업을 시작할 수 없습니다. 현재 진행 중인 백업 작업이 완료된 후 다른 백업 작업을 트리거 또는 예약해야 합니다. 백업 작업 상태를 확인하려면 다음 단계를 수행하세요.

1. Azure Portal에 로그인하고 **모든 서비스** 를 선택합니다. Recovery Services를 입력하고 **Recovery Services 자격 증명 모음** 을 선택합니다. Recovery Services 자격 증명 모음의 목록이 표시됩니다.
2. Recovery Services 자격 증명 모음 목록에서 백업이 구성된 자격 증명 모음을 선택합니다.
3. 자격 증명 모음 대시보드 메뉴에서 **백업 작업** 을 선택하여 모든 백업 작업을 표시합니다.
   - 백업 작업이 진행 중이면 백업 작업이 완료될 때까지 기다리거나 취소합니다.
     - 백업 작업을 취소하려면 백업 작업을 마우스 오른쪽 단추로 클릭하고 **취소** 를 선택하거나 [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)을 사용합니다.
   - 다른 자격 증명 모음에서 백업을 다시 구성한 경우 이전 자격 증명 모음에서 실행 중인 백업이 없는지 확인합니다. 있는 경우 백업 작업을 취소합니다.
     - 백업 작업을 취소하려면 백업 작업을 마우스 오른쪽 단추로 클릭하고 **취소** 를 선택하거나 [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)을 사용합니다.
4. 백업 작업을 다시 시도합니다.

예약된 백업 작업이 다음 백업 구성과 충돌하여 오래 걸리는 경우 [모범 사례](backup-azure-vms-introduction.md#best-practices), [백업 성능](backup-azure-vms-introduction.md#backup-performance) 및 [복원 고려 사항](backup-azure-vms-introduction.md#backup-and-restore-considerations)을 검토하세요.

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - 오류로 인해 백업하지 못했습니다. 자세한 내용은 작업 오류 메시지 세부 정보를 참조하세요.

**오류 코드**: UserErrorCrpReportedUserError <br>
**오류 메시지**: 오류로 인해 백업하지 못했습니다. 자세한 내용은 작업 오류 메시지 세부 정보를 참조하세요.

이 오류는 IaaS VM에서 보고됩니다. 문제의 근본 원인을 확인하려면 Recovery Services 자격 증명 모음 설정으로 이동합니다. **모니터링** 섹션에서 **백업 작업** 을 선택하여 상태를 필터링하고 확인합니다. **실패** 를 선택하여 기본 오류 메시지 정보를 검토합니다. 오류 정보 페이지의 권장 사항에 따라 추가 작업을 수행합니다.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent - 백업 실패: 이 가상 머신은 현재 Azure Backup으로 보호되지 않습니다.

**오류 코드**: UserErrorBcmDatasourceNotPresent <br>
**오류 메시지**: 백업 실패: 이 가상 머신은 현재 Azure Backup으로 보호되지 않습니다.

지정된 가상 머신이 현재 일시 중지 상태가 아니고 Azure Backup으로 보호되는지 확인합니다. 이 문제를 해결하려면 가상 머신이 활성 상태인지 확인한 다음, 작업을 다시 시도하세요.

## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution-for-this-error"></a>이 오류에 대한 해결 방법

VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하면 최신 버전을 가져올 수 있습니다. 또한 서비스와의 통신을 다시 시작하는 데도 도움이 됩니다.

1. VM 서비스(services.msc)에서 Microsoft Azure 게스트 에이전트 서비스가 실행 중인지 확인합니다. Windows Azure 게스트 에이전트 서비스를 다시 시작하고 백업을 시작해 보세요.
2. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되지 않으면 제어판에서 **프로그램 및 기능** 으로 이동하여 Windows Azure 게스트 에이전트 서비스가 설치되어 있는지 확인합니다.
3. Microsoft Azure 게스트 에이전트가 **프로그램 및 기능** 에 표시되면 Windows Azure 게스트 에이전트를 제거합니다.
4. [최신 버전의 에이전트 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.
5. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되는지 확인합니다.
6. 다음과 같이 주문형 백업을 실행합니다.
   - 포털에서 **지금 백업** 을 선택합니다.

또는 VM에서 [Microsoft .NET 4.5가 설치되어 있는지](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 확인합니다. .NET 4.5는 VM 에이전트가 서비스와 통신하는 데 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>솔루션

Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/extensions/update-linux-agent.md)의 지침을 따르세요.

   > [!NOTE]
   > 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장* 합니다. GitHub에서 에이전트 코드를 직접 다운로드한 후 업데이트하는 것은 바람직하지 않습니다. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

2. `ps -e` 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다.

   이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

   - Ubuntu의 경우: `service walinuxagent start`
   - 기타 배포의 경우: `service waagent start`

3. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. 새 테스트 백업을 실행합니다. 오류가 계속되면 VM에서 다음 로그를 수집합니다.

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

waagent의 자세한 로깅이 필요한 경우 다음 단계를 따르세요.

1. /etc/waagent.conf 파일에서 다음 줄을 찾습니다. **자세한 정보 로깅 사용(y|n)**
2. **Logs.Verbose** 값을 *n* 에서 *y* 로 변경합니다.
3. 변경 내용을 저장하고 이 섹션 앞부분에 설명된 단계를 완료하여 waagent를 다시 시작합니다.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM 에이전트 구성 옵션이 설정되지 않았습니다(Linux VM의 경우).

구성 파일(/etc/waagent.conf)은 waagent의 동작을 제어합니다. 구성 파일 옵션 **Extensions.Enable** 을 **y** 로 설정하고 **Provisioning.Agent** 를 **auto** 로 설정해야 백업이 작동합니다.
VM-Agent 구성 파일 옵션의 전체 목록은 <https://github.com/Azure/WALinuxAgent#configuration-file-options>를 참조하세요.

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>애플리케이션 제어 솔루션이 IaaSBcdrExtension.exe를 차단하고 있습니다.

[AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) 또는 다른 애플리케이션 제어 솔루션을 실행하고 있고 규칙이 게시자 또는 경로를 기반으로 하는 경우 **IaaSBcdrExtension.exe** 실행 파일의 실행이 차단될 수 있습니다.

#### <a name="solution-to-this-issue"></a>이 문제에 대한 해결 방법

AppLocker 또는 기타 애플리케이션 제어 소프트웨어에서 `/var/lib` 경로 또는 **IaaSBcdrExtension.exe** 실행 파일을 제외합니다.

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.

VM 백업은 기본 스토리지 계정에 대한 스냅샷 명령 실행을 사용합니다. 스토리지 계정에 액세스할 수 없거나 스냅샷 작업의 실행이 지연되기 때문에 백업이 실패할 수 있습니다.

#### <a name="solution-for-this-issue"></a>이 문제에 대한 해결 방법

다음 조건으로 인해 스냅샷 작업이 실패할 수 있습니다.

| 원인 | 해결 방법 |
| --- | --- |
| VM이 RDP(원격 데스크톱 프로토콜)에서 종료되므로 VM 상태가 잘못 보고됩니다. | RDP에서 VM을 종료하는 경우 VM 상태가 올바른지 여부를 확인하려면 포털을 확인합니다. 올바르지 않으면 VM 대시보드의 **종료** 옵션을 사용하여 포털에서 VM을 종료합니다. |
| VM이 DHCP에서 호스트 또는 패브릭 주소를 가져올 수 없습니다. | IaaS VM 백업이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다. VM이 DHCP 응답 245에서 호스트 또는 패브릭 주소를 가져올 수 없는 경우에는 어떠한 확장도 다운로드하거나 실행할 수 없습니다. 고정 개인 IP가 필요한 경우 **Azure Portal** 또는 **PowerShell** 을 통해 구성해야 하며 VM 내 DHCP 옵션을 사용할 수 있는지 확인합니다. PowerShell을 사용하여 고정 IP 주소를 설정하는 방법에 대해 [자세히 알아보세요](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface).

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>복구 지점 리소스 그룹에서 잠금 제거

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 리소스 옵션** 으로 이동한 다음, AzureBackupRG_`<Geo>`_`<number>` 형식의 복원 지점 컬렉션 리소스 그룹을 선택합니다.
3. **설정** 섹션에서 **잠금** 을 선택하여 잠금을 표시합니다.
4. 잠금을 제거하려면 줄임표를 선택하고 **삭제** 를 선택합니다.

    ![잠금 삭제](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> 복원 지점 컬렉션 정리

잠금을 제거한 후 복원 지점을 정리해야 합니다.

VM의 리소스 그룹 또는 VM 자체를 삭제하는 경우 관리 디스크의 인스턴트 복원 스냅샷은 활성 상태로 유지되며 보존 세트에 따라 만료됩니다. 복원 지점 컬렉션에 저장된 인스턴트 복원 스냅샷(더 이상 필요하지 않은 경우)을 삭제하려면 아래 단계에 따라 복원 지점 컬렉션을 정리합니다.

복원 지점을 정리하려면 다음 방법 중 하나를 따르세요.<br>

- [주문형 백업을 실행하여 복원 지점 컬렉션 정리](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Azure Portal에서 복원 지점 컬렉션 정리](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>주문형 백업을 실행하여 복원 지점 컬렉션 정리

잠금을 제거한 후 주문형 백업을 트리거합니다. 그러면 복원 지점이 자동으로 정리됩니다. 처음에는 이 주문형 작업이 실패하지만, 복원 지점을 수동으로 삭제하는 대신 자동 정리되도록 합니다. 정리 후에 예약된 다음 백업은 성공합니다.

> [!NOTE]
> 주문형 백업을 트리거하고 몇 시간 후에 자동 정리가 수행됩니다. 예약된 백업이 여전히 실패하는 경우 [여기](#clean-up-restore-point-collection-from-azure-portal)에 나열된 단계를 따라 복원 지점 컬렉션을 수동으로 삭제해 보세요.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Azure Portal에서 복원 지점 컬렉션 정리 <br>

리소스 그룹에 대한 잠금으로 인해 지워지지 않은 복원 지점 컬렉션을 수동으로 지우려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **허브** 메뉴에서 **모든 리소스** 를 선택하고 사용자 VM이 있는, AzureBackupRG_`<Geo>`_`<number>` 형식의 리소스 그룹을 선택합니다.

    ![리소스 그룹 선택](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 리소스 그룹을 선택하면 **개요** 창이 표시됩니다.
4. 숨겨진 리소스를 모두 표시하려면 **숨겨진 유형 표시** 옵션을 선택합니다. AzureBackupRG_`<VMName>`_`<number>` 형식의 복원 지점 컬렉션을 선택합니다.

    ![복원 지점 컬렉션을 선택합니다.](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 복원 지점 컬렉션을 정리하려면 **삭제** 를 선택합니다.
6. 백업 작업을 다시 시도합니다.

> [!NOTE]
 >리소스(RP 컬렉션)에 여러 복원 지점이 있는 경우 포털에서 이들을 삭제하면 시간 제한으로 인해 작업이 실패할 수 있습니다. 이것은 일부 복원 지점이 규정된 시간에 삭제되지 않아 작업 시간이 초과되는 알려진 CRP 문제입니다. 그러나 삭제 작업은 일반적으로 2~3회 재시도 후 성공합니다.
