---
title: Kubernetes용 Azure Policy 알아보기
description: Azure Policy에서 Rego 및 Open Policy Agent를 사용하여 Azure 또는 온-프레미스에서 Kubernetes를 실행하는 클러스터를 관리하는 방법을 알아봅니다.
ms.date: 09/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b5e010ec6f024838a0407f2cafae1d28bdcf1e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436071"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Kubernetes 클러스터에 대한 Azure Policy 이해

Azure Policy는 OPA([Open Policy Agent](https://www.openpolicyagent.org/))에 대한 ‘허용 컨트롤러 웹후크’인 [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3을 확장하여 중앙 집중식의 일관된 방식으로 클러스터에 대규모 적용 및 보호 기능을 적용합니다. Azure Policy를 사용하면 한 곳에서 Kubernetes 클러스터의 준수 상태를 관리하고 보고할 수 있습니다. 추가 기능은 다음 함수를 적용합니다.

- Azure Policy 서비스를 사용하여 클러스터에 대한 정책 할당을 확인합니다.
- [제약 조건 템플릿](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) 및 [제약 조건](https://github.com/open-policy-agent/gatekeeper#constraints) 사용자 지정 리소스로 클러스터에 정책 정의를 배포합니다.
- 감사 및 규정 준수 세부 정보를 다시 Azure Policy 서비스에 보고

Kubernetes용 Azure Policy는 다음 클러스터 환경을 지원합니다.

- [AKS(Azure Kubernetes Service)](../../../aks/intro-kubernetes.md)
- [Azure Arc 지원 Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS 엔진](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> AKS 엔진과 Arc 지원 Kubernetes의 추가 기능은 **미리 보기** 로 제공됩니다. Kubernetes에 대 한 Azure Policy는 Linux 노드 풀 및 기본 제공 정책 정의만 지원 합니다 (사용자 지정 정책 정의는 _공개 미리 보기_ 기능). 기본 제공 정책 정의는 **Kubernetes** 범주에 있습니다. **EnforceOPAConstraint** 및 **EnforceRegoPolicy** 효과 및 관련 **Kubernetes 서비스** 범주가 포함된, 제한된 미리 보기 정책 정의는 _더 이상 사용되지 않습니다_.
> 대신 `Microsoft.Kubernetes.Data` 리소스 공급자 모드를 통해 _감사_ 및 _거부_ 효과를 사용합니다.

## <a name="overview"></a>개요

Kubernetes 클러스터에서 Azure Policy를 사용하도록 설정하고 사용하려면 다음 작업을 수행합니다.

1. Kubernetes 클러스터를 구성하고 추가 기능을 설치합니다.
   - [AKS(Azure Kubernetes Service)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc 지원 Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS 엔진](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > 설치와 관련된 일반적인 문제는 [문제 해결 - Azure Policy 추가 기능](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors)을 참조하세요.

1. [Kubernetes용 Azure Policy 언어 이해](#policy-language)

1. [Kubernetes 클러스터에 정의 할당](#assign-a-policy-definition)

1. [유효성 검사 대기](#policy-evaluation)

## <a name="limitations"></a>제한 사항

다음의 일반 제한 사항은 Kubernetes 클러스터용 Azure Policy 추가 기능에 적용됩니다.

- Kubernetes용 Azure Policy 추가 기능은 Kubernetes 버전 **1.14** 이상에서 지원됩니다.
- Kubernetes 용 Azure Policy 추가 기능을 Linux 노드 풀에만 배포할 수 있습니다.
- 기본 제공 정책 정의만 지원 됩니다. 사용자 지정 정책 정의는 _공개 미리 보기_ 기능입니다.
- Azure Policy 추가 기능에서 지 원하는 최대 pod 수: **1만**
- 클러스터별 정책당 최대 비호환 레코드 수: **500** 개
- 구독당 최대 비호환 레코드 수: **100만** 개
- Azure Policy 추가 기능 외의 Gatekeeper 설치는 지원되지 않습니다. Azure Policy 추가 기능을 사용하도록 설정하기 전에 이전 Gatekeeper 설치를 통해 설치된 모든 구성 요소를 제거합니다.
- `Microsoft.Kubernetes.Data`
  [리소스 공급자 모드](./definition-structure.md#resource-provider-modes)에서는 [비준수 이유](../how-to/determine-non-compliance.md#compliance-reasons)를 사용할 수 없습니다. [구성 요소 세부 정보](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)를 사용합니다.
- [리소스 공급자 모드](./definition-structure.md#resource-provider-modes)에서는 [제외](./exemption-structure.md)가 지원되지 않습니다.

다음 제한 사항은 AKS용 Azure Policy 추가 기능에만 적용됩니다.

- [AKS Pod 보안 정책](../../../aks/use-pod-security-policies.md) 및 AKS용 Azure Policy 추가 기능을 모두 사용하도록 설정할 수 없습니다. 자세한 내용은 [AKS Pod 보안 제한](../../../aks/use-azure-policy.md)을 참조하세요.
- 평가 시 _kube-system_, _gatekeeper-system_, _aks-periscope_ 네임스페이스는 Azure Policy 추가 기능에서 자동으로 제외됩니다.

## <a name="recommendations"></a>권장 사항

다음은 Azure Policy 추가 기능 사용 시 일반적으로 권장되는 사항입니다.

- Azure Policy 추가 기능을 실행하려면 Gatekeeper 구성 요소가 3개(감사 Pod 1개, 웹후크 Pod 복제본 2개) 필요합니다. 이러한 구성 요소는 클러스터에서 Kubernetes 리소스 및 정책 할당 수가 늘어남에 따라 리소스를 더 많이 사용하므로, 감사 및 적용 작업이 필요합니다.

  - 제약 조건이 최대 20개 있는 단일 클러스터의 Pod가 500개보다 적은 경우: vCPU 2개와 구성 요소별 메모리 350MB
  - 제약 조건이 최대 40개 있는 단일 클러스터의 Pod가 500개보다 많은 경우: vCPU 3개와 구성 요소별 메모리 600MB

- Windows Pod는 [보안 컨텍스트를 지원하지 않습니다](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  따라서 루트 권한 허용 안 함 같은 일부 Azure Policy 정의는 Windows Pod에서 에스컬레이션될 수 없으며, Linux Pod에만 적용됩니다.

다음 권장 사항은 AKS 및 Azure Policy 추가 기능에만 적용됩니다.

- `CriticalAddonsOnly` taint와 함께 시스템 노드 풀을 사용하여 Gatekeeper Pod를 예약합니다. 자세한 내용은 [시스템 노드 풀 사용](../../../aks/use-system-pools.md#system-and-user-node-pools)을 참조하세요.
- AKS 클러스터에서 아웃바운드 트래픽을 보호합니다. 자세한 내용은 [클러스터 노드의 송신 트래픽 제어](../../../aks/limit-egress-traffic.md)를 참조하세요.
- 클러스터에 `aad-pod-identity`이(가) 사용하도록 설정된 경우 NMI(Node Managed Identity) Pod는 Azure Instance Metadata 엔드포인트에 대한 호출을 가로채는 노드의 iptables를 수정합니다. 이 구성은 Pod가 `aad-pod-identity`을(를) 사용하지 않는 경우에도 Metadata 엔드포인트에 대한 모든 요청을 NMI가 가로챈다는 의미입니다. AzurePodIdentityException CRD는 CRD에 정의된 레이블과 일치하는 Pod에서 Metadata 엔드포인트로 전송되는 모든 요청을 NMI에서 처리하지 않고 프록시되어야 함을 `aad-pod-identity`에 알리도록 구성할 수 있습니다. AzurePodIdentityException CRD를 구성하여 _kube-system_ 네임스페이스에 `kubernetes.azure.com/managedby: aks` 레이블이 지정된 시스템 Pod를 `aad-pod-identity`에서 제외해야 합니다. 자세한 내용은 [특정 Pod 또는 애플리케이션에 대한 aad-pod-identity 사용 안 함](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)을 참조하세요.
  예외를 구성하려면 [mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)을 설치합니다.

## <a name="install-azure-policy-add-on-for-aks"></a>AKS에 대한 Azure Policy 추가 기능 설치

Azure Policy 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 해당 구독에서 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정해야 합니다.

1. Azure CLI 버전 2.12.0 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 리소스 공급자 및 미리 보기 기능을 등록합니다.

   - Azure Portal:

     **Microsoft.PolicyInsights** 리소스 공급자를 등록합니다. 단계는 [리소스 공급자 및 형식](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요.

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. 제한된 미리 보기 정책 정의를 설치한 경우 **정책** 페이지에서 AKS 클러스터의 **사용 안 함** 단추로 추가 기능을 제거합니다.

1. AKS 클러스터 버전은 _1.14_ 이상이어야 합니다. 다음 스크립트를 사용하여 AKS 클러스터 버전의 유효성을 검사합니다.

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Azure CLI 버전 _2.12.0_ 이상을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

위의 필수 구성 요소 단계가 완료되면 관리하려는 AKS 클러스터에 Azure Policy 추가 기능을 설치합니다.

- Azure portal

  1. **모든 서비스** 를 선택한 다음 **Kubernetes 서비스** 를 검색 및 선택하여 Azure Portal에서 AKS 서비스를 시작합니다.

  1. AKS 클러스터 중 하나를 선택합니다.

  1. Kubernetes 서비스 페이지의 왼쪽에서 **정책** 을 선택합니다.

  1. 기본 페이지에서 **추가 기능 사용** 단추를 선택합니다.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

추가 기능 설치가 성공적이고 해당 _azure-policy_ 및 _gatekeeper_ Pod가 실행 중인지 유효성을 검사하려면 다음 명령을 실행합니다.

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

마지막으로 Azure CLI 명령을 실행하여 최신 추가 기능이 설치되어 있는지 확인하고 `<rg>`를 리소스 그룹 이름으로, `<cluster-name>`을 AKS 클러스터의 이름 `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>`으로 바꿉니다. 결과는 다음 출력과 유사하게 나타납니다.

```output
{
        "config": null,
        "enabled": true,
        "identity": null
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Azure Arc 사용 Kubernetes용 Azure Policy 추가 기능 설치(미리 보기)

Azure Policy 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 해당 구독은 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정하고 클러스터 서비스 주체에 대한 역할 할당을 만들어야 합니다.

1. Azure CLI 버전 2.12.0 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 리소스 공급자를 사용하도록 설정하려면 [리소스 공급자 및 유형](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 단계를 따르거나 Azure CLI 또는 Azure PowerShell 명령을 실행합니다.

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Kubernetes 클러스터는 버전 _1.14_ 이상이어야 합니다.

1. [Helm 3](https://v3.helm.sh/docs/intro/install/)을 설치합니다.

1. Azure Arc에 대해 Kubernetes 클러스터를 사용하도록 설정했습니다. 자세한 내용은 [Azure Arc에 Kubernetes 클러스터 온보딩](../../../azure-arc/kubernetes/quickstart-connect-cluster.md)을 참조하세요.

1. Azure Arc 지원 Kubernetes 클러스터의 정규화된 Azure 리소스 ID가 있어야 합니다.

1. 추가 기능에 대한 포트를 엽니다. Azure Policy 추가 기능은 이러한 도메인과 포트를 사용하여 정책 정의 및 할당을 가져오고 클러스터의 규정 준수를 Azure Policy에 다시 보고합니다.

   |도메인 |포트 |
   |---|---|
   |`data.policy.core.windows.net` |`443` |
   |`store.policy.core.windows.net` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. ‘Policy Insights Data Writer(미리 보기)’ 역할 할당을 Azure Arc 지원 Kubernetes 클러스터에 할당합니다. `<subscriptionId>`를 구독 ID로, `<rg>`를 Azure Arc 지원 Kubernetes 클러스터의 리소스 그룹으로, `<clusterName>`을 Azure Arc 지원 Kubernetes 클러스터 이름으로 바꿉니다. 설치 단계에서 _appId_, _password_ 및 _tenant_ 에 대해 반환된 값을 추적합니다.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   위 명령 출력의 샘플은 다음과 같습니다.

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

위의 필수 구성 요소 단계가 완료되면 Azure Arc 지원 Kubernetes 클러스터에 Azure Policy 추가 기능을 설치합니다.

1. Helm에 Azure Policy 추가 기능 리포지토리를 추가합니다.

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Helm 차트를 사용하여 Azure Policy 추가 기능을 설치합니다.

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   추가 기능 Helm 차트 설치에 대한 자세한 내용은 GitHub에서 [Azure Policy 추가 기능 Helm 차트 정의](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters)를 참조하세요.

추가 기능 설치가 성공적이고 해당 _azure-policy_ 및 _gatekeeper_ Pod가 실행 중인지 유효성을 검사하려면 다음 명령을 실행합니다.

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>AKS 엔진용 Azure Policy 추가 기능 설치(미리 보기)

Azure Policy 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 해당 구독은 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정하고 클러스터 서비스 주체에 대한 역할 할당을 만들어야 합니다.

1. Azure CLI 버전 2.0.62 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 리소스 공급자를 사용하도록 설정하려면 [리소스 공급자 및 유형](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 단계를 따르거나 Azure CLI 또는 Azure PowerShell 명령을 실행합니다.

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. 클러스터 서비스 주체에 대한 역할 할당을 만듭니다.

   - 클러스터 서비스 주체 앱 ID를 모르는 경우 다음 명령을 사용하여 조회합니다.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLI를 사용하여 ‘Policy Insights Data Writer(미리 보기)’ 역할 할당을 클러스터 서비스 주체 앱 ID(이전 단계의 _aadClientID_ 값)에 할당합니다. `<subscriptionId>`를 구독 ID로 바꾸고 `<aks engine cluster resource group>`은 AKS 엔진 자체 관리형 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉니다.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

위의 필수 구성 요소 단계가 완료되면 Azure Policy 추가 기능을 설치합니다. 설치는 AKS 엔진의 만들기 또는 업데이트 주기 동안 또는 기존 클러스터에 대한 독립된 작업으로 수행할 수 있습니다.

- 만들기 또는 업데이트 주기 중에 설치

  자체 관리형 클러스터를 새로 만드는 동안 또는 기존 클러스터에 대한 업데이트로 Azure Policy 추가 기능을 사용하도록 설정하려면 AKS 엔진에 대한 [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) 속성 클러스터 정의를 포함합니다.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  자세한 내용은 외부 가이드인 [AKS 엔진 클러스터 정의](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)를 참조하세요.

- Helm 차트를 사용하여 기존 클러스터에 설치

  다음 단계를 수행하여 클러스터를 준비하고 추가 기능을 설치합니다.

  1. [Helm 3](https://v3.helm.sh/docs/intro/install/)을 설치합니다.

  1. Helm에 Azure Policy 리포지토리를 추가합니다.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     자세한 내용은 [Helm 차트 - 빠른 시작 가이드](https://helm.sh/docs/using_helm/#quickstart-guide)를 참조하세요.

  1. Helm 차트를 사용하여 추가 기능을 설치합니다. `<subscriptionId>`를 구독 ID로 바꾸고 `<aks engine cluster resource group>`은 AKS 엔진 자체 관리형 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉니다.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     추가 기능 Helm 차트 설치에 대한 자세한 내용은 GitHub에서 [Azure Policy 추가 기능 Helm 차트 정의](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine)를 참조하세요.

     > [!NOTE]
     > Azure Policy 추가 기능과 리소스 그룹 ID 간의 관계로 인해 Azure Policy는 각 리소스 그룹에 대해 AKS 엔진 클러스터를 하나만 지원합니다.

추가 기능 설치가 성공적이고 해당 _azure-policy_ 및 _gatekeeper_ Pod가 실행 중인지 유효성을 검사하려면 다음 명령을 실행합니다.

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>정책 언어

Kubernetes를 관리하기 위한 Azure Policy 언어 구조는 기존 정책 정의의 언어를 따릅니다. `Microsoft.Kubernetes.Data`의 [리소스 공급자 모드](./definition-structure.md#resource-provider-modes)에서는 [감사](./effects.md#audit) 및 [거부](./effects.md#deny) 효과를 사용하여 Kubernetes 클러스터를 관리할 수 있습니다. _감사_ 와 _거부_ 는 [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) 및 Gatekeeper v3 작업과 관련된 **세부 정보** 속성을 제공해야 합니다.

정책 정의의 _details.templateInfo_, _details.constraint_ 또는 _details.constraintTemplate_ 속성의 일부로 Azure Policy 이러한 [CustomResourceDefinitions(CRD)의](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) URI 또는 Base64Encoded 값을 추가 기능으로 전달합니다. Rego는 Kubernetes 클러스터에 대한 요청을 유효성 검사하도록 OPA 및 Gatekeeper가 지원하는 언어입니다. Kubernetes 관리의 기존 표준을 지원함으로써 Azure Policy에서는 기존 규칙을 다시 사용하고 Azure Policy와 쌍으로 연결하여 통합 클라우드 규정 준수 보고 환경을 구성할 수 있습니다. 자세한 내용은 [Rego란?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)을 참조하세요.

## <a name="assign-a-policy-definition"></a>정책 정의 할당

Kubernetes 클러스터에 정책 정의를 할당하려면 적절한 Azure RBAC(Azure 역할 기반 액세스 제어) 정책 할당 작업을 할당해야 합니다. Azure 기본 제공 역할인 **리소스 정책 기여자** 및 **소유자** 가 이러한 작업을 수행합니다. 자세한 내용은 [Azure Policy의 Azure RBAC 권한](../overview.md#azure-rbac-permissions-in-azure-policy)을 참조하세요.

> [!NOTE]
> 사용자 지정 정책 정의는 _공개 미리 보기_ 기능입니다.

다음 단계에 따라 Azure Portal 사용하여 클러스터를 관리하기 위한 기본 제공 정책 정의를 찾습니다. 사용자 지정 정책 정의를 사용하는 경우 이름 또는 해당 정의를 만든 범주별로 검색합니다.

1. Azure Portal에서 Azure Policy 서비스를 시작합니다. 왼쪽 창에서 **모든 서비스** 를 선택한 다음, **정책** 을 검색하여 선택합니다.

1. Azure Policy 페이지의 왼쪽 창에서 **정의** 를 선택합니다.

1. 범주 드롭다운 목록 상자에서 **모두 선택** 을 사용하여 필터를 해제한 다음, **Kubernetes** 를 선택합니다.

1. 정책 정의를 선택한 다음, **할당** 단추를 선택합니다.

1. **범위** 를 정책 할당이 적용될 관리 그룹, 구독 또는 Kubernetes 클러스터의 리소스 그룹으로 설정합니다.

   > [!NOTE]
   > Kubernetes 정의를 위해 Azure Policy를 할당할 경우 **범위** 에 클러스터 리소스가 포함되어야 합니다. AKS 엔진 클러스터의 경우 **범위** 는 클러스터의 리소스 그룹이어야 합니다.

1. 쉽게 식별할 수 있도록 정책 할당에 **이름** 과 **설명** 을 지정합니다.

1. [정책 적용](./assignment-structure.md#enforcement-mode)을 아래의 값 중 하나로 설정합니다.

   - **사용** - 클러스터에서 정책을 적용합니다. 위반이 있는 Kubernetes 허용 요청이 거부됩니다.

   - **사용 안 함** - 클러스터에서 정책을 적용하지 않습니다. 위반이 있는 Kubernetes 허용 요청이 거부되지 않습니다. 규정 준수 평가 결과는 계속 제공됩니다. 실행 중인 클러스터에 새 정책 정의를 롤아웃하는 경우 위반이 있는 허용 요청은 거부되지 않으므로 ‘사용 안 함’ 옵션이 정책 정의를 테스트하는 데 도움이 됩니다.

1. **다음** 을 선택합니다.

1. **매개 변수 값** 설정

   - 정책 평가에서 Kubernetes 네임스페이스를 제외하려면 **네임 스페이스 제외** 매개 변수에 네임스페이스 목록을 지정합니다. _kube-system_, _gatekeeper-system_ 및 _azure-arc_ 를 제외하는 것이 좋습니다.

1. **검토 + 만들기** 를 선택합니다.

또는 [정책 할당 - 포털](../assign-policy-portal.md) 빠른 시작을 사용하여 Kubernetes 정책을 찾고 할당합니다. 샘플 ‘audit vms’ 대신 Kubernetes 정책 정의를 검색합니다.

> [!IMPORTANT]
> 기본 제공 정책 정의는 **Kubernetes** 범주의 Kubernetes 클러스터에 제공됩니다. 기본 제공 정책 정의 목록은 [Kubernetes 샘플](../samples/built-in-policies.md#kubernetes)을 참조하세요.

## <a name="policy-evaluation"></a>정책 평가

추가 기능은 Azure Policy 서비스를 통해 체크 인하여 15분마다 정책 할당에 변경 사항이 있는지 확인합니다.
새로 고침 주기 동안 추가 기능은 변경 사항이 있는지 확인합니다. 이러한 변경 사항은 제약 조건 템플릿 및 제약 조건에 대한 생성, 업데이트 또는 삭제를 트리거합니다.

Kubernetes 클러스터에서 네임스페이스에 cluster-appropriate 레이블이 있으면 위반이 있는 허용 요청이 거부되지 않습니다. 규정 준수 평가 결과는 계속 제공됩니다.

- Azure Arc 지원 Kubernetes 클러스터: `admission.policy.azure.com/ignore`
- Azure Kubernetes Service 클러스터: `control-plane`

> [!NOTE]
> 클러스터 관리자는 Azure Policy 추가 기능에서 설치한 제약 조건 템플릿 및 제약 조건 리소스를 만들고 업데이트할 수 있는 권한이 있지만 수동 업데이트가 덮어써 있으므로 지원되지 않는 시나리오입니다. Gatekeeper는 추가 기능을 설치하고 Azure Policy 정책 정의를 할당하기 전에 있었던 정책을 계속해서 평가합니다.

추가 기능은 15분마다 클러스터에 대한 전체 검사를 호출합니다. Gatekeeper에서 클러스터에 시도된 변경을 전체 검사하고 실시간 평가한 세부 정보를 수집한 후 추가 기능은 Azure Policy 할당 같은 [규정 준수 세부 정보](../how-to/get-compliance-data.md)를 포함하기 위해 결과를 다시 Azure Policy에 보고합니다. 감사 주기 동안에는 활성 정책 할당의 결과만 반환됩니다. 감사 결과는 실패한 제약 조건의 상태 필드에 나열된 [위반](https://github.com/open-policy-agent/gatekeeper#audit)으로 확인할 수도 있습니다. _비호환_ 리소스에 대한 자세한 내용은 [리소스 공급자 모드에 대한 구성 요소 세부 정보](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)를 참조하세요.

> [!NOTE]
> Kubernetes 클러스터에 대한 Azure Policy의 각 규정 준수 보고서에는 지난 45분 이내의 모든 위반이 포함됩니다. 타임스탬프는 위반이 발생한 시기를 나타냅니다.

기타 고려 사항은 다음과 같습니다.

- 클러스터 구독이 Azure Security Center에 등록된 경우 Azure Security Center Kubernetes 정책이 클러스터에 자동으로 적용됩니다.

- 기존 Kubernetes 리소스를 사용하여 클러스터에 거부 정책을 적용하면 새 정책과 호환되지 않는 기존 리소스가 계속 실행됩니다. 비호환 리소스가 다른 노드에서 다시 예약되면 Gatekeeper가 리소스 생성을 차단합니다.

- 클러스터에 리소스의 유효성을 검사하는 거부 정책이 있으면 배포를 만들 때 사용자에게 거부 메시지가 표시되지 않습니다. 예를 들면, 복제 세트와 Pod가 포함된 Kubernetes 배포를 생각해 보세요. 사용자가 `kubectl describe deployment $MY_DEPLOYMENT`을(를) 실행하면 이벤트의 일부로 거부 메시지가 반환되지 않습니다. 하지만 `kubectl describe replicasets.apps $MY_DEPLOYMENT`에서는 거부와 관련된 이벤트를 반환합니다.

> [!NOTE]
> 초기화 컨테이너는 정책 평가 중에 포함 될 수 있습니다. Init 컨테이너가 포함 되어 있는지 확인 하려면 다음 또는 유사한 선언에 대해 CRD를 검토 합니다.
>
> ```rego
> input_containers[c] { 
>    c := input.review.object.spec.initContainers[_] 
> }
> ```

### <a name="constraint-template-conflicts"></a>제약 조건 템플릿 충돌

제약 조건 템플릿이 동일한 리소스 메타 데이터 이름을 갖지만 정책 정의가 다른 위치의 원본을 참조 하는 경우 정책 정의가 충돌 하는 것으로 간주 됩니다. 예: 두 정책 정의는 `template.yaml` Azure Policy 템플릿 저장소 ( `store.policy.core.windows.net` ) 및 GitHub와 같은 서로 다른 원본 위치에 저장 된 동일한 파일을 참조 합니다.

정책 정의와 해당 제약 조건 템플릿이 할당 되었지만 클러스터에 아직 설치 되어 있지 않고 충돌 하는 경우 충돌을 해결 하기 전 까지는 충돌로 보고 되며 클러스터에 설치 되지 않습니다. 마찬가지로 기존 정책 정의 및 새로 할당 된 정책 정의와 충돌 하는 클러스터에 이미 있는 제약 조건 템플릿이 계속 정상적으로 작동 합니다. 기존 할당이 업데이트 되 고 제약 조건 템플릿을 동기화 하지 못하는 경우 클러스터도 충돌로 표시 됩니다. 모든 충돌 메시지는 [AKS 리소스 공급자 모드 준수 이유](../how-to/determine-non-compliance.md#aks-resource-provider-mode-compliance-reasons) 를 참조 하세요.

## <a name="logging"></a>로깅

Kubernetes 컨트롤러/컨테이너로 _azure-policy_ 및 _gatekeeper_ Pod는 모두 Kubernetes 클러스터에 로그를 유지합니다. 로그는 Kubernetes 클러스터의 **인사이트** 페이지에 공개될 수 있습니다. 자세한 내용은 [컨테이너용 Azure Monitor를 사용하여 Kubernetes 클러스터 성능 모니터링](../../../azure-monitor/containers/container-insights-analyze.md)을 참조하세요.

추가 기능 로그를 보려면 `kubectl`을 사용합니다.

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

자세한 내용은 Gatekeeper 설명서의 [Gatekeeper 디버깅](https://open-policy-agent.github.io/gatekeeper/website/docs/debug/)을 참조하세요.

## <a name="troubleshooting-the-add-on"></a>추가 기능 문제 해결

Kubernetes용 추가 기능의 문제를 해결하는 방법에 대한 자세한 내용은 Azure Policy 문제 해결 문서의 [Kubernetes 섹션](../troubleshoot/general.md#add-on-for-kubernetes-general-errors)을 참조하세요.

## <a name="remove-the-add-on"></a>추가 기능 제거

### <a name="remove-the-add-on-from-aks"></a>AKS에서 추가 기능 제거

AKS 클러스터에서 Azure Policy 추가 기능을 제거하려면 Azure Portal 또는 Azure CLI를 사용합니다.

- Azure portal

  1. **모든 서비스** 를 선택한 다음 **Kubernetes 서비스** 를 검색 및 선택하여 Azure Portal에서 AKS 서비스를 시작합니다.

  1. Azure Policy 추가 기능을 사용하지 않으려는 AKS 클러스터를 선택합니다.

  1. Kubernetes 서비스 페이지의 왼쪽에서 **정책** 을 선택합니다.

  1. 기본 페이지에서 **추가 기능 사용 안 함** 단추를 선택합니다.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes에서 추가 기능 제거

Azure Arc 지원 Kubernetes 클러스터에서 Azure Policy 추가 기능 및 Gatekeeper를 제거하려면 다음 Helm 명령을 실행합니다.

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>AKS 엔진에서 추가 기능 제거

AKS 엔진 클러스터에서 Azure Policy 추가 기능 및 Gatekeeper를 제거하려면 추가 기능 설치에 적절한 방법을 사용합니다.

- AKS 엔진에 대한 클러스터 정의에서 **addons** 속성을 설정하여 설치된 경우:

  _azure-policy_ 에 대한 **addons** 속성을 false로 변경한 후 클러스터 정의를 AKS 엔진에 다시 배포합니다.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  자세한 내용은 [AKS 엔진 - Azure Policy 추가 기능 사용 안 함](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)을 참조하세요.

- Helm 차트와 함께 설치한 경우에는 다음 Helm 명령을 실행합니다.

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy 추가 기능이 수집한 진단 데이터

Kubernetes용 Azure Policy 추가 기능은 제한된 클러스터 진단 데이터를 수집합니다. 이 진단 데이터는 소프트웨어 및 성능과 관련된 중요한 기술 데이터입니다. 다음과 같은 방법으로 사용됩니다.

- Azure Policy 추가 기능을 최신 상태로 유지
- Azure Policy 추가 기능을 안전하고 신뢰할 수 있으며 성능이 뛰어나게 유지
- Azure Policy 추가 기능 향상 - 추가 기능 사용의 집계 분석을 통해

추가 기능이 수집한 정보는 개인 데이터가 아닙니다. 현재 수집되는 세부 정보는 다음과 같습니다.

- Azure Policy 추가 기능 에이전트 버전
- 클러스터 유형
- 클러스터 영역
- 클러스터 리소스 그룹
- 클러스터 리소스 ID
- 클러스터 구독 ID
- 클러스터 OS(예: Linux)
- 클러스터 도시(예: 시애틀)
- 클러스터 주 또는 지방(예: 워싱턴)
- 클러스터 국가 또는 지역(예: 미국)
- 정책 평가에 에이전트를 설치하는 동안 Azure Policy 추가 기능에서 발생한 예외/오류
- Azure Policy 추가 기능으로 설치하지 않은 Gatekeeper 정책 정의 수

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](../how-to/programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
