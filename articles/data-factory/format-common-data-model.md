---
title: Common Data Model 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 파이프라인에서 공통 데이터 모델 메타 데이터 시스템을 사용 하 여 데이터를 변환 합니다.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: makromer
ms.openlocfilehash: 5e97bb5a51bc636cf8f2227473f090e51bb49c1c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787701"
---
# <a name="common-data-model-format-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics의 공통 데이터 모델 형식
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

CDM(Common Data Model) 메타데이터 시스템을 사용하면 애플리케이션 및 비즈니스 프로세스에서 데이터 및 해당 의미를 쉽게 공유할 수 있습니다. 자세히 알아보려면 [Common Data Model](/common-data-model/) 개요를 참조하세요.

Azure Data Factory 및 Synapse 파이프라인에서는 데이터 흐름 매핑을 사용 하 여 [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2)에 저장 된 CDM 엔터티에서 데이터를 변환할 수 있습니다. 분할된 폴더에서 데이터를 CSV 또는 Parquet 형식으로 지정하는 CDM 엔터티 참조를 사용하여 CDM 형식으로 데이터를 싱크할 수도 있습니다. 

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

Common Data Model은 원본 및 싱크로서의 매핑 데이터 흐름에서 [인라인 데이터 세트](data-flow-source.md#inline-datasets)로 사용할 수 있습니다.

> [!NOTE]
> CDM 엔터티를 작성할 때는 참조로 사용할 기존 CDM 엔터티 정의(메타데이터 스키마)가 이미 정의되어 있어야 합니다. 데이터 흐름 싱크에서 해당 CDM 엔터티 파일을 읽고 필드 매핑을 위한 싱크로 스키마를 가져옵니다.

### <a name="source-properties"></a>원본 속성

다음 표에는 CDM 원본에서 지원하는 속성이 나열되어 있습니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 `cdm`여야 합니다. | 예 | `cdm` | format |
| 메타데이터 형식 | 데이터에 대한 엔터티 참조가 있는 위치입니다. CDM 버전 1.0을 사용하는 경우 매니페스트를 선택합니다. 1\.0보다 이전의 CDM 버전을 사용하는 경우 model.json을 선택합니다. | 예 | `'manifest'` 또는 `'model'` | manifestType |
| 루트 위치: 컨테이너 | CDM 폴더의 컨테이너 이름입니다. | 예 | String | fileSystem |
| 루트 위치: 폴더 경로 | CDM 폴더의 루트 폴더 위치입니다. | 예 | String | folderPath |
| 매니페스트 파일: 엔터티 경로 | 루트 폴더 안에 있는 엔터티의 폴더 경로입니다. | 아니요 | String | entityPath |
| 매니페스트 파일: 매니페스트 이름 | 매니페스트 파일의 이름입니다. 기본값은 'default'입니다.  | 예 | String | manifestName |
| 마지막으로 수정한 시간으로 필터링 | 마지막으로 변경된 시간에 따라 파일을 필터링하도록 선택합니다. | 아니요 | 타임스탬프 | modifiedAfter <br> modifiedBefore | 
| 스키마 연결된 서비스 | 모음이 위치하는 연결된 서비스입니다. | 예(매니페스트를 사용하는 경우) | `'adlsgen2'` 또는 `'github'` | corpusStore | 
| 엔터티 참조 컨테이너 | 모음이 위치하는 컨테이너입니다. | 예(매니페스트를 사용하고 모음이 ADLS Gen2에 있는 경우) | String | adlsgen2_fileSystem |
| 엔터티 참조 리포지토리 | GitHub 리포지토리 이름 | 예(매니페스트를 사용하고 모음이 GitHub에 있는 경우) | String | github_repository |
| 엔터티 참조 분기 | GitHub 리포지토리 분기입니다. | 예(매니페스트를 사용하고 모음이 GitHub에 있는 경우) | String |  github_branch |
| 모음 폴더 | 모음의 루트 위치입니다. | 예(매니페스트를 사용하는 경우) | String | corpusPath |
| 모음 엔터티 | 엔터티 참조의 경로입니다. | 예 | String | 엔터티 |
| 파일을 찾을 수 없음 허용 | true이면 파일이 없는 경우 오류가 throw되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

원본 및 싱크 변환에서 '엔터티 참조'를 선택하는 경우 다음 세 가지 옵션 중에서 엔터티 참조 위치를 선택할 수 있습니다.

* 로컬은 서비스에서 이미 사용 중인 매니페스트 파일에 정의 된 엔터티를 사용 합니다.
* 사용자 지정은 서비스에서 사용 하는 매니페스트 파일과 다른 엔터티 매니페스트 파일을 가리키도록 요청 합니다.
* 표준은 ```Github```에서 유지 관리되는 CDM 엔터티의 표준 라이브러리로부터 엔터티 참조를 사용합니다.

### <a name="sink-settings"></a>싱크 설정

* 쓰려는 엔터티의 정의가 포함된 CDM 엔터티 참조 파일을 가리킵니다.

:::image type="content" source="media/data-flow/common-data-model-111.png" alt-text="엔터티 설정":::

* 서비스에서 엔터티를 작성 하는 데 사용할 출력 파일의 파티션 경로와 형식을 정의 합니다.

:::image type="content" source="media/data-flow/common-data-model-222.png" alt-text="엔터티 형식":::

* 출력 파일 위치와 매니페스트 파일 위치 및 이름을 설정합니다.

:::image type="content" source="media/data-flow/common-data-model-333.png" alt-text="CDM 위치":::


#### <a name="import-schema"></a>스키마 가져오기

CDM은 인라인 데이터 세트로만 사용할 수 있으며 기본적으로 연결된 스키마가 없습니다. 열 메타데이터를 가져오려면 **프로젝션** 탭에서 **스키마 가져오기** 단추를 클릭합니다. 이렇게 하면 모음에서 지정한 열 이름과 데이터 형식을 참조할 수 있습니다. 스키마를 가져오려면 [데이터 흐름 디버그 세션](concepts-data-flow-debug-mode.md)이 활성 상태여야 하고 기존 CDM 엔터티 정의 파일을 가리켜야 합니다.

싱크 변환에서 데이터 흐름 열을 엔터티 속성에 매핑할 때 '매핑' 탭을 클릭하고 '스키마 가져오기'를 선택합니다. 서비스는 싱크 옵션에서 가리키는 엔터티 참조를 읽어 대상 CDM 스키마에 매핑할 수 있도록 합니다.

:::image type="content" source="media/data-flow/common-data-model-444.png" alt-text="CDM 싱크 설정":::

> [!NOTE]
>  Power BI 또는 Power Platform 데이터 흐름에서 시작된 model.json 원본 형식을 사용하는 경우 원본 변환에서 "모음 경로가 Null이거나 비어 있습니다." 오류가 발생할 수 있습니다. 이는 model.json 파일에 포함된 파티션 위치 경로의 서식 지정 문제 때문일 수 있습니다. 이를 수정하려면 다음 단계를 수행하세요. 

1. 텍스트 편집기에서 model.json 파일을 엽니다.
2. partitions.Location 속성을 찾습니다. 
3. 'blob.core.windows.net'을 'dfs.core.windows.net'으로 변경합니다.
4. URL에서 '%2F' 인코딩을 모두 '/'로 수정합니다.
5. ADF 데이터 흐름을 사용하는 경우 파티션 파일 경로의 특수 문자를 영숫자 값으로 바꾸거나 Azure Synapse 데이터 흐름으로 전환해야 합니다.

### <a name="cdm-source-data-flow-script-example"></a>CDM 원본 데이터 흐름 스크립트 예제

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>싱크 속성

다음 표에는 CDM 싱크에서 지원하는 속성이 나열되어 있습니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 `cdm`여야 합니다. | 예 | `cdm` | format |
| 루트 위치: 컨테이너 | CDM 폴더의 컨테이너 이름입니다. | 예 | String | fileSystem |
| 루트 위치: 폴더 경로 | CDM 폴더의 루트 폴더 위치입니다. | 예 | String | folderPath |
| 매니페스트 파일: 엔터티 경로 | 루트 폴더 안에 있는 엔터티의 폴더 경로입니다. | 아니요 | String | entityPath |
| 매니페스트 파일: 매니페스트 이름 | 매니페스트 파일의 이름입니다. 기본값은 'default'입니다. | 예 | String | manifestName |
| 스키마 연결된 서비스 | 모음이 위치하는 연결된 서비스입니다. | 예 | `'adlsgen2'` 또는 `'github'` | corpusStore | 
| 엔터티 참조 컨테이너 | 모음이 위치하는 컨테이너입니다. | 예(모음이 ADLS Gen2에 있는 경우) | String | adlsgen2_fileSystem |
| 엔터티 참조 리포지토리 | GitHub 리포지토리 이름 | 예(모음이 GitHub에 있는 경우) | String | github_repository |
| 엔터티 참조 분기 | GitHub 리포지토리 분기입니다. | 예(모음이 GitHub에 있는 경우) | String |  github_branch |
| 모음 폴더 | 모음의 루트 위치입니다. | 예 | String | corpusPath |
| 모음 엔터티 | 엔터티 참조의 경로입니다. | 예 | String | 엔터티 |
| 파티션 경로 | 파티션이 기록될 위치입니다. | 아니요 | String | partitionPath |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | 아니요 | `true` 또는 `false` | truncate |
| 형식 유형 | Parquet 형식을 지정하려면 선택합니다. | 아니요 | `parquet`, 지정된 경우 | subformat |
| 열 구분 기호 | DelimitedText에 쓰는 경우 열을 구분하는 방법입니다. | 예(DelimitedText에 쓰는 경우) | String | columnDelimiter |
| 첫 행을 헤더로 | DelimitedText를 사용하는 경우 열 이름이 헤더로 추가되는지 여부입니다. | 아니요 | `true` 또는 `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM 싱크 데이터 흐름 스크립트 예제

연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>다음 단계

매핑 데이터 흐름에서 [원본 변환](data-flow-source.md)을 만듭니다.
