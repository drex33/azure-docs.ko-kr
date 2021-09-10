---
title: Azure 정책 개요
description: Azure Policy는 Azure 환경에서 정책 정의를 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다.
ms.date: 07/27/2021
ms.topic: overview
ms.openlocfilehash: 9462281ce37e066a920c17d1dc142a177b97fb2a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770553"
---
# <a name="what-is-azure-policy"></a>Azure Policy이란?

Azure Policy를 사용하면 조직의 표준을 적용하고 규정 준수를 규모에 맞게 평가할 수 있습니다. 리소스별 정책별 세분성으로 드릴다운할 수 있는 기능을 사용하여 환경의 전체 상태를 평가할 수 있는 집계된 보기가 규정 준수 대시보드를 통해 제공됩니다. 또한 기존 리소스에 대한 대량 수정 및 새 리소스에 대한 자동 수정을 통해 리소스를 규정 준수 상태로 전환할 수 있습니다.

Azure Policy에 대한 일반적인 사용 사례에는 리소스 일관성, 규정 준수, 보안, 비용 및 관리에 대한 거버넌스 구현이 포함됩니다. 이러한 일반적인 사용 사례에 대한 정책 정의는 Azure 환경에서 이미 기본 제공 기능으로 사용할 수 있으므로 시작하는 데 도움이 됩니다.

모든 Azure Policy 데이터와 개체는 미사용 시 암호화됩니다. 자세한 내용은 [미사용 Azure 데이터 암호화](../../security/fundamentals/encryption-atrest.md)를 참조하세요.

## <a name="overview"></a>개요

Azure Policy는 해당 리소스의 속성을 비즈니스 규칙과 비교하여 Azure의 리소스를 평가합니다. [JSON 형식](./concepts/definition-structure.md)에서 설명하는 이러한 비즈니스 규칙을 [정책 정의](#policy-definition)라고 합니다. 관리를 간소화하기 위해 여러 비즈니스 규칙을 그룹화하여 [정책 이니셔티브](#initiative-definition)(_policySet_ 라고도 함)를 구성할 수 있습니다. 비즈니스 규칙이 구성되면 정책 정의 또는 이니셔티브가 Azure에서 지원하는 리소스의 범위(예: [관리 그룹](../management-groups/overview.md), 구독, [ 리소스 그룹](../../azure-resource-manager/management/overview.md#resource-groups) 또는 개별 리소스)에 [할당](#assignments)됩니다. 할당은 해당 할당의 [Resource Manager 범위](../../azure-resource-manager/management/overview.md#understand-scope) 내에 있는 모든 리소스에 적용됩니다. 필요한 경우 하위 범위를 제외할 수 있습니다. 자세한 내용은 [Azure Policy의 범위](./concepts/scope.md)를 참조하세요.

Azure Policy는 [JSON 형식](./concepts/definition-structure.md)을 사용하여 평가에서 리소스의 규정 준수 여부를 확인하는 데 사용하는 논리를 구성합니다. 정의에는 메타데이터와 정책 규칙이 포함됩니다. 정의된 규칙은 함수, 매개 변수, 논리 연산자, 조건 및 속성 [별칭](./concepts/definition-structure.md#aliases)을 사용하여 원하는 시나리오와 정확하게 일치시킬 수 있습니다. 정책 규칙은 할당 범위에서 평가되는 리소스를 결정합니다.

### <a name="understand-evaluation-outcomes"></a>평가 결과 이해

리소스는 리소스 수명 주기, 정책 할당 수명 주기 및 정기적인 지속적인 일반 규정 준수 평가 기간의 특정 시간에 평가됩니다. 리소스가 평가되는 시간 또는 이벤트는 다음과 같습니다.

- 리소스가 정책 할당을 통해 범위에서 만들어지거나 업데이트되거나 삭제됩니다.
- 정책 또는 이니셔티브는 범위에 새로 할당됩니다.
- 범위에 새로 할당된 정책 또는 이니셔티브는 업데이트됩니다.
- 표준 규정 준수 평가 주기 동안 24시간마다 한 번씩 수행됩니다.

정책 평가가 수행되는 시기와 방법에 대한 자세한 내용은 [평가 트리거](./how-to/get-compliance-data.md#evaluation-triggers)를 참조하세요.

### <a name="control-the-response-to-an-evaluation"></a>평가에 대한 응답 제어

비준수 리소스를 처리하는 비즈니스 규칙은 조직마다 크게 다릅니다. 조직에서 비규격 리소스에 대응하는 플랫폼을 원하는 방법에 대한 예는 다음과 같습니다.

- 리소스 변경 거부
- 리소스에 대한 변경 내용 기록
- 변경 전 리소스 변경
- 변경 후 리소스 변경
- 관련 규정 준수 리소스 배포

Azure Policy는 [효과](./concepts/effects.md)를 적용하여 이러한 비즈니스 응답 각각을 가능하게 합니다. 효과는 [정책 정의](./concepts/definition-structure.md)의 **정책 규칙** 부분에 설정됩니다.

### <a name="remediate-non-compliant-resources"></a>규정 비준수 리소스 수정

이러한 효과는 리소스를 만들거나 업데이트할 때 주로 리소스에 영향을 주지만, Azure Policy는 해당 리소스를 변경할 필요 없이 기존 비준수 리소스를 처리하도록 지원합니다. 기존 리소스에서 규정 준수하도록 만드는 방법에 대한 자세한 내용은 [리소스 수정](./how-to/remediate-resources.md)을 참조하세요.

### <a name="video-overview"></a>비디오 개요

Azure Policy의 다음 개요는 Build 2018에서부터 시작됩니다. 슬라이드 또는 비디오 다운로드의 경우 Channel 9에서 [Azure Policy를 통해 Azure 환경 제어](https://channel9.msdn.com/events/Build/2018/THR2030)를 방문하세요.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>시작

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy 및 Azure RBAC

Azure Policy 및 Azure RBAC(Azure 역할 기반 액세스 제어) 간에는 몇 가지 주요 차이점이 있습니다. Azure Policy는 Resource Manager에 표시되는 리소스의 속성 및 일부 리소스 공급자의 속성을 검사하여 상태를 평가합니다. Azure Policy는 작업(_동작_ 이라고도 함)을 제한하지 않습니다. Azure Policy를 사용하면 변경한 사용자 또는 변경 권한이 있는 사용자에 대한 걱정 없이 리소스 상태에서 비즈니스 규칙을 준수하는지 확인할 수 있습니다. [정책 정의](#policy-definition), [이니셔티브 정의](#initiative-definition) 및 [할당](#assignments)과 같은 일부 Azure Policy 리소스는 모든 사용자에게 표시됩니다. 이 디자인을 통해 해당 환경에 설정된 정책 규칙이 모든 사용자 및 서비스에 투명하게 표시됩니다.

Azure RBAC는 다양한 범위에서 사용자 [작업](../../role-based-access-control/resource-provider-operations.md)을 관리하는 데 중점을 둡니다. 작업을 제어해야 하는 경우 Azure RBAC가 사용하는 데 적합한 도구입니다. 개인에게 작업을 수행할 수 있는 액세스 권한이 있더라도 결과가 비준수 리소스이면 Azure Policy에서 만들기 또는 업데이트를 계속 차단합니다.

Azure RBAC와 Azure Policy의 조합은 Azure에서 전체 범위 제어를 제공합니다.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Azure Policy의 Azure RBAC 사용 권한

Azure Policy에는 여러 권한이 있는데, 다음 두 리소스 공급자에서는 작업으로 알려져 있습니다.

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

여러 기본 제공 역할은 Azure Policy 리소스에 대한 권한을 부여합니다. **리소스 정책 기여자** 역할은 대부분의 Azure Policy 작업을 포함합니다. **소유자** 는 전체 권한을 보유합니다. **기여자** 및 **읽기 권한자** 는 모두 _읽기_ Azure Policy 작업에 액세스할 수 있습니다. **기여자** 는 리소스 수정을 트리거할 수 있지만, 정의 또는 할당을 _만들_ 수는 없습니다. **사용자 액세스 관리자** 는 **deployIfNotExists** 에서 관리 ID를 부여하거나 할당에 필요한 권한을 **수정** 하는 데 필요합니다.

기본 제공 역할에 필수 권한이 없는 경우 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 만듭니다.

> [!NOTE]
> **deployIfNotExists** 또는 **수정** 정책 할당의 관리 ID에는 대상 리소스를 만들거나 업데이트할 수 있는 충분한 권한이 필요합니다. 자세한 내용은 [수정에 대한 정책 정의 구성](./how-to/remediate-resources.md#configure-policy-definition)을 참조하세요.

### <a name="resources-covered-by-azure-policy"></a>Azure Policy에서 처리하는 리소스

Azure Policy는 Arc 지원 리소스를 포함하여 구독 수준 이하의 모든 Azure 리소스를 평가합니다. [게스트 구성](./concepts/guest-configuration.md), [Azure Kubernetes Service](../../aks/intro-kubernetes.md), [Azure Key Vault](../../key-vault/general/overview.md)와 같은 특정 리소스 공급자의 경우 설정 및 개체 관리를 위해 더 긴밀하게 통합됩니다. 자세한 내용은 [리소스 공급자 모드](./concepts/definition-structure.md)를 참조하세요.

### <a name="recommendations-for-managing-policies"></a>정책 관리에 대한 권장 사항

다음은 유의해야 할 몇 가지 포인터 및 팁입니다.

- 정책 정의가 환경의 리소스에 미치는 영향을 추적하기 위해 거부 효과 대신 감사 효과로 시작합니다. 이미 애플리케이션 크기를 자동으로 조정하는 스크립트가 있는 경우 거부 효과를 설정하면 기존의 자동화 작업이 방해를 받을 수 있습니다.

- 정의 및 할당을 만들 때는 조직 계층 구조를 고려합니다. 관리 그룹 또는 구독 수준과 같은 상위 수준에서 정의를 만드는 것이 좋습니다. 그런 후에, 다음 하위 수준에서 할당을 만듭니다. 관리 그룹에서 정의를 만드는 경우 할당 범위를 해당 관리 그룹 내의 구독 또는 리소스 그룹으로 축소할 수 있습니다.

- 단일 정책 정의에 대해서도 이니셔티브 정의를 만들고 할당하는 것이 좋습니다.
  예를 들어 정책 정의 _policyDefA_ 가 있고, 이니셔티브 정의 _initiativeDefC_ 아래에 만듭니다. 나중에 _policyDefA_ 와 비슷한 목표로 _policyDefB_ 에 대한 다른 정책 정의를 만드는 경우 _initiativeDefC_ 아래에 추가하고 함께 추적할 수 있습니다.

- 이니셔티브 할당을 만들면 이니셔티브에 추가된 정책 정의도 해당 이니셔티브 할당의 일부가 됩니다.

- 이니셔티브 할당을 평가할 때 이니셔티브 내의 모든 정책도 평가됩니다.
  정책을 개별적으로 평가해야 하는 경우 이니셔티브에 포함하지 않는 것이 좋습니다.

## <a name="azure-policy-objects"></a>Azure Policy 개체

### <a name="policy-definition"></a>정책 정의

Azure Policy에서 정책을 만들고 구현하는 과정은 정책 정의 만들기로 시작합니다. 모든 정책 정의에는 정책이 적용되는 조건이 있습니다. 또한 조건이 충족되면 발생하는 정의된 효과가 있습니다.

Azure Policy에는 기본적으로 사용 가능한 여러 가지 기본 제공 정책이 있습니다. 예를 들면 다음과 같습니다.

- **허용되는 스토리지 계정 SKU**(거부): 배포 중인 스토리지 계정이 SKU 크기 세트 내에 있는지 여부를 확인합니다. 정의된 SKU 크기 세트를 준수하지 않는 모든 스토리지 계정을 거부하게 됩니다.
- **허용되는 리소스 종류**(거부): 배포할 수 있는 리소스 유형을 정의합니다. 이 정의된 목록에 속하지 않는 모든 리소스를 거부하게 됩니다.
- **허용되는 위치**(거부): 새 리소스를 사용할 수 있는 위치를 제한합니다. 해당 효과는 지역 규정 준수 요구 사항을 적용하는 데 사용됩니다.
- **허용되는 가상 머신 SKU**(거부): 배포할 수 있는 가상 머신 SKU 세트를 지정합니다.
- **리소스에 태그 추가**(수정): 배포 요청에 의해 지정되지 않은 경우 필수 태그 및 해당 기본값을 적용합니다.
- **허용되지 않는 리소스 종류**(거부): 리소스 종류 목록이 배포되지 않도록 합니다.

이러한 정책 정의(기본 제공 및 사용자 지정 정의)를 구현하려면 할당해야 합니다. Azure Portal, PowerShell 또는 Azure CLI를 통해 이러한 정책을 할당할 수 있습니다.

정책 평가는 정책 할당 또는 정책 업데이트와 같은 여러 가지 작업에서 수행됩니다. 전체 목록은 [정책 평가 트리거](./how-to/get-compliance-data.md#evaluation-triggers)를 참조하세요.

정책 정의의 구조에 대한 자세한 내용은 [정책 정의 구조](./concepts/definition-structure.md)를 확인하세요.

정책 매개 변수는 만들어야 하는 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 더 일반적인 정책 정의를 만들 때는 매개 변수를 정의할 수 있습니다. 그런 다음 다른 시나리오에 대해 해당 정책 정의를 다시 사용할 수 있습니다. 정책 정의를 할당할 때 다른 값을 전달하면 됩니다. 예를 들어 구독에 대해 하나의 위치 집합을 지정합니다.

매개 변수는 정책 정의를 만들 때 정의됩니다. 매개 변수를 정의할 때는 이름과, 필요에 따라 값이 지정됩니다. 예를 들어 _location_ 이라는 정책에 대한 매개 변수를 정의할 수 있습니다. 그런 다음 정책을 할당할 때 _EastUS_ 또는 _WestUS_ 와 같은 다른 값을 지정할 수 있습니다.

정책 매개 변수에 대한 자세한 내용은 [정의 구조 - 매개 변수](./concepts/definition-structure.md#parameters)를 참조하세요.

### <a name="initiative-definition"></a>이니셔티브 정의

이니셔티브 정의는 가장 중요한 단일 목표를 달성하기 위해 맞춤화된 정책 정의의 컬렉션입니다. 이니셔티브 정의를 사용하면 정책 정의 관리 및 할당을 더 쉽게 수행할 수 있습니다. 정책 집합을 단일 항목으로 그룹화하여 단순화합니다. 예를 들어 이름이 **Azure Security Center에서 모니터링 사용** 이면서 목표가 Azure Security Center의 모든 사용 가능한 보안 권장 사항을 모니터링하는 것인 이니셔티브를 만들 수 있습니다.

> [!NOTE]
> SDK(예: Azure CLI 및 Azure PowerShell)는 **PolicySet** 라는 속성과 매개 변수를 사용하여 이니셔티브를 참조합니다.

이 이니셔티브에 따라 다음과 같은 정책 정의가 있게 됩니다.

- **Security Center의 암호화되지 않은 SQL 데이터베이스 모니터링** - 암호화되지 않은 SQL 데이터베이스 및 서버 모니터링
- **Security Center의 OS 취약성 모니터링** – 구성된 기준을 충족하지 않는 서버 모니터링
- **Security Center의 누락된 엔드포인트 보호 모니터링** – 엔드포인트 보호 에이전트가 설치되지 않은 서버 모니터링

정책 매개 변수처럼 이니셔티브 매개 변수는 중복을 줄여 이니셔티브 관리를 간소화합니다. 이니셔티브 매개 변수는 이니셔티브 내의 정책 정의에 사용되는 매개 변수입니다.

예를 들어 각각 다른 형식의 매개 변수가 필요한 **policyA** 및 **policyB** 정책 정의를 사용하여 이니셔티브 정의 **initiativeC** 가 있는 시나리오를 살펴보겠습니다.

| 정책 | 매개 변수의 이름 |매개 변수 형식  |참고 |
|---|---|---|---|
| policyA | allowedLocations | array  |매개 변수 형식이 배열로 정의되었으므로 이 매개 변수는 값으로 문자열 목록을 예상합니다. |
| policyB | allowedSingleLocation |문자열 |매개 변수 형식이 문자열로 정의되었으므로 이 매개 변수는 값으로 한 단어를 예상합니다. |

이 시나리오에서는 **initiativeC** 에 대한 이니셔티브 매개 변수를 정의할 때 세 가지 옵션이 있습니다.

- 이 이니셔티브 내의 정책 정의 매개 변수 사용: 이 예제에서는 _allowedLocations_ 및 _allowedSingleLocation_ 이 **initiativeC** 에 대한 이니셔티브 매개 변수가 됩니다.
- 이 이니셔티브 정의 내에서 정책 정의의 매개 변수에 값 제공: 이 예에서는 위치 목록을 **policyA** 의 매개 변수(**allowedLocations**) 및 **policyB** 의 매개 변수(**allowedSingleLocation**)에 제공할 수 있습니다. 이 이니셔티브를 할당할 때 값을 제공할 수도 있습니다.
- 이 이니셔티브를 할당할 때 _값_ 옵션 목록 제공. 이 이니셔티브를 할당할 때 이니셔티브 내 정책 정의에서 상속된 매개 변수는 이 제공된 목록의 값만 가질 수 있습니다.

이니셔티브 정의에 값 옵션을 만드는 경우 이니셔티브 할당 중에 목록에 속하지 않는 다른 값을 입력할 수 없습니다.

이니셔티브 정의 구조에 대해 자세히 알아보려면 [이니셔티브 정의 구조](./concepts/initiative-definition-structure.md)를 검토하세요.

### <a name="assignments"></a>할당

할당은 특정 범위 내에서 수행하도록 할당된 정책 정의 또는 이니셔티브입니다. 이 범위는 [관리 그룹](../management-groups/overview.md)에서 개별 리소스에 이르기까지 다양할 수 있습니다. _범위_ 라는 용어는 정의가 할당된 모든 리소스, 리소스 그룹, 구독 또는 관리 그룹을 나타냅니다. 할당은 모든 자식 리소스에 상속됩니다. 이 설계는 리소스 그룹에 적용된 정의가 해당 리소스 그룹의 리소스에도 적용된다는 것을 의미합니다. 그러나 할당에서 하위 범위를 제외할 수 있습니다.

예를 들어 구독 범위에서 네트워킹 리소스를 만들지 못하도록 방지하는 정의를 할당할 수 있습니다. 해당 구독 내에서 네트워킹 인프라를 대상으로 하는 리소스 그룹을 제외할 수 있습니다. 그런 다음, 네트워킹 리소스 생성을 신뢰하는 사용자에게 이 네트워킹 리소스 그룹에 대한 액세스 권한을 부여합니다.

다른 예제에서는 관리 그룹 수준에서 리소스 종류 허용 목록 정의를 할당할 수 있습니다. 그런 다음, 더 많은 허용 정책(더 많은 리소스 종류 허용)을 자식 관리 그룹에 할당하거나 구독에 직접 할당합니다. 그러나 Azure Policy는 명시적 거부 시스템이므로 이 예는 작동하지 않습니다. 대신 관리 그룹 수준 할당에서 자식 관리 그룹 또는 구독을 제외해야 합니다. 그런 다음, 더 많은 허용 정의를 자식 관리 그룹 또는 구독 수준에 할당합니다. 할당으로 인해 리소스가 거부되는 경우 리소스를 허용하는 유일한 방법은 거부하는 할당을 수정하는 것입니다.

포털을 통해 할당을 설정하는 방법에 대한 자세한 내용은 [Azure 환경에서 비준수 리소스를 식별하는 정책 할당 만들기](./assign-policy-portal.md)를 참조하세요. [PowerShell](./assign-policy-powershell.md) 및 [Azure CLI](./assign-policy-azurecli.md)용 단계도 제공됩니다. 할당 구조에 대한 정보는 [할당 구조](./concepts/assignment-structure.md)를 참조하세요.

## <a name="maximum-count-of-azure-policy-objects"></a>Azure Policy 개체의 최대 수

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>다음 단계

이제 Azure Policy 개요와 몇 가지 핵심 개념을 살펴보았으므로 다음과 같은 단계를 권장합니다.

- [정책 정의 구조를 검토합니다](./concepts/definition-structure.md).
- [포털을 사용하여 정책 정의를 할당합니다](./assign-policy-portal.md).
