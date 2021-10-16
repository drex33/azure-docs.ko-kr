---
title: Azure Cosmos 계정에 대한 Azure Private Link 구성
description: 가상 네트워크에서 개인 IP 주소를 사용하여 Azure Cosmos 계정에 액세스하도록 Azure Private Link를 설정하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/08/2021
ms.author: thweiss
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 78f112b7c1de9709aab661525bf22a3325c462cc
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069238"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Azure Cosmos 계정에 대한 Azure Private Link 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure Cosmos 계정에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내부에 있는 서브넷의 개인 IP 주소 세트입니다. 그런 다음, 개인 IP 주소를 통해 Azure Cosmos 계정에 대한 액세스를 제한할 수 있습니다. Private Link를 제한된 NSG 정책과 결합하면 데이터 반출 위험을 줄일 수 있습니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure Private Link](../private-link/private-link-overview.md) 문서를 참조하세요.

> [!NOTE]
> Private Link는 Azure Cosmos 엔드포인트가 퍼블릭 DNS를 통해 확인되는 것을 방지하지 않습니다. 들어오는 요청 필터링은 전송이나 네트워크 수준이 아닌 애플리케이션 수준에서 발생합니다.

Private Link를 사용하면 사용자가 가상 네트워크 내부에서 또는 피어링된 가상 네트워크에서 Azure Cosmos 계정에 액세스할 수 있습니다. Private Link에 매핑된 리소스 역시 프라이빗 피어링을 사용하여 VPN 또는 Azure ExpressRoute를 통해 온-프레미스에서 액세스할 수 있습니다.

자동 또는 수동 승인 방법을 사용하여 Private Link를 통해 구성된 Azure Cosmos 계정에 연결할 수 있습니다. 자세한 내용은 Private Link 설명서의 [승인 워크플로](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) 섹션을 참조하세요.

이 문서에서는 Azure Cosmos DB 트랜잭션 저장소의 프라이빗 엔드포인트를 설정하는 방법을 설명합니다. 자동 승인 방법을 사용하는 것으로 가정합니다. 분석 저장소를 사용하는 경우 [분석 저장소의 프라이빗 엔드포인트](analytical-store-private-endpoints.md) 문서를 참조하세요.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Azure Portal을 사용하여 프라이빗 엔드포인트 만들기

다음 단계에 따라 Azure Portal을 사용하여 기존 Azure Cosmos 계정에 대한 프라이빗 엔드포인트를 만듭니다.

1. **모든 리소스** 창에서 Azure Cosmos 계정을 선택합니다.

1. 다음과 같이 설정 목록에서 **프라이빗 엔드포인트 연결** 을 선택한 다음, **프라이빗 엔드포인트** 를 선택합니다.

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Azure Portal에서 프라이빗 엔드포인트를 만들기 위해 선택하는 항목":::

1. **프라이빗 엔드포인트 만들기 - 기본** 창에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | 리소스 그룹을 선택합니다.|
    | **인스턴스 세부 정보** |  |
    | 속성 | 프라이빗 엔드포인트의 이름을 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    |지역| Private Link를 배포하려는 지역을 선택합니다. 가상 네트워크가 있는 위치와 동일한 위치에 프라이빗 엔드포인트를 만듭니다.|
    |||
1. 완료되면 **다음: 리소스** 를 선택합니다.
1. **프라이빗 엔드포인트 만들기 - 리소스** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | **내 디렉터리의 Azure 리소스에 연결** 을 선택합니다. <br/><br/> 그런 다음, 리소스 중 하나를 선택하여 Private Link를 설정할 수 있습니다. 또는 다른 사람이 나와 공유하는 리소스 ID 또는 별칭을 사용하여 그 사람의 리소스에 연결할 수도 있습니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.AzureCosmosDB/databaseAccounts** 를 선택합니다. |
    | 리소스 |Azure Cosmos 계정을 선택합니다. |
    |대상 하위 리소스 |매핑할 Azure Cosmos DB API 유형을 선택합니다. 기본적으로 SQL, MongoDB 및 Cassandra API에 대해 하나만 선택해야 합니다. Gremlin 및 Table API는 SQL API와 상호 운용이 가능하므로 **Sql** 을 선택해도 됩니다. |
    |||

1. 완료되면 **다음: 구성** 을 선택합니다.
1. **프라이빗 엔드포인트 만들기 - 구성** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| 가상 네트워크를 선택합니다. |
    | 서브넷 | 서브넷을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예** 를 선택합니다. <br><br/> 프라이빗 엔드포인트에 비공개로 연결하려면 DNS 레코드가 필요합니다. 프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합하는 것이 좋습니다. 자체 DNS 서버를 활용하거나 가상 머신의 호스트 파일을 사용하여 DNS 레코드를 만들 수도 있습니다. <br><br/> 이 옵션에 대해 예를 선택하면 프라이빗 DNS 영역 그룹도 생성됩니다. DNS 영역 그룹은 프라이빗 DNS 영역과 프라이빗 엔드포인트 간의 링크입니다. 이 링크를 사용하면 프라이빗 엔드포인트에 대한 업데이트가 있을 때 프라이빗 DNS 영역을 자동으로 업데이트할 수 있습니다. 예를 들어 지역을 추가하거나 제거하는 경우 프라이빗 DNS 영역이 자동으로 업데이트됩니다. |
    |프라이빗 DNS 영역 |**privatelink.documents.azure.com** 을 선택합니다. <br><br/> 프라이빗 DNS 영역은 자동으로 결정됩니다. Azure Portal을 사용하여 변경할 수 없습니다.|
    |||

1. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지에서 Azure가 구성의 유효성을 검사합니다.
1. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

Azure Cosmos 계정에 대한 Private Link를 승인하면 Azure Portal에서 **방화벽 및 가상 네트워크** 창에서 **모든 네트워크** 옵션을 사용할 수 없습니다.

## <a name="api-types-and-private-zone-names"></a><a id="private-zone-name-mapping"></a>API 형식과 프라이빗 영역 이름

다음 표는 여러 Azure Cosmos 계정 API 유형, 지원되는 하위 리소스 및 해당하는 프라이빗 영역 이름 간의 매핑을 보여줍니다. SQL API를 통해 Gremlin 및 Table API 계정에 액세스할 수도 있으므로, 이러한 API에 대한 두 가지 항목이 있습니다.

|Azure Cosmos 계정 API 유형  |지원되는 하위 리소스(또는 그룹 ID) |프라이빗 영역 이름  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|테이블    |    테이블     |   privatelink.table.cosmos.azure.com    |
|테이블     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>개인 IP 주소 가져오기

프라이빗 엔드포인트가 프로비저닝된 후에는 IP 주소를 쿼리할 수 있습니다. Azure Portal에서 IP 주소를 보는 방법은 다음과 같습니다.

1. **모든 리소스** 를 선택합니다.
1. 이전에 만든 프라이빗 엔드포인트를 검색합니다. 이 문서에서는 **cdbPrivateEndpoint3** 입니다.
1. **개요** 탭을 선택하여 DNS 설정 및 IP 주소를 확인합니다.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Azure Portal의 개인 IP 주소":::

프라이빗 엔드포인트마다 다음과 같은 여러 IP 주소가 생성됩니다.

* Azure Cosmos 계정의 글로벌(지역에 관계 없음) 엔드포인트에 대한 IP 주소
* Azure Cosmos 계정이 배포되는 각 지역의 IP 주소

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기

다음 PowerShell 스크립트를 실행하여 기존 Azure Cosmos 계정에 대한 "MyPrivateEndpoint"라는 프라이빗 엔드포인트를 만듭니다. 변수 값을 환경에 맞는 정보로 바꿉니다.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location $Location -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합

프라이빗 엔드포인트를 만든 후에는 다음 PowerShell 스크립트를 사용하여 프라이빗 DNS 영역과 통합할 수 있습니다.

```azurepowershell-interactive
Import-Module Az.PrivateDns

# Zone name differs based on the API type and group ID you are using. 
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"

# Create DNS configuration

$PrivateDnsZoneId = $zone.ResourceId

$config = New-AzPrivateDnsZoneConfig -Name $zoneName `
 -PrivateDnsZoneId $PrivateDnsZoneId

## Create a DNS zone group
New-AzPrivateDnsZoneGroup -ResourceGroupName $ResourceGroupName `
 -PrivateEndpointName $PrivateEndpointName `
 -Name "MyPrivateZoneGroup" `
 -PrivateDnsZoneConfig $config
```

### <a name="fetch-the-private-ip-addresses"></a>개인 IP 주소 가져오기

프라이빗 엔드포인트가 프로비저닝된 후에는 다음 PowerShell 스크립트를 사용하여 IP 주소와 FQDN 매핑을 쿼리할 수 있습니다.

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Azure CLI를 사용하여 프라이빗 엔드포인트 만들기

다음 Azure CLI 스크립트를 실행하여 기존 Azure Cosmos 계정에 대한 "myPrivateEndpoint"라는 프라이빗 엔드포인트를 만듭니다. 변수 값을 환경에 맞는 정보로 바꿉니다.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합

프라이빗 엔드포인트를 만든 후에는 다음 Azure CLI 스크립트를 사용하여 프라이빗 DNS 영역과 통합할 수 있습니다.

```azurecli-interactive
#Zone name differs based on the API type and group ID you are using. 
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Create a DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group $ResourceGroupName \
   --endpoint-name $PrivateEndpointName \
   --name "MyPrivateZoneGroup" \
   --private-dns-zone $zoneName \
   --zone-name "myzone"
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 프라이빗 엔드포인트 만들기

가상 네트워크 서브넷에 프라이빗 엔드포인트를 만들어 Private Link를 설정할 수 있습니다. 이 작업에는 Azure Resource Manager 템플릿을 사용합니다.

다음 코드를 사용하여 "PrivateEndpoint_template.json"이라는 Resource Manager 템플릿을 만듭니다. 이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos SQL API 계정에 대한 프라이빗 엔드포인트를 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**템플릿의 매개 변수 파일 정의**

템플릿의 매개 변수 파일을 만들고, 이름을 "PrivateEndpoint_parameters.json"으로 지정합니다. 매개 변수 파일에 다음 코드를 추가합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**PowerShell 스크립트를 사용하여 템플릿 배포**

다음 코드를 사용하여 PowerShell 스크립트를 만듭니다. 스크립트를 실행하기 전에 구독 ID, 리소스 그룹 이름 및 기타 변수 값을 환경에 맞는 정보로 바꿉니다.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

PowerShell 스크립트의 `GroupId` 변수는 하나의 값만 포함할 수 있습니다. 이 값은 계정의 API 유형입니다. 허용되는 값은 `Sql`, `MongoDB`, `Cassandra`, `Gremlin` 및 `Table`입니다. 일부 Azure Cosmos 계정 유형은 여러 API를 통해 액세스할 수 있습니다. 다음은 그 예입니다.

* Gremlin API 계정은 Gremlin 계정과 SQL API 계정에서 모두 액세스할 수 있습니다.
* Table API 계정은 Table 계정과 SQL API 계정에서 모두 액세스할 수 있습니다.

이러한 계정은 API 형식마다 프라이빗 엔드포인트를 하나씩 만들어야 합니다. 해당 API 형식은 `GroupId` 배열에서 지정합니다.

템플릿이 성공적으로 배포되면 다음 이미지와 비슷한 출력을 볼 수 있습니다. 프라이빗 엔드포인트가 올바르게 설정되면 `provisioningState` 값이 `Succeeded`입니다.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Resource Manager 템플릿의 배포 출력":::

템플릿이 배포된 후에는 서브넷 내에서 개인 IP 주소가 예약됩니다. Azure Cosmos 계정의 방화벽 규칙은 프라이빗 엔드포인트의 연결만 허용하도록 구성됩니다.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합

다음 코드를 사용하여 "PrivateZone_template.json"이라는 Resource Manager 템플릿을 만듭니다. 이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos SQL API 계정에 대한 프라이빗 DNS 영역을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

**템플릿의 매개 변수 파일 정의**

템플릿에 대한 다음 두 매개 변수를 만듭니다. "PrivateZone_parameters.json"을 만듭니다. 다음 코드와 바꿉니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

다음 코드를 사용하여 “PrivateZoneGroup_template.json”이라는 Resource Manager 템플릿을 만듭니다. 이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos SQL API 계정에 대한 프라이빗 DNS 영역 그룹을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "PrivateEndpointDnsGroupName": {
            "value": "string"
        },
        "privateEndpointName":{
            "value": "string"
        }        
    },
    "resources": [
        {
            "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
            "apiVersion": "2020-06-01",
            "name": "[parameters('PrivateEndpointDnsGroupName')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]",
                "[variables('privateEndpointName')]"
            ],
          "properties": {
            "privateDnsZoneConfigs": [
              {
                "name": "config1",
                "properties": {
                  "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
                }
              }
            ]
          }
        }
    ]
}
```

**템플릿의 매개 변수 파일 정의**

템플릿에 대한 다음 두 매개 변수를 만듭니다. “PrivateZoneGroup_parameters.json”을 만듭니다. 다음 코드와 바꿉니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "PrivateEndpointDnsGroupName": {
            "value": ""
        },
        "privateEndpointName":{
            "value": ""
        }
    }
}
```

**PowerShell 스크립트를 사용하여 템플릿 배포**

다음 코드를 사용하여 PowerShell 스크립트를 만듭니다. 스크립트를 실행하기 전에 구독 ID, 리소스 그룹 이름 및 기타 변수 값을 환경에 맞는 정보로 바꿉니다.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

# Name of the DNS zone group to create
$PrivateEndpointDnsGroupName = "myPrivateDNSZoneGroup"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"
$PrivateZoneGroupTemplateFilePath = "PrivateZoneGroup_template.json"
$PrivateZoneGroupParametersFilePath = "PrivateZoneGroup_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneGroupDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneGroupTemplateFilePath `
    -TemplateParameterFile $PrivateZoneGroupParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -PrivateEndpointName $PrivateEndpointName`
    -PrivateEndpointDnsGroupName $PrivateEndpointDnsGroupName

```

## <a name="configure-custom-dns"></a>사용자 지정 DNS 구성

프라이빗 엔드포인트를 만든 서브넷 내부의 프라이빗 DNS 영역을 사용해야 합니다. 각 개인 IP 주소가 DNS 항목에 매핑되도록 엔드포인트를 구성합니다. 앞에 표시된 응답의 `fqdns` 속성을 참조하세요.

프라이빗 엔드포인트를 만들 때 Azure의 프라이빗 DNS 영역과 통합할 수 있습니다. 사용자 지정 DNS 영역을 대신 사용하기로 선택하는 경우 프라이빗 엔드포인트에 대해 예약된 모든 개인 IP 주소의 DNS 레코드를 추가하도록 구성해야 합니다.

> [!IMPORTANT]
> 해당 요청이 프라이빗 엔드포인트를 통과하는지 아니면 표준 퍼블릭 경로를 사용하는지 결정하는 요청의 DNS 확인입니다. 로컬 DNS가 프라이빗 엔드포인트에서 매핑된 개인 IP 주소를 올바르게 참조하는지 확인합니다.

## <a name="private-link-combined-with-firewall-rules"></a>방화벽 규칙과 결합된 Private Link

Private Link를 방화벽 규칙과 함께 사용하면 다음과 같은 상황 및 결과가 발생할 수 있습니다.

* 방화벽 규칙을 구성하지 않으면 기본적으로 모든 트래픽이 Azure Cosmos 계정에 액세스할 수 있습니다.

* 공용 트래픽 또는 서비스 엔드포인트를 구성하고 프라이빗 엔드포인트를 만들면 해당 유형의 방화벽 규칙이 다양한 유형의 수신 트래픽 유형에 권한을 부여합니다. 프라이빗 엔드포인트가 서비스 엔드포인트도 구성된 서브넷에 구성된 경우:
  * 프라이빗 엔드포인트로 매핑된 데이터베이스 계정에 대한 트래픽은 프라이빗 엔드포인트를 통해 라우팅됩니다.
  * 서브넷에서 다른 데이터베이스 계정으로의 트래픽은 서비스 엔드포인트를 통해 라우팅됩니다.

* 공용 트래픽 또는 서비스 엔드포인트를 구성하지 않고 프라이빗 엔드포인트를 만들면 프라이빗 엔드포인트를 통해서만 Azure Cosmos 계정에 액세스할 수 있습니다. 퍼블릭 트래픽이나 서비스 엔드포인트를 구성하지 않으면 승인된 모든 프라이빗 엔드포인트가 거부되거나 삭제된 후 계정이 전체 네트워크에 대해 열립니다. 단, PublicNetworkAccess가 Disabled로 설정된 경우는 제외입니다.

## <a name="blocking-public-network-access-during-account-creation"></a>계정을 만드는 동안 공용 네트워크 액세스 차단

이전 섹션에서 설명했듯이, 방화벽 규칙을 설정하지 않고 프라이빗 엔드포인트를 추가하면 프라이빗 엔드포인트를 통해서만 Azure Cosmos 계정에 액세스할 수 있습니다. Azure Cosmos 계정이 만들어진 후 프라이빗 엔드포인트가 추가되기 전까지는 공용 트래픽이 Azure Cosmos 계정에 도달할 수 있다는 뜻입니다. 프라이빗 엔드포인트를 만들기 전에도 공용 네트워크 액세스를 사용하지 않도록 설정하려면 계정을 만들 때 `publicNetworkAccess` 플래그를 `Disabled`으로 설정하면 됩니다. 이 플래그는 모든 IP 또는 가상 네트워크 규칙보다 우선하며, 원본 IP나 가상 네트워크가 방화벽 구성에서 허용되는 경우에도 플래그가 `Disabled`로 설정되면 모든 퍼블릭 및 가상 네트워크 트래픽이 차단됩니다.

이 플래그를 사용하는 방법을 보여주는 예제는 [이 Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/cosmosdb-private-endpoint/)을 참조하세요.

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>가동 중지 시간 없이 기존 Cosmos 계정에 프라이빗 엔드포인트 추가

기본적으로 기존 계정에 프라이빗 엔드포인트를 추가하면 약 5분의 짧은 가동 중지 시간이 발생합니다. 이 가동 중지 시간을 방지하려면 아래 지침을 따르세요.

1. 클라이언트 연결을 명시적으로 허용하려면 방화벽 구성에 IP나 가상 네트워크 규칙을 추가합니다.
1. 10분 동안 기다린 다음, 구성 업데이트가 적용되었는지 확인합니다.
1. 새 프라이빗 엔드포인트를 구성합니다.
1. 1단계에서 설정된 방화벽 규칙을 제거합니다.

## <a name="port-range-when-using-direct-mode"></a>직접 모드를 사용할 때의 포트 범위

직접 모드 연결을 통해 Azure Cosmos 계정으로 Private Link를 사용할 때 전체 범위의 TCP 포트(0 - 65535)가 열려 있는지 확인해야 합니다.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>지역을 추가하거나 제거할 때 프라이빗 엔드포인트 업데이트

예를 들어 Azure Cosmos 계정을 “미국 서부”, “미국 중부”, “서유럽”에 배포하는 경우입니다. 계정의 프라이빗 엔드포인트를 만들면 서브넷에서 4개의 개인 IP가 예약됩니다. 각 지역의 IP가 하나씩 있고, 나머지 하나는 글로벌/지역에 구애받지 않는 엔드포인트의 IP입니다. 나중에 Azure Cosmos 계정에 새 지역(예: "미국 동부")을 추가할 수 있습니다. 프라이빗 DNS 영역은 다음과 같이 업데이트됩니다.

* **프라이빗 DNS 영역 그룹을 사용하는 경우:**

  프라이빗 DNS 영역 그룹을 사용하는 경우 프라이빗 엔드포인트가 업데이트되면 프라이빗 DNS 영역이 자동으로 업데이트됩니다. 이전 예제에서는 새 지역을 추가한 다음, 프라이빗 DNS 영역이 자동으로 업데이트됩니다.

* **프라이빗 DNS 영역 그룹을 사용하지 않는 경우:**

  프라이빗 DNS 영역 그룹을 사용하지 않는 경우 Azure Cosmos 계정에 지역을 추가하거나 제거하려면 해당 계정의 DNS 항목을 추가하거나 제거해야 합니다. 지역을 추가 또는 제거한 후에는 추가 또는 제거된 DNS 항목과 해당 개인 IP 주소를 반영하도록 서브넷의 프라이빗 DNS 영역을 업데이트할 수 있습니다.

  이전 예제에서 새 지역을 추가한 후에는 해당 DNS 레코드를 프라이빗 DNS 영역 또는 사용자 지정 DNS에 추가해야 합니다. 지역을 제거할 때에도 동일한 단계를 사용하면 됩니다. 지역을 제거한 후에는 프라이빗 DNS 영역 또는 사용자 지정 DNS에서 해당 DNS 레코드를 제거해야 합니다.

## <a name="current-limitations"></a>현재 제한 사항

Azure Cosmos 계정에 Private Link를 사용하면 다음 제한이 적용됩니다.

* 단일 Azure Cosmos 계정의 프라이빗 엔드포인트는 200개가 넘을 수 없습니다.

* 직접 모드 연결을 통해 Azure Cosmos 계정에 Private Link를 사용하면 TCP 프로토콜만 사용할 수 있습니다. HTTP 프로토콜은 현재 지원되지 않습니다.

* Azure Cosmos DB의 API for MongoDB 계정을 사용하면 서버 버전 3.6 이상의 계정(즉, `*.mongo.cosmos.azure.com` 형식의 엔드포인트를 사용하는 계정)에만 프라이빗 엔드포인트가 지원됩니다. 서버 버전 3.2의 계정(즉, `*.documents.azure.com` 형식의 엔드포인트를 사용하는 계정)에는 Private Link가 지원되지 않습니다. Private Link를 사용하려면 이전 계정을 새 버전으로 마이그레이션해야 합니다.

* Private Link가 있는 MongoDB 계정용 Azure Cosmos DB의 API를 사용하는 경우 도구/라이브러리는 SNI(서비스 이름 식별)를 지원하거나 연결 문자열에서 `appName` 매개 변수를 전달하여 제대로 연결해야 합니다. 일부 이전 도구/라이브러리는 Private Link 기능을 사용하도록 호환되지 않을 수 있습니다.

* 승인된 프라이빗 엔드포인트를 자동으로 만들려면 Azure Cosmos 계정 범위에서 네트워크 관리자에게 적어도 `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` 권한을 부여해야 합니다.

### <a name="limitations-to-private-dns-zone-integration"></a>프라이빗 DNS 영역 통합에 대한 제한

프라이빗 DNS 영역 그룹을 사용하는 경우가 아니면, 프라이빗 엔드포인트를 삭제하거나 Azure Cosmos 계정에서 지역을 제거해도 프라이빗 DNS 영역의 DNS 레코드는 자동으로 제거되지 않습니다. 다음 작업을 수행하기 전에 DNS 레코드를 수동으로 제거해야 합니다.

* 이 프라이빗 DNS 영역에 연결된 새 프라이빗 엔드포인트를 추가합니다.
* 프라이빗 엔드포인트가 이 프라이빗 DNS 영역에 연결된 데이터베이스 계정에 새 영역을 추가합니다.

DNS 레코드를 정리하지 않으면 예기치 않은 데이터 평면 문제가 발생할 수 있습니다. 이러한 문제에는 프라이빗 엔드포인트 또는 지역을 제거한 후 추가된 지역의 데이터 중단이 포함됩니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB 보안 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

* Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원](how-to-configure-firewall.md)을 참조하세요.

* Azure Cosmos 계정에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 방법에 대한 자세한 내용은 [가상 네트워크 액세스 구성](how-to-configure-vnet-service-endpoint.md)을 참조하세요.

* Private Link에 대한 자세한 내용은 [Azure Private Link](../private-link/private-link-overview.md) 설명서를 참조하세요.
