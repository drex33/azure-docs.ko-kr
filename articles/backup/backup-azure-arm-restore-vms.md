---
title: Azure Portal을 사용하여 VM 복원
description: 지역 간 복원 기능을 포함한 Azure Portal을 사용하여 복구 지점에서 Azure 가상 머신을 복원합니다.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 11/16/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: b207fa803831aaa1e53da2c9991517a26fb9c07b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550490"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure Portal에서 Azure VM 데이터를 복원하는 방법

이 문서에서는 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음에 저장된 복구 지점에서 Azure VM 데이터를 복원하는 방법에 대해 설명합니다.

## <a name="restore-options"></a>복원 옵션

Azure Backup은 다양한 VM 복원 방법을 제공합니다.

**복원 옵션** | **세부 정보**
--- | ---
**새 VM 만들기** | 복원 지점에서 기본 VM을 빠르게 만들고, 준비하고, 실행할 수 있습니다.<br/><br/> VM에 대한 이름을 지정하고, VM을 배치할 리소스 그룹과 VNet(가상 네트워크)을 선택하고, 복원된 VM에 대한 스토리지 계정을 지정할 수 있습니다. 새 VM을 원본 VM과 동일한 지역에 만들어야 합니다.<br><br>Azure의 지정된 지역에서 Azure VM SKU를 사용할 수 없거나 다른 문제로 인해 VM 복원이 실패하는 경우 Azure Backup은 지정된 리소스 그룹의 디스크를 계속 복원합니다.
**디스크 복원** | 새 VM을 만드는 데 사용할 수 있는 VM 디스크를 복원합니다.<br/><br/> Azure Backup은 VM을 사용자 지정하고 만드는 데 도움이 되는 템플릿을 제공합니다. <br/><br> 복원 작업은 다운로드하여 사용자 지정 VM 설정을 지정하고 VM을 만드는 데 사용할 수 있는 템플릿을 생성합니다.<br/><br/> 지정한 리소스 그룹에 디스크가 복사됩니다.<br/><br/> 또는 디스크를 기존 VM에 연결하거나 PowerShell을 사용하여 새 VM을 만들 수 있습니다.<br/><br/> 이 옵션은 VM을 사용자 지정하거나, 백업 시 존재하지 않았던 구성 설정을 추가하거나, 템플릿 또는 PowerShell을 사용하여 구성해야 하는 설정을 추가하려는 경우에 유용합니다.
**기존 항목 바꾸기** | 디스크를 복원하고 이 디스크를 사용하여 기존 VM의 디스크를 바꿀 수 있습니다.<br/><br/> 현재 VM이 있어야 합니다. VM이 삭제된 경우에는 이 옵션을 사용할 수 없습니다.<br/><br/> 디스크를 교체하기 전에 Azure Backup은 기존 VM의 스냅샷을 만들어 지정된 준비 위치에 저장합니다. VM에 연결된 기존 디스크가 선택한 복원 지점으로 바뀝니다.<br/><br/> 만든 스냅샷은 보존 정책에 따라 자격 증명 모음에 복사되어 유지됩니다. <br/><br/> 디스크 바꾸기 작업 후에는 원래 디스크가 리소스 그룹에 유지됩니다. 필요하지 않은 경우 원본 디스크를 수동으로 삭제하도록 선택할 수 있습니다. <br/><br/>[사용자 지정 이미지를 사용하여 만들어진](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) VM을 포함하여 암호화되지 않은 관리 VM에 대해 기존 항목 바꾸기가 지원됩니다. 클래식 VM, 관리되지 않는 VM 및 [일반화된 VM](../virtual-machines/windows/upload-generalized-managed.md)에서는 지원되지 않습니다.<br/><br/> 현재 VM보다 많거나 적은 디스크가 복원 지점에 있는 경우 복원 지점의 디스크 수에는 VM 구성만 반영됩니다.<br><br> [사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 또는 [Key Vault](../key-vault/general/overview.md)와 같은 연결된 리소스가 있는 VM에 대해서도 기존 항목 바꾸기가 지원됩니다.
**지역 간(보조 지역)** | 지역 간 복원을 사용하여 보조 지역에서 Azure VM을 복원할 수 있으며, 이 보조 지역은 [Azure 쌍으로 연결된 지역](../best-practices-availability-paired-regions.md#what-are-paired-regions)입니다.<br><br> 백업이 보조 지역에서 수행되는 경우 선택한 복구 지점에 대한 모든 Azure VM을 복원할 수 있습니다.<br><br> 백업 중에는 스냅샷이 보조 지역에 복제되지 않습니다. 자격 증명 모음에 저장된 데이터만 복제됩니다. 따라서 보조 지역 복원은 [자격 증명 모음 계층](about-azure-vm-restore.md#concepts) 복원일 뿐입니다. 보조 지역의 복원 시간은 주 지역에 대한 자격 증명 모음 계층 복원 시간과 거의 동일합니다.  <br><br> 이 기능은 아래 옵션에 사용할 수 있습니다.<br> <li> [VM 만들기](#create-a-vm) <br> <li> [디스크 복원](#restore-disks) <br><br> 현재 [기존 디스크 바꾸기](#replace-existing-disks) 옵션을 지원하지 않습니다.<br><br> 사용 권한<br> 보조 지역에 대한 복원 작업은 Backup Admins 및 App 관리자가 수행할 수 있습니다.

> [!NOTE]
> Azure VM에서 특정 파일과 폴더를 복구할 수도 있습니다. [자세히 알아보기](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Storage 계정

스토리지 계정에 대한 몇 가지 세부 정보:

- **VM 만들기**: 새 VM을 만들면 지정한 스토리지 계정에 VM이 배치됩니다.
- **디스크 복원**: 디스크를 복원하면 지정한 스토리지 계정에 디스크가 복사됩니다. 복원 작업은 다운로드하여 사용자 지정 VM 설정을 지정하는 데 사용할 수 있는 템플릿을 생성합니다. 이 템플릿은 지정된 스토리지 계정에 배치됩니다.
- **디스크 바꾸기**: 기존 VM의 디스크를 바꾸면 Azure Backup이 디스크를 바꾸기 전에 기존 VM의 스냅샷을 만듭니다. 또한 백그라운드 프로세스로서 데이터 전송을 통해 Recovery Services 자격 증명 모음에 스냅샷이 복사됩니다. 그러나 스냅샷 단계가 완료되면 디스크 바꾸기 작업이 트리거됩니다. 디스크 바꾸기 작업 후 원본 Azure VM의 디스크는 작업에 대해 지정된 리소스 그룹에 남아 있으며 VHD는 지정된 스토리지 계정에 저장됩니다. 이러한 VHD와 디스크를 삭제하거나 보존하도록 선택할 수 있습니다.
- **스토리지 계정 위치**: 스토리지 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다. 이러한 계정만 표시됩니다. 위치에 스토리지 계정이 없는 경우 새로 만들어야 합니다.
- **스토리지 형식**: Blob Storage는 지원되지 않습니다.
- **스토리지 중복도**: ZRS(영역 중복 스토리지)는 지원되지 않습니다. 계정에 대한 복제 및 중복도 정보는 계정 이름 뒤의 괄호 안에 표시됩니다.
- **Premium Storage:**
  - 프리미엄이 아닌 VM을 복원하는 경우 Premium Storage 계정은 지원되지 않습니다.
  - 관리 VM을 복원하는 경우 네트워크 규칙으로 구성된 Premium Storage 계정은 지원되지 않습니다.

## <a name="before-you-start"></a>시작하기 전에

VM을 복원(새 VM 만들기)하려면 VM 복원 작업에 대한 올바른 Azure RBAC(Azure 역할 기반 액세스 제어) [권한](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)이 있는지 확인합니다.

권한이 없는 경우 [디스크를 복원](#restore-disks)할 수 있습니다. 디스크가 복원 된 후 복원 작업의 일부로 생성된 [템플릿을 사용](#use-templates-to-customize-a-restored-vm)하여 새 VM을 만들 수 있습니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>복원 지점 선택

1. Azure Portal에서 **백업 센터** 로 이동하고 **개요** 탭에서 **복원** 을 클릭합니다.

    ![복원으로 이동](./media/backup-azure-arm-restore-vms/select-restore.png)

1. **데이터 원본 유형** 으로 **Azure Virtual Machines** 를 선택한 후 백업 인스턴스를 선택합니다.

    ![데이터 원본 유형 선택](./media/backup-azure-arm-restore-vms/virtual-machine-as-datasource.png)

1. VM을 선택하고 **계속** 을 클릭합니다.
1. 표시되는 다음 화면에서 복구에 사용할 복원 지점을 선택합니다.

    ![가상 머신의 복원 지점 선택](./media/backup-azure-arm-restore-vms/restore-point.png)



## <a name="choose-a-vm-restore-configuration"></a>VM 복원 구성 선택

1. **가상 머신 복원** 에서 복원 옵션을 선택합니다.
    - **새로 만들기**: 새 VM을 만들려면 이 옵션을 사용합니다. 간단한 설정으로 VM을 만들거나, 디스크를 복원하고 사용자 지정된 VM을 만들 수 있습니다.
    - **기존 항목 바꾸기**: 기존 VM의 디스크를 바꾸려면 이 옵션을 사용합니다.

        ![가상 머신 구성 복원 마법사](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. 선택한 복원 옵션에 대한 설정을 지정합니다.

## <a name="create-a-vm"></a>VM 만들기

[복원 옵션](#restore-options) 중 하나로, 복원 지점의 기본 설정을 사용하여 VM을 빠르게 만들 수 있습니다.

1. **가상 머신 복원** > **새로 만들기** > **복원 유형** 에서 **가상 머신 만들기** 를 선택합니다.
1. **가상 머신 이름** 에서 구독에 없는 VM을 지정합니다.
1. **리소스 그룹** 에서 새 VM에 대해 기존 리소스 그룹을 선택하거나, 새 리소스 그룹을 전역 고유 이름으로 만듭니다. 이미 있는 이름을 할당하면 Azure에서 VM과 동일한 이름을 그룹에 할당합니다.
1. **가상 네트워크** 에서 VM을 배치할 VNet를 선택합니다. 활성 상태이 고 선호도 그룹과 연결 되지 않은 자격 증명 모음과 동일한 위치에 있는 구독과 연결 된 모든 Vnet 표시 됩니다. 서브넷을 선택합니다.

   기본적으로 첫 번째 서브넷이 선택됩니다.

1. **스테이징 위치** 에서 VM에 대한 스토리지 계정을 지정합니다. [자세히 알아보기](#storage-accounts).

    ![구성 복원 마법사 - 복원 옵션 선택](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. **복원** 을 선택하여 복원 작업을 트리거합니다.

>[!Note]
>NSG 설정을 수정하기 전에 VM 복원 작업이 완료되었는지 확인합니다. 복원 [작업 추적에](#track-the-restore-operation)대해 알아봅니다.

## <a name="restore-disks"></a>디스크 복원

[복원 옵션](#restore-options) 중 하나로, 복원 지점에서 디스크를 만들 수 있습니다. 그런 다음, 이 디스크를 사용하여 다음 작업 중 하나를 수행할 수 있습니다.

- 복원 작업 중 생성된 템플릿을 사용하여 설정을 사용자 지정하고 VM 배포를 트리거합니다. 기본 템플릿 설정을 편집하고 VM 배포용 템플릿을 제출합니다.
- [복원된 디스크](../virtual-machines/windows/attach-managed-disk-portal.md)를 기존 VM에 연결합니다.
- PowerShell을 사용하여 복원된 디스크에서 [새 VM을 만듭니다](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

1. **복원 구성** > **새로 만들기** > **복원 유형** 에서 **복원 디스크** 를 선택합니다.
1. **리소스 그룹** 에서 복원된 디스크에 대해 기존 리소스 그룹을 선택하거나, 새 리소스 그룹을 전역 고유 이름으로 만듭니다.
1. **스테이징 위치** 에서 VHD를 복사할 스토리지 계정을 지정합니다. [자세히 알아보기](#storage-accounts).

    ![리소스 그룹 및 스테이징 위치 선택](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. **복원** 을 선택하여 복원 작업을 트리거합니다.

가상 머신에서 관리 디스크를 사용하는 경우 **가상 머신 만들기** 옵션을 선택하면 Azure Backup에서 지정된 스토리지 계정을 사용하지 않습니다. **디스크 복원** 및 **즉시 복원** 의 경우 스토리지 계정은 템플릿을 저장하는 데만 사용됩니다. 지정된 리소스 그룹에 관리 디스크가 만들어집니다.
가상 머신에서 비관리 디스크를 사용하는 경우 스토리지 계정에 Blob으로 비관리 디스크가 복원됩니다.

### <a name="use-templates-to-customize-a-restored-vm"></a>템플릿을 사용하여 복원된 VM 사용자 지정

디스크가 복원되면 복원 작업의 일부로 생성된 템플릿을 사용하여 새 VM을 사용자 지정하고 만듭니다.

1. **백업 작업** 에서 관련 복원 작업을 선택합니다.

1. **복원** 에서 **템플릿 배포** 를 선택하여 템플릿 배포를 시작합니다.

    ![복원 작업 드릴 다운](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)
   
   >[!Note]
   >**스토리지 계정 키 액세스 허용** 이 사용되지 않는 SAS(공유 액세스 서명)의 경우 **템플릿 배포** 를 선택하면 템플릿이 배포되지 않습니다.

1. 템플릿에 제공된 VM 설정을 사용자 지정하려면 **템플릿 편집** 을 선택합니다. 사용자 지정을 추가하려면 **매개 변수 편집** 을 선택합니다.
    - 사용자 지정 템플릿에서 리소스를 배포하는 방법에 대해 [자세히 알아보세요](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).
    - 템플릿 작성에 대해 [자세히 알아보세요](../azure-resource-manager/templates/syntax.md).

   ![템플릿 배포 로드](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. VM에 대한 사용자 지정 값을 입력하고, **사용 약관** 에 동의하고, **구매** 를 선택합니다.

   ![템플릿 배포 제출](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>기존 디스크 바꾸기

[복원 옵션](#restore-options) 중 하나로, 기존 VM 디스크를 선택한 복원 지점으로 바꿀 수 있습니다. 모든 복원 옵션을 [검토](#restore-options)합니다.

1. **복원 구성** 에서 **기존 항목 바꾸기** 를 선택합니다.
1. **복원 유형** 에서 **디스크 바꾸기** 를 선택합니다. 이는 기존 VM 디스크를 바꾸는 데 사용할 복원 지점입니다.
1. **준비 위치** 에서 복원 프로세스 중 현재 관리 디스크의 스냅샷을 저장해야 하는 위치를 지정합니다. [자세히 알아보기](#storage-accounts).

   ![기존 항목을 바꾸는 구성 복원 마법사](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>지역 간 복원

[복원 옵션](#restore-options) 중 하나인 CRR(지역 간 복원)을 사용하면 Azure 쌍으로 연결된 지역인 보조 지역에서 Azure VM을 복원할 수 있습니다.

기능 사용을 시작하려면 [시작하기 전에 섹션](./backup-create-rs-vault.md#set-cross-region-restore)을 참조하세요.

CRR을 사용하도록 설정되어 있는지 확인하려면 [지역 간 복원 구성](backup-create-rs-vault.md#configure-cross-region-restore)의 지침을 따르세요.

### <a name="view-backup-items-in-secondary-region"></a>보조 지역에서 백업 항목 보기

CRR을 사용하도록 설정된 경우 보조 지역에서 백업 항목을 볼 수 있습니다.

1. 포털에서 **Recovery Services 자격 증명 모음**  >  **백업 항목** 으로 이동합니다.
1. **보조 지역** 을 선택하여 보조 지역의 항목을 봅니다.

>[!NOTE]
>CRR 기능을 지원하는 백업 관리 유형만 목록에 표시됩니다. 현재 보조 지역에 대한 보조 지역 데이터 복원만 지원됩니다.<br></br>Azure VM용 CRR은 Azure Managed VM(암호화된 Azure VM 포함)에 지원됩니다. 지역 [간 복원을 지원하는 관리 유형을](/azure/backup/backup-support-matrix#cross-region-restore)참조하세요.

![보조 지역의 가상 머신](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![보조 지역 선택](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>보조 지역의 복원

보조 지역 복원 사용자 환경은 주 지역 복원 사용자 환경과 유사합니다. 복원 구성 창에서 세부 정보를 구성하여 복원을 구성하는 경우 보조 지역 매개 변수만 제공하라는 메시지가 표시됩니다.

현재 [RA-GRS(읽기 액세스 지역 중복 스토리지)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 복제는 15분이나, 보조 지역 [RPO](azure-backup-glossary.md#rpo-recovery-point-objective)는 주 지역에서 최대 12시간입니다.

![복원할 VM 선택](./media/backup-azure-arm-restore-vms/sec-restore.png)

![복원 지점 선택](./media/backup-azure-arm-restore-vms/sec-rp.png)

- VM을 복원하고 만들려면 [VM 만들기](#create-a-vm)를 참조하세요.
- 디스크로 복원하려면 [디스크 복원](#restore-disks)을 참조하세요.

>[!NOTE]
>
>- 복원이 트리거되고 데이터 전송 단계에서는 복원 작업을 취소할 수 없습니다.
>- 지역 간 복원 기능은 CMK 지원 Recovery Services 자격 증명 모음에 백업되지 않은 CMK(고객 관리 키) 지원 Azure VM을 보조 지역의 비 CMK 지원 VM으로 복원합니다.
>- 보조 지역에서 복원하는 데 필요한 Azure 역할은 주 지역의 경우와 동일합니다.
>- Azure VM을 복원하는 동안 Azure Backup은 보조 지역의 가상 네트워크 설정을 자동으로 구성합니다. 템플릿을 배포하는 동안 [디스크를 복원](#restore-disks)하는 경우 보조 지역에 해당하는 가상 네트워크 설정을 제공해야 합니다.

[Azure 영역 고정 VM](../virtual-machines/windows/create-portal-availability-zone.md)은 동일한 지역의 모든 [가용성 영역](../availability-zones/az-overview.md)에서 복원할 수 있습니다.

복원 프로세스에서 **가용성 영역** 옵션을 볼 수 있습니다. 먼저 기본 영역을 볼 수 있습니다. 다른 영역을 선택하려면 원하는 영역의 번호를 선택합니다. 고정 영역을 사용할 수 없는 경우 백업된 데이터가 영역별로 복제되지 않기 때문에 데이터를 다른 영역으로 복원할 수 없습니다. 가용성 영역의 복원은 자격 증명 모음 계층의 복구 지점에서만 가능합니다.

요약하면 가용성 **영역은**
 - 원본 VM이 영역으로 고정되고 암호화되지 않음
 - 복구 지점은 자격 증명 모음 계층에만 있습니다(스냅샷만 또는 스냅샷 및 자격 증명 모음 계층은 지원되지 않음).
 - 복구 옵션은 새 VM을 만들거나 디스크를 복원하는 것입니다(디스크 바꾸기 옵션은 원본 데이터를 대체하므로 가용성 영역 옵션은 적용되지 않음).
 - 자격 증명 모음의 스토리지 중복성이 ZRS인 경우 동일한 지역에 VM/디스크 만들기(원본 VM이 영역 고정된 경우에도 자격 증명 모음의 스토리지 중복성이 GRS인 경우 작동하지 않습니다.)
 - 지역 간 복원 및 쌍을 이루는 지역에서 영역을 지원하는 경우 자격 증명 모음의 스토리지 중복성을 사용하도록 설정된 경우 쌍을 이루는 지역에 VM/디스크 만들기


![가용성 영역 선택](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>보조 지역 복원 작업 모니터링

1. 포털에서 **Recovery Services 자격 증명 모음**  >  **백업 작업** 으로 이동합니다.
1. **보조 지역** 을 선택하여 보조 지역의 항목을 봅니다.

    ![필터링된 백업 작업](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>비관리 VM 및 디스크를 관리로 복원

복원 중 [비관리 디스크](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks)를 [관리 디스크](../virtual-machines/managed-disks-overview.md)로 복원하는 옵션이 제공됩니다. 기본적으로 비관리 VM/디스크는 비관리 VM/디스크로 복원됩니다. 그러나 이제 관리 VM/디스크로 복원하는 것도 가능합니다. 이러한 복원은 스냅샷 단계에서 트리거되지 않고 자격 증명 모음 단계에서만 트리거됩니다. 암호화된 비관리 VM에는 이 기능을 사용할 수 없습니다.

![관리 디스크로 복원](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>특수 구성으로 VM 복원

VM을 복원해야 하는 일반적인 시나리오는 여러 가지가 있습니다.

**시나리오** | **지침**
--- | ---
**하이브리드 사용 혜택을 사용하여 VM 복원** | Windows VM에서 [HUB(하이브리드 사용 혜택) 라이선스](../virtual-machines/windows/hybrid-use-benefit-licensing.md)를 사용하는 경우, 디스크를 복원하고 제공된 템플릿(**라이선스 유형** 이 **Windows_Server** 로 설정됨) 또는 PowerShell을 사용하여 새 VM을 만듭니다.  이 설정은 VM을 만든 후에 적용할 수도 있습니다.
**Azure 데이터 센터 재해 중 VM 복원** | 자격 증명 모음에서 GRS를 사용하고 VM에 대한 기본 데이터 센터의 작동이 중단되면, Azure Backup에서 쌍을 이루는 데이터 센터로 백업된 VM을 복원할 수 있습니다. 쌍을 이루는 데이터 센터에서 스토리지 계정을 선택하고 정상적으로 복원합니다. Azure Backup은 쌍을 이루는 지역에서 컴퓨팅 서비스를 사용하여 복원된 VM을 만듭니다. 데이터 센터 복원력에 대해 [자세히 알아보세요](/azure/architecture/resiliency/recovery-loss-azure-region).<br><br> 자격 증명 모음에서 GRS를 사용하는 경우 새 기능인 [지역 간 복원](#cross-region-restore)을 선택할 수 있습니다. 이를 통해 전체 또는 부분 중단 시나리오에서 또는 중단이 전혀 없는 경우에도 두 번째 지역으로 복원할 수 있습니다.
**완전 복원** | Azure VM과 온-프레미스 하이퍼바이저의 주요 차이점은 Azure에서 사용할 수 있는 VM 콘솔이 없다는 것입니다. BMR(완전 복구) 유형 백업을 사용한 복구와 같은 특정 시나리오에서는 콘솔이 필요합니다. 하지만 자격 증명 모음의 VM 복원이 BMR로 완전히 대체됩니다.
**특수 네트워크 구성이 있는 VM 복원** | 특수 네트워크 구성에는 내부 또는 외부 부하 분산을 사용하거나, 여러 NICS를 사용하거나, 예약된 여러 IP 주소를 사용하는 VM이 포함되어 있습니다. [디스크 복원 옵션](#restore-disks)을 사용하여 이러한 VM을 복원합니다. 이 옵션은 VHD 복사본을 지정된 스토리지 계정에 만듭니다. 그러면 구성에 따라 [내부](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) 또는 [외부](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) 부하 분산 장치, [여러 NICS](../virtual-machines/windows/multiple-nics.md) 또는 [예약된 여러 IP 주소](../virtual-network/ip-services/virtual-network-multiple-ip-addresses-powershell.md)가 있는 VM을 만들 수 있습니다.
**NIC/서브넷의 NSG(네트워크 보안 그룹)** | Azure VM 백업은 vnet, 서브넷 및 NIC 수준에서 NSG 정보의 백업 및 복원을 지원합니다.
**영역 고정 VM** | Azure Backup을 사용하여 영역에 고정된 Azure VM을 백업하는 경우 고정된 동일한 영역에서 복원할 수 있습니다. [자세한 정보](../availability-zones/az-overview.md)
**모든 가용성 집합에서 VM 복원** | 포털에서 VM을 복원하는 경우 가용성 집합을 선택할 수 있는 옵션이 없습니다. 복원된 VM에는 가용성 집합이 없습니다. 디스크 복원 옵션을 사용하는 경우 제공된 템플릿 또는 PowerShell을 사용하여 디스크에서 VM을 만들 때 [가용성 집합을 지정](../virtual-machines/windows/tutorial-availability-sets.md)할 수 있습니다.
**SQL VM 등의 특수 VM 복원** | Azure VM 백업을 사용하여 SQL VM을 백업한 다음, VM 복원 옵션을 사용하거나 디스크를 복원한 후 VM을 만드는 경우 [여기](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash)에 언급된 대로 새로 만든 VM을 SQL 공급자에 등록해야 합니다. 이렇게 하면 복원된 VM이 SQL VM으로 변환됩니다.

### <a name="restore-domain-controller-vms"></a>도메인 컨트롤러 VM 복원

**시나리오** | **지침**
--- | ---
**단일 도메인에서 단일 도메인 컨트롤러 VM 복원** | 다른 VM과 마찬가지로 VM을 복원합니다. 다음 사항에 유의합니다.<br/><br/> Azure VM은 Active Directory의 관점에서 다른 VM과 동일합니다.<br/><br/> Active Directory 복원 모드(DSRM)도 사용할 수 있으므로 모든 Directory Services 복원 모드를 실행할 수 있습니다. 가상화된 도메인 컨트롤러에 대한 백업 및 복원 고려 사항에 대해 [자세히 알아보세요](#post-restore-steps).
**단일 도메인에서 여러 도메인 컨트롤러 VM 복원** | 네트워크를 통해 동일한 도메인의 다른 도메인 컨트롤러에 연결할 수 있는 경우 도메인 컨트롤러를 다른 VM처럼 복원할 수 있습니다. 도메인에서 마지막으로 남아 있는 도메인 컨트롤러이거나 격리된 네트워크에서 복구가 수행되면 [포리스트 복구](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)를 사용합니다.
**다중 도메인 구성에서 단일 도메인 컨트롤러 VM 복원** |  [PowerShell을 사용](backup-azure-vms-automation.md#restore-the-disks)하여 디스크 복원 및 VM 만들기  
**단일 포리스트에 여러 도메인 복원** | [포리스트 복구](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)를 사용하는 것이 좋습니다.

자세한 내용은 [Active Directory 도메인 컨트롤러 백업 및 복원](active-directory-backup-restore.md)을 참조하세요.

## <a name="restore-vms-with-managed-identities"></a>관리 ID를 통해 VM 복원

관리 ID를 사용하면 사용자가 자격 증명을 유지할 필요가 없습니다. 관리 ID는 Azure AD(Azure Active Directory) 인증을 지원하는 리소스에 연결할 때 사용할 애플리케이션의 ID를 제공합니다.  

Azure Backup은 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 관리되는 Azure VM을 복원할 수 있는 유연성을 제공합니다. 아래 그림과 같이 [시스템 관리 ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 또는 사용자 관리 ID를 선택할 수 있습니다. 이는 Azure VM의 [**구성 복원** 블레이드](#create-a-vm)에서 입력 매개 변수 중 하나로 도입되었습니다. 입력 매개 변수 중 하나로 사용되는 관리 ID는 스토리지 계정에 액세스하는 데만 사용되며, 다른 Azure 리소스 제어가 아니라 복원 중에 준비 위치로 사용됩니다. 이러한 관리 ID는 자격 증명 모음에 연결되어야 합니다.

:::image type="content" source="./media/backup-azure-arm-restore-vms/select-system-managed-identities-or-user-managed-identities.png" alt-text="시스템 관리 ID 또는 사용자 관리 ID의 선택을 보여 주는 스크린샷":::

시스템이 할당한 또는 사용자가 할당한 관리 ID를 선택하려는 경우 대상 준비 스토리지 계정에서 관리 ID에 대해 아래 작업을 확인합니다.

```json
"permissions": [
            {
                "actions": [
                    "Microsoft.Authorization/*/read",
                    "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
                    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
                    "Microsoft.Storage/storageAccounts/blobServices/containers/write"
                ],
                "notActions": [],
                "dataActions": [
                    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
                    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
                    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
                ],
                "notDataActions": []
            }
```

또는 성공적인 복원 작업을 위한 [스토리지 계정 백업 기여자](./blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts) 및 [스토리지 Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)를 둘 수 있도록 준비 위치(스토리지 계정)에 역할 할당을 추가합니다.

:::image type="content" source="./media/backup-azure-arm-restore-vms/add-role-assignment-on-staging-location.png" alt-text="스테이징 위치에 역할 할당을 추가하는 스크린샷.":::

아래 그림에 제공된 대로 MSI 리소스 ID로 입력을 제공하여 [사용자 관리 ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 선택할 수도 있습니다.   

:::image type="content" source="./media/backup-azure-arm-restore-vms/select-user-managed-identity-by-providing-input-as-msi-resource-id.png" alt-text="MSI 리소스 ID로 입력을 제공하여 사용자 관리 ID를 선택하는 스크린샷":::

>[!Note]
>지원은 관리되는 VM에만 사용할 수 있으며, 클래식 VM 및 관리되지 않는 VM은 지원되지 않습니다. [방화벽으로 제한된 스토리지 계정](../storage/common/storage-network-security.md?tabs=azure-portal)의 경우 시스템 MSI만 지원됩니다.
>
>지역 간 복원은 관리 ID에서 지원되지 않습니다.
>
>현재 모든 Azure 퍼블릭 및 국가별 클라우드 지역에서 사용할 수 있습니다.

## <a name="track-the-restore-operation"></a>복원 작업 추적

복원 작업이 트리거되면 백업 서비스에서 추적 작업을 만듭니다. Azure Backup에서 작업에 대한 알림을 포털에 표시합니다. 표시되지 않는 경우 **알림** 기호를 선택한 다음, **활동 로그의 추가 이벤트** 를 선택하여 복원 프로세스 상태를 확인합니다.

![트리거된 복원](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 다음과 같이 복원을 추적합니다.

1. 작업(job)의 작업(operation)을 보려면 알림 하이퍼링크를 선택합니다. 또는 자격 증명 모음에서 **백업 작업** 을 선택한 다음, 관련 VM을 선택합니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. 복원 진행 상황을 모니터링하려면 상태가 **진행 중** 인 복원 작업을 선택합니다. 복원 진행 상황에 대한 정보를 보여주는 진행률 표시줄이 표시됩니다.

    - **예상 복원 시간** 은 처음에 복원 작업을 완료하는 데 소요되는 시간을 제공합니다. 작업이 진행됨에 따라 소요 시간이 감소하고, 복원 작업이 완료되면 0에 도달합니다.
    - **복원 비율**: 수행되는 복원 작업에 대한 백분율을 표시합니다.
    - **전송된 바이트 수**: 새 VM을 만들어 복원하는 경우 전송될 총 바이트 수와 대비하여 전송된 바이트 수를 표시합니다.

## <a name="post-restore-steps"></a>복원 후 단계

VM 복원 후 주의해야 할 몇 가지 사항이 있습니다.

- 백업 구성 중에 제공되는 확장이 설치되지만 사용하도록 설정되어 있지 않습니다. 문제가 있으면 확장을 다시 설치합니다.
- 백업된 VM에 고정 IP 주소가 있으면 복원된 VM에서 동적 IP 주소를 사용하도록 하여 충돌을 방지합니다. [고정 IP 주소는 복원된 VM에 추가](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)할 수 있습니다.
- 복원된 VM에는 가용성 집합이 없습니다. 디스크 복원 옵션을 사용하는 경우 제공된 템플릿 또는 PowerShell을 사용하여 디스크에서 VM을 만들 때 [가용성 집합을 지정](../virtual-machines/windows/tutorial-availability-sets.md)할 수 있습니다.
- Ubuntu와 같은 cloud-init 기반 Linux 배포를 사용하면 보안상의 이유로 복원 후에 암호가 차단됩니다. 복원된 VM에서 VMAccess 확장을 사용하여 [암호를 재설정](/troubleshoot/azure/virtual-machines/reset-password)하세요. 복원 후에 암호를 다시 설정할 필요가 없도록 이러한 배포에서 SSH 키를 사용하는 것이 좋습니다.
- VM이 도메인 컨트롤러와의 관계가 끊어져 복원된 후 VM에 액세스할 수 없는 경우 아래 단계에 따라 VM을 불러옵니다.
  - OS 디스크를 복구된 VM에 데이터 디스크로 연결합니다.
  - Azure 에이전트가 응답하지 않는 경우 이 [링크](/troubleshoot/azure/virtual-machines/install-vm-agent-offline)를 따라 수동으로 VM 에이전트를 설치합니다.
  - VM에서 직렬 콘솔 액세스를 사용하도록 설정하여 VM에 대한 명령줄 액세스 허용

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - VM이 다시 빌드되면 Azure Portal을 사용하여 로컬 관리자 계정 및 암호 초기화
  - 직렬 콘솔 액세스 및 CMD를 사용하여 도메인에서 VM 연결 해제

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- VM이 연결 해제되고 다시 시작되면 로컬 관리자 자격 증명을 사용하여 VM에 RDP하고 VM을 도메인에 다시 연결할 수 있습니다.

## <a name="backing-up-restored-vms"></a>복원된 VM 백업

- VM을 원래 백업한 VM과 동일한 이름의 리소스 그룹에 복원한 경우 복원 후에도 VM에서 계속 백업됩니다.
- VM을 다른 리소스 그룹에 복원했거나 복원된 VM에 다른 이름을 지정한 경우 복원된 VM에 대한 백업을 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

- 복원 프로세스 중에 문제가 발생하는 경우 일반적인 문제 및 오류를 [검토](backup-azure-vms-troubleshoot.md#restore)합니다.
- VM이 복원되면 [가상 머신 관리](backup-azure-manage-vms.md)에 대해 알아봅니다.