---
title: Azure의 Kubernetes에서 Azure RBAC 관리
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 Kubernetes 권한 부여에 Azure RBAC를 사용하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 02/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: b6cd1bce4528d35cf0f7f897e6032079cae6cdea
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579239"
---
# <a name="use-azure-rbac-for-kubernetes-authorization"></a>Kubernetes 권한 부여에 Azure RBAC 사용

현재 [Azure AD(Azure Active Directory)와 AKS 간에 통합 인증](managed-aad.md)을 이미 활용할 수 있습니다. 이 통합을 사용하면 고객이 Azure AD 사용자, 그룹 또는 서비스 주체를 Kubernetes RBAC의 subject로 사용할 수 있습니다. 자세한 내용은 [여기](azure-ad-rbac.md)를 참조하세요.
이 기능을 사용하면 Kubernetes에 대한 사용자 ID와 자격 증명을 별도로 관리할 필요가 없습니다. 단, Azure RBAC와 Kubernetes RBAC는 여전히 별도로 설정하고 관리해야 합니다. AKS에서 RBAC를 사용한 인증 및 권한 부여에 대한 자세한 내용은 [여기](concepts-identity.md)를 참조하세요.

이 문서에서는 Azure 리소스, AKS 및 Kubernetes 리소스에 대한 통합 관리 및 액세스 제어를 허용하는 새로운 접근 방식을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure에서 Kubernetes 리소스에 대한 RBAC를 관리하는 기능을 통해 Azure 또는 네이티브 Kubernetes 메커니즘을 사용하여 클러스터 리소스에 대한 RBAC를 관리할 수 있습니다. 사용하도록 설정하면 Azure AD 보안 주체는 Azure RBAC에서 독점적으로 유효성이 검사되고 일반 Kubernetes 사용자 및 서비스 계정은 Kubernetes RBAC에서 독점적으로 유효성이 검사됩니다. AKS에서 RBAC를 사용한 인증 및 권한 부여에 대한 자세한 내용은 [여기](concepts-identity.md#azure-rbac-for-kubernetes-authorization)를 참조하세요.

### <a name="prerequisites"></a>필수 요건

- Azure CLI 버전 2.24.0 이상이 있는지 확인합니다.
- [kubectl v1.18.3+][az-aks-install-cli]가 설치되어 있는지 확인합니다.

### <a name="limitations"></a>제한 사항

- [관리형 Azure AD 통합](managed-aad.md)이 필요합니다.
- [kubectl v1.18.3+][az-aks-install-cli]를 사용합니다.
- CRD가 있고 사용자 지정 역할을 정의하는 경우 현재 CRD를 처리하는 유일한 방법은 `Microsoft.ContainerService/managedClusters/*/read`를 제공하는 것입니다. AKS는 CRD에 대한 보다 세부적인 사용 권한을 제공하기 위해 노력하고 있습니다. 나머지 개체의 경우 특정 API 그룹을 사용할 수 있습니다(예: `Microsoft.ContainerService/apps/deployments/read`).
- 새 역할 할당은 전파하고 권한 부여 서버에서 업데이트하는 데 최대 5분이 걸릴 수 있습니다.
- 인증을 위해 구성된 Azure AD 테넌트가 AKS 클러스터를 보유하는 구독의 테넌트와 동일해야 합니다. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Azure RBAC 및 관리되는 Azure AD 통합을 사용하여 새 클러스터 만들기

다음 CLI 명령을 사용하여 AKS 클러스터를 만듭니다.

Azure 리소스 그룹 만들기:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Kubernetes 권한 부여용 Azure RBAC 및 관리되는 Azure AD 통합을 사용하여 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Kubernetes 권한 부여용 Azure RBAC 및 관리되는 Azure AD 통합을 사용하여 클러스터 만들기에 성공하면 응답 본문에 다음 섹션이 있습니다.

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="integrate-azure-rbac-into-an-existing-cluster"></a>기존 클러스터에 Azure RBAC 통합

> [!NOTE]
> Kubernetes 권한 부여에 Azure RBAC를 사용하려면 클러스터에서 Azure Active Directory 통합을 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Active Directory 통합][managed-aad]을 참조하세요.

Kubernetes 인증에 대한 Azure RBAC를 기존 AKS 클러스터에 추가하려면 `enable-azure-rbac` 플래그와 함께 [az aks update][az-aks-update] 명령을 사용합니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster --enable-azure-rbac
```
기존 AKS 클러스터에서 Kubernetes 권한 부여에 대 한 Azure RBAC를 제거 하려면 플래그가 지정 된 [az AKS update][az-aks-update] 명령을 사용 합니다 `disable-azure-rbac` .

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster --disable-azure-rbac
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>사용자가 클러스터에 액세스할 수 있도록 역할 할당 만들기

AKS에는 다음과 같은 4가지 기본 제공 역할이 있습니다.


| 역할                                | Description  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC 읽기 권한자  | 읽기 전용 권한을 허용하여 네임스페이스에 있는 대부분의 개체를 볼 수 있습니다. 역할 또는 역할 바인딩은 볼 수 없습니다. 이 역할은 `Secrets`를 볼 수 없습니다. 비밀의 콘텐츠를 읽을 수 있으면 네임스페이스의 ServiceAccount 자격 증명에 액세스할 수 있으므로 네임스페이스의 ServiceAccount로 API 액세스가 허용될 수 있기 때문입니다(일종의 권한 상승).  |
| Azure Kubernetes Service RBAC 쓰기 권한자 | 네임스페이스의 대부분의 개체에 대한 읽기/쓰기 권한을 허용합니다. 이 역할은 역할 또는 역할 바인딩을 보거나 수정할 수 없습니다. 그러나 이 역할을 통해 `Secrets`과 네임스페이스의 ServiceAccount로 실행 중인 Pod에 엑세스할 수 있으므로, 네임스페이스에 있는 모든 ServiceAccount의 API 액세스 수준을 얻는 데 사용할 수 있습니다. |
| Azure Kubernetes Service RBAC 관리자  | 네임 스페이스 내에서 부여되는 관리자 액세스를 허용합니다. 네임스페이스(또는 클러스터 범위)에 있는 대부분의 리소스에 대한 읽기/쓰기 권한을 허용하며, 여기에는 네임스페이스 내에서 역할 및 역할 바인딩을 만들 수 있는 권한이 포함됩니다. 이 역할은 리소스 할당량 또는 네임스페이스 자체에 대한 쓰기 권한을 허용하지 않습니다. |
| Azure Kubernetes Service RBAC 클러스터 관리자  | 슈퍼 사용자 액세스를 허용하여 모든 리소스에 대한 모든 작업을 수행할 수 있습니다. 클러스터 및 모든 네임스페이스의 모든 리소스를 완전히 제어할 수 있습니다. |


**전체 AKS 클러스터** 로 범위가 지정된 역할 할당은 Azure Portal에 있는 클러스터 리소스의 액세스 제어(IAM) 블레이드에서 또는 아래와 같이 Azure CLI 명령을 사용하여 수행할 수 있습니다.

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

여기서 `<AAD-ENTITY-ID>`는 사용자 이름(예: user@contoso.com) 또는 서비스 주체의 ClientID일 수 있습니다.

클러스터 내의 특정 **네임스페이스** 로 범위가 지정된 역할 할당을 만들 수도 있습니다.

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

현재 네임스페이스로 범위가 지정된 역할 할당은 Azure CLI를 통해 구성해야 합니다.


### <a name="create-custom-roles-definitions"></a>사용자 지정 역할 정의 만들기

필요에 따라 자체 역할 정의를 만든 다음, 위와 같이 할당하도록 선택할 수 있습니다.

다음은 사용자가 배포만 읽을 수 있고 다른 것은 읽을 수 없도록 하는 역할 정의의 예입니다. 가능한 작업의 전체 목록은 [여기](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)에서 확인할 수 있습니다.


아래 json을 `deploy-view.json`이라는 파일에 복사합니다.

```json
{
    "Name": "AKS Deployment Reader",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

다음을 실행하여 얻을 수 있는 구독의 ID로 `<YOUR SUBSCRIPTION ID>`를 바꿉니다.

```azurecli-interactive
az account show --query id -o tsv
```

이제 `deploy-view.json`을 저장한 폴더에서 아래 명령을 실행하여 역할 정의를 만들 수 있습니다.

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

역할 정의를 만들었으면, 다음을 실행하여 사용자 또는 다른 ID에 할당할 수 있습니다.

```azurecli-interactive
az role assignment create --role "AKS Deployment Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>`kubectl`을 사용하여 Kubernetes 권한 부여에 Azure RBAC 사용

> [!NOTE]
> 아래 명령을 실행하여 최신 kubectl이 있는지 확인합니다.
>
> ```azurecli-interactive
> az aks install-cli
> ```
>
> `sudo` 권한으로 실행해야 할 수도 있습니다.

이제 원하는 역할 및 사용 권한을 할당했습니다. Kubernetes API 호출을 시작할 수 있습니다(예를 들어, `kubectl`을 통해).

이를 위해 먼저 아래 명령을 사용하여 클러스터의 kubeconfig를 가져옵니다.

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> 위 단계를 수행하려면 [Azure Kubernetes Service 클러스터 사용자](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 기본 제공 역할이 필요합니다.

이제 kubectl을 사용하여 예를 들면, 클러스터의 노드를 나열할 수 있습니다. 처음 실행하는 경우 로그인해야 하며 후속 명령은 해당 액세스 토큰을 사용합니다.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>`kubelogin`을 사용하여 Kubernetes 권한 부여에 Azure RBAC 사용

비대화형 로그인, 이전 `kubectl` 버전 또는 새 클러스터에 로그인할 필요 없이 여러 클러스터에서 SSO를 활용하는 등의 추가 시나리오를 차단 해제하기 위해(토큰이 여전히 유효한 경우), AKS는 [`kubelogin`](https://github.com/Azure/kubelogin)이라는 exec 플러그 인을 만들었습니다.

다음을 실행하여 사용할 수 있습니다.

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

처음에는 일반 kubectl과 같이 대화형으로 로그인해야 하지만 이후에는 더 이상 그럴 필요가 없으며, 새 Azure AD 클러스터의 경우에도 마찬가지입니다(토큰이 여전히 유효하기만 하면).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```

## <a name="clean-up"></a>정리

### <a name="clean-role-assignment"></a>정리 역할 할당

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```

수행한 모든 할당에서 ID를 복사한 후 다음을 실행합니다.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>역할 정의 정리

```azurecli-interactive
az role definition delete -n "AKS Deployment Reader"
```

### <a name="delete-cluster-and-resource-group"></a>클러스터 및 리소스 그룹 삭제

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

- AKS 인증, 권한 부여, Kubernetes RBAC 및 Azure RBAC에 대한 자세한 내용은 [여기](concepts-identity.md)를 참조하세요.
- Azure RBAC에 대한 자세한 내용은 [여기](../role-based-access-control/overview.md)를 참조하세요.
- Kubernetes 권한 부여를 위한 사용자 지정 Azure 역할을 세부적으로 정의하는 데 사용할 수 있는 모든 작업에 대해 자세히 알아보려면 [여기](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)를 참조하세요.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-update]: /cli/azure/aks#az_aks_update
[managed-aad]: ./managed-aad.md
