---
title: Azure Blob Storage에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 Blob Storage 간에 데이터를 복사하고 Blob Storage에서 데이터를 변환하는 방법에 대해 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 9de59b4510642ab70540c4217ef074347a34ac89
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764187"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 Azure Blob Storage에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> - [버전 1](v1/data-factory-azure-blob-connector.md)
> - [현재 버전](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse 파이프라인의 복사 작업을 사용하여 Azure Blob Storage 간에 데이터를 복사하는 방법을 간략하게 설명합니다. 또한 데이터 흐름 작업을 사용하여 Azure Blob Storage에서 데이터를 변환하는 방법을 설명합니다. 자세한 내용은 [Azure Data Factory](introduction.md) 및 [Azure Synapse Analytics](..\synapse-analytics\overview-what-is.md) 소개 문서를 참조하세요.

>[!TIP]
>데이터 레이크 또는 데이터 웨어하우스의 마이그레이션 시나리오에 대한 자세한 내용은 [데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터 마이그레이션](data-migration-guidance-overview.md) 문서를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Blob Storage 커넥터는 다음 작업에 대해 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)

복사 작업의 경우 이 Blob Storage 커넥터는 다음을 지원합니다.

- 범용 Azure 스토리지 계정 및 핫/쿨 Blob Storage 간에 Blob을 복사합니다.
- 계정 키, SAS(서비스 공유 액세스 서명), 서비스 주체 또는 Azure 리소스 인증을 위한 관리 ID를 사용하여 Blob을 복사합니다.
- 블록, 추가 또는 페이지 Blob에서 Blob을 복사하고, 블록 Blob에만 데이터를 복사합니다.
- Blob을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 Blob을 구문 분석하거나 생성합니다.
- [복사 중 파일 메타데이터 보존](#preserving-metadata-during-copy).

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-blob-storage-linked-service-using-ui"></a>UI를 사용하여 Azure Blob Storage 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Blob Storage 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Blob을 검색하고 Azure Blob Storage 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-blob-storage/azure-blob-storage-connector.png" alt-text="Azure Blob Storage 커넥터 선택.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-blob-storage/configure-azure-blob-storage-linked-service.png" alt-text="Azure Blob Storage 연결된 서비스의 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Blob Storage에 특정한 Data Factory 및 Synapse 파이프라인 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

이 Blob Storage 커넥터는 다음 인증 유형을 지원합니다. 자세한 내용은 해당 섹션을 참조하세요.

- [계정 키 인증](#account-key-authentication)
- [공유 액세스 서명 인증](#shared-access-signature-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [시스템이 할당한 관리 ID 인증](#managed-identity)
- [사용자 할당 관리 ID 인증](#user-assigned-managed-identity-authentication)

>[!NOTE]
>- 공개 Azure 통합 런타임을 사용하여 Azure Storage 방화벽에서 사용하도록 설정된 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 옵션을 활용하여 Blob Storage에 연결하려면 [관리 ID 인증](#managed-identity)을 사용해야 합니다.
>- PolyBase 또는 COPY 문을 사용하여 Azure Synapse Analytics에 데이터를 로드할 때 원본 또는 준비 Blob Storage가 Azure Virtual Network 엔드포인트로 구성된 경우 Azure Synapse에서 요구하는 대로 관리 ID 인증을 사용해야 합니다. 추가 구성 필수 조건은 [관리 ID 인증](#managed-identity) 섹션을 참조하세요.

>[!NOTE]
>Azure HDInsight 및 Azure Machine Learning 작업은 Azure Blob Storage 계정 키를 사용하는 인증만 지원합니다.

### <a name="account-key-authentication"></a>계정 키 인증

Azure Data Factory 또는 Synapse 파이프라인의 스토리지 계정 키 인증에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | `type` 속성은 `AzureBlobStorage`(권장) 또는 `AzureStorage`로 설정되어야 합니다(다음 참고 참조). | 예 |
| connectionString | `connectionString` 속성에 대한 Storage에 연결하는 데 필요한 정보를 지정합니다. <br/> Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임(데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 해당 속성을 지정하지 않으면 서비스는 기본 Azure 통합 런타임을 사용합니다. | 예 |

>[!NOTE]
>계정 키 인증을 사용하는 경우 보조 Blob 서비스 엔드포인트가 지원되지 않습니다. 다른 인증 유형을 사용할 수 있습니다.

>[!NOTE]
>`AzureStorage` 유형의 연결된 서비스를 사용하는 경우 그대로 지원됩니다. 그러나 앞으로는 새로운 `AzureBlobStorage`에 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
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

공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 공유 액세스 서명을 사용하여 스토리지 계정의 개체에 대해 지정된 시간 동안 제한된 권한을 클라이언트에 부여할 수 있습니다. 

계정 액세스 키를 공유할 필요가 없습니다. 공유 액세스 서명은 스토리지 리소스에 대해 인증된 액세스에 필요한 모든 정보를 쿼리 매개 변수에 포함하는 URI입니다. 공유 액세스 서명을 사용하여 스토리지 리소스에 액세스하려면 클라이언트에서 공유 액세스 서명을 해당 생성자 또는 메서드에 전달하기만 하면 됩니다.

공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-sas-overview.md)를 참조하세요.

> [!NOTE]
>- 이 서비스는 이제 *서비스 공유 액세스 서명* 과 *계정 공유 액세스 서명* 이 모두 지원됩니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명을 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../storage/common/storage-sas-overview.md)를 참조하세요.
>- 이후 데이터 세트 구성에서 폴더 경로는 컨테이너 수준에서 시작하는 절대 경로입니다. SAS URI의 경로와 일치하는 경로를 구성해야 합니다.

공유 액세스 서명 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | `type` 속성은 `AzureBlobStorage`(권장) 또는 `AzureStorage`로 설정되어야 합니다(다음 참고 참조). | 예 |
| sasUri | 스토리지 리소스(예: Blob 또는 컨테이너)에 대한 공유 액세스 서명 URI를 지정합니다. <br/>이 필드를 `SecureString`으로 표시하여 안전하게 저장합니다. SAS 토큰을 Azure Key Vault에 넣어 자동 회전을 사용하고 토큰 부분을 제거할 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임(데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 해당 속성을 지정하지 않으면 서비스는 기본 Azure 통합 런타임을 사용합니다. | 예 |

>[!NOTE]
>`AzureStorage` 유형의 연결된 서비스를 사용하는 경우 그대로 지원됩니다. 그러나 앞으로는 새로운 `AzureBlobStorage`에 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
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
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
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

공유 액세스 서명 URI를 만들 때 고려해야 할 사항은 다음과 같습니다.

- 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방법에 따라 개체에 대한 적절한 읽기/쓰기 권한을 설정합니다.
- **만료 시간** 을 적절하게 설정합니다. Storage 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않도록 합니다.
- URI는 필요에 따라 적절한 컨테이너 또는 Blob에 만들어야 합니다. Blob에 대한 공유 액세스 서명 URI를 사용하면 데이터 팩터리 또는 Synapse 파이프라인에서 해당 특정 Blob에 액세스할 수 있습니다. Blob 스토리지 컨테이너에 대한 공유 액세스 서명 URI를 사용하면 데이터 팩터리 또는 Synapse 파이프라인에서 해당 컨테이너의 Blob을 반복할 수 있습니다. 나중에 더 많거나 적은 개체에 대한 액세스를 제공하거나 공유 액세스 서명 URI를 업데이트하려면 연결된 서비스를 새 URI로 업데이트해야 합니다.

### <a name="service-principal-authentication"></a>서비스 주체 인증

Azure Storage 서비스 주체 인증에 대한 일반적인 정보는 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/blobs/authorize-access-azure-active-directory.md)을 참조하세요.

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure Blob Storage에서 서비스 주체에게 적절한 권한을 부여합니다. 역할에 대한 자세한 내용은 [Azure Portal을 사용하여 Blob 및 큐 데이터에 대한 액세스를 위한 Azure 역할 할당](../storage/blobs/assign-azure-role-data-access.md)을 참조하세요.

    - **원본으로** **액세스 제어(IAM)** 에서 최소한 **Storage Blob 데이터 읽기 권한자** 역할을 부여합니다.
    - **싱크의 경우** **액세스 제어(IAM)** 에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **AzureBlobStorage** 로 설정해야 합니다. | 예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. | 예 |
| accountKind | 스토리지 계정의 종류를 지정합니다. 허용되는 값은 **스토리지**(범용 v1), **스토리지V2**(범용 v2), **BlobStorage** 또는 **BlockBlobStorage** 입니다. <br/><br/>데이터 흐름에서 Azure Blob에 연결된 서비스를 사용하는 경우 계정 종류가 비어 있거나 "스토리지"인 경우 관리 ID 또는 서비스 주체 인증이 지원되지 않습니다. 적절한 계정 종류를 지정하거나 다른 인증을 선택하거나 스토리지 계정을 범용 v2로 업그레이드합니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 팩터리에 저장하거나 [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| azureCloudType | 서비스 주체 인증의 경우 Azure Active Directory 애플리케이션이 등록된 Azure 클라우드 환경의 유형을 지정합니다. <br/> 허용되는 값은 **AzurePublic**, **AzureChina**, **AzureUsGovernment**, **AzureGermany** 입니다. 기본적으로 데이터 팩터리 또는 Synapse 파이프라인의 클라우드 환경이 사용됩니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임(데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 해당 속성을 지정하지 않으면 서비스는 기본 Azure 통합 런타임을 사용합니다. | 예 |

>[!NOTE]
>
>- Blob 계정이 [일시 삭제](../storage/blobs/soft-delete-blob-overview.md)를 사용하는 경우 데이터 흐름에서 서비스 주체 인증이 지원되지 않습니다.
>- 데이터 흐름을 사용하여 프라이빗 엔드포인트를 통해 Blob Storage에 액세스하는 경우 서비스 주체 인증이 사용되면 데이터 흐름이 Blob 엔드포인트 대신 ADLS Gen2 엔드포인트에 연결됩니다. 액세스를 사용하도록 설정하려면 데이터 팩터리 또는 Synapse 작업 영역에서 해당 프라이빗 엔드포인트를 만들어야 합니다.

>[!NOTE]
>서비스 주체 인증은 "AzureBlobStorage" 유형 연결된 서비스에서만 지원되고, 이전의 "AzureStorage" 유형 연결된 서비스에서는 지원되지 않습니다.

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a>시스템 할당 관리 ID 인증

데이터 팩터리 또는 Synapse 파이프라인은 다른 Azure 서비스에 대한 인증을 위해 해당 리소스를 나타내는 [Azure 리소스용 시스템 할당 관리 ID](data-factory-service-identity.md#system-assigned-managed-identity)와 연결될 수 있습니다. 사용자 고유의 서비스 주체를 사용하는 것과 비슷하게 Blob Storage 인증에 이 시스템 할당 관리 ID를 직접 사용할 수 있습니다. 그러면 이 지정된 리소스가 Blob Storage 간에 데이터를 액세스하고 복사할 수 있습니다. Azure 리소스에 대한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

Azure Storage 인증에 대한 일반적인 정보는 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/blobs/authorize-access-azure-active-directory.md)을 참조하세요. Azure 리소스 인증에 관리 ID를 사용하려면 다음 단계를 수행합니다.

1. 팩터리 또는 Synapse 작업 영역과 함께 생성된 시스템 할당 관리 ID 개체 ID 값을 복사하여 [시스템 할당 관리 ID 정보를 검색](data-factory-service-identity.md#retrieve-managed-identity)합니다.

2. Azure Blob Storage에서 사용 권한을 관리 ID에 부여합니다. 역할에 대한 자세한 내용은 [Azure Portal을 사용하여 Blob 및 큐 데이터에 대한 액세스를 위한 Azure 역할 할당](../storage/blobs/assign-azure-role-data-access.md)을 참조하세요.

    - **원본으로** **액세스 제어(IAM)** 에서 최소한 **Storage Blob 데이터 읽기 권한자** 역할을 부여합니다.
    - **싱크의 경우** **액세스 제어(IAM)** 에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **AzureBlobStorage** 로 설정해야 합니다. | 예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. | 예 |
| accountKind | 스토리지 계정의 종류를 지정합니다. 허용되는 값은 **스토리지**(범용 v1), **스토리지V2**(범용 v2), **BlobStorage** 또는 **BlockBlobStorage** 입니다. <br/><br/>데이터 흐름에서 Azure Blob에 연결된 서비스를 사용하는 경우 계정 종류가 비어 있거나 "스토리지"인 경우 관리 ID 또는 서비스 주체 인증이 지원되지 않습니다. 적절한 계정 종류를 지정하거나 다른 인증을 선택하거나 스토리지 계정을 범용 v2로 업그레이드합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임(데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 해당 속성을 지정하지 않으면 서비스는 기본 Azure 통합 런타임을 사용합니다. | 예 |

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>사용자 할당 관리 ID 인증
데이터 팩터리는 하나 이상의 [사용자가 할당한 관리 ID](data-factory-service-identity.md#user-assigned-managed-identity)로 할당할 수 있습니다. Blob Storage 인증 시 이 사용자 할당 관리 ID를 사용할 수 있습니다. 이 ID를 사용하면 데이터에 액세스하고 Blob Storage 간에 데이터를 복사할 수 있습니다. Azure 리소스에 대한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

Azure Storage 인증에 대한 일반적인 정보는 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/blobs/authorize-access-azure-active-directory.md)을 참조하세요. 사용자가 할당한 관리 ID 인증을 사용하려면 다음 단계를 수행합니다.

1. [사용자 할당 관리 ID를 하나 이상 만들고](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) Azure Blob Storage에 액세스 권한을 부여합니다. 역할에 대한 자세한 내용은 [Azure Portal을 사용하여 Blob 및 큐 데이터에 대한 액세스를 위한 Azure 역할 할당](../storage/blobs/assign-azure-role-data-access.md)을 참조하세요.

    - **원본으로** **액세스 제어(IAM)** 에서 최소한 **Storage Blob 데이터 읽기 권한자** 역할을 부여합니다.
    - **싱크의 경우** **액세스 제어(IAM)** 에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.
     
2. 하나 이상의 사용자가 할당한 관리 ID를 데이터 팩터리에 할당하고 각 사용자가 할당한 관리 ID에 대한 [자격 증명을 만듭니다](data-factory-service-identity.md#credentials). 


Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **AzureBlobStorage** 로 설정해야 합니다. | 예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. | 예 |
| accountKind | 스토리지 계정의 종류를 지정합니다. 허용되는 값은 **스토리지**(범용 v1), **스토리지V2**(범용 v2), **BlobStorage** 또는 **BlockBlobStorage** 입니다. <br/><br/>데이터 흐름에서 Azure Blob에 연결된 서비스를 사용하는 경우 계정 종류가 비어 있거나 "스토리지"인 경우 관리 ID 또는 서비스 주체 인증이 지원되지 않습니다. 적절한 계정 종류를 지정하거나 다른 인증을 선택하거나 스토리지 계정을 범용 v2로 업그레이드합니다. | 예 |
| 자격 증명 | 사용자가 할당한 관리 ID를 자격 증명 개체로 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임(데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 해당 속성을 지정하지 않으면 서비스는 기본 Azure 통합 런타임을 사용합니다. | 예 |

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

>[!IMPORTANT]
>PolyBase 또는 COPY 문을 사용하여 Blob Storage(원본 또는 스테이징)에서 Azure Synapse Analytics로 데이터를 로드하는 경우 Blob Storage에 대한 관리 ID 인증을 사용할 때 [이 안내](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)의 1~3단계를 따르세요. 이러한 단계는 Azure AD에 서버를 등록하고 Storage Blob Data 기여자 역할을 서버에 할당합니다. 나머지는 Data Factory에서 처리합니다. Azure Virtual Network 엔드포인트로 Blob Storage를 구성하는 경우 Azure Storage 계정 **방화벽 및 가상 네트워크** 설정 메뉴에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 Azure Synapse에서 요구하는 대로 액세스하도록 허용** 을 설정해야 합니다.

> [!NOTE]
>
> - Blob 계정이 [일시 삭제](../storage/blobs/soft-delete-blob-overview.md)를 사용하도록 설정하는 경우 시스템 할당/사용자 할당 관리 ID 인증이 데이터 흐름에서 지원되지 않습니다.
> - 데이터 흐름을 사용하여 프라이빗 엔드포인트를 통해 Blob Storage에 액세스하는 경우 시스템 할당/사용자 할당 관리 ID 인증이 사용되면 데이터 흐름이 Blob 엔드포인트 대신 ADLS Gen2 엔드포인트에 연결됩니다. 액세스를 사용하도록 설정하려면 ADF에서 해당 프라이빗 엔드포인트를 만들어야 합니다.

> [!NOTE]
> 시스템 할당/사용자 할당 관리 ID 인증은 이전 "AzureStorage" 유형의 연결된 서비스가 아닌 "AzureBlobStorage" 유형의 연결된 서비스에서만 지원됩니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 세트의 `location` 설정에서 Azure Blob Storage에 다음 속성이 지원됩니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 세트에서 위치의 **type** 속성은 **AzureBlobStorageLocation** 으로 설정해야 합니다. | 예      |
| container  | Blob 컨테이너입니다.                                          | 예      |
| folderPath | 지정된 컨테이너 아래에 있는 폴더의 경로입니다. 와일드카드를 사용하여 폴더를 필터링하려면 이 설정을 건너뛰고 작업 원본 설정에서 지정합니다. | 예       |
| fileName   | 지정된 컨테이너 및 폴더 경로 아래의 파일 이름입니다. 와일드카드를 사용하여 파일을 필터링하려면 이 설정을 건너뛰고 작업 원본 설정에서 지정합니다. | 예       |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Blob Storage 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="blob-storage-as-a-source-type"></a>Blob Storage를 원본 형식으로

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 원본의 `storeSettings` 설정의 Azure Blob Storage에 다음 속성이 지원됩니다.

| 속성                 | Description                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 아래의 **type** 속성은 **AzureBlobStorageReadSettings** 로 설정해야 합니다. | 예                                           |
| ***복사할 파일 찾기:*** |  |  |
| 옵션 1: 정적 경로<br> | 제공된 컨테이너 또는 데이터 세트에 지정된 폴더/파일 경로에서 복사합니다. 컨테이너 또는 폴더의 모든 Blob을 복사하려면 `wildcardFileName`을 `*`로 지정합니다. |  |
| 옵션 2: Blob 접두사<br>- prefix | 원본 Blob 파일을 필터링하기 위해 데이터 세트에 구성된 제공한 컨테이너의 Blob 이름에 대한 접두사입니다. 이름이 `container_in_dataset/this_prefix`로 시작하는 Blob이 선택됩니다. 와일드카드 필터보다 더 나은 성능을 제공하는 Blob Storage에 대한 서비스 쪽 필터를 사용합니다.<br><br>접두사를 사용하고 계층 구조를 유지하는 파일 기반 싱크에 복사하도록 선택하면 접두사의 마지막 "/" 뒤의 하위 경로가 유지됩니다. 예를 들어 원본`container/folder/subfolder/file.txt`이 있고 접두사를 `folder/sub`로 구성하면 유지되는 파일 경로는 `subfolder/file.txt`입니다. | 예                                                          |
| 옵션 3: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링하도록 데이터 세트에 구성된, 지정된 컨테이너 아래에 와일드카드 문자가 포함된 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                            |
| 옵션 3: 와일드카드<br>- wildcardFileName | 원본 파일을 필터링하기 위해 지정된 컨테이너 및 폴더 경로(또는 와일드카드 폴더 경로) 아래에 와일드카드 문자가 있는 파일 이름입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. 파일 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`를 사용하여 이스케이프합니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예 |
| 옵션 4: 파일 목록<br>- fileListPath | 지정된 파일 집합을 복사하도록 지정합니다. 복사할 파일 목록이 포함된 텍스트 파일을 가리키며, 데이터 세트에 구성된 경로에 대한 상대 경로를 사용하여 한 줄에 하나의 파일을 가리킵니다.<br/>이 옵션을 사용하는 경우 데이터 세트에 파일 이름을 지정하지 마세요. [파일 목록 예](#file-list-examples)에서 더 많은 예를 참조하세요. | 예 |
| ***추가 설정:*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. **recursive** 를 **true** 로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 생성되지 않습니다. <br>허용되는 값은 **true**(기본값) 및 **false** 입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예 |
| deleteFilesAfterCompletion | 대상 저장소로 이동한 후에 원본 저장소에서 이진 파일을 삭제할지를 나타냅니다. 파일 삭제는 파일 단위로 이루어지므로 복사 작업에 실패하면 일부 파일은 대상에 복사되고 원본에서 삭제되었지만, 다른 파일은 원본 저장소에 계속 남아 있는 것을 확인할 수 있습니다. <br/>이 속성은 이진 파일 복사 시나리오에서만 유효합니다. 기본값: false. | 예 |
| modifiedDatetimeStart    | 파일은 마지막으로 수정된 특성을 기준으로 필터링됩니다. <br>마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 “2018-12-01T05:00:00Z” 형식의 UTC 표준 시간대에 적용됩니다. <br> 속성은 **NULL** 일 수 있습니다. 즉, 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 날짜/시간 값이 있지만 `modifiedDatetimeEnd`가 **NULL** 이면, 마지막으로 수정된 특성이 날짜/시간 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 날짜/시간 값이 있지만 `modifiedDatetimeStart`가 **NULL** 이면, 마지막으로 수정된 특성이 날짜/시간 값보다 작은 파일이 선택됩니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                            |
| enablePartitionDiscovery | 분할된 파일의 경우 파일 경로에서 파티션을 구문 분석할지를 지정하고 추가 원본 열로 추가합니다.<br/>허용되는 값은 **false**(기본값) 및 **true** 입니다. | 예                                            |
| partitionRootPath | 파티션 검색을 사용하는 경우 분할된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정합니다.<br/><br/>지정하지 않으면 기본적으로 다음과 같이 지정됩니다.<br/>- 데이터 세트의 파일 경로 또는 원본의 파일 목록을 사용하는 경우 파티션 루트 경로는 데이터 세트에서 구성된 경로입니다.<br/>- 와일드카드 폴더 필터를 사용하는 경우 파티션 루트 경로는 첫 번째 와일드카드 앞의 하위 경로입니다.<br/>- 접두사를 사용하는 경우 파티션 루트 경로는 마지막 “/” 앞의 하위 경로입니다. <br/><br/>예를 들어 데이터 세트의 경로를 "root/folder/year=2020/month=08/day=27"로 구성한다고 가정합니다.<br/>- 파티션 루트 경로를 "root/folder/year=2020"으로 지정하면 복사 작업은 내부의 열 외에도 각각 값이 "08" 및 "27"인 `month` 및 `day` 열을 파일에 두 개 더 생성합니다.<br/>- 파티션 루트 경로를 지정하지 않으면 추가 열이 생성되지 않습니다. | 예                                            |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예                                            |

> [!NOTE]
> Parquet/구분된 텍스트 형식의 경우 다음 섹션에서 언급한 복사 작업 원본의 **BlobSource** 유형은 이전 버전과의 호환성을 위해 계속 지원됩니다. 작성 UI가 이러한 새 유형을 생성하도록 전환될 때까지 새 모델을 사용하는 것이 좋습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

> [!NOTE]
> 스토리지 계정에 대해 스토리지 분석이 사용하도록 설정되면 자동으로 생성되는 `$logs` 컨테이너는 UI를 통해 컨테이너 나열 작업을 수행할 때 표시되지 않습니다. 데이터 팩터리 또는 Synapse 파이프라인이 `$logs` 컨테이너의 파일을 사용하려면 파일 경로를 직접 제공해야 합니다.

### <a name="blob-storage-as-a-sink-type"></a>Blob Storage를 싱크 형식으로

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)] 

형식 기반 복사 싱크의 `storeSettings` 설정의 Azure Blob Storage에 다음 속성이 지원됩니다.

| 속성                 | 설명                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 아래의 `type` 속성은 `AzureBlobStorageWriteSettings`로 설정되어야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예       |
| blockSizeInMB | 블록 Blob에 데이터를 쓰는 데 사용되는 블록 크기(메가바이트)를 지정합니다. [블록 Blob에 대해](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 자세히 알아보세요. <br/>허용되는 값은 *4~100MB* 입니다. <br/>기본적으로 이 서비스는 원본 저장소 유형과 데이터에 따라 블록 크기를 자동으로 결정합니다. Blob Storage로 이진이 아닌 파일을 복사하는 경우 기본 블록 크기는 100MB이므로 최대 4.95TB의 데이터에 적합할 수 있습니다. 데이터가 크지 않으면 최적이 아닐 수 있습니다. 특히 네트워크 연결이 좋지 않은 상태에서 자체 호스팅 통합 런타임을 사용하여 작업 시간 초과 또는 성능 문제가 발생하는 경우에 그렇습니다. `blockSizeInMB*50000`이 데이터를 저장할 수 있을 만큼 충분히 큰지 확인하면서 블록 크기를 명시적으로 지정할 수 있습니다. 그러지 않으면 복사 작업 실행이 실패합니다. | 예 |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예       |
| metadata |싱크로 복사할 때 사용자 지정 메타데이터를 설정합니다. `metadata` 배열의 각 개체는 추가 열을 나타냅니다. `name`은 메타데이터 키 이름을 정의하고 `value`는 해당 키의 데이터 값을 나타냅니다. [특성 유지 기능](./copy-activity-preserve-metadata.md#preserve-metadata)을 사용하는 경우 지정된 메타데이터는 원본 파일 메타데이터와 통합/덮어씁니다.<br/><br/>허용되는 데이터 값은 다음과 같습니다.<br/>- `$$LASTMODIFIED`: 예약된 변수는 원본 파일의 마지막으로 수정된 시간을 저장함을 나타냅니다. 이진 형식만 사용하는 파일 기반 원본에 적용합니다.<br/><b>- 식<b><br/>- <b>정적 값<b>| 예       |

**예:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy",
                    "metadata": [
                        {
                            "name": "testKey1",
                            "value": "value1"
                        },
                        {
                            "name": "testKey2",
                            "value": "value2"
                        },
                        {
                            "name": "lastModifiedKey",
                            "value": "$$LASTMODIFIED"
                        }
                    ]
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
| `container/Folder*` | (비어 있음, 기본값 사용) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (비어 있음, 기본값 사용) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>파일 목록 예

이 섹션에서는 복사 작업 원본에서 파일 목록 경로를 사용하는 결과 동작에 대해 설명합니다.

다음 원본 폴더 구조가 있고 굵게 표시된 파일을 복사하려고 한다고 가정합니다.

| 샘플 원본 구조                                      | FileListToCopy.txt의 콘텐츠                             | Configuration 
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 세트:**<br>- 컨테이너: `container`<br>- 폴더 경로: `FolderA`<br><br>**복사 작업 원본:**<br>- 파일 목록 경로: `container/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 복사하려는 파일 목록이 포함된 동일한 데이터 저장소의 텍스트 파일을 가리키며, 데이터 세트에 구성된 경로의 상대 경로를 사용하여 한 줄에 하나의 파일을 가리킵니다. |

### <a name="some-recursive-and-copybehavior-examples"></a>일부 recursive 및 copyBehavior 예제

이 섹션에서는 다양한 **recursive** 및 **copyBehavior** 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |

## <a name="preserving-metadata-during-copy"></a>복사 중 메타데이터 보존

mazon S3, Azure Blob Storage 또는 Azure Data Lake Storage Gen2의 파일을 Azure Data Lake Storage Gen2 또는 Azure Blob Storage에 복사할 때 데이터와 함께 파일 메타데이터를 유지하도록 선택할 수 있습니다. [메타데이터 유지](copy-activity-preserve-metadata.md#preserve-metadata)에서 자세히 알아보세요.

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서 데이터를 변환할 때 Azure Blob Storage에서 다음 형식으로 파일을 읽고 쓸 수 있습니다.

- [Avro](format-avro.md#mapping-data-flow-properties)
- [구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)
- [델타](format-delta.md#mapping-data-flow-properties)
- [Excel](format-excel.md#mapping-data-flow-properties)
- [JSON](format-json.md#mapping-data-flow-properties)
- [Parquet](format-parquet.md#mapping-data-flow-properties)

형식별 설정은 해당 형식에 대한 설명서에 있습니다. 자세한 내용은 [데이터 흐름 매핑의 원본 변환](data-flow-source.md) 및 [데이터 흐름 매핑의 싱크 변환](data-flow-sink.md)을 참조하세요.

### <a name="source-transformation"></a>원본 변환

원본 변환 시 Azure Blob Storage의 컨테이너, 폴더 또는 개별 파일에서 읽을 수 있습니다. **원본 옵션** 탭을 사용하여 파일을 읽는 방법을 관리합니다. 

:::image type="content" source="media/data-flow/sourceOptions1.png" alt-text="원본 옵션":::

**와일드카드 경로:** 와일드카드 패턴을 사용하면 서비스가 일치하는 각 폴더와 파일을 단일 원본 변환에서 반복하도록 지시합니다. 이는 단일 흐름 내에서 여러 파일을 처리하는 효과적인 방법입니다. 기존 와일드카드 패턴을 마우스로 가리키면 표시되는 + 기호를 사용하여 와일드카드 일치 패턴을 여러 개 추가합니다.

원본 컨테이너에서 패턴과 일치하는 일련의 파일을 선택합니다. 데이터 세트에는 컨테이너만 지정할 수 있습니다. 따라서 와일드카드 경로에는 루트 폴더의 폴더 경로도 포함되어야 합니다.

와일드카드 예제:

- `*` 모든 문자 집합 나타내기
- `**` 재귀 디렉터리 중첩 나타내기
- `?` 한 문자 바꾸기
- `[]` 괄호 안에 있는 하나 이상의 문자와 일치시키기

- `/data/sales/**/*.csv` /data/sales에서 .csv 파일 모두 가져오기
- `/data/sales/20??/**/` 20세기의 파일 모두 가져오기
- `/data/sales/*/*/*.csv` /data/sales에서 .csv 파일을 두 가지 수준으로 가져오기
- `/data/sales/2004/*/12/[XY]1?.csv` X 또는 Y 접두사가 두 자리 숫자로 시작하는 2004년 12월의 .csv 파일 모두 가져오기

**파티션 루트 경로:** 파일 원본에 `key=value` 형식으로 분할된 폴더(예: `year=2019`)가 있는 경우 해당 파티션 폴더 트리의 최상위 수준을 데이터 흐름 데이터 스트림의 열 이름에 할당할 수 있습니다.

먼저 분할된 폴더와 읽을 리프 파일인 모든 경로를 포함하도록 와일드카드를 설정합니다.

:::image type="content" source="media/data-flow/partfile2.png" alt-text="파티션 원본 파일 설정":::

**파티션 루트 경로** 설정을 사용하여 폴더 구조의 최상위 수준을 정의합니다. 데이터 미리 보기를 통해 데이터 콘텐츠를 보면 서비스가 각 폴더 수준에서 찾은 해결된 파티션을 추가함을 확인할 수 있습니다.

:::image type="content" source="media/data-flow/partfile1.png" alt-text="파티션 루트 경로":::

**파일 목록:** 파일 집합입니다. 처리할 상대 경로 파일 목록이 포함된 텍스트 파일을 만듭니다. 이 텍스트 파일을 가리킵니다.

**파일 이름을 저장할 열:** 원본 파일의 이름을 데이터 열에 저장합니다. 파일 이름 문자열을 저장하려면 여기에 새 열 이름을 입력합니다.

**완료 후 기간(일):** 데이터 흐름이 실행된 후 원본 파일을 사용해 아무 작업도 수행하지 않도록 선택하거나, 원본 파일을 삭제 또는 이동합니다. 이동 경로는 상대 경로입니다.

원본 파일을 다른 위치에 후처리로 이동하려면 먼저 파일 작업에서 "이동"을 선택합니다. 그런 다음, "원본" 디렉터리를 설정합니다. 경로에 와일드카드를 사용하지 않을 경우 "원본" 설정은 원본 폴더와 같은 폴더에 있습니다.

와일드카드가 있는 원본 경로를 사용하는 경우 구문은 다음과 같습니다.

`/data/sales/20??/**/*.csv`

"원본"은 다음과 같이 지정할 수 있습니다.

`/data/sales`

그리고 "to"를 다음으로 지정할 수 있습니다.

`/backup/priorSales`

이 경우 `/data/sales`에서 제공되는 모든 파일이 `/backup/priorSales`로 이동됩니다.

> [!NOTE]
> 파이프라인에서 Data Flow 실행 작업을 사용하는 파이프라인 실행(파이프라인 디버그 또는 실행)에서 데이터 흐름을 시작하는 경우에만 파일 작업이 실행됩니다. Data Flow 디버그 모드에서는 파일 작업이 실행되지 *않습니다.*

**마지막으로 수정한 사람 필터링:** 마지막으로 수정된 날짜 범위를 지정하여 처리하는 파일을 필터링할 수 있습니다. 모든 날짜/시간은 UTC 기준입니다. 

### <a name="sink-properties"></a>싱크 속성

싱크 변환에서 Azure Blob Storage의 컨테이너 또는 폴더에 쓸 수 있습니다. **설정** 탭을 사용하여 파일을 작성하는 방법을 관리할 수 있습니다.

:::image type="content" source="media/data-flow/file-sink-settings.png" alt-text="싱크 옵션":::

**폴더 선택 취소:** 데이터를 쓰기 전에 대상 폴더를 지울 것인지 여부를 결정합니다.

**파일 이름 옵션:** 대상 폴더에서 대상 파일의 이름을 지정하는 방법을 결정합니다. 파일 이름 옵션은 다음과 같습니다.
   - **기본값**: Spark에서 PART 기본값에 따라 파일 이름을 지정하도록 허용합니다.
   - **패턴**: 파티션별로 출력 파일을 열거하는 패턴을 입력합니다. 예를 들어 `loans[n].csv`는 `loans1.csv`, `loans2.csv` 등을 만듭니다.
   - **파티션 기준**: 파티션별 파일 이름을 입력합니다.
   - **열의 데이터로**: 출력 파일을 열 값으로 설정합니다. 경로는 대상 폴더가 아닌 데이터 세트 컨테이너를 기준으로 합니다. 데이터 세트에 폴더 경로가 있는 경우 재정의됩니다.
   - **단일 파일로 출력**: 분할된 출력 파일을 명명된 단일 파일로 결합합니다. 경로는 데이터 세트 폴더를 기준으로 합니다. 병합 작업은 노드 크기에 따라 실패할 수 있습니다. 대규모 데이터 세트에는 이 옵션을 권장하지 않습니다.

**모두 인용:** 모든 값을 인용 부호로 묶을지 여부를 결정합니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용은 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요. 

## <a name="delete-activity-properties"></a>삭제 작업 속성

속성에 대한 자세한 내용은 [삭제 작업](delete-activity.md)을 확인하세요.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원됩니다. 앞서 설명한 새 모델을 사용하는 것이 좋습니다. 새 모델을 생성하도록 작성 UI가 전환되었습니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 세트 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 `type` 속성을 `AzureBlob`로 설정해야 합니다. | 예 |
| folderPath | Blob Storage의 컨테이너 및 폴더 경로입니다. <br/><br/>컨테이너 이름을 제외한 경로에 대해 와일드카드 필터가 지원됩니다. 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br/><br/>예는 `myblobcontainer/myblobfolder/`입니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 복사 또는 조회 작업의 경우 예, GetMetadata 작업의 경우 아니요 |
| fileName | 지정된 `folderPath` 값 아래의 Blob에 대한 이름 또는 와일드카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 Blob을 가리킵니다. <br/><br/>필터에 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>파일 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`를 사용하여 이스케이프합니다.<br/><br/>`fileName`이 출력 데이터 세트에 대해 지정되지 않고 `preserveHierarchy`가 작업 싱크에 지정되지 않으면, 복사 작업에서 자동으로 다음 패턴의 Blob 이름을 생성합니다. "*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* " 예: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". <br/><br/>쿼리 대신 테이블 이름을 사용하여 테이블 형식 원본에서 복사하는 경우 이름 패턴은 `[table name].[format].[compression if configured]`입니다. 예: "MyTable.csv". | 예 |
| modifiedDatetimeStart | 파일은 마지막으로 수정된 특성을 기준으로 필터링됩니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 이 설정을 사용하면 대량의 파일을 필터링하려는 경우 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 `NULL`일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 `NULL`이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 날짜/시간 값이 있지만 `modifiedDatetimeStart`가 `NULL`이면 마지막으로 수정된 특성이 날짜/시간 값보다 작은 파일이 선택됩니다.| 예 |
| modifiedDatetimeEnd | 파일은 마지막으로 수정된 특성을 기준으로 필터링됩니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 이 설정을 사용하면 대량의 파일을 필터링하려는 경우 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 `NULL`일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 `NULL`이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 날짜/시간 값이 있지만 `modifiedDatetimeStart`가 `NULL`이면 마지막으로 수정된 특성이 날짜/시간 값보다 작은 파일이 선택됩니다.| 예 |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** 입니다. **format** 의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. | 아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. | 예 |

>[!TIP]
>폴더 아래에서 모든 Blob을 복사하려면 **folderPath** 만을 지정합니다.<br>지정된 이름의 단일 Blob을 복사하려면 폴더 부분에 **folderPath** 를 지정하고 파일 이름에 **fileName** 을 지정합니다.<br>폴더 아래에서 Blob의 하위 집합을 복사하려면 폴더 부분에 **folderPath** 및 와일드카드 필터로 **fileName** 을 지정합니다. 

**예:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>복사 작업을 위한 레거시 원본 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 `type` 속성을 `BlobSource`로 설정해야 합니다. | 예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. `recursive`를 `true`로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다.<br/>허용되는 값은 `true`(기본값) 및 `false`입니다. | 예 |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>복사 작업을 위한 레거시 싱크 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 `type` 속성을 `BlobSink`로 설정해야 합니다. | 예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예 |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
