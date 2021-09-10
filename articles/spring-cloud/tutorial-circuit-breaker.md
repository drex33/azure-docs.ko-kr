---
title: 자습서 - Azure Spring Cloud와 함께 회로 차단기 대시보드 사용
description: Azure Spring Cloud를 사용하는 회로 차단기 대시보드를 사용하는 방법을 알아봅니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6fce05bf1f853edd527df920f579689e23f080c8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015666"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>자습서: Azure Spring Cloud를 사용하는 회로 차단기 대시보드 사용

**이 문서는 다음에 적용됩니다.** ✔️ Java

Spring [Cloud Netflix Turbine](https://github.com/Netflix/Turbine)은 여러 [Hystrix](https://github.com/Netflix/Hystrix) 메트릭 스트림을 집계하는 데 널리 사용되므로 Hystrix 대시보드를 사용하여 단일 뷰에서 스트림을 모니터링할 수 있습니다. 이 자습서에서는 Azure Spring Cloud에서 이를 사용하는 방법을 설명합니다.

> [!NOTE]
> Netflix Hystrix는 기존의 많은 Spring Cloud 앱에서 널리 사용되고 있지만 실제 개발에는 더 이상 사용되지 않습니다. 새 프로젝트를 개발하는 경우 [resilience4j](https://github.com/resilience4j/resilience4j)와 같은 Spring Cloud 회로 차단기 구현을 대신 사용합니다. 이 자습서에 표시된 Turbine과는 달리, 새 Spring Cloud 회로 차단기 프레임워크는 메트릭 데이터 파이프라인의 모든 구현을 마이크로미터로 통합합니다. Azure Spring Cloud에서도 지원됩니다. [자세한 정보](./how-to-circuit-breaker-metrics.md).

## <a name="prepare-your-sample-applications"></a>샘플 애플리케이션 준비

샘플은 이 [리포지토리](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine)에서 포크됩니다.

개발 환경에 샘플 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

이 자습서에서 사용할 애플리케이션 3개를 빌드합니다.

* user-service: /personalized/{id}라는 단일 엔드포인트가 있는 간단한 REST 서비스
* recommendation-service: user-service가 호출할 /recommendations라는 단일 엔드포인트가 있는 간단한 REST 서비스
* hystrix-turbine: Hystrix 스트림을 표시하는 Hystrix 대시보드 서비스와 다른 서비스에서 Hystrix 스트림을 집계하는 Turbine 서비스

```bash
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```

## <a name="provision-your-azure-spring-cloud-instance"></a>Azure Spring Cloud 인스턴스 프로비저닝

[Azure CLI에서 서비스 인스턴스 프로비저닝](./quickstart.md#provision-an-instance-of-azure-spring-cloud) 절차를 따르세요.

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Azure Spring Cloud에 애플리케이션 배포

이러한 앱은 **구성 서버** 를 사용하지 않으므로 Azure Spring Cloud용 **구성 서버** 를 설정할 필요가 없습니다.  다음과 같이 만들고 배포합니다.

```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```

## <a name="verify-your-apps"></a>앱 인증

모든 앱이 실행되고 검색 가능하면 브라우저에서 `https://<username>-user-service.azuremicroservices.io/personalized/1` 경로로 `user-service`에 액세스합니다. 사용자 서비스가 `recommendation-service`에 액세스할 수 있는 경우 다음 출력을 가져와야 합니다. 웹 페이지가 작동하지 않는 경우 여러 번 새로 고칩니다.

```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```

## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Hystrix 대시보드 및 메트릭 스트림에 액세스

퍼블릭 엔드포인트 또는 프라이빗 테스트 엔드포인트를 사용하여 인증합니다.

### <a name="using-public-endpoints"></a>퍼블릭 엔드포인트 사용

브라우저에서 `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` 경로를 사용하여 hystrix-turbine에 액세스합니다.  다음 그림은 이 앱에서 실행되는 Hystrix 대시보드를 보여 줍니다.

![Hystrix 대시보드](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

텍스트 상자에 Turbine 스트림 URL `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default`를 복사하고 **스트림 모니터링** 을 선택합니다.  그러면 대시보드가 표시됩니다. 뷰어에 아무것도 표시되지 않는 경우 `user-service` 엔드포인트를 눌러 스트림을 생성합니다.

![Hystrix 스트림](media/spring-cloud-circuit-breaker/hystrix-stream.png) 이제 회로 차단기 대시보드를 사용해 볼 수 있습니다.

> [!NOTE]
> 프로덕션 환경에서는 Hystrix 대시보드 및 메트릭 스트림이 인터넷에 노출되지 않아야 합니다.

### <a name="using-private-test-endpoints"></a>프라이빗 테스트 엔드포인트 사용

`test-endpoint`에서 Hystrix 메트릭 스트림에 액세스할 수도 있습니다. 백 엔드 서비스이므로 `recommendation-service`에 대한 퍼블릭 엔드포인트가 할당되지 않았지만 `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`에서 테스트 엔드포인트를 사용하여 메트릭을 표시할 수 있습니다.

![Hystrix 테스트 엔드포인트 스트림](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

웹앱인 Hystrix 대시보드는 `test-endpoint`에서 작동해야 합니다. 제대로 작동하지 않을 경우 두 가지 이유가 있을 수 있습니다. 첫 번째는 `test-endpoint`를 사용해서 기본 URL이 `/`에서 `/<APP-NAME>/<DEPLOYMENT-NAME>`으로 변경된 경우이고, 두 번째는 웹앱이 정적 리소스의 절대 경로를 사용하는 경우입니다. `test-endpoint`에서 작동하도록 프런트 엔드 파일에서 `<base>`를 수동으로 편집해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure CLI에서 서비스 인스턴스 프로비저닝](./quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비](how-to-prepare-app-deployment.md)
