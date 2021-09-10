---
title: Azure Data Factory에서 복사 작업을 사용하여 메타데이터 및 ACL 유지
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory에서 복사 작업을 사용하여 복사하는 동안 메타데이터 및 ACL을 유지하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jianleishen
ms.openlocfilehash: b5bb9cc624f298ae4997b46a5cc7b4cf2a0d21ed
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250743"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Azure Data Factory에서 복사 작업을 사용하여 메타데이터 및 ACL 유지

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 복사 작업을 사용하여 원본에서 싱크로 데이터를 복사하는 경우 다음과 같은 시나리오에서 메타데이터 및 ACL을 함께 유지할 수도 있습니다.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> 레이크 마이그레이션에 대한 메타데이터 유지

[Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md)를 포함하여 한 데이터 레이크에서 다른 데이터 레이크로 데이터를 마이그레이션하는 경우 데이터와 함께 파일 메타데이터를 유지하도록 선택할 수 있습니다.

복사 작업은 데이터 복사 중 다음 특성을 유지하도록 지원합니다.

- **모든 고객 지정 메타데이터** 
- 및 다음 **5개 데이터 저장소 기본 제공 시스템 속성**: `contentType`, `contentLanguage`(Amazon S3는 제외), `contentEncoding`, `contentDisposition`, `cacheControl`.

**메타데이터 간 차이 처리:** Amazon S3 및 Azure Storage는 고객 지정 메타데이터의 키에 다른 문자 세트를 허용합니다. 복사 작업을 사용하여 메타데이터를 유지하도록 선택하면 ADF는 잘못된 문자를 자동으로 '_'로 바꿉니다.

이진 형식을 사용하여 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob storage/Azure Files에서 Azure Data Lake Storage Gen2/Azure Blob storage/Azure Files로 파일을 있는 그대로 복사하는 경우 작업 작성을 위한 **복사 작업** > **설정** 탭 또는 데이터 복사 도구의 **설정** 페이지에서 **유지** 옵션을 찾을 수 있습니다.

![복사 작업 메타데이터 유지](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

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

단일 폴더에서 복사하도록 지정하면 `recursive`가 true로 설정된 경우 Data Factory는 지정된 폴더 및 해당 폴더에 있는 파일/디렉터리에 대한 ACL을 복제합니다. 단일 파일에서 복사하도록 지정하면 해당 파일에 대한 ACL이 복사됩니다.

>[!NOTE]
>ADF를 사용하여 Data Lake Storage Gen1/Gen2에서 Gen2로 ACL을 유지하는 경우 싱크 Gen2의 해당 폴더/파일에 대한 기존 ACL을 덮어씁니다.

>[!IMPORTANT]
>ACL을 유지하도록 선택하는 경우 Data Factory가 싱크 Data Lake Storage Gen2 계정에 대해 작동할 수 있도록 충분한 권한을 부여해야 합니다. 예를 들어 계정 키 인증을 사용하거나 스토리지 Blob 데이터 소유자 역할을 서비스 주체 또는 관리 ID에 할당합니다.

이진 형식 또는 이진 복사 옵션을 사용하여 원본을 Data Lake Storage Gen1/Gen2로 구성하거나 이진 형식 또는 이진 복사 옵션을 사용하여 싱크를 Data Lake Storage Gen2로 구성하는 경우, 데이터 복사 도구의 **설정** 페이지 또는 작업 작성을 위한 **복사 작업** > **설정** 탭에서 **보존** 옵션을 찾을 수 있습니다.

![Data Lake Storage Gen1/Gen2에서 Gen2로 ACL 유지](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

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
