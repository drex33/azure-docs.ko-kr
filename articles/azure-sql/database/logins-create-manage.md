---
title: 로그인 및 사용자 계정을 사용하여 서버 및 데이터베이스 액세스 인증
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL Database, SQL Managed Instance, Azure Synapse가 로그인 및 사용자 계정을 사용하여 사용자 액세스를 인증하는 방법을 알아봅니다. 데이터베이스 역할과 명시적 권한을 부여하여, 동작을 수행하고 데이터를 쿼리하기 위해 로그인 및 사용자 권한을 부여하는 방법도 알아봅니다.
keywords: sql 데이터베이스 보안,데이터베이스 보안 관리,로그인 보안,데이터베이스 보안,데이터베이스 액세스
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: AndreasWolter
ms.author: anwolter
ms.reviewer: vanto, mathoma
ms.date: 03/23/2020
ms.openlocfilehash: e8b058e18625d20427eb2c26cdc8c20817f640bf
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546990"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>SQL Database, SQL Managed Instance 및 Azure Synapse Analytics에 대한 데이터베이스 액세스 권한 부여
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 문서에서는 다음에 대해 알아봅니다.

- 사용자가 관리 작업을 수행하고 각 데이터베이스에 저장된 데이터에 액세스할 수 있도록 Azure SQL Database, Azure SQL Managed Instance와 Azure Synapse Analytics를 구성하는 옵션.
- 새 서버를 처음 만든 후의 액세스 및 권한 부여 구성.
- master 데이터베이스에서 로그인 및 사용자 계정을 추가하고 해당 계정에 관리 권한을 부여하는 방법.
- 사용자 계정을 로그인에 연결된 사용자 계정으로 또는 포함된 사용자 계정으로 사용자 데이터베이스에 추가하는 방법.
- 사용자 데이터베이스에서 데이터베이스 역할 및 명시적` 권한을 사용하여 권한과 함께 사용자 계정을 구성하는 방법.

> [!IMPORTANT]
> Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse의 데이터베이스는 이 문서의 나머지 부분에서 전체적으로 데이터베이스로 참조되며, 서버는 Azure SQL Database와 Azure Synapse용 데이터베이스를 관리하는 [서버](logical-servers.md)를 참조합니다.

> [!div class="nextstepaction"]
> [Azure SQL 개선하기 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

[**인증**](security-overview.md#authentication)은 사용자의 신원을 증명하는 과정입니다. 사용자는 사용자 계정을 사용하여 데이터베이스에 접속합니다.
데이터베이스 접속을 시도하는 사용자는 사용자 계정과 인증 정보를 제공합니다. 사용자는 다음과 같은 두 가지 인증 방법 중 하나를 사용하여 인증됩니다.

- [SQL 인증](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  이 인증 방법에서는 사용자가 사용자 계정 이름 및 암호를 제출하여 연결을 설정합니다. 암호는 로그인에 연결된 사용자 계정의 경우 master 데이터베이스에, 로그인에 연결되지 ‘않은’ 사용자 계정의 경우 사용자 계정을 포함하는 데이터베이스에 저장됩니다.
- [Azure Active Directory 인증](authentication-aad-overview.md)

  이 인증 방법에서는 사용자가 사용자 계정 이름을 제출하고 서비스가 Azure AD(Azure Active Directory)에 저장된 자격 증명 정보를 사용하도록 요청합니다.

**로그인 및 사용자**: 데이터베이스의 사용자 계정은 master 데이터베이스에 저장된 로그인에 연결되거나 개별 데이터베이스에 저장된 사용자 이름일 수 있습니다.

- **로그인** 은 master 데이터베이스에 있는 개별 계정으로, 하나 이상의 데이터베이스에 있는 사용자 계정과 연결될 수 있습니다. 로그인에는 해당 사용자 계정의 자격 증명 정보가 함께 저장됩니다.
- **사용자 계정** 은 임의 데이터베이스에 있는 개별 계정으로, 로그인과 연결될 수 있지만 반드시 연결되어야 하지는 않습니다. 로그인에 연결되지 않은 사용자 계정에는 해당 사용자 계정의 자격 증명 정보가 함께 저장됩니다.

데이터에 액세스하고 각종 동작을 수행하기 위한 [**권한 부여**](security-overview.md#authorization)는 데이터베이스 역할 및 명시적 권한을 사용하여 관리됩니다. 권한 부여는 사용자에게 할당되는 권한을 가리키며, 사용자가 수행할 수 있는 작업을 결정합니다. 권한 부여는 사용자 계정의 데이터베이스 [역할 구성원 자격](/sql/relational-databases/security/authentication-access/database-level-roles) 및 [개체 수준 권한](/sql/relational-databases/security/permissions-database-engine)에 의해 제어됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>새 데이터베이스를 만든 후의 기존 로그인 및 사용자 계정

처음으로 Azure SQL을 배포할 때 해당 로그인에 대한 관리자 로그인과 연결된 암호를 지정합니다. 이 관리 계정을 **서버 관리자** 라고 합니다. 다음과 같은 master 데이터베이스와 사용자 데이터베이스의 로그인 및 사용자 구성은 배포 중에 이루어집니다.

- 관리자 권한을 갖는 SQL 로그인이 지정된 로그인 이름을 사용하여 만들어집니다. [로그인](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login)은 SQL Database, SQL Managed Instance 및 Azure Synapse에 로깅하는 개인 사용자 계정입니다.
- 이 로그인에는 모든 데이터베이스에 대한 전체 관리 권한이 [서버 수준 보안 주체](/sql/relational-databases/security/authentication-access/principals-database-engine)로서 부여됩니다. 로그인에는 사용 가능한 모든 권한이 있으므로 제한할 수 없습니다. SQL Managed Instance에서는 이 로그인이 [sysadmin 고정 서버 역할](/sql/relational-databases/security/authentication-access/server-level-roles)에 추가됩니다(이 역할은 Azure SQL Database에는 없음).
- 각 사용자 데이터베이스에서 이 로그인에 대해 `dbo`라는 [사용자 계정](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users)이 생성됩니다. [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) 사용자는 데이터베이스에서 모든 데이터베이스 권한을 가지며 `db_owner` 고정 데이터베이스 역할에 매핑됩니다. 그 밖의 고정 데이터베이스 역할에 대해서는 이 문서의 뒷부분에서 설명합니다.

데이터베이스의 관리자 계정을 식별하려면 Azure Portal을 열고 서버 또는 관리형 인스턴스의 **속성** 탭으로 이동합니다.

![SQL Server 관리자](./media/logins-create-manage/sql-admins.png)

![속성 메뉴 옵션을 강조 표시하는 스크린샷.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> 관리자 로그인 이름을 만든 후에는 변경할 수 없습니다. 서버 관리자에 대한 암호를 다시 설정하려면 [Azure Portal](https://portal.azure.com)로 이동하여 **SQL Server** 를 클릭하고 목록에서 서버를 선택한 다음, **암호 재설정** 을 클릭합니다. SQL Managed Instance의 암호를 다시 설정하려면 Azure Portal로 이동하여 인스턴스를 클릭하고 **암호 재설정** 을 클릭합니다. PowerShell이나 Azure CLI를 사용할 수도 있습니다.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>관리자 권한을 갖는 추가 로그인 및 사용자 만들기

이 시점에서 서버 또는 관리되는 인스턴스는 단일 SQL 로그인 및 사용자 계정을 사용하는 액세스에 대해서만 구성됩니다. 전체 또는 부분 관리자 권한을 갖는 추가 로그인을 만들려면 (배포 모드에 따라) 다음과 같은 옵션을 사용할 수 있습니다.

- **전체 관리자 권한을 갖는 Azure Active Directory 관리자 계정 만들기**

  Azure Active Directory 인증을 사용하도록 설정하고 Azure AD 관리자 로그인을 만듭니다. 하나의 Azure Active Directory 계정을 전체 관리자 권한을 갖는 Azure SQL 배포의 관리자로 구성할 수 있습니다. 이 계정은 개별 계정일 수도 있고 보안 그룹 계정일 수도 있습니다. Azure AD 계정을 사용하여 SQL Database, SQL Managed Instance 또는 Azure Synapse에 연결하려면 Azure AD 관리자를 반드시 구성 **해야 합니다**. 모든 Azure SQL 배포 유형에 Azure AD 인증을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

  - [SQL을 사용하여 인증을 위해 Azure Active Directory 인증 사용](authentication-aad-overview.md)
  - [SQL을 사용하여 Azure Active Directory 인증 구성 및 관리](authentication-aad-configure.md)

- **SQL Managed Instance에서 전체 관리 권한이 있는 SQL 로그인을 만듭니다**

  - master 데이터베이스에서 추가 SQL 로그인을 만듭니다.
  - [ALTER SERVER ROLE](/sql/t-sql/statements/alter-server-role-transact-sql) 문을 사용하여 [sysadmin 고정 서버 역할](/sql/relational-databases/security/authentication-access/server-level-roles)에 로그인을 추가합니다. 이 로그인은 전체 관리자 권한을 갖습니다.
  - 또는 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) 구문을 사용하여 [Azure AD 로그인](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)을 만듭니다.

- **SQL Database에서 관리 권한이 제한된 SQL 로그인을 만듭니다.**

  - master 데이터베이스에서 추가 SQL 로그인을 만듭니다.
  - master 데이터베이스에 이 새로운 로그인에 연결된 사용자 계정을 만듭니다.
  - [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql) 문을 사용하여 `master` 데이터베이스에서 `dbmanager`, `loginmanager` 역할 또는 둘 다에 사용자 계정을 추가합니다(Azure Synapse의 경우 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 문 사용).

  > [!NOTE]
  > `dbmanager` 및 `loginmanager` 역할은 SQL Managed Instance 배포와 관련이 **없습니다**.

  Azure SQL Database의 이러한 [특수 master 데이터베이스 역할](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) 구성원에게는 데이터베이스를 만들고 관리하거나 로그인을 만들고 관리하는 권한이 있습니다. `dbmanager` 역할의 구성원인 사용자가 만든 데이터베이스에서, 해당 구성원은 `db_owner` 고정 데이터베이스 역할에 매핑되며 `dbo` 사용자 계정을 사용하여 해당 데이터베이스에 로그인하고 해당 데이터베이스를 관리할 수 있습니다. 이러한 역할에는 master 데이터베이스 외부에서는 명시적인 권한이 없습니다.

  > [!IMPORTANT]
  > SQL Database에서는 전체 관리자 권한을 갖는 추가 SQL 로그인을 만들 수 없습니다.

## <a name="create-accounts-for-non-administrator-users"></a>비관리자 사용자를 위한 계정 만들기

다음 두 가지 방법 중 하나를 사용하여 비관리자 사용자를 위한 계정을 만들 수 있습니다.

- **로그인 만들기**

  master 데이터베이스에서 SQL 로그인을 만듭니다. 그런 다음 사용자가 액세스해야 하는 각 데이터베이스에서 사용자 계정을 만들고 해당 사용자 계정을 로그인에 연결합니다. 이 접근 방식은 사용자가 여러 데이터베이스에 액세스해야 하며 암호를 일정하게 유지하려는 경우에 선호됩니다. 그러나 이 접근 방식에서는 주 서버와 보조 서버 양쪽에서 모두 로그인이 만들어져야 하므로 지역 복제와 함께 사용할 경우 복잡한 문제가 발생할 수 있습니다. 자세한 내용은 [지역 복원 또는 장애 조치(failover)를 위해 Azure SQL Database 보안 구성 및 관리](active-geo-replication-security-configure.md)를 참조하세요.
- **사용자 계정 만들기**

  사용자가 액세스해야 하는 데이터베이스에서 사용자 계정을 만듭니다([포함된 사용자](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - SQL Database로 항상 이러한 유형의 사용자 계정을 만들 수 있습니다.
  - [Azure AD 서버 주체](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 지원하는 SQL Managed Instance를 사용하면, 데이터베이스 사용자를 포함된 데이터베이스 사용자로 만들지 않고도 SQL Managed Instance에 인증할 사용자 계정을 만들 수 있습니다.

  이 접근 방식에서는 사용자 인증 정보가 각 데이터베이스에 저장되고 지역 복제된 데이터베이스로 자동 복제됩니다. 그러나 여러 데이터베이스에 동일한 계정이 존재하며 Azure SQL 인증을 사용하는 경우에는 암호를 수동으로 동기화된 상태로 유지해야 합니다. 또한, 사용자에게 다른 데이터베이스에 다른 암호를 갖는 계정이 있는 경우 암호를 기억하는 것이 문제가 될 수 있습니다.

> [!IMPORTANT]
> Azure AD ID에 매핑된 포함된 사용자를 만들려면 Azure SQL Database의 데이터베이스에서 관리자인 Azure AD 계정을 사용하여 로그인해야 합니다. SQL Managed Instance에서 `sysadmin` 권한이 있는 SQL 로그인 또한 Azure AD 로그인 또는 사용자를 만들 수 있습니다.

로그인 및 사용자를 만드는 방법을 보여 주는 예제는 다음을 참조하세요.

- [Azure SQL Database에 대한 로그인 만들기](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)
- [Azure SQL Managed Instance에 대한 로그인 만들기](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true#examples-2)
- [Azure Synapse의 로그인 만들기](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-3)
- [사용자 만들기](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD 포함된 사용자 만들기](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Azure SQL Database에서 사용자 만들기를 포함하는 보안 자습서는 [자습서: Azure SQL Database 보호](secure-database-tutorial.md)를 참조하세요.

## <a name="using-fixed-and-custom-database-roles"></a>고정 및 사용자 지정 데이터베이스 역할 사용

데이터베이스에서 로그인 기반 또는 포함된 사용자로서 사용자 계정을 만든 후에는 해당 사용자가 여러 동작을 수행하고 특정 데이터베이스에 있는 데이터에 액세스하도록 권한을 부여할 수 있습니다. 다음 방법을 사용하여 액세스 권한을 부여할 수 있습니다.

- **고정 데이터베이스 역할**

  사용자 계정을 [고정 데이터베이스 역할](/sql/relational-databases/security/authentication-access/database-level-roles)에 추가합니다. 고정 데이터베이스 역할은 9가지이며 각각 정의된 권한 세트가 있습니다. 가장 일반적인 고정 데이터베이스 역할은 **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** 및 **db_denydatareader** 입니다. **db_owner** 는 일반적으로 일부 사용자에게만 전체 권한을 부여하는 데 사용됩니다. 기타 고정된 데이터베이스 역할은 개발에서 단순한 데이터베이스를 신속하게 가져오는 데 유용하지만 대부분의 프로덕션 데이터베이스에는 권장되지 않습니다. 예를 들어 **db_datareader** 고정 데이터베이스 역할은 데이터베이스에 있는 모든 테이블에 대한 읽기 액세스 권한을 부여합니다. 읽기 액세스 권한은 반드시 필요한 권한 이상입니다.

  - 고정 데이터베이스 역할에 사용자를 추가하려면:

    - Azure SQL Database에서 [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql) 문을 사용합니다. 예제는 [ALTER ROLE 예제](/sql/t-sql/statements/alter-role-transact-sql#examples)를 참조하세요.
    - Azure Synapse에서 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 문을 사용합니다. 예제는 [sp_addrolemember 예제](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql#examples)를 참조하세요.

- **사용자 지정 데이터베이스 역할**

  [CREATE ROLE](/sql/t-sql/statements/create-role-transact-sql) 문을 사용하여 사용자 지정 데이터베이스 역할을 만듭니다. 사용자 지정 역할을 사용하면 사용자 정의 데이터베이스 역할을 만들고 각 역할에 비즈니스 요구 사항에 맞는 최소한의 권한을 신중하게 부여할 수 있습니다. 그런 다음 사용자 지정 역할에 사용자를 추가할 수 있습니다. 사용자가 여러 역할의 멤버인 경우 모두에 대한 권한을 집계합니다.
- **권한 직접 부여**

  사용자 계정 [권한](/sql/relational-databases/security/permissions-database-engine)을 직접 부여합니다. SQL Database에는 개별적으로 부여하거나 거부할 수 있는 100개가 넘는 사용 권한이 있습니다. 이러한 사용 권한은 대부분 중첩됩니다. 예를 들어 스키마에 대한 `UPDATE` 권한에는 해당 스키마 내에 있는 각 테이블에 대한 `UPDATE` 권한이 포함됩니다. 대부분의 사용 권한 시스템에서와 같이 사용 권한 거부는 권한 부여를 재정의합니다. 중첩된 특성과 사용 권한 수로 인해 데이터베이스를 제대로 보호할 적절한 사용 권한 시스템을 설계하는 데 신중을 기할 수 있습니다. [사용 권한(데이터베이스 엔진)](/sql/relational-databases/security/permissions-database-engine)에서 사용 권한 목록부터 시작하여 사용 권한의 [포스터 크기 그래픽](/sql/relational-databases/security/media/database-engine-permissions.png)을 검토하세요.

## <a name="using-groups"></a>그룹 사용

효율적인 액세스 관리에서는 개별 사용자가 아닌 Active Directory 보안 그룹 및 고정 또는 사용자 지정 역할에 할당된 권한을 사용합니다.

- Azure Active Directory 인증을 사용하는 경우 Azure Active Directory 사용자를 Azure Active Directory 보안 그룹에 배치합니다. 해당 그룹에 대해 포함된 데이터베이스 사용자를 만듭니다. 사용자 그룹에 적절한 특정 사용 권한이 있는 사용자 지정 또는 기본 제공 데이터베이스 역할에 하나 이상의 데이터베이스 사용자를 구성원으로 추가합니다.

- SQL 인증을 사용하는 경우 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다. 하나 이상의 데이터베이스 사용자를 해당 사용자 그룹에 적절한 권한을 갖는 사용자 지정 데이터베이스 역할에 배치합니다.

  > [!NOTE]
  > 포함되지 않은 데이터베이스 사용자에 대해서는 그룹을 사용할 수도 있습니다.

사용 권한을 제한 또는 확장시키는 데 사용할 수 있는 다음 기능을 잘 알고 있어야 합니다.

- [가장](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) 및 [모듈 서명](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)은 일시적으로 권한을 안전하게 상승시키는 데 사용할 수 있습니다.
- [행 수준 보안](/sql/relational-databases/security/row-level-security) 은 사용자가 액세스할 수 있는 행을 제한하는 데 사용할 수 있습니다.
- [데이터 마스킹](dynamic-data-masking-overview.md) 은 중요한 데이터의 노출을 제한하는 데 사용할 수 있습니다.
- [저장 프로시저](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

모든 Azure SQL Database 및 SQL Managed Instance 보안 기능의 개요는 [보안 개요](security-overview.md)를 참조하세요.
