---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67182156"
---
Azure에서 가용성 그룹 수신기를 구성하는 경우 두 가지 방법을 사용하는 것을 고려해야 합니다. 이러한 방법은 수신기를 만들 때 사용하는 Azure Load Balancer의 유형에 따라 달라집니다. 다음 표에 차이점이 나와 있습니다.

| 부하 분산 장치 유형 | 구현 | 사용하는 경우: |
| --- | --- | --- |
| **외부로** |VM(가상 머신)을 호스팅하는 클라우드 서비스의 *공용 가상 IP 주소*를 사용합니다. |인터넷을 포함하여 가상 네트워크의 외부에서 수신기에 액세스해야 합니다. |
| **내부** |수신기에 대한 프라이빗 주소가 있는 *내부 부하 분산 장치*를 사용합니다. |동일한 가상 네트워크 내에서만 수신기에 액세스할 수 있습니다. 이 액세스에는 하이브리드 시나리오의 사이트 간 VPN이 포함됩니다. |

> [!IMPORTANT]
> 클라우드 서비스의 공용 VIP(외부 부하 분산 장치)를 사용하는 수신기의 경우 클라이언트, 수신기, 데이터베이스가 동일한 Azure 지역에 있을 때는 송신 비용이 발생하지 않습니다. 그렇지 않은 경우 수신기를 통해 반환된 데이터는 송신으로 간주되며 정상적인 데이터 전송 요금으로 청구됩니다. 
> 
> 

ILB는 지역 범위의 가상 네트워크에서만 구성할 수 있습니다. 선호도 그룹에 대해 구성된 기존 가상 네트워크는 ILB를 사용할 수 없습니다. 자세한 내용은 [내부 부하 분산 장치 개요](../articles/load-balancer/load-balancer-internal-overview.md)를 참조하세요.

