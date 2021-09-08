---
title: Azure Data Factory의 Avro 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 항목에서는 Azure Data Factory에서 Avro 형식을 처리하는 방법에 대해 설명합니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jianleishen
ms.openlocfilehash: 6a4e3f033583f70250e12702b948fcd30875fb65
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250699"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure Data Factory의 Avro 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Avro 파일을 구문 분석하거나 데이터를 Avro 형식으로 쓰려면** 이 문서의 내용을 따르세요. 

Avro 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [Oracle Cloud Storage](connector-oracle-cloud-storage.md) 및 [SFTP](connector-sftp.md) 커넥터에 지원됩니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 Avro 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 세트의 type 속성을 **Avro** 로 설정해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`의 고유한 위치 형식 및 지원되는 속성이 있습니다. **자세한 내용은 커넥터 문서 -> 데이터 세트 속성 섹션을 참조하세요**. | 예      |
| avroCompressionCodec | Avro 파일에 쓸 때 사용할 압축 코덱입니다. Avro 파일에서 읽을 때 Data Factory는 파일 메타데이터를 기반으로 압축 코덱을 자동으로 결정합니다.<br>지원되는 형식은 **없음**(기본값), **deflate**, **snappy** 입니다. 현재 복사 작업은 Avro 파일을 읽고 쓸 때 Snappy를 지원하지 않습니다. | 예       |

> [!NOTE]
> Avro 파일에서는 열 이름에 공백이 지원되지 않습니다.

다음은 Azure Blob Storage에 대한 Avro 데이터 세트의 예입니다.

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Avro 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="avro-as-source"></a>Avro를 원본으로

복사 작업 ***\*source\**** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **AvroSource** 로 설정해야 합니다. | 예      |
| storeSettings | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 읽기 설정이 있습니다. **자세한 내용은 커넥터 문서 -> 복사 작업 속성 섹션을 참조하세요**. | 예       |

### <a name="avro-as-sink"></a>Avro로 싱크로

복사 작업 ***\*sink\**** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성을 **AvroSink** 로 설정해야 합니다. | 예      |
| formatSettings          | 속성 그룹입니다. 아래의 **Avro 쓰기 설정** 표를 참조하세요.| 예      |
| storeSettings | 데이터 저장소에 데이터를 쓰는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 쓰기 설정이 있습니다. **자세한 내용은 커넥터 문서 -> 복사 작업 속성 섹션을 참조하세요**. | 예       |

`formatSettings`에서 지원되는 **Avro 쓰기 설정**:

| 속성      | 설명                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings의 type을 **AvroWriteSettings** 로 설정해야 합니다. | 예                                                   |
| maxRowsPerFile | 폴더에 데이터를 쓸 때 여러 파일에 쓰도록 선택하고 파일당 최대 행 수를 지정할 수 있습니다.  | 예 |
| fileNamePrefix | `maxRowsPerFile`이 구성된 경우 적용할 수 있습니다.<br> 여러 파일에 데이터를 쓸 때 파일 이름 접두사를 지정합니다. 이 패턴은 `<fileNamePrefix>_00000.<fileExtension>`입니다. 지정하지 않는 경우 파일 이름 접두사가 자동으로 생성됩니다. 원본이 파일 기반 저장소 또는 [파티션 옵션 사용 데이터 저장소](copy-activity-performance-features.md)인 경우에는 이 속성이 적용되지 않습니다.  | 예 |

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)같은 데이터 저장소에서 Avro 형식을 읽고 쓸 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에는 Avro 원본에서 지원하는 속성이 나열되어 있습니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 와일드 카드 경로 | 와일드 카드 경로와 일치하는 모든 파일이 처리됩니다. 데이터 세트에 설정된 폴더 및 파일 경로를 재정의합니다. | 아니요 | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할된 파일 데이터의 경우 분할된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 아니요 | String | partitionRootPath |
| 파일 목록 | 원본이 처리할 파일을 나열하는 텍스트 파일을 가리키는지 여부입니다. | 아니요 | `true` 또는 `false` | fileList |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용하여 새 열을 만듭니다. | 아니요 | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제하거나 이동합니다. 컨테이너 루트에서 파일 경로가 시작됩니다. | 아니요 | 삭제: `true` 또는 `false` <br> 이동: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 마지막으로 수정한 사람으로 필터링 | 마지막으로 변경된 시간에 따라 파일을 필터링하도록 선택합니다. | 아니요 | 타임스탬프 | modifiedAfter <br> modifiedBefore |
| 파일을 찾을 수 없음 허용 | true이면 파일이 없는 경우 오류가 throw되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>싱크 속성

다음 표에는 Avro 싱크에서 지원하는 속성이 나열되어 있습니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | 아니요 | `true` 또는 `false` | truncate |
| 파일 이름 옵션 | 작성된 데이터의 명명 형식입니다. 기본적으로 파티션당 파일 하나이고 형식은 `part-#####-tid-<guid>`입니다. | 아니요 | 패턴: String <br> 파티션당: String[] <br> 열의 데이터로: String <br> 단일 파일로 출력: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| 모두 따옴표로 묶기 | 모든 값을 따옴표로 묶습니다. | 아니요 | `true` 또는 `false` | quoteAll |

## <a name="data-type-support"></a>데이터 형식 지원

### <a name="copy-activity"></a>복사 활동
Avro [복합 데이터 형식](https://avro.apache.org/docs/current/spec.html#schema_complex)은 복사 작업에서 지원되지 않습니다(레코드, 열거형, 배열, 매핑, 공용 구조체 및 고정).

### <a name="data-flows"></a>데이터 흐름
데이터 흐름에서 Avro 파일을 사용하는 경우 복합 데이터 형식을 읽고 쓸 수 있지만 먼저 데이터 세트에서 물리적 스키마를 지워야 합니다. 데이터 흐름에서 논리적 프로젝션을 설정하고 복합 구조체인 열을 파생시킨 다음 이러한 필드를 Avro 파일에 자동으로 매핑할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
