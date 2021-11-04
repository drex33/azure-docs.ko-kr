---
title: 레지스트리 인증 옵션
description: Azure Active Directory ID, 서비스 주체, 선택적 관리자 자격 증명 등을 사용한 로그인을 포함하여 프라이빗 Azure Container Registry의 인증 옵션
ms.topic: article
ms.date: 06/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7c6510247ff6900145241834841964271db6530b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578966"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Azure Container Registry로 인증

Azure Container Registry로 인증하는 방법은 여러 가지가 있으며 각 방법을 하나 이상의 레지스트리 사용 시나리오에 적용할 수 있습니다.

권장되는 방법은 다음과 같습니다.

* [개별 로그인](#individual-login-with-azure-ad)을 통해 직접 레지스트리에 인증
* 애플리케이션 및 컨테이너 오케스트레이터는 Azure AD(Azure Active Directory) [서비스 주체](#service-principal)를 사용하여 무인 또는 "헤드리스" 인증을 수행할 수 있습니다.

AKS(Azure Kubernetes Service) 또는 다른 Kubernetes 클러스터와 함께 컨테이너 레지스트리를 사용하는 경우 [Kubernetes의 Azure Container Registry 사용하여 인증하는 시나리오](authenticate-kubernetes-options.md)를 참조하세요.

## <a name="authentication-options"></a>인증 옵션

다음 표에는 사용 가능한 인증 방법과 일반적인 시나리오가 나와 있습니다. 자세한 내용은 연결된 콘텐츠를 참조하세요.

| 메서드                               | 인증 방법                                           | 시나리오                                                            | Azure RBAC(Azure 역할 기반 액세스 제어)                             | 제한 사항                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [개별 AD ID](#individual-login-with-azure-ad)                | `az acr login` (Azure CLI)<br/><br/> Azure PowerShell의 `Connect-AzContainerRegistry`                             | 개발자, 테스터의 대화형 푸시/풀                                    | 예                              | AD 토큰을 3시간마다 갱신해야 합니다.     |
| [AD 서비스 주체](#service-principal)                  | `docker login`<br/><br/>`az acr login`(Azure CLI)<br/><br/> Azure PowerShell의 `Connect-AzContainerRegistry`<br/><br/> API 또는 도구의 레지스트리 로그인 설정<br/><br/> [Kubernetes 풀 비밀](container-registry-auth-kubernetes.md)                                           | CI/CD 파이프라인에서 무인 푸시<br/><br/> Azure 또는 외부 서비스에 무인 풀  | 예                              | SP 암호 기본 만료는 1년입니다.       |
| [Azure 리소스용 관리 서비스 ID](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` (Azure CLI)<br/><br/> Azure PowerShell의 `Connect-AzContainerRegistry`                                       | Azure CI/CD 파이프라인에서 무인 푸시<br/><br/> Azure 서비스로 무인 풀<br/><br/>   | 예                              | [Azure 리소스의 관리 ID를 지원하는](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) 일부 Azure 서비스에서만 사용              |
| [AKS 클러스터 관리 ID](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | AKS 클러스터를 만들거나 업데이트할 때 레지스트리 연결  | 동일하거나 다른 구독에서 AKS 클러스터로 무인 끌어오기                                                 | 아니요, 풀 액세스만             | AKS 클러스터에서만 사용 가능<br/><br/>교차 테 넌 트 인증에 사용할 수 없습니다.            |
| [AKS 클러스터 서비스 주체](authenticate-aks-cross-tenant.md)                    | AKS 클러스터를 만들거나 업데이트 시 활성화  | 다른 AD 테넌트의 레지스트리에서 AKS 클러스터로 무인 끌어오기                                                  | 아니요, 풀 액세스만             | AKS 클러스터에서만 사용 가능            |
| [관리 사용자](#admin-account)                            | `docker login`                                          | 개인 개발자 또는 테스터의 대화형 푸시/풀<br/><br/>레지스트리에서 Azure App Service 또는 Azure Container Instances로 이미지 포털 배포                      | 아니요, 항상 풀 및 푸시 액세스  | 레지스트리당 단일 계정, 여러 사용자에는 권장되지 않음         |
| [리포지토리 범위 액세스 토큰](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`(Azure CLI)<br/><br/> Azure PowerShell의 `Connect-AzContainerRegistry`<br/><br/> [Kubernetes 풀 비밀](container-registry-auth-kubernetes.md)    | 개인 개발자 또는 테스터의 리포지토리로 대화형 푸시/풀<br/><br/> 개별 시스템 또는 외부 디바이스별 리포지토리에서 무인 끌어오기                  | 예                              | 현재 AD ID와 통합되어 있지 않음  |

## <a name="individual-login-with-azure-ad"></a>Azure AD로 개별 로그인

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

개발 워크스테이션에서 만든 레지스트리로 이미지 풀 및 푸시와 같이 직접 레지스트리를 사용하여 작업할 때 개별 Azure ID를 사용하여 인증합니다. [az login](/cli/azure/reference-index#az_login)을 사용하여 [Azure CLI](/cli/azure/install-azure-cli)에 로그인한 다음 [az acr login](/cli/azure/acr#az_acr_login) 명령을 실행합니다.

```azurecli
az login
az acr login --name <acrName>
```

`az acr login`을 사용하여 로그인하는 경우 CLI는 `az login`을 실행할 때 만든 토큰을 사용하여 원활하게 레지스트리로 세션을 인증합니다. 인증 흐름을 완료하려면 환경에서 Docker CLI 및 Docker 디먼이 설치되어 실행되고 있어야 합니다. `az acr login`은 Docker 클라이언트를 사용하여 `docker.config` 파일에 Azure Active Directory 토큰을 설정합니다. 이러한 방식으로 로그인하고 나면 자격 증명이 캐시되고 세션의 후속 `docker` 명령에 사용자 이름 또는 암호가 필요하지 않습니다.

> [!TIP]
> 또한 [OCI 아티팩트](container-registry-oci-artifacts.md)와 같은 Docker 이미지 외의 아티팩트를 레지스트리로 푸시 또는 풀하려면 `az acr login`을 사용하여 개별 ID를 인증합니다.

레지스트리 액세스의 경우 `az acr login`에 사용되는 토큰은 **3시간** 동안 유효하므로 항상 `docker` 명령을 실행하기 전에 레지스트리에 로그인하는 것이 좋습니다. 토큰이 만료될 경우 다시 `az acr login` 명령을 사용하여 토큰을 새로 고친 후 다시 인증합니다.

Azure ID와 함께 `az acr login`을 사용하면 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)가 제공됩니다. 일부 시나리오의 경우 Azure AD의 개별 ID를 사용하여 레지스트리에 로그인하거나 특정 [Azure 역할 및 사용 권한](container-registry-roles.md)으로 다른 Azure 사용자를 구성할 수 있습니다. 교차 서비스 시나리오의 경우나 개별 액세스를 관리하지 않으려는 작업 그룹 또는 개발 워크플로의 요구 사항을 처리해야 하는 시나리오에서는 [Azure 리소스의 관리 ID](container-registry-authentication-managed-identity.md)로 로그인할 수도 있습니다.

### <a name="az-acr-login-with---expose-token"></a>az acr login with --expose-token

경우에 따라 환경에서 Docker 디먼이 실행되고 있지 않으면 `az acr login`을 사용하여 인증해야 합니다. 예를 들어 Docker CLI를 제공하지만 Docker 디먼을 실행하지 않는 Azure Cloud Shell에서는 스크립트에서 `az acr login`을 실행해야 할 수 있습니다.

이 시나리오의 경우 먼저 `--expose-token` 매개 변수를 사용하여 `az acr login`을 실행합니다. 이 옵션을 지정하면 Docker CLI를 통해 로그인하지 않고 액세스 토큰을 노출합니다.

```azurecli
az acr login --name <acrName> --expose-token
```

출력에 액세스 토큰이 약식으로 표시됩니다.

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
```
레지스트리 인증의 경우 토큰 자격 증명을 안전한 위치에 저장하고 권장 방법에 따라 [docker 로그인](https://docs.docker.com/engine/reference/commandline/login/) 자격 증명을 관리하는 것이 좋습니다. 예를 들어 다음과 같이 환경 변수에 토큰 값을 저장합니다.

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

그런 다음 `00000000-0000-0000-0000-000000000000`을 사용자 이름으로 전달하고 액세스 토큰을 암호로 사용하여 `docker login`을 실행합니다.

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```
마찬가지로에서 반환 된 토큰을 명령과 함께 사용 하 여 레지스트리를 인증할 수 있습니다 `az acr login` `helm registry login` .

```console
echo $TOKEN | helm registry login myregistry.azurecr.io \
            --username 00000000-0000-0000-0000-000000000000 \
            --password-stdin
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

개발 워크스테이션에서 만든 레지스트리로 이미지 풀 및 푸시와 같이 직접 레지스트리를 사용하여 작업할 때 개별 Azure ID를 사용하여 인증합니다. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 사용하여 [Azure PowerShell](/powershell/azure/uninstall-az-ps)에 로그인한 다음, [Connect-AzContainerRegistry](/powershell/module/az.containerregistry/connect-azcontainerregistry) cmdlet을 실행합니다.

```azurepowershell
Connect-AzAccount
Connect-AzContainerRegistry -Name <acrName>
```

`Connect-AzContainerRegistry`를 사용하여 로그인하는 경우 PowerShell은 `Connect-AzAccount`를 실행할 때 만든 토큰을 사용하여 원활하게 레지스트리로 세션을 인증합니다. 인증 흐름을 완료하려면 환경에서 Docker CLI 및 Docker 디먼이 설치되어 실행되고 있어야 합니다. `Connect-AzContainerRegistry`은 Docker 클라이언트를 사용하여 `docker.config` 파일에 Azure Active Directory 토큰을 설정합니다. 이러한 방식으로 로그인하고 나면 자격 증명이 캐시되고 세션의 후속 `docker` 명령에 사용자 이름 또는 암호가 필요하지 않습니다.

> [!TIP]
> 또한 [OCI 아티팩트](container-registry-oci-artifacts.md)와 같은 Docker 이미지 외의 아티팩트를 레지스트리로 푸시 또는 풀하려면 `Connect-AzContainerRegistry`을 사용하여 개별 ID를 인증합니다.

레지스트리 액세스의 경우 `Connect-AzContainerRegistry`에 사용되는 토큰은 **3시간** 동안 유효하므로 항상 `docker` 명령을 실행하기 전에 레지스트리에 로그인하는 것이 좋습니다. 토큰이 만료될 경우 다시 `Connect-AzContainerRegistry` 명령을 사용하여 토큰을 새로 고친 후 다시 인증합니다.

Azure ID와 함께 `Connect-AzContainerRegistry`을 사용하면 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)가 제공됩니다. 일부 시나리오의 경우 Azure AD의 개별 ID를 사용하여 레지스트리에 로그인하거나 특정 [Azure 역할 및 사용 권한](container-registry-roles.md)으로 다른 Azure 사용자를 구성할 수 있습니다. 교차 서비스 시나리오의 경우나 개별 액세스를 관리하지 않으려는 작업 그룹 또는 개발 워크플로의 요구 사항을 처리해야 하는 시나리오에서는 [Azure 리소스의 관리 ID](container-registry-authentication-managed-identity.md)로 로그인할 수도 있습니다.

---

## <a name="service-principal"></a>서비스 주체

레지스트리에 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)를 할당하면 애플리케이션 또는 서비스에서 헤드리스 인증에 이를 사용할 수 있습니다. 서비스 주체는 레지스트리에 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 허용하며 사용자는 레지스트리에 여러 서비스 주체를 할당할 수 있습니다. 여러 서비스 주체를 사용하면 서로 다른 애플리케이션에 대한 다양한 액세스를 정의할 수 있습니다.

컨테이너 레지스트리에 사용할 수 있는 역할은 다음과 같습니다.

* **AcrPull**: 끌어오기

* **AcrPush**: 끌어오기 및 밀어넣기

* **소유자**: 풀, 푸시, 다른 사용자에게 역할 할당

전체 역할 목록은 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.

Azure Container Registry에 인증하기 위한 서비스 주체를 만드는 CLI 스크립트와 자세한 지침은 [Azure Container Registry authentication with service principals](container-registry-auth-service-principal.md)(서비스 주체를 사용한 Azure Container Registry 인증)를 참조하세요.

## <a name="admin-account"></a>관리자 계정

각 컨테이너 레지스트리에는 관리 사용자 계정이 포함되어 있으며 기본적으로 사용하지 않도록 설정되어 있습니다. 관리 사용자를 사용하도록 설정하고 Azure Portal을 사용하거나 Azure CLI, Azure PowerShell 또는 기타 Azure 도구를 사용하여 해당 자격 증명을 관리할 수 있습니다. 관리자 계정은 레지스트리에 대한 모든 권한을 가집니다.

컨테이너 레지스트리에서 특정 Azure 서비스로 이미지를 배포하는 일부 시나리오에서는 현재 관리자 계정이 필요합니다. 예를 들어 Azure Portal을 사용하여 레지스트리에서 [Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) 또는 [Azure Web Apps for Containers](container-registry-tutorial-deploy-app.md)로 컨테이너 이미지를 직접 배포할 경우 관리자 계정이 필요합니다.

> [!IMPORTANT]
> 관리자 계정은 주로 테스트 용도로 단일 사용자가 레지스트리에 액세스하도록 설계되었습니다. 관리자 계정 자격 증명을 여러 사용자 간에 공유하지 않는 것이 좋습니다. 관리자 계정으로 인증하는 모든 사용자는 레지스트리에 대한 푸시 및 풀 액세스 권한이 있는 단일 사용자로 나타납니다. 이 계정을 변경하거나 사용하지 않도록 설정하면 해당 자격 증명을 사용하는 모든 사용자의 레지스트리 액세스는 허용되지 않습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다.
>

관리자 계정은 두 개의 암호가 제공되며, 둘 다 다시 생성할 수 있습니다. 두 개의 암호를 사용하면 다른 암호를 다시 생성하는 동안에 하나의 암호를 사용하여 레지스트리에 대한 연결을 유지할 수 있습니다. 관리자 계정을 사용할 수 있으면 레지스트리에 대한 기본 인증 메시지가 표시될 때 사용자 이름과 둘 중 한 가지 암호를 `docker login` 명령에 전달할 수 있습니다. 예를 들면 다음과 같습니다.

```
docker login myregistry.azurecr.io
```

로그인 자격 증명을 관리하는 권장 방법은 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 참조하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

기존 레지스트리에 대한 관리 사용자를 사용하도록 설정하려면 Azure CLI에서 [az acr update](/cli/azure/acr#az_acr_update) 명령의 `--admin-enabled` 매개 변수를 사용하면 됩니다.

```azurecli
az acr update -n <acrName> --admin-enabled true
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

기존 레지스트리에 대해 관리 사용자를 사용하도록 설정하려면 Azure PowerShell에서 [Update-AzContainerRegistry](/powershell/module/az.containerregistry/update-azcontainerregistry) 명령의 `EnableAdminUser` 매개 변수를 사용하면 됩니다.

```azurepowershell
Update-AzContainerRegistry -Name <acrName> -ResourceGroupName myResourceGroup -EnableAdminUser
```

---

레지스트리로 이동하여 **설정** 에서 **액세스 키** 를 선택한 다음 **관리 사용자** 에서 **사용** 을 선택하면 Azure Portal에서 관리 사용자를 사용하도록 설정할 수 있습니다.

![Azure Portal에서 관리 사용자 UI 사용][auth-portal-01]

## <a name="next-steps"></a>다음 단계

* [Azure CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-azure-cli.md)

* [Azure PowerShell을 사용하여 첫 번째 이미지 푸시](container-registry-get-started-powershell.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
