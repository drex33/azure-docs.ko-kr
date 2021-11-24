---
title: Azure Cosmos DB의 API for MongoDB 및 .NET SDK를 사용하여 웹 API 빌드
description: Azure Cosmos DB의 API for MongoDB를 사용하여 연결 및 쿼리할 수 있는 .NET 코드 샘플을 제공합니다.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 8/26/2021
ms.custom: devx-track-csharp, mode-other
ms.openlocfilehash: 469565733c373876f41c97a4cf3399d772ab1724
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063818"
---
# <a name="quickstart-build-a-net-web-api-using-azure-cosmos-dbs-api-for-mongodb"></a>빠른 시작: Azure Cosmos DB의 API for MongoDB를 사용하여 .NET 웹 API 빌드
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

이 빠른 시작에서는 다음과 같은 방법을 설명합니다.
1. [Azure Cosmos DB API for MongoDB 계정](mongodb-introduction.md) 만들기 
2. [MongoDB .NET 드라이버](https://docs.mongodb.com/ecosystem/drivers/csharp/)를 사용하여 제품 카탈로그 웹 API 빌드
3. 샘플 데이터 가져오기

## <a name="prerequisites-to-run-the-sample-app"></a>샘플 앱을 실행하기 위한 필수 구성 요소

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
* 활성 구독이 있는 Azure 계정. [Azure 계정을 무료로 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Azure 구독, 요금 및 약정 없이 [Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)해 볼 수도 있습니다.

아직 Visual Studio가 없으면 설치 시 **ASP.NET 및 웹 개발** 워크로드가 설치되는 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드합니다.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>개체 모델 학습

애플리케이션 빌드를 계속하기 전에 API for MongoDB의 리소스 계층 구조와 해당 리소스를 만들고 액세스하는 데 사용되는 개체 모델을 살펴보겠습니다. API for MongoDB는 다음과 같은 순서로 리소스를 만듭니다.

* Azure Cosmos DB API for MongoDB 계정
* 데이터베이스 
* 컬렉션 
* 문서

엔터티 계층 구조를 자세히 알아보려면 [Azure Cosmos DB 리소스 모델](../account-databases-containers-items.md) 문서를 참조하세요.

## <a name="install-the-sample-app-template"></a>샘플 앱 템플릿 설치

이 샘플은 로컬 복사본을 만들기 위해 설치할 수 있는 dotnet 프로젝트 템플릿입니다. 명령 창에서 다음 명령을 실행합니다.

```bash
mkdir "C:\cosmos-samples"
cd "C:\cosmos-samples"
dotnet new -i Microsoft.Azure.Cosmos.Templates
dotnet new cosmosmongo-webapi
```

이전 명령은

1. 샘플에 대한 *C:\cosmos-samples* 디렉터리를 만듭니다. 운영 체제에 적합한 폴더를 선택합니다.
1. 현재 디렉터리를 *C:\cosmos-samples* 폴더로 변경합니다.
1. 프로젝트 템플릿을 설치하여 dotnet CLI에서 전역적으로 사용할 수 있도록 합니다.
1. 프로젝트 템플릿을 사용하여 로컬 샘플 앱을 만듭니다.

dotnet CLI를 사용하지 않으려면 [프로젝트 템플릿을 ZIP 파일로 다운로드](https://github.com/Azure/azure-cosmos-dotnet-templates)할 수도 있습니다. 이 샘플은 `Templates/APIForMongoDBQuickstart-WebAPI` 폴더에 있습니다.

## <a name="review-the-code"></a>코드 검토

다음 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토하세요. 그렇지 않으면 [애플리케이션 설정 업데이트](#update-the-application-settings)로 건너뜁니다.

### <a name="setup-connection"></a>연결 설정

다음 코드 조각은 *Services/MongoService.cs* 파일에서 가져왔습니다.

* 다음 클래스는 클라이언트를 나타내며 .NET 프레임워크에서 클라이언트를 사용하는 서비스에 주입합니다.

    ```cs
        public class MongoService
        {
            private static MongoClient _client;

            public MongoService(IDatabaseSettings settings)
            {
                _client = new MongoClient(settings.MongoConnectionString);
            }

            public MongoClient GetClient()
            {
                return _client;
            }
        }
    ```

### <a name="setup-product-catalog-data-service"></a>제품 카탈로그 데이터 서비스 설치

다음 코드 조각은 *Services/ProductService.cs* 파일에서 가져왔습니다.

* 다음 코드는 데이터베이스와 컬렉션을 검색하고 아직 없으면 새로 만듭니다.

    ```csharp
    private readonly IMongoCollection<Product> _products;        

    public ProductService(MongoService mongo, IDatabaseSettings settings)
    {
        var db = mongo.GetClient().GetDatabase(settings.DatabaseName);
        _products = db.GetCollection<Product>(settings.ProductCollectionName);
    }
    ```

* 다음 코드는 고유 제품 식별자인 sku로 문서를 검색합니다.

    ```csharp
    public Task<Product> GetBySkuAsync(string sku)
    {
        return _products.Find(p => p.Sku == sku).FirstOrDefaultAsync();
    }
    ```

* 다음 코드는 제품을 만들고 컬렉션에 삽입합니다.

    ```csharp
    public Task CreateAsync(Product product)
    {
        _products.InsertOneAsync(product);
    }
    ```

* 다음 코드는 제품을 찾고 업데이트합니다.

    ```csharp
    public Task<Product> UpdateAsync(Product update)
    {
        return _products.FindOneAndReplaceAsync(
            Builders<Product>.Filter.Eq(p => p.Sku, update.Sku), 
            update, 
            new FindOneAndReplaceOptions<Product> { ReturnDocument = ReturnDocument.After });
    }
    ```

    마찬가지로 [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) 메서드를 사용하여 문서를 삭제할 수 있습니다.

## <a name="update-the-application-settings"></a>애플리케이션 설정 업데이트

Azure Portal에서 연결 문자열 정보를 복사합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Cosmos DB 계정을 선택하고 왼쪽 탐색 영역에서 **연결 문자열** 을 선택한 다음, **읽기-쓰기 키** 를 선택합니다. 다음 단계에서 화면 오른쪽에 있는 복사 단추를 사용하여 기본 연결 문자열을 *appsettings.json* 파일에 복사합니다.

2. *appsettings.json* 파일을 엽니다.

3. 포털에서 **기본 연결 문자열** 값을 복사하고(복사 단추 사용) **appsettings.json** 파일의 **DatabaseSettings.MongoConnectionString** 속성 값으로 만듭니다.

4. **appsettings.json** 파일에서 **DatabaseSettings.DatabaseName** 속성의 **database name** 값을 검토합니다.

5. **appsettings.json** 파일에서 **DatabaseSettings.ProductCollectionName** 속성의 **collection name** 값을 검토합니다.

> [!WARNING]
> 암호 또는 기타 중요한 데이터를 소스 코드로 확인하지 마세요.

이제 Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다.

## <a name="load-sample-data"></a>샘플 데이터 로드

소량의 JSON, CSV 또는 TSV 데이터를 쉽게 가져오는 CLI 도구인 [mongoimport](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport)를 [다운로드](https://www.mongodb.com/try/download/database-tools)합니다. mongoimport를 사용하여 이 프로젝트의 `Data` 폴더에 제공된 샘플 제품 데이터를 로드합니다.

Azure Portal에서 연결 정보를 복사하여 아래 명령에 입력합니다. 

```bash
mongoimport --host <HOST>:<PORT> -u <USERNAME> -p <PASSWORD> --db cosmicworks --collection products --ssl --jsonArray --writeConcern="{w:0}" --file Data/products.json
```

1. [Azure Portal](https://portal.azure.com/)에서 Azure Cosmos DB API for MongoDB 계정을 선택하고 왼쪽 탐색 영역에서 **연결 문자열** 을 선택한 다음, **읽기-쓰기 키** 를 선택합니다. 

1. 포털에서 **HOST** 값을 복사하여(복사 단추 사용) **\<HOST\>** 대신 입력합니다.

1. 포털에서 **PORT** 값을 복사하여(복사 단추 사용) **\<PORT\>** 대신 입력합니다.

1. 포털에서 **USERNAME** 값을 복사하여(복사 단추 사용) **\<USERNAME\>** 대신 입력합니다.

1. 포털에서 **PASSWORD** 값을 복사하여(복사 단추 사용) **\<PASSWORD\>** 대신 입력합니다.

1. **database name** 값을 검토하고 `cosmicworks`가 아닌 다른 항목을 만들었으면 업데이트합니다.

1. **collection name** 값을 검토하고 `products`가 아닌 다른 항목을 만들었으면 업데이트합니다.

> [!Note]
> 이 단계를 건너뛰려면 이 웹 API 프로젝트의 일부로 제공된 POST 엔드포인트를 사용하여 올바른 스키마로 문서를 만들면 됩니다.

## <a name="run-the-app"></a>앱 실행

Visual Studio에서 CTRL + F5를 선택하여 앱을 실행합니다. 기본 브라우저가 앱과 함께 시작됩니다.

CLI를 선호하는 경우 명령 창에서 다음 명령을 실행하여 샘플 앱을 시작합니다. 이 명령은 프로젝트 종속성도 설치하고 프로젝트를 빌드하기는 하지만 브라우저를 자동으로 시작하지는 않습니다.

```bash
dotnet run
```

애플리케이션이 실행된 후 `https://localhost:5001/swagger/index.html`로 이동하여 웹 API에 대한 [Swagger 설명서](https://swagger.io/)를 확인하고 샘플 요청을 제출합니다.

테스트하려는 API를 선택하고 "사용해 보기"를 선택합니다.

:::image type="content" source="./media/create-mongodb-dotnet/try-swagger.png" alt-text="웹 API Swagger UI Try API 엔드포인트 페이지의 스크린샷":::

필요한 매개 변수를 입력하고 "실행"을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 API for MongoDB 계정을 만들고, 코드를 사용하여 데이터베이스 및 컬렉션을 만들고, 웹 API 앱을 실행하는 방법을 알아보았습니다. 이제 데이터베이스에 추가 데이터를 가져올 수 있습니다. 

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
