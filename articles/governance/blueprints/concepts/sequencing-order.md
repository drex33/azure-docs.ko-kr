---
title: 배포 순서 이해
description: 청사진 할당 도중 청사진 아티팩트가 배포되는 순서와 배포 순서를 사용자 지정하는 방법을 알아봅니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: aef672d4f32af0300932b8a2c953816b2f861029
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122568018"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Azure Blueprint의 배포 순서 이해

Azure Blueprints는 청사진 정의의 할당을 처리할 때 **시퀀싱 순서** 를 사용하여 리소스 만들기 순서를 결정합니다. 이 문서에서는 다음 개념에 대해 설명합니다.

- 사용되는 기본 시퀀싱 순서
- 순서를 사용자 지정하는 방법
- 사용자 지정 순서가 처리되는 방법

JSON 예제에는 사용자 고유의 값으로 바꿔야 하는 변수가 있습니다.

- `{YourMG}` - 사용자의 관리 그룹 이름으로 대체

## <a name="default-sequencing-order"></a>기본 시퀀싱 순서

청사진 정의에 아티팩트 배포 순서에 대한 지시문이 없거나 지시문이 null이면 다음 순서가 사용됩니다.

- 아티팩트 이름을 기준으로 정렬된 구독 수준 **역할 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 구독 수준 **정책 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 구독 수준 **Azure Resource Manager 템플릿**(ARM 템플릿) 아티팩트
- 자리 표시자 이름을 기준으로 정렬된 **리소스 그룹** 아티팩트(자식 아티팩트 포함)

각 **리소스 그룹** 아티팩트 내에서, 다음 시퀀스 순서를 사용하여 해당 리소스 그룹 내에 아티팩트를 만듭니다.

- 아티팩트 이름을 기준으로 정렬된 리소스 그룹 자식 **역할 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 리소스 그룹 자식 **정책 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 리소스 그룹 자식 **Azure Resource Manager 템플릿**(ARM 템플릿) 아티팩트

> [!NOTE]
> [artifacts()](../reference/blueprint-functions.md#artifacts)를 사용하면 참조되는 아티팩트에 대한 암시적 종속성을 만듭니다.

## <a name="customizing-the-sequencing-order"></a>시퀀싱 순서 사용자 지정

대형 청사진 정의를 작성할 때 특정 순서에 따라 리소스를 만들어야 하는 경우도 있습니다. 이 시나리오의 가장 일반적인 사용 패턴은 청사진 정의에 여러 개의 ARM 템플릿이 포함된 경우입니다. Azure Blueprints는 시퀀싱 순서를 정의할 수 있도록 허용하여 이 패턴을 처리합니다.

JSON에서 `dependsOn` 속성을 정의하여 순서를 지정할 수 있습니다. 리소스 그룹에 대한 청사진 정의 및 아티팩트 개체만 이 속성을 지원합니다. `dependsOn`은 특정 아티팩트가 만들어지기 전에 만들어야 하는 아티팩트 이름의 문자열 배열입니다.

> [!NOTE]
> 청사진 개체를 만들 때 각 아티팩트 리소스는 [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)을 사용하는 파일 이름으로부터, 또는 [REST API](/rest/api/blueprints/artifacts/createorupdate)를 사용하는 경우 URL 엔드포인트로부터 이름을 가져옵니다. 아티팩트의 _resourceGroup_ 참조는 청사진 정의에 정의되는 참조와 일치해야 합니다.

### <a name="example---ordered-resource-group"></a>예 - 순서가 지정된 리소스 그룹

이 예제 청사진 정의에는 표준 리소스 그룹과 함께 `dependsOn` 값을 선언하여 사용자 지정 시퀀싱 순서가 정의된 리소스 그룹이 있습니다. 이 예에서 **assignPolicyTags** 라는 이름의 아티팩트가 **ordered-rg** 리소스 그룹보다 먼저 처리됩니다.
**standard-rg** 는 기본 시퀀싱 순서대로 처리됩니다.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>예 - 사용자 지정 순서를 사용하는 아티팩트

이 예제는 ARM 템플릿을 사용하는 정책 아티팩트입니다. 기본 순서를 사용하여 정책 아티팩트를 만든 후 ARM 템플릿을 만듭니다. 이 순서 지정을 사용하면 ARM 템플릿이 만들어질 때까지 정책 아티팩트가 대기할 수 있습니다.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>예 - 리소스 그룹에 따른 구독 수준 템플릿 아티팩트

리소스 그룹에 따라 다른 구독 수준에서 배포된 ARM 템플릿에 대한 예입니다. 기본 순서 지정에서 구독 수준 아티팩트는 해당 리소스 그룹의 모든 리소스 그룹 및 자식 아티팩트 앞에 생성됩니다. 리소스 그룹은 다음과 같은 청사진 정의에서 정의됩니다.

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

**wait-for-me** 리소스 그룹에 따라 구독 수준 템플릿이 다음과 같이 정의됩니다.

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>사용자 지정된 시퀀스 처리

만들기 프로세스가 진행되는 동안 토폴로지 정렬을 사용하여 청사진 아티팩트의 종속성 그래프가 만들어집니다. 리소스 그룹과 아티팩트 간의 각 종속성 수준이 지원되는지 확인합니다.

기본 순서를 변경하지 않는 아티팩트 종속성이 선언된 경우에는 변경 작업이 수행되지 않습니다.
구독 수준 정책을 사용하는 리소스 그룹을 예로 들 수 있습니다. 다른 예로는 리소스 그룹 ‘standard-rg’ 자식 역할 할당을 사용하는 리소스 그룹 ‘standard-rg’ 자식 정책 할당이 있습니다. 두 경우 모두 `dependsOn`이 기본값을 변경하지 않으며 아무 것도 변경되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](./lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](./parameters.md) 사용 방법 이해
- [청사진 리소스 잠금](./resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결