---
title: 동적 데이터 마스킹
description: 동적 데이터 마스킹은 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에 대해 권한이 없는 사용자에게 마스킹하여 중요한 데이터 노출을 제한합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 09/12/2021
tags: azure-synpase
ms.openlocfilehash: 8a3740a228aa03a23f3584c3412b8451ebacc35e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812406"
---
# <a name="dynamic-data-masking"></a>동적 데이터 마스킹 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics는 동적 데이터 마스킹을 지원합니다. 동적 데이터 마스킹에서는 권한이 없는 사용자로 마스킹하여 중요한 데이터 노출을 제한합니다. 

동적 데이터 마스킹을 사용하면 고객이 애플리케이션 계층에 미치는 영향을 최소화하고 표시할 중요한 데이터의 양을 지정할 수 있게 하여 중요한 데이터에 대한 무닥 액세스를 방지할 수 있습니다. 데이터베이스의 데이터는 변경되지 않으면서 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다.

예를 들어 콜 센터의 서비스 담당자가 이메일 주소의 여러 문자를 확인하여 호출자를 식별할 수 있지만 전체 이메일 주소는 서비스 담당자에게 표시되지 않아야 합니다. 쿼리 결과 세트의 모든 이메일 주소를 마스킹하는 마스킹 규칙을 정의할 수 있습니다. 또 다른 예로, 개인 데이터를 보호하는 적합한 데이터 마스크를 정의할 수 있으므로 개발자가 준수 규정을 위반하지 않고 문제 해결을 위해 프로덕션 환경을 쿼리할 수 있습니다.

## <a name="dynamic-data-masking-basics"></a>동적 데이터 마스킹 기본 사항

SQL Database 구성 창의 **보안** 에서 **동적 데이터 마스킹** 블레이드를 선택하여 Azure Portal에서 동적 데이터 마스킹 정책을 설정합니다. 이 기능은 SQL Managed Instance용 포털을 사용하여 설정할 수 없습니다. 자세한 내용은 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)을 참조하세요.

### <a name="dynamic-data-masking-policy"></a>동적 데이터 마스킹 정책

* **마스킹에서 제외되는 SQL 사용자** - SQL 쿼리 결과에서 마스킹되지 않은 데이터를 가져오는 SQL 사용자 또는 Azure AD ID 세트입니다. 관리자 권한이 있는 사용자는 항상 마스킹에서 제외되며 마스크 없이 원본 데이터를 보게 됩니다.
* **마스킹 규칙** - 마스킹하도록 지정되는 필드와 사용할 마스킹 기능을 정의하는 규칙 집합입니다. 데이터베이스 스키마 이름, 테이블 이름 및 열 이름을 사용하여 마스킹하도록 지정되는 필드를 정의할 수 있습니다.
* **마스킹 함수** - 각 시나리오에서 데이터 표시를 제어하는 메서드 집합입니다.

| 마스킹 함수 | 마스킹 논리 |
| --- | --- |
| **기본값** |**지정된 필드의 데이터 형식에 따라 모든 데이터 마스킹**<br/><br/>• 문자열 데이터 형식(nchar, ntext, nvarchar)의 필드 크기가 4자 미만이면 XXXX개 이하의 X를 사용합니다.<br/>• 숫자 데이터 형식(bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real)의 경우 0 값을 사용합니다.<br/>• 날짜/시간 데이터 형식(date, datetime2, datetime, datetimeoffset, smalldatetime, time)의 경우 01-01-1900을 사용합니다.<br/>• SQL 변형의 경우 현재 형식의 기본값이 사용됩니다.<br/>• XML의 경우 \<masked/> 문서가 사용됩니다.<br/>• 특수 데이터 형식(타임스탬프 테이블, hierarchyid, GUID, 이진, 이미지, varbinary 공간 형식)의 경우 빈 값을 사용합니다. |
| **신용 카드** |**지정된 필드의 마지막 4자리를 표시** 하고 신용 카드 형식 접두사로 상수 문자열을 추가하는 마스킹 방법입니다.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |메일 주소 형식의 상수 문자열 접두사를 사용하여 **첫 글자를 표시하고 도메인을 XXX.com으로 바꾸는 마스킹 메서드** 입니다.<br/><br/>aXX@XXXX.com |
| **난수** |선택한 경계 및 실제 데이터 형식에 따라 **난수를 생성하는 마스킹 메서드** 입니다. 지정된 경계가 같으면 마스킹 함수로 상수가 사용됩니다.<br/><br/>![난수를 생성하기 위한 마스킹 방법을 보여주는 스크린샷.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **사용자 지정 텍스트** |**첫 문자와 마지막 문자를 표시** 하고 가운데에 사용자 지정 패딩 문자열을 추가하는 마스킹 메서드입니다. 원래 문자열이 노출된 접두사 및 접미사보다 짧은 경우 패딩 문자열만 사용됩니다. <br/>접두사[여백]접미사<br/><br/>![탐색 창](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>마스크 권장 필드

DDM 권장 사항 엔진은 중요한 필드일 가능성이 있어 마스크 대상이 될 만한 데이터베이스의 특정 필드를 플래그합니다. 포털의 동적 데이터 마스킹 블레이드에는 데이터베이스에 대한 권장 열이 표시됩니다. 이 필드에 마스크를 적용하려면 하나 이상의 열에 대해 **마스크 추가** 를 클릭한 다음 **저장** 을 클릭하기만 하면 됩니다.

## <a name="manage-dynamic-data-masking-using-t-sql"></a>T-SQL 사용하여 동적 데이터 마스킹 관리

- 동적 데이터 마스크를 만들려면 [Dynamic Data 마스크 만들기를 참조하세요.](/sql/relational-databases/security/dynamic-data-masking#creating-a-dynamic-data-mask)
- 기존 열에서 마스크를 추가하거나 편집하려면 기존 [열에서 마스크 추가 또는 편집을 참조하세요.](/sql/relational-databases/security/dynamic-data-masking#adding-or-editing-a-mask-on-an-existing-column)
- 마스킹되지 않은 데이터를 볼 수 있는 권한을 부여하려면 [마스킹되지 않은 데이터를 볼 수 있는 권한 부여를 참조하세요.](/sql/relational-databases/security/dynamic-data-masking#granting-permissions-to-view-unmasked-data)
- 동적 데이터 마스크를 삭제하려면 [Dynamic Data 마스크 삭제를 참조하세요.](/sql/relational-databases/security/dynamic-data-masking#dropping-a-dynamic-data-mask)

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정

### <a name="data-masking-policies"></a>데이터 마스킹 정책

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>데이터 마스킹 규칙

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>REST API를 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정

REST API를 사용하여 데이터 마스킹 정책 및 규칙을 프로그래밍 방식으로 관리할 수 있습니다. 게시된 REST API는 다음과 같은 작업을 지원합니다.

### <a name="data-masking-policies"></a>데이터 마스킹 정책

- [만들기 또는 업데이트](/rest/api/sql/2014-04-01/datamaskingpolicies/createorupdate): 데이터베이스 데이터 마스킹 정책을 만들거나 업데이트합니다.
- [가져오기](/rest/api/sql/2014-04-01/datamaskingpolicies/get): 데이터베이스 데이터 마스킹 정책을 가져옵니다. 

### <a name="data-masking-rules"></a>데이터 마스킹 규칙

- [만들기 또는 업데이트](/rest/api/sql/2014-04-01/datamaskingrules/createorupdate): 데이터베이스 데이터 마스킹 규칙을 만들거나 업데이트합니다.
- [데이터베이스별 목록](/rest/api/sql/2014-04-01/datamaskingrules/listbydatabase): 데이터베이스 데이터 마스킹 규칙 목록을 가져옵니다.

## <a name="permissions"></a>사용 권한

동적 데이터 마스킹을 구성하는 기본 제공 역할은 다음과 같습니다.
- [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- [SQL DB 기여자](../../role-based-access-control/built-in-roles.md#sql-db-contributor)
- [SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor)

동적 데이터 마스킹을 사용하는 데 필요한 작업은 다음과 같습니다.

읽기/쓰기:
- Microsoft.Sql/servers/databases/dataMaskingPolicies/* 읽기:
- Microsoft.Sql/servers/databases/dataMaskingPolicies/read Write:
-   Microsoft.Sql/servers/databases/dataMaskingPolicies/write

T-SQL 명령으로 동적 데이터 마스킹을 사용할 때의 사용 권한에 대한 자세한 내용은 [사용 권한을 참조하세요.](/sql/relational-databases/security/dynamic-data-masking#permissions)

## <a name="see-also"></a>참고 항목

- SQL Server용 [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking).
- 채널 9의 [Azure SQL 동적 데이터 마스킹에 대한 세분화된 권한](https://channel9.msdn.com/Shows/Data-Exposed/Granular-Permissions-for-Azure-SQL-Dynamic-Data-Masking)에 관한 데이터 노출 에피소드입니다.
