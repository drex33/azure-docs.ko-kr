---
title: 효과 작동 방식 이해
description: Azure Policy 정의는 규정 준수가 관리되고 보고되는 방법을 결정하는 다양한 효과가 있습니다.
ms.date: 09/01/2021
ms.topic: conceptual
ms.openlocfilehash: bbcdce83fad513c85ab45f4c38c936b345828ef3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449681"
---
# <a name="understand-azure-policy-effects"></a>Azure Policy의 영향 파악

Azure Policy의 각 정책 정의는 단일 효과가 있습니다. 해당 효과는 정책 규칙이 일치되는 것으로 평가될 때 어떻게 되는지 결정합니다. 효과는 새 리소스, 업데이트된 리소스 또는 기존 리소스인 경우 서로 다르게 동작합니다.

정책 정의에는 현재 다음과 같은 효과가 지원됩니다.

- [Append](#append)
- [감사](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [거부](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [사용 안 함](#disabled)
- [수정](#modify)

다음 효과는 _사용되지 않습니다_.

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> 리소스 공급자 모드 `Microsoft.Kubernetes.Data`에서는 **EnforceOPAConstraint** 또는 **EnforceRegoPolicy** 효과 대신 _audit_ 및 _deny_ 를 사용합니다. 기본 제공 정책 정의가 업데이트되었습니다. 이러한 기본 제공 정책 정의의 기존 정책 할당이 수정되면 _effect_ 매개 변수를 업데이트된 _allowedValues_ 목록의 값으로 변경해야 합니다.

## <a name="order-of-evaluation"></a>계산 순서

리소스를 만들거나 업데이트하는 요청은 먼저 Azure Policy에서 평가됩니다. Azure Policy는 리소스에 적용한 다음, 각 정의에 대해 리소스를 평가하는 모든 할당 목록을 만듭니다. [Resource Manager 모드](./definition-structure.md#resource-manager-modes)에서 Azure Policy는 적절한 리소스 공급자에 요청을 전달하기 전에 다양한 효과를 처리합니다. 따라서 리소스가 Azure Policy의 설계된 거버넌스 컨트롤을 충족하지 않을 때 리소스 공급자의 불필요한 처리를 방지할 수 있습니다. [리소스 공급자 모드](./definition-structure.md#resource-provider-modes)에서 리소스 공급자는 평가 및 결과를 관리하고 그 결과를 다시 Azure Policy에 보고합니다.

- **사용 안 함** 을 먼저 선택하여 정책 규칙을 평가할지 여부를 확인합니다.
- 그런 다음, **Append** 및 **Modify** 가 평가됩니다. 어느 쪽이든 요청을 변경할 수 있기 때문에 변경하면 audit 또는 deny 효과가 트리거되지 않을 수 있습니다. 이들 효과는 Resource Manager 모드에서만 사용할 수 있습니다.
- 그런 다음, **거부** 가 평가됩니다. 감사 전에 거부를 평가하여 원치 않는 리소스의 이중 로깅이 방지됩니다.
- **audit** 이 마지막으로 평가됩니다.

Resource Manager 모드 요청에 대해 리소스 공급자가 성공 코드를 반환하면, **AuditIfNotExists** 및 **DeployIfNotExists** 가 추가 규정 준수 로깅 또는 작업이 필요한지 여부를 확인하기 위해 평가를 수행합니다.

또한 `tags` 관련 필드만 수정하는 `PATCH` 요청은 `tags` 관련 필드를 검사하는 조건이 포함된 정책으로 정책 평가를 제한합니다.

## <a name="append"></a>추가

만들기 또는 업데이트 중에 요청된 리소스에 추가 필드를 추가하는 데 추가가 사용됩니다. 일반적인 예는 스토리지 리소스에 허용된 IP를 지정하는 것입니다.

> [!IMPORTANT]
> Append는 태그가 아닌 속성에 사용할 수 있습니다. Append는 생성 또는 업데이트 요청 중에 리소스에 태그를 추가할 수 있으므로, 태그에 [Modify](#modify) 효과를 대신 사용하는 것이 좋습니다.

### <a name="append-evaluation"></a>추가 평가

추가는 리소스의 생성 또는 업데이트하는 도중 리소스 공급 기업이 요청을 처리하기 전에 평가합니다. 추가는 정책 규칙의 **if** 조건이 충족되는 경우 리소스에 필드를 추가합니다. 추가 효과가 원래 요청의 값을 다른 값으로 재정의하는 경우 거부 효과로서 역할을 하고 해당 요청을 거부합니다. 새 값을 기존 배열에 추가하려면 **\[\*\]** 버전의 별칭을 사용합니다.

추가 효과를 사용하는 정책 정의가 평가 주기의 일부로 실행되는 경우 이미 존재하는 리소스를 변경하지 않습니다. 대신 비호환으로 **if** 조건을 충족하는 모든 리소스를 표시합니다.

### <a name="append-properties"></a>추가 속성

추가 효과는 필수인 **details** 배열만을 갖습니다. **details** 는 배열이므로 단일 **필드/값** 쌍 또는 여러 개를 사용할 수 있습니다. 허용 가능한 필드 목록은 [정의 구조](definition-structure.md#fields)를 참조하세요.

### <a name="append-examples"></a>추가 예제

예제 1: 스토리지 계정에 IP 규칙을 설정하는 **value** 배열이 포함된 **\[\*\]** 가 아닌 
[alias](definition-structure.md#aliases)를 사용하는 단일 **field/value** 쌍입니다. **\[\*\]** 가 아닌 별칭이 배열이면 효과는 **value** 를 전체 배열로 추가합니다. 배열이 이미 있으면 충돌로 인해 거부 이벤트가 발생합니다.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

예제 2: 스토리지 계정에 IP 규칙을 설정하는 **value** 배열이 포함된 **\[\*\]** [별칭](definition-structure.md#aliases)을 사용하는 단일 **field/value** 쌍입니다. **\[\*\]** 별칭을 사용하면 이 효과는 기존 배열(있는 경우)에 **value** 를 추가합니다. 배열이 아직 없으면 생성됩니다.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="audit"></a>감사

감사는 비준수 리소스를 평가하는 경우 활동 로그에서 경고 이벤트를 만드는 데 사용되지만 요청을 중단하지는 않습니다.

### <a name="audit-evaluation"></a>감사 평가

Audit는 리소스의 만들기 또는 업데이트하는 중에 Azure Policy에서 확인된 마지막 효과입니다. Resource Manager 모드에서 Azure Policy는 그런 다음, 리소스 공급자에 리소스를 보냅니다. 리소스에 대한 만들기 또는 업데이트 요청을 평가할 때 Azure Policy는 `Microsoft.Authorization/policies/audit/action` 작업을 활동 로그에 추가하고 해당 리소스를 비준수로 표시합니다. 표준 규정 준수 평가 주기 동안에는 리소스의 준수 상태만 업데이트됩니다.

### <a name="audit-properties"></a>감사 속성

Resource Manager 모드에서는 audit 효과에 정책 정의의 **then** 조건에 사용할 수 있는 추가 속성이 없습니다.

리소스 공급자 모드 `Microsoft.Kubernetes.Data`에서는 audit 효과에 다음과 같은 **details** 하위 속성이 추가로 있습니다. 은 더 이상 사용되지 않은 `templateInfo` 새 정책 정의 또는 업데이트된 정책 정의에 `constraintTemplate` 필요합니다.

- **templateInfo(필수)**
  - `constraintTemplate`와 함께 사용할 수 없습니다.
  - **sourceType(필수)**
    - 제약 조건 템플릿의 소스 형식을 정의합니다. 허용되는 값: _PublicURL_ 또는 _Base64인코딩된_.
    - _PublicURL인_ 경우 `url` 속성과 쌍을 이루어 제약 조건 템플릿의 위치를 제공합니다. 위치에 공개적으로 액세스할 수 있어야 합니다.

      > [!WARNING]
      > 에서 SAS URI 또는 `url` 토큰을 사용하거나 비밀을 노출할 수 있는 다른 토큰을 사용하지 마세요.

    - _Base64Encoded인_ 경우 속성과 `content` 쌍을 이루어 base 64로 인코딩된 제약 조건 템플릿을 제공합니다. 기존 OPA(Open [Policy Agent)](https://www.openpolicyagent.org/) GateKeeper v3 제약 조건 템플릿에서 사용자 지정 정의를 만들려면 제약 조건 [템플릿에서](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates)정책 [정의 만들기를](../how-to/extension-for-vscode.md) 참조하세요.
- **제약 조건(선택** 사항)
  - `templateInfo`와 함께 사용할 수 없습니다.
  - 제약 조건 템플릿의 CRD 구현입니다. `{{ .Values.<valuename> }}`을 사용하여 **values** 를 통해 전달되는 매개 변수를 사용합니다. 아래 예제 2에서 이들 값은 `{{ .Values.excludedNamespaces }}` 및 `{{ .Values.allowedContainerImagesRegex }}`입니다.
- **네임스페이스(선택** 사항)
  - 정책 평가를 제한할 [Kubernetes 네임스페이스의](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) _배열입니다._
  - 값이 비어 있거나 누락된 경우 정책 평가에 excludedNamespaces 에 정의된 네임스페이스를 제외한 모든 _네임스페이스가 포함됩니다._
- **excludedNamespaces(필수)**
  - 정책 평가에서 제외할 [Kubernetes 네임스페이스의](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) _배열입니다._
- **labelSelector(필수)**
  - 제공된 레이블 및 [선택기와](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)일치하는 정책 평가에 포함할 Kubernetes 리소스를 지정할 수 있도록 _matchLabels(개체)_ 및 _matchExpression(배열)_ 속성을 포함하는 _개체입니다._
  - 값이 비어 있거나 누락된 경우 정책 평가에 _excludedNamespaces_ 에 정의된 네임스페이스를 제외한 모든 레이블 및 선택기가 포함됩니다.
- **apiGroups(templateInfo를** 사용하는 경우 필수) 
  - 일치시킬 [API 그룹이](https://kubernetes.io/docs/reference/using-api/#api-groups) 포함된 _배열입니다._ 빈 배열( `[""]` )은 핵심 API 그룹이지만 `["*"]` 모든 API 그룹과 일치합니다.
-  _kinds(templateInfo를_ 사용하는 경우 필수)
  - 평가를 제한할 Kubernetes 개체의 [종류를](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#required-fields) 포함하는 _배열입니다._
- **values**(선택 사항)
  - 제약 조건에 전달할 매개 변수 및 값을 정의합니다. 각 값은 제약 조건 템플릿 CRD에 있어야 합니다.
- **constraintTemplate(사용되지** 않습니다.)
  - `templateInfo`와 함께 사용할 수 없습니다.
  - `templateInfo`정책 정의를 만들거나 업데이트할 때는 로 대체해야 합니다.
  - 새로운 제약 조건을 정의하는 제약 조건 템플릿 CRD(CustomResourceDefinition)입니다. 이 템플릿은 Azure 정책의 **values** 를 통해 전달되는 Rego 논리, 제약 조건 스키마 및 제약 조건 매개 변수를 정의합니다.

### <a name="audit-example"></a>감사 예제

예제 1: Resource Manager 모드에 audit 효과 사용.

```json
"then": {
    "effect": "audit"
}
```

예제 2: 리소스 공급자 모드 `Microsoft.Kubernetes.Data`에 audit 효과 사용. **details.templateInfo의** 추가 정보는 _PublicURL_ 사용을 선언하고 를 `url` Kubernetes에서 사용할 제약 조건 템플릿의 위치로 설정하여 허용되는 컨테이너 이미지를 제한합니다.

```json
"then": {
    "effect": "audit",
    "details": {
        "templateInfo": {
            "sourceType": "PublicURL",
            "url": "https://store.policy.core.windows.net/kubernetes/container-allowed-images/v1/template.yaml",
        },
        "values": {
            "imageRegex": "[parameters('allowedContainerImagesRegex')]"
        },
        "apiGroups": [""],
        "kinds": ["Pod"]
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists를 사용하면 **if** 조건과 일치하지만 **then** 조건의 **details** 에 지정된 속성을 포함하지 않는 리소스와 _관련된_ 리소스를 감사할 수 있습니다.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 평가

리소스 공급 기업이 리소스 만들기 또는 업데이트 요청을 처리하고 성공 상태 코드를 반환한 후 AuditIfNotExists가 실행됩니다. 관련된 리소스가 없거나 **ExistenceCondition** 에서 정의된 리소스가 true로 평가되지 않는 경우 감사가 발생합니다. Azure Policy는 새 리소스와 업데이트된 리소스에 대해 `Microsoft.Authorization/policies/audit/action` 작업을 활동 로그에 추가하고 리소스를 비준수로 표시합니다. 트리거되는 경우 **if** 조건을 충족하는 리소스는 비호환으로 표시되는 리소스입니다.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 속성

AuditIfNotExists 효과의 **details** 속성에는 일치하는 관련된 리소스를 정의하는 모든 하위 속성이 있습니다.

- **Type**(필수)
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - **details.type** 이 **if** 조건 리소스 아래에 있는 리소스 유형이면 정책은 평가된 리소스 범위 내에서 **type** 의 리소스를 쿼리합니다. 그렇지 않으면, 평가된 리소스와 동일한 리소스 그룹 내에서 정책 쿼리가 수행됩니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
  - **if.field.type** 과 **then.details.type** 의 조건 값이 일치하면 **Name** 은 _required_ 가 되고 `[field('name')]`이 되거나 하위 리소스의 경우 `[field('fullName')]`이어야 합니다.
    하지만 [audit](#audit) 효과를 대신 고려해야 합니다.
- **ResourceGroupName**(옵션)
  - 다른 리소스 그룹에서 오는 관련된 리소스의 일치를 허용합니다.
  - **type** 이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - 기본값은 리소스의 리소스 그룹인 **if** 조건입니다.
- **ExistenceScope**(옵션)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_ 입니다.
  - 일치하는 관련된 리소스를 인출할 범위를 설정합니다.
  - **type** 이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - _ResourceGroup_ 의 경우 **ResourceGroupName** 에서 지정된 **if** 조건 리소스의 리소스 그룹 또는 리소스 그룹으로 제한합니다.
  - _Subscription_ 의 경우 관련된 리소스에 대한 전체 구독을 쿼리합니다. 적절한 평가를 위해서는 할당 범위를 구독 이상으로 설정해야 합니다. 
  - 기본값은 _ResourceGroup_ 입니다.
- **EvaluationDelay**(선택 사항)
  - 관련 리소스의 존재를 평가해야 하는 시기를 지정합니다. 지연은 리소스 만들기 또는 업데이트 요청의 결과인 평가에만 사용됩니다.
  - 허용되는 값은 `AfterProvisioning`, `AfterProvisioningSuccess`, `AfterProvisioningFailure` 또는 0분에서 360분 사이의 ISO 8601 기간입니다.
  - _AfterProvisioning_ 값은 정책 규칙의 IF 조건에서 평가된 리소스의 프로비저닝 결과를 검사합니다. `AfterProvisioning`은 결과에 관계없이 프로비저닝이 완료된 후 실행됩니다. 프로비저닝이 6시간보다 오래 걸리는 경우 _AfterProvisioning_ 평가 지연을 결정할 때 실패로 처리됩니다.
  - 기본값은 `PT10M`(10분)입니다.
  - 긴 평가 지연을 지정하면 기록된 리소스의 규정 준수 상태가 다음 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)까지 업데이트되지 않을 수 있습니다.
- **ExistenceCondition**(옵션)
  - 지정되지 않은 경우 **type** 의 모든 관련 리소스는 효과를 충족하고 감사를 트리거하지 않습니다.
  - **if** 조건에 대해 정책 규칙과 동일한 언어를 사용하지만 관련된 각 리소스에 대해 개별적으로 평가됩니다.
  - 일치하는 관련 리소스가 true로 평가되는 경우 효과가 충족되고 감사를 트리거하지 않습니다.
  - [field()]를 사용하여 **if** 조건의 값과 같은지 확인할 수 있습니다.
  - 예를 들어 부모 리소스(**if** 조건에서)가 일치하는 관련된 리소스와 동일한 리소스 위치에 있는지 확인하는 데 사용될 수 있습니다.

### <a name="auditifnotexists-example"></a>AuditIfNotExists 예제

예: 가상 머신을 평가하여 맬웨어 방지 확장이 있는지 여부를 확인한 후, 누락된 경우 감사를 수행합니다.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>거부

거부는 정책 정의를 통해 정의된 표준과 일치하지 않고 요청에 실패하는 리소스 요청을 방지하는 데 사용됩니다.

### <a name="deny-evaluation"></a>거부 평가

Resource Manager 모드에서 일치된 리소스를 만들거나 업데이트할 때 deny는 리소스 공급자에게 보내기 전에 먼저 요청을 방지합니다. 요청은 `403 (Forbidden)`으로 반환됩니다. 포털에서 금지됨은 정책 할당에서 차단된 배포에 대한 상태로 확인할 수 있습니다. 리소스 공급자 모드에서는 리소스 공급자가 리소스의 평가를 관리합니다.

기존 리소스의 평가 중 거부 정책 정의와 일치하는 리소스는 비준수로 표시됩니다.

### <a name="deny-properties"></a>거부 속성

Resource Manager 모드에서는 deny 효과에 정책 정의의 **then** 조건에 사용할 수 있는 추가 속성이 없습니다.

리소스 공급자 모드 `Microsoft.Kubernetes.Data`에서는 deny 효과에 다음과 같은 **details** 하위 속성이 추가로 있습니다. 은 더 이상 사용되지 않은 `templateInfo` 새 정책 정의 또는 업데이트된 정책 정의에 `constraintTemplate` 필요합니다.

- **templateInfo(필수)**
  - `constraintTemplate`와 함께 사용할 수 없습니다.
  - **sourceType(필수)**
    - 제약 조건 템플릿의 소스 형식을 정의합니다. 허용되는 값: _PublicURL_ 또는 _Base64인코딩된_.
    - _PublicURL인_ 경우 `url` 속성과 쌍을 이루어 제약 조건 템플릿의 위치를 제공합니다. 위치에 공개적으로 액세스할 수 있어야 합니다.

      > [!WARNING]
      > 에서 SAS URI 또는 `url` 토큰을 사용하거나 비밀을 노출할 수 있는 다른 토큰을 사용하지 마세요.

    - _Base64Encoded인_ 경우 속성과 `content` 쌍을 이루어 base 64로 인코딩된 제약 조건 템플릿을 제공합니다. 기존 OPA(Open [Policy Agent)](https://www.openpolicyagent.org/) GateKeeper v3 제약 조건 템플릿에서 사용자 지정 정의를 만들려면 제약 조건 [템플릿에서](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates)정책 [정의 만들기를](../how-to/extension-for-vscode.md) 참조하세요.
- **제약 조건(선택** 사항)
  - `templateInfo`와 함께 사용할 수 없습니다.
  - 제약 조건 템플릿의 CRD 구현입니다. `{{ .Values.<valuename> }}`을 사용하여 **values** 를 통해 전달되는 매개 변수를 사용합니다. 아래 예제 2에서 이들 값은 `{{ .Values.excludedNamespaces }}` 및 `{{ .Values.allowedContainerImagesRegex }}`입니다.
- **네임스페이스(선택** 사항)
  - 정책 평가를 제한할 [Kubernetes 네임스페이스의](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) _배열입니다._
  - 값이 비어 있거나 누락된 경우 정책 평가에 excludedNamespaces 에 정의된 네임스페이스를 제외한 모든 _네임스페이스가 포함됩니다._
- **excludedNamespaces(필수)**
  - 정책 평가에서 제외할 [Kubernetes 네임스페이스의](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) _배열입니다._
- **labelSelector(필수)**
  - 제공된 레이블 및 [선택기와](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)일치하는 정책 평가에 포함할 Kubernetes 리소스를 지정할 수 있도록 _matchLabels(개체)_ 및 _matchExpression(배열)_ 속성을 포함하는 _개체입니다._
  - 값이 비어 있거나 누락된 경우 정책 평가에 _excludedNamespaces_ 에 정의된 네임스페이스를 제외한 모든 레이블 및 선택기가 포함됩니다.
- **apiGroups(templateInfo를** 사용하는 경우 필수) 
  - 일치시킬 [API 그룹이](https://kubernetes.io/docs/reference/using-api/#api-groups) 포함된 _배열입니다._ 빈 배열( `[""]` )은 핵심 API 그룹이지만 `["*"]` 모든 API 그룹과 일치합니다.
-  _kinds(templateInfo를_ 사용하는 경우 필수)
  - 평가를 제한할 Kubernetes 개체의 [종류를](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#required-fields) 포함하는 _배열입니다._
- **values**(선택 사항)
  - 제약 조건에 전달할 매개 변수 및 값을 정의합니다. 각 값은 제약 조건 템플릿 CRD에 있어야 합니다.
- **constraintTemplate(사용되지** 않습니다.)
  - `templateInfo`와 함께 사용할 수 없습니다.
  - `templateInfo`정책 정의를 만들거나 업데이트할 때로 바꾸어야 합니다.
  - 새로운 제약 조건을 정의하는 제약 조건 템플릿 CRD(CustomResourceDefinition)입니다. 이 템플릿은 Azure 정책의 **values** 를 통해 전달되는 Rego 논리, 제약 조건 스키마 및 제약 조건 매개 변수를 정의합니다. 새를 사용 하 여 대체 하는 것이 좋습니다 `templateInfo` `constraintTemplate` .

### <a name="deny-example"></a>거부 예제

예제 1: Resource Manager 모드에 deny 효과 사용.

```json
"then": {
    "effect": "deny"
}
```

예제 2: 리소스 공급자 모드 `Microsoft.Kubernetes.Data`에 deny 효과 사용. **Details** 정보의 추가 정보는 _publicurl_ 사용을 선언 하 고 `url` Kubernetes에서 허용 되는 컨테이너 이미지를 제한 하는 데 사용할 제약 조건 템플릿의 위치로 설정 합니다.

```json
"then": {
    "effect": "deny",
    "details": {
        "templateInfo": {
            "sourceType": "PublicURL",
            "url": "https://store.policy.core.windows.net/kubernetes/container-allowed-images/v1/template.yaml",
        },
        "values": {
            "imageRegex": "[parameters('allowedContainerImagesRegex')]"
        },
        "apiGroups": [""],
        "kinds": ["Pod"]
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

AuditIfNotExists와 마찬가지로 DeployIfNotExists 정책 정의는 조건이 충족하는 경우 템플릿 배포를 실행합니다.

> [!NOTE]
> **deployIfNotExists** 에서는 [중첩 템플릿](../../../azure-resource-manager/templates/linked-templates.md#nested-template)은 지원되지만 [연결된 템플릿](../../../azure-resource-manager/templates/linked-templates.md#linked-template)은 현재 지원되지 않습니다.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 평가

DeployIfNotExists는 리소스 공급자가 구독 또는 리소스 요청 만들기 또는 업데이트를 처리하고 성공 상태 코드를 반환한 경우 구성 가능한 지연 후에 실행됩니다. 관련 리소스가 없거나 **ExistenceCondition** 에서 정의된 리소스가 true로 평가되지 않는 경우 템플릿 배포가 발생합니다. 배포 기간은 템플릿에 포함된 리소스의 복잡성에 따라 달라집니다.

평가 주기 중 리소스와 일치하는 DeployIfNotExists 효과가 포함된 정책 정의는 비준수로 표시되지만 해당 리소스에서 작업이 수행되지 않습니다. 기존 비준수 리소스는 [수정 작업](../how-to/remediate-resources.md)을 통해 수정할 수 있습니다.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 속성

DeployIfNotExists 효과의 **details** 속성에는 일치하는 관련된 리소스를 정의하는 모든 하위 속성 및 실행할 템플릿 배포가 있습니다.

- **Type**(필수)
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - **if** 조건 리소스 아래의 리소스를 인출하려는 시도로 시작한 다음, **if** 리소스 조건와 동일한 리소스 그룹 내에서 쿼리합니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
  - **if.field.type** 과 **then.details.type** 의 조건 값이 일치하면 **Name** 은 _required_ 가 되고 `[field('name')]`이 되거나 하위 리소스의 경우 `[field('fullName')]`이어야 합니다.
- **ResourceGroupName**(옵션)
  - 다른 리소스 그룹에서 오는 관련된 리소스의 일치를 허용합니다.
  - **type** 이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - 기본값은 리소스의 리소스 그룹인 **if** 조건입니다.
  - 템플릿 배포가 실행되는 경우 이 값의 리소스 그룹에 배포됩니다.
- **ExistenceScope**(옵션)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_ 입니다.
  - 일치하는 관련된 리소스를 인출할 범위를 설정합니다.
  - **type** 이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - _ResourceGroup_ 의 경우 **ResourceGroupName** 에서 지정된 **if** 조건 리소스의 리소스 그룹 또는 리소스 그룹으로 제한합니다.
  - _Subscription_ 의 경우 관련된 리소스에 대한 전체 구독을 쿼리합니다. 적절 한 평가를 위해 구독 이상에서 할당 범위를 설정 해야 합니다. 
  - 기본값은 _ResourceGroup_ 입니다.
- **EvaluationDelay**(선택 사항)
  - 관련 리소스의 존재를 평가해야 하는 시기를 지정합니다. 지연은 리소스 만들기 또는 업데이트 요청의 결과인 평가에만 사용됩니다.
  - 허용되는 값은 `AfterProvisioning`, `AfterProvisioningSuccess`, `AfterProvisioningFailure` 또는 0분에서 360분 사이의 ISO 8601 기간입니다.
  - _AfterProvisioning_ 값은 정책 규칙의 IF 조건에서 평가된 리소스의 프로비저닝 결과를 검사합니다. `AfterProvisioning`은 결과에 관계없이 프로비저닝이 완료된 후 실행됩니다. 프로비저닝이 6시간보다 오래 걸리는 경우 _AfterProvisioning_ 평가 지연을 결정할 때 실패로 처리됩니다.
  - 기본값은 `PT10M`(10분)입니다.
  - 긴 평가 지연을 지정하면 기록된 리소스의 규정 준수 상태가 다음 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)까지 업데이트되지 않을 수 있습니다.
- **ExistenceCondition**(옵션)
  - 지정되지 않은 경우 **type** 의 모든 관련 리소스는 효과를 충족하고 배포를 트리거하지 않습니다.
  - **if** 조건에 대해 정책 규칙과 동일한 언어를 사용하지만 관련된 각 리소스에 대해 개별적으로 평가됩니다.
  - 일치하는 모든 관련 리소스가 true로 평가되는 경우 효과가 충족되고 배포를 트리거하지 않습니다.
  - [field()]를 사용하여 **if** 조건의 값과 같은지 확인할 수 있습니다.
  - 예를 들어 부모 리소스(**if** 조건에서)가 일치하는 관련된 리소스와 동일한 리소스 위치에 있는지 확인하는 데 사용될 수 있습니다.
- **roleDefinitionIds**(필수)
  - 이 속성은 구독에서 액세스할 수 있는 역할 기반 액세스 제어 역할 ID와 일치하는 문자열 배열을 포함해야 합니다. 자세한 내용은 [수정 - 정책 정의 구성](../how-to/remediate-resources.md#configure-policy-definition)을 참조하세요.
- **DeploymentScope**(선택 사항)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_ 입니다.
  - 트리거될 배포 유형을 설정합니다. _Subscription_ 은 [구독 수준의 배포](../../../azure-resource-manager/templates/deploy-to-subscription.md)를 나타내며, _ResourceGroup_ 은 리소스 그룹에 대한 배포를 나타냅니다.
  - 구독 수준 배포를 사용하는 경우 _location_ 속성을 _Deployment_ 에 지정해야 합니다.
  - 기본값은 _ResourceGroup_ 입니다.
- **Deployment**(필수)
  - 이 속성은 `Microsoft.Resources/deployments` PUT API로 전달되므로 전체 템플릿 배포를 포함해야 합니다. 자세한 내용은 [배포 REST API](/rest/api/resources/deployments)를 참조하세요.
  - 템플릿 내의 중첩된 `Microsoft.Resources/deployments`는 여러 정책 평가 간의 경합을 피하기 위해 고유한 이름을 사용해야 합니다. 상위 배포의 이름은 `[concat('NestedDeploymentName-', uniqueString(deployment().name))]`을 통해 중첩된 배포 이름의 일부로 사용할 수 있습니다.

  > [!NOTE]
  > **배포** 속성 내부의 모든 함수는 정책이 아닌 템플릿의 구성 요소로 평가됩니다. 예외는 정책에서 템플릿으로 값을 전달하는 **parameters** 속성입니다. 템플릿 매개 변수 이름 아래에 있는 이 섹션의 **값** 은 이 값의 전달을 수행하는 데 사용됩니다(DeployIfNotExists 예제의 _fullDbName_ 참조).

### <a name="deployifnotexists-example"></a>DeployIfNotExists 예제

예: SQL Server 데이터베이스를 평가하여 transparentDataEncryption이 사용되는지 여부를 확인합니다.
그렇지 않은 경우 활성화하는 배포가 실행됩니다.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "evaluationDelay": "AfterProvisioning",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>사용 안 함

이 효과는 테스트 상황 또는 정책 정의가 효과를 매개 변수화한 경우에 유용합니다. 이러한 유연성을 사용하면 해당 정책의 모든 할당을 사용하지 않도록 설정하는 대신 단일 할당을 사용하지 않도록 설정할 수 있습니다.

Disabled 효과의 대안은 **enforcementMode** 이며, 정책 할당에서 설정됩니다.
**enforcementMode** 가 _Disabled_ 이면 리소스는 계속 평가됩니다. 활동 로그와 같은 로깅 및 정책 효과는 발생하지 않습니다. 자세한 내용은 [정책 할당 - 적용 모드](./assignment-structure.md#enforcement-mode)를 참조하세요.

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

이 효과는 `Microsoft.Kubernetes.Data`의 정책 정의 _mode_ 와 함께 사용됩니다. OPA([Open Policy Agent](https://www.openpolicyagent.org/))에 대한 [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework)로 정의된 Gatekeeper v3 허용 제어 규칙을 Azure의 Kubernetes 클러스터로 전달하는 데 사용됩니다.

> [!IMPORTANT]
> **EnforceOPAConstraint** 효과 및 관련 **Kubernetes 서비스** 범주가 포함된 제한된 미리 보기 정책 정의는 _더 이상 사용되지 않습니다_. 대신 `Microsoft.Kubernetes.Data` 리소스 공급자 모드를 통해 _감사_ 및 _거부_ 효과를 사용합니다.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint 평가

Open Policy Agent 승인 컨트롤러는 클러스터에 대한 새로운 요청을 실시간으로 평가합니다.
15분마다 클러스터에 대한 전체 검사가 완료되고 Azure Policy에 결과가 보고됩니다.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint 속성

EnforceOPAConstraint 효과의 **details** 속성에는 Gatekeeper v3 허용 제어 규칙을 설명하는 하위 속성이 있습니다.

- **constraintTemplate**(필수)
  - 새로운 제약 조건을 정의하는 제약 조건 템플릿 CRD(CustomResourceDefinition)입니다. 이 템플릿은 Azure 정책의 **values** 를 통해 전달되는 Rego 논리, 제약 조건 스키마 및 제약 조건 매개 변수를 정의합니다.
- **constraint**(필수)
  - 제약 조건 템플릿의 CRD 구현입니다. `{{ .Values.<valuename> }}`을 사용하여 **values** 를 통해 전달되는 매개 변수를 사용합니다. 다음 예에서는 이들 값이 `{{ .Values.cpuLimit }}`과 `{{ .Values.memoryLimit }}`입니다.
- **values**(선택 사항)
  - 제약 조건에 전달할 매개 변수 및 값을 정의합니다. 각 값은 제약 조건 템플릿 CRD에 있어야 합니다.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint 예제

예제: Kubernetes에서 컨테이너 CPU 및 메모리 리소스 제한을 설정하는 Gatekeeper v3 허용 제어 규칙입니다.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

이 효과는 `Microsoft.ContainerService.Data`의 정책 정의 _mode_ 와 함께 사용됩니다. [Azure Kubernetes Service](../../../aks/intro-kubernetes.md)에서 OPA([Open Policy Agent](https://www.openpolicyagent.org/))에 대한 [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)로 정의된 Gatekeeper v2 허용 제어 규칙을 전달하는 데 사용됩니다.

> [!IMPORTANT]
> **EnforceRegoPolicy** 효과 및 관련 **Kubernetes 서비스** 범주가 포함된 제한된 미리 보기 정책 정의는 ‘더 이상 사용되지 않습니다’. 대신 `Microsoft.Kubernetes.Data` 리소스 공급자 모드를 통해 _감사_ 및 _거부_ 효과를 사용합니다.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy 평가

Open Policy Agent 승인 컨트롤러는 클러스터에 대한 새로운 요청을 실시간으로 평가합니다.
15분마다 클러스터에 대한 전체 검사가 완료되고 Azure Policy에 결과가 보고됩니다.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy 속성

EnforceRegoPolicy 효과의 **details** 속성에는 Gatekeeper v2 허용 제어 규칙을 설명하는 하위 속성이 있습니다.

- **policyId**(필수)
  - Rego 허용 제어 규칙에 매개 변수로 전달되는 고유 이름입니다.
- **policy**(필수)
  - Rego 허용 제어 규칙의 URI를 지정합니다.
- **policyParameters**(선택 사항)
  - Rego 정책에 전달할 매개 변수 및 값을 정의합니다.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy 예제

예제: AKS에서 지정된 컨테이너 이미지만 허용하는 Gatekeeper v2 허용 제어 규칙입니다.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>수정

Modify는 구독이나 리소스의 만들기 또는 업데이트 중에 태그를 추가, 업데이트 또는 제거하는 데 사용됩니다. 일반적인 예는 costCenter와 같은 리소스의 태그를 업데이트하는 것입니다. 기존 비준수 리소스는 [수정 작업](../how-to/remediate-resources.md)을 통해 수정할 수 있습니다. 단일 Modify 규칙은 많은 수의 operations를 포함할 수 있습니다.

Modify에는 다음 작업이 지원됩니다.

- 리소스 태그를 추가, 바꾸기 또는 제거합니다. 대상 리소스가 리소스 그룹이 아닌 한, Modify 정책에서 태그의 `mode`는 항상 _Indexed_ 로 설정되어 있어야 합니다.
- 가상 머신 및 가상 머신 확장 세트의 관리 ID 형식(`identity.type`) 값을 추가하거나 바꿉니다.
- 특정 별칭의 값을 추가하거나 바꿉니다.
  - `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }` 사용
    Azure PowerShell **4.6.0** 이상에서 Modify와 함께 사용할 수 있는 별칭 목록을 가져옵니다.

> [!IMPORTANT]
> 태그를 관리할 때는 Append 대신 Modify를 사용하는 것이 좋습니다. Modify는 추가 작업 유형 및 기존 리소스를 수정하는 기능을 제공하기 때문입니다. 그러나 관리 ID를 만들 수 없거나 Modify가 아직 리소스 속성에 대한 별칭을 지원하지 않는 경우에는 Append를 사용하는 것이 좋습니다.

### <a name="modify-evaluation"></a>Modify 평가

Modify는 리소스의 생성 또는 업데이트하는 도중 리소스 공급자가 요청을 처리하기 전에 평가합니다. Modify 작업은 정책 규칙의 **if** 조건이 충족될 때 요청 콘텐츠에 적용됩니다. 각 Modify 작업에는 적용 시기를 결정하는 조건을 지정할 수 있습니다. 조건이 _false_ 로 평가되는 작업은 건너뜁니다.

별칭을 지정하는 경우에는 다음과 같은 추가 검사를 수행하여 Modify 작업으로 인해 리소스 공급자가 거부하게 만드는 방식으로 요청 콘텐츠를 변경하지 않도록 합니다.

- 별칭이 매핑되는 속성은 요청의 API 버전에서 'Modifiable'로 표시됩니다.
- Modify 작업의 토큰 형식은 요청의 API 버전에서 속성에 필요한 토큰 형식과 일치합니다.

이들 검사 중 하나라도 실패하면 정책 평가는 지정된 **conflictEffect** 로 대체됩니다.

> [!IMPORTANT]
> 매핑된 속성이 ‘Modifiable’이 아닌 API 버전을 사용하는 요청의 실패를 방지하기 위해, 별칭이 포함된 Modify 정의에는 **충돌 효과** _감사_ 를 사용하는 것이 좋습니다. 동일한 별칭이 API 버전마다 다르게 동작하는 경우에는 조건부 Modify 작업을 사용하여 각 API 버전에 사용되는 Modify 작업을 확인할 수 있습니다.

Modify 효과를 사용하는 정책 정의가 평가 주기의 일부로 실행되는 경우 이미 존재하는 리소스를 변경하지 않습니다. 대신 비호환으로 **if** 조건을 충족하는 모든 리소스를 표시합니다.

### <a name="modify-properties"></a>Modify 속성

Modify 효과의 **details** 속성에는 수정에 필요한 권한과 태그 값을 추가, 업데이트 또는 제거하는 데 사용되는 **operations** 를 정의하는 모든 하위 속성이 있습니다.

- **roleDefinitionIds**(필수)
  - 이 속성은 구독에서 액세스할 수 있는 역할 기반 액세스 제어 역할 ID와 일치하는 문자열 배열을 포함해야 합니다. 자세한 내용은 [수정 - 정책 정의 구성](../how-to/remediate-resources.md#configure-policy-definition)을 참조하세요.
  - 정의된 역할에는 [Contributor](../../../role-based-access-control/built-in-roles.md#contributor) 역할에 부여된 모든 operations가 포함되어야 합니다.
- **conflictEffect**(선택 사항)
  - 둘 이상의 정책 정의가 동일한 속성을 수정하거나 지정된 별칭에서 Modify 작업이 작동하지 않는 경우에 "우선하는" 정책 정의를 결정합니다.
    - 새 리소스 또는 업데이트된 리소스에서는 _deny_ 가 포함된 정책 정의가 우선적으로 적용됩니다. _audit_ 이 포함된 정책 정의는 모든 **작업** 을 건너뜁니다. 둘 이상의 정책 정의에 _deny_ 가 포함된 경우 요청이 충돌로 거부됩니다. 모든 정책 정의에 _audit_ 이 포함된 경우 충돌하는 정책 정의의 어떠한 **작업** 도 처리되지 않습니다.
    - 기존 리소스에서 둘 이상의 정책 정의에 _deny_ 가 포함된 경우 준수 상태는 _Conflict_ 입니다. 하나 이하의 정책 정의에 _deny_ 가 포함된 경우 각 할당 시 준수 상태를 _Non-compliant_ 로 반환합니다.
  - 사용 가능한 값: _audit_, _deny_, _disabled_.
  - 기본값은 _deny_ 입니다.
- **operations**(필수)
  - 일치하는 리소스에서 완료될 모든 태그 operations의 배열입니다.
  - 속성
    - **operation**(필수)
      - 일치하는 리소스에 수행할 작업을 정의합니다. 옵션에는 _addOrReplace_, _Add_, _Remove_ 가 있습니다. _Add_ 는 [Append](#append) 효과와 유사하게 작동합니다.
    - **field**(필수)
      - 추가, 대체 또는 제거할 태그입니다. 태그 이름은 다른 [fields](./definition-structure.md#fields)와 동일한 명명 규칙을 준수해야 합니다.
    - **value**(선택 사항)
      - 태그를 설정할 값입니다.
      - **operation** 이 _addOrReplace_ 또는 _Add_ 이면 이 속성이 필요합니다.
    - **condition**(필수)
      - _true_ 또는 _false_ 로 평가되는 [Policy 함수](./definition-structure.md#policy-functions)가 있는 Azure Policy 언어 식이 포함된 문자열입니다.
      - Policy 함수 `field()`, `resourceGroup()`, `subscription()`은 지원하지 않습니다.

### <a name="modify-operations"></a>operations 수정

**operations** 속성 배열을 사용하면 단일 정책 정의와 다른 방식으로 여러 태그를 변경할 수 있습니다. 각 operation은 **operation**, **field**, **value** 속성으로 구성됩니다. operation은 수정 작업이 태그에 수행할 작업을 결정하고, field는 변경될 태그를 결정하며, value는 태그에 대한 새 설정을 정의합니다. 아래 예시에서는 다음과 같이 태그를 변경합니다.

- `environment` 태그가 이미 다른 값으로 존재하더라도 "Test"로 설정합니다.
- `TempResource`태그를 제거합니다.
- `Dept` 태그를 정책 할당에 구성된 정책 매개 변수 _DeptName_ 으로 설정합니다.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

**operation** 속성에는 다음과 같은 옵션이 있습니다.

|작업(Operation) |Description |
|-|-|
|addOrReplace |속성 또는 태그가 다른 값으로 이미 존재하더라도 정의된 속성 또는 태그와 값을 리소스에 추가합니다. |
|추가 |정의된 속성 또는 태그와 값을 리소스에 추가합니다. |
|제거 |정의된 속성 또는 태그를 리소스에서 제거합니다. |

### <a name="modify-examples"></a>Modify 예제

예제 1: `environment` 태그를 추가하고 기존 `environment` 태그를 "Test"로 바꿉니다.

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

예제 2: `env` 태그를 제거하고 `environment` 태그를 추가하거나 기존 `environment` 태그를 매개 변수가 있는 값으로 바꿉니다.

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

예제 3: 스토리지 계정에서 Blob 공용 액세스를 허용하지 않는지 확인합니다. Modify 작업은 API 버전이 '2019-04-01' 이상인 요청을 평가할 때에만 적용됩니다.

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>정책 정의 계층화

리소스는 여러 할당에서 영향을 받을 수 있습니다. 이러한 할당은 동일한 범위 또는 서로 다른 범위에 있을 수 있습니다. 이러한 각 할당은 정의된 다른 효과를 가질 수 있습니다. 각 정책에 대한 조건 및 효과는 독립적으로 평가됩니다. 다음은 그 예입니다.

- 정책 1
  - 리소스 위치를 'westus'로 제한
  - 구독 A에 할당됨
  - 거부 효과
- 정책 2
  - 리소스 위치를 'eastus'로 제한
  - 구독 A의 리소스 그룹 B에 할당됨
  - 감사 효과

이 설정은 다음 결과로 나타납니다.

- 'eastus'에 이미 있는 리소스 그룹 B의 모든 리소스는 정책 2를 준수하지만 정책 1을 준수하지 않음
- 'eastus'에 있지 않은 리소스 그룹 B의 모든 리소스는 정책 2를 준수하지 않고 'westus'에 있지 않은 경우 정책 1도 준수하지 않음
- 'westus'에 있지 않은 구독 A의 모든 새 리소스는 정책 1에서 거부됨
- 'westus'에 있는 구독 A 및 리소스 그룹 B의 모든 새 리소스는 정책 2에서 생성되고 정책 2를 준수하지 않음

정책 1 및 정책 2 모두에 거부의 효과가 있는 경우 상황은 다음으로 변경됩니다.

- 'eastus'에 있지 않은 리소스 그룹 B의 모든 리소스는 정책 2를 준수하지 않음
- 'westus'에 있지 않은 리소스 그룹 B의 모든 리소스는 정책 1을 준수하지 않음
- 'westus'에 있지 않은 구독 A의 모든 새 리소스는 정책 1에서 거부됨
- 구독 A의 리소스 그룹 B에 있는 모든 새 리소스는 거부됨

각 할당은 개별적으로 평가됩니다. 따라서 리소스가 범위의 차이로 인한 간격을 통해 떨어질 기회는 없습니다. 계층화 정책 정의의 최종 결과는 **가장 제한적인 누적** 으로 간주됩니다. 예를 들어 정책 1 및 정책 2 모두에 deny 효과가 있으면 겹치고 충돌하는 정책 정의에 의해 리소스가 차단됩니다. 리소스를 여전히 대상 범위에서 만들어야 하는 경우 각 할당에 대한 제외를 검토하여 적절한 정책 할당이 적절한 범위에 적용되고 있는지 유효성을 검사합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](definition-structure.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](../how-to/programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
