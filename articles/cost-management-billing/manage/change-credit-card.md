---
title: Azure에 대한 신용 카드 변경
description: Azure 구독 지불에 사용하는 신용 카드를 변경하는 방법에 대해 설명합니다.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/27/2021
ms.author: banders
ms.openlocfilehash: 18d8a363a21e8086b3ebcd01c0b32b0ae119a483
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000426"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Azure에 대한 신용 카드 추가 또는 업데이트

이 문서는 신용 카드로 Azure 온라인에 등록한 고객에게 적용됩니다.

Azure Portal에서 기본 결제 방법을 새 신용 카드로 변경하고 신용 카드 세부 정보를 업데이트할 수 있습니다. 이러한 변경을 수행하려면 [계정 관리자](add-change-subscription-administrator.md#whoisaa)이거나 올바른 [MCA 권한](understand-mca-roles.md)이 있어야 합니다.

신용 카드를 삭제하려면 [Azure 청구 결제 방법 삭제](delete-azure-payment-method.md)를 참조하세요.

Microsoft Azure에 지원되는 결제 방법은 신용 카드와 수표/전신 송금입니다. 수표/전신 송금으로 결제하도록 승인하려면 [청구서로 Azure 구독 비용 지불](pay-by-invoice.md)을 참조하세요.

Microsoft 고객 계약을 통해 결제 방법이 청구 프로필과 연결됩니다. [Microsoft 고객 계약에 대한 액세스 확인](#check-the-type-of-your-account) 방법을 알아보세요. MCA가 있는 경우 [Microsoft 고객 계약의 신용 카드 관리](#manage-credit-cards-for-a-microsoft-customer-agreement)로 건너뜁니다.

>[!NOTE]
> 새 구독을 만들 때 새 신용 카드를 지정할 수 있습니다. 그렇게 하면 다른 구독이 새 신용 카드와 연결되지 않습니다. 그러나 나중에 다음 중 하나라도 변경하면 *모든 구독* 에서 선택한 결제 방법이 사용됩니다.
  >- **활성화 설정** 옵션을 사용하여 결제 방법을 활성화합니다.
  >- 모든 구독에 대해 **바꾸기** 결제 옵션을 사용합니다.
  >- 기본 결제 방법 변경

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Azure 구독의 신용 카드 관리

다음 섹션은 Microsoft Online Services 프로그램 청구 계정이 있는 고객에게 적용됩니다. [청구 계정 유형을 확인](#check-the-type-of-your-account)하는 방법을 알아보세요. 청구 계정 유형이 Microsoft Online Services 프로그램인 경우 결제 방법이 개별 Azure 구독과 연결됩니다. 신용 카드를 추가한 후 오류가 표시되면 [Azure 등록 시 신용 카드 거부](./troubleshoot-declined-card.md)를 참조하세요.

### <a name="change-credit-card-for-all-subscriptions-by-adding-a-new-credit-card"></a>새 신용 카드를 추가하여 모든 구독에 대한 신용 카드 변경

Azure 구독의 기본 신용 카드를 새 신용 카드 또는 Azure Portal에서 이전에 저장한 신용 카드로 변경할 수 있습니다. 신용 카드를 변경하려면 계정 관리자여야 합니다. 

여러 구독에 동일한 활성 결제 방법이 있는 경우 이러한 구독 중에서 기본 결제 방법을 변경하면 다른 구독에 대한 활성 결제 방법도 업데이트됩니다.

다음 단계를 수행하여 구독의 기본 신용 카드를 새 신용 카드로 변경할 수 있습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색합니다.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="검색을 보여 주는 스크린샷." lightbox="./media/change-credit-card/search.png" :::
1. 신용 카드를 추가하려는 구독을 선택합니다.
1. **결제 방법** 을 선택합니다.  
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="선택한 결제 방법 관리 옵션을 보여 주는 스크린샷" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. 왼쪽 위 모서리에서 **+ 추가** 를 선택하여 카드를 추가합니다. 오른쪽에 신용 카드 양식이 나타납니다.
1. 신용 카드 세부 정보를 입력합니다.  
    :::image type="content" source="./media/change-credit-card/sub-add-new-default.png" alt-text="새 카드 추가를 보여 주는 스크린샷" lightbox="./media/change-credit-card/sub-add-new-default.png" :::
1. 이 카드를 기본 결제 방법으로 설정하려면 양식 위의 **이 카드를 기본 결제 방법으로 설정** 을 선택합니다. 이 카드는 선택한 구독과 동일한 카드를 사용하는 모든 구독의 활성 결제 방법이 됩니다.
1. **다음** 을 선택합니다.

### <a name="replace-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>이전에 저장한 신용 카드로 구독하기 위한 신용 카드 바꾸기

다음 단계를 수행하여 구독의 기본 신용 카드를 계정에 이미 저장된 신용 카드로 바꿀 수도 있습니다. 이 절차는 다른 모든 구독에 대한 신용 카드를 변경합니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색합니다.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Cost Management + Billing 검색을 보여 주는 스크린샷." lightbox="./media/change-credit-card/search.png" :::
1. 신용 카드를 추가하려는 구독을 선택합니다.
1. **결제 방법** 을 선택합니다.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="결제 방법 관리 옵션을 보여 주는 스크린샷." lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. 현재 신용카드를 선택한 신용카드로 변경하려면 **바꾸기** 를 선택합니다.
    :::image type="content" source="./media/change-credit-card/replace-credit-card.png" alt-text="바꾸기 옵션을 보여 주는 스크린샷." lightbox="./media/change-credit-card/replace-credit-card.png" :::
1. **기본 결제 방법 바꾸기** 에서 기본 신용 카드를 바꿀 다른 신용 카드를 선택한 후 **다음** 을 선택합니다.
    :::image type="content" source="./media/change-credit-card/replace-default-payment-method.png" alt-text="기본 결제 방법 바꾸기 상자를 보여 주는 스크린샷." lightbox="./media/change-credit-card/replace-default-payment-method.png" :::
1. 잠시 후 결제 방법이 변경되었다는 확인 메시지가 표시됩니다.

### <a name="edit-credit-card-details"></a>신용 카드 세부 정보 입력

신용 카드가 갱신되고 번호가 동일하게 유지되는 경우 만료 날짜처럼 기존 신용 카드 세부 정보를 업데이트합니다. 카드 분실, 도난 또는 만료로 인해 카드 번호가 변경되는 경우 [결제 방법으로 신용 카드 추가](#addcard) 섹션의 단계를 따릅니다. CVV를 업데이트할 필요가 없습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색합니다.
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="검색 스크린샷" lightbox="./media/change-credit-card/search.png" :::
1. **결제 방법** 을 선택합니다.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="결제 방법 관리를 보여 주는 스크린샷." lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. 편집하려는 신용 카드를 선택합니다. 오른쪽에 신용 카드 양식이 나타납니다.
    :::image type="content" source="./media/change-credit-card/edit-card-x.png" alt-text="결제 방법 편집을 보여 주는 스크린샷." lightbox="./media/change-credit-card/edit-card-x.png" :::
1. 신용 카드 세부 정보를 업데이트합니다.
1. **다음** 을 선택합니다.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약의 신용 카드 관리

다음 섹션은 Microsoft 고객 계약을 보유하고 있고 신용 카드로 Azure 온라인에 가입한 고객과 올바른 [MCA 권한](understand-mca-roles.md)이 있는 고객에게 적용됩니다. [Microsoft 고객 계약을 보유하고 있는지 확인하는 방법을 알아보세요](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>기본 신용 카드 변경

Microsoft 고객 계약의 경우 신용 카드가 청구 프로필과 연결됩니다. 청구 프로필의 결제 방법을 변경하려면 Azure에 가입하고 청구 계정을 만든 사람이거나 올바른 [MCA 권한](understand-mca-roles.md)이 있어야 합니다.

청구 프로필의 기본 결제 방법을 수표/전신 송금으로 변경하려는 경우 [청구서로 Azure 구독 비용 지불](pay-by-invoice.md)을 참조하세요.

신용 카드를 변경하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 에서 검색합니다.
1. 왼쪽 메뉴에서 **청구 프로필** 을 선택합니다.
1. 청구 프로필을 선택합니다.
1. 왼쪽 메뉴에서 **결제 방법** 을 선택합니다.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="메뉴의 결제 방법을 보여 주는 스크린샷." lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. **기본 결제 방법** 섹션에서 **바꾸기** 를 선택합니다.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="바꾸기를 보여 주는 스크린샷" lightbox="./media/change-credit-card/change-payment-method-mca.png" :::
1. 오른쪽의 새 영역에 있는 드롭다운에서 기존 카드를 선택하거나 파란색 **새 결제 방법 추가** 링크를 선택하여 새 카드를 추가합니다.

### <a name="edit-a-credit-card"></a>신용 카드 편집

Azure Portal에서 신용 카드 세부 정보(예: 만료 날짜 업데이트)를 편집할 수 있습니다. 

신용 카드를 편집하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 에서 검색합니다.
1. 왼쪽 메뉴에서 **청구 프로필** 을 선택합니다.
1. 청구 프로필을 선택합니다.
1. 왼쪽 메뉴에서 **결제 방법** 을 선택합니다.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="메뉴의 결제 방법을 보여 주는 스크린샷." lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. **신용 카드** 섹션에서 편집할 신용 카드를 찾습니다.
1. 행 끝에 있는 줄임표(`...`)를 선택합니다.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="줄임표를 보여 주는 스크린샷." lightbox="./media/change-credit-card/edit-delete-credit-card-mca.png" :::
1. 신용 카드 세부 정보를 편집하려면 바로 가기 메뉴에서 **편집** 을 선택합니다.

## <a name="troubleshooting"></a>문제 해결

Azure는 가상 카드 또는 선불 카드를 지원하지 않습니다. 유효한 신용 카드를 추가하거나 업데이트할 때 오류가 발생하면 프라이빗 모드에서 브라우저를 열어 보세요.

## <a name="frequently-asked-questions"></a>질문과 대답

다음 섹션에서는 신용 카드 정보를 변경하는 방법에 관한 자주 묻는 질문에 대답합니다.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>“로그인 세션이 만료되었습니다. 다시 로그인하려면 여기를 클릭하세요.” 메시지를 계속해서 받는 이유는 무엇입니까?

로그아웃하고 다시 로그인했는데도 이 오류 메시지가 계속 표시되는 경우 프라이빗 브라우징 세션으로 다시 시도해 보세요.

### <a name="how-do-i-use-a-different-card-for-each-subscription"></a>각 구독에 대해 서로 다른 카드를 사용하려면 어떻게 해야 하나요?

이전에 언급했듯이 새 구독을 만들 때 새 신용 카드를 지정할 수 있습니다. 그렇게 하면 다른 구독이 새 신용 카드와 연결되지 않습니다. 각각 고유한 신용 카드를 사용하여 여러 개의 새 구독을 추가할 수 있습니다. 그러나 나중에 다음 중 하나라도 변경하면 *모든 구독* 에서 선택한 결제 방법이 사용됩니다.

- **활성화 설정** 옵션을 사용하여 결제 방법을 활성화합니다.
- 모든 구독에 대해 **바꾸기** 결제 옵션을 사용합니다.
- 기본 결제 방법 변경

### <a name="how-do-i-make-payments"></a>지불하려면 어떻게 해야 할까요?

결제 방법으로 신용 카드를 설정한 경우 각 청구 기간 후에 카드로 자동으로 청구됩니다. 수행할 작업은 없습니다.

[청구서로 지불](pay-by-invoice.md)하는 경우 청구서 아래쪽에 나열된 위치로 결제 금액을 보냅니다.

### <a name="how-do-i-change-the-tax-id"></a>세금 ID를 변경하려면 어떻게 할까요?

세금 ID를 추가 또는 업데이트하려면 [Azure Portal](https://portal.azure.com)에서 프로필을 업데이트한 다음, **세금 레코드** 를 선택합니다. 이 세금 ID는 면세 계산에 사용되며 청구서에 표시됩니다.

## <a name="check-the-type-of-your-account"></a>계정 유형 확인

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- 비용을 절감할 수 있는지 확인하기 위해 [Azure Reservations](../reservations/save-compute-costs-reservations.md)에 대해 알아봅니다.
- 신용 카드를 삭제하려면 [Azure 청구 결제 방법 삭제](delete-azure-payment-method.md)를 참조하세요.