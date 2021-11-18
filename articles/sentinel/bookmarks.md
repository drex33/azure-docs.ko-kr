---
title: Microsoft 센티널에서 데이터 조사에 대해 구하기 책갈피 사용
description: 이 문서에서는 Microsoft 센티널 구하기 책갈피를 사용 하 여 데이터를 추적 하는 방법을 설명 합니다.
author: yelevin
ms.author: yelevin
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 1db58f67fbaa364fc796fa5bb1d99a452c0d9f53
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756968"
---
# <a name="keep-track-of-data-during-hunting-with-microsoft-sentinel"></a>Microsoft 센티널을 사용 하 여 데이터 추적 유지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

위협 헌팅을 사용하려면 일반적으로 많은 로그 데이터를 검토하여 악의적인 동작의 증거를 찾아야 합니다. 이 프로세스 중에 조사자는 잠재적인 가설의 유효성을 검사하고 손상의 전체적인 스토리를 파악하는 작업의 일부로 기억하고, 다시 방문하고, 분석하려는 이벤트를 찾습니다.

Microsoft 센티널의 구하기 책갈피를 사용 하면 **Microsoft 센티널 로그** 에서 실행 한 쿼리를 관련이 있는 쿼리 결과와 함께 유지 하 여이 작업을 수행할 수 있습니다. 컨텍스트에 따른 관찰 내용을 기록하고 메모와 태그를 추가하여 결과를 참조할 수도 있습니다. 책갈피가 설정된 데이터는 손쉬운 협업을 위해 본인과 팀 동료가 볼 수 있습니다.

이제 사용자 지정 구하기 쿼리를 MITRE AT&T&헤드 기술에 매핑하여 모든 구하기 쿼리에서 MITRE AT&T&접시 헤드 기술 검사의 간격을 식별 하 고 해결할 수 있습니다.

> [!IMPORTANT]
>
> 책갈피에 대 한 MITRE AT&T&헤드 기술 매핑은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

사용자 지정 쿼리에서 Microsoft 센티널 Analytics에서 지 원하는 전체 엔터티 형식 및 식별자 집합을 매핑하여 책갈피를 사용 하 여 다른 유형의 엔터티를 조사할 수도 있습니다. 이렇게 하면 책갈피를 사용 하 여 [엔터티 페이지](entities.md#entity-pages), [인시던트](investigate-cases.md) 및 [조사 그래프](investigate-cases.md#use-the-investigation-graph-to-deep-dive)를 사용 하 여 검색 쿼리 결과에서 반환 되는 엔터티를 탐색할 수 있습니다. 책갈피가 검색 쿼리에서 결과를 캡처하는 경우 쿼리 MITRE AT&T&접시 헤드 기술 및 엔터티 매핑을 자동으로 상속 합니다.

> [!IMPORTANT]
>
> 책갈피에 대 한 확장 된 엔터티 형식 및 식별자 집합의 매핑은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

로그에서 검색 하는 동안 급하게를 해결 해야 하는 것이 있는 경우 책갈피를 쉽게 만들고,이를 인시던트에 승격 하거나 기존 인시던트에 추가할 수 있습니다. 인시던트에 대 한 자세한 내용은 [Microsoft 센티널로 인시던트 조사](investigate-cases.md)를 참조 하세요.

책갈피를 찾을 수 있지만 바로 긴급 하지 않은 경우에는 언제 든 지 책갈피를 만든 다음, **검색 창의** **책갈피** 탭에서 책갈피 된 데이터를 다시 방문할 수 있습니다. 필터링 및 검색 옵션을 사용하면 현재 진행 중인 조사를 위해 빠르게 특정 데이터를 찾을 수 있습니다.

책갈피 세부 정보에서 **조사** 를 선택 하 여 책갈피가 설정 된 데이터를 시각화할 수 있습니다. 그러면 대화형 엔터티-그래프 다이어그램과 타임라인을 사용하여 결과를 보고, 조사하고, 시각적으로 전달할 수 있는 조사 환경이 시작됩니다.

또는 Log Analytics 작업 영역의 **HuntingBookmark** 테이블에서 직접 책갈피가 지정된 데이터를 볼 수 있습니다. 예를 들면 다음과 같습니다.

:::image type="content" source="media/bookmarks/bookmark-table.png" alt-text="구하기 책갈피 표를 보는 스크린샷" lightbox="media/bookmarks/bookmark-table.png":::

테이블에서 책갈피를 보면 책갈피가 지정된 데이터를 필터링, 요약하고 다른 데이터 원본과 조인하여 보강 증거를 쉽게 찾을 수 있습니다.

## <a name="add-a-bookmark"></a>책갈피 추가

1. Azure Portal에서 **Microsoft 센티널**  >  **Threat management**  >  **구하기** 로 이동 하 여 의심 스러운 비정상 동작에 대 한 쿼리를 실행 합니다.

1. 헌팅 쿼리 중 하나를 선택하고 오른쪽에 있는 헌팅 쿼리 세부 정보에서 **쿼리 실행** 을 선택합니다.

1. **쿼리 결과 보기** 를 선택합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/bookmarks/new-processes-observed-example.png" alt-text="Microsoft 센티널 구하기에서 쿼리 결과를 보는 스크린샷":::

    이 작업은 쿼리 결과를 **로그** 창에서 엽니다.

1. 로그 쿼리 결과 목록에서 확인란을 사용하여 관심 있는 정보를 포함하는 행을 하나 이상 선택합니다.

1. **책갈피 추가** 를 선택합니다.

    :::image type="content" source="media/bookmarks/add-hunting-bookmark.png" alt-text="쿼리에 대해 구하기 책갈피를 추가 하는 스크린샷" lightbox="media/bookmarks/add-hunting-bookmark.png":::

1. 오른쪽에 있는 **책갈피 추가** 창에서 필요에 따라 책갈피 이름을 업데이트하고, 태그를 추가하고, 항목에 관해 흥미로운 것을 식별할 수 있는 메모를 추가합니다.

1. **(미리 보기)** 책갈피는 MITRE AT&T&헤드 기술 또는 하위 기술에 선택적으로 매핑될 수 있습니다. MITRE AT&T&접시 헤드 매핑은 구하기 쿼리의 매핑된 값에서 상속 되지만 수동으로 만들 수도 있습니다. **책갈피 추가** 창의 **전술 & 기술 (미리 보기)** 섹션에 있는 드롭다운 메뉴에서 원하는 기술과 연결 된 MITRE at&t&접시 헤드 기법을 선택 합니다. 메뉴는 MITRE AT&T&접시 헤드 기술을 모두 표시 하도록 확장 되며이 메뉴에서 여러 기술 및 하위 기술을 선택할 수 있습니다.

    :::image type="content" source="media/bookmarks/mitre-attack-mapping.png" alt-text="Mitre 공격 전략 및 기술을 책갈피에 매핑하는 방법의 스크린샷":::

1. **(미리 보기)** 이제 추가 조사를 위해 책갈피가 설정 된 쿼리 결과에서 확장 된 엔터티 집합을 추출할 수 있습니다. **엔터티 매핑 (미리 보기)** 섹션에서 드롭다운을 사용 하 여 [엔터티 형식 및 식별자](entities-reference.md)를 선택 합니다. 그런 다음 해당 식별자가 포함 된 쿼리 결과에 열을 매핑합니다. 다음은 그 예입니다.

    :::image type="content" source="media/bookmarks/map-entity-types-bookmark.png" alt-text="구하기 책갈피에 대 한 엔터티 형식을 매핑하기 위한 스크린샷":::

    조사 그래프에서 책갈피를 보려면 하나 이상의 엔터티를 매핑해야 합니다. 이전 버전과의 호환성을 유지 하면서이 미리 보기 전에 생성 된 계정, 호스트, IP 및 URL 엔터티 형식에 대 한 엔터티 매핑이 계속 지원 됩니다.

1. **저장** 을 클릭하여 변경 내용을 커밋하고 책갈피를 추가합니다. 모든 책갈피가 설정 된 데이터는 다른 분석가와 공유 되며 공동 작업 조사 환경을 위한 첫 번째 단계입니다.

> [!NOTE]
> 로그 쿼리 결과는이 창이 Microsoft 센티널에서 열릴 때마다 책갈피를 지원 합니다. 예를 들어, 탐색 모음에서 **일반** > **로그** 를 선택하거나, 조사 그래프에서 이벤트 링크를 선택하거나, 인시던트의 전체 세부 정보(현재 미리 보기 상태)에서 경고 ID를 선택합니다. **로그** 창이 다른 위치에서 열리면(예: Azure Monitor에서 직접) 책갈피를 만들 수 없습니다.

## <a name="view-and-update-bookmarks"></a>책갈피 보기 및 업데이트

1. Azure Portal에서 **Microsoft 센티널**  >  **Threat management**  >  **구하기** 로 이동 합니다.

2. **책갈피** 탭을 선택하여 책갈피 목록을 봅니다.

3. 특정 책갈피를 찾는 데 도움이 되도록 검색 상자 또는 필터 옵션을 사용합니다.

4. 개별 책갈피를 선택하고 오른쪽 세부 정보 창에서 책갈피 세부 정보를 봅니다.

5. 필요에 따라 변경하면 자동으로 저장됩니다.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>조사 그래프에서 책갈피 검색

1. Azure Portal에서 **Microsoft 센티널**  >  **Threat management**  >  **구하기**  >  **책갈피** 탭으로 이동 하 고 조사할 책갈피 또는 책갈피를 선택 합니다.

2. 책갈피 세부 정보에서 하나 이상의 엔터티가 매핑되었는지 확인합니다.

3. 조사 그래프에서 책갈피를 보려면 **조사** 를 선택 합니다.

조사 그래프를 사용하기 위한 지침은 [조사 그래프를 사용하여 심층 분석](investigate-cases.md#use-the-investigation-graph-to-deep-dive)을 참조하세요.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>새 인시던트 또는 기존 인시던트에 책갈피 추가

1. Azure Portal에서 **Microsoft 센티널**  >  **Threat management**  >  **구하기**  >  **책갈피** 탭으로 이동 하 고 인시던트에 추가 하려는 책갈피 또는 책갈피를 선택 합니다.

2. 명령 모음에서 **인시던트 작업** 을 선택 합니다.

    :::image type="content" source="media/bookmarks/incident-actions.png" alt-text="인시던트에 책갈피를 추가 하는 스크린샷":::

3. 필요에 따라 **새 인시던트를 만들거나** **기존 인시던트에 추가** 를 선택 합니다. 그렇다면

    - 새 인시던트의 경우: 필요에 따라 인시던트의 세부 정보를 업데이트한 후 **만들기** 를 선택합니다.
    - 기존 인시던트에 책갈피를 추가하려면: 하나의 인시던트를 선택한 후 **추가** 를 선택합니다.

인시던트 내에서 책갈피를 보려면 다음을 수행 합니다. **Microsoft 센티널**  >  **위협 관리**  >  **인시던트** 로 이동 하 고 책갈피를 사용 하 여 인시던트를 선택 합니다. **전체 세부 정보 보기** 를 선택한 후 **책갈피** 탭을 선택합니다.

> [!TIP]
> 명령 모음에서 **인시던트 작업** 옵션 대신 하나 이상의 책갈피에 대 한 상황에 맞는 메뉴 (**...**)를 사용 하 여 **새 인시던트를 만들고**, **기존 인시던트에 추가** 하 고, **인시던트에 제거할** 수 있는 옵션을 선택할 수 있습니다.

## <a name="view-bookmarked-data-in-logs"></a>로그에서 책갈피가 지정된 데이터 보기

책갈피가 지정된 쿼리, 결과 또는 해당 기록을 보려면 **헌팅** > **책갈피** 탭에서 책갈피를 선택하고 세부 정보 창에 제공된 링크를 사용합니다.

- **원본 쿼리 보기** - **로그** 창에서 원본 쿼리를 봅니다.

- **책갈피 로그 보기** - 업데이트한 사람, 업데이트된 값, 업데이트가 발생한 시간을 포함하는 모든 책갈피 메타데이터를 확인합니다.

또한 **헌팅** > **책갈피** 탭의 명령 모음에서 **책갈피 로그** 를 선택하여 모든 책갈피의 원시 책갈피 데이터를 볼 수 있습니다.

:::image type="content" source="media/bookmarks/bookmark-logs.png" alt-text="Bookmark logs 명령의 스크린샷":::

이 보기에는 연결된 메타데이터가 있는 모든 책갈피가 표시됩니다. [Kusto Query Language](/azure/data-explorer/kql-quick-reference) (KQL) 쿼리를 사용 하 여 찾으려는 특정 책갈피의 최신 버전으로 필터링 할 수 있습니다.

> [!NOTE]
> 책갈피를 만드는 시간과 책갈피가 **책갈피** 탭에 표시되는 시간 사이에 상당한 지연(분 단위로 측정됨)이 있을 수 있습니다.

## <a name="delete-a-bookmark"></a>책갈피 삭제

1. Azure Portal에서 **Microsoft 센티널**  >  **Threat management**  >  **구하기**  >  **책갈피** 탭으로 이동 하 고 삭제할 책갈피 또는 책갈피를 선택 합니다.

2. 선택 항목을 마우스 오른쪽 단추로 클릭 하 고 선택 된 책갈피의 수를 삭제 하는 옵션을 선택 합니다.

책갈피를 삭제하면 **책갈피** 탭의 목록에서 책갈피가 제거됩니다. Log Analytics 작업 영역의 **HuntingBookmark** 테이블은 이전 책갈피 항목을 계속 포함하지만 최신 항목은 **SoftDelete** 값을 true로 변경하여 이전 책갈피를 쉽게 필터링할 수 있습니다. 책갈피를 삭제해도 다른 책갈피 또는 경고와 연결된 조사 환경의 엔터티가 제거되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널에서 책갈피를 사용 하 여 사냥 조사를 실행 하는 방법을 알아보았습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [Notebook을 사용하여 자동 헌팅 캠페인 실행](notebooks.md)
- [Microsoft 센티널을 사용한 위협 요소 구하기 (모듈 학습)](/learn/modules/hunt-threats-sentinel/)
