---
title: Azure Cosmos DB용 SQL API에 대한 Node.js 자습서
description: SQL API를 사용하여 Azure Cosmos DB에 연결하고 쿼리하는 방법을 보여주는 Node.js 자습서
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 9283f68aee0ad433257ae461f1ea245b673b183e
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117725"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>자습서: JavaScript SDK를 사용하여 Azure Cosmos DB SQL API 데이터를 관리하는 Node.js 콘솔 앱 빌드
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [비동기 Java](./create-sql-api-java.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

개발자는 NoSQL 문서 데이터를 사용하는 애플리케이션을 가지고 있을 수도 있습니다. Azure Cosmos DB의 SQL API 계정을 사용하여 이 문서 데이터를 저장하고 액세스할 수 있습니다. 이 자습서에서는 Azure Cosmos DB 리소스를 만들고 쿼리하는 Node.js 콘솔 애플리케이션을 빌드하는 방법을 보여 줍니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Cosmos DB 계정 만들기 및 연결
> * 애플리케이션 설치
> * 데이터베이스 만들기
> * 컨테이너를 만듭니다.
> * 컨테이너에 항목 추가
> * 항목, 컨테이너 및 데이터베이스에 대한 기본 작업 수행

## <a name="prerequisites"></a>필수 구성 요소 

다음 리소스가 있는지 확인합니다.

* 활성 Azure 계정. 아직 구독하지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 이상이 필요합니다.

## <a name="create-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

Azure Cosmos DB 계정을 만들어 보겠습니다. 사용하려는 계정이 이미 있는 경우 [Node.js 애플리케이션 설치](#SetupNode)로 건너뛸 수 있습니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 [Azure Cosmos DB 에뮬레이터](../local-emulator.md)의 단계에 따라 에뮬레이터를 설정하고 [Node.js 애플리케이션 설정](#SetupNode)으로 건너뜁니다. 

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Node.js 애플리케이션 설치

애플리케이션을 빌드하는 코드를 작성하기 전에 앱에 대한 프레임워크를 빌드할 수 있습니다. 다음 단계를 실행하여 프레임워크 코드가 있는 Node.js 애플리케이션을 설치합니다.

1. 자주 사용하는 터미널을 엽니다.
2. Node.js 애플리케이션을 저장하려는 폴더 또는 디렉터리를 찾습니다.
3. 다음 명령을 사용하여 빈 JavaScript 파일을 만듭니다.

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. `package.json` 파일을 만들고 초기화합니다. 다음 명령을 사용합니다.
   * ```npm init -y```

5. npm을 통해 @azure/cosmos 모듈을 설치합니다. 다음 명령을 사용합니다.
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>앱의 구성 설정

이제 앱이 있으므로 Azure Cosmos DB와 통신할 수 있는지 확인합니다. 다음 단계와 같이 몇 가지 구성 설정을 업데이트하면 앱에서 Azure Cosmos DB와 통신하도록 설정할 수 있습니다.

1. 즐겨찾는 텍스트 편집기에서 *config.js* 파일을 엽니다.

1. 다음 코드 조각을 복사하여 *config.js* 파일에 붙여넣고 `endpoint` 및 `key` 속성을 Azure Cosmos DB 엔드포인트 URI 및 기본 키로 설정합니다. 데이터베이스, 컨테이너 이름은 **작업** 및 **항목** 으로 설정되어 있습니다. 이 애플리케이션에 사용할 파티션 키는 **/category** 입니다.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   [Azure Portal](https://portal.azure.com)의 **키** 창에서 엔드포인트 및 주요 세부 정보를 찾을 수 있습니다.

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="Azure Portal에서 키 가져오기 스크린샷":::

JavaScript SDK는 일반 용어인 *컨테이너* 및 *항목* 을 사용합니다. 컨테이너는 컬렉션, 그래프 또는 테이블입니다. 항목은 문서, 에지/꼭짓점 또는 행이며, 컨테이너 내부의 콘텐츠입니다. 이전 코드 조각에서 `module.exports = config;` 코드는 *app.js* 파일 내에서 참조할 수 있도록 구성 개체를 내보내는 데 사용됩니다.

## <a name="create-a-database-and-a-container"></a>데이터베이스 및 컨테이너 만들기

1. 즐겨찾는 텍스트 편집기에서 *databaseContext.js* 파일을 엽니다.

1. 다음 코드를 복사하여 *databaseContext.js* 파일에 붙여넣습니다. 이 코드는 Azure Cosmos 계정에 아직 존재하지 않는 경우 "Tasks", "Items" 데이터베이스 및 컨테이너를 만드는 함수를 정의합니다.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   데이터베이스는 여러 컨테이너에 분할된 항목의 논리적 컨테이너입니다. `createIfNotExists` 또는 **Databases** 클래스의 create 함수를 사용하여 데이터베이스를 만듭니다. 컨테이너는 SQL API의 경우 JSON 문서 항목으로 구성됩니다. `createIfNotExists` 또는 **Containers** 클래스의 create 함수를 사용하여 컨테이너를 만듭니다. 컨테이너를 만든 후에는 데이터를 저장하고 쿼리할 수 있습니다.

   > [!WARNING]
   > 컨테이너를 만드는 것은 가격 책정에 영향을 줍니다. 예상되는 결과를 확인하려면 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 방문하세요.

## <a name="import-the-configuration"></a>구성 가져오기

1. 즐겨찾는 텍스트 편집기에서 *app.js* 파일을 엽니다.

1. 아래 코드를 복사하여 붙여넣어 이전 단계에서 정의한 `@azure/cosmos` 모듈, 구성 및 databaseContext를 가져옵니다. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Azure Cosmos 계정에 연결

*app.js* 파일에서 이전에 저장된 엔드포인트와 키를 사용하여 새 CosmosClient 개체를 생성하려면 다음 코드를 복사하여 붙여넣습니다.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> **Cosmos DB 에뮬레이터** 에 연결하는 경우 노드 프로세스에 대한 TLS 확인을 사용하지 않도록 설정합니다.
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Azure Cosmos DB 클라이언트를 시작하는 코드가 생겼으니, Azure Cosmos DB 리소스를 작업하는 방법을 알아보겠습니다.

## <a name="query-items"></a><a id="QueryItem"></a>쿼리 항목

Azure Cosmos DB는 각 컨테이너에 저장된 JSON 항목에 대해 다양한 쿼리를 지원합니다. 다음 샘플 코드는 컨테이너의 항목에 대해 실행할 수 있는 쿼리를 보여줍니다. `Items` 클래스의 쿼리 함수를 사용하여 항목을 쿼리할 수 있습니다. 다음 코드를 *app.js* 파일에 추가하여 Azure Cosmos 계정에서 항목을 쿼리합니다.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>항목 만들기

항목은 `Items` 클래스의 create 함수를 사용하여 만들 수 있습니다. SQL API를 사용하는 경우 항목은 사용자 정의(임의) JSON 콘텐츠인 문서로 프로젝션됩니다. 이 자습서에서는 작업 데이터베이스 내에 새 항목을 만듭니다.

1. app.js 파일에서 항목 정의를 정의합니다.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. 다음 코드를 추가하여 이전에 정의한 항목을 만듭니다.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>항목 업데이트

Azure Cosmos DB는 항목의 콘텐츠 바꾸기를 지원합니다. 다음 코드를 복사하여 *app.js* 파일에 붙여넣습니다. 이 코드는 컨테이너에서 항목을 가져오고 *isComplete* 필드를 true로 업데이트합니다.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>항목 삭제

Azure Cosmos DB는 JSON 항목 삭제를 지원합니다. 다음 코드는 ID를 기준으로 항목을 가져오고 삭제하는 방법을 보여줍니다. 다음 코드를 복사하여 *app.js* 파일에 붙여넣습니다.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Node.js 애플리케이션 실행

전체적으로 코드는 다음과 비슷할 것입니다.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다.

```bash 
node app.js
```

시작한 앱의 출력이 표시됩니다. 출력은 아래의 예제 텍스트와 일치해야 합니다.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>전체 Node.js 자습서 솔루션 다운로드

이 자습서의 단계를 완료할 시간이 없거나 코드를 다운로드하려는 경우 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started )에서 가져올 수 있습니다.

이 문서의 모든 코드가 포함된 시작 솔루션을 실행하려면 다음이 필요합니다.

* [Azure Cosmos DB 계정][create-account].
* GitHub에서 제공하는 [시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) 솔루션.

npm을 통해 프로젝트의 종속성을 설치합니다. 다음 명령을 사용합니다.

* ```npm install``` 

다음으로, ```config.js``` 파일에서 config.endpoint 및 config.key 값을 [3단계: 앱의 구성 설정](#Config)에서 설명한 대로 업데이트합니다.  

그런 다음, 터미널에서 ```app.js``` 파일을 찾아서 다음 명령을 실행합니다.  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>리소스 정리

이러한 리소스가 더 이상 필요하지 않은 경우 리소스 그룹, Azure Cosmos DB 계정 및 모든 관련 리소스를 삭제할 수 있습니다. 이렇게 하려면 Azure Cosmos DB 계정에 사용한 리소스 그룹을 선택하고, **삭제** 를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB 계정 모니터링](../monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account