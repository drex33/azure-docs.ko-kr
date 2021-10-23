---
title: 조직의 Azure 가격 책정 보기 및 다운로드
description: 조직의 가격 책정으로 가격 책정 또는 예상 비용을 살펴보고 다운로드하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8ee5cc31e0127ead62fa91f6b1bfab66854aac83
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260413"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>조직의 Azure 가격 책정 보기 및 다운로드

Azure EA(기업계약), MCA(Microsoft 고객 계약) 또는 MPA(Microsoft 파트너 계약)가 있는 Azure 고객은 Azure Portal의 가격 책정을 보고 다운로드할 수 있습니다. [청구 계정 유형을 확인하는 방법을 알아봅니다](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>기업 계약에 대한 가격 책정 다운로드

엔터프라이즈 관리자가 조직에 대해 설정한 정책에 따라 특정 관리 역할만 조직의 EA 가격 정보에 대한 액세스 권한을 제공합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요.

1. 엔터프라이즈 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. *Cost Management + 청구* 를 검색합니다.  
   ![Azure Portal 검색을 보여 주는 스크린샷](./media/ea-pricing/portal-cm-billing-search.png)
1. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 먼저 선택해야 할 수도 있습니다.
1. 탐색 메뉴에서 **사용량 + 요금** 을 선택 합니다.  
   ![청구 아래의 사용량 및 요금을 보여 주는 스크린샷](./media/ea-pricing/ea-pricing-usage-charges-nav.png)
1. ![다운로드 아이콘](./media/ea-pricing/download-icon.png)을 선택합니다. 해당 월에 대해 **다운로드합니다**.
1. 사용량 + 요금 다운로드 페이지의 가격표에서 **문서 준비** 를 선택 합니다. 파일을 준비 하는 데 시간이 걸릴 수 있습니다.  
    :::image type="content" source="./media/ea-pricing/download-enterprise-agreement-price-sheet-01.png" alt-text="사용량 다운로드 + 요금 옵션을 보여주는 스크린샷." :::
1. 파일이 다운로드 될 준비가 되 면 **Csv 다운로드** 를 선택 합니다.


## <a name="download-pricing-for-an-mca-or-mpa-account"></a>MCA 또는 MPA 계정에 대한 가격 책정 다운로드

MCA가 있는 경우 가격 책정을 보고 다운로드하려면 청구 프로필 소유자, 기여자, 독자 또는 송장 관리자여야 합니다. MPA가 있는 경우 가격 책정을 보고 다운로드하려면 파트너 조직에 글로벌 관리자 및 관리자 에이전트 역할이 있어야 합니다.

### <a name="download-price-sheets-for-billed-charges"></a>청구 요금에 대한 가격표 다운로드

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. *Cost Management + 청구* 를 검색합니다.
1. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 먼저 선택해야 할 수도 있습니다.
1. **청구서** 를 선택합니다.
1. 청구서 그리드에서 다운로드하려는 가격표에 해당하는 청구서의 행을 찾습니다.
1. 행 끝에 있는 줄임표(`...`)를 클릭합니다.  
    ![선택한 줄임표를 보여 주는 스크린샷](./media/ea-pricing/billingprofile-invoicegrid-new.png)
1. 선택한 청구서에서 서비스에 대한 가격을 확인하려면 **청구서 가격표** 를 선택합니다.
1. 지정된 청구 기간에 대한 모든 Azure 서비스의 가격을 확인하려면 **Azure 가격표** 를 선택합니다.  
    ![가격표가 있는 상황에 맞는 메뉴를 보여 주는 스크린샷](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>현재 청구 기간에 대한 가격표 다운로드

MCA가 있는 경우 현재 청구 기간에 대한 가격 책정을 다운로드할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. *Cost Management + 청구* 를 검색합니다.
1. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 먼저 선택해야 할 수도 있습니다.
1. **개요** 영역에서 월간 누계 요금 아래의 다운로드 링크를 찾습니다.
1. **Azure 가격표** 를 선택합니다.  
    ![개요에서 다운로드를 보여 주는 스크린샷](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Azure 가격 책정 계산기로 비용 예상

조직의 가격 책정을 사용하여 Azure 가격 책정 계산기로 비용을 예상할 수도 있습니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)로 이동합니다.
1. 오른쪽 맨 위에서 **로그인** 을 선택합니다.
1. **프로그램 및 제품** > **라이선스 프로그램** 아래에서 **EA(기업계약)** 를 선택합니다.
1. **프로그램 및 제품** > **선택한 계약** 아래에서 **선택된 항목 없음** 을 선택합니다.  
    ![스크린샷은 사용 가능한 프로그램 및 제품을 보여줍니다.](./media/ea-pricing/ea-pricing-calculator-estimate.png)
1. 조직을 선택합니다.
1. **적용** 을 선택합니다.
1. 제품을 검색한 다음, 예상에 추가합니다.
1. 표시된 예상 가격은 선택한 조직의 가격을 기준으로 합니다.

## <a name="check-your-billing-account-type"></a>청구 계정 유형 확인
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>다음 단계

EA 고객인 경우 다음을 참조하세요.

- [Azure의 EA 청구 정보에 대한 액세스 관리](manage-billing-access.md)
- [Microsoft Azure 청구서 보기 및 다운로드](../understand/download-azure-invoice.md)
- [Microsoft Azure 사용량 및 요금 보기 및 다운로드](../understand/download-azure-daily-usage.md)
- [기업계약 고객에 대한 청구서 이해](../understand/review-enterprise-agreement-bill.md)

Microsoft 고객 계약이 있는 경우 다음을 참조하세요.

- [Microsoft 고객 계약에 대한 가격표에서 사용 약관 이해](mca-understand-pricesheet.md)
- [Microsoft Azure 청구서 보기 및 다운로드](../understand/download-azure-invoice.md)
- [Microsoft Azure 사용량 및 요금 보기 및 다운로드](../understand/download-azure-daily-usage.md)
- [청구 프로필에 대한 세금 문서 보기 및 다운로드](../understand/mca-download-tax-document.md)
- [청구 프로필 청구서의 요금 이해](../understand/review-customer-agreement-bill.md)
