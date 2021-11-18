---
title: Azure 역할 할당 조건 형식 및 구문(미리 보기) - Azure RBAC
description: Azure ABAC(Azure 특성 기반 액세스 제어)에 대한 Azure 역할 할당 조건의 형식 및 구문에 대한 개요를 확인하세요.
services: active-directory
author: rolyon
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 11/16/2021
ms.author: rolyon
ms.openlocfilehash: 487c2594c3ae6cc74405406855a6f2cfcce96262
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756187"
---
# <a name="azure-role-assignment-condition-format-and-syntax-preview"></a>Azure 역할 할당 조건 형식 및 구문(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

조건은 필요에 따라 역할 할당에 추가하여 더 세분화된 액세스 제어를 제공할 수 있는 추가 검사입니다. 예를 들어 개체를 읽을 특정 태그를 포함하는 개체를 필요로 하는 조건을 추가할 수 있습니다. 이 문서에서는 역할 할당 조건의 형식 및 구문을 설명합니다.

## <a name="condition-format"></a>조건 형식

역할 할당 조건을 더 잘 이해하려면 형식을 살펴보는 것이 좋습니다.

### <a name="simple-condition"></a>단순 조건

가장 기본적인 조건은 대상 동작과 식으로 구성됩니다. 작업은 사용자가 리소스 형식에 대해 수행할 수 있는 작업입니다. 식은 true 또는 false로 평가되는 명령문이며 작업을 수행할 수 있는지 여부를 결정합니다.

다음은 단순 조건의 형식을 보여줍니다.

![단일 작업 및 단일 식이 있는 단순 조건의 형식입니다.](./media/conditions-format/format-simple.png)

다음 조건에는 "Blob 읽기" 작업이 있습니다. 식은 컨테이너 이름이 blob-example-container인지 여부를 확인 합니다.

![Blob 읽기 작업 및 컨테이너 이름 식이 있는 단순 조건 예시입니다.](./media/conditions-format/format-simple-example.png)

![특정 컨테이너 이름을 가진 Blob에 대한 읽기 액세스 권한을 보여주는 다이어그램](./media/conditions-format/format-simple-example-diagram.png)

### <a name="how-a-condition-is-evaluated"></a>조건이 평가되는 방법

사용자가 `<action>`이 아닌 역할 할당에서 작업을 수행하려고 하면 `!(ActionMatches)`가 true로 평가되고 전체 조건이 true로 평가되어 작업을 수행할 수 있습니다.

사용자가 역할 할당에서 `<action>`을 수행하려고 하면 `!(ActionMatches)`가 false로 평가되므로 식이 평가됩니다. 식이 true로 평가되면 전체 조건이 true로 평가되어 `<action>`을 수행할 수 있습니다. 그렇지 않으면 `<action>`을 수행할 수 없습니다.

다음 의사 코드는 이 조건을 읽을 수 있는 다른 방법을 보여줍니다.

```
if a user tries to perform an action in the role assignment that does not match <action>
{
    Allow action to be performed
}
else
{
    if <attribute> <operator> <value> is true
    {
        Allow <action> to be performed
    }
    else
    {
        Do not allow <action> to be performed
    }
}
```

### <a name="suboperations"></a>하위 작업

일부 작업에는 하위 작업이 있습니다. 예를 들어 "Blob 읽기" 데이터 작업에는 "태그 조건이 있는 Blob에서 콘텐츠 읽기" 하위 작업이 있습니다. 하위 작업이 있는 조건의 형식은 다음과 같습니다.

![하위 작업이 있는 작업의 형식입니다.](./media/conditions-format/format-suboperation.png)

### <a name="multiple-actions"></a>여러 작업

조건에는 조건이 true인 경우 허용할 여러 작업이 포함될 수 있습니다. 단일 조건에 대해 여러 작업을 선택하는 경우 선택한 작업에서 특성을 사용할 수 있어야 하므로 조건에 대해 선택할 특성이 적을 수 있습니다.

![조건이 true인 경우 허용할 여러 작업의 형식입니다.](./media/conditions-format/format-multiple-actions.png)

### <a name="multiple-expressions"></a>여러 식

조건에는 여러 식이 포함될 수 있습니다. 연산자에 따라 여러 값에 대해 특성을 확인할 수 있습니다.

![부울 연산자와 여러 값을 사용하는 여러 식의 형식입니다.](./media/conditions-format/format-multiple-expressions.png)

### <a name="multiple-conditions"></a>여러 조건

조건을 결합하여 여러 작업을 대상으로 지정할 수도 있습니다.

![부울 연산자를 사용하는 여러 조건의 형식입니다.](./media/conditions-format/format-multiple-conditions.png)

## <a name="condition-syntax"></a>조건 구문

다음은 역할 할당 조건에 대한 구문을 보여줍니다.

```
(
    (
        !(ActionMatches{'<action>'} AND SubOperationMatches{'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND SubOperationMatches{'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
(
    (
        !(ActionMatches{'<action>'} AND SubOperationMatches{'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND SubOperationMatches{'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
...
```

## <a name="actions"></a>동작

현재 스토리지 Blob 데이터 작업을 포함하는 기본 제공 또는 사용자 지정 역할 할당에 조건을 추가할 수 있습니다. 여기에는 다음과 같은 기본 제공 역할이 포함됩니다.

- [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor)
- [Storage Blob 데이터 소유자](built-in-roles.md#storage-blob-data-owner)
- [Storage Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader)

조건에서 사용할 수 있는 스토리지 Blob 작업 목록은 [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](../storage/common/storage-auth-abac-attributes.md)을 참조하세요.

## <a name="attributes"></a>특성

선택한 작업에 따라 다른 위치에서 특성을 찾을 수 있습니다. 단일 조건에 대해 여러 작업을 선택하는 경우 선택한 작업에서 특성을 사용할 수 있어야 하므로 조건에 대해 선택할 특성이 적을 수 있습니다. 특성을 지정하려면 원본을 접두사로 포함해야 합니다.

> [!div class="mx-tableFixed"]
> | 특성 원본 | Description | 코드 |
> | --- | --- | --- |
> | 리소스 | 특성이 컨테이너 이름과 같은 리소스에 있음을 나타냅니다. | `@Resource` |
> | 요청 | 특성이 Blob 인덱스 태그 설정과 같은 작업 요청의 일부임을 나타냅니다. | `@Request` |
> | 주 서버 | 특성이 사용자, 엔터프라이즈 애플리케이션(서비스 주체) 또는 관리 ID와 같은 보안 주체의 Azure AD 사용자 지정 보안 특성임을 나타냅니다. | `@Principal` |

#### <a name="resource-and-request-attributes"></a>리소스 및 요청 특성

조건에서 사용할 수 있는 스토리지 Blob 특성 목록은 다음을 참조하세요.

- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](../storage/common/storage-auth-abac-attributes.md)

#### <a name="principal-attributes"></a>보안 주체 특성

보안 주체 특성을 사용하려면 **다음이 모두** 있어야 합니다.

- Azure AD Premium P1 또는 P2 라이선스
- [로그인한](../active-directory/roles/permissions-reference.md#attribute-assignment-administrator) 사용자에 대한 Azure AD 권한(예: 특성 할당 관리자 역할)
- Azure AD에 정의된 사용자 지정 보안 특성

사용자 지정 보안 특성에 대한 자세한 내용은 다음을 참조하세요.

- [태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스 허용](conditions-custom-security-attributes.md)
- [조건을 추가할 때 보안 주체가 특성 원본에 표시되지 않음](conditions-troubleshoot.md#symptom---principal-does-not-appear-in-attribute-source-when-adding-a-condition)
- [Azure AD에서 사용자 지정 보안 특성 추가 또는 비활성화](../active-directory/fundamentals/custom-security-attributes-add.md)

## <a name="operators"></a>연산자

다음 표에는 조건을 구성하는 데 사용할 수 있는 연산자가 나열되어 있습니다.

| 범주 | 연산자 | Description |
| --- | --- | --- |
| 논리 비교 |`AND`<br/>`&&` | And 연산자 |
|  | `OR`<br/>`||` | Or 연산자 |
|  | `NOT`<br/>`!` | Not 또는 부정 연산자 |
| 문자열 비교 | `StringEquals`<br/>`StringEqualsIgnoreCase` | 대/소문자를 구분하여(또는 대/소문자를 구분하지 않고) 일치 여부 확인. 값은 문자열과 정확히 일치해야 합니다. |
|  | `StringNotEquals`<br/>`StringNotEqualsIgnoreCase` | `StringEquals`(또는 `StringEqualsIgnoreCase`) 연산자의 부정 |
|  | `StringStartsWith`<br/>`StringStartsWithIgnoreCase` | 대/소문자를 구분하여(또는 대/소문자를 구분하지 않고) 일치 여부 확인. 값은 문자열로 시작합니다. |
|  | `StringNotStartsWith`<br/>`StringNotStartsWithIgnoreCase` | `StringStartsWith`(또는 `StringStartsWithIgnoreCase`) 연산자의 부정 |
|  | `StringLike`<br/>`StringLikeIgnoreCase` | 대/소문자를 구분하여(또는 대/소문자를 구분하지 않고) 일치 여부 확인. 이 값은 문자열의 임의의 위치에 여러 문자 일치 와일드카드(`*`) 또는 단일 문자 일치 와일드카드(`?`)를 포함할 수 있습니다. 이러한 문자는 필요한 경우 백슬래시 `\*` 및 `\?`를 추가하여 이스케이프할 수 있습니다. |
|  | `StringNotLike`<br/>`StringNotLikeIgnoreCase` | `StringLike`(또는 `StringLikeIgnoreCase`) 연산자의 부정 |
| 숫자 비교 | `NumericEquals`<br/>`NumericNotEquals`<br/>`NumericLessThan`<br/>`NumericLessThanEquals`<br/>`NumericGreaterThan`<br/>`NumericGreaterThanEquals` | 현재는 정수만 지원됩니다. |
| 상위 수준 함수 | `ActionMatches` | Action[ID] 값이 지정된 작업 패턴과 일치하는지 확인합니다. 이 연산자는 SDK가 권한 내의 작업 패턴과 작업을 비교할 때 사용하는 작업 일치 논리와 동일합니다. |
| 교차곱 비교 | `ForAnyOfAnyValues:StringEquals`<br/>`ForAnyOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotEquals`<br/>`ForAnyOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringLike`<br/>`ForAnyOfAnyValues:StringLikeIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotLike`<br/>`ForAnyOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAnyValues:NumericEquals`<br/>`ForAnyOfAnyValues:NumericNotEquals`<br/>`ForAnyOfAnyValues:NumericGreaterThan`<br/>`ForAnyOfAnyValues:NumericGreaterThanEquals`<br/>`ForAnyOfAnyValues:NumericLessThan`<br/>`ForAnyOfAnyValues:NumericLessThanEquals` | 왼쪽에 있는 하나 이상의 값이 오른쪽에 있는 하나 이상의 값에 대한 비교를 충족하는 경우 식은 true로 평가됩니다. 형식은 `ForAnyOfAnyValues:<BooleanFunction>`입니다. 여러 문자열과 숫자를 지원합니다. |
|  | `ForAllOfAnyValues:StringEquals`<br/>`ForAllOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringNotEquals`<br/>`ForAllOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringLike`<br/>`ForAllOfAnyValues:StringLikeIgnoreCase`<br/>`ForAllOfAnyValues:StringNotLike`<br/>`ForAllOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAnyValues:NumericEquals`<br/>`ForAllOfAnyValues:NumericNotEquals`<br/>`ForAllOfAnyValues:NumericGreaterThan`<br/>`ForAllOfAnyValues:NumericGreaterThanEquals`<br/>`ForAllOfAnyValues:NumericLessThan`<br/>`ForAllOfAnyValues:NumericLessThanEquals` | 왼쪽에 있는 모든 값이 오른쪽에 있는 하나 이상의 값에 대한 비교를 충족하는 경우 식은 true로 평가됩니다. 형식은 `ForAllOfAnyValues:<BooleanFunction>`입니다. 여러 문자열과 숫자를 지원합니다. |
|  | `ForAnyOfAllValues:StringEquals`<br/>`ForAnyOfAllValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringNotEquals`<br/>`ForAnyOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringLike`<br/>`ForAnyOfAllValues:StringLikeIgnoreCase`<br/>`ForAnyOfAllValues:StringNotLike`<br/>`ForAnyOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAllValues:NumericEquals`<br/>`ForAnyOfAllValues:NumericNotEquals`<br/>`ForAnyOfAllValues:NumericGreaterThan`<br/>`ForAnyOfAllValues:NumericGreaterThanEquals`<br/>`ForAnyOfAllValues:NumericLessThan`<br/>`ForAnyOfAllValues:NumericLessThanEquals` | 왼쪽에 있는 하나 이상의 값이 오른쪽에 있는 모든 값에 대한 비교를 충족하는 경우 식은 true로 평가됩니다. 형식은 `ForAnyOfAllValues:<BooleanFunction>`입니다. 여러 문자열과 숫자를 지원합니다. |
|  | `ForAllOfAllValues:StringEquals`<br/>`ForAllOfAllValues:StringEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringNotEquals`<br/>`ForAllOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringLike`<br/>`ForAllOfAllValues:StringLikeIgnoreCase`<br/>`ForAllOfAllValues:StringNotLike`<br/>`ForAllOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAllValues:NumericEquals`<br/>`ForAllOfAllValues:NumericNotEquals`<br/>`ForAllOfAllValues:NumericGreaterThan`<br/>`ForAllOfAllValues:NumericGreaterThanEquals`<br/>`ForAllOfAllValues:NumericLessThan`<br/>`ForAllOfAllValues:NumericLessThanEquals` | 왼쪽에 있는 모든 값이 오른쪽에 있는 모든 값에 대한 비교를 충족하는 경우 식은 true로 평가됩니다. 형식은 `ForAllOfAllValues:<BooleanFunction>`입니다. 여러 문자열과 숫자를 지원합니다. |

## <a name="operator-examples"></a>연산자 예제

> [!div class="mx-tableFixed"]
> | 예제 | 결과 |
> | --- | --- |
> | `ActionMatches{'Microsoft.Authorization/roleAssignments/*'}` | 확인되는 작업이 "Microsoft.Authorization/roleAssignments/write"와 같으면 true입니다. |
> | `ActionMatches{'Microsoft.Authorization/roleDefinitions/*'}` | 확인되는 작업이 "Microsoft.Authorization/roleAssignments/write"와 같으면 false입니다. |
> | `Resource[name1] StringLike 'a*c?'` | Resource[name1]이 "abcd"와 같으면 true입니다. |
> | `Resource[name1] StringLike 'A*C?'` | Resource[name1]이 "abcd"와 같으면 false입니다. |
> | `Resource[name1] StringLike 'a*c'` | Resource[name1]이 "abcd"와 같으면 false입니다. |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'blue', 'green'}` | true |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'orange', 'green'}` | false |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'orange', 'red', 'blue'}` | true |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'red', 'green'}` | false |
> | `{10, 20} ForAnyOfAllValues:NumericLessThan {15, 18}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {5, 15, 18}` | false |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {25, 30}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {15, 25, 30}` | false |

## <a name="special-characters"></a>특수 문자

| 문자 | Description |
| --- | --- |
| `*` | 별표(*)는 여러 문자 와일드 카드 일치를 나타내며 `Like` 연산자와 함께 사용할 수 있습니다. 필요한 경우 백슬래시를 추가하여(`\*`) 별표를 이스케이프할 수 있습니다. |
| `?` | 물음표(?)는 단일 문자 와일드 카드 일치를 나타내며 `Like` 연산자와 함께 사용할 수 있습니다. 필요한 경우 백슬래시를 추가하여(`\?`) 물음표를 이스케이프할 수 있습니다. |
| `$` | 달러 기호($)는 태그 키를 묘사하는 데 사용됩니다. Azure PowerShell에서 큰따옴표(")로 묶인 문자열에 달러 기호가 포함된 경우 백틱(\`) 접두사를 붙여야 합니다. 예: ``tags:Project<`$key_case_sensitive`$>`` | 

## <a name="grouping-and-precedence"></a>그룹화 및 우선 순위

식 사이에 다른 연산자를 사용 하는 대상 동작에 대 한 식이 3 개 이상 있는 경우 평가 순서는 모호 합니다. 괄호를 사용 `()` 하 여 식을 그룹화 하 고 식이 계산 되는 순서를 지정 합니다. 괄호로 묶인 식이 우선 순위가 더 높습니다. 예를 들어 다음 식이 있는 경우:

```
a AND b OR c
```

다음 중 한 가지 방법으로 괄호를 추가해야 합니다.

```
(a AND b) OR c
```

```
a AND (b OR c)
```

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예(미리 보기)](../storage/common/storage-auth-abac-examples.md)
- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](../storage/common/storage-auth-abac-attributes.md)
- [Azure Portal을 사용하여 Azure 역할 할당 조건 추가 또는 편집(미리 보기)](conditions-role-assignments-portal.md)
