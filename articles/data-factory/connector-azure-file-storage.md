---
title: Azure Files에서 또는 Azure Files로 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory를 사용하여 Azure Files에서 지원되는 싱크 데이터 저장소로, 또는 지원되는 원본 데이터 저장소에서 Azure Files로 데이터를 복사하는 방법에 대해 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 03/17/2021
ms.openlocfilehash: 50136700ce4cc39a2a8166ce4c7d7d2960b53990
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123313924"
---
# <a name="copy-data-from-or-to-azure-files-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Files에서 또는 Azure Files로부터 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Files에서 또는 Azure Files로 데이터를 복사하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Files 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)

Azure Files에서 지원되는 싱크 데이터 저장소로 데이터를 복사하거나 지원되는 원본 데이터 저장소에서 Azure Files로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 Azure Files 커넥터는 다음을 지원합니다.

- 계정 키 또는 서비스 SAS(공유 액세스 서명) 인증을 사용하여 파일을 복사합니다.
- 파일을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 파일을 붙여넣거나 생성합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-files-using-ui"></a>UI를 사용하여 Azure Files에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Files에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택한 후 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

2. 파일을 검색하고 레이블이 *Azure File Storage* 인 Azure Files 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-file-storage/azure-file-storage-connector.png" alt-text="Azure File Storage 커넥터의 스크린샷":::    

1. 서비스 세부 정보를 구성하고, 연결을 테스트하고, 새로운 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-file-storage/configure-azure-file-storage-linked-service.png" alt-text="Azure File Storage에 연결된 서비스 구성의 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Azure Files와 관련된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

이 Azure Files 커넥터는 다음 인증 유형을 지원합니다. 자세한 내용은 해당 섹션을 참조하세요.

- [계정 키 인증](#account-key-authentication)
- [공유 액세스 서명 인증](#shared-access-signature-authentication)

>[!NOTE]
> ADF 작성 UI에서 "기본 인증"으로 표시된 [레거시 모델](#legacy-model)과 함께 Azure Files에 연결된 서비스를 사용하는 경우에는 있는 그대로 지원되지만 앞으로는 새 모델을 사용하는 것이 좋습니다. 레거시 모델은 SMB(서버 메시지 블록)를 통해 스토리지에서 데이터를 전송하고 새 모델은 처리량이 더 우수한 스토리지 SDK를 사용합니다. 업그레이드하려면 연결된 서비스를 편집하여 인증 방법을 "계정 키" 또는 "SAS URI"로 전환할 수 있습니다. 데이터 세트 또는 복사 작업에 변경이 필요하지 않습니다.

### <a name="account-key-authentication"></a>계정 키 인증

Data Factory는 Azure Files 계정 키 인증에 대해 다음 속성을 지원합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성을 다음으로 설정해야 합니다. **AzureFileStorage**. | 예 |
| connectionString | Azure Files에 연결하는 데 필요한 정보를 지정합니다. <br/> Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| fileShare | 파일 공유를 지정합니다. | 예 |
| 스냅샷 | 스냅샷에서 복사하려면 [파일 공유 스냅샷](../storage/files/storage-snapshots-files.md)의 날짜를 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

**예:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net;",
            "fileShare": "<file share name>"
        },
        "connectVia": {
          "referenceName": "<name of Integration Runtime>",
          "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "fileShare": "<file share name>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>공유 액세스 서명 인증

공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 공유 액세스 서명을 사용하여 스토리지 계정의 개체에 대해 지정된 시간 동안 제한된 권한을 클라이언트에 부여할 수 있습니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-sas-overview.md)를 참조하세요.

Data Factory는 공유 액세스 서명 인증을 사용하기 위해 다음 속성을 지원합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성을 다음으로 설정해야 합니다. **AzureFileStorage**. | 예 |
| sasUri | 리소스에 대한 공유 액세스 서명 URI를 지정합니다. <br/>이 필드를 **SecureString** 으로 표시하여 Data Factory에 안전하게 저장합니다. SAS 토큰을 Azure Key Vault에 넣어 자동 회전을 사용하고 토큰 부분을 제거할 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. | 예 |
| fileShare | 파일 공유를 지정합니다. | 예 |
| 스냅샷 | 스냅샷에서 복사하려면 [파일 공유 스냅샷](../storage/files/storage-snapshots-files.md)의 날짜를 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

**예:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the resource e.g. https://<accountname>.file.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            },
            "fileShare": "<file share name>",
            "snapshot": "<snapshot version>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.file.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="legacy-model"></a>레거시 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성을 다음으로 설정해야 합니다. **AzureFileStorage**. | 예 |
| host | Azure Files 엔드포인트를 다음으로 지정합니다. <br/>-UI 사용: `\\<storage name>.file.core.windows.net\<file service name>` 지정<br/>- JSON 사용: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"` | 예 |
| userId | Azure Files에 다음으로 액세스할 사용자를 지정합니다. <br/>-UI 사용: `AZURE\<storage name>` 지정<br/>\- JSON 사용: `"userid": "AZURE\\<storage name>"` | 예 |
| password | 스토리지 액세스 키를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |원본에는 아니요이고 싱크에는 예입니다 |

**예:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 세트의 `location` 설정에서 Azure Files에 다음 속성이 지원됩니다.

| 속성   | 설명                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 세트의 `location` 아래의 type 속성은 **AzureFileStorageLocation** 으로 설정되어야 합니다. | 예      |
| folderPath | 폴더에 대한 경로입니다. 와일드카드를 사용하여 폴더를 필터링하려면 이 설정을 건너뛰고 작업 원본 설정에서 지정합니다. | 예       |
| fileName   | 지정된 folderPath 아래의 파일 이름입니다. 와일드카드를 사용하여 파일을 필터링하려면 이 설정을 건너뛰고 작업 원본 설정에서 지정합니다. | 예       |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureFileStorageLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Files 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-files-as-source"></a>원본 파일 형태의 Azure Files

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 원본의 `storeSettings` 설정에서 Azure Files에 다음 속성이 지원됩니다.

| 속성                 | 설명                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 아래의 type 속성은 **AzureFileStorageReadSettings** 로 설정해야 합니다. | 예                                           |
| ***복사할 파일 찾기:*** |  |  |
| 옵션 1: 정적 경로<br> | 데이터 세트에 지정된 폴더/파일 경로에서 복사합니다. 폴더의 모든 파일을 복사하려면 `wildcardFileName`을 `*`로 지정합니다. |  |
| 옵션 2: 파일 접두사<br>- 접두사 | 원본 파일을 필터링하기 위해 데이터 세트에 구성된 지정된 파일 공유 아래의 파일 이름에 대한 접두사입니다. 이름이 `fileshare_in_linked_service/this_prefix`로 시작하는 파일이 선택되었습니다. 와일드카드 필터보다 더 나은 성능을 제공하는 Azure Files에 대한 서비스측 필터를 사용합니다. 이 기능은 [기존 링크 서비스 모델](#legacy-model)을 사용하는 경우 지원되지 않습니다. | 예                                                          |
| 옵션 3: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링할 와일드카드 문자가 포함된 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                            |
| 옵션 3: 와일드카드<br>- wildcardFileName | 원본 파일을 필터링하기 위해 지정된 folderPath/wildcardFolderPath 아래의 와일드카드 문자가 포함된 파일 이름입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 파일 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예 |
| 옵션 4: 파일 목록<br>- fileListPath | 지정된 파일 집합을 복사하도록 지정합니다. 복사할 파일 목록이 포함된 텍스트 파일을 가리키며, 데이터 세트에 구성된 경로에 대한 상대 경로를 사용하여 한 줄에 하나의 파일을 가리킵니다.<br/>이 옵션을 사용하는 경우 데이터 세트에서 파일 이름을 지정하지 마세요. [파일 목록 예](#file-list-examples)에서 더 많은 예를 참조하세요. |예 |
| ***추가 설정:*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. <br>허용되는 값은 **true**(기본값) 및 **false** 입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. |예 |
| deleteFilesAfterCompletion | 대상 저장소로 이동한 후에 원본 저장소에서 이진 파일을 삭제할지를 나타냅니다. 파일 삭제는 파일 단위로 이루어지므로 복사 작업에 실패하면 일부 파일은 대상에 복사되고 원본에서 삭제되었지만, 다른 파일은 원본 저장소에 계속 남아 있는 것을 확인할 수 있습니다. <br/>이 속성은 이진 파일 복사 시나리오에서만 유효합니다. 기본값: false. |예 |
| modifiedDatetimeStart    | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 <br>마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br> 이 속성은 NULL일 수 있습니다. 즉, 데이터 세트에 파일 특성 필터가 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                            |
| enablePartitionDiscovery | 분할된 파일의 경우 파일 경로에서 파티션을 구문 분석할지를 지정하고 추가 원본 열로 추가합니다.<br/>허용되는 값은 **false**(기본값) 및 **true** 입니다. | 예                                            |
| partitionRootPath | 파티션 검색을 사용하는 경우 분할된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정합니다.<br/><br/>지정하지 않으면 기본적으로 다음과 같이 지정됩니다.<br/>- 데이터 세트의 파일 경로 또는 원본의 파일 목록을 사용하는 경우 파티션 루트 경로는 데이터 세트에서 구성된 경로입니다.<br/>- 와일드카드 폴더 필터를 사용하는 경우 파티션 루트 경로는 첫 번째 와일드카드 앞의 하위 경로입니다.<br/><br/>예를 들어 데이터 세트의 경로를 "root/folder/year=2020/month=08/day=27"로 구성한다고 가정합니다.<br/>- 파티션 루트 경로를 "root/folder/year=2020"으로 지정하면 복사 작업은 내부의 열 외에도 각각 값이 "08" 및 "27"인 `month` 및 `day` 열을 파일에 두 개 더 생성합니다.<br/>- 파티션 루트 경로를 지정하지 않으면 추가 열이 생성되지 않습니다. | 예                                            |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예                                            |

**예:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureFileStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-files-as-sink"></a>싱크로서의 Azure Files

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)]

형식 기반 복사 싱크의 `storeSettings` 설정에서 Azure Files에 다음 속성이 지원됩니다.

| 속성                 | 설명                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 아래의 type 속성은 **AzureFileStorageWriteSettings** 로 설정해야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예       |
| maxConcurrentConnections | 작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예       |

**예:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureFileStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| folderPath | fileName | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (비어 있음, 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (비어 있음, 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>파일 목록 예

이 섹션에서는 복사 작업 원본에서 파일 목록 경로를 사용하는 경우의 결과 동작을 설명합니다.

원본 폴더 구조가 다음과 같고 굵게 표시된 파일을 복사하려는 것으로 가정합니다.

| 샘플 원본 구조                                      | FileListToCopy.txt의 콘텐츠                             | ADF 구성                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 세트에서:**<br>- 폴더 경로: `root/FolderA`<br><br>**복사 작업 원본:**<br>- 파일 목록 경로: `root/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 복사하려는 파일 목록이 포함된 동일한 데이터 저장소의 텍스트 파일을 가리키며, 데이터 세트에 구성된 경로의 상대 경로를 사용하여 한 줄에 하나의 파일을 가리킵니다. |

### <a name="recursive-and-copybehavior-examples"></a>recursive 및 copyBehavior 예제

이 섹션에서는 다양한 recursive 및 copyBehavior 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요. 

## <a name="delete-activity-properties"></a>삭제 작업 속성

속성에 대한 자세한 내용을 보려면 [삭제 작업](delete-activity.md)을 확인하세요.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원됩니다. 앞의 섹션에서 설명한 새 모델을 사용하는 것이 좋습니다. 그러면 ADF 작성 UI가 새 모델을 생성하도록 전환됩니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 세트 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **FileShare** |예 |
| folderPath | 파일의 경로입니다. <br/><br/>와일드카드 필터가 지원되며, 허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br/><br/>예: rootfolder/subfolder/(더 많은 예제는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples) 참조) |예 |
| fileName | 지정된 "folderPath" 아래의 파일에 대한 **이름 또는 와일드 카드 필터** 입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터에 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`을 사용하여 실제 파일 이름 내에 와일드카드 또는 이 이스케이프 문자가 있는 경우 이스케이프합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않고 **preserveHierarchy** 가 작업 싱크에 지정되지 않으면, 복사 작업은 다음과 같은 패턴으로 파일 이름을 자동으로 생성합니다. "*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* ", 예: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; 쿼리 대신 테이블 이름을 사용하여 테이블 형식 원본에서 복사하면, 이름 패턴이 " *[table name].[format].[compression if configured]* "입니다(예: "MyTable.csv"). |예 |
| modifiedDatetimeStart | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 많은 양의 파일에서 파일을 필터링하려는 경우 이 설정을 사용하면 데이터 이동의 전반적인 성능에 영향을 줄 수 있습니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| modifiedDatetimeEnd | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 많은 양의 파일에서 파일을 필터링하려는 경우 이 설정을 사용하면 데이터 이동의 전반적인 성능에 영향을 줄 수 있습니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| format | 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 입니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다.<br/>지원되는 수준은 **최적** 및 **가장 빠름** 입니다. |예 |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath** 만을 지정합니다.<br>지정된 이름의 단일 파일을 복사하려면 폴더 부분으로 **folderPath** 및 파일 이름으로 **fileName** 을 지정합니다.<br>폴더 아래에서 파일의 하위 집합을 복사하려면 폴더 부분으로 **folderPath** 및 와일드카드 필터로 **fileName** 을 지정합니다.

>[!NOTE]
>파일 필터에 "fileFilter" 속성을 사용한 경우 그대로 계속 지원되지만, 이후 "fileName"에 추가된 새 필터 기능을 사용하도록 제안합니다.

**예:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>레거시 복사 작업 원본 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **FileSystemSource** |예 |
| recursive | 하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive가 true로 설정되고 싱크가 파일 기반 저장소인 경우 싱크에서 빈 폴더/하위 폴더가 복사/생성되지 않습니다.<br/>허용되는 값은 **true**(기본값), **false** 입니다. | 예 |
| maxConcurrentConnections | 작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>레거시 복사 작업 싱크 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성을 다음으로 설정해야 합니다. **FileSystemSink** |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일은 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다. | 예 |
| maxConcurrentConnections | 작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.