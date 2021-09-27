---
title: 연결된 서비스 매개 변수화
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인에 연결된 서비스를 매개 변수화하고 런타임에 동적 값을 전달하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
author: chez-charlie
ms.author: chez
ms.openlocfilehash: 6500e743644cc505a15e44be02358d943fa5baa4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750514"
---
# <a name="parameterize-linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics에 연결된 서비스 매개 변수화

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이제 연결된 서비스를 매개 변수화하고 런타임에 동적 값을 전달할 수 있습니다. 예를 들어 동일한 논리 SQL Server의 다른 데이터베이스에 연결하려는 경우 이제 연결된 서비스 정의에서 데이터베이스 이름을 매개 변수화할 수 있습니다. 이렇게 하면 논리 SQL Server에서 각 데이터베이스의 연결된 서비스를 만들 필요가 없습니다. 예를 들어 연결된 서비스 정의에서 *사용자 이름* 과 같은 기타 속성을 매개 변수화할 수 있습니다.

Azure Portal의 UI 또는 프로그래밍 인터페이스를 사용하여 연결된 서비스를 매개 변수화할 수 있습니다.

> [!TIP]
> 암호니 비밀은 매개 변수화하지 않는 것이 좋습니다. 대신, Azure Key Vault의 모든 암호를 저장하고 *비밀 이름* 을 매개 변수화합니다.

> [!Note]
> 매개 변수 이름에 "-" 사용에 대한 공개 버그가 있습니다. 버그가 해결될 때까지 "-" 없이 이름을 사용하는 것이 좋습니다.

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>지원되는 연결된 서비스 유형

연결된 모든 서비스 유형이 매개 변수화에 대해 지원됩니다.

**UI에서 기본적으로 지원됨:** UI에 연결된 서비스를 작성할 때 서비스는 다음 유형의 연결된 서비스에 대해 기본 제공 매개 변수화 환경을 제공합니다. 연결된 서비스 생성/편집 블레이드에서 새 매개 변수에 대한 옵션을 찾고 동적 콘텐츠를 추가할 수 있습니다. [UI 환경](#ui-experience)을 참조하세요.

- Amazon Redshift
- Amazon S3
- Amazon S3 호환 스토리지
- Azure Blob Storage
- Azure Cosmos DB(SQL API)
- Azure Data Lake Storage Gen2
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Synapse Analytics 
- Azure Table Storage
- 일반 HTTP
- 일반 REST
- MySQL
- Oracle
- Oracle Cloud Storage
- SQL Server

**고급 작성:** 위 목록에 없는 다른 연결된 서비스 유형의 경우 UI에서 JSON을 편집하여 연결된 서비스를 매개 변수화할 수 있습니다.

- 연결된 서비스 생성/편집 블레이드에서 "고급"을 펼치고, "JSON 형식의 동적 콘텐츠 지정" 확인란을 선택하고, 연결된 서비스 JSON 페이로드를 지정합니다. 
- 또는 매개 변수화를 사용하지 않고 연결된 서비스를 생성된 후 [관리 허브](author-visually.md#management-hub) -> 연결된 서비스 -> 특정 연결된 서비스를 찾고 -> "코드"("{}" 단추)를 클릭하여 JSON을 편집합니다. 

매개 변수를 정의하고 ` @{linkedService().paramName} `을 사용하여 매개 변수를 참조하는 ` parameters` 섹션을 추가하려면 [JSON 샘플](#json)을 참조하세요.

## <a name="ui-experience"></a>UI 환경

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-1.png" alt-text="연결된 서비스 정의에 동적 콘텐츠 추가":::

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-2.png" alt-text="새 매개 변수 만들기":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-1-synapse.png" alt-text="연결된 서비스 정의에 동적 콘텐츠 추가":::

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-2-synapse.png" alt-text="새 매개 변수 만들기":::

---

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
