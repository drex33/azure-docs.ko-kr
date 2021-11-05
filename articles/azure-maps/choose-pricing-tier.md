---
title: Microsoft Azure Maps에 적절한 가격 책정 계층 선택
description: Azure Maps 가격 책정 계층에 대해 자세히 알아봅니다. 어떤 기능을 어떤 계층에서 제공하는지 확인하고 가격 책정 계층을 선택하기 위한 주요 고려 사항을 확인합니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/04/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: a0035fd022945b4d073e40f3114a37356a61ed03
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846485"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps에서 적절한 가격 책정 계층 선택

이제 Azure Maps는 Gen 1과 Gen 2의 두 가지 가격 책정 계층을 제공 합니다. 새 Gen 2 가격 책정 계층에는 Gen 1에 대 한 QPS (초당 쿼리 수) 한도가 증가 하는 모든 Azure Maps 기능이 포함 되어 있으므로 Azure Maps 트랜잭션이 증가 함에 따라 비용 절감 효과를 얻을 수 있습니다. 이 문서의 목적은 요구 사항에 적합한 가격 책정 계층을 선택하도록 도와주는 것입니다.

## <a name="pricing-tier-targeted-customers"></a>가격 책정 계층 대상 고객

Gen 1 및 Gen 2 가격 책정 계층에 대해 더 잘 이해하려면 아래의 **가격 책정 계층 대상 고객** 표를 참조하세요.  자세한 내용은 [Azure Maps 가격 책정](https://azure.microsoft.com/pricing/details/azure-maps/)을 참조하세요. 현재 Azure Maps 고객의 경우 [가격 책정 계층 관리](how-to-manage-pricing-tier.md) 문서에서 gen 1부터 gen 2 가격으로 변경 하는 방법을 배울 수 있습니다.

| 가격 책정 계층  | SKU | 대상 고객|
|---------------|:---:| ------------------|
|**Gen 1**|S0| S0 가격 책정 계층은 개념 증명 개발 및 초기 단계 테스트부터 애플리케이션 프로덕션 및 배포에 이르는 모든 프로덕션 단계에 적용됩니다. 그러나 이 계층은 소규모 개발이나 동시 사용자가 적은 고객 또는 둘 다를 위해 설계되었습니다. S0에는 모든 서비스에 대해 50 QPS 제한이 적용 됩니다.
|         |S1| S1 가격 책정 계층은 대규모 엔터프라이즈 애플리케이션, 중요 업무용 애플리케이션 또는 많은 동시 사용자가 있는 고객을 위한 것입니다. 고급 지리 공간 서비스를 필요로 하는 고객에게도 적합합니다.
| **Gen 2** | 맵/위치 인사이트 | Gen 2 가격 책정은 신규 및 기존 Azure Maps 고객을 위한 것입니다. Gen 2에는 Azure Maps에서 테스트하고 빌드하는 데 사용할 수 있는 무료 월별 트랜잭션 계층이 제공됩니다. Maps 및 Location Insights SKU에는 모든 Azure Maps 기능이 포함되어 있습니다. 이를 통해 Azure Maps 트랜잭션이 증가할 때 비용 절감 효과를 얻을 수 있습니다. 또한 Gen 1 보다 QPS 한도가 높습니다.  
|     |  |

QPS 제한에 대 한 자세한 내용은 [Azure Maps QPS rate 제한](azure-maps-qps-rate-limits.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 보고 변경하는 방법에 대해 자세히 알아보기:

> [!div class="nextstepaction"]
> [가격 책정 계층 관리](how-to-manage-pricing-tier.md)
