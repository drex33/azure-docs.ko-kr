---
title: 사용량 파일 피벗 테이블을 사용하여 Azure MCA 청구 문제 해결
description: 이 문서는 CSV 사용량 파일에서 만든 피벗 테이블을 사용하여 MCA(Microsoft 고객 계약) 청구 문제를 해결하는 데 도움이 됩니다.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 1d4e6a407e75a70618089f20a7d19343785380ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616482"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>사용량 파일 피벗 테이블을 사용하여 MCA 청구 문제 해결

이 문서는 사용량 파일의 피벗 테이블을 사용하여 MCA(Microsoft 고객 계약) 청구 문제를 해결하는 데 도움이 됩니다. Azure 사용량 파일에는 모든 Azure 사용량 및 소비 정보가 포함됩니다. 파일의 정보는 다음을 이해하는 데 도움이 될 수 있습니다.

- Azure 예약이 사용되고 적용되는 방법 이해
- Azure Cost Management에서 청구된 청구서를 사용하여 정보 조정
- 비용 급증 문제 해결
- 서비스 수준 계약에 대한 환불 금액 계산

사용량 파일의 정보를 사용하면 사용량 문제를 더 잘 이해하고 진단할 수 있습니다. 사용량 파일은 CSV(쉼표로 구분) 형식으로 생성됩니다. 사용량 파일은 큰 CSV 파일일 수 있으므로 Excel과 같은 스프레드시트 애플리케이션에서 더 쉽게 피벗 테이블로 조작하여 볼 수 있습니다. 이 문서의 예에서는 Excel을 사용하지만 원하는 스프레드시트 애플리케이션을 사용할 수 있습니다.

청구 프로필 소유자, 기여자, 읽기 권한자 또는 송장 관리자만 사용량 파일을 다운로드할 수 있습니다. 자세한 내용은 [Microsoft 고객 계약에 대한 사용량 다운로드](../understand/download-azure-daily-usage.md)를 참조하세요. 

## <a name="get-the-data-and-format-it"></a>데이터 가져오기 및 형식 지정

Azure 사용량 파일은 CSV 형식이므로 Excel에서 사용할 수 있도록 데이터를 준비해야 합니다. 다음 단계에 따라 데이터를 테이블 형식으로 지정합니다.

1. [Azure Portal에서 사용량 다운로드](../understand/download-azure-daily-usage.md)의 지침에 따라 사용량 파일을 다운로드합니다.
1. Excel에서 파일을 엽니다.
1. 형식이 지정되지 않은 데이터는 다음 예와 비슷합니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="형식이 지정되지 않은 데이터를 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. 테이블에서 첫 번째 필드(**invoiceID**)를 선택합니다.
1. Ctrl+Shift+아래쪽 화살표를 누른 다음, Ctrl+Shift+오른쪽 화살표를 눌러 테이블의 모든 정보를 선택합니다.
1. 위쪽 메뉴에서 **삽입** > **테이블** 을 차례로 선택합니다. [테이블 만들기] 상자에서 **머리글 포함** 을 선택한 다음, **확인** 을 선택합니다.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="테이블 만들기 대화 상자를 보여 주는 예" :::
1. 위쪽 메뉴에서 **삽입** > **피벗 테이블** 을 차례로 선택한 다음, **확인** 을 선택합니다. 파일에 새 시트가 만들어지고, 시트의 오른쪽에 있는 피벗 테이블 영역으로 이동됩니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="피벗 테이블 필드 영역을 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

피벗 테이블 필드 영역은 끌어서 놓기 영역입니다. 다음 섹션으로 계속 진행하여 피벗 테이블을 만듭니다.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>리소스별 Azure 비용을 볼 수 있는 피벗 테이블 만들기

이 섹션에서는 일반적인 전체 Azure 사용량 문제를 해결할 수 있는 피벗 테이블을 만듭니다. 예제 테이블은 리소스를 가장 많이 사용하는 서비스를 조사하는 데 도움이 될 수 있습니다. 또는 가장 많은 비용이 발생하는 리소스와 서비스 요금이 청구되는 방법을 살펴볼 수 있습니다.

1. [피벗 테이블 필드] 영역에서 **미터 범주** 및 **Product** 를 **행** 섹션으로 끕니다. **Product** 를 **미터 범주** 아래에 배치합니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="행의 미터 범주와 Product를 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 다음으로 **costInBillingCurrenty** 열을 **값** 섹션에 추가합니다. 대신 **수량** 열을 사용하여 사용량 단위 및 트랜잭션 수에 대한 정보를 가져올 수도 있습니다. 예를 들어 GB 및 시간입니다. 또는 USD, EUR, INR과 같은 다른 통화의 비용 대신 트랜잭션 수를 가져올 수도 있습니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="피벗 테이블에 추가된 필드를 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. 이제 일반화된 사용량 조사에 대한 대시보드가 있습니다. 피벗 테이블의 필터링 옵션을 사용하여 특정 서비스를 필터링할 수 있습니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="행 레이블에 대한 피벗 테이블 필터 옵션을 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    피벗 테이블에서 두 번째 수준(예: 리소스)을 필터링하려면 테이블에서 두 번째 수준 항목을 선택합니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="필드 선택의 필터 옵션을 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. **ResourceID** 열을 **Product** 아래의 **행** 영역으로 끌어 리소스별 각 서비스의 비용을 확인합니다.
1. **date** 열을 **열** 영역에 추가하여 제품에 대한 일일 사용량을 확인합니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="date를 열 영역에 배치할 위치를 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. 각 월의 열에 대한 **+** 기호를 사용하여 해당 월을 펼치거나 접습니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="+ 기호를 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

**Cost** 및 **Quantity** 열 모두를 **값** 영역에 추가하는 것은 선택 사항입니다. 이렇게 하면 Date 열이 피벗 테이블의 [열] 섹션에 있을 때 각 월 및 일 아래의 각 데이터 섹션에 대해 두 개의 열이 만들어집니다.

추가 필터의 경우 InvoiceSection, costCenter, SubscriptionID, ResourceGroupName 또는 Tags를 필터 섹션에 추가하고 원하는 범위를 선택할 수 있습니다.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>특정 리소스에 대한 비용을 볼 수 있는 피벗 테이블 만들기

단일 리소스에서 여러 서비스에 대해 여러 요금이 발생할 수 있습니다. 예를 들어 하나의 가상 머신에서 컴퓨팅 요금, OS 라이선스, 대역폭(데이터 전송), RI 사용량 및 스냅샷용 스토리지에 대한 비용이 발생할 수 있습니다. 특정 리소스에 대한 전체 사용량을 검토할 때마다 다음 단계에서는 사용량 파일을 사용하여 전체 사용량을 볼 수 있는 대시보드를 만드는 과정을 안내합니다.

1. 오른쪽 메뉴에서 **ResourceID** 를 피벗 테이블 메뉴의 **필터** 섹션으로 끕니다.
1. 비용을 확인하려는 리소스를 선택합니다. **검색** 상자에서 찾으려는 리소스 이름을 입력합니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="resourceID를 검색할 위치를 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. **meterCategory** 및 **Product** 를 **행** 영역에 추가합니다. **Product** 를 **meterCategory** 아래에 배치합니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="피벗 테이블 필드에서 meterCategory를 배치할 위치를 보여 주는 예" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. 다음으로 **확장 비용** 열을 **값** 섹션에 추가합니다. 대신 [사용한 수량] 열을 사용하여 사용량 단위 및 트랜잭션 수에 대한 정보를 가져올 수도 있습니다. 예를 들어 GB 및 시간입니다. 또는 USD, EUR, INR과 같은 다른 통화의 비용 대신 트랜잭션 수를 가져올 수도 있습니다. 이제 리소스에서 사용하는 모든 서비스를 보여 주는 대시보드가 있습니다.
1. **Date** 열을 **열** 섹션에 추가합니다. 이 경우 일일 사용량을 보여 줍니다.
1. 각 월의 열에 있는 **+** 아이콘을 사용하여 펼치거나 접을 수 있습니다.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="+ 기호를 보여 주는 예" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

[!INCLUDE [Troubleshoot usage spikes](../../../includes/cost-management-billing-troubleshoot-usage-spikes.md)]

## <a name="next-steps"></a>다음 단계

- [비용 분석을 사용하여 비용 검색 및 분석](../costs/quick-acm-cost-analysis.md)