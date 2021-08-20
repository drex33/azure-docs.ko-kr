---
title: 자습서 - 기본 이미지 업데이트 시 이미지 빌드 트리거
description: 이 자습서에서는 기본 이미지가 동일한 레지스트리에서 업데이트 될 때 클라우드에서 컨테이너 이미지 빌드를 자동으로 트리거하도록 Azure Container Registry 작업을 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 18716171b72fd266fda1aff06b67850159627b34
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983665"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>자습서: Azure Container Registry에서 기본 이미지가 업데이트될 때 컨테이너 이미지 빌드 자동화 

[ACR 작업](container-registry-tasks-overview.md)은 기본 이미지 중 하나에서 OS 또는 애플리케이션 프레임워크를 패치할 때와 같이 컨테이너의 [기본 이미지가 업데이트](container-registry-tasks-base-images.md)될 때 자동화되는 컨테이너 이미지 빌드를 지원합니다. 

이 자습서에서는 컨테이너의 기본 이미지가 동일한 레지스트리에 푸시될 때 클라우드에서 빌드를 트리거하는 ACR 작업을 만드는 방법에 대해 알아봅니다. 또한 기본 이미지가 [다른 Azure 컨테이너 레지스트리](container-registry-tutorial-private-base-image-update.md)로 푸시될 때 이미지 빌드를 트리거하는 ACR 작업을 만드는 방법에 대한 자습서도 사용할 수 있습니다. 

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 기본 이미지 빌드
> * 기본 이미지를 추적하기 위해 동일한 레지스트리에서 애플리케이션 이미지 만들기 
> * 애플리케이션 이미지 작업을 트리거하도록 기본 이미지 업데이트
> * 트리거된 작업 표시
> * 업데이트된 애플리케이션 이미지 확인

## <a name="prerequisites"></a>사전 요구 사항

### <a name="complete-the-previous-tutorials"></a>이전 자습서 완료

이 자습서에서는 사용자가 이미 환경을 구성했고 시리즈의 처음 두 자습서의 단계를 이미 완료했다고 가정합니다.

- Azure 컨테이너 레지스트리 만들기
- 샘플 리포지토리 포크
- 샘플 리포지토리 복제
- GitHub 개인용 액세스 토큰 만들기

아직 수행하지 않은 경우 계속하기 전에 다음 자습서를 완료하세요.

[자습서 - Azure Container Registry 작업을 사용하여 클라우드에 컨테이너 이미지 빌드](container-registry-tutorial-quick-task.md)

[Azure Container Registry 작업을 사용하여 컨테이너 이미지 빌드 자동화](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>환경 구성

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- 이 문서에는 Azure CLI 버전 2.0.46 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

이러한 셸 환경 변수를 사용자 환경에 적합한 값으로 채웁니다. 이 단계는 반드시 필요한 것이 아니지만, 여러 줄로 된 Azure CLI 명령을 이 자습서에서 좀 더 쉽게 실행할 수 있게 합니다. 이러한 환경 변수를 채우지 않는 경우 명령 예제에 나타나는 각 값을 수동으로 바꿔야 합니다.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>기본 이미지 업데이트 시나리오

이 자습서에서는 기본 이미지와 애플리케이션 이미지가 단일 레지스트리에 유지 관리되는 기본 이미지 업데이트 시나리오를 안내합니다. 

[코드 샘플][code-sample]에는 두 개의 Docker 파일, 즉 애플리케이션 이미지와 해당 애플리케이션에서 기본으로 지정하는 이미지가 포함되어 있습니다. 다음 섹션에서는 새 버전의 기본 이미지가 동일한 컨테이너 레지스트리에 푸시될 때 애플리케이션 이미지의 빌드를 자동으로 트리거하는 ACR 작업을 만듭니다.

* [Dockerfile-app][dockerfile-app]: 기반이 되는 Node.js 버전을 표시하는 정적 웹 페이지를 렌더링하는 작은 Node.js 웹 애플리케이션입니다. 버전 문자열이 시뮬레이션되며, 기본 이미지에 정의된 `NODE_VERSION` 환경 변수의 내용을 표시합니다.

* [Dockerfile-base][dockerfile-base]: `Dockerfile-app`에서 기본으로 지정하는 이미지입니다. 이미지 자체는 [노드][base-node] 이미지를 기반으로 하며 `NODE_VERSION` 환경 변수를 포함합니다.

다음 섹션에서는 작업을 만들고, Dockerfile 기본 이미지의 `NODE_VERSION` 값을 업데이트한 다음, ACR 작업을 사용하여 기본 이미지를 빌드합니다. ACR 작업에서 새 기본 이미지를 레지스트리에 푸시하면 애플리케이션 이미지의 빌드가 자동으로 트리거됩니다. 필요에 따라 애플리케이션 컨테이너 이미지를 로컬로 실행하여 빌드된 이미지에 다른 버전 문자열을 표시합니다.

이 자습서의 ACR 작업은 Dockerfile에 지정된 애플리케이션 컨테이너 이미지를 빌드하고 푸시합니다. 또한 ACR 작업은 [다단계 작업](container-registry-tasks-multi-step.md)을 실행하고, YAML 파일을 사용하여 여러 컨테이너를 빌드하고, 푸시하고, 필요에 따라 테스트하는 단계를 정의할 수 있습니다.

## <a name="build-the-base-image"></a>기본 이미지 빌드

먼저 [az acr build][az-acr-build]를 사용하여 *빠른 작업* ACR 작업을 통해 기본 이미지를 빌드합니다. 시리즈의 [첫 번째 자습서](container-registry-tutorial-quick-task.md)에서 설명한 대로 이 프로세스는 이미지를 빌드할 뿐만 아니라 빌드가 성공하면 컨테이너 레지스트리에 푸시합니다.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>작업을 만듭니다.

다음으로, [az acr task create][az-acr-task-create]를 사용하여 작업을 만듭니다.

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample1 \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

이 작업은 [이전 자습서](container-registry-tutorial-build-task.md)에서 만든 작업과 비슷합니다. 커밋이 `--context`에 지정된 리포지토리에 푸시될 때 ACR 작업에서 이미지 빌드를 트리거하도록 지시합니다. 이전 자습서에서 이미지를 빌드하는 데 사용된 Dockerfile은 공용 기본 이미지(`FROM node:15-alpine`)를 지정하지만 이 작업의 Dockerfile([dockerfile-app][dockerfile-app])은 동일한 레지스트리의 기본 이미지를 지정합니다.

```dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

이 구성을 통해 이 자습서의 뒷부분에서 기본 이미지의 프레임워크 패치를 쉽게 시뮬레이션할 수 있습니다.

## <a name="build-the-application-container"></a>애플리케이션 컨테이너 빌드

[az acr task run][az-acr-task-run]을 사용하여 작업을 수동으로 트리거하고 애플리케이션 이미지를 빌드합니다. 이 단계는 작업에서 기본 이미지에 대한 애플리케이션 이미지의 종속성을 추적하는 데 필요합니다.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample1
```

작업이 완료된 후 다음의 선택적 단계를 수행하려면 **실행 ID**(예: "da6")를 적어 둡니다.

### <a name="optional-run-application-container-locally"></a>선택 사항: 로컬로 애플리케이션 컨테이너 실행

로컬(Cloud Shell이 아님)에서 작업 중이고 Docker가 설치되어 있는 경우, 기본 이미지를 다시 빌드하기 전에 컨테이너를 실행하여 웹 브라우저에서 렌더링된 애플리케이션을 확인합니다. Cloud Shell을 사용하는 경우 이 섹션을 건너뜁니다(Cloud Shell에서는 `az acr login` 또는 `docker run`을 지원하지 않음).

먼저 [az acr login][az-acr-login]을 사용하여 컨테이너 레지스트리에 인증합니다.

```azurecli
az acr login --name $ACR_NAME
```

이제 `docker run`을 사용하여 컨테이너를 로컬로 실행합니다. **\<run-id\>** 를 이전 단계의 출력에 있는 실행 ID(예: "da6")로 바꿉니다. 이 예에서는 컨테이너 `myapp`의 이름을 지정하고 컨테이너를 중지할 때 제거하도록 `--rm` 매개 변수가 포함됩니다.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

브라우저에서 `http://localhost:8080`으로 이동하면 다음과 비슷하게 웹 페이지에 렌더링된 Node.js 버전 번호가 표시됩니다. 이후 단계에서 버전 문자열에 "a"를 추가하여 버전을 범프합니다.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="브라우저의 샘플 애플리케이션 스크린샷":::

컨테이너를 중지하고 제거하려면 다음 명령을 실행합니다.

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>빌드 나열

다음으로, [az acr task list-runs][az-acr-task-list-runs] 명령을 사용하여 ACR 작업에서 레지스트리에 대해 완료한 작업 실행을 나열합니다.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

이전 자습서를 완료했으면(레지스트리를 삭제하지 않은 경우) 다음과 비슷한 출력이 표시됩니다. 다음 섹션에서 기본 이미지를 업데이트한 후 출력을 비교할 수 있도록 작업 실행 횟수와 최신 RUN ID를 적어 둡니다.

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cax       baseexample1    linux       Succeeded  Manual     2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual     2020-11-20T23:07:29Z  00:00:27
```

## <a name="update-the-base-image"></a>기본 이미지 업데이트

여기서는 기본 이미지의 프레임워크 패치를 시뮬레이션합니다. **Dockerfile-base** 를 편집하고, `NODE_VERSION`에 정의된 버전 번호 뒤에 "a"를 추가합니다.

```dockerfile
ENV NODE_VERSION 15.2.1a
```

빠른 작업을 실행하여 수정된 기본 이미지를 빌드합니다. 출력에 있는 **실행 ID** 를 적어 둡니다.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

빌드가 완료되고 ACR 작업에서 새 기본 이미지를 레지스트리에 푸시하면 애플리케이션 이미지의 빌드가 트리거됩니다. 새로 빌드되고 푸시된 기본 이미지를 검색해야 하므로 이전에 만든 작업에서 애플리케이션 이미지 빌드를 트리거하는 데 약간의 시간이 걸릴 수 있습니다.

## <a name="list-updated-build"></a>업데이트된 빌드 나열

이제 기본 이미지를 업데이트했으므로 작업 실행을 다시 나열하여 이전 목록과 비교합니다. 처음에 출력이 다르지 않으면 명령을 정기적으로 실행하여 새 작업 실행이 목록에 표시되는지 확인합니다.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

출력은 다음과 유사합니다. 마지막으로 실행된 빌드에 대한 TRIGGER는 기본 이미지의 빠른 작업에서 작업을 시작했음을 나타내는 "Image Update"(이미지 업데이트)여야 합니다.

```output
Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual        2020-11-20T23:07:29Z  00:00:27
```

새로 빌드한 컨테이너를 실행하여 업데이트된 버전 번호를 확인하는 다음의 선택적 단계를 수행하려면 Image Update가 트리거된 빌드에 대한 **RUN ID** 값을 적어 둡니다(앞의 출력에서 "ca11"임).

### <a name="optional-run-newly-built-image"></a>선택 사항: 새로 빌드된 이미지 실행

로컬(Cloud Shell이 아님)에서 작업 중이고 Docker가 설치되어 있는 경우, 빌드가 완료되면 새 애플리케이션 이미지를 실행합니다. `<run-id>`를 이전 단계에서 얻은 RUN ID로 바꿉니다. Cloud Shell을 사용하는 경우 이 섹션을 건너뜁니다(Cloud Shell에서는 `docker run`을 지원하지 않음).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

브라우저에서 http://localhost:8081 로 이동하면 웹 페이지에 업데이트된 Node.js 버전 번호("a" 포함)가 표시됩니다.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="브라우저에서 업데이트된 샘플 애플리케이션의 스크린샷":::


중요한 것은 **기본** 이미지를 새 버전 번호로 업데이트했지만 마지막으로 빌드된 **애플리케이션** 이미지에 새 버전이 표시된다는 것입니다. ACR 작업에서 기본 이미지에 대한 변경 내용을 선택하고 애플리케이션 이미지를 자동으로 다시 빌드했습니다.

컨테이너를 중지하고 제거하려면 다음 명령을 실행합니다.

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 이미지의 기본 이미지가 업데이트되었을 때 작업을 사용하여 컨테이너 이미지 빌드를 자동으로 트리거하는 방법을 알아보았습니다.

퍼블릭 원본에서 제공되는 기본 이미지를 관리하는 전체 워크플로에 대해서는 [Azure Container Registry 작업을 사용하여 퍼블릭 콘텐츠를 사용하고 유지 관리하는 방법](tasks-consume-public-content.md)을 참조하세요. 

이제 다음 자습서로 이동하여 정의된 일정에 따라 작업을 트리거하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [일정에 따라 작업 실행](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-node]: https://hub.docker.com/_/node/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr
