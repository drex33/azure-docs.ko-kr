---
title: Azure Kubernetes Service에서 Azure AD 사용
description: AKS(Azure Kubernetes Service)에서 Azure AD를 사용하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 10/20/2021
ms.author: miwithro
ms.openlocfilehash: d2e2a7326029ff4f25a960ca2f3012b5868d2c33
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227438"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS 관리형 Azure Active Directory 통합

AKS 관리 Azure AD 통합은 Azure AD 통합 프로세스를 간소화합니다. 이전에는 사용자가 클라이언트 및 서버 앱을 만들어야 했으며 Azure AD 테넌트에 디렉터리 읽기 권한을 부여해야 했습니다. 새 버전에서는 AKS 리소스 공급자가 클라이언트 및 서버 앱을 자동으로 관리합니다.

## <a name="azure-ad-authentication-overview"></a>Azure AD 인증 개요

클러스터 관리자는 사용자의 ID 또는 디렉터리 그룹 구성원 자격에 따라 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 구성할 수 있습니다. OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Azure AD 통합 흐름에 대한 자세한 정보는 [Azure Active Directory 통합 개념 설명서](concepts-identity.md#azure-ad-integration)를 참조하세요.

## <a name="limitations"></a>제한 사항 

* AKS 관리형 Azure AD 통합을 사용하지 않도록 설정할 수 없습니다
* AKS 관리형 Azure AD 통합 클러스터를 레거시 AAD로 변경하는 기능은 지원되지 않습니다
* Kubernetes RBAC를 사용하지 않는 클러스터는 AKS 관리형 Azure AD 통합에 대해 지원되지 않습니다.
* AKS 관리형 Azure AD 통합에 연결된 Azure AD 테넌트 변경은 지원되지 않습니다

## <a name="prerequisites"></a>사전 요구 사항

* Azure CLI 버전 2.29.0 이상
* Kubectl 최소 버전 [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) 또는 [kubelogin](https://github.com/Azure/kubelogin)
* [helm](https://github.com/helm/helm)을 사용하는 경우 최소 버전은 helm 3.3입니다.

> [!Important]
> 최소 1.18.1 버전의 Kubectl 또는 kubelogin을 사용해야 합니다. Kubernetes와 kubectl의 마이너 버전 간 차이는 1 버전 이상이면 안 됩니다. 올바른 버전을 사용하지 않는 경우 인증 문제가 발생합니다.

Kubectl와 kubelogin을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

다른 운영 체제에 대한 [지침](https://kubernetes.io/docs/tasks/tools/install-kubectl/)을 사용합니다.

## <a name="before-you-begin"></a>시작하기 전에

클러스터의 경우 Azure AD 그룹이 필요합니다. 이 그룹은 클러스터 관리자 권한을 부여하기 위해 클러스터에 관리자 그룹으로 등록됩니다. 기존 Azure AD 그룹을 사용하거나 그룹을 새로 만들 수 있습니다. Azure AD 그룹의 개체 ID를 기록합니다.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

클러스터 관리자에 새 Azure AD 그룹을 만들려면 다음 명령을 사용합니다.

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD를 사용하는 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용하여 AKS 클러스터를 만들 수 있습니다.

Azure 리소스 그룹 만들기:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

AKS 클러스터를 만들고 Azure AD 그룹에 대한 관리 액세스를 사용하도록 설정합니다

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

AKS 관리형 Azure AD 클러스터가 성공적으로 만들어지면 응답 본문에 다음 섹션이 포함됩니다
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

클러스터를 만든 후에는 해당 클러스터에 액세스를 시작할 수 있습니다.

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD를 사용하는 클러스터에 액세스

Azure AD 정의 그룹을 사용하여 클러스터에 액세스하기 전에 [Azure Kubernetes Service 클러스터 사용자](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 기본 제공 역할이 필요합니다.

클러스터에 액세스하는 데 사용할 사용자 자격 증명을 가져옵니다.
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
지침에 따라 로그인합니다.

다음과 같이 kubectl 노드 가져오기 명령을 사용하여 클러스터의 노드를 봅니다.

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
[Azure RBAC(Azure 역할 기반 액세스 제어)](./azure-ad-rbac.md)를 구성하여 클러스터에 대한 추가 보안 그룹을 구성합니다.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Azure AD의 액세스 문제 해결

> [!Important]
> 아래에 설명된 단계는 일반 Azure AD 그룹 인증을 우회합니다. 응급 상황에서만 사용하세요.

클러스터에 액세스 권한이 있는 유효한 Azure AD 그룹에 액세스하지 않아 영구적으로 차단된 경우에도 관리자 자격 증명을 가져와 클러스터에 직접 액세스할 수 있습니다.

이러한 단계를 수행하려면 [Azure Kubernetes Service 클러스터 관리자](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) 기본 제공 역할에 액세스 권한이 있어야 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>기존 클러스터에서 AKS 관리형 Azure AD 통합 사용

기존 Kubernetes RBAC 사용 클러스터에서 AKS 관리형 Azure AD 통합을 사용하도록 설정할 수 있습니다. 클러스터에 대한 액세스를 유지하려면 관리자 그룹을 설정해야 합니다.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

AKS 관리형 Azure AD 클러스터가 성공적으로 활성화되면 응답 본문에 다음 섹션이 포함됩니다

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

클러스터에 액세스하기 위해 [여기][access-cluster]의 단계를 따라 사용자 자격 증명을 다운로드하세요.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>AKS 관리형 Azure AD 통합으로 업그레이드

클러스터에서 레거시 Azure AD 통합을 사용하는 경우 AKS 관리형 Azure AD 통합으로 업그레이드할 수 있습니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

AKS 관리형 Azure AD 클러스터가 성공적으로 마이그레이션되면 응답 본문에 다음 섹션이 포함됩니다

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Kubeconfig를 업데이트 하 여 클러스터에 액세스 하려면 [다음][access-cluster]단계를 수행 합니다.

## <a name="non-interactive-sign-in-with-kubelogin"></a>kubelogin을 사용한 비 대화형 로그인

현재 kubectl에서 사용할 수 없는, 연속 통합 파이프라인과 같은 몇 가지 비 대화형 시나리오가 있습니다. [`kubelogin`](https://github.com/Azure/kubelogin)을 사용하여 비 대화형 서비스 주체 로그인으로 클러스터에 액세스할 수 있습니다.

## <a name="disable-local-accounts"></a>로컬 계정 사용 안 함

AKS 클러스터를 배포할 때 로컬 계정은 기본적으로 사용하도록 설정됩니다. RBAC 또는 Azure Active Directory 통합을 사용하는 경우에도 기본적으로 감사할 수 없는 백도어 옵션으로 `--admin` 액세스 권한이 있습니다. 이를 고려하여 AKS는 사용자에게 플래그 `disable-local-accounts`을 통해 로컬 계정을 사용하지 않도록 할 수 있는 기능을 제공합니다. 기능이 클러스터에서 사용하도록 설정되었는지 여부를 나타내는 필드 `properties.disableLocalAccounts`도 관리 클러스터 API에 추가되었습니다.

> [!NOTE]
> Azure AD 통합이 사용하도록 설정된 클러스터에서 `aad-admin-group-object-ids`로 지정된 그룹에 속한 사용자는 관리자가 아닌 자격 증명을 통해 계속 액세스할 수 있습니다. Azure AD 통합을 사용하지 않고 `properties.disableLocalAccounts`를 true로 설정하지 않은 클러스터에서는 사용자 및 관리자 자격 증명을 모두 얻지 못합니다.

> [!NOTE]
> 사용자가 로컬 계정/s를 사용 했을 수 있는 기존 AKS 클러스터에서 로컬 계정 사용자를 사용 하지 않도록 설정한 후 관리자가 [클러스터 인증서를 회전](https://docs.microsoft.com/azure/aks/certificate-rotation#rotate-your-cluster-certificates)하 여 해당 사용자가 액세스할 수 있는 인증서를 해지 해야 합니다.  이 클러스터가 새 클러스터 인 경우 작업이 필요 하지 않습니다.

### <a name="create-a-new-cluster-without-local-accounts"></a>로컬 계정이 없는 새 클러스터 만들기

로컬 계정 없이 새 AKS 클러스터를 만들려면 `disable-local-accounts` 플래그와 함께 [az aks create][az-aks-create] 명령을 사용합니다.

```azurecli-interactive
az aks create -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

출력에서 `properties.disableLocalAccounts` 필드가 true로 설정되어 있는지 확인하여 로컬 계정이 사용하지 않도록 설정되었는지 확인합니다.

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

관리자 자격 증명을 가져오려고 하면 기능에서 액세스를 차단하고 있음을 나타내는 오류 메시지와 함께 실패합니다.

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="disable-local-accounts-on-an-existing-cluster"></a>기존 클러스터에서 로컬 계정 사용 안 함

기존 AKS 클러스터에서 로컬 계정을 사용하지 않도록 설정하려면 `disable-local-accounts` 플래그와 함께 [az aks update][az-aks-update] 명령을 사용합니다.

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

출력에서 `properties.disableLocalAccounts` 필드가 true로 설정되어 있는지 확인하여 로컬 계정이 사용하지 않도록 설정되었는지 확인합니다.

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

관리자 자격 증명을 가져오려고 하면 기능에서 액세스를 차단하고 있음을 나타내는 오류 메시지와 함께 실패합니다.

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="re-enable-local-accounts-on-an-existing-cluster"></a>기존 클러스터에서 로컬 계정 다시 사용

AKS는 또한 `enable-local` 플래그를 사용하여 기존 클러스터에서 로컬 계정을 다시 사용하는 기능을 제공합니다.

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --enable-local
```

출력에서 `properties.disableLocalAccounts` 필드가 false로 설정되어 있는지 확인하여 로컬 계정이 다시 사용하도록 설정되었는지 확인합니다.

```output
"properties": {
    ...
    "disableLocalAccounts": false,
    ...
}
```

관리자 자격 증명 가져오기가 성공합니다.

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Merged "<cluster-name>-admin" as current context in C:\Users\<username>\.kube\config
```

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Azure AD와 AKS를 사용하여 조건부 액세스 사용

AKS 클러스터와 Azure AD를 통합하는 경우 [조건부 액세스][aad-conditional-access]를 사용하여 클러스터에 대한 액세스를 제어할 수도 있습니다.

> [!NOTE]
> Azure AD 조건부 액세스는 Azure AD Premium의 기능입니다.

AKS와 함께 사용할 예제 조건부 액세스 정책을 만들려면 다음 단계를 완료합니다.

1. Azure Portal에서 Azure Active Directory를 검색하여 선택합니다.
1. 왼쪽의 Azure Active Directory 메뉴에서 *엔터프라이즈 애플리케이션* 을 선택합니다.
1. 왼쪽의 엔터프라이즈 애플리케이션에 메뉴에서 *조건부 액세스* 를 선택합니다.
1. 왼쪽의 조건부 액세스 메뉴에서 *정책* 과 *새 정책* 을 차례로 선택합니다.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="조건부 액세스 정책 추가":::
1. 정책 이름(예: *aks-policy*)을 입력합니다.
1. *사용자 및 그룹* 을 선택하고 *포함* 아래에서 *사용자 및 그룹 선택* 을 선택합니다. 정책을 적용할 사용자 및 그룹을 선택합니다. 이 예시에서는 클러스터에 대한 관리 액세스 권한이 있는 동일한 Azure AD 그룹을 선택합니다.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="조건부 액세스 정책을 적용할 사용자 또는 그룹을 선택":::
1. *클라우드 앱 또는 작업* 을 선택하고 *포함* 아래에서 *앱 선택* 을 선택합니다. *Azure Kubernetes Service* 를 검색하고 *Azure Kubernetes Service AAD 서버* 를 선택합니다.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="조건부 액세스 정책을 적용하기 위해 Azure Kubernetes Service AD 서버 선택":::
1. *액세스 제어* 에서 *권한 부여* 를 선택합니다. *액세스 허용* 을 선택하고 *디바이스를 규격으로 표시해야 함* 을 선택합니다.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="조건부 액세스 정책에 대해 규격 디바이스만 허용하도록 선택":::
1. *정책 사용* 에서 *켜기* 를 선택한 후 *만들기* 를 선택합니다.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="조건부 액세스 정책 사용":::

클러스터에 액세스하기 위한 사용자 자격 증명을 가져옵니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

지침에 따라 로그인합니다.

`kubectl get nodes` 명령을 사용하여 클러스터의 노드를 봅니다.

```azurecli-interactive
kubectl get nodes
```

지침에 따라 다시 로그인합니다. 로그인에 성공했음을 나타내는 오류 메시지가 표시되지만 관리자는 리소스에 액세스하기 위해 Azure AD에서 관리할 액세스를 요청하는 디바이스가 필요합니다.

Azure Portal에서 Azure Active Directory로 이동하여 *엔터프라이즈 애플리케이션* 을 선택한 다음 *작업* 에서 *로그인* 을 선택합니다. 위쪽의 항목에 *상태* 가 *실패* 이며 *조건부 액세스* 가 *성공* 했음이 표시됩니다. 항목을 선택하고 *조건부 액세스* 를 *세부 정보* 에서 선택합니다. 조건부 액세스 정책이 나열되어 있는지 확인합니다.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="조건부 액세스 정책으로 인한 로그인 항목 실패":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Azure AD 및 AKS를 사용하여 Just-In-Time 클러스터 액세스 구성

클러스터 액세스 제어를 위한 또 다른 옵션은 Just-In-Time 요청에 대해 PIM(Privileged Identity Management)을 사용하는 것입니다.

>[!NOTE]
> PIM은 프리미엄 P2 SKU를 필요로 하는 Azure AD Premium 기능입니다. Azure AD SKU에 대한 자세한 내용은 [가격 책정 가이드][aad-pricing]를 참조하세요.

AKS 관리형 Azure AD 통합을 사용하여 AKS 클러스터와 Just-In-Time 액세스 요청을 통합하려면 다음 단계를 완료합니다.

1. Azure Portal에서 Azure Active Directory를 검색하여 선택합니다.
1. 이 지침의 나머지 부분에서 `<tenant-id>`로 참조되는 테넌트 ID를 기록해 둡니다. :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="웹 브라우저에서 Azure Active Directory의 Azure Portal 화면은 테넌트 ID가 강조된 상태로 표시됩니다.":::
1. 왼쪽의 Azure Active Directory 메뉴의 *관리* 아래에서 *그룹* 을 선택한 후 *새 그룹* 을 차례로 선택합니다.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="‘새 그룹’ 옵션이 강조된 Azure Portal Active Directory 그룹 화면을 표시합니다.":::
1. *보안* 그룹 유형이 선택되어 있는지 확인하고 *myJITGroup* 과 같은 그룹 이름을 입력합니다. *Azure AD 역할을 이 그룹에 할당할 수 있습니다(미리 보기)* 에서 *예* 를 선택합니다. 마지막으로 *만들기* 를 선택합니다.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Azure Portal의 새 그룹 만들기 화면을 표시합니다.":::
1. 그러면 *그룹* 페이지로 돌아갑니다. 새로 만든 그룹을 선택하고 이러한 지침의 나머지 부분에서 `<object-id>`로 참조되는 개체 ID를 기록해 둡니다.
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="개체 ID를 강조하여 방금 만든 그룹의 Azure Portal 화면을 표시합니다":::
1. 이전의 `<tenant-id>`과 `<object-id>` 값을 사용하여 AKS 관리형 Azure AD 통합을 통해 AKS 클러스터를 배포합니다.
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Azure Portal로 돌아가서 왼쪽의 *작업* 메뉴에서 *권한 있는 액세스(미리 보기)* 를 선택하고 *권한 있는 액세스 사용* 을 선택합니다.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="'권한 있는 액세스 사용'이 강조된 Azure Portal의 권한 있는 액세스(미리 보기) 페이지가 표시됩니다":::
1. *할당 추가* 를 선택하여 액세스 권한 부여를 시작합니다.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="사용하도록 설정한 후의 Azure Portal의 권한 있는 액세스(미리 보기) 화면이 표시됩니다. ‘할당 추가’ 옵션이 강조됩니다.":::
1. *구성원* 의 역할을 선택하고 클러스터 액세스 권한을 부여할 사용자 및 그룹을 선택합니다. 이러한 할당은 그룹 관리자가 언제든지 수정할 수 있습니다. 이동할 준비가 되면 *다음* 을 선택합니다.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="샘플 사용자가 구성원으로 추가되도록 선택된 Azure Portal 할당 구성원 자격 추가 화면이 표시됩니다. ‘다음’ 옵션이 강조됩니다.":::
1. *활성* 의 할당 형식, 원하는 기간을 선택하고 사유를 입력합니다. 계속할 준비가 되면 *할당* 을 선택합니다. 할당 형식에 대한 자세한 내용은 [Privileged Identity Management에서 권한 있는 액세스 그룹(미리 보기)에 대한 자격 할당][aad-assignments]을 참조하세요.
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Azure Portal의 할당 추가 설정 화면이 표시됩니다. ‘활성’의 할당 형식을 선택하고 샘플 사유를 입력했습니다. ‘할당’ 옵션이 강조됩니다.":::

할당이 완료되면 클러스터에 액세스하여 Just-In-Time 액세스가 작동하는지 확인합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

단계에 따라 로그인합니다.

`kubectl get nodes` 명령을 사용하여 클러스터의 노드를 봅니다.

```azurecli-interactive
kubectl get nodes
```

인증 요구 사항을 확인하고 인증 단계를 따릅니다. 성공하면 다음과 비슷한 내용이 출력됩니다.

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```
### <a name="apply-just-in-time-access-at-the-namespace-level"></a>네임 스페이스 수준에서 Just-in-time 액세스를 적용 합니다.

1. AKS 클러스터를 [AZURE RBAC](manage-azure-rbac.md)와 통합 합니다.
2. 역할 할당을 통해 클러스터에서 네임 스페이스를 사용 하 여 Just-in-time 액세스와 통합 하려는 그룹을 연결 합니다.

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```
3. 네임 스페이스 수준에서 구성 된 그룹을 PIM과 연결 하 여 구성을 완료 합니다.

### <a name="troubleshooting"></a>문제 해결

만약 `kubectl get nodes`가 다음과 같은 오류를 반환한다면

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

보안 그룹의 관리자가 사용자의 계정에 *활성* 할당을 줬는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Kubernetes 권한 부여에 대한 Azure RBAC 통합][azure-rbac-integration]에 대해 알아봅니다
* [Kubernetes RBAC와 Azure AD 통합][azure-ad-rbac]에 대해 알아봅니다.
* [kubelogin](https://github.com/Azure/kubelogin)을 사용하여 kubectl에서 사용할 수 없는 Azure 인증 기능에 액세스합니다.
* [AKS 및 Kubernetes ID 개념][aks-concepts-identity]에 대한 자세한 정보를 알아봅니다.
* [ARM(Azure Resource Manager) 템플릿][aks-arm-template]을 사용하여 AKS 관리형 Azure AD 사용 클러스터를 만듭니다.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-update]: /cli/azure/aks#az_aks_update
