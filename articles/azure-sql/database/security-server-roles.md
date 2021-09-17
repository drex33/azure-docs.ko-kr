---
title: 서버 역할
titleSuffix: Azure SQL Database
description: 이 문서에서는 의 논리 서버에 대한 서버 역할의 개요를 제공합니다Azure SQL Database
ms.service: sql-database
ms.subservice: security
author: AndreasWolter
ms.author: anwolter
ms.topic: conceptual
ms.date: 09/02/2021
ms.reviewer: vanto
ms.openlocfilehash: 64c012373187059792174618920b797b2d081aa2
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537205"
---
# <a name="azure-sql-database-server-roles-for-permission-management"></a>권한 관리를 위한 서버 역할 Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 서버는 논리적 개념이며 서버 수준에서 권한을 부여할 수 없습니다. 권한 관리를 간소화하기 위해 Azure SQL Database 논리 서버에 대한 사용 권한을 관리하는 데 도움이 되는 고정 [서버](logical-servers.md)수준 역할 집합을 제공합니다. 역할은 로그인을 그룹화 하는 보안 주체입니다.

> [!NOTE]
> 이 문서의 *역할* 개념은 Windows 운영 체제의 *그룹과* 비슷합니다.

이러한 특수한 고정 서버 수준 역할은 **접두사 ##MS_** 및 접미사를 사용하여 **##** 사용자가 만든 다른 일반 보안 주체와 구별합니다.

SQL Server 온-프레미스와 마찬가지로 서버 권한은 계층적으로 구성됩니다. 이러한 서버 수준 역할에서 보유한 사용 권한은 데이터베이스 사용 권한으로 전파할 수 있습니다. 사용 권한을 데이터베이스에 효과적으로 전파하려면 로그인에 데이터베이스에 사용자 계정이 있어야 합니다.

예를 들어 서버 수준 역할 **##MS_ServerStateReader##에는** **VIEW SERVER STATE** 권한이 있습니다. 이 역할의 멤버인 로그인에 데이터베이스 *master* 및 *WideWorldImporters의* 사용자 계정이 있는 경우 이 사용자에게는 해당 두 데이터베이스의 **VIEW DATABASE STATE** 권한이 있습니다. 

> [!NOTE]
> 사용자 데이터베이스 내에서 모든 사용 권한을 거부할 수 있으며, 실제로 역할 멤버 자격을 통해 서버 차원의 권한 부여를 재정의할 수 있습니다. 그러나 시스템 데이터베이스 *마스터* 에서는 사용 권한을 부여하거나 거부할 수 없습니다.

Azure SQL Database 현재 세 개의 고정 서버 역할을 제공합니다. 고정 서버 역할에 부여된 사용 권한은 변경할 수 없으며 이러한 역할은 다른 고정 역할을 멤버로 사용할 수 없습니다. 서버 수준 SQL 로그인을 서버 수준 역할에 멤버로 추가할 수 있습니다.

> [!IMPORTANT]
> 고정 서버 역할의 각 멤버는 같은 역할에 다른 로그인을 추가할 수 있습니다.

Azure SQL Database 로그인 및 사용자에 대한 자세한 내용은 [SQL Database, SQL Managed Instance 및 Azure Synapse Analytics 대한 데이터베이스 액세스 권한 부여를](logins-create-manage.md)참조하세요.
  
## <a name="built-in-server-level-roles"></a>기본 제공 서버 수준 역할

다음 표에서는 고정 서버 수준 역할과 해당 기능을 보여 줍니다.  
  
|기본 제공 서버 수준 역할|Description|  
|------------------------------|-----------------|  
|**##MS_DefinitionReader ##**|**##MS_DefinitionReader##** 고정 서버 역할의 멤버는 이 역할의 멤버가 사용자 계정이 있는 모든 데이터베이스에서 **VIEW ANY DEFINITION** 에서 다루는 모든 카탈로그 **뷰를** 읽을 수 있습니다.|  
|**##MS_ServerStateReader ##**|**##MS_ServerStateReader##** 고정 서버 역할의 멤버는 이 역할의 멤버가 사용자 계정이 있는 모든 데이터베이스에서 **VIEW SERVER STATE** 에 적용되는 모든 DMV(동적 관리 뷰) 및 함수를 읽을 수 있습니다. |
|**##MS_ServerStateManager ##**|**##MS_ServerStateManager##** 고정 서버 역할의 멤버는 **##MS_ServerStateReader##** 역할과 동일한 권한을 갖습니다. 또한 **ALTER SERVER STATE** 권한을 보유하여 , , `DBCC FREEPROCCACHE` `DBCC FREESYSTEMCACHE ('ALL')` `DBCC SQLPERF()` 등의 여러 관리 작업에 액세스할 수 있습니다. |  


## <a name="permissions-of-fixed-server-roles"></a>고정 서버 역할의 권한

각 기본 제공 서버 수준 역할에는 할당된 특정 권한이 있습니다. 다음 표에서는 서버 수준 역할에 할당된 사용 권한을 보여줍니다. 또한 사용자 계정이 데이터베이스에 있는 경우 상속된 데이터베이스 수준 사용 권한도 보여 있습니다.
  
|고정 서버 수준 역할|서버 수준 권한|데이터베이스 수준 사용 권한(데이터베이스 사용자가 있는 경우)  
|-------------|----------|-----------------|  
|**##MS_DefinitionReader ##**|데이터베이스 보기, 정의 보기, 보안 정의 보기|정의 보기, 보안 정의 보기|  
|**##MS_ServerStateReader ##**|서버 상태 보기, 서버 성능 상태 보기, 서버 보안 상태 보기|데이터베이스 상태 보기, 데이터베이스 성능 상태 보기, 데이터베이스 보안 상태 보기|  
|**##MS_ServerStateManager ##**|서버 상태 변경, 서버 상태 보기, 서버 성능 상태 보기, 서버 보안 상태 보기|데이터베이스 상태 보기, 데이터베이스 성능 상태 보기, 데이터베이스 보안 상태 보기|   
  
  
## <a name="working-with-server-level-roles"></a>서버 수준 역할 작업

다음 표에서는 시스템 뷰 및 Azure SQL Database 서버 수준 역할을 사용하는 데 사용할 수 있는 함수에 대해 설명합니다.  
  
|기능|Type|Description|  
|-------------|----------|-----------------|  
|[IS_SRVROLEMEMBER&#40;Transact-SQL&#41;](/sql/t-sql/functions/is-srvrolemember-transact-sql)|메타데이터|SQL 로그인이 지정된 서버 수준 역할의 멤버인지 여부를 나타냅니다.|  
|[sys.server_role_members&#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-server-role-members-transact-sql)|메타데이터|각 서버 수준 역할의 각 멤버에 대해 행을 반환합니다.|
|[sys.sql_logins&#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-sql-logins-transact-sql)|메타데이터|각 SQL 로그인에 대해 하나의 행을 반환합니다.|
|[ALTER SERVER ROLE&#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-server-role-transact-sql)|명령|서버 역할의 멤버 자격을 변경합니다.| 

## <a name="examples"></a><a name="_examples"></a> 예

이 섹션의 예제에서는 Azure SQL Database 서버 수준 역할을 사용하는 방법을 보여줍니다.  

### <a name="a-adding-a-sql-login-to-a-server-level-role"></a>A. 서버 수준 역할에 SQL 로그인 추가

다음 예제에서는 'Jiao' SQL 로그인을 서버 수준 역할 ##MS_ServerStateReader##에 추가합니다. 이 문은 가상 master 데이터베이스에서 실행해야 합니다.
  
```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao;  
GO
```  

### <a name="b-listing-all-principals-sql-authentication-which-are-members-of-a-server-level-role"></a>B. 서버 수준 역할의 멤버인 모든 보안 주체(SQL 인증) 나열

다음 문은 및 카탈로그 뷰를 사용하여 고정 서버 수준 역할의 모든 멤버를 `sys.server_role_members` `sys.sql_logins` 반환합니다. 이 문은 가상 master 데이터베이스에서 실행해야 합니다.
  
```sql  
SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;  
GO  
```  
### <a name="c-complete-example-adding-a-login-to-a-server-level-role-retrieving-metadata-for-role-membership-and-permissions-and-running-a-test-query"></a>C. 전체 예제: 서버 수준 역할에 로그인 추가, 역할 멤버 자격 및 권한에 대한 메타데이터 검색 및 테스트 쿼리 실행

#### <a name="part-1-preparing-role-membership-and-user-account"></a>1부: 역할 멤버 자격 및 사용자 계정 준비

가상 master 데이터베이스에서 이 명령을 실행합니다.

```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao

-- check membership in metadata:
select IS_SRVROLEMEMBER('##MS_ServerStateReader##', 'Jiao')
--> 1 = Yes

SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;   
GO  
``` 

결과 집합은 다음과 같습니다.
  
```
MemberPrincipalID MemberPrincipalName RolePrincipalID RolePrincipalName        
------------- ------------- ------------------ -----------   
6         Jiao      11            ##MS_ServerStateReader##   
```  

사용자 데이터베이스에서 이 명령을 실행합니다.

```sql  
-- Creating a database-User for 'Jiao'
CREATE USER Jiao
    FROM LOGIN Jiao
;   
GO  
``` 

#### <a name="part-2-testing-role-membership"></a>2부: 역할 멤버 자격 테스트

로그인으로 `Jiao` 로그인하고 예제에 사용된 사용자 데이터베이스에 연결합니다.

```sql  
-- retrieve server-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'Server')
;  

-- check server-role membership for `##MS_ServerStateReader##` of currently logged on User
SELECT USER_NAME(), IS_SRVROLEMEMBER('##MS_ServerStateReader##')
--> 1 = Yes

-- Does the currently logged in User have the `VIEW DATABASE STATE`-permission?
SELECT HAS_PERMS_BY_NAME(NULL, 'DATABASE', 'VIEW DATABASE STATE'); 
--> 1 = Yes

-- retrieve database-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'DATABASE')
GO 

-- example query:
SELECT * FROM sys.dm_exec_query_stats
--> will return data since this user has the necessary permission

``` 

## <a name="limitations-of-server-level-roles"></a>서버 수준 역할의 제한 사항

- 역할 할당이 적용되기까지 최대 5분이 걸릴 수 있습니다. 또한 기존 세션의 경우 연결이 닫혀 다시 열릴 때까지 서버 역할 할당에 대한 변경 내용이 적용되지 않습니다. 이는 *master* 데이터베이스와 동일한 논리 서버의 다른 데이터베이스 간에 분산 아키텍처가 있기 때문입니다.
  - 부분 해결 방법: 대기 기간을 줄이고 서버 역할 할당이 데이터베이스, 서버 관리자 또는 Azure AD 관리자에 최신 상태인지 확인하려면 로그인에 액세스할 수 있는 사용자 데이터베이스에서 를 실행할 수 `DBCC FLUSHAUTHCACHE` 있습니다. 현재 로그온한 사용자는 멤버 자격 변경 내용이 적용되기 위해 를 실행한 후에도 다시 `DBCC FLUSHAUTHCACHE` 연결해야 합니다.

- `IS_SRVROLEMEMBER()` 는 *master* 데이터베이스에서 지원되지 않습니다.


## <a name="see-also"></a>참고 항목

- [데이터베이스 수준 역할](/sql/relational-databases/security/authentication-access/database-level-roles)   
- [보안 카탈로그 뷰&#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)   
- [보안 함수&#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)   
- [사용 권한&#40;데이터베이스 엔진&#41;](/sql/relational-databases/security/permissions-database-engine)
- [DBCC FLUSHAUTHCACHE(Transact-SQL)](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)
