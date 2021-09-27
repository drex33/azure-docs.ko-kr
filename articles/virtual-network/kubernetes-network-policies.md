---
title: Azure Kubernetes 네트워크 정책 | Microsoft Docs
description: Kubernetes 클러스터를 보호하는 Kubernetes 네트워크 정책에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: f8a8e5785af1bd74fb958a315de379facdc87171
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575975"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes 네트워크 정책 개요

네트워크 정책은 NSG(네트워크 보안 그룹)이 VM에 대한 마이크로 구분을 제공하는 것처럼 pod에 대한 마이크로 구분을 제공합니다. Azure 네트워크 정책 관리자(Azure NPM이라고도 함)는 표준 Kubernetes 네트워크 정책 사양을 지원합니다. 레이블을 사용하여 Pod 그룹을 선택하고 이러한 Pod와의 트래픽을 필터링하는 수신 및 송신 규칙의 목록을 정의할 수 있습니다. [Kubernetes 설명서](https://kubernetes.io/docs/concepts/services-networking/network-policies/)에서 Kubernetes 네트워크 정책에 대해 자세히 알아보세요.

![Kubernetes 네트워크 정책 개요](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM 구현은 컨테이너를 위한 VNet 통합을 제공하는 Azure CNI와 함께 작동합니다. NPM은 Linux에서만 지원됩니다. 이 구현에서는 정의된 정책을 기반으로 Linux IPTables의 허용 및 거부 IP 규칙을 구성하여 트래픽 필터링을 적용합니다. 이러한 규칙은 Linux IPSets를 사용하여 함께 그룹화됩니다.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Kubernetes 클러스터에 대한 보안 계획
클러스터에 대한 보안을 구현하는 경우 NSG(네트워크 보안 그룹)를 사용하여 클러스터 서브넷을 드나드는 트래픽을 필터링합니다(북-남 트래픽). 클러스터의 Pod 간 트래픽에 대해 Azure NPM을 사용합니다(동-서 트래픽).

## <a name="using-azure-npm"></a>Azure NPM 사용
Azure NPM은 Pod에 대한 미세 구분을 제공하기 위해 다음과 같은 방법으로 사용할 수 있습니다.

### <a name="azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)
NPM은 기본적으로 AKS에서 사용할 수 있으며 클러스터를 만들 때 사용하도록 설정할 수 있습니다. [AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 Pod 간 트래픽 보호](../aks/use-network-policies.md)에 대해 자세히 알아보세요.

### <a name="aks-engine"></a>AKS 엔진
AKS 엔진은 Azure에서 Kubernetes 클러스터 배포를 위한 Azure Resource Manager 템플릿을 생성하는 도구입니다. 클러스터 구성은 템플릿 생성 시 도구로 전달되는 JSON 파일에서 지정됩니다. 지원되는 클러스터 설정 및 해당 설명의 전체 목록을 자세히 알아보려면 Microsoft Azure Container Service Engine - 클러스터 정의를 참조하세요.

acs 엔진을 사용하여 배포된 클러스터에서 정책을 사용하도록 설정하려면 클러스터 정의 파일의 networkPolicy 설정의 값을 “azure”로 지정합니다.

#### <a name="example-configuration"></a>구성 예

다음 JSON 예제 구성은 새 가상 네트워크 및 서브넷을 만들고 Azure CNI 내에서 Kubernetes 클러스터를 배포합니다. “Notepad”를 사용하여 JSON 파일을 편집하는 것이 좋습니다. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Azure의 DIY(Do It Yourself) Kubernetes 클러스터
 DIY 클러스터의 경우 먼저 CNI 플러그 인을 설치하고 클러스터의 모든 가상 머신에서 사용하도록 설정합니다. 자세한 지침은 [직접 배포하는 Kubernetes 클러스터를 위한 플러그 인 배포](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)를 참조하세요.

클러스터가 배포되면 다음 `kubectl` 명령을 실행하여 Azure NPM '디먼 집합'을 클러스터에 다운로드하고 적용합니다.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
솔루션도 오픈 소스이며 코드는 [Azure Container Networking 리포지토리](https://github.com/Azure/azure-container-networking/tree/master/npm)에서 제공됩니다.

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Azure NPM를 사용하여 네트워크 구성 모니터링 및 시각화
Azure NPM에는 구성을 모니터링하고 더 잘 이해할 수 있는 정보를 제공하는 Prometheus 메트릭이 포함됩니다. Azure Portal 또는 Grafana Labs에서 기본 제공 시각화를 제공합니다. Azure Monitor 또는 Prometheus 서버를 사용하여 이러한 메트릭 수집을 시작할 수 있습니다.

### <a name="benefits-of-azure-npm-metrics"></a>Azure NPM 메트릭의 이점
이전에는 사용자가 클러스터 노드 내에서 `iptables -L` 명령을 실행하여 네트워크 구성에 대해 알아볼 수 있었습니다. 이 명령은 자세하고 이해하기 어려운 출력을 생성합니다. NPM 메트릭은 네트워크 정책, IPTables 규칙 및 IPSet와 관련하여 다음과 같은 이점을 제공합니다.
- 구성 디버그를 위해 3차원과 시간 차원 간의 관계에 대한 정보를 제공
- 모든 IPSet 및 각 IPSet의 항목 수
- IPTable/IPSet 수준 세분성을 사용하여 정책을 적용하는 데 걸린 시간
 
### <a name="supported-metrics"></a>지원되는 메트릭
다음은 지원되는 메트릭의 목록입니다.

|메트릭 이름 |Description  |Prometheus 메트릭 유형  |레이블  |
|---------|---------|---------|---------|
|`npm_num_policies`     |네트워크 정책 수          |계기         |-         |
|`npm_num_iptables_rules`     | IPTables 규칙 수     | 계기        |-         |         
|`npm_num_ipsets`     |IPSet 수         |계기            |-         |
|`npm_num_ipset_entries`     |모든 IPSet 내의 IP 주소 항목 수         |계기         |-         |
|`npm_add_policy_exec_time`     |네트워크 정책을 추가하기 위한 런타임         |요약         |분위수(0.5, 0.9 또는 0.99)         |
|`npm_add_iptables_rule_exec_time`     |IPTables 규칙을 추가하기 위한 런타임         |요약         |분위수(0.5, 0.9 또는 0.99)         |
|`npm_add_ipset_exec_time`     |IPSet를 추가하기 위한 런타임         |요약         |분위수(0.5, 0.9 또는 0.99)         |
|`npm_ipset_counts`(고급)     |각 개별 IPSet 내의 항목 수         |GaugeVec         |집합 이름 및 해시         |

"exec_time" 메트릭의 다양한 분위수 수준은 일반적인 시나리오와 최악의 시나리오를 구분하는 데 도움이 됩니다.

각 "exec_time" 요약 메트릭에 대한 "exec_time_count" 및 "exec_time_sum" 메트릭도 있습니다.

메트릭은 컨테이너용 Azure Monitor 또는 Prometheus를 통해 스크랩할 수 있습니다.

### <a name="setup-for-azure-monitor"></a>Azure Monitor 설정
첫 번째 단계는 Kubernetes 클러스터에서 컨테이너용 Azure Monitor를 사용하도록 설정하는 것입니다. 단계는 [컨테이너용 Azure Monitor 개요](../azure-monitor/containers/container-insights-overview.md)에서 확인할 수 있습니다. 컨테이너용 Azure Monitor를 사용하도록 설정했으면 NPM 통합 및 Prometheus NPM 메트릭 수집을 사용하도록 [컨테이너용 Azure Monitor ConfigMap](https://aka.ms/container-azm-ms-agentconfig)을 구성합니다. 컨테이너용 Azure monitor ConfigMap에는 NPM 메트릭을 수집하기 위한 설정이 포함된 ```integrations``` 섹션이 있습니다. 이러한 설정은 ConfigMap에서 기본적으로 사용되지 않습니다. 기본 설정 ```collect_basic_metrics = true```를 지정하면 기본 NPM 메트릭이 수집됩니다. 고급 설정 ```collect_advanced_metrics = true```를 지정하면 기본 메트릭과 함께 고급 메트릭이 수집됩니다. 

ConfigMap을 편집한 후에는 로컬에 저장하고 다음과 같이 ConfigMap을 클러스터에 적용합니다.

`kubectl apply -f container-azm-ms-agentconfig.yaml`

 다음은 고급 메트릭 컬렉션과 함께 사용하도록 설정된 NPM 통합을 보여 주는 [컨테이너용 Azure Monitor ConfigMap](https://aka.ms/container-azm-ms-agentconfig)의 코드 조각입니다.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
고급 메트릭은 선택 사항이며 이를 설정하면 기본 메트릭 수집이 자동으로 설정됩니다. 현재 고급 메트릭에는 `npm_ipset_counts`만 포함됩니다.

[구성 맵의 컨테이너용 Azure Monitor 수집 설정](../azure-monitor/containers/container-insights-agent-config.md)에 대해 자세히 알아보세요.

### <a name="visualization-options-for-azure-monitor"></a>Azure Monitor의 시각화 옵션
NPM 메트릭 수집을 사용하도록 설정하면 Container Insights를 사용하여 Azure Portal에서 또는 Grafana에서 메트릭을 볼 수 있습니다.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Azure Portal에서 클러스터 인사이트 보기
Azure Portal을 엽니다. 클러스터의 인사이트에서 '통합 문서'로 이동한 다음 'NPM(네트워크 정책 관리자) 구성'을 엽니다.

통합 문서(아래 그림)를 보는 것 외에도 인사이트 섹션 아래의 '로그'에서 Prometheus 메트릭을 직접 쿼리할 수 있습니다. 예를 들어 다음 쿼리는 수집되는 모든 메트릭을 반환합니다.
| where TimeGenerated > ago(5h) | where Name contains "npm_"

Log Analytics에서 직접 메트릭을 쿼리할 수도 있습니다. [Log Analytics 쿼리 시작하기](../azure-monitor/containers/container-insights-log-query.md)에 대해 자세히 알아보세요. 

#### <a name="viewing-in-grafana-dashboard"></a>Grafana 대시보드에서 보기
[여기](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource)에 설명된 대로 Grafana 서버를 설정하고 Log Analytics 데이터 원본을 구성합니다. 그런 다음 [Log Analytics 백 엔드가 포함된 Grafana 대시보드](https://grafana.com/grafana/dashboards/10956)를 Grafana Labs로 가져옵니다.

대시보드에는 Azure 통합 문서와 유사한 시각적 개체가 있습니다. 차트에 패널을 추가하고 InsightsMetrics 테이블에서 NPM 메트릭을 시각화할 수 있습니다.

### <a name="setup-for-prometheus-server"></a>Prometheus 서버 설정
일부 사용자는 컨테이너에 대한 Azure Monitor 대신 Prometheus 서버를 사용하여 메트릭을 수집하도록 선택할 수 있습니다. NPM 메트릭을 수집하려면 scrape config에 두 개의 작업을 추가하기만 하면 됩니다.

간단한 Prometheus 서버를 설치하려면 클러스터에 다음 Helm 리포지토리를 추가합니다.
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
그런 다음 서버를 추가합니다.
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
여기서 `prometheus-server-scrape-config.yaml`은 다음으로 구성됩니다.
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


`azure-npm-node-metrics` 작업을 아래의 내용으로 바꾸거나 Kubernetes Pod용 기존 작업에 통합할 수도 있습니다.
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Prometheus의 시각화 옵션
Prometheus 서버를 사용하는 경우 Grafana 대시보드만 지원됩니다. 

아직 설치하지 않은 경우 Grafana 서버를 설치하고 Prometheus 데이터 원본을 구성합니다. 그런 다음, [Prometheus 백 엔드가 포함된 Grafana 대시보드](https://grafana.com/grafana/dashboards/13000)를 Grafana Labs로 가져옵니다.

이 대시보드의 시각적 개체는 Container Insights/Log Analytics 백 엔드가 포함된 대시보드와 동일합니다.

### <a name="sample-dashboards"></a>샘플 대시보드
다음은 CI(Container Insights) 및 Grafana의 NPM 메트릭에 대한 몇 가지 대시보드 예제입니다.

#### <a name="ci-summary-counts"></a>CI 요약 수
![Azure 통합 문서 요약 수](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>시간별 CI 수
[![시간별 Azure 통합 문서 수](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet 항목
[![Azure 통합 문서 IPSet 항목](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI 런타임 분위수
![Azure 통합 문서 런타임 분위수](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Grafana 대시보드 요약 수
![Grafana 대시보드 요약 수](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>시간별 Grafana 대시보드 수
[![시간별 Grafana 대시보드 수](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana 대시보드 IPSet 항목
[![Grafana 대시보드 IPSet 항목](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana 대시보드 런타임 분위수
[![Grafana 대시보드 런타임 분위수](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>다음 단계
- [Azure Kubernetes Service](../aks/intro-kubernetes.md)에 대해 알아봅니다.
-  [컨테이너 네트워크](container-networking-overview.md)에 대해 알아봅니다.
- Kubernetes 클러스터 또는 Docker 컨테이너에 대한 [플러그 인을 배포](deploy-container-networking.md)합니다.
