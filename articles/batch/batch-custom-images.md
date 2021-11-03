---
title: 사용자 지정 이미지를 사용하여 사용자 지정 이미지 풀 만들기
description: 관리 이미지 리소스에서 Batch 풀을 만들어 애플리케이션의 소프트웨어 및 데이터를 사용하여 컴퓨팅 노드를 프로비저닝합니다.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 9e70d3391ca9c8d4854c4cd587ffb6e840ef4254
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476892"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>사용자 지정 이미지를 사용하여 사용자 지정 이미지 풀 만들기

Batch 풀의 VM(가상 머신)에 대한 사용자 지정 이미지 풀을 만들려면 관리형 이미지를 사용하여 [Azure Compute 갤러리 이미지를](batch-sig-images.md)만들 수 있습니다. 관리형 이미지만 사용하는 것도 지원되지만 2019-08-01 이하의 API 버전에 대해서만 지원됩니다.

> [!IMPORTANT]
> 대부분의 경우 Azure Compute 갤러리를 사용하여 사용자 지정 이미지를 만들어야 합니다. Azure Compute 갤러리를 사용하면 풀을 더 빠르게 프로비전하고, 더 많은 수의 VM을 확장하고, VM을 프로비전할 때 안정성을 개선할 수 있습니다. 자세한 내용은 [Azure Compute 갤러리를 사용하여 사용자 지정 풀 만들기를 참조하세요.](batch-sig-images.md)

이 토픽에서는 관리된 이미지만 사용하여 사용자 지정 이미지 풀을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- **관리되는 이미지 리소스**. 사용자 지정 이미지를 사용하여 가상 머신 풀을 만들려면 Batch 계정과 동일한 Azure 구독 및 지역에 관리되는 이미지 리소스가 있거나 해당 리소스를 만들어야 합니다. 이미지는 VM 운영 체제(OS) 디스크의 스냅샷 및 필요에 따라 연결된 데이터 디스크에서 만들어야 합니다.
  - 작성하는 각 풀에 대해 고유한 사용자 지정 이미지를 사용합니다.
  - Batch API를 사용하여 이미지로 풀을 만들려면 이미지의 **리소스 ID** 를 지정합니다. 리소스 ID의 형식은 다음과 같습니다. `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`
  - 관리되는 이미지 리소스는 강화에 사용할 수 있도록 풀 수명 동안 유지되어야 하며, 풀을 삭제한 후에 제거할 수 있습니다.

- **Azure AD(Azure Active Directory) 인증**. Batch 클라이언트 API는 Azure AD 인증을 사용해야 합니다. Azure AD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다.

## <a name="prepare-a-managed-image"></a>관리형 이미지 준비

Azure의 다음에서 관리형 이미지를 준비할 수 있습니다.

- Azure VM의 OS 및 데이터 디스크의 스냅샷
- 관리 디스크를 사용하는 일반화된 Azure VM
- 클라우드로 업로드된 일반화된 온-프레미스 VHD

관리형 이미지를 사용하여 안정적으로 Batch 풀 크기를 조정하려는 경우 VM 디스크의 스냅샷을 사용하는 첫 번째 방법 *만* 사용하여 관리형 이미지를 만드는 것이 좋습니다. 다음 단계는 VM을 준비하고, 스냅샷을 만들고, 스냅샷에서 관리형 이미지를 만드는 방법을 보여줍니다.

### <a name="prepare-a-vm"></a>VM 준비

이미지용으로 새 VM을 만드는 경우 Batch에서 지원하는 자사 Azure Marketplace 이미지를 관리형 이미지의 기본 이미지로 사용합니다. 자사 이미지만 기본 이미지로 사용할 수 있습니다. Azure Batch에서 지원하는 Azure Marketplace 이미지 참조의 전체 목록을 가져오려면 [노드 에이전트 SKU 나열](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 작업을 참조하세요.

> [!NOTE]
> 추가 라이선스 및 구매 약관이 있는 타사 이미지는 기본 이미지로 사용할 수 없습니다. 이 Marketplace 이미지에 대한 자세한 내용은 [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) 또는 [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties) VM에 대한 가이드라인을 참조하세요.

- 관리 디스크로 VM이 생성되었는지 확인합니다. VM을 만들 때 기본 스토리지 설정입니다.
- 사용자 지정 스크립트 확장 등의 Azure 확장을 VM에 설치해서는 안 됩니다. 이미지에 미리 설치된 확장이포함되어 있으면 Batch 풀을 배포할 때 Azure에서 문제가 발생할 수 있습니다.
- 연결된 데이터 디스크를 사용하는 경우 VM 내에서 디스크를 탑재하고 포맷하여 사용해야 합니다.
- 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하도록 해야 합니다. Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상합니다.
- OS 디스크가 암호화되지 않았는지 확인합니다.
- VM이 실행되면 RDP(Windows용) 또는 SSH(Linux용)를 통해 연결합니다. 필요한 소프트웨어를 설치하거나 원하는 데이터를 복사합니다.  

### <a name="create-a-vm-snapshot"></a>VM 스냅샷 만들기

스냅샷은 VHD의 전체 읽기 전용 복사본입니다. VM OS 또는 데이터 디스크의 스냅샷을 만들려면 Azure Portal 또는 명령줄 도구를 사용할 수 있습니다. 스냅샷을 만드는 단계 및 옵션은 VM에 대한 지침을 [참조하세요.](../virtual-machines/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>스냅샷 하나 이상에서 이미지 만들기

스냅샷에서 관리되는 이미지를 만들려면 [az image create](/cli/azure/image) 명령과 같은 Azure 명령줄 도구를 사용합니다. OS 디스크 스냅샷을 지정하고 필요에 따라 데이터 디스크 스냅샷을 하나 이상 지정하여 이미지를 만들 수 있습니다.

## <a name="create-a-pool-from-a-managed-image"></a>관리되는 이미지에서 풀을 생성합니다.

관리형 이미지의 리소스 ID를 찾았으면 해당 이미지에서 사용자 지정 이미지 풀을 만듭니다. 다음 단계에서는 Batch Service 또는 Batch Management를 사용하여 사용자 지정 이미지 풀을 만드는 방법을 보여줍니다.

> [!NOTE]
> Azure AD 인증에 사용하는 ID에 이미지 리소스에 대한 권한이 있는지 확인합니다. [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)을 참조하세요.
>
> 관리형 이미지에 대한 리소스는 풀의 수명 동안 존재해야 합니다. 기본 리소스를 삭제하면 풀 크기를 조정할 수 없습니다.

### <a name="batch-service-net-sdk"></a>Batch Service .NET SDK

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>Batch Management REST API

REST API URI

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

요청 본문

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>대형 풀 관련 고려 사항

사용자 지정 이미지를 사용하여 VM이 수백 개인 풀을 만들려는 경우에는 위의 지침에 따라 VM 스냅샷에서 만든 이미지를 사용해야 합니다.

다음 고려 사항도 유의해야 합니다.

- **크기 제한** - 사용자 지정 이미지 사용 시에는 Batch의 풀 크기가 전용 컴퓨팅 노드 2,500개 또는 낮은 우선 순위 노드 1,000개로 제한됩니다.

  같은 이미지 또는 같은 기본 스냅샷을 기반으로 하는 여러 이미지를 사용해 여러 풀을 만드는 경우에는 풀의 총 컴퓨팅 노드 수가 위의 제한을 초과할 수 없습니다. 하나의 이미지나 해당 기본 스냅샷을 둘 이상의 풀에 사용하지 않는 것이 좋습니다.

  [인바운드 NAT 풀](pool-endpoint-configuration.md)을 사용하여 풀을 구성하는 경우에는 제한을 낮출 수 있습니다.

- **크기 조정 시간 제한** - 풀에 고정된 수의 노드가 포함되는 경우(풀 크기가 자동으로 조정되지 않음) 풀의 resizeTimeout 속성을 20~30분 등의 값으로 늘립니다. 풀이 제한 시간 내에 대상 크기에 도달하지 않으면 다른 [크기 조정 작업](/rest/api/batchservice/pool/resize)을 수행합니다.

  풀에 컴퓨팅 노드를 300개보다 많이 포함하려는 경우에는 풀이 대상 크기에 도달하도록 크기를 여러 번 조정해야 할 수 있습니다.
  
[Azure Compute 갤러리를](batch-sig-images.md)사용하면 더 많은 공유 이미지 복제본과 함께 사용자 지정된 이미지를 사용하여 더 큰 풀을 만들 수 있습니다. 공유 이미지를 사용하는 경우 풀이 안정된 상태에 도달하는 데 걸리는 시간은 최대 25% 더 빠르며, VM 유휴 대기 시간은 최대 30% 더 짧습니다.

## <a name="considerations-for-using-packer"></a>Packer 사용을 위한 고려 사항

Packer를 사용하여 관리형 이미지 리소스를 직접 만드는 작업은 사용자 구독 모드 Batch 계정으로만 수행할 수 있습니다. Batch 서비스 모드 계정의 경우 먼저 VHD를 만든 다음, VHD를 관리형 이미지 리소스로 가져와야 합니다. 풀 할당 모드(사용자 구독 또는 Batch 서비스)에 따라 관리형 이미지 리소스를 만드는 단계가 달라집니다.

관리형 이미지를 생성하는 데 사용되는 리소스가 사용자 지정 이미지를 참조하는 모든 풀의 수명 동안 존재하는지 확인합니다. 이렇게 하지 않으면 풀 할당 오류 및/또는 크기 조정 오류가 발생할 수 있습니다.

이미지 또는 기본 리소스가 제거되면 `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`와 유사한 오류가 발생할 수 있습니다. 이 오류가 발생하는 경우 기본 리소스가 제거되지 않았는지 확인합니다.

Packer를 사용하여 VM을 만드는 방법에 대한 자세한 내용은 [Packer를 사용하여 Linux 이미지 빌드](../virtual-machines/linux/build-image-with-packer.md) 또는 [Packer를 사용하여 Windows 이미지 빌드](../virtual-machines/windows/build-image-with-packer.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Compute 갤러리를](batch-sig-images.md) 사용하여 사용자 지정 풀을 만드는 방법을 알아봅니다.
- Batch에 대한 자세한 개요는 [Batch 서비스 워크플로 및 리소스](batch-service-workflow-features.md)를 참조하세요.
