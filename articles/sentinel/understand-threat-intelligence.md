---
title: Microsoft Sentinel | 위협 인텔리전스 이해 Microsoft Docs
description: Microsoft Sentinel에서 위협 인텔리전스 피드를 연결, 관리 및 사용하여 데이터를 분석하고, 위협을 검색하고, 경고를 보강하는 방법을 이해합니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 09835e201417040d3f8db6fd2387f325b2728e9d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516953"
---
# <a name="understand-threat-intelligence-in-microsoft-sentinel"></a>Microsoft Sentinel의 위협 인텔리전스 이해

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="introduction-to-threat-intelligence"></a>위협 인텔리전스 소개

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

CTI(사이버 위협 인텔리전스)는 시스템 및 사용자에게 알려진 기존의 또는 잠재적인 위협을 설명하는 정보입니다. 이 유형의 정보는 특정 위협 행위자의 동기, 인프라, 기술을 상세히 기술한 보고서부터 알려진 사이버 위협과 관련된 IP 주소, 도메인, 파일 해시 및 기타 아티팩트에 대한 특정 관찰까지 다양한 형태를 취합니다. 조직은 CTI를 사용하여 비정상적인 활동에 대한 중요한 컨텍스트를 제공하여 보안 담당자가 사람, 정보 및 기타 자산을 신속하게 보호할 수 있도록 합니다. CTI는 오픈 소스 데이터 피드, 위협 인텔리전스 공유 커뮤니티, 상용 인텔리전스 피드, 조직 내 보안 조사 과정에서 수집된 로컬 인텔리전스 등의 여러 위치에서 원본으로 사용할 수 있습니다.

Microsoft Sentinel과 같은 SIEM(보안 정보 및 이벤트 관리) 솔루션 내에서 가장 일반적으로 사용되는 형태의 CTI는 손상 지표 또는 IoC라고도 하는 위협 지표입니다. 위협 지표는 URL, 파일 해시, IP 주소 등의 관찰된 아티팩트를 피싱, 봇네트, 맬웨어 등의 알려진 위협 활동과 연결하는 데이터입니다. 이러한 형태의 위협 인텔리전스는 조직에 대한 잠재적 위협을 감지하고 이를 보호하기 위해 보안 제품 및 자동화에 대규모로 적용할 수 있기 때문에 종종 전략적 위협 *인텔리전스라고* 합니다. Microsoft Sentinel에서는 위협 지표를 사용하여 사용자 환경에서 관찰된 악의적인 활동을 감지하고 보안 조사자에게 컨텍스트를 제공하여 대응 결정을 알릴 수 있습니다.

다음 활동을 통해 TI(위협 인텔리전스)를 Microsoft Sentinel에 통합합니다.

- 다양한 TI 플랫폼 및 피드에 **대한 데이터 커넥터를** 사용하도록 설정하여 **위협 인텔리전스를** Microsoft Sentinel로 [가져옵니다.](connect-threat-intelligence-taxii.md) [](connect-threat-intelligence-tip.md)

- 로그 및 Microsoft Sentinel의 위협 인텔리전스 블레이드에서 가져온 **위협 인텔리전스를** 보고 **관리합니다.** 

- 가져온 위협 인텔리전스를 기반으로 기본 제공 **분석** 규칙 템플릿을 사용하여 **위협을 탐지** 하고 보안 경고 및 인시던트를 생성합니다.

- **위협 인텔리전스 통합 문서** 를 사용하여 Microsoft Sentinel에서 가져온 위협 인텔리전스에 대한 주요 정보를 **시각화합니다.**

Microsoft는 다른 지표 세부 정보와 함께 표시되는 [GeoLocation 및 WhoIs 데이터](#view-your-geolocation-and-whois-data-enrichments-public-preview)를 사용하여 가져온 모든 위협 인텔리전스 지표를 보강합니다.

> [!TIP]
> 또한 위협 인텔리전스는 **헌팅** 및 Notebook과 같은 다른 Microsoft Sentinel 환경 내에서 유용한 **컨텍스트를 제공합니다.** 자세한 내용은 [Microsoft Sentinel의 Jupyter Notebook 및](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) [자습서: Microsoft Sentinel에서 Jupyter Notebook 및 MSTICPy 시작을 참조하세요.](notebook-get-started.md)
>

## <a name="import-threat-intelligence-with-data-connectors"></a>데이터 커넥터를 사용하여 위협 인텔리전스 가져오기

Microsoft Sentinel의 다른 모든 이벤트 데이터와 마찬가지로 데이터 커넥터를 사용하여 위협 지표를 가져옵니다. Microsoft Sentinel에는 위협 지표를 위해 특별히 제공되는 두 개의 데이터 커넥터, 즉 업계 표준 STIX/TAXII 피드용 **위협 인텔리전스 - TAXII** 및 통합 및 큐레이팅된 TI 피드용 **위협 인텔리전스 플랫폼이** 있습니다. 조직에서 위협 지표의 원본으로 사용하는 위치에 따라 데이터 커넥터를 단독으로 사용하거나 두 커넥터를 함께 사용할 수 있습니다.

Microsoft Sentinel에서 사용할 수 있는 [위협 인텔리전스 통합 카탈로그를](threat-intelligence-integration.md) 참조하세요.

### <a name="add-threat-indicators-to-microsoft-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>위협 인텔리전스 플랫폼 데이터 커넥터를 사용하여 Microsoft Sentinel에 위협 지표 추가

많은 조직에서는 TIP(위협 인텔리전스 플랫폼) 솔루션을 사용하여 다양한 원본에서 위협 지표 피드를 집계하고, 플랫폼 내에서 데이터를 큐레이팅한 다음, 네트워크 디바이스, EDR/XDR 솔루션 또는 Microsoft Sentinel과 같은IEM과 같은 다양한 보안 솔루션에 적용할 위협 지표를 선택합니다. 조직에서 통합 [TIP 솔루션](connect-threat-intelligence-tip.md)를 사용하는 경우 **위협 인텔리전스 플랫폼 데이터 커넥터를** 사용하면 TIP을 사용하여 위협 지표를 Microsoft Sentinel로 가져올 수 있습니다.

TIP 데이터 커넥터는 [Microsoft Graph Security tiIndicators API와](/graph/api/resources/tiindicator) 함께 작동하므로 tiIndicators API와 통신하는 모든 사용자 지정 위협 인텔리전스 플랫폼에서 지표를 Microsoft Sentinel(및 Microsoft 365 Defender 같은 다른 Microsoft 보안 솔루션)에 보낼 수도 있습니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="위협 인텔리전스 가져오기 경로":::

Microsoft Sentinel과 통합된 TIP 솔루션에 대한 자세한 내용은 [통합 위협 인텔리전스 플랫폼 제품 을 참조하세요.](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products)

**통합 TIP 또는 사용자 지정 위협 인텔리전스 플랫폼에서 Microsoft Sentinel로 위협 지표를 가져오려면:**

1. Azure Active Directory에서 **애플리케이션 ID** 및 **클라이언트 암호** 가져오기

1. TIP 솔루션 또는 사용자 지정 애플리케이션에 이 정보를 입력

1. Microsoft Sentinel에서 위협 인텔리전스 플랫폼 데이터 커넥터 사용

자세한 내용은 [Microsoft Sentinel에 위협 인텔리전스 플랫폼 커넥트 참조하세요.](connect-threat-intelligence-tip.md)

### <a name="add-threat-indicators-to-microsoft-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>위협 인텔리전스 - TAXII 데이터 커넥터를 사용하여 Microsoft Sentinel에 위협 지표 추가

위협 인텔리전스 전송을 위한 가장 널리 채택된 업계 표준은 [STIX 데이터 형식과 TAXII 프로토콜의 조합](https://oasis-open.github.io/cti-documentation/)입니다. 조직에서 현재 STIX/TAXII 버전(2.0 또는 2.1)을 지원하는 솔루션에서 위협 지표를 가져오는 경우 **위협 인텔리전스 - TAXII** 데이터 커넥터를 사용하여 위협 지표를 Microsoft Sentinel로 가져올 수 있습니다. 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하면 Microsoft Sentinel의 기본 제공 TAXII 클라이언트가 TAXII 2.x 서버에서 위협 인텔리전스를 가져올 수 있습니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 가져오기 경로":::

**TAXII 서버에서 STIX 형식 위협 지표를 Microsoft Sentinel로 가져오려면:**

1. TAXII 서버 API 루트 및 컬렉션 ID 가져오기

1. Microsoft Sentinel에서 위협 인텔리전스 - TAXII 데이터 커넥터 사용

자세한 내용은 [stIX/TAXII 위협 인텔리전스 피드에 Microsoft Sentinel 커넥트 참조하세요.](connect-threat-intelligence-taxii.md)

## <a name="view-and-manage-your-threat-indicators"></a>위협 지표 확인 및 관리

모든 Microsoft Sentinel 이벤트 데이터가 저장되는 **로그의** **ThreatIntelligenceIndicator** 테이블(Microsoft **Sentinel** 그룹 아래)에서 사용된 원본 피드 또는 커넥터에 관계없이 가져온 위협 지표를 볼 수 있습니다. 이 표는 분석 및 통합 문서와 같은 다른 Microsoft Sentinel 기능에서 수행하는 위협 인텔리전스 쿼리의 기초입니다.

결과는 아래에 표시된 샘플 위협 지표와 비슷합니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="샘플 쿼리 데이터":::

기본 Microsoft Sentinel 메뉴에서 액세스할 수 있는 새 **위협 인텔리전스** 블레이드에서 표시기를 보고 관리할 수도 있습니다. 심지어 Log Analytics 쿼리를 작성하지 않고도 가져온 위협 지표를 정렬, 필터링 및 검색할 수 있습니다. 또한 이 기능을 사용하면 Microsoft Sentinel 인터페이스 내에서 직접 위협 지표를 만들 수 있을 뿐만 아니라 가장 일반적인 두 가지 위협 인텔리전스 관리 작업인 표시기 태그 지정 및 보안 조사와 관련된 새 지표 만들기를 수행할 수 있습니다.

위협 지표에 태그를 지정하면 지표를 함께 그룹화하여 손쉽게 찾을 수 있습니다. 일반적으로 특정 인시던트와 관련된 지표 또는 알려진 행위자나 잘 알려진 공격 캠페인의 위협을 나타내는 지표에 태그를 적용합니다. 위협 지표에 각각 태그를 지정할 수도 있고 여러 개를 선택하여 한 번에 모두 태그를 지정할 수도 있습니다. 인시던트 ID를 사용하여 여러 지표에 태그를 지정하는 예제는 다음과 같습니다. 태그 지정은 특정한 형식이 없기 때문에, 위협 지표 태그에 대한 표준 명명 규칙을 만드는 것이 좋습니다. 각 지표에 여러 태그를 적용할 수 있습니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="위협 지표에 태그 적용" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

위협 지표 보기 및 관리에 대한 자세한 내용은 [Microsoft Sentinel에서 위협 지표 작업을](work-with-threat-indicators.md#view-your-threat-indicators-in-microsoft-sentinel)참조하세요.

### <a name="view-your-geolocation-and-whois-data-enrichments-public-preview"></a>GeoLocation 및 WhoIs 데이터 보강 보기(공개 미리 보기)

Microsoft는 추가 GeoLocation 및 WhoIs 데이터로 각 지표를 보강하여 선택한 IOC(손상 지표)가 있는 조사를 위한 더 많은 컨텍스트를 제공합니다.

Microsoft Sentinel로 가져온 손상의 각 지표에 대한 **위협 인텔리전스** 창에서 GeoLocation 및 WhoIs 데이터를 볼 수 있습니다.

예를 들어 GeoLocation 데이터를 사용하여 지표에 대한 *조직* 또는 *국가* 및 WhoIs 데이터와 같은 세부 정보를 찾아 *등록자* 및 *레코드 만들기* 데이터와 같은 데이터를 찾습니다.

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>위협 지표 기반 분석을 사용하여 위협 탐지

Microsoft Sentinel과 같은 SIEM 솔루션의 위협 지표에 대한 가장 중요한 사용 사례는 위협 탐지에 대한 분석 규칙을 구동하는 것입니다. 이러한 지표 기반 규칙은 데이터 원본의 원시 이벤트를 위협 지표와 비교하여 조직에서 보안 위협을 탐지합니다. Microsoft Sentinel **Analytics** 에서 일정에 따라 실행되고 보안 경고를 생성하는 분석 규칙을 만듭니다. 이러한 규칙은 규칙 실행 빈도, 보안 경고 및 인시던트를 생성하는 쿼리 결과의 종류, 경고에 대한 대응으로 트리거할 자동화를 결정하는 구성과 함께 쿼리를 통해 작동합니다.

언제든지 처음부터 새 분석 규칙을 만들 수 있지만 Microsoft Sentinel은 Microsoft 보안 엔지니어가 만든 기본 제공 규칙 템플릿 집합을 제공하며, 이 템플릿을 있는 그대로 사용하거나 필요에 맞게 수정할 수 있습니다. 위협 지표를 사용하는 규칙 템플릿은 모두 '**TI map**...'으로 시작하므로 쉽게 식별할 수 있습니다. 이러한 모든 규칙 템플릿은 사용하는 위협 지표 유형(도메인, 이메일, 파일 해시, IP 주소 또는 URL) 및 일치시킬 이벤트 유형을 제외하고 유사하게 작동합니다. 각 템플릿에는 규칙이 작동하는 데 필요한 필수 데이터 원본이 나열되므로 Microsoft Sentinel에서 이미 가져온 필수 이벤트가 있는지 한눈에 볼 수 있습니다. 기존 규칙 템플릿을 편집하고 저장하거나 새 규칙을 만들 때 기본적으로 사용하도록 설정됩니다.

사용 가능한 규칙은 Microsoft Sentinel의 **분석** 섹션에 있는 **활성 규칙** 탭에서 찾을 수 있습니다. 여기에서 활성 규칙을 편집, 활성화, 비활성화, 복제 또는 삭제할 수 있습니다. 새 규칙은 활성화되는 즉시 실행되며, 그 다음에는 정의된 일정에 따라 실행됩니다.

규칙이 기본 설정의 일정에 따라 실행될 때마다 검색된 결과가 있으면 보안 경고가 생성됩니다. Microsoft Sentinel의 보안 경고는 Microsoft Sentinel 그룹의 **SecurityAlert** 테이블에 있는 Microsoft **Sentinel의** **로그** 섹션에서 볼 수 있습니다.

Microsoft Sentinel에서 분석 규칙에서 생성된 경고는 Microsoft Sentinel 메뉴의 위협 **관리** 아래 **인시던트에서** 찾을 수 있는 보안 인시던트도 생성합니다. 인시던트는 보안 운영 팀이 적절한 대응 조치를 결정하기 위해 선별 및 조사하는 것입니다. 자세한 내용은 이 [자습서: Microsoft Sentinel을 사용하여 인시던트 조사를](./investigate-cases.md)참조하세요.

분석 규칙에서 위협 지표 사용에 대한 자세한 내용은 [Microsoft Sentinel에서 위협 지표 작업을](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics)참조하세요.

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>통합 문서는 위협 인텔리전스에 대한 정보를 제공합니다.

통합 문서에서는 Microsoft Sentinel의 모든 측면에 대한 인사이트를 제공하는 강력한 대화형 대시보드를 제공하며 위협 인텔리전스도 예외는 아닙니다. 기본 제공 **위협 인텔리전스 통합 문서** 를 사용하여 위협 인텔리전스에 대한 주요 정보를 시각화할 수 있으며, 통합 문서를 비즈니스 요구 사항에 따라 쉽게 사용자 지정할 수 있습니다. 뿐만 아니라 다양한 데이터 원본을 결합하여 새 대시보드를 만들 수도 있으므로 고유한 방식으로 데이터를 시각화할 수 있습니다. Microsoft Sentinel 통합 문서는 Azure Monitor 통합 문서를 기반으로 하며, 이미 광범위한 설명서와 더 많은 템플릿을 사용할 수 있습니다. [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/visualize/workbooks-overview.md) 방법에 대한 이 문서에서 시작하는 것이 좋습니다.

또한 GitHub에는 추가 템플릿을 다운로드하고 자체 템플릿을 제공할 수 있는 활발한 [Azure Monitor 통합 문서](https://github.com/microsoft/Application-Insights-Workbooks) 커뮤니티가 있습니다.

위협 인텔리전스 통합 문서를 사용하고 사용자 지정하는 자세한 내용은 [Microsoft Sentinel에서 위협 지표 작업을](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence)참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 위협 인텔리전스 블레이드를 포함하여 Microsoft Sentinel의 위협 인텔리전스 기능에 대해 배웠습니다. Microsoft Sentinel의 위협 인텔리전스 기능 사용에 대한 실제 지침은 다음 문서를 참조하세요.

- Microsoft Sentinel을 [STIX/TAXII 위협 인텔리전스 피드로 커넥트.](./connect-threat-intelligence-taxii.md)
- 위협 인텔리전스 플랫폼을 Microsoft Sentinel에 [커넥트.](./connect-threat-intelligence-tip.md)
- Microsoft Sentinel과 쉽게 통합할 수 있는 [TIP 플랫폼, TAXII 피드 및 보강을](threat-intelligence-integration.md) 확인합니다.
- Microsoft Sentinel 환경 전체에서 [위협 지표를 사용하여 작업합니다.](work-with-threat-indicators.md)
- Microsoft Sentinel에서 [기본 제공](./detect-threats-built-in.md) 또는 [사용자 지정](./detect-threats-custom.md) 분석 규칙을 사용하여 위협 검색
- Microsoft Sentinel에서 [인시던트 조사](./investigate-cases.md)
