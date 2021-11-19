---
title: 가격 책정 계층 선택
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 가격 책정 계층(또는 SKU)에 대해 알아봅니다. 검색 서비스는 무료, 기본 및 표준 계층으로 프로비전할 수 있습니다. 표준은 다양한 리소스 구성 및 용량 수준으로 사용 가능합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5d3b4c3204bf6ae347fcfbba0bfa56debd74f173
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132811375"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 가격 책정 계층 선택

[검색 서비스를 만드는](search-create-service-portal.md) 과정에서 서비스 수명 동안 고정된 가격 책정 계층(또는 SKU)을 선택해야 합니다. Portal에서 서비스를 만들 때 **가격 책정 계층 선택** 페이지에 계층이 지정됩니다. PowerShell 또는 Azure CLI를 통해 프로비저닝하는 경우 계층은 **`-Sku`** 매개 변수를 통해 지정됩니다.

선택하는 계층에 따라 다음이 결정됩니다.

+ 서비스에서 허용되는 인덱스 및 기타 개체의 최대 수
+ 파티션(실제 스토리지)의 크기와 속도
+ 고정된 월별 비용으로 청구 가능하고 용량을 추가하는 경우에는 증분 비용이 청구됩니다.

일부 인스턴스에서는 선택하는 계층에 따라 [프리미엄 기능](#premium-features)의 가용성이 결정됩니다.

서비스를 실행하는 가격 또는 월별 예상 비용은 Portal의 **가격 책정 계층 선택** 페이지에 표시됩니다. 예상 비용에 대해 알아보려면 [서비스 가격 책정](https://azure.microsoft.com/pricing/details/search/)을 확인해야 합니다.

> [!NOTE]
> 'Azure SKU'에 대한 정보를 찾고 있나요? [Azure 가격 책정](https://azure.microsoft.com/pricing/)에서 시작한 후 아래로 스크롤하여 서비스별 가격 페이지에 대한 링크로 이동합니다.

## <a name="tier-descriptions"></a>계층 설명

계층에는 **무료**, **기본**, **표준** 및 **스토리지 최적화** 가 포함됩니다. 표준 및 스토리지 최적화는 여러 구성 및 용량에서 사용할 수 있습니다. Azure Portal의 다음 스크린샷은 사용 가능한 계층을 보여 줍니다(포털 및 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에서 확인할 수 있는 가격 책정은 제외). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="가격 책정 계층 차트" border="true":::

**무료** 는 자습서 및 코드 샘플 실행과 같이 소규모 프로젝트에 대해 제한된 검색 서비스를 만듭니다. 내부적으로 시스템 리소스는 여러 구독자 간에 공유됩니다. 무료 서비스는 확장하거나 중요한 워크로드를 실행할 수 없습니다.

**기본** 및 **표준** 은 가장 일반적으로 사용되는 청구 가능 계층으로, **표준** 은 워크로드를 보다 유연하게 확장할 수 있으므로 기본값입니다. 사용자가 제어하는 전용 리소스를 사용하여 대규모 프로젝트를 배포하고 성능을 최적화하고 용량을 늘릴 수 있습니다.

일부 계층은 특정 유형의 작업을 위해 설계되었습니다. 예를 들어 **S3 HD(표준 3 고밀도)** 는 S3용 '호스팅 모드'입니다. 여기서 기본 하드웨어는 많은 수의 작은 인덱스에 최적화되고 다중 테넌시 시나리오를 위한 것입니다. S3 HD는 S3와 단위당 비용이 동일하지만 하드웨어는 많은 수의 작은 인덱스에서 빠른 파일 읽기에 최적화되어 있습니다.

**스토리지 최적화** 계층은 표준 계층보다 저렴한 TB당 가격으로 더 큰 스토리지 용량을 제공합니다. 기본 단점은 더 높은 쿼리 대기 시간으로, 특정 애플리케이션 요구 사항에 적합한지 검증해야 합니다. 이 계층의 성능 고려 사항에 대해 자세히 알아보려면 [성능 및 최적화 고려 사항](search-performance-optimization.md)을 참조하세요.

[가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/), [Azure Cognitive Search 서비스 한도](search-limits-quotas-capacity.md) 문서 및 서비스를 프로비전할 때 포털 페이지에서 다양한 계층에 대한 자세한 내용을 확인할 수 있습니다.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>계층별 기능 가용성

무료 계층을 포함하여 모든 계층에서 대부분의 기능을 사용할 수 있습니다. 경우에 따라 선택하는 계층이 특정 기능을 구현할 수 있는지 여부에 영향을 줍니다. 다음 표에서는 서비스 계층과 관련된 기능 제약 조건을 설명합니다.

| 기능 | 제한 사항 |
|---------|-------------|
| [인덱서](search-indexer-overview.md) | 인덱서는 S3 HD에서 사용할 수 없습니다.  |
| [AI 보강](cognitive-search-concept-intro.md) | 무료 계층에서 실행되지만 권장하지 않습니다. |
| [아웃바운드(인덱서) 액세스를 위한 관리 또는 신뢰할 수 있는 ID](search-howto-managed-identities-data-sources.md) | 무료 계층에서는 사용할 수 없습니다.|
| [고객 관리형 암호화 키](search-security-manage-encryption-keys.md) | 무료 계층에서는 사용할 수 없습니다. |
| [IP 방화벽 액세스](service-configure-firewall.md) | 무료 계층에서는 사용할 수 없습니다. |
| [프라이빗 엔드포인트(Azure Private Link와 통합)](service-create-private-endpoint.md) | 검색 서비스에 대한 인바운드 연결의 경우 무료 계층에서 사용할 수 없습니다. 인덱서를 사용한 다른 Azure 리소스에 대한 아웃바운드 연결의 경우 무료 또는 S3 HD에서 사용할 수 없습니다. 기술 세트를 사용하는 인덱서의 경우 무료, 기본, S1 또는 S3 HD에서 사용할 수 없습니다.| 
| [가용성 영역](search-performance-optimization.md) | 무료 계층 또는 기본 계층에서 사용할 수 없습니다. |
| [의미 체계 검색(미리 보기)](semantic-search-overview.md) | 무료 계층 또는 기본 계층에서 사용할 수 없습니다. |

리소스 집약적 기능은 충분한 용량을 제공하지 않으면 제대로 작동하지 않을 수 있습니다. 예를 들어 [AI 보강](cognitive-search-concept-intro.md)에는 데이터 세트가 작은 경우를 제외하고 무료 서비스에서는 시간 초과되는 장기 실행 기술이 있습니다.

## <a name="upper-limits"></a>상한

계층은 서비스 자체의 최대 스토리지뿐만 아니라 만들 수 있는 인덱스, 인덱서, 데이터 원본, 기술 세트 및 동의어 맵의 최대 수를 결정합니다. 모든 제한을 완전히 차단하려면 [Azure Cognitive Search 서비스 제한](search-limits-quotas-capacity.md)을 참조하세요. 

## <a name="partition-size-and-speed"></a>파티션 크기 및 속도

계층 가격 책정에는 기본 계층의 2GB부터 스토리지 최적화(L2) 계층의 최대 2TB까지 범위에 해당하는 파티션별 스토리지에 대한 세부 정보가 포함됩니다. 작업 속도, 대기 시간 및 전송 속도와 같은 다른 하드웨어 특성은 게시되지 않지만 특정 솔루션 아키텍처에 맞게 설계된 계층은 이러한 시나리오를 지원하기 위한 기능이 포함된 하드웨어를 기반으로 합니다. 파티션에 대한 자세한 내용은 [용량 예측 및 관리](search-capacity-planning.md) 및 [성능을 위한 크기 조정](search-performance-optimization.md)을 참조하세요.

## <a name="billing-rates"></a>청구 요금

계층마다 청구 요금이 다릅니다. 더 비싼 하드웨어에서 실행되거나 더 많은 비용이 드는 기능을 제공하는 계층일수록 요금이 높습니다. Azure Cognitive Search에 대한 계층당 청구 요금은 [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에서 확인할 수 있습니다.

서비스를 만들면 청구 요금은 24시간 서비스 실행에 대한 '고정 비용'과 용량 추가를 선택할 경우 '증분 비용'의 합계가 됩니다.

검색 서비스는 '파티션'(스토리지) 및 '복제본'(쿼리 엔진 인스턴스) 형식으로 컴퓨팅 리소스가 할당됩니다. 처음에는 각 리소스를 하나씩 사용하여 서비스를 만들며, 청구 요금은 두 리소스를 모두 포함합니다. 그러나 용량을 조정하는 경우 비용이 청구 가능 요금 단위로 증가 또는 감소합니다.

다음 예제에서 이에 대해 설명합니다. 가상의 청구 요금이 월 $100이라고 가정합니다. 하나의 파티션과 하나의 복제본에 대한 초기 용량으로 검색 서비스를 유지하는 경우 월말에 $100를 지불하게 됩니다. 그러나 고가용성을 위해 두 개 이상의 복제본을 추가하는 경우 월별 청구는 $300(첫 번째 복제본-파티션 쌍에 대한 $100, 두 복제본에 대한 $200)로 증가합니다.

이 청구 모델은 검색 서비스에서 사용하는 SU(숫자 *검색 단위)에* 청구 요금을 적용하는 개념을 기반으로 합니다. 모든 서비스가 처음에는 하나의 SU에서 프로비전되지만 더 큰 워크로드를 처리하기 위해 파티션 또는 복제본을 추가하여 SU를 늘릴 수 있습니다. 자세한 내용은 [검색 서비스 비용을 예측하는 방법](search-sku-manage-costs.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 선택하는 가장 좋은 방법은 가장 저렴한 계층으로 시작한 후 경험 및 테스트 결과에 입각해 서비스를 유지할지 더 높은 계층에서 새 서비스를 만들지 결정하는 것입니다. 다음 단계로는 필요한 테스트 수준을 수용할 수 있는 계층에서 검색 서비스를 만든 다음, 비용 및 용량을 예측하는 방법에 대한 다음 지침에서 권장 사항을 확인하는 것이 좋습니다.

+ [검색 서비스 만들기](search-create-service-portal.md)
+ [비용 예측](search-sku-manage-costs.md)
+ [용량 예측](search-sku-manage-costs.md)