---
title: 카탈로그 권한 (미리 보기)
description: 이 문서에서는 Azure Purview에서 역할 기반 액세스 제어(RBAC)를 구성하는 방법에 대한 개요를 제공합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: c9c924b6188372da2ff772f7283f6ea47f89ef92
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124735933"
---
# <a name="access-control-in-azure-purview"></a>Azure Purview의 액세스 제어

Azure Purview는 **컬렉션** 을 사용하여 원본, 자산 및 기타 아티팩트의 액세스를 구성하고 관리합니다. 이 문서에서는 Azure Purview 계정의 컬렉션 및 액세스 관리에 대해 설명합니다.

> [!NOTE]
> 현재 이 정보는 **2021년 8월 18일 이후** 에 만든 Azure Purview 계정에만 적용됩니다. 8월 18일 전에 만든 인스턴스는 컬렉션을 만들 수 있지만, 해당 컬렉션을 통해 사용 권한을 관리하지는 않습니다. 8월 18일 전에 만든 Purview 인스턴스의 액세스 제어에 대한 자세한 내용은 페이지 맨 아래에 있는 [**레거시 권한 가이드**](#legacy-permission-guide)를 참조하세요.
>
> 모든 레거시 계정은 향후 몇 주 동안 자동으로 업그레이드됩니다. Purview 계정이 업그레이드되면 메일 알림을 받게 됩니다. 계정이 업그레이드되면 변경되는 사항에 대한 자세한 내용은 [업그레이드된 계정 가이드](concept-account-upgrade.md)를 참조하세요.

## <a name="collections"></a>컬렉션

컬렉션이란 Azure Purview가 자산, 원본 및 기타 아티팩트를 더 쉽게 검색되도록 계층 구조로 그룹화하고 액세스 제어를 관리하는 데 사용하는 도구입니다. Purview의 리소스에 대한 모든 액세스는 Purview 계정 자체의 컬렉션에서 관리됩니다.

## <a name="roles"></a>역할

Azure Purview는 미리 정의된 역할 세트를 사용하여 누가 계정 내의 어떤 항목에 액세스할 수 있는지를 제어합니다. 이러한 역할은 현재 다음과 같습니다.

- **컬렉션 관리자** - Purview 컬렉션, 해당 세부 정보 및 하위 컬렉션을 편집할 수 있습니다. 본인이 관리자로 있는 컬렉션의 다른 Purview 역할에 사용자를 추가할 수도 있습니다.
- **데이터 원본 관리자** - 데이터 원본 및 데이터 검색을 관리할 수 있습니다.
- **데이터 큐레이터** - 카탈로그 데이터 자산을 만들고 읽고 수정하고 삭제할 수 있으며 자산 간의 관계를 설정할 수 있습니다.
- **데이터 읽기 권한자** - 카탈로그 데이터 자산에 액세스할 수 있지만 수정할 수는 없습니다.

## <a name="who-should-be-assigned-to-what-role"></a>누가 어떤 역할에 할당되어야 하나요?

|사용자 시나리오|적절한 역할|
|-------------|-----------------|
|그저 자산을 찾을 수 있어야 하고, 아무것도 편집하지 않겠습니다.|데이터 읽기 권한자|
|자산에 대한 정보를 편집하고, 분류를 할당하고, 용어집 항목과 연결하는 등의 작업을 수행해야 합니다.|데이터 큐레이터|
|용어집을 편집하거나 새 분류 정의를 설정해야 합니다.|데이터 큐레이터|
|애플리케이션의 서비스 주체는 Azure Purview에 데이터를 푸시해야 합니다.|데이터 큐레이터|
|Purview Studio를 통해 검사를 설정해야 합니다.|데이터 원본 관리자 + 적어도 원본이 등록된 컬렉션의 데이터 읽기 권한자 **또는** 데이터 큐레이터|
|서비스 사용자 또는 그룹이 카탈로그의 정보에 액세스하도록 허용하지 않으면서 Azure Purview에서 검사를 설정하고 모니터링할 수 있도록 설정해야 합니다. |데이터 원본 관리자|
|Azure Purview에서 사용자를 역할에 배치해야 합니다. | 컬렉션 관리자 |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles.png" alt-text="Purview 역할을 보여 주는 차트" lightbox="./media/catalog-permissions/collection-permissions-roles.png":::

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>Azure Purview의 역할 및 컬렉션을 사용하는 방법 이해

모든 액세스 제어는 Purview의 컬렉션에서 관리됩니다. Purview의 컬렉션은 [Purview Studio](use-purview-studio.md)에서 찾을 수 있습니다. [Azure Portal](https://portal.azure.com)에서 Purview 계정을 열고 [개요] 페이지에서 Purview Studio 타일을 선택합니다. 여기서 왼쪽 메뉴의 데이터 맵으로 이동한 다음, '컬렉션' 탭을 선택합니다.

만들어진 Azure Purview 계정은 Purview 계정 자체와 동일한 이름을 가진 루트 컬렉션으로 시작합니다. Purview 계정의 작성자는 자동으로 이 루트 컬렉션의 컬렉션 관리자, 데이터 원본 관리자, 데이터 큐레이터 및 데이터 읽기 권한자로 추가되며, 이 컬렉션을 편집하고 관리할 수 있습니다.

원본, 자산 및 개체를 이 루트 컬렉션에 직접 추가할 수 있지만 다른 컬렉션에 추가할 수도 있습니다. 컬렉션을 추가하면 Purview 계정에서 데이터에 액세스할 수 있는 사용자를 보다 세밀하게 제어할 수 있습니다.

그 외의 사용자는 위에서 언급한 역할 중 하나가 할당되면 Azure Purview 계정 또는 자신이 속한 그룹의 정보에만 액세스할 수 있습니다. 즉, Azure Purview 계정을 만들 때 계정 작성자를 제외한 어느 누구도 [컬렉션에서 위에서 언급한 역할 중 하나 이상이 할당](how-to-create-and-manage-collections.md#add-role-assignments)되기 전에는 API에 액세스하거나 사용할 수 있습니다.

컬렉션 관리자 또는 권한 상속을 통해서만 사용자를 컬렉션에 추가할 수 있습니다. 부모 컬렉션의 권한은 해당 하위 컬렉션에 자동으로 상속됩니다. 그러나 컬렉션에 대한 [권한 상속을 제한](how-to-create-and-manage-collections.md#restrict-inheritance)할 수 있습니다. 권한 상속을 제한하면 해당 하위 컬렉션이 더 이상 부모 컬렉션에서 권한을 상속하지 않으며 직접 추가해야 합니다. 다만 부모 컬렉션에서 자동으로 상속되는 컬렉션 관리자를 제거할 수 없습니다.

## <a name="assign-permissions-to-your-users"></a>사용자에게 권한 할당

Azure Purview 계정을 만든 후 가장 먼저 할 일은 컬렉션을 만들고 해당 컬렉션 내에서 사용자에게 역할을 할당하는 것입니다.

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

역할 할당은 컬렉션을 통해 관리됩니다. [컬렉션 관리자 역할](#roles)이 할당된 사용자만이 해당 컬렉션의 다른 사용자에게 권한을 부여할 수 있습니다. 새 권한을 추가해야 하는 경우 컬렉션 관리자는 Purview Studio에 액세스하고, 데이터 맵으로 이동하고, [컬렉션] 탭으로 이동하고, 사용자를 추가해야 하는 컬렉션을 선택합니다. [역할 할당] 탭에서는 권한이 필요한 사용자를 추가하고 관리할 수 있습니다.

전체 지침은 [역할 할당을 추가하는 방법 가이드](how-to-create-and-manage-collections.md#add-role-assignments)를 참조하세요.

## <a name="legacy-permission-guide"></a>레거시 권한 가이드

> [!NOTE]
> 이 레거시 컬렉션 가이드는 2021년 8월 18일 전에 만든 Purview 계정에만 적용됩니다. 이 날짜 이후에 만든 인스턴스는 위의 가이드를 따라야 합니다.

이 문서에서는 8월 18일 전에 만든 Purview 리소스와 관련하여 Azure Purview의 [데이터 평면](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)에서 RBAC(역할 기반 액세스 제어)를 구현하는 방법을 설명합니다.

> [!IMPORTANT]
> Purview 계정을 만든 사용자에게는 그렇거나 혹은 그렇지 않을 수 있는 데이터 평면 역할에 관계없이 모든 데이터 평면 권한이 자동으로 부여됩니다. 다른 모든 사용자가 Azure Purview에서 모든 작업을 수행하려면 미리 정의된 데이터 평면 역할 중 하나 이상에 있어야 합니다.

### <a name="azure-purviews-pre-defined-legacy-data-plane-roles"></a>Azure Purview의 미리 정의된 레거시 데이터 평면 역할

Azure Purview는 Azure Purview에서 액세스를 제어하는 데 사용할 수 있는 미리 정의된 데이터 평면 역할 세트를 제공합니다. 이러한 역할은 다음과 같습니다.

* **Purview 데이터 판독기 역할** - Purview 포털에 대한 액세스 권한을 가지며, 검사 바인딩을 제외하고 Azure Purview의 모든 콘텐츠를 읽을 수 있습니다.
* **Purview 데이터 큐레이터 역할** - Purview 포털에 액세스할 수 있으며 검사 바인딩을 제외한 Azure Purview의 모든 콘텐츠를 읽을 수 있고, 자산에 대한 정보를 편집할 수 있으며, 분류 정의 및 용어를 편집할 수 있고, 자산에 분류 및 용어를 적용할 수 있습니다.
* **Purview 데이터 원본 관리자 역할** - Purview Portal에 대한 액세스 권한이 없으며(사용자는 데이터 판독기 또는 데이터 큐레이터 역할에 있어야 함), Azure Purview에 대한 데이터 검사의 모든 측면을 관리할 수 있지만, 검사에 관련된 것 외에 Azure Purview의 콘텐츠에 대한 읽기 또는 쓰기 액세스 권한은 없습니다.

### <a name="understand-how-to-use-azure-purviews-legacy-data-plane-roles"></a>Azure Purview의 레거시 데이터 평면 역할을 사용하는 방법 이해

Azure Purview 계정이 만들어지면, 작성자는 Azure Purview 데이터 큐레이터 및 데이터 원본 관리자 역할 모두에 있는 것처럼 간주됩니다. 그러나 계정 작성자는 역할 저장소의 이러한 역할에 할당되지 않습니다. Azure Purview는 보안 주체가 계정의 작성자임을 인식하고 ID를 기반으로 이러한 기능을 확장합니다.

다른 모든 사용자는 이러한 역할 중 하나 이상에 배치된 경우에만 Azure Purview 계정을 사용할 수 있습니다. 즉, Azure Purview 계정을 만들 때, 하나 이상의 미리 정의된 역할에 배치될 때까지 작성자만 계정에 액세스하거나 해당 API를 사용할 수 있습니다.

Purview 데이터 원본 관리자 역할에는 지원되는 두 가지 시나리오가 있습니다. 첫 번째 시나리오는 이미 Purview 데이터 판독기 또는 Purview 데이터 큐레이터인 사용자를 위한 것으로, 검사를 만들 수도 있어야 합니다. 이러한 사용자는 최소한 Purview 데이터 읽기 권한자 또는 Purview 데이터 큐레이터 중 하나인 두 역할에 속해야 하며, Purview 데이터 원본 관리자 역할에도 배치되어야 합니다.

Purview 데이터 원본 관리자의 다른 시나리오는 검색을 설정하고 모니터링할 수 있어야 하지만 카탈로그의 데이터에 액세스할 수 없어야 하는 프로그래매틱 프로세스(예: 서비스 주체)에 대한 것입니다.

이 시나리오는 다른 두 역할에 배치하지 않고 Purview 데이터 원본 관리자 역할에 서비스 주체를 배치하여 구현할 수 있습니다. 보안 주체는 Purview 포털에 액세스할 수 없지만 괜찮습니다. 프로그래밍 방식 보안 주체이며 API를 통해서만 통신하기 때문입니다.

### <a name="putting-users-into-legacy-roles"></a>레거시 역할에 사용자 배치

따라서 Azure Purview 계정을 만든 후 첫 번째 비즈니스 순서는 이러한 역할에 사람을 할당하는 것입니다.

역할 할당은 [Azure의 RBAC](../role-based-access-control/overview.md)를 통해 관리됩니다.

Azure에서 두 개의 기본 제공 컨트롤 플레인 역할만 사용자 역할을 할당할 수 있고, 이러한 역할은 소유자 또는 사용자 액세스 관리자입니다. 따라서 Azure Purview에 대한 이러한 역할에 사용자를 배치하려면 소유자 또는 사용자 액세스 관리자인 사람을 찾거나 스스로가 되어야 합니다.

#### <a name="an-example-of-assigning-someone-to-a-legacy-role"></a>레거시 역할에 사용자 할당 예제

1. https://portal.azure.com 로 이동하여 Azure Purview 계정으로 이동합니다.
1. 왼쪽에서 "액세스 제어 (IAM)"을 클릭합니다.
1. 그런 다음 [여기](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group) 에 제공된 일반적인 지침을 따릅니다.

### <a name="legacy-role-definitions-and-actions"></a>레거시 역할 정의 및 작업

역할은 동작의 컬렉션으로 정의됩니다. 역할을 정의하는 방법에 대한 자세한 내용은 [여기](../role-based-access-control/role-definitions.md)를 참조하세요. Azure Purview 역할에 대한 역할 정의는 [여기](../role-based-access-control/built-in-roles.md)를 참조하세요.

### <a name="getting-added-to-a-legacy-data-plane-role-in-an-azure-purview-account"></a>Azure Purview 계정의 레거시 데이터 평면 역할에 추가

Azure Purview 계정에 대한 액세스 권한을 부여하여 해당 스튜디오를 사용하거나 API를 호출할 수 있도록 하려면 Azure Purview 데이터 평면 역할에 추가되어야 합니다. 이 작업을 수행할 수 있는 유일한 사용자는 Azure Purview 계정에 대한 소유자 또는 사용자 액세스 관리자입니다. 대부분의 사용자는 다음 단계를 통해 액세스 권한을 부여할 수 있는 적절한 사용자를 찾을 수 있는 로컬 관리자를 찾을 수 있습니다.

회사의 [Azure Portal](https://portal.azure.com)에 대한 액세스 권한이 있는 사용자는 가입하려는 특정 Azure Purview 계정을 조회할 수 있으며, 해당 "액세스 제어 (IAM)" 탭을 클릭하여 소유자 또는 사용자 액세스 관리자(UAAs)가 누구인지 확인할 수 있습니다. 그러나 일부 경우에는 Azure Active Directory 그룹 또는 서비스 주체를 소유자 또는 UAAs로 사용할 수 있으며, 이 경우에는 직접 연락하지 못할 수 있습니다. 대신, 도움을 주는 관리자를 찾아야 합니다.

### <a name="legacy---who-should-be-assigned-to-what-role"></a>레거시 - 누구에게 어떤 역할을 할당해야 하나요?

|사용자 시나리오|적절한 역할|
|-------------|-----------------|
|그저 자산을 찾을 수 있어야 하고, 아무것도 편집하지 않겠습니다.|Purview 데이터 읽기 권한자 역할|
|자산에 대한 정보를 편집하고, 분류를 입력하고, 용어를 용어집 항목과 연결 등을 해야 합니다.|Purview 데이터 큐레이터 역할|
|용어집을 편집하거나 새 분류 정의를 설정해야 합니다.|Purview 데이터 큐레이터 역할|
|애플리케이션의 서비스 주체는 Azure Purview에 데이터를 푸시해야 합니다.|Purview 데이터 큐레이터 역할|
|Purview Studio를 통해 검사를 설정해야 합니다.|Purview 데이터 원본 관리자 역할에 Purview 데이터 읽기 권한자 역할 또는 Purview 데이터 관리자 역할 중 하나 이상 추가|
|프로그래매틱 ID가 카탈로그의 정보에 액세스하도록 허용하지 않고 Azure Purview에서 검사를 설정하고 모니터링하려면 서비스 주체 또는 기타 프로그래매틱 ID를 사용하도록 설정해야 합니다. |Purview 데이터 원본 관리자 역할|
|Azure Purview에서 사용자를 역할에 배치해야 합니다. | 소유자 또는 사용자 액세스 관리자 |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles-legacy.png" alt-text="Purview 레거시 역할을 보여 주는 차트" lightbox="./media/catalog-permissions/collection-permissions-roles-legacy.png":::

## <a name="next-steps"></a>다음 단계

컬렉션 및 액세스 제어의 기본 사항을 이해했으므로, 아래 가이드에 따라 해당 컬렉션을 만들고 관리하거나 원본을 Purview 리소스에 등록합니다.

- [컬렉션을 만들고 관리하는 방법](how-to-create-and-manage-collections.md)
- [Purview에서 지원하는 데이터 원본](purview-connector-overview.md)
