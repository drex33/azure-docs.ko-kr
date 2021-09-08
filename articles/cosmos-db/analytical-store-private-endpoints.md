---
title: Azure Cosmos DB 분석 저장소에 대한 프라이빗 엔드포인트 구성
description: Azure Cosmos DB 분석 저장소에 대한 관리형 프라이빗 엔드포인트를 설정하여 네트워크 액세스를 제한하는 방법을 알아봅니다.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 457182e2f8bf24e911fa2b5e0514b0dba48d6ae0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111972"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소에 대한 Azure Private Link 구성
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

이 문서에서는 Azure Cosmos DB 분석 저장소에 대한 관리형 프라이빗 엔드포인트를 설정하는 방법을 설명합니다. 트랜잭션 저장소를 사용하는 경우 [트랜잭션 저장소에 대한 프라이빗 엔드포인트](how-to-configure-private-endpoints.md) 문서를 참조하세요. [관리형 프라이빗 엔드포인트](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)를 사용하면 Azure Cosmos DB 분석 저장소에 대한 네트워크 액세스를 Azure Synapse 작업 영역과 연결된 관리형 가상 네트워크로 제한할 수 있습니다. 관리형 프라이빗 엔드포인트는 분석 저장소에 대한 프라이빗 링크를 설정합니다.

> [!NOTE]
> Cosmos DB에 프라이빗 DNS 영역을 사용 중이고 분석 저장소 하위 리소스에 대한 Synapse 관리형 프라이빗 엔드포인트를 만들려면 먼저 Cosmos DB 가상 네트워크에 연결된 분석 저장소(`privatelink.analytics.cosmos.azure.com`)의 DNS 영역을 만들어야 합니다.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>분석 저장소에 대한 프라이빗 엔드포인트 사용

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>관리형 가상 네트워크를 사용하여 Azure Synapse Analytics 작업 영역 설정

[데이터 반출이 설정된 Azure Synapse Analytics에서 작업 영역을 만듭니다.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) [데이터 반출 방지](../synapse-analytics/security/workspace-data-exfiltration-protection.md)를 사용하면 악의적인 사용자가 Azure 리소스에서 조직의 범위 밖의 위치로 데이터를 복사하거나 전송할 수 없도록 할 수 있습니다.

Azure Synapse Analytics 작업 영역에 대해 데이터 반출 방지가 설정된 경우 다음 액세스 제한이 적용됩니다.

* Azure Synapse Analytics에 Azure Spark를 사용하는 경우 Azure Cosmos DB 분석 저장소에 대해 승인된 관리형 프라이빗 엔드포인트에 대한 액세스만 허용됩니다.

* Synapse 서버리스 SQL 풀을 사용하는 경우 Azure Synapse Link를 사용하여 모든 Azure Cosmos DB 계정을 쿼리할 수 있습니다. 그러나 [CETAS(create external tables as select)](../synapse-analytics/sql/develop-tables-cetas.md) 쓰기 요청은 작업 영역 가상 네트워크에서 승인된 관리형 프라이빗 엔드포인트에만 허용됩니다.

> [!NOTE]
> 작업 영역을 만든 후에는 관리형 가상 네트워크 및 데이터 반출 구성을 변경할 수 없습니다.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소에 대한 관리형 프라이빗 엔드포인트 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal에서 Synapse Analytics 작업 영역으로 이동하고 **개요** 창을 엽니다.

1. **시작** 창으로 이동하여 Synapse Studio를 시작하고 **Synapse Studio 열기** 에서 **열기** 를 선택합니다.

1. Synapse Studio에서 **관리** 탭을 엽니다.

1. **관리형 프라이빗 엔드포인트** 로 이동하고 **새로 만들기** 를 선택합니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="분석 저장소에 대한 새 프라이빗 엔드포인트를 만듭니다." border="true":::

1. **Azure Cosmos DB(SQL API)** 계정 유형을 선택하고 **계속** 합니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Azure Cosmos DB SQL API를 선택하여 프라이빗 엔드포인트를 만듭니다." border="true":::

1. 다음 세부 정보를 사용하여 **관리형 새 프라이빗 엔드포인트** 양식을 채웁니다.

   * **이름** - 관리형 프라이빗 엔드포인트의 이름입니다. 이 이름은 만든 후에는 업데이트할 수 없습니다.
   * **설명** - 프라이빗 엔드포인트를 식별하는 친숙한 설명을 제공합니다.
   * **Azure 구독** - Azure 구독의 사용 가능한 계정 목록에서 Azure Cosmos DB 계정을 선택합니다.
   * **Azure Cosmos DB 계정 이름**  - SQL 또는 MongoDB 유형의 기존 Azure Cosmos DB 계정을 선택합니다.
   * **대상 하위 리소스** - 다음 옵션 중 하나를 선택합니다. **분석**: Azure Cosmos DB 분석 저장소에 대한 프라이빗 엔드포인트를 추가하려는 경우
     **SQL**(또는 **MongoDB**): OLTP 또는 트랜잭션 계정 엔드포인트를 추가하려는 경우

   > [!NOTE]
   > Azure Synapse Analytics 작업 영역에서 동일한 Azure Cosmos DB 계정에 트랜잭션 저장소와 분석 저장소 프라이빗 엔드포인트를 모두 추가할 수 있습니다. 분석 쿼리만 실행하려는 경우에는 분석 프라이빗 엔드포인트를 매핑해야 할 수 있습니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="대상 하위 리소스에 대한 분석을 선택합니다." border="true":::

1. 만든 후 프라이빗 엔드포인트 이름으로 이동하고 **Azure Portal에서 승인 관리** 를 선택합니다.

1. Azure Cosmos DB 계정으로 이동하여 프라이빗 엔드포인트를 선택하고 **승인** 을 선택합니다.

1. Synapse Analytics 작업 영역으로 다시 이동하고 **관리형 프라이빗 엔드포인트** 창에서 **새로 고침** 을 클릭합니다. 프라이빗 엔드포인트가 **승인됨** 상태인지 확인합니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="프라이빗 엔드포인트가 승인되었는지 확인합니다." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Azure Synapse Analytics용 Apache Spark 사용

데이터 반출 방지가 설정된 Azure Synapse 작업 영역을 만든 경우 Synapse Spark에서 Azure Cosmos DB 계정으로의 아웃바운드 액세스는 기본적으로 차단됩니다. 또한 Azure Cosmos DB에 기존 프라이빗 엔드포인트가 이미 있는 경우 Synapse Spark는 액세스할 수 없습니다.

Azure Cosmos DB 데이터에 대한 액세스를 허용하려면:

* Azure Synapse Link를 사용하여 Azure Cosmos DB 데이터를 쿼리하려면 Azure Cosmos DB 계정에 대한 관리형 **분석** 프라이빗 엔드포인트를 추가합니다.

* 트랜잭션 저장소에 대해 일괄 처리 쓰기/읽기 및/또는 스트리밍 쓰기/읽기를 사용하는 경우 Azure Cosmos DB 계정에 대한 관리형 *SQL* 또는 *MongoDB* 프라이빗 엔드포인트를 추가합니다. 또한 다음 코드 조각과 같이 *connectionMode* 를 *게이트웨이* 로 설정해야 합니다.

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Synapse 서버리스 SQL 풀 사용

Synapse 서버리스 SQL 풀은 관리형 가상 네트워크에 배포되지 않은 다중 테넌트 기능을 사용합니다. Azure Cosmos DB 계정에 기존 프라이빗 엔드포인트가 있으면 Synapse 서버리스 SQL 풀은 Azure Cosmos DB 계정의 네트워크 격리 검사로 인해 계정에 액세스하지 못하도록 차단됩니다.

Synapse 작업 영역에서 이 계정에 대한 네트워크 격리를 구성하려면:

1. Synapse 작업 영역에서 계정에 대한 `NetworkAclBypassResourceId` 설정을 지정하여 Azure Cosmos DB 계정에 액세스할 수 있도록 허용합니다.

   **PowerShell 사용**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Azure CLI 사용**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB 계정 및 Azure Synapse Analytics 작업 영역은 동일한 Azure AD(Azure Active Directory) 테넌트 아래에 있어야 합니다.

2. 이제 Azure Synapse Link를 통해 T-SQL 쿼리를 사용하여 서버리스 SQL 풀에서 계정에 액세스할 수 있습니다. 그러나 분석 저장소에서 데이터에 대한 네트워크 격리를 보장하려면 이 계정에 대한 **분석** 관리형 프라이빗 엔드포인트를 추가해야 합니다. 그러지 않으면 분석 저장소의 데이터는 공용 액세스에서 차단되지 않습니다.

> [!IMPORTANT]
> Azure Synapse Link를 사용하고 분석 저장소에서 데이터에 대한 네트워크 격리가 필요한 경우 **분석** 관리형 프라이빗 엔드포인트를 사용하여 Azure Cosmos DB 계정을 Synapse 작업 영역에 매핑해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Spark 3을 사용하여 분석 저장소 쿼리](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) 시작
* [Azure Synapse Spark 2를 사용하여 분석 저장소 쿼리](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) 시작
* [Azure Synapse 서버리스 SQL 풀을 사용하여 분석 저장소 쿼리](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) 시작
