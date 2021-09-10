---
title: Azure SQL을 사용하는 Azure Active Directory 서비스 주체
description: AD 애플리케이션 활용(서비스 주체)은 Azure SQL Database 및 Azure SQL Managed Instance에서의 Azure AD 사용자 생성을 지원합니다.
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 05/11/2021
ms.openlocfilehash: 781cce588654ab5babcd74277a3fca97f9f906c1
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252568"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure SQL을 사용하는 Azure Active Directory 서비스 주체

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure AD(Azure Active Directory)는 Azure AD 애플리케이션(서비스 주체)을 대신하여 Azure SQL Database(SQL DB)에서 사용자 생성을 지원합니다.

> [!NOTE]
> 이 기능은 SQL Managed Instance에서 이미 지원 중입니다.

## <a name="service-principal-azure-ad-applications-support"></a>서비스 주체(Azure AD 애플리케이션) 지원

이 문서는 Azure AD와 통합되어 Azure AD 등록에 속한 애플리케이션에 적용됩니다. 애플리케이션을 통해 다양한 작업을 수행하려면 Azure SQL에 대한 인증 및 권한 부여 액세스가 필요합니다. 이 기능을 사용하면 서비스 주체가 SQL Database에서 Azure AD 사용자를 만들 수 있습니다. 제거된 Azure AD 애플리케이션을 대신하여 Azure AD 개체를 만들 수 없다는 제약이 있었습니다.

Azure Portal 또는 PowerShell 명령을 사용하여 Azure AD 애플리케이션을 등록하면 Azure AD 테넌트에 두 개체가 만들어집니다.

- 애플리케이션 개체
- 서비스 주체 개체

Azure AD 애플리케이션에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../../active-directory/develop/app-objects-and-service-principals.md) 및 [Azure PowerShell을 사용하여 Azure 서비스 주체 만들기](/powershell/azure/create-azure-service-principal-azureps)를 참조하세요.

SQL Database 및 SQL Managed Instance는 다음과 같은 Azure AD 개체를 지원합니다.

- Azure AD 사용자(관리, 페더레이션, 게스트)
- Azure AD 그룹(관리 및 페더레이션)
-  Azure AD 애플리케이션 

이제 Azure AD 애플리케이션 대신 `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` T-SQL 명령이 SQL Database에서 지원됩니다.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>서비스 주체를 사용한 Azure AD 사용자 생성 기능

이 기능을 지원하는 것은 Azure AD 개체를 만들고 사람이 상호 작용하지 않고 SQL Database 유지 관리하는 Azure AD 애플리케이션 자동화 프로세스에서 유용합니다. 서비스 주체는 그룹에 속한 사용자 또는 개별 사용자로서 SQL 논리 서버의 Azure AD 관리자가 될 수 있습니다. 애플리케이션은 시스템 관리자로 실행될 때 SQL Database에서의 Azure AD 개체 생성을 자동화할 수 있으며 이때 추가 SQL 권한은 필요하지 않습니다. 이를 통해 데이터베이스 사용자 생성을 완벽하게 자동화할 수 있습니다. 또한 이 기능은 서비스 주체를 대신하여 SQL Database 사용자로 만들 수 있는 Azure AD 시스템 할당 관리 ID 및 사용자 할당 관리 ID를 지원합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

## <a name="enable-service-principals-to-create-azure-ad-users"></a>서비스 주체를 사용하여 Azure AD 사용자 만들기

Azure AD 애플리케이션 대신 SQL Database에서 Azure AD 개체 만들기를 사용하려면 다음과 같은 설정이 필요합니다.

1. 서버 ID를 할당합니다. 할당된 서버 ID는 MSI(관리 서비스 ID)를 나타냅니다. 현재 Azure SQL의 서버 ID는 UMI(사용자 관리 ID)를 지원하지 않습니다.
    - 새 Azure SQL 논리 서버의 경우 다음 PowerShell 명령을 실행합니다.
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    자세한 내용은 [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) 명령을 참조하세요.

    - 기존 Azure SQL 논리 서버의 경우 다음 명령을 실행합니다.
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    자세한 내용은 [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) 명령을 참조하세요.

    - 서버 ID가 서버에 할당되었는지 확인하려면 Get-AzSqlServer 명령을 실행합니다.

    > [!NOTE]
    > REST API 및 CLI 명령을 사용하여 서버 ID를 할당할 수도 있습니다. 자세한 내용은 [az sql server create](/cli/azure/sql/server#az_sql_server_create), [az sql server update](/cli/azure/sql/server#az_sql_server_update) 및 [서버 - REST API](/rest/api/sql/2020-08-01-preview/servers)를 참조하세요.

2. 서버에 생성되거나 할당된 서버 ID에 Azure AD [**디렉터리 읽기 권한자**](../../active-directory/roles/permissions-reference.md#directory-readers) 권한을 부여합니다.
    - 이 권한을 부여하려면 [Azure AD 관리자 프로비저닝(SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance) 문서에 나오는 SQL Managed Instance에 사용된 설명을 따르세요.
    - 이 권한을 부여하는 Azure AD 사용자는 Azure AD **전역 관리자** 또는 **권한 있는 역할 관리자** 역할에 속해야 합니다.

> [!IMPORTANT]
> 1단계와 2단계는 위의 순서대로 실행해야 합니다. 먼저 서버 ID를 만들거나 할당한 다음 [**디렉터리 읽기 권한자**](../../active-directory/roles/permissions-reference.md#directory-readers) 권한을 부여합니다. 단계 중 하나 또는 둘 다 생략하면 Azure AD 애플리케이션 대신 Azure SQL에서 Azure AD 개체를 만드는 동안 실행 오류가 발생합니다.
>
> Azure AD의 그룹에 **디렉터리 읽기 권한자** 역할을 할당할 수 있습니다. 그런 다음, 그룹 소유자는 관리 ID를 이 그룹의 멤버로 추가할 수 있습니다. 그러면 **전역 관리자** 또는 **권한 있는 역할 관리자** 에게 **디렉터리 읽기 권한자** 역할을 부여할 필요가 없습니다. 이 기능에 대한 자세한 내용은 [Azure SQL용 Azure Active Directory의 Directory Readers 역할](authentication-aad-directory-readers-role.md)을 참조하세요.

## <a name="troubleshooting-and-limitations"></a>문제 해결 및 제한 사항

- 서버 ID를 사용하도록 설정하고 **디렉터리 읽기 권한자** 권한을 부여하지 않고 Azure AD 애플리케이션 대신 Azure SQL에서 Azure AD 개체를 만드는 경우 작업이 실패하고 다음과 같은 오류가 발생할 수 있습니다. 아래 예제 오류는 [자습서: Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기](authentication-aad-service-principal-tutorial.md) 문서에서 SQL Database 사용자 `myapp`을 만드는 PowerShell 명령 실행에 대한 것입니다.
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved. Error message:
    'Server identity is not configured. Please follow the steps in "Assign an Azure AD identity to your server and add
    Directory Reader permission to your identity" (https://aka.ms/sqlaadsetup)'"`
      - 위 오류의 경우 [Azure SQL 논리 서버에 ID를 할당](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server)하고 [SQL 논리 서버 ID에 디렉터리 읽기 권한자 권한을 할당](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)하는 단계를 따릅니다.
    - SQL Database에 대한 Azure AD 관리자로 서비스 주체(Azure AD 애플리케이션)를 설정하는 기능은 Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse), [REST API](/rest/api/sql/2020-08-01-preview/servers) 및 [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) 명령을 사용하여 지원됩니다.
- 다른 Azure AD 테넌트의 서비스 주체를 통해 Azure AD 애플리케이션을 사용하면 다른 테넌트에서 만든 SQL Database 또는 SQL Managed Instance에 액세스할 수 없게 됩니다. 이 애플리케이션에 할당된 서비스 주체는 SQL 논리 서버 또는 Managed Instance와 동일한 테넌트의 서비스 주체여야 합니다.
- [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 모듈 이상(PowerShell을 사용하여 개별 Azure AD 애플리케이션을 Azure SQL에 대한 Azure AD 관리자로 설정할 때 필요함). 최신 모듈로 업그레이드해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기](authentication-aad-service-principal-tutorial.md)
