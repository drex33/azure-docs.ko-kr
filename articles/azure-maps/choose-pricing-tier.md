---
title: Microsoft Azure Maps에 적절한 가격 책정 계층 선택
description: Azure Maps 가격 책정 계층에 대해 자세히 알아봅니다. 어떤 기능을 어떤 계층에서 제공하는지 확인하고 가격 책정 계층을 선택하기 위한 주요 고려 사항을 확인합니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ef61b0c8a52e37b5eb73f38ab63cc97e66bf7e4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398918"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps에서 적절한 가격 책정 계층 선택

Azure 지도 이제 1세대와 2세대의 두 가지 가격 책정 계층을 제공합니다. 새 Gen 2 가격 책정 계층에는 Gen 1에 비해 QPS(초당 쿼리 수) 제한이 증가된 모든 Azure 지도 기능이 포함되어 있으며, Azure 지도 트랜잭션이 증가함에 따라 비용을 절감할 수 있습니다. 이 문서의 목적은 요구 사항에 적합한 가격 책정 계층을 선택하도록 도와주는 것입니다.

## <a name="pricing-tier-targeted-customers"></a>가격 책정 계층 대상 고객

Gen 1 및 Gen 2 가격 책정 계층에 대해 더 잘 이해하려면 아래의 **가격 책정 계층 대상 고객** 표를 참조하세요.  자세한 내용은 [Azure Maps 가격 책정](https://aka.ms/CreatorPricing)을 참조하세요. 현재 Azure 지도 고객인 경우 가격 책정 계층 관리 문서에서 Gen 1에서 Gen 2 가격 [책정으로](how-to-manage-pricing-tier.md) 변경하는 방법을 알아볼 수 있습니다.

| 가격 책정 계층  | SKU | 대상 고객|
|---------------|:---:| ------------------|
|**Gen 1**|S0| S0 가격 책정 계층은 개념 증명 개발 및 초기 단계 테스트부터 애플리케이션 프로덕션 및 배포에 이르는 모든 프로덕션 단계에 적용됩니다. 그러나 이 계층은 소규모 개발이나 동시 사용자가 적은 고객 또는 둘 다를 위해 설계되었습니다. S0에는 결합된 모든 서비스에 대해 50 QPS 제한이 있습니다.
|         |S1| S1 가격 책정 계층은 대규모 엔터프라이즈 애플리케이션, 중요 업무용 애플리케이션 또는 많은 동시 사용자가 있는 고객을 위한 것입니다. 고급 지리 공간 서비스를 필요로 하는 고객에게도 적합합니다.
| **Gen 2** | 맵/위치 인사이트 | Gen 2 가격 책정은 신규 및 기존 Azure Maps 고객을 위한 것입니다. Gen 2에는 Azure Maps에서 테스트하고 빌드하는 데 사용할 수 있는 무료 월별 트랜잭션 계층이 제공됩니다. Maps 및 Location Insights SKU에는 모든 Azure Maps 기능이 포함되어 있습니다. Azure 지도 트랜잭션이 증가함에 따라 비용을 절감할 수 있습니다. 또한 1세대보다 QPS 제한이 높습니다. [실내 맵](creator-indoor-maps.md)에 Creator를 사용하는 경우 Gen 2 가격 책정 계층이 필요합니다.
|     |  |

QPS 제한에 대한 자세한 내용은 [Azure 지도 QPS 속도 제한을 참조하세요.](azure-maps-qps-rate-limits.md)

[실내 맵의 Creator에 대한](creator-indoor-maps.md)추가 가격 책정 정보는 [Azure 지도 가격 책정의](https://aka.ms/CreatorPricing) *Creator* 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 보고 변경하는 방법에 대해 자세히 알아보기:

> [!div class="nextstepaction"]
> [가격 책정 계층 관리](how-to-manage-pricing-tier.md)
