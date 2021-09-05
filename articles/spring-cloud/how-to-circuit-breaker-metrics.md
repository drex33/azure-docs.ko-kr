---
title: Micrometer로 Spring Cloud Resilience4J 회로 차단기 메트릭 수집
description: Azure Spring Cloud에서 Micrometer를 사용하여 Spring Cloud Resilience4J 회로 차단기 메트릭을 수집하는 방법입니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: dae08798aa7b3bc1937295e0cd7701446bd7259e
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567530"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Micrometer로 Spring Cloud Resilience4J 회로 차단기 메트릭 수집(미리 보기)

이 문서에서는 Application Insights Java In Process 에이전트로 Spring Cloud Resilience4j 회로 차단기 메트릭을 수집하는 방법에 대해 설명합니다. 이 기능을 사용하면 Micrometer를 통해 Application Insights에서 resilience4j 회로 차단기의 메트릭을 모니터링할 수 있습니다.

[spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo)를 사용하여 작동 방식을 보여드리겠습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Application Insights 가이드용 Java In Process 에이전트](./how-to-application-insights.md#enable-java-in-process-agent-for-application-insights)에서 Java In Process 에이전트를 사용합니다.
* [Application Insights 가이드](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)에서 Resilience4j 메트릭용 차원 수집을 사용합니다.
* 개발 컴퓨터에서 이미 사용하고 있지 않은 경우 git, Maven, Java를 설치합니다.

## <a name="build-and-deploy-apps"></a>앱 빌드 및 배포

다음 절차를 통해 앱을 빌드하고 배포합니다.

1. 데모 리포지토리를 복제하고 빌드합니다.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. 엔드포인트를 사용하여 애플리케이션 만들기

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. 응용 프로그램을 배포합니다.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Resilience4j에 필요한 종속성을 포함합니다.
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
>
> * 고객 코드는 `CircuitBreakerFactory`의 API를 사용해야 합니다. 이 API는 Spring Cloud 회로 차단기 스타터를 포함하는 경우 자동으로 생성되는 `bean`으로 구현됩니다. 에 대한 자세한 내용은 [Spring Cloud 회로 차단기](https://spring.io/projects/spring-cloud-circuitbreaker#overview)를 참조하세요.
>
> * 다음 2개의 종속성은 위의 resilient4j 패키지와 충돌합니다.  고객이 이를 포함하지 않도록 해야 합니다.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> 게이트웨이 애플리케이션에서 제공하는 URL로 이동하여 다음과 같이 [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo)에서 엔드포인트에 액세스합니다.
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>포털에서 Resilence4j 메트릭 찾기

1. Azure Spring Cloud 포털에서 **Application Insights** 블레이드를 선택하고 **Application Insights** 를 선택합니다.

   [ ![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. **Application Insights** 페이지에서 **메트릭** 을 선택합니다.  **메트릭 네임스페이스** 에서 **azure applicationinsights** 를 선택합니다.  또한 **평균** 이 있는 **resilience4j_circuitbreaker_buffered_calls** 메트릭을 선택합니다.

   [ ![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. **resilience4j_circuitbreaker_calls** 메트릭과 **평균** 을 선택합니다.

   [ ![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. **resilience4j_circuitbreaker_calls** 메트릭과 **평균** 을 선택합니다. **필터 추가** 를 선택한 다음, 이름을 **createNewAccount** 로 선택합니다.

   [ ![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. **resilience4j_circuitbreaker_calls** 메트릭과 **평균** 을 선택합니다.  그런 다음, **분할 적용** 을 선택하고 **종류** 를 선택합니다.

   [ ![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. **평균** 이 있는 **resilience4j_circuitbreaker_calls**, `**resilience4j_circuitbreaker_buffered_calls**, **resilience4j_circuitbreaker_slow_calls** 메트릭을 선택합니다.

   [ ![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>참고 항목

* [애플리케이션 인사이트](./how-to-application-insights.md)
* [분산된 추적](./how-to-distributed-tracing.md)
* [회로 차단기 대시보드](./tutorial-circuit-breaker.md)
