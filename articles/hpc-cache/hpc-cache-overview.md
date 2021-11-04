---
title: Azure HPC Cache 개요
description: 고성능 컴퓨팅을 위한 파일 액세스 가속기 솔루션인 Azure HPC Cache에 대해 설명합니다.
author: femila
ms.service: hpc-cache
ms.topic: overview
ms.date: 03/11/2021
ms.author: femila
ms.openlocfilehash: 9a750884c28d9ad74c07106c4d55be4a896b8ea6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011185"
---
# <a name="what-is-azure-hpc-cache"></a>Azure HPC Cache란?

Azure HPC Cache는 HPC(고성능 컴퓨팅) 작업을 위해 데이터 액세스 속도를 높입니다. Azure에서 파일을 캐싱하여 Azure HPC Cache는 기존 워크플로에 대한 클라우드 컴퓨팅의 확장성을 제공합니다. 이 서비스는 데이터가 로컬 데이터 센터 NAS(Network Attached Storage) 환경과 같이 WAN 연결을 통해 저장된 워크플로에서도 사용할 수 있습니다.

Azure HPC Cache는 Azure Portal에서 쉽게 시작하고 모니터링할 수 있습니다. 기존 NFS 스토리지 또는 새 Blob 컨테이너가 집계된 네임스페이스의 일부로 포함될 수 있으므로, 백 엔드 스토리지 대상을 변경하더라도 클라이언트 액세스가 간소화됩니다.

## <a name="overview-video"></a>개요 동영상

[![비디오 썸네일: Azure HPC Cache 개요 - 클릭하여 비디오 페이지 방문](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

위의 이미지를 클릭하여 [Azure HPC Cache의 간단한 개요](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)를 시청하세요.

## <a name="use-cases"></a>사용 사례

Azure HPC Cache는 다음과 같은 워크플로에서 생산성이 가장 높습니다.

* 읽기 작업이 많은 파일 액세스 워크플로
* NFS를 통해 액세스할 수 있는 스토리지, Azure Blob 또는 둘 다에 저장된 데이터
* CPU 코어가 최대 75,000개인 컴퓨팅 팜

Azure HPC Cache는 다양한 산업의 광범위한 워크플로에 추가할 수 있습니다. 이 서비스는 많은 머신이 낮은 대기 시간으로 대규모 파일 세트에 액세스해야 하는 모든 시스템에 도움이 됩니다. 아래 섹션에서는 특정 예제를 제공합니다.

### <a name="visual-effects-vfx-rendering"></a>시각 효과(VFX) 렌더링

미디어 및 엔터테인먼트에서 Azure HPC Cache는 시간이 중요한 렌더링 프로젝트의 데이터 액세스 속도를 높일 수 있습니다. VFX 렌더링 워크플로에서는 많은 컴퓨팅 노드의 막바지 처리가 필요한 경우가 많습니다. 일반적으로 이러한 워크플로의 데이터는 온-프레미스 NAS 환경에 있습니다. Azure HPC Cache는 해당 파일 데이터를 클라우드에 캐시하여 대기 시간을 줄이고 요청 시 렌더링의 유연성을 향상할 수 있습니다.

### <a name="life-sciences"></a>생명 과학

많은 생명 과학 워크플로에서도 스케일 아웃 파일 캐싱을 활용할 수 있습니다.

게놈 분석 워크플로를 Azure로 포팅하려는 연구 기관은 Azure HPC Cache를 사용하여 쉽게 이동할 수 있습니다. 캐시는 POSIX 파일 액세스를 제공하므로, 클라이언트에서 기존 클라이언트 워크플로를 실행하기 위해 클라이언트 쪽을 변경할 필요가 없습니다.

Azure HPC Cache를 활용하여 보조 분석, 약학 시뮬레이션 또는 AI 기반 이미지 분석과 같은 작업의 효율성을 높일 수도 있습니다.

### <a name="financial-services-analytics"></a>금융 서비스 분석

Azure HPC Cache 배포는 정량적 분석 계산, 위험 분석 워크로드 및 몬테카를로 시뮬레이션 속도를 높여 금융 서비스 회사에 전략적 의사 결정을 내리기 위한 더 나은 인사이트를 제공할 수 있습니다.

## <a name="region-availability"></a>지역 가용성

지역별 [Azure 글로벌 인프라 제품](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) 페이지를 방문하여 Azure HPC Cache를 사용할 수 있는 위치를 알아봅니다.

Azure HPC Cache는 단일 지역에 상주합니다. 여기에 있는 Blob 컨테이너에 연결하면 다른 지역에 저장된 데이터에 액세스할 수 있습니다. 캐시는 고객 데이터를 영구적으로 저장하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure HPC Cache 제품 페이지](https://azure.microsoft.com/services/hpc-cache)에서 해당 기능 알아보기
* 제품 [필수 조건](hpc-cache-prerequisites.md) 알아보기
* Azure Portal에서 [Azure HPC Cache 만들기](hpc-cache-create.md)
