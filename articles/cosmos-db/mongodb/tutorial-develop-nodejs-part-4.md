---
title: Azure Cosmos DB의 API for MongoDB를 사용하여 Angular 앱 만들기(1부)
description: MongoDB에 사용하는 것과 정확히 동일한 API를 사용한 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법에 대한 자습서 시리즈의 4부
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18, devx-track-js, devx-track-azurecli
ms.reviewer: sngun
ms.openlocfilehash: 41a025607979fcca68b0bfc61c8dd62b6ba4a0f7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035351"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Azure Cosmos DB의 API for MongoDB를 사용하여 Angular 앱 만들기 - Cosmos 계정 만들기
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

여러 파트로 구성된 이 자습서에서는 Express 및 Angular를 사용하여 Node.js로 작성된 새 앱을 만든 다음, [Cosmos DB의 API for MongoDB를 사용하여 구성된 Cosmos 계정](mongodb-introduction.md)에 연결하는 방법을 보여줍니다.

자습서의 4부는 [3부](tutorial-develop-nodejs-part-3.md)를 기반으로 하고 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure 리소스 그룹 만들기
> * Azure CLI를 사용하여 Cosmos 계정 만들기

## <a name="video-walkthrough"></a>연습 동영상

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>필수 구성 요소

자습서의 이 부분을 시작하기 전에 자습서 [3부](tutorial-develop-nodejs-part-3.md)의 단계를 완료했는지 확인합니다. 

이 자습서 섹션에서는 (인터넷 브라우저에서) 로컬로 설치된 Azure Cloud Shell 또는 [Azure CLI](/cli/azure/install-azure-cli)를 사용합니다.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> 이 자습서에서는 애플리케이션을 구축하는 단계를 단계별로 설명합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 애플리케이션을 가져올 수 있습니다.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

[`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 Azure Cosmos DB 계정을 만듭니다.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* `<cosmosdb-name>`이 고유한 Azure Cosmos DB 계정 이름을 사용하는 경우 해당 이름은 Azure에 있는 모든 Azure Cosmos DB 계정 이름에 대해 고유해야 합니다.
* `--kind MongoDB` 설정을 사용하면 Azure Cosmos DB에서 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

명령이 완료되는 데 일 분 정도 걸릴 수 있습니다. 작업이 완료되면 터미널 창에는 새 데이터베이스에 대한 정보가 표시됩니다. 

Cosmos DB Azure 계정을 만들면:
1. 새 브라우저 창을 열고 [https://portal.azure.com](https://portal.azure.com)으로 이동합니다.
1. 왼쪽 모음에서 모든 Azure Cosmos DB 로고:::image type="icon" source="./media/tutorial-develop-nodejs-part-4/azure-cosmos-db-icon.png":::를 클릭하면 사용자가 보유한 모든 Azure Cosmos DB가 표시됩니다.
1. 방금 만든 Azure Cosmos DB 계정을 클릭하고, **개요** 탭을 선택하고, 아래로 스크롤하여 데이터베이스가 위치한 맵을 봅니다. 

    :::image type="content" source="./media/tutorial-develop-nodejs-part-4/azure-cosmos-db-angular-portal.png" alt-text="스크린샷은 Azure Cosmos DB 계정의 개요를 보여줍니다.":::

4. 왼쪽 탐색 창에서 아래로 스크롤하고 **데이터를 전역적으로 복제** 탭을 클릭하면 복제 가능한 다른 영역을 볼 수 있는 맵을 표시합니다. 예를 들어, 오스트레일리아 동남부 또는 오스트레일리아 동부를 클릭하고 오스트레일리아에 데이터를 복제할 수 있습니다. [Azure Cosmos DB를 사용하여 전역적으로 데이터를 배포하는 방법](../distribute-data-globally.md)에서 전역 복제에 대해 자세히 알아볼 수 있습니다. 지금은 이전 인스턴스 및 복제할 시기를 유지하겠습니다.

    :::image type="content" source="./media/tutorial-develop-nodejs-part-4/azure-cosmos-db-replicate-portal.png" alt-text="스크린샷은 전역적으로 데이터 복제가 선택된 Azure Cosmos DB 계정을 보여줍니다.":::

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure 리소스 그룹 만들기
> * Azure CLI를 사용하여 Azure Cosmos DB 계정 만들기

Mongoose를 사용하여 Azure Cosmos DB를 앱에 연결하는 자습서의 다음 단계로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Mongoose를 사용하여 Azure Cosmos DB에 연결](tutorial-develop-nodejs-part-5.md)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.