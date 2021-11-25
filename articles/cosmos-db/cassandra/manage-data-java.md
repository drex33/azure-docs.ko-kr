---
title: Java 3.0 SDK를 사용하는 Azure Cosmos DB Cassandra API가 포함된 Java 앱
description: 이 빠른 시작은 Azure Portal 및 Java 3.0 SDK와 함께 Azure Cosmos DB Cassandra API를 사용하여 프로필 애플리케이션을 만드는 방법을 보여 줍니다.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 07/17/2021
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java, mode-other
ms.openlocfilehash: 8d88de8cf1e7840997cbd5815daafd76aad3d53f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133075545"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v3-driver"></a>Azure Cosmos DB Cassandra API 데이터를 관리하는 Java 앱 빌드(v3 드라이버)
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

이 빠른 시작에서는 Azure Cosmos DB Cassandra API 계정을 만들고, GitHub에서 복제된 Cassandra Java 앱을 만들고, Java용 [v3.x Apache Cassandra 드라이버](https://github.com/datastax/java-driver/tree/3.x)를 사용하여 Cassandra 데이터베이스 및 컨테이너를 만듭니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다.
- [JDK(Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). `JAVA_HOME` 환경 변수를 JDK가 설치된 폴더로 지정합니다.
- [Maven 이진 보관 파일](https://maven.apache.org/download.cgi). Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 설치합니다.
- [Git](https://www.git-scm.com/downloads) Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 설치합니다.

> [!NOTE]
> Java용 오픈 소스 Apache Cassandra 드라이버 [버전 3](https://github.com/datastax/java-driver/tree/3.x)을 사용하는 간단한 빠른 시작입니다. 대부분의 경우 기존 코드를 변경하지 않고도 기존 Apache Cassandra 종속 Java 애플리케이션을 Azure Cosmos DB Cassandra API에 연결할 수 있습니다. 그러나 더 나은 전체 환경을 위해 사용자 지정 재시도 및 부하 분산 정책이 포함된 [사용자 지정 Java 확장](https://github.com/Azure/azure-cosmos-cassandra-extensions/tree/feature/java-driver-3%2F1.0.0)을 추가하는 것이 좋습니다. 이는 각각 Azure Cosmos DB에서 [속도 제한](./scale-account-throughput.md#handling-rate-limiting-429-errors) 및 애플리케이션 수준 장애 조치(failover)를 처리하기 위한 것입니다. 확장을 구현하는 포괄적인 샘플은 [여기](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample)서 찾을 수 있습니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

문서 데이터베이스를 만들려면 Azure Cosmos DB로 Cassandra 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 코드 사용으로 전환해 보겠습니다. GitHub에서 Cassandra 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 엽니다. 이름이 `git-samples`인 새 폴더를 만듭니다. 그런 다음, 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 코드로 데이터베이스 리소스를 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 이 코드 조각은 모두 *src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java* 파일에서 가져옵니다.  

* Cassandra 호스트, 포트, 사용자 이름, 암호 및 TLS/SSL 옵션이 설정됩니다. 연결 문자열 정보는 Azure Portal의 연결 문자열 페이지에서 가져옵니다.

  ```java
  cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
  ```

* `cluster`가 Azure Cosmos DB Cassandra API에 연결되고 액세스할 세션을 반환합니다.

  ```java
  return cluster.connect();
  ```

다음 코드 조각은 *src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java* 파일에서 가져온 것입니다.

* 새 keyspace를 만듭니다.

  ```java
  public void createKeyspace() {
      final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
      session.execute(query);
      LOGGER.info("Created keyspace 'uprofile'");
  }
  ```

* 새 테이블을 만듭니다.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* 준비 문 개체를 사용하여 사용자 엔터티를 삽입합니다.

  ```java
  public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name, user_bcity) VALUES (?,?,?)";
      return session.prepare(insertStatement);
  }

  public void insertUser(PreparedStatement statement, int id, String name, String city) {
      BoundStatement boundStatement = new BoundStatement(statement);
      session.execute(boundStatement.bind(id, name, city));
  }
  ```

* 모든 사용자 정보를 얻기 위한 쿼리입니다.

  ```java
  public void selectAllUsers() {
      final String query = "SELECT * FROM uprofile.user";
      List<Row> rows = session.execute(query).all();

      for (Row row : rows) {
          LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
      }
  }
  ```

* 단일 사용자 정보를 가져오기 위한 쿼리입니다.

  ```java
  public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
  }
  ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 연결 문자열 세부 정보를 통해 앱이 호스트된 데이터베이스와 통신할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열** 을 선택합니다. 

    :::image type="content" source="./media/manage-data-java/copy-username-connection-string-azure-portal.png" alt-text="Azure Portal, 연결 문자열 페이지에서 사용자 이름 보기 및 복사":::

2. 화면 오른쪽에 있는 :::image type="icon" source="./media/manage-data-java/copy-button-azure-portal.png"::: 단추를 사용하여 CONTACT POINT 값을 복사합니다. 

3. *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources* 폴더에서 *config.properties* 파일을 엽니다. 

3. 포털의 CONTACT POINT 값을 줄 2의 `<Cassandra endpoint host>`에 붙여넣습니다.

    *config.properties* 의 줄 2는 이제 다음과 유사하게 나타납니다. 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. 포털로 돌아가서 USERNAME 값을 복사합니다. 포털의 USERNAME 값을 줄 4의 `<cassandra endpoint username>`에 붙여넣습니다.

    *config.properties* 의 줄 4는 이제 다음과 유사하게 나타납니다. 

    `cassandra_username=cosmos-db-quickstart`

4. 포털로 돌아가서 PASSWORD 값을 복사합니다. 포털의 PASSWORD 값을 줄 5의 `<cassandra endpoint password>`에 붙여넣습니다.

    *config.properties* 의 줄 5는 이제 다음과 유사하게 나타납니다. 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. 줄 6에서 특정 TLS/SSL 인증서를 사용하려면 `<SSL key store file location>`을 TLS/SSL 인증서의 위치로 바꿉니다. 값을 제공하지 않으면 <JAVA_HOME>/jre/lib/security/cacerts에 설치된 JDK 인증서가 사용됩니다. 

6. 특정 TLS/SSL 인증서를 사용하기 위해 줄 6을 변경한 경우 줄 7을 업데이트하여 해당 인증서의 암호를 사용합니다. 

7. *config.properties* 파일을 저장합니다.

## <a name="run-the-java-app"></a>Java 앱 실행

1. Git 터미널 창에서 `azure-cosmosdb-cassandra-java-getting-started` 폴더로 `cd`합니다.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started"
    ```

2. Git 터미널 창에서 다음 명령을 사용하여 `cosmosdb-cassandra-examples.jar` 파일을 생성합니다.

    ```git
    mvn clean install
    ```

3. git 터미널 창에서 다음 명령을 실행하여 Java 애플리케이션을 시작합니다.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    터미널 창에 키스페이스와 테이블이 작성되었다는 알림이 표시됩니다. 그런 다음, 테이블의 모든 사용자를 선택하여 반환하고 출력을 표시한 후 ID로 행을 선택하고 값을 표시합니다.  

    프로그램 실행을 중지하고 콘솔 창을 닫으려면 Ctrl+C를 누릅니다.

4. Azure Portal에서 **데이터 탐색기** 를 열어 이 새 데이터를 쿼리/수정/사용합니다. 

    :::image type="content" source="./media/manage-data-java/view-data-explorer-java-app.png" alt-text="데이터 탐색기에서 데이터 보기 - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cassandra API를 사용하여 Azure Cosmos DB 계정을 만들고 Cassandra 데이터베이스 및 컨테이너를 만드는 Cassandra Java 앱을 실행하는 방법을 알아보았습니다. 이제 Azure Cosmos DB 계정으로 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 Cassandra 데이터 가져오기](migrate-data.md)
