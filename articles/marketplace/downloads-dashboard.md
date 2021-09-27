---
title: 파트너 센터의 Microsoft 상업용 Marketplace 분석에서 다운로드 대시보드 - Azure Marketplace
description: Marketplace 제품에 대한 다운로드 요청에 액세스하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: shganesh-dev
ms.author: shganesh
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: 34d8746939b1ec4a957d72b089541eb6b5c0b5dd
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080231"
---
# <a name="downloads-dashboard-in-commercial-marketplace-analytics"></a>상업용 Marketplace 분석의 다운로드 대시보드

이 문서에서는 파트너 센터의 다운로드 대시보드에 대한 정보를 제공합니다. 이 대시보드에는 지난 30일 동안의 다운로드 요청 목록이 표시됩니다.

>[!NOTE]
> 분석 용어에 대한 자세한 정의는 [상업용 마켓플레이스 분석에 대한 자주 묻는 질문과 용어](analytics-faq.yml)를 참조하세요.

## <a name="downloads-dashboard"></a>다운로드 대시보드

[다운로드 대시보드](https://go.microsoft.com/fwlink/?linkid=2165766)에는 1000개 이상의 고객 또는 주문 데이터 행이 포함된 다운로드 요청이 표시됩니다.

1000개 이상의 데이터 행이 포함된 다운로드를 요청할 때마다 **다운로드** 대시보드 링크가 포함된 팝업 알림을 받게 됩니다. 이러한 데이터 다운로드는 30일 동안 사용할 수 있으며 이후 제거됩니다.

## <a name="access-the-downloads-dashboard"></a>다운로드 대시보드에 액세스

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 홈 페이지에서 **Insights** 타일을 선택합니다.

    [![파트너 센터 홈 페이지의 Insights 타일을 보여 줍니다. ](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 왼쪽 메뉴에서 **다운로드를** 선택합니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색에서 상업용 **Marketplace**  >  **분석**  >  **다운로드를** 선택합니다.

---

## <a name="lifetime-export-of-commercial-marketplace-analytics-reports"></a>상업용 Marketplace 분석 보고서의 수명 내보내기

다운로드 페이지에서 최종 사용자는 다음을 수행할 수 있습니다.

- 상업용 Marketplace 분석 보고서의 수명 내보내기(csv 및 tsv 형식)
- 모든 날짜 범위에 대한 상업용 Marketplace 분석 보고서 내보내기
- 6개월 또는 12개월 동안 상업용 Marketplace 분석 보고서 내보내기

분석 보고서의 수명 내보내기 기능 지원:

| 보고서 | 수명 내보내기 | 날짜를 기준으로 하는 모든 기간 |
| - | - | - |
| Orders | ![녹색 확인 표시](media/downloads-dashboard/check-green-yes.png) | ![녹색 확인 표시](media/downloads-dashboard/check-green-yes.png) |
| 고객 | ![녹색 확인 표시](media/downloads-dashboard/check-green-yes.png) | ![녹색 확인 표시](media/downloads-dashboard/check-green-yes.png) |
| Marketplace 인사이트 | ![녹색 확인 표시](media/downloads-dashboard/check-green-yes.png) | ![녹색 확인 표시](media/downloads-dashboard/check-green-yes.png) |
| 사용 | ![검정 X 표시](media/downloads-dashboard/check-black-no.png) | 최대 1년 |
|

사용자는 다운로드 대시보드에서 보고서의 비동기 다운로드를 예약할 수 있습니다.

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

[![다운로드 페이지에서 보고서의 비동기 다운로드 예약](media/downloads-dashboard/download-reports-workspaces.png)](media/downloads-dashboard/download-reports.png#lightbox)

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

[![다운로드 섹션에서 보고서의 비동기 다운로드 예약](media/downloads-dashboard/download-reports.png)](media/downloads-dashboard/download-reports.png#lightbox)

---

## <a name="next-steps"></a>다음 단계

- 파트너 센터 상업용 마켓플레이스에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터의 상업용 마켓플레이스에 대한 분석](analytics.md)을 참조하세요.
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 마켓플레이스 분석의 요약 대시보드](summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능 형식으로 주문 정보를 보려면 [상업용 마켓플레이스 분석의 주문 대시보드](orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금제 청구 메트릭은 [상업용 마켓플레이스 분석의 사용량 대시보드](usage-dashboard.md)를 참조하세요.
- 증가 추세를 비롯한 고객에 대한 자세한 내용은 [상업용 마켓플레이스 분석의 고객 대시보드](customer-dashboard.md)를 참조하세요.
- Azure Marketplace 및 AppSource의 제품에 대한 고객 의견의 통합 보기를 보려면 [상업용 Marketplace 분석의 평가 및 검토 대시보드](ratings-reviews.md)를 참조하세요.
- 상업용 마켓플레이스 분석 및 데이터 용어의 종합 사전에 대한 자주 묻는 질문은 [상업용 마켓플레이스 분석에 대한 자주 묻는 질문과 용어](analytics-faq.yml)를 참조하세요.
