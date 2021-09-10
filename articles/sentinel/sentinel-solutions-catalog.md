---
title: Azure Sentinel 솔루션 카탈로그 | Microsoft Docs
description: 이 문서에서는 현재 사용할 수 있는 Sentinel 솔루션 패키지를 표시하고 자세히 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: yelevin
ms.openlocfilehash: c983ff540637478d9283b76d060c7f04c319adef
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442486"
---
# <a name="azure-sentinel-solutions-catalog"></a>Azure Sentinel 솔루션 카탈로그

> [!IMPORTANT]
>
> Azure Sentinel 솔루션 환경은 모든 개별 솔루션 패키지와 마찬가지로 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure Sentinel 솔루션](sentinel-solutions.md)은 단일 배포 단계를 통해 작업 영역에서 데이터 커넥터, 통합 문서, 분석 및 자동화와 같은 Azure Sentinel 콘텐츠를 획득하는 통합된 방법을 제공합니다.

##  <a name="currently-available-solutions"></a>현재 사용 가능한 솔루션

이제 Azure Sentinel에서 다음 솔루션을 사용할 수 있습니다. 데이터 수집, Logic Apps 사용 등에 대한 일반적인 요금이 적용되지만 모두 무료로 설치할 수 있습니다.


| 해결 방법 | Description |
| - | - |
| **Sentinel용 Azure Firewall 솔루션** | Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다.  Sentinel의 이 Azure Firewall 솔루션은 Azure Sentinel을 기반으로 사용자 지정 가능한 기본 제공 위협 검색을 제공합니다. 솔루션에는 통합 문서, 검색, 헌팅 쿼리 및 플레이북이 포함됩니다. |
| **Azure Sentinel 4 Dynamics 365** | Dynamics 365용 지속적인 위협 모니터링 솔루션은 Dynamics 365 로그를 수집하고, Dynamics 365 내 활동의 가시성을 확보하고 분석하여 위협 및 악의적인 활동을 검색하는 기능을 제공합니다. 솔루션에는 데이터 커넥터, 통합 문서, 분석 규칙 및 헌팅 쿼리가 포함됩니다. |
| **SQL PaaS용 Azure Sentinel**| 이 솔루션은 SQL 감사 로그를 기준으로 하고 Azure Defender for SQL의 경고를 원활하게 통합하여 Azure Sentinel 위에 Azure SQL PaaS 서비스에 대한 사용자 지정 가능한 위협 검색 기능을 제공합니다.|
| **Teams용 Azure Sentinel** | Teams는 Microsoft 365 클라우드에서 통신 및 데이터 공유 모두에서 중심적인 역할을 합니다. Teams 서비스는 클라우드의 많은 기본 기술을 활용하기 때문에 로그에서 헌팅할 때뿐만 아니라 회의에서 실시간으로 모니터링할 때에도 인간 및 자동화된 분석을 활용할 수 있습니다. Azure Sentinel은 관리자에게 이러한 솔루션을 제공합니다. 이 솔루션에는 분석 규칙, 헌팅 쿼리 및 플레이북이 포함됩니다. |
| **Box 솔루션** | [Box](https://www.box.com/overview)는 파일 생성 및 공유에서 공동 편집, 서명, 분류 및 보존에 이르기까지 전체 콘텐츠 수명 주기 동안 구축된 안전하고 사용하기 쉬운 단일 플랫폼입니다. |
|**Check Point Azure Sentinel 솔루션** |Check Point 논리 앱 커넥터 및 플레이북을 통해 고객은 Azure Sentinel에서 직접 미리 패키징된 Check Point 커넥터 및 플레이북을 쉽게 찾아서 배포할 수 있습니다. 사용자는 CloudGuard 보안 게이트웨이 및 온-프레미스 Check Point 게이트웨이를 사용하여 자동으로 위협을 수정하도록 Azure Sentinel SOAR 플레이북을 구성함으로써 Microsoft Azure 및 Check Point CloudGuard의 보안 기능을 향상시킬 수 있습니다. <br><br>Check Point 논리 앱 커넥터 및 플레이북도 자동화된 수정 기능을 제공할 수 있습니다. 고객은 Check Point CloudGuard 보안 게이트웨이를 자동으로 트리거하여 보안 정책을 업데이트하고 악의적인 트래픽을 차단하도록 SOAR 플레이북을 구성할 수 있습니다. 이 커넥터는 Check Point Management API를 활용하여 이러한 보안 작업을 자동화할 수 있습니다. 이를 통해 IT 직원, 네트워크 관리자 및 보안 담당자는 시간을 절약할 수 있을 뿐만 아니라 공격자가 보안 문제를 악용할 수 있는 시간이 크게 단축될 수 있습니다. 이러한 문제는 수동으로 해결할 필요가 없기 때문입니다. 그뿐 아니라 네트워크 및 보안 관리자, 보안 분석가, DevOps/DevSecOps 팀의 시간도 절약하면서 조직의 공격 노출 표면을 최소화할 수 있습니다. 또한 미리 정의된 플레이북은 개별 API 호출을 쓸 필요가 없도록 하며, Check Point 플레이북을 모든 원시 Azure 서비스 및 수백 개의 기존 논리 앱 커넥터와 쉽게 통합할 수 있습니다. |
|**Cisco ACI Solution** | 업계 최고의 Cisco Nexus 9000 플랫폼을 토대로 구축된 [Cisco ACI(Cisco Application Centric Infrastructure)](https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/solution-overview-c22-741487.html)는 데이터 센터의 민첩성과 복원력을 지원하는 의도 기반 네트워킹 프레임워크에 포함됩니다. Cisco ACI는 인프라 배포 및 거버넌스를 가속화하고, 다중 패브릭 및 다중 클라우드 프레임워크에서 워크로드를 쉽게 이동하도록 관리를 간소화하고, 어디에서나 발생하는 위험으로부터 사전에 보호하는 자동화를 지원합니다. 애플리케이션 배포 수명 주기를 완전히 간소화하고 최적화하고 가속화합니다.|
| **Cisco ISE 솔루션** | [Cisco ISE(Identity Services Engine)](https://www.cisco.com/c/en/us/products/collateral/security/identity-services-engine/data_sheet_c78-656174.html)는 보안 정책 관리를 간소화하고 운영 비용을 줄이기 위한 원스톱 솔루션입니다. ISE를 사용하여 유선, 무선 및 VPN 연결을 통해 회사 네트워크에 액세스를 제어하는 사용자 및 디바이스를 확인할 수 있습니다. |
|**Cisco SEG 솔루션** | [Cisco SEG(Secure Email Gateway)](https://www.cisco.com/c/en/us/products/security/email-security/index.html)는 업계 최고 수준으로 사이버 위협에서 메일을 보호합니다. 온-프레미스 및 클라우드 기반 메일에 대한 Secure Email 기능의 포괄적인 보호는 가장 일반적이면서 심각한 손상을 주는 사이버 위협을 중지합니다.|
| **Cisco Umbrella 솔루션** | [Cisco Umbrella](https://umbrella.cisco.com/)는 필요할 때 필요한 방식으로 유연한 클라우드 제공 보안을 제공합니다. 여러 보안 기능을 하나의 솔루션으로 결합하므로 디바이스, 원격 사용자 및 분산 위치로의 보호를 확장할 수 있습니다.  |
|**Cisco WSA(Web Security Appliance)** |[Cisco Secure Web Appliance](https://www.cisco.com/c/en/us/products/security/web-security-appliance/index.html)는 TLS 1.3 및 고성능 기능을 사용하여 조직을 보호함으로써 웹 기반 위협을 자동으로 검색 및 차단하고 사용자를 안전하게 보호하는 웹 프록시 솔루션입니다. |
| **Cloudflare 솔루션** | [Cloudflare](https://www.cloudflare.com/)는 웹 사이트, API 및 애플리케이션과 같은 외부 리소스의 안정성을 보호하고 보장합니다. 이 솔루션은 방화벽 애플리케이션, 팀, 디바이스 등의 내부 리소스를 보호합니다. 그리고 전역적으로 확장 가능한 애플리케이션을 개발하기 위한 플랫폼입니다. |
| **SAP용 Continuous Threat Monitoring** | [SAP용 Continuous Threat Monitoring](sap-deploy-solution.md)을 사용하면 비즈니스 및 애플리케이션 계층 내에서 정교한 위협이 있는지 SAP 시스템을 모니터링할 수 있습니다. SAP 데이터 커넥터는 전체 SAP 시스템 환경에서 다양한 14개의 애플리케이션 로그를 스트리밍하고 NetWeaver RFC 호출을 통해 ABAP(Advanced Business Application Programming)에서, OSSAP 제어 인터페이스를 통해 파일 스토리지 데이터에서 로그를 수집합니다. |
| **Contrast Protect Azure Sentinel 솔루션** | [Contrast Protect](https://www.contrastsecurity.com/runtime-application-self-protection-rasp)는 애플리케이션 내에 자동화되고 정확한 런타임 보호 기능을 내장하여 공격을 지속적으로 모니터링하고 차단함으로써 팀이 실행 중인 모든 애플리케이션을 방어할 수 있도록 합니다. 실행 가능하고 시기 적절한 애플리케이션 계층 위협 인텔리전스에 집중함으로써 보안 및 운영 팀이 위협 및 공격의 심각도를 이해하고 관리하는 데 더 쉽게 사용할 수 있습니다. Contrast Protect는 Azure Sentinel로 원활하게 통합되므로 애플리케이션 계층에서 보안 위험에 대한 통찰력을 높일 수 있습니다. |
| **Azure Sentinel용 Corelight** | Azure Sentinel용 [Corelight](https://corelight.com/)를 사용하면 Azure Sentinel을 사용하는 인시던트 응답자와 위협 헌터가 더 빠르고 효과적으로 작업할 수 있습니다. Corelight는 네트워크 방어자가 환경에 대한 광범위한 가시성을 얻을 수 있도록 하는 최고 수준의 오픈 소스 기술 Zeek 및 Suricata를 기반으로 하는 NDR(네트워크 검색 및 응답) 솔루션을 제공합니다.<br><br>데이터 커넥터를 사용하면 Corelight 센서를 통해 Zeek 및 Suricata에서 Azure Sentinel 이벤트를 수집할 수 있습니다. 또한 Azure Sentinel Corelight에는 통합 문서 및 대시보드, 헌팅 쿼리 및 분석 규칙이 포함되어 있습니다. 이를 통해 조직은 Corelight와 Azure Sentinel을 조합하여 효율적인 조사 및 인시던트 응답을 추진할 수 있습니다. |
| **CrowdStrike Falcon Endpoint Protection 솔루션** | [CrowdStrike Falcon Endpoint Protection](https://www.crowdstrike.com/resources/data-sheets/falcon-endpoint-protection-pro/)은 가장 효과적인 예방 기술과 완전한 공격 가시성을 내장된 위협 인텔리전스 및 응답과 결합하여 이상적인 바이러스 백신 대체 솔루션을 제공합니다. |
| **Azure Sentinel용 HYAS Insight 솔루션 갤러리** | [HYAS Insight](https://www.hyas.com/hyas-insight)는 결과 정확도를 높이면서 분석가, 연구원 및 조사자의 가시성과 생산성을 향상시키기 위해 배타적 데이터 원본 및 기존의 메커니즘을 사용하는 위협 조사 및 특성 솔루션입니다. HYAS Insight는 공격 인스턴스 및 캠페인을 수십억 개의 손상 지표에 연결하여 인사이트와 가시성을 제공합니다. HYAS Insight는 사용하기 쉬운 사용자 인터페이스, 변환 및 API 액세스를 통해 풍부한 위협 데이터를 강력한 연구 및 특성 솔루션으로 결합합니다. HYAS Insight는 조직이 매일 직면하는 위협의 특성을 더 잘 이해할 수 있도록 도와주는 HYAS Intelligence 팀에 의해 보완됩니다. |
|**Google Cloud Platform DNS 솔루션(미리 보기)** | [Google Cloud Platform DNS(Domain Name System)](https://cloud.google.com/dns)는 비용 효율적인 방식으로 글로벌 DNS에 도메인 이름을 게시하는 복원력이 뛰어난 고성능 글로벌 DNS(Domain name system) 서비스입니다.|
| **Google Cloud Platform Cloud Monitoring 솔루션** |[Google Cloud Platform Cloud Monitoring](https://cloud.google.com/monitoring)은 Google Cloud 서비스에 대한 자동 기본 제공 메트릭 수집 대시보드를 제공합니다. 또한 하이브리드 및 다중 클라우드 환경의 모니터링을 지원하고, 애플리케이션 및 인프라의 성능, 가용성 및 상태에 대한 가시성을 제공합니다. |
| **Google Cloud Platform IAM 솔루션** | [Google Cloud Platform IAM(ID 및 액세스 관리)](https://cloud.google.com/iam)을 사용하여 관리자는 특정 리소스에 대해 작업을 수행할 수 있는 사용자에게 권한을 부여하고, Google Cloud 리소스를 중앙에서 관리하기 위한 모든 권한 및 가시성을 부여합니다|
| **Infoblox 클라우드 데이터 커넥터 솔루션** | [BloxOne DDI](https://www.infoblox.com/products/bloxone-ddi/)는 업계 최초의 DDI(DNS/DHCP/IPAM) 솔루션으로, 이를 통해 전례 없는 비용 효율성으로 클라우드에서 모든 위치에 이르는 DDI를 중앙에서 관리하고 자동화할 수 있습니다. 클라우드 네이티브 원칙을 사용하여 구축되고 SaaS 서비스로 사용할 수 있는 BloxOne DDI는 기존 DDI 구현에 대한 복잡성, 병목 및 확장성 제한을 제거하여 네트워크 관리를 크게 간소화합니다.<br><br>BloxOne 위협 방어는 기존 방어를 사용하여 네트워크를 보호하고 SD WAN, IoT 및 클라우드를 비롯한 필수적인 디지털 보안을 자동으로 확장하여 브랜드 보호를 극대화합니다. 이 솔루션은 보안 오케스트레이션, 자동화 및 응답(SOAR) 솔루션을 개발하고, 사이버 위협을 조사하고 수정하는 데 걸리는 시간을 줄이고, 전체 보안 에코 시스템의 성능을 최적화하고, 엔터프라이즈 위협 방어의 총 비용을 절감합니다. |
|**Juniper IDP** | [Juniper Intrusion Detection and Prevention](https://www.juniper.net/documentation/us/en/software/junos/idp-policy/topics/topic-map/security-idp-overview.html)은 네트워크에서 발생하는 이벤트를 모니터링하고 보안 정책에 대한 가능한 인시던트, 위반 또는 임박한 위협 징후가 있는지 분석합니다. 이러한 보안 조치는 네트워크의 일부로 포함되어 잠재적 인시던트를 검색 및 중지하는 IDS(침입 탐지 시스템) 및 IPS(침입 방지 시스템)로 사용될 수 있습니다.|
| **McAfee ePolicy Orchestrator 솔루션** | [McAfee ePO](https://www.mcafee.com/enterprise/en-in/products/epolicy-orchestrator.html)는 엔드포인트 및 엔터프라이즈 보안 제품에 대해 중앙 집중식으로 정책을 관리하고 적용합니다. McAfee ePO는 네트워크를 모니터링하고 관리하며 위협을 검색하고 이러한 위협으로부터 엔드포인트를 보호합니다. |
|**McAfee Network Security Platform 솔루션** |[McAfee Network Security Platform](https://www.mcafee.com/enterprise/en-us/products/virtual-network-security-platform.html)은 가상화된 환경에서 네트워크 보호를 확장하여 가상 네트워크에 대한 지능형 위협 방지 기능을 제공합니다. |
| **Oracle Database 감사 솔루션** | [Oracle Database](https://www.oracle.com/database/technologies/security/db-auditing.html)는 데이터베이스의 Enterprise 및 Standard Edition 둘 다에서 강력한 감사 지원을 제공합니다. Oracle Database 통합 감사를 사용하면 정책 및 조건을 사용하여 Oracle Database 내에서 선택 및 효과적인 감사를 수행할 수 있습니다. |
| **Palo Alto Prisma 솔루션** | [Prisma Cloud](https://www.paloaltonetworks.com/prisma/cloud)는 멀티 및 하이브리드 클라우드 환경을 위한 전체 수명 주기 보안과 전체 스택 보호를 제공하는 업계 최고의 포괄적인 클라우드 네이티브 보안 플랫폼입니다. |
| **PingFederate 솔루션** | [PingFederate®](https://www.pingidentity.com/en/resources/client-library/data-sheets/pingfederate-data-sheet.html)는 사용자 인증 및 직원, 파트너 및 고객 ID 유형을 위한 표준 기반 SSO(Single Sign-On)를 위한 선도적인 엔터프라이즈 페더레이션 서버입니다. PingFederate를 사용하면 조직은 비용이 많이 들고 유연하지 않은 레거시 IAM 솔루션을 사용하지 않고 복잡한 엔터프라이즈 요구를 충족하도록 설계된 최신 ID 및 액세스 관리 솔루션을 대신 적용할 수 있습니다. |
| **Proofpoint POD 솔루션** | [Proofpoint on Demand Email Security](https://www.proofpoint.com/us/products/email-security-and-protection/email-protection)는 다양한 유형의 이메일을 정확하게 분류하는 동시에 악성 페이로드와 관련이 없는 위협을 검색하고 차단합니다. 의심스러운 이메일에 자동으로 태그를 지정하여 최종 사용자 인식을 높이고 몇 초 안에 모든 이메일을 추적할 수 있습니다. |
| **Proofpoint TAP 솔루션** | [Proofpoint TAP(Targeted Attack Protection)](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection)는 악성 첨부 파일 및 URL을 사용하여 맬웨어를 설치하거나 사용자를 속여 암호 및 중요한 정보를 공유하도록 하는 공격을 포함하여 이메일을 통해 사용자를 대상으로 하는 고급 위협을 검색, 완화 및 차단하는 데 도움이 됩니다. 또한 TAP는 클라우드 앱에서 위협 및 위험을 검색하고 자격 증명 유출 또는 기타 공격과 관련된 이메일 공격을 연결합니다. |
| **Qualys VM 솔루션** | [Qualys VM(Vulnerability Management)](https://www.qualys.com/apps/vulnerability-management/)은 IT 자산이 취약한 위치와 보호 방법에 대한 글로벌 가시성을 제공합니다. 기업이 디지털 변환을 위해 클라우드 컴퓨팅, 이동성 및 기타 중단 기술을 채택함에 따라 Qualys VM은 기존 경계가 흐리게 된 하이브리드 IT 환경에 대한 차세대 취약성 관리 기능을 제공합니다. 이는 네트워크 범위를 검사할 수 없는 자산으로 확장하는 경량 Qualys 클라우드 에이전트에서 에이전트 기반 검색을 통해 수행됩니다. |
| **Rapid7 InsightVM CloudAPI 솔루션** |[Rapid7 Insight platform](https://www.rapid7.com/products/insightvm/)은 취약성 연구, 악용 지식, 글로벌 공격자 동작, 인터넷 차원의 검색 데이터, 노출 분석 및 실시간 보고의 Rapid7 라이브러리를 함께 사용하여 취약성 데이터를 수집하고 답변으로 전환하는 완전히 사용 가능하고 스케일링 가능한 효율적인 방법을 제공합니다. InsightVM은 라이브 취약성 및 엔드포인트 분석을 위해 이 플랫폼을 활용합니다. |
| **RiskIQ 보안 인텔리전스 플레이북** | [RiskIQ](https://www.riskiq.com/)는 Azure Sentinel 플랫폼 내 인시던트에 컨텍스트를 강화하고 추가하기 위해 기능을 미리 패키지화하는 여러 Azure Sentinel 플레이북을 만들었습니다. 이러한 플레이북은 개별적으로 실행하거나 Azure Sentinel 포털 내에서 자동으로 실행되도록 구성할 수 있습니다. 인시던트에서 도메인 또는 IP 주소와 같은 알려진 지표를 포함하는 경우 RiskIQ는 인터넷에서 연결된 다른 기능과 위협이 될 수 있는 경우 해당 값을 보강합니다. 설명은 RiskIQ의 조사 플랫폼 내에서 자세한 정보로 연결되는 인시던트에 추가됩니다. |
| **Azure Sentinel용 Senserva 제품** | [Senserva](https://www.senserva.com/product/)는 Azure Sentinel용 CSPM(클라우드 보안 태세 관리)이며 우선 순위 기반 위험 평가를 지속적으로 생성하여 문제가 되기 전에 Azure Active Directory 보안 위험 관리를 단순화합니다. Senserva 정보에는 Senserva에서 관리하는 모든 Azure 개체에 대한 자세한 보안 순위가 포함되며, 가장 높은 영향을 미치는 항목의 가장 중요한 문제를 먼저 수정하여 고객이 최적의 검색 및 수정을 수행할 수 있도록 합니다.  Senserva의 모든 보강 정보는 Log Analytics 작업 영역을 통해 쿼리, 통합 문서 및 플레이북에서 처리하기 위해 Azure Sentinel에 전송됩니다.<br><br>Senserva는 Microsoft 클라우드 환경 내에서 보안 사용자 계정, Azure Active Directory 구성(조건부 액세스 포함), 애플리케이션 및 이벤트에 대한 심층 분석을 제공합니다. Senservas의 특허 기술을 통해 Microsoft 365 및 Microsoft Azure 관리자와 보안 팀의 수많은 시간을 절약할 수 있습니다. Senserva는 특허 출원 중인 산업 표준(NIST 800-53, MITER ATT&CK), 업계 권장 사항, 공급업체 권장 사항 및 Senserva 자체 전문가의 조합을 사용하여 만들어졌습니다. |
| **Slack Audit 솔루션** | [Slack Audit](https://slack.com/) 데이터 커넥터를 사용하면 REST API를 통해 [Slack Audit 레코드](https://api.slack.com/admins/audit-logs) 이벤트를 Azure Sentinel로 수집할 수 있습니다. 자세한 내용은 [API 설명서](https://api.slack.com/admins/audit-logs#the_audit_event)를 참조하세요. 이러한 이벤트의 가시성은 잠재적 보안 위험 검사, 팀의 공동 작업 사용 분석, 구성 문제 진단 등을 수행하는 데 도움이 됩니다. |
|**Sophos Endpoint Protection 솔루션** | [Sophos](https://www.sophos.com/en-us/company.aspx)는 차세대 사이버 보안 분야의 세계적 선두주자로, 오늘날과 같은 강력한 사이버 위협에 직면한 150개국 이상에 있는 50만 개 이상의 조직과 수백만 명의 소비자를 보호합니다. SophosLabs 및 SophosAI의 위협 인텔리전스, AI 및 기계 학습을 활용하는 Sophos는 사용자, 네트워크 및 엔드포인트를 랜섬웨어, 맬웨어, 악용, 피싱 및 광범위한 다른 사이버 공격으로부터 보호하기 위한 광범위한 고급 제품 및 서비스 포트폴리오를 제공합니다.|
| **Sophos XG Firewall 솔루션** | [Sophos XG Firewall](https://www.sophos.com/products/next-gen-firewall.aspx)은 가시성, 동기화된 보안 및 자동 응답을 통해 기업을 위한 올인원 보호를 제공합니다. 여기에는 숨겨진 위험 노출, 알 수 없는 위협 중지 및 감염된 시스템 격리 등이 포함됩니다. XG Firewall을 사용하면 VPN 클라이언트 및 하드웨어를 통해 어디에서든지 보안 네트워크를 직원으로 쉽게 확장할 수 있습니다. |
| **Symantec Endpoint Protection 솔루션** | [Symantec Endpoint Protection](https://www.broadcom.com/products/cyber-security/endpoint)은 서버 및 데스크톱 컴퓨터를 위한 맬웨어 방지, 침입 방지 및 방화벽 기능으로 구성된 보안 소프트웨어 제품군입니다. 또한 데이터 손실 방지 소프트웨어의 일반적인 몇 가지 기능도 있습니다. 승인되지 않은 프로그램 실행을 방지하고 네트워크 트래픽을 차단하거나 허용하는 방화벽 정책을 적용하는 데 사용됩니다. 회사 네트워크에 있거나 웹 브라우저에서 들어오는 악의적인 트래픽을 식별하고 차단하려고 합니다. |
| **Symantec ProxySG 솔루션** | Symantec은 웹 활동을 대상으로 하는 고급 위협으로부터 통신을 보호하도록 설계된 확장성 있는 고성능 웹 프록시 어플라이언스로 조직을 보호합니다. [Symantec Secure Web Gateway](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) 솔루션은 조직이 트래픽을 효과적으로 모니터링, 제어 및 보호하여 안전한 웹 및 클라우드 환경을 보장할 수 있는 고유한 프록시 서버 아키텍처를 기반으로 합니다. |
| **TitaniumCloud 파일 보강 솔루션** | [ReversingLabs TitaniumCloud](https://www.reversinglabs.com/products/file-reputation-service)는 최신 파일 평판 서비스, 위협 분류, 100억 개 이상의 "굿웨어" 및 맬웨어 파일에 대한 풍부한 컨텍스트를 제공하는 위협 인텔리전스 솔루션입니다. 파일은 ReversingLabs의 파일 분해 기술을 사용하여 처리됩니다. 강력한 REST API 쿼리 및 피드 기능 집합은 위협 식별, 분석, 인텔리전스 개발 및 위협 헌팅 서비스를 위한 대상 파일 및 맬웨어 인텔리전스를 제공합니다. |
|**Trend Micro Apex One 솔루션** |[Trend Micro Apex One](https://www.trendmicro.com/en_us/business/products/user-protection/sps/endpoint.html) 보호 기능은 파일리스 및 랜섬웨어를 비롯한 다양한 위협에 대해 고급 자동화 위협 탐지 및 응답을 제공합니다. 여러 세대의 최신 기술이 혼합되어 성능과 효율성을 극대화하는 잘 조정된 엔드포인트 보호 효과를 제공합니다. |
| **Ubiquiti UniFi 솔루션** | [Ubiquiti Inc.](https://www.ui.com/)는 여러 브랜드 이름으로 기업과 가정을 위한 무선 데이터 통신 및 유선 제품을 제조 및 판매하는 미국의 기술 회사입니다. |
| **vArmour 애플리케이션 컨트롤러 및 Azure Sentinel 솔루션** | [vArmour Application Controller](https://www.varmour.com/)는 엔터프라이즈를 시각화하고 보호하는 혁신적인 방법인 애플리케이션 관계 관리를 위한 업계 최고의 솔루션입니다. Azure Sentinel과 함께 사용하면 두 가지가 원활하게 통합되어 향상된 가시성과 자동화된 보안 작업을 제공합니다. 디지털 우선 비즈니스는 하이브리드 환경에서 사용자와 애플리케이션 간의 수백만 개의 동적 상호 연결을 기반으로 합니다. 이러한 상호 연결의 대부분은 오늘날 볼 수 없습니다. 환경 복잡성이 증가함에 따라 조직에 대한 위험도 증가합니다. 애플리케이션 컨트롤러는 애플리케이션 관계와 종속성에 대한 포괄적인 실시간 가시성과 제어를 제공하는 배포하기 쉬운 솔루션이므로 운영 의사 결정을 개선하고, 보안 상태를 강화하고, 비용이 많이 드는 새 에이전트 또는 인프라를 추가하지 않고도 다중 클라우드 배포에 대한 비즈니스 위험을 줄일 수 있습니다. 따라서 애플리케이션은 더 탄력적이고 안전합니다. |
| **VMware Carbon Black 솔루션** | [VMware Carbon Black](https://www.carbonblack.com/products/vmware-carbon-black-cloud-endpoint/)은 사용자의 요구에 맞는 클라우드 네이티브 엔드포인트 보호를 통해 보안을 혁신합니다. VMware Carbon Black은 악의적인 공격을 숨기고 이에 대응하여 예방 조치를 조정하는 사소한 변동을 발견하는 데 도움이 되는 엔드포인트 플랫폼을 제공합니다. |
| | |

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 솔루션과 이를 찾아 배포하는 방법에 대해 알아봅니다.

- [Azure Sentinel 솔루션](sentinel-solutions.md)에 대해 자세히 알아봅니다.
- [Azure Sentinel 솔루션을 찾아 배포합니다](sentinel-solutions-deploy.md).
