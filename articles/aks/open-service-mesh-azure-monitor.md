---
title: Azure Monitor 및 Application Insights 사용
description: 서비스 메시를 사용 하 여 Azure Monitor 및 Application Insights를 사용 하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 4e1df1275e225b42bf01bd2a4092cea07f9c4c12
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057817"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Azure Monitor 및 Application Insights를 사용한 OSM(Open Service Mesh) 모니터링 및 가시성

Azure Monitor 및 Azure 애플리케이션는 모두 응용 프로그램 및 서비스의 가용성과 성능을 극대화 하는 데 도움이 Insights. 이러한 서비스는 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동 하기 위한 종합적인 솔루션을 제공 합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

OSM AKS 추가 기능은 이러한 Azure 서비스 둘 다에 긴밀 하 게 통합 되어 있으며 OSM 메트릭에 의해 제공 되는 중요 Kpi를 보고 응답 하기 위한 원활한 Azure 환경을 제공 합니다. 

## <a name="enable-azure-monitor"></a>Azure Monitor 사용

AKS 클러스터에서 OSM AKS 추가 기능을 사용 하도록 설정한 후에는 Azure Portal를 통해 클러스터에서 Azure Monitor를 사용 하도록 설정 해야 합니다. AKS 클러스터를 클릭 하 고 "모니터링" 아래의 "Insights" 탭으로 이동 하 고 "사용"을 선택 합니다. 

Azure Monitor 사용 하도록 설정 되 면 kube 네임 스페이스에서 다음 로그를 볼 수 있습니다. 

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>OSM 모니터링 되는 네임 스페이스에서 메트릭 사용

메시에 서 모니터링 하는 특정 네임 스페이스에서 메트릭을 스크랩 하려면 다음 명령을 실행 해야 합니다.

```sh
osm metrics enable --osm-namespace <namespace>
```

예를 들어, 서 [점 데모](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/)를 실행 하는 경우 `osm metrics enable` 다음 네임 스페이스에 대해 명령을 실행 합니다.

```sh
osm metrics enable --osm-namespace bookbuyer
osm metrics enable --osm-namespace bookstore
osm metrics enable --osm-namespace bookthief
osm metrics enable --osm-namespace bookwarehouse
```
## <a name="apply-configmaps"></a>ConfigMaps 적용

에서 `kube-system` 모니터링 해야 하는 네임 스페이스를 AzMon 알려주는 다음 ConfigMap을 만듭니다. 예를 들어 bookbuyer/서 점 데모의 경우 ConfigMap은 다음과 같이 표시 됩니다. 

```yaml
kind: ConfigMap
apiVersion: v1
data:
  schema-version: v1
  config-version: ver1
  osm-metric-collection-configuration: |-
    # OSM metric collection settings
    [osm_metric_collection_configuration]
      [osm_metric_collection_configuration.settings]
          # Namespaces to monitor
          monitor_namespaces = ["bookstore", "bookbuyer", "bookthief", "bookwarehouse"]
metadata:
  name: container-azm-ms-osmconfig
  namespace: kube-system

```

그런 다음 [monitor_kubernetes_pods를 true로](https://github.com/microsoft/Docker-Provider/blob/24b709f9e3c3b18779102b491fc98b87a99d1335/kubernetes/container-azm-ms-agentconfig.yaml#L72)설정 하기 위해 두 번째 configmap을 만들어야 합니다.

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: container-azm-ms-agentconfig
  namespace: kube-system
data:
  schema-version: v1
  config-version: ver1
  prometheus-data-collection-settings: |-
    [prometheus_data_collection_settings.cluster]
        interval = "30s"
        monitor_kubernetes_pods = true
```

## <a name="view-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭 보기

Azure Portal에서 Kubernetes 클러스터를 선택 하 고 "모니터링" 아래에서 "로그" 탭을 선택 합니다. 이제 테이블을 쿼리하여 `InsightsMetrics` 사용 가능한 네임 스페이스의 메트릭을 볼 수 있습니다. 예를 들어,에 대 한 엔보이 메트릭을 표시 하려는 경우 `bookbuyer` 다음 쿼리를 사용 합니다.

```sh
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "bookbuyer"
```

## <a name="additional-information"></a>추가 정보

OSM AKS 추가 기능에 대 한 Azure Monitor 및 Insights Azure 애플리케이션를 사용 하도록 설정 하 고 구성 하는 방법에 대 한 자세한 내용은 [OSM에 대 한 Azure Monitor](https://aka.ms/azmon/osmpreview) 페이지를 참조 하세요.