---
title: 암호화된 Azure VM 백업 및 복원
description: Azure Backup 서비스를 사용하여 암호화된 Azure VM을 백업하고 복원하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: ef23abb7ba25675ce267fb6a4b0e273a1cc445a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528123"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>암호화된 Azure 가상 머신 백업 및 복원

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 암호화된 디스크로 Windows 또는 Linux Azure VM(가상 머신)을 백업하고 복원하는 방법을 설명합니다. 자세한 내용은 [Azure VM 백업 암호화](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups)를 참조하세요.

## <a name="encryption-using-platform-managed-keys"></a>플랫폼 관리형 키를 사용하여 암호화

기본적으로 VM의 모든 디스크는 [스토리지 서비스 암호화](../storage/common/storage-service-encryption.md)를 사용하는 PMK(플랫폼 관리형 키)를 사용하여 미사용 상태로 자동으로 암호화됩니다. 최종에서 암호화를 지원하는 데 필요한 특정 작업 없이 Azure Backup을 사용하여 이러한 VM을 백업할 수 있습니다. 플랫폼 관리형 키를 사용하여 암호화하는 방법에 대한 자세한 내용은 [이 문서를 참조하세요](../virtual-machines/disk-encryption.md#platform-managed-keys).

![암호화된 디스크](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>고객 관리형 키를 사용하여 암호화

CMK(고객 관리형 키)를 사용하여 디스크를 암호화하는 경우 디스크 암호화에 사용되는 키는 Azure Key Vault에 저장되며 사용자가 관리합니다. CMK를 사용하는 SSE(스토리지 서비스 암호화)는 ADE(Azure Disk Encryption) 암호화와 다릅니다. ADE는 운영 체제의 암호화 도구를 사용합니다. SSE는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 또는 이미지를 사용할 수 있도록 합니다.

고객 관리형 키를 사용하여 디스크를 암호화하는 VM의 백업 또는 복원에 대한 명시적 작업을 수행할 필요가 없습니다. 자격 증명 모음에 저장된 이러한 VM에 대한 백업 데이터는 [자격 증명 모음에서 사용되는 암호화](encryption-at-rest-with-cmk.md)와 동일한 방법으로 암호화됩니다.

고객 관리형 키를 사용하여 관리 디스크를 암호화하는 방법에 대한 자세한 내용은 [이 문서](../virtual-machines/disk-encryption.md#customer-managed-keys)를 참조하세요.

## <a name="encryption-support-using-ade"></a>ADE를 사용하여 암호화 지원

Azure Backup은 ADE(Azure Disk Encryption)로 암호화된 OS/데이터 디스크가 있는 Azure VM의 백업을 지원합니다. ADE는 Windows VM 암호화를 위해 BitLocker를 사용하고 Linux VM의 경우 dm-crypt 기능을 사용합니다. ADE은 Azure Key Vault와 통합되어 디스크 암호화 키와 비밀을 관리합니다. Key Vault KEK(키 암호화 키)를 사용하여 추가 보안 계층을 추가하고 암호화 비밀을 Key Vault에 쓰기 전에 암호화할 수 있습니다.

다음 표에 요약된 것처럼 Azure AD 앱을 사용하거나 사용하지 않고 ADE를 사용하여 Azure Backup에서 Azure VM을 백업하고 복원할 수 있습니다.

**VM 디스크 유형** | **ADE(BEK/dm-crypt)** | **ADE 및 KEK**
--- | --- | ---
**비관리형** | 예 | 예
**관리**  | 예 | 예

- [ADE](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md) 및 [kek](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)에 대해 자세히 알아보세요.
- Azure VM 디스크 암호화에 대한 [FAQ](../security/fundamentals/azure-disk-encryption-vms-vmss.md)를 참조하세요.

### <a name="limitations"></a>제한 사항

- 동일한 구독 내에서 ADE 암호화된 VM을 백업하고 복원할 수 있습니다.
- Azure Backup은 독립 실행형 키를 사용하여 암호화된 VM을 지원합니다. VM을 암호화하는 데 사용되는 인증서의 일부인 키는 현재 지원되지 않습니다.
- Azure Backup은 Azure 쌍을 이루는 지역으로 암호화된 Azure VM의 교차 지역 복원을 지원합니다. 자세한 내용은 [지원 매트릭스](./backup-support-matrix.md#cross-region-restore)를 참조하세요.
- ADE 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 파일 및 폴더를 복원하려면 전체 VM을 복구해야 합니다.
- VM을 복원하는 경우 ADE 암호화된 VM에 대해 [기존 VM 바꾸기](backup-azure-arm-restore-vms.md#restore-options) 옵션을 사용할 수 없습니다. 이 옵션은 암호화되지 않은 관리 디스크에 대해서만 지원됩니다.

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 다음을 수행합니다.

1. ADE를 사용하는 [Windows](../virtual-machines/linux/disk-encryption-overview.md) 또는 [Linux](../virtual-machines/linux/disk-encryption-overview.md) VM이 하나 이상 있는지 확인합니다.
2. Azure VM 백업에 대한 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.
3. Recovery Services Backup 자격 증명 모음이 없는 경우 [만듭니다](backup-create-rs-vault.md).
4. 백업이 이미 활성화된 VM에 암호화를 활성화하는 경우 중단 없이 백업을 계속할 수 있도록 Key Vault에 액세스할 수 있는 권한을 Backup에 제공해야 합니다. 이러한 사용 권한을 할당하는 방법에 대해 [자세히 알아보세요](#provide-permissions).

또한 경우에 따라 몇 가지 작업을 수행해야 할 수도 있습니다.

- **VM에 VM 에이전트 설치**: Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. Azure Marketplace 이미지에서 VM을 만든 경우 에이전트가 설치되어 실행됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우에는 [에이전트를 수동으로 설치](backup-azure-arm-vms-prepare.md#install-the-vm-agent)해야 할 수 있습니다.

## <a name="configure-a-backup-policy"></a>백업 정책 구성

1. Recovery Services 백업 자격 증명 모음을 아직 만들지 않은 경우 [다음 지침](backup-create-rs-vault.md)을 따르세요.
1. 백업 센터로 이동하고 **개요** 탭에서 **+백업** 을 클릭합니다.

    ![백업 창](./media/backup-azure-vms-encryption/select-backup.png)

1. **데이터 원본 유형** 으로 **Azure Virtual Machines** 를 선택하고, 만든 자격 증명 모음을 선택한 후에 **계속** 을 클릭합니다.

     ![시나리오 창](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. 자격 증명 모음과 연결할 정책을 선택한 다음, **확인** 을 선택합니다.
   - 백업 정책은 백업이 수행되는 시기와 저장 기간을 지정합니다.
   - 드롭다운 메뉴 아래에 기본 정책의 상세 정보가 나열됩니다.

   ![백업 정책 선택](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
    
1. 기본 정책을 사용하지 않으려면 **새로 만들기** 를 선택하고 [사용자 지정 정책을 만듭니다](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. **Virtual Machines** 에서 **추가** 를 선택합니다.

    ![가상 컴퓨터 추가](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. 선택 정책을 사용하여 백업하려는 암호화된 VM을 선택하고 **확인** 을 선택합니다.

      ![암호화된 VM 선택](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Azure Key Vault를 사용하는 경우 자격 증명 모음 페이지에 Azure Backup에 Key Vault의 키와 비밀에 대한 읽기 전용 액세스 권한이 필요하다는 메시지가 표시됩니다.

    - 이 메시지가 표시되면 아무 조치도 필요하지 않습니다.

        ![액세스 양호](./media/backup-azure-vms-encryption/access-ok.png)

    - 이 메시지가 표시되면 [아래 절차](#provide-permissions)에 설명된 대로 사용 권한을 설정해야 합니다.

        ![액세스 경고](./media/backup-azure-vms-encryption/access-warning.png)

1. **백업 사용** 을 선택하여 자격 증명 모음에 백업 정책을 배포하고 선택한 VM에 대한 백업을 사용하도록 설정합니다.

## <a name="trigger-a-backup-job"></a>백업 작업 트리거

초기 백업은 일정에 따라 실행되지만, 다음과 같이 즉시 실행할 수 있습니다.

1. **백업 센터** 로 이동하여 **백업 인스턴스** 메뉴 항목을 선택합니다.
1. **데이터 원본 유형** 으로 **Azure Virtual Machines** 를 선택하고 백업을 위해 구성한 VM을 검색합니다.
1. 관련 행을 마우스 오른쪽 단추로 클릭하거나 추가 아이콘(...)을 선택하고 **지금 백업** 을 클릭합니다.
1. **지금 백업** 에서 달력 컨트롤을 사용하여 복구 지점을 유지해야 하는 마지막 날을 선택합니다. 그런 다음, **확인** 을 선택합니다.
1. 포털 알림을 모니터링합니다.
   작업 진행 상황을 모니터링하려면 **백업 센터** > **백업 작업** 으로 이동하여 **진행 중** 인 작업에 대한 목록을 필터링합니다.
   VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="provide-permissions"></a>권한 제공

연결된 VM과 함께 키와 비밀을 백업하려면 Azure Backup에 읽기 전용 액세스가 필요합니다.

- Key Vault는 Azure 구독의 Azure AD 테넌트와 연결되어 있습니다. **멤버 사용자** 인 경우 Azure Backup은 추가 작업 없이 Key Vault에 대한 액세스 권한을 획득합니다.
- **게스트 사용자** 인 경우 키 자격 증명 모음에 액세스하는 Azure Backup에 대한 권한을 제공해야 합니다. 암호화된 VM에 대한 Backup을 구성하려면 키 자격 증명 모음에 대한 액세스 권한이 있어야 합니다.

권한을 설정하려면:

1. Azure Portal에서 **모든 서비스** 를 선택하고 **키 자격 증명 모음** 을 검색합니다.
1. 백업 중인 암호화된 VM과 연결된 키 자격 증명 모음을 선택합니다.

    >[!TIP]
    >VM의 연결 된 키 자격 증명 모음을 식별하려면 다음 PowerShell 명령을 사용합니다. 리소스 그룹 이름 및 VM 이름을 대체합니다.
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > 다음 줄에서 키 자격 증명 모음 이름을 찾습니다.
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. **액세스 정책** > **액세스 정책 추가** 를 선택합니다.

    ![액세스 정책 추가](./media/backup-azure-vms-encryption/add-access-policy.png)

1. **액세스 정책 추가** > **템플릿에서 구성(선택 사항)** 에서 **Azure Backup** 을 선택합니다.
    - **키 권한** 및 **비밀 권한** 에서 필요한 권한이 미리 입력됩니다.
    - VM이 **BEK 전용** 을 사용하여 암호화된 경우에는 비밀만 사용할 수 있는 권한이 필요하므로 **키 권한** 에 대한 선택 항목을 제거합니다.

    ![Azure Backup 선택](./media/backup-azure-vms-encryption/select-backup-template.png)

1. **추가** 를 선택합니다. **액세스 정책** 에 **백업 관리 서비스** 가 추가됩니다.

    ![액세스 정책](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. **저장** 을 선택하여 Azure Backup에 권한을 제공합니다.

## <a name="restore-an-encrypted-vm"></a>암호화된 VM 복원

암호화된 VM은 아래에 설명된 대로 VM 디스크를 복원하는 방법으로만 복원할 수 있습니다. **기존 항목 바꾸기** 와 **VM 복원** 은 지원되지 않습니다.

암호화된 VM을 다음과 같이 복원합니다.

1. [VM 디스크 복원](backup-azure-arm-restore-vms.md#restore-disks).

   > [!NOTE]
   > VM 디스크를 복원한 후에는 다시 만들지 않고도 원래 VM의 OS 디스크를 복원된 VM 디스크로 수동 전환할 수 있습니다. [자세한 정보](https://azure.microsoft.com/blog/os-disk-swap-managed-disks/).

2. 다음 작업 중 하나를 수행하여 가상 머신 인스턴스를 다시 만듭니다.
    1. 복원 작업 중에 생성된 템플릿을 사용하여 VM 설정을 사용자 지정하고 VM 배포를 트리거합니다. [자세히 알아봅니다](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
       >[!NOTE]
       >템플릿을 배포하는 동안 저장소 계정 컨테이너와 공용/개인 설정을 확인합니다.
    1. PowerShell을 사용하여 복원된 디스크에서 새 VM을 만듭니다. [자세히 알아봅니다](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
1. Linux VM의 경우 데이터 디스크가 열리고 탑재되도록 ADE 확장을 다시 설치합니다.

## <a name="next-steps"></a>다음 단계

문제가 발생하는 경우 다음 문서를 검토하세요.

- 암호화된 Azure VM 백업 및 복원 시 [일반적인 오류](backup-azure-vms-troubleshoot.md)
- [Azure VM 에이전트/백업 확장](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 문제