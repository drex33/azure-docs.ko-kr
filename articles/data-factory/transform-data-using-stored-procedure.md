---
title: 저장 프로시저 작업을 이용한 데이터 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: SQL Server 저장 프로시저 작업을 사용 하 여 Azure Data Factory 또는 Synapse Analytics 파이프라인에서 Azure SQL Database/Data Warehouse의 저장 프로시저를 호출 하는 방법을 설명 합니다.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 30e40c20aa8e11add35b270baf867a5fd5e46058
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016573"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics에서 SQL Server 저장 프로시저 작업을 사용 하 여 데이터 변환
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-stored-proc-activity.md)
> * [현재 버전](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory 또는 Synapse [파이프라인](concepts-pipelines-activities.md) 의 데이터 변환 작업을 사용 하 여 원시 데이터를 예측 및 통찰력으로 변환 하 고 처리 합니다. 저장 프로시저 작업은 파이프라인이 지 원하는 변환 작업 중 하나입니다. 이 문서는 데이터 변환 및 지원되는 변환 작업의 일반적인 개요를 제공하는 [데이터 변환](transform-data.md) 문서를 기반으로 합니다.

> [!NOTE]
> Azure Data Factory를 처음 접하는 경우 이 문서를 읽기 전에 [Azure Data Factory 소개](introduction.md)를 읽고 [자습서: 데이터 변환](tutorial-transform-data-spark-powershell.md)을 수행하세요.  Synapse Analytics에 대해 자세히 알아보려면 [Azure Synapse Analytics 란?](../synapse-analytics/overview-what-is.md)을 참조 하세요.

저장 프로시저 작업을 사용하여 엔터프라이즈 또는 Azure VM(Virtual Machine)의 다음 데이터 저장소 중 하나에서 저장 프로시저를 호출할 수 있습니다. 

- Azure SQL Database
- Azure Synapse Analytics
- SQL Server 데이터베이스.  SQL Server를 사용 중인 경우 데이터베이스를 호스트하는 동일한 컴퓨터 또는 데이터베이스에 대한 액세스 권한이 있는 별도 컴퓨터에서 자체 호스팅 통합 런타임을 설치합니다. 자체 호스팅 통합 런타임은 온-프레미스/Azure VM에서 데이터 원본을 클라우드 서비스에 안전하고 관리되는 방식으로 연결하는 구성 요소입니다. 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md)을 참조하세요.

> [!IMPORTANT]
> Azure SQL Database 또는 SQL Server로 데이터를 복사할 때 **sqlWriterStoredProcedureName** 속성을 사용하여 복사 작업에 저장 프로시저를 호출하도록 **SqlSink** 를 구성할 수 있습니다. 이 속성에 대한 자세한 내용은 커넥터 문서 [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md)를 참조하세요. 복사 작업을 사용하여 Azure Synapse Analytics로 데이터를 복사하는 동안 저장 프로시저를 호출하는 동작은 지원되지 않습니다. 그러나 저장 프로시저 작업을 사용하여 Azure Synapse Analytics의 저장 프로시저를 호출할 수는 있습니다. 
>
> Azure SQL Database 또는 SQL Server 또는 Azure Synapse Analytics에서 데이터를 복사하는 경우 복사 작업에서 **SqlSource** 를 구성하여, **sqlReaderStoredProcedureName** 속성을 사용해서 원본 데이터베이스에서 데이터를 읽을 수 있도록 저장 프로시저를 호출할 수 있습니다. 자세한 내용은 커넥터 문서 [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Synapse Analytics](connector-azure-sql-data-warehouse.md)를 참조하세요.          

 

## <a name="syntax-details"></a>구문 세부 정보
다음은 저장 프로시저 작업을 정의하기 위한 JSON 형식입니다.

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

다음 표에서는 이러한 JSON 속성에 대해 설명합니다.

| 속성                  | 설명                              | 필수 |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | 작업의 이름                     | 예      |
| description               | 작업이 무엇에 사용되는지 설명하는 텍스트입니다. | 예       |
| type                      | 저장 프로시저 작업의 경우 활동 형식은 **SqlServerStoredProcedure** 입니다. | 예      |
| linkedServiceName         | **Azure SQL Database** 나 **Azure Synapse Analytics** 또는 Data Factory에 링크된 서비스로 등록된 **SQL Server** 를 참조하세요. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요. | 예      |
| storedProcedureName       | 호출할 저장 프로시저의 이름을 지정합니다. | 예      |
| storedProcedureParameters | 저장 프로시저 매개 변수의 값을 지정합니다. 매개 변수 값 및 데이터 원본에서 지원하는 해당 형식을 전달하기 위해 `"param1": { "value": "param1Value","type":"param1Type" }`를 사용합니다. 매개 변수에 null을 전달해야 하는 경우 `"param1": { "value": null }`(모두 소문자)을 사용합니다. | 예       |

## <a name="parameter-data-type-mapping"></a>매개 변수 데이터 형식 매핑
매개 변수에 대해 지정 하는 데이터 형식은 사용 하는 데이터 원본의 데이터 형식에 매핑되는 내부 서비스 유형입니다. 커넥터 설명서에서 설명된 데이터 원본의 데이터 형식 매핑을 찾을 수 있습니다. 예를 들면 다음과 같습니다.

- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#data-type-mapping-for-azure-synapse-analytics)
- [Azure SQL Database 데이터 형식 매핑](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)
- [Oracle 데이터 형식 매핑](connector-oracle.md#data-type-mapping-for-oracle)
- [SQL Server 데이터 형식 매핑](connector-sql-server.md#data-type-mapping-for-sql-server)

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
