---
title: 새 유물 Java 에이전트를 사용 하 여 스프링 부팅 앱을 모니터링 하는 방법
titleSuffix: Azure Spring Cloud
description: 새 유물 Java agent를 사용 하 여 스프링 부팅 응용 프로그램을 모니터링 하는 방법에 대해 알아봅니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-java
ms.openlocfilehash: 740193a9526bf19efb0e98f937c6f77c30b50b61
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258300"
---
# <a name="how-to-monitor-spring-boot-apps-using-new-relic-java-agent-preview"></a>새 유물 Java 에이전트를 사용 하 여 스프링 부팅 앱을 모니터링 하는 방법 (미리 보기)

이 기능을 사용 하면 새 유물 Java 에이전트로 Azure 스프링 클라우드에서 실행 되는 스프링 부팅 응용 프로그램을 모니터링할 수 있습니다.

New Relic Java 에이전트를 사용하면 다음을 수행할 수 있습니다.
* New Relic Java 에이전트를 사용합니다.
* 환경 변수를 사용하여 New Relic Java 에이전트를 구성합니다.
* New Relic 대시보드에서 모든 모니터링 데이터를 확인합니다.

다음 비디오에서는 New Relic One을 사용하여 Azure Spring Cloud에서 실행되는 Spring Boot 애플리케이션을 활성화하고 모니터링하는 방법을 설명합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/4GQPwJSP3ys?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

## <a name="prerequisites"></a>사전 요구 사항

* [New Relic](https://newrelic.com/) 계정
* [Azure CLI 버전 2.0.67 이상](/cli/azure/install-azure-cli)

## <a name="activate-the-new-relic-java-in-process-agent"></a>프로세스 에이전트의 새 유물 Java 활성화

다음 절차에 따라 에이전트에 액세스합니다.

1. Azure Spring Cloud의 인스턴스를 만듭니다.

2. 애플리케이션을 만듭니다.

    ```azurecli
      az spring-cloud app create --name "appName" --is-public true \
      -s "resourceName" -g "resourceGroupName"
    ```

3. New Relic 에이전트 및 환경 변수를 사용하여 배포를 만듭니다.

    ```azurecli
    az spring-cloud app deploy --name "appName" --jar-path app.jar \
       -s "resourceName" -g "resourceGroupName" \
       --jvm-options="-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar" \
       --env NEW_RELIC_APP_NAME=appName NEW_RELIC_LICENSE_KEY=newRelicLicenseKey
    ```

Azure Spring Cloud는 New Relic Java 에이전트를 */opt/agents/newrelic/java/newrelic-agent.jar* 에 미리 설치합니다. 고객은 응용 프로그램의 **JVM 옵션** 에서 에이전트를 활성화할 수 있을 뿐만 아니라 [새 유물 Java 에이전트 환경 변수](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)를 사용 하 여 에이전트를 구성할 수 있습니다.

## <a name="portal"></a>포털

다음 절차를 사용 하 여 포털에서이 에이전트를 활성화할 수도 있습니다.

1. 탐색 창의 **설정**/**앱** 에서 앱을 찾습니다.

   [ ![모니터링할 앱 찾기](media/new-relic-monitoring/find-app.png) ](media/new-relic-monitoring/find-app.png)

2. 애플리케이션을 클릭하여 **개요** 페이지로 이동합니다.

   [ ![개요 페이지](media/new-relic-monitoring/overview-page.png) ](media/new-relic-monitoring/overview-page.png)

3. 왼쪽 탐색 창에서 **구성** 을 선택하여 애플리케이션의 **환경 변수** 를 추가/업데이트/삭제합니다.

   [ ![환경 업데이트](media/new-relic-monitoring/configurations-update-environment.png) ](media/new-relic-monitoring/configurations-update-environment.png)

4. **일반 설정** 을 선택하여 애플리케이션의 **JVM 옵션** 을 추가/업데이트/삭제합니다.

   [ ![JVM 옵션 업데이트](media/new-relic-monitoring/update-jvm-option.png) ](media/new-relic-monitoring/update-jvm-option.png)

5. New Relic 대시보드에서 애플리케이션 API/게이트웨이 **요약** 페이지를 봅니다.

   [ ![앱 요약 페이지](media/new-relic-monitoring/app-summary-page.png) ](media/new-relic-monitoring/app-summary-page.png)

6. New Relic 대시보드에서 애플리케이션 customers-service **요약** 페이지를 봅니다.
 
   [ ![Customers-service 페이지](media/new-relic-monitoring/customers-service.png) ](media/new-relic-monitoring/customers-service.png)  

7. New Relic 대시보드에서 **서비스 맵** 페이지를 봅니다.  

   [ ![서비스 맵 페이지](media/new-relic-monitoring/service-map.png) ](media/new-relic-monitoring/service-map.png)

8. New Relic 대시보드에서 애플리케이션의 **JVM** 페이지를 봅니다.

   [ ![JVM 페이지](media/new-relic-monitoring/jvm-page.png) ](media/new-relic-monitoring/jvm-page.png)

9. New Relic 대시보드에서 애플리케이션 프로필을 봅니다.

   [ ![애플리케이션 프로필](media/new-relic-monitoring/profile-app.png) ](media/new-relic-monitoring/profile-app.png)

## <a name="automate-provisioning"></a>프로비저닝 자동화

Terraform 또는 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 프로 비전 자동화 파이프라인을 실행할 수도 있습니다. 이 파이프라인은 사용자가 만들고 배포하는 모든 새 애플리케이션을 계측하고 모니터링하기 위한 완전한 자동 환경을 제공할 수 있습니다.

### <a name="automate-provisioning-using-terraform"></a>Terraform을 사용 하 여 프로 비전 자동화

Terraform 템플릿에서 환경 변수를 구성하려면 템플릿에 다음 코드를 추가하고 *\<...>* 자리 표시자를 고유한 값으로 바꿉니다. 자세한 내용은 [활성 Azure Spring Cloud 배포 관리](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment)를 참조하세요.

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar"
  ...
    environment_variables = {
      "NEW_RELIC_APP_NAME": "<app-name>",
      "NEW_RELIC_LICENSE_KEY": "<new-relic-license-key>"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>ARM 템플릿을 사용 하 여 프로 비전 자동화

ARM 템플릿에서 환경 변수를 구성하려면 템플릿에 다음 코드를 추가하고 *\<...>* 자리 표시자를 고유한 값으로 바꿉니다. 자세한 내용은 [Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json)를 참조하세요.

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "NEW_RELIC_APP_NAME" : "<app-name>",
    "NEW_RELIC_LICENSE_KEY" : "<new-relic-license-key>"
  },
  "jvmOptions": "-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar",
  ...
}
```

## <a name="view-new-relic-java-agent-logs"></a>새 유물 Java 에이전트 로그 보기

기본적으로 Azure Spring Cloud는 New Relic Java 에이전트의 로그를 `STDOUT`에 출력합니다. 로그는 애플리케이션 로그와 혼합됩니다. 애플리케이션 로그에서 명시적 에이전트 버전을 확인할 수 있습니다.

또한 다음 위치에서 새 유물 에이전트의 로그를 가져올 수 있습니다.

* Azure 스프링 클라우드 로그
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

New Relic에서 제공하는 일부 환경 변수를 활용하여 새 에이전트(예: `NEW_RELIC_LOG_LEVEL`)의 로깅을 구성하여 로그 수준을 제어할 수 있습니다. 자세한 내용은 [New Relic 환경 변수](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)를 참조하세요.

> [!CAUTION]
> New Relic용 Azure Spring Cloud에서 제공하는 로깅 기본 동작은 재정의하지 *않는* 것이 좋습니다. 재정의하면 위의 시나리오에서 로깅 시나리오가 차단되고 로그 파일이 손실될 수 있습니다. 예를 들어, 애플리케이션에 다음 환경 변수를 전달하면 안 됩니다. 애플리케이션을 다시 시작하거나 재배포한 후 로그 파일이 손실될 수 있습니다.
>
> * NEW_RELIC_LOG
> * NEW_RELIC_LOG_FILE_PATH

## <a name="new-relic-java-agent-updateupgrade"></a>New Relic Java 에이전트 업데이트/업그레이드

New Relic Java 에이전트는 JDK를 정기적으로 업데이트/업그레이드합니다. 에이전트 업데이트/업그레이드는 다음 시나리오에 영향을 미칠 수 있습니다.

* 업데이트/업그레이드 전에 New Relic Java 에이전트를 사용하는 기존 애플리케이션은 변경되지 않습니다.
* 업데이트/업그레이드 전에 New Relic Java 에이전트를 사용하는 기존 애플리케이션은 New Relic Java 에이전트의 새 버전을 사용하기 위해 다시 시작하거나 다시 배포해야 합니다.
* 업데이트/업그레이드 후에 만든 새 애플리케이션은 New Relic Java 에이전트의 새 버전을 사용합니다.

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Vnet 삽입 인스턴스 아웃바운드 트래픽 구성

Azure Spring Cloud의 vnet 삽입 인스턴스에서는 New Relic Java 에이전트에 대해 아웃바운드 트래픽이 올바르게 구성되었는지 확인해야 합니다. 자세한 내용은 [New Relic 네트워크](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [분산 추적 및 앱 인사이트](how-to-distributed-tracing.md)
