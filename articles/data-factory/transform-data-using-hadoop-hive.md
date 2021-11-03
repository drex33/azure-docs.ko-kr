---
title: Hadoop Hive 작업을 사용하여 데이터 변환
description: Azure Data Factory 또는 Synapse Analytics 파이프라인에서 Hive 활동을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Hive 쿼리를 실행하는 방법을 알아봅니다.
titleSuffix: Azure Data Factory & Azure Synapse
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 2a7330c799111fbb0271aedacd3daaf939db8f38
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005749"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics Hadoop Hive 활동을 사용하여 데이터 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-hive-activity.md)
> * [현재 버전](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 또는 Synapse Analytics [파이프라인의](concepts-pipelines-activities.md) HDInsight Hive 작업은 [사용자](compute-linked-services.md#azure-hdinsight-linked-service) 고유 또는 주문형 HDInsight [클러스터에서](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  Hive 쿼리를 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](transform-data.md) 문서에서 작성합니다.

Azure Data Factory 및 Synapse Analytics 경우 이 문서를 읽기 전에 [Azure Data Factory](introduction.md) 또는 [Synapse Analytics](../synapse-analytics/overview-what-is.md)소개 문서를 읽고 [자습서: 데이터 변환을](tutorial-transform-data-spark-powershell.md) 수행합니다. 

## <a name="syntax"></a>구문

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>구문 세부 정보
| 속성            | 설명                                                  | 필수 |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | 작업의 이름                                         | 예      |
| description         | 작업이 무엇에 사용되는지 설명하는 텍스트입니다.                | 예       |
| type                | Hive 작업의 경우 작업 유형은 HDinsightHive입니다.        | 예      |
| linkedServiceName   | 연결된 서비스로 등록된 HDInsight 클러스터에 대한 참조입니다. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요. | 예      |
| scriptLinkedService | 실행할 Hive 스크립트를 저장하는 데 사용되는 Azure Storage 연결된 서비스에 대한 참조입니다. 여기서는 **[Azure Blob Storage](./connector-azure-blob-storage.md)** 및 **[ADLS Gen2](./connector-azure-data-lake-storage.md)** 연결 서비스만 지원됩니다. 이 연결된 서비스를 지정하지 않으면 HDInsight 연결된 서비스에 정의된 Azure Storage 연결된 서비스가 사용됩니다.  | 예       |
| scriptPath          | scriptLinkedService에서 참조하는 Azure Storage에 저장된 스크립트 파일의 경로를 제공합니다. 파일 이름은 대/소문자를 구분합니다. | 예      |
| getDebugInfo        | scriptLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 로그 파일을 언제 복사할지 지정합니다. 허용되는 값은 다음과 같습니다. 없음, 항상 또는 실패. 기본값: 없음 | 예       |
| 인수           | Hadoop 작업에 대한 인수 배열을 지정합니다. 인수는 각 작업에 대한 명령줄 인수로 전달됩니다. | 예       |
| defines             | Hive 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정합니다. | 예       |
| queryTimeout        | 쿼리 시간 제한 값(분)입니다. HDInsight 클러스터에서 Enterprise Security Package가 사용하도록 설정되어 있으면 적용됩니다. | 예       |

>[!NOTE]
>queryTimeout의 기본값은 120분입니다. 

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
