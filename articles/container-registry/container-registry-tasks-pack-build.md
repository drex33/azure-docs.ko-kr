---
title: 클라우드 네이티브 빌드팩을 사용하여 이미지 빌드
description: Dockerfile을 사용하지 않고 az acr pack build 명령을 사용하여 앱에서 컨테이너 이미지를 빌드하고 Azure Container Registry에 푸시합니다.
ms.topic: article
ms.date: 06/24/2021
ms.custom: devx-track-js
ms.openlocfilehash: 28630c46ea56bd4df1a43e5e377b3051f9cdd966
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913965"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>클라우드 네이티브 빌드팩을 사용하여 앱에서 이미지 빌드 및 푸시

Azure CLI 명령 `az acr pack build`는 [빌드팩](https://buildpacks.io/)에서 [`pack`](https://github.com/buildpack/pack) CLI 도구를 사용하여 앱을 빌드하고 이미지를 Azure Container Registry에 푸시합니다. 이 기능은 Dockerfile을 정의할 필요 없이 Node.js, Java, 기타 언어로 된 애플리케이션 소스 코드에서 컨테이너 이미지를 신속하게 빌드할 수 있는 옵션을 제공합니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 문서의 예제를 실행할 수 있습니다. 로컬로 사용하려는 경우 2.0.70 이상 버전이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="use-the-build-command"></a>build 명령 사용

클라우드 네이티브 빌드팩을 사용하여 컨테이너 이미지를 빌드하고 푸시하려면 [az acr pack build][az-acr-pack-build] 명령을 실행합니다. [az acr build][az-acr-build] 명령은 Dockerfile 소스 및 관련 코드에서 이미지를 빌드하여 푸시하는 반면에 `az acr pack build`를 사용하면 애플리케이션 소스 트리를 직접 지정할 수 있습니다.

최소한 `az acr pack build`를 실행하는 경우 다음을 지정합니다.

* 명령을 실행하는 Azure Container Registry
* 결과 이미지에 대한 이미지 이름 및 태그
* ACR 작업에 [지원되는 컨텍스트 위치](container-registry-tasks-overview.md#context-locations)(예: 로컬 디렉터리, GitHub 리포지토리 또는 원격 tarball) 중 하나
* 애플리케이션에 적합한 빌드팩 빌더 이미지의 이름 Azure Container Registry에서 캐시되지 않는 경우 작성기 이미지는 `--pull` 매개 변수를 사용하여 끌어와야 합니다.  

`az acr pack build`는 [실행 변수](container-registry-tasks-reference-yaml.md#run-variables) 및 스트리밍되고 나중에 검색할 수 있도록 저장되는 [작업 실행 로그](container-registry-tasks-logs.md)를 포함한 ACR 작업 명령의 다른 기능을 지원합니다.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>예제: Cloud Foundry 빌더를 사용하여 Node.js 이미지 빌드

다음 예제에서는 `cloudfoundry/cnb:cflinuxfs3` 빌더를 사용하여 [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) 리포지토리에 Node.js 앱의 컨테이너 이미지를 빌드합니다.

```azurecli
az acr pack build \
    --registry myregistry \
    --image node-app:1.0 \
    --pull --builder cloudfoundry/cnb:cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

이 예제에서는 `1.0` 태그를 사용하여 `node-app` 이미지를 빌드하고 *myregistry* 컨테이너 레지스트리에 푸시합니다. 이 예제에서는 대상 레지스트리 이름이 이미지 이름 앞에 명시적으로 붙습니다. 지정하지 않으면 레지스트리 로그인 서버 이름이 이미지 이름 앞에 자동으로 붙습니다.

명령 출력은 이미지를 빌드하고 푸시하는 진행률을 보여 줍니다. 

이미지가 성공적으로 빌드되면 설치한 경우 Docker를 사용하여 실행할 수 있습니다. 먼저 레지스트리에 로그인합니다.

```azurecli
az acr login --name myregistry
```

이미지를 실행합니다.

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

즐겨 사용하는 브라우저에서 `localhost:1337`로 이동하여 샘플 웹앱을 확인합니다. 컨테이너를 중지하려면 `[Ctrl]+[C]`를 누릅니다.

## <a name="example-build-java-image-with-heroku-builder"></a>예: Heroku 빌더를 사용하여 Java 이미지 빌드

다음 예제에서는 `heroku/buildpacks:18` 빌더를 사용하여 [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) 리포지토리에 Java 앱의 컨테이너 이미지를 빌드합니다. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

이 예제는 명령의 실행 ID로 태그가 지정된 `java-app` 이미지를 빌드하여 *myregistry* 컨테이너 레지스트리에 푸시합니다.

명령 출력은 이미지를 빌드하고 푸시하는 진행률을 보여 줍니다. 

이미지가 성공적으로 빌드되면 설치한 경우 Docker를 사용하여 실행할 수 있습니다. 먼저 레지스트리에 로그인합니다.

```azurecli
az acr login --name myregistry
```

이미지를 실행하여 이미지 태그로 *runid* 를 대체합니다.

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

즐겨 사용하는 브라우저에서 `localhost:8080`로 이동하여 샘플 웹앱을 확인합니다. 컨테이너를 중지하려면 `[Ctrl]+[C]`를 누릅니다.


## <a name="next-steps"></a>다음 단계

`az acr pack build`를 사용하여 컨테이너 이미지를 빌드하여 푸시하면 선택한 대상에 임의의 이미지처럼 배포할 수 있습니다. Azure 배포 옵션에는 [App Service](../app-service/tutorial-custom-container.md) 또는 [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md)에서 실행이 포함됩니다.

ACR 작업 기능에 대한 자세한 내용은 [ACR 작업을 사용하여 컨테이너 이미지 빌드 및 유지 관리 자동화](container-registry-tasks-overview.md)를 참조하세요.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
