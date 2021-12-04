---
title: 지역별 리소스 가용성
description: 여러 다른 Azure 지역의 Azure Container Instances 서비스에 대한 컴퓨팅 및 메모리 리소스 가용성입니다.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: fcf3f3d5f2e7f4c9b5f070abb1c47cfccd0ef788
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133545315"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 지역의 Azure Container Instances에 대한 리소스 가용성

이 문서에서는 Azure 지역 및 대상 운영 체제별 Azure Container Instances 컴퓨팅, 메모리 및 스토리지 리소스의 가용성을 자세히 설명합니다. Azure Container Instances가 사용 가능한 지역에 대한 일반 목록은 [사용 가능한 지역](https://azure.microsoft.com/regions/services/)을 참조하세요.

제공되는 값은 [컨테이너 그룹](container-instances-container-groups.md) 배포당 사용 가능한 최대 리소스입니다. 값은 게시 시점에 제공됩니다.

> [!NOTE]
> 이러한 리소스 제한 내에서 만든 컨테이너 그룹은 배포 지역 내의 사용 가능 여부의 적용을 받습니다. 영역이 과부하 상태에 있는 경우 인스턴스를 배포할 때 오류가 발생할 수 있습니다. 이러한 배포 실패를 완화하려면 리소스 설정이 낮은 인스턴스를 배포하거나, 나중에 배포를 시도하거나, 사용 가능한 리소스가 있는 다른 지역에 배포를 시도합니다.

배포의 할당량 및 기타 제한에 대한 내용은 [Azure Container Instances의 할당량 및 제한](container-instances-quotas.md)을 참조하세요.

## <a name="linux-container-groups"></a>Linux 컨테이너 그룹

다음 지역과 최대 리소스는 일반 배포, [Azure 가상 네트워크](container-instances-vnet.md) 배포, 그리고 [GPU 리소스](container-instances-gpu.md)를 이용한 배포에서 Linux 컨테이너가 있는 컨테이너 그룹에 사용할 수 있습니다(미리 보기).

> [!IMPORTANT]
> 지역의 최대 리소스는 배포에 따라 다릅니다. 예를 들어 같은 지역이어도 Azure 가상 네트워크 배포에서는 일반 배포를 할 때와는 다른 최대 CPU와 메모리 크기를 가질 수 있습니다. 동일한 지역에 GPU 리소스를 사용하는 배포에 대한 다른 최댓값 집합이 있을 수도 있습니다. 아래 테이블에서 해당 지역의 최댓값을 확인하기 전에 배포 유형을 확인합니다.

> [!NOTE]
> 일부 지역에서는 가용성 영역(아래 표의 'N/A'로 나타냄)을 지원하지 않고 일부 지역에는 가용성 영역이 있지만 ACI는 현재 이 기능을 활용하지 않습니다(아래 표의 'N'으로 나타냄). 자세한 내용은 [가용성 영역이 있는 Azure 지역을 참조하세요.][az-region-support]

| 지역 | 최대 CPU | 최대 메모리(GB) | VNET 최대 CPU | VNET 최대 메모리(GB) | 스토리지(GB) | GPU SKU(미리 보기) | 가용성 영역 지원 |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: | :----: |
| 오스트레일리아 동부 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | Y |
| 오스트레일리아 남동부 | 4 | 14 | N/A | 해당 없음 | 50 | 해당 없음 | N |
| 브라질 남부 | 4 | 16 | 2 | 8 | 50 | 해당 없음 | Y |
| 캐나다 중부 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | N |
| 캐나다 동부 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | N |
| 인도 중부 | 4 | 16 | 4 | 4 | 50 | V100 | N |
| 미국 중부 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | Y |
| 동아시아 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | N |
| 미국 동부 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | Y |
| 미국 동부 2 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | Y |
| 프랑스 중부 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | Y|
| 독일 중서부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | 해당 없음 | Y |
| 일본 동부 | 2 | 8 | 4 | 16 | 50 | 해당 없음 | Y |
| 일본 서부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | 해당 없음 | N |
| 한국 중부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | 해당 없음 | N |
| 미국 중북부 | 2 | 3.5 | 4 | 16 | 50 | K80, P100, V100 | N |
| 북유럽 | 4 | 16 | 4 | 16 | 50 | K80 | Y |
| 노르웨이 동부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | 해당 없음 | N |
| 미국 중남부 | 4 | 16 | 4 | 16 | 50 | V100 | Y |
| 동남아시아 | 4 | 16 | 4 | 16 | 50 | P100, V100 | Y |
| 인도 남부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | K80 | N |
| 스위스 북부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | 해당 없음 | N |
| 영국 남부 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | Y|
| 영국 서부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | 해당 없음 | N |
| 아랍에미리트 북부 | 4 | 16 | 해당 없음 | 해당 없음 | 50 | 해당 없음 | N |
| 미국 중서부| 4 | 16 | 4 | 16 | 50 | 해당 없음 | N |
| 서유럽 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | Y |
| 미국 서부 | 4 | 16 | 4 | 16 | 50 | 해당 없음 | N |
| 미국 서부 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | Y |

다음 최대 리소스는 [GPU 리소스](container-instances-gpu.md)를 사용해 배포된 컨테이너 그룹에 사용 가능합니다(미리 보기).

> [!IMPORTANT]
> 현재 GPU 리소스를 사용하는 배포는 Azure 가상 네트워크 배포에서 지원되지 않으며 Linux 컨테이너 그룹에서만 사용할 수 있습니다.

| GPU SKUs | GPU 수 | 최대 CPU | 최대 메모리(GB) | 스토리지(GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows 컨테이너 그룹

다음 지역 및 최대 리소스는 [지원 및 미리 보기](./container-instances-faq.yml) Windows Server 컨테이너를 사용하는 컨테이너 그룹에 사용할 수 있습니다.

> [!IMPORTANT]
> 현재는 Windows 컨테이너 그룹을 사용한 배포가 Azure 가상 네트워크 배포에서 지원되지 않습니다.

### <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> 1B 및 2B 호스트는 Windows Server 2016 사용되지 않습니다. 1B, 2B 및 3B 호스트에 대한 자세한 정보는 [호스트 및 컨테이너 버전 호환성](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility)을 참조하세요.

| 지역 |3B Max CPU | 3B 최대 메모리(GB) | 스토리지(GB) |
| -------- | :----: | :-----: | :-------: |
| 오스트레일리아 동부 | 2 | 8 | 20 |
| 브라질 남부 | 4 | 16 | 20 |
| 캐나다 중부  | 2 | 3.5 | 20 |
| 인도 중부 | 2 | 3.5 | 20 |
| 미국 중부 | 2 | 3.5 | 20 |
| 동아시아 | 2 | 3.5 | 20 |
| 미국 동부 | 2 | 8 | 20 |
| 미국 동부 2 | 4 | 16 | 20 |
| 일본 동부 | 4 | 16 | 20 |
| 한국 중부 | 4 | 16 | 20 |
| 미국 중북부 | 4 | 16 | 20 |
| 북유럽 | 2 | 8 | 20 |
| 미국 중남부 | 2 | 8 | 20 |
| 동남아시아 | 2 | 3.5 | 20 |
| 인도 남부 | 2 | 3.5 | 20 |
| 영국 남부 | 2 | 3.5 | 20 |
| 미국 중서부 | 2 | 8 | 20 |
| 서유럽 | 4 | 16 | 20 |
| 미국 서부 | 2 | 8 | 20 |
| 미국 서부 2 | 2 | 3.5 | 20 |

### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> 1B 및 2B 호스트는 Windows Server 2019 LTSC에서 더 이상 사용되지 않습니다. 1B, 2B 및 3B 호스트에 대한 자세한 정보는 [호스트 및 컨테이너 버전 호환성](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility)을 참조하세요.

| 지역 | 3B Max CPU | 3B 최대 메모리(GB) | 스토리지(GB) | 가용성 영역 지원 |
| -------- | :----: | :-----: | :-------: |
| 오스트레일리아 동부 | 4 | 16 | 20 | Y |
| 브라질 남부 | 4 | 16 | 20 | Y |
| 캐나다 중부 | 4 | 16 | 20 | N |
| 인도 중부 | 4 | 16 | 20 | N |
| 미국 중부 | 4 | 16 | 20 | Y |
| 동아시아 | 4 | 16 | 20 | N |
| 미국 동부 | 4 | 16 | 20 | Y |
| 미국 동부 2 | 2 | 3.5 | 20 | Y |
| 프랑스 중부 | 4 | 16 | 20 | Y |
| 일본 동부 | 4 | 16 | 20 | Y |
| 한국 중부 | 4 | 16 | 20 | N |
| 미국 중북부 | 4 | 16 | 20 | N |
| 북유럽 | 4 | 16 | 20 | Y |
| 미국 중남부 | 4 | 16 | 20 | Y |
| 동남아시아 | 4 | 16 | 20 | Y |
| 인도 남부 | 4 | 16 | 20 | N |
| 영국 남부 | 4 | 16 | 20 | Y |
| 미국 중서부 | 4 | 16 | 20 | N |
| 서유럽 | 4 | 16 | 20 | Y |
| 미국 서부 | 4 | 16 | 20 | N |
| 미국 서부 2 | 4 | 16 | 20 | Y |

## <a name="next-steps"></a>다음 단계

추가 지역 또는 증가된 리소스 가용성을 확인하고 싶은 경우 [aka.ms/aci/feedback](https://aka.ms/aci/feedback)을 통해 팀에 알려주세요.

컨테이너 인스턴스 배포 문제 해결 방법에 대한 내용은 [Azure Container Instances로의 배포 문제 해결](container-instances-troubleshooting.md)을 참조하세요.


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[az-region-support]: /azure/availability-zones/az-overview#regions
