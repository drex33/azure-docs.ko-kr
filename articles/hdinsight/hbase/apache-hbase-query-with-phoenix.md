---
title: '빠른 시작: Apache HBase 및 Apache Phoenix - Azure HDInsight'
description: 이 빠른 시작에서는 HDInsight에서 Apache Phoenix를 사용하는 방법을 알아봅니다. 또한 컴퓨터에 SQLLine을 설치 및 설정하여 HDInsight의 HBase 클러스터에 연결하는 방법도 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive, mode-other
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 9e5adb9a0ecb5de059412af9f2563dfc78cc89b0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133045938"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>빠른 시작: Apache Phoenix를 사용하여 Azure HDInsight에서 Apache HBase 쿼리

이 빠른 시작에서는 Apache Phoenix를 사용하여 Azure HDInsight에서 HBase 쿼리를 실행하는 방법을 알아봅니다. Apache Phoenix는 Apache HBase용 SQL 쿼리 엔진입니다. JDBC 드라이버로 액세스되며 SQL을 사용하여 HBase 테이블을 쿼리하고 관리할 수 있도록 합니다. [SQLLine](http://sqlline.sourceforge.net/)은 SQL을 실행하는 명령줄 유틸리티입니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Apache HBase 클러스터 [클러스터 만들기](../hadoop/apache-hadoop-linux-tutorial-get-started.md)를 참조하여 HDInsight 클러스터를 만듭니다.  **HBase** 클러스터 유형을 선택해야 합니다.

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="identify-a-zookeeper-node"></a>ZooKeeper 노드 식별

HBase 클러스터에 연결할 때 Apache ZooKeeper 노드의 하나에 연결해야 합니다. 각 HDInsight 클러스터에는 세 개의 ZooKeeper 노드가 있습니다. ZooKeeper 노드를 신속하게 식별하는 데 curl을 사용할 수 있습니다. `PASSWORD` 및 `CLUSTERNAME`을 관련 값으로 바꿔서 아래 curl 명령을 편집한 다음, 명령 프롬프트에서 다음 명령을 입력합니다.

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

출력의 일부는 다음과 유사합니다.

```output
    {
      "href" : "http://hn*.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/<zookeepername1>.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "<zookeepername1>.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

나중에 사용할 수 있도록 `host_name`에 대한 값을 기록해 둡니다.

## <a name="create-a-table-and-manipulate-data"></a>테이블을 만든 후 데이터 조작

SSH를 사용하여 HBase 클러스터에 연결한 다음, Apache Phoenix를 사용하여 HBase 테이블을 만들고 데이터를 삽입하고 데이터를 쿼리할 수 있습니다.

1. `ssh` 명령을 사용하여 HBase 클러스터에 연결합니다. `CLUSTERNAME`을 클러스터의 이름으로 대체하여 아래 명령을 편집한 다음, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 디렉터리를 Phoenix 클라이언트로 변경합니다. 다음 명령을 입력합니다.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. [SQLLine](http://sqlline.sourceforge.net/)을 시작합니다. `ZOOKEEPER`를 이전에 식별한 ZooKeeper 노드로 바꾸어 아래 명령을 편집한 다음, 다음 명령을 입력합니다.

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. HBase 테이블을 만듭니다. 다음 명령을 입력합니다.

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. SQLLine `!tables` 명령을 사용하여 HBase에 모든 테이블을 나열합니다. 다음 명령을 입력합니다.

    ```sqlline
    !tables
    ```

6. 테이블에 값을 삽입합니다. 다음 명령을 입력합니다.

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. 테이블을 쿼리합니다. 다음 명령을 입력합니다.

    ```sql
    SELECT * FROM Company;
    ```

8. 레코드를 삭제합니다. 다음 명령을 입력합니다.

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. 테이블을 삭제합니다. 다음 명령을 입력합니다.

    ```hbase
    DROP TABLE Company;
    ```

10. SQLLine `!quit`명령을 사용하여 SQLLine을 종료합니다. 다음 명령을 입력합니다.

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작을 완료한 후 클러스터를 삭제하는 것이 좋습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

클러스터를 삭제하려면 [브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제](../hdinsight-delete-cluster.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Apache Phoenix를 사용하여 Azure HDInsight에서 HBase 쿼리를 실행하는 방법을 알아보았습니다. Apache Phoenix에 대해 자세히 알아보려면 다음 문서에 자세한 실험이 나와 있습니다.

> [!div class="nextstepaction"]
> [HDInsight의 Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)
