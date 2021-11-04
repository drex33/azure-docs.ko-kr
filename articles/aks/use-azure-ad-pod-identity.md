---
title: Azure Kubernetes Service에서 Azure Active Directory Pod 관리 ID 사용(미리 보기)
description: Azure Kubernetes 서비스 (AKS)에서 Azure AD pod 관리 id를 사용 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: 3792dbfe187ef6e4b850338448bde5417b78e687
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501764"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Azure Kubernetes Service에서 Azure Active Directory Pod 관리 ID 사용(미리 보기)

Azure Active Directory (azure ad) pod 관리 id는 Kubernetes 기본 형식을 사용 하 여 azure ad의 [azure 리소스 및 id에 대 한 관리 되는 id][az-managed-identities] 를 pod와 연결 합니다. 관리자는 pod가 Azure AD를 id 공급자로 사용 하는 Azure 리소스에 액세스할 수 있도록 하는 Kubernetes 기본 형식으로 id 및 바인딩을 만듭니다.

> [!NOTE]
> 이 문서에서 설명하는 Pod 관리 ID(미리 보기) 기능은 Pod 관리 ID V2(미리 보기)로 대체됩니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스가 설치되어 있어야 합니다.

* Azure CLI 버전 2.20.0 이상
* `aks-preview` 확장 버전 0.5.5 이상

### <a name="limitations"></a>제한 사항

* 클러스터에는 최대 200개 Pod ID가 허용됩니다.
* 클러스터에는 최대 200개 Pod ID 예외가 허용됩니다.
* Pod 관리 ID는 Linux 노드 풀에서만 사용할 수 있습니다.

### <a name="register-the-enablepodidentitypreview"></a>`EnablePodIdentityPreview`를 등록합니다.

`EnablePodIdentityPreview` 기능을 등록합니다.

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI 설치

*aks-preview* Azure CLI 확장 버전 0.5.5 이상도 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="operation-mode-options"></a>작업 모드 옵션

Azure AD pod id는 두 가지 작업 모드를 지원 합니다.
 
* **표준 모드**:이 모드에서는 다음 두 구성 요소가 AKS 클러스터에 배포 됩니다. 
  * [Mic (관리 Id 컨트롤러](https://azure.github.io/aad-pod-identity/docs/concepts/mic/)): Mic는 Kubernetes API 서버를 통해 Pod, [Azureidentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) 및 [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) 에 대 한 변경 내용을 감시 하는 Kubernetes 컨트롤러입니다. 관련 변경 내용이 검색되면 MIC는 필요에 따라 [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/)를 추가하거나 삭제합니다. 구체적으로 pod가 예약 되 면 MIC는 Azure에서 관리 되는 id를 생성 단계 중 노드 풀에서 사용 되는 기본 가상 머신 확장 집합에 할당 합니다. Id를 사용 하는 모든 pod가 삭제 되 면 동일한 관리 id를 다른 pod에서 사용 하지 않는 한 노드 풀의 가상 머신 확장 집합에서 id가 제거 됩니다. MIC는 AzureIdentity 또는 AzureIdentityBinding을 만들거나 삭제할 때도 유사한 작업을 수행합니다.
  * [Nmi (Node Managed Identity)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/): NMI는 AKS 클러스터의 각 노드에서 DaemonSet로 실행 되는 pod입니다. NMI는 각 노드에서 [Azure Instance Metadata Service](../virtual-machines/linux/instance-metadata-service.md?tabs=linux) 에 대 한 보안 토큰 요청을 가로채 며, 해당 요청을 자신으로 리디렉션하고, pod가 응용 프로그램을 대신 하 여 azure AD 테 넌 트에서 토큰을 요청 하는 id에 액세스할 수 있는지 확인 합니다.
* **관리 모드**:이 모드는 NMI만 제공 합니다. 사용자가 ID를 수동으로 할당하고 관리해야 합니다. 자세한 내용은 [관리 모드의 Pod id](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/)를 참조 하십시오.

[설치 가이드](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/)에 표시 된 것 처럼 투구 차트 또는 yaml 매니페스트를 통해 Azure AD pod id를 설치 하는 경우와 모드 중에서 선택할 수 있습니다 `standard` `managed` . 이 문서에 표시 된 것 처럼 AKS 클러스터 추가 기능을 사용 하 여 Azure AD pod id를 설치 하기로 결정 하는 경우 설치 프로그램에서이 모드를 사용 `managed` 합니다.

## <a name="create-an-aks-cluster-with-azure-container-networking-interface-cni"></a>Azure CNI(Container Networking Interface)를 사용하여 AKS 클러스터 만들기

> [!NOTE]
> 이는 기본 권장 구성입니다.

Azure CNI 및 Pod 관리 ID를 사용하도록 설정하여 AKS 클러스터를 만듭니다. 다음 명령은 [az group create][az-group-create]를 사용하여 *myResourceGroup* 이라는 리소스 그룹을 만들고 [az aks create][az-aks-create] 명령을 사용하여 *myResourceGroup* 리소스 그룹에 *myAKSCluster* 라는 AKS 클러스터를 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

[az aks get-credentials][az-aks-get-credentials]를 사용하여 AKS 클러스터에 로그인합니다. 또한 이 명령은 개발 컴퓨터에서 `kubectl` 클라이언트 인증서를 다운로드하고 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

> [!NOTE]
> AKS 클러스터에서 Pod 관리 ID를 사용하도록 설정하면 *aks-addon-exception* 이라는 AzurePodIdentityException이 *kube-system* 네임스페이스에 추가됩니다. AzurePodIdentityException를 사용 하면 특정 레이블이 있는 pod가 NMI 서버에서 가로채 않고 Azure Instance Metadata Service (IMDS) 끝점에 액세스할 수 있습니다. *Aks-추가-예외* 를 통해 Azure AD pod 관리 id와 같은 aks 자사 addons는 수동으로 AzurePodIdentityException를 구성 하지 않고도 작동할 수 있습니다. 필요에 따라 `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` 또는 `kubectl`을 사용하여 AzurePodIdentityException을 추가, 제거 및 업데이트할 수 있습니다.

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Azure CNI를 사용하여 기존 AKS 클러스터 업데이트

Pod 관리 ID를 포함하도록 Azure CNI를 사용하여 기존 AKS 클러스터를 업데이트합니다.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity
```

## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Azure Active Directory Pod 관리 ID와 함께 Kubenet 네트워크 플러그 인 사용 

> [!IMPORTANT]
> Kubenet을 사용하여 클러스터에서 aad-pod-identity를 실행하는 것은 보안에 영향을 주므로 권장되는 구성이 아닙니다. Kubenet을 사용하여 클러스터에서 aad-pod-identity를 사용하도록 설정하기 전에 완화 단계를 수행하고 정책을 구성하세요.

### <a name="mitigation"></a>완화 방법

클러스터 수준에서 취약점을 완화하기 위해 Azure 기본 제공 정책 "Kubernetes 클러스터 컨테이너는 허용된 기능만 사용해야 합니다."를 사용하여 CAP_NET_RAW 공격을 제한할 수 있습니다.  

"필수 삭제 기능"에 NET_RAW 추가

![image](https://user-images.githubusercontent.com/50749048/118558790-206b8880-b735-11eb-9e48-236b81116812.png)

Azure Policy를 사용하지 않는 경우 OpenPolicyAgent 허용 컨트롤러를 Gatekeeper 유효성 검사 웹후크와 함께 사용할 수 있습니다. 클러스터에 이미 Gatekeeper가 설치된 경우 K8sPSPCapabilities 형식의 ConstraintTemplate을 추가합니다.

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
NET_RAW 기능을 사용하여 Pod 생성을 제한하는 템플릿을 추가합니다.

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Kubenet 네트워크 플러그 인을 사용하여 AKS 클러스터 만들기

Kubenet 네트워크 플러그 인 및 Pod 관리 ID를 사용하여 AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Kubenet 네트워크 플러그 인을 사용하여 기존 AKS 클러스터 업데이트

Pod 관리 ID를 포함하도록 Kubenet 네트워크 플러그 인을 사용하여 기존 AKS 클러스터를 업데이트합니다.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>ID 만들기

> [!IMPORTANT]
> Id를 만들려면 구독에 대 한 관련 사용 권한 (예: Owner)이 있어야 합니다.

[az identity create][az-identity-create]를 사용하여 ID를 만들고 *IDENTITY_CLIENT_ID* 및 *IDENTITY_RESOURCE_ID* 변수를 설정합니다.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>관리 ID에 대한 권한 할당

데모를 실행하려면 *IDENTITY_CLIENT_ID* 관리 ID에 AKS 클러스터의 가상 머신 확장 집합이 포함된 리소스 그룹에서 가상 머신 참가자 권한이 있어야 합니다.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Virtual Machine Contributor" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Pod ID 만들기

`az aks pod-identity add`를 사용하여 클러스터의 Pod ID를 만듭니다.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> “POD_IDENTITY_NAME”은 [RFC 1123]에 정의된 대로 유효한 [DNS 하위 도메인 이름]이어야 합니다. 

> [!NOTE]
> `pod-identity add`를 사용하여 포드 ID를 할당하면 Azure CLI는 클러스터 ID에 Pod ID(*IDENTITY_RESOURCE_ID*)에 대한 관리 ID 운영자 역할을 부여하려고 시도합니다.

## <a name="run-a-sample-application"></a>샘플 애플리케이션 실행

Pod에서 Azure AD pod 관리 id를 사용 하려면 pod에 *AzureIdentityBinding* 의 선택기와 일치 하는 값을 가진 *aadpodidbinding* 레이블이 필요 합니다. Azure AD pod 관리 id를 사용 하 여 샘플 응용 프로그램을 실행 하려면 `demo.yaml` 다음 내용으로 파일을 만듭니다. *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* 및 *IDENTITY_RESOURCE_GROUP* 을 이전 단계의 값으로 바꿉니다. *SUBSCRIPTION_ID* 를 구독 ID로 바꿉니다.

> [!NOTE]
> 이전 단계에서는 *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* 및 *IDENTITY_RESOURCE_GROUP* 변수를 만들었습니다. `echo`와 같은 명령을 사용하여 변수에 대해 설정한 값(예: `echo $IDENTITY_NAME`)을 표시할 수 있습니다.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: $POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Pod 정의에는 이전 단계에서 `az aks pod-identity add`를 실행한 Pod ID 이름과 일치하는 값을 가진 *aadpodidbinding* 레이블이 있습니다.

`kubectl apply`를 사용하여 Pod ID와 동일한 네임스페이스에 `demo.yaml`을 배포합니다.

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

샘플 애플리케이션이 `kubectl logs`를 사용하여 성공적으로 실행되는지 확인합니다.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

토큰이 성공적으로 획득 되었으며 *가져오기* 작업에 성공 했는지 확인 합니다.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="run-an-application-with-multiple-identities"></a>여러 ID를 사용하여 애플리케이션 실행

응용 프로그램에서 여러 id를 사용 하도록 설정 하려면 `--binding-selector` pod id를 만들 때를 동일한 선택기로 설정 합니다.

```azurecli-interactive
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME_1} --identity-resource-id ${IDENTITY_RESOURCE_ID_1} --binding-selector myMultiIdentitySelector
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME_2} --identity-resource-id ${IDENTITY_RESOURCE_ID_2} --binding-selector myMultiIdentitySelector
```

그런 다음 `aadpodidbinding` POD YAML의 필드를 지정한 바인딩 선택기로 설정 합니다.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: myMultiIdentitySelector
...
```

## <a name="clean-up"></a>정리

클러스터에서 Azure AD pod 관리 id를 제거 하려면 클러스터에서 샘플 응용 프로그램 및 pod id를 제거 합니다. 그런 다음, ID를 제거합니다.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>다음 단계

관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID][az-managed-identities]를 참조하세요.

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[RFC 1123]: https://tools.ietf.org/html/rfc1123
[DNS 하위 도메인 이름]: https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names
