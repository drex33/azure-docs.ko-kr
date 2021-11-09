---
title: Azure HDInsight Apache Ambari 메트릭 수집기 문제
description: Azure HDInsight Apache Ambari 메트릭 수집기 문제 해결
ms.service: hdinsight
ms.author: linjzhu
author: jacky-hdi
ms.topic: troubleshooting
ms.date: 11/03/2021
ms.openlocfilehash: 6fbfc4f47cb14966a7d03d9ef378bb03f519183c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064472"
---
# <a name="apache-ambari-metrics-collector-issues-in-azure-hdinsight"></a>Azure HDInsight Apache Ambari 메트릭 수집기 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="scenario-outofmemoryerror-or-unresponsive-apache-ambari-metrics-collector"></a>시나리오: OutOfMemoryError 또는 응답하지 않는 Apache Ambari 메트릭 수집기

### <a name="issue"></a>문제

* Ambari UI에서 중요한 **"메트릭 수집기 프로세스"** 경고를 수신하고 유사한 메시지 아래에 표시할 수 있습니다.
    `Connection failed: timed out to <headnode fqdn>:6188`
* Ambari 메트릭 수집기는 헤드 노드에서 자주 다시 시작될 수 있습니다.
* 일부 Apache Ambari 메트릭이 표시되지 않을 수 있습니다. 예를 들어 **NAMENODE는** **활성/대기** 상태 대신 **시작을** 표시합니다. 


### <a name="cause"></a>원인

이러한 문제의 가능한 원인은 다음과 같은 시나리오입니다.

#### <a name="an-out-of-memory-exception-happens-frequently"></a>메모리 부족 예외가 자주 발생합니다.

Apache Ambari 메트릭 수집기 로그를 `/var/log/ambari-metrics-collector/ambari-metrics-collector.log*` 확인합니다.

```
19:59:45,457 ERROR [325874797@qtp-2095573052-22] log:87 - handle failed
java.lang.OutOfMemoryError: Java heap space

19:59:45,457 FATAL [MdsLoggerSenderThread] YarnUncaughtExceptionHandler:51 - Thread Thread[MdsLoggerSenderThread,5,main] threw an Error.  Shutting down now...
java.lang.OutOfMemoryError: Java heap space
```

#### <a name="busy-garbage-collection"></a>사용 중인 가비지 수집

1. Apache Ambari 메트릭 수집기는 hbase-ams 로그의 **6188에서** 수신 대기하지 않습니다. `/var/log/ambari-metrics-collector/hbase-ams-master-hn*.log`

   ```
   2021-04-13 05:57:37,546 INFO  [timeline] timeline.HadoopTimelineMetricsSink: No live collector to send metrics to. Metrics to be sent will be discarded. This message will be skipped for the next 20 times.
   ```
   
2. Apache Ambari 메트릭 수집기 pid를 얻고 GC 성능 확인

   ```
   ps -fu ams | grep 'org.apache.ambari.metrics.AMSApplicationServer'
   ```
       
3. 를 사용하여 가비지 수집 상태를 `jstat -gcutil <pid> 1000 100` 확인합니다. **FGCT가** 몇 초 안에 많이 증가하는 경우 Apache Ambari 메트릭 수집기가 전체 GC에서 사용 중이며 다른 요청을 처리할 수 없음을 나타냅니다.

### <a name="resolution"></a>해결 방법

이러한 문제를 방지하려면 다음 옵션 중 하나를 사용하는 것이 좋습니다.

1. **Ambari** Ambari 메트릭 수집기 구성 메트릭 수집기 힙 크기에서 Apache  >  **Ambari**  >    >  **메트릭 수집기의 힙** 메모리 늘리기

   :::image type="content" source="./media/apache-ambari-troubleshoot-ams-issues/editing-ams-configuration-ambari.png" alt-text="Ambari 메트릭 서비스 구성 속성을 편집하는 스크린샷." border="true":::

2. 다음 단계에 따라 AMS(Ambari 메트릭 서비스) 데이터를 정리합니다.

   > [!NOTE]
   > AMS 데이터를 정리하는 경우 사용 가능한 모든 기록 AMS 데이터가 제거됩니다. 기록이 필요한 경우 최상의 옵션이 아닐 수 있습니다.

   1.  Ambari 포털에 로그인  
    1.  유지 관리하도록 AMS 설정  
    2.  Ambari에서 AMS 중지  
    3.  **AMS 구성** 화면에서 다음을 식별합니다.  
                1.  `hbase.rootdir` (기본값은 `file:///mnt/data/ambari-metrics-collector/hbase` )  
                2.  `hbase.tmp.dir` (기본값은 `/var/lib/ambari-metrics-collector/hbase-tmp` )  
   2. Apache Ambari 메트릭 수집기 존재 하는 헤드 노드에 SSH합니다. 슈퍼사용자:
    1. 의 콘텐츠를 **백업하고** 제거하여 AMS Zookeeper 데이터를 제거합니다.  `'hbase.tmp.dir'/zookeeper`
    2. `<hbase.tmp.dir>/phoenix-spool` 폴더에서 Phoenix 스풀 파일을 제거합니다. 
    3. ***(처음에는 이 단계를 건너뛰고 AMS를 다시 시작하여 문제가 해결되었는지 확인하는 것이 좋습니다. AMS가 여전히 실패하는 경우 이 단계를 시도합니다.)***  
            AMS 데이터가 위에서 식별된 `hbase.rootdir`에 저장됩니다. 일반 OS 명령을 사용하여 파일을 백업하고 제거합니다. 예:     
            `tar czf /mnt/backupof-ambari-metrics-collector-hbase-$(date +%Y%m%d-%H%M%S).tar.gz /mnt/data/ambari-metrics-collector/hbase`  
   3.  Ambari를 사용하여 AMS를 다시 시작합니다.


## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
