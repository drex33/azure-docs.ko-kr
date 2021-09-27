---
title: Container Insights 사용 | Microsoft Docs
description: 이 문서에서는 컨테이너의 성능과 어떤 성능 관련 문제가 확인되었는지 이해할 수 있도록 Container Insights를 사용하도록 설정하고 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 04bc88034c3537431c4c1972611d0c4d93e4cda4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631404"
---
# <a name="enable-container-insights"></a>Container Insights 사용

이 문서에서는 Kubernetes 환경에 배포되고 호스트되는 워크로드의 성능을 모니터링하도록 Container Insights를 설정하는 데 사용할 수 있는 옵션의 개요를 제공합니다.

- [AKS(Azure Kubernetes Service)](../../aks/index.yml)  
- [Arc 지원 Kubernetes 클러스터](../../azure-arc/kubernetes/overview.md)
   - [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) 또는 온-프레미스
   - [AKS 엔진](https://github.com/Azure/aks-engine)
   - [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 버전 4.x  
   - [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 버전 4.x  


지원되는 다음 방법 중 하나를 사용하여 새 배포 또는 Kubernetes의 기존 배포 하나 이상에 대해 Container Insights를 사용하도록 설정할 수 있습니다.

- Azure 포털
- Azure PowerShell
- Azure CLI
- [Terraform 및 AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

AKS가 아닌 Kubernetes 클러스터의 경우 모니터링을 사용하도록 설정하기 전에 먼저 클러스터를 [Azure Arc](../../azure-arc/kubernetes/overview.md) 연결해야 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 요구 사항을 충족해야 합니다.

> [!IMPORTANT]
> Log Analytics 컨테이너화 Linux Agent(replicaset pod)는 클러스터 내의 Kubelet 보안 포트(10250)의 모든 Windows 노드에 대한 API 호출을 수행하여 노드 및 컨테이너 성능 관련 메트릭을 수집합니다. Kubelet 보안 포트(: 10250)는 Windows 노드 및 컨테이너 성능 관련 메트릭 수집이 작동되려면 인바운드 및 아웃바운드 둘 다에 대한 클러스터의 가상 네트워크에서 열려 있어야 합니다.
>
> Windows 노드가 포함된 Kubernetes 클러스터가 있는 경우 네트워크 보안 그룹 및 네트워크 정책을 검토하고 구성하여 클러스터의 가상 네트워크에서 Kubelet 보안 포트(:10250)가 인바운드 및 아웃바운드 모두에 대해 열려 있는지 확인하세요.


- Log Analytics 작업 영역이 있습니다.

   Container Insights는 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열된 지역에서 Log Analytics 작업 영역을 지원합니다.

   새 AKS 클러스터 모니터링을 사용하도록 설정할 때 만들거나 온보드 환경에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만들도록 할 수 있습니다. 
   
   작업 영역을 직접 만들도록 선택하는 경우 다음을 통해 만들 수 있습니다. 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure 포털](../logs/quick-create-workspace.md) 
   
   기본 작업 영역에 사용할 수 있는 지원되는 매핑 쌍 목록은 [Container Insights에 대한 지역 매핑](container-insights-region-mapping.md)을 참조하세요.

- 컨테이너 모니터링을 사용하도록 설정하기 위한 *Log Analytics 기여자* 그룹의 멤버입니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../logs/manage-access.md)를 참조하세요.

- AKS 클러스터 리소스에서 [*소유자* 그룹](../../role-based-access-control/built-in-roles.md#owner)의 멤버입니다.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- 모니터링 데이터를 보려면 컨테이너 인사이트를 사용하여 구성된 Log Analytics 작업 영역에 [*Log Analytics 읽기 권한자*](../logs/manage-access.md#manage-access-using-azure-permissions) 역할이 있어야 합니다.

- 기본적으로 Prometheus 메트릭은 수집되지 않습니다. 메트릭을 수집하도록 [에이전트를 구성](container-insights-prometheus-integration.md)하기 전에 스크래핑할 수 있는 데이터 및 지원되는 방법을 이해하려면 [Prometheus 설명서](https://prometheus.io/)를 검토하는 것이 중요합니다.
- AKS 클러스터를 동일한 Azure AD 테넌트의 다른 Azure 구독에 있는 Log Analytics 작업 영역에 연결할 수 있습니다. 현재는 Azure Portal을 사용하여 이 작업을 수행할 수 없으며 Azure CLI 또는 Resource Manager 템플릿을 사용하여 수행할 수 있습니다.

## <a name="supported-configurations"></a>지원되는 구성

Container Insights는 공식적으로 다음 구성을 지원합니다.

- 환경: Azure Red Hat OpenShift, Kubernetes 온-프레미스 및 Azure/Azure Stack의 AKS 엔진 자세한 내용은 [Azure Stack에서 AKS 엔진](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)을 참조하세요.
- Kubernetes 및 지원 정책의 버전은 [AKS(Azure Kubernetes Service)에서 지원](../../aks/supported-kubernetes-versions.md)되는 것과 동일합니다.
- 클러스터를 Azure Arc 연결하고 [Azure Arc](../../azure-arc/kubernetes/overview.md) 통해 컨테이너 Insights 통해 모니터링을 사용하도록 설정하는 것이 좋습니다.

## <a name="network-firewall-requirements"></a>네트워크 방화벽 요구 사항

다음 표에는 컨테이너화된 에이전트가 Container Insights와 통신하는 데 필요한 프록시 및 방화벽 구성 정보가 나열되어 있습니다. 에이전트의 모든 네트워크 트래픽은 Azure Monitor로 아웃바운드됩니다.

|에이전트 리소스|포트 |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

다음 표에서는 Azure 중국 21Vianet에 대한 프록시 및 방화벽 구성 정보를 나열합니다.

|에이전트 리소스|포트 |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | 데이터 수집 |
| `*.oms.opinsights.azure.cn` | 443 | OMS 온보딩 |
| `dc.services.visualstudio.com` | 443 | Azure 퍼블릭 클라우드 Application Insights를 사용하는 에이전트 원격 분석의 경우 |

다음 표에서는 Azure 미국 정부에 대한 프록시 및 방화벽 구성 정보를 나열합니다.

|에이전트 리소스|포트 |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | 데이터 수집 |
| `*.oms.opinsights.azure.us` | 443 | OMS 온보딩 |
| `dc.services.visualstudio.com` | 443 | Azure 퍼블릭 클라우드 Application Insights를 사용하는 에이전트 원격 분석의 경우 |

## <a name="components"></a>구성 요소

성능을 모니터링하는 기능은 Container Insights용으로 특별히 개발된 Linux용 컨테이너화 Log Analytics 에이전트에 따라 달라집니다. 이 특수 에이전트는 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집하며, 배포 과정에서 에이전트가 지정된 Log Analytics 작업 영역에 자동으로 배포 및 등록됩니다. 

에이전트 버전은 microsoft/oms:ciprod04202018 이상이며, *mmddyyyy* 형식의 날짜로 표시됩니다.

>[!NOTE]
>AKS에 대한 Windows Server 지원이 일반 공급되면서 Windows Server 노드가 있는 AKS 클러스터에는 로그를 수집하고 Log Analytics에 전달하기 위해 각 개별 Windows Server 노드에 미리 보기 에이전트가 daemonset pod로 설치되어 있습니다. 성능 메트릭의 경우 표준 배포의 일부로 클러스터에 자동으로 배포되는 Linux 노드는 클러스터의 모든 Windows 노드를 대신하여 Azure Monitor 데이터를 수집하고 전달합니다.

새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다. 릴리스된 버전을 추적하려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.

> [!NOTE]
> AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다.
>
> 템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.

Container Insights를 사용하도록 설정하려면 다음 표에 설명된 방법 중 하나를 사용합니다.

| 배포 상태 | 메서드 | Description |
|------------------|--------|-------------|
| 새 Kubernetes 클러스터 | [Azure CLI를 사용하여 AKS 클러스터 만들기](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI를 사용하여 만든 새 AKS 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. |
| | [Terraform을 사용하여 AKS 클러스터 만들기](container-insights-enable-new-cluster.md#enable-using-terraform)| 오픈 소스 도구인 Terraform을 사용하여 만든 새 AKS 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. |
| | [Azure Resource Manager 템플릿을 사용하여 OpenShift 클러스터 만들기](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 미리 구성된 Azure Resource Manager 템플릿을 사용하여 만드는 새 OpenShift 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. |
| | [Azure CLI를 사용하여 OpenShift 클러스터 만들기](/cli/azure/openshift#az_openshift_create) | Azure CLI를 사용하여 새 OpenShift 클러스터를 배포할 때 모니터링을 사용하도록 설정할 수 있습니다. |
| 기존 AKS 클러스터 | [Azure CLI를 사용하여 AKS 클러스터의 모니터링 사용](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Azure CLI를 사용하여 이미 배포된 AKS 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. |
| |[Terraform을 사용하여 AKS 클러스터에 사용](container-insights-enable-existing-clusters.md#enable-using-terraform) | 오픈 소스 도구 Terraform을 사용하여 이미 배포된 AKS 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. |
| | [Azure Monitor에서 AKS 클러스터에 사용](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor의 다중 클러스터 페이지에서 이미 배포된 하나 이상의 AKS 클러스터에 대해 모니터링을 사용하도록 설정할 수 있습니다. |
| | [AKS 클러스터에서 사용](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure Portal의 AKS 클러스터에서 직접 모니터링을 사용하도록 설정할 수 있습니다. |
| | [Azure Resource Manager 템플릿을 사용하여 AKS 클러스터에 사용](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 미리 구성된 Azure Resource Manager 템플릿을 사용하여 AKS 클러스터에 대해 모니터링을 사용하도록 설정할 수 있습니다. |
| 기존 비 AKS Kubernetes 클러스터 | [Azure CLI 사용하여 비 AKS Kubernetes 클러스터에 대해 를 사용하도록 설정합니다.](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-cli) | Azure 외부에서 호스트되고 Azure Arc 사용하도록 설정된 Kubernetes 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. 여기에는 Azure CLI 사용하는 하이브리드, OpenShift 및 다중 클라우드가 포함됩니다. |
| | [Azure Resource Manager 템플릿을 사용하여 비 AKS Kubernetes 클러스터에 대해 사용](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-resource-manager) | 미리 구성된 Azure Resource Manager 템플릿을 사용하여 Arc에서 사용하도록 설정된 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. |
| | [Azure Monitor 비 AKS Kubernetes 클러스터에 대해 사용하도록 설정](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-portal) | Azure Monitor 다중 클러스터 페이지에서 이미 배포된 Arc를 사용하여 사용하도록 설정된 하나 이상의 클러스터에 대한 모니터링을 사용하도록 설정할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

이제 모니터링을 사용하도록 설정했으므로 AKS(Azure Kubernetes Service), Azure Stack 또는 다른 환경에서 호스트되는 Kubernetes 클러스터의 성능 분석을 시작할 수 있습니다. Container Insights를 사용하는 방법에 대한 자세한 내용은 [Kubernetes 클러스터 성능 보기](container-insights-analyze.md)를 참조하세요.
