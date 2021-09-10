---
title: 게스트 구성에 대한 수정 옵션
description: Azure Policy의 게스트 구성 기능은 수정 작업을 사용하여 지속적인 수정 또는 제어 옵션을 제공합니다.
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 6c16f4a2b20ea753b1ded4e8d389babc613b4b36
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773102"
---
# <a name="remediation-options-for-guest-configuration"></a>게스트 구성에 대한 수정 옵션

시작하기 전에 [게스트 구성](../concepts/guest-configuration.md)에 대한 개요 페이지를 읽어보는 것이 좋습니다.

> [!IMPORTANT]
> 게스트 구성 확장은 Azure 가상 머신에 필요합니다. 모든 머신에서 확장을 대규모로 배포하려면 정책 이니셔티브(`Deploy prerequisites to enable guest configuration policies on
> virtual machines`)를 할당합니다.
> 
> 구성을 적용하는 게스트 구성 패키지를 사용하려면 Azure VM 게스트 구성 확장 버전 **1.29.24** 이상 또는 Arc 에이전트 **1.10.0** 이상이 필요합니다.
>
> **AuditIfNotExists** 를 사용한 사용자 지정 게스트 구성 정책 정의는 일반적으로 사용 가능하지만 게스트 구성을 포함한 **DeployIfNotExists** 를 사용한 정의는 **미리 보기 상태** 입니다.

## <a name="how-remediation-set-is-managed-by-guest-configuration"></a>게스트 구성에서 수정(설정) 관리 방법

게스트 구성은 머신 내에서 변경 내용을 제공하는 정의에 대해 정책 효과([DeployIfNotExists](../concepts/effects.md#deployifnotexists))를 사용합니다.
[평가](../concepts/effects.md#deployifnotexists-evaluation)가 자동으로 또는 주문에 의해 구성을 제공하는 방법을 제어하기 위해 정책 할당의 속성을 설정합니다.

[이 문서의 동영상 연습 사용 가능](https://youtu.be/rjAk1eNmDLk).

### <a name="guest-configuration-assignment-types"></a>게스트 구성 할당 유형

게스트 할당을 만들 때 사용 가능한 할당 유형은 세 가지입니다.
속성은 **DeployIfNotExists** 를 지원하는 게스트 구성 정의의 매개 변수로 사용할 수 있습니다.

| 할당 유형 | 동작 |
|-|-|
| 감사 | 머신의 상태를 보고하지만 변경하지는 않습니다. |
| ApplyandMonitor | 머신에 한 번 적용된 후 변경 내용을 모니터링합니다. 구성이 드리프트되고 NonCompliant 상태가 되는 경우에는 수정이 트리거되지 않는 한 자동으로 수정되지 않습니다. |
| ApplyandAutoCorrect | 머신에 적용됩니다. 머신이 드리프트될 경우 머신 내부의 로컬 서비스는 다음 평가에서 수정합니다. |

세 가지 할당 유형 각각에서 새로운 정책 할당이 기존 머신에 할당되면 구성 상태를 감사하는 게스트 할당이 자동으로 생성되며, 수정이 필요한 머신 관련 결정을 내리는 데 필요한 정보가 제공됩니다.

## <a name="remediation-on-demand-applyandmonitor"></a>주문형 수정(ApplyAndMonitor)

기본적으로 게스트 구성 할당은 “요청 시 수정” 시나리오에서 작동합니다. 구성이 적용된 다음에는 드리프트되어 규정을 준수하지 않는 것이 허용됩니다. 구성을 적용하는 동안 오류가 발생하지 않거나 다음 평가 중에 머신이 더 이상 원하는 상태가 아닌 경우 게스트 할당의 규정 준수 상태는 “Compliant”입니다. 에이전트는 상태를 “NonCompliant”로 보고하고 자동으로 수정하지 않습니다.

이 동작을 사용하려면 게스트 구성 할당의 [assignmentType 속성](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)을 “ApplyandMonitor”로 설정합니다. 머신 내에서 할당이 처리될 때마다 [Test](/powershell/scripting/dsc/resources/get-test-set#test) 메서드는 각 리소스에 “true”를 반환하고 에이전트는 “Compliant”를 보고합니다. 또는 메서드가 “false”를 반환하는 경우 에이전트는 “NonCompliant”를 보고합니다.

## <a name="continuous-remediation-autocorrect"></a>연속 수정(자동 수정)

게스트 구성은 “연속 수정” 개념을 지원합니다. 구성에서 머신이 드리프트되어 규정을 준수하지 않을 경우 다음 평가 시 구성이 자동으로 수정됩니다. 오류가 발생하지 않는 한 머신은 항상 구성 상태를 “Compliant”로 보고합니다. 연속 수정을 사용할 때 드리프트가 자동으로 수정된 시기를 보고할 방법은 없습니다.

이 동작을 사용하려면 게스트 구성 할당의 [assignmentType 속성](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)을 “ApplyandAutoCorrect”로 설정합니다. 머신 내에서 할당이 처리될 때마다 [Test](/powershell/scripting/dsc/resources/get-test-set#test) 메서드는 각 리소스에 “false”를 반환하고 [Set](/powershell/scripting/dsc/resources/get-test-set#set) 메서드는 자동으로 실행됩니다.

## <a name="disable-remediation"></a>수정 사용 안 함

`assignmentType` 속성이 “Audit”로 설정된 경우 에이전트는 머신만 감사하고 구성이 규정을 준수하지 않는 경우에는 수정을 시도하지 않습니다.

### <a name="disable-remediation-of-custom-content"></a>사용자 지정 콘텐츠 수정 사용 안 함

이름이 **CustomGuestConfigurationSetPolicy** 이고 값이 **사용 중지** 인 머신에 태그를 추가하여 사용자 지정 콘텐츠 패키지에 대한 할당 유형 속성을 재정의할 수 있습니다. 태그를 추가하면 Microsoft에서 기본 제공하는 콘텐츠가 아니라 사용자 지정 콘텐츠 패키지만 수정할 수 없습니다.

## <a name="azure-policy-enforcement"></a>Azure Policy 적용

Azure Policy 할당에는 새 리소스 및 기존 리소스 동작을 결정하는 필수 속성 [적용 모드](../concepts/assignment-structure.md#enforcement-mode)가 포함됩니다.
구성이 머신에 자동으로 적용되는지를 제어하려면 이 속성을 사용합니다.

**기본적으로 적용은 “사용 가능”** 합니다. 새 머신을 배포하거나 **머신의 속성을 업데이트** 할 때 머신이 “게스트 구성” 범주의 정책 정의를 사용하여 Azure Policy 할당 범위에 있으면 Azure Policy는 자동으로 구성을 적용합니다. **업데이트 작업에는 Azure Resource Manager에서 발생하는 작업**(예: 태그 추가 또는 변경)과 가상 머신에 대해서는 변경(예: 디스크 크기 조정 또는 연결)이 포함됩니다. Azure의 머신 리소스가 변경될 때 구성을 수정해야 하는 경우 적용을 사용 가능하게 설정된 상태로 둡니다. 머신 내 변경 내용은 Azure Resource Manager에서 머신 리소스를 변경하지 않는 한 자동 수정을 트리거하지 않습니다.

적용을 “사용 안 함”으로 설정하면 [수정 작업](../how-to/remediate-resources.md)을 통해 동작이 변경될 때까지 구성 할당에서 머신의 상태를 감사합니다. 기본적으로 게스트 구성 정의는 [assignmentType 속성](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)을 “Audit”에서 “ApplyandMonitor”로 업데이트하므로, 구성이 한 번만 적용된 후에는 수정을 트리거할 때까지 다시 적용되지 않습니다.

## <a name="optional-remediate-all-existing-machines"></a>선택 사항: 기존 머신을 모두 수정

Azure Portal에서 Azure Policy 할당을 만든 경우 “수정” 탭에서 “수정 작업 만들기” 확인란을 사용할 수 있습니다. 이 확인란을 선택하면 정책 할당을 만든 후 “NonCompliant”로 평가되는 모든 리소스가 수정 작업에 의해 자동으로 수정됩니다.

게스트 구성에 대한 이 설정으로 인해 정책 할당만으로 여러 머신에서 구성을 배포할 수 있습니다. 또한 규정을 준수하지 않는 머신에는 수정 작업을 수동으로 실행하지 않아도 됩니다.

## <a name="manually-trigger-remediation-outside-of-azure-policy"></a>Azure Policy 외부에서 수동으로 수정을 트리거

업데이트에서 리소스 속성을 변경하지 않는 경우에도 게스트 할당 리소스를 업데이트하여 Azure Policy 환경 외부에서 수정을 오케스트레이션 할 수 있습니다.

게스트 구성 할당이 생성되면 [complianceStatus 속성](/rest/api/guestconfiguration/guest-configuration-assignments/get#compliancestatus)이 “Pending”으로 설정됩니다.
머신 내의 게스트 구성 서비스([게스트 구성 확장](../../../virtual-machines/extensions/guest-configuration.md)에 의해 Azure 가상 머신에 제공되고 Arc 지원 서버와 함께 포함됨)는 5분마다 할당 목록을 요청합니다.
게스트 구성 할당에 두 가지 요구 사항(“Pending”의 `complianceStatus`, “ApplyandMonitor” 또는 “ApplyandAutoCorrect”의 `configurationMode`)이 모두 있는 경우 머신의 서비스가 구성을 적용합니다. 구성이 적용된 후 [다음 간격](./guest-configuration.md#validation-frequency)에서 구성 모드는 동작이 규정 준수 상태에 대해 보고만 하고 드리프트를 허용할지 아니면 자동으로 수정할지를 결정합니다.

## <a name="understanding-combinations-of-settings"></a>설정 조합 이해

|~| 감사 | ApplyandMonitor | ApplyandAutoCorrect |
|-|-|-|-|
| 적용 사용 | 상태만 보고 | 구성을 VM 만들기에 적용하고 **업데이트에 다시 적용** 했지만 그 외에는 드리프트를 허용함 | 구성을 VM 만들기에 적용하고 업데이트에 다시 적용했으며 드리프트 발생 시 다음 간격에 수정함 |
| 적용 사용 안 함 | 상태만 보고 | 구성이 적용되었지만 드리프트가 허용됨 | 구성을 VM 만들기 또는 업데이트에 적용하고 드리프트 발생 시 다음 간격에 수정함 |

## <a name="next-steps"></a>다음 단계

- [게스트 구성 개요](./guest-configuration.md)를 읽어봅니다.
- 사용자 지정 게스트 구성 패키지 [개발 환경](../how-to/guest-configuration-create-setup.md)을 설치합니다.
- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](../how-to/guest-configuration-create.md).
- 사용자 개발 환경에서 [패키지 아티팩트 테스트](../how-to/guest-configuration-create-test.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](../how-to/guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
