---
title: 고가용성을 위한 영역 중복 레지스트리
description: Azure Container Registry에서 영역 중복을 사용하도록 설정하는 방법에 대해 알아봅니다. Azure 가용성 영역에 컨테이너 레지스트리 또는 복제를 만듭니다. 영역 중복은 프리미엄 서비스 계층의 기능입니다.
ms.topic: article
ms.date: 09/13/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ca475f61c3164f54682a22f459e1f8f768073f1f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586820"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>복원력 및 고가용성을 위해 Azure Container Registry에서 영역 중복 사용

Azure Container Registry는 하나 이상의 Azure 지역에서 레지스트리 데이터를 복제하여 가용성을 제공하고 지역 운영의 대기 시간을 줄이는 [지역에서 복제](container-registry-geo-replication.md) 외에도 선택적 ‘영역 중복’을 지원합니다. [영역 중복](../availability-zones/az-overview.md#availability-zones)은 특정 지역의 레지스트리 또는 복제 리소스(복제본)에 복원력 및 고가용성을 제공합니다.

이 문서에서는 Azure CLI, Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 영역 중복 컨테이너 레지스트리 또는 복제본을 설정하는 방법을 보여 줍니다. 

영역 중복은 프리미엄 컨테이너 레지스트리 서비스 계층의 **미리 보기** 기능입니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 현재 다음 지역에서 지원됩니다. 
  
    |아메리카  |유럽  |아프리카  |아시아 태평양  |
    |---------|---------|---------|---------|
    |브라질 남부<br/>캐나다 중부<br/>미국 중부<br/>미국 동부<br/>미국 동부 2<br/>미국 중남부<br/>미국 버지니아 주 정부<br/>미국 서부 2<br/>미국 서부 3     |프랑스 중부<br/>독일 중서부<br/>북유럽<br/>노르웨이 동부<br/>서유럽<br/>영국 남부      |남아프리카 북부<br/>        |오스트레일리아 동부<br/>인도 중부<br/>일본 동부<br/>한국 중부<br/>  |

* 가용성 영역으로의 지역 변환은 현재 지원되지 않습니다. 지역에서 가용성 영역 지원을 사용하도록 설정하려면 가용성 영역 지원을 사용하도록 설정하여 레지스트리를 원하는 지역에 만들거나, 가용성 영역 지원을 사용하도록 설정하여 복제된 지역을 추가해야 합니다.
* 영역 중복은 지역에서 사용하지 않도록 설정할 수 없습니다.
* [ACR 작업](container-registry-tasks-overview.md)은 아직 가용성 영역을 지원하지 않습니다.

## <a name="about-zone-redundancy"></a>영역 중복 정보

Azure [가용성 영역](../availability-zones/az-overview.md)을 사용하여 Azure 지역 내에서 복원력 있고 가용성이 높은 Azure Container Registry를 만듭니다. 예를 들어 조직은 다른 [지원되는 Azure 리소스](../availability-zones/az-region.md)에서 영역 중복 Azure Container Registry를 설정하여 데이터 보존 및 기타 준수 요구 사항을 충족하면서 지역 내에서 고가용성을 제공할 수 있습니다.

또한 Azure Container Registry는 여러 지역에서 서비스를 복제하는 [지역에서 복제](container-registry-geo-replication.md)를 지원하여 다른 위치의 컴퓨팅 리소스에 중복성 및 위치를 사용하도록 설정합니다. 지역 내 중복성을 위한 가용성 영역과 여러 지역 간의 지역에서 복제를 조합하면 레지스트리의 안정성과 성능이 향상됩니다.

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 각 영역에는 독립적인 전원, 냉각 및 네트워킹을 갖춘 데이터 센터가 하나 이상 있습니다. 영역 중복을 위해 구성된 경우 레지스트리(또는 다른 지역의 레지스트리 복제본)는 해당 지역의 모든 가용성 영역에서 복제되어 데이터 센터 오류가 발생하는 경우에도 사용할 수 있도록 유지됩니다.

## <a name="create-a-zone-redundant-registry---cli"></a>영역 중복 레지스트리 만들기 - CLI

Azure CLI를 사용하여 영역 중복을 사용하도록 설정하려면 Azure CLI 버전 2.17.0 이상 또는 Azure Cloud Shell이 필요합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create](/cli/azure/group#az_group_create) 명령을 실행하여 레지스트리에 대한 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>영역 지원 레지스트리 만들기

[az acr create](/cli/azure/acr#az_acr_create) 명령을 실행하여 프리미엄 서비스 계층에 영역 중복 레지스트리를 만듭니다. Azure Container Registry에 대해 [가용성 영역을 지원](../availability-zones/az-region.md)하는 지역을 만듭니다. 다음 예제에서는 *eastus* 지역에서 영역 중복을 사용하도록 설정합니다. 더 많은 레지스트리 옵션은 `az acr create` 명령을 참조하세요.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

명령 출력에서 레지스트리의 `zoneRedundancy` 속성을 확인합니다. 사용하도록 설정하면 레지스트리가 영역 중복됩니다.

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>영역 중복 복제 만들기

[az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) 명령을 실행하여 Azure Container Registry에 대해 [가용성 영역을 지원](../availability-zones/az-region.md)하는 지역(예: *westus2*)에 영역 중복 레지스트리 복제본을 만듭니다. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
명령 출력에서 복제본의 `zoneRedundancy` 속성을 확인합니다. 사용하도록 설정하면 복제본이 영역 중복됩니다.

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>영역 중복 레지스트리 만들기 - 포털

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
1. **리소스 만들기** > **컨테이너** > **컨테이너 레지스트리** 를 선택합니다.
1. **기본 사항** 탭에서 리소스 그룹을 선택하거나 만들고, 고유한 레지스트리 이름을 입력합니다. 
1. **위치** 에서 Azure Container Registry에 대해 영역 중복을 지원하는 지역(예: ‘미국 동부’)을 선택합니다.
1. **SKU** 에서 **프리미엄** 을 선택합니다.
1. **가용성 영역** 에서 **사용** 을 선택합니다.
1. 필요에 따라 추가 레지스트리 설정을 구성한 다음, **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택하여 레지스트리 인스턴스를 배포합니다.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Azure Portal에서 영역 중복 사용":::

영역 중복 복제를 만들려면 다음을 수행합니다.

1. 프리미엄 계층 컨테이너 레지스트리로 이동하고 **복제** 를 선택합니다.
1. 맵이 표시되면 Azure Container Registry에 대해 영역 중복을 지원하는 지역(예: **미국 서부 2**)에서 녹색 육각형을 선택합니다. 또는 **+ 추가** 를 선택합니다.
1. **복제 만들기** 창에서 **위치** 를 확인합니다. **가용성 영역** 에서 **사용** 을 선택한 다음, **만들기** 를 선택합니다.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Azure Portal에서 영역 중복 복제 사용":::

## <a name="create-a-zone-redundant-registry---template"></a>영역 중복 레지스트리 만들기 - 템플릿

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create](/cli/azure/group#az_group_create) 명령을 실행하여 Azure Container Registry에 대해 [가용성 영역을 지원](../availability-zones/az-region.md)하는 지역(예: *eastus*)의 레지스트리에 대한 리소스 그룹을 만듭니다. 이 지역은 템플릿에서 레지스트리 위치를 설정하는 데 사용됩니다.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>템플릿 배포 

다음 Resource Manager 템플릿을 사용하여 영역 중복, 지역 복제 레지스트리를 만들 수 있습니다. 기본적으로 템플릿은 레지스트리 및 지역 복제본에서 영역 중복을 사용하도록 설정합니다. 

다음 내용을 새 파일에 복사하고 `registryZone.json`과 같은 파일 이름을 사용하여 저장합니다.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

다음 [az deployment group create](/cli/azure/group/deployment#az_group_deployment_create) 명령을 실행하여 이전 템플릿 파일로 레지스트리를 만듭니다. 표시된 위치에 다음을 제공합니다.

* 고유한 레지스트리 이름, 매개 변수 없이 템플릿을 배포면 자동으로 고유한 이름 생성
* 가용성 영역을 지원하는 복제본의 위치(예: *westus2*)

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

명령 출력에서 레지스트리 및 복제본의 `zoneRedundancy` 속성을 확인합니다. 사용하도록 설정하면 각 리소스가 영역 중복됩니다.

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>다음 단계

* [가용성 영역을 지원하는 지역](../availability-zones/az-region.md)에 대해 자세히 알아봅니다.
* Azure에서 [안정성](/azure/architecture/framework/resiliency/app-design)을 보장하도록 빌드하는 방법을 자세히 알아봅니다.
