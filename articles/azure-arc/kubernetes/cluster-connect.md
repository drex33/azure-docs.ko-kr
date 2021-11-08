---
title: 클러스터 커넥트 사용하여 Azure Arc 지원 Kubernetes 클러스터에 연결
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 클러스터 커넥트 사용하여 Azure Arc 지원 Kubernetes 클러스터에 안전하게 연결
ms.openlocfilehash: e585b67495aef1fb094d157c584c6744d993b135
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000814"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>클러스터 커넥트 사용하여 Azure Arc 지원 Kubernetes 클러스터에 연결

클러스터 커넥트 사용하면 방화벽에서 인바운드 포트를 사용하도록 설정하지 않고도 Azure Arc 지원 Kubernetes 클러스터에 안전하게 연결할 수 있습니다. `apiserver`Azure Arc 지원 Kubernetes 클러스터의 에 액세스하면 다음과 같은 시나리오가 가능합니다.
* 대화형 디버깅 및 문제 해결을 사용합니다.
* [사용자 지정 위치](custom-locations.md)의 Azure 서비스와 클러스터 기반에 만들어진 다른 리소스에 대한 클러스터 액세스를 제공합니다.

이 기능에 대한 개념적 개요는 [클러스터 연결 - Azure Arc 지원 Kubernetes](conceptual-cluster-connect.md) 문서에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소   

- 버전 >= 2.16.0으로 [Azure CLI 설치 또는 업그레이드](/cli/azure/install-azure-cli)

- `connectedk8s` Azure CLI 확장 버전 1.1.0 이상을 설치합니다.

    ```console
    az extension add --name connectedk8s
    ```
  
    `connectedk8s` 확장을 이미 설치한 경우 최신 버전의 확장으로 업데이트합니다.
    
    ```console
    az extension update --name connectedk8s
    ```

- 기존 Azure Arc 지원 Kubernetes 연결 클러스터.
    - 아직 클러스터를 연결하지 않은 경우 [빠른 시작](quickstart-connect-cluster.md)을 사용하세요.
    - [에이전트](agent-upgrade.md#manually-upgrade-agents)를 버전 >= 1.1.0으로 업그레이드합니다.

- [Kubernetes 클러스터를 Azure Arc에 연결](quickstart-connect-cluster.md#meet-network-requirements)에서 언급한 것 외에도 아웃바운드 액세스를 위해 아래 엔드포인트를 사용하도록 설정합니다.

    | 엔드포인트 | 포트 |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

- 자리 표시자를 바꾸고 아래 명령을 실행하여 이 문서에 사용되는 환경 변수를 설정합니다.

    ```console
    CLUSTER_NAME=<cluster-name>
    RESOURCE_GROUP=<resource-group-name>
    ARM_ID_CLUSTER=$(az connectedk8s show -n $CLUSTER_NAME -g $RESOURCE_GROUP --query id -o tsv)
    ```


## <a name="enable-cluster-connect-feature"></a>클러스터 커넥트 기능 사용

파일이 문제의 클러스터를 가리키는 컴퓨터에서 다음 명령을 실행하여 Azure Arc 지원 Kubernetes 클러스터에서 클러스터 커넥트 사용하도록 설정할 수 `kubeconfig` 있습니다.

```console
az connectedk8s enable-features --features cluster-connect -n $CLUSTER_NAME -g $RESOURCE_GROUP
```

## <a name="azure-active-directory-authentication-option"></a>Azure Active Directory 인증 옵션

1. Azure `objectId` AD 엔터티와 연결된 를 얻습니다.

    - Azure AD 사용자 계정의 경우:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad signed-in-user show --query objectId -o tsv)
        ```

    - Azure AD 애플리케이션의 경우:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad sp show --id <id> --query objectId -o tsv)
        ```

1. 적절한 권한으로 AAD 엔터티에 권한을 부여합니다.

    - 클러스터에서 권한 부여 검사를 위해 Kubernetes 네이티브 ClusterRoleBinding 또는 RoleBinding을 사용하는 경우 직접 액세스를 위해 클러스터의 를 가리키는 파일을 사용하여 `kubeconfig` `apiserver` 이 클러스터에 액세스해야 하는 Azure AD 엔터티(서비스 주체 또는 사용자)에 매핑된 파일을 만들 수 있습니다. 예:
    
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=$AAD_ENTITY_OBJECT_ID
        ```

    - 클러스터에서 권한 부여 확인을 위해 Azure RBAC를 사용하는 경우 Azure AD 엔터티에 매핑된 Azure 역할 할당을 만들 수 있습니다. 예:

        ```console
        az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee $AAD_ENTITY_OBJECT_ID --scope $ARM_ID_CLUSTER
        ```

## <a name="service-account-token-authentication-option"></a>서비스 계정 토큰 인증 옵션

1. Kubernetes 클러스터 `apiserver`를 가리키는 `kubeconfig` 파일을 사용하여 임의의 네임스페이스에 서비스 계정을 만듭니다(다음 명령에서는 default 네임스페이스에 만듦).

    ```console
    kubectl create serviceaccount admin-user
    ```

1. ClusterRoleBinding 또는 RoleBinding을 만들어 이 [서비스 계정에 클러스터 에 대한 적절한 권한을 부여합니다.](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding) 예:

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. 다음 명령을 사용하여 서비스 계정의 토큰을 가져옵니다.

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

## <a name="access-your-cluster"></a>클러스터 액세스

1. 사용되는 인증 옵션을 기반으로 클러스터에 액세스하는 데 필요한 클러스터 커넥트 기반 kubeconfig를 설정합니다.

    - Azure Active Directory 인증 옵션을 사용하는 경우 관심 있는 Azure AD 엔터티를 사용하여 Azure CLI 로그인한 후 클러스터 `kubeconfig` 주변 방화벽 외부에서도 어디서나 클러스터와 통신하는 데 필요한 클러스터 커넥트 얻습니다.

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP
        ```

    - 서비스 계정 인증 옵션을 사용하는 경우 `kubeconfig` 어디서나 클러스터와 통신하는 데 필요한 클러스터 커넥트.

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP --token $TOKEN
        ```

1. `kubectl`을 사용하여 클러스터로 요청을 보냅니다.

    ```console
    kubectl get pods
    ```
    
    이제 `default` 네임스페이스 아래의 모든 Pod 목록이 포함된 클러스터의 응답이 표시되어야 합니다.

## <a name="known-limitations"></a>알려진 제한 사항

Kubernetes 클러스터에 대한 요청을 수행할 때 사용된 Azure AD 엔터티가 200개가 넘는 그룹의 멤버에 해당하는 경우 알려진 제한 사항인 다음 오류가 나타납니다.

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

이 오류가 발생하지 않도록 하려면 다음을 수행합니다.
1. 200개가 넘는 그룹의 멤버일 가능성이 낮은 [서비스 주체](/cli/azure/create-an-azure-service-principal-azure-cli)를 만듭니다.
1. `az connectedk8s proxy` 명령을 실행하기 전에 이 서비스 주체를 사용하여 Azure CLI에 [로그인](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> 클러스터에서 [Azure AD RBAC](azure-rbac.md) 설정
