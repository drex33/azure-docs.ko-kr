---
title: Azure Data Factory의 Excel 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 항목에서는 Azure Data Factory 및 Azure Synapse Analytics에서 Excel 형식을 처리하는 방법을 설명합니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: jianleishen
ms.openlocfilehash: 7a1a0331276169bb81dcf86c74aab1a16855b25c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255850"
---
# <a name="excel-file-format-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics의 Excel 파일 형식
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Excel 파일을 구문 분석** 하려는 경우 이 문서의 내용을 따르세요. 이 서비스는 ".xls" 및 ".xlsx"을 모두 지원합니다.

Excel 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [Oracle Cloud Storage](connector-oracle-cloud-storage.md) 및 [SFTP](connector-sftp.md) 커넥터에 지원됩니다. 원본으로 지원되며 싱크로는 지원되지 않습니다. 

>[!NOTE]
>[HTTP](connector-http.md)를 사용할 때는 ".xls" 형식이 지원되지 않습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Excel 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 세트의 type 속성을 **Excel** 로 설정해야 합니다.   | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`의 고유한 위치 형식 및 지원되는 속성이 있습니다. | 예      |
| sheetName        | 데이터를 읽을 Excel 워크시트 이름입니다.                       | `sheetName` 또는 `sheetIndex`를 지정합니다. |
| sheetIndex | 데이터를 읽을 Excel 워크시트 인덱스입니다(0부터 시작). | `sheetName` 또는 `sheetIndex`를 지정합니다. |
| range            | 지정된 워크시트에서 선택적 데이터를 찾기 위한 셀 범위입니다. 예를 들면 다음과 같습니다.<br>-지정하지 않음: 비어 있지 않은 첫 번째 행과 열의 테이블로 전체 워크시트를 읽습니다.<br>- `A3`: 지정된 셀에서 시작하여 테이블을 읽고 아래의 모든 행과 오른쪽에 있는 모든 열을 동적으로 검색합니다.<br>- `A3:H5`: 이 고정 범위를 테이블로 읽습니다.<br>- `A3:A3`: 이 단일 셀을 읽습니다. | 예       |
| firstRowAsHeader | 지정된 워크시트/범위의 첫 번째 행을 열 이름의 머리글 줄로 처리할지 여부를 지정합니다.<br>허용되는 값은 **true** 및 **false**(기본값)입니다. | 예       |
| nullValue        | Null 값의 문자열 표현을 지정합니다. <br>기본값은 **빈 문자열** 입니다. | 예       |
| 압축 | 파일 압축을 구성하는 속성 그룹입니다. 작업 실행 중 압축/압축 풀기를 수행하려는 경우 이 섹션을 구성합니다. | 예 |
| type<br/>( *`compression` 아래*) | JSON 파일을 읽고 쓰는 데 사용되는 압축 코덱입니다. <br>허용되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Tar**, **snappy** 또는 **lz4** 입니다. 기본값은 압축되지 않습니다.<br>**참고** 현재 복사 작업은 'snappy' 및 'lz4'를 지원하지 않으며, 매핑 데이터 흐름은 'ZipDeflate', 'TarGzip', 'Tar'을 지원하지 않습니다.<br>**참고** 복사 작업을 사용하여 **ZipDeflate** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓰면 파일이 `<path specified in dataset>/<folder named as source zip file>/` 폴더에 추출됩니다. | 아니요.  |
| 수준<br/>( *`compression`아래*) | 압축 비율입니다. <br>허용 값은 **최적** 또는 **가장 빠름** 입니다.<br>- **가장 빠름:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업을 최대한 빨리 완료해야 합니다.<br>- **최적**: 작업이 완료되는 데 시간이 오래 걸리더라도 최적으로 압축해야 합니다. 자세한 내용은 [압축 수준](/dotnet/api/system.io.compression.compressionlevel) 항목을 참조하세요. | 예       |

다음은 Azure Blob Storage에 대한 Excel 데이터 세트의 예입니다.

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Excel 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="excel-as-source"></a>Excel을 원본으로 

복사 작업 ***\*source\* 섹션*** 에서 지원되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **ExcelSource** 로 설정해야 합니다. | 예      |
| storeSettings | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 읽기 설정이 있습니다. | 예       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)같은 데이터 저장소에서 Excel 형식을 읽을 수 있습니다. Excel 데이터 세트 또는 [인라인 데이터 세트](data-flow-source.md#inline-datasets)를 사용하여 Excel 파일을 가리킬 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 Excel 원본에서 지원하는 속성을 나열합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다. 인라인 데이터 세트를 사용하는 경우 [데이터 세트 속성](#dataset-properties) 섹션에 설명된 속성과 동일한 추가 파일 설정이 표시됩니다.

| 이름                      | 설명                                                  | 필수 | 허용되는 값                                            | 데이터 흐름 스크립트 속성         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| 와일드 카드 경로           | 와일드 카드 경로와 일치하는 모든 파일이 처리됩니다. 데이터 세트에 설정된 폴더 및 파일 경로를 재정의합니다. | 아니요       | String[]                                                  | wildcardPaths                     |
| 파티션 루트 경로       | 분할된 파일 데이터의 경우 분할된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 아니요       | String                                                    | partitionRootPath                 |
| 파일 목록             | 원본이 처리할 파일을 나열하는 텍스트 파일을 가리키는지 여부입니다. | 아니요       | `true` 또는 `false`                                         | fileList                          |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용하여 새 열을 만듭니다.       | 아니요       | String                                                    | rowUrlColumn                      |
| 완료 후          | 처리 후 파일을 삭제하거나 이동합니다. 컨테이너 루트에서 파일 경로가 시작됩니다. | 아니요       | 삭제: `true` 또는 `false` <br> 이동: `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| 마지막으로 수정한 사람으로 필터링   | 마지막으로 변경된 시간에 따라 파일을 필터링하도록 선택합니다. | 아니요       | 타임스탬프                                                 | modifiedAfter <br> modifiedBefore |
| 파일을 찾을 수 없음 허용 | true이면 파일이 없는 경우 오류가 throw되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

### <a name="source-example"></a>원본 예

아래 이미지는 데이터 세트 모드를 사용하는 매핑 데이터 흐름의 Excel 원본 구성의 예입니다.

![Excel 원본](media/data-flow/excel-source.png)

연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

인라인 데이터 세트를 사용하는 경우 매핑 데이터 흐름에 다음 원본 옵션이 표시됩니다.

![Excel 원본 인라인 데이터 세트](media/data-flow/excel-source-inline-dataset.png)

연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
