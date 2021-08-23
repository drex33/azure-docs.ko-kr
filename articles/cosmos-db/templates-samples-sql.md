---
title: Azure Cosmos DB Core용 Azure Resource Manager 템플릿(SQL API)
description: Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB를 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: bb39414e73ec3df2d2714d794f7060815b99bce1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536079"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Resource Manager 템플릿
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 Core(SQL) API 계정에 대한 Azure Resource Manager 템플릿 예제만 보여줍니다. [Cassandra](cassandra/templates-samples.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](mongodb/resource-manager-template-samples.md) 및 [Table](table/resource-manager-templates.md) API에 대한 템플릿 예제를 찾을 수도 있습니다.

## <a name="core-sql-api"></a>Core(SQL) API

|**템플릿**|**설명**|
|---|---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-templates.md#create-autoscale) | 이 템플릿은 자동 크기 조정 처리량이 있는 데이터베이스와 컨테이너의 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[분석 저장소를 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-templates.md#create-analytical-store) | 이 템플릿은 분석 TTL이 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량을 사용하는 옵션을 통해 한 지역에 Core(SQL) API 계정을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-templates.md#create-manual) | 이 템플릿은 표준 처리량이 있는 데이터베이스와 컨테이너의 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[저장 프로시저, 트리거 및 UDF를 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-with-templates.md#create-sproc) | 이 템플릿은 컨테이너에 대한 저장 프로시저, 트리거 및 UDF를 사용하여 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[Azure AD ID, 역할 정의 및 역할 할당을 사용하여 Azure Cosmos 계정 만들기](manage-with-templates.md#create-rbac) | 이 템플릿은 서비스 주체에 대해 Azure AD ID, 역할 정의 및 역할 할당을 사용하여 Core(SQL) API 계정을 만듭니다. |
|[기존 Azure Cosmos 계정용 프라이빗 엔드포인트 만들기](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos Core(SQL) API 계정에 대한 프라이빗 엔드포인트를 만듭니다. |
|[체험 계층 Azure Cosmos 계정 만들기](manage-with-templates.md#free-tier) |  이 템플릿은 체험 계층에서 Azure Cosmos DB Core(SQL) API 계정을 만듭니다. |

참조 설명서는 [Azure Cosmos DB용 Azure Resource Manager 참조](/azure/templates/microsoft.documentdb/allversions) 페이지를 참조하세요.
