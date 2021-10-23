---
title: Azure Data Factory를 사용하여 파일 시스템에서 데이터 복사
description: Azure Data Factory를 사용하여 온-프레미스 파일 시스템에서 데이터를 복사하는 방법에 대해 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: be951add54590d91c9f405887df8817942f83dd8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242844"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Azure Data Factory를 통한 온-프레미스 파일 시스템에서의 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-onprem-file-system-connector.md)
> * [버전 2(현재 버전)](../connector-file-system.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 파일 시스템 커넥터](../connector-file-system.md)를 참조하세요.


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스 파일 시스템의 데이터를 다른 곳으로 복사하는 방법 또는 그 반대로 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>지원되는 시나리오
**온-프레미스 파일 시스템에서** 다음 데이터 저장소로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sink](includes/data-factory-supported-sinks.md)]

다음 데이터 저장소에서 **온-프레미스 파일 시스템으로** 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sources](includes/data-factory-supported-sources.md)]

> [!NOTE]
> 복사 작업 시 원본 파일이 대상에 성공적으로 복사된 후 원본 파일이 삭제되지 않습니다. 성공적 복사 후 원본 파일을 삭제해야 할 경우 파일을 삭제하는 사용자 지정 작업을 만들고 파이프라인에서 해당 작업을 사용합니다.

## <a name="enabling-connectivity"></a>연결 사용
Data Factory는 **데이터 관리 게이트웨이** 를 통해 온-프레미스 파일 시스템 간의 연결을 지원합니다. Data Factory 서비스에서 파일 시스템을 비롯한 지원되는 온-프레미스 데이터 저장소에 연결하도록 허용하려면 온-프레미스 환경에 데이터 관리 게이트웨이를 설치해야 합니다. 데이터 관리 게이트웨이 및 단계별 게이트웨이 설정 지침을 알아보려면 [온-프레미스 원본과 클라우드 간에 데이터 관리 게이트웨이로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요. 데이터 관리 게이트웨이와 달리 온-프레미스 파일 시스템과 통신하기 위해 다른 이진 파일을 설치할 필요가 없습니다. 파일 시스템이 Azure IaaS VM인 경우에도 데이터 관리 게이트웨이를 설치하여 사용해야 합니다. 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 참조하세요.

Linux 파일 공유를 사용하려면 Linux 서버에 [Samba](https://www.samba.org/)를 설치하고 Windows 서버에 데이터 관리 게이트웨이를 설치합니다. Linux 서버에 대한 데이터 관리 게이트웨이 설치는 지원되지 않습니다.

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 파일 시스템 간에 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사** 를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API**, **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. **데이터 팩터리** 를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다.
2. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다. 예를 들어 Azure Blob Storage에서 온-프레미스 파일 시스템으로 데이터를 복사하는 경우 온-프레미스 파일 시스템 및 Azure Storage 계정을 데이터 팩터리에 연결하는 두 개의 연결된 서비스를 만듭니다. 온-프레미스 파일 시스템과 관련된 연결된 서비스 속성은 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.
3. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다. 마지막 단계에서 설명한 예제에서는 입력 데이터가 포함된 BLOB 컨테이너 및 폴더를 지정하는 데이터 세트를 만듭니다. 또한 파일 시스템의 폴더 및 파일 이름(선택 사항)을 지정하는 다른 데이터 세트를 만듭니다. 온-프레미스 파일 시스템과 관련된 데이터 세트 속성은 [데이터 세트 속성](#dataset-properties) 섹션을 참조하세요.
4. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다. 앞에서 언급한 예에서는 BlobSource를 원본으로, FileSystemSink를 복사 작업의 싱크로 사용합니다. 마찬가지로, 온-프레미스 파일 시스템에서 Azure Blob Storage로 복사하는 경우 복사 작업에 FileSystemSource 및 BlobSink를 사용합니다. 온-프레미스 파일 시스템과 관련된 복사 작업 속성은 [복사 작업 속성](#copy-activity-properties) 섹션을 참조하세요. 원본 또는 싱크로 데이터 저장소를 사용하는 방법에 대 한 자세한 내용을 보려면 데이터 저장소에 대한 이전 섹션의 링크를 클릭하세요.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다.  다른 곳에서 파일 시스템으로 또는 그 반대로 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 예](#json-examples-for-copying-data-to-and-from-file-system) 섹션을 참조하세요.

다음 섹션에서는 파일 시스템에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
**온-프레미스 파일 서버** 연결 서비스를 사용하면 Azure Data Factory에 온-프레미스 파일 시스템을 연결할 수 있습니다. 다음 표에서는 온-프레미스 파일 서버 연결 서비스에 지정된 JSON 요소에 대해 설명합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |type 속성은 **OnPremisesFileServer** 로 설정되어야 합니다. |예 |
| host |복사할 폴더의 루트 경로를 지정하고 있습니다. 문자열에서 특수 문자로 이스케이프 문자 '\'를 사용합니다. 예제를 살펴보려면 [연결된 서비스 및 데이터 세트 정의 샘플](#sample-linked-service-and-dataset-definitions) 을 참조하세요. |예 |
| userId |서버에 대한 액세스 권한이 있는 사용자의 ID를 지정합니다. |아니요(encryptedCredential을 선택하는 경우) |
| password |사용자(userid)의 암호를 지정합니다. |아니요(encryptedcredential을 선택하는 경우) |
| encryptedCredential |New-AzDataFactoryEncryptValue cmdlet을 실행하여 얻을 수 있는 암호화된 자격 증명을 지정합니다. |아니요(일반 텍스트에 userid 및 암호를 지정하는 경우) |
| gatewayName |Data Factory에서 온-프레미스 파일 서버에 연결하는 데 사용해야 하는 게이트웨이의 이름을 지정하고 있습니다. |예 |


### <a name="sample-linked-service-and-dataset-definitions"></a>연결된 서비스 및 데이터 세트 정의 샘플
| 시나리오 | 연결된 서비스 정의의 호스트 | 데이터 세트 정의의 folderPath |
| --- | --- | --- |
| 데이터 관리 게이트웨이 컴퓨터의 로컬 폴더: <br/><br/>예제: D:\\\* 또는 D:\folder\subfolder\\\* |D:\\\\(데이터 관리 게이트웨이 버전 2.0 이상) <br/><br/> localhost(데이터 관리 게이트웨이 버전 2.0 미만) |.\\\\ 또는 folder\\\\subfolder(데이터 관리 게이트웨이 버전 2.0 이상) <br/><br/>D:\\\\ 또는 D:\\\\folder\\\\subfolder(게이트웨이 버전 2.0 미만) |
| 원격 공유 폴더: <br/><br/>예: \\\\myserver\\share\\\* 또는 \\\\myserver\\share\\folder\\subfolder\\\\* |\\\\\\\\myserver\\\\share |.\\\\ 또는 folder\\\\subfolder |

>[!NOTE]
>UI를 통해 작성하는 경우 JSON을 사용할 때처럼 이스케이프하기 위해 이중 백슬래시(`\\`)를 입력할 필요가 없으며 단일 백슬래시를 지정합니다.

### <a name="example-using-username-and-password-in-plain-text"></a>예제: 일반 텍스트에 사용자 이름 및 암호 사용

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>예제: encryptedcredential 사용

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md)를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다.

typeProperties 섹션은 데이터 세트의 각 형식마다 다릅니다. 데이터 저장소에 있는 데이터의 위치 및 형식과 같은 정보를 제공합니다. **FileShare** 형식의 데이터 세트에 대한 typeProperties 섹션에는 다음과 같은 속성이 포함됩니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |폴더의 하위 경로를 지정하고 있습니다. 문자열에서 특수 문자로 이스케이프 문자 '\'를 사용합니다. 와일드카드 필터는 지원되지 않습니다. 예제를 살펴보려면 [연결된 서비스 및 데이터 세트 정의 샘플](#sample-linked-service-and-dataset-definitions) 을 참조하세요.<br/><br/>이 속성을 **partitionBy** 와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |테이블이 폴더의 특정 파일을 참조하도록 하려는 경우 **folderPath** 에 파일 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>**fileName** 이 출력 데이터 세트에 대해 지정되지 않았고 **preserveHierarchy** 가 활동 싱크에 지정되지 않은 경우 생성된 파일의 이름은 다음 형식을 따릅니다. <br/><br/>`Data.<Guid>.txt`(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |예 |
| fileFilter |모든 파일이 아닌 folderPath의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다. <br/><br/>허용 되는 값은 `*`(여러 문자) 및 `?`(하나의 문자)입니다.<br/><br/>예 1: "fileFilter": "*.log"<br/>예 2: "fileFilter": 2014-1-?. txt "<br/><br/>fileFilter는 FileShare 입력 데이터 세트에 적용할 수 있습니다. |예 |
| partitionedBy |partitionedBy를 사용하면 시계열 데이터의 동적 folderPath/fileName을 지정할 수 있습니다. 예를 들어 매시간 데이터에 대한 매개 변수를 포함하는 folderPath가 있습니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

> [!NOTE]
> fileName 및 fileFilter는 동시에 사용할 수 없습니다.

### <a name="using-partitionedby-property"></a>partitionedBy 속성 사용
이전 섹션에서 설명했듯이 **partitionedBy** 속성, [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md)를 사용하여 시계열 데이터의 동적 folderPath와 filename을 지정할 수 있습니다.

시계열 데이터 세트, 예약 및 조각에 대해 자세히 이해하려면 [데이터 세트 만들기](data-factory-create-datasets.md), [예약 및 실행](data-factory-scheduling-and-execution.md) 그리고 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요.

#### <a name="sample-1"></a>샘플 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

이 예제에서 {Slice}는 Data Factory 시스템 변수인 SliceStart의 값(YYYYMMDDHH 형식)으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다.

#### <a name="sample-2"></a>샘플 2:

```JSON
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

이 예제에서 SliceStart의 년, 월, 일 및 시는 folderPath 및 fileName 속성에서 사용하는 별도의 변수로 추출됩니다.

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 데이터 세트, 정책 등의 속성은 모든 유형의 활동에 사용할 수 있습니다. 반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다.

복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다. 온-프레미스 파일 시스템에서 데이터를 이동하는 경우 복사 작업의 원본 유형을 **FileSystemSource** 로 설정합니다. 온-프레미스 파일 시스템으로 데이터를 이동하는 경우 복사 작업의 싱크 유형을 **FileSystemSink** 로 설정합니다. 이 섹션에서는 FileSystemSource 및 FileSystemSink에서 지원되는 속성의 목록을 제공합니다.

**FileSystemSource** 는 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True, False(기본값) |예 |

**FileSystemSink** 에서 지원하는 속성은 다음과 같습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| copyBehavior |원본이 BlobSource 또는 FileSystem인 경우 복사 동작을 정의합니다. |**PreserveHierarchy:** 대상 폴더에서 파일 계층 구조를 유지합니다. 즉, 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/>**FlattenHierarchy:** 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 만들어집니다. 대상 파일은 자동 생성된 이름으로 만들어집니다.<br/><br/>**MergeFiles:** 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 이름/Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. |예 |

### <a name="recursive-and-copybehavior-examples"></a>recursive 및 copyBehavior 예제
이 섹션에서는 recursive 및 copyBehavior 값의 다양한 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive 값 | copyBehavior 값 | 결과 동작 |
| --- | --- | --- |
| true |preserveHierarchy |다음과 같은 구조의 Folder1 원본 폴더인 경우<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |다음과 같은 구조의 Folder1 원본 폴더인 경우<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles |다음과 같은 구조의 Folder1 원본 폴더인 경우<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. |
| false |preserveHierarchy |다음과 같은 구조의 Folder1 원본 폴더인 경우<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy |다음과 같은 구조의 Folder1 원본 폴더인 경우<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles |다음과 같은 구조의 Folder1 원본 폴더인 경우<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다.<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |

## <a name="supported-file-and-compression-formats"></a>지원되는 파일 및 압축 형식
자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md) 문서를 참조하세요.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>파일 시스템으로/에서 데이터를 복사하는 JSON 예제
다음 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 온-프레미스 파일 시스템과 Azure Blob Storage 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하면 데이터를 원본에서 [지원되는 원본 및 싱크](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 나열된 싱크 중 하나로 *직접* 복사할 수 있습니다.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>예제: 온-프레미스 파일 시스템에서 Azure Blob Storage로 데이터 복사
이 샘플에서는 온-프레미스 파일 시스템에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 샘플에 포함된 Data Factory 엔터티는 다음과 같습니다.

* [OnPremisesFileServer](#linked-service-properties)형식의 연결된 서비스
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [FileShare](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [FileSystemSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

여기서는 매시간 시계열 데이터를 온-프레미스 파일 시스템에서 Azure Blob Storage로 복사합니다. 샘플에 사용된 JSON 속성은 샘플 뒤에 나오는 섹션에서 설명합니다.

첫 단계로 [온-프레미스 원본과 클라우드 간에 데이터 관리 게이트웨이로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)의 지침에 따라 데이터 관리 게이트웨이를 설정해야 합니다.

**온-프레미스 파일 서버 연결 서비스:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

**userid** 및 **password** 속성을 사용하는 대신 **encryptedCredential** 속성을 사용하는 것이 좋습니다. 이 연결 서비스에 대한 자세한 내용은 [파일 서버 연결 서비스](#linked-service-properties)를 참조하세요.

**Azure Storage 연결된 서비스:**

```JSON
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

**온-프레미스 파일 시스템 입력 데이터 세트:**

데이터는 매시간 새 파일에서 선택됩니다. folderPath 및 fileName 속성은 조각의 시작 시간에 따라 결정됩니다.

`"external": "true"`로 설정하면 데이터 세트가 Data Factory의 외부에 있고 Data Factory의 활동으로 생성되지 않는다고 Data Factory에 전달됩니다.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Azure Blob Storage 출력 데이터 세트:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월, 일 및 시 부분을 사용합니다.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
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

**파일 시스템 원본 및 Blob 싱크를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **FileSystemSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>예제: Azure SQL Database에서 온-프레미스 파일 시스템으로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

* [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) 형식의 연결된 서비스입니다.
* [OnPremisesFileServer](#linked-service-properties)형식의 연결된 서비스
* [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 형식의 입력 데이터 세트입니다.
* [FileShare](#dataset-properties) 형식의 출력 데이터 세트입니다.
* [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) 및 [FileSystemSink](#copy-activity-properties)를 사용하는 복사 작업의 파이프라인입니다.

이 샘플에서는 매시간 시계열 데이터를 Azure SQL 테이블에서 온-프레미스 파일 시스템으로 복사합니다. 샘플에 사용된 JSON 속성은 샘플 뒤에 나오는 섹션에서 설명합니다.

**Azure SQL Database 연결된 서비스:**

```JSON
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

**온-프레미스 파일 서버 연결 서비스:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

**userid** 및 **password** 속성을 사용하는 대신 **encryptedCredential** 속성을 사용하는 것이 좋습니다. 이 연결 서비스에 대한 자세한 내용은 [파일 시스템 연결 서비스](#linked-service-properties)를 참조하세요.

**Azure SQL 입력 데이터 세트:**

이 샘플에서는 Azure SQL에서 'MyTable' 테이블을 만들었고 이 테이블에 'timestampcolumn'라는 시계열 데이터 열이 포함되어 있다고 가정합니다.

``"external": "true"``로 설정하면 데이터 세트가 Data Factory의 외부에 있고 Data Factory의 활동으로 생성되지 않는다고 Data Factory에 전달됩니다.

```JSON
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

**온-프레미스 파일 시스템 출력 데이터 세트:**

데이터는 매시간 새 파일로 복사됩니다. Blob에 대한 folderPath 및 fileName은 조각의 시작 시간에 따라 결정됩니다.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**SQL 원본 및 파일 시스템 싱크를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **SqlSource** 로 설정되고 **sink** 형식은 **FileSystemSink** 로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 복사할 과거 한 시간의 데이터를 선택합니다.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

복사 작업 정의에서 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
 Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 다양한 최적화 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md) 를 참조하세요.
