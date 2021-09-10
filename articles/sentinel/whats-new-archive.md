---
title: Azure Sentinel의 새로운 기능 보관
description: 6개월 전 또는 그 이전의 Azure Sentinel의 새로운 기능 및 변경된 기능에 대한 설명입니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 06/15/2021
ms.openlocfilehash: 979855c0ad6a19c03c0c2c23cde935bc03cba157
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567615"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Azure Sentinel의 새로운 기능 보관

기본 [Azure Sentinel의 새로운 기능](whats-new.md) 릴리스 정보 페이지에는 지난 6개월 동안의 업데이트가 포함되어 있지만 이 페이지에는 더 오래된 항목도 포함되어 있습니다.

이전에 제공된 기능에 대한 자세한 내용은 [기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)를 참조하세요.

언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.


> [!TIP]
> Microsoft 위협 헌팅 팀에서는 [Azure Sentinel 커뮤니티](https://github.com/Azure/Azure-Sentinel)에 쿼리, 플레이북, 통합 문서, Notebook을 제공하고 있으며, 여기에는 여러분의 팀이 조정하여 사용할 수 있는 특정한 [헌팅 쿼리](https://github.com/Azure/Azure-Sentinel)가 포함됩니다.
>
> 여러분도 참여할 수 있습니다! [Azure Sentinel 위협 헌터 GitHub 커뮤니티](https://github.com/Azure/Azure-Sentinel/wiki)에 참여하세요.
>

## <a name="february-2021"></a>2021년 2월

- [CMMC(Cybersecurity Maturity Model Certification) 통합 문서](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [타사 데이터 커넥터](#third-party-data-connectors)
- [엔터티 페이지의 UEBA 인사이트(공개 미리 보기)](#ueba-insights-in-the-entity-page-public-preview)
- [향상된 인시던트 검색(공개 미리 보기)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>CMMC(Cybersecurity Maturity Model Certification) 통합 문서

Azure Sentinel CMMC 통합 문서는 Microsoft 보안 제품, Office 365, Teams, Intune, Windows Virtual Desktop 등을 비롯한 Microsoft 포트폴리오 전체에서 CMMC 컨트롤에 맞게 조정된 로그 쿼리를 볼 수 있는 메커니즘을 제공합니다.

CMMC 통합 문서를 통해 보안 설계자, 엔지니어, 보안 운영 분석가, 관리자 및 IT 전문가는 클라우드 워크로드의 보안 상태에 대한 상황적 인식 가시성을 확보할 수 있습니다. 각 CMMC 요구 사항 및 업무 방식에 맞게 Microsoft 제품을 선택, 설계, 배포 및 구성할 수 있는 권장 사항도 있습니다.

CMMC를 준수할 필요가 없더라도 CMMC 통합 문서는 보안 운영 센터를 구축하고, 경고를 개발하고, 위협 요소를 시각화하고, 워크로드에 대한 상황 인식을 제공하기에 유용합니다.

Azure Sentinel **통합 문서** 영역에서 CMMC 통합 문서에 액세스할 수 있습니다. **템플릿** 을 선택하고 **CMMC** 를 검색하세요.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="CMMC 통합 문서 가이드 설정/해제에 대한 GIF 기록" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


자세한 내용은 다음을 참조하세요.

- [Azure Sentinel CMMC(Cybersecurity Maturity Model Certification) 통합 문서](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [데이터 시각화 및 모니터링](monitor-your-data.md)


### <a name="third-party-data-connectors"></a>타사 데이터 커넥터

Microsoft의 타사 통합 컬렉션은 지난 2개월 동안 30개의 커넥터가 추가되면서 계속 증가하고 있습니다. 목록은 다음과 같습니다.

- [Agari 피싱 방어 및 브랜드 보호](connect-agari-phishing-defense.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Active Directory용 Alsid](connect-alsid-active-directory.md)
- [Apache HTTP 서버](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco UCS(Unified Computing System)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace(이전의 G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis 플랫폼](connect-data-sources.md)
- [Proofpoint On Demand Email Security(POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Salesforce 서비스 클라우드](connect-salesforce-service-cloud.md)
- [SonicWall 방화벽](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>엔터티 페이지의 UEBA 인사이트(공개 미리 보기)

Azure Sentinel 엔터티 세부 정보 페이지에는 [인사이트 창](identify-threats-with-entity-behavior-analytics.md#entity-insights)이 제공됩니다. 여기에는 엔터티에 대한 동작 인사이트가 표시되어 이상 징후와 보안 위협을 빠르게 식별하는 데 도움이 됩니다.

[UEBA를 사용하도록 설정](ueba-enrichments.md)하고 기간을 4일 이상 선택하면 이 인사이트 창에 UEBA 인사이트를 위한 다음과 같은 새 섹션이 포함됩니다.

|섹션  |Description  |
|---------|---------|
|**UEBA 인사이트**     | 비정상적인 사용자 활동이 다음과 같이 요약됩니다. <br>- 지리적 위치, 디바이스, 환경 전반<br>- 사용자 자신의 기록과 비교한 시간 및 빈도 전반 <br>- 피어의 동작과 비교 <br>- 조직의 동작과 비교     |
|**보안 그룹 멤버 자격에 기반한 사용자 피어**     |   Azure AD 보안 그룹 멤버 자격을 기반으로 사용자의 피어를 나열하여 유사한 권한을 공유하는 다른 사용자 목록을 보안 운영 팀에 제공합니다.  |
|**Azure 구독에 대한 사용자 액세스 권한**     |     직접 또는 Azure AD 그룹/서비스 주체를 통해 액세스할 수 있는 Azure 구독에 대한 사용자의 액세스 권한을 표시합니다.   |
|**사용자와 관련된 위협 지표**     |  사용자의 활동에 나타난 IP 주소와 관련하여 알려진 위협 컬렉션을 나열합니다. 위협은 위협 유형 및 제품군별로 나열되며 Microsoft의 위협 인텔리전스 서비스를 통해 보강됩니다.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>향상된 인시던트 검색(공개 미리 보기)

Azure Sentinel 인시던트 검색 환경을 향상되어, 특정 위협을 조사할 때 인시던트를 더 빠르게 탐색할 수 있습니다.

Azure Sentinel에서 인시던트를 검색할 때 이제 다음과 같은 인시던트 세부 정보를 기반으로 검색할 수 있습니다.

- ID
- 제목
- 제품
- 소유자
- 태그

## <a name="january-2021"></a>2021년 1월

- [분석 규칙 마법사: 향상된 쿼리 편집 환경(공개 미리 보기)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az.SecurityInsights PowerShell 모듈(공개 미리 보기)](#azsecurityinsights-powershell-module-public-preview)
- [SQL 데이터베이스 커넥터](#sql-database-connector)
- [Dynamics 365 커넥터(공개 미리 보기)](#dynamics-365-connector-public-preview)
- [인시던트 주석 기능 향상](#improved-incident-comments)
- [전용 Log Analytics 클러스터](#dedicated-log-analytics-clusters)
- [논리 앱 관리 ID](#logic-apps-managed-identities)
- [분석 규칙 미리 보기 그래프를 통해 규칙 조정 기능 향상](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>분석 규칙 마법사: 향상된 쿼리 편집 환경(공개 미리 보기)

Azure Sentinel 예약된 분석 규칙 마법사에 쿼리 작성 및 편집을 위해 다음과 같은 향상된 기능이 제공됩니다.

-   확장 가능한 편집 창을 통해 쿼리를 볼 수 있는 화면 공간이 더 많이 제공됩니다.
-   쿼리 코드에서 핵심 단어 강조가 강조 표시됩니다.
-   자동 완성 지원이 확장됩니다.
-   실시간 쿼리 유효성 검사. 이제 쿼리의 오류가 스크롤 막대에 빨간색 블록으로 표시되고 **규칙 논리 설정** 탭 이름에 빨간색 점으로 표시됩니다. 또한 오류가 있는 쿼리는 저장할 수 없습니다.

자세한 내용은 [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)를 참조하세요.
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell 모듈(공개 미리 보기)

Azure Sentinel에서 새로운 [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell 모듈이 지원됩니다.

**Az. SecurityInsights** 모듈은 인시던트와 상호 작용하여 상태, 심각도, 소유자 등을 변경하고, 인시던트에 주석과 레이블을 추가하고, 책갈피를 만드는 등의 일반적인 Azure Sentinel 사용 사례를 지원합니다.

CI/CD 파이프라인에는 [ARM(Azure Resource Manager)](../azure-resource-manager/templates/index.yml) 템플릿을 사용하는 것이 좋고 **Az.SecurityInsights** 모듈은 배포 후 작업에 유용하며 SOC 자동화를 대상으로 합니다.  예를 들어 SOC 자동화에는 데이터 커넥터를 구성하거나, 분석 규칙을 만들거나, 분석 규칙에 자동화 작업을 추가하는 단계가 포함될 수 있습니다.

사용 가능한 cmdlet의 전체 목록과 설명, 매개 변수 설명 및 예를 포함한 자세한 내용은 [Az.SecurityInsights PowerShell 설명서](/powershell/module/az.securityinsights/)를 참조하세요.

### <a name="sql-database-connector"></a>SQL 데이터베이스 커넥터

Azure Sentinel에 이제 Azure SQL 데이터베이스 커넥터가 제공됩니다. 이를 통해 데이터베이스의 감사 및 진단 로그를 Azure Sentinel로 스트리밍하고 모든 인스턴스의 활동을 지속적으로 모니터링할 수 있습니다.

Azure SQL은 사용자 개입 없이 업그레이드, 패치, 백업 및 모니터링과 같은 데이터베이스 관리 기능 대부분을 처리하는 완전 관리형 Platform as a Service(PaaS) 데이터베이스 엔진입니다.

자세한 내용은 [Azure SQL 데이터베이스 진단 및 감사 로그 연결](connect-azure-sql-logs.md)을 참조하세요.

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365 커넥터(공개 미리 보기)

Azure Sentinel에 이제 Microsoft Dynamics 365용 커넥터가 제공됩니다. 이를 통해 Dynamics 365 애플리케이션의 사용자, 관리자 및 지원 활동 로그를 Azure Sentinel에 수집할 수 있습니다. 이러한 데이터를 사용하여, 발생하는 데이터 처리 작업 전체를 감사하고 보안 위반 가능성을 분석할 수 있습니다.

자세한 내용은 [Dynamics 365 활동 로그를 Azure Sentinel에 연결](connect-dynamics-365.md)을 참조하세요.

### <a name="improved-incident-comments"></a>인시던트 주석 기능 향상

분석가는 인시던트 주석을 사용하여 인시던트 관련 협업을 진행하고 프로세스 및 단계를 수동으로 문서화하거나 플레이북의 일부로 문서화합니다. 

인시던트 주석 처리 환경이 향상되어, 주석의 형식을 지정하고 기존 주석을 편집하거나 삭제할 수 있습니다.

자세한 내용은 [Microsoft 보안 경고에서 인시던트 자동 생성](create-incidents-from-alerts.md)을 참조하세요.
### <a name="dedicated-log-analytics-clusters"></a>전용 Log Analytics 클러스터

이제 Azure Sentinel에 전용 Log Analytics 클러스터가 배포 옵션으로 지원됩니다. 다음과 같은 경우 전용 클러스터를 고려하는 것이 좋습니다.

- Azure Sentinel 작업 영역에 **하루 수집 규모가 1TB를 초과하는 경우**
- Azure 등록에 **여러 Azure Sentinel 작업 영역이 있는 경우**

전용 클러스터를 사용하면 동일한 클러스터에 여러 작업 영역이 있을 경우 고객 관리형 키, Lockbox, 이중 암호화 및 작업 영역 간 빠른 쿼리와 같은 기능을 사용할 수 있습니다.

자세한 내용은 [Azure Monitor 로그 전용 클러스터](../azure-monitor/logs/logs-dedicated-clusters.md)를 참조하세요.

### <a name="logic-apps-managed-identities"></a>논리 앱 관리 ID

이제 Azure Sentinel에서 Azure Sentinel Logic Apps 커넥터에 대한 관리 ID가 지원되어, ID를 추가로 만드는 대신 Azure Sentinel에서 작동하도록 특정 플레이북에 직접 권한을 부여할 수 있습니다.

- **관리 ID가 없는 경우** Azure Sentinel에서 실행하려면 Logic Apps 커넥터에 Azure Sentinel RBAC 역할이 있는 별도의 ID가 필요합니다. 별도의 ID는 Azure AD 사용자 또는 Azure AD 등록 애플리케이션과 같은 서비스 주체일 수 있습니다.

- **논리 앱에서 관리 ID 지원을 켜면** 논리 앱이 Azure AD에 등록되고 개체 ID가 제공됩니다. Azure Sentinel에서 개체 ID를 사용하여 Azure Sentinel 작업 영역에서 Azure RBAC 역할이 있는 논리 앱을 할당할 수 있습니다. 

자세한 내용은 다음을 참조하세요.

- [Azure Logic Apps에서 관리 ID로 인증](../logic-apps/create-managed-service-identity.md)
- [Azure Sentinel Logic Apps 커넥터 설명서](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>분석 규칙 미리 보기 그래프를 통해 규칙 조정 기능 향상(공개 미리 보기)

Azure Sentinel을 통해 분석 규칙을 더 잘 조정할 수 있기 때문에 정확도를 높이고 노이즈를 줄이는 데 유용합니다.

**규칙 논리 설정** 탭에서 분석 규칙을 편집한 후 오른쪽의 **결과 시뮬레이션** 영역을 찾습니다. 

**현재 데이터로 테스트** 를 선택하여 Azure Sentinel이 분석 규칙의 마지막 50개 실행에 대한 시뮬레이션을 실행하도록 합니다. 평가된 원시 이벤트 데이터를 기반으로 규칙이 생성했을 평균 경고 수를 보여주는 그래프가 생성됩니다. 

자세한 내용은 [규칙 쿼리 논리 정의 및 설정 구성](detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)을 참조하세요.

## <a name="december-2020"></a>2020년 12월

- [80개의 새로운 기본 제공 헌팅 쿼리](#80-new-built-in-hunting-queries)
- [Log Analytics 에이전트의 향상된 기능](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80개의 새로운 기본 제공 헌팅 쿼리
 
Azure Sentinel의 기본 제공 헌팅 쿼리는 SOC 분석가가 현재 검색 범위의 격차를 줄이고 새로운 헌팅 리드를 활성화할 수 있도록 지원합니다.

Azure Sentinel에 대한 이번 업데이트에는 MITRE ATT&CK 프레임워크 매트릭스 전반의 범위를 제공하는 새로운 헌팅 쿼리가 포함되어 있습니다.

- **컬렉션**
- **명령 및 제어**
- **자격 증명 액세스**
- **검색**
- **실행**
- **반출**
- **영향**
- **초기 액세스**
- **지속성**
- **권한 상승**

추가된 헌팅 쿼리는는 사용자 환경에서 의심스러운 활동을 찾는 데 도움이 되도록 설계되었습니다. 정당한 활동과 잠재적으로 악의적 활동을 반환할 수 있지만 헌팅을 안내하는 데 유용할 수 있습니다. 

이러한 쿼리를 실행한 후 결과를 확신하면 분석 규칙으로 변환하거나 헌팅 결과를 기존 또는 신규 인시던트에 추가할 수 있습니다.

추가한 모든 쿼리는 Azure Sentinel 헌팅 페이지를 통해 사용할 수 있습니다. 자세한 내용은 [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)을 참조하세요.

### <a name="log-analytics-agent-improvements"></a>Log Analytics 에이전트의 향상된 기능

Azure Sentinel 사용자는 다음과 같은 Log Analytics 에이전트의 향상된 기능을 활용할 수 있습니다.

- CentOS 8, RedHat 8 및 SUSE Linux 15를 포함하여 **더 많은 운영 체제 지원**
- Python 2 외에 **Python 3 지원**

Azure Sentinel은 Log Analytics 에이전트를 사용하여 Windows 보안 이벤트, Syslog 이벤트, CEF 로그 등의 작업 영역으로 이벤트를 보냅니다.

> [!NOTE]
> Log Analytics 에이전트를 OMS 에이전트 또는 MMA(Microsoft Monitoring Agent)라고 하는 경우도 있습니다. 
> 

자세한 내용은 [Log Analytics 설명서](../azure-monitor/agents/log-analytics-agent.md) 및 [Log Analytics 에이전트 릴리스 정보](https://github.com/microsoft/OMS-Agent-for-Linux/releases)를 참조하세요.
## <a name="november-2020"></a>2020년 11월

- [Azure Sentinel에서 플레이북의 상태 모니터링](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender 커넥터(공개 미리 보기)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Azure Sentinel에서 플레이북의 상태 모니터링

Azure Sentinel 플레이북은 [Azure Log Apps](../logic-apps/index.yml)에 구축된 워크플로를 기반으로 하며, 작업, 비즈니스 프로세스 및 워크플로를 예약, 자동화 및 오케스트레이션하는 데 유용한 클라우드 서비스입니다. 플레이북은 인시던트가 생성될 때 또는 인시던트를 심사하고 작업할 때 자동으로 호출될 수 있습니다. 

플레이북의 상태, 성능 및 사용에 대한 인사이트를 제공하기 위해 **플레이북 상태 모니터링** 이라는 [통합 문서](../azure-monitor/visualize/workbooks-overview.md)가 추가되었습니다. 

**플레이북 상태 모니터링** 통합 문서를 사용하여 플레이북 상태를 모니터링하거나 성공한 실행 또는 실패한 실행의 수량에서 변칙을 찾을 수 있습니다. 

이제 Azure Sentinel 템플릿 갤러리에서 **플레이북 상태 모니터링** 통합 문서를 사용할 수 있습니다.

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="샘플 플레이북 상태 모니터링 통합 문서":::

자세한 내용은 다음을 참조하세요.

- [Logic Apps 설명서](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor 설명서](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender 커넥터(공개 미리 보기)
 
Azure Sentinel용 Microsoft 365 Defender 커넥터를 사용하면 Microsoft 365 Defender에서 Azure Sentinel로 원시 이벤트 데이터 형식인 고급 헌팅 로그를 스트리밍할 수 있습니다. 

[MDATP(엔드포인트용 Microsoft Defender)](/windows/security/threat-protection/)가 [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) 보안 우산에 통합됨에 따라 이제 Microsoft 365 Defender 커넥터를 사용하여 엔드포인트용 Microsoft Defender 고급 헌팅 이벤트를 수집하고 Azure Sentinel 작업 영역에서 새로운 용도로 빌드된 테이블로 직접 스트리밍할 수 있습니다. 

Azure Sentinel 테이블은 Microsoft 365 Defender 포털에서 사용되는 것과 동일한 스키마를 기반으로 하며 고급 헌팅 로그의 집합 전체에 대한 완전한 액세스를 제공합니다. 

자세한 내용은 [Microsoft 365 Defender의 데이터를 Azure Sentinel에 연결](connect-microsoft-365-defender.md)을 참조하세요.

> [!NOTE]
> Microsoft 365 Defender의 이전 이름은 MTP(Microsoft Threat Protection)였습니다. 엔드포인트용 Microsoft Defender의 이전 이름은 MDATP(Microsoft Defender Advanced Threat Protection)였습니다.
> 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Sentinel 온보딩](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](get-visibility.md)