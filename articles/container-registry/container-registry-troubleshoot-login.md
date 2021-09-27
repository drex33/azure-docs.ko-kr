---
title: 레지스트리에 로그인 문제 해결
description: Azure Container Registry에 로그인할 때 발생하는 일반적인 문제에 대한 증상, 원인 및 해결 방법
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 48b3069911b16fce23017d562d0b99fbd8f0268c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733013"
---
# <a name="troubleshoot-registry-login"></a>레지스트리 로그인 문제 해결

이 문서는 Azure Container Registry에 로그인할 때 발생할 수 있는 문제를 해결하는 데 도움이 됩니다. 

## <a name="symptoms"></a>증상

다음 중 하나 이상을 포함할 수 있습니다.

* `docker login`, `az acr login` 또는 둘 다를 사용하여 레지스트리에 로그인할 수 없습니다.
* 레지스트리에 로그인할 수 없으며 `unauthorized: authentication required` 또는 `unauthorized: Application not registered with AAD` 오류가 발생합니다.
* 레지스트리에 로그인할 수 없으며 Azure CLI 오류 `Could not connect to the registry login server`가 표시됩니다.
* 이미지를 푸시하거나 끌어올 수 없으며 Docker 오류 `unauthorized: authentication required`가 표시됩니다.
* Azure Kubernetes Service, Azure DevOps 또는 다른 Azure 서비스에서 레지스트리에 액세스할 수 없습니다.
* 레지스트리에 액세스할 수 없고 `Error response from daemon: login attempt failed with status: 403 Forbidden` 오류가 표시됩니다. - [레지스트리로 네트워크 문제 해결](container-registry-troubleshoot-access.md) 참조
* Azure Portal 또는 Azure CLI를 사용하여 레지스트리 설정을 확인하거나 볼 수 없습니다.

## <a name="causes"></a>원인

* Docker가 사용자 환경에 제대로 구성되지 않았습니다. - [솔루션](#check-docker-configuration)
* 레지스트리가 없거나 이름이 올바르지 않습니다. - [솔루션](#specify-correct-registry-name)
* 레지스트리 자격 증명이 유효하지 않습니다. - [솔루션](#confirm-credentials-to-access-registry)
* 푸시, 끌어오기 또는 Azure Resource Manager 작업에 대한 자격 증명이 승인되지 않았습니다. - [솔루션](#confirm-credentials-are-authorized-to-access-registry)
* 자격 증명이 만료되었습니다. - [솔루션](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>추가 진단 

[az acr check-health](/cli/azure/acr#az_acr_check_health) 명령을 실행하여 레지스트리 환경의 상태에 대한 자세한 정보를 얻고 선택적으로 대상 레지스트리에 액세스합니다. 예를 들어 Docker 구성 오류 또는 Azure Active Directory 로그인 문제를 진단합니다. 

명령 예는 [Azure Container Registry 상태 확인](container-registry-check-health.md)을 참조하세요. 오류가 보고되면 [오류 참조](container-registry-health-error-reference.md) 및 다음 섹션에서 권장 솔루션을 검토하세요.

Azure Kubernetes Service에서 레지스트리를 사용하는 데 문제가 있는 경우 [az aks check-acr](/cli/azure/aks#az_aks_check_acr) 명령을 실행하여 AKS 클러스터에서 레지스트리에 액세스할 수 있는지 확인합니다.

> [!NOTE]
> 일부 인증 또는 권한 부여 오류는 레지스트리 액세스를 방해하는 방화벽 또는 네트워크 구성이 있는 경우에도 발생할 수 있습니다. [레지스트리로 네트워크 문제 해결](container-registry-troubleshoot-access.md)을 참조하세요.

## <a name="potential-solutions"></a>잠재적 해결 방법

### <a name="check-docker-configuration"></a>Docker 구성 확인

대부분의 Azure Container Registry 인증 흐름에는 로컬 Docker 설치가 필요하므로 이미지 푸시 및 끌어오기 등의 작업을 위해 레지스트리를 사용하여 인증할 수 있습니다. 사용자 환경에서 Docker CLI 클라이언트 및 디먼(Docker 엔진)이 실행 중인지 확인합니다. Docker 클라이언트 버전 18.03 이상이 필요합니다.

관련 링크:

* [인증 개요](container-registry-authentication.md#authentication-options)
* [컨테이너 레지스트리 FAQ](container-registry-faq.yml)

### <a name="specify-correct-registry-name"></a>올바른 레지스트리 이름 지정

`docker login`을 사용하는 경우 레지스트리의 전체 로그인 서버 이름(예: *myregistry.azurecr.io*)을 제공합니다. 소문자만 사용해야 합니다. 예제:

```console
docker login myregistry.azurecr.io
```

Azure Active Directory ID와 함께 [az acr login](/cli/azure/acr#az_acr_login)을 사용하는 경우 먼저 [Azure CLI에 로그인](/cli/azure/authenticate-azure-cli)한 다음 레지스트리의 Azure 리소스 이름을 지정합니다. 리소스 이름은 *myregistry*(도메인 접미사 없음)와 같이 레지스트리가 작성될 때 제공되는 이름입니다. 예제:

```azurecli
az acr login --name myregistry
```

관련 링크:

* [az acr login이 성공했지만 '권한 없음: 인증 필요' 오류로 인해 docker 명령이 실패함](container-registry-faq.yml#az-acr-login-succeeds-but-docker-fails-with-error--unauthorized--authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>레지스트리에 액세스할 자격 증명 확인

시나리오에 사용하는 자격 증명의 유효성을 검사하거나 레지스트리 소유자가 제공한 자격 증명의 유효성을 검사합니다. 가능한 문제는 다음과 같습니다.

* Active Directory 서비스 주체를 사용하는 경우 Active Directory 테넌트에서 올바른 자격 증명을 사용해야 합니다.
  * 사용자 이름 - 서비스 주체 애플리케이션 ID(*클라이언트 ID* 라고도 함)
  * 암호 - 서비스 주체 암호(*클라이언트 암호* 라고도 함)
* Azure Kubernetes Service 또는 Azure DevOps와 같은 Azure 서비스를 사용하여 레지스트리에 액세스하는 경우 서비스에 대한 레지스트리 구성을 확인합니다. 
* Docker 디먼을 사용하지 않고 레지스트리 로그인을 사용하도록 설정하는 `--expose-token` 옵션과 함께 `az acr login`을 실행한 경우 사용자 이름 `00000000-0000-0000-0000-000000000000`으로 인증해야 합니다.
* 레지스트리가 [익명 끌어오기 액세스](container-registry-faq.yml#how-do-i-enable-anonymous-pull-access-)로 구성된 경우 이전 Docker 로그인에서 저장된 기존 Docker 자격 증명은 익명 액세스를 차단할 수 있습니다. 레지스트리에서 익명 끌어오기 작업을 시도하기 전에 `docker logout`을 실행합니다.

관련 링크:

* [인증 개요](container-registry-authentication.md#authentication-options)
* [Azure AD로 개별 로그인](container-registry-authentication.md#individual-login-with-azure-ad)
* [서비스 주체로 로그인](container-registry-auth-service-principal.md)
* [관리 ID로 로그인](container-registry-authentication-managed-identity.md)
* [리포지토리 범위 토큰으로 로그인](container-registry-repository-scoped-permissions.md)
* [관리자 계정으로 로그인](container-registry-authentication.md#admin-account)
* [Azure AD 인증 및 권한 부여 오류 코드](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az_acr_login) 참조

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>레지스트리에 액세스할 수 있도록 자격 증명에 권한이 있는지 확인

자격 증명과 연결된 레지스트리 권한(예: 레지스트리에서 이미지를 끌어오는 `AcrPull` Azure 역할 또는 이미지를 푸시하는 `AcrPush` 역할)을 확인합니다. 

Azure CLI를 사용하여 포털 또는 레지스트리 관리에서 레지스트리에 액세스하려면 Azure Resource Manager 작업을 수행할 최소한 `Reader` 역할 또는 이와 동등한 권한이 필요합니다.

포털을 통해 레지스트리 액세스를 허용하도록 사용 권한이 최근에 변경된 경우 오래된 브라우저 캐시 또는 쿠키를 방지하려면 브라우저에서 incognito 또는 프라이빗 세션을 시도해야 할 수 있습니다.

사용자 또는 레지스트리 소유자는 구독에서 역할 할당을 추가하거나 제거할 수 있는 권한을 보유해야 합니다.

관련 링크:

* [Azure 역할 및 권한 - Azure Container Registry](container-registry-roles.md)
* [리포지토리 범위 토큰으로 로그인](container-registry-repository-scoped-permissions.md)
* [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-portal.md)
* [포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)
* [새 애플리케이션 비밀 만들기](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD 인증 및 권한 부여 코드](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>자격 증명이 만료되지 않았는지 확인

토큰 및 Active Directory 자격 증명은 정의된 기간 후에 만료되어 레지스트리 액세스를 방지합니다. 액세스를 사용하도록 설정하려면 자격 증명을 다시 설정하거나 다시 생성해야 할 수 있습니다.

* 레지스트리 로그인을 위해 개인 AD ID, 관리 ID 또는 서비스 주체를 사용하는 경우 AD 토큰은 3시간 후에 만료됩니다. 레지스트리에 다시 로그인합니다.  
* 만료된 클라이언트 암호로 AD 서비스 주체를 사용하는 경우 구독 소유자 또는 계정 관리자는 자격 증명을 다시 설정하거나 새 서비스 주체를 생성해야 합니다.
* [리포지토리 범위 토큰](container-registry-repository-scoped-permissions.md)을 사용하는 경우 레지스트리 소유자가 암호를 다시 설정하거나 새 토큰을 생성해야 할 수 있습니다.

관련 링크:

* [서비스 주체 자격 증명 다시 설정](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [토큰 암호 다시 생성](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Azure AD로 개별 로그인](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>고급 문제 해결

레지스트리에서 [리소스 로그 컬렉션](monitor-service.md) 을 사용 하는 경우 ContainerRegistryLoginEvents 로그를 검토 합니다. 이 로그는 들어오는 ID와 IP 주소를 포함하여 인증 이벤트와 상태를 저장합니다. 로그에서 [레지스트리 인증 실패](monitor-service.md#registry-authentication-failures)를 쿼리합니다. 

관련 링크:

* [진단 평가 및 감사를 위한 로그](./monitor-service.md)
* [컨테이너 레지스트리 FAQ](container-registry-faq.yml)
* [Azure Container Registry의 모범 사례](container-registry-best-practices.md)

## <a name="next-steps"></a>다음 단계

여기에서 문제를 해결하지 않으면 다음 옵션을 참조하세요.

* 기타 레지스트리 문제 해결 항목은 다음과 같습니다.
  * [레지스트리 관련 네트워크 문제 해결](container-registry-troubleshoot-access.md)
  * [쿼리 성능 문제 해결](container-registry-troubleshoot-performance.md)
* [커뮤니티 지원](https://azure.microsoft.com/support/community/) 옵션
* [Microsoft Q&A](/answers/products/)
* [지원 티켓 열기](https://azure.microsoft.com/support/create-ticket/) - 제공한 정보에 따라 레지스트리의 인증 실패에 대한 빠른 진단이 실행될 수 있습니다.
