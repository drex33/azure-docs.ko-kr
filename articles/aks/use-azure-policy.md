---
title: Azure Policy를 사용하여 클러스터 보호
description: Azure Policy를 사용하여 AKS(Azure Kubernetes Service) 클러스터를 보호합니다.
ms.service: container-service
ms.topic: how-to
ms.date: 09/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eb2f85064413f3d4700fea01aa4ead81508dd8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631498"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Azure Policy를 사용하여 클러스터 보호

AKS(Azure Kubernetes Service) 클러스터의 보안을 강화하기 위해 Azure Policy를 사용하여 클러스터에서 기본 제공 보안 정책을 적용할 수 있습니다. [Azure Policy][azure-policy]를 사용하면 조직의 표준을 적용하고 규정 준수를 대규모로 평가할 수 있습니다. [AKS용 Azure Policy 추가 기능][kubernetes-policy-reference]을 설치한 후 개별 정책 정의 또는 이니셔티브(정책 세트라고도 함)라는 정책 정의 그룹을 클러스터에 적용할 수 있습니다. AKS 정책 및 이니셔티브 정의의 전체 목록은 [AKS에 대한 Azure Policy 기본 제공 정의][aks-policies]를 참조하세요.

이 문서에서는 클러스터에 정책 정의를 적용하고 해당 할당이 적용되는지 확인하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- 기존 AKS 클러스터 AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.
- AKS 클러스터에 설치된 AKS용 Azure Policy 추가 기능입니다. 다음과 같이 [Azure Policy 추가 기능을 설치하는 단계][azure-policy-addon]를 수행합니다.

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>기본 제공 정책 정의 또는 이니셔티브 할당

정책 정의 또는 이니셔티브를 적용하려면 Azure Portal을 사용합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. Azure Policy 페이지의 왼쪽 창에서 **정의** 를 선택합니다.
1. **범주** 에서 `Kubernetes`를 선택합니다.
1. 적용하려는 정책 정의 또는 이니셔티브를 선택합니다. 이 예제에서는 `Kubernetes cluster pod security baseline standards for Linux-based workloads` 이니셔티브를 선택합니다.
1. **할당** 을 선택합니다.
1. Azure Policy 추가 기능을 사용하도록 설정하고 **범위** 를 AKS 클러스터의 리소스 그룹으로 설정합니다.
1. **매개 변수** 페이지를 선택하고 **효과** 를 `audit`에서 `deny`로 업데이트하여 기준 이니셔티브를 위반하는 새 배포를 차단합니다. 평가에서 제외할 네임스페이스를 더 추가할 수도 있습니다. 이 예제의 경우 기본값을 유지합니다.
1. **검토 + 만들기**, **만들기** 를 차례로 선택하여 정책 할당을 제출합니다.

## <a name="create-and-assign-a-custom-policy-definition-preview"></a>사용자 지정 정책 정의 만들기 및 할당(미리 보기)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

사용자 지정 정책을 사용하면 Azure 사용에 대한 규칙을 정의할 수 있습니다. 예를 들어 다음을 적용할 수 있습니다.
- 보안 관행
- 비용 관리
- 조직별 규칙(예: 이름 지정 또는 위치)

사용자 지정 정책을 만들기 전에 [일반적인 패턴 및 샘플 목록을][azure-policy-samples] 확인하여 사례가 이미 검사되었는지 확인합니다.

사용자 지정 정책 정의는 JSON으로 작성됩니다. 사용자 지정 정책을 만드는 방법에 대한 자세한 내용은 [Azure Policy 정의 구조][azure-policy-definition-structure] 및 사용자 지정 정책 정의 [만들기를 참조하세요.][custom-policy-tutorial-create]

> [!NOTE]
> 이제 Azure Policy 사용자가 제약 조건 템플릿의 소스 형식을 정의할 수 있도록 *templateInfo라는* 새 속성을 활용합니다. 정책 정의에서 *templateInfo를* 정의하면 사용자가 *constraintTemplate* 또는 *제약 조건* 속성을 정의할 필요가 없습니다. 사용자는 여전히 *apiGroups* 및 종류를 정의해야 *합니다.* 이에 대한 자세한 내용은 [Azure Policy 효과 이해를 참조하세요.][azure-policy-effects-audit]

사용자 지정 정책 정의가 만들어지면 Kubernetes 클러스터에 정책을 할당하는 단계별 연습에 대한 정책 [정의][custom-policy-tutorial-assign] 할당을 참조하세요.

## <a name="validate-a-azure-policy-is-running"></a>Azure Policy 실행 중인지 유효성 검사

다음을 실행하여 정책 할당이 클러스터에 적용되는지 확인합니다.

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> 정책 할당은 각 클러스터에 [동기화하는 데 최대 20분][azure-policy-assign-policy]이 걸릴 수 있습니다.

출력은 다음과 비슷해야 합니다.

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>권한 있는 Pod 거부 유효성 검사

먼저 `privileged: true`의 보안 컨텍스트를 사용하여 Pod를 예약할 때 발생하는 결과를 테스트하겠습니다. 이 보안 컨텍스트는 Pod의 권한을 에스컬레이션합니다. 이니셔티브는 권한 있는 Pod를 허용하지 않으므로 요청이 거부되어 배포가 거부됩니다.

`nginx-privileged.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f nginx-privileged.yaml
```

예상대로 다음 예제 출력과 같이 Pod가 예약되지 않습니다.

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Pod가 예약 단계에 도달하지 않으므로 계속 진행하기 전에 삭제할 리소스가 없습니다.

### <a name="test-creation-of-an-unprivileged-pod"></a>권한 없는 Pod 만들기 테스트

이전 예제에서 컨테이너 이미지는 자동으로 루트를 사용하여 NGINX를 포트 80에 바인딩하려고 했습니다. 정책 이니셔티브가 이 요청을 거부했으므로 Pod가 시작되지 않습니다. 이제 권한 있는 액세스 없이 동일한 NGINX Pod를 실행해 보겠습니다.

`nginx-unprivileged.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pod가 성공적으로 예약되었습니다. [kubectl get pods][kubectl-get] 명령을 사용하여 Pod의 상태를 확인하는 경우 Pod는 ‘실행 중’ 상태입니다.

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

이 예제에서는 컬렉션에서 정책을 위반하는 배포에만 영향을 주는 기준 이니셔티브를 보여 줍니다. 허용되는 배포는 계속 작동합니다.

[kubectl delete][kubectl-delete] 명령을 사용하여 NGINX 권한이 없는 Pod를 삭제하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>정책 또는 이니셔티브 사용 안 함

기준 이니셔티브를 제거하려면:

1. Azure Portal의 정책 창으로 이동합니다.
1. 왼쪽 창에서 **할당** 을 선택합니다.
1. `Kubernetes cluster pod security baseline standards for Linux-based workloads` 이니셔티브 옆에 있는 **...** 단추를 클릭합니다.
1. **할당 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Policy 작동 방식에 관한 자세한 내용은 다음을 참조하세요.

- [Azure Policy 개요][azure-policy]
- [AKS에 대한 Azure Policy 이니셔티브 및 정책][aks-policies]
- [Azure Policy 추가 기능][azure-policy-addon-remove]을 제거합니다.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[azure-policy-effects-audit]: ../governance/policy/concepts/effects.md#audit-properties
[custom-policy-tutorial-create]: ../governance/policy/tutorials/create-custom-policy-definition.md
[custom-policy-tutorial-assign]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-policy-definition
[azure-policy-samples]: ../governance/policy/samples/index.md
[azure-policy-definition-structure]: ../governance/policy/concepts/definition-structure.md