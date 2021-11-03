---
title: Azure Sentinel 데이터에서 Power BI 보고서 만들기
description: Azure Sentinel Log Analytics에서 내보낸 쿼리를 사용하여 Power BI 보고서를 만드는 방법에 대해 알아봅니다. Power BI 서비스 및 Teams 채널의 다른 사용자와 보고서를 공유합니다.
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 558d6d979321148923f8a10bc2f92bb13eedd746
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023047"
---
# <a name="tutorial-create-a-power-bi-report-from-azure-sentinel-data"></a>자습서: Azure Sentinel 데이터에서 Power BI 보고서 만들기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Power BI](https://powerbi.microsoft.com/)는 데이터를 몰입형의 일관된 대화형 시각화로 전환하는 보고 및 분석 플랫폼입니다. Power BI를 사용하면 데이터 원본에 쉽게 연결하고, 관계를 시각화 및 발견하고, 원하는 모든 사람과 통찰력을 공유할 수 있습니다.

Azure Sentinel Log Analytics 작업 영역의 데이터를 바탕으로 Power BI 보고서를 작성하고, Azure Sentinel에 액세스할 수 없는 사용자와 해당 보고서를 공유할 수 있습니다. 예를 들어, Azure Sentinel 액세스 권한을 부여하지 않고 실패한 로그인 시도에 대한 정보를 앱 소유자와 공유할 수 있습니다. Power BI 시각화는 데이터를 한 눈에 파악할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Log Analytics Kusto 쿼리를 Power BI M 언어 쿼리로 내보냅니다.
> * Power BI Desktop에서 M 쿼리를 사용하여 시각화 및 보고서를 만들 수 있습니다.
> * Power BI 서비스에 보고서를 게시하고 다른 사용자와 공유합니다.
> * Teams 채널에 보고서를 추가합니다.

Power BI 서비스에서 액세스 권한을 부여한 사람과 Teams 채널의 구성원들은 Azure Sentinel 사용 권한이 없어도 보고서를 볼 수 있습니다.

> [!NOTE]
> 이 자습서에서는 Azure Sentinel 데이터에 대한 PowerBI의 분석 보고서 보기와 같은 주요 고객 요청에 대한 시나리오 기반 절차를 제공합니다. 자세한 내용은 [데이터 원본 연결](connect-data-sources.md) 및 [수집된 데이터 시각화](get-visibility.md)를 참조하세요.
>
## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

- 로그인 시도를 모니터링하는 Azure Sentinel Log Analytics 작업 영역에 대한 최소한의 읽기 액세스 권한.
- Log Analytics 작업 영역에 대한 읽기 권한이 있는 Power BI 계정.
- [Microsoft Store에서 설치한 Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="export-a-query-from-log-analytics"></a>Log Analytics에서 쿼리 내보내기

Azure Sentinel Log Analytics 작업 영역에서 Kusto 쿼리를 만들고 실행하고 내보냅니다. 

1. 간단한 쿼리를 만들려면 Azure Sentinel Log Analytics 작업 영역에서 **로그** 를 선택합니다. 쿼리 편집기의 **새 쿼리 1** 에서 다음 Kusto 쿼리를 입력합니다.
   
   ```kusto
   SigninLogs
   |  where TimeGenerated >ago(7d)
   | summarize Attempts = count(), Failed=countif(ResultType !=0), Succeeded = countif(ResultType ==0) by AppDisplayName
   |  top 10 by Failed
   | sort by Failed
   ```
   
   또는 선호하는 Azure Sentinel Log Analytics Kusto 쿼리를 사용합니다.
   
1. **실행** 을 선택하여 쿼리를 실행하고 결과를 생성합니다.
   
   :::image type="content" source="media/powerbi/query.png" alt-text="Log Analytics Kusto 쿼리 및 결과를 보여 주는 스크린샷.":::
   
1. 쿼리를 Power BI M 쿼리 형식으로 내보내려면 **내보내기** 를 선택한 다음, **Power BI로 내보내기(M 쿼리)** 를 선택합니다. Log Analytics에서 *PowerBIQuery.txt* 라는 텍스트 파일로 쿼리를 내보냅니다.
   
   :::image type="content" source="media/powerbi/export.png" alt-text="Power BI M 형식으로 쿼리 내보내기를 보여 주는 스크린샷.":::
   
1. 내보낸 파일의 콘텐츠를 복사합니다.

## <a name="get-the-data-in-power-bi-desktop"></a>Power BI Desktop에서 데이터 가져오기

Power BI Desktop에서 내보낸 M 쿼리를 실행하여 데이터를 가져옵니다.

1. Power BI Desktop을 열고 Log Analytics 작업 영역에 대한 읽기 권한이 있는 Power BI 계정에 로그인합니다.
   
   :::image type="content" source="media/powerbi/sign-in.png" alt-text="Power BI Desktop에 대한 로그인을 보여 주는 스크린샷.":::
   
1. Power BI 리본에서 **데이터 가져오기** 를 선택한 다음, **빈 쿼리** 를 선택합니다. **파워 쿼리 편집기** 가 열립니다.
   
   :::image type="content" source="media/powerbi/blank-query.png" alt-text="Power BI Desktop의 데이터 가져오기에서 선택한 빈 쿼리를 보여 주는 스크린샷.":::
   
1. **파워 쿼리 편집기** 에서 **고급 편집기** 를 선택합니다.
   
1. 내보낸 *PowerBIQuery.txt* 파일의 복사한 콘텐츠를 **고급 편집기** 창에 붙여넣은 다음, **완료** 를 선택합니다.
   
   :::image type="content" source="media/powerbi/advanced-editor.png" alt-text="Power BI 고급 편집기에 붙여넣은 M 쿼리를 보여 주는 스크린샷.":::
   
1. **파워 쿼리 편집기** 에서 쿼리의 이름을 *App_signin_stats* 로 바꾼 다음, **닫기 및 적용** 을 선택합니다.
   
   :::image type="content" source="media/powerbi/close-apply.png" alt-text="Power Query 편집기에서 이름이 바뀐 쿼리 및 닫기 및 적용 명령을 보여 주는 스크린샷.":::

## <a name="create-visualizations-from-the-data"></a>데이터에서 시각화 만들기

이제 데이터가 Power BI에 있으므로 데이터에 대한 인사이트를 제공하는 시각화를 만들 수 있습니다.

### <a name="create-a-table-visual"></a>테이블 시각적 개체 만들기

먼저 쿼리 결과를 모두 표시하는 테이블을 만듭니다.

1. Power BI Desktop 캔버스에 테이블 시각화를 추가하려면 **시각화** 아래에 있는 **테이블** 아이콘을 선택합니다.
   
   :::image type="content" source="media/powerbi/table.png" alt-text="Power BI Desktop의 시각화 아래에 있는 테이블 아이콘을 보여 주는 스크린샷.":::
   
1. **필드** 에서 쿼리의 모든 필드를 선택하여 테이블에 모두 표시되도록 합니다. 테이블에 모든 데이터가 표시되지 않는 경우 해당 선택 핸들을 끌어 테이블을 확대합니다.
   
   :::image type="content" source="media/powerbi/select-fields.png" alt-text="테이블 시각화에 대해 선택한 모든 필드를 보여 주는 스크린샷.":::
   
### <a name="create-a-pie-chart"></a>원형 차트 만들기

다음으로 실패한 로그인 시도가 가장 많은 애플리케이션을 보여 주는 원형 차트를 만듭니다.

1. 바깥쪽을 클릭하거나 탭하여 테이블 시각적 개체를 선택 취소한 다음, **시각화** 에서 **원형 차트** 아이콘을 선택합니다.
   
   :::image type="content" source="media/powerbi/pie-chart.png" alt-text="Power BI Desktop의 시각화 아래에 있는 원형 차트 아이콘을 보여 주는 스크린샷.":::
   
1. **범례** 웰에서 **AppDisplayName** 을 선택하거나 **필드** 창에서 끌어옵니다. **값** 웰에서 **실패** 를 선택하거나 **필드** 에서 끌어옵니다. 이제 원형 차트에는 애플리케이션당 실패한 로그인 시도 횟수가 표시됩니다.
   
   :::image type="content" source="media/powerbi/failed.png" alt-text="애플리케이션당 실패한 로그인 시도 횟수가 포함된 원형 차트를 보여 주는 스크린샷.":::
   
### <a name="create-a-new-quick-measure"></a>새 빠른 측정 만들기

각 애플리케이션에 대해 실패한 로그인 시도의 백분율도 표시하려고 합니다. 쿼리에 백분율 열이 없기 때문에 이 정보를 표시하는 새 측정값을 만들 수 있습니다.

1. **시각화** 에서 **누적 세로 막대형 차트** 아이콘을 선택하여 누적 세로 막대형 차트를 만듭니다.
   
   :::image type="content" source="media/powerbi/column-chart.png" alt-text="Power BI Desktop의 시각화 아래에 있는 누적 세로 막대형 차트 아이콘을 보여 주는 스크린샷.":::
   
1. 새 시각화가 선택된 상태에서 리본에 있는 **빠른 측정** 을 선택합니다.
   
1. **빠른 측정** 창의 **계산** 에서 **나누기** 를 선택합니다. **실패** 를 **필드** 에서 **분자** 필드로 끌고, **시도** 를 **필드** 에서 **분모** 로 끌어옵니다.
   
   :::image type="content" source="media/powerbi/quick-measures.png" alt-text="빠른 측정 창의 설정을 보여 주는 스크린샷.":::
   
1. **확인** 을 선택합니다. 새 측정값이 **필드** 창에 나타납니다.
   
1. **필드** 창에서 새 측정값을 선택하고 리본의 **서식 지정** 아래에서 **백분율** 을 선택합니다.
   
   :::image type="content" source="media/powerbi/percentage.png" alt-text="필드 창에서 선택한 새 측정값 및 리본의 서식 지정에서 선택한 백분율을 보여 주는 스크린샷.":::
   
1. 캔버스에서 선택한 세로 막대형 차트 시각화를 사용하여 **AppDisplayName** 필드를 **축** 웰로, 새 **실패한 것을 시도로 나누기** 측정값을 **값** 웰로 선택하거나 끌어옵니다. 이제 차트에 각 애플리케이션에 대해 실패한 로그인 시도의 백분율이 표시됩니다.
   
   :::image type="content" source="media/powerbi/failed-percentage.png" alt-text="각 애플리케이션에 대해 실패한 시도의 백분율이 포함된 세로 막대형 차트를 보여 주는 스크린샷.":::
   
### <a name="refresh-the-data-and-save-the-report"></a>데이터 새로 고침 및 보고서 저장

1. Azure Sentinel에서 최신 데이터를 가져오려면 **새로 고침** 을 선택합니다.
   
   :::image type="content" source="media/powerbi/refresh.png" alt-text="리본에서 새로 고침 단추를 보여 주는 스크린샷.":::
   
1. **파일** > **저장** 을 선택하고 Power BI 보고서를 저장합니다.

## <a name="create-a-power-bi-online-workspace"></a>Power BI 온라인 작업 영역 만들기

보고서를 공유하기 위한 Power BI 작업 영역을 만들려면 다음을 수행합니다.

1. Power BI Desktop 및 Azure Sentinel 읽기 액세스에 사용한 것과 동일한 계정으로 [powerbi.com](https://powerbi.com)에 로그인합니다.
   
1. **작업 영역** 에서 **작업 영역 만들기** 를 선택합니다. 작업 영역의 이름을 *관리 보고서* 로 지정하고 **저장** 을 선택합니다.
   
   :::image type="content" source="media/powerbi/create-workspace.png" alt-text="Power BI 서비스에서 작업 영역 만들기를 보여 주는 스크린샷.":::
   
1. 사용자 및 그룹에 작업 영역에 대한 액세스 권한을 부여하려면 새 작업 영역 이름 옆에 있는 **기타 옵션** 점을 선택한 다음, **작업 영역 액세스** 를 선택합니다.
   
   :::image type="content" source="media/powerbi/workspace-access.png" alt-text="작업 영역 추가 옵션 메뉴의 작업 영역 액세스를 보여 주는 스크린샷.":::
   
1. **작업 영역 액세스** 쪽 창에서 사용자의 이메일 주소를 추가하고 각 사용자에게 역할을 할당할 수 있습니다. 역할에는 관리자, 구성원, 기여자 및 시청자가 있습니다.

## <a name="publish-the-power-bi-report"></a>Power BI 보고서 게시

이제 Power BI Desktop을 사용하여 다른 사람이 볼 수 있도록 Power BI 보고서를 게시할 수 있습니다.

1. Power BI Desktop의 새 보고서에서 **게시** 를 선택합니다.
   
   :::image type="content" source="media/powerbi/publish.png" alt-text="Power BI Desktop 리본의 게시를 보여 주는 스크린샷.":::
   
1. 게시할 **관리 보고서** 작업 영역을 선택하고 **선택** 을 선택합니다.
   
   :::image type="content" source="media/powerbi/select-workspace.png" alt-text="게시할 Power BI 관리 보고서 작업 영역의 선택을 보여 주는 스크린샷.":::
   
## <a name="import-the-report-to-a-microsoft-teams-channel"></a>Microsoft Teams 채널로 보고서 가져오기

이제 관리 Teams 채널의 구성원이 보고서를 볼 수 있도록 하려고 합니다. Teams 채널에 보고서를 추가하려면 다음을 수행합니다.

1. 관리 Teams 채널에서 **+** 를 선택하여 탭을 추가하고, **탭 추가** 창에서 **Power BI** 를 검색하여 선택합니다.

   :::image type="content" source="media/powerbi/add-tab.png" alt-text="Teams의 탭 추가 창에서 Power BI 선택을 보여 주는 스크린샷.":::
   
1. Power BI 보고서 목록에서 새 보고서를 선택하고 **저장** 을 선택합니다. 보고서가 Teams 채널의 새 탭에 나타납니다.
   
   :::image type="content" source="media/powerbi/teams.png" alt-text="Teams 채널의 탭에 Power BI 보고서를 보여 주는 스크린샷.":::

## <a name="schedule-report-refresh"></a>보고서 새로 고침 예약

업데이트된 데이터가 항상 보고서에 표시되도록 일정에 따라 Power BI 보고서를 새로 고칩니다.

1. Power BI 서비스에서 보고서를 게시한 작업 영역을 선택합니다.
   
1. 보고서의 데이터 세트 옆에 있는 **기타 옵션** > **설정** 을 선택합니다.
   
   :::image type="content" source="media/powerbi/settings.png" alt-text="Power BI 보고서 데이터 세트의 추가 옵션 아래에 있는 설정을 보여 주는 스크린샷.":::
   
1. **자격 증명 편집** 을 선택하여 Log Analytics 작업 영역에 대한 읽기 권한이 있는 계정에 대한 자격 증명을 제공합니다.
   
1. **예약된 새로 고침** 에서 슬라이더를 **켜기** 로 설정하고 보고서에 대한 새로 고침 일정을 설정합니다.
   
   :::image type="content" source="media/powerbi/schedule.png" alt-text="Power BI 보고서 데이터 세트에 대한 예약된 새로 고침 설정을 보여 주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그 데이터를 Power BI로 가져오기](../azure-monitor/logs/log-powerbi.md)
- [파워 쿼리 M 수식 언어](/powerquery-m/)
