---
title: Shared Image Gallery를 사용하여 사용자 지정 이미지 풀 만들기
description: 사용자 지정 이미지 풀은 Batch 워크로드를 실행하도록 컴퓨팅 노드를 구성하는 효율적인 방법입니다.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 19a6168c2d6d2a37458dbbe9d8917f6e679da47f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827490"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Shared Image Gallery를 사용하여 사용자 지정 이미지 풀 만들기

Virtual Machine 구성을 사용하여 Azure Batch 풀을 만들 경우 풀에서 각 컴퓨팅 노드에 대해 운영 체제를 제공하는 VM 이미지를 지정합니다. 지원되는 Azure Marketplace 이미지를 사용하여 가상 머신의 풀을 만들거나 [Shared Image Gallery 이미지](../virtual-machines/shared-image-galleries.md)를 사용하여 사용자 지정 이미지를 만들 수 있습니다.

## <a name="benefits-of-the-shared-image-gallery"></a>Shared Image Gallery의 이점

사용자 지정 이미지에 대한 Shared Image Gallery를 사용하는 경우 운영 체제 유형과 구성 뿐만 아니라 데이터 디스크의 유형을 제어할 수 있습니다. 공유 이미지는 프로비저닝되는 즉시 모든 Batch 풀 노드에서 사용할 수 있는 애플리케이션 및 참조 데이터를 포함할 수 있습니다.

사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수도 있습니다. 이미지 버전을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다.

공유 이미지를 사용하면 풀의 컴퓨팅 노드가 Batch 워크로드를 실행하는 데 걸리는 시간을 절약할 수 있습니다. 프로비저닝 후에는 Azure Marketplace 이미지를 사용하고 각 컴퓨팅 노드에서 소프트웨어를 설치할 수 있지만 공유 이미지를 사용하는 것이 더 효율적입니다. 또한 공유 이미지에 대해 여러 복제본을 지정할 수 있으므로 여러 VM(600개 이상)을 사용하여 풀을 만들 때 풀 생성 시간을 절약합니다.

시나리오에 맞게 구성된 공유 이미지를 사용하면 몇 가지 이점을 제공할 수 있습니다.

- **지역에서 동일한 이미지 사용** 모든 풀에서 동일한 이미지를 사용하도록 여러 지역에 걸쳐 공유 이미지 복제본을 만들 수 있습니다.
- **OS(운영 체제) 구성** 이미지의 운영 체제 디스크 구성을 사용자 지정할 수 있습니다.
- **애플리케이션 미리 설치**. OS 디스크에 애플리케이션을 미리 설치하는 것은 시작 작업을 사용하여 컴퓨팅 노드를 프로비저닝한 후에 애플리케이션을 설치하는 것보다 효율성은 높고 오류 발생 가능성은 낮습니다.
- **많은 데이터를 한 번에 복사** 관리되는 공유 이미지를 관리되는 이미지의 데이터 디스크에 복사하여 관리되는 공유 이미지의 정적 데이터 부분을 만듭니다. 이 작업은 한 번만 수행하며 풀의 각 노드에서 데이터를 사용할 수 있게 됩니다.
- **풀을 더 큰 크기로 확장** Shared Image Gallery를 사용하면 더 많은 공유 이미지 복제본과 함께 사용자 지정된 이미지를 사용하여 더 큰 풀을 만들 수 있습니다.
- **관리형 이미지를 사용자 지정 이미지로 사용하는 것보다 성능이 뛰어납니다.** 공유 이미지 사용자 지정 이미지 풀의 경우 정상 상태에 도달하는 시간이 최대 25% 더 빠르며 VM 유휴 지연 시간이 최대 30% 더 짧습니다.
- **보다 쉽게 관리할 수 있도록 이미지 버전 관리 및 그룹화** 이미지 그룹화 정의에는 이미지를 만든 이유에 대한 정보, 사용 중인 OS 및 이미지 사용에 대한 정보가 포함되어 있습니다. 이미지를 그룹화하면 이미지를 쉽게 관리할 수 있습니다. 자세한 내용은 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> Azure AD를 사용하여 인증해야 합니다. 공유 키 인증을 사용하는 경우 인증 오류가 발생합니다.  

- **Azure Batch 계정.** Batch 계정을 만들려면 [Azure Portal](quick-create-portal.md) 또는 [Azure CLI](quick-create-cli.md)를 사용하는 Batch 빠른 시작을 참조하세요.

- **Shared Image Gallery 이미지** 공유 이미지를 만들려면 관리되는 이미지 리소스를 갖거나 만들어야 합니다. VM OS 디스크 및 연결된 데이터 디스크(선택 사항)의 스냅샷에서 이미지를 만들어야 합니다.

> [!NOTE]
> 공유 이미지가 Batch 계정과 동일한 구독에 없는 경우 해당 구독에 대해 [Microsoft.Batch 리소스 공급자를 등록](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)해야 합니다. 두 구독은 동일한 Azure AD 테넌트에 있어야 합니다.
>
> Batch 계정과 동일한 지역에 복제본이 있는 경우 이미지는 다른 지역에 있을 수 있습니다.

Azure AD 애플리케이션을 사용하여 Shared Image Gallery 이미지가 있는 사용자 지정 이미지 풀을 만드는 경우 해당 애플리케이션에는 공유 이미지에 대한 액세스 권한을 부여하는 [Azure 기본 제공 역할](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)이 부여되어야 합니다. 공유 이미지로 이동하여 **액세스 제어(IAM)** 를 선택하고 애플리케이션에 대한 역할 할당을 추가하여 Azure Portal에서 이 액세스 권한을 부여할 수 있습니다.

## <a name="prepare-a-shared-image"></a>공유 이미지 준비

Azure에서는 다음을 통해 만들 수 있는 관리되는 이미지에서 공유 이미지를 준비할 수 있습니다.

- Azure VM의 OS 및 데이터 디스크의 스냅샷
- 관리 디스크를 사용하는 일반화된 Azure VM
- 클라우드로 업로드된 일반화된 온-프레미스 VHD

> [!NOTE]
> Batch는 일반화된 공유 이미지만 지원합니다. 특수 공유 이미지를 사용하여 풀을 만들 수 없습니다.

다음 단계는 VM을 준비하고 스냅샷을 생성하고 스냅샷에서 이미지를 만드는 방법을 보여 줍니다.

### <a name="prepare-a-vm"></a>VM 준비

이미지용으로 새 VM을 만드는 경우 Batch에서 지원하는 자사 Azure Marketplace 이미지를 관리형 이미지의 기본 이미지로 사용합니다. 자사 이미지만 기본 이미지로 사용할 수 있습니다. Azure Batch에서 지원하는 Azure Marketplace 이미지 참조의 전체 목록을 가져오려면 [노드 에이전트 SKU 나열](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 작업을 참조하세요.

> [!NOTE]
> 추가 라이선스 및 구매 약관이 있는 타사 이미지는 기본 이미지로 사용할 수 없습니다. Marketplace 이미지에 대한 자세한 내용은 [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) 또는 [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties) VM을 참조하세요.

VM을 만드는 경우 다음 지침을 따릅니다.

- 관리 디스크로 VM이 생성되었는지 확인합니다. VM을 만들 때 기본 스토리지 설정입니다.
- 사용자 지정 스크립트 확장 등의 Azure 확장을 VM에 설치해서는 안 됩니다. 이미지에 미리 설치된 확장이포함되어 있으면 Batch 풀을 배포할 때 Azure에서 문제가 발생할 수 있습니다.
- 연결된 데이터 디스크를 사용하는 경우 VM 내에서 디스크를 탑재하고 포맷하여 사용해야 합니다.
- 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하도록 해야 합니다. Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상합니다.
- OS 디스크가 암호화되지 않았는지 확인합니다.
- VM이 실행되면 RDP(Windows용) 또는 SSH(Linux용)를 통해 연결합니다. 필요한 소프트웨어를 설치하거나 원하는 데이터를 복사합니다.
- 더 빠른 풀 프로비저닝을 위해 VM의 OS 디스크에 [ReadWrite 디스크 캐시 설정](../virtual-machines/premium-storage-performance.md#disk-caching)을 사용합니다.

### <a name="create-a-vm-snapshot"></a>VM 스냅샷 만들기

스냅샷은 VHD의 전체 읽기 전용 복사본입니다. VM OS 또는 데이터 디스크의 스냅샷을 만들려는 경우 Azure Portal 또는 명령줄 도구를 사용할 수 있습니다. 스냅샷을 만드는 단계 및 옵션은 VM에 대한 지침을 [참조하세요.](../virtual-machines/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>스냅샷 하나 이상에서 이미지 만들기

스냅샷에서 관리되는 이미지를 만들려면 [az image create](/cli/azure/image) 명령과 같은 Azure 명령줄 도구를 사용합니다. OS 디스크 스냅샷을 지정하고 필요에 따라 데이터 디스크 스냅샷을 하나 이상 지정하여 이미지를 만듭니다.

### <a name="create-a-shared-image-gallery"></a>Shared Image Gallery 만들기

관리 이미지를 성공적으로 만들었으면 Shared Image Gallery를 만들어 사용자 지정 이미지를 사용할 수 있도록 해야 합니다. 이미지에 대한 Shared Image Gallery 만드는 방법을 알아보려면 [Shared Image Gallery 만들기를](../virtual-machines/create-gallery.md)참조하세요.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Azure CLI를 사용하여 공유 이미지에서 풀 만들기

Azure CLI를 사용하여 공유 이미지에서 풀을 만들려면 `az batch pool create` 명령을 사용합니다. `--image` 필드에서 공유 이미지 ID를 지정합니다. OS 유형 및 SKU가 `--node-agent-sku-id`에 지정된 버전과 일치하는지 확인합니다.

> [!NOTE]
> Azure AD를 사용하여 인증해야 합니다. 공유 키 인증을 사용하는 경우 인증 오류가 발생합니다.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>C#을 사용하여 공유 이미지에서 풀 만들기

또는 C# SDK를 사용하여 공유 이미지에서 풀을 만들 수 있습니다.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Python을 사용하여 공유 이미지에서 풀 만들기

Python SDK를 사용하여 공유 이미지에서 풀을 만들 수도 있습니다. 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Azure Portal을 사용하여 공유 이미지에서 풀 만들기

다음 단계를 사용하여 Azure Portal의 공유 이미지에서 풀을 만듭니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. **Batch 계정** 으로 이동하여 계정을 선택합니다.
1. **풀** 을 선택한 다음, **추가** 를 선택하여 새 풀을 만듭니다.
1. **이미지 형식** 섹션에서 **Shared Image Gallery** 를 선택합니다.
1. 관리되는 이미지에 대한 정보를 사용하여 나머지 섹션을 완료합니다.
1. **확인** 을 선택합니다.

![포털을 사용하여 공유 이미지에서 풀을 만듭니다.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>대형 풀 관련 고려 사항

공유 이미지를 사용하여 수백 또는 수천 개의 VM이 포함된 풀을 만들려는 경우 다음 지침을 사용합니다.

- **Shared Image Gallery 복제본 번호**  최대 300개의 인스턴스를 포함하는 모든 풀에 대해 하나 이상의 복제본을 유지하는 것이 좋습니다. 예를 들어 3000개의 VM을 사용하여 풀을 만드는 경우 이미지의 복제본을 10개 이상 유지해야 합니다. 성능 향상을 위해 항상 최소 요구 사항보다 더 많은 복제본을 유지하는 것이 좋습니다.

- **크기 조정 시간 제한** 풀이 고정된 수의 노드를 포함하는 경우(자동 크기 조정하지 않는 경우) 풀 크기에 따라 풀의 `resizeTimeout` 속성을 늘립니다. 1000개의 VM마다 권장 크기 조정 시간 제한은 15분 이상입니다. 예를 들어 2000개의 VM이 있는 풀에 권장되는 크기 조정 제한 시간은 30분 이상입니다.

## <a name="next-steps"></a>다음 단계

- Batch에 대한 자세한 개요는 [Batch 서비스 워크플로 및 리소스](batch-service-workflow-features.md)를 참조하세요.
- [Shared Image Gallery](../virtual-machines/shared-image-galleries.md)에 대해 알아봅니다.