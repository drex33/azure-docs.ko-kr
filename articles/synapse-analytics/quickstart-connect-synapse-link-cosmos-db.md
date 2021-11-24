---
title: '빠른 시작: Azure Cosmos DB용 Azure Synapse Link에 연결'
description: Synapse Link를 사용하여 Azure Cosmos DB를 Synapse 작업 영역에 연결하는 방법
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db, mode-other
ms.openlocfilehash: f181d9fe0d8e2ed92d06f564f27436494299c52a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133045462"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>빠른 시작: Azure Cosmos DB용 Azure Synapse Link에 연결

이 문서에서는 Synapse Link를 사용하여 Azure Synapse Analytics Studio에서 Azure Cosmos DB 데이터베이스에 액세스하는 방법을 설명합니다. 

## <a name="prerequisites"></a>사전 요구 사항

Azure Cosmos DB 계정을 작업 영역에 연결하기 전에 몇 가지 필요한 사항이 있습니다.

* 기존 Azure Cosmos DB 계정 또는 이 [빠른 시작](../cosmos-db/how-to-manage-database-account.md)에 따라 새 계정을 만듭니다.
* 이 [빠른 시작](./quickstart-create-workspace.md)에 따라 기존 Synapse 작업 영역을 만들거나 새 작업 영역을 만듭니다. 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소 사용

운영 성능에 영향을 주지 않고 대규모 분석을 Azure Cosmos DB으로 실행하려면 Azure Cosmos DB에 대해 Synapse Link를 사용하는 것이 좋습니다. 이 함수는 컨테이너에 HTAP 기능을 제공하고 Azure Synapse의 기본 제공 지원을 제공합니다. 이 빠른 시작에 따라 Cosmos DB 컨테이너에 대해 Synapse 링크를 사용하도록 설정합니다.

## <a name="navigate-to-synapse-studio"></a>Synapse Studio로 이동

Synapse 작업 영역에서 **Synapse Studio 시작** 을 선택합니다. Synapse Studio 홈페이지에서 **데이터를 선택하면 **데이터 개체 탐색기** 로 이동합니다.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Synapse 작업 영역에 Azure Cosmos DB 데이터베이스 연결

Azure Cosmos DB 데이터베이스 연결은 연결된 서비스로 수행됩니다. 사용자는 Cosmos DB 연결 서비스를 통해 Azure Synapse Analytics 또는 SQL용 Apache Spark에서 Azure Cosmos DB로 데이터를 탐색, 읽기 및 쓰기를 할 수 있습니다.

데이터 개체 탐색기에서 다음 단계를 수행하여 Azure Cosmos DB 데이터베이스를 직접 연결할 수 있습니다.

1. 데이터 근처 ***+*** 아이콘을 선택합니다.
2. **외부 데이터에 연결** 을 선택합니다.
3. 연결하려는 API를 선택합니다. SQL 또는 MongoDB
4. ***계속*** 을 선택합니다.
5. 연결된 서비스 이름을 지정합니다. 이 이름은 개체 탐색기에 표시되며 Synapse 런타임에서 데이터베이스 및 컨테이너에 연결하는 데 사용됩니다. 친숙한 이름을 사용하는 것이 좋습니다.
6. **Cosmos DB 계정 이름** 및 **데이터베이스 이름** 을 선택합니다.
7. (선택 사항) 지역이 지정되지 않은 경우 Synapse 런타임 작업은 분석 저장소가 활성화된 가장 가까운 지역으로 라우팅됩니다. 그러나 사용자가 Cosmos DB 분석 저장소에 액세스할 지역을 수동으로 설정할 수 있습니다. **추가 연결 속성** 을 선택한 다음, **새로 만들기** 를 선택합니다. **속성 이름** 아래에 **_PreferredRegions_ *_를 작성하고 _* 값** 을 원하는 지역(예: WestUS2, 단어와 숫자 사이에 공백이 없음)으로 설정합니다.
8. ***만들기***

Azure Cosmos DB 데이터베이스는 Azure Cosmos DB 섹션의 **연결** 탭 아래에 표시됩니다. 다음 아이콘을 사용하여 HTAP 지원 Azure Cosmos DB 컨테이너를 OLTP 전용 컨테이너와 구분할 수 있습니다.

**Synapse 컨테이너**:

![HTAP 컨테이너](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**OLTP 전용 컨테이너**:

![OLTP 컨테이너](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>코드 생성 작업과 빠르게 상호 작용

컨테이너를 마우스 오른쪽 단추로 클릭하면 Spark 또는 SQL 런타임을 트리거하는 제스처 목록이 표시됩니다. 컨테이너에 쓰기는 Azure Cosmos DB의 트랜잭션 저장소를 통해 수행되며 요청 단위를 사용합니다.  

## <a name="next-steps"></a>다음 단계

* [Synapse와 Azure Cosmos DB 간에 지원되는 항목 알아보기](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Azure Synapse Analytics용 Apache Spark 3을 사용하여 분석 저장소를 쿼리하는 방법 알아보기](synapse-link/how-to-query-analytical-store-spark-3.md)
* [Azure Synapse Analytics용 Apache Spark 2를 사용하여 분석 저장소를 쿼리하는 방법 알아보기](synapse-link/how-to-query-analytical-store-spark.md)
