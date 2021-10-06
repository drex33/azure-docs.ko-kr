---
title: Azure Resource Manager 템플릿으로 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure Resource Manager 템플릿을 사용하여 새 Azure Machine Learning 작업 영역을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 04/21/2021
ms.openlocfilehash: c0220070d6b377212099a67c4328bc080afc1648
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544624"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning에 대한 작업 영역을 만듭니다.

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning 작업 영역을 만드는 몇 가지 방법을 설명합니다. Resource Manager 템플릿을 사용하면 조정된 단일 작업으로 리소스를 쉽게 만들 수 있습니다. 템플릿은 배포에 필요한 리소스를 정의하는 JSON 문서입니다. 배포 매개 변수도 지정할 수 있습니다. 매개 변수는 템플릿 사용 시 입력 값을 제공하는 데 사용됩니다.

자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning의 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* CLI에서 템플릿을 사용하려면 [Azure PowerShell](/powershell/azure/) 또는 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>작업 영역 Resource Manager 템플릿

이 문서 전체에서 사용되는 Azure Resource Manager 템플릿은 Azure 빠른 시작 템플릿 GitHub 리포지토리의 [microsoft.machineleaerningservices/machine-learning-workspace-vnet](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-workspace-vnet/azuredeploy.json) 디렉터리에서 찾을 수 있습니다.

이 템플릿은 다음과 같은 Azure 서비스를 만듭니다.

* Azure Storage 계정
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning 작업 영역

리소스 그룹은 서비스를 보관하는 컨테이너입니다. Azure Machine Learning 작업 영역에는 다양한 서비스가 필요합니다.

예제 템플릿에는 다음과 같은 두 개의 **필수** 매개 변수가 있습니다.

* 리소스가 생성될 **위치** 입니다.

    선택한 위치는 템플릿에서 대부분의 리소스에 사용됩니다. 단, 다른 서비스를 사용할 수 있는 일부 위치에서 사용할 수 없는 Application Insights 서비스는 예외입니다. 서비스를 사용할 수 없는 위치를 선택하면 미국 중남부 위치에 서비스가 생성됩니다.

* **workspaceName** - Azure Machine Learning 작업 영역의 식별 이름입니다.

    > [!NOTE]
    > 작업 영역 이름은 대/소문자를 구분하지 않습니다.

    다른 서비스의 이름은 임의로 생성됩니다.

> [!TIP]
> 이 문서와 연결된 템플릿은 새 Azure Container Registry를 만들지만 컨테이너 레지스트리를 만들지 않고도 새 작업 영역을 만들 수도 있습니다. 컨테이너 레지스트리가 필요한 작업을 수행할 때 하나의 작업 영역이 만들어집니다. 예를 들어 모델을 학습하거나 배포합니다.
>
> 새로 만들지 않고 Azure Resource Manager 템플릿에 있는 기존 컨테이너 레지스트리 또는 스토리지 계정을 참조할 수도 있습니다. 이 작업을 수행하는 경우 [관리 ID](how-to-use-managed-identities.md)(미리 보기)를 사용하거나 컨테이너 레지스트리에 대해 [관리자 계정을 사용하도록 설정](../container-registry/container-registry-authentication.md#admin-account)해야 합니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

템플릿에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/templates/syntax.md)
* [Azure Resource Manager 템플릿으로 애플리케이션 배포](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices 리소스 종류](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>템플릿 배포

템플릿을 배포하려면 리소스 그룹을 만들어야 합니다.

그래픽 사용자 인터페이스를 사용하려는 경우 [Azure Portal](#use-the-azure-portal) 섹션을 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

리소스 그룹이 생성되면 다음 명령을 사용하여 템플릿을 배포합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

기본적으로 템플릿의 일부로 생성된 리소스는 새로운 리소스입니다. 그러나 기존 리소스를 사용하는 옵션도 있습니다. 템플릿에 추가 매개 변수를 제공하여 기존 리소스를 사용할 수 있습니다. 예를 들어 기존 스토리지 계정을 사용하려는 경우 **storageAccountOption** 값을 **기존** 값으로 설정하고 **storageAccountName** 매개 변수에 스토리지 계정의 이름을 입력합니다.

> [!IMPORTANT]
> 기존 Azure Storage 계정을 사용하려는 경우 프리미엄 계정(Premium_LRS 및 Premium_GRS)으로 사용할 수 없습니다. 또한 계층 구조 네임스페이스(Azure Data Lake Storage Gen2와 함께 사용)를 가질 수 없습니다. Premium Storage 또는 계층 구조 네임스페이스는 작업 영역의 기본 스토리지 계정에서 지원되지 않습니다. Premium Storage 또는 계층 구조 네임스페이스는 작업 영역의 ‘기본’ 스토리지 계정에서 지원되지 않습니다. ‘기본이 아닌’ 스토리지 계정으로 Premium Storage 또는 계층 구조 네임스페이스를 사용할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>암호화된 작업 영역 배포

다음 템플릿 예에서는 세 가지 설정으로 작업 영역을 만드는 방법을 보여 줍니다.

* 작업 영역에 대해 높은 기밀성을 사용하도록 설정합니다. 그러면 새 Cosmos DB 인스턴스가 만들어집니다.
* 작업 영역에 대해 암호화를 사용하도록 설정합니다.
* 기존 Azure Key Vault를 사용하여 고객 관리형 키를 검색합니다. 고객 관리형 키를 사용하여 작업 영역에 대한 새 Cosmos DB 인스턴스를 만듭니다.

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> 작업 영역을 만든 후에는 기밀 데이터, 암호화, 키 자격 증명 모음 ID 또는 키 식별자에 대한 설정을 변경할 수 없습니다. 이러한 값을 변경하려면 새 값을 사용하여 새 작업 영역을 만들어야 합니다.

자세한 내용은 [미사용 암호화](concept-data-encryption.md#encryption-at-rest)를 참조하세요.

> [!IMPORTANT]
> 이 템플릿을 사용하기 전에 구독에서 충족해야 하는 특정 요구 사항이 있습니다.
> * 암호화 키를 포함하는 기존 Azure Key Vault가 있어야 합니다.
> * Azure Key Vault는 Azure Machine Learning 작업 영역을 만들 위치와 동일한 영역에 있어야 합니다.
> * Azure Key Vault ID와 암호화 키의 URI를 지정해야 합니다.

이 템플릿에 필요한 `cmk_keyvault`(Key Vault ID) 및 `resource_cmk_uri`(키 URI) 매개 변수에 대한 __값을 가져오려면__ 다음 단계를 사용합니다.    

1. Key Vault ID를 가져오려면 다음 명령을 사용합니다.  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    이 명령은 `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>`와 비슷한 값을 반환합니다.  

1. 고객 관리형 키에 대한 URI 값을 가져오려면 다음 명령을 사용합니다.    

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    이 명령은 `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`와 비슷한 값을 반환합니다. 

> [!IMPORTANT]  
> 작업 영역을 만든 후에는 기밀 데이터, 암호화, 키 자격 증명 모음 ID 또는 키 식별자에 대한 설정을 변경할 수 없습니다. 이러한 값을 변경하려면 새 값을 사용하여 새 작업 영역을 만들어야 합니다.

고객 관리형 키를 사용하도록 설정하려면 템플릿을 배포할 때 다음 매개 변수를 설정합니다.

* **encryption_status** 를 **사용하도록 설정** 합니다.
* **cmk_keyvault** 를 이전 단계에서 얻은 `cmk_keyvault` 값으로 설정합니다.
* **resource_cmk_uri** 를 이전 단계에서 얻은 `resource_cmk_uri` 값으로 설정합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

고객 관리형 키를 사용하는 경우 Azure Machine Learning은 Cosmos DB 인스턴스를 포함하는 보조 리소스 그룹을 만듭니다. 자세한 내용은 [미사용 암호화 - Cosmos DB](concept-data-encryption.md#encryption-at-rest)를 참조하세요.

**confidential_data** 매개 변수를 **true** 로 설정하여 데이터에 추가 구성을 제공합니다. 이 작업을 통해 다음을 수행합니다.

* 구독에서 이전 클러스터를 만들지 않은 경우 Azure Machine Learning 컴퓨팅 클러스터에 대한 로컬 스크래치 디스크 암호화를 시작합니다. 이전에 구독에서 클러스터를 만든 경우 지원 티켓을 열어 컴퓨팅 클러스터에 대해 스크래치 디스크를 암호화하도록 설정합니다.
* 실행 작업 사이에 로컬 스크래치 디스크를 정리합니다.
* 키 자격 증명 모음을 사용하여 스토리지 계정, 컨테이너 레지스트리 및 SSH 계정의 자격 증명을 실행 레이어에서 컴퓨팅 클러스터로 안전하게 전달합니다.
* IP 필터링을 사용하도록 설정하여 AzureMachineLearningService 이외의 외부 서비스에서 기본 배치 풀을 호출할 수 없도록 합니다.

    > [!IMPORTANT]
    > 작업 영역을 만든 후에는 기밀 데이터, 암호화, 키 자격 증명 모음 ID 또는 키 식별자에 대한 설정을 변경할 수 없습니다. 이러한 값을 변경하려면 새 값을 사용하여 새 작업 영역을 만들어야 합니다.

  자세한 내용은 [미사용 암호화](concept-data-encryption.md#encryption-at-rest)를 참조하세요.

## <a name="deploy-workspace-behind-a-virtual-network"></a>가상 네트워크 뒤에 작업 영역 배포

`vnetOption`매개 변수 값을 `new` 또는 `existing`으로 설정하면 가상 네트워크 뒤의 작업 영역에서 사용하는 리소스를 만들 수 있습니다.

> [!IMPORTANT]
> 컨테이너 레지스트리의 경우 ‘프리미엄’ SKU만 지원됩니다.

> [!IMPORTANT]
> Application Insights는 가상 네트워크 뒤의 배포를 지원하지 않습니다.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>프라이빗 엔드포인트 뒤에 작업 영역만 배포

연결된 리소스가 가상 네트워크 뒤에 있지 않은 경우 **privateEndpointType** 매개 변수를 `AutoAproval` 또는 `ManualApproval`로 설정하여 작업 영역을 프라이빗 엔드포인트 뒤에 배포할 수 있습니다. 새 작업 영역과 기존 작업 영역 모두에 대해 이 작업을 수행할 수 있습니다. 기존 작업 영역을 업데이트할 때 기존 작업 영역의 정보를 사용하여 템플릿 매개 변수를 입력합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>새 가상 네트워크 사용

새 가상 네트워크 뒤에 리소스를 배포하려면 해당 리소스에 대한 가상 네트워크 설정과 함께 **vnetOption** 을 **new** 로 설정합니다. 아래 배포에서는 새 가상 네트워크 뒤에 스토리지 계정 리소스를 사용하여 작업 영역을 배포하는 방법을 보여 줍니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

또는 가상 네트워크 뒤에 다수 또는 모든 종속 리소스를 배포할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>기존 가상 네트워크 및 리소스 사용

기존에 연결된 리소스가 있는 작업 영역을 배포하려면 서브넷 매개 변수와 함께 **vnetOption** 매개 변수를 **기존** 으로 설정해야 합니다. 그러나 배포 **전에** 각 리소스에 대해 가상 네트워크에서 서비스 엔드포인트를 만들어야 합니다. 새 가상 네트워크 배포와 마찬가지로 가상 네트워크 뒤에 리소스를 하나 또는 모두 사용할 수 있습니다.

> [!IMPORTANT]
> 서브넷에는 `Microsoft.Storage` 서비스 엔드포인트가 있어야 합니다.

> [!IMPORTANT]
> 서브넷에서는 프라이빗 엔드포인트를 만들 수 없습니다. 서브넷을 사용하도록 설정하려면 프라이빗 엔드포인트를 사용하지 않도록 설정합니다.

1. 리소스에 대해 서비스 엔드포인트를 사용하도록 설정합니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. 작업 영역 배포

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)의 단계를 수행합니다. __템플릿 선택__ 화면이 나타나면 **빠른 시작** 항목을 선택합니다. 표시되면 "템플릿 리포지토리를 열려면 여기를 클릭하세요."라고 레이블이 지정된 링크를 선택합니다. 이 링크를 통해 Azure 빠른 시작 템플릿 리포지토리의 `quickstarts` 디렉터리로 이동합니다.
1. 빠른 시작 템플릿 목록에서 `microsoft.machinelearningservices`를 선택합니다. 마지막으로 `Deploy to Azure`를 선택합니다.
1. 템플릿이 표시되면 배포 시나리오에 따라 다음과 같은 필수 정보 및 기타 매개 변수를 입력합니다.

   * 구독: 이러한 리소스에 사용할 Azure 구독을 선택합니다.
   * 리소스 그룹: 서비스를 포함할 리소스 그룹을 선택하거나 만듭니다.
   * 지역: 리소스가 생성될 Azure 지역을 선택합니다.
   * 작업 영역 이름: 생성될 Azure Machine Learning 작업 영역에 사용할 이름입니다. 작업 영역 이름은 3자에서 33자 사이여야 합니다. 영숫자와 ‘-’만 포함할 수 있습니다.
   * 위치: 리소스가 생성될 위치를 선택합니다.
1. __검토 + 만들기__ 를 선택합니다.
1. __검토 + 만들기__ 화면에서 나열된 사용 약관에 동의하고 __만들기__ 를 선택합니다.

자세한 내용은 [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 액세스 정책 및 Azure Resource Manager 템플릿

Azure Resource Manager 템플릿을 사용하여 작업 영역 및 연결된 리소스(Azure Key Vault 포함)를 만드는 경우 여러 번 사용합니다. 예를 들어 연속 통합 및 배포 파이프라인의 일부와 동일한 매개 변수와 함께 템플릿을 여러 번 사용합니다.

템플릿을 통한 대부분의 리소스 생성 작업은 여러 번 사용해도 결과가 달라지지 않지만 Key Vault는 템플릿이 사용될 때마다 액세스 정책을 지웁니다. 액세스 정책을 지우면 사용 중인 기존 작업 영역에 대한 Key Vault 액세스가 중단됩니다. 예를 들어 Azure Notebooks VM의 중지/만들기 기능이 실패할 수 있습니다.  

이 문제를 방지하려면 다음 방법 중 하나를 사용하는 것이 좋습니다.

* 동일한 매개 변수에 템플릿을 두 번 이상 배포하지 마세요. 다시 배포하려면 기존 리소스를 삭제한 후에 템플릿을 다시 만들어 사용해야 합니다.

* Key Vault 액세스 정책을 검토한 후 이러한 정책을 사용하여 템플릿의 `accessPolicies` 속성을 설정합니다. 액세스 정책을 보려면 다음 Azure CLI 명령을 사용합니다.

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    템플릿의 `accessPolicies` 섹션 사용에 대한 자세한 내용은 [AccessPolicyEntry 개체 참조](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)를 참조하세요.

* Key Vault 리소스가 이미 있는지 확인합니다. 이 경우 템플릿을 통해 다시 만들지 마세요. 예를 들어 새로 만드는 대신 기존 Key Vault를 사용하려면 템플릿을 다음과 같이 변경합니다.

    * 기존 Key Vault 리소스의 ID를 허용하는 매개 변수를 **추가** 합니다.

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * Key Vault 리소스를 만드는 섹션을 **제거** 합니다.

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * 작업 영역의 `dependsOn` 섹션에서 `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 줄을 **제거** 합니다. 또한 `keyVaultId` 매개 변수를 참조하여 작업 영역의 `properties` 섹션에서 `keyVault` 항목을 **변경** 합니다.

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    이러한 변경 후 템플릿을 실행할 때 기존 Key Vault 리소스의 ID를 지정할 수 있습니다. 그런 다음 템플릿에서 작업 영역의 `keyVault` 속성을 해당 ID로 설정하여 Key Vault를 다시 사용합니다.

    Key Vault의 ID를 가져오기 위해 원래 템플릿 실행의 출력을 참조하거나 Azure CLI를 사용할 수 있습니다. 다음 명령은 Azure CLI를 사용하여 Key Vault 리소스 ID를 가져오는 예입니다.

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>다음 단계

* [Resource Manager 템플릿 및 Resource Manager REST API를 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-rest.md)
* [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
* [Azure Machine Learning과 관련된 다른 템플릿은 Azure 빠른 시작 템플릿 리포지토리를 참조하세요.](https://github.com/Azure/azure-quickstart-templates)
