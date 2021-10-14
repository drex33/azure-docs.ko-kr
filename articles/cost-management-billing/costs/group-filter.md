---
title: Azure Cost Management의 그룹 및 필터 옵션
description: 이 문서에서는 Cost Management 그룹 및 필터 옵션을 사용하는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 10/12/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 150a79c00003d390f75eb32bc386e826702a173b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997141"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>비용 분석의 그룹 및 필터 옵션

비용 분석에는 많은 그룹화 및 필터링 옵션이 있습니다. 이 문서는 사용 시기를 이해하는 데 도움을 줍니다.

그룹화 및 필터링 옵션에 대한 비디오를 시청하려면 [차원 및 태그별 Cost Management 보고](https://www.youtube.com/watch?v=2Vx7V17zbmk) 비디오를 시청하세요. 다른 비디오를 시청하려면 [Cost Management YouTube 채널](https://www.youtube.com/c/AzureCostManagement)을 방문하세요.

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>그룹 및 필터 속성

다음 표에는 비용 분석에서 사용할 수 있는 가장 일반적인 그룹화 및 필터링 옵션과 사용해야 하는 시기가 나와 있습니다.

| 속성 | 사용 시기 | 메모 |
| --- | --- | --- |
| **가용성 영역** | AWS 비용을 가용성 영역별로 분류합니다. | AWS 범위 및 관리 그룹에만 적용됩니다. Azure 데이터에는 가용성 영역이 포함되지 않으며 **가용성 영역 없음** 으로 표시됩니다. |
| **청구 기간** | PAYG 비용을 청구서가 발부되었거나 발부될 월별로 분류합니다. | **청구 기간을** 사용하여 청구서가 발부된 PAYG 요금의 정확한 표현을 얻을 수 있습니다. 사용자 지정 날짜 범위로 필터링하는 경우 청구 기간 전후 2일을 추가로 포함합니다. 정확한 청구 기간 날짜로 제한하면 청구서와 일치하지 않습니다. 청구 기간의 모든 청구서에 대한 비용을 표시합니다. **청구서 ID** 를 사용하여 특정 청구서로 필터링합니다. EA 및 MCA는 월별로 청구되므로 PAYG 구독에만 적용됩니다. EA/MCA 계정은 동일한 목표를 달성하기 위해 날짜 선택 또는 월간 세분성에서 달력 월을 사용할 수 있습니다. |
| **요금 유형** | 사용량, 구매, 환불 및 사용되지 않은 예약 비용을 분류합니다. | 예약 구매 및 환불은 실제 비용을 사용하는 경우에만 사용할 수 있습니다(분할 상환 비용을 사용하는 경우에는 사용할 수 없음). 사용되지 않은 예약 비용은 분할 상환 비용을 확인하는 경우에만 사용할 수 있습니다. |
| **부서** | 비용을 EA 부서별로 분류합니다. | EA 및 관리 그룹에 대해서만 사용할 수 있습니다. PAYG 구독에는 부서가 없으며 **부서 없음** 또는 **할당되지 않음** 으로 표시됩니다. |
| **등록 계정** | EA 계정 소유자별 비용을 분류합니다. | EA 청구 계정, 부서 및 관리 그룹에 대해서만 사용할 수 있습니다. PAYG 구독은 EA 등록 계정을 사용하지 않으며, **등록 계정 없음** 또는 **할당되지 않음** 으로 표시됩니다. |
| **빈도** | 사용량 기반, 일회성 및 반복 비용을 분류합니다. | |
| **청구서 ID** | 비용을 청구된 청구서별로 분류합니다. | 청구되지 않은 요금은 아직 청구서 ID를 사용하지 않고, EA 비용은 청구서 세부 정보를 포함하지 않으며 **청구서 ID 없음** 으로 표시됩니다.  |
| **위치** | 비용을 리소스 위치 또는 지역별로 분류합니다. | 구매 및 Marketplace 사용량은 **할당되지 않음** 또는 **리소스 위치 없음** 으로 표시될 수 있습니다. |
| **미터** | 비용을 사용량 미터별로 분류합니다. | 구매 및 Marketplace 사용량은 **미터 없음** 으로 표시됩니다. **요금 유형** 을 참조하여 구매를 식별하고, **게시자 유형** 을 참조하여 Marketplace 요금을 식별합니다. |
| **연산** | AWS 비용을 작업별로 분류합니다. | AWS 범위 및 관리 그룹에만 적용됩니다. Azure 데이터에는 작업이 포함되지 않으며 **작업 없음** 으로 표시됩니다. 대신 **미터** 를 사용합니다. |
| **가격 책정 모델** | 요청 시, 예약 또는 스폿 사용량별로 비용을 분류합니다. | 구매는 **OnDemand** 로 표시됩니다. **해당 없음** 이 표시되면 **예약** 을 기준으로 그룹화하여 예약 사용량 또는 요청 시 사용량인지 확인하고 **요금 유형** 을 통해 구매를 식별합니다.
| **공급자** | Azure, Microsoft 365, Dynamics 365, AWS 등 공급자 유형별로 비용을 분석합니다. | 제품 및 사업장의 식별자입니다. |
| **게시자 유형** | Microsoft, Azure, AWS 및 Marketplace 비용을 세분화합니다. | 값은 MCA 계정에 대한 **Microsoft,** EA 및 종량제 계정에 대한 **Azure입니다.** |
| **예약** | 예약별 비용을 분류합니다. | 예약과 연결되지 않은 사용량 또는 구매는 **예약 없음** 으로 표시됩니다. **게시자 유형** 을 기준으로 그룹화하여 다른 Azure, AWS 또는 Marketplace 구매를 식별합니다. |
| **리소스** | 리소스별 비용을 분류합니다. | Marketplace 구매는 **기타 Marketplace 구매** 로 표시되고 예약 및 지원 요금과 같은 Azure 구매는 **기타 Azure 구매** 로 표시됩니다. **게시자 유형** 을 기준으로 그룹화 또는 필터링하여 다른 Azure, AWS 또는 Marketplace 구매를 식별합니다. |
| **리소스 그룹** | 리소스 그룹별 비용을 분류합니다. | 구매, 구독과 연결되지 않은 테넌트 리소스, 리소스 그룹에 배포되지 않은 구독 리소스 및 클래식 리소스에는 리소스 그룹이 없으며 **기타 Marketplace 구매**, **기타 Azure 구매**, **기타 테넌트 리소스**, **기타 구독 리소스**, **$system** 또는 **기타 요금** 으로 표시됩니다. |
| **리소스 종류** | 리소스 종류별 비용을 분류합니다. | 구매 및 클래식 서비스는 Azure Resource Manager 리소스 종류를 사용하지 않으며 **기타**, **클래식 서비스** 또는 **리소스 종류 없음** 으로 표시됩니다. |
| **서비스 이름** 또는 **미터 범주** | Azure 서비스별 비용을 분류합니다. | 구매 및 Marketplace 사용량은 **서비스 이름 없음** 또는 **할당되지 않음** 으로 표시됩니다. |
| **서비스 계층** 또는 **미터 하위 범주** | Azure 사용량 미터 하위 범주별 비용을 분류합니다. | 구매 및 Marketplace 사용량은 비어 있거나 **할당되지 않음** 으로 표시됩니다. |
| **구독** | 비용을 Azure 구독 및 AWS 연결된 계정별로 분류합니다. | 구매 및 테넌트 리소스는 **구독 없음** 으로 표시될 수 있습니다. |
| **Tag** | 특정 태그 키에 대한 태그 값별 비용을 분류합니다. | 구매, 구독과 연결되지 않은 테넌트 리소스, 리소스 그룹에 배포되지 않은 구독 리소스 및 클래식 리소스는 태그를 지정할 수 없으며 **태그를 사용할 수 없음** 으로 표시됩니다. 사용량 데이터에 태그를 포함하지 않는 서비스는 **태그가 지원되지 않음** 으로 표시됩니다. 리소스에 태그가 지정되지 않은 나머지 경우는 **태그가 지정되지 않음** 으로 표시됩니다. [각 리소스 종류에 대한 태그 지원](../../azure-resource-manager/management/tag-support.md)에 대해 자세히 알아보세요. |

용어에 대한 자세한 내용은 [Azure 사용량 및 요금 파일에 사용되는 용어 이해](../understand/understand-usage.md)를 참조하세요.

## <a name="publisher-type-value-changes"></a>Publisher 형식 값 변경

Cost Management PublisherType 필드는 Microsoft, Marketplace 또는 [AWS(클라우드 간 커넥터가](aws-integration-set-up-configure.md) 구성된 경우) 제품에 대한 요금인지 여부를 나타냅니다.

변경 내용은?

2021년 10월 14일부터 값이 "Azure"인 PublisherType 필드가 [Microsoft 고객 계약](../understand/review-customer-agreement-bill.md#check-access-to-a-microsoft-customer-agreement)있는 모든 고객에 대해 "Microsoft"로 업데이트됩니다. 이 변경은 Microsoft 365 및 Dynamics 365와 같은 Azure 이외의 Microsoft 제품을 지원하기 위해 예정된 향상된 기능을 수용하기 위해 적용됩니다.

"Marketplace" 및 "AWS"의 값은 변경되지 않습니다.

이 변경 내용은 기업계약 또는 종량제 제안이 있는 고객에게는 영향을 미치지 않습니다.

**영향 및 동작**
<a name="impact-action"></a>

2021 년 10 월 14 일 이전에 다운로드 한 모든 Cost Management 데이터의 경우 이전 "Azure" 및 새 "Microsoft" PublisherType 필드 값을 고려해 야 합니다. 데이터는 내보내기, 사용 세부 정보 또는 Cost Management에서 다운로드할 수 있습니다.

PublisherType 필드를 "Azure" 값으로 필터링 하는 Cost Management + 청구 REST API 호출을 사용 하는 경우 2021 10 월 14 일 이후 변경 내용을 처리 하 고 새 값 "Microsoft"로 필터링 해야 합니다. 그런 다음 Publisher type = "Azure"에 대 한 필터를 사용 하 여 API 호출을 수행 하는 경우 데이터가 반환 되지 않습니다.

변경 내용이 필터에 자동으로 반영 되기 때문에 비용 분석 또는 예산에는 영향을 주지 않습니다. Publisher Type = "Azure" 필터를 사용 하 여 만든 모든 저장 된 보기 또는 예산이 자동으로 업데이트 됩니다.

## <a name="next-steps"></a>다음 단계

- [비용 분석 시작](./quick-acm-cost-analysis.md).
