---
title: Azure Spring Cloud에 배포할 애플리케이션을 준비하는 방법
description: Azure Spring Cloud에 배포할 애플리케이션을 준비하는 방법을 알아봅니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/06/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: faa4c57a4fc5e75d0e6262833c27833e9069fb30
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568077"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud에 배포할 애플리케이션 준비

::: zone pivot="programming-language-csharp"
Azure Spring Cloud는 Steeltoe 앱을 호스팅, 모니터링, 크기 조정, 업데이트하기 위한 강력한 서비스를 제공합니다. 이 아티클에서는 Azure Spring Cloud에 배포하기 위해 기존 Steeltoe 애플리케이션을 준비하는 방법을 보여줍니다.

이 문서에서는 Azure Spring Cloud에서 .NET Core Steeltoe 앱을 실행하는 데 필요한 종속성, 구성 및 코드를 설명합니다. Azure Spring Cloud에 애플리케이션을 배포하는 방법에 대한 자세한 내용은 [첫 번째 Azure Spring Cloud 애플리케이션 배포](./quickstart.md)를 참조하세요.

>[!Note]
> Azure Spring Cloud에 대한 Steeltoe 지원은 현재 공개 미리 보기로 제공됩니다. 퍼블릭 미리 보기 제품을 통해 고객은 공식 릴리스 전에 새로운 기능을 시험해 볼 수 있습니다.  퍼블릭 미리 보기 기능 및 서비스는 프로덕션 용도로 사용되지 않습니다.  미리 보기 동안 제공되는 지원에 대한 자세한 내용은 [FAQ](https://azure.microsoft.com/support/faq/)를 참조하거나 [지원 요청](../azure-portal/supportability/how-to-create-azure-support-request.md)을 제출하세요.

## <a name="supported-versions"></a>지원되는 버전

Azure Spring Cloud는 다음을 지원합니다.

* .NET Core 3.1
* Steeltoe 2.4 및 3.0

## <a name="dependencies"></a>종속성

Steeltoe 2.4의 경우 프로젝트 파일에 최신 [Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) 패키지를 추가합니다.

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Steeltoe 3.0의 경우 프로젝트 파일에 최신 [Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) 패키지를 추가합니다.

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Program.cs 업데이트

`Program.Main` 메서드에서 `UseAzureSpringCloudService` 메서드를 호출합니다.

Steeltoe 2.4.4의 경우 `ConfigureWebHostDefaults` 다음 `UseAzureSpringCloudService`, 다음이 호출된 경우 `AddConfigServer`를 호출합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Steeltoe 3.0.0의 경우 `ConfigureWebHostDefaults` 이전 및 Steeltoe 구성 코드 이전에 `UseAzureSpringCloudService`를 호출합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Eureka 서버 서비스 검색 사용

앱이 Azure Spring Cloud에서 실행될 때 사용되는 구성 원본에서 `spring.application.name`을 프로젝트가 배포될 Azure Spring Cloud 앱과 동일한 이름으로 설정합니다.

예를 들어 `EurekaDataProvider`라는 이름의 .NET 프로젝트를 `planet-weather-provider`라는 이름의 Azure Spring Cloud 앱에 배포하는 경우 *appSettings.json* 파일은 다음 JSON을 포함해야 합니다.

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>서비스 검색 사용

Eureka 서버 서비스 검색을 사용하여 서비스를 호출하려면 `app_name`이 대상 앱의 `spring.application.name` 값인 `http://<app_name>`에 HTTP 요청을 수행합니다. 예를 들어 다음 코드는 `planet-weather-provider` 서비스를 호출합니다.

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```

::: zone-end

::: zone pivot="programming-language-java"
이 항목에서는 Azure Spring Cloud에 배포하기 위해 기존 Java Spring 애플리케이션을 준비하는 방법을 보여줍니다. 제대로 구성된 경우, Azure Spring Cloud는 Java Spring Cloud 애플리케이션을 모니터링하고, 크기를 조정하고, 업데이트할 수 있는 강력한 서비스를 제공합니다.

이 예제를 실행하기 전에 [기본 빠른 시작](./quickstart.md)을 시도해 볼 수 있습니다.

다른 예제에서는 POM 파일이 구성된 경우 Azure Spring Cloud에 애플리케이션을 배포하는 방법을 설명합니다.

* [첫 번째 앱 시작](./quickstart.md)
* [마이크로서비스 빌드 및 실행](./quickstart-sample-app-introduction.md)

이 문서에서는 필요한 종속성과 이것을 POM 파일에 추가하는 방법을 설명합니다.

## <a name="java-runtime-version"></a>Java Runtime 버전

Spring/Java 애플리케이션만 Azure Spring Cloud에서 실행할 수 있습니다.

Azure Spring Cloud는 Java 8 및 Java 11을 모두 지원합니다. 호스팅 환경에는 Azure용 Azul Zulu OpenJDK의 최신 버전이 포함되어 있습니다. Azure용 Azul Zulu OpenJDK에 대한 자세한 내용은 [JDK 설치](/azure/developer/java/fundamentals/java-jdk-install)를 참조하세요.

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 및 Spring Cloud 버전

Azure Spring Cloud에 배포할 기존 Spring Boot 애플리케이션을 준비하려면 다음 섹션에 표시된 대로 애플리케이션 POM 파일에 Spring Boot 및 Spring Cloud 종속성을 포함합니다.

Azure Spring Cloud는 출시 후 1개월 이내에 최신 Spring Boot 또는 Spring Cloud 릴리스를 지원합니다. 지원되는 Spring Boot 버전은 [Spring Boot Releases](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases)에서, Spring Cloud 버전은 [Spring Cloud Releases](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases)에서 얻을 수 있습니다. 

아래 표에는 지원되는 Spring Boot 및 Spring Cloud 조합이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전
---|---
2.3.x | Hoxton.SR8+
2.4.x, 2.5.x | 2020.0(즉, Ilford +)

> [!NOTE]
> - Spring Boot를 2.5.2 또는 2.4.8로 업그레이드하여 CVE 보고서 [CVE-2021-22119: spring-security-oauth2-client를 사용한 서비스 거부 공격](https://tanzu.vmware.com/security/cve-2021-22119)을 해결하세요. Spring Security를 ​​사용하는 경우 5.5.1, 5.4.7, 5.3.10 또는 5.2.11로 업그레이드하세요.
> - 앱과 Spring Cloud Service Registry 간의 TLS 인증을 통해 Spring Boot 2.4.0에서 문제가 확인되었습니다. 2.4.1 이상을 사용하세요. 2\.4.0을 계속 사용하는 경우 해결 방법은 [FAQ](./faq.md?pivots=programming-language-java#development)를 참조하세요.

### <a name="dependencies-for-spring-boot-version-2223"></a>Spring Boot 버전 2.2/2.3에 대한 종속성

Spring Boot 버전 2.2의 경우, 애플리케이션 POM 파일에 다음 종속성을 추가합니다.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>Spring Boot 버전 2.4에 대한 종속성

Spring Boot 버전 2.2의 경우, 애플리케이션 POM 파일에 다음 종속성을 추가합니다.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.8</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> 부트스트랩 구성에 `server.port`를 지정하지 마십시오. Azure Spring Cloud는 이 설정을 고정 포트 번호로 재정의합니다. 또한 이 설정을 준수하고 코드에서 서버 포트를 지정하지 마십시오.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Azure Spring Cloud 기능을 사용하도록 설정하기 위한 기타 권장 종속성

서비스 레지스트리에서 분산 추적까지 Azure Spring Cloud의 다양한 기본 제공 기능을 사용하도록 설정하려면 애플리케이션에 다음 종속성도 포함해야 합니다. 특정 앱에 해당하는 기능이 필요하지 않은 경우 이러한 종속성 중 일부를 삭제할 수 있습니다.

### <a name="service-registry"></a>서비스 레지스트리

관리형 Azure Service Registry 서비스를 사용하려면 아래와 같이 `spring-cloud-starter-netflix-eureka-client` 종속성을 pom.xml 파일에 포함합니다.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

서비스 레지스트리 서버의 엔드포인트는 앱에 환경 변수로 자동 삽입됩니다. 애플리케이션이 서비스 레지스트리 서버에 자체적으로 등록되고, 기타 종속 마이크로서비스를 검색할 수 있습니다.

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 주석

애플리케이션 소스 코드에 다음 주석을 추가합니다.

```java
@EnableDiscoveryClient
```

예를 들어 이전 예제의 piggymetrics 애플리케이션을 참조하세요.

```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>분산 구성

분산 구성을 사용하도록 설정하려면, pom.xml 파일의 종속성 섹션에 `spring-cloud-config-client` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

> [!WARNING]
> 부트스트랩 구성에 `spring.cloud.config.enabled=false`를 지정하지 마세요. 그렇지 않으면 애플리케이션이 구성 서버 작업을 중지합니다.

### <a name="metrics"></a>메트릭

다음과 같이 pom.xml 파일의 종속성 섹션에 `spring-boot-starter-actuator` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 JMX 엔드포인트에서 메트릭을 정기적으로 끌어옵니다. Azure Portal을 사용하여 메트릭을 시각화할 수 있습니다.

 > [!WARNING]
 > 구성 속성에서 `spring.jmx.enabled=true`로 지정하십시오. 그러지 않으면 Azure Portal에서 메트릭을 시각화할 수 없습니다.

### <a name="distributed-tracing"></a>분산 추적

또한, Azure Application Insights 인스턴스가 Azure Spring Cloud 서비스 인스턴스와 작동하도록 설정해야 합니다. Azure Spring Cloud에서 Application Insights를 사용하는 방법에 대한 자세한 내용은 [분산 추적에 대한 설명서](./how-to-distributed-tracing.md)를 참조하세요.

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3

pom.xml 파일의 종속성 섹션에 다음 `spring-cloud-starter-sleuth` 및 `spring-cloud-starter-zipkin` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

#### <a name="spring-boot-24"></a>Spring Boot 2.4

*pom.xml* 파일의 종속성 섹션에 다음 `spring-cloud-sleuth-zipkin` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>참고 항목

* [애플리케이션 로그 및 메트릭 분석](./diagnostic-services.md)
* [구성 서버 설정](./how-to-config-server.md)
* [Azure Spring Cloud에서 분산 추적 사용](./how-to-distributed-tracing.md)
* [Spring 빠른 시작 가이드](https://spring.io/quickstart)
* [Spring Boot 설명서](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>다음 단계

이 항목에서는 Azure Spring Cloud에 배포하기 위해 Java Spring 애플리케이션을 구성하는 방법을 알아보았습니다. 구성 서버 인스턴스를 설정하는 방법을 알아보려면 [구성 서버 인스턴스 설정](./how-to-config-server.md)을 참조하세요.

GitHub에서 더 많은 샘플을 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end
