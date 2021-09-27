---
title: Azure Sentinel의 새로운 기능 보관
description: 6개월 전 또는 그 이전의 Azure Sentinel의 새로운 기능 및 변경된 기능에 대한 설명입니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 07a0848de708f3d01cc081130a02ffa6e11f07db
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818887"
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

## <a name="march-2021"></a>2021년 3월

- [보기 모드에서 통합 문서가 자동으로 새로 고쳐지도록 설정](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Azure Firewall에 대한 신규 검색](#new-detections-for-azure-firewall)
- [자동화 규칙 및 인시던트 트리거 플레이북(공개 미리 보기)](#automation-rules-and-incident-triggered-playbooks-public-preview) (완전히 새로운 플레이북 문서 포함)
- [새로운 경고 강화: 향상된 엔터티 매핑 및 사용자 지정 세부 정보(공개 미리 보기)](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview)
- [Azure Sentinel 통합 문서를 인쇄하거나 PDF로 저장](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [인시던트 필터 및 정렬 기본 설정이 세션에 저장됨(공개 미리 보기)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender 인시던트 통합(공개 미리 보기)](#microsoft-365-defender-incident-integration-public-preview)
- [Azure Policy를 사용하는 새로운 Microsoft 서비스 커넥터](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>보기 모드에서 통합 문서가 자동으로 새로 고쳐지도록 설정

Azure Sentinel 사용자는 이제 새로운 [Azure Monitor 기능](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555)을 사용하여 보기 세션 중에 통합 문서 데이터를 자동으로 새로 고칠 수 있습니다.

각 통합 문서 또는 통합 문서 템플릿에서 :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **자동 새로 고침** 을 선택하여 간격 옵션을 표시합니다. 현재 보기 세션에 사용할 옵션을 선택하고 **적용** 을 선택합니다.

- 지원되는 새로 고침 간격의 범위는 **5분** 에서 **1일** 사이입니다.
- 기본적으로 자동 새로 고침은 꺼져 있습니다. 성능을 최적화하기 위해 자동 새로 고침은 통합 문서를 닫을 때마다 꺼지며 백그라운드에서 실행되지 않습니다. 다음에 통합 문서를 열 때 필요에 따라 자동 새로 고침을 다시 켭니다.
- 통합 문서를 편집하는 동안 자동 새로 고침이 일시 중지되며, 편집 모드에서 보기 모드로 다시 전환할 때마다 자동 새로 고침 간격이 다시 시작됩니다.

    :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **새로 고침** 단추를 선택하여 통합 문서를 수동으로 새로 고치는 경우에도 간격이 다시 시작됩니다.

자세한 내용은 [데이터 시각화 및 모니터링](monitor-your-data.md)과 [Azure Monitor 설명서](../azure-monitor/visualize/workbooks-overview.md)를 참조하세요.

### <a name="new-detections-for-azure-firewall"></a>Azure Firewall에 대한 신규 검색

Azure Firewall에 대한 몇 가지 기본 검색이 Azure Sentinel의 [Analytics](./understand-threat-intelligence.md) 영역에 추가되었습니다. 새로운 검색을 사용하면 내부 네트워크의 컴퓨터가 검색 규칙 쿼리에 정의되어 있는 알려진 IOC와 연결된 인터넷 도메인 이름 또는 IP 주소를 쿼리하거나 연결하려고 하면 보안 팀이 경고를 받을 수 있습니다.

새 검색에는 다음이 포함됩니다.

- [Solorigate 네트워크 비콘](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [알려진 GALLIUM 도메인 및 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [알려진 IRIDIUM IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [알려진 Phosphorus 그룹 도메인/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [DCU 무력화에 포함된 THALLIUM 도메인](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [알려진 ZINC 관련 맬웨어 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [알려진 STRONTIUM 그룹 도메인](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM - 도메인 및 IP IOC - 2021년 3월](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Azure 방화벽에 대한 검색은 기본 제공 템플릿 갤러리에 지속적으로 추가됩니다. Azure 방화벽에 대한 최신 검색을 보려면 **규칙 템플릿** 아래에서 **데이터 원본** 을 **Azure Firewall** 로 필터링합니다.

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Analytics 효율성 통합 문서의 새로운 검색":::

자세한 내용은 [Azure Sentinel의 Azure Firewall에 대한 신규 검색](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958)을 참조하세요.

### <a name="automation-rules-and-incident-triggered-playbooks-public-preview"></a>자동화 규칙 및 인시던트 트리거 플레이북(공개 미리 보기)

자동화 규칙은 인시던트 처리 자동화를 중앙에서 관리할 수 있는 Azure Sentinel의 새로운 개념입니다. 자동화 규칙을 사용하면 이전처럼 경고가 아닌 인시던트에 플레이북을 할당하는 것 외에도, 한 번에 여러 분석 규칙에 대한 응답을 자동화하고, 플레이북 없이도 인시던트에 자동으로 태그를 지정하거나, 할당하거나, 종료하고, 실행되는 작업의 순서를 제어할 수 있습니다. 자동화 규칙은 Azure Sentinel에서 자동화 사용을 간소화하고 인시던트 오케스트레이션 프로세스의 복잡한 워크플로를 단순화할 수 있습니다.

이러한 [자동화 규칙에 대한 전체 설명](automate-incident-handling-with-automation-rules.md)을 통해 자세히 알아보세요.

위에서 언급했듯이 이제 경고 트리거 외에 인시던트 트리거를 사용하여 플레이북을 활성화할 수 있습니다. 인시던트 트리거는 플레이북으로 작업할 입력 집합을 더 크게 제공하여(인시던트에는 경고 및 엔터티 데이터가 모두 포함되기 때문에) 대응 워크플로에 훨씬 더 강력한 성능과 유연성을 제공합니다. 인시던트 트리거 플레이북은 자동화 규칙에서 호출하여 활성화됩니다.

[플레이북의 향상된 기능](automate-responses-with-playbooks.md) 및 플레이북을 자동화 규칙과 함께 사용하여 [응답 워크플로를 정교하게 만드는](tutorial-respond-threats-playbook.md) 방법에 대해 자세히 알아보세요.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview"></a>새로운 경고 강화: 향상된 엔터티 매핑 및 사용자 지정 세부 정보(공개 미리 보기)

두 가지 새로운 방법으로 경고를 보강하여 보다 유용하고 유익하게 만들 수 있습니다.

먼저 엔터티 매핑을 다음 단계로 끌어 올립니다. 이제 사용자, 호스트 및 IP 주소에서 파일 및 프로세스, 사서함, Azure 리소스 및 IoT 디바이스에 이르기까지 거의 20가지의 엔터티를 매핑할 수 있습니다. 각 엔터티에 대해 여러 식별자를 사용하여 엔터티 고유의 식별을 강화할 수도 있습니다. 이를 통해 인시던트의 훨씬 더 풍부한 데이터 세트를 제공하여 보다 광범위한 상관 관계와 강력한 조사가 가능해집니다. 경고에서 [엔터티를 매핑하는 새로운 방법을 알아보세요](map-data-fields-to-entities.md).

[엔터티에 대해 자세히 알아보고](entities-in-azure-sentinel.md) [사용 가능한 엔터티 및 식별자의 전체 목록](entities-reference.md)을 확인하세요.

원시 이벤트의 세부 정보를 노출하도록 경고를 사용자 지정하여 조사 및 대응 기능을 더욱 강화할 수 있습니다. 인시던트에 대한 이벤트 콘텐츠 가시성을 제공하면 보안 위협을 조사하고 대처하는 기능을 강화하고 유연성을 제공할 수 있습니다. 경고에서 [사용자 지정 세부 정보를 노출하는 방법을 알아보세요](surface-custom-details-in-alerts.md).



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Azure Sentinel 통합 문서를 인쇄하거나 PDF로 저장

이제 Azure Sentinel 통합 문서를 인쇄하여 PDF로 내보내고 로컬에 저장하거나 공유할 수 있습니다.

통합 문서에서 옵션 메뉴 > :::image type="icon" source="media/whats-new/print-icon.png" border="false":::**내용 인쇄** 를 선택합니다. 그런 다음, 프린터를 선택하거나 필요에 따라 **PDF로 저장** 을 선택합니다.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="통합 문서를 인쇄하거나 PDF로 저장합니다.":::

자세한 내용은 [데이터 시각화 및 모니터링](monitor-your-data.md)을 참조하세요.

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>인시던트 필터 및 정렬 기본 설정이 세션에 저장됨(공개 미리 보기)

이제 인시던트 필터 및 정렬이 제품의 다른 영역으로 이동하는 동안에도 Azure Sentinel 세션 전체에 저장됩니다.
동일한 세션에 있는 한, Azure Sentinel의 [인시던트](investigate-cases.md) 영역으로 다시 돌아오면 필터와 정렬이 그대로 표시됩니다.

> [!NOTE]
> Azure Sentinel을 종료하거나 브라우저를 새로 고치면 인시던트 필터 및 정렬이 저장되지 않습니다.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender 인시던트 통합(공개 미리 보기)

Azure Sentinel의 [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) 인시던트 통합을 통해 모든 M365D 인시던트를 Azure Sentinel로 스트리밍하고 두 포털 간에 동기화 상태를 유지할 수 있습니다. M365D(이전의 Microsoft Threat Protection 또는 MTP)의 인시던트에는 연결된 모든 경고, 엔터티 및 관련 정보가 포함되어 있어서 Azure Sentinel에서 심사 및 예비 조사를 수행하기에 충분한 컨텍스트를 제공합니다. Sentinel에서는 인시던트가 M365D와 양방향 동기화된 상태로 유지되어 사고 조사에서 두 포털의 이점을 모두 활용할 수 있습니다.

Azure Sentinel과 Microsoft 365 Defender를 함께 사용하면 두 곳의 장점을 모두 활용할 수 있습니다. SIEM이 조직의 전체 정보 리소스 범위에 대해 제공하는 광범위한 인사이트와 Microsoft 365 리소스를 보호하기 위해 XDR이 제공하는 사용자 지정 및 맞춤형 심층적인 조사 기능이 함께 조정 및 동기화되어 원활한 SOC 운영에 활용할 수 있습니다.

자세한 내용은 [Azure Sentinel과 Microsoft 365 Defender 통합](microsoft-365-defender-sentinel-integration.md)을 참조하세요.

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Azure Policy를 사용하는 새로운 Microsoft 서비스 커넥터

[Azure Policy](../governance/policy/overview.md)는 정책을 사용하여 리소스의 속성을 적용하고 제어할 수 있는 Azure 서비스입니다. 정책을 사용하면 리소스가 IT 거버넌스 표준을 준수하도록 유지할 수 있습니다.

정책으로 제어할 수 있는 리소스 속성 중에는 진단 및 감사 로그를 만들고 처리하는 것이 있습니다. Azure Sentinel은 Azure Policy를 사용하여 Azure Sentinel에 수집하려는 로그가 있는 특정 유형의 모든(현재 및 미래) 리소스에 공통 진단 로그 설정 집합을 적용할 수 있습니다. Azure Policy를 활용하면 리소스마다 더 이상 진단 로그를 설정할 필요가 없습니다.

이제 다음 Azure 서비스에 Azure Policy 기반 커넥터를 사용할 수 있습니다.
- [Azure Key Vault](./data-connectors-reference.md#azure-key-vault)(공개 미리 보기)
- [Azure Kubernetes Service](./data-connectors-reference.md#azure-kubernetes-service-aks)(공개 미리 보기)
- [Azure SQL 데이터베이스/서버](./data-connectors-reference.md#azure-sql-databases)(GA)

고객은 특정 인스턴스에 대한 로그를 여전히 수동으로 보낼 수 있으며 정책 엔진을 사용할 필요가 없습니다.


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

- [Agari 피싱 방어 및 브랜드 보호](./data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview)
- [Akamai Security Events](./data-connectors-reference.md#akamai-security-events-preview)
- [Active Directory용 Alsid](./data-connectors-reference.md#alsid-for-active-directory)
- [Apache HTTP 서버](./data-connectors-reference.md#apache-http-server)
- [Aruba ClearPass](./data-connectors-reference.md#aruba-clearpass-preview)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](./data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](./data-connectors-reference.md#cisco-meraki-preview)
- [Cisco Umbrella](./data-connectors-reference.md#cisco-umbrella-preview)
- [Cisco UCS(Unified Computing System)](./data-connectors-reference.md#cisco-unified-computing-system-ucs-preview)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace(이전의 G Suite)](./data-connectors-reference.md#google-workspace-g-suite-preview)
- [Imperva WAF Gateway](./data-connectors-reference.md#imperva-waf-gateway-preview)
- [Juniper SRX](./data-connectors-reference.md#juniper-srx-preview)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](./data-connectors-reference.md#nxlog-dns-logs-preview)
- [NXLog Linux Audit](./data-connectors-reference.md#nxlog-linuxaudit-preview)
- [Onapsis 플랫폼](connect-data-sources.md)
- [Proofpoint On Demand Email Security(POD)](./data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Salesforce 서비스 클라우드](./data-connectors-reference.md#salesforce-service-cloud-preview)
- [SonicWall 방화벽](connect-data-sources.md)
- [Sophos Cloud Optix](./data-connectors-reference.md#sophos-cloud-optix-preview)
- [Squid Proxy](./data-connectors-reference.md#squid-proxy-preview)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](./data-connectors-reference.md#thycotic-secret-server-preview)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](./data-connectors-reference.md#vmware-esxi-preview)

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

자세한 내용은 [Azure SQL 데이터베이스 진단 및 감사 로그 연결](./data-connectors-reference.md#azure-sql-databases)을 참조하세요.

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365 커넥터(공개 미리 보기)

Azure Sentinel에 이제 Microsoft Dynamics 365용 커넥터가 제공됩니다. 이를 통해 Dynamics 365 애플리케이션의 사용자, 관리자 및 지원 활동 로그를 Azure Sentinel에 수집할 수 있습니다. 이러한 데이터를 사용하여, 발생하는 데이터 처리 작업 전체를 감사하고 보안 위반 가능성을 분석할 수 있습니다.

자세한 내용은 [Dynamics 365 활동 로그를 Azure Sentinel에 연결](./data-connectors-reference.md#dynamics-365)을 참조하세요.

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