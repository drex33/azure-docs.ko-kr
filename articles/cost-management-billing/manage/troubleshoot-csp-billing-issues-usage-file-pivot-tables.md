---
title: 사용량 파일 피벗 테이블을 사용하여 Azure CSP 청구 문제 해결
description: 이 문서는 CSV 사용량 파일에서 만든 피벗 테이블을 사용하여 Azure CSP(클라우드 솔루션 공급자) 청구 문제를 해결하는 데 도움이 됩니다.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 2532b02247a85d180d7b1cccd504838b83a8f93f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591846"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>사용량 파일 피벗 테이블을 사용하여 CSP 청구 문제 해결

이 문서는 파트너 센터 조정(사용량) 파일에서 만든 피벗 테이블을 사용하여 CSP(클라우드 솔루션 공급자) 청구 문제를 해결하는 데 도움이 됩니다. Azure 사용량 파일에는 모든 Azure 사용량 및 소비 정보가 포함됩니다. 파일의 정보는 다음을 이해하는 데 도움이 될 수 있습니다.

- Azure 예약이 사용되고 적용되는 방법 이해
- Azure Cost Management에서 청구된 청구서를 사용하여 정보 조정
- 비용 급증 문제 해결
- 서비스 수준 계약에 대한 환불 금액 계산

사용량 파일의 정보를 사용하면 사용량 문제를 더 잘 이해하고 진단할 수 있습니다. 사용량 파일은 CSV(쉼표로 구분) 형식으로 생성됩니다. 사용량 파일은 큰 CSV 파일일 수 있으므로 Excel과 같은 스프레드시트 애플리케이션에서 더 쉽게 피벗 테이블로 조작하여 볼 수 있습니다. 이 문서의 예제에서는 Excel을 사용하지만, 원하는 다른 스프레드시트 애플리케이션을 사용해도 됩니다.

청구 관리자와 전역 관리자만이 조정 파일을 다운로드할 수 있습니다. 자세한 내용은 [파트너 센터 조정 파일에서 품목을 읽는 방법 알아보기](/partner-center/use-the-reconciliation-files)를 참조하세요.

## <a name="get-the-data-and-format-it"></a>데이터를 가져오고 형식 지정

Azure 사용량 파일은 CSV 형식이므로 Excel에서 사용할 수 있도록 데이터를 준비해야 합니다. 다음 단계에 따라 데이터를 테이블 형식으로 지정합니다.

1. [청구서 찾기](/partner-center/read-your-bill#find-your-bill)의 지침에 따라 사용량 파일을 다운로드합니다.
1. Excel에서 파일을 엽니다.
1. 형식이 지정되지 않은 데이터는 다음 예와 비슷합니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Excel에서 서식이 지정되지 않은 데이터를 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. 테이블에서 첫 번째 필드 **PartnerID** 를 선택합니다.
1. Ctrl + Shift + 아래쪽 화살표를 누른 다음, Ctrl + Shift + 오른쪽 화살표를 눌러 테이블의 모든 정보를 선택합니다.
1. 위쪽 메뉴에서 **삽입** > **테이블** 을 차례로 선택합니다. [테이블 만들기] 상자에서 **머리글 포함** 을 선택한 다음, **확인** 을 선택합니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="테이블 만들기 대화 상자를 보여 주는 예" :::
1. 위쪽 메뉴에서 **삽입** > **피벗 테이블** 을 차례로 선택한 다음, **확인** 을 선택합니다. 파일에 새 시트가 만들어지고, 시트의 오른쪽에 있는 피벗 테이블 영역으로 이동됩니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="피벗 테이블 필드 영역을 보여 주는 예" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

피벗 테이블 필드 영역은 끌어서 놓기 영역입니다. 다음 섹션으로 계속 진행하여 피벗 테이블을 만듭니다.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>리소스별 Azure 비용을 볼 수 있는 피벗 테이블 만들기

이 섹션에서는 일반적인 전체 Azure 사용량 문제를 해결할 수 있는 피벗 테이블을 만듭니다. 예제 테이블은 리소스를 가장 많이 사용하는 서비스를 조사하는 데 도움이 될 수 있습니다. 또는 가장 많은 비용이 발생하는 리소스를 확인하고 서비스 요금이 어떻게 청구되는지 살펴볼 수 있습니다.

1. 피벗 테이블 필드 영역에서 **서비스 이름** 및 **리소스** 를 **행** 영역으로 끌어옵니다. **리소스** 를 **서비스 이름** 에 배치합니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="행의 서비스 이름 및 리소스를 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 그런 다음, **값** 영역에 **세후 총계** 를 넣습니다. [사용한 수량] 열을 대신 사용하여 사용 단위 및 트랜잭션 정보를 가져올 수도 있습니다. 예를 들어 GB 및 시간입니다. 또는 USD, EUR, INR과 같은 다른 통화의 비용 대신 트랜잭션 수를 가져올 수도 있습니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="피벗 테이블 필드에 추가된 열을 보여 주는 예" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. 이제 일반화된 사용량 조사에 대한 대시보드가 있습니다. 피벗 테이블의 필터링 옵션을 사용하여 특정 서비스를 필터링할 수 있습니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="행 레이블에 대한 피벗 테이블 필터 옵션을 보여 주는 예" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    피벗 테이블에서 두 번째 수준(예: 리소스)을 필터링하려면 테이블에서 두 번째 수준 항목을 선택합니다.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="[선택] 필드의 필터 옵션을 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. 추가 필터의 경우 **SubscriptionID** 및 **고객 회사 이름** 을 **필터** 영역에 추가하고 원하는 범위를 선택할 수 있습니다.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>날짜별로 Azure 사용량을 볼 수 있는 피벗 테이블 만들기

이 섹션에서는 사용한 수량 및 날짜에 따른 일반적인 Azure 사용량 문제를 해결할 수 있는 피벗 테이블을 만듭니다. 요금 급증을 날짜 및 서비스 별로 확인하면 도움이 됩니다. 또는 가장 많은 비용이 발생하는 리소스를 확인하고 서비스 요금이 어떻게 청구되는지 살펴볼 수 있습니다.

조정 파일에는 두 개의 테이블이 있습니다. 하나는 문서 맨 위에 있고(기본 테이블), 다른 하나는 문서 맨 아래에 있습니다. 이 두 번째 테이블은 대부분 첫 번째 테이블의 정보와 동일한 정보를 포함하고 있지만, 가격 책정 또는 비용 세부 정보는 포함하지 않습니다. 두 번째 테이블에는 사용 날짜 및 사용한 수량이 포함됩니다.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="두 개의 데이터 테이블이 있는 조정 파일을 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. [데이터를 가져오고 형식 지정](#get-the-data-and-format-it) 섹션의 동일한 단계에 따라 조정 파일 아래쪽의 정보가 들어 있는 Excel 테이블을 만듭니다.
1. 테이블이 준비되고 피벗 테이블 시트가 생성되었으면 [리소스별 Azure 비용을 볼 수 있는 피벗 테이블 만들기] 섹션의 동일한 단계에 따라 대시보드를 준비합니다. 세후 총계를 사용하는 대신 **값** 영역에 **사용한 수량** 을 넣습니다.
1. **사용 날짜** 를 열 섹션에 추가합니다. 피벗 테이블은 다음 예제와 비슷합니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="최종 피벗 테이블 필드를 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. 이제 날짜별 사용량을 보여주는 대시보드가 생겼습니다. **+** 기호를 선택하여 각 월을 확장할 수 있습니다.

대시보드에는 사용된 수량이 GB, 시간, Transfers 등의 단위로 표시됩니다.

일별 금액을 보려면 **행** 영역에 **리소스 GUID** 를 추가하면 됩니다. 상단 테이블에서 리소스의 단가(**ListPrice**)를 추가합니다. **ListPrice** 를 **사용한 수량** 과 곱하여 세후 요금을 계산합니다. 수량이 일치해야 합니다.

일부 리소스(서비스)는 사용한 수량에 따라 계층형 가격 책정이 적용됩니다. 예를 들어 일부 리소스는 최초 100GB까지는 더 높은 요금이 적용되고 그 이후부터는 GB당 가격이 인하됩니다. 비용을 수동으로 계산할 때 계층형 가격 책정을 염두에 두어야 합니다.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>특정 리소스 비용을 볼 수 있는 피벗 테이블 만들기

단일 리소스에서 여러 서비스에 대해 여러 요금이 발생할 수 있습니다. 예를 들어 하나의 가상 머신에서 컴퓨팅 요금, OS 라이선스, 대역폭(데이터 전송), RI 사용량 및 스냅샷용 스토리지에 대한 비용이 발생할 수 있습니다. 특정 리소스의 전체 사용량을 검토하려면 다음 단계에 따라 사용량 파일을 통해 전체 사용량을 볼 수 있는 대시보드를 만들면 됩니다.

조정 파일에는 리소스 관련 세부 정보가 포함되지 않습니다. 따라서 집계된 사용량 파일을 사용합니다. [Azure 청구 지원](https://go.microsoft.com/fwlink/?linkid=2083458)에 연락하여 구독에 대해 집계된 사용량 파일을 제공해 달라고 요청하세요. 집계된 파일은 구독 수준에서 생성됩니다. 형식이 지정되지 않은 데이터는 다음 예제와 비슷합니다.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="형식이 지정되지 않은 집계된 사용량 파일을 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

이 파일에는 다음 열이 포함되어 있습니다.

- **UsageStart** 및 **UsageEnd** - 각 품목(각 사용 단위)의 날짜입니다. 각 날짜를 예로 들 수 있습니다.
- **MeteredResourceID** – Azure에서는 미터 ID에 해당합니다.
- **속성** - 위치와 같은 기타 세부 정보와 함께 인스턴스 ID(리소스 이름)를 포함합니다.
- **수량** - 조정 파일의 사용한 수량입니다.

1. 테이블에서 첫 번째 필드 **PartnerID** 를 선택합니다.  
1. Ctrl + Shift + 아래쪽 화살표를 누른 다음, Ctrl + Shift + 오른쪽 화살표를 눌러 테이블의 모든 정보를 선택합니다.
1. 위쪽 메뉴에서 **삽입** > **테이블** 을 차례로 선택합니다. [테이블 만들기] 상자에서 **머리글 포함** 을 선택한 다음, **확인** 을 선택합니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="테이블 만들기 대화 상자를 보여 주는 예" :::
1. 위쪽 메뉴에서 **삽입** > **피벗 테이블** 을 차례로 선택한 다음, **확인** 을 선택합니다. 파일에 새 시트가 만들어지고, 시트의 오른쪽에 있는 피벗 테이블 영역으로 이동됩니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="조정 파일의 피벗 테이블 필드 영역을 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. 그런 다음, **MeteredResourceID** 를 **행** 영역에 추가하고 **수량** 을 **값** 에 추가합니다. 결과는 전체 사용량 정보를 표시합니다. 자세한 내용을 보려면 **열** 영역에 **UsageEndDateTime** 을 넣습니다.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="전체 사용량 정보를 보여주는 예제" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. 전체 보고서를 보려면 **MeteredResourceID** 아래의 **행** 에 **속성** 을 추가합니다. 이렇게 하면 사용량에 대한 전체 대시보드가 표시됩니다.
1. 특정 리소스로 필터링하려면 **속성** 을 **필터** 영역에 추가하고 원하는 사용량을 선택합니다. 검색을 사용하여 리소스 이름을 찾을 수 있습니다.
    리소스 비용을 확인하려면 총 소비 수량을 확인하고 그 값에 정가를 곱합니다. 정가는 각 리소스 GUID(MeteredResourceID)에만 적용됩니다. 한 리소스에서 여러 MeteredResourceID를 사용하는 경우 각 ID의 총 금액을 확인해야 합니다.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>다음 단계

- [파트너용 Azure Cost Management를 시작합니다](../costs/get-started-partners.md).