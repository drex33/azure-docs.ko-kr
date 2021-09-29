---
title: 컬렉션을 만들고 관리하는 방법
description: 이 문서에서는 Azure Purview에서 컬렉션을 만들고 관리하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 0a684cec230766119345b8fd8acd3df53cc3f25a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213017"
---
# <a name="create-and-manage-collections-in-azure-purview"></a>Azure Purview에서 컬렉션 만들기 및 관리

> [!NOTE]
> 현재 이 가이드는 **2021년 8월 18일 이후에** 만든 Azure Purview 인스턴스에만 적용됩니다. 8월 18일 전에 만든 인스턴스는 컬렉션을 만들 수 있지만, 해당 컬렉션을 통해 권한을 관리할 수는 없습니다. 8월 18일 전에 만든 Purview 인스턴스에 대한 컬렉션을 만드는 방법에 대한 내용은 페이지 맨 아래에 있는 [**레거시 컬렉션 가이드**](#legacy-collection-guide)를 참조하세요.
>
> 모든 레거시 계정은 돌아오는 주에 자동으로 업그레이드됩니다. Purview 계정이 업그레이드되면 메일 알림을 받게 됩니다. 계정이 업그레이드되면 변경되는 사항에 대한 자세한 내용은 [업그레이드된 계정 가이드](concept-account-upgrade.md)를 참조하세요.

Azure Purview의 컬렉션은 비즈니스 흐름에 따라 자산 및 원본을 구성하는 데 사용할 수 있지만 Purview에서 액세스를 관리하는 데 사용되는 도구이기도 합니다. 이 가이드에서는 이러한 컬렉션의 생성 및 관리를 안내할 뿐만 아니라 원본을 등록하고 컬렉션에 자산을 추가하는 방법에 대한 단계를 다룹니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 사용자 고유의 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* 활성 [Purview 리소스](create-catalog-portal.md).

### <a name="check-permissions"></a>사용 권한 확인

Purview에서 컬렉션을 만들고 관리하려면 Purview에서 **컬렉션 관리자** 여야 합니다. 이러한 권한은 [Purview Studio](https://web.purview.azure.com/resource/)에서 확인할 수 있습니다. 이 스튜디오는 [Azure Portal](https://portal.azure.com)에서 Purview 리소스로 이동한 다음, 개요 페이지에서 Purview Studio 열기 타일을 선택하여 찾을 수 있습니다.

1. 왼쪽 창에서 데이터 맵 > 컬렉션을 선택하여 컬렉션 관리 페이지를 엽니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="데이터 맵이 열리고 컬렉션 탭이 선택된 Purview Studio 창의 스크린샷" border="true":::

1. 루트 컬렉션을 선택합니다. 루트 컬렉션은 컬렉션 목록의 최상위 컬렉션이며 Purview 리소스와 동일한 이름을 사용합니다. 아래 예제에서는 루트 컬렉션의 이름이 Contoso Purview입니다. 또는 컬렉션이 이미 있는 경우 하위 컬렉션을 만들려는 컬렉션을 선택할 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-root-collection.png" alt-text="데이터 맵이 열리고 루트 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 컬렉션 창에서 **역할 할당** 을 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/role-assignments.png" alt-text="데이터 맵이 열리고 역할 할당 탭이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 컬렉션을 만들려면 역할 할당 아래의 컬렉션 관리자 목록에 있어야 합니다. Purview 리소스를 만든 사람은 이미 루트 컬렉션 아래에 컬렉션 관리자로 나열되어 있을 것입니다. 나열되어 있지 않으면 컬렉션 관리자에게 권한을 부여해 달라고 문의해야 합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-admins.png" alt-text="데이터 맵이 열리고 컬렉션 관리자 섹션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

## <a name="collection-management"></a>컬렉션 관리

### <a name="create-a-collection"></a>컬렉션 만들기

컬렉션을 만들려면 컬렉션 관리자여야 합니다. 확실하지 않은 경우 [위의 가이드](#check-permissions)에 따라 권한을 확인합니다.

1. 왼쪽 창에서 데이터 맵 > 컬렉션을 선택하여 컬렉션 관리 페이지를 엽니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="데이터 맵이 열리고 컬렉션 탭이 선택되어 열린 Purview Studio 창의 스크린샷" border="true":::

1. **+ 컬렉션 추가** 를 선택합니다. 다시 말하지만, [컬렉션 관리자](#check-permissions)만 컬렉션을 관리할 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-add-a-collection.png" alt-text="컬렉션 추가 단추가 강조 표시된 새 컬렉션 창을 보여 주는 Purview 스튜디오 창의 스크린샷" border="true":::

1. 오른쪽 패널에서 컬렉션 이름과 설명을 입력합니다. 필요한 경우 새 컬렉션에 컬렉션 관리자로 사용자나 그룹도 추가할 수 있습니다.
1. **만들기** 를 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/create-collection.png" alt-text="표시 이름 및 컬렉션 관리자를 선택하고 만들기 단추가 강조 표시된 새 컬렉션 창을 보여주는 Purview Studio 창의 스크린샷" border="true":::

1. 새 컬렉션 정보가 페이지에 반영됩니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/created-collection.png" alt-text="새로 만든 컬렉션 창을 보여 주는 Purview Studio 창의 스크린샷" border="true":::

### <a name="edit-a-collection"></a>컬렉션 편집

1. 컬렉션 세부 정보 페이지나 컬렉션의 드롭다운 메뉴에서 **편집** 을 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-collection.png" alt-text="선택한 컬렉션 창과 컬렉션 이름 옆에 있는 줄임표 단추 아래에 ‘편집’ 단추가 강조 표시된 컬렉션 창에 열린 Purview 스튜디오 창의 스크린샷" border="true":::

1. 현재 컬렉션 설명과 컬렉션 관리자는 편집할 수 있습니다. 변경한 다음, **저장** 을 선택하여 변경 내용을 저장합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-description.png" alt-text="컬렉션 편집 창이 열려 있고 컬렉션에 설명이 추가되어 있으며 저장 단추가 강조 표시된 Purview 스튜디오 창의 스크린샷" border="true":::

### <a name="view-collections"></a>컬렉션 보기

1. 컬렉션 이름 옆에 있는 삼각형 아이콘을 선택하여 컬렉션 계층 구조를 확장하거나 축소합니다. 살펴볼 컬렉션 이름을 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/subcollection-menu.png" alt-text="컬렉션 이름 옆에 단추가 강조 표시되어 있는 Purview 스튜디오 컬렉션 창의 스크린샷" border="true":::

1. 컬렉션을 필터링하려면 목록 맨 위에 있는 필터 상자에 입력합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/filter-collections.png" alt-text="컬렉션 위의 필터가 강조 표시된 Purview 스튜디오 컬렉션 창의 스크린샷" border="true":::

1. 루트 컬렉션의 컨텍스트 메뉴에서 **새로 고침** 을 선택하여 컬렉션 목록을 다시 로드합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-collections.png" alt-text="리소스 이름 옆에 있는 단추가 선택되고 새로 고침 단추가 강조 표시된 Purview 스튜디오 컬렉션 창의 스크린샷" border="true":::

1. 컬렉션 세부 정보 페이지에서 **새로 고침** 을 선택하여 단일 컬렉션을 다시 로드합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-single-collection.png" alt-text="컬렉션 창 아래에 새로 고침 단추가 강조 표시된 Purview 스튜디오 컬렉션 창의 스크린샷" border="true":::

## <a name="add-roles-and-restrict-access-through-collections"></a>컬렉션을 통해 역할 추가 및 액세스 제한

사용 권한은 Purview의 컬렉션을 통해 관리되므로 역할 및 사용자에게 제공되는 권한을 파악하는 것이 중요합니다. 컬렉션에 대한 권한이 부여된 사용자는 해당 컬렉션과 관련된 원본 및 자산에 액세스할 수 있을 뿐만 아니라, 하위 컬렉션에 대한 권한을 상속받습니다. 상속은 [제한될 수 있지만](#restrict-inheritance) 기본적으로 허용됩니다.

아래 가이드에서는 역할, 관리 방법 및 사용 권한 상속에 대해 설명합니다.

### <a name="roles"></a>역할

할당된 모든 역할은 역할이 적용되는 컬렉션 내의 원본, 자산 및 기타 개체에 적용됩니다.

* **컬렉션 관리자** - 컬렉션, 해당 세부 정보 및 하위 컬렉션을 편집할 수 있습니다. 또한 데이터 큐레이터, 데이터 판독기 및 기타 Purview 역할을 컬렉션 범위에 추가할 수 있습니다. 부모 컬렉션에서 자동으로 상속된 컬렉션 관리자는 제거할 수 없습니다.
* **데이터 원본 관리자** - 데이터 원본 및 데이터 검사를 관리할 수 있습니다.
* **데이터 큐레이터** - 카탈로그 데이터 개체의 작업을 만들고 읽고 수정하고 삭제할 수 있으며 개체 간의 관계를 설정할 수 있습니다.
* **데이터 읽기 권한자** - 카탈로그 데이터 개체에 액세스할 수 있지만 수정할 수는 없습니다.

### <a name="add-role-assignments"></a>역할 할당 추가

1. **역할 할당** 탭을 선택하여 컬렉션의 모든 역할을 봅니다. 컬렉션 관리자만 역할 할당을 관리할 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-role-assignments.png" alt-text="역할 할당 탭이 강조 표시된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

1. **역할 할당 편집** 또는 사람 아이콘을 선택하여 각 역할 멤버를 편집합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-role-assignments.png" alt-text="역할 할당 편집 드롭다운 목록이 선택된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

1. 역할 멤버에 추가할 사용자를 텍스트 상자에 입력하여 검색합니다. **X** 를 선택하여 추가하지 않을 멤버를 제거합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-user-permissions.png" alt-text="검색 표시줄이 강조 표시된 Purview 스튜디오 컬렉션 관리 창의 스크린샷" border="true":::

1. **확인** 을 선택하여 변경 사항을 저장하면 역할 할당 목록에 새 사용자가 반영되어 있음을 볼 수 있습니다.

### <a name="remove-role-assignments"></a>역할 할당 제거

1. 역할 할당을 제거하려면 사용자 이름 옆에 있는 **X** 단추를 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-role-assignment.png" alt-text="역할 할당 탭이 선택되고 이름 중 하나 옆에 있는 x 단추가 강조 표시된 Purview 스튜디오 컬렉션 창의 스크린샷" border="true":::

1. 사용자를 제거하려면 **확인** 을 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/confirm-remove.png" alt-text="확인 단추가 강조 표시된 확인 팝업 스크린샷" border="true":::

### <a name="restrict-inheritance"></a>상속 제한

컬렉션 권한은 부모 컬렉션에서 자동으로 상속됩니다. 예를 들어 루트 컬렉션(목록 상단에 있는 Purview 리소스와 이름이 같은 컬렉션)에 대한 모든 권한은 그 아래에 있는 모든 컬렉션에 상속됩니다. 상속된 권한 제한 옵션을 사용하여 언제든지 부모 컬렉션의 상속을 제한할 수 있습니다.

상속을 제한하면 제한된 컬렉션에 사용자를 직접 추가하여 액세스 권한을 부여해야 합니다.

1. 상속을 제한할 컬렉션으로 이동하고 **역할 할당** 탭을 선택합니다.
1. 이 컬렉션과 모든 하위 컬렉션에서 상속된 권한을 제거하려면 **상속된 권한 제한** 을 선택하고 팝업 대화 상자에서 **액세스 제한** 을 선택합니다. 컬렉션 관리자 권한에는 영향을 주지 않습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/restrict-access-inheritance.png" alt-text="역할 할당 탭이 선택되고 상속된 권한 제한 슬라이드 단추가 강조 표시된 Purview 스튜디오 컬렉션 창의 스크린샷" border="true":::

1. 제한 후에는 상속된 멤버가 컬렉션 관리자에 필요한 역할에서 제거됩니다.
1. 되돌리려면 **상속된 권한 제한** 토글 단추를 다시 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-restriction.png" alt-text="역할 할당 탭이 선택되고 상속된 권한 제한 해제 슬라이드 단추가 강조 표시된 Purview 스튜디오 컬렉션 창의 스크린샷" border="true":::
    
## <a name="register-source-to-a-collection"></a>컬렉션에 원본 등록

1. **등록** 을 선택하거나 컬렉션 노드에서 등록 아이콘을 선택하여 데이터 원본을 등록합니다. 데이터 원본 관리자만 원본을 등록할 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-by-collection.png" alt-text="페이지 상단과 컬렉션 아래에서 모두 등록 단추가 강조 표시된 데이터 맵 Purview 스튜디오 창의 스크린샷"border="true":::

1. 데이터 원본 이름 및 기타 원본 정보를 입력합니다.  양식의 아래쪽에는 검사 권한이 있는 모든 컬렉션이 나열됩니다. 한 컬렉션을 선택할 수 있습니다. 이 원본 아래의 모든 자산은 선택한 컬렉션에 속합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-source.png" alt-text="원본 등록 창의 스크린샷"border="true":::

1. 만든 데이터 원본은 선택한 컬렉션 아래에 배치됩니다. 데이터 원본을 보려면 **세부 정보 보기** 를 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/see-registered-source.png" alt-text="새로 추가된 원본 카드가 강조 표시된 데이터 맵 Purview 스튜디오 창의 스크린샷"border="true":::

1. **New scan**(새 검사)을 선택하여 데이터 원본 아래에 검사를 만듭니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/new-scan.png" alt-text="새 검사 단추가 강조 표시된 원본 Purview 스튜디오 창의 스크린샷"border="true":::

1. 마찬가지로 양식 하단에서 컬렉션을 선택할 수 있으며 검사한 모든 자산이 컬렉션에 포함됩니다.
여기에 나열된 컬렉션은 데이터 원본 컬렉션의 하위 컬렉션으로 제한됩니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/scan-under-collection.png" alt-text="컬렉션 드롭다운이 강조 표시된 새 검사 창의 스크린샷"border="true":::

1. 컬렉션 창으로 돌아오면 원본 카드의 컬렉션에 연결된 데이터 원본이 표시됩니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/source-under-collection.png" alt-text="새로 추가된 원본 카드가 맵에 강조 표시된 데이터 맵 Purview 스튜디오 창의 스크린샷"border="true":::

## <a name="add-assets-to-collections"></a>컬렉션에 자산 추가

자산과 원본도 컬렉션과 연결됩니다. 검사가 컬렉션과 연결된 경우, 검사 중에 자산이 해당 컬렉션에 자동으로 추가되지만 하위 컬렉션에 수동으로 추가할 수도 있습니다.

1. 자산 세부 정보에서 컬렉션 정보를 확인합니다. 자산 세부 정보 페이지의 오른쪽 상단에 있는 **컬렉션 경로** 섹션에서 컬렉션 정보를 찾을 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-path.png" alt-text="컬렉션 경로가 강조 표시된 Purview 스튜디오 자산 창의 스크린샷" border="true":::

1. 자산 세부 정보 페이지의 권한:
    1. [위의 컬렉션에 대한 역할 추가 및 액세스 제한 가이드](#add-roles-and-restrict-access-through-collections)에 따라 컬렉션 기반 권한 모델을 확인하세요.
    1. 컬렉션에 대한 읽기 권한이 없으면 해당 컬렉션의 자산이 검색 결과에 나열되지 않습니다. 한 자산의 직접 URL을 가져와 열면 액세스 금지 페이지가 표시됩니다. 이 경우 액세스 권한을 부여하도록 Purview 관리자에게 문의하세요. **새로 고침** 단추를 선택하여 권한을 다시 확인할 수 있습니다.

        :::image type="content" source="./media/how-to-create-and-manage-collections/no-access.png" alt-text="사용자에게 권한이 없고 정보나 옵션에 액세스할 수 없는 Purview 스튜디오 자산 창의 스크린샷" border="true":::

    1. 한 컬렉션에 대한 읽기 권한이 있지만 쓰기 권한이 없는 경우 자산 세부 정보 페이지를 살펴볼 수 있지만 다음 작업은 수행할 수 없습니다.
        * 자산을 편집합니다. **편집** 단추를 사용하지 않게 설정합니다.
        * 자산을 삭제합니다. **삭제** 단추를 사용하지 않게 설정합니다.
        * 자산을 다른 컬렉션으로 이동합니다. 컬렉션 경로 섹션의 오른쪽 위 모서리에 있는 줄임표 단추는 숨겨집니다.
    1. **계층 구조** 섹션의 자산도 권한의 영향을 받습니다. 읽기 권한이 없는 자산은 회색으로 표시됩니다.

        :::image type="content" source="./media/how-to-create-and-manage-collections/hierarchy-permissions.png" alt-text="사용자가 읽기 권한만 있고 옵션에는 액세스할 수 없는 Purview Studio 계층 구조 창의 스크린샷" border="true":::

### <a name="move-asset-to-another-collection"></a>자산을 다른 컬렉션으로 이동

1. 컬렉션 경로 섹션의 오른쪽 위 모서리에 있는 줄임표 단추를 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-asset.png" alt-text="컬렉션 경로가 강조 표시되고 컬렉션 경로 옆에 있는 줄임표 단추가 선택된 Purview 스튜디오 자산 창의 스크린샷" border="true":::

1. **다른 컬렉션으로 이동** 단추를 선택합니다.
1. 오른쪽 패널에서 이동할 대상 컬렉션을 선택합니다. 쓰기 권한이 있는 컬렉션만 볼 수 있습니다. 또한 데이터 원본 컬렉션의 하위 컬렉션에만 자산을 추가할 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-select-collection.png" alt-text="컬렉션 선택 드롭다운 메뉴가 강조 표시된 Purview 스튜디오 팝업 창의 스크린샷" border="true":::

1. 창 하단의 **이동** 단추를 선택하여 자산을 이동합니다.

## <a name="search-and-browse-by-collections"></a>컬렉션별 검색 및 찾아보기

### <a name="search-by-collection"></a>컬렉션별 검색

1. Azure Purview에서 검색 창은 Purview studio UX 상단에 위치합니다.

   :::image type="content" source="./media/how-to-create-and-manage-collections/purview-search-bar.png" alt-text="Azure Purview 검색 창의 위치를 보여 주는 스크린샷" border="true":::

1. 검색 창을 선택하면 최근 검색 기록과 최근에 액세스한 자산을 확인할 수 있습니다. 최근에 본 모든 자산을 확인하려면 **모두 보기** 를 선택합니다.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-no-keywords.png" alt-text="아무 키워드도 입력하지 않은 검색 창을 보여 주는 스크린샷" border="true":::

1. 이름, 데이터 형식, 분류, 용어집 용어 등의 자산을 식별하는 데 도움이 되는 키워드를 입력합니다. 원하는 자산과 관련된 키워드를 입력하면 Azure Purview는 검색 및 잠재적인 자산 일치 항목에 대한 제안을 표시합니다. 검색을 완료하려면 **검색 결과 보기** 를 선택하거나 **Enter** 키를 누릅니다.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-keywords.png" alt-text="사용자가 키워드를 입력하는 동안의 검색 창을 보여 주는 스크린샷" border="true":::

1. 검색 결과 페이지에는 관련성 순서로 제공된 키워드와 일치하는 자산 목록이 표시됩니다. 다양한 요인이 자산의 관련성 점수에 영향을 줄 수 있습니다. 찾고 있는 자산에 적용되는 특정 컬렉션, 데이터 저장소, 분류, 연락처, 레이블 및 용어집 용어를 선택하여 목록을 더 자세히 필터링할 수 있습니다.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-results.png" alt-text="검색 결과를 보여 주는 스크린샷" border="true":::

1. 스키마, 계보, 자산 소유자를 비롯한 속성을 볼 수 있는 자산 세부 정보 페이지를 확인하려면 원하는 자산을 선택합니다.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-by-collection.png" alt-text="컬렉션이 있는 검색 결과를 보여 주는 스크린샷" border="true":::

### <a name="browse-by-collection"></a>컬렉션별 찾아보기

1. 홈페이지에서 **자산 찾아보기** 를 선택하여 데이터 자산을 찾아볼 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/browse-by-collection.png" alt-text="자산 찾아보기 단추가 강조 표시된 Purview Studio 카탈로그 창의 스크린샷" border="true":::

1. 자산 찾아보기 페이지에서 **컬렉션별** 피벗을 선택합니다. 컬렉션은 계층적 테이블 뷰로 나열됩니다. 각 컬렉션의 자산을 추가로 살펴보려면 해당 컬렉션 이름을 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/by-collection-view.png" alt-text="컬렉션별 탭이 선택된 자산 Purview Studio 창의 스크린샷"border="true":::

1. 다음 페이지에서는 선택한 컬렉션 아래에 있는 자산의 검색 결과가 표시됩니다. 패싯 필터를 선택하여 결과 범위를 좁힐 수 있습니다. 또는 하위/관련 컬렉션 이름을 선택하여 다른 컬렉션의 자산을 볼 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-results-by-collection.png" alt-text="컬렉션별 탭이 선택된 Purview Studio 카탈로그 창의 스크린샷"border="true":::

1. 자산의 세부 정보를 보려면 검색 결과에서 자산 이름을 선택합니다. 또는 자산을 확인하고 대량 편집할 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/view-asset-details.png" alt-text="컬렉션별 탭이 선택되고 자산 확인란이 강조 표시된 Purview Studio 카탈로그 창의 스크린샷"border="true":::

## <a name="legacy-collection-guide"></a>레거시 컬렉션 가이드

> [!NOTE]
> 이 레거시 컬렉션 가이드는 2021년 8월 18일 전에 만든 Purview 인스턴스에만 적용됩니다. 이 날짜 이후에 만든 인스턴스는 위의 가이드를 따라야 합니다.

레거시 컬렉션은 데이터 맵의 원본만 구성하고 해당 원본에 대한 사용 권한은 관리하지 않습니다.

### <a name="create-a-legacy-collection"></a>레거시 컬렉션 만들기

1. 왼쪽 창에서 데이터 맵을 선택하여 데이터 맵을 엽니다. 맵 보기를 사용하여 컬렉션 및 해당 컬렉션 아래에 나열된 원본을 볼 수 있습니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-view.png" alt-text="데이터 맵이 열린 Purview Studio 창의 스크린샷" border="true":::

1. **+ 새 컬렉션** 을 선택합니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-create.png" alt-text="데이터 맵이 열리고 + 새 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 컬렉션 이름을 지정하고 부모 또는 ‘없음’을 선택합니다. **만들기** 를 선택합니다. 컬렉션 정보가 데이터 맵에 반영됩니다.

    :::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-name.png" alt-text="새 컬렉션 창을 보여주는 Purview Studio 창의 스크린샷" border="true":::

## <a name="next-steps"></a>다음 단계

이제 컬렉션이 생겼으므로, 아래 지침에 따라 리소스를 추가하고 검사할 수 있습니다.

* [데이터 원본 관리](manage-data-sources.md)

* [지원되는 데이터 원본](purview-connector-overview.md)

* [검사 및 수집](concept-scans-and-ingestion.md)
