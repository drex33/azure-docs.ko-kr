---
title: 암호화된 Azure VM 복원
description: Azure Backup 서비스를 사용하여 암호화된 Azure VM을 복원하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: eb16549a88754c7015b54f016526c6b4ae1530ef
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133438501"
---
# <a name="restore-encrypted-azure-virtual-machines"></a>암호화된 Azure Virtual Machines 복원

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 암호화된 디스크로 Windows 또는 Linux Azure VM(가상 머신)을 복원하는 방법을 설명합니다. 자세한 내용은 [Azure VM 백업 암호화](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups)를 참조하세요.


## <a name="before-you-start"></a>시작하기 전에

암호화된 VM의 복원을 시작하기 전에 알려진 제한 사항을 검토합니다.

- 동일한 구독 내에서 ADE 암호화된 VM을 백업하고 복원할 수 있습니다.
- Azure Backup은 독립 실행형 키를 사용하여 암호화된 VM을 지원합니다. VM을 암호화하는 데 사용되는 인증서의 일부인 키는 현재 지원되지 않습니다.
- ADE 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 파일 및 폴더를 복원하려면 전체 VM을 복구해야 합니다.
- VM을 복원하는 경우 ADE 암호화된 VM에 대해 [기존 VM 바꾸기](backup-azure-arm-restore-vms.md#restore-options) 옵션을 사용할 수 없습니다. 이 옵션은 암호화되지 않은 관리 디스크에 대해서만 지원됩니다.


## <a name="restore-an-encrypted-vm"></a>암호화된 VM 복원

암호화된 VM은 VM 디스크를 복원하고 아래에 설명된 대로 가상 머신 인스턴스를 만들어야만 복원할 수 있습니다. **기존 VM의 기존 디스크 교체**, **복원 지점에서 VM 만들기** 및 **파일 또는 폴더 수준 복원** 은 현재 지원되지 않습니다.
 
암호화된 VM을 복원하려면 다음 단계를 따릅니다.

### <a name="step-1-restore-the-vm-disk"></a>**1단계**: VM 디스크 복원

1. **복원 구성** > **새로 만들기** > **복원 유형** 에서 **복원 디스크** 를 선택합니다.
1. **리소스 그룹** 에서 복원된 디스크에 대해 기존 리소스 그룹을 선택하거나, 새 리소스 그룹을 전역 고유 이름으로 만듭니다.
1. **스테이징 위치** 에서 VHD가 복사되어야 하는 스토리지 계정을 지정합니다. [자세히 알아보기](backup-azure-arm-restore-vms.md#storage-accounts).

    ![리소스 그룹 및 스테이징 위치 선택](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. **복원** 을 선택하여 복원 작업을 트리거합니다.

가상 머신에서 관리 디스크를 사용하는 경우 **가상 머신 만들기** 옵션을 선택하면 Azure Backup에서 지정된 스토리지 계정을 사용하지 않습니다. **디스크 복원** 및 **즉시 복원** 의 경우 스토리지 계정은 템플릿을 저장하는 데만 사용됩니다. 지정된 리소스 그룹에 관리 디스크가 만들어집니다.
가상 머신에서 비관리 디스크를 사용하는 경우 스토리지 계정에 Blob으로 비관리 디스크가 복원됩니다.

   > [!NOTE]
   > VM 디스크를 복원한 후에는 다시 만들지 않고도 원래 VM의 OS 디스크를 복원된 VM 디스크로 수동 전환할 수 있습니다. [자세한 정보](https://azure.microsoft.com/blog/os-disk-swap-managed-disks/).

### <a name="step-2-recreate-the-virtual-machine-instance"></a>**2단계**: 가상 머신 인스턴스 다시 만들기 

다음 작업 중 하나를 수행합니다.

- 복원 작업 중에 생성된 템플릿을 사용하여 VM 설정을 사용자 지정하고 VM 배포를 트리거합니다. [자세히 알아봅니다](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
  >[!NOTE]
   >템플릿을 배포하는 동안 저장소 계정 컨테이너와 공용/개인 설정을 확인합니다.
- PowerShell을 사용하여 복원된 디스크에서 새 VM을 만듭니다. [자세히 알아봅니다](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

### <a name="step-3-restore-an-encrypted-linux-vm"></a>**3단계**: 암호화된 Linux VM 복원

데이터 디스크가 열리고 탑재되도록 ADE 확장을 다시 설치합니다.

## <a name="cross-region-restore-for-an-encrypted-azure-vm"></a>암호화된 Azure VM에 대한 교차 지역 복원

Azure Backup은 [Azure 쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md)으로 암호화된 Azure VM의 교차 지역 복원을 지원합니다. 암호화된 VM에 대해 [교차 지역 복원을 사용하도록 설정](backup-create-rs-vault.md#configure-cross-region-restore)하는 방법을 알아봅니다.

## <a name="move-an-encrypted-azure-vm"></a>암호화된 Azure VM 이동

자격 증명 모음 또는 리소스 그룹 간에 암호화된 VM을 이동하는 것은 백업된 Azure Virtual Machine을 이동하는 것과 같습니다. 다음을 참조하세요.

- [Azure 가상 머신을 다른 Recovery Services 자격 증명 모음으로 이동하는 단계](backup-azure-move-recovery-services-vault.md#move-an-azure-virtual-machine-to-a-different-recovery-service-vault)
- [Azure Virtual Machine을 다른 리소스 그룹 또는 구독으로 이동하는 단계](../azure-resource-manager/management/move-resource-group-and-subscription.md)


## <a name="next-steps"></a>다음 단계

문제가 발생하는 경우 다음 문서를 검토하세요.

- 암호화된 Azure VM 백업 및 복원 시 [일반적인 오류](backup-azure-vms-troubleshoot.md)
- [Azure VM 에이전트/백업 확장](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 문제



