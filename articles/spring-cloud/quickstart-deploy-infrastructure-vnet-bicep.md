---
title: 빠른 시작 - Bicep을 사용하여 Azure Spring Cloud 프로비저닝
description: 이 빠른 시작에서는 Bicep을 사용하여 기존 가상 네트워크에 Spring Cloud 클러스터를 배포하는 방법을 보여 줍니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ssarwa
ms.date: 11/12/2021
ms.openlocfilehash: ac155d1937638b4be26c74214d63d35edd009322
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488814"
---
# <a name="quickstart-provision-azure-spring-cloud-using-bicep"></a>빠른 시작: Bicep을 사용하여 Azure Spring Cloud 프로비저닝

이 빠른 시작에서는 Bicep 템플릿를 사용하여 기존 가상 네트워크에 Azure Spring Cloud 클러스터를 배포하는 방법을 설명합니다.

Azure Spring Cloud를 사용하면 코드 변경 없이 Spring Boot 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다. 이 서비스는 개발자가 코드에 집중할 수 있도록 Spring Cloud 애플리케이션의 인프라를 관리합니다. Azure Spring Cloud는 포괄적인 모니터링 및 진단, 구성 관리, 서비스 검색, CI/CD 통합, 청록색 배포 등을 사용하여 수명 주기 관리를 제공합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.
* Azure Spring Cloud 클러스터 전용 서브넷 두 개. 하나는 서비스 런타임용이고, 하나는 Spring Boot 마이크로 서비스 애플리케이션용입니다. 서브넷 및 가상 네트워크 요구 사항은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) 섹션을 참조하세요.
* Azure Spring Cloud 진단 설정을 위한 기존 Log Analytics 작업 영역. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md)을 참조하세요.
* Azure Spring Cloud 클러스터에서 사용하도록 식별된 세 개의 내부 CIDR(Classless Inter-Domain Routing) 범위(각각 */16* 이상). 이러한 CIDR 범위는 직접 라우팅할 수 없으며 Azure Spring Cloud 클러스터 내부에서만 사용됩니다. 클러스터는 내부 Spring Cloud CIDR 범위 또는 클러스터 가상 네트워크 주소 범위 내에 포함된 IP 범위에 *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* 또는 *192.0.2.0/24* 를 사용할 수 없습니다.
* 가상 네트워크에 부여된 서비스 권한. Azure Spring Cloud 리소스 공급자가 추가 배포 및 유지 관리를 위해 가상 네트워크에서 전용 및 동적 서비스 주체를 부여하려면 가상 네트워크에 대한 소유자 권한이 있어야 합니다. 지침 및 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [가상 네트워크에 서비스 권한 부여](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) 섹션을 참조하세요.
* Azure Firewall 또는 NVA(네트워크 가상 어플라이언스)를 사용하는 경우 다음 필수 조건도 충족해야 합니다.
   * 네트워크 및 FQDN(정규화된 도메인 이름) 규칙. 자세한 내용은 [가상 네트워크 요구 사항](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)을 참조하세요.
   * 각 서비스 런타임 및 Spring Boot 마이크로 서비스 애플리케이션 서브넷에 적용되는 고유한 UDR(사용자 정의 경로). UDR에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요. UDR은 Spring Cloud 클러스터를 배포하기 전에 NVA의 대상과 함께 *0.0.0.0/0* 에 대한 경로로 구성해야 합니다. 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포](how-to-deploy-in-azure-virtual-network.md)의 [사용자 고유의 경로 테이블 가져오기](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) 섹션을 참조하세요.
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="deploy-using-bicep"></a>Bicep을 사용하여 배포

클러스터를 배포하려면 다음 단계를 따르세요.

1. 다음 내용으로 *azuredeploy.bicep* 파일을 만듭니다.

   ```Bicep
   @description('The instance name of the Azure Spring Cloud resource')
   param springCloudInstanceName string

   @description('The name of the Application Insights instance for Azure Spring Cloud')
   param appInsightsName string

   @description('The resource ID of the existing Log Analytics workspace. This will be used for both diagnostics logs and Application    Insights')
   param laWorkspaceResourceId string

   @description('The resourceID of the Azure Spring Cloud App Subnet')
   param springCloudAppSubnetID string

   @description('The resourceID of the Azure Spring Cloud Runtime Subnet')
   param springCloudRuntimeSubnetID string

   @description('Comma-separated list of IP address ranges in CIDR format. The IP ranges are reserved to host underlying Azure Spring Cloud    infrastructure, which should be 3 at least /16 unused IP ranges, must not overlap with any Subnet IP ranges')
   param springCloudServiceCidrs string = '10.0.0.0/16,10.2.0.0/16,10.3.0.1/16'

   @description('The tags that will be associated to the Resources')
   param tags object = {
     environment: 'lab'
   }

   var springCloudSkuName = 'S0'
   var springCloudSkuTier = 'Standard'
   var location = resourceGroup().location

   resource appInsights 'Microsoft.Insights/components@2020-02-02-preview' = {
     name: appInsightsName
     location: location
     kind: 'web'
     tags: tags
     properties: {
       Application_Type: 'web'
       Flow_Type: 'Bluefield'
       Request_Source: 'rest'
       WorkspaceResourceId: laWorkspaceResourceId
     }
   }

   resource springCloudInstance 'Microsoft.AppPlatform/Spring@2020-07-01' = {
     name: springCloudInstanceName
     location: location
     tags: tags
     sku: {
       name: springCloudSkuName
       tier: springCloudSkuTier
     }
     properties: {
       networkProfile: {
         serviceCidr: springCloudServiceCidrs
         serviceRuntimeSubnetId: springCloudRuntimeSubnetID
         appSubnetId: springCloudAppSubnetID
       }
     }
   }

   resource springCloudMonitoringSettings 'Microsoft.AppPlatform/Spring/monitoringSettings@2020-07-01' = {
     name: '${springCloudInstance.name}/default'
     properties: {
       traceEnabled: true
       appInsightsInstrumentationKey: appInsights.properties.InstrumentationKey
     }
   }

   resource springCloudDiagnostics 'microsoft.insights/diagnosticSettings@2017-05-01-preview' = {
     name: 'monitoring'
     scope: springCloudInstance
     properties: {
       workspaceId: laWorkspaceResourceId
       logs: [
         {
           category: 'ApplicationConsole'
           enabled: true
           retentionPolicy: {
             days: 30
             enabled: false
           }
         }
       ]
     }
   }
   ```

1. Bash 창을 열고 다음 Azure CLI 명령을 실행하여 *\<value>* 자리 표시자를 다음 값으로 바꿉니다.

   - **resource-group:** Spring Cloud 인스턴스를 배포하기 위한 리소스 그룹 이름입니다.
   - **springCloudInstanceName:** Azure Spring Cloud 리소스의 이름.
   - **appInsightsName:** Azure Spring Cloud에 대한 Application Insights 인스턴스의 이름.
   - **laWorkspaceResourceId:** 기존 Log Analytics 작업 영역(예: */subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* )의 리소스 ID.
   - **springCloudAppSubnetID:** Azure Spring Cloud 앱 서브넷의 resourceID.
   - **springCloudRuntimeSubnetID:** Azure Spring Cloud 런타임 서브넷의 resourceID.
   - **springCloudServiceCidrs:** CIDR 형식의 쉼표로 구분된 IP 주소 범위 목록(총 3개). IP 범위는 기본 Azure Spring Cloud 인프라를 호스트하도록 예약되어 있습니다. 이러한 세 개의 범위는 */16* 이상의 사용되지 않는 IP 범위여야 하며, 네트워크 내에서 사용되는 라우팅할 수 있는 서브넷 IP 범위와 겹치지 않아야 합니다.

   ```azurecli
   az deployment group create \
       --resource-group <value> \
       --name initial \
       --template-file azuredeploy.bicep \
       --parameters \
           springCloudInstanceName=<value> \
           appInsightsName=<value> \
           laWorkspaceResourceId=<value> \
           springCloudAppSubnetID=<value> \
           springCloudRuntimeSubnetID=<value> \
           springCloudServiceCidrs=<value>
   ```

   이 명령은 Bicep 템플릿을 사용하여 기존 가상 네트워크에서 Azure Spring Cloud 인스턴스를 만듭니다. 이 명령은 또한 기존 Azure Monitor Log Analytics 작업 영역에 작업 영역 기반 Application Insights 인스턴스를 만듭니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 배포된 리소스를 확인하거나 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 배포된 리소스를 나열할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다. 더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 리소스가 삭제됩니다. Azure CLI를 사용하여 리소스 그룹을 삭제하려면 다음 명령을 사용합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Bicep을 사용하여 기존 가상 네트워크에 Azure Spring Cloud 인스턴스를 배포한 후 배포의 유효성을 검사했습니다. Azure Spring Cloud에 대해 자세히 알아보려면 아래 리소스를 계속 진행하세요.

- 아래 위치에서 다음 샘플 애플리케이션 중 하나를 배포합니다.
   - [MySQL 통합을 사용하는 펫 클리닉 앱 ](https://github.com/azure-samples/spring-petclinic-microservices)(MySQL 백 엔드를 사용하는 마이크로 서비스).
   - [간단한 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Azure Spring Cloud에서 [사용자 지정 도메인](tutorial-custom-domain.md)을 사용합니다.
- [Azure Application Gateway](expose-apps-gateway-azure-firewall.md)를 사용하여 Azure Spring Cloud의 애플리케이션을 인터넷에 노출합니다.
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)를 기반으로 하는 안전한 엔드투엔드 [Azure Spring Cloud 참조 아키텍처](reference-architecture.md)를 확인합니다.
