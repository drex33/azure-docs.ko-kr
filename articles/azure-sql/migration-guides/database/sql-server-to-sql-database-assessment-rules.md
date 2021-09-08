---
title: Azure SQL Database로 SQL Server 마이그레이션 평가 규칙
description: Azure SQL Database로 마이그레이션하기 전에 해결해야 하는 원본 SQL Server 인스턴스의 문제를 식별하는 평가 규칙입니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: rajeshsetlem
ms.author: rsetlem
ms.reviewer: mathoma, cawrites
ms.date: 12/15/2020
ms.openlocfilehash: ab030e8e4b86fc669325099e13650f8e1c178986
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835339"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>Azure SQL Database로 SQL Server 마이그레이션 평가 규칙
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

마이그레이션 도구는 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하기 전에 해결해야 하는 문제를 식별하는 다양한 평가 규칙을 실행하여 원본 SQL Server 인스턴스의 유효성을 검사합니다. 

이 문서에서는 SQL Server 데이터베이스의 Azure SQL Database 마이그레이션이 실행 가능한지 평가하는 데 사용되는 규칙 목록을 제공합니다. 


## <a name="bulk-insert"></a>BULK INSERT<a id="BulkInsert"></a>

**제목: 비 Azure Blob 데이터 원본에서의 BULK INSERT는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
Azure SQL Database가 파일 공유 또는 Windows 폴더에 액세스할 수 없습니다. Azure Blob을 참조하지 않는 BULK INSERT 문의 구체적 사용에 대해서는 ‘영향을 받는 개체’ 섹션을 참조하세요. 원본이 Azure Blob Storage가 아닌 'BULK INSERT'가 포함된 개체는 Azure SQL Database로 마이그레이션한 후에 작동하지 않습니다. 


**권장 사항**   
Azure SQL Database로 마이그레이션하는 경우 대신 Azure Blob Storage의 파일을 사용하도록 로컬 파일 또는 파일 공유를 사용하는 BULK INSERT 문을 변환해야 합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

## <a name="compute-clause"></a>COMPUTE 절<a id="ComputeClause"></a>

**제목: COMPUTE 절이 지원 중단되며 제거되었습니다**   
**범주**: 경고   

**설명**   
COMPUTE 절은 결과 집합의 끝에 추가 요약 열로 표시되는 합계를 생성합니다. 그러나 이 절은 Azure SQL Database에서 더 이상 지원되지 않습니다. 


**권장 사항**   
대신 ROLLUP 연산자를 사용하여 T-SQL 모듈을 다시 작성해야 합니다. 아래 코드에서는 COMPUTE를 ROLLUP으로 대체하는 방법을 보여 줍니다. 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능 ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="clr-assemblies"></a>CLR 어셈블리<a id="ClrAssemblies"></a>

**제목: SQL CLR 어셈블리는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   


**설명**   
Azure SQL Database는 SQL CLR 어셈블리를 지원하지 않습니다. 


**권장 사항**   
현재 Azure SQL Database에서 지원할 수 있는 방법은 없습니다. 권장되는 대체 해결 방법을 사용하려면 Azure SQL Database에서 지원하는 어셈블리만 사용하도록 애플리케이션 코드와 데이터베이스를 변경해야 합니다. 또는 Azure SQL Managed Instance로 마이그레이션하거나 Azure Virtual Machines의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Database에서 지원되지 않는 Transact-SQL 차이점](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>암호화 공급자<a id="CryptographicProvider"></a>

**제목: CREATE CRYPTOGRAPHIC PROVIDER 또는 ALTER CRYPTOGRAPHIC PROVIDER가 사용된 경우를 발견했지만 이는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
Azure SQL Database는 파일에 액세스할 수 없기 때문에 CRYPTOGRAPHIC POVIDER 문을 지원하지 않습니다. CRYPTOGRAPHIC PROVIDER 문의 구체적 용도는 영향을 받는 개체 섹션을 참조하세요. `CREATE CRYPTOGRAPHIC PROVIDER` 또는 `ALTER CRYPTOGRAPHIC PROVIDER`가 있는 개체는 Azure SQL Database로 마이그레이션한 후 제대로 작동하지 않습니다.  


**권장 사항**   
`CREATE CRYPTOGRAPHIC PROVIDER` 또는 `ALTER CRYPTOGRAPHIC PROVIDER`가 있는 개체를 검토합니다. 필요한 모든 개체에서 이러한 기능의 사용을 제거합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

## <a name="cross-database-references"></a>데이터베이스 간 참조<a id="CrossDataseReferences"></a>

**제목: 데이터베이스 간 쿼리는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
이 서버의 데이터베이스는 Azure SQL Database에서 지원되지 않는 데이터베이스 간 쿼리를 사용합니다. 


**권장 사항**   
Azure SQL Database는 데이터베이스 간 쿼리를 지원하지 않습니다. 다음과 같은 작업이 권장됩니다. 
- 종속 데이터베이스를 Azure SQL Database로 마이그레이션하고 탄력적 데이터베이스 쿼리(현재 미리 보기 상태) 기능을 사용하여 Azure SQL Database에서 쿼리합니다. 
- 다른 데이터베이스의 종속 데이터 세트를 마이그레이션하는 데이터베이스로 이동합니다. 
- Azure SQL Managed Instance로 마이그레이션합니다.
- Azure 가상 머신의 SQL Server로 마이그레이션합니다. 

추가 정보: [Azure SQL Database 탄력적 데이터베이스 쿼리(미리 보기) 확인](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>데이터베이스 호환성<a id="DbCompatLevelLowerThan100"></a>

**제목: Azure SQL Database는 100 미만의 호환성 수준을 지원하지 않습니다**   
**범주**: 경고   

**설명**   
데이터베이스 호환성 수준은 SQL Server 데이터베이스 엔진을 업그레이드할 수 있도록 하면서 동일한 업그레이드 전 데이터베이스 호환성 수준을 유지하여 연결하는 애플리케이션이 계속 작동하도록 함으로써 데이터베이스 현대화를 지원하는 유용한 도구입니다. Azure SQL Database는 100 미만의 호환성 수준을 지원하지 않습니다. 


**권장 사항**   
Azure SQL Managed Instance에서 데이터베이스 호환성 수준이 100으로 업그레이드될 때 애플리케이션 기능이 그대로 유지되는지 여부를 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

## <a name="database-mail"></a>데이터베이스 메일<a id="DatabaseMail"></a>

**제목: 데이터베이스 메일은 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 경고   

**설명**   
이 서버는 Azure SQL Database에서 지원되지 않는 데이터베이스 메일 기능을 사용합니다.


**권장 사항**   
데이터베이스 메일을 지원하는 Azure SQL Managed Instance로 마이그레이션하는 것이 좋습니다.  또는 Azure 함수 및 Sendgrid를 사용하여 Azure SQL Database에서 메일 기능을 수행하는 것이 좋습니다.

추가 정보: [Azure Functions 스크립트를 사용하여 Azure SQL Database에서 메일 보내기](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>데이터베이스 보안 주체 별칭<a id="DatabasePrincipalAlias"></a>

**제목: SYS.DATABASE_PRINCIPAL_ALIASES가 지원 중단되며 제거되었습니다**   
**범주**: 이슈   

**설명**   
SYS.DATABASE_PRINCIPAL_ALIASES가 Azure SQL Database에서 중단되고 제거되었습니다.  


**권장 사항**   
별칭 대신 역할을 사용해야 합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 옵션<a id="DisableDefCNSTCHK"></a>

**제목: SET 옵션 DISABLE_DEF_CNST_CHK가 지원 중단되며 제거되었습니다**   
**범주**: 이슈   

**설명**   
SET 옵션 DISABLE_DEF_CNST_CHK가 Azure SQL Database에서 중단되고 제거되었습니다.  


추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 힌트<a id="FastFirstRowHint"></a>

**제목: FASTFIRSTROW 쿼리 힌트가 지원 중단되며 제거되었습니다**   
**범주**: 경고   

**설명**   
FASTFIRSTROW 쿼리 힌트가 Azure SQL Database에서 중단되고 제거되었습니다.  


**권장 사항**   
FASTFIRSTROW 쿼리 힌트 대신 OPTION(FAST n)을 사용합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>파일 스트림<a id="FileStream"></a>

**제목: 파일 스트림은 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
NTFS 파일 시스템에 텍스트 문서, 이미지 및 비디오와 같은 구조화되지 않은 데이터를 저장하는 데 사용할 수 있는 파일 스트림 기능은 Azure SQL Database에서 지원되지 않습니다. 


**권장 사항**   
구조화되지 않은 파일을 Azure Blob Storage에 업로드하고 이러한 파일과 관련된 메타데이터(이름, 형식, URL 위치, 스토리지 키 등)를 Azure SQL Database에 저장합니다. Azure SQL Database에서 스트리밍 Blob을 사용할 수 있도록 애플리케이션을 다시 엔지니어링해야 할 수도 있습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL 간 스트리밍 Blob 블로그](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>연결된 서버<a id="LinkedServer"></a>

**제목: 연결된 서버 기능은 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
연결된 서버를 사용하면 SQL Server 데이터베이스 엔진에서 SQL Server 인스턴스 외부의 OLE DB 데이터 원본에 대해 명령을 실행할 수 있습니다. 


**권장 사항**   
Azure SQL Database는 연결된 서버 기능을 지원하지 않습니다. 연결된 서버에 대한 필요성을 제거하려면 다음 작업을 수행하는 것이 좋습니다. 
- 원격 SQL Server에서 종속 데이터 세트를 식별하고 이를 마이그레이션하는 데이터베이스로 이동하는 것이 좋습니다. 
- 종속 데이터베이스를 Azure로 마이그레이션하고 탄력적 데이터베이스 쿼리(미리 보기) 기능을 사용하여 Azure SQL Database의 데이터베이스에서 쿼리합니다. 

추가 정보: [Azure SQL Database 탄력적 쿼리(미리 보기) 확인](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**제목: BEGIN DISTRIBUTED TRANSACTION은 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
Transact SQL BEGIN DISTRIBUTED TRANSACTION으로 시작되고 MS DTC(Microsoft Distributed Transaction Coordinator)를 관리하는 분산 트랜잭션은 Azure SQL Database에서 지원되지 않습니다.  


**권장 사항**   
BEGIN DISTRIBUTED TRANSACTION을 사용하여 모든 개체를 보려면 Azure Migrate의 영향을 받는 개체 섹션을 검토합니다. 여러 인스턴스에 걸친 분산 트랜잭션이 지원되는(현재 미리 보기 상태) Azure SQL Managed Instance로 참가자 데이터베이스를 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance에 대한 여러 서버 간 트랜잭션 ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET(BULK)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**제목: 비 Azure Blob Storage 데이터 원본이 있는 대량 작업에 사용되는 OpenRowSet는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명** OPENROWSET는 파일의 데이터를 행 집합으로 읽고 쓸 수 있도록 하는 기본 제공 BULK 공급자를 통해 대량 작업을 지원합니다. 비 Azure Blob Storage 데이터 원본이 있는 OPENROWSET는 Azure SQL Database에서 지원되지 않습니다.


**권장 사항**   
Azure SQL Database는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob Storage에서 파일을 가져와야 합니다. 따라서 OPENROWSET 함수에서는 Blob 유형 DATASOURCE만 지원됩니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Database로 마이그레이션 중 Transact-SQL 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET(공급자)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**제목: SQL 또는 SQL 이외의 공급자를 사용한 OpenRowSet는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
SQL 또는 SQL 이외의 공급자를 사용하는 OpenRowSet는 연결된 서버의 테이블에 액세스하는 대안이며, OLE DB를 사용하여 원격 데이터에 연결하고 액세스하는 일회성의 임시 방법입니다. SQL 또는 SQL 이외의 공급자를 사용하는 OpenRowSet는 Azure SQL Database에서 지원되지 않습니다.


**권장 사항**   
Azure SQL Database는 Azure Blob Storage에서 가져오는 경우에만 OPENROWSET를 지원합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Database로 마이그레이션 중 Transact-SQL 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>비 ANSI 왼쪽 우선 외부 조인<a id="NonANSILeftOuterJoinSyntax"></a>

**제목: 비 ANSI 스타일 왼쪽 우선 외부 조인이 중단되고 제거되었습니다**   
**범주**: 경고   

**설명**   
비 ANSI 스타일 왼쪽 우선 외부 조인은 Azure SQL Database에서 중단되고 제거되었습니다. 


**권장 사항**   
ANSI 조인 구문을 사용합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>비 ANSI 오른쪽 우선 외부 조인<a id="NonANSIRightOuterJoinSyntax"></a>

**제목: 비 ANSI 스타일 오른쪽 우선 외부 조인이 중단되고 제거되었습니다**   
**범주**: 경고   

**설명**   
비 ANSI 스타일 오른쪽 우선 외부 조인은 Azure SQL Database에서 중단되고 제거되었습니다. 


**권장 사항**   
ANSI 조인 구문을 사용합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>다음 열<a id="NextColumn"></a>

**제목: 이름이 NEXT인 테이블과 열은 Azure SQL Database에서 오류를 일으킵니다**   
**범주**: 문제   

**설명**   
이름이 NEXT인 테이블 또는 열이 검색되었습니다. Microsoft SQL Server에서 도입된 시퀀스는 ANSI 표준 NEXT VALUE FOR 함수를 사용합니다. 테이블 또는 열 이름이 NEXT이고 열 별칭이 VALUE이며 ANSI 표준 AS가 생략된 경우 결과 문에서 오류가 발생할 수 있습니다.  


**권장 사항**   
테이블 또는 열 별칭을 지정할 때 ANSI 표준 AS 키워드를 포함하도록 문을 다시 작성합니다. 예를 들어 열 이름이 NEXT이고 열 별칭이 VALUE인 경우 `SELECT NEXT VALUE FROM TABLE` 쿼리는 오류를 발생시키며 SELECT NEXT AS VALUE FROM TABLE로 다시 작성해야 합니다. 마찬가지로 테이블 이름이 NEXT이고 테이블 별칭이 VALUE인 경우 `SELECT Col1 FROM NEXT VALUE` 쿼리는 오류를 발생시키며 `SELECT Col1 FROM NEXT AS VALUE`로 다시 작성해야 합니다.

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**제목: 레거시 스타일 RAISERROR 호출을 현대적 방식으로 대체해야 합니다.**    
**범주**: 경고   

**설명**   
아래 예제와 같은 RAISERROR 호출은 쉼표와 괄호를 포함하지 않기 때문에 레거시 스타일이라고 합니다. `RAISERROR 50001 'this is a test'`. 이 RAISERROR 호출 메서드는 Azure SQL Database에서 중단되고 제거됩니다. 


**권장 사항**   
최신 RAISERROR 구문을 사용하여 문을 다시 작성하거나 현대적 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 방식을 실행할 수 있는지 여부를 평가합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>서버 감사<a id="ServerAudits"></a>

**제목: Azure SQL Database 감사 기능을 사용하여 서버 감사를 대체합니다**   
**범주**: 경고   

**설명**   
서버 감사는 Azure SQL Database에서 지원되지 않습니다.


**권장 사항**   
Azure SQL Database 감사 기능으로 서버 감사를 대체하는 것이 좋습니다.  Azure SQL은 감사를 지원하며 기능은 SQL Server보다 더 다양합니다. Azure SQL Database는 데이터 액세스, 스키마 변경(DDL), 데이터 변경(DML), 계정, 역할 및 권한(DCL, 보안 예외)을 비롯한 다양한 데이터베이스 작업 및 이벤트를 감사할 수 있습니다. Azure SQL Database 감사는 조직이 데이터베이스에 대한 업데이트와 쿼리 등 데이터베이스 안에서 발생하는 변경 내용과 이벤트에 대한 깊이 있는 인사이트를 얻는 능력을 높입니다. 또는 Azure SQL Managed Instance로 마이그레이션하거나 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Database에 대한 감사 ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>서버 자격 증명<a id="ServerCredentials"></a>

**제목: 서버 범위 자격 증명은 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 경고   

**설명**   
자격 증명은 SQL Server 외부의 리소스에 연결하는 데 필요한 인증 정보(자격 증명)가 포함된 레코드입니다. Azure SQL Database는 데이터베이스 자격 증명을 지원하지만 SQL Server 범위에서 만들어진 자격 증명은 지원하지 않습니다.   


**권장 사항**   
Azure SQL Database는 데이터베이스 범위 자격 증명을 지원합니다. 서버 범위 자격 증명을 데이터베이스 범위 자격 증명으로 변환하세요. 또는 Azure SQL Managed Instance로 마이그레이션하거나 Azure Virtual Machines의 SQL Server로 마이그레이션합니다.

추가 정보: [데이터베이스 범위 자격 증명 만들기](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**제목: Service Broker 기능은 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
SQL Server Service Broker는 SQL Server 데이터베이스 엔진에서 메시징 및 큐 애플리케이션에 대한 기본 지원을 제공합니다. Service Broker 기능은 Azure SQL Database에서 지원되지 않습니다.


**권장 사항**   
Service Broker 기능은 Azure SQL Database에서 지원되지 않습니다. 동일한 인스턴스 내에서 Service Broker를 지원하는 Azure SQL Managed Instance로 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다. 

## <a name="server-scoped-triggers"></a>서버 범위 트리거<a id="ServerScopedTriggers"></a>

**제목: 서버 범위 트리거는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 경고   

**설명**   
트리거는 데이터 삽입, 삭제 또는 업데이트와 같은 테이블에서의 특정 작업에 대한 응답으로 실행되는 특별한 종류의 저장 프로시저입니다. 서버 범위 트리거는 Azure SQL Database에서 지원되지 않습니다. Azure SQL Database는 FOR LOGON, ENCRYPTION, WITH APPEND, NOT FOR REPLICATION, EXTERNAL NAME 옵션(외부 메서드 지원 없음), ALL SERVER 옵션(DDL 트리거), LOGON 이벤트 시 트리거(로그온 트리거) 등의 트리거 옵션을 지원하지 않습니다. Azure SQL Database는 CLR-트리거를 지원하지 않습니다.


**권장 사항**   
대신 데이터베이스 수준 트리거를 사용합니다. 또는 Azure SQL Managed Instance로 마이그레이션하거나 Azure Virtual Machines의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Database로 마이그레이션 중 Transact-SQL 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL 에이전트 작업<a id="AgentJobs"></a>

**제목: SQL Server 에이전트 작업은 Azure SQL Database에서 사용할 수 없습니다**   
**범주**: 경고   

**설명**   
SQL Server 에이전트는 SQL Server에서 ‘작업’이라고 하는 일정이 지정된 관리 작업을 실행하는 Microsoft Windows 서비스입니다. SQL Server 에이전트 작업은 Azure SQL Database에서 사용할 수 없습니다. 


**권장 사항** Azure SQL Database에서 SQL Server 에이전트 작업을 대체하는 탄력적 작업(미리 보기)을 사용합니다. Azure SQL Database에 대한 탄력적 데이터베이스 작업(미리 보기)을 사용하면 자동으로 다시 시도하여 최종 완료를 보장하는 동시에 여러 데이터베이스에 걸친 T-SQL 스크립트를 안정적으로 실행할 수 있습니다. 또는 Azure SQL Managed Instance로 마이그레이션하거나 Azure Virtual Machines의 SQL Server로 마이그레이션합니다.

추가 정보: [탄력적 데이터베이스 작업(미리 보기) 시작 ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL 데이터베이스 크기<a id="SQLDBDatabaseSize"></a>

**제목: Azure SQL Database는 100TB보다 큰 데이터베이스 크기를 지원하지 않습니다**   
**범주**: 문제   

**설명**   
데이터베이스 크기가 지원되는 최대 크기인 100TB보다 큽니다. 


**권장 사항**   
데이터를 여러 데이터베이스에 보관하거나 압축 또는 분할할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다. 

추가 정보: [vCore 리소스 한도](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL 메일<a id="SqlMail"></a>

**제목: SQL 메일이 중단되었습니다.**    
**범주**: 경고   

**설명**   
SQL 메일은 Azure SQL Database에서 중단되고 제거되었습니다.


**권장 사항**   
Azure SQL Managed Instance로 마이그레이션하거나 Azure Virtual Machines의 SQL Server로 마이그레이션하고 데이터베이스 메일을 사용하는 것이 좋습니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**제목: Azure SQL Database에서 사용할 수 없는 제거된 시스템 저장 프로시저를 참조하는 문이 검색되었습니다**   
**범주**: 경고   

**설명**   
지원되지 않는 다음 시스템 및 확장 저장 프로시저는 Azure SQL Database에서 사용할 수 없습니다. `sp_dboption`, `sp_addserver`, `sp_dropalias`, `sp_activedirectory_obj`, `sp_activedirectory_scp`, `sp_activedirectory_start`.


**권장 사항**    
Azure SQL Database에서 제거된 지원되지 않는 시스템 프로시저에 대한 참조를 제거합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>추적 플래그<a id="TraceFlags"></a>

**제목: Azure SQL Database는 추적 플래그를 지원하지 않습니다**   
**범주**: 경고   

**설명**   
추적 플래그는 특정 서버의 특성을 임시로 설정하거나 특정 동작을 중지시키는 데 사용합니다. 추적 플래그는 주로 성능 문제를 진단하거나 저장 프로시저 또는 복잡한 컴퓨터 시스템을 디버깅하는 데 사용됩니다. Azure SQL Database는 추적 플래그를 지원하지 않습니다. 


**권장 사항**   
Azure Migrate의 영향을 받는 개체 섹션을 검토하여 Azure SQL Database에서 지원되지 않는 추적 플래그를 모두 확인하고 제거할 수 있는지 평가합니다. 또는 제한된 수의 전역 추적 플래그를 지원하는 Azure SQL Managed Instance 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Database로 마이그레이션 중 Transact-SQL 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows 인증<a id="WindowsAuthentication"></a>

**제목: Windows 인증(통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 경고   

**설명**   
Azure SQL Database는 다음 두 가지 인증 유형을 지원합니다. 
- SQL 인증: 사용자 이름과 암호를 사용합니다. 
- Azure Active Directory 인증: Azure Active Directory에서 관리하는 ID를 사용하며, 관리되는 도메인과 통합된 도메인에서 지원됩니다. 

Windows 인증(통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Database에서 지원되지 않습니다. 



**권장 사항**   
로컬 Active Directory와 Azure Active Directory를 페더레이션합니다. 그런 다음, Windows ID를 해당하는 Azure Active Directory ID로 바꿀 수 있습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Database 보안 기능](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>xp_cmdshell<a id="XpCmdshell"></a>

**제목: xp_cmdshell은 Azure SQL Database에서 지원되지 않습니다**   
**범주**: 문제   

**설명**   
Windows 명령 셸을 생성하고 실행을 위해 문자열을 전달하는 xp_cmdshell은 Azure SQL Database에서 지원되지 않습니다. 


**권장 사항**   
Azure Migrate의 영향을 받는 개체 섹션을 검토하여 xp_cmdshell을 사용하는 모든 개체를 확인하고 xp_cmdshell에 대한 참조 또는 영향을 받는 개체를 제거할 수 있는지 평가합니다. 또한 클라우드 기반 자동화 및 구성 서비스를 제공하는 Azure Automation을 살펴보는 것이 좋습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다. 

## <a name="next-steps"></a>다음 단계

SQL Server를 Azure SQL Database로 마이그레이션하려면 [SQL Server에서 SQL Database로 마이그레이션 가이드](sql-server-to-sql-database-guide.md)를 참조하세요.

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [데이터 마이그레이션에 사용할 수 있는 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- SQL Database에 대해 자세히 알아보려면 다음을 참조하세요.
   - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 

- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)을 참조하세요.
- 데이터 액세스 레이어 A/B 테스트를 수행하는 방법에 관한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
