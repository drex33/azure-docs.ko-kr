---
title: 연결된 서비스
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 연결된 서비스에 대해 알아봅니다. 연결된 서비스는 컴퓨팅 및 데이터 저장소를 서비스에 연결합니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: d9b6695824b5fc19d2cd2ee40aec929a372750f2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812035"
---
# <a name="linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics 연결된 서비스

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-create-datasets.md)
> * [현재 버전](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 연결된 서비스의 정의, 연결된 서비스를 JSON 형식으로 정의하는 방법, Azure Data Factory 및 Azure Synapse Analytics에서 연결된 서비스를 사용하는 방법에 대해 설명합니다.

자세한 내용은 [Azure Data Factory](introduction.md) 또는 [Azure Synapse](../synapse-analytics/overview-what-is.md)의 소개 문서를 참조하세요.

## <a name="overview"></a>개요

Azure Data Factory 및 Azure Synapse Analytics는 하나 이상의 파이프라인을 가질 수 있습니다. **파이프라인** 은 함께 하나의 작업을 수행하는 **활동** 의 논리적 그룹화입니다. 파이프라인의 활동은 데이터에 수행할 작업을 정의합니다. 예를 들어, 복사 활동을 사용하여 SQL Server에서 Azure Blob Storage로 데이터를 복사할 수 있습니다. 그런 다음 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 Hive 활동을 사용하여 출력 데이터를 생성하도록 Blob Storage의 데이터를 처리합니다. 마지막으로 두 번째 복사 활동을 사용하여 BI(비즈니스 인텔리전스) 보고 솔루션의 기반이 되는 Azure Synapse Analytics로 출력 데이터를 복사할 수 있습니다. 파이프라인 및 활동에 대한 자세한 내용은 [파이프라인 및 활동](concepts-pipelines-activities.md) 문서를 참조하세요.

이제 **데이터 세트** 는 입력 및 출력으로 **활동** 에서 사용하려는 데이터를 단순히 가리키거나 참조하는 데이터의 명명된 뷰입니다.

데이터 세트를 만들기 전에 **연결된 서비스** 를 만들어 데이터 저장소를 Data Factory 또는 Synapse Workspace에 연결해야 합니다. 연결된 서비스는 이 서비스에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 데이터 세트는 연결된 데이터 저장소 내의 데이터 구조를 나타내고, 연결된 서비스는 데이터 원본에 연결을 정의한다고 생각하시면 됩니다. 예를 들어, Azure Storage 연결된 서비스는 스토리지 계정을 서비스에 연결합니다. Azure Blob 데이터 세트는 Blob 컨테이너 및 처리할 입력 Blob이 포함된 Azure Storage 계정 내의 폴더를 나타냅니다.

샘플 시나리오는 다음과 같습니다. Blob Storage에서 SQL Database로 데이터를 복사하려면 두 개의 연결된 서비스, 즉, Azure Storage 및 Azure SQL Database를 만듭니다. 그런 다음, 두 개의 데이터 세트, 즉 Azure Blob 데이터 세트(Azure Storage 연결된 서비스 참조), Azure SQL 테이블 데이터 세트(Azure SQL Database 연결된 서비스 참조)를 만듭니다. Azure Storage 및 Azure SQL Database 연결된 서비스에는 서비스가 런타임에 Azure Storage 및 Azure SQL Database 각각에 연결하는 데 사용하는 연결 문자열이 포함되어 있습니다. Azure Blob 데이터 세트은 Blob Storage에 입력 Blob을 포함하는 Blob 컨테이너와 Blob 폴더를 지정합니다. Azure SQL Table 데이터 세트는 데이터를 복사할 Azure SQL Database의 SQL 테이블을 지정합니다.

다음 다이어그램에서는 서비스의 파이프라인, 활동, 데이터 세트 및 연결된 서비스 간의 관계를 보여 줍니다.

:::image type="content" source="media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png" alt-text="파이프라인, 활동, 데이터 세트, 연결된 서비스 간 관계":::

## <a name="linked-service-json"></a>연결된 서비스 JSON

연결된 서비스는 다음과 같이 JSON 형식으로 정의됩니다.

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

다음 표에서는 위의 JSON에서 속성을 설명합니다.

속성 | Description | 필수 |
-------- | ----------- | -------- |
name | 연결된 서비스의 이름입니다. [명명 규칙](naming-rules.md)을 참조하세요. |  예 |
type | 연결된 서비스의 형식입니다. 예: AzureBlobStorage(데이터 저장소) 또는 AzureBatch(컴퓨팅). typeProperties에 대한 설명을 참조하세요. | 예 |
typeProperties | 형식 속성은 각 데이터 저장소 또는 컴퓨팅에 대해 다릅니다. <br/><br/> 지원되는 데이터 저장소 형식 및 해당 형식 속성은 [커넥터 개요](copy-activity-overview.md#supported-data-stores-and-formats) 문서를 참조하세요. 데이터 저장소 관련 형식 속성에 대해 알아보려면 데이터 저장소 커넥터 문서로 이동하세요. <br/><br/> 지원되는 컴퓨팅 형식 및 해당 형식 속성은 [컴퓨팅 연결 서비스](compute-linked-services.md)를 참조하세요. | 예 |
connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예

## <a name="linked-service-example"></a>연결된 서비스 예제

다음의 연결된 서비스는 Azure Blob Storage 연결된 서비스입니다. 형식은 Azure Blob Storage로 설정됩니다. Azure Blob Storage 연결된 서비스의 형식 속성에는 연결 문자열이 포함됩니다. 서비스는 이 연결 문자열을 사용하여 런타임에 데이터 저장소에 연결합니다.

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

## <a name="create-linked-services"></a>연결된 서비스 만들기

연결된 서비스는 [관리 허브](author-management-hub.md) 및 이를 참조하는 모든 활동, 데이터 세트 또는 데이터 흐름을 사용하여 Azure Data Factory UX에서 만들 수 있습니다.

[.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), [Azure Resource Manager 템플릿](quickstart-create-data-factory-resource-manager-template.md) 및 [Azure Portal](quickstart-create-data-factory-portal.md) 등의 도구 또는 SDK 중 하나를 사용하여 연결된 서비스를 만들 수 있습니다.


## <a name="data-store-linked-services"></a>데이터 저장소 연결된 서비스

지원되는 데이터 저장소 목록은 [커넥터 개요](copy-activity-overview.md#supported-data-stores-and-formats) 문서에서 찾을 수 있습니다. 데이터 저장소를 클릭하여 지원되는 연결 속성을 알아봅니다.

## <a name="compute-linked-services"></a>컴퓨팅 연결 서비스

다른 구성 뿐만 아니라 서비스에서 연결할 수 있는 다양한 컴퓨팅 환경에 대한 자세한 내용은 [지원되는 컴퓨팅 환경](compute-linked-services.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 도구 또는 SDK 중 하나를 사용하여 파이프라인 및 데이터 세트를 만들기 위한 단계별 지침은 다음 자습서를 참조하세요.

- [빠른 시작: .NET을 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [빠른 시작: PowerShell을 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)
- [빠른 시작: REST API를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md)
- [빠른 시작: Azure Portal을 사용하여 Data Factory 만들기](quickstart-create-data-factory-portal.md)
