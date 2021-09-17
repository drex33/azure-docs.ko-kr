---
title: OSM OSS 관찰성
description: Open Service 메시에 대해 오픈 소스 관찰성를 구성 하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ce03fc4007ad55485150feb715242d4cc216433e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441294"
---
# <a name="manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>관찰성에 대 한 OSM (Open Service 메시) 메트릭을 보려면 Grafana 및 Jaeger를 수동으로 배포 합니다.

> [!WARNING]
> Prometheus, Grafana 및 Jaeger의 설치는 이러한 도구를 활용하여 OSM 메트릭 데이터를 보는 방법을 보여 주는 일반 지침으로 제공됩니다. 설치 지침은 프로덕션 설정에 활용되지 않습니다. 필요에 따라 가장 적절히 설치하는 방법은 각 도구의 설명서를 참조하세요. 가장 주목할 만한 것은 영구 스토리지가 없다는 것입니다. 즉, Prometheus Grafana 및/또는 Jaeger Pod가 종료되면 모든 데이터가 손실됩니다.

OSM(Open Service Mesh)은 메시 내의 모든 트래픽과 관련된 상세 메트릭을 생성합니다. 이러한 메트릭은 사용자가 애플리케이션의 문제를 해결하고, 유지 관리하고, 분석할 수 있도록 지원하는 메시의 애플리케이션 동작에 대한 정보를 제공합니다.

현재 OSM은 사이드카 프록시(Envoy)에서 직접 메트릭을 수집합니다. OSM은 메시의 모든 서비스에 대해 들어오고 나가는 트래픽에 대한 다양한 메트릭을 제공합니다. 이러한 메트릭을 사용하여 사용자는 전체 트래픽 볼륨, 트래픽 내의 오류 및 요청에 대한 응답 시간에 대한 정보를 얻을 수 있습니다.

OSM은 Prometheus를 사용하여 메시에서 실행 중인 모든 애플리케이션에 대해 일관된 트래픽 메트릭과 통계를 수집하고 저장합니다. Prometheus는 Kubernetes 및 서비스 메시 환경에서 일반적으로 사용되는 오픈 소스 모니터링 및 경고 도구 키트입니다.

메시의 일부인 각 애플리케이션은 Prometheus 형식으로 메트릭(프록시 메트릭)을 공개하는 Envoy 사이드카를 포함하는 Pod에서 실행됩니다. 또한 메시의 일부인 모든 Pod에는 Prometheus 주석이 있어 Prometheus 서버가 애플리케이션을 동적으로 스크래핑할 수 있습니다. 이 메커니즘은 새 네임스페이스/Pod/서비스가 메시에 추가될 때마다 자동으로 메트릭 스크래핑을 사용하도록 설정합니다.

OSM 메트릭은 오픈 소스 시각화 및 분석 소프트웨어인 Grafana를 사용하여 볼 수 있습니다. 이를 통해 메트릭을 쿼리, 시각화, 경고 및 탐색할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> - Prometheus 인스턴스 만들기 및 배포
> - OSM을 구성하여 Prometheus 스크래핑 허용
> - 프로메테우스 업데이트 `Configmap`
> - Grafana 인스턴스 만들기 및 배포
> - Prometheus 데이터 원본을 사용하여 Grafana 구성
> - Grafana에 대한 OSM 대시보드 가져오기
> - Jaeger 인스턴스 만들기 및 배포
> - OSM에 대한 Jaeger 추적 구성

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.8.0 이상
- JSON 프로세서 "jq" 버전 1.6 이상

## <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>OSM에 대한 Prometheus 인스턴스 배포 및 구성

여기서는 Helm을 사용하여 Prometheus 인스턴스를 배포합니다. 다음 명령을 실행하여 Helm을 통해 Prometheus를 설치합니다.

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

설치가 완료되면 아래와 유사한 출력이 표시됩니다. Prometheus 서버 포트 및 클러스터 DNS 이름을 적어 둡니다. 이 정보는 나중에 Grafana에 대한 데이터 원본으로 Prometheus를 구성하는 데 사용됩니다.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

### <a name="configure-osm-to-allow-prometheus-scraping"></a>OSM을 구성하여 Prometheus 스크래핑 허용

OSM 구성 요소가 Prometheus 스크랩에 대해 구성되어 있는지 확인하기 위해 OSM 구성 파일에 있는 **prometheus_scraping** 구성을 확인하려고 합니다. 다음 명령을 사용하여 결과를 확인합니다.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

OSM이 Prometheus 스크래핑에 대해 구성된 경우 이전 명령의 출력에서 `true`가 반환되어야 합니다. 반환된 값이 `false`이면 구성을 `true`로 업데이트해야 합니다. 다음 명령을 실행하여 OSM Prometheus 스크래핑을 **설정** 합니다.

```azurecli-interactive
kubectl patch configmap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

다음 출력이 표시되어야 합니다.

```Output
configmap/osm-config patched
```

### <a name="update-the-prometheus-configmap"></a>Prometheus Configmap 업데이트

프로메테우스의 기본 설치에는 두 개의 Kubernetes 포함 됩니다 `configmaps` . 다음 명령을 사용 하 여 프로메테우스 목록을 볼 수 있습니다 `configmaps` .

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

**안정적인-프로메테우스-서버** 에 있는 `configmap` OSM 구성을 다음 구성으로 바꾸어야 합니다. 이 작업을 수행하는 데는 여러 가지 파일 편집 기술이 있습니다. 간단 하 고 안전한 방법은를 내보내고 `configmap` 백업에 대 한 복사본을 만든 다음 Visual Studio 코드와 같은 편집기를 사용 하 여 편집 하는 것입니다.

> [!NOTE]
> Visual Studio Code가 설치되어 있지 않은 경우 [여기](https://code.visualstudio.com/Download)에서 다운로드하여 설치할 수 있습니다.

먼저 **stable-prometheus-server** configmap을 내보낸 다음 백업용 복사본을 만듭니다.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

다음으로 Visual Studio Code를 사용하여 편집할 파일을 엽니다.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

`configmap`Visual Studio Code 편집기에서를 연 후에는 아래의 OSM 구성으로 프로메테우스 파일을 바꾸고 파일을 저장 합니다.

> [!WARNING]
> yaml 파일의 들여쓰기 구조를 유지하는 것은 매우 중요합니다. yaml 파일 구조를 조금이라도 변경하면 configmap을 다시 적용할 수 없습니다.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

`configmap`다음 명령을 사용 하 여 업데이트 된 yaml 파일을 적용 합니다.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> 누락된 Kubernetes 주석에 대한 메시지가 표시될 수 있습니다. 지금은 이를 무시해도 됩니다.

### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>OSM 메시 및 API 엔드포인트를 스크래핑하도록 Prometheus가 구성되었는지 확인합니다.

Prometheus가 OSM 메시 및 API 엔드포인트를 스크래핑하도록 올바르게 구성되었는지 확인하기 위해 Prometheus Pod에 포트를 전달하고 대상 구성을 확인합니다. 다음 명령을 실행합니다.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

브라우저에서 `http://localhost:9090/targets`를 엽니다.

아래로 스크롤하면 모든 SMI 메트릭 엔드포인트 상태가 **UP** 으로 표시되고 아래 그림과 같이 정의된 다른 OSM 메트릭이 표시됩니다.

![OSM Prometheus 대상 메트릭 UI 이미지](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

## <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>OSM에 대한 Grafana 인스턴스 배포 및 구성

Helm을 사용하여 Grafana 인스턴스를 배포합니다. 다음 명령을 실행하여 Helm을 통해 Grafana를 설치합니다.

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

다음으로 Grafana 사이트에 로그인하기 위한 기본 Grafana 암호를 검색합니다.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Grafana 암호를 기록합니다.

다음으로 Grafana Pod를 검색하여 로그인할 Grafana 대시보드로 포트를 전달합니다.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

브라우저에서 `http://localhost:3000`을 엽니다.

아래 그림에 나와 있는 로그인 화면에서 **admin** 을 사용자 이름으로 입력하고 앞에서 캡처한 Grafana 암호를 사용합니다.

![OSM Grafana 로그인 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-ui-login.png)

### <a name="configure-the-grafana-prometheus-data-source"></a>Grafana Prometheus 데이터 원본 구성

Grafana에 성공적으로 로그인한 다음 단계로 Grafana에 대한 데이터 원본으로 Prometheus를 추가합니다. 이렇게 하려면 왼쪽 메뉴의 구성 아이콘을 탐색하고 아래와 같이 데이터 원본을 선택합니다.

![OSM Grafana 데이터 원본 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

**데이터 원본 추가** 단추를 클릭하고 시계열 데이터베이스에서 Prometheus를 선택합니다.

![OSM Grafana 데이터 원본 선택 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

**아래에서 Prometheus 데이터 원본 구성** 페이지에서 HTTP URL 설정에 대한 Kubernetes 클러스터 FQDN을 Prometheus 서비스에 입력합니다. 기본 FQDN은 `stable-prometheus-server.default.svc.cluster.local`이어야 합니다. 해당 Prometheus 서비스 엔드포인트를 입력한 후 페이지 아래쪽으로 스크롤하고 **저장 및 테스트** 를 선택합니다. 데이터 원본이 작동 중임을 나타내는 녹색 확인란이 표시됩니다.

### <a name="importing-osm-dashboards"></a>OSM 대시보드 가져오기

OSM 대시보드는 다음 두 가지 방법을 통해 사용할 수 있습니다.

- [리포지토리](https://github.com/grafana/grafana)에서 웹 관리 포털을 통해 json Blob으로 가져오거나
- [Grafana.com에서 온라인으로](https://grafana.com/grafana/dashboards/14145) 가져올 수 있습니다.

대시보드를 가져오려면 왼쪽 메뉴에서 `+` 기호를 찾아 `import`를 선택합니다.
`Grafana.com`에서 대시보드를 ID로 직접 가져올 수 있습니다. 예를 들어, `OSM Mesh Details` 대시보드에 ID(`14145`)를 사용하는 경우 이 ID를 양식에 직접 사용하고 `import`를 선택할 수 있습니다.

![OSM Grafana 대시보드 가져오기 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

가져오기를 선택하는 즉시 가져온 대시보드로 자동으로 이동합니다.

![OSM Grafana 대시보드 가져오기 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

## <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>OSM에 대한 Kubernetes에 Jaeger 연산자 배포 및 구성

[Jaeger](https://www.jaegertracing.io/)는 분산 시스템을 모니터링하고 문제를 해결하는 데 사용되는 오픈 소스 추적 시스템입니다. OSM을 사용하여 새 인스턴스로 배포하거나 자체 인스턴스를 가져올 수 있습니다. 다음 지침에서는 AKS 클러스터의 `jaeger` 네임스페이스에 Jaeger의 새 인스턴스를 배포합니다.

### <a name="deploy-jaeger-to-the-aks-cluster"></a>AKS 클러스터에 Jaeger 배포

Jaeger를 설치하려면 다음 매니페스트를 적용합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

### <a name="enable-tracing-for-the-osm-add-on"></a>OSM 추가 기능에 대한 추적 사용

다음에는 OSM 추가 기능에 대한 추적을 사용하도록 설정해야 합니다.

다음 명령을 실행하여 OSM 추가 기능에 대한 추적을 사용하도록 설정합니다.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}' --type=merge
```

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

### <a name="view-the-jaeger-ui-with-port-forwarding"></a>포트 전달이 있는 Jaeger UI 보기

Jaeger의 UI가 포트 16686에서 실행되고 있습니다. Kubectl port-forward를 사용하여 웹 UI를 볼 수 있습니다.

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

브라우저에서 서비스 드롭다운이 표시되며, 이를 통해 bookstore 데모에서 배포된 다양한 애플리케이션을 선택할 수 있습니다. 모든 범위에서 볼 서비스를 선택합니다. 예를 들어 `bookbuyer` 1시간의 조회로 를 선택하면 booktore-v1 및 bookstore-v2와의 상호 작용이 시간별로 정렬된 것을 볼 수 있습니다.

![OSM Jaeger 추적 페이지 UI 이미지](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

항목을 선택하여 추가 세부 정보에서 확인합니다. 여러 항목을 선택하여 추적을 비교합니다. 예를 들어 `bookbuyer` 특정 시점에 의 책점 및 bookstore-v2와의 상호 작용을 비교할 수 있습니다.

시스템 아키텍처 탭을 선택하여 다양한 애플리케이션이 상호 작용하고 통신하는 방법에 대한 그래프를 볼 수도 있습니다. 이를 통해 애플리케이션 간에 트래픽이 흐르는 방식을 알 수 있습니다.

![OSM Jaeger 시스템 아키텍처 UI 이미지](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)