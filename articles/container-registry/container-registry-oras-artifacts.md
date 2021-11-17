---
title: 밀어넣기 및 끌어오기 공급망 아티팩트
description: Azure에서 개인 컨테이너 레지스트리를 사용 하 여 푸시 및 끌어오기 공급망 아티팩트
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 11/11/2021
ms.author: stevelas
ms.custom: references_regions
ms.openlocfilehash: 4a8d3a4b73993a386d2cd49e2daa784a8bb7aced
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495076"
---
# <a name="push-and-pull-supply-chain-artifacts-using-a-private-container-registry-in-azure-preview"></a>Azure에서 개인 컨테이너 레지스트리를 사용 하는 푸시 및 끌어오기 공급망 아티팩트 (미리 보기)

Azure container registry를 사용 하 여 서명을 포함 하는 아티팩트 그래프를 저장 하 고 관리할 수 있습니다. 

![컨테이너 이미지, 서명 및 서명 된 소프트웨어 자료를 포함 하는 아티팩트의 Graph](./media/container-registry-artifacts/oras-artifact-graph.svg)

이 기능을 설명 하기 위해이 문서에서는 [oras (Storage) 도구로 OCI 레지스트리](https://oras.land) 를 사용 하 여 아티팩트 그래프를 Azure container Registry로 푸시하고 풀 하는 방법을 보여 줍니다.

oras Artifacts support는 미리 보기 기능이 며 [제한 사항이](#preview-limitations)적용 됩니다. Premium 서비스 계층에서 사용할 수 있는 [영역 중복성](zone-redundancy.md)이 필요 합니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **oras cli** -oras cli를 사용 하면 아티팩트를 푸시, 검색, 사용 하도록 설정 된 Artifacts 레지스트리로 가져올 수 있습니다.
* **Azure CLI** -id를 만들고 리포지토리를 나열 및 삭제 하려면 Azure CLI의 로컬 설치가 필요 합니다. 2.29.1 이상 버전을 권장 합니다. `az --version `을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* **Docker (선택 사항)** -연습을 완료 하려면 컨테이너 이미지를 참조 합니다. 로컬로 설치 된 Docker를 사용 하 여 컨테이너 이미지를 빌드 및 푸시 하거나 기존 컨테이너 이미지를 참조할 수 있습니다. Docker는 모든 [macOS][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 Docker를 쉽게 구성할 수 있는 패키지를 제공합니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

oras Artifacts 지원은 미국 중부 중부 지역으로 제한 되며 가용성 영역을 지원 합니다.

* 지역에서 복제 된 레지스트리는 참조 된 아티팩트를 다른 지역에 복제 하지 않습니다. 추가 지역에서 Artifacts를 지 원하는 경우 참조 된 아티팩트가 복제 됩니다.

## <a name="oras-installation"></a>ORAS 설치

운영 체제용 preview ORAS 릴리스를 다운로드 하 여 설치 합니다. 운영 체제에 대 한 파일을 추출 하 고 설치 하는 방법에 대 한 자세한 내용은 [oras 설치 지침][oras-install-docs] 을 참조 하세요 [GitHub][oras-preview-install] .

## <a name="configure-a-private-registry"></a>개인 레지스트리 구성

명령에 쉽게 복사 하 고 붙여 넣을 수 있도록 환경 변수를 구성 합니다. 명령은 [Azure Cloud Shell](https://shell.azure.com/) 에서 실행할 수 있습니다.

```console
ACR_NAME=myregistry
REGISTRY=$ACR_NAME.azurecr.io
REPO=net-monitor
TAG=v1
IMAGE=$REGISTRY/${REPO}:$TAG
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create](/cli/azure/group#az_group_create) 명령을 실행하여 레지스트리에 대한 리소스 그룹을 만듭니다.

```azurecli
az group create --name $ACR_NAME --location southcentralus
```
### <a name="create-oras-artifact-enabled-registry"></a>ORAS 아티팩트 사용 레지스트리 만들기

oras Artifacts에 대 한 미리 보기 지원에는 미국 중부 지역에서 Premium 서비스 계층이 필요한 영역 중복성이 필요 합니다. [az acr create](/cli/azure/acr#az_acr_create) 명령을 실행 하 여 oras Artifacts enabled 레지스트리를 만듭니다. 더 많은 레지스트리 옵션은 `az acr create` 명령을 참조하세요.

```azurecli
az acr create \
  --resource-group $ACR_NAME \
  --name $ACR_NAME \
  --zone-redundancy enabled \
  --sku Premium \
  --output jsonc
```

명령 출력에서 레지스트리의 `zoneRedundancy` 속성을 확인합니다. 사용 하도록 설정 되 면 레지스트리가 영역 중복 되 고, 아티팩트가 사용 하도록 설정 됩니다.

```JSON
{
  [...]
  "zoneRedundancy": "Enabled",
}
```

### <a name="sign-in-with-azure-cli"></a>Azure CLI로 로그인

ID로 Azure CLI에 [로그인](/cli/azure/authenticate-azure-cli)하여 컨테이너 레지스트리에서 아티팩트를 밀어넣고 끌어옵니다.

그런 다음 Azure CLI 명령 [az acr login](/cli/azure/acr#az_acr_login)을 사용하여 레지스트리에 액세스합니다.

```azurecli
az login
az acr login --name $ACR_NAME
```

> [!NOTE]
> `az acr login`은 Docker 클라이언트를 사용하여 `docker.config` 파일에 Azure Active Directory 토큰을 설정합니다. 개별 인증 흐름을 완료하려면 Docker 클라이언트가 설치되고 실행 중이어야 합니다.

## <a name="sign-in-with-oras"></a>ORAS로 로그인

이 섹션에서는 레지스트리에 로그인 하는 옵션을 보여 줍니다. 사용자 환경에 적합한 방법을 선택하세요.

`oras login`을 실행하여 레지스트리로 인증 서비스 주체 자격 증명, 사용자 id 또는 리포지토리 범위 토큰 (미리 보기)과 같은 시나리오에 적합 한 [레지스트리 자격 증명](container-registry-authentication.md) 을 전달할 수 있습니다.

- [개별 AZURE ad id](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) 로 인증 하 여 ad 토큰을 사용 합니다.

  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```

- 비 AD 기반 토큰을 사용 하려면 [리포지토리 범위 토큰](container-registry-repository-scoped-permissions.md) (미리 보기)을 사용 하 여 인증 합니다.

  ```bash
  USER_NAME="oras-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --repository $REPO content/write \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```

- [끌어오기 및 푸시 권한](container-registry-auth-service-principal.md#create-a-service-principal) (acrpush 역할)이 포함 된 Azure Active Directory 서비스 주체를 사용 하 여 인증 합니다.

  ```bash
  SERVICE_PRINCIPAL_NAME="oras-sp"
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpush \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```

### <a name="sign-in-with-oras"></a>ORAS로 로그인

자격 증명을에 제공 `oras login` 합니다.

  ```bash
  oras login $REGISTRY \
    --username $USER_NAME \
    --password $PASSWORD
  ```

Stdin에서 암호를 읽으려면 `--password-stdin`을 사용합니다.

## <a name="push-a-container-image"></a>컨테이너 이미지 푸시

이 예제에서는 아티팩트 그래프를 컨테이너 이미지에 연결 합니다. 컨테이너 이미지를 빌드 및 푸시 하거나 개인 레지스트리에 있는 기존 이미지를 참조 합니다.

```bash
docker build -t $IMAGE https://github.com/wabbit-networks/net-monitor.git#main
docker push $IMAGE
```

## <a name="create-a-sample-signature-to-the-container-image"></a>컨테이너 이미지에 샘플 서명 만들기

```bash
echo '{"artifact": "'${IMAGE}'", "signature": "pat hancock"}' > signature.json
```

### <a name="push-a-signature-to-the-registry-as-a-reference-to-the-container-image"></a>컨테이너 이미지에 대 한 참조로 레지스트리에 서명을 푸시합니다.

ORAS 명령은 저장소에 서명을 푸시하고 매개 변수를 통해 다른 아티팩트를 참조 합니다 `subject` . 는 `--artifact-type` 다양 한 파일 형식을 사용 하는 파일 확장명과 유사 하 게 아티팩트를 구별 하기 위해 제공 합니다. 하나 이상의 파일은 다음을 지정 하 여 푸시할 수 있습니다. `file:mediaType`

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'signature/example' \
    --subject $IMAGE \
    ./signature.json:application/json
```

Oras push에 대 한 자세한 내용은 [oras 설명서][oras-push-docs]를 참조 하십시오.

## <a name="push-a-multi-file-artifact-as-a-reference"></a>다중 파일 아티팩트를 참조로 푸시합니다.

아티팩트에 대 한 몇 가지 설명서 만들기

```bash
echo 'Readme Content' > readme.md
echo 'Detailed Content' > readme-details.md
```

다중 파일 아티팩트를 참조로 푸시합니다.

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'readme/example' \
    --subject $IMAGE \
    ./readme.md:application/markdown \
    ./readme-details.md:application/markdown
```

## <a name="discovering-artifact-references"></a>아티팩트 참조 검색

oras Artifacts 사양은 아티팩트에 대 한 참조를 검색 하는 [참조 페이지 API] [oras 아티팩트] 참조 페이지를 정의 합니다 `subject` . `oras discover`명령은 컨테이너 이미지에 대 한 참조 목록을 표시할 수 있습니다.

를 사용 하 여 `oras discover` 레지스트리에 저장 된 아티팩트의 그래프를 봅니다.

```bash
oras discover -o tree $IMAGE
```

출력에는 아티팩트 그래프의 시작이 표시 됩니다. 여기에서 시그니처와 문서는 컨테이너 이미지의 자식으로 표시 됩니다.

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
└── readme/example
    └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
```

## <a name="creating-a-deep-graphs-of-artifacts"></a>아티팩트의 전체 그래프 만들기

oras Artifacts 사양은 심층 그래프를 사용 하 여 서명 된 sbom (소프트웨어 청구 자료) 및 기타 아티팩트 유형을 사용 하도록 설정 합니다.

### <a name="create-a-sample-sbom"></a>샘플 SBoM 만들기

```bash
echo '{"version": "0.0.0.0", "artifact": "'${IMAGE}'", "contents": "good"}' > sbom.json
```

### <a name="push-a-sample-sbom-to-the-registry"></a>레지스트리에 샘플 SBoM 푸시

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'sbom/example' \
  --subject $IMAGE \
  ./sbom.json:application/json
```

### <a name="sign-the-sbom"></a>SBoM 서명

참조로 푸시된 Artifacts은 일반적으로 제목 아티팩트의 일부로 간주 되는 태그를 포함 하지 않습니다. 다른 아티팩트의 자식인 아티팩트에 서명을 푸시 하려면 `oras discover` 필터링과 함께를 사용 `--artifact-type` 하 여 다이제스트를 찾습니다.

```bash
SBOM_DIGEST=$(oras discover -o json \
                --artifact-type sbom/example \
                $IMAGE | jq -r ".references[0].digest")
```

SBoM의 서명 만들기

```bash
echo '{"artifact": "'$REGISTRY/${REPO}@$SBOM_DIGEST'", "signature": "pat hancock"}' > sbom-signature.json
```

### <a name="push-the-sbom-signature"></a>SBoM 서명 푸시

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'signature/example' \
  --subject $REGISTRY/$REPO@$SBOM_DIGEST \
  ./sbom-signature.json:application/json
```

### <a name="view-the-graph"></a>그래프 보기

```bash
oras discover -o tree $IMAGE
```

다음 출력이 생성됩니다.

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
├── readme/example
│   └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
└── sbom/example
    └── sha256:4280eef9adb632b42cf200e7cd5a822a456a558e4f3142da6b...
        └── signature/example
            └── sha256:a31ab875d37eee1cca68dbb14b2009979d05594d44a075bdd7...
```

## <a name="pull-a-referenced-artifact"></a>참조 된 아티팩트를 가져옵니다.

참조 된 형식을 끌어오는 경우 참조의 다이제스트는 명령을 사용 하 여 검색 됩니다. `oras discover`

```bash
DOC_DIGEST=$(oras discover -o json \
              --artifact-type 'readme/example' \
              $IMAGE | jq -r ".references[0].digest")
```

### <a name="create-a-clean-directory-for-downloading"></a>다운로드를 위한 정리 디렉터리 만들기

```bash
mkdir ./download
```

### <a name="pull-the-docs-into-the-download-directory"></a>문서를 다운로드 디렉터리로 끌어옵니다.
```bash
oras pull -a -o ./download $REGISTRY/$REPO@$DOC_DIGEST
```
### <a name="view-the-docs"></a>문서 보기

```bash
ls ./download
```

## <a name="view-the-repository-and-tag-listing"></a>리포지토리 및 태그 목록 보기

oras Artifacts를 사용 하면 태그를 할당 하지 않고도 아티팩트 그래프를 푸시, 검색, 끌어 복사할 수 있습니다. 이를 통해 태그 목록은 사용자가 생각 하는 아티팩트에 초점을 맞출 수 있습니다 .이는 컨테이너 이미지, 투구 차트 및 기타 아티팩트와 연결 된 서명 및 SBoMs과는 대조적입니다.

### <a name="view-a-list-of-tags"></a>태그 목록 보기

```azurecli
az acr repository show-tags \
  -n $ACR_NAME \
  --repository $REPO \
  -o jsonc
```

### <a name="view-a-list-of-manifests"></a>매니페스트 목록 보기

리포지토리에는 태그가 지정 되 고 태그가 지정 되지 않은 매니페스트 목록이 있을 수 있습니다.

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

컨테이너 이미지 매니페스트에는 `"tags":`

```json
{
  "architecture": "amd64",
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "configMediaType": "application/vnd.docker.container.image.v1+json",
  "createdTime": "2021-11-12T00:18:54.5123449Z",
  "digest": "sha256:a0fc570a245b09ed752c42d600ee3bb5b4f77bbd70d8898780b7ab4...",
  "imageSize": 2814446,
  "lastUpdateTime": "2021-11-12T00:18:54.5123449Z",
  "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
  "os": "linux",
  "tags": [
    "v1"
  ]
}
```

서명이 태그 되지 않지만 `oras.artifact.manifest` 컨테이너 이미지에 대 한 참조로 추적 됩니다.

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2021-11-12T00:19:10.987156Z",
  "digest": "sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcbcc0b0d...",
  "imageSize": 85,
  "lastUpdateTime": "2021-11-12T00:19:10.987156Z",
  "mediaType": "application/vnd.cncf.oras.artifact.manifest.v1+json"
}
```
## <a name="delete-all-artifacts-in-the-graph"></a>그래프의 모든 아티팩트 삭제

oras Artifacts 사양을 지원 하면 루트 아티팩트와 연결 된 아티팩트의 그래프를 삭제할 수 있습니다. [Az acr repository delete][az-acr-repository-delete] 명령을 사용 하 여 sbom의 서명, sbom 및 서명을 삭제 합니다.

```bash
az acr repository delete \
  -n $ACR_NAME \
  -t ${REPO}:$TAG -y
```

### <a name="view-the-remaining-manifests"></a>나머지 매니페스트 보기

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

## <a name="next-steps"></a>다음 단계

* [ORAS CLI](https://oras.land) 에 대 한 자세한 정보
* 제공 체인 아티팩트 그래프를 푸시, 검색, 끌어오기, 복사 하는 방법에 대 한 [Artifacts][oras-artifacts] 에 대해 자세히 알아보세요.

<!-- LINKS - external -->
[docker-linux]:         https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]:           https://docs.docker.com/docker-for-mac/
[docker-windows]:       https://docs.docker.com/docker-for-windows/
[oras-install-docs]:    https://oras.land/cli/
[oras-preview-install]: https://github.com/oras-project/oras/releases/tag/v0.2.1-alpha.1
[oras-push-docs]:       https://oras.land/cli/1_pushing/
[oras-artifacts]:       https://github.com/oras-project/artifacts-spec/
<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
