---
title: Azure Portal을 사용하여 사용자 지정 역할 만들기 또는 업데이트
description: Azure Portal 및 Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure 사용자 지정 역할을 만드는 방법을 알아봅니다. 여기에는 사용자 지정 역할을 나열, 생성, 업데이트 및 삭제하는 방법이 포함됩니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 08/27/2021
ms.author: rolyon
ms.openlocfilehash: dddcbef1b43866a7cba631cd565856aef787ec8b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360940"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트

[Azure 기본 제공 역할](built-in-roles.md)이 조직의 특정 요구 사항을 충족하지 않는 경우 Azure 사용자 지정 역할을 만들면 됩니다. 기본 제공 역할과 마찬가지로 구독 및 리소스 그룹 범위에서 사용자 지정 역할을 사용자, 그룹 및 서비스 주체에 할당할 수 있습니다. 사용자 지정 역할은 Azure AD(Azure Active Directory) 디렉터리에 저장되며 구독에서 공유할 수 있습니다. 각 디렉터리는 최대 5,000개의 사용자 지정 역할을 가질 수 있습니다. 사용자 지정 역할은 Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 할당할 수 있습니다. 이 문서에서는 Azure Portal을 사용하여 사용자 지정 역할을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

사용자 지정 역할을 만들려면 다음이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))

## <a name="step-1-determine-the-permissions-you-need"></a>1단계: 필요한 권한 결정

Azure에는 잠재적으로 사용자 지정 역할에 포함할 수 있는 수천 개의 권한이 있습니다. 사용자 지정 역할에 추가할 권한을 결정하는 데 도움이 되는 몇 가지 방법은 다음과 같습니다.

- 기존 [기본 제공 역할](built-in-roles.md)을 확인합니다.
- 액세스 권한을 부여할 Azure 서비스를 나열합니다.
- [Azure 서비스에 매핑되는 리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md)를 결정합니다. 검색 방법은 [4단계: 권한](#step-4-permissions) 뒷부분에 설명되어 있습니다.
- [사용 가능한 권한](resource-provider-operations.md)을 검색하여 포함하려는 권한을 찾습니다. 검색 방법은 [4단계: 권한](#step-4-permissions) 뒷부분에 설명되어 있습니다.

## <a name="step-2-choose-how-to-start"></a>2단계: 시작 방법 선택

세 가지 방법으로 사용자 지정 역할을 만들 수 있습니다. 기존 역할을 복제하거나, 처음부터 새로 시작하거나, JSON 파일을 사용하여 시작할 수 있습니다. 가장 쉬운 방법은 필요한 권한 대부분을 포함하는 기존 역할을 찾은 다음, 복제하고 시나리오에 맞게 수정하는 것입니다. 

### <a name="clone-a-role"></a>역할 복제

기존 역할에 필요한 권한이 없는 경우 해당 역할을 복제한 다음, 권한을 수정할 수 있습니다. 역할 복제를 시작하려면 다음 단계를 수행합니다.

1. Azure Portal에서 사용자 지정 역할을 할당할 수 있도록 하려는 구독 또는 리소스 그룹을 연 다음, **액세스 제어(IAM)** 를 엽니다.

    다음 스크린샷은 구독에 대해 열려 있는 액세스 제어(IAM) 페이지를 보여 줍니다.

    ![구독의 액세스 제어(IAM) 페이지](./media/shared/sub-access-control.png)

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

1. 청구 읽기 권한자 역할과 같이 복제할 역할을 검색합니다.

1. 행의 끝에서 줄임표( **...** )를 클릭하고 **복제** 를 클릭합니다.

    ![복제 상황에 맞는 메뉴](./media/custom-roles-portal/clone-menu.png)

    그러면 **역할 복제** 옵션이 선택된 상태로 사용자 지정 역할 편집기가 열립니다.

1. [3단계: 기본 사항](#step-3-basics)을 계속 진행합니다.

### <a name="start-from-scratch"></a>처음부터 시작

원할 경우 다음 단계를 수행하여 사용자 지정 역할을 처음부터 새로 시작할 수 있습니다.

1. Azure Portal에서 사용자 지정 역할을 할당할 수 있도록 하려는 구독 또는 리소스 그룹을 연 다음, **액세스 제어(IAM)** 를 엽니다.

1. **추가** 를 클릭한 후 **사용자 지정 역할 추가** 를 클릭합니다.

    ![사용자 지정 역할 추가 메뉴](./media/custom-roles-portal/add-custom-role-menu.png)

    그러면 **처음부터 새로 시작** 옵션이 선택된 상태로 사용자 지정 역할 편집기가 열립니다.

1. [3단계: 기본 사항](#step-3-basics)을 계속 진행합니다.

### <a name="start-from-json"></a>JSON에서 시작

원할 경우 JSON 파일에서 대부분의 사용자 지정 역할 값을 지정할 수 있습니다. 사용자 지정 역할 편집기에서 파일을 열고 추가로 변경한 다음, 사용자 지정 역할을 만들 수 있습니다. JSON 파일로 시작하려면 다음 단계를 수행합니다.

1. 다음 형식의 JSON 파일을 만듭니다.

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. JSON 파일에서 다양한 속성에 대한 값을 지정합니다. 일부 값이 추가된 예제는 다음과 같습니다. 여러 속성에 대한 내용은 [Azure 역할 정의 이해](role-definitions.md)를 참조하세요.

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Azure Portal에서 **액세스 제어(IAM)** 페이지를 엽니다.

1. **추가** 를 클릭한 후 **사용자 지정 역할 추가** 를 클릭합니다.

    ![사용자 지정 역할 추가 메뉴](./media/custom-roles-portal/add-custom-role-menu.png)

    그러면 사용자 지정 역할 편집기가 열립니다.

1. 기본 정보 탭의 **기준 권한** 에서 **JSON에서 시작** 을 선택합니다.

1. 파일 선택 상자 옆에 있는 폴더 단추를 클릭하여 열기 대화 상자를 엽니다.

1. JSON 파일을 선택한 다음, **열기** 를 클릭합니다.

1. [3단계: 기본 사항](#step-3-basics)을 계속 진행합니다.

## <a name="step-3-basics"></a>3단계: 기본 사항

**기본 사항** 탭에서 사용자 지정 역할에 대한 이름, 설명 및 기준 권한을 지정합니다.

1. **사용자 지정 역할 이름** 상자에 사용자 지정 역할의 이름을 지정합니다. 이름은 Azure AD 디렉터리에 대해 고유해야 합니다. 이름에는 문자, 숫자, 공백 및 특수 문자를 포함할 수 있습니다.

1. **설명** 상자에 사용자 지정 역할에 대한 선택적 설명을 지정합니다. 이 설명은 사용자 지정 역할에 대한 도구 설명으로 사용됩니다.

    이전 단계를 기준으로 **기준 권한** 옵션이 이미 설정되어 있지만 변경할 수 있습니다.

    ![값이 지정된 기본 사항 탭](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>4단계: 권한

**권한** 탭에서 사용자 지정 역할에 대한 권한을 지정합니다. 역할을 복제했는지 또는 JSON으로 시작했는지에 따라 권한 탭에는 이미 일부 권한이 나열될 수 있습니다.

![사용자 지정 역할 만들기의 권한 탭](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>권한 추가 또는 제거

사용자 지정 역할에 대한 권한을 추가하거나 제거하려면 다음 단계를 따르세요.

1. 권한을 추가하려면 **권한 추가** 를 클릭하여 권한 추가 창을 엽니다.

    이 창에는 사용 가능한 모든 권한이 범주별로 그룹화되어 카드 형식으로 표시됩니다. 각 범주는 Azure 리소스를 제공하는 서비스에 해당하는 *리소스 공급자* 를 나타냅니다.

1. **권한 검색** 상자에 권한을 검색할 문자열을 입력합니다. 예를 들어, 청구서와 관련된 권한을 찾으려면 *청구서* 를 검색합니다.

    리소스 공급자 카드의 목록이 검색 문자열에 따라 표시됩니다. Azure 서비스와 일치하는 리소스 공급자의 목록은 [Azure 서비스용 리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md)를 참조하세요.

    ![리소스 공급자를 사용하여 권한 창 추가](./media/shared/add-permissions-provider.png)

1. **Microsoft 청구** 와 같이 사용자 지정 역할에 추가하려는 권한이 있을 수 있는 리소스 공급자 카드를 클릭합니다.

    해당 리소스 공급자에 대한 관리 권한 목록이 검색 문자열에 따라 표시됩니다.

    ![권한 목록 추가](./media/shared/add-permissions-list.png)

1. 데이터 평면에 적용되는 권한을 찾고 있는 경우 **데이터 작업** 을 클릭합니다. 그렇지 않으면 작업 설정/해제를 **작업** 으로 설정 하 여 컨트롤 평면에 적용 되는 사용 권한을 나열 합니다. 제어 평면과 데이터 평면 간의 차이점에 대 한 자세한 내용은 [제어 및 데이터 작업](role-definitions.md#control-and-data-actions)을 참조 하세요.

1. 필요한 경우 검색 문자열을 업데이트하여 검색을 구체화합니다.

1. 사용자 지정 역할에 추가하려는 권한을 하나 이상 찾았으면 권한 옆에 확인 표시를 추가합니다. 예를 들어, **기타: 청구서 다운로드** 옆에 확인 표시를 추가하여 청구서 다운로드 권한을 추가합니다.

1. **추가** 를 클릭하여 권한 목록에 권한을 추가합니다.

    권한은 `Actions` 또는 `DataActions`로 추가됩니다.

    ![권한이 추가됨](./media/custom-roles-portal/permissions-list-add.png)

1. 권한을 제거하려면 행의 끝에 있는 삭제 아이콘을 클릭합니다. 이 예에서는 사용자가 지원 티켓을 만들 수 있는 기능이 필요하지 않으므로 `Microsoft.Support/*` 권한을 삭제할 수 있습니다.

### <a name="add-wildcard-permissions"></a>와일드카드 권한 추가

선택한 시작 방법에 따라 권한 목록에 와일드카드(`*`)가 있는 권한이 포함될 수 있습니다. 와일드카드(`*`)는 사용자가 제공하는 작업 문자열과 일치하는 모든 항목으로 권한을 확장합니다. 예를 들어, 다음 와일드카드 문자열은 Azure Cost Management 및 내보내기와 관련된 모든 권한을 추가합니다. 여기에는 추가될 수 있는 후속 내보내기 권한도 포함됩니다.

```
Microsoft.CostManagement/exports/*
```

새 와일드카드 권한은 **권한 추가** 창을 사용하여 추가할 수 없습니다. 와일드카드 권한을 추가하려면 **JSON** 탭을 사용하여 수동으로 추가해야 합니다. 자세한 내용은 [6단계: JSON](#step-6-json)을 참조하세요.

### <a name="exclude-permissions"></a>권한 제외

역할에 와일드카드(`*`) 권한이 있고 해당 와일드카드 권한에서 특정 권한을 제외하거나 빼려는 경우 해당 권한을 제외할 수 있습니다. 예를 들어, 다음과 같은 와일드카드 권한이 있다고 가정해 보겠습니다.

```
Microsoft.CostManagement/exports/*
```

내보내기 삭제를 허용하지 않으려면 다음과 같은 삭제 권한을 제외할 수 있습니다.

```
Microsoft.CostManagement/exports/delete
```

권한을 제외하면 `NotActions` 또는 `NotDataActions`로 추가됩니다. 실제 관리 권한은 모든 `Actions`를 추가한 후 모든 `NotActions`를 빼서 계산합니다. 실제 데이터 권한은 모든 `DataActions`를 추가한 후 모든 `NotDataActions`를 빼서 계산합니다.

> [!NOTE]
> 권한 제외는 거부와 동일하지 않습니다. 권한 제외는 단순히 와일드카드 권한에서 권한을 빼는 편리한 방법입니다.

1. 허용되는 와일드카드 권한에서 권한을 제외하거나 빼려면 **권한 제외** 를 클릭하여 권한 제외 창을 엽니다.

    이 창에서 제외하거나 뺀 관리 또는 데이터 권한을 지정합니다.

1. 제외하려는 하나 이상의 권한을 찾았으면 권한 옆에 확인 표시를 추가하고 **추가** 단추를 클릭합니다.

    ![권한 제외 창 - 권한이 선택됨](./media/custom-roles-portal/exclude-permissions-select.png)

    권한은 `NotActions` 또는 `NotDataActions`로 추가됩니다.

    ![권한이 제외됨](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>5단계: 할당 가능한 범위

**할당 가능한 범위** 탭에서 관리 그룹, 구독 또는 리소스 그룹과 같이 할당에 사용자 지정 역할을 사용할 수 있는 위치를 지정합니다. 선택한 시작 방법에 따라 이 탭에는 액세스 제어(IAM) 페이지를 연 범위가 이미 나열될 수 있습니다.

 할당 가능한 범위에는 하나의 관리 그룹만 정의할 수 있습니다. 할당 가능한 범위에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다. 할당 가능한 범위를 루트 범위("/")로 설정하는 것은 지원되지 않습니다.

1. **할당 가능한 범위 추가** 를 클릭하여 할당 가능한 범위 추가 창을 엽니다.

    ![할당 가능한 범위 탭](./media/custom-roles-portal/assignable-scopes.png)

1. 사용하려는 하나 이상의 범위(일반적으로 구독)를 클릭합니다.

    ![할당 가능한 범위 추가](./media/custom-roles-portal/add-assignable-scopes.png)

1. **추가** 단추를 클릭하여 할당 가능한 범위를 추가합니다.

## <a name="step-6-json"></a>6단계: JSON

**JSON** 탭에서는 JSON으로 형식이 지정된 사용자 지정 역할이 표시됩니다. 원할 경우 JSON을 직접 편집할 수 있습니다. 와일드카드(`*`) 권한을 추가하려면 이 탭을 사용해야 합니다.

1. JSON 파일을 편집하려면 **편집** 을 클릭합니다.

    ![사용자 지정 역할을 보여 주는 JSON 탭](./media/custom-roles-portal/json.png)

1. JSON을 변경합니다.

    JSON의 형식이 잘못된 경우 수직 여백에 빨간색 뾰족한 선과 표시기가 표시됩니다.

1. 편집을 마친 후 **저장** 을 클릭합니다.

## <a name="step-7-review--create"></a>7단계 : 검토 + 만들기

**검토 + 만들기** 탭에서 사용자 지정 역할 설정을 검토할 수 있습니다.

1. 사용자 지정 역할 설정을 검토합니다.

    ![리뷰 + 만들기 탭](./media/custom-roles-portal/review-create.png)

1. **만들기** 를 클릭하여 사용자 지정 역할을 만듭니다.

    잠시 후 사용자 지정 역할이 성공적으로 만들어졌는지 나타내는 메시지 상자가 나타납니다.

    ![사용자 지정 역할 만들기](./media/custom-roles-portal/custom-role-success.png)

    오류가 감지되면 메시지가 표시됩니다.

    ![검토 + 만들기 오류](./media/custom-roles-portal/review-create-error.png)

1. **역할** 목록에서 새 사용자 지정 역할을 확인합니다. 사용자 지정 역할이 표시되지 않으면 **새로 고침** 을 클릭합니다.

     사용자 지정 역할이 표시될 때까지 몇 분 정도 걸릴 수 있습니다.

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

사용자 지정 역할을 보려면 다음 단계를 수행합니다.

1. 구독 또는 리소스 그룹을 연 다음, **액세스 제어(IAM)** 를 엽니다.

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

1. **유형** 목록에서 **CustomRole** 을 선택하여 사용자 지정 역할만 표시합니다.

    방금 사용자 지정 역할을 만든 경우 목록에 표시되지 않으면 **새로 고침** 을 클릭합니다.

    ![사용자 지정 역할 목록](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

1. 이 문서의 앞부분에서 설명한 대로 사용자 지정 역할의 목록을 엽니다.

1. 업데이트하려는 사용자 지정 역할에 대해 줄임표( **...** )를 클릭한 다음, **편집** 을 클릭합니다. 기본 제공 역할은 업데이트할 수 없습니다.

    사용자 지정 역할이 편집기에서 열립니다.

    ![사용자 지정 역할 메뉴](./media/custom-roles-portal/edit-menu.png)

1. 다른 탭을 사용하여 사용자 지정 역할을 업데이트합니다.

1. 변경을 완료한 후에는 **검토 + 만들기** 탭을 클릭하여 변경 내용을 검토합니다.

1. **업데이트** 단추를 클릭하여 사용자 지정 역할을 업데이트합니다.

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

1. 이 문서의 앞부분에서 설명한 대로 사용자 지정 역할의 목록을 엽니다.

1. 사용자 지정 역할을 사용하는 역할 할당을 제거합니다.

1. 삭제하려는 사용자 지정 역할에 대해 줄임표( **...** )를 클릭한 다음, **삭제** 를 클릭합니다.

    ![삭제하도록 선택할 수 있는 사용자 지정 역할 목록을 보여 주는 스크린샷](./media/custom-roles-portal/delete-menu.png)

    사용자 지정 역할을 완전히 삭제하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure PowerShell을 사용하여 Azure 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [Azure 사용자 지정 역할](custom-roles.md)
- [Azure 리소스 공급자 작업](resource-provider-operations.md)
