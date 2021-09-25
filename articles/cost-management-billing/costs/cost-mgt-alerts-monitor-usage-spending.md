---
title: 비용 경고를 사용하여 사용량 및 지출 모니터링
description: 이 문서에서는 Azure Cost Management에서 비용 경고를 사용하여 사용량 및 지출을 모니터링하는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 09/15/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 14d8408ecec26ea75bc3628ad1f5e35de6833548
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596166"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>비용 경고를 사용하여 사용량 및 지출 모니터링

이 문서는 Cost Management 경고를 이해하고 사용하여 Azure 사용량 및 지출을 모니터링하는 데 도움이 됩니다. 비용 경고는 Azure 리소스를 사용한 시기에 따라 자동으로 생성됩니다. 경고는 모든 활성 비용 관리 및 청구 경고를 한곳에서 보여 줍니다. 소비가 지정된 임계값에 도달하면 Cost Management에서 경고가 생성됩니다. 예산 경고, 크레딧 경고, 부서 지출 할당량 경고 등 세 가지 유형의 비용 경고가 있습니다.

## <a name="budget-alerts"></a>예산 경고

예산 경고는 사용량 또는 비용을 기준으로 지출이 [예산의 경고 조건](tutorial-acm-create-budgets.md)에 정의된 금액에 도달하거나 초과할 때 알립니다. Cost Management 예산은 Azure Portal 또는 [Azure Consumption](/rest/api/consumption) API를 사용하여 만들어집니다.

Azure Portal에서는 비용을 통해 예산이 정의됩니다. Azure Consumption API를 사용하는 경우 비용 또는 소비 사용량을 통해 예산이 정의됩니다. 예산 경고는 비용 기반 예산과 사용량 기반 예산을 둘 다 지원합니다. 예산 경고 조건이 충족될 때마다 예산 경고가 자동으로 생성됩니다. Azure Portal에서 모든 비용 경고를 볼 수 있습니다. 경고가 생성될 때마다 비용 경고에 표시됩니다. 또한 예산의 경고 수신자 목록에 있는 사람에게 경고 메일이 전송됩니다.

예산 API를 사용하여 다른 언어로 이메일 경고를 보낼 수 있습니다. 자세한 내용은 [예산 경고 이메일이 지원되는 로캘](manage-automation.md#supported-locales-for-budget-alert-emails)을 참조하세요.

## <a name="credit-alerts"></a>크레딧 경고

신용 경고는 Azure 선불(이전에는 현금 약정 금액이라고 함)이 소비되면 사용자에게 알려줍니다. Azure 선불은 기업계약이 있는 조직을 위한 것입니다. 크레딧 경고는 Azure 선불 크레딧 잔액의 90% 및 100%에서 자동으로 생성됩니다. 경고가 생성될 때마다 비용 경고 및 계정 소유자에게 전송되는 메일에 반영됩니다.

## <a name="department-spending-quota-alerts"></a>부서 지출 할당량 경고

부서 지출 할당량 경고는 부서가 할당량의 고정 임계값에 도달할 때 사용자에게 알립니다. 지출 할당량은 EA Portal에서 구성됩니다. 임계값이 충족될 때마다 부서 소유자를 위한 메일이 생성되고 비용 경고에 표시됩니다. 예를 들어 할당량의 50% 또는 75%입니다.

## <a name="supported-alert-features-by-offer-categories"></a>제품 범주별 지원되는 경고 기능

경고 유형 지원은 보유한 Azure 계정(Microsoft 제품) 유형에 따라 달라집니다. 다음 표에서는 다양한 Microsoft 제품에서 지원되는 경고 기능을 보여 줍니다. [Cost Management 데이터 이해](understand-cost-mgt-data.md)에서 Microsoft 제품의 전체 목록을 볼 수 있습니다.

| 경고 유형 | 기업 계약 | Microsoft 고객 계약 | 웹 직접/종량제 |
|---|---|---|---|
| 예산 | ✔ | ✔ | ✔ |
| 크레딧 | ✔ |✘ | ✘ |
| 부서 지출 할당량 | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>비용 경고 보기

비용 경고를 보려면 Azure Portal에서 원하는 범위를 열고 메뉴에서 **예산** 을 선택합니다. **범위** 필을 사용하여 다른 범위로 전환합니다. 메뉴에서 **비용 경고** 를 선택합니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

![Cost Management에 표시된 경고의 예제 이미지](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

활성 및 해제된 경고의 총 개수가 비용 경고 페이지에 표시됩니다.

모든 경고에는 경고 유형이 표시됩니다. 예산 경고에는 생성된 이유와 적용되는 예산의 이름이 표시됩니다. 각 경고는 생성된 날짜, 상태 및 경고가 적용되는 범위(구독 또는 관리 그룹)가 표시됩니다.

가능한 상태는 **활성** 및 **해제됨** 입니다. 활성 상태는 경고가 여전히 관련이 있음을 나타냅니다. 해제 상태는 다른 사람이 경고를 더 이상 관련 없는 것으로 표시했음을 나타냅니다.

목록에서 정보를 보려는 경고를 선택합니다. 경고 정보에는 경고에 대한 자세한 정보가 표시됩니다. 예산 경고에는 예산에 대한 링크가 포함됩니다. 예산 경고에 대한 권장 사항을 사용할 수 있는 경우 권장 사항에 대한 링크도 표시됩니다. 예산, 크레딧 및 부서 지출 할당량 경고에는 경고 범위에 대한 비용을 살펴볼 수 있는 비용 분석에서 분석하는 링크가 있습니다. 다음 예제에서는 경고 세부 정보와 함께 부서의 지출을 보여 줍니다.

![경고 세부 정보와 함께 부서의 지출이 표시된 예제 이미지](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

해제된 경고의 세부 정보를 보는 경우 수동 작업이 필요하면 다시 활성화할 수 있습니다. 다음 이미지에 예가 나와 있습니다.

![해제 및 다시 활성화 옵션이 표시된 예제 이미지](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>추가 정보

- 예산을 만들지 않았거나 예산에 대한 경고 조건을 설정하지 않은 경우 [예산 만들기 및 관리](tutorial-acm-create-budgets.md) 자습서를 완료합니다.