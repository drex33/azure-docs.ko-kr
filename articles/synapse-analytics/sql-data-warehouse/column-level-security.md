---
title: 전용 SQL 풀에 대한 열 수준 보안
description: 열 수준 보안을 통해 고객은 사용자의 실행 컨텍스트 또는 그룹 멤버 자격에 따라 데이터베이스 테이블 열에 대한 액세스를 제어하여, 애플리케이션에서 보안의 디자인과 코딩을 간소화하고, 열 액세스에 대한 제한을 구현할 수 있습니다.
services: synapse-analytics
author: nanditavalsan
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: nanditav
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: f71cbc730bccb41a5a502d9b0e9ff382da501d5f
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133286855"
---
# <a name="column-level-security"></a>열 수준 보안

고객은 열 수준 보안을 통해 사용자의 실행 컨텍스트 또는 해당 그룹 구성원 자격에 따라 테이블 열에 대한 액세스를 제어할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
이 비디오가 게시된 이후에 Azure Synapse의 전용 SQL 풀에서 [행 수준 보안](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 사용할 수 있게 되었습니다.

열 수준 보안은 애플리케이션의 보안 디자인 및 코딩을 간소화하여 열 액세스를 제한함으로써 중요한 데이터를 보호할 수 있도록 합니다. 예를 들어 특정 사용자가 해당 부서와 관련된 테이블의 특정 열에만 액세스할 수 있도록 합니다. 액세스 제한 논리는 다른 애플리케이션 계층의 데이터와 다소 떨어진 데이터베이스 계층에 위치합니다. 데이터베이스는 모든 계층에서 데이터 액세스를 시도할 때마다 액세스 제한을 적용합니다. 이렇게 제한하면 전체 보안의 노출 영역이 줄어들어 보안 시스템을 보다 안정적이고 강력하게 만들 수 있습니다. 또한 열 수준 보안을 사용하면 사용자에게 액세스 제한을 적용하기 위해 열을 필터링하는 뷰를 도입할 필요가 없습니다.

[GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL 문을 사용하여 열 수준 보안을 구현할 수 있습니다. 이 메커니즘을 사용하면 SQL 및 Azure AD(Azure Active Directory) 인증이 모두 지원됩니다.

![다이어그램에는 첫 번째 열 맨 앞에는 닫힌 자물쇠가 있고 해당 셀은 주황색으로 표시되지만 다른 열은 흰색 셀을 포함하는 도식 테이블이 표시됩니다.](./media/column-level-security/cls.png)

## <a name="syntax"></a>구문

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>예제

다음 예제는 `Membership` 테이블의 `SSN` 열에 액세스하지 못하도록 `TestUser`를 제한하는 방법을 보여 줍니다.

사회 보장 번호를 저장하는 데 사용되는 SSN 열이 포함된 `Membership` 테이블을 생성합니다.

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

`TestUser`는 중요한 데이터가 포함된 SSN 열을 제외한 모든 열에 액세스할 수 있습니다.

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

`TestUser`로 실행된 쿼리는 SSN 열을 포함하는 경우 실패합니다.

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>사용 사례

현재 열 수준 보안을 사용하는 방법에 대한 몇 가지 예는 다음과 같습니다.

- 금융 서비스 회사는 계정 관리자만 고객 SSN(사회 보장 번호), 전화 번호 및 기타 개인 데이터에 액세스할 수 있도록 허용합니다.
- 의료 공급업체는 청구 부서의 구성원은 이 데이터를 볼 수 없는 반면, 의사와 간호사만 중요한 의료 기록에 액세스할 수 있도록 허용합니다.
