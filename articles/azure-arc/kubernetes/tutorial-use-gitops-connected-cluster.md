---
title: '자습서: Azure Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 구성 배포'
description: 이 자습서에서는 Azure Arc 지원 Kubernetes 클러스터에 구성을 적용하는 방법을 보여줍니다. 이 프로세스에 대한 개념은 구성 및 GitOps - Azure Arc 지원 Kubernetes 문서를 참조하세요.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: c98a912394bf660f7a2f09c25c10629fd9810434
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098306"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>자습서: Azure Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 구성 배포 

이 자습서에서는 Azure Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 구성을 적용합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 예제 Git 리포지토리를 사용하여 Azure Arc 지원 Kubernetes 클러스터에 대한 구성을 만듭니다.
> * 구성이 성공적으로 만들어졌는지 확인합니다.
> * 프라이빗 Git 리포지토리에서 구성을 적용합니다.
> * Kubernetes 구성의 유효성을 검사합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 기존 Azure Arc 지원 Kubernetes 연결 클러스터.
    - 아직 클러스터를 연결하지 않은 경우 [Azure Arc 지원 Kubernetes 클러스터 연결 빠른 시작](quickstart-connect-cluster.md)을 살펴보세요.
- 이 기능의 이점과 아키텍처 이해. [구성 및 GitOps - Azure Arc 지원 Kubernetes 문서](conceptual-configurations.md)에서 자세히 알아보세요.
- `k8s-configuration` Azure CLI 확장 버전 >= 1.0.0을 설치합니다.
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > `k8s-configuration` 확장이 이미 설치되어 있는 경우 다음 명령 - `az extension update --name k8s-configuration`을 사용하여 최신 버전으로 업데이트할 수 있습니다.

- Git 리포지토리가 방화벽 외부에 있고 git 프로토콜이 구성 리포지토리 매개 변수와 함께 사용되는 경우 방화벽에서 송신 액세스를 위해 포트 9418(`git://:9418`)의 TCP를 사용하도록 설정해야 합니다.

## <a name="create-a-configuration"></a>구성 만들기

이 문서에 사용된 [예제 리포지토리](https://github.com/Azure/arc-k8s-demo)는 클러스터 연산자의 가상 사용자를 중심으로 구성됩니다. 이 리포지토리의 매니페스트는 몇 가지 네임스페이스를 프로비저닝하고, 워크로드를 배포하고, 몇 가지 팀 특정 구성을 제공합니다. GitOps와 함께 이 리포지토리를 사용하면 클러스터에 다음 리소스가 만들어집니다.

* 네임스페이스: `cluster-config`, `team-a`, `team-b`
* 배포: `arc-k8s-demo`
* ConfigMap: `team-a/endpoints`

`config-agent`는 새 구성 또는 업데이트된 구성에 대해 Azure를 폴링합니다. 이 작업에는 최대 5분이 소요됩니다.

프라이빗 리포지토리를 구성과 연결하는 경우 [프라이빗 Git 리포지토리에서 구성 적용](#apply-configuration-from-a-private-git-repository)의 아래 단계를 완료해야 합니다.

## <a name="use-azure-cli"></a>Azure CLI 사용
`k8s-configuration`에 대한 Azure CLI 확장을 사용하여 연결된 클러스터를 [예제 Git 리포지토리](https://github.com/Azure/arc-k8s-demo)에 연결합니다. 
1. 이 구성의 이름을 `cluster-config`로 지정합니다.
1. `cluster-config` 네임스페이스에 연산자를 배포하도록 에이전트에 지시합니다.
1. 운영자 `cluster-admin` 권한 부여

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
    }
    ```

### <a name="use-a-public-git-repository"></a>공용 Git 리포지토리 사용

| 매개 변수 | 형식 |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] 또는 git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>SSH 및 Flux에서 만든 키를 사용하여 프라이빗 Git 리포지토리 사용

Flux에 의해 생성된 공개 키를 Git 서비스 공급자의 사용자 계정에 추가합니다. 사용자 계정 대신 키가 리포지토리에 추가되면 URL에서 `user@` 대신 `git@`를 사용합니다. 

자세한 내용은 [프라이빗 Git 리포지토리에서 구성 적용](#apply-configuration-from-a-private-git-repository) 섹션으로 이동하세요.


| 매개 변수 | 형식 | 메모
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] 또는 user@server:repo[.git] | `git@`가 `user@`를 대체할 수 있습니다.

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>SSH 및 사용자 제공 키를 사용하여 프라이빗 Git 리포지토리 사용

프라이빗 키를 직접 또는 파일로 제공합니다. 키는 [PEM 형식](https://aka.ms/PEMformat)이어야 하며 줄 바꿈(\n)으로 끝나야 합니다. 

Git 서비스 공급자의 사용자 계정에 연결된 공개 키를 추가합니다. 사용자 계정 대신 키가 리포지토리에 추가되면 `user@` 대신 `git@`를 사용합니다. 

자세한 내용은 [프라이빗 Git 리포지토리에서 구성 적용](#apply-configuration-from-a-private-git-repository) 섹션으로 이동하세요.  

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] 또는 user@server:repo[.git] | `git@`가 `user@`를 대체할 수 있습니다. |
| `--ssh-private-key` | [PEM 형식의](https://aka.ms/PEMformat) base64 인코딩 키 | 키 직접 제공 |
| `--ssh-private-key-file` | 로컬 파일의 전체 경로 | PEM 형식 키를 포함하는 로컬 파일의 전체 경로를 제공합니다.

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>SSH 및 사용자가 제공한 알려진 호스트와 함께 프라이빗 Git 호스트 사용

Flux 연산자는 SSH 연결을 설정하기 전에 Git 리포지토리를 인증하기 위해 알려진 호스트 파일의 일반 Git 호스트 목록을 유지 관리합니다. *일반적이지 않은* Git 리포지토리 또는 사용자 자신의 Git 호스트를 사용하는 경우 Flux가 리포지트리를 식별할 수 있도록 호스트 키를 제공할 수 있습니다. 

프라이빗 키와 마찬가지로 known_hosts 콘텐츠를 직접 또는 파일에 제공할 수 있습니다. 사용자 자신의 콘텐츠를 제공하는 경우 위의 SSH 키 시나리오 중 하나와 함께 [known_hosts 콘텐츠 형식 사양](https://aka.ms/KnownHostsFormat)을 사용합니다.

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] 또는 user@server:repo[.git] | `git@`가 `user@`를 대체할 수 있습니다. |
| `--ssh-known-hosts` | base64로 인코딩됨 | 알려진 호스트 콘텐츠 직접 제공 |
| `--ssh-known-hosts-file` | 로컬 파일의 전체 경로 | 로컬 파일에 알려진 호스트 콘텐츠 제공 |

### <a name="use-a-private-git-repository-with-https"></a>HTTPS를 사용하여 프라이빗 Git 리포지토리 사용

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo [.git] | 기본 인증을 사용하는 HTTPS |
| `--https-user` | 원시 또는 base64로 인코딩됨 | HTTPS 사용자 이름 |
| `--https-key` | 원시 또는 base64로 인코딩됨 | HTTPS 개인용 액세스 토큰 또는 암호

>[!NOTE]
>* Helm 연산자 차트 버전 1.2.0+는 HTTPS Helm 릴리스 프라이빗 인증을 지원합니다.
>* AKS 관리형 클러스터에는 HTTPS Helm 릴리스가 지원되지 않습니다.
>* 프록시를 통해 Git 리포지토리에 액세스하는 데 Flux가 필요한 경우 Azure Arc 에이전트를 프록시 설정으로 업데이트해야 합니다. 자세한 내용은 [아웃바운드 프록시 서버를 사용하여 연결](./quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)을 참조하세요.


## <a name="additional-parameters"></a>추가 매개 변수

다음과 같은 선택적 매개 변수를 사용하여 구성을 사용자 지정합니다.

| 매개 변수 | 설명 |
| ------------- | ------------- |
| `--enable-helm-operator`| Helm 차트 배포에 대한 지원을 사용하도록 설정하는 스위치. |
| `--helm-operator-params` | Helm 연산자(사용 설정된 경우)에 대한 차트 값. 예들 들어 `--set helm.versions=v3`입니다. |
| `--helm-operator-chart-version` | Helm 연산자(사용 설정된 경우)에 대한 차트 버전. 버전 1.2.0+를 사용합니다. 기본값: '1.2.0'. |
| `--operator-namespace` | 연산자 네임스페이스의 이름. 기본값: 'default'. 최대: 23자. |
| `--operator-params` | 연산자에 대한 매개 변수. 작은따옴표 안에 지정해야 합니다. 예를 들어 ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>`--operator-params`에서 지원되는 옵션:

| 옵션 | 설명 |
| ------------- | ------------- |
| `--git-branch`  | Kubernetes 매니페스트에 사용할 Git 리포지토리의 분기. 기본값은 'master'입니다. 최신 리포지토리에는 `main`이라는 루트 분기가 있으며, 이 경우 `--git-branch=main`을 설정해야 합니다. |
| `--git-path`  | Kubernetes 매니페스트를 찾는 Flux에 대한 Git 리포지토리 내의 상대 경로. |
| `--git-readonly` | Git 리포지토리는 읽기 전용으로 간주됩니다. Flux는 여기에 쓰려 하지 않을 것입니다. |
| `--manifest-generation`  | 사용 설정된 경우 Flux는 .flux.yaml을 찾고 Kustomize 또는 기타 매니페스트 생성기를 실행합니다. |
| `--git-poll-interval`  | 새 커밋을 위해 Git 리포지토리를 폴링하는 기간. 기본값은 `5m`(5분)입니다. |
| `--sync-garbage-collection`  | 사용 설정된 경우 Flux는 만들었지만 더 이상 Git에 존재하지 않는 리소스를 삭제합니다. |
| `--git-label`  | 동기화 진행 상황을 추적하기 위한 레이블. Git 분기에 태그를 지정하는 데 사용됩니다.  기본값은 `flux-sync`입니다. |
| `--git-user`  | Git 커밋의 사용자 이름. |
| `--git-email`  | Git 커밋에 사용할 이메일. 

Flux가 리포지토리에 쓰지 않도록 하고 `--git-user` 또는 `--git-email`이 설정되지 않은 경우 `--git-readonly`가 자동으로 설정됩니다.

자세한 내용은 [Flux 설명서](https://aka.ms/FluxcdReadme)를 참조하세요.

>[!NOTE]
> Flux는 기본적으로 git 리포지토리의 `master` 분기에서 동기화됩니다. 그러나 최신 git 리포지토리에는 `main`이라는 루트 브랜치가 있으며 이 경우 --operator-params에서 `--git-branch=main`을 설정해야 합니다. 

> [!TIP]
> Azure Arc 지원 Kubernetes 리소스의 **GitOps** 탭에 있는 Azure Portal에서 구성을 만들 수 있습니다.

## <a name="validate-the-configuration"></a>구성 유효성 검사

Azure CLI를 사용하여 구성이 성공적으로 만들어졌는지 확인합니다.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

구성 리소스는 규정 준수 상태, 메시지 및 디버깅 정보로 업데이트됩니다.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

구성을 만들거나 업데이트하면 몇 가지 상황이 발생합니다.

1. Azure Arc `config-agent`는 새 구성 또는 업데이트된 구성(`Microsoft.KubernetesConfiguration/sourceControlConfigurations`)에 대해 Azure Resource Manager를 모니터링하고 새 `Pending` 구성을 확인합니다.
1. `config-agent`는 구성 속성을 읽고 대상 네임스페이스를 만듭니다.
1. Azure Arc `controller-manager`는 Kubernetes 서비스 계정을 만들고 적절한 권한(`cluster` 또는 `namespace` 범위)에 대한 [ClusterRoleBinding 또는 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)에 매핑합니다. 그런 다음, `flux`의 인스턴스를 배포합니다.
1. Flux 생성 키와 함께 SSH 옵션을 사용하는 경우 `flux`는 SSH 키를 생성하고 공개 키를 기록합니다.
1. `config-agent`는 Azure의 구성 리소스에 상태를 다시 보고합니다.

프로비저닝 프로세스가 진행되는 동안 구성 리소스는 몇 가지 상태 변경을 거칩니다. 위의 `az k8s-configuration show ...` 명령을 사용하여 진행률을 모니터링합니다.

| 스테이지 변경 | 설명 |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | 초기 및 진행 중인 상태를 나타냅니다. |
| `complianceStatus` -> `Installed`  | `config-agent`는 클러스터를 구성했고 오류 없이 `flux`를 배포했습니다. |
| `complianceStatus` -> `Failed` | `config-agent`는 `flux`를 배포하는 동안 오류가 발생했습니다. 세부 정보는 `complianceStatus.message` 응답 본문에 제공됩니다. |

## <a name="apply-configuration-from-a-private-git-repository"></a>프라이빗 Git 리포지토리에서 구성 적용

프라이빗 Git 리포지토리를 사용하는 경우 리포지토리에 SSH 공개 키를 구성해야 합니다. 사용자가 제공하거나 Flux에서 SSH 공개 키를 생성합니다. 특정 Git 리포지토리 또는 리포지토리에 대한 액세스 권한이 있는 Git 사용자에 대해 공개 키를 구성할 수 있습니다. 

### <a name="get-your-own-public-key"></a>사용자 자신의 공개 키 가져오기

사용자 자신의 SSH 키를 생성한 경우에는 프라이빗 키와 공개 키가 이미 있습니다.

#### <a name="get-the-public-key-using-azure-cli"></a>Azure CLI를 사용하여 공개 키 가져오기 

Flux에서 키를 생성하는 경우 Azure CLI에서 다음을 사용합니다.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Azure Portal에서 공개 키 가져오기

Flux에서 키를 생성하는 경우 Azure Portal에서 다음을 수행합니다.

1. Azure Portal에서 연결된 클러스터 리소스로 이동합니다.
2. 리소스 페이지에서 "GitOps"를 선택하고 이 클러스터에 대한 구성 목록을 확인합니다.
3. 프라이빗 Git 리포지토리를 사용하는 구성을 선택합니다.
4. 열린 컨텍스트 창의 맨 아래에서 **리포지토리 공개 키** 를 복사합니다.

#### <a name="add-public-key-using-github"></a>GitHub를 사용하여 공개 키 추가

다음 옵션 중 하나를 사용합니다.

* 옵션 1: 사용자 계정에 공개 키를 추가합니다(계정의 모든 리포지토리에 적용됨).  
    1. GitHub를 열고 페이지 오른쪽 위 모서리에 있는 프로필 아이콘을 클릭합니다.
    2. **설정** 을 클릭합니다.
    3. **SSH 및 GPG 키** 를 클릭합니다.
    4. **새 SSH 키** 를 클릭합니다.
    5. 제목을 제공합니다.
    6. 따옴표 없이 공개 키를 붙여넣습니다.
    7. **SSH 키 추가** 를 클릭합니다.

* 옵션 2: 공개 키를 Git 리포지토리에 배포 키로 추가합니다(이 리포지토리에만 적용됨).  
    1. GitHub를 열고 리포지토리로 이동합니다.
    1. **설정** 을 클릭합니다.
    1. **키 배포** 를 클릭합니다.
    1. **키 배포 추가** 를 클릭합니다.
    1. 제목을 제공합니다.
    1. **쓰기 액세스 허용** 을 선택합니다.
    1. 따옴표 없이 공개 키를 붙여넣습니다.
    1. **키 추가** 를 클릭합니다.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Azure DevOps 리포지토리를 사용하여 공개 키 추가

SSH 키에 키를 추가하려면 다음 단계를 사용합니다.

1. 오른쪽 위(프로필 이미지 옆)에 있는 **사용자 설정** 에서 **SSH 공개 키** 를 클릭합니다.
1. **+ 새 키** 를 선택합니다.
1. 이름을 제공합니다.
1. 따옴표 없이 공개 키를 붙여넣습니다.
1. **추가** 를 클릭합니다.

## <a name="validate-the-kubernetes-configuration"></a>Kubernetes 구성의 유효성을 검사합니다.

`config-agent`가 `flux` 인스턴스를 설치한 후 Git 리포지토리에 보관된 리소스가 클러스터로 이동하기 시작해야 합니다. 다음 명령을 사용하여 네임스페이스, 배포 및 리소스가 만들어졌는지 확인합니다.

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

`team-a`, `team-b`, `itops` 및 `cluster-config` 네임스페이스가 만들어진 것을 확인할 수 있습니다.

구성 리소스의 지시에 따라 `flux` 연산자가 `cluster-config` 네임스페이스에 배치되었습니다.

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>추가로 알아보기

다음을 사용하여 구성 리포지토리의 일부로 배포된 다른 리소스를 살펴볼 수 있습니다.

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>리소스 정리

Azure CLI 또는 Azure Portal을 사용하여 구성을 삭제합니다. delete 명령을 실행하면 Azure에서 구성 리소스가 즉시 삭제됩니다. 클러스터에서 연결된 개체를 완전히 삭제하면 10분 이내에 삭제됩니다. 제거할 때 구성이 실패한 상태인 경우 연결된 개체를 완전히 삭제하는 데 최대 1시간이 걸릴 수 있습니다.

범위가 `namespace`인 구성이 삭제되면 Azure Arc는 기존 워크로드가 중단되는 것을 방지하기 위해 네임스페이스를 삭제하지 않습니다. 필요한 경우 `kubectl`을 사용하여 이 네임스페이스를 수동으로 삭제할 수 있습니다.

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> 추적된 Git 리포지토리에서 배포한 결과로 클러스터에 변경된 내용은 구성이 삭제될 때 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

GitOps를 통해 CI/CD를 구현하는 방법을 알아보려면 다음 자습서로 이동합니다.
> [!div class="nextstepaction"]
> [GitOps를 통한 CI/CD 구현](./tutorial-gitops-ci-cd.md)
