---
title: Azure Cosmos DB를 Azure Spring Cloud 애플리케이션에 바인딩
description: Azure Cosmos DB를 Azure Spring Cloud 애플리케이션에 바인딩하는 방법 알아보기
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 387d526002411395e8bebc0fa59925bfa383e598
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529501"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Azure Cosmos DB 데이터베이스를 Azure Spring Cloud 애플리케이션에 바인딩

**이 문서는 다음에 적용됩니다.** ✔️ Java

Spring Boot 애플리케이션을 수동으로 구성하는 대신 Azure Spring Cloud를 사용하여 선택한 Azure 서비스를 사용자 애플리케이션에 자동으로 바인딩할 수 있습니다. 이 문서에서는 Azure Cosmos DB 데이터베이스에 애플리케이션을 바인딩하는 방법을 보여 줍니다.

필수 조건:

* 배포된 Azure Spring Cloud 인스턴스. [Azure CLI를 통해 배포에 대한 빠른 시작](./quickstart.md)을 수행하여 시작하세요.
* 최소 수준의 기여자 권한이 있는 Azure Cosmos DB 계정

## <a name="prepare-your-java-project"></a>Java 프로젝트 준비

1. Azure Spring Cloud 애플리케이션의 pom.xml 파일에 다음 종속성 중 하나를 추가합니다. API 유형에 적합한 종속성을 선택합니다.

    * API 형식: Core(SQL)

      ```xml
      <dependency>
          <groupId>com.azure.spring</groupId>
          <artifactId>azure-spring-boot-starter-cosmos</artifactId>
          <version>3.6.0</version>
      </dependency>
      ```

    * API 형식: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API 형식: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API 형식: Azure 테이블

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. `az spring-cloud app deploy`를 실행하여 현재 앱을 업데이트하거나, `az spring-cloud app deployment create`를 실행하여 이 변경 사항에 대한 새 배포를 만듭니다.

## <a name="bind-your-app-to-the-azure-cosmos-db"></a>앱을 Azure Cosmos DB에 바인딩

#### <a name="service-binding"></a>[서비스 바인딩](#tab/Service-Binding)
Azure Cosmos DB에는 바인딩을 지원하는 다음 5가지 API 형식이 있습니다. 아래 절차에서는 이를 사용하는 방법을 보여 줍니다.

1. Azure Cosmos DB 데이터베이스를 만듭니다. 도움말은 [데이터베이스 만들기](../cosmos-db/create-cosmosdb-resources-portal.md)에 대한 빠른 시작을 참조하세요.

1. 데이터베이스의 이름을 기록합니다. 이 절차에서 데이터베이스 이름은 **testdb** 입니다.

1. Azure Portal에서 Azure Spring Cloud 서비스 페이지로 이동합니다. **애플리케이션 대시보드** 로 이동하여 Azure Cosmos DB에 바인딩할 애플리케이션을 선택합니다. 이 애플리케이션은 이전 단계에서 업데이트하거나 배포한 애플리케이션과 동일합니다.

1. **서비스 바인딩** 을 선택하고 **서비스 바인딩 만들기** 를 선택합니다. 양식을 작성하려면 다음을 선택합니다.
   * **바인딩 유형** 값 **Azure Cosmos DB**
   * API 형식
   * 데이터베이스 이름
   * Azure Cosmos DB 계정

    > [!NOTE]
    > Cassandra를 사용하는 경우 데이터베이스 이름에 대한 키 공간을 사용합니다.

1. 애플리케이션 페이지에서 **다시 시작** 을 선택하여 애플리케이션을 다시 시작합니다.

1. 서비스가 올바르게 바인딩되었는지 확인하려면 바인딩 이름을 선택하고 세부 정보를 확인합니다. `property` 필드는 다음 예제와 비슷합니다.

    ```properties
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)
다음 Terraform 스크립트는 Azure Cosmos DB MongoDB API를 사용하여 Azure Spring Cloud 앱을 설정하는 방법을 보여 줍니다.

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_cosmosdb_account" "cosmosdb" {
  name                = "cosmosacct-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  offer_type          = "Standard"
  kind                = "MongoDB"

  consistency_policy {
    consistency_level = "Session"
  }

  geo_location {
    failover_priority = 0
    location          = azurerm_resource_group.example.location
  }
}

resource "azurerm_cosmosdb_mongo_database" "cosmosdb" {
  name                = "cosmos-${var.application_name}-001"
  resource_group_name = azurerm_cosmosdb_account.cosmosdb.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmosdb.name
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "${var.application_name}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "${var.application_name}-app"
  resource_group_name = azurerm_resource_group.example.name
  service_name        = azurerm_spring_cloud_service.example.name
  is_public           = true
  https_only          = true
}

resource "azurerm_spring_cloud_java_deployment" "example" {
  name                = "default"
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  cpu                 = 2
  memory_in_gb        = 4
  instance_count      = 2
  jvm_options         = "-XX:+PrintGC"
  runtime_version     = "Java_11"

  environment_variables = {
    "azure.cosmosdb.uri" : azurerm_cosmosdb_account.cosmosdb.connection_strings[0]
    "azure.cosmosdb.database" : azurerm_cosmosdb_mongo_database.cosmosdb.name
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Spring Cloud 애플리케이션을 Azure Cosmos DB 데이터베이스에 바인딩하는 방법을 알아보았습니다. 서비스를 애플리케이션에 바인딩하는 방법에 대한 자세한 내용은 [Azure Cache for Redis 캐시에 바인딩](./how-to-bind-redis.md)을 참조하세요.
