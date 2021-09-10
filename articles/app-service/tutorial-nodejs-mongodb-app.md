---
title: '자습서: MongoDB를 사용하는 Node.js 앱'
description: Azure(Cosmos DB)의 MongoDB 데이터베이스에 연결하여 Azure에서 작동하며 Node.js 앱을 Azure에서 작동하는 방법을 알아봅니다. Sails.js 및 Angular 12는 자습서에서 사용됩니다.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/13/2021
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 7338e5c8ef1e3f1fce6242b62153dd492de9e3a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746017"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>자습서: Azure에서 Node.js 및 MongoDB 앱 빌드

::: zone pivot="platform-windows"  

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Windows의 App Service에서 Node.js 앱을 만들고 MongoDB 데이터베이스에 연결하는 방법을 보여줍니다. 완료되면 MEAN 애플리케이션(MongoDB, Express, AngularJS 및 Node.js)이 [Azure App Service](overview.md)에서 실행됩니다. 샘플 애플리케이션은 [Sails.js](https://sailsjs.com/) 및 [Angular 12](https://angular.io/)의 조합을 사용합니다.

::: zone-end

::: zone pivot="platform-linux"


[Azure App Service](overview.md)는 Linux 운영 체제를 사용하여 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Linux의 App Service에서 Node.js 앱을 만들고 MongoDB 데이터베이스에 로컬로 연결한 다음, Azure Cosmos DB의 API for MongoDB에 있는 데이터베이스에 배포하는 방법을 보여줍니다. 완료되면 MEAN 애플리케이션(MongoDB, 기본, AngularJS 및 Node.js)이 Linux의 App Service에서 실행됩니다. 샘플 애플리케이션은 [Sails.js](https://sailsjs.com/) 및 [Angular 12](https://angular.io/)의 조합을 사용합니다.

::: zone-end

![Azure App Service에서 실행 중인 MEAN 앱](./media/tutorial-nodejs-mongodb-app/run-in-azure.png)

학습할 내용:

> [!div class="checklist"]
> * Azure에서 MongoDB 데이터베이스 만들기
> * Node.js 앱을 MongoDB에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

- [Git 설치](https://git-scm.com/)
- [Node.js 및 NPM 설치](https://nodejs.org/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="create-local-nodejs-app"></a>로컬 Node.js 앱 만들기

이 단계에서는 로컬 Node.js 프로젝트를 설정합니다.

### <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/mean-todoapp.git
```

> [!NOTE]
> 샘플 앱을 만드는 방법에 관한 내용은 [https://github.com/Azure-Samples/mean-todoapp](https://github.com/Azure-Samples/mean-todoapp)을 참조하세요.

### <a name="run-the-application"></a>애플리케이션 실행

다음 명령을 실행하여 필요한 패키지를 설치하고 애플리케이션을 시작합니다.

```bash
cd mean-todoapp
npm install
node app.js --alter
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

<pre>
debug: -------------------------------------------------------
debug: :: Fri Jul 09 2021 13:10:34 GMT+0200 (Central European Summer Time)

debug: Environment : development
debug: Port        : 1337
debug: -------------------------------------------------------
</pre>

브라우저에서 `http://localhost:1337` 으로 이동합니다. 몇 가지 todo 항목을 추가합니다.

MEAN 샘플 애플리케이션은 데이터베이스에 사용자 데이터를 저장합니다. 기본적으로 디스크 기반 개발 데이터베이스를 사용합니다. 사용자가 Todo 항목을 만들고 볼 수 있는 경우 앱이 데이터를 읽고 쓰는 것입니다.

![MEAN 앱이 로드됨](./media/tutorial-nodejs-mongodb-app/run-locally.png)

언제든지 Node.js를 중지하려면 터미널에서 `Ctrl+C`를 입력합니다. 

## <a name="create-production-mongodb"></a>프로덕션 MongoDB 만들기

이 단계에서는 Azure에 MongoDB 데이터베이스를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

MongoDB의 경우 이 자습서에서는 [Azure Cosmos DB](../cosmos-db/index.yml)를 사용합니다. Cosmos DB는 MongoDB 클라이언트 연결을 지원합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기

> [!NOTE]
> 이 자습서에서는 사용자의 Azure 구독에 Azure Cosmos DB 데이터베이스를 만드는 비용이 발생합니다. 7일 동안 체험판 Azure Cosmos DB 계정을 사용하려면 [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/) 경험을 사용하면 됩니다. MongoDB 타일에서 **만들기** 단추를 클릭하고 Azure에서 체험판 MongoDB 데이터베이스를 만들면 됩니다. 데이터베이스를 만든 후에는 포털에서 **연결 문자열** 로 이동하여 이 자습서의 뒷부분에서 사용할 Azure Cosmos DB 연결 문자열을 검색합니다.
>

Cloud Shell에서 [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 Cosmos DB 계정을 만듭니다.

다음 명령에서 *\<cosmosdb-name>* 자리 표시자를 고유한 Cosmos DB 이름으로 바꿉니다. 이 이름은 Cosmos DB 엔드포인트(`https://<cosmosdb-name>.documents.azure.com/`)의 일부로 사용되므로 Azure의 모든 Cosmos DB 계정에서 고유해야 합니다. 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-50자 사이여야 합니다.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

*--kind MongoDB* 매개 변수는 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

Cosmos DB 계정을 만든 경우 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

<pre>
{
  "apiProperties": {
    "serverVersion": "3.6"
  },
  "backupPolicy": {
    "periodicModeProperties": {
      "backupIntervalInMinutes": 240,
      "backupRetentionIntervalInHours": 8,
      "backupStorageRedundancy": "Geo"
    },
    "type": "Periodic"
  },
  "capabilities": [
    {
      "name": "EnableMongo"
    }
  ],
  "connectorOffer": null,
  "consistencyPolicy": {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "cors": [],
  "databaseAccountOfferType": "Standard",
  "defaultIdentity": "FirstPartyIdentity",
  "disableKeyBasedMetadataWriteAccess": false,
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>프로덕션 MongoDB에 앱 연결

이 단계에서는 MongoDB 연결 문자열을 사용하여 샘플 애플리케이션을 방금 만든 Cosmos DB 데이터베이스에 연결합니다. 

### <a name="retrieve-the-database-key"></a>데이터베이스 키 검색

Cosmos DB 데이터베이스에 연결하려면 데이터베이스 키가 필요합니다. Cloud Shell에서 [`az cosmosdb keys list`](/cli/azure/cosmosdb#az_cosmosdb_keys_list) 명령을 사용하여 기본 키를 검색합니다.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup
```

Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

`primaryMasterKey`의 값을 복사합니다. 이 정보는 다음 단계에서 필요합니다.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-sample-application"></a>샘플 애플리케이션에서 연결 문자열 구성

로컬 리포지토리의 _config/datastores.js_ 에서 기존 콘텐츠를 다음 코드로 바꾸고 변경 내용을 저장합니다.

```javascript
module.exports.datastores = {
  default: {
    adapter: 'sails-mongo',
    url: process.env.MONGODB_URI,
    ssl: true,
  },
};
```

[Cosmos DB에서 TLS/SSL을 요구](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)하므로 `ssl: true` 옵션이 필요합니다. `url`은 다음에 설정할 환경 변수로 설정됩니다. 

터미널에서 `MONGODB_URI` 환경 변수를 설정합니다. 두 개의 \<cosmosdb-name> 자리 표시자를 Cosmos DB 데이터베이스 이름으로 바꾸고 \<cosmosdb-key> 자리 표시자를 이전 단계에서 복사한 키로 바꿔야 합니다.

```bash
export MONGODB_URI=mongodb://<cosmosdb-name>:<cosmosdb-key>@<cosmosdb-name>.documents.azure.com:10250/todoapp
```

> [!NOTE]
> 이 연결 문자열은 [Sails.js 설명서](https://sailsjs.com/documentation/reference/configuration/sails-config-datastores#?the-connection-url)에 정의된 형식을 따릅니다.

### <a name="test-the-application-with-mongodb"></a>MongoDB를 사용하여 애플리케이션 테스트

로컬 터미널 창에서 `node app.js --alter`를 다시 실행합니다.

```bash
node app.js --alter
```

`http://localhost:1337`으로 다시 이동합니다. 사용자가 Todo 항목을 만들고 볼 수 있는 경우 앱이 Azure에서 Cosmos DB 데이터베이스를 사용하여 데이터를 읽고 쓰는 것입니다. 

터미널에서 `Ctrl+C`를 입력하여 Node.js를 중지합니다. 

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포

이 단계에서는 MongoDB에 연결된 Node.js 애플리케이션을 Azure App Service에 배포합니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

::: zone pivot="platform-windows"  

Cloud Shell에서 [`az appservice plan create`](/cli/azure/appservice/plan) 명령을 사용하여 App Service 계획을 만듭니다.

다음 예제에서는 **B1** 가격 책정 계층에서 `myAppServicePlan`이라는 App Service 요금제를 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

App Service 계획을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

<pre>
{ 
  "freeOfferExpirationTime": null,
  "geoRegion": "UK West",
  "hostingEnvironmentProfile": null,
  "hyperV": false,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "isSpot": false,
  "isXenon": false,
  "kind": "app",
  "location": "ukwest",
  "maximumElasticWorkerCount": 1,
  "maximumNumberOfWorkers": 0,
  &lt; JSON data removed for brevity. &gt;
} 
</pre>

::: zone-end

::: zone pivot="platform-linux"

Cloud Shell에서 [`az appservice plan create`](/cli/azure/appservice/plan) 명령을 사용하여 App Service 계획을 만듭니다.

<!-- [!INCLUDE [app-service-plan](app-service-plan.md)] -->

다음 예제에서는 **B1** 가격 책정 계층에서 `myAppServicePlan`이라는 App Service 요금제를 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
```

App Service 계획을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

<pre>
{ 
  "freeOfferExpirationTime": null,
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>웹앱 만들기

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>환경 변수 구성

샘플 애플리케이션은 `config/datastores.js`에서 `MONGODB_URI` 환경 변수를 사용하도록 구성되어 있습니다. App Service에서 [앱 설정](configure-common.md#configure-app-settings)을 사용하여 이 변수를 삽입합니다. 

앱 설정을 지정하려면 Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용합니다. 

다음 예제에서는 Azure 앱에 `MONGODB_URI` 앱 설정을 구성합니다. *\<app-name>* , *\<cosmosdb-name>* 및 *\<cosmosdb-key>* 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI='mongodb://<cosmosdb-name>:<cosmosdb-key>@<cosmosdb-name>.documents.azure.com:10250/todoapp' DEPLOYMENT_BRANCH='main'
```

> [!NOTE]
> `DEPLOYMENT_BRANCH`는 App Service에 배포할 Git 분기를 배포 엔진에 알려 주는 특수 앱 설정입니다.

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

::: zone pivot="platform-windows"

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 318 bytes | 318.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '4eb0ca7190'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Creating app_offline.htm
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Copying file: 'package.json'
remote: Deleting app_offline.htm
remote: Looking for app.js/server.js under site root.
remote: Using start-up script app.js
remote: Generated web.config.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

> [!TIP]
> Git 배포 중에 배포 엔진은 빌드 자동화의 일부로 `npm install --production`을 실행합니다.
>
> - `package.json`에 정의된 대로 `postinstall` 스크립트는 `npm install`을 통해 선택되고 `ng build`를 실행하여 Angular에 대한 프로덕션 파일을 생성하고 [assets](https://sailsjs.com/documentation/concepts/assets) 폴더에 배포합니다.
> - `package.json`의 `scripts`는 `node_modules/.bin`에 설치된 도구를 사용할 수 있습니다. `npm install`은 `node_modules/.bin/ng`도 설치했기 때문에 이를 사용하여 Angular 클라이언트 파일을 배포할 수 있습니다. 이 npm 동작은 Azure App Service에서 똑같습니다.
> `package.json`의 `devDependencies` 아래 패키지는 설치되지 않습니다. 프로덕션 환경에서 필요한 모든 패키지를 `dependencies` 아래로 이동해야 합니다.
>
> 앱이 기본 자동화를 무시하고 사용자 지정 자동화를 실행해야 하는 경우 [Grunt/Bower/Gulp 실행](configure-language-nodejs.md#run-gruntbowergulp)을 참조하세요.

::: zone-end

::: zone pivot="platform-linux"

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 347 bytes | 347.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'f776be774a'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Operation performed by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: 
remote: Oryx Version: 0.2.20210420.1, Commit: 85c6e9278aae3980b86cb1d520aaad532c814ed7, ReleaseTagName: 20210420.1
remote: 
remote: Build Operation ID: |qwejn9R4StI=.5e8a3529_
remote: Repository Commit : f776be774a3ea8abc48e5ee2b5132c037a636f73
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/a6fcf811136739f145e0de3be82ff195bca7a68b/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   4f7e3ac..a6fcf81  main -> main
</pre>

> [!TIP]
> Git 배포 중에 배포 엔진은 빌드 자동화의 일부로 `npm install`을 실행합니다.
>
> - `package.json`에 정의된 대로 `postinstall` 스크립트는 `npm install`을 통해 선택되고 `ng build`를 실행하여 Angular에 대한 프로덕션 파일을 생성하고 [assets](https://sailsjs.com/documentation/concepts/assets) 폴더에 배포합니다.
> - `package.json`의 `scripts`는 `node_modules/.bin`에 설치된 도구를 사용할 수 있습니다. `npm install`은 `node_modules/.bin/ng`도 설치했기 때문에 이를 사용하여 Angular 클라이언트 파일을 배포할 수 있습니다. 이 npm 동작은 Azure App Service에서 똑같습니다.
> 빌드 자동화가 완료되면 완료된 전체 리포지토리는 앱이 호스트되는 `/home/site/wwwroot` 폴더에 복사됩니다.
>
> 앱이 기본 자동화를 무시하고 사용자 지정 자동화를 실행해야 하는 경우 [Grunt/Bower/Gulp 실행](configure-language-nodejs.md#run-gruntbowergulp)을 참조하세요.

::: zone-end

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기 

웹 브라우저를 사용하여 배포된 앱으로 이동합니다. 

```bash 
https://<app-name>.azurewebsites.net 
``` 

사용자가 브라우저에서 todo 항목을 만들고 볼 수 있는 경우 Azure의 샘플 앱은 MongoDB(Cosmos DB) 데이터베이스에 연결된 것입니다. 

![Azure App Service에서 실행 중인 MEAN 앱](./media/tutorial-nodejs-mongodb-app/run-in-azure.png)

**축하합니다.** Azure App Service에서 데이터 기반 Node.js 응용 프로그램을 실행합니다.

## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 `Todo` 데이터 모델을 변경하고 변경 내용을 Azure에 게시합니다.

### <a name="update-the-server-side-model"></a>서버 쪽 모델 업데이트

Sails.js에서 서버 쪽 모델과 API 코드를 변경하는 작업은 데이터 모델을 변경하는 것만큼 간단합니다. 기본적으로 모델에 대한 [일반적인 경로가 Sails.js에 이미 정의](https://sailsjs.com/documentation/concepts/blueprints/blueprint-routes#?restful-routes)되어 있기 때문입니다. 

로컬 리포지토리에서 _api/models/Todo.js_ 를 열고 `done` 특성을 추가합니다. 완료된 후의 스키마 코드는 다음과 유사합니다.

```javascript
module.exports = {

  attributes: {
    value: {type: 'string'},
    done: {type: 'boolean', defaultsTo: false}
  },

};
```

### <a name="update-the-client-code"></a>클라이언트 코드 업데이트

클라이언트 모델, HTML 템플릿 및 구성 요소 파일의 세 가지 파일을 수정해야 합니다. 

_client/src/app/todo.ts_ 를 열고 `done` 속성을 추가합니다. 작업이 완료되면 모델이 다음과 같이 표시됩니다.

```typescript
export class Todo {
    id!: String;
    value!: String;
    done!: Boolean;
}
```

_client/src/app/app.component.html_ 을 엽니다. 유일한 `<span>` 요소 바로 위에 다음 코드를 추가하여 각 todo 항목의 시작 부분에 확인란을 추가합니다.

```html
<input class="form-check-input me-2" type="checkbox" [checked]="todo.done" (click)="toggleDone(todo.id, i)" [disabled]="isProcessing">
```

_client/src/app/app.component.ts_ 를 엽니다. 마지막 닫는 중괄호(`}`) 바로 위에 다음 메서드를 삽입합니다. 메서드는 확인란이 클릭되고 서버 쪽 데이터를 업데이트할 때 위 템플릿 코드에서 호출됩니다.

```typescript
toggleDone(id:any, i:any) {
  console.log("Toggled checkbox for " + id);
  this.isProcessing = true;
  this.Todos[i].done = !this.Todos[i].done;
  this.restService.updateTodo(id, this.Todos[i])
  .subscribe((res) => {
      console.log('Data updated successfully!');
      this.isProcessing = false;
    }, (err) => {
      console.log(err);
      this.Todos[i].done = !this.Todos[i].done;
  });
}
```

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

로컬 터미널 창에서 `package.json`에 정의된 빌드 스크립트를 사용하여 업데이트된 Angular 클라이언트 코드를 컴파일합니다.

```bash
npm run build
```

다시 `node app.js --alter`를 사용하여 변경 내용을 테스트합니다. 서버 쪽 모델을 변경했으므로 `--alter` 플래그를 사용하면 `Sails.js`가 Cosmos DB 데이터베이스의 데이터 구조를 변경할 수 있습니다.

```bash
node app.js --alter
```

`http://localhost:1337`로 이동합니다. 이제 todo 항목 앞에 확인란이 표시됩니다. 확인란을 선택하거나 선택 취소하면 todo 항목이 완료되었음을 나타내도록 Azure의 Cosmos DB 데이터베이스가 업데이트됩니다.

![추가된 완료 데이터 및 UI](./media/tutorial-nodejs-mongodb-app/added-done.png)

터미널에서 `Ctrl+C`를 입력하여 Node.js를 중지합니다. 

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

로컬 터미널 창에서 Git의 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git commit -am "added done field"
git push azure main
```

`git push`가 완료되면 Azure 앱으로 이동하여 새 기능을 테스트해 봅니다.

![Azure에 게시된 모델 및 데이터베이스 변경 내용](media/tutorial-nodejs-mongodb-app/added-done-published.png)

이전에 문서를 추가했으면 그 문서를 지금도 볼 수 있습니다. Cosmos DB의 기존 데이터는 손실되지 않습니다. 또한 데이터 스키마가 업데이트되고 기존 데이터는 그대로 유지됩니다.

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림 

::: zone pivot="platform-windows"  

Node.js 애플리케이션이 Azure App Service에서 실행되는 동안 콘솔 로그를 터미널에 파이프할 수 있습니다. 이 방법으로 애플리케이션 오류를 디버깅하는 데 도움이 되는 진단 메시지를 동일하게 받을 수 있습니다.

로그 스트리밍을 시작하려면 Cloud Shell에서 [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) 명령을 사용합니다.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

로그 스트리밍이 시작되고 나면 브라우저에서 Azure 앱을 새로 고쳐 웹 트래픽을 만듭니다. 이제 터미널에 파이프된 콘솔 로그가 표시됩니다.

언제든지 `Ctrl+C`를 입력하여 로그 스트리밍을 중지합니다. 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>Azure 앱 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 앱을 확인합니다.

왼쪽 메뉴에서 **App Services** 를 클릭한 다음, Azure 앱의 이름을 클릭합니다.

![Azure 앱에 대한 포털 탐색](./media/tutorial-nodejs-mongodb-app/access-portal.png)

기본적으로 포털에 앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure에서 MongoDB 데이터베이스 만들기
> * Node.js 앱을 MongoDB에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

사용자 지정 DNS 이름을 앱에 매핑하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"] 
> [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

- [Node.js 앱 구성](configure-language-nodejs.md)
- [환경 변수 및 앱 설정 참조](reference-app-settings.md)