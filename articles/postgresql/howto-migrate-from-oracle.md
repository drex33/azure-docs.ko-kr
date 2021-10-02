---
title: 'Oracle을 Azure Database for PostgreSQL로: 마이그레이션 가이드'
titleSuffix: Azure Database for PostgreSQL
description: 이 가이드는 Oracle 스키마를 Azure Database for PostgreSQL로 마이그레이션하는 데 도움이 됩니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 758199dca165c301322631e0d29b1af2711dc56b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356938"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle을 Azure Database for PostgreSQL로 마이그레이션

이 가이드는 Oracle 스키마를 Azure Database for PostgreSQL로 마이그레이션하는 데 도움이 됩니다. 

자세하고 포괄적인 마이그레이션 지침은 [마이그레이션 가이드 리소스](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

Oracle 스키마를 Azure Database for PostgreSQL로 마이그레이션하려면 다음을 수행해야 합니다. 

- 원본 환경이 지원되는지 확인합니다. 
- 최신 버전의 [ora2pg](https://ora2pg.darold.net/)를 다운로드합니다. 
- 최신 버전의 [ 모듈](https://www.cpan.org/modules/by-module/DBD/)을 포함합니다. 


## <a name="overview"></a>개요

PostgreSQL은 세계 최고 수준의 고급 오픈 소스 데이터베이스 중 하나입니다. 이 문서에서는 무료 ora2pg 도구를 사용하여 Oracle 데이터베이스를 PostgreSQL로 마이그레이션하는 방법을 설명합니다. Ora2pg를 사용하여 Oracle 데이터베이스 또는 MySQL 데이터베이스를 PostgreSQL 호환 스키마로 마이그레이션할 수 있습니다. 

Ora2pg 도구는 Oracle 데이터베이스를 연결하고, 자동으로 검색하고, 해당 구조 또는 데이터를 추출합니다. 그런 다음 ora2pg는 PostgreSQL 데이터베이스에 로드할 수 있는 SQL 스크립트를 생성합니다. Ora2pg를 사용하여 Oracle 데이터베이스를 리버스 엔지니어링하거나, 방대한 엔터프라이즈 데이터베이스를 마이그레이션하거나, 단순히 일부 Oracle 데이터를 PostgreSQL 데이터베이스로 복제할 수 있습니다. 이 도구는 사용하기 쉬우며 Oracle 데이터베이스에 연결하는 데 필요한 매개 변수를 제공하는 기능 외에는 Oracle 데이터베이스 지식이 필요하지 않습니다.

> [!NOTE]
> 최신 버전의 ora2pg를 사용하는 방법에 대한 자세한 내용은 [ora2pg 설명서](https://ora2pg.darold.net/documentation.html)를 참조하세요.

### <a name="typical-ora2pg-migration-architecture"></a>일반적인 ora2pg 마이그레이션 아키텍처

![Ora2pg 마이그레이션 아키텍처의 스크린샷](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

VM 및 Azure Database for PostgreSQL을 프로비전한 후에는 두 가지 구성인 **Azure 서비스에 대한 액세스 허용** 및 **SSL 연결 적용** 간에 연결을 사용하도록 설정해야 합니다. 

- **연결 보안** 블레이드 > **Azure Services에 대한 액세스 허용** > **켜기**

- **연결 보안** 블레이드 > **SSL 설정** > **SSL 연결 적용** > **사용 안 함**

### <a name="recommendations"></a>권장 사항

- Oracle 서버에서 평가 또는 내보내기 작업의 성능을 향상시키려면 통계를 수집합니다.

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- `INSERT` 대신 `COPY` 명령을 사용하여 데이터를 내보냅니다.

- FKS(외래 키), 제약 조건 및 인덱스를 사용하여 테이블을 내보내지 마세요. 이러한 요소는 PostgreSQL으로 데이터를 가져오는 프로세스의 속도를 저하시킵니다.

- *no data 절* 을 사용하여 구체화된 뷰를 만듭니다. 그런 다음 나중에 보기를 새로 고칩니다.

- 가능하면 구체화된 뷰에서 고유한 인덱스를 사용합니다. 이러한 인덱스는 `REFRESH MATERIALIZED VIEW CONCURRENTLY` 구문을 사용할 때 새로 고침 속도를 높일 수 있습니다.


## <a name="pre-migration"></a>사전 마이그레이션 

원본 환경이 지원되고 필수 구성 요소가 해결되면 마이그레이션 전 단계를 시작할 준비가 된 것입니다. 시작하려면 

1. **검색**: 마이그레이션해야 하는 데이터베이스의 인벤토리를 작성합니다. 
2. **평가**: 잠재적인 마이그레이션 문제 또는 차단에 대해 해당 데이터베이스를 평가합니다.
3. **변환**: 표시된 항목을 모두 해결합니다. 
 
Oracle에서 Azure Database for PostgreSQL로의 이기종 마이그레이션의 경우 이 단계에는 원본 데이터베이스 스키마를 대상 환경과 호환되도록 만드는 작업도 포함됩니다.

### <a name="discover"></a>검색

검색 단계의 목표는 기존 데이터 원본 및 사용 중인 기능에 대한 세부 정보를 식별하는 것입니다. 이 단계를 통해 마이그레이션을 더욱 잘 이해하고 계획할 수 있습니다. 프로세스에서는 네트워크를 검색하여 사용 중인 버전 및 기능과 함께 조직의 모든 Oracle 인스턴스를 파악합니다.

Oracle에 대한 Microsoft 사전 평가 스크립트는 Oracle 데이터베이스에 대해 실행됩니다. 이 사전 평가 스크립트는 Oracle 메타데이터를 쿼리합니다. 스크립트는 다음을 제공합니다.

- 스키마, 유형 및 상태별 개체 개수를 포함하는 데이터베이스 인벤토리
- 통계를 기반으로 한 각 스키마의 원시 데이터에 대한 대략적인 예상 값입니다.
- 각 스키마에 있는 테이블의 크기입니다.
- 패키지, 함수, 프로시저 등의 항목당 코드 줄 수입니다.

[github](https://github.com/microsoft/DataMigrationTeam/tree/master/Whitepapers)에서 관련 스크립트를 다운로드합니다.

### <a name="assess"></a>평가

Oracle 데이터베이스의 인벤토리를 작성한 후에는 데이터베이스 크기와 잠재적인 문제를 파악하게 됩니다. 다음 단계는 평가를 실행하는 것입니다.

Oracle에서 PostgreSQL로 마이그레이션하는 비용을 예측하는 것은 쉽지 않습니다. 마이그레이션 비용을 평가하기 위해 ora2pg는 모든 데이터베이스 개체, 함수 및 저장 프로시저에서 자동으로 변환할 수 없는 개체 및 PL/SQL 코드를 확인합니다.

Ora2pg 도구에는 Oracle 데이터베이스를 검사하여 텍스트 보고서를 생성하는 콘텐츠 분석 모드가 있습니다. 이 보고서에는 Oracle 데이터베이스에 포함된 항목과 내보낼 수 없는 항목이 설명되어 있습니다.

*분석 및 보고서* 모드를 활성화하려면 다음 명령에 표시된 것처럼 내보낸 유형 `SHOW_REPORT`를 사용합니다.

```
ora2pg -t SHOW_REPORT
```

Ora2pg 도구는 Oracle 구문에서 PostgreSQL로 SQL 및 PL/SQL 코드를 변환할 수 있습니다. 따라서 데이터베이스를 분석한 후 ora2pg는 코드 문제 및 전체 데이터베이스를 마이그레이션하는 데 필요한 시간을 예측할 수 있습니다.

ora2pg를 사용하면 사용자의 기간 마이그레이션 비용을 예측할 수 있으므로 `ESTIMATE_COST`이라는 구성 지시문을 사용할 수 있습니다. 명령 프롬프트에서 다음 지시문을 사용하도록 설정할 수도 있습니다.

```
ora2pg -t SHOW_REPORT --estimate_cost
```

기본 마이그레이션 단위는 PostgreSQL 전문가의 약 5분을 나타냅니다. 이 마이그레이션을 처음 사용하는 경우 구성 지시문 `COST_UNIT_VALUE` 또는 `--cost_unit_value` 명령줄 옵션을 사용하여 기본 마이그레이션 단위를 늘릴 수 있습니다.

보고서의 마지막 줄에는 사용자의 기간 총 예상 마이그레이션 코드가 표시됩니다. 예상은 각 개체에 대해 예상되는 마이그레이션 단위 수를 따릅니다.

다음 코드 예제에는 몇 가지 평가 변형이 나와 있습니다. 
* 테이블 평가
* 열 평가
* 5분의 기본 비용 단위를 사용하는 스키마 평가
* 10분의 비용 단위를 사용하는 스키마 평가

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt 
ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

다음은 스키마 평가 마이그레이션 수준 B-5의 출력입니다.

* 마이그레이션 수준:

  * A - 자동으로 실행될 수 있는 마이그레이션
    
  * B - 코드 재작성을 사용하여 마이그레이션 및 사용자 기간 비용(최대 5일)
    
  * C - 코드 재작성을 사용하여 마이그레이션 및 사용자 기간 비용(5일 이상)
    
* 기술 수준:

   * 1 = Trivial: 저장된 함수 및 트리거 없음

   * 2 = Easy: 저장된 함수는 없지만 트리거 있음, 수동 다시 쓰기 없음

   * 3 = Simple: 저장된 함수가 및/또는 트리거 있음, 수동 다시 쓰기 없음

   * 4 = Manual: 저장된 함수는 없지만 코드 다시 쓰기로 트리거 또는 보기

   * 5 = Difficult: 코드 다시 쓰기로 저장된 함수 및/또는 트리거

평가는 다음과 같이 구성되어 있습니다. 
* 마이그레이션에 수동 다시 쓰기가 필요한지 여부를 지정하는 문자(A 또는 B)

* 기술적인 문제를 나타내는 1에서 5 사이의 숫자 

또 다른 옵션인 `-human_days_limit`는 사용자 기간 제한을 지정합니다. 여기에서 마이그레이션 수준을 C로 설정하여 마이그레이션에 대량 작업, 전체 프로젝트 관리 및 마이그레이션 지원이 필요함을 나타냅니다. 기본값은 사용자 기간 10일입니다. 구성 지시문 `HUMAN_DAYS_LIMIT`를 사용하여 이 기본값을 영구적으로 변경할 수 있습니다.

이 스키마 평가는 사용자가 먼저 마이그레이션할 데이터베이스와 동원할 팀을 결정할 수 있도록 개발되었습니다.

### <a name="convert"></a>변환

최소 가동 중지 시간 마이그레이션 시 마이그레이션 원본이 변경됩니다. 일회성 마이그레이션 후 데이터 및 스키마를 기준으로 대상에서 드리프트합니다. *데이터 동기화* 단계 중에 원본에 있는 모든 변경 내용이 캡처되고 거의 실시간으로 대상에 적용되는지 확인합니다. 모든 변경 내용이 대상에 적용되었는지 확인한 후 원본에서 대상 환경으로 *전환* 할 수 있습니다.

이 마이그레이션 단계에서 Oracle 코드 및 DDL 스크립트는 PostgreSQL로 변환됩니다. Ora2pg 도구는 PostgreSQL 형식의 Oracle 개체를 자동으로 내보냅니다. 일부 생성된 개체는 수동으로 변경해야 PostgreSQL 데이터베이스에서 컴파일할 수 있습니다.  

수동 개입이 필요한 요소를 파악하려면 먼저 PostgreSQL 데이터베이스에 대해 ora2pg에서 생성한 파일을 컴파일하세요. 로그를 확인하고 스키마 구조가 PostgreSQL 구문과 호환될 때까지 필요한 변경을 수행합니다.


#### <a name="create-a-migration-template"></a>마이그레이션 템플릿 만들기 

Ora2pg에서 제공하는 마이그레이션 템플릿을 사용 하는 것이 좋습니다. `--project_base` 및 `--init_project` 옵션을 사용하는 경우 ora2pg는 Oracle 데이터베이스에서 모든 개체를 내보내는 작업 트리, 구성 파일 및 스크립트가 있는 프로젝트 템플릿을 만듭니다. 자세한 내용은 [ora2pg 설명서](https://ora2pg.darold.net/documentation.html)를 참조하세요.

다음 명령을 사용합니다. 

```
ora2pg --project_base /app/migration/ --init_project test_project
```

출력의 예제는 다음과 같습니다. 
   
```
ora2pg --project_base /app/migration/ --init_project test_project
        Creating project test_project.
        /app/migration/test_project/
                schema/
                        dblinks/
                        directories/
                        functions/
                        grants/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        sequences/
                        synonyms/
                        tables/
                        tablespaces/
                        triggers/
                        types/
                        views/
                sources/
                        functions/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        triggers/
                        types/
                        views/
                data/
                config/
                reports/

        Generating generic configuration file
        Creating script export_schema.sh to automate all exports.
        Creating script import_all.sh to automate all imports.
```

`sources/` 디렉터리에는 Oracle 코드가 포함되어 있습니다. `schema/` 디렉터리에는 PostgreSQL로 이식된 코드가 포함되어 있습니다. 또한 `reports/` 디렉터리에는 HTML 보고서 및 마이그레이션 비용 평가가 포함되어 있습니다.


프로젝트 구조를 만들면 일반 구성 파일이 만들어집니다. 구성 파일의 Oracle 데이터베이스 연결 및 관련 구성 매개 변수를 정의합니다. 구성 파일에 대한 자세한 내용은 [ora2pg 설명서](https://ora2pg.darold.net/documentation.html)를 참조하세요.


#### <a name="export-oracle-objects"></a>Oracle 개체 내보내기

그런 다음 *export_schema.sh* 파일을 실행하여 Oracle 개체를 PostgreSQL 개체로 내보냅니다.

```
cd /app/migration/mig_project
./export_schema.sh
```

다음 명령을 수동으로 실행합니다.

```
SET namespace="/app/migration/mig_project"

ora2pg -p -t DBLINK -o dblink.sql -b %namespace%/schema/dblinks -c %namespace%/config/ora2pg.conf
ora2pg -p -t DIRECTORY -o directory.sql -b %namespace%/schema/directories -c %namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c %namespace%/config/ora2pg.conf 
ora2pg -p -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf 
ora2pg -p -t MVIEW -o mview.sql -b %namespace%/schema/mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql -b %namespace%/schema/packages -c %namespace%/config/ora2pg.conf
ora2pg -p -t PARTITION -o partitions.sql -b %namespace%/schema/partitions -c %namespace%/config/ora2pg.conf
ora2pg -p -t PROCEDURE -o procs.sql -b %namespace%/schema/procedures -c %namespace%/config/ora2pg.conf
ora2pg -p -t SEQUENCE -o sequences.sql -b %namespace%/schema/sequences -c %namespace%/config/ora2pg.conf
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c %namespace%/config/ora2pg.conf
ora2pg -p -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c %namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf 
ora2pg -p -t VIEW -o views.sql -b %namespace%/schema/views -c %namespace%/config/ora2pg.conf
```

데이터를 추출하려면 다음 명령을 사용합니다.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>파일 컴파일

마지막으로 Azure Database for PostgreSQL 서버에 대한 모든 파일을 컴파일합니다. 수동으로 생성된 DDL 파일을 로드하도록 선택하거나 두 번째 스크립트인 *import_all.sh* 를 사용하여 해당 파일을 대화형으로 가져올 수 있습니다.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l %namespace%\schema\tables\create_table.log
```

데이터 가져오기 명령은 다음과 같습니다.

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

파일이 컴파일되는 동안 로그를 확인하고 ora2pg에서 자체적으로 변환할 수 없는 구문을 수정합니다.

자세한 내용은 [Oracle에서 Azure Database for PostgreSQL로의 마이그레이션 해결 방법](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf)을 참조하세요.

## <a name="migrate"></a>마이그레이션 

필요한 필수 구성 요소가 있고 마이그레이션 전 단계를 완료한 후에는 스키마 및 데이터 마이그레이션을 시작할 수 있습니다.

### <a name="migrate-schema-and-data"></a>스키마 및 데이터 마이그레이션

필요한 수정 작업을 수행하면 데이터베이스의 안정적인 빌드를 배포할 준비가 된 것입니다. 수정된 코드를 포함한 파일을 가리키는 `psql` 가져오기 명령을 실행합니다. 이 작업은 PostgreSQL 데이터베이스에 대해 데이터베이스 개체를 컴파일하고 데이터를 가져옵니다.

이 단계에서는 데이터를 가져올 때 병렬 처리 수준을 구현할 수 있습니다.

### <a name="sync-data-and-cut-over"></a>데이터 동기화 및 전환

온라인(최소 가동 중지 시간) 마이그레이션의 경우 마이그레이션 원본은 계속 변경됩니다. 일회성 마이그레이션 후 데이터 및 스키마를 기준으로 대상에서 드리프트합니다. 

*데이터 동기화* 단계 중에 원본에 있는 모든 변경 내용이 캡처되고 거의 실시간으로 대상에 적용되는지 확인합니다. 모든 변경 내용이 적용되었는지 확인한 후 원본에서 대상 환경으로 전환할 수 있습니다.

온라인 마이그레이션을 수행하려면 AskAzureDBforPostgreSQL@service.microsoft.com에 지원을 문의하세요.

Ora2pg를 사용하는 *델타/증분* 마이그레이션의 경우 각 테이블에 대해 날짜, 시간 또는 다른 매개 변수를 기준으로 필터링(*잘라내기*)하는 쿼리를 사용합니다. 그런 다음, 나머지 데이터를 마이그레이션하는 두 번째 쿼리를 사용하여 마이그레이션을 완료합니다.

원본 데이터 테이블에서 모든 기록 데이터를 먼저 마이그레이션합니다. 예를 들면 다음과 같습니다.

```
select * from table1 where filter_data < 01/01/2019
```

다음과 같은 명령을 실행하여 초기 마이그레이션 이후 변경 내용을 쿼리할 수 있습니다.

```
select * from table1 where filter_data >= 01/01/2019
```

이 경우 원본 및 대상 양쪽 모두에서 데이터 패리티를 확인하여 유효성 검사를 향상시키는 것이 좋습니다.

## <a name="post-migration"></a>마이그레이션 후 작업 

*마이그레이션* 단계를 완료한 후 모든 작업이 최대한 원활하고 효율적으로 작동하도록 하려면 마이그레이션 후 작업을 완료합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 설치 프로그램에서 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="test"></a>테스트

데이터를 대상으로 마이그레이션되면 데이터베이스에 테스트를 실행하여 애플리케이션이 대상과 잘 작동하는지 확인합니다. Oracle 원본 및 PostgreSQL 대상 데이터베이스에 대해 수동 데이터 유효성 검사 스크립트를 실행하여 원본 및 대상이 제대로 마이그레이션되었는지 확인합니다.

이상적으로는 원본 및 대상 데이터베이스에 네트워킹 경로가 있는 경우 데이터 유효성 검사에 ora2pg를 사용해야 합니다. `TEST` 작업을 수행하여 Oracle 데이터베이스의 모든 개체가 PostgreSQL에서 생성되었는지 확인할 수 있습니다. 

다음 명령을 실행합니다.

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>최적화

마이그레이션 후 작업 단계는 데이터 정확도 문제를 조정하고 완결성을 확인하는 데 중요합니다. 또한 이 단계에서는 워크로드와 관련된 성능 문제를 해결합니다.

## <a name="migration-assets"></a>마이그레이션 자산 

이 마이그레이션 시나리오에 대한 자세한 내용은 다음 리소스를 참조하세요. 해당 리소스는 실제 마이그레이션 프로젝트 참여를 지원합니다.

| 리소스 | Description    |
| -------------- | ------------------ |
| [Oracle에서 Azure PostgreSQL로 마이그레이션 쿡북](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | 이 문서는 설계자, 컨설턴트, 데이터베이스 관리자 및 관련 역할이 ora2pg를 사용하여 Oracle에서 Azure Database for PostgreSQL로 워크로드를 빠르게 마이그레이션하는 데 도움이 됩니다. |
| [Oracle에서 Azure PostgreSQL로 마이그레이션 쿡북](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | 이 문서를 통해 설계자, 컨설턴트, 데이터베이스 관리자 및 관련 역할은 Oracle에서 Azure Database for PostgreSQL로 워크로드를 마이그레이션하는 동안 문제를 신속하게 수정하거나 해결할 수 있습니다. |
| [Windows 또는 Linux에서 ora2pg를 설치하는 단계](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | 이 문서는 Windows 또는 Linux에서 ora2pg를 사용하여 Oracle에서 Azure Database for PostgreSQL로 스키마 및 데이터를 마이그레이션하기 위한 빠른 설치 가이드를 제공합니다. 자세한 내용은 [ora2pg 설명서](http://ora2pg.darold.net/documentation.html)를 참조하세요. |

데이터 SQL 엔지니어링 팀이 해당 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.

## <a name="more-support"></a>추가 지원

ora2pg 도구의 범위를 벗어난 마이그레이션 도움말은 [ @AskAzure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)을 참조하세요.

## <a name="next-steps"></a>다음 단계

데이터베이스 및 데이터 마이그레이션과 전문 작업을 위한 서비스 및 도구 매트릭스는 [데이터 마이그레이션을 위한 서비스 및 도구](../dms/dms-tools-matrix.md)를 참조하세요.

설명서: 
- [Azure Database for PostgreSQL 설명서](./index.yml)
- [ora2pg 설명서](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL 웹 사이트](https://www.postgresql.org/)
- [PostgreSQL의 자치 트랜잭션 지원](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
