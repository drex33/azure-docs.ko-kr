---
title: Azure Stack Edge Pro GPU 디바이스에서 Azure Monitor 사용
description: Azure Stack Edge Pro GPU 디바이스에서 Azure Monitor를 사용하도록 설정하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/03/2021
ms.author: alkohli
ms.openlocfilehash: 86ddddbffd0a909a22acba6f780632690700eda2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715858"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 Azure Monitor 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스에서 컨테이너를 모니터링하는 것은 특히 여러 컴퓨팅 애플리케이션을 실행할 때 중요합니다. Azure Monitor를 사용하면 디바이스에서 실행되는 Kubernetes 클러스터의 컨테이너 로그와 메모리 및 프로세서 메트릭을 수집할 수 있습니다.

이 문서에서는 디바이스에서 Azure Monitor를 사용하도록 설정하고 Log Analytics 작업 영역에서 컨테이너 로그를 수집하는 데 필요한 단계를 설명합니다. Azure Monitor 메트릭 저장소는 현재 Azure Stack Edge Pro GPU 디바이스에서 지원되지 않습니다. 

> [!NOTE]
> 장치에서 Kubernetes 클러스터에 azure arc를 사용 하는 경우 [azure arc 사용 Kubernetes 클러스터에 대 한 컨테이너 Insights Azure Monitor](../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=%2fazure%2fazure-arc%2fkubernetes%2ftoc.json) 의 단계에 따라 컨테이너 모니터링을 설정 합니다.


## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 조건을 충족해야 합니다.

- Azure Stack Edge Pro 디바이스. [자습서: 디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md)의 단계에 따라 디바이스가 활성화되었는지 확인합니다.
- 디바이스에서 [자습서: Azure Stack Edge Pro 디바이스의 컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)에 따라 **컴퓨팅 단계 구성** 단계를 완료했습니다. 디바이스에 연결된 IoT Hub 리소스, IoT 디바이스, IoT Edge 디바이스가 있어야 합니다.


## <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

다음 단계를 수행하여 Log Analytics 작업 영역을 만듭니다. Log Analytics 작업 영역은 로그 데이터를 수집하고 저장하는 논리적 스토리지 단위입니다.

1. Azure Portal에서 **+ 리소스 만들기** 를 선택하고 **Log Analytics 작업 영역** 을 검색한 후 **만들기** 를 선택합니다. 
1. **Log Analytics 만들기 작업 영역** 에서 다음 설정을 구성합니다. 나머지를 기본값으로 적용합니다.

    1. **기본** 탭에서 작업 영역에 대한 구독, 리소스 그룹, 이름 및 지역을 입력합니다. 

        ![Log Analytics 작업 영역에 대한 기본 탭](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. **가격 책정 계층** 탭에서 기본 **종량제 요금제** 를 적용합니다.

        ![Log Analytics 작업 영역에 대한 가격 책정 탭](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. **검토 + 만들기** 탭에서 작업 영역에 대한 정보를 검토하고 **만들기** 를 선택합니다.

        ![Log Analytics 작업 영역에 대한 검토 + 만들기](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

자세한 내용은 [Azure Portal를 통해 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)에 나와 있는 자세한 단계를 참조하세요.



## <a name="enable-container-insights"></a>컨테이너 인사이트 사용

작업 영역에서 컨테이너 인사이트를 사용하려면 다음 단계를 수행합니다. 

1. [Azure Monitor 컨테이너 솔루션을 추가하는 방법](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution)에 나와 있는 자세한 단계를 따르세요. `containerSolution.json` 템플릿 파일을 사용합니다.

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. 리소스 ID 및 위치를 가져옵니다. [https://editor.swagger.io](`Your Log Analytics workspace > General > Properties`) 로 이동합니다. 다음 정보를 복사합니다.

    - **리소스 ID**: Azure Log Analytics 작업 영역의 정규화된 Azure 리소스 ID입니다. 
    - **위치**: Azure 지역입니다.

    ![Log Analytics 작업 영역의 속성](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. `containerSolutionParams.json` 매개 변수 파일을 사용합니다. `workspaceResourceId`를 리소스 ID로 바꾸고, `workspaceRegion`을 이전 단계에서 복사한 위치로 바꿉니다.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    컨테이너 인사이트를 사용하는 Log Analytics 작업 영역의 샘플 출력은 다음과 같습니다.
    
    ```output
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/myaccount> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/myaccount> ls
    clouddrive  containerSolution.json
    PS /home/myaccount> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/myaccount> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/myaccount>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>디바이스에서 Azure Monitor 구성

1. 새로 만든 Log Analytics 리소스로 이동하여 **작업 영역 ID** 및 **기본 키**(작업 영역 키)를 복사합니다.

    ![Log Analytics 작업 영역에서 에이전트 관리](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    이후 단계에서 사용할 예정이므로 이 정보를 저장합니다.

1. [디바이스의 PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. 다음 cmdlet을 이용해 Log Analytics 작업 영역 ID 및 작업 영역 키를 사용합니다.

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Azure Monitor를 사용하면 Log Analytics 작업 영역에 로그가 표시됩니다. 디바이스에 배포된 Kubernetes 클러스터의 상태를 보려면 **Azure Monitor > 인사이트 > 컨테이너** 로 이동합니다. 환경 옵션에서 **모두** 를 선택합니다. 

    ![Log Analytics 작업 영역의 메트릭](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>다음 단계

- [Kubernetes 대시보드를 통해 Kubernetes 워크로드를 모니터링](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)하는 방법을 알아봅니다.
- [디바이스 이벤트 경고 알림을 관리](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)하는 방법을 알아봅니다.
