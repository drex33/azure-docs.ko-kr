---
title: Azure Machine Learning 파이프라인 실행
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 파이프라인에서 Azure Machine Learning 파이프라인을 실행하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 09/09/2021
ms.openlocfilehash: 0d7fe523b7300634df6c876525b1ffe47a49c205
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806329"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics Azure Machine Learning 파이프라인 실행

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 및 Synapse Analytics 파이프라인의 단계로 Azure Machine Learning 파이프라인을 실행합니다. Machine Learning Execute Pipeline 작업을 통해 가능한 대출 기본값 식별, 감정 확인 및 고객 동작 패턴 분석과 같은 일괄 처리 예측 시나리오를 사용할 수 있습니다.

아래 비디오는 이 기능에 대한 6분 소개와 데모를 제공합니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>구문

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | 파이프라인의 작업 이름입니다. | String | 예
type | 작업 유형은 'AzureMLExecutePipeline'입니다. | String | 예
linkedServiceName | Azure Machine Learning에 연결된 서비스 | 연결된 서비스 참조 | Yes
mlPipelineId | 게시된 Azure Machine Learning 파이프라인의 ID | 문자열(또는 resultType 문자열이 있는 식) | Yes
experimentName | Machine Learning 파이프라인 실행의 실행 기록 실험 이름 | 문자열(또는 resultType 문자열이 있는 식) | No
mlPipelineParameters | 게시된 Azure Machine Learning 파이프라인 엔드포인트에 전달할 키, 값 쌍입니다. 키는 게시된 Azure Machine Learning 파이프라인에 정의된 파이프라인 매개 변수의 이름과 일치해야 합니다. | 키 값 쌍이 있는 개체(또는 resultType 개체가 있는 식) | No
mlParentRunId | 부모 Azure Machine Learning 파이프라인 실행 ID | 문자열(또는 resultType 문자열이 있는 식) | No
dataPathAssignments | Azure Machine learning에서 데이터 경로를 변경하는 데 사용되는 사전입니다. 데이터 경로 전환 사용 | 키 값 쌍을 포함하는 개체 | No
continueOnStepFailure | 단계가 실패하는 경우 Machine Learning 파이프라인 실행에서 다른 단계의 실행을 계속할지 여부 | boolean | No

> [!NOTE]
> Machine Learning 파이프라인 이름 및 ID의 드롭다운 항목을 채우려면 사용자에게 ML 파이프라인을 나열할 수 있는 권한이 있어야 합니다. UI는 로그인한 사용자의 자격 증명을 사용하여 AzureMLService API를 직접 호출합니다.  

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요.

* [Data Flow 작업 실행](control-flow-execute-data-flow-activity.md)
* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
