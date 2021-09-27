---
title: 서비스 엔드포인트 정책 구성 - Azure HDInsight
description: Azure HDInsight를 사용하여 가상 네트워크에 대한 서비스 엔드포인트 정책을 구성하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 09/13/2021
ms.openlocfilehash: 500bbdce924313924b8b6f698dff0e17107ebd79
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646571"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Azure HDInsight에 대한 가상 네트워크 서비스 엔드포인트 정책 구성

이 문서에서는 Azure HDInsight를 사용하여 가상 네트워크에서 서비스 엔드포인트 정책을 구현하는 방법에 대한 정보를 제공합니다.

## <a name="background"></a>배경

Azure HDInsight를 사용하면 사용자 고유의 가상 네트워크에 클러스터를 만들 수 있습니다. 가상 네트워크에서 스토리지 계정 등의 다른 Azure 서비스로 나가는 트래픽을 허용해야 하는 경우 [서비스 엔드포인트 정책](../virtual-network/virtual-network-service-endpoint-policies-overview.md)을 만들 수 있습니다. 그러나 Azure Portal을 통해 만들어진 서비스 엔드포인트 정책을 사용하면 단일 계정, 구독의 모든 계정 또는 리소스 그룹의 모든 계정에 대한 정책만 만들 수 있습니다.

그러나 관리형 서비스인 Azure HDInsight는 각 지역의 특정 스토리지 계정에 있는 각 클러스터에서 데이터 및 로그 파일을 수집합니다. 이 데이터가 가상 네트워크에서 HDInsight에 도달하려면 Azure HDInsight에서 관리되는 특정 데이터 수집 지점으로의 나가는 트래픽을 허용하는 서비스 엔드포인트 정책을 만들어야 합니다.

## <a name="service-endpoint-policies-for-hdinsight"></a>HDInsight에 대한 서비스 엔드포인트 정책

이러한 서비스 엔드포인트 정책은 다음 기능을 지원합니다.

- 클러스터 만들기, 작업 실행 및 플랫폼 작업(예: 스케일링)에 대한 로그 및 원격 분석 수집
- 클러스터에서 소프트웨어 및 라이브러리를 프로비저닝하기 위해 새로 만든 클러스터 노드에 VHD(가상 하드 디스크) 연결

이 데이터 흐름을 사용하도록 설정하기 위해 서비스 엔드포인트 정책을 만들지 않으면 클러스터 만들기가 실패할 수 있으며 Azure HDInsight는 클러스터에 대한 지원을 제공할 수 없습니다.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>HDInsight에 대한 서비스 엔드포인트 정책 만들기

새 클러스터를 만들기 전에 올바른 서비스 엔드포인트 정책이 가상 네트워크에 연결되어 있는지 확인합니다. 그렇지 않으면 클러스터 만들기가 실패하거나 오류가 발생할 수 있습니다.

다음 프로세스를 사용하여 필요한 서비스 엔드포인트 정책을 만듭니다.

1. HDInsight 클러스터를 만들 지역을 결정합니다.
1. HDInsight 관리 스토리지 계정에 대한 모든 리소스 그룹을 제공하는 [서비스 엔드포인트 정책 리소스 목록](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)에서 해당 지역을 조회합니다.
1. 해당 지역의 리소스 그룹 목록을 선택합니다. `Canada Central`에 대한 리소스 예제는 다음과 같습니다.

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
        "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro"
    ],
    ```

1. Azure CLI 또는 Azure PowerShell로 작성된 설치 스크립트에 해당 리소스 그룹 목록을 삽입합니다.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    # Be sure to get the most recent list of resource groups from the [list of service endpoint policy resources](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    PowerShell을 사용하여 서비스 엔드포인트 정책을 설정하려면 다음 코드 조각을 사용합니다.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    # Be sure to get the most recent list of resource groups from the [list of service endpoint policy resources](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```
> [!IMPORTANT] 
> 수동으로 또는 자동화를 통해 [서비스 끝점 정책 리소스의 최신 목록을](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json) 가져오는 것이 좋습니다. 이렇게 하면 추가 리소스 그룹이 JSON 파일에서 추가 되거나 제거 될 때 CRUD 문제가 방지 됩니다. 


## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)
* [네트워크 가상 어플라이언스 구성](./network-virtual-appliance.md)
