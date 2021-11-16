---
title: 이미지 잠그기
description: 컨테이너 이미지 또는 리포지토리의 특성을 설정하여 Azure Container Registry에서 삭제하거나 덮어쓸 수 없습니다.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 0b2cdd770233833e45c84bea1916ddf7f6e1e317
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494556"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Azure Container Registry의 컨테이너 이미지 잠금

Azure Container Registry에서 이미지 버전 또는 리포지토리를 삭제하거나 업데이트할 수 없도록 잠글 수 있습니다. 이미지나 리포지토리를 잠그려면 Azure CLI 명령 [az acr repository update][az-acr-repository-update]를 사용하여 해당 특성을 업데이트합니다. 

이 문서에서는 Azure Cloud Shell에서 또는 로컬로 Azure CLI를 실행해야 합니다(버전 2.0.55 이상 권장). `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

> [!IMPORTANT]
> 이 문서는 Azure Portal의 **설정 > 잠금** 또는 Azure CLI의 `az lock` 명령을 사용하는 것처럼 전체 레지스트리를 잠그는 경우에는 적용되지 않습니다. 레지스트리 리소스를 잠그더라도 리포지토리에서 데이터를 생성, 업데이트 또는 삭제할 수 있습니다. 레지스트리를 잠그면 복제를 추가 또는 삭제하거나 레지스트리 자체를 삭제하는 등의 관리 작업에만 영향을 줍니다. 자세한 내용은 [예기치 않은 변경이 생기지 않도록 리소스 잠금](../azure-resource-manager/management/lock-resources.md)을 참조하세요.

## <a name="scenarios"></a>시나리오

기본적으로 Azure Container Registry의 태그가 지정된 이미지는 *변경 가능* 하므로 적절한 사용 권한이 있으면 동일한 태그가 있는 이미지를 반복적으로 업데이트하고 레지스트리로 푸시할 수 있습니다. 필요에 따라 컨테이너 이미지를 [삭제](container-registry-delete.md)할 수도 있습니다. 이 동작은 이미지를 개발하고 레지스트리의 크기를 유지해야 하는 경우에 유용합니다.

그러나 컨테이너 이미지를 프로덕션에 배포하는 경우에는 *변경 불가능* 한 컨테이너 이미지가 필요할 수 있습니다. 변경 불가능한 이미지는 실수로 삭제하거나 덮어쓸 수 없는 이미지입니다.

레지스트리에서 이미지에 태그를 지정하고 버전을 지정하는 전략은 [컨테이너 이미지 태그 지정 및 버전 지정 권장 사항](container-registry-image-tag-version.md)을 참조하세요.

다음을 수행할 수 있도록 [az acr repository update][az-acr-repository-update] 명령을 사용하여 리포지토리 특성을 설정합니다.

* 이미지 버전 또는 전체 리포지토리 잠금

* 이미지 버전 또는 리포지토리가 삭제되지 않도록 보호하지만 업데이트는 허용

* 이미지 버전 또는 전체 리포지토리에서 읽기(끌어오기) 작업 방지

예제는 다음 섹션을 참조하세요. 

## <a name="lock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 

### <a name="show-the-current-repository-attributes"></a>현재 리포지토리 특성 표시
리포지토리의 현재 특성을 보려면 다음 [az acr repository show][az-acr-repository-show] 명령을 실행합니다.

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>현재 이미지 특성 표시
태그의 현재 특성을 보려면 다음 [az acr repository show][az-acr-repository-show] 명령을 실행합니다.

```azurecli
az acr repository show \
    --name myregistry --image myimage:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>태그로 이미지 잠금

Myimage에서 *myimage: tag* 이미지를 잠그려면 다음 [az acr repository update][az-acr-repository-update] 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>매니페스트 다이제스트로 이미지 잠금

매니페스트 다이제스트 (로 표시 되는 SHA-256 해시)로 식별 되는 *myimage* 이미지를 잠그려면 `sha256:...` 다음 명령을 실행 합니다. (하나 이상의 이미지 태그와 연결된 매니페스트 다이제스트를 찾으려면 [az acr repository show-manifests][az-acr-repository-show-manifests] 명령을 실행합니다.)

```azurecli
az acr repository update \
    --name myregistry --image myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>리포지토리 잠금

*Myrepo* 리포지토리 및 모든 이미지를 잠그려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>이미지 또는 리포지토리가 삭제되지 않도록 보호

### <a name="protect-an-image-from-deletion"></a>이미지가 삭제되지 않도록 보호

*Myimage: tag* 이미지를 업데이트 하지만 삭제 하지 않도록 허용 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>리포지토리가 삭제되지 않도록 보호

다음 명령은 *myrepo* 리포지토리를 삭제할 수 없도록 설정 합니다. 개별 이미지는 계속 업데이트하거나 삭제할 수 있습니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>이미지 또는 리포지토리에서 읽기 작업 방지

*Myimage: tag* 이미지에서 읽기 (끌어오기) 작업을 방지 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --read-enabled false
```

*Myrepo* 리포지토리의 모든 이미지에 대 한 읽기 작업을 방지 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 해제

삭제 하 고 업데이트할 수 있도록 *myimage: tag* 이미지의 기본 동작을 복원 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --delete-enabled true --write-enabled true
```

*Myrepo* 리포지토리 및 모든 이미지를 삭제 하 고 업데이트할 수 있도록 기본 동작을 복원 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 [az acr repository update][az-acr-repository-update] 명령을 사용하여 리포지토리의 이미지 버전 삭제 또는 업데이트를 방지하는 방법을 배웠습니다. 추가 특성을 설정하려면 [az acr repository update][az-acr-repository-update] 명령 참조를 참조하세요.

이미지 버전 또는 리포지토리에 설정된 특성을 보려면 [az acr repository show][az-acr-repository-show] 명령을 사용합니다.

삭제 작업에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제][container-registry-delete]를 참조하세요.

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md
