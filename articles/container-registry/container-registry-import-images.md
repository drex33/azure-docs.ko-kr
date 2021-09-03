---
title: 컨테이너 이미지 가져오기
description: Docker 명령을 실행하지 않고도 Azure API를 사용하여 컨테이너 이미지를 Azure Container Registry로 가져옵니다.
ms.topic: article
ms.date: 05/28/2021
ms.openlocfilehash: 04e9ead09061fad5630b883c6f5749bafc7a4a7a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529001"
---
# <a name="import-container-images-to-a-container-registry"></a>컨테이너 이미지를 컨테이너 레지스트리로 가져오기

Docker 명령을 사용하지 않고 컨테이너 이미지를 Azure Container Registry로 쉽게 가져올(복사) 수 있습니다. 예를 들어 개발 레지스트리에서 프로덕션 레지스트리로 이미지를 가져오거나 공개 레지스트리에서 기본 이미지를 복사합니다.

Azure Container Registry는 기존 레지스트리에서 이미지 및 기타 아티팩트를 복사하는 많은 일반적인 시나리오를 처리합니다.

* 퍼블릭 레지스트리에서 이미지 가져오기

* 같거나 다른 Azure 구독 또는 테넌트의 다른 Azure Container Registry에서 Helm 3 차트를 비롯한 이미지나 OCI 아티팩트 가져오기

* 비 Azure 프라이빗 컨테이너 레지스트리에서 가져오기

Docker CLI 명령을 사용하는 대신 Azure Container Registry로 이미지를 가져오는 경우 다음과 같은 이점이 있습니다.

* 클라이언트 환경에는 로컬 Docker 설치가 필요하지 않으므로 지원되는 OS 유형과 관계없이 컨테이너 이미지를 가져옵니다.

* 다중 아키텍처 이미지(예: 공식 Docker 이미지)를 가져오는 경우 매니페스트 목록에 지정된 모든 아키텍처 및 플랫폼의 이미지가 복사됩니다.

* 대상 레지스트리에 액세스하기 위해 레지스트리의 퍼블릭 엔드포인트를 사용할 필요가 없습니다.

컨테이너 이미지를 가져오기 위해 이 문서에서는 Azure Cloud Shell이나 로컬로 Azure CLI를 실행하도록 요구합니다(버전 2.0.55 이상 권장). `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

> [!IMPORTANT]
> 두 Azure Container Registry 간의 이미지 가져오기에 대한 변경 내용은 2021년 1월에 도입되었습니다.
> * 네트워크 제한 Azure Container Registry에서 또는 해당 레지스트리로 가져오려면 [**신뢰할 수 있는 서비스에 의한 액세스를 허용**](allow-access-trusted-services.md)하여 네트워크를 우회하기 위해 제한된 레지스트리가 필요합니다. 기본적으로 이 설정은 사용하도록 설정되어 있으므로 가져오기를 허용합니다. 프라이빗 엔드포인트를 사용하거나 레지스트리 방화벽 규칙을 사용하여 새로 만든 레지스트리에서 해당 설정을 사용하도록 설정하지 않은 경우 가져오기가 실패합니다. 
> * 가져오기 원본 또는 대상으로 사용되는 기존 네트워크 제한 Azure Container Registry에서 이 네트워크 보안 기능을 사용하도록 설정하는 것은 선택 사항이지만 설정하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 요건

Azure Container Registry가 아직 없는 경우 레지스트리를 만듭니다. 단계는 [빠른 시작: Azure CLI를 사용하여 프라이빗 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요.

이미지를 Azure Container Registry로 가져오려면 ID에 대상 레지스트리에 대한 쓰기 권한이 있어야 합니다(적어도 Contributor 역할이나 importImage 작업을 허용하는 사용자 지정 역할). [Azure Container Registry 역할 및 권한](container-registry-roles.md#custom-roles)을 참조하세요. 

## <a name="import-from-a-public-registry"></a>공개 레지스트리에서 가져오기

### <a name="import-from-docker-hub"></a>Docker 허브에서 가져오기

예를 들어 [az acr import][az-acr-import] 명령을 사용하여 Docker 허브에서 *myregistry* 레지스트리로 다중 아키텍처 `hello-world:latest` 이미지를 가져옵니다. `hello-world`는 Docker 허브의 공식 이미지이므로 이 이미지는 기본 `library` 리포지토리에 있습니다. `--source` 이미지 매개 변수의 값에 리포지토리 이름 및 선택적으로 태그를 포함합니다. 태그 대신 해당 매니페스트 다이제스트를 통해 이미지를 선택적으로 식별할 수 있으며, 이 경우 이미지의 특정 버전이 보장됩니다.
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

`az acr repository show-manifests` 명령을 실행하여 이 이미지에 여러 개의 매니페스트가 연결된 것을 확인할 수 있습니다.

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

[Docker Hub 계정](https://www.docker.com/pricing)이 있으면 Docker Hub에서 이미지를 가져올 때 자격 증명을 사용하는 것이 좋습니다. Docker Hub 사용자 이름과 암호 또는 [개인용 액세스 토큰](https://docs.docker.com/docker-hub/access-tokens/)을 `az acr import`에 매개 변수로 전달합니다. 다음 예제에서는 Docker Hub 자격 증명을 사용하여 Docker Hub의 `tensorflow` 리포지토리에서 퍼블릭 이미지를 가져옵니다.

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Microsoft Container Registry에서 가져오기

예를 들어 Microsoft Container Registry의 `windows` 리포지토리에서 `ltsc2019` Windows Server Core 이미지를 가져옵니다.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>동일한 AD 테넌트의 Azure Container Registry에서 가져오기

통합 Azure Active Directory 권한을 사용하여 동일한 AD 테넌트의 Azure Container Registry에서 이미지를 가져올 수 있습니다.

* 원본 레지스트리에서 읽고(Reader 역할) 대상 레지스트리로 가져올 수 있는(contributor 역할) Azure Active Directory 권한이 ID에 있어야 합니다(Contributor 역할 또는 importImage 작업을 허용하는 [사용자 지정 역할](container-registry-roles.md#custom-roles)).

* 레지스트리는 동일한 Active Directory 테넌트의 다른 Azure 구독이나 동일한 Azure 구독에 있을 수 있습니다.

* 원본 레지스트리에 대한 [퍼블릭 액세스](container-registry-access-selected-networks.md#disable-public-network-access)를 사용하지 않도록 설정할 수 있습니다. 퍼블릭 액세스를 사용하지 않도록 설정한 경우 레지스트리 로그인 서버 이름 대신 리소스 ID로 원본 레지스트리를 지정합니다.

* 원본 레지스트리 및/또는 대상 레지스트리가 프라이빗 엔드포인트를 포함하거나 레지스트리 방화벽 규칙이 적용되는 경우 네트워크에 액세스할 수 있도록 제한된 레지스트리에서 [신뢰할 수 있는 서비스 허용](allow-access-trusted-services.md)해야 합니다.

### <a name="import-from-a-registry-in-the-same-subscription"></a>동일한 구독의 레지스트리에서 가져오기

예를 들어 원본 레지스트리 *mysourceregistry* 에서 동일한 Azure 구독의 *myregistry* 로 `aci-helloworld:latest` 이미지를 가져옵니다.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

다음 예제에서는 레지스트리의 퍼블릭 엔드포인트에 대한 액세스를 사용하지 않도록 설정한 원본 레지스트리 *mysourceregistry* 에서 *myregistry* 로 `aci-helloworld:latest` 이미지를 가져옵니다. `--registry` 매개 변수를 사용하여 원본 레지스트리의 리소스 ID를 제공합니다. `--source` 매개 변수는 원본 리포지토리 및 태그만 지정하고, 레지스트리 로그인 서버 이름은 지정하지 않습니다.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

다음 예제에서는 태그 대신 매니페스트 다이제스트(`sha256:...`으로 표시되는 SHA-256 해시)를 통해 이미지를 가져옵니다.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>다른 구독의 레지스트리에서 가져오기

다음 예제에서 *mysourceregistry* 는 동일한 Active Directory 테넌트의 *myregistry* 에서 다른 구독에 있습니다. `--registry` 매개 변수를 사용하여 원본 레지스트리의 리소스 ID를 제공합니다. `--source` 매개 변수는 원본 리포지토리 및 태그만 지정하고, 레지스트리 로그인 서버 이름은 지정하지 않습니다.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>서비스 주체 자격 증명을 사용하여 레지스트리에서 가져오기

Active Directory 권한을 사용하여 액세스할 수 없는 레지스트리에서 가져오려면 원본 레지스트리에 서비스 주체 자격 증명을 사용할 수 있습니다(사용 가능한 경우). 원본 레지스트리에 대한 ACRPull 액세스 권한이 있는 Active Directory [서비스 주체](container-registry-auth-service-principal.md)의 appID 및 암호를 제공합니다. 서비스 주체를 사용하면 이미지를 레지스트리로 가져와야 하는 빌드 시스템 및 기타 무인 시스템에 도움이 됩니다.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>다른 AD 테넌트의 Azure Container Registry에서 가져오기

다른 Azure Active Directory 테넌트의 Azure Container Registry에서 가져오려면 로그인 서버 이름으로 원본 레지스트리를 지정하고 레지스트리에 대한 풀 액세스를 사용할 수 있는 자격 증명을 제공합니다. 

### <a name="cross-tenant-import-with-username-and-password"></a>사용자 이름 및 암호를 사용하여 교차 테넌트 가져오기
예를 들어 [리포지토리 범위 토큰](container-registry-repository-scoped-permissions.md) 및 암호나 원본 레지스트리에 대한 ACRPull 액세스 권한이 있는 Active Directory [서비스 주체](container-registry-auth-service-principal.md)의 appID 및 암호를 사용합니다. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

### <a name="cross-tenant-import-with-access-token"></a>액세스 토큰을 사용하여 교차 테넌트 가져오기

레지스트리 권한이 있는 원본 테넌트의 ID를 사용하여 원본 레지스트리에 액세스하려면 액세스 토큰을 가져올 수 있습니다.

```azurecli
# Login to Azure CLI with the identity, for example a user-assigned managed identity
az login --identity --username <identity_ID>

# Get access token returned by `az account get-access-token`
az account get-access-token 
```

대상 테넌트에서 액세스 토큰을 `az acr import` 명령에 대한 암호로 전달합니다. 원본 레지스트리는 로그인 서버 이름으로 지정됩니다. 이 명령에는 사용자 이름이 필요하지 않습니다.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --password <access-token>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>비 Azure 프라이빗 컨테이너 레지스트리에서 가져오기

레지스트리에 대한 풀 액세스를 허용하는 자격 증명을 지정하여 비 Azure 프라이빗 레지스트리에서 이미지를 가져옵니다. 예를 들어 프라이빗 Docker 레지스트리에서 이미지를 끌어옵니다. 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 공용 레지스트리 또는 다른 프라이빗 레지스트리에서 Azure Container Registry로 컨테이너 이미지를 가져오는 방법을 알아보았습니다. 

* 추가 이미지 가져오기 옵션은 [az acr import][az-acr-import] 명령 참조를 참조하세요. 

* 이미지 가져오기는 다른 Azure 지역, 구독 또는 Azure AD 테넌트의 컨테이너 레지스트리로 콘텐츠를 이동하는 데 도움이 될 수 있습니다. 자세한 내용은 [수동으로 컨테이너 레지스트리를 다른 지역으로 이동](manual-regional-move.md)을 참조하세요.

* 네트워크 제한 컨테이너 레지스트리에서 [아티팩트 내보내기를 사용하지 않도록 설정](data-loss-prevention.md)하는 방법을 알아보세요.


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
