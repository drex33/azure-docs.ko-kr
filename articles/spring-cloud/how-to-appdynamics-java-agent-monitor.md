---
title: AppDynamics Java 에이전트를 사용 하 여 스프링 부팅 앱을 모니터링 하는 방법 (미리 보기)
titleSuffix: Azure Spring Cloud
description: AppDynamics Java 에이전트를 사용 하 여 Azure 스프링 클라우드의 스프링 부팅 응용 프로그램을 모니터링 하는 방법입니다.
author: KarlErickson
ms.author: jiec
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: b549ce3e8e79b7315144f52484fe63f7553982c4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492647"
---
# <a name="how-to-monitor-spring-boot-apps-with-the-appdynamics-java-agent-preview"></a>AppDynamics Java 에이전트를 사용 하 여 스프링 부팅 앱을 모니터링 하는 방법 (미리 보기)

이 문서에서는 AppDynamics Java 에이전트를 사용 하 여 Azure 스프링 클라우드의 스프링 부팅 응용 프로그램을 모니터링 하는 방법을 설명 합니다.

AppDynamics Java 에이전트를 사용 하 여 다음을 수행할 수 있습니다.

- 애플리케이션 모니터링
- 환경 변수를 사용 하 여 AppDynamics Java 에이전트 구성
- AppDynamics 대시보드에서 모든 모니터링 데이터를 확인 합니다.

다음 비디오는 AppDynamics Java in-process 에이전트를 소개 합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/4dZuRX5bNAs]

## <a name="prerequisites"></a>필수 구성 요소

* [Azure CLI](/cli/azure/install-azure-cli)
* [AppDynamics 계정](https://www.appdynamics.com/)

## <a name="activate-the-appdynamics-java-in-process-agent"></a>AppDynamics Java in-process 에이전트 활성화

전체 워크플로의 경우 다음을 수행 해야 합니다.

* 응용 프로그램 메트릭 데이터를 생성 하려면 Azure 스프링 클라우드에서 AppDynamics Java in-process 에이전트를 활성화 합니다.
* Appdynamics 에이전트를 AppDynamics 컨트롤러에 커넥트 하 여 컨트롤러의 데이터를 수집 하 고 시각화 합니다.

![' Azure 스프링 클라우드 ' 상자에서 스프링 부팅 응용 프로그램을 보여 주는 다이어그램입니다 .이 상자에는 ' AppDynamics 에이전트 ' 상자에 연결 하는 양방향 화살표가 있습니다. 여기에는 ' AppDynamics 컨트롤러 ' 상자를 가리키는 화살표도 있습니다.](media/how-to-appdynamics-java-agent-monitor/appdynamics-activation.jpg)

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-cli"></a>Azure CLI를 사용 하 여 AppDynamics 에이전트로 응용 프로그램 활성화

Azure CLI를 통해 응용 프로그램을 활성화 하려면 다음 단계를 사용 합니다.

1. 리소스 그룹을 만듭니다.
1. Azure Spring Cloud의 인스턴스를 만듭니다.
1. 다음 명령을 사용 하 여 응용 프로그램을 만듭니다. *\<...>* 자리 표시자를 고유한 값으로 바꿉니다.

    ```azurecli
    az spring-cloud app create \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --is-public true
    ```

1. 환경 변수를 사용 하 여 AppDynamics 에이전트로 배포를 만듭니다.

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

Azure 스프링 클라우드는 */opt/agents/appdynamics/java/javaagent.jar* 경로에 Appdynamics Java 에이전트를 사전 설치 합니다. 응용 프로그램의 JVM 옵션에서 에이전트를 활성화 한 다음 환경 변수를 사용 하 여 에이전트를 구성할 수 있습니다. [Java 에이전트를 사용 하 여 Azure 스프링 클라우드 모니터링](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent)에서 이러한 변수에 대 한 값을 찾을 수 있습니다. 이러한 변수를 통해 AppDynamics UI에서 보고서를 보고 구성 하는 방법에 대 한 자세한 내용은 [계층 및 노드](https://docs.appdynamics.com/21.9/en/application-monitoring/tiers-and-nodes)를 참조 하세요.

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-portal"></a>Azure Portal를 사용 하 여 AppDynamics 에이전트로 응용 프로그램 활성화

Azure Portal를 통해 응용 프로그램을 활성화 하려면 다음 단계를 사용 합니다.

1. Azure Portal에서 Azure 스프링 클라우드 인스턴스로 이동 합니다.

1. 왼쪽 탐색 창의 **설정** 섹션에서 **앱** 을 선택 합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png" alt-text="앱 섹션을 보여 주는 Azure Portal 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png":::

1. 응용 프로그램을 선택 하 여 **개요** 페이지로 이동 합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png" alt-text="앱의 개요 페이지 Azure Portal 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png":::

1. 왼쪽 탐색 창에서 **구성** 을 선택 하 여 응용 프로그램의 환경 변수를 추가, 업데이트 또는 삭제 합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png" alt-text="앱 구성 페이지의 ' 환경 변수 ' 섹션을 보여 주는 Azure Portal 스크린샷 " lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png":::

1. 응용 프로그램의 JVM 옵션을 추가, 업데이트 또는 삭제 하려면 **일반 설정** 을 선택 합니다.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png" alt-text="' JVM 옵션 '이 강조 표시 된 앱 구성 페이지의 ' 일반 설정 ' 섹션을 보여 주는 Azure Portal 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png":::

## <a name="automate-provisioning"></a>프로비저닝 자동화

Terraform 또는 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 프로 비전 자동화 파이프라인을 실행할 수도 있습니다. 이 파이프라인은 사용자가 만들고 배포하는 모든 새 애플리케이션을 계측하고 모니터링하기 위한 완전한 자동 환경을 제공할 수 있습니다.

### <a name="automate-provisioning-using-terraform"></a>Terraform을 사용 하 여 프로 비전 자동화

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

### <a name="automate-provisioning-using-an-arm-template"></a>ARM 템플릿을 사용 하 여 프로 비전 자동화

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

이 섹션에서는 AppDynamics의 다양 한 보고서를 보여 줍니다.

다음 스크린샷에서는 AppDynamics 대시보드의 앱에 대 한 개요를 보여 줍니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg" alt-text="응용 프로그램 페이지를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg":::

**응용 프로그램 대시보드** 는 예제 응용 프로그램을 사용 하는 다음 스크린샷에 표시 된 것 처럼 각 앱에 대 한 전체 정보를 표시 합니다.

- `api-gateway`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg" alt-text="예제 api 게이트웨이 앱에 대 한 응용 프로그램 대시보드를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg":::

- `customers-service`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg" alt-text="예제 고객-서비스 앱에 대 한 응용 프로그램 대시보드를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg":::

다음 스크린샷은 **데이터베이스 호출** 대시보드에서 기본 정보를 가져오는 방법을 보여 줍니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg" alt-text="데이터베이스 호출 대시보드를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg":::

다음 스크린샷에 표시 된 것 처럼 가장 느린 데이터베이스 호출에 대 한 정보를 얻을 수도 있습니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg" alt-text="가장 느린 데이터베이스 호출 페이지를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg":::

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg" alt-text="가장 느린 데이터베이스 호출 페이지에서 액세스 한 상관 관계가 지정 된 스냅숏 페이지를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg":::

다음 스크린샷은 **메모리** 페이지의 **힙** 섹션에서 메모리 사용량 분석을 보여 줍니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg" alt-text="메모리 페이지의 힙 섹션을 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg":::

다음 스크린샷에 표시 된 것 처럼 가비지 수집 프로세스를 볼 수도 있습니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg" alt-text="메모리 페이지의 가비지 수집 섹션을 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg":::

다음 스크린샷은 **저속 트랜잭션** 페이지를 보여 줍니다. 

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg" alt-text="저속 트랜잭션 페이지를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg":::

**메트릭 브라우저** 의이 스크린샷에 표시 된 것 처럼 JVM에 대해 더 많은 메트릭을 정의할 수 있습니다.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg" alt-text="메트릭 브라우저를 보여 주는 AppDynamics 스크린샷" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg":::

## <a name="view-appdynamics-agent-logs"></a>AppDynamics 에이전트 로그 보기

기본적으로 Azure 스프링 클라우드는 AppDynamics 에이전트의 *정보* 수준 로그를에 인쇄 `STDOUT` 합니다. 로그는 애플리케이션 로그와 혼합됩니다. 애플리케이션 로그에서 명시적 에이전트 버전을 확인할 수 있습니다.

다음 위치에서 AppDynamics 에이전트의 로그를 가져올 수도 있습니다.

* Azure 스프링 클라우드 로그
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

## <a name="learn-about-appdynamics-agent-upgrade"></a>AppDynamics 에이전트 업그레이드에 대 한 자세한 정보

AppDynamics 에이전트는 JDK (분기별로)를 사용 하 여 정기적으로 업그레이드 됩니다. 에이전트 업그레이드는 다음 시나리오에 영향을 줄 수 있습니다.

* 업그레이드 하기 전에 AppDynamics Agent를 사용 하는 기존 응용 프로그램은 변경 되지 않지만 새 버전의 AppDynamics 에이전트에 참여 하려면 다시 시작 또는 다시 배포 해야 합니다.
* 업그레이드 후 만든 응용 프로그램은 새 버전의 AppDynamics 에이전트를 사용 합니다.

## <a name="configure-vnet-injection-instance-outbound-traffic"></a>VNet 주입 인스턴스 아웃 바운드 트래픽 구성

Azure 스프링 클라우드의 VNet 삽입 인스턴스에 대해 AppDynamics 에이전트에 대해 아웃 바운드 트래픽이 올바르게 구성 되어 있는지 확인 합니다. 자세한 내용은 [VNET에서 Azure 스프링 클라우드 실행을 위한](vnet-customer-responsibilities.md) [SAAS 도메인 및 IP 범위](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges) 및 고객 책임을 참조 하세요.

## <a name="understand-the-limitations"></a>제한 사항 이해

AppDynamics 에이전트의 제한 사항을 이해 하려면 [Java 에이전트를 사용 하 여 Azure 스프링 클라우드 모니터링](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 스프링 클라우드에서 Application Insights Java In-Process 에이전트 사용](/azure/spring-cloud/how-to-application-insights)
