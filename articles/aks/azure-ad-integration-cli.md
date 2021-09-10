---
title: Azure Kubernetes Service와 Azure Active Directory 통합(레거시)
description: Azure CLI를 사용하여 Azure Active Directory 지원 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법 알아보기(레거시)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/29/2021
ms.author: miwithro
ms.openlocfilehash: 2075c4ce9bf01d4843de2037259fec2670d5dc06
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566987"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Azure CLI를 사용하여 Azure Kubernetes Service와 Azure Active Directory 통합(레거시)

> [!WARNING]
> **이 문서에서 설명하는 Azure AD 통합(레거시)의 기능은 2024년 2월 29일부터 더 이상 사용되지 않습니다.
>
> AKS에는 서버 또는 클라이언트 애플리케이션을 관리하지 않아도 되는 새롭고 향상된 [AKS 관리형 Azure AD][managed-aad] 환경이 포함됩니다. 마이그레이션하려면 [여기][managed-aad-migrate]에 있는 지침을 따릅니다.

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인할 수 있습니다. 클러스터 운영자는 사용자의 ID 또는 디렉터리 그룹 멤버 자격에 따라 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 구성할 수도 있습니다.

이 문서에서는 필요한 Azure AD 구성 요소를 만든 다음, Azure AD 지원 클러스터를 배포하고, AKS 클러스터에서 기본 Kubernetes 역할을 만드는 방법을 보여 줍니다.

이 문서에서 사용되는 전체 샘플 스크립트는 [Azure CLI 샘플 - Azure AD와 AKS 통합][complete-script]을 참조하세요.

## <a name="the-following-limitations-apply"></a>다음과 같은 제한 사항이 적용됩니다.

- Azure AD는 Kubernetes RBAC 지원 클러스터에서만 사용할 수 있습니다.
- Azure AD 레거시 통합은 클러스터를 만드는 동안에만 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

[https://shell.azure.com](https://shell.azure.com)으로 이동하여 브라우저에서 Cloud Shell을 엽니다.

일관성을 보장하고 이 문서의 명령을 실행하는 데 도움이 되도록 원하는 AKS 클러스터 이름에 대한 변수를 만듭니다. 다음 예제에서는 *myakscluster* 이름을 사용합니다.

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 인증 개요

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다. Webhook 토큰 인증에 대한 자세한 내용은 [webhook 인증 설명서][kubernetes-webhook]를 참조하세요.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 애플리케이션을 구성합니다. Azure 테넌트 관리자가 이 작업을 완료해야 합니다.

## <a name="create-azure-ad-server-component"></a>Azure AD 서버 구성 요소 만들기

AKS와 통합하려면 ID 요청의 엔드포인트로 사용되는 Azure AD 애플리케이션을 만들고 사용합니다. 필요한 첫 번째 Azure AD 애플리케이션은 사용자의 Azure AD 그룹 멤버 자격을 얻습니다.

[az ad app create][az-ad-app-create] 명령을 사용하여 서버 애플리케이션 구성 요소를 만든 다음, [az ad app update][az-ad-app-update] 명령을 사용하여 그룹 멤버 자격 클레임을 업데이트합니다. 다음 예제에서는 [시작하기 전에](#before-you-begin) 섹션에 정의된 *aksname* 변수를 사용하고 변수를 만듭니다.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

이제 [az ad sp create][az-ad-sp-create] 명령을 사용하여 서버 앱의 서비스 주체를 만듭니다. 이 서비스 주체는 Azure 플랫폼 내에서 자신을 인증하는 데 사용됩니다. 그런 다음, [az ad sp credential reset][az-ad-sp-credential-reset] 명령을 사용하여 서비스 주체 비밀을 가져오고 다음 단계 중 하나에서 사용할 *serverApplicationSecret* 이라는 변수에 할당합니다.

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD 서비스 주체에는 다음 작업을 수행할 수 있는 권한이 필요합니다.

* 디렉터리 데이터 읽기
* 로그인 및 사용자 프로필 읽기

[az ad app permission add][az-ad-app-permission-add] 명령을 사용하여 이 권한을 할당합니다.

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

마지막으로, [az ad app permission grant][az-ad-app-permission-grant] 명령을 사용하여 서버 애플리케이션에 대해 이전 단계에서 할당된 권한을 부여합니다. 현재 계정이 테넌트 관리자가 아니면 이 단계가 실패합니다. 또한 [az ad app permission admin-consent][az-ad-app-permission-admin-consent]를 사용하여 관리자 동의가 필요할 수 있는 정보를 요청하도록 Azure AD 애플리케이션에 대한 권한을 추가해야 합니다.

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD 클라이언트 구성 요소 만들기

두 번째 Azure AD 애플리케이션은 사용자가 Kubernetes CLI(`kubectl`)를 사용하여 AKS 클러스터에 로그할 때 사용됩니다. 이 클라이언트 애플리케이션은 사용자의 인증 요청을 사용하고 해당 자격 증명 및 권한을 확인합니다. [az ad app create][az-ad-app-create] 명령을 사용하여 클라이언트 구성 요소의 Azure AD 앱을 만듭니다.

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

[az ad sp create][az-ad-sp-create] 명령을 사용하여 클라이언트 애플리케이션의 서비스 주체를 만듭니다.

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

[az ad app show][az-ad-app-show] 명령을 사용하여 두 앱 구성 요소 간 인증 흐름을 허용하도록 서버 앱의 oAuth2 ID를 가져옵니다. 이 oAuth2 ID는 다음 단계에서 사용됩니다.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

[az ad app permission add][az-ad-app-permission-add] 명령을 사용하여 oAuth2 통신 흐름을 사용할 수 있도록 클라이언트 애플리케이션 및 서버 애플리케이션 구성 요소에 대한 권한을 추가합니다. 그런 다음, [az ad app permission grant][az-ad-app-permission-grant] 명령을 사용하여 서버 애플리케이션과 통신하도록 클라이언트 애플리케이션에 대한 권한을 부여합니다.

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>클러스터 배포

두 개의 Azure AD 애플리케이션을 만든 후 이제 AKS 클러스터 자체를 만듭니다. 먼저, [az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *EastUS* 지역에 리소스 그룹을 만듭니다.

클러스터용 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

[az account show][az-account-show] 명령을 사용하여 Azure 구독의 테넌트 ID를 가져옵니다. 그런 다음, [az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. AKS 클러스터를 만드는 명령은 서버 및 클라이언트 애플리케이션 ID, 서버 애플리케이션 서비스 주체 비밀, 테넌트 ID를 제공합니다.

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

마지막으로, [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 클러스터 관리자 자격 증명을 가져옵니다. 다음 단계 중 하나에서 일반 ‘사용자’ 클러스터 자격 증명을 가져와서 Azure AD 인증 흐름이 작동하는지 확인합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Kubernetes RBAC 바인딩 만들기

Azure Active Directory 계정을 AKS 클러스터와 함께 사용하려면 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. *역할* 은 부여할 사용 권한을 정의하고, *바인딩* 은 원하는 사용자에게 역할을 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [Kubernetes RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

[az ad signed-in-user show][az-ad-signed-in-user-show] 명령을 사용하여 현재 로그인한 사용자의 UPN(사용자 계정 이름)을 가져옵니다. 이 사용자 계정은 다음 단계에서 Azure AD 통합에 대해 사용됩니다.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Kubernetes RBAC 바인딩 권한을 부여한 사용자가 동일한 Azure AD 테넌트에 있는 경우 *userPrincipalName* 에 따라 권한을 할당합니다. 사용자가 다른 Azure AD 테넌트에 있는 경우에는 대신 *objectId* 속성을 쿼리하고 사용합니다.

`basic-azure-ad-binding.yaml`이라는 YAML 매니페스트를 만들고 다음 콘텐츠를 붙여넣습니다. 마지막 줄에서 *userPrincipalName_or_objectId* 를 이전 명령의 UPN 또는 개체 ID 출력으로 바꿉니다.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

[kubectl apply][kubectl-apply] 명령을 사용하여 ClusterRoleBinding을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Azure AD를 사용하여 클러스터에 액세스

이제 AKS 클러스터에 대한 Azure AD 인증의 통합을 테스트하겠습니다. `kubectl` 구성 컨텍스트를 설정하여 일반 사용자 자격 증명을 사용합니다. 이 컨텍스트는 Azure AD를 통해 다시 모든 인증 요청을 전달합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

이제 [kubectl get pods][kubectl-get] 명령을 사용하여 모든 네임스페이스에서 Pod를 확인합니다.

```console
kubectl get pods --all-namespaces
```

웹 브라우저를 통해 Azure AD 자격 증명을 사용하여 인증하라는 로그인 프롬프트가 표시됩니다. 인증된 후에는 다음 예제 출력과 같이 `kubectl` 명령이 AKS 클러스터에 Pod를 표시합니다.

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

`kubectl`에 대해 수신된 인증 토큰이 캐시됩니다. 토큰이 만료되었거나 Kubernetes 구성 파일이 다시 만들어지는 경우에만 로그인하라는 메시지가 다시 표시됩니다.

다음 예제 출력과 같이 웹 브라우저를 사용하여 로그인한 후에 권한 부여 오류 메시지가 표시되는 경우 다음과 같은 가능한 문제를 확인합니다.

```output
error: You must be logged in to the server (Unauthorized)
```

* 사용자 계정이 동일한 Azure AD 테넌트에 있는지 여부에 따라 적절한 개체 ID 또는 UPN을 정의했습니다.
* 사용자는 200개가 넘는 그룹의 멤버가 아닙니다.
* 서버의 애플리케이션 등록에 정의된 비밀은 `--aad-server-app-secret`을 사용하여 구성된 값과 일치합니다.
* 컴퓨터에 한 번에 한 버전의 kubectl만 설치되어 있어야 합니다. 충돌하는 버전은 권한 부여 중에 문제를 일으킬 수 있습니다. 최신 버전을 설치하려면, [az aks install-cli][az-aks-install-cli]를 사용하세요.

## <a name="next-steps"></a>다음 단계

이 문서에 표시된 명령을 포함하는 전체 스크립트는 [AKS 샘플 리포지토리의 Azure AD 통합 스크립트][complete-script]를 참조하세요.

Azure AD 사용자 및 그룹을 사용하여 클러스터 리소스에 대한 액세스를 제어하려면 [AKS에서 Kubernetes 역할 기반 액세스 제어 및 Azure AD ID를 사용하여 클러스터 리소스에 대한 액세스 제어][azure-ad-rbac]를 참조하세요.

Kubernetes 클러스터를 보호하는 방법에 관한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션][rbac-authorization]을 참조하세요.

ID 및 리소스 제어에 관한 모범 사례는 [AKS의 인증 및 권한 부여 모범 사례][operator-best-practices-identity]를 참조하세요.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-ad-app-create]: /cli/azure/ad/app#az_ad_app_create
[az-ad-app-update]: /cli/azure/ad/app#az_ad_app_update
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az_ad_app_permission_add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az_ad_app_permission_grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az_ad_app_permission_admin_consent
[az-ad-app-show]: /cli/azure/ad/app#az_ad_app_show
[az-group-create]: /cli/azure/group#az_group_create
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az_ad_signed_in_user_show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
