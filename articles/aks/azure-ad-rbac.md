---
title: 클러스터에 Azure AD 및 Kubernetes RBAC 사용
titleSuffix: Azure Kubernetes Service
description: Azure Active Directory 그룹 멤버 자격을 사용하여 AKS(Azure Kubernetes Service)에서 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 사용하는 클러스터 리소스에 대한 액세스를 제한하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 571a0153bfe56a6d3b92700eb0e40f457cf92ffc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097950"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 Kubernetes 역할 기반 액세스 제어 및 Azure Active Directory를 사용하는 클러스터 리소스에 대한 액세스 제어

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인합니다. 인증되면 기본 제공 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 사용하여 사용자의 ID 또는 그룹 멤버 자격을 기반으로 네임스페이스 및 클러스터 리소스에 대한 액세스를 관리할 수 있습니다.

이 문서에서는 Azure AD 그룹 멤버 자격을 기반으로 하는 AKS 클러스터에서 Kubernetes RBAC를 사용하여 액세스를 제어하는 방법을 보여 줍니다. 예제 그룹 및 사용자가 Azure AD에 생성된 다음, 리소스를 만들고 볼 수 있는 적절한 권한을 부여하기 위해 Roles 및 RoleBindings가 AKS 클러스터에 생성됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서는 기존 AKS 클러스터가 Azure AD 통합을 사용하도록 설정되어 있다고 가정합니다. AKS 클러스터가 필요한 경우 [AKS와의 Azure Active Directory 통합][azure-ad-aks-cli]을 참조하세요.

Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="create-demo-groups-in-azure-ad"></a>Azure AD에서 데모 그룹 만들기

이 문서에서는 Kubernetes RBAC와 Azure AD에서 클러스터 리소스에 대한 액세스를 제어하는 방법을 보여 주는 데 사용할 수 있는 두 개의 사용자 역할을 만들어 보겠습니다. 다음 두 가지 예제 역할이 사용됩니다.

* **애플리케이션 개발자**
    * *appdev* 그룹에 속한 *aksdev* 라는 사용자.
* **사이트 안정성 엔지니어**
    * *opssre* 그룹에 속한 *akssre* 라는 사용자.

프로덕션 환경에서는 Azure AD 테넌트 내에서 기존 사용자와 그룹을 사용할 수 있습니다.

먼저 [az aks show][az-aks-show] 명령을 사용하여 AKS 클러스터의 리소스 ID를 가져옵니다. 추가 명령에서 참조될 수 있도록 *AKS_ID* 라는 변수에 리소스 ID를 할당합니다.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

[az ad group create][az-ad-group-create] 명령을 사용하여 애플리케이션 개발자를 위한 Azure AD의 첫 번째 예제 그룹을 만듭니다. 다음 예제는 *appdev* 라는 그룹을 만듭니다.

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

이제 [az role assignment create][az-role-assignment-create] 명령을 사용하여 *appdev* 그룹에 대한 Azure 역할 할당을 만듭니다. 이 할당을 사용하면 그룹의 모든 멤버가 `kubectl`을 사용하여 AKS 클러스터에 ‘Azure Kubernetes Service 클러스터 사용자 역할’을 부여하는 방식으로 클러스터와 상호 작용할 수 있습니다.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`와 같은 오류가 발생할 경우 Azure AD 그룹 개체 ID가 디렉터리를 통해 전파되기를 몇 초간 기다렸다가 `az role assignment create` 명령을 다시 실행합니다.

이름이 *opssre* 인 SRE에 대해 두 번째 예제 그룹을 만듭니다.

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

다시 Azure 역할 할당을 만들어서 그룹 구성원에게 ‘Azure Kubernetes Service 클러스터 사용자 역할’을 부여합니다.

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Azure AD에서 데모 사용자 만들기

이제 애플리케이션 개발자 및 SRE에 대해 Azure AD에서 만든 두 가지 예제 그룹을 사용하여 두 예제 사용자를 만들 수 있습니다. 이 문서의 끝에서 Kubernetes RBAC 통합을 테스트하려면 해당 계정을 사용하여 AKS 클러스터에 로그인합니다.

애플리케이션 개발자에 대한 UPN(사용자 계정 이름)과 암호를 설정합니다. 다음 명령은 UPN에 대한 메시지를 표시하고 이후 명령에서 사용할 수 있도록 *AAD_DEV_UPN* 으로 설정합니다(이 문서의 명령은 BASH 셸에 입력됨). UPN에는 테넌트의 확인된 도메인 이름이 포함되어야 합니다(예: `aksdev@contoso.com`).

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

다음 명령은 암호에 대한 메시지를 표시하 고 이후 명령에서 사용할 수 있도록 *AAD_DEV_PW* 로 설정합니다.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

[az ad user create][az-ad-user-create] 명령을 사용하여 Azure AD에서 첫 번째 사용자 계정을 만듭니다.

다음 예제는 *AAD_DEV_UPN* 및 *AAD_DEV_PW* 의 값을 사용하여 표시 이름이 *AKS Dev* 인 사용자와 UPN, 안전한 암호를 만듭니다.

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

이제 [az ad group member add][az-ad-group-member-add] 명령을 사용하여 이전 섹션에서 만든 *appdev* 그룹에 사용자를 추가합니다.

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

SRE의 UPN 및 암호를 설정합니다. 다음 명령은 UPN에 대한 메시지를 표시하고 이후 명령에서 사용할 수 있도록 *AAD_SRE_UPN* 으로 설정합니다(이 문서의 명령은 BASH 셸에 입력됨). UPN에는 테넌트의 확인된 도메인 이름이 포함되어야 합니다(예: `akssre@contoso.com`).

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

다음 명령은 암호에 대한 메시지를 표시하 고 이후 명령에서 사용할 수 있도록 *AAD_SRE_PW* 로 설정합니다.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

두 번째 사용자 계정을 만듭니다. 다음 예제는 *AAD_SRE_UPN* 및 *AAD_SRE_PW* 의 값을 사용하여 표시 이름이 *AKS SRE* 인 사용자와 UPN, 안전한 암호를 만듭니다.

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>app devs에 대한 AKS 클러스터 리소스 만들기

이제 Azure AD 그룹과 사용자를 만들었습니다. AKS 클러스터에 일반 사용자로 연결하기 위해 그룹 구성원에 대해 Azure 역할 할당을 만들었습니다. 이제 서로 다른 그룹이 특정 리소스에 액세스할 수 있도록 AKS 클러스터를 구성해 보겠습니다.

먼저 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 클러스터 관리자 자격 증명을 가져옵니다. 다음 섹션 중 하나에서 일반 ‘사용자’ 클러스터 자격 증명을 가져와서 Azure AD 인증 흐름이 작동하는지 확인합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

[kubectl create namespace][kubectl-create] 명령을 사용하여 AKS 클러스터에서 네임스페이스를 만듭니다. 다음 예제는 *dev* 라는 네임스페이스를 만듭니다.

```console
kubectl create namespace dev
```

Kubernetes에서 *Roles* 는 부여할 권한을 정의하고 *RoleBindings* 는 이를 원하는 사용자 또는 그룹에 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [Kubernetes RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

먼저 *dev* 네임스페이스에 대한 역할을 만듭니다. 이 역할은 네임스페이스에 모든 권한을 부여합니다. 프로덕션 환경에서는 다른 사용자 또는 그룹에 대해 보다 세부적인 사용 권한을 지정할 수 있습니다.

`role-dev-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

[kubectl apply][kubectl-apply] 명령을 사용하여 역할을 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f role-dev-namespace.yaml
```

다음으로 [az ad group show][az-ad-group-show] 명령을 사용하여 *appdev* 그룹에 대한 리소스 ID를 가져옵니다. 이 그룹은 다음 단계에서 RoleBinding의 주체로 설정됩니다.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

이제 *appdev* 그룹에 대한 RoleBinding을 만들어서 네임스페이스 액세스에 대해 이전에 만든 역할을 사용합니다. `rolebinding-dev-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄에서 *groupObjectId* 를 이전 명령의 그룹 개체 ID 출력으로 바꿉니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

[kubectl apply][kubectl-apply] 명령을 사용하여 RoleBinding을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>SRE에 대한 AKS 클러스터 리소스 만들기

이제 이전 단계를 반복하여 SRE에 대한 네임스페이스, Role, RoleBinding을 만듭니다.

먼저 [kubectl create namespace][kubectl-create] 명령을 사용하여 *sre* 에 대한 네임스페이스를 만듭니다.

```console
kubectl create namespace sre
```

`role-sre-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

[kubectl apply][kubectl-apply] 명령을 사용하여 역할을 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f role-sre-namespace.yaml
```

[az ad group show][az-ad-group-show] 명령을 사용하여 *opssre* 그룹에 대한 리소스 ID를 가져옵니다.

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

*opssre* 그룹에 대한 RoleBinding을 만들어서 네임스페이스 액세스에 대해 이전에 만든 역할을 사용합니다. `rolebinding-sre-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄에서 *groupObjectId* 를 이전 명령의 그룹 개체 ID 출력으로 바꿉니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

[kubectl apply][kubectl-apply] 명령을 사용하여 RoleBinding을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Azure AD ID를 사용하여 클러스터 리소스와 상호 작용

이제 AKS 클러스터에서 리소스를 만들고 관리할 때 예상되는 사용 권한을 테스트해 보겠습니다. 이 예에서는 사용자의 할당된 네임스페이스에서 Pod를 예약하고 확인합니다. 그런 다음, 할당된 네임스페이스 외부에서 Pod를 예약하고 확인해 봅니다.

먼저 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 *kubeconfig* 컨텍스트를 초기화합니다. 이전 섹션에서는 클러스터 관리자 자격 증명을 사용하여 컨텍스트를 설정했습니다. 관리 사용자는 Azure AD 로그인 프롬프트를 무시합니다. `--admin` 매개 변수를 사용하지 않으면 Azure AD를 사용하여 모든 요청을 인증해야 하는 사용자 컨텍스트가 적용됩니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

*dev* 네임스페이스의 [kubectl run][kubectl-run] 명령을 사용하여 기본 NGINX Pod를 예약합니다.

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

로그인 프롬프트로 문서를 시작할 때 만든 자체 `appdev@contoso.com` 계정에 대한 자격 증명을 입력합니다. 성공적으로 로그인되면 이후 `kubectl` 명령에 대해 계정 토큰이 캐시됩니다. 다음 예제 출력과 같이 NGINX이 성공적으로 예약됩니다.

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

이제 [kubectl get pods][kubectl-get] 명령을 사용하여 *dev* 네임스페이스에서 Pod를 확인합니다.

```console
kubectl get pods --namespace dev
```

다음 예제 출력과 같이 NGINX Pod가 성공적으로 ‘실행 중’입니다.

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>할당된 네임스페이스 외부에서 클러스터 리소스 만들기 및 보기

이제 *dev* 네임스페이스 외부에서 Pod를 확인해 봅니다. [kubectl get pods][kubectl-get] 명령을 다시 사용하여 이번에는 다음과 같이 `--all-namespaces`를 확인합니다.

```console
kubectl get pods --all-namespaces
```

사용자의 그룹 멤버 자격에는 다음 예제 출력과 같이 이 작업을 허용하는 Kubernetes 역할이 없습니다.

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

동일한 방식으로 *sre* 네임스페이스와 같은 다른 네임스페이스의 Pod를 예약해 봅니다. 다음 예제 출력과 같이 사용자의 그룹 멤버 자격은 권한을 부여하는 Kubernetes Role 및 RoleBinding과 일치하지 않습니다.

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>AKS 클러스터 리소스에 대한 SRE 액세스 테스트

Azure AD 그룹 멤버 자격 및 Kubernetes RBAC가 서로 다른 사용자와 그룹 간에 올바르게 작동하는지 확인하려면 *opssre* 사용자로 로그인되어 있을 때 이전 명령을 사용해 보세요.

*aksdev* 사용자에 대해 이전에 캐시된 인증 토큰을 지우는 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 *kubeconfig* 컨텍스트를 초기화합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

할당된 *sre* 네임스페이스에서 Pod를 예약하고 확인합니다. 메시지가 표시되면 `opssre@contoso.com` 문서의 시작 부분에서 만든 자격 증명을 사용하여 로그인합니다.

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

다음 예제 출력과 같이 성공적으로 Pod을 만들고 확인할 수 있습니다.

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

이제 할당된 SRE 네임스페이스의 외부에서 Pod을 확인하거나 예약해 봅니다.

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

다음 예제 출력에 표시된 것처럼 `kubectl` 명령은 실패합니다. 사용자의 그룹 멤버 자격과 Kubernetes Role 및 RoleBindings는 다른 네임스페이스에서 리소스를 만들거나 관리할 권한을 부여하지 않습니다.

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 AKS 클러스터의 리소스와 Azure AD의 사용자 및 그룹을 만들었습니다. 이 모든 리소스를 정리하려면 다음 명령을 실행합니다.

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터를 보호하는 방법에 대한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션][rbac-authorization]을 참조하세요.

ID 및 리소스 제어에 관한 모범 사례는 [AKS의 인증 및 권한 부여 모범 사례][operator-best-practices-identity]를 참조하세요.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
