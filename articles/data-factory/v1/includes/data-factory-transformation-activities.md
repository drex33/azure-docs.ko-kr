---
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 850333ff1d0a2668dcd0553f2d03562fb2c046a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624421"
---
Azure Data Factory는 개별적 또는 다른 작업과 연계하여 파이프라인에 추가할 수 있는 다음과 같은 변환 작업을 지원합니다.

| 데이터 변환 작업 | 컴퓨팅 환경 |
|:--- |:--- |
| [Hive](../data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop 스트리밍](../data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../data-factory-spark.md) | HDInsight [Hadoop] |
| [ML Studio(클래식) 작업: Batch Execution 및 리소스 업데이트](../data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [저장 프로시저](../data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics 또는 SQL Server |
| [데이터 레이크 분석 U-SQL](../data-factory-usql-activity.md) |Azure 데이터 레이크 분석 |
| [DotNet](../data-factory-use-custom-activities.md) |HDInsight [Hadoop] 또는 Azure Batch |

> [!NOTE]
> MapReduce 작업을 사용하여 HDInsight Spark 클러스터에서 Spark 프로그램을 실행할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 Spark 프로그램 호출](../data-factory-spark.md) 을 참조하세요.
> R이 설치된 HDInsight 클러스터에서 R 스크립트를 실행하는 사용자 지정 작업을 만들 수 있습니다. [Azure Data Factory를 사용하여 R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)을 참조하세요.
> 
> 

