---
title: 서버 역할
titleSuffix: Azure SQL Database
description: 이 문서에서는의 논리 서버에 대 한 서버 역할의 개요를 제공 Azure SQL Database
ms.service: sql-database
ms.subservice: security
author: AndreasWolter
ms.author: anwolter
ms.topic: conceptual
ms.date: 09/02/2021
ms.reviewer: vanto
ms.openlocfilehash: cfb24ee98bf00fa46f75b0bef321a7b1694e2113
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630265"
---
# <a name="azure-sql-database-server-roles-for-permission-management"></a>권한 관리를 위한 Azure SQL Database 서버 역할

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 서버는 논리적 개념 이므로 서버 수준에서 사용 권한을 부여할 수 없습니다. 권한 관리를 간소화 하기 위해 Azure SQL Database는 [논리적 서버](logical-servers.md)에 대 한 사용 권한을 관리 하는 데 도움이 되는 일련의 고정 서버 수준 역할을 제공 합니다. 역할은 로그인을 그룹화 하는 보안 주체입니다.

> [!NOTE]
> 이 문서의 *역할* 개념은 Windows 운영 체제의 *그룹* 과 비슷합니다.

이러한 특수 고정 서버 수준 역할은 접두사 **# #MS_** 및 접미사를 사용 **##** 하 여 다른 일반 사용자 생성 주체와 구별 합니다.

온-프레미스 SQL Server 처럼 서버 사용 권한은 계층적으로 구성 됩니다. 이러한 서버 수준 역할이 보유 한 사용 권한은 데이터베이스 사용 권한으로 전파할 수 있습니다. 데이터베이스에 대 한 사용 권한을 효과적으로 전파 하려면 로그인에 데이터베이스의 사용자 계정이 있어야 합니다.

예를 들어 서버 수준 역할 **# #MS_ServerStateReader # #** 은 사용 권한 **뷰 서버 상태** 를 보유 합니다. 이 역할의 멤버인 로그인에 데이터베이스 *master* 및 *WideWorldImporters* 의 사용자 계정이 있는 경우이 사용자는 해당 두 데이터베이스에서 **데이터베이스 상태를 볼** 수 있는 권한을 갖습니다. 

> [!NOTE]
> 사용자 데이터베이스 내에서 모든 사용 권한을 거부할 수 있습니다. 실제로는 역할 멤버 자격을 통해 서버 차원의 권한을 재정의 합니다. 그러나 시스템 데이터베이스 *마스터* 에서는 사용 권한을 부여 하거나 거부할 수 없습니다.

현재 Azure SQL Database는 세 개의 고정 서버 역할을 제공 합니다. 고정 서버 역할에 부여 된 사용 권한은 변경할 수 없으며 이러한 역할은 다른 고정 역할을 구성원으로 가질 수 없습니다. 서버 수준 역할에 서버 수준 SQL 로그인을 멤버로 추가할 수 있습니다.

> [!IMPORTANT]
> 고정 서버 역할의 각 멤버는 같은 역할에 다른 로그인을 추가할 수 있습니다.

로그인 및 사용자 Azure SQL Database에 대 한 자세한 내용은 [SQL Database, SQL Managed Instance 및 Azure Synapse 분석에 대 한 데이터베이스 액세스 권한 부여](logins-create-manage.md)를 참조 하세요.
  
## <a name="built-in-server-level-roles"></a>기본 제공 서버 수준 역할

다음 표에서는 고정 서버 수준 역할과 해당 기능을 보여 줍니다.  
  
|기본 제공 서버 수준 역할|Description|  
|------------------------------|-----------------|  
|**# #MS_DefinitionReader ##**|**# #MS_DefinitionReader # #** 고정 서버 역할의 멤버는이 역할의 멤버에 게 사용자 계정이 있는 모든 데이터베이스에 대 한 **정의** 를 볼 수 있는 모든 **정의 보기** 에서 적용 되는 모든 카탈로그 뷰를 읽을 수 있습니다.|  
|**# #MS_ServerStateReader ##**|**# #MS_ServerStateReader # #** 고정 서버 역할의 멤버는 모든 dmv (동적 관리 뷰) 및 **뷰 서버 상태** 에 적용 되는 함수를 읽을 수 있습니다 .이 역할의 멤버에 게는 각각 사용자 계정이 있는 모든 데이터베이스의 **데이터베이스 상태가 표시** 됩니다.|
|**# #MS_ServerStateManager ##**|# **#MS_ServerStateManager # #** 고정 서버 역할의 멤버는 **# #MS_ServerStateReader # #** 역할과 동일한 권한을 갖습니다. 또한,,, 등의 여러 관리 작업에 대 한 액세스를 허용 하는 **ALTER SERVER STATE** 권한을 보유 합니다. `DBCC FREEPROCCACHE` `DBCC FREESYSTEMCACHE ('ALL')` `DBCC SQLPERF()` |  


## <a name="permissions-of-fixed-server-roles"></a>고정 서버 역할의 사용 권한

각 기본 제공 서버 수준 역할에는 특정 권한이 할당 되어 있습니다. 다음 표에서는 서버 수준 역할에 할당 된 사용 권한을 보여 줍니다. 또한 사용자 계정이 데이터베이스에 있는 경우에는 상속 된 데이터베이스 수준 사용 권한도 보여 줍니다.
  
|고정 서버 수준 역할|서버 수준 권한|데이터베이스 수준 권한 (로그인과 일치 하는 데이터베이스 사용자가 있는 경우)  
|-------------|----------|-----------------|  
|**# #MS_DefinitionReader ##**|모든 데이터베이스 보기, 모든 정의 보기, 보안 정의 보기|뷰 정의, 보안 정의 보기|  
|**# #MS_ServerStateReader ##**|서버 상태 보기, 서버 성능 상태 보기, 서버 보안 상태 보기|데이터베이스 상태 보기, 데이터베이스 성능 상태 보기, 데이터베이스 보안 상태 보기|  
|**# #MS_ServerStateManager ##**|서버 상태 변경, 서버 상태 보기, 서버 성능 상태 보기, 서버 보안 상태 보기|데이터베이스 상태 보기, 데이터베이스 성능 상태 보기, 데이터베이스 보안 상태 보기|   
  
  
## <a name="working-with-server-level-roles"></a>서버 수준 역할 작업

다음 표에서는 Azure SQL Database에서 서버 수준 역할을 수행 하는 데 사용할 수 있는 시스템 뷰 및 함수에 대해 설명 합니다.  
  
|기능|Type|Description|  
|-------------|----------|-----------------|  
|[IS_SRVROLEMEMBER&#40;Transact-SQL&#41;](/sql/t-sql/functions/is-srvrolemember-transact-sql)|메타데이터|SQL 로그인이 지정 된 서버 수준 역할의 멤버 인지 여부를 나타냅니다.|  
|[sys.server_role_members&#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-server-role-members-transact-sql)|메타데이터|각 서버 수준 역할의 각 멤버에 대해 행을 반환합니다.|
|[sys.sql_logins&#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-sql-logins-transact-sql)|메타데이터|각 SQL 로그인에 대해 하나의 행을 반환 합니다.|
|[ALTER SERVER ROLE&#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-server-role-transact-sql)|명령|서버 역할의 멤버 자격을 변경 합니다.| 

## <a name="examples"></a><a name="_examples"></a> 예

이 단원의 예제에서는 Azure SQL Database에서 서버 수준 역할을 사용 하는 방법을 보여 줍니다.  

### <a name="a-adding-a-sql-login-to-a-server-level-role"></a>A. 서버 수준 역할에 SQL 로그인 추가

다음 예에서는 서버 수준 역할 # #MS_ServerStateReader # #에 SQL 로그인 ' Jiao '를 추가 합니다. 이 문은 가상 master 데이터베이스에서 실행 해야 합니다.
  
```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao;  
GO
```  

### <a name="b-listing-all-principals-sql-authentication-which-are-members-of-a-server-level-role"></a>B. 서버 수준 역할의 멤버인 모든 보안 주체 (SQL 인증) 나열

다음 문은 `sys.server_role_members` 및 카탈로그 뷰를 사용 하 여 고정 된 서버 수준 역할의 모든 멤버를 반환 합니다 `sys.sql_logins` . 이 문은 가상 master 데이터베이스에서 실행 해야 합니다.
  
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
### <a name="c-complete-example-adding-a-login-to-a-server-level-role-retrieving-metadata-for-role-membership-and-permissions-and-running-a-test-query"></a>C. 전체 예제: 서버 수준 역할에 로그인 추가, 역할 멤버 자격 및 사용 권한에 대 한 메타 데이터 검색 및 테스트 쿼리 실행

#### <a name="part-1-preparing-role-membership-and-user-account"></a>1 부: 역할 멤버 자격 및 사용자 계정 준비

가상 master 데이터베이스에서이 명령을 실행 합니다.

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

사용자 데이터베이스에서이 명령을 실행 합니다.

```sql  
-- Creating a database-User for 'Jiao'
CREATE USER Jiao
    FROM LOGIN Jiao
;   
GO  
``` 

#### <a name="part-2-testing-role-membership"></a>2 부: 역할 멤버 자격 테스트

로그인으로 로그인 `Jiao` 하 고 예제에 사용 된 사용자 데이터베이스에 연결 합니다.

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

- 역할 할당은 적용 되는 데 최대 5 분이 걸릴 수 있습니다. 또한 기존 세션의 경우에는 연결을 닫았다가 다시 열 때까지 서버 역할 할당에 대 한 변경 내용이 적용 되지 않습니다. 이는 *master* 데이터베이스와 동일한 논리 서버에 있는 다른 데이터베이스 간의 분산 아키텍처 때문입니다.
  - 부분 해결 방법: 대기 기간을 줄이고 데이터베이스에서 서버 역할 할당을 최신으로 유지 하 고, 서버 관리자 또는 Azure AD 관리자를 `DBCC FLUSHAUTHCACHE` 로그인이 액세스할 수 있는 사용자 데이터베이스에서 실행할 수 있습니다. 현재 로그온 한 사용자에 게 `DBCC FLUSHAUTHCACHE` 는 멤버 자격 변경 내용을 적용 하기 위해를 실행 한 후에도 다시 연결 해야 합니다.

- `IS_SRVROLEMEMBER()`*master* 데이터베이스에서는 지원 되지 않습니다.


## <a name="see-also"></a>참조

- [데이터베이스 수준 역할](/sql/relational-databases/security/authentication-access/database-level-roles)   
- [보안 카탈로그 뷰&#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)   
- [보안 함수&#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)   
- [사용 권한&#40;데이터베이스 엔진&#41;](/sql/relational-databases/security/permissions-database-engine)
- [DBCC FLUSHAUTHCACHE(Transact-SQL)](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)
