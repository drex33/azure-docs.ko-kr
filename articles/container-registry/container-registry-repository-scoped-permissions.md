---
title: Azure Container Registry의 리포지토리에 대한 권한
description: 프리미엄 레지스트리의 특정 리포지토리로 범위가 지정된 권한으로 토큰을 만들어 이미지를 풀하거나 푸시하거나 다른 작업을 수행합니다.
ms.topic: article
ms.date: 02/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 560722669dc663799af3219393cb7bdc4acdf3ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037989"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>리포지토리 범위 권한이 있는 토큰 만들기

이 문서에서는 컨테이너 레지스트리의 특정 리포지토리에 대한 액세스 권한을 관리하기 위한 토큰 및 범위 맵을 만드는 방법을 설명합니다. 토큰을 만들면 레지스트리 소유자가 리포지토리로 범위가 지정된 시간 제한 액세스 권한을 사용자 또는 서비스에 제공하여 이미지를 풀하거나 푸시하거나 다른 작업을 수행할 수 있습니다. 토큰은 전체 레지스트리에 대한 권한 범위를 지정하는 다른 레지스트리 [인증 옵션](container-registry-authentication.md)보다 더 세분화된 권한을 제공합니다. 

토큰을 만드는 시나리오는 다음과 같습니다.

* 개별 토큰이 있는 IoT 디바이스에서 리포지토리로부터 이미지를 가져올 수 있도록 허용합니다.
* 특정 리포지토리에 대한 권한을 외부 조직에 제공합니다. 
* 리포지토리 액세스를 조직의 다른 사용자 그룹으로 제한합니다. 예를 들어 특정 리포지토리를 대상으로 하는 이미지를 빌드하는 개발자에게 쓰기 및 읽기 권한을 제공하고, 해당 리포지토리에서 배포하는 팀에 읽기 권한을 제공합니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 리포지토리 범위 권한은 현재 Azure Active Directory ID(예: 서비스 주체 또는 관리 ID)에 할당할 수 없습니다.
* [익명 풀 액세스](container-registry-faq.yml#how-do-i-enable-anonymous-pull-access-)에 대해 사용하도록 설정된 레지스트리에서 범위 맵을 만들 수 없습니다.

## <a name="concepts"></a>개념

리포지토리 범위 권한을 구성하려면 연결된 *범위 맵* 이 포함된 *토큰* 을 만듭니다. 

* 생성된 암호와 함께 **토큰** 을 사용하면 사용자가 레지스트리를 사용하여 인증할 수 있습니다. 토큰 암호의 만료 날짜를 설정하거나 언제든지 토큰을 사용하지 않도록 설정할 수 있습니다.  

  토큰을 사용하여 인증되면 사용자 또는 서비스에서 하나 이상의 리포지토리로 범위가 지정된 하나 이상의 *작업* 을 수행할 수 있습니다.

  |작업  |설명  | 예제 |
  |---------|---------|--------|
  |`content/delete`    | 리포지토리에서 데이터 제거  | 리포지토리 또는 매니페스트 삭제 |
  |`content/read`     |  리포지토리에서 데이터 읽기 |  아티팩트 끌어오기 |
  |`content/write`     |  리포지토리에 데이터 쓰기     | `content/read`를 통해 아티팩트 푸시 |
  |`metadata/read`    | 리포지토리에서 메타데이터 읽기   | 태그 또는 매니페스트 나열 |
  |`metadata/write`     |  리포지토리에 메타데이터 쓰기  | 읽기, 쓰기 또는 삭제 작업 사용/사용 안 함 |

* **범위 맵** 은 토큰에 적용하는 리포지토리 권한을 그룹화하고 다른 토큰에 다시 적용할 수 있습니다. 모든 토큰은 단일 범위 맵에 연결됩니다. 

   범위 맵을 사용하는 경우

    * 리포지토리 세트에 대해 동일한 권한이 있는 여러 토큰을 구성합니다.
    * 범위 맵에서 리포지토리 작업을 추가 또는 제거하거나 다른 범위 맵을 적용할 때 토큰 권한을 업데이트합니다. 

  또한 Azure Container Registry는 토큰을 만들 때 적용할 수 있는 몇 가지 시스템 정의 범위 맵을 제공합니다. 시스템 정의 범위 맵의 권한은 레지스트리의 모든 리포지토리에 적용됩니다.

다음 이미지에서는 토큰과 범위 맵 간의 관계를 보여 줍니다. 

![레지스트리 토큰 및 범위 맵](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>사전 요구 사항

* **Azure CLI** - 이 문서의 Azure CLI 명령 예제에는 Azure CLI 버전 2.17.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* **Docker** - 이미지를 풀하거나 푸시하기 위해 레지스트리를 사용하여 인증하려면 로컬 Docker를 설치해야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에 대한 설치 지침을 제공합니다.
* **컨테이너 레지스트리** - 아직 없는 경우 Azure 구독에서 프리미엄 컨테이너 레지스트리를 만들거나 기존 레지스트리를 업그레이드합니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다. 

## <a name="create-token---cli"></a>토큰 만들기 - CLI

### <a name="create-token-and-specify-repositories"></a>토큰 만들기 및 리포지토리 지정

[az acr token create][az-acr-token-create] 명령을 사용하여 토큰을 만듭니다. 토큰을 만들 때 각 리포지토리에서 하나 이상의 리포지토리 및 관련 작업을 지정할 수 있습니다. 리포지토리가 아직 레지스트리에 있을 필요는 없습니다. 기존 범위 맵을 지정하여 토큰을 만들려면 [다음 섹션](#create-token-and-specify-scope-map)을 참조하세요.

다음 예제에서는 `samples/hello-world` 리포지토리에 대한 `content/write` 및 `content/read` 권한이 있는 토큰을 *myregistry* 레지스트리에 만듭니다. 이 명령은 기본적으로 기본 토큰 상태를 `enabled`로 설정하지만, 언제든지 해당 상태를 `disabled`로 업데이트할 수 있습니다.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --output json
```

출력에는 토큰에 대한 세부 정보가 표시됩니다. 기본적으로 만료되지 않는 두 개의 암호가 생성되지만 필요에 따라 만료 날짜를 설정할 수 있습니다. 나중에 인증에 사용할 수 있도록 암호를 안전한 장소에 저장하는 것이 좋습니다. 암호는 다시 검색할 수 없지만 새 암호는 생성할 수 있습니다.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> 토큰 암호 및 만료 기간을 다시 생성하려면 이 문서의 뒷부분에 나오는 [토큰 암호 다시 생성](#regenerate-token-passwords)을 참조하세요.

출력에는 명령에서 만든 범위 맵에 대한 세부 정보가 포함됩니다. 여기서는 `MyToken-scope-map`이라는 범위 맵을 사용하여 동일한 리포지토리 작업을 다른 토큰에 적용할 수 있습니다. 또는 나중에 연결된 토큰의 권한을 변경하기 위해 범위 맵을 업데이트합니다.

### <a name="create-token-and-specify-scope-map"></a>토큰 만들기 및 범위 맵 지정

토큰을 만드는 다른 방법은 기존 범위 맵을 지정하는 것입니다. 범위 맵이 아직 없는 경우 먼저 리포지토리 및 관련 작업을 지정하여 범위 맵을 만듭니다. 그런 다음, 토큰을 만들 때 범위 맵을 지정합니다. 

범위 맵을 만들려면 [az acr scope-map create][az-acr-scope-map-create] 명령을 사용합니다. 다음 명령에서는 이전에 사용한 `samples/hello-world` 리포지토리에 대해 동일한 권한이 있는 범위 맵을 만듭니다. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

[az acr token create][az-acr-token-create]를 실행하여 토큰을 만들고 *MyScopeMap* 범위 맵을 지정합니다. 이전 예제에서와 같이 이 명령은 기본 토큰 상태를 `enabled`로 설정합니다.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

출력에는 토큰에 대한 세부 정보가 표시됩니다. 기본적으로 두 개의 암호가 생성됩니다. 나중에 인증에 사용할 수 있도록 암호를 안전한 장소에 저장하는 것이 좋습니다. 암호는 다시 검색할 수 없지만 새 암호는 생성할 수 있습니다.

> [!NOTE]
> 토큰 암호 및 만료 기간을 다시 생성하려면 이 문서의 뒷부분에 나오는 [토큰 암호 다시 생성](#regenerate-token-passwords)을 참조하세요.

## <a name="create-token---portal"></a>토큰 만들기 - 포털

Azure Portal을 사용하여 토큰과 범위 맵을 만들 수 있습니다. `az acr token create` CLI 명령과 마찬가지로 하나 이상의 리포지토리 및 관련 작업을 지정하여 토큰을 만들 때 기존 범위 맵을 적용하거나 범위 맵을 만들 수 있습니다. 리포지토리가 아직 레지스트리에 있을 필요는 없습니다. 

다음 예제에서는 토큰을 만들고, `samples/hello-world` 리포지토리에 대한 `content/write` 및 `content/read` 권한이 있는 범위 맵을 만듭니다.

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **리포지토리 권한** 아래에서 **토큰(미리 보기) > +추가** 를 선택합니다.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="포털에서 토큰 만들기":::
1. 토큰 이름을 입력합니다.
1. **범위 맵** 아래에서 **새로 만들기** 를 선택합니다.
1. 범위 맵을 구성합니다.
    1. 범위 맵에 대한 이름 및 설명을 입력합니다. 
    1. **리포지토리** 아래에서 `samples/hello-world`를 입력하고, **권한** 아래에서 `content/read` 및 `content/write`를 선택합니다. 그런 다음, **+ 추가** 를 선택합니다.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="포털에서 범위 맵 만들기":::

    1. 리포지토리 및 권한이 추가되면 **추가** 를 선택하여 범위 맵을 추가합니다.
1. 기본 토큰 **상태** 를 **사용** 으로 적용한 다음, **만들기** 를 선택합니다.

토큰의 유효성이 검사되고 토큰이 만들어지면 **토큰** 화면에 토큰 세부 정보가 표시됩니다.

### <a name="add-token-password"></a>토큰 암호 추가

포털에서 만든 토큰을 사용하려면 암호를 생성해야 합니다. 하나 또는 두 개의 암호를 생성하고 각 암호의 만료 날짜를 설정할 수 있습니다. 

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **리포지토리 권한** 아래에서 **토큰(미리 보기)** 을 선택하고, 토큰을 선택합니다.
1. 토큰 세부 정보에서 **password1** 또는 **password2** 를 선택하고, [생성] 아이콘을 선택합니다.
1. 암호 화면에서 필요에 따라 암호의 만료 날짜를 설정하고, **생성** 을 선택합니다. 만료 날짜를 설정하는 것이 좋습니다.
1. 암호가 생성되면 이를 복사하여 안전한 위치에 저장합니다. 화면을 닫으면 생성된 암호를 검색할 수 없지만 새 암호는 생성할 수 있습니다.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="포털에서 토큰 암호 만들기":::

## <a name="authenticate-with-token"></a>토큰을 사용하여 인증

사용자 또는 서비스에서 토큰을 사용하여 대상 레지스트리를 인증하는 경우 토큰 이름을 사용자 이름으로 제공하고 생성된 암호 중 하나를 제공합니다. 

인증 방법은 구성된 작업 또는 토큰과 연결된 작업에 따라 달라집니다.

|작업  |인증 방법  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`(Azure CLI)<br/><br/>예: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login`(Azure CLI)<br/><br/>예: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`(Azure CLI)     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`(Azure CLI)   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`(Azure CLI) |

## <a name="examples-use-token"></a>예제: 토큰 사용

다음 예제에서는 이 문서의 앞부분에서 만든 토큰을 사용하여 리포지토리에 대한 일반적인 작업(이미지 푸시/풀, 이미지 삭제 및 리포지토리 태그 나열)을 수행합니다. 토큰은 처음에 `samples/hello-world` 리포지토리에 대한 푸시 권한(`content/write` 및 `content/read` 작업)으로 설정되었습니다.

### <a name="pull-and-tag-test-images"></a>테스트 이미지 풀 및 태그 지정

다음 예제에서는 Microsoft Container Registry에서 공개 `hello-world` 및 `nginx` 이미지를 풀하고 레지스트리 및 리포지토리용으로 태그를 지정합니다.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>토큰을 사용하여 인증

`docker login` 또는 `az acr login`을 실행하여 이미지를 푸시 또는 풀할 레지스트리에 인증합니다. 토큰 이름을 사용자 이름으로 제공하고 해당 암호 중 하나를 제공합니다. 토큰에 `Enabled` 상태여야 합니다.

다음 예제는 bash 셸 형식으로 지정되고, 환경 변수를 사용하여 값을 제공합니다.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

출력에 성공적인 인증이 표시됩니다.

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>레지스트리에 이미지 푸시

성공적으로 로그인하면 태그가 지정된 이미지를 레지스트리에 푸시하려고 시도합니다. 이미지를 `samples/hello-world` 리포지토리에 푸시할 수 있는 권한이 토큰에 있으므로 다음 푸시가 성공합니다.

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

`samples/nginx` 리포지토리에 대한 권한이 토큰에 없으므로 `requested access to the resource is denied`와 비슷한 오류로 인해 다음 푸시 시도가 실패합니다.

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>토큰 권한 업데이트

토큰의 권한을 업데이트하려면 연결된 범위 맵에서 권한을 업데이트합니다. 업데이트된 범위 맵은 연결된 모든 토큰에 즉시 적용됩니다. 

예를 들어 `MyToken-scope-map` 작업을 `samples/ngnx` 리포지토리에 대한 `content/write` 및 `content/read` 작업으로 업데이트하고, `samples/hello-world` 리포지토리에 대한 `content/write` 작업을 제거합니다.  

Azure CLI를 사용하려면 [az acr scope-map update][az-acr-scope-map-update]를 실행하여 범위 맵을 업데이트합니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

Azure Portal에서 다음을 수행합니다.

1. 컨테이너 레지스트리로 이동합니다.
1. **리포지토리 권한** 아래에서 **범위 맵(미리 보기)** 을 선택하고, 업데이트할 범위 맵을 선택합니다.
1. **리포지토리** 아래에서 `samples/nginx`을 입력하고, **권한** 아래에서 `content/read` 및 `content/write`를 선택합니다. 그런 다음, **+ 추가** 를 선택합니다.
1. **리포지토리** 아래에서 `samples/hello-world`를 선택하고, **권한** 아래에서 `content/write`를 선택 취소합니다. 그런 다음 **저장** 을 선택합니다.

범위 맵이 업데이트되면 다음 푸시가 성공합니다.

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

범위 맵에는 `samples/hello-world` 리포지토리에 대한 `content/read` 권한만 있으므로 이제 `samples/hello-world` 리포지토리에 대한 푸시 시도가 실패합니다.
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

범위 맵에서 두 리포지토리 모두에 대한 `content/read` 권한을 제공하므로 두 리포지토리 모두에서의 이미지 풀이 성공합니다.

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>이미지 삭제

`content/delete` 작업을 `nginx` 리포지토리에 추가하여 범위 맵을 업데이트합니다. 이 작업을 통해 리포지토리의 이미지를 삭제하거나 전체 리포지토리를 삭제할 수 있습니다.

간단히 하기 위해 범위 맵을 업데이트하는 [az acr scope-map update][az-acr-scope-map-update] 명령만 보여 줍니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

포털을 사용하여 범위 맵을 업데이트하려면 [이전 섹션](#update-token-permissions)을 참조하세요.

다음 [az acr repository delete][az-acr-repository-delete] 명령을 사용하여 `samples/nginx` 리포지토리를 삭제합니다. 이미지 또는 리포지토리를 삭제하려면 토큰의 이름 및 암호를 명령에 전달합니다. 다음 예제에서는 이 문서의 앞부분에서 만든 환경 변수를 사용합니다.

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>리포지토리 태그 표시 

`metadata/read` 작업을 `hello-world` 리포지토리에 추가하여 범위 맵을 업데이트합니다. 이 작업을 통해 리포지토리의 매니페스트 및 태그 데이터를 읽을 수 있습니다.

간단히 하기 위해 범위 맵을 업데이트하는 [az acr scope-map update][az-acr-scope-map-update] 명령만 보여 줍니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

포털을 사용하여 범위 맵을 업데이트하려면 [이전 섹션](#update-token-permissions)을 참조하세요.

`samples/hello-world` 리포지토리에서 메타데이터를 읽으려면 [az acr repository show-manifests][az-acr-repository-show-manifests] 또는 [az acr repository show-tags][az-acr-repository-show-tags] 명령을 실행합니다. 

메타데이터를 읽으려면 토큰의 이름 및 암호를 두 명령 중 하나에 전달합니다. 다음 예제에서는 이 문서의 앞부분에서 만든 환경 변수를 사용합니다.

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

샘플 출력:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>토큰 및 범위 맵 관리

### <a name="list-scope-maps"></a>범위 맵 나열

[az acr scope-map list][az-acr-scope-map-list] 명령 또는 포털의 **범위 맵(미리 보기)** 화면을 사용하여 레지스트리에 구성된 모든 범위 맵을 나열합니다. 다음은 그 예입니다.

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

출력은 3개의 시스템 정의 범위 맵과 사용자에 의해 생성된 다른 범위 맵으로 구성됩니다. 토큰은 이러한 범위 맵 중 하나를 사용하여 구성할 수 있습니다.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>토큰 세부 정보 표시

상태 및 암호 만료 날짜와 같은 토큰 세부 정보를 보려면 [az acr token show][az-acr-token-show] 명령을 실행하거나 포털의 **토큰(미리 보기)** 화면에서 토큰을 선택합니다. 다음은 그 예입니다.

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

[az acr token list][az-acr-token-list] 명령 또는 포털의 **토큰(미리 보기)** 화면을 사용하여 레지스트리에 구성된 모든 토큰을 나열합니다. 다음은 그 예입니다.

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>토큰 암호 다시 생성

토큰 암호를 생성하지 않았거나 새 암호를 생성하려면 [az acr token credential generate][az-acr-token-credential-generate] 명령을 실행합니다. 

다음 예제에서는 만료 기간이 30일인 *MyToken* 토큰에 대해 password1의 새 값을 생성합니다. 암호를 `TOKEN_PWD` 환경 변수에 저장합니다. 이 예제는 bash 셸 형식으로 지정됩니다.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Azure Portal을 사용하여 토큰 암호를 생성하려면 이 문서의 앞부분에 있는 [토큰 만들기 - 포털](#create-token---portal)의 단계를 참조하세요.

### <a name="update-token-with-new-scope-map"></a>새 범위 맵으로 토큰 업데이트

토큰을 다른 범위 맵으로 업데이트하려면 [az acr token update][az-acr-token-update]를 실행하고 새 범위 맵을 지정합니다. 다음은 그 예입니다.

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

포털의 **토큰(미리 보기)** 화면에서 토큰을 선택하고, **범위 맵** 아래에서 다른 범위 맵을 선택합니다.

> [!TIP]
> 토큰이 새 범위 맵으로 업데이트되면 새 토큰 암호를 생성하는 것이 좋습니다. [az acr token credential generate][az-acr-token-credential-generate] 명령을 사용하거나 Azure Portal에서 토큰 암호를 다시 생성합니다.

## <a name="disable-or-delete-token"></a>토큰 사용 안 함 또는 삭제

사용자 또는 서비스에 대한 토큰 자격 증명을 일시적으로 사용하지 않도록 설정해야 할 수도 있습니다. 

Azure CLI를 사용하는 경우 [az acr token update][az-acr-token-update] 명령을 실행하여 `status`를 `disabled`로 설정합니다.

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

포털의 **토큰(미리 보기)** 화면에서 토큰을 선택하고, **상태** 아래에서 **사용 안 함** 을 선택합니다.

토큰을 삭제하여 해당 자격 증명을 사용하는 모든 사용자의 액세스를 영구적으로 무효화하려면 [az acr token delete][az-acr-token-delete] 명령을 실행합니다. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

포털의 **토큰(미리 보기)** 화면에서 토큰을 선택하고, **취소** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* 범위 맵 및 토큰을 관리하기 위해 [az acr scope-map][az-acr-scope-map] 및 [az acr token][az-acr-token] 명령 그룹의 추가 명령을 사용합니다.
* Azure 컨테이너 레지스트리를 사용하여 인증하는 다른 옵션(Azure Active Directory ID, 서비스 주체 또는 관리자 계정 사용 포함)은 [인증 개요](container-registry-authentication.md)를 참조하세요.
* 연결된 [레지스트리에](intro-connected-registry.md) 대해 알아보고 [액세스에](overview-connected-registry-access.md)토큰을 사용하는 방법을 알아봅니다.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az_acr_repository_show_manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az_acr_repository_delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az_acr_scope_map_create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az_acr_scope_map_update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az_acr_token_show
[az-acr-token-list]: /cli/azure/acr/token/#az_acr_token_list
[az-acr-token-delete]: /cli/azure/acr/token/#az_acr_token_delete
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-update]: /cli/azure/acr/token/#az_acr_token_update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az_acr_token_credential_generate
