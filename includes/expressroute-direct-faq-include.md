---
title: 포함 파일
description: 포함 파일
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 02def8321ce5df33fb89ca8d9f6c24167c15bbb6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536010"
---
### <a name="what-is-expressroute-direct"></a>ExpressRoute Direct란?

ExpressRoute Direct는 고객에게 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. ExpressRoute Direct는 대규모로 활성/활성 연결을 지원하는 이중 100 또는 10Gbps 연결을 제공합니다. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>고객이 ExpressRoute Direct에 연결하려면 어떻게 해야 하나요? 

고객은 ExpressRoute 라우터에 연결하여 ExpressRoute Direct를 활용하려면 로컬 이동 통신 사업자 및 공동 배치 공급자와 협력해야 합니다.

### <a name="what-locations-currently-support-expressroute-direct"></a>현재 ExpressRoute Direct를 지원하는 위치는 어디인가요? 

[위치 페이지](../articles/expressroute/expressroute-locations-providers.md)에서 가용성을 확인하세요. 

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct에 대한 SLA란 무엇인가요?

ExpressRoute Direct는 동일한 [엔터프라이즈급 ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)를 사용합니다. 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>고객이 ExpressRoute Direct 사용을 고려해야 하는 시나리오는 무엇인가요?  

ExpressRoute Direct는 Microsoft 글로벌 백본에 대한 직접 100 또는 10Gbps 포트 쌍을 고객에게 제공합니다. 고객에게 최상의 이점을 제공하는 시나리오에는 대량 데이터 수집, 규제 시장에 대한 물리적 격리 및 버스트 시나리오(예: 렌더링)에 대한 전용 용량이 포함됩니다. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct에 대한 청구 모델은 무엇인가요? 

ExpressRoute Direct는 고정 금액으로 포트 쌍에 대한 요금이 청구됩니다. 표준 회로는 추가 시간 없이 포함되며 프리미엄에는 약간의 추가 요금이 포함됩니다. 피어링 위치의 영역에 따라 회로별로 송신에 대한 요금이 청구됩니다.

### <a name="when-does-billing-start-and-stop-for-the-expressroute-direct-port-pairs"></a>ExpressRoute Direct 포트 쌍에 대한 요금 청구는 언제 시작되고 중지되나요?

ExpressRoute Direct 포트 쌍은 연결 중 하나 또는 둘 다를 사용할 수 있는 날짜나 ExpressRoute Direct 리소스가 생성된 날짜부터 45일 후 중 더 이른 날짜에 청구됩니다. 45일 유예 기간은 고객이 공동 장소 공급자와 연결 간 프로세스를 완료할 수 있도록 하기 위해 제공됩니다.

직접 포트를 삭제하고 교차 연결을 제거하면 ExpressRoute Direct 포트 쌍에 대한 요금이 청구되지 않습니다. 
