---
title: Azure Portal에서 대시보드 만들기
description: 이 문서에서는 Azure Portal에서 대시보드를 만들고 사용자 지정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 08/19/2021
ms.openlocfilehash: af26cc36f73088c9a4231e59f3414efaa828d452
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597725"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Azure Portal에서 대시보드 만들기

대시보드는 Azure Portal의 클라우드 리소스에 집중적으로 구성된 보기입니다. 대시보드를 작업 영역으로 사용하여 일상적인 작업 목적으로 리소스를 모니터링하고 신속하게 작업을 시작할 수 있습니다. 예를 들어 프로젝트, 작업 또는 사용자 역할을 기반으로 사용자 지정 대시보드를 빌드합니다.

Azure Portal은 기본 대시보드를 시작점으로 제공합니다. 기본 대시보드를 편집하고 추가 대시보드를 만들어 사용자 지정할 수 있습니다.

> [!NOTE]
> 각 사용자는 최대 100개의 프라이빗 대시보드를 만들 수 있습니다. [대시보드를 게시하고 공유](azure-portal-dashboard-share-access.md)하면 구독에서 Azure 리소스로 구현되며 이 제한에 대해 계산되지 않습니다.

이 문서에서는 새 대시보드를 만들고 사용자 지정하는 방법을 설명합니다. 대시보드 공유에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어를 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)를 참조하세요.

## <a name="create-a-new-dashboard"></a>새 대시보드 만들기

이 예에서는 할당된 이름으로 새 프라이빗 대시보드를 만드는 방법을 보여 줍니다. 원하는 경우 조직의 다른 사용자와 대시보드를 게시하고 공유하도록 선택할 수 있지만 모든 대시보드는 생성 시 비공개입니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 메뉴에서 **대시보드** 를 선택합니다. 기본 보기는 이미 대시보드로 설정되어 있을 수 있습니다.

    :::image type="content" source="media/azure-portal-dashboards/portal-menu-dashboard.png" alt-text="대시보드가 선택된 Azure Portal의 스크린샷":::

1. **새 대시보드**, **빈 대시보드** 를 차례로 선택합니다.

    :::image type="content" source="media/azure-portal-dashboards/create-new-dashboard.png" alt-text="새 대시보드 옵션의 스크린샷":::

    이 작업을 수행하면 타일을 선택할 수 있는 **타일 갤러리** 와 타일을 배열할 빈 그리드가 열립니다.

1. 대시보드 레이블에서 **내 대시보드** 텍스트를 선택하고 사용자 지정 대시보드를 쉽게 식별할 수 있는 이름을 입력합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="타일 갤러리가 있는 빈 그리드의 스크린샷":::

1. 대시보드를 있는 그대로 저장하려면 페이지 헤더에서 **사용자 지정 완료** 를 선택합니다. 또는 다음 섹션의 2단계로 계속 진행하여 타일을 추가하고 대시보드를 저장합니다.

이제 대시보드 보기에 새 대시보드가 표시됩니다. 대시보드 이름 옆의 화살표를 선택하면 사용할 수 있는 대시보드를 표시합니다. 이 목록에는 다른 사용자가 만들고 공유한 대시보드가 포함되어 있을 수 있습니다.

## <a name="edit-a-dashboard"></a>대시보드 편집

이제 대시보드를 편집하여 사용자의 Azure 리소스를 대표하는 타일을 추가, 크기 조정 및 정렬해 봅니다.

### <a name="add-tiles-from-the-tile-gallery"></a>타일 갤러리에서 타일 추가

대시보드에 타일을 추가하려면 다음 단계를 수행합니다.

1. 대시보드의 페이지 헤더에서 ![편집 아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 을 선택합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-edit.png" alt-text="편집 옵션을 강조 표시한 대시보드의 스크린샷":::

1. **타일 갤러리** 를 탐색하거나 검색 필드를 사용하여 특정 타일을 찾습니다. 대시보드에 추가하려는 타일을 선택합니다.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="타일 갤러리의 스크린샷":::

1. **추가** 를 선택해 기본 사이즈와 기본 위치로 타일을 대시보드에 추가합니다. 또는 타일을 그리드로 끌어 원하는 위치에 놓습니다. 원하는 타일을 추가합니다. 다음과 같은 몇 가지 아이디어가 있습니다.

    - **모든 리소스** 를 추가하여 이미 만든 리소스를 확인합니다.

    - 둘 이상의 조직에서 작업하는 경우 **조직 ID** 타일을 대시보드에 추가해 리소스가 속한 조직을 명확하게 표시합니다.

1. 원하는 경우 타일을 [크기 조정하거나 다시 정렬](#resize-or-rearrange-tiles)합니다.

1. 변경 사항을 저장하려면 페이지 헤더에서 **저장** 을 선택합니다. 페이지 헤더에서 **미리 보기** 를 선택하여 저장하지 않고 변경 사항을 미리 볼 수도 있습니다. 이 미리 보기 모드를 사용하면 [필터](#set-and-override-dashboard-filters)가 타일에 어떤 영향을 미치는지 확인할 수도 있습니다. 미리 보기 화면에서 **저장** 을 선택하여 변경 내용을 유지하거나, **취소** 를 선택하여 제거하거나, **편집** 을 선택하여 편집 옵션으로 돌아가고 추가로 변경할 수 있습니다.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="미리 보기, 저장 및 삭제 옵션의 스크린샷":::

> [!NOTE]
> markdown 타일을 사용하면 대시보드에 사용자 지정 고정 콘텐츠를 표시할 수 있습니다. 이는 기본 지침, 이미지, 하이퍼링크 집합 또는 연락처 정보를 표시할 수도 있습니다. markdown 타일을 사용하는 방법에 대한 자세한 내용은 [Azure 대시보드의 markdown 타일을 사용하여 사용자 지정 콘텐츠 표시](azure-portal-markdown-tile.md)를 참조하세요.

### <a name="pin-content-from-a-resource-page"></a>리소스 페이지에서 콘텐츠 고정

리소스 페이지에서 대시보드에 타일을 직접 추가할 수도 있습니다.

많은 리소스 페이지에는 페이지 머리글에 고정 아이콘이 포함되어 있습니다. 즉, 원본 페이지를 나타내는 타일을 고정할 수 있습니다. 경우에 따라 고정 아이콘이 페이지 내의 특정 콘텐츠에 의해 나타날 수도 있습니다. 즉, 전체 페이지가 아닌 특정 콘텐츠에 대한 타일을 고정할 수 있습니다.

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-blade.png" alt-text="고정 아이콘이 있는 페이지 명령 모음의 스크린샷":::

이 아이콘을 선택하면 기존 프라이빗 또는 공유 대시보드에 타일을 고정할 수 있습니다. **새로 만들기** 를 선택하여 이 고정을 포함할 새 대시보드를 만들 수도 있습니다.

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-pane.png" alt-text="대시보드에 고정 옵션의 스크린샷":::

### <a name="copy-a-tile-to-a-new-dashboard"></a>새 대시보드에 타일 복사

다른 대시보드에서 타일을 다시 사용하려는 경우 한 대시보드에서 다른 대시보드로 복사할 수 있습니다. 이렇게 하려면 오른쪽 상단에서 컨텍스트 메뉴를 선택한 다음 **복사** 를 선택합니다.

:::image type="content" source="media/azure-portal-dashboards/copy-dashboard.png" alt-text="Azure Portal에서 타일을 복사하는 방법을 보여 주는 스크린샷":::

그런 다음 타일을 기존 프라이빗 또는 공유된 대시보드에 복사할지 선택하거나 이미 작업 중인 대시보드 내에서 타일의 복사본을 만들 수 있습니다. **새로 만들기** 를 선택하여 타일 복사본을 포함할 새 대시보드를 만들 수도 있습니다.

### <a name="resize-or-rearrange-tiles"></a>타일 크기 조정 또는 재정렬

타일의 크기를 변경하거나 대시보드의 타일을 재정렬하려면 다음 단계를 수행합니다.

1. 페이지 머리글에서 ![편집 아이콘](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 을 선택합니다.

1. 타일의 오른쪽 위 모퉁이에 있는 바로 가기 메뉴를 선택합니다. 그 다음 타일 크기를 선택합니다. 모든 크기를 지원하는 타일의 오른쪽 아래 모서리에는 타일을 원하는 크기로 끌어서 조절할 수 있는 “핸들”이 포함됩니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="타일 크기 메뉴가 열린 대시보드의 스크린샷":::

1. 타일을 선택하고 그리드의 새 위치로 끌어 대시보드를 정렬합니다.

### <a name="set-and-override-dashboard-filters"></a>대시보드 필터 설정 및 재정의

대시보드 상단에는 대시보드에 표시되는 데이터의 **자동 새로 고침** 및 **시간 설정** 을 설정하는 옵션과 함께 추가 필터를 추가하는 옵션이 있습니다.

:::image type="content" source="media/azure-portal-dashboards/dashboard-global-filters.png" alt-text="대시보드의 전역 필터를 보여 주는 스크린샷":::

기본적으로 데이터는 매시간 새로 고쳐집니다. 이를 변경하려면 **자동 새로 고침** 을 선택하고 새 새로 고침 간격을 선택합니다. 선택한 후 **적용** 을 선택합니다.

기본 시간 설정은 **UTC 시간** 이며 **지난 24 시간** 에 대한 데이터를 표시합니다. 이를 변경하려면 단추를 선택하고 새 시간 범위, 시간 단위 및/또는 시간대를 선택한 다음 **적용** 을 선택합니다.

추가 필터를 적용하려면 **필터 추가** 를 선택합니다. 표시되는 옵션은 대시보드의 타일에 따라 달라집니다. 예를 들어 특정 구독 또는 위치에 대한 데이터만 표시할 수 있습니다. 사용할 필터를 선택하고 원하는 필터를 선택합니다. 그러면 필터가 데이터에 적용됩니다. 필터를 제거하려면 해당 단추에서 **X** 를 선택합니다.

필터링을 지원하는 타일에는 타일의 왼쪽 상단 모서리에 ![필터 아이콘](./media/azure-portal-dashboards/dashboard-filter.png) 필터 아이콘이 있습니다. 일부 타일에서는 해당 타일에 특정한 필터를 사용하여 전역 필터를 재정의할 수 있습니다. 이렇게 하려면 컨텍스트 메뉴에서 **타일 데이터 구성** 을 선택하거나 필터 아이콘을 선택한 다음 원하는 필터를 적용합니다.

특정 타일에 대한 필터를 설정하는 경우 해당 타일의 왼쪽 모서리에는 해당 타일의 데이터가 자체 필터를 반영함을 나타내는 이중 필터 아이콘이 표시됩니다.

:::image type="content" source="media/azure-portal-dashboards/dashboard-filter-override.png" alt-text="필터 재정의가 있는 타일의 아이콘을 보여 주는 스크린샷":::

## <a name="modify-tile-settings"></a>타일 설정 수정

일부 타일에는 원하는 정보를 표시하는 추가 구성이 필요할 수 있습니다. 예를 들어 **메트릭 차트** 타일은 Azure Monitor의 메트릭을 표시하도록 설정되어야 합니다. 또한 타일 데이터를 사용자 지정하여 대시보드의 기본 시간 설정 및 필터를 재정의할 수 있습니다.

### <a name="complete-tile-configuration"></a>타일 구성 완료

설정해야 하는 타일은 타일을 사용자 지정할 때까지 배너가 표시됩니다. 예를 들어, **메트릭 차트** 에서 배너는 **메트릭에서 편집** 을 읽습니다. 다른 배너는 **타일 구성** 과 같은 다른 텍스트를 사용할 수 있습니다.

타일을 사용자 지정하려면 다음을 수행합니다.

1. 페이지 머리글에서 **저장** 을 선택하여 편집 모드를 종료합니다.

1. 배너를 선택하고 필요한 설정을 수행합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-configure-tile.png" alt-text="구성이 필요한 타일의 스크린샷":::

### <a name="customize-time-span-for-a-tile"></a>타일의 시간 범위 사용자 지정

대시보드의 데이터는 전역 필터에 따라 활동 및 새로 고침을 표시합니다. 일부 타일을 사용하면 하나의 타일에 대해 다른 시간 범위를 선택할 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 바로 가기 메뉴나 타일의 왼쪽 위 모서리에 있는 ![필터 아이콘](./media/azure-portal-dashboards/dashboard-filter.png)에서 **타일 데이터 사용자 지정** 을 선택합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-customize-tile-data.png" alt-text="타일 바로 가기 메뉴 스크린샷":::

1. **타일 수준에서 대시보드 시간 설정 재정의** 로 확인란을 선택합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-override-time-settings.png" alt-text="타일 시간 설정을 구성하는 대화 상자 스크린샷":::

1. 이 타일에 표시할 시간 범위를 정합니다. 지난 30분에서 지난 30일 사이에서 선택하거나 사용자 지정 범위를 정의할 수 있습니다.

1. 표시할 시간 세분성을 선택합니다.  1분에서 1개월까지 어디든 표시할 수 있습니다.

1. **적용** 을 선택합니다.

### <a name="change-the-title-and-subtitle-of-a-tile"></a>타일의 제목 및 부제목 변경

일부 타일에서는 제목과 부제목을 편집할 수 있습니다. 이렇게 하려면 상황에 맞는 메뉴에서 **타일 설정 구성** 을 선택합니다.

:::image type="content" source="media/azure-portal-dashboards/dashboard-tile-rename.png" alt-text="타일 설정 구성 옵션을 보여 주는 스크린샷":::

타일의 제목 및/또는 부제목을 변경한 다음, **적용** 을 선택합니다.

:::image type="content" source="media/azure-portal-dashboards/dashboard-title-subtitle.png" alt-text="타일의 제목과 부제목을 변경하는 방법을 보여 주는 스크린샷":::
 
## <a name="delete-a-tile"></a>타일 삭제

대시보드에서 타일을 제거하려면 다음 중 하나를 수행합니다.

- 타일의 오른쪽 위 모서리에 있는 바로 가기 메뉴를 선택한 다음 **대시보드에서 제거** 를 선택합니다.

- ![편집 아이콘 ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **편집** 을 선택해 사용자 지정 모드로 전환합니다. 타일의 오른쪽 위 모서리를 마우스로 가리킨 다음 ![삭제 아이콘](./media/azure-portal-dashboards/dashboard-delete-icon.png)을 선택해 대시보드에서 타일을 제거합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-tile.png" alt-text="대시보드에서 타일을 제거하는 방법을 보여 주는 스크린샷":::

## <a name="clone-a-dashboard"></a>대시보드 복제

기존 대시보드를 새 대시보드의 템플릿으로 사용하려면 다음 단계를 수행합니다.

1. 대시보드 보기에 복사하려는 대시보드가 표시되는지 확인합니다.

1. 페이지 머리글에서 ![복제 아이콘](./media/azure-portal-dashboards/dashboard-clone.png) **복제** 를 선택합니다.

1. 대시보드 복사본(*대시보드 이름의* **복제본**)이 편집 모드에서 열립니다. 이 문서의 이전 단계를 이용해 대시보드 이름을 바꾸고 사용자 지정합니다.

## <a name="publish-and-share-a-dashboard"></a>대시보드 게시 및 공유

대시보드를 만들 때는 기본적으로 비공개입니다. 즉, 사용자 자신만이 이를 볼 수 있는 유일한 사용자입니다. 다른 사용자가 대시보드를 사용할 수 있도록 하려면 해당 대시보드를 게시하고 공유할 수 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어를 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)를 참조하세요.

### <a name="open-a-shared-dashboard"></a>공유 대시보드 열기

공유 대시보드를 찾아서 열려면 다음 단계를 수행합니다.

1. 대시보드 이름 옆의 화살표를 선택합니다.

1. 표시된 대시보드 목록에서 선택합니다. 열려는 대시보드가 목록에 없는 경우.

    1. **모든 대시보드 찾아보기** 를 선택합니다.

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse.png" alt-text="대시보드 선택 메뉴 스크린샷":::

    1. **유형** 필드에서 **공유 대시보드** 를 선택합니다.

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse-all.png" alt-text="모든 대시보드 선택 메뉴의 스크린샷":::

    1. 하나 이상의 구독을 선택합니다. 텍스트를 입력하여 이름별로 대시보드를 필터링할 수도 있습니다.

    1. 공유 대시보드 목록에서 대시보드를 선택합니다.

## <a name="delete-a-dashboard"></a>대시보드 삭제

프라이빗 또는 공유 대시보드를 영구적으로 삭제하려면 다음 단계를 수행합니다.

1. 대시보드 이름 옆의 목록에서 삭제하려는 대시보드를 선택합니다.

1. 페이지 머리글에서 ![삭제 아이콘](./media/azure-portal-dashboards/dashboard-delete-icon.png) **삭제** 를 선택합니다.

1. 개인 대시보드의 경우 확인 대화 상자에서 **확인** 을 선택해 대시보드를 제거합니다. 공유 대시보드의 경우 확인 대화 상자에서 확인란을 선택하여 게시된 대시보드가 더 이상 다른 사용자에게 표시되지 않음을 확인합니다. 그런 다음 **확인** 을 선택합니다.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-dash.png" alt-text="삭제 확인 스크린샷":::

## <a name="recover-a-deleted-dashboard"></a>삭제된 대시보드 복구

전체 Azure 클라우드에 있는 경우 Azure Portal에서 _게시된_ 대시보드를 삭제하면 삭제 후 14일 내에 해당 대시보드를 복구할 수 있습니다. 자세한 내용은 [Azure Portal에서 삭제된 대시보드 복구](recover-shared-deleted-dashboard.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 기반 액세스 제어를 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)
- [Azure 대시보드를 프로그래밍 방식으로 만들기](azure-portal-dashboards-create-programmatically.md)
