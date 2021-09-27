---
title: Azure Data Explorer에 예약 할인이 적용되는 방식 이해
description: Azure Data Explorer 태그 요금제에 예약 할인이 적용되는 방식을 알아봅니다.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 99f3687fc3b43461abb48b85c48cfb15286b262f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557760"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Azure Data Explorer에 예약 할인이 적용되는 방식 이해

Azure Data Explorer 예약된 용량을 구입한 후 예약의 특성 및 수량과 일치하는 Azure Data Explorer 리소스에 예약 할인이 자동으로 적용됩니다. 예약에는 Azure Data Explorer 태그 요금이 포함됩니다. Azure Data Explorer 클러스터를 운영하는 데 사용되는 컴퓨팅, 네트워킹, 스토리지 또는 기타 Azure 리소스는 포함되지 않습니다. 이러한 리소스에 대한 예약은 별도로 구입해야 합니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 “*use-it-or-lose-it(사용 또는 소멸)*” 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Azure Data Explorer 클러스터에 적용되는 예약 할인

예약 할인은 Azure Data Explorer 태그 사용에 시간 단위로 적용됩니다. 1시간 내내 실행되지 않는 Azure Data Explorer 리소스의 경우 예약 특성과 일치하는 다른 Data Explorer 리소스에 예약 할인이 자동으로 적용됩니다. 동시에 실행되는 Azure Data Explorer 리소스에도 할인이 적용될 수 있습니다. 1시간 내내 실행되며 예약 특성과 일치하는 Azure Data Explorer 리소스가 없으면 해당 시간 동안의 예약 할인 혜택 전액이 적용되지 않습니다.

> [!NOTE]
> * 예약된 용량 절감을 최대화하려면 Azure Data Explorer 클러스터에 사용되는 가상 머신에 대한 [예약 용량](../../virtual-machines/prepay-reserved-vm-instances.md)을 구매하는 것이 **가장 좋습니다**.
> * 예약 구매 시 모든 지역에 할인이 적용됩니다.

## <a name="examples"></a>예제

다음 예제에서는 구입한 태그 단위 수 및 실행되는 시기에 따라 Azure Data Explorer 예약된 용량 할인이 적용되는 방법을 보여줍니다.
예를 들어 엔진 클러스터 크기: **2개의 D11_v2 VM의** 경우 총 주문형 요금은 시간당 Azure Data Explorer 태그 미터의 4단위입니다.

**시나리오 1**

Azure Data Explorer 태그 단위 8개에 대해 Azure Data Explorer 예약된 용량을 구입합니다. 총 16코어를 사용하는 두 개의 D13_v2 VM의 엔진 클러스터를 실행합니다. 이로 인해 시간당 Azure Data Explorer 태그 단위의 16개 단위에 대한 요금이 청구되고 예약의 나머지 특성과 일치합니다. 8코어 Azure Data Explorer 컴퓨팅 사용량에 대한 종량제 가격이 청구되고, 8코어 Azure Data Explorer 태그 단위의 1시간 사용에 대해서는 예약 할인이 제공됩니다.

이러한 예제의 나머지 부분에서는 구매한 Azure Data Explorer 예약된 용량이 16코어 Azure Data Explorer 클러스터에 대한 것이며, 나머지 예약 특성은 실행 중인 Azure Data Explorer 클러스터와 일치하는 것으로 가정합니다.

**시나리오 2**

서로 다른 두 지역에서 1시간 동안 8코어를 사용하여 두 개의 Azure Data Explorer 엔진 클러스터를 실행합니다. 사용되는 Azure Data Explorer 태그 단위의 16개 단위와 클러스터 모두에 16코어 예약 할인이 적용됩니다.

**시나리오 3**

오후 1시부터 오후 1시 30분까지 하나의 16코어 Azure Data Explorer 엔진 클러스터를 실행합니다. 오후 1시 30분부터 오후 2시까지는 다른 16코어 Azure Data Explorer 엔진 클러스터를 실행합니다. 둘 다 예약 할인에 포함됩니다.

**시나리오 4**

오후 1시부터 오후 1시 45분까지 하나의 16코어 Azure Data Explorer 엔진 클러스터를 실행합니다. 오후 1시 30분부터 오후 2시까지는 다른 16코어 Azure Data Explorer 엔진 클러스터를 실행합니다. 중복되는 15분에 대해서는 종량제 가격으로 요금이 청구됩니다. 나머지 시간에 대한 Azure Data Explorer 태그 사용량에는 예약 할인이 적용됩니다.

청구 사용량 보고서에서 Azure Reservations의 적용을 이해하고 보려면 [Azure 예약 사용량 이해를](understand-reserved-instance-usage-ea.md)참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Data Explorer 예약된 용량을 사용한 Azure Data Explorer 컴퓨팅 리소스에 대한 선불](/azure/data-explorer/pricing-reserved-capacity)  
* [Azure 예약이란?](save-compute-costs-reservations.md)  
* [Azure 예약 관리](manage-reserved-vm-instance.md)  
* [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
* [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
* [CSP 구독의 예약 사용량 이해](/partner-center/azure-reservations)