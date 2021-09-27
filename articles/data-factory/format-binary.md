---
title: 이진 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 항목에서는 Azure Data Factory 및 Synapse Analytics에서 이진 형식을 처리 하는 방법에 대해 설명 합니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: b1000d83f674614213ad26aa9b926f6c9255720b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779657"
---
# <a name="binary-format-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics의 이진 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이진 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [Oracle Cloud Storage](connector-oracle-cloud-storage.md) 및 [SFTP](connector-sftp.md) 커넥터에 지원됩니다.

[복사 작업](copy-activity-overview.md), [GetMetadata 작업](control-flow-get-metadata-activity.md) 또는 [삭제 작업](delete-activity.md)에서 이진 데이터 세트를 사용할 수 있습니다. 이진 데이터 집합을 사용 하는 경우 서비스는 파일 콘텐츠를 구문 분석 하지 않고 그대로 처리 합니다. 

>[!NOTE]
>복사 작업에서 이진 데이터 세트를 사용하는 경우 이진 데이터 세트에서 이진 데이터 세트로만 복사할 수 있습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 이진 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 세트의 형식 속성을 **이진** 으로 설정해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`의 고유한 위치 형식 및 지원되는 속성이 있습니다. **자세한 내용은 커넥터 문서 -> 데이터 세트 속성 섹션을 참조하세요**. | 예      |
| 압축 | 파일 압축을 구성하는 속성 그룹입니다. 작업 실행 중 압축/압축 풀기를 수행하려는 경우 이 섹션을 구성합니다. | 예 |
| type | 이진 파일을 읽고/쓰는 데 사용되는 압축 코덱입니다. <br>허용 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **Tar** 또는 **TarGzip** 입니다. <br>**참고** 복사 작업을 통해 **ZipDeflate** / **TarGzip** / **Tar** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때, 기본적으로 파일은 `<path specified in dataset>/<folder named as source compressed file>/`과 같이 폴더로 추출됩니다. 즉, [복사 작업 원본](#binary-as-source)에서 `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder`를 사용하여 압축된 파일의 이름을 폴더 구조로 유지할지 여부를 제어합니다.| 예       |
| 수준 | 압축 비율입니다. 복사 작업 싱크에서 데이터 세트를 사용하는 경우에 적용됩니다.<br>허용 값은 **최적** 또는 **가장 빠름** 입니다.<br>- **가장 빠름:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업을 최대한 빨리 완료해야 합니다.<br>- **최적**: 작업이 완료되는 데 시간이 오래 걸리더라도 최적으로 압축해야 합니다. 자세한 내용은 [압축 수준](/dotnet/api/system.io.compression.compressionlevel) 항목을 참조하세요. | 예       |

다음은 Azure Blob Storage에서의 이진 데이터 세트의 예입니다.

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 이진 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

>[!NOTE]
>복사 작업에서 이진 데이터 세트를 사용하는 경우 이진 데이터 세트에서 이진 데이터 세트로만 복사할 수 있습니다.

### <a name="binary-as-source"></a>이진을 원본으로

복사 작업 ***\*원본\**** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 형식 속성은 **BinarySource** 로 설정해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **이진 읽기 설정** 표를 참조하세요. | 예       |
| storeSettings | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 읽기 설정이 있습니다. **자세한 내용은 커넥터 문서 -> 복사 작업 속성 섹션을 참조하세요**. | 예       |

`formatSettings` 아래에서 지원되는 **이진 읽기 설정** 은 다음과 같습니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings의 유형을 **Binaryreadsettings** 로 설정해야 합니다. | 예      |
| compressionProperties | 지정된 압축 코덱에 대한 데이터의 압축을 푸는 방법에 대한 속성 그룹입니다. | 예       |
| preserveZipFileNameAsFolder<br>( *`compressionProperties`->`type`아래 `ZipDeflateReadSettings`* 으로) | **ZipDeflate** 압축을 사용하여 입력 데이터 세트를 구성할 때 적용됩니다. 복사 중에 원본 zip 파일 이름을 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **True (기본값)** 로 설정 된 경우 서비스는 압축을 푼 파일을에 씁니다 `<path specified in dataset>/<folder named as source zip file>/` .<br>- **false** 로 설정하면, 서비스가 압축을 푼 파일을 `<path specified in dataset>`에 직접 씁니다. 레이싱 또는 예기치 않은 동작을 방지하기 위해 다른 원본 zip 파일에 중복된 파일 이름이 없는지 확인합니다.  | 예 |
| preserveCompressionFileNameAsFolder<br>( *`compressionProperties`->`type` 아래 `TarGZipReadSettings` 또는 `TarReadSettings`* 으로) | 입력 데이터 세트가 **TarGzip**/**Tar** 압축을 사용하여 구성될 때 적용됩니다. 복사 중에 원본 압축 파일 이름을 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **true(기본값)** 로 설정하면, 서비스가 압축 해제한 파일을 `<path specified in dataset>/<folder named as source compressed file>/`에 씁니다. <br>- **false** 로 설정하면, 서비스가 압축 해제한 파일을 `<path specified in dataset>`에 직접 씁니다. 레이싱 또는 예기치 않은 동작을 방지하기 위해 다른 원본 파일에 중복된 파일 이름이 없는지 확인합니다. | 예 |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
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

### <a name="binary-as-sink"></a>이진을 싱크로

복사 작업 ***\*싱크\**** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 형식 속성은 **BinarySink** 로 설정해야 합니다. | 예      |
| storeSettings | 데이터 저장소에 데이터를 쓰는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 쓰기 설정이 있습니다. **자세한 내용은 커넥터 문서 -> 복사 작업 속성 섹션을 참조하세요**. | 예       |

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [삭제 작업](delete-activity.md)