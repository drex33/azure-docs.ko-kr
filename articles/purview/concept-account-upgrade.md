---
title: Azure Purview 계정 업그레이드 정보
description: 2021년 8월 18일 이전에 만든 Azure Purview 계정은 자동으로 최신 버전의 Purview로 업그레이드됩니다. 이 문서에서는 변경될 내용과 필요한 다음 단계를 설명합니다.
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: template-concept
ms.openlocfilehash: e4d46bf93eed7d04bbddf14003a2426dfe6c9e3c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649381"
---
# <a name="azure-purview-account-upgrade-information"></a>Azure Purview 계정 업그레이드 정보

Azure Purview는 탄력적 데이터 맵, 업데이트된 컬렉션 및 업데이트된 액세스 제어를 포함하여 2021년 8월 18일에 몇 가지 새로운 기능을 릴리스했습니다. 2021년 8월 18일 **이후에** 만든 Azure Purview 계정은 이미 사용 가능한 모든 새로운 기능을 통해 만들어집니다.

2021년 8월 18일 **이전에** 만든 계정(레거시 계정)은 이러한 기능을 포함하도록 자동으로 업그레이드됩니다.

2021년 8월 18일 **이전에** 만든 Purview 계정이 있는 경우 Purview 계정이 업그레이드될 때 이메일 알림을 받게 됩니다.

이 문서에서는 계정이 업그레이드될 때 표시되는 변경 내용과 새 기능을 사용하기 위해 수행해야 하는 모든 단계에 대해 설명합니다.

## <a name="elastic-data-map"></a>탄력적 데이터 맵

업그레이드된 Azure Purview 계정은 사용량에 따라 동적으로 확장되는 새 데이터 맵 시스템인 탄력적 데이터 맵을 사용합니다. 탄력적 데이터 맵은 하나의 용량 단위에서 시작하여 필요에 따라 용량 단위를 추가하고 줄입니다.
이 새로운 기능은 Purview와 직접 상호 작용하는 방식에는 영향을 주지 않지만 청구에 영향을 미칩니다. 데이터 환경을 관리하는 데 필요한 크기로 크기가 조정되며 사용하는 것에 대해서만 요금이 청구됩니다.

새 탄력적 데이터 맵 및 청구 방법에 대한 자세한 내용은 [탄력적 데이터 맵 페이지](concept-elastic-data-map.md)를 참조하세요.

모든 Purview에 대한 청구 세부 정보는 가격 계산기 를 [참조하세요.](https://azure.microsoft.com/pricing/details/azure-purview/)

계정을 업그레이드할 때 탄력적 데이터 맵을 사용하기 위해 변경할 필요가 없습니다. 자동으로 사용하도록 설정됩니다.

## <a name="collections"></a>컬렉션

컬렉션은 레거시 Purview 계정에 있지만 새로운 기능이 있으며 업그레이드된 Purview 계정에서 다른 방식으로 관리됩니다.

[레거시 컬렉션은](how-to-create-and-manage-collections.md#legacy-collection-guide) Purview 계정에서 데이터 원본 및 아티팩트 구성 방법이었습니다. 컬렉션은 비즈니스 환경과 일치하도록 Purview 데이터 맵을 사용자 지정하는 데 계속 사용되지만 이제 액세스 제어도 포함됩니다. 데이터 맵 외부의 상위 수준에서 액세스를 제어하는 대신, 컬렉션을 통해 액세스 관리 환경이 데이터 맵과 일치합니다.

[컬렉션을](how-to-create-and-manage-collections.md) 사용하면 데이터 원본뿐만 아니라 검색 기능도 세밀하게 제어할 수 있습니다. 사용자는 액세스 권한이 있는 컬렉션의 자산만 볼 수 있으므로 필요한 정보만 볼 수 있습니다.

Purview 계정이 업그레이드되면 컬렉션도 업데이트됩니다. 모든 현재 자산이 이러한 새 컬렉션으로 마이그레이션됩니다. 아래 섹션에서는 컬렉션 및 기존 자산을 찾을 수 있는 위치에 대해 설명합니다.

### <a name="locate-and-manage-collections"></a>컬렉션 찾기 및 관리

새 컬렉션을 찾으려면 [Purview Studio](use-purview-studio.md)에서 시작하겠습니다. [Azure Portal](https://portal.azure.com) Purview 리소스로 가서 개요 페이지에서 **Purview Studio 열기** 타일을 선택하여 스튜디오를 찾을 수 있습니다.

왼쪽 창에서 데이터 맵 > 컬렉션을 선택하여 컬렉션 관리 페이지를 엽니다.

:::image type="content" source="./media/concept-account-upgrade/find-collections.png" alt-text="데이터 맵이 열리고 [컬렉션] 탭이 선택된 Purview Studio 창의 스크린샷" border="true":::

루트 컬렉션과 모든 기존 컬렉션이 표시됩니다. 루트 컬렉션은 컬렉션 목록의 최상위 컬렉션이며 Purview 리소스와 동일한 이름을 갖습니다. 아래 예제에서는 루트 컬렉션의 이름이 Contoso Purview입니다.

:::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="데이터 맵이 열리고 루트 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

모든 기존 컬렉션이 이 루트 컬렉션에 추가되었으며 이 페이지에서 관리될 수 있습니다.
새 컬렉션을 만들려면 + 컬렉션 **추가를** 선택합니다.

:::image type="content" source="./media/concept-account-upgrade/select-add-a-collection.png" alt-text="컬렉션 추가 단추가 강조 표시된 새 컬렉션 창을 보여 주는 Purview 스튜디오 창의 스크린샷" border="true":::

컬렉션을 편집하려면 컬렉션 세부 정보 페이지 또는 컬렉션의 드롭다운 메뉴에서 **편집을** 선택합니다.

:::image type="content" source="./media/concept-account-upgrade/edit-collection.png" alt-text="선택한 컬렉션 창과 컬렉션 이름 옆에 있는 줄임표 단추 아래에 ‘편집’ 단추가 강조 표시된 컬렉션 창에 열린 Purview 스튜디오 창의 스크린샷" border="true":::

컬렉션에서 역할 할당을 편집하려면 **역할 할당 탭을** 선택하여 컬렉션의 모든 역할을 확인합니다. 컬렉션 관리자만 역할 할당을 관리할 수 있습니다. [아래 섹션에는](#permissions)업그레이드된 계정의 사용 권한에 대한 자세한 내용이 있습니다.

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="[역할 할당] 탭이 강조 표시된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

업그레이드된 계정의 컬렉션에 대한 자세한 내용은 [컬렉션 만들기 및 관리에 대한 가이드를 참조하세요.](how-to-create-and-manage-collections.md)

### <a name="what-happens-to-your-collections-during-upgrade"></a>업그레이드하는 동안 컬렉션은 어떻게 되나요?

1. 루트 컬렉션이 만들어집니다. 루트 컬렉션은 컬렉션 목록의 최상위 컬렉션이며 Purview 리소스와 동일한 이름을 갖습니다. 아래 예제에서는 루트 컬렉션의 이름이 Contoso Purview입니다.

    :::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="데이터 맵이 열리고 루트 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 기존 컬렉션이 루트 컬렉션에 연결됩니다. 루트 컬렉션 아래에 나열된 항목이 표시되며, 이 컬렉션에서 상호 작용할 수 있습니다.

### <a name="what-happens-to-your-sources-during-upgrade"></a>업그레이드하는 동안 원본은 어떻게 되나요?

1. 이전에 컬렉션과 연결되지 않은 소스는 루트 컬렉션에 자동으로 추가됩니다.

1. 이전에 컬렉션과 연결된 모든 원본 또는 검색은 업그레이드된 계정의 해당 컬렉션에 추가됩니다.

자산은 업그레이드된 계정의 컬렉션과도 연결되지만 업그레이드된 컬렉션 아래에 즉시 표시되지 않을 수 있습니다. 이는 다음 두 가지 이유 중 하나입니다.

* 예약된 검색을 통해 자산을 검색하고 다음 검사 실행이 아직 발생하지 않았습니다.
* 자산은 일회성 검색을 통해서만 검색되었습니다.

> [!NOTE]
> 자산은 검색 프로세스 중에 컬렉션에 추가되므로 자산에서 다른 검사를 실행하여 이러한 새 컬렉션에 추가해야 합니다.

예약된 검사의 경우 이러한 검색의 다음 실행을 기다리기만 하면 자산이 컬렉션에 추가됩니다.

일회성 검색의 경우 컬렉션의 자산을 채우려면 수동으로 다시 실행해야 합니다.

1. Purview Studio에서 데이터 맵 열고 **원본을** 선택합니다. 검색할 원본을 선택합니다.

    :::image type="content" source="./media/concept-account-upgrade/select-sources.png" alt-text="원본이 강조 표시된 데이터 맵 열린 Purview Studio 창의 스크린샷." border="true":::

1. 검색 **탭을** 선택한 다음, 다시 실행하려는 검사를 선택합니다.

    :::image type="content" source="./media/concept-account-upgrade/select-scan.png" alt-text="검색이 강조 표시된 원본으로 열린 Purview Studio 창의 스크린샷." border="true":::

1. **지금 검색 실행을** 선택하여 검사를 다시 실행합니다.

    :::image type="content" source="./media/concept-account-upgrade/run-scan-now.png" alt-text="이제 검색 실행이 강조 표시된 검색으로 열린 Purview Studio 창의 스크린샷." border="true":::

### <a name="what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin"></a>업그레이드된 계정에 컬렉션 관리자가 없으면 어떻게 되나요?

프로세스에서 다음 순서로 하나 이상의 사용자 또는 그룹을 식별할 수 있는 경우 업그레이드된 Purview 계정에는 기본 컬렉션 관리자가 있습니다. 

1. 소유자(명시적으로 할당됨)

1. 사용자 액세스 관리자(명시적으로 할당됨)

1. 데이터 원본 관리자 및 데이터 보존

계정에 위의 조건과 일치하는 사용자 또는 그룹이 없는 경우 업그레이드된 Purview 계정에는 컬렉션 관리자가 없습니다. 

관리 API를 사용하여 컬렉션 관리자를 수동으로 추가할 수 있습니다. 이 API를 호출하는 사용자는 쓰기 작업을 실행하려면 Purview 계정에 대한 소유자 또는 사용자 액세스 관리자 권한이 있어야 합니다. `objectId`API를 통해 제출하려면 새 컬렉션 관리자의 를 알아야 합니다.

#### <a name="request"></a>요청

   ```
    POST https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Purview/accounts/<accountName>/addRootCollectionAdmin?api-version=2021-07-01
   ```    
    
#### <a name="request-body"></a>요청 본문

   ```json
    {
        "objectId": "<objectId>"
    }
   ```    

`objectId` 는 추가할 새 컬렉션 관리자의 objectId입니다.

#### <a name="response-body"></a>응답 본문

성공하면 코드가 있는 빈 본문 응답을 받게 `200` 됩니다.

실패하는 경우 응답 본문의 형식은 다음과 같습니다.

   ```json
    {
        "error": {
            "code": "19000",
            "message": "The caller does not have Microsoft.Authorization/roleAssignments/write permission on resource: [/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>].",
            "target": null,
            "details": []
        }
    }
   ```

## <a name="permissions"></a>사용 권한

업그레이드된 Purview 계정에서 사용 권한은 컬렉션을 통해 관리됩니다.

> [!NOTE]
> 업그레이드된 계정의 권한은 더 이상 IAM(Access Control)을 통해 관리되지 않습니다. 레거시 계정이 업그레이드되면 기존 IAM 권한은 Access Control IAM 아래에 계속 표시되지만 Purview의 액세스에는 더 이상 영향을 미치지 않습니다.

레거시 계정이 업그레이드되면 Access Control IAM에 할당된 모든 권한이 루트 컬렉션에 다음 역할 중 하나에 할당됩니다.

* **컬렉션 관리자** - 컬렉션 및 컬렉션 세부 정보를 편집하고, 컬렉션의 액세스를 관리하고, 하위 컬렉션을 추가할 수 있습니다.
* **데이터 원본 관리자** - 데이터 원본 및 데이터 검색을 관리할 수 있습니다.
* **데이터 큐레이터** - 카탈로그 데이터 개체에 대한 만들기, 읽기, 수정 및 삭제 작업을 수행할 수 있습니다.
* **데이터 읽기 권한자** - 카탈로그 데이터 개체에 액세스할 수 있지만 수정할 수는 없습니다.

Purview의 원본 및 아티팩트 액세스 권한을 사용자에게 부여하려면 데이터에 액세스해야 할 수 있는 컬렉션 및 하위 컬렉션에 대한 액세스 권한을 부여해야 합니다.

> [!NOTE]
> 컬렉션 관리자만 컬렉션에서 액세스를 관리할 수 있는 권한이 있습니다.

컬렉션에서 역할 할당을 보거나 편집하려면 컬렉션 내에서 **역할 할당 탭을** 선택합니다.

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="[역할 할당] 탭이 강조 표시된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

업그레이드된 Purview 계정에서 사용 권한을 관리하는 자세한 내용은 [Purview 권한 가이드를 참조하세요.](catalog-permissions.md)

## <a name="next-steps"></a>다음 단계

위의 개념에 대한 자세한 내용이나 Purview 리소스 보안에 대한 자세한 내용은 아래 링크를 참조하세요.

* [Azure Purview를 통해 프라이빗 엔드포인트](catalog-private-link.md)
* [탄력적 데이터 맵](concept-elastic-data-map.md)
* [Purview 권한 가이드](catalog-permissions.md)
