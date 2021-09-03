---
title: 클러스터 보안에 대한 모범 사례
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Services)에서 클러스터 보안 및 업그레이드를 관리하는 방법에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 7560e9aaabf8b21729e1e9d8e008c0b6a0e8cefb
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453325"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 클러스터 보안 및 업그레이드 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 관리할 때 워크로드 및 데이터 보안이 주요 고려 사항입니다. 논리적 격리를 사용하여 다중 테넌트 클러스터를 실행하는 경우 특히 리소스 및 워크로드 액세스를 보호해야 합니다. 최신 Kubernetes 및 노드 OS 보안 업데이트를 적용하여 공격 위험을 최소화합니다.

이 문서에서는 AKS 클러스터의 보안을 유지하는 방법을 중점적으로 설명합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Active Directory 및 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 사용하여 API 서버 액세스 보호
> * 노드 리소스에 대한 컨테이너 액세스 보안 유지
> * AKS 클러스터를 최신 Kubernetes 버전으로 업그레이드
> * 노드를 최신 상태로 유지하고 보안 패치를 자동으로 적용

[컨테이너 이미지 관리][best-practices-container-image-management] 및 [Pod 보안][best-practices-pod-security]에 대한 모범 사례를 참조할 수도 있습니다.

또한 [Azure Kubernetes Services와 Security Center 통합][security-center-aks]을 사용하여 AKS 클러스터 보안을 위한 위협을 감지하고 권장 사항을 볼 수 있습니다.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>API 서버 및 클러스터 노드에 대한 액세스 보안 유지

> **모범 사례 지침** 
>
> 클러스터를 보호하는 가장 중요한 방법 중 하나는 Kubernetes API 서버에 대한 액세스를 보호하는 것입니다. API 서버에 대한 액세스를 제어하려면 Kubernetes RBAC를 Azure AD(Azure Active Directory)와 통합합니다. 이러한 제어를 통해 Azure 구독에 대한 액세스를 보호하는 것과 동일한 방식으로 AKS를 보호할 수 있습니다.

Kubernetes API 서버는 클러스터 내의 작업 수행 요청에 대한 단일 연결점을 제공합니다. API 서버에 대한 액세스를 보호하고 감사하려면 액세스를 제한하고 가능한 가장 낮은 권한 수준을 제공합니다. 이 방식이 Kubernetes에만 해당하는 것은 아니지만 다중 테넌트 사용을 위해 AKS 클러스터를 논리적으로 격리한 경우에 특히 중요합니다.

Azure AD는 AKS 클러스터와 통합되는 엔터프라이즈급 ID 관리 솔루션을 제공합니다. Kubernetes는 ID 관리 솔루션을 제공하지 않기 때문에 API 서버에 대한 액세스를 세부적으로 제한하기가 어려울 수 있습니다. AKS에서 Azure AD 통합 클러스터를 사용하는 경우 기존 사용자 및 그룹 계정을 사용하여 API 서버에서 사용자를 인증합니다.

![AKS 클러스터와의 Azure Active Directory 통합](media/operator-best-practices-cluster-security/aad-integration.png)

Kubernetes RBAC 및 Azure AD 통합을 사용하면 API 서버를 보호하고 단일 네임스페이스와 같이 범위가 지정된 리소스 세트에 필요한 최소 권한을 제공할 수 있습니다. 다른 Azure AD 사용자 또는 그룹에 다른 Kubernetes 역할을 부여할 수 있습니다. 세분화된 권한을 사용하여 API 서버에 대한 액세스를 제한하고 수행된 작업에 대한 명확한 감사 추적을 제공할 수 있습니다.

권장되는 모범 사례는 그룹을 사용하여 개별 ID 대신 파일 및 폴더에 대한 액세스를 제공하는 것입니다. 예를 들어 Azure AD 그룹 멤버 자격을 사용하여 개별 사용자가 아닌 Kubernetes 역할에 사용자를 바인딩합니다.  사용자의 그룹 멤버 자격이 변경되면 AKS 클러스터에 대한 해당 액세스 권한도 그에 따라 변경됩니다. 

한편 개별 사용자를 역할에 직접 바인딩하고 작업 함수가 변경된다고 가정해 보겠습니다. Azure AD 그룹 멤버 자격이 업데이트되어도 AKS 클러스터에 대한 권한은 업데이트되지 않습니다. 이런 경우 사용자는 필요한 것보다 더 많은 권한을 갖게 됩니다.

Azure AD 통합, Kubernetes RBAC 및 Azure RBAC에 대한 자세한 내용은 [AKS의 인증 및 권한 부여 모범 사례][aks-best-practices-identity]를 참조하세요.

## <a name="secure-container-access-to-resources"></a>리소스에 대한 컨테이너 액세스 보호

> **모범 사례 지침** 
> 
> 컨테이너가 수행할 수 있는 작업에 대한 액세스를 제한합니다. 최소한의 권한을 제공하고 루트 액세스 또는 권한 상승을 사용하지 마십시오.

사용자나 그룹에 필요한 최소한의 권한을 부여해야 하듯이 컨테이너를 필요한 작업과 프로세스로만 제한해야 합니다. 공격 위험을 최소화하려면 상승된 권한이나 루트 액세스가 필요한 애플리케이션 및 컨테이너를 구성하지 마십시오. 

예를 들어 pod 매니페스트에서 `allowPrivilegeEscalation: false`를 설정합니다. 이러한 기본 제공 Kubernetes Pod 보안 컨텍스트를 사용하면 실행 시 사용할 사용자 또는 그룹과 같은 추가 권한 또는 노출할 Linux 기능을 정의할 수 있습니다. 추가 모범 사례에 대해서는 [리소스에 대한 pod 액세스 보안 유지][pod-security-contexts]를 참조하세요.

컨테이너 작업을 훨씬 더 세부적으로 제어하기 위해 *AppArmor* 및 *seccomp* 와 같은 기본 제공 Linux 보안 기능을 사용할 수도 있습니다. 
1. 노드 수준에서 Linux 보안 기능을 정의합니다.
1. Pod 매니페스트를 통해 기능을 구현합니다. 

Linux 노드 및 pod에서만 기본 제공 Linux 보안 기능을 사용할 수 있습니다.

> [!NOTE]
> 현재 Kubernetes 환경은 악의적인 다중 테넌트 사용에 대해 완전히 안전하지 않습니다. *AppArmor*, *seccomp*,*Pod* 보안 정책 또는 노드용 Kubernetes RBAC와 같은 추가 보안 기능을 통해 익스플로잇을 효율적으로 차단할 수 있습니다. 
>
>악의적인 다중 테넌트 워크로드를 실행할 때의 진정한 보안을 위해서는 하이퍼바이저만 신뢰하세요. Kubernetes의 보안 도메인은 개별 노드가 아닌 전체 클러스터가 됩니다. 
>
> 이러한 유형의 악의적인 다중 테넌트 워크로드의 경우 물리적으로 격리된 클러스터를 사용해야 합니다.

### <a name="app-armor"></a>App Armor

컨테이너 작업을 제한하기 위해 [AppArmor][k8s-apparmor] Linux 커널 보안 모듈을 사용할 수 있습니다. AppArmor는 기본 AKS 노드 OS의 일부로 사용할 수 있으며 기본적으로 사용하도록 설정됩니다. 읽기, 쓰기 또는 실행 작업이나 파일 시스템 탑재와 같은 시스템 함수를 제한하는 AppArmor 프로필을 만듭니다. 기본 AppArmor 프로필은 다양한 `/proc` 및 `/sys` 위치에 대한 액세스를 제한하며, 기본 노드에서 컨테이너를 논리적으로 격리하는 방법을 제공합니다. AppArmor는 Kubernetes pod 뿐 아니라 Linux에서 실행되는 모든 애플리케이션에 작동합니다.

![AKS 클러스터에서 컨테이너 작업을 제한하기 위해 사용되는 AppArmor 프로필](media/operator-best-practices-container-security/apparmor.png)

작동하는 AppArmor를 볼 수 있도록 하기 위해 다음 예제에서는 파일에 대한 쓰기를 방지하는 프로필을 만듭니다. 
1. AKS 노드에 [SSH][aks-ssh]로 연결합니다.
1. *deny-write.profile* 이라는 파일을 만듭니다.
1. 다음 콘텐츠를 붙여넣습니다.

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

AppArmor 프로필은 `apparmor_parser` 명령을 사용하여 추가됩니다. 
1. AppArmor에 프로필을 추가합니다.
1. 이전 단계에서 만든 프로필의 이름을 지정합니다.

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    프로필이 올바르게 구문 분석되고 AppArmor에 적용되면 출력이 표시되지 않으며 명령 프롬프트로 돌아갑니다.

1. 로컬 컴퓨터에서 *aks-apparmor.yaml* 이라는 Pod 매니페스트를 생성합니다. 이 매니페스트는:
    * `container.apparmor.security.beta.kubernetes`에 대한 주석을 정의합니다.
    * 이전 단계에서 만든 *deny-write* 프로필을 참조합니다.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. pod를 배포한 상태에서 *hello-apparmor* pod가 *차단* 된 것으로 표시되는지 확인합니다.

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

AppArmor에 대한 자세한 내용은 [Kubernetes의 AppArmor 프로필][k8s-apparmor]을 참조하세요.

### <a name="secure-computing"></a>컴퓨팅 보안

AppArmor는 Linux 애플리케이션에 작동하지만 [seccomp(*sec* ure *comp* uting)][seccomp]는 프로세스 수준에서 작동합니다. Seccomp는 또한 Linux 커널 보안 모듈이며, 기본적으로 AKS 노드에 사용되는 Docker 런타임에서 지원됩니다. seccomp를 사용하면 컨테이너 프로세스 호출을 제한할 수 있습니다. 다음을 통해서만 실행할 수 있도록 컨테이너에 최소한의 권한을 부여하는 모범 사례에 맞춰 조정합니다.
* 필터를 사용하여 허용하거나 거부할 작업을 정의합니다.
* seccomp 필터와 연결할 Pod YAML 매니페스트 내에서 주석을 추가합니다. 

작동 중인 seccomp를 보려면 파일에 대한 권한을 변경하지 못하게 하는 필터를 만듭니다. 
1. AKS 노드에 [SSH][aks-ssh]로 연결합니다.
1. */var/lib/kubelet/seccomp/prevent-chmod* 라는 seccomp 필터를 만듭니다.
1. 다음 콘텐츠를 붙여넣습니다.

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    버전 1.19 이상에서는 다음을 구성해야 합니다.

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. 로컬 컴퓨터에서 *aks-seccomp.yaml* 이라는 Pod 매니페스트를 만든 후 다음 콘텐츠를 붙여넣습니다. 이 매니페스트는:
    * `seccomp.security.alpha.kubernetes.io`에 대한 주석을 정의합니다.
    * 이전 단계에서 만든 *prevent-chmod* 필터를 참조합니다.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    버전 1.19 이상에서는 다음을 구성해야 합니다.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. [kubectl apply][kubectl-apply] 명령을 사용하여 샘플 pod를 배포합니다.

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. [kubectl get pods][kubectl-get] 명령을 사용하여 Pod 상태를 봅니다. 
    * Pod는 오류를 보고합니다. 
    * 다음 예제 출력에서처럼 `chmod` 명령이 seccomp 필터에 의해 실행되지 않도록 합니다.    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

사용 가능한 필터에 대한 자세한 내용은 [Docker에 대한 Seccomp 보안 프로필][seccomp]을 참조하세요.

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>최신 버전의 Kubernetes로 정기적으로 업데이트

> **모범 사례 지침** 
> 
> 새로운 기능 및 버그 수정을 최신 상태로 유지하려면 AKS 클러스터에서 Kubernetes 버전을 정기적으로 업그레이드하세요.

Kubernetes는 보다 일반적인 인프라 플랫폼보다 더 빠른 속도로 새 기능을 릴리스합니다. Kubernetes 업데이트에는 다음이 포함됩니다.
* 새로운 기능
* 버그 또는 보안 수정 

새로운 기능은 일반적으로 안정되기 전에 알파 및 베타 상태를 거치게 됩니다.   일단 안정되면 일반 공급되며 프로덕션용으로 권장됩니다. Kubernetes의 새로운 기능 릴리스 주기에 따라 정기적으로 호환성이 손상되는 변경 사항이 발생하거나 배포 및 템플릿을 조정하지 않고도 Kubernetes를 업데이트할 수 있습니다.

AKS는 Kubernetes의 3개의 부 버전을 지원합니다. 새로운 부 패치 버전이 릴리스되면 지원되는 가장 오래된 부 버전 및 패치 릴리스는 사용 중지됩니다. 사소한 Kubernetes 업데이트는 정기적으로 발생합니다. 지원 범위 내에서 유지하려면 필요한 업그레이드를 확인하는 거버넌스 프로세스가 있는지 확인하십시오. 자세한 내용은 [지원되는 Kubernetes 버전 AKS][aks-supported-versions]를 참조하세요.

클러스터에 사용할 수 있는 버전을 확인하려면 다음 예제와 같이 [az aks get-upgrades][az-aks-get-upgrades] 명령을 사용합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

그런 다음, [az aks upgrade][az-aks-upgrade] 명령을 사용하여 AKS 클러스터를 업그레이드할 수 있습니다. 업그레이드 프로세스는 안전하게 다음을 수행합니다.
* 한 번에 하나의 노드를 차단하고 드레이닝합니다.
* 나머지 노드에서 Pod를 예약합니다.
* 최신 OS 및 Kubernetes 버전을 실행하는 새 노드를 배포합니다.

>[!IMPORTANT]
> 개발 테스트 환경에서 새로운 부 버전을 테스트하고 새 Kubernetes 버전을 사용하여 워크로드가 정상 상태로 유지되는지 확인합니다. 
>
> Kubernetes는 워크로드에 사용되는 API(예: 버전 1.16)를 사용 중단 수 있습니다. 새 버전을 프로덕션으로 가져올 때는 [별도의 버전에서 다중 노드 풀](use-multiple-node-pools.md)을 사용하고 개별 풀을 한 번에 하나씩 업그레이드하여 클러스터에서 업데이트를 점진적으로 롤업하세요. 여러 클러스터를 실행하는 경우 한 번에 한 클러스터를 업그레이드하여 영향 또는 변경을 점차적으로 모니터링합니다.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

AKS의 업그레이드에 대한 자세한 내용은 [AKS의 지원되는 Kubernetes 버전][aks-supported-versions] 및 [AKS 클러스터 업그레이드][aks-upgrade]를 참조하세요.

## <a name="process-linux-node-updates"></a>Linux 노드 업데이트 처리

매일 저녁 AKS의 Linux 노드는 배포판 업데이트 채널을 통해 보안 패치를 받습니다. 이 동작은 노드가 AKS 클러스터에 배포되면 자동으로 구성됩니다. 실행 중인 워크로드 중단 및 잠재적인 영향을 최소화하기 위해 보안 패치 또는 커널 업데이트에 필요한 경우에도 노드가 자동으로 다시 부팅되지 않습니다. 노드 다시 부팅을 처리하는 방법에 대한 자세한 내용은 [AKS에서 노드에 보안 및 커널 업데이트 적용][aks-kured]을 참조하세요.

### <a name="node-image-upgrades"></a>노드 이미지 업그레이드

자동 업그레이드는 Linux 노드 OS에 업데이트를 적용하지만 클러스터에 대한 노드를 만드는 데 사용되는 이미지는 변경되지 않습니다. 새 Linux 노드가 클러스터에 추가되면 원래 이미지를 사용하여 노드를 만듭니다. 이 새 노드는 매일 밤 자동 검사 중에 사용 가능한 모든 보안 및 커널 업데이트를 받지만 모든 검사와 다시 시작이 완료될 때까지 패치되지 않습니다. 노드 이미지 업그레이드를 사용하여 클러스터에서 사용하는 노드 이미지를 검사하고 업데이트할 수 있습니다. 노드 이미지 업그레이드에 관한 자세한 내용은 [AKS(Azure Kubernetes Service) 노드 이미지 업그레이드][node-image-upgrade]를 참조하세요.

## <a name="process-windows-server-node-updates"></a>Windows Server 노드 업데이트 처리

Windows Server 노드의 경우 노드 이미지 업그레이드 작업을 정기적으로 수행하여 안전하게 pod를 차단 및 드레이닝하고 업데이트된 노드를 배포합니다.

<!-- EXTERNAL LINKS -->
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
[node-image-upgrade]: node-image-upgrade.md
