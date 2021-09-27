---
title: Azure Database for PostgreSQL 서버 관리
description: Azure Database for PostgreSQL 서버 관리에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 2546279f22df9a39f454b926d9960f38cf42f2fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631251"
---
# <a name="manage-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버 관리

이 문서에서는 Azure Backup 서비스를 사용 하 여 백업 되는 Azure Database for PostgreSQL 서버를 관리 하는 방법을 설명 합니다.

## <a name="change-policy"></a>정책 변경

백업 인스턴스를 통해 연결된 정책을 변경할 수 있습니다.

1. 백업 **인스턴스** -> **변경 정책** 을 선택합니다.


   :::image type="content" source="./media/manage-azure-database-postgresql/change-policy.png" alt-text="정책을 변경 하는 옵션을 보여 주는 스크린샷":::
   
1. 데이터베이스에 적용할 새 정책을 선택합니다.

   :::image type="content" source="./media/manage-azure-database-postgresql/reassign-policy.png" alt-text="정책을 재할당 하는 옵션을 보여 주는 스크린샷":::

## <a name="stop-protection-preview"></a>보호 중지 (미리 보기)

Azure Database for PostgreSQL 서버 보호를 중지 하는 방법에는 세 가지가 있습니다.

- **보호 중지 및 데이터 보존 (영구적으로 유지)**:이 옵션은 이후의 모든 백업 작업이 Azure Database for PostgreSQL 서버를 보호 하는 것을 중지 하는 데 도움이 됩니다. 그러나 Azure Backup 서비스는 영구적으로 백업 되는 복구 지점이 유지 됩니다. 자격 증명 모음에서 복구 지점이 유지되도록 요금을 지불해야 합니다(자세한 내용은 [Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/) 참조). 필요한 경우 이러한 복구 지점은 복원할 수 있습니다. 보호를 다시 시작 하려면 **백업 다시 시작** 옵션을 사용 합니다.

- **보호 중지 및 데이터 보존 (정책에 따라 유지)**:이 옵션은 이후의 모든 백업 작업이 Azure Database for PostgreSQL 서버를 보호 하는 것을 중지 하는 데 도움이 됩니다. 복구 지점은 정책에 따라 유지 되 고 [Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/)에 따라 청구 가능한 됩니다. 그러나 최신 복구 지점은 영구적으로 보존 됩니다.

- **보호 중지 및 데이터 삭제**:이 옵션을 사용 하면 이후의 모든 백업 작업이 Azure Database for PostgreSQL 서버를 보호 하는 것을 중지 하 고 모든 복구 지점이 삭제 됩니다. 데이터베이스를 복원 하거나 **백업 다시 시작** 옵션을 사용할 수 없습니다.

### <a name="stop-protection-and-retain-data"></a>보호 중지 및 데이터 보존

1. **백업 센터** 로 이동 하 여 **Azure Database for PostgreSQL 서버 (미리 보기)** 를 선택 합니다.

1. 서버 백업 인스턴스 목록에서 유지 하려는 인스턴스를 선택 합니다.

1. **백업 중지 (미리 보기)** 를 선택 합니다.

   :::image type="content" source="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-inline.png" alt-text="중지할 Azure Database for PostgreSQL 서버 백업 인스턴스의 선택 항목을 보여 주는 스크린샷" lightbox="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-expanded.png":::

1. 다음 데이터 보존 옵션 중 하나를 선택 합니다.

   1. 영구적으로 유지
   1. 정책에 따라 유지
   
   :::image type="content" source="./media/manage-azure-database-postgresql/data-retention-options-inline.png" alt-text="선택할 데이터 보존 옵션을 보여 주는 스크린샷" lightbox="./media/manage-azure-database-postgresql/data-retention-options-expanded.png":::

   드롭다운 목록에서 백업을 중지 하는 이유를 선택할 수도 있습니다.

1. **백업 중지** 를 클릭 합니다.

1. **확인** 을 선택 하 여 백업을 중지 합니다.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="백업 중지 확인에 대 한 스크린샷" lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

1. **백업 센터** 로 이동 하 여 **Azure Database for PostgreSQL 서버 (미리 보기)** 를 선택 합니다.

1.  서버 백업 인스턴스 목록에서 삭제 하려는 인스턴스를 선택 합니다.

1. **백업 중지 (미리 보기)** 를 클릭 합니다.

1. **백업 데이터 삭제** 를 선택 합니다.

   백업 인스턴스의 이름, 삭제 이유 및 기타 주석을 제공 합니다.

   :::image type="content" source="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-inline.png" alt-text="백업 데이터를 삭제 하 고 추가 하는 데 필요한 세부 정보를 삭제 하는 옵션을 보여 주는 스크린샷" lightbox="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-expanded.png":::

1. **백업 중지** 를 선택합니다.

1. **확인** 을 선택 하 여 백업을 중지 합니다.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="백업 중지 확인에 대 한 스크린샷" lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

## <a name="resume-protection"></a>보호 다시 시작

데이터 백업을 중지 하는 동안 **보호 중지 및 데이터 보존** 옵션을 선택한 경우 Azure Database for PostgreSQL 서버에 대 한 보호를 다시 시작할 수 있습니다.

>[!Note]
>데이터베이스 보호를 시작 하면 보존 된 데이터에도 백업 정책이 적용 됩니다. 정책에 따라 만료 된 복구 지점은 정리 됩니다.

다음 단계를 사용합니다.

1. **백업 센터** 로 이동 하 여 **Azure Database for PostgreSQL 서버 (미리 보기)** 를 선택 합니다.

1. 서버 백업 인스턴스 목록에서 다시 시작 하려는 인스턴스를 선택 합니다.

1. **백업 다시 시작 (미리 보기)** 을 선택 합니다.

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-protection-inline.png" alt-text="데이터 보호를 다시 시작 하는 옵션을 보여 주는 스크린샷" lightbox="./media/manage-azure-database-postgresql/resume-data-protection-expanded.png":::

1. **백업 다시 시작** 을 선택 합니다.

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-backup-inline.png" alt-text="데이터 백업을 다시 시작 하는 옵션을 보여 주는 스크린샷" lightbox="./media/manage-azure-database-postgresql/resume-data-backup-expanded.png":::

## <a name="next-steps"></a>다음 단계

[Backup 자격 증명 모음 개요](backup-vault-overview.md)
