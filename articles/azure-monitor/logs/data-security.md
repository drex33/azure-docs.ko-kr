---
title: Azure Monitor 로그 데이터 보안 | Microsoft Docs
description: Azure Monitor 로그가 개인 정보를 보호 하 고 데이터를 보호 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: 00f083dd5b8ed056f31d268017e56a571fb3b913
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434271"
---
# <a name="azure-monitor-logs-data-security"></a>Azure Monitor 로그 데이터 보안
이 문서에서는 [Azure 보안 센터](https://www.microsoft.com/en-us/trust-center?rtc=1)의 정보를 보충하기 위해 [Azure Monitor 로그](../logs/data-platform-logs.md)와 관련된 정보를 제공합니다.  

이 문서에서는 Azure Monitor가 로그 데이터를 수집, 처리 및 보호하는 방법을 설명합니다. 에이전트를 사용해서 웹 서비스에 연결하거나, System Center Operations Manager를 사용하여 작동 데이터를 수집하거나, Azure 진단에서 Azure Monitor에 사용할 데이터를 검색할 수 있습니다. 

Azure Monitor 로그는 다음 방법을 사용하여 클라우드 기반 데이터를 안전하게 관리합니다.

* 데이터 분리
* 데이터 보존
* 물리적 보안
* 인시던트 관리
* 규정 준수
* 보안 표준 인증

또한 Azure Monitor에 기본 제공되는 추가 보안 기능을 사용할 수 있습니다. 이러한 기능에는 더 많은 관리자 관리가 필요합니다. 
* 고객 관리형(보안) 키
* Azure Private Storage
* Private Link 네트워킹 
* Azure Lockbox에서 설정하는 Azure 지원 액세스 제한

보안 정책을 포함하여 다음 정보와 관련된 질문, 제안 사항 또는 문제가 있을 경우 [Azure 지원 옵션](https://azure.microsoft.com/support/options/)에서 문의하세요.

## <a name="sending-data-securely-using-tls-12"></a>TLS 1.2를 사용하여 안전하게 데이터 전송 

Azure Monitor로 전송 중인 데이터를 보호하려면 적어도 TLS(전송 계층 보안) 1.2 이상을 사용하도록 에이전트를 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)이 취약한 것으로 나타났습니다. 따라서 현재 이전 버전과 호환성을 허용하기 위해 작동하지만 **사용하지 않는 것이 좋으며** 업계는 이러한 이전 프로토콜에 대한 지원을 중단하도록 빠르게 변화하고 있습니다. 

[PCI 보안 표준 위원회](https://www.pcisecuritystandards.org/)는 이전 버전의 TLS/SSL를 사용하지 않고 추가 보안 프로토콜을 업그레이드하는 [최종 기한인 2018년 6월 30일](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf)을 설정했습니다. Azure가 레거시 지원을 삭제하면 에이전트가 적어도 TLS 1.2를 통해 통신할 수 없는 경우 Azure Monitor 로그에 데이터를 보낼 수 없게 됩니다. 

반드시 필요한 경우가 아니면 TLS 1.2만 사용하도록 에이전트를 명시적으로 설정하지 않는 것이 좋습니다. 에이전트가 이후 보안 표준을 자동으로 감지, 협상 및 활용할 수 있도록 허용하는 것이 좋습니다. 그렇지 않으면 새로운 표준의 추가된 보안 기능을 이용하지 못할 수 있고 TLS 1.2가 새 표준으로 대체되었을 때 문제가 발생할 수 있습니다.    

### <a name="platform-specific-guidance"></a>플랫폼별 지침

|플랫폼/언어 | 지원 | 추가 정보 |
| --- | --- | --- |
|Linux | Linux 배포판은 TLS 1.2 지원에 대해 [OpenSSL](https://www.openssl.org)을 사용하는 경향이 있습니다.  | [OpenSSL Changelog](https://www.openssl.org/news/changelog.html)를 확인하여 OpenSSL 버전이 지원되는지 확인합니다.|
| Windows 8.0 - 10 | 지원됨, 기본적으로 활성화됩니다. | [기본 설정](/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면  |
| Windows Server 2012 - 2016 | 지원되며 기본적으로 사용하도록 설정됩니다. | [기본 설정](/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면 |
| Windows 7 SP1 및 Windows Server 2008 R2 SP1 | 지원되지만 기본적으로 사용하도록 설정되지 않습니다. | 활성화하는 방법에 대한 자세한 내용은 [TLS(전송 계층 보안) 레지스트리 설정](/windows-server/security/tls/tls-registry-settings) 페이지를 참조하세요.  |

## <a name="data-segregation"></a>데이터 분리
Azure Monitor에서 수집된 이후 데이터는 서비스 전체의 각 구성 요소에서 논리적으로 구분된 상태로 유지됩니다. 모든 데이터에는 작업 영역별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다. 사용자의 데이터는 사용자가 선택한 영역의 스토리지 클러스터에 있는 전용 데이터베이스에 저장됩니다.

## <a name="data-retention"></a>데이터 보존
인덱싱된 로그 검색 데이터가 저장되고 가격 계획에 따라 유지됩니다. 자세한 내용은 [Log Analytics 가격](https://azure.microsoft.com/pricing/details/log-analytics/)을 참조하세요.

[구독 계약](https://azure.microsoft.com/support/legal/subscription-agreement/)의 일부로 Microsoft는 계약 조건에 따라 데이터를 유지합니다.  고객 데이터가 제거될 때 물리적 드라이브는 소멸되지 않습니다.  

다음 표에는 사용할 수 있는 솔루션 및 각 솔루션이 수집하는 데이터 형식의 예가 나와 있습니다.

| **해결 방법** | **데이터 형식** |
| --- | --- |
| 용량 및 성능 |성능 데이터 및 메타데이터 |
| 업데이트 관리 |메타데이터 및 상태 데이터 |
| 로그 관리 |사용자 정의 이벤트 로그, Windows 이벤트 로그 및/또는 IIS 로그 |
| 변경 내용 추적 |소프트웨어 인벤토리, Windows 서비스 및 Linux 디먼 메타데이터 및 Windows/Linux 파일 메타데이터 |
| SQL 및 Active Directory 평가 |WMI 데이터, 레지스트리 데이터, 성능 데이터 및 SQL Server 동적 관리 보기 결과 |

다음 표에는 데이터 형식의 예가 나와 있습니다.

| **데이터 형식** | **필드** |
| --- | --- |
| 경고 |Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| 구성 |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| 이벤트 |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**참고:** 사용자 지정 필드가 있는 이벤트를 Windows 이벤트 로그에 기록하면 Log Analytics에서 해당 이벤트를 수집합니다. |
| 메타데이터 |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| 성능 |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| 시스템 상태 |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>물리적 보안
Azure Monitor는 Microsoft 담당자가 관리하며 모든 활동을 기록하여 감사할 수 있습니다. Azure Monitor는 Azure 서비스로 작동하며 모든 Azure 준수 및 보안 요구 사항을 충족합니다. Azure 자산의 물리적 보안에 대한 자세한 내용은 [Microsoft Azure 보안 개요](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)의 18페이지에서 확인할 수 있습니다. 더 이상 Azure Monitor 서비스에 대한 전송, 종료 등의 책임이 없는 사용자는 영업일 기준 1일 이내에 보안 영역에 대한 물리적 액세스 권한이 변경됩니다. [Microsoft 데이터 센터](https://azure.microsoft.com/global-infrastructure/)에서 사용하는 글로벌 물리적 인프라에 대해 읽을 수 있습니다.

## <a name="incident-management"></a>인시던트 관리
Azure Monitor에는 모든 Microsoft 서비스가 준수하는 인시던트 관리 프로세스가 있습니다. 요약하면 Microsoft는

* Microsoft가 보안의 일부를 책임지고 고객이 일부를 책임지는 공유 책임 모델을 사용합니다.
* Azure 보안 인시던트를 관리합니다.
  * 인시던트를 감지하면 조사를 시작합니다.
  * 대기 인시던트 대응 팀원이 인시던트의 영향과 심각도를 평가합니다. 평가 후 증거를 기준으로 보안 대응 팀의 추가 에스컬레이션 여부를 결정합니다.
  * 보안 대응 전문가가 인시던트를 진단하여 기술적 또는 범죄 조사를 실시하고 제약, 완화, 해결 방법 전략을 식별합니다. 보안 팀이 고객 데이터가 불법적 또는 권한 없는 개인에게 노출된 것으로 판단할 경우 이와 동시에 고객 인시던트 알림 프로세스를 실시합니다.  
  * 상황을 안정시키고 복구합니다. 인시던트 대응 팀이 문제를 완화하기 위한 복구 계획을 만듭니다. 진단과 함께 영향을 받은 시스템을 격리하는 등의 위기 억제 단계를 즉시 및 동시에 실시합니다. 즉각적인 위기가 지나간 후에 실시할 장기적 완화 계획을 세울 수 있습니다.  
  * 인시던트를 종결하고 사후 분석을 실시합니다. 인시던트 대응 팀이 이벤트의 재발을 방지하기 위한 정책, 절차, 프로세스를 수정하기 위해 인시던트 상세 정보를 요약한 사후 분석을 만듭니다.
* 고객에게 보안 인시던트를 알립니다.
  * 영향을 받은 고객의 범위를 확인하고 영향을 받은 고객에게 가능한 자세한 공지를 제공합니다.
  * 공지 프로세스를 지나치게 지연하지 않으면서 고객 측에서 조사를 실시하고 고객이 최종 사용자에게 한 약속을 지킬 수 있을 만큼 충분한 정보를 제공하는 공지를 작성합니다.
  * 필요에 따라 인시던트를 확인 및 선언합니다.
  * 불합리적인 지연 없이 법적 또는 계약적 의무에 따라 고객에게 인시턴트 공지를 전달합니다. 보안 인시던트 공지는 Microsoft가 선택하는 수단(전자 메일 포함)을 통해 한 명 이상의 고객의 관리자에게 배달됩니다.
* 팀 준비 및 교육을 실시합니다.
  * Microsoft 담당자는 의심스러운 보안 문제를 식별 및 보고할 수 있도록 완전한 보안 및 인식 교육을 이수해야 합니다.  
  * Microsoft Azure 서비스 운용 담당자는 고객 데이터를 호스팅하고 있는 민감한 시스템에 대한 액세스 권한과 관련하여 추가 교육을 이수할 의무가 있습니다.
  * Microsoft 보안 대응 담당자는 자신의 역할에 관한 전문 교육을 받습니다.

매우 드물지만 Microsoft는 고객 데이터의 중대한 손실이 발생하는 경우 1일 내에 각 고객에게 알립니다. 

Microsoft가 보안 인시던트에 대응하는 방법에 대한 자세한 내용은 [클라우드에서 Microsoft Azure의 보안 대응](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf)을 참조하세요.

## <a name="compliance"></a>규정 준수
Azure Monitor 소프트웨어 개발 및 서비스 팀의 정보 보안 및 거버넌스 프로그램은 비즈니스 요구 사항을 지원하며 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/) 및 [Microsoft 보안 센터 규정 준수](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)에 설명된 법률 및 규정을 준수합니다. 여기에는 Azure Monitor가 보안 요구 사항을 정하고 보안 컨트롤을 식별하며 위험을 관리 및 모니터링하는 방식도 설명되어 있습니다. 정책, 표준, 절차, 지침을 매년 검토합니다.

각 개발 팀원은 공식적인 애플리케이션 보안 교육을 이수합니다. 내부적으로는 소프트웨어 개발에 대한 버전 관리 시스템을 사용합니다. 각 소프트웨어 프로젝트는 버전 관리 시스템으로 보호됩니다.

Microsoft에는 Microsoft의 모든 서비스를 감독 및 평가하는 보안 및 규정 준수 팀이 있습니다. 이 팀은 정보 보안 책임자로 구성되어 있으며 Log Analytics를 개발하는 엔지니어링 팀과 독립적입니다. 보안 책임자는 자체 관리 체임이 있으며 제품과 서비스에 대한 독립적 평가를 실시하여 보안과 규정 준수를 보장합니다.

Microsoft의 이사회는 Microsoft의 모든 정보 보안 프로그램에 대한 연간 보고서를 받아 봅니다.

Log Analytics 소프트웨어 개발 및 서비스 팀은 다양한 인증을 받기 위해 Microsoft 법률 및 규정 준수 팀은 물론 다른 업계 파트너와도 적극적으로 협력하고 있습니다.

## <a name="certifications-and-attestations"></a>인증 및 증명
Azure Log Analytics는 다음 요구 사항을 충족합니다.

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* PCI Security Standards Council의 [Payment Card Industry(PCI 규격) Data Security Standard(PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI).
* [SOC(Service Organization Controls) 1 Type 1 및 SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) 규정 준수
* HIPAA BAA(Business Associate Agreement)를 소유하는 회사에 대한 [HIPAA 및 HITECH](/compliance/regulatory/offering-hipaa-hitech)
* Windows Common Engineering Criteria
* Microsoft 신뢰할 수 있는 컴퓨팅
* Azure Monitor에 사용되는 구성 요소는 Azure 서비스로서 Azure 규정 준수 요구 사항을 준수합니다. 자세한 내용은 [Microsoft 보안 센터 규정 준수](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)를 참조하세요.

> [!NOTE]
> 일부 인증/증명의 경우 Azure Monitor 로그가 *Operational Insights* 의 이전 이름으로 나열됩니다.
>
>

## <a name="cloud-computing-security-data-flow"></a>클라우드 컴퓨팅 보안 데이터 흐름
다음 다이어그램에서는 회사의 정보 흐름 및 궁극적으로 Azure Portal에서 볼 수 있도록 Azure Monitor로 이동할 때 보안이 유지되는 방식으로 클라우드 보안 아키텍처를 보여 줍니다. 각 단계에 대한 자세한 내용은 다이어그램 뒤에 나옵니다.

![Azure Monitor 로그 데이터 수집 및 보안 이미지](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-azure-monitor-and-collect-data"></a>1. Azure Monitor에 등록하여 데이터 수집
조직에서 Azure Monitor로그에 데이터를 보내도록 하려면 Azure 가상 머신에서 실행되거나 사용자 환경 또는 다른 클라우드 공급자의 가상 또는 물리적 컴퓨터에서 실행되는 Windows 또는 Linux 에이전트를 구성합니다.  Operations Manager를 사용하는 경우 관리 그룹에서 Operations Manager 에이전트를 구성합니다. 사용자(이 문서의 독자, 다른 개별 사용자 또는 사용자 그룹)는 하나 이상의 Log Analytics 작업 영역을 만들고 다음 계정 중 하나를 사용하여 에이전트를 등록합니다.

* [조직 ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft 계정 - Outlook, Office Live, MSN](https://account.microsoft.com/account)

Log Analytics 작업 영역은 데이터를 수집, 집계, 분석 및 제공하는 데 사용됩니다. 작업 영역은 주로 데이터를 분할하는 데 사용되며, 각 작업 영역은 고유합니다. 예를 들어 하나의 작업 영역을 사용하여 프로덕션 데이터를 관리하고 다른 작업 영역을 사용하여 테스트 데이터를 관리할 수 있습니다. 관리자는 작업 영역을 사용하여 데이터에 대한 사용자 액세스를 제어할 수 있습니다. 각 작업 영역에는 여러 사용자 계정이 연결될 수 있으며, 각 사용자 계정은 여러 Log Analytics 작업 영역에 액세스할 수 있습니다. 데이터 센터 영역을 기준으로 작업 영역을 만듭니다.

Operations Manager의 경우 Operations Manager 관리 그룹이 Azure Monitor 서비스와의 연결을 설정합니다. 그런 다음, 관리 그룹에서 데이터를 수집해 서비스로 보낼 수 있는 에이전트 관리 대상 시스템을 구성합니다. 활성화한 솔루션에 따라 이러한 솔루션의 데이터는 Operations Manager 관리 서버에서 Azure Monitor 서비스로 직접 전송되거나 에이전트 관리 시스템에서 수집된 데이터의 양으로 인해 에이전트에서 해당 서비스로 직접 전송됩니다. Operations Manager로 모니터링되지 않는 시스템의 경우 각 시스템이 Azure Monitor 서비스에 직접 안전하게 연결합니다.

연결된 시스템과 Azure Monitor 서비스 간의 모든 통신은 암호화됩니다. TLS(HTTPS) 프로토콜은 암호화에 사용됩니다.  Log Analytics에 최신 암호화 프로토콜을 적용할 수 있도록 Microsoft SDL 프로세스를 준수합니다.

각 에이전트 유형이 Azure Monitor에 대한 로그 데이터를 수집합니다. 수집되는 데이터 형식은 작업 영역 구성 및 Azure Monitor의 기타 기능에 따라 달라집니다. 

## <a name="2-send-data-from-agents"></a>2. 에이전트에서 데이터 보내기
등록 키를 사용해서 모든 에이전트 유형을 등록하고 인증서 기반 인증과 포트 443을 통한 TLS를 사용하여 에이전트와 Azure Monitor 서비스 간에 보안 연결이 설정됩니다. Azure Monitor는 비밀 저장소를 사용하여 키를 생성하고 유지 관리합니다. 프라이빗 키는 90일마다 회전되어 Azure에 저장되며 Azure 운영팀에서 엄격한 규정 및 규정 준수 방식을 따라 관리합니다.

Operations Manager를 사용하면 Log Analytics 작업 영역에 등록된 관리 그룹이 Operations Manager 관리 서버와의 안전한 HTTPS 연결을 설정합니다.

Azure 가상 머신에서 실행되는 Windows 또는 Linux 에이전트의 경우 Azure 테이블에서 진단 이벤트를 읽는 데 읽기 전용 스토리지 키가 사용됩니다.  

Azure Monitor와 통합된 Operations Manager 관리 그룹에 보고하는 모든 에이전트에서 어떤 이유로든 관리 서버가 서비스와 통신할 수 없으면 수집된 데이터가 관리 서버의 임시 캐시에 로컬로 저장됩니다.   2시간 동안 8분마다 데이터를 재전송하려고 합니다.  관리 서버를 무시하고 Azure Monitor로 직접 전송되는 데이터의 경우에는 동작이 Windows 에이전트와 일치합니다.  

Windows 또는 관리 서버 에이전트에서 캐시한 데이터는 운영 체제의 자격 증명 저장소에 의해 보호됩니다. 2시간 후 서비스가 데이터를 처리할 수 없을 경우 에이전트는 데이터를 큐에 추가합니다. 큐가 꽉 차면 에이전트는 성능 데이터부터 시작하여 데이터 형식을 삭제하기 시작합니다. 에이전트 큐 한계는 레지스트리 키이므로 필요에 따라 수정할 수 있습니다. 수집된 데이터는 압축되어 Operations Manager 관리 그룹 데이터베이스를 무시하고 서비스로 전송되므로 로드가 추가되지 않습니다. 수집된 데이터는 전송된 후 캐시에서 제거됩니다.

위에서 설명한 바와 같이 관리 서버 또는 직접 연결된 에이전트의 데이터는 TLS를 통해 Microsoft Azure 데이터 센터로 전송됩니다. 또는 ExpressRoute를 사용하여 데이터에 대한 추가 보안을 제공할 수 있습니다. ExpressRoute는 네트워크 서비스 공급자가 제공하는 MPLS(multi-protocol label switching) VPN과 같은 기존 WAN 네트워크에서 Azure에 직접 연결하는 방법입니다. 자세한 내용은 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 참조하세요.

## <a name="3-the-azure-monitor-service-receives-and-processes-data"></a>3. Azure Monitor 서비스에서 데이터를 받아서 처리
Azure Monitor 서비스는 Azure 인증을 통해 인증서 및 데이터 무결성의 유효성을 검사하여 들어오는 데이터가 신뢰할 수 있는 출처로부터 온 것인지 확인합니다. 처리되지 않은 원시 데이터는 데이터가 미사용 시 저장될 지역의 Azure Event Hub에 저장됩니다. 저장되는 데이터 형식은 데이터를 수집하기 위해 가져와 사용하는 솔루션 유형에 따라 다릅니다. 그런 다음, Azure Monitor 서비스가 원시 데이터를 처리하고 이를 데이터베이스에 수집합니다.

데이터베이스에 저장된 수집 데이터의 보존 기간은 선택한 가격 책정 계획에 따라 다릅니다. *체험* 계층의 경우 수집된 데이터는 7일 동안 사용할 수 있습니다. *유료* 계층의 경우 수집된 데이터는 기본적으로 31일 동안 사용할 수 있지만 730일까지 사용할 수 있도록 연장 가능합니다. 데이터는 Azure Storage에 암호화되어 데이터 기밀성을 보장하며 로컬 중복 스토리지(LRS)를 사용하여 로컬 영역 내에 데이터가 복제됩니다. 지난 2주의 데이터는 SSD 기반 캐시에도 저장되며 이 캐시는 암호화됩니다.

수집한 후에는 데이터베이스 스토리지의 데이터를 변경할 수 없지만 [*제거* API 경로](personal-data-mgmt.md#delete)를 통해 삭제할 수는 있습니다. 데이터를 변경할 수 없지만, 일부 인증 시 데이터를 변경할 수 없는 상태로 유지하고 스토리지에서 변경하거나 삭제할 수 없도록 요구할 수 있습니다. 데이터 불변성은 [변경 불가능한 스토리지](../../storage/blobs/immutable-policy-configure-version-scope.md)로 구성된 스토리지 계정에 [데이터 내보내기](logs-data-export.md)를 사용하여 달성할 수 있습니다.

## <a name="4-use-azure-monitor-to-access-the-data"></a>4. Azure Monitor를 사용하여 데이터 액세스
Log Analytics 작업 영역에 액세스하려면 이전에 설정한 Microsoft 계정 또는 조직 계정을 사용하여 Azure Portal에 로그인합니다. 포털과 Azure Monitor 서비스 간의 모든 트래픽은 보안 HTTPS 채널을 통해 전송됩니다. 포털을 사용할 때는 사용자 클라이언트(웹 브라우저)에 세션 ID가 생성되며 세션이 종료될 때까지 데이터가 로컬 캐시에 저장됩니다. 세션이 종료되면 캐시가 삭제됩니다. 개인 식별이 가능한 정보가 포함되지 않는 클라이언트 측 쿠키는 자동으로 제거되지 않습니다. 세션 쿠키는 HTTPOnly로 표시되며 보안됩니다. 사전 지정한 유휴 기간이 지나면 Azure Portal 세션이 종료됩니다.


## <a name="additional-security-features"></a>추가 보안 기능
이러한 추가 보안 기능을 사용하여 Azure Monitor 환경을 더욱 안전하게 보호할 수 있습니다. 이러한 기능에는 더 많은 관리자 관리가 필요합니다. 
- [고객 관리형(보안) 키](../logs/customer-managed-keys.md) - 고객 관리형 키를 사용하여 Log Analytics 작업 영역으로 전송되는 데이터를 암호화할 수 있습니다. Azure Key Vault를 사용해야 합니다. 
- [프라이빗/고객 관리형 스토리지](./private-storage.md) - 개인적으로 암호화된 스토리지 계정을 관리하고 이를 사용해서 모니터링 데이터를 저장하도록 Azure Monitor에 지시합니다. 
- [Private Link 네트워킹](./private-link-security.md) - Azure Private Link를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스(Azure Monitor 포함)를 가상 네트워크에 안전하게 연결할 수 있습니다. 
- [Azure 고객 Lockbox](../../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-preview) - Microsoft Azure에 대한 고객 Lockbox는 고객이 고객 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다. 지원 요청 시 Microsoft 엔지니어가 고객 데이터에 액세스해야 하는 경우에 사용됩니다.


## <a name="next-steps"></a>다음 단계
* [Azure Monitor에서 수집할 수 있는 여러 종류의 데이터를 참조하세요](../monitor-reference.md).
