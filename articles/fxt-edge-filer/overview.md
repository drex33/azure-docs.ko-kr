---
title: Microsoft Azure FXT Edge Filer 개요
description: 고성능 컴퓨팅을 위한 Azure FXT Edge Filer 하이브리드 스토리지 캐시, 활성 보관 및 파일 액세스 가속기 솔루션을 설명합니다.
author: femila
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: femila
ms.openlocfilehash: ad0a530f5453060ede34059e3265b9e784e9810f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085946"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Azure FXT Edge Filer 하이브리드 스토리지 캐시란?

Azure FXT Edge Filer는 HPC(고성능 컴퓨팅) 작업을 위한 빠른 파일 액세스 및 활성 보관을 제공하는 하이브리드 스토리지 캐싱 어플라이언스입니다.

로컬 데이터 센터에, 원격으로 또는 클라우드에 저장하는지 여부와 관계없이 여러 데이터 원본에서 작동합니다. Azure FXT Edge Filer는 다양한 스토리지 시스템에서 데이터에 대한 통합 네임스페이스를 제공할 수 있습니다.

세 개 이상의 FXT Edge Filer 하드웨어 디바이스가 클러스터 파일 시스템으로 함께 작동하여 캐시를 제공합니다. 필요한 하드웨어 구매에 대한 자세한 내용은 Microsoft 담당자에게 문의하세요.

자세한 내용은 [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/)의 제품 정보 및 데이터 시트를 참조하세요.

## <a name="use-cases"></a>사용 사례

Azure FXT Edge Filer는 다음과 같은 워크플로에서 생산성이 가장 높습니다.

* 읽기 작업이 많은 파일 액세스 워크플로
* NFSv3 또는 SMB2 프로토콜
* CPU 코어가 1,000-100,000개인 컴퓨팅 팜

### <a name="nas-optimization-and-scaling"></a>NAS 최적화 및 확장

Azure FXT Edge Filer 캐시를 사용하면 기존 NetApp 및 Dell EMC Isilon NAS 시스템에 원활하게 액세스할 수 있습니다. 또한 Azure Blob 또는 기타 클라우드 스토리지를 추가하여 클라이언트 쪽에서 데이터 액세스 프로세스를 다시 작업하지 않아도 확장성을 제공할 수 있습니다.

### <a name="wan-caching"></a>WAN 캐싱

Azure FXT Edge Filer는 필요한 데이터를 다른 곳에 저장할 때 고급 사용자의 빠른 파일 액세스를 지원하는 데 사용될 수 있습니다. 중앙 집중식 데이터 센터에서 백업 및 기타 데이터 관리 시스템을 유지하면서 액세스를 제공합니다.

### <a name="active-archive-in-azure-blob"></a>Azure Blob의 활성 보관

Azure FXT Edge Filer를 액세스 지점으로 사용하여 데이터 센터를 클라우드 스토리지로 확장합니다.

## <a name="features"></a>기능

두 개의 하드웨어 모델을 사용할 수 있습니다.

| 모델 | DRAM | NVMe SSD | 네트워크 포트 |
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25.6TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12.8TB | 6 x 25Gb/10Gb + 2 x 1Gb |

## <a name="next-steps"></a>다음 단계

* [사양](specs.md) 또는 [설치 자습서](install.md)를 참조하여 Azure FXT Edge Filer에 대해 계속 알아봅니다.
* [Azure FXT Edge Filer 제품 페이지](https://azure.microsoft.com/services/fxt-edge-filer/)에서 Azure FXT Edge Filer를 구매하는 방법을 알아봅니다.
