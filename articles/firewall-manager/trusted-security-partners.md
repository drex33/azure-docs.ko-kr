---
title: Azure Firewall Manager 보안 파트너 공급자란?
description: Azure Firewall Manager 보안 파트너 공급자에 관해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: victorh
ms.openlocfilehash: 2e3dbc292108507793de0dd095e30ea2f63a7dab
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535716"
---
# <a name="what-are-security-partner-providers"></a>보안 파트너 공급자란?

Azure Firewall Manager의 *보안 파트너 공급자* 를 통해 익숙한 동급 최고의 타사 SECaaS(Security as a Service) 제품을 사용하여 사용자를 위해 인터넷 액세스를 보호할 수 있습니다.

빠른 구성을 사용하면 지원되는 보안 파트너를 통해 허브를 보호하고 VNet(가상 네트워크)나 지역 내의 분기 위치에서 인터넷 트래픽을 라우팅하고 필터링할 수 있습니다. UDR(사용자 정의 경로)을 설정하고 관리하지 않고도 자동화된 경로 관리를 통해 이를 수행할 수 있습니다.

선택한 보안 파트너로 구성된 보안 허브를 여러 Azure 지역에 배포하여 전 세계 해당 지역의 모든 사용자를 위한 연결과 보안을 얻을 수 있습니다. 인터넷/SaaS 애플리케이션 트래픽에 보안 파트너의 제품을 사용하고 보안 허브의 프라이빗 트래픽에 Azure Firewall을 사용하는 기능을 통해 이제 전 세계에 분산된 사용자와 애플리케이션에 가까운 Azure에서 보안 에지 빌드를 시작할 수 있습니다.

지원되는 보안 파트너는 **Zscaler**, **[Check Point](check-point-overview.md)** , **iboss** 입니다.

![보안 파트너 공급자](media/trusted-security-partners/trusted-security-partners.png)

Zscaler 개요는 Jack Tracey의 다음 비디오를 참조하세요.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWI4n8]

## <a name="key-scenarios"></a>주요 시나리오

보안 파트너를 사용하여 다음과 같은 시나리오에서 인터넷 트래픽을 필터링할 수 있습니다.

- VNet(가상 네트워크)-인터넷

   Azure에서 실행되는 클라우드 워크로드에 고급 사용자 인식 인터넷 보호를 사용합니다.

- 분기-인터넷

   Azure 연결 및 글로벌 배포를 사용하여 분기와 인터넷 간 시나리오에 대해 타사 NSaaS 필터링을 쉽게 추가할 수 있습니다. Azure Virtual WAN을 사용하여 글로벌 전송 네트워크와 보안 에지를 빌드할 수 있습니다.

다음과 같은 시나리오가 지원됩니다.
- 허브의 두 보안 공급자

   보안 파트너 공급자를 통한 VNet/분기-인터넷과 Azure Firewall을 통한 기타 트래픽(스포크-스포크, 스포크-분기, 분기-스포크).
- 허브의 단일 공급자

   - Azure Firewall을 통해 보안이 유지되는 모든 트래픽(스포크-스포크, 스포크-분기, VNet/분기-인터넷)<br>
      또는
   - 보안 파트너 공급자를 통한 VNet/분기-인터넷

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>보안 가상 허브에서 인터넷 트래픽 필터링 모범 사례

인터넷 트래픽은 일반적으로 웹 트래픽을 포함합니다. 그러나 여기에는 Microsoft 365와 같은 SaaS 애플리케이션과 Azure Storage, Azure Sql 등과 같은 Azure 퍼블릭 PaaS 서비스로 향하는 트래픽도 포함됩니다. 다음은 해당 서비스의 트래픽을 처리하기 위한 모범 사례 권장 방법입니다.

### <a name="handling-azure-paas-traffic"></a>Azure PaaS 트래픽 처리
 
- 트래픽이 대부분 Azure PaaS로 구성되고 애플리케이션에 대한 리소스 액세스가 IP 주소, FQDN, 서비스 태그 또는 FQDN 태그를 사용하여 필터링될 수 있으면 보호를 위해 Azure Firewall을 사용합니다.

- 트래픽이 SaaS 애플리케이션 액세스로 구성되거나 사용자 인식 필터링(예: VDI(가상 데스크톱 인프라) 워크로드용)이 필요하거나 고급 인터넷 필터링 기능이 필요한 경우 허브에서 타사 파트너 솔루션을 사용합니다.

![Azure Firewall Manager의 모든 시나리오](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Microsoft 365 트래픽 처리

전 세계적으로 분산된 분기 위치 시나리오에서는 나머지 인터넷 트래픽을 Azure 보안 허브로 보내기 전에 Microsoft 365 트래픽을 분기에서 직접 리디렉션해야 합니다.

Microsoft 365에서 성공적인 사용자 환경을 얻으려면 네트워크 대기 시간과 성능이 매우 중요합니다. 최적의 성능과 사용자 환경에 관한 해당 목표를 달성하려면 고객은 Azure를 통해 나머지 인터넷 트래픽을 라우팅하기 전에 Microsoft 365 직접 및 로컬 이스케이프를 구현해야 합니다.

[Microsoft 365 네트워크 연결 원칙](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles)에서는 주요 Microsoft 365 네트워크 연결이 사용자 분기나 모바일 디바이스에서 로컬로 라우팅되고 인터넷을 통해 가장 가까운 Microsoft 네트워크 로그인 지점으로 직접 라우팅되어야 합니다.

또한 Microsoft 365 연결은 개인 정보 보호를 위해 암호화되며 성능상의 이유로 유효한 독점 프로토콜을 사용합니다. 따라서 해당 연결을 기존 네트워크 수준 보안 솔루션에 적용하기가 어렵습니다. 이러한 이유로 고객은 Azure를 통해 나머지 트래픽을 보내기 전에 분기에서 직접 Microsoft 365 트래픽을 보내는 것이 좋습니다. Microsoft는 Azure 및 Microsoft 365와 통합하고 고객이 Microsoft 365 직접 및 로컬 인터넷 분류를 쉽게 사용할 수 있도록 지원하는 여러 SD-WAN 솔루션 공급자와 파트너 관계를 맺었습니다. 자세한 내용은 [Azure Virtual WAN이란?](../virtual-wan/virtual-wan-about.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Firewall Manager를 사용하여 보안 허브에 보안 파트너 제품을 배포](deploy-trusted-security-partner.md)합니다.
