---
title: 탄력적 쿼리 개요
description: 탄력적 쿼리를 사용하여 여러 데이터베이스에 걸쳐 있는 Transact-SQL 쿼리를 실행할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: mathoma
ms.date: 11/09/2021
ms.openlocfilehash: fa127df408ce8da080e6e0543f92fbdb001b4547
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136902"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL Database 탄력적 쿼리 개요(미리 보기)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

탄력적 데이터베이스 쿼리 기능(미리 보기)을 사용하면 Azure SQL Database에서 여러 데이터베이스에 걸쳐 있는 Transact-SQL 쿼리를 실행할 수 있습니다. 이를 통해 데이터베이스 간 쿼리를 수행하여 원격 테이블에 액세스하고 Microsoft와 타사 도구(Excel, PowerBI, Tableau 등)를 연결하여 여러 데이터베이스의 데이터 계층 전체를 쿼리할 수 있습니다. 이 기능을 사용하여 큰 데이터 계층에 대한 쿼리를 확장할 수 있으며 BI(비즈니스 인텔리전스) 보고서의 결과를 시각화할 수도 있습니다.

## <a name="why-use-elastic-queries"></a>탄력적 쿼리를 사용하는 이유

### <a name="azure-sql-database"></a>Azure SQL Database

완전히 T-SQL로 Azure SQL Database의 데이터베이스에서 쿼리합니다. 이 기능은 원격 데이터베이스에 대한 읽기 전용 쿼리를 허용하고 현재 SQL Server 고객에게 3부분 및 4부분 이름이나 SQL DB 연결 서버를 사용하여 애플리케이션을 마이그레이션하는 옵션을 제공합니다.

### <a name="available-on-all-service-tiers"></a>모든 서비스 계층에서 사용 가능

탄력적 쿼리는 Azure SQL Database의 모든 서비스 계층에서 지원됩니다. 낮은 서비스 계층에 대한 성능 제한 사항은 아래의 '미리 보기 제한 사항' 섹션을 참조하세요.

### <a name="push-parameters-to-remote-databases"></a>원격 데이터베이스에 매개 변수 푸시하기

이제 탄력적 쿼리가 SQL 매개 변수를 원격 데이터베이스에 푸시하여 실행할 수 있습니다.

### <a name="stored-procedure-execution"></a>저장 프로시저 실행

[sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database)를 사용하여 원격 저장된 프로시저 호출 또는 원격 함수를 실행합니다.

### <a name="flexibility"></a>유연성

탄력적 쿼리가 있는 외부 테이블이 다른 스키마나 테이블 이름이 있는 원격 테이블을 참조할 수 있습니다.

## <a name="elastic-query-scenarios"></a>탄력적 쿼리 시나리오

목표는 여러 데이터베이스를 통해 하나의 전체적인 결과를 얻을 수 있도록 쿼리 시나리오를 간소화하는 것입니다. 쿼리는 사용자 또는 애플리케이션에서 직접 작성할 수도 있고 데이터베이스에 연결된 도구를 통해 간접적으로 작성할 수도 있습니다. 이것은 보고서를 만들거나, 유료 BI 또는 데이터 통합 도구나 변경할 수 없는 모든 소프트웨어를 사용할 때 특히 유용합니다. 탄력적 쿼리를 사용하여 Excel, PowerBI, Tableau 또는 Cognos 같은 도구에서 친숙한 SQL 서버 연결 환경을 사용하여 여러 데이터베이스를 쿼리할 수 있습니다.
탄력적 쿼리는 또한 SQL Server Management Studio 또는 Visual Studio에서 발급하는 쿼리를 통해 전체 데이터베이스  컬렉션에 쉽게 액세스할 수 있으며, Entity Framework 또는 기타 ORM 환경에서 데이터 베이스 간 쿼리를 수월하게 해줍니다. 그림 1에서는 기존 클라우드 애플리케이션([탄력적 데이터베이스 클라이언트 라이브러리](elastic-database-client-library.md)사용)이 수평 확장 데이터 계층에서 구축되고, 탄력적 쿼리가 데이터베이스 간 보고에 사용되는 것을 보여 줍니다.

**그림 1** 수평 확장된 데이터 계층에서 사용하는 탄력적 쿼리

![수평 확장된 데이터 계층에서 사용하는 탄력적 쿼리][1]

탄력적 쿼리의 고객 시나리오는 다음 포톨로지로 구분됩니다.

* **수직 분할 – 데이터베이스 간 쿼리** (토폴로지 1): 한 데이터 계층의 많은 데이터베이스 간에 데이터를 수직으로 분할합니다. 일반적으로 서로 다른 테이블 집합이 서로 다른 데이터베이스에 상주합니다. 즉 스키마가 데이터베이스마다 서로 다릅니다. 예를 들어, 재고의 모든 테이블은 한 데이터베이스 안에 있지만 모든 회계 관련 테이블은 보조 데이터베이스에 있습니다. 이 토폴로지의 일반적인 사용 사례에서는 여러 데이터베이스의 테이블에서 쿼리하거나 보고서를 컴파일해야 합니다.
* **행 분할 – 분할** (토폴로지 2): 데이터가 수평 확장된 데이터 계층에서 수평으로 배포되기 위해 분할됩니다. 이 방법에서는 스키마가 모든 분할 데이터베이스에서 동일합니다. 이 방법을 "분할"이라고도 합니다. 분할은 (1)탄력적 데이터베이스 도구 라이브러리 또는 (2)자체 분할을 사용하여 수행 및 관리됩니다. 탄력적 쿼리는 여러 분할에서 보고서를 쿼리 또는 컴파일하는 데 사용됩니다. 분할된 데이터베이스는 일반적으로 탄력적 풀 내의 데이터베이스입니다. 탄력적 쿼리는 데이터베이스가 공통 스키마를 공유하는 한, 탄력적 풀의 모든 데이터베이스를 한 번에 쿼리하는 효율적인 방법으로 간주할 수 있습니다.

> [!NOTE]
> 탄력적 쿼리는 대부분의 처리(필터링, 집계)를 외부 소스 쪽에서 수행할 수 있는 보고 시나리오에 가장 적합합니다. 원격 데이터베이스에서 많은 양의 데이터를 전송하는 ETL 작업에는 적합하지 않습니다. 쿼리가 보다 복잡한 과도한 보고 작업 부하 또는 데이터 웨어하우징 시나리오의 경우, [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) 사용도 고려하세요.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>수직 분할 – 데이터베이스 간 쿼리

코딩을 시작하려면 [데이터베이스 간 쿼리 시작(수직 분할)](elastic-query-getting-started-vertical.md)을 참조하세요.

탄력적 쿼리를 사용하여 SQL Database의 데이터베이스에 있는 데이터를 다른 SQL Database의 데이터베이스에서 사용하도록 할 수 있습니다. 이렇게 하면 한 데이터베이스의 쿼리가 SQL Database의 다른 모든 원격 데이터베이스의 테이블을 참조할 수 있습니다. 우선 각각의 원격 데이터베이스에 대해 외부 데이터 원본을 정의합니다. 외부 데이터 원본은 원격 데이터베이스에 위치한 테이블에 대한 액세스를 부여하려는 로컬 데이터베이스에서 정의합니다. 원격 데이터베이스 변경은 필요하지 않습니다. 서로 다른 데이터베이스가 각기 다른 스키마를 갖는 일반 세로 분할 시나리오에서는 탄력적 쿼리를 사용하여 데이터 참조 액세스, 데이터베이스 간 쿼리 등의 일반적인 사용 사례를 구현할 수 있습니다.

> [!IMPORTANT]
> 사용자는 모든 외부 데이터 원본 ALTER 권한이 있어야 합니다. 이 사용 권한은 ALTER DATABASE 권한에 포함됩니다. 기본 데이터 원본을 참조하기 위해 ALTER ANY EXTERNAL DATA SOURCE 권한이 필요합니다.
>

**참조 데이터**: 토폴로지는 참조 데이터 관리에 사용됩니다. 아래 그림에서는 전용 데이터베이스에 참조 데이터가 있는 두 테이블(T1과 T2)이 유지됩니다. 이제 그림에서처럼 탄력적 쿼리를 사용하여 다른 데이터베이스에서 원격으로 T1과 T2 테이블에 액세스할 수 있습니다. 참조 테이블이 작거나 참조 테이블에 대한 원격 쿼리에 선택적 조건자가 있으면 토폴로지 1을 사용합니다.

**그림 2** 수직 분할 - 탄력적 쿼리를 사용하여 참조 데이터 쿼리

![수직 분할 - 탄력적 쿼리를 사용하여 참조 데이터 쿼리][3]

**데이터베이스 간 쿼리**: 탄력적 쿼리를 사용하면 SQL Database의 여러 데이터베이스 간 쿼리가 필요한 사용 사례를 구현할 수 있습니다. 그림 3에는 CRM, 재고, HR 및 제품 등, 서로 다른 4가지 데이터베이스가 있습니다. 데이터베이스 중 하나에서 수행되는 쿼리도 하나 또는 전체의 다른 데이터베이스에 대한 액세스가 필요합니다. 탄력적 쿼리를 사용하면 몇 가지 간단한 DDL 문을 4개의 데이터베이스 각각에서 실행하여 이 케이스에 대한 데이터베이스를 구성할 수 있습니다. 이렇게 한 번 구성한 후에는 원격 테이블 액세스가 T-SQL 쿼리나 BI 도구에서 로컬 테이블을 참조하는 것처럼 간단해집니다. 원격 쿼리에서 반환되는 결과가 크지 않은 경우에는 이 방법이 권장됩니다.

**그림 3** 수직 분할 - 탄력적 쿼리를 사용하여 여러 데이터베이스 쿼리

![수직 분할 - 탄력적 쿼리를 사용하여 여러 데이터베이스 쿼리][4]

다음 단계에서는 동일한 스키마를 사용하여 SQL Database의 원격 SQL 데이터베이스에 있는 테이블에 대한 액세스가 필요한 수직 분할 시나리오에 탄력적 데이터베이스 쿼리를 구성합니다.

* [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* [CREATE/DROP EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource of type **RDBMS**
* [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) mytable

DDL 문을 실행한 후에는 원격 테이블인 "mytable"에 로컬 테이블처럼 액세스할 수 있습니다. Azure SQL Database는 자동으로 원격 데이터베이스에 대한 연결을 실행하고, 원격 데이터베이스에서 요청을 처리하며, 결과를 반환합니다.

## <a name="horizontal-partitioning---sharding"></a>행 분할 - 분할

탄력적 쿼리를 사용하여 분할된, 즉 수평으로 분할된 데이터 계층에서 보고 작업을 수행하려면 [탄력적 데이터베이스 분할 맵](elastic-scale-shard-map-management.md)이 데이터 계층의 데이터베이스를 나타내야 합니다. 일반적으로 이 시나리오에서는 하나의 분할 된 데이터베이스 맵은 시나리오에서 사용되고 보고 쿼리에 대한 진입점으로 탄력적 쿼리 기능(헤드 노드)을 사용하여 전용된 데이터베이스 역할을 합니다. 이 전용 데이터베이스만 분할 맵 액세스가 필요합니다. 그림 4에서는 이 토폴로지와 탄력적 쿼리 데이터베이스 및 분할 맵 사용하여 해당 구성을 보여줍니다. 탄력적 데이터베이스 클라이언트 라이브러리 및 분할 맵 만들기에 대한 자세한 내용은 [분할 맵 관리](elastic-scale-shard-map-management.md)를 참조하세요.

**그림 4** 행 분할 - 탄력적 쿼리를 사용하여 분할된 데이터 계층에 대해 보고

![행 분할 - 탄력적 쿼리를 사용하여 분할된 데이터 계층에 대해 보고][5]

> [!NOTE]
> 탄력적 쿼리 데이터베이스(헤드 노드)는 별도 데이터베이스이거나 분할 맵을 호스팅하는 동일한 데이터베이스일 수 있습니다.
> 어떤 구성을 선택하든 해당 데이터베이스의 서비스 계층 및 컴퓨팅 크기에서 필요한 로그인/쿼리 요청의 양을 처리할 수 있을 만큼 충분한지 확인합니다.

다음 단계에서는 (보통) SQL Database의 여러 개의 원격 SQL 데이터베이스에 있는 테이블 세트에 액세스가 필요한 행 분할 시나리오에 대해 탄력적 데이터베이스 쿼리를 구성합니다.

* [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* 탄력적 데이터베이스 클라이언트 라이브러리를 사용하여 데이터 계층을 나타내는 [분할 맵](elastic-scale-shard-map-management.md) 을 만듭니다.
* [CREATE/DROP EXTERNA DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)**SHARD_MAP_MANAGER** 형식의 mydatasource
* [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) mytable

이 단계를 수행하고 나면 행 분할 테이블인 "mytable"에 로컬 테이블처럼 액세스할 수 있습니다. Azure SQL Database는 테이블이 물리적으로 저장된 원격 데이터베이스에 대해 여러 병렬 연결을 자동으로 열고, 원격 데이터베이스에서 요청을 처리하며, 결과를 반환합니다.
행 분할 시나리오에 필요한 단계에 대한 자세한 내용은 [행 분할에 대한 탄력적 쿼리](elastic-query-horizontal-partitioning.md)에서 제공합니다.

코딩을 시작하려면 [행 분할(분할)을 위한 탄력적 데이터베이스 쿼리 시작하기](elastic-query-getting-started.md)를 참조하세요.

> [!IMPORTANT]
> 큰 데이터베이스 세트에 대한 탄력적 쿼리의 실행 성공 여부는 쿼리를 실행하는 동안 각 데이터베이스의 가용성에 크게 좌우됩니다. 데이터베이스 중 하나를 사용할 수 없으면 전체 쿼리가 실패합니다. 수백 또는 수천 개의 데이터베이스를 한 번에 쿼리하려는 경우 클라이언트 애플리케이션에 다시 시도 논리가 포함되어 있는지 확인하거나, [탄력적 데이터베이스 작업(미리 보기)](./job-automation-overview.md)을 활용하여 데이터베이스의 더 작은 하위 집합을 쿼리하고 각 쿼리의 결과를 단일 대상으로 통합하는 것이 좋습니다.

## <a name="t-sql-querying"></a>T-SQL 쿼리

외부 데이터 원본과 외부 테이블을 정의한 후에는 일반 SQL Server 연결 문자열을 사용하여 외부 테이블을 정의한 데이터베이스에 연결할 수 있습니다. 이 연결을 통해 외부 테이블에서 T-SQL 문을 실행할 수 있는데, 여기에는 다음과 같은 제한 사항이 있습니다. [수평 분할](elastic-query-horizontal-partitioning.md) 및 [수직 분할](elastic-query-vertical-partitioning.md)에 대한 설명서 항목에서 T-SQL 쿼리의 자세한 내용 및 예제를 찾을 수 있습니다.

## <a name="connectivity-for-tools"></a>도구에 대한 연결

일반 SQL Server 연결 문자열을 사용 하여 애플리케이션과 BI를 연결하거나, 데이터 통합 도구를 외부 테이블이 있는 데이터베이스에 연결합니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 연결된 후에는 도구로 연결하는 타 SQL Server 데이터베이스에서처럼 탄력적 쿼리 데이터베이스와 이 데이터베이스의 외부 테이블을 참조합니다.

> [!IMPORTANT]
> 탄력적 쿼리를 통해 Azure Active Directory를 사용한 인증은 현재 지원되지 않습니다.

## <a name="cost"></a>Cost

탄력적 쿼리는 Azure SQL Database 비용 안에 포함됩니다. 원격 데이터베이스가 탄력적 쿼리 엔드포인트와 다른 데이터 센터에 있는 토폴로지를 지원하나, 원격 데이터베이스에서 데이터 송신에는 일반 [Azure 요금](https://azure.microsoft.com/pricing/details/data-transfers/)이 부과됩니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 더 작은 리소스와 표준 및 범용 서비스 계층에서 첫 번째 탄력적 쿼리를 실행하는 데 최대 몇 분이 걸릴 수 있습니다. 이 시간은 탄력적 쿼리 기능을 로드하는 데 필요하며, 더 높은 서비스 계층 및 컴퓨팅 크기로 인해 로드 성능이 향상됩니다.
* 외부 데이터 원본이나, SSMS 또는 SSDT에서의 외부 테이블 스크립팅은 아직 지원되지 않습니다.
* SQL Database에 대한 Import/Export는 외부 데이터 원본 및 외부 테이블을 아직 지원하지 않습니다. Import/Export를 사용해야 하는 경우 내보내기 전에 이러한 개체를 삭제하고 가져온 후 다시 만듭니다.
* 탄력적 쿼리는 현재 외부 테이블에 대한 읽기 전용 액세스만 지원합니다. 그러나 외부 테이블이 정의된 데이터베이스에서 전체 T-SQL 기능을 사용할 수 있습니다. 예를 들어, SELECT <column_list> INTO <local_table>을 사용하여 나온 일시적 결과를 유지하거나 외부 테이블을 참조하는 탄력적 쿼리 데이터베이스의 저장 프로시저를 정의하는 데 이 기능이 유용합니다.
* nvarchar(max)를 제외하고 LOB 형식(공간 형식 포함)은 외부 테이블 정의에서 지원되지 않습니다. 차선책으로, LOB 형식을 nvarchar(max)로 캐스팅하고, 기준 테이블 대신 뷰에서 외부 테이블을 정의한 다음 다시 쿼리에서 원래의 LOB 형식으로 캐스팅하는 원격 데이터베이스에서 뷰를 만들 수 있습니다.
* 결과 집합에서 nvarchar(max) 데이터 형식의 열은 탄력적 쿼리 구현에 사용된 고급 일괄 처리 기술을 사용하지 않고, 집계되지 않은 엄청난 양의 데이터가 쿼리 결과로 전송되는 비규범적 사용 사례에서 자릿수, 심지어 두 자릿수에 대한 쿼리 성능에 영향을 줄 수 있습니다.
* 외부 테이블에 대한 열 통계는 현재 지원되지 않습니다. 테이블 통계는 지원되지만 수동으로 만들어야 합니다.
* 탄력적 쿼리는 Azure SQL Database에서만 작동합니다. SQL Server 인스턴스를 쿼리하는 데 사용할 수 없습니다.

## <a name="share-your-feedback"></a>피드백 공유

아래 MSDN 포럼 또는 Stack Overflow에서 탄력적 쿼리에 대한 경험과 의견을 나눌 수 있습니다. 모든 종류의 서비스에 대한 사용자 의견에 관심이 있습니다.(결함, 조잡함, 기능의 격차)

## <a name="next-steps"></a>다음 단계

* 수직 분할 자습서는 [데이터베이스 간 쿼리 시작(수직 분할)](elastic-query-getting-started-vertical.md)을 참조하세요.
* 수직 분할된 데이터에 대한 구문 및 예제 쿼리는 [수직 분할된 데이터 쿼리하기](elastic-query-vertical-partitioning.md)를 참조하세요.
* 행 분할(분할) 자습서는 [행 분할(분할)을 위한 탄력적 데이터베이스 쿼리 시작하기](elastic-query-getting-started.md)를 참조하세요.
* 행 분할된 데이터에 대한 구문 및 예제 쿼리는 [행 분할된 데이터 쿼리하기](elastic-query-horizontal-partitioning.md)를 참조하세요.
* 단일 원격 Azure SQL Database 또는 수평 분할 구성표의 분할을 제공하는 데이터베이스 세트에서 Transact-SQL 문을 실행하는 저장된 프로시저는 [sp\_실행 \_원격](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database)을 참조하세요.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
