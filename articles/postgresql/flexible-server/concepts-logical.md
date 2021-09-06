---
title: 논리 복제 및 논리 디코딩 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL - 유연한 서버에서 논리 복제 및 논리 디코딩을 사용하는 방법에 대해 알아봅니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: e23eaead0d9c8dd162d73176330f620a9ee8e737
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536588"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버에서 논리 복제 및 논리 디코딩

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

Azure Database for PostgreSQL - 유연한 서버는 다음과 같은 논리적 데이터 추출 및 복제 방법론을 지원합니다.
1. **논리적 복제**
   1. PostgreSQL [원시 논리 복제](https://www.postgresql.org/docs/12/logical-replication.html)를 사용하여 데이터 개체를 복제합니다. 논리 복제를 사용하면 테이블 수준 데이터 복제를 비롯하여 데이터 복제를 세부적으로 제어할 수 있습니다.
   2. 논리 스트리밍 복제를 제공하는 [pglogical](https://github.com/2ndQuadrant/pglogical) 확장을 사용하면 데이터베이스의 초기 스키마 복사, TRUNCATE 지원, DDL 복제 기능 등의 추가 기능을 제공합니다. 
2. **논리 디코딩** 은 WAL(미리 쓰기 로그)의 콘텐츠를 [디코딩](https://www.postgresql.org/docs/12/logicaldecoding-explanation.html)하여 구현합니다. 

## <a name="comparing-logical-replication-and-logical-decoding"></a>논리 복제 및 논리 디코딩 비교
논리 복제 및 논리 디코딩은 여러 유사성을 가집니다. 공통적인 성격은 다음과 같습니다.
* Postgres에서 데이터를 복제할 수 있습니다.
* 변경 내용 소스로 [WAL(미리 쓰기 로그)](https://www.postgresql.org/docs/current/wal.html)을 사용합니다.
* [논리 복제 슬롯](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)을 사용하여 데이터를 보냅니다. 슬롯은 변경 내용의 스트림을 나타냅니다.
* 테이블의 [REPLICA IDENTITY 속성](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY)을 사용하여 보낼 수 있는 변경 내용을 확인합니다.
* DDL 변경 내용을 복제하지 않습니다.


두 기술의 차이점은 논리 복제입니다. 
* 복제할 테이블 또는 테이블 집합을 지정할 수 있습니다.
* PostgreSQL 인스턴스 간에 데이터를 복제합니다.

논리 디코딩 
* 데이터베이스의 모든 테이블에서 변경 내용을 추출합니다. 
* PostgreSQL 인스턴스 간에 데이터를 직접 보낼 수 없습니다.

>[!NOTE]
> 현재 유연한 서버는 지역 간 읽기 복제본을 지원하지 않습니다. 워크로드 유형에 따라 지역 간 DR(재해 복구) 용도로 논리적 복제 기능을 사용하도록 선택할 수 있습니다.

## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>논리 복제 및 논리 디코딩을 위한 필수 구성 요소

1. 포털의 서버 매개 변수 페이지로 이동합니다.
2. `wal_level` 서버 매개 변수를 `logical`로 설정합니다.
3. pglogical 확장을 사용하려는 경우 `shared_preload_libaries` 매개 변수를 검색하고 드롭다운 상자에서 `pglogical`을 선택합니다.
4. `max_worker_processes` 매개 변수 값을 최소 16으로 업데이트합니다. 그렇지 않으면 `WARNING: out of background worker slots`와 같은 문제가 발생할 수 있습니다.
5. 변경 내용을 저장하고 서버를 다시 시작하여 `wal_level` 변경 내용을 적용합니다.
6. PostgreSQL 인스턴스가 연결하는 리소스의 네트워크 트래픽을 허용하는지 확인합니다.
7. 관리 사용자 복제 권한을 부여합니다.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```
8. 사용 중인 역할에 복제하는 스키마에 대한 [권한](https://www.postgresql.org/docs/current/sql-grant.html)이 있는지 확인할 수 있습니다. 그렇지 않으면 `Permission denied for schema`와 같은 오류가 발생할 수 있습니다. 

## <a name="using-logical-replication-and-logical-decoding"></a>논리 복제 및 논리 디코딩 사용

### <a name="native-logical-replication"></a>원시 논리 복제
논리 복제는 ‘게시자’와 ‘구독자’라는 용어를 사용합니다. 
* 게시자는 데이터를 **보내는** PostgreSQL 데이터베이스입니다. 
* 구독자는 데이터를 **받는** PostgreSQL 데이터베이스입니다.

논리 복제를 시도하는 데 사용할 수 있는 몇 가지 샘플 코드는 다음과 같습니다.

1. 게시자 데이터베이스에 연결합니다. 테이블을 만들고 데이터를 추가합니다.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. 테이블에 대한 게시를 만듭니다.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. 구독자 데이터베이스로 연결합니다. 게시자와 동일한 스키마를 사용하여 테이블을 만듭니다.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. 이전에 만든 게시에 연결할 구독을 만듭니다.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. 이제 구독자로부터 테이블을 쿼리할 수 있습니다. 게시자에서 데이터를 수신하는 것을 볼 수 있습니다.
   ```SQL
   SELECT * FROM basic;
   ```
   게시자의 테이블에 더 많은 행을 추가하고 구독자에 대한 변경 내용을 볼 수 있습니다.

   데이터를 볼 수 없는 경우 `azure_pg_admin`에 대한 로그인 권한을 사용하도록 설정하고 테이블 콘텐츠를 확인합니다. 
   ```SQL 
   ALTER ROLE azure_pg_admin login;
   ```


[논리 복제](https://www.postgresql.org/docs/current/logical-replication.html)에 대한 자세한 정보는 PostgreSQL 설명서를 참조하세요.

### <a name="pglogical-extension"></a>pglogical 확장

다음은 공급자 데이터베이스 서버와 구독자에서 pglogical을 구성하는 예입니다. 세부 정보는 pglogical 확장 설명서를 참조하세요. 또한 위에 나열된 필수 구성 조건 작업을 수행했는지 확인합니다.

1. 공급자와 구독자 데이터베이스 서버 모두의 데이터베이스에 pglogical 확장을 설치합니다.
    ```SQL
   \C myDB
   CREATE EXTENSION pglogical;
   ```
2. **공급자**(원본/게시자) 데이터베이스 서버에서 공급자 노드를 만듭니다.
   ```SQL
   select pglogical.create_node( node_name := 'provider1', 
   dsn := ' host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
3. 복제 세트를 만듭니다.
   ```SQL
   select pglogical.create_replication_set('myreplicationset');
   ```
4. 데이터베이스의 모든 테이블을 복제 세트에 추가합니다.
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('myreplicationset', '{public}'::text[]);
   ```

   대체 방법으로 특정 스키마(예: testUser)의 테이블을 기본 복제 세트에 추가할 수도 있습니다.
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('default', ARRAY['testUser']);
   ```

5. **구독자** 데이터베이스 서버에서 구독자 노드를 만듭니다.
   ```SQL
   select pglogical.create_node( node_name := 'subscriber1', 
   dsn := ' host=mySubscriberServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPasword' );
   ```
6. 동기화 및 복제 프로세스를 시작하는 구독을 만듭니다.
    ```SQL
   select pglogical.create_subscription (
   subscription_name := 'subscription1',
   replication_sets := array['myreplicationset'],
   provider_dsn := 'host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
7. 그런 다음, 구독 상태를 확인할 수 있습니다.
   ```SQL
   SELECT subscription_name, status FROM pglogical.show_subscription_status();
   ```

### <a name="logical-decoding"></a>논리 디코딩
논리 디코딩을 스트리밍 프로토콜 또는 SQL 인터페이스를 통해 이용할 수 있습니다. 

#### <a name="streaming-protocol"></a>스트리밍 프로토콜
스트리밍 프로토콜을 사용하여 변경 내용을 사용하는 것이 좋습니다. 사용자 고유의 소비자 및 커넥터를 만들거나 [Debezium](https://debezium.io/)과 같은 타사 서비스를 사용할 수 있습니다. 

[pg_recvlogical로 스트리밍 프로토콜을 사용하는 예제](https://github.com/eulerto/wal2json#pg_recvlogical)는 wal2json 설명서를 참조하세요.

#### <a name="sql-interface"></a>SQL 인터페이스 
아래 예제에서는 wal2json 플러그인과 함께 SQL 인터페이스를 사용합니다.
 
1. 슬롯을 만듭니다.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL 명령을 부여합니다. 예를 들면 다음과 같습니다.
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 변경 내용을 이용합니다.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   출력은 다음과 같습니다.
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. 슬롯을 사용했으면 슬롯을 삭제합니다.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

[논리 디코딩](https://www.postgresql.org/docs/current/logicaldecoding.html)에 대해 자세히 알아보려면 PostgreSQL 설명서를 참조하세요.


## <a name="monitoring"></a>모니터링
논리 디코딩을 모니터링해야 합니다. 사용하지 않는 모든 복제 슬롯을 삭제해야 합니다. 슬롯은 변경 내용을 읽을 때까지 Postgres WAL 로그 및 관련 시스템 카탈로그에 저장됩니다. 구독자 또는 소비자가 실패하거나 적절히 구성되지 않은 경우에는 소비되지 않은 로그가 누적되어 스토리지를 채웁니다. 또한 로그가 이용되지 않으면 트랜잭션 ID 래핑의 위험이 증가합니다. 두 경우 모두 서버를 사용할 수 없게 될 수 있습니다. 따라서 논리 복제 슬롯을 지속적으로 이용하는 것이 중요합니다. 논리 복제 슬롯을 더 이상 사용하지 않는 경우 즉시 삭제합니다.

Pg_replication_slots 보기의 ‘활성’ 열은 슬롯에 소비자가 연결되어 있는지 여부를 나타냅니다.
```SQL
SELECT * FROM pg_replication_slots;
```
**최대 사용 트랜잭션 ID** 및 **스토리지에서 사용한** 유연한 서버 메트릭에 대해 [경고를 설정](howto-alert-on-metrics.md)하여 값이 기본 임계값을 초과하는 경우 사용자에게 알립니다. 

## <a name="limitations"></a>제한 사항
* **논리 복제** 제한 사항은 [여기](https://www.postgresql.org/docs/12/logical-replication-restrictions.html)에서 설명한 대로 적용됩니다.
* **읽기 복제본** - Azure Database for PostgreSQL 읽기 복제본은 현재 유연한 서버에서 지원되지 않습니다.
* **슬롯 및 HA 장애 조치(failover)** - 주 서버의 논리 복제 슬롯은 보조 AZ의 대기 서버에서 사용할 수 없습니다. 이는 서버에서 영역 중복 고가용성 옵션을 사용하는 경우에 적용됩니다. 대기 서버에 대한 장애 조치(failover)가 발생하는 경우 대기 서버에서 논리 복제 슬롯을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [네트워킹 옵션](concepts-networking.md)에 관한 자세한 정보
* 유연한 서버에서 사용할 수 있는 [확장](concepts-extensions.md)에 대한 자세한 정보
* [고가용성](concepts-high-availability.md)에 대해 자세한 정보

