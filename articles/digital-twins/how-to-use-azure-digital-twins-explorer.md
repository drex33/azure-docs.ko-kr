---
title: Azure Digital Twins Explorer 사용
titleSuffix: Azure Digital Twins
description: Azure Digital Twins Explorer의 기능을 사용하는 방법 이해
author: baanders
ms.author: baanders
ms.date: 10/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 374fb9c6ea82b368bbebe6f8c833a4c984a11f29
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243510"
---
# <a name="use-azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer 사용(미리 보기)

[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)는 Azure Digital Twins를 시각화하고 작업하는 데 사용되는 도구입니다. 이 문서에서는 Azure Digital Twins Explorer의 기능과 이를 사용하여 Azure Digital Twins 인스턴스의 데이터를 관리하는 방법을 설명합니다. 클릭 또는 바로 가기 키를 사용하여 Azure Digital Twins Explorer 상호 작용할 수 [있습니다.](#accessibility-and-advanced-settings)

>[!NOTE]
>이 도구는 현재 **공개 미리 보기** 로 제공됩니다.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

### <a name="switch-contexts-within-the-app"></a>앱 내에서 컨텍스트 전환

애플리케이션에 들어간 후에는 위쪽 도구 모음에서 인스턴스 이름을 선택하여 현재 탐색기에 연결된 인스턴스를 변경할 수도 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png" alt-text="위쪽 도구 모음의 인스턴스 이름이 강조 표시된 Azure Digital Twins Explorer의 스크린샷." lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png":::

그러면 **Azure Digital Twins URL 모달** 이 표시됩니다. 여기서 *https://* 다음에 다른 Azure Digital Twins 인스턴스의 호스트 이름을 입력하여 해당 인스턴스에 연결할 수 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png" alt-text="Azure Digital Twins Explorer의 스크린샷. Azure Digital Twins URL 모달에 https:// 및 호스트 이름을 포함한 편집 가능한 상자가 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png":::

>[!NOTE]
>지금은 앱 내에서 컨텍스트를 전환하는 기능은 개인 MSA(Microsoft 계정)에서 사용할 수 **없습니다**. MSA 사용자는 Azure Portal의 올바른 인스턴스에서 탐색기에 액세스하거나 [환경에 대한 직접 링크](#link-to-your-environment)를 통해 특정 인스턴스에 연결할 수 있습니다.


## <a name="query-your-digital-twin-graph"></a>Digital Twin 그래프 쿼리

**쿼리 탐색기** 패널을 사용하여 그래프에 대한 [쿼리](concepts-query-language.md)를 수행할 수 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png" alt-text="Azure Digital Twins Explorer의 스크린샷. 쿼리 탐색기 패널이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png":::

실행하려는 쿼리를 입력하고 **쿼리 실행** 단추를 선택합니다. 그러면 **트윈 그래프** 패널에 쿼리 결과가 로드됩니다.

>[!NOTE]
> 결과에 하나 이상의 트윈이 포함된 경우에도 관계를 비롯한 쿼리 결과는 **트윈 그래프** 패널에서만 렌더링할 수 있습니다. Azure Digital Twins에서는 관계만 반환하는 쿼리가 가능하지만 Azure Digital Twins Explorer에서는 [출력 패널](#accessibility-and-advanced-settings)을 사용해야만 관계를 볼 수 있습니다.

### <a name="overlay-query-results"></a>쿼리 결과 오버레이

패널의 콘텐츠를 새 쿼리 결과로 완전히 바꾸는 대신 현재 **트윈 그래프** 패널에 표시된 내용을 강조 표시하려면 쿼리를 실행하기 전에 **오버레이 결과** 상자를 선택할 수 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png" alt-text="Azure Digital Twins Explorer 쿼리 탐색기 패널의 스크린샷. 오버레이 결과 상자가 선택되고 트윈 그래프 패널에 표시된 더 큰 그래프에서 두 개의 트윈이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png":::

현재 **트윈 그래프** 패널에 표시되지 않는 항목이 쿼리 결과에 포함되어 있으면 요소가 기존 보기에 추가됩니다.

### <a name="save-and-rerun-queries"></a>쿼리 저장 및 다시 실행

쿼리를 브라우저의 로컬 스토리지에 저장하여 손쉽게 선택하고 다시 실행할 수 있습니다.

>[!NOTE]
>로컬 브라우저 스토리지는 저장된 쿼리를 저장한 브라우저 외에 다른 브라우저에서는 사용할 수 없음을 의미합니다. 그러한 쿼리는 로컬 스토리지를 지울 때까지 브라우저 스토리지에 무기한 유지됩니다.

쿼리를 저장하려면 쿼리 상자에 쿼리를 입력하고 패널의 오른쪽에 있는 **저장** 아이콘을 선택합니다. 대화 상자가 나타나면 저장된 쿼리에 대한 이름을 입력합니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png" alt-text="Azure Digital Twins Explorer 쿼리 탐색기 패널의 스크린샷. 저장 아이콘이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png":::

쿼리를 저장한 후에는 **저장된 쿼리** 드롭다운 메뉴에서 선택하여 손쉽게 다시 실행할 수 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png" alt-text="Azure Digital Twins Explorer 쿼리 탐색기 패널의 스크린샷. 저장된 쿼리 드롭다운 메뉴가 강조 표시되고 두 개의 샘플 쿼리가 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png":::

저장된 쿼리를 삭제하려면 **저장된 쿼리** 드롭다운 메뉴가 열릴 때 쿼리 이름 옆에 있는 **X** 아이콘을 선택합니다.

>[!TIP]
>더 큰 그래프의 경우 제한된 하위 집합만 쿼리한 다음, 필요에 따라 그래프의 나머지 부분을 로드하는 것이 좋습니다. **트윈 그래프** 패널에서 트윈을 두 번 클릭하여 추가 관련 노드를 검색할 수 있습니다.

## <a name="explore-the-twin-graph"></a>트윈 그래프 살펴보기

**트윈 그래프** 패널을 사용하면 인스턴스의 트윈 및 관계를 탐색할 수 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png" alt-text="Azure Digital Twins Explorer의 스크린샷. 트윈 그래프 패널이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png":::

이 패널을 사용하여 [트윈 및 관계를 볼 수](#view-twins-and-relationships) 있습니다.

트윈 그래프 패널은 그래프 보기 환경을 사용자 지정할 수 있는 기능도 몇 가지 제공합니다.
* [트윈 그래프 레이아웃 편집](#edit-twin-graph-layout)
* [트윈 그래프 확장 제어](#control-twin-graph-expansion)
* [트윈 그래프 요소 표시 및 숨기기](#show-and-hide-twin-graph-elements)
* [트윈 그래프 요소 필터링 및 강조 표시](#filter-and-highlight-twin-graph-elements)

### <a name="view-twins-and-relationships"></a>트윈 및 관계 보기

[쿼리 탐색기](#query-your-digital-twin-graph)를 사용하여 쿼리를 실행하면 **트윈 그래프** 패널에 표시되는 쿼리 결과에 트윈 및 관계가 표시됩니다.

>[!TIP]
>모든 트윈 및 관계를 표시하는 쿼리는 `SELECT * FROM digitaltwins`입니다.

#### <a name="view-twin-and-relationship-properties"></a>트윈 및 관계 속성 보기

트윈 또는 관계의 속성 값을 보려면 **트윈 그래프** 에서 트윈 또는 관계를 선택하고 **속성 검사기 설정/해제** 단추를 사용하여 **속성** 패널을 확장합니다. 이 패널에는 해당 값과 함께 요소와 연결된 모든 속성이 표시됩니다. 또한 아직 설정되지 않은 속성에 대한 기본값을 포함합니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Azure Digital Twins Explorer Twin Graph 패널의 스크린샷. FactoryA 쌍이 선택되고 트윈 속성 패널이 확장되어 트윈의 속성이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

트윈 속성 패널에는 트윈 또는 일부 속성이 더 이상 해당 모델과 일치하지 않는 경우 오류 메시지가 표시됩니다. 트윈에 대한 모델을 찾을 수 없거나 속성이 트윈에서 사용하는 모델 정의의 일부가 아닌 경우 다음과 같은 오류 메시지가 표시될 수 있습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/properties-errors.png" alt-text="두 개의 오류 메시지를 보여 Azure Digital Twins Explorer 쌍 쌍 속성 패널의 스크린샷. 한 오류는 모델이 누락되었음을 나타내고 다른 오류는 속성에 모델이 누락되었음을 나타냅니다. " lightbox="media/how-to-use-azure-digital-twins-explorer/properties-errors.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="view-a-twins-relationships"></a>트윈의 관계 보기

또한 특정 트윈(들어오고 나가는 관계 포함)을 포함하는 모든 관계의 코드를 빠르게 볼 수 있습니다.

이렇게 하려면 그래프에서 트윈을 마우스 오른쪽 단추로 클릭하고 **관계 가져오기** 를 선택합니다. 이렇게 하면 들어오고 나가는 전체 관계의 [JSON 표현](concepts-twins-graph.md#relationship-json-format)을 표시하는 **관계 정보** 모달이 나타납니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 화면 가운데에 들어오고 나가는 관계를 보여 주는 관계 정보 모달이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png":::

### <a name="edit-twin-graph-layout"></a>트윈 그래프 레이아웃 편집

트윈 그래프 화면에서 트윈을 클릭하여 끌어서 다른 구성으로 다시 정렬할 수 있습니다.

레이아웃 선택 메뉴의 옵션에서 그래프에 여러 **레이아웃** 알고리즘 중 하나를 적용할 수도 있습니다. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png" alt-text="Azure Digital Twins Explorer Twin Graph 패널의 스크린샷. 레이아웃 선택 단추가 강조 표시되어 있으며, 레이아웃 옵션이 있는 메뉴가 표시되어 있습니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png":::

### <a name="control-twin-graph-expansion"></a>트윈 그래프 확장 제어

**트윈 그래프** 패널에서 쿼리 결과를 보는 동안 트윈을 두 번 클릭하면 그래프에서 해당 관계 및 관련 트윈을 페치할 수 있습니다. 보기에 아직 없으면 표시할 수도 있습니다. 크기와 방향을 설정하여 페치할 트윈의 수를 결정하여 이 확장을 사용자 지정할 수 있습니다.

확장할 레이어의 수를 설정하려면 **확장 수준** 옵션을 사용합니다. 이 숫자는 선택한 트윈에서 페치할 관계의 레이어 수를 나타냅니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 확장 수준 단추가 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png":::

확장할 때 따라야 할 관계 유형을 나타내려면 **확장 방향** 단추를 사용합니다. 이렇게 하면 들어오는 관계에서만 또는 나가는 관계에서만 선택할 수도 있고, 아니면 들어오고 나가는 관계 모두에서 선택할 수도 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png" alt-text="Azure Digital Twins Explorer Twin Graph 패널의 스크린샷. 확장 방향 단추가 강조 표시되어 In, Out 및 In/Out 옵션이 있는 메뉴를 표시합니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png":::

### <a name="show-and-hide-twin-graph-elements"></a>트윈 그래프 요소 표시 및 숨기기

그래프 보기에서 트윈 또는 관계를 숨기는 옵션을 설정/해제할 수 있습니다. 

트윈 또는 관계를 숨기려면 **트윈 그래프** 창에서 해당 항목을 마우스 오른쪽 단추로 클릭합니다. 그러면 요소 또는 기타 관련 요소를 숨기는 옵션이 있는 메뉴가 표시됩니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. FactoryA 트윈이 선택되어 있으며, 선택한 항목 숨기기, 선택한 항목 + 자식 숨기기, 기타 모든 항목 숨기기, 자식이 아닌 항목 숨기기 옵션이 포함된 메뉴가 있습니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png":::

CTRL/CMD 또는 SHIFT 키를 사용하여 그래프에서 동일한 유형의 여러 요소를 다중 선택하여 여러 트윈 또는 관계를 한 번에 숨길 수도 있습니다. 지금부터 동일한 오른쪽 클릭 프로세스를 따라 숨기기 옵션을 볼 수 있습니다.

일부 항목을 숨긴 후 모든 트윈 표시로 돌아가려면 **모두 표시** 단추를 사용합니다. 모든 관계 표시로 돌아가려면 **모든 관계 표시** 단추를 사용합니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 모두 표시 및 모든 관계 표시 단추가 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png":::

### <a name="filter-and-highlight-twin-graph-elements"></a>트윈 그래프 요소 필터링 및 강조 표시

이 **필터** 아이콘을 선택하여 그래프에 표시되는 트윈 및 관계를 텍스트로 필터링할 수 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 텍스트 필터 아이콘이 선택되어 검색어를 입력할 수 있는 필터 탭이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png":::

이 **강조 표시** 아이콘을 선택하여 그래프에 표시되는 트윈 및 그래프를 텍스트 기준으로 강조 표시할 수도 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 텍스트 필터 아이콘이 선택되어 검색어를 입력할 수 있는 강조 표시 탭이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png":::

## <a name="manage-twins-and-graph"></a>트윈 및 그래프 관리

Azure Digital Twins Explorer는 인스턴스의 [트윈](concepts-twins-graph.md#digital-twins) 및 [관계](concepts-twins-graph.md#relationships-a-graph-of-digital-twins)를 관리하는 여러 가지 방법을 제공합니다.

이 섹션에서는 다음과 같은 관리 작업을 수행하는 방법을 설명합니다.
* [트윈 및 관계의 플랫 목록 보기](#view-flat-list-of-twins-and-relationships)
* 초기 속성을 사용하거나 사용하지 않고 [트윈 만들기](#create-twins)
* 트윈 간에 [관계 만들기](#create-relationships)
* [트윈 및 관계 편집](#edit-twins-and-relationships)
* [트윈 및 관계 삭제](#delete-twins-and-relationships)

트윈 및 관계의 보기 환경에 대한 자세한 내용은 [트윈 및 트윈 그래프 살펴보기](#explore-the-twin-graph)를 참조하세요.

### <a name="view-flat-list-of-twins-and-relationships"></a>트윈 및 관계의 플랫 목록 보기

**트윈** 패널에는 트윈 및 연결된 관계의 플랫 목록이 표시됩니다. 이름으로 쌍를 검색 하 고이를 확장 하 여 들어오고 나가는 관계에 대 한 세부 정보를 확인할 수 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twins-panel.png" alt-text="Azure Digital Twins 탐색기 쌍 패널의 스크린샷 쌍이 강조 표시 되 고 해당 관계가 표시 됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twins-panel.png":::

### <a name="create-twins"></a>트윈 만들기

**모델** 패널의 모델 정의에서 새 디지털 트윈을 만들 수 있습니다.

모델에서 쌍을 만들려면 목록에서 해당 모델을 찾고 모델 이름 옆의 메뉴 점을 선택 합니다. 그런 다음 쌍 **만들기** 를 선택 합니다. 새 트윈에 사용할 **이름** 을 입력하라는 메시지가 표시됩니다. 이 이름은 고유해야 합니다. 그런 다음, 트윈을 저장하여 그래프에 추가합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png" alt-text="Azure Digital Twins 탐색기 모델 패널의 스크린샷 단일 모델에 대 한 메뉴 점이 강조 표시 되 고 쌍을 만드는 메뉴 옵션도 강조 표시 됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

트윈에 속성 값을 추가하려면 [트윈 및 관계 편집](#edit-twins-and-relationships)을 참조하세요.

### <a name="create-relationships"></a>관계 만들기

두 트윈 간에 관계를 만들려면 **트윈 그래프** 창에서 관계에 대한 원본 트윈을 선택하는 것부터 시작합니다. 다음으로, CTRL/CMD 또는 SHIFT 키를 누른 상태에서 두 번째 트윈을 선택하여 관계의 대상으로 지정합니다.

두 트윈을 동시에 선택한 후에는 트윈 중 하나를 마우스 오른쪽 단추로 클릭합니다. 그러면 두 항목 간의 **관계를 추가** 하는 옵션이 포함된 메뉴가 표시됩니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. FactoryA 및 Consumer 트윈이 선택되고 메뉴에 관계를 추가하는 옵션이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png":::

이렇게 하면 관계의 원본 트윈 및 대상 트윈을 보여 주는 **관계 만들기** 대화 상자가 표시되고, 원본 트윈에 포함될 수 있는 관계 유형(DTDL 모델에 정의됨)이 포함된 **관계** 드롭다운 메뉴가 표시됩니다. 관계 유형에 대한 옵션을 선택하고 새 관계를 **저장** 합니다.

### <a name="edit-twins-and-relationships"></a>트윈 및 관계 편집

트윈 또는 관계의 속성 값을 보려면 **트윈 그래프** 에서 요소를 선택하고 **속성 검사기 설정/해제** 단추를 사용하여 **속성** 패널을 확장합니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Azure Digital twins 탐색기 쌍 Graph 패널의 스크린샷 FactoryA 쌍이 선택 되 고 쌍 속성 패널이 확장 되어 쌍의 속성을 표시 합니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

이 패널을 사용하여 쓰기 가능한 속성을 직접 편집할 수 있습니다. 값을 인라인으로 업데이트하고 패널 위쪽의 **패치 트윈**(저장) 단추를 클릭하여 변경 내용을 저장합니다. 업데이트를 저장하면 [업데이트 API](/rest/api/azure-digitaltwins/)에서 적용한 JSON 패치 작업을 보여 주는 모달 창이 화면에 표시됩니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 화면 가운데에는 JSON 패치 코드를 표시하는 경로 정보 모달이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png":::

### <a name="delete-twins-and-relationships"></a>트윈 및 관계 삭제

트윈 또는 관계를 삭제하려면 **트윈 그래프** 창에서 해당 항목을 마우스 오른쪽 단추로 클릭합니다. 그러면 요소를 삭제하는 옵션이 포함된 메뉴가 표시됩니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. FactoryA 트윈이 선택되고 트윈을 삭제하는 옵션이 포함된 메뉴가 있습니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png":::

CTRL/CMD 또는 SHIFT 키를 사용하여 그래프에서 동일한 유형의 여러 요소를 다중 선택하여 여러 개의 트윈이나 관계를 한 번에 삭제할 수도 있습니다. 지금부터 동일한 오른쪽 클릭 프로세스에 따라 요소를 삭제합니다.

위쪽의 도구 모음에 있는 **모든 트윈 삭제** 단추를 사용하여 인스턴스의 모든 트윈을 동시에 삭제하도록 선택할 수도 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png" alt-text="Azure Digital Twins Explorer의 스크린샷. 모든 트윈 삭제 아이콘이 선택됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png":::

## <a name="explore-models-and-the-model-graph"></a>모델 및 모델 그래프 살펴보기

모델은 Azure Digital Twins Explorer 화면의 왼쪽에 있는 **모델** 패널과, 화면 중간의 **모델 그래프** 패널에서 볼 수 있습니다.

**모델** 패널: :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Azure Digital Twins Explorer의 스크린샷. 모델 패널이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

**모델 그래프** 패널: :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Azure Digital Twins Explorer의 스크린샷. 모델 그래프 패널이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

이러한 패널을 사용하여 [모델을 볼 수](#view-models) 있습니다.

모델 그래프 패널은 그래프 보기 환경을 사용자 지정할 수 있는 기능도 몇 가지 제공합니다.
* [모델 그래프 레이아웃 편집](#edit-model-graph-layout)
* [모델 그래프 요소 필터링 및 강조 표시](#filter-and-highlight-model-graph-elements)
* [모델 이미지 업로드](#upload-model-images)로 그래프에 모델 나타내기

### <a name="view-models"></a>모델 보기

**모델** 패널에서 인스턴스의 모델에 대한 단순 목록을 볼 수 있습니다. 이 목록은 패널의 **검색** 창을 사용하여 검색할 수 있습니다.

**모델 그래프** 패널을 사용하여 인스턴스의 모델에 대한 그래픽 표현과 모델을 서로 연결하는 관계, 상속 및 구성 요소를 볼 수 있습니다.

#### <a name="view-model-definition"></a>모델 정의 보기

모델에 대 한 전체 정의를 보려면 **모델 창에서** 해당 모델을 찾고 모델 이름 옆의 메뉴 점을 선택 합니다. 그런 다음 **모델 보기** 를 선택 합니다. 모델의 원시 DTDL 정의를 보여 주는 **모델 정보** 모달이 표시됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png" alt-text="Azure Digital Twins 탐색기 모델 패널의 스크린샷 단일 모델에 대 한 메뉴 점이 강조 표시 되 고 모델을 보는 메뉴 옵션도 강조 표시 됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

모델 **Graph** 에서 모델을 선택 하 고 모델 **정보 설정/해제** 단추를 사용 하 여 모델 **세부** 정보 패널을 확장 하는 방법으로 모델의 전체 정의를 볼 수도 있습니다. 이 패널에는 모델에 대한 전체 DTDL 코드도 표시됩니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png" alt-text="Azure Digital twins 탐색기 모델 Graph 패널의 스크린샷 평면도가 선택 되 고 모델 정보 패널이 확장 되어 모델의 DTDL 코드를 표시 합니다." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png":::

### <a name="edit-model-graph-layout"></a>모델 그래프 레이아웃 편집

모델 그래프 화면에서 모델을 클릭하여 끌어서 다른 구성으로 다시 정렬할 수 있습니다.

**레이아웃 실행** 메뉴의 옵션에서 여러 레이아웃 알고리즘 중 하나를 모델 그래프에 적용할 수도 있습니다. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png" alt-text="Azure Digital Twins Explorer 모델 그래프 패널의 스크린샷. 레이아웃 실행 단추가 강조 표시되고 Cola, Dagre, fCoSE, Klay 및 d3Force 레이아웃 옵션이 포함된 메뉴가 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png":::

### <a name="filter-and-highlight-model-graph-elements"></a>모델 그래프 요소 필터링 및 강조 표시

모델 그래프에 표시되는 연결 유형을 필터링할 수 있습니다. 이 메뉴의 스위치를 통해 연결 유형 중 하나를 끄면 해당 연결 유형이 그래프에 표시되지 않습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png" alt-text="Azure Digital Twins Explorer 모델 그래프 패널의 스크린샷. 관계, 상속 및 구성 요소에 대한 필터 메뉴가 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png":::

이 **필터** 아이콘을 선택하여 그래프에 표시되는 모델 및 연결을 텍스트로 필터링할 수도 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png" alt-text="Azure Digital Twins Explorer 모델 그래프 패널의 스크린샷. 텍스트 필터 아이콘이 선택되어 검색어를 입력할 수 있는 필터 탭이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png":::

이 **강조 표시** 아이콘을 선택하여 그래프에 표시되는 모델 및 연결을 텍스트 기준으로 강조 표시할 수도 있습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png" alt-text="Azure Digital Twins Explorer 모델 그래프 패널의 스크린샷. 텍스트 필터 아이콘이 선택되어 검색어를 입력할 수 있는 강조 표시 탭이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png":::

### <a name="upload-model-images"></a>모델 이미지 업로드

모델 그래프 및 [트윈 그래프](#explore-the-twin-graph) 보기에서 다른 모델을 나타내기 위해 사용자 지정 이미지를 업로드할 수 있습니다. 개별 모델을 업로드하거나 여러 개 모델을 한 번에 업로드할 수 있습니다.

>[!NOTE]
>이러한 이미지는 로컬 브라우저 스토리지에 저장됩니다. 즉, 이미지를 저장한 브라우저 외에 다른 브라우저에서는 사용할 수 없음을 의미합니다. 그러한 이미지는 로컬 스토리지를 지울 때까지 브라우저 스토리지에 무기한 유지됩니다.

단일 모델에 대 한 이미지를 업로드 하려면 모델 패널에서 해당 모델을 **찾고 모델 이름** 옆의 메뉴 점을 선택 합니다. 그런 다음 **업로드 모델 이미지** 를 선택 합니다. 표시되는 파일 선택기 상자에서 사용자 컴퓨터에 있는 해당 모델에 대해 업로드할 이미지 파일로 이동합니다. **열기** 를 선택하여 업로드합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png" alt-text="Azure Digital Twins 탐색기 모델 패널의 스크린샷 단일 모델에 대 한 메뉴 점이 강조 표시 되 고 모델 이미지를 업로드 하는 메뉴 옵션도 강조 표시 됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

모델 이미지를 대량으로 업로드할 수도 있습니다.

먼저 다음 지침을 사용하여 업로드하기 전에 이미지 파일 이름을 설정합니다. 이렇게 하면 Azure Digital Twins Explorer에서 업로드 후에 올바른 모델에 이미지를 자동으로 할당할 수 있습니다. 
1. 모델 ID 값으로 시작합니다(예: `dtmi:example:Floor;1`).
1. “:”의 인스턴스를 “_”로 바꿉니다(이 예제에서는 `dtmi_example_Floor;1`).
1. “;”의 인스턴스를 “-”로 바꿉니다(이 예제에서는 `dtmi_example_Floor-1`).
1. 파일에 이미지 확장명이 있는지 확인합니다(이 예제에서는 `dtmi_example_Floor-1.png`).

> [!NOTE]
> 위의 조건을 사용하여 기존 모델에 매핑되지 않는 이미지를 업로드하려고 하면 이미지가 업로드되지 않거나 저장되지 않습니다.

그런 다음, 이미지를 동시에 업로드하려면 모델 패널의 맨 위에 있는 **모델 이미지 업로드** 아이콘을 사용합니다. 파일 선택기 상자에서 업로드할 이미지 파일을 선택합니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png" alt-text="Azure Digital Twins Explorer 모델 패널의 스크린샷. 모델 이미지 업로드 아이콘이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-models"></a>모델 관리

Azure Digital Twins Explorer 화면의 왼쪽에 있는 **모델** 패널을 사용하여 전체 모델 세트 또는 개별 모델에서 관리 작업을 수행할 수 있습니다. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Azure Digital Twins Explorer의 스크린샷. 모델 패널이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

이 패널을 사용하여 다음 모델 관리 작업을 완료할 수 있습니다.
* Azure Digital Twins 인스턴스에 [모델 업로드](#upload-models)
* 인스턴스에서 [모델 삭제](#delete-models)
* 이 패널에 모든 모델 목록을 다시 로드하도록 [모델 새로 고침](#refresh-models)

모델의 보기 환경에 대한 자세한 내용은 [모델 및 모델 그래프 살펴보기](#explore-models-and-the-model-graph)를 참조하세요.

### <a name="upload-models"></a>모델 업로드

사용자를 개별적으로 선택하거나 모델의 전체 폴더를 한 번에 업로드하여 컴퓨터에 있는 모델을 업로드할 수 있습니다.

개별적으로 선택 된 하나 이상의 모델을 업로드 하려면 위쪽 화살표를 표시 하는 **모델 업로드** 아이콘을 선택 합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png" alt-text="Azure Digital Twins Explorer 모델 패널의 스크린샷. 모델 업로드 아이콘이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

표시되는 파일 선택기 상자에서 사용자 컴퓨터에 있는 업로드할 모델로 이동합니다. 하나 이상의 JSON 모델 파일을 선택하고 **열기** 를 선택하여 업로드할 수 있습니다.

모든 항목을 포함한 모델의 폴더를 업로드하려면 파일 폴더를 표시하는 **모델의 디렉터리 업로드** 아이콘을 선택합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png" alt-text="Azure Digital Twins Explorer 모델 패널의 스크린샷. 모델의 디렉터리 업로드 아이콘이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

표시되는 파일 선택기 상자에서 사용자 컴퓨터에 있는 JSON 모델 파일이 포함된 폴더로 이동합니다. **열기** 를 선택하여 최상위 폴더와 모든 콘텐츠를 업로드합니다.

>[!IMPORTANT]
>관계 또는 구성 요소를 정의할 때와 같이 모델이 정의에서 다른 모델을 참조하는 경우 참조되는 모델이 인스턴스에 있어야 해당 모델을 사용하는 모델을 업로드할 수 있습니다. 모델을 하나씩 업로드하는 경우 이를 사용하는 모델을 업로드하기 **전에** 참조되는 모델을 업로드해야 합니다. 모델을 대량으로 업로드하는 경우 동일한 가져오기에서 모델 모두 선택할 수 있으며 Azure Digital Twins에서 업로드 순서를 유추합니다.

### <a name="delete-models"></a>모델 삭제

모델 패널을 사용하여 개별 모델 또는 인스턴스의 모든 모델을 한 번에 삭제할 수 있습니다.

단일 모델을 삭제하려면 목록에서 해당 모델을 찾고 모델 이름 옆에 있는 메뉴 점을 선택합니다. 그런 다음, **모델 삭제를** 선택합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png" alt-text="Azure Digital Twins Explorer 모델 패널의 스크린샷. 단일 모델에 대한 메뉴 점이 강조 표시되고 모델 삭제 메뉴 옵션도 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

인스턴스의 모든 모델을 한 번에 삭제하려면 모델 패널 맨 위에 있는 **모든 모델 삭제** 아이콘을 선택합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png" alt-text="Azure Digital Twins Explorer 모델 패널의 스크린샷. 모든 모델 삭제 아이콘이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="refresh-models"></a>모델 새로 고침

Azure Digital Twins Explorer를 열면 모델 패널에 사용자 환경에서 사용 가능한 모든 모델이 자동으로 표시됩니다. 

그러나 언제든지 패널을 수동으로 새로 고쳐 Azure Digital Twins 인스턴스의 모든 모델 목록을 다시 로드할 수 있습니다. 이렇게 하려면 **모델 새로 고침** 아이콘을 선택합니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png" alt-text="Azure Digital Twins Explorer 모델 패널의 스크린샷. 모델 새로 고침 아이콘이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="importexport-graph"></a>그래프 가져오기/내보내기

**트윈 그래프** 패널에는 그래프 [가져오기](#import-graph) 및 [내보내기](#export-graph-and-models) 기능의 옵션이 있습니다.

### <a name="import-graph"></a>그래프 가져오기

가져오기 기능을 사용하여 트윈, 관계 및 모델을 인스턴스에 추가할 수 있습니다. 이는 여러 트윈, 관계 및/또는 모델을 한 번에 만드는 데 유용할 수 있습니다.

#### <a name="create-import-file"></a>가져오기 파일 만들기

그래프를 가져오는 첫 번째 단계는 추가하려는 트윈과 관계를 나타내는 파일을 만드는 것입니다.

가져오기 파일은 다음 두 가지 형식 중 하나일 수 있습니다.
* **사용자 지정 Excel 기반 형식** 은 이 섹션의 나머지 부분에서 설명하며, 트윈 및 관계를 업로드할 수 있습니다.
* **JSON 기반 형식** 은 [그래프 내보내기](#export-graph-and-models)에서 생성되며, 트윈, 관계 및/또는 모델이 포함될 수 있습니다.

Excel에서 사용자 지정 그래프를 만들려면 다음 형식을 사용합니다.

각 행은 만들 요소를 나타냅니다. 트윈, 관계 또는 트윈과 해당 관계의 조합입니다.
다음 열을 사용하여 트윈 또는 관계 데이터를 구성합니다. 열 이름은 사용자 지정할 수 있지만 이 순서대로 유지되어야 합니다.

| ModelID | ID | 관계(원본) | 관계 이름 | Init 데이터 |
| --- | --- | --- | --- | --- |
| *선택 사항*<br>만들어야 하는 트윈의 DTMI 모델 ID입니다.<br><br>행이 트윈이 아닌 관계만 생성하도록 하려면 행에 대해 이 열을 비워 둘 수 있습니다. | *필수*<br>트윈의 고유 ID입니다.<br><br>이 행에서 새 트윈을 만드는 경우 새 트윈의 ID가 됩니다.<br>행에 관계 정보가 있으면 이 ID가 관계의 **대상** 으로 사용됩니다. | *선택 사항*<br>새 관계의 **원본** 트윈이어야 하는 트윈의 ID입니다.<br><br>행이 관계가 아닌 트윈만 생성하도록 하려면 행에 대해 이 열을 비워 둘 수 있습니다. | *선택 사항*<br>만들 새 관계의 이름입니다. 관계 방향은 C열의 트윈 **에서** B열의 트윈 **까지** 입니다. | *선택 사항*<br>만들 트윈에 대한 속성 설정이 포함된 JSON 문자열입니다. 속성은 A열의 모델에 정의된 속성과 일치해야 합니다. |

다음은 2개 층과 2개의 방으로 된 작은 그래프를 만드는 .xlsx 파일의 예제입니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/import-example.png" alt-text="Excel의 그래프 데이터 스크린샷. 열 머리글은 위의 필드에 순서대로 해당하며 행에는 해당 데이터 값이 포함됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/import-example.png":::

GitHub의 [Azure Digital Twins Explorer 리포지토리](https://github.com/Azure-Samples/digital-twins-explorer/tree/main/client/examples)에서 이 파일 및 추가 .xlsx 그래프 예제를 볼 수 있습니다.

>[!NOTE]
>.xlsx에 설명된 속성 및 관계는 관련 트윈에 대한 모델 정의에 정의된 것과 일치해야 합니다.

#### <a name="import-file-to-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer로 파일 가져오기

컴퓨터에 가져올 준비가 된 파일이 있으면 트윈 그래프 패널에서 **그래프 가져오기** 아이콘을 선택합니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 그래프 가져오기 단추가 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png":::

표시되는 파일 선택기 상자에서 사용자 컴퓨터에 있는 업로드하려는 그래프 파일(.xlsx 또는 .json)로 이동하고 **열기** 를 선택하여 업로드합니다.

Azure Digital Twins에서 가져올 그래프의 미리 보기를 보여 주는 **가져오기** 패널이 열립니다. 확인하려면 패널의 오른쪽 위 모서리에서 **저장** 아이콘을 선택합니다.

가져오기에 성공하면 모달 창에 업로드된 모델, 트윈 및 관계의 수가 표시됩니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 화면 가운데에는 가져온 트윈 4개와 가져온 관계 2개가 표시된 가져오기 성공 모달이 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png":::

### <a name="export-graph-and-models"></a>그래프 및 모델 내보내기

내보내기 기능을 사용하여 모델, 트윈 및 관계를 포함한 부분 또는 전체 그래프를 내보낼 수 있습니다. 내보내기는 인스턴스의 모든 모델뿐만 아니라 가장 최근의 쿼리 결과의 트윈 및 관계를 컴퓨터에 다운로드할 수 있는 JSON 기반 형식으로 직렬화합니다.

시작하려면 [쿼리 탐색기](#query-your-digital-twin-graph) 패널을 사용하여 다운로드하려는 트윈 및 관계를 선택하는 쿼리를 실행합니다. 그러면 트윈 그래프 패널에 채워집니다.

>[!TIP]
>모든 트윈 및 관계를 표시하는 쿼리는 `SELECT * FROM digitaltwins`입니다.

트윈 그래프 패널에 다운로드하려는 그래프 부분이 표시되면 **그래프 내보내기** 아이콘을 선택합니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png" alt-text="Azure Digital Twins Explorer 트윈 그래프 패널의 스크린샷. 그래프 내보내기 단추가 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png":::

그러면 트윈 그래프 상자에서 **다운로드** 링크가 활성화됩니다. 이를 선택하여 쿼리 결과의 JSON 기반 표현과 인스턴스의 모든 모델을 사용자 컴퓨터에 다운로드합니다.

>[!TIP]
>[가져오기](#import-graph) 기능을 통해 이 파일을 편집하거나 Azure Digital Twins로 다시 업로드할 수 있습니다.

## <a name="link-to-your-environment"></a>사용자 환경에 연결

Azure Digital Twins Explorer 환경을 다른 사람과 공유하여 작업에서 협업할 수 있습니다. 이 섹션에서는 Azure Digital Twins Explorer 환경을 다른 사람에게 보내고 액세스 권한이 있는지 확인하는 방법을 설명합니다.

환경을 공유하려면 수신자에게 링크를 보내 인스턴스에 연결된 Azure Digital Twins Explorer 창을 열 수 있습니다. 아래 링크를 사용하고 **테넌트 ID** 의 자리 표시자와 Azure Digital Twins 인스턴스의 **호스트 이름** 을 대체합니다. 

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>`

>[!NOTE]
> 호스트 이름 자리 표시자의 값은 여기서 *https://* 뒤에 오지 **않습니다**.

자리 표시자 값이 채워진 URL의 예제는 다음과 같습니다.

`https://explorer.digitaltwins.azure.net/?tid=00a000aa-00a0-00aa-0a0aa000aa00&eid=ADT-instance.api.wcus.digitaltwins.azure.net`

수신자가 결과 Azure Digital Twins Explorer 창에서 인스턴스를 보려면 해당 Azure 계정에 로그인하고 인스턴스에 대한 **Azure Digital Twins 데이터 읽기 권한자** 액세스 권한이 있어야 합니다(Azure Digital Twins 역할에 대한 자세한 내용은 [보안](concepts-security.md) 참조). 수신자가 그래프 및 데이터를 변경하려면 인스턴스에 대한 **Azure Digital Twins 데이터 소유자** 역할이 있어야 합니다.

### <a name="link-with-a-query"></a>쿼리와 연결

환경을 공유하고 방문 시 실행할 쿼리를 지정하여 팀 동료의 하위 그래프 또는 사용자 지정 보기를 강조 표시할 수 있습니다. 이렇게 하려면 환경에 대한 URL로 시작하고 쿼리 텍스트를 querystring 매개 변수로 URL에 추가합니다.

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>&query=<query-text>`

쿼리 텍스트는 URL로 인코딩되어야 합니다. 

>[!TIP]
>**쿼리 탐색기** 창에서 쿼리 텍스트를 복사하여 URL 끝의 올바른 위치에 있는 URL 창에 붙여넣을 수 있습니다. 이 URL을 제출하면 적절한 URL 인코딩을 사용하도록 쿼리 텍스트가 변환됩니다.
>
> 독립 URL 인코더를 사용하여 쿼리 텍스트를 변환할 수도 있습니다.

다음은 **SELECT * FROM digitaltwins** 에 대한 쿼리의 매개 변수 예제입니다.

`...&query=SELECT%20*%20FROM%20digitaltwins`

그런 다음, 완료된 URL을 공유할 수 있습니다.

## <a name="accessibility-and-advanced-settings"></a>접근성 및 고급 설정

Azure Digital Twins Explorer 대한 몇 가지 고급 설정을 사용하도록 설정하여 환경을 사용자 지정하거나 더 쉽게 액세스할 수 있도록 할 수 있습니다.

오른쪽 위에 있는 바로 **가기 키** 아이콘을 사용 하 여 Azure Digital Twins 탐색기를 탐색 하는 데 사용할 수 있는 바로 가기 키 목록을 볼 수 있습니다.

 :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png" alt-text="Azure Digital Twins 탐색기의 스크린샷 바로 가기 키 아이콘은 맨 위 도구 모음에 강조 표시 됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png":::

오른쪽 위 도구 모음의 설정 코그에서 액세스할 수 있는 몇 가지 고급 기능이 있습니다.
* **즉시 로드**: 쿼리가 쿼리 결과에 포함 **되지** 않은 다른 쌍에 대 한 관계가 있는 쌍를 반환 하는 경우이 기능은 그래프를 렌더링 하기 전에 "누락 된" 쌍을 로드 합니다.
* **캐싱**:이 기능을 사용 하도록 설정 하면 Azure Digital Twins 탐색기는 관계 및 모델의 로컬 캐시를 메모리에 보관 하 여 쿼리 성능을 향상 시킵니다. 이러한 캐시는 브라우저 새로 고침뿐만 아니라 관련 요소에 대한 쓰기 작업에서 지워집니다.
* **콘솔**:이 기능을 사용 하면 그래프 작업에 간단한 셸 함수를 사용할 수 있는 콘솔 창을 표시할 수 있습니다.
* **출력**:이 기능을 사용 하면 작업의 진단 추적을 보여 주는 출력 창을 표시할 수 있습니다.
* **고대비**:이 기능은 Azure Digital Twins 탐색기의 색을 변경 하 여 더 높은 대비로 표시 되도록 합니다.

Azure Digital twins 탐색기를 구성 하는 패널의 위치를 편집 하 여 **패널 레이아웃을 사용자 지정할** 수 있습니다 (쿼리 탐색기, 쌍, 모델, 쌍 Graph, 모델 Graph). 패널을 다른 위치로 이동하려면 패널 이름을 클릭하고 누른 상태로 원하는 새 위치로 끌어 놓습니다.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/panels.png" alt-text="Azure Digital Twins Explorer의 스크린샷. 쿼리 탐색기, 모델, 트윈 그래프 및 모델 그래프 패널의 이름이 강조 표시됩니다." lightbox="media/how-to-use-azure-digital-twins-explorer/panels.png":::

브라우저 창을 새로 고치면 패널 위치가 다시 설정됩니다.

## <a name="next-steps"></a>다음 단계 

Azure Digital Twins 트윈 그래프에 대한 쿼리 작성에 대해 알아봅니다. 
* [쿼리 언어](concepts-query-language.md)
* [쌍 그래프 쿼리](how-to-query-graph.md)