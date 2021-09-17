---
title: Cloud Services에서 Virtual Machines로 Batch 풀 구성 마이그레이션
description: 풀 구성을 최신 및 권장 구성으로 업데이트하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 9d4ce46c5291333f161f260c3b4706ea9dcf9a24
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538888"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Cloud Services에서 Virtual Machine으로 Batch 풀 구성 마이그레이션

현재 Batch 풀은 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 또는 [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration)을 사용하여 만들 수 있습니다. 이 구성은 모든 Batch 기능을 지원하므로 Virtual Machine Configuration만 사용하는 것이 좋습니다.

Cloud Services Configuration 풀은 현재 Batch 기능 중 일부를 지원하지 않으며 새로 추가되는 기능을 지원하지 않을 것입니다. [2024년 2월 29일 이후](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)에는 새 'CloudServiceConfiguration' 풀을 만들거나 새 노드를 기존 풀에 추가할 수 없습니다.

Batch 솔루션에서 현재 'cloudServiceConfiguration' 풀을 사용하는 경우 최대한 빨리 'virtualMachineConfiguration'으로 변경하는 것이 좋습니다. 이렇게 하면 확장된 [VM 시리즈](batch-pool-vm-sizes.md), Linux VM, [컨테이너](batch-docker-container-workloads.md), [Azure Resource Manager 가상 네트워크](batch-virtual-network.md) 및 [노드 디스크 암호화](disk-encryption.md)와 같은 모든 Batch 기능을 활용할 수 있습니다.

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Virtual Machine Configuration을 사용하여 풀 만들기

'cloudServiceConfiguration'을 사용하는 기존 활성 풀을 'virtualMachineConfiguration'을 사용하도록 전환할 수 없습니다. 대신 새 풀을 만들어야 합니다. 새 'virtualMachineConfiguration' 풀을 만들고 모든 작업과 태스크를 복제했으면 더 이상 사용하지 않는 이전 'cloudServiceConfiguration' 풀을 삭제할 수 있습니다.

모든 Batch API, 명령줄 도구, Azure Portal 및 Batch Explorer UI를 사용하면 'virtualMachineConfiguration'을 사용하여 풀을 만들 수 있습니다.

'virtualMachineConfiguration’을 사용하는 풀을 만드는 프로세스에 대한 연습은 [.NET 자습서](tutorial-parallel-dotnet.md) 또는 [Python 자습서](tutorial-parallel-python.md)를 참조하세요.

## <a name="pool-configuration-differences"></a>풀 구성 차이점

두 구성의 주요 차이점은 다음과 같습니다.

- 'cloudServiceConfiguration' 풀 노드는 Windows OS만 사용합니다. 'virtualMachineConfiguration' 풀은 Linux 또는 Windows OS를 사용할 수 있습니다.
- 'cloudServiceConfiguration' 풀에 비해 'virtualMachineConfiguration' 풀에는 컨테이너 지원, 데이터 디스크 및 디스크 암호화와 같은 다양한 기능이 있습니다.
- 풀 및 노드 시작 및 삭제 시간은 'cloudServiceConfiguration' 풀과 'virtualMachineConfiguration' 풀 간에 약간 다를 수 있습니다.
- 'virtualMachineConfiguration' 풀 노드는 관리형 OS 디스크를 활용합니다. 각 노드에 사용되는 [관리 디스크 유형](../virtual-machines/disks-types.md)은 풀에 대해 선택한 VM 크기에 따라 달라집니다. 풀에 대해 's' VM 크기가 지정된 경우(예: 'Standard_D2s_v3') 프리미엄 SSD가 사용됩니다. 'non-s' VM 크기가 지정된 경우(예: 'Standard_D2_v3') 표준 HDD가 사용됩니다.

   > [!IMPORTANT]
   > Virtual Machines 및 Virtual Machine Scale Sets와 마찬가지로 각 노드에 사용되는 OS 관리 디스크는 비용을 발생시킵니다. 이는 VM 비용에 추가됩니다. ' virtualMachineConfiguration ' 풀은 VM 캐시 또는 임시 SSD에서 OS 디스크를 만드는 임시 [os 디스크](create-pool-ephemeral-os-disk.md)를 사용 하 여 관리 디스크와 관련 된 추가 비용을 피할 수 있습니다. 노드 로컬 SSD에 OS 디스크가 만들어지므로 ' cloudServiceConfiguration ' 노드에 대 한 OS 디스크 비용이 없습니다.

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory 사용자 지정 작업 풀

Azure Batch 풀은 Data Factory 사용자 지정 작업을 실행하는 데 사용할 수 있습니다. 사용자 지정 작업을 실행하는 데 사용되는 모든 'cloudServiceConfiguration' 풀을 삭제하고 새 'virtualMachineConfiguration' 풀을 만들어야 합니다.

Data Factory 사용자 지정 작업을 실행할 새 풀을 만들 때 다음 방법을 따르세요.

- 새 풀을 만들고 기존 풀을 삭제하기 전에 모든 파이프라인을 일시 중지하여 실행이 중단되지 않도록 합니다.
- 연결된 서비스 구성 변경을 방지하기 위해 동일한 풀 ID를 사용할 수 있습니다.
- 새 풀이 만들어진 경우 파이프라인을 다시 시작합니다.

Azure Batch를 사용하여 Data Factory 사용자 지정 작업을 실행하는 방법에 대한 자세한 내용은 [Azure Batch 연결된 서비스](../data-factory/compute-linked-services.md#azure-batch-linked-service) 및 [Data Factory 파이프라인의 사용자 지정 작업](../data-factory/transform-data-using-dotnet-custom-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [풀 구성](nodes-and-pools.md#configurations)에 대해 자세히 알아보세요.
- [풀 모범 사례](best-practices.md#pools)에 대해 자세히 알아보세요.
- [풀 덧셈](/rest/api/batchservice/pool/add) 및 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)에 대한 REST API 참조를 참조하세요.