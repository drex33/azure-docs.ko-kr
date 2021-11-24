---
title: 빠른 시작 - Azure Portal에서 Azure Cosmos DB 리소스 만들기
description: 이 빠른 시작에서는 Azure Portal를 사용하여 Azure Cosmos 데이터베이스, 컨테이너 및 항목을 만드는 방법을 보여줍니다.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: mode-portal
ms.openlocfilehash: dc728f6700f4dbe1cb2561361c8c8746d7af91a0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133059420"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 항목 만들기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
>  

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB를 사용하여 Azure Cosmos DB의 핵심인 글로벌 배포 및 수평적 크기 조정 기능의 이점을 활용하는 키/값 데이터베이스, 문서 데이터베이스 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB [SQL API](../introduction.md) 계정, 문서 데이터베이스 및 컨테이너를 만들고 컨테이너에 데이터를 추가하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독 또는 Azure Cosmos DB 체험 계정
- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>데이터베이스 및 컨테이너 추가 

Azure Portal에서 데이터 탐색기를 사용하여 데이터베이스와 컨테이너를 만들 수 있습니다.

1. Azure Cosmos DB 계정 페이지의 왼쪽 탐색 모음에서 **Data Explorer** 를 선택한 다음, **새 컬렉션** 을 선택합니다. 

    **컨테이너 추가** 창을 보려면 오른쪽으로 스크롤해야 할 수도 있습니다.

    :::image type="content" source="./media/create-cosmosdb-resources-portal/add-database-container.png" alt-text="Azure Portal Data Explorer, 컨테이너 추가 창":::

1. **컨테이너 추가** 창에서 새 컨테이너의 설정을 입력합니다.

    |설정|제안 값|Description
    |---|---|---|
    |**데이터베이스 ID**|ToDoList|새 데이터베이스의 이름으로 *ToDoList* 를 입력합니다. 데이터베이스 이름은 1~255자여야 하며, `/, \\, #, ?` 또는 후행 공백은 포함할 수 없습니다. **컨테이너 간에 처리량 공유** 옵션을 선택합니다. 그러면 데이터베이스에 프로비저닝된 처리량을 데이터베이스 내 모든 컨테이너가 공유할 수 있습니다. 이 옵션은 비용 절감에도 도움이 됩니다. |
    | **데이터베이스 처리량**| **자동 스케일링** 또는 **수동** 처리량을 프로비저닝할 수 있습니다. 수동 처리량을 사용하면 RU/s를 직접 스케일링할 수 있지만 자동 스케일링을 사용하면 시스템에서 사용량에 따라 RU/s를 스케일링할 수 있습니다. 이 예제에서는 **수동** 을 선택합니다. <br><br> 처리량을 400 RU/s(초당 요청 단위)로 유지합니다. 대기 시간을 줄이려면 [용량 계산기](estimate-ru-with-capacity-planner.md)를 사용하여 필요한 RU/s를 예측하여 나중에 처리량을 스케일 업할 수 있습니다.<br><br>**참고**: 서버리스 계정에서 새 컨테이너를 만들 때는 이 설정을 사용할 수 없습니다. |
    |**컨테이너 ID**|Items|새 컨테이너의 이름으로 *Items* 를 입력합니다. 컨테이너 ID에는 데이터베이스 이름과 동일한 문자 요구 사항이 적용됩니다.|
    |**파티션 키**| /category| 이 문서에 설명된 샘플은 파티션 키로 */category* 를 사용합니다.|

    이 예제에서는 **고유 키** 를 추가하거나 **분석 저장소** 를 설정하지 마세요. 고유 키를 사용하면 분할 키당 하나 이상의 값의 고유성을 보장하여 데이터베이스에 데이터 무결성 레이어를 추가할 수 있습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](../unique-keys.md)를 참조하세요. [분석 저장소](../analytical-store-introduction.md)는 트랜잭션 워크로드에 영향을 주지 않으면서 운영 데이터에 대한 대규모 분석을 사용하도록 설정하는 데 사용됩니다.

1. **확인** 을 선택합니다. Data Explorer가 새 데이터베이스와 앞에서 만든 컨테이너를 표시합니다.

## <a name="add-data-to-your-database"></a>데이터베이스에 데이터 추가

데이터 탐색기를 사용하여 새 데이터베이스에 데이터를 추가합니다.

1. **Data Explorer** 에서 **ToDoList** 데이터베이스를 확장하고 **Items** 컨테이너를 확장합니다. 다음으로, **항목** 을 선택한 다음, **새 항목** 을 선택합니다. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Azure Portal의 Data Explorer에서 새 문서 만들기":::
   
1. 다음 구조를 **문서** 창 오른쪽의 문서에 추가합니다.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. **저장** 을 선택합니다.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Azure Portal의 Data Explorer에서 Json 데이터를 복사하고 저장을 선택합니다.":::
   
1. **새 문서** 를 다시 선택하고 고유한 `id`의 다른 문서를 만들고 저장한 다음, 원하는 다른 속성 및 값을 지정합니다. Azure Cosmos DB가 데이터에 어떠한 스키마도 적용하지 않으므로 해당 문서는 사용자가 원하는 어떠한 구조든 가질 수 있습니다.

## <a name="query-your-data"></a>데이터 쿼리

[!INCLUDE [cosmos-db-create-sql-api-query-data](../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

데이터베이스만 삭제하고 나중에 Azure Cosmos 계정을 사용하려면 다음 단계에 따라 데이터베이스를 삭제할 수 있습니다.

* Azure Cosmos 계정으로 이동합니다.
* **데이터 탐색기** 를 열고, 마우스 오른쪽 단추로 삭제할 데이터베이스를 클릭하고, **데이터베이스 삭제** 를 선택합니다.
* 데이터베이스 ID/데이터베이스 이름을 입력하여 삭제 작업을 확인합니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 데이터베이스 및 컨테이너를 만드는 방법을 알아보았습니다. 이제 사용자의 Azure Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다. 

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](../import-data.md)
