---
title: 작업 영역 및 테 넌 트 간에 Microsoft 센티널 확장 | Microsoft Docs
description: Microsoft 센티널을 사용 하 여 작업 영역 및 테 넌 트 전체에서 데이터를 쿼리하고 분석 하는 방법입니다.
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
ms.openlocfilehash: bd0166e501cac685d9d0f0e661511e8c8e78df35
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715191"
---
# <a name="extend-microsoft-sentinel-across-workspaces-and-tenants"></a>작업 영역 및 테넌트에 걸쳐 Microsoft Sentinel 확장

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="the-need-to-use-multiple-microsoft-sentinel-workspaces"></a>여러 Microsoft 센티널 작업 영역을 사용 해야 하는 경우

Microsoft 센티널은 Log Analytics 작업 영역을 기반으로 빌드됩니다. Microsoft 센티널을 등록 하는 첫 번째 단계는 해당 목적으로 사용 하려는 Log Analytics 작업 영역을 선택 하는 것입니다.

단일 작업 영역을 사용 하는 경우 Microsoft 센티널 환경을 최대한 활용할 수 있습니다. 하지만 여러 작업 영역을 사용해야 하는 경우도 있습니다. 다음 표에서는 해당 상황 중 일부를 나열하고 가능한 경우 단일 작업 영역을 사용하여 요구 사항을 충족하는 방법을 제안합니다.

| 요구 사항 | Description | 작업 영역 수를 줄이는 방법 |
|-------------|-------------|--------------------------------|
| 관할 및 규정 준수 | 작업 영역이 특정 지역에 귀속되어 있음. 규정 준수를 위해 데이터를 다른 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)에 보관해야 한다면, 여러 작업 영역에 분할해서 보관해야 합니다. |  |
| 데이터 소유권 | 자회사 또는 계열사와 같은 데이터 소유권의 경계는 별도의 작업 영역을 사용하면 보다 쉽게 구분할 수 있습니다. |  |
| 다수의 Azure 테넌트 | microsoft 센티널은 자체 Azure Active Directory (azure AD) 테 넌 트 경계 내 에서만 microsoft 및 azure SaaS 리소스의 데이터 수집을 지원 합니다. 따라서 각 Azure AD 테넌트에는 별도의 작업 영역이 필요합니다. |  |
| 세부적인 데이터 액세스 제어 | 조직에서 조직의 내부 또는 외부에 있는 다른 그룹을 허용 하 여 Microsoft 센티널에서 수집 된 일부 데이터에 액세스 해야 할 수 있습니다. 예를 들면 다음과 같습니다.<br><ul><li>리소스 소유자의 리소스에 관련된 데이터에 대한 액세스</li><li>지역 또는 자회사 SOC 조직의 일부와 관련된 데이터에 대한 액세스</li></ul> | [리소스 Azure RBAC](resource-context-rbac.md) 또는 [테이블 수준 Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) 사용 |
| 세부적인 데이터 보존 설정 | 지금까지는 여러 작업 영역을 사용하는 것이 여러 데이터 형식에 서로 다른 보존 기간을 설정하는 유일한 방법이었습니다. 하지만 테이블 수준 보존 설정의 도입으로 인해 해당 방법은 더 이상 필요하지 않게 되었습니다. | [테이블 수준 보존 설정](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) 사용 또는 [데이터 삭제](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) 자동화 |
| 분할 청구 | 작업 영역을 별도의 구독에 배치하면 여러 당사자에게 비용을 청구할 수 있습니다. | 사용량 보고 및 교차 요금 |
| 레거시 아키텍처 | 여러 작업 영역을 사용하는 이유가 더 이상 적용되지 않는 제한 사항이나 모범 사례를 고려한 과거 디자인 때문일 수도 있습니다. Microsoft 센티널에 맞게 수정할 수 있는 임의 디자인 선택도 될 수 있습니다.<br><br>다음은 이러한 템플릿의 예입니다.<br><ul><li>클라우드 용 Microsoft Defender를 배포할 때 구독 당 기본 작업 영역 사용</li><li>비교적 새로운 솔루션으로서 세부적 액세스 제어 또는 보존 설정이 필요</li></ul> | 작업 영역 재설계 |

### <a name="managed-security-service-provider-mssp"></a>MSSP(관리되는 보안 서비스 공급자)

여러 작업 영역을 요구 하는 특정 사용 사례는 MSSP Microsoft 센티널 서비스입니다. 해당 서비스의 경우 위의 요구 사항이 모두 적용되지는 않지만 많은 항목이 적용되므로 테넌트 간에 여러 작업 영역을 만드는 것이 가장 좋습니다. MSSP는 [Azure Lighthouse](../lighthouse/overview.md) 를 사용 하 여 테 넌 트 간에 Microsoft 센티널 상호 작업 영역 기능을 확장할 수 있습니다.

## <a name="microsoft-sentinel-multiple-workspace-architecture"></a>Microsoft 센티널 다중 작업 영역 아키텍처

위의 요구 사항에 암시 된 대로 Azure Active Directory (Azure AD) 테 넌 트 간에 여러 Microsoft 센티널 작업 영역을 중앙에서 모니터링 하 고 관리 해야 하는 경우가 있습니다.

- MSSP Microsoft 센티널 서비스입니다.

- 다수의 자회사에 서비스를 제공하는 글로벌 SOC로서 각기 자체적인 로컬 SOC를 보유하고 있습니다.

- 조직 내의 여러 Azure AD 테넌트를 모니터링하는 SOC입니다.

이러한 요구 사항을 해결 하기 위해 Microsoft 센티널은 중앙 모니터링, 구성 및 관리를 가능 하 게 하는 여러 작업 영역 기능을 제공 하 여 아래 다이어그램에 표시 된 대로 SOC에 포함 된 모든 항목에 대해 단일 창을 제공 합니다.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="작업 영역 간 아키텍처":::

이 다중 작업 영역 모델은 모든 데이터를 단일 작업 영역으로 복사하는 완전 중앙 집중식 모델에 비해 훨씬 많은 이점을 제공합니다. 대표적인 이점은 다음과 같습니다.

- 글로벌 및 로컬 SOC 또는 MSSP 고객에게 유연한 역할 할당을 제공합니다.

- 데이터 소유권, 데이터 프라이버시 및 규정 준수 관련 문제가 줄어듭니다.

- 네트워크 대기 시간 및 요금이 최소화됩니다.

- 신규 자회사 또는 고객의 온보딩 및 오프보딩이 쉽습니다.

다음 섹션에서는 해당 모델을 운영하는 방법과 특히 다음 작업을 수행하는 방법을 설명합니다.

- 잠재적으로 테넌트 간에 여러 작업 영역을 중앙에서 모니터링하여 단일 창으로 SOC를 제공합니다.

- 자동화 기능을 사용하여 잠재적으로 테넌트 간에 여러 작업 영역을 중앙에서 구성하고 관리합니다.

## <a name="cross-workspace-monitoring"></a>작업 영역 간 모니터링

### <a name="manage-incidents-on-multiple-workspaces"></a>여러 작업 영역에서 인시던트 관리

Microsoft 센티널은 여러 작업 영역에서 중앙 인시던트 모니터링과 관리를 용이 하 게 하는 [여러 작업 영역 인시던트 보기](./multiple-workspace-view.md) 를 지원 합니다. 중앙 집중식 인시던트 보기 기능을 사용하면 인시던트를 직접 관리하거나 원래 작업 영역의 컨텍스트에서 인시던트 세부 정보를 투명하게 드릴다운할 수 있습니다.

### <a name="cross-workspace-querying"></a>작업 영역 간 쿼리

Microsoft 센티널은 단일 쿼리에서 [여러 작업 영역](../azure-monitor/logs/cross-workspace-query.md)쿼리를 지원 하므로 단일 쿼리에서 여러 작업 영역의 데이터를 검색 하 고 상관 관계를 지정할 수 있습니다.

- [작업 영역()식](../azure-monitor/logs/workspace-expression.md)을 사용하여 다른 작업 영역에 있는 테이블을 참조합니다.

- 작업 영역()식과 함께 [union 연산자](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor)를 사용하여 여러 작업 영역에 있는 테이블 간에 쿼리를 적용합니다.

저장된 [함수](../azure-monitor/logs/functions.md)를 사용하여 작업 영역 간 쿼리를 단순화할 수 있습니다. 예를 들어 작업 영역에 대한 참조가 긴 경우 `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent`식을 `SecurityEventCustomerA`라는 함수로 저장하는 것이 좋습니다. 그런 다음 쿼리를 `SecurityEventCustomerA | where ...`로 작성할 수 있습니다.

함수는 일반적으로 사용되는 공용 구조체를 단순화할 수도 있습니다. 예를 들어 다음 식을 `unionSecurityEvent`라는 함수로 저장할 수 있습니다.

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

그런 다음 `unionSecurityEvent | where ...`부터 시작하여 두 작업 영역에 걸쳐 쿼리를 작성할 수 있습니다.

#### <a name="cross-workspace-analytics-rules"></a>작업 영역 간 분석 규칙<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
이제 작업 영역 간 쿼리를 예약된 분석 규칙에 포함할 수 있습니다. 다음과 같은 제한 사항이 적용됩니다. 다음 제한 사항에 따라 MSSP의 경우와 같이 중앙 SOC 및 테넌트 간에(Azure Lighthouse 사용) 작업 공간 간 분석 규칙을 사용할 수 있습니다.

- 단일 쿼리에 **최대 20개의 작업공간** 을 포함할 수 있습니다.
- Microsoft 센티널은 쿼리에서 참조 되는 **모든 작업 영역에 배포** 되어야 합니다.
- 교차 작업 공간 분석 규칙에 의해 생성된 알림과 알림에서 생성된 문제는 **규칙이 정의된 작업 공간에만** 존재합니다. 쿼리에서 참조하는 다른 작업 영역에는 표시되지 않습니다.

교차 작업 영역 분석 규칙에 의해 생성된 경고 및 인시던트에는 참조된 모든 작업 영역과 "홈" 작업 영역(규칙이 정의된 위치)의 항목을 포함하여 모든 관련 엔터티가 포함됩니다. 그러면 분석가가 경고 및 인시던트를 전체 파악할 수 있습니다.

> [!NOTE]
> 동일한 쿼리에서 여러 작업 영역을 쿼리하면 성능에 영향을 줄 수 있으므로 논리에 해당 기능이 필요한 경우에만 권장됩니다.

#### <a name="cross-workspace-workbooks"></a>작업 영역 간 통합 문서<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
[통합 문서](./overview.md#workbooks) 는 Microsoft 센티널에 대시보드 및 앱을 제공 합니다. 여러 작업 영역에서 작업하는 경우 작업 영역 간에 모니터링 및 작업을 제공합니다.

통합 문서는 다음과 같은 세 가지 방법 중 하나에서 작업 영역 간 쿼리를 제공할 수 있습니다. 각 메서드는 최종 사용자의 전문성 수준에 따라 달라집니다.

| 메서드  | 설명 | 언제 사용해야 하나요? |
|---------|-------------|--------------------|
| 작업 영역 간 쿼리 작성 | 통합 문서 작성자는 통합 문서에서 위에서 설명된 작업 영역 간 쿼리를 작성할 수 있습니다. | 해당 옵션을 사용하면 통합 문서 작성자가 작업 영역 구조에서 사용자를 완전히 보호할 수 있습니다. |
| 통합 문서에 작업 영역 선택기 추가 | 통합 문서 작성자는 [여기](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)에 설명된 대로 통합 문서의 일부로 작업 영역 선택기를 구현할 수 있습니다. | 해당 옵션은 사용하기 쉬운 드롭다운 박스를 통해 통합 문서에 표시되는 작업 영역에 대한 컨트롤을 사용자에게 제공합니다. |
| 대화형으로 통합 문서 편집 | 기존 통합 문서를 수정하는 고급 사용자는 편집기에서 작업 영역 선택기를 사용하여 대상 작업 영역을 선택하는 것으로 기존 통합 문서 내의 쿼리를 편집할 수 있습니다. | 해당 옵션을 사용하면 고급 사용자가 여러 작업 영역에서 작업할 수 있도록 기존 통합 문서를 쉽게 수정할 수 있습니다. |
|

#### <a name="cross-workspace-hunting"></a>작업 영역 간 헌팅

Microsoft Sentinel은 테이블 및 쿼리 언어를 시작하고 익숙해지도록 미리 로드된 쿼리 샘플을 제공합니다. Microsoft 보안 연구원들은 지속적으로 새 쿼리를 추가하고 기존 쿼리를 세부 조정하는 방식으로 기본 제공 헌팅 쿼리를 개발합니다. 해당 헌팅 쿼리는 진입점을 제공하여 새 검색을 찾고 보안 도구에서 감지하지 못한 침입의 징후를 식별할 수 있습니다.  

작업 영역 간 헌팅 기능을 사용하면, 위협 헌터가 위에 표시된 것처럼 union 연산자와 workspace()식을 사용하여 새로운 헌팅 쿼리를 만들거나 기존 쿼리를 조정하여 여러 작업 영역을 처리하도록 할 수 있습니다.

## <a name="cross-workspace-management-using-automation"></a>자동화 기능을 사용한 작업 영역 간 관리

여러 Microsoft Sentinel 작업 영역을 구성하고 관리하려면 Microsoft Sentinel 관리 API의 사용을 자동화해야 합니다. 경고 규칙, 헌팅 쿼리, 통합 문서 및 플레이북을 포함하여 Microsoft Sentinel 리소스의 배포를 자동화하는 방법에 대한 자세한 내용은 [Microsoft Sentinel 확장: API, 통합 및 관리 자동화를](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)참조하세요.

Microsoft [Sentinel을 코드로 관리하고](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) 프라이빗 GitHub 리포지토리에서 리소스를 배포 및 구성하기 위한 통합된 커뮤니티 기여 방법론에 대한 Microsoft Sentinel을 코드로 배포 및 관리 및 microsoft [Sentinel의](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) DevOps 기능과 Azure Lighthouse 결합을 참조하세요.

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Azure Lighthouse를 사용하여 테넌트 간 작업 영역 관리

위에서 설명한 것처럼 많은 시나리오에서 다양한 Microsoft Sentinel 작업 영역은 서로 다른 Azure AD 테넌트에서 배치할 수 있습니다. [Azure Lighthouse](../lighthouse/overview.md) 사용하여 테넌트 경계를 넘어 모든 작업 영역 간 작업을 확장할 수 있습니다. 이를 통해 관리 테넌트에서 사용자가 모든 테넌트에서 Microsoft Sentinel 작업 영역에서 작업할 수 있습니다. Azure Lighthouse가 [온보딩](../lighthouse/how-to/onboard-customer.md)되면 Azure Portal에서 [디렉터리 + 구독 선택기](./multiple-tenants-service-providers.md#how-to-access-microsoft-sentinel-in-managed-tenants)를 사용하여 관리하려는 작업 영역이 포함된 모든 구독을 선택하여 포털의 다른 작업 영역 선택기에서 사용할 수 있도록 합니다.

Azure Lighthouse 사용하는 경우 각 Microsoft Sentinel 역할에 대한 그룹을 만들고 각 테넌트에서 해당 그룹에 권한을 위임하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 작업 영역 및 테넌트에서 Microsoft Sentinel의 기능을 확장할 수 있는 방법을 알아 냈습니다. Microsoft Sentinel의 작업 영역 간 아키텍처 구현에 대한 실제 지침은 다음 문서를 참조하세요.

- Azure Lighthouse 사용하여 Microsoft Sentinel에서 [여러 테넌트를 사용하는](./multiple-tenants-service-providers.md) 방법을 알아봅니다.
- [여러 작업 영역에서 인시던트를 보고 관리](./multiple-workspace-view.md)하는 원활한 방법을 알아봅니다.
