---
title: Azure Sentinel에서 위협 인텔리전스 통합 | Microsoft Docs
description: Azure Sentinel로 위협 인텔리전스 피드를 통합하고 사용하는 다양한 방법에 대해 알아봅니다.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2021
ms.author: yelevin
ms.openlocfilehash: ba9d853b6c4bcbe43b81463870a06821dc34d665
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277616"
---
# <a name="threat-intelligence-integration-in-azure-sentinel"></a>Azure Sentinel에서 위협 인텔리전스 통합

Azure Sentinel은 [위협 인텔리전스 피드를 사용](work-with-threat-indicators.md)하여 알려진 위협을 탐지하고 우선 순위를 지정하는 보안 분석가의 기능을 향상시키는 몇 가지 다른 방법을 제공합니다. 

사용 가능한 여러 통합 [TIP(위협 인텔리전스 플랫폼) 제품](connect-threat-intelligence-tip.md) 중 하나를 사용할 수 있고, [TAXII 서버에 연결](connect-threat-intelligence-taxii.md)하여 STIX 호환 위협 인텔리전스 원본을 사용할 수 있으며, [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator)와 직접 통신할 수 있는 사용자 지정 솔루션을 사용할 수도 있습니다. 

직접 조사 및 대응 작업에 도움이 될 수 있는 TI 정보로 인시던트 보강을 위해 플레이북에서 위협 인텔리전스 원본에 연결할 수도 있습니다.

> [!TIP]
> [MSSP(관리형 서비스 공급자)](mssp-protect-intellectual-property.md)와 같이 동일한 테넌트에 여러 작업 영역이 있는 경우 중앙 작업 영역에만 위협 지표를 연결하는 것이 더 비용 효율적일 수 있습니다.
>
> 각각의 개별 작업 영역으로 가져온 동일한 위협 지표 세트가 있는 경우 작업 영역 간 쿼리를 실행하여 작업 영역에서 위협 지표를 집계할 수 있습니다. MSSP 인시던트 검색, 조사, 헌팅 환경 내에서 상호 연결합니다.
>

## <a name="taxii-threat-intelligence-feeds"></a>TAXII 위협 인텔리전스 피드

TAXII 위협 인텔리전스 피드에 연결하려면 아래에 링크된 각 공급업체에서 제공한 데이터와 함께 [Azure Sentinel을 STIX/TAXII 위협 인텔리전스 피드에 연결](connect-threat-intelligence-taxii.md) 지침을 따르세요. 커넥터와 함께 사용하는 데 필요한 데이터를 얻으려면 공급업체에 직접 문의해야 할 수 있습니다.

### <a name="anomali-limo"></a>Anomali Limo

- [Anomali Limo 피드에 연결하는 데 필요한 내용을 확인합니다](https://www.anomali.com/resources/limo).

### <a name="cybersixgill-darkfeed"></a>Cybersixgill Darkfeed

- [Azure Sentinel과 Cybersix 통합에 대해 알아봅니다@Cybersixgill](https://www.cybersixgill.com/partners/azure-sentinel/)
- Azure Sentinel을 Cybersixgill TAXII 서버에 연결하고 Darkfeed에 액세스하려면 [Cybersix에 연결](mailto://azuresentinel@cybersixgill.com)해서 API 루트, 컬렉션 ID, 사용자 이름 및 암호를 획득합니다.

### <a name="financial-services-information-sharing-and-analysis-center-fs-isac"></a>FS-ISAC(금융 서비스 정보 공유 및 분석 센터)

- [FS-ISAC](https://www.fsisac.com/membership?utm_campaign=ThirdParty&utm_source=MSFT&utm_medium=ThreatFeed-Join)에 조인하여 이 피드에 액세스할 자격 증명을 얻습니다.

### <a name="health-intelligence-sharing-community-h-isac"></a>H-ISAC(상태 인텔리전스 공유 커뮤니티)

- [H-ISAC](https://h-isac.org/soltra/)에 조인하여 이 피드에 액세스할 자격 증명을 얻습니다.

### <a name="ibm-x-force"></a>IBM X-Force

- [IBM X-Force 통합에 대해 자세히 알아보기](https://www.ibm.com/security/xforce)

### <a name="intsights"></a>IntSights

- [Azure Sentinel과 IntSights 통합에 대해 자세히 알아봅니다@IntSights](https://intsights.com/resources/intsights-microsoft-azure-sentinel)
- Azure Sentinel을 IntSights TAXII 서버에 연결하려면 Azure Sentinel로 보내려는 데이터의 정책을 구성한 후 IntSights 포털에서 API 루트, 컬렉션 ID, 사용자 이름 및 암호를 가져옵니다.

### <a name="threatconnect"></a>ThreatConnect

- [STIX 및 TAXII에 대해 더 알아보기@ThreatConnect](https://threatconnect.com/stix-taxii/)
- [TAXII 서비스 설명서 @ThreatConnect](https://docs.threatconnect.com/en/latest/rest_api/v2/taxii/taxii.html)

## <a name="integrated-threat-intelligence-platform-products"></a>통합 위협 인텔리전스 플랫폼 제품

TIP(위협 인텔리전스 플랫폼) 피드에 연결하려면 지침에 따라 [Azure Sentinel에 위협 인텔리전스 플랫폼을 연결](connect-threat-intelligence-tip.md)합니다. 이 지침의 두 번째 파트에서는 TIP 솔루션에 정보를 입력하는 방법을 알려드립니다. 자세한 정보는 아래 링크를 참조하세요.

### <a name="agari-phishing-defense-and-brand-protection"></a>Agari 피싱 방어 및 브랜드 보호

- [Agari 피싱 방어 및 브랜드 보호](https://agari.com/products/phishing-defense/)에 연결하려면 Azure Sentinel에서 기본 제공하는 [Agari 데이터 커넥터](./data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview)를 사용합니다.

### <a name="anomali-threatstream"></a>Anomali ThreatStream

- [ThreatStream 통합자 및 확장](https://www.anomali.com/products/threatstream)과 ThreatStream 인텔리전스를 Microsoft Graph 보안 API에 연결하기 위한 지침을 다운로드하려면 [ThreatStream 다운로드](https://ui.threatstream.com/downloads) 페이지를 참조하세요.

### <a name="alienvault-open-threat-exchange-otx-from-att-cybersecurity"></a>AT&T Cybersecurity에서의 AlienVault OTX(Open Threat Exchange)

- [AlienVault OTX](https://otx.alienvault.com/)는 Azure Logic Apps(플레이북)를 사용하여 Azure Sentinel에 연결합니다. 전체 제안을 최대한 활용하는 데 필요한 [특수 지침](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566)을 참조하세요.

### <a name="eclecticiq-platform"></a>EclecticIQ 플랫폼

- EclecticIQ 플랫폼은 Azure Sentinel과 통합되어 위협 탐지, 헌팅, 응답을 향상시킵니다. 이 양방향 통합의 [이점 및 사용 사례](https://www.eclecticiq.com/resources/azure-sentinel-and-eclecticiq-intelligence-center)에 관해 자세히 알아보세요.

### <a name="groupib-threat-intelligence-and-attribution"></a>GroupIB 위협 인텔리전스 및 특성

- Azure Sentinel에 [GroupIB 위협 인텔리전스 및 특성](https://www.group-ib.com/intelligence-attribution.html)을 연결하기 위해 GroupIB는 Azure Logic Apps을 사용합니다. 전체 제안을 최대한 활용하는 데 필요한 [특수 지침](https://techcommunity.microsoft.com/t5/azure-sentinel/group-ib-threat-intelligence-and-attribution-connector-azure/ba-p/2252904)을 참조하세요.

### <a name="misp-open-source-threat-intelligence-platform"></a>MISP 오픈 소스 위협 인텔리전스 플랫폼

- 클라이언트에 MISP 인스턴스를 제공하여 위협 표시기를 Microsoft Graph 보안 API로 마이그레이션하는 샘플 스크립트는 [Microsoft Graph 보안 스크립트에 대한 MISP](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)를 참조하세요.
- [MISP 프로젝트에 대해 자세히 알아보세요](https://www.misp-project.org/).

### <a name="palo-alto-networks-minemeld"></a>Palo Alto Networks MineMeld

- Azure Sentinel 연결 정보를 사용하여 [Palo Alto MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)를 구성하려면 [MineMeld를 사용하여 Microsoft Graph 보안 API에 IOC 전송](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540) 문서를 참조한 후 **MineMeld 구성** 제목으로 넘어가세요.

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future 보안 인텔리전스 플랫폼

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/)는 Azure Logic Apps(플레이북)를 사용하여 Azure Sentinel과 연결합니다. 전체 제안을 최대한 활용하는 데 필요한 [특수 지침](https://go.recordedfuture.com/hubfs/partners/microsoft-azure-installation-guide.pdf)을 참조하세요.

### <a name="threatconnect-platform"></a>ThreatConnect 플랫폼

- 지침 [Microsoft Graph 보안 위협 표시기 통합 구성 가이드](https://training.threatconnect.com/learn/article/microsoft-graph-security-threat-indicators-integration-configuration-guide-kb-article)를 참조하여 Azure Sentinel에 [ThreatConnect](https://threatconnect.com/solution/)를 연결하세요.

### <a name="threatquotient-threat-intelligence-platform"></a>ThreatQuotient 위협 인텔리전스 플랫폼

- 문서 [ThreatQ 통합을 위한 Microsoft Sentinel 커넥터](https://azuremarketplace.microsoft.com/marketplace/apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?tab=overview)에서 [ThreatQuotient TIP](https://www.threatq.com/)를 Azure Sentinel과 연결하는 데 필요한 지원 정보와 지침을 확인할 수 있습니다.

## <a name="incident-enrichment-sources"></a>인시던트 보강 소스

위협 지표를 가져오는 데 사용되는 용도 외에도 위협 인텔리전스 피드는 인시던트에서 정보를 보강하고 조사에 더 많은 컨텍스트를 제공하는 소스 역할을 할 수 있습니다. 다음 피드는 이러한 용도로 사용되며 [자동 인시던트 응답](automate-responses-with-playbooks.md)에 사용할 논리 앱 플레이북을 제공합니다.

### <a name="hyas-insight"></a>HYAS 인사이트

- [HYAS 인사이트](https://www.hyas.com/hyas-insight)에 대한 인시던트 보강 플레이북을 Azure Sentinel [GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)에서 찾아 사용하도록 설정합니다. "Enrich-Sentinel-Incident-HYAS-Insight-"로 시작하는 하위 폴더를 검색합니다.
- HYAS 인사이트 논리 앱 [커넥터 설명서](/connectors/hyasinsight/)를 참조하세요.

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future 보안 인텔리전스 플랫폼

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/)에 대한 인시던트 보강 플레이북을 Azure Sentinel [GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)에서 찾아 사용하도록 설정합니다. "RecordedFuture_"로 시작하는 하위 폴더를 검색합니다.
- Recorded Future 논리 앱 [커넥터 설명서](/connectors/recordedfuture/)를 참조하세요.

### <a name="reversinglabs-titaniumcloud"></a>ReversingLabs TitaniumCloud

- [ReversingLabs](https://www.reversinglabs.com/products/file-reputation-service) 에 대한 인시던트 보강 플레이북을 Azure Sentinel [GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/ReversingLabs/Playbooks/Enrich-SentinelIncident-ReversingLabs-File-Information)에서 찾아 사용하도록 설정합니다.
- ReversingLabs 인텔리전스 논리 앱 [커넥터 설명서](/connectors/reversinglabsintelligence/)를 참조하세요.

### <a name="riskiq-passive-total"></a>RiskIQ Passive Total

- [RiskIQ Passive Total](https://www.riskiq.com/products/passivetotal/) 에 대한 인시던트 보강 플레이북을 Azure Sentinel [GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)에서 찾아 사용하도록 설정합니다. "Enrich-SentinelIncident-RiskIQ-"로 시작하는 하위 폴더를 검색합니다.
- RiskIQ 플레이북 사용에 대한 [자세한 정보](https://techcommunity.microsoft.com/t5/azure-sentinel/enrich-azure-sentinel-security-incidents-with-the-riskiq/ba-p/1534412)를 참조하세요.
- RiskIQ PassiveTotal 논리 앱 [커넥터 설명서](/connectors/riskiqpassivetotal/)를 참조하세요.

### <a name="virus-total"></a>Virus Total

- [Virus Total](https://developers.virustotal.com/v3.0/reference)에 대한 인시던트 보강 플레이북을 Azure Sentinel [GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)에서 찾아 사용하도록 설정합니다. "Get-VirusTotal" 및 "Get-VTURL"로 시작하는 하위 폴더를 검색합니다.
- Virus Total 논리 앱 [커넥터 설명서](/connectors/virustotal/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 위협 인텔리전스 공급자를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 잠재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
