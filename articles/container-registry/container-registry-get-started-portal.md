---
title: 빠른 시작 - 포털에서 레지스트리 만들기
description: Azure Portal을 사용하여 프라이빗 Azure 컨테이너 레지스트리를 만드는 방법을 빠르게 알아봅니다.
ms.date: 06/23/2021
ms.topic: quickstart
ms.custom: mvc, mode-portal, contperf-fy21q4
ms.openlocfilehash: 8ac194e2343120dea1daec688bf383162846bdfd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043550"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure 컨테이너 레지스트리 만들기

Azure Container Registry는 컨테이너 이미지 및 관련 아티팩트를 빌드, 저장 및 관리하기 위한 프라이빗 레지스트리 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Container Registry 인스턴스를 만들었습니다. 그런 다음, Docker 명령을 사용하여 컨테이너 이미지를 레지스트리로 푸시하고, 마지막으로 레지스트리에서 이미지를 끌어와서 실행합니다.

레지스트리에 로그인하여 컨테이너 이미지를 사용할 수 있도록 이 빠른 시작에서는 Azure CLI(버전 2.0.55 이상 권장)를 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [Mac][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

**리소스 만들기** > **컨테이너** > **컨테이너 레지스트리** 를 선택합니다.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="포털의 컨테이너 레지스트리로 이동":::

**기본** 탭에서 **리소스 그룹** 및 **레지스트리 이름** 에 대한 값을 입력합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 이 빠른 시작에서는 `West US` 위치에 `myResourceGroup`이라는 새 리소스 그룹을 만들고, **SKU** 로 '기본'을 선택합니다.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="포털에서 컨테이너 레지스트리 만들기":::

나머지 설정에 대해 기본값을 그대로 적용합니다. 그런 다음, **검토 + 만들기** 를 선택합니다. 설정을 검토한 후 **만들기** 를 선택합니다.

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

**배포 성공** 메시지가 표시되면 포털에서 컨테이너 레지스트리를 선택합니다. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="포털의 컨테이너 레지스트리 개요":::

레지스트리 이름 및 **로그인 서버** 값(Azure 클라우드에서 `azurecr.io`로 끝나는 정규화된 이름)을 기록해 둡니다. Docker를 사용하여 이미지를 밀어넣고 끌어올 때 다음 단계에서 이러한 값을 사용합니다.

## <a name="log-in-to-registry"></a>레지스트리에 로그인

컨테이너 이미지를 푸시하고 끌어오려면 레지스트리 인스턴스에 로그인해야 합니다. 로컬 머신에서 [Azure CLI에 로그인][get-started-with-azure-cli]한 다음, [az acr login][az-acr-login] 명령을 실행합니다. Azure CLI를 사용하여 로그인할 때 레지스트리 리소스 이름만 지정합니다. 정규화된 로그인 서버 이름을 사용하지 마세요.

```azurecli
az acr login --name <registry-name>
```

예제:

```azurecli
az acr login --name mycontainerregistry
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>컨테이너 이미지 나열

레지스트리의 이미지를 나열하려면 포털에서 레지스트리로 이동하여 **리포지토리** 를 선택한 다음, `docker push`를 사용하여 만든 **hello-world** 리포지토리를 선택합니다.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="포털에 컨테이너 이미지 나열":::

**hello-world** 리포지토리를 선택하면 **태그** 아래에 `v1` 태그가 지정된 이미지가 표시됩니다.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>리소스 정리

리소스를 정리하려면 포털에서 **myResourceGroup** 리소스 그룹으로 이동합니다. 리소스 그룹이 로드되면 **리소스 그룹 삭제** 를 클릭하여 리소스 그룹, 컨테이너 레지스트리 및 저장된 컨테이너 이미지를 제거합니다.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="포털에서 리소스 그룹 삭제":::


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Container Registry를 만들고, 컨테이너 이미지를 푸시하고, 레지스트리에서 이미지를 끌어와서 실행했습니다. Azure Container Registry 자습서를 계속 진행하여 ACR에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry 자습서][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry 작업 자습서][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
