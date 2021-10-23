---
title: Azure Firewall Manager 아키텍처 옵션은 무엇인가요?
description: Azure Firewall Manager를 사용하여 허브 가상 네트워크 또는 보안 가상 허브 아키텍처 사용을 비교 및 대조합니다.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: a7aebbe0b454c1db49e001bb4caaab96c2903090
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251645"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure Firewall Manager 아키텍처 옵션은 무엇인가요?

Azure Firewall Manager는 다음 두 가지 네트워크 아키텍처 유형에 대한 보안 관리를 제공할 수 있습니다.

- **보안 가상 허브**

   [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources)는 허브 및 스포크 아키텍처를 쉽게 만들 수 있는 Microsoft 관리 리소스입니다. 보안 및 라우팅 정책이 이러한 허브와 연결된 경우에는 *[보안 가상 허브](secured-virtual-hub.md)* 라고도 합니다. 
- **허브 가상 네트워크**

   이는 사용자가 직접 만들고 관리하는 표준 Azure 가상 네트워크입니다. 이러한 허브와 연결된 보안 정책을 *보안 가상 허브* 라고도 합니다. 지금은 Azure Firewall Policy만 지원됩니다. 워크로드 서버 및 서비스가 포함된 스포크 가상 네트워크를 피어링할 수 있습니다. 또한 모든 스포크에 피어링되지 않는 독립 실행형 가상 네트워크에서 방화벽을 관리할 수 있습니다.

## <a name="comparison"></a>비교

다음 표는 이러한 두 가지 아키텍처 옵션을 비교하고 조직의 보안 요구 사항에 적합한 옵션을 결정하는 데 도움이 될 수 있습니다.


|  |**Hub 가상 네트워크**|**보안 가상 허브**  |
|---------|---------|---------|
|**기본 리소스**     |가상 네트워크|Virtual WAN 허브|
|**허브 및 스포크**     |가상 네트워크 피어링 사용|허브 가상 네트워크 연결을 사용하여 자동화|
|**온-프레미스 연결**     |VPN Gateway 최대 10Gbps 및 30개의 S2S 연결, ExpressRoute|보다 확장성 있는 VPN Gateway 최대 20Gbps 및 1000개의 S2S 연결, ExpressRoute|
|**SDWAN을 사용하는 자동 분기 연결**      |지원되지 않음|지원됨|
|**지역별 허브**     |지역별 여러 가상 네트워크|지역당 여러 Virtual Hub|
|**Azure Firewall - 여러 공용 IP 주소**      |고객이 제공|자동 생성|
|**Azure Firewall 가용성 영역**     |지원됨|지원됨|
|**타사 보안을 서비스 파트너로 사용하는 고급 인터넷 보안**     |선택한 파트너 서비스에 대해 고객이 설정 및 관리하는 VPN 연결|보안 파트너 공급자 흐름 및 파트너 관리 환경을 통해 자동화|
|**트래픽을 허브로 라우팅하는 중앙 집중형 경로 관리**     |고객이 관리하는 사용자 정의 경로|BGP를 사용하여 지원|
|**여러 보안 공급자 지원**|타사 방화벽에 대한 수동으로 구성된 강제 터널링을 사용하여 지원|두 보안 공급자, 즉 프라이빗 트래픽 필터링을 위한 Azure Firewall과 인터넷 필터링을 위한 타사에 대한 자동화 지원|
|**Application Gateway의 Web Application Firewall** |가상 네트워크에서 지원|현재 스포크 네트워크에서 지원됨|
|**네트워크 가상 어플라이언스**|가상 네트워크에서 지원|현재 스포크 네트워크에서 지원됨|
|**Azure DDoS Protection 표준 지원**|예|아니요|

## <a name="next-steps"></a>다음 단계

- [Azure Firewall Manager 배포 개요](deployment-overview.md) 검토
- [보안 가상 허브](secured-virtual-hub.md)에 대해 알아보기
