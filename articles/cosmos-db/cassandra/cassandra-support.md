---
title: Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능
description: Azure Cosmos DB Cassandra API에서 지원되는 Apache Cassandra 기능에 대해 알아봅니다.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 910e478e5bcdf2fb7ade7169a6182087ea7f3987
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133270950"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능 
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. CQL 바이너리 프로토콜 v4 [유선 프로토콜](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) 호환 오픈 소스 Cassandra 클라이언트 [드라이버](https://cassandra.apache.org/doc/latest/cassandra/getting_started/drivers.html?highlight=driver)를 통해 Azure Cosmos DB Cassandra API와 통신할 수 있습니다. 

Azure Cosmos DB Cassandra API를 사용하면 Apache Cassandra API의 기능뿐 아니라 Azure Cosmos DB에서 제공되는 엔터프라이즈 기능도 활용할 수 있습니다. 엔터프라이즈 기능에는 [전역 배포](../distribute-data-globally.md), [자동 규모 확장 분할](cassandra-partitioning.md), 가용성 및 대기 시간 보장, 미사용 시 암호화, 백업 등 여러 가지가 있습니다.

## <a name="cassandra-protocol"></a>Cassandra 프로토콜 

Azure Cosmos DB Cassandra API는 CQL(Cassandra Query Language) v3.11 API(버전 2.x와 역호환 가능)와 호환됩니다. 지원되는 CQL 명령, 도구, 제한 사항 및 예외는 아래에 나와 있습니다. 이러한 프로토콜을 인식하는 모든 클라이언트 드라이버는 Azure Cosmos DB Cassandra API에 연결할 수 있습니다.

## <a name="cassandra-driver"></a>Cassandra 드라이버

Azure Cosmos DB Cassandra API에서 지원하는 Cassandra 드라이버의 버전은 다음과 같습니다.

* [Java 3.5 이상](https://github.com/datastax/java-driver)  
* [C# 3.5 이상](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5 이상](https://github.com/datastax/nodejs-driver)  
* [Python 3.15 이상](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 

## <a name="cql-data-types"></a>CQL 데이터 형식 

Azure Cosmos DB Cassandra API는 다음 CQL 데이터 형식을 지원합니다.

|유형  |지원됨 |
|---------|---------|
| ascii  | 예 |
| bigint  | 예 |
| blob  | 예 |
| boolean  | 예 |
| counter  | 예 |
| date  | 예 |
| decimal  | 예 |
| double  | 예 |
| float  | 예 |
| frozen  | 예 |
| inet  | 예 |
| int  | 예 |
| list  | 예 |
| set  | 예 |
| smallint  | 예 |
| text  | 예 |
| time  | 예 |
| timestamp  | 예 |
| timeuuid  | 예 |
| tinyint  | 예 |
| tuple  | 예 |
| uuid  | 예 |
| varchar  | 예 |
| varint  | 예 |
| tuples | 예 | 
| udts  | 예 |
| map | 예 |

데이터 형식 선언에 정적이 지원됩니다.

## <a name="cql-functions"></a>CQL 함수

Azure Cosmos DB Cassandra API는 다음 CQL 함수를 지원합니다.

|명령  |지원됨 |
|---------|---------|
| Token * | 예 |
| ttl *** | 예 |
| writetime *** | 예 |
| 캐스트 ** | Yes |

> [!NOTE] 
> \* Cassandra API는 토큰을 프로젝션/선택기로 지원하며 where 절의 왼쪽에는 token(pk)만 허용합니다. 예를 들어 `WHERE token(pk) > 1024`는 지원되지만 `WHERE token(pk) > token(100)`은 지원되지 **않습니다**.  
> \*\* `cast()` 함수는 Cassandra API에서 중첩할 수 없습니다. 예를 들어 `SELECT cast(count as double) FROM myTable`는 지원되지만 `SELECT avg(cast(count as double)) FROM myTable`은 지원되지 **않습니다**.    
> \*\*\* `USING` 옵션으로 지정된 사용자 지정 타임스탬프 및 TTL은 셀이 아닌 행 수준에서 적용됩니다.



집계 함수:

|명령  |지원됨 |
|---------|---------|
| avg | 예 |
| count | 예 |
| min | 예 |
| max | 예 |
| sum | 예 |

> [!NOTE]
> 집계 함수는 일반 열에서 작동하지만 클러스터링 열에 대한 집계는 지원되지 **않습니다**.


Blob 변환 함수:
 
|명령  |지원됨 |
|---------|---------|
| typeAsBlob(value)   | 예 |
| blobAsType(value) | 예 |


UUID 및 timeuuid 함수:
 
|명령  |지원됨 |
|---------|---------|
| dateOf()  | 예 |
| now()  | 예 |
| minTimeuuid()  | 예 |
| unixTimestampOf()  | 예 |
| toDate(timeuuid)  | 예 |
| toTimestamp(timeuuid)  | 예 |
| toUnixTimestamp(timeuuid)  | 예 |
| toDate(timestamp)  | 예 |
| toUnixTimestamp(timestamp)  | 예 |
| toTimestamp(date)  | 예 |
| toUnixTimestamp(date) | 예 |


  
## <a name="cql-commands"></a>CQL 명령

Azure Cosmos DB는 Cassandra API 계정에서 다음 데이터베이스 명령을 지원합니다.

|명령  |지원됨 |
|---------|---------|
| ALLOW FILTERING | 예 |
| ALTER KEYSPACE | 해당 없음(PaaS 서비스, 내부적으로 복제 관리)|
| ALTER MATERIALIZED VIEW | 예 |
| ALTER_ROLE | 예 |
| ALTER TABLE | 예 |
| ALTER TYPE | 예 |
| ALTER USER | 예 |
| BATCH | 예(기록되지 않은 일괄 처리만 해당)|
| COMPACT STORAGE | 해당 없음(PaaS 서비스) |
| CREATE AGGREGATE | 예 | 
| CREATE CUSTOM INDEX (SASI) | 예 |
| CREATE  INDEX | 예([인덱스 이름 지정](secondary-indexing.md) 없음, 클러스터링 키 또는 전체 FROZEN 컬렉션에 대한 인덱스가 지원되지 않음) |
| CREATE FUNCTION | 예 |
| CREATE KEYSPACE(복제 설정 무시됨) | 예 |
| CREATE MATERIALIZED VIEW | 예 |
| CREATE TABLE | 예 |
| CREATE TRIGGER | 예 |
| CREATE TYPE | 예 |
| CREATE ROLE | 예 |
| CREATE USER(네이티브 Apache Cassandra에서는 사용되지 않음) | 예 |
| Delete | 예 |
| DISTINCT | 예 |
| DROP AGGREGATE | 예 |
| .DROP FUNCTION | 예 |
| DROP INDEX | 예 |
| DROP KEYSPACE | 예 |
| DROP MATERIALIZED VIEW | 예 |
| DROP ROLE | 예 |
| DROP TABLE | 예 |
| DROP_TRIGGER | 예 | 
| DROP TYPE | 예 |
| DROP USER(네이티브 Apache Cassandra에서는 사용되지 않음) | 예 |
| GRANT | 예 |
| INSERT | 예 |
| LIST PERMISSIONS | 예 |
| LIST ROLES | 예 |
| LIST USERS(네이티브 Apache Cassandra에서는 사용되지 않음) | 예 |
| REVOKE | 예 |
| SELECT | 예 |
| UPDATE | 예 |
| TRUNCATE | 예 |
| USE | 예 |

## <a name="lightweight-transactions-lwt"></a>경량 트랜잭션(LWT)

| 구성 요소  |지원됨 |
|---------|---------|
| 존재하는 경우 삭제 | 예 |
| 삭제 조건 | 예 |
| 없는 경우 삽입 | 예 |
| 존재하는 경우 업데이트 | 예 |
| 없는 경우 업데이트 | Yes |
| 업데이트 조건 | 예 |

> [!NOTE]
> 경량 트랜잭션은 현재 다중 지역 쓰기를 사용하는 계정에 대해 지원되지 않습니다.

## <a name="cql-shell-commands"></a>CQL Shell 명령

Azure Cosmos DB는 Cassandra API 계정에서 다음 데이터베이스 명령을 지원합니다.

|명령  |지원됨 |
|---------|---------|
| 캡처 | Yes |
| CLEAR | Yes |
| 일관성 * | 해당 없음 |
| 복사 | No |
| DESCRIBE | Yes |
| cqlshExpand | No |
| EXIT | Yes |
| LOGIN | 해당 없음(CQL 함수 `USER`가 지원되지 않으므로 `LOGIN`이 중복됨) |
| 페이징 | Yes |
| 직렬 일관성 * | 해당 없음 |
| 표시 | Yes |
| 원본 | 예 |
| 추적 | 해당 없음(Cassandra API는 Azure Cosmos DB에 의해 지원됨 - 문제 해결을 위해 [진단 로깅](../cosmosdb-monitor-resource-logs.md) 사용) |

> [!NOTE] 
>  일관성은 Azure Cosmos DB에서 다르게 작동합니다. 자세한 내용은 [여기](apache-cassandra-consistency-mapping.md)를 참조하세요.  


## <a name="json-support"></a>JSON 지원
|명령  |지원됨 |
|---------|---------|
| SELECT JSON | 예 |
| INSERT JSON | 예 |
| fromJson() | 예 |
| toJson() | 예 |


## <a name="cassandra-api-limits"></a>Cassandra API 한도

Azure Cosmos DB Cassandra API에는 테이블에 저장할 수 있는 데이터 크기에 제한이 없습니다. 그러므로 파티션 키 제한 범위 내에서 수백 TB/PB에 달하는 데이터를 저장할 수 있습니다. 마찬가지로, 모든 엔터티 또는 그에 해당하는 행에도 열 수는 제한되지 않습니다. 그러나 엔터티의 총 크기는 2MB를 초과할 수 없습니다. 파티션 키당 데이터는 다른 모든 API와 마찬가지로 20GB를 초과할 수 없습니다.

## <a name="tools"></a>도구 

Azure Cosmos DB Cassandra API는 관리되는 서비스 플랫폼입니다. 그러므로 클러스터 관리 과정의 오버헤드가 발생하지 않으며 가비지 수집기, JVM(Java Virtual Machine), nodetool 등의 유틸리티를 사용할 필요가 없습니다. 그리고 이진 CQLv4 호환성을 활용하는 cqlsh 등의 도구를 지원합니다. 

* 계정 관리를 위해 지원되는 다른 메커니즘으로는 Azure Portal의 데이터 탐색기, 메트릭, 로그 진단, PowerShell 및 CLI 등이 있습니다.

## <a name="hosted-cql-shell-preview"></a>호스팅된 CQL 셸(미리 보기)

호스팅된 네이티브 Cassandra 셸(CQLSH v5.0.1)은 [Azure Portal](../data-explorer.md) 또는 [Azure Cosmos DB Explorer](https://cosmos.azure.com/)의 데이터 탐색기에서 직접 열 수 있습니다. CQL 셸을 사용하도록 설정하기 전에 계정에서 [Notebooks 기능을 사용하도록 설정](../enable-notebooks.md)해야 합니다(아직 활성화하지 않은 경우 `Open Cassandra Shell`을 클릭하면 메시지가 표시됨). 지원되는 Azure 지역에 대해 [Azure Cosmos DB 계정용 Notebooks 사용](../enable-notebooks.md#supported-regions) 문서를 참조하세요.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="오픈 CQLSH":::

로컬 머신에 설치된 CQLSH를 사용하여 Azure Cosmos DB의 Cassandra API에 연결할 수도 있습니다. Apache Cassandra 3.1.1과 함께 제공되며 환경 변수를 설정하여 즉시 사용할 수 있습니다. 다음 섹션에는 CQLSH를 사용하여 Windows 또는 Linux에서 Azure Cosmos DB의 Cassandra API를 설치, 구성 및 연결하는 지침이 포함되어 있습니다.

> [!NOTE]
> Azure Cosmos DB Cassandra API에 대한 연결은 DSE(DataStax Enterprise) 버전의 CQLSH에서 작동하지 않습니다. Cassandra API에 연결할 때는 오픈 소스 Apache Cassandra 버전의 CQLSH만 사용해야 합니다. 

**Windows:**

Windows를 사용하는 경우 [Linux용 Windows 파일 시스템](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux)을 사용하도록 설정하는 것이 좋습니다. 그런 다음, 아래의 linux 명령을 따를 수 있습니다.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

CQL v4 호환 SDK를 통해 실행하는 모든 CRUD 작업에서는 오류, 사용한 요청 단위와 관련된 추가 정보가 반환됩니다. 프로비저닝된 처리량을 가장 효율적으로 사용하기 위해 리소스 거버넌스를 고려하면서 DELETE 및 UPDATE 명령을 처리해야 합니다.

* 참고: gc_grace_seconds 값은 지정하는 경우 0이어야 합니다.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>일관성 매핑 

Azure Cosmos DB Cassandra API에서는 읽기 작업을 일관되게 수행할지 여부를 선택할 수 있습니다.  일관성 매핑에 대한 자세한 내용은 [여기](apache-cassandra-consistency-mapping.md#mapping-consistency-levels)를 참조하세요.

## <a name="permission-and-role-management"></a>권한 및 역할 관리

Azure Cosmos DB는 프로비저닝, 키 회전, 메트릭 보기를 위한 Azure RBAC(Azure 역할 기반 액세스 제어)와 [Azure Portal](https://portal.azure.com)을 통해 가져올 수 있는 읽기-쓰기 및 읽기 전용 암호/키를 지원합니다. Azure Cosmos DB는 CRUD 활동을 위한 역할을 지원하지 않습니다.

## <a name="keyspace-and-table-options"></a>키스페이스 및 테이블 옵션

"Create Keyspace" 명령에서 지역 이름, 클래스, replication_factor 및 데이터 센터의 옵션은 현재 무시됩니다. 시스템은 기본 Azure Cosmos DB의 [글로벌 배포](../global-dist-under-the-hood.md) 복제 방법을 사용하여 지역을 추가합니다. 영역 간 데이터가 필요한 경우 PowerShell, CLI 또는 포털을 사용하여 계정 수준에서 이를 활성화할 수 있습니다. 자세한 내용은 [지역 추가 방법](../how-to-manage-database-account.md#addremove-regions-from-your-database-account) 문서를 참조하세요. Azure Cosmos DB는 모든 쓰기가 내구성이 있는지 확인하기 때문에 Durable_writes를 비활성화할 수 없습니다. 모든 지역에서 Azure Cosmos DB는 4개의 복제본으로 구성된 복제본 세트 전체에 데이터를 복제하고 이 복제본 세트 [구성](../global-dist-under-the-hood.md)은 수정할 수 없습니다.
 
0으로 설정해야 하는 gc_grace_seconds를 제외하고 모든 옵션은 테이블을 만들 때 무시됩니다.
키스페이스 및 테이블에는 "cosmosdb_provisioned_throughput"이라는 추가 옵션이 있으며 최솟값은 400RU/s입니다. 키스페이스 처리량은 여러 테이블에서 처리량을 공유할 수 있도록 하며 모든 테이블이 프로비저닝된 처리량을 활용하지 않는 시나리오에 유용합니다. Alter Table 명령을 사용하면 지역 전체에서 프로비저닝된 처리량을 변경할 수 있습니다. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>보조 인덱스
Cassandra API는 고정 컬렉션 형식, 10진수 및 변형 형식을 제외한 모든 데이터 형식에 대한 보조 인덱스를 지원합니다. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Cassandra 다시 시도 연결 정책 사용

Azure Cosmos DB는 리소스 관리 시스템입니다. 즉, 작업에서 사용하는 요청 단위에 따라 특정 초에 특정 작업을 수행할 수 있습니다. 애플리케이션이 지정된 초 안에 해당 제한을 초과하는 경우 요청은 속도로 제한되며 예외가 throw됩니다. Azure Cosmos DB의 Cassandra API는 Cassandra 네이티브 프로토콜에서 이러한 예외를 오버로드된 오류로 변환합니다. 속도 제한이 있는 경우 애플리케이션이 요청을 가로채고 다시 시도할 수 있도록 [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) 및 [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) 확장이 제공됩니다. Azure Cosmos DB의 Cassandra API에 연결하는 경우 [버전 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) 및 [버전 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) Datastax 드라이버에 대한 Java 코드 샘플도 참조하세요. 다른 SDK를 사용하여 Azure Cosmos DB에서 Cassandra API에 액세스하는 경우 이러한 예외에 대해 다시 시도하는 연결 정책을 만듭니다.

## <a name="next-steps"></a>다음 단계

- Java 애플리케이션을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-account-java.md)를 시작합니다.
