---
title: Hadoop 스트리밍 작업을 통한 데이터 변환 - Azure
description: Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행함으로써 데이터를 변환하는 방법을 알아봅니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: a14e051c13161825ce0a36a13a806d30e1203bbc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259964"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 데이터 변환
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
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory에서 Hadoop 스트리밍 작업을 사용하여 데이터 변환](../transform-data-using-hadoop-streaming.md)을 참조하세요.


HDInsightStreamingActivity 작업을 사용하여 Azure Data Factory 파이프라인에서 Hadoop 스트리밍 작업을 호출할 수 있습니다. 다음 JSON 조각은 파이프라인 JSON 파일에서 HDInsightStreamingActivity를 사용하기 위한 구문을 보여 줍니다. 

Data Factory [파이프라인](data-factory-create-pipelines.md)의 HDInsight 스트리밍 작업은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 기반 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

> [!NOTE] 
> Azure Data Factory를 처음 접하는 경우 [Azure Data Factory 소개](data-factory-introduction.md)를 읽고 이 문서를 읽기 전에 [첫 번째 데이터 파이프라인 빌드](data-factory-build-your-first-pipeline.md) 자습서를 수행하세요. 

## <a name="json-sample"></a>JSON 샘플
HDInsight 클러스터는 예제 프로그램(wc.exe 및 cat.exe) 및 데이터(davinci.txt)와 함께 자동으로 채워집니다. 기본적으로 HDInsight 클러스터에 사용되는 컨테이너의 이름은 클러스터 자체의 이름입니다. 예를 들어, 클러스터 이름이 myhdicluster이면 연결된 BLOB 컨테이너의 이름은 myhdicluster가 됩니다. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

다음 사항에 유의하세요.

1. **linkedServiceName** 을 스트리밍 mapreduce 작업을 실행할 수 있는 HDInsight 클러스터를 가리키는 연결된 서비스의 이름으로 설정합니다.
2. activity의 type을 **HDInsightStreaming** 으로 설정합니다.
3. **mapper** 속성의 경우 매퍼 실행 파일 이름을 지정합니다. 예제에서는 cat.exe가 매퍼 실행 파일입니다.
4. **reducer** 속성에는 리듀서 실행 파일의 이름을 지정합니다. 예제에서는 wc.exe가 리듀서 실행 파일입니다.
5. **input** 유형 속성에는 매퍼의 입력 파일(위치 포함)을 지정합니다. `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt` 예제에서 adfsample은 Blob 컨테이너이고, example/data/Gutenberg는 폴더이며, davinci.txt는 Blob입니다.
6. **output** 유형 속성에는 리듀서의 출력 파일(위치 포함)을 지정합니다. Hadoop 스트리밍 작업의 출력이 이 속성에 지정된 위치에 기록됩니다.
7. **filePaths** 섹션에는 매퍼 및 리듀서 실행 파일의 경로를 지정합니다. "adfsample/example/apps/wc.exe" 예에서 adfsample은 Blob 컨테이너, example/apps는 폴더, wc.exe는 실행 파일입니다.
8. **fileLinkedService** 속성에는 filePaths 섹션에 지정된 파일이 포함된 Azure Storage를 나타내는 Azure Storage 연결된 서비스를 지정합니다.
9. **arguments** 속성의 경우 스트리밍 작업의 인수를 지정합니다.
10. **getDebugInfo** 속성은 선택적 요소입니다. Failure로 설정되면 실패한 경우에만 로그가 다운로드됩니다. Always로 설정되면 실행 상태에 관계없이 로그가 항상 다운로드됩니다.

> [!NOTE]
> 예제에서 볼 수 있듯이 **output** 속성에 대해 Hadoop 스트리밍 작업에 대한 출력 데이터 세트를 지정합니다. 이 데이터 세트는 파이프라인 일정을 진행하는데 필요한 더미 데이터 세트입니다. **input** 속성에 대한 작업에 입력 데이터 세트를 지정할 필요가 없습니다.  
> 
> 

## <a name="example"></a>예제
이번 연습의 파이프라인에서는 Azure HDInsight 클러스터에서 Word Count 스트리밍 Map/Reduce 프로그램을 실행합니다. 

### <a name="linked-services"></a>연결된 서비스
#### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
우선 Azure HDInsight 클러스터에서 사용되는 Azure Storage를 Azure 데이터 팩터리에 연결하도록 연결된 서비스를 생성합니다. 다음 코드를 복사하여 붙여넣는 경우, 잊지 말고 계정 이름과 계정 키를 사용자의 Azure Storage의 이름과 키로 바꿉니다. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 연결된 서비스
다음으로, Azure HDInsight 클러스터를 Azure 데이터 팩터리에 연결하도록 연결된 서비스를 만듭니다. 다음 코드를 복사하여 붙여넣는 경우, HDInsight 클러스터 이름을 사용자의 HDInsight 클러스터 이름으로 바꾸고 사용자 이름과 암호 값을 변경합니다. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>데이터 세트
#### <a name="output-dataset"></a>출력 데이터 세트
이 예제의 파이프라인은 input을 포함하지 않습니다. HDInsight 스트리밍 작업에 대한 출력 데이터 세트를 지정합니다. 이 데이터 세트는 파이프라인 일정을 진행하는데 필요한 더미 데이터 세트입니다. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>파이프라인
이 예제의 파이프라인은 **HDInsightStreaming** 형식의 작업을 하나만 포함합니다. 

HDInsight 클러스터는 예제 프로그램(wc.exe 및 cat.exe) 및 데이터(davinci.txt)와 함께 자동으로 채워집니다. 기본적으로 HDInsight 클러스터에 사용되는 컨테이너의 이름은 클러스터 자체의 이름입니다. 예를 들어, 클러스터 이름이 myhdicluster이면 연결된 BLOB 컨테이너의 이름은 myhdicluster가 됩니다.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>참고 항목
* [Hive 작업](data-factory-hive-activity.md)
* [Pig 작업](data-factory-pig-activity.md)
* [MapReduce 작업](data-factory-map-reduce.md)
* [Spark 프로그램 호출](data-factory-spark.md)
* [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

