---
title: Azure 스프링 클라우드의 응용 프로그램에 Azure Database for MySQL 인스턴스를 바인딩하는 방법
description: Azure 스프링 클라우드의 응용 프로그램에 Azure Database for MySQL 인스턴스를 바인딩하는 방법에 대해 알아봅니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 7c48b9eee1ff727cbd3d2ab7204045d962a21f44
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490348"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-application-in-azure-spring-cloud"></a>Azure Database for MySQL 인스턴스를 Azure Spring Cloud의 애플리케이션에 바인딩

**이 문서는 다음에 적용됩니다.** ✔️ Java

Azure Spring Cloud를 사용하면 Spring Boot 애플리케이션을 수동으로 구성하는 대신, 선택한 Azure 서비스를 애플리케이션에 자동으로 바인딩할 수 있습니다. 이 문서에서는 애플리케이션을 Azure Database for MySQL 인스턴스에 바인딩하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* 배포된 Azure Spring Cloud 인스턴스
* Azure Database for MySQL 계정
* Azure CLI

Azure 스프링 클라우드 인스턴스가 배포 되지 않은 경우 빠른 시작: 첫 번째 스프링 클라우드 앱을 배포 하 [는 Azure Portal을 사용 하 여 Azure 스프링 클라우드에서 응용 프로그램 시작](./quickstart.md) 의 지침을 따르세요.

## <a name="prepare-your-java-project"></a>Java 프로젝트 준비

1. 프로젝트의 *pom.xml* 파일에서 다음 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```

1. *application.properties* 파일에서 `spring.datasource.*` 속성을 제거합니다.

1. `az spring-cloud app deploy`를 실행하여 현재 앱을 업데이트하거나, `az spring-cloud app deployment create`를 실행하여 이 변경 사항에 대한 새 배포를 만듭니다.

## <a name="bind-your-app-to-the-azure-database-for-mysql-instance"></a>Azure Database for MySQL 인스턴스에 앱 바인딩

#### <a name="service-binding"></a>[서비스 바인딩](#tab/Service-Binding)
1. Azure Database for MySQL 계정의 관리 사용자 이름 및 암호를 기록해 둡니다.

1. 서버에 연결하고, MySQL 클라이언트에서 **testdb** 라는 데이터베이스를 만든 다음, 관리자가 아닌 새 계정을 만듭니다.

1. Azure Portal의 **Azure Spring Cloud** 서비스 페이지에서 **애플리케이션 대시보드** 를 찾은 다음, Azure Database for MySQL 인스턴스에 바인딩할 애플리케이션을 선택합니다.  이 애플리케이션은 이전 단계에서 업데이트하거나 배포한 것과 동일한 애플리케이션입니다.

1. **서비스 바인딩** 을 선택한 다음, **서비스 바인딩 만들기** 단추를 선택합니다.

1. 양식을 작성하고, **바인딩 유형** 으로 **Azure MySQL** 을 선택하고, 이전에 사용한 것과 동일한 데이터베이스 이름과 첫 번째 단계에서 적어둔 사용자 이름 및 암호를 사용합니다.

1. 앱을 다시 시작하면 이 바인딩이 작동합니다.

1. 서비스 바인딩이 올바른지 확인하려면 바인딩 이름을 선택하고 해당 세부 정보를 확인합니다. `property` 필드는 다음과 같이 표시되어야 합니다.

    ```properties
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

다음 Terraform 스크립트는 Azure Database for MySQL을 사용하여 Azure Spring Cloud 앱을 설정하는 방법을 보여 줍니다.

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

variable "administrator_login" {
  type        = string
  description = "The MySQL administrator login"
  default     = "myadmin"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "random_password" "password" {
  length           = 32
  special          = true
  override_special = "_%@"
}

resource "azurerm_mysql_server" "database" {
  name                = "mysql-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location

  administrator_login          = var.administrator_login
  administrator_login_password = random_password.password.result

  sku_name                          = "B_Gen5_1"
  storage_mb                        = 5120
  version                           = "5.7"
  auto_grow_enabled                 = true
  backup_retention_days             = 7
  geo_redundant_backup_enabled      = false
  infrastructure_encryption_enabled = false
  public_network_access_enabled     = true
  ssl_enforcement_enabled           = true
  ssl_minimal_tls_version_enforced  = "TLS1_2"
}

resource "azurerm_mysql_database" "database" {
  name                = "mysqldb-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}

# This rule is to enable the 'Allow access to Azure services' checkbox
resource "azurerm_mysql_firewall_rule" "database" {
  name                = "mysqlfw-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  start_ip_address    = "0.0.0.0"
  end_ip_address      = "0.0.0.0"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "example-springcloudapp"
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
    "spring.datasource.url" : "jdbc:mysql://${azurerm_mysql_server.database.fqdn}:3306/${azurerm_mysql_database.database.name}?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC"
    "spring.datasource.username" : "${var.administrator_login}@${azurerm_mysql_server.database.name}"
    "spring.datasource.password" : random_password.password.result
    "spring.jpa.properties.hibernate.dialect" : "org.hibernate.dialect.MySQL5InnoDBDialect"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 스프링 클라우드의 응용 프로그램을 Azure Database for MySQL 인스턴스에 바인딩하는 방법에 대해 알아보았습니다. 응용 프로그램에 서비스를 바인딩하는 방법에 대해 자세히 알아보려면 [Azure 스프링 클라우드의 응용 프로그램에 Azure Cosmos DB 데이터베이스 바인딩](./how-to-bind-cosmos.md)을 참조 하세요.
