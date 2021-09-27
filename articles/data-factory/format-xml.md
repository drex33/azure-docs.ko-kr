---
title: XML 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 항목에서는 Azure Data Factory 및 Synapse Analytics 파이프라인에서 XML 형식을 처리 하는 방법에 대해 설명 합니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 202c2ea7014ea0295fb342b1fc7fabd70d87ac29
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743555"
---
# <a name="xml-format-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Azure Data Factory 및 Synapse Analytics 파이프라인의 XML 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**XML 파일을 구문 분석** 하려면 이 문서의 내용을 따르세요. 

XML 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md),  [Oracle Cloud Storage](connector-oracle-cloud-storage.md) 및 [SFTP](connector-sftp.md) 커넥터에 지원됩니다. 원본으로 지원되며 싱크로는 지원되지 않습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 XML 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 세트의 형식 속성을 **Xml** 로 설정해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`의 고유한 위치 형식 및 지원되는 속성이 있습니다. **자세한 내용은 커넥터 문서 -> 데이터 세트 속성 섹션을 참조하세요**. | 예      |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용되는 인코딩 형식입니다. <br>허용되는 값: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| 예       |
| nullValue | Null 값의 문자열 표현을 지정합니다.<br/>기본값은 **빈 문자열** 입니다. | 예 |
| 압축 | 파일 압축을 구성하는 속성 그룹입니다. 작업 실행 중 압축/압축 풀기를 수행하려는 경우 이 섹션을 구성합니다. | 예 |
| type<br>( *`compression`아래*) | XML 파일을 읽고 쓰는 데 사용되는 압축 코덱입니다. <br>허용되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Tar**, **snappy** 또는 **lz4** 입니다. 기본값은 압축되지 않습니다.<br>**참고** 현재 복사 작업은 “snappy” & “lz4”을 지원하지 않으며, 매핑 데이터 흐름은 “ZipDeflate”, “TarGzip”, “Tar”를 지원하지 않습니다.<br>**참고** 복사 작업을 통해 **ZipDeflate** / **TarGzip** / **Tar** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때, 기본적으로 파일은 `<path specified in dataset>/<folder named as source compressed file>/`과 같이 폴더로 추출됩니다. 즉, [복사 작업 원본](#xml-as-source)에서 `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder`를 사용하여 압축된 파일의 이름을 폴더 구조로 유지할지 여부를 제어합니다. | 아니요.  |
| 수준<br/>( *`compression` 아래*) | 압축 비율입니다. <br>허용 값은 **최적** 또는 **가장 빠름** 입니다.<br>- **가장 빠름:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업을 최대한 빨리 완료해야 합니다.<br>- **최적**: 작업이 완료되는 데 시간이 오래 걸리더라도 최적으로 압축해야 합니다. 자세한 내용은 [압축 수준](/dotnet/api/system.io.compression.compressionlevel) 항목을 참조하세요. | 예       |

다음은 Azure Blob Storage에 대한 XML 데이터 세트의 예입니다.

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 XML 원본에서 지원하는 속성의 목록을 제공합니다.

[스키마 매핑](copy-activity-schema-and-type-mapping.md)에서 XML 데이터 및 싱크 데이터 저장소/포맷 매핑하는 방법 알아보기. XML 파일을 미리 볼 때 데이터는 JSON 계층 구조와 함께 표시되며 JSON 경로를 사용하여 필드를 가리킵니다.

### <a name="xml-as-source"></a>XML 원본

복사 작업 ***\*원본\**** 섹션에서 지원되는 속성은 다음과 같습니다. [XML 커넥터 동작](#xml-connector-behavior)에 대한 자세한 정보.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 형식 속성을 **XmlSource** 로 설정해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **XML 읽기 설정** 표를 참조하세요. | 예       |
| storeSettings | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings` 아래에 고유의 지원되는 읽기 설정이 있습니다. **자세한 내용은 커넥터 문서 -> 복사 작업 속성 섹션을 참조하세요**. | 예       |

`formatSettings`에서 지원되는 **XML 읽기 설정**:

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings의 형식을 **XmlReadSettings** 로 설정해야 합니다. | 예      |
| validationMode | XML 스키마의 유효성을 검사할지 여부를 지정합니다.<br>허용되는 값은 **none**(기본값, 유효성 검사하지 않음), **xsd**(XSD를 사용하여 유효성 검사), **dtd**(DTD를 사용하여 유효성 검사)입니다. | 예 |
| 네임스페이스 | XML 파일을 구문 분석할 때 네임스페이스를 사용할지 여부를 지정합니다. 허용되는 값은 **true**(기본값), **false** 입니다. | 예 |
| namespacePrefixes | Xml 파일을 구문 분석할 때 필드의 이름을 결정하는 데 사용되는 네임스페이스 URI-접두사 매핑<br/>XML 파일에 네임스페이스 및 네임스페이스가 사용되는 경우 기본적으로 필드 이름은 XML 문서에 있는 것과 동일합니다.<br>이 맵에서 네임스페이스 URI에 대해 정의된 항목이 있는 경우 필드 이름은 `prefix:fieldName` 입니다. | 예 |
| detectDataType | 정수, 더블 및 부울 데이터 형식의 검색 여부. 허용되는 값은 **true**(기본값), **false** 입니다.| 예 |
| compressionProperties | 지정된 압축 코덱에 대한 데이터의 압축을 푸는 방법에 대한 속성 그룹입니다. | 예       |
| preserveZipFileNameAsFolder<br>( *`compressionProperties`->`type`아래 `ZipDeflateReadSettings`* 으로)  | **ZipDeflate** 압축을 사용하여 입력 데이터 세트를 구성할 때 적용됩니다. 복사 중에 원본 zip 파일 이름을 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **true(기본값)** 로 설정하면, 서비스가 압축을 푼 파일을 `<path specified in dataset>/<folder named as source zip file>/`에 씁니다.<br>- **false** 로 설정하면, 서비스가 압축을 푼 파일을 `<path specified in dataset>`에 직접 씁니다. 레이싱 또는 예기치 않은 동작을 방지하기 위해 다른 원본 zip 파일에 중복된 파일 이름이 없는지 확인합니다.  | 예 |
| preserveCompressionFileNameAsFolder<br>( *`compressionProperties`->`type` 아래 `TarGZipReadSettings` 또는 `TarReadSettings`* 으로) | 입력 데이터 세트가 **TarGzip**/**Tar** 압축을 사용하여 구성될 때 적용됩니다. 복사 중에 원본 압축 파일 이름을 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **true(기본값)** 로 설정하면, 서비스가 압축 해제한 파일을 `<path specified in dataset>/<folder named as source compressed file>/`에 씁니다. <br>- **false** 로 설정하면, 서비스가 압축 해제한 파일을 `<path specified in dataset>`에 직접 씁니다. 레이싱 또는 예기치 않은 동작을 방지하기 위해 다른 원본 파일에 중복된 파일 이름이 없는지 확인합니다. | 예 |

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) 같은 데이터 저장소에서 XML 형식을 읽을 수 있습니다. XML 데이터 세트나 [인라인 데이터 세트](data-flow-source.md#inline-datasets)를 사용하여 XML 파일을 가리킬 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 XML 원본에서 지원하는 속성을 나열합니다. 해당 속성은 **원본 옵션** 탭에서 편집할 수 있습니다. [XML 커넥터 동작](#xml-connector-behavior)에 대한 자세한 정보. 인라인 데이터 세트를 사용하는 경우 [데이터 세트 속성](#dataset-properties) 섹션에 설명된 속성과 동일한 추가 파일 설정이 표시됩니다. 

| 이름 | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 와일드 카드 경로 | 와일드 카드 경로와 일치하는 모든 파일이 처리됩니다. 데이터 세트에 설정된 폴더 및 파일 경로를 재정의합니다. | 예 | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할된 파일 데이터의 경우 분할된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 예 | String | partitionRootPath |
| 파일 목록 | 원본이 처리할 파일을 나열하는 문자 파일을 가리키고 있는지 여부. | 예 | `true` 또는 `false` | fileList |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용하여 새 열을 만듭니다. | 예 | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제하거나 이동합니다. 컨테이너 루트에서 파일 경로가 시작됩니다. | 예 | 삭제: `true` 또는 `false` <br> 이동: `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| 마지막으로 수정한 사람으로 필터링 | 마지막으로 변경된 시간에 따라 파일을 필터링하도록 선택합니다. | 예 | 타임스탬프 | modifiedAfter <br>modifiedBefore |
| 유효성 검사 모드 | XML 스키마의 유효성을 검사할지 여부를 지정합니다. | 예 | `None` (기본값, 유효성 검사하지 않음)<br>`xsd` (XSD를 사용하여 유효성 검사)<br>`dtd` (DTD를 사용하여 유효성 검사) | validationMode |
| 네임스페이스 | XML 파일을 구문 분석할 때 네임스페이스를 사용할지 여부를 지정합니다. | 예 | `true`(기본값) 또는 `false` | 네임스페이스 |
| 네임스페이스 접두사 쌍 | Xml 파일을 구문 분석할 때 필드의 이름을 결정하는 데 사용되는 네임스페이스 URI-접두사 매핑<br/>XML 파일에 네임스페이스 및 네임스페이스가 사용되는 경우 기본적으로 필드 이름은 XML 문서에 있는 것과 동일합니다.<br>이 맵에서 네임스페이스 URI에 대해 정의된 항목이 있는 경우 필드 이름은 `prefix:fieldName` 입니다. | 예 | 패턴이 포함된 배열`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| 파일을 찾을 수 없음 | true이면 파일이 없는 경우 오류가 throw되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>XML 원본 스크립트의 예

아래 스크립트는 데이터 세트 모드를 사용하는 매핑 데이터 흐름의 XML 원본 구성의 예입니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

아래 스크립트는 인라인 데이터 세트 모드를 사용하는 XML 원본 구성의 예입니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>XML 커넥터 동작

XML을 원본으로 사용하는 경우 다음에 유의하세요.

- XML 특성:

    - 요소의 특성은 계층 구조에서 요소의 하위 필드로 구문 분석됩니다.
    - 특성 필드의 이름은 `@attributeName` 패턴을 따릅니다.

- XML 스키마 유효성 검사:

    - 스키마의 유효성을 검사하지 않거나 XSD 또는 DTD를 사용하여 스키마의 유효성을 검사하도록 선택할 수 있습니다.
    - XSD 또는 DTD를 사용하여 XML 파일의 유효성을 검사하는 경우에는 XSD/DTD를 상대 경로를 통해 XML 파일 내에서 참조해야 합니다.

- 네임스페이스 처리:

    - 데이터 흐름을 사용하는 경우 네임스페이스를 비활성화할 수 있습니다. 이 경우 네임스페이스를 정의하는 특성은 일반 특성으로 구문 분석됩니다.
    - 네임스페이스를 사용하는 경우 요소와 특성의 이름은 기본값으로 `namespaceUri,elementName`과 `namespaceUri,@attributeName` 패턴을 따릅니다. 원본에서 각 네임스페이스 URI에 대한 네임스페이스 접두사를 정의할 수 있습니다. 이 경우에는 요소 및 특성의 이름은 대신 `definedPrefix:elementName` 또는 `definedPrefix:@attributeName` 패턴을 따릅니다.

- 값 열:

    - XML 요소에 단순 문자값과 특성/자식 요소가 둘 다 있는 경우 단순 문자값은 기본 제공 필드 이름 `_value_`을 포함하는 ‘값 열’의 값으로 구문 분석됩니다. 적용되는 경우에도 요소의 네임스페이스를 상속합니다.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
