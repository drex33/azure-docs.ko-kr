---
title: Azure PowerShell을 사용하여 Azure 퍼블릭 IP 구성을 다른 Azure 지역으로 이동
description: Azure PowerShell을 사용하여 Azure 퍼블릭 IP 구성을 한 Azure 지역에서 다른 지역으로 이동하려면 Azure Resource Manager 템플릿을 사용합니다.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f6be8adf08000fdd23568481b592cde36f9bb248
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257749"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 퍼블릭 IP 구성을 다른 지역으로 이동

기존 Azure 퍼블릭 IP 구성을 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성 및 SKU를 사용하여 퍼블릭 IP를 만들 수 있습니다. 재해 복구 계획의 일부로 퍼블릭 IP 구성을 다른 지역으로 이동할 수도 있습니다.

**Azure 퍼블릭 IP는 지역별로 지정되며, 한 지역에서 다른 지역으로 이동할 수 없습니다.** 그러나 Azure Resource Manager 템플릿을 사용하여 퍼블릭 IP의 기존 구성을 내보낼 수 있습니다.  그런 다음, 퍼블릭 IP를 템플릿으로 내보내고, 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 준비할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

- 이동하려는 Azure 지역에 Azure 퍼블릭 IP가 있는지 확인합니다.

- Azure 퍼블릭 IP는 지역 간에 이동할 수 없습니다.  새 퍼블릭 IP를 대상 지역의 리소스에 연결해야 합니다.

- 퍼블릭 IP 구성을 내보내고 템플릿을 배포하여 다른 지역에 퍼블릭 IP를 만들려면 네트워크 기여자 역할 이상이 필요합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 디바이스, NSG(네트워크 보안 그룹) 및 가상 네트워크를 포함하되 국한되지 않습니다.

- Azure 구독에서 사용되는 대상 Azure 지역에 퍼블릭 IP를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 퍼블릭 IP 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 Resource Manager 템플릿을 사용하여 구성 이동을 위해 퍼블릭 IP를 준비하고 Azure PowerShell을 사용하여 퍼블릭 IP 구성을 대상 지역으로 이동하는 방법을 보여 줍니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>템플릿 내보내기 및 스크립트에서 배포

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 지역으로 이동하려는 퍼블릭 IP의 리소스 ID를 가져온 후 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) 명령을 실행하는 디렉터리의 .json 파일로 원본 가상 네트워크를 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드한 파일 이름으로 리소스를 내보낸 리소스 그룹의 이름이 사용됩니다.  명령에서 내보낸 **\<resource-group-name>.json** 파일을 찾은 후 원하는 편집기에서 엽니다.
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

         기본 및 표준 sku 퍼블릭 IP 간의 차이점에 대한 자세한 내용은 [퍼블릭 IP 주소 만들기, 변경 또는 삭제](./ip-services/virtual-network-public-ip-address.md)를 참조하세요.

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

        할당 방법 및 유휴 시간 제한 값에 대한 자세한 내용은 [퍼블릭 IP 주소 만들기, 변경 또는 삭제](./ip-services/virtual-network-public-ip-address.md)를 참조하세요.


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
## <a name="discard"></a>취소 

배포 후 대상에서 퍼블릭 IP를 시작하거나 삭제하려는 경우 대상에서 만든 리소스 그룹을 삭제하면 이동한 퍼블릭 IP가 삭제됩니다.  리소스 그룹을 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크의 이동을 완료하려면 원본 가상 네트워크 또는 리소스 그룹을 삭제하고, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 또는 [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress)를 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 퍼블릭 IP를 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)