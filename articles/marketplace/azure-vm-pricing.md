---
title: Azure Marketplace 월별 청구 가격 구성
description: VM 가격을 구성하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshah
ms.author: iqshah
ms.date: 09/28/2021
ms.openlocfilehash: 38eecfefab8bbb8690b8125ad034ca3c3b076a1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076531"
---
# <a name="configure-prices-for-usage-based-monthly-billing"></a>사용량 기반 월별 청구 가격 구성

사용량 기반 월간 청구 플랜은 고객에게 시간별 사용량에 대한 요금을 청구하고 매월 청구됩니다. 고객이 사용한 시간에 대해서만 요금이 청구되는 "종량제" 플랜입니다.
이 플랜을 선택하는 경우 다음 가격 책정 옵션 중 하나를 선택합니다.

- **무료** – VM 제안은 무료입니다.
- **고정 요금(권장)** – VM 제안이 실행되는 하드웨어에 관계없이 동일한 가격입니다.
- **코어당** – VM 제품 가격은 CPU 코어 수당입니다(하나의 CPU 코어에 대한 가격을 제공하고 하드웨어 크기에 따라 가격 책정을 증분함).
- **코어 크기당** – 배포된 하드웨어의 CPU 코어 수에 따라 가격을 할당합니다.
- **시장 및 코어 크기별** – 배포된 하드웨어의 CPU 코어 수 및 모든 시장에 대해 가격을 할당합니다(통화 변환은 게시자가 수행합니다. 가져오기 가격 책정 기능을 사용하는 경우 이 옵션이 더 쉽습니다).

가격 책정 옵션을 선택할 때 고려해야 할 몇 가지 사항:

- 처음 네 가지 옵션에서 Microsoft는 통화 변환을 수행합니다.
- Microsoft는 소프트웨어 솔루션에 대해 고정 요금 가격 책정을 사용하는 것이 좋습니다.
- 가격은 고정되므로 게시된 후에는 조정할 수 없습니다. 그러나 VM 제안에 대한 가격을 줄이려면 [지원 티켓을](./support.md)열 수 있습니다.

## <a name="new-offering-pricing"></a>새 제품 가격 책정

Microsoft Azure 정기적으로 새 VM 인프라를 추가합니다. 경우에 따라 이전에 제공되지 않은 CPU 수가 있는 머신을 추가합니다. Microsoft는 이전 가격 책정에 따라 새 코어 크기의 가격을 결정하고 제안된 가격으로 추가합니다.

새 코어 크기에 대한 가격이 설정되면 게시자는 이메일을 받게 되며, 필요에 따라 검토하고 조정할 시간이 있습니다. 최종 기한이 지나면 Microsoft는 새로 추가된 코어 크기에 대한 가격을 게시합니다.

게시자가 무료, 플랫 또는 코어당 크기를 선택한 경우 게시자는 새 코어 크기에 대한 제안 가격을 책정하는 방법에 필요한 세부 정보를 이미 제공했으며 추가 조치가 필요하지 않습니다. 그러나 게시자가 이전에 코어당 크기 또는 시장별 및 코어 크기를 선택한 경우 업데이트된 가격 정보를 Microsoft에 문의해야 합니다.

## <a name="next-steps"></a>다음 단계

- 질문이 있는 경우 [지원으로](./support.md)티켓을 엽니다.