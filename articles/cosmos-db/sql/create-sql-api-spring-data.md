---
title: 빠른 시작 - Spring Data Azure Cosmos DB v3을 사용하여 Azure Cosmos DB를 통해 문서 데이터베이스 만들기
description: 이 빠른 시작에서는 Azure Cosmos DB SQL API에 연결하고 쿼리하는 데 사용할 수 있는 Spring Data Azure Cosmos DB v3 코드 샘플을 제공합니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 08/26/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 3982a5a190200e40afcbeb7068f1571da0a182eb
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757505"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>빠른 시작: Azure Cosmos DB SQL API 데이터를 관리하는 Spring Data Azure Cosmos DB v3 앱을 빌드
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3 커넥터](create-sql-api-spark.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)

이 빠른 시작에서는 Azure Portal 및 GitHub에서 복제된 Spring Data Azure Cosmos DB v3 앱을 사용하여 Azure Cosmos DB SQL API 계정을 만들고 관리합니다. 먼저 Azure Portal을 사용하여 Azure Cosmos DB SQL API 계정을 만든 다음, Spring Data Azure Cosmos DB v3 커넥터를 사용하여 Spring Boot 앱을 만든 후, Spring Boot 애플리케이션을 사용하여 Cosmos DB 계정에 리소스를 추가합니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 Spring Data Azure Cosmos DB 버전 3에 대한 것입니다. [여기에서 버전 2의 릴리스 정보](sql-api-sdk-java-spring-v2.md)를 확인할 수 있습니다. 
>
> Spring Data Azure Cosmos DB는 SQL API만 지원합니다.
>
> 다른 Azure Cosmos DB API의 Spring Data에 대한 정보는 다음 문서를 참조하세요.
> * [Azure Cosmos DB의 Spring Data for Apache Cassandra](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB의 Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
>

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. URI가 `https://localhost:8081`이고 키가 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`인 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 사용할 수도 있습니다.
- [JDK(Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). `JAVA_HOME` 환경 변수를 JDK가 설치된 폴더로 지정합니다.
- [Maven 이진 보관 파일](https://maven.apache.org/download.cgi). Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 설치합니다.
- [Git](https://www.git-scm.com/downloads) Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 설치합니다.

## <a name="introductory-notes"></a>소개 정보

*Cosmos DB 계정의 구조입니다.* API 또는 프로그래밍 언어와 관계없이 Cosmos DB *계정* 에는 0개 이상의 *데이터베이스* 가 포함되고, *DB(데이터베이스)* 에는 0개 이상의 *컨테이너* 가 포함되고, *컨테이너* 에는 0개 이상의 항목이 포함됩니다(아래 다이어그램 참조).

:::image type="content" source="../media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos 계정 엔터티" border="false":::

데이터베이스, 컨테이너 및 항목은 [여기](../account-databases-containers-items.md)서 자세히 알아볼 수 있습니다. 몇 가지 중요한 속성(예: *프로비저닝 처리량* 및 *파티션 키*)은 컨테이너 수준에서 정의됩니다. 

프로비저닝된 처리량은 *RU(요청 단위)* 로 측정됩니다. 이는 화폐 가격을 포함하고 계정의 운영 비용에 대한 실질적인 결정 요소입니다. 프로비저닝된 처리량은 컨테이너 단위 또는 데이터베이스 단위 세분성에서 선택할 수 있지만 일반적으로 컨테이너 수준 처리량 사양이 선호됩니다. 처리량 프로비저닝은 [여기](../set-throughput.md)서 자세히 알아볼 수 있습니다.

항목이 Cosmos DB 컨테이너에 삽입되면 요청을 처리하기 위해 더 많은 스토리지와 컴퓨팅을 추가하여 데이터베이스를 수평으로 확장시킵니다. 스토리지 및 컴퓨팅 용량은 *파티션* 이라는 별도의 단위로 추가되며, 각 문서를 파티션에 매핑하는 파티션 키로 하나의 필드를 문서에서 선택해야 합니다. 파티션을 관리하는 방법은 파티션 키 값의 범위에서 거의 동일한 조각이 각 파티션에 할당되는 것입니다. 따라서 비교적 임의로 또는 균일하게 분산되는 파티션 키를 선택하는 것이 좋습니다. 그렇지 않으면 일부 파티션에는 훨씬 더 많은 요청이 표시되고(*핫 파티션*), 다른 파티션에는 훨씬 더 적은 요청이 표시되므로(*콜드 파티션*) 이를 방지해야 합니다. 파티션 분할은 [여기](../partitioning-overview.md)서 자세히 알아볼 수 있습니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

문서 데이터베이스를 만들기 전에 Azure Cosmos DB를 사용하여 SQL API 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>컨테이너 추가

[!INCLUDE [cosmos-db-create-collection](../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>샘플 데이터 추가

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>데이터 쿼리

[!INCLUDE [cosmos-db-create-sql-api-query-data](../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 코드 사용으로 전환해 보겠습니다. GitHub에서 SQL API 앱을 복제하고 연결 문자열을 설정한 다음 실행해보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [앱 실행](#run-the-app)으로 넘어갈 수 있습니다. 

### <a name="application-configuration-file"></a>애플리케이션 구성 파일

여기서는 Spring Boot 및 Spring Data가 사용자 환경을 개선하는 방법을 보여 줍니다. Cosmos 클라이언트를 설정하고 Cosmos 리소스에 연결하는 프로세스는 이제 코드가 아닌 구성입니다. 애플리케이션 시작 시 Spring Boot는 **application.properties** 의 설정을 사용하여 이 모든 상용구를 처리합니다.

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만든 후에는 구성 파일에 있는 필드를 입력하기만 하면 Spring Boot/Spring Data가 자동으로 다음을 수행합니다. (1) URI 및 키를 사용하여 기본 Java SDK `CosmosClient` 인스턴스를 만들고 (2) 데이터베이스 및 컨테이너에 연결합니다. 모든 설정이 완료되었습니다. **리소스 관리 코드는 더 이상 필요하지 않습니다.**

### <a name="java-source"></a>Java 소스

Spring Data 값-추가는 데이터 저장소에서 작동하기 위해 간단하고 명확한 표준화 및 플랫폼 독립적 인터페이스에서 제공됩니다. 위에서 연결된 Spring Data GitHub 샘플을 기반으로 하는 빌드는 Spring Data Azure Cosmos DB를 사용하여 Azure Cosmos DB 문서를 조작하기 위한 CRUD 및 쿼리 샘플입니다.

* `save` 메서드를 사용하여 항목 생성 및 업데이트

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* 리포지토리에 정의된 파생 쿼리 메서드를 사용한 지점-읽기. `findByIdAndLastName`은 `UserRepository`에 대해 지점-읽기를 수행합니다. 메서드 이름에 언급된 필드는 Spring Data가 `id` 및 `lastName` 필드로 정의된 지점-읽기를 실행하도록 합니다.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* `deleteAll`을 사용한 항목 삭제:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* 리포지토리 메서드 이름을 기반으로 파생된 쿼리. Spring Data는 `firstName` 필드에서 `UserRepository` `findByFirstName` 메서드를 Java SDK SQL 쿼리로 구현합니다. 이 쿼리는 지점-읽기로 구현할 수 없습니다.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>앱 실행

이제 Azure Portal로 돌아가서 연결 문자열 정보를 가져오고 엔드포인트 정보를 사용하여 앱을 시작합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다.

1. git 터미널 창에서 샘플 코드 폴더로 `cd`합니다.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. git 터미널 창에서 다음 명령을 사용하여 필요한 Spring Data Azure Cosmos DB 패키지를 설치합니다.

    ```bash
    mvn clean package
    ```

3. git 터미널 창에서 다음 명령을 사용하여 필요한 Spring Data Azure Cosmos DB 애플리케이션을 시작합니다.

    ```bash
    mvn spring-boot:run
    ```
    
4. 앱은 **application.properties** 를 로드하고 Azure Cosmos DB 계정의 리소스를 연결합니다.
5. 앱은 위에서 설명한 지점 CRUD 작업을 수행합니다.
6. 앱은 파생된 쿼리를 수행합니다.
7. 앱은 리소스를 삭제하지 않습니다. 요금이 발생하지 않도록 하려면 포털로 다시 전환하여 계정에서 [리소스를 정리](#clean-up-resources)합니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB SQL API 계정을 만들고, 데이터 탐색기를 사용하여 문서 데이터베이스 및 컨테이너를 만들고, Spring Data 앱을 실행하여 프로그래밍 방식으로 동일한 작업을 수행하는 방법을 알아보았습니다. 이제 Azure Cosmos DB 계정으로 추가 데이터를 가져올 수 있습니다. 

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](../import-data.md)