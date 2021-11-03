---
title: Azure Blob Storage 간 데이터 복사
description: 'Azure Data Factory에서 Blob 데이터를 복사하는 방법을 알아봅니다. 샘플 사용: Azure Blob Storage 및 Azure SQL Database 간에 데이터를 복사하는 방법입니다.'
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cd797c58edb1d6918c8681f9cadffecdba8ef827
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011468"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob Storage 사이에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-azure-blob-connector.md)
> * [버전 2(현재 버전)](../connector-azure-blob-storage.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 Azure Blob Storage 커넥터](../connector-azure-blob-storage.md)를 참조하세요.


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Blob Storage 사이에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

## <a name="overview"></a>개요
모든 지원되는 원본 데이터 저장소에서 Azure Blob Storage로 또는 Azure Blob Storage에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소 목록을 제공합니다. 예를 들어 SQL Server 데이터베이스 또는 Azure SQL Database의 데이터베이스 **에서** Azure Blob Storage **로** 데이터를 이동할 수 있습니다. 그리고 데이터를 Azure Blob Storage **에서** Azure Synapse Analytics 또는 Azure Cosmos DB 컬렉션 **으로** 복사할 수 있습니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>지원되는 시나리오
**Azure Blob Storage에서** 다음 데이터 저장소로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sink](includes/data-factory-supported-sinks.md)]

다음 데이터 저장소에서 **Azure Blob Storage로** 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sources](includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> 복사 작업은 범용 Azure Storage 계정 및 핫/쿨 Blob Storage 간의 데이터 복사를 모두 지원합니다. 이 작업은 **블록에서 읽기, 추가 또는 페이지 Blob** 를 지원하며 **블록 Blob에 쓰기만** 지원합니다. Azure Premium Storage는 페이지 Blob으로 지원되므로 싱크로 지원하지 않습니다.
>
> 복사 작업 시 데이터가 대상에 성공적으로 복사된 후 원본에서 데이터가 삭제되지 않습니다. 성공적으로 복사한 후에 원본 데이터를 삭제해야 하는 경우 데이터를 삭제하는 [사용자 지정 활동](data-factory-use-custom-activities.md)을 만들고 파이프라인에서 해당 작업을 사용합니다. 예를 들어 [GitHub의 blob 또는 폴더 삭제 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity)을 참조하세요.

## <a name="get-started"></a>시작
다른 도구/API를 사용하여 Azure Blob Storage 간에 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사** 를 사용하는 것입니다. 이 문서에는 Azure Blob Storage 위치에서 다른 Azure Blob Storage 위치로 데이터를 복사하기 위한 파이프라인을 만드는 [연습](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) 과정이 나와 있습니다. Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하기 위한 파이프라인 생성에 대한 자습서를 보려면 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API**, **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. **데이터 팩터리** 를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다.
2. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다. 예를 들어 Azure Blob Storage에서 Azure SQL 데이터베이스로 데이터를 복사하는 경우 Azure 스토리지 계정 및 Azure SQL Database를 데이터 팩터리에 연결하는 두 개의 연결된 서비스를 만듭니다. Azure Blob Storage와 관련된 연결된 서비스 속성은 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다. 마지막 단계에서 설명한 예제에서는 입력 데이터가 포함된 BLOB 컨테이너 및 폴더를 지정하는 데이터 세트를 만듭니다. 그리고 Blob Storage에서 복사한 데이터를 포함하는 Azure SQL Database의 SQL 테이블을 지정하는 또 다른 데이터 세트를 만듭니다. Azure Blob Storage와 관련된 데이터 세트 속성은 [데이터 세트 속성](#dataset-properties) 섹션을 참조하세요.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다. 앞에서 언급한 예에서는 BlobSource를 원본으로, SqlSink를 복사 작업의 싱크로 사용합니다. 마찬가지로, Azure SQL Database에서 Azure Blob Storage로 복사하는 경우 복사 작업에 SqlSource 및 BlobSink를 사용합니다. Azure Blob Storage와 관련된 복사 작업 속성은 [복사 작업 속성](#copy-activity-properties) 섹션을 참조하세요. 원본 또는 싱크로 데이터 저장소를 사용하는 방법에 대 한 자세한 내용을 보려면 데이터 저장소에 대한 이전 섹션의 링크를 클릭하세요.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다.  다른 곳에서 Azure Blob Storage로 또는 그 반대로 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 예](#json-examples-for-copying-data-to-and-from-blob-storage  ) 섹션을 참조하세요.

다음 섹션에서는 Azure Blob Storage에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
Azure Storage를 Azure Data Factory에 연결하는 데 사용할 수 있는 두 가지 유형의 연결된 서비스가 있습니다. **AzureStorage** 연결된 서비스 및 **AzureStorageSas** 연결된 서비스입니다. Azure Storage 연결된 서비스는 Azure Storage에 대한 전역 액세스로 Data Factory를 제공합니다. 반면 Azure Storage SAS(공유 액세스 서명) 연결된 서비스는 Azure Storage에 대한 제한/시간 제한 액세스로 Data Factory를 제공합니다. 이 두 연결된 서비스에는 다른 차이가 없습니다. 필요에 맞는 연결된 서비스를 선택합니다. 다음 섹션에서는 이러한 두 연결된 서비스에 대한 자세한 정보를 제공합니다.

[!INCLUDE [data-factory-azure-storage-linked-services](includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>데이터 세트 속성
Azure Blob Storage에서 입력 또는 출력 데이터를 표시할 데이터 세트를 지정하려면 데이터 세트의 형식 속성을 **AzureBlob** 으로 설정합니다. 데이터 세트의 **linkedServiceName** 속성을 Azure Storage 또는 Azure Storage SAS 연결된 서비스의 이름으로 설정합니다.  데이터 세트의 형식 속성은 Blob Storage에서 **Blob 컨테이너** 와 **폴더** 를 지정합니다.

데이터 세트 정의에 사용할 수 있는 JSON 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

데이터 팩터리는 Azure Blob과 같은 읽기 시 스키마 데이터 원본에 대하여 “structure”의 형식 정보를 제공하는 데 다음과 같은 CLS 규격 .NET 기반 형식 값을 지원합니다. Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. 데이터 팩터리는 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사할 때 형식 변환을 자동으로 수행합니다.

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치, 서식 등에 대한 정보를 제공합니다. **AzureBlob** 데이터 세트 형식의 데이터 세트에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |Blob Storage에서 컨테이너 및 폴더에 대한 경로입니다. 예제: myblobcontainer\myblobfolder\ |예 |
| fileName |Blob의 이름입니다. fileName은 선택 사항이며 대/소문자를 구분합니다.<br/><br/>filename을 지정하는 경우 활동(복사 포함)은 특정 Blob에서 작동합니다.<br/><br/>fileName을 지정하지 않으면 복사는 입력 데이터 세트에 대한 folderPath에 모든 Blob을 포함합니다.<br/><br/>**fileName** 이 출력 데이터 세트에 대해 지정되지 않고 **preserveHierarchy** 가 활동 싱크에 지정되지 않은 경우, 생성된 파일의 이름은 다음 형식을 사용합니다. `Data.<Guid>.txt`(예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |예 |
| partitionedBy |partitionedBy는 선택적 속성입니다. 동적 folderPath 및 시계열 데이터에 대한 filename을 지정하는 데 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대한 매개 변수화됩니다. 자세한 내용과 예제는 [partitionedBy 속성 사용 섹션](#using-partitionedby-property)을 참조하세요. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

### <a name="using-partitionedby-property"></a>partitionedBy 속성 사용
이전 섹션에서 설명했듯이 **partitionedBy** 속성, [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md)를 사용하여 시계열 데이터의 동적 folderPath와 filename을 지정할 수 있습니다.

시간 시계열 데이터 세트, 예약 및 조각에 대한 자세한 내용은 [데이터 세트 만들기](data-factory-create-datasets.md) 및 [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

#### <a name="sample-1"></a>샘플 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

이 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 Data Factory 시스템 변수 SliceStart 값으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다

#### <a name="sample-2"></a>샘플 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

이 예제에서 SliceStart의 연도, 월, 일 및 시간은 folderPath 및 fileName 속성에서 사용하는 별도 변수로 추출됩니다.

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 데이터 세트, 정책 등의 속성은 모든 유형의 활동에 사용할 수 있습니다. 반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다. Azure Blob Storage에서 데이터를 이동하는 경우 복사 작업의 원본 유형을 **BlobSource** 로 설정합니다. 마찬가지로 Azure Blob Storage로 데이터를 이동하는 경우 복사 작업의 싱크 유형을 **BlobSink** 로 설정합니다. 이 섹션에서는 BlobSource 및 BlobSink에서 지원되는 속성의 목록을 제공합니다.

**BlobSource** 는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True(기본값), False |예 |

**BlobSink** 는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| copyBehavior |원본이 BlobSource 또는 FileSystem인 경우 복사 동작을 정의합니다. |<b>PreserveHierarchy</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/><b>FlattenHierarchy</b>: 원본 폴더의 모든 파일은 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><br/><b>MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다. |예 |

**BlobSource** 에서도 이전 버전과의 호환성을 위해 이러한 두 속성을 지원합니다.

* **treatEmptyAsNull**: Null 또는 빈 문자열을 null 값으로 처리할지 여부를 지정합니다.
* **skipHeaderLineCount** - 건너뛰어야 하는 줄 수를 지정합니다. 입력 데이터 세트가 TextFormat을 사용하는 경우에만 해당합니다.

마찬가지로, **BlobSink** 는 이전 버전과의 호환성을 위해 다음 속성을 지원합니다.

* **blobWriterAddHeader**: 출력 데이터 세트에 쓰는 동안 열 정의의 헤더를 추가할지 여부를 지정합니다.

이제 데이터 세트는 동일한 기능을 구현하는 다음 속성을 지원합니다. **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**

다음 표에서는 Blob 원본/싱크 속성 대신 새 데이터 세트 속성을 사용하는 지침을 제공합니다.

| 복사 작업 속성 | 데이터 세트 속성 |
|:--- |:--- |
| BlobSource에서 skipHeaderLineCount |skipLineCount 및 firstRowAsHeader. 줄을 먼저 건너뛴 다음 첫 번째 행을 헤더로 읽습니다. |
| BlobSource에서 treatEmptyAsNull |입력 데이터 세트에서 treatEmptyAsNull |
| BlobSink에서 blobWriterAddHeader |출력 데이터 세트에서 firstRowAsHeader |

이러한 속성에 대한 자세한 내용은 [TextFormat 지정](data-factory-supported-file-and-compression-formats.md#text-format) 섹션을 참조하세요.

### <a name="recursive-and-copybehavior-examples"></a>recursive 및 copyBehavior 예제
이 섹션에서는 다양한 recursive 및 copyBehavior 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 결과 동작 |
| --- | --- | --- |
| true |preserveHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. |
| false |preserveHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles |다음 구조를 가진 원본 폴더 Folder1의 경우:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. File1에 대해 자동으로 생성된 이름<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>연습: 복사 마법사를 사용하여 Blob Storage 간 데이터 복사
이 스토리지를 만든 후에 쉽게 탑재 및 공유할 수 있도록 하려면 Azure File Storage 정보를 텍스트 파일에 저장하고 해당 위치에 대한 경로를 기록합니다. 이 연습에 나오는 원본 및 대상 데이터 저장소의 유형은 모두 Azure Blob Storage입니다. 이 연습의 파이프라인은 한 폴더의 데이터를 동일한 Blob 컨테이너의 다른 폴더로 복사합니다. 이 연습에서는 Blob Storage를 원본 또는 싱크로 사용할 때 설정 또는 속성을 표시하는 것이 간단합니다.

### <a name="prerequisites"></a>필수 조건
1. 아직 만들지 않은 경우 범용 **Azure Storage 계정** 을 만듭니다. 이 연습에서는 Blob Storage를 **원본** 및 **대상** 데이터 스토리지로 사용합니다. Azure Storage 계정이 없는 경우 새로 만드는 단계는 [스토리지 계정 만들기](../../storage/common/storage-account-create.md) 문서를 참조하세요.
2. 스토리지 계정에 **adfblobconnector** 라는 Blob 컨테이너를 만듭니다.
4. **adfblobconnector** 컨테이너에 **input** 이라는 폴더를 만듭니다.
5. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)와 같은 도구를 사용하여 다음 내용이 포함된 **emp.txt** 라는 파일을 만들어 **input** 폴더에 업로드합니다.
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>데이터 팩터리 만들기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위 모서리에서 **리소스 만들기** 를 클릭하고 **인텔리전스 + 분석** 및 **Data Factory** 를 차례로 클릭합니다.
3. **새 데이터 팩터리** 창에서 다음을 수행합니다.  
    1. **이름** 으로 **ADFBlobConnectorDF** 입력합니다. Azure Data Factory 이름은 전역적으로 고유해야 합니다. `*Data factory name "ADFBlobConnectorDF" is not available` 오류가 표시되면 데이터 팩터리 이름을 변경하고(예: yournameADFBlobConnectorDF) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
    2. Azure **구독** 을 선택합니다.
    3. 리소스 그룹에 대해 **기존 리소스 그룹 사용** 을 선택하고 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 리소스 그룹의 이름을 입력합니다.
    4. Data Factory의 **위치** 를 선택합니다.
    5. 블레이드 하단에서 **대시보드에 고정** 확인란을 선택합니다.
    6. **만들기** 를 클릭합니다.
3. 만들기가 완료되면 다음 이미지와 같이 **데이터 팩터리** 블레이드가 나타납니다. :::image type="content" source="./media/data-factory-azure-blob-connector/data-factory-home-page.png" alt-text="데이터 팩터리 홈페이지":::

### <a name="copy-wizard"></a>복사 마법사
1. 데이터 팩터리 홈 페이지에서 **데이터 복사** 타일을 클릭하여 **데이터 복사 마법사** 를 별도의 탭에서 시작합니다.  

    > [!NOTE]
    > 웹 브라우저가 "권한 부여..." 상태로 중지된 것을 확인하면 **타사 쿠키 및 사이트 데이터 차단** 설정을 사용 안 함/선택 취소하고 (또는) 계속 사용하지 않습니다. 그리고 **login.microsoftonline.com** 에 대한 예외를 만든 다음 마법사를 다시 시작해봅니다.
2. **속성** 페이지에서 다음을 수행합니다.
    1. **작업 이름** 에 대해 **CopyPipeline** 을 입력합니다. 태스크 이름은 데이터 팩터리의 파이프라인 이름입니다.
    2. 작업에 대한 **설명** 을 입력합니다(선택 사항).
    3. **작업 흐름 또는 작업 일정** 에 대해서는 **일정에 따라 정기적으로 실행** 옵션을 유지합니다. 스케줄에서 반복적으로 실행하는 대신 이 태스크를 한 번만 실행하려면 **지금 한 번 실행** 을 선택합니다. **지금 한 번 실행** 옵션을 선택하면 [일회성 파이프라인](data-factory-create-pipelines.md#onetime-pipeline)이 만들어집니다.
    4. **되풀이 패턴** 에 대한 설정을 유지합니다. 이 작업은 다음 단계에서 지정한 시작 시간과 종료 시간 사이에 매일 실행됩니다.
    5. **시작 날짜 시간** 을 **04/21/2017** 로 변경합니다.
    6. **종료 날짜 시간** 을 **04/25/2017** 로 변경합니다. 캘린더를 탐색하는 대신 날짜를 입력할 수 있습니다.
    8. **다음** 을 클릭합니다.
        :::image type="content" source="./media/data-factory-azure-blob-connector/copy-tool-properties-page.png" alt-text="복사 도구 - 속성 페이지":::
3. **원본 데이터 저장소** 페이지에서 **Azure Blob Storage** 타일을 클릭합니다. 이 페이지를 사용하여 복사 작업에 사용할 원본 데이터 저장소를 지정합니다. 기존 데이터 저장소 연결된 서비스를 사용하거나 새 데이터 저장소를 지정할 수 있습니다. 기존의 연결된 서비스를 사용하려면 **기존의 연결된 서비스에서** 를 클릭하고 올바로 연결된 서비스를 선택합니다.
    :::image type="content" source="./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png" alt-text="복사 도구 - 원본 데이터 저장소 페이지":::
4. **Azure Blob Storage 계정 지정** 페이지에서 다음을 수행합니다.
    1. **연결 이름** 에 대해 자동 생성된 이름을 유지합니다. 연결 이름은 Azure Storage 유형의 링크된 서비스의 이름입니다.
    2. **계정 선택 방법** 에 **Azure 구독에서** 옵션이 선택되었는지 확인합니다.
    3. Azure 구독을 선택하거나 **Azure 구독** 에 대해 **모두 선택** 을 유지합니다.
    4. 선택한 구독에서 사용할 수 있는 Azure Storage 계정 목록에서 **Azure Storage 계정** 을 선택합니다. **계정 선택 방법** 으로 **수동으로 입력** 옵션을 선택하여 스토리지 계정 설정을 수동으로 입력할 수도 있습니다.
    5. **다음** 을 클릭합니다.  
        :::image type="content" source="./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png" alt-text="복사 도구 - Azure Blob Storage 계정 지정 페이지":::
5. **입력 파일 또는 폴더 선택** 페이지에서 다음을 수행합니다.
    1. **adfblobcontainer** 를 두 번 클릭합니다.
    2. **input** 을 선택하고 **선택** 을 클릭합니다. 이 연습에서는 입력 폴더를 선택합니다. 대신 폴더에서 emp.txt 파일을 선택할 수도 있습니다.
        :::image type="content" source="./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png" alt-text="복사 도구 - 입력 파일 또는 폴더 1 선택":::
6. **입력 파일 또는 폴더 선택** 페이지에서 다음을 수행합니다.
    1. **파일 또는 폴더** 가 **adfblobconnector/input** 으로 설정되었는지 확인합니다. 파일이 하위 폴더(예: 2017/04/01, 2017/04/02)에 있는 경우 파일 또는 폴더에 대해 adfblobconnector/input/{year}/{month}/{day}를 입력합니다. 텍스트 상자 밖으로 TAB을 누르면 연도(yyyy), 월(MM) 및 일(dd)의 형식을 선택하는 세 개의 드롭다운 목록이 나타납니다.
    2. **파일을 재귀적으로 복사** 는 설정하지 마십시오. 대상에 복사할 파일의 폴더를 반복적으로 탐색하려면 이 옵션을 선택합니다.
    3. **이진 복사** 옵션을 사용하지 마십시오. 원본 파일을 대상으로 이진 복사본을 수행하려면 이 옵션을 선택합니다. 이 연습을 선택하지 마십시오. 다음 페이지에서 더 많은 옵션을 볼 수 있습니다.
    4. **압축 유형** 이 **없음** 으로 설정되었는지 확인합니다. 소스 파일이 지원되는 형식 중 하나로 압축된 경우 이 옵션의 값을 선택합니다.
    5. **다음** 을 클릭합니다.
    :::image type="content" source="./media/data-factory-azure-blob-connector/chose-input-file-folder.png" alt-text="복사 도구 - 입력 파일 또는 폴더 2 선택":::
7. **파일 형식 설정** 페이지에 파일을 구문 분석하여 마법사에 의해 자동으로 감지되는 구분 기호와 스키마가 표시됩니다.
    1. 다음 옵션을 확인합니다.  
        a. **파일 형식** 이 **텍스트 형식** 으로 설정되었습니다. 드롭다운 목록에서 지원되는 모든 형식을 볼 수 있습니다. 예: JSON, Avro, ORC, Parquet.
       b. **열 구분 기호** 가 `Comma (,)`로 설정되었습니다. 드롭다운 목록에서 Data Factory가 지원하는 다른 열 구분 기호를 볼 수 있습니다. 사용자 지정 구분 기호를 지정할 수도 있습니다.
       다. **행 구분 기호** 가 `Carriage Return + Line feed (\r\n)`로 설정되었습니다. 드롭다운 목록에서 Data Factory가 지원하는 다른 행 구분 기호를 볼 수 있습니다. 사용자 지정 구분 기호를 지정할 수도 있습니다.
       d. **건너뛸 줄 수** 가 **0** 으로 설정되었습니다. 파일 맨 위에서 몇 줄을 건너뛰려면 여기에 숫자를 입력합니다.
       e. **첫 번째 데이터 행에 열 이름 포함** 이 설정되었습니다. 원본 파일에 첫 번째 행에 열 이름이 있으면 이 옵션을 선택합니다.
       f. **빈 열 값을 Null로 간주합니다** 옵션이 설정되었습니다.
    2. **고급 설정** 을 확장하여 사용 가능한 고급 옵션을 확인합니다.
    3. 페이지 맨 아래에서 emp.txt 파일의 데이터 **미리 보기** 를 참조하세요.
    4. 아래쪽의 **스키마** 탭을 클릭하면 원본 파일의 데이터를 보고 복사 마법사가 추론한 스키마를 볼 수 있습니다.
    5. 구분 기호를 검토하고 데이터를 미리 본 후에 **다음** 을 클릭합니다.
    :::image type="content" source="./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png" alt-text="복사 도구 - 파일 형식 설정":::
8. **대상 데이터 저장소 페이지** 에서 **Azure Blob Storage** 를 선택하고 **다음** 을 클릭합니다. 이 연습에서는 Azure Blob Storage를 원본 및 대상 데이터 저장소로 사용하고 있습니다.  
    :::image type="content" source="media/data-factory-azure-blob-connector/select-destination-data-store.png" alt-text="복사 도구 - 대상 데이터 저장소 선택":::
9. **Azure Blob Storage 계정 지정** 페이지에서 다음을 수행합니다.  
    1. **연결 이름** 필드에 **AzureStorageLinkedService** 를 입력합니다.
    2. **계정 선택 방법** 에 **Azure 구독에서** 옵션이 선택되었는지 확인합니다.
    3. Azure **구독** 을 선택합니다.
    4. Azure Storage 계정을 선택합니다.
    5. **다음** 을 클릭합니다.
10. **출력 파일 또는 폴더 선택** 페이지에서 다음을 수행합니다.  
    1. **폴더 경로** 를 **adfblobconnector/output/{year}/{month}/{day}** 로 지정합니다. **탭** 을 입력합니다.
    1. **년** 에 대해 **yyyy** 를 선택합니다.
    1. **월** 에 대해 **MM** 으로 설정되어 있는지 확인합니다.
    1. **일** 에 대해 **dd** 로 설정되어 있는지 확인합니다.
    1. **압축 유형** 이 **없음** 으로 설정되었는지 확인합니다.
    1. **복사 동작** 이 **파일 병합** 으로 설정되어 있는지 확인합니다. 같은 이름의 출력 파일이 이미 존재하면 새로운 내용이 끝에 동일한 파일에 추가됩니다.
    1. **다음** 을 클릭합니다.
       :::image type="content" source="media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png" alt-text="복사 도구 - 출력 파일 또는 폴더 선택":::
11. **파일 형식 설정** 페이지에서 설정을 검토하고 **다음** 을 클릭합니다. 추가 옵션 중 하나는 출력 파일에 헤더를 추가하는 것입니다. 이 옵션을 선택하면 원본의 스키마에서 열 이름과 함께 머리글 행이 추가됩니다. 원본에 대한 스키마를 볼 때 기본 열 이름의 이름을 바꿀 수 있습니다. 예를 들어 첫 번째 열을 이름으로, 두 번째 열을 성으로 변경할 수 있습니다. 그런 다음 출력 파일은 이러한 이름을 가진 머리글을 열 이름으로 생성합니다.
    :::image type="content" source="media/data-factory-azure-blob-connector/file-format-destination.png" alt-text="복사 도구 - 대상의 파일 형식 설정":::
12. **성능 설정** 페이지에서 **클라우드 단위** 및 **병렬 사본** 이 **자동** 으로 설정되었는지 확인하고 다음을 클릭합니다. 이러한 설정에 대한 자세한 내용은 [복사 활동 성능 및 튜닝 가이드](data-factory-copy-activity-performance.md#parallel-copy)를 참조하세요.
    :::image type="content" source="media/data-factory-azure-blob-connector/copy-performance-settings.png" alt-text="복사 도구 - 성능 설정":::
14. **요약** 페이지에서 모든 설정(작업 속성, 원본 및 대상 설정 및 복사 설정)을 검토하고 **다음** 을 클릭합니다.
    :::image type="content" source="media/data-factory-azure-blob-connector/copy-tool-summary-page.png" alt-text="복사 도구 - 요약 페이지":::
15. **요약** 페이지에서 정보를 검토하고 **마침** 을 클릭합니다. 마법사는 데이터 팩터리(복사 마법사를 실행한 위치)에 두 개의 연결된 서비스, 두 개의 데이터 세트(입력 및 출력), 하나의 파이프라인을 만듭니다.
    :::image type="content" source="media/data-factory-azure-blob-connector/copy-tool-deployment-page.png" alt-text="복사 도구 - 배포 페이지":::

### <a name="monitor-the-pipeline-copy-task"></a>파이프라인(복사 활동) 모니터링

1. **배포** 페이지에서 `Click here to monitor copy pipeline` 링크를 클릭합니다.
2. **애플리케이션 모니터 및 관리** 를 별도의 탭에서 보아야 합니다. :::image type="content" source="media/data-factory-azure-blob-connector/monitor-manage-app.png" alt-text="앱 모니터 및 관리":::
3. 상단의 **시작** 시간을 `04/19/2017` 및 **종료** 시간을 `04/27/2017`로 변경한 다음 **적용** 을 클릭합니다.
4. **활동 창** 목록에 5개의 활동 창이 표시됩니다. **WindowStart** 시간은 파이프라인 시작부터 파이프라인 종료 시간까지의 모든 날을 포함해야 합니다.
5. 모든 활동 창의 상태가 준비로 설정될 때까지 **활동 창** 목록에 대한 **새로 고침** 단추를 몇 번 클릭합니다.
6. 이제 출력 파일이 adfblobconnector 컨테이너의 출력 폴더에 생성되었는지 확인합니다. 출력 폴더에 다음 폴더 구조가 표시되어야 합니다.

    ```output
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```

    데이터 팩터리 모니터링 및 관리에 대한 자세한 내용은 [데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) 문서를 참조하세요.

### <a name="data-factory-entities"></a>데이터 팩터리 엔터티
이제 데이터 팩터리 홈 페이지로 다시 탭으로 전환합니다. 현재 데이터 팩터리에는 두 개의 연결된 서비스, 두 개의 데이터 세트 및 한 개의 파이프라인이 있습니다.

:::image type="content" source="media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png" alt-text="엔터티가 있는 데이터 팩터리 홈 페이지":::

**작성자를 클릭하고 배포** 하여 데이터 팩터리 편집기를 시작합니다.

:::image type="content" source="media/data-factory-azure-blob-connector/data-factory-editor.png" alt-text="데이터 팩터리 편집기":::

데이터 팩터리에 다음과 같은 데이터 팩터리 엔티티가 표시되어야 합니다.

- 두 개의 연결된 서비스. 하나는 원본을 위한 것이고 다른 하나는 목적지를 위한 것입니다. 연결된 서비스는 모두 이 연습에서 동일한 Azure Storage 계정을 참조합니다.
- 두 개의 데이터 세트입니다. 입력 데이터 세트 및 출력 데이터 세트. 이 연습에서는 둘 다 동일한 Blob 컨테이너를 사용하지만 다른 폴더(입력 및 출력)를 참조합니다.
- 파이프라인입니다. 파이프라인에는 Blob 원본 및 Blob 싱크를 사용하여 Azure Blob 위치에서 다른 Azure Blob 위치로 데이터를 복사하는 복사 활동이 포함되어 있습니다.

다음 섹션에서는 이러한 요소에 대한 자세한 정보를 제공합니다.

#### <a name="linked-services"></a>연결된 서비스
두 개의 연결된 서비스가 표시되어야 합니다. 하나는 원본을 위한 것이고 다른 하나는 목적지를 위한 것입니다. 이 연습에서는 이름을 제외하고 두 정의가 모두 동일하게 보입니다. 연결된 서비스의 **형식** 이 **AzureStorage** 로 설정되었습니다. 연결된 서비스 정의의 가장 중요한 속성은 **connectionString** 입니다. 이는 Data Factory에서 런타임에 Azure Storage 계정에 연결하는 데 사용됩니다. 정의에서 hubName 특성을 무시합니다.

##### <a name="source-blob-storage-linked-service"></a>(선택 사항) 전체 팀에게 유용한 데이터 자산을 저장하는 데 사용할 Azure File Storage를 만듭니다.
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Azure File Storage 정보 파일이 있는지 묻는 메시지가 나타나면 Enter 또는 y를 클릭하고 이전 단계에서 만든 파일의 *전체 경로와 이름을 입력합니다.

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Azure Storage 링크된 서비스에 대한 자세한 내용은 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.

#### <a name="datasets"></a>데이터 세트
입력 데이터 세트와 출력 데이터 세트의 두 가지 데이터 세트가 있습니다. 데이터 세트의 유형은 둘 다 **AzureBlob** 으로 설정됩니다.

입력 데이터 세트는 **adfblobconnector** Blob 컨테이너의 **input** 폴더를 가리킵니다. 이 데이터 세트를 입력으로 사용하는 복사 활동으로 파이프라인에서 데이터를 생성하지 않으므로 이 데이터 세트에 대해 **external** 속성이 **true** 로 설정됩니다.

출력 데이터 세트는 동일한 Blob 컨테이너의 **output** 폴더를 가리킵니다. 출력 데이터 세트는 **SliceStart** 시스템 변수의 년, 월, 일을 사용하여 출력 파일의 경로를 동적으로 평가합니다. Data Factory에서 지원하는 함수 및 시스템 변수 목록은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md)를 참조하세요. 이 데이터 세트가 파이프라인에 의해 생성되기 때문에 **external** 속성은 **false**(기본값)로 설정됩니다.

Azure Blob 데이터 세트가 지원하는 속성에 대한 자세한 내용은 [데이터 세트 속성](#dataset-properties) 섹션을 참조하세요.

##### <a name="input-dataset"></a>입력 데이터 세트

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>출력 데이터 세트

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>파이프라인
파이프라인에는 하나의 활동만 있습니다. 활동의 **type** 은 **Copy** 로 설정합니다. 활동의 유형 특성에는 원본과 싱크에 대한 두 개의 섹션이 있습니다. 활동 유형이 Blob Storage 영역에서 데이터를 복사할 때 원본 유형은 **BlobSource** 로 설정됩니다. 싱크 유형은 **BlobSink** 로 설정되어 데이터를 Blob Storage에 복사합니다. 복사 활동은 InputDataset-z4y를 입력으로, OutputDataset-z4y를 출력으로 사용합니다.

BlobSource 및 BlobSink에서 지원하는 속성에 대한 자세한 내용은 [복사 활동 속성](#copy-activity-properties) 섹션을 참조하세요.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Blob Storage로/에서 데이터를 복사하는 JSON 예제
다음 예에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure Blob Storage 및 Azure SQL Database 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 **여기** 에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON 예제: Blob Storage에서 SQL Database로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](#linked-service-properties) 형식의 연결된 서비스
3. [AzureBlob](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)입니다.
5. [BlobSource](#copy-activity-properties) 및 [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure Blob에서 Azure SQL 테이블로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 연결된 서비스:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage 연결된 서비스:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory는 두 가지 유형의 Azure Storage 연결된 서비스: **AzureStorage** 및 **AzureStorageSas** 를 제공합니다. 첫 번째 것의 경우 계정 키를 포함하는 연결 문자열을 지정하고 이후 것의 경우 SAS(공유 액세스 서명) Uri를 지정합니다. 자세한 내용은 [연결된 서비스](#linked-service-properties) 섹션을 참조하세요.

**Azure Blob 입력 데이터 세트:**

데이터는 매시간 새 blob에 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. “external”: “true”를 설정하면 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 활동에 의해 생성되지 않는다는 사실이 Data Factory에 전달됩니다.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL 출력 데이터 세트:**

샘플은 Azure SQL Database의 “MyTable”이라는 테이블에 데이터를 복사합니다. Blob CSV 파일이 포함할 것으로 예상한 것과 같은 수의 열을 사용하여 SQL 데이터베이스에 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Blob 원본 및 SQL 싱크를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource** 로 설정되고 **싱크** 형식은 **SqlSink** 로 설정됩니다.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON 예제: Azure SQL에서 Azure Blob으로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](#linked-service-properties) 형식의 연결된 서비스
3. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [AzureBlob](#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
5. [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) 및 [BlobSink](#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure SQL 테이블에서 Azure Blob으로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 연결된 서비스:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage 연결된 서비스:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory는 두 가지 유형의 Azure Storage 연결된 서비스: **AzureStorage** 및 **AzureStorageSas** 를 제공합니다. 첫 번째 것의 경우 계정 키를 포함하는 연결 문자열을 지정하고 이후 것의 경우 SAS(공유 액세스 서명) Uri를 지정합니다. 자세한 내용은 [연결된 서비스](#linked-service-properties) 섹션을 참조하세요.

**Azure SQL 입력 데이터 세트:**

샘플은 Azure SQL에서 “MyTable” 테이블을 만들었고 여기에 시계열 데이터에 대한 “timestampcolumn”이라는 열이 포함되었다고 가정합니다.

“external”: “true”를 설정하면 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob 출력 데이터 세트:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**SQL 원본 및 Blob 싱크를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **SqlSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
