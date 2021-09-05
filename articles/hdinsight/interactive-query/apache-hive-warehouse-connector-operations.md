---
title: Azure HDInsight의 Hive Warehouse Connector에서 지원하는 Apache Spark 작업
description: Azure HDInsight의 Hive Warehouse Connector에 대한 다양한 기능에 대해 알아봅니다.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: b876cd702a2398e8dcb0e1a0be5fffce0615e58d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529373"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight의 Hive Warehouse Connector에서 지원하는 Apache Spark 작업

이 문서에서는 HWC(Hive Warehouse Connector)에서 지원하는 Spark 기반 작업을 보여 줍니다. 아래에 표시된 모든 예는 Apache Spark 셸을 통해 실행됩니다.

## <a name="prerequisite"></a>필수 요소

[Hive Warehouse Connector 설정](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) 단계를 완료합니다.

## <a name="getting-started"></a>시작

Spark 셸 세션을 시작하려면 다음 단계를 수행합니다.

1. [ssh 명령](../hdinsight-hadoop-linux-use-ssh-unix.md)을 사용하여 Apache Spark 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. ssh 세션에서 다음 명령을 실행하여 `hive-warehouse-connector-assembly` 버전을 확인합니다.

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. 위에서 식별된 `hive-warehouse-connector-assembly` 버전을 사용하여 아래 코드를 편집합니다. 그런 다음 명령을 실행하여 Spark 셸을 시작합니다.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Spark 셸을 시작한 뒤 다음 명령을 사용하여 Hive Warehouse Connector 인스턴스를 시작할 수 있습니다.

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Hive 쿼리를 사용하여 Spark DataFrame 만들기

HWC 라이브러리를 사용하는 모든 쿼리 결과가 DataFrame으로 반환됩니다. 다음 예에서는 기본 hive 쿼리를 만드는 방법을 보여 줍니다.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

쿼리 결과는 Spark DataFrame이며 이는 MLIB 및 SparkSQL과 같은 Spark 라이브러리와 함께 사용할 수 있습니다.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Hive 테이블에 Spark DataFrame 작성

Spark는 기본적으로 Hive의 관리되는 ACID 테이블에 쓰기를 지원하지 않습니다. 그러나 HWC를 사용하여 Hive 테이블에 모든 DataFrame을 작성할 수 있습니다. 다음 예의 작업에서 이 기능을 확인할 수 있습니다.

1. 다음 명령을 사용하여 `sampletable_colorado`라는 테이블을 만들고 해당 열을 지정합니다.

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 열 `state`가 `Colorado`와 일치하는 테이블 `hivesampletable`을 필터링합니다. 이 하이브 쿼리는 Spark DataFrame을 반환하고 결과는 `write` 함수를 사용하여 Hive 테이블 `sampletable_colorado`에 저장됩니다.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. 다음 명령을 사용하여 결과를 확인합니다.

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="Hive Warehouse Connector hive 테이블 표시" border="true":::


## <a name="structured-streaming-writes"></a>구조적 스트림 작성

Hive Warehouse Connector를 사용하여 Spark 스트리밍을 사용해 데이터를 Hive 테이블에 쓸 수 있습니다.

> [!IMPORTANT]
> ESP 지원 Spark 4.0 클러스터에서 구조적 스트리밍 쓰기가 지원되지 않습니다.

아래 단계에 따라 Hive Warehouse Connector를 통해 localhost 포트 9999의 Spark 스트림에서 Hive 테이블로 데이터를 수집합니다

1. 열려 있는 Spark 셸에서 다음 명령을 사용하여 Spark 스트림을 시작합니다.

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 다음 단계를 통해 만든 Spark 스트림에 대한 데이터를 생성합니다.
    1. 동일한 Spark 클러스터에서 두 번째 SSH 세션을 엽니다.
    1. 명령 프롬프트에서 `nc -lk 9999`를 입력합니다. 이 명령은 `netcat` 유틸리티를 사용하여 데이터를 명령줄에서 지정된 포트로 보냅니다.

1. 첫 번째 SSH 세션으로 돌아가서 스트리밍 데이터를 보관할 새 Hive 테이블을 만듭니다. Spark 셸에서 다음 명령을 입력합니다.

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 그런 후에 다음 명령을 사용하여 새로 만든 테이블에 스트리밍 데이터를 작성합니다.

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` 및 `database` 옵션은 현재 Apache Spark의 알려진 문제로 인해 수동으로 설정해야 합니다. 이 문제에 대한 자세한 내용은 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)을 참조하세요.

1. 두 번째 SSH 세션으로 돌아가서 다음 값을 입력합니다.

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 첫 번째 SSH 세션으로 돌아가서 간단한 작업을 기록합니다. 데이터를 보려면 다음 명령을 사용합니다.

    ```scala
    hive.table("stream_table").show()
    ```

**Ctrl + C** 를 사용하여 두 번째 SSH 세션에서 `netcat`을 중지합니다. `:q`를 사용하여 첫 번째 SSH 세션에서 Spark 셸을 종료합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Spark 및 Apache Hive와 HWC 통합](./apache-hive-warehouse-connector.md)
* [HDInsight에서 대화형 쿼리 사용](./apache-interactive-query-get-started.md)
* [Apache Zeppelin와 HWC 통합](./apache-hive-warehouse-connector-zeppelin.md)
* [HWC 지원 API](./hive-warehouse-connector-apis.md)
