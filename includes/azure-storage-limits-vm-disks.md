---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/27/2021
ms.author: rogarana
ms.openlocfilehash: aedfaf0606547653e88ad6e120f51a542dabdb95
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155170"
---
Azure VM (가상 머신)에 많은 데이터 디스크를 연결할 수 있습니다. VM의 데이터 디스크 확장성 및 성능 목표에 따라 성능 및 용량 요구 사항을 충족하는 데 필요한 디스크의 수와 유형을 결정할 수 있습니다.

> [!IMPORTANT]
> 최적의 성능을 얻기 위해 가상 머신에 연결되어 자주 활용되는 디스크의 수를 제한하여 가능한 제한을 방지합니다. 연결된 모든 디스크의 동시 사용률이 높지 않으면 가상 머신이 더 많은 수의 디스크를 지원할 수 있습니다.

**Azure 관리 디스크:**

다음 표에서는 구독당 지역별 리소스 수의 기본 및 최대 제한을 보여 줍니다. 이러한 제한은 플랫폼 관리형 키 또는 고객 관리형 키로 암호화된 디스크에 관계없이 동일하게 유지됩니다. 리소스 그룹당 Managed Disks, 스냅샷 및 이미지 수에는 제한이 없습니다.  

> | 리소스 | 제한 |
> | --- | --- |
> | 표준 관리 디스크 | 50,000 |
> | 표준 SSD 관리 디스크 | 50,000 |
> | 프리미엄 관리 디스크 | 50,000 |
> | Standard_LRS 스냅숏<sup>1</sup> | 75,000 |
> | Standard_ZRS 스냅숏<sup>1</sup> | 75,000 |
> | 관리형 이미지 | 50,000 |

<sup>1</sup> 개별 디스크가 가질 수 있는 전체 디스크 스냅숏의 총 수는 200입니다. 개별 디스크에는 전체 디스크 스냅숏과 별도로 계산 되는 200 증분 스냅숏이 있을 수 있습니다. 

**Standard storage 계정:** Standard storage 계정의 최대 총 요청 율은 2만 IOPS입니다. 표준 스토리지 계정에서 모든 가상 머신 디스크의 총 IOPS는 이 제한을 초과할 수 없습니다.
  
요청 속도 제한에 따라 단일 표준 스토리지 계정에서 지원하는 활용된 디스크의 개수를 대략 계산할 수 있습니다. 예를 들어 기본 계층 VM의 경우 자주 활용되는 디스크의 최대 수는 약 66개(디스크당 20,000/300 IOPS)입니다. 표준 계층 VM에 대해 자주 활용되는 디스크의 최대 수는 약 40개(디스크당 20,000/500 IOPS)입니다. 

**Premium storage 계정:** Premium storage 계정의 최대 총 처리량 속도는 50 Gbps입니다. 모든 VM 디스크의 총 처리량은 이 제한을 초과할 수 없습니다.

