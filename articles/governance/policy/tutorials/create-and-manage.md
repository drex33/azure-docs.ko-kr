---
title: '자습서: 규정 준수를 적용하는 정책 빌드'
description: 이 자습서에서는 정책을 사용하여 표준을 적용하고, 비용을 제어하고, 보안을 유지하고, 엔터프라이즈 수준의 디자인 원칙을 적용합니다.
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: f7c8b2c84f2aa0c4186fbdf69d5a579033904bca
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324729"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>자습서: 규정 준수를 적용하는 정책 만들기 및 관리

회사 표준 및 서비스 수준 계약의 준수를 유지하는 데 있어 Azure에서 정책을 만들고 관리하는 방법을 이해하는 것이 중요합니다. 이 자습서에서는 Azure Policy를 사용하여 조직 전체에서 정책을 생성, 할당 및 관리하는 것과 관련된 보다 일반적인 작업 중 일부를 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - 나중에 만들 리소스에 대한 조건을 적용하는 정책 할당
> - 여러 리소스에 대한 규정 준수를 추적하기 위한 이니셔티브 정의 만들기 및 할당
> - 규정 비준수 또는 거부된 리소스 해결
> - 조직 전체에서 새 정책 구현

기존 리소스의 현재 규정 준수 상태를 식별하는 정책을 할당하려는 경우 이 빠른 시작 문서를 통해 방법을 살펴보세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="assign-a-policy"></a>정책 할당

Azure Policy 준수를 적용하기 위한 첫 번째 단계는 정책 정의를 할당하는 것입니다. 정책 정의는 정책이 적용되는 조건과 효과를 정의합니다. 이 예제에서는 _누락된 경우 리소스 그룹에서 태그 상속_ 이라는 기본 제공 정책 정의를 할당하여 부모 리소스 그룹의 값으로 지정된 태그를 태그가 누락된 새 리소스 또는 업데이트된 리소스에 추가합니다.

1. Azure Portal로 이동하여 정책을 할당합니다. **정책** 을 검색하고 선택합니다.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="검색 창에서 정책을 검색하는 스크린샷." border="false":::

1. Azure Policy 페이지의 왼쪽에서 **할당** 을 선택합니다. 할당은 특정 범위 내에서 수행하도록 할당된 정책입니다.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="정책 개요 페이지에서 할당 노드를 선택하는 스크린샷." border="false":::

1. **정책 - 할당** 창의 위쪽에서 **정책 할당** 을 선택합니다.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="할당 페이지에서 '정책 할당' 단추를 선택하는 스크린샷." border="false":::

1. **정책 할당** 페이지와 **기본 사항** 탭에서 줄임표를 선택하고 관리 그룹 또는 구독을 선택하여 **범위** 를 선택합니다. 원하는 경우 리소스 그룹을 선택합니다. 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다.
   그런 다음, **범위** 페이지의 맨 위에서 **선택** 을 선택합니다.

   이 예제에서는 **Contoso** 구독을 사용합니다. 자신의 구독은 다를 수 있습니다.

1. **범위** 에 따라 리소스를 제외할 수 있습니다. **제외** 는 **범위** 수준보다 한 수준 아래에서 시작됩니다. **제외** 는 원하는 경우에만 적용하면 되므로 지금은 비워 둡니다.

1. **정책 정의** 줄임표를 선택하여 사용 가능한 정의 목록을 엽니다. 정책 정의 **유형** 을 _기본 제공_ 으로 필터링하여 모든 정책을 살펴보고 설명을 읽을 수 있습니다.

1. **누락된 경우 리소스 그룹에서 태그 상속** 을 선택합니다. 즉시 찾을 수 없는 경우 검색 상자에 **태그 상속** 을 입력한 다음, ENTER를 누르거나 검색 상자 바깥쪽을 선택합니다.
   정책 정의를 찾아서 선택했으면 **사용 가능한 정의** 페이지의 맨 아래에서 **선택** 을 선택합니다.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="정책 정의를 선택하는 검색 필터의 스크린샷.":::

1. 선택한 정책 이름이 **할당 이름** 에 자동으로 채워지지만, 할당 이름을 변경할 수 있습니다. 이 예제에서는 _누락된 경우 리소스 그룹에서 태그 상속_ 을 그대로 둡니다. 선택적인 **설명** 을 추가할 수도 있습니다. 설명은 이 정책 할당에 대한 세부 정보를 제공합니다.

1. **정책 적용** 을 _사용_ 으로 둡니다. _사용 안 함_ 으로 할 경우 이 설정은 효과를 트리거하지 않고 정책의 결과를 테스트할 수 있습니다. 자세한 내용은 [적용 모드](../concepts/assignment-structure.md#enforcement-mode)를 참조하세요.

1. **할당한 사람** 은 로그인한 사용자를 기준으로 자동으로 입력됩니다. 이 필드는 선택 사항이므로 사용자 지정 값을 입력할 수 있습니다.

1. 마법사 맨 위에 있는 **매개 변수** 탭을 선택합니다.

1. **태그 이름** 에 _환경_ 을 입력합니다.

1. 마법사 맨 위에 있는 **수정** 탭을 선택합니다.

1. **수정 작업 만들기** 를 선택하지 않은 상태로 둡니다. 이 상자에서는 새 리소스 또는 업데이트된 리소스 외에도 기존 리소스를 변경하는 작업을 만들 수 있습니다. 자세한 내용은 [리소스 수정](../how-to/remediate-resources.md)을 참조하세요.

1. 이 정책 정의는 [수정](../concepts/effects.md#modify) 효과를 사용하므로 **관리 ID 만들기** 가 자동으로 선택됩니다. **권한** 은 정책 정의에 따라 자동으로 _기여자_ 로 설정됩니다. 자세한 내용은 [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md) 및 [수정 보안의 작동 방식](../how-to/remediate-resources.md#how-remediation-security-works)을 참조하세요.

1. 마법사 맨 위에 있는 **비규격 메시지** 탭을 선택합니다.

1. **비규격 메시지** 를 _이 리소스는 필수 태그가 없음_ 으로 설정합니다. 이 사용자 지정 메시지는 리소스가 거부되거나 정기 평가 중에 비규격 리소스에 대해 표시됩니다.

1. 마법사 맨 위에 있는 **검토 + 만들기** 탭을 선택합니다.

1. 선택 내용을 검토한 다음, 페이지 아래쪽에서 **만들기** 를 선택합니다.

## <a name="implement-a-new-custom-policy"></a>새 사용자 지정 정책 구현

기본 제공 정책 정의를 할당했으니, 이제 Azure Policy로 더 많은 일을 할 수 있습니다. 다음으로, 환경에서 만든 가상 머신을 G 시리즈에 사용할 수 없는지 유효성을 검사하여 비용을 절감하는 새로운 사용자 지정 정책을 만듭니다. 이렇게 하면 조직 내 사용자가 G 시리즈에서 가상 머신을 만들려고 시도할 때마다 요청이 거부됩니다.

1. Azure Policy 페이지의 왼쪽에 있는 **작성** 에서 **정의** 를 선택합니다.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="작성 그룹 아래의 정의 페이지 스크린샷." border="false":::

1. 페이지 위쪽에서 **+ 정책 정의** 를 선택합니다. 이 단추를 선택하면 **정책 정의** 페이지가 열립니다.

1. 다음 정보를 입력합니다.

   - 정책 정의가 저장되는 관리 그룹 또는 구독. **정의 위치** 에 있는 줄임표를 사용하여 선택합니다.

     > [!NOTE]
     > 이 정책 정의를 여러 구독에 적용하려는 경우 위치는 정책을 할당할 구독이 포함된 관리 그룹이어야 합니다. 이니셔티브 정의도 마찬가지입니다.

   - 정책 정의 이름 - _G 시리즈에 없는 VM SKU 필요_
   - 정책 정의의 용도에 대한 설명 - _이 정책 정의는 비용을 줄이기 위해 이 범위에서 만든 모든 가상 머신에서 G 시리즈 이외의 SKU를 사용하도록 강제합니다._
   - 기존 옵션(예: _Compute_) 중에서 선택하거나 이 정책 정의에 대한 새 범주를 만듭니다.
   - 다음 JSON 코드를 복사한 다음, 필요에 맞게 업데이트합니다.
      - 정책 매개 변수
      - 정책 규칙/조건 - 여기서는 'VM SKU 크기가 G 시리즈와 같음'입니다.
      - 정책 효과 - 여기서는 **거부** 입니다.

   JSON 코드는 다음과 같습니다. 수정된 코드를 Azure Portal에 붙여 넣습니다.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   정책 규칙의 _field_ 속성은 지원되는 값이어야 합니다. 전체 값 목록은 [정책 정의 구조 필드](../concepts/definition-structure.md#fields)에서 찾을 수 있습니다. 별칭의 예로 `"Microsoft.Compute/VirtualMachines/Size"`를 들 수 있습니다.

   더 많은 Azure Policy 샘플을 보려면 [Azure Policy 샘플](../samples/index.md)을 참조하세요.

1. **저장** 을 선택합니다.

## <a name="create-a-policy-definition-with-rest-api"></a>REST API를 사용하여 정책 정의 만들기

REST API를 사용하여 Azure Policy 정의에 대한 정책을 만들 수 있습니다. REST API를 사용하여 정책 정의를 만들고, 삭제하고, 기존 정의에 관한 정보를 가져올 수 있습니다. 정책 정의를 만들려면 다음 예제를 사용합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

다음 예제와 비슷한 요청 본문을 포함합니다.

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>PowerShell을 사용하여 정책 정의 만들기

PowerShell 예제를 진행하기 전에 최신 버전의 Azure PowerShell Az 모듈을 설치했는지 확인합니다.

`New-AzPolicyDefinition` cmdlet을 사용하여 정책 정의를 만들 수 있습니다.

파일에서 정책 정의를 만들려면 경로를 파일에 전달합니다. 외부 파일에 대해서는 다음 예제를 사용합니다.

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

로컬 파일에 대해서는 다음 예제를 사용합니다.

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

인라인 규칙을 사용하여 정책 정의를 만들려면 다음 예제를 사용합니다.

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

출력은 `$definition` 개체에 저장되어 정책 할당 중에 사용됩니다. 다음 예제에서는 매개 변수를 포함하는 정책 정의를 만듭니다.

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>PowerShell을 사용하여 정책 정의 보기

구독에서 모든 정책 정의를 보려면 다음 명령을 사용합니다.

```azurepowershell-interactive
Get-AzPolicyDefinition
```

기본 제공 정책을 비롯한 사용 가능한 모든 정책 정의를 반환합니다. 각 정책은 다음 형식으로 반환됩니다.

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Azure CLI를 사용하여 정책 정의 만들기

`az policy definition` 명령과 함께 Azure CLI를 사용하여 정책 정의를 만들 수 있습니다. 인라인 규칙을 사용하여 정책 정의를 만들려면 다음 예제를 사용합니다.

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Azure CLI를 사용하여 정책 정의 보기

구독에서 모든 정책 정의를 보려면 다음 명령을 사용합니다.

```azurecli-interactive
az policy definition list
```

기본 제공 정책을 비롯한 사용 가능한 모든 정책 정의를 반환합니다. 각 정책은 다음 형식으로 반환됩니다.

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>이니셔티브 정의 만들기 및 할당

이니셔티브 정의를 사용하면 여러 정책 정의를 그룹화하여 매우 중요한 하나의 목표를 달성할 수 있습니다. 이니셔티브는 할당 범위 내 리소스가 포함된 정책을 준수하는지 평가합니다. 이니셔티브 정의에 대한 자세한 내용은 [Azure Policy 개요](../overview.md)를 참조하세요.

### <a name="create-an-initiative-definition"></a>이니셔티브 정의 만들기

1. Azure Policy 페이지의 왼쪽에 있는 **작성** 에서 **정의** 를 선택합니다.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="작성 그룹 아래의 정의 페이지 스크린샷.":::

1. 페이지 위쪽에서 **+ 이니셔티브 정의** 를 선택하여 **이니셔티브 정의** 마법사를 엽니다.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="설정할 이니셔티브 정의 페이지 및 속성의 스크린샷.":::

1. **이니셔티브 위치** 줄임표를 사용하여 해당 정의를 저장할 관리 그룹 또는 구독을 선택합니다. 이전 페이지의 범위를 단일 관리 그룹 또는 구독으로 지정한 경우 **이니셔티브 위치** 가 자동으로 채워집니다.

1. 이니셔티브의 **이름** 과 **설명** 을 입력합니다.

   이 예제에서는 리소스가 보안 강화에 대한 정책 정의를 준수하는지 확인합니다. 이니셔티브의 이름은 **보안 강화** 이고, 설명은 **이 이니셔티브는 리소스 보안과 관련된 모든 정책 정의를 처리하기 위해 만들어졌습니다** 가 됩니다.

1. **범주** 로는 기존 옵션에서 선택하거나 새 범주를 만듭니다.

1. 이니셔티브의 **버전** 을 설정합니다(예: _1.0_).

   > [!NOTE]
   > 버전 값은 전적으로 메타데이터이며 Azure Policy 서비스의 업데이트 또는 프로세스에는 사용되지 않습니다.

1. 페이지의 아래쪽에서 **다음** 을 선택하거나 마법사의 위쪽에서 **정책** 탭을 선택합니다.

1. **정책 정의 추가** 단추를 선택하고 목록을 살펴봅니다. 이 이니셔티브에 추가할 정책 정의를 선택합니다. **보안 강화** 이니셔티브의 경우 정책 정의 옆에 있는 확인란을 선택하여 다음과 같은 기본 제공 정책 정의를 추가합니다.

   - 허용되는 위치
   - Azure Security Center에서 누락된 Endpoint Protection 모니터링
   - 네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 함
   - Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.
   - 가상 머신에서 디스크 암호화를 적용해야 합니다.
   - 리소스에 대한 태그 추가 또는 바꾸기(이 정책 정의를 두 번 추가)

   목록에서 각 정책 정의를 선택한 후에는 목록의 맨 아래에서 **추가** 를 선택합니다.
   _리소스에 대한 태그 추가 또는 바꾸기_ 정책 정의가 두 번 추가되므로 _참조 ID_ 가 서로 다릅니다.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="선택한 정책 정의와 이니셔티브 정의 페이지의 참조 id 및 그룹을 보여주는 스크린샷":::

   > [!NOTE]
   > 선택한 정책 정의는 추가된 정의를 하나 이상 선택하고 **그룹에 선택한 정책 추가** 를 선택하여 그룹에 추가할 수 있습니다. 먼저 그룹이 있어야 하며 마법사의 **그룹** 탭에서 그룹을 만들 수 있습니다.

1. 페이지의 아래쪽에서 **다음** 을 선택하거나 마법사의 위쪽에서 **그룹** 탭을 선택합니다. 이 탭에서 새 그룹을 추가할 수 있습니다. 이 자습서에서는 그룹을 추가하지 않습니다.

1. 페이지의 아래쪽에서 **다음** 을 선택하거나 마법사의 위쪽에서 **이니셔티브 매개 변수** 탭을 선택합니다. 하나 이상의 포함된 정책 정의에 전달하기 위해 이니셔티브에 매개 변수가 있어야 하는 경우 매개 변수는 여기에서 정의된 후 **정책 매개 변수** 탭에서 사용됩니다. 이 자습서에서는 이니서티브 매개 변수를 추가하지 않습니다.

   > [!NOTE]
   > 이니셔티브 정의에 저장된 이니셔티브 매개 변수는 이니셔티브에서 삭제할 수 없습니다. 이니셔티브 매개 변수가 더 이상 필요 없으면 정책 정의 매개 변수에 사용되지 않도록 제거합니다.

1. 페이지의 아래쪽에서 **다음** 을 선택하거나 마법사의 위쪽에서 **정책 매개 변수** 탭을 선택합니다.

1. 매개 변수가 있는 이니셔티브에 추가된 정책 정의가 그리드에 표시됩니다. _값 형식_ 은 '기본값', '값 설정' 또는 '이니셔티브 매개 변수 사용'입니다. '값 설정'을 선택하면 _값_ 에 관련 값이 입력됩니다. 정책 정의에 대한 매개 변수에 허용되는 값 목록이 있으면 해당 항목 상자는 드롭다운 목록 선택기입니다. '이니셔티브 매개 변수 사용'을 선택하면 **이니셔티브 매개 변수** 탭에서 만든 이니셔티브 매개 변수의 이름과 함께 드롭다운 목록 옵션이 제공됩니다.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="이니셔티브 정의 페이지의 정책 매개 변수 탭에서 허용되는 위치 정의 매개 변수에 허용되는 값의 옵션을 보여주는 스크린샷":::

   > [!NOTE]
   > 일부 `strongType` 매개 변수의 경우 값 목록은 자동으로 결정할 수 없습니다. 이러한 경우 매개 변수 행의 오른쪽에 줄임표가 표시됩니다. 이를 선택하면 '매개 변수 범위(&lt;매개 변수 이름&gt;)' 페이지가 열립니다. 이 페이지에서 값 옵션을 제공하는 데 사용할 구독을 선택합니다. 이 매개 변수 범위는 이니셔티브 정의를 만드는 중에 사용되며, 할당되는 경우 정책 평가 또는 이니셔티브의 범위에는 영향을 주지 않습니다.

   '허용 위치' _값 형식_ 을 '값 설정'으로 설정하고 드롭다운 목록에서 '미국 동부 2'를 선택합니다. _리소스에 태그 추가 또는 바꾸기_ 정책 정의의 두 가지 인스턴스와 관련하여, 아래와 같이 **태그 이름** 매개 변수를 'Env' 및 'CostCenter'로 설정하고 **태그 값** 매개 변수를 '테스트' 및 '랩'으로 설정합니다. 나머지는 '기본값'으로 둡니다. 매개 변수가 다른 동일한 정의를 이니셔티브에 두 번 사용하는 이 구성은 'Env' 태그를 'Test'라는 값으로 바꾸거나 추가하고, 'CostCenter' 태그를 할당 범위에서 리소스에 대한 '랩' 값으로 바꾸거나 추가합니다.

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="이니셔티브 정의 페이지의 정책 매개 변수 탭에서 허용되는 위치 정의 매개 변수에 허용되는 값으로 입력된 옵션과 두 태그 매개 변수 세트의 값을 보여주는 스크린샷":::

1. 페이지의 아래쪽 또는 마법사의 위쪽에서 **검토 + 만들기** 를 선택합니다.

1. 설정을 검토하고 **만들기** 를 선택합니다.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Azure CLI를 사용하여 정책 이니셔티브 정의 만들기

Azure CLI에서 `az policy set-definition` 명령을 사용하여 정책 이니셔티브 정의를 만들 수 있습니다. 기존 정책 정의를 사용하여 정책 이니셔티브 정의를 만들려면 다음 예제를 사용합니다.

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Azure PowerShell을 사용하여 정책 이니셔티브 정의 만들기

Azure PowerShell에서 `New-AzPolicySetDefinition` cmdlet을 사용하여 정책 이니셔티브 정의를 만들 수 있습니다. 기존 정책 정의를 사용하여 정책 이니셔티브 정의를 만들려면 다음 정책 정의 이니셔티브 정의 파일을 `VMPolicySet.json`으로 사용합니다.

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>이니셔티브 정의 할당

1. Azure Policy 페이지의 왼쪽에 있는 **작성** 에서 **정의** 를 선택합니다.

1. 앞에서 만든 **보안 강화** 이니셔티브 정의를 찾아서 선택합니다. 페이지 위쪽에서 **할당** 을 선택하여 **보안 강화: 이니셔티브 할당** 페이지를 엽니다.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="이니셔티브 정의 페이지에 있는 '할당' 단추의 스크린샷." border="false":::

   또한 선택된 행을 선택한 상태로 유지(또는 마우스 오른쪽 단추로 클릭)하거나 행 끝에 있는 줄임표(...)를 선택하여 상황에 맞는 메뉴를 표시할 수 있습니다. 그리고 **할당** 을 선택합니다.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="할당 기능을 선택하는 이니셔티브에 대한 컨텍스트 메뉴의 스크린샷." border="false":::

1. 다음 예제 정보를 입력하여 **보안 강화: 이니셔티브 할당** 페이지를 채웁니다. 사용자 고유의 정보를 사용해도 됩니다.

   - 범위: 이니셔티브를 저장한 관리 그룹 또는 구독이 기본값이 됩니다.
     저장 위치 내의 리소스 그룹 또는 구독에 이니셔티브를 할당하도록 범위를 변경할 수 있습니다.
   - 제외: 범위 내의 리소스에 이니셔티브 할당이 적용되지 않도록 리소스를 구성합니다.
   - 이니셔티브 정의 및 할당 이름: 보안 강화(할당되는 이니셔티브의 이름으로 미리 채워짐).
   - 설명: 이 이니셔티브 할당은 이 정책 정의 그룹을 적용하도록 조정됩니다.
   - 정책 적용: 기본값인 _사용_ 으로 둡니다.
   - 할당한 사람: 로그인한 사용자를 기준으로 자동 입력됩니다. 이 필드는 선택 사항이므로 사용자 지정 값을 입력할 수 있습니다.

1. 마법사 맨 위에 있는 **매개 변수** 탭을 선택합니다. 이전 단계에서 이니셔티브 매개 변수를 구성한 경우 여기서 값을 설정합니다.

1. 마법사 맨 위에 있는 **수정** 탭을 선택합니다. **관리 ID 만들기** 는 선택하지 않은 상태로 유지합니다. 할당 중인 정책이나 이니셔티브에 [deployIfNotExists](../concepts/effects.md#deployifnotexists) 또는 [modify](../concepts/effects.md#modify)가 적용되는 정책이 있으면 이 확인란을 _반드시_ 선택해야 합니다. 이 자습서에 사용되는 정책의 경우 해당 항목이 적용되지 않으므로 이 확인란을 비워 둡니다. 자세한 내용은 [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md) 및 [수정 보안의 작동 방식](../how-to/remediate-resources.md#how-remediation-security-works)을 참조하세요.

1. 마법사 맨 위에 있는 **검토 + 만들기** 탭을 선택합니다.

1. 선택 내용을 검토한 다음, 페이지 아래쪽에서 **만들기** 를 선택합니다.

## <a name="check-initial-compliance"></a>초기 규정 준수 확인

1. Azure Policy 페이지의 왼쪽에서 **규정 준수** 를 선택합니다.

1. **보안 가져오기** 이니셔티브를 찾습니다. ‘규정 준수 상태’가 **시작되지 않음** 일 가능성이 여전히 있습니다.
   이니셔티브를 선택하여 할당에 대한 모든 세부 정보를 가져옵니다.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="시작 안 함 상태의 할당 평가를 보여주는 이니셔티브 규정 준수 페이지의 스크린샷." border="false":::

1. 이니셔티브 할당이 완료되었으면 규정 준수 페이지에서 **준수 상태** 가 _준수_ 로 업데이트됩니다

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="완료 및 규정 준수 상태의 할당 평가를 보여주는 이니셔티브 규정 준수 페이지의 스크린샷." border="false":::

1. 이니셔티브 규정 준수 페이지에서 아무 정책을 선택하면 해당 정책에 대한 규정 준수 세부 정보 페이지가 열립니다. 이 페이지는 규정 준수에 대한 리소스 수준의 세부 정보를 제공합니다.

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>제외가 포함된 범위에서 비규격 또는 거부된 리소스 제거

특정 위치를 요구하도록 정책 이니셔티브를 할당한 후에는 다른 위치에서 생성된 리소스가 거부됩니다. 이 섹션에서는 단일 리소스 그룹에 대한 제외를 만들어서 리소스 생성 요청이 거부되는 문제 해결 방법을 단계별로 안내합니다. 제외는 해당 리소스 그룹에 정책(또는 이니셔티브)이 적용되지 않도록 방지합니다. 다음 예제에서는 모든 위치가 제외된 리소스 그룹에서 허용됩니다. 구독, 리소스 그룹 또는 개별 리소스에 제외를 적용할 수 있습니다.

> [!NOTE]
> [정책 예외](../concepts/exemption-structure.md)를 사용하여 리소스 평가를 건너뛸 수도 있습니다. 자세한 내용은 [Azure Policy의 범위](../concepts/scope.md)를 참조하세요.

할당된 정책 또는 이니셔티브에 의해 금지되는 배포는 배포 대상으로 지정된 리소스 그룹에서 볼 수 있습니다. 페이지 왼쪽에서 **배포** 를 선택하고 실패한 배포에서 **배포 이름** 을 선택합니다. 거부된 리소스가 _사용할 수 없음_ 상태와 함께 나열됩니다. 리소스를 거부한 정책 또는 이니셔티브와 할당을 확인하려면 개요 페이지에서 **실패했습니다. 자세한 내용을 보려면 여기를 클릭하세요. ->** 를 클릭합니다. 페이지 오른쪽에 창이 열리고 오류 정보가 표시됩니다. **오류 세부 정보** 아래에 관련 정책 개체의 GUID가 있습니다.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="정책 할당에 의해 거부되어 실패한 배포의 스크린샷." border="false":::

Azure Policy 페이지: 페이지 왼쪽에서 **규정 준수** 를 선택하고 **보안 강화** 정책 이니셔티브를 선택합니다. 이 페이지에서는 차단된 리소스의 **거부** 수가 증가합니다. **이벤트** 탭에는 정책 정의에 의해 거부된 리소스를 만들거나 배포하려고 시도한 사용자에 대한 세부 정보가 있습니다.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="이니셔티브 규정 준수 페이지의 이벤트 탭 및 정책 이벤트 세부 정보에 대한 스크린샷." border="false":::

이 예제에서는 Contoso의 수석 가상화 전문가 중 한 명인 Trent Baker가 필수 작업을 수행하고 있었습니다. Trent에 예외를 위한 공간을 부여해야 합니다. 그래서 **LocationsExcluded** 라고 하는 새 리소스 그룹을 만들었으며, 이제 이 정책 할당에 예외를 부여할 것입니다.

### <a name="update-assignment-with-exclusion"></a>할당에 제외 업데이트

1. Azure Policy 페이지의 왼쪽에 있는 **작성** 에서 **할당** 을 선택합니다.

1. 모든 정책 할당을 검색하여 _보안 강화_ 정책 할당을 엽니다.

1. 줄임표를 선택하고 제외할 리소스 그룹(이 예제에서는 _LocationsExcluded_)을 선택하여 **제외** 를 설정합니다. **선택한 범위에 추가** 를 선택하고 **저장** 을 선택합니다.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="정책 할당에 제외된 리소스 그룹을 추가하는 이니셔티브 할당 페이지의 제외 옵션 스크린샷." border="false":::

   > [!NOTE]
   > 정책 정의 및 그 영향에 따라 할당 범위 내의 리소스 그룹에 속한 특정 리소스에 제외를 부여할 수도 있습니다. 이 자습서에서 사용한 **거부** 효과와 마찬가지로, 이미 있는 특정 리소스에 제외를 설정하는 것은 의미가 없습니다.

1. **검토 + 저장** 을 선택하고 **저장** 을 선택합니다.

이 섹션에서는 단일 리소스 그룹에 대한 제외를 만들어서 요청 거부 문제를 해결했습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 리소스가 더 이상 필요 없으면 다음 단계에 따라 위에서 만든 정책 할당 또는 정의를 삭제합니다.

1. Azure Policy 페이지의 왼쪽 창에 있는 **작성** 아래에서 **정의**(또는 할당을 삭제하려는 경우 **할당** 을 선택)를 선택합니다.

1. 제거할 새 이니셔티브 또는 정책 정의(또는 할당)를 검색합니다.

1. 행을 마우스 오른쪽 단추로 클릭하거나 정의(또는 할당) 끝에 있는 줄임표를 선택하고 **정의 삭제**(또는 **할당 삭제**)를 선택합니다.

## <a name="review"></a>검토

이 자습서에서는 다음 작업을 성공적으로 완료했습니다.

> [!div class="checklist"]
> - 나중에 만들 리소스에 대한 조건을 적용하는 정책 할당
> - 여러 리소스에 대한 규정 준수를 추적하기 위한 이니셔티브 정의 만들기 및 할당
> - 규정 비준수 또는 거부된 리소스 해결
> - 조직 전체에서 새 정책 구현

## <a name="next-steps"></a>다음 단계

정책 정의의 구조에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Policy 정의 구조](../concepts/definition-structure.md)
