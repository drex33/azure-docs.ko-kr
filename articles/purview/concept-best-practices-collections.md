---
title: Purview 컬렉션 아키텍처 및 모범 사례
description: 이 문서에서는 Azure Purview 컬렉션 아키텍처의 예제를 제공하고 모범 사례를 설명합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 838730453f5d49efd756abce40faec74513d52b2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390333"
---
# <a name="azure-purview-collections-architectures-and-best-practices"></a>Azure Purview 컬렉션 아키텍처 및 모범 사례  

Azure Purview의 핵심인 데이터 맵은 데이터 자산 전체에서 자산 및 해당 메타데이터의 최신 맵을 유지하는 PaaS(Platform as a Service) 구성 요소입니다. 데이터 맵을 수화하려면 데이터 원본을 등록하고 검사해야 합니다. 조직에는 중앙 집중식 또는 탈중앙화 팀에서 관리하고 관리하는 수천 개의 데이터 원본이 있을 수 있습니다.  

Azure [Purview의 컬렉션은](./how-to-create-and-manage-collections.md) 메타데이터의 조직 매핑을 지원합니다. 컬렉션을 사용하면 플랫 구조 대신 계층 구조에서 데이터 원본, 검색 및 자산을 관리하고 유지 관리할 수 있습니다. 컬렉션을 사용하면 조직에서 Azure Purview를 사용하여 환경을 관리하는 방법에 따라 데이터 환경의 사용자 지정 계층적 모델을 빌드할 수 있습니다.

또한 컬렉션은 데이터 맵의 메타데이터에 대한 보안 경계를 제공합니다. 컬렉션, 데이터 원본 및 메타데이터에 대한 액세스는 최소 권한 모델에 따라 Azure Purview의 컬렉션 계층 구조에 따라 설정 및 유지 관리됩니다. 
- 사용자는 자신의 작업을 수행하는 데 필요한 최소 액세스 권한을 갖습니다. 
- 사용자는 필요하지 않은 중요한 데이터에 액세스할 수 없습니다.

## <a name="intended-audience"></a>대상 그룹

- 데이터 아키텍처 팀 
- 데이터 거버넌스 및 관리 팀
- 데이터 보안 팀

## <a name="why-do-you-need-to-define-collections-and-an-authorization-model-for-your-azure-purview-account"></a>Azure Purview 계정에 대한 컬렉션 및 권한 부여 모델을 정의해야 하는 이유는 무엇인가요? 

Azure Purview에서 컬렉션을 배포하여 다음 요구 사항을 충족하는 것이 좋습니다. 

- 데이터 원본을 구성하고, 자산을 배포하고, 비즈니스 요구 사항, 데이터의 지리적 분포, 데이터 관리 팀, 부서 또는 비즈니스 기능에 따라 검사를 실행합니다. 

- 해당 컬렉션에 역할을 할당하여 데이터 원본 및 자산의 소유권을 해당 팀에 위임합니다. 

- 컬렉션을 통해 자산을 검색하고 필터링합니다. 
 

## <a name="define-a-collection-hierarchy"></a>컬렉션 계층 정의  

### <a name="design-considerations"></a>디자인 고려 사항  

- 각 Purview 계정은 기본 _루트 컬렉션_ 를 통해 만들어집니다. 루트 컬렉션 이름은 Azure Purview 계정 이름과 동일합니다. 루트 컬렉션은 제거할 수 없습니다. 루트 컬렉션의 이름을 변경하려면 Purview 관리 센터에서 Purview 계정의 이름을 변경할 수 있습니다.   

- 컬렉션은 데이터 원본, 검색, 자산 및 역할 할당을 보유할 수 있습니다.

- 컬렉션에는 필요한 만큼의 자식 컬렉션이 있을 수 있습니다. 그러나 각 컬렉션에는 하나의 부모 컬렉션만 있을 수 있습니다. 루트 컬렉션 위에 컬렉션을 배포할 수 없습니다.

- 데이터 원본, 검색 및 자산은 하나의 컬렉션에만 속할 수 있습니다. 

- Azure Purview의 컬렉션 계층 구조는 최대 8개 수준의 깊이를 가진 최대 256개의 컬렉션을 지원할 수 있습니다. 루트 컬렉션은 포함되지 않습니다. 

- 의도적으로 단일 Purview 계정에서 데이터 원본을 여러 번 등록할 수 없습니다. 이 아키텍처는 단일 데이터 원본에 다양한 수준의 액세스 제어를 할당할 위험을 방지하는 데 도움이 됩니다. 여러 팀이 단일 데이터 원본의 메타데이터를 사용하는 경우 부모 컬렉션에서 데이터 원본을 등록하고 관리할 수 있습니다. 그런 다음 각 하위 컬렉션 아래에 해당 검사를 만들어 관련 자산이 각 자식 컬렉션 아래에 표시되도록 할 수 있습니다.

- 계보 연결 및 아티팩트 는 데이터 원본이 하위 수준 컬렉션에 등록되어 있더라도 루트 컬렉션에 연결됩니다.

- 새 검사를 실행하면 기본적으로 검색이 데이터 원본과 동일한 컬렉션에 배포됩니다. 필요에 따라 다른 하위 데이터 정렬을 선택하여 검사를 실행할 수 있습니다. 결과적으로 자산은 하위 데이터 정렬 아래에 속합니다. 

- 현재는 컬렉션 간에 데이터 원본을 이동할 수 없습니다. 데이터 원본을 다른 컬렉션으로 이동해야 하는 경우 모든 자산을 삭제하고, 원래 컬렉션에서 데이터 원본을 제거하고, 대상 컬렉션에서 데이터 원본을 다시 등록해야 합니다.

- 사용자에게 원본 및 대상 컬렉션에 대한 데이터 보호기 역할이 부여된 경우 컬렉션 간에 자산을 이동할 수 있습니다. 

- 현재 컬렉션의 이동 및 이름 바꾸기와 같은 특정 작업은 허용되지 않습니다. 

- 자산, 연결된 검색, 데이터 원본 또는 자식 컬렉션이 없는 경우 컬렉션을 삭제할 수 있습니다.

- 데이터 원본, 검색 및 자산이 Azure Purview 데이터 맵에 있는 경우 컬렉션에 속해야 합니다.    

<!-- 
- Moving data sources across collections is allowed if the user is granted the Data Source Admin role for the source and destination collections. 

- Moving assets across collections is allowed if the user is granted the Data Curator role for the source and destination collections. 

-->

### <a name="design-recommendations"></a>디자인 권장 사항 

- 컬렉션 구조를 계획하기 전에 [Azure Purview 계정 모범 사례를](./deployment-best-practices.md#determine-the-number-of-purview-instances) 검토하고 조직에 필요한 Purview 계정 수를 적절하게 정의합니다.  

- 조직의 보안 요구 사항과 데이터 관리 및 거버넌스 구조를 기반으로 컬렉션 아키텍처를 디자인하는 것이 좋습니다. 이 문서에서 [권장되는 컬렉션 아키타입을 검토합니다.](#collections-archetypes)

- 향후 확장성을 위해 루트 컬렉션 아래에 조직에 대한 최상위 컬렉션을 만드는 것이 좋습니다. 루트 컬렉션 대신 최상위 컬렉션에서 관련 역할을 할당합니다.  

- Azure Purview에서 컬렉션을 빌드할 때 보안 및 액세스 관리를 디자인 의사 결정 프로세스의 일부로 고려합니다. 

- 각 컬렉션에는 이름 특성과 친숙한 이름 특성이 있습니다. Azure [Purview Studio를](https://web.purview.azure.com/resource/) 사용하여 컬렉션을 배포하는 경우 중복을 방지하기 위해 시스템에서 컬렉션에 임의의 6자 이름을 자동으로 할당합니다. 복잡성을 줄이려면 특히 동일한 수준에서 컬렉션에서 중복된 친숙한 이름을 사용하지 않도록 합니다.  

- 가능하면 조직 구조를 깊게 중첩된 컬렉션 계층 구조로 복제하지 않도록 합니다. 이 작업을 방지할 수 없는 경우 계층 구조의 모든 컬렉션에 대해 서로 다른 이름을 사용하여 컬렉션을 쉽게 구분할 수 있도록 해야 합니다.

- 컬렉션 및 역할 할당을 대량으로 배포하려는 경우 API를 사용하여 컬렉션 배포를 자동화합니다.

- 전용 SPN(서비스 사용자 이름)을 사용하여 API를 사용하여 컬렉션 및 역할 할당에 대한 작업을 실행합니다. SPN을 사용하면 상승된 권한이 있는 사용자 수가 줄어들고 최소 권한 지침을 따를 수 있습니다.

## <a name="define-an-authorization-model"></a>권한 부여 모델 정의

Azure Purview 데이터 평면 역할은 Azure Purview에서 관리됩니다. Purview 계정을 배포하면 Purview 계정의 작성자에게 루트 컬렉션에서 다음 역할이 자동으로 할당됩니다. Purview Studio 또는 프로그래밍 방법을 사용하여 Azure [Purview에서](https://web.purview.azure.com/resource/) 역할을 직접 할당하고 관리할 수 있습니다.

  - **컬렉션 관리자는** Purview 컬렉션과 세부 정보를 편집하고 하위 컬렉션을 추가할 수 있습니다. 또한 사용자가 관리자인 컬렉션에서 다른 Purview 역할에 사용자를 추가할 수도 있습니다.
  - **데이터 원본 관리자는** 데이터 원본 및 데이터 검색을 관리할 수 있습니다.
  - **데이터 표시자에서는** 카탈로그 데이터 자산을 생성, 읽기, 수정 및 삭제하고 자산 간의 관계를 설정할 수 있습니다.
  - **데이터 판독기는** 카탈로그 데이터 자산에 액세스할 수 있지만 수정할 수는 없습니다.

### <a name="design-considerations"></a>디자인 고려 사항  

- Azure Purview 액세스 관리가 데이터 평면으로 이동되었습니다. Azure Resource Manager 역할은 더 이상 사용되지 않으므로 Azure Purview를 사용하여 역할을 할당해야 합니다. 

- Azure Purview에서 Purview 계정이 배포된 동일한 Azure AD 테넌트에서 azure AD(Azure Active Directory)의 사용자, 보안 그룹 및 서비스 주체(관리 ID 포함)에 역할을 할당할 수 있습니다.
  
- 외부 사용자에게 Purview 역할을 할당하려면 먼저 Azure AD 테넌트에서 B2B 사용자로 게스트 계정을 추가해야 합니다. 

- 기본적으로 Collection Admins는 자산을 읽거나 수정할 수 있는 액세스 권한이 없습니다. 그러나 액세스 권한을 상승시키고 더 많은 역할에 자신을 추가할 수 있습니다.

- 기본적으로 모든 역할 할당은 모든 자식 컬렉션에서 자동으로 상속됩니다. 그러나 루트 컬렉션을 제외한 모든 컬렉션에 대해 **상속된 권한 제한을** 사용하도록 설정할 수 있습니다. **상속된 권한 제한은** 컬렉션 관리자 역할을 제외한 모든 부모 컬렉션에서 상속된 역할을 제거합니다. 

- Azure Data Factory 연결의 경우: Azure Data Factory 연결하려면 루트 컬렉션에 대한 컬렉션 관리자이어야 합니다.

- 계보를 위해 Azure Data Factory 연결해야 하는 경우 Purview 루트 컬렉션 수준에서 데이터 팩터리의 관리 ID에 데이터 경계 역할을 부여합니다. 제작 UI에서 Data Factory Purview에 연결하면 Data Factory 이러한 역할 할당을 자동으로 추가하려고 시도합니다. Purview 루트 컬렉션에 컬렉션 관리자 역할이 있는 경우 이 작업이 작동합니다. 

### <a name="design-recommendations"></a>디자인 권장 사항 

- Purview 계정 수준 잠금을 방지하려면 Azure Purview 루트 컬렉션 수준에서 컬렉션 관리자 역할에 대한 [비상 액세스](/azure/active-directory/users-groups-roles/directory-emergency-access) 또는 비상 전략을 구현하는 것이 좋습니다. 응급 계정 사용 프로세스를 문서화합니다. 

    > [!NOTE]
    > 특정 시나리오에서는 응급 계정을 사용하여 Azure Purview에 로그인해야 할 수 있습니다. 다른 사람이 Purview에 로그인할 수 없거나 회사 인증 문제로 인해 다른 관리자가 특정 작업을 수행할 수 없는 경우 조직 수준 액세스 문제를 해결하려면 이 유형의 계정이 필요할 수 있습니다. 클라우드 전용 사용자를 사용하여 [응급 액세스 계정](/azure/active-directory/users-groups-roles/directory-emergency-access) 구현과 관련된 Microsoft 모범 사례를 따르는 것이 좋습니다.
    >
    > 이전 컬렉션 관리자를 사용할 수 없는 경우 [이 문서의](./concept-account-upgrade.md#what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin) 지침에 따라 Purview 루트 컬렉션에 대한 액세스를 복구합니다.

- 루트 컬렉션 관리자 수를 최소화합니다. SPN 및 분리 계정을 포함하여 루트 컬렉션에서 최대 3명의 컬렉션 관리자 사용자를 할당합니다. 대신 컬렉션 관리자 역할을 최상위 컬렉션 또는 하위 컬렉션에 할당합니다.

- 개별 사용자 대신 그룹에 역할을 할당하여 관리 오버헤드 및 개별 역할 관리 오류를 줄입니다. 

- 자동화를 위해 루트 컬렉션에서 서비스 주체를 할당합니다.

- 보안을 강화하려면 적어도 컬렉션 관리자, 데이터 원본 관리자 및 데이터 관리자에 대해 다단계 인증을 사용하여 Azure AD 조건부 액세스를 사용하도록 설정합니다. 응급 계정이 조건부 액세스 정책에서 제외 되는지 확인 합니다.
 
## <a name="collections-archetypes"></a>컬렉션 archetype

중앙 집중화 되 고 분산 되거나 하이브리드 데이터 관리 및 거 버 넌 스 모델을 기반으로 Azure 부서의 범위 컬렉션을 배포할 수 있습니다. 비즈니스 및 보안 요구 사항에 따라이 결정을 내립니다.

### <a name="example-1-single-region-organization"></a>예 1: 단일 지역 조직 

이 구조는 다음을 수행 하는 조직에 적합 합니다. 
- 주로 단일 지리적 위치를 기반으로 합니다. 
- 다음 수준의 데이터 관리가 부서, 팀 또는 프로젝트에 속하는 중앙 집중화 된 데이터 관리 및 거 버 넌 스 팀이 있어야 합니다.  

컬렉션 계층 구조는 다음 감축할 구성 됩니다. 

- 루트 컬렉션 (기본값)
- Contoso (최상위 수준 컬렉션)
- 부서 (각 부서에 대 한 위임 된 컬렉션) 
- Teams 또는 프로젝트 (프로젝트를 기반으로 하는 추가 분리)

각 데이터 원본은 해당 컬렉션에서 등록 되 고 검색 됩니다. 따라서 자산은 동일한 컬렉션에도 표시 됩니다. 

조직 수준 공유 데이터 원본은 Hub-Shared 컬렉션에서 등록 되 고 검색 됩니다. 

부서 수준 공유 데이터 원본은 부서 컬렉션에서 등록 되 고 검색 됩니다. 

:::image type="content" source="media/concept-best-practices/collections-example-1.png" alt-text="첫 번째 Azure 부서의 범위 컬렉션 예제를 보여 주는 스크린샷"lightbox="media/concept-best-practices/collections-example-1.png":::

### <a name="example-2-multiregion-organization"></a>예 2: 다중 지역 조직

이 시나리오는 조직에 유용 합니다. 
- 여러 지역에 존재 합니다. 
- 데이터 거 버 넌 스 팀은 각 지역에서 중앙 집중화 되거나 분산 됩니다.
- 데이터 관리 팀은 각 지리적 위치에 배포 됩니다. 

컬렉션 계층 구조는 다음 감축할 구성 됩니다. 

- 루트 컬렉션 (기본값)
- FourthCoffee (최상위 컬렉션)
- 지리적 위치 (데이터 원본 및 데이터 소유자가 있는 지리적 위치를 기준으로 하는 중간 수준 컬렉션)
- 부서 (각 부서에 대 한 위임 된 컬렉션) 
- Teams 또는 프로젝트 (팀 또는 프로젝트를 기반으로 하는 추가 분리)

이 시나리오에서 각 지역은 부서의 범위 계정의 최상위 컬렉션 아래에 자체 하위 컬렉션 있습니다. 데이터 원본은 자신의 지리적 위치에서 해당 하위 컬렉션에 등록 되 고 검색 됩니다. 그러면 자산이 해당 지역에 대 한 하위 컬렉션 계층 구조에도 표시 됩니다. 

중앙 집중화 된 데이터 관리 및 거 버 넌 스 팀이 있는 경우 최상위 수준 컬렉션에서 액세스 권한을 부여할 수 있습니다. 이렇게 하면 데이터 맵의 전체 데이터 공간에 대 한 감독을 얻게 됩니다. 필요에 따라 중앙 집중식 팀은 공유 데이터 원본을 등록 하 고 검색할 수 있습니다.

지역 기반 데이터 관리 및 거 버 넌 스 팀은 하위 수준에서 해당 컬렉션에 대 한 액세스 권한을 얻을 수 있습니다.

부서 수준 공유 데이터 원본은 부서 컬렉션에서 등록 되 고 검색 됩니다. 

:::image type="content" source="media/concept-best-practices/collections-example-2.png" alt-text="두 번째 Azure 부서의 범위 collections 예제를 보여 주는 스크린샷"lightbox="media/concept-best-practices/collections-example-2.png":::

### <a name="example-3-multiregion-data-transformation"></a>예 3: 다중 지역, 데이터 변환

이 시나리오는 지리적 위치 및 데이터 변환 상태에 따라 메타 데이터 액세스 관리를 배포 하려는 경우에 유용할 수 있습니다. 데이터를 더 의미 있게 변환할 수 있는 데이터 과학자 및 데이터 엔지니어는 원시 및 영역 구체화를 관리할 수 있습니다. 그런 다음 데이터를 생성 또는 큐 레이트 영역으로 이동할 수 있습니다.  

컬렉션 계층 구조는 다음 감축할 구성 됩니다. 

- 루트 컬렉션 (기본값)
- Fabrikam (최상위 수준 컬렉션)
- 지리적 위치 (데이터 원본 및 데이터 소유자가 있는 지리적 위치를 기준으로 하는 중간 수준 컬렉션)
- 데이터 변환 단계 (Raw, 구체화, 생성/큐 레이트) 

데이터 과학자 및 데이터 엔지니어는 메타 데이터를 만들 수 있도록 해당 영역에 대 한 데이터 Curators 역할을 가질 수 있습니다. 큐 레이트 영역에 대 한 데이터 판독기 액세스는 전체 데이터 가상 사용자 및 비즈니스 사용자에 게 부여할 수 있습니다. 

:::image type="content" source="media/concept-best-practices/collections-example-3.png" alt-text="세 번째 Azure 부서의 범위 collections 예제를 보여 주는 스크린샷"lightbox="media/concept-best-practices/collections-example-3.png":::

### <a name="example-4-multiregion-business-functions"></a>예 4: 다중 지역, 비즈니스 기능 

이 옵션은 비즈니스 기능을 기반으로 하 여 메타 데이터 및 액세스 관리를 구성 해야 하는 조직에서 사용할 수 있습니다.

컬렉션 계층 구조는 다음 감축할 구성 됩니다. 

- 루트 컬렉션 (기본값)
- AdventureWorks (최상위 컬렉션)
- 지리적 위치 (데이터 원본 및 데이터 소유자가 있는 지리적 위치를 기준으로 하는 중간 수준 컬렉션)
- 주요 비즈니스 기능 또는 클라이언트 (함수 또는 클라이언트를 기반으로 하는 추가 분리)

각 지역은 부서의 범위 계정의 최상위 컬렉션 아래에 자체 하위 컬렉션 있습니다. 데이터 원본은 자신의 지리적 위치에서 해당 하위 컬렉션에 등록 되 고 검색 됩니다. 그러면 자산이 해당 지역에 대 한 하위 컬렉션 계층 구조에 추가 됩니다. 

중앙 집중화 된 데이터 관리 및 거 버 넌 스 팀이 있는 경우 최상위 수준 컬렉션에서 액세스 권한을 부여할 수 있습니다. 이렇게 하면 데이터 맵의 전체 데이터 공간에 대 한 감독을 얻게 됩니다. 필요에 따라 중앙 집중식 팀은 공유 데이터 원본을 등록 하 고 검색할 수 있습니다.

지역 기반 데이터 관리 및 거 버 넌 스 팀은 하위 수준에서 해당 컬렉션에 대 한 액세스 권한을 얻을 수 있습니다.
각 사업부에는 고유한 하위 컬렉션 있습니다.

:::image type="content" source="media/concept-best-practices/collections-example-4.png" alt-text="네 번째 Azure 부서의 범위 collections 예제를 보여 주는 스크린샷"lightbox="media/concept-best-practices/collections-example-4.png":::

## <a name="access-management-options"></a>액세스 관리 옵션

전체 조직에서 데이터 democratization를 구현 하려는 경우 데이터 관리, 거 버 넌 스 및 비즈니스 사용자에 게 최상위 수준 컬렉션에서 데이터 읽기 권한자 역할을 할당 합니다. 하위 컬렉션 수준에서 데이터 원본 관리자 및 데이터 큐레이터 역할을 해당 하는 데이터 관리 및 거 버 넌 스 팀에 할당 합니다.

조직에서 메타 데이터 검색 및 검색에 대 한 액세스를 제한 해야 하는 경우 특정 컬렉션 수준에서 데이터 판독기 및 데이터 큐레이터 역할을 할당 합니다. 예를 들어 LATAM 컬렉션이 아닌 미국 컬렉션 수준 에서만 데이터를 읽을 수 있도록 미국 직원을 제한할 수 있습니다. 

일부 컬렉션에 대 한 몇 가지 예외를 제외 하 고 total data democratization가 필요한 경우 부서의 범위 데이터 맵에서 이러한 두 시나리오의 조합을 적용할 수 있습니다. 최상위 수준 컬렉션에서 부서의 범위 역할을 할당 하 고 특정 자식 컬렉션으로 상속을 제한할 수 있습니다.

최상위 수준 컬렉션에서 중앙 집중식 데이터 보안 및 관리 팀에 컬렉션 관리자 역할을 할당 합니다. 하위 수준 컬렉션의 추가 컬렉션 관리를 해당 팀에 위임 합니다.

## <a name="next-steps"></a>다음 단계
-  [부서의 범위에서 컬렉션 만들기 및 사용 권한 할당](./quickstart-create-collection.md)
-  [Azure Purview에서 컬렉션 만들기 및 관리](./how-to-create-and-manage-collections.md)
-  [Azure Purview의 액세스 제어](./catalog-permissions.md)
