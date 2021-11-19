---
title: 빠른 시작 - ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Spring Cloud 프로비저닝
description: 이 빠른 시작에서는 ARM 템플릿을 사용하여 기존 가상 네트워크에 Spring Cloud 클러스터를 배포하는 방법을 보여 줍니다.
services: azure-resource-manager
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-java
ms.author: rhudson
ms.date: 11/12/2021
ms.openlocfilehash: c34d2d9f88f97b80892b4329def4ca84f5f7b70c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488870"
---
# <a name="quickstart-provision-azure-spring-cloud-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Spring Cloud 프로비저닝

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 기존 가상 네트워크에 Azure Spring Cloud 클러스터를 배포하는 방법을 설명합니다.

Azure Spring Cloud를 사용하면 코드 변경 없이 Spring Boot 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다. 이 서비스는 개발자가 코드에 집중할 수 있도록 Spring Cloud 애플리케이션의 인프라를 관리합니다. Azure Spring Cloud는 포괄적인 모니터링 및 진단, 구성 관리, 서비스 검색, CI/CD 통합, 청록색 배포 등을 사용하여 수명 주기 관리를 제공합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.
* Azure Spring Cloud 클러스터 전용 서브넷 두 개. 하나는 서비스 런타임용이고, 하나는 Spring Boot 마이크로 서비스 애플리케이션용입니다. 서브넷 및 가상 네트워크 요구 사항은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) 섹션을 참조하세요.
* Azure Spring Cloud 진단 설정 및 작업 영역 기반 Application Insights 리소스의 기존 Log Analytics 작업 영역. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md) 및 [Azure Spring Cloud의 Application Insights Java In-Process 에이전트](how-to-application-insights.md)를 참조하세요.
* Azure Spring Cloud 클러스터에서 사용하도록 식별된 세 개의 내부 CIDR(Classless Inter-Domain Routing) 범위(각각 */16* 이상). 이러한 CIDR 범위는 직접 라우팅할 수 없으며 Azure Spring Cloud 클러스터 내부에서만 사용됩니다. 클러스터는 내부 Spring Cloud CIDR 범위 또는 클러스터 가상 네트워크 주소 범위 내에 포함된 IP 범위에 *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* 또는 *192.0.2.0/24* 를 사용할 수 없습니다.
* 가상 네트워크에 부여된 서비스 권한. Azure Spring Cloud 리소스 공급자가 추가 배포 및 유지 관리를 위해 가상 네트워크에서 전용 및 동적 서비스 주체를 부여하려면 가상 네트워크에 대한 소유자 권한이 있어야 합니다. 지침 및 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크에 서비스 권한 부여](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) 섹션을 참조하세요.
* Azure Firewall 또는 NVA(네트워크 가상 어플라이언스)를 사용하는 경우 다음 필수 조건도 충족해야 합니다.

   * 네트워크 및 FQDN(정규화된 도메인 이름) 규칙. 자세한 내용은 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)을 참조하세요.
   * 각 서비스 런타임 및 Spring Boot 마이크로 서비스 애플리케이션 서브넷에 적용되는 고유한 UDR(사용자 정의 경로). UDR에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요. UDR은 Spring Cloud 클러스터를 배포하기 전에 NVA의 대상과 함께 *0.0.0.0/0* 에 대한 경로로 구성해야 합니다. 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [사용자 고유의 경로 테이블 가져오기](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) 섹션을 참조하세요.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure Spring Cloud 참조 아키텍처](reference-architecture.md)에서 가져온 것입니다.

:::code language="json" source="~/azure-spring-cloud-reference-architecture/ARM/brownfield-deployment/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [Microsoft.AppPlatform/Spring](/azure/templates/microsoft.appplatform/spring): Azure Spring Cloud 인스턴스를 만듭니다.
* [Microsoft.Insights/components](/azure/templates/microsoft.insights/components): Application Insights 작업 영역을 만듭니다.

Azure CLI, Terraform 및 Bicep 배포에 대해서는 GitHub의 [Azure Spring Cloud 참조 아키텍처](https://github.com/Azure/azure-spring-cloud-reference-architecture) 리포지토리를 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 배포하려면 다음 단계를 따릅니다.

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 기존 Virtual Network에 Azure Spring Cloud 인스턴스를 만들고 기존 Azure Monitor Log Analytics 작업 영역에 작업 영역 기반 Application Insights 인스턴스를 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

2. 다음 필드에 대한 값을 입력합니다.

   - **리소스 그룹:** **새로 만들기** 를 선택하고 **리소스 그룹** 에 고유한 이름을 입력한 다음, **확인** 을 선택합니다.
   - **springCloudInstanceName:** Azure Spring Cloud 리소스의 이름을 입력합니다.
   - **appInsightsName:** Azure Spring Cloud에 대한 Application Insights 인스턴스의 이름을 입력합니다.
   - **laWorkspaceResourceId:** 기존 Log Analytics 작업 영역(예: */subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* )의 리소스 ID를 입력합니다.
   - **springCloudAppSubnetID:** Azure Spring Cloud 앱 서브넷의 resourceID를 입력합니다.
   - **springCloudRuntimeSubnetID:** Azure Spring Cloud 런타임 서브넷의 resourceID를 입력합니다.
   - **springCloudServiceCidrs:** 쉼표로 구분된 IP 주소 범위 목록(총 3개)을 CIDR 형식으로 입력합니다. IP 범위는 기본 Azure Spring Cloud 인프라를 호스트하도록 예약되어 있습니다. 이러한 세 개의 범위는 */16* 이상의 사용되지 않는 IP 범위여야 하며, 네트워크 내에서 사용되는 라우팅할 수 있는 서브넷 IP 범위와 겹치지 않아야 합니다.
   - **tags:** 사용자 지정 태그를 입력합니다.

3. **검토 + 만들기**, **만들기** 를 차례로 선택합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 배포된 리소스를 확인하거나 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 배포된 리소스를 나열할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다. 더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 리소스가 삭제됩니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음 명령을 사용합니다.

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 기존 가상 네트워크에 Azure Spring Cloud 인스턴스를 배포한 후 배포의 유효성을 검사했습니다. Azure Spring Cloud 및 Azure Resource Manager에 대해 자세히 알아보려면 다음 리소스를 계속 진행하세요.

- 아래 위치에서 다음 샘플 애플리케이션 중 하나를 배포합니다.
   - [MySQL 통합을 사용하는 펫 클리닉 앱 ](https://github.com/azure-samples/spring-petclinic-microservices)(MySQL 백 엔드를 사용하는 마이크로 서비스).
   - [간단한 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Azure Spring Cloud에서 [사용자 지정 도메인](tutorial-custom-domain.md)을 사용합니다.
- [Azure Application Gateway](expose-apps-gateway-azure-firewall.md)를 사용하여 Azure Spring Cloud의 애플리케이션을 인터넷에 노출합니다.
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)를 기반으로 하는 안전한 엔드투엔드 [Azure Spring Cloud 참조 아키텍처](reference-architecture.md)를 확인합니다.
- [Azure 리소스 관리자](../azure-resource-manager/management/overview.md)에 대해 자세히 알아봅니다.
