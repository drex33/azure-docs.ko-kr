---
title: Azure Sentinel 관심 목록 사용
description: 이 문서에서는 Azure Sentinel 관심 목록을 사용하여 허용 목록/차단 목록을 만들고, 이벤트 데이터를 보강하고, 위협 조사를 보조하는 방법을 설명합니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 07/11/2021
ms.openlocfilehash: ef2afe833af9d5beb1351c6306493e5aed55f907
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083824"
---
# <a name="use-azure-sentinel-watchlists"></a>Azure Sentinel 관심 목록 사용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Sentinel 관심 목록을 사용하면 보유 중인 Azure Sentinel 환경에서 일어나는 이벤트와의 연결을 위해 외부 데이터에서 데이터를 수집할 수 있게 됩니다. 생성한 다음에는 검색, 검색 규칙, 위협 헌팅, 대응 플레이북에 관심 목록을 활용할 수 있습니다. 관심 목록은 Azure Sentinel 작업 영역에 이름 값 쌍으로 저장되며 쿼리 성능 최적화와 대기 시간 최소화를 위해 캐시됩니다.

> [!IMPORTANT]
> 언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

관심 목록을 사용하는 일반적인 시나리오는 다음을 포함합니다.

- IP 주소, 파일 해시, 기타 데이터를 CSV 파일에서 신속하게 가져와 **위협을 조사** 하소 인시던트에 신속하게 대응합니다. 한 번 가져온 뒤부터는 관심 목록을 참가용 이름 값 쌍으로 사용하는 한편, 경고 규칙, 위협 헌팅, 통합 문서, 노트북, 일반 쿼리 관련 필터로 활용할 수 있습니다.

- 관심 목록으로 **비즈니스 데이터 가져오기**. 예를 들어 시스템 액세스 권한이 높은 사용자 목록이나 권한이 종료된 직원 사용자 목록을 가져온 뒤 관심 목록을 사용해 허용 목록 및 차단 목록을 만들고 이를 통해 해당 사용자들이 네트워크에 로그인하는 것을 검색하거나 방지할 수 있습니다.

- **경고 피로 감소**. 일반적으로 경고를 트리거할 수 있는 작업을 수행하는 허가된 IP 주소를 사용하는 사용자와 같이, 사용자 그룹에서 발생하는 경고를 억제하고, 무해한 이벤트로 인해 이러한 경고가 발생하지 않도록 방지하기 위해 허용 목록을 만듭니다.

- **이벤트 데이터 보강**. 관심 목록을 사용해 외부 데이터 소스에서 파생된 이름 값 조합으로 이벤트 데이터를 보강합니다.

> [!NOTE]
> - 관심 목록 사용은 대규모 데이터 볼륨용으로 디자인되지 않았으므로 참조 데이터로 제한되어야 합니다.
>
> - 단일 작업 영역에 있는 모든 관심 목록 간의 **활성 관심 목록 항목의 총 개수** 는 현재 **1,000만 개** 로 제한됩니다. 삭제된 관심 목록 항목은 이 개수에 포함되지 않습니다. 대규모 데이터 볼륨에 대한 참조 기능이 필요하면 대신 [사용자 지정 로그](../azure-monitor/agents/data-sources-custom-logs.md)를 사용하여 수집할 수 있습니다.
>
> - 관심 목록은 동일한 작업 영역 내에서만 참조될 수 있습니다. 교차 작업 영역 및/또는 Lighthouse 시나리오는 현재 지원되지 않습니다.

## <a name="create-a-new-watchlist"></a>새 관심 목록 만들기

1. Azure Portal에서 **Azure Sentinel** > **구성** > **관심 목록** 으로 이동한 다음, **+ 새로 추가** 를 선택합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new.png" alt-text="새 관심 목록" lightbox="./media/watchlists/sentinel-watchlist-new.png":::

1. **일반** 페이지에서, 관심 목록의 이름, 설명, 별칭을 입력한 후 **다음: 원본** 을 선택합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-general.png" alt-text="관심 목록 일반 페이지":::

1. **원본** 페이지에서 데이터 세트 유형(현재 CSV만 사용 가능)을 선택하고, 데이터 파일에서 **헤더 행 앞** 의 줄 수를 입력한 후 두 가지 방법 중 하나로 업로드할 파일을 선택합니다.
    1. **파일 업로드** 상자에서 **파일 찾아보기** 를 클릭하고 업로드할 데이터 파일을 선택합니다.
    1. 데이터 파일을 **파일 업로드** 상자에 끌어서 놓습니다.

    마법사 화면에서 처음 50개 결과 행의 미리 보기가 표시됩니다.

1. **SearchKey** 필드에서 다른 데이터와의 조인 또는 자주 사용되는 검색 개체로 사용할 열 이름을 관심 목록에 입력합니다. 예를 들어 서버 관심 목록에 국가 이름과 그에 따른 두 글자 국가 번호가 포함되어 있고 검색 또는 조인을 위해 해당 국가 번호를 자주 사용할 것으로 예상되면 **코드** 열을 SearchKey로 사용합니다.

1. <a name="review-and-create"></a>**다음: 검토 및 만들기** 를 선택합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="관심 목록 원본 페이지" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 현재 업로드 가능한 파일의 최대 크기는 3.8MB로 제한됩니다.

1. 정보를 검토하고 올바른지 확인하고, *유효성 검사 통과* 메시지를 기다린 후, **만들기** 를 선택합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-review.png" alt-text="관심 목록 검토 페이지":::

    관심 목록이 만들어지면 알림이 표시됩니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="관심 목록 생성 성공 알림" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="create-a-new-watchlist-using-a-template-public-preview"></a>템플릿을 사용하여 새 관심 목록 만들기(공개 미리 보기)

1. Azure Portal에서 **Azure Sentinel** > **구성** > **관심 목록** > **템플릿(미리 보기)** 로 이동합니다.

1. 목록에서 템플릿을 선택하여 오른쪽에 세부 정보를 표시한 후 **템플릿에서 만들기** 를 선택하여 관심 목록을 만듭니다.

    :::image type="content" source="./media/watchlists/create-watchlist-from-template.png" alt-text="기본 제공 템플릿에서 관심 목록을 만듭니다." lightbox="./media/watchlists/create-watchlist-from-template.png":::

1. **관심 목록 마법사** 를 계속합니다.

    - 관심 목록 템플릿을 사용할 때 관심 목록의 **이름**, **설명** 및 **관심 목록 별칭** 값은 모두 읽기 전용입니다.

    - **스키마 다운로드** 를 선택하여 선택한 관심 목록 템플릿에 필요한 관련 스키마가 포함된 CSV 파일을 다운로드합니다.

    각 기본 제공 관심 목록 템플릿에는 템플릿에 연결된 CSV 파일에 나열된 데이터의 고유 집합이 포함됩니다. 자세한 내용은 [기본 제공 관심 목록 스키마](watchlist-schemas.md)를 참조하세요.

1.  CSV 파일의 로컬 버전을 채운 후 이를 다시 마법사에 업로드합니다.

1. [처음부터 새 관심 목록 만들기](#review-and-create)에서와 같이 작업을 계속 진행한 후 [쿼리](#use-watchlists-in-queries) 및 [분석 규칙](#use-watchlists-in-analytics-rules)과 함께 관심 목록을 사용합니다.

## <a name="use-watchlists-in-queries"></a>쿼리에서 관심 목록 사용

> [!TIP]
> 최적의 쿼리 성능을 위해서는 **SearchKey**(관심 목록을 만들 때 정의한 필드)를 쿼리에서 조인 키로 사용합니다. 아래 예제를 참조하세요.

1. Azure Portal에서 **Azure Sentinel** > **구성** > **관심 목록** 으로 이동하여 사용할 관심 목록을 선택한 다음 **Log Analytics에서 보기** 를 선택합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="쿼리에서 관심 목록 사용" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png" :::

1. 관심 목록의 항목은 쿼리에 대해 자동으로 추출되고 **결과** 탭에 표시됩니다. 아래 예제는 **이름** 및 **IP 주소** 필드의 추출 결과를 보여 줍니다. **SearchKey** 는 고유 열로 표시됩니다.

    > [!NOTE]
    > 쿼리의 타임스탬프는 쿼리 UI와 예약된 경고에서 무시됩니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="관심 목록 필드가 있는 쿼리" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::

1. 관심 목록을 조인 및 조회에 대한 테이블로 처리하여 관심 목록 데이터에 대한 테이블의 데이터를 쿼리할 수 있습니다. **SearchKey** 를 조인 키로 사용합니다.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('mywatchlist') 
     on $left.RemoteIPCountry == $right.SearchKey
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="조회로 관심 목록에 대한 쿼리" lightbox="./media/watchlists/sentinel-watchlist-queries-join.png":::

## <a name="use-watchlists-in-analytics-rules"></a>분석 규칙에서 관심 목록 사용

> [!TIP]
> 최적의 쿼리 성능을 위해서는 **SearchKey**(관심 목록을 만들 때 정의한 필드)를 쿼리에서 조인 키로 사용합니다. 아래 예제를 참조하세요.

분석 규칙에서 관심 목록을 사용하려면 Azure Portal에서 **Azure Sentinel** > **구성** > **분석** 로 이동하고 쿼리에서 `_GetWatchlist('<watchlist>')` 함수를 사용하여 규칙을 만듭니다.

1. 이 예에서는 다음 값을 사용하여 “ipwatchlist”라는 관심 목록을 만듭니다.

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="관심 목록에 대한 4가지 항목 목록":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-other.png" alt-text="4가지 항목으로 관심 목록 만들기":::

1. 다음으로 분석 규칙을 만듭니다.  이 예에서는 관심 목록의 IP 주소에서 발생하는 이벤트만 포함합니다.

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    //Use SearchKey for the best performance
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project SearchKey)
    )
    ```

    :::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="분석 규칙에서 관심 목록 사용":::

## <a name="view-list-of-watchlists-aliases"></a>관심 목록 별칭 목록 보기

관심 목록 별칭 목록을 가져오려면 Azure Portal에서 **Azure Sentinel** > **일반** > **로그** 로 이동하고 다음 쿼리를 실행합니다. `_GetWatchlistAlias`. **결과** 탭에서 별칭 목록을 볼 수 있습니다.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-alias.png" alt-text="관심 목록" lightbox="./media/watchlists/sentinel-watchlist-alias.png":::

## <a name="manage-your-watchlist-in-the-azure-sentinel-portal"></a>Azure Sentinel 포털에서 관심 목록 관리

Azure Sentinel 포털의 관심 목록 블레이드에서 직접 관심 목록 항목을 보고, 편집하고, 새로 만들 수 있습니다.

1. 관심 목록을 편집하려면 **Azure Sentinel > 구성 > 관심 목록** 으로 이동하고, 편집하려는 관심 목록을 선택하고, 세부 정보 창에서 **관심 목록 항목 편집** 을 선택합니다.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit.png" alt-text="관심 목록 편집 방법을 보여주는 스크린샷" lightbox="./media/watchlists/sentinel-watchlist-edit.png":::

1. 기존 관심 목록 항목을 편집하려면 해당 관심 목록 항목의 확인란을 표시하고, 항목을 편집한 후 **저장** 을 선택합니다. 확인 프롬프트에서 **예** 를 선택합니다.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-change.png" alt-text="관심 목록 항목 표시 및 편집 방법을 보여주는 스크린샷입니다.":::

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-confirm.png" alt-text="변경 내용을 확인하는 스크린샷입니다.":::

1. 관심 목록에 새 항목을 추가하려면 **관심 목록 항목 편집** 화면에서 **새로 추가** 를 선택하고, **관심 목록 항목 추가** 패널의 필드를 입력한 후 해당 패널 아래에서 **추가** 를 선택합니다.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-add.png" alt-text="관심 목록에 새 항목을 추가하는 방법을 보여주는 스크린샷입니다.":::

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel 관심 목록을 사용하여 데이터를 보강하고 조사를 개선하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
