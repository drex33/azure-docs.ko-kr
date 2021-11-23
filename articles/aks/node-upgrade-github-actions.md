---
title: GitHub 작업을 사용하여 AKS 노드 업그레이드 처리
titleSuffix: Azure Kubernetes Service
description: GitHub 작업을 사용하여 AKS 노드를 업데이트하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: f6238dc1fac8ed23eae473776bd9bedbd4d24923
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940641"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>GitHub 작업을 사용하여 AKS(Azure Kubernetes Service) 노드에 자동으로 보안 업데이트 적용

보안 업데이트는 AKS 클러스터의 보안을 유지하고 기본 OS에 대한 최신 수정 사항을 준수하는 데 중요한 부분입니다. 이러한 업데이트는 OS 보안 수정 사항 또는 커널 업데이트를 포함합니다. 일부 업데이트는 프로세스를 완료하도록 노드를 다시 부팅해야 합니다.

`az aks upgrade`를 실행하면 제로 가동 중지 시간으로 업데이트를 적용할 수 있습니다. 이 명령은 모든 클러스터 노드에 최신 업데이트를 적용하고, 노드에 대한 트래픽을 통제 및 드레이닝하고, 노드를 다시 시작한 다음, 업데이트된 노드에 대한 트래픽을 허용합니다. 다른 방법을 사용하여 노드를 업데이트하는 경우 AKS는 노드를 자동으로 다시 시작하지 않습니다.

> [!NOTE]
> `--node-image-only` 플래그와 함께 사용하는 경우 `az aks upgrade`의 주요 차이점은 사용 시 노드 이미지만 업그레이드된다는 것입니다. 생략할 경우 노드 이미지와 Kubernetes 컨트롤 플레인 버전이 모두 업그레이드됩니다. [노드의 관리형 업그레이드에 대한 설명서][managed-node-upgrades-article]와 [클러스터 업그레이드용 설명서][cluster-upgrades-article]에서 심층 정보를 확인할 수 있습니다.

모든 Kubernetes 노드는 표준 Azure VM(가상 머신)에서 실행됩니다. 이러한 VM은 Windows 또는 Linux를 기준으로 할 수 있습니다. 이러한 Linux 기반 VM은 Ubuntu 이미지를 사용하며, 해당 OS는 매일 밤 업데이트를 자동으로 확인하도록 구성됩니다.

`az aks upgrade` 명령을 사용하는 경우 Azure CLI는 최신 보안 및 커널 업데이트에 따라 새로운 노드가 급증하게 되며, 이러한 노드는 초기에는 업데이트가 완료될 때까지 앱이 예약되지 않도록 통제됩니다. 완료되면 Azure는 이전 노드를 통제(노드를 새 워크로드 예약에 사용할 수 없게 설정)하고 드레이닝한 후(기존 워크로드를 다른 노드로 이동) 새 노드의 통제를 해제하여 결과적으로 예약된 모든 애플리케이션을 새 노드로 전송합니다.

이 프로세스는 Linux 기반 커널을 수동으로 업데이트하는 것보다 더 나은 방법입니다. 새 커널 업데이트를 설치하면 Linux를 다시 부팅해야 하기 때문입니다. OS를 수동으로 업데이트하는 경우에도 VM을 다시 부팅하고 모든 앱을 수동으로 통제 및 드레이닝해야 합니다.

이 문서에서는 AKS 노드의 업데이트 프로세스를 자동화하는 방법을 보여 줍니다. GitHub Actions 및 Azure CLI를 사용하여 자동으로 실행되는 `cron`을 기준으로 업데이트 작업을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

또한 이 문서에서는 작업을 만들 [GitHub][github] 계정이 있다고 가정합니다.

## <a name="create-a-timed-github-action"></a>시간이 정해진 GitHub 작업 만들기

`cron`은 자동화된 일정에 따라 명령 세트 또는 작업을 실행할 수 있는 유틸리티입니다. 자동화된 일정에 따라 AKS 노드를 업데이트하는 작업을 만들려면 작업을 호스트할 리포지토리가 필요합니다. 일반적으로 GitHub 작업은 애플리케이션과 동일한 리포지토리에 구성되지만 어떤 리포지토리도 사용할 수 있습니다. 이 문서에서는 [프로필 리포지토리][profile-repository]를 사용합니다. 리포지토리가 없는 경우 GitHub 사용자 이름과 동일한 이름으로 새 리포지토리를 만듭니다.

1. GitHub에서 리포지토리로 이동
1. 페이지 위쪽에서 **작업** 탭을 클릭합니다.
1. 이 리포지토리에서 워크플로를 이미 설정한 경우 완료된 실행 목록으로 이동됩니다. 이 경우 **새 워크플로** 단추를 클릭합니다. 이 워크플로가 리포지토리의 첫 번째 워크플로인 경우 GitHub에서 몇 가지 프로젝트 템플릿을 표시합니다. 그러면 설명 텍스트 아래에 있는 **워크플로 직접 설정** 링크를 클릭합니다.
1. 워크플로 `name` 및 `on` 태그를 아래와 비슷하게 변경합니다. GitHub Actions는 Linux 기반 시스템과 동일한 [POSIX cron 구문][cron-syntax]을 사용합니다. 이 일정에서는 15일마다 오전 3시에 실행하도록 워크플로에 지시합니다.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. 아래 항목을 사용하여 새 작업을 만듭니다. 이 작업의 이름은 `upgrade-node`이고, Ubuntu 에이전트에서 실행되고, Azure CLI 계정에 연결하여 노드를 업그레이드하는 데 필요한 단계를 실행합니다.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>워크플로에서 Azure CLI 설정

`steps` 키에서 워크플로가 노드를 업그레이드하기 위해 실행할 모든 작업을 정의합니다.

Azure CLI를 다운로드하고 로그인합니다.

1. GitHub Actions 화면의 오른쪽에서 *마켓플레이스 검색 표시줄* 을 찾아 **"Azure 로그인"** 을 입력합니다.
1. 결과적으로 **Azure에서** 게시한 **Azure 로그인** 이라는 작업이 수행됩니다.

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="첫 번째 작업을 'Azure 로그인'으로 표시하는 줄과 두 번째 작업을 'Azure Container Registry 로그인'으로 표시하는 줄을 포함하는 검색 결과":::

1. **Azure 로그인** 을 클릭합니다. 다음 화면에서 코드 샘플의 오른쪽 위에 있는 **복사 아이콘** 을 클릭합니다.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="아래에 코드 샘플이 있고 복사 아이콘 주변의 빨간색 사각형으로 클릭 지점을 강조 표시하는 Azure 로그인 작업 결과 창":::

1. `steps` 키 아래에 다음을 붙여넣습니다.

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Azure CLI에서 다음 명령을 실행하여 새 사용자 이름 및 암호를 생성합니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --role Contributor -o json
    ```

    출력은 다음 json과 비슷합니다.

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **새 브라우저 창에서** GitHub 리포지토리로 이동하여 리포지토리의 **설정** 탭을 엽니다. **비밀** 을 클릭하고 **새 리포지토리 암호** 를 클릭합니다.
1. *이름* 에는 `AZURE_CREDENTIALS`를 사용합니다.
1.  *값* 에 대해 새 사용자 이름 및 암호를 만든 이전 단계의 출력에서 전체 콘텐츠를 추가합니다.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="비밀 제목으로 AZURE_CREDENTIALS를 표시하고 실행된 명령 결과를 JSON으로 붙여넣은 양식":::

1. **비밀 추가** 를 클릭합니다.

작업에서 사용된 CLI는 Azure 계정에 로깅되고 명령을 실행할 준비가 됩니다.

Azure CLI 명령을 실행하는 단계를 만들려면

1. 화면 오른쪽에 있는 *GitHub marketplace* 의 **검색 페이지** 로 이동하고 *Azure CLI 작업* 을 검색합니다. *Azure의 Azure CLI 작업* 을 선택합니다.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Azure에서 만든 대로 첫 번째 결과가 표시된 'Azure CLI 작업'에 대한 검색 결과":::

1. *GitHub marketplace 결과* 에서 복사 단추를 클릭하고 주 편집기의 작업 콘텐츠를 다음과 비슷한 *Azure 로그인* 단계 아래에 붙여넣습니다.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > 이전 단계와 비슷한 비밀에 추가하여 명령에서 `-g` 및 `-n` 매개 변수를 분리할 수 있습니다. `{resourceGroupName}` 및 `{aksClusterName}` 자리 표시자를 해당 비밀(예: `${{secrets.RESOURCE_GROUP_NAME}}` 및 `${{secrets.AKS_CLUSTER_NAME}}`)로 바꿉니다.

1. 파일의 이름을 `upgrade-node-images`로 변경합니다.
1. **커밋 시작** 을 클릭하고 메시지 제목을 추가한 후 워크플로를 저장합니다.

커밋을 만들면 워크플로가 저장되고 실행할 준비가 됩니다.

> [!NOTE]
> 클러스터의 모든 노드 풀 대신 단일 노드 풀을 업그레이드하려면 `az aks nodepool upgrade` 명령에 `--name` 매개 변수를 추가하여 노드 풀 이름을 지정합니다. 예를 들면 다음과 같습니다.
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>GitHub 작업을 수동으로 실행

`workflow_dispatch`라는 새 `on` 트리거를 추가하여 예약된 실행 외에도 워크플로를 수동으로 실행할 수 있습니다. 완성된 파일은 아래의 YAML과 같습니다.

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>다음 단계

- 최신 노드 이미지에 대한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases)를 참조하세요.
- [AKS 클러스터를 업그레이드][cluster-upgrades-article]하여 Kubernetes 버전을 업그레이드하는 방법을 알아봅니다.
- 다양한 노드 풀 및 [여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 사용하여 노드 풀을 업그레이드하는 방법에 대해 자세히 알아보세요.
- [시스템 노드 풀][system-pools]에 대해 자세히 알아봅니다.
- 스폿 인스턴스를 사용하여 비용을 절감 하는 방법을 알아보려면 [AKS에 별색 노드 풀 추가][spot-pools]를 참조하세요.

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
