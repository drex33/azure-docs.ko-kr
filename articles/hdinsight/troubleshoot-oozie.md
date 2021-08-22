---
title: Azure HDInsight의 Apache Oozie 문제 해결
description: Azure HDInsight의 특정 Apache Oozie 오류 문제를 해결합니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 4dfc85b566f464a6c0fd49f7859f2c62aad1d8c4
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283882"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Azure HDInsight의 Apache Oozie 문제 해결

Apache Oozie UI를 사용하여 Oozie 로그를 볼 수 있습니다. Oozie UI에는 워크플로에서 시작된 MapReduce 작업에 대한 JobTracker 로그의 링크도 포함되어 있습니다. 문제 해결 패턴은 다음과 같습니다.

1. Oozie 웹 UI에서 작업을 확인합니다.

2. 특정 동작에 대한 오류 또는 실패가 있는 경우 해당 동작을 선택하여 **오류 메시지** 필드에 오류에 대한 자세한 정보가 제공되는지 확인합니다.

3. 사용 가능한 경우 동작의 URL을 사용하여 해당 동작에 대한 자세한 정보(예: JobTracker 로그)를 확인할 수 있습니다.

다음은 발생할 수 있는 특정 오류 및 해결 방법입니다.

## <a name="ja009-cant-initialize-cluster"></a>JA009: 클러스터를 초기화할 수 없음

### <a name="issue"></a>문제

작업 상태가 **일시 중단됨** 으로 변경되었습니다. 작업에 대한 세부 정보에 `RunHiveScript` 상태가 **START_MANUAL** 로 표시됩니다. 동작을 선택하면 다음과 같은 오류 메시지가 표시됩니다.

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>원인

**job.xml** 파일에 사용된 Azure Blob Storage 주소에 스토리지 컨테이너 또는 스토리지 계정 이름이 포함되어 있지 않습니다. Blob Storage 주소 형식은 `wasbs://containername@storageaccountname.blob.core.windows.net`이어야 합니다.

### <a name="resolution"></a>해결 방법

해당 작업이 사용하는 Blob Storage 주소를 변경합니다.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie에서 &lt;USER&gt;를 가장할 수 없음

### <a name="issue"></a>문제

작업 상태가 **일시 중단됨** 으로 변경되었습니다. 작업에 대한 세부 정보에 `RunHiveScript` 상태가 **START_MANUAL** 로 표시됩니다. 동작을 선택하면 다음 오류 메시지가 표시됩니다.

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>원인

현재 권한 설정에서 Oozie가 지정된 사용자 계정을 가장하도록 허용하지 않습니다.

### <a name="resolution"></a>해결 방법

Oozie에서 **`users`** 그룹의 사용자를 가장할 수 있습니다. `groups USERNAME` 을 사용하여 사용자 계정이 멤버로 속해 있는 그룹을 확인합니다. 사용자가 **`users`** 그룹의 멤버가 아닌 경우 다음 명령을 사용하여 사용자를 그룹에 추가합니다.

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> HDInsight에서 사용자가 그룹에 추가된 것을 인식하는 데 몇 분 정도 걸릴 수 있습니다.

---

## <a name="launcher-error-sqoop"></a>시작 관리자 오류(Sqoop)

### <a name="issue"></a>문제

작업 상태가 **중단됨** 으로 변경되었습니다. 작업에 대한 세부 정보에 `RunSqoopExport` 상태가 **ERROR** 로 표시됩니다. 동작을 선택하면 다음 오류 메시지가 표시됩니다.

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>원인

Sqoop가 데이터베이스에 액세스하는 데 필요한 데이터베이스 드라이버를 로드할 수 없습니다.

### <a name="resolution"></a>해결 방법

Oozie 작업에서 Sqoop를 사용하는 경우 작업이 사용하는 다른 리소스(예: workflow.xml)와 함께 데이터베이스 드라이버를 포함해야 합니다. 또한 workflow.xml의 `<sqoop>...</sqoop>` 섹션에서 데이터베이스 드라이버가 포함된 보관 파일을 참조합니다.

예를 들어 [Hadoop Oozie 워크플로 사용](hdinsight-use-oozie-linux-mac.md)의 작업 예제를 보려면 다음 단계를 사용합니다.

1. `mssql-jdbc-7.0.0.jre8.jar` 파일을 **/tutorials/useoozie** 디렉터리에 복사합니다.

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. `workflow.xml`을 수정하여 `</sqoop>` 위의 새 줄에 다음 XML을 추가합니다.

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]