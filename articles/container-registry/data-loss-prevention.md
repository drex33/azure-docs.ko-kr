---
title: 아티팩트 내보내기 사용 안 함
description: Premium Azure Container Registry에서 데이터 유출을 방지하도록 레지스트리 속성을 설정합니다.
ms.topic: how-to
ms.date: 07/27/2021
ms.openlocfilehash: 8fa32197069376c71c035df0285852f4041efc64
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535483"
---
# <a name="disable-export-of-artifacts-from-an-azure-container-registry"></a>Azure Container Registry에서 아티팩트 내보내기 사용 안 함 

조직의 레지스트리 사용자가 가상 네트워크 외부의 아티팩트에서 악의적으로 또는 실수로 누출되지 않도록 하려면 레지스트리의 *내보내기 정책* 을 구성하여 내보내기를 사용하지 않도록 설정할 수 있습니다.

내보내기 정책은 프리미엄 컨테이너 레지스트리에 대한 API 버전 **2021-06-01-preview** 에 도입된 속성입니다. `exportPolicy` 속성의 상태가 `disabled`로 설정되면 사용자가 다음을 시도할 때 네트워크 제한 레지스트리에서 아티팩트 내보내기를 차단합니다.

* 다른 Azure Container Registry로 레지스트리의 아티팩트 [가져오기](container-registry-import-images.md)
* 다른 컨테이너 레지스트리로 아티팩트 전송을 위한 레지스트리 [내보내기 파이프라인](container-registry-transfer-images.md) 만들기

> [!NOTE]
> 아티팩트 내보내기를 사용하지 않도록 하면 권한 있는 사용자가 가상 네트워크 내의 레지스트리에 액세스하여 아티팩트 끌어오거나 다른 데이터 평면 작업을 수행할 수 없습니다. 이 사용을 감사하려면 레지스트리 작업을 [모니터링](monitor-service.md)하도록 진단 설정을 구성하는 것이 좋습니다. 

## <a name="prerequisites"></a>필수 구성 요소

* [프라이빗 엔드포인트](container-registry-private-link.md)로 구성된 프리미엄 컨테이너 레지스트리입니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="other-requirements-to-disable-exports"></a>내보내기를 사용하지 않도록 설정하는 기타 요구 사항

* **공용 네트워크 액세스 사용 안 함** - 아티팩트 내보내기를 사용하지 않도록 설정하려면 레지스트리에 대한 공용 액세스도 사용하지 않도록 설정해야 합니다(레지스트리의 `publicNetworkAccess` 속성을 `disabled`로 설정해야 함). 내보내기를 사용하지 않도록 설정하거나 동시에 사용하지 않도록 설정하기 전에 레지스트리에 대한 공용 네트워크 액세스를 사용하지 않도록 설정할 수 있습니다.

    레지스트리의 퍼블릭 엔드포인트에 대한 액세스를 사용하지 않도록 설정하면 레지스트리 작업이 가상 네트워크 내에서만 허용됩니다. 아티팩트 끌어오기 및 기타 작업을 수행하기 위한 레지스트리에 대한 공용 액세스는 금지됩니다. 

*  **내보내기 파이프라인 제거** - 레지스트리의 `exportPolicy` 상태를 `disabled`로 설정하기 전에 레지스트리에 구성된 기존 내보내기 파이프라인을 삭제합니다. 파이프라인이 구성된 경우 `exportPolicy` 상태를 변경할 수 없습니다.

## <a name="disable-exportpolicy-for-an-existing-registry"></a>기존 레지스트리에 대해 exportPolicy 사용 안 함

레지스트리를 만들 때 `exportPolicy` 상태는 기본적으로 `enabled`로 설정되어 아티팩트 내보내기를 허용합니다. ARM 템플릿 또는 `az resource update` 명령을 사용하여 상태를 `disabled`로 업데이트할 수 있습니다.

### <a name="arm-template"></a>ARM 템플릿 

다음 JSON을 포함하여 `exportPolicy` 상태를 업데이트하고 `publicNetworkAccess` 속성을 `disabled`로 설정합니다. [ARM 템플릿으로 리소스를 배포](../azure-resource-manager/templates/deploy-cli.md)하는 방법에 대해 자세히 알아봅니다.

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
      "publicNetworkAccess": "disabled",
      "policies": {
        "exportPolicy": {
          "status": "disabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

[az resource update](/cli/azure/resource/#az_resource_update)를 실행하여 기존 레지스트리의 `exportPolicy` 상태를 `disabled`로 설정합니다. 레지스트리 및 리소스 그룹의 이름을 대체합니다.

이 예제와 같이 `exportPolicy` 속성을 사용하지 않도록 설정할 때 `publicNetworkAccess` 속성도 `disabled`로 설정합니다.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=disabled" \
    --set "properties.publicNetworkAccess=disabled"  
```

출력에 내보내기 정책 상태가 비활성화된 것으로 표시됩니다.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "identity": null,
  "kind": null,
  "location": "centralus",
  "managedBy": null,
  "name": "myregistry",
  "plan": null,
  "properties": {
    [...]
    "policies": {
      "exportPolicy": {
        "status": "disabled"
      },
      "quarantinePolicy": {
        "status": "disabled"
      },
      "retentionPolicy": {
        "days": 7,
        "lastUpdatedTime": "2021-07-20T23:20:30.9985256+00:00",
        "status": "disabled"
      },
      "trustPolicy": {
        "status": "disabled",
        "type": "Notary"
      },
    "privateEndpointConnections": [],
    "provisioningState": "Succeeded",
    "publicNetworkAccess": "Disabled",
    "zoneRedundancy": "Disabled"
[...]
}
```

## <a name="enable-exportpolicy"></a>exportPolicy 사용 

레지스트리에서 `exportPolicy` 상태를 사용하지 않도록 설정한 후에는 언제든지 ARM 템플릿 또는 `az resource update` 명령을 사용하여 다시 사용하도록 설정할 수 있습니다.

### <a name="arm-template"></a>ARM 템플릿 

다음 JSON을 포함하여 `exportPolicy` 상태를 `enabled`로 업데이트합니다. [ARM 템플릿으로 리소스를 배포](../azure-resource-manager/templates/deploy-cli.md)하는 방법에 대해 자세히 알아봅니다.

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
     "policies": {
        "exportPolicy": {
          "status": "enabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

[az resource update](/cli/azure/resource/#az_resource_update)를 실행하여 `exportPolicy` 상태를 `enabled`로 설정합니다. 레지스트리 및 리소스 그룹의 이름을 대체합니다.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=enabled"
```
 
## <a name="next-steps"></a>다음 단계

* [Azure Container Registry 역할 및 권한](container-registry-roles.md)에 대해 알아봅니다.
* 레지스트리 아티팩트가 실수로 삭제되는 것을 방지하려면 [컨테이너 이미지 잠금](container-registry-image-lock.md)을 참조하세요.
* Azure Container Registry를 보호하는 기본 제공 [Azure 정책](container-registry-azure-policy.md)에 대해 알아봅니다.
