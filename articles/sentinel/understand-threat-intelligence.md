---
title: Azure Sentinel의 위협 인텔리전스 이해 | Microsoft Docs
description: 위협 인텔리전스 피드를 Azure Sentinel에 연결하고, 관리하고, 사용하여 데이터를 분석하고, 위협을 탐지하고, 경고를 보강하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: 6ab9ecbe3b67ec933604ab1d8f6efdc7dbd8a5af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528760"
---
# <a name="understand-threat-intelligence-in-azure-sentinel"></a>Azure Sentinel의 위협 인텔리전스 이해

## <a name="introduction-to-threat-intelligence"></a>위협 인텔리전스 소개

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

CTI(사이버 위협 인텔리전스)는 시스템 및 사용자에게 알려진 기존의 또는 잠재적인 위협을 설명하는 정보입니다. 이 유형의 정보는 특정 위협 행위자의 동기, 인프라, 기술을 상세히 기술한 보고서부터 알려진 사이버 위협과 관련된 IP 주소, 도메인, 파일 해시 및 기타 아티팩트에 대한 특정 관찰까지 다양한 형태를 취합니다. 조직은 CTI를 사용하여 비정상적인 활동에 대한 중요한 컨텍스트를 제공하여 보안 담당자가 사람, 정보 및 기타 자산을 신속하게 보호할 수 있도록 합니다. CTI는 오픈 소스 데이터 피드, 위협 인텔리전스 공유 커뮤니티, 상용 인텔리전스 피드, 조직 내 보안 조사 과정에서 수집된 로컬 인텔리전스 등의 여러 위치에서 원본으로 사용할 수 있습니다.

Azure Sentinel과 같은 SIEM(보안 정보 및 이벤트 관리) 솔루션 내에서 가장 많이 사용되는 CTI 형태는 침해 지표 또는 IoC(손상 지표)라고도 하는 위협 지표입니다. 위협 지표는 URL, 파일 해시, IP 주소 등의 관찰된 아티팩트를 피싱, 봇네트, 맬웨어 등의 알려진 위협 활동과 연결하는 데이터입니다. 이러한 형태의 위협 인텔리전스는 조직에 대한 잠재적 위협을 탐지하고 방어하기 위해 보안 제품 및 자동화에 대규모로 적용할 수 있기 때문에 보통 전략적 위협 인텔리전스라고 합니다. Azure Sentinel에서는 위협 지표를 사용하여 환경에서 관찰된 악의적인 활동을 탐지하고 보안 분석가에게 컨텍스트를 제공하여 대응 결정을 전파할 수 있습니다.

다음 작업을 통해 TI(위협 인텔리전스)를 Azure Sentinel로 통합할 수 있습니다.

- 다양한 TI [플랫폼](connect-threat-intelligence-tip.md) 및 [피드](connect-threat-intelligence-taxii.md)에 **데이터 커넥터** 를 사용하여 Azure Sentinel로 **위협 인텔리전스를 가져옵니다**.
- **로그** 및 Azure Sentinel의 **위협 인텔리전스** 블레이드에서 가져온 위협 인텔리전스를 **확인 및 관리** 합니다.
- 가져온 위협 인텔리전스를 기반으로 기본 제공 **분석** 규칙 템플릿을 사용하여 **위협을 탐지** 하고 보안 경고 및 인시던트를 생성합니다.
- **위협 인텔리전스 통합 문서** 를 사용하여 Azure Sentinel에서 가져온 위협 인텔리전스에 대한 **중요 정보를 시각화** 합니다.

또한 위협 인텔리전스는 **헌팅** 및 **Notebook** 같은 다른 Azure Sentinel 환경 내에서 유용한 컨텍스트를 제공하며, 이 문서에서는 다루지 않지만 Notebook 내에서 CTI 사용에 대해 설명하는 [Azure Sentinel의 Jupyter Notebook](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)에 대한 Ian Hellen의 유용한 블로그 게시물에서 이러한 환경을 다룹니다.

## <a name="import-threat-intelligence-with-data-connectors"></a>데이터 커넥터를 사용하여 위협 인텔리전스 가져오기

Azure Sentinel의 다른 모든 이벤트 데이터와 마찬가지로 위협 지표는 데이터 커넥터를 사용하여 가져옵니다. Azure Sentinel은 산업 표준 STIX/TAXII 피드를 위한 **위협 인텔리전스 - TAXII** 와 통합되고 큐레이션된 TI 피드를 위한 **위협 인텔리전스 플랫폼** 이라는 두 가지 데이터 커넥터를 위협 지표 전용으로 제공합니다. 조직에서 위협 지표의 원본으로 사용하는 위치에 따라 데이터 커넥터를 단독으로 사용하거나 두 커넥터를 함께 사용할 수 있습니다. 

Azure Sentinel에서 사용할 수 있는 [위협 인텔리전스 통합](threat-intelligence-integration.md) 카탈로그를 참조하세요.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>위협 인텔리전스 플랫폼 데이터 커넥터를 사용하여 Azure Sentinel에 위협 지표 추가

많은 조직에서는 TIP(위협 인텔리전스 플랫폼) 솔루션을 사용하여 다양한 원본에서 위협 지표 피드를 집계하고, 플랫폼 내에서 데이터를 선별한 다음, 네트워크 디바이스, EDR/XDR 솔루션 또는 SIEM(예: Azure Sentinel) 같은 다양한 보안 솔루션에 적용할 위협 지표를 선택합니다. 조직에서 [통합 TIP 솔루션](connect-threat-intelligence-tip.md)을 사용하는 경우 **위협 인텔리전스 플랫폼 데이터 커넥터** 를 사용하면 조직의 TIP을 사용하여 위협 지표를 Azure Sentinel로 가져올 수 있습니다. 

TIP 데이터 커넥터는 [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator)와 함께 작동하여 이 작업을 수행하므로, tiIndicators API와 통신하는 사용자 지정 위협 인텔리전스 플랫폼에서도 이 커넥터를 사용하여 Azure Sentinel(및 Microsoft 365 Defender 같은 다른 Microsoft 보안 솔루션)로 지표를 보낼 수 있습니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="위협 인텔리전스 가져오기 경로":::

Azure Sentinel과 통합된 TIP 솔루션에 대한 자세한 내용은 [통합 위협 인텔리전스 플랫폼 제품](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products)을 참조하세요.

다음은 통합 TIP 또는 사용자 지정 위협 인텔리전스 솔루션에서 Azure Sentinel로 위협 지표를 가져오려면 수행해야 하는 주요 단계입니다.

1. Azure Active Directory에서 **애플리케이션 ID** 및 **클라이언트 암호** 가져오기

1. TIP 솔루션 또는 사용자 지정 애플리케이션에 이 정보를 입력

1. Azure Sentinel에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용하도록 설정

각 단계에 대한 자세한 내용은 [Azure Sentinel에 위협 인텔리전스 플랫폼 연결](connect-threat-intelligence-tip.md)을 참조하세요.


### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>위협 인텔리전스 - TAXII 데이터 커넥터를 사용하여 Azure Sentinel에 위협 지표 추가

위협 인텔리전스 전송을 위한 가장 널리 채택된 업계 표준은 [STIX 데이터 형식과 TAXII 프로토콜의 조합](https://oasis-open.github.io/cti-documentation/)입니다. 조직이 현재 STIX/TAXII 버전(2.0 또는 2.1)을 지원하는 솔루션에서 위협 지표를 가져오는 경우 **위협 인텔리전스 - TAXII** 데이터 커넥터를 사용하여 Azure Sentinel로 위협 지표를 가져올 수 있습니다. 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하면 Azure Sentinel에서 기본 제공 TAXII 클라이언트를 사용하여 TAXII 2.x 서버로부터 위협 인텔리전스를 가져올 수 있습니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 가져오기 경로":::
 
TAXII 서버에서 Azure Sentinel로 STIX 형식 위협 지표를 가져오려면 다음 단계를 수행합니다.

1. TAXII 서버 API 루트 및 컬렉션 ID 가져오기

1. Azure Sentinel에서 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하도록 설정

각 단계에 대한 자세한 내용은 [STIX/TAXII 위협 인텔리전스 피드에 Azure Sentinel 연결](connect-threat-intelligence-taxii.md)을 참조하세요.

## <a name="view-and-manage-your-threat-indicators"></a>위협 지표 확인 및 관리

사용한 소스 피드 또는 커넥터에 관계없이, 모든 Azure Sentinel 이벤트 데이터가 저장되는 **로그** 의 **ThreatIntelligenceIndicator** 테이블에서(**Azure Sentinel** 그룹 아래에 있음) 가져온 위협 지표를 볼 수 있습니다. 이 테이블은 Analytics 및 Workbooks와 같은 다른 Azure Sentinel 기능에서 수행하는 쿼리의 기초가 됩니다.

결과는 아래에 표시된 샘플 위협 지표와 비슷합니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="샘플 쿼리 데이터":::

Azure Sentinel 주 메뉴에서 액세스할 수 있는 새로운 **위협 인텔리전스** 블레이드에서도 지표를 확인하고 관리할 수 있습니다. 심지어 Log Analytics 쿼리를 작성하지 않고도 가져온 위협 지표를 정렬, 필터링 및 검색할 수 있습니다. 또한 이 기능을 통해 Azure Sentinel 인터페이스 내에서 직접 위협 지표를 만들고, 가장 일반적인 두 가지 위협 인텔리전스 관리 작업인 지표 태그 지정 및 보안 조사와 관련된 신규 지표 작성을 수행할 수 있습니다.

위협 지표에 태그를 지정하면 지표를 함께 그룹화하여 손쉽게 찾을 수 있습니다. 일반적으로 특정 인시던트와 관련된 지표 또는 알려진 행위자나 잘 알려진 공격 캠페인의 위협을 나타내는 지표에 태그를 적용합니다. 위협 지표에 각각 태그를 지정할 수도 있고 여러 개를 선택하여 한 번에 모두 태그를 지정할 수도 있습니다. 인시던트 ID를 사용하여 여러 지표에 태그를 지정하는 예제는 다음과 같습니다. 태그 지정은 특정한 형식이 없기 때문에, 위협 지표 태그에 대한 표준 명명 규칙을 만드는 것이 좋습니다. 각 지표에 여러 태그를 적용할 수 있습니다.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="위협 지표에 태그 적용" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

위협 지표 확인 및 관리에 대한 자세한 내용은 [Azure Sentinel에서 위협 지표 작업](work-with-threat-indicators.md#view-your-threat-indicators-in-azure-sentinel)을 참조하세요.

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>위협 지표 기반 분석을 사용하여 위협 탐지

Azure Sentinel과 같은 SIEM 솔루션에서 위협 지표의 가장 중요한 사용 사례는 위협 탐지를 위한 분석 규칙을 제공하는 것입니다. 이러한 지표 기반 규칙은 데이터 원본의 원시 이벤트를 위협 지표와 비교하여 조직에서 보안 위협을 탐지합니다. Azure Sentinel **분석** 에서 일정에 따라 실행되고 보안 경고를 생성하는 분석 규칙을 만듭니다. 이러한 규칙은 규칙 실행 빈도, 보안 경고 및 인시던트를 생성하는 쿼리 결과의 종류, 경고에 대한 대응으로 트리거할 자동화를 결정하는 구성과 함께 쿼리를 통해 작동합니다.

언제든지 새 분석 규칙을 처음부터 만들 수 있지만, Azure Sentinel은 Microsoft 보안 엔지니어가 만든 기본 제공 규칙 템플릿 집합을 제공하며, 이를 그대로 사용하거나 필요에 맞게 수정할 수 있습니다. 위협 지표를 사용하는 규칙 템플릿은 모두 '**TI map**...'으로 시작하므로 쉽게 식별할 수 있습니다. 이러한 모든 규칙 템플릿은 사용하는 위협 지표 유형(도메인, 이메일, 파일 해시, IP 주소 또는 URL) 및 일치시킬 이벤트 유형을 제외하고 유사하게 작동합니다. 각 템플릿에는 규칙이 작동하는 데 필요한 데이터 원본이 나열되어 있으므로 Azure Sentinel에서 필요한 이벤트를 이미 가져왔는지 확인할 수 있습니다. 기존 규칙 템플릿을 편집하고 저장하거나 새 규칙을 만들 때 기본적으로 사용하도록 설정됩니다.

Azure Sentinel **분석** 섹션의 **활성 규칙** 탭에서 설정된 규칙을 찾을 수 있습니다. 여기에서 활성 규칙을 편집, 활성화, 비활성화, 복제 또는 삭제할 수 있습니다. 새 규칙은 활성화되는 즉시 실행되며, 그 다음에는 정의된 일정에 따라 실행됩니다.

규칙이 기본 설정의 일정에 따라 실행될 때마다 검색된 결과가 있으면 보안 경고가 생성됩니다. Azure Sentinel의 보안 경고는 **Azure Sentinel** 그룹 아래 **SecurityAlert** 테이블에 있는 Azure Sentinel의 **로그** 섹션에서 볼 수 있습니다.

Azure Sentinel에서 분석 규칙에서 생성된 경고는 Azure Sentinel 메뉴의 **위협 관리** 아래에 있는 **인시던트** 에서 확인할 수 있는 보안 인시던트도 생성합니다. 인시던트는 보안 운영 팀이 적절한 대응 조치를 결정하기 위해 선별 및 조사하는 것입니다. 이 [자습서: Azure Sentinel을 사용하여 인시던트 조사](./investigate-cases.md)에서 자세한 정보를 찾을 수 있습니다.

분석 규칙에 위협 지표를 사용하는 방법에 대한 자세한 내용은 [Azure Sentinel에서 위협 지표 작업](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics)을 참조하세요.

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>통합 문서는 위협 인텔리전스에 대한 정보를 제공합니다.

Workbooks는 Azure Sentinel의 모든 측면에 대한 정보를 제공하는 강력한 대화형 대시보드를 제공하며, 위협 인텔리전스도 예외가 아닙니다. 기본 제공 **위협 인텔리전스 통합 문서** 를 사용하여 위협 인텔리전스에 대한 주요 정보를 시각화할 수 있으며, 통합 문서를 비즈니스 요구 사항에 따라 쉽게 사용자 지정할 수 있습니다. 뿐만 아니라 다양한 데이터 원본을 결합하여 새 대시보드를 만들 수도 있으므로 고유한 방식으로 데이터를 시각화할 수 있습니다. Azure Sentinel 통합 문서는 Azure Monitor 통합 문서를 기반으로 하기 때문에 이미 광범위한 설명서를 사용할 수 있으며 더 많은 템플릿이 있습니다. [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/visualize/workbooks-overview.md) 방법에 대한 이 문서에서 시작하는 것이 좋습니다. 

또한 GitHub에는 추가 템플릿을 다운로드하고 자체 템플릿을 제공할 수 있는 활발한 [Azure Monitor 통합 문서](https://github.com/microsoft/Application-Insights-Workbooks) 커뮤니티가 있습니다.

위협 인텔리전스 통합 문서를 사용하고 사용자 지정하는 방법에 대한 자세한 내용은 [Azure Sentinel에서 위협 지표 작업](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 위협 인텔리전스 블레이드를 포함하여 Azure Sentinel의 위협 인텔리전스 기능에 대해 알아보았습니다. Azure Sentinel의 위협 인텔리전스 기능을 사용하는 방법에 대한 실제 지침은 다음 문서를 참조하세요.

- Azure Sentinel을 [STIX/TAXII 위협 인텔리전스 피드에 연결](./connect-threat-intelligence-taxii.md)
- [위협 인텔리전스 플랫폼을 Azure Sentinel에 연결](./connect-threat-intelligence-tip.md)
- Azure Sentinel과 즉시 통합할 수 있는 [TIP 플랫폼, TAXII 피드 및 보강 기능](threat-intelligence-integration.md)
- Azure Sentinel 환경 전체에서 [위협 지표 작업](work-with-threat-indicators.md)
- Azure Sentinel에서 [기본 제공](./detect-threats-built-in.md) 또는 [사용자 지정](./detect-threats-custom.md) 분석 규칙을 사용하여 위협 탐지
- Azure Sentinel에서 [인시던트 조사](./investigate-cases.md)
