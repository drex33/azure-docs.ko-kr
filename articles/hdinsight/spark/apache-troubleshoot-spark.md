---
title: Azure HDInsight의 Apache Spark 문제 해결
description: Apache Spark 및 Azure HDInsight 작업에 대한 일반적인 질문에 답합니다.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 6841a6965646afbf4192c502c0a9eba260531f5e
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369077"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Azure HDInsight를 사용하여 Apache Spark 문제 해결

[Apache Ambari](https://ambari.apache.org/)에서 Apache Spark 페이로드 작업 시 주요 issue 및 해결 방법을 알아봅니다.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>클러스터에서 Apache Ambari를 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 해야 하나요?

Spark 구성 값을 튜닝하여 Apache Spark 애플리케이션 `OutofMemoryError` 예외를 방지할 수 있습니다. 다음 단계는 Azure HDInsight의 기본 Spark 구성 값을 보여 줍니다.

1. `https://CLUSTERNAME.azurehdidnsight.net`에서 클러스터 자격 증명을 사용하여 Ambari에 로그인합니다. 초기 화면에 대시보드 개요가 표시됩니다. HDInsight 3.6과 4.0 간에는 약간의 외관상 차이가 있습니다.

1. **Spark2** > **Configs** 로 이동합니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Configs 탭 선택" border="true":::

1. 구성 목록에서 **Custom-spark2-defaults** 를 선택하고 확장합니다.

1. **spark.executor.memory** 와 같이 조정해야 하는 값 설정을 찾습니다. 이 경우 **9728m** 의 값이 너무 높습니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="custom-spark-defaults 선택" border="true":::

1. 값을 권장 설정으로 지정합니다. 이 설정에는 **2048m** 값이 권장됩니다.

1. 값을 저장하고 구성을 저장합니다. **저장** 을 선택합니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="값을 2048m으로 변경" border="true":::

    구성 변경 내용에 대한 메모를 작성하고 **저장** 을 선택합니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="변경 내용에 대한 메모 입력" border="true":::

    주의할 필요가 있는 구성이면 알림이 표시됩니다. 항목을 확인한 후 **계속 진행** 을 선택합니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="계속 진행 선택" border="true":::

1. 구성이 저장될 때마다 서비스를 다시 시작하라는 메시지가 표시됩니다. **다시 시작** 을 선택합니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="다시 시작을 선택합니다" border="true":::

    다시 시작을 확인합니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="다시 시작 확인 선택" border="true":::

    실행 중인 프로세스를 검토할 수 있습니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="실행 중인 프로세스 검토" border="true":::

1. 구성을 추가할 수 있습니다. 구성 목록에서 **Custom-spark2-defaults** 를 선택하고 **속성 추가** 를 선택합니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="속성 추가 선택" border="true":::

1. 새 속성을 정의합니다. 데이터 형식과 같은 특정 설정에 대한 대화 상자를 사용하여 단일 속성을 정의할 수 있습니다. 또는 줄당 하나의 정의를 사용하여 여러 속성을 정의할 수 있습니다.

    이 예제에서 **spark.driver.memory** 속성의 값은 **4g** 로 정의되었습니다.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="새 속성 정의" border="true":::

1. 6단계와 7단계에서 설명한 대로 구성을 저장하고 서비스를 다시 시작합니다.

이러한 변경 내용은 클러스터 전체를 대상으로 하지만 Spark 작업을 제출할 때 재정의할 수 있습니다.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>클러스터에서 Jupyter Notebook을 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 할까요?

Jupyter Notebook의 첫 번째 셀에서 **%%configure** 지시문 뒤에 유효한 JSON 형식의 Spark 구성을 지정합니다. 필요에 따라 실제 값을 변경합니다.

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="구성 추가" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>클러스터에서 Apache Livy를 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 해야 하나요?

cURL 같은 REST 클라이언트를 사용하여 Livy로 Spark 애플리케이션을 제출합니다. 다음과 유사한 명령을 사용합니다. 필요에 따라 실제 값을 변경합니다.

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>클러스터에서 spark-submit을 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 해야 하나요?

다음과 비슷한 명령을 사용하여 spark-shell을 시작합니다. 필요에 따라 구성의 실제 값을 변경합니다.

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>추가 자료

[HDInsight 클러스터에서 Apache Spark 작업 제출](/archive/blogs/azuredatalake/spark-job-submission-on-hdinsight-101)

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Spark 메모리 관리 개요](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [HDInsight 클러스터에서 Spark 애플리케이션 디버깅](/archive/blogs/azuredatalake/spark-debugging-101).

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
