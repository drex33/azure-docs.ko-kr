---
title: 서비스 주체를 사용하여 인증
description: Azure Active Directory 서비스 주체를 사용하여 프라이빗 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공합니다.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 117929df4c8b0fa7a54a23bcc039294122cd5afa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651886"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>서비스 주체로 Azure Container Registry 인증

Azure AD(Azure Active Directory) 서비스 주체를 사용하여 컨테이너 레지스트리에 대한 푸시, 풀 또는 기타 액세스를 제공할 수 있습니다. 서비스 주체를 사용하면 "헤드리스" 서비스 및 애플리케이션에 대한 액세스를 제공할 수 있습니다.

## <a name="what-is-a-service-principal"></a>서비스 주체란?

Azure AD [*서비스 주체는*](../active-directory/develop/app-objects-and-service-principals.md) 구독 내에서 Azure 리소스에 대한 액세스를 제공합니다. 서비스 주체는 서비스에 대한 사용자 ID라고 생각할 수 있습니다. 여기서 "서비스"는 리소스에 액세스 권한이 필요한 애플리케이션, 서비스 또는 플랫폼입니다. 지정한 리소스에 대해서만 액세스 권한이 있는 서비스 주체를 구성할 수 있습니다. 그런 다음, 서비스 주체의 자격 증명을 사용하여 해당 리소스에 액세스하도록 애플리케이션이나 서비스를 구성합니다.

Azure Container Registry 컨텍스트에서, Azure의 프라이빗 레지스트리에 대한 풀, 푸시 및 풀 또는 기타 권한이 있는 Azure AD 서비스 주체를 만들 수 있습니다. 전체 목록에 대해서는 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.

## <a name="why-use-a-service-principal"></a>서비스 주체를 사용하는 이유

Azure AD 서비스 주체를 사용하면 프라이빗 컨테이너 레지스트리에 대해 범위가 지정된 액세스를 제공할 수 있습니다. 애플리케이션이나 서비스마다 레지스트리에 대한 맞춤 액세스 권한이 있는 여러 서비스 주체를 만듭니다. 또한 서비스와 애플리케이션 사이에 자격 증명을 공유하지 않아도 되기 때문에, 선택한 서비스 주체(및 애플리케이션)에 대해서만 자격 증명을 순환하거나 액세스 권한을 철회할 수 있습니다.

예를 들어, 웹 애플리케이션은 이미지 `pull` 액세스만 제공하는 서비스 주체를 사용하도록 구성하고, 빌드 시스템은 `push` 및 `pull` 액세스를 모두 제공하는 서비스 주체를 사용합니다. 애플리케이션 개발 팀 인력에 변화가 있으면, 빌드 시스템에 영향을 주지 않으면서 서비스 주체 자격 증명을 순환할 수 있습니다.

## <a name="when-to-use-a-service-principal"></a>서비스 주체를 사용하는 경우

**헤드리스 시나리오** 에서 레지스트리 액세스를 제공하려면 서비스 주체를 사용해야 합니다. 즉, 자동 또는 무인 방식으로 컨테이너 이미지를 푸시하거나 풀해야 하는 모든 애플리케이션, 서비스 또는 스크립트입니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

  * *풀*: 레지스트리에서 Kubernetes, DC/OS 및 Docker Swarm을 포함한 오케스트레이션 시스템으로 컨테이너를 배포합니다. 컨테이너 레지스트리에서 관련 Azure 서비스(예: [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) 등)로 가져올 수도 있습니다.

    > [!TIP]
    > Azure 컨테이너 레지스트리에서 이미지를 끌어오려면 여러 [Kubernetes 시나리오에서](authenticate-kubernetes-options.md) 서비스 주체를 권장합니다. AKS(Azure Kubernetes Service)를 사용하면 자동화된 메커니즘을 사용해 클러스터의 [관리 ID](../aks/cluster-container-registry-integration.md)를 사용하도록 설정하여 대상 레지스트리를 인증할 수도 있습니다. 
  * *푸시*: 컨테이너 이미지를 빌드하고 Azure Pipelines 또는 Jenkins와 같은 연속 통합 및 배포 솔루션을 사용하여 레지스트리에 푸시합니다.

레지스트리에 개별 액세스하는 경우, 예를 들어 컨테이너 이미지를 개발 워크스테이션에 수동으로 풀하는 경우, 레지스트리 액세스를 위해 [Azure AD ID](container-registry-authentication.md#individual-login-with-azure-ad)(예: [az acr login][az-acr-login])를 사용하는 것이 좋습니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>샘플 스크립트

GitHub에서 Azure CLI에 대한 이전 샘플 스크립트 및 Azure PowerShell에 대한 버전을 찾을 수 있습니다.

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>서비스 주체를 사용한 인증

컨테이너 레지스트리에 대한 액세스 권한이 부여된 서비스 주체가 있으면 "헤드리스" 서비스 및 애플리케이션에 액세스하기 위한 자격 증명을 구성하거나 `docker login` 명령을 사용하여 해당 자격 증명을 입력할 수 있습니다. 다음 값을 사용합니다.

* **사용자 이름** - 서비스 주체의 **애플리케이션(클라이언트) ID**
* **암호** - 서비스 주체의 **암호(클라이언트 암호)**

각 값의 형식은 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다. 

> [!TIP]
> [az ad sp credential reset](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset) 명령을 실행하여 서비스 주체의 암호(클라이언트 암호)를 다시 생성할 수 있습니다.
>

### <a name="use-credentials-with-azure-services"></a>Azure 서비스에서 자격 증명 사용

Azure Container Registry로 인증할 수 있는 모든 Azure 서비스의 서비스 주체 자격 증명을 사용할 수 있습니다.  다양한 시나리오에 대해 레지스트리의 관리자 자격 증명 대신 서비스 주체 자격 증명을 사용합니다.

예를 들어 자격 증명을 사용하여 Azure Container Registry에서 [Azure Container Instances](container-registry-auth-aci.md)로 이미지를 풀합니다.

### <a name="use-with-docker-login"></a>Docker 로그인과 함께 사용

서비스 주체를 사용하여 `docker login`을 실행할 수 있습니다. 다음 예제에서 서비스 주체 애플리케이션 ID는 환경 변수 `$SP_APP_ID`로 전달되고 암호는 변수 `$SP_PASSWD`로 전달됩니다. Docker 자격 증명을 관리하는 권장 방법은 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 확인하세요.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

로그인하면 Docker는 자격 증명을 캐시합니다.

### <a name="use-with-certificate"></a>인증서와 함께 사용

서비스 주체에 인증서를 추가한 경우 인증서 기반 인증을 사용하여 Azure CLI에 로그인한 다음, [az acr login][az-acr-login] 명령을 사용하여 레지스트리에 액세스할 수 있습니다. CLI를 사용할 때 암호 대신 인증서를 비밀로 사용하면 추가 보안이 제공됩니다. 

[서비스 주체를 만들 때](/cli/azure/create-an-azure-service-principal-azure-cli) 자체 서명된 인증서를 만들 수 있습니다. 또는 기존 서비스 주체에 인증서를 하나 이상 추가합니다. 예를 들어 이 문서의 스크립트 중 하나를 사용하여 레지스트리에서 이미지를 풀하거나 푸시할 수 있는 권한이 있는 서비스 주체를 만들거나 업데이트하는 경우 [az ad sp credential reset][az-ad-sp-credential-reset] 명령을 사용하여 인증서를 추가합니다.

인증서와 함께 서비스 주체를 사용하여 [Azure CLI에 로그인](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)하려면 인증서가 PEM 형식이어야 하며 프라이빗 키를 포함해야 합니다. 인증서가 필수 형식이 아닌 경우 `openssl`과 같은 도구를 사용하여 변환합니다. [az login][az-login]을 실행하여 서비스 주체를 사용해 CLI에 로그인하는 경우 서비스 주체의 애플리케이션 ID와 Active Directory 테넌트 ID도 제공합니다. 다음 예제에서는 이러한 값을 환경 변수로 보여줍니다.

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

그런 다음, [az acr login][az-acr-login]을 실행하여 레지스트리를 사용해 인증합니다.

```azurecli
az acr login --name myregistry
```

CLI는 레지스트리로 세션을 인증하기 위해 `az login`을 실행할 때 생성된 토큰을 사용합니다.

## <a name="create-service-principal-for-cross-tenant-scenarios"></a>교차 테넌트 시나리오에 대한 서비스 주체 만들기

한 Azure Active Directory(테넌트)의 컨테이너 레지스트리에서 다른 Azure Active Directory 서비스 또는 앱으로 이미지를 끌어와야 하는 Azure 시나리오에서도 서비스 주체를 사용할 수 있습니다. 예를 들어 조직은 테넌트 B의 공유 컨테이너 레지스트리에서 이미지를 가져와야 하는 테넌트 A의 앱을 실행할 수 있습니다.

테넌트 간 시나리오에서 컨테이너 레지스트리로 인증할 수 있는 서비스 주체를 만들려면 다음을 수행합니다.

*  테넌트 A에서 [다중 테넌트 앱](../active-directory/develop/single-and-multi-tenant-apps.md)(서비스 주체)을 만듭니다. 
* 테넌트 B에서 앱 프로비전
* 테넌트 B의 레지스트리에서 끌어올 수 있는 서비스 주체 권한 부여
* 새 서비스 주체를 사용하여 인증하도록 테넌트 A에서 서비스 또는 앱 업데이트

예제 단계는 [컨테이너 레지스트리에서 다른 AD 테넌트의 AKS 클러스터로 이미지 끌어오기](authenticate-aks-cross-tenant.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Container Registry로 인증하는 다른 시나리오에 대한 [인증 개요](container-registry-authentication.md)를 참조하세요.

* Azure Key Vault를 사용하여 컨테이너 레지스트리에 대한 서비스 주체 자격 증명을 저장하고 검색하는 예제는 [ACR 작업 사용하여 컨테이너 이미지 빌드 및 배포](container-registry-tutorial-quick-task.md) 자습서를 참조하세요.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-login]: /cli/azure/reference-index#az_login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
