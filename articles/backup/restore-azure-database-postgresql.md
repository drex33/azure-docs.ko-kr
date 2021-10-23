---
title: 복원 Azure Database for PostgreSQL
description: Azure Database for PostgreSQL 백업을 복원 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: df48d37f61b910dd9e8396fb8460d98479186f9c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224033"
---
# <a name="restore-azure-database-for-postgresql-backups-preview"></a>Azure Database for PostgreSQL 백업 복원 (미리 보기)

이 문서에서는 Azure Backup에 의해 백업 되는 Azure PostgreSQL 서버로 데이터베이스를 복원 하는 방법을 설명 합니다.

서비스에 대상 서버에 대 한 적절 한 [사용 권한 집합이](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) 있는 경우 동일한 구독 내의 Azure PostgreSQL 서버로 데이터베이스를 복원할 수 있습니다.

## <a name="restore-a-database-on-the-target-storage-account"></a>대상 Storage 계정에서 데이터베이스 복원


1. **백업 자격 증명 모음**  ->  **백업 인스턴스** 로 이동 합니다. 데이터베이스를 선택 하 고 **복원** 을 클릭 합니다.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-database-for-restore-inline.png" alt-text="데이터베이스를 선택 하 고 복원 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/restore-azure-database-postgresql/select-database-for-restore-expanded.png":::

   또는 [백업 센터](./backup-center-overview.md)에서이 페이지로 이동할 수 있습니다.    
  
1. **복원 지점 선택** 페이지의 선택 된 백업 인스턴스에 대해 사용 가능한 모든 전체 백업 목록에서 복구 지점을 선택 합니다. 기본적으로 최신 복구 지점을 선택합니다.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-restore-point-inline.png" alt-text="복구 지점을 선택 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/restore-azure-database-postgresql/select-restore-point-expanded.png":::

   복원 지점이 보관 계층에 있는 경우 복원 하기 전에 복구 지점을 리하이드레이션 해야 합니다. 리하이드레이션에 필요한 다음과 같은 추가 매개 변수를 제공합니다.

   - **리하이드레이션 priority**: 기본값은 **표준** 입니다.
   - **리하이드레이션 duration**: 최대 리하이드레이션 기간은 30 일이 고 최소 리하이드레이션 기간은 10 일입니다. 기본값은 **15 일** 입니다. 이 기간 동안 복구 지점은 **백업 데이터 저장소** 에 저장 됩니다.

1. **복원 매개 변수** 페이지에서 복원 유형 ( **데이터베이스** **또는 파일로 복원)** 중 하나를 선택 합니다.

   - **데이터베이스로 복원**

     대상 서버는 원본 서버와 같을 수 있습니다. 그러나 원래 데이터베이스를 덮어쓰는 것은 지원되지 않습니다. 자격 증명 모음과 동일한 지역에 있는 모든 구독에 대해 서버에서 선택할 수 있습니다.

     1. 키 자격 **증명 모음 및 암호 선택** 드롭다운 목록에서 대상 서버에 연결 하기 위한 자격 증명을 저장 하는 자격 증명 모음을 선택 합니다.

     1. 유효성 검사를 트리거하기 위해 **검토 + 복원** 을 선택 하 여 [대상 서버에 대 한 복원 권한이](backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)서비스에 있는지 확인 합니다. 이러한 권한은 [수동으로 부여](backup-azure-database-postgresql-overview.md#grant-access-on-the-azure-postgresql-server-and-key-vault-manually)해야 합니다.

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-database-inline.png" alt-text="선택한 복원 유형을 데이터베이스로 복원으로 표시 하는 스크린샷" lightbox="./media/restore-azure-database-postgresql/restore-as-database-expanded.png":::

   - **파일로 복원: 대상 저장소 계정 (blob)에 백업 파일을 덤프 합니다.**

     저장소 계정은 모든 구독에서 선택할 수 있지만 자격 증명 모음과 동일한 지역에서 선택할 수 있습니다.     

     1. **대상 컨테이너 선택** 드롭다운 목록에서 선택한 저장소 계정에 대해 필터링 된 컨테이너 중 하나를 선택 합니다.
     1. 유효성 검사를 트리거하기 위해 **검토 + 복원** 을 선택 하 여 백업 서비스에 [대상 저장소 계정에 대 한 복원 권한이](#restore-permissions-on-the-target-storage-account)있는지 확인 합니다.

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-files-inline.png" alt-text="선택한 복원 유형을 파일로 복원으로 표시 하는 스크린샷" lightbox="./media/restore-azure-database-postgresql/restore-as-files-expanded.png":::
   
1. 복원 작업을 제출 하 고 **백업 작업** 에서 트리거된 작업을 추적 합니다.
   
   :::image type="content" source="./media/restore-azure-database-postgresql/track-triggered-job-inline.png" alt-text="백업 작업에서 추적 된 트리거된 작업을 보여 주는 스크린샷" lightbox="./media/restore-azure-database-postgresql/track-triggered-job-expanded.png":::

>[!NOTE]
>Azure Database for PostgreSQL에 대한 보관 지원은 제한된 공개 미리 보기로 제공됩니다.

## <a name="restore-permissions-on-the-target-storage-account"></a>대상 저장소 계정에 대 한 복원 권한

Azure Portal를 사용 하 여 백업 자격 증명 모음 MSI에 저장소 계정 컨테이너에 액세스할 수 있는 권한을 할당 합니다.

1. **스토리지 계정** -> **액세스 컨트롤** -> **역할 할당 추가** 로 이동합니다.

1. **역할** 드롭다운 목록에서 백업 자격 증명 모음 MSI에 **Storage Blob 데이터 참가자** 역할을 선택 합니다.

   :::image type="content" source="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-inline.png" alt-text="Azure Portal 사용 하 여 저장소 계정 컨테이너에 액세스할 수 있는 권한을 백업 자격 증명 모음 MSI에 할당 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-expanded.png":::

또는 Azure CLI [az role 대입문](/cli/azure/role/assignment) create 명령을 사용 하 여 복원 하는 특정 컨테이너에 세부적인 사용 권한을 부여 합니다.

```azurecli
az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
```
담당자 매개 변수를 자격 증명 모음 MSI의 _애플리케이션 ID_ 및 범위 매개 변수로 바꿔 특정 컨테이너를 참조합니다. 자격 증명 모음 MSI의 **응용 프로그램 ID** 를 가져오려면 **응용 프로그램 종류** 아래에서 **모든 응용 프로그램** 을 선택 합니다. 자격 증명 모음 이름을 검색 하 고 응용 프로그램 ID를 복사 합니다.

 :::image type="content" source="./media/restore-azure-database-postgresql/select-application-type-for-id-inline.png" alt-text="자격 증명 모음 MSI의 응용 프로그램 ID를 가져오는 프로세스를 보여 주는 스크린샷" lightbox="./media/restore-azure-database-postgresql/select-application-type-for-id-expanded.png":::

 :::image type="content" source="./media/restore-azure-database-postgresql/copy-vault-id-inline.png" alt-text="자격 증명 모음의 응용 프로그램 ID를 복사 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/restore-azure-database-postgresql/copy-vault-id-expanded.png":::
 
## <a name="next-steps"></a>다음 단계

[Azure Backup를 사용 하 여 PostgreSQL 데이터베이스 백업 문제 해결](backup-azure-database-postgresql-troubleshoot.md)