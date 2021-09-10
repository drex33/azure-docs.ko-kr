---
title: Azure Cosmos DB용 Azure Synapse Link에 연결
description: Azure Synapse Link를 사용하여 Azure Cosmos DB 데이터베이스를 Azure Synapse 작업 영역에 연결하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: f2c687d3124e19bd91321391396499675052662b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113042"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Synapse Link에 연결

이 문서에서는 Azure Synapse Link를 사용하여 Azure Synapse Analytics Studio에서 Azure Cosmos DB 데이터베이스에 액세스하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Cosmos DB 데이터베이스를 작업 영역에 연결하기 전에 다음 항목이 필요합니다.

* 기존 Azure Cosmos DB 데이터베이스 또는 [빠른 시작: Azure Cosmos DB 계정 만들기](../../cosmos-db/how-to-manage-database-account.md)의 단계를 따라 새 계정을 만듭니다.
* 기존 Azure Synapse 작업 영역 또는 [빠른 시작: Synapse 작업 영역 만들기](../quickstart-create-workspace.md)의 단계를 따라 새 작업 영역을 만듭니다.

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Azure Cosmos DB 데이터베이스 계정에서 Synapse Link 사용

운영 성능에 영향을 주지 않고 대규모 분석을 Azure Cosmos DB으로 실행하려면 Azure Cosmos DB에 대해 Synapse Link를 사용하는 것이 좋습니다. Synapse Link는 컨테이너에 HTAP 기능을 제공하고 Azure Synapse의 기본 제공 지원을 제공합니다.

## <a name="go-to-synapse-studio"></a>Synapse Studio로 이동

Azure Synapse 작업 영역에서 **Synapse Studio 시작** 을 선택합니다. Synapse Studio 홈페이지에서 **데이터** 를 선택합니다. 그러면 데이터 개체 탐색기로 이동합니다.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역에 Azure Cosmos DB 데이터베이스 연결

Azure Cosmos DB 데이터베이스 연결은 연결된 서비스로 수행됩니다. Azure Cosmos DB 연결 서비스를 통해 Azure Synapse Analytics 또는 SQL용 Apache Spark에서 Azure Cosmos DB로 데이터를 탐색, 읽기 및 쓰기를 할 수 있습니다.

데이터 개체 탐색기에서 다음 단계를 따라 Azure Cosmos DB 데이터베이스에 직접 연결할 수 있습니다.

1. **데이터** 옆에 있는 **+** 아이콘을 선택합니다.
1. **외부 데이터에 대한 연결** 을 선택합니다.
1. 연결하려는 API를 선택합니다(예: **SQL API** 또는 **API for MongoDB**).
1. **계속** 을 선택합니다.
1. 식별 이름을 사용하여 연결된 서비스의 이름을 지정합니다. 이름은 개체 탐색기에 표시되며, Azure Synapse 런타임에서 데이터베이스 및 컨테이너에 연결하는 데 사용됩니다.
1. **Azure Cosmos DB 계정 이름** 및 **데이터베이스 이름** 을 선택합니다.
1. (선택 사항) 지역이 지정되지 않은 경우 Azure Synapse 런타임 작업은 분석 저장소가 활성화된 가장 가까운 지역으로 라우팅됩니다. 사용자가 Azure Cosmos DB 분석 저장소에 액세스하는 데 사용할 지역을 수동으로 설정할 수도 있습니다. **추가 연결 속성** 을 선택한 다음, **새로 만들기** 를 선택합니다. **속성 이름** 에 **PreferredRegions** 를 입력합니다. **값** 을 원하는 지역으로 설정합니다(예: **WestUS2**). (단어와 숫자 사이에는 공백이 없습니다.)
1. **만들기** 를 선택합니다.

Azure Cosmos DB 데이터베이스는 **Azure Cosmos DB** 섹션 아래의 **연결됨** 탭에 표시됩니다. Azure Cosmos DB를 사용하면 다음 아이콘을 통해 HTAP 지원 컨테이너를 OLTP 전용 컨테이너와 구분할 수 있습니다.

**OLTP 전용 컨테이너**:

![OLTP 컨테이너 아이콘을 표시하는 시각화입니다.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP 지원 컨테이너**:

![HTAP 컨테이너 아이콘을 표시하는 시각화입니다.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>코드 생성 작업과 빠르게 상호 작용

컨테이너를 마우스 오른쪽 단추로 클릭하면 Spark 또는 SQL 런타임을 트리거하는 제스처 목록이 표시됩니다. 컨테이너에 쓰기는 Azure Cosmos DB의 트랜잭션 저장소를 통해 수행되며 요청 단위를 사용합니다.  

## <a name="next-steps"></a>다음 단계

* [Azure Synapse와 Azure Cosmos DB 간에 지원되는 항목 알아보기](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB 분석 저장소에 대한 프라이빗 엔드포인트 구성](../../cosmos-db/analytical-store-private-endpoints.md)
* [Spark 3을 사용하여 분석 저장소를 쿼리하는 방법 알아보기](./how-to-query-analytical-store-spark-3.md)
* [Spark 2를 사용하여 분석 저장소를 쿼리하는 방법 알아보기](./how-to-query-analytical-store-spark.md)
