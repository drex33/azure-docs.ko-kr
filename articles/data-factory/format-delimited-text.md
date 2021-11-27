---
title: Azure Data Factory의 구분된 텍스트 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 문서에서는 Azure Data Factory 및 Azure Synapse Analytics에서 구분된 텍스트 형식을 처리하는 방법을 설명합니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 11/25/2021
ms.author: jianleishen
ms.openlocfilehash: f1b4a779e71487c6bd2323c0cde32a9649c86d17
ms.sourcegitcommit: 625ffc4a23b7b71680aaf572fede507053d4ef03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2021
ms.locfileid: "133177027"
---
# <a name="delimited-text-format-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics의 구분된 텍스트 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**구분된 텍스트 파일을 구문 분석하거나 데이터를 구분된 텍스트 형식으로 쓰려면** 이 문서의 내용을 따르세요. 

다음 커넥터에 대해 구분 기호로 분리 된 텍스트 형식이 지원 됩니다. 

- [Amazon S3](connector-amazon-simple-storage-service.md)
- [Amazon S3 호환 스토리지](connector-amazon-s3-compatible-storage.md)
- [Azure Blob](connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
- [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
- [Azure 파일](connector-azure-file-storage.md)
- [파일 시스템](connector-file-system.md)
- [FTP](connector-ftp.md)
- [Google Cloud Storage](connector-google-cloud-storage.md)
- [HDFS](connector-hdfs.md)
- [HTTP](connector-http.md)
- [Oracle Cloud Storage](connector-oracle-cloud-storage.md)
- [SFTP](connector-sftp.md)

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 구분된 텍스트 데이터 세트에서 지원하는 속성 목록을 제공합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 세트의 type 속성을 **DelimitedText** 로 설정해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`의 고유한 위치 형식 및 지원되는 속성이 있습니다.  | 예      |
| columnDelimiter  | 파일의 열을 구분하는 데 사용되는 문자입니다. <br>기본값은 **쉼표 `,`** 입니다. 열 구분 기호를 빈 문자열로 정의한 경우(구분 기호가 없음을 의미) 전체 줄이 단일 열로 사용됩니다.<br>현재 빈 문자열인 열 구분 기호는 데이터 흐름을 매핑하는 경우에만 지원 되 고 복사 작업에는 지원 되지 않습니다.  | 예       |
| rowDelimiter     | 파일의 행을 구분하는 데 사용되는 문자 또는 "\r\n"입니다. <br>기본값은 다음 값 중 하나입니다. **읽기: ["\r\n", "\r", "\n"]** 및 **쓰기: "\n" 또는 "\r\n"** (각각 매핑 데이터 흐름 및 복사 작업). <br>행 구분 기호를 구분 기호 없음(빈 문자열)으로 설정한 경우에는 열 구분 기호도 구분 기호 없음(빈 문자열)으로 설정해야 합니다. 즉, 전체 내용을 단일 값으로 처리합니다.<br>현재 행 구분 기호는 매핑 데이터 흐름에만 지원되고 복사 작업에는 지원되지 않습니다. | 예       |
| quoteChar        | 열 구분 기호를 포함하는 경우 열 값을 따옴표로 묶을 단일 문자입니다. <br>기본값은 **큰따옴표** `"`입니다. <br>`quoteChar`를 빈 문자열로 정의한 경우 따옴표 문자가 없고 열 값이 따옴표로 묶이지 않으며 `escapeChar`가 열 구분 기호 및 자신을 이스케이프하는 데 사용된다는 의미입니다. | 예       |
| escapeChar       | 따옴표로 묶인 값 안에서 따옴표를 이스케이프할 단일 문자입니다.<br>기본값은 **백슬래시 `\`** 입니다. <br>`escapeChar`를 빈 문자열로 정의한 경우에는 `quoteChar`도 빈 문자열로 설정해야 합니다. 이 경우 모든 열 값에 구분 기호가 포함되지 않아야 합니다. | 예       |
| firstRowAsHeader | 첫 번째 행을 열의 이름을 가진 머리글 줄로 처리할지 여부를 지정합니다.<br>허용되는 값은 **true** 및 **false**(기본값)입니다.<br>firstRowAsHeader가 false인 경우 UI 데이터 미리 보기 및 조회 작업 출력에서 열 이름을 Prop_{n}(0부터 시작)으로 자동 생성하고, 복사 작업은 원본에서 싱크로의 [명시적 매핑](copy-activity-schema-and-type-mapping.md#explicit-mapping)을 요구하고 열을 서수(1부터 시작)로 찾으며, 매핑 데이터 흐름은 열을 나열하고 이름이 Column_{n}(1부터 시작)인 열을 찾습니다.  | 예       |
| nullValue        | Null 값의 문자열 표현을 지정합니다. <br>기본값은 **빈 문자열** 입니다. | 예       |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용되는 인코딩 형식입니다. <br>허용 되는 값은 다음과 같습니다. "UTF-8", "u t f-8 (BOM 포함 안 함)," UTF-16 "," UTF-16 be "," UTF-16-32 "," UTF-32BE "," US-ASCII "," UTF-7 "," BIG5 "," EUC-JP "," EUC-KR "," GB2312 "," GB18030 "," 조합 "," CP875 "," "," CP866 "," IBM00858 "," IBM037 "," IBM273 "," IBM437 "," IBM500 "," IBM737 "," IBM775 "," IBM850 "," IBM852 "," IBM855 "," IBM857 "," IBM860 "," IBM861 "," IBM863 "," IBM864 "," IBM865 "," IBM869 "," IBM870 "," IBM01140 "," IBM01141 "," IBM01142 "," IBM01143 "," IBM01144 "," IBM01145 "," IBM01146 "," IBM01147 ",  "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>매핑 데이터 흐름은 UTF-7 인코딩을 지원하지 않습니다. | 예       |
| compressionCodec | 텍스트 파일을 읽고 쓰는 데 사용되는 압축 코덱입니다. <br>허용되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Tar**, **snappy** 또는 **lz4** 입니다. 기본값은 압축되지 않습니다. <br>**참고** 현재 복사 작업은 “snappy” & “lz4”을 지원하지 않으며, 매핑 데이터 흐름은 “ZipDeflate”, “TarGzip”, “Tar”를 지원하지 않습니다. <br>**참고** 복사 작업을 통해 **ZipDeflate** / **TarGzip** / **Tar** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때, 기본적으로 파일은 `<path specified in dataset>/<folder named as source compressed file>/`과 같이 폴더로 추출됩니다. 즉, [복사 작업 원본](#delimited-text-as-source)에서 `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder`를 사용하여 압축된 파일의 이름을 폴더 구조로 유지할지 여부를 제어합니다. | 예       |
| compressionLevel | 압축 비율입니다. <br>허용 값은 **최적** 또는 **가장 빠름** 입니다.<br>- **가장 빠름:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업을 최대한 빨리 완료해야 합니다.<br>- **최적**: 작업이 완료되는 데 시간이 오래 걸리더라도 최적으로 압축해야 합니다. 자세한 내용은 [압축 수준](/dotnet/api/system.io.compression.compressionlevel) 항목을 참조하세요. | 예       |

다음은 Azure Blob Storage에서 구분된 텍스트 데이터 세트의 예입니다.

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 구분된 텍스트 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="delimited-text-as-source"></a>원본으로서의 구분된 텍스트 

복사 작업 ***\*source\**** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성       | 설명                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 작업 원본의 type 속성은 **DelimitedTextSource** 로 설정해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **구분된 텍스트 읽기 설정** 표를 참조하세요. |  예       |
| storeSettings  | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 읽기 설정이 있습니다. | 예       |

**아래의 지원되는** 구분된 텍스트 읽기 설정`formatSettings`:

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings의 형식을 **DelimitedTextReadSettings** 로 설정해야 합니다. | 예      |
| skipLineCount | 입력 파일에서 데이터를 읽을 때 건너뛸 **비어 있지 않은** 행의 수를 나타냅니다. <br>skipLineCount와 firstRowAsHeader가 모두 지정되면 먼저 줄을 건너뛴 다음, 입력 파일에서 헤더 정보를 읽습니다. | 예       |
| compressionProperties | 지정된 압축 코덱에 대한 데이터의 압축을 푸는 방법에 대한 속성 그룹입니다. | 예       |
| preserveZipFileNameAsFolder<br>( *`compressionProperties`->`type`아래 `ZipDeflateReadSettings`* 으로) |  **ZipDeflate** 압축을 사용하여 입력 데이터 세트를 구성할 때 적용됩니다. 복사 중에 원본 zip 파일 이름을 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **true(기본값)** 로 설정하면, 서비스가 압축을 푼 파일을 `<path specified in dataset>/<folder named as source zip file>/`에 씁니다.<br>- **false** 로 설정하면, 서비스가 압축을 푼 파일을 `<path specified in dataset>`에 직접 씁니다. 레이싱 또는 예기치 않은 동작을 방지하기 위해 다른 원본 zip 파일에 중복된 파일 이름이 없는지 확인합니다.  | 예 |
| preserveCompressionFileNameAsFolder<br>( *`compressionProperties`->`type` 아래 `TarGZipReadSettings` 또는 `TarReadSettings`* 으로)  | 입력 데이터 세트가 **TarGzip**/**Tar** 압축을 사용하여 구성될 때 적용됩니다. 복사 중에 원본 압축 파일 이름을 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **true(기본값)** 로 설정하면, 서비스가 압축 해제한 파일을 `<path specified in dataset>/<folder named as source compressed file>/`에 씁니다. <br>- **false** 로 설정하면, 서비스가 압축 해제한 파일을 `<path specified in dataset>`에 직접 씁니다. 레이싱 또는 예기치 않은 동작을 방지하기 위해 다른 원본 파일에 중복된 파일 이름이 없는지 확인합니다. | 예 |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>싱크로서의 구분된 텍스트

복사 작업 ***\*sink\**** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성       | 설명                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 작업 원본의 type 속성은 **DelimitedTextSink** 로 설정해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **구분된 텍스트 쓰기 설정** 표를 참조하세요. |    예      |
| storeSettings  | 데이터 저장소에 데이터를 쓰는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 쓰기 설정이 있습니다.  | 예       |

**아래의 지원되는** 구분된 텍스트 쓰기 설정`formatSettings`:

| 속성      | 설명                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings의 형식을 **DelimitedTextWriteSettings** 로 설정해야 합니다. | 예                                                   |
| fileExtension | 출력 파일의 이름을 지정하는 데 사용되는 파일 확장명입니다(예: `.csv`, `.txt`). 출력 DelimitedText 데이터 세트에 `fileName`이 지정되지 않은 경우 이 속성을 반드시 지정해야 합니다. 출력 데이터 세트에서 파일 이름이 구성되어 있으면 해당 이름이 싱크 파일 이름으로 사용되고 파일 확장명 설정은 무시됩니다.  | 출력 데이터 세트에 파일 이름이 지정되지 않은 경우 예입니다. |
| maxRowsPerFile | 폴더에 데이터를 쓸 때 여러 파일에 쓰도록 선택하고 파일당 최대 행 수를 지정할 수 있습니다.  | 예 |
| fileNamePrefix | `maxRowsPerFile`이 구성된 경우 적용할 수 있습니다.<br> 여러 파일에 데이터를 쓸 때 파일 이름 접두사를 지정합니다. 이 패턴은 `<fileNamePrefix>_00000.<fileExtension>`입니다. 지정하지 않는 경우 파일 이름 접두사가 자동으로 생성됩니다. 원본이 파일 기반 저장소 또는 [파티션 옵션 사용 데이터 저장소](copy-activity-performance-features.md)인 경우에는 이 속성이 적용되지 않습니다.  | 예 |

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)데이터 저장소에서 구분 된 텍스트 형식을 읽고 쓸 수 있으며 [Amazon](connector-amazon-simple-storage-service.md#mapping-data-flow-properties)s 3에서 구분 된 텍스트 형식을 읽을 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 구분된 텍스트 원본에서 지원하는 속성을 나열합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| 이름 | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 와일드 카드 경로 | 와일드 카드 경로와 일치하는 모든 파일이 처리됩니다. 데이터 세트에 설정된 폴더 및 파일 경로를 재정의합니다. | 아니요 | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할된 파일 데이터의 경우 분할된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 아니요 | String | partitionRootPath |
| 파일 목록 | 원본이 처리할 파일을 나열하는 텍스트 파일을 가리키는지 여부입니다. | 아니요 | `true` 또는 `false` | fileList |
| 여러 줄 행 | 원본 파일에 여러 줄에 걸쳐 있는 행이 포함되어 있는지를 나타냅니다. 여러 줄 값은 따옴표로 묶어야 합니다. | 아니요 `true` 또는 `false` | multiLineRow |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용하여 새 열을 만듭니다. | 아니요 | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제하거나 이동합니다. 컨테이너 루트에서 파일 경로가 시작됩니다. | 아니요 | 삭제: `true` 또는 `false` <br> 이동: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 마지막으로 수정한 사람으로 필터링 | 마지막으로 변경된 시간에 따라 파일을 필터링하도록 선택합니다. | 아니요 | 타임스탬프 | modifiedAfter <br> modifiedBefore |
| 파일을 찾을 수 없음 허용 | true이면 파일이 없는 경우 오류가 throw되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

> [!NOTE]
> 파일 목록에 대한 데이터 흐름 원본 지원은 파일에서 1,024개의 항목으로 제한됩니다. 추가 파일을 포함하려면 파일 목록에서 와일드카드를 사용합니다.

### <a name="source-example"></a>원본 예

아래 그림은 매핑 데이터 흐름에서 구분된 텍스트 원본 구성의 예입니다.

:::image type="content" source="media/data-flow/delimited-text-source.png" alt-text="DelimitedText 원본":::

연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> 데이터 흐름 원본은 Hadoop 파일 시스템에서 지원하는 제한된 Linux 와일드 카드 세트를 지원합니다.

### <a name="sink-properties"></a>싱크 속성

다음 표에서는 구분된 텍스트 소스에서 지원하는 속성을 나열합니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| 이름 | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | 아니요 | `true` 또는 `false` | truncate |
| 파일 이름 옵션 | 작성된 데이터의 명명 형식입니다. 기본적으로 파티션당 파일 하나이고 형식은 `part-#####-tid-<guid>`입니다. | 아니요 | 패턴: String <br> 파티션당: String[] <br> 열 데이터로 파일 이름 지정: String <br> 단일 파일로 출력: `['<fileName>']` <br> 열 데이터로 폴더 이름 지정: String | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames <br> rowFolderUrlColumn |
| 모두 따옴표로 묶기 | 모든 값을 따옴표로 묶습니다. | 아니요 | `true` 또는 `false` | quoteAll |
| header | 출력 파일에 고객 헤더 추가 | 아니요 | `[<string array>]` | header |

### <a name="sink-example"></a>싱크 예

아래 그림은 매핑 데이터 흐름에서 구분된 텍스트 싱크 구성의 예입니다.

:::image type="content" source="media/data-flow/delimited-text-sink.png" alt-text="DelimitedText 싱크":::

연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="related-connectors-and-formats"></a>관련 커넥터 및 형식

구분 기호로 분리된 텍스트 형식과 관련된 몇 가지 일반적인 커넥터 및 형식은 다음과 같습니다.

- Azure Blob Storage(connector-azure-blob-storage.md)
- 이진 형식(format-binary.md)
- Dataverse(connector-dynamics-crm-office-365.md)
- 델타 형식(format-delta.md)
- Excel format(format-excel.md)
- 파일 시스템(connector-file-system.md)
- FTP(connector-ftp.md)
- HTTP(connector-http.md)
- JSON 형식(format-json.md)
- Parquet 형식(format-parquet.md)

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
