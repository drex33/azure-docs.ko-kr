---
title: Azure Resource Manager를 사용하여 배포 및 업그레이드
description: Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터로 애플리케이션 및 서비스를 배포하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ed6bc7d96cb3ea0934929e6543c5e637a9f42c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97930840"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>애플리케이션 및 서비스를 Azure Resource Manager 리소스로 관리

Azure Resource Manager를 통해 Service Fabric 클러스터에 애플리케이션 및 서비스를 배포할 수 있습니다. 즉, 클러스터가 준비될 때까지 기다린 후에 PowerShell 또는 CLI를 통해 애플리케이션을 배포 및 관리하는 대신, 이제 애플리케이션 및 서비스를 JSON으로 나타내고 클러스터와 동일한 Resource Manager 템플릿에 배포할 수 있습니다. 애플리케이션 등록, 프로비저닝 및 배포 프로세스는 모두 한 번에 수행됩니다.

이 방법은 클러스터에 필요한 설치, 거버넌스 또는 클러스터 관리 애플리케이션을 배포할 때 권장되는 방법입니다. 여기에는 [패치 오케스트레이션 애플리케이션](service-fabric-patch-orchestration-application.md), Watchdogs 또는 다른 서비스나 애플리케이션을 배포하기 위해 먼저 클러스터에서 실행 중이어야 하는 모든 애플리케이션이 포함됩니다. 

해당하는 경우 애플리케이션을 Resource Manager 리소스로 관리하여 다음을 향상시킬 수 있습니다.
* 감사 내역: Resource Manager는 모든 작업을 감사하고, 이러한 애플리케이션 및 클러스터에 대한 변경 내용을 추적하는 데 도움이 될 수 있는 자세한 *활동 로그* 를 유지합니다.
* Azure RBAC(Azure 역할 기반 액세스 제어): 클러스터에 배포된 애플리케이션뿐만 아니라 클러스터에 대한 액세스 관리 작업을 동일한 Resource Manager 템플릿을 통해 수행할 수 있습니다.
* Azure Resource Manager(Azure Portal을 통해)는 클러스터 및 중요한 애플리케이션 배포를 관리하기 위한 원스톱 상점이 됩니다.

다음 코드 조각에서는 한 템플릿을 통해 관리될 수 있는 여러 종류의 리소스를 보여 줍니다.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

## <a name="add-a-new-application-to-your-resource-manager-template"></a>Resource Manager 템플릿에 새 애플리케이션 추가

1. 배포를 위해 클러스터의 Resource Manager 템플릿을 준비합니다. 이에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.
2. 클러스터에 배포하려는 일부 애플리케이션을 고려해보세요. 다른 애플리케이션이 종속성을 가질 수 있는 애플리케이션이 항상 실행되고 있나요? 클러스터 거버넌스 또는 설치 애플리케이션을 배포할 계획인가요? 이러한 종류의 애플리케이션은 위에서 설명한 것처럼 Resource Manager 템플릿을 통해 가장 잘 관리됩니다. 
3. 해당 방식으로 배포하려는 애플리케이션을 결정했으면 애플리케이션을 패키지하고, 압축하고, 스토리지 공유에 배치해야 합니다. Azure Resource Manager가 배포 중에 사용할 수 있도록 하려면 REST 엔드포인트를 통해 공유에 액세스할 수 있어야 합니다. 자세한 내용은 [스토리지 계정 만들기](service-fabric-concept-resource-model.md#create-a-storage-account)를 참조하세요.
4. Resource Manager 템플릿에서 클러스터 선언 아래에는 각 애플리케이션의 속성이 설명되어 있습니다. 이러한 속성에는 복제본 또는 인스턴스 수와 리소스(다른 애플리케이션이나 서비스) 간의 모든 종속성 체인이 포함됩니다. 이 목록은 애플리케이션 또는 서비스 매니페스트를 대신하지 않으며, 포함된 일부 항목을 클러스터의 Resource Manager 템플릿의 일부로 설명합니다. 다음은 상태 비저장 서비스 *Service1* 과 상태 저장 서비스 *Service2* 를 *Application1* 의 일부로 배포하는 작업을 포함하는 샘플 템플릿입니다.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > 개별 템플릿 속성에 대한 사용 및 세부 정보를 찾으려면 Service Fabric [Azure Resource Manager 참조](/azure/templates/microsoft.servicefabric/clusters/applicationtypes)를 참조하세요.

5. 배포합니다. 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Service Fabric 리소스 공급자 애플리케이션 리소스 제거
다음은 클러스터에서 프로비저닝이 해제되도록 앱 패키지를 트리거하여 사용된 디스크 공간을 정리합니다.
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
ARM 템플릿에서 Microsoft.ServiceFabric/clusters/application을 제거하는 작업만으로는 애플리케이션의 프로비저닝이 해제되지 않습니다.

>[!NOTE]
> 제거가 완료되면 SFX 또는 ARM의 패키지 버전이 더 이상 표시되지 않습니다. 실행 중인 애플리케이션에서 사용하는 애플리케이션 유형 버전 리소스는 삭제할 수 없으며, ARM/SFRP에서 이를 차단합니다. 실행 중인 패키지의 프로비저닝을 해제하려는 경우 SF 런타임에서 이를 차단합니다.


## <a name="manage-an-existing-application-via-resource-manager"></a>Resource Manager를 통해 기존 애플리케이션 관리

클러스터가 이미 작동 중이며 Resource Manager 리소스로 관리하려는 일부 애플리케이션이 이미 클러스터에 배포된 경우, 애플리케이션을 제거한 후 다시 배포하는 대신, 동일한 API를 통해 PUT 호출을 사용하여 애플리케이션이 Resource Manager 리소스로 승인되도록 할 수 있습니다. 자세한 내용은 [Service Fabric 애플리케이션 리소스 모델이란?](./service-fabric-concept-resource-model.md)을 참조하세요.

> [!NOTE]
> 클러스터 업그레이드에서 비정상 응용 프로그램을 무시하도록 허용하려면 고객은 “upgradeDescription/healthPolicy” 섹션에서 “maxPercentUnhealthyApplications: 100”을 지정할 수 있습니다. 모든 설정에 대한 자세한 설명은 [Service Fabrics REST API 클러스터 업그레이드 정책 설명서](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)에 있습니다.

## <a name="next-steps"></a>다음 단계

* [Service Fabric CLI](service-fabric-cli.md) 또는 [PowerShell](service-fabric-deploy-remove-applications.md)을 사용하여 클러스터에 다른 애플리케이션을 배포합니다. 
* [Service Fabric 클러스터 업그레이드](service-fabric-cluster-upgrade.md)
