---
title: '빠른 시작: 컬렉션 만들기'
description: 컬렉션은 Azure Purview의 액세스 제어와 자산 구성에 사용됩니다. 이 문서에서는 컬렉션을 만들고 권한을 추가하고 원본을 등록하고 자산을 컬렉션에 등록하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: template-quickstart
ms.openlocfilehash: 5dff616c6fa7ee8e72a932da281935f77db19010
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853703"
---
# <a name="quickstart-create-a-collection-and-assign-permissions-in-purview"></a>빠른 시작: Purview에서 컬렉션 만들기 및 권한 할당

컬렉션은 자산, 원본 및 정보에 대한 소유권과 액세스 제어를 관리하는 데 사용되는 Azure Purview 도구입니다. 또한 컬렉션은 고객의 관리 환경이 고객의 데이터와 일치하도록 사용자 지정된 범주로 원본 및 자산을 구성합니다. 이 가이드에서는 첫 번째 컬렉션 및 컬렉션 관리자를 설정하여 조직의 Purview 환경을 준비하는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 사용자 고유의 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* 활성 [Purview 리소스](create-catalog-portal.md).

## <a name="check-permissions"></a>사용 권한 확인

Purview에서 컬렉션을 만들고 관리하려면 Purview에서 **컬렉션 관리자** 여야 합니다. 이러한 권한은 [Purview Studio](use-purview-studio.md)에서 확인할 수 있습니다. 이 스튜디오는 [Azure Portal](https://portal.azure.com)에서 Purview 리소스로 이동한 다음, [개요] 페이지에서 **Purview Studio 열기** 타일을 선택하여 찾을 수 있습니다.

1. 왼쪽 창에서 데이터 맵 > 컬렉션을 선택하여 컬렉션 관리 페이지를 엽니다.

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="데이터 맵이 열리고 컬렉션 탭이 선택된 Purview 스튜디오 창의 스크린샷" border="true":::

1. 루트 컬렉션을 선택합니다. 루트 컬렉션은 컬렉션 목록의 최상위 컬렉션이며 Purview 리소스와 동일한 이름을 사용합니다. 아래 예제에서는 루트 컬렉션의 이름이 Contoso Purview입니다.

    :::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="데이터 맵이 열리고 루트 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 컬렉션 창에서 역할 할당을 선택합니다.

    :::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="데이터 맵이 열리고 [역할 할당] 탭이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 컬렉션을 만들려면 역할 할당 아래의 컬렉션 관리자 목록에 있어야 합니다. Purview 리소스를 만든 사람은 이미 루트 컬렉션 아래에 컬렉션 관리자로 나열되어 있을 것입니다. 나열되어 있지 않으면 컬렉션 관리자에게 권한을 부여해 달라고 문의해야 합니다.

    :::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="데이터 맵이 열리고 컬렉션 관리자 섹션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

## <a name="create-a-collection-in-the-portal"></a>포털에서 컬렉션 만들기

컬렉션 만들기는 [Purview Studio](use-purview-studio.md)에서 시작합니다. 이 스튜디오는 Azure Portal에서 Purview 리소스로 이동한 다음, [개요] 페이지에서 **Purview Studio 열기** 타일을 선택하여 찾을 수 있습니다.

1. 왼쪽 창에서 데이터 맵 > 컬렉션을 선택하여 컬렉션 관리 페이지를 엽니다.

    :::image type="content" source="./media/quickstart-create-collection/find-collections-2.png" alt-text="데이터 맵이 열리고 [컬렉션] 탭이 선택된 Purview Studio 창의 스크린샷" border="true":::

1. **+ 컬렉션 추가** 를 선택합니다.

    :::image type="content" source="./media/quickstart-create-collection/select-add-collection.png" alt-text="데이터 맵이 열리고 컬렉션 탭이 선택된 상태에서 컬렉션 추가가 강조 표시된 Purview 스튜디오 창의 스크린샷" border="true":::

1. 오른쪽 패널에서 컬렉션 이름과 설명을 입력하고, 컬렉션 관리자로 추가할 사용자를 검색합니다.

    :::image type="content" source="./media/quickstart-create-collection/create-collection.png" alt-text="표시 이름 및 컬렉션 관리자를 선택하고 [만들기] 단추가 강조 표시된 새 컬렉션 창을 보여주는 Purview Studio 창의 스크린샷" border="true":::

1. **만들기** 를 선택합니다. 컬렉션 정보가 페이지에 반영됩니다.

    :::image type="content" source="./media/quickstart-create-collection/created-collection.png" alt-text="새로 만든 컬렉션 창을 보여주는 Purview Studio 창의 스크린샷" border="true":::

## <a name="assign-permissions-to-collection"></a>컬렉션에 권한 할당

이제 컬렉션이 생겼으므로, 이 컬렉션에 권한을 할당하여 Purview에 대한 사용자 액세스를 관리할 수 있습니다.

### <a name="roles"></a>역할

할당된 모든 역할은 역할이 적용되는 컬렉션 안에 있는 원본, 자산 및 기타 개체에 적용됩니다.

* **컬렉션 관리자** - 컬렉션 및 컬렉션 세부 정보를 편집하고, 컬렉션의 액세스를 관리하고, 하위 컬렉션을 추가할 수 있습니다.
* **데이터 원본 관리자** - 데이터 원본 및 데이터 검색을 관리할 수 있습니다.
* **데이터 큐레이터** - 카탈로그 데이터 개체에 대한 만들기, 읽기, 수정 및 삭제 작업을 수행할 수 있습니다.
* **데이터 읽기 권한자** - 카탈로그 데이터 개체에 액세스할 수 있지만 수정할 수는 없습니다.

### <a name="assign-permissions"></a>권한 할당

1. **역할 할당** 탭을 선택하여 컬렉션의 모든 역할을 봅니다.

    :::image type="content" source="./media/quickstart-create-collection/select-role-assignments.png" alt-text="[역할 할당] 탭이 강조 표시된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

1. **역할 할당 편집** 또는 사람 아이콘을 선택하여 각 역할 멤버를 편집합니다.

    :::image type="content" source="./media/quickstart-create-collection/edit-role-assignments.png" alt-text="역할 할당 편집 드롭다운 목록이 선택된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

1. 역할 멤버에 추가할 사용자를 텍스트 상자에 입력하여 검색합니다. **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

이제 컬렉션이 생겼으므로, 아래 지침에 따라 리소스를 추가하고, 검색하고, 컬렉션을 관리할 수 있습니다.

* [컬렉션에 원본 등록](how-to-create-and-manage-collections.md#register-source-to-a-collection)
* [컬렉션을 통한 액세스 관리](how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)
