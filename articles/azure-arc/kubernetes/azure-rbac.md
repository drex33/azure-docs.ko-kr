---
title: Azure Arc 지원 Kubernetes 클러스터를 위한 Azure RBAC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 지원 Kubernetes 클러스터에서 권한 부여 검사를 위해 Azure RBAC를 사용합니다.
ms.openlocfilehash: 2607f82e0935ead0b6013bae963e22616c340b80
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105042"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 지원 Kubernetes 클러스터와 Azure Active Directory를 통합합니다.

Kubernetes [ClusterRoleBinding 및 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) 개체 형식은 Kubernetes에서 기본적으로 권한 부여를 정의하는 데 도움이 됩니다. 이 기능을 사용하면 Azure에서 Azure AD(Azure Active Directory) 및 역할 할당을 사용하여 클러스터에 대한 권한 부여 검사를 제어할 수 있습니다. 즉, 이제 Azure 역할 할당을 사용하여 배포, Pod, 서비스와 같은 Kubernetes 개체를 읽고, 쓰고, 삭제할 수 있는 사람을 세부적으로 제어할 수 있습니다.

이 기능에 대한 개념적인 개요는 [Azure Arc 지원 Kubernetes의 Azure RBAC](conceptual-azure-rbac.md) 문서를 참조하세요.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 버전 2.16.0 이상으로 [Azure CLI를 설치 및 업그레이드](/cli/azure/install-azure-cli)합니다.

- `connectedk8s` Azure CLI 확장 버전 1.1.0 이상을 설치합니다.

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    `connectedk8s` 확장이 이미 설치되어 있는 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다. 

    ```azurecli
    az extension update --name connectedk8s
    ```

- 기존 Azure Arc 지원 Kubernetes 클러스터를 연결합니다.
    - 아직 클러스터를 연결하지 않은 경우 [빠른 시작](quickstart-connect-cluster.md)을 사용하세요.
    - 버전 1.1.0 이상으로 [에이전트를 업그레이드](agent-upgrade.md#manually-upgrade-agents)합니다.

> [!NOTE]
> 이 기능은 사용자가 클러스터의 API 서버에 액세스할 수 없는 클라우드 공급자(예: Elastic Kubernetes Service 또는 Google Kubernetes Engine)의 관리되는 Kubernetes 제품에 대해 설정할 수 없습니다. Azure Kubernetes Service(AKS) 클러스터의 경우 이 [기능은 고유하게 제공되며](../../aks/manage-azure-rbac.md) AKS 클러스터를 Azure Arc에 연결할 필요가 없습니다.

## <a name="set-up-azure-ad-applications"></a>Azure AD 애플리케이션 설정

### <a name="create-a-server-application"></a>서버 애플리케이션 생성

1. 새 Azure AD 애플리케이션을 생성하고 `appId` 값을 받습니다. 이 값은 이후 단계에서 `serverApplicationId`로 사용됩니다.

    ```azurecli
    CLUSTERNAME="<clusterName>"
    SERVER_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Server" --identifier-uris "https://${CLUSTERNAME}Server" --query appId -o tsv)
    echo $SERVER_APP_ID
    ```

1. 애플리케이션의 그룹 멤버십 클레임을 업데이트합니다.

    ```azurecli
    az ad app update --id "${SERVER_APP_ID}" --set groupMembershipClaims=All
    ```

1. 서비스 주체를 생성하고 `password` 필드 값을 받습니다. 나중에 클러스터에서 이 기능을 사용하도록 설정할 때, `serverApplicationSecret`으로서 이 값이 필요합니다.

    ```azurecli
    az ad sp create --id "${SERVER_APP_ID}"
    SERVER_APP_SECRET=$(az ad sp credential reset --name "${SERVER_APP_ID}" --credential-description "ArcSecret" --query password -o tsv)
    ```

1. 애플리케이션에 "로그인 및 사용자 프로필 읽기" API 권한을 부여합니다.

    ```azurecli
    az ad app permission add --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > Azure 테넌트 관리자가 이 단계를 실행해야 합니다.
    > 
    > 프로덕션에서 이 기능을 사용하려면 각 클러스터에 대해 서로 다른 서버 애플리케이션을 만드는 것이 좋습니다.

### <a name="create-a-client-application"></a>클라이언트 애플리케이션 생성하기

1. 새 Azure AD 애플리케이션을 생성하고 `appId` 값을 받습니다. 이 값은 이후 단계에서 `clientApplicationId`로 사용됩니다.

    ```azurecli
    CLIENT_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Client" --native-app --reply-urls "https://${CLUSTERNAME}Client" --query appId -o tsv)
    echo $CLIENT_APP_ID
    ```

2. 이 클라이언트 애플리케이션의 서비스 주체를 만듭니다.

    ```azurecli
    az ad sp create --id "${CLIENT_APP_ID}"
    ```

3. 서버 애플리케이션에 대해 `oAuthPermissionId` 값을 받습니다.

    ```azurecli
    az ad app show --id "${SERVER_APP_ID}" --query "oauth2Permissions[0].id" -o tsv
    ```

4. 클라이언트 애플리케이션에 대해 필요한 권한을 부여합니다.

    ```azurecli
    az ad app permission add --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}" --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}"
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>서버 애플리케이션에 대한 역할 할당 만들기

요청을 수행하는 사용자에게 해당 요청에 포함된 Kubernets 개체에 대한 권한이 부여되었는지 확인할 수 있는 `Microsoft.Authorization/*/read` 권한이 서버 애플리케이션에 필요합니다.

1. 다음 콘텐츠가 포함된 *accessCheck.json* 이라는 파일을 만듭니다.

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    `<subscription-id>`을 실제 구독 ID로 바꿉니다.

2. 다음 명령을 실행하여 새 사용자 지정 역할을 만듭니다.

    ```azurecli
    ROLE_ID=$(az role definition create --role-definition ./accessCheck.json --query id -o tsv)
    ```

3. 생성한 역할을 사용하여 서버 애플리케이션에서 역할 할당을 `assignee`로 만듭니다.

    ```azurecli
    az role assignment create --role "${ROLE_ID}" --assignee "${SERVER_APP_ID}" --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-the-cluster"></a>클러스터에서 Azure RBAC 사용 설정

다음 명령을 실행하여 Arc 지원 Kubernetes 클러스터에서 Azure RBAC(역할 기반 액세스 제어)를 사용하도록 설정합니다.

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id "${SERVER_APP_ID}" --app-secret "${SERVER_APP_SECRET}"
```
    
> [!NOTE]
> 위 명령을 실행하기 전 컴퓨터의 `kubeconfig` 파일이 Azure RBAC 기능을 사용하도록 설정할 클러스터를 지정하는지 확인합니다.
>
> Azure RBAC 대신 Kubernetes 네이티브 `ClusterRoleBinding` 및 `RoleBinding` 개체를 사용하여 권한 부여 검사를 진행 중인 사용자 이름, 이메일, OpenID 연결을 쉼표로 구분한 위 명령과 함께 `--skip-azure-rbac-list`을 사용합니다.

### <a name="generic-cluster-where-no-reconciler-is-running-on-the-apiserver-specification"></a>apiserver 사양에서 실행 중인 조정자 없는 제네릭 클러스터

1. 클러스터의 모든 마스터 노드에 SSH로 연결하고 다음 단계를 실행합니다.

    1. 편집 모드에서 `apiserver` 매니페스트를 엽니다.
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. `volumes` 아래에 다음 사양 추가:
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. `volumeMounts` 아래에 다음 사양 추가:

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. 다음 `apiserver` 인수 추가:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Kubernetes 클러스터가 버전 1.19.0 이상이면 다음 `apiserver` 인수도 설정해야 합니다.

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. `apiserver` Pod를 업데이트하기 위해 편집기를 저장하고 종료합니다.


### <a name="cluster-created-by-using-cluster-api"></a>Cluster API를 사용하여 생성한 클러스터

1. 워크로드 클러스터에서 인증 및 권한 부여 웹후크 구성 파일을 포함하는 가드 비밀을 컴퓨터에 복사합니다.

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. *azure-arc-guard-manifests.yaml* 파일에 있는 `namespace` 필드를 워크로드 클러스터 생성을 위해 사용자 지정 리소스를 적용하려는 관리 클러스터 내의 네임스페이스로 변경합니다.

1. 이 매니페스트를 적용합니다.

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. `kubectl edit kcp <clustername>-control-plane`를 실행하여 `KubeadmControlPlane` 개체를 편집합니다.
    
    1. 다음 코드 조각을 `files` 아래에 추가합니다.
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. 다음 코드 조각을 `apiServer` > `extraVolumes` 아래에 추가합니다.
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. 다음 코드 조각을 `apiServer` > `extraArgs` 아래에 추가합니다.
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. `KubeadmControlPlane` 개체를 업데이트하기 위해 저장하고 종료합니다. 이러한 변경 사항이 워크로드 클러스터에 적용될 때까지 기다립니다.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>사용자가 클러스터에 액세스하기 위한 역할 할당 만들기

Azure Arc 지원 Kubernetes 리소스 소유자는 기본 제공 역할 또는 사용자 지정 역할을 사용하여 다른 사용자에게 Kubernetes 클러스터 액세스 권한을 부여할 수 있습니다.

### <a name="built-in-roles"></a>기본 제공 역할

| 역할 | Description |
|---|---|
| [Azure Arc Kubernetes 뷰어](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | 네임스페이스에 있는 대부분의 개체를 볼 수 있는 읽기 전용 액세스 권한을 허용합니다. 이 역할은 비밀 보기를 허용하지 않습니다. 비밀에 대한 `read` 권한은 네임스페이스에 있는 `ServiceAccount` 자격 증명에 대한 액세스를 허용하기 때문입니다. 이러한 자격 증명을 사용하면 해당 `ServiceAccount` 값(권한 상승 형태)을 통해 API에 액세스할 수 있습니다. |
| [Azure Arc Kubernetes 작성자](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | 네임스페이스에 있는 대부분의 개체에 대해 읽기/쓰기 액세스 권한을 허용합니다. 이 역할은 보기 또는 수정 역할 또는 역할 바인딩을 허용하지 않습니다. 그러나 이 역할을 사용하여 네임스페이스에서 `ServiceAccount` 값으로 Pod를 실행하고 비밀에 액세스할 수 있습니다. 따라서 이를 사용하여 네임스페이스의 모든 `ServiceAccount` 값의 API 액세스 수준을 얻기 위해 사용될 수 있습니다. |
| [Azure Arc Kubernetes 관리자](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | 관리자 액세스 권한을 허용합니다. 네임스페이스 내에서 `RoleBinding`을 통해 부여하려는 의도입니다. `RoleBinding`에서 사용할 경우, 네임스페이스 내에서 역할 및 역할 바인딩 만들기 기능을 포함하여 네임스페이스에 있는 대부분의 리소스에 대한 읽기/쓰기 권한을 허용합니다. 이 역할은 리소스 할당량 또는 네임스페이스 자체에 대한 쓰기 권한을 허용하지 않습니다. |
| [Azure Arc Kubernetes 클러스터 관리자](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | 슈퍼 사용자 액세스를 허용하여 모든 리소스에 대한 모든 작업을 실행할 수 있습니다. `ClusterRoleBinding`에서 사용할 경우, 클러스터 및 모든 네임스페이스의 모든 리소스를 완전히 제어할 수 있습니다. `RoleBinding`에서 사용할 경우, 네임스페이스 자체를 포함하여 역할 바인딩의 네임스페이스에 있는 모든 리소스에 대한 전체 제어 권한을 부여합니다.|

클러스터 리소스의 **Access Control(IAM)** 창에서 Azure Portal 내의 Arc 지원 Kubernetes 클러스터로 범위가 지정된 역할 할당을 생성할 수 있습니다. 다음 Azure CLI 명령 또한 사용할 수 있습니다.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

이러한 명령에서, `AZURE-AD-ENTITY-ID`은 사용자 이름(예: `testuser@mytenant.onmicrosoft.com`) 또는 서비스 주체의 `appId` 값일 수도 있습니다.

다음은 클러스터 내의 특정 네임스페이스로 범위가 지정된 역할 할당을 만드는 예시입니다.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Azure Portal 또는 Azure CLI를 사용하여 클러스터로 범위가 지정된 역할 할당을 만들 수 있지만, 네임스페이스로 범위가 지정된 역할 할당은 CLI로만 가능합니다.

### <a name="custom-roles"></a>사용자 지정 역할

역할 할당의 용도에 따라 고유 역할 정의를 만들도록 선택할 수 있습니다.

사용자의 배포 읽기만 허용하는 역할 정의에 대한 아래 예시를 확인하세요. 자세한 내용은 [역할 정의를 생성하는 데 사용할 수 있는 데이터 작업의 전체 목록](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)을 참조하세요.

아래 JSON 개체를 *custom-role.json* 이라는 파일에 복사합니다. `<subscription-id>` 자리 표시자를 실제 구독 ID로 바꿉니다. 사용자 지정 역할에는 데이터 작업 중 하나가 사용되며, 이를 사용해서 역할 할당이 생성되는 범위(클러스터 또는 네임스페이스)의 모든 배포를 볼 수 있습니다.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. *custom-role.json* 을 저장한 폴더에서 아래 명령을 실행하여 역할 정의를 생성합니다.

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. 이 사용자 지정 역할 정의를 사용하여 역할 할당을 생성합니다.

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>사용자 자격 증명으로 kubectl 구성

클러스터에 액세스하는 데 필요한 *kubeconfig* 파일을 가져오는 방법은 두 가지가 있습니다.

- Azure Arc 지원 Kubernetes 클러스터의 [클러스터 연결](cluster-connect.md) 기능(`az connectedk8s proxy`)을 사용합니다.
- 클러스터 관리자는 *kubeconfig* 파일을 다른 모든 사용자와 공유합니다.

### <a name="if-youre-accessing-the-cluster-by-using-the-cluster-connect-feature"></a>클러스터 연결 기능을 사용하여 클러스터에 액세스하는 경우

다음 명령을 실행하여 프록시 프로세스를 시작합니다.

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

프록시 프로세스가 실행된 후 콘솔에서 또 다른 탭을 열어서 [클러스터에 요청 전송을 시작](#send-requests-to-the-cluster)할 수 있습니다.

### <a name="if-the-cluster-admin-shared-the-kubeconfig-file-with-you"></a>클러스터 관리자가 kubeconfig 파일을 사용자에게 공유한 경우 

1. 다음 명령을 실행하여 사용자에 대해 자격 증명을 설정합니다.

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. 이전에 만든 *kubeconfig* 파일을 엽니다. `contexts`에서 클러스터와 연결된 컨텍스트가 이전 단계에서 만든 사용자 자격 증명을 가리키는지 확인합니다.

1. `user` > `config` 아래에서 **config-mode** 설정을 추가합니다.
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="send-requests-to-the-cluster"></a>클러스터로 요청을 보냅니다.

1. `kubectl` 명령을 실행합니다. 예:
   * `kubectl get nodes` 
   * `kubectl get pods`

1. 브라우저 기반 인증을 묻는 메시지가 표시되면 디바이스 로그인 URL(`https://microsoft.com/devicelogin`)을 복사하고 웹 브라우저로 엽니다.

1. 콘솔에 인쇄된 코드를 입력합니다. 터미널에서 코드를 복사하여 디바이스 인증 입력 프롬프트에 붙여 넣습니다.

1. 사용자 이름(`testuser@mytenant.onmicrosoft.com`) 및 연결된 암호를 입력합니다.

1. 이와 비슷한 오류 메시지가 표시되면 요청된 리소스에 액세스하도록 권한이 부여되지 않은 것입니다.

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    이 사용자가 리소스에 액세스하도록 권한을 부여하려면 관리자가 새 역할 할당을 만들어야 합니다.

## <a name="use-conditional-access-with-azure-ad"></a>Azure AD에서 조건부 액세스 사용

Arc 지원 Kubernetes 클러스터와 Azure AD를 통합하는 경우, [조건부 액세스](../../active-directory/conditional-access/overview.md)를 사용하여 클러스터에 대한 액세스를 제어할 수도 있습니다.

> [!NOTE]
> Azure AD 조건부 액세스는 Azure AD Premium의 기능입니다.

클러스터에 사용할 예시 조건부 액세스 정책을 만들려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure Active Directory** 를 검색하여 선택합니다.
1. 왼쪽의 Azure Active Directory 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
1. 왼쪽의 엔터프라이즈 애플리케이션에 메뉴에서 **조건부 액세스** 를 선택합니다.
1. 왼쪽의 조건부 액세스 메뉴에서 **정책** > **새 정책** 을 차례로 선택합니다.
    
    [ ![조건부 액세스 정책을 추가하는 단추를 보여 주는 스크린샷.](./media/azure-rbac/conditional-access-new-policy.png) ](./media/azure-rbac/conditional-access-new-policy.png#lightbox)

1. 정책 이름(예: **arc-k8s-policy**)을 입력합니다.
1. **사용자 및 그룹** 을 선택합니다. **포함** 아래에서 **사용자 및 그룹 선택** 을 선택합니다. 정책을 적용할 사용자 및 그룹을 선택합니다. 이 예시에서는 클러스터에 대한 관리 액세스 권한이 있는 동일한 Azure AD 그룹을 선택합니다.

    [ ![조건부 액세스 정책을 적용할 사용자 또는 그룹 선택을 보여 주는 스크린샷.](./media/azure-rbac/conditional-access-users-groups.png) ](./media/azure-rbac/conditional-access-users-groups.png#lightbox)

1. **클라우드 앱 또는 작업** 을 선택합니다. **포함** 에서 **앱 선택** 을 선택합니다. 그런 다음 이전에 만든 서버 애플리케이션을 검색하여 선택합니다.

    [ ![조건부 액세스 정책을 적용할 서버 애플리케이션을 선택하는 방법을 보여 주는 스크린샷.](./media/azure-rbac/conditional-access-apps.png) ](./media/azure-rbac/conditional-access-apps.png#lightbox)

1. **액세스 제어** 에서 **권한 부여** 를 선택합니다. **액세스 권한 부여** > **디바이스가 규격으로 표시되어야 함** 을 선택합니다.

    [ ![조건부 액세스 정책에 대해 규격 디바이스만 허용하도록 선택하는 것을 보여 주는 스크린샷.](./media/azure-rbac/conditional-access-grant-compliant.png) ](./media/azure-rbac/conditional-access-grant-compliant.png#lightbox)
    
1. **정책 사용** 에서 **켜기** > **생성** 을 선택합니다.

    [ ![조건부 액세스 정책 사용을 보여 주는 스크린샷. ](./media/azure-rbac/conditional-access-enable-policies.png) ](./media/azure-rbac/conditional-access-enable-policies.png#lightbox)

클러스터에 다시 액세스합니다. 예를 들어, `kubectl get nodes` 명령을 실행하여 클러스터의 노드를 봅니다.

```console
kubectl get nodes
```

지침에 따라 다시 로그인합니다. 로그인에 성공했음을 나타내는 오류 메시지가 표시되지만, 리소스에 액세스하기 위해 관리자는 Azure AD에서 액세스 요청을 관리하는 디바이스를 요구합니다. 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure Active Directory** 로 이동합니다.
1. **Enterprise 애플리케이션** 을 선택합니다. 그런 다음 **활동** 아래에서 **로그인** 을 선택합니다. 
1. 맨 위에 있는 항목은 **상태** 에 대해 **실패**, **조건부 액세스** 에 대해 **성공** 을 표시합니다. 항목을 선택하고, **세부 정보** 에서 **조건부 액세스** 를 선택합니다. 조건부 액세스 정책이 나열되어 있는지 확인합니다.

   [ ![조건부 액세스 정책으로 인해 실패한 로그인 항목을 보여 주는 스크린샷.](./media/azure-rbac/conditional-access-sign-in-activity.png) ](./media/azure-rbac/conditional-access-sign-in-activity.png#lightbox)

## <a name="configure-just-in-time-cluster-access-with-azure-ad"></a>Azure AD를 사용하여 Just-In-Time 클러스터 액세스 구성

클러스터 액세스 제어를 위한 또 다른 옵션은 Just-In-Time 요청에 대해 PIM(Privileged Identity Management)을 사용하는 것입니다.

>[!NOTE]
> PIM은 프리미엄 P2 SKU를 필요로 하는 Azure AD Premium 기능입니다. Azure AD SKU에 대한 자세한 내용은 [가격 책정 가이드](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

클러스터에 대한 Just-In-Time 액세스 요청을 구성하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure Active Directory** 를 검색하여 선택합니다.
1. 테넌트 ID를 기록해 둡니다. 지침의 나머지 부분에서 해당 ID를 `<tenant-id>`로 지칭합니다.

    [ ![Azure Active Directory 테넌트 세부 정보를 보여 주는 스크린샷.](./media/azure-rbac/jit-get-tenant-id.png) ](./media/azure-rbac/jit-get-tenant-id.png#lightbox)

1. 왼쪽의 Azure Active Directory 메뉴의 **관리** 아래에서 **그룹** 을 선택한 후  > **새 그룹** 을 차례로 선택합니다.

    [ ![새 그룹을 만들기 위한 선택 항목을 보여 주는 스크린샷.](./media/azure-rbac/jit-create-new-group.png) ](./media/azure-rbac/jit-create-new-group.png#lightbox)

1. **그룹 유형** 에 대해 **보안** 이 선택되어 있는지 확인합니다.  그룹 이름(예: **myJITGroup**)을 입력합니다. **Azure AD 역할을 이 그룹에 할당할 수 있습니다(미리 보기)** 에서 **예** 를 선택합니다. 마지막으로 **만들기** 를 선택합니다.

    [ ![새 그룹에 대한 세부 정보를 보여 주는 스크린샷.](./media/azure-rbac/jit-new-group-created.png) ](./media/azure-rbac/jit-new-group-created.png#lightbox)

1. 그리고 **그룹** 페이지로 돌아갑니다. 새로 생성된 그룹을 선택하고 개체 ID를 적어둡니다. 지침의 나머지 부분에서는 이 ID를 `<object-id>`로 지칭합니다.

    [ ![생성된 그룹의 개체 식별자를 보여 주는 스크린샷.](./media/azure-rbac/jit-get-object-id.png) ](./media/azure-rbac/jit-get-object-id.png#lightbox)

1. Azure Portal로 돌아가서, 왼쪽의 **작업** 메뉴에서 **권한 있는 액세스(미리 보기)** 를 선택합니다. 그런 다음 **권한 있는 액세스를 사용하도록 설정** 을 선택합니다.

    [ ![권한 있는 액세스를 사용하도록 설정하기 위한 선택 항목을 보여 주는 스크린샷.](./media/azure-rbac/jit-enabling-priv-access.png) ](./media/azure-rbac/jit-enabling-priv-access.png#lightbox)

1. **할당 추가** 를 선택하여 액세스 권한 부여를 시작합니다.

    [ ![활성 할당을 추가하는 단추를 보여 주는 스크린샷.](./media/azure-rbac/jit-add-active-assignment.png) ](./media/azure-rbac/jit-add-active-assignment.png#lightbox)

1. **멤버** 의 역할을 선택하고, 클러스터 액세스 권한을 부여할 사용자 및 그룹을 선택합니다. 그룹 관리자는 언제든지 이러한 할당을 수정할 수 있습니다. 계속 진행할 준비가 되면 **다음** 을 선택합니다.

    [ ![할당 추가를 보여 주는 스크린샷.](./media/azure-rbac/jit-adding-assignment.png) ](./media/azure-rbac/jit-adding-assignment.png#lightbox)

1. **활성** 의 할당 유형, 원하는 기간을 선택하고 사유를 입력합니다. 계속할 준비가 되면 **할당** 을 선택합니다. 할당 형식에 대한 자세한 내용은 [Privileged Identity Management에서 권한 있는 액세스 그룹(미리 보기)에 대한 자격 할당](../../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group)을 참조하세요.

    [ ![할당에 대한 속성 선택을 보여 주는 스크린샷.](./media/azure-rbac/jit-set-active-assignment.png) ](./media/azure-rbac/jit-set-active-assignment.png#lightbox)

할당이 완료되면 클러스터에 액세스하여 Just-In-Time 액세스가 작동하는지 확인합니다. 예를 들어, `kubectl get nodes` 명령을 사용하여 클러스터의 노드를 봅니다.

```console
kubectl get nodes
```

인증 요구 사항을 확인하고 인증 단계를 따릅니다. 인증에 성공하면 다음과 비슷한 내용이 출력됩니다.

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME      STATUS   ROLES    AGE      VERSION
node-1    Ready    agent    6m36s    v1.18.14
node-2    Ready    agent    6m42s    v1.18.14
node-3    Ready    agent    6m33s    v1.18.14
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [클러스터 연결](cluster-connect.md)을 사용하여 클러스터에 안전하게 연결
