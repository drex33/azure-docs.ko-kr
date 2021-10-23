---
title: 빠른 시작 - Azure CLI를 사용하여 Azure Spring Cloud 프로비저닝
description: 이 빠른 시작에서는 Azure CLI를 사용하여 기존 가상 네트워크에 Spring Cloud 클러스터를 배포하는 방법을 보여 줍니다.
services: azure-cli
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-java
ms.author: vramasubbu
ms.date: 10/12/2021
ms.openlocfilehash: a1e4a03b5a9f2c173bb7a8a301fd20997b738005
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003007"
---
# <a name="quickstart-provision-azure-spring-cloud-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Spring Cloud 프로비저닝

이 빠른 시작에서는 Azure CLI를 사용하여 기존 가상 네트워크에 Azure Spring Cloud 클러스터를 배포하는 방법을 설명합니다.

Azure Spring Cloud를 사용하면 코드 변경 없이 Spring Boot 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다. 이 서비스는 개발자가 코드에 집중할 수 있도록 Spring Cloud 애플리케이션의 인프라를 관리합니다. Azure Spring Cloud는 포괄적인 모니터링 및 진단, 구성 관리, 서비스 검색, CI/CD 통합, 청록색 배포 등을 사용하여 수명 주기 관리를 제공합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.
* Azure Spring Cloud 클러스터 전용 서브넷 두 개. 하나는 서비스 런타임용이고, 하나는 Spring Boot 마이크로 서비스 애플리케이션용입니다. 서브넷 및 가상 네트워크 요구 사항은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) 섹션을 참조하세요.
* Azure Spring Cloud 진단 설정 및 작업 영역 기반 Application Insights 리소스의 기존 Log Analytics 작업 영역. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md) 및 [Azure Spring Cloud의 Application Insights Java In-Process 에이전트](how-to-application-insights.md)를 참조하세요.
* Azure Spring Cloud 클러스터에서 사용하도록 식별된 세 개의 내부 CIDR(Classless Inter-Domain Routing) 범위(각각 */16* 이상). 이러한 CIDR 범위는 직접 라우팅할 수 없으며 Azure Spring Cloud 클러스터 내부에서만 사용됩니다. 클러스터는 내부 Spring Cloud CIDR 범위 또는 클러스터 가상 네트워크 주소 범위 내에 포함된 IP 범위에 *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* 또는 *192.0.2.0/24* 를 사용할 수 없습니다.
* 가상 네트워크에 부여된 서비스 권한. Azure Spring Cloud 리소스 공급자가 추가 배포 및 유지 관리를 위해 가상 네트워크에서 전용 및 동적 서비스 주체를 부여하려면 가상 네트워크에 대한 소유자 권한이 있어야 합니다. 지침 및 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크에 서비스 권한 부여](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) 섹션을 참조하세요.
* Azure Firewall 또는 NVA(네트워크 가상 어플라이언스)를 사용하는 경우 다음 필수 조건도 충족해야 합니다.
   * 네트워크 및 FQDN(정규화된 도메인 이름) 규칙. 자세한 내용은 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)을 참조하세요.
   * 각 서비스 런타임 및 Spring Boot 마이크로 서비스 애플리케이션 서브넷에 적용되는 고유한 UDR(사용자 정의 경로). UDR에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요. UDR은 Spring Cloud 클러스터를 배포하기 전에 NVA의 대상과 함께 *0.0.0.0/0* 에 대한 경로로 구성해야 합니다. 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [사용자 고유의 경로 테이블 가져오기](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) 섹션을 참조하세요.
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="review-the-azure-cli-deployment-script"></a>Azure CLI 배포 스크립트 검토

이 빠른 시작에서 사용되는 배포 스크립트는 [Azure Spring Cloud 참조 아키텍처](reference-architecture.md)에서 가져온 것입니다.

```azurecli
#!/bin/bash

echo "Enter Azure Subscription ID: "
read subscription
subscription=$subscription

echo "Enter Azure region for resource deployment: "
read region
location=$region

echo "Enter Azure Spring cloud Resource Group Name: "
read azurespringcloudrg
azurespringcloud_resource_group_name=$azurespringcloudrg

echo "Enter Azure Spring cloud VNet Resource Group Name: "
read azurespringcloudvnetrg
azurespringcloud_vnet_resource_group_name=$azurespringcloudvnetrg

echo "Enter Azure Spring cloud Spoke VNet : "
read azurespringcloudappspokevnet
azurespringcloudappspokevnet=$azurespringcloudappspokevnet

echo "Enter Azure Spring cloud App SubNet : "
read azurespringcloudappsubnet
azurespringcloud_app_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudappsubnet

echo "Enter Azure Spring cloud Service SubNet : "
read azurespringcloudservicesubnet
azurespringcloud_service_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudservicesubnet

echo "Enter Azure Log Analytics Workspace Resource Group Name: "
read loganalyticsrg
loganalyticsrg=$loganalyticsrg

echo "Enter Log Analytics Workspace Resource ID: "
read workspace
workspaceID='/subscriptions/'$subscription'/resourcegroups/'$loganalyticsrg'/providers/microsoft.operationalinsights/workspaces/'$workspace

echo "Enter Reserved CIDR Ranges for Azure Spring Cloud: "
read reservedcidrrange
reservedcidrrange=$reservedcidrrange

echo "Enter key=value pair used for tagging Azure Resources (space separated for multiple tags): "
read tag
tags=$tag

randomstring=$(LC_ALL=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 13 | head -n 1)
azurespringcloud_service='spring-'$randomstring #Name of unique Spring Cloud resource
azurespringcloud_appinsights=$azurespringcloud_service
azurespringcloud_resourceid='/subscriptions/'$subscription'/resourceGroups/'$azurespringcloud_resource_group_name'/providers/Microsoft.AppPlatform/Spring/'$azurespringcloud_service

# Create Application Insights
az monitor app-insights component create \
    --app ${azurespringcloud_service} \
    --location ${location} \
    --kind web \
    -g ${azurespringcloudrg} \
    --application-type web \
    --workspace ${workspaceID}

# Create Azure Spring Cloud Instance
az spring-cloud create \
   -n ${azurespringcloud_service} \
   -g ${azurespringcloudrg} \
   -l ${location} \
   --enable-java-agent true \
   --app-insights ${azurespringcloud_service} \
   --sku Standard \
   --app-subnet ${azurespringcloud_app_subnet_name} \
   --service-runtime-subnet ${azurespringcloud_service_subnet_name} \
   --reserved-cidr-range ${reservedcidrrange} \
   --tags ${tags}

# Update diagnostic setting for Azure Spring Cloud instance
az monitor diagnostic-settings create  \
   --name monitoring \
   --resource ${azurespringcloud_resourceid} \
   --logs    '[{"category": "ApplicationConsole","enabled": true}]' \
   --workspace  ${workspaceID}
```

## <a name="deploy-the-cluster"></a>클러스터 배포

Azure CLI 스크립트를 사용하여 Azure Spring Cloud 클러스터를 배포하려면 다음 단계를 수행합니다.

1. 다음 명령을 사용하여 Azure에 로그인합니다.

   ```azurecli
   az login
   ```

   로그인한 후에 이 명령은 사용자에게 액세스 권한이 있는 모든 구독에 대한 정보를 출력합니다. 사용할 구독의 이름과 ID를 적어둡니다.

1. 대상 구독을 설정합니다.

   ```azurecli
   az account set --subscription "<your subscription name>"
   ```

1. Azure Spring Cloud 리소스 공급자를 등록합니다.

   ```azurecli
   az provider register --namespace 'Microsoft.AppPlatform'
   ```

1. Azure CLI에 필요한 확장을 추가합니다.

   ```azurecli
   az extension add --name spring-cloud
   ```

1. [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)에 표시된 대로 Azure Spring Cloud를 사용할 수 있는 지역의 배포 위치를 선택합니다.

1. 다음 명령을 사용하여 Azure 위치 목록을 생성합니다. 이전 단계에서 선택한 지역에 대한 짧은 **이름** 값을 적어둡니다.

   ```azurecli
   az account list-locations --output table
   ```

1. 리소스를 배포할 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name <your-resource-group-name> --location <location-name>
   ```

1. [deploySpringCloud.sh](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/deploySpringCloud.sh) Bash 스크립트를 로컬로 저장한 다음, Bash 프롬프트에서 실행합니다.

   ```azurecli
   ./deploySpringCloud.sh
   ```

1. 스크립트에서 요구할 경우 다음 값을 입력합니다.

   - 이전에 저장한 Azure 구독 ID
   - 이전에 저장한 Azure 위치 이름
   - 이전에 만든 리소스 그룹의 이름
   - 리소스를 배포할 가상 네트워크 리소스 그룹의 이름
   - 스포크 가상 네트워크의 이름(예: *vnet-spoke*)
   - Spring Cloud App Service에서 사용할 서브넷의 이름(예: *snet-app*)
   - Spring Cloud 런타임 서비스에서 사용할 서브넷의 이름(예: *snet-runtime*)
   - Azure Log Analytics 작업 영역이 진단 로그를 저장하는 데 사용할 리소스 그룹의 이름
   - Azure Log Analytics 작업 영역의 이름(예: *la-cb5sqq6574o2a*)
   - Azure Spring Cloud에서 사용할 가상 네트워크의 CIDR 범위(예: *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*)
   - 태그를 지원하는 모든 리소스에서 태그로 적용할 키/값 쌍. 자세한 내용은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요. 여러 태그를 적용하려면 공백으로 구분된 목록을 사용합니다(예: *environment=Dev BusinessUnit=finance*).

이 정보를 입력하면 스크립트에서 Azure 리소스를 만들고 배포합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 배포된 리소스를 확인하거나 Azure CLI를 사용하여 배포된 리소스를 나열할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다. 더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 리소스가 삭제됩니다. Azure CLI를 사용하여 리소스 그룹을 삭제하려면 다음 명령을 사용합니다.

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 기존 가상 네트워크에 Azure Spring Cloud 인스턴스를 배포한 후 배포의 유효성을 검사했습니다. Azure Spring Cloud에 대해 자세히 알아보려면 아래 리소스를 계속 진행하세요.

- 아래 위치에서 다음 샘플 애플리케이션 중 하나를 배포합니다.
   - [MySQL 통합을 사용하는 펫 클리닉 앱 ](https://github.com/azure-samples/spring-petclinic-microservices)(MySQL 백 엔드를 사용하는 마이크로 서비스).
   - [간단한 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Azure Spring Cloud에서 [사용자 지정 도메인](tutorial-custom-domain.md)을 사용합니다.
- [Azure Application Gateway](expose-apps-gateway-azure-firewall.md)를 사용하여 Azure Spring Cloud 애플리케이션을 인터넷에 노출합니다.
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)를 기반으로 하는 안전한 엔드투엔드 [Azure Spring Cloud 참조 아키텍처](reference-architecture.md)를 확인합니다.
