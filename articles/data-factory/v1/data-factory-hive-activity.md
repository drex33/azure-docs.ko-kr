---
title: Hive 작업을 사용하여 데이터 변환 - Azure
description: Azure Data Factory v1에서 Hive 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Hive 쿼리를 실행하는 방법에 대해 알아봅니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 284fa5910a397efa30b0fc12a55e1301438cadc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259926"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory에서 Hive 활동을 사용하여 데이터 변환 
> [!div class="op_single_selector" title1="변환 작업"]
> * [Hive 작업](data-factory-hive-activity.md) 
> * [Pig 작업](data-factory-pig-activity.md)
> * [MapReduce 작업](data-factory-map-reduce.md)
> * [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
> * [Spark 작업](data-factory-spark.md)
> * [ML Studio(클래식) Batch Execution 작업](data-factory-azure-ml-batch-execution-activity.md)
> * [ML Studio(클래식) 업데이트 리소스 작업](data-factory-azure-ml-update-resource-activity.md)
> * [저장 프로시저 작업](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 작업](data-factory-usql-activity.md)
> * [.NET 사용자 지정 작업](data-factory-use-custom-activities.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory에서 Hive 작업을 사용하여 데이터 변환](../transform-data-using-hadoop-hive.md)을 참조하세요.

Data Factory [파이프라인](data-factory-create-pipelines.md)에서 HDInsight Hive 작업은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 기반 HDInsight 클러스터의 Hive 쿼리를 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

> [!NOTE] 
> Azure Data Factory를 처음 접하는 경우 [Azure Data Factory 소개](data-factory-introduction.md)를 읽고 이 문서를 읽기 전에 [첫 번째 데이터 파이프라인 빌드](data-factory-build-your-first-pipeline.md) 자습서를 수행하세요. 

## <a name="syntax"></a>구문

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>구문 세부 정보
| 속성 | 설명 | 필수 |
| --- | --- | --- |
| name |작업의 이름 |예 |
| description |작업이 무엇에 사용되는지 설명하는 텍스트입니다. |예 |
| type |HDinsightHive |예 |
| 입력 |Hive 활동에서 사용된 입력 |예 |
| outputs |Hive 활동에서 생성된 출력 |예 |
| linkedServiceName |데이터 팩터리에서 연결된 서비스로 등록된 HDInsight 클러스터에 대한 참조 |예 |
| 스크립트 |Hive 스크립트 인라인 지정 |예 |
| scriptPath |.NET용 File Storage 시작 'script' 또는 'scriptPath' 속성을 사용합니다. 둘 모두를 사용할 수는 없습니다. 파일 이름은 대/소문자를 구분합니다. |예 |
| defines |'hiveconf'를 사용하는 Hive 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정 |예 |

## <a name="example"></a>예제
회사에서 출시한 게임을 사용자가 플레이한 시간을 파악하려는 게임 로그 분석의 예를 살펴보겠습니다. 

다음 로그는 쉼표(`,`)로 구분되고 ProfileID, SessionStart, Duration, SrcIPAddress 및 GameType 필드를 포함하는 샘플 게임 로그입니다.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

이 데이터를 처리하는 **Hive 스크립트** 는 다음과 같습니다.

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

데이터 팩터리 파이프라인에서 이 Hive 스크립트를 실행하려면 다음을 수행해야 합니다

1. 연결된 서비스를 만들어 [자체적인 HDInsight 컴퓨팅 클러스터](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)를 등록하거나 [주문형 HDInsight 컴퓨팅 클러스터](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 구성합니다. 이 연결된 서비스를 "HDInsightLinkedService"라고 하겠습니다.
2. [연결된 서비스](data-factory-azure-blob-connector.md)를 만들어 데이터를 호스팅하는 Azure Blob Storage에 연결을 구성합니다. 이 연결된 서비스를 "StorageLinkedService"라고 합니다.
3. 입력 및 출력 데이터를 가르키는 [데이터 세트](data-factory-create-datasets.md)를 만듭니다. 입력 데이터 세트를 "HiveSampleIn"라고 하고 출력 데이터 세트를 "HiveSampleOut"라고 합니다.
4. \#2단계에서 구성된 Azure Blob Storage에 Hive 쿼리를 파일로 복사합니다. 데이터를 호스팅하는 스토리지가 이 쿼리 파일을 호스트하는 스토리지와 다른 경우 서비스에 연결된 별도 Azure Storage를 만들고 작업에서 이를 참조합니다. **scriptPath** 를 사용하여 hive 쿼리 파일에 대한 경로를 지정하고 **scriptLinkedService** 를 사용하여 스크립트 파일을 포함하는 Azure Storage를 지정합니다. 
   
   > [!NOTE]
   > **script** 속성을 사용하여 활동 정의에서 Hive 스크립트를 인라인으로 제공할 수도 있습니다. 이렇게 하면 JSON 문서 내의 스크립트 모든 특수 문자를 이스케이프 처리해야 하므로 디버그 관련 문제가 발생할 수 있기 때문에 이 방식은 사용하지 않는 것이 좋습니다. 모법 사례는 4단계를 수행하는 것입니다.
   > 
   > 
5. HDInsightHive 활동이 포함된 파이프라인을 만듭니다. 작업은 데이터를 프로세스/변환합니다.

  ```json
  {
    "name": "HiveActivitySamplePipeline",
       "properties": {
    "activities": [
      {
        "name": "HiveActivitySample",
        "type": "HDInsightHive",
        "inputs": [
        {
          "name": "HiveSampleIn"
        }
        ],
             "outputs": [
               {
                "name": "HiveSampleOut"
               }
             ],
             "linkedServiceName": "HDInsightLinkedService",
             "typeproperties": {
                 "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                 "scriptLinkedService": "StorageLinkedService"
             },
              "scheduler": {
          "frequency": "Hour",
                   "interval": 1
             }
           }
      ]
    }
  }
  ```

6. 파이프라인을 배포합니다. 자세한 내용은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 
7. 데이터 팩터리 모니터링 및 관리 보기를 사용하여 파이프라인을 모니터링합니다. 자세한 내용은 [데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 문서를 참조하세요. 

## <a name="specifying-parameters-for-a-hive-script"></a>Hive 스크립트에 대한 매개 변수 지정
이 예에서 게임 로그는 Azure Blob Storage에 매일 수집되고 날짜 및 시간으로 분할된 폴더에 저장됩니다. Hive 스크립트를 매개 변수화하고 런타임 동안 입력 폴더 위치를 동적으로 전달하며 날짜 및 시간으로 분할된 출력을 생성하려고 합니다.

매개 변수가 있는 Hive 스크립트를 사용하려면 다음을 수행합니다.

* **defines** 에서 매개 변수를 정의합니다.

  ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* Hive 스크립트에서 **${hiveconf:parameterName}** 를 사용하는 매개 변수를 참조합니다. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>참고 항목
* [Pig 작업](data-factory-pig-activity.md)
* [MapReduce 작업](data-factory-map-reduce.md)
* [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
* [Spark 프로그램 호출](data-factory-spark.md)
* [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

