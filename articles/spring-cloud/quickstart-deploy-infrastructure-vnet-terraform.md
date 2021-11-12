---
title: 빠른 시작 - Terraform을 사용하여 Azure Spring Cloud 프로비저닝
description: 이 빠른 시작에서는 Terraform을 사용하여 기존 가상 네트워크에 Spring Cloud 클러스터를 배포하는 방법을 보여 줍니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ariel
ms.date: 10/12/2021
ms.openlocfilehash: 08ec73ffad5f95484840cbda091c1ef528a61f91
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399753"
---
# <a name="quickstart-provision-azure-spring-cloud-using-terraform"></a>빠른 시작: Terraform을 사용하여 Azure Spring Cloud 프로비저닝

이 빠른 시작에서는 Terraform을 사용하여 기존 가상 네트워크에 Azure Spring Cloud 클러스터를 배포하는 방법을 설명합니다.

Azure Spring Cloud를 사용하면 코드 변경 없이 Spring Boot 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다. 이 서비스는 개발자가 코드에 집중할 수 있도록 Spring Cloud 애플리케이션의 인프라를 관리합니다. Azure Spring Cloud는 포괄적인 모니터링 및 진단, 구성 관리, 서비스 검색, CI/CD 통합, 청록색 배포 등을 사용하여 수명 주기 관리를 제공합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.
* [Hashicorp Terraform](https://www.terraform.io/downloads.html)
* Azure Spring Cloud 클러스터 전용 서브넷 두 개. 하나는 서비스 런타임용이고, 하나는 Spring Boot 마이크로 서비스 애플리케이션용입니다. 서브넷 및 가상 네트워크 요구 사항은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) 섹션을 참조하세요.
* Azure Spring Cloud 진단 설정 및 작업 영역 기반 Application Insights 리소스의 기존 Log Analytics 작업 영역. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md) 및 [Azure Spring Cloud의 Application Insights Java In-Process 에이전트](how-to-application-insights.md)를 참조하세요.
* Azure Spring Cloud 클러스터에서 사용하도록 식별된 세 개의 내부 CIDR(Classless Inter-Domain Routing) 범위(각각 */16* 이상). 이러한 CIDR 범위는 직접 라우팅할 수 없으며 Azure Spring Cloud 클러스터 내부에서만 사용됩니다. 클러스터는 내부 Spring Cloud CIDR 범위 또는 클러스터 가상 네트워크 주소 범위 내에 포함된 IP 범위에 *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* 또는 *192.0.2.0/24* 를 사용할 수 없습니다.
* 가상 네트워크에 부여된 서비스 권한. Azure Spring Cloud 리소스 공급자가 추가 배포 및 유지 관리를 위해 가상 네트워크에서 전용 및 동적 서비스 주체를 부여하려면 가상 네트워크에 대한 소유자 권한이 있어야 합니다. 지침 및 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크에 서비스 권한 부여](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) 섹션을 참조하세요.
* Azure Firewall 또는 NVA(네트워크 가상 어플라이언스)를 사용하는 경우 다음 필수 조건도 충족해야 합니다.
   * 네트워크 및 FQDN(정규화된 도메인 이름) 규칙. 자세한 내용은 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)을 참조하세요.
   * 각 서비스 런타임 및 Spring Boot 마이크로 서비스 애플리케이션 서브넷에 적용되는 고유한 UDR(사용자 정의 경로). UDR에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요. UDR은 Spring Cloud 클러스터를 배포하기 전에 NVA의 대상과 함께 *0.0.0.0/0* 에 대한 경로로 구성해야 합니다. 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [사용자 고유의 경로 테이블 가져오기](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) 섹션을 참조하세요.

## <a name="review-the-configuration-file"></a>구성 파일 검토

이 빠른 시작에서 사용되는 구성 파일은 [Azure Spring Cloud 참조 아키텍처](reference-architecture.md)에서 가져온 것입니다.

```hcl
provider "azurerm" {
    features {}
}

resource "azurerm_resource_group" "sc_corp_rg" {
    name      = var.resource_group_name
    location  = var.location
}

resource "azurerm_application_insights" "sc_app_insights" {
  name                = var.app_insights_name
  location            = var.location
  resource_group_name = var.resource_group_name
  application_type    = "web"
  depends_on = [azurerm_resource_group.sc_corp_rg]
}

resource "azurerm_spring_cloud_service" "sc" {
  name                = var.sc_service_name
  resource_group_name = var.resource_group_name
  location            = var.location

  network {
    app_subnet_id                   = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.app_subnet_id}"
    service_runtime_subnet_id       = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.service_runtime_subnet_id}"
    cidr_ranges                     = var.sc_cidr
  }

  timeouts {
      create = "60m"
      delete = "2h"
  }

  depends_on = [azurerm_resource_group.sc_corp_rg]
  tags = var.tags

}

resource "azurerm_monitor_diagnostic_setting" "sc_diag" {
  name                        = "monitoring"
  target_resource_id          = azurerm_spring_cloud_service.sc.id
  log_analytics_workspace_id = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.OperationalInsights/workspaces/${var.sc_law_id}"

  log {
    category = "ApplicationConsole"
    enabled  = true

    retention_policy {
      enabled = false
    }
  }

  metric {
    category = "AllMetrics"

    retention_policy {
      enabled = false
    }
  }
}
```

## <a name="apply-the-configuration"></a>구성 적용

구성을 적용하려면 다음 단계를 수행합니다.

1. [variables.tf](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/terraform/brownfield-deployment/variable.tf) 파일을 로컬에 저장한 다음, 편집기에서 엽니다.

1. 파일을 편집하여 다음 값을 추가합니다.

   - 배포할 Azure 계정의 구독 ID

   - [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)에 표시된 대로 Azure Spring Cloud를 사용할 수 있는 지역의 배포 위치. 약식 형태의 위치 이름이 필요합니다. 이 값을 얻으려면 다음 명령을 사용하여 Azure 위치 목록을 생성한 다음, 선택한 지역의 **이름** 값을 조회합니다.

   ```azurecli
   az account list-locations --output table
   ```

   - 배포할 리소스 그룹의 이름
   - 선택한 Spring Cloud 배포 이름
   - 리소스를 배포할 가상 네트워크 리소스 그룹의 이름
   - 스포크 가상 네트워크의 이름(예: *vnet-spoke*)
   - Spring Cloud App Service에서 사용할 서브넷의 이름(예: *snet-app*)
   - Spring Cloud 런타임 서비스에서 사용할 서브넷의 이름(예: *snet-runtime*)
   - Azure Log Analytics 작업 영역의 이름
   - Azure Spring Cloud에서 사용할 가상 네트워크의 CIDR 범위(예: *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*)
   - 태그를 지원하는 모든 리소스에서 태그로 적용할 키/값 쌍. 자세한 내용은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.

1. 다음 명령을 실행하여 Terraform 모듈을 초기화합니다.

   ```bash
   terraform init
   ```

1. 다음 명령을 실행하여 Terraform 배포 계획을 만듭니다.

   ```bash
   terraform plan -out=springcloud.plan
   ```

1. 다음 명령을 실행하여 Terraform 배포 계획을 적용합니다.

   ```bash
   terraform apply springcloud.plan
   ```

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 배포된 리소스를 확인하거나 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 배포된 리소스를 나열할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다. 더 이상 필요하지 않은 경우 다음 명령을 실행하여 이 문서에서 만든 리소스를 삭제합니다.

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Terraform을 사용하여 기존 가상 네트워크에 Azure Spring Cloud 인스턴스를 배포한 후 배포의 유효성을 검사했습니다. Azure Spring Cloud에 대해 자세히 알아보려면 아래 리소스를 계속 진행하세요.

- 아래 위치에서 다음 샘플 애플리케이션 중 하나를 배포합니다.
   - [MySQL 통합을 사용하는 펫 클리닉 앱 ](https://github.com/azure-samples/spring-petclinic-microservices)(MySQL 백 엔드를 사용하는 마이크로 서비스).
   - [간단한 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Azure Spring Cloud에서 [사용자 지정 도메인](tutorial-custom-domain.md)을 사용합니다.
- [Azure Application Gateway](expose-apps-gateway.md)를 사용하여 Azure Spring Cloud 애플리케이션을 인터넷에 노출합니다.
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)를 기반으로 하는 안전한 엔드투엔드 [Azure Spring Cloud 참조 아키텍처](reference-architecture.md)를 확인합니다.
