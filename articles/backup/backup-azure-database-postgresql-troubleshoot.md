---
title: Azure Database for PostgreSQL 백업 문제 해결
description: Azure Database for PostgreSQL 백업에 대한 문제 해결 정보입니다.
ms.topic: troubleshooting
ms.date: 09/22/2021
ms.openlocfilehash: 0dcb5b3a85fd1364d90327648274194d3bc30364
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634281"
---
# <a name="troubleshoot-postgresql-database-backup-by-using-azure-backup-preview"></a>Azure Backup 사용하여 PostgreSQL 데이터베이스 백업 문제 해결(미리 보기)

이 문서에서는 Azure Backup Azure PostgreSQL 데이터베이스를 백업하기 위한 문제 해결 정보를 제공합니다.

## <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

백업하거나 복원할 PG 서버에서 백업 자격 증명 모음 MSI에 **읽기** 액세스 권한을 부여합니다.

PostgreSQL 데이터베이스에 대한 보안 연결을 설정하기 위해 Azure Backup은 [MSI(관리 서비스 ID)](../active-directory/managed-identities-azure-resources/overview.md) 인증 모델을 사용합니다. 즉, 백업 자격 증명 모음은 사용자가 명시적으로 권한을 부여한 리소스에만 액세스할 수 있습니다.

시스템 MSI는 생성 시 자격 증명 모음에 자동으로 할당됩니다. 데이터베이스를 백업하려는 PostgreSQL 서버에 대한 액세스 권한을 이 자격 증명 모음 MSI에 부여해야 합니다.

단계:

1. Postgres server에서 **Access Control(IAM)** 창으로 이동합니다.

    ![액세스 제어 창](./media/backup-azure-database-postgresql/access-control-pane.png)

1. **역할 할당 추가** 를 선택합니다.

    ![역할 할당을 추가하는 스크린샷](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 열리는 오른쪽 컨텍스트 창에서 다음을 입력합니다.<br>

   - **역할:** 드롭다운 목록에서 **읽기 권한자** 역할을 선택합니다.<br>
   - **액세스 할당:** 드롭다운 목록에서 **사용자, 그룹 또는 서비스 주체** 옵션을 선택합니다.<br>
   - **선택:** 이 서버 및 해당 데이터베이스를 백업하려는 백업 자격 증명 모음 이름을 입력합니다.<br>

    ![역할을 선택하는 방법을 보여주는 스크린샷.](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

## <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Azure Active Directory를 사용하여 인증할 수 있는 데이터베이스 백업 사용자를 만듭니다.

PostgreSQL 서버에 대한 Azure Active Directory 관리자가 없거나 Azure Active Directory를 사용하여 인증할 수 있는 백업 사용자가 없을 때 이 오류가 발생할 수 있습니다.

단계:

OSS 서버에 Active Directory 관리자를 추가합니다.

이 단계는 암호 대신 Azure Active Directory를 사용하여 인증할 수 있는 사용자를 통해 데이터베이스에 연결하는 데 필요합니다. Azure Database for PostgreSQL의 Azure AD 관리 사용자에게 **azure_ad_admin** 역할이 있습니다. **Azure_ad_admin** 역할만 Azure AD를 사용하여 인증할 수 있는 새 데이터베이스 사용자를 만들 수 있습니다.

1. 서버 보기의 왼쪽 탐색 창에 있는 Active Directory 관리자 탭으로 이동하고 자신(또는 다른 사용자)을 Active Directory 관리자로 추가합니다.

    ![Active Directory 관리자를 설정하는 방법을 보여주는 스크린샷](./media/backup-azure-database-postgresql/set-admin.png)

1. AD 관리 사용자 설정을 **저장** 해야 합니다.

    ![Active Directory 관리 사용자 설정을 저장하는 방법을 보여주는 스크린샷.](./media/backup-azure-database-postgresql/save-admin-setting.png)

권한 부여 단계를 완료하기 위해 수행해야 하는 단계 목록은 [이 문서](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)를 참조하세요.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

서버 보기에서 **Azure 서비스에 대한 액세스 허용** 플래그를 사용하여 네트워크 시야를 설정합니다. 서버 보기의 **연결 보안** 창에서 **Azure 서비스에 대한 액세스 허용** 플래그를 **예** 로 설정합니다.

>[!Note]
>이 플래그를 사용하도록 설정하기 전에 **공용 네트워크 액세스 거부** 플래그를 **아니요로** 설정해야 합니다.

![Azure 서비스에 대한 액세스를 허용하는 방법을 보여주는 스크린샷](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

## <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>파일로 복원할 때 스토리지 계정 컨테이너에 복원할 수 있는 권한

1. 백업 자격 증명 모음 MSI에 Azure Portal 사용하여 스토리지 계정 컨테이너에 액세스할 수 있는 권한을 부여합니다.
    1. **스토리지 계정** -> **액세스 컨트롤** -> **역할 할당 추가** 로 이동합니다.
    1. **스토리지 Blob 데이터 기여자** 역할을 Backup 자격 증명 모음 MSI에 할당합니다.

    ![Storage Blob 데이터 기여자 역할을 할당하는 프로세스를 보여주는 스크린샷](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. 또는 Azure CLI [az role assignment create](/cli/azure/role/assignment) 명령을 사용하여 복원하려는 특정 컨테이너에 대해 세부적인 권한을 부여합니다.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. 담당자 매개 변수를 자격 증명 모음 MSI의 **애플리케이션 ID** 및 범위 매개 변수로 바꿔 특정 컨테이너를 참조합니다.
    1. 자격 증명 모음 MSI의 **애플리케이션 ID를** 얻으려면 **애플리케이션 유형** 아래에서 **모든 애플리케이션** 을 선택합니다.

        ![모든 애플리케이션을 선택하는 방법을 보여주는 스크린샷](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. 자격 증명 모음 이름을 검색하고 애플리케이션 ID를 복사합니다.

        ![자격 증명 모음 이름을 검색하는 방법을 보여주는 스크린샷.](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL 백업 정보(미리 보기)](backup-azure-database-postgresql-overview.md)
