---
title: Azure Cosmos DB의 API for MongoDB를 사용하여 Node.js 앱 빌드
description: Azure Cosmos DB의 API for MongoDB를 사용하여 온라인 데이터베이스를 만드는 자습서입니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js
ms.openlocfilehash: 9edcb24280db6c3fb102b9d1c28d7b2a0e537294
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031621"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Node.js 및 Azure Cosmos DB의 API for MongoDB를 사용하여 앱 빌드 
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](../sql-api-get-started.md)
> * [.NET Core](../sql-api-get-started.md)
> * [Java](../create-sql-api-java.md)
> * [MongoDB용 Node.js](nodejs-console-app.md)
> * [Node.JS](../sql-api-nodejs-get-started.md)
>

이 예제에서는 Node.js 및 Azure Cosmos DB의 API for MongoDB를 사용하여 콘솔 앱을 빌드하는 방법을 보여줍니다.

이 예제를 사용하려면 다음을 수행해야 합니다.

* Azure Cosmos DB의 API for MongoDB를 사용하도록 구성된 Cosmos 계정을 [만듭니다](create-mongodb-dotnet.md#create-account).
* [연결 문자열](connect-mongodb-account.md) 정보를 검색합니다.

## <a name="create-the-app"></a>앱 만들기

1. *app.js* 파일을 만들고 아래 코드를 복사하여 붙여넣습니다.

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **선택 사항**: **MongoDB Node.js 2.2 드라이버** 를 사용 중인 경우 다음 코드 조각을 바꾸십시오.

    원본:

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    다음으로 바꿔여 합니다.

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. 계정 설정에 따라 *app.js* 파일에서 다음 변수를 수정합니다([연결 문자열](connect-mongodb-account.md)을 찾는 방법 자세히 알아보기).

    > [!IMPORTANT]
    > **MongoDB Node.js 3.0 드라이버** 는 Cosmos DB 암호에서 특수 문자를 인코딩할 것을 요구합니다. '=' 문자를 %3D로 인코딩하는지 확인
    >
    > 예: 암호 *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* 가 *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D* 로 인코딩됩니다
    >
    > **MongoDB Node.js 2.2 드라이버** 는 Cosmos DB 암호에서 특수 문자를 인코딩할 것을 요구하지 않습니다.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. 즐겨찾는 터미널을 열고 **npm install mongodb --save** 를 설치한 다음 **node app.js** 로 앱을 실행합니다.

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](connect-using-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](connect-using-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](nodejs-console-app.md)을 살펴봅니다.
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.