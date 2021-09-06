---
title: Azure NetApp Files에 대한 Azure 가상 머신 SKU 모범 사례 | Microsoft Docs
description: SKU 내부 및 SKU 사이의 차이점을 포함하여 Azure 가상 머신 SKU에 대한 Azure NetApp Files 모범 사례를 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6644a52ee094ce0076293b37fa79db28568c9c67
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233481"
---
# <a name="azure-virtual-machine-skus-best-practices-for-azure-netapp-files"></a>Azure NetApp Files에 대한 Azure 가상 머신 SKU 모범 사례

이 문서에서는 SKU 내부 및 SKU 사이의 차이점을 포함하여 Azure 가상 머신 SKU에 대한 Azure NetApp Files 모범 사례를 설명합니다.   

## <a name="sku-selection-considerations"></a>SKU 선택 시 고려 사항

스토리지 성능에는 스토리지 속도 자체 그 이상의 것들이 관여됩니다. 프로세서 속도와 아키텍처는 특정 컴퓨팅 노드의 전반적인 환경에 많은 영향을 줍니다. 지정된 SKU를 선택하는 동안 다음 요인을 고려해야 합니다.

* AMD 또는 Intel: 예를 들어 SAS는 특히 Intel 프로세서용으로 설계된 수학 커널 라이브러리를 사용합니다.  여기에서는 AMD SKU보다 Intel SKU가 선호됩니다.
* F2, E_v3, D_v3 머신 유형은 각각 2개 이상의 칩셋을 기반으로 합니다.  Azure Dedicated Hosts를 사용할 때는 특정 모델(Broadwell이나 Cascade Lake 또는 E 유형을 선택할 경우에는 Skylake)을 선택해야 합니다. 그렇지 않은 경우에는 칩셋 선택이 비결정적입니다.  HPC 클러스터를 배포하는 중이고 인벤토리 간에 일관적인 경험이 중요하다면 단일 Azure Dedicated Hosts를 고려할 수 있으며, E_v4 또는 D_v4와 같은 단일 칩셋 SKU를 선택할 수도 있습니다.
* Intel Broadwell 기반 SKU 및 AMD EPYC™ 7551 기반 SKU 테스트 모두에서 NAS(네트워크 연결 스토리지)의 성능 편차가 관찰되었습니다. 관찰된 문제는 다음 두 가지입니다.
    * 가속화된 네트워크 인터페이스가 최적이 아닌 NUMA 노드에 부적절하게 매핑된 경우 읽기 성능이 크게 감소합니다.   가속화된 네트워킹 인터페이스를 특정 NUMA 노드에 매핑하는 것이 신규 SKU에 도움이 되더라도 이러한 칩셋의 SKU에서는 요구 사항으로 간주되어야 합니다(Lv2|E_v3|D_v3).
    * Lv2에서 실행되는 가상 머신 또는 Broadwell 칩셋으로 실행되는 E_v3 또는 D_v3는 다른 SKU에서 실행될 때와 비교해서 리소스 경합에 더 취약합니다.  단일 Azure Dedicated Host 내에서 실행되는 여러 가상 머신을 사용하여 테스트할 때 하나의 가상 머신에서 네트워크 기반 스토리지 워크로드를 실행하면 보조 가상 머신에서 실행되는 네트워크 기반 스토리지 워크로드 성능이 감소하는 것으로 확인되었습니다. 이러한 감소는 노드의 가상 머신에 가속화된 네트워크 인터페이스/NUMA 노드가 최적의 방식으로 매핑되지 않은 경우에 더 커집니다.  E_v3 및 D_V3는 이들 사이에서 Haswell, Broadwell, Cascade Lake 또는 Skylake에 사용될 수 있습니다. 

가상 머신을 선택할 때 가장 일관적인 성능을 얻기 위해서는 단일 칩셋 유형의 SKU 중에서 선택합니다. 가능하다면 오래된 모델보다 새로운 SKU가 선호됩니다.  전용 호스트를 사용하는 경우를 제외하고, E_v3 또는 D_v3 가상 머신이 사용되는 하드웨어 유형을 올바르게 예측하는 것은 가능성이 낮습니다.  E_v3 또는 D_v3 SKU를 사용하는 경우:

* 가상 머신이 해제되고, 할당 취소된 후 다시 설정된 경우에는 가상 머신이 호스트를 변경하고 따라서 하드웨어 모델도 변경될 가능성이 높습니다.
* 여러 가상 머신 간에 애플리케이션이 배포된 경우 가상 머신이 서로 다른 하드웨어에서 실행된다고 예상할 수 있습니다.

## <a name="differences-within-and-between-skus"></a>SKU 내부와 SKU 사이의 차이점
 
다음 표에서는 SKU 내부 및 SKU 사이의 차이점을 보여줍니다.  예를 들어 기본 E_v3 및 D_v3의 칩셋은 Broadwell, Cascade Lake, Skylake 간에 다르며, D_v3의 경우에도 다릅니다.  

|     패밀리    |     버전    |   설명     |     주파수(GHz)    |
|-|-|-|-|
|     E    |     V3    |     Intel® Xeon® E5-2673 v4(Broadwell)    |     2.3(3.6)    |
|     E    |     V3    |     Intel® Xeon® Platinum 8272CL(Cascade Lake)    |     2.6(3.7)    |
|     E    |     V3    |     Intel® Xeon® Platinum 8171M(Skylake)    |     2.1(3.8)    |
|     E    |     V4    |     Intel® Xeon® Platinum 8272CL(Cascade Lake)    |     2.6(3.7)    |
|     Ea    |     V4    |     AMD EPYC™ 7452    |     2.35(3.35)    |
|     D    |     V3    |     Intel® Xeon® E5-2673 v4(Broadwell)    |     2.3(3.6)    |
|     D    |     V3    |     Intel® Xeon® E5-2673 v3(Haswell)    |     2.3(2.3)    |
|     D    |     V3    |     Intel® Xeon® Platinum 8272CL(Cascade Lake)    |     2.6(3.7)    |
|     D    |     V3    |     Intel® Xeon® Platinum 8171M(Skylake)    |     2.1(3.8)    |
|     D    |     V4    |     Intel® Xeon® Platinum 8272CL(Cascade Lake)    |     2.6(3.7)    |
|     Da    |     V4    |     AMD EPYC™ 7452    |     2.35(3.35)    |
|     L    |     V2    |     AMD EPYC™ 7551    |     2.0(3.2)    |
|     F    |     1    |     Intel Xeon® E5-2673 v3(Haswell)     |     2.3(2.3)    |
|     F    |     2    |     Intel® Xeon® Platinum 8168M(Cascade Lake)    |     2.7(3.7)    |
|     F    |     2    |     Gen 2 Intel® Xeon® Platinum 8272CL(Skylake)    |     2.1(3.8)   |

프로덕션용으로 다중 노드 SAS GRID 환경을 준비할 때는 기본 하드웨어 이외의 다른 차이점 없이 분석 실행 간에 1시간 15분의 편차가 반복되는 것을 확인할 수 있습니다.  

|     SKU 및 하드웨어 플랫폼    |     작업 실행 시간    |
|-|-|
|     E32-8_v3(Broadwell)    |     5.5시간    |
|     E32-8_v3(Cascade Lake)    |     4.25시간    |

두 테스트 집합 모두에서 E32-8_v3 SKU가 선택되었고 RHEL 8.3이 `nconnect=8` 마운트 옵션과 함께 사용되었습니다.

## <a name="best-practices"></a>모범 사례 

* 가능하면 E_v3 또는 D_v3 SKU 대신 E_v4, D_v4 이상을 선택하세요.  
* 가능하면 L2 SKU 대신 Ed_v4, Dd_v4 이상을 선택하세요.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 Linux 직접 I/O 모범 사례](performance-linux-direct-io.md)
* [Azure NetApp Files에 대한 Linux 파일 시스템 캐시 모범 사례](performance-linux-filesystem-cache.md)
* [Azure NetApp Files에 대한 Linux NFS 탑재 옵션 모범 사례](performance-linux-mount-options.md)
* [Linux 동시성 모범 사례](performance-linux-concurrency-session-slots.md)
* [Linux NFS 미리 읽기 모범 사례](performance-linux-nfs-read-ahead.md)
* [Linux용 성능 벤치마크](performance-benchmarks-linux.md) 
