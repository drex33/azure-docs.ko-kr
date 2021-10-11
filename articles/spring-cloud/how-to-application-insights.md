---
title: Azure Spring Cloud에서 Application Insights Java In-Process 에이전트를 사용하는 방법
description: Azure Spring Cloud에서 Application Insights Java In-Process 에이전트를 사용하여 앱 및 마이크로 서비스를 모니터링하는 방법입니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d4cb278fbfd6feb3fb2be0e2a113092ff24644cc
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740268"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud"></a>Azure Spring Cloud Application Insights Java In-Process Agent

이 글에서는 Azure Spring Cloud의 Application Insights Java 에이전트를 사용하여 앱 및 마이크로서비스를 모니터링하는 방법을 설명합니다.

이 기능을 사용하면 다음을 수행할 수 있습니다.

* 다양한 필터로 추적 데이터 검색
* 마이크로서비스의 종속성 맵을 봅니다.
* 요청 성능을 확인합니다.
* 실시간 라이브 메트릭을 모니터링합니다.
* 요청 실패를 확인합니다.
* 애플리케이션 메트릭을 확인합니다.

애플리케이션 Insights 다음과 같은 다양한 관찰 가능한 관점을 제공할 수 있습니다.

* 애플리케이션 맵
* 성능
* 오류
* 메트릭
* 라이브 메트릭
* 가용성

## <a name="enable-java-in-process-agent-for-application-insights"></a>Application Insights에 대한 Java In-Process 에이전트 사용

다음 절차를 사용하여 Java In-Process 에이전트를 사용하도록 설정합니다.

1. 서비스 인스턴스의 서비스 개요 페이지로 이동합니다.
2. **모니터링** 창에서 **애플리케이션 Insights** 항목을 선택합니다.
3. **애플리케이션 Insights 사용을** 선택하여 애플리케이션 **Insights** 통합을 사용하도록 설정합니다.
4. Application Insights의 기존 인스턴스를 선택하거나 새 인스턴스를 만듭니다.
   여기에서 샘플링 주기를 0에서 100으로 사용자 지정할 수도 있습니다.
5. **저장** 을 선택하여 변경 내용을 저장합니다.

## <a name="portal"></a>포털

1. **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **Application Insights** 를 선택합니다.
2. **Application Insights 사용** 을 선택하여 Azure Spring Cloud에서 Application Insights를 사용하도록 설정합니다.
3. **애플리케이션 Insights** 사용하도록 설정되면 하나의 선택적 샘플링 비율(기본값 10.0%)을 구성할 수 있습니다.

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

> [!Note]
> 다른 Azure Spring Cloud 인스턴스에서 동일한 AI를 사용하지 마십시오. 그렇지 않으면 혼합된 데이터가 표시됩니다.

## <a name="using-the-application-insights-feature"></a>Application Insights 기능 사용

**Application Insights** 기능이 사용 설정된 경우 다음을 수행할 수 있습니다.

왼쪽 탐색 창에서 **Application Insights** 를 선택하여 Application Insights의 **개요** 페이지로 이동합니다.

* **애플리케이션 맵** 을 선택하여 애플리케이션 간의 호출 상태를 확인합니다.

   [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* 고객-서비스 간 링크와 `petclinic`을 선택하여 SQL의 쿼리와 같은 자세한 정보를 확인합니다.

* 왼쪽 탐색 창에서 **성능** 을 선택하여 모든 애플리케이션 작업의 성능 데이터는 물론 종속성과 역할도 확인합니다.

   [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 왼쪽 탐색 창에서 **실패** 를 선택하여 애플리케이션에 예기치 않은 항목이 있는지 확인합니다.

   [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 왼쪽 탐색 창에서 **메트릭** 을 선택하여 네임스페이스를 선택하면 Spring Boot 메트릭과 사용자 지정 메트릭(있는 경우)이 모두 표시됩니다.

   [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 왼쪽 탐색 창에서 **라이브 메트릭을** 선택하여 다양한 차원에 대한 실시간 메트릭을 확인합니다.

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 왼쪽 탐색 창에서 **가용성** 을 선택하여 [Application Insights의 가용성 테스트](../azure-monitor/app/monitor-web-app-availability.md)를 만들어 웹앱의 가용성 및 응답성을 모니터링합니다.

   [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="automation"></a>Automation

다음 섹션에서는 ARM 템플릿(Azure Resource Manager 템플릿) 또는 Terraform을 사용하여 배포를 자동화하는 방법을 설명합니다.
    
### <a name="arm-templates"></a>ARM 템플릿

ARM 템플릿을 사용하여 배포하려면 다음 콘텐츠를 *azuredeploy.json* 파일에 복사합니다. 자세한 내용은 [Microsoft.AppPlatform Spring/monitoringSettings를 참조하세요.](/azure/templates/microsoft.appplatform/spring/monitoringsettings)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

### <a name="terraform"></a>Terraform

Terraform 배포의 경우 다음 템플릿을 사용합니다. 자세한 내용은 [azurerm_spring_cloud_service.](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_service)

```terraform
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_application_insights" "example" {
  name                = "tf-test-appinsights"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  application_type    = "web"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku_name            = "S0"

  config_server_git_setting {
    uri          = "https://github.com/Azure-Samples/piggymetrics"
    label        = "config"
    search_paths = ["dir1", "dir2"]
  }

  trace {
    connection_string = azurerm_application_insights.example.connection_string
    sample_rate       = 10.0
  }

  tags = {
    Env = "staging"
  }
}
```

## <a name="cli"></a>CLI

Azure CLI 명령을 사용하여 애플리케이션 Insights 관리할 수 있습니다. 다음 명령에서 텍스트를 설명된 값으로 바꾸어야 *\<placeholder>* 합니다. *\<service-name>* 자리 표시자는 Azure Spring Cloud 인스턴스의 이름을 참조합니다.

Azure Spring Cloud 인스턴스를 만들 때 Application Insights 구성하려면 다음 명령을 사용합니다. 인수의 경우 `app-insights` 애플리케이션 Insights 이름 또는 리소스 ID를 지정할 수 있습니다.
   
```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name "serviceName" \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

다음 예제와 같이 Application Insights 연결 문자열(기본 설정) 또는 계측 키를 사용할 수도 있습니다.
   
```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

Azure Spring Cloud 인스턴스를 만들 때 Application Insights 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable-app-insights
```

기존 Azure Spring Cloud 인스턴스의 애플리케이션 Insights 설정을 확인하려면 다음 명령을 사용합니다.

```azurecli
az spring-cloud app-insights show \
    --resource-group <resource-group-name> \
    --name <service-name>
```

연결 문자열(기본 설정) 또는 계측 키를 사용하여 애플리케이션 Insights 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

리소스 이름 또는 ID를 사용하여 애플리케이션 Insights 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

기존 Azure Spring Cloud 인스턴스에서 애플리케이션 Insights 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable
```

## <a name="java-agent-updateupgrade"></a>Java 에이전트 업데이트/업그레이드

Java 에이전트는 JDK를 통해 정기적으로 업데이트/업그레이드되며, 이는 다음 시나리오에 영향을 줄 수 있습니다.

> [!Note]
> JDK 버전은 매년 분기별로 업데이트/업그레이드됩니다.

* 업데이트/업그레이드 전 Java 에이전트를 사용하는 기존 애플리케이션은 영향을 받지 않습니다.
* 업데이트/업그레이드 후에 만든 애플리케이션은 새 버전의 Java 에이전트를 활용합니다.
* 이전에 Java 에이전트를 사용하지 않은 기존 애플리케이션은 새 버전의 Java 에이전트를 활용하려면 다시 시작하거나 다시 배포해야 합니다.

## <a name="java-agent-configuration-hot-loading"></a>Java 에이전트 구성 핫 로드

Azure Spring Cloud 핫 로드 메커니즘을 사용하면 애플리케이션을 다시 시작하지 않고 에이전트 구성의 설정을 조정할 수 있습니다.

> [!Note]
> 핫 로딩 메커니즘은 몇 분 안에 지연됩니다.

* 이전에 Java 에이전트를 사용하도록 설정한 경우 Application Insights 인스턴스 및/또는 SamplingRate를 변경해도 애플리케이션을 다시 시작할 필요가 없습니다.
* Java 에이전트를 사용할 경우 애플리케이션을 다시 시작해야 합니다.
* Java 에이전트를 사용하지 않을 때는 애플리케이션이 지연 시간(분) 후에 모든 모니터링 데이터 전송을 중지합니다. 애플리케이션을 다시 시작하여 Java 런타임 환경에서 에이전트를 제거할 수 있습니다.

## <a name="concept-matching-between-azure-spring-cloud-and-application-insights"></a>Azure Spring Cloud와 Application Insights 간의 개념 일치

| Azure Spring Cloud | Application Insights                                         |
| ------------------ | ------------------------------------------------------------ |
| `App`              | * __애플리케이션 맵__/역할<br />* __라이브 메트릭__/역할<br />* __실패__/역할/클라우드 역할<br />* __성능__/역할/클라우드 역할 |
| `App Instance`     | * __애플리케이션 맵__/역할 인스턴스<br />* __라이브 메트릭__/서비스 이름<br />* __실패__/역할/클라우드 인스턴스<br />* __성능__/역할/클라우드 인스턴스 |

Azure Spring Cloud의 이름 `App Instance`는 다음 시나리오에서 변경되거나 생성됩니다.

* 새 애플리케이션을 만듭니다.
* 기존 애플리케이션에 JAR 파일 또는 소스 코드를 배포합니다.
* 블루/그린 배포를 시작합니다.
* 애플리케이션을 다시 시작합니다.
* 애플리케이션의 배포를 중지한 다음 다시 시작합니다.

데이터가 Application Insights에 저장되면, Java 에이전트를 사용하도록 설정한 이후 생성되거나 배포된 Azure Spring Cloud 앱 인스턴스의 기록이 여기에 포함됩니다. 즉, Application Insights 포털에서는 어제 생성되었지만 지난 24시간과 같은 특정 시간 범위 내에서 삭제된 애플리케이션 데이터를 볼 수 있습니다. 다음 시나리오는 이것의 작동 방식을 보여 줍니다.

* 오늘 오전 8시경에 Java 에이전트를 사용하도록 설정한 채 Azure Spring Cloud에서 애플리케이션을 만든 다음, 오늘 오전 8시 10분경에 JAR 파일을 이 애플리케이션에 배포했습니다. 약간의 테스트 후 코드를 변경하고 오늘 오전 8시 30분에 새 JAR 파일을 이 애플리케이션에 배포합니다. 그런 다음 휴식을 취하고, 오전 11시경에 다시 돌아와서 Application Insights에서 일부 데이터를 확인합니다. 다음과 같습니다.
  * 지난 24시간 동안의 시간 범위와 함께 실패, 성능, 메트릭이 있는 애플리케이션 맵의 3개 인스턴스.
  * 지난 한 시간 동안의 시간 범위와 함께 실패, 성능, 메트릭이 있는 애플리케이션 맵의 1개 인스턴스.
  * 라이브 메트릭의 1개 인스턴스
* 오늘 오전 8시경에 Java 에이전트를 사용하도록 설정한 채 Azure Spring Cloud에서 애플리케이션을 만든 다음, 오늘 오전 8시 10분경에 JAR 파일을 이 애플리케이션에 배포했습니다. 오늘 오전 8시 30분경에 또 다른 JAR 파일을 사용하여 블루/그린 배포를 시도합니다. 현재 이 애플리케이션에 대한 2개의 배포가 있습니다. 휴식을 취한 후 오늘 오전 11시경에 Application Insights에서 일부 데이터를 확인하려고 합니다. 다음과 같습니다.
  * 지난 24시간 동안의 시간 범위와 함께 실패, 성능, 메트릭이 있는 애플리케이션 맵의 3개 인스턴스.
  * 지난 한 시간 동안의 시간 범위와 함께 실패, 성능, 메트릭이 있는 애플리케이션 맵의 2개 인스턴스.
  * 라이브 메트릭의 2개 인스턴스

## <a name="see-also"></a>참고 항목

* [Azure Spring Cloud에서 분산 추적 사용](./how-to-distributed-tracing.md)
* [로그 및 메트릭 분석](diagnostic-services.md)
* [실시간으로 로그 스트리밍](./how-to-log-streaming.md)
