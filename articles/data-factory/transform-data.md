---
title: 데이터 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Hadoop, ML 스튜디오(클래식) 또는 Azure Data Lake Analytics를 사용하여 Azure Data Factory나 Azure Synapse Analytics에서 데이터를 변환하거나 처리합니다.
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: b212afb91b712d47380d93b7a960c9fdb0dfc457
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821269"
---
# <a name="transform-data-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics에서 데이터 변환

> [!div class="op_single_selector"]
> * [매핑 데이터 흐름](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight 스트리밍](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [ML Studio(클래식)](transform-data-using-machine-learning.md) 
> * [저장 프로시저](transform-data-using-stored-procedure.md)
> * [데이터 레이크 분석 U-SQL](transform-data-using-data-lake-analytics.md)
> * [Azure Synapse notebook](../synapse-analytics/synapse-notebook-activity.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET 사용자 지정](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>개요
이 문서에서는 원시 데이터를 대규모로 예측하고 인사이트로 변환하고 처리하는 데 사용할 수 있는 Azure Data Factory 및 Synapse 파이프라인의 데이터 변환 작업을 설명합니다. 변환 작업은 Azure Databricks 또는 Azure HDInsight 클러스터와 같은 컴퓨팅 환경에서 실행됩니다. 각 변환 작업에 대한 자세한 정보가 있는 문서에 대한 링크를 제공합니다.

이 서비스는 개별적으로 또는 다른 작업과 연계하여 [파이프라인](concepts-pipelines-activities.md)에 추가할 수 있는 다음 데이터 변환 작업을 지원합니다.

## <a name="transform-natively-in-azure-data-factory-and-azure-synapse-analytics-with-data-flows"></a>데이터 흐름을 사용하여 Azure Data Factory 및 Azure Synapse Analytics에서 기본적으로 변환

### <a name="mapping-data-flows"></a>데이터 흐름 매핑

매핑 데이터 흐름은 Azure Data Factory와 Azure Synapse에서 시각적으로 디자인된 데이터 변환입니다. 데이터 흐름을 통해 데이터 엔지니어는 코드를 작성하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있습니다. 결과 데이터 흐름은 스케일 아웃된 Spark 클러스터를 사용하는 파이프라인 내에서 작업으로 실행됩니다. 데이터 흐름 작업은 서비스 내에서 기존 일정, 제어, 흐름, 모니터링 기능을 통해 운영화될 수 있습니다. 자세한 내용은 [매핑 데이터 흐름](concepts-data-flow-overview.md)을 참조하세요.

### <a name="data-wrangling"></a>데이터 랭글링

Azure Data Factory의 Power Query는 클라우드 규모의 데이터 랭글링을 활성화하여 클라우드 규모의 코드 없는 데이터 준비를 반복적으로 수행할 수 있습니다. 데이터 랭글링은 [Power Query Online](/power-query/)과 통합되며 Spark 실행을 통해 클라우드 규모의 데이터 랭글링에 Power Query M 함수를 사용할 수 있습니다. 자세한 내용은 [Azure Data Factory의 데이터 랭글링](wrangling-overview.md)을 참조하세요.

> [!NOTE]
> 파워 쿼리는 현재 Azure Synapse가 아닌 Azure Data Factory에서만 지원됩니다.  각 서비스에서 지원되는 특정 기능 목록은 [Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 사용 가능한 기능](../synapse-analytics/data-integration/concepts-data-factory-differences.md)을 참조하세요.

## <a name="external-transformations"></a>외부 변환

필요에 따라 변환을 직접 코딩하고 외부 컴퓨팅 환경을 직접 관리할 수 있습니다.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive 작업
파이프라인의 HDInsight Hive 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 Hive 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Hive 작업](transform-data-using-hadoop-hive.md) 문서를 참조하세요. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig 작업
파이프라인의 HDInsight Pig 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 Pig 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Pig 작업](transform-data-using-hadoop-pig.md) 문서를 참조하세요. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 작업
파이프라인의 HDInsight MapReduce 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 MapReduce 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [MapReduce 작업](transform-data-using-hadoop-map-reduce.md) 문서를 참조하세요.

### <a name="hdinsight-streaming-activity"></a>HDInsight 스트리밍 작업
파이프라인의 HDInsight 스트리밍 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [HDInsight 스트리밍 작업](transform-data-using-hadoop-streaming.md)을 참조하세요.

### <a name="hdinsight-spark-activity"></a>HDInsight Spark 작업
파이프라인의 HDInsight Spark 작업은 사용자 고유 HDInsight 클러스터에서 Spark 프로그램을 실행합니다. 자세한 내용은 [Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 Spark 프로그램 호출](transform-data-using-spark.md)을 참조하세요. 

### <a name="ml-studio-classic-activities"></a>ML 스튜디오(클래식) 작업
이 서비스를 사용하면 예측 분석을 위해 게시된 ML 스튜디오(클래식) 웹 서비스를 사용하는 파이프라인을 쉽게 만들 수 있습니다. 파이프라인에서 [Batch Execution 작업](transform-data-using-machine-learning.md)을 사용하면 Studio(클래식) 웹 서비스를 호출하여 데이터를 일괄적으로 예측할 수 있습니다.

시간이 지남에 따라 Studio(클래식) 점수 매기기 실험의 예측 모델은 새 입력 데이터 세트를 사용하여 다시 학습되어야 합니다. 재학습으로 완료한 후에는 재학습한 기계 학습 모델로 점수 매기기 웹 서비스를 업데이트하려고 합니다. [업데이트 리소스 작업](update-machine-learning-models.md)을 사용하여 새로 학습된 모델로 웹 서비스를 업데이트합니다.  

이러한 스튜디오(클래식) 작업에 관한 자세한 내용은 [ML 스튜디오(클래식) 작업 사용](transform-data-using-machine-learning.md)을 참조하세요. 

### <a name="stored-procedure-activity"></a>저장 프로시저 작업
Data Factory 파이프라인에서 SQL Server 저장 프로시저 작업을 사용하여 엔터프라이즈 또는 Azure VM의 Azure SQL Database, Azure Synapse Analytics, SQL Server Database의 데이터 저장소 중 하나에서 저장 프로시저를 호출할 수 있습니다. 자세한 내용은 [저장 프로시저 작업](transform-data-using-stored-procedure.md) 문서를 참조하세요.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 작업
Data Lake Analytics U-SQL 작업은 Azure Data Lake Analytics 클러스터에 대해 U-SQL 스크립트를 실행합니다. 자세한 내용은 [Data Analytics U-SQL 작업](transform-data-using-data-lake-analytics.md) 문서를 참조하세요. 

### <a name="azure-synapse-notebook-activity"></a>Azure Synapse Notebook 작업 

Synapse 파이프라인의 Azure Synapse Notebook 작업은 Azure Synapse 작업 영역에서 Synapse Notebook을 실행합니다. [Azure Synapse Notebook을 실행하여 데이터 변환](../synapse-analytics/synapse-notebook-activity.md)을 참조하세요.

### <a name="databricks-notebook-activity"></a>Databricks Notebook 활동

파이프라인의 Azure Databricks Notebook 작업은 Azure Databricks 작업 영역에서 Databricks Notebook을 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Databricks Notebook을 실행하여 데이터 변환](transform-data-databricks-notebook.md)을 참조하세요.

### <a name="databricks-jar-activity"></a>Databricks Jar 활동

파이프라인의 Azure Databricks Jar 작업은 Azure Databricks 클러스터에서 Spark Jar를 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Azure Databricks에서 Jar 활동을 실행하여 데이터 변환](transform-data-databricks-jar.md)을 참조하세요.

### <a name="databricks-python-activity"></a>Databricks Python 활동

파이프라인의 Azure Databricks Python 작업은 Azure Databricks 클러스터에서 Python 파일을 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Azure Databricks에서 Python 활동을 실행하여 데이터 변환](transform-data-databricks-python.md)을 참조하세요.

### <a name="custom-activity"></a>사용자 지정 작업
Data Factory에서 지원되지 않는 방식으로 데이터를 변환해야 하는 경우 고유의 데이터 이동 논리가 포함된 사용자 지정 작업을 만들어서 파이프라인에 해당 작업을 사용할 수 있습니다. Azure Batch 서비스 또는 Azure HDInsight 클러스터를 사용하여 실행되도록 사용자 지정 .NET 작업을 구성할 수 있습니다. 자세한 내용은 [사용자 지정 작업 사용](transform-data-using-dotnet-custom-activity.md) 문서를 참조하세요. 

R이 설치된 HDInsight 클러스터에서 R 스크립트를 실행하는 사용자 지정 작업을 만들 수 있습니다. [Azure Data Factory 및 Synapse 파이프라인을 사용하여 R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)을 참조하세요. 

### <a name="compute-environments"></a>컴퓨팅 환경
컴퓨팅 환경을 위한 연결된 서비스를 만들고 변환 작업을 정의할 때 이 연결된 서비스를 사용합니다. 지원되는 컴퓨팅 환경은 두 가지 유형이 있습니다. 

- **주문형**: 이 경우 컴퓨팅 환경은 서비스에서 완전 관리됩니다. 데이터를 처리하기 위한 작업을 제출하기 전에 서비스에서 자동으로 컴퓨팅 환경을 만들고 작업이 완료되면 제거합니다. 작업 실행, 클러스터 관리, 부트스트래핑 작업에 대한 주문형 컴퓨팅 환경의 세부적인 설정을 구성 및 제어할 수 있습니다. 
- **자체 환경 사용**: 이 경우 사용자 고유의 컴퓨팅 환경(예: HDInsight 클러스터)을 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 이를 사용하여 작업을 실행하는 서비스와 사용자에 의해 관리됩니다. 

지원되는 컴퓨팅 서비스에 관한 자세한 내용은 [컴퓨팅 연결된 서비스](compute-linked-services.md) 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계
변환 작업 사용 예제에 대해서는 [자습서: Spark를 사용하여 데이터 변환](tutorial-transform-data-spark-powershell.md)을 참조하세요.