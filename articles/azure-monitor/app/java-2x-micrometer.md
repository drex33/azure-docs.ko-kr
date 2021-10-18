---
title: Azure Application Insights Java SDK에 Micrometer를 사용하는 방법
description: Micrometer를 Application Insights Spring Boot 및 Spring Boot 이외 애플리케이션에 사용하기 위한 단계별 가이드입니다.
ms.topic: conceptual
ms.custom: devx-track-java
ms.date: 11/01/2018
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 20d43fca48bfcdf264ae7c008c5fa406b7d5d719
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133940"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>Azure Application Insights Java SDK로 Micrometer를 사용하는 방법(권장되지 않음)

> [!CAUTION]
> 이 문서는 더 이상 권장되지 않는 Application Insights Java 2.x에 적용됩니다.
>
> 최신 버전에 대한 문서는 [Application Insights Java 3.x](./java-in-process-agent.md)에서 찾을 수 있습니다.

Micrometer 애플리케이션 모니터링은 JVM 기반 애플리케이션 코드의 메트릭을 측정하며, 자주 사용하는 모니터링 시스템으로 데이터를 내보낼 수 있습니다. 이 문서에서는 Spring Boot 및 Spring Boot 이외 애플리케이션 모두에 대해 Application Insights에 Micrometer를 사용하는 방법을 안내합니다.

## <a name="using-spring-boot-15x"></a>Spring Boot 1.5x 사용
pom.xml 또는 build.gradle 파일에 다음 종속성을 추가합니다. 
* [Application Insights spring-boot-starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 이상
* Micrometer Azure Registry 1.1.0 이상
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 이상(Spring 프레임워크의 자동 구성 코드 지원)
* [ApplicationInsights 리소스](./create-new-resource.md)

단계

1. Spring Boot 애플리케이션의 pom.xml 파일을 업데이트하고 그 안에 다음 종속성을 추가합니다.

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. 다음 속성을 사용하여 Application Insights 계측 키로 application.properties 또는 yml 파일을 업데이트합니다.

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. 애플리케이션 빌드 및 실행
2. 미리 집계된 메트릭이 Azure Monitor에 자동 수집된 상태로 가동 및 실행되어야 합니다. Application Insights Spring Boot Starter를 미세 조정하는 자세한 방법은 [GitHub에 대한 readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md)를 참조하세요.

## <a name="using-spring-2x"></a>Spring 2.x 사용

pom.xml 또는 build.gradle 파일에 다음 종속성을 추가합니다.

* Application Insights Spring-boot-starter 2.1.2 이상
* Azure-spring-boot-metrics-starters 2.0.7 이상
* [Application Insights 리소스](./create-new-resource.md)

단계:

1. Spring Boot 애플리케이션의 pom.xml 파일을 업데이트하고 그 안에 다음 종속성을 추가합니다.

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. 다음 속성을 사용하여 Application Insights 계측 키로 application.properties 또는 yml 파일을 업데이트합니다.

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. 애플리케이션 빌드 및 실행
4. 미리 집계된 메트릭이 Azure Monitor에 자동 수집된 상태로 실행되어야 합니다. Application Insights Spring Boot Starter를 미세 조정하는 자세한 방법은 [GitHub에 대한 readme](https://github.com/Microsoft/azure-spring-boot/releases/latest)를 참조하세요.

기본 메트릭:

*    Tomcat, JVM, Logback 메트릭, Log4J 메트릭, 가동 시간 메트릭, 프로세서 메트릭, FileDescriptorMetrics 메트릭에 대해 자동으로 구성된 메트릭입니다.
*    예를 들어 Netflix Hystrix가 클래스 경로에 있는 경우 해당 메트릭도 함께 가져옵니다. 
*    해당 bean을 추가하여 다음 메트릭을 사용할 수 있습니다. 
        - CacheMetrics(CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 메트릭 
        - Kafka 메트릭 



자동 메트릭 수집을 해제하는 방법: 

- JVM 메트릭: 
    - management.metrics.binders.jvm.enabled=false 
- Logback 메트릭: 
    - management.metrics.binders.logback.enabled=false
- 가동 시간 메트릭: 
    - management.metrics.binders.uptime.enabled=false 
- 프로세서 메트릭:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- 클래스 경로의 라이브러리가 다음인 경우 Hystrix 메트릭: 
    - management.metrics.binders.hystrix.enabled=false 
- 클래스 경로의 라이브러리가 다음인 경우 AspectJ 메트릭: 
    - spring.aop.enabled=false 

> [!NOTE]
> Spring Boot 애플리케이션의 application.properties 또는 application.yml 파일에서 위의 속성을 지정합니다.

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Spring Boot 웹 애플리케이션이 없는 Micrometer 사용

pom.xml 또는 build.gradle 파일에 다음 종속성을 추가합니다.

* Application Insights Web Auto 2.5.0 이상
* Micrometer Azure Registry 1.1.0 이상
* [Application Insights 리소스](./create-new-resource.md)

단계:

1. pom.xml 또는 build.gradle 파일에 다음 종속성을 추가합니다.

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>

        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. 다음 리소스 폴더에 `ApplicationInsights.xml` 파일을 넣습니다.

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>

       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>

    </ApplicationInsights>
    ```

3. 샘플 Servlet 클래스(타이머 메트릭 내보냄):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {

          private static final long serialVersionUID = -4751096228274971485L;

          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {

            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");

        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }

        }

    ```

4. 샘플 구성 클래스:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {

           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {

         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}

             @Override
             public boolean enabled() {
                 return false;
             }
         };

      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);

             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);

           }

           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {

           }
         }
    ```

메트릭에 대한 자세한 내용은 [Micrometer 설명서](https://micrometer.io/docs/)를 참조하세요.

다양한 메트릭 유형을 만드는 방법에 대한 다른 샘플 코드는 [공식 Micrometer GitHub 리포지토리](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)에서 제공합니다.

## <a name="how-to-bind-additional-metrics-collection"></a>추가 메트릭 컬렉션을 바인딩하는 방법

### <a name="springbootspring"></a>SpringBoot/Spring

각 메트릭 범주의 bean을 만듭니다. 예를 들어, Guava 캐시 메트릭이라고 가정해 보겠습니다.

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
기본적으로 사용되지 않는 몇 가지 메트릭이 있지만 위의 방법으로 바인딩할 수 있습니다. 전체 목록은 [공식 Micrometer GitHub 리포지토리](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )를 참조하세요.

### <a name="non-spring-apps"></a>Spring 이외 앱
구성 파일에 다음 바인딩 코드를 추가합니다.
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>다음 단계

* Micrometer에 대한 보다 자세한 정보는 공식 [Micrometer 설명서](https://micrometer.io/docs)를 참조하세요.
* Azure의 Spring에 대한 자세한 내용은 공식 [Azure의 Spring 설명서](/java/azure/spring-framework/)를 참조하세요.
