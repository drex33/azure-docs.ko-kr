---
title: Microsoft 센티널 데이터 원본 스키마 참조
description: 이 문서에서는 참조 설명서에 대 한 링크와 함께 Microsoft 센티널에서 지 원하는 Azure 및 타사 데이터 원본 스키마를 나열 합니다.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.topic: reference
ms.custom: ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 0ea3809ec96d956e50d698c85cabbcf99420335d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724428"
---
# <a name="data-source-schema-reference"></a>데이터 원본 스키마 참조

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에는 지원되는 Azure 및 타사 데이터 원본 스키마가 해당 참조 설명서에 대한 링크와 함께 나열되어 있습니다.

## <a name="azure-data-sources"></a>Azure 데이터 원본

| 유형                             | 데이터 원본             | Log Analytics 테이블 이름 | 스키마 참조 |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Azure AD 활동 보고서 로그인 속성](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor AuditLogs 참조](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Monitor AzureActivity 참조](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Office 365 관리 작업 API 스키마: <br>- [공용 스키마 ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange 관리 스키마 ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange 사서함 스키마](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint 기본 스키마](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint 파일 작업](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Monitor AzureDiagnostics 참조](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | syslog                 | [Azure Monitor Syslog 참조](/azure/azure-monitor/reference/tables/syslog) |
| **Network**                          | IIS 로그               | W3CIISLog              | [Azure Monitor W3CIISLog 참조](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Network**                          | VMinsights             | VMConnection           | [Azure Monitor VMConnection 참조](/azure/azure-monitor/reference/tables/vmconnection) |
| **Network**                          | Wire Data 솔루션     | WireData               | [Azure Monitor WireData 참조](/azure/azure-monitor/reference/tables/wiredata) |
| **Network**                          | NSG 흐름 로그          | AzureNetworkAnalytics  | [트래픽 분석에서 스키마 및 데이터 집계](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> 자세한 내용은 전체 [Azure Monitor 데이터 참조](/azure/azure-monitor/reference/)를 참조하세요.
>
## <a name="3rd-party-vendor-data-sources"></a>타사 공급업체 데이터 원본

다음 표에는 다양한 지원 로그 유형별로 지원되는 타사 공급업체 및 해당 CEF(Common Event Format) 매핑 설명서가 나열되어 있으며, 해당 설명서에는 각 범주 유형에 대한 CEF 필드 매핑 및 샘플 로그가 포함되어 있습니다.

| 유형 |    Vendor |    제품 | Log Analytics 테이블 이름 | CEF 필드 매핑 참조  |
| ----- | ----- | ----- | ----- |----- |
| **Network** | Palo Alto   | PAN OS    | CommonSecurityLog |   [PAN-OS 9.0 Common Event Format 통합 가이드](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf)(*CEF - 스타일 로그 형식* 검색) |
| **Network** | Check Point  |ALL   | CommonSecurityLog | [로그 필드 설명](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Network** | Fortigate   | ALL   | CommonSecurityLog | [로그 스키마 구조](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Network** | Barracuda | 웹 애플리케이션 방화벽 |  CommonSecurityLog   | [Syslog 및 기타 로그를 구성하는 방법](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Network** | 시스코 | ASA | CommonSecurityLog | [Cisco ASA 시리즈 Syslog 메시지](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Network** | 시스코 | Firepower   | CommonSecurityLog | [Cisco Firepower 위협 방어 Syslog 메시지](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.html)    |
| **Network** | 시스코   | Umbrella  | 사용자 지정 로그 테이블  | [로그 형식 및 버전 관리](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Network**   | 시스코 | Meraki    | CommonSecurityLog |   [Syslog 이벤트 유형 및 로그 예제](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Network**   | Zscaler | NSS(Nano Streaming Service)|   CommonSecurityLog | [NSS 피드 형식 지정](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds)(웹, 방화벽, DNS, 터널 로그에만 해당) |
| **Network**   |F5 | BigIP LTM|    CommonSecurityLog|  [이벤트 메시지 및 공격 유형](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Network** | F5  | BigIP ASM|    CommonSecurityLog|  [로깅 애플리케이션 보안 이벤트](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Network** | Citrix  |웹앱 방화벽   | CommonSecurityLog|    [애플리케이션 방화벽에서 CEF(Common Event Format) 로깅 지원](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12.0 Syslog 메시지 참조](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | SEPM(Symantec Endpoint Protection Manager) | CommonSecurityLog|[Endpoint Protection Manager에 대한 외부 로깅 설정 및 로그 이벤트 심각도 수준](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Trend Micro |모두 |CommonSecurityLog | [Syslog 콘텐츠 매핑 - CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

> [!NOTE]
> 자세한 내용은 [CEF 및 CommonSecurityLog 필드 매핑](cef-name-mapping.md)을 참조하세요.
> 
## <a name="next-steps"></a>다음 단계

CEF, Syslog, direct, agent, 사용자 지정 커넥터 등의 지원 되는 Microsoft 센티널 커넥터에 대해 자세히 알아보세요.

- [데이터 원본 연결](connect-data-sources.md)

- [Microsoft 센티널 Syslog, CEF 및 기타 타사 커넥터](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)
