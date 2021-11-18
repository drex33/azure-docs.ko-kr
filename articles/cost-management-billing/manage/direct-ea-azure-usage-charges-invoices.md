---
title: Azure 사용 요약 세부 정보 보기 및 직접 EA 등록에 대한 보고서 다운로드
description: 이 문서에서는 EA(직접 기업계약) 등록의 엔터프라이즈 관리자가 사용량 데이터, Azure 선불 사용량 및 Azure Portal 다른 사용량과 관련된 요금의 요약을 볼 수 있는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 11/16/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: be9040fea81a1a8980874ebd5ec125f170b1bf42
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718548"
---
# <a name="view-your-usage-summary-details-and-download-reports-for-direct-ea-enrollments"></a>사용 요약 세부 정보 보기 및 직접 EA 등록에 대한 보고서 다운로드

이 문서에서는 EA(직접 기업계약) 등록의 엔터프라이즈 관리자가 사용량 데이터, Azure 선불 사용량 및 Azure Portal 다른 사용량과 관련된 요금의 요약을 볼 수 있는 방법을 설명합니다. 요금은 등록의 모든 계정 및 구독에 대한 요약 수준에서 표시됩니다.

[EA 관리자가 사용량 및 청구서 관리 비디오를 확인하세요.](https://www.youtube.com/watch?v=bO8V9eLfQHY) Azure Portal 시리즈 비디오 시리즈에서 [직접 Enterprise 고객 청구 환경의](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) 일부입니다.

>[!VIDEO https://www.youtube.com/embed/bO8V9eLfQHY]

## <a name="prerequisites"></a>사전 요구 사항

청구서 요금을 검토하고 확인하려면 엔터프라이즈 관리자여야 합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요. 조직의 Enterprise 관리자가 누구인지 모르는 경우 Azure Portal 지원 요청을 만듭니다.

## <a name="review-usage-charges"></a>사용 요금 검토

특정 계정의 자세한 사용량을 보려면 사용량 세부 정보 보고서를 다운로드합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + Billing** 검색한 다음 선택합니다.
1. 탐색 메뉴에서 **청구 범위를** 선택한 다음, 작업할 청구 계정을 선택합니다.
1. 탐색 메뉴에서 **사용량 + 요금을** 선택합니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" alt-text="사용량 + 요금 페이지를 보여주는 스크린샷." lightbox="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" :::
1. 이전 연도의 세부 정보를 보려면 **시간 창** 을 선택합니다.

다음 표에는 Azure Portal 사용량 + 요금 페이지에 표시된 용어와 설명이 나와 있습니다. Azure Portal 표시된 요금은 USD 통화입니다.

| **기간** | **설명** |
| --- | --- |
| 월 | 사용량 월 |
| 크레딧에 대한 요금 | 해당 특정 기간에 적용된 크레딧입니다. |
| 서비스 초과 | 조직의 사용 요금이 크레딧 잔액을 초과합니다. |
| 별도로 청구 | 조직에서 사용한 서비스가 크레딧에 적용되지 않습니다. |
| Azure Marketplace | Azure Marketplace 구매 및 사용량은 조직의 크레딧에 포함되지 않으며 별도로 청구됩니다. |
| 총 요금 | 크레딧에 대한 요금 + 서비스 초과분 + 별도로 청구 + Azure Marketplace |

## <a name="download-usage-charges-csv-file"></a>사용 요금 CSV 파일 다운로드

Enterprise 관리자는 사용량 + 요금 다운로드 페이지를 사용하여 다음 보고서를 CSV 파일로 다운로드합니다.

- **사용량 세부 정보** - 선택한 항목에 따라 CSV 파일은 RI(예약) 구매를 포함한 모든 요금(사용량 및 구매)을 제공합니다. 또는 예약 구매를 포함한 분할 상환 요금(사용량 및 구매)입니다.
- **Marketplace 스토어 요금** - Marketplace 스토어 요금 CSV 파일에는 지정된 청구 기간의 일별 사용량 기반 Marketplace 요금 분석이 포함되어 있습니다.
- **가격표** - 가격표 CSV 파일에는 지정된 등록 및 청구 기간의 각 미터에 적용 가능한 요금이 포함됩니다.
- **잔액 및 요약** - 잔액 및 요약 CSV 파일에는 잔액, 신규 구매, Marketplace 서비스 요금, 조정 및 초과분 요금에 대한 월별 정보 요약이 포함되어 있습니다.

1. 월 보고서 옆에 있는 **다운로드** 기호를 선택합니다.  
:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-usage-charges-csv.png" alt-text="다운로드 옵션을 보여주는 스크린샷." :::
1. 사용량 + 요금 다운로드 페이지에서 다운로드하려는 보고서에 대한 **문서 준비를** 선택합니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" alt-text="문서 준비 페이지를 보여주는 스크린샷." lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" :::
1. Azure에서 월별 사용량에 따라 다운로드를 준비하는 데 시간이 걸릴 수 있습니다. 다운로드할 준비가 되면 **csv 다운로드를** 선택합니다.

Enterprise 관리자는 사용량 + 요금 페이지의 맨 아래에서 선택한 시간 창에 대한 요금의 전체 요약을 볼 수도 있습니다.

:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/usage-charges-summary.png" alt-text="사용 요금 요약을 보여주는 스크린샷." lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png":::

## <a name="download-or-view-your-azure-billing-invoice"></a>Azure 청구서 다운로드 또는 보기

[Azure Portal](https://portal.azure.com) 청구서를 다운로드하거나 이메일로 보낼 수 있습니다. 등록에 대한 청구서를 받도록 설정된 사람에게 청구서가 전송됩니다.

Enterprise 관리자만 청구 청구서를 보고 받을 수 있는 권한이 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](manage-billing-access.md)를 참조하세요.

청구 기간 중에 다음 이벤트 중 하나가 발생하면 Azure 청구서를 받게 됩니다.

- **서비스 초과분** - 조직의 사용 요금이 크레딧 잔액을 초과합니다.
- **별도로 청구된 요금** - 조직에서 사용한 서비스에는 크레딧이 적용되지 않습니다. 다음 서비스에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다.
  - Canonical
  - Citrix XenApp Essentials
  - Citrix XenDesktop
  - Registered User
  - OpenLogic
  - Remote Access Rights XenApp Essentials Registered User
  - Ubuntu Advantage
  - Visual Studio Enterprise(월간)
  - Visual Studio Enterprise(연간)
  - Visual Studio Professional(월간)
  - Visual Studio Professional(연간)
- **Marketplace 요금** - Azure Marketplace 구매 및 사용량에는 조직의 크레딧이 적용되지 않습니다. 따라서 Marketplace 요금에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다. Azure Portal Enterprise 관리자가 Marketplace 구매를 사용하거나 사용하지 않도록 설정할 수 있습니다.

청구서에는 Azure 사용량 요금과 관련된 비용이 먼저 표시되고 Marketplace 요금이 표시됩니다. 크레딧 잔액이 있는 경우 Azure 사용량에 적용되며 청구서에는 마지막 비용 없이 Azure 사용량 및 Marketplace 사용량이 표시됩니다.

### <a name="download-your-azure-invoices-pdf"></a>Azure 청구서(.pdf) 다운로드

대부분의 구독의 경우 Azure Portal 청구서를 다운로드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + Billing** 검색하여 선택합니다.
1. 탐색 메뉴에서 **청구 범위를** 선택한 다음, 작업할 청구 계정을 선택합니다.
1. 탐색 메뉴에서 청구서를 **선택합니다.** 청구서 페이지에는 지난 12개월 동안 생성된 모든 청구서 및 크레딧 메모가 표시됩니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" alt-text="청구서 페이지를 보여주는 스크린샷." lightbox="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" :::
    
1. 청구서 페이지에서 다운로드하려는 청구서의 행을 찾습니다. 행 오른쪽에서 줄임표(**...**) 기호를 선택합니다.
1. 상황에 맞는 메뉴에서 **다운로드를** 선택합니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-context-menu.png" alt-text="다운로드 상황에 맞는 메뉴를 보여주는 스크린샷."  :::

타임스팬을 선택하여 지난 3년 동안의 청구서 세부 정보를 볼 수 있습니다.

청구서에 PDF 파일에 대해 40페이지를 초과하는 여러 줄 항목이 있는 경우 여러 문서 번호로 분할됩니다.

다음 표에는 청구서 페이지에 표시된 용어와 설명이 나와 있습니다.

| **기간** | **설명** |
| --- | --- |
| 문서 날짜 | 청구서 또는 크레딧 메모가 생성된 날짜입니다. |
| 문서 번호 | 청구서 또는 신용 메모 번호입니다. |
| 청구 기간 | 청구서 또는 크레딧 메모에 대한 청구 기간입니다. |
| PO 번호 | 청구서 또는 크레딧 메모의 PO 번호입니다. |
| 총 금액 | 청구서 또는 크레딧의 총 금액입니다. |

## <a name="review-credit-charges"></a>크레딧 요금 검토

이 섹션의 정보는 Azure 선불(이전에는 약정 금액이라고 함)에 대한 시작 잔액, 잔액 및 크레딧 조정을 보는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + Billing** 검색하여 선택합니다.
1. 탐색 메뉴에서 **청구 범위를** 선택한 다음, 작업할 청구 계정을 선택합니다.
1. 탐색 메뉴에서 크레딧 **+ 약정을** 선택합니다.
1. 크레딧 탭에는 크레딧에 대한 분석과 시간별 잔액을 보여주는 그래프가 표시됩니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" alt-text="크레딧 탭을 보여주는 스크린샷." lightbox="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" :::

다음 표에는 크레딧 탭에 표시된 용어와 설명이 나와 있습니다.

| **기간** | **설명** |
| --- | --- |
| 월 | 크레딧 월 |
| 크레딧 시작 | 해당 월의 시작 크레딧 |
| 새 크레딧 | 새 크레딧이 추가되었습니다. |
| 조정 | 월별 조정 |
| 요금에 적용되는 크레딧 | 생성된 청구서 또는 크레딧의 총 금액 |
| 크레딧 종료 | 크레딧 최종 잔액 |

## <a name="review-reservation-transaction-details"></a>예약 트랜잭션 세부 정보 검토

Azure Portal 기업계약 대해 배치된 모든 예약을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + Billing** 검색하여 선택합니다.
1. 탐색 메뉴에서 **청구 범위를** 선택한 다음, 작업할 청구 계정을 선택합니다.
1. 탐색 메뉴에서 예약 **트랜잭션을 선택합니다.** 다음 이미지에 표시된 가격은 예입니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" alt-text="예약 트랜잭션 페이지를 보여주는 스크린샷." lightbox="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" :::

다음 표에는 예약 트랜잭션 페이지에 표시된 용어와 설명이 나와 있습니다.

| **기간** | **설명** |
| --- | --- |
| Date | 예약이 이루어진 날짜 |
| 이름 | 예약 이름 |
| 설명 | 예약에 대한 설명 |
| 구독 구매 | 예약이 이루어진 구독 |
| 구매 계정 | 예약이 만든 계정의 구매 계정 |
| 대금 청구 주기 | 예약의 청구 빈도 |
| 형식 | 트랜잭션의 유형입니다. 예를 들어 구매 또는 환불입니다. |
| 수량 | 구매한 예약 수량 |
| 금액(USD) | 예약 비용 |

## <a name="csv-report-formatting-issues"></a>CSV 보고서 서식 지정 문제

Excel CSV 보고서를 볼 때 청구 통화가 표시되면 쉼표 및 마침표와 관련된 서식 문제가 있을 수 있습니다.

표시된 비용은 예입니다.

예를 들어 다음과 같은 코드가 표시될 수 있습니다.

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
|---|--- | ---|---|
| 시간 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

그러나 다음이 *표시됩니다.*

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 시간 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

서식 지정 문제는 Excel 가져오기 기능의 기본 설정으로 인해 발생합니다. Excel 모든 필드를 *일반* 텍스트로 가져오고 숫자가 수학 표준으로 구분되어 있다고 가정합니다. 예: *1,000.00*.

통화에서 천 번째 자리 구분 기호에 마침표(**.**)를 사용하고 소수 자릿수 구분 기호에 쉼표(**,**)를 사용하는 경우 잘못 표시됩니다. 예: *1.000,00.* 가져오기 결과는 지역별 언어 설정에 따라 달라질 수 있습니다.

서식 지정 문제 없이 CSV 파일을 가져오려면 다음을 수행합니다.

1. Microsoft Excel에서 **파일** > **열기** 로 차례로 이동합니다. 텍스트 가져오기 마법사가 나타납니다.
1. **원본 데이터 형식** 아래에서 **구분 기호로 분리됨** 을 선택합니다. 기본값은 **고정 폭** 입니다.
1. **다음** 을 선택합니다.
1. **구분 기호** 아래에서 **쉼표** 상자를 선택합니다. 이 확인란이 선택되면 **탭** 을 지웁니다.
1. **다음** 을 선택합니다.
1. **ResourceRate** 및 **ExtendedCost** 열로 스크롤합니다.
1. **ResourceRate** 열을 선택합니다. 검은색으로 강조 표시됩니다.
1. **열 데이터 서식** 섹션 아래에서 **일반** 대신 **텍스트** 를 선택합니다. 열 머리글이 **일반** 에서 **텍스트** 로 변경됩니다.
1. **확장 비용** 열에 대해 8단계와 9단계를 반복한 다음, **마침** 을 선택합니다.

> [!TIP]
> Excel에서 CSV 파일을 자동으로 열도록 설정한 경우 Excel에서 **열기** 기능을 대신 사용해야 합니다. Excel에서 **파일** > **열기** 로 차례로 이동하세요.

## <a name="next-steps"></a>다음 단계

- 직접 엔터프라이즈 관리자가 Azure Portal에서 수행 하는 일반적인 작업에 대해 알아보려면 [Azure DIRECT EA 관리](direct-ea-administration.md)를 참조 하세요.