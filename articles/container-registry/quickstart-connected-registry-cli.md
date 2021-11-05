---
title: 빠른 시작 - CLI를 사용하여 연결된 레지스트리 만들기
description: Azure CLI 명령을 사용하여 이미지 및 기타 아티팩트를 클라우드 레지스트리와 동기화할 수 있는 연결된 Azure Container Registry 리소스를 만듭니다.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 926d9ca087f509a1aa76dcec235fd7996bce4a78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101785"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 연결된 레지스트리 만들기

이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 [연결된 레지스트리](intro-connected-registry.md) 리소스를 만듭니다. Azure Container Registry의 연결된 레지스트리 기능을 사용하면 레지스트리를 원격으로 또는 온-프레미스에서 배포하고 이미지 및 기타 아티팩트를 클라우드 레지스트리와 동기화할 수 있습니다. 

여기에서 클라우드 레지스트리에 대해 두 개의 연결된 레지스트리 리소스를 만듭니다. 그 중 하나는 읽기 및 쓰기(아티팩트 풀 및 푸시) 기능을 허용하고, 다른 하나는 읽기 전용 기능을 허용합니다. 

연결된 레지스트리를 만든 후 다른 가이드에 따라 온-프레미스 또는 원격 인프라에서 배포하고 사용할 수 있습니다.

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

* Azure Container Registry - 컨테이너 레지스트리가 아직 없는 경우 연결된 레지스트리를 지원하는 [지역](intro-connected-registry.md#available-regions)에 [하나를 만듭니다](container-registry-get-started-azure-cli.md)(프리미엄 계층 필요). 

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>클라우드 레지스트리에 대한 전용 데이터 엔드포인트 사용

[az acr update][az-acr-update] 명령을 사용하여 클라우드의 Azure Container Registry에 대해 전용 데이터 엔드포인트를 사용합니다. 이 단계는 연결된 레지스트리가 클라우드 레지스트리와 통신하는 데 필요합니다.

```azurecli
# Set the REGISTRY_NAME environment variable to identify the existing cloud registry
REGISTRY_NAME=<container-registry-name>

az acr update --name $REGISTRY_NAME \
  --data-endpoint-enabled
```

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>읽기 및 쓰기 기능을 위한 연결된 레지스트리 리소스 만들기

[az acr connected-registry create][az-acr-connected-registry-create] 명령을 사용하여 연결된 레지스트리를 만듭니다. 연결된 레지스트리 이름은 문자로 시작해야 하며 영숫자 문자만 포함해야 합니다. 길이는 5~40자여야 하며 이 Azure 컨테이너 레지스트리의 계층 구조에서 고유해야 합니다.

```azurecli
# Set the CONNECTED_REGISTRY_RW environment variable to provide a name for the connected registry with read/write functionality
CONNECTED_REGISTRY_RW=<connnected-registry-name>

az acr connected-registry create --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy"
```

이 명령은 이름이 *$CONNECTED_REGISTRY_RW* 값인 연결된 레지스트리 리소스를 만들고 이름이 *$REGISTRY_NAME* 값인 클라우드 레지스트리에 연결합니다. 빠른 시작 가이드 후반부에서는 연결된 레지스트리를 배포하는 옵션에 대해 알아봅니다. 
* 지정된 리포지토리는 배포되면 클라우드 레지스트리와 연결된 레지스트리 간에 동기화됩니다. 
* 연결된 레지스트리에 대해 `--mode` 옵션이 지정되지 않았기 때문에 기본 [ReadWrite 모드](intro-connected-registry.md#modes)에서 만들어집니다. 
* 이 연결된 레지스트리에 대해 정의된 동기화 일정이 없기 때문에 리포지토리는 중단 없이 클라우드 레지스트리와 연결된 레지스트리 간에 동기화됩니다.

  > [!IMPORTANT]
  > 하위 계층이 인터넷에 액세스할 수 없는 중첩 시나리오를 지원하려면 항상 `acr/connected-registry` 리포지토리의 동기화를 허용해야 합니다. 이 리포지토리에는 연결된 레지스트리 런타임에 대한 이미지가 포함되어 있습니다.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>읽기 전용 기능을 위한 연결된 레지스트리 리소스 만들기

[az acr connected-registry create][az-acr-connected-registry-create] 명령을 사용하여 읽기 전용 기능으로 연결된 레지스트리를 만들 수도 있습니다. 

```azurecli
# Set the CONNECTED_REGISTRY_READ environment variable to provide a name for the connected registry with read-only functionality
CONNECTED_REGISTRY_RO=<connnected-registry-name>
az acr connected-registry create --registry $REGISTRY_NAME \
  --parent $CONNECTED_REGISTRY_RW \
  --name $CONNECTED_REGISTRY_RO \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy" \
  --mode ReadOnly
```

이 명령은 이름이 *$CONNECTED_REGISTRY_RO* 값인 연결된 레지스트리 리소스를 만들고 이름이 *$REGISTRY_NAME* 값인 클라우드 레지스트리에 연결합니다. 
* 지정된 레지스트리는 *$CONNECTED_REGISTRY_RW* 값으로 명명된 부모 레지스트리와 배포된 연결된 레지스트리 간에 동기화됩니다.
* 이 리소스는 배포된 후 읽기 전용(아티팩트 풀) 기능을 사용하는 [ReadOnly 모드](intro-connected-registry.md#modes)에서 만들어집니다. 
* 이 연결된 레지스트리에 대해 정의된 동기화 일정이 없기 때문에 리포지토리는 중단 없이 부모 레지스트리와 연결된 레지스트리 간에 동기화됩니다.

## <a name="verify-that-the-resources-are-created"></a>리소스가 만들어지는지 확인합니다.

연결된 레지스트리 [az acr connected-registry list][az-acr-connected-registry-list] 명령을 사용하여 리소스가 만들어졌는지 확인할 수 있습니다. 

```azurecli
az acr connected-registry list \
  --registry $REGISTRY_NAME \
  --output table
```

다음과 같은 응답이 표시되어야 합니다. 연결된 레지스트리가 아직 배포되지 않았기 때문에 "오프라인"의 연결 상태는 현재 클라우드에서 연결이 끊긴 상태임을 나타냅니다.

```
NAME                 MODE        CONNECTION STATE    PARENT               LOGIN SERVER    LAST SYNC (UTC)
-------------------  --------    ------------------  -------------------  --------------  -----------------
myconnectedregrw    ReadWrite    Offline
myconnectedregro    ReadOnly     Offline             myconnectedregrw
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 두 개의 연결된 레지스트리 리소스를 만듭니다. 새로 연결된 레지스트리 리소스는 클라우드 레지스트리에 연결되며 클라우드 레지스트리와 아티팩트를 동기화할 수 있습니다.

연결된 레지스트리 배포 가이드를 계속 진행하여 IoT Edge 인프라에서 연결된 레지스트리를 배포하고 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [빠른 시작: IoT Edge에 연결된 레지스트리 배포][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
