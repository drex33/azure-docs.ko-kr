---
title: Azure Arc를 사용하여 대규모 하이브리드 인프라 관리
description: Azure Lighthouse를 사용하면 Azure 외부에서 고객의 컴퓨터와 Kubernetes 클러스터를 효과적으로 관리할 수 있습니다.
ms.date: 09/07/2021
ms.topic: how-to
ms.openlocfilehash: 8761c87daeda97b6c67ce8e79e438ed01d8d3a37
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714735"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Azure Arc를 사용하여 대규모 하이브리드 인프라 관리

[Azure Lighthouse](../overview.md) 서비스 공급자가 Azure Arc 사용하여 모든 Azure AD(관리형 Azure Active Directory) 테넌트에서 가시성을 확보하여 고객의 하이브리드 환경을 관리하는 데 도움이 될 수 있습니다.

[Azure Arc](../../azure-arc/overview.md)를 사용하면 온-프레미스, 에지, 다중 클라우드에 걸쳐 복잡하고 분산된 환경을 간소화하여 Azure 서비스를 어디서나 배포하고 Azure 관리를 모든 인프라로 확장할 수 있습니다.

[Azure Arc 지원 서버를](../../azure-arc/servers/overview.md)사용하면 고객은 네이티브 Azure 가상 머신을 관리하는 것과 동일한 방식으로 회사 네트워크에서 Azure 외부에서 호스트되는 모든 Windows 및 Linux 머신을 관리할 수 있습니다. 하이브리드 머신을 Azure에 연결하면 해당 머신은 Azure에서 리소스로 연결되고 처리됩니다. 그런 다음 서비스 공급자는 고객의 Azure 리소스와 함께 이러한 비 Azure 머신을 관리할 수 있습니다.

[Azure Arc 사용하도록 설정된 Kubernetes를](../../azure-arc/kubernetes/overview.md) 통해 고객은 Azure 내부 또는 외부에서 Kubernetes 클러스터를 연결하고 구성할 수 있습니다. Azure Arc에 연결된 Kubernetes 클러스터는 Azure Portal에서 Azure Resource Manager ID 및 관리 ID와 함께 표시됩니다. 클러스터는 표준 Azure 구독에 연결되고, 리소스 그룹에 위치하며, 다른 Azure 리소스와 마찬가지로 태그를 받을 수 있습니다.

이 항목에서는 관리하는 고객 테넌트에서 확장 가능한 방식으로 Azure Arc 지원 서버 및 Azure Arc 지원 Kubernetes를 사용하는 방법에 대한 개요를 제공합니다.

> [!TIP]
> 이 항목은 서비스 공급자 및 고객을 염두에 두고 만들었지만 이 지침은 [Azure Lighthouse를 사용하여 여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에도 적용됩니다.

## <a name="manage-hybrid-servers-at-scale-with-azure-arcenabled-servers"></a>Azure Arc 지원 서버를 사용하여 대규모 하이브리드 서버 관리

서비스 공급자는 고객이 [Azure Connected Machine 에이전트](../../azure-arc/servers/agent-overview.md)를 사용하여 구독에 연결한 Azure 외부의 온-프레미스 Windows Server 또는 Linux 머신을 관리할 수 있습니다. Azure Portal 위임된 구독에 대한 리소스를 볼 때 Azure Arc 레이블이 있는 연결된 컴퓨터가 **표시됩니다.**

고객의 Azure 리소스를 관리하는 것과 동일한 방식으로 Azure Policy 및 태그 지정과 같은 Azure 구문을 사용하여 이러한 연결된 머신을 관리할 수 있습니다. 또한 고객 테넌트 간에 작업하여 연결된 모든 하이브리드 머신을 함께 관리할 수 있습니다.

예를 들어, [고객의 하이브리드 머신에 동일한 정책 집합을 적용](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md)할 수 있습니다. Azure Security Center 사용하여 고객의 모든 하이브리드 환경에서 규정 준수를 모니터링하거나 Azure Monitor [사용하여 하이브리드 머신에서](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) Log Analytics 작업 영역으로 직접 데이터를 수집할 수도 있습니다. [가상 머신 확장](../../azure-arc/servers/manage-vm-extensions.md)은 비 Azure Windows 및 Linux VM에 배포할 수 있으며 고객의 하이브리드 머신 관리를 간소화합니다.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes를 사용하여 대규모로 하이브리드 Kubernetes 클러스터 관리

Azure에서 실행되는 것처럼 [Azure Arc를 사용하여 고객 구독에 연결](../../azure-arc/kubernetes/quickstart-connect-cluster.md)된 Kubernetes 클러스터를 관리할 수 있습니다.

고객이 [Kubernetes 클러스터를 온보딩하여 Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md)서비스 주체 계정을 만든 경우 클러스터를 온보딩하고 관리할 수 있도록 이 계정에 액세스할 수 있습니다. 이렇게 하려면 서비스 주체 계정을 포함하는 구독이 Azure Lighthouse [온보딩된](onboard-customer.md)경우 관리 테넌트 사용자에게 "Kubernetes 클러스터 - Azure Arc 온보딩" Azure 기본 제공 역할이 부여되어야 합니다.

연결된 클러스터에 GitOps를 사용하여 [구성](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) 및 [Helm 차트](../../azure-arc/kubernetes/use-gitops-with-helm.md)를 배포할 수 있습니다.

또한 Azure Monitor를 사용하여 연결된 클러스터를 모니터링하고 [Azure Policy를 사용하여 클러스터 구성을 대규모로 적용](../../azure-arc/kubernetes/use-azure-policy.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Arc GitHub 리포지토리](https://github.com/microsoft/azure_arc)에서 빠른 시작 및 샘플을 살펴봅니다.
- Azure Arc [지원되는 서버에 대해 지원되는 시나리오에](../../azure-arc/servers/overview.md#supported-cloud-operations)대해 알아봅니다.
- [Azure Arc에서 지원하는 Kubernetes 배포판](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)에 대해 알아봅니다.
