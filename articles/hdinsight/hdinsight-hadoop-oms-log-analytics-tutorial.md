---
title: Azure Monitor Logs를 사용하여 Azure HDInsight 모니터링
description: Azure Monitor Logs를 사용하여 HDInsight 클러스터에서 실행되는 작업을 모니터링하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurepowershell, references_regions
ms.date: 08/02/2021
ms.openlocfilehash: 0627cbb6c590178c5f393cfd519fb4a4504d050f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529966"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor Logs를 사용하여 HDInsight 클러스터 모니터링

Azure Monitor Logs를 사용하여 HDInsight에서 Hadoop 클러스터 작업을 모니터링하는 방법을 알아봅니다. HDInsight 모니터링 솔루션을 추가하는 방법도 알아봅니다.

[Azure Monitor Logs](../azure-monitor/logs/log-query-overview.md)는 클라우드 및 온-프레미스 환경을 모니터링하는 Azure Monitor 서비스입니다. 모니터링은 가용성과 성능을 유지하기 위한 것입니다. 이 서비스는 클라우드, 온-프레미스 환경 및 다른 모니터링 도구의 리소스에서 생성된 데이터를 수집합니다. 데이터는 여러 원본을 망라하는 분석을 제공하는 데 사용됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

#### <a name="new-azure-monitor-experience"></a>[새로운 Azure Monitor 환경](#tab/new)

> [!Important]
> 새로운 Azure Monitor 환경은 미국 동부 및 서유럽에서만 미리 보기 기능으로 사용할 수 있습니다.  
>

## <a name="prerequisites"></a>사전 요구 사항

* Log Analytics 작업 영역. 이 작업 영역은 자체의 데이터 리포지토리, 데이터 원본 및 솔루션을 포함한 고유한 Azure Monitor Logs 환경으로 생각할 수 있습니다. 지침은 [Log Analytics 작업 영역 만들기](../azure-monitor/vm/monitor-virtual-machine.md)를 참조하세요.

* Azure HDInsight 클러스터를 만듭니다. Azure Monitor Logs는 현재 다음 HDInsight 클러스터 유형에서 사용할 수 있습니다.

  * Hadoop은
  * HBase
  * 대화형 쿼리
  * Kafka
  * Spark
  * Storm

  HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.  

* PowerShell을 사용하는 경우 [Az Module](/powershell/azure/)이 필요합니다. 최신 버전이 있는지 확인합니다. 필요한 경우 `Update-Module -Name Az`를 실행합니다.

* Azure CLI를 사용하려 하나 아직 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

> [!NOTE]  
> 새로운 Azure Monitor 환경은 미국 동부 및 서유럽에서만 미리 보기 기능으로 사용할 수 있습니다. 성능 향상을 위해 동일한 지역에 HDInsight 클러스터와 Log Analytics 작업 영역을 모두 배치하는 것이 좋습니다. Azure Monitor Logs를 모든 Azure 지역에서 사용할 수 있는 것은 아닙니다.
>

## <a name="enable-azure-monitor-using-the-portal"></a>포털을 통해 Azure Monitor 사용

이 섹션에서는 Azure Log Analytics 작업 영역을 사용하여 작업, 디버그 로그 등을 모니터링하도록 기존 HDInsight Hadoop 클러스터를 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에서 디렉터리를 선택합니다. 클러스터가 새 포털 페이지에서 열립니다.

2. 왼쪽의 모니터링에서  **통합 모니터** 를 선택합니다. 

3. 기본 보기의  **Azure Monitor for HDInsight 클러스터 통합** 에서  **사용** 을 선택합니다. 

4.  **작업 영역 선택** 드롭다운 목록에서 기존 Log Analytics 작업 영역을 선택합니다. 

5.  **저장** 을 선택합니다. 설정을 저장하는 데 몇 분 정도 걸립니다. 

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-azure-monitor.png" alt-text="HDInsight 클러스터에 모니터링 사용":::

Azure Monitor 사용하지 않도록 설정하려면 이 포털에서 동일한 작업을 수행할 수 있습니다. 

## <a name="enable-azure-monitor-using-azure-powershell"></a>Azure PowerShell을 통해 Azure Monitor 사용

Azure PowerShell Az 모듈 [Enable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/enable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) cmdlet을 통해 Azure Monitor Logs를 사용할 수 있습니다.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster
```

사용하지 않게 설정하려면 [Disable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/disable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) cmdlet을 사용합니다.

```powershell
Disable-AzHDInsightAzureMonitor -ResourceGroupName $resourceGroup `
-ClusterName $cluster
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Azure CLI를 통해 Azure Monitor 사용

Azure CLI [`az hdinsight azure-monitor enable`](/cli/azure/hdinsight/azure-monitor?view=azure-cli-latest&preserve-view=true) 명령을 통해 Azure Monitor Logs를 사용할 수 있습니다.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor show --name $cluster --resource-group $resourceGroup
```

사용하지 않으려면 [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) 명령을 사용합니다.

```azurecli
az hdinsight azure-monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="use-hdinsight-out-of-box-insights-to-monitor-a-single-cluster"></a>HDInsight에서 즉시 사용 가능한 인사이트를 사용하여 단일 클러스터 모니터링

HDInsight에서는 빠르게 인사이트를 얻을 수 있도록 워크로드별 통합 문서를 제공합니다. 이 통합 문서에서는 HDInsight 클러스터에서 중요한 성능 메트릭을 수집하고 가장 일반적인 시나리오에 대한 시각화 및 대시보드를 제공합니다. 즉시 사용 가능한 인사이트에서는 리소스 사용률 및 애플리케이션 상태를 포함하여 단일 HDInsight 클러스터에 대한 완전한 보기를 제공합니다.

사용 가능한 HDInsight 통합 문서:

- HDInsight Spark 통합 문서
- HDInsight Kafka 통합 문서
- HDInsight HBase 통합 문서
- HDInsight Hive/LLAP 통합 문서
- HDInsight Storm 통합 문서

Spark 통합 문서의 스크린샷 :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-spark-workbook.png" alt-text="Spark 통합 문서 스크린샷":::

## <a name="use-at-scale-insights-to-monitor-multiple-clusters"></a>대규모 Insights 사용하여 여러 클러스터 모니터링

Azure Portal에 로그인하고 모니터링을 선택할 수 있습니다. **인사이트** 섹션에서 **인사이트 허브** 를 선택할 수 있습니다. 그런 다음 HDInsight 클러스터를 찾을 수 있습니다.

이 보기에서는 여러 HDInsight 클러스터를 한 위치에서 모니터링할 수 있습니다.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-monitor-insights.png" alt-text="클러스터 모니터 인사이트 스크린샷":::

모니터링하려는 구독 및 HDInsight 클러스터를 선택할 수 있습니다. 
 - **모니터링된 클러스터** 에는 Azure Monitor 통합을 사용으로 설정한 클러스터 수를 표시합니다.
 - **모니터링되지 않은 클러스터** 에는 Azure Monitor 통합을 사용으로 설정하지 않은 클러스터 수를 표시합니다.

각 섹션에서 세부 정보 클러스터 목록을 볼 수 있습니다. 

**모니터링된 클러스터** 의 **개요** 탭에서 클러스터 유형, 중요 경고 및 리소스 사용률을 볼 수 있습니다.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-cluster-alerts.png" alt-text="클러스터 모니터 경고 스크린샷":::

또한 Spark, HBase, Hive, Kafka, Storm을 비롯한 각 워크로드 유형의 클러스터를 볼 수 있습니다.

활성 노드 관리자 수, 실행 중인 애플리케이션 수 등을 포함하여 각 워크로드 유형의 상위 수준 메트릭이 표시됩니다.

:::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/spark-metrics.png" alt-text="클러스터 모니터 spark 메트릭":::

## <a name="configuring-performance-counters"></a>성능 카운터 구성

Azure Monitor는 클러스터의 노드에 대한 성능 메트릭의 수집 및 분석을 지원합니다. 자세한 내용은 [Azure Monitor의 Linux 성능 데이터 원본](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)을 참조하세요.

## <a name="cluster-auditing"></a>클러스터 감사

HDInsight는 다음 유형의 로그를 가져와 Azure Monitor Logs를 통한 클러스터 감사를 지원합니다.

* `log_gateway_audit_CL` - 이 테이블은 성공 및 실패한 로그인 시도를 표시하는 클러스터 게이트웨이 노드 감사 로그를 제공합니다.
* `log_auth_CL` - 이 테이블은 성공 및 실패한 로그인 시도를 포함한 SSH 로그를 제공합니다.
* `log_ambari_audit_CL` - 이 테이블은 Ambari의 감사 로그를 제공합니다.
* `log_ranger_audti_CL` - 이 테이블은 ESP 클러스터에서 Apache 레인저의 감사 로그를 제공합니다.


#### <a name="classic-azure-monitor-experience"></a>[클래식 Azure Monitor 환경](#tab/previous)

## <a name="prerequisites"></a>사전 요구 사항

* Log Analytics 작업 영역. 이 작업 영역은 자체의 데이터 리포지토리, 데이터 원본 및 솔루션을 포함한 고유한 Azure Monitor Logs 환경으로 생각할 수 있습니다. 지침은 [Log Analytics 작업 영역 만들기](../azure-monitor/vm/monitor-virtual-machine.md)를 참조하세요.

* Azure HDInsight 클러스터를 만듭니다. Azure Monitor Logs는 현재 다음 HDInsight 클러스터 유형에서 사용할 수 있습니다.

  * Hadoop은
  * HBase
  * 대화형 쿼리
  * Kafka
  * Spark
  * Storm

  HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.  

* PowerShell을 사용하는 경우 [Az Module](/powershell/azure/)이 필요합니다. 최신 버전이 있는지 확인합니다. 필요한 경우 `Update-Module -Name Az`를 실행합니다.

* Azure CLI를 사용하려 하나 아직 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

> [!NOTE]  
> 성능 향상을 위해 동일한 지역에 HDInsight 클러스터와 Log Analytics 작업 영역을 모두 배치하는 것이 좋습니다. Azure Monitor Logs를 모든 Azure 지역에서 사용할 수 있는 것은 아닙니다.

## <a name="enable-azure-monitor-using-the-portal"></a>포털을 통해 Azure Monitor 사용

이 섹션에서는 Azure Log Analytics 작업 영역을 사용하여 작업, 디버그 로그 등을 모니터링하도록 기존 HDInsight Hadoop 클러스터를 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에서 디렉터리를 선택합니다. 클러스터가 새 포털 페이지에서 열립니다.

1. 왼쪽 창 메뉴의 **모니터링** 에서 **Azure Monitor** 를 선택합니다.

1. 기본 보기의 **Azure Monitor 통합** 에서 **사용** 을 선택합니다.

1. **작업 영역 선택** 드롭다운 목록에서 기존 Log Analytics 작업 영역을 선택합니다.

1. **저장** 을 선택합니다.  설정을 저장하는 데 몇 분 정도 걸립니다.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="HDInsight 클러스터에 모니터링 사용":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Azure PowerShell을 통해 Azure Monitor 사용

Azure PowerShell Az module [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet을 통해 Azure Monitor Logs를 사용할 수 있습니다.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

사용하지 않으려면 [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) cmdlet을 사용합니다.

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Azure CLI를 통해 Azure Monitor 사용

Azure CLI `[az hdinsight monitor enable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable) 명령을 사용하여 Azure Monitor 로그를 사용하도록 설정할 수 있습니다.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

사용하지 않으려면 [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) 명령을 사용합니다.

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight 클러스터 관리 솔루션 설치

HDInsight는 Azure Monitor Logs에 추가할 수 있는 클러스터 특정 관리 솔루션을 제공합니다. [관리 솔루션](../azure-monitor/insights/solutions.md)은 Azure Monitor Logs에 기능을 추가하여 추가 데이터 및 분석 도구를 제공합니다. 이러한 솔루션은 HDInsight 클러스터에서 중요한 성능 메트릭을 수집합니다. 또한 메트릭 검색 도구를 제공합니다. 또한 HDInsight에서 지원되는 대부분의 클러스터 유형에 대해 시각화 및 대시보드를 제공합니다. 솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다.

사용 가능한 HDInsight 솔루션:

* HDInsight Hadoop 모니터링
* HDInsight HBase 모니터링
* HDInsight 대화형 쿼리 모니터링
* HDInsight Kafka 모니터링
* HDInsight Spark 모니터링
* HDInsight Storm 모니터링

관리 솔루션 지침은 [Azure의 관리 솔루션](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)을 참조하세요. 실험하려면 HDInsight Hadoop 모니터링 솔루션을 설치합니다. 완료되면 **요약** 에 **HDInsightHadoop** 타일이 나열됩니다. **HDInsightHadoop** 타일을 선택합니다. HDInsightHadoop 솔루션은 다음과 같습니다.

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="HDInsight 모니터링 솔루션 보기":::

클러스터는 새로운 클러스터이므로 보고서에 작업이 표시되지 않습니다.

## <a name="configuring-performance-counters"></a>성능 카운터 구성

Azure Monitor는 클러스터의 노드에 대한 성능 메트릭의 수집 및 분석을 지원합니다. 자세한 내용은 [Azure Monitor의 Linux 성능 데이터 원본](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)을 참조하세요.

## <a name="cluster-auditing"></a>클러스터 감사

HDInsight는 다음 유형의 로그를 가져와 Azure Monitor Logs를 통한 클러스터 감사를 지원합니다.

* `log_gateway_audit_CL` - 이 테이블은 성공 및 실패한 로그인 시도를 표시하는 클러스터 게이트웨이 노드 감사 로그를 제공합니다.
* `log_auth_CL` - 이 테이블은 성공 및 실패한 로그인 시도를 포함한 SSH 로그를 제공합니다.
* `log_ambari_audit_CL` - 이 테이블은 Ambari의 감사 로그를 제공합니다.
* `log_ranger_audti_CL` - 이 테이블은 ESP 클러스터에서 Apache 레인저의 감사 로그를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor Logs를 쿼리하여 HDInsight 클러스터 모니터링](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Apache Ambari 및 Azure Monitor Logs를 사용하여 클러스터 가용성 모니터링하는 방법](./hdinsight-cluster-availability.md)