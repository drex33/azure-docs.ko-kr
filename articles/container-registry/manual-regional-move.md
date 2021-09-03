---
title: Azure Container Registry를 다른 지역으로 이동
description: Azure Container Registry 설정 및 데이터를 다른 Azure 지역으로 수동으로 이동합니다.
ms.topic: article
ms.date: 06/08/2021
ms.openlocfilehash: 4e0afb418fbb0b33330c3fb82fd04370f0c3ee99
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286321"
---
# <a name="manually-move-a-container-registry-to-another-region"></a>수동으로 컨테이너 레지스트리를 다른 지역으로 이동

한 Azure 지역에서 다른 Azure 지역으로 Azure Container Registry를 이동해야 할 수도 있습니다. 예를 들어 개발 파이프라인을 실행하거나 다른 지역에서 새 배포 대상을 호스팅하고 가까운 레지스트리를 제공하려고 할 수 있습니다.

[Azure Resource Mover](../resource-mover/overview.md)는 현재 Azure Container Registry 이동을 자동화할 수 없지만 Container Registry를 다른 지역으로 수동으로 이동할 수 있습니다.

* Resource Manager 템플릿으로 레지스트리 설정 내보내기
* 템플릿을 사용하여 다른 Azure 지역에 레지스트리 배포
* 원본 레지스트리에서 대상 레지스트리로 레지스트리 콘텐츠 가져오기


[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure CLI

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="considerations"></a>고려 사항

* 이 문서의 단계를 사용하여 동일한 구독의 다른 지역으로 레지스트리를 이동합니다. 레지스트리를 다른 Azure 구독 또는 Active Directory 테넌트로 이동하려면 추가 구성이 필요합니다. 
* Resource Manager 템플릿을 내보내고 사용하면 많은 레지스트리 설정을 다시 만드는 데 도움이 될 수 있습니다. 템플릿을 편집하여 추가 설정을 구성하거나 만든 후 대상 레지스트리를 업데이트할 수 있습니다.

## <a name="export-template-from-source-registry"></a>원본 레지스트리에서 템플릿 내보내기 

Azure Portal, Azure CLI, Azure PowerShell 또는 기타 Azure 도구를 사용하여 Resource Manager 템플릿을 내보냅니다. Azure 포털을 사용하려면

1. [Azure Portal](https://portal.azure.com)에서 원본 레지스트리로 이동합니다.
1. 메뉴의 **자동화** 에서 **템플릿 내보내기** > **다운로드** 를 선택합니다.

    :::image type="content" source="media/manual-regional-move/export-template.png" alt-text="컨테이너 레지스트리용 내보내기 템플릿":::

## <a name="redeploy-target-registry-in-new-region"></a>새 지역에 대상 레지스트리 재배포

### <a name="modify-template"></a>템플릿 수정

다운로드한 템플릿 JSON 파일에서 레지스트리 속성을 검사하고 필요에 따라 변경합니다. 최소한 다음을 수행합니다.

* 레지스트리 이름의 `defaultValue`을 대상 레지스트리의 원하는 이름으로 변경합니다.
* 대상 레지스트리에 대해 원하는 Azure 지역으로 `location` 업데이트

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "registries_myregistry_name": {
            "defaultValue": "myregistry",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('myregistry_name')]",
            "location": "centralus",
[...]
```

자세한 내용은 [Azure Portal에서 내보낸 템플릿 사용](../azure-resource-manager/templates/template-tutorial-export-template.md) 및 [템플릿 참조](/azure/templates/microsoft.containerregistry/registries)를 참조하세요.

> [!IMPORTANT]
> 고객 관리형 키를 사용하여 대상 레지스트리를 암호화하려면 필수 관리 ID, 키 자격 증명 모음 및 키에 대한 설정으로 템플릿을 업데이트해야 합니다. 레지스트리를 배포할 때만 고객 관리형 키를 사용하도록 설정할 수 있습니다.
> 
> 자세한 내용은 [고객 관리형 키를 사용하여 레지스트리 암호화](./container-registry-customer-managed-keys.md#enable-customer-managed-key---template)를 참조하세요.

### <a name="create-resource-group"></a>리소스 그룹 만들기 

[az group create](/cli/azure/group#az_group_create)를 사용하여 대상 레지스트리에 대한 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="deploy-target-registry-in-new-region"></a>새 지역에 대상 레지스트리 배포

[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 명령을 사용하여 템플릿을 사용하여 대상 레지스트리를 배포합니다.

```azurecli
az deployment group --resource-group myResourceGroup \
   --template-file template.json --name mydeployment
```

> [!NOTE]
> 배포 중에 오류가 표시되면 템플릿 파일의 특정 구성을 업데이트하고 명령을 다시 시도해야 할 수 있습니다.

## <a name="import-registry-content-in-target-registry"></a>대상 레지스트리에서 레지스트리 콘텐츠 가져오기

대상 지역에서 레지스트리를 만든 후 [az acr import](/cli/azure/acr#az_acr_import) 명령 또는 이에 상응하는 PowerShell 명령 `Import-AzContainerImage`를 사용하여 원본 레지스트리에서 대상 레지스트리로 보존하려는 이미지 및 기타 아티팩트를 가져옵니다. 명령 예는 [컨테이너 레지스트리로 컨테이너 이미지 가져오기](container-registry-import-images.md)를 참조하세요.

* Azure CLI 명령 [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) 및 [az acr repository show-tags](/cli/azure/acr/repository#az_acr_repository_show_tags) 또는 이에 상응하는 Azure PowerShell을 사용하여 원본 레지스트리의 콘텐츠를 열거할 수 있습니다.
* 개별 아티팩트에 대해 가져오기 명령을 실행하거나 아티팩트 목록에 대해 실행하도록 스크립트를 작성합니다.

다음 샘플 Azure CLI 스크립트는 원본 리포지토리 및 태그를 열거한 다음 대상 레지스트리로 아티팩트를 가져옵니다. 특정 리포지토리 또는 태그를 가져오려면 필요에 따라 수정합니다.

```azurecli
#!/bin/bash
# Modify registry names for your environment
SOURCE_REG=myregistry
TARGET_REG=targetregistry

# Get list of source repositories
REPO_LIST=$(az acr repository list \
    --name $SOURCE_REG --output tsv)

# Enumerate tags and import to target registry
for repo in $REPO_LIST; do
    TAGS_LIST=$(az acr repository show-tags --name $SOURCE_REG --repository $repo --output tsv);
    for tag in $TAGS_LIST; do
        echo "Importing $repo:$tag";
        az acr import --name $TARGET_REG --source $SOURCE_REG.azurecr.io/$repo":"$tag;
    done
done
```

## <a name="verify-target-registry"></a>대상 레지스트리 확인

대상 레지스트리에서 다음 정보를 확인합니다.

* 레지스트리 이름, 서비스 계층, 공용 액세스 및 복제와 같은 레지스트리 설정
* 보존하려는 콘텐츠의 리포지토리 및 태그.


### <a name="additional-configuration"></a>추가 구성

* 필요한 경우 프라이빗 엔드포인트, IP 액세스 규칙 및 관리 ID와 같은 대상 레지스트리의 설정을 수동으로 구성합니다.

* 원본 레지스트리 대신 대상 레지스트리를 사용하도록 개발 및 배포 시스템을 업데이트합니다.

* 대상 레지스트리에 대한 액세스를 허용하도록 클라이언트 방화벽 규칙을 업데이트합니다.

## <a name="delete-original-registry"></a>원본 레지스트리 삭제

대상 레지스트리를 성공적으로 배포하고, 콘텐츠를 마이그레이션하고, 레지스트리 설정을 확인한 후 원본 레지스트리를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 공용 레지스트리 또는 다른 프라이빗 레지스트리에서 Azure Container Registry로 [컨테이너 이미지를 가져오는 방법](container-registry-import-images.md)을 알아봅니다. 
* Azure Container Registry에 대한 [Resource Manager 템플릿 참조](/azure/templates/microsoft.containerregistry/registries)를 확인하세요.