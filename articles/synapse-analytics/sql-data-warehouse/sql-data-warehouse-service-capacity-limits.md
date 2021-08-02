---
title: 전용 SQL 풀의 용량 제한
description: Azure Synapse Analytics에서 전용 SQL 풀의 다양한 구성 요소에 허용되는 최대값입니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d915577cd6729ead60a00250e186fb2df444b3b1
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112029174"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀의 용량 한도

Azure Synapse Analytics에서 전용 SQL 풀의 다양한 구성 요소에 허용되는 최대값입니다.

## <a name="workload-management"></a>워크로드 관리

| 범주 | 설명 | 최대 |
|:--- |:--- |:--- |
| [DWU(데이터 웨어하우스 단위)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |단일 전용 SQL 풀의 최대 DWU  | Gen1: DW6000<br></br>Gen2: DW30000c |
| [DWU(데이터 웨어하우스 단위)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |서버당 기본 DTU |54,000<br></br>기본적으로 각 SQL Server(예: myserver.database.windows.net)에는 DTU 할당량인 54,000이 있으며 최대 DW6000c가 허용됩니다. 이 할당량은 안전을 위한 제한일 뿐입니다. [지원 티켓을 만들고](sql-data-warehouse-get-started-create-support-ticket.md)*할당량* 을 요청 형식으로 선택하여 할당량을 늘릴 수 있습니다.  DTU 요구 사항을 계산하려면 7.5를 필요한 총 DWU로 곱하거나 9를 필요한 총 cDWU로 곱합니다. 예를 들면 다음과 같습니다.<br></br>DW6000 x 7.5 = 45,000DTU<br></br>DW7500c x 9 = 67,500DTU.<br></br>포털의 SQL Server 옵션에서 현재 DTU 사용량을 볼 수 있습니다. 일시 중지되거나 일시 중지되지 않은 데이터베이스는 모두 DTU 할당량에 포함됩니다. |
| 데이터베이스 연결 |열린 최대 동시 세션 수 |1024<br/><br/>동시에 열린 세션 수는 선택한 DWU에 따라 달라집니다. DWU1000c 이상에서는 최대 1024개의 열린 세션을 지원합니다. DWU500c 이하에서는 최대 512개의 동시에 열린 세션 한도를 지원합니다. 동시에 실행할 수 있는 쿼리 수에 제한이 있습니다. 동시성 제한을 초과하는 경우 요청이 처리될 때까지 대기하는 내부 큐로 이동합니다. |
| 데이터베이스 연결 |준비된 문에 대한 최대 메모리 |20MB |
| [워크로드 관리](resource-classes-for-workload-management.md) |최대 동시 쿼리 수 |128<br/><br/>  최대 128개의 동시 쿼리가 실행되고 남은 쿼리가 큐에 대기됩니다.<br/><br/>사용자에게 더 높은 수의 리소스 클래스가 할당되거나 [데이터 웨어하우스 단위](memory-concurrency-limits.md) 설정이 낮아지면 동시 쿼리 수가 감소될 수 있습니다. DMV 쿼리와 같은 일부 쿼리는 항상 실행하도록 허용돼도 동시 쿼리 제한에 영향을 주지 않습니다. 동시 쿼리 실행에 관한 자세한 내용은 [동시성 최대값](memory-concurrency-limits.md) 문서를 참조합니다. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |최대 GB |DW100c당 399GB. DWU1000c에서 tempdb의 크기는 3.99TB로 조정됩니다. |
||||

## <a name="database-objects"></a>데이터베이스 개체

| 범주 | 설명 | 최대 |
|:--- |:--- |:--- |
| 데이터베이스 |최대 크기 | Gen1: 디스크에서 압축된 240TB 이 공간은 tempdb 또는 로그 공간과 독립적이므로 영구 테이블에만 사용됩니다.  클러스터형 columnstore의 압축에 따른 예상 크기 증가 비율은 5배입니다.  즉, 모든 테이블이 클러스터형 columnstore(기본 테이블 유형)일 때 이러한 압축을 통해 데이터베이스를 약 1PB로 확장할 수 있습니다. <br/><br/> Gen2: columnstore 테이블의 무제한 스토리지입니다.  데이터베이스의 Rowstore 부분은 여전히 디스크에서 압축된 240TB로 제한됩니다. |
| 테이블 |최대 크기 |columnstore 테이블의 크기가 무제한입니다. <br>디스크에 압축된 rowstore 테이블의 경우 60TB |
| 테이블 |데이터베이스당 테이블 수 | 100,000 |
| 테이블 |테이블당 열 수 |1024열 |
| 테이블 |열 당 바이트 |열 [데이터 형식](sql-data-warehouse-tables-data-types.md)에 따라 다릅니다. char 데이터 형식의 경우 8,000자, nvarchar의 경우 4,000자, MAX 데이터 형식의 경우 2GB로 제한됩니다. |
| 테이블 |행 당 바이트, 정의된 크기 |8,060바이트<br/><br/>행당 바이트 수는 페이지 압축이 설정된 SQL Server에 대한 방법과 동일하게 계산됩니다. SQL Server와 마찬가지로, **가변 길이 열** 을 행 외부로 밀수 있게 하는 행 오버플로 스토리지가 지원됩니다. 가변 길이 행을 행 외부로 밀어 넣으면 주 레코드에는 24바이트 루트만 저장됩니다. 자세한 내용은 [8KB를 초과하는 행 오버플로 데이터](/previous-versions/sql/sql-server-2008-r2/ms186981(v=sql.105))를 참조하세요. |
| 테이블 |테이블 당 파티션 |15,000<br/><br/>높은 성능을 위해서는 계속해서 비즈니스 요구사항을 지원하면서 파티션 수를 줄이는 것이 좋습니다. 파티션 수가 늘어나면 DDL(데이터 정의 언어) 및 DML(데이터 조작 언어) 작업에 대한 오버헤드가 증가하고 성능이 저하됩니다. |
| 테이블 |파티션 경계 값 당 문자. |4000 |
| 인덱스 |테이블 당 비클러스터형 인덱스. |50<br/><br/>rowstore 테이블에만 적용됩니다. |
| 인덱스 |테이블 당 클러스터형 인덱스. |1<br><br/>rowstore 및 columnstore 테이블 모두에 적용됩니다. |
| 인덱스 |인덱스 키 크기. |900바이트.<br/><br/>rowstore 인덱스에만 적용됩니다.<br/><br/>인덱스를 만들 때 열에 있는 기존 데이터가 900바이트를 초과하지 않는 경우 최대 크기가 900바이트 보다 큰 varchar 열에 인덱스를 만들 수 있습니다. 그러나 나중에 전체 크기가 900바이트를 초과하는 열에서 삽입 또는 업데이트 동작이 실패합니다. |
| 인덱스 |인덱스 당 키 열. |16<br/><br/>rowstore 인덱스에만 적용됩니다. 클러스터형 columnstore 인덱스는 모든 열을 포함합니다. |
| 통계 |결합된 열 값의 크기. |900바이트. |
| 통계 |통계 개체 당 열. |32 |
| 통계 |테이블 당 열에 만든 통계. |30,000 |
| 저장 프로시저 |최대 수준의 중첩. |8 |
| 보기 |뷰당 열 수 |1,024 |
||||

## <a name="loads"></a>부하

| 범주 | 설명 | 최대 |
|:--- |:--- |:--- |
| Polybase 로드 |행당 MB |1<br/><br/>Polybase는 1MB보다 작은 행을 로드합니다. CCI(클러스터형 Columnstore 인덱스)를 사용하여 테이블에 LOB 데이터 형식을 로드하는 것은 지원되지 않습니다.<br/> |
|Polybase 로드|총 파일 수|1,000,000<br/><br/>Polybase 로드는 1M보다 많은 파일을 초과할 수 없습니다. **분할 수가 상한 1000000을 초과하여 작업에 실패함** 오류가 발생할 수 있습니다.|

## <a name="queries"></a>쿼리

| 범주 | 설명 | 최대 |
|:--- |:--- |:--- |
| 쿼리 |사용자 테이블에서 쿼리된 쿼리입니다. |1000 |
| 쿼리 |시스템 뷰에서 동시 쿼리입니다. |100 |
| 쿼리 |시스템 뷰에서 쿼리된 쿼리입니다. |1000 |
| 쿼리 |최대 매개 변수 |2098 |
| Batch |최대 크기 |65,536*4096 |
| 결과 선택 |행 당 열 |4096<br/><br/>결과에는 행마다 4096개 이상의 열이 있어서는 안 됩니다. 항상 4096이 있다고 보장할 수 없습니다. 쿼리 계획에 임시 테이블이 필요한 경우 테이블 당 최대 1024 열이 적용될 수 있습니다. |
| SELECT |중첩 하위 쿼리 수 |32<br/><br/>SELECT 문에는 32개 보다 많은 중첩된 하위 쿼리가 있어서는 안 됩니다. 항상 32가 있다고 보장할 수 없습니다. 예를 들어 조인은 쿼리 계획에 하위 쿼리를 제공할 수 있습니다. 또한 사용 가능한 메모리에서 하위 쿼리의 수를 제한할 수 있습니다. |
| SELECT |조인 당 열 |1024열<br/><br/>조인에는 1024개 이상의 열이 있어서는 안 됩니다. 항상 1024가 있다고 보장할 수 없습니다. 조인 계획에 조인 결과보다 많은 열을 가진 임시 테이블이 필요한 경우 1024 제한은 임시 테이블에 적용됩니다. |
| SELECT |그룹화 기준 열 당 바이트. |8060<br/><br/>GROUP BY 절의 열은 최대 8060바이트를 포함할 수 있습니다. |
| SELECT |정렬 기준 열 당 바이트 |8,060바이트<br/><br/>ORDER BY 절의 열은 최대 8060바이트를 포함할 수 있습니다. |
| 명령문당 식별자 |참조된 식별자의 수 |65,535<br/><br/> 쿼리의 단일 수식에 포함될 수 있는 식별자의 수를 제한합니다. 이 숫자를 초과하면 SQL Server 오류 8632가 발생합니다. 자세한 내용은 [내부 오류: 식 서비스 제한에 도달했습니다.](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)를 참조하세요. |
| 문자열 리터럴 | 명령문의 문자열 리터럴 수 | 20,000 <br/><br/>S쿼리의 단일 수식에서 문자열 상수의 수를 제한합니다. 이 숫자를 초과하면 SQL Server 오류 8632가 발생합니다.|
||||

## <a name="metadata"></a>메타데이터

DMV는 전용 SQL 풀이 일시 중지되거나 스케일링될 때 재설정됩니다.

| 시스템 뷰 | 최대 행 수 |
|:--- |:--- |
| [sys.dm_pdw_dms_cores](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-cores-transact-sql?view=azure-sqldw-latest&preserve-view=true) |100 |
| [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true) |가장 최근 1000 SQL 요청에 대한 DMS 작업자의 수. |
| [sys.dm_pdw_errors](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-errors-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10000 |
| [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10000 |
| [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10000 |
| [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true) |sys.dm_pdw_exec_requests에 저장된 가장 최근 1000 SQL 요청에 대한 단계의 총 수입니다. |
| [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |sys.dm_pdw_exec_requests에 저장된 가장 최근 1000 SQL 요청입니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure Synapse 사용에 관한 권장 사항은 [치트 시트](cheat-sheet.md)를 참조하세요.