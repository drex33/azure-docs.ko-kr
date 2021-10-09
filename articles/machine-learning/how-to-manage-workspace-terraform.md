---
title: Terraform을 사용하여 작업 영역 관리
titleSuffix: Azure Machine Learning
description: Terraform을 사용하여 Azure Machine Learning 작업 영역을 관리하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: deeikele
author: denniseik
ms.date: 10/08/2021
ms.topic: how-to
ms.custom: ''
ms.openlocfilehash: e273c2f10ddbc21d12be9eb62e069d77a5b5d65e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717681"
---
# <a name="manage-azure-machine-learning-workspaces-using-terraform-preview"></a>Terraform을 사용하여 Azure Machine Learning 작업 영역 관리(미리 보기)

이 문서에서는 Terraform 구성 파일을 사용하여 Azure Machine Learning 작업 영역을 만들고 관리하는 방법을 알아봅니다. [Terraform의](/azure/developer/terraform/)템플릿 기반 구성 파일을 사용하면 반복 가능하고 예측 가능한 방식으로 Azure 리소스를 정의, 생성 및 구성할 수 있습니다. Terraform은 리소스 상태를 추적하고 리소스를 정리하고 삭제할 수 있습니다. 

Terraform 구성은 배포에 필요한 리소스를 정의하는 문서입니다. 배포 변수를 지정할 수도 있습니다. 변수는 구성을 사용할 때 입력 값을 제공하는 데 사용됩니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. 없는 경우 Azure Machine Learning 무료 또는 [유료 버전을](https://azure.microsoft.com/free/)사용해 보세요.
* [Azure CLI](/cli/azure/)설치된 버전입니다.
* Terraform 구성: 이 문서의 지침과 [Terraform 및 Azure에 대한 액세스 구성](/azure/developer/terraform/get-started-cloud-shell) 문서를 따릅니다.

## <a name="declare-the-azure-provider"></a>Azure 공급자 선언

Azure 공급자를 선언하는 Terraform 구성 파일을 만듭니다.

1. 이름이 `main.tf`인 새 파일을 만듭니다. Azure Cloud Shell 사용하는 경우 bash를 사용합니다.

    ```bash
    code main.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    **main.tf:**
    :::code language="terraform" source="~/terraform/quickstart/101-machine-learning/main.tf":::

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="deploy-a-workspace"></a>작업 영역 배포

다음 Terraform 구성을 사용하여 Azure Machine Learning 작업 영역을 만들 수 있습니다. Azure Machine Learning 작업 영역을 만들 때 다양한 다른 서비스가 dependencies로 필요합니다. 또한 템플릿은 작업 영역에 연결된 이러한 [리소스를 지정합니다.](/azure/machine-learning/concept-workspace#resources) 필요에 따라 공용 또는 프라이빗 네트워크 연결로 리소스를 만드는 템플릿을 사용하도록 선택할 수 있습니다.

# <a name="public-network-connectivity"></a>[공용 네트워크 연결](#tab/publicworkspace)

Azure의 일부 리소스에는 전역적으로 고유한 이름이 필요합니다. 다음 템플릿을 사용하여 리소스를 배포하기 전에 `name` 변수를 고유한 값으로 설정합니다.

**variables.tf:**
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/variables.tf":::

**workspace.tf:**
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/workspace.tf":::

# <a name="private-network-connectivity"></a>[프라이빗 네트워크 연결](#tab/privateworkspace)

아래 구성은 Azure Private Link 엔드포인트를 사용하여 격리된 네트워크 환경에 작업 영역을 만듭니다. [프라이빗 DNS 영역은](/azure/dns/private-dns-privatednszone) 가상 네트워크 내에서 도메인 이름을 확인할 수 있도록 포함됩니다.

Azure의 일부 리소스에는 전역적으로 고유한 이름이 필요합니다. 다음 템플릿을 사용하여 리소스를 배포하기 전에 `resourceprefix` 변수를 고유한 값으로 설정합니다.

Azure Container Registry 및 Azure Machine Learning 프라이빗 링크 엔드포인트를 사용하는 경우 Azure Container Registry 작업은 [환경](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) 이미지를 빌드하는 데 사용할 수 없습니다. 대신 Azure Machine Learning 컴퓨팅 클러스터를 사용하여 이미지를 빌드할 수 있습니다. 클러스터 이름 사용을 구성하려면 [image_build_compute_name](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 인수를 설정합니다. [public_network_access_enabled](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 인수를 사용하여 프라이빗 링크 엔드포인트가 있는 작업 영역에 [대한 공용 액세스를 허용하도록](/azure/machine-learning/how-to-configure-private-link?tabs=python#enable-public-access) 를 구성할 수 있습니다.

**variables.tf:**
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/variables.tf":::

**workspace.tf:**
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/workspace.tf":::

**network.tf:**
```terraform
# Virtual Network
resource "azurerm_virtual_network" "default" {
  name                = "vnet-${var.name}-${var.environment}"
  address_space       = var.vnet_address_space
  location            = azurerm_resource_group.default.location
  resource_group_name = azurerm_resource_group.default.name
}

resource "azurerm_subnet" "snet-training" {
  name                                           = "snet-training"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.training_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-aks" {
  name                                           = "snet-aks"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.aks_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-workspace" {
  name                                           = "snet-workspace"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.ml_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

# ...
# For full reference, see: https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/network.tf
```

프라이빗 링크 엔드포인트 작업 영역에 연결하는 몇 가지 옵션이 있습니다. 이러한 옵션에 대한 자세한 내용은 [작업 영역에 안전하게 연결을 참조하세요.](/azure/machine-learning/how-to-secure-workspace-vnet#securely-connect-to-your-workspace)

---

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="current-limitations"></a>현재 제한 사항

* 현재 고객 관리형 암호화 키를 사용하여 작업 영역을 배포하는 데 Terraform을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* Azure의 Terraform 지원에 대한 자세한 내용은 [Azure의 Terraform 설명서를 참조하세요.](/azure/developer/terraform/)
* Terraform에 대한 "빠른 시작" 템플릿 예제를 찾으려면 [Azure Terraform 빠른 시작 템플릿:](https://github.com/Azure/terraform/tree/master/quickstart)
  
  * [101: 기계 학습 작업 영역 및 컴퓨팅](https://github.com/Azure/terraform/tree/master/quickstart/101-machine-learning) – Azure ML 시작하는 데 필요한 최소한의 리소스 집합입니다.
  * [201: 기계 학습 작업 영역, 컴퓨팅 및 네트워크 격리를 위한 네트워크 구성 요소 집합](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) - HBI 데이터와 함께 사용하기 위해 프로덕션 파일럿 환경을 만드는 데 필요한 모든 리소스입니다.
  * [202: 201과 비슷하지만 기존 네트워크 구성 요소를 가져오는 옵션이 있습니다.](https://github.com/Azure/terraform/tree/master/quickstart/202-machine-learning-moderately-secure-existing-VNet).
  
* Terraform Azure 공급자에 대한 자세한 내용은 [Terraform Registry Azure Resource Manager Provider를 참조하세요.](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
* 네트워크 구성 옵션에 대한 자세한 내용은 [VNet(가상 네트워크)을 사용하여 Azure Machine Learning 작업 영역 리소스 보안을 참조하세요.](/azure/machine-learning/how-to-network-security-overview)
* 대체 Azure Resource Manager 템플릿 기반 배포는 Resource Manager [템플릿을 통해 리소스 배포 및 Resource Manager REST API 참조하세요.](../azure-resource-manager/templates/deploy-rest.md)
