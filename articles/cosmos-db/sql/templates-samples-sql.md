---
title: Azure Cosmos DB Core용 Azure Resource Manager 템플릿(SQL API)
description: Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB를 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 58c416ec16cbfdade541119b88fe0524071e4b3b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117173"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Resource Manager 템플릿
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Core(SQL) API 계정에 대한 Azure Resource Manager 템플릿 예제만 보여줍니다. [Cassandra](../cassandra/templates-samples.md), [Gremlin](../graph/resource-manager-template-samples.md), [MongoDB](../mongodb/resource-manager-template-samples.md) 및 [Table](../table/resource-manager-templates.md) API에 대한 템플릿 예제를 찾을 수도 있습니다.

## <a name="core-sql-api"></a>Core(SQL) API

|**템플릿**|**설명**|
|---|---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-templates.md#create-autoscale) | 이 템플릿은 자동 크기 조정 처리량이 있는 데이터베이스와 컨테이너의 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[분석 저장소를 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-templates.md#create-analytical-store) | 이 템플릿은 분석 TTL이 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량을 사용하는 옵션을 통해 한 지역에 Core(SQL) API 계정을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-templates.md#create-manual) | 이 템플릿은 표준 처리량이 있는 데이터베이스와 컨테이너의 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[저장 프로시저, 트리거 및 UDF를 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-with-templates.md#create-sproc) | 이 템플릿은 컨테이너에 대한 저장 프로시저, 트리거 및 UDF를 사용하여 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[Azure AD ID, 역할 정의 및 역할 할당을 사용하여 Azure Cosmos 계정 만들기](manage-with-templates.md#create-rbac) | 이 템플릿은 서비스 주체에 대해 Azure AD ID, 역할 정의 및 역할 할당을 사용하여 Core(SQL) API 계정을 만듭니다. |
|[기존 Azure Cosmos 계정용 프라이빗 엔드포인트 만들기](../how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos Core(SQL) API 계정에 대한 프라이빗 엔드포인트를 만듭니다. |
|[체험 계층 Azure Cosmos 계정 만들기](manage-with-templates.md#free-tier) |  이 템플릿은 체험 계층에서 Azure Cosmos DB Core(SQL) API 계정을 만듭니다. |

참조 설명서는 [Azure Cosmos DB용 Azure Resource Manager 참조](/azure/templates/microsoft.documentdb/allversions) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.