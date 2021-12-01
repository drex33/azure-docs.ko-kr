---
title: Purview 계정 아키텍처 및 모범 사례
description: 이 문서에서는 Azure Purview 계정 아키텍처의 예제를 제공하고 모범 사례를 설명합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 83e9f9970cc96c7484d828959c16e373acde6462
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133426287"
---
# <a name="azure-purview-accounts-architectures-and-best-practices"></a>Azure Purview 계정 아키텍처 및 모범 사례  

Azure Purview는 통합 데이터 거버넌스 솔루션입니다. Azure Purview 계정을 배포하여 클라우드 및 온-프레임 환경 둘 다에 걸쳐 데이터 자산 전체에서 데이터 거버넌스를 중앙에서 관리합니다. Azure Purview를 중앙 집중식 데이터 거버넌스 솔루션으로 사용하려면 Azure 구독 내에 하나 이상의 Purview 계정을 배포해야 합니다. Purview 인스턴스 수를 최소한으로 유지하는 것이 좋습니다. 그러나 비즈니스 보안 및 규정 준수 요구 사항을 충족하기 위해 더 많은 Purview 인스턴스가 필요한 경우도 있습니다.

## <a name="single-purview-account"></a>단일 Purview 계정

전체 조직에 대해 최소 Purview 계정 수를 배포하는 것이 좋습니다. 이 방법은 플랫폼 내에 상주하는 데이터의 기능으로 플랫폼의 값이 기하급수적으로 증가하는 "네트워크 효과"를 최대한 활용합니다. 

[Azure Purview 컬렉션 계층 구조를](./concept-best-practices-collections.md) 사용하여 단일 Purview 계정 내에 조직의 데이터 관리 구조를 배치합니다. 이 시나리오에서는 하나의 Purview 계정이 Azure 구독에 배포됩니다. 하나 이상의 Azure 구독에서 데이터 원본을 등록하고 Azure Purview 내에서 검사할 수 있습니다. 온-프레미스 또는 다중 클라우드 환경에서 데이터 원본을 등록하고 검사할 수도 있습니다.

:::image type="content" source="media/concept-best-practices/accounts-single-account.png" alt-text="단일 Azure Purview 계정을 보여 주는 스크린샷."lightbox="media/concept-best-practices/accounts-single-account.png":::

## <a name="multiple-purview-accounts"></a>여러 Purview 계정

일부 조직에서는 여러 Azure Purview 계정을 설정해야 할 수 있습니다. Azure Purview 계정 아키텍처를 정의할 때 다음 시나리오를 몇 가지 예로 검토합니다.  

### <a name="testing-new-features"></a>새 기능 테스트 

격리된 환경에서 검사 구성 또는 분류를 테스트할 때 Purview 계정의 새 인스턴스를 만드는 것이 좋습니다. 일부 시나리오의 경우 용어집과 같은 플랫폼의 일부 영역에는 "버전 관리" 기능이 있지만 Purview의 "삭제 가능한" 인스턴스를 사용하여 예상 기능을 자유롭게 테스트한 다음, 프로덕션 인스턴스에 기능을 롤아웃하는 것이 더 쉽습니다.  

또한 롤백을 수행할 수 없는 경우 테스트 Purview 계정을 사용하는 것이 좋습니다. 예를 들어 Purview 계정에 추가된 후 Purview 인스턴스에서 용어집 용어 특성을 제거할 수 없습니다. 이 경우 먼저 테스트 Purview 계정을 사용하는 것이 좋습니다.
 
### <a name="isolating-productionand-non-production-environments"></a>프로덕션 및 비프로덕션 환경 격리 

특히 각 환경에 대해 별도의 데이터 인스턴스가 있는 경우 개발, 테스트 및 프로덕션 환경을 위해 Purview 계정의 개별 인스턴스를 배포하는 것이 좋습니다.  

이 시나리오에서는 해당 Purview 인스턴스 내에서 프로덕션 및 비프로덕션 데이터 원본을 등록하고 검사할 수 있습니다.

필요한 경우 하나 이상의 Purview 인스턴스에 데이터 원본을 등록할 수 있습니다.

:::image type="content" source="media/concept-best-practices/accounts-multiple-accounts.png" alt-text="환경에 따라 여러 Azure Purview 계정을 보여 주는 스크린샷."lightbox="media/concept-best-practices/accounts-multiple-accounts.png":::

### <a name="fulfilling-compliance-requirements"></a>규정 준수 요구 사항 충족  

Azure Purview에서 데이터 원본을 스캔하면 메타데이터와 관련된 정보가 Purview 계정이 배포된 Azure 지역의 Azure Purview 데이터 맵 내에 수집되고 저장됩니다. 특정 지리적 위치에 메타데이터를 포함하는 특정 규정 및 규정 준수 요구 사항이 있는 경우 별도의 Azure Purview 인스턴스를 배포하는 것이 좋습니다.  

조직에 여러 지역에 데이터가 있고 메타데이터를 실제 데이터와 동일한 지역에 유지해야 하는 경우 각 지역에 하나씩 여러 Purview 인스턴스를 배포해야 합니다. 이 경우 각 지역의 데이터 원본을 등록하고 데이터 원본 지역 또는 지역에 해당하는 Purview 계정에서 검사해야 합니다.

:::image type="content" source="media/concept-best-practices/accounts-multiple-regions.png" alt-text="규정 준수 요구 사항에 따라 여러 Azure Purview 계정을 보여 주는 스크린샷."lightbox="media/concept-best-practices/accounts-multiple-regions.png":::

### <a name="having-data-sources-distributed-across-multiple-tenants"></a>데이터 원본을 여러 테넌트 간에 분산  

현재 Purview는 다중 테넌트 지원을 지원하지 않습니다. 서로 다른 Azure Active Directory 테넌트에서 여러 Azure 구독에 분산된 Azure 데이터 원본이 있는 경우 각 테넌트 아래에 별도의 Azure Purview 계정을 배포하는 것이 좋습니다. 

VM 기반 데이터 원본 및 Power BI 테넌트에서 예외가 적용됩니다. 단일 Purview 계정에서 교차 테넌트 Power BI 검색하고 등록하는 방법에 대한 자세한 내용은 교차 [테넌트 Power BI 등록 및 검색을](./register-scan-power-bi-tenant.md)참조하세요. 

:::image type="content" source="media/concept-best-practices/accounts-multiple-tenants.png" alt-text="다중 테넌트 요구 사항에 따라 여러 Azure Purview 계정을 보여 주는 스크린샷."lightbox="media/concept-best-practices/accounts-multiple-tenants.png"::: 

### <a name="billing-model"></a>청구 모델 

예산 모델을 정의하고 조직을 위한 [Azure Purview](https://azure.microsoft.com/pricing/details/azure-purview) 아키텍처를 디자인할 때 Azure Purview 가격 책정 모델을 검토합니다. Purview 계정이 배포된 구독의 단일 Purview 계정에 대해 하나의 청구가 생성됩니다. 이 모델은 Purview 데이터 맵 내에서 메타데이터 검색 및 분류와 같은 다른 Purview 비용에도 적용됩니다.

일부 조직에는 개별적으로 작동하는 여러 사업부(BU)가 있으며, 경우에 따라 청구를 서로 공유하지도 않습니다. 이러한 경우 조직은 각 BU에 대한 Purview 인스턴스를 생성하게 됩니다. 그러나 이 모델은 이상적이지 않습니다. 특히 사업부에서 Azure 청구를 공유하지 않으려는 경우가 많기 때문에 필요할 수 있습니다. 

차지백 및 쇼백 모델의 클라우드 컴퓨팅 비용 모델에 대한 자세한 내용은 [클라우드 회계란?을 참조하세요.](/azure/cloud-adoption-framework/strategy/cloud-accounting)  

## <a name="additional-considerations-and-recommendations"></a>추가 고려 사항 및 권장 사항 

- 간소화된 관리 오버헤드를 위해 Purview 계정 수를 낮게 유지합니다. 여러 Purview 계정을 빌드하려는 경우 Purview 계정에서 추가 검사, 액세스 제어 모델, 자격 증명 및 런타임을 만들고 관리해야 할 수 있습니다. 또한 각 Purview 계정에 대한 분류 및 용어집 용어를 관리해야 할 수도 있습니다.

- 예산 및 재무 요구 사항을 검토합니다. 가능하면 Azure 서비스를 사용할 때 차지백 또는 쇼백 모델을 사용하고 조직 전체에서 Azure Purview 비용을 나누어 Purview 계정 수를 최소로 유지합니다. 

- [Azure Purview 컬렉션을](concept-best-practices-collections.md) 사용하여 조직의 비즈니스 사용자, 데이터 관리 및 거버넌스 팀에 대한 Azure Purview 데이터 맵 내에서 메타데이터 액세스 제어를 정의합니다. 자세한 내용은 [Azure Purview의 액세스 제어를 참조하세요.](./catalog-permissions.md)

- 새 Purview 계정을 배포하기 전에 [Azure Purview 제한을](./how-to-manage-quotas.md#azure-purview-limits) 검토합니다. 현재 테넌트당(결합된 모든 구독) 지역당 Purview 계정의 기본 제한은 3입니다. Azure Purview의 추가 인스턴스를 배포하기 전에 구독 또는 테넌트에서 이 제한을 늘리려면 Microsoft 지원에 문의해야 할 수 있습니다.  

- 환경에 새 [Purview](./create-catalog-portal.md#prerequisites) 계정을 배포하기 전에 Azure Purview 필수 구성 요소 검토
  
## <a name="next-steps"></a>다음 단계
-  [Purview 계정 만들기](./create-catalog-portal.md)
