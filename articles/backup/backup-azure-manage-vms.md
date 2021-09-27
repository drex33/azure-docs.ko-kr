---
title: Azure VM 백업 관리 및 모니터링
description: Azure Backup 서비스를 사용하여 Azure VM 백업을 관리하고 모니터링하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: d1040c1261c3edff3010403a63c856ff1c0200fa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605623"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Azure Backup 서비스를 사용하여 Azure VM 백업 관리

이 문서에서는 [Azure Backup 서비스](backup-overview.md)를 사용하여 백업된 Azure VM(가상 머신)을 관리하는 방법을 설명합니다. 또한 이 문서에서는 자격 증명 모음 대시보드에서 찾을 수 있는 백업 정보를 요약합니다.

Azure Portal에서 Recovery Services 자격 증명 모음 대시보드는 다음을 포함한 자격 증명 모음 정보에 대한 액세스를 제공합니다.

* 최신 백업(최신 복원 지점이기도 함)
* 백업 정책
* 모든 백업 스냅샷의 총 크기
* 백업을 사용할 수 있는 VM의 수

대시보드를 사용하고 개별 VM으로 드릴다운하여 백업을 관리할 수 있습니다. 컴퓨터 백업을 시작하려면 대시보드에서 자격 증명 모음을 엽니다.

![슬라이더로 전체 대시보드 보기](./media/backup-azure-manage-vms/bottom-slider.png)

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="view-vms-on-the-dashboard"></a>대시보드에서 VM 보기

자격 증명 모음 대시보드에서 VM을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 왼쪽 메뉴에서 **모든 서비스** 를 선택합니다.

    ![모든 서비스 선택](./media/backup-azure-manage-vms/select-all-services.png)

1. **모든 서비스** 대화 상자에서 *Recovery Services* 를 입력합니다. 입력 내용에 따라 리소스 목록이 필터링됩니다. 리소스 목록에서 **Recovery Services 자격 증명 모음** 을 선택합니다.

    ![Recovery Services 자격 증명 모음 입력 및 선택](./media/backup-azure-manage-vms/all-services.png)

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.

1. 사용 편의성을 위해 자격 증명 모음 이름 옆의 고정 아이콘을 선택하고 **대시보드에 고정** 을 선택합니다.
1. 자격 증명 모음 대시보드를 엽니다.

    ![자격 증명 모음 대시보드와 설정 창 열기](./media/backup-azure-manage-vms/full-view-rs-vault.png)

1. **백업 항목** 타일에서 **Azure 가상 머신** 을 선택합니다.

    ![백업 항목 타일 열기](./media/backup-azure-manage-vms/azure-virtual-machine.png)

1. **백업 항목** 창에서 보호된 VM의 목록을 볼 수 있습니다. 이 예시에서 자격 증명 모음은 *myVMR1* 이라는 가상 머신을 보호합니다.  

    ![백업 항목 창 보기](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

1. 자격 증명 모음 항목의 대시보드에서 백업 정책 수정, 주문형 백업 실행, VM 보호 중지 또는 다시 시작, 백업 데이터 삭제, 복원 시점 보기, 복원 실행 등을 수행할 수 있습니다.

    ![백업 항목 대시보드 및 설정 창](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>VM의 백업 정책 관리

### <a name="modify-backup-policy"></a>백업 정책 수정

기존 백업 정책을 수정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 자격 증명 모음 대시보드를 엽니다.
2. **관리 > 백업 정책** 에서 **Azure 가상 머신** 유형에 대한 백업 정책을 선택합니다.
3. **수정** 을 선택하고 설정을 변경합니다.

### <a name="switch-backup-policy"></a>백업 정책 전환

백업 정책을 관리하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 자격 증명 모음 대시보드를 엽니다.
2. **백업 항목** 타일에서 **Azure 가상 머신** 을 선택합니다.

    ![백업 항목 타일 열기](./media/backup-azure-manage-vms/azure-virtual-machine.png)

3. **백업 항목** 창에서 보호된 VM 목록과 최근 복원 시점 시간을 사용한 마지막 백업 상태를 볼 수 있습니다.

    ![백업 항목 창 보기](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. 자격 증명 모음 항목의 대시보드에서 백업 정책을 선택할 수 있습니다.

   * 정책을 전환하려면 다른 정책을 선택하고 **저장** 을 선택합니다. 자격 증명 모음에 새 정책이 즉시 적용됩니다.

     ![백업 정책 선택](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

보호를 설정한 후에 VM의 주문형 백업을 실행할 수 있습니다. 다음 세부 사항을 기억해 두세요.

* 초기 백업이 보류 중인 경우, 주문형 백업은 Recovery Services 자격 증명 모음에 VM의 전체 복사본을 만듭니다.
* 초기 백업이 완료되면 주문형 백업은 이전 스냅샷에서 Recovery Services 자격 증명 모음에 변경 내용만 보냅니다. 즉, 이후 백업은 항상 증분형입니다.
* 주문형 백업의 보존 범위는 백업을 트리거할 때 지정하는 보존 값입니다.

> [!NOTE]
> Azure Backup는 백업 정책에 따라 VM 1 개 백업 및 요청 시 백업을 3 개 백업 하는 하루에 4 개의 백업을 권장 합니다. 그러나 실패 한 시도의 경우 사용자 재시도를 허용 하기 위해 주문형 백업에 대 한 하드 한도는 9 회 시도로 설정 됩니다.

요청 시 백업을 트리거하려면 다음을 수행합니다.

1. [자격 증명 모음 항목 대시보드](#view-vms-on-the-dashboard)의 **보호된 항목** 에서 **백업 항목** 을 선택합니다.

    ![지금 백업 옵션](./media/backup-azure-manage-vms/backup-now-button.png)

2. **백업 관리 유형** 에서 **Azure 가상 머신** 을 선택합니다. **백업 항목(Azure Virtual Machine)** 창이 나타납니다.
3. VM을 선택하고 **지금 백업** 을 선택하여 주문형 백업을 만듭니다. **지금 백업** 창이 나타납니다.
4. **백업 보존 기간** 필드에서 백업을 보존할 날짜를 지정합니다.

    ![지금 백업 일정](./media/backup-azure-manage-vms/backup-now-check.png)

5. **확인** 을 선택하여 백업 작업을 실행합니다.

작업 진행률을 추적하려면 자격 증명 모음 대시보드에서 **백업 작업** 타일을 선택합니다.

## <a name="stop-protecting-a-vm"></a>VM 보호 중지

VM 보호를 중지하는 방법은 두 가지가 있습니다.

* **보호를 중지하고 백업 데이터를 보존**. 이 옵션은 VM을 보호하는 이후의 모든 백업 작업을 중지합니다. 그러나 Azure Backup 서비스에서 백업된 복구 지점은 유지됩니다.  자격 증명 모음에서 복구 지점이 유지되도록 요금을 지불해야 합니다(자세한 내용은 [Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/) 참조). 필요한 경우 VM을 복원할 수 있습니다. VM 보호를 다시 시작하기로 결정한 경우 *백업 다시 시작* 옵션을 사용할 수 있습니다.
* **보호를 중지하고 백업 데이터를 삭제**. 이 옵션을 선택하면 VM을 보호하는 이후의 모든 백업 작업이 중지되고 모든 복구 지점을 삭제합니다. VM을 복원하거나 *백업 다시 시작* 옵션을 사용할 수 없습니다.

>[!NOTE]
>백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다. 이전 복구 지점은 정책에 따라 만료되지만 가장 최근 복구 지점은 백업을 중지하고 데이터를 삭제할 때까지 항상 유지됩니다.
>

### <a name="stop-protection-and-retain-backup-data"></a>보호 중지 및 백업 데이터 보존

보호를 중지하고 VM의 데이터를 보존하려면 다음 단계를 수행합니다.

1. [자격 증명 모음 항목의 대시보드](#view-vms-on-the-dashboard)에서 **백업 중지** 를 선택합니다.
2. **백업 데이터 보존** 을 선택하고 필요에 따라 선택 내용을 확인합니다. 원하는 경우 주석을 추가합니다. 항목 이름을 모르는 경우 이름을 보려면 느낌표 위로 마우스 커서를 가져갑니다.

    ![백업 데이터 보존](./media/backup-azure-manage-vms/retain-backup-data.png)

알림을 통해 백업 작업이 중지되었음을 알 수 있습니다.

### <a name="stop-protection-and-delete-backup-data"></a>보호 중지 및 백업 데이터 삭제

VM의 보호를 중지하고 데이터를 삭제하려면 다음 단계를 수행합니다.

>[!Note]
>보관 계층에서 180일간 유지되지 않은 보관의 복구 지점을 삭제하면 초기 삭제 비용이 발생합니다. [자세히 알아보기](../storage/blobs/storage-blob-storage-tiers.md#cool-and-archive-early-deletion).


1. [자격 증명 모음 항목의 대시보드](#view-vms-on-the-dashboard)에서 **백업 중지** 를 선택합니다.
2. **백업 데이터 삭제** 를 선택하고 필요에 따라 선택 내용을 확인합니다. 원하는 경우 백업 항목의 이름을 입력하고 주석을 추가합니다.

    ![백업 데이터 삭제](./media/backup-azure-manage-vms/delete-backup-data.png)

> [!NOTE]
> 삭제 작업을 완료한 후 백업된 데이터는 [일시 삭제된 상태](./soft-delete-virtual-machines.md)로 14일 동안 보존됩니다. <br>또한 [일시 삭제를 사용하거나 사용하지 않도록](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 설정할 수도 있습니다.

## <a name="resume-protection-of-a-vm"></a>VM 보호 다시 시작

VM 보호를 중지한 동안 [보호 중지 및 백업 데이터 보존](#stop-protection-and-retain-backup-data) 옵션을 선택했다면 **백업 다시 시작** 을 사용할 수 있습니다. [보호 중지 및 백업 데이터 삭제](#stop-protection-and-delete-backup-data) 옵션을 선택했거나 [백업 데이터를 삭제](#delete-backup-data)하는 경우에는 이 옵션을 사용할 수 없습니다.

VM 보호를 다시 시작하려면 다음 단계를 수행합니다.

1. [자격 증명 모음 항목의 대시보드에서](#view-vms-on-the-dashboard) **백업 다시 시작** 을 선택합니다.

2. [백업 정책 관리](#manage-backup-policy-for-a-vm)의 단계에 따라 VM에 대한 정책을 할당합니다. VM의 초기 보호 정책을 선택할 필요는 없습니다.
3. VM에 백업 정책을 적용하면 다음과 같은 메시지가 표시됩니다.

    ![성공적으로 보호된 VM을 나타내는 메시지](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>백업 데이터 삭제

VM의 백업 데이터를 삭제하는 방법은 두 가지입니다.

* 자격 증명 모음 항목 대시보드에서 백업 중지를 선택하고 [보호 중지 및 백업 데이터 삭제](#stop-protection-and-delete-backup-data) 옵션에 대한 지침을 따릅니다.

  ![백업 중지 선택](./media/backup-azure-manage-vms/stop-backup-button.png)

* 자격 증명 모음 항목 대시보드에서 백업 데이터 삭제를 선택합니다. VM 보호를 중지한 동안 [보호 중지 및 백업 데이터 보존](#stop-protection-and-retain-backup-data) 옵션을 선택한 경우 이 옵션을 사용하는 것으로 설정할 수 있습니다.

  ![백업 삭제 선택](./media/backup-azure-manage-vms/delete-backup-button.png)

  * [자격 증명 모음 항목 대시보드](#view-vms-on-the-dashboard)에서 **백업 데이터 삭제** 를 선택합니다.
  * 백업 항목의 이름을 입력하여 복구 지점을 삭제한다고 확인합니다.

    ![백업 데이터 삭제](./media/backup-azure-manage-vms/delete-backup-data.png)

  * 해당 항목에 대한 백업 데이터를 삭제하려면 **삭제** 를 선택합니다. 알림 메시지를 통해 백업 데이터가 삭제되었음을 알 수 있습니다.

데이터를 보호하기 위해 Azure Backup에는 일시 삭제 기능이 포함되어 있습니다. 일시 삭제를 사용하면 VM의 백업(모든 복구 지점)을 삭제하더라도 백업 데이터가 추가로 14일 동안 보존됩니다. 자세한 내용은 [일시 삭제 설명서](./backup-azure-security-feature-cloud.md)를 참조하세요.

  > [!NOTE]
  > 백업 데이터를 삭제하면 연결된 모든 복구 지점이 삭제됩니다. 특정 복구 지점을 삭제하도록 선택할 수는 없습니다.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>주 데이터 원본이 더 이상 존재하지 않는 백업 항목

* 보호를 중지하지 않고 Azure Backup에 구성된 Azure VM이 삭제되거나 이동된 경우, 예약된 백업 작업과 주문형(애드혹) 백업 작업이 모두 실패하고 UserErrorVmNotFoundV2 오류가 발생합니다. 백업 사전 검사는 실패한 주문형 백업 작업만 중요한 항목으로 표시합니다(실패한 예약 작업은 표시되지 않음).
* 이러한 백업 항목은 사용자가 설정한 백업 및 보존 정책을 준수하는 시스템에서 활성 상태로 유지됩니다. 이러한 Azure VM에 대한 백업된 데이터는 보존 정책에 따라 보존됩니다. 만료된 복구 지점(가장 최근 복구 지점 제외)은 백업 정책에 설정된 보존 범위에 따라 정리됩니다.
* 추가 비용 발생을 방지하려면 주 데이터 원본이 더 이상 존재하지 않는 백업 항목을 삭제하기를 권장합니다. 최근 복구 지점이 영구적으로 유지되고 해당하는 백업 가격 책정에 따라 요금이 청구되기 때문에, 삭제된 리소스에 대한 백업 항목/데이터가 더 이상 필요하지 않은 시나리오입니다.

## <a name="next-steps"></a>다음 단계

* [VM의 설정에서 Azure VM을 백업](backup-azure-vms-first-look-arm.md)하는 방법에 대해 알아봅니다.
* [VM을 복원](backup-azure-arm-restore-vms.md)하는 방법에 대해 알아봅니다.
* [Azure VM 백업을 모니터링](./backup-azure-monitoring-built-in-monitor.md)하는 방법에 대해 알아봅니다.