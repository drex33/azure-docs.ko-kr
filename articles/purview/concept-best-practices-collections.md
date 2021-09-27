---
title: 부서의 범위 컬렉션 아키텍처 및 모범 사례
description: 이 문서에서는 Azure 부서의 범위 컬렉션 아키텍처의 예제를 제공 하 고 모범 사례를 설명 합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: bc3005731baf036a719ed76b1f03e3f94051fba3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634322"
---
# <a name="azure-purview-collections-architectures-and-best-practices"></a>Azure 부서의 범위 컬렉션 아키텍처 및 모범 사례  

Azure 부서의 범위의 핵심에서 데이터 맵은 데이터 공간 전체의 자산 및 메타 데이터에 대 한 최신 맵을 유지 하는 PaaS (platform as a service) 구성 요소입니다. 데이터 맵을 하이드레이션 하며 나중 데이터 원본을 등록 하 고 검색 해야 합니다. 조직에는 중앙 집중식 또는 분산 된 팀에서 관리 하 고 관리 하는 수천 개의 데이터 원본이 있을 수 있습니다.  

Azure 부서의 범위의 [컬렉션](./how-to-create-and-manage-collections.md) 은 메타 데이터의 조직 매핑을 지원 합니다. 컬렉션을 사용 하 여 플랫 구조 대신 계층 구조에서 데이터 원본, 검색 및 자산을 관리 하 고 유지 관리할 수 있습니다. 컬렉션을 사용 하면 조직에서 Azure 부서의 범위를 사용 하 여 사용자의 가로를 관리 하는 방법에 따라 데이터 가로의 사용자 지정 계층 구조 모델을 빌드할 수 있습니다.

또한 컬렉션은 데이터 맵의 메타 데이터에 대 한 보안 경계를 제공 합니다. 컬렉션, 데이터 원본 및 메타 데이터에 대 한 액세스는 최소 권한 모델에 따라 Azure 부서의 범위의 컬렉션 계층 구조에 따라 설정 및 유지 관리 됩니다. 
- 사용자는 작업을 수행 하는 데 필요한 최소한의 액세스 권한이 있습니다. 
- 사용자는 필요 하지 않은 중요 한 데이터에 액세스할 수 없습니다.

## <a name="intended-audience"></a>대상 그룹

- 데이터 아키텍처 팀 
- 데이터 거 버 넌 스 및 관리 팀
- 데이터 보안 팀

## <a name="why-do-you-need-to-define-collections-and-an-authorization-model-for-your-azure-purview-account"></a>Azure 부서의 범위 계정에 대 한 컬렉션 및 권한 부여 모델을 정의 해야 하는 이유는 무엇 인가요? 

Azure 부서의 범위에 컬렉션을 배포 하 여 다음 요구 사항을 충족 하는 것이 좋습니다. 

- 비즈니스 요구 사항, 데이터의 지리적 분포, 데이터 관리 팀, 부서 또는 비즈니스 기능에 따라 데이터 원본을 구성 하 고, 자산을 배포 하 고, 검색을 실행 합니다. 

- 해당 컬렉션에 역할을 할당 하 여 데이터 원본 및 자산의 소유권을 해당 팀에 위임 합니다. 

- 컬렉션을 기준으로 자산을 검색 하 고 필터링 합니다. 
 

## <a name="define-a-collection-hierarchy"></a>컬렉션 계층 구조 정의  

### <a name="design-considerations"></a>설계 고려 사항  

- 각 부서의 범위 계정은 기본 _루트 컬렉션_ 을 사용 하 여 생성 됩니다. 루트 컬렉션 이름은 Azure 부서의 범위 계정 이름과 동일 합니다. 루트 컬렉션은 제거할 수 없습니다. 루트 컬렉션의 이름을 변경 하려면 부서의 범위 계정의 이름을 부서의 범위 관리 센터에서 변경할 수 있습니다.   

- 컬렉션은 데이터 원본, 검색, 자산 및 역할 할당을 보유할 수 있습니다.

- 컬렉션에는 필요한 만큼의 자식 컬렉션이 있을 수 있습니다. 하지만 각 컬렉션에는 부모 컬렉션이 하나만 있을 수 있습니다. 루트 컬렉션 위에는 컬렉션을 배포할 수 없습니다.

- 데이터 원본, 검색 및 자산은 하나의 컬렉션에만 속할 수 있습니다. 

- Azure 부서의 범위의 컬렉션 계층은 최대 8 개의 깊이의 컬렉션을 300 지원할 수 있습니다. 여기에는 루트 컬렉션이 포함 되지 않습니다. 

- 기본적으로 단일 부서의 범위 계정에서 데이터 원본을 여러 번 등록할 수 없습니다. 이 아키텍처는 단일 데이터 원본에 서로 다른 수준의 액세스 제어를 할당 하는 위험을 방지 하는 데 도움이 됩니다. 여러 팀이 단일 데이터 원본의 메타 데이터를 사용 하는 경우에는 부모 컬렉션에서 데이터 원본을 등록 하 고 관리할 수 있습니다. 그런 다음 각 하위 컬렉션 아래에 해당 하는 검색을 만들어 각 자식 컬렉션 아래에 관련 자산이 표시 되도록 할 수 있습니다.

- 계보 연결 및 아티팩트는 데이터 소스가 하위 수준 컬렉션에 등록 된 경우에도 루트 컬렉션에 연결 됩니다.

- 새 검색을 실행 하는 경우 기본적으로 검색은 데이터 원본과 동일한 컬렉션에 배포 됩니다. 필요한 경우 다른 하위 컬렉션를 선택 하 여 검색을 실행할 수 있습니다. 따라서 자산은 하위 컬렉션 아래에 포함 됩니다. 

- 현재 컬렉션 간에 데이터 원본을 이동 하는 것은 허용 되지 않습니다. 다른 컬렉션에서 데이터 원본을 이동 해야 하는 경우 모든 자산을 삭제 하 고, 원래 컬렉션에서 데이터 원본을 제거 하 고, 대상 컬렉션 아래에서 데이터 원본을 다시 등록 해야 합니다.

- 사용자에 게 원본 및 대상 컬렉션에 대 한 데이터 큐레이터 역할이 부여 된 경우 컬렉션 간에 자산을 이동할 수 있습니다. 

- 현재는 컬렉션의 삭제, 이동 및 이름 바꾸기와 같은 특정 작업을 수행할 수 없습니다. 

- 데이터 원본, 검색 및 자산은 Azure 부서의 범위 데이터 맵에 있는 경우 컬렉션에 속해야 합니다.    

<!-- 
- Moving data sources across collections is allowed if the user is granted the Data Source Admin role for the source and destination collections. 

- Moving assets across collections is allowed if the user is granted the Data Curator role for the source and destination collections. 

- Certain operations, like delete, move, and rename of a collection, aren't allowed via the Azure Purview Studio graphical interface. You can use the API to perform such operations directly in your Azure Purview data map. 

- You can delete a collection if there are no assets or data sources associated with the collection. You can delete a collection that has a scan associated with it. 
-->

### <a name="design-recommendations"></a>디자인 권장 사항 

- [Azure 부서의 범위 계정 모범 사례](./deployment-best-practices.md#determine-the-number-of-purview-instances) 를 검토 하 고 컬렉션 구조를 계획 하기 전에 조직에 필요한 적절 한 수의 부서의 범위 계정을 정의 합니다.  

- 조직의 보안 요구 사항 및 데이터 관리 및 거 버 넌 스 구조에 따라 컬렉션 아키텍처를 설계 하는 것이 좋습니다. 이 문서의 권장 되는 [컬렉션 archetype](#collections-archetypes) 을 검토 합니다.

- 이후 확장성을 위해 루트 컬렉션 아래에서 조직에 대 한 최상위 컬렉션을 만드는 것이 좋습니다. 루트 컬렉션이 아닌 최상위 컬렉션에서 관련 역할을 할당 합니다.  

- Azure 부서의 범위에서 컬렉션을 빌드할 때 디자인 의사 결정 프로세스의 일환으로 보안 및 액세스 관리를 고려 합니다. 

- 각 컬렉션에는 이름 특성과 이름 특성이 있습니다. Azure 부서의 범위 Studio를 사용 하 여 컬렉션을 배포 하는 경우 시스템은 중복을 방지 하기 위해 컬렉션에 임의의 6 자 이름을 자동으로 할당 합니다. 복잡성을 줄이려면 컬렉션에서 특히 동일한 수준에서 중복 된 이름을 사용 하지 마십시오.  

- 가능 하면 조직 구조를 깊게 중첩 된 컬렉션 계층 구조로 복제 하지 마십시오. 이를 방지할 수 없는 경우 컬렉션을 쉽게 구분할 수 있도록 계층의 모든 컬렉션에 대해 다른 이름을 사용 해야 합니다.

- 컬렉션 및 역할 할당을 대량으로 배포할 계획인 경우 API를 사용 하 여 컬렉션 배포를 자동화 합니다.

- 전용 SPN (서비스 사용자 이름)을 사용 하 여 API를 통해 컬렉션과 역할 할당에 대 한 작업을 실행 합니다. SPN을 사용하면 상승된 권한이 있는 사용자 수가 줄어들고 최소 권한 지침을 따를 수 있습니다.

## <a name="define-an-authorization-model"></a>권한 부여 모델 정의

Azure 부서의 범위 데이터 평면 역할은 Azure 부서의 범위에서 관리 됩니다. 부서의 범위 계정을 배포한 후 부서의 범위 계정의 작성자에 게는 루트 컬렉션에서 다음 역할이 자동으로 할당 됩니다. 부서의 범위 Studio 또는 프로그래밍 방식 메서드를 사용 하 여 Azure 부서의 범위에서 역할을 직접 할당 하 고 관리할 수 있습니다.

  - **컬렉션 관리자** 는 부서의 범위 컬렉션 및 세부 정보를 편집 하 고 하위 컬렉션을 추가할 수 있습니다. 또한 관리자 인 컬렉션의 다른 부서의 범위 역할에 사용자를 추가할 수 있습니다.
  - 데이터 **원본 관리자** 는 데이터 원본 및 데이터 검색을 관리할 수 있습니다.
  - **데이터** 는 카탈로그 데이터 자산을 생성, 읽기, 수정 및 삭제 하 고 자산 간의 관계를 설정할 수 있습니다.
  - **데이터 판독기** 는 카탈로그 데이터 자산에 액세스할 수 있지만 수정할 수는 없습니다.

### <a name="design-considerations"></a>설계 고려 사항  

- Azure 부서의 범위 access management가 데이터 평면으로 이동 되었습니다. Azure Resource Manager 역할은 더 이상 사용 되지 않으므로 Azure 부서의 범위를 사용 하 여 역할을 할당 해야 합니다. 

- azure 부서의 범위에서 부서의 범위 계정이 배포 되는 동일한 azure ad 테 넌 트의 사용자, 보안 그룹 및 서비스 주체 (Azure ad) Azure Active Directory에서 역할을 사용자, 보안 그룹 및 서비스 주체 (azure ad)에 할당할 수 있습니다.
  
- 외부 사용자에 게 부서의 범위 역할을 할당 하려면 먼저 B2B 사용자로 Azure AD 테 넌 트에 게스트 계정을 추가 해야 합니다. 

- 기본적으로 컬렉션 관리자는 자산을 읽거나 수정할 수 있는 권한이 없습니다. 하지만 액세스 권한을 상승 하 고 더 많은 역할에 자신을 추가할 수 있습니다.

- 기본적으로 모든 역할 할당은 모든 자식 컬렉션에 의해 자동으로 상속 됩니다. 그러나 루트 컬렉션을 제외 하 고 모든 컬렉션에 대해 **상속 된 권한 제한을** 사용 하도록 설정할 수 있습니다. **상속 된 사용 권한 제한** 컬렉션 관리자 역할을 제외 하 고 모든 부모 컬렉션에서 상속 된 역할을 제거 합니다. 

- Azure Data Factory 연결의 경우: Azure Data Factory에 연결 하려면 루트 컬렉션에 대 한 컬렉션 관리자 여야 합니다.

- 계보에 대 한 Azure Data Factory에 연결 해야 하는 경우 부서의 범위 루트 컬렉션 수준에서 data Factory의 관리 되는 id에 데이터 큐레이터 역할을 부여 합니다. Data Factory를 제작 UI에서 부서의 범위에 연결 하면 Data Factory 이러한 역할 할당을 자동으로 추가 하려고 시도 합니다. 부서의 범위 루트 컬렉션에 대 한 컬렉션 관리자 역할이 있는 경우이 작업은 작동 합니다. 

### <a name="design-recommendations"></a>디자인 권장 사항 

- 부서의 범위 계정 수준 잠금을 방지 하기 위해 Azure 부서의 범위 루트 컬렉션 수준에서 수집 관리자 역할에 대 한 [비상 액세스](/azure/active-directory/users-groups-roles/directory-emergency-access) 또는 고장 방지 전략을 구현 하는 것이 좋습니다. 응급 계정 사용 프로세스를 문서화 합니다. 

    > [!NOTE]
    > 특정 시나리오에서는 비상 계정을 사용 하 여 Azure 부서의 범위에 로그인 해야 할 수 있습니다. 부서의 범위에 로그인 할 수 없는 경우 또는 다른 관리자가 회사 인증 문제로 인해 특정 작업을 수행할 수 없는 경우 조직 수준 액세스 문제를 해결 하려면이 유형의 계정이 필요할 수 있습니다. 클라우드 전용 사용자를 사용 하 여 [응급 액세스 계정](/azure/active-directory/users-groups-roles/directory-emergency-access) 구현에 대 한 Microsoft 모범 사례를 따르는 것이 좋습니다.
    >
    > 이전 컬렉션 관리자를 사용할 수 없는 경우 [이 문서의](./concept-account-upgrade.md#what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin) 지침에 따라 부서의 범위 루트 컬렉션에 대 한 액세스를 복구 합니다.

- 루트 컬렉션 관리자의 수를 최소화 합니다. SPN 및 사용자의 차단 계정을 포함 하 여 루트 컬렉션에 최대 3 개의 컬렉션 관리자 사용자를 할당 합니다. 대신 컬렉션 관리자 역할을 최상위 컬렉션이 나 하위 컬렉션에 할당 합니다.

- 개별 사용자 대신 그룹에 역할을 할당 하 여 개별 역할 관리의 관리 오버 헤드 및 오류를 줄일 수 있습니다. 

- 자동화를 위해 루트 컬렉션에서 서비스 주체를 할당 합니다.

- 보안을 강화 하려면 최소 수집 관리자, 데이터 원본 관리자 및 데이터에 대 한 다단계 인증을 통해 Azure AD 조건부 액세스를 사용 하도록 설정 합니다. 응급 계정이 조건부 액세스 정책에서 제외 되는지 확인 합니다.
 
## <a name="collections-archetypes"></a>컬렉션 아키타입

중앙 집중식, 탈중앙화 또는 하이브리드 데이터 관리 및 거버넌스 모델을 기반으로 Azure Purview 컬렉션을 배포할 수 있습니다. 이 결정은 비즈니스 및 보안 요구 사항에 따라 결정됩니다.

### <a name="example-1-single-region-organization"></a>예제 1: 단일 지역 조직 

이 구조는 다음과 같은 조직에 적합합니다. 
- 주로 단일 지리적 위치에 기반합니다. 
- 데이터 관리의 다음 수준이 부서, 팀 또는 프로젝트에 속하는 중앙 집중식 데이터 관리 및 거버넌스 팀이 있어야 합니다.  

컬렉션 계층 구조는 다음과 같은 세로로 구성됩니다. 

- 루트 컬렉션(기본값)
- Contoso(최상위 컬렉션)
- 부서(각 부서에 대한 위임된 컬렉션) 
- Teams 또는 프로젝트(프로젝트에 따라 추가 분리)

각 데이터 원본은 해당 컬렉션에서 등록 및 검사됩니다. 따라서 자산도 동일한 컬렉션에 표시됩니다. 

조직 수준 공유 데이터 원본은 Hub-Shared 컬렉션에서 등록 및 검사됩니다. 

부서 수준 공유 데이터 원본은 부서 컬렉션에서 등록 및 검사됩니다. 

:::image type="content" source="media/concept-best-practices/collections-example-1.png" alt-text="첫 번째 Azure Purview 컬렉션 예제를 보여 주는 스크린샷."lightbox="media/concept-best-practices/collections-example-1.png":::

### <a name="example-2-multiregion-organization"></a>예제 2: 다중 구성

이 시나리오는 조직에 유용합니다. 
- 여러 지역에 존재합니다. 
- 데이터 거버넌스 팀이 각 지역에서 중앙 집중화되거나 분산되어 있는 위치입니다.
- 데이터 관리 팀이 각 지리적 위치에 분산되는 위치입니다. 

컬렉션 계층 구조는 다음과 같은 세로로 구성됩니다. 

- 루트 컬렉션(기본값)
- FourthCoffee(최상위 컬렉션)
- 지리적 위치(데이터 원본 및 데이터 소유자가 있는 지리적 위치에 따라 중간 수준 컬렉션)
- 부서(각 부서에 대한 위임된 컬렉션) 
- Teams 또는 프로젝트(팀 또는 프로젝트에 따라 추가 분리)

이 시나리오에서 각 지역에는 Purview 계정의 최상위 컬렉션 아래에 자체 하위 컬렉션이 있습니다. 데이터 원본은 자체 지리적 위치의 해당 하위 데이터 정렬에서 등록 및 검사됩니다. 따라서 자산은 지역의 하위 데이터 정렬 계층에도 표시됩니다. 

중앙 집중식 데이터 관리 및 거버넌스 팀이 있는 경우 최상위 컬렉션에서 액세스 권한을 부여할 수 있습니다. 이렇게 하면 데이터 맵의 전체 데이터 자산에 대한 감독을 얻습니다. 필요에 따라 중앙 집중식 팀은 모든 공유 데이터 원본을 등록하고 검색할 수 있습니다.

지역 기반 데이터 관리 및 거버넌스 팀은 하위 수준에서 해당 컬렉션에서 액세스 권한을 얻을 수 있습니다.

부서 수준 공유 데이터 원본은 부서 컬렉션에서 등록 및 검사됩니다. 

:::image type="content" source="media/concept-best-practices/collections-example-2.png" alt-text="두 번째 Azure Purview 컬렉션 예제를 보여 주는 스크린샷."lightbox="media/concept-best-practices/collections-example-2.png":::

### <a name="example-3-multiregion-data-transformation"></a>예제 3: 다중region, 데이터 변환

이 시나리오는 지리적 위치 및 데이터 변환 상태에 따라 메타데이터 액세스 관리를 배포하려는 경우에 유용할 수 있습니다. 데이터를 보다 의미 있게 변환할 수 있는 데이터 과학자 및 데이터 엔지니어는 원시 및 구체화 영역을 관리할 수 있습니다. 그런 다음, 데이터를 Produce 또는 Curated 영역으로 이동할 수 있습니다.  

컬렉션 계층 구조는 다음과 같은 세로로 구성됩니다. 

- 루트 컬렉션(기본값)
- Fabrikam(최상위 컬렉션)
- 지리적 위치(데이터 원본 및 데이터 소유자가 있는 지리적 위치에 따라 중간 수준 컬렉션)
- 데이터 변환 단계(원시, 구체화, 생성/큐레이팅) 

데이터 과학자 및 데이터 엔지니어는 메타데이터를 큐레이팅할 수 있도록 해당 영역에서 데이터 개발자 역할을 가질 수 있습니다. 큐레이팅된 영역에 대한 데이터 읽기 권한자 액세스 권한자는 전체 데이터 사용자 및 비즈니스 사용자에게 부여할 수 있습니다. 

:::image type="content" source="media/concept-best-practices/collections-example-3.png" alt-text="세 번째 Azure Purview 컬렉션 예제를 보여 주는 스크린샷."lightbox="media/concept-best-practices/collections-example-3.png":::

### <a name="example-4-multiregion-business-functions"></a>예제 4: 다중 기능, 비즈니스 기능 

이 옵션은 비즈니스 기능에 따라 메타데이터 및 액세스 관리를 구성해야 하는 조직에서 사용할 수 있습니다.

컬렉션 계층 구조는 다음과 같은 세로로 구성됩니다. 

- 루트 컬렉션(기본값)
- AdventureWorks(최상위 컬렉션)
- 지리적 위치(데이터 원본 및 데이터 소유자가 있는 지리적 위치에 따라 중간 수준 컬렉션)
- 주요 비즈니스 기능 또는 클라이언트(함수 또는 클라이언트에 따라 추가 분리)

각 지역에는 Purview 계정의 최상위 컬렉션 아래에 자체 하위 컬렉션이 있습니다. 데이터 원본은 자체 지리적 위치의 해당 하위 데이터 정렬에서 등록 및 검사됩니다. 따라서 자산은 지역의 하위 데이터 정렬 계층 구조에 추가됩니다. 

중앙 집중식 데이터 관리 및 거버넌스 팀이 있는 경우 최상위 컬렉션에서 액세스 권한을 부여할 수 있습니다. 이렇게 하면 데이터 맵의 전체 데이터 자산에 대한 감독을 얻습니다. 필요에 따라 중앙 집중식 팀은 모든 공유 데이터 원본을 등록하고 검색할 수 있습니다.

지역 기반 데이터 관리 및 거버넌스 팀은 하위 수준에서 해당 컬렉션에서 액세스 권한을 얻을 수 있습니다.
각 사업부에는 자체 하위 데이터 정렬이 있습니다.

:::image type="content" source="media/concept-best-practices/collections-example-4.png" alt-text="네 번째 Azure Purview 컬렉션 예제를 보여 주는 스크린샷."lightbox="media/concept-best-practices/collections-example-4.png":::

## <a name="access-management-options"></a>액세스 관리 옵션

전체 조직에서 데이터 민주화를 구현하려면 최상위 컬렉션의 데이터 판독기 역할을 데이터 관리, 거버넌스 및 비즈니스 사용자에게 할당합니다. 하위 데이터 정렬 수준에서 데이터 원본 관리자 및 데이터 관리자 역할을 해당 데이터 관리 및 거버넌스 팀에 할당합니다.

조직에서 메타데이터 검색 및 검색에 대한 액세스를 제한해야 하는 경우 특정 컬렉션 수준에서 데이터 읽기 권한자 및 데이터 변수 역할을 할당합니다. 예를 들어 LATAM 컬렉션이 아닌 미국 컬렉션 수준에서만 데이터를 읽을 수 있도록 미국 직원을 제한할 수 있습니다. 

일부 컬렉션에 대한 몇 가지 예외를 제외하고 총 데이터 민주화가 필요한 경우 Purview 데이터 맵에서 이러한 두 시나리오의 조합을 적용할 수 있습니다. 최상위 컬렉션에서 Purview 역할을 할당하고 상속을 특정 자식 컬렉션으로 제한할 수 있습니다.

최상위 컬렉션에서 중앙 집중식 데이터 보안 및 관리 팀에 컬렉션 관리자 역할을 할당합니다. 하위 수준 컬렉션의 추가 컬렉션 관리를 해당 팀에 위임합니다.

## <a name="next-steps"></a>다음 단계
-  [Purview에서 컬렉션 만들기 및 권한 할당](./quickstart-create-collection.md)
-  [Azure Purview에서 컬렉션 만들기 및 관리](./how-to-create-and-manage-collections.md)
-  [Azure Purview의 액세스 제어](./catalog-permissions.md)
