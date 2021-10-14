---
title: 정책 정의 구조에 대한 세부 정보
description: 정책 정의를 사용하여 조직에서 Azure 리소스에 대한 규칙을 설정하는 방법을 설명합니다.
ms.date: 09/01/2021
ms.topic: conceptual
ms.openlocfilehash: 08e4df487786de0e43e7bc1ae7c0a5debb6a9752
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002513"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy 정의 구조

Azure Policy는 리소스에 대한 규칙을 설정합니다. 정책 정의는 리소스 규정 준수 조건 및 [조건](#conditions) 충족 시 미치는 영향을 설명합니다. 조건은 리소스 속성 [필드](#fields) 또는 [값](#value)을 필요한 값과 비교합니다. 리소스 속성 필드에는 [별칭](#aliases)을 사용하여 액세스합니다. 리소스 속성 필드가 배열인 경우 특별한 [배열 별칭](#understanding-the--alias)을 사용하여 모든 배열 멤버의 값을 선택하고 각 멤버에 조건을 적용할 수 있습니다. [조건](#conditions)에 대해 자세히 알아보세요.

규칙을 정의하여 비용을 제어하고 리소스를 보다 쉽게 관리할 수 있습니다. 예를 들어, 특정 유형의 가상 머신만 허용되게 지정할 수 있습니다. 또는 리소스가 특정 태그를 갖도록 요구할 수 있습니다. 정책 할당은 모든 자식 리소스로 상속됩니다. 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 해당 정책을 적용할 수 있습니다.

정책 정의 _policyRule_ 스키마는 다음 위치에서 찾을 수 있습니다. [https://schema.management.azure.com/schemas/2020-10-01/policyDefinition.json](https://schema.management.azure.com/schemas/2020-10-01/policyDefinition.json)

JSON을 사용하여 정책 정의를 만듭니다. 정책 정의에는 다음 요소가 포함됩니다.

- 표시 이름
- description
- mode
- metadata
- 매개 변수
- 정책 규칙
  - 논리 평가
  - 영향

예를 들어 다음 JSON에서는 리소스가 배포되는 위치를 제한하는 정책을 보여줍니다.

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
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

Azure Policy 기본 제공 및 패턴은 [Azure Policy 샘플](../samples/index.md)에 있습니다.

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **description** 을 사용하여 정책 정의를 식별하고 사용하는 시기에 대한 컨텍스트를 제공합니다. **displayName** 은 최대 길이가 _128_ 자이고 **description** 은 최대 길이가 _512_ 자입니다.

> [!NOTE]
> 정책 정의를 만들거나 업데이트하는 동안 **id**, **type**, **name** 이 JSON 외부의 속성으로 정의되며 JSON 파일에는 필요하지 않습니다. SDK를 통해 정책 정의를 가져오면 **id**, **type**, **name** 속성이 JSON의 일부로 반환되지만 각각은 정책 정의와 관련된 읽기 전용 정보입니다.

## <a name="type"></a>형식

**유형** 속성은 설정할 수 없지만 SDK에서 반환되고 포털에 표시되는 세 가지 값이 있습니다.

- `Builtin`: 이러한 정책 정의는 Microsoft에서 제공하고 유지관리합니다.
- `Custom`: 고객에 의해 생성된 모든 정책 정의에는 이 값이 있습니다.
- `Static`: Microsoft **소유권** 의 [규제 규정 준수](./regulatory-compliance.md) 정책 정의를 나타냅니다. 이러한 정책 정의에 대한 준수 결과는 Microsoft 인프라의 타사 감사 결과입니다. Azure Portal에서 이 값은 **Microsoft에서 관리** 되는 것으로 표시되기도 합니다. 자세한 내용은 [클라우드의 공유 책임](../../../security/fundamentals/shared-responsibility.md)을 참조하세요.

## <a name="mode"></a>Mode

**모드** 는 정책이 Azure Resource Manager 속성 또는 리소스 공급자 속성 중 무엇을 대상으로 하는지 따라 구성됩니다.

### <a name="resource-manager-modes"></a>Resource Manager 모드

**모드** 는 정책 정의에 대해 평가할 리소스 종류를 결정합니다. 지원되는 모드는 다음과 같습니다.

- `all`: 리소스 그룹, 구독 및 모든 리소스 종류를 평가합니다.
- `indexed`: 태그 및 위치를 지원하는 리소스 종류만 평가합니다.

예를 들어 리소스 `Microsoft.Network/routeTables`는 태그와 위치를 지원하고 두 모드 모두에서 계산됩니다. 하지만 리소스 `Microsoft.Network/routeTables/routes`에는 태그를 지정할 수 없으며 `Indexed` 모드에서 평가되지 않습니다.

대부분 **mode** 를 `all`로 설정하는 것이 좋습니다. 포털을 통해 생성된 모든 정책 정의는 `all` 모드를 사용합니다. PowerShell 또는 Azure CLI를 사용하는 경우 **mode** 매개 변수를 수동으로 지정할 수 있습니다. 정책 정의에 **mode** 값이 포함되지 않으면 기본적으로 Azure PowerShell에서는 `all`로 설정되고 Azure CLI에서는 `null`로 설정됩니다. `null` 모드는 이전 버전과의 호환성을 지원하기 위해 `indexed`를 사용하는 것과 같습니다.

`indexed`는 태그 또는 위치를 시스템에 적용하는 정책을 만들 때 사용해야 합니다. 이 모드는 반드시 사용해야 하는 것은 아니지만, 사용하는 경우 태그와 위치를 지원하지 않는 리소스가 규정 준수 결과에 미준수 항목으로 표시되지 않습니다. 예외는 **리소스 그룹** 및 **구독** 입니다. 리소스 그룹 또는 구독에서 위치 또는 태그를 적용하는 정책 정의는 **mode** 를 `all`로 설정하고 특히, `Microsoft.Resources/subscriptions/resourceGroups` 또는 `Microsoft.Resources/subscriptions` 유형을 대상으로 지정해야 합니다. 예제는 [패턴: 태그 - 샘플 #1](../samples/pattern-tags.md)을 참조하세요. 태그를 지원하는 리소스 목록은 [Azure 리소스에 대한 태그 지원](../../../azure-resource-manager/management/tag-support.md)을 참조하세요.

### <a name="resource-provider-modes"></a>리소스 공급자 모드

다음 리소스 공급자 모드는 완전히 지원됩니다.

- `Microsoft.Kubernetes.Data`는 Azure 온/오프 Kubernetes 클러스터를 관리하는 데 사용됩니다. 해당 리소스 공급자 모드를 사용하는 정의 파일은 _감사_, _거부_ 및 _사용 안 함_ 효과를 사용합니다. 이 모드는 사용자 지정 정의를 _공개 미리 보기_ 로 지원합니다. 기존 OPA(Open [Policy Agent)](https://www.openpolicyagent.org/) GateKeeper v3 제약 조건 템플릿에서 사용자 지정 정의를 만들려면 제약 조건 [템플릿에서](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates)정책 [정의 만들기를](../how-to/extension-for-vscode.md) 참조하세요. [EnforceOPAConstraint](./effects.md#enforceopaconstraint) 효과는 _사용되지 않습니다_.

다음 리소스 공급자 모드는 현재 **미리 보기** 로 지원됩니다.

- `Microsoft.ContainerService.Data`는 [Azure Kubernetes Service](../../../aks/intro-kubernetes.md)에서 허용 컨트롤러 규칙을 관리하는 데 사용합니다. 해당 리소스 공급자 모드를 사용하는 정의 파일은 [EnforceRegoPolicy](./effects.md#enforceregopolicy) 효과를 **반드시** 사용해야 합니다. 이 모드는 _사용되지 않습니다_.
- `Microsoft.KeyVault.Data`는 [Azure Key Vault](../../../key-vault/general/overview.md)에서 자격 증명 모음 및 인증서를 관리하는 데 사용됩니다. 이러한 정책 정의에 대한 자세한 내용은 [Azure Key Vault와 Azure Policy 통합](../../../key-vault/general/azure-policy.md)을 참조하세요.

> [!NOTE]
> 리소스 공급자 모드는 기본 제공 정책 정의만 지원하며 명시적으로 명시되지 않은 경우 [예외를](./exemption-structure.md) 지원하지 않습니다.

## <a name="metadata"></a>메타데이터

선택적 `metadata` 속성은 정책 정의에 대한 정보를 저장합니다. 고객은 `metadata`에서 조직에 유용한 모든 속성 및 값을 정의할 수 있습니다. 그러나 Azure Policy 및 기본 제공에서 사용하는 몇 가지 _공통_ 속성이 있습니다. 각 `metadata` 속성에는 1024자 제한이 있습니다.

### <a name="common-metadata-properties"></a>공통 메타데이터 속성

- `version`(문자열): 정책 정의 콘텐츠의 버전에 대한 세부 정보를 추적합니다.
- `category`(문자열): Azure Portal에서 정책 정의가 표시되는 범주를 결정합니다.
- `preview`(부울): 정책 정의가 _미리 보기_ 인 경우에 대한 True 또는 False 플래그입니다.
- `deprecated`(부울): 정책 정의가 _더 이상 사용되지 않음_ 으로 표시된 경우에 대한 True 또는 False 플래그입니다.
- `portalReview` (string): 필요한 입력에 관계없이 포털에서 매개 변수를 검토할지 여부를 결정합니다. 

> [!NOTE]
> Azure Policy 서비스는 `version`, `preview` 및 `deprecated` 속성을 사용하여 기본 제공 정책 정의 또는 이니셔티브 및 상태에 대한 변경 수준 전달합니다. `version`의 형식은 `{Major}.{Minor}.{Patch}`입니다. _deprecated_ 또는 _preview_ 와 같은 특정 상태가 `version` 속성에 추가되거나 다른 속성에 **부울** 로 추가됩니다. Azure Policy 버전 기본 제공 방식에 대한 자세한 내용은 [기본 제공 버전 관리](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)를 참조하세요.

## <a name="parameters"></a>매개 변수

매개 변수는 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 양식의 필드 `name`, `address`, `city`, `state`와 같은 매개 변수에 관해 생각해 봅니다. 이러한 매개 변수는 항상 그대로 유지되지만, 그 값은 양식을 작성하는 개별 값에 기초하여 달라집니다.
매개 변수는 정책을 만들 때와 같은 방법으로 작동합니다. 정책 정의에 매개 변수를 포함함으로써 서로 다른 값을 사용하여 다양한 시나리오에 대해 해당 정책을 재사용할 수 있습니다.

> [!NOTE]
> 매개 변수를 기존 및 할당된 정의에 추가할 수 있습니다. 새 매개 변수는 **defaultValue** 속성을 포함해야 합니다. 이렇게 하면 정책 또는 이니셔티브의 기존 지정이 간접적으로 유효하지 않게 됩니다.

### <a name="parameter-properties"></a>매개 변수 속성

매개 변수에는 정책 정의에 사용되는 다음 속성이 있습니다.

- `name`: 매개 변수의 이름입니다. 정책 규칙 내의 `parameters` 배포 함수에서 사용됩니다. 자세한 내용은 [매개 변수 값 사용](#using-a-parameter-value)을 참조하세요.
- `type`: 매개 변수가 **문자열**, **배열**, **개체**, **부울**, **정수**, **float**, **datetime** 중에 무엇인지 확인합니다.
- `metadata`: Azure Portal에서 사용자에게 친숙한 정보를 표시하는 데 주로 사용되는 하위 속성을 정의합니다.
  - `description`: 매개 변수의 용도에 대한 설명입니다. 허용 가능한 값의 예를 제공하는 데 사용할 수 있습니다.
  - `displayName`: 매개 변수에 대해 포털에 표시되는 이름입니다.
  - `strongType`: (선택 사항) 포털을 통해 정책 정의를 할당할 때 사용됩니다. 컨텍스트 인식 목록을 제공합니다. 자세한 내용은 [strongType](#strongtype)을 참조하세요.
  - `assignPermissions`: (선택 사항) 정책 할당 중에 Azure Portal에서 역할 할당을 만들도록 하려면 _true_ 로 설정합니다. 이 속성은 할당 범위 외부에서 사용 권한을 할당하려는 경우에 유용합니다. 정책 내 역할 정의마다(또는 이니셔티브의 모든 정책에서 역할 정의마다) 하나의 역할 할당이 있습니다. 매개 변수 값은 유효한 리소스 또는 범위여야 합니다.
- `defaultValue`: (선택 사항) 값이 지정되지 않은 경우 할당에서 매개 변수의 값을 설정합니다.
  할당된 기존 정책 정의를 업데이트할 때 필요합니다.
- `allowedValues`: (선택 사항) 할당 중에 매개 변수가 허용하는 값의 배열을 제공합니다. 허용되는 값 비교는 대/소문자를 구분합니다.

예를 들어 리소스를 배포할 수 있는 위치를 제한하는 정책 정의를 정의할 수 있습니다. 해당 정책 정의의 매개 변수는 **allowedLocations** 일 수 있습니다. 이 매개 변수는 정책 정의의 각 할당에서 허용되는 값을 제한하는 데 사용됩니다. **strongType** 을 사용하면 포털을 통해 할당을 완료할 때 경험이 개선됩니다.

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>매개 변수 값 사용

정책 규칙에서 다음 `parameters` 함수 구문을 사용하여 매개 변수를 참조할 수 있습니다.

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

이 샘플은 [매개 변수 속성](#parameter-properties)에 설명된 **allowedLocations** 매개 변수를 참조합니다.

### <a name="strongtype"></a>strongType

`metadata` 속성 안에 **strongType** 을 사용하여 Azure Portal 내에서 다중 선택 옵션 목록을 제공할 수 있습니다. **strongType** 은 지원되는 리소스 유형이거나 허용되는 값일 수 있습니다. _리소스 유형_ 이 **strongType** 에 유효한지 여부를 확인하려면 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)를 사용합니다. _리소스 종류_ **strongType** 의 형식은 `<Resource Provider>/<Resource Type>`입니다. 예: `Microsoft.Network/virtualNetworks/subnets`.

**Get-AzResourceProvider** 에서 반환하지 않는 일부 리소스 유형이 지원됩니다. 이러한 형식은 다음과 같습니다.

- `Microsoft.RecoveryServices/vaults/backupPolicies`

**strongType** 의 리소스 유형이 아닌 허용되는 값은 다음과 같습니다.

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>정의 위치

이니셔티브 또는 정책을 만드는 동안 정의 위치를 지정해야 합니다. 정의 위치는 관리 그룹 또는 구독이어야 합니다. 이 위치는 이니셔티브 또는 정책을 할당할 수 있는 범위를 결정합니다. 리소스는 할당 대상으로 지정할 정의 위치의 계층 구조 내의 직접 멤버 또는 자식 멤버여야 합니다.

정의 위치는 다음과 같습니다.

- **구독** - 해당 구독 내의 리소스만 정책 정의에 할당할 수 있습니다.
- **관리 그룹** - 자식 관리 그룹과 자식 구독 내의 리소스만 정책 정의에 할당할 수 있습니다. 정책 정의를 여러 구독에 적용하려는 경우 위치는 각 구독이 포함된 관리 그룹이어야 합니다.

자세한 내용은 [Azure Policy의 범위 이해](./scope.md#definition-location)를 참조하세요.

## <a name="policy-rule"></a>정책 규칙

정책 규칙은 **If** 및 **Then** 블록으로 이루어집니다. **If** 블록에서 정책이 적용되는 시점을 지정하는 하나 이상의 조건을 정의합니다. 이러한 조건에 논리 연산자를 적용하여 정책에 대한 시나리오를 정확하게 정의할 수 있습니다.

**Then** 블록에서 **If** 조건이 충족된 경우에 발생하는 효과를 정의합니다.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>논리 연산자

지원되는 논리 연산자는 다음과 같습니다.

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 구문은 조건의 결과를 반전시킵니다. **allOf** 구문(논리 **And** 작업과 비슷함)은 모든 조건이 true여야 합니다. **anyOf** 구문(논리 **Or** 작업과 비슷함)은 하나 이상의 조건이 true여야 합니다.

논리 연산자를 중첩할 수 있습니다. 다음 예제에서는 **allOf** 작업 내에 중첩된 **not** 작업을 표시합니다.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>조건

조건은 값이 특정 기준을 충족하는지를 평가합니다. 지원되는 조건은 다음과 같습니다.

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

**less**, **lessOrEquals**, **greater**, **greaterOrEquals** 의 경우, 속성 유형이 조건 유형과 일치하지 않으면 오류가 발생합니다. 문자열 비교는 `InvariantCultureIgnoreCase`를 사용하여 수행됩니다.

**like** 및 **notLike** 조건을 사용하는 경우 값에 와일드카드 `*`를 제공합니다. 값에 와일드카드 `*`를 두 개 이상 포함하면 안 됩니다.

**match** 및 **notMatch** 조건을 사용하는 경우 숫자 하나를 일치시키려면 `#`을, 문자 하나를 일치시키려면 `?`를, 임의 문자를 일치시키려면 `.`를, 실제 문자와 일치시키려면 다른 문자를 입력합니다. **match** 와 **notMatch** 는 대/소문자를 구분하지만 _stringValue_ 를 평가하는 다른 모든 조건은 대/소문자를 구분하지 않습니다. 대/소문자를 구분하지 않는 대안은 **matchInsensitively** 및 **notMatchInsensitively** 에서 확인할 수 있습니다.

### <a name="fields"></a>필드

리소스 요청 페이로드의 속성 값이 특정 기준을 충족하는지 여부를 평가하는 조건은 **필드** 식을 사용하여 지정할 수 있습니다. 다음 필드가 지원됩니다.

- `name`
- `fullName`
  - 리소스의 전체 이름을 반환합니다. 리소스의 전체 이름은 리소스 이름에 상위 리소스 이름을 접두어로 추가하여 만듭니다(예: "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - 위치 필드는 다양한 형식을 지원하도록 정규화됩니다. 예를 들어 `East US 2`는 `eastus2`와 동일한 것으로 간주됩니다.
  - 위치에 관계없는 리소스에는 **전역** 을 사용합니다.
- `id`
  - 평가 중인 리소스의 리소스 ID를 반환합니다.
  - 예: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - 리소스에서 사용 가능한 [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md) 형식을 반환합니다.
- `tags`
- `tags['<tagName>']`
  - 이 대괄호 구문은 하이픈, 마침표, 공백 등의 문장 부호가 있는 태그 이름을 지원합니다.
  - 여기서 **\<tagName\>** 은 조건을 validate할 태그 이름입니다.
  - 예: `tags['Acct.CostCenter']`. 여기서 **Acct.CostCenter** 는 태그 이름입니다.
- `tags['''<tagName>''']`
  - 이 대괄호 구문은 이중 아포스트로피로 이스케이프 처리하여 아포스트로피가 있는 태그 이름을 지원합니다.
  - 여기서 **‘\<tagName\>’** 은 조건을 validate할 태그 이름입니다.
  - 예: `tags['''My.Apostrophe.Tag''']`, 여기서 **'My.Apostrophe.Tag'** 는 태그의 이름입니다.
- 속성 별칭 - 목록은 [별칭](#aliases)을 참조하세요.

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` 및 `tags[tag.with.dots]`도 여전히 허용되는 태그 필드 선언 방법입니다. 그러나 기본 식은 위에 나열된 식입니다.

> [!NOTE]
> **\[\*\]alias** 를 참조하는 **필드** 식에서, 배열의 각 요소는 요소 간에 논리적 **and** 를 사용하여 개별적으로 평가됩니다. 자세한 내용은 [배열 리소스 속성 참조](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)를 참조하세요.

#### <a name="use-tags-with-parameters"></a>매개 변수와 함께 태그 사용

매개 변수 값을 태그 필드에 전달할 수 있습니다. 매개 변수를 태그 필드에 전달하면 정책 할당 중에 정책 정의의 유연성이 증가합니다.

다음 예제에서 `concat`는 이름이 **tagName** 매개 변수의 값인 태그에 대한 태그 필드 조회를 만드는 데 사용됩니다. 해당 태그가 없는 경우 **modify** 효과를 사용하여 `resourcegroup()` 조회 함수를 통해 감사된 리소스 부모 리소스 그룹에 설정된 동일한 이름의 태그 값을 사용하는 태그를 추가합니다.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f"
            ]
        }
    }
}
```

### <a name="value"></a>값

**값** 식을 사용하여 값이 특정 조건을 충족하는지 여부를 평가하는 조건을 지정할 수 있습니다. 값은 리터럴, [매개 변수](#parameters)값 또는 [지원되는 템플릿 함수](#policy-functions)의 반환값이 될 수 있습니다.

> [!WARNING]
> _템플릿 함수_ 의 결과가 오류이면 정책 평가가 실패합니다. 실패한 평가는 암시적 **거부** 입니다. 자세한 내용은 [템플릿 오류 방지](#avoiding-template-failures)를 참조하세요. **DoNotEnforce** 의 [enforcementMode](./assignment-structure.md#enforcement-mode)를 사용하여 새 정책 정의를 테스트하고 검증하는 동안 실패한 평가가 새로운 또는 업데이트된 리소스에 미치는 영향을 방지합니다.

#### <a name="value-examples"></a>값 예제

이 정책 규칙 예제는 **value** 를 사용하여 `resourceGroup()` 함수의 결과와 반환된 **name** 속성을 `*netrg`의 **like** 조건과 비교합니다. 규칙은 이름이 `*netrg`로 끝나는 리소스 그룹에서 `Microsoft.Network/*` **type** 이 아닌 리소스를 모두 거부합니다.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

이 정책 규칙 예제에서는 **value** 를 사용하여 여러 중첩 함수의 결과가 **equals** `true`인지 확인합니다. 이 규칙은 최소 3개의 태그가 없는 리소스를 모두 거부합니다.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>템플릿 실패 방지

**value** 에 _template_ 함수를 사용하면 복잡한 중첩 함수를 많이 사용할 수 있습니다. _템플릿 함수_ 의 결과가 오류이면 정책 평가가 실패합니다. 실패한 평가는 암시적 **거부** 입니다. 특정 시나리오에서 실패하는 **value** 의 예는 다음과 같습니다.

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

위의 정책 규칙 예제는 [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring)을 사용하여 **name** 의 처음 세 자를 **abc** 와 비교합니다. **name** 이 세 자 미만이면 `substring()` 함수에서 오류가 발생합니다. 이 오류로 인해 정책은 **deny** 효과가 됩니다.

대신 [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) 함수를 사용하면 **name** 이 세 자 미만인 경우 오류가 발생하지 않으면서 **name** 의 처음 세 자가 **abc** 와 같은지 확인할 수 있습니다.

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

수정한 정책 규칙에서 `if()`는 세 자 미만인 값에서 `substring()`을 구하기 전에 **name** 의 길이를 확인합니다. **name** 이 너무 짧으면 **abc** 와 비교하는 대신 "not starting with abc" 값이 반환됩니다. **abc** 로 시작하지 않는 짧은 이름의 리소스는 여전히 정책 규칙에 실패하지만 평가 중에 더 이상 오류가 발생하지 않습니다.

### <a name="count"></a>개수

배열의 멤버 중 특정 기준을 충족하는 멤버의 수를 세는 조건은 **count** 식을 사용하여 형성할 수 있습니다. 일반적인 시나리오에서는 배열 멤버 '중 하나 이상', '중 정확히 하나', '중 모두' 또는 '중 아무도'라는 조건을 충족하는지 여부를 확인합니다. **Count** 는 조건식에 대해 각 배열 멤버를 평가하고 _true_ 결과의 합계를 낸 다음, 식 연산자와 비교합니다.

#### <a name="field-count"></a>필드 수

요청 페이로드에서 배열의 멤버 중 조건식을 충족하는 멤버의 수를 세는 조건을 계수합니다. **field count** 식의 구조는 다음과 같습니다.

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

**field count** 에 사용되는 속성은 다음과 같습니다.

- **count.field** (필수): 배열의 경로를 포함하며 배열 별칭이어야 합니다.
- **count.where**(선택 사항): `count.field`의 각 [\[\*\]alias](#understanding-the--alias) 배열 멤버를 개별적으로 평가하는 조건식입니다. 속성이 제공되지 않으면 'field' 경로가 있는 모든 배열 멤버는 _true_ 로 평가됩니다. 속성 내에서 모든 [condition](../concepts/definition-structure.md#conditions)을 사용할 수 있습니다.
  [논리 연산자](#logical-operators)는 이 속성 내에서 복잡한 평가 요구 사항을 만드는 데 사용할 수 있습니다.
- **\<condition\>** (필수): 이 값은 **count.where** 조건식을 충족하는 항목 수와 비교됩니다. 숫자 [조건](../concepts/definition-structure.md#conditions)을 사용해야 합니다.

**Field count** 식은 단일 **policyRule** 정의에서 동일한 필드 배열을 최대 3번까지 열거할 수 있습니다.

**field count** 식이 계산되는 방법에 대한 자세한 설명을 포함하여 Azure Policy에서 배열 속성으로 작업하는 방법에 대한 세부 정보는 [배열 리소스 속성 참조](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)를 참조하세요.

#### <a name="value-count"></a>값 개수

배열에서 조건을 충족하는 멤버 수를 계산합니다. 배열은 리터럴 배열이거나 [배열 매개 변수에 대한 참조](#using-a-parameter-value)일 수 있습니다. **value count** 식의 구조는 다음과 같습니다.

```json
{
    "count": {
        "value": "<literal array | array parameter reference>",
        "name": "<index name>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

**value count** 에 사용되는 속성은 다음과 같습니다.

- **count.value**(필수): 평가할 배열입니다.
- **count.name**(필수): 영어 문자와 숫자로 구성된 인덱스 이름입니다. 현재 반복에서 계산된 배열 멤버의 값에 대한 이름을 정의합니다. 이 이름은 `count.where` 조건 내에서 현재 값을 참조하는 데 사용됩니다. **Count** 식이 다른 **count** 식의 자식에 있지 않은 경우 선택 사항입니다. 제공되지 않은 경우 인덱스 이름은 암시적으로 `"default"`로 설정됩니다.
- **count.where**(선택 사항): `count.value`의 각 배열 멤버를 개별적으로 평가하는 조건식입니다. 해당 속성이 제공되지 않으면 모든 배열 멤버는 _true_ 로 평가됩니다. 속성 내에서 모든 [condition](../concepts/definition-structure.md#conditions)을 사용할 수 있습니다. [논리 연산자](#logical-operators)는 이 속성 내에서 복잡한 평가 요구 사항을 만드는 데 사용할 수 있습니다. 현재 열거된 배열 멤버의 값은 [현재](#the-current-function) 함수를 호출하여 액세스할 수 있습니다.
- **\<condition\>** (필수): 이 값은 `count.where` 조건식을 충족하는 항목 수와 비교됩니다. 숫자 [조건](../concepts/definition-structure.md#conditions)을 사용해야 합니다.

다음 한도가 적용됩니다.
- 단일 **policyRule** 정의에는 최대 10개의 **value count** 식을 사용할 수 있습니다.
- 각 **value count** 식은 최대 100회까지 수행할 수 있습니다. 이 수는 부모 **value count** 식에 의해 수행되는 반복 횟수를 포함합니다.

#### <a name="the-current-function"></a>현재 함수

`current()` 함수는 `count.where` 조건 내에서만 사용할 수 있습니다. 이 메서드는 현재 **개수** 식 계산에 의해 열거된 배열 멤버의 값을 반환합니다.

**값 개수 사용량**

- `current(<index name defined in count.name>)`. 예: `current('arrayMember')`
- `current()`. **값 개수** 식이 다른 **count** 식의 자식이 아닌 경우에만 허용됩니다. 위와 동일한 값을 반환합니다.

호출에서 반환된 값이 개체이면 속성 접근자가 지원됩니다. 예: `current('objectArrayMember').property`

**필드 수 사용량**

- `current(<the array alias defined in count.field>)`. 예: `current('Microsoft.Test/resource/enumeratedArray[*]')`.
- `current()`. **field count** 식이 다른 **count** 식의 자식이 아닌 경우에만 허용됩니다. 위와 동일한 값을 반환합니다.
- `current(<alias of a property of the array member>)`. 예: `current('Microsoft.Test/resource/enumeratedArray[*].property')`.

#### <a name="field-count-examples"></a>필드 수 예제

예제 1: 배열이 비어 있는지 확인

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

예제 2: 배열 멤버 중 하나만 조건식을 충족하는지 확인

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

예 3: 배열 멤버 하나 이상이 조건식을 충족하는지 확인

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

예 4: 모든 개체 배열 멤버가 조건식을 충족하는지 확인

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

예제 5: 하나 이상의 배열 멤버가 조건식의 여러 속성과 일치하는지 확인

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

예제 6: `where` 조건 내에서 `current()` 함수를 사용하여 템플릿 함수에서 현재 열거된 배열 멤버의 값에 액세스합니다. 이 조건은 가상 네트워크에 10.0.0.0/24 CIDR 범위에 없는 주소 접두사가 포함되어 있는지 여부를 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
          "equals": false
        }
    },
    "greater": 0
}
```

예제 7: `where` 조건 내에서 `field()` 함수를 사용하여 현재 열거된 배열 멤버의 값에 액세스합니다. 이 조건은 가상 네트워크에 10.0.0.0/24 CIDR 범위에 없는 주소 접두사가 포함되어 있는지 여부를 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', first(field(('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]')))]",
          "equals": false
        }
    },
    "greater": 0
}
```

#### <a name="value-count-examples"></a>값 개수 예

예제 1: 리소스 이름이 지정된 이름 패턴과 일치하는지 확인합니다.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

예제 2: 리소스 이름이 지정된 이름 패턴과 일치하는지 확인합니다. `current()` 함수는 인덱스 이름을 지정하지 않습니다. 결과는 이전 예제와 동일합니다.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

예제 3: 리소스 이름이 배열 매개 변수에서 제공하는 지정된 이름 패턴과 일치하는지 확인합니다.

```json
{
    "count": {
        "value": "[parameters('namePatterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

예 4: 승인된 접두사 목록 아래에 가상 네트워크 주소 접두사가 없는지 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
            "count": {
                "value": "[parameters('approvedPrefixes')]",
                "name": "approvedPrefix",
                "where": {
                    "value": "[ipRangeContains(current('approvedPrefix'), current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
                    "equals": true
                },
            },
            "equals": 0
        }
    },
    "greater": 0
}
```

예 5: 모든 예약된 NSG 규칙이 NSG에 정의되어 있는지 확인합니다. 예약된 NSG 규칙의 속성은 개체를 포함하는 배열 매개 변수에 정의됩니다.

매개 변수 값:

```json
[
    {
        "priority": 101,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 22
    },
    {
        "priority": 102,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 3389
    }
]
```

정책:

```json
{
    "count": {
        "value": "[parameters('reservedNsgRules')]",
        "name": "reservedNsgRule",
        "where": {
            "count": {
                "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
                "where": {
                    "allOf": [
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].priority",
                            "equals": "[current('reservedNsgRule').priority]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                            "equals": "[current('reservedNsgRule').access]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                            "equals": "[current('reservedNsgRule').direction]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                            "equals": "[current('reservedNsgRule').destinationPortRange]"
                        }
                    ]
                }
            },
            "equals": 1
        }
    },
    "equals": "[length(parameters('reservedNsgRules'))]"
}
```

### <a name="effect"></a>영향

Azure Policy는 다음과 같은 유형의 효과를 지원합니다.

- **추가** 는 정의된 필드 집합을 요청에 추가합니다.
- **Audit**: 활동 로그에 경고 이벤트를 생성하지만 요청을 실패하지는 않습니다.
- **AuditIfNotExists**: 관련 리소스가 없으면 활동 로그에 경고 이벤트를 생성합니다.
- **거부**: 활동 로그에 이벤트를 생성하고 요청을 실패합니다.
- **DeployIfNotExists**: 관련 리소스가 아직 없으면 배포합니다.
- **Disabled**: 정책 규칙 준수에 대해 리소스를 평가하지 않습니다.
- **수정**: 리소스 또는 구독에서 정의된 태그를 추가, 업데이트 또는 제거합니다.
- **EnforceOPAConstraint**(사용되지 않음): Azure의 자체 관리형 Kubernetes 클러스터용 Gatekeeper v3로 Open Policy Agent 허용 컨트롤러 구성
- **EnforceRegoPolicy**(사용되지 않음): Azure Kubernetes Service에서 Gatekeeper v2를 사용하여 Open Policy Agent 허용 컨트롤러 구성

각 효과, 평가 순서, 속성 및 예제에 대한 자세한 내용은 [Azure Policy 효과 이해](effects.md)를 참조하세요.

### <a name="policy-functions"></a>정책 함수

함수를 사용하여 정책 규칙에 추가 논리를 도입할 수 있습니다. 정책 정의의 [정책 규칙](#policy-rule) 내에서 그리고 [이니셔티브의 정책 정의에 할당된 매개 변수 값 내에서 확인됩니다.](initiative-definition-structure.md#passing-a-parameter-value-to-a-policy-definition)

다음 함수와 사용자 정의 함수를 제외하고 모든 [Resource Manager 템플릿 함수](../../../azure-resource-manager/templates/template-functions.md)를 정책 규칙 내에서 사용할 수 있습니다.

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> 이러한 함수는 **deployIfNotExists** 정책 정의에서 템플릿 배포의 `details.deployment.properties.template` 부분에 계속 사용할 수 있습니다.

다음 함수는 정책 규칙에서 사용할 수 있지만 Azure Resource Manager 템플릿(ARM 템플릿)에서 사용하는 것과는 다릅니다.

- `utcNow()` - ARM 템플릿과는 달리 _defaultValue_ 외부에서 사용할 수 있습니다.
  - 범용 ISO 8601 DateTime 형식 `yyyy-MM-ddTHH:mm:ss.fffffffZ`의 현재 날짜와 시간으로 설정된 문자열을 반환합니다.

다음 함수는 정책 규칙에서만 사용할 수 있습니다.

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [필수] 문자열 - 범용 ISO 8601 DateTime 형식 'yyyy-MM-ddTHH:mm:ss.FFFFFFFZ'의 문자열
  - **numberOfDaysToAdd**: [필수] 정소 - 추가할 일수

- `field(fieldName)`
  - **fieldName**: [필수] 문자열 - 검색할 [field](#fields)의 이름
  - If 조건에 의해 평가되는 리소스에서 해당 필드의 값을 반환합니다.
  - `field`는 주로 평가 중인 리소스의 필드를 참조하기 위해 **AuditIfNotExists** 및 **DeployIfNotExists** 와 함께 사용합니다. 이 사용 예제는 [DeployIfNotExists 예제](effects.md#deployifnotexists-example)에서 볼 수 있습니다.

- `requestContext().apiVersion`
  - 정책 평가를 트리거한 요청의 API 버전을 반환합니다(예: `2021-09-01`).
    이 값은 리소스 생성/업데이트 평가를 위해 PUT/PATCH 요청에 사용된 API 버전입니다. 기존 리소스에 대한 규정 준수 평가 중에는 항상 최신 API 버전이 사용됩니다.

- `policy()`
  - 평가 중인 정책에 대한 다음 정보를 반환합니다. 반환된 개체(예: `[policy().assignmentId]`)에서 속성에 액세스할 수 있습니다.

    ```json
    {
      "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
      "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
      "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
      "definitionReferenceId": "StorageAccountNetworkACLs"
    }
    ```

- `ipRangeContains(range, targetRange)`
  - **range**: [필수] 문자열 - _targetRange_ 가 범위 내에 있는지 확인할 IP 주소의 범위를 지정하는 문자열입니다.
  - **targetRange**: [필수] 문자열 - _range_ 내에 포함되며 validate할 IP 주소 범위를 지정하는 문자열입니다.
  - _range_ IP 주소 범위에 _targetRange_ IP 주소 범위가 포함되어 있는지 여부에 대한 _boolean_ 을 반환합니다. 빈 범위 또는 IP 제품군 간 혼합은 허용되지 않으며 평가 오류가 발생합니다.

  지원되는 형식:
  - 단일 IP 주소 (예: `10.0.0.0`, `2001:0DB8::3:FFFE`)
  - CIDR 범위 (예: `10.0.0.0/24`, `2001:0DB8::/110`)
  - 시작 및 끝 IP 주소에 의해 정의된 범위(예: `192.168.0.1-192.168.0.9`, `2001:0DB8::-2001:0DB8::3:FFFF`)

- `current(indexName)`
  - [Count expressions](#count) 내에서만 사용할 수 있는 특수 함수입니다.

#### <a name="policy-function-example"></a>정책 함수 예제

이 정책 규칙 예제에서는 `resourceGroup` 리소스 함수를 `concat` 배열 및 개체 함수와 함께 사용하여 **name** 속성을 가져오고 리소스 이름을 리소스 그룹 이름으로 시작하도록 하는 `like` 조건을 작성합니다.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>별칭

리소스 유형에 대한 특정 속성에 액세스하려면 속성 별칭을 사용합니다. 별칭을 사용하면 리소스의 속성에 허용되는 값이나 조건을 제한할 수 있습니다. 각 별칭은 주어진 리소스 유형에 대해 서로 다른 API 버전의 경로에 매핑됩니다. 정책 평가 중에 정책 엔진은 해당 API 버전에 대한 속성 경로를 가져옵니다.

별칭의 목록은 항상 업데이트됩니다. 현재 Azure Policy에서 지원하는 별칭을 찾으려면 다음 방법 중 하나를 사용합니다.

- Visual Studio Code용 Azure Policy 확장(권장)

  [Visual Studio Code용 Azure Policy 확장](../how-to/extension-for-vscode.md)을 사용하여 리소스 속성에 대한 별칭을 보고 검색할 수 있습니다.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="별칭 이름을 표시하는 속성을 맴도는 Visual Studio Code에 대한 Azure Policy 확장의 스크린샷" border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > [수정](./effects.md#modify) 효과와 함께 사용할 수 있는 별칭을 찾으려면 Azure PowerShell **4.6.0** 이상에서 다음 명령을 사용합니다.
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>[*] 별칭 이해

사용 가능한 별칭 중 일부에 ‘normal’ 이름으로 표시되는 버전과 **\[\*\]** 가 추가된 다른 버전이 있습니다. 다음은 그 예입니다.

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

'normal' 별칭은 필드를 단일 값으로 나타냅니다. 이 필드는 전체 값 세트가 정의한 것과 정확히 같아야 하는(초과 또는 미만 없이) 정확한 일치 비교 시나리오를 위한 것입니다.

**\[\*\]** 별칭은 배열 리소스 속성의 요소에서 선택한 값의 컬렉션을 나타냅니다. 예를 들면 다음과 같습니다.

| Alias | 선택한 값 |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `ipRules` 배열 요소입니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `ipRules` 배열의 각 요소에 대한 `action` 속성 값입니다. |

[필드](#fields) 조건에서 사용하는 경우 배열 별칭을 사용하여 각 개별 배열 요소를 대상 값과 비교할 수 있습니다. [개수](#count) 식과 함께 사용하는 경우 다음을 수행할 수 있습니다.

- 배열의 크기 확인
- 배열 요소의 전부나 일부가 복잡한 조건을 충족하는지 확인
- 정확히 ***n*** 개의 배열 요소가 복잡한 조건을 충족하는지 확인

자세한 내용 및 예제는 [배열 리소스 속성 참조](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [이니셔티브 정의 구조](./initiative-definition-structure.md)를 참조합니다.
- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](../how-to/programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
