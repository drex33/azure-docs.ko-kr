---
title: '자습서: 사용자 지정 정책 정의 만들기'
description: 이 자습서에서는 Azure Policy에 대한 사용자 지정 정책 정의를 만들어 사용자 지정 비즈니스 규칙을 Azure 리소스에 적용합니다.
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: ee7939461c6d655447ebbd32079ec2e1345a89ad
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323620"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>자습서: 사용자 지정 정책 정의 만들기

사용자 지정 정책 정의를 통해 고객은 Azure 사용에 대한 자체 규칙을 정의할 수 있습니다. 이러한 규칙은 종종 다음 항목을 적용합니다.

- 보안 관행
- 비용 관리
- 조직별 규칙(예: 이름 지정 또는 위치)

사용자 지정 정책을 만드는 비즈니스 요인이 무엇이든, 새 사용자 지정 정책을 정의하는 단계는 동일합니다.

사용자 지정 정책을 만들기 전에 [정책 샘플](../samples/index.md)을 검사하여 요구 사항과 일치하는 기존 정책이 있는지 여부를 확인합니다.

사용자 지정 정책을 만드는 단계는 다음과 같습니다.

> [!div class="checklist"]
> - 비즈니스 요구 사항 식별
> - 각 요구 사항을 Azure 리소스 속성에 매핑
> - 속성을 별칭에 매핑
> - 사용할 효과 결정
> - 정책 정의 작성

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="identify-requirements"></a>ID 요구 사항

정책 정의를 만들기 전에, 정책의 의도를 이해해야 합니다. 이 자습서에서는 다음과 같은 일반적인 기업 보안 요구 사항을 목표로 사용하여 관련 단계를 보여드리겠습니다.

- 각 스토리지 계정에서 HTTPS 사용
- 각 스토리지 계정에서 HTTP 사용 안 함

요구 사항에서 "사용" 및 "사용 안 함" 리소스 상태를 정확하게 식별해야 합니다.

예상되는 리소스 상태를 정의했지만, 비 규격 리소스에 원하는 내용은 아직 정의하지 않았습니다. Azure Policy는 많은 [효과](../concepts/effects.md)를 지원합니다. 이 자습서에서는 비즈니스 규칙을 준수하지 않는 리소스 만들기를 차단하는 비즈니스 요구 사항을 정의할 것입니다. 이 목표를 달성하기 위해 [Deny](../concepts/effects.md#deny) 효과를 사용하겠습니다. 또한 특정 할당에 대한 정책을 일시 중단하려고 합니다. 따라서 [Disabled](../concepts/effects.md#disabled) 효과를 사용하고, 정책 정의에 [매개 변수](../concepts/definition-structure.md#parameters) 효과를 만들겠습니다.

## <a name="determine-resource-properties"></a>리소스 속성 확인

비즈니스 요구 사항에 따라 Azure Policy를 통해 감사할 Azure 리소스는 스토리지 계정입니다. 그러나 우리는 정책 정의에 어떤 속성을 사용해야 하는지 아직 모릅니다. Azure Policy는 리소스의 JSON 표현과 비교하여 평가하므로 해당 리소스에 사용할 수 있는 속성을 이해해야 합니다.

Azure 리소스의 속성을 확인하는 방법은 여러 가지가 있습니다. 이 자습서에는 각 방법을 살펴보도록 하겠습니다.

- VS Code용 Azure Policy 확장
- ARM 템플릿(Azure Resource Manager 템플릿)
  - 기존 리소스 내보내기
  - 환경 만들기
  - 빠른 시작 템플릿(GitHub)
  - 템플릿 참조 문서
- Azure Resource Explorer

### <a name="view-resources-in-vs-code-extension"></a>VS Code 확장에서 리소스 보기

[VS Code 확장](../how-to/extension-for-vscode.md#search-for-and-view-resources)을 사용하여 사용자 환경에서 리소스를 찾고 각 리소스에 대한 Resource Manager 속성을 볼 수 있습니다.

### <a name="arm-templates"></a>ARM 템플릿

관리하려는 속성이 포함된 [ARM 템플릿](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md)을 찾는 여러 가지 방법이 있습니다.

#### <a name="existing-resource-in-the-portal"></a>포털의 기존 리소스

속성을 찾는 가장 간단한 방법은 동일한 형식의 기존 리소스를 살펴보는 것입니다. 적용하려는 설정을 사용하여 이미 구성된 리소스는 비교 값을 제공합니다.
해당 리소스에 대한 Azure Portal에서 **템플릿 내보내기** 페이지(**설정** 아래)를 확인하세요.

> [!WARNING]
> Azure Portal에서 내보낸 ARM 템플릿은 [deployIfNotExists](../concepts/effects.md#deployifnotexists) 정책 정의에서 ARM 템플릿의 `deployment` 속성에 직접 연결할 수 없습니다.

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Azure Portal의 기존 리소스에 대한 템플릿 내보내기 페이지의 스크린샷." border="false":::

스토리지 계정에 대해 이렇게 하면 다음 예제와 비슷한 템플릿이 표시됩니다.

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

**속성** 아래를 보면 **supportsHttpsTrafficOnly** 라는 값이 **false** 로 설정되어 있습니다. 이 속성이 우리가 찾는 속성일 가능성이 있습니다. 그리고 리소스 **형식** 은 **Microsoft.Storage/storageAccounts** 입니다. 이 형식은 정책을 이 형식의 리소스에만 적용하도록 제한할 수 있습니다.

#### <a name="create-a-resource-in-the-portal"></a>포털에서 리소스 만들기

포털을 이용하는 또 다른 방법은 리소스 만들기 환경입니다. 포털을 통해 스토리지 계정을 만들 때 **고급** 탭 아래를 보면 **보안 전송 필요** 라는 옵션이 있습니다. 이 속성은 _사용 안 함_ 및 _사용_ 옵션을 제공합니다. 정보 아이콘에는 이 옵션이 우리가 찾는 속성일 확률이 높다는 것을 확인해주는 추가 텍스트가 있습니다. 그러나 포털의 이 화면에는 속성 이름이 표시되지 않습니다.

**검토 + 만들기** 탭에서 페이지 하단을 보면 **자동화 템플릿 다운로드** 링크가 있습니다. 이 링크를 선택하면 우리가 구성한 리소스를 만드는 템플릿이 열립니다. 이 예에서는 다음과 같은 두 가지 핵심 정보가 보입니다.

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

이 정보는 속성 형식을 알려줄 뿐 아니라 우리가 찾는 속성이 **supportsHttpsTrafficOnly** 라는 것을 확인해 줍니다.

#### <a name="quickstart-templates-on-github"></a>GitHub의 빠른 시작 템플릿

GitHub의 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)에는 다양한 리소스를 위해 빌드된 수백 개의 ARM 템플릿이 있습니다. 이러한 템플릿을 통해 원하는 리소스 속성을 찾을 수 있습니다. 일부 속성은 우리가 찾는 속성처럼 보이지만, 제어 대상이 다릅니다.

#### <a name="resource-reference-docs"></a>리소스 참조 문서

**supportsHttpsTrafficOnly** 가 올바른 속성인지 확인하려면 스토리지 공급자의 [스토리지 계정 리소스](/azure/templates/microsoft.storage/2018-07-01/storageaccounts)에 대한 ARM 템플릿 참조를 확인하세요. 속성 개체는 유효한 매개 변수 목록을 갖고 있습니다. [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) 링크를 선택하면 허용되는 속성 표가 표시됩니다. **supportsHttpsTrafficOnly** 가 있으며 해당 설명을 보면 우리가 비즈니스 요구 사항을 충족하기 위해 찾고 있는 속성과 일치합니다.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Azure 리소스를 살펴보는 또 다른 방법은 [Azure Resource Explorer](https://resources.azure.com)(미리 보기)입니다. 이 도구는 구독의 컨텍스트를 사용하므로 Azure 자격 증명을 사용하여 웹 사이트에 인증해야 합니다. 인증되면 공급자, 구독, 리소스 그룹 및 리소스별로 찾아볼 수 있습니다.

스토리지 계정 리소스를 찾아서 속성을 살펴봅니다. **supportsHttpsTrafficOnly** 속성이 여기에도 있습니다. **설명서** 탭을 선택하면 속성 설명이 우리가 참조 문서에서 찾은 내용과 일치하는 것을 볼 수 있습니다.

## <a name="find-the-property-alias"></a>속성 별칭 찾기

리소스 속성을 확인했지만, 해당 속성을 [별칭](../concepts/definition-structure.md#aliases)에 매핑해야 합니다.

Azure 리소스의 별칭을 확인하는 몇 가지 방법이 있습니다. 이 자습서에는 각 방법을 살펴보도록 하겠습니다.

- VS Code용 Azure Policy 확장
- Azure CLI
- Azure PowerShell

### <a name="get-aliases-in-vs-code-extension"></a>VS Code 확장에서 별칭 가져오기

VS Code 확장용 Azure Policy 확장을 사용하면 리소스를 쉽게 찾아보고 [별칭을 검색](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties)할 수 있습니다.

> [!NOTE]
> VS Code 확장은 Resource Manager 모드 속성만 노출하고 [리소스 공급자 모드](../concepts/definition-structure.md#mode) 속성은 표시하지 않습니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 `az provider` 명령 그룹은 리소스 별칭을 검색하는 데 사용됩니다. 앞에서 Azure 리소스에 대해 찾은 세부 정보를 기반으로 **Microsoft.Storage** 네임스페이스를 필터링할 것입니다.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

결과를 보면 스토리지 계정에서 **supportsHttpsTrafficOnly** 라는 별칭을 지원하는 것을 알 수 있습니다. 이 별칭이 있다는 것은 비즈니스 요구 사항을 적용하는 정책을 작성할 수 있다는 의미입니다.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell에서 `Get-AzPolicyAlias` cmdlet은 리소스 별칭을 검색하는 데 사용됩니다. 앞에서 Azure 리소스에 대해 찾은 세부 정보를 기반으로 **Microsoft.Storage** 네임스페이스를 필터링할 것입니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Azure CLI와 마찬가지로, 결과를 보면 스토리지 계정에서 **supportsHttpsTrafficOnly** 라는 별칭을 지원하는 것을 알 수 있습니다.

## <a name="determine-the-effect-to-use"></a>사용할 효과 결정

비 규격 리소스를 어떻게 할 것인지 결정하는 사안은 무엇을 평가할 것인지 결정하는 사안만큼 중요합니다. 비 규격 리소스에 대해 나올 수 있는 각 대응을 [효과](../concepts/effects.md)라고 합니다. 효과는 비 규격 리소스가 로깅되는지, 차단되는지, 데이터가 추가되었는지 또는 리소스를 규정 준수 상태로 되돌리기 위한 배포가 연결되는지 여부를 제어합니다.

우리 예제에서는 Azure 환경에 비 규격 리소스를 만들지 않을 것이므로 우리에게 필요한 효과는 Deny입니다. 감사는 정책을 Deny로 설정하기 전에 정책의 영향을 확인하기에 좋은 정책 효과입니다. 할당별 효과를 보다 쉽게 변경하는 한 가지 방법은 효과를 매개 변수화하는 것입니다. 자세한 방법은 아래의 [매개 변수](#parameters)를 참조하세요.

## <a name="compose-the-definition"></a>정의 작성

우리가 관리하려는 대상의 속성 세부 정보와 별칭을 확보했습니다. 다음으로, 정책 규칙 자체를 구성하겠습니다. 아직 정책 언어에 익숙하지 않은 경우 [정책 정의 구조](../concepts/definition-structure.md)에서 정책 정의를 구성하는 방법을 알아보세요. 다음은 정책 정의가 어떻게 생겼는지 보여주는 빈 템플릿입니다.

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>메타데이터

처음 세 개 구성 요소는 정책 메타데이터입니다. 이러한 구성 요소는 우리가 값을 만들 대상을 알고 있기 때문에 값을 쉽게 제공할 수 있습니다. [모드](../concepts/definition-structure.md#mode)는 주로 태그 및 리소스 위치와 관련이 있습니다. 평가 범위를 태그 지원 리소스로 제한할 필요가 없으므로 **모드** 의 값으로 _all_ 을 사용하겠습니다.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>매개 변수

평가 변경에 매개 변수를 사용하지는 않았지만, 매개 변수를 사용하여 문제 해결을 위한 **효과** 변경을 허용할 것입니다. **effectType** 매개 변수를 정의하고 **Deny** 및 **Disabled** 로 제한하겠습니다. 이러한 두 옵션은 비즈니스 요구 사항과 일치합니다. 완성된 매개 변수 블록은 다음 예제와 비슷합니다.

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>정책 규칙

[정책 규칙](../concepts/definition-structure.md#policy-rule) 작성은 사용자 지정 정책 정의를 빌드하는 최종 단계입니다. 다음 내용을 테스트하는 두 개의 명령문이 식별되었습니다.

- 스토리지 계정 **형식** 이 **Microsoft.Storage/storageAccounts** 입니다.
- 스토리지 계정 **supportsHttpsTrafficOnly** 는 **true** 가 아닙니다.

이러한 두 명령문이 모두 true여야 하므로 **allOf** [논리 연산자](../concepts/definition-structure.md#logical-operators)를 사용하겠습니다. 정적 선언을 만드는 대신 **effectType** 매개 변수를 효과로 전달하겠습니다. 완성된 규칙은 다음 예제와 비슷합니다.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>완성된 정의

정책의 세 부분이 모두 정의되었으며, 다음은 완성된 정의입니다.

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

완성된 정의는 새 정책 만들기에 사용할 수 있습니다. 포털 및 각 SDK(Azure CLI, Azure PowerShell 및 REST API)는 여러 방법으로 정의를 수락하므로, 각각의 명령을 검토하여 올바른 사용법을 확인하세요. 그 후 매개 변수화된 효과를 사용하여 적절한 리소스에 할당하고 스토리지 계정의 보안을 관리하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 리소스를 모두 마쳤으면 다음 단계를 사용하여 위에서 만든 할당 또는 정의를 삭제합니다.

1. Azure Policy 페이지의 왼쪽 창에 있는 **작성** 아래에서 **정의**(또는 할당을 삭제하려는 경우 **할당** 을 선택)를 선택합니다.

1. 제거할 새 이니셔티브 또는 정책 정의(또는 할당)를 검색합니다.

1. 행을 마우스 오른쪽 단추로 클릭하거나 정의(또는 할당) 끝에 있는 줄임표를 선택하고 **정의 삭제**(또는 **할당 삭제**)를 선택합니다.

## <a name="review"></a>검토

이 자습서에서는 다음 작업을 성공적으로 완료했습니다.

> [!div class="checklist"]
> - 비즈니스 요구 사항 식별
> - 각 요구 사항을 Azure 리소스 속성에 매핑
> - 속성을 별칭에 매핑
> - 사용할 효과 결정
> - 정책 정의 작성

## <a name="next-steps"></a>다음 단계

다음으로, 사용자 지정 정책 정의를 사용하여 정책을 만들고 할당합니다.

> [!div class="nextstepaction"]
> [정책 정의 만들기 및 할당](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
