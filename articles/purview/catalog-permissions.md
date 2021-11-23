---
title: 액세스 및 사용 권한 이해
description: 이 문서에서는 Azure 부서의 범위의 개요 권한, 액세스 제어 및 컬렉션을 제공 합니다. RBAC (역할 기반 액세스 제어)는 Azure 부서의 범위 자체 내에서 관리 되므로이 가이드에서는 정보를 보호 하기 위한 기본 사항을 다룹니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.topic: conceptual
ms.date: 11/22/2021
ms.openlocfilehash: 19e640cfb608b1d548047291a6674af897eade3e
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954527"
---
# <a name="access-control-in-azure-purview"></a>Azure Purview의 액세스 제어

Azure Purview는 **컬렉션** 을 사용하여 원본, 자산 및 기타 아티팩트의 액세스를 구성하고 관리합니다. 이 문서에서는 Azure Purview 계정의 컬렉션 및 액세스 관리에 대해 설명합니다.

## <a name="collections"></a>컬렉션

컬렉션이란 Azure Purview가 자산, 원본 및 기타 아티팩트를 더 쉽게 검색되도록 계층 구조로 그룹화하고 액세스 제어를 관리하는 데 사용하는 도구입니다. Purview의 리소스에 대한 모든 액세스는 Purview 계정 자체의 컬렉션에서 관리됩니다.

> [!NOTE]
> 2021 년 11 월 8 일부 터 ***Insights*** 는 데이터에 액세스할 수 있습니다. 데이터 판독기는 Insights에 대 한 액세스 권한이 없습니다.
>
>
## <a name="roles"></a>역할

Azure Purview는 미리 정의된 역할 세트를 사용하여 누가 계정 내의 어떤 항목에 액세스할 수 있는지를 제어합니다. 이러한 역할은 현재 다음과 같습니다.

- **컬렉션 관리자** -Azure 부서의 범위에서 다른 사용자에 게 역할을 할당 하거나 컬렉션을 관리 해야 하는 사용자에 대 한 역할입니다. 컬렉션 관리자는 관리자 인 컬렉션의 역할에 사용자를 추가할 수 있습니다. 컬렉션 및 세부 정보를 편집 하 고 하위 컬렉션을 추가할 수도 있습니다.
- **데이터 curators** 자산을 관리 하 고, 사용자 지정 분류를 구성 하 고, 용어를 설정 하 고, 정보를 볼 수 있는 데이터 카탈로그에 대 한 액세스를 제공 하는 역할입니다. 데이터는 자산을 만들고, 읽고, 수정 하 고, 이동 하 고, 삭제할 수 있습니다. 또한 자산에 주석을 적용할 수 있습니다.
- **데이터 판독기** -데이터 자산, 분류, 분류 규칙, 컬렉션 및 용어집 용어에 대 한 읽기 전용 액세스를 제공 하는 역할입니다.
- **데이터 원본 관리자** -사용자가 데이터 원본과 검색을 관리할 수 있는 역할입니다. 사용자가 지정 된 데이터 원본에 대 한 **데이터 원본 관리자** 역할에만 부여 된 경우 기존 검색 규칙을 사용 하 여 새 검색을 실행할 수 있습니다. 새 스캔 규칙을 만들려면 사용자도 **데이터 판독기** 또는 **데이터 큐레이터** 역할로 부여 되어야 합니다.

## <a name="who-should-be-assigned-to-what-role"></a>누가 어떤 역할에 할당되어야 하나요?

|사용자 시나리오|적절한 역할|
|-------------|-----------------|
|그저 자산을 찾을 수 있어야 하고, 아무것도 편집하지 않겠습니다.|데이터 읽기 권한자|
|자산에 대한 정보를 편집하고, 분류를 할당하고, 용어집 항목과 연결하는 등의 작업을 수행해야 합니다.|데이터 큐레이터|
|용어집을 편집하거나 새 분류 정의를 설정해야 합니다.|데이터 큐레이터|
|내 데이터 공간에 대 한 거 버 넌 스를 이해 하기 위해 Insights를 확인 해야 합니다.|데이터 큐레이터|
|애플리케이션의 서비스 주체는 Azure Purview에 데이터를 푸시해야 합니다.|데이터 큐레이터|
|Purview Studio를 통해 검사를 설정해야 합니다.|원본이 등록 된 컬렉션 **또는** 데이터 큐레이터 **및** 데이터 원본 관리자에 대 한 데이터 큐레이터|
|서비스 사용자 또는 그룹이 카탈로그의 정보에 액세스하도록 허용하지 않으면서 Azure Purview에서 검사를 설정하고 모니터링할 수 있도록 설정해야 합니다. |데이터 원본 관리자|
|Azure Purview에서 사용자를 역할에 배치해야 합니다. | 컬렉션 관리자 |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles.png" alt-text="Purview 역할을 보여 주는 차트" lightbox="./media/catalog-permissions/collection-permissions-roles.png":::

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>Azure Purview의 역할 및 컬렉션을 사용하는 방법 이해

모든 액세스 제어는 Purview의 컬렉션에서 관리됩니다. Purview의 컬렉션은 [Purview Studio](https://web.purview.azure.com/resource/)에서 찾을 수 있습니다. [Azure Portal](https://portal.azure.com)에서 Purview 계정을 열고 [개요] 페이지에서 Purview Studio 타일을 선택합니다. 여기서 왼쪽 메뉴의 데이터 맵으로 이동한 다음, '컬렉션' 탭을 선택합니다.

만들어진 Azure Purview 계정은 Purview 계정 자체와 동일한 이름을 가진 루트 컬렉션으로 시작합니다. Purview 계정의 작성자는 자동으로 이 루트 컬렉션의 컬렉션 관리자, 데이터 원본 관리자, 데이터 큐레이터 및 데이터 읽기 권한자로 추가되며, 이 컬렉션을 편집하고 관리할 수 있습니다.

원본, 자산 및 개체를 이 루트 컬렉션에 직접 추가할 수 있지만 다른 컬렉션에 추가할 수도 있습니다. 컬렉션을 추가하면 Purview 계정에서 데이터에 액세스할 수 있는 사용자를 보다 세밀하게 제어할 수 있습니다.

그 외의 사용자는 위에서 언급한 역할 중 하나가 할당되면 Azure Purview 계정 또는 자신이 속한 그룹의 정보에만 액세스할 수 있습니다. 즉, Azure Purview 계정을 만들 때 계정 작성자를 제외한 어느 누구도 [컬렉션에서 위에서 언급한 역할 중 하나 이상이 할당](how-to-create-and-manage-collections.md#add-role-assignments)되기 전에는 API에 액세스하거나 사용할 수 있습니다.

컬렉션 관리자 또는 권한 상속을 통해서만 사용자를 컬렉션에 추가할 수 있습니다. 부모 컬렉션의 권한은 해당 하위 컬렉션에 자동으로 상속됩니다. 그러나 컬렉션에 대한 [권한 상속을 제한](how-to-create-and-manage-collections.md#restrict-inheritance)할 수 있습니다. 권한 상속을 제한하면 해당 하위 컬렉션이 더 이상 부모 컬렉션에서 권한을 상속하지 않으며 직접 추가해야 합니다. 다만 부모 컬렉션에서 자동으로 상속되는 컬렉션 관리자를 제거할 수 없습니다.

부서의 범위 계정의 구독과 연결 된 Azure Active Directory에서 사용자, 보안 그룹 및 서비스 사용자에 게 부서의 범위 역할을 할당할 수 있습니다.

## <a name="assign-permissions-to-your-users"></a>사용자에게 권한 할당

Azure Purview 계정을 만든 후 가장 먼저 할 일은 컬렉션을 만들고 해당 컬렉션 내에서 사용자에게 역할을 할당하는 것입니다.

> [!NOTE]
> 서비스 주체를 사용 하 여 Azure 부서의 범위 계정을 만든 경우 부서의 범위 Studio에 액세스 하 고 사용자에 게 사용 권한을 할당 하려면 루트 컬렉션에 대 한 사용자 컬렉션 관리자 권한을 부여 해야 합니다.
> [이 Azure CLI 명령을](/cli/azure/purview/account#az_purview_account_add_root_collection_admin)사용할 수 있습니다.
>
>   ```azurecli
>   az purview account add-root-collection-admin --account-name --resource-group [--object-id]
>   ```
>

### <a name="create-collections"></a>컬렉션 만들기

컬렉션은 Purview 계정의 원본 구조에 맞게 사용자 지정할 수 있으며, 이러한 리소스에 대해 구성된 스토리지 빈처럼 작동할 수 있습니다. 필요한 컬렉션에 대해 생각할 때 사용자가 정보에 액세스하거나 정보를 검색하는 방법을 고려해야 합니다. 원본이 부서별로 구분되나요? 부서 내에 일부 자산을 검색하기만 하면 되는 특수 그룹이 있나요? 모든 사용자가 검색할 수 있어야 하는 원본이 있나요?

이러한 질문에 답하면 데이터 맵을 가장 효과적으로 구성하는 데 필요한 컬렉션 및 하위 컬렉션을 알 수 있습니다.

새 컬렉션을 데이터 맵에 직접 추가하고 드롭다운에서 새 컬렉션의 부모 컬렉션을 선택할 수 있고, 부모 컬렉션에서 새 컬렉션을 하위 컬렉션으로 추가할 수도 있습니다. 데이터 맵 보기에서 컬렉션을 기준으로 정렬된 모든 원본 및 자산을 볼 수 있으며, 목록에는 원본의 컬렉션이 나열됩니다.

자세한 지침 및 정보는 [컬렉션을 만들고 관리하는 방법에 대한 가이드](how-to-create-and-manage-collections.md)를 참조하세요.

#### <a name="a-collections-example"></a>컬렉션 예제

컬렉션, 권한 및 작동 방식에 대한 기본적인 내용을 이해를 했으므로, 이제 예제를 살펴보겠습니다.

:::image type="content" source="./media/catalog-permissions/collection-example.png" alt-text="영역 및 부서별로 분할된 샘플 컬렉션 계층 구조를 보여주는 차트" border="true":::

보시는 것은 조직에서 데이터를 구조화하는 방법 중 하나입니다. 루트 컬렉션(이 예제에서는 Contoso)부터 시작하여 컬렉션은 지역을 기준으로 분류된 다음, 부서 및 하위 부서로 분류됩니다. 데이터 원본 및 자산을 이러한 컬렉션 중 하나에 추가하여 지역 및 부서별로 데이터 리소스를 구성하고 이러한 라인에 따라 액세스 제어를 관리할 수 있습니다. 액세스 지침이 엄격한 하위 부서인 Revenue가 있으므로 권한을 세밀하게 관리해야 합니다.

[데이터 읽기 권한자 역할](#roles)은 카탈로그 내의 정보에 액세스할 수 있지만 관리하거나 편집할 수는 없습니다. 따라서 위의 예제에서 루트 컬렉션의 그룹에 데이터 읽기 권한자 권한을 추가하고 상속을 허용하면 해당 그룹의 모든 사용자에게 Purview 원본 및 자산에 대한 읽기 권한자 권한이 부여됩니다. 이렇게 하면 해당 그룹의 모든 사용자가 해당 리소스를 검색할 수 있지만 편집할 수는 없습니다. Revenue 그룹에서 [상속을 제한](how-to-create-and-manage-collections.md#restrict-inheritance)하면 이러한 자산에 대한 액세스를 제어할 수 있습니다. 수익 정보에 액세스해야 하는 사용자를 Revenue 컬렉션에 별도로 추가할 수 있습니다.
데이터 큐레이터 및 데이터 원본 관리자 역할과 마찬가지로 이러한 그룹의 권한은 이들이 할당된 컬렉션에서 시작되며 상속을 제한하지 않는 하위 컬렉션으로 전달됩니다. 아래는 Americas 하위 컬렉션의 컬렉션 수준에서 여러 그룹에 대한 권한이 할당된 것을 보여주는 그림입니다.

:::image type="content" source="./media/catalog-permissions/collection-permissions-example.png" alt-text="권한 분포를 보여주는 지역 및 부서별로 분류된 샘플 컬렉션 계층 구조를 보여주는 차트" border="true":::

### <a name="add-users-to-roles"></a>역할에 사용자 추가

역할 할당은 컬렉션을 통해 관리됩니다. [컬렉션 관리자 역할](#roles)이 할당된 사용자만이 해당 컬렉션의 다른 사용자에게 권한을 부여할 수 있습니다. 새 권한을 추가 해야 하는 경우 컬렉션 관리자는 [부서의 범위 Studio](https://web.purview.azure.com/resource/)에 액세스 하 고, 데이터 맵으로 이동한 다음, 컬렉션 탭으로 이동 하 고, 사용자를 추가 해야 하는 컬렉션을 선택 합니다. [역할 할당] 탭에서는 권한이 필요한 사용자를 추가하고 관리할 수 있습니다.

전체 지침은 [역할 할당을 추가하는 방법 가이드](how-to-create-and-manage-collections.md#add-role-assignments)를 참조하세요.

## <a name="next-steps"></a>다음 단계

컬렉션 및 액세스 제어의 기본 사항을 이해했으므로, 아래 가이드에 따라 해당 컬렉션을 만들고 관리하거나 원본을 Purview 리소스에 등록합니다.

- [컬렉션을 만들고 관리하는 방법](how-to-create-and-manage-collections.md)
- [Purview에서 지원하는 데이터 원본](purview-connector-overview.md)
