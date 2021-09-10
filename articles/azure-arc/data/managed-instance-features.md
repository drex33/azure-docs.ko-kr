---
title: Azure Arc 지원 SQL Managed Instance의 특징 및 기능
description: Azure Arc 지원 SQL Managed Instance의 특징 및 기능
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: ef855102f4877d26c1b6d16d73e99719e3e97ed1
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567470"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance의 특징 및 기능

Azure Arc 지원 SQL Managed Instance는 안정적인 최신 버전의 SQL Server를 사용하여 공통 코드 베이스를 공유합니다. 대부분의 표준 SQL 언어, 쿼리 처리, 데이터베이스 관리 기능은 동일합니다. SQL Server와 SQL Database 또는 SQL Managed Instance 간의 일반적인 기능은 다음과 같습니다.

- 언어 기능 - [흐름 언어 키워드](/sql/t-sql/language-elements/control-of-flow), [커서](/sql/t-sql/language-elements/cursors-transact-sql), [데이터 형식](/sql/t-sql/data-types/data-types-transact-sql), [DML 문](/sql/t-sql/queries/queries), [조건자](/sql/t-sql/queries/predicates), [시퀀스 번호](/sql/relational-databases/sequence-numbers/sequence-numbers), [저장 프로시저](/sql/relational-databases/stored-procedures/stored-procedures-database-engine), [변수](/sql/t-sql/language-elements/variables-transact-sql) 컨트롤
- 데이터베이스 기능 - [자동 조정(계획 강제 적용)](/sql/relational-databases/automatic-tuning/automatic-tuning), [변경 내용 추적](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [데이터베이스 데이터 정렬](/sql/relational-databases/collations/set-or-change-the-database-collation), [포함된 데이터베이스](/sql/relational-databases/databases/contained-databases), [포함된 사용자](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [데이터 압축](/sql/relational-databases/data-compression/data-compression), [데이터베이스 구성 설정](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [온라인 인덱스 작업](/sql/relational-databases/indexes/perform-index-operations-online), [분할](/sql/relational-databases/partitions/partitioned-tables-and-indexes), [temporal 테이블](/sql/relational-databases/tables/temporal-tables)([시작 가이드 참조](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables))
- 보안 기능 - [애플리케이션 역할](/sql/relational-databases/security/authentication-access/application-roles), [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)([Azure Portal에서 SQL Database 동적 데이터 마스킹 시작](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [행 수준 보안](/sql/relational-databases/security/row-level-security)
- 다중 모델 기능 - [Graph 처리](/sql/relational-databases/graphs/sql-graph-overview), [JSON 데이터](/sql/relational-databases/json/json-data-sql-server), [OPENXML](/sql/t-sql/functions/openxml-transact-sql), [공간](/sql/relational-databases/spatial/spatial-data-sql-server), [OPENJSON](/sql/t-sql/functions/openjson-transact-sql), [XML 인덱스](/sql/t-sql/statements/create-xml-index-transact-sql).


## <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS High Availability  
  
|기능|Azure Arc 지원 SQL Managed Instance|
|-------------|----------------|
|Always On 장애 조치(failover) 클러스터 인스턴스<sup>1</sup>| 해당 사항 없음. 비슷한 기능 사용 가능|
|Always On 가용성 그룹<sup>2</sup>|중요 비즈니스 서비스 계층 미리 보기 상태.|
|기본 가용성 그룹 <sup>2</sup>|해당 사항 없음. 비슷한 기능 사용 가능|
|최소 복제본 커밋 가용성 그룹<sup>2</sup>|중요 비즈니스 서비스 계층 미리 보기 상태.|
|클러스터가 없는 가용성 그룹|예|
|데이터베이스 백업 | 예 - `COPY_ONLY` [백업 - (Transact-SQL)](/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current&preserve-view=true) 참조|
|백업 압축|예|
|백업 미러 |예|
|백업 암호화|예|
|Azure에 백업 - (URL에 백업)|예|
|데이터베이스 스냅샷|예|
|빠른 복구|예|
|Hot Add 메모리 및 CPU|예|
|로그 전달|아직 사용할 수 없습니다.|
|온라인 페이지 및 파일 복원|예|
|온라인 인덱싱|예|
|온라인 스키마 변경|예|
|다시 시작 가능한 온라인 인덱스 다시 작성|예|

<sup>1</sup> Pod가 실패한 시나리오에서 새 SQL Managed Instance가 시작되고 데이터가 포함된 영구 볼륨에 다시 연결됩니다. [여기에서 Kubernetes 영구 볼륨에 대해 자세한 정보를 알아봅니다](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

## <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS Scalability and Performance  

| 기능 | Azure Arc 지원 SQL Managed Instance |
|--|--|
| columnstore | 예 |
| 클러스터형 columnstore 인덱스의 큰 개체 이진 파일 | 예 |
| 온라인 비클러스터형 columnstore 인덱스 다시 작성 | 예 |
| 메모리 내 OLTP | 예 |
| 영구 주 메모리 | 예 |
| 테이블 및 인덱스 분할 | 예 |
| 데이터 압축 | 예 |
| 관리 | 예 |
| 분할된 테이블 병렬 처리 | 예 |
| NUMA 인식 및 큰 페이지 메모리 및 버퍼 배열 할당 | 예 |
| IO 리소스 관리 | 예 |
| 지연된 내구성 | 예 |
| 자동 튜닝 | 예 |
| 일괄 처리 모드 적응 조인 | 예 |
| 일괄 처리 모드 메모리 부여 피드백 | 예 |
| 다중 문 테이블 반환 함수에 대한 인터리브 실행 | 예 |
| 대량 삽입 기능 개선 | 예 |

## <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS Security

| 기능 | Azure Arc 지원 SQL Managed Instance |
|--|--|
| 행 수준 보안 | 예 |
| Always Encrypted | 예 |
| 보안 Enclave를 사용한 Always Encrypted | 아니요 |
| 동적 데이터 마스킹 | 예 |
| 기본 감사 | 예 |
| 미세 감사 | 예 |
| 투명한 데이터베이스 암호화 | 예 |
| 사용자 정의 역할 | 예 |
| 포함된 데이터베이스 | 예 |
| 백업을 위한 암호화 | 예 |
| SQL Server 인증 | 예 |
| Azure Active Directory 인증 | No |
| Windows 인증 | No |

## <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS Manageability  

| 기능 | Azure Arc 지원 SQL Managed Instance |
|--|--|
| 관리자 전용 연결 | 예 |
| PowerShell 스크립팅 지원 | 예 |
| 데이터 계층 애플리케이션 구성 요소 작업 지원 - 추출, 배포, 업그레이드, 삭제 | 예 |
| 정책 자동화(일정 및 변경 내용 검사) | 예 |
| 성능 데이터 수집기 | 예 |
| 표준 성능 보고서 | 예 |
| 계획 지침을 위한 계획 지침 및 계획 고정 | 예 |
| 인덱스 뷰의 직접 쿼리(NOEXPAND 힌트 사용) | 예 |
| 인덱싱된 뷰의 자동 유지 관리 | 예 |
| 분산형 분할 뷰 | 예 |
| 병렬 인덱스 작업 | 예 |
| 쿼리 최적화 프로그램의 인덱싱된 뷰 자동 사용 | 예 |
| 병렬 일관성 검사 | 예 |

### <a name="programmability"></a><a name="Programmability"></a> Programmability  

| 기능 | Azure Arc 지원 SQL Managed Instance |
|--|--|
| JSON | 예 |
| 쿼리 저장소 | 예 | 
| 임시 테이블 | 예 | 
| 네이티브 XML 지원 | 예 | 
| XML 인덱싱 | 예 | 
| MERGE 및 UPSERT 기능 | 예 | 
| 날짜 및 시간 데이터 형식 | 예 | 
| 국제화 지원 | 예 | 
| 전체 텍스트 및 의미 체계 검색 | 아니요 |
| 쿼리에서 언어 지정 | 예 | 
| Service Broker(메시징) | 예 | 
| Transact-SQL 엔드포인트 | 예 | 
| 그래프 | 예 | 
| Machine Learning Services | 아니요 |
| PolyBase | 아니요 |


### <a name="tools"></a>도구

Azure Arc 지원 SQL Managed Instance는 데이터를 관리하는 데 도움이 되는 다양한 데이터 도구를 지원합니다.

| **도구** | Azure Arc 지원 SQL Managed Instance|
| --- | --- | --- |
| Azure Portal <sup>1</sup> | 아니요 |
| Azure CLI | 예 |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | 예 |
| Azure PowerShell | No |
| [BACPAC 파일(내보내기)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 예 |
| [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 예 |
| [SSDT(SQL Server Data Tools)](/sql/ssdt/download-sql-server-data-tools-ssdt) | 예 |
| [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) | 예 |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | 예 |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | 예 |

<sup>1</sup> Azure Portal은 Azure Arc 지원 SQL Managed Instance를 만들고, 확인하고, 삭제하는 데 사용할 수 있습니다.  현재 Azure Portal를 통해 업데이트를 수행할 수 없습니다.

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

### <a name="unsupported-features--services"></a><a name="Unsupported"></a> 지원되지 않는 기능 및 서비스

Azure Arc 지원 SQL Managed Instance에는 다음 기능 및 서비스를 사용할 수 없습니다. 이 기능에 대한 지원은 시간이 지나면서 점점 더 활성화됩니다.

| 영역 | 지원되지 않는 기능 또는 서비스 |
|-----|-----|
| **데이터베이스 엔진** | 병합 복제 |
| &nbsp; | Stretch DB |
| &nbsp; | 타사 연결을 사용하는 분산 쿼리 |
| &nbsp; | SQL Server 및 Azure SQL 제품이 아닌 데이터 원본에 연결된 서버 |
| &nbsp; | 시스템 확장 저장 프로시저(XP_CMDSHELL 등) |
| &nbsp; | Filetable, 파일 스트림 |
| &nbsp; | EXTERNAL_ACCESS 또는 UNSAFE 권한 세트가 있는 CLR 어셈블리 |
| &nbsp; | Buffer Pool Extension |
| **SQL Server 에이전트** |  하위 시스템: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | 경고 |
| &nbsp; | 관리되는 백업 |
| **고가용성** | 데이터베이스 미러링  |
| **보안** | 확장 가능 키 관리 |
| &nbsp; | 연결된 서버의 AD 인증 | 
| &nbsp; | AG(가용성 그룹)의 AD 인증 | 
