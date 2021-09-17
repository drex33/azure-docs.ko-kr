---
title: 일반적인 오류 문제 해결
description: 정책 정의 만들기, 다양한 SDK 및 Kubernetes에 대한 추가 항목에서 발생하는 문제를 해결하는 방법을 알아봅니다.
ms.date: 09/01/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0ab4319a7a0d515b51c8bbe259ad0ea49ed2b940
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433605"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Azure Policy를 사용한 오류 해결

정책 정의를 만들거나 SDK를 사용하거나 [Kubernetes에 대한 Azure Policy](../concepts/policy-for-kubernetes.md) 추가 기능을 설정하는 경우 오류가 발생할 수 있습니다. 이 문서에서는 발생 가능한 다양한 일반적 오류에 대해 설명하고 해당 오류를 해결하는 방법을 설명합니다.

## <a name="find-error-details"></a>오류 세부 정보 찾기

오류 세부 정보의 위치는 작업 중인 Azure Policy의 양상에 따라 다릅니다.

- 사용자 지정 정책을 사용하여 작업하는 경우 Azure Portal로 이동하여 스키마에 대한 린팅 피드백을 얻거나 결과로 도출된 [준수 데이터](../how-to/get-compliance-data.md)를 검토하여 리소스 평가 방법을 확인합니다.
- 다양한 SDK 중 하나를 사용하여 작업하는 경우 SDK는 함수가 실패한 이유에 대한 세부 정보를 제공합니다.
- Kubernetes에 대한 추가 기능을 사용하여 작업하는 경우 클러스터의 [로깅](../concepts/policy-for-kubernetes.md#logging)을 시작합니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-alias-not-found"></a>시나리오: 별칭을 찾을 수 없음

#### <a name="issue"></a>문제

정책 정의에 잘못되었거나 존재하지 않는 별칭이 사용되었습니다. Azure Policy는 [별칭](../concepts/definition-structure.md#aliases)을 사용하여 Azure Resource Manager 속성에 매핑합니다.

#### <a name="cause"></a>원인

정책 정의에 잘못되었거나 존재하지 않는 별칭이 사용되었습니다.

#### <a name="resolution"></a>해결 방법

먼저 Resource Manager 속성에 별칭이 있는지 확인합니다. 사용 가능한 별칭을 조회하려면 [Visual Studio Code에 대한 Azure Policy 확장](../how-to/extension-for-vscode.md) 또는 SDK로 이동합니다.
Resource Manager 속성에 대한 별칭이 존재하지 않으면 지원 티켓을 만듭니다.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>시나리오: 평가 세부 정보가 최신 상태가 아님

#### <a name="issue"></a>문제

리소스가 _시작 안 됨_ 상태이거나 준수 세부 정보가 최신이 아닙니다.

#### <a name="cause"></a>원인

새 정책 또는 이니셔티브 할당을 적용하는 데에는 약 30분이 소요됩니다. 기존 할당 범위 내에서 새로운 리소스 또는 업데이트된 리소스가 사용할 수 있게 되는 데에는 약 15분이 소요됩니다. 표준 준수 검사는 24시간마다 수행됩니다. 자세한 내용은 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)를 참조하세요.

#### <a name="resolution"></a>해결 방법

먼저 평가가 끝나고 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 될 때까지 적정 시간 기다립니다. Azure PowerShell 또는 REST API를 사용하여 새 평가 검사를 시작하려면 [온디맨드형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조하세요.

### <a name="scenario-compliance-isnt-as-expected"></a>시나리오: 예상대로 준수하지 않음

#### <a name="issue"></a>문제

리소스가 리소스에 대해 예상되는 _준수_ 또는 _미준수_ 평가 상태에 있지 않습니다.

#### <a name="cause"></a>원인

리소스가 정책 할당에 대한 올바른 범위에 속하지 않거나 정책 정의가 의도한 대로 작동하지 않습니다.

#### <a name="resolution"></a>해결 방법

정책 정의 문제를 해결하려면 다음을 수행합니다.

1. 먼저 평가가 끝나고 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 될 때까지 적정 시간을 기다립니다.

1. Azure PowerShell 또는 REST API를 사용하여 새 평가 검사를 시작하려면 [온디맨드형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조하세요.
1. 할당 매개 변수와 할당 범위가 올바르게 설정되어 있는지 확인하세요.
1. [정책 정의 모드](../concepts/definition-structure.md#mode)를 확인합니다.
   - 모든 리소스 종류에 대한 모드는 `all`이어야 합니다.
   - 정책 정의가 태그나 위치를 확인하는 경우 모드는 `indexed`이어야 합니다.
1. 리소스의 범위가 [제외되거나](../concepts/assignment-structure.md#excluded-scopes) [면제되지](../concepts/exemption-structure.md) 않았는지 확인합니다.
1. 정책 할당에 대한 준수에서 `0/0` 리소스를 표시하는 경우 할당 범위 내에서 적용 가능한 리소스가 결정되지 않은 것입니다. 정책 정의와 할당 범위를 모두 확인합니다.
1. 준수될 것이라 예상되는 리소스가 미준수 리소스로 나타나는 경우 [미준수 이유 확인](../how-to/determine-non-compliance.md)을 참조하세요. 정의를 평가된 속성 값과 비교하면 리소스가 미준수로 나타나는 이유를 알 수 있습니다.
   - **대상 값** 이 잘못된 경우 정책 정의를 수정하세요.
   - **현재 값** 이 잘못된 경우 `resources.azure.com`을 통해 리소스 페이로드의 유효성을 검사합니다.
1. RegEx 문자열 매개 변수(예: 및 기본 제공 정의 "컨테이너 이미지는 신뢰할 수 있는 레지스트리에서만 배포되어야 합니다")를 지원하는 [리소스 공급자 모드](../concepts/definition-structure.md#resource-provider-modes) 정의의 경우 `Microsoft.Kubernetes.Data` [RegEx 문자열](/dotnet/standard/base-types/regular-expression-language-quick-reference) 매개 변수가 올바른지 확인합니다.
1. 다른 일반적인 문제 및 솔루션에 대해서는 [문제 해결: 예상대로 적용되지 않음](#scenario-enforcement-not-as-expected)을 참조하세요.

중복되는 사용자 지정 기본 제공 정책 정의 또는 사용자 지정 정의에 문제가 계속 발생하는 경우 **정책 작성** 에서 지원 티켓을 만들어 문제를 올바르게 라우팅합니다.

### <a name="scenario-enforcement-not-as-expected"></a>시나리오: 예상대로 적용되지 않음

#### <a name="issue"></a>문제

Azure Policy가 작업을 수행할 것으로 예상했던 리소스가 작업을 수행하지 않으며 [Azure 활동 로그](../../../azure-monitor/essentials/platform-logs-overview.md)에 항목이 없습니다.

#### <a name="cause"></a>원인

_사용 안 함_ 의 [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) 설정에 대해 정책 할당이 구성되어 있지 않습니다.
**enforcementMode** 가 사용하지 않도록 설정되어 있는 동안 정책 효과는 적용되지 않으며 활동 로그에도 항목이 없습니다.

#### <a name="resolution"></a>해결 방법

다음을 수행하여 정책 할당 적용 문제를 해결합니다.

1. 먼저 평가가 끝나고 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 될 때까지 적정 시간을 기다립니다.

1. Azure PowerShell 또는 REST API를 사용하여 새 평가 검사를 시작하려면 [온디맨드형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조하세요.
1. 할당 매개 변수 및 할당 범위를 올바르게 설정하고 **enforcementMode** 를 _사용_ 하도록 설정했는지 확인하세요.
1. [정책 정의 모드](../concepts/definition-structure.md#mode)를 확인합니다.
   - 모든 리소스 종류에 대한 모드는 `all`이어야 합니다.
   - 정책 정의가 태그나 위치를 확인하는 경우 모드는 `indexed`이어야 합니다.
1. 리소스의 범위가 [제외되거나](../concepts/assignment-structure.md#excluded-scopes) [면제되지](../concepts/exemption-structure.md) 않았는지 확인합니다.
1. 리소스 페이로드가 정책 논리와 일치하는지 확인합니다. [HAR(HTTP Archive) 추적을 캡처](../../../azure-portal/capture-browser-trace.md)하거나 ARM 템플릿(Azure Resource Manager 템플릿) 속성을 검토하여 해당 작업을 수행할 수 있습니다.
1. 다른 일반적인 문제 및 솔루션에 대해서는 [문제 해결: 예상대로 준수하지 않음](#scenario-compliance-isnt-as-expected)을 참조하세요.

중복되는 사용자 지정 기본 제공 정책 정의 또는 사용자 지정 정의에 문제가 계속 발생하는 경우 **정책 작성** 에서 지원 티켓을 만들어 문제를 올바르게 라우팅합니다.

### <a name="scenario-denied-by-azure-policy"></a>시나리오: Azure Policy에 의해 거부됨

#### <a name="issue"></a>문제

리소스 만들기 또는 업데이트가 거부되었습니다.

#### <a name="cause"></a>원인

새 리소스 또는 업데이트된 리소스 범위에 대한 정책 할당이 [거부](../concepts/effects.md#deny) 효과가 있는 정책 정의의 기준을 충족합니다. 해당 정의를 충족하는 리소스는 만들거나 업데이트할 수 없습니다.

#### <a name="resolution"></a>해결 방법

거부 정책 할당의 오류 메시지에는 정책 정의 및 정책 할당 ID가 포함됩니다. 메시지의 오류 정보가 누락된 경우에도 [활동 로그](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)에서 해당 정보를 사용할 수 있습니다. 해당 정보를 사용하여 리소스 제한을 이해하고 요청에서 리소스 속성을 허용되는 값과 일치하도록 조정하는 방법에 대한 세부 정보를 확인하세요.

### <a name="scenario-definition-targets-multiple-resource-types"></a>시나리오: 정의는 여러 리소스 종류를 대상으로 함

#### <a name="issue"></a>문제

여러 리소스 종류를 포함하는 정책 정의는 생성 또는 업데이트 중에 다음 오류로 인해 유효성 검사에 실패합니다.

```error
The policy definition '{0}' targets multiple resource types, but the policy rule is authored in a way that makes the policy not applicable to the target resource types '{1}'.
```

#### <a name="cause"></a>원인

정책 정의 규칙에 대상 리소스 종류에 의해 평가되지 않는 하나 이상의 조건이 있습니다.

#### <a name="resolution"></a>해결 방법

별칭을 사용하는 경우 그 앞에 형식 조건을 추가하여 별칭이 속한 리소스 종류에 대해서만 별칭이 평가되는지 확인합니다. 대안은 정책 정의를 여러 정의로 분할하여 여러 리소스 종류를 대상으로 지정하지 않도록 하는 것입니다.

### <a name="scenario-subscription-limit-exceeded"></a>시나리오: 구독 제한 초과

#### <a name="issue"></a>문제

정책 할당에 대한 규정 준수를 검색할 때 Azure Portal의 규정 준수 페이지에 오류 메시지가 표시됩니다.

#### <a name="cause"></a>원인

요청에서 선택한 범위 아래의 구독 수가 5,000개 구독 제한을 초과했습니다. 규정 준수 결과가 부분적으로 표시될 수 있습니다.

#### <a name="resolution"></a>해결 방법

자식 구독 수가 적은 보다 세부적인 범위를 선택하여 전체 결과를 확인합니다.

## <a name="template-errors"></a>템플릿 오류

### <a name="scenario-policy-supported-functions-processed-by-template"></a>시나리오: 정책 지원 함수가 템플릿에서 처리됨

#### <a name="issue"></a>문제

Azure Policy는 정책 정의에서만 사용할 수 있는 여러 ARM 템플릿 함수 및 함수를 지원합니다. Resource Manager는 해당 기능을 정책 정의의 일부가 아닌 배포의 일부로서 처리합니다.

#### <a name="cause"></a>원인

`parameter()` 또는 `resourceGroup()`과 같은 지원되는 함수를 사용하면 정책 정의 및 Azure Policy 엔진에서 처리하는 함수를 허용하는 대신 배포 시간에 함수의 처리된 결과가 발생합니다.

#### <a name="resolution"></a>해결 방법

함수를 정책 정의의 일부로 전달하려면 `[`을 사용하여 전체 문자열을 이스케이프 처리하여 속성이 `[[resourceGroup().tags.myTag]`와 같이 표시되도록 합니다. 이스케이프 문자를 사용하면 Resource Manager가 템플릿을 처리할 때 값을 문자열로 취급합니다. 그런 다음 Azure Policy는 함수를 정책 정의에 배치하여 예상대로 동적이 될 수 있도록 합니다. 자세한 내용은 [Azure Resource Manager 템플릿의 구문 및 식](../../../azure-resource-manager/templates/template-expressions.md)을 참조하세요.

## <a name="add-on-for-kubernetes-installation-errors"></a>Kubernetes 설치 오류에 대한 추가 기능

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>시나리오: 암호 오류로 인해 Helm 차트를 사용하여 설치하지 못함

#### <a name="issue"></a>문제

`helm install azure-policy-addon` 명령이 실패하고 다음 오류 중 하나를 반환합니다.

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>원인

생성된 암호에는 Helm 차트가 분할 중인 쉼표(`,`)가 포함되어 있습니다.

#### <a name="resolution"></a>해결 방법

`helm install azure-policy-addon`을 실행할 때 암호 값에서 쉼표(`,`)를 백슬래시(`\`)로 이스케이프 처리합니다.

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>시나리오: 이름이 이미 존재하여 Helm 차트를 사용하여 설치하지 못함

#### <a name="issue"></a>문제

`helm install azure-policy-addon` 명령이 실패하고 다음과 같은 오류를 반환합니다.

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>원인

이름이 `azure-policy-addon`인 Helm 차트가 이미 설치되었거나 부분적으로 설치되었습니다.

#### <a name="resolution"></a>해결 방법

지침에 따라 [Kubernetes 추가 기능에 대한 Azure Policy를 제거](../concepts/policy-for-kubernetes.md#remove-the-add-on)한 후 `helm install azure-policy-addon` 명령을 다시 실행합니다.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>시나리오: Azure 가상 머신 사용자 할당 ID가 시스템 할당 관리 ID로 대체됨

#### <a name="issue"></a>문제

머신 내부에서 감사 설정에 게스트 구성 정책 이니셔티브를 할당한 후에는 머신에 할당된 사용자 할당 관리 ID가 더 이상 할당되지 않습니다. 시스템 할당 관리 ID만 할당됩니다.

#### <a name="cause"></a>원인

게스트 구성 DeployIfNotExists 정의에 이전에 사용되었던 적이 있는 정책 정의는 시스템 할당 ID가 머신에 할당되었음을 확인하는 동시에 사용자 할당 ID 할당을 제거합니다.

#### <a name="resolution"></a>해결 방법

이전에 이미 해당 문제를 일으킨 정의는 더 이상 _\[사용되지 않는\]_ 것으로 표시되며 사용자 할당 관리 ID를 제거하지 않고 필수 조건을 관리하는 정책 정의로 대체됩니다. 수동 단계가 필요합니다. _\[더 이상 사용되지 않는\]_ 것으로 표시된 기존 정책 할당을 삭제하고 원래와 동일한 이름을 가진 업데이트된 필수 조건 정책 이니셔티브 및 정책 정의로 대체합니다.

자세한 내용은 블로그 게시물 [게스트 구성 감사 정책에 대해 릴리스된 중요 변경 내용](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)을 참조하세요.

## <a name="add-on-for-kubernetes-general-errors"></a>Kubernetes 일반 오류에 대한 추가 기능

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>시나리오: 송신 제한으로 인해 추가 기능이 Azure Policy 서비스 엔드포인트에 도달할 수 없음

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 엔드포인트에 도달할 수 없으며 다음 오류 중 하나가 반환됩니다.

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>원인

해당 문제는 클러스터 송신이 잠겨 있을 경우 발생합니다.

#### <a name="resolution"></a>해결 방법

다음 문서에서 설명하는 도메인 및 포트가 열려 있는지 확인합니다.

- [AKS 클러스터에 대한 필수 아웃 바운드 네트워크 규칙 및 FQDN](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)(정규화된 도메인 이름)
- [Azure Arc 사용 Kubernetes(미리 보기)에 대한 Azure Policy 추가 기능 설치](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>시나리오: add-pod-identity 구성으로 인해 추가 기능이 Azure Policy 서비스 엔드포인트에 도달할 수 없음

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 엔드포인트에 도달할 수 없으며 다음 오류 중 하나가 반환됩니다.

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>원인

해당 오류는 클러스터에 _add-pod-identity_ 가 설치되어 있고 _add-pod-identity_ 에서 _kube 시스템_ Pod가 제외되지 않은 경우 발생합니다.

_add-pod-identity_ 구성 요소 NMI(Node Managed Identity) Pod는 Azure 인스턴스 메타데이터 엔드포인트에 대한 호출을 가로채는 노드의 iptables를 수정합니다. 해당 설정은 Pod에서 _add-pod-identity_ 를 사용하지 않는 경우에도 메타데이터 엔드포인트에 대한 모든 요청이 NMI에 의해 차단됨을 의미합니다.
_AzurePodIdentityException_ CRD(CustomResourceDefinition)은 _add-pod-identity_ 를 알리도록 구성하여 CRD에 정의된 레이블과 일치하는 Pod에서 유래하는 메타데이터 엔드포인트에 대한 모든 요청이 NMI를 처리하지 않고 프록시되도록 할 수 있습니다.

#### <a name="resolution"></a>해결 방법

_AzurePodIdentityException_ CRD를 구성하여 _add-pod-identity_ 의 _kube 시스템_ 네임스페이스에서 `kubernetes.azure.com/managedby: aks` 레이블이 있는 시스템 Pod를 제외합니다.

자세한 내용은 [특정 Pod/애플리케이션에 대한 Azure AD(Azure Active Directory) Pod ID 사용 안 함](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)을 참조하세요.

예외를 구성하려면 다음 예제를 따르세요.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>시나리오: 리소스 공급자가 등록되지 않음

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 엔드포인트에 도달할 수 있지만 추가 기능 로그에서 다음 오류 중 하나를 표시합니다.

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>원인

‘Microsoft.PolicyInsights’ 리소스 공급자가 등록되지 않았습니다. 정책 정의를 가져오고 준수 데이터를 반환하려면 추가 기능에 리소스 공급자가 등록되어 있어야 합니다.

#### <a name="resolution"></a>해결 방법

클러스터 구독에서 ‘Microsoft.PolicyInsights’ 리소스 공급자를 등록합니다. 자세한 내용은 [리소스 공급자 등록](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)을 참조하세요.

### <a name="scenario-the-subscription-is-disabled"></a>시나리오: 구독을 사용하지 않도록 설정되어 있음

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 엔드포인트에 도달할 수 있지만 다음과 같은 오류를 표시합니다.

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>원인

해당 오류는 구독이 문제를 일으키는 것으로 확인되어 기능 플래그 `Microsoft.PolicyInsights/DataPlaneBlocked`가 추가된 것으로 인해 구독이 차단되었음을 의미합니다.

#### <a name="resolution"></a>해결 방법

해당 문제를 조사하고 해결하려면 [기능 팀에 문의](mailto:azuredg@microsoft.com)하세요.

### <a name="scenario-definitions-in-category-guest-configuration-cannot-be-duplicated-from-azure-portal"></a>시나리오: "게스트 구성" 범주의 정의는 Azure Portal에서 중복 될 수 없습니다.

#### <a name="issue"></a>문제

정책 정의에 대 한 Azure Portal 페이지에서 사용자 지정 정책 정의를 만들려고 할 때 "중복 된 정의" 단추를 선택 합니다. 정책을 할당 한 후에는 게스트 구성 할당 리소스가 없으므로 컴퓨터가 _비규격_ 상태임을 확인할 수 있습니다.

#### <a name="cause"></a>원인

게스트 구성은 게스트 구성 할당 리소스를 만들 때 정책 정의에 추가 된 사용자 지정 메타 데이터에 의존 합니다. Azure Portal의 "중복 정의" 작업은 사용자 지정 메타 데이터를 복사 하지 않습니다.

#### <a name="resolution"></a>해결 방법

포털을 사용 하는 대신 정책 Insights API를 사용 하 여 정책 정의를 복제 합니다. 다음 PowerShell 샘플에서는 옵션을 제공 합니다.

```powershell
# duplicates the built-in policy which audits Windows machines for pending reboots
$def = Get-AzPolicyDefinition -id '/providers/Microsoft.Authorization/policyDefinitions/4221adbc-5c0f-474f-88b7-037a99e6114c' | % Properties
New-AzPolicyDefinition -name (new-guid).guid -DisplayName "$($def.DisplayName) (Copy)" -Description $def.Description -Metadata ($def.Metadata | convertto-json) -Parameter ($def.Parameters | convertto-json) -Policy ($def.PolicyRule | convertto-json -depth 15)
```

## <a name="next-steps"></a>다음 단계

해당 문서에 나열되어 있지 않거나 해결할 수 없는 문제의 경우 다음 채널 중 하나를 방문하여 지원을 받으세요.

- [Microsoft Q&A](/answers/topics/azure-policy.html)를 통해 전문가의 답변을 받으세요.
- [@AzureSupport](https://twitter.com/azuresupport)에 연결합니다. Twitter의 해당 공식 Microsoft Azure 리소스는 Azure 커뮤니티를 올바른 답변, 지원 및 전문가에게 연결하여 고객 환경을 개선하는 데 도움이 됩니다.
- 여전히 도움이 필요한 경우 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 요청 제출** 을 선택합니다.
