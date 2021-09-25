---
title: Azure Managed Disks 관리
description: Azure Portal Azure Managed Disk 관리에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: 4b40a71a71ce543c66bf347ec745fea6ae384779
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708916"
---
# <a name="manage-azure-managed-disks"></a>Azure Managed Disks 관리

이 문서에서는 Azure Portal Azure Managed Disk를 관리하는 방법을 설명합니다.

## <a name="stop-protection-preview"></a>보호 중지(미리 보기)


다음 세 가지 방법으로 Azure 디스크 보호를 중지할 수 있습니다.

- **보호 중지 및 데이터 보존(영속 유지)**: 이 옵션을 사용하면 향후의 모든 백업 작업에서 디스크를 보호할 수 없습니다. 그러나 Azure Backup 서비스는 백업되는 복구 지점을 유지합니다. 자격 증명 모음에서 복구 지점이 유지되도록 요금을 지불해야 합니다(자세한 내용은 [Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/) 참조). 필요한 경우 디스크를 복원할 수 있습니다. 디스크 보호를 다시 시작하려면 **백업 다시 시작** 옵션을 사용합니다.

- **보호 중지 및 데이터 보존(정책에 따라 유지)**: 이 옵션을 사용하면 향후의 모든 백업 작업에서 디스크를 보호할 수 없습니다. 복구 지점은 정책에 따라 유지되며 [Azure Backup 가격 책정에](https://azure.microsoft.com/pricing/details/backup/)따라 요금이 청구됩니다. 그러나 최신 복구 지점은 유지됩니다.

- **보호 중지 및 데이터 삭제:** 이 옵션을 사용하면 이후의 모든 백업 작업에서 디스크를 보호하고 모든 복구 지점을 삭제할 수 있습니다. 디스크를 복원하거나 **백업 다시 시작** 옵션을 사용할 수 없습니다.

### <a name="stop-protection-and-retain-data"></a>보호 중지 및 데이터 보존

1. 백업 **센터로** 이동하여 **Azure 디스크를** 선택합니다.

1. 디스크 백업 인스턴스 목록에서 유지하려는 인스턴스를 선택합니다.

1. **백업 중지(미리 보기)** 를 선택합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-inline.png" alt-text="중지할 Azure 디스크 백업 인스턴스의 선택을 보여주는 스크린샷." lightbox="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-expanded.png":::
 
1. 다음 데이터 보존 옵션 중 하나를 선택합니다.

   1. 영원하게 유지
   1. 정책에 따라 유지
 
   :::image type="content" source="./media/manage-azure-managed-disks/data-retention-options-for-disk-inline.png" alt-text="디스크 백업 인스턴스 보호를 중지하는 옵션을 보여주는 스크린샷" lightbox="./media/manage-azure-managed-disks/data-retention-options-for-disk-expanded.png":::

   드롭다운 목록에서 백업을 중지하는 이유를 선택할 수도 있습니다.

1. **백업 중지를** 클릭합니다.

1. **확인을** 선택하여 데이터 보호를 중지합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="선택할 디스크 백업 인스턴스 보존 옵션을 보여주는 스크린샷." lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

1. 백업 **센터로** 이동하여 **Azure 디스크를** 선택합니다.

1. 디스크 백업 인스턴스 목록에서 삭제할 인스턴스를 선택합니다.

1. **백업 중지(미리 보기)** 를 클릭합니다.

1. **백업 데이터 삭제를** 선택합니다.

   백업 인스턴스의 이름, 삭제 이유 및 기타 주석을 제공합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/details-to-stop-disk-backup-inline.png" alt-text="디스크 백업을 중지하기 위한 확인 스크린샷" lightbox="./media/manage-azure-managed-disks/details-to-stop-disk-backup-expanded.png":::

1. **백업 중지** 를 선택합니다.

1. **확인을** 선택하여 데이터 보호를 중지합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="선택할 디스크 백업 인스턴스 보존 옵션을 보여주는 스크린샷." lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

## <a name="resume-protection"></a>보호 다시 시작

보호 중지 및 **데이터 보존** 옵션을 선택한 경우 디스크에 대한 보호를 다시 시작할 수 있습니다.

>[!Note]
>디스크 보호를 시작하면 백업 정책이 보존된 데이터에도 적용됩니다. 정책에 따라 만료된 복구 지점이 정리됩니다.

다음 단계를 사용합니다.

1. 백업 **센터로** 이동하여 **Azure 디스크를** 선택합니다.

1. 디스크 백업 인스턴스 목록에서 다시 시작할 인스턴스를 선택합니다.

1. **백업 다시 시작(미리 보기) 을** 선택합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-protection-inline.png" alt-text="디스크 보호를 다시 시작하는 옵션을 보여주는 스크린샷." lightbox="./media/manage-azure-managed-disks/resume-disk-protection-expanded.png":::

1. **백업 다시 시작을** 선택합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-backup-inline.png" alt-text="디스크 백업을 다시 시작하는 옵션을 보여주는 스크린샷." lightbox="./media/manage-azure-managed-disks/resume-disk-backup-expanded.png":::

## <a name="delete-backup-instance"></a>백업 인스턴스 삭제

예약된 모든 백업 작업을 중지하고 기존 백업을 모두 삭제하도록 선택한 경우 **백업 인스턴스 삭제를** 사용합니다.

>[!Note]
>스냅샷 리소스 그룹이 수동으로 삭제되거나 Backup 자격 증명 모음의 관리 ID에 대한 권한이 취소되면 백업 인스턴스를 삭제하지 못합니다. 이러한 오류의 경우 스냅샷 리소스 그룹(동일한 이름 사용)을 일시적으로 만들고 [여기에](/azure/backup/backup-managed-disks-ps#assign-permissions)설명된 대로 필요한 역할 권한이 있는 Backup 자격 증명 모음의 관리 ID를 제공합니다. **Backup 인스턴스** 화면의 **Essentials** 탭에서 스냅샷 리소스 그룹의 이름을 찾을 수 있습니다. 

디스크 백업 인스턴스를 삭제하려면 다음 단계를 수행합니다.

1. 백업 인스턴스 화면에서 **삭제를** 클릭합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-inline.png" alt-text="백업 인스턴스를 삭제하는 프로세스를 보여주는 스크린샷." lightbox="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-expanded.png":::

1. Backup 인스턴스의 이름, 삭제 이유 및 추가 주석을 포함한 확인 세부 정보를 제공합니다.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-inline.png" alt-text="백업 인스턴스 삭제를 확인하는 스크린샷" lightbox="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-expanded.png":::

1. **삭제를** 클릭하여 백업 인스턴스 삭제를 확인하고 계속 진행합니다.

## <a name="next-steps"></a>다음 단계

[Backup 자격 증명 모음 개요](backup-vault-overview.md)
