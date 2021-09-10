---
title: '자습서: Azure Cosmos DB JavaScript SDK를 사용하여 SQL API 데이터를 관리하는 Node.js 웹앱 빌드'
description: 이 Node.js 자습서에서는 Microsoft Azure Cosmos DB를 사용하여 Microsoft Azure App Service의 Web Apps에 호스팅되는 Node.js Express 웹 애플리케이션에서 데이터를 저장하고 액세스하는 방법을 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: e83c245960815630891407a042303f1d8ae58b35
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123118201"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>자습서: JavaScript SDK를 사용하여 Azure Cosmos DB의 SQL API 계정을 관리하는 Node.js 웹앱 빌드 
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

개발자는 NoSQL 문서 데이터를 사용하는 애플리케이션을 가지고 있을 수도 있습니다. Azure Cosmos DB의 SQL API 계정을 사용하여 이 문서 데이터를 저장하고 액세스할 수 있습니다. 이 Node.js 자습서에서는 Microsoft Azure App Service의 Web Apps에 호스팅되는 Node.js Express 애플리케이션을 사용하여 Azure Cosmos DB의 SQL API 계정에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다. 이 자습서에서는 작업을 만들고 검색하고 수행할 수 있게 하는 웹 기반 애플리케이션(Todo 앱)을 빌드합니다. 작업은 Azure Cosmos DB에 JSON 문서로 저장됩니다. 

이 자습서에서는 Azure Portal을 사용하여 Azure Cosmos DB의 SQL API 계정을 만드는 방법을 보여 줍니다. 그런 다음, Node.js SDK 기반의 웹앱을 빌드하고 실행하여 데이터베이스와 컨테이너를 만들고 컨테이너에 항목을 추가합니다. 이 자습서에서는 JavaScript SDK 버전 3.0을 사용합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Cosmos DB 계정 만들기
> * 새 Node.js 애플리케이션 만들기
> * Azure Cosmos DB에 애플리케이션 연결
> * 애플리케이션을 실행하고 Azure에 배포

## <a name="prerequisites"></a><a name="prerequisites"></a>필수 조건

이 문서의 지침을 따르기 전에, 다음 리소스가 있는지 확인합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] 버전 6.10 이상.
* [Express 생성기](https://www.expressjs.com/starter/generator.html)(`npm install express-generator -g`를 통해 Express 설치 가능)
* 로컬 워크스테이션에 [Git][Git]을 설치합니다.

## <a name="create-an-azure-cosmos-db-account"></a><a name="create-account"></a>Azure Cosmos DB 계정 만들기
Azure Cosmos DB 계정을 만들어 시작해 보겠습니다. 계정이 이미 있거나 이 자습서에서 Azure Cosmos DB 에뮬레이터를 사용하는 경우 [2단계: 새 Node.js 애플리케이션 만들기](#create-new-app)로 건너뛸 수 있습니다.

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="create-new-app"></a>새 Node.js 애플리케이션 만들기
이제 Express 프레임워크를 사용하여 기본 Hello World Node.js 프로젝트를 만드는 방법을 알아보겠습니다.

1. Node.js 명령 프롬프트와 같이 줄겨찾는 터미널을 엽니다.

1. 새 애플리케이션을 저장하려는 디렉터리로 이동합니다.

1. Express 생성기를 사용해서 **todo** 라는 새로운 애플리케이션을 생성합니다.

   ```bash
   express todo
   ```

1. **todo** 디렉터리를 열고 종속 요소를 설치합니다.

   ```bash
   cd todo
   npm install
   ```

1. 새 애플리케이션을 실행합니다.

   ```bash
   npm start
   ```

1. 브라우저에서 `http://localhost:3000`으로 이동하여 새 애플리케이션을 확인할 수 있습니다.
   
   :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-express.png" alt-text="Node.js 알아보기 - 브라우저 창에 표시된 Hello World 애플리케이션의 스크린샷":::

   터미널 창에서 Ctrl+C를 사용하여 애플리케이션을 중지하고, **y** 를 선택하여 일괄 작업을 종료합니다.

## <a name="install-the-required-modules"></a><a name="install-modules"></a>필요한 모듈 설치

**package.json** 파일은 프로젝트 루트에 생성되는 파일 중 하나입니다. 이 파일에는 Node.js 애플리케이션에 필요한 추가 모듈의 목록이 들어 있습니다. 이 애플리케이션을 Azure에 배포할 때, 애플리케이션을 지원하려면 어떤 모듈을 Azure에 설치해야 하는지 이 파일을 사용하여 확인합니다. 이 자습서에서는 다음 두 패키지를 더 설치합니다.

1. npm을 통해 **\@azure/cosmos** 모듈을 설치합니다. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="connect-to-database"></a>Azure Cosmos DB에 Node.js 애플리케이션 연결
초기 설치 및 구성을 마쳤으니, 다음으로 todo 애플리케이션이 Azure Cosmos DB와 통신하는 데 필요한 코드를 작성하겠습니다.

### <a name="create-the-model"></a>모델 만들기
1. 프로젝트의 루트 디렉터리에서 **models** 라는 새 디렉터리를 만듭니다.  

2. **models** 디렉터리에서 **taskDao.js** 라는 새 파일을 만듭니다. 이 파일에는 데이터베이스와 컨테이너를 만드는 데 필요한 코드가 포함되어 있습니다. 또한 Azure Cosmos DB에서 작업을 읽고, 업데이트하고, 만들고, 찾는 메서드를 정의합니다. 

3. 다음 코드를 **taskDao.js** 파일에 복사합니다.

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. **taskDao.js** 파일을 저장하고 닫습니다.  

### <a name="create-the-controller"></a>컨트롤러 만들기

1. 프로젝트의 **routes** 디렉터리에서 **tasklist.js** 라는 새 파일을 만듭니다.  

2. 아래 코드를 **tasklist.js** 에 추가합니다. 이 코드는 **tasklist.js** 에서 사용되는 CosmosClient 및 async 모듈을 로드합니다. 또한 **TaskList** 클래스를 정의합니다. 이 클래스는 앞에서 정의한 **TaskDao** 개체의 인스턴스로 전달됩니다.
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. **tasklist.js** 파일을 저장하고 닫습니다.

### <a name="add-configjs"></a>config.js 추가

1. 프로젝트 디렉터리의 루트에서 **config.js** 라는 새 파일을 만듭니다. 

2. 다음 코드를 **config.js** 파일에 추가합니다. 이 코드는 애플리케이션에 필요한 구성 설정 및 값을 정의합니다.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. **config.js** 파일에서 [Azure Portal](https://portal.azure.com)에 있는 Azure Cosmos DB 계정의 [키] 페이지에 있는 값을 사용하여 HOST 및 AUTH_KEY 값을 업데이트합니다. 

4. **config.js** 파일을 저장하고 닫습니다.

### <a name="modify-appjs"></a>app.js 수정

1. 프로젝트 디렉터리에서 **app.js** 파일을 엽니다. 이 파일은 이전에 Express 웹 애플리케이션을 만들 때 생성되었습니다.  

2. 다음 코드를 **app.js** 파일에 추가합니다. 이 코드는 사용할 구성 파일을 정의하고, 다음 섹션에서 사용할 값을 일부 변수에 로드합니다. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. 마지막으로, **app.js** 파일을 저장하고 닫습니다.

## <a name="build-a-user-interface"></a><a name="build-ui"></a>사용자 인터페이스 작성

이제 사용자가 애플리케이션과 상호 작용할 수 있도록 사용자 인터페이스를 빌드하겠습니다. 이전 섹션에서 만든 Express 애플리케이션은 **Jade** 를 보기 엔진으로 사용합니다.

1. **views** 디렉터리의 **layout.jade** 파일은 다른 **.jade** 파일에 대한 전역 템플릿으로 사용됩니다. 이 단계에서는 웹 사이트를 디자인하는 데 사용되는 도구 키트인 Twitter Bootstrap을 사용하도록 수정합니다.  

2. **views** 폴더에 있는 **layout.jade** 파일을 열고 콘텐츠를 다음 코드로 바꿉니다.

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    이 코드는 애플리케이션에 대한 일부 HTML을 렌더링하도록 **Jade** 엔진에 지시하고, 콘텐츠 페이지의 레이아웃을 제공할 수 있는 **content** 라는 **block** 을 만듭니다. **layout.jade** 파일을 저장하고 닫습니다.

3. 이제 애플리케이션에 사용되는 보기인 **index.jade** 파일을 열고, 파일 콘텐츠를 다음 코드로 바꿉니다.

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

이 코드는 레이아웃을 확장하고, 앞에서 **layout.jade** 파일에서 본 **content** 자리 표시자의 콘텐츠를 제공합니다. 이 레이아웃에서 HTML 양식 2개를 만들었습니다.

첫 번째 양식에는 데이터에 대한 테이블 및 컨트롤러의 **/completeTask** 메서드에 게시하여 항목을 업데이트할 수 있게 해주는 단추 및 데이터 테이블이 포함됩니다.
    
두 번째 양식에는 컨트롤러의 **/addtask** 메서드에 게시하여 새 항목을 만들 수 있게 해주는 단추와 2개의 입력 필드가 포함됩니다. 이것만 있으면 애플리케이션이 작동합니다.

## <a name="run-your-application-locally"></a><a name="run-app-locally"></a>로컬로 애플리케이션 실행

애플리케이션을 빌드했으므로 다음 단계를 사용하여 로컬로 실행할 수 있습니다.  

1. 로컬 머신에서 애플리케이션을 테스트하려면 터미널에서 `npm start`를 실행하여 애플리케이션을 시작한 다음, `http://localhost:3000` 브라우저 페이지를 새로 고칩니다. 이제 페이지가 다음 스크린샷과 비슷하게 표시됩니다.
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png" alt-text="브라우저 창에 표시된 MyTodo List 애플리케이션의 스크린샷":::

    > [!TIP]
    > layout.jade 파일 또는 index.jade 파일에서 들여쓰기에 대한 오류가 발생하면 두 파일의 처음 두 줄을 공백 없이 왼쪽에 맞춥니다. 처음 두 줄 앞에 공백이 있으면 이를 제거하고, 두 파일을 모두 저장한 다음, 브라우저 창을 새로 고칩니다. 

2. [항목], [항목 이름] 및 [범주] 필드를 사용하여 새 작업을 입력한 다음, **항목 추가** 를 선택합니다. 이러한 속성이 포함된 문서가 Azure Cosmos DB에 생성됩니다. 

3. 페이지가 업데이트되어 새로 만든 항목을 ToDo 목록에 표시합니다.
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png" alt-text="ToDo 목록에 새 항목이 포함된 애플리케이션의 스크린샷":::

4. 작업을 완료하려면 [완료] 열의 확인란을 선택한 다음, **작업 업데이트** 를 선택합니다. 그러면 이미 만든 문서가 업데이트되고 보기에서 제거됩니다.

5. 애플리케이션을 중지하려면 터미널 창에서 Ctrl+C를 누르고 **Y** 를 선택하여 배치 작업을 종료합니다.

## <a name="deploy-your-application-to-web-apps"></a><a name="deploy-app"></a>Web Apps에 애플리케이션 배포

애플리케이션이 로컬에서 성공하면 다음 단계를 사용하여 Azure에 배포할 수 있습니다.

1. 아직 Web Apps 애플리케이션에 git 리포지토리를 사용하도록 설정하지 않은 경우 이를 사용하도록 설정합니다.

2. Web Apps 애플리케이션을 git remote로 추가합니다.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. 원격에 푸시하여 애플리케이션을 배포합니다.
   
   ```bash
   git push azure main
   ```

4. 몇 초 후 웹 애플리케이션이 게시되고 브라우저에서 시작됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이러한 리소스가 더 이상 필요하지 않은 경우 리소스 그룹, Azure Cosmos DB 계정 및 모든 관련 리소스를 삭제할 수 있습니다. 이렇게 하려면 Azure Cosmos DB 계정에 사용한 리소스 그룹을 선택하고, **삭제** 를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Xamarin 및 Azure Cosmos DB를 사용하여 모바일 애플리케이션 빌드](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app