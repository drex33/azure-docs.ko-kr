---
title: '자습서: Azure Cosmos DB Cassandra API 계정을 만드는 Java 앱 빌드'
description: 이 자습서에서는 Java 애플리케이션을 사용하여 Cassandra API 계정을 만들고, 데이터베이스(키스페이스라고도 함)를 추가하고, 테이블을 해당 계정에 추가하는 방법을 보여 줍니다.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: abe6bcf71ed8c83bfed688a67d5a6f5de15c28df
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788209"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>자습서: 키/값 데이터를 저장하는 Java 애플리케이션을 사용하여 Azure Cosmos DB의 Cassandra API 계정 만들기
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

개발자는 키/값 쌍을 사용하는 애플리케이션을 가질 수도 있습니다. Azure Cosmos DB의 Cassandra API 계정을 사용하여 키/값 데이터를 저장할 수 있습니다. 이 자습서에서는 Java 애플리케이션을 사용하여 Azure Cosmos DB의 Cassandra API 계정을 만들고, 데이터베이스(키스페이스라고도 함)를 추가하고, 테이블을 추가하는 방법에 대해 설명합니다. Java 애플리케이션에서 [Java 드라이버](https://github.com/datastax/java-driver)를 사용하여 사용자 ID, 사용자 이름 및 사용자 도시와 같은 세부 정보를 포함하는 사용자 데이터베이스를 만듭니다.  

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Cassandra 데이터베이스 계정 만들기
> * 계정 연결 문자열 가져오기
> * Maven 프로젝트 및 종속성 만들기
> * 데이터베이스 및 테이블 추가
> * 앱 실행

## <a name="prerequisites"></a>필수 구성 요소 

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다. 

* 최신 버전의 [JDK(Java Development Kit)](/java/azure/jdk/)를 가져옵니다. 

* [Maven](https://maven.apache.org/) 이진 보관 파일을 [다운로드](https://maven.apache.org/download.cgi)하여 [설치](https://maven.apache.org/install.html)합니다. 
  - Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다. 

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기 

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="get-the-connection-details-of-your-account"></a>계정의 연결 세부 정보 가져오기  

Azure Portal에서 연결 문자열 정보를 가져오고, Java 구성 파일에 복사합니다. 연결 문자열을 통해 앱이 호스트된 데이터베이스와 통신할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에서 Azure Cosmos 계정으로 이동합니다. 

2. **연결 문자열** 창을 엽니다.  

3. 다음 단계에서 사용할 **CONTACT POINT**, **PORT**, **USERNAME** 및 **PRIMARY PASSWORD** 값을 복사합니다.

## <a name="create-the-project-and-the-dependencies"></a>프로젝트 및 종속성 만들기 

이 문서에서 사용하는 Java 샘플 프로젝트는 GitHub에서 호스팅됩니다. 이 문서의 단계를 실행하거나 [azure-cosmos-db-cassandra-java-geting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) 리포지토리에서 샘플을 다운로드할 수 있습니다. 

파일이 다운로드되면 `java-examples\src\main\resources\config.properties` 파일에 있는 연결 문자열 정보를 업데이트하고 실행합니다.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

다음 단계를 사용하여 샘플을 처음부터 빌드합니다. 

1. 터미널 또는 명령 프롬프트에서 Cassandra-demo라는 새 Maven 프로젝트를 만듭니다. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. `cassandra-demo` 폴더를 찾습니다. 텍스트 편집기를 사용하여 생성된 `pom.xml` 파일을 엽니다. 

   [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/pom.xml) 파일에 표시된 대로 Cassandra 종속성을 추가하고 프로젝트에 필요한 플러그 인을 빌드합니다.  

3. `cassandra-demo\src\main` 폴더 아래에서 `resources`라는 새 폴더를 만듭니다.  리소스 폴더 아래에서 config.properties 및 log4j.properties 파일을 추가합니다.

   - [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/config.properties) 파일은 Cassandra API 계정의 연결 엔드포인트 및 키 값을 저장합니다. 
   
   - [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/log4j.properties) 파일은 Cassandra API와 상호 작용하는 데 필요한 로깅 수준을 정의합니다.  

4. `src/main/java/com/azure/cosmosdb/cassandra/` 폴더로 이동합니다. cassandra 폴더 내에서 `utils`라는 또 다른 폴더를 만듭니다. 새 폴더는 Cassandra API 계정에 연결하는 데 필요한 유틸리티 클래스를 저장합니다. 

   [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) 클래스를 추가하여 클러스터를 만들고 Cassandra 세션을 열고 닫습니다. 클러스터에서 Azure Cosmos DB의 Cassandra API 계정에 연결하고 액세스할 세션을 반환합니다. [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) 클래스를 사용하여 config.properties 파일에서 연결 문자열 정보를 읽습니다. 

5. Java 샘플은 사용자 정보(예: 사용자 이름, 사용자 ID 및 사용자 도시)가 있는 데이터베이스를 만듭니다. main 함수의 사용자 세부 정보에 액세스하려면 get 및 set 메서드를 정의해야 합니다.
 
   get 및 set 메서드를 사용하여 `src/main/java/com/azure/cosmosdb/cassandra/` 폴더 아래에 [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) 클래스를 만듭니다. 

## <a name="add-a-database-and-a-table"></a>데이터베이스 및 테이블 추가  

이 섹션에서는 CQL을 사용하여 데이터베이스(키스페이스)와 테이블을 추가하는 방법을 설명합니다.

1. `src\main\java\com\azure\cosmosdb\cassandra` 폴더 아래에서 `repository`라는 새 폴더를 만듭니다. 

2. `UserRepository` Java 클래스를 만들고 여기에 다음 코드를 추가합니다. 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. `src\main\java\com\azure\cosmosdb\cassandra` 폴더를 찾아 `examples`라는 새 하위 폴더를 만듭니다.

4. `UserProfile` Java 클래스를 만듭니다. 이 클래스에는 이전에 정의한 createKeyspace 및 createTable 메서드를 호출하는 main 메서드가 들어 있습니다. 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>앱 실행 

1. 명령 프롬프트 또는 터미널 창을 엽니다. 다음 코드 블록을 붙여넣습니다. 

   이 코드는 프로젝트를 만든 폴더 경로로 디렉터리를 변경(cd)합니다. 그런 다음, `mvn clean install` 명령을 실행하여 대상 폴더 내에 `cosmosdb-cassandra-examples.jar` 파일을 생성합니다. 마지막으로 Java 애플리케이션을 실행합니다.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   터미널 창에 키스페이스와 테이블이 작성되었다는 알림이 표시됩니다. 
   
2. 이제 Azure Portal에서 **데이터 탐색기** 를 열어 키스페이스 및 테이블이 생성되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Java 애플리케이션을 사용하여 Azure Cosmos DB의 Cassandra API 계정, 데이터베이스 및 테이블을 만드는 방법을 알아보았습니다. 이제 다음 문서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [샘플 데이터를 Cassandra API 테이블에 로드](load-data-table.md).
