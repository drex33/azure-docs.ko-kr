---
title: Azure Data Explorer(Kusto)
description: 이 문서에서는 Azure Data Explorer(Kusto)와 서버리스 Apache Spark 풀 간에 데이터를 이동하기 위해 커넥터를 사용하는 방법에 대한 정보를 제공합니다.
services: synapse-analytics
ms.author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/19/2020
ms.reviewer: ManojRaheja
author: midesa
ms.openlocfilehash: 82310e14b3eac25be038a7748d1992daef72d83f
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516678"
---
# <a name="azure-data-explorer-kusto-connector-for-apache-spark"></a>Apache Spark용 Azure Data Explorer(Kusto) 커넥터
Apache Spark용 Azure Data Explorer(Kusto) 커넥터는 Kusto 클러스터와 Spark 간에 데이터를 효율적으로 전송하도록 설계되었습니다. 이 커넥터는 Python, Java 및 .NET에서 사용할 수 있습니다. Azure Synapse Apache Spark 2.4 런타임에 기본 제공됩니다.

## <a name="authentication"></a>인증
Azure Synapse Notebooks 또는 Apache Spark 작업 정의를 사용할 때 시스템 간 인증은 연결된 서비스와 원활하게 이루어집니다. 토큰 서비스는 Azure Active Directory와 연결하여 Kusto 클러스터에 액세스할 때 사용할 보안 토큰을 얻습니다.

Azure Synapse Pipelines의 경우 인증은 서비스 주체 이름을 사용합니다. 현재 관리 ID는 Azure Data Explorer 커넥터에서 지원되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항 
  - [Azure Data Explorer 연결:](../../quickstart-connect-azure-data-explorer.md)기존 Kusto 클러스터에 연결하도록 연결된 서비스를 설정해야 합니다.

## <a name="limitations"></a>제한 사항
  - Azure Data Explorer(Kusto) 커넥터는 현재 Azure Synapse Apache Spark 2.4 런타임에서만 지원됩니다.
  - Azure Data Explorer 연결된 서비스는 서비스 주체 이름으로만 구성할 수 있습니다.
  - Azure Synapse Notebooks 또는 Apache Spark 작업 정의 내에서 Azure Data Explorer 커넥터는 AAD 통과를 사용하여 Kusto 클러스터에 연결합니다.


## <a name="use-the-azure-data-explorer-kusto-connector"></a>Azure Data Explorer(Kusto) 커넥터 사용
다음 섹션에서는 Kusto 테이블에 데이터를 쓰고 Kusto 테이블에서 데이터를 읽는 방법에 대한 간단한 예제를 제공합니다. 자세한 설명서는 [Azure Data Explorer(Kusto) 커넥터 프로젝트를](https://github.com/Azure/azure-kusto-spark) 참조하세요. 

### <a name="read-data"></a>데이터 읽기

```python
kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .load()

display(kustoDf)
```

강제 배포 모드 및 기타 고급 옵션을 통해 읽기를 일괄 처리할 수도 있습니다. 자세한 내용은 [Kusto 원본 옵션 참조](https://github.com/Azure/azure-kusto-spark/blob/master/connector/src/main/scala/com/microsoft/kusto/spark/datasource/KustoSourceOptions.scala)를 참조하세요.

```python
crp = sc._jvm.com.microsoft.azure.kusto.data.ClientRequestProperties()
crp.setOption("norequesttimeout",True)
crp.toString()

kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .option("clientRequestPropertiesJson", crp.toString()) \
            .option("readMode", 'ForceDistributedMode') \
            .load()

display(kustoDf) 
```
### <a name="write-data"></a>데이터 쓰기

```python
df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .mode("Append") \
    .save()
```
또한 추가 수집 속성을 제공하여 데이터 일괄 쓰기를 수행할 수도 있습니다. 지원되는 수집 속성에 대한 자세한 내용은 [Kusto ingestion 속성 참조 자료](/azure/data-explorer/ingestion-properties)를 참조하세요.


 ```python
extentsCreationTime = sc._jvm.org.joda.time.DateTime.now().plusDays(1)
csvMap = "[{\"Name\":\"ColA\",\"Ordinal\":0},{\"Name\":\"ColB\",\"Ordinal\":1}]"
# Alternatively use an existing csv mapping configured on the table and pass it as the last parameter of SparkIngestionProperties or use none


sp = sc._jvm.com.microsoft.kusto.spark.datasink.SparkIngestionProperties(
        False, ["dropByTags"], ["ingestByTags"], ["tags"], ["ingestIfNotExistsTags"], extentsCreationTime, csvMap, None)

df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .option("sparkIngestionPropertiesJson", sp.toString()) \
    .option("tableCreateOptions","CreateIfNotExist") \
    .mode("Append") \
    .save()
```

## <a name="next-steps"></a>다음 단계
- [Azure Data Explorer에 연결](../../quickstart-connect-azure-data-explorer.md)
- [Azure Data Explorer(Kusto) Apache Spark 커넥터](https://github.com/Azure/azure-kusto-spark)
