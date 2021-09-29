---
title: Azure 부서의 범위 계정 업그레이드 정보
description: 2021 년 8 월 18 일 이전에 만든 Azure 부서의 범위 계정은 자동으로 부서의 범위의 최신 버전으로 업그레이드 됩니다. 이 문서에서는 변경 되는 내용과 필요한 다음 단계에 대해 설명 합니다.
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: template-concept
ms.openlocfilehash: b528e4a4b96e0dbe5f8b2eca748420a23f3c2950
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208904"
---
# <a name="azure-purview-account-upgrade-information"></a>Azure 부서의 범위 계정 업그레이드 정보

Azure 부서의 범위는 탄력적 데이터 맵, 업데이트 된 컬렉션 및 업데이트 된 액세스 제어를 포함 하 여 2021 년 8 월 18 일에 몇 가지 새로운 기능을 출시 했습니다. 2021 년 8 월 18 일 **이후에** 만든 Azure 부서의 범위 계정은 이러한 모든 새 기능을 이미 사용할 수 있습니다.

2021 년 8 월 18 일 **이전** 에 만든 계정은 이러한 기능을 포함 하도록 자동으로 업그레이드 됩니다.

2021 년 8 월 18 일 **이전** 에 부서의 범위 계정을 만든 경우 부서의 범위 계정이 업그레이드 되 면 전자 메일 알림을 받게 됩니다.

이 문서에서는 계정이 업그레이드 될 때 표시 되는 변경 내용 및 새로운 기능을 사용 하기 위해 수행 해야 하는 단계에 대해 설명 합니다.

## <a name="elastic-data-map"></a>탄력적 데이터 맵

업그레이드 된 Azure 부서의 범위 계정은 사용에 따라 동적으로 크기가 조정 되는 새 데이터 맵 시스템을 사용 합니다. 탄력적 데이터 맵 탄력적 데이터 맵은 하나의 용량 단위에서 시작 하 여 필요에 따라 용량 단위를 추가 하 고 감소 시킵니다.
이 새로운 기능은 부서의 범위와 직접 상호 작용 하는 방법에는 영향을 주지 않지만 대금 청구에 영향을 줍니다. 데이터의 가로를 관리 하는 데 필요한 크기로 크기를 조정 하 고 사용 하는 만큼만 청구 됩니다.

새 탄력적 데이터 맵과 요금 청구 방법에 대 한 자세한 내용은 [탄력적 데이터 맵 페이지](concept-elastic-data-map.md)를 참조 하세요.

모든 부서의 범위에 대 한 청구 정보는 [가격 계산기](https://azure.microsoft.com/pricing/details/azure-purview/)를 참조 하세요.

계정이 업그레이드 되 면 탄력적 데이터 맵을 사용 하기 위해 변경할 필요가 없습니다. 자동으로 사용 하도록 설정 됩니다.

## <a name="collections"></a>컬렉션

컬렉션은 레거시 부서의 범위 계정에 있지만 업그레이드 된 부서의 범위 계정에서 다른 방식으로 관리 됩니다.

[레거시 컬렉션](how-to-create-and-manage-collections.md#legacy-collection-guide) 은 부서의 범위 계정에서 데이터 원본 및 아티팩트를 구성 하는 방법 이었습니다. 컬렉션은 부서의 범위 데이터 맵을 비즈니스 환경에 맞게 사용자 지정 하는 데 여전히 사용 되지만 이제는 액세스 제어도 포함 합니다. 데이터 맵 외부에서 상위 수준으로 액세스를 제어 하는 대신 컬렉션을 통해 액세스 관리 환경이 데이터 맵과 일치 합니다.

[컬렉션](how-to-create-and-manage-collections.md) 을 통해 데이터 원본에 대 한 세밀 한 제어를 제공 하 고 검색 가능성을 확인할 수도 있습니다. 사용자는 액세스 권한이 있는 컬렉션의 자산만 볼 수 있으므로 필요한 정보만 표시 됩니다.

부서의 범위 계정이 업그레이드 되 면 컬렉션도 업데이트 됩니다. 모든 현재 자산이 이러한 새 컬렉션으로 마이그레이션됩니다. 다음 섹션에서는 컬렉션과 기존 자산을 찾을 수 있는 위치에 대해 설명 합니다.

### <a name="locate-and-manage-collections"></a>컬렉션 찾기 및 관리

새 컬렉션을 찾기 위해 [부서의 범위 Studio](https://web.purview.azure.com/resource/)에서 시작 합니다. [Azure Portal](https://portal.azure.com) 에서 부서의 범위 리소스로 이동 하 고 개요 페이지에서 **부서의 범위 studio 열기** 타일을 선택 하 여 studio를 찾을 수 있습니다.

왼쪽 창에서 데이터 맵 > 컬렉션을 선택 하 여 컬렉션 관리 페이지를 엽니다.

:::image type="content" source="./media/concept-account-upgrade/find-collections.png" alt-text="데이터 맵이 열리고 [컬렉션] 탭이 선택된 Purview Studio 창의 스크린샷" border="true":::

여기에는 모든 기존 컬렉션 뿐만 아니라 루트 컬렉션도 표시 됩니다. 루트 컬렉션은 컬렉션 목록의 맨 위 컬렉션이 며 부서의 범위 리소스와 동일한 이름을 갖습니다. 아래 예제에서는 루트 컬렉션의 이름이 Contoso Purview입니다.

:::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="데이터 맵이 열리고 루트 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

모든 기존 컬렉션은이 루트 컬렉션에 추가 되었으며이 페이지에서 관리할 수 있습니다.
새 컬렉션을 만들려면 **+ 컬렉션 추가** 를 선택 합니다.

:::image type="content" source="./media/concept-account-upgrade/select-add-a-collection.png" alt-text="컬렉션 추가 단추가 강조 표시된 새 컬렉션 창을 보여 주는 Purview 스튜디오 창의 스크린샷" border="true":::

컬렉션을 편집 하려면 컬렉션 세부 정보 페이지에서 편집을 선택 하거나 컬렉션의 드롭다운 메뉴에서 **편집** 을 선택 합니다.

:::image type="content" source="./media/concept-account-upgrade/edit-collection.png" alt-text="선택한 컬렉션 창과 컬렉션 이름 옆에 있는 줄임표 단추 아래에 ‘편집’ 단추가 강조 표시된 컬렉션 창에 열린 Purview 스튜디오 창의 스크린샷" border="true":::

컬렉션에서 역할 할당을 편집 하려면 **역할 할당** 탭을 선택 하 여 컬렉션의 모든 역할을 표시 합니다. 컬렉션 관리자만 역할 할당을 관리할 수 있습니다. 업그레이드 된 계정의 사용 권한에 대 한 자세한 내용은 [아래 섹션](#permissions)에 있습니다.

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="[역할 할당] 탭이 강조 표시된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

업그레이드 된 계정의 컬렉션에 대 한 자세한 내용은 [컬렉션 만들기 및 관리 가이드](how-to-create-and-manage-collections.md)를 참조 하세요.

### <a name="what-happens-to-your-collections-during-upgrade"></a>업그레이드 하는 동안 컬렉션은 어떻게 되나요?

1. 루트 컬렉션이 생성 됩니다. 루트 컬렉션은 컬렉션 목록의 맨 위 컬렉션이 며 부서의 범위 리소스와 동일한 이름을 갖습니다. 아래 예제에서는 루트 컬렉션의 이름이 Contoso Purview입니다.

    :::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="데이터 맵이 열리고 루트 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 이전에 기존 컬렉션은 루트 컬렉션에 연결 됩니다. 루트 컬렉션 아래에 나열 되는 것을 볼 수 있으며,이를 통해 상호 작용할 수 있습니다.

### <a name="what-happens-to-your-sources-during-upgrade"></a>업그레이드 하는 동안 소스는 어떻게 되나요?

1. 이전에 컬렉션과 연결 되지 않은 모든 소스는 루트 컬렉션에 자동으로 추가 됩니다.

1. 이전에 컬렉션과 연결 된 모든 원본 또는 검색은 업그레이드 된 계정의 해당 컬렉션에 추가 됩니다.

자산은 업그레이드 된 계정의 컬렉션과 연결 되지만 업그레이드 된 컬렉션에 즉시 표시 되지 않을 수도 있습니다. 이는 다음 두 가지 이유 중 하나에 해당 합니다.

* 예약 된 검사를 통해 자산이 검색 되며 다음 검색 실행은 아직 발생 하지 않았습니다.
* 자산은 일회성 검색에 의해서만 검색 되었습니다.

> [!NOTE]
> 검색 프로세스 중에 자산을 컬렉션에 추가 하므로 이러한 새 컬렉션에 추가 하기 위해 자산에서 다른 검색을 실행 해야 합니다.

예약 된 검색의 경우 다음에 검색을 실행할 때까지 기다려야 하며, 자산이 컬렉션에 추가 됩니다.

일회성 검색의 경우 컬렉션의 자산을 채우려면 수동으로 다시 실행 해야 합니다.

1. [부서의 범위 Studio](https://web.purview.azure.com/resource/)에서 데이터 맵을 열고 **소스** 를 선택 합니다. 검색 하려는 원본을 선택 합니다.

    :::image type="content" source="./media/concept-account-upgrade/select-sources.png" alt-text="소스가 강조 표시 된 상태로 데이터 맵에 열린 부서의 범위 studio 창의 스크린샷" border="true":::

1. **검색** 탭을 선택 하 고 다시 실행 하려는 검색을 선택 합니다.

    :::image type="content" source="./media/concept-account-upgrade/select-scan.png" alt-text="검사가 강조 표시 된 상태로 원본에 열린 부서의 범위 studio 창의 스크린샷" border="true":::

1. 검색을 **지금 실행** 을 선택 하 여 검사를 다시 실행 합니다.

    :::image type="content" source="./media/concept-account-upgrade/run-scan-now.png" alt-text="검색 실행 시 검색 된 부서의 범위 studio 창의 스크린샷 이제 강조 표시 됩니다." border="true":::

### <a name="what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin"></a>업그레이드 된 계정에 컬렉션 관리자가 없는 경우 어떻게 되나요?

프로세스에서 다음 순서에 따라 하나 이상의 사용자 또는 그룹을 식별할 수 있는 경우 업그레이드 된 부서의 범위 계정에 기본 컬렉션 관리자가 포함 됩니다. 

1. 소유자 (명시적으로 할당 됨)

1. 사용자 액세스 관리자 (명시적으로 할당 됨)

1. 데이터 원본 관리자 및 데이터 큐레이터

계정에 위의 조건과 일치 하는 사용자 또는 그룹이 없는 경우 업그레이드 된 부서의 범위 계정에는 컬렉션 관리자가 없습니다. 

관리 API를 사용 하 여 컬렉션 관리자를 수동으로 추가할 수 있습니다. 이 API를 호출 하는 사용자에 게는 쓰기 작업을 실행 하기 위해 부서의 범위 계정에 대 한 소유자 또는 사용자 액세스 관리자 권한이 있어야 합니다. `objectId`API를 통해 제출할 새 컬렉션 관리자의를 알고 있어야 합니다.

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

`objectId` 추가할 새 컬렉션 관리자의 objectId입니다.

#### <a name="response-body"></a>응답 본문

성공 하면 코드가 포함 된 빈 본문 응답을 받게 됩니다 `200` .

오류가 발생 하는 경우 응답 본문의 형식은 다음과 같습니다.

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

업그레이드 된 부서의 범위 계정에서 사용 권한은 컬렉션을 통해 관리 됩니다.

> [!NOTE]
> 업그레이드 된 계정의 사용 권한은 더 이상 IAM (Access Control)을 통해 관리 되지 않습니다. 레거시 계정이 업그레이드 될 때 기존 IAM 권한은 Access Control IAM에 계속 표시 되지만 더 이상 부서의 범위의 액세스에는 영향을 주지 않습니다.

레거시 계정이 업그레이드 되 면 Access Control IAM에서 할당 된 모든 사용 권한이 다음 역할 중 하나에 대 한 루트 컬렉션에 할당 됩니다.

* **컬렉션 관리자** - 컬렉션 및 컬렉션 세부 정보를 편집하고, 컬렉션의 액세스를 관리하고, 하위 컬렉션을 추가할 수 있습니다.
* **데이터 원본 관리자** - 데이터 원본 및 데이터 검색을 관리할 수 있습니다.
* **데이터 큐레이터** - 카탈로그 데이터 개체에 대한 만들기, 읽기, 수정 및 삭제 작업을 수행할 수 있습니다.
* **데이터 읽기 권한자** - 카탈로그 데이터 개체에 액세스할 수 있지만 수정할 수는 없습니다.

사용자에 게 부서의 범위의 원본 및 아티팩트에 대 한 액세스 권한을 부여 하려면 데이터에 액세스 해야 하는 컬렉션 및 하위 컬렉션에 대 한 액세스 권한을 사용자에 게 부여 하는 것이 좋습니다.

> [!NOTE]
> 컬렉션 관리자만 컬렉션의 액세스를 관리할 수 있는 권한이 있습니다.

컬렉션에서 역할 할당을 보거나 편집 하려면 컬렉션 내에서 **역할 할당** 탭을 선택 합니다.

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="[역할 할당] 탭이 강조 표시된 Purview Studio 컬렉션 창의 스크린샷" border="true":::

업그레이드 된 부서의 범위 계정의 사용 권한을 관리 하는 방법에 대 한 자세한 내용은 [부서의 범위 사용 권한 가이드](catalog-permissions.md)를 따르세요.

## <a name="next-steps"></a>다음 단계

위의 개념에 대한 자세한 내용이나 Purview 리소스 보안에 대한 자세한 내용은 아래 링크를 따르세요.

* [Azure Purview를 통해 프라이빗 엔드포인트](catalog-private-link.md)
* [탄력적 데이터 맵](concept-elastic-data-map.md)
* [Purview 권한 가이드](catalog-permissions.md)
