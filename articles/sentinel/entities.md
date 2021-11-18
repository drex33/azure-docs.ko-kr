---
title: 엔터티를 사용하여 Microsoft Sentinel | 데이터 분류 및 분석 Microsoft Docs
description: 엔터티 분류(사용자, 호스트 이름, IP 주소)를 Microsoft Sentinel의 데이터 항목에 할당하고, 이를 사용하여 여러 원본의 데이터를 비교, 분석 및 상호 연관시킬 수 있습니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f6a0787c13f21b4a41e6921843876c04638c3f99
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730616"
---
# <a name="classify-and-analyze-data-using-entities-in-microsoft-sentinel"></a>Microsoft Sentinel에서 엔터티를 사용하여 데이터 분류 및 분석

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="what-are-entities"></a>엔터티란 무엇인가요?

경고가 Microsoft Sentinel에 전송되거나 생성되면 Sentinel에서 인식하고 범주로 **엔터티로** 분류할 수 있는 데이터 항목이 포함됩니다. Microsoft Sentinel은 특정 데이터 항목이 나타내는 엔터티 종류를 이해하면 이에 대해 질문할 올바른 질문을 알고 있으며, 해당 항목에 대한 인사이트를 전체 데이터 원본에서 비교하고 분석, 조사, 수정, 헌팅 등 전체 Sentinel 환경 전체에서 쉽게 추적하고 참조할 수 있습니다. 엔터티의 일반적인 예에는 사용자, 호스트, 파일, 프로세스, IP 주소 및 URL이 있습니다.

### <a name="entity-identifiers"></a>엔터티 식별자

Microsoft Sentinel은 다양한 엔터티 형식을 지원합니다. 각 형식에는 고유한 특성이 있으며 특정 엔터티를 식별하는 데 사용할 수 있는 특성을 포함합니다. 이러한 속성은 항목의 필드로 표시되며 **식별자** 라고 합니다. 지원되는 엔터티와 해당 식별자의 전체 목록은 아래를 참조하세요.

#### <a name="strong-and-weak-identifiers"></a>강력한 식별자와 약한 식별자

위에서 언급한 것처럼 각 엔터티 형식에 대한 필드 또는 필드 집합이 있으며 이를 통해 식별할 수 있습니다. 이러한 필드 또는 필드 집합은 모호성 없이 항목을 고유하게 식별할 수 있는 경우 **강력한 식별자** 라고 하고 특정 상황에서는 항목을 식별할 수 있지만 모든 경우에 엔터티를 고유하게 식별할 수 없는 경우 **약한 식별자** 라고 합니다. 그러나 대부분의 경우 약한 식별자를 결합하여 강력한 식별자를 생성할 수 있습니다.

예를 들어 사용자 계정은 Azure AD 계정의 숫자 식별자(**GUID** 필드) 또는 **사용자 계정 이름(UPN)** 값과 같은 **강력한 식별자** 아니면 **이름** 및 **NTDomain** 과 같은 **약한 식별자** 조합 사용 필드를 사용하여 **계정** 엔터티로 식별될 수 있습니다. 서로 다른 데이터 원본으로 동일한 사용자를 식별할 수 있습니다. Microsoft Sentinel은 식별자에 따라 동일한 엔터티로 인식할 수 있는 두 엔터티를 발견할 때마다 두 엔터티를 단일 엔터티로 병합하여 적절하고 일관되게 처리할 수 있습니다.

그러나 리소스 제공자 중 하나가 엔터티가 충분히 식별되지 않음을 나타내는 경고를 만드는 경우(예: 도메인 이름 컨텍스트 없이 사용자 이름과 같은 단일 **약한 식별자** 를 사용하는 경우) 사용자 엔터티는 동일한 사용자 계정의 다른 인스턴스와 병합될 수 없습니다. 이러한 다른 인스턴스는 별도의 엔터티로 식별되며 해당 두 엔터티는 통합되지 않고 별개로 유지됩니다.

해당 상황이 발생하는 위험을 최소화하기 위해 모든 경고 공급자가 생성하는 경고에서 엔터티를 제대로 식별하는지 확인해야 합니다. 또한 사용자 계정 엔터티를 Azure Active Directory와 동기화하면 통합 디렉터리가 생성되어 사용자 계정 엔터티를 병합할 수 있습니다.

#### <a name="supported-entities"></a>지원되는 엔터티

현재 Microsoft Sentinel에서 식별되는 엔터티 형식은 다음과 같습니다.

- 사용자 계정
- 호스트
- IP 주소
- 맬웨어
- 파일
- Process
- 클라우드 애플리케이션
- 도메인 이름
- Azure 리소스
- 파일 해시
- 레지스트리 키
- 레지스트리 값
- 보안 그룹
- URL
- IoT 디바이스
- 사서함
- 메일 클러스터
- 메일 메시지
- 메일 제출

이러한 엔터티의 식별자 및 기타 관련 정보는 [엔터티 참조](entities-reference.md)에서 볼 수 있습니다.

## <a name="entity-mapping"></a>엔터티 매핑

Microsoft Sentinel은 경고의 데이터 조각을 엔터티를 식별하는 것으로 어떻게 인식하나요?

Microsoft Sentinel에서 데이터 처리가 수행되는 방식을 살펴보겠습니다. 데이터는 서비스 간, 에이전트 기반 또는 syslog 서비스 및 로그 전달자를 사용하여 [커넥터](connect-data-sources.md)를 통해 다양한 소스에서 수집됩니다. 데이터는 Log Analytics 작업 영역의 테이블에 저장됩니다. 이러한 테이블은 사용자가 정의하고 사용하도록 설정한 분석 규칙에 따라 정기적으로 예약된 간격으로 쿼리됩니다. 이러한 분석 규칙에서 수행되는 많은 작업 중 하나는 테이블의 데이터 필드를 Microsoft Sentinel 인식 엔터티에 매핑하는 것입니다. 분석 규칙에서 정의하는 매핑에 따라 Microsoft Sentinel은 쿼리에서 반환된 결과의 필드를 사용하여 각 엔터티 형식에 대해 지정한 식별자를 통해 인식하고 해당 식별자에 의해 식별된 엔터티 형식을 적용합니다.

이 모든 것의 요점은 무엇일까요?

Microsoft Sentinel이 다양한 유형의 데이터 원본에서 경고의 엔터티를 식별할 수 있는 경우, 특히 각 데이터 원본 또는 세 번째 스키마에 공통된 강력한 식별자를 사용하여 엔터티를 식별할 수 있는 경우 이러한 모든 경고와 데이터 원본 간에 쉽게 상관 관계를 지정할 수 있습니다. 이러한 상관 관계는 엔터티에 대한 풍부한 정보와 인사이트를 제공하여 보안 작업을 위한 견고한 토대를 제공하는 데 도움이 됩니다.

[데이터 필드를 엔터티에 매핑](map-data-fields-to-entities.md)하는 방법에 대해 알아봅니다.

[엔터티를 강력하게 식별하는 식별자](entities-reference.md)에 대해 알아봅니다.

## <a name="entity-pages"></a>엔터티 페이지

검색, 경고 또는 조사에서 엔터티(현재 사용자 및 호스트로 제한됨)가 발생하면 엔터티를 선택하고 엔터티 **페이지로** 이동하여 해당 엔터티에 대한 유용한 정보를 모두 볼 수 있습니다. 해당 페이지에서 찾을 수 있는 정보 유형에는 엔터티에 대한 기본 팩트, 해당 엔터티와 관련된 주목할 만한 이벤트의 타임라인 및 엔터티 동작에 대한 인사이트가 포함됩니다.

엔터티 페이지는 다음 세 부분으로 구성됩니다.

- 왼쪽 패널에는 Azure Active Directory, Azure Monitor, Microsoft Defender for Cloud 및 Microsoft Defender for Cloud와 같은 데이터 원본에서 수집된 엔터티의 식별 정보가 포함되어 있습니다.

- 가운데 패널에는 경고 북마크 및 활동과 같이 엔터티와 관련된 주목할 만한 이벤트의 그래픽 및 텍스트 타임라인이 표시됩니다. 활동은 Log Analytics에서 주목할 만한 이벤트의 집계입니다. 활동을 검색하는 쿼리는 Microsoft 보안 연구 팀에서 개발합니다.

- 오른쪽 패널은 엔터티에 대한 동작 인사이트를 제공합니다. 해당 인사이트는 변칙 및 보안 위협을 빠르게 식별하는 데 도움이 됩니다. 해당 인사이트는 Microsoft 보안 연구 팀에서 개발되었으며 변칙 탐지 모델을 기반으로 합니다.

### <a name="the-timeline"></a>타임라인

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="엔터티 페이지 타임라인":::

타임라인은 Microsoft Sentinel의 동작 분석에 대한 엔터티 페이지 기여의 주요 부분입니다. 엔터티 관련 이벤트에 대한 스토리를 제공하여 특정 시간 프레임 내에서 엔터티의 작업을 이해하도록 도와줍니다.

몇 가지 사전 설정된 옵션(예: *최근 24시간*) 중에서 **시간 범위** 를 선택하거나 사용자 지정된 시간 프레임으로 설정할 수 있습니다. 또한 타임라인의 정보를 특정 형식의 이벤트 또는 경고로 제한하는 필터를 설정할 수 있습니다.

타임라인에는 다음과 같은 형식의 항목이 포함되어 있습니다.

- 경고 - 엔터티가 **매핑된 엔터티로** 정의된 모든 경고입니다. 조직에서 [분석 규칙을 사용하여 사용자 지정 경고](./detect-threats-custom.md)를 생성된 경우 규칙의 엔터티 매핑이 제대로 수행되었는지 확인해야 합니다.

- 북마크 - 페이지에 표시된 특정 엔터티를 포함하는 책갈피입니다.

- 활동 - 엔터티와 관련된 주목할 만한 이벤트의 집계입니다.

### <a name="entity-insights"></a>엔터티 인사이트

엔터티 인사이트는 분석가가 더 효율적이고 효과적으로 조사하는 데 도움이 되도록 Microsoft 보안 연구원에 의해 정의된 쿼리입니다. 해당 인사이트는 엔터티 페이지의 일부로 제공되며 호스트와 사용자에 대한 중요 보안 정보를 테이블 형식 데이터 및 차트 형식으로 제공합니다. 여기에 인사이트가 있다는 것은 Log Analytics로 우회할 필요가 없다는 것을 의미합니다. 해당 인사이트에는 로그인, 그룹 추가, 비정상 이벤트 등에 대한 데이터가 포함되며 비정상적인 동작을 탐지하는 고급 ML 알고리즘이 포함되어 있습니다.

인사이트는 다음과 같은 데이터 원본을 기반으로 합니다.

- Syslog(Linux)
- SecurityEvent(Windows)
- AuditLogs(Azure AD)
- SigninLogs(Azure AD)
- OfficeActivity(Office 365)
- BehaviorAnalytics(Microsoft Sentinel UEBA)
- 하트비트(Azure Monitor 에이전트)
- CommonSecurityLog(Microsoft Sentinel)

### <a name="how-to-use-entity-pages"></a>엔터티 페이지를 사용하는 방법

엔터티 페이지는 여러 사용 시나리오의 일부로 설계되었으며 인시던트 관리, 조사 그래프, 책갈피 또는 Microsoft Sentinel 주 메뉴의 **엔터티 동작 분석 아래 엔터티** 검색 페이지에서 직접 액세스할 수 있습니다.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="엔터티 페이지 사용 사례":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel에서 엔터티를 사용하는 방법을 배웠습니다. 구현에 대한 유용한 지침을 알아보고 얻은 인사이트를 사용하려면 다음 문서를 참조하세요.

- Microsoft 센티널에서 [엔터티 동작 분석을 사용 하도록 설정](./enable-entity-behavior-analytics.md) 합니다.
- [보안 위협에 대한 헌팅](./hunting.md)
