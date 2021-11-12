---
title: Azure VM을 만들 때 백업 사용
description: Azure Backup를 사용하여 Azure VM을 만들 때 백업을 사용하도록 스냅샷 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/09/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: d94faf113fb3d75c1c0f5c878369c1856366b1be
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332121"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Azure VM을 만들 때 백업 사용

Azure Backup 서비스를 사용하여 Azure VM(가상 머신)을 백업할 수 있습니다. VM은 백업 정책에 지정된 일정에 따라 백업되며, 백업에서 복구 지점이 생성됩니다. 복구 지점은 Recovery Services 자격 증명 모음에 저장됩니다.

이 문서에서는 Azure Portal에서 VM(가상 머신)을 만들 때 백업을 사용하도록 설정하는 방법을 자세히 설명합니다.  

## <a name="before-you-start"></a>시작하기 전에

- VM을 만들 때 백업을 사용하도록 설정하는 경우 지원되는 운영 체제를 [확인](backup-support-matrix-iaas.md#supported-backup-actions)합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

계정에 아직 로그인하지 않았다면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-vm-with-backup-configured"></a>백업이 구성된 VM 만들기

1. Azure Portal에서 **리소스 생성** 를 선택합니다.

2. Azure Marketplace에서 **Compute** 를 선택한 후 VM 이미지를 선택합니다.

   >[!Note]
   >Marketplace가 아닌 이미지에서 VM을 만들거나 Marketplace가 아닌 이미지를 사용 하 여 vm의 OS 디스크를 교환 하려면 VM에서 계획 정보를 제거 합니다. 이렇게 하면 원활한 VM 복원에 도움이 됩니다.

3. [Windows](../virtual-machines/windows/quick-create-portal.md) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md) 지침에 따라 VM을 설정합니다.

4. **관리** 탭의 **백업 사용** 에서 **켜기** 를 선택합니다.
5. Azure Backup은 Recovery Services 자격 증명 모음에 백업을 합니다. 기존 자격 증명 모음이 없는 경우 **새로 만들기** 를 선택합니다.
6. 제안된 자격 증명 모음 이름을 그대로 적용하거나 고유한 이름을 지정합니다.
7. 자격 증명 모음을 배치할 리소스 그룹을 지정하거나 만듭니다. 리소스 그룹 자격 증명 모음은 VM 리소스 그룹과 다를 수 있습니다.

    ![VM에 백업을 사용하도록 설정합니다.](./media/backup-during-vm-creation/enable-backup.png)

8. 기본 백업 정책을 적용하거나 설정을 수정합니다.
    - 백업 정책에서 VM의 백업 스냅샷 생성 빈도와 해당 백업 복사본의 보존 기간이 지정됩니다.
    - 기본 정책은 하루에 한 번 VM을 백업합니다.
    - 매일 또는 매주 백업을 수행하도록 Azure VM의 백업 정책을 사용자 지정할 수 있습니다.
    - Azure VM에 대한 백업 고려 사항에 대해 [자세히 알아보세요](backup-azure-vms-introduction.md#backup-and-restore-considerations).
    - 즉시 복원 기능에 대해 [자세히 알아보세요](backup-instant-restore-capability.md).

      ![기본 백업 정책](./media/backup-during-vm-creation/daily-policy.png)

>[!NOTE]
> [SSE 및 PMK는](backup-encryption.md) Azure VM의 기본 암호화 방법입니다. Azure Backup은 Gen2 VM의 백업 및 복원을 지원합니다.

## <a name="azure-backup-resource-group-for-virtual-machines"></a>가상 머신에 대한 Azure Backup 리소스 그룹

이 백업 서비스는 VM의 리소스 그룹과 별개의 리소스 그룹(RG)을 만들어 복원 지점 컬렉션(RPC)을 저장합니다. RPC는 관리되는 VM의 인스턴트 복구 지점을 보관합니다. 이 백업 서비스가 만드는 리소스 그룹의 명명 형식은 `AzureBackupRG_<Geo>_<number>`입니다. 예를 들어, *AzureBackupRG_northeurope_1* 이 여기에 해당합니다. 이제 Azure Backup에서 만든 리소스 그룹 이름을 사용자 지정할 수 있습니다.

주의할 사항:

1. RG의 기본 이름을 사용하거나, 이를 회사 요구 사항에 따라 편집할 수 있습니다.<br>RG를 만들지 않은 경우 restorepointcollection에 대한 RG를 지정하려면 다음 단계를 수행합니다.
   1. restorepointcollection에 대한 RG를 만듭니다. 예: "rpcrg".
   1. VM 백업 정책에서 RG의 이름을 언급합니다.
   >[!NOTE]
   >그러면 숫자가 추가된 RG가 생성되고 restorepointcollection에 사용됩니다.
1. VM 백업 정책을 만드는 동안 RG 이름 패턴을 입력으로 제공합니다. RG 이름은 `<alpha-numeric string>* n <alpha-numeric string>` 형식이어야 합니다. 'n'은 1부터 시작 하는 정수로 바뀌고, 첫 번째 RG가 가득 찬 경우 크기를 확장하는 데 사용됩니다. 하나의 RG는 현재 최대 600의 RPC를 사용할 수 있습니다.
              ![정책을 만들 때 이름 선택](./media/backup-during-vm-creation/create-policy.png)
1. 패턴은 아래의 RG 명명 규칙을 따라야 하며, 총 길이는 최대 허용 RG 이름 길이를 초과해서는 안 됩니다.
    1. 리소스 그룹 이름에는 영숫자, 마침표, 밑줄, 하이픈, 괄호만 허용됩니다. 마침표로 끝날 수 없습니다.
    2. 리소스 그룹 이름은 RG 이름과 접미사를 포함하여 최대 74자까지 포함할 수 있습니다.
1. 첫 번째 `<alpha-numeric-string>`은 필수 항목이며, 'n' 뒤의 두 번째는 선택 사항입니다. 이는 사용자 지정된 이름을 사용하는 경우에만 적용됩니다. 텍스트 상자 중 하나에 아무것도 입력하지 않으면, 기본 이름이 사용됩니다.
1. 필요한 경우 정책을 수정하여 RG 이름을 편집할 수 있습니다. 이름 패턴이 변경되면, 새 RG에 새 RP가 만들어집니다. 그러나 RP 컬렉션은 리소스 이동을 지원하지 않으므로, 이전 RP는 여전히 이전 RG에 상주하고 이동하지 않습니다. 결과적으로, 해당 지점이 만료되면 RP가 가비지 수집을 실행합니다.
![정책을 수정할 때 이름 변경](./media/backup-during-vm-creation/modify-policy.png)
1. 백업 서비스에서 사용하기 위해 생성된 리소스 그룹을 잠그지 않는 것이 좋습니다.

PowerShell을 사용하여 가상 머신에 Azure Backup 리소스 그룹을 구성하려면 [스냅샷 보존 중 Azure Backup 리소스 그룹 만들기](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention)를 참조하세요.

## <a name="start-a-backup-after-creating-the-vm"></a>VM을 만든 후 백업 시작

VM 백업은 사용자의 백업 정책에 따라 실행됩니다. 그러나 초기 백업을 실행하는 것이 좋습니다.

VM을 만든 후 다음을 수행합니다.

1. VM 속성에서 **백업** 을 선택합니다. VM 상태는 초기 백업이 실행될 때까지 초기 백업 보류 중입니다.
2. **지금 백업** 을 선택하여 주문형 백업을 실행합니다.

    ![주문형 백업 실행](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Resource Manager 템플릿을 사용하여 보호된 VM 배포

이전 단계에서는 Azure Portal을 사용하여 가상 머신을 만들고 Recovery Services 자격 증명 모음에서 이를 보호하는 방법을 설명했습니다. 하나 이상의 VM을 신속하게 배포하고 Recovery Services 자격 증명 모음에서 이를 보호하려면, [Windows VM 배포 및 백업 사용](https://azure.microsoft.com/resources/templates/recovery-services-create-vm-and-configure-backup/) 템플릿을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 VM을 보호했으므로, 다음으로 VM 관리 및 복원 방법을 알아봅니다.

- [VM 관리 및 모니터링](backup-azure-manage-vms.md)
- [VM 복원](backup-azure-arm-restore-vms.md)

문제가 발생할 경우 [문제 해결 가이드](backup-azure-vms-troubleshoot.md)를 검토하세요.
