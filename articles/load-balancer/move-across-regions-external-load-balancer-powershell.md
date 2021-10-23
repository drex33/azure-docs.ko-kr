---
title: Azure PowerShell을 사용하여 Azure 외부 Load Balancer를 다른 Azure 지역으로 이동
description: Azure PowerShell을 사용하여 Azure 외부 Load Balancer를 한 Azure 지역에서 다른 지역으로 이동하려면 Azure Resource Manager 템플릿을 사용합니다.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 848d3264dbc31d01a767871d1e4783c8d383841d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258699"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 외부 Load Balancer를 다른 지역으로 이동

기존 Azure 외부 부하 분산 장치를 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성의 외부 부하 분산 장치를 만들 수 있습니다. 재해 복구 계획의 일부로 외부 부하 분산 장치를 다른 지역으로 이동할 수도 있습니다.

Azure 외부 부하 분산 장치는 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용하여 외부 부하 분산 장치의 기존 구성과 공용 IP를 내보낼 수 있습니다.  그런 다음, 내부 부하 분산 장치와 공용 IP를 템플릿으로 내보내고, 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 스테이징할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

- 이동하려는 Azure 지역에 Azure 외부 부하 분산 장치가 있는지 확인합니다.

- Azure 외부 부하 분산 장치는 지역 간에 이동할 수 없습니다.  새 부하 분산 장치를 대상 지역의 리소스에 연결해야 합니다.

- 외부 부하 분산 장치를 내보내고 템플릿을 배포하여 다른 지역에 외부 부하 분산 장치를 만들려면 네트워크 기여자 역할 이상이 필요합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 디바이스, 네트워크 보안 그룹, 공용 IP 및 가상 네트워크를 포함하되 국한되지 않습니다.

- Azure 구독에서 사용되는 대상 Azure 지역에 외부 부하 분산 장치를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 부하 분산 장치 추가를 지원할 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 Resource Manager 템플릿을 사용하여 외부 부하 분산 장치 이동을 준비하고 Azure PowerShell을 사용하여 외부 부하 분산 장치 구성을 대상 지역으로 이동하는 방법을 보여 줍니다.  이 프로세스의 일부로, 외부 부하 분산 장치를 이동하기 전에 외부 부하 분산 장치의 공용 IP 구성이 포함되어야 하고 먼저 수행되어야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>공용 IP 템플릿을 내보내고 Azure PowerShell에서 배포

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. 대상 지역으로 이동하려는 퍼블릭 IP의 리소스 ID를 가져온 후 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) 명령을 실행하는 디렉터리의 .json 파일로 원본 공용 IP를 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드한 파일 이름으로 리소스를 내보낸 리소스 그룹의 이름이 사용됩니다.  명령에서 내보낸 **\<resource-group-name>.json** 파일을 찾은 후 원하는 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 퍼블릭 IP 이름의 매개 변수를 편집하려면 원본 퍼블릭 IP 이름의 속성 **defaultValue** 를 대상 퍼블릭 IP의 이름으로 변경하고 이름을 따옴표로 묶었는지 확인합니다.
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. 퍼블릭 IP가 이동될 대상 영역을 편집하려면 리소스에서 **location** 속성을 변경합니다.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. 지역 위치 코드를 가져오려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation)을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 또한 선택하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.

    * **Sku** - **\<resource-group-name>.json** 파일에서 **sku** > **name** 속성을 변경하여 구성의 퍼블릭 IP sku를 표준에서 기본으로 또는 기본에서 표준으로 변경할 수 있습니다.

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         기본 및 표준 sku 퍼블릭 IP 간의 차이점에 대한 자세한 내용은 [퍼블릭 IP 주소 만들기, 변경 또는 삭제](../virtual-network/ip-services/virtual-network-public-ip-address.md)를 참조하세요.

    * **퍼블릭 IP 할당 방법** 및 **유휴 시간 제한** - **publicIPAllocationMethod** 속성을 **Dynamic** 에서 **Static** 으로 또는 **Static** 에서 **Dynamic** 으로 변경하여 템플릿에서 이러한 옵션을 모두 변경할 수 있습니다. **idleTimeoutInMinutes** 속성을 원하는 크기만큼 변경하여 유휴 시간 제한을 변경할 수 있습니다.  기본값은 **4** 입니다.

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        할당 방법 및 유휴 시간 제한 값에 대한 자세한 내용은 [퍼블릭 IP 주소 만들기, 변경 또는 삭제](../virtual-network/ip-services/virtual-network-public-ip-address.md)를 참조하세요.


9. **\<resource-group-name>.json** 파일을 저장합니다.

10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 배포할 대상 퍼블릭 IP에 대한 대상 지역에 리소스 그룹을 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용하여 이전 단계에서 만든 리소스 그룹에 편집된 **\<resource-group-name>.json** 파일을 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 대상 지역에서 리소스를 만들었는지 확인하려면 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) 및 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>외부 부하 분산 장치 템플릿을 내보내고 Azure PowerShell에서 배포

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 지역으로 이동하려는 외부 부하 분산 장치의 리소스 ID를 가져온 후 [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer)를 사용하여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) 명령을 실행하는 디렉터리의 .json 파일로 원본 외부 부하 분산 장치 구성을 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. 다운로드한 파일 이름으로 리소스를 내보낸 리소스 그룹의 이름이 사용됩니다.  명령에서 내보낸 **\<resource-group-name>.json** 파일을 찾은 후 원하는 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 외부 부하 분산 장치 이름의 매개 변수를 편집하려면 원본 외부 부하 분산 장치 이름의 **defaultValue** 속성을 대상 외부 부하 분산 장치 이름으로 변경하고 이름을 따옴표로 묶었는지 확인합니다.

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  위에서 이동한 대상 공용 IP의 값을 편집하려면 먼저 리소스 ID를 얻은 다음 **\<resource-group-name>.json** 파일에 복사하여 붙여넣어야 합니다.  ID를 가져오려면 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용합니다.

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    변수를 입력하고 enter 키를 눌러 리소스 ID를 표시합니다.  ID 경로를 강조 표시하고 클립보드에 복사합니다.

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  **\<resource-group-name>.json** 파일에서 공용 IP 외부 ID의 두 번째 매개 변수에 **defaultValue** 대신 **Resource ID** 를 붙여넣고, 경로를 따옴표로 묶었는지 확인합니다.

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  부하 분산 장치에 대한 아웃바운드 NAT 및 아웃바운드 규칙을 구성한 경우 이 파일에 아웃바운드 공용 IP의 외부 ID에 대한 세 번째 항목이 표시됩니다.  **대상 지역** 에서 위의 단계를 반복하여 아웃바운드 공용 iP에 대한 ID를 가져오고 해당 항목을 **\<resource-group-name>.json** 파일에 붙여넣습니다.

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. 외부 부하 분산 장치 구성이 이동할 대상 지역을 편집하려면 **\<resource-group-name>.json** 파일의 **리소스** 에서 **위치** 속성을 변경합니다.

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. 지역 위치 코드를 가져오려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation)을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 또한 선택하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.
    
    * **Sku** - **\<resource-group-name>.json** 파일에서 **sku** > **이름** 속성을 변경하여 구성의 외부 부하 분산 장치 sku를 표준에서 기본으로 또는 기본에서 표준으로 변경할 수 있습니다.

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      기본 및 표준 sku 부하 분산 장치 간의 차이점에 대한 자세한 내용은 [Azure 표준 Load Balancer 개요](./load-balancer-overview.md) 참조

    * **부하 분산 규칙** - **\<resource-group-name>.json** 파일의 **loadBalancingRules** 섹션에 항목을 추가하거나 제거하여 구성에서 부하 분산 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       부하 분산 규칙에 대한 자세한 내용은 [Azure Load Balancer란?](./load-balancer-overview.md) 페이지를 참조하세요.

    * **프로브** -  **\<resource-group-name>.json** 파일의 **프로브** 섹션에 항목을 추가하거나 제거하여 구성에서 부하 분산 장치에 대한 프로브를 추가하거나 제거할 수 있습니다.

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Azure Load Balancer 상태 프로브에 대한 자세한 내용은 [Load Balancer 상태 프로브](./load-balancer-custom-probe-overview.md) 참조

    * **인바운드 NAT 규칙** - **\<resource-group-name>.json** 파일의 **inboundNatRules** 섹션에 항목을 추가하거나 제거하여 부하 분산 장치에 대한 인바운드 NAT 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        인바운드 NAT 규칙의 추가 또는 제거를 완료하려면  **\<resource-group-name>.json** 파일 끝에 **형식** 속성으로 규칙이 나타나거나 제거되어야 합니다.

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        인바운드 NAT 규칙에 대한 자세한 내용은 [Azure Load Balancer란?](./load-balancer-overview.md) 페이지를 참조하세요.

    * **아웃바운드 규칙** - **\<resource-group-name>.json** 파일에서 **outboundRules** 속성을 편집하여 구성에서 아웃바운드 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         아웃바운드 규칙에 대한 자세한 내용은 [Load Balancer 아웃바운드 규칙](./load-balancer-outbound-connections.md#outboundrules) 참조

13. **\<resource-group-name>.json** 파일을 저장합니다.
    
10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 대상 외부 부하 분산 장치를 배포하기 위해 대상 지역에 리소스 그룹을 만듭니다. 위의 기존 리소스 그룹을 이 프로세스의 일부로 재사용할 수도 있습니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용하여 이전 단계에서 만든 리소스 그룹에 편집된 **\<resource-group-name>.json** 파일을 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 대상 지역에서 리소스를 만들었는지 확인하려면 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) 및 [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer)를 사용합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>취소 

배포 후 대상 지역에서 공용 IP 및 부하 분산 장치를 다시 시작하거나 취소하고 싶은 경우, 대상 지역에 생성된 리소스 그룹을 삭제하면 이동된 공용 IP 및 부하 분산 장치가 삭제됩니다.  리소스 그룹을 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료하려면, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 또는 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 및 [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer)를 사용해 원본 NSG 또는 리소스 그룹을 삭제

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 네트워크 보안 그룹을 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)