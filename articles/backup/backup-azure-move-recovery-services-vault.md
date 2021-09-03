---
title: Azure Backup Recovery Services 자격 증명 모음을 이동하는 방법
description: Azure 구독 및 리소스 그룹에서 Recovery Services 자격 증명 모음을 이동하는 방법에 대한 지침입니다.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 6de0d6902705ff0edffd5e51e0e8f9abc6378830
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114298092"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Azure 구독 및 리소스 그룹에서 Recovery Services 자격 증명 모음 이동

이 문서에서는 Azure 구독 간에 또는 동일한 구독의 다른 리소스 그룹에 Azure Backup에 대해 구성된 Recovery Services 자격 증명 모음을 이동하는 방법을 설명합니다. Azure Portal 또는 PowerShell을 사용하여 Recovery Services 자격 증명 모음을 이동할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

프랑스 중부, 프랑스 남부, 독일 북동부, 독일 중부, 중국 북부, 중국 북부2, 중국 동부 및 중국 동부2를 제외한 모든 공용 지역 및 주권 지역이 지원됩니다.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Recovery Services 자격 증명 모음 이동의 사전 요구 사항

- 리소스 그룹 간에 자격 증명 모음을 이동하는 중에는 쓰기 및 삭제 작업을 방지하기 위해 원본 및 대상 리소스 그룹이 모두 잠깁니다. 자세한 내용은 [이 문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 관리자 구독에만 자격 증명 모음을 이동할 권한이 있습니다.
- 구독에서 자격 증명 모음을 이동할 때 대상 구독은 원본 구독과 동일한 테넌트에 있어야 하며, 사용 설정된 상태로 존재해야 합니다. 자격 증명 모음을 다른 Azure AD로 이동하려면 [다른 디렉터리로 구독 이전](../role-based-access-control/transfer-subscription.md) 및 [Recovery Services 자격 증명 모음 FAQ](/azure/backup/backup-azure-backup-faq#recovery-services-vault)를 참조하세요.
- 대상 리소스 그룹에 쓰기 작업을 수행할 수 있는 권한이 있어야 합니다.
- 자격 증명 모음을 이동하면 리소스 그룹이 변경됩니다. Recovery Services 자격 증명 모음은 동일한 위치에 있으며 변경할 수 없습니다.
- 지역별로 한 번에 하나의 Recovery Services 자격 증명 모음을 이동할 수 있습니다.
- VM이 구독 간에 또는 새 리소스 그룹으로 Recovery Services 자격 증명 모음과 함께 이동하지 않는 경우 현재 VM 복구 지점은 만료될 때까지 자격 증명 모음에 그대로 남아 있게 됩니다.
- VM을 자격 증명 모음과 함께 이동했는지 여부에 관계없이 자격 증명 모음의 유지된 백업 기록에서 항상 VM을 복원할 수 있습니다.
- Azure Disk Encryption은 키 자격 증명 모음 및 VM이 동일한 Azure 지역 및 구독에 있어야 합니다.
- 관리 디스크가 있는 가상 머신을 이동하려면 이 [문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 구독 안에서 또는 새 구독으로 리소스를 이동할지 여부에 따라 클래식 모델을 통해 배포된 리소스의 이동 옵션은 다릅니다. 자세한 내용은 [이 문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 자격 증명 모음에 대해 정의된 백업 정책은 자격 증명 모음이 구독 간에 또는 새 리소스 그룹으로 이동 후에 유지됩니다.
- 다음 유형의 백업 항목을 포함하는 자격 증명 모음만 이동할 수 있습니다. 아래에 나열되지 않은 유형의 백업 항목은 모두 중지되어야 하며, 자격 증명 모음을 이동하기 전에 해당 데이터를 영구적으로 삭제해야 합니다.
  - Azure Virtual Machines
  - MARS(Microsoft Azure Recovery Services) 에이전트
  - MABS(Microsoft Azure Backup 서버)
  - DPM(Data Protection Manager)
- 구독 간에 VM 백업 데이터를 포함하는 자격 증명 모음을 이동하는 경우에는 VM을 동일한 구독으로 이동하고, 동일한 대상 VM 리소스 그룹 이름(이전 구독과 동일한 이름)을 사용하여 백업을 계속해야 합니다.

> [!NOTE]
> Azure 지역 간에 Azure Backup용 Recovery Services 자격 증명 모음을 이동하는 것은 지원되지 않습니다.<br><br>
> **Azure Site Recovery** 를 사용하여 재해 복구에 대해 VM(Azure IaaS, Hyper-V, VMware) 또는 물리적 머신을 구성한 경우 이동 작업이 차단됩니다. Azure Site Recovery 자격 증명 모음을 이동하려면 [이 문서](../site-recovery/move-vaults-across-regions.md)를 검토하여 자격 증명 모음을 수동으로 이동하는 방법을 알아보세요.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Azure Portal을 사용하여 Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동

Recovery Services 자격 증명 모음 및 연결된 해당 리소스를 다른 리소스 그룹으로 이동하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Recovery Services 자격 증명 모음** 의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

   ![Recovery Services 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   자격 증명 모음에 대한 **Essentials** 정보가 표시되지 않는 경우 드롭다운 아이콘을 선택합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

   ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **리소스 그룹** 옆의 **변경** 을 선택하여 **리소스 이동** 창을 엽니다.

   ![리소스 그룹 변경](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. **리소스 이동** 창에서 선택한 자격 증명 모음의 경우 다음 이미지처럼 확인란을 선택하여 관련된 옵션 리소스를 이동하는 것이 좋습니다.

   ![구독 이동](./media/backup-azure-move-recovery-services/move-resource.png)

5. 대상 리소스 그룹을 추가하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새 그룹 만들기** 옵션을 선택합니다.

   ![리소스 만들기](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 리소스 그룹을 추가한 후 **새 리소스 ID를 사용하려는 경우 이동한 리소스와 연결된 도구 및 스크립트를 업데이트하지 않으면 정상적으로 작동하지 않는다는 점을 이해합니다.** 옵션을 확인한 다음, **확인** 을 선택하여 자격 증명 모음 이동을 완료합니다.

   ![확인 메시지](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Azure Portal을 사용하여 Recovery Services 자격 증명 모음을 다른 구독으로 이동

Recovery Services 자격 증명 모음 및 연결된 해당 리소스를 다른 구독으로 이동할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Recovery Services 자격 증명 모음의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

    ![Recovery Services 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    자격 증명 모음에 대한 **Essentials** 정보가 표시되지 않는 경우 드롭다운 아이콘을 선택합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

    ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **구독** 옆의 **변경** 을 선택하여 **리소스 이동** 창을 엽니다.

   ![구독 변경](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 이동할 리소스를 선택합니다. 여기에서 **모두 선택** 옵션을 사용하여 나열된 모든 선택적 리소스를 선택하는 것이 좋습니다.

   ![리소스 이동](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 자격 증명 모음을 이동하려는 **구독** 드롭다운 목록에서 대상 구독을 선택합니다.
6. 대상 리소스 그룹을 추가하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새 그룹 만들기** 옵션을 선택합니다.

   ![구독 추가](./media/backup-azure-move-recovery-services/add-subscription.png)

7. **새 리소스 ID를 사용하려는 경우 이동한 리소스와 연결된 도구 및 스크립트를 업데이트하지 않으면 정상적으로 작동하지 않는다는 점을 이해합니다.** 옵션을 클릭하여 확인한 다음, **확인** 을 선택합니다.

> [!NOTE]
> 구독 간 백업(RS 자격 증명 모음 및 보호된 VM이 다른 구독에 있음)은 지원되는 시나리오가 아닙니다. 또한 LRS(로컬 중복 스토리지)에서 GRS(글로벌 중복 스토리지)로 스토리지 중복 옵션 및 그 반대의 경우는 자격 증명 모음 이동 작업 중 수정할 수 없습니다.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>PowerShell을 사용하여 Recovery Services 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 `Move-AzureRMResource` cmdlet을 사용합니다. `Move-AzureRMResource`에는 리소스 이름 및 리소스 종류가 필요합니다. `Get-AzureRmRecoveryServicesVault` cmdlet에서 가져올 수 있습니다.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

리소스를 다른 구독으로 이동하기 위해 `-DestinationSubscriptionId` 매개 변수를 포함합니다.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

위의 cmdlet을 실행한 후 지정한 리소스를 이동할 것인지 묻는 메시지가 나타납니다. **Y** 를 입력하여 확인합니다. 유효성 검사에 성공 후 리소스를 이동합니다.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI를 사용하여 Recovery Services 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 다음 cmdlet을 사용합니다.

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

## <a name="post-migration"></a>마이그레이션 후

1. 리소스 그룹에 대한 액세스 제어를 설정/확인합니다.  
2. 이동이 완료된 후 자격 증명 모음에 대해 백업 보고 및 모니터링 기능을 다시 구성해야 합니다. 이전 구성은 이동 작업 중 손실됩니다.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Azure 가상 머신을 다른 Recovery Services 자격 증명 모음으로 이동합니다. 

Azure Backup을 사용하도록 설정된 Azure 가상 머신을 이동하려는 경우 두 가지 옵션 중 선택할 수 있습니다. 비즈니스 요구 사항에 따라 적절한 옵션을 선택하세요.

- [이전 백업 데이터를 보존할 필요가 없음](#dont-need-to-preserve-previous-backed-up-data)
- [이전 백업 데이터를 유지해야 함](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>이전 백업 데이터를 보존할 필요가 없음

새 자격 증명 모음에서 워크로드를 보호하려면 이전 자격 증명 모음에서 현재 보호 및 데이터를 삭제하고 백업을 다시 구성해야 합니다.

>[!WARNING]
>다음 작업은 파괴적이며 실행 취소할 수 없습니다. 보호된 서버와 연결된 모든 백업 데이터 및 백업 항목은 영구적으로 삭제됩니다. 주의하여 진행하세요.

**이전 자격 증명 모음에서 현재 보호를 중지 및 삭제합니다.**

1. 자격 증명 모음 속성에서 일시 삭제를 사용하지 않도록 설정합니다. 일시 삭제를 사용하지 않도록 설정하려면 [다음 단계](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal)를 수행합니다.

2. 보호를 중지하고 현재 자격 증명 모음에서 백업을 삭제합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 항목** 을 선택합니다. 새 자격 증명 모음으로 이동해야 하는 여기에 나열된 항목은 백업 데이터와 함께 제거해야 합니다. [클라우드에서 보호된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)하는 방법 및 [온-프레미스에서 보호된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-on-premises)하는 방법을 참조하세요.

3. AFS(Azure 파일 공유), SQL 서버 또는 SAP HANA 서버를 이동할 계획인 경우 해당 서버의 등록을 취소해야 합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 인프라** 를 선택합니다. [SQL 서버 등록을 취소](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)하고, [Azure 파일 공유와 연결된 스토리지 계정 등록을 취소](manage-afs-backup.md#unregister-a-storage-account)하고, [SAP HANA 인스턴스 등록을 취소](sap-hana-db-manage.md#unregister-an-sap-hana-instance)하는 방법을 참조하세요.

4. 이전 자격 증명에서 서버가 제거되면 새 자격 증명 모음에서 워크로드에 대한 백업을 계속 구성합니다.

### <a name="must-preserve-previous-backed-up-data"></a>이전 백업 데이터를 유지해야 함

이전 자격 증명 모음에서 현재 보호되는 데이터를 유지하고, 새 자격 증명 모음에서 보호를 계속해야 하는 경우 일부 워크로드에서 사용할 수 있는 제한된 옵션이 있습니다.

- MARS의 경우 [데이터 보관을 통해 보호를 중지](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup)하고 새 자격 증명 모음에 에이전트를 등록할 수 있습니다.

  - Azure Backup 서비스는 이전 자격 증명 모음의 모든 기존 복구 지점을 계속 보관합니다.
  - 이전 자격 증명 모음의 복구 지점을 유지하려면 요금을 지불해야 합니다.
  - 이전 자격 증명 모음에 있는 만료되지 않은 복구 지점에 대한 백업된 데이터만 복원할 수 있습니다.
  - 새 자격 증명 모음에 데이터의 새 초기 복제본을 만들어야 합니다.

- Azure VM의 경우 이전 자격 증명 모음에서 VM에 대해 [데이터 보관을 통해 보호를 중지](backup-azure-manage-vms.md#stop-protecting-a-vm)하고 VM을 다른 리소스 그룹으로 이동한 다음 새 자격 증명 모음에서 해당 VM을 보호할 수 있습니다. 다른 리소스 그룹으로 VM을 이동하는 방법에 대한 [지침 및 제한 사항](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)을 참조하세요.

  VM은 한 번에 하나의 자격 증명 모음에서만 보호할 수 있습니다. 그러나 새 리소스 그룹의 VM은 다른 VM으로 간주되므로 새 자격 증명 모음에서 보호할 수 있습니다.

  - Azure Backup 서비스는 이전 자격 증명 모음에서 백업된 복구 지점을 유지합니다.
  - 이전 자격 증명 모음에서 복구 지점을 유지하려면 요금을 지불해야 합니다. 자세한 내용은 [Azure Backup 가격 책정](azure-backup-pricing.md)을 참조하세요.
  - 필요한 경우 이전 자격 증명 모음에서 VM을 복원할 수 있습니다.
  - 새 리소스에 있는 VM의 새 자격 증명에 대한 첫 번째 백업이 초기 복제본이 됩니다.

## <a name="next-steps"></a>다음 단계

리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다.

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.