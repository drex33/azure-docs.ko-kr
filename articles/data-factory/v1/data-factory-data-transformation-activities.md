---
title: '데이터 변환: 데이터 처리 및 변환 '
description: Hadoop, ML Studio(클래식) 또는 Azure Data Lake Analytics를 사용하여 Azure Data Factory에서 데이터를 변환 또는 처리하는 방법에 대해 알아봅니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 642e30aca08ae8490909992422939438650c7ed1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250049"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Azure Data Factory 버전 1의 데이터 변환
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop 스트리밍](data-factory-hadoop-streaming-activity.md)
> * [ML Studio(클래식)](data-factory-azure-ml-batch-execution-activity.md) 
> * [저장 프로시저](data-factory-stored-proc-activity.md)
> * [데이터 레이크 분석 U-SQL](data-factory-usql-activity.md)
> * [.NET 사용자 지정](data-factory-use-custom-activities.md)

## <a name="overview"></a>개요
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Data Factory에서 데이터 변환 작업](../transform-data.md)을 참조하세요.

이 문서에서는 원시 데이터를 예측 가능하고 통찰력 있는 정보로 변환하고 처리하는 데 사용할 수 있는 Azure Data Factory의 데이터 변환 작업을 설명합니다. 변환 작업은 Azure HDInsight 클러스터나 Azure Batch와 같은 컴퓨팅 환경에서 실행됩니다. 각 변환 작업에 대한 자세한 정보가 있는 문서에 대한 링크를 제공합니다.

Data Factory는 개별적 또는 다른 작업과 연계하여 [파이프라인](data-factory-create-pipelines.md)에 추가할 수 있는 다음 데이터 변환 작업을 지원합니다.

> [!NOTE]
> 단계별 지침이 포함된 연습은 [Hive 변환으로 파이프라인 만들기](data-factory-build-your-first-pipeline.md) 문서를 참조하세요.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive 작업
Data Factory 파이프라인에서 HDInsight Hive 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Hive 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Hive 작업](data-factory-hive-activity.md) 문서를 참조하세요. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig 작업
Data Factory 파이프라인에서 HDInsight Pig 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Pig 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Pig 작업](data-factory-pig-activity.md) 문서를 참조하세요. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 작업
Data Factory 파이프라인의 HDInsight MapReduce 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 MapReduce 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [MapReduce 작업](data-factory-map-reduce.md) 문서를 참조하세요.

## <a name="hdinsight-streaming-activity"></a>HDInsight 스트리밍 작업
Data Factory 파이프라인의 HDInsight 스트리밍 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [HDInsight 스트리밍 작업](data-factory-hadoop-streaming-activity.md)을 참조하세요.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark 작업
Data Factory 파이프라인에서 HDInsight Spark 작업은 사용자 고유 HDInsight 클러스터에서 Spark 프로그램을 실행합니다. 자세한 내용은 [Azure Data Factory에서 Spark 프로그램 호출](data-factory-spark.md) 을 참조하세요. 

## <a name="ml-studio-classic-activities"></a>ML Studio(클래식) 작업
Azure Data Factory를 사용하면 예측 분석을 위해 게시된 ML Studio(클래식) 웹 서비스를 사용하는 파이프라인을 쉽게 만들 수 있습니다. Azure Data Factory 파이프라인에서 [일괄 실행 작업](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity)을 사용하면 Studio(클래식) 웹 서비스를 호출하여 데이터를 일괄적으로 예측할 수 있습니다.

시간이 지남에 따라 Studio(클래식) 점수 매기기 실험의 예측 모델은 새 입력 데이터 세트를 사용하여 다시 학습되어야 합니다. 재학습으로 완료한 후에는 재학습한 Machine Learning 모델로 점수 매기기 웹 서비스를 업데이트하려고 합니다. [업데이트 리소스 작업](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity)을 사용하여 새로 학습된 모델로 웹 서비스를 업데이트합니다.  

이러한 Studio(클래식) 작업에 대한 자세한 내용은 [ML Studio(클래식) 작업 사용](data-factory-azure-ml-batch-execution-activity.md)을 참조하세요. 

## <a name="stored-procedure-activity"></a>저장 프로시저 작업
Data Factory 파이프라인에서 SQL Server 저장 프로시저 작업을 사용하여 엔터프라이즈 또는 Azure VM의 Azure SQL Database,Azure Synapse Analytics, SQL Server Database의 데이터 저장소 중 하나에서 저장 프로시저를 호출할 수 있습니다. 자세한 내용은 [저장 프로시저 작업](data-factory-stored-proc-activity.md)을 참조하세요.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 작업
Data Lake Analytics U-SQL 작업은 Azure Data Lake Analytics 클러스터에 대해 U-SQL 스크립트를 실행합니다. 자세한 내용은 [Data Analytics U-SQL 작업](data-factory-usql-activity.md) 문서를 참조하세요. 

## <a name="net-custom-activity"></a>.NET 사용자 지정 작업
Data Factory에서 지원되지 않는 방식으로 데이터를 변환해야 하는 경우 고유의 데이터 이동 논리가 포함된 사용자 지정 작업을 만들어서 파이프라인에 해당 작업을 사용할 수 있습니다. Azure Batch 서비스 또는 Azure HDInsight 클러스터를 사용하여 실행되도록 사용자 지정 .NET 작업을 구성할 수 있습니다. 자세한 내용은 [사용자 지정 작업 사용](data-factory-use-custom-activities.md) 문서를 참조하세요. 

R이 설치된 HDInsight 클러스터에서 R 스크립트를 실행하는 사용자 지정 작업을 만들 수 있습니다. [Azure Data Factory를 사용하여 R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)을 참조하세요. 

## <a name="compute-environments"></a>컴퓨팅 환경
컴퓨팅 환경을 위한 연결된 서비스를 만들고 변환 작업을 정의할 때 이 연결된 서비스를 사용합니다. 데이터 팩터리에서 지원하는 컴퓨팅 환경은 두 가지 유형이 있습니다. 

1. **주문형**: 이 경우 데이터 팩터리에서 완전히 컴퓨팅 환경을 관리합니다. 데이터를 처리하기 위한 작업을 제출하기 전에 데이터 팩터리 서비스에서 자동으로 컴퓨팅 환경을 만들고 작업이 완료되면 제거합니다. 작업 실행, 클러스터 관리, 부트스트래핑 작업에 대한 주문형 컴퓨팅 환경의 세부적인 설정을 구성 및 제어할 수 있습니다. 
2. **자체 환경 사용**: 이 경우 사용자 고유의 컴퓨팅 환경(예: HDInsight 클러스터)을 데이터 팩터리에 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 이를 사용하여 작업을 실행하는 데이터 팩터리 서비스와 사용자에 의해 관리됩니다. 

데이터 팩터리에서 지원하는 컴퓨팅 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](data-factory-compute-linked-services.md) 문서를 참조하세요. 

## <a name="summary"></a>요약
Azure Data Factory는 작업에 대한 다음 데이터 변환 작업 및 컴퓨팅 환경을 지원합니다. 변환 작업은 개별적 또는 다른 작업과 연계하여 파이프라인에 추가될 수 있습니다.

| 데이터 변환 작업 | 컴퓨팅 환경 |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop 스트리밍](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [ML Studio(클래식) 작업: Batch Execution 및 리소스 업데이트](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [저장 프로시저](data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics 또는 SQL Server |
| [데이터 레이크 분석 U-SQL](data-factory-usql-activity.md) |Azure 데이터 레이크 분석 |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] 또는 Azure Batch |

