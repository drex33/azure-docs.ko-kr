---
title: Azure 청구 결제 방법 삭제
description: Azure 구독에서 사용하는 결제 방법을 삭제하는 방법을 설명합니다.
author: bandersmsft
ms.reviewer: lishepar
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/10/2021
ms.author: banders
ms.openlocfilehash: 6050b98d54cf46bf83168d7d33b9ca44d2bd6eda
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180009"
---
# <a name="delete-an-azure-billing-payment-method"></a>Azure 청구 결제 방법 삭제

이 문서에서는 다양한 유형의 Azure 구독에서 신용 카드와 같은 결제 방법을 삭제하는 데 도움이 되는 지침을 제공합니다. 다음 항목에 대한 결제 방법을 삭제할 수 있습니다.

- MCA(Microsoft 고객 계약)
- MOSP(Microsoft Online Services 프로그램) - 종량제라고도 함

Azure 구독 유형에 관계없이 연결된 결제 방법을 삭제하려면 해당 구독을 취소해야 합니다.

Microsoft 파트너 계약 및 기업계약과 같은 다른 Azure 구독 유형에 대한 결제 방법을 제거하는 것은 지원되지 않습니다.

## <a name="delete-an-mca-payment-method"></a>MCA 결제 방법 삭제

Microsoft 고객 계약 계정을 만든 사용자만 결제 방법을 삭제할 수 있습니다.

Microsoft 고객 계약에 대한 결제 방법을 삭제하려면 다음 단계를 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/ ) 에서 Azure Portal에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 필요한 경우 청구 범위를 선택합니다.
1. 왼쪽 메뉴 목록의 **청구** 아래에서 **청구 프로필** 을 선택합니다.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Azure Portal의 청구 프로필을 보여 주는 스크린샷의 예" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. 청구 프로필 목록에서 결제 방법이 사용되는 프로필을 선택합니다.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="청구 프로필 목록을 보여 주는 이미지의 예" :::
1. 왼쪽 메뉴 목록의 **설정** 아래에서 **결제 방법** 을 선택합니다.
1. 청구 프로필에 대한 결제 방법 페이지의 **신용 카드** 섹션 아래에 결제 방법 표가 표시됩니다. 삭제하려는 신용 카드를 찾고, 줄임표( **…** ), **삭제** 를 차례로 선택합니다.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="신용 카드를 삭제할 위치를 보여 주는 예" :::
1. [결제 방법 삭제] 페이지가 표시됩니다. Azure에서 결제 방법이 사용되는지 확인합니다.
    - 결제 방법이 사용되지 않는 경우 **삭제** 옵션을 사용하도록 설정됩니다. 신용 카드 정보를 삭제하려면 이 옵션을 선택합니다.
    - 결제 방법이 사용되는 경우 바꾸거나 분리해야 합니다. 다음 섹션을 계속 읽습니다. 여기서는 구독에서 사용하는 결제 방법을 **분리** 하는 방법을 설명합니다.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>MCA 청구 프로필에서 사용하는 결제 방법 분리

MCA 청구 프로필에서 결제 방법을 사용하는 경우 다음 예와 비슷한 메시지가 표시됩니다.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Microsoft 고객 계약에서 결제 방법을 사용하고 있음을 보여 주는 이미지의 예" :::

결제 방법을 분리하려면 조건 목록을 충족해야 합니다. 조건이 충족되지 않으면 해당 조건을 충족하는 방법을 설명하는 지침이 표시됩니다. 또한 조건을 확인할 수 있는 위치로 이동하는 링크도 표시됩니다.

모든 조건이 충족되면 청구 프로필에서 결제 방법을 분리할 수 있습니다.

> [!NOTE]
> 기본 결제 방법이 분리되면 청구 프로필이 _비활성_ 상태로 전환됩니다. 이 프로세스에서 삭제된 항목은 복구할 수 없습니다. 청구 프로필이 비활성으로 설정되면 새 Azure 구독에 가입하여 새 리소스를 만들어야 합니다.

#### <a name="to-detach-a-payment-method"></a>결제 방법 분리

1. 결제 방법 삭제 영역에서 **현재 결제 방법 분리** 링크를 선택합니다.
1. 모든 조건이 충족되면 **분리** 를 선택합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.
1. [분리]를 사용할 수 없는 경우 조건 목록이 표시됩니다. 나열된 작업을 수행합니다. 기본 결제 방법 분리 영역에 표시된 링크를 선택합니다. 수행해야 하는 작업을 설명하는 정정 작업의 예는 다음과 같습니다.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="MCA 결제 방법을 분리하는 데 필요한 정정 작업을 보여 주는 예" :::
1. 정정 작업 링크를 선택하면 작업을 수행하는 Azure 페이지로 리디렉션됩니다. 필요한 모든 정정 작업을 수행합니다.
1. 필요한 경우 다른 모든 정정 작업을 완료합니다.
1. **Cost Management + 청구** > **청구 프로필** > **결제 방법** 으로 차례로 다시 이동합니다. **분리** 를 선택합니다. 기본 결제 방법 분리 페이지의 아래쪽에서 **분리** 를 선택합니다.

> [!NOTE]
> - 구독을 취소한 후 구독이 삭제되는 데 최대 90일이 걸릴 수 있습니다.
> - 청구 프로필에 대한 모든 이전 요금이 결제된 후에만 결제 방법을 삭제할 수 있습니다. 활성 청구 기간에 있는 경우 청구 기간이 끝날 때까지 기다린 후에 결제 방법을 삭제해야 합니다. **청구 기간이 끝날 때까지 기다리는 동안 다른 모든 분리 조건이 충족되는지 확인하세요**.

## <a name="delete-a-mosp-payment-method"></a>MOSP 결제 방법 삭제

결제 방법을 삭제하려면 계정 관리자여야 합니다.

MOSP 구독에서 결제 방법을 사용하는 경우 다음 단계를 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/ ) 에서 Azure Portal에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 필요한 경우 청구 범위를 선택합니다.
1. 왼쪽 메뉴 목록의 **청구** 아래에서 **결제 방법** 을 선택합니다.
1. 지불 방법 영역의 지불 방법이 있는 행에서 줄임표 (**...**) 기호를 선택한 다음 **삭제** 를 선택 합니다.
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="MOSP 결제 방법을 분리하는 데 필요한 정정 작업을 보여 주는 예" :::
1. 모든 조건이 충족되면 결제 방법 삭제 영역에서 **삭제** 를 선택합니다. [삭제]를 사용할 수 없는 경우 다음 단계로 계속 진행합니다.
1. 조건 목록이 표시됩니다. 나열된 작업을 수행합니다. 결제 방법 삭제 영역에 표시된 링크를 선택합니다.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="MOSP 구독에서 결제 방법을 사용하고 있음을 보여 주는 이미지의 예" :::
1. 정정 작업 링크를 선택하면 작업을 수행하는 Azure 페이지로 리디렉션됩니다. 필요한 모든 정정 작업을 수행합니다.
1. 필요한 경우 다른 모든 정정 작업을 완료합니다.
1. **Cost Management + 청구** > **청구 프로필** > **결제 방법** 으로 차례로 다시 이동하여 결제 방법을 삭제합니다.

> [!NOTE]
> 구독을 취소한 후 구독이 삭제되는 데 최대 90일이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure 구독을 취소하는 방법에 대한 자세한 내용이 필요한 경우 [Azure 구독 취소](cancel-azure-subscription.md)를 참조하세요.
- 신용 카드를 추가하거나 업데이트하는 방법에 대한 자세한 내용은 [Azure에 대한 신용 카드 추가 또는 업데이트](change-credit-card.md)를 참조하세요.
