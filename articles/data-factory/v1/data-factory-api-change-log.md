---
title: Azure Data Factory - .NET API 변경 로그
description: Azure Data Factory용 특정 .NET API 버전의 호환성이 손상되는 변경, 기능 추가, 버그 수정 등을 설명합니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jonburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
robots: noindex
ms.date: 10/22/2021
ms.openlocfilehash: 3b28e8d4042377e7834fe619e15feabb916b2591
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059626"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure 데이터 팩터리 - .NET API 변경 로그
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 

이 문서에서는 특정 버전의 Azure Data Factory SDK 변경 내용에 대해 설명합니다. [여기](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>버전 4.11.0
기능 추가 사항:

* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* 다음과 같은 원본 복사 형식이 추가되었습니다.
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>4.10.0 버전
* 다음 선택적 속성이 TextFormat에 추가되었습니다.
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* 다음과 같은 원본 복사 형식이 추가되었습니다.
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) 속성을 AzureMLBatchExecutionActivity에 추가합니다.
  * 다중 웹 서비스 입력을 Azure Machine Learning 실험에 전달하도록 설정합니다.

## <a name="version-491"></a>버전 4.9.1
### <a name="bug-fix"></a>버그 수정
* [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)에 대한 WebApi 기반 인증이 더 이상 사용되지 않습니다.

## <a name="version-490"></a>버전 4.9.0
### <a name="feature-additions"></a>기능 추가 사항
* CopyActivity에 [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) 및 [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) 속성을 추가합니다. 기능에 대한 자세한 내용은 [준비된 복사](data-factory-copy-activity-performance.md#staged-copy) 를 참조하세요.

### <a name="bug-fix"></a>버그 수정
* [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) 인스턴스를 사용하는 [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) 메서드의 오버로드를 지정합니다.
* [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) 및 [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink)를 CopySink에 옵션으로 표시합니다.

## <a name="version-480"></a>버전 4.8.0
### <a name="feature-additions"></a>기능 추가 사항
* 복사 성능을 조정할 수 있도록 하기 위해 복사 작업 유형에 다음과 같은 옵션 속성이 추가되었습니다.
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>버전 4.7.0
### <a name="feature-additions"></a>기능 추가 사항
* 파일을 ORC(Optimized Row Columnar) 형식으로 복사하기 위해 새 StorageFormat 형식 [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) 형식이 추가되었습니다.
* [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) 및 PolyBaseSettings 속성을 SqlDWSink에 추가합니다.
  * PolyBase를 사용하여 Azure Synapse Analytics로 데이터를 복사할 수 있습니다.

## <a name="version-461"></a>버전 4.6.1
### <a name="bug-fixes"></a>버그 픽스
* 작업 창을 나열하기 위한 HTTP 요청을 해결합니다.
  * 요청 페이로드에서 리소스 그룹 이름 및 데이터 팩터리 이름을 제거합니다.

## <a name="version-460"></a>버전 4.6.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음 속성이 [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)에 추가되었습니다.
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [데이터 세트](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* 다음 속성이 [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)에 추가되었습니다.
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* 데이터가 JSON 형식인 데이터 세트를 정의하는 [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) 유형 [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) 유형이 새로 추가되었습니다.

## <a name="version-450"></a>버전 4.5.0
### <a name="feature-additions"></a>기능 추가 사항
* [작업 창에 대한 작업 목록](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)이 추가되었습니다.
  * 엔터티 형식(즉, 데이터 팩터리, 데이터 세트, 파이프라인 및 작업)을 기준으로 필터를 사용하여 작업 창을 검색하는 메서드가 추가되었습니다.
* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* 다음과 같은 원본 복사 형식이 추가되었습니다.     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>버전 4.4.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음 연결된 서비스 유형이 복사 작업에 대한 데이터 원본 및 싱크로 추가되었습니다.
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). 개념 정보 및 예제는 [Azure Storage SAS 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 를 참조하세요.

## <a name="version-430"></a>버전 4.3.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음 연결된 서비스 유형이 복사 작업에 대한 데이터 원본으로 추가되었습니다.
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). 개념 정보 및 예제는 [Data Factory를 사용하여 HDFS에서 데이터 이동](data-factory-hdfs-connector.md) 을 참조하세요.
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). 개념 정보 및 예제는 [Azure Data Factory를 사용하여 ODBC 데이터 저장소에서 데이터 이동](data-factory-odbc-connector.md) 을 참조하세요.

## <a name="version-420"></a>버전 4.2.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음과 같은 새 활동 유형을 추가되었습니다. [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity) [업데이트 리소스 활동을 사용하여 Azure ML 모델 업데이트](data-factory-azure-ml-batch-execution-activity.md)를 참조하세요.
* 새 선택 속성 [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)가 [AzureMLLinkedService class](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)에 추가되었습니다.
* [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 및 [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 속성이 [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 클래스에 추가되었습니다.
* 데이터 팩터리 서비스에 대한 클라이언트 호출의 시간 제한 구성을 허용합니다.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* 다음 활동 유형이 추가 되었습니다.
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* 다음 복사 작업에 대한 원본 및 싱크 유형은 추가 되었습니다.
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>버전 4.0.1
### <a name="breaking-changes"></a>주요 변경 내용
다음 클래스의 이름이 변경되었습니다. 새 이름은 4.0.0 릴리스 전의 클래스 원래 이름이었습니다.

| 4.0.0의 이름 | 4.0.1의 이름 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>버전 4.0.0

### <a name="breaking-changes"></a>주요 변경 내용

* 다음 클래스/인터페이스의 이름이 변경되었습니다.

| 이전 이름 | 새 이름 |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| 테이블 |[데이터 세트](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* **List** 메서드는 이제 페이징된 결과를 반환합니다. 응답에 비어 있지 않은 **NextLink** 속성이 포함된 경우 클라이언트 애플리케이션은 모든 페이지가 반환될 때까지 다음 페이지를 계속 가져와야 합니다.  예를 들면 다음과 같습니다.

  ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
  ```

* **List** 파이프라인 API는 전체 세부 정보 대신 파이프라인의 요약만 반환합니다. 예를 들어 파이프라인 요약의 작업에는 이름과 형식만 포함됩니다.

### <a name="feature-additions"></a>기능 추가 사항
* [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) 클래스는 두 가지 새로운 속성인 **SliceIdentifierColumnName** 및 **SqlWriterCleanupScript** 를 지원하여 Azure Synapase Analytics로의 idempotent 복사를 지원합니다. 이러한 속성에 대한 자세한 내용은 [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) 문서를 참조하세요.
* 이제 복사 작업의 일부로 Azure SQL Database 및 Azure Synapse Analytics 소스에 대한 저장 프로시저 실행을 지원합니다. [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource)와 [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) 클래스에는 **SqlReaderStoredProcedureName** 과 **StoredProcedureParameters** 속성이 있습니다. 이러한 속성에 대한 자세한 내용은 Azure.com에서 [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) 및 [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) 문서를 참조하세요.