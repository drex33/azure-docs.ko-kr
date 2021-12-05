---
title: 메타데이터 가져오기 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인에서 메타데이터 가져오기 작업을 사용하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.author: jianleishen
ms.openlocfilehash: e8dd22beda5e84510b203e815fe69f5b41401136
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570388"
---
# <a name="get-metadata-activity-in-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics에서 메타데이터 가져오기 작업

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

메타데이터 가져오기 작업을 사용하여 Azure Data Factory 또는 Synapse 파이프라인에서 데이터의 메타데이터를 검색할 수 있습니다. 조건식에서 메타데이터 가져오기 작업의 출력을 사용하여 유효성 검사를 수행하거나 후속 작업에서 메타데이터를 사용할 수 있습니다.

## <a name="create-a-get-metadata-activity-with-ui"></a>UI를 사용 하 여 메타 데이터 가져오기 작업 만들기

파이프라인에서 Get Metadata 작업을 사용 하려면 다음 단계를 완료 합니다.

1. 파이프라인 활동 창에서 _메타 데이터 가져오기_ 를 검색 하 고 실패 활동을 파이프라인 캔버스로 끕니다.
1. 캔버스에서 새 메타 데이터 가져오기 작업을 선택 하지 않은 경우 해당 작업을 선택 하 고 해당  **데이터 집합** 탭을 선택 하 여 세부 정보를 편집 합니다.
1. 데이터 집합을 선택 하거나 새로 만들기 단추를 사용 하 여 새 데이터 집합을 만듭니다.  그런 다음 필터 옵션을 지정 하 고 데이터 집합에 사용할 수 있는 메타 데이터의 열을 추가할 수 있습니다.

   :::image type="content" source="media/control-flow-get-metadata-activity/get-metadata-activity.png" alt-text="메타 데이터 가져오기 작업에 대 한 UI를 표시 합니다.":::

1. 활동의 출력을 다른 활동에 대 한 입력으로 사용 합니다 (예:이 예의 Switch 활동).  다른 작업에서 동적 콘텐츠가 지원 되는 위치에 관계 없이 메타 데이터 작업의 출력을 참조할 수 있습니다.

   :::image type="content" source="media/control-flow-get-metadata-activity/using-metadata-in-another-activity.png" alt-text="메타 데이터 가져오기 작업의 출력을 처리 하기 위해 전환 작업을 추가한 파이프라인을 보여 줍니다.":::

1. 동적 콘텐츠 편집기에서 메타 데이터 가져오기 작업 출력을 선택 하 여 다른 활동에서 참조 합니다.

   :::image type="content" source="media/control-flow-get-metadata-activity/dynamic-content-editor-using-metadata.png" alt-text="동적 콘텐츠로 메타 데이터 가져오기 작업의 출력을 사용 하 여 동적 콘텐츠 편집기를 표시 합니다.":::

## <a name="supported-capabilities"></a>지원되는 기능

메타데이터 가져오기 작업은 데이터 세트를 입력으로 사용하여 메타데이터 정보를 출력으로 반환합니다. 현재 다음 커넥터와 해당 검색 가능한 메타데이터가 지원됩니다. 반환되는 메타데이터의 최대 크기는 **4MB** 입니다.

### <a name="supported-connectors"></a>지원되는 커넥터

**File Storage**

| 커넥터/메타데이터 | itemName<br>(파일/폴더) | itemType<br>(파일/폴더) | 크기<br>(파일) | created<br>(파일/폴더) | lastModified<sup>1</sup><br>(파일/폴더) |childItems<br>(폴더) |contentMD5<br>(파일) | structure<sup>2</sup><br/>(파일) | columnCount<sup>2</sup><br>(파일) | exists<sup>3</sup><br>(파일/폴더) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Amazon S3 호환 스토리지](connector-amazon-s3-compatible-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Oracle Cloud Storage](connector-oracle-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure 파일](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [파일 시스템](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> 메타데이터 `lastModified`:
- Amazon S3, Amazon S3 Compatible Storage, Google Cloud Storage 및 Oracle Cloud Storage의 경우 `lastModified`는 버킷과 키에 적용되지만 가상 폴더에는 적용되지 않고 `exists`는 버킷과 키에 적용되지만 접두사 또는 가상 폴더에는 적용되지 않습니다. 
- Azure Blob Storage의 경우 `lastModified`는 컨테이너와 Blob에 적용되지만 가상 폴더에는 적용되지 않습니다.

<sup>2</sup> 이진 파일, JSON 또는 XML 파일에서 메타데이터를 가져올 때 메타데이터 `structure` 및 `columnCount`는 지원되지 않습니다.

<sup>3</sup> 메타데이터 `exists`: Amazon S3, Amazon S3 Compatible Storage, Google Cloud Storage 및 Oracle Cloud Storage의 경우 `exists`가 버킷과 키에 적용되지만 접두사 또는 가상 폴더에는 적용되지 않습니다.

다음 사항에 유의하세요.

- 폴더에 대해 메타데이터 가져오기 작업을 사용하는 경우 지정된 폴더에 대한 LIST/EXECUTE 권한이 있는지 확인합니다.
- 폴더/파일에 대한 와일드카드 필터는 메타데이터 가져오기 작업에 지원되지 않습니다.
- 커넥터에 설정된 `modifiedDatetimeStart` 및 `modifiedDatetimeEnd` 필터:

    - 이러한 두 속성은 폴더에서 메타데이터를 가져올 때 자식 항목을 필터링하는 데 사용됩니다. 파일에서 메타데이터를 가져올 때는 적용되지 않습니다.
    - 이러한 필터를 사용하면 출력의 `childItems`에는 지정된 범위 내에서 수정된 파일만 포함되고 폴더는 포함되지 않습니다.
    - 이러한 필터를 적용하기 위해 GetMetadata 작업은 지정된 폴더의 모든 파일을 열거하고 수정된 시간을 확인합니다. 예상되는 정규화된 파일 수가 적더라도 많은 수의 파일이 있는 폴더를 가리키지 마세요. 

**관계형 데이터베이스**

| 커넥터/메타데이터 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Amazon RDS for SQL Server](connector-amazon-rds-for-sql-server.md) | √ | √ | √ |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>메타데이터 옵션

메타데이터 가져오기 작업 필드 목록에서 다음 메타데이터 형식을 지정하여 해당 정보를 검색할 수 있습니다.

| 메타데이터 유형 | 설명 |
|:--- |:--- |
| itemName | 파일 또는 폴더의 이름입니다. |
| itemType | 파일 또는 폴더의 형식입니다. 반환된 값은 `File` 또는 `Folder`입니다. |
| 크기 | 파일 크기(바이트)입니다. 파일에만 적용됩니다. |
| created | 파일 또는 폴더를 만든 날짜/시간입니다. |
| lastModified | 파일 또는 폴더를 마지막으로 수정한 날짜/시간입니다. |
| childItems | 지정된 폴더의 하위 폴더 및 파일 목록입니다. 폴더에만 적용됩니다. 반환된 값은 각 자식 항목의 이름과 유형 목록입니다. |
| contentMD5 | 파일의 MD5입니다. 파일에만 적용됩니다. |
| structure | 파일 또는 관계형 데이터베이스 테이블의 데이터 구조입니다. 반환 값은 열 이름과 열 형식의 목록입니다. |
| columnCount | 파일 또는 관계형 테이블의 열 수입니다. |
| exists| 파일, 폴더 또는 테이블이 있는지 여부입니다. 메타데이터 가져오기 필드 목록에 `exists`가 지정되면 파일, 폴더 또는 테이블이 존재하지 않아도 작업이 실패하지 않습니다. 대신 `exists: false`가 출력에 반환됩니다. |

> [!TIP]
> 파일, 폴더 또는 테이블이 있는지 유효성을 검증하려면 메타데이터 가져오기 작업 필드 목록에 `exists`를 지정합니다. 그런 다음 작업 출력에서 `exists: true/false` 결과를 확인할 수 있습니다. `exists`가 필드 목록에 지정되지 않은 경우 개체를 찾을 수 없으면 메타데이터 가져오기 작업이 실패합니다.

> [!NOTE]
> 파일 저장소에서 메타데이터를 가져오고 `modifiedDatetimeStart` 또는 `modifiedDatetimeEnd`를 구성하면 출력의 `childItems`에는 지정된 범위 내에서 마지막으로 수정된 시간이 있는 지정된 경로의 파일만 포함됩니다. 하위 폴더의 항목은 포함되지 않습니다.

> [!NOTE]
> 구분된 텍스트 및 Excel 형식 데이터 세트에 대한 실제 데이터 구조를 제공하는 **구조** 필드 목록의 경우 이러한 데이터 원본에 대해서만 지원되는 `First Row as Header` 속성을 사용하도록 설정해야 합니다.

## <a name="syntax"></a>구문

**메타데이터 가져오기 작업**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**데이터 세트**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>형식 속성

현재, 메타데이터 가져오기 작업은 다음과 같은 유형의 메타데이터 정보를 반환할 수 있습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
fieldList | 필요한 메타데이터 정보의 유형입니다. 지원되는 메타데이터에 대한 자세한 내용은 이 문서의 [메타데이터 옵션](#metadata-options) 섹션을 참조하세요. | 예 
데이터 세트 | GetMetadata 작업 시 메타데이터를 검색할 참조 데이터 세트입니다. 지원되는 커넥터에 대한 정보는 [기능](#supported-capabilities) 섹션을 참조하세요. 데이터 세트 구문 세부 정보는 특정 커넥터 항목을 참조하세요. | 예
formatSettings | 형식 유형 데이터 세트를 사용할 때 적용합니다. | 예
storeSettings | 형식 유형 데이터 세트를 사용할 때 적용합니다. | 아니요

## <a name="sample-output"></a>샘플 출력

메타데이터 가져오기 결과가 작업 출력에 표시됩니다. 다음은 광범위한 메타데이터 옵션을 보여 주는 두 가지 샘플입니다. 후속 작업에서 결과를 사용하려면 `@{activity('MyGetMetadataActivity').output.itemName}` 패턴을 사용합니다.

### <a name="get-a-files-metadata"></a>파일의 메타데이터 가져오기

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>폴더의 메타데이터 가져오기

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
지원되는 다른 제어 흐름 작업에 대해 알아봅니다.

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
