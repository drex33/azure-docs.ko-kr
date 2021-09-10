---
title: 수동으로 클러스터 스케일링 - Azure HDInsight
description: Azure HDInsight에서 워크로드와 일치하도록 탄력적으로 Apache Hadoop 클러스터를 수동으로 스케일링
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 86759dacfc4412fff1d100f5f6808aa7269575e1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539226"
---
# <a name="manually-scale-azure-hdinsight-clusters"></a>수동으로 Azure HDInsight 클러스터 스케일링

HDInsight는 클러스터의 작업자 노드 수를 증가 및 감소하는 옵션을 제공하여 탄력성을 보장합니다. 이러한 탄력성을 통해 몇 시간 후 또는 주말에 클러스터를 축소할 수 있습니다. 그리고 최고 비즈니스 수요 동안 확장합니다.

클러스터에 적절한 리소스가 있도록 주기적 일괄 처리를 수행하기 전에 클러스터를 확장합니다.  처리가 완료된 후 사용량이 다시 줄어들면, HDInsight 클러스터를 더 적은 수의 작업자 노드로 축소할 수 있습니다.

아래에 설명된 방법 중 하나를 사용하여 클러스터를 수동으로 확장할 수 있습니다. [자동 크기 조정](hdinsight-autoscale-clusters.md) 옵션을 사용하여 특정 메트릭에 대한 응답으로 자동으로 확장 및 축소할 수도 있습니다.

> [!NOTE]  
> HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다.

## <a name="utilities-to-scale-clusters"></a>클러스터 크기 조정 유틸리티

Microsoft는 클러스터 크기를 조정하는 다음과 같은 유틸리티를 제공합니다.

|유틸리티 | 설명|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az_hdinsight_resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure 클래식 CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure Portal](https://portal.azure.com)|HDInsight 클러스터 창을 열고 왼쪽 메뉴에서 **클러스터 크기** 를 선택한 다음, 클러스터 크기 창에서 작업자 노드 수를 입력하고 저장을 선택합니다.|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure Portal 클러스터 규모 옵션":::

이러한 방법 중 하나를 사용하여 몇 분 만에 HDInsight 클러스터를 확장 또는 축소할 수 있습니다.

> [!IMPORTANT]  
> * Azure 클래식 CLI는 사용되지 않으므로 클래식 배포 모델과 함께여야만 사용됩니다. 다른 모든 배포에는 [Azure CLI](/cli/azure/)를 사용합니다.
> * PowerShell AzureRM 모듈은 더 이상 사용되지 않습니다.  가능하면 [Az module](/powershell/azure/new-azureps-module-az)을 사용하세요.

## <a name="impact-of-scaling-operations"></a>크기 조정 작업의 영향

실행 중인 HDInsight 클러스터(스케일 업)에 노드를 **추가** 할 경우, 작업에는 영향을 미치지 않습니다. 크기 조정 프로세스가 실행되는 동안 새 작업을 안전하게 제출할 수 있습니다. 크기 조정 작업이 실패하면 클러스터는 기능 상태로 유지됩니다.

노드를 **제거** 하는 경우 (스케일 다운) 크기 조정 작업이 완료되면 보류 중이거나 실행 중인 작업은 실패합니다. 이 오류는 크기 조정 프로세스 중 일부 서비스가 다시 시작되기 때문에 발생합니다. 수동 크기 조정 작업을 수행하는 동안 클러스터가 안전 모드에서 중단될 수 있습니다.

데이터 노드 수 변경에 따른 영향은 다음과 같이 HDInsight에서 지원하는 각 클러스터 유형에 따라 다릅니다.

* Apache Hadoop

    모든 작업에 영향을 주지 않고 실행 중인 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 스케일링 작업의 실패는 정상적으로 처리됩니다. 클러스터는 항상 기능 상태로 남아있습니다.

    데이터 노드가 적은 Hadoop 클러스터가 스케일 다운되면 일부 서비스가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.

* Apache HBase

    HBase 클러스터가 실행 중인 동안 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 지역 서버를 수동으로 분산할 수 있습니다. 클러스터 헤드 노드에 로그인하고 다음 명령을 실행합니다.

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase 셸을 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache HBase 예제 시작](hbase/apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

* Apache Storm

    Storm이 실행 중인 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 하지만 크기 조정 작업이 성공적으로 완료되면 토폴로지 균형을 다시 조정해야 합니다. 균형을 다시 조정하면 토폴로지를 새 클러스터의 노드 수에 따라 [병렬 처리 설정](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)을 다시 조정할 수 있습니다. 실행 중인 토폴로지의 균형을 다시 조정하려면 다음 옵션 중 하나를 사용합니다.

  * Storm 웹 UI

    Storm UI를 사용하여 토폴로지 균형을 다시 맞추려면 다음 단계를 사용합니다.

    1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/stormui`을 열고, 여기서 `CLUSTERNAME`는 Storm 클러스터의 이름입니다. 메시지가 표시되면 클러스터를 만들 때 지정한 HDInsight 클러스터 관리자(관리자) 이름 및 암호를 입력합니다.

    1. 균형을 다시 맞추려는 토폴로지를 선택한 다음 **균형 다시 맞추기** 단추를 선택합니다. 균형 재조정 작업이 수행되기 전에 지연 시간을 입력합니다.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HDInsight Storm 규모 균형 재조정":::

  * 명령줄 인터페이스(CLI) 도구

    서버에 연결하고 다음 명령을 사용하여 토폴로지 균형을 다시 맞춥니다.

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    매개 변수를 지정하여 원래 토폴로지로 제공된 병렬 처리 힌트를 재정의할 수도 있습니다. 예를 들어 아래의 코드는 `mytopology` 토폴로지를 5개 작업자 프로세스, 파란색 spout 구성 요소를 3개 실행자, 그리고 노란색 bolt 구성 요소를 10개 실행자로 다시 구성합니다.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    크기 조정 작업 후 파티션 복제본의 균형을 다시 조정해야 합니다. 자세한 내용은 [HDInsight에서 Apache Kafka를 사용한 데이터의 고가용성](./kafka/apache-kafka-high-availability.md) 문서를 참조하세요.

* Apache Hive LLAP

    `N` 작업자 노드로 크기를 조정한 후, HDInsight는 다음 구성을 자동으로 설정하고 Hive를 다시 시작합니다.

  * 총 동시 발생 쿼리 수: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Hive의 LLAP에서 사용되는 노드 수: `num_llap_nodes  = N`
  * Hive LLAP daemon을 실행하는 노드 수: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>클러스터를 안전하게 축소하는 방법

### <a name="scale-down-a-cluster-with-running-jobs"></a>실행 중인 작업으로 클러스터 규모 축소

규모 축소 작업 중에 실행 중인 작업이 실패하는 것을 방지하기 위해 다음 세 가지 작업을 시도할 수 있습니다.

1. 클러스터를 축소하기 전에 작업이 완료될 때까지 기다립니다.
1. 수동으로 작업을 종료합니다.
1. 크기 조정 작업이 완료된 후 작업을 다시 전송합니다.

보류 중이거나 실행 중인 작업 목록을 보려면 YARN **리소스 관리자 UI** 를 사용하여 다음 단계를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 디렉터리를 선택합니다.  클러스터가 새 포털 페이지에서 열립니다.
2. 메인 보기에서 **클러스터 대시보드**  >  **Ambari home** 으로 이동합니다. 클러스터 자격 증명을 입력합니다.
3. Ambari UI에서 왼쪽 메뉴에 있는 서비스 목록에서 **YARN** 를 선택합니다.  
4. YARN 페이지에서 **빠른 링크** 를 선택하고 활성 헤드 노드 위로 마우스로 가져간 후 **리소스 관리자 UI** 를 클릭합니다.

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Apache Ambari 빠른 링크 리소스 관리자 UI":::

`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`에서 리소스 관리자 UI에 직접 액세스할 수 있습니다.

현재 상태와 함께 작업 목록이 표시됩니다. 스크린샷에는 현재 실행 중인 하나의 작업만 나와 있습니다.

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="리소스 관리자 UI 애플리케이션":::

실행 중인 해당 애플리케이션을 수동으로 종료하려면 SSH 셸에서 다음 명령을 실행합니다.

```bash
yarn application -kill <application_id>
```

예를 들면 다음과 같습니다.

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>안전 모드에서 중단

클러스터를 축소하는 경우 HDInsight는 Apache Ambari 관리 인터페이스를 사용하여 먼저 추가 작업자 노드의 서비스를 해제합니다. 노드가 HDFS 블록을 다른 온라인 작업자 노드에 복제합니다. 그 후에 HDInsight는 클러스터를 안전하게 확장합니다. HDFS는 크기 조정 작업을 수행하는 동안 안전 모드로 전환됩니다. 크기 조정이 완료되면 HDFS가 나오게 되어 있습니다. 그러나 일부 경우에는 복제 중인 파일 블록 때문에 크기 조정 작업을 수행하는 동안 HDFS는 안전 모드에서 중단됩니다.

기본적으로 HDFS는 사용할 수 있는 각 파일 블록의 복사본 수를 제어하는 `dfs.replication`1의 설정으로 구성됩니다. 파일 블록의 각 복사본은 클러스터의 다른 노드에 저장됩니다.

예상되는 블록 복사본 수를 사용할 수 없는 경우, HDFS는 safe 모드로 전환되고 Ambari에서 경고를 생성합니다. HDFS는 크기 조정 작업을 위해 안전 모드로 전환될 수도 있습니다. 필요한 수의 노드가 복제에 대해 검색되지 않는 경우 클러스터가 안전 모드에서 중단될 수 있습니다.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>안전 모드가 켜지는 오류 예제

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

클러스터 크기가 조정될 시간이 가까워질 때 `/var/log/hadoop/hdfs/` 폴더에서 이름 노드 로그를 검토하여 안전 모드가 시작되었는지 확인할 수 있습니다. 로그 파일의 이름은 `Hadoop-hdfs-namenode-<active-headnode-name>.*`입니다.

근본 원인은 Hive가 쿼리를 실행하는 동안 HDFS에서 임시 파일을 사용하는 것입니다. HDFS가 안전 모드를 시작하면, Hive는 HDFS에 쓸 수 없으므로 쿼리를 실행할 수 없습니다. HDFS의 임시 파일은 개별 작업자 노드 VM에 탑재된 로컬 드라이브에 있습니다. 파일은 최소 3개의 복제본으로 다른 작업자 노드 간에 복제됩니다.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>HDInsight가 안전 모드에서 중단되는 것을 방지하는 방법

다음과 같은 여러 가지 방법으로 HDInsight가 안전 모드를 유지하지 않도록 할 수 있습니다.

* HDInsight를 축소하기 전에 모든 Hive 작업을 중지합니다. 또는 실행 중인 Hive 작업과 충돌하지 않도록 축소 프로세스를 예약합니다.
* 축소하기 전에 HDFS에서 Hive의 스크래치 `tmp` 디렉터리 파일을 수동으로 정리합니다.
* HDInsight를 최소 3개의 작업자 노드로만 축소합니다. 작업자 노드 1개만큼 줄이지는 마세요.
* 필요한 경우 안전 모드를 종료하는 명령을 실행합니다.

다음 섹션에서는 이러한 옵션을 설명합니다.

#### <a name="stop-all-hive-jobs"></a>모든 Hive 작업 중지

하나의 작업자 노드를 축소하기 전에 모든 Hive 작업을 중지합니다. 워크로드가 예약되면 Hive 작업이 완료된 후에 축소를 실행합니다.

크기를 조정하기 전에 Hive 작업을 중지하면 tmp 폴더 (있는 경우)의 스크래치 파일 수를 최소화 하는 데 도움이 됩니다.

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive의 스크래치 파일을 수동으로 정리

Hive가 임시 파일을 남겨 두면 안전 모드를 피하기 위해 축소 전에 해당 파일을 수동으로 정리할 수 있습니다.

1. `hive.exec.scratchdir` 구성 속성을 확인하여 Hive임시 파일에 사용 중인 위치를 확인하십시오. 이 매개 변수는 `/etc/hive/conf/hive-site.xml` 내에 설정됩니다.

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive 서비스를 중지하고 모든 쿼리 및 작업이 완료되었는지 확인합니다.

1. 위에서 찾은 스크래치 디렉터리의 내용을 나열하여 `hdfs://mycluster/tmp/hive/` 파일이 포함되어 있는지 확인합니다.

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    다음은 파일이 있는 경우의 샘플 출력입니다.

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. 이러한 파일에 대해 Hive가 완료된 것이 확인되면 제거해도 됩니다. Yarn Resource Manager UI 페이지에서 확인하여 Hive에 실행 중인 쿼리가 없는지 확인합니다.

    HDFS에서 파일을 제거하는 명령줄 예제는 다음과 같습니다.

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>3개 이상의 작업자 노드로 HDInsight 확장

3개 이상의 작업자 노드로 축소하는 경우 클러스터가 안전 모드에서 중단되면 최소한 세 개의 작업자 노드로 유지합니다.

작업자 노드가 세 개인 경우 하나의 작업자 노드만으로 축소하는 것보다 비용이 많이 듭니다. 그러나 이 작업을 수행하면 클러스터가 안전 모드에서 중단되지 않습니다.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>HDInsight를 하나의 작업자 노드로 규모 축소

클러스터가 한 노드로 축소되는 경우에도 작업자 노드 0은 계속 남아있습니다. 작업자 노드 0은 서비스 해제될 수 없습니다.

#### <a name="run-the-command-to-leave-safe-mode"></a>안전 모드를 종료하는 명령 실행

마지막 옵션은 안전 모드 종료 명령을 실행하는 것입니다. 복제 중인 Hive 파일 때문에 HDFS가 안전 모드를 시작한 경우 다음 명령을 실행하여 안전 모드를 종료합니다.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase 클러스터 축소

지역 서버는 몇 분 안에 크기 조정 작업을 완료한 후 자동으로 균형을 맞춥니다. 지역 서버를 수동으로 조정하려면 다음 단계를 완료합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. HBase 셸을 시작합니다.

    ```bash
    hbase shell
    ```

3. 다음 명령을 사용하여 지역 서버의 부하를 수동으로 분산합니다.

    ```bash
    balancer
    ```

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 클러스터 자동 크기 조정](hdinsight-autoscale-clusters.md)

HDInsight 클러스터 크기 조정에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Azure 명령줄 인터페이스를 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-command-line.md#scale-clusters)
