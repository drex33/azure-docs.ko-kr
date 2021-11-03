---
title: Azure HPC Cache 시나리오
description: 컴퓨팅 작업이 Azure HPC Cache에서 제대로 작동하는지 확인하는 방법을 설명합니다.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: femila
ms.openlocfilehash: 40434634fde18a9ff9e63f9e2174cd412182b500
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078563"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>작업이 Azure HPC Cache에 적합한가요?

Azure HPC Cache는 다양한 분야에서의 고성능 컴퓨팅 작업을 위해 데이터에 대한 액세스 속도를 높일 수 있습니다. 그러나 일부 유형의 워크플로에는 적합하지 않습니다. 이 문서에서는 HPC Cache가 사용자의 필요에 적합한 옵션인지를 결정하는 방법에 대한 지침을 제공합니다.

또한 [개요](hpc-cache-overview.md) 문서에서는 Azure HPC Cache를 사용하는 시기와 사용 사례의 몇 가지 예에 대한 간략한 개요를 제공합니다.

또한 [NFS 탑재 BLOB 스토리지](../storage/blobs/network-file-system-protocol-support.md)를 효과적으로 사용하는 방법에 대해서는 [이 문서](nfs-blob-considerations.md)를 참조하세요.

## <a name="nfs-version-30-applications"></a>NFS 버전 3.0 애플리케이션

Azure HPC Cache는 NFS 3.0 클라이언트만 지원합니다.

## <a name="high-read-to-write-ratio"></a>높은 읽기/쓰기 비율

컴퓨팅 클라이언트에서 작성보다 읽기를 더 많이 수행하는 작업은 일반적으로 캐시에 적합합니다. 예를 들어 읽기/쓰기 비율이 80/20 또는 70/30인 경우, Azure HPC Cache는 자주 요청되는 파일을 원격 스토리지에서 반복하여 가져오는 것보다는 캐시에서 제공함으로써 도움이 될 수 있습니다.

파일을 가져와 캐시에 처음으로 저장하게 되면 일반적인 클라이언트 요청에서 스토리지에 대해 직접적으로 약간의 추가 대기 시간이 발생합니다. 따라서 효율성은 클라이언트에서 다음에 동일한 파일을 요청하는 경우 증가합니다. 이러한 현상은 파일 크기가 클 때 특히 두드러집니다. 각 클라이언트 요청이 고유한 경우에는 HPC Cache의 영향이 제한됩니다. 그러나 파일이 클수록 성능은 첫 번째 액세스 후 시간이 지남에 따라 향상됩니다.

## <a name="file-based-analytic-workload"></a>파일 기반 분석 워크로드

Azure HPC Cache는 파일 기반 데이터를 사용하고 여러 컴퓨팅 클라이언트에서 실행되는 파이프라인에 적합하며, 컴퓨팅 클라이언트가 Azure 가상 머신인 경우 특히 그렇습니다. 이는 파일 액세스 시간이 길어서 발생하는 성능 저하나 비일관성 문제를 해결하는 데 도움이 될 수 있습니다.

## <a name="remote-data-access"></a>원격 데이터 액세스

Azure HPC Cache는 워크로드가 컴퓨팅 리소스에 더 가깝게 이동할 수 없는 원격 데이터에 액세스해야 하는 경우 대기 시간을 줄이는 데 도움이 될 수 있습니다. 예를 들어, 다른 Azure 지역 또는 고객 데이터 센터의 WAN 환경 끝에 레코드가 있을 수 있습니다. (이를 때때로 "파일-버스팅"이라고 합니다.)

## <a name="heavy-request-load"></a>높은 요청 로드

여러 클라이언트가 원본에서 동시에 데이터를 요청하는 경우 Azure HPC Cache가 파일 액세스 속도를 높일 수 있습니다. 예를 들어 고성능 컴퓨팅 클러스터와 함께 사용하는 경우 Azure HPC Cache는 캐시를 통해 많은 수의 동시 요청에 대한 확장성을 제공합니다.

## <a name="compute-resources-are-located-in-azure"></a>컴퓨팅 리소스는 Azure에 있습니다.

Azure 가상 머신은 고성능 컴퓨팅 워크로드에 있어서 확장 가능하고 비용 효율적인 해답입니다. Azure HPC Cache는 필요한 정보를 가까이에 가져옴으로써, 특히 원본 데이터가 원격 시스템에 저장된 경우 도움이 될 수 있습니다.

고객이 Azure 가상 머신에서 현재 파이프라인을 "있는 그대로" 실행하려고 하는 경우, Azure HPC Cache는 확장성을 위해 POSIX 기반 공유 스토리지(또는 캐싱) 솔루션을 제공할 수 있습니다.

Azure HPC Cache를 사용하면 Azure BLOB 스토리지로 기본 호출이 진행되도록 작업 파이프라인을 재설계할 필요가 없습니다. 원본 시스템의 데이터에 액세스하거나 HPC Cache를 사용하여 새 BLOB 컨테이너로 옮길 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 캐시의 계획 및 구성 방법에 대한 자세한 내용은 [개요](hpc-cache-overview.md) 및 [필수 구성 요소](hpc-cache-prerequisites.md) 문서를 참조하세요.
* Azure HPC Cache로 [NFS 사용 BLOB 스토리지](nfs-blob-considerations.md)를 사용할 때의 고려 사항을 확인하세요.
