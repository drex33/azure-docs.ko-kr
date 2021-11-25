---
title: 빠른 시작 - 레지스트리 만들기 - Azure CLI
description: Azure CLI를 사용한 프라이빗 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다.
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli, mode-api
ms.openlocfilehash: fb077ccf3ed3eff6ee636e29634ba5e121974a7c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133059609"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 프라이빗 컨테이너 레지스트리 만들기

Azure Container Registry는 컨테이너 이미지 및 관련 아티팩트를 빌드, 저장 및 관리하기 위한 프라이빗 레지스트리 서비스입니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry 인스턴스를 만들었습니다. 그런 다음, Docker 명령을 사용하여 컨테이너 이미지를 레지스트리로 푸시하고, 마지막으로 레지스트리에서 이미지를 끌어와서 실행합니다.

이 빠른 시작을 수행하려면 Azure CLI를 실행해야 합니다(버전 2.0.55 이상 권장). `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [macOS][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 Docker를 쉽게 구성할 수 있는 패키지를 제공합니다.

Azure Cloud Shell에는 필요한 Docker 구성 요소(`dockerd` 데몬) 중 일부가 없기 때문에 이 빠른 시작에 Cloud Shell을 사용할 수 없습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

이 빠른 시작에서는 Azure Container Registry에 대해 배우기 시작하는 개발자를 위해 비용 최적화된 옵션인 *기본* 레지스트리를 만듭니다. 사용 가능한 서비스 계층에 대한 자세한 내용은 [컨테이너 레지스트리 서비스 계층][container-registry-skus]을 참조하세요.

[az acr create][az-acr-create] 명령을 사용하여 ACR 인스턴스를 만듭니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 다음 예제에서는 *myContainerRegistry007* 을 사용합니다. 이를 고유한 값으로 업데이트합니다.

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

레지스트리를 만들면 출력은 다음과 비슷합니다.

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

정규화된 레지스트리 이름(모두 소문자)인 출력의 `loginServer`를 기록해 둡니다. 이 빠른 시작의 나머지 부분에서 `<registry-name>`은 컨테이너 레지스트리 이름의 자리 표시자이고 `<login-server>`는 레지스트리의 로그인 서버 이름에 대한 자리 표시자입니다.

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

## <a name="log-in-to-registry"></a>레지스트리에 로그인

컨테이너 이미지를 푸시하고 끌어오려면 레지스트리에 로그인해야 합니다. 이렇게 하려면 [az acr login][az-acr-login] 명령을 사용합니다. Azure CLI를 사용하여 로그인할 때 레지스트리 리소스 이름만 지정합니다. 정규화된 로그인 서버 이름을 사용하지 마세요. 

```azurecli
az acr login --name <registry-name>
```

예제:

```azurecli
az acr login --name mycontainerregistry
```

완료되면 이 명령은 `Login Succeeded` 메시지를 반환합니다.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>컨테이너 이미지 나열

다음 예제에서는 레지스트리의 리포지토리를 나열합니다.

```azurecli
az acr repository list --name <registry-name> --output table
```

출력:

```
Result
----------------
hello-world
```

다음 예제는 **hello-world** 리포지토리의 태그를 나열합니다.

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

출력:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 레지스트리 및 저장된 컨테이너 이미지를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들고, 컨테이너 이미지를 레지스트리로 푸시하고, 레지스트리에서 이미지를 끌어와서 실행했습니다. Azure Container Registry 자습서를 계속 진행하여 ACR에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry 자습서][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry 작업 자습서][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
