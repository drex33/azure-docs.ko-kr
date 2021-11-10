---
title: Helm 차트 저장
description: Azure Container Registry의 리포지토리를 사용하여 Kubernetes 애플리케이션에 대한 Helm 차트를 저장하는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/20/2021
ms.openlocfilehash: 5c96df6458a1f1fc40f4033d367c988c246a0fde
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135781"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Azure 컨테이너 레지스트리에 Helm 차트 푸시 및 끌어오기

Kubernetes용 애플리케이션을 빠르게 관리하고 배포하려는 경우 [오픈 소스 Helm 패키지 관리자][helm]를 사용할 수 있습니다. Helm 사용 시 애플리케이션 패키지는 [Helm 차트 리포지토리](https://helm.sh/docs/topics/chart_repository/)에 수집되고 저장되는 [차트](https://helm.sh/docs/topics/charts/)로 정의됩니다.

이 문서에서는 Helm 3 명령을 사용하고 차트를 [OCI 아티팩트](container-registry-image-formats.md#oci-artifacts)로 저장하여 Azure Container Registry에서 Helm 차트 리포지토리를 호스트하는 방법을 보여 줍니다. 대부분의 시나리오에서는 개발하는 애플리케이션용으로 차트를 직접 작성하여 업로드합니다. 사용자 고유의 Helm 차트를 작성하는 방법에 대한 자세한 내용은 [차트 템플릿 개발자 가이드][develop-helm-charts]를 참조하세요. 다른 Helm 리포지토리의 기존 Helm 차트를 저장할 수도 있습니다.

> [!IMPORTANT]
> 이 문서는 버전 **3.7.1** 의 투구 3 명령으로 업데이트 되었습니다. 투구 3.7.1에는 이전 버전의 투구 3에서 도입 된 투구 CLI 명령 및 OCI 지원에 대 한 변경 내용이 포함 되어 있습니다. 

## <a name="helm-3-or-helm-2"></a>Helm 3 또는 Helm 2?

Helm 차트를 저장, 관리 및 설치하려면 Helm CLI에서 명령을 사용합니다. 주요 Helm 릴리스에는 Helm 3 및 Helm 2가 포함됩니다. 버전 차이점에 대한 자세한 내용은 [버전 FAQ](https://helm.sh/docs/faq/)를 참조하세요. 

Helm 3은 Azure Container Registry에서 Helm 차트를 호스트하는 데 사용해야 합니다. Helm 3을 사용하여 다음을 수행합니다.

* Azure Container Registry의 리포지토리에 Helm 차트를 저장하고 관리할 수 있습니다.
* Helm 차트를 레지스트리에 [OCI 아티팩트](container-registry-image-formats.md#oci-artifacts)로 저장합니다. Azure Container Registry는 Helm 차트를 포함하여 OCI 아티팩트에 대한 GA 지원을 제공합니다.
* `helm registry login` 또는 `az acr login` 명령을 사용하여 레지스트리로 인증합니다.
* `helm` 명령을 사용하여 레지스트리에서 Helm 차트 푸시, 풀 및 관리
* `helm install`를 사용 하 여 레지스트리의 Kubernetes 클러스터에 차트를 설치 합니다.

### <a name="feature-support"></a>기능 지원

Azure Container Registry는 Helm 3(현재) 또는 Helm 2(더 이상 사용되지 않음)를 사용하는지 여부에 따라 특정 Helm 차트 관리 기능을 지원합니다.

| 기능 | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| `az acr helm` 명령을 사용하여 차트 관리 | :heavy_check_mark: | |
| 차트를 OCI 아티팩트로 저장 | | :heavy_check_mark:  |
| Azure Portal에서 `az acr repository` 명령과 **리포지토리** 블레이드를 사용하여 차트 관리| | :heavy_check_mark:  |


> [!NOTE]
> Helm 3부터 Helm 2 클라이언트와 함께 사용하기 위한 [az acr helm][az-acr-helm] 명령은 더 이상 사용되지 않습니다. 명령 제거 전에 최소 3개월의 알림이 제공됩니다.

### <a name="chart-version-compatibility"></a>차트 버전 호환성

다음 Helm [차트 버전](https://helm.sh/docs/topics/charts/#the-apiversion-field)은 Azure Container Registry에 저장할 수 있으며 Helm 2 및 Helm 3 클라이언트에서 설치할 수 있습니다. 

| 버전 | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| apiVersion v1 | :heavy_check_mark: | :heavy_check_mark: |
| apiVersion v2 | | :heavy_check_mark: |

### <a name="migrate-from-helm-2-to-helm-3"></a>Helm 2에서 Helm 3으로 마이그레이션

이전에 Helm 2 및 Azure Container Registry를 사용하여 차트를 저장하고 배포한 경우 Helm 3으로 마이그레이션하는 것이 좋습니다. 참조

* Helm 문서의 [Helm 2에서 3으로 마이그레이션](https://helm.sh/docs/topics/v2_v3_migration/)
* [레지스트리를 마이그레이션하여 Helm OCI 아티팩트 저장](#migrate-your-registry-to-store-helm-oci-artifacts)(이 문서 뒷부분)

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 시나리오에는 다음 리소스가 필요합니다.

- Azure 구독의 **Azure 컨테이너 레지스트리** 필요한 경우 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용하여 레지스트리를 만듭니다.
- **투구 클라이언트 버전 3.7.1 이상** -를 실행 `helm version` 하 여 현재 버전을 찾습니다. Helm을 설치하고 업그레이드하는 방법에 대한 자세한 내용은 [Helm 설치][helm-install]를 참조하세요. 이전 버전의 투구 3에서 업그레이드 하는 경우 [릴리스 정보](https://github.com/helm/helm/releases)를 검토 하세요.
- Helm 차트를 설치하는 **Kubernetes 클러스터** 필요한 경우 [Azure Kubernetes Service 클러스터][aks-quickstart]를 만듭니다. 
- **Azure CLI 버전 2.0.71 이상** - `az --version`을 실행하여 버전을 확인합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="enable-oci-support"></a>OCI 지원 사용

`helm version` 명령을 사용하여 Helm 3을 설치했는지 확인합니다.

```console
helm version
```

다음 환경 변수를 설정하여 Helm 3 클라이언트에서 OCI 지원을 사용하도록 설정합니다. 현재 이 지원은 실험적이며 변경될 수 있습니다. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

대상 레지스트리에 대해 다음 환경 변수를 설정 합니다. ACR_NAME은 레지스트리 리소스 이름입니다. ACR 레지스트리 url이 myregistry.azurecr.io 인 경우 ACR_NAME를 myregistry로 설정 합니다.

```console
ACR_NAME=<container-registry-name>
```

## <a name="create-a-sample-chart"></a>샘플 차트 만들기

다음 명령을 사용하여 테스트 차트를 만듭니다.

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

기본 예제로 디렉터리를 `templates` 폴더로 변경하고 해당 콘텐츠를 먼저 삭제합니다.

```console
cd hello-world/templates
rm -rf *
```

`templates` 폴더에서 다음 명령을 실행하여 `configmap.yaml`이라는 파일을 만듭니다.

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

이 예제를 만들고 실행하는 방법에 대한 자세한 내용은 Helm 문서에서 [시작](https://helm.sh/docs/chart_template_guide/getting_started/)을 참조하세요.

## <a name="save-chart-to-local-archive"></a>로컬 보관 파일에 차트 저장

디렉터리를 `hello-world` 하위 디렉터리로 변경합니다. 그런 다음를 실행 `helm package` 하 여 차트를 로컬 보관 파일에 저장 합니다. 

다음 예에서는의 이름 및 버전이 포함 된 차트가 저장 됩니다 `Chart.yaml` .

```console
cd ..
helm package .
```

출력은 다음과 비슷합니다.

```output
Successfully packaged chart and saved it to: /my/path/hello-world-0.1.0.tgz
```

## <a name="authenticate-with-the-registry"></a>레지스트리로 인증

`helm registry login`을 실행하여 레지스트리로 인증 서비스 주체 자격 증명, 사용자 id 또는 리포지토리 범위 토큰과 같은 사용자 시나리오에 적합 한 [레지스트리 자격 증명](container-registry-authentication.md) 을 전달할 수 있습니다.

- [끌어오기 및 푸시 권한](container-registry-auth-service-principal.md#create-a-service-principal) (acrpush 역할)이 포함 된 Azure Active Directory 서비스 주체를 사용 하 여 인증 합니다.
  ```bash
  SERVICE_PRINCIPAL_NAME=<acr-helm-sp>
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpull \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```
- AD 토큰을 사용 하 여 투구 차트를 푸시 및 풀 하도록 [개별 AZURE ad id](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) 로 인증 합니다.
  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```
- [리포지토리 범위 토큰](container-registry-repository-scoped-permissions.md) (미리 보기)을 사용 하 여 인증 합니다.
  ```bash
  USER_NAME="helm-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --scope-map _repositories_admin \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```
- 그런 다음에 자격 증명을 제공 `helm registry login` 합니다.
  ```bash
  helm registry login $ACR_NAME.azurecr.io \
    --username $USER_NAME \
    --password $PASSWORD
  ```

## <a name="push-chart-to-registry-as-oci-artifact"></a>레지스트리에 OCI 아티팩트로 차트 푸시

`helm push`투구 3 CLI에서 명령을 실행 하 여 차트 보관을 정규화 된 대상 리포지토리로 푸시합니다. 다음 예제에서 대상 리포지토리 네임 스페이스는이 `helm/hello-world` 고 차트에는 태그가 지정 됩니다 `0.1.0` .

```console
helm push hello-world-0.1.0.tgz oci://$ACR_NAME.azurecr.io/helm
```

푸시가 성공적으로 완료되면 출력은 다음과 유사합니다.

```output
Pushed: <registry>.azurecr.io/helm/hello-world:0.1.0
digest: sha256:5899db028dcf96aeaabdadfa5899db02589b2899b025899b059db02
```

## <a name="list-charts-in-the-repository"></a>리포지토리의 차트 목록 표시

Azure 컨테이너 레지스트리에 저장된 이미지와 마찬가지로 [az acr repository][az-acr-repository] 명령을 사용하여 차트를 호스팅하는 리포지토리와 차트 태그 및 매니페스트를 표시할 수 있습니다. 

예를 들어 [az acr repository show][az-acr-repository-show]를 실행하여 이전 단계에서 만든 리포지토리의 속성을 볼 수 있습니다.

```azurecli
az acr repository show \
  --name $ACR_NAME \
  --repository helm/hello-world
```

출력은 다음과 비슷합니다.

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2021-10-05T12:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2021-10-05T12:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

[az acr repository show-manifests][az-acr-repository-show-manifests] 명령을 실행하여 리포지토리에 저장된 차트의 세부 정보를 확인합니다. 예를 들면 다음과 같습니다.

```azurecli
az acr repository show-manifests \
  --name $ACR_NAME \
  --repository helm/hello-world --detail
```

이 예제의 축약된 출력에는 `application/vnd.cncf.helm.config.v1+json`의 `configMediaType`이 표시됩니다.

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2021-10-05T12:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2021-10-05T12:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "0.1.0"
    ]
```

## <a name="install-helm-chart"></a>Helm 차트 설치

`helm install`를 실행 하 여 레지스트리에 푸시된 투구 차트를 설치 합니다. 차트 태그는 매개 변수를 사용 하 여 전달 됩니다 `--version` . *myhelmtest* 와 같은 릴리스 이름을 지정하거나 `--generate-name` 매개 변수를 전달합니다. 예를 들면 다음과 같습니다.

```console
helm install myhelmtest oci://$ACR_NAME.azurecr.io/helm/hello-world --version 0.1.0
```

성공적인 차트 설치 후의 출력은 다음과 유사합니다.

```console
NAME: myhelmtest
LAST DEPLOYED: Tue Oct  4 16:59:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

설치를 확인하려면 `helm get manifest` 명령을 실행합니다. 

```console
helm get manifest myhelmtest
```

명령은 `configmap.yaml` 템플릿 파일의 YAML 데이터를 반환합니다.

`helm uninstall`을 실행하여 클러스터에서 차트 릴리스를 제거합니다.

```console
helm uninstall myhelmtest
```

## <a name="pull-chart-to-local-archive"></a>로컬 보관 파일로 차트 끌어오기

필요에 따라 를 사용하여 컨테이너 레지스트리에서 로컬 보관 파일로 차트를 끌어올 수 `helm pull` 있습니다. 차트 태그는 매개 변수를 사용하여 `--version` 전달됩니다. 로컬 보관이 현재 경로에 있는 경우 이 명령은 이를 덮어씁니다.

```console
helm pull oci://$ACR_NAME.azurecr.io/helm/hello-world --version 0.1.0
```

## <a name="delete-chart-from-the-registry"></a>레지스트리에서 차트 삭제

컨테이너 레지스트리에서 차트를 삭제하려면 [az acr repository delete][az-acr-repository-delete] 명령을 사용합니다. 다음 명령을 실행하고 메시지가 표시되면 작업을 확인합니다.

```azurecli
az acr repository delete --name $ACR_NAME --image helm/hello-world:0.1.0
```

## <a name="migrate-your-registry-to-store-helm-oci-artifacts"></a>Helm OCI 아티팩트를 저장하도록 레지스트리 마이그레이션

이전에 Helm 2 및 `az acr helm` 명령을 사용하여 Azure Container Registry를 차트 리포지토리로 설정한 경우 Helm 3 클라이언트로 [업그레이드][helm-install]하는 것이 좋습니다. 그런 후 다음 단계에 따라 차트를 레지스트리에 OCI 아티팩트로 저장합니다. 

> [!IMPORTANT]
> * Helm 2 스타일(index.yaml 기반) 차트 리포지토리에서 OCI 아티팩트 리포지토리로 마이그레이션을 완료한 후 Helm CLI 및 `az acr repository` 명령을 사용하여 차트를 관리합니다. 이 문서의 이전 섹션을 참조하세요. 
> * Helm OCI 아티팩트 리포지토리는 `helm search` 및 `helm repo list`와 같은 Helm 명령을 사용하여 검색할 수 없습니다. 차트를 OCI 아티팩트로 저장하는 데 사용되는 Helm 명령에 대한 자세한 내용은 [Helm 문서](https://helm.sh/docs/topics/registries/)를 참조하세요.

### <a name="enable-oci-support"></a>OCI 지원 사용

Helm 3 클라이언트를 사용 중인지 확인합니다.

```console
helm version
```

Helm 3 클라이언트에서 OCI 지원을 사용하도록 설정합니다. 현재 이 지원은 실험적이며 변경될 수 있습니다.

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="list-current-charts"></a>현재 차트 나열

현재 *myregistry* 라는 이름의 레지스트리에 저장된 차트를 나열합니다.

```console
helm search repo myregistry
```

출력에 차트 및 차트 버전이 표시됩니다.

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
myregistry/ingress-nginx        3.20.1          0.43.0          Ingress controller for Kubernetes...
myregistry/wordpress            9.0.3           5.3.2           Web publishing platform for building...
[...]
```

### <a name="pull-chart-archives-locally"></a>로컬로 끌어오기 차트 보관

리포지전의 각 차트에 대해 차트 보관 파일을 로컬로 끌어오고 파일 이름을 기록해 둡다.

```console 
helm pull myregisry/ingress-nginx
ls *.tgz
```

와 같은 로컬 차트 `ingress-nginx-3.20.1.tgz` 보관이 만들어집니다.

### <a name="push-charts-as-oci-artifacts-to-registry"></a>레지스트리에 OCI 아티팩트로 차트 푸시

레지스트리에 로그인:

```azurecli
az acr login --name $ACR_NAME
```

레지스트리에 각 차트 보관 파일을 푸시합니다. 예:

```console
helm push ingress-nginx-3.20.1.tgz oci://$ACR_NAME.azurecr.io/helm
```

차트를 푸시한 후 레지스트리에 저장되었는지 확인합니다.

```azurecli
az acr repository list --name $ACR_NAME
```

모든 차트를 푸시한 후 선택적으로 레지스트리에서 Helm 2 스타일 차트 리포지토리를 제거합니다. 이렇게 하면 레지스트리의 스토리지가 줄어듭니다.

```console
helm repo remove $ACR_NAME
```

## <a name="next-steps"></a>다음 단계

* Helm 차트를 만들고 배포하는 방법에 대한 자세한 내용은 [Helm 차트 개발][develop-helm-charts]을 참조하세요.
* [AKS(Azure Kubernetes Service)](../aks/kubernetes-helm.md)에서 Helm을 사용하여 애플리케이션을 설치하는 방법에 대해 자세히 알아보세요.
* Helm 차트는 컨테이너 빌드 프로세스의 일부로 사용할 수 있습니다. 자세한 내용은 [Azure Container Registry 작업 사용][acr-tasks]을 참조하세요.

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
