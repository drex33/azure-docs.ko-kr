---
title: AppDynamics Java 에이전트를 사용하여 Spring Boot 앱을 모니터링하는 방법(미리 보기)
titleSuffix: Azure Spring Cloud
description: AppDynamics Java 에이전트를 사용하여 Azure Spring Cloud Spring Boot 애플리케이션을 모니터링하는 방법입니다.
author: KarlErickson
ms.author: jiec
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 8c4a708cd04334f10f44c07d5daf9468f7137acf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722699"
---
# <a name="how-to-monitor-spring-boot-apps-with-the-appdynamics-java-agent-preview"></a>AppDynamics Java 에이전트를 사용하여 Spring Boot 앱을 모니터링하는 방법(미리 보기)

이 문서에서는 AppDynamics Java 에이전트를 사용하여 Azure Spring Cloud Spring Boot 애플리케이션을 모니터링하는 방법을 설명합니다.

AppDynamics Java 에이전트를 사용하면 다음을 수행할 수 있습니다.

- 애플리케이션 모니터링
- 환경 변수를 사용하여 AppDynamics Java 에이전트 구성
- AppDynamics 대시보드에서 모든 모니터링 데이터 확인

다음 비디오에서는 AppDynamics Java in-process 에이전트를 소개합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/4dZuRX5bNAs]

## <a name="prerequisites"></a>필수 구성 요소

* [Azure CLI](/cli/azure/install-azure-cli)
* [AppDynamics 계정](https://www.appdynamics.com/)

## <a name="activate-the-appdynamics-java-in-process-agent"></a>AppDynamics Java In Process 에이전트 활성화

전체 워크플로의 경우 다음을 수행해야 합니다.

* Azure Spring Cloud AppDynamics Java in process 에이전트를 활성화하여 애플리케이션 메트릭 데이터를 생성합니다.
* AppDynamics 에이전트를 AppDynamics 컨트롤러에 커넥트 컨트롤러의 데이터를 수집하고 시각화합니다.

!['appDynamics Agent' 상자에 연결되는 양방향 화살표가 있는 'Azure Spring Cloud' 상자의 Spring Boot 애플리케이션을 보여 주는 다이어그램. 'AppDynamics Controller' 상자를 가리키는 화살표도 있습니다.](media/how-to-appdynamics-java-agent-monitor/appdynamics-activation.jpg)

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-cli"></a>Azure CLI 사용하여 AppDynamics 에이전트로 애플리케이션 활성화

Azure CLI 통해 애플리케이션을 활성화하려면 다음 단계를 사용합니다.

1. 리소스 그룹을 만듭니다.
1. Azure Spring Cloud의 인스턴스를 만듭니다.
1. 다음 명령을 사용하여 애플리케이션을 만듭니다. *\<...>* 자리 표시자를 고유한 값으로 바꿉니다.

    ```azurecli
    az spring-cloud app create \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --is-public true
    ```

1. 환경 변수를 사용하여 AppDynamics 에이전트를 사용하여 배포를 만듭니다.

    ```azurecli
    az spring-cloud app deploy \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --jar-path app.jar \
        --jvm-options="-javaagent:/opt/agents/appdynamics/java/javaagent.jar" \
        --env APPDYNAMICS_AGENT_APPLICATION_NAME=<your-app-name> \
              APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY=<your-agent-access-key> \
              APPDYNAMICS_AGENT_ACCOUNT_NAME=<your-agent-account-name> \
              APPDYNAMICS_AGENT_NODE_NAME=<your-agent-node-name> \
              APPDYNAMICS_AGENT_TIER_NAME=<your-agent-tier-name> \
              APPDYNAMICS_CONTROLLER_HOST_NAME=<your-AppDynamics-controller-host-name> \
              APPDYNAMICS_CONTROLLER_SSL_ENABLED=true \
              APPDYNAMICS_CONTROLLER_PORT=443
    ```

Azure Spring Cloud */opt/agents/appdynamics/java/javaagent.jar 경로에 AppDynamics Java 에이전트를* 미리 설치합니다. 애플리케이션의 JVM 옵션에서 에이전트를 활성화한 다음 환경 변수를 사용하여 에이전트를 구성할 수 있습니다. [Java 에이전트를](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent)Azure Spring Cloud 모니터링에서 이러한 변수에 대한 값을 찾을 수 있습니다. 이러한 변수가 AppDynamics UI에서 보고서를 보고 구성하는 데 도움이 되는 방법에 대한 자세한 내용은 [계층 및 노드를 참조하세요.](https://docs.appdynamics.com/21.9/en/application-monitoring/tiers-and-nodes)

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-portal"></a>Azure Portal 사용하여 AppDynamics 에이전트로 애플리케이션 활성화

Azure Portal 통해 애플리케이션을 활성화하려면 다음 단계를 사용합니다.

1. Azure Portal Azure Spring Cloud 인스턴스로 이동합니다.

1. 왼쪽 탐색 창의 **설정** 섹션에서 **앱을** 선택합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png" alt-text="앱 섹션을 보여 Azure Portal 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png":::

1. 애플리케이션을 선택하여 **개요** 페이지로 이동합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png" alt-text="Azure Portal 앱의 개요 페이지 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png":::

1. 왼쪽 탐색 창에서 **구성을** 선택하여 애플리케이션의 환경 변수를 추가, 업데이트 또는 삭제합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png" alt-text="앱 구성 페이지의 '환경 변수' 섹션을 보여 Azure Portal 스크린샷 " lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png":::

1. **일반 설정을** 선택하여 애플리케이션의 JVM 옵션을 추가, 업데이트 또는 삭제합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png" alt-text="'JVM 옵션'이 강조 표시된 앱 구성 페이지의 '일반 설정' 섹션을 보여주는 Azure Portal 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png":::

## <a name="automate-provisioning"></a>프로비저닝 자동화

Terraform 또는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 프로비저닝 자동화 파이프라인을 실행할 수도 있습니다. 이 파이프라인은 사용자가 만들고 배포하는 모든 새 애플리케이션을 계측하고 모니터링하기 위한 완전한 자동 환경을 제공할 수 있습니다.

### <a name="automate-provisioning-using-terraform"></a>Terraform을 사용하여 프로비저닝 자동화

Terraform 템플릿에서 환경 변수를 구성하려면 템플릿에 다음 코드를 추가하고 *\<...>* 자리 표시자를 고유한 값으로 바꿉니다. 자세한 내용은 [활성 Azure Spring Cloud 배포 관리](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment)를 참조하세요.

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/appdynamics/java/javaagent.jar"
  ...
    environment_variables = {
      "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
      "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
      "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
      "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
      "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
      "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
      "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
      "APPDYNAMICS_CONTROLLER_PORT" : "443"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>ARM 템플릿을 사용하여 프로비저닝 자동화

ARM 템플릿에서 환경 변수를 구성하려면 템플릿에 다음 코드를 추가하고 *\<...>* 자리 표시자를 고유한 값으로 바꿉니다. 자세한 내용은 [Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json)를 참조하세요.

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
    "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
    "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
    "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
    "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
    "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
    "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
    "APPDYNAMICS_CONTROLLER_PORT" : "443"
  },
  "jvmOptions": "-javaagent:/opt/agents/appdynamics/java/javaagent.jar",
  ...
}
```

## <a name="review-reports-in-the-appdynamics-dashboard"></a>AppDynamics 대시보드에서 보고서 검토

이 섹션에서는 AppDynamics의 다양한 보고서를 보여줍니다.

다음 스크린샷은 AppDynamics 대시보드의 앱 개요를 보여줍니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg" alt-text="애플리케이션 페이지를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg":::

**애플리케이션 대시보드는** 예제 애플리케이션을 사용하는 다음 스크린샷에 표시된 것처럼 각 앱에 대한 전체 정보를 표시합니다.

- `api-gateway`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg" alt-text="예제 api-gateway 앱에 대한 애플리케이션 대시보드를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg":::

- `customers-service`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg" alt-text="예제 customers-service 앱에 대한 애플리케이션 대시보드를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg":::

다음 스크린샷은 **데이터베이스 호출** 대시보드에서 기본 정보를 얻는 방법을 보여줍니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg" alt-text="데이터베이스 호출 대시보드를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg":::

다음 스크린샷과 같이 가장 느린 데이터베이스 호출에 대한 정보를 얻을 수도 있습니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg" alt-text="가장 느린 데이터베이스 호출 페이지를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg":::

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg" alt-text="가장 느린 데이터베이스 호출 페이지에서 액세스하는 상관 관계가 있는 스냅샷 페이지를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg":::

다음 스크린샷은 메모리 페이지의 **힙** 섹션에 있는 **메모리** 사용량 분석을 보여줍니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg" alt-text="메모리 페이지의 힙 섹션을 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg":::

이 스크린샷과 같이 가비지 수집 프로세스를 볼 수도 있습니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg" alt-text="메모리 페이지의 가비지 수집 섹션을 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg":::

다음 스크린샷은 느린 트랜잭션 페이지를 **보여줍니다.** 

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg" alt-text="느린 트랜잭션 페이지를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg":::

메트릭 브라우저 의 스크린샷에 표시된 대로 JVM에 대한 더 많은 **메트릭을** 정의할 수 있습니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg" alt-text="메트릭 브라우저를 보여주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg":::

## <a name="view-appdynamics-agent-logs"></a>AppDynamics 에이전트 로그 보기

기본적으로 Azure Spring Cloud AppDynamics 에이전트의 *정보* 수준 로그를 에 `STDOUT` 출력합니다. 로그는 애플리케이션 로그와 혼합됩니다. 애플리케이션 로그에서 명시적 에이전트 버전을 확인할 수 있습니다.

다음 위치에서 AppDynamics 에이전트의 로그를 얻을 수도 있습니다.

* 로그 Azure Spring Cloud
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

## <a name="learn-about-appdynamics-agent-upgrade"></a>AppDynamics 에이전트 업그레이드에 대해 알아보기

AppDynamics 에이전트는 JDK를 통해 정기적으로 업그레이드됩니다(분기별). 에이전트 업그레이드는 다음 시나리오에 영향을 줄 수 있습니다.

* 업그레이드 전에 AppDynamics 에이전트를 사용하는 기존 애플리케이션은 변경되지 않지만 새 버전의 AppDynamics 에이전트를 사용하려면 다시 시작하거나 다시 배포해야 합니다.
* 업그레이드 후에 만든 애플리케이션은 새 버전의 AppDynamics 에이전트를 사용합니다.

## <a name="configure-vnet-injection-instance-outbound-traffic"></a>VNet 삽입 인스턴스 아웃바운드 트래픽 구성

Azure Spring Cloud VNet 삽입 인스턴스의 경우 아웃바운드 트래픽이 AppDynamics 에이전트에 대해 올바르게 구성되었는지 확인합니다. 자세한 내용은 [SaaS 도메인 및 IP 범위](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges) 및 [VNET에서 Azure Spring Cloud 실행하기 위한 고객 책임을 참조하세요.](vnet-customer-responsibilities.md)

## <a name="understand-the-limitations"></a>제한 사항 이해

AppDynamics 에이전트의 제한에 대한 자세한 내용은 [Java 에이전트를 사용하여 Azure Spring Cloud 모니터링을 참조하세요.](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent)

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud Application Insights Java In-Process Agent 사용](./how-to-application-insights.md)