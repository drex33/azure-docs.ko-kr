---
title: 전용 풀을 사용하여 작업 실행 - 작업
description: 레지스트리에서 Azure Container Registry 작업을 실행할 전용 컴퓨팅 풀(에이전트 풀)을 설정합니다.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: a303155b82cc35461cd6080b3cce694ffe7a1fc4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112122834"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>전용 에이전트 풀에서 ACR 작업 실행

전용 컴퓨팅 환경에서 [Azure Container Registry 작업][acr-tasks]을 실행할 수 있도록 Azure 관리형 VM 풀(‘에이전트 풀’)을 설정합니다. 레지스트리에서 풀을 하나 이상 구성하면 서비스의 기본 컴퓨팅 환경 대신 작업을 실행할 풀을 선택할 수 있습니다.

에이전트 풀은 다음을 제공합니다.

- **가상 네트워크 지원** - Azure VNet에 에이전트 풀을 할당하여 컨테이너 레지스트리, 키 자격 증명 모음, 스토리지 등과 같은 VNet의 리소스에 대한 액세스를 제공합니다.
- **필요에 따라 스케일링** - 컴퓨팅을 많이 사용하는 작업에 대해 에이전트 풀의 인스턴스 수를 늘리거나 0으로 스케일링합니다. 요금은 풀 할당을 기준으로 청구됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/container-registry/)을 참조하세요.
- **유연한 옵션** - 작업 워크로드 요구 사항에 맞게 다양한 [풀 계층](#pool-tiers) 및 스케일링 옵션 중에서 선택합니다.
- **Azure 관리** - 작업 풀을 Azure에서 패치하고 유지 관리하여 예약된 할당을 제공하므로 개별 VM을 유지 관리할 필요가 없습니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry SKU][acr-tiers]를 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="preview-limitations"></a>미리 보기 제한 사항

- 작업 에이전트 풀에서는 현재 Linux 노드를 지원합니다. Windows 노드는 현재 지원되지 않습니다.
- 작업 에이전트 풀은 미국 서부 2, 미국 중남부, 미국 동부 2, 미국 동부, 미국 중부, 서유럽, 북유럽, 캐나다 중부, USGov 애리조나, USGov 텍사스, USGov 버지니아 지역에서 미리 보기로 제공됩니다.
- 각 레지스트리에 대해 기본 총 vCPU(코어) 할당량은 모든 표준 에이전트 풀의 경우 16개, 격리 에이전트 풀의 경우 0개입니다. 추가 할당이 필요하면 [지원 요청][open-support-ticket]을 엽니다.
- 현재는 에이전트 풀에서 작업 실행을 취소할 수 없습니다.

## <a name="prerequisites"></a>필수 조건

* 이 문서의 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.3.1 이상이 필요합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요. 또는 [Azure Cloud Shell](../cloud-shell/quickstart.md)에서 실행합니다.
* 컨테이너 레지스트리가 아직 없는 경우 미리 보기 지역에서 [만듭니다][create-reg-cli](프리미엄 계층 필요).

## <a name="pool-tiers"></a>풀 계층

에이전트 풀 계층에서는 풀의 인스턴스당 다음과 같은 리소스를 제공합니다.

|계층    | Type  |  CPU  |메모리(GB)  |
|---------|---------|---------|---------|
|S1     |  표준    | 2       |    3     |
|S2     |  표준    | 4       |    8     |
|S3     |  표준    | 8       |   16     |
|I6     |  Isolated    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>작업 에이전트 풀 만들기 및 관리

### <a name="set-default-registry-optional"></a>기본 레지스트리 설정(선택 사항)

다음에 나오는 Azure CLI 명령을 간소화하려면 [az config][az-config] 명령을 실행하여 기본 레지스트리를 설정합니다.

```azurecli
az config set defaults.acr=<registryName>
```

다음 예제에서는 기본 레지스트리를 설정했다고 가정합니다. 설정하지 않은 경우 각 `az acr` 명령에 `--registry <registryName>` 매개 변수를 전달합니다.

### <a name="create-agent-pool"></a>에이전트 풀 만들기

[az acr agentpool create][az-acr-agentpool-create] 명령을 사용하여 에이전트 풀을 만듭니다. 다음 예제에서는 계층 S2 풀(4개 CPU/인스턴스)을 만듭니다. 기본적으로 풀은 인스턴스 1개를 포함합니다.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> 에이전트 풀 만들기와 기타 풀 관리 작업은 완료하는 데 몇 분 정도 걸립니다.

### <a name="scale-pool"></a>풀 스케일링

[az acr agentpool update][az-acr-agentpool-update] 명령을 사용하여 풀 크기를 스케일 업하거나 다운할 수 있습니다. 다음 예제에서는 풀을 2개 인스턴스로 스케일링합니다. 0개 인스턴스로 스케일링할 수도 있습니다.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>가상 네트워크에서 풀 만들기

### <a name="add-firewall-rules"></a>방화벽 규칙 추가

작업 에이전트 풀을 사용하려면 다음 Azure 서비스에 액세스해야 합니다. 기존 네트워크 보안 그룹이나 사용자 정의 경로에 다음 방화벽 규칙을 추가해야 합니다.

| Direction | 프로토콜 | 원본         | 원본 포트 | 대상          | 대상 포트 | 사용됨    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | AzureKeyVault        | 443       | 기본값 |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | 스토리지              | 443       | 기본값 |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | EventHub             | 443       | 기본값 |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | AzureActiveDirectory | 443       | 기본값 |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | AzureMonitor         | 443       | 기본값 |

> [!NOTE]
> 작업에 퍼블릭 인터넷의 추가 리소스가 필요한 경우 해당 규칙을 추가합니다. 예를 들어 Docker Hub에서 기본 이미지를 가져오는 Docker 빌드 작업을 실행하거나 NuGet 패키지를 복원하려면 추가 규칙이 필요합니다.

### <a name="create-pool-in-vnet"></a>VNet에서 풀 만들기

다음 예제에서는 네트워크 *myvnet* 의 *mysubnet* 서브넷에 에이전트 풀을 만듭니다.

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>에이전트 풀에서 작업 실행

다음 예제에서는 작업을 큐에 대기시킬 때 에이전트 풀을 지정하는 방법을 보여 줍니다.

> [!NOTE]
> ACR 작업에서 에이전트 풀을 사용하려면 풀에 인스턴스가 1개 이상 포함되어 있어야 합니다.
>

### <a name="quick-task"></a>빠른 작업

[az acr build][az-acr-build] 명령을 사용하여 에이전트 풀에서 빠른 작업을 큐에 대기시키고 `--agent-pool` 매개 변수를 전달합니다.

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>자동으로 트리거된 작업

예를 들어 [az acr task create][az-acr-task-create]를 사용하여 에이전트 풀에서 예약된 작업을 만들고 `--agent-pool` 매개 변수를 전달합니다.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

작업 설정을 확인하려면 [az acr task run][az-acr-task-run]을 실행합니다.

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>풀 상태 쿼리

에이전트 풀에서 현재 예약된 실행 수를 확인하려면 [az acr agentpool show][az-acr-agentpool-show]를 실행합니다.

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>다음 단계

클라우드에서 컨테이너 이미지 빌드 및 유지 관리의 더 많은 예제를 보려면 [ACR 작업 자습서 시리즈](container-registry-tutorial-quick-task.md)를 확인하세요.



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-config]: /cli/azure#az_config
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az_acr_agentpool_create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az_acr_agentpool_update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az_acr_agentpool_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[create-reg-cli]: container-registry-get-started-azure-cli.md
