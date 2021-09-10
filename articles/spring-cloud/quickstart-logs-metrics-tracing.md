---
title: 빠른 시작 - 로그, 메트릭 및 추적을 사용하여 Azure Spring Cloud 앱 모니터링
description: 로그 스트리밍, 로그 분석, 메트릭 및 추적을 사용하여 Azure Spring Cloud에서 PetClinic 샘플 앱을 모니터링합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 04/23/2021
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4ff1666222eedecd944b94de428c7666b0998d03
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271580"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>빠른 시작: 로그, 메트릭 및 추적을 사용하여 Azure Spring Cloud 앱 모니터링

::: zone pivot="programming-language-csharp"
Azure Spring Cloud의 기본 제공 모니터링 기능을 사용하여 복잡한 문제를 디버그하고 모니터링할 수 있습니다. Azure Spring Cloud는 Steeltoe [분산 추적](https://docs.steeltoe.io/api/v3/tracing/)을 Azure의 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 통합합니다. 이 통합은 Azure Portal에서 강력한 로그, 메트릭 및 분산 추적 기능을 제공합니다.

다음 절차에서는 이전 빠른 시작에서 배포한 샘플 앱에서 로그 스트리밍, Log Analytics, 메트릭 및 분산 추적을 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 시리즈의 이전 빠른 시작을 완료합니다.

  * [Azure Spring Cloud 서비스 프로비저닝](./quickstart-provision-service-instance.md)
  * [Azure Spring Cloud 구성 서버 설정](./quickstart-setup-config-server.md)
  * [앱을 빌드 및 배포](./quickstart-deploy-apps.md)합니다.

## <a name="logs"></a>로그

Azure Spring Cloud에 대한 로그를 확인하는 방법에는 두 가지가 있습니다. 앱 인스턴스당 실시간 로그의 **로그 스트리밍** 또는 고급 쿼리 기능이 있는 집계 로그의 **Log Analytics**.

### <a name="log-streaming"></a>스트리밍 로그

다음 명령을 사용하여 Azure CLI에서 로그 스트리밍을 사용할 수 있습니다.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

다음 예제와 비슷한 출력이 표시됩니다.

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> `az spring-cloud app logs -h`를 사용하여 더 많은 매개 변수 및 로그 스트림 기능을 탐색합니다.

### <a name="log-analytics"></a>Log Analytics

1. Azure Portal에서 **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **로그** 를 선택합니다. Azure Spring Cloud에 대한 샘플 쿼리 중 하나에서 **실행** 을 선택합니다.

   [ ![Logs Analytics 항목](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)

1. 경고 및 오류 로그에 대한 표시를 제한하는 Where 절을 제거하도록 쿼리를 편집합니다.

1. 그런 다음, `Run`을 선택하면 로그가 표시됩니다. 쿼리 작성에 대한 자세한 지침은 [Azure Log Analytics 문서](../azure-monitor/logs/get-started-queries.md)를 참조하세요.

   [ ![Logs Analytics 쿼리 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. Log Analytics에서 사용되는 쿼리 언어에 대한 자세한 내용은 [Azure Monitor 로그 쿼리](/azure/data-explorer/kusto/query/)를 참조하세요. 중앙 집중식 클라이언트에서 모든 Log Analytics 로그를 쿼리하려면 [Azure Data Explorer](/azure/data-explorer/query-monitor-data)를 확인하세요.

## <a name="metrics"></a>메트릭

1. Azure Portal에서 **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **메트릭** 을 선택합니다. **메트릭** 드롭다운의 **성능(.NET)** 또는 **요청(.NET**)에서 .NET 메트릭 중 하나를 선택하여 첫 번째 메트릭을 추가하고 해당 메트릭에 대한 타임라인을 확인하려면 **집계** 의 `Avg`를 선택합니다.

   [ ![메트릭 항목 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)

1. 도구 모음에서 **필터 추가** 를 선택하고, `App=solar-system-weather`를 선택하여 **solar-system-weather** 앱에 대해서만 CPU 사용량을 확인합니다.

   [ ![메트릭에서 필터 사용 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. 이전 단계에서 만든 필터를 해제하여 **분할 적용** 을 선택하고, **값** 에 대해 `App`을 선택하여 다른 앱의 CPU 사용량을 확인합니다.

   [ ![메트릭에 분할 적용 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>분산된 추적

1. Azure Portal에서 **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **분산 추적** 을 선택합니다. 그런 다음, 오른쪽의 있는 **애플리케이션 맵 보기** 탭을 선택합니다.

   [ ![분산 추적 항목 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. 이제 앱 간의 호출 상태를 볼 수 있습니다.

   [ ![분산 추적 개요 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)

1. **solar-system-weather** 와 **planet-weather-provider** 사이의 링크를 선택하여 HTTP 메서드에 의한 가장 느린 호출과 같은 자세한 정보를 확인합니다.

   [![ 분산 추적 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)

1. 마지막으로, **성능** 조사를 선택하여 더 강력한 기본 제공 성능 분석을 탐색합니다.

   [ ![분산 추적 성능 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud의 기본 제공 모니터링 기능을 사용하여 복잡한 문제를 디버그하고 모니터링할 수 있습니다. Azure Spring Cloud는 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth)를 Azure의 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 통합합니다. 이 통합은 Azure Portal에서 강력한 로그, 메트릭 및 분산 추적 기능을 제공합니다. 다음 절차에서는 배포된 PetClinic 앱에서 로그 스트리밍, Log Analytics, 메트릭 및 분산 추적을 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이전 단계를 완료합니다.

* [Azure Spring Cloud의 인스턴스 프로비저닝](./quickstart-provision-service-instance.md)
* [구성 서버 설정](./quickstart-setup-config-server.md)
* [앱을 빌드 및 배포](./quickstart-deploy-apps.md)합니다.

## <a name="logs"></a>로그

Azure Spring Cloud에 대한 로그를 확인하는 방법에는 두 가지가 있습니다. 앱 인스턴스당 실시간 로그의 **로그 스트리밍** 또는 고급 쿼리 기능이 있는 집계 로그의 **Log Analytics**.

### <a name="log-streaming"></a>스트리밍 로그

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

다음 명령을 사용하여 Azure CLI에서 로그 스트리밍을 사용할 수 있습니다.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

다음과 같은 로그가 표시됩니다.

[ ![Azure CLI에서 로그 스트리밍](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> `az spring-cloud app logs -h`를 사용하여 더 많은 매개 변수 및 로그 스트림 기능을 탐색합니다.

Log Analytics에서 사용되는 쿼리 언어에 대한 자세한 내용은 [Azure Monitor 로그 쿼리](/azure/data-explorer/kusto/query/)를 참조하세요. 중앙 집중식 클라이언트에서 모든 Log Analytics 로그를 쿼리하려면 [Azure Data Explorer](/azure/data-explorer/query-monitor-data)를 확인하세요.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Azure Toolkit for IntelliJ를 사용하여 로그를 가져오려면 다음을 수행합니다.

1. **Azure Explorer** 를 선택한 다음, **Spring Cloud** 를 선택합니다.

1. 실행 중인 앱을 마우스 오른쪽 단추로 클릭합니다.

1. 드롭다운 목록에서 **스트리밍 로그** 를 선택합니다.

   ![스트리밍 로그 선택](media/spring-cloud-intellij-howto/streaming-logs.png)

1. **인스턴스** 를 선택합니다.

   ![인스턴스 선택](media/spring-cloud-intellij-howto/select-instance.png)

1. 스트리밍 로그는 출력 창에 표시됩니다.

   ![스트리밍 로그 출력](media/spring-cloud-intellij-howto/streaming-log-output.png)

 Log Analytics에서 사용되는 쿼리 언어에 대한 자세한 내용은 [Azure Monitor 로그 쿼리](/azure/data-explorer/kusto/query/)를 참조하세요. 중앙 집중식 클라이언트에서 모든 Log Analytics 로그를 쿼리하려면 [Azure Data Explorer](/azure/data-explorer/query-monitor-data)를 확인하세요.

---

### <a name="log-analytics"></a>Log Analytics

1. **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **로그** 를 선택합니다. Azure Spring Cloud에 대한 샘플 쿼리 중 하나에서 **실행** 을 선택합니다.

   [ ![Logs Analytics 포털 항목](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png#lightbox)

1. 그러면 필터링된 로그가 표시됩니다. 쿼리 작성에 대한 자세한 지침은 [Azure Log Analytics 문서](../azure-monitor/logs/get-started-queries.md)를 참조하세요.

   [ ![Logs Analytics 쿼리](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>메트릭

`Application insights` 블레이드로 이동합니다. 그런 다음 `Metrics` 블레이드로 이동 - Spring Boot 앱, Spring Cloud 모듈 및 종속성에서 제공하는 메트릭을 볼 수 있습니다. 

아래 차트는 `gateway-requests`(Spring Cloud Gateway), `hikaricp_connections`(JDBC 연결) 및 `http_client_requests`를 보여줍니다.

[ ![메트릭 블레이드](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg#lightbox)

Spring Boot는 JVM, CPU, Tomcat, Logback 등 많은 수의 핵심 메트릭을 등록합니다. Spring Boot 자동 구성은 Spring MVC가 처리하는 요청을 계측할 수 있도록 합니다.
이러한 세 가지 REST 컨트롤러 `OwnerResource`, `PetResource` 및 `VisitResource`는 모두 클래스 수준에서 `@Timed` Micrometer 주석에 의해 계측되었습니다.

* `customers-service` 애플리케이션에는 다음과 같은 사용자 지정 메트릭이 활성화되어 있습니다.
  * @Timed: `petclinic.owner`
  * @Timed: `petclinic.pet`
* `visits-service` 애플리케이션에는 다음과 같은 사용자 지정 메트릭이 활성화되어 있습니다.
  * @Timed: `petclinic.visit`

`Metrics` 블레이드에서 사용자 지정 메트릭을 볼 수 있습니다. [ ![사용자 지정 메트릭](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg#lightbox)

Application Insights에서 가용성 테스트 기능을 사용하여 애플리케이션의 가용성을 모니터링할 수 있습니다.

[ ![가용성 테스트](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg#lightbox)

`Live Metrics` 블레이드로 이동 - 화면에 대기 시간이 1초 미만인 라이브 메트릭을 볼 수 있습니다. [ ![라이브 메트릭](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg#lightbox)

## <a name="tracing"></a>추적

Azure Spring Cloud에서 만든 Application Insights를 열고 마이크로 서비스 애플리케이션 모니터링을 시작합니다.

`Application Map` 블레이드로 이동: [ ![애플리케이션 맵](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg#lightbox)

`Performance` 블레이드로 이동: [ ![성능 블레이드](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg#lightbox)

`Performance/Dependenices` 블레이드로 이동 - 종속성, 특히 SQL 호출에 대한 성능 수치를 볼 수 있습니다. [ ![성능/종속성 블레이드](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg#lightbox)

SQL 호출을 선택하여 컨텍스트에서 엔드투엔드 트랜잭션을 확인합니다. [ ![SQL 엔드투엔드 트랜잭션](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg#lightbox)

`Failures/Exceptions` 블레이드로 이동 - 예외 컬렉션을 볼 수 있습니다. [ ![실패/예외](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.png#lightbox)

예외를 선택하여 컨텍스트에서 엔드투엔드 트랜잭션 및 stacktrace를 확인합니다. [ ![Stacktrace 엔드투엔드](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

이러한 빠른 시작에서는 구독에 남아있는 경우 요금이 계속 청구되는 Azure 리소스를 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 예상되는 경우 포털을 사용하여 리소스 그룹을 삭제하거나 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

이전 빠른 시작에서는 기본 리소스 그룹 이름도 설정합니다. 다음 빠른 시작을 계속하지 않으려면 다음 CLI 명령을 실행하여 기본값을 지웁니다.

```azurecli
az config set defaults.group=
```

## <a name="next-steps"></a>다음 단계

Azure Spring Cloud의 추가 모니터링 기능을 탐색하려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [진단 서비스](diagnostic-services.md)
>
> [분산된 추적](./how-to-distributed-tracing.md)
>
> [실시간으로 로그 스트리밍](./how-to-log-streaming.md)
