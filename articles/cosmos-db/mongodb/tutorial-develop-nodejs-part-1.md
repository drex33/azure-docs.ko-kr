---
title: MongoB용 Cosmos DB의 API를 사용하는 Node.Js, Angular 앱(1부)
description: 이 동영상 기반 자습서 시리즈에서 MongoDB에 사용하는 것과 정확히 동일한 API를 사용하여 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법을 알아봅니다.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: ec1275027c0a145da59e44017d596229d04e1298
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038207"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB를 사용하여 Angular 앱 만들기
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

여러 파트로 구성된 이 자습서에서는 Express 및 Angular를 사용하여 Node.js로 작성된 새 앱을 만든 다음, [Cosmos DB의 API for MongoDB를 사용하여 구성된 Cosmos 계정](mongodb-introduction.md)에 연결하는 방법을 보여줍니다.

Azure Cosmos DB는 모든 규모의 개방형 API를 포함하는 Microsoft의 빠른 NoSQL 데이터베이스입니다. 이를 통해 SLA 지원 속도와 가용성, 자동 및 즉각적인 확장성, 많은 NoSQL 엔진에 대한 오픈 소스 API를 갖춘 최신 앱을 개발할 수 있습니다.

이 다중 파트 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * [Angular CLI를 사용하여 Node.js Express 앱 만들기](tutorial-develop-nodejs-part-2.md)
> * [Angular를 사용하여 UI 빌드](tutorial-develop-nodejs-part-3.md)
> * [Azure CLI를 사용하여 Azure Cosmos DB 계정 만들기](tutorial-develop-nodejs-part-4.md) 
> * [Mongoose를 사용하여 Azure Cosmos DB에 연결](tutorial-develop-nodejs-part-5.md)
> * [앱에 함수 게시, 배치 및 삭제 추가](tutorial-develop-nodejs-part-6.md)

React를 사용하여 동일한 앱을 빌드하시겠습니까? [React 자습서 비디오 시리즈](tutorial-develop-mongodb-react.md)를 참조하세요.

## <a name="video-walkthrough"></a>연습 동영상

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>완료된 프로젝트 

이 자습서에서는 애플리케이션을 구축하는 단계를 단계별로 설명합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 애플리케이션을 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Cosmos DB를 사용하여 MEAN.js 앱을 만드는 단계의 개요를 확인합니다. 

Node.js Express 앱을 만드는 자습서의 다음 단계로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Angular CLI를 사용하여 Node.js Express 앱 만들기](tutorial-develop-nodejs-part-2.md)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
