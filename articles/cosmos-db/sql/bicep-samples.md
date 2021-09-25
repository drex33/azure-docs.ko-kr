---
title: Azure Cosmos DB Core(SQL API)에 대한 Bicep 샘플
description: Bicep을 사용하여 Azure Cosmos DB를 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 8cab03d2b3cd3c6d7b6a2f9bd15c686bea529de9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705686"
---
# <a name="bicep-for-azure-cosmos-db"></a>Azure Cosmos DB용 Bicep

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Core(SQL) API 계정에 대한 Bicep 샘플을 보여줍니다. [Cassandra,](../cassandra/manage-with-bicep.md) [Gremlin,](../graph/manage-with-bicep.md) [MongoDB](../mongodb/manage-with-bicep.md)및 [Table](../table/manage-with-bicep.md) API에 대한 Bicep 샘플을 찾을 수도 있습니다.

## <a name="core-sql-api"></a>Core(SQL) API

|**샘플**|**설명**|
|---|---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-bicep.md#create-autoscale) | 자동 크기 조정 처리량이 있는 데이터베이스 및 컨테이너라는 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[분석 저장소를 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-bicep.md#create-analytical-store) | 분석 TTL을 사용하도록 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량을 사용하는 옵션을 사용하여 한 지역에 Core(SQL) API 계정을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-with-bicep.md#create-manual) | 표준 처리량이 있는 데이터베이스와 컨테이너의 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[저장 프로시저, 트리거 및 UDF를 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-with-bicep.md#create-sproc) | 컨테이너에 대한 저장 프로시저, 트리거 및 UDF를 사용하여 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[Azure AD ID, 역할 정의 및 역할 할당을 사용하여 Azure Cosmos 계정 만들기](manage-with-bicep.md#create-rbac) | 서비스 주체에서 Azure AD ID, 역할 정의 및 역할 할당을 사용하여 코어(SQL) API 계정을 만듭니다. |
|[체험 계층 Azure Cosmos 계정 만들기](manage-with-bicep.md#free-tier) |  무료 계층에서 Azure Cosmos DB Core(SQL) API 계정을 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.

* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요.
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
