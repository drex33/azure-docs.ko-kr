---
title: Azure Batch 풀에 대해 사용 후 삭제 OS 디스크 노드 사용
description: 사용 후 삭제 OS 디스크 노드를 사용 하는 Batch 풀을 만드는 방법 및 이유를 알아봅니다.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 760e2848917c6b1c502ac6ba9aae00d5ddd89c8c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544447"
---
# <a name="use-ephemeral-os-disk-nodes-for-azure-batch-pools"></a>Azure Batch 풀에 대해 사용 후 삭제 OS 디스크 노드 사용

일부 Azure VM (가상 머신) 시리즈는 노드 가상 머신 로컬 저장소에 OS 디스크를 만드는 사용 후 [삭제 os](../virtual-machines/ephemeral-os-disks.md)디스크의 사용을 지원 합니다. 기본 Batch 풀 구성은 노드 OS 디스크에 대해 [Azure managed disks](../virtual-machines/managed-disks-overview.md) 를 사용 합니다. 여기서 관리 디스크는 실제 디스크와 유사 하지만 가상화 되어 원격 Azure Storage에서 지속 됩니다.

Batch 워크 로드의 경우, 사용 후 삭제 OS 디스크를 사용 하는 경우의 주요 이점은 풀과 관련 된 비용, 더 빠른 노드 시작 시간 및 향상 된 응용 프로그램 성능을 향상 시킬 수 있습니다. 사용 후 삭제 OS 디스크를 워크 로드에 사용 해야 하는지 여부를 선택할 때 다음 사항을 고려 하세요.

- 임시 OS 디스크에 대 한 읽기/쓰기 대기 시간이 낮을수록 응용 프로그램 성능이 향상 될 수 있습니다.
- 임시 OS 디스크에 대 한 저장소 비용은 없으며 관리 되는 각 OS 디스크에 대 한 비용이 있습니다.
- 일괄 처리에 의해 지원 되는 경우 노드의 이미지로 다시 설치는 관리 디스크와 비교 하 여 임시 디스크에 비해 더 빠릅니다.
- 사용 후 삭제 OS 디스크를 사용 하는 경우 노드 시작 시간이 약간 더 빨라질 수 있습니다.
- 사용 후 삭제 OS 디스크는 내구성이 높지 않으며 사용 가능 합니다. 어떤 이유로 든 VM이 제거 되 면 OS 디스크가 손실 됩니다. 일괄 처리 작업은 본질적으로 상태 비저장 이며 일반적으로 유지 되는 OS 디스크에 대 한 변경 내용을 사용 하지 않으므로 대부분의 Batch 작업에 사용 하기에 적합 합니다.
- 현재는 모든 Azure VM 시리즈에서 사용 후 삭제 OS 디스크를 지원 하지 않습니다. VM 크기가 사용 후 삭제 OS 디스크를 지원 하지 않는 경우 관리 되는 OS 디스크를 사용 해야 합니다.

> [!NOTE]
> 임시 OS 디스크 구성은 ' virtualMachineConfiguration ' 풀에만 적용 되며 ' cloudServiceConfiguration ' 풀에서 지원 되지 않습니다. ' CloudServiceConfiguration ' 풀이 모든 기능을 지원 하지 않으며 새로운 기능이 계획 되지 않으므로 Batch 풀에 ' virtualMachineConfiguration '를 사용 하는 것이 좋습니다. [2024년 2월 29일 이후](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/) 새 ‘cloudServiceConfiguration’ 풀을 생성하거나 기존 풀에 새 노드를 추가할 수 없습니다. 자세한 내용은 [Cloud Services에서 가상 머신으로 Batch 풀 구성 마이그레이션](batch-pool-cloud-service-to-virtual-machine-configuration.md)을 참조하세요.

## <a name="vm-series-support"></a>VM 시리즈 지원

VM 시리즈에서 임시 OS 디스크를 지원 하는지 확인 하려면 각 VM 인스턴스에 대 한 설명서를 확인 합니다. 예를 들어 [Ddv4 및 Ddsv4 시리즈](../virtual-machines/ddv4-ddsv4-series.md) 는 임시 OS 디스크를 지원 합니다.

또는 프로그래밍 방식으로 쿼리하여 ' EphemeralOSDiskSupported ' 기능을 확인할 수 있습니다. 이 기능을 쿼리 하는 예제 PowerShell cmdlet은 [임시 OS 디스크](../virtual-machines/ephemeral-os-disks.md#frequently-asked-questions)에 대 한 질문과 대답에 제공 됩니다.

## <a name="create-a-pool-that-uses-ephemeral-os-disks"></a>사용 후 삭제 OS 디스크를 사용 하는 풀 만들기

`EphemeralOSDiskSettings`속성은 기본적으로 설정 되어 있지 않습니다. 풀 노드에서 사용 후 삭제 OS 디스크를 구성 하려면이 속성을 설정 해야 합니다.

다음 예에서는 노드가 관리 되지 않는 디스크 및 임시 OS 디스크를 사용 하는 Batch 풀을 만드는 방법을 보여 줍니다.

### <a name="batch-net-api"></a>Batch .NET API

```csharp
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: nodeAgentSku
        );
virtualMachineConfiguration.OSDisk = new OSDisk();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings = new DiffDiskSettings();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings.Placement = DiffDiskPlacement.CacheDisk;
```

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Azure Batch 작업에 연결 될 수 있는 비용](budget.md)에 대해 알아봅니다.
