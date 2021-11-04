---
title: Terraform을 사용 하 여 작업 영역 관리
titleSuffix: Azure Machine Learning
description: terraform을 사용 하 여 Azure Machine Learning 작업 영역을 관리 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: deeikele
author: denniseik
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ''
ms.openlocfilehash: 21e3f646f384695fa6a305eb45f7e3bb019a18e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560818"
---
# <a name="manage-azure-machine-learning-workspaces-using-terraform-preview"></a>terraform을 사용 하 여 Azure Machine Learning 작업 영역 관리 (미리 보기)

이 문서에서는 terraform 구성 파일을 사용 하 여 Azure Machine Learning 작업 영역을 만들고 관리 하는 방법에 대해 알아봅니다. [Terraform](/azure/developer/terraform/)의 템플릿 기반 구성 파일을 사용 하 여 반복 가능 하 고 예측 가능한 방식으로 Azure 리소스를 정의 하 고 만들고 구성할 수 있습니다. Terraform은 리소스 상태를 추적 하 고 리소스를 정리 및 제거할 수 있습니다. 

Terraform 구성은 배포에 필요한 리소스를 정의 하는 문서입니다. 배포 변수도 지정할 수 있습니다. 변수는 구성을 사용할 때 입력 값을 제공 하는 데 사용 됩니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. 없는 경우 [무료 또는 유료 버전의 Azure Machine Learning](https://azure.microsoft.com/free/)을 사용해 보세요.
* 설치 된 [Azure CLI](/cli/azure/)버전입니다.
* Terraform 구성:이 문서 및 [Azure에 대 한 액세스 권한 구성](/azure/developer/terraform/get-started-cloud-shell) 문서의 지침을 따르세요.

## <a name="declare-the-azure-provider"></a>Azure 공급자 선언

Azure 공급자를 선언 하는 Terraform 구성 파일을 만듭니다.

1. 이름이 `main.tf`인 새 파일을 만듭니다. Azure Cloud Shell 작업 하는 경우 bash를 사용 합니다.

    ```bash
    code main.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    **main.tf**:
    :::code language="terraform" source="~/terraform/quickstart/101-machine-learning/main.tf":::

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="deploy-a-workspace"></a>작업 영역 배포

다음 terraform 구성을 사용 하 여 Azure Machine Learning 작업 영역을 만들 수 있습니다. Azure Machine Learning 작업 영역을 만들 때 다른 여러 서비스가 종속성으로 필요 합니다. 템플릿에는 이러한 [연결 된 리소스도 작업 영역에](./concept-workspace.md#resources)지정 됩니다. 필요에 따라 공용 또는 개인 네트워크 연결을 사용 하 여 리소스를 만드는 템플릿을 사용 하도록 선택할 수 있습니다.

# <a name="public-network-connectivity"></a>[공용 네트워크 연결](#tab/publicworkspace)

Azure의 일부 리소스에는 전역적으로 고유한 이름이 필요 합니다. 다음 템플릿을 사용 하 여 리소스를 배포 하기 전에 `name` 변수를 고유한 값으로 설정 합니다.

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/workspace.tf":::

# <a name="private-network-connectivity"></a>[프라이빗 네트워크 연결](#tab/privateworkspace)

아래 구성에서는 Azure 개인 링크 끝점을 사용 하 여 격리 된 네트워크 환경에 작업 영역을 만듭니다. 가상 네트워크 내에서 도메인 이름을 확인할 수 있도록 [사설 DNS 영역이](../dns/private-dns-privatednszone.md) 포함 됩니다.

Azure의 일부 리소스에는 전역적으로 고유한 이름이 필요 합니다. 다음 템플릿을 사용 하 여 리소스를 배포 하기 전에 `resourceprefix` 변수를 고유한 값으로 설정 합니다.

Azure Container Registry 및 Azure Machine Learning에 대 한 개인 링크 끝점을 사용 하는 경우 [환경](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) 이미지를 빌드하기 위해 Azure Container Registry 작업을 사용할 수 없습니다. 대신 Azure Machine Learning 계산 클러스터를 사용 하 여 이미지를 빌드할 수 있습니다. 사용의 클러스터 이름을 구성 하려면 [image_build_compute_name](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 인수를 설정 합니다. [Public_network_access_enabled](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 인수를 사용 하 여 개인 링크 끝점이 있는 작업 영역에 대 한 [공용 액세스를 허용](./how-to-configure-private-link.md?tabs=python#enable-public-access) 하도록를 구성할 수 있습니다.

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/workspace.tf":::

**network.tf**:
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

개인 링크 끝점 작업 영역에 연결 하는 몇 가지 옵션이 있습니다. 이러한 옵션에 대해 자세히 알아보려면 [작업 영역에 안전](./how-to-secure-workspace-vnet.md#securely-connect-to-your-workspace)하 게 연결을 참조 하세요.

---

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="current-limitations"></a>현재 제한 사항

* 현재는 고객이 관리 하는 암호화 키를 사용 하 여 작업 영역을 배포 하는 데 Terraform을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* Azure의 Terraform 지원에 대 한 자세한 내용은 [azure의 terraform 설명서](/azure/developer/terraform/)를 참조 하세요.
* Terraform에 대 한 "빠른 시작" 템플릿 예제를 찾으려면 [Azure Terraform 빠른 시작 템플릿](https://github.com/Azure/terraform/tree/master/quickstart)을 참조 하세요.
  
  * [101: Machine learning 작업 영역 및 계산](https://github.com/Azure/terraform/tree/master/quickstart/101-machine-learning) – Azure ML를 시작 하는 데 필요한 최소한의 리소스 집합입니다.
  * [201: 기계 학습 작업 영역, 계산 및 네트워크 격리를 위한 네트워크 구성 요소 집합](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) -HBI 데이터와 함께 사용 하기 위한 프로덕션 파일럿 환경을 만드는 데 필요한 모든 리소스입니다.
  * [202:201와 비슷하지만 기존 네트워크 구성 요소를 가져오는 옵션을 사용](https://github.com/Azure/terraform/tree/master/quickstart/202-machine-learning-moderately-secure-existing-VNet)합니다.
  
* Terraform Azure 공급자에 대 한 자세한 내용은 [Terraform 레지스트리 Azure Resource Manager 공급자](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)를 참조 하세요.
* 네트워크 구성 옵션에 대 한 자세한 내용은 [가상 네트워크를 사용 하 여 작업 영역 리소스 보안 Azure Machine Learning (vnet) (영문)](./how-to-network-security-overview.md)을 참조 하세요.
* 템플릿 기반 배포에 대 한 대체 Azure Resource Manager [리소스 관리자 템플릿과 리소스 관리자 REST API를 사용 하 여 리소스 배포](../azure-resource-manager/templates/deploy-rest.md)를 참조 하세요.