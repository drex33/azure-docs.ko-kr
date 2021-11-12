---
title: 관리 및 모니터링 보안 기능 - Microsoft Azure | Microsoft Docs
description: 이 문서에서는 Azure Cloud Services 및 Virtual Machines의 관리 및 모니터링을 지원하기 위해 Azure에서 제공하는 보안 기능 및 서비스에 대한 개요를 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: 81306e06d4ac6d0168215cebe7d45b4fb4c051de
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305791"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure 보안 관리 및 모니터링 개요
이 문서에서는 Azure Cloud Services 및 Virtual Machines의 관리 및 모니터링을 지원하기 위해 Azure에서 제공하는 보안 기능 및 서비스에 대한 개요를 제공합니다.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

Azure RBAC(역할 기반 액세스 제어)는 Azure 리소스에 대한 세부 액세스 관리 기능을 제공합니다. Azure RBAC를 통해 사용자에게 해당 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 또한 Azure RBAC를 사용하여 직원이 퇴사한 후 클라우드 리소스에 액세스하지 못하게 할 수 있습니다.

자세한 정보:

* [Azure RBAC의 Active Directory 팀 블로그](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>맬웨어 방지

Azure를 통해 Microsoft, Symantec, Trend Micro, McAfee, Kaspersky 등 주요 보안 공급업체의 맬웨어 방지 소프트웨어를 사용할 수 있습니다. 이 소프트웨어는 악성 파일, 애드웨어 및 기타 위협으로부터 가상 머신을 보호하도록 도와줍니다.

Azure Cloud Services 및 Virtual Machines용 Microsoft 맬웨어 방지는 PaaS 역할 및 가상 머신 모두에 대한 맬웨어 방지 에이전트를 설치할 수 있는 기능을 제공합니다. System Center Endpoint Protection에 기반한 이 기능은 클라우드에 입증된 온-프레미스 보안 기술을 구현합니다.

또한, Azure 플랫폼에서 Trend의 [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) 및 [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) 제품에 대한 심층적인 통합도 제공합니다. DeepSecurity는 바이러스 백신 솔루션이며 SecureCloud는 암호화 솔루션입니다. DeepSecurity는 확장 모델을 통해 VM 내부에 배포됩니다. Azure Portal UI 및 PowerShell을 사용하면 복제하려는 새 VM 내부의 DeepSecurity를 사용하거나 이미 배포된 기존 VM을 사용할지 선택할 수 있습니다.

SEP(Symantec Endpoint Protection)도 Azure에서 지원됩니다. 포털 통합을 통해 VM에서 SEP를 사용할지 지정할 수 있습니다. SEP는 Azure Portal을 통해 새로운 VM에 설치하거나 PowerShell을 통해 기존 VM에 설치할 수 있습니다.

자세한 정보:

* [Azure Virtual Machines에 맬웨어 방지 솔루션 배포](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Azure Cloud Services 및 Virtual Machines용 Microsoft 맬웨어 방지 프로그램](antimalware.md)
* [Windows VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법](/previous-versions/azure/virtual-machines/extensions/trend)
* [Windows VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법](../../virtual-machines/extensions/symantec.md)
* [Azure Virtual Machines를 보호하는 새로운 맬웨어 방지 옵션](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD 다단계 인증은 두 가지 이상의 확인 방법을 사용하도록 요구하는 인증 방법입니다. 사용자 로그인 및 트랜잭션에 중요한 두 번째 보안 계층을 추가합니다.

간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Multi-Factor Authentication을 사용하면 데이터와 애플리케이션에 대한 액세스를 보호합니다. 전화 통화, 문자 메시지 또는 모바일 앱 알림 또는 확인 코드 및 타사 OATH 토큰과 같은 다양한 확인 옵션을 통해 강력한 인증을 전달합니다.

자세한 정보:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure AD Multi-Factor Authentication이란?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Azure AD Multi-Factor Authentication 작동 방법](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Azure ExpressRoute를 사용하여 연결 공급자에서 쉽게 처리된 전용 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Azure, Microsoft 365, CRM Online 등의 Microsoft 클라우드 서비스에 연결할 수 있습니다. 연결을 설정할 수 있습니다.

* 모든 대상 간(IP VPN) 네트워크
* 지점 간 이더넷 네트워크
* 공동 배치 시설에서 연결 공급자를 통한 가상 교차 연결

ExpressRoute 연결은 공용 인터넷을 사용하지 않습니다. Express 경로는 일반적인 인터넷을 통한 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

자세한 정보:

* [ExpressRoute 기술 개요](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>가상 네트워크 게이트웨이

Azure Virtual Network 게이트웨이라는 VPN Gateway는 가상 네트워크와 온-프레미스 위치 간에 네트워크 트래픽을 보내는 데 사용됩니다. 또한 Azure 내의 여러 가상 네트워크 간(네트워크 간)에 트래픽을 보내는 데에도 사용됩니다. VPN 게이트웨이는 Azure와 인프라 사이의 안전한 프레미스 간 연결을 제공합니다.

자세한 정보:

* [VPN 게이트웨이 정보](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure 네트워크 보안 개요](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

경우에 따라 사용자는 Azure 리소스 또는 기타 SaaS 애플리케이션에서 권한 있는 작업을 수행해야 합니다. 이는 보통 조직이 사용자에게 Azure AD(Azure Active Directory)에서 영구 권한 있는 액세스를 제공함을 의미합니다.

조직은 사용자가 권한 있는 액세스 권한으로 수행하는 작업을 충분히 모니터링할 수 없으므로 클라우드에 호스트된 리소스의 보안 위험이 증가합니다. 또한 권한 있는 액세스가 있는 사용자 계정이 손상되면 이로 인해 조직의 전반적인 클라우드 보안에 영향을 줄 수 있습니다. Azure AD Privileged Identity Management는 권한의 노출 시간을 낮추고 사용에 대한 가시성을 높여 이러한 위험을 해결하는 데 도움이 됩니다.  

Privileged Identity Management는 역할에 대해 임시 관리자 개념 또는 “Just-In-Time” 관리자 액세스 개념을 도입합니다. 이러한 종류의 관리자는 할당된 역할에 대한 활성화 프로세스를 완료해야 합니다. 활성화 프로세스는 Azure AD에서 할당된 사용자 역할을 지정된 기간 동안 비활성에서 활성 상태로 변경합니다.

자세한 정보:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD Privileged Identity Management 시작](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>ID 보호

Azure AD Identity Protection은 의심스러운 로그인 활동 및 잠재적 취약성에 대한 통합 뷰를 제공하여 비즈니스를 보호하는 데 도움이 됩니다. Identity Protection은 다음과 같은 신호에 따라 사용자 및 권한 있는(관리자) ID에 대한 의심스러운 활동을 감지합니다.

* 무차별 암호 대입 공격
* 유출된 자격 증명
* 알 수 없는 위치 및 감염된 디바이스에서 로그인

알림 및 권장된 수정을 제공하여 실시간으로 위험을 완화하는 데 도움이 됩니다. 사용자 위험 심각도를 계산하므로 이후 위협으로부터 애플리케이션 액세스를 자동으로 보호하도록 위험 기반 정책을 구성할 수 있습니다.

자세한 정보:

* [Azure Active Directory ID 보호](../../active-directory/identity-protection/overview-identity-protection.md)
* [Channel 9: Azure AD 및 ID 표시: ID 보호 미리 보기](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="defender-for-cloud"></a>클라우드 용 Defender

클라우드 용 Microsoft Defender는 위협을 예방, 감지 및 대응 하는 데 도움이 됩니다. 클라우드 용 Defender는 하이브리드 클라우드 환경에 있는 Azure 리소스 및 Azure 리소스의 보안에 대 한 향상 된 가시성과 제어를 제공 합니다. 

Defender for Cloud는 연결 된 리소스에 대 한 지속적인 보안 평가를 수행 하 고 [Azure 보안 벤치 마크](../benchmarks/introduction.md) 에 대 한 구성 및 배포를 비교 하 여 환경에 맞게 조정 된 세부 보안 권장 사항을 제공 합니다.

클라우드 용 Defender는 다음을 통해 Azure 리소스의 보안을 최적화 하 고 모니터링 하는 데 도움이 됩니다.

- 다음에 따라 Azure 구독 리소스에 대한 정책을 정의할 수 있습니다.
    - 조직의 보안 요구 사항.
    - 각 구독에서 애플리케이션의 유형 또는 데이터의 민감도
    - 구독에 적용하는 모든 산업 또는 규제 표준이나 벤치마크. 
- Azure 가상 머신, 네트워킹 및 애플리케이션의 상태를 모니터링합니다.
- 통합된 파트너 솔루션에서의 경고를 포함하여 우선 순위가 지정된 보안 경고 목록을 제공합니다. 또한 공격 및 해결 방법에 대한 권장 사항을 신속히 조사하는 데 필요한 정보를 제공합니다.

자세한 정보:

* [클라우드 용 Microsoft Defender 소개](../../security-center/security-center-introduction.md)
* [Microsoft Defender for Cloud에서 보안 점수 개선](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph는 Microsoft 제품 및 서비스에서 실시간 위협 보호를 제공합니다. 대량의 위협 인텔리전스 및 보안 데이터를 연결하는 고급 분석을 사용하여 조직 보안을 강화할 수 있는 통찰력을 제공합니다. Microsoft는 매월 4,500억 개 이상의 인증을 처리하고, 맬웨어 및 피싱을 찾기 위해 4,000억 개의 이메일을 검사하며, 10억 대의 디바이스를 업데이트하는 등의 고급 분석을 사용하여 더 풍부한 통찰력을 제공합니다. 이러한 통찰력은 조직이 검색 및 반응하여 공격에 대응하는 데 도움이 될 수 있습니다.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>다음 단계
[공유 책임 모델](shared-responsibility.md) 및 Microsoft에서 처리하는 보안 작업과 사용자가 처리하는 작업에 대해 알아봅니다.

보안 관리에 대한 자세한 내용은 [Azure의 보안 관리](management.md)를 참조하세요.
