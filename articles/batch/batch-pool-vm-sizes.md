---
title: 풀의 VM 크기 및 이미지 선택
description: Azure Batch 풀의 컴퓨팅 노드에 대해 사용 가능한 VM 크기 및 OS 버전을 선택하는 방법
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: seodec18
ms.openlocfilehash: bff6ef88345b280e2c977661814630af38fbe9d5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224824"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch 풀의 컴퓨팅 노드에 대한 VM 크기 선택

Azure Batch 풀에 대한 노드 크기를 선택하면 Azure에서 사용할 수 있는 거의 모든 VM 크기 중에서 선택할 수 있습니다. Azure는 다양한 작업에 맞게 다양한 크기의 Linux 및 Windows VM을 제공합니다.

## <a name="supported-vm-series-and-sizes"></a>지원되는 VM 시리즈 및 크기

### <a name="pools-in-virtual-machine-configuration"></a>Virtual Machine 구성의 풀

VM 구성의 Batch 풀은 거의 모든 [VM 크기](../virtual-machines/sizes.md)를 지원합니다. 지원되는 크기 및 제한 사항에 대해 자세히 알아보려면 다음 표를 참조하세요.

| VM 시리즈  | 지원되는 크기 |
|------------|---------|
| Basic A | Basic_A0(A0)을 *제외한* 모든 크기 |
| A | Standard_A0, Standard_A8, Standard_A9, Standard_A10, Standard_A11을 ‘제외한’ 모든 크기 |
| Av2 | 모든 크기 |
| b | 지원되지 않음 |
| DCsv2 | 모든 크기 |
| Dv2, DSv2 | 모든 크기 |
| Dv3, Dsv3 | 모든 크기 |
| Dav4, Dasv4 | 모든 크기 |
| Ddv4, Ddsv4 |  모든 크기 |
| Dv4, Dsv4 | 지원되지 않음 |
| Ev3, Esv3 | E64is_v3를 제외한 모든 크기 |
| Eav4, Easv4 | 모든 크기 |
| Edv4, Edsv4 | 모든 크기 |
| Ev4, Esv4 | 지원되지 않음 |
| F, Fs | 모든 크기 |
| Fsv2 | 모든 크기 |
| FX<sup>1</sup> | 모든 크기 |
| G, Gs | 모든 크기 |
| H | 모든 크기 |
| HB | 모든 크기 |
| HBv2 | 모든 크기 |
| HBv3 | 모든 크기 |
| HC | 모든 크기 |
| Ls | 모든 크기 |
| Lsv2 | 모든 크기 |
| M | 모든 크기 |
| Mv2<sup>1</sup> | 모든 크기 |
| NC | 모든 크기 |
| NCv2 | 모든 크기 |
| NCv3 | 모든 크기 |
| NCasT4_v3 | 모든 크기 |
| ND | 모든 크기 |
| NDv4 | 모든 크기 |
| NDv2 | 없음 - 아직 사용할 수 없습니다. |
| NP | 모든 크기 |
| NV | 모든 크기 |
| NVv3 | 모든 크기 |
| NVv4 | 모든 크기 |
| SAP HANA | 지원되지 않음 |

<sup>1</sup> 이 VM 시리즈는 2세대 VM 이미지에만 사용할 수 있습니다.

### <a name="using-generation-2-vm-images"></a>2세대 VM 이미지 사용

[Mv2](../virtual-machines/mv2-series.md)와 같은 일부 VM 시리즈는 [2세대 VM 이미지](../virtual-machines/generation-2.md)에만 사용할 수 있습니다. 2세대 VM 이미지는 [‘imageReference’](/rest/api/batchservice/pool/add#imagereference) 구성의 ‘sku’ 속성을 사용하여 VM이미지와 같이 지정됩니다. ‘sku’ 문자열에는 “-g2” 또는 “-gen2”와 같은 접미사가 있습니다. 2세대 이미지를 포함하여 Batch에서 지원되는 VM 이미지 목록을 가져오려면 [‘지원되는 이미지 나열’](/rest/api/batchservice/account/listsupportedimages) API, [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)또는 [Azure CLI](/cli/azure/batch/pool/supported-images)를 사용합니다.

### <a name="pools-in-cloud-services-configuration"></a>Cloud Services 구성의 풀

> [!WARNING]
> Cloud Services 구성 풀은 [사용되지 않습니다](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). 대신 Virtual Machine 구성 풀을 사용하세요.

Cloud Service 구성의 Batch 풀은 다음을 **제외** 한 모든 [Cloud Services에 적합한 VM 크기](../cloud-services/cloud-services-sizes-specs.md)를 지원합니다.

| VM 시리즈  | 지원되지 않는 크기 |
|------------|-------------------|
| A 시리즈   | 매우 작음       |
| Av2 시리즈 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>크기 고려 사항

- **애플리케이션 요구 사항** - 노드에서 실행할 애플리케이션의 특성 및 요구 사항을 고려합니다. 애플리케이션이 다중 스레드되었는지 여부 및 애플리케이션에서 소비하는 메모리 양과 같은 측면은 가장 적절하고 비용 효과적인 노드 크기를 결정하는 데 도움을 줄 수 있습니다. 다중 인스턴스 [MPI 작업](batch-mpi.md) 또는 CUDA 애플리케이션의 경우, 각각 특수화된 [HPC](../virtual-machines/sizes-hpc.md) 또는 [GPU 가능](../virtual-machines/sizes-gpu.md) VM 크기를 고려합니다. 자세한 내용은 [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요.

- **노드당 태스크 수** - 한 번에 하나의 태스크가 하나의 노드에서 실행된다고 가정하여 노드 크기를 선택하는 것이 일반적입니다. 하지만 작업 실행 중에 컴퓨팅 노드에서 여러 태스크(따라서 여러 애플리케이션 인스턴스)를 [병렬로 실행하는](batch-parallel-node-tasks.md) 것이 유리할 수도 있습니다. 이 경우 병렬 태스크 실행에 대해 증가되는 수요를 수용할 수 있도록 멀티 코어 노드 크기를 선택하는 것이 일반적입니다.

- **다른 태스크에 대한 로드 수준** - 풀의 모든 노드는 동일한 크기입니다. 상이한 시스템 요구 사항 및/또는 부하 수준으로 애플리케이션을 실행하려면 별도의 풀을 사용하는 것이 좋습니다.

- **지역 가용성** - VM 시리즈 또는 크기는 배치 계정을 만든 지역에서 사용하지 못할 수도 있습니다. 크기를 사용할 수 있는지를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

- **할당량** - 배치 계정의 [코어 할당량](batch-quota-limit.md#resource-quotas)은 Batch 풀에 추가할 수 있는 지정된 크기의 노드 수를 제한할 수 있습니다. 필요한 경우 [할당량 증가를 요청](batch-quota-limit.md#increase-a-quota)할 수 있습니다.

- **풀 구성** - 일반적으로 Cloud Service 구성에 비해 Virtual Machine 구성에서 풀을 만들 때 더 많은 VM 크기 옵션을 사용할 수 있습니다.

## <a name="supported-vm-images"></a>지원되는 VM 이미지

다음 API 중 하나를 사용하여 각 이미지에 대한 노드 에이전트 SKU ID를 비롯하여 현재 Batch에서 지원되는 Windows 및 Linux VM 이미지 목록을 반환합니다.

- Batch 서비스 REST API: [지원되는 이미지 나열](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [az batch pool supported-images](/cli/azure/batch/pool/supported-images)

Batch 지원 EOL(end of Life) 날짜가 임박한 이미지는 피하는 것이 좋습니다. 해당 날짜는 [`ListSupportedImages` API](/rest/api/batchservice/account/listsupportedimages), [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage) 또는 [Azure CLI](/cli/azure/batch/pool/supported-images)를 통해 검색할 수 있습니다. Batch 풀 VM 이미지 선택에 대한 자세한 내용은 [Batch 모범 사례 가이드](best-practices.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- 계산 집약적 VM 크기를 사용하는 방법에 대한 자세한 내용은 [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요.
