---
title: Service Fabric 관리형 클러스터 노드에 대한 자동 조정 구성
description: Service Fabric 관리형 클러스터에서 자동 조정 정책을 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 6c2dd14535b726d242d7076bcac94894e4c560dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102907"
---
# <a name="introduction-to-autoscaling-on-service-fabric-managed-clusters-preview"></a>Service Fabric 관리형 클러스터의 자동 조정 소개(미리 보기)
[자동 조정은](../azure-monitor/autoscale/autoscale-overview.md) 뛰어난 탄력성을 제공하며 보조 노드 유형에서 필요에 따라 노드를 추가하거나 감소시킬 수 있습니다. 이 자동화된 탄력적 동작은 워크로드를 서비스하는 노드의 양을 모니터링하고 최적화하여 관리 오버헤드 및 잠재적 비즈니스 영향을 줄입니다. 워크로드에 대한 규칙을 구성하고 자동 조정에서 나머지를 처리하도록 합니다. 정의된 임계값이 충족되면 자동 크기 조정 규칙은 노드 유형의 용량을 조정하는 작업을 수행합니다. 자동 조정은 언제든지 사용하거나 사용하지 않도록 설정하거나 구성할 수 있습니다. 이 문서에서는 배포 예제, 자동 크기 조정을 사용하거나 사용하지 않도록 설정하는 방법 및 예제 자동 크기 조정 정책을 구성하는 방법을 제공합니다.


**요구 사항 및 지원되는 메트릭:**
* 관리형 클러스터에서 자동 조정을 사용하려면 API 버전 `2021-07-01-preview` 이상이어야 합니다.
* 클러스터 SKU는 표준이어야 합니다.
* 클러스터의 보조 노드 유형에서만 구성할 수 있습니다.
* 노드 형식에 대해 자동 크기 조정을 사용하도록 설정한 후 `vmInstanceCount` 리소스를 다시 배포할 때 속성을 로 `-1` 구성합니다.
* [게시된 Azure Monitor 메트릭만](../azure-monitor/essentials/metrics-supported.md) 지원됩니다.

자동 크기 조정이 유용한 일반적인 시나리오는 특정 서비스의 부하가 시간에 따라 달라지는 경우입니다. 예를 들어, 게이트웨이와 같은 서비스는 들어오는 요청을 처리하는 데 필요한 리소스의 양에 따라 크기 조정할 수 있습니다. 이러한 크기 조정 규칙의 예제를 살펴보고 이 문서의 후반부에서 사용합니다.
* 내 게이트웨이의 모든 인스턴스가 평균 70% 이상을 사용하는 경우 인스턴스를 두 개 더 추가하여 게이트웨이 서비스를 확장합니다. 30분마다 이 작업을 수행하지만 총 20개 이상의 인스턴스가 없습니다.
* 내 게이트웨이의 모든 인스턴스가 평균 40% 미만의 코어를 사용하는 경우 인스턴스 하나를 제거하여 서비스를 확장합니다. 30분마다 이 작업을 수행하지만 총 인스턴스 수가 3개 미만인 경우는 없습니다.

## <a name="example-autoscale-deployment"></a>자동 크기 조정 배포 예제

이 예제에서는 다음을 안내합니다. 
* 표준 SKU 만들기 Service Fabric 기본적으로 및 두 개의 노드 유형으로 관리되는 클러스터를 `NT1` `NT2` 만듭니다.
* 보조 노드 형식 에 자동 크기 조정 규칙 추가 `NT2`

>[!NOTE] 
> 노드 유형의 자동 크기 조정은 관리형 클러스터 VMSS CPU 호스트 메트릭을 기반으로 수행됩니다. VMSS 리소스는 템플릿에서 자동으로 확인할 수 있습니다. 


다음은 자동 크기 조정이 구성된 클러스터 설정을 단계별로 안내합니다.

1) 지역에서 리소스 그룹 만들기

   ```powershell 
   Login-AzAccount
   Select-AzSubscription -SubscriptionId $subscriptionid
   New-AzResourceGroup -Name $myresourcegroup -Location $location
   ```

2) 클러스터 리소스 만들기

   이 샘플 [표준 SKU Service Fabric 관리형 클러스터 샘플을 다운로드합니다.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/azuredeploy.json) 이 명령을 실행하여 클러스터 리소스를 배포합니다.

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   adminPassword = $VmAdminPassword
   clientCertificateThumbprint = $clientCertificateThumbprint
   } 
   New-AzResourceGroupDeployment -Name "deploy_cluster" -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterObject $parameters -Verbose
   ```

3) 보조 노드 형식에서 자동 크기 조정 규칙 구성 및 사용
 
   다음 명령을 사용하여 자동 크기 조정을 구성하는 데 사용할 [관리형 클러스터 자동](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) 크기 조정 샘플 템플릿을 다운로드합니다.

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   }
   New-AzResourceGroupDeployment -Name "deploy_autoscale" -ResourceGroupName $resourceGroupName -TemplateFile .\sfmc-deploy-autoscale.json -TemplateParameterObject $parameters -Verbose 
   ```

>[!NOTE]
> 이 배포가 완료되면 이후 클러스터 리소스 배포에서 `vmInstanceCount` 자동 크기 조정 규칙을 사용하도록 설정된 보조 노드 유형에서 속성을 로 설정해야 `-1` 합니다. 이렇게 하면 클러스터 배포가 자동 크기 조정과 충돌하지 않습니다.


## <a name="enable-or-disable-autoscaling-on-a-secondary-node-type"></a>보조 노드 유형에서 자동 조정 사용 또는 사용 안 함

Service Fabric 관리형 클러스터에서 배포된 노드 유형은 기본적으로 자동 조정을 사용하도록 설정하지 않습니다. 자동 조정은 구성되고 사용 가능한 노드 유형별로 언제든지 사용하거나 사용하지 않도록 설정할 수 있습니다.

이 기능을 사용하도록 설정하려면 `enabled` 아래와 같이 ARM 템플릿의 형식에서 속성을 `Microsoft.Insights/autoscaleSettings` 구성합니다.

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": true,
            ...
```

자동 조정을 사용하지 않도록 설정하려면 값을 로 설정합니다. `false`

## <a name="delete-autoscaling-rules"></a>자동 조정 규칙 삭제

노드 유형에 대한 자동 조정 정책 설정을 삭제하려면 다음 PowerShell 명령을 실행하면 됩니다.

```PowerShell
Remove-AzResource -ResourceId "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/microsoft.insights/autoscalesettings/$name" -Force
```

## <a name="set-policies-for-autoscaling"></a>자동 조정에 대한 정책 설정

 Service Fabric 관리형 클러스터는 기본적으로 [자동 조정에 대한 정책을](../azure-monitor/autoscale/autoscale-understanding-settings.md) 구성하지 않습니다. 기본 리소스에서 발생하는 모든 크기 조정 작업에 대해 자동 크기 조정 정책을 구성해야 합니다.

다음 예제에서는 에 대한 정책을 `nodeType2Name` 3개 이상의 노드로 설정하지만 최대 20개의 노드를 확장할 수 있습니다. 평균 CPU 사용량이 지난 30분 동안 70%이고 세분성이 1분인 경우 강화가 트리거됩니다. 평균 CPU 사용량이 1분 단위로 지난 30분 동안 40% 미만이면 규모 축소가 트리거됩니다.

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": "[parameters('enableAutoScale')]",
                "profiles": [
                    {
                        "name": "Autoscale by percentage based on CPU usage",
                        "capacity": {
                            "minimum": "3",
                            "maximum": "20",
                            "default": "3"
                        },
                        "rules": [
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "GreaterThan",
                                  "threshold": 70
                                },
                                "scaleAction": {
                                  "direction": "Increase",
                                  "type": "ChangeCount",
                                  "value": "5",
                                  "cooldown": "PT5M"
                                }
                            },
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "LessThan",
                                  "threshold": 40
                                },
                                "scaleAction": {
                                  "direction": "Decrease",
                                  "type": "ChangeCount",
                                  "value": "1",
                                  "cooldown": "PT5M"
                                }
                            }
                            ]
                    }
                    ]
                }
            }
        
    ]                           
```

위의 예제를 포함하는 [자동 크기 조정을 사용하도록 설정하려면](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) 이 ARM 템플릿을 다운로드할 수 있습니다.


## <a name="view-configured-autoscale-definitions-of-your-managed-cluster-resource"></a>관리되는 클러스터 리소스의 구성된 자동 크기 조정 정의 보기

[Azure Resource Explorer](https://resources.azure.com/)사용하여 구성된 자동 크기 조정 설정을 볼 수 있습니다.

1) [Azure Resource Explorer](https://resources.azure.com/) 이동합니다.

2) `subscriptions`  ->  `SubscriptionName`  ->  `resource group`  ->  `microsoft.insights`  ->  `autoscalesettings` -> 자동 크기 조정 정책 이름(예: )으로 `sfmc01-NT2` 이동합니다. 

   탐색 트리에 다음과 유사한 내용이 표시됩니다.

   ![Azure Resource Explorer 예제 트리 뷰][autoscale-are-tree]


3) 오른쪽에서 이 자동 크기 조정 설정의 전체 정의를 볼 수 있습니다. 

   이 예제에서 자동 크기 조정은 CPU% 기반 스케일 아웃 및 규모 확장 규칙으로 구성됩니다.

   ![Azure Resource Explorer 예제 노드 유형 자동 크기 조정 세부 정보][autoscale-nt-details]



## <a name="troubleshooting"></a>문제 해결

고려할 사항은 다음과 같습니다. 

* 관리형 클러스터 보조 노드 유형에 대해 트리거되는 자동 크기 조정 이벤트 검토

   1) 클러스터 활동 로그로 이동합니다.
   2) 자동 크기 조정 강화/축소 완료 작업에 대한 활동 로그 검토

* 노드 유형에 대해 구성된 VM의 수와 모든 VM 또는 일부에서 워크로드가 발생하나요?

* 규모 축소 및 확장 임계값 차이가 충분한가요?

   평균 CPU가 5분 이상 50%보다 클 때 규모를 확장하고 50%보다 작을 때 규모를 축소하도록 설정했다고 가정합니다. 이 설정은 CPU 사용량이 임계값에 가까워질 때 크기 조정 작업이 지속적으로 집합의 크기를 늘리고 줄이기를 반복하면서 “플래핑” 문제가 발생됩니다. 이 설정 때문에 자동 크기 조정 서비스는 크기를 조정하지 않는 것으로 나타날 수 있는 “플래핑”을 방지하려고 합니다. 그러므로 크기 조정 사이에 일부 간격을 허용하도록 규모 확장 및 축소 임계값의 차이가 충분한지 확인합니다.

* 노드 형식을 스케일 인 또는 스케일 아웃할 수 있나요?
   노드 유형 수준에서 노드 수를 조정하고 성공적으로 완료되었는지 확인합니다. [관리되는 클러스터에서 노드 형식의 크기를 조정하는 방법](./how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-a-template)

* Microsoft.ServiceFabric/managedclusters/nodetypes 및 Microsoft를 확인합니다. Azure Resource Explorer 리소스 Insights

   Azure Resource Explorer는 Azure Resource Manager 리소스의 상태를 보여 주는 필수적인 문제 해결 도구입니다. 구독을 클릭하고 문제를 해결하려는 리소스 그룹을 살펴봅니다. 리소스 `ServiceFabric/managedclusters/clustername` 공급자 아래에서 만든 노드 형식을 찾은 `NodeTypes` 다음, 속성의 유효성을 검사하여 `provisioningState` 가 인지 `Succeeded` 확인합니다. 그런 다음, Microsoft로 이동합니다. 아래에 리소스 공급자를 Insights `clustername` 자동 크기 조정 규칙이 올바르게 보이는지 확인합니다. 

* 내측된 메트릭 값이 예상대로 표시되어 있나요?
   `Get-AzMetric` [PowerShell 모듈을 사용하여 리소스의 메트릭 값을 얻고](/powershell/module/az.monitor/get-azmetric) 검토합니다.


이러한 단계를 마친 후에도 자동 크기 조정 문제가 계속 발생하면 [지원 요청 로깅 리소스를](./service-fabric-support.md#create-an-azure-support-request)사용해 볼 수 있습니다. 템플릿 및 성능 데이터 보기를 공유하도록 준비합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Monitor 자동 크기 조정 지원에 대해 읽어보기](../azure-monitor/autoscale/autoscale-overview.md)
> [!div class="nextstepaction"]
> [Azure Monitor 메트릭 검토](../azure-monitor/essentials/data-platform-metrics.md)
> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 구성 옵션](how-to-managed-cluster-configuration.md)

[autoscale-are-tree]: ./media/how-to-managed-cluster-autoscale/autoscale-are-tree.png
[autoscale-nt-details]: ./media/how-to-managed-cluster-autoscale/autoscale-nt-details.png


