---
title: '빠른 시작: Azure NetApp Files 및 NFS 볼륨 설정'
description: 빠른 시작 - 신속하게 Azure NetApp Files를 설정하고 볼륨을 만드는 방법을 설명합니다.
author: b-hchen
ms.author: b-hchen
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 10/04/2021
ms.custom: devx-track-azurecli, subject-armqs, mode-other
ms.openlocfilehash: 54aef74122b84cb2e6fdedf85ee0289c9099f4b2
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487742"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>빠른 시작: Azure NetApp Files 설정 및 NFS 볼륨 만들기

이 문서에서는 신속하게 Azure NetApp Files를 설정하고 NFS 볼륨을 만드는 방법을 보여줍니다. 

이 빠른 시작에서는 다음 항목을 설정합니다.

- NetApp 리소스 공급자 등록
- NetApp 계정
- 용량 풀
- Azure NetApp Files에 대한 NFS 볼륨

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

NFS 볼륨 및 관련 고려 사항에 대해 활성화할 수 있는 모든 기능을 확인하려면 [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)를 참조하세요. 

## <a name="register-for-netapp-resource-provider"></a>NetApp 리소스 공급자 등록

> [!NOTE]
> 등록 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.
>

# <a name="portal"></a>[포털](#tab/azure-portal)

포털을 사용하는 등록 단계의 경우 위에서 설명한 대로 Cloud Shell 세션을 열고 다음 Azure CLI 단계를 수행합니다.

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 방법 문서에는 Azure PowerShell 모듈 Az 버전 2.6.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 현재 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 원하는 경우 PowerShell 세션에서 Cloud Shell 콘솔을 대신 사용할 수 있습니다.

1. PowerShell 명령 프롬프트(또는 PowerShell Cloud Shell 세션)에서 Azure NetApp Files에 대해 승인된 구독을 지정합니다.
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. 다음과 같이 Azure 리소스 공급자를 등록합니다.
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[템플릿](#tab/template)

없음

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 [NetApp 리소스 공급자에 등록](azure-netapp-files-register.md)합니다.

---

## <a name="create-a-netapp-account"></a>NetApp 계정 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal의 검색 상자에 **Azure NetApp Files** 를 입력한 다음, 나타나는 목록에서 **Azure NetApp Files** 를 선택합니다.

      ![Azure NetApp Files 선택](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. **+ 만들기** 를 클릭하여 새 NetApp 계정을 만듭니다.

3. [새 NetApp 계정] 창에서 다음 정보를 입력합니다.
   1. 계정 이름으로 **myaccount1** 을 입력합니다.
   2. 구독을 선택합니다.
   3. **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다. 리소스 그룹 이름으로 **myRG1** 을 입력합니다. **확인** 을 클릭합니다.
   4. 계정 위치를 선택합니다.

      ![새 NetApp 계정 창](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![리소스 그룹 창](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. **만들기** 를 클릭하여 새 NetApp 계정을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 나머지 예제에서 참조할 수 있도록 몇 가지 변수를 정의합니다.

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > 지원되는 지역 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)을 참조하세요.
    > 명령줄 도구에서 지원되는 지역 이름을 가져오려면 `Get-AzLocation | select Location`을 사용하세요.
    >

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 새 리소스 그룹을 만듭니다.

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) 명령을 사용하여 Azure NetApp Files 계정을 만듭니다.

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 나머지 예제에서 참조할 수 있도록 몇 가지 변수를 정의합니다.

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > 지원되는 지역 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)을 참조하세요.
    > 명령줄 도구에서 지원되는 지역 이름을 가져오려면 `az account list-locations --query "[].{Region:name}" --out table`을 사용하세요.
    >

2. [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 새 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. [az netappfiles account create](/cli/azure/netappfiles/account#az_netappfiles_account_create) 명령을 사용하여 Azure NetApp Files 계정을 만듭니다.

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[템플릿](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

다음 코드 조각에서는 [Microsoft.NetApp/netAppAccounts](/azure/templates/microsoft.netapp/netappaccounts) 리소스를 사용하여 ARM 템플릿(Azure Resource Manager 템플릿)에서 NetApp 계정을 만드는 방법을 보여 줍니다. 코드를 실행하려면 GitHub 리포지토리에서 [전체 ARM 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json)을 다운로드합니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="create-a-capacity-pool"></a>용량 풀 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure NetApp Files 관리 블레이드에서 NetApp 계정(**myaccount1**)을 선택합니다.

    ![NetApp 계정 선택](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. NetApp 계정의 Azure NetApp Files 관리 블레이드에서 **용량 풀** 을 클릭합니다.

    ![용량 풀 클릭](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. **+ 풀 추가** 를 클릭합니다.

    ![풀 추가 클릭](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. 다음과 같은 용량 풀 정보를 입력합니다.
    * 풀 이름으로 **mypool1** 을 입력합니다.
    * 서비스 수준으로 **프리미엄** 을 선택합니다.
    * 풀 크기로 **4(TiB)** 를 지정합니다.
    * **자동** QoS 유형을 사용합니다.

5. **만들기** 를 클릭합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 나중에 참조할 일부 새 변수 정의

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)을 사용하여 새 용량 풀을 만듭니다.

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 나중에 참조할 일부 새 변수 정의

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. [az netappfiles pool create](/cli/azure/netappfiles/pool#az_netappfiles_pool_create)를 사용하여 새 용량 풀을 만듭니다.

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[템플릿](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

다음 코드 조각에서는 [Microsoft.NetApp/netAppAccounts/capacityPools](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) 리소스를 사용하여 ARM 템플릿(Azure Resource Manager 템플릿)에서 용량 풀을 만드는 방법을 보여 줍니다. 코드를 실행하려면 GitHub 리포지토리에서 [전체 ARM 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json)을 다운로드합니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFS 볼륨 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

1. NetApp 계정의 Azure NetApp Files 관리 블레이드에서 **볼륨** 을 클릭합니다.

    ![볼륨 클릭](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. **+ 볼륨 추가** 를 클릭합니다.

    ![볼륨 추가 클릭](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. 볼륨 만들기 창에서 다음과 같은 볼륨 정보를 입력합니다.
   1. 볼륨 이름으로 **myvol1** 을 입력합니다.
   2. 용량 풀을 선택합니다(**mypool1**).
   3. 할당량은 기본값을 사용합니다.
   4. 가상 네트워크 아래에서 **새로 만들기** 를 클릭하여 새 Azure 가상 네트워크(Vnet)를 만듭니다.  다음 정보를 입력합니다.
       * Vnet 이름으로 **myvnet1** 을 입력합니다.
       * 설정의 주소 공간을 지정합니다(예: 10.7.0.0/16).
       * 서브넷 이름으로 **myANFsubnet** 을 입력합니다.
       * 서브넷 주소 범위를 지정합니다(예: 10.7.0.0/24). 전용 서브넷은 다른 리소스와 공유할 수 없습니다.
       * 서브넷 위임으로 **Microsoft.NetApp/volumes** 를 선택합니다.
       * **확인** 을 클릭하여 Vnet을 만듭니다.
   5. 서브넷에서, 새로 만든 Vnet(**myvnet1**)을 대리자 서브넷으로 선택합니다.

      ![볼륨 만들기 창](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![가상 네트워크 만들기 창](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. **프로토콜** 을 클릭한 후, 다음 작업을 완료합니다.
    * **NFS** 를 볼륨에 대한 프로토콜 유형으로 선택합니다.
    * 볼륨의 내보내기 경로를 만드는 데 사용할 파일 경로로 **myfilepath1** 을 입력합니다.
    * 볼륨의 NFS 버전(**NFSv3** 또는 **NFSv4.1**)을 선택합니다.
      NFS 버전에 대한 [고려 사항](azure-netapp-files-create-volumes.md#considerations) 및 [모범 사례](azure-netapp-files-create-volumes.md#best-practice)를 참조하세요.

    ![빠른 시작에 대한 NFS 프로토콜 지정](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. **검토 + 만들기** 를 클릭하여 만드는 볼륨에 대한 정보를 표시합니다.  

6. **만들기** 를 클릭하여 볼륨을 만듭니다. 
    만든 볼륨은 볼륨 블레이드에 표시됩니다.

    ![만든 볼륨](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [New-AzDelegation](/powershell/module/az.network/new-azdelegation) 명령을 사용하여 "Microsoft.NetApp/volumes"에 대한 서브넷 위임을 만듭니다.

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 명령을 사용하여 서브넷 구성을 만듭니다.

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 명령을 사용하여 가상 네트워크를 만듭니다.

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) 명령을 사용하여 볼륨을 만듭니다.

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 나중에 사용하기 위해 몇 가지 변수를 정의합니다.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 명령을 사용하여 서브넷이 없는 가상 네트워크를 만듭니다.

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 명령을 사용하여 위임된 서브넷을 만듭니다.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. [az netappfiles volume create](/cli/azure/netappfiles/volume#az_netappfiles_volume_create) 명령을 사용하여 볼륨을 만듭니다.

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a>[템플릿](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

다음 코드 조각에서는 VNet을 설정하고 ARM 템플릿(Azure Resource Manager 템플릿)에 Azure NetApp Files 볼륨을 만드는 방법을 보여 줍니다. VNet 설치 프로그램은 [Microsoft.Network/virtualNetworks](/azure/templates/Microsoft.Network/virtualNetworks) 리소스를 사용합니다. 볼륨 생성은 [Microsoft.NetApp/netAppAccounts/capacityPools/volumes](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) 리소스를 사용합니다. 코드를 실행하려면 GitHub 리포지토리에서 [전체 ARM 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json)을 다운로드합니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>리소스 정리

# <a name="portal"></a>[포털](#tab/azure-portal)

완료되면 원하는 경우 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다.

> [!IMPORTANT]
> 리소스 그룹 내의 모든 리소스가 영구적으로 삭제되고 취소할 수 없습니다.

1. Azure Portal의 검색 상자에 **Azure NetApp Files** 를 입력한 다음, 나타나는 목록에서 **Azure NetApp Files** 를 선택합니다.

2. 구독 목록에서 삭제하려는 리소스 그룹(myRG1)을 클릭합니다.

    ![리소스 그룹으로 이동](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. 리소스 그룹 페이지에서 **리소스 그룹 삭제** 를 클릭합니다.

    ![리소스 그룹 삭제 단추를 강조 표시하는 스크린샷](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    창이 하나 열리고 리소스 그룹과 함께 삭제될 리소스에 대한 경고가 표시됩니다.

4. 리소스 그룹의 이름(myRG1)을 입력하고 리소스 그룹 및 포함된 모든 리소스를 영구적으로 삭제할 것인지 확인한 다음, **삭제** 를 클릭합니다.

    ![리소스 그룹 삭제 확인](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

완료되면 원하는 경우 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다.

> [!IMPORTANT]
> 리소스 그룹 내의 모든 리소스가 영구적으로 삭제되고 취소할 수 없습니다.

1. [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹을 삭제합니다.

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

완료되면 원하는 경우 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다.

> [!IMPORTANT]
> 리소스 그룹 내의 모든 리소스가 영구적으로 삭제되고 취소할 수 없습니다.

1. [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹을 삭제합니다.

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[템플릿](#tab/template)

없음

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 리소스 그룹을 삭제합니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure NetApp Files의 스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)

> [!div class="nextstepaction"]
> [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)
