---
title: Enterprise 참조 아키텍처
description: 이 문서에서는 엔터프라이즈에서 Azure DevTest Labs에 대한 참조 아키텍처 지침을 제공합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 4714d143fd0f31bfd1c0570c37013fc15c2887cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283549"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>엔터프라이즈의 Azure DevTest Labs 참조 아키텍처
이 문서에서는 엔터프라이즈에서 Azure DevTest Labs 기반 솔루션을 배포하는 데 도움이 되는 참조 아키텍처를 제공합니다. 이 문서의 내용은 다음과 같습니다.
- Azure ExpressRoute를 통해 온-프레미스 연결
- 가상 머신에 원격으로 로그인하는 원격 데스크톱 게이트웨이
- 프라이빗 아티팩트의 아티팩트 리포지토리에 연결
- 랩에서 사용되는 기타 PaaS 서비스

![참조 아키텍처 다이어그램](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architecture
다음은 참조 아키텍처의 주요 요소입니다.

- **Azure AD(Azure Active Directory)** : DevTest Labs에서는 [ID 관리에 Azure AD 서비스](../active-directory/fundamentals/active-directory-whatis.md)를 사용합니다. DevTest Labs를 기반으로 환경에 대한 액세스 권한을 사용자에게 부여하는 경우 다음 두 가지 주요 측면을 고려하세요.
    - **리소스 관리**: 리소스를 관리하기 위해 Azure Portal에 대한 액세스 권한을 제공합니다(가상 머신 만들기, 환경 만들기, 아티팩트 시작, 중지, 다시 시작, 삭제 적용 등). 리소스 관리는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 수행됩니다. 사용자에게 역할을 할당하고 리소스 및 액세스 수준 권한을 설정합니다.
    - **가상 머신(네트워크 수준)** : 기본 구성에서 가상 머신은 로컬 관리자 계정을 사용합니다. 사용 가능한 도메인([Azure AD Domain Services](../active-directory-domain-services/overview.md), 온-프레미스 도메인 또는 클라우드 기반 도메인)이 있으면 머신을 도메인에 조인할 수 있습니다. 그러면 사용자가 자신의 도메인 기반 ID를 사용하여 VM에 연결할 수 있습니다.
- **온-프레미스 연결**: 이 아키텍처 다이어그램에서는 [ExpressRoute](../expressroute/expressroute-introduction.md)를 사용합니다. 하지만 [사이트 간 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)을 사용할 수도 있습니다. DevTest Labs에 ExpressRoute를 사용할 필요가 없긴 하지만 엔터프라이즈에서는 일반적으로 사용됩니다. ExpressRoute는 회사 리소스에 액세스해야 하는 경우에만 필요합니다. 일반적인 시나리오는 다음과 같습니다.
    - 클라우드로 이동할 수 없는 온-프레미스 데이터가 있습니다.
    - 랩의 가상 머신을 온-프레미스 도메인에 조인하는 것을 선호합니다.
    - 보안/준수를 위해 클라우드 환경에서 모든 네트워크 트래픽이 온-프레미스 방화벽을 통해 들어오고 나가도록 강제하려고 합니다.
- **네트워크 보안 그룹**: 원본 및 대상 IP 주소를 기반으로 클라우드 환경으로(또는 클라우드 환경 내로) 트래픽을 제한하는 일반적인 방법은 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 사용하는 것입니다. 예를 들어 회사 네트워크에서 발생하는 트래픽만 랩의 네트워크로 들어가도록 허용하려고 합니다.
- **원격 데스크톱 게이트웨이**: 기업은 일반적으로 회사 방화벽에서 나가는 원격 데스크톱 연결을 차단합니다. DevTest Labs에는 클라우드 기반 환경에 연결을 사용하도록 설정하는 몇 가지 옵션이 있습니다. 예를 들면 다음과 같습니다.
  - [원격 데스크톱 게이트웨이](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)를 사용하고 게이트웨이 부하 분산 장치의 고정 IP 주소를 허용합니다.
  - ExpressRoute/사이트 간 VPN 연결을 통해 [들어오는 모든 RDP 트래픽을 보냅니다](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). 이 기능은 기업이 DevTest Labs 배포를 계획할 때 일반적으로 고려하는 사항입니다.
- **네트워크 서비스(가상 네트워크, 서브넷)** : [Azure 네트워킹](../networking/fundamentals/networking-overview.md) 토폴로지는 DevTest Labs 아키텍처의 또 다른 주요 요소입니다. 랩의 리소스가 서로 통신하고 온-프레미스 및 인터넷에 액세스할 수 있는지 여부를 제어합니다. 이 아키텍처 다이어그램에는 고객이 DevTest Labs를 사용하는 가장 일반적인 방법, 즉 모든 랩이 [허브-스포크 모델](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)을 사용하여 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md) 을 통해 ExpressRoute/온-프레미스에 대한 사이트 간 VPN 연결에 연결하는 방법이 포함되어 있습니다. 그러나 DevTest Labs는 Azure Virtual Network를 직접 사용하므로 네트워킹 인프라를 설정하는 방법에 제한이 없습니다.
- **DevTest labs**: DevTest labs는 전반적인 아키텍처의 핵심 부분입니다. 서비스에 대해 자세히 알아보려면 [DevTest Labs 정보](devtest-lab-overview.md)를 참조하세요.
- **가상 머신 및 기타 리소스(SaaS, PaaS, IaaS)** : 가상 머신은 DevTest Labs에서 다른 Azure 리소스와 함께 지원하는 주요 워크로드입니다. DevTest Labs를 통해 기업은 (VM 및 기타 Azure 리소스를 비롯한) Azure 리소스에 쉽게 액세스할 수 있습니다. [개발자](devtest-lab-developer-lab.md)와 [테스터](devtest-lab-test-env.md)를 위한 Azure 액세스에 대해 자세히 알아보세요.

## <a name="scalability-considerations"></a>확장성 고려 사항
DevTest Labs에는 기본 제공 할당량 또는 한도가 없지만 랩의 일반 작업에 사용되는 다른 Azure 리소스에는 [구독 수준 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)이 있습니다. 따라서 일반적인 엔터프라이즈 배포에서는 DevTest Labs의 대량 배포를 처리하기 위해 여러 Azure 구독이 필요합니다. 기업에서 가장 일반적으로 사용하는 할당량은 다음과 같습니다.

- **리소스 그룹**: 기본 구성에서 DevTest Labs는 모든 새 가상 머신에 대해 리소스 그룹을 만들거나 사용자가 서비스를 사용하여 환경을 만듭니다. 구독은 [최대 980개의 리소스 그룹](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)을 포함할 수 있습니다. 따라서 이는 구독의 가상 머신과 환경에 대한 제한입니다. 고려해야 할 두 가지 다른 구성이 있습니다.
    - **[모든 가상 머신이 동일한 리소스 그룹으로 이동](resource-group-control.md)** : 이 설정으로 인해 리소스 그룹 제한에 도달하게 되더라도 리소스 그룹당 리소스 종류 제한에 영향을 미칩니다.
    - **공유 공용 IP 사용**: 크기와 지역이 같은 모든 VM이 동일한 리소스 그룹으로 이동합니다. 이 구성은 가상 머신에 공용 IP 주소를 사용하도록 허용된 경우 리소스 그룹 할당량과 리소스 그룹당 리소스 종류 할당량의 “중간”에 해당합니다.
- **리소스 그룹 및 리소스 종류당 리소스**: [리소스 그룹 및 리소스 종류당 리소스의 기본 제한은 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)입니다.  *모든 VM이 동일한 리소스 그룹으로 이동* 구성을 사용하는 경우 VM에 추가 디스크가 많으면 특히 이 구독 제한에 더 빨리 도달합니다.
- **스토리지 계정**: DevTest Labs의 랩에는 스토리지 계정이 함께 제공됩니다. [구독당 지역별 스토리지 계정 수에 대한 Azure 할당량은 250개](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)입니다. 동일한 지역 내 DevTest Labs의 최대 수는 250개입니다.
- **역할 할당**: 역할 할당은 사용자 또는 보안 주체에게 리소스에 대한 액세스 권한(소유자, 리소스, 권한 수준)을 부여하는 방법입니다. Azure에서 [구독당 역할 할당은 2,000개로 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits)됩니다. 기본적으로 DevTest Labs 서비스는 각 VM에 대한 리소스 그룹을 만듭니다. 소유자에게는 DevTest Labs VM에 대한 *소유자* 권한과 리소스 그룹에 대한 *읽기 권한자* 권한이 부여됩니다. 만든 새 VM은 각각 이러한 방식으로 랩에 대한 사용자 권한을 부여할 때 사용되는 할당 외에도 두 가지 역할 할당을 사용합니다.
- **API 읽기/쓰기**: REST APIs, PowerShell, Azure CLI, Azure SDK를 비롯한 다양한 방법으로 Azure와 DevTest Labs를 자동화할 수 있습니다. 자동화를 통해 API 요청에 대한 다른 제한에 도달할 수 있습니다. 각 구독은 최대 [시간당 12,000회 읽기 요청과 1,200회 쓰기 요청](../azure-resource-manager/management/request-limits-and-throttling.md)을 허용합니다. DevTest Labs를 자동화하는 경우 이 한도를 염두에 두어야 합니다.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항
DevTest Labs에는 단일 랩으로 작업하기 위한 뛰어난 관리자 인터페이스가 있습니다. 그러나 기업에서 여러분은 여러 Azure 구독과 많은 랩을 보유하고 있을 것입니다. 모든 랩을 일관되게 변경하려면 스크립팅/자동화가 필요합니다. DevTest Labs 배포에 대한 몇 가지 예제와 최선의 관리 사례는 다음과 같습니다.

- **랩 설정 변경**: 일반적인 시나리오는 배포의 모든 랩에서 특정 랩 설정을 업데이트하는 것입니다. 예를 들어 새 VM 인스턴스 크기를 사용할 수 있으며 이를 허용하도록 모든 랩을 업데이트해야 합니다. PowerShell 스크립트, CLI 또는 REST API를 사용하여 이러한 변경을 자동화하는 것이 가장 좋습니다.  
- **아티팩트 리포지토리 개인용 액세스 토큰**: 일반적으로 Git 리포지토리의 개인용 액세스 토큰은 90일, 1년 또는 2년 후에 만료됩니다. 연속성을 보장하려면 개인용 액세스 토큰을 확장하거나 새로 만들어야 합니다. 그런 다음 자동화를 사용하여 모든 랩에 새 개인용 액세스 토큰을 적용합니다.
- **랩 설정에 대한 변경 제한**: 종종 특정 설정(예: 마켓플레이스 이미지 사용 허용)을 제한해야 합니다. Azure Policy를 사용하여 리소스 종류에 대한 변경을 방지할 수 있습니다. 또는 사용자 지정 역할을 만들어 랩의 *소유자* 역할 대신 이 역할을 사용자에게 부여할 수 있습니다. 랩의 대부분 설정(내부 지원, 랩 알림, 허용된 VM 크기 등)에 대해 이처럼 할 수 있습니다.
- **이 명명 규칙을 따르도록 요구**: 일반적으로 관리자는 클라우드 기반 개발 환경과 테스트 환경에 속한 VM을 쉽게 식별하고자 합니다. [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)를 사용하여 이처럼 할 수 있습니다.

DevTest Labs는 동일한 방식으로 관리되는 기본 Azure 리소스 (네트워킹, 디스크, 컴퓨팅 등)를 사용합니다. 예를 들어 Azure Policy는 랩에서 만든 가상 머신에 적용됩니다. 클라우드 용 Microsoft Defender는 VM 준수에 대해 보고할 수 있습니다. Azure Backup 서비스는 랩의 VM에 대한 정기 백업을 제공할 수 있습니다.

## <a name="security-considerations"></a>보안 고려 사항
Azure DevTest Labs는 Azure에서 기존 리소스(컴퓨팅, 네트워킹 등)를 사용합니다. 따라서 플랫폼에 기본 제공되는 보안 기능을 자동으로 활용합니다. 예를 들어 들어오는 원격 데스크톱 연결이 회사 네트워크에서만 시작되도록 하려면 원격 데스크톱 게이트웨이의 가상 네트워크에 네트워크 보안 그룹을 추가하기만 하면 됩니다. 유일한 추가 보안 고려 사항은 일상적으로 랩을 사용하는 팀 구성원에게 부여하는 권한 수준입니다. 가장 일반적인 권한은 [*소유자* 와 *사용자*](devtest-lab-add-devtest-user.md)입니다. 역할에 대한 자세한 내용은 [Azure DevTest Labs에 소유자 및 사용자 추가](devtest-lab-add-devtest-user.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서인 [Azure DevTest Labs 인프라 스케일 업](devtest-lab-guidance-scale.md)을 참조하세요.
