---
title: Azure 엔드투엔드 보안 | Microsoft Docs
description: 이 문서에서는 클라우드 리소스를 보호하며, 위협을 감지하고 조사하는 데 도움이 되는 Azure 서비스의 맵을 다룹니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: eb96e21644f6318320ee8a5c7c33b83a73a59c94
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335502"
---
# <a name="end-to-end-security-in-azure"></a>Azure 엔드투엔드 보안
Azure를 애플리케이션과 서비스에 사용하는 가장 좋은 이유 중 하나는 광범위한 보안 도구와 기능을 활용한다는 점입니다. 이러한 도구 및 기능을 통해 안전한 Azure 플랫폼에서 안전한 솔루션을 만들 수 있습니다. Microsoft Azure는 고객 데이터의 기밀성, 무결성 및 가용성을 제공하는 한편 투명한 책임도 가능하게 합니다.

다음 다이어그램과 설명서에서는 Azure의 보안 서비스에 대해 소개합니다. 이러한 보안 서비스는 비즈니스의 보안 요구 사항을 충족하고 클라우드에서 사용자, 디바이스, 리소스, 데이터 및 애플리케이션을 보호하는 데 도움이 됩니다.

## <a name="microsoft-security-services-map"></a>Microsoft 보안 서비스 맵

보안 서비스 맵은 보호할 리소스(열)로 서비스를 구성합니다. 또한 다이어그램은 서비스를 다음 범주(행)로 그룹화합니다.

- 보안 및 보호 - ID, 호스트, 네트워크 및 데이터에 걸쳐 계층화된 심층 방어 전략을 구현할 수 있는 서비스입니다. 이 보안 서비스 및 기능 컬렉션은 Azure 환경에서 보안 상태를 파악하고 개선하는 방법을 제공합니다.
- 위협 감지 – 의심스러운 활동을 식별하고 위협을 완화하는 서비스입니다.
- 조사 및 응답 – 의심스러운 활동을 평가하고 대응할 수 있도록 로깅 데이터를 가져오는 서비스입니다.

다이어그램에는 Azure에서 사용하는 서비스를 보호하는 데 사용할 수 있는 효과적인 보안 권장 사항 컬렉션인 Azure Security Benchmark 프로그램이 포함되어 있습니다.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Azure의 종단 간 보안 서비스를 보여주는 다이어그램" border="false":::

## <a name="security-controls-and-baselines"></a>보안 제어 및 기준
[Azure Security Benchmark](../benchmarks/introduction.md) 프로그램에는 Azure에서 사용하는 서비스를 보호하는 데 사용할 수 있는 효과적인 보안 권장 사항 컬렉션이 포함되어 있습니다.

- 보안 제어 - 이러한 권장 사항은 일반적으로 Azure 테넌트 및 Azure 서비스 전체에 적용됩니다. 각 권장 사항은 벤치마크의 계획, 승인 또는 구현에 관여하는 이해관계자의 목록을 식별합니다.
- 서비스 기준 - 개별 Azure 서비스에 컨트롤을 적용하여 해당 서비스의 보안 구성에 대한 권장 사항을 제공합니다.

## <a name="secure-and-protect"></a>보안 및 보호

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="클라우드 리소스를 보호하는 데 도움이 되는 Azure 서비스를 보여주는 다이어그램입니다." border="false":::

| 서비스 | 설명 |
|------|--------|
| [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md)| 데이터 센터의 보안 상태를 강화하고, 온-프레미스뿐 아니라 Azure에 있는지 여부와 관계없이 클라우드의 전체 하이브리드 워크로드에 대해 지능형 위협 방지 기능을 제공하는 통합된 인프라 보안 관리 시스템입니다. |
| **ID&nbsp;&&nbsp;액세스&nbsp;관리** | |
| [Azure AD(Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md)| Microsoft 클라우드 기반 ID 및 액세스 관리 서비스.  |
|  | [조건부 액세스](../../active-directory/conditional-access/overview.md)는 ID 신호를 한데 가져와 결정을 내리고 조직 정책을 적용하기 위해 Azure AD에서 사용하는 도구입니다. |
|  | [도메인 서비스](../../active-directory-domain-services/overview.md)는 도메인 조인, 그룹 정책, LDAP(Lightweight Directory Access Protocol) 및 Kerberos/NTLM 인증과 같이 관리되는 도메인 서비스를 제공하기 위해 Azure AD에서 사용하는 도구입니다. |
|  | [PIM(Privileged Identity Management)](../../active-directory/privileged-identity-management/pim-configure.md)은 조직에서 중요한 리소스에 대한 액세스를 관리, 제어 및 모니터링할 수 있는 Azure AD의 서비스입니다. |
|  | [다단계 인증](../../active-directory/authentication/concept-mfa-howitworks.md)은 두 번째 형태의 인증을 요구하여 데이터 및 애플리케이션에 대한 액세스를 보호하기 위해 Azure AD에서 사용하는 도구입니다. |
| [Azure AD ID 보호](../../active-directory/identity-protection/overview-identity-protection.md) | 각 조직에서 ID 기반 위험 검색 및 수정을 자동화하고, 포털의 데이터를 사용하여 위험을 조사하며, 추가 분석을 위해 위험 검색 데이터를 타사 유틸리티로 내보내는 데 사용할 수 있는 도구입니다. |
| **인프라&nbsp;&&nbsp;네트워크** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | 공용 인터넷을 통해 Azure 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 보내고, Microsoft 네트워크를 통해 Azure 가상 네트워크 간에 암호화된 트래픽을 전송하는 데 사용되는 가상 네트워크 게이트웨이입니다. |
| [Azure DDoS Protection 표준](../../ddos-protection/ddos-protection-overview.md) | DDoS 공격을 막는 향상된 DDoS 완화 기능을 제공합니다. 가상 네트워크에서 특정 Azure 리소스를 보호하도록 자동으로 조정됩니다. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Microsoft 글로벌 에지 네트워크를 사용하여 빠르고, 안전하고, 확장성이 뛰어난 웹 애플리케이션을 만들기 위한 확장 가능 글로벌 진입점입니다. |
| [Azure Firewall](../../firewall/overview.md) | Azure Virtual Network 리소스를 보호하는 관리형 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. |
| [Azure Key Vault](../../key-vault/general/overview.md) | 토큰, 암호, 인증서, API 키 및 기타 암호에 대한 보안 비밀 저장소입니다. Key Vault를 사용하면 데이터를 암호화하는 데 사용되는 암호화 키를 만들고 제어할 수 있습니다. |
| [Key Vault 관리형 HSM](../../key-vault/managed-hsm/overview.md) | FIPS 140-2 수준 3 유효성이 검사된 HSM을 사용하여 클라우드 애플리케이션용 암호화 키를 보호할 수 있는 완전 관리형 고가용 단일 테넌트 표준 규격 클라우드 서비스입니다. |
| [Azure Private Link](../../private-link/private-link-overview.md) | 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS Services(예: Azure Storage 및 SQL Database)와 Azure 호스팅 고객 소유/파트너 서비스에 액세스할 수 있습니다. |
| [Azure Application Gateway](../../application-gateway/overview.md) | 웹 애플리케이션에 대한 트래픽을 관리할 수 있도록 하는 고급 웹 트래픽 부하 분산 장치입니다. Application Gateway는 URI 경로 또는 호스트 헤더와 같은 HTTP 요청의 추가 특성을 기반으로 라우팅 결정을 내릴 수 있습니다. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | 메시지 큐와 게시-구독 토픽이 있는 완전 관리형 엔터프라이즈 메시지 broker입니다. Service Bus는 애플리케이션과 서비스를 서로 분리하는 데 사용됩니다. |
| [웹 애플리케이션 방화벽](../../web-application-firewall/overview.md) | 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. WAF는 Azure Application Gateway 및 Azure Front Door Service와 함께 배포할 수 있습니다. |
| **데이터 및 애플리케이션** |  |
| [Azure Backup](../../backup/backup-overview.md) | 데이터를 백업하고 Microsoft Azure 클라우드에서 복구할 수 있는 간단하고, 안전하며, 비용 효율적인 솔루션을 제공합니다. |
| [Azure Storage 서비스 암호화](../../storage/common/storage-service-encryption.md) | 데이터를 저장하기 전에 자동으로 암호화하고, 데이터를 검색할 때 자동으로 암호 해독할 수 있습니다. |
| [Azure Information Protection](/azure/information-protection/what-is-information-protection) | 조직에서 콘텐츠에 레이블을 적용하여 문서 및 이메일을 검색, 분류 및 보호할 수 있도록 하는 클라우드 기반 솔루션입니다. |
| [API Management](../../api-management/api-management-key-concepts.md) | 기존 백 엔드 서비스를 위해 일관된 최신 API 게이트웨이를 빠르게 만드는 방법입니다. |
| [Azure 기밀 컴퓨팅](../../confidential-computing/overview.md) | 이것을 사용하여 클라우드에서 처리하는 동안 중요한 데이터를 격리할 수 있습니다. |
| [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) | 개발 프로젝트에서 Azure DevOps에 저장된 보안 및 거버넌스 기술, 운영 관행 및 규정 준수 정책의 여러 계층을 활용할 수 있습니다. |
| **고객 액세스** |  |
| [Azure Active Directory External Identities](../../active-directory/external-identities/compare-with-b2c.md) | Azure AD의 외부 ID를 사용하여 조직 외부 사용자가 앱과 리소스에 액세스하도록 허용하는 동시에 원하는 모든 ID를 사용하여 로그인하도록 할 수 있습니다. |
|  | [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md) 협업을 통해 외부 사용자와 앱과 리소스를 공유할 수 있습니다. |
|  | [Azure AD B2C](../../active-directory-b2c/overview.md)를 사용하면 매일 수백만 명의 사용자와 수십억 건의 인증을 지원하여 서비스 거부, 암호 스프레이 또는 무차별 암호 대입 공격 같은 위협을 모니터링하고 자동으로 처리할 수 있습니다. |

## <a name="detect-threats"></a>위협 감지

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="위협을 검색하는 Azure 서비스를 보여주는 다이어그램" border="false":::

| 서비스 | 설명 |
|------|--------|
| [Microsoft Defender for Cloud](../../security-center/azure-defender.md) | Azure, 하이브리드 리소스, 워크로드에 고급 지능형 보호를 제공합니다. Defender for Cloud의 작업 보호 대시보드는 사용자 환경에 대 한 클라우드 워크 로드 보호 기능에 대 한 가시성 및 제어를 제공 합니다. |
| [Microsoft 센티널](../../sentinel/overview.md) | 확장 가능한 클라우드 네이티브, SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동화 응답) 솔루션입니다. Sentinel은 엔터프라이즈 전반에 지능적인 보안 분석 및 위협 인텔리전스를 제공하며, 경고 검색, 위협 가시성, 주도적 헌팅 및 위협 대응을 위한 단일 솔루션을 제공합니다. |
| **ID&nbsp;&&nbsp;액세스&nbsp;관리** |  |
| [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) | 엔드포인트, ID, 메일 및 애플리케이션 전반에서 감지, 방지, 조사, 응답을 기본적으로 조정하여 정교한 공격에 대비한 통합 보호를 제공하는 통합된 위반 전후 엔터프라이즈 방어 도구 모음입니다. |
|  | [Microsoft Defender For Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint)는 엔터프라이즈 네트워크가 고급 위협을 방지, 검색, 조사 및 대응하는 데 도움이 되도록 설계된 엔터프라이즈 엔드포인트 보안 플랫폼입니다. |
|  | [Microsoft Defender for Identity](/defender-for-identity/what-is)는 온-프레미스 Active Directory 신호를 활용하여 고급 위협, 손상된 ID 및 사용자의 조직을 대상으로 한 악의적인 내부자 작업을 식별하고 검색하고 조사하는 클라우드 기반 보안 솔루션입니다. |
| [Azure AD ID 보호](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | 사용자 위험 및 위험 검색을 관리하는 데 도움이 되는 두 가지 유형의 자동화된 알림 이메일을 보냅니다(감지된 위험 사용자 이메일과 주간 요약 이메일). |
| **인프라 및 네트워크** |  |
| [IoT용 Microsoft Defender](../../defender-for-iot/overview.md) | IoT/OT 디바이스, 취약성 및 위협을 식별하는 통합 보안 솔루션입니다. 기존 IoT/OT 디바이스를 보호해야 하든 아니면 새로운 IoT 혁신의 보안을 구축해야 하든, 이 솔루션은 전체 IoT/OT 환경을 보호합니다. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Azure 가상 네트워크의 리소스를 모니터링 및 진단하고, 메트릭을 조회하고, 리소스에 대한 로그를 활성화 또는 비활성화하는 도구를 제공합니다. Network Watcher는 Virtual Machines, Virtual Networks, Application Gateways, 부하 분산 장치를 포함하는 IaaS 제품의 네트워크 상태를 모니터링하고 복구하도록 설계되었습니다. |
| [Azure Policy 감사 로깅](../../governance/policy/overview.md) | 조직의 표준을 적용하고 규정 준수를 규모에 맞게 평가할 수 있습니다. Azure Policy는 자동으로 사용하도록 설정되는 활동 로그를 사용하여 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소를 포함시킵니다. |
| **데이터 및 애플리케이션** |  |
| [컨테이너 레지스트리용 Microsoft Defender](../../security-center/defender-for-container-registries-introduction.md) | Azure Resource Manager 기반 Azure Container Registry 레지스트리의 이미지를 검사하고 이미지의 취약성에 대한 보다 심층적인 가시성을 제공하는 취약성 검사기가 포함되어 있습니다. |
| [Microsoft Defender for Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | AKS(Azure Kubernetes Service)에서 검색한 로그를 통해 AKS 관리형 서비스를 모니터링하여 클러스터 수준 위협 방지를 제공합니다. |
| [Cloud Apps용 Microsoft Defender](/cloud-app-security/what-is-cloud-app-security) | 여러 클라우드에서 작동하는 CASB(클라우드 액세스 보안 브로커)입니다. 다양한 가시성, 데이터 이동 제어 및 정교한 분석을 제공하여 모든 클라우드 서비스에서의 사이버 위협을 식별하고 대처합니다. |

## <a name="investigate-and-respond"></a>조사 및 대응

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="위협을 조사하고 위협에 대응하는 데 도움이 되는 Azure 서비스를 보여주는 다이어그램입니다." border="false":::

| 서비스 | 설명 |
|------|--------|
| [Microsoft Sentinel](../../sentinel/hunting.md) | 조직의 데이터 원본에서 보안 위협을 헌팅하는 강력한 검색 및 쿼리 도구입니다. |
| [Azure&nbsp;모니터&nbsp;로그&nbsp;및&nbsp;메트릭](../../azure-monitor/overview.md) | 클라우드 및 온-프레미스 환경에서 원격 분석의 수집, 분석 및 작업에 대한 포괄적인 솔루션을 제공합니다. Azure Monitor는 다양한 원본의 [데이터를 수집하여](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) 분석, 시각화 및 경고 생성에 사용할 수 있는 공통 데이터 플랫폼으로 집계합니다. |
| **ID&nbsp;&&nbsp;액세스&nbsp;관리** |  |
| [Azure&nbsp;AD&nbsp;보고서&nbsp;및&nbsp;모니터링](../../active-directory/reports-monitoring/index.yml) | [Azure AD 보고서](../../active-directory/reports-monitoring/overview-reports.md)는 환경에서 작업의 포괄적인 보기를 제공합니다. |
|  | [Azure AD 모니터링](../../active-directory/reports-monitoring/overview-monitoring.md)을 사용하여 Azure AD 활동 로그를 다른 엔드포인트로 라우팅할 수 있습니다.|
| [Azure AD PIM 감사 기록](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | 권한이 있는 모든 역할에 대해 지난 30일 이내에 모든 역할 할당 및 활성화를 표시합니다. |
| **데이터 및 애플리케이션** |  |
| [Cloud Apps용 Microsoft Defender](/cloud-app-security/investigate) | 클라우드 환경에서 발생하는 일에 대해 더 자세히 알 수 있는 도구를 제공합니다. |

## <a name="next-steps"></a>다음 단계

- [클라우드에서의 공동 책임](shared-responsibility.md)을 이해합니다.

- 악성 사용자와 비-악성 사용자 모두에 대한 [Azure 클라우드의 격리 선택 사항](isolation-choices.md)을 이해합니다.
