---
title: 가용성 및 연속성
titleSuffix: Azure Cognitive Search
description: 검색 서비스를 항상 사용 가능하게 설정하고 기간 중단 또는 치명적인 오류에 대해 복원하는 방법을 알아봅니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: ad59bae5d0d5309dea15effefd29a23e8029cd5b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587711"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Azure Cognitive Search의 가용성 및 비즈니스 연속성

Cognitive Search에서 여러 개의 복제본을 통해 가용성을 달성하는 반면 비즈니스 연속성 및 재해 복구는 여러 검색 서비스를 통해 달성됩니다. 이 문서에서는 가용성 및 연속 작업에 대한 비즈니스 요구 사항을 충족하는 전략을 개발하기 위한 시작 지점으로 사용할 수 있는 지침을 제공합니다.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>고가용성

Cognitive Search 복제본은 인덱스의 복제본입니다. 복제본이 여러 개인 경우 Azure Cognitive Search는 한 복제본에 대해 컴퓨터 다시 부팅 및 유지 관리를 수행할 수 있습니다. 반면 쿼리 실행은 다른 복제본에서 계속됩니다. 복제본 추가에 대한 자세한 내용은 [복제본 및 파티션 추가 또는 축소](search-capacity-planning.md#adjust-capacity)를 참조하세요.

각 개별 검색 서비스에 대해 Microsoft는 다음 조건을 충족하는 구성에 대해 최소 99.9%의 가용성을 보장합니다. 

+ 읽기 전용 작업(쿼리)의 고가용성을 위한 복제본 두 개

+ 읽기/쓰기 작업(쿼리 및 인덱싱)의 고가용성을 위한 복제본 세 개 이상 

무료 계층에 대해서는 SLA가 제공되지 않습니다. 자세한 내용은 [Azure Cognitive Search에 대한 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)를 참조하세요.

<a name="availability-zones"></a>

## <a name="availability-zones"></a>가용성 영역

[가용성 영역](../availability-zones/az-overview.md)은 동일한 지역 안에서 고가용성을 제공하기 위해 지역의 데이터 센터를 개별 실제 위치 그룹으로 구분하는 Azure 플랫폼 기능입니다. Cognitive Search에 대해 가용성 영역을 사용하는 경우 개별 복제본은 영역 할당 단위입니다. 검색 서비스는 한 지역 안에서 실행되며 해당 복제본은 다른 영역에서 실행됩니다.

검색 서비스에 두 개 이상의 복제본을 추가하여 Azure Cognitive Search에서 가용성 영역을 활용할 수 있습니다. 각 복제본은 지역 내의 다른 가용성 영역에 배치됩니다. 가용성 영역보다 많은 복제본이 있는 경우 복제본은 가용성 영역 전체에 최대한 균등하게 분산됩니다. 가용성 영역을 제공하는 지역에서 [검색 서비스를 생성](search-create-service-portal.md)한 다음 [여러 복제본을 사용](search-capacity-planning.md#adjust-capacity)하도록 서비스를 구성하는 것 외에는 사용자가 수행해야 하는 특정 작업이 없습니다.

Azure Cognitive Search는 현재 다음 지역 중 하나에서 만든 표준 계층 이상 검색 서비스에 대한 가용성 영역을 지원합니다.

+ 오스트레일리아 동부(2021년 1월 30일 이후 생성)
+ 브라질 남부(2021년 5월 2일 이후 생성)
+ 캐나다 중부(2021년 1월 30일 이후 생성)
+ 미국 중부(2020년 12월 4일 이후 생성)
+ 미국 동부(2021년 1월 27일 이후 생성)
+ 미국 동부 2(2021년 1월 30일 이후 생성)
+ 프랑스 중부(2020년 10월 23일 이후 생성)
+ 독일 중서부(2021년 5월 3일 이후 생성)
+ 일본 동부(2021년 1월 30일 이후 생성)
+ 북유럽(2021년 1월 28일 이후 생성)
+ 미국 중남부(2021년 4월 30일 이후 생성)
+ 동남 아시아(2021년 1월 31일 이후 생성)
+ 영국 남부(2021년 1월 30일 이후 생성)
+ US Gov 버지니아(2021년 4월 30일 이후 생성)
+ 서유럽(2021년 1월 29일 이후 생성)
+ 미국 서부 2(2021년 1월 30일 이후 생성)

가용성 영역은 [Azure Cognitive Search Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)에 영향을 미치지 않습니다. 그러나 쿼리 고가용성을 위해서는 3개 이상의 복제본이 필요합니다.

## <a name="multiple-services-in-separate-geographic-regions"></a>별도의 지역에 있는 여러 서비스

대부분의 고객이 하나의 서비스만 사용하지만 운영과 관련해서 다음과 같은 사항이 요구될 경우 서비스 중복성이 필요할 수 있습니다.

+ [BCDR(비즈니스 연속성 및 재해 복구)](../best-practices-availability-paired-regions.md)(Cognitive Search는 가동 중단 시 즉각적인 장애 조치(failover)를 제공하지 않습니다.)
+ 전역적으로 배포된 애플리케이션. 쿼리 및 인덱싱 요청이 전 세계에서 오는 경우 호스트 데이터 센터에 가장 가까운 사용자가 더 빠른 성능을 갖습니다. 이러한 사용자와 가까운 지역에 추가 서비스를 만들면 모든 사용자의 성능을 균등화할 수 있습니다.
+ [다중 테넌트 아키텍처](search-modeling-multitenant-saas-applications.md)는 때로 두 개 이상의 서비스를 호출하는 경우도 있습니다.

검색 서비스가 두 개 더 필요한 경우 다른 지역에서 생성하면 연속성 및 복구에 대한 애플리케이션 요구 사항을 충족할 수 있을 뿐만 아니라 글로벌 사용자 기반에 대한 더 빠른 응답 시간을 얻을 수 있습니다.

Azure Cognitive Search 기능은 현재 여러 지역에 걸쳐 검색 인덱스를 자동으로 지리적으로 복제하는 방법을 제공하지 않지만, 이러한 프로세스를 간단히 구현하고 관리하는 데 사용할 수 있는 몇 가지 기술이 있습니다. 이러한 기술은 다음 일부 섹션에 간단히 설명되어 있습니다.

검색 서비스 집합을 지리적으로 분산시키는 이유는 둘 이상의 지역에서 둘 이상의 인덱스를 사용할 수 있게 지원하여, 대기 시간이 최소인 Azure Cognitive Search 서비스로 사용자를 라우팅하기 위해서입니다.

   ![지역별 서비스 크로스탭][1]

여러 서비스를 만들고 데이터 동기화 전략을 설계하여 이 아키텍처를 구현할 수 있습니다. 필요에 따라 라우팅 요청에 Azure Traffic Manager와 같은 리소스를 포함할 수 있습니다. 자세한 내용은 [검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>여러 서비스 간에 데이터를 동기화된 상태로 유지

[Azure Cognitive Search 인덱서](search-indexer-overview.md) 또는 푸시 API([Azure Cognitive Search REST API](/rest/api/searchservice/)라고도 함) 사용 등, 두 가지 옵션으로 둘 이상의 분산된 검색 서비스를 동기화된 상태로 유지할 수 있습니다. 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>옵션 1: 여러 서비스에서 콘텐츠를 업데이트하는 데 인덱서 사용

한 서비스에서 이미 인덱서를 사용하는 경우 두 번째 서비스에 대해 두 번째 인덱서를 구성하여, 같은 데이터 원본 개체를 통해 같은 위치에서 데이터를 풀할 수 있습니다. 각 지역의 서비스마다 고유한 인덱서 및 대상 인덱스가 있으나(검색 인덱스는 공유되지 않으므로 데이터가 중복됨) 각 인덱서는 동일한 데이터 원본을 참조합니다.

아키텍처의 형태를 간략히 시각적으로 표현하면 다음과 같습니다.

   ![분산 인덱서 및 서비스 조합을 포함하는 단일 데이터 원본][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>옵션 2: 여러 서비스에서 콘텐츠 업데이트를 푸시하는 데 REST API 사용

Azure Cognitive Search REST API를 사용하여 [검색 인덱스에서 콘텐츠를 푸시](tutorial-optimize-indexing-push-api.md)하는 경우, 업데이트가 필요할 때마다 모든 검색 서비스에 변경 내용을 푸시하여 여러 검색 서비스를 동기화된 상태로 유지할 수 있습니다. 코드에서 한 검색 서비스에 대한 업데이트는 실패하고, 다른 검색 서비스에 대한 업데이트는 성공하는 사례를 처리해야 합니다.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Azure Traffic Manager를 사용하여 요청 조정

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 사용하면 여러 지역에 위치한 웹 사이트로 요청을 라우팅할 수 있습니다. 그러면 이러한 요청은 여러 검색 서비스를 통해 처리될 수 있습니다. 이 Traffic Manager의 장점 중 하나는 Azure Cognitive Search가 사용 가능한지 조사하고 가동 중지 시간이 발생할 경우 사용자를 대체 검색 서비스로 라우팅할 수 있다는 것입니다. 또한 Azure 웹 사이트를 통해 검색 요청을 라우팅하면 웹 사이트는 작동하지만 Azure Cognitive Search는 작동하지 않을 경우에 Azure Traffic Manager를 사용하여 부하를 분산할 수 있습니다. Traffic Manager를 활용하는 아키텍처 예는 다음과 같습니다.

   ![지역별 서비스 크로스탭(중앙 Traffic Manager 포함)][3]

## <a name="disaster-recovery-and-service-outages"></a>재해 복구 및 서비스 중단

[SLA(Service Level Agreement(서비스 수준 약정))에서](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 언급했듯이 Azure Cognitive Search 서비스 인스턴스가 둘 이상의 복제본으로 구성된 경우 인덱스 쿼리 요청에 대해 높은 수준의 가용성을 보장하고 Azure Cognitive Search 서비스 인스턴스가 3개 이상의 복제본으로 구성된 경우 인덱스 업데이트 요청을 보장합니다. 그러나 재해 복구를 위한 기본 제공 메커니즘은 없습니다. Microsoft에서 제어할 수 없는 치명적인 오류가 발생할 경우 연속 서비스가 필요한 경우 다른 지역에 두 번째 서비스를 프로비전하고 지역 복제 전략을 구현하여 인덱스가 모든 서비스에서 완전히 중복되도록 하는 것이 좋습니다.

[인덱서](search-indexer-overview.md)를 사용하여 인덱스를 채우고 새로 고치는 고객은 동일한 데이터 원본을 활용하여 지역별 인덱서를 통해 재해 복구를 처리할 수 있습니다. 인덱서를 실행하는 서로 다른 지역의 두 서비스는 동일한 데이터 소스에서 인덱싱하여 지리적 중복을 적용할 수 있습니다. 지역 중복 데이터 원본에서 인덱싱하는 경우에는 Azure Cognitive Search 인덱서가 주 복제본에서만 증분 인덱싱을 수행할 수 있습니다(새 문서, 수정된 문서 또는 삭제된 문서에서 업데이트 병합). 장애 조치(failover) 이벤트에서 인덱서를 새로운 주 복제본으로 다시 지정하십시오. 

인덱서를 사용하지 않는 경우, 애플리케이션 코드를 사용하여 개체 및 데이터를 여러 서비스에 동시에 푸시합니다. 자세한 내용은 [여러 서비스 간에 데이터를 동기화된 상태로 유지](#data-sync)를 참조하세요.

## <a name="back-up-and-restore-alternatives"></a>백업 및 복원 대안

Azure Cognitive Search는 기본 데이터 스토리지 솔루션이 아니므로 Microsoft는 셀프 서비스 백업 및 복원에 대한 공식적인 메커니즘을 제공하지 않습니다. 그러나 이 [Azure Cognitive Search .NET 샘플 리포지토리](https://github.com/Azure-Samples/azure-search-dotnet-samples)의 **index-backup-restore** 샘플 코드를 사용하여 인덱스 정의와 스냅샷을 일련의 JSON 파일에 백업하고 필요한 경우 이러한 파일을 사용하여 인덱스를 복원할 수 있습니다. 이 도구는 서비스 계층 간에 인덱스를 이동할 수도 있습니다.

그렇지 않으면 인덱스를 만들고 채우는 데 사용되는 애플리케이션 코드는 인덱스를 실수로 삭제하는 경우에 사실상 복원 옵션으로 사용됩니다. 인덱스를 다시 작성하려면 삭제(있다고 가정)하고, 서비스에서 인덱스를 다시 만들고, 기본 데이터 저장소에서 데이터를 검색하여 다시 로드합니다.

## <a name="next-steps"></a>다음 단계

각각의 가격 책정 계층 및 서비스 제한에 대한 자세한 정보는 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요. 파티션 및 복제본 조합에 대한 자세한 정보는 [용량 계획](search-capacity-planning.md)을 참조하세요.

이 문서에서 설명하는 기법의 성능 및 데모에 대한 내용은 다음 동영상에서 확인하세요.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png