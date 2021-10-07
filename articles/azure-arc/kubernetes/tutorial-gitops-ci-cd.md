---
title: '자습서: Azure Arc 지원 Kubernetes 클러스터를 사용하여 GitOps로 CI/CD 구현'
description: 이 자습서에서는 Azure Arc 지원 Kubernetes 클러스터와 함께 GitOps를 사용하여 CI/CD 솔루션을 설정하는 과정을 안내합니다. 이 워크플로의 개념에 대한 내용은 GitOps - Azure Arc 지원 Kubernetes를 사용하는 CI/CD 워크플로 문서를 참조하세요.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: a325f248a4f634657d9b73e3d4b7929e0614a023
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820730"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>자습서: Azure Arc 지원 Kubernetes 클러스터를 사용하여 GitOps로 CI/CD 구현

이 자습서에서는 Azure Arc 지원 Kubernetes 클러스터와 함께 GitOps를 사용하여 CI/CD 솔루션을 설정합니다. Azure Vote 샘플 앱을 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Arc 지원 Kubernetes 클러스터를 만듭니다.
> * 애플리케이션 및 GitOps 리포지토리를 Azure Repos에 연결합니다.
> * CI/CD 파이프라인을 가져옵니다.
> * ACR(Azure Container Registry)을 Azure DevOps 및 Kubernetes에 연결합니다.
> * 환경 변수 그룹을 만듭니다.
> * `dev` 및 `stage` 환경을 배포합니다.
> * 애플리케이션 환경을 테스트합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>시작하기 전에

이 자습서에서는 여러분이 Azure DevOps, Azure Repos 및 Azure CLI에 대해 잘 알고 있다고 가정합니다.

* [Azure DevOps Services](https://dev.azure.com/)에 로그인합니다.
* [이전 자습서](./tutorial-use-gitops-connected-cluster.md)를 완료하여 CI/CD 환경에 사용할 GitOps 배포 방법을 알아봅니다.
* 이 기능의 [이점과 아키텍처](./conceptual-configurations.md)를 이해합니다.
* 다음을 확인합니다.
  * **arc-cicd-cluster** 라는 [Azure Arc 지원 Kubernetes 클러스터가 연결되어 있습니다](./quickstart-connect-cluster.md#3-connect-an-existing-kubernetes-cluster).
  * [AKS 통합](../../aks/cluster-container-registry-integration.md) 또는 [비 AKS 클러스터 인증](../../container-registry/container-registry-auth-kubernetes.md)을 사용하는 ACR(Azure Container Registry)이 연결되어 있습니다.
  * [Azure Repos](/azure/devops/repos/get-started/what-is-repos) 및 [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started)에 대한 "Build Admin" 및 "Project admin" 권한이 있습니다.
* 다음 Azure Arc 지원 Kubernetes CLI 확장 버전 1.0.0 이상을 설치합니다.

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * 확장을 최신 버전으로 업데이트하려면 다음 명령을 실행합니다.

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>애플리케이션 및 GitOps 리포지토리를 Azure Repos로 가져오기

[애플리케이션 리포지토리](./conceptual-gitops-ci-cd.md#application-repo) 및 [GitOps 리포지토리](./conceptual-gitops-ci-cd.md#gitops-repo) 를 Azure Repos로 가져옵니다. 이 자습서에서는 다음 예제 리포지토리를 사용합니다.

* **arc-cicd-demo-src** 애플리케이션 리포지토리
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * GitOps를 사용하여 배포할 Azure Vote 샘플 앱을 포함하고 있습니다.
* **arc-cicd-demo-gitops** GitOps 리포지토리
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Azure Vote 앱을 보관하는 클러스터 리소스의 베이스로 작동합니다.

[Git 리포지토리 가져오기](/azure/devops/repos/git/import-git-repository)에 대해 자세히 알아보세요.

>[!NOTE]
> 애플리케이션 리포지토리와 GitOps 리포지토리에 각각 별도의 리포지토리를 가져와서 사용하면 보안과 단순성을 높일 수 있습니다. 애플리케이션 리포지토리와 GitOps 리포지토리의 권한 및 표시 유형을 개별적으로 조정할 수 있습니다.
> 예를 들어 클러스터 관리자가 원하는 클러스터 상태와 관련된 애플리케이션 코드의 변경 내용을 찾지 못할 수 있습니다. 반대로 애플리케이션 개발자는 각 환경의 특정 매개 변수를 알 필요가 없습니다. 매개 변수의 범위를 제공하는 테스트 값 세트면 충분합니다.

## <a name="connect-the-gitops-repo"></a>GitOps 리포지토리 연결

앱을 지속적으로 배포하려면 GitOps를 사용하여 애플리케이션 리포지토리를 클러스터에 연결합니다. **arc-cicd-demo-gitops** GitOps 리포지토리에는 앱을 가동하고 **arc-cicd-cluster** 클러스터에서 실행하기 위한 기본 리소스가 포함되어 있습니다.

초기 GitOps 리포지토리에는 배포 환경에 해당하는 **dev** 및 **stage** 네임스페이스를 만드는 [매니페스트](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml)만 들어 있습니다.

여기서 만드는 GitOps 연결은 자동으로 다음을 수행합니다.
* 매니페스트 디렉터리의 매니페스트를 동기화합니다.
* 클러스터 상태를 업데이트합니다.

CI/CD 워크플로는 매니페스트 디렉터리를 앱 배포를 위한 추가 매니페스트로 채웁니다.


1. Azure Repos의 새로 가져온 **arc-cicd-demo-gitops** 리포지토리에 대한 [새 GitOps 연결을 만듭니다](./tutorial-use-gitops-connected-cluster.md).

   ```azurecli
   az k8s-configuration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/<Your project>/_git/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Flux에서 기본 경로로 `arc-cicd-cluster/manifests` 디렉터리 *만* 사용하는지 확인합니다. 다음 연산자 매개 변수를 사용하여 경로를 정의합니다.

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > HTTPS 연결 문자열을 사용하는데 연결 문제가 있는 경우에는 URL에서 사용자 이름 접두사를 생략해야 합니다. 예를 들어 `https://alice@dev.azure.com/contoso/project/_git/arc-cicd-demo-gitops`에서 `alice@`를 제거해야 합니다. 그 대신 `--https-user`로 사용자를 지정합니다(예: `--https-user alice`).

1. Azure Portal에서 배포 상태를 확인합니다.
   * 배포가 성공하면 클러스터에서 만든 `dev` 및 `stage` 네임스페이스가 모두 표시됩니다.

## <a name="import-the-cicd-pipelines"></a>CI/CD 파이프라인 가져오기

GitOps 연결을 동기화했으므로, 매니페스트를 만드는 CI/CD 파이프라인을 가져와야 합니다.

애플리케이션 리포지토리에는 PR, CI 및 CD에 사용할 파이프라인이 들어 있는 `.pipeline` 폴더가 있습니다. 샘플 리포지토리에 제공된 3개의 파이프라인을 가져오고 이름을 바꿉니다.

| 파이프라인 파일 이름 | 설명 |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | **arc-cicd-demo-src PR** 이라는 애플리케이션 PR 파이프라인 |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | **arc-cicd-demo-src CI** 라는 애플리케이션 CI 파이프라인 |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | **arc-cicd-demo-src CD** 라는 애플리케이션 CD 파이프라인 |



## <a name="connect-your-acr"></a>ACR 연결
파이프라인과 클러스터는 모두 ACR을 활용하여 Docker 이미지를 저장하고 검색합니다.

### <a name="connect-acr-to-azure-devops"></a>Azure DevOps에 ACR 연결
CI 프로세스 중에 애플리케이션 컨테이너를 레지스트리에 배포할 것입니다. 먼저 다음과 같이 Azure 서비스를 연결합니다.

1. Azure DevOps의 프로젝트 설정 페이지에서 **서비스 연결** 페이지를 엽니다. TFS의 상단 메뉴 모음에 있는 **설정** 아이콘에서 **서비스** 페이지를 엽니다.
2. **+ 새 서비스 연결** 을 선택하고 필요한 서비스 연결 유형을 선택합니다.
3. 서비스 연결에 대한 매개 변수를 입력합니다. 이 자습서의 경우:
   * 서비스 연결 이름을 **arc-demo-acr** 로 지정합니다. 
   * 리소스 그룹으로 **myResourceGroup** 을 선택합니다.
4. **모든 파이프라인에 액세스 권한 부여** 를 선택합니다. 
   * 이 옵션은 YAML 파이프라인 파일에 서비스 연결 권한을 부여합니다. 
5. **확인** 을 선택하여 연결을 만듭니다.

### <a name="connect-acr-to-kubernetes"></a>Kubernetes에 ACR 연결
Kubernetes 클러스터가 ACR에서 이미지를 끌어올 수 있도록 설정합니다. 프라이빗인 경우 인증이 필요합니다.

#### <a name="connect-acr-to-existing-aks-clusters"></a>기존 AKS 클러스터에 ACR 연결

다음 명령을 사용하여 기존 ACR을 기존 AKS 클러스터와 통합합니다.

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>이미지 끌어오기 비밀 만들기

AKS가 아닌 클러스터와 로컬 클러스터를 ACR에 연결하려면 이미지 끌어오기 비밀을 만듭니다. Kubernetes는 이미지 끌어오기 비밀을 사용하여 레지스트리 인증에 필요한 정보를 저장합니다.

다음 `kubectl` 명령을 사용하여 이미지 끌어오기 비밀을 만듭니다. `dev` 및 `stage` 네임스페이스 둘 모두에 대해 같은 과정을 반복합니다.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Pod마다 imagePullSecret을 설정하지 않으려면 `dev` 및 `stage` 네임스페이스의 서비스 계정에 imagePullSecret을 추가합니다. 자세한 내용은 [Kubernetes 자습서](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)를 참조하세요.

## <a name="create-environment-variable-groups"></a>환경 변수 그룹 만들기

### <a name="app-repo-variable-group"></a>앱 리포지토리 변수 그룹
**az-vote-app-dev** 라는 [변수 그룹을 만듭니다](/azure/devops/pipelines/library/variable-groups). 다음 값을 설정합니다.

| 변수 | 값 |
| -------- | ----- |
| AZ_ACR_NAME | (개발자의 ACR 인스턴스. 예: azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (개발자의 Azure 서비스 연결. 이 자습서 앞부분의 **arc-demo-acr**) |
| AZURE_VOTE_IMAGE_REPO | Azure Vote 앱 리포지토리의 전체 경로. 예: azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | 개발 |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_FOLDER | `azure-vote-manifests` |
| MANIFESTS_REPO | `arc-cicd-demo-gitops` |
| ORGANIZATION_NAME | Azure DevOps 조직의 이름 |
| PROJECT_NAME | Azure DevOps의 GitOps 프로젝트 이름 |
| REPO_URL | GitOps 리포지토리의 전체 URL |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

### <a name="stage-environment-variable-group"></a>Stage 환경 변수 그룹

1. **az-vote-app-dev** 변수 그룹을 복제합니다.
1. 이름을 **az-vote-app-stage** 로 변경합니다.
1. 해당 변수의 다음 값을 확인합니다.

| 변수 | 값 |
| -------- | ----- |
| ENVIRONMENT_NAME | 단계 |
| TARGET_NAMESPACE | `stage` |

이제 `dev` 및 `stage` 환경에 배포할 준비가 되었습니다.

## <a name="give-more-permissions-to-the-build-service"></a>빌드 서비스에 추가 권한 부여
CD 파이프라인은 실행 중인 빌드의 보안 토큰을 사용하여 GitOps 리포지토리에 인증합니다. 파이프라인에서 새 분기를 만들고, 변경 내용을 푸시하고, 끌어오기 요청을 만들려면 더 많은 권한이 필요합니다.

1. Azure DevOps 프로젝트 기본 페이지에서 `Project settings`로 이동합니다.
1. `Repositories`를 선택합니다.
1. `<GitOps Repo Name>`를 선택합니다.
1. `Security`를 선택합니다. 
1. `<Project Name> Build Service (<Organization Name>)`의 경우 `Contribute`, `Contribute to pull requests` 및 `Create branch`를 허용합니다.

자세한 내용은 다음을 참조하세요.
- [빌드 서비스에 VC 권한 부여](/azure/devops/pipelines/scripts/git-commands?preserve-view=true&tabs=yaml&view=azure-devops#version-control )
- [빌드 서비스 계정 권한 관리](/azure/devops/pipelines/process/access-tokens?preserve-view=true&tabs=yaml&view=azure-devops#manage-build-service-account-permissions)


## <a name="deploy-the-dev-environment-for-the-first-time"></a>처음으로 개발 환경 배포
CI 및 CD 파이프라인을 만들었으면 CI 파이프라인을 실행하여 앱을 처음으로 배포합니다.

### <a name="ci-pipeline"></a>CI 파이프라인

CI 파이프라인이 처음으로 실행될 때 서비스 연결 이름을 읽는 과정에서 리소스 권한 부여 오류가 발생할 수 있습니다.
1. 액세스 중인 변수가 AZURE_SUBSCRIPTION인지 확인합니다.
1. 사용 권한을 부여합니다.
1. 파이프라인을 다시 실행합니다.

CI 파이프라인은 다음을 수행합니다.
* 애플리케이션 변경 내용이 배포에 대한 모든 자동 품질 검사를 통과하는지 확인합니다.
* PR 파이프라인에서 완료할 수 없는 추가 유효성 검사를 수행합니다.
    * GitOps와 관련하여 이 파이프라인은 CD 파이프라인을 통해 배포되는 커밋의 아티팩트도 게시합니다.
* Docker 이미지가 변경되었고 새 이미지가 푸시되었는지 확인합니다.

### <a name="cd-pipeline"></a>CD 파이프라인
초기 CD 파이프라인을 실행하는 동안 GitOps 리포지토리에 대한 파이프라인 액세스 권한을 부여하라는 메시지가 표시됩니다. 파이프라인에 리소스에 액세스할 수 있는 권한이 필요하다는 메시지가 표시되면 보기를 선택합니다. 그런 다음, 허용을 선택하여 파이프라인의 현재 및 향후 실행에 GitOps 리포지토리를 사용할 수 있는 권한을 부여합니다.

CI 파이프라인이 성공적으로 실행되면 CD 파이프라인이 트리거되어 배포 프로세스를 완료합니다. 각 환경이 점진적으로 배포됩니다.

> [!TIP]
> CD 파이프라인이 자동으로 트리거되지 않으면 다음을 수행합니다.
> 1. [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)에서 이름이 분기 트리거와 일치하는지 확인합니다.
>    * `arc-cicd-demo-src CI`여야 합니다.
> 1. CI 파이프라인을 다시 실행합니다.

GitOps 리포지토리의 템플릿과 매니페스트가 변경되면 CD 파이프라인은 커밋을 만들고, 푸시하고, 승인을 받기 위해 PR을 만듭니다.
1. `Create PR` 작업 출력에 제공되는 PR 링크를 엽니다.
1. GitOps 리포지토리 변경 내용을 확인합니다. 다음과 같은 결과가 표시됩니다.
   * 대략적인 Helm 템플릿 변경 내용
   * 원하는 상태의 기본적인 변화를 보여주는 하위 수준 Kubernetes 매니페스트. Flux가 이러한 매니페스트를 배포합니다.
1. 모두 정상이면 PR을 승인하고 완료합니다.

1. 몇 분 후 Flux가 변경 내용을 선택하고 배포를 시작합니다.
1. `kubectl`을 사용하여 로컬로 포트를 전달하고 다음 명령을 사용하여 앱이 올바르게 작동하는지 확인합니다.

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. 브라우저를 사용하여 `http://localhost:8080/`에서 Azure Vote 앱을 봅니다.

1. 마음에 드는 항목에 투표하고 앱을 변경할 준비를 합니다.

## <a name="set-up-environment-approvals"></a>환경 승인 설정
앱을 배포할 때 코드 또는 템플릿을 변경할 수 있을 뿐 아니라 의도치 않게 클러스터를 잘못된 상태로 만들 수 있습니다.

dev 환경에서 배포 후 중단이 드러나는 경우 환경 승인을 사용하여 중단이 이후 환경으로 전달되지 않도록 합니다.

1. Azure DevOps 프로젝트에서 보호가 필요한 환경으로 이동합니다.
1. 리소스의 **승인 및 확인** 으로 이동합니다.
1. **만들기** 를 선택합니다.
1. 승인자 및 선택적 메시지를 입력합니다.
1. **만들기** 를 다시 선택하여 추가적인 수동 승인 확인을 완료합니다.

자세한 내용은 [승인 및 확인 정의](/azure/devops/pipelines/process/approvals) 자습서를 참조하세요.

다음에 CD 파이프라인이 실행되면 GitOps PR 생성 후 파이프라인이 일시 중지됩니다. 변경 내용이 제대로 동기화되었으며 기본 기능을 전달하는지 확인합니다. 파이프라인에서 확인을 승인하여 변경 내용이 다음 환경으로 전달되도록 합니다.

## <a name="make-an-application-change"></a>애플리케이션 변경

클러스터의 상태를 나타내는 이러한 템플릿 및 매니페스트 기준 세트를 사용하여 앱을 약간 변경하겠습니다.

1. **arc-cicd-demo-src** 리포지토리에서 [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) 파일을 편집합니다.

2. "Cats vs Dogs"는 투표 수가 충분하지 않으므로 "Tabs vs Spaces"로 변경하여 투표 수를 늘립니다.

3. 새 분기의 변경을 커밋하고, 푸시하고, 끌어오기 요청을 만듭니다.
   * 이것이 CI/CD 수명 주기를 시작하는 일반적인 개발자 흐름입니다.

## <a name="pr-validation-pipeline"></a>PR 유효성 검사 파이프라인

PR 파이프라인은 잘못된 변경을 방지하는 첫 번째 방어선입니다. 일반적인 애플리케이션 코드 품질 검사에는 린팅 및 정적 분석이 포함됩니다. GitOps 관점에서, 배포하는 최종 인프라에도 동일한 품질을 보장해야 합니다.

애플리케이션의 Dockerfile 및 Helm 차트에서 애플리케이션과 비슷한 방식으로 린팅을 사용할 수 있습니다.

린팅 중에 발견되는 오류의 범위는 아래와 같습니다.
* 잘못된 형식의 YAML 파일부터
* 애플리케이션의 CPU 및 메모리 제한 설정과 같은 모범 사례 제안까지

> [!NOTE]
> 실제 애플리케이션에서 Helm 린팅으로 최상의 범위를 얻으려면 실제 환경에서 사용되는 값과 상당히 비슷한 값으로 바꿔야 합니다.

파이프라인 실행 중에 발견된 오류는 실행의 테스트 결과 섹션에 표시됩니다. 여기에서는 다음과 같은 작업을 할 수 있습니다.
* 오류 유형에 대한 유용한 통계를 추적합니다.
* 오류가 발견된 첫 번째 커밋을 찾습니다.
* 오류를 발생시킨 코드 섹션에 대한 추적 스타일 링크를 배치합니다.

파이프라인 실행이 완료되면 애플리케이션 코드와 이 코드를 배포할 템플릿의 품질이 보장됩니다. 이제 PR을 승인하고 완료할 수 있습니다. CI가 다시 실행되어 템플릿과 매니페스트를 다시 생성하고, CD 파이프라인을 트리거합니다.

> [!TIP]
> 실제 환경에서는 PR이 품질 검사를 통과하도록 잊지 말고 분기 정책을 설정해야 합니다. 자세한 내용은 [분기 정책 설정](/azure/devops/repos/git/branch-policies) 문서를 참조하세요.

## <a name="cd-process-approvals"></a>CD 프로세스 승인

CI 파이프라인이 성공적으로 실행되면 CD 파이프라인이 트리거되어 배포 프로세스를 완료합니다. CD 파이프라인을 처음으로 사용할 수 있게 되는 때와 마찬가지로 각 환경에 점진적으로 배포합니다. 이번에는 파이프라인에서 각 배포 환경을 승인해야 합니다.

1. `dev` 환경에 대한 배포를 승인합니다.
1. GitOps 리포지토리의 템플릿과 매니페스트가 변경되면 CD 파이프라인은 커밋을 만들고, 푸시하고, 승인을 받기 위해 PR을 만듭니다.
1. 작업에 제공되는 PR 링크를 엽니다.
1. GitOps 리포지토리 변경 내용을 확인합니다. 다음과 같은 결과가 표시됩니다.
   * 대략적인 Helm 템플릿 변경 내용
   * 원하는 상태의 기본적인 변화를 보여주는 하위 수준 Kubernetes 매니페스트.
1. 모두 정상이면 PR을 승인하고 완료합니다.
1. 배포가 완료될 때가지 기다립니다.
1. 기본 스모크 테스트로, 애플리케이션 페이지로 이동하여 투표 앱이 이제 Tabs vs Spaces를 표시하는지 확인합니다.
   * `kubectl`을 사용하여 로컬로 포트를 전달하고 `kubectl port-forward -n dev svc/azure-vote-front 8080:80` 명령을 사용하여 앱이 올바르게 작동하는지 확인합니다.
   * 브라우저를 통해 http://localhost:8080/ 에서 Azure Vote 앱을 살펴보고, 투표 항목이 Tabs vs Spaces로 변경되었는지 확인합니다. 
1. `stage` 환경에 대해 1-7단계를 반복합니다.

이제 배포가 완료되었습니다. 이것으로 CI/CD 워크플로가 끝났습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 더 이상 사용하지 않으려면 다음 단계에 따라 리소스를 삭제합니다.

1. 다음과 같이 Azure Arc GitOps 구성 연결을 삭제합니다.
   ```azurecli
   az k8s-configuration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. 다음과 같이 `dev` 네임스페이스를 제거합니다.
   * `kubectl delete namespace dev`

3. 다음과 같이 `stage` 네임스페이스를 제거합니다.
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>다음 단계

이 자습서에서는 애플리케이션 개발부터 배포까지 DevOps를 구현하는 전체 CI/CD 워크플로를 설정했습니다. 앱을 변경하면 자동으로 유효성 검사 및 배포가 트리거되고, 수동 승인을 통해 제어합니다.

개념 문서를 계속 진행하여 GitOps 및 Azure Arc 지원 Kubernetes를 사용한 구성에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [GitOps - Azure Arc 지원 Kubernetes를 사용하는 CI/CD 워크플로](./conceptual-gitops-ci-cd.md)
