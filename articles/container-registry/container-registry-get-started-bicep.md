---
title: 빠른 시작 - 레지스트리 만들기 - Bicep
description: Bicep 파일을 사용하여 Azure Container Registry를 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: mumian
ms.author: jgao
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom: mode-other
ms.openlocfilehash: 3c50c9c4e06fbd16b0abb01d8f21783c5bb3e57f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133048615"
---
# <a name="quickstart-create-a-container-registry-by-using-a-bicep-file"></a>빠른 시작: Bicep 파일을 사용하여 컨테이너 레지스트리 만들기

이 빠른 시작에서는 Bicep 파일을 사용하여 Azure Container Registry 인스턴스를 만드는 방법을 보여줍니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-bicep-introduction.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="review-the-bicep-file"></a>Bicep 파일 검토

Visual Studio Code 또는 좋아하는 편집기를 사용하여 다음 콘텐츠가 포함된 파일을 만들고 이름을 **main.bicep** 으로 지정합니다.

```bicep
@minLength(5)
@maxLength(50)
@description('Provide a globally unique name of your Azure Container Registry')
param acrName string = 'acr${uniqueString(resourceGroup().id)}'

@description('Provide a location for the registry.')
param location string = resourceGroup().location

@description('Provide a tier of your Azure Container Registry.')
param acrSku string = 'Basic'

resource acrResource 'Microsoft.ContainerRegistry/registries@2021-06-01-preview' = {
  name: acrName
  location: location
  sku: {
    name: acrSku
  }
  properties: {
    adminUserEnabled: false
  }
}

@description('Output the login server property for later use')
output loginServer string = acrResource.properties.loginServer

```

Bicep 파일에 다음 리소스가 정의되어 있습니다.

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : Azure Container Registry 만들기

[빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)에서 더 많은 Azure Container Registry 템플릿 샘플을 찾을 수 있습니다.

## <a name="deploy-the-bicep-file"></a>Bicep 파일 배포

만든 파일을 배포하려면 PowerShell 또는 Azure CLI를 엽니다. 통합된 Visual Studio Code 터미널을 사용하려면 `ctrl` + ```` ` ```` 키 조합을 선택합니다. 현재 디렉터리를 Bicep 파일이 있는 위치로 변경합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name myContainerRegRG --location centralus

az deployment group create --resource-group myContainerRegRG --template-file main.bicep --parameters acrName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name myContainerRegRG -Location centralus

New-AzResourceGroupDeployment -ResourceGroupName myContainerRegRG -TemplateFile ./main.bicep -acrName "{your-unique-name}"
```

---

> [!NOTE]
> 중괄호를 포함하여 **{your-unique-name}** 을 고유한 컨테이너 레지스트리 이름으로 바꿉니다.

배포가 완료되면 배포에 성공했음을 나타내는 메시지가 표시됩니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal 또는 Azure CLI와 같은 도구를 사용하여 컨테이너 레지스트리의 속성을 검토합니다.

1. 포털에서 **Container Registry** 를 검색하고 만든 컨테이너 레지스트리를 선택합니다.

1. **개요** 페이지에서 레지스트리의 **로그인 서버** 를 확인합니다. Docker를 사용하여 이미지에 태그를 지정하고 레지스트리에 푸시할 때 이 URI를 사용합니다. 자세한 내용은 [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)를 참조하세요.

    :::image type="content" source="media/container-registry-get-started-bicep/registry-overview.png" alt-text="레지스트리 개요":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 리소스가 필요하지 않으면 리소스 그룹과 레지스트리를 삭제합니다. 이렇게 하려면 Azure Portal로 이동하고, 레지스트리가 포함된 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

리소스 그룹 삭제

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Bicep 파일을 사용하여 Azure Container Registry를 만들었습니다. Azure Container Registry 자습서를 계속 진행하여 ACR에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry 자습서](container-registry-tutorial-prepare-registry.md)

Bicep 파일 만들기 프로세스를 안내하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md)
