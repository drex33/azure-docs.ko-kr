---
title: Azure Policy의 범위 이해
description: Azure Resource Manager의 범위 개념과 Azure Policy에서 평가할 리소스를 제어하는 Azure Policy에 적용하는 방법에 대해 설명합니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: b83acebe3f24b7dac1c506a598c641035132fb30
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567906"
---
# <a name="understand-scope-in-azure-policy"></a>Azure Policy의 범위 이해

평가할 수 있는 리소스와 Azure Policy에 의해 평가되는 리소스를 결정하는 여러 설정이 있습니다. 이러한 컨트롤에 대한 기본 개념은 _범위_ 입니다. Azure Policy의 범위는 Azure Resource Manager에서 범위가 작동하는 방식을 기반으로 합니다. 고급 개요는 [Azure Resource Manager의 범위](../../../azure-resource-manager/management/overview.md#understand-scope)를 참조하세요.
이 문서에서는 Azure Policy의 _범위_ 의 중요성과 관련 개체 및 속성을 설명합니다.

## <a name="definition-location"></a>정의 위치

Azure Policy에서 사용하는 첫 번째 인스턴스 범위는 정책 정의를 만들 때입니다. 정의는 관리 그룹 또는 구독 중 하나에 저장될 수 있습니다. 해당 위치는 이니셔티브 또는 정책을 할당할 수 있는 범위를 결정합니다. 리소스는 할당 대상으로 지정할 정의 위치의 계층 구조 내에 있어야 합니다.

정의 위치는 다음과 같습니다.

- **구독** - 해당 구독 내의 리소스만 정책 정의에 할당할 수 있습니다.
- **관리 그룹** - 자식 관리 그룹과 자식 구독 내의 리소스만 정책 정의에 할당할 수 있습니다. 정책 정의를 여러 구독에 적용하려는 경우 위치는 각 구독이 포함된 관리 그룹이어야 합니다.

위치는 정책 정의를 사용하려는 모든 리소스에서 공유하는 리소스 컨테이너여야 합니다. 해당 리소스 컨테이너는 일반적으로 루트 관리 그룹 근처의 관리 그룹입니다.

## <a name="assignment-scopes"></a>할당 범위

할당에는 범위를 설정하는 몇 가지 속성이 있습니다. 이러한 속성을 사용하여 평가할 Azure Policy 리소스와 규정 준수에 중요한 리소스가 무엇인지 결정합니다. 이러한 속성은 다음 개념에 매핑됩니다.

- 포함 - 정의에 따라 리소스 계층 또는 개별 리소스의 규정 준수를 평가해야 합니다. 할당 개체의 `properties.scope` 속성은 규정 준수에 포함하고 평가할 항목을 결정합니다. 자세한 내용은 [할당 정의](./assignment-structure.md)를 참조하세요.

- 제외 - 정의에 따라 리소스 계층 또는 개별 리소스는 규정 준수를 평가하지 않아야 합니다. 할당 개체의 `properties.notScopes` _배열_ 속성은 제외할 항목을 결정합니다. 이러한 범위 내의 리소스는 평가되거나 규정 준수 개수에 포함되지 않습니다. 자세한 내용은 [할당 정의 - 제외된 범위](./assignment-structure.md#excluded-scopes)를 참조하세요.

정책 할당의 속성 이외에도 [정책 예외](./exemption-structure.md) 개체가 있습니다. 예외는 평가하지 않을 할당의 일부를 식별하는 방법을 제공하여 범위 스토리를 개선합니다.

- 예외(**미리 보기 평가판** 기능) - 리소스 계층 또는 개별 리소스는 정의에 따라 규정 준수를 평가해야 하지만 면제되었거나 다른 방법을 통해 완화되는 등의 이유로 평가되지 않습니다. 이 상태의 리소스는 추적할 수 있도록 규정 준수 보고서에 **제외** 된 것으로 표시됩니다. 예외 개체는 리소스 계층 구조 또는 개별 리소스에 대해 자식 개체로 만들어지며 예외 범위를 결정합니다. 리소스 계층 또는 개별 리소스는 여러 할당에서 제외될 수 있습니다. 예외는 `expiresOn` 속성을 사용하여 일정에 따라 만료되도록 구성할 수 있습니다. 자세한 내용은 [예외 정의](./exemption-structure.md)를 참조하세요.

  > [!NOTE]
  > 리소스 계층 또는 개별 리소스에 대한 예외 부여에 따른 영향으로 인해 예외에는 추가 보안 조치가 있습니다. 리소스 계층 또는 개별 리소스에 대한 `Microsoft.Authorization/policyExemptions/write` 작업을 요구하는 것 이외에도 예외 작성자에게는 대상 할당에 대한 `exempt/Action` 동사가 있어야 합니다.

## <a name="scope-comparison"></a>범위 비교

다음 표는 범위 옵션에 대한 비교를 보여 줍니다.

| | 포함 | 제외(notScopes) | 예외 |
|---|:---:|:---:|:---:|
|**리소스가 평가됨** | &#10004; | - | - |
|**Resource Manager 개체** | - | - | &#10004; |
|**정책 할당 개체를 수정해야 함** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.