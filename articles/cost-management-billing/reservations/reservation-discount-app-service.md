---
title: Azure App Service에 대한 예약 할인
description: Azure App Service Premium v3 및 Premium v2 인스턴스 및 격리 스탬프에 예약 할인이 적용되는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: banders
ms.openlocfilehash: 2158a99d12b39891c9e52220fd2b84821b742c58
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446318"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service"></a>Azure App Service에 예약 할인이 적용되는 방법

이 문서에서는 Azure App Service Premium v3 또는 Premium v2 인스턴스 및 격리 스탬프에 예약 할인이 적용되는 방법을 알아봅니다.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Premium v3 인스턴스에 예약 할인이 적용되는 방법

Azure App Service Premium v3 예약 인스턴스를 구입하면, 예약 할인이 예약의 특성 및 수량과 일치하는 App Service 인스턴스에 자동으로 적용됩니다. 예약은 Premium v3 인스턴스의 비용을 포함합니다. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>할인이 Azure App Service에 적용되는 방법 

예약 할인은 *use-it-or-lose-it(사용하지 않으면 사라짐)* 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.
리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간은 소멸됩니다.

### <a name="reservation-discount-for-premium-v3-instances"></a>Premium v3 인스턴스에 대한 예약 할인

Azure 예약 할인은 시간 단위로 실행 중인 Premium v3 인스턴스에 적용됩니다. 구입한 예약은 실행 중인 Premium v3 인스턴스의 사용량과 일치하여 예약 할인을 적용합니다. 전체 시간을 실행하지 못한 Premium v3 인스턴스의 경우 예약은 현재 실행 중인 인스턴스를 비롯하여 예약을 사용하지 않은 다른 인스턴스에서 채워집니다. 시간이 종료되면 해당 시간의 인스턴스에 대한 예약 애플리케이션이 잠깁니다. 인스턴스가 1시간 동안 실행되지 않거나 시간 내 동시 인스턴스가 예약 시간을 채우지 못하는 경우 예약이 해당 시간 동안 미달 사용됩니다. 다음 그래프는 청구 가능한 VM 사용량에 대한 예약 애플리케이션을 보여줍니다. 일러스트레이션은 하나의 예약 구매 및 일치하는 두 개의 VM 인스턴스를 기반으로 합니다.

![청구 가능한 VM 사용량에 대한 예약 애플리케이션을 보여 주는 이미지](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  예약 줄을 초과하는 사용량은 일반 종량제 요금으로 청구됩니다. 예약 선을 초과하지 않는 사용량은 이미 예약 구매의 일부로 지불되었기 때문에 청구되지 않습니다.
2.  시간 1 중에 인스턴스 1은 0.75시간 동안 실행되고 인스턴스 2는 0.5시간 동안 실행됩니다. 시간 1의 총 사용량은 1.25시간입니다. 나머지 0.25시간에 대한 종량제 요금이 청구됩니다.
3.  시간 2 및 시간 3의 경우 두 인스턴스는 1시간 동안 실행되었습니다. 하나의 인스턴스는 예약에 포함되고 다른 하나는 종량제 요금으로 요금이 부과됩니다.
4.  시간 4의 경우 인스턴스 1은 0.5시간 동안 실행되고 인스턴스 2는 1시간 동안 실행됩니다. 인스턴스 1은 예약에 완전히 포함되고 인스턴스 2 중 0.5시간도 포함됩니다. 나머지 0.5시간에 대한 종량제 요금이 청구됩니다.

청구 사용량 보고서에서 Azure 예약 적용 결과를 이해하고 확인하려면 [예약 사용량 이해](understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="how-reservation-discounts-apply-to-isolated-v2-instances"></a>Isolated v2 인스턴스에 예약 할인이 적용되는 방법

Azure App Service Isolated v2 예약 인스턴스를 구입하면, 예약 할인이 예약의 특성 및 수량과 일치하는 App Service 인스턴스에 자동으로 적용됩니다. 예약은 Isolated v2 인스턴스의 비용을 포함합니다.

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>할인이 Azure App Service에 적용되는 방법

예약 할인은 _use-it-or-lose-it(사용하지 않으면 사라짐)_ 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다. 리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간은 소멸됩니다.

### <a name="reservation-discount-for-isolated-v2-instances"></a>Isolated v2 인스턴스에 대한 예약 할인

Azure 예약 할인은 시간 단위로 실행 중인 Isolated v2 인스턴스에 적용됩니다. 구입한 예약은 예약 할인을 적용하기 위해 Isolated v2 인스턴스의 사용량과 일치합니다. 전체 시간을 실행하지 못한 Isolated v2 인스턴스의 경우 예약은 현재 실행 중인 인스턴스를 비롯하여 예약을 사용하지 않은 다른 인스턴스에서 채워집니다. 시간이 종료되면 해당 시간의 인스턴스에 대한 예약 애플리케이션이 잠깁니다. 인스턴스가 1시간 동안 실행되지 않거나 시간 내 동시 인스턴스가 예약 시간을 채우지 못하는 경우 예약이 해당 시간 동안 미달 사용됩니다.

청구 사용량 보고서에서 Azure 예약 적용 결과를 이해하고 확인하려면 [예약 사용량 이해](understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>격리 스탬프에 예약 할인이 적용되는 방법

App Service 격리 인지세 예약 용량을 구입하면, 지역의 인지세에 예약 할인이 자동으로 적용됩니다. 예약 할인은 격리 인지세 미터에서 내보낸 사용량에 적용됩니다. 작업자, 추가 프런트 엔드 및 스탬프와 연결된 기타 리소스는 일반 요금으로 계속 청구됩니다.

### <a name="reservation-discount-application"></a>예약 할인 애플리케이션

App Service 격리 인지세 할인은 시간 단위로 격리 스탬프를 실행하는 데 적용됩니다. 한 시간 동안 배포된 스탬프가 없으면 해당 시간 동안 예약된 용량이 낭비됩니다. 이월되지 않습니다.

구매 후에는 구매한 예약이 특정 지역에서 실행되는 격리 스탬프와 일치합니다. 해당 스탬프를 종료하면 예약 할인은 해당 지역에서 실행 중인 다른 스탬프에 자동으로 적용됩니다. 스탬프가 없으면 예약은 해당 지역에서 생성된 다음 스탬프에 적용됩니다.

스탬프가 한 시간 동안 실행되지 않으면, 같은 시간 동안 같은 지역에 있는 일치하는 다른 스탬프에 예약이 자동으로 적용됩니다.

### <a name="choose-a-stamp-type---windows-or-linux"></a>스탬프 유형 선택 - Windows 또는 Linux

비어 있는 격리 스탬프는 기본적으로 Windows 스탬프 미터를 내보냅니다. 예를 들어 작업자가 배포되지 않은 경우, Windows 작업자가 배포될 때 계속해서 미터를 내보냅니다. Linux 작업자를 배포하는 경우 미터가 Linux 스탬프 미터로 변경됩니다. Linux 및 Windows 작업자가 모두 배포되면 스탬프가 Windows 미터를 내보냅니다.

그 결과, 스탬프 미터는 스탬프 수명 기간 동안 Windows와 Linux 사이에서 변경될 수 있습니다. 한편, 예약은 운영 체제마다 다릅니다. 스탬프에 배포할 작업자를 지원하는 예약을 구매해야 합니다. Windows 전용 스탬프와 혼합 스탬프는 Windows 예약을 사용합니다. Linux 작업자만 있는 스탬프는 Linux 예약을 사용합니다.

Linux 예약을 구매해야 하는 유일한 경우는 스탬프에 Linux 작업자 _만_ 포함하려는 경우입니다.

### <a name="discount-examples"></a>할인 예제

다음 예제에서는 배포에 따라 격리 인지세 예약 인스턴스 할인이 적용되는 방법을 보여줍니다.

- **예제 1**: App Service 격리 스탬프가 없는 지역에서 격리된 예약 스탬프 용량 인스턴스를 하나 구매합니다. 지역에 새 스탬프를 배포하고 이 스탬프에 예약된 요금을 지불합니다.
- **예제 2**: App Service 격리 스탬프가 이미 배포된 지역에서 격리된 예약 스탬프 용량 인스턴스를 하나 구매합니다. 배포된 스탬프에 예약된 요금을 받기 시작합니다.
- **예제 3**: App Service 격리 스탬프가 이미 배포된 지역에서 격리된 예약 스탬프 용량 인스턴스를 하나 구매합니다. 배포된 스탬프에 대해 예약된 요금을 받기 시작합니다. 나중에 스탬프를 삭제하고 새 스탬프를 배포합니다. 새 스탬프에 예약된 요금을 받습니다. 배포된 스탬프가 없는 기간 동안 할인은 이월되지 않습니다.
- **예제 4**: 한 지역에서 격리된 Linux 예약 스탬프 용량 인스턴스를 하나 구입한 다음, 이 지역에 새 스탬프를 배포합니다. 스탬프가 작업자 없이 처음 배포되면 Windows 스탬프 미터를 내보냅니다. 할인을 받지 못합니다. 첫 번째 Linux 작업자가 스탬프를 배포하면 Linux 스탬프 미터를 내보내고 예약 할인이 적용됩니다. Windows 작업자가 나중에 스탬프에 배포되면 스탬프 미터가 Windows로 되돌아갑니다. 격리된 Linux 예약 스탬프 예약에 대해 더 이상 할인을 받지 않습니다.

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](manage-reserved-vm-instance.md)를 참조하세요.
- 비용 절감을 위한 사전 구매 App Service Premium v3 및 격리 스탬프 예약 용량에 대해 자세히 알아보려면 [예약된 용량으로 Azure App Service를 선불로 결제](prepay-app-service.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](manage-reserved-vm-instance.md)
  - [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
