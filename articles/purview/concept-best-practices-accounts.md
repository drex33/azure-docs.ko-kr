---
title: 부서의 범위 계정 아키텍처 및 모범 사례
description: 이 문서에서는 Azure 부서의 범위 accounts 아키텍처의 예제를 제공 하 고 모범 사례를 설명 합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: a31e16ffd74808fb6b6232f0cbda3d447cfce513
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008730"
---
# <a name="azure-purview-accounts-architectures-and-best-practices"></a>Azure 부서의 범위 accounts 아키텍처 및 모범 사례  

Azure 부서의 범위는 통합 데이터 거 버 넌 스 솔루션입니다. Azure 부서의 범위 계정을 배포 하 여 클라우드 및 온-프레미스 환경에 걸친 데이터 공간 전체의 데이터 관리를 중앙에서 관리 합니다. Azure 부서의 범위를 중앙화 된 데이터 거 버 넌 스 솔루션으로 사용 하려면 Azure 구독 내에 부서의 범위 계정을 하나 이상 배포 해야 합니다. 부서의 범위 인스턴스 수를 최소로 유지 하는 것이 좋지만, 비즈니스 보안 및 규정 준수 요구 사항을 충족 하기 위해 더 많은 부서의 범위 인스턴스가 필요한 경우도 있습니다.

## <a name="intended-audience"></a>대상 그룹

- 데이터 아키텍처 팀
- 데이터 거 버 넌 스 및 관리 팀
- 데이터 보안 팀

## <a name="single-purview-account"></a>Single 부서의 범위 계정

전체 조직에 대 한 최소 개수의 부서의 범위 계정을 배포 하는 것이 좋습니다. 이 방법을 사용 하면 플랫폼 내에 있는 데이터의 기능으로 서 플랫폼의 값이 급격 하 게 향상 되는 "네트워크 효과"가 최대한 활용 됩니다. 

[Azure 부서의 범위 collections 계층 구조](./concept-best-practices-collections.md) 를 사용 하 여 단일 부서의 범위 계정 내에서 조직의 데이터 관리 구조를 배치 합니다. 이 시나리오에서는 하나의 부서의 범위 계정이 Azure 구독에 배포 됩니다. 하나 이상의 Azure 구독에서 데이터 원본을 등록 하 고 Azure 부서의 범위 내에서 검색할 수 있습니다. 또한 온-프레미스 또는 다중 클라우드 환경에서 데이터 원본을 등록 하 고 검색할 수 있습니다.

:::image type="content" source="media/concept-best-practices/accounts-single-account.png" alt-text="단일 Azure 부서의 범위 계정을 보여 주는 스크린샷"lightbox="media/concept-best-practices/accounts-single-account.png":::

## <a name="multiple-purview-accounts"></a>여러 부서의 범위 계정

일부 조직에서는 여러 Azure 부서의 범위 계정을 설정 해야 할 수 있습니다. Azure 부서의 범위 accounts 아키텍처를 정의할 때 몇 가지 예를 들어 다음 시나리오를 검토 합니다.  

### <a name="testing-new-features"></a>새 기능 테스트 

격리 된 환경에서 검사 구성 또는 분류를 테스트할 때 부서의 범위 계정의 새 인스턴스를 만드는 것이 좋습니다. 일부 시나리오의 경우 용어집과 같은 플랫폼의 일부 영역에는 "버전 관리" 기능이 있지만, 예상 되는 기능을 자유롭게 테스트 하 고 프로덕션 인스턴스에 기능을 롤아웃 하도록 계획 하는 "부서의 범위" 인스턴스를 포함 하는 것이 더 쉬울 수 있습니다.  

또한 롤백을 수행할 수 없는 경우 test 부서의 범위 계정을 사용 하는 것이 좋습니다. 예를 들어, 부서의 범위 계정에 추가 된 후에는 부서의 범위 인스턴스에서 용어집 용어 특성을 제거할 수 없습니다. 이 경우 먼저 test 부서의 범위 계정을 사용 하는 것이 좋습니다.
 
### <a name="isolating-productionand-non-production-environments"></a>프로덕션 환경과 비프로덕션 환경 격리 

개발, 테스트 및 프로덕션 환경에 대해 별도의 부서의 범위 계정 인스턴스를 배포 하는 것이 좋습니다 .이는 각 환경에 대 한 별도의 데이터 인스턴스가 있는 경우에 유용 합니다.  

이 시나리오에서는 프로덕션 및 비 프로덕션 데이터 원본을 해당 부서의 범위 인스턴스 내에서 등록 하 고 검색할 수 있습니다.

필요에 따라 두 개 이상의 부서의 범위 인스턴스에 데이터 원본을 등록할 수 있습니다.

:::image type="content" source="media/concept-best-practices/accounts-multiple-accounts.png" alt-text="환경을 기반으로 여러 Azure 부서의 범위 계정을 보여 주는 스크린샷"lightbox="media/concept-best-practices/accounts-multiple-accounts.png":::

### <a name="fulfilling-compliance-requirements"></a>규정 준수 요구 사항 충족  

Azure 부서의 범위에서 데이터 원본을 검색 하는 경우 메타 데이터와 관련 된 정보는 수집 되어 부서의 범위 계정이 배포 된 Azure 지역의 Azure 부서의 범위 데이터 맵 내에 저장 됩니다. 특정 지리적 위치에 메타 데이터를 포함 하는 특정 규제 및 규정 준수 요구 사항이 있는 경우 Azure 부서의 범위의 개별 인스턴스를 배포 하는 것이 좋습니다.  

조직에서 여러 지역에 데이터를 보유 하 고 있고 실제 데이터와 동일한 지역에 메타 데이터를 유지 해야 하는 경우 각 지리에 대해 하나씩 여러 개의 부서의 범위 인스턴스를 배포 해야 합니다. 이 경우 각 지역의 데이터 원본을 등록 하 고 데이터 원본 지역 또는 지리에 해당 하는 부서의 범위 계정에 검색 해야 합니다.

:::image type="content" source="media/concept-best-practices/accounts-multiple-regions.png" alt-text="규정 준수 요구 사항에 따라 여러 Azure 부서의 범위 계정을 보여 주는 스크린샷"lightbox="media/concept-best-practices/accounts-multiple-regions.png":::

### <a name="having-data-sources-distributed-across-multiple-tenants"></a>데이터 원본을 여러 테 넌 트에 분산 하는 방법  

현재 부서의 범위는 다중 테 넌 트를 지원 하지 않습니다. 여러 azure 구독에 다른 Azure Active Directory 테 넌 트에 분산 된 azure 데이터 소스가 있는 경우 각 테 넌 트에 별도의 azure 부서의 범위 계정을 배포 하는 것이 좋습니다. 

VM 기반 데이터 원본 및 Power BI 테 넌 트에는 예외가 적용 됩니다. 단일 부서의 범위 계정에서 교차 테 넌 트 Power BI를 검색 하 고 등록 하는 방법에 대 한 자세한 내용은 [테 넌 트 간 Power BI 등록 및 검사](/register-scan-power-bi-tenant#register-and-scan-a-cross-tenant-power-bi)를 참조 하세요. 

:::image type="content" source="media/concept-best-practices/accounts-multiple-tenants.png" alt-text="다중 테 넌 트 요구 사항에 따라 여러 Azure 부서의 범위 계정을 보여 주는 스크린샷"lightbox="media/concept-best-practices/accounts-multiple-tenants.png"::: 

### <a name="billing-model"></a>청구 모델 

예산 모델을 정의 하 고 조직에 대 한 Azure 부서의 범위 아키텍처를 디자인 하는 경우 [Azure 부서의 범위 가격 책정 모델](https://azure.microsoft.com/pricing/details/azure-purview) 을 검토 합니다. 부서의 범위 계정이 배포 된 구독에서 단일 부서의 범위 계정에 대해 하나의 청구가 생성 됩니다. 이 모델은 부서의 범위 데이터 맵 내에서 메타 데이터를 검색 하 고 분류 하는 등의 다른 부서의 범위 비용에도 적용 됩니다.

일부 조직에는 개별적으로 작동 하는 많은 버스 (비즈니스 단위)가 있으며, 경우에 따라 서로를 사용 하 여 대금 청구를 공유 하지 않을 수도 있습니다. 이러한 경우 조직은 각 BU에 대한 Purview 인스턴스를 생성하게 됩니다. 그러나이 모델은 특히 비즈니스 단위가 Azure 청구를 공유 하지 않기 때문에 필요할 수 있습니다. 

비용 정산 및 쇼 백 모델의 클라우드 컴퓨팅 비용 모델에 대 한 자세한 내용은 [클라우드 계정용 이란?](/cloud-adoption-framework/strategy/cloud-accounting)을 참조 하세요.  

## <a name="additional-considerations-and-recommendations"></a>추가 고려 사항 및 권장 사항 

- 간소화 된 관리 오버 헤드를 위해 부서의 범위 계정 수를 낮게 유지 합니다. 여러 부서의 범위 계정을 구축할 계획인 경우에는 부서의 범위 계정에서 추가 검색, 액세스 제어 모델, 자격 증명 및 런타임을 만들고 관리 해야 할 수 있습니다. 또한 각 부서의 범위 계정에 대 한 분류 및 용어집 용어를 관리 해야 할 수도 있습니다.

- 예산 및 금융 요구 사항을 검토 합니다. 가능 하면 azure 서비스를 사용 하는 경우 비용 정산 또는 쇼 백 모델을 사용 하 고 조직 전체에서 azure 부서의 범위의 비용을 분산 하 여 부서의 범위 계정 수를 최소로 유지 합니다. 

- [Azure 부서의 범위 컬렉션](concept-best-practices-collections.md) 을 사용 하 여 조직의 비즈니스 사용자, 데이터 관리 및 거 버 넌 스 팀에 대 한 Azure 부서의 범위 데이터 맵 내에서 메타 데이터 액세스 제어를 정의 합니다. 자세한 내용은 [Azure 부서의 범위의 Access control](./catalog-permissions.md)을 참조 하세요.

- 새 부서의 범위 계정을 배포 하기 전에 [Azure 부서의 범위 한도](./how-to-manage-quotas.md#azure-purview-limits) 를 검토 합니다. 현재, 테 넌 트 당 부서의 범위 계정의 기본 제한 (모든 구독이 결합 됨)은 3입니다. Azure 부서의 범위의 추가 인스턴스를 배포 하기 전에 Microsoft 지원에 문의 하 여 구독 또는 테 넌 트의이 제한을 늘려야 할 수도 있습니다.  

- 환경에 새 부서의 범위 계정을 배포 하기 전에 [Azure 부서의 범위 필수 구성 요소](./create-catalog-portal.md#prerequisites) 를 검토 합니다.
  
## <a name="next-steps"></a>다음 단계
-  [Purview 계정 만들기](./create-catalog-portal.md)