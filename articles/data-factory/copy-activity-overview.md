---
title: 복사 활동
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics의 복사 작업에 대해 알아봅니다. 지원되는 원본 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 데 사용할 수 있습니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 2c7c2a6d0056cb16f2ff79cb662cce2604d835b5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767625"
---
# <a name="copy-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics의 복사 작업

> [!div class="op_single_selector" title1="사용 중인 Data Factory 버전을 선택합니다."]
> * [버전 1](v1/data-factory-data-movement-activities.md)
> * [현재 버전](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 및 Synapse 파이프라인에서는 복사 작업을 사용하여 온-프레미스와 클라우드에 있는 데이터 저장소 간에 데이터를 복사할 수 있습니다. 데이터를 복사한 후 다른 작업을 사용하여 추가 변환 및 분석을 수행할 수 있습니다. 복사 작업을 통해 BI(비즈니스 인텔리전스) 및 애플리케이션에서 사용할 수 있도록 변환 및 분석 결과를 게시할 수도 있습니다.

:::image type="content" source="media/copy-activity-overview/copy-activity.png" alt-text="복사 작업의 역할":::

복사 작업은 [통합 런타임](concepts-integration-runtime.md)에서 실행됩니다. 다양한 데이터 복사 시나리오에서 다양한 유형의 통합 런타임을 사용할 수 있습니다.

* 모든 IP에서 인터넷을 통해 공개적으로 액세스할 수 있는 두 데이터 저장소 간에 데이터를 복사하는 경우 복사 작업에 Azure Integration Runtime을 사용할 수 있습니다. 이 통합 런타임은 안전하고, 안정적이고, 확장 가능하며 [전역적으로 사용할 수](concepts-integration-runtime.md#integration-runtime-location) 있습니다.
* 액세스 제어가 적용된 온-프레미스 또는 네트워크(예: Azure 가상 네트워크)에(서) 데이터를 복사하는 경우 자체 호스팅 통합 런타임을 설정해야 합니다.

통합 런타임을 각각의 원본 및 싱크 데이터 저장소와 연결해야 합니다. 복사 작업에서 사용할 통합 런타임을 결정하는 방법에 대한 자세한 내용은 [사용할 IR 결정](concepts-integration-runtime.md#determining-which-ir-to-use)을 참조하세요.

원본에서 싱크로 데이터를 복사하기 위해 복사 작업을 실행하는 서비스는 다음 단계를 수행합니다.

1. 원본 데이터 저장소에서 데이터를 읽습니다.
2. 직렬화/역직렬화, 압축/압축 해제, 열 매핑 등을 수행합니다. 이러한 작업은 입력 데이터 세트, 출력 데이터 세트 및 복사 작업의 구성에 따라 달라집니다.
3. 싱크/대상 데이터 저장소에 데이터를 씁니다.

:::image type="content" source="media/copy-activity-overview/copy-activity-overview.png" alt-text="복사 작업 개요":::

## <a name="supported-data-stores-and-formats"></a>지원되는 데이터 저장소 및 형식

[!INCLUDE [data-factory-v2-supported-data-stores](includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>지원되는 파일 형식

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

복사 작업을 사용하여 두 파일 기반 데이터 저장소 간에 있는 그대로 파일을 복사할 수 있습니다. 이 때 데이터는 직렬화 또는 역직렬화 없이 효율적으로 복사됩니다. 또한 지정된 형식의 파일을 구문 분석하거나 생성할 수도 있습니다. 예를 들어 다음을 수행할 수 있습니다.

* SQL Server 데이터베이스에서 데이터를 복사하고 Parquet 형식으로 Azure Data Lake Storage Gen2에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트(CSV) 형식의 파일을 복사하여 Avro 형식으로 Azure Blob Storage에 씁니다.
* 온-프레미스 파일 시스템에서 압축된 파일을 복사하여 즉시 압축을 풀고 압축을 푼 파일을 Azure Data Lake Storage Gen2에 씁니다.
* Azure Blob Storage에서 Gzip 압축 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL Database에 씁니다.
* 직렬화/역직렬화 또는 압축/압축 해제가 필요한 작업이 더 많이 있습니다.

## <a name="supported-regions"></a>지원되는 지역

복사 작업을 사용하는 서비스는 [Azure Integration Runtime 위치](concepts-integration-runtime.md#integration-runtime-location)에 표시된 지역 및 지리에서 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지에서는 대개 지역 간 홉이 없는 효율적인 데이터 이동이 가능합니다. 특정 지역에서 Data Factory, Synapse 작업 영역 및 데이터 이동을 사용할 수 있는지 확인하려면 [지역별 제품](https://azure.microsoft.com/regions/#services)을 참조하세요.

## <a name="configuration"></a>Configuration

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

일반적으로 Azure Data Factory 또는 Synapse 파이프라인에서 복사 작업을 사용하려면 다음이 필요합니다.

1. **원본 데이터 저장소 및 싱크 데이터 저장소에 대한 연결된 서비스를 만듭니다.** 지원되는 커넥터 목록은 이 문서의 [지원되는 데이터 저장소 및 형식](#supported-data-stores-and-formats) 섹션에서 찾을 수 있습니다. 구성 정보 및 지원되는 속성은 커넥터 문서의 '연결된 서비스 속성' 섹션을 참조하세요. 
2. **원본 및 싱크에 대한 데이터 세트를 만듭니다.** 구성 정보 및 지원되는 속성은 원본 및 싱크 커넥터 문서의 '데이터 세트 속성' 섹션을 참조하세요.
3. **복사 작업을 사용하는 파이프라인을 만듭니다.** 다음 섹션에서 예제를 제공합니다.

### <a name="syntax"></a>구문

복사 작업의 다음 템플릿은 지원되는 속성의 전체 목록을 포함합니다. 시나리오에 적합한 속성을 지정하세요.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>구문 세부 정보

| 속성 | 설명 | 필수 여부 |
|:--- |:--- |:--- |
| type | 복사 작업의 경우 `Copy`로 설정합니다. | 예 |
| 입력 | 원본 데이터를 가리키도록 만든 데이터 세트를 지정합니다. 복사 작업에서는 하나의 입력만 지원합니다. | 예 |
| outputs | 싱크 데이터를 가리키도록 만든 데이터 세트를 지정합니다. 복사 작업에서는 하나의 출력만 지원합니다. | 예 |
| typeProperties | 복사 작업을 구성할 속성을 지정합니다. | 예 |
| source | 데이터 검색을 위한 복사 원본 유형 및 해당 속성을 지정합니다.<br/>자세한 내용은 [지원되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 열거된 커넥터 문서의 '복사 작업 속성' 섹션을 참조하세요. | 예 |
| 싱크 | 데이터 쓰기를 위한 복사 싱크 유형 및 해당 속성을 지정합니다.<br/>자세한 내용은 [지원되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 열거된 커넥터 문서의 '복사 작업 속성' 섹션을 참조하세요. | 예 |
| 번역기 | 원본에서 싱크로의 명시적 열 매핑을 지정합니다. 이 속성은 기본 복사 동작이 사용자의 요구를 충족하지 않는 경우에 적용됩니다.<br/>자세한 내용은 [복사 작업의 스키마 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요. | 예 |
| dataIntegrationUnits | [Azure Integration Runtime](concepts-integration-runtime.md)이 데이터 복사에 사용하는 전력량을 나타내는 측정값을 지정합니다. 이전에는 이러한 단위를 클라우드 DMU(데이터 이동 단위)라고 했습니다. <br/>자세한 내용은 [데이터 통합 단위](copy-activity-performance-features.md#data-integration-units)를 참조하세요. | 예 |
| parallelCopies | 복사 작업이 원본에서 데이터를 읽어 싱크에 쓸 때 사용할 병렬 처리를 지정합니다.<br/>자세한 내용은 [병렬 복사](copy-activity-performance-features.md#parallel-copy)를 참조하세요. | 예 |
| 보존 | 데이터를 복사하는 동안 메타데이터/ACL을 유지할지 여부를 지정합니다. <br/>자세한 내용은 [메타데이터 유지](copy-activity-preserve-metadata.md)를 참조하세요. |예 |
| enableStaging<br/>stagingSettings | 데이터를 원본에서 싱크로 직접 복사하는 대신 Blob Storage에서 중간 데이터를 준비할지 여부를 선택합니다.<br/>유용한 시나리오 및 구성 세부 정보는 [단계별 복사](copy-activity-performance-features.md#staged-copy)를 참조하세요. | 예 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 데이터를 원본에서 싱크로 복사할 때 호환되지 않는 행을 처리하는 방법을 선택합니다.<br/>자세한 내용은 [내결함성](copy-activity-fault-tolerance.md)을 참조하세요. | 예 |

## <a name="monitoring"></a>모니터링

Azure Data Factory 및 Synapse 파이프라인에서 실행되는 복사 작업 실행을 시각적으로 또는 프로그래밍 방식으로 모니터링할 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-monitoring.md)을 참조하세요.

## <a name="incremental-copy"></a>증분 복사

Data Factory 및 Synapse 파이프라인을 사용하면 원본 데이터 저장소에서 싱크 데이터 저장소로 델타 데이터를 증분 복사할 수 있습니다. 자세한 내용은 [자습서: 증분 방식으로 데이터 복사](tutorial-incremental-copy-overview.md)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝

[복사 작업 모니터링](copy-activity-monitoring.md) 환경에는 각 활동 실행에 대한 복사 성능 통계가 표시됩니다. [복사 작업 성능 및 확장성 가이드](copy-activity-performance.md)에서는 복사 작업을 통한 데이터 이동 성능에 영향을 주는 주요 요인에 대해 설명합니다. 또한 테스트 중 관찰된 성능 값을 나열하고 복사 작업 성능을 최적화하는 방법을 설명합니다.

## <a name="resume-from-last-failed-run"></a>마지막으로 실패한 실행에서 다시 시작

복사 작업은 파일 기반 저장소 간에 이진 형식을 사용하는 대용량 파일을 있는 그대로 복사하고, 예를 들어 Amazon S3에서 Azure Data Lake Storage Gen2로 데이터를 마이그레이션하기 위해 원본에서 싱크로 폴더/파일 계층 구조를 유지하도록 선택하는 경우 마지막으로 실패한 실행에서 다시 시작을 지원합니다. [Amazon S3](connector-amazon-simple-storage-service.md), [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md) [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [Oracle Cloud Storage](connector-oracle-cloud-storage.md) 및 [SFTP](connector-sftp.md)와 같은 파일 기반 커넥터에 적용됩니다.

다음 두 가지 방법으로 복사 작업 다시 시작을 활용할 수 있습니다.

- **작업 수준 다시 시도:** 복사 작업에 대한 재시도 횟수를 설정할 수 있습니다. 파이프라인을 실행하는 동안 이 복사 작업이 실행되지 않으면 다음 자동 다시 시도가 마지막 시도의 실패 지점에서 시작됩니다.
- **실패한 작업에서 다시 실행:** 파이프라인 실행이 완료된 후 ADF UI 모니터링 보기에서 또는 프로그래밍 방식으로 실패한 작업에서 다시 실행을 트리거할 수도 있습니다. 실패한 작업이 복사 작업인 경우 파이프라인은 이 작업에서 다시 실행될 뿐만 아니라 이전 실행의 실패 지점에서 다시 시작합니다.

    :::image type="content" source="media/copy-activity-overview/resume-copy.png" alt-text="복사 다시 시작":::

주의할 사항:

- 다시 시작은 파일 수준에서 발생합니다. 파일을 복사하는 동안 복사 작업이 실패하는 경우 다음 실행에서 해당 파일이 다시 복사됩니다.
- 다시 시작이 제대로 작동하려면 다시 실행 간에 복사 작업 설정을 변경하지 마세요.
- Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 및 Google Cloud Storage에서 데이터를 복사하는 경우 복사 작업은 복사된 파일 수에 관계없이 다시 시작될 수 있습니다. 원본으로 사용되는 나머지 파일 기반 커넥터에서는 현재 복사 작업이 제한된 개수의 파일에서 다시 시작을 지원합니다. 이는 일반적으로 수십 개 범위이고 파일 경로의 길이에 따라 달라집니다. 이 범위를 초과하는 파일은 다시 실행하는 동안 다시 복사됩니다.

이진 파일 복사 이외의 시나리오에서는 복사 작업 다시 실행은 처음부터 시작됩니다.

## <a name="preserve-metadata-along-with-data"></a>데이터와 함께 메타데이터 유지

데이터 레이크 마이그레이션과 같은 시나리오에서 데이터를 원본에서 싱크로 복사하는 동안 복사 작업을 사용하여 데이터와 함께 메타데이터 및 ACL을 유지하도록 선택할 수도 있습니다. 자세한 내용은 [메타데이터 유지](copy-activity-preserve-metadata.md)를 참조하세요.

## <a name="schema-and-data-type-mapping"></a>스키마 및 데이터 형식 매핑

복사 작업이 원본 데이터를 싱크에 매핑하는 방법은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

## <a name="add-additional-columns-during-copy"></a>복사 중에 열 추가

원본 데이터 저장소에서 싱크로 데이터를 복사하는 것 외에도 싱크에 복사할 데이터 열을 추가하도록 구성할 수 있습니다. 예를 들면 다음과 같습니다.

- 파일 기반 원본에서 복사하는 경우 데이터를 가져온 파일을 추적할 추가 열로 상대 파일 경로를 저장합니다.
- 지정한 원본 열을 다른 열로 복제합니다. 
- ADF 식으로 열을 추가하여 파이프라인 이름/파이프라인 ID와 같은 ADF 시스템 변수를 연결하거나 업스트림 작업의 출력에서 다른 동적 값을 저장합니다.
- 정적 값을 사용하여 다운스트림 소비 요구 사항을 충족하는 열을 추가합니다.

복사 작업 원본 탭에서 다음 구성을 찾을 수 있습니다. 또한 정의된 열 이름을 사용하여 일반적으로 복사 작업 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)에서 이러한 추가 열도 매핑할 수 있습니다. 

:::image type="content" source="./media/copy-activity-overview/copy-activity-add-additional-columns.png" alt-text="복사 작업에서 열 추가":::

>[!TIP]
>이 기능은 최신 데이터 세트 모델에서 작동합니다. UI에서 이 옵션이 표시되지 않으면 새 데이터 세트를 만들어 보세요.

프로그래밍 방식으로 구성하려면 복사 작업 원본에 `additionalColumns` 속성을 추가합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| additionalColumns | 싱크에 복사할 추가 데이터 열을 추가합니다.<br><br>`additionalColumns` 배열의 각 개체는 추가 열을 나타냅니다. `name`은 열 이름을 정의하고 `value`는 해당 열의 데이터 값을 나타냅니다.<br><br>허용되는 데이터 값은 다음과 같습니다.<br>-  **`$$FILEPATH`** - 예약 변수는 데이터 세트에 지정된 폴더 경로에 원본 파일의 상대 경로를 저장함을 나타냅니다. 파일 기반 원본에 적용됩니다.<br>-  **`$$COLUMN:<source_column_name>`** - 예약 변수 패턴은 지정한 원본 열을 다른 열로 복제함을 나타냅니다.<br>- **식**<br>- **정적 값** | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>싱크 테이블 자동 생성

SQL 데이터베이스/Azure Synapse Analytics로 데이터를 복사하는 경우 대상 테이블이 존재하지 않는 경우 복사 작업은 원본 데이터에 기반한 자동 생성을 지원합니다. 이 기능은 데이터 로드를 빠르게 시작하고 SQL 데이터베이스/Azure Synapse Analytics를 평가하는 데 도움을 주기 위한 것입니다. 데이터 수집 후에는 필요에 따라 싱크 테이블 스키마를 검토하고 조정할 수 있습니다.

이 기능은 모든 원본에서 다음 싱크 데이터 저장소로 데이터를 복사할 때 지원됩니다. *ADF 작성 UI* –> *복사 작업 싱크* –> *테이블 옵션* –> *테이블 자동 생성* 에서 또는 복사 작업 싱크 페이로드의 `tableOption` 속성을 통해 옵션을 찾을 수 있습니다.

- [Azure SQL Database](connector-azure-sql-database.md)
- [Azure SQL Database Managed Instance](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

:::image type="content" source="media/copy-activity-overview/create-sink-table.png" alt-text="싱크 테이블 만들기":::

## <a name="fault-tolerance"></a>내결함성

기본적으로 복사 작업은 원본 데이터 행이 싱크 데이터 행과 호환되지 않는 경우 데이터 복사를 중지하고 오류를 반환합니다. 성공적으로 복사를 수행하려면 호환되지 않는 행은 건너뛰고 로그하면서 호환되는 데이터만 복사하도록 복사 작업을 구성할 수 있습니다. 자세한 내용은 [복사 작업 내결함성](copy-activity-fault-tolerance.md)을 참조하세요.

## <a name="data-consistency-verification"></a>데이터 일관성 확인

원본의 데이터를 대상 저장소로 이동하는 경우, 복사 작업에서 추가로 데이터 일관성을 확인하여 데이터가 원본에서 대상 저장소로 복사되도록 할 뿐만 아니라 원본과 대상 저장소 간에 일관성을 확인하는 옵션을 제공합니다. 데이터 이동 중에 일관되지 않은 파일이 발견되면 복사 작업을 중단하거나, 일관되지 않은 파일을 건너뛰도록 내결함성 설정을 사용하도록 설정하여 나머지를 계속 복사합니다. 복사 작업에서 세션 로그 설정을 사용하도록 설정하여 건너뛴 파일 이름을 가져올 수 있습니다. 자세한 내용은 [복사 작업의 데이터 일관성 확인](copy-activity-data-consistency.md)을 참조하세요.

## <a name="session-log"></a>세션 로그
복사한 파일 이름을 로그할 수 있습니다. 그러면 복사 작업 세션 로그를 검토하여 데이터를 원본 저장소에서 대상 저장소로 복사할 뿐 아니라 원본 및 대상 저장소 간에 일관성을 유지할 수 있습니다. 자세한 내용은 [복사 작업의 세션 로그](copy-activity-log.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 퀵 스타트, 자습서 및 샘플을 참조하세요.

- [동일한 Azure Blob Storage 계정에서 한 위치의 데이터를 다른 위치에 복사](quickstart-create-data-factory-dot-net.md)
- [Azure Blob Storage에서 Azure SQL Database로 데이터 복사](tutorial-copy-data-dot-net.md)
- [SQL Server 데이터베이스에서 Azure로 데이터 복사](tutorial-hybrid-copy-powershell.md)
