---
title: '빠른 시작: Cassandra API와 Node.js - Azure Cosmos DB'
description: 이 빠른 시작은 Node.js와 함께 Azure Cosmos DB Cassandra API를 사용하여 프로필 애플리케이션을 만드는 방법을 보여줍니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/10/2021
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 3b634ea6fec223efeec93c74291f9319d7ccbf77
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042220"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>빠른 시작: Node.js SDK 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](manage-data-dotnet.md)
> * [.NET Core](manage-data-dotnet-core.md)
> * [Java v3](manage-data-java.md)
> * [Java v4](manage-data-java-v4-sdk.md)
> * [Node.JS](manage-data-nodejs.md)
> * [Python](manage-data-python.md)
> * [Golang](manage-data-go.md)
>    

이 빠른 시작에서는 Azure Cosmos DB Cassandra API 계정을 만들고 GitHub에서 복제된 Cassandra Node.js 앱을 사용하여 Cassandra 데이터베이스 및 컨테이너를 만듭니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)] 또는 Azure 구독, 요금 및 약정 없이 [Azure Cosmos DB 평가판](https://azure.microsoft.com/try/cosmosdb/)을 사용할 수 있습니다.

또한 다음 항목도 필요합니다.
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) 버전 v0.10.29 이상
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

문서 데이터베이스를 만들려면 Azure Cosmos DB로 Cassandra 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 Cassandra API 앱을 복제하고 연결 문자열을 설정한 다음, 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 엽니다. 이름이 `git-samples`인 새 폴더를 만듭니다. 그런 다음, 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. git bash와 같은 git 터미널 창을 엽니다. `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

1. npm을 사용하여 Node.js 종속성을 설치합니다.

    ```bash
    npm install
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 코드로 데이터베이스 리소스를 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 이 코드 조각은 모두 `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started` 폴더에 있는 `uprofile.js` 파일에서 가져온 것입니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 

* 사용자 이름 및 암호 값은 Azure Portal의 연결 문자열 페이지를 사용하여 설정되었습니다. 

   ```javascript
    let authProvider = new cassandra.auth.PlainTextAuthProvider(
        config.username,
        config.password
    );
   ```

* `client`는 contactPoint 정보를 사용하여 초기화됩니다. contactPoint는 Azure Portal에서 검색됩니다.

    ```javascript
    let client = new cassandra.Client({
        contactPoints: [`${config.contactPoint}:10350`],
        authProvider: authProvider,
        localDataCenter: config.localDataCenter,
        sslOptions: {
            secureProtocol: "TLSv1_2_method"
        },
    });
    ```

* `client`는 Azure Cosmos DB Cassandra API에 연결됩니다.

    ```javascript
    client.connect();
    ```

* 새 keyspace가 생성됩니다.

    ```javascript
    var query =
        `CREATE KEYSPACE IF NOT EXISTS ${config.keySpace} WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter' : '1' }`;
    await client.execute(query);
  }
    ```

* 새 테이블이 생성됩니다.

   ```javascript
    query =
        `CREATE TABLE IF NOT EXISTS ${config.keySpace}.user (user_id int PRIMARY KEY, user_name text, user_bcity text)`;
    await client.execute(query);
   },
   ```

* 키/값 엔터티가 삽입됩니다.

    ```javascript
    const arr = [
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (1, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (2, 'JiriK', 'Toronto')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (3, 'IvanH', 'Mumbai')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (4, 'IvanH', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (5, 'IvanaV', 'Belgaum')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (6, 'LiliyaB', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (7, 'JindrichH', 'Buenos Aires')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (8, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (9, 'JozefM', 'Seattle')`,
    ];
    for (const element of arr) {
        await client.execute(element);
    }
    ```

* 모든 키 값을 가져오는 쿼리입니다.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user`;
    const resultSelect = await client.execute(query);

    for (const row of resultSelect.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* 키 값을 가져오는 쿼리입니다.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user where user_id=1`;
    const resultSelectWhere = await client.execute(query);

    for (const row of resultSelectWhere.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* 연결을 닫습니다. 

    ```javascript
    client.shutdown();
    ```  

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 연결 문자열을 통해 앱이 호스트된 데이터베이스와 통신할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열** 을 선택합니다. 

1. 화면 오른쪽에 있는 :::image type="icon" source="./media/manage-data-nodejs/copy.png"::: 단추를 사용하여 상위 값인 CONTACT POINT 값을 복사합니다.

    :::image type="content" source="./media/manage-data-nodejs/keys.png" alt-text="Azure Portal, 연결 문자열 페이지에서 CONTACT POINT, USERNAME 및 PASSWORD를 보고 복사":::

1. `config.js` 파일을 엽니다. 

1. 포털의 CONTACT POINT 값을 줄 9의 `'CONTACT-POINT`에 붙여넣습니다.

    9줄은 다음과 같이 표시됩니다. 

    `contactPoint: "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com",`

1. 포털의 USERNAME 값을 복사하여 줄 2의 `<FillMEIN>`에 붙여넣습니다.

    줄 2는 다음과 같이 보일 것입니다. 

    `username: 'cosmos-db-quickstart',`

1. 포털의 PASSWORD 값을 복사하여 줄 8의 `USERNAME`에 붙여넣습니다.

    줄 8은 다음과 같이 보일 것입니다.

    `password: '2Ggkr662ifxz2Mg==',`

1. 지역을 이 리소스를 만든 Azure 지역으로 바꿉니다.

1. `config.js` 파일을 저장합니다.


## <a name="run-the-nodejs-app"></a>Node.js 앱 실행

1. 터미널 창에서 이전에 복제한 샘플 디렉터리에 있는지 확인합니다.

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

1. 노드 애플리케이션을 실행합니다.

    ```bash
    npm start
    ```

4. 명령줄에서 예상대로 결과가 나타나는지 확인하세요.

    :::image type="content" source="./media/manage-data-nodejs/output.png" alt-text="출력 보기 및 확인":::

    프로그램 실행을 중지하고 콘솔 창을 닫으려면 CTRL+C를 누릅니다. 

5. Azure Portal에서 **데이터 탐색기** 를 열어 이 새 데이터를 쿼리/수정/사용합니다. 

    :::image type="content" source="./media/manage-data-nodejs/data-explorer.png" alt-text="데이터 탐색기에서 데이터 보기"::: 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cassandra API를 사용하여 Azure Cosmos DB 계정을 만들고 Cassandra 데이터베이스 및 컨테이너를 만드는 Cassandra Node.js 앱을 실행하는 방법을 알아보았습니다. 이제 Azure Cosmos DB 계정으로 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 Cassandra 데이터 가져오기](migrate-data.md)
