---
title: 레지스트리 상태 확인
description: 빠른 진단 명령을 사용하여 로컬 Docker 구성 및 레지스트리 연결을 포함한 Azure 컨테이너 레지스트리를 사용할 때 일반적인 문제를 식별하는 방법을 알아봅니다
ms.topic: article
ms.date: 07/14/2021
ms.openlocfilehash: b0a95179e01de63185092c965b3290017dc96dfb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464306"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Azure 컨테이너 레지스트리 상태 확인

Azure 컨테이너 레지스트리를 사용하는 경우 가끔 문제가 발생할 수 있습니다. 예를 들어 로컬 환경의 Docker 문제로 인해 컨테이너 이미지를 끌어오지 못할 수 있습니다. 또는 네트워크 문제로 인해 레지스트리에 연결하지 못할 수 있습니다. 

첫 번째 진단 단계로 [az acr check-health][az-acr-check-health]명령을 실행하여 환경 상태에 대한 정보를 얻고 선택적으로 대상 레지스트리에 액세스합니다. 이 명령은 Azure CLI 2.0.67 버전 이상에서 사용할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

추가 레지스트리 문제 해결 지침은 다음을 참조하세요:
* [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md)
* [레지스트리 관련 네트워크 문제 해결](container-registry-troubleshoot-access.md)
* [쿼리 성능 문제 해결](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>az acr check-health 실행

다음 예제에서는 `az acr check-health` 명령을 실행하는 다른 방법을 알아봅니다.

> [!NOTE]
> Azure Cloud Shell에서 명령을 실행하면 로컬 환경이 확인되지 않습니다. 그러나 대상 레지스트리에 대한 액세스를 확인할 수 있습니다.

### <a name="check-the-environment-only"></a>환경만 확인

로컬 Docker 디먼, CLI 버전 및 Helm 클라이언트 구성을 확인하려면 추가 매개 변수 없이 명령을 실행합니다:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>환경 및 대상 레지스트리 확인

레지스트리에 대한 액세스를 확인하고 로컬 환경을 확인하려면 대상 레지스트리의 이름을 전달합니다. 예를 들면 다음과 같습니다.

```azurecli
az acr check-health --name myregistry
```

### <a name="check-registry-access-in-a-virtual-network"></a>가상 네트워크의 레지스트리 액세스 확인

프라이빗 엔드포인트로 라우팅하는 DNS 설정을 확인하려면 가상 네트워크의 이름이나 리소스 ID를 전달합니다. 가상 네트워크가 레지스트리와 다른 구독이나 리소스 그룹에 있는 경우 리소스 ID가 필요합니다.

```azurecli
az acr check-health --name myregistry --vnet myvnet
```

## <a name="error-reporting"></a>오류 보고

명령은 정보를 표준 출력에 기록합니다. 문제가 감지되면 오류 코드 및 설명을 제공합니다. 코드 및 가능한 해결방법에 대한 자세한 내용은 [오류 참조](container-registry-health-error-reference.md)를 참조하세요.

기본적으로 오류를 발견할 때마다 명령이 중지됩니다. 오류가 발견되더라도 모든 상태 확인에 대한 출력을 제공하도록 명령을 실행할 수도 있습니다. 아래 예제와 같이 `--ignore-errors` 매개 변수를 추가합니다:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry; skip confirmation to pull image
az acr check-health --name myregistry --ignore-errors --yes
```      

샘플 출력:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  

## <a name="next-steps"></a>다음 단계

[az acr check-health][az-acr-check-health] 명령에서 반환하는 오류 코드에 대한 자세한 내용은 [상태 확인 오류 참조](container-registry-health-error-reference.md)를 참조하세요.

Azure Container Registry에 대한 질문과 대답 및 알려진 문제는 [FAQ](container-registry-faq.yml)를 참조하세요.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
