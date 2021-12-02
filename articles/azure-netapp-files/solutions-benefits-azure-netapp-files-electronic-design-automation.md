---
title: 전자 디자인 자동화에 대한 Azure NetApp Files 사용의 이점 | Microsoft Docs
description: 반도체 및 칩 디자인 업계의 요구 사항을 충족하기 위해 Azure NetApp Files에서 제공하는 솔루션에 대해 설명합니다. Azure NetApp Files를 사용하여 EDA(전자 디자인 자동화)용 표준 업계 벤치마크를 실행하는 테스트 시나리오를 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-hchen
ms.openlocfilehash: 49c736c4459ad395d2d1254c80b86c1095548dbd
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484793"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>전자 디자인 자동화에 Azure NetApp Files 사용의 이점

TTM(출시 시간)은 반도체 및 칩 설계 업계에서 중요한 고려 사항입니다. 업계는 대역폭이 높고 대기 시간이 낮은 스토리지를 요구합니다. 이 문서에서는 업계의 요구 사항을 충족하기 위해 Azure NetApp Files에서 제공하는 솔루션에 대해 설명합니다. Azure NetApp Files를 사용하여 EDA(전자 디자인 자동화)를 위한 표준 업계 벤치마크를 실행하는 테스트 시나리오를 제공합니다. 

## <a name="test-scenario-configurations"></a>시나리오 구성 테스트

테스트에는 다음 구성의 세 가지 시나리오가 포함됩니다. 

|    시나리오    |    볼륨    |    클라이언트<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    하나         |    1          |    1                           |
|    2         |    6          |    24                          |
|    3       |    12         |    24                          |

첫 번째 시나리오는 단일 볼륨이 얼마나 멀리 구동될 수 있는지를 다룹니다.  

두 번째 및 세 번째 시나리오는 단일 Azure NetApp Files 엔드포인트의 제한 사항을 평가합니다. I/O 상한 및 대기 시간의 잠재적인 이점을 조사합니다.

## <a name="test-scenario-results"></a>테스트 시나리오 결과

다음 테이블에 테스트 시나리오 결과가 요약되어 있습니다.

|    시나리오       |    I/O 속도<br>  2밀리초     |    I/O 속도<br>  에지에서     |    처리량<br>  2밀리초     |    처리량<br>  에지에서     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    볼륨 1개       |    39,601                 |    49,502                      |    692MiB/s                 |    866MiB/s                      |
|    볼륨 6개      |    255,613                |    317,000                     |    4,577MiB/s               |    5,568MiB/s                    |
|    볼륨 12개     |    256,612                |    319,196                     |    4,577MiB/s               |    5,709MiB/s                    |

단일 볼륨 시나리오는 기본 애플리케이션 구성을 나타냅니다. 이는 후속 테스트 시나리오에 대한 기준 시나리오입니다.  

6개 볼륨 시나리오는 단일 볼륨 워크로드 대비 선형 증가(600%)를 나타냅니다.  단일 가상 네트워크 내의 모든 볼륨은 단일 IP 주소를 통해 액세스됩니다.  

12개 볼륨 시나리오에서는 6개 볼륨 시나리오와 비교하여 일반적인 대기 시간 감소가 나타납니다. 그러나 그만큼 달성 가능한 처리량이 증가하는 것은 아닙니다.   

다음 그래프는 Azure NetApp Files의 EDA 워크로드에 대한 대기 시간 및 작업 속도를 보여 줍니다.  

![Azure NetApp Files의 EDA 워크로드에 대한 대기 시간 및 작업 속도](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

다음 그래프는 Azure NetApp Files의 EDA 워크로드에 대한 대기 시간 및 처리량을 보여 줍니다.  

![Azure NetApp Files의 EDA 워크로드에 대한 대기 시간 및 처리량](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>테스트 시나리오의 레이아웃 

아래 테이블에는 테스트 시나리오의 레이아웃이 요약되어 있습니다.

|    테스트 시나리오     |    총 디렉터리 수     |    총 파일 수     |
|----------------------|------------------------------------|------------------------------|
|    볼륨 1개          |    88,000                          |    880,000                   |
|    볼륨 6개         |    568,000                         |    5,680,000                 |
|    볼륨 12개        |    568,000                         |    5,680,000                 |

전체 워크로드는 동시에 실행되는 기능 및 물리적 단계를 혼합한 것입니다. 이는 하나의 EDA 도구 세트에서 다른 것으로의 일반적인 흐름을 나타냅니다.   

기능 단계는 초기 사양과 논리적 디자인으로 구성됩니다. 물리적 단계는 논리적 디자인이 물리적 칩으로 변환될 때 발생합니다. 승인 단계와 양산 직전 단계 중에 최종 검사가 완료되고 디자인이 제조를 위해 파운드리로 전달됩니다.  

기능 단계에는 순차 및 임의 읽기 및 쓰기 I/O가 혼합되어 있습니다. 기능 단계는 파일 상태 및 액세스 호출과 같이 메타데이터 집약적입니다. 메타데이터 작업은 사실상 크기가 없지만 읽기 및 쓰기 작업의 범위는 1K 미만에서 16K 사이입니다. 대부분의 읽기는 4K에서 16K 사이이며, 쓰기는 4K 이하입니다. 물리적 단계는 32K 및 64K OP 크기가 혼합된 순차적 읽기 및 쓰기 작업으로 구성됩니다.  

위의 그래프에서 대부분의 처리량은 워크로드의 순차적 물리적 단계에서 제공됩니다. I/O는 작은 무작위 및 메타데이터 집약적 기능 단계에서 제공됩니다. 두 단계가 병렬로 수행됩니다. 

결론적으로 Azure 컴퓨팅을 Azure NetApp Files와 쌍으로 연결하여 EDA 디자인이 스케일 가능한 광대역을 가지도록 할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)
