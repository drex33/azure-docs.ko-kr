---
title: Azure Sentinel에서 UEBA(사용자 및 엔터티 동작 분석)를 사용하여 고급 위협 식별 | Microsoft Docs
description: 엔터티(사용자, 호스트 이름, IP 주소)에 대한 동작 기준을 만들고 이를 사용하여 비정상적인 동작을 감지하고 제로 데이 APT(고급 영구 위협)를 식별합니다.
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
ms.date: 05/11/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 66775aa492f47d1107a24b6aac0e5b5368995e9e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037153"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Azure Sentinel에서 UEBA(사용자 및 엔터티 동작 분석)를 사용하여 고급 위협 식별

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - UEBA 및 엔터티 페이지 기능은 이제 **_모든_** Azure Sentinel 지역에서 **일반 공급** 됩니다. 
>
> - **IP 주소 엔터티** 는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>UEBA(사용자 및 엔터티 동작 분석)란?

조직 내부의 위협 및 위협의 잠재적 영향(손상된 엔터티 또는 악의적인 내부자)을 식별하는 것은 항상 시간과 인력이 많이 소모되는 프로세스입니다. 경고를 검토하고 내용을 이해하며 적극적인 헌팅하는 과정에서 낮은 성과와 단순히 검색을 회피하는 정교한 위협으로 인해 막대한 시간과 노력이 소비됩니다. 특히 탐지가 어려운 제로 데이, 대상이 지정된 위협, 지능형 지속 위협은 조직에 매우 큰 위험을 초래할 수 있기 때문에 반드시 탐지해야 합니다.

Azure Sentinel의 UEBA 기능은 분석가들의 워크로드에서 오는 힘든 작업 및 노력을 기울이는 것에 대한 불확실성을 제거하고 높은 충실도의 실행 가능한 인텔리전스를 제공하여 조사 및 수정 작업에 집중할 수 있도록 합니다.

Azure Sentinel은 연결된 모든 데이터 원본에서 로그 및 경고를 수집하고 해당 로그 및 경고를 분석하여 조직의 엔터티(예: 사용자, 호스트, IP 주소 및 애플리케이션)의 기준 동작 프로필을 시간 및 피어 그룹을 기준선으로 하여 빌드합니다. Azure Sentinel은 다양한 기술 및 기계 학습 기능을 사용하여 비정상적인 활동을 식별하여 자산의 손상 여부를 확인하는 데 활용됩니다. 그뿐만 아니라 특정 자산의 상대적인 민감도를 파악하고 자산의 피어 그룹을 식별하며 손상된 특정 자산의 잠재적 영향(‘blast radius’)을 평가할 수도 있습니다. 해당 정보를 사용하여 조사 및 침해 인시던트 처리의 우선 순위를 효과적으로 지정할 수 있습니다. 

### <a name="ueba-analytics-architecture"></a>UEBA 분석 아키텍처

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="엔터티 동작 분석 아키텍처":::

### <a name="security-driven-analytics"></a>보안 기반 분석

UEBA 솔루션에 대한 Gartner의 패러다임에서 제공되는 Azure Sentinel은 세 가지 참조 프레임을 기반으로 하는 “외부-내부” 접근 방식을 제공합니다.

- **사용 사례:** 다양한 엔터티를 킬체인의 희생자, 공격자 또는 피벗 포인트로 설정하는 MITRE ATT&CK의 전술적 프레임워크에 맞춘 보안 연구를 기반으로 하여 관련 공격 벡터 및 시나리오를 우선 순위로 두면서 Azure Sentinel은 특히 각 데이터 원본이 제공할 수 있는 가장 가치 있는 로그에 초점을 맞춥니다.

- **데이터 원본:** Azure Sentinel은 Azure 데이터 소스를 가장 먼저 지원하는 동시에 위협 시나리오에 맞는 데이터를 제공하기 위해 타사 데이터 소스를 적절하게 지원합니다.

- **분석:** 다양한 ML(기계 학습) 알고리즘을 사용하여, Azure Sentinel은 비정상적인 활동을 식별하고 다음과 같은 몇 가지 예를 들어 상세한 형태로 명확하고 간결하게 증거를 제시합니다.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="동작 분석 외부 접근 방식":::

Azure Sentinel은 보안 분석가가 컨텍스트의 비정상적인 활동을 명확하게 이해하고 사용자의 기준 프로필을 비교하는 데 활용되는 아티팩트를 제공합니다. 사용자(또는 호스트나 주소)가 수행하는 작업은 상황별로 평가됩니다. “true”결과는 변칙이 탐지되었음을 의미합니다.
- 지리적 위치, 디바이스, 환경에 걸쳐 탐지
- 시간 및 빈도 범위에 걸쳐 탐지(사용자 본인의 기록과 비교)
- 피어 동작과 비교를 통해 탐지
- 조직의 동작과 비교를 통해 탐지

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="엔터티 컨텍스트":::


### <a name="scoring"></a>점수 매기기

각 활동은 “조사 우선 순위 점수”로 점수가 매겨지며 해당 점수는 특정 사용자가 사용자와 피어의 동작 학습에 따라 특정 활동을 수행할 확률을 결정합니다. 가장 비정상적인 활동으로 식별된 활동은 가장 높은 점수를 받습니다(0~10까지의 점수).

작동 방식에 대한 예제는 [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136)에서 동작 분석을 사용하는 방법을 참조하세요.

## <a name="entity-pages"></a>엔터티 페이지

[Azure Sentinel의 엔터티](entities-in-azure-sentinel.md)에 대해 자세히 알아보고 [지원되는 엔터티 및 식별자](entities-reference.md)의 전체 목록을 확인하세요.

엔터티 검색, 경고 또는 조사에서 사용자 또는 호스트 엔터티(IP 주소 엔터티가 미리 보기로 제공됨)를 확인하면 엔터티를 선택하여 **엔터티 페이지**(해당 엔터티에 대한 유용한 정보가 포함된 데이터시트)로 이동할 수 있습니다. 해당 페이지에서 찾을 수 있는 정보 유형에는 엔터티에 대한 기본 팩트, 해당 엔터티와 관련된 주목할 만한 이벤트의 타임라인 및 엔터티 동작에 대한 인사이트가 포함됩니다.
 
엔터티 페이지는 다음 세 부분으로 구성됩니다.
- 왼쪽 패널에는 Azure Active Directory, Azure Monitor, Azure Defender, CEF/Syslog 및 Microsoft 365 Defender와 같은 데이터 원본에서 수집된 엔터티의 식별 정보가 포함되어 있습니다.

- 가운데 패널에는 경고 북마크 및 활동과 같이 엔터티와 관련된 주목할 만한 이벤트의 그래픽 및 텍스트 타임라인이 표시됩니다. 활동은 Log Analytics에서 주목할 만한 이벤트의 집계입니다. 이러한 활동을 검색하는 쿼리는 Microsoft 보안 연구팀에서 개발했으며 이제 [사용자 지정 쿼리를 추가하여 선택한 활동을 검색](customize-entity-activities.md)할 수 있습니다. 

- 오른쪽 패널은 엔터티에 대한 동작 인사이트를 제공합니다. 해당 인사이트는 변칙 및 보안 위협을 빠르게 식별하는 데 도움이 됩니다. 해당 인사이트는 Microsoft 보안 연구 팀에서 개발되었으며 변칙 탐지 모델을 기반으로 합니다.

> [!NOTE]
> **IP 주소 엔터티 페이지**(현재 미리 보기로 제공됨)에는 **Microsoft 위협 인텔리전스 서비스** 에서 제공하는 **지리적 위치 데이터** 가 포함되어 있습니다. 이 서비스는 Microsoft 솔루션과 타사 공급업체 및 파트너의 지리적 위치 데이터를 결합합니다. 그런 다음 데이터를 보안 인시던트 컨텍스트에서 분석 및 조사할 수 있습니다. 자세한 내용은 [REST API를 통해 지리적 위치 데이터를 사용하여 Azure Sentinel에서 엔터티 보강(공개 미리 보기)](geolocation-data-api.md)을 참조하세요.

### <a name="the-timeline"></a>타임라인

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="엔터티 페이지 타임라인":::

타임라인은 Azure Sentinel의 동작 분석에 대한 엔터티 페이지 기여의 주요 부분입니다. 엔터티 관련 이벤트에 대한 스토리를 제공하여 특정 시간 프레임 내에서 엔터티의 작업을 이해하도록 도와줍니다.

몇 가지 사전 설정된 옵션(예: *최근 24시간*) 중에서 **시간 범위** 를 선택하거나 사용자 지정된 시간 프레임으로 설정할 수 있습니다. 또한 타임라인의 정보를 특정 형식의 이벤트 또는 경고로 제한하는 필터를 설정할 수 있습니다.

타임라인에는 다음과 같은 형식의 항목이 포함되어 있습니다.

- 경고 - 엔터티가 **매핑된 엔터티로** 정의된 모든 경고입니다. 조직에서 [분석 규칙을 사용하여 사용자 지정 경고](./detect-threats-custom.md)를 생성된 경우 규칙의 엔터티 매핑이 제대로 수행되었는지 확인해야 합니다.

- 북마크 - 페이지에 표시된 특정 엔터티를 포함하는 책갈피입니다.

- 활동 - 엔터티와 관련된 주목할 만한 이벤트의 집계입니다. 광범위한 활동이 자동으로 수집되며 이제 자체적으로 선택한 [활동을 추가하여 이 섹션을 사용자 지정](customize-entity-activities.md)할 수 있습니다.

### <a name="entity-insights"></a>엔터티 인사이트
 
엔터티 인사이트는 분석가가 더 효율적이고 효과적으로 조사하는 데 도움이 되도록 Microsoft 보안 연구원에 의해 정의된 쿼리입니다. 해당 인사이트는 엔터티 페이지의 일부로 제공되며 호스트와 사용자에 대한 중요 보안 정보를 테이블 형식 데이터 및 차트 형식으로 제공합니다. 여기에 인사이트가 있다는 것은 Log Analytics로 우회할 필요가 없다는 것을 의미합니다. 해당 인사이트에는 로그인, 그룹 추가, 비정상 이벤트 등에 대한 데이터가 포함되며 비정상적인 동작을 탐지하는 고급 ML 알고리즘이 포함되어 있습니다. 

인사이트는 다음과 같은 데이터 원본을 기반으로 합니다.
- Syslog(Linux)
- SecurityEvent(Windows)
- AuditLogs(Azure AD)
- SigninLogs(Azure AD)
- OfficeActivity(Office 365)
- BehaviorAnalytics(Azure Sentinel UEBA)
- 하트비트(Azure Monitor 에이전트)
- CommonSecurityLog(Azure Sentinel)
- ThreatIntelligenceIndicators(Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>엔터티 페이지를 사용하는 방법

엔터티 페이지는 다양한 활용 시나리오의 일부로 설계되었으며 인시던트 관리, 조사 그래프, 북마크에서 액세스하거나, Azure Sentinel 메인 메뉴의 **엔터티 동작 분석** 에 있는 엔터티 검색 페이지에서 직접 액세스할 수 있습니다.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="엔터티 페이지 사용 사례":::

엔터티 페이지 정보는 [Azure Sentinel UEBA 보강 참조](ueba-enrichments.md)에 자세히 설명된 **BehaviorAnalytics** 테이블에 저장됩니다.

## <a name="querying-behavior-analytics-data"></a>동작 분석 데이터 쿼리

[KQL](/azure/data-explorer/kusto/query/)을 사용하여 동작 분석 테이블을 쿼리할 수 있습니다.

예를 들어 Azure 리소스에 로그인하지 못한 사용자의 모든 사례를 찾으려고 하고(사용자가 지정된 국가에서 연결을 처음으로 시도함), 해당 국가에서의 연결이 일반적으로 사용자의 피어에 대해서도 발생하지 않는 경우 다음 쿼리를 사용할 수 있습니다.

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>사용자 피어 메타데이터 - 테이블 및 Notebook

사용자 피어의 메타데이터는 위협 검색, 인시던트 조사 및 잠재적 위협에 대한 헌팅에서 중요한 컨텍스트를 제공합니다. 보안 분석가는 사용자의 피어에 대한 정상적인 활동을 관찰하여 사용자의 활동이 사용자의 피어와 비교하여 비정상적인지 확인할 수 있습니다.

Azure Sentinel은 사용자의 Azure AD 보안 그룹 멤버 자격, 메일링 목록 등을 기반으로 사용자의 피어를 계산하고 순위를 매겨 **UserPeerAnalytics** 테이블에 1-20위의 피어를 저장합니다. 아래 스크린샷은 UserPeerAnalytics 테이블의 스키마를 표시하고, 사용자 Kendall Collins의 상위 8개 순위 피어를 표시합니다. Azure Sentinel은 *TF-IDF(용어 빈도-역문서 빈도)* 알고리즘을 사용하여 순위 계산을 위해 가중치를 표준화합니다. 그룹이 작을수록 가중치가 높아집니다. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="사용자 피어 메타데이터 테이블의 스크린샷":::

Azure Sentinel GitHub 리포지토리에 제공된 [Jupyter Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata)을 사용하여 사용자 피어 메타데이터를 시각화할 수 있습니다. Notebook을 사용하는 방법에 대한 자세한 내용은 [단계별 분석 - 사용자 보안 메타데이터](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) Notebook을 참조하세요.

### <a name="permission-analytics---table-and-notebook"></a>권한 분석 - 테이블 및 Notebook

권한 분석은 공격자의 조직 자산의 손상으로 인한 잠재적 영향을 확인하는 데 도움이 됩니다. 이러한 영향을 자산의 "폭발 반지름"이라고도 합니다. 보안 분석가는 이 정보를 사용하여 조사 및 인시던트 처리의 우선 순위를 지정할 수 있습니다.

Azure Sentinel은 사용자가 직접 또는 그룹 또는 서비스 주체를 통해 액세스할 수 있는 Azure 구독을 평가하여 지정된 사용자가 Azure 리소스에 대해 보유한 직접 및 전이적 액세스 권한을 결정합니다. 이 정보는 물론 사용자의 Azure AD 보안 그룹 멤버 자격에 대한 전체 목록이 **UserAccessAnalytics** 테이블에 저장됩니다. 아래 스크린샷은 사용자 Alex Johnson에 대한 UserAccessAnalytics 테이블의 예제 행을 보여 줍니다. **원본 엔터티** 는 사용자 또는 서비스 주체 계정이고, **대상 엔터티** 는 원본 엔터티가 액세스할 수 있는 리소스입니다. **액세스 수준** 및 **액세스 형식** 의 값은 대상 엔터티의 액세스 제어 모델에 따라 달라집니다. Alex에 Azure 구독 *Contoso Hotels Tenant* 에 대한 기여자 액세스 권한이 있는 것을 볼 수 있습니다. 구독의 액세스 제어 모델은 Azure RBAC입니다.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="사용자 액세스 분석 테이블의 스크린샷":::

Azure Sentinel GitHub 리포지토리에서 [Jupyter Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata)(위에서 언급한 것과 동일한 Notebook)을 사용하여 권한 분석 데이터를 시각화할 수 있습니다. Notebook을 사용하는 방법에 대한 자세한 내용은 [단계별 분석 - 사용자 보안 메타데이터](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) Notebook을 참조하세요.

### <a name="hunting-queries-and-exploration-queries"></a>헌팅 쿼리 및 탐색 쿼리

Azure Sentinel은 **BehaviorAnalytics** 테이블을 기반으로 하는 헌팅 쿼리, 탐색 쿼리 및 **사용자 및 엔터티 동작 분석** 통합 문서 세트를 기본 제공합니다. 이러한 도구는 비정상적인 동작을 나타내는 특정 사용 사례에 초점을 맞춘 보강된 데이터를 제공합니다.

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)
- [데이터 시각화 및 모니터링](monitor-your-data.md)

레거시 방어 도구가 더 이상 사용되지 않게 됨에 따라 조직에는 환경에서 발생할 수 있는 위험 및 상태에 대한 포괄적인 계획을 수립하는 것이 관리할 수 없을 정도로 방대하고 다공성인 디지털 자산이 있을 수 있습니다. 분석 및 규칙과 같은 사후 대응 노력에 크게 의존함으로써 악의적인 행위자는 이러한 노력을 회피하는 방법을 배울 수 있습니다. 실제로 무슨 일이 일어나고 있는지 파악하기 위해 위험 점수 매기기 방법론과 알고리즘을 제공함으로써 UEBA가 작동하게 하는 곳입니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel의 엔터티 동작 분석 기능에 대해 알아보았습니다. 구현에 대한 유용한 지침을 알아보고 얻은 인사이트를 사용하려면 다음 문서를 참조하세요.

- Azure Sentinel에서 [엔터티 동작 분석 사용](./enable-entity-behavior-analytics.md)
- [UEBA 데이터로 인시던트 조사](investigate-with-ueba.md).
- [보안 위협에 대한 헌팅](./hunting.md)

자세한 내용은 [Azure Sentinel UEBA 보강 참조](ueba-enrichments.md)도 참조하세요.
