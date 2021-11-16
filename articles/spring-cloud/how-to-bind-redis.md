---
title: Azure 스프링 클라우드의 응용 프로그램에 Redis 용 Azure Cache 바인딩
description: Azure 스프링 클라우드의 응용 프로그램에 Redis 용 Azure Cache를 바인딩하는 방법에 대해 알아봅니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 9f357526b64b34d7348114ce71840a5f4aafc394
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492504"
---
# <a name="bind-azure-cache-for-redis-to-your-application-in-azure-spring-cloud"></a>Azure 스프링 클라우드의 응용 프로그램에 Redis 용 Azure Cache 바인딩

**이 문서는 다음에 적용됩니다.** ✔️ Java

Spring Boot 애플리케이션을 수동으로 구성하는 대신 Azure Spring Cloud를 사용하여 선택한 Azure 서비스를 사용자 애플리케이션에 자동으로 바인딩할 수 있습니다. 이 문서에서는 Azure Cache for Redis에 애플리케이션을 바인딩하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* 배포된 Azure Spring Cloud 인스턴스
* Azure Cache for Redis 서비스 인스턴스
* Azure CLI용 Azure Spring Cloud 확장

배포된 Azure Spring Cloud 인스턴스가 없는 경우 [Azure Spring Cloud 앱 배포에 대한 빠른 시작](./quickstart.md)의 단계를 따릅니다.

## <a name="prepare-your-java-project"></a>Java 프로젝트 준비

1. 프로젝트의 pom.xml 파일에 다음 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```

1. `application.properties` 파일에서 `spring.redis.*` 속성을 제거합니다.

1. `az spring-cloud app update`를 사용하여 현재 배포를 업데이트하거나 `az spring-cloud app deployment create`를 사용하여 새 배포를 만듭니다.

## <a name="bind-your-app-to-the-azure-cache-for-redis"></a>Azure Cache for Redis에 앱 바인딩

#### <a name="service-binding"></a>[서비스 바인딩](#tab/Service-Binding)
1. Azure Portal에서 Azure Spring Cloud 서비스 페이지로 이동합니다. **Application Dashboard** 로 이동하여 Azure Cache for Redis에 바인딩할 애플리케이션을 선택합니다. 이 애플리케이션은 이전 단계에서 업데이트하거나 배포한 애플리케이션과 동일합니다.

1. **서비스 바인딩** 을 선택하고 **서비스 바인딩 만들기** 를 선택합니다. **바인딩 유형** 값 **Azure Cache for Redis**, 사용자의 Azure Cache for Redis 서버 및 **기본** 키 옵션을 선택하도록 양식을 작성합니다.

1. 앱을 다시 시작합니다. 이제 바인딩이 작동합니다.

1. 서비스 바인딩이 올바른지 확인하려면 바인딩 이름을 선택하고 해당 세부 정보를 확인합니다. `property` 필드는 다음과 같이 표시되어야 합니다.

    ```properties
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

다음 Terraform 스크립트는 Azure Cache for Redis를 사용하여 Azure Spring Cloud 앱을 설정하는 방법을 보여 줍니다.

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

resource "azurerm_redis_cache" "redis" {
  name                = "redis-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  capacity            = 0
  family              = "C"
  sku_name            = "Standard"
  enable_non_ssl_port = false
  minimum_tls_version = "1.2"
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
    "spring.redis.host"     = azurerm_redis_cache.redis.hostname
    "spring.redis.password" = azurerm_redis_cache.redis.primary_access_key
    "spring.redis.port"     = "6380"
    "spring.redis.ssl"      = "true"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 스프링 클라우드의 응용 프로그램을 Redis 용 Azure Cache에 바인딩하는 방법에 대해 알아보았습니다. 서비스를 애플리케이션에 바인딩하는 방법에 대한 자세한 내용은 [Azure Database for MySQL 인스턴스에 바인딩](./how-to-bind-mysql.md)을 참조하세요.
