---
title: Azure Sentinel용 파트너 데이터 커넥터 | Microsoft Docs
description: Azure Sentinel용 모든 파트너 데이터 커넥터에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 25b09b496ff505bb37452b80355cae705af8936d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528179"
---
# <a name="azure-sentinel-partner-data-connectors"></a>Azure Sentinel 파트너 데이터 커넥터

이 문서에는 Azure Sentinel이 타사 파트너 조직에 대해 지원하는 데이터 커넥터가 알파벳 순서로 나열되어 있습니다. 찾고 있는 커넥터가 있으면, 이 페이지에서 해당 이름까지 스크롤하거나 해당 이름을 검색하거나 문서의 오른쪽 또는 상단에 있는 링크를 사용하세요.

- 나열된 커넥터를 사용하도록 설정하고 구성하려면 [데이터 원본 연결](connect-data-sources.md) 및 개별 목록의 링크를 참조하세요.
- Microsoft 작성 및 서비스 간 데이터 커넥터에 대한 자세한 내용은 [서비스 간 통합](connect-data-sources.md#service-to-service-integration)을 참조하세요.
- Azure Sentinel 지원 모델 및 **지원 제공** 필드에 대한 자세한 내용은 [데이터 커넥터 지원](connect-data-sources.md#data-connector-support)을 참조하세요.

> [!IMPORTANT]
> 다음 Azure Sentinel 파트너 데이터 커넥터 중 다수는 현재 **미리 보기** 상태입니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense 및 Brand Protection(미리 보기)

Agari Phishing Defense 및 Brand Protection 커넥터는 Brand Protection 및 Phishing Defense 솔루션 로그를 Azure Sentinel에 연결합니다.

자세한 내용은 [Agari 개발자 사이트](https://developers.agari.com/agari-platform) 및 [Agari Phishing Defense 및 Brand Protection을 Azure Sentinel에 연결](connect-agari-phishing-defense.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support)

## <a name="ai-analyst-aia-by-darktrace-preview"></a>Darktrace의 AIA(AI Analyst)(미리 보기)

Darktrace 커넥터는 모델 위반 및 AIA 인시던트를 Azure Sentinel에 보냅니다. Darktrace AIA 통합 문서의 시각화를 통해 대화식으로 데이터를 살펴봅니다. 여기에는 시간 브러싱이 있는 개요 그래프가 포함되어 있습니다. 특정 위반 및 인시던트에 대한 자세한 드릴다운을 확인한 다음, Darktrace UI에서 인시던트를 확인하고 추가로 살펴볼 수 있습니다.

AIA는 Azure Log Analytics의 Azure Sentinel CommonSecurityLog 테이블에 위반 데이터를 보냅니다. CommonSecurityLog 테이블은 다양한 파트너 커넥터에서 간편한 쿼리 데이터 정렬 및 집계를 지원합니다.

**데이터 수집 방법:** Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)

**지원 제공:** [Darktrace](https://customerportal.darktrace.com/)

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect(미리 보기)

AI Vectra Detect 데이터 커넥터는 AI Vectra Detect 데이터를 Azure Sentinel로 가져옵니다. AI Vectra 통합 문서를 통해 Sentinel에서 직접 네트워크 공격을 조사하기 시작할 수 있습니다. 중요한 호스트, 계정, 캠페인 및 탐지를 살펴보고 Vectra의 시스템 상태 및 감사 로그를 모니터링할 수 있습니다.

Azure Sentinel 연결에 대한 자세한 내용은 [AI Vectra Detect를 Azure Sentinel에 연결](connect-ai-vectra-detect.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Vectra](https://www.vectra.ai/support)

## <a name="akamai-security-events-preview"></a>Akamai Security Events(미리 보기)

Akamai Security Events 데이터 커넥터는 [Akamai Security Events](https://www.akamai.com/us/en/products/security/)를 Azure Sentinel에 수집합니다. 자세한 내용은 [Splunk 및 CEF Syslog용 Akamai SIEM 통합](https://developer.akamai.com/tools/integrations/siem)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Akamai Security Events를 Azure Sentinel에 연결](connect-akamai-security-events.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** [Akamai](https://www.akamai.com/us/en/support/)

## <a name="alcide-kaudit-preview"></a>Alcide kAudit(미리 보기)

Alcide kAudit 커넥터는 Kubernetes 클러스터 감사 로그를 실시간으로 Azure Sentinel에 자동으로 내보냅니다. kAudit 커넥터는 Kubernetes 감사 로그에 대한 향상된 가시성과 관측성을 제공합니다. Alcide kAudit은 포렌식 용도로 강력한 보안 및 모니터링 기능을 제공합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Alcide kAudit를 Azure Sentinel에 연결](connect-alcide-kaudit.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** [Alcide](https://www.alcide.io/company/contact-us/)

## <a name="alsid-for-active-directory-preview"></a>Alsid for Active Directory(미리 보기)

Alsid for Active Directory 커넥터는 Alsid Indicators of Exposures, 트레일 흐름 및 Indicators of Attacks 로그를 실시간으로 Azure Sentinel에 내보냅니다. 이 커넥터는 로그를 조작하는 데 유용한 데이터 구문 분석기도 제공합니다. Workbooks는 Active Directory 모니터링 및 데이터 시각화를 제공합니다. 분석 템플릿은 이벤트, 노출 및 공격에 대한 응답을 자동화하는 데 유용합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Alsid for Active Directory를 Azure Sentinel에 연결](connect-alsid-active-directory.md)을 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** [Alsid](https://www.alsid.com/contact-us/)

## <a name="amazon-web-services"></a>Amazon Web Services

AWS 데이터 커넥터는 AWS CloudTrail 관리 이벤트를 Azure Sentinel로 가져옵니다. 이 커넥터를 사용하도록 설정하려면 [Azure Sentinel을 AWS CloudTrail에 연결](connect-aws.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** Microsoft

## <a name="apache-http-server-preview"></a>Apache HTTP Server(미리 보기)

Apache HTTP Server 데이터 커넥터는 Apache HTTP Server 이벤트를 Azure Sentinel에 수집합니다. 자세한 내용은 [Apache 로그 설명서](https://httpd.apache.org/docs/2.4/logs.html)를 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="apache-tomcat-preview"></a>Apache Tomcat(미리 보기)

Apache Tomcat 데이터 커넥터는 [Apache Tomcat](http://tomcat.apache.org/) 이벤트를 Azure Sentinel에 수집합니다. 자세한 내용은 [Apache Tomcat 설명서](http://tomcat.apache.org/tomcat-10.0-doc/logging.html)를 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass(미리 보기)

Aruba ClearPass 커넥터는 Aruba ClearPass 감사, 세션, 시스템 및 인사이트 로그를 Azure Sentinel에 연결합니다. syslog를 전달하도록 Aruba ClearPass 솔루션을 구성하는 방법에 대한 자세한 내용은 [Adding a Syslog Export Filter](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Aruba ClearPass를 Azure Sentinel에 연결](connect-aruba-clearpass.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit(미리 보기)

[Atlassian Confluence](https://www.atlassian.com/software/confluence) Audit 데이터 커넥터는 Confluence Audit 레코드를 수집합니다. 이 커넥터는 잠재적인 보안 위험을 검사하고, 팀의 협업을 분석하고, 구성 문제를 진단하는 등의 이벤트를 가져올 수 있습니다. 자세한 내용은 [View the audit log](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/)를 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit(미리 보기)

Atlassian Jira Audit 데이터 커넥터는 Jira Audit Records 이벤트를 Azure Sentinel에 수집합니다. 이 커넥터는 잠재적인 보안 위험을 검사하고, 팀의 협업을 분석하고, 구성 문제를 진단하는 등의 이벤트를 가져올 수 있습니다. 자세한 내용은 [Audit records](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)를 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="barracuda-cloudgen-firewall-cgfw"></a>Barracuda CGFW(CloudGen Firewall)

Barracuda CGFW 커넥터는 Barracuda CGFW 로그를 Azure Sentinel에 연결합니다. Barracuda CGFW에 대한 syslog 스트리밍을 구성하려면 [How to Configure Syslog Streaming](https://aka.ms/sentinel-barracudacloudfirewall-connector)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Barracuda CloudGen Firewall을 Azure Sentinel에 연결](connect-barracuda-cloudgen-firewall.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** [Barracuda](https://www.barracuda.com/support)

## <a name="barracuda-web-application-firewall-waf"></a>Barracuda WAF(Web Application Firewall)

Barracuda WAF 커넥터는 Barracuda WAF 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Configure the Barracuda Web Application Firewall](https://aka.ms/asi-barracuda-connector)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Barracuda WAF를 Azure Sentinel에 연결](connect-barracuda.md)을 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs)

**지원 제공:** [Barracuda](https://www.barracuda.com/support)

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER MTD(Mobile Threat Defense)(미리 보기)

BETTER MTD 커넥터를 통해 기업은 Better MTD 인스턴스를 Azure Sentinel에 연결할 수 있습니다. 이 커넥터는 조직의 모바일 디바이스 및 현재 모바일 보안 상태에 대한 인사이트를 제공하여 전반적인 SecOps 기능을 개선합니다. 자세한 내용은 BETTER Mobile [Azure Sentinel 설치 설명서](https://mtd-docs.bmobi.net/integrations/how-to-setup-azure-sentinel-integration#mtd-integration-configuration)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [BETTER Mobile Threat Defense를 Azure Sentinel에 연결](connect-better-mtd.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** BETTER Mobile

## <a name="beyond-security-besecure-preview"></a>Beyond Security beSECURE(미리 보기)

Beyond Security beSECURE 커넥터는 Beyond Security beSECURE 검사 이벤트, 검사 결과 및 감사 추적을 Azure Sentinel에 연결합니다. 자세한 내용은 beSECURE [개요](https://beyondsecurity.com/solutions/besecure.html) 페이지를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Beyond Security beSECURE를 Azure Sentinel에 연결](connect-besecure.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Beyond Security](https://beyondsecurity.freshdesk.com/support/home)

## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT(미리 보기)

Blackberry CylancePROTECT 커넥터는 CylancePROTECT 감사, 위협, 애플리케이션 컨트롤, 디바이스, 메모리 보호 및 위협 분류 로그를 Azure Sentinel에 연결합니다. syslog를 전달하도록 CylancePROTECT를 구성하려면 [Cylance Syslog Guide](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf)를 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec DLP(Data Loss Prevention)(미리 보기)

Broadcom Symantec DLP 커넥터는 Symantec DLP 정책/응답 규칙 트리거를 Azure Sentinel에 연결합니다. 이 커넥터를 사용하면 조사에 도움이 되는 사용자 지정 대시보드 및 경고를 만들 수 있습니다. syslog를 전달하도록 Symantec DLP를 구성하려면 [Configuring the Log to a Syslog Server action](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Broadcom Symantec DLP를 Azure Sentinel에 연결](connect-broadcom-symantec-dlp.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="check-point"></a>Check Point

Check Point 방화벽 커넥터는 Check Point 로그를 Azure Sentinel에 연결합니다. 로그를 내보내도록 Check Point 제품을 구성하려면 [Log Exporter - Check Point Log Export](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Check Point를 Azure Sentinel에 연결](connect-checkpoint.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Check Point](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.checkpoint.com%2Fsupport-services%2Fcontact-support%2F&data=04%7C01%7CNayef.Yassin%40microsoft.com%7C9965f53402ed44988a6c08d913fc51df%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637562796697084967%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=Av1vTkeYoEXzRKO%2FotZLtMMexIQI%2FIKjJGnPQsbhqmE%3D&reserved=0)

## <a name="cisco-asa"></a>Cisco ASA

Cisco ASA 방화벽 커넥터는 Cisco ASA 로그를 Azure Sentinel에 연결합니다. 연결을 설정하려면 [Cisco ASA Series CLI Configuration Guide](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Cisco ASA를 Azure Sentinel에 연결](connect-cisco.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** Microsoft

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer(미리 보기)

Cisco Firepower eStreamer 커넥터는 eStreamer 로그를 Azure Sentinel에 연결합니다. Cisco Firepower eStreamer는 Cisco Firepower NGFW 솔루션용으로 설계된 클라이언트-서버 API입니다. 자세한 내용은 [eStreamer eNcore for Sentinel Operations Guide](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html)를 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Cisco](https://www.cisco.com/c/en/us/support/index.html)

## <a name="cisco-meraki-preview"></a>Cisco Meraki(미리 보기)

Cisco Meraki 커넥터는 Cisco Meraki(MX/MR/MS) 이벤트 로그, URL, 흐름, IDS_Alerts, 보안 이벤트 및 Airmarshal 이벤트를 Azure Sentinel에 연결합니다. syslog를 전달하도록 Meraki 디바이스를 구성하려면 [Meraki Device Reporting - Syslog, SNMP, and API](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Cisco Meraki를 Azure Sentinel에 연결](connect-cisco-meraki.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco UCS(Unified Computing System)(미리 보기)

Cisco UCS 커넥터는 Cisco UCS 감사, 이벤트 및 장애 로그를 Azure Sentinel에 연결합니다. syslog를 전달하도록 Cisco UCS를 구성하려면 [Steps to Configure Syslog to a Remote Syslog Server](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Cisco UCS(Unified Computing System)를 Azure Sentinel에 연결](connect-cisco-ucs.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella(미리 보기)

Cisco Umbrella 데이터 커넥터는 Amazon S3에 저장된 Cisco Umbrella 이벤트(예: DNS, 프록시 및 IP 로그)를 Azure Sentinel에 수집합니다. Cisco Umbrella 데이터 커넥터는 Amazon S3 REST API를 사용합니다. 로깅을 설정하고 자격 증명을 얻으려면 [Logging to Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Cisco Umbrella를 Azure Sentinel에 연결](connect-cisco-umbrella.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="citrix-analytics-security"></a>Citrix Analytics(보안)

Citrix Analytics(Security) 데이터 커넥터는 Citrix Analytics(Security)의 위험한 이벤트 데이터를 Azure Sentinel에 내보냅니다. 사용자 지정 대시보드를 만들고 다른 원본의 데이터를 Citrix Analytics(Security) 데이터와 함께 분석하고 사용자 지정 Logic Apps 워크플로를 만들어서 보안 이벤트를 모니터링하고 완화할 수 있습니다. 자세한 내용은 [Microsoft Azure Sentinel integration](https://docs.citrix.com/en-us/security-analytics/getting-started-security/azure-sentinel-integration.html)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Citrix Analytics(Security)를 Azure Sentinel에 연결](connect-citrix-analytics.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Citrix](https://www.citrix.com/support/)

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix WAF(Web App Firewall)(미리 보기)

Citrix WAF 데이터 커넥터는 Citrix WAF 로그를 Azure Sentinel에 연결합니다. Citrix WAF는 웹 사이트, 앱 및 API를 비롯한 공개 자산에 대한 위협을 완화합니다.
- WAF를 구성하려면 [Support WIKI - WAF Configuration with NetScaler](https://support.citrix.com/article/CTX234174)를 참조하세요.
- CEF 로그를 구성하려면 [CEF Logging Support in the Application Firewall](https://support.citrix.com/article/CTX136146)을 참조하세요.
- 로그를 프록시에 전달하려면 [Configuring Citrix ADC appliance for audit logging](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Citrix WAF를 Azure Sentinel에 연결](connect-citrix-waf.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Citrix](https://www.citrix.com/support/)

## <a name="cognni-preview"></a>Cognni(미리 보기)

Cognni 데이터 커넥터는 Azure Sentinel에 대한 빠르고 간단한 통합을 제공합니다. Cognni를 사용하면 이전에 분류되지 않은 중요한 정보를 자체적으로 매핑하고 관련 인시던트를 탐지할 수 있습니다. Cognni를 사용하면 중요한 정보에 대한 위험을 인식하고, 인시던트의 심각성을 파악하고, 개선해야 할 세부 사항을 신속하게 조사하여 변화를 가져올 수 있습니다.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Cognni](https://cognni.ai/contact-support/)

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk EPV(Enterprise Password Vault) Events(미리 보기)

CyberArk 데이터 커넥터는 자격 증명 모음에 대해 취한 조치에 대한 CyberArk EPV XML Syslog 메시지를 수집합니다. EPV는 *Sentinel.xsl* 변환기를 통해 XML 메시지를 CEF 표준 형식으로 변환하여 사용자가 선택한 Syslog 스테이징 서버(syslog-ng, rsyslog)에 보냅니다. Syslog 스테이징 서버의 Log Analytics 에이전트는 메시지를 Log Analytics에 가져옵니다. 자세한 내용은 CyberArk 설명서의 [SIEM(보안 정보 및 이벤트 관리) 애플리케이션](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [CyberArk Enterprise Password Vault를 Azure Sentinel에 연결](connect-cyberark.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [CyberArk](https://www.cyberark.com/customer-support/)

## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs(미리 보기)

Cyberpion Security Logs 데이터 커넥터는 Cyberpion 시스템의 로그를 직접 Azure Sentinel에 수집합니다. 자세한 내용은 Cyberpion 설명서의 [Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** Cyberpion

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector(미리 보기)

ESET Enterprise Inspector 데이터 커넥터는 ESET Enterprise Inspector 버전 1.4 이상에서 제공되는 REST API를 사용하여 ESET Enterprise Inspector에서 탐지를 수집합니다. 자세한 내용은 ESET Enterprise Inspector 설명서의 [REST API](https://help.eset.com/eei/1.5/en-US/api.html)를 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** [ESET](https://support.eset.com/en)

## <a name="eset-security-management-center-smc-preview"></a>ESET SMC(Security Management Center)(미리 보기)

ESET SMC 데이터 커넥터는 ESET SMC 위협 이벤트, 감사 로그, 방화벽 이벤트 및 웹 사이트 필터를 Azure Sentinel에 수집합니다. 자세한 내용은 ESET SMC 설명서의 [Syslog server](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html)를 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs)

**지원 제공:** [ESET](https://support.eset.com/en)

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics(미리 보기)

Exabeam Advanced Analytics 데이터 커넥터는 시스템 상태, 주목할만한 세션/변칙, 고급 분석 및 작업 상태와 같은 Exabeam Advanced Analytics 이벤트를 Azure Sentinel에 수집합니다. Syslog를 통해 Exabeam Advanced Analytics에서 로그를 보내려면 [Configure Advanced Analytics system activity notifications](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08)의 지침을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

ExtraHop Reveal(x) 데이터 커넥터는 Reveal(x) 시스템을 Azure Sentinel에 연결합니다. Azure Sentinel 통합에는 ExtraHop Detection SIEM Connector가 필요합니다. Reveal(x) 시스템에 SIEM 커넥터를 설치하려면 [ExtraHop Detection SIEM Connector](https://aka.ms/asi-syslog-extrahop-forwarding)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [ExtraHop Reveal(x)을 Azure Sentinel에 연결](connect-extrahop.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [ExtraHop](https://www.extrahop.com/support/)

## <a name="f5-big-ip"></a>F5 BIG-IP 

F5 BIG-IP 데이터 F5 LTM, 시스템, ASM 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Integrating the F5 BIGIP with Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [F5 BIG-IP를 Azure Sentinel에 연결](connect-f5-big-ip.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** Microsoft

## <a name="f5-networks"></a>F5 Networks

F5 방화벽 커넥터는 F5 애플리케이션 보안 이벤트를 Azure Sentinel에 연결합니다. 원격 로깅을 설정하려면 [Configuring Application Security Event Logging](https://aka.ms/asi-syslog-f5-forwarding)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [F5 ASM을 Azure Sentinel에 연결](connect-f5.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** Microsoft

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint CASB(Cloud Access Security Broker)(미리 보기)

Forcepoint CASB 데이터 커넥터는 CASB 로그 및 이벤트를 실시간으로 Azure Sentinel에 자동으로 내보냅니다. 자세한 내용은 [Forcepoint CASB and Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Forcepoint 제품을 Azure Sentinel에 연결](connect-forcepoint-casb-ngfw.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint CSG(Cloud Security Gateway)(미리 보기)

Forcepoint CSG 데이터 커넥터는 CSG 로그를 Azure Sentinel에 자동으로 내보냅니다. CSG는 사용자와 데이터가 어디에 있든 가시성, 제어 및 위협 보호를 제공하는 수렴된 클라우드 보안 서비스입니다. 자세한 내용은 [Forcepoint Cloud Security Gateway and Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Forcepoint 제품을 Azure Sentinel에 연결](connect-forcepoint-casb-ngfw.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint DLP(Data Loss Prevention)(미리 보기)

Forcepoint DLP 데이터 커넥터는 Forcepoint DLP의 DLP 인시던트 데이터를 실시간으로 Azure Sentinel에 자동으로 내보냅니다. 자세한 내용은 [Forcepoint Data Loss Prevention and Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Forcepoint DLP를 Azure Sentinel에 연결](connect-forcepoint-dlp.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint NGFW(Next Generation Firewall)(미리 보기)

Forcepoint NGFW 데이터 커넥터는 사용자 정의 Forcepoint NGFW 로그를 실시간으로 Azure Sentinel에 자동으로 내보냅니다. 자세한 내용은 [Forcepoint Next-Gen Firewall and Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Forcepoint 제품을 Azure Sentinel에 연결](connect-forcepoint-casb-ngfw.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock CAUD(Common Audit) for CEF(미리 보기)

ForgeRock Identity Platform은 단일 공통 감사 프레임워크를 제공하기 때문에 로그 데이터를 전체적으로 추적할 수 있습니다. CAUD 이벤트 핸들러 및 고유 ID를 사용하여 전체 플랫폼에서 로그 데이터를 추출하고 집계할 수 있습니다. CAUD for CEF 커넥터는 개방형이고 확장이 가능하며 Azure Sentinel과의 통합을 위해 감사 로깅 및 보고 기능을 사용할 수 있습니다. 자세한 내용은 [Azure Sentinel의 CAUD(ForgeRock Common Audit)](https://github.com/javaservlets/SentinelAuditEventHandler)를 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [ForgeRock](https://www.forgerock.com/support)

## <a name="fortinet"></a>Fortinet 

Fortinet 방화벽 커넥터는 Fortinet 로그를 Azure Sentinel에 연결합니다. 자세한 내용을 보려면 [Fortinet Document Library](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)로 이동하여 버전을 선택하고 *Handbook* 및 *Log Message Reference* PDF를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Fortinet을 Azure Sentinel에 연결](connect-fortinet.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Fortinet](https://support.fortinet.com/)

## <a name="google-workspace-g-suite-preview"></a>Google Workspace(G-Suite)(미리 보기)

Google Workspace 데이터 커넥터는 REST API를 통해 Google Workspace 활동 이벤트를 Azure Sentinel에 수집합니다. 이벤트를 수집하는 기능은 다음을 수행하는 데 도움이 됩니다.
- 잠재적인 보안 위험을 조사
- 팀의 협업 분석
- 구성 문제 진단
- 누가 언제 로그인하는지 추적
- 관리자 활동 분석
- 사용자가 콘텐츠를 만들고 공유하는 방법 이해
- 조직 이벤트 검토

자격 증명을 얻으려면 [Perform Google Workspace Domain-Wide Delegation of Authority](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Google Workspace(이전 명칭: G Suite)를 Azure Sentinel에 연결](connect-google-workspace.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive AMS(Attack Management System)(미리 보기)

Illusive AMS 커넥터는 Illusive 공격 표면 분석 데이터 및 인시던트 로그를 Azure Sentinel에 공유합니다. 이런 정보는 전용 대시보드에서 볼 수 있습니다. ASM Workbook은 조직의 공격 표면 위험에 대한 인사이트를 제공하고 ADS Workbook은 조직의 네트워크에서 무단 측면 이동을 추적합니다. 자세한 내용은 [Illusive Networks Admin Guide](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Illusive Networks AMS를 Azure Sentinel에 연결](connect-illusive-attack-management-system.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Illusive Networks](https://www.illusivenetworks.com/technical-support/)

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway(미리 보기)

Imperva 커넥터는 Imperva WAF Gateway 경고를 Azure Sentinel에 연결합니다. 자세한 내용은 [Steps for Enabling Imperva WAF Gateway Alert Logging to Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Imperva WAF Gateway를 Azure Sentinel에 연결](connect-imperva-waf-gateway.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Imperva](https://www.imperva.com/support/technical-support/)

## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox NIOS(Network Identity Operating System)(미리 보기)

Infoblox NIOS 커넥터는 Infoblox NIOS 로그를 Azure Sentinel에 연결합니다. Infoblox NIOS 로그의 syslog 전달을 사용하도록 설정하려면 [NIOS SNMP and Syslog Deployment Guide](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Infoblox NIOS를 Azure Sentinel에 연결](connect-infoblox.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="juniper-srx-preview"></a>Juniper SRX(미리 보기)

Juniper SRX 커넥터는 Juniper SRX 로그를 Azure Sentinel에 연결합니다. 트래픽 로그를 전달하려면 [SRX 분기 디바이스에 대한 트래픽 로깅(보안 정책 로그) 구성](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)을 참조하세요. 시스템 로그를 전달하려면 [Configure System Logging](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Juniper SRX를 Azure Sentinel에 연결](connect-juniper-srx.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** [Juniper Networks](https://support.juniper.net/support/)

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP(미리 보기)

Azure Sentinel용 Morphisec 데이터 커넥터는 보안 제품의 중요한 인사이트를 통합합니다. 검색 및 상관 관계, 위협 인텔리전스, 사용자 지정된 경고를 통해 분석 기능을 확장할 수 있습니다. Morphisec 데이터 커넥터는 정교한 파일리스 공격, 메모리 내 공격, 제로 데이와 같은 지능형 위협에 대한 가시성을 제공합니다. 단일 제품 간 보기를 통해 가장 중요한 자산을 보호하기 위해 데이터를 기반으로 한 실시간 의사 결정을 내릴 수 있습니다.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Morphisec

## <a name="netskope-preview"></a>Netskope(미리 보기)

Netskope Cloud Security Platform 커넥터는 Netskop 로그 및 이벤트를 Azure Sentinel에 수집합니다. 자세한 내용은 [The Netskope Cloud Security Platform](https://www.netskope.com/platform)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server(미리 보기)

NGINX HTTP Server 데이터 커넥터는 NGINX HTTP Server 이벤트를 Azure Sentinel에 수집합니다. 자세한 내용은 [Module ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html)을 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog BSM(Basic Security Module) macOS(미리 보기)

NXLog BSM macOS 데이터 커넥터는 Sun BSM Auditing API를 사용하여 macOS 플랫폼의 커널에서 직접 감사 이벤트를 캡처합니다. 이 데이터 커넥터는 macOS 감사 이벤트를 실시간으로 Azure Sentinel에 효율적으로 내보낼 수 있습니다. 자세한 내용은 [NXLog Azure Sentinel User Guide](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)를 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-dns-logs-preview"></a>NXLog DNS Logs(미리 보기)

NXLog DNS Logs 데이터 커넥터는 ETW(Windows용 이벤트 추적)를 사용하여 감사 및 분석 DNS 서버 이벤트를 모두 수집합니다. 최대 효율성을 위해 NXLog im_etw 모듈은 이벤트 추적 데이터를 직접 읽기 때문에 이벤트 추적을 *.etl* 파일로 캡처할 필요가 없습니다. 이 REST API 커넥터는 DNS 서버 이벤트를 Azure Sentinel에 실시간으로 전달할 수 있습니다. 자세한 내용은 [NXLog Azure Sentinel User Guide](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [NXLog(Windows) DNS Logs를 Azure Sentinel에 연결](connect-nxlog-dns.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit(미리 보기)

NXLog LinuxAudit 데이터 커넥터는 사용자 지정 감사 규칙을 지원하며 AuditD 또는 기타 사용자 소프트웨어를 사용하지 않고 로그를 수집합니다. 커넥터는 IP 주소와 그룹/사용자 ID를 각각의 이름으로 확인하여 Linux 감사 로그를 더 이해하기 쉽게 만듭니다. 이 REST API 커넥터는 Linux 보안 이벤트를 Azure Sentinel에 실시간으로 내보낼 수 있습니다. 자세한 내용은 [NXLog Azure Sentinel User Guide](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [NXLog LinuxAudit를 Azure Sentinel에 연결](connect-nxlog-linuxaudit.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [NXLog](https://nxlog.co/community-forum)

## <a name="okta-single-sign-on-preview"></a>Okta Single Sign-On(미리 보기)

Okta SSO(Single Sign-On) 데이터 커넥터는 Okta API의 감사 및 이벤트 로그를 Azure Sentinel에 수집합니다. API 토큰을 생성하려면 [Create the token](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Okta SSO를 Azure Sentinel에 연결](connect-okta-single-sign-on.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="onapsis-platform-preview"></a>Onapsis Platform(미리 보기)

Onapsis 데이터 커넥터는 Onapsis Platform에서 트리거된 경보를 실시간으로 Azure Sentinel에 내보냅니다.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Onapsis](https://onapsis.force.com/s/login/)

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard(미리 보기)

One Identity Safeguard CEF Sentinel 데이터 커넥터는 권한 있는 세션별 대시보드에 대한 보호 기능을 포함하여 표준 CEF 커넥터를 향상시킵니다. 이 커넥터는 시각화, 경고, 조사 등에 디바이스 이벤트를 사용합니다. 자세한 내용은 [One Identity Safeguard for Privileged Sessions Administration Guide](https://aka.ms/sentinel-cef-oneidentity-forwarding)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [One Identity Safeguard를 Azure Sentinel에 연결](connect-one-identity.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [One Identity](https://support.oneidentity.com/)

## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server(미리 보기)

OracleWebLogicServer 데이터 커넥터는 OracleWebLogicServer 이벤트를 Azure Sentinel에 수집합니다. 자세한 내용은 [Oracle WebLogic Server 설명서](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html)를 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="orca-security-alert-preview"></a>Orca Security Alert(미리 보기)

Orca Security Alerts 커넥터는 Alerts 로그를 Azure Sentinel에 자동으로 내보냅니다. 자세한 내용은 [Azure Sentinel 통합](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Orca Security를 Azure Sentinel에 연결](connect-orca-security-alerts.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Orca Security](http://support.orca.security/)

## <a name="ossec-preview"></a>OSSEC(미리 보기)

OSSEC 데이터 커넥터는 OSSEC 이벤트를 Azure Sentinel에 수집합니다. 자세한 내용은 [OSSEC 설명서](https://www.ossec.net/docs/)를 참조하세요. Syslog를 통해 경고를 보내는 OSSEC를 구성하려면 [Sending alerts via syslog](https://www.ossec.net/docs/docs/manual/output/syslog-output.html)의 지침을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** Microsoft

## <a name="palo-alto-networks"></a>Palo Alto Networks

Palo Alto Networks 방화벽 데이터 커넥터는 Palo Alto Networks 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [CEF(Common Event Format) 구성 가이드](https://aka.ms/asi-syslog-paloalto-forwarding) 및 [Syslog 모니터링 구성](https://aka.ms/asi-syslog-paloalto-configure)을 참조하세요.

Palo Alto Networks 데이터 커넥터는 Syslog를 통해 Azure Sentinel [CEF](connect-common-event-format.md)에 로그를 수집합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Palo Alto Networks를 Azure Sentinel에 연결](connect-paloalto.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support)

## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 Activity Logs(미리 보기)

Perimeter 81 Activity Logs 데이터 커넥터는 Perimeter 81 활동 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 Perimeter 81 [Azure Sentinel](https://support.perimeter81.com/docs/360012680780) 설명서를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Perimeter 81 로그를 Azure Sentinel에 연결](connect-perimeter-81-logs.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Perimeter 81](https://support.perimeter81.com/)

## <a name="proofpoint-on-demand-pod-email-security-preview"></a>POD(Proofpoint On Demand) Email Security(미리 보기)

POD Email Security 데이터 커넥터는 POD Email Protection 데이터를 가져옵니다. 이 커넥터를 사용하면 메시지 추적 가능성을 확인하고 공격자와 악의적인 내부자의 이메일 활동, 위협 및 데이터 유출을 모니터링할 수 있습니다. 조직 이벤트를 신속하게 검토하고 최근 활동에 대해 시간 단위로 이벤트 로그를 가져올 수 있습니다. POD Log API를 사용하도록 설정하고 확인하는 방법에 대한 자세한 내용은 [Proofpoint 커뮤니티에 로그인](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)하여 [Proofpoint-on-Demand-Pod-Log-API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [POD(Proofpoint On Demand) Email Security를 Azure Sentinel에 연결](connect-proofpoint-pod.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint TAP(Targeted Attack Protection)(미리 보기)

Proofpoint TAP 커넥터는 Proofpoint TAP 로그 및 이벤트를 Azure Sentinel에 수집합니다. 이 커넥터는 Azure Sentinel의 메시지 및 클릭 이벤트에 대한 가시성을 제공합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Proofpoint TAP를 Azure Sentinel에 연결](connect-proofpoint-tap.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure(미리 보기)

Pulse Connect Secure 커넥터는 Pulse Connect Secure 로그를 Azure Sentinel에 연결합니다. Pulse Connect Secure 로그의 syslog 스트리밍을 사용하도록 설정하려면 [Configuring Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Pulse Connect Secure를 Azure Sentinel에 연결](connect-pulse-connect-secure.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="qualys-vulnerability-management-vm-knowledgebase-kb-preview"></a>Qualys VM(Vulnerability Management) KB(KnowledgeBase)(미리 보기)

Qualys VM KB 데이터 커넥터는 Qualys KB의 최신 취약성 데이터를 Azure Sentinel에 수집합니다. 이 데이터를 사용하여 Qualys VM 데이터 커넥터에서 취약성 탐지 상관 관계를 지정하고 보강할 수 있습니다.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="qualys-vm-preview"></a>Qualys VM(미리 보기)

Qualys VM 데이터 커넥터는 Qualys API를 통해 취약성 호스트 탐지 데이터를 Azure Sentinel에 수집합니다. 커넥터는 취약성 검사에서 호스트 탐지 데이터에 대한 가시성을 제공합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Qualys VM을 Azure Sentinel에 연결](connect-qualys-vm.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud(미리 보기)

Salesforce Service Cloud 데이터 커넥터는 REST API를 통해 Salesforce 운영 이벤트에 대한 정보를 Azure Sentinel에 수집합니다. 이 커넥터를 사용하면 조직 이벤트를 신속하게 검토하고 최근 활동에 대해 시간 단위로 이벤트 로그를 가져올 수 있습니다. 자세한 정보 및 자격 증명은 Salesforce [REST API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Salesforce Service Cloud를 Azure Sentinel에 연결](connect-salesforce-service-cloud.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="sentinelone-preview"></a>SentinelOne(미리 보기)

SentinelOne 데이터 커넥터는 SentinelOne 이벤트를 Azure Sentinel에 수집합니다. 이벤트에는 위협, 에이전트, 애플리케이션, 활동, 정책, 그룹 등과 같은 서버 개체가 포함됩니다. 이 커넥터는 잠재적인 보안 위험을 검사하고, 팀의 협업을 분석하고, 구성 문제를 진단하는 등의 이벤트를 가져올 수 있습니다.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall(미리 보기)

SonicWall Firewall 데이터 커넥터는 방화벽 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Log > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)를 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [SonicWall](https://www.sonicwall.com/support/)

## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix(미리 보기)

Sophos Cloud Optix 데이터 커넥터는 Sophos Cloud Optix 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 Cloud Optix 설정에서 Azure Sentinel [통합 페이지](https://optix.sophos.com/#/integrations/sentinel)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Sophos Cloud Optix를 Azure Sentinel에 연결](connect-sophos-cloud-optix.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Sophos](https://secure2.sophos.com/en-us/support.aspx)

## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall(미리 보기)

Sophos XG Firewall은 Sophos XG Firewall 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Add a syslog server](https://docs.sophos.com/nsg/sophos-firewall/18.0/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Sophos XG를 Azure Sentinel에 연결](connect-sophos-xg-firewall.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="squadra-technologies-secrmm"></a>Squadra 기술 secRMM

Squadra Technologies secRMM 데이터 커넥터는 USB 이동식 스토리지 보안 이벤트 데이터를 Azure Sentinel에 푸시합니다. 자세한 내용은 [secRMM Azure Sentinel Administrator Guide](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Squadra Technologies secRMM을 Azure Sentinel에 연결](connect-squadra-secrmm.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx)

## <a name="squid-proxy-preview"></a>Squid Proxy(미리 보기)

[Squid Proxy](http://www.squid-cache.org/) 데이터 커넥터는 Squid Proxy 로그를 Azure Sentinel에 연결합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Squid Proxy를 Azure Sentinel에 연결](connect-squid-proxy.md)을 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec ICDx(Integrated Cyber Defense Exchange)

Symantec ICDx 데이터 커넥터는 Symantec 보안 솔루션 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Symantec ICDx 어플라이언스 연결](connect-symantec.md)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Symantec ICDx를 Azure Sentinel에 연결](connect-symantec.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Broadcom Symantec](https://support.broadcom.com/security)

## <a name="symantec-proxysg-preview"></a>Symantec ProxySG(미리 보기)

Symantec ProxySG 데이터 커넥터는 Symantec ProxySG 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Sending Access Logs to a Syslog server](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html)를 참조하세요.

Symantec의 ProxySG 데이터 커넥터는 [Syslog](connect-syslog.md)를 통해 Azure Sentinel에 로그를 수집합니다. 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Symantec Proxy SG를 Azure Sentinel에 연결](connect-symantec-proxy-sg.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="symantec-vip-preview"></a>Symantec VIP(미리 보기)

Symantec VIP 데이터 커넥터는 Symantec VIP 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Configuring syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US)를 참조하세요.

Symantec의 VIP 데이터 커넥터는 [Syslog](connect-syslog.md)를 통해 Azure Sentinel에 로그를 수집합니다. 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Symantec VIP를 Azure Sentinel에 연결](connect-symantec-vip.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server(미리 보기)

Thycotic Secret Server 데이터 커넥터는 Secret Server 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Secure Syslog/CEF Logging](https://docs.thycotic.com/ss/10.9.0/events-and-alerts/secure-syslog-cef)을 참조하세요.

Thycotic 데이터 커넥터는 Syslog를 통해 Azure Sentinel [CEF](connect-common-event-format.md)에 로그를 수집합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Thycotic Secret Server를 Azure Sentinel에 연결](connect-thycotic-secret-server.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Thycotic](https://thycotic.force.com/support/s/)

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

Trend Micro Deep Security 데이터 커넥터는 Deep Security 로그를 Azure Sentinel에 연결합니다. 자세한 내용은 [Syslog 또는 SIEM 서버로 Deep Security 이벤트 전달](https://help.deepsecurity.trendmicro.com/12_0/on-premise/siem-syslog-forwarding-secure.html?redirected=true&Highlight=Configure%20Syslog#Protection_modules_DSM)을 참조하세요.

Trend Micro Deep Security 데이터 커넥터는 Syslog를 통해 Azure Sentinel [CEF](connect-common-event-format.md)에 로그를 수집합니다. 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Trend Micro Deep Security를 Azure Sentinel에 연결](connect-trend-micro.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint(미리 보기)

Trend Micro TippingPoint 데이터 커넥터는 TippingPoint SMS IPS 이벤트를 Azure Sentinel에 연결합니다.

Azure Sentinel 연결에 대한 자세한 내용은 [Trend Micro TippingPoint를 Azure Sentinel에 연결](connect-trend-micro-tippingpoint.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-xdr-preview"></a>Trend Micro XDR(미리 보기)

Trend Micro XDR 데이터 커넥터는 Trend Micro XDR API의 워크벤치 경고를 Azure Sentinel에 수집합니다. 계정과 API 인증 토큰을 생성하려면 [Obtaining API Keys for Third-Party Access](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys)의 지침을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard(미리 보기)

VMware Carbon Black Endpoint Standard 데이터 커넥터는 Carbon Black Endpoint Standard 데이터를 Azure Sentinel에 수집합니다. API 키를 생성하려면 [Creating an API Key](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key)의 지침을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [VMware Carbon Black Cloud Endpoint Standard를 Azure Sentinel에 연결](connect-vmware-carbon-black.md)을 참조하세요.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공:** Microsoft

## <a name="vmware-esxi-preview"></a>VMware ESXi(미리 보기)

VMware ESXi 데이터 커넥터는 VMware ESXi 로그를 Azure Sentinel에 연결합니다. syslog를 전달하도록 VMware ESXi 커넥터를 구성하려면 [ESXi 3.5 및 4.x에서 syslog 사용](https://kb.vmware.com/s/article/1016621) 및 [ESXi 호스트에서 Syslog 구성](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [VMware ESXi를 Azure Sentinel에 연결](connect-vmware-esxi.md)을 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox(미리 보기)

WatchGuard Firebox 데이터 커넥터는 방화벽 로그를 Azure Sentinel에 수집합니다. 자세한 내용은 [Microsoft Azure Sentinel Integration Guide](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html)를 참조하세요.

**데이터 수집 방법:** [Syslog](connect-syslog.md) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview)

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform(미리 보기)

WireX Systems 데이터 커넥터를 사용하면 보안 전문가가 Azure Sentinel과 통합하여 포렌식 조사를 보강할 수 있습니다. 이 커넥터는 WireX에서 제공하는 컨텍스트 콘텐츠를 포함할 뿐만 아니라 다른 출처의 데이터를 분석할 수 있습니다. 포렌식 조사 중에 가장 완전한 그림을 제공하기 위해 사용자 지정 대시보드 및 워크플로를 만들 수 있습니다. 자세한 정보 및 구성 지원은 [WireX 지원](https://wirexsystems.com/contact-us/)에 문의하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [WireX Network Forensics Platform을 Azure Sentinel에 연결](connect-wirex-systems.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** WireX

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook(미리 보기)

Workplace 데이터 커넥터는 Webhooks를 통해 일반적인 Workplace 이벤트를 Azure Sentinel에 수집합니다. Webhooks를 사용하면 맞춤형 통합 앱이 Workplace의 이벤트를 구독하고 실시간으로 업데이트를 받을 수 있습니다. 변화가 생기면 Workplace는 이벤트 정보가 포함된 HTTPS POST 요청을 콜백 데이터 커넥터 URL에 보냅니다. 자세한 내용은 Webhooks 설명서를 참조하세요. 이 커넥터는 잠재적인 보안 위험을 검사하고, 팀의 협업을 분석하고, 구성 문제를 진단하는 등의 이벤트를 가져올 수 있습니다.

**데이터 수집 방법:** [Azure Functions 및 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)

**지원 제공**: Microsoft

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense(미리 보기)

Zimperium Mobile Threat Defense 데이터 커넥터는 Zimperium 위협 로그를 Azure Sentinel에 연결하여 대시보드를 확인하고, 사용자 지정 경고를 만들고, 조사를 개선합니다. 이 커넥터는 조직의 모바일 위협 환경에 대한 자세한 인사이트를 제공하고 보안 작업 기능을 향상시킵니다. 자세한 지침은 [Zimperium customer support portal](https://support.zimperium.com/)을 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Zimperium을 Azure Sentinel에 연결](connect-zimperium-mtd.md)을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** [Zimperium](https://www.zimperium.com/support)

## <a name="zoom-reports-preview"></a>Zoom Reports(미리 보기)

Zoom Reports 데이터 커넥터는 Zoom Reports 이벤트를 Azure Sentinel에 수집합니다. 이 커넥터는 잠재적인 보안 위험을 검사하고, 팀의 협업을 분석하고, 구성 문제를 진단하는 등의 이벤트를 가져올 수 있습니다. 자격 증명을 얻으려면 [JWT With Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt)의 지침을 참조하세요.

**데이터 수집 방법:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)

**지원 제공:** Microsoft

## <a name="zscaler"></a>Zscaler 

Zscaler 데이터 커넥터는 ZIA(Zscaler Internet Access) 로그를 Azure Sentinel에 연결합니다. Azure Sentinel에서 Zscaler를 사용하면 조직의 인터넷 사용에 대한 인사이트를 얻을 수 있고 보안 운영 기능을 향상시킬 수 있습니다. 자세한 내용은 [Zscaler and Microsoft Azure Sentinel Deployment Guide](https://aka.ms/ZscalerCEFInstructions)를 참조하세요.

Azure Sentinel 연결에 대한 자세한 내용은 [Zscaler를 Azure Sentinel에 연결](connect-zscaler.md)을 참조하세요.

**데이터 수집 방법:** Syslog를 통한 [CEF](connect-common-event-format.md)

**지원 제공:** [Zscaler](https://help.zscaler.com/submit-ticket-links)

## <a name="zscaler-private-access-zpa-preview"></a>ZPA(Zscaler Private Access)(미리 보기)

ZPA 데이터 커넥터는 Zscaler Private Access 이벤트를 Azure Sentinel에 수집합니다. 자세한 내용은 [Zscaler Private Access 설명서](https://help.zscaler.com/zpa)를 참조하세요.

**데이터 수집 방법:** [Log Analytics 에이전트 사용자 지정 로그](connect-data-sources.md#custom-logs) 커넥터는 Kusto 함수를 기반으로 하는 로그 파서도 사용합니다.

**지원 제공:** Microsoft
