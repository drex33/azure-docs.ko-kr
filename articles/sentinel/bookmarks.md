---
title: Azure Sentinel에서 데이터 조사에 헌팅 책갈피 사용
description: 이 문서에서는 Azure Sentinel 헌팅 책갈피를 사용하여 데이터를 추적하는 방법을 설명합니다.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.date: 10/24/2019
ms.openlocfilehash: 661a02a15b7acb53ab496c1f5fcf9a9ab8c04205
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060766"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Azure Sentinel을 사용하여 헌팅하는 동안 데이터 추적 유지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

위협 헌팅을 사용하려면 일반적으로 많은 로그 데이터를 검토하여 악의적인 동작의 증거를 찾아야 합니다. 이 프로세스 중에 조사자는 잠재적인 가설의 유효성을 검사하고 손상의 전체적인 스토리를 파악하는 작업의 일부로 기억하고, 다시 방문하고, 분석하려는 이벤트를 찾습니다.

Azure Sentinel의 헌팅 책갈피를 사용하면 관련성이 있는 것으로 간주하는 쿼리 결과와 함께 **Azure Sentinel - 로그** 에서 실행한 쿼리를 유지하여 이 작업을 수행할 수 있습니다. 컨텍스트에 따른 관찰 내용을 기록하고 메모와 태그를 추가하여 결과를 참조할 수도 있습니다. 책갈피가 설정된 데이터는 손쉬운 협업을 위해 본인과 팀 동료가 볼 수 있습니다.

언제든지 **헌팅** 창의 **책갈피** 탭에서 책갈피가 지정된 데이터를 다시 방문할 수 있습니다. 필터링 및 검색 옵션을 사용하면 현재 진행 중인 조사를 위해 빠르게 특정 데이터를 찾을 수 있습니다. 또는 Log Analytics 작업 영역의 **HuntingBookmark** 테이블에서 직접 책갈피가 지정된 데이터를 볼 수 있습니다. 예를 들면 다음과 같습니다.

> [!div class="mx-imgBorder"]
> ![HuntingBookmark 테이블 보기](./media/bookmarks/bookmark-table.png)

테이블에서 책갈피를 보면 책갈피가 지정된 데이터를 필터링, 요약하고 다른 데이터 원본과 조인하여 보강 증거를 쉽게 찾을 수 있습니다.

현재 미리 보기 상태에서는 로그에서 헌팅하는 동안 긴급하게 처리해야 하는 어떤 것을 찾은 경우 몇 번 클릭으로 책갈피를 만들고 인시던트로 승격하거나 기존 인시던트에 책갈피를 추가할 수 있습니다. 인시던트에 관한 자세한 내용은 [자습서: Azure Sentinel을 사용하여 인시던트 조사](investigate-cases.md)를 참조하세요. 

또한 미리 보기에서는 책갈피 세부 정보에서 **조사** 를 클릭하여 책갈피가 지정된 데이터를 시각화할 수 있습니다. 그러면 대화형 엔터티-그래프 다이어그램과 타임라인을 사용하여 결과를 보고, 조사하고, 시각적으로 전달할 수 있는 조사 환경이 시작됩니다.

## <a name="add-a-bookmark"></a>책갈피 추가

1. Azure Portal에서 **Sentinel** > **위협 관리** > **헌팅** 으로 이동하여 의심스럽고 비정상적인 동작에 대한 쿼리를 실행합니다.

2. 헌팅 쿼리 중 하나를 선택하고 오른쪽에 있는 헌팅 쿼리 세부 정보에서 **쿼리 실행** 을 선택합니다. 

3. **쿼리 결과 보기** 를 선택합니다. 예를 들면 다음과 같습니다.
    
    > [!div class="mx-imgBorder"]
    > ![Azure Sentinel 헌팅에서 쿼리 결과 보기](./media/bookmarks/new-processes-observed-example.png)
    
    이 작업은 쿼리 결과를 **로그** 창에서 엽니다.

4. 로그 쿼리 결과 목록에서 확인란을 사용하여 관심 있는 정보를 포함하는 행을 하나 이상 선택합니다.

5. **책갈피 추가** 를 선택합니다.
    
    > [!div class="mx-imgBorder"]
    > ![쿼리에 헌팅 책갈피 추가](./media/bookmarks/add-hunting-bookmark.png)

6. 오른쪽에 있는 **책갈피 추가** 창에서 필요에 따라 책갈피 이름을 업데이트하고, 태그를 추가하고, 항목에 관해 흥미로운 것을 식별할 수 있는 메모를 추가합니다.

7. **쿼리 정보** 섹션에서 드롭다운 상자를 사용하여 **계정**, **호스트** 및 **IP 주소** 엔터티 형식에 대한 정보를 쿼리 결과에서 추출합니다. 이 작업은 선택한 엔터티 형식을 쿼리 결과의 특정 열에 매핑합니다. 예를 들면 다음과 같습니다.
    
    > [!div class="mx-imgBorder"]
    > ![헌팅 책갈피에 대한 엔터티 형식 매핑](./media/bookmarks/map-entity-types-bookmark.png)
    
    조사 그래프(현재 미리 보기 상태)에서 책갈피를 보려면 **계정,** **호스트** 또는 **IP 주소** 엔터티 형식 중 하나 이상을 매핑해야 합니다. 

5. **저장** 을 클릭하여 변경 내용을 커밋하고 책갈피를 추가합니다. 모든 책갈피가 지정된 데이터는 다른 조사자와 공유되며 협업 조사 환경을 위한 첫 번째 단계입니다.

 
> [!NOTE]
> 로그 쿼리 결과는 Azure Sentinel에서 이 창이 열릴 때마다 책갈피를 지원합니다. 예를 들어, 탐색 모음에서 **일반** > **로그** 를 선택하거나, 조사 그래프에서 이벤트 링크를 선택하거나, 인시던트의 전체 세부 정보(현재 미리 보기 상태)에서 경고 ID를 선택합니다. **로그** 창이 다른 위치에서 열리면(예: Azure Monitor에서 직접) 책갈피를 만들 수 없습니다.

## <a name="view-and-update-bookmarks"></a>책갈피 보기 및 업데이트 

1. Azure Portal에서 **Sentinel** > **위협 관리** > **헌팅** 으로 이동합니다. 

2. **책갈피** 탭을 선택하여 책갈피 목록을 봅니다.

3. 특정 책갈피를 찾는 데 도움이 되도록 검색 상자 또는 필터 옵션을 사용합니다.

4. 개별 책갈피를 선택하고 오른쪽 세부 정보 창에서 책갈피 세부 정보를 봅니다.

5. 필요에 따라 변경하면 자동으로 저장됩니다.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>조사 그래프에서 책갈피 검색

> [!IMPORTANT]
> 조사 그래프의 책갈피와 조사 그래프 자체를 검색하는 기능은 퍼블릭 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

1. Azure Portal에서 **Sentinel** > **위협 관리** > **헌팅** > **책갈피** 탭으로 이동하고 조사하려는 책갈피를 선택합니다.

2. 책갈피 세부 정보에서 하나 이상의 엔터티가 매핑되었는지 확인합니다. 예를 들어, **엔터티** 의 경우 **IP**, **머신** 또는 **계정** 의 항목이 표시됩니다.

3. **조사** 를 클릭하여 조사 그래프의 책갈피를 봅니다.

조사 그래프를 사용하기 위한 지침은 [조사 그래프를 사용하여 심층 분석](investigate-cases.md#use-the-investigation-graph-to-deep-dive)을 참조하세요.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>새 인시던트 또는 기존 인시던트에 책갈피 추가

> [!IMPORTANT]
> 새 인시던트나 기존 인시던트에 책갈피를 추가하는 기능은 현재 퍼블릭 미리 보기 상태입니다.
> 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

1. Azure Portal에서 **Sentinel** > **위협 관리** > **헌팅** > **책갈피** 탭으로 이동하고 인시던트에 추가하려는 책갈피를 선택합니다.

2. 명령 모음에서 **인시던트 작업(미리 보기)** 을 선택합니다.
    
    > [!div class="mx-imgBorder"]
    > ![인시던트에 책갈피 추가](./media/bookmarks/incident-actions.png)

3. 필요에 따라 **새 인시던트 만들기** 또는 **기존 인시던트에 추가** 를 선택합니다. 그렇다면
    
    - 새 인시던트의 경우: 필요에 따라 인시던트의 세부 정보를 업데이트한 후 **만들기** 를 선택합니다.
    - 기존 인시던트에 책갈피를 추가하려면: 하나의 인시던트를 선택한 후 **추가** 를 선택합니다. 

인시던트 내에서 책갈피를 보려면: **Sentinel** > **위협 관리** > **인시던트** 로 이동하고 책갈피가 있는 인시던트를 선택합니다. **전체 세부 정보 보기** 를 선택한 후 **책갈피** 탭을 선택합니다.

> [!TIP]
> 명령 모음의 **인시던트 작업(미리 보기)** 옵션 대신, 하나 이상의 책갈피에 대한 상황에 맞는 메뉴( **...** )를 사용하여 **새 인시던트 만들기**, **기존 인시던트에 추가** 및 **인시던트에서 제거** 옵션을 선택합니다. 

## <a name="view-bookmarked-data-in-logs"></a>로그에서 책갈피가 지정된 데이터 보기

책갈피가 지정된 쿼리, 결과 또는 해당 기록을 보려면 **헌팅** > **책갈피** 탭에서 책갈피를 선택하고 세부 정보 창에 제공된 링크를 사용합니다. 

- **원본 쿼리 보기** - **로그** 창에서 원본 쿼리를 봅니다.

- **책갈피 로그 보기** - 업데이트한 사람, 업데이트된 값, 업데이트가 발생한 시간을 포함하는 모든 책갈피 메타데이터를 확인합니다.

또한 **헌팅** > **책갈피** 탭의 명령 모음에서 **책갈피 로그** 를 선택하여 모든 책갈피의 원시 책갈피 데이터를 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![책갈피 로그](./media/bookmarks/bookmark-logs.png)

이 보기에는 연결된 메타데이터가 있는 모든 책갈피가 표시됩니다. [KQL](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference)(Keyword Query Language) 쿼리를 사용하여 찾으려는 특정 책갈피의 최신 버전으로 필터링할 수 있습니다.

> [!NOTE]
> 책갈피를 만드는 시간과 책갈피가 **책갈피** 탭에 표시되는 시간 사이에 상당한 지연(분 단위로 측정됨)이 있을 수 있습니다.

## <a name="delete-a-bookmark"></a>책갈피 삭제
 
1.  Azure Portal에서 **Sentinel** > **위협 관리** > **헌팅** > **책갈피** 탭으로 이동하고 삭제하려는 책갈피를 선택합니다. 

2. 선택 항목을 마우스 오른쪽 단추로 클릭하고 책갈피를 삭제하는 옵션을 선택합니다. 예를 들어, 책갈피를 하나만 선택한 경우 **책갈피 삭제** 를 선택하고, 두 개의 책갈피를 선택한 경우 **두 개의 책갈피 삭제** 를 선택합니다.
    
책갈피를 삭제하면 **책갈피** 탭의 목록에서 책갈피가 제거됩니다. Log Analytics 작업 영역의 **HuntingBookmark** 테이블은 이전 책갈피 항목을 계속 포함하지만 최신 항목은 **SoftDelete** 값을 true로 변경하여 이전 책갈피를 쉽게 필터링할 수 있습니다. 책갈피를 삭제해도 다른 책갈피 또는 경고와 연결된 조사 환경의 엔터티가 제거되지 않습니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 책갈피를 사용하여 헌팅 조사를 실행하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.


- [위협 요소를 사전에 헌팅하기](hunting.md)
- [Notebook을 사용하여 자동 헌팅 캠페인 실행](notebooks.md)
