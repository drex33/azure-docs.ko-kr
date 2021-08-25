---
title: MongoB용 Cosmos DB의 API를 사용하는 Node.Js, Angular 앱(1부)
description: 이 동영상 기반 자습서 시리즈에서 MongoDB에 사용하는 것과 정확히 동일한 API를 사용하여 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법을 알아봅니다.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: 6a36836e1e80038978113e592bb06296276abd7c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786292"
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
