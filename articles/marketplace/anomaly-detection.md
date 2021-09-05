---
title: 파트너 센터에서 요금제 청구 변칙 관리 | Azure Marketplace
description: 요금제 청구에 대한 자동 변칙 검색을 통해 상업용 Marketplace 제품의 사용 요금이 고객에게 올바르게 청구되도록 하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 5/03/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1226a66a68c9ee8163e1a786cba8f1107c84c2b1
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414632"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>파트너 센터에서 요금제 청구 변칙 관리

사용자 지정 요금제 청구 옵션은 현재 [SaaS(Software as a service)](plan-saas-offer.md) 제품 및 [Azure 애플리케이션](plan-azure-application-offer.md#types-of-plans)(관리형 애플리케이션 요금제)에 사용할 수 있습니다.

비 표준 단위를 기준으로 사용량에 대한 요금을 청구할 수 있는 상업용 Marketplace 프로그램 제품을 만들 수 있는 요금제 청구 옵션을 사용하는 경우 고객이 언제 예상보다 많이 서비스를 사용했는지 알아야 합니다.

## <a name="use-the-anomaly-detection-feature"></a>변칙 검색 기능 사용

Microsoft는 파트너가 보고하는 고객의 SaaS 또는 Azure 관리형 애플리케이션의 초과분 사용량에 따라 고객에게 인보이스를 발부합니다. 잘못된 사용량이 보고되는 경우 고객이 잘못된 청구서를 수신할 수 있으므로 Microsoft와 파트너 모두 신뢰도가 훼손될 수 있습니다.

고객에게 정확한 요금을 청구할 수 있도록 SaaS 앱 및 Azure 애플리케이션 관리형 앱 요금제 모두에 **변칙 검색** 기능을 사용합니다. 이 기능은 요금제 청구 사용량을 모니터링하고 예상 범위 내에서 사용량을 예측합니다. 사용량이 예상 범위를 벗어나면 예기치 않은 것(변칙)으로 처리되며 파트너 센터의 상업용 Marketplace 프로그램의 제품 개요 페이지에서 경고 알림을 받게 됩니다. 설정한 모든 사용자 지정 미터 차원에서 고객의 사용량을 매일 추적할 수 있습니다.

## <a name="view-and-manage-metered-usage-anomalies"></a>요금제 사용량 변칙 보기 및 관리

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **분석** > **사용량** 을 선택합니다.
1. **요금제 사용량 변칙** 탭을 선택합니다.

    [![사용량 페이지의 요금제 사용량 변칙 탭을 보여 줍니다.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)<br>
    ***그림 1: 요금제 사용량 변칙 탭***

1. 요금제 청구에 대해 사용량 변칙이 검색된 경우 파트너는 게시자로서 해당 변칙이 정상인지 여부를 조사하고 확인해야 합니다. 진단을 확인하려면 **변칙으로 표시** 를 선택합니다.

     [![변칙으로 표시 대화 상자를 보여 줍니다.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)<br>
    ***그림 2: 변칙으로 표시 대화 상자***

1. 검색된 초과 사용 변칙이 정상이 아닌 것으로 판단되는 경우 파트너 센터 플래그가 변칙으로 표시한 특정 초과 사용에 대해 **변칙 아님** 을 선택하여 해당 피드백을 제공할 수 있습니다.

    [![변칙이 아닌 이유 대화 상자를 보여 줍니다.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***그림 3: 변칙이 아닌 이유는 무엇 인가요? 대화 상자***

1. 페이지 아래쪽으로 스크롤하여 확인되지 않은 변칙의 인벤토리 목록을 볼 수 있습니다. 이 목록에는 파트너가 확인하지 않은 변칙의 인벤토리가 제공됩니다. 파트너 센터 플래그가 지정된 변칙을 정상 또는 오류로 표시할 수 있습니다.

   [![사용량 페이지의 파트너 센터에 의해 확인되지 않은 변칙 목록을 보여 줍니다.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)<br>
    ***그림 4: 파트너 센터 에 의해 확인되지 않은 변칙 목록***

    기본적으로 예상 재무 영향이 100달러(미화)를 초과하는 플래그가 지정된 변칙은 파트너 센터에 표시됩니다. 그러나 **변칙의 예상 재무 영향** 목록에서 **모두** 를 선택하여 플래그가 지정된 모든 변칙을 확인할 수 있습니다.

    :::image type="content" source="./media/anomaly-detection/all-anomalies.png" alt-text="선택한 제품에 대한 모든 요금제 사용량 변칙의 스크린샷.":::

1. 초과 사용에 대해 조치한 작업을 보여 주는 변칙 작업 로그도 표시됩니다. 작업 로그에서 사실 또는 오류로 표시된 초과 사용 이벤트를 확인할 수 있습니다.

   [![사용량 페이지의 변칙 작업 로그를 보여줍니다.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox)<br>
   ***그림 5: 변칙 작업 로그***

1. 파트너 센터 분석은 보고서 내보내기에서 초과 사용 이벤트 수정을 지원하지 않습니다. 파트너 센터에서는 변칙에 대해 수정된 초과 사용을 입력할 수 있으며, 자세한 내용은 조사를 위해 Microsoft 팀에 전달됩니다. 조사 결과에 따라 Microsoft는 초과 청구된 고객에게 적절히 환불 크레딧을 발급합니다. 플래그가 지정된 변칙 중 하나를 선택하는 경우 **변칙으로 표시** 를 선택하여 초과 사용 변칙을 정상으로 표시할 수 있습니다.

   [![변칙으로 표시 대화 상자를 보여줍니다.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox)<br>
   ***그림 6: 변칙으로 표시 대화 상자***

처음에 파트너 센터에서 초과 사용이 불규칙으로 플래그 지정되는 경우 해당 인스턴스에서 변칙을 정상 또는 오류로 표시하도록 30일의 기간이 부여됩니다. 30일 후에는 변칙에 대한 작업을 수행할 수 없습니다.

> [!IMPORTANT]
> 제대로 보고되지 않은 초과 사용 단위는 수정 및 재무 조정 대상이 되지 않습니다.

선택한 계산 기간에 대해 모든 변칙(확인 또는 기타)을 볼 수 있습니다. 다양한 계산 기간은 지난 30일, 지난 60일 및 지난 90일입니다.

> [!IMPORTANT]
> 파트너는 파트너 센터에서 처음 변칙이 보고된 때로부터 30일 이내에 플래그 지정된 변칙에 대해 조치를 취해야 합니다.

위젯의 필터 모달을 사용하면 개별 제품 및 개별 사용자 지정 미터를 선택할 수 있습니다.

초과 사용을 변칙으로 표시하거나 변칙으로 플래그 지정된 모델을 정상이라고 확인하는 경우 선택 항목을 '변칙 아님'으로 변경할 수 없습니다.

> [!IMPORTANT]
> 과다 청구 상황의 경우 초과 사용을 다시 제출할 수 있습니다.

## <a name="see-also"></a>참고 항목
- [상업용 Marketplace 계량 서비스를 사용하여 SaaS 요금제 청구](./partner-center-portal/saas-metered-billing.md)
- [관리형 애플리케이션 요금제 청구](marketplace-metering-service-apis.md)
- [요금제 청구를 위한 변칙 검색 서비스](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
