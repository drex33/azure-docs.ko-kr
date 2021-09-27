---
title: SQL Server에서 Azure SQL Managed Instance로의 마이그레이션에 대한 평가 규칙
description: Azure SQL Managed Instance로 마이그레이션하기 전에 해결해야 하는 원본 SQL Server 인스턴스의 문제를 식별하는 평가 규칙입니다.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: rajeshsetlem
ms.author: rsetlem
ms.reviewer: mathoma, cawrites
ms.date: 12/15/2020
ms.openlocfilehash: 0d3ef1e9a7a4d30e4a716992caf4ec3f2f04cc1c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625585"
---
# <a name="assessment-rules-for-sql-server-to--azure-sql-managed-instance-migration"></a>SQL Server에서 Azure SQL Managed Instance로의 마이그레이션에 대한 평가 규칙
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

마이그레이션 도구는 SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션하기 전에 해결해야 하는 문제를 식별하는 다양한 평가 규칙을 실행하여 원본 SQL Server 인스턴스의 유효성을 검사합니다. 

이 문서에서는 SQL Server 데이터베이스에서 Azure SQL Managed Instance로의 마이그레이션이 실행 가능한지 평가하는 데 사용되는 규칙 목록을 제공합니다. 

## <a name="analysiscommand-job"></a>AnalysisCommand job<a id="AnalysisCommandJob"></a>

**제목: AnalysisCommand 작업 단계는 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 경고   


**설명**   
Analysis Services 명령을 실행하는 작업 단계입니다. AnalysisCommand 작업 단계는 Azure SQL Managed Instance에서 지원되지 않습니다.

**권장 사항**     
Azure Migrate의 영향을 받은 개체 섹션을 검토하여 Analysis Service 명령 작업 단계를 사용하는 모든 작업을 확인하고 작업 단계 또는 영향을 받은 개체를 제거할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 SQL Server 에이전트 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery 작업<a id="AnalysisQueryJob"></a>

**제목: AnalysisQuery 작업 단계는 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 경고   

**설명**   
Analysis Services 쿼리를 실행하는 작업 단계입니다. AnalysisQuery 작업 단계는 Azure SQL Managed Instance에서 지원되지 않습니다.


**권장 사항**   
Azure Migrate의 영향을 받은 개체 섹션을 검토하여 Analysis Service 쿼리 작업 단계를 사용하는 모든 작업을 확인하고 작업 단계 또는 영향을 받은 개체를 제거할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 SQL Server 에이전트 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>파일의 어셈블리<a id="AssemblyFromFile"></a>

**제목: 파일 매개 변수가 있는 'CREATE ASSEMBLY' 및 'ALTER ASSEMBLY'는 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 이슈   

**설명**   
Azure SQL Managed Instance는 파일 매개 변수와 함께 'CREATE ASSEMBLY' 또는 'ALTER ASSEMBLY'를 지원하지 않습니다. 이진 매개 변수는 지원됩니다. 파일 매개 변수가 사용되는 특정 개체의 경우 영향을 받은 개체 섹션을 참조하세요.

**권장 사항**   
파일 매개 변수와 함께 'CREATE ASSEMBLY' 또는 'ALTER ASSEMBLY'를 사용하여 개체를 검토합니다. 이러한 개체가 필요한 경우 파일 매개 변수를 이진 매개 변수로 변환합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다. 

추가 정보: [Azure SQL Managed Instance의 CLR 차이 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Bulk insert<a id="BulkInsert"></a>

**제목: 비 Azure Blob 데이터 원본에서의 BULK INSERT는 Azure SQL Managed Instance에서 지원되지 않습니다**   
**범주**: 이슈   

**설명**   
Azure SQL Managed Instance는 파일 공유 또는 Windows 폴더에 액세스할 수 없습니다. Azure Blob을 참조하지 않는 BULK INSERT 문의 구체적 사용에 대해서는 ‘영향을 받는 개체’ 섹션을 참조하세요. 원본이 Azure Blob Storage가 아닌 'BULK INSERT'가 포함된 개체는 Azure SQL Managed Instance로 마이그레이션한 후에 작동하지 않습니다.


**권장 사항**   
Azure SQL Managed Instance로 마이그레이션하는 경우 대신 Azure Blob Storage의 파일을 사용하도록 로컬 파일 또는 파일 공유를 사용하는 BULK INSERT 문을 변환해야 합니다.

추가 정보: [Azure SQL Managed Instance의 Bulk Insert 및 OPENROWSET 차이 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR 보안<a id="ClrStrictSecurity"></a>

**제목: SAFE 또는 EXTERNAL_ACCESS로 표시된 CLR 어셈블리는 UNSAFE로 간주됩니다.**    
**범주**: 경고   

**설명**   
CLR Strict Security 모드는 Azure SQL Managed Instance에서 적용됩니다. 이 모드는 기본적으로 사용하도록 설정되며 SAFE 또는 EXTERNAL_ACCESS로 표시된 사용자 정의 CLR 어셈블리가 포함된 데이터베이스에 대해 호환성이 손상되는 변경 내용을 적용합니다.


**권장 사항**   
CLR은 더 이상 보안 경계로 지원되지 않는 .NET Framework의 CAS(코드 액세스 보안)를 사용합니다. SQL Server 2017(14.x) 데이터베이스 엔진부터 CLR 어셈블리의 보안을 강화하기 위해 clr strict security라는 `sp_configure` 옵션이 도입되었습니다. clr strict security는 기본적으로 사용되며 SAFE 및 EXTERNAL_ACCESS CLR 어셈블리가 UNSAFE로 표시된 것처럼 처리됩니다. clr strict security를 사용하지 않도록 설정하면 PERMISSION_SET = SAFE로 만든 CLR 어셈블리에서 외부 시스템 리소스에 액세스하고, 비관리 코드를 호출하고, sysadmin 권한을 얻을 수 있습니다. strict security(엄격한 보안)를 사용하도록 설정하면 서명되지 않은 어셈블리는 로드되지 않습니다. 또한 데이터베이스에 SAFE 또는 EXTERNAL_ACCESS 어셈블리가 있는 경우 RESTORE 또는 ATTACH DATABASE 문을 완료할 수 있지만 어셈블리 로드에 실패할 수 있습니다. 어셈블리를 로드하려면 서버에 대한 UNSAFE ASSEMBLY 권한이 있는 해당 로그인이 포함된 인증서 또는 비대칭 키로 서명되도록 각 어셈블리를 변경하거나 삭제한 다음 다시 만들어야 합니다.

추가 정보: [CLR strict security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Compute 절<a id="ComputeClause"></a>

**제목: COMPUTE 절이 지원 중단되며 제거되었습니다**   
**범주**: 경고   

**설명**   
COMPUTE 절은 결과 집합의 끝에 추가 요약 열로 표시되는 합계를 생성합니다. 그러나 이 절은 Azure SQL Managed Instance에서 더 이상 지원되지 않습니다.



**권장 사항**   
대신 ROLLUP 연산자를 사용하여 T-SQL 모듈을 다시 작성해야 합니다. 아래 코드에서는 COMPUTE를 ROLLUP으로 대체하는 방법을 보여 줍니다. 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>암호화 공급자<a id="CryptographicProvider"></a>

**제목: CREATE CRYPTOGRAPHIC PROVIDER 또는 ALTER CRYPTOGRAPHIC PROVIDER가 사용된 경우를 발견했지만 이는 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 이슈   

**설명**   
Azure SQL Managed Instance는 파일에 액세스할 수 없기 때문에 CRYPTOGRAPHIC POVIDER 문을 지원하지 않습니다. CRYPTOGRAPHIC PROVIDER 문의 구체적 용도는 영향을 받는 개체 섹션을 참조하세요. 'CREATE CRYPTOGRAPHIC PROVIDER' 또는 'ALTER CRYPTOGRAPHIC PROVIDER'가 있는 개체는 Azure SQL Managed Instance로 마이그레이션한 후 제대로 작동하지 않습니다.


**권장 사항**   
'CREATE CRYPTOGRAPHIC PROVIDER' 또는 'ALTER CRYPTOGRAPHIC PROVIDER'를 사용하여 개체를 검토합니다. 필요한 모든 개체에서 이러한 기능의 사용을 제거합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다. 

추가 정보: [Azure SQL Managed Instance의 암호화 공급자 차이 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>데이터베이스 호환성<a id="DbCompatLevelLowerThan100"></a>

**제목: 100 미만의 데이터베이스 호환성 수준은 지원되지 않습니다.**    
**범주**: 경고   

**설명**   
데이터베이스 호환성 수준은 SQL Server 데이터베이스 엔진을 업그레이드할 수 있도록 하면서 동일한 업그레이드 전 데이터베이스 호환성 수준을 유지하여 연결하는 애플리케이션이 계속 작동하도록 함으로써 데이터베이스 현대화를 지원하는 유용한 도구입니다. Azure SQL Managed Instance는 100 미만의 호환성 수준을 지원하지 않습니다. 호환성 수준이 100 미만인 데이터베이스가 Azure SQL Managed Instance에서 복원되면 호환성 수준이 100으로 업그레이드됩니다. 


**권장 사항**: Azure SQL Managed Instance에서 데이터베이스 호환성 수준이 100으로 업그레이드될 때 애플리케이션 기능이 그대로 유지되는지 여부를 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance에서 지원되는 호환성 수준 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>데이터베이스 보안 주체 별칭<a id="DatabasePrincipalAlias"></a>

**제목: SYS.DATABASE_PRINCIPAL_ALIASES가 지원 중단되며 제거되었습니다**   
**범주**: 이슈   

**설명**   
SYS.DATABASE_PRINCIPAL_ALIASES가 Azure SQL Managed Instance에서 지원 중단되며 제거되었습니다.


**권장 사항**   
별칭 대신 역할을 사용해야 합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 옵션<a id="DisableDefCNSTCHK"></a>

**제목: SET 옵션 DISABLE_DEF_CNST_CHK가 지원 중단되며 제거되었습니다**   
**범주**: 이슈   

**설명**   
SET 옵션 DISABLE_DEF_CNST_CHK가 Azure SQL Managed Instance에서 중단되고 제거되었습니다.


추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 힌트<a id="FastFirstRowHint"></a>

**제목: FASTFIRSTROW 쿼리 힌트가 지원 중단되며 제거되었습니다**   
**범주**: 경고   

**설명**   
FASTFIRSTROW 쿼리 힌트가 Azure SQL Managed Instance에서 중단되고 제거되었습니다.


**권장 사항**   
FASTFIRSTROW 쿼리 힌트 대신 OPTION(FAST n)을 사용합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**제목: Filestream 및 Filetable은 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 이슈   

**설명**   
NTFS 파일 시스템에 텍스트 문서, 이미지 및 비디오와 같은 구조화되지 않은 데이터를 저장하는 데 사용할 수 있는 파일 스트림 기능은 Azure SQL Managed Instance에서 지원되지 않습니다. **Azure SQL Managed Instance에서 Filestream 파일 그룹이 포함된 백업을 복원할 수 없으므로 이 데이터베이스를 마이그레이션할 수 없습니다.**


**권장 사항**   
구조화되지 않은 파일을 Azure Blob Storage에 업로드하고 이러한 파일과 관련된 메타데이터(이름, 형식, URL 위치, 스토리지 키 등)를 Azure SQL Managed Instance에 저장합니다. Azure SQL Managed Instance에서 스트리밍 Blob을 사용할 수 있도록 애플리케이션을 다시 엔지니어링해야 할 수도 있습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Azure 간 Blob 스트리밍 블로그](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>다른 유형 MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**제목: 비 SQL Server 원격 서버를 사용하는 BEGIN DISTRIBUTED TRANSACTION은 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 이슈   

**설명**   
Transact SQL BEGIN DISTRIBUTED TRANSACTION으로 시작되고 MS DTC(Microsoft Distributed Transaction Coordinator)를 관리하는 분산 트랜잭션은 원격 서버가 SQL Server가 아닌 경우 Azure SQL Managed Instance에서 지원되지 않습니다. 


**권장 사항**   
BEGIN DISTRIBUTED TRANSACTION을 사용하여 모든 개체를 보려면 Azure Migrate의 영향을 받는 개체 섹션을 검토합니다. 여러 인스턴스에 걸친 분산 트랜잭션이 지원되는(현재 미리 보기 상태) Azure SQL Managed Instance로 참가자 데이터베이스를 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance에 대한 여러 서버 간 트랜잭션 ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>같은 유형 MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**제목: Azure SQL Managed Instance용 여러 서버에서 BEGIN DISTRIBUTED TRANSACTION이 지원됩니다.**    
**범주**: 이슈   

**설명**   
Transact SQL BEGIN DISTRIBUTED TRANSACTION으로 시작되고 MS DTC(Microsoft Distributed Transaction Coordinator)를 관리하는 분산 트랜잭션은 Azure SQL Managed Instance용 여러 서버에서 지원됩니다.


**권장 사항**   
BEGIN DISTRIBUTED TRANSACTION을 사용하여 모든 개체를 보려면 Azure Migrate의 영향을 받는 개체 섹션을 검토합니다. 여러 인스턴스에 걸친 분산 트랜잭션이 지원되는(현재 미리 보기 상태) Azure SQL Managed Instance로 참가자 데이터베이스를 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다. 

추가 정보: [Azure SQL Managed Instance에 대한 여러 서버 간 트랜잭션 ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>연결된 서버(비 SQL 공급자)<a id="LinkedServerWithNonSQLProvider"></a>

**제목: 비 SQL Server 공급자와의 연결된 서버는 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 이슈   

**설명**   
연결된 서버를 사용하면 SQL Server 데이터베이스 엔진에서 SQL Server 인스턴스 외부의 OLE DB 데이터 원본에 대해 명령을 실행할 수 있습니다. 비 SQL Server 공급자와의 연결된 서버는 Azure SQL Managed Instance에서 지원되지 않습니다. 


**권장 사항**   
원격 서버 공급자가 Oracle, Sybase 등과 같은 비 SQL Server인 경우 Azure SQL Managed Instance는 연결된 서버 기능을 지원하지 않습니다. 

연결된 서버에 대한 필요성을 제거하려면 다음 작업을 수행하는 것이 좋습니다. 
- 원격 비 SQL Server에서 종속 데이터베이스를 식별하고 이를 마이그레이션하는 데이터베이스로 이동하는 것이 좋습니다. 
- 종속 데이터베이스를 SQL Managed Instance, SQL Database, Azure Synapse SQL 및 SQL Server 인스턴스와 같은 지원되는 대상으로 마이그레이션합니다. 
- Azure SQL Managed Instance와 Azure 가상 머신(SQL VM)의 SQL Server 간에 연결된 서버를 만드는 것이 좋습니다.  그런 다음, SQL VM에서 Oracle, Sybase 등에 대한 연결된 서버를 만듭니다. 이 접근 방식에는 두 홉이 사용되지만 임시 해결 방법으로 사용할 수 있습니다.  
- 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 연결된 서버 차이 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>병합 조인<a id="MergeJob"></a>

**제목: Azure SQL Managed Instance에서 병합 작업 단계가 지원되지 않습니다.**    
**범주**: 경고   

**설명**   
복제 병합 에이전트를 활성화하는 작업 단계입니다. 복제 병합 에이전트는 데이터베이스 테이블에 저장된 초기 스냅샷 파일을 구독자에 적용하는 유틸리티 실행 파일입니다. 또한 이 에이전트는 초기 스냅샷이 만들어진 후 게시자에서 발생한 증분 데이터 변경 내용을 병합하고, 사용자가 구성한 규칙에 따라 또는 사용자가 만든 사용자 지정 해결 프로그램을 사용하여 충돌을 조정합니다. 병합 작업 단계는 Azure SQL Managed Instance에서 지원되지 않습니다.


**권장 사항**   
Azure Migrate의 영향을 받은 개체 섹션을 검토하여 병합 작업 단계를 사용하는 모든 작업을 확인하고 작업 단계 또는 영향을 받은 개체를 제거할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 SQL Server 에이전트 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI 데이터베이스 크기<a id="MIDatabaseSize<"></a>

**제목: Azure SQL Managed Instance는 8TB보다 큰 데이터베이스 크기를 지원하지 않습니다**   
**범주**: 이슈   

**설명**   
데이터베이스의 크기가 최대 인스턴스 예약 스토리지보다 큽니다. **크기가 허용된 제한을 초과하므로 이 데이터베이스를 마이그레이션하기 위해 선택할 수 없습니다.**


**권장 사항**   
데이터를 여러 데이터베이스에 보관하거나 압축 또는 분할할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 하드웨어 생성 특성 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI 인스턴스 크기<a id="MIInstanceSize<"></a>

**제목: Azure SQL Managed Instance의 최대 인스턴스 스토리지 크기는 8TB를 초과할 수 없습니다.**    
**범주**: 경고   

**설명**   
모든 데이터베이스의 크기가 최대 인스턴스 예약 스토리지보다 큽니다.  


**권장 사항**   
모든 데이터베이스가 동일한 인스턴스에 있어야 하는 경우 다른 Azure SQL Managed Instance 또는 Azure 가상 머신의 SQL Server로 데이터베이스를 마이그레이션하는 것이 좋습니다. 

추가 정보: [Azure SQL Managed Instance의 하드웨어 생성 특성 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>여러 로그 파일<a id="MultipleLogFiles<"></a>

**제목: Azure SQL Managed Instance는 여러 로그 파일을 지원하지 않습니다.**    
**범주**: 이슈   

**설명**   
SQL Server를 사용하면 하나의 데이터베이스에서 여러 파일에 기록할 수 있습니다. 이 데이터베이스에는 Azure SQL Managed Instance에서 지원되지 않는 여러 로그 파일이 있습니다. **Azure SQL Managed Instance에서 백업을 복원할 수 없으므로 이 데이터베이스를 마이그레이션할 수 없습니다. 
**

**권장 사항**   
Azure SQL Managed Instance는 데이터베이스당 단일 로그만 지원합니다. 이 데이터베이스를 Azure로 마이그레이션하기 전에 로그 파일 중 하나만 제외한 모든 로그 파일을 삭제해야 합니다. 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

추가 정보: [Azure SQL Managed Instance에서 지원되지 않는 데이터베이스 옵션 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>다음 열<a id="NextColumn"></a>

**제목: 이름이 NEXT인 테이블과 열은 Azure SQL Managed Instance에서 오류를 일으킵니다**   
**범주**: 이슈   

**설명**   
이름이 NEXT인 테이블 또는 열이 검색되었습니다. Microsoft SQL Server에서 도입된 시퀀스는 ANSI 표준 NEXT VALUE FOR 함수를 사용합니다. 테이블 또는 열 이름이 NEXT이고 열 별칭이 VALUE이며 ANSI 표준 AS가 생략된 경우 결과 문에서 오류가 발생할 수 있습니다.


**권장 사항**   
테이블 또는 열 별칭을 지정할 때 ANSI 표준 AS 키워드를 포함하도록 문을 다시 작성합니다. 예를 들어 열 이름이 NEXT이고 열 별칭이 VALUE인 경우 SELECT NEXT VALUE FROM TABLE 쿼리는 오류를 발생시키며 SELECT NEXT AS VALUE FROM TABLE로 다시 작성해야 합니다. 마찬가지로 테이블 이름이 NEXT이고 테이블 별칭이 VALUE인 경우 SELECT NEXT VALUE FROM TABLE 쿼리는 오류를 발생시키고 SELECT Col1 FROM NEXT AS VALUE로 다시 작성해야 합니다.



## <a name="non-ansi-style-left-outer-join"></a>ANSI 스타일이 아닌 왼쪽 우선 외부 조인<a id="NonANSILeftOuterJoinSyntax"></a>

**제목: 비 ANSI 스타일 왼쪽 우선 외부 조인이 지원 중단되며 제거되었습니다.**    
**범주**: 경고   

**설명**   
비 ANSI 스타일 왼쪽 우선 외부 조인은 Azure SQL Managed Instance에서 지원 중단되며 제거되었습니다. 


**권장 사항**   
ANSI 조인 구문을 사용합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>ANSI 스타일이 아닌 오른쪽 우선 외부 조인<a id="NonANSIRightOuterJoinSyntax"></a>

**제목: 비 ANSI 스타일 오른쪽 우선 외부 조인이 지원 중단되며 제거되었습니다.**    
**범주**: 경고   

**설명**   
비 ANSI 스타일 오른쪽 우선 외부 조인은 Azure SQL Managed Instance에서 지원 중단되며 제거되었습니다. 



추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**권장 사항**   
ANSI 조인 구문을 사용합니다.

## <a name="databases-exceed-100"></a>데이터베이스가 100을 초과합니다.<a id="NumDbExceeds100"></a>

**제목: Azure SQL Managed Instance는 인스턴스당 최대 100개의 데이터베이스를 지원합니다.**    
**범주**: 경고   

**설명**   
인스턴스 스토리지 크기 제한에 도달하지 않는 한, Azure SQL Managed Instance에서 지원되는 최대 데이터베이스 수는 100개입니다.



**권장 사항**   
모든 데이터베이스가 동일한 인스턴스에 있어야 하는 경우 다른 Azure SQL Managed Instance 또는 Azure 가상 머신의 SQL Server로 데이터베이스를 마이그레이션하는 것이 좋습니다. 

추가 정보: [Azure SQL Managed Instance 리소스 제한 ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET(비 Blob 데이터 원본)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**제목: 비 Azure Blob Storage 데이터 원본이 있는 대량 작업에 사용되는 OpenRowSet는 Azure SQL Managed Instance에서 지원되지 않습니다**   
**범주**: 이슈   

**설명**   
OPENROWSET은 파일의 데이터를 읽어서 행 세트로 반환할 수 있는 기본 제공 BULK 공급자를 통해 대량 작업을 지원합니다. 비 Azure Blob Storage 데이터 원본이 있는 OPENROWSET는 Azure SQL Managed Instance에서 지원되지 않습니다. 

**권장 사항**   
Azure SQL Managed Instance는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob Storage에서 파일을 가져와야 합니다. 따라서 OPENROWSET 함수에서는 Blob 유형 DATASOURCE만 지원됩니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 Bulk Insert 및 OPENROWSET 차이 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET(비 SQL 공급자)<a id="OpenRowsetWithNonSQLProvider"></a>

**제목: 비 SQL 공급자를 사용한 OpenRowSet는 Azure SQL Managed Instance에서 지원되지 않습니다**   
**범주**: 이슈   

**설명**   
이 방법은 OLE DB를 사용하여 원격 데이터에 연결하고 액세스하는 일회성의 임시 방법이며 연결된 서버에서 테이블을 액세스하는 방법의 대안입니다. 비 SQL 공급자를 사용한 OpenRowSet는 Azure SQL Managed Instance에서 지원되지 않습니다. 



**권장 사항**   
OPENROWSET 함수는 SQL Server 인스턴스(관리되는 인스턴스, 온-프레미스 인스턴스 또는 가상 머신 중 하나)에서만 쿼리를 실행하는 데 사용할 수 있습니다. SQLNCLI, SQLNCLI11 및 SQLOLEDB 값만 공급자로 지원됩니다. 따라서 권장 작업은 원격 비 SQL Server에서 종속 데이터베이스를 식별하고 이를 마이그레이션하는 데이터베이스로 이동하는 것입니다.

추가 정보: [Azure SQL Managed Instance의 Bulk Insert 및 OPENROWSET 차이 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell 작업<a id="PowerShellJob"></a>

**제목: Azure SQL Managed Instance에서 PowerShell 작업 단계가 지원되지 않습니다.**    
**범주**: 경고   

**설명**   
PowerShell 스크립트를 실행하는 작업 단계입니다. PowerShell 작업 단계는 Azure SQL Managed Instance에서 지원되지 않습니다. 



**권장 사항**   
Azure Migrate의 영향을 받은 개체 섹션을 검토하여 PowerShell 작업 단계를 사용하는 모든 작업을 확인하고 작업 단계 또는 영향을 받은 개체를 제거할 수 있는지 평가합니다.  Azure Automation을 사용할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 SQL Server 에이전트 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>큐 판독기 작업<a id="QueueReaderJob"></a>

**제목: Azure SQL Managed Instance에서 큐 판독기 작업 단계가 지원되지 않습니다.**    
**범주**: 경고   

**설명**   
복제 큐 판독기 에이전트를 활성화하는 작업 단계입니다. 복제 큐 판독기 에이전트는 Microsoft SQL Server 큐 또는 Microsoft 메시지 큐에 저장된 메시지를 읽고 해당 메시지를 게시자에 적용하는 실행 파일입니다. 큐 판독기 에이전트는 지연 업데이트를 허용하는 스냅샷 및 트랜잭션 게시와 함께 사용됩니다. Azure SQL Managed Instance에서 큐 판독기 작업 단계가 지원되지 않습니다.


**권장 사항**   
Azure Migrate의 영향을 받은 개체 섹션을 검토하여 큐 판독기 작업 단계를 사용하는 모든 작업을 확인하고 작업 단계 또는 영향을 받은 개체를 제거할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 SQL Server 에이전트 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**제목: 레거시 스타일 RAISERROR 호출을 현대적 방식으로 대체해야 합니다.**    
**범주**: 경고   

**설명**   
아래 예제와 같은 RAISERROR 호출은 쉼표와 괄호를 포함하지 않기 때문에 레거시 스타일이라고 합니다. RAISERROR 50001 '이것은 테스트입니다.' 이 RAISERROR 호출 메서드는 Azure SQL Managed Instance에서 중단되고 제거됩니다.



**권장 사항**   
최신 RAISERROR 구문을 사용하여 문을 다시 작성하거나 현대적 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 방식을 실행할 수 있는지 여부를 평가합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="sql-mail"></a>SQL 메일<a id="SqlMail"></a>

**제목: SQL 메일이 중단되었습니다.**    
**범주**: 경고   


**설명**   
SQL 메일은 Azure SQL Managed Instance에서 지원 중단되며 제거되었습니다.



**권장 사항**   
데이터베이스 메일을 사용합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**제목: Azure SQL Managed Instance에서 사용할 수 없는 제거된 시스템 저장 프로시저를 참조하는 문이 검색되었습니다.**    
**범주**: 경고   

**설명**   
지원되지 않는 `sp_dboption`, `sp_addserver`, `sp_dropalias`, `sp_activedirectory_obj`, `sp_activedirectory_scp`, `sp_activedirectory_start`와 같은 시스템 및 확장 저장 프로시저는 Azure SQL Managed Instance에서 사용할 수 없습니다. 




**권장 사항**   
Azure SQL Managed Instance에서 제거된 지원되지 않는 시스템 프로시저에 대한 참조를 제거합니다.

추가 정보:[SQL Server에서 중단된 데이터베이스 엔진 기능](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL 작업<a id="TransactSqlJob"></a>

**제목: Azure SQL Managed Instance의 TSQL 작업 단계에 지원되지 않는 명령이 포함되어 있습니다.**    
**범주**: 경고   


**설명**   
예약된 시간에 TSQL 스크립트를 실행하는 작업 단계입니다. TSQL 작업 단계에는 Azure SQL Managed Instance에서 지원되지 않는 명령이 포함되어 있습니다.



**권장 사항**   
Azure Migrate의 영향을 받는 개체 섹션을 검토하여 Azure SQL Managed Instance에서 지원되지 않는 명령이 포함된 모든 작업을 확인하고, 작업 단계 또는 영향을 받는 개체를 제거할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 SQL Server 에이전트 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>추적 플래그<a id="TraceFlags"></a>

**제목: Azure SQL Managed Instance에서 지원되지 않는 추적 플래그가 발견되었습니다.**    
**범주**: 경고   


**설명**   
Azure SQL Managed Instance는 제한된 수의 전역 추적 플래그만 지원합니다. 세션 추적 플래그는 지원되지 않습니다.



**권장 사항**   
Azure Migrate의 영향을 받는 개체 섹션을 검토하여 Azure SQL Managed Instance에서 지원되지 않는 추적 플래그를 모두 확인하고 제거할 수 있는지 평가합니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [추적 플래그](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows 인증<a id="WindowsAuthentication"></a>

**제목: Windows 인증(통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 경고   


**설명**   
Azure SQL Managed Instance는 두 가지 인증 유형을 지원합니다. 
- SQL 인증은 사용자 이름과 암호를 사용합니다.
- Azure Active Directory 인증은 Azure Active Directory에서 관리하는 ID를 사용하고 관리되고 통합된 도메인을 지원합니다. 

Windows 인증(통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Managed Instance에서 지원되지 않습니다. 


**권장 사항**   
로컬 Active Directory와 Azure Active Directory를 페더레이션합니다. 그런 다음, Windows ID를 해당하는 Azure Active Directory ID로 바꿀 수 있습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Managed Instance 보안 기능](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**제목: xp_cmdshell은 Azure SQL Managed Instance에서 지원되지 않습니다.**    
**범주**: 이슈   


**설명**   
Windows 명령 셸을 생성하고 실행을 위해 문자열을 전달하는 xp_cmdshell은 Azure SQL Managed Instance에서 지원되지 않습니다. 



**권장 사항**   
Azure Migrate의 영향을 받는 개체 섹션을 검토하여 xp_cmdshell을 사용하는 모든 개체를 확인하고 xp_cmdshell에 대한 참조 또는 영향을 받는 개체를 제거할 수 있는지 평가합니다. 클라우드 기반 자동화 및 구성 서비스를 제공하는 Azure Automation을 살펴보는 것이 좋습니다. 또는 Azure 가상 머신의 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Managed Instance의 저장 프로시저 차이 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>다음 단계

SQL Server를 Azure SQL Managed Instance로 마이그레이션하려면 [SQL Server에서 SQL Managed Instance로 마이그레이션 가이드](sql-server-to-managed-instance-guide.md)를 참조하세요.

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [데이터 마이그레이션에 사용할 수 있는 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- Azure SQL Managed Instance에 대해 자세히 알아보려면 다음을 참조하세요.
   - [Azure SQL Managed Instance의 서비스 계층](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server와 Azure SQL Managed Instance 간의 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)을 참조하세요.
- 데이터 액세스 레이어 A/B 테스트를 수행하는 방법에 관한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
