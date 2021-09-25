---
title: Azure Blob Storage의 NFS 3.0 성능 고려 사항 | Microsoft Docs
description: 이 문서의 권장 사항을 사용하여 NFS(네트워크 파일 시스템) 3.0 스토리지 요청의 성능을 최적화합니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 8fb4583fbf04637c58795d6532dcce82ccb8168e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624978"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage"></a>Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 성능 고려 사항

이제 Blob Storage는 NFS(네트워크 파일 시스템) 3.0 프로토콜을 지원합니다. 이 문서에는 스토리지 요청의 성능을 최적화하는 데 도움이 되는 권장 사항이 포함되어 있습니다. Azure Blob Storage의 NFS 3.0 지원에 대한 자세한 내용은 [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원](network-file-system-protocol-support.md)을 참조하세요.

## <a name="add-clients-to-increase-throughput"></a>클라이언트를 추가하여 처리량 늘리기

Azure Blob Storage 최대 스토리지 계정 송신 및 수신 제한에 도달할 때까지 선형으로 크기 조정됩니다. 따라서 애플리케이션은 더 많은 클라이언트를 사용하여 더 높은 처리량을 달성할 수 있습니다. 스토리지 계정 송신 및 수신 제한을 보려면 [표준 스토리지 계정의 확장성 및 성능 목표](../common/scalability-targets-standard-account.md)를 참조하세요.

다음 차트에서는 더 많은 클라이언트를 추가할 때 대역폭을 늘리는 방법을 보여 줍니다. 이 차트에서 클라이언트는 VM(가상 머신)이며 계정은 표준 성능 계층을 사용합니다.

> [!div class="mx-imgBorder"]
> ![표준 성능](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

다음 차트는 프리미엄 성능 계층을 사용하는 계정에 적용될 때 이와 동일한 효과를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![프리미엄 성능](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>작은 규모의 애플리케이션에 프리미엄 성능 계층 사용

클라이언트를 더 추가하여 모든 애플리케이션을 확장할 수 있는 것은 아닙니다. 이러한 애플리케이션의 경우 [Azure 프리미엄 블록 Blob Storage 계정](../common/storage-account-create.md)은 일관된 짧은 대기 시간과 높은 트랜잭션 속도를 제공합니다. 프리미엄 블록 Blob Storage 계정은 스레드 및 클라이언트 수를 줄여 최대 대역폭에 도달할 수 있습니다. 예를 들어 단일 클라이언트의 경우 프리미엄 블록 Blob Storage 계정은 표준 성능 범용 v2 스토리지 계정에 사용된 것과 동일한 설정에 비해 **2.3배** 의 대역폭을 달성할 수 있습니다.

다음 차트의 각 막대는 프리미엄 및 표준 성능 스토리지 계정 간에 달성된 대역폭의 차이를 보여 줍니다. 클라이언트 수가 늘어나면 이러한 차이가 줄어듭니다.

> [!div class="mx-imgBorder"]
> ![상대적 성능](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="improve-read-ahead-size-to-increase-large-file-read-throughput"></a>큰 파일 읽기 처리량을 늘리기 위해 미리 읽기 크기 개선

read_ahead_kb kernel 매개 변수는 지정된 읽기 요청을 수행한 후에 읽어야 하는 추가 데이터의 양을 나타냅니다. 이 매개 변수를 16MB로 늘려 대용량 파일 읽기 처리량을 향상시킬 수 있습니다.

```
export AZMNT=/your/container/mountpoint

echo 15728640 > /sys/class/bdi/0:$(stat -c "%d" $AZMNT)/read_ahead_kb
```

## <a name="avoid-frequent-overwrites-on-data"></a>데이터에 대한 자주 덮어쓰기 방지

새 쓰기 작업보다는 덮어쓰기 작업을 완료하는 데 시간이 더 오래 걸립니다. NFS 덮어쓰기 작업, 특히 부분 내부 파일 편집은 몇 가지 기본 Blob 작업(읽기, 수정 및 쓰기 작업)의 조합이기 때문입니다. 그러므로 자주 사용하는 편집이 필요한 애플리케이션은 NFS 사용 Blob Storage 계정에 적합하지 않습니다.

## <a name="deploy-azure-hpc-cache-for-latency-sensitive-applications"></a>대기 시간에 민감한 애플리케이션을 위한 Azure HPC Cache 배포

일부 애플리케이션에는 높은 처리량 외에 짧은 대기 시간이 필요할 수 있습니다. [Azure HPC Cache](../../hpc-cache/nfs-blob-considerations.md)를 배포하여 대기 시간을 크게 개선할 수 있습니다. [Blob Storage의 대기 시간](storage-blobs-latency.md)에 대해 자세히 알아봅니다.

## <a name="other-best-practice-recommendations"></a>기타 모범 사례 권장 사항

- 네트워크 대역폭이 충분한 VM을 사용합니다.

- 워크 로드에서 허용 하는 경우 여러 탑재 위치를 사용 합니다.

- 최대한 많은 스레드를 사용합니다.

- 큰 블록 크기를 사용합니다.

- 스토리지 계정과 동일한 지역에 있는 클라이언트에서 스토리지 요청을 만듭니다. 이렇게 하면 네트워크 대기 시간을 향상시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Blob Storage의 NFS 3.0 지원에 대한 자세한 내용은 [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원](network-file-system-protocol-support.md)을 참조하세요.

- 시작하려면 [NFS(네트워크 파일 시스템) 3.0 프로토콜을 사용하여 Blob 스토리지 탑재](network-file-system-protocol-support-how-to.md)를 참조하세요.
