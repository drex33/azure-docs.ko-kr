---
title: Azure 사용 요약 정보를 확인 하 고 direct EA 등록 보고서를 다운로드 합니다.
description: 이 문서에서는 등록 기업계약의 엔터프라이즈 관리자가 사용 현황 데이터, 사용 되는 Azure 선불 및 Azure Portal의 다른 사용량과 관련 된 요금에 대 한 요약을 볼 수 있는 방법을 설명 합니다.
author: bandersmsft
ms.author: banders
ms.date: 10/12/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 005f2c16e463bd01c7e453548ddb29c50e2f937b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008790"
---
# <a name="view-your-usage-summary-details-and-download-reports-for-direct-ea-enrollments"></a>사용 요약 정보를 확인 하 고 direct EA 등록 보고서를 다운로드 합니다.

이 문서에서는 등록 기업계약의 엔터프라이즈 관리자가 사용 현황 데이터, 사용 되는 Azure 선불 및 Azure Portal의 다른 사용량과 관련 된 요금에 대 한 요약을 볼 수 있는 방법을 설명 합니다. 요금은 모든 계정 및 등록 구독에서 요약 수준으로 표시 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

청구서 요금을 검토하고 확인하려면 엔터프라이즈 관리자여야 합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요. 조직에 Enterprise 관리자가 누구 인지 모르는 경우 Azure Portal에서 지원 요청을 만듭니다.

## <a name="review-usage-charges"></a>사용 요금 검토

특정 계정의 자세한 사용량을 보려면 사용량 세부 정보 보고서를 다운로드합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색 하 고 선택 합니다.
1. 탐색 메뉴에서 **청구 범위** 를 선택한 다음 작업 하려는 청구 계정을 선택 합니다.
1. 탐색 메뉴에서 **사용량 + 요금** 을 선택 합니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" alt-text="사용량 + 요금 페이지를 보여 주는 스크린샷" lightbox="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" :::
1. 이전 연도의 세부 정보를 보려면 **Timespan** 을 선택 합니다.

다음 표에는 Azure Portal의 사용량 + 요금 페이지에 표시 되는 용어 및 설명이 나와 있습니다. Azure Portal 표시 되는 요금은 USD 통화 단위입니다.

| **기간** | **설명** |
| --- | --- |
| 월 | 사용 월 |
| 크레딧 대비 요금 | 해당 특정 기간에 적용된 크레딧입니다. |
| 서비스 초과분 | 조직의 사용 요금이 신용 잔액을 초과 합니다. |
| 별도로 청구 | 조직에서 사용한 서비스가 크레딧에 적용되지 않습니다. |
| Azure Marketplace | Azure Marketplace 구매 및 사용은 조직의 크레딧이 적용 되지 않으며 별도로 청구 됩니다. |
| 총 요금 | 크레딧을 청구 하는 요금 + 서비스 초과분 + + Azure Marketplace |

## <a name="download-usage-charges-csv-file"></a>사용 요금 CSV 파일 다운로드

Enterprise 관리자는 사용량 + 요금 다운로드 페이지를 사용 하 여 다음 보고서를 CSV 파일로 다운로드할 수 있습니다.

- **사용량 세부 정보** -사용자의 선택에 따라, CSV 파일은 RI (예약) 구매를 포함 하 여 모든 요금 (사용량 및 구매)을 제공 합니다. 또는 예약 구매를 포함 하는 분할 상환 요금 (사용량 및 구매).
- **Marketplace 스토어** 요금-Marketplace 저장소 요금 CSV 파일에는 지정 된 청구 기간에 대 한 일별 사용량 기반 Marketplace 요금 분석이 포함 됩니다.
- **가격표** -가격표 CSV 파일에는 지정 된 등록 및 청구 기간에 대 한 각 미터의 적용 가능한 요금이 포함 됩니다.
- **잔액 및 요약** -잔액 및 요약 CSV 파일에는 잔액, 신규 구매, Marketplace 서비스 요금, 조정 및 초과분 요금에 대 한 월별 정보가 요약 되어 있습니다.

1. 월 보고서 옆에 있는 **다운로드** 기호를 선택 합니다.  
:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-usage-charges-csv.png" alt-text="다운로드 옵션을 보여 주는 스크린샷" :::
1. 사용량 + 요금 다운로드 페이지에서 다운로드 하려는 보고서에 대해 **문서 준비** 를 선택 합니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" alt-text="문서 준비 페이지를 보여 주는 스크린샷" lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" :::
1. Azure에서 사용자의 월간 사용량에 따라 다운로드를 준비 하는 데 시간이 걸릴 수 있습니다. 다운로드할 준비가 되 면 **Csv 다운로드** 를 선택 합니다.

Enterprise 관리자는 사용량 + 요금 페이지의 맨 아래에 있는 선택한 Timespan 청구 금액에 대 한 전체 요약을 볼 수도 있습니다.

:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/usage-charges-summary.png" alt-text="사용 요금에 대 한 요약을 보여 주는 스크린샷" lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png":::

## <a name="download-or-view-your-azure-billing-invoice"></a>Azure 청구서 다운로드 또는 보기

[Azure Portal](https://portal.azure.com) 에서 송장을 다운로드 하거나 전자 메일로 보낼 수 있습니다. 등록에 대한 청구서를 받도록 설정된 사람에게 청구서가 전송됩니다.

Enterprise 관리자 에게만 청구서를 보고 받을 수 있는 권한이 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](manage-billing-access.md)를 참조하세요.

청구 기간 중에 다음 이벤트 중 하나가 발생하면 Azure 청구서를 받게 됩니다.

- **서비스 초과분** -조직의 사용 요금은 크레딧 잔액을 초과 합니다.
- **별도로 요금이 청구** 됩니다. 조직에서 사용 하는 서비스는 크레딧을 포함 하지 않습니다. 다음 서비스에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다.
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
- **Marketplace 요금은** Azure Marketplace 구매 및 사용은 조직의 크레딧을 포함 하지 않습니다. 따라서 Marketplace 요금에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다. Azure Portal에서 Enterprise 관리자는 Marketplace 구매를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

청구서에는 먼저 관련 비용이 포함 된 Azure 사용 요금이 표시 되 고 그 다음에는 Marketplace 요금이 표시 됩니다. 신용 잔액을 사용 하는 경우 Azure 사용에 적용 되며 청구서에는 비용 없이 Azure 사용량 및 Marketplace 사용 현황이 표시 됩니다.

### <a name="download-your-azure-invoices-pdf"></a>Azure 청구서(.pdf) 다운로드

대부분의 구독에서 Azure Portal 청구서를 다운로드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색 하 고 선택 합니다.
1. 탐색 메뉴에서 **청구 범위** 를 선택한 다음 작업 하려는 청구 계정을 선택 합니다.
1. 탐색 메뉴에서 **송장** 을 선택 합니다. 청구서 페이지에는 지난 12 개월 동안 생성 된 모든 송장 및 크레딧 메모를 표시 합니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" alt-text="청구서 페이지를 보여 주는 스크린샷" lightbox="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" :::
    
1. 청구서 페이지에서 다운로드 하려는 청구서의 행을 찾습니다. 행의 오른쪽에 있는 줄임표 (**...**) 기호를 선택 합니다.
1. 상황에 맞는 메뉴에서 **다운로드** 를 선택 합니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-context-menu.png" alt-text="다운로드 상황에 맞는 메뉴를 보여 주는 스크린샷"  :::

Timespan을 선택 하 여 지난 3 년간의 송장 세부 정보를 볼 수 있습니다.

청구서에 PDF 파일에 대해 40 페이지를 초과 하는 여러 줄 항목이 있는 경우 여러 문서 번호로 분할 됩니다.

다음 표에는 청구서 페이지에 표시 되는 용어 및 설명이 나와 있습니다.

| **기간** | **설명** |
| --- | --- |
| 문서 날짜 | 청구서 또는 신용 메모가 생성 된 날짜입니다. |
| 문서 번호 | 청구서 또는 신용 메모 번호. |
| 청구 기간 | 청구서 또는 신용 메모를 청구 하는 기간입니다. |
| PO 번호 | 청구서 또는 신용 메모의 PO 번호입니다. |
| 총 금액 | 송장 또는 크레딧의 총 금액입니다. |

## <a name="review-credit-charges"></a>신용 요금 검토

이 섹션에서는 Azure 선불 (이전에는 금액 약정 이라고 함)에 대 한 시작 잔액, 종료 잔액 및 크레딧 조정을 보는 방법에 대해 설명 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색 하 고 선택 합니다.
1. 탐색 메뉴에서 **청구 범위** 를 선택한 다음 작업 하려는 청구 계정을 선택 합니다.
1. 탐색 메뉴에서 **크레딧 + 약정** 을 선택 합니다.
1. 크레딧 탭에는 크레딧을 분석 하 고 시간에 따른 잔액을 보여 주는 그래프가 표시 됩니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" alt-text="크레딧 탭을 보여 주는 스크린샷" lightbox="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" :::

다음 표에는 크레딧을 탭에 표시 되는 용어 및 설명이 나와 있습니다.

| **기간** | **설명** |
| --- | --- |
| 월 | 신용의 월 |
| 크레딧 시작 | 해당 월의 시작 크레딧 |
| 새 크레딧 | 새 크레딧을 추가 했습니다. |
| 조정 | 월에 적용 된 조정 |
| 요금 청구 금액에 적용 된 크레딧 | 송장 또는 크레딧이 생성 된 총 금액입니다. |
| 크레딧 종료 | 크레딧 최종 잔액 |

## <a name="review-reservation-transaction-details"></a>예약 트랜잭션 세부 정보 검토

Azure Portal에서 기업계약에 대해 배치 된 모든 예약을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색 하 고 선택 합니다.
1. 탐색 메뉴에서 **청구 범위** 를 선택한 다음 작업 하려는 청구 계정을 선택 합니다.
1. 탐색 메뉴에서 **예약 트랜잭션** 을 선택 합니다. 다음 그림에 표시 된 가격은 예제입니다.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" alt-text="예약 트랜잭션 페이지를 보여 주는 스크린샷" lightbox="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" :::

다음 표에는 예약 트랜잭션 페이지에 표시 되는 용어와 설명이 나와 있습니다.

| **기간** | **설명** |
| --- | --- |
| 날짜 | 예약이 만들어진 날짜 |
| 이름 | 예약 이름 |
| Description | 예약에 대 한 설명 |
| 구독 구매 | 예약이 수행 된 구독 |
| 구매 계정 | 예약 계정을 만든 구매 계정 |
| 대금 청구 주기 | 예약의 청구 빈도 |
| 형식 | 트랜잭션의 유형입니다. 예를 들어 구매 또는 환불 합니다. |
| 수량 | 구매한 예약 수량 |
| 금액(USD) | 예약 비용 |

## <a name="csv-report-formatting-issues"></a>CSV 보고서 서식 지정 문제

Excel에서 CSV 보고서를 볼 때 청구 통화는 유로화 인 경우 쉼표 및 마침표와 관련 된 서식 지정 문제가 있을 수 있습니다.

표시 되는 비용은 예입니다.

예를 들어 다음과 같은 코드가 표시될 수 있습니다.

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
|---|--- | ---|---|
| 시간 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

그러나 다음이 표시 *되어야 합니다* .

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 시간 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

서식 지정 문제는 Excel의 가져오기 기능에 대 한 기본 설정 때문에 발생 합니다. Excel 모든 필드를 *일반* 텍스트로 가져오며 수학적 표준에서 숫자가 분리 된 것으로 가정 합니다. 예: *1000.00*.

통화에서 1000 단위 구분 기호에 마침표 (**.**)를 사용 하 고 소수 자릿수 구분 기호에 쉼표 (**,**)를 사용 하는 경우 잘못 표시 됩니다. 예: *1.000, 00*. 가져오기 결과는 지역별 언어 설정에 따라 달라질 수 있습니다.

서식 지정 문제 없이 CSV 파일을 가져오려면 다음을 수행합니다.

1. Microsoft Excel에서 **파일** > **열기** 로 차례로 이동합니다. 텍스트 가져오기 마법사가 나타납니다.
1. **원본 데이터 형식** 아래에서 **구분 기호로 분리됨** 을 선택합니다. 기본값은 **고정 폭** 입니다.
1. **다음** 을 선택합니다.
1. **구분 기호** 에서 **쉼표** 의 상자를 선택 합니다. 이 확인란이 선택되면 **탭** 을 지웁니다.
1. **다음** 을 선택합니다.
1. **ResourceRate** 및 **ExtendedCost** 열로 스크롤합니다.
1. **ResourceRate** 열을 선택합니다. 검은색으로 강조 표시 됩니다.
1. **열 데이터 서식** 섹션 아래에서 **일반** 대신 **텍스트** 를 선택합니다. 열 머리글은 **일반** 에서 **텍스트로** 변경 됩니다.
1. **확장 비용** 열에 대해 8단계와 9단계를 반복한 다음, **마침** 을 선택합니다.

> [!TIP]
> Excel에서 CSV 파일을 자동으로 열도록 설정한 경우 Excel에서 **열기** 기능을 대신 사용해야 합니다. Excel에서 **파일** > **열기** 로 차례로 이동하세요.

## <a name="next-steps"></a>다음 단계

- 직접 엔터프라이즈 관리자가 Azure Portal에서 수행 하는 일반적인 작업에 대해 알아보려면 [Azure DIRECT EA 관리](direct-ea-administration.md)를 참조 하세요.