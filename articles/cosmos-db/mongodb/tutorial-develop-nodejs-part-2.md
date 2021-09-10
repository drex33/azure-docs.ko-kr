---
title: Azure Cosmos DB의 API for MongoDB를 사용하여 Node.js Express 앱 만들기(2부)
description: MongoDB에 사용하는 것과 정확히 동일한 API를 사용한 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법에 대한 자습서 시리즈의 2부
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: f78c64d8ab22168bba44305bffa66987e02ae21c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039389"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Azure Cosmos DB의 API for MongoDB를 사용하여 Angular 앱 만들기 - Node.js Express 앱 만들기
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

여러 파트로 구성된 이 자습서에서는 Express 및 Angular를 사용하여 Node.js로 작성된 새 앱을 만든 다음, [Cosmos DB의 API for MongoDB를 사용하여 구성된 Cosmos 계정](mongodb-introduction.md)에 연결하는 방법을 보여줍니다.

자습서의 2부는 [소개](tutorial-develop-nodejs-part-1.md)를 기반으로 하고 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * Angular CLI 및 TypeScript 설치
> * Angular를 사용하여 새 프로젝트 만들기
> * Express 프레임워크를 사용하여 앱 빌드
> * Postman에서 앱 테스트

## <a name="video-walkthrough"></a>연습 동영상

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>필수 구성 요소

자습서의 이 부분을 시작하기 전에 [소개 비디오](tutorial-develop-nodejs-part-1.md)를 시청했는지 확인합니다.

또한 이 자습서를 사용하려면 다음이 필요합니다. 
* [Node.js](https://nodejs.org/) 버전 8.4.0 이상
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) 또는 즐겨 찾는 코드 편집기

> [!TIP]
> 이 자습서에서는 애플리케이션을 구축하는 단계를 단계별로 설명합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 애플리케이션을 가져올 수 있습니다.

## <a name="install-the-angular-cli-and-typescript"></a>Angular CLI 및 TypeScript 설치

1. Windows 명령 프롬프트 또는 Mac 터미널 창을 열고 Angular CLI를 설치합니다.

    ```bash
    npm install -g @angular/cli
    ```

2. 프롬프트에서 다음 명령을 입력하여 TypeScript를 설치합니다. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Angular CLI를 사용하여 새 프로젝트 만들기

1. 명령 프롬프트에서 새 프로젝트를 만들려는 폴더로 변경하고 다음 명령을 실행합니다. 이 명령은 새 폴더 및 angular-cosmosdb라는 프로젝트를 만들고 새 앱에 필요한 Angular 구성 요소를 설치합니다. 또한 최소한의 설치를 사용하고(--minimal), 프로젝트에서 Sass를 사용하도록 지정합니다(플래그로 지정된 CSS 유사 구문 --style scss).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. 명령이 완료되면 src/client 폴더로 디렉터리를 변경합니다.

    ```bash
    cd angular-cosmosdb
    ```

3. 그런 다음 Visual Studio Code에서 폴더를 엽니다.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Express 프레임워크를 사용하여 앱 빌드

1. Visual Studio Code의 **탐색기** 창에서 **src** 폴더를 마우스 오른쪽 단추로 클릭하고 **새 폴더** 를 클릭하고 새 폴더 이름을 *server* 로 지정합니다.

2. **탐색기** 창에서 **server** 폴더를 마우스 오른쪽 단추로 클릭하고, **새 파일** 을 클릭하고, 새 파일 이름을 *index.js* 로 지정합니다.

3. 명령 프롬프트로 다시 돌아가서 본문 파서를 설치하는 다음 명령을 사용합니다. 이렇게 하면 앱에서 API를 통해 전달되는 JSON 데이터를 구문 분석할 수 있습니다.

    ```bash
    npm i express body-parser --save
    ```

4. Visual Studio Code에서 index.js 파일에 다음 코드를 복사합니다. 이 코드에서는 다음을 수행합니다.
    * 참조 Express
    * 요청 본문에서 JSON 데이터를 읽기 위해 본문 파서 가져오기
    * 경로라는 기본 제공 기능 사용
    * 쉽게 코드의 위치를 찾을 수 있도록 루트 변수 설정
    * 포트 설정
    * Express 시작
    * 서버를 제공하는 데 사용하려고 하는 미들웨어를 사용하는 방법을 앱에 지시합니다
    * dist 폴더에 있는 모든 항목을 제공하여 고정 콘텐츠로 사용
    * 애플리케이션 제공 및 서버에 없는 모든 GET 요청에 index.html 제공(딥 링크의 경우)
    * app.listen에서 서버 시작
    * arrow 함수를 사용하여 활성 포트 기록
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Visual Studio Code의 **탐색기** 창에서 **server** 폴더를 마우스 오른쪽 단추로 클릭하고 **새 파일** 을 클릭합니다. 새 파일의 이름을 *routes.js* 로 지정합니다. 

6. 다음 코드를 **routes.js** 에 복사합니다. 이 코드에서는 다음을 수행합니다.
   * Express 라우터 참조
   * Heroes 가져오기
   * 정의된 Heroes에 JSON 다시 전송

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. 수정한 모든 파일 저장 

8. Visual Studio Code에서 **디버그** 단추 :::image type="icon" source="./media/tutorial-develop-nodejs-part-2/debug-button.png":::를 클릭하고 기어 단추 :::image type="icon" source="./media/tutorial-develop-nodejs-part-2/gear-button.png":::를 클릭합니다. Visual Studio Code에서 새로운 launch.json 파일이 열립니다. 

8. launch.json 파일의 11줄에서 `"${workspaceFolder}\\server"`을 `"program": "${workspaceRoot}/src/server/index.js"`로 변경하고 파일을 저장합니다.

9. **디버깅 시작** 단추 :::image type="icon" source="./media/tutorial-develop-nodejs-part-2/start-debugging-button.png":::를 클릭하여 앱을 실행합니다.

    앱이 오류 없이 실행되어야 합니다.

## <a name="use-postman-to-test-the-app"></a>Postman을 사용하여 앱 테스트

1. 이제 Postman을 열고 GET 상자에 `http://localhost:3000/api/heroes`를 배치합니다. 

2. **보내기** 단추를 클릭하고 앱에서 json 응답을 가져옵니다. 

    이 응답에서는 앱을 로컬로 실행된다고 표시합니다. 

    :::image type="content" source="./media/tutorial-develop-nodejs-part-2/azure-cosmos-db-postman.png" alt-text="요청 및 응답을 보여주는 Postman":::


## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Angular CLI를 사용하여 Node.js 프로젝트 만들기
> * Postman을 사용하여 앱 테스트

UI를 빌드하는 자습서의 다음 단계로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Angular를 사용하여 UI 빌드](tutorial-develop-nodejs-part-3.md)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
