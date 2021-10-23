---
title: Dynatrace Java OneAgent를 사용하여 Spring Boot 앱을 모니터링하는 방법
description: Dynatrace Java OneAgent를 사용하여 Azure Spring Cloud 실행되는 Spring Boot 애플리케이션을 모니터링하는 방법
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 20625461d27108c201f44458eff3f96d77f81132
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225532"
---
# <a name="how-to-monitor-spring-boot-apps-with-dynatrace-java-oneagent"></a>Dynatrace Java OneAgent를 사용하여 Spring Boot 앱을 모니터링하는 방법

이 문서에서는 Dynatrace OneAgent를 사용하여 Azure Spring Cloud 실행되는 Spring Boot 애플리케이션을 모니터링하는 방법을 설명합니다.

Dynatrace OneAgent로 다음을 수행할 수 있습니다.

* Dynatrace OneAgent로 앱을 모니터링합니다.
* 환경 변수를 사용하여 Dynatrace OneAgent를 구성합니다.
* Dynatrace 대시보드에서 모든 모니터링 데이터를 확인합니다.

다음 동영상은 Dynatrace OneAgent를 소개합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/PF0_SxuiZ2w]

## <a name="prerequisites"></a>필수 구성 요소

* [Azure CLI](/cli/azure/install-azure-cli)
* [Dynatrace 계정](https://www.dynatrace.com/)
* [Dynatrace PaaS 토큰 및 테넌트 토큰](https://www.dynatrace.com/support/help/reference/dynatrace-concepts/access-tokens/)

## <a name="activate-dynatrace-oneagent"></a>Activate Dynatrace OneAgent

다음 섹션에서는 Dynatrace OneAgent를 활성화하는 방법을 설명합니다.

### <a name="prepare-your-azure-spring-cloud-environment"></a>Azure Spring Cloud 환경 준비

1. Azure Spring Cloud의 인스턴스를 만듭니다.
1. 다음 명령을 실행하여 Dynatrace에 보고할 애플리케이션을 만듭니다. *\<...>* 자리 표시자를 고유한 값으로 바꿉니다.
   ```azurecli
   az spring-cloud app create \
       --resource-group <your-resource-group-name> \
       --service <your-Azure-Spring-Cloud-name> \
       --name <your-application-name> \
       --is-public true 
   ```

### <a name="determine-the-values-for-the-required-environment-variables"></a>필요한 환경 변수의 값 결정

Azure Spring Cloud 인스턴스에서 Dynatrace OneAgent를 활성화하려면 `DT_TENANT`, `DT_TENANTTOKEN`, `DT_CONNECTION_POINT` 및 `DT_CLUSTER_ID`의 네 가지 환경 변수를 구성해야 합니다. 자세한 내용은 [Azure Spring Cloud와 OneAgent 통합](https://www.dynatrace.com/support/help/shortlink/azure-spring)을 참조하세요.

여러 인스턴스가 있는 애플리케이션의 경우 Dynatrace는 여러 가지 방법으로 인스턴스를 그룹화할 수 있습니다. `DT_CLUSTER_ID`가 하나의 방법입니다. 자세한 내용은 [프로세스 그룹의 구조 사용자 지정](https://www.dynatrace.com/support/help/how-to-use-dynatrace/process-groups/configuration/adapt-the-composition-of-default-process-groups/)을 참조하세요.

### <a name="add-the-environment-variables-to-your-application"></a>애플리케이션에 환경 변수 추가

Azure Portal 또는 Azure CLI를 사용하여 환경 변수 키/값 쌍을 애플리케이션에 추가할 수 있습니다.

#### <a name="option-1-azure-cli"></a>옵션 1: Azure CLI

Azure CLI를 사용하여 키/값 쌍을 추가하려면 다음 명령을 실행하여 자리 표시자 *\<...>* 를 이전 단계에서 결정된 값으로 바꿉니다.

```azurecli
az spring-cloud app deploy \
    --resource-group <your-resource-group-name> \
    --service <your-Azure-Spring-Cloud-name> \
    --name <your-application-name> \
    --jar-path app.jar \
    --env \
        DT_TENANT=<your-environment-ID> \
        DT_TENANTTOKEN=<your-tenant-token> \
        DT_CONNECTION_POINT=<your-communication-endpoint>
```

#### <a name="option-2-portal"></a>옵션 2: 포털

Azure Portal을 사용하여 키/값 쌍을 추가하려면 다음 단계를 사용합니다.

1. 기존 애플리케이션 목록으로 이동합니다.

   :::image type="content" source="media/dynatrace-oneagent/existing-applications.png" alt-text="Azure Spring Cloud Apps 섹션을 보여 주는 Azure Portal의 스크린샷" lightbox="media/dynatrace-oneagent/existing-applications.png":::

1. 애플리케이션을 선택하여 애플리케이션의 **개요** 페이지로 이동합니다.

   :::image type="content" source="media/dynatrace-oneagent/overview-application.png" alt-text="애플리케이션의 개요 섹션 스크린샷." lightbox="media/dynatrace-oneagent/overview-application.png":::

1. **구성** 을 선택하여 애플리케이션의 **환경 변수** 섹션에서 값을 추가, 업데이트 또는 삭제합니다.

   :::image type="content" source="media/dynatrace-oneagent/configuration-application.png" alt-text="애플리케이션 구성 섹션의 '환경 변수' 탭 스크린샷" lightbox="media/dynatrace-oneagent/configuration-application.png":::

## <a name="automate-provisioning"></a>프로비저닝 자동화

Terraform 또는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 프로비저닝 자동화 파이프라인을 실행할 수도 있습니다. 이 파이프라인은 사용자가 만들고 배포하는 모든 새 애플리케이션을 계측하고 모니터링하기 위한 완전한 자동 환경을 제공할 수 있습니다.

### <a name="automate-provisioning-using-terraform"></a>Terraform을 사용하여 프로비저닝 자동화

Terraform 템플릿에서 환경 변수를 구성하려면 템플릿에 다음 코드를 추가하고 *\<...>* 자리 표시자를 고유한 값으로 바꿉니다. 자세한 내용은 [활성 Azure Spring Cloud 배포 관리](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment)를 참조하세요.

```terraform
environment_variables = {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>ARM 템플릿을 사용하여 프로비저닝 자동화

ARM 템플릿에서 환경 변수를 구성하려면 템플릿에 다음 코드를 추가하고 *\<...>* 자리 표시자를 고유한 값으로 바꿉니다. 자세한 내용은 [Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json)를 참조하세요.

```ARM template
"environmentVariables": {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

## <a name="view-reports-in-dynatrace"></a>Dynatrace에서 보고서 보기

이 섹션에서는 Dynatrace에서 다양한 보고서를 찾는 방법을 설명합니다.

> [!NOTE]
> Dynatrace 메뉴와 사용자 인터페이스는 점진적으로 발전할 것입니다. 이러한 이유로 대시보드는 Dynatrace 웹 사이트의 다른 섹션으로 이동될 수 있으며 다음 스크린샷은 사용자 인터페이스의 현재 버전을 반영하지 않을 수 있습니다.

환경 변수를 애플리케이션에 추가하면 Dynatrace가 데이터 수집을 시작합니다. 보고서를 보려면 [Dynatrace 메뉴](https://www.dynatrace.com/support/help/get-started/navigation/)를 사용하고 **서비스** 로 이동한 다음 애플리케이션을 선택합니다.

**\<your-app-name>/세부 정보/서비스 흐름** 에서 **서비스 흐름** 을 찾을 수 있습니다.

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png" alt-text="Dynatrace '서비스 흐름' 보고서의 스크린샷." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png":::

**\<your-app-name>/세부 정보/메서드 핫스폿** 에서 **메서드 핫스폿** 을 찾을 수 있습니다.

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png" alt-text="'메서드 핫스폿' 보고서의 스크린샷." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png":::

**\<your-app-name>/세부 정보/응답 시간 분석** 에서 **데이터베이스 문** 을 찾을 수 있습니다.

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png" alt-text="'응답 시간 분석' 보고서 및 '데이터베이스 문' 섹션의 스크린샷" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png":::

그런 다음 **다차원 분석** 섹션으로 이동합니다.

**다차원 분석/상위 데이터베이스 문** 에서 **상위 데이터베이스 문** 을 찾을 수 있습니다.

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png" alt-text="'상위 데이터베이스 문' 보고서의 스크린샷." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png":::

**다차원 분석/예외 개요** 에서 **예외 개요** 를 찾을 수 있습니다.

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png" alt-text="'예외 개요' 보고서의 스크린샷." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png":::

그런 다음 **프로파일링 및 최적화** 섹션으로 이동합니다.

**프로파일링 및 최적화/CPU 분석** 에서 **CPU 분석** 을 찾을 수 있습니다.

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png" alt-text="'CPU 분석' 보고서의 스크린샷." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png":::

그런 다음 **데이터베이스** 섹션으로 이동합니다.

**데이터베이스/세부 정보/역추적** 에서 **역추적** 를 찾을 수 있습니다.

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png" alt-text="역추적 보고서의 스크린샷." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png":::

## <a name="view-dynatrace-oneagent-logs"></a>Dynatrace OneAgent 로그 보기

기본적으로 Azure Spring Cloud는 Dynatrace OneAgent의 *정보* 수준 로그를 `STDOUT`에 인쇄합니다. 로그는 애플리케이션 로그와 혼합됩니다. 애플리케이션 로그에서 명시적 에이전트 버전을 확인할 수 있습니다.

다음 위치에서 Dynatrace 에이전트의 로그를 가져올 수도 있습니다.

* 로그 Azure Spring Cloud
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

Dynatrace에서 제공하는 일부 환경 변수를 적용하여 Dynatrace OneAgent에 대한 로깅을 구성할 수 있습니다. 예를 들어, `DT_LOGLEVELCON`은 로그 수준을 제어합니다.

> [!CAUTION]
> Dynatrace용 Azure Spring Cloud에서 제공하는 로깅 기본 동작은 재정의하지 않는 것이 좋습니다. 재정의하면 위의 시나리오에서 로깅 시나리오가 차단되고 로그 파일이 손실될 수 있습니다. 예를 들어, `DT_LOGLEVELFILE` 환경 변수를 애플리케이션에 출력해서는 안 됩니다.

## <a name="dynatrace-oneagent-upgrade"></a>Dynatrace OneAgent 업그레이드

Dynatrace OneAgent 자동 업그레이드는 사용하지 않도록 설정되어 있으며 JDK와 함께 분기별로 업그레이드됩니다. 에이전트 업그레이드는 다음 시나리오에 영향을 줄 수 있습니다.

* 업그레이드 전에 Dynatrace OneAgent를 사용하는 기존 애플리케이션은 변경되지 않지만 Dynatrace OneAgent의 새 버전을 사용하려면 다시 시작하거나 재배포해야 합니다.
* 업그레이드 후에 만들어진 애플리케이션은 Dynatrace OneAgent의 새 버전을 사용합니다.

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Vnet 주입 인스턴스 아웃바운드 트래픽 구성

Azure Spring Cloud의 VNet 주입 인스턴스의 경우 Dynatrace 통신 엔드포인트에 대한 아웃바운드 트래픽이 Dynatrace OneAgent에 대해 올바르게 구성되었는지 확인해야 합니다. `communicationEndpoints`를 가져오는 방법에 대한 자세한 내용은 [배포 API - OneAgent에 대한 GET 연결 정보](https://www.dynatrace.com/support/help/dynatrace-api/environment-api/deployment/oneagent/get-connectivity-info/)를 참조하세요. 자세한 내용은 [VNET에서 Azure Spring Cloud 실행에 대한 고객 책임](vnet-customer-responsibilities.md)을 참조하세요.

## <a name="dynatrace-support-model"></a>Dynatrace 지원 모델

Dynatrace OneAgent를 애플리케이션 전용 모드로 배포할 때의 제한 사항에 대한 자세한 내용은 [OneAgent 플랫폼 및 기능 지원 매트릭스](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix)의 [클라우드 애플리케이션 플랫폼](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix/#cloud-application-platforms) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud에서 분산 추적 사용](how-to-distributed-tracing.md)
