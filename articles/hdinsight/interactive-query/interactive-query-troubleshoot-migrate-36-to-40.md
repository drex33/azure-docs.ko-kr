---
title: 3\.6에서 4.0으로 Hive 마이그레이션 문제 해결 - Azure HDInsight
description: HDInsight 3.6에서 4.0으로 Hive 워크로드 마이그레이션을 위한 문제 해결 가이드
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/12/2021
ms.openlocfilehash: eecf8cd36c1f631176ce836523be802d751ed55b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625965"
---
# <a name="troubleshooting-guide-for-migration-of-hive-workloads-from-hdinsight-36-to-hdinsight-40"></a>HDInsight 3.6에서 HDInsight 4.0으로 Hive 워크로드 마이그레이션을 위한 문제 해결 가이드

이 문서에서는 Hive 워크로드를 HDInsight 3.6에서 HDInsight 4.0으로 마이그레이션할 때 고객이 직면하는 가장 일반적인 문제에 대한 답변을 제공합니다.

## <a name="reduce-latency-when-running-describe-table_name"></a>`DESCRIBE TABLE_NAME` 실행 시 대기 시간 줄이기

해결 방법:

* 한 일괄 처리 내에 메타스토어에서 검색할 수 있는 최대 개체(테이블/파티션) 수를 늘립니다. 만족스러운 대기 시간 수준에 도달할 때까지 큰 수(기본값은 300)로 설정합니다. 숫자가 높을수록 Hive 메타스토어 서버에 필요한 왕복 횟수가 줄어들지만 클라이언트 측에서 더 높은 메모리 요구 사항이 발생할 수도 있습니다.

  `hive.metastore.batch.retrieve.max=2000`

* Hive 및 모든 오래된 서비스 다시 시작

## <a name="unable-to-query-gzipped-text-file-if-skipheaderlinecount-and-skipfooterlinecount-are-set-for-table"></a>skip.header.line.count 및 skip.footer.line.count가 테이블에 대해 설정된 경우 Gzipped 텍스트 파일을 쿼리할 수 없음

문제가 Interactive Query 4.0에서 수정되었지만 여전히 Interactive Query 3.1.0에서는 수정되지 않았습니다.

해결 방법:
* ```"skip.header.line.count"="1"``` 및 ```"skip.footer.line.count"="1"```을 사용하지 않고 테이블을 만든 다음 원래 테이블로부터 쿼리에서 머리글/바닥글 행을 제외하는 뷰를 만듭니다.

## <a name="unable-to-use-unicode-characters"></a>유니코드 문자를 사용할 수 없음

해결 방법:
1. 클러스터의 hive 메타스토어 데이터베이스에 연결합니다.

2. 다음 명령을 사용하여 `TBLS` 및 `TABLE_PARAMS` 테이블을 백업합니다.
    ```sql
        select * into tbls_bak from tbls;
        select * into table_params_bak from table_params;
    ```

3. 영향을 받는 열 유형을 `nvarchar(max)`로 수동으로 변경합니다.
    ```sql 
        alter table TABLE_PARAMS alter column PARAM_VALUE nvarchar(max);
        alter table TBLS alter column VIEW_EXPANDED_TEXT nvarchar(max) null;    
        alter table TBLS alter column VIEW_ORIGINAL_TEXT nvarchar(max) null;
    ```

## <a name="create-table-as-select-ctas-creates-a-new-table-with-same-uuid"></a>Create table as select(CTAS)는 동일한 UUID로 새 테이블을 만듭니다.

Hive 3.1(HDInsight 4.0)은 고유한 UUID를 생성하는 기본 제공 UDF를 제공합니다. Hive UUID() 메서드는 CTAS를 사용하더라도 고유 ID를 생성합니다. 다음과 같이 사용할 수 있습니다.
```hql
create table rhive as
select uuid() as UUID
from uuid_test
```

## <a name="hive-job-output-format-differs-from-hdinsight-36"></a>Hive 작업 출력 형식이 HDInsight 3.6과 다릅니다.

HDInsight 3.6과 HDInsight 4.0의 WebHCat(Templeton) 차이로 인해 발생합니다.

* Hive Rest API - ```arg=--showHeader=false -d arg=--outputformat=tsv2 -d``` 추가

* .NET SDK - ```HiveJobSubmissionParameters```의 인수 초기화
    ```csharp
    List<string> args = new List<string> { { "--showHeader=false" }, { "--outputformat=tsv2" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SELECT clientid,market from hivesampletable LIMIT 10",
                    Defines = defines,
                    Arguments = args
                };
    ```

## <a name="reduce-hive-internal-table-creation-latency"></a>Hive 내부 테이블 만들기 대기 시간 줄이기

1. 고급 hive-site 및 고급 hivemetastore-site에서 ```hive.metastore.transactional.event.listeners```에 대한 ```org.apache.hive.hcatalog.listener.DbNotificationListener``` 값을 삭제합니다. 

2. ```hive.metastore.event.listeners```에 값이 있으면 제거합니다.

3. DbNotificationListener는 REPL 명령을 사용하는 경우에만 필요하며 그렇지 않은 경우 제거하는 것이 안전합니다.

    :::image type="content" source="./media/apache-hive-40-migration-guide/hive-reduce-internal-table-creation-latency.png" alt-text="HDInsight 4.0에서 내부 테이블 대기 시간 줄이기" border="true":::

## <a name="change-hive-default-table-location"></a>Hive 기본 테이블 위치 변경

이 동작 변경은 HDInsight 4.0(Hive 3.1)에서 의도적으로 설계된 것입니다. 이 변경의 주요 이유는 파일 권한을 제어하기 위함입니다. 

사용자 지정 위치에서 외부 테이블을 만들려면 create table 문에서 위치를 지정합니다.

## <a name="disable-acid-in-hdinsight-40"></a>HDInsight 4.0에서 ACID를 사용하지 않도록 설정

HDInsight 4.0에서 ACID를 사용하도록 설정하는 것이 좋습니다. Hive의 기능 및 성능을 포함한 대부분의 최근 개선 사항은 ACID 테이블에서만 사용할 수 있습니다.

HDInsight 4.0에서 ACID를 사용하지 않도록 설정하는 단계:
1. Ambari에서 다음 Hive 구성을 변경합니다.

    ```text
    hive.strict.managed.tables=false
    hive.support.concurrency=false; 
    hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DummyTxnManager;
    hive.enforce.bucketing=false;
    hive.compactor.initiator.on=false;
    hive.compactor.worker.threads=0;
    hive.create.as.insert.only=false;
    metastore.create.as.acid=false;
    ```

2. Hive 서비스를 다시 시작합니다.

> [!IMPORTANT]
> Microsoft는 HDInsight 3.6 및 HDInsight 4.0 Hive 관리 테이블과 동일한 데이터/스토리지를 공유하지 않을 것을 권장합니다. 이는 지원되지 않는 시나리오입니다.

* 일반적으로 HDInsight 4.0 클러스터에서 Hive 테이블을 만들기 전에도 위의 구성을 설정해야 합니다. 관리되는 테이블이 만들어지면 ACID를 사용하지 않도록 설정하면 안 됩니다. 잠재적으로 데이터 손실 또는 일관성 없는 결과를 초래할 수 있습니다. 따라서 새 클러스터를 만들 때 한 번 설정하고 나중에 변경하지 않는 것이 좋습니다.

* 테이블을 만든 후 ACID를 사용하지 않도록 설정하는 것은 위험하지만 이를 수행하려는 경우 잠재적인 데이터 손실 또는 불일치를 방지하기 위해 아래 단계를 따릅니다.

    1. 동일한 스키마로 외부 테이블을 만들고 CTAS 명령 ```create external table e_t1 select * from m_t1```을 사용하여 원래 관리되는 테이블에서 데이터를 복사합니다.    
    2. ```drop table m_t1```을 사용하여 관리 테이블을 삭제합니다.
    3. 제안된 구성을 사용하여 ACID를 사용하지 않도록 설정합니다.        
    4. m_t1을 다시 만들고 CTAS 명령 ```create table m_t1 select * from e_t1```을 사용하여 외부 테이블에서 데이터를 복사합니다.        
    5. ```drop table e_t1```을 사용하여 외부 테이블을 삭제합니다.

ACID를 사용하지 않도록 설정하기 전에 모든 관리 테이블이 외부 테이블로 변환되고 삭제되었는지 확인합니다. 또한 불일치를 방지하기 위해 각 단계 후에 스키마와 데이터를 비교합니다.

## <a name="create-hive-external-table-with-755-permission"></a>755 권한으로 Hive 외부 테이블 만들기

이 문제는 다음 두 가지 옵션 중 하나로 해결할 수 있습니다.

1. 수동으로 폴더 권한을 757 또는 777로 설정하여 하이브 사용자가 디렉터리에 쓸 수 있도록 합니다.

2. “Hive 권한 부여 관리자”를 ```org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider```에서 ```org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly```로 변경합니다.

Hive 메타스토어가 HDInsight 4.0에 포함되어 있지 않기 때문에 MetaStoreAuthzAPIAuthorizerEmbedOnly는 보안 검사를 효과적으로 사용하지 않도록 설정합니다. 그러나 다른 잠재적인 문제가 발생할 수 있습니다. 이 옵션을 사용하는 경우 주의해야 합니다.

## <a name="permission-errors-in-hive-job-after-upgrading-to-hdinsight-40"></a>HDInsight 4.0으로 업그레이드한 후 Hive 작업의 권한 오류

* HDInsight 4.0에서 Hive 구성 요소가 있는 모든 클러스터 모양은 새 권한 부여 공급자로 구성됩니다. 

  `org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider`

* HDFS 파일 권한은 액세스 중인 파일에 대해 Hive 사용자에게 할당되어야 합니다. 오류 메시지는 문제를 해결하는 데 필요한 세부 정보를 제공합니다.

* HDInsight 3.6 Hive 클러스터에서 사용되는 ```MetaStoreAuthzAPIAuthorizerEmbedOnly``` 공급자로 전환할 수도 있습니다.

  `org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly`

  :::image type="content" source="./media/apache-hive-40-migration-guide/hive-job-permission-errors.png" alt-text="MetaStoreAuthzAPIAuthorizerEmbedOnly로 권한 부여 설정" border="true":::

## <a name="unable-to-query-table-with-opencsvserde"></a>OpenCSVSerde로 테이블을 쿼리할 수 없음

`csv` 형식 테이블에서 데이터를 읽으면 다음과 같은 예외가 발생할 수 있습니다.
```text
MetaException(message:java.lang.UnsupportedOperationException: Storage schema reading not supported)
```

해결 방법:

* Ambari UI를 통해 `Custom hive-site`에 구성 `metastore.storage.schema.reader.impl`=`org.apache.hadoop.hive.metastore.SerDeStorageSchemaReader` 추가

* 모든 오래된 Hive 서비스 다시 시작

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
