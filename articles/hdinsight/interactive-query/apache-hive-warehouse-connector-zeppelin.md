---
title: Hive Warehouse Connector - Livy를 사용하는 Apache Zeppelin - Azure HDInsight
description: Azure HDInsight의 Apache Zeppelin과 Hive Warehouse Connector를 통합하는 방법에 대해 알아봅니다.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 6a7d58a1646e77e99ad7282af5be0dc1f8d2976d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529372"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Zeppelin과 Hive Warehouse Connector 통합

HDInsight Spark 클러스터에는 다른 인터프리터를 사용하는 Apache Zeppelin 노트북이 포함되어 있습니다. 이 문서에서는 Hive Warehouse Connector를 사용하여 Spark에서 Hive 테이블에 액세스하기 위해 Livy 인터프리터에 대해서만 중점적으로 설명합니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *허용 목록* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

## <a name="prerequisite"></a>필수 요소

[Hive Warehouse Connector 설정](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) 단계를 완료합니다.

## <a name="getting-started"></a>시작

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 Apache Spark 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. ssh 세션에서 다음 명령을 실행하여 `hive-warehouse-connector-assembly` 및 `pyspark_hwc`에 대한 버전을 확인합니다.

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Apache Zeppelin를 구성할 때 나중에 사용할 출력을 저장합니다.

## <a name="configure-livy"></a>Livy 구성

Livy 인터프리터를 사용하여 Zeppelin에서 Hive 테이블에 액세스하려면 다음과 같은 구성이 필요합니다.

### <a name="interactive-query-cluster"></a>대화형 쿼리 클러스터

1. 웹 브라우저에서 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs`로 이동합니다. 여기서 LLAPCLUSTERNAME은 대화형 쿼리 클러스터의 이름입니다.

1. **고급** > **사용자 지정 core-site** 로 이동합니다. **속성 추가...** 를 선택하여 다음 구성을 추가합니다.

    | 구성                 | 값 |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. 변경 내용을 저장하고 영향을 받는 모든 구성 요소를 다시 시작합니다.

### <a name="spark-cluster"></a>Spark 클러스터

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`로 이동합니다. 여기서 CLUSTERNAME은 Apache Spark 클러스터의 이름입니다.

1. **사용자 지정 livy2-conf** 를 확장합니다. **속성 추가...** 를 선택하여 다음 구성을 추가합니다.

    | 구성                 | 값                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. 변경 내용을 저장하고 영향을 받는 모든 구성 요소를 다시 시작합니다.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Zeppelin UI에서 Livy 인터프리터 구성(Spark 클러스터)

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter`로 이동합니다. 여기서 `CLUSTERNAME`은 Apache Spark 클러스터의 이름입니다.

1. **livy2** 로 이동합니다.

1. 다음 구성들을 추가합니다.

    | 구성                 | 값                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy,zeppelin |
    | livy.spark.jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>VERSION을 앞부분의 [시작](#getting-started)에서 가져온 값으로 바꿉니다. |
    | livy.spark.submit.pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>VERSION을 앞부분의 [시작](#getting-started)에서 가져온 값으로 바꿉니다. |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | 대화형 쿼리 클러스터의 HiveServer2 대화형 JDBC URL로 설정합니다. |
    | spark.security.credentials.hiveserver2.enabled | true |

1. ESP 클러스터의 경우에만 다음 구성을 추가합니다.

    | 구성| 값|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/_HOST@<AAD-Domain>` |

    * [ssh 명령](../hdinsight-hadoop-linux-use-ssh-unix.md)을 사용하여 Interactive Query 클러스터에 연결합니다. `/etc/krb5.conf` 파일에서 `default_realm` 매개 변수를 찾습니다. `<AAD-DOMAIN>`을 대문자 문자열로 하여 이 값으로 바꿉니다. 그렇지 않으면 자격 증명을 찾을 수 없습니다.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Hive Warehouse Connector AAD 도메인" border="true":::

1. 변경 내용을 저장하고 Livy 인터프리터를 다시 시작합니다.

Livy 인터프리터에 액세스할 수 없는 경우, Ambari의 Zeppelin 구성 요소 내에 있는 `shiro.ini` 파일을 수정합니다. 자세한 내용은 [Apache Zeppelin Security](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html)구성을 참조하세요.  

## <a name="running-queries-in-zeppelin"></a>Zeppelin에서 쿼리 실행

Livy 인터프리터를 사용하여 Zeppelin 노트북을 시작하고 다음을 실행합니다.

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>다음 단계

* [HWC 및 Apache Spark 작업](./apache-hive-warehouse-connector-operations.md)
* [Apache Spark 및 Apache Hive와 HWC 통합](./apache-hive-warehouse-connector.md)
* [HDInsight에서 대화형 쿼리 사용](./apache-interactive-query-get-started.md)