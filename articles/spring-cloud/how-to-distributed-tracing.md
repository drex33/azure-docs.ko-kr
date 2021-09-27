---
title: Azure Spring Cloud에서 분산 추적 사용
description: Azure Application Insights를 통해 Spring Cloud의 분산 추적을 사용하는 방법을 알아봅니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: bacf4f808a7e963c11e8790636dabe1f3f4e4a47
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129044192"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud-deprecated"></a>Azure Spring Cloud 분산 추적 사용(사용되지 않습니다.)

Azure Spring Cloud의 분산 추적 도구를 사용하면 복잡한 문제를 쉽게 디버그하고 모니터링할 수 있습니다. Azure Spring Cloud는 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth)를 Azure의 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 통합합니다. 이러한 통합은 Azure Portal의 강력한 분산 추적 기능을 제공합니다.

::: zone pivot="programming-language-csharp"
이 문서에서는 .NET Core Steeltoe 앱에서 분산 추적을 사용하도록 설정하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 절차를 따르려면 사전에 [Azure Spring Cloud에 배포하기 위해 준비한](how-to-prepare-app-deployment.md) Steeltoe 앱이 필요합니다.

## <a name="dependencies"></a>종속성

Steeltoe 2.4.4의 경우 다음 NuGet 패키지를 추가합니다.

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe.Management.ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

Steeltoe 3.0.0의 경우 다음 NuGet 패키지를 추가합니다.

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>Startup.cs 업데이트

1. Steeltoe 2.4.4의 경우 `ConfigureServices` 메서드에서 `AddDistributedTracing` 및 `AddZipkinExporter`를 호출합니다.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   Steeltoe 3.0.0의 경우 `ConfigureServices` 메서드에서 `AddDistributedTracing`을 호출합니다.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. Steeltoe 2.4.4의 경우 `Configure` 메서드에서 `UseTracingExporter`를 호출합니다.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   Steeltoe 3.0.0의 경우에는 `Configure` 메서드에 변경이 필요하지 않습니다.

## <a name="update-configuration"></a>구성 업데이트

앱이 Azure Spring Cloud에서 실행될 때 사용할 구성 원본에 다음 설정을 추가합니다.

1. `management.tracing.alwaysSample`를 true로 설정합니다.

2. Eureka 서버, 구성 서버 및 사용자 앱 간에 전송된 추적 범위를 보려면 `management.tracing.egressIgnorePattern`을 "/api/v2/spans|/v2/apps/. */permissions|/eureka/.* |/oauth/.*"로 설정합니다.

예를 들어 *appsettings.json* 에는 다음과 같은 속성이 포함됩니다.

```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

.NET Core Steeltoe 앱의 분산 추적에 대한 자세한 내용은 Steeltoe 설명서의 [분산 추적](https://docs.steeltoe.io/api/v3/tracing/)을 참조하세요.
::: zone-end
::: zone pivot="programming-language-java"
이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure Portal에서 분산 추적을 사용하도록 설정
> * 애플리케이션에 Spring Cloud Sleuth 추가
> * 마이크로서비스 애플리케이션에 대한 종속성 맵 보기
> * 다양한 필터로 추적 데이터 검색

## <a name="prerequisites"></a>필수 구성 요소

다음 절차를 수행하려면 이미 프로비저닝되어 실행 중인 Azure Spring Cloud 서비스가 필요합니다. [첫 번째 Azure Spring Cloud 애플리케이션 배포](./quickstart.md)를 완료하여 Azure Spring Cloud 서비스를 프로비전하고 실행합니다.

## <a name="add-dependencies"></a>종속성 추가

1. 다음 줄을 application.properties 파일에 추가합니다.

   ```xml
   spring.zipkin.sender.type = web
   ```

   이렇게 변경한 후 Zipkin 발신자를 웹에 보낼 수 있습니다.

1. [Azure Spring Cloud 애플리케이션 준비에 대한 가이드](how-to-prepare-app-deployment.md)를 수행한 경우, 이 단계를 건너뛸 수 있습니다. 그렇지 않으면 로컬 개발 환경으로 이동하고, 다음 Spring Cloud Sleuth 종속성을 포함하도록 pom.xml 파일을 편집합니다.

    * Spring Boot 버전 2.4.x 미만

      ```xml
      <dependencyManagement>
          <dependencies>
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-sleuth</artifactId>
                  <version>${spring-cloud-sleuth.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-sleuth</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-zipkin</artifactId>
          </dependency>
      </dependencies>
      ```

    * Spring Boot 버전 2.4.x 이상

      ```xml
      <dependencyManagement>
          <dependencies>
            <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-sleuth</artifactId>
                  <version>${spring-cloud-sleuth.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-sleuth</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-sleuth-zipkin</artifactId>
           </dependency>
      </dependencies>
      ```

1. Azure Spring Cloud 서비스에서 이러한 변경 내용을 반영하도록 다시 빌드하고 배포합니다.

## <a name="modify-the-sample-rate"></a>샘플 속도 수정

샘플 속도를 수정하여 원격 분석 데이터를 수집하는 속도를 변경할 수 있습니다. 예를 들어, 절반을 자주 샘플링하려면 application.properties 파일을 열어서 다음 줄을 변경합니다.

```xml
spring.sleuth.sampler.probability=0.5
```

애플리케이션을 이미 빌드하고 배포한 경우, 샘플 속도를 수정할 수 있습니다. Azure CLI나 Azure Portal에서 이전 행을 환경 변수로 추가하면 됩니다.
::: zone-end

## <a name="enable-application-insights"></a>Application Insights 사용

1. Azure Portal에서 Azure Spring Cloud 서비스 페이지로 이동합니다.
1. **모니터링**  페이지에서 **분산 추적** 을 선택합니다.
1. **설정 편집** 을 선택하여 새 설정을 편집하거나 추가합니다.
1. 새 Application Insights 쿼리를 만들거나 기존 쿼리를 선택합니다.
1. 모니터링할 로깅 범주를 선택하고 보존 시간(일)을 지정합니다.
1. **적용** 을 선택하여 추적을 적용합니다.

## <a name="view-the-application-map"></a>애플리케이션 맵 보기

**분산 추적** 페이지로 돌아가서 **애플리케이션 맵 보기** 를 선택합니다. 애플리케이션 및 모니터링 설정의 시각적 표현을 검토합니다. 애플리케이션 맵 사용 방법을 알아보려면 [애플리케이션 맵: 분산 애플리케이션 심사](../azure-monitor/app/app-map.md)를 참조하세요.

## <a name="use-search"></a>검색 사용

검색 함수를 사용하여 기타 특정한 원격 분석 항목을 쿼리할 수 있습니다. **분산 추적** 페이지에서 **검색** 을 선택합니다. 검색 함수를 사용하는 방법에 대한 자세한 내용은 [Application Insights에서 검색 사용](../azure-monitor/app/diagnostic-search.md)을 참조하세요.

## <a name="use-application-insights"></a>Application Insights 사용

Application Insights는 애플리케이션 맵과 검색 기능 외에도 모니터링 기능을 제공합니다. Azure Portal에서 애플리케이션 이름을 검색한 다음, Application Insights 페이지를 열어서 모니터링 정보를 찾을 수 있습니다. 이러한 도구를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor 로그 쿼리](/azure/data-explorer/kusto/query/)를 확인하세요.

## <a name="disable-application-insights"></a>Application Insights를 사용하지 않도록 설정

1. Azure Portal에서 Azure Spring Cloud 서비스 페이지로 이동합니다.
1. **모니터링** 에서 **분산 추적** 을 선택합니다.
1. **사용 안 함** 을 선택하여 Application Insights를 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Spring Cloud에서 분산 추적을 사용하도록 설정하고 이해하는 방법을 알아보았습니다. 서비스를 애플리케이션에 바인딩하는 방법에 대한 자세한 내용은 [Azure Cosmos DB 데이터베이스를 Azure Spring Cloud 애플리케이션에 바인딩](./how-to-bind-cosmos.md)을 참조하세요.
