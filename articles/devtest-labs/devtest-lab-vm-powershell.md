---
title: Azure PowerShell를 사용 하 여 Azure DevTest Labs에서 가상 컴퓨터 만들기
description: Azure DevTest Labs를 사용하여 Azure PowerShell에서 가상 머신을 만들고 관리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 128962ae54ef5dbbcc9578cb5dbff9d1531336ea
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600592"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Azure PowerShell을 사용하여 DevTest Labs에서 가상 머신 만들기
이 문서에서는 Azure PowerShell을 사용하여 Azure DevTest Labs에서 가상 머신을 만드는 방법을 보여 줍니다. PowerShell 스크립트를 사용하여 Azure DevTest Labs의 랩에서 가상 머신 생성을 자동화할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소
시작하기 전에

- 이 문서의 스크립트나 명령을 테스트할 때 기존 랩을 사용하지 않으려는 경우 [랩을 만듭니다](devtest-lab-create-lab.md). 
- [Azure PowerShell을 설치](/powershell/azure/install-az-ps)하거나 Azure Portal에 통합된 Azure Cloud Shell을 사용합니다. 

## <a name="powershell-script"></a>PowerShell 스크립트
이 섹션의 샘플 스크립트에서는 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) cmdlet을 사용합니다.  이 cmdlet은 랩의 리소스 ID, 수행할 작업의 이름(`createEnvironment`), 해당 작업을 수행하는 데 필요한 매개 변수를 사용합니다. 매개 변수는 모든 가상 머신 설명 속성을 포함하는 해시 테이블에 있습니다. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

위의 스크립트에 있는 가상 머신의 속성을 사용하면 Windows Server 2016 DataCenter를 OS로 사용하여 가상 머신을 만들 수 있습니다. 가상 머신 유형마다 속성은 조금씩 다릅니다. [가상 머신 정의](#define-virtual-machine) 섹션에서는 이 스크립트에서 사용할 속성을 결정하는 방법을 보여 줍니다.

다음 명령은 Create-LabVirtualMachine.ps1 파일 이름에 저장된 스크립트를 실행하는 예제를 제공합니다. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>가상 머신 정의
이 섹션에서는 만들려는 가상 머신의 유형에 해당하는 속성을 가져오는 방법을 보여 줍니다. 

### <a name="use-azure-portal"></a>Azure Portal 사용
Azure Portal에서 VM을 만들 때 Azure Resource Manager 템플릿을 생성할 수 있습니다. VM을 만드는 프로세스를 완료할 필요가 없습니다. 템플릿이 표시될 때까지 단계를 따르면 됩니다. 이는 랩 VM을 아직 만들지 않은 경우 필요한 JSON 설명을 가져오는 가장 좋은 방법입니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택합니다.
3. 서비스 목록에서 **DevTest Labs** 를 검색하고 선택합니다. 
4. **DevTest labs** 페이지의 랩 목록에서 랩을 선택합니다.
5. 랩의 홈페이지에 있는 도구 모음에서 **추가** 를 선택합니다. 
6. VM의 **기본 이미지** 를 선택합니다. 
7. 페이지 아래쪽의 **제출** 단추 위에 있는 **자동화 옵션** 을 선택합니다. 
8. 가상 머신을 만들기 위한 **Azure Resource Manager 템플릿** 이 표시됩니다. 
9. **리소스** 섹션의 JSON 세그먼트에는 이전에 선택한 이미지 형식에 대한 정의가 있습니다. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

이 예제에서는 Azure Market Place 이미지에 대한 정의를 가져오는 방법이 나옵니다. 이와 동일한 방식으로 사용자 지정 이미지, 수식 또는 환경에 대한 정의를 가져올 수 있습니다. 가상 머신에 필요한 아티팩트를 추가하고 필요한 고급 설정을 설정합니다. **자동화 옵션** 단추를 선택하기 전에 필수 필드 및 선택적 필드에 대한 값을 제공한 후입니다.

### <a name="use-azure-rest-api"></a>Azure REST API 사용
다음 절차에서는 REST API를 사용하여 이미지의 속성을 가져오는 단계를 제공합니다. 단계는 랩의 기존 VM에만 해당합니다. 

1. [Virtual Machines - 목록](/rest/api/dtl/virtualmachines/list) 페이지로 이동하여 **시도** 단추를 선택합니다. 
2. Azure **구독** 을 선택합니다.
3. **랩의 리소스 그룹** 을 입력합니다.
4. **랩의 이름** 을 입력합니다. 
5. **실행** 을 선택합니다.
6. VM이 만들어진 **이미지의 속성** 을 볼 수 있습니다. 

## <a name="set-expiration-date"></a>만료 날짜 설정
학습, 데모, 체험 등의 시나리오에서 가상 머신을 만들고 일정한 기간 후에 자동으로 삭제하여 불필요한 비용이 발생하지 않도록 할 수 있습니다. [PowerShell 스크립트](#powershell-script) 섹션 예제에 표시된 대로, PowerShell을 사용하여 VM을 만드는 동안 VM의 만료 날짜를 설정할 수 있습니다.

다음은 랩에서 모든 기존 VM의 만료 날짜를 설정하는 샘플 PowerShell 스크립트입니다.

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>다음 단계
[Azure DevTest Labs용 Azure PowerShell 설명서](/powershell/module/az.devtestlabs/)의 내용을 참조하세요.
