---
title: '빠른 시작: Node.js MongoDB 앱을 Azure Cosmos DB에 연결'
description: 이 빠른 시작은 Node.js로 작성된 기존 MongoDB 앱을 Azure Cosmos DB 데이터베이스에 연결하는 방법을 보여 줍니다.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli, mode-other
ms.openlocfilehash: 42bccf780eaf5e71272c102cf958ef656f7d67e5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133053184"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>빠른 시작: 기존 MongoDB Node.js 웹앱을 Azure Cosmos DB로 마이그레이션 
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

이 빠른 시작에서는 Azure Cloud Shell을 사용하고 GitHub에서 복제된 MEAN(MongoDB, Express, Angular 및 node.js) 앱을 사용하여 Azure Cosmos DB for Mongo DB API 계정을 만들고 관리합니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)] 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용해 보세요](https://azure.microsoft.com/try/cosmosdb/). 연결 문자열 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`와 함께 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 사용할 수도 있습니다.

- [Node.js](https://nodejs.org/) 및 Node.js에 대한 실무 지식.

- [Git](https://git-scm.com/downloads)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.


## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 샘플 리포지토리에는 기본 [MEAN.js](https://meanjs.org/) 애플리케이션이 들어 있습니다.

1. 명령 프롬프트를 열고, git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>애플리케이션 실행

Node.js로 작성된 이 MongoDB 앱은 MongoDB 클라이언트를 지원하는 Azure Cosmos DB 데이터베이스에 연결됩니다. 즉, 애플리케이션에서 데이터를 인식하지 못하므로 데이터가 Azure Cosmos DB 데이터베이스에 저장됩니다.

필요한 패키지를 설치하고 애플리케이션을 시작합니다.

```bash
cd mean
npm install
npm start
```
애플리케이션은 MongoDB 원본에 연결을 시도했다가 실패하고, 계속 진행하다가, 출력이 “[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”을 반환하면 애플리케이션을 종료합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

설치된 Azure CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다. Azure Cloud Shell을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Azure Cosmos DB 모듈 추가

설치된 Azure CLI를 사용하는 경우 `az` 명령을 실행하여 `cosmosdb` 구성 요소가 이미 설치되어 있는지 확인합니다. 기본 명령 목록에 `cosmosdb`가 있으면 다음 명령으로 진행합니다. Azure Cloud Shell을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

`cosmosdb`가 기본 명령 목록에 없으면 [Azure CLI](/cli/azure/install-azure-cli)를 다시 설치합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create)를 사용하여 [리소스 그룹](../../azure-resource-manager/management/overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 스토리지 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 서유럽 지역의 리소스 그룹을 만듭니다. 리소스 그룹에 고유한 이름을 선택합니다.

Azure Cloud Shell을 사용하는 경우 **시도** 를 선택하고, 화면의 지시에 따라 로그인한 다음, 명령 프롬프트에 명령을 복사합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

[az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 Cosmos 계정을 만듭니다.

다음 명령에서 `<cosmosdb-name>` 자리 표시자를 표시하는 고유한 Cosmos 계정 이름을 바꿉니다. 이 고유한 이름은 Cosmos DB 엔드포인트(`https://<cosmosdb-name>.documents.azure.com/`)의 일부로 사용되므로, Azure의 모든 Cosmos 계정에서 고유해야 합니다. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` 매개 변수는 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

Azure Cosmos DB 계정을 만든 경우 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

> [!NOTE]
> 이 예서는 Azure CLI 출력 형식으로 JSON을 사용합니다(기본값). 다른 출력 형식을 사용하려면 [Azure CLI 명령에 대한 출력 형식](/cli/azure/format-output-azure-cli)을 참조하세요.

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>데이터베이스에 Node.js 애플리케이션 연결

이 단계에서는 방금 만든 Azure Cosmos DB 데이터베이스 계정에 MEAN.js 샘플 애플리케이션을 연결합니다. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js 애플리케이션에 연결 문자열 구성

MEAN.js 리포지토리에서 `config/env/local-development.js`를 엽니다.

이 파일 내용을 다음 코드로 바꿉니다. 또한 두 `<cosmosdb-name>` 자리 표시자를 해당하는 Cosmos 계정 이름으로 바꿔야 합니다.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>키 검색

Cosmos 데이터베이스에 연결하려면 데이터베이스 키가 필요합니다. [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) 명령을 사용하여 기본 키를 검색합니다.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI는 다음 예와 유사한 정보를 출력합니다. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

`primaryMasterKey`의 값을 복사합니다. `local-development.js`에 있는 `<primary_master_key>`에 덮어씁니다.

변경 내용을 저장합니다.

### <a name="run-the-application-again"></a>애플리케이션을 다시 실행합니다.

`npm start`을 다시 실행합니다. 

```bash
npm start
```

이제 콘솔 메시지에서는 개발 환경이 실행된다고 알려 주어야 합니다. 

브라우저에서 `http://localhost:3000`으로 이동합니다. 맨 위 메뉴에서 **등록** 을 선택하여 두 개의 더미 사용자를 만듭니다. 

MEAN.js 샘플 애플리케이션은 데이터베이스에 사용자 데이터를 저장합니다. 성공해서 MEAN.js가 생성된 사용자로 자동 로그인하면 Azure Cosmos DB 연결이 작동합니다. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="MEAN.js가 MongoDB 연결에 성공":::

## <a name="view-data-in-data-explorer"></a>데이터 탐색기에서 데이터 보기

Cosmos 데이터베이스에 저장된 데이터는 Azure Portal에서 살펴보고 쿼리할 수 있습니다.

이전 단계에서 만든 사용자 데이터를 보고 쿼리하고 사용하려면 웹 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.

맨 위 검색 상자에 **Azure Cosmos DB** 를 입력합니다. Cosmos 계정 블레이드가 열리면 Cosmos 계정을 선택합니다. 왼쪽 탐색에서 **데이터 탐색기** 를 선택합니다. 컬렉션 창에서 컬렉션을 확장하면 컬렉션에서 문서를 보고, 데이터를 쿼리하고 저장된 프로시저, 트리거 및 UDF를 만들고 실행합니다. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Azure Portal의 데이터 탐색기":::


## <a name="deploy-the-nodejs-application-to-azure"></a>Azure에 Node.js 애플리케이션 배포

이 단계에서는 Node.js 애플리케이션을 Cosmos DB에 배포합니다.

개발 환경을 위해 이전에 변경한 구성 파일을 알 수 있습니다(`/config/env/local-development.js`). 애플리케이션을 App Service에 배포하면 기본적으로 프로덕션 환경에서 실행합니다. 이제 해당 구성 파일을 동일하게 변경해야 합니다.

MEAN.js 리포지토리에서 `config/env/production.js`를 엽니다.

`db` 개체에서 다음 예에 표시된 것과 같이 `uri` 값을 바꿉니다. 전과 같이 자리 표시자를 바꿔야 합니다.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Cosmos DB 요구 사항으로 인해 `ssl=true` 옵션이 중요합니다. 자세한 내용은 [연결 문자열 요구 사항](connect-mongodb-account.md#connection-string-requirements)을 참조하세요.
>
>

터미널에서 Git에 모든 변경 내용을 커밋합니다. 두 명령을 복사하여 함께 실행할 수 있습니다.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cloud Shell을 사용하여 Azure Cosmos DB MongoDB API 계정을 만들고, MEAN .js 앱을 만들고 실행하여 사용자를 계정에 추가하는 방법을 알아보았습니다. 이제 사용자의 Azure Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다.

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
  * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
  * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
