---
title: 특정 API 커넥터에 대한 연결 차단
description: Azure Logic Apps에서 API 연결 만들기 및 사용 제한
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: f13ffe10ed9ade4e6fe8767ef61b6709747b7d2a
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606251"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Azure Logic Apps에서 커넥터로 만든 연결 차단

조직에서 Azure Logic Apps의 커넥터를 사용하여 제한되거나 승인되지 않은 리소스에 연결을 허용하지 않는 경우 논리 앱 워크플로에서 해당 연결을 만들고 사용하는 기능을 차단할 수 있습니다. [Azure Policy](../governance/policy/overview.md)를 사용하여 차단하려는 커넥터에 대한 연결을 만들거나 사용하지 못하도록 방지하는 [정책](../governance/policy/overview.md#policy-definition)을 정의하고 적용할 수 있습니다. 예를 들어, 보안상의 이유로 특정 소셜 미디어 플랫폼 또는 다른 서비스와 시스템에 대한 연결을 차단할 수 있습니다.

이 항목에서는 Azure Portal을 사용하여 특정 연결을 차단하는 정책을 설정하는 방법을 보여 주지만 예를 들어, Azure REST API, Azure PowerShell, Azure CLI, Azure Resource Manager 템플릿을 통해 다른 방법으로 정책 정의를 만들 수 있습니다. 자세한 내용은 [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* 차단하려는 커넥터의 참조 ID. 자세한 내용은 [커넥터 참조 ID 찾기](#connector-reference-ID)를 참조하세요.

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>커넥터 참조 ID 찾기

차단할 연결을 포함하는 논리 앱이 이미 있는 경우 [Azure Portal에 해당하는 단계](#connector-ID-portal)를 수행합니다. 액세스 권한이 없는 경우 다음 단계를 수행하세요.

1. [Logic Apps 커넥터 목록](/connectors/connector-reference/connector-reference-logicapps-connectors)을 방문합니다.

1. 차단할 커넥터에 대한 참조 페이지를 찾습니다.

   예를 들어, 사용되지 않는 Instagram 커넥터를 차단하려는 경우 다음 페이지로 이동합니다.

   `https://docs.microsoft.com/connectors/instagram/`

1. 페이지의 URL에서 슬래시(`/`)를 사용하지 않고 끝에 커넥터 참조 ID를 복사하여 저장합니다(예: `instagram`).

   나중에 정책 정의를 만들 때 이 ID를 정의의 조건 문에 사용합니다. 예를 들면 다음과 같습니다.

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에서 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴에서 **논리 앱 코드 보기** 를 선택합니다. 그러면 논리 앱의 JSON 정의를 볼 수 있습니다.

   !["논리 앱 코드 보기"를 열어 커넥터 ID 찾기](./media/block-connections-connectors/code-view-connector-id.png)

1. `$connections` 개체를 포함한 `parameters` 개체를 찾습니다. 포함된 개체에는 논리 앱의 각 연결에 대한 `{connection-name}` 개체를 포함하고 해당 연결에 대한 정보가 들어 있습니다.

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   예를 들어, Instagram 커넥터의 경우 Instagram 연결을 식별하는 `instagram` 개체를 찾습니다.

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. 차단하려는 연결에 대해 다음 형식을 따르는 `id` 속성과 값을 찾습니다. 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   예를 들어, 여기서는 Instagram 연결의 `id` 속성과 값입니다.

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. `id` 속성 값에서 커넥터 참조 ID를 복사하여 끝에 저장합니다(예: `instagram`).

   나중에 정책 정의를 만들 때 이 ID를 정의의 조건 문에 사용합니다. 예를 들면 다음과 같습니다.

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>연결 만들기를 차단하는 정책 만들기

논리 앱에서 연결 만들기를 모두 차단하려면 다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 검색 상자에 `policy`를 입력하고 **정책** 을 선택합니다.

   ![Azure Portal에서 “정책”을 찾아 선택합니다.](./media/block-connections-connectors/find-select-azure-policy.png)

1. **정책** 메뉴의 **작성** 에서 **정의** >  **+ 정책 정의** 를 선택합니다.

   !["정의" > "+ 정책 정의"를 선택합니다.](./media/block-connections-connectors/add-new-policy-definition.png)

1. **정책 정의** 에서는 예제에서 설명하는 속성에 따라 정책 정의에 대한 정보를 제공합니다.

   ![“정책 정의” 속성을 보여 주는 스크린샷](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **정의 위치** | 예 | <*Azure-subscription-name*> | 정책 정의에 사용할 Azure 구독 <p><p>1. 구독을 찾으려면 줄임표( **...** ) 단추를 선택합니다. <br>2. **구독** 목록에서 구독을 찾아 선택합니다. <br>3. 완료되면 **선택** 을 선택합니다. |
   | **이름** | 예 | <*policy-definition-name*> | 정책 정의에 사용할 이름 |
   | **설명** | 예 | <*policy-definition-name*> | 정책 정의에 대한 설명 |
   | **범주** | 예 | **논리 앱** | 정책 정의의 기존 범주 또는 새 범주의 이름 |
   | **정책 적용** | 예 | **Enabled** | 이 설정은 작업을 저장할 때 정책 정의를 사용하도록 또는 사용하지 않도록 설정할지 여부를 지정합니다. |
   ||||

1. **정책 규칙** 에서 JSON 편집 상자는 정책 정의 템플릿으로 미리 채워져 있습니다. 다음 구문에 따라 아래 표에서 설명하는 속성 기반 [정책 정의](../governance/policy/concepts/definition-structure.md)로 이 템플릿을 바꿉니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | `mode` | `All` | 정책이 평가하는 리소스 종류를 결정하는 모드입니다. <p><p>이 시나리오에서는 `mode`를 `All`로 설정합니다. 그러면 정책이 Azure 리소스 그룹, 구독 및 모든 리소스 종류에 적용됩니다. <p><p>자세한 내용은 [정책 정의 구조 - 모드](../governance/policy/concepts/definition-structure.md#mode)를 참조하세요. |
   | `if` | `{condition-to-evaluate}` | 정책 규칙을 적용할 시기를 결정하는 조건입니다. <p><p>이 시나리오에서는 `{condition-to-evaluate}`가 `Microsoft.Web/connections/api.id`의 `api.id` 값이 와일드카드 (*) 값을 지정하는 `*managedApis/{connector-name}`의 값과 일치하는지 여부를 확인합니다. <p><p>자세한 내용은 [정책 정의 구조 - 정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조하세요. |
   | `field` | `Microsoft.Web/connections/api.id` | 조건과 비교할 `field` 값입니다. <p><p>이 시나리오에서는 커넥터 속성 `api.id`의 값에 액세스하기 위해 `field`가 [*별칭*](../governance/policy/concepts/definition-structure.md#aliases), `Microsoft.Web/connections/api.id`를 사용합니다. |
   | `like` | `*managedApis/{connector-name}` | `field` 값을 비교하는 데 사용할 논리 연산자 및 값입니다. <p><p>이 시나리오에서 `like` 연산자와 와일드카드(*) 문자는 둘 다 규칙이 지역에 관계없이 작동하는지 확인하고, 문자열 `*managedApis/{connector-name}`은 일치하는 값인데, 여기서 `{connector-name}`은 차단하려는 커넥터의 ID와 일치하는 값입니다. <p><p>예를 들어 소셜 미디어 플랫폼 또는 데이터베이스에 대한 연결 만들기를 차단하려는 경우를 가정해 보겠습니다. <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server 또는 Azure SQL: `sql` <p><p>이러한 커넥터 ID를 찾으려면 이 항목의 앞부분에 나오는 [커넥터 참조 ID 찾기](#connector-reference-ID)를 참조하세요. |
   | `then` | `{effect-to-apply}` | `if` 조건이 충족될 때 적용할 효과. <p><p>이 시나리오에서는 `{effect-to-apply}` 정책을 준수하지 않는 요청이나 작업을 차단하고 실행하지 않습니다. <p><p>자세한 내용은 [정책 정의 구조 - 정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조하세요. |
   | `effect` | `deny` | `effect`는 지정된 연결을 만드는 요청을 차단합니다. <p><p>자세한 내용은 [Azure Policy 효과 이해 - 거부](../governance/policy/concepts/effects.md#deny)를 참조하세요. |
   ||||

   예를 들어, Instagram 커넥터를 사용하여 연결 만들기를 차단하려고 한다고 가정해 보겠습니다. 사용할 수 있는 정책 정의는 다음과 같습니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   다음은 **정책 규칙** 상자가 표시되는 방법입니다.

   ![정책 규칙 예제가 나와 있는 “정책 규칙” 상자를 보여 주는 스크린샷](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   여러 커넥터의 경우 예를 들어, 다음과 같은 조건을 더 추가할 수 있습니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. 완료되면 **저장** 을 선택합니다. 정책 정의를 저장하면 Azure Policy는 더 많은 속성 값을 생성하여 정책 정의에 추가합니다.

1. 그런 다음 정책을 적용하려는 정책 정의를 할당하려면 [정책 할당을 만듭니다](#create-policy-assignment).

Azure Policy 정의에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Policy 정의 구조](../governance/policy/concepts/definition-structure.md)
* [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
* [Azure Logic Apps에 대한 Azure Policy 기본 제공 정책 정의](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>연결을 사용하여 차단하는 정책 만들기

논리 앱 내에서 연결을 만들면 해당 연결은 별도의 Azure 리소스로 존재합니다. 논리 앱만 삭제하면 연결이 자동으로 삭제되지 않고 연결 자체가 삭제될 때까지 계속 존재합니다. 연결이 이미 존재하거나 논리 앱 외부에서 사용할 연결을 만들어야 하는 시나리오가 있을 수 있습니다. 제한된 연결이나 승인되지 않은 연결을 포함하는 논리 앱을 저장하지 못하도록 방지하는 정책을 만들어 논리 앱에서 기존 연결을 사용하는 기능을 차단할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 검색 상자에 `policy`를 입력하고 **정책** 을 선택합니다.

   ![Azure Portal에서 “정책”을 찾아 선택합니다.](./media/block-connections-connectors/find-select-azure-policy.png)

1. **정책** 메뉴의 **작성** 에서 **정의** >  **+ 정책 정의** 를 선택합니다.

   !["정의" > "+ 정책 정의"를 선택합니다.](./media/block-connections-connectors/add-new-policy-definition.png)

1. **정책 정의** 에서 예제에 설명된 속성을 기반으로 정책 정의에 대한 정보를 제공하고, 예를 들어 다음 예제처럼 Instagram을 사용하여 계속 진행합니다.

   ![정책 정의 속성](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **정의 위치** | 예 | <*Azure-subscription-name*> | 정책 정의에 사용할 Azure 구독 <p><p>1. 구독을 찾으려면 줄임표( **...** ) 단추를 선택합니다. <br>2. **구독** 목록에서 구독을 찾아 선택합니다. <br>3. 완료되면 **선택** 을 선택합니다. |
   | **이름** | 예 | <*policy-definition-name*> | 정책 정의에 사용할 이름 |
   | **설명** | 예 | <*policy-definition-name*> | 정책 정의에 대한 설명 |
   | **범주** | 예 | **논리 앱** | 정책 정의의 기존 범주 또는 새 범주의 이름 |
   | **정책 적용** | 예 | **Enabled** | 이 설정은 작업을 저장할 때 정책 정의를 사용하도록 또는 사용하지 않도록 설정할지 여부를 지정합니다. |
   ||||

1. **정책 규칙** 에서 JSON 편집 상자는 정책 정의 템플릿으로 미리 채워져 있습니다. 다음 구문에 따라 아래 표에서 설명하는 속성 기반 [정책 정의](../governance/policy/concepts/definition-structure.md)로 이 템플릿을 바꿉니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | `mode` | `All` | 정책이 평가하는 리소스 종류를 결정하는 모드입니다. <p><p>이 시나리오에서는 `mode`를 `All`로 설정합니다. 그러면 정책이 Azure 리소스 그룹, 구독 및 모든 리소스 종류에 적용됩니다. <p><p>자세한 내용은 [정책 정의 구조 - 모드](../governance/policy/concepts/definition-structure.md#mode)를 참조하세요. |
   | `if` | `{condition-to-evaluate}` | 정책 규칙을 적용할 시기를 결정하는 조건입니다. <p><p>이 시나리오에서 `{condition-to-evaluate}`는 `[string(field('Microsoft.Logic/workflows/parameters'))]`의 문자열 출력에 문자열 `{connector-name}`이 포함되어 있는지 여부를 확인합니다. <p><p>자세한 내용은 [정책 정의 구조 - 정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조하세요. |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | 조건과 비교할 값입니다. <p><p>이 시나리오에서 `value`는 `Microsoft.Logic/workflows/parameters` 개체 내의 `$connectors` 개체를 문자열로 변환하는 `[string(field('Microsoft.Logic/workflows/parameters'))]`의 문자열 출력입니다. |
   | `contains` | `{connector-name}` | `value` 속성과 비교하는 데 사용할 논리 연산자 및 값입니다. <p><p>이 시나리오에서 `contains` 연산자는 `{connector-name}`이 표시되는 위치와 관계없이 규칙이 작동하도록 합니다. 여기서 문자열 `{connector-name}`은 제한하거나 차단하려는 커넥터의 ID입니다. <p><p>예를 들어 소셜 미디어 플랫폼 또는 데이터베이스에 대한 연결 사용을 차단하려는 경우를 가정해 보겠습니다. <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server 또는 Azure SQL: `sql` <p><p>이러한 커넥터 ID를 찾으려면 이 항목의 앞부분에 나오는 [커넥터 참조 ID 찾기](#connector-reference-ID)를 참조하세요. |
   | `then` | `{effect-to-apply}` | `if` 조건이 충족될 때 적용할 효과. <p><p>이 시나리오에서는 `{effect-to-apply}` 정책을 준수하지 않는 요청이나 작업을 차단하고 실행하지 않습니다. <p><p>자세한 내용은 [정책 정의 구조 - 정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조하세요. |
   | `effect` | `deny` | `effect`는 지정된 연결을 사용하는 논리 앱을 저장하는 요청을 `deny` 또는 차단합니다. <p><p>자세한 내용은 [Azure Policy 효과 이해 - 거부](../governance/policy/concepts/effects.md#deny)를 참조하세요. |
   ||||

   예를 들어, Instagram 연결을 사용하는 논리 앱의 저장을 차단하려는 경우를 가정해 보겠습니다. 사용할 수 있는 정책 정의는 다음과 같습니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   다음은 **정책 규칙** 상자가 표시되는 방법입니다.

   ![정책 정의에 대한 규칙](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. 완료되면 **저장** 을 선택합니다. 정책 정의를 저장하면 Azure Policy는 더 많은 속성 값을 생성하여 정책 정의에 추가합니다.

1. 그런 다음 정책을 적용하려는 정책 정의를 할당하려면 [정책 할당을 만듭니다](#create-policy-assignment).

Azure Policy 정의에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Policy 정의 구조](../governance/policy/concepts/definition-structure.md)
* [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
* [Azure Logic Apps에 대한 Azure Policy 기본 제공 정책 정의](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>정책 할당 만들기

그런 다음 정책을 적용하려는 정책 정의를 예를 들어, 단일 리소스 그룹, 여러 리소스 그룹, Azure AD(Azure Active Directory) 테넌트 또는 Azure 구독에 할당해야 합니다. 이 작업의 경우 다음 단계에 따라 정책 할당을 만듭니다.

1. 로그아웃한 경우 [Azure Portal](https://portal.azure.com)에 다시 로그인합니다. 검색 상자에 `policy`를 입력하고 **정책** 을 선택합니다.

   ![Azure Portal에서 “정책”을 찾아 선택합니다.](./media/block-connections-connectors/find-select-azure-policy.png)

1. **정책** 메뉴의 **작성** 에서 **할당**  >  **정책 할당** 을 선택합니다.

   ![“할당” > “할당”을 선택합니다.](./media/block-connections-connectors/add-new-policy-assignment.png)

1. **기본** 에서 정책 할당에 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **범위** | 예 | 정책 할당을 적용하려는 리소스입니다. <p><p>1. **범위** 상자 옆의 줄임표( **...** ) 단추를 선택합니다. <br>2. **구독** 목록에서 Azure 구독을 선택합니다. <br>3. 필요에 따라 **리소스 그룹** 목록에서 리소스 그룹을 선택합니다. <br>4. 완료되면 **선택** 을 선택합니다. |
   | **제외 항목** | 예 | 정책 할당에서 제외할 모든 Azure 리소스입니다. <p><p>1. **제외** 상자 옆의 줄임표( **...** ) 단추를 선택합니다. <br>2. **리소스** 목록에서 리소스 > **선택한 범위에 추가** 를 선택합니다. <br>3. 완료되면 **저장** 을 선택합니다. |
   | **정책 정의** | 예 | 할당하고 적용하려는 정책 정의의 이름입니다. 이 예에서는 Instagram 정책 예제인 “Instagram 연결 차단”을 사용하여 계속 진행합니다. <p><p>1. **정책 정의** 상자 옆에 있는 줄임표( **...** ) 단추를 선택합니다. <br>2. **형식** 필터 또는 **검색** 상자를 사용하여 정책 정의를 찾아 선택합니다. <br>3. 완료되면 **선택** 을 선택합니다. |
   | **할당 이름** | 예 | 정책 정의와 다른 경우 정책 할당에 사용할 이름입니다. |
   | **할당 ID** | 예 | 정책 할당에 대해 자동으로 생성된 ID입니다. |
   | **설명** | 예 | 정책 할당에 대한 설명입니다. |
   | **정책 적용** | 예 | 정책 할당을 사용하거나 사용하지 않도록 설정하는 설정 |
   | **할당자** | 예 | 정책 할당을 만들고 적용한 사용자의 이름입니다. |
   ||||

   예를 들어, 다음 Instagram 예제를 사용하여 Azure 리소스 그룹에 정책을 할당합니다.

   ![정책 할당 속성](./media/block-connections-connectors/policy-assignment-basics.png)

1. 완료되면 **검토 + 만들기** 를 선택합니다.

   정책을 만든 후 정책을 적용하려면 최대 15분 정도 기다려야 할 수 있습니다. 변경 내용의 지연 효과가 유사할 수도 있습니다.

1. 정책이 적용된 후 [정책을 테스트할](#test-policy)수 있습니다.

자세한 내용은 [빠른 시작: 비준수 리소스를 식별하는 정책 할당 만들기](../governance/policy/assign-policy-portal.md)를 참조하세요.

<a name="test-policy"></a>

## <a name="test-the-policy"></a>정책 테스트

정책을 시도하려면 논리 앱 디자이너에서 현재 제한된 커넥터를 사용하여 연결 만들기를 시작합니다. Instagram 예제를 계속 사용하면, Instagram에 로그인할 때 논리 앱에서 연결을 생성할 수 없다는 오류가 표시됩니다.

![적용된 정책으로 인한 연결 실패](./media/block-connections-connectors/connection-failure-message.png)

메시지에는 다음 정보가 포함됩니다.

| Description | 콘텐츠 |
|-------------|---------|
| 실패 이유 | `"Resource 'instagram' was disallowed by policy."` |
| 할당 이름 | `"Block Instagram connections"` |
| 할당 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| 정책 정의 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>다음 단계

* [Azure Policy](../governance/policy/overview.md)에 대해 자세히 알아보기