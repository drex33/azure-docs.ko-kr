---
title: 복사 작업을 사용 하 여 메타 데이터 및 Acl 유지
description: Azure Data Factory 및 Synapse Analytics 파이프라인에서 복사 작업을 사용 하는 경우 메타 데이터 및 Acl을 유지 하는 방법에 대해 알아봅니다.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 64608834c5d5b22383242f6739747d955e2feb3a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767378"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics에서 복사 작업을 사용 하 여 메타 데이터 및 Acl 유지

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 또는 Synapse Analytics 파이프라인 복사 작업을 사용 하 여 원본에서 싱크로 데이터를 복사 하는 경우 다음과 같은 시나리오에서 메타 데이터와 Acl을 함께 유지할 수도 있습니다.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> 레이크 마이그레이션에 대한 메타데이터 유지

[Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md)를 포함하여 한 데이터 레이크에서 다른 데이터 레이크로 데이터를 마이그레이션하는 경우 데이터와 함께 파일 메타데이터를 유지하도록 선택할 수 있습니다.

복사 작업은 데이터 복사 중 다음 특성을 유지하도록 지원합니다.

- **모든 고객 지정 메타데이터** 
- 및 다음 **5개 데이터 저장소 기본 제공 시스템 속성**: `contentType`, `contentLanguage`(Amazon S3는 제외), `contentEncoding`, `contentDisposition`, `cacheControl`.

**메타데이터 간 차이 처리:** Amazon S3 및 Azure Storage는 고객 지정 메타데이터의 키에 다른 문자 세트를 허용합니다. 복사 작업을 사용 하 여 메타 데이터를 유지 하도록 선택 하면 서비스에서 자동으로 잘못 된 문자를 ' _ '로 바꿉니다.

이진 형식을 사용하여 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob storage/Azure Files에서 Azure Data Lake Storage Gen2/Azure Blob storage/Azure Files로 파일을 있는 그대로 복사하는 경우 작업 작성을 위한 **복사 작업** > **설정** 탭 또는 데이터 복사 도구의 **설정** 페이지에서 **유지** 옵션을 찾을 수 있습니다.

:::image type="content" source="./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png" alt-text="복사 작업 메타데이터 유지":::

다음은 복사 작업 JSON 구성의 예입니다(`preserve` 참조). 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Data Lake Storage Gen1/Gen2에서 Gen2으로 ACL 유지

Azure Data Lake Storage Gen1/Gen2의 파일을 Gen2로 업그레이드하거나 ADLS Gen2 간에 파일을 복사할 때 데이터와 함께 POSIX ACL(액세스 제어 목록)을 유지하도록 선택할 수 있습니다. 액세스 제어에 대한 자세한 내용은 [Azure Data Lake Storage Gen1에서 액세스 제어](../data-lake-store/data-lake-store-access-control.md) 및 [Azure Data Lake Storage Gen2에서 액세스 제어](../storage/blobs/data-lake-storage-access-control.md)를 참조하세요.

복사 작업은 데이터 복사 도중 다음 유형의 ACL을 유지하도록 지원합니다. 하나 이상의 유형을 선택할 수 있습니다.

- **ACL**: 파일 및 디렉터리에 대한 POSIX 액세스 제어 목록을 복사하고 유지합니다. 원본에서 싱크로 기존 ACL 전체를 복사합니다. 
- **소유자**: 파일 및 디렉터리를 소유하는 사용자를 복사하고 유지합니다. 싱크 Data Lake Storage Gen2에 대한 슈퍼 사용자 액세스가 필요합니다.
- **그룹**: 파일 및 디렉터리를 소유하는 그룹을 복사하고 유지합니다. 싱크 Data Lake Storage Gen2 또는 소유 사용자(소유하는 사용자도 대상 그룹의 멤버인 경우)에 대한 슈퍼 사용자 액세스 권한이 필요합니다.

폴더에서 복사 하도록 지정 하는 경우 `recursive` 이 true로 설정 된 경우 서비스는 지정 된 폴더와 해당 폴더에 있는 파일 및 디렉터리에 대 한 acl을 복제 합니다. 단일 파일에서 복사하도록 지정하면 해당 파일에 대한 ACL이 복사됩니다.

>[!NOTE]
>복사 작업을 사용 하 여 Data Lake Storage Gen1/Gen2에서 Gen2로 acl을 유지 하는 경우 싱크에 있는 기존 acl Gen2's 해당 폴더/파일을 덮어씁니다.

>[!IMPORTANT]
>acl 유지를 선택 하는 경우 서비스에서 싱크 Data Lake Storage Gen2 계정에 대해 작동 하는 데 충분 한 권한을 부여 해야 합니다. 예를 들어 계정 키 인증을 사용하거나 스토리지 Blob 데이터 소유자 역할을 서비스 주체 또는 관리 ID에 할당합니다.

이진 형식 또는 이진 복사 옵션을 사용하여 원본을 Data Lake Storage Gen1/Gen2로 구성하거나 이진 형식 또는 이진 복사 옵션을 사용하여 싱크를 Data Lake Storage Gen2로 구성하는 경우, 데이터 복사 도구의 **설정** 페이지 또는 작업 작성을 위한 **복사 작업** > **설정** 탭에서 **보존** 옵션을 찾을 수 있습니다.

:::image type="content" source="./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png" alt-text="Data Lake Storage Gen1/Gen2에서 Gen2로 ACL 유지":::

다음은 복사 작업 JSON 구성의 예입니다(`preserve` 참조). 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>다음 단계

다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)
