---
title: '자습서: Azure Cosmos DB의 Cassandra API 계정에서 데이터 쿼리'
description: 이 자습서에서는 Java 애플리케이션을 사용하여 Azure Cosmos DB Cassandra API 계정에서 사용자 데이터를 쿼리하는 방법을 보여줍니다.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 7f33d5e41085c1216f33e897033e981998c4a93d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788202"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>자습서: Azure Cosmos DB의 Cassandra API 계정에서 데이터 쿼리
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

개발자는 키/값 쌍을 사용하는 애플리케이션을 가질 수도 있습니다. Azure Cosmos DB에서 Cassandra API 계정을 사용하여 키/값 데이터를 저장 및 쿼리할 수 있습니다. 이 자습서에서는 Java 애플리케이션을 사용하여 Azure Cosmos DB의 Cassandra API 계정에서 사용자 데이터를 쿼리하는 방법을 보여줍니다. Java 애플리케이션은 [Java 드라이버](https://github.com/datastax/java-driver)를 사용하며 사용자 ID, 사용자 이름, 사용자 도시와 같은 사용자 데이터를 쿼리합니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Cassandra 테이블에서 데이터 쿼리
> * 앱 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 이 문서는 여러 부분으로 구성된 자습서의 일부분입니다. 시작하기 전에 Cassandra API 계정, 키스페이스, 테이블을 만들고, [테이블에 샘플 데이터를 로드](load-data-table.md)하는 이전 단계를 완료해야 합니다. 

## <a name="query-data"></a>쿼리 데이터

다음 단계에 따라 Cassandra API 계정에서 데이터를 쿼리합니다.

1. `src\main\java\com\azure\cosmosdb\cassandra` 폴더에서 `UserRepository.java` 파일을 엽니다. 다음 코드 블록을 추가합니다. 이 코드는 다음 세 가지 방법을 제공합니다. 

   * 데이터베이스의 모든 사용자를 쿼리
   * 사용자 ID로 필터링한 특정 사용자를 쿼리
   * 테이블 삭제

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. `src\main\java\com\azure\cosmosdb\cassandra` 폴더에서 `UserProfile.java` 파일을 엽니다. 이 클래스에는 이전에 정의한 데이터 삽입 메서드인 createKeyspace 및 createTable을 호출하는 main 메서드가 들어 있습니다. 이제 모든 사용자나 특정 사용자를 쿼리하는 다음 코드를 추가합니다.

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Java 앱 실행
1. 명령 프롬프트 또는 터미널 창을 엽니다. 다음 코드 블록을 붙여넣습니다. 

   이 코드는 프로젝트를 만든 폴더 경로로 디렉터리를 변경(cd)합니다. 그런 다음, `mvn clean install` 명령을 실행하여 대상 폴더 내에 `cosmosdb-cassandra-examples.jar` 파일을 생성합니다. 마지막으로 Java 애플리케이션을 실행합니다.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. 이제 Azure Portal에서 **데이터 탐색기** 를 열고 사용자 테이블이 삭제되었는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 리소스 그룹, Azure Cosmos 계정 및 모든 관련 리소스를 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고, **삭제** 를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB의 Cassandra API 계정에서 데이터를 쿼리하는 방법을 알아보았습니다. 이제 다음 문서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Cassandra API 계정으로 데이터 마이그레이션](migrate-data.md)


