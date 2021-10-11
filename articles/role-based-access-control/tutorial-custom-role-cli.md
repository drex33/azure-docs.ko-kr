---
title: '자습서: Azure CLI를 사용하여 Azure 사용자 지정 역할 만들기 - Azure RBAC'
description: 이 자습서에서는 Azure CLI 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 사용자 지정 역할 만들기를 시작합니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6fed28798e8e2f7795600b50c0121361ce54584b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358237"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure 사용자 지정 역할 만들기

Azure 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [사용자 지정 역할](built-in-roles.md)을 만들면 됩니다. 이 자습서에서는 Azure CLI를 사용하여 Reader 지원 티켓이라는 사용자 지정 역할을 만듭니다. 사용자 지정 역할을 통해 사용자는 구독의 컨트롤 플레인에서 모든 것을 살펴보고 지원 티켓을 열 수도 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 역할 만들기
> * 사용자 지정 역할 나열
> * 사용자 지정 역할 업데이트
> * 사용자 지정 역할 삭제

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))
- [Azure Cloud Shell](../cloud-shell/overview.md) 또는 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Azure CLI에 로그인

[Azure CLI](/cli/azure/authenticate-azure-cli)에 로그인합니다.

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만드는 가장 쉬운 방법은 JSON 템플릿으로 시작하여 변경 내용을 추가한 다음, 새 역할을 만드는 것입니다.

1. [Microsoft.Support 리소스 공급자](resource-provider-operations.md#microsoftsupport)에 대한 작업 목록을 검토합니다. 권한을 만드는 데 사용할 수 있는 작업을 파악하는 데 도움이 됩니다.

    | 작업 | Description |
    | --- | --- |
    | Microsoft.Support/register/action | 지원 리소스 공급자에 등록합니다. |
    | Microsoft.Support/supportTickets/read | 상태, 심각도, 연락처 세부 정보, 통신 등 지원 티켓 세부 정보를 얻거나 구독의 지원 티켓 목록을 얻습니다. |
    | Microsoft.Support/supportTickets/write | 지원 티켓을 만들거나 업데이트합니다. 기술, 청구, 할당량 또는 구독 관리 관련 문제에 대한 지원 티켓을 만들 수 있습니다. 기존 지원 티켓의 심각도, 연락처 세부 정보 및 통신을 업데이트할 수 있습니다. |
    
1. **ReaderSupportRole.json** 이라는 새 파일을 만듭니다.

1. 편집기에서 ReaderSupportRole.json 파일을 열고 다음 JSON을 추가합니다.

    여러 속성에 대한 내용은 [Azure 사용자 지정 역할](custom-roles.md)을 참조하세요.

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. `Actions` 속성에 다음 작업을 추가합니다. 이러한 작업을 통해 사용자는 구독의 모든 것을 살펴보고 지원 티켓을 만들 수 있습니다.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. [az account list](/cli/azure/account#az_account_list) 명령을 사용하여 구독 ID를 가져옵니다.

    ```azurecli
    az account list --output table
    ```

1. `AssignableScopes`에서 `{subscriptionId1}`을 자신의 구독 ID로 바꿉니다.

    명시적 구독 ID를 추가해야 합니다. 그렇지 않으면 역할을 구독으로 가져올 수 없습니다.

1. `Name` 및 `Description` 속성을 "Reader 지원 티켓" 및 "구독의 모든 것을 살펴보고 지원 티켓 열기"로 변경합니다.

    JSON 파일은 다음과 비슷합니다.

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. 새로운 사용자 지정 역할을 만들려면 [az role definition create](/cli/azure/role/definition#az_role_definition_create) 명령을 사용하여 JSON 역할 정의 파일을 지정합니다.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    이제 새로운 사용자 지정 역할을 사용할 수 있으며 기본 제공 역할과 마찬가지로 사용자, 그룹 또는 서비스 사용자에게 할당할 수 있습니다.

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

- 모든 사용자 지정 역할을 나열하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list) 명령과 `--custom-role-only` 매개 변수를 사용합니다.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    Azure Portal에서도 사용자 지정 역할을 볼 수 있습니다.

    ![Azure Portal에서 가져온 사용자 지정 역할의 스크린샷](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 업데이트하려면 JSON 파일을 업데이트한 다음, 사용자 지정 역할을 업데이트합니다.

1. ReaderSupportRole.json 파일을 엽니다.

1. `Actions`에서 리소스 그룹 배포 `"Microsoft.Resources/deployments/*"`를 만들고 관리하는 작업을 추가합니다. 이전 작업 뒤에 쉼표를 추가해야 합니다.

    업데이트된 JSON 파일은 다음과 비슷합니다.

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. 사용자 지정 역할을 업데이트하려면 [az role definition update](/cli/azure/role/definition#az_role_definition_update) 명령을 사용하여 업데이트된 JSON 파일을 지정합니다.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

- [az role definition delete](/cli/azure/role/definition#az_role_definition_delete) 명령을 사용하여 사용자 지정 역할을 삭제할 역할 이름 또는 역할 ID를 지정합니다.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 사용자 지정 역할 만들기 또는 업데이트](custom-roles-cli.md)
