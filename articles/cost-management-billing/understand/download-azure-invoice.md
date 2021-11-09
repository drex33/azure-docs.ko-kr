---
title: Azure 청구서 보기 및 다운로드
description: Azure 청구서를 보고 다운로드하는 방법을 알아봅니다. Azure Portal에서 청구서를 다운로드하거나 이메일로 받아볼 수 있습니다.
keywords: 청구서, 청구서 다운로드, azure 청구서, azure 사용 현황
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: banders
ms.openlocfilehash: c3e37fa2d4a24344c2d417bd54544bd786fc8e75
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054260"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Microsoft Azure 청구서 보기 및 다운로드

[Azure Portal](https://portal.azure.com/)에서 청구서를 다운로드하거나 이메일로 전송할 수 있습니다. 기업계약이 체결된 Azure 고객(EA 고객)의 경우 조직의 청구서를 다운로드할 수 없습니다. 대신, 등록에 대한 청구서를 받도록 설정된 사람에게 청구서가 전송됩니다.

## <a name="when-invoices-are-generated"></a>청구서가 생성되는 경우

청구서는 청구 계정 유형에 따라 생성됩니다. MOSP(Microsoft 온라인 서비스 프로그램), MCA(Microsoft 고객 계약) 및 MPA(Microsoft 파트너 계약) 청구 계정에 대한 청구서가 생성됩니다. 또한 EA(기업계약) 청구 계정에 대한 청구서도 생성됩니다. 그러나 EA 청구 계정에 대한 청구서는 Azure Portal에 표시되지 않습니다.

청구 계정에 대해 자세히 알아보고 청구 계정 유형을 확인하려면 [Azure Portal에서 청구 계정 보기](../manage/view-all-accounts.md)를 참조하세요.

### <a name="invoice-status"></a>청구서 상태

Azure Portal에서 청구서 상태를 확인할 때 각 청구서의 상태는 다음 기호 중 하나로 표시됩니다.

|  상태 기호 | Description  |
|---|---|
| ![기한 상태 기호](./media/download-azure-invoice/due.svg) | 청구서가 생성되었지만 아직 결제되지 않은 경우 *기한* 이 표시됩니다. |
| ![기한 초과 상태 기호](./media/download-azure-invoice/past-due.svg)  | Azure에서 지정된 결제 방법으로 수금하려고 했지만 결제가 거절된 경우 *기한 초과* 로 표시됩니다. |
| ![완납 상태 기호](./media/download-azure-invoice/paid.svg)  | Azure에서 지정된 결제 방법으로 수금에 성공하면 *완납* 상태로 표시됩니다. |

Azure Portal에서 청구서가 생성되면 *기한* 상태로 표시됩니다. 기한 상태는 정상이며 예상된 상태입니다.  

청구서가 결제되지 않은 경우에는 *기한 초과* 로 표시됩니다. 청구서가 결제되지 않은 기한 초과 구독은 사용하지 않도록 설정됩니다.

## <a name="invoices-for-mosp-billing-accounts"></a>MOSP 청구 계정의 청구서

MOSP 청구 계정은 Azure 웹 사이트를 통해 Azure에 가입할 때 생성됩니다. 예를 들어 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입하는 경우 [종량제 요금을 사용하는 계정](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) 자격입니다.

Azure 웹 사이트를 통해 [종량제 요금을 사용한 계정](https://azure.microsoft.com/offers/ms-azr-0003p/)이나 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입한 특정 지역의 고객은 MCA에 대한 청구 계정도 있을 수 있습니다.

청구 계정 유형을 잘 모르는 경우 이 문서의 지침을 수행하기 전에 [청구 계정 유형 확인](../manage/view-all-accounts.md#check-the-type-of-your-account)을 참조하세요. 

MOSP 청구 계정에는 다음과 같은 청구서가 있을 수 있습니다.

**Azure 서비스 요금** - 구독에서 사용하는 Azure 리소스를 포함하는 각 Azure 구독에 대한 청구서가 생성됩니다. 청구서에는 청구 기간에 해당하는 요금이 포함됩니다. 청구 기간은 구독이 생성된 날짜로 결정됩니다.

예를 들어 John은 3월 5일에 *Azure sub 01* 을 만들고 3월 10일에 *Azure sub 02* 를 만들었습니다. *Azure sub 01* 청구서에는 매월 5일부터 익월 4일까지의 요금이 청구됩니다. *Azure sub 02* 청구서에는 매월 10일부터 익월 9일까지의 요금이 청구됩니다. 모든 Azure 구독에 대한 청구서는 일반적으로 계정이 만들어진 날에 생성되지만 최대 2일이 더 소요될 수 있습니다. 이 예에서 John이 2월 2일에 계정을 만들었다면 *Azure sub 01* 및 *Azure sub 02* 에 대한 청구서는 일반적으로 매월 2일에 생성되지만 최대 2일이 더 소요될 수 있습니다.

**Azure Marketplace, 예약 및 스폿 VM** - 구독을 사용하여 구매한 예약, 마켓플레이스 제품 및 스폿 VM에 대한 청구서가 생성됩니다. 청구서에는 이전 달의 관련 요금이 표시됩니다. 예를 들어 John이 3월 1일에 예약을 구매하고 3월 30일에 다른 예약을 구매했습니다. 4월에는 두 예약에 대한 단일 청구서가 생성됩니다. Azure Marketplace, 예약 및 스폿 VM에 대한 청구서는 항상 9일쯤에 생성됩니다.

사용자가 신용 카드로 Azure에 대한 요금을 지불하고 예약을 구매하는 경우 Azure는 즉시 청구서를 생성합니다. 하지만 청구서로 요금이 청구되는 경우 다음 달 청구서에 예약 대금이 청구됩니다.

**Azure 지원 계획** - 매달 지원 계획 구독에 대한 청구서가 생성됩니다. 첫 번째 청구서는 구매일 또는 구매일로부터 최대 2일 후에 생성됩니다. 후속 지원 계획 청구서는 일반적으로 계정이 만들어진 날에 생성되지만 최대 2일 후에 생성될 수 있습니다.

## <a name="download-your-mosp-azure-subscription-invoice"></a>MOSP Azure 구독 청구서 다운로드

청구서는 MOSP 청구 계정에 속한 구독에 대해서만 생성됩니다. [MOSP 계정에 대한 액세스 권한을 확인하세요](../manage/view-all-accounts.md#check-the-type-of-your-account). 

청구서를 다운로드하려면 구독에 대한 계정 관리자 역할이 있어야 합니다. 소유자, 기여자 또는 읽기 권한자 역할을 가진 사용자는 계정 관리자로부터 권한을 부여받은 경우 해당 청구서를 다운로드할 수 있습니다. 자세한 내용은 [사용자가 청구서를 다운로드하도록 허용](../manage/manage-billing-access.md#opt-in)을 참조하세요.

1. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.
1. 청구 섹션에서 **청구서** 를 선택합니다.  
    ![구독에 대한 청구서 옵션을 선택하는 사용자를 보여주는 스크린샷](./media/download-azure-invoice/select-subscription-invoice.png)
1. 다운로드할 청구서를 선택한 다음, **청구서 다운로드** 를 선택합니다.  
    ![MOSP 청구서 다운로드 옵션이 있는 스크린샷](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. 또한 사용한 수량 및 요금의 일일 분석 결과를 다운로드할 수 있습니다. 이렇게 하려면 다운로드 아이콘을 클릭한 다음 사용량 세부 정보 섹션에서 **Azure 사용량 파일 준비** 단추를 클릭합니다. CSV 파일을 준비하는 데 몇 분 정도 걸릴 수 있습니다.  
    ![청구서 및 사용량 다운로드 옵션을 보여주는 스크린샷](./media/download-azure-invoice/usage-and-invoice-subscription.png)

청구서에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](../understand/review-individual-bill.md)를 참조하세요. 비정상적인 비용을 식별하려면 [예기치 못한 요금 분석](analyze-unexpected-charges.md)을 참조하세요.

## <a name="download-your-mosp-support-plan-invoice"></a>MOSP 지원 계획 청구서 다운로드

청구서는 MOSP 청구 계정에 속한 지원 계획 구독에 대해서만 생성됩니다. [MOSP 계정에 대한 액세스 권한을 확인하세요](../manage/view-all-accounts.md#check-the-type-of-your-account).

청구서를 다운로드하려면 지원 계획 구독에 대한 계정 관리자 역할이 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색합니다.  
    ![비용 관리 + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/download-azure-invoice/search-cmb.png)
1. 왼쪽에서 **청구서** 를 선택합니다.
1. 지원 플랜 구독을 선택합니다.  
    [![MOSP 지원 플랜 청구서 청구 프로필 목록을 보여주는 스크린샷](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. 다운로드할 청구서를 선택한 다음, **청구서 다운로드** 를 선택합니다.  
    ![MOSP 지원 플랜 청구서의 다운로드 옵션을 보여주는 스크린샷](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-your-subscription-invoice"></a>다른 사용자가 구독 청구서를 다운로드할 수 있도록 허용

청구서를 다운로드하려면 다음을 수행합니다.

1.  구독의 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2.  **Cost Management + 청구** 를 검색합니다.

    ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/download-azure-invoice/search-cmb.png)

3.  왼쪽에서 **청구서** 를 선택합니다.

4.  Azure 구독을 선택한 다음, **다른 사람이 청구서를 다운로드할 수 있도록 허용** 을 클릭합니다.

    [![청구서에 액세스 선택을 보여주는 스크린샷](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)

5.  페이지 위쪽에서 **켜기**, **저장** 을 차례로 선택합니다.  
    ![청구서에 액세스 켜기를 선택하는 모습을 보여주는 스크린샷](./media/download-azure-invoice/cmb-access-to-invoice.png)
    
> [!NOTE]
> 기밀 정보 또는 개인 식별 가능한 정보는 제3자와 공유하지 않는 것이 좋습니다. 이 권장 사항은 비용 최적화를 위해 Azure 청구서를 제3자와 공유하는 경우에도 마찬가지로 적용됩니다. 자세한 내용은 https://azure.microsoft.com/support/legal/ 및 https://www.microsoft.com/trust-center를 참조하세요.

## <a name="get-mosp-subscription-invoice-in-email"></a>MOSP 구독 청구서를 이메일로 받기

이메일로 청구서를 받도록 옵트인하려면 구독 또는 지원 계획에 대한 계정 관리자 역할이 있어야 합니다. 옵트인한 후에는 이메일로 청구서를 받을 또 다른 수신자를 추가할 수 있습니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  **Cost Management + 청구** 를 검색합니다.  
3.  왼쪽에서 **청구서** 를 선택합니다.
4.  Azure 구독 또는 지원 계획 구독을 선택한 다음, **이메일로 청구서 받기** 를 선택합니다.  
    [![이메일로 청구서 받기 옵션을 보여주는 스크린샷](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. **이메일 청구서** 를 클릭하고 사용 약관에 동의합니다.  
    ![옵트인 흐름 2단계를 보여주는 스크린샷](./media/download-azure-invoice/invoicearticlestep02.png)
6. 청구서는 기본 통신 이메일로 전송됩니다. **프로필 업데이트** 를 선택하여 이메일을 업데이트합니다.  
    ![옵트인 흐름 3단계를 보여주는 스크린샷](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoice"></a>구독 및 지원 플랜 청구서 공유

매월 구독 및 지원 플랜에 대한 청구서를 회계 팀과 공유하거나 자신의 다른 이메일 주소 중 하나로 보낼 수 있습니다.

1. [구독 및 지원 계획의 청구서를 이메일로 받기](#get-mosp-subscription-invoice-in-email)의 단계를 수행하고 **받는 사람 구성** 을 선택합니다.  
    [![받는 사람 구성을 선택하는 경우를 보여주는 스크린샷](./media/download-azure-invoice/invoice-article-step03.png)](./media/download-azure-invoice/invoice-article-step03-zoomed.png#lightbox)
1. 이메일 주소를 입력한 다음, **받는 사람 추가** 를 선택합니다. 이메일 주소를 여러 개 추가할 수 있습니다.  
    ![수신자를 추가하는 사용자를 보여주는 스크린샷](./media/download-azure-invoice/invoice-article-step04.png)
1. 이메일 주소를 모두 추가한 후 화면 아래쪽에서 **완료** 를 선택합니다.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>MCA 및 MPA 청구 계정의 청구서

귀하의 조직이 Microsoft 담당자와 협력하여 MCA에 서명하는 경우 MCA 청구 계정이 생성됩니다. Azure 웹 사이트를 통해 [종량제 요금을 사용한 계정](https://azure.microsoft.com/offers/ms-azr-0003p/)이나 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입한 특정 지역의 일부 고객은 MCA에 대한 청구 계정도 있을 수 있습니다. 자세한 내용은 [MCA 청구 계정 시작](../understand/mca-overview.md)을 참조하세요.

MPA 청구 계정은 CSP(클라우드 솔루션 공급자) 파트너가 자체 고객을 새로운 상거래 환경에서 관리할 수 있도록 생성되었습니다. Azure Portal에서 청구 계정을 관리하려면 파트너에게 [Azure 플랜](/partner-center/purchase-azure-plan)을 사용하는 고객이 하나 이상 있어야 합니다. 자세한 내용은 [MPA 청구 계정 시작](../understand/mpa-overview.md)을 참조하세요.

월초에 사용자 계정에서 각 청구 프로필의 월별 청구서가 생성됩니다. 청구서에는 이전 달의 모든 Azure 구독 및 기타 구매건의 해당 요금이 포함됩니다. 예를 들어 John은 3월 5일에 *Azure sub 01* 을 만들고 3월 10일에 *Azure sub 02* 를 만들었습니다. 3월 28일에 *Billing profile 01* 을 사용하여 *Azure support 01* 구독을 구매했습니다. John은 4월 초부터 Azure 구독과 지원 계획에 대한 요금이 포함된 단일 청구서를 받게 됩니다.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>MCA 또는 MPA 청구 프로필 청구서 다운로드

Azure Portal에서 청구서를 다운로드하려면 청구 프로필 소유자, 기여자, 읽기 권한자 또는 송장 관리자여야 합니다. 청구 계정에 대한 소유자, 기여자 또는 읽기 권한자 역할이 있는 사용자는 계정의 모든 청구 프로필에 대한 청구서를 다운로드할 수 있습니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

2.  **Cost Management + 청구** 를 검색합니다.

    ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/download-azure-invoice/search-cmb.png)

3. 왼쪽에서 **청구서** 를 선택합니다.

    [![MCA 청구 계정의 청구서 페이지를 보여주는 스크린샷](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. 청구서 표에서 다운로드하려는 청구서를 선택합니다.

5. 페이지 위쪽에서 **청구서 PDF 다운로드** 를 클릭합니다.

    [![청구서 PDF 다운로드를 보여 주는 스크린샷](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. 또한 **Azure 사용량 다운로드** 를 클릭하여 일일 사용량 및 예상 요금을 다운로드할 수 있습니다. csv 파일을 준비하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="get-your-billing-profiles-invoice-in-email"></a>청구 프로필의 청구서를 이메일로 받기

이메일 청구서 기본 설정을 업데이트하려면 청구 프로필 또는 청구 계정에 대한 소유자 또는 기여자 역할이 있어야 합니다. 옵트인하면 청구 프로필에 대한 소유자, 기여자, 읽기 권한자 및 송장 관리자 역할이 있는 모든 사용자가 이메일로 청구서를 받게 됩니다. 

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
1.  **Cost Management + 청구** 를 검색합니다.  
1.  왼쪽에서 **청구서** 를 선택한 다음, 페이지 위쪽에서 **청구서 이메일 기본 설정** 을 선택합니다.  
    [![청구서에 대한 이메일 청구서 옵션을 보여주는 스크린샷](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  여러 청구 프로필이 있는 경우 청구 프로필을 선택한 다음, **예** 를 선택합니다.  
    [![옵트인 옵션을 보여주는 스크린샷](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  **저장** 을 선택합니다.

MCA 또는 MPA 청구 프로필에 대한 송장 관리자 역할을 할당하여 다른 사용자에게 청구서를 보고, 다운로드하고, 요금을 지불할 수 있는 액세스 권한을 부여할 수 있습니다. 청구서를 이메일로 받기 위해 옵트인한 경우 사용자도 이메일로 청구서를 받게 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색합니다.  
1. 왼쪽에서 **청구 프로필** 을 선택합니다. 청구 프로필 목록에서 송장 관리자 역할을 할당하려는 청구 프로필을 선택합니다.  
   ![청구 프로필을 선택하는 청구 프로필 페이지를 보여주는 스크린샷](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. 왼쪽에서 **Access Control(IAM)** 을 선택한 다음, 페이지 맨 위에서 **추가** 를 선택합니다.  
    ![액세스 제어 페이지를 보여주는 스크린샷](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. 역할 드롭다운 목록에서 **송장 관리자** 를 선택합니다. 액세스 권한을 부여할 사용자의 이메일 주소를 입력합니다. **저장** 을 선택하여 역할을 할당합니다.  
    [![사용자를 송장 관리자로 추가하는 것을 보여주는 스크린샷](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   

## <a name="share-your-billing-profiles-invoice"></a>청구 프로필의 청구서 공유

청구 프로필에 대한 다른 이메일 사용 권한을 회계 팀에 부여하지 않고도 매월 청구서를 회계 팀과 공유하거나 자신의 다른 이메일 주소 중 하나로 보낼 수 있습니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
1.  **Cost Management + 청구** 를 검색합니다.  
1.  왼쪽에서 **청구서** 를 선택한 다음, 페이지 위쪽에서 **청구서 이메일 기본 설정** 을 선택합니다.  
    [![청구서에 대한 이메일 청구서 옵션을 보여주는 스크린샷](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  여러 청구 프로필이 있는 경우 청구 프로필을 선택합니다.
1.  추가 받는 사람 섹션에서 청구서를 받을 이메일 주소를 추가합니다.
    [![청구서 이메일의 추가 받는 사람을 보여주는 스크린샷](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients.png)](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients-zoomed.png#lightbox)
1.  **저장** 을 선택합니다.

##  <a name="why-you-might-not-see-an-invoice"></a>청구서가 보이지 않는 이유

<a name="noinvoice"></a>

몇 가지 이유로 인해 청구서가 보이지 않을 수 있습니다.

- 청구서가 아직 준비되지 않았습니다.
    
    - Azure를 구독한 지 30일 이내입니다. 

    - Azure는 청구 기간이 지나고 며칠 후에 요금을 청구합니다. 따라서 청구서가 아직 생성되지 않았을 수 있습니다.

- 청구서를 볼 수 있는 권한이 없습니다. 
    
    - MCA 또는 MPA 청구 계정이 있는 경우 청구서를 보려면 청구 프로필에 대한 소유자, 기여자, 읽기 권한자 또는 송장 관리자 역할이 있거나 청구 계정에 대한 소유자, 기여자 또는 읽기 권한자 역할이 있어야 합니다. 
    
    - 다른 청구 계정의 경우 계정 관리자가 아니면 청구서가 보이지 않을 수 있습니다.

- 계정이 청구서를 지원하지 않습니다.

    - MOSP(Microsoft Online Services Program) 청구 계정이 있고 월별 크레딧 금액이 포함된 Azure 체험 계정 또는 구독에 가입한 경우 월별 크레딧 금액을 초과하는 경우에만 청구서를 받게 됩니다.

    - MCA(Microsoft 고객 계약) 또는 MPA(Microsoft 파트너 계약)의 청구 계정이 있는 경우 항상 청구서를 받게 됩니다.

- 다른 계정 중 하나를 통해 청구서에 액세스할 수 있습니다.

    - 이 상황은 일반적으로 포털에서 청구서를 확인할지 묻는 이메일의 링크를 클릭하면 발생합니다. 링크를 클릭하면 `We can't display your invoices. Please try again` 같은 오류 메시지가 표시됩니다. 청구서를 볼 권한이 있는 이메일 주소로 로그인했는지 확인하세요.

- 다른 ID를 통해 청구서에 액세스할 수 있습니다. 

    - 일부 고객에게는 동일한 이메일 주소를 가진 두 개의 ID(회사 계정 및 Microsoft 계정)가 있습니다. 일반적으로 해당 ID 중 하나에만 청구서를 볼 수 있는 권한이 부여됩니다. 권한이 없는 ID를 사용하여 로그인하면 청구서가 표시되지 않습니다. 올바른 ID를 사용하여 로그인하고 있는지 확인하세요.

- 잘못된 Azure AD(Azure Active Directory) 테넌트에 로그인했습니다. 

    - 청구 계정은 특정 Azure AD 테넌트와 연결되어 있습니다. 잘못된 테넌트에 로그인하면 청구 계정에 구독에 대한 청구서가 표시되지 않습니다. 올바른 Azure AD 테넌트에 로그인했는지 확인하세요. 올바른 테넌트에 로그인하지 않은 경우 다음에 따라 Azure Portal에서 테넌트를 전환합니다.

        1. 페이지 오른쪽 상단에서 이메일을 선택합니다.

        2. **디렉터리 전환** 을 선택합니다.

           ![포털에서 디렉터리 전환을 선택하는 것을 보여 주는 스크린샷](./media/download-azure-invoice/select-switch-directory.png)

        3. **모든 디렉터리** 섹션에서 디렉터리에 대 한 **스위치** 를 선택 합니다.

           ![포털에서 디렉터리를 선택하는 것을 보여 주는 스크린샷](./media/download-azure-invoice/select-directory.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

청구서 및 요금에 대한 자세한 내용은 다음을 참조하세요.

- [Microsoft Azure 사용량 및 요금 보기 및 다운로드](download-azure-daily-usage.md)
- [Microsoft Azure 요금 청구 방식 이해](review-individual-bill.md)
- [Azure 청구서의 용어 이해](understand-invoice.md)

MCA가 있는 경우 다음을 참조하세요.

- [청구 프로필 청구서의 요금 이해](review-customer-agreement-bill.md)
- [청구 프로필 청구서의 용어 이해](mca-understand-your-invoice.md)
- [청구 프로필에 대한 Azure 사용량 및 요금 파일 이해](mca-understand-your-usage.md)