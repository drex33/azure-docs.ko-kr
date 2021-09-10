---
title: Container Insights Prometheus 통합 구성 | Microsoft Docs
description: 이 문서에서는 Kubernetes 클러스터를 사용하여 Prometheus에서 메트릭을 스크래핑하도록 Container Insights 에이전트를 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 441b468f71f0d134a503418b3fde64b758a033a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528793"
---
# <a name="configure-scraping-of-prometheus-metrics-with-container-insights"></a>Container Insights를 사용하여 Prometheus 메트릭 스크래핑 구성

[Prometheus](https://prometheus.io/)는 널리 사용되는 오픈 소스 메트릭 모니터링 솔루션으로, [Cloud Native Compute Foundation](https://www.cncf.io/)의 일부입니다. Container Insights는 Prometheus 메트릭을 수집하기 위한 원활한 온보딩 환경을 제공합니다. 일반적으로 Prometheus를 사용하려면 저장소를 사용하여 Prometheus 서버를 설정하고 관리해야 합니다. Azure Monitor와 통합하면 Prometheus 서버가 필요하지 않습니다. 내보내기 또는 Pod(애플리케이션)를 통해 Prometheus 메트릭 엔드포인트를 공개해야 하며 Container Insights의 컨테이너화된 에이전트는 메트릭을 스크래핑할 수 있습니다. 

![Prometheus용 컨테이너 모니터링 아키텍처](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Prometheus 메트릭을 스크래핑하는 데 지원되는 최소 에이전트 버전은 ciprod07092019 이상이며 `KubeMonAgentEvents` 테이블에서 구성 및 에이전트 오류를 기록하는 데 지원되는 에이전트 버전은 ciprod10112019입니다. Azure Red Hat OpenShift 및 Red Hat OpenShift v4의 경우 에이전트 버전 ciprod04162020 이상 
>
>에이전트 버전 및 각 릴리스에 포함되는 내용에 대한 자세한 내용은 [에이전트 릴리스 정보](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)를 참조하세요. 
>에이전트 버전을 확인하려면 **노드** 탭에서 노드를 선택하고 속성 창에서 **에이전트 이미지 태그** 속성의 값을 확인합니다.

Prometheus 메트릭 스크래핑은 다음에 호스트된 Kubernetes 클러스터에서 지원됩니다.

- AKS(Azure Kubernetes Service)
- Azure Stack 또는 온-프레미스
- Azure Red Hat OpenShift 버전 3.x
- Azure Red Hat OpenShift 및 Red Hat OpenShift 버전 4.x

### <a name="prometheus-scraping-settings"></a>Prometheus 스크래핑 설정

Prometheus의 활성 메트릭 스크래핑은 다음 두 가지 관점 중 하나에서 수행됩니다.

* 클러스터 전체 - HTTP URL로, 서비스의 나열된 엔드포인트에서 대상을 검색합니다. kube-dns 및 kube-state-metrics와 같은 k8s 서비스 및 애플리케이션 관련 pod 주석을 예로 들 수 있습니다. 이 컨텍스트에서 수집된 메트릭은 ConfigMap 섹션 *[Prometheus data_collection_settings. cluster]* 에 정의됩니다.
* 노드 전체 - HTTP URL로, 서비스의 나열된 엔드포인트에서 대상을 검색합니다. 이 컨텍스트에서 수집된 메트릭은 ConfigMap 섹션 *[Prometheus_data_collection_settings.node]* 에 정의됩니다.

| 엔드포인트 | 범위 | 예제 |
|----------|-------|---------|
| Pod 주석 | 클러스터 전체 | 주석: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 서비스 | 클러스터 전체 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/엔드포인트 | 노드당 및/또는 클러스터 전체 | `http://myurl:9101/metrics` |

URL을 지정하면 Cpontainer Insights는 엔드포인트를 수집합니다. Kubernetes 서비스를 지정하면 서비스 이름이 클러스터 DNS 서버를 통해 확인된 후 IP 주소를 가져오고, 확인된 서비스가 스크래핑됩니다.

|범위 | 키 | 데이터 형식 | 값 | Description |
|------|-----|-----------|-------|-------------|
| 클러스터 전체 | | | | 다음 세 가지 방법 중 하나를 지정하여 엔드포인트의 메트릭을 수집합니다. |
| | `urls` | String | 쉼표로 구분된 배열 | HTTP 엔드포인트(IP 주소 또는 올바른 URL 경로 중 하나)입니다. 예: `urls=[$NODE_IP/metrics]` ($NODE _IP는 특정 Container Insights 매개 변수이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자여야 합니다.) |
| | `kubernetes_services` | String | 쉼표로 구분된 배열 | kube-state-metrics에서 메트릭을 수집하는 Kubernetes 서비스 배열입니다. 여기서는 정규화된 도메인 이름을 사용해야 합니다. 예를 들면 `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace.svc.cluster.local:9100/metrics]`입니다.|
| | `monitor_kubernetes_pods` | 부울 | true 또는 false | 클러스터 전체 설정에서 `true`로 설정된 경우 Container Insights 에이전트는 다음 Prometheus 주석에 대해 전체 클러스터에서 Kubernetes Pod를 스크래핑합니다.<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | 부울 | true 또는 false | Pod의 스크래핑을 사용하도록 설정합니다. `monitor_kubernetes_pods`은 `true`로 설정해야 합니다. |
| | `prometheus.io/scheme` | String | HTTP 또는 HTTPS | 기본값은 HTTP를 통한 스크래핑입니다. 필요한 경우 `https`로 실행합니다. | 
| | `prometheus.io/path` | String | 쉼표로 구분된 배열 | 메트릭을 가져올 HTTP 리소스 경로입니다. 메트릭 경로가 `/metrics`가 아닌 경우에는 이 주석을 사용하여 정의합니다. |
| | `prometheus.io/port` | String | 9102 | 스크래핑할 포트를 지정합니다. 포트가 설정되지 않은 경우 기본값은 9102입니다. |
| | `monitor_kubernetes_pods_namespaces` | String | 쉼표로 구분된 배열 | Kubernetes Pod에서 메트릭을 스크래핑할 네임스페이스의 허용 목록입니다.<br> 예를 들어 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 노드 전체 | `urls` | String | 쉼표로 구분된 배열 | HTTP 엔드포인트(IP 주소 또는 올바른 URL 경로 중 하나)입니다. 예: `urls=[$NODE_IP/metrics]` ($NODE _IP는 특정 Container Insights 매개 변수이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자여야 합니다.) |
| 노드 전체 또는 클러스터 전체 | `interval` | String | 60초 | 수집 간격 기본값은 1분(60초)입니다. ‘[prometheus_data_collection_settings.node]’ 및/또는 ‘[prometheus_data_collection_settings.cluster]’에 대한 수집을 시간 단위(예: s, m, h)로 수정할 수 있습니다. |
| 노드 전체 또는 클러스터 전체 | `fieldpass`<br> `fielddrop`| String | 쉼표로 구분된 배열 | 허용(`fieldpass`) 및 허용 안 함(`fielddrop`) 목록을 설정하여 엔드포인트에서 수집할 메트릭 및 수집하지 않을 메트릭을 지정할 수 있습니다. 먼저 허용 목록을 설정해야 합니다. |

ConfigMap은 글로벌 목록으로, 에이전트에는 하나의 ConfigMap만 적용할 수 있습니다. 다른 ConfigMap을 컬렉션에 과도하게 사용할 수 없습니다.

## <a name="configure-and-deploy-configmaps"></a>ConfigMap 구성 및 배포

다음 단계를 수행하여 다음 클러스터에 대한 ConfigMap 구성 파일을 구성합니다.

* AKS(Azure Kubernetes Service)
* Azure Stack 또는 온-프레미스
* Azure Red Hat OpenShift 버전 4.x 및 Red Hat OpenShift 버전 4.x

1. 템플릿 ConfigMap yaml 파일을 [다운로드](https://aka.ms/container-azm-ms-agentconfig)하고 container-azm-ms-agentconfig.yaml로 저장합니다.

   >[!NOTE]
   >Azure Red Hat OpenShift를 사용하는 경우에는 ConfigMap 템플릿이 클러스터에 이미 있기 때문에 이 단계가 필요하지 않습니다.

2. 사용자 지정 항목을 사용하여 ConfigMap yaml 파일을 편집하고 Prometheus 메트릭을 스크래핑합니다.

    >[!NOTE]
    >Azure Red Hat OpenShift에 대한 ConfigMap yaml 파일을 편집하는 경우 먼저 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 명령을 실행하여 텍스트 편집기에서 파일을 엽니다.

    >[!NOTE]
    >조정을 방지하려면 *container-azm-ms-agentconfig* ConfigMap의 메타데이터 아래에 다음 주석 `openshift.io/reconcile-protect: "true"`를 추가해야 합니다. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 클러스터 전체에서 Kubernetes 서비스를 수집하려면 다음 예제를 사용하여 ConfigMap 파일을 구성합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 클러스터의 특정 URL에서 Prometheus 메트릭의 스크래핑을 구성하려면 다음 예제를 사용하여 ConfigMap 파일을 구성합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 클러스터의 모든 개별 노드에 대해 에이전트 DaemonSet의 Prometheus 메트릭 스크래핑을 구성하려면 ConfigMap에서 다음을 구성합니다.
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP는 특정 Container Insights 매개 변수이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자여야 합니다. 

    - Pod 주석을 지정하여 스크랩의 Prometheus 메트릭 스크래핑을 구성하려면 다음 단계를 수행합니다.

       1. ConfigMap에서 다음을 지정합니다.

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Pod 주석에 대해 다음 구성을 지정합니다.

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          주석을 포함하는 Pod에 대한 특정 네임스페이스로 모니터링을 제한하려면(예: 프로덕션 워크로드 전용 pod만 포함) ConfigMap에서 `monitor_kubernetes_pod`를 `true`로 설정하고 스크래핑할 네임스페이스를 지정하여 네임스페이스 필터 `monitor_kubernetes_pods_namespaces`를 추가합니다. 예를 들어 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. kubectl 명령 `kubectl apply -f <configmap_yaml_file.yaml>`을 실행합니다.
    
    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

구성 변경 내용을 적용하는 데 몇 분 정도 걸릴 수 있고 클러스터의 모든 omsagent Pod가 다시 시작됩니다. 다시 시작은 모두 동시에 다시 시작되는 것이 아니라, 모든 omsagent Pod에 대한 순환 방식의 다시 시작입니다. 다시 시작이 완료되면 결과를 포함하는 `configmap "container-azm-ms-agentconfig" created`와 유사한 메시지가 표시됩니다.

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>ConfigMaps 구성 및 배포 - Azure Red Hat OpenShift v3

이 섹션에는 Azure Red Hat OpenShift v3.x 클러스터에 대한 ConfigMap 구성 파일을 성공적으로 구성하기 위한 요구 사항 및 단계가 포함되어 있습니다.

>[!NOTE]
>Azure Red Hat OpenShift v3.x의 경우 ‘openshift-azure-logging’ 네임스페이스에 템플릿 configmap 파일이 만들어집니다. 에이전트에서 메트릭 또는 데이터 수집을 적극적으로 스크래핑하도록 구성되어 있지는 않습니다.

### <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 컨테이너화된 에이전트 및 Prometheus 스크래핑 설정을 구성하려면 Azure Red Hat OpenShift 클러스터의 고객 클러스터 관리자 역할의 멤버인지 확인합니다. *osa-customer-admins* 그룹의 멤버인지 확인하려면 다음 명령을 실행합니다.

``` bash
  oc get groups
```

출력은 다음과 유사합니다.

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

*osa-customer-admins* 그룹의 멤버인 경우 다음 명령을 사용하여 `container-azm-ms-agentconfig` ConfigMap을 나열할 수 있습니다.

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

출력은 다음과 유사합니다.

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>모니터링 사용

다음 단계를 수행하여 Azure Red Hat OpenShift v3.x 클러스터에 대한 ConfigMap 구성 파일을 구성합니다.

1. 사용자 지정 항목을 사용하여 ConfigMap yaml 파일을 편집하고 Prometheus 메트릭을 스크래핑합니다. ConfigMap 템플릿은 Red Hat OpenShift v3 클러스터에 이미 있습니다. `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 명령을 실행하여 텍스트 편집기에서 파일을 엽니다.

    >[!NOTE]
    >조정을 방지하려면 *container-azm-ms-agentconfig* ConfigMap의 메타데이터 아래에 다음 주석 `openshift.io/reconcile-protect: "true"`를 추가해야 합니다. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 클러스터 전체에서 Kubernetes 서비스를 수집하려면 다음 예제를 사용하여 ConfigMap 파일을 구성합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 클러스터의 특정 URL에서 Prometheus 메트릭의 스크래핑을 구성하려면 다음 예제를 사용하여 ConfigMap 파일을 구성합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 클러스터의 모든 개별 노드에 대해 에이전트 DaemonSet의 Prometheus 메트릭 스크래핑을 구성하려면 ConfigMap에서 다음을 구성합니다.
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP는 특정 Container Insights 매개 변수이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자여야 합니다. 

    - Pod 주석을 지정하여 스크랩의 Prometheus 메트릭 스크래핑을 구성하려면 다음 단계를 수행합니다.

       1. ConfigMap에서 다음을 지정합니다.

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Pod 주석에 대해 다음 구성을 지정합니다.

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          주석을 포함하는 Pod에 대한 특정 네임스페이스로 모니터링을 제한하려면(예: 프로덕션 워크로드 전용 pod만 포함) ConfigMap에서 `monitor_kubernetes_pod`를 `true`로 설정하고 스크래핑할 네임스페이스를 지정하여 네임스페이스 필터 `monitor_kubernetes_pods_namespaces`를 추가합니다. 예를 들어 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. 편집기에서 변경 내용을 저장합니다.

구성 변경 내용을 적용하는 데 몇 분 정도 걸릴 수 있고 클러스터의 모든 omsagent Pod가 다시 시작됩니다. 다시 시작은 모두 동시에 다시 시작되는 것이 아니라, 모든 omsagent Pod에 대한 순환 방식의 다시 시작입니다. 다시 시작이 완료되면 결과를 포함하는 `configmap "container-azm-ms-agentconfig" created`와 유사한 메시지가 표시됩니다.

`oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 명령을 실행하여 업데이트된 ConfigMap을 볼 수 있습니다. 

## <a name="applying-updated-configmap"></a>업데이트된 ConfigMap 적용

클러스터에 ConfigMap을 이미 배포했으며 최신 구성으로 업데이트하려는 경우 이전에 사용한 ConfigMap 파일을 편집한 다음, 이전과 동일한 명령을 사용하여 적용할 수 있습니다.

다음 Kubernetes 환경의 경우:

- AKS(Azure Kubernetes Service)
- Azure Stack 또는 온-프레미스
- Azure Red Hat OpenShift 및 Red Hat OpenShift 버전 4.x

`kubectl apply -f <configmap_yaml_file.yaml` 명령을 실행합니다. 

Azure Red Hat OpenShift v3.x 클러스터의 경우 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 명령을 실행하여 기본 편집기에서 파일을 열고 수정한 후 저장합니다.

구성 변경 내용을 적용하는 데 몇 분 정도 걸릴 수 있고 클러스터의 모든 omsagent Pod가 다시 시작됩니다. 다시 시작은 모두 동시에 다시 시작되는 것이 아니라, 모든 omsagent Pod에 대한 순환 방식의 다시 시작입니다. 다시 시작이 완료되면 다음과 유사한 메시지가 표시되고 결과(`configmap "container-azm-ms-agentconfig" updated`)가 포함됩니다.

## <a name="verify-configuration"></a>구성 확인

구성이 클러스터에 성공적으로 적용되었는지 확인하려면 명령 `kubectl logs omsagent-fdf58 -n=kube-system`을 사용하여 에이전트 Pod에서 로그를 검토합니다. 

>[!NOTE]
>이 명령은 Azure Red Hat OpenShift v3.x 클러스터에 적용되지 않습니다.
> 

omsagent Pod에 구성 오류가 있는 경우 출력에 다음과 유사한 오류가 표시됩니다.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

구성 변경 내용 적용과 관련한 오류도 검토할 수 있습니다. 다음 옵션을 사용하여 구성 변경 및 Prometheus 메트릭 스크래핑에 대한 추가적인 문제 해결을 수행할 수 있습니다.

- 같은 `kubectl logs` 명령을 사용하여 에이전트 Pod 로그에서 
    >[!NOTE]
    >이 명령은 Azure Red Hat OpenShift 클러스터에 적용되지 않습니다.
    > 

- 라이브 데이터(미리 보기)에서. 라이브 데이터(미리 보기) 로그에는 다음과 비슷한 오류가 표시됩니다.

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Log Analytics 작업 영역에 있는 **KubeMonAgentEvents** 테이블. 스크래핑 오류에 대해 *경고* 심각도가 지정되고, 구성 오류에 대해 *오류* 심각도가 지정된 상태로 매시간 데이터가 전송됩니다. 오류가 없는 경우 테이블의 항목에 있는 데이터는 오류를 보고하지 않는 *정보* 심각도입니다. **Tags** 속성은 오류가 발생한 Pod 및 컨테이너 ID에 대한 상세 정보와, 첫 번째 발생, 마지막 발생 및 지난 한 시간 동안의 횟수를 포함합니다.

- Azure Red Hat OpenShift v3.x 및 v4.x의 경우 **ContainerLog** 테이블 검색을 통해 omsagent 로그를 점검하여 openshift-azure-logging의 로그 수집을 사용하는지 확인합니다.

오류가 발생하면 omsagent에서 파일을 구문 분석하지 못하게 되어 다시 시작되고 기본 구성을 사용하게 됩니다. Azure Red Hat OpenShift v3.x가 아닌 클러스터의 ConfigMap에서 오류를 수정한 후 yaml 파일을 저장하고 다음 명령을 실행하여 업데이트된 ConfigMap을 적용합니다: `kubectl apply -f <configmap_yaml_file.yaml`. 

Azure Red Hat OpenShift v3.x의 경우 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 명령을 실행하여 업데이트된 ConfigMap을 편집 및 저장합니다.

## <a name="query-prometheus-metrics-data"></a>Prometheus 메트릭 데이터 쿼리

Azure Monitor에서 스크래핑하는 Prometheus 메트릭과 에이전트에서 보고하는 모든 구성/스크래핑 오류를 보려면 [Prometheus 메트릭 데이터 쿼리](container-insights-log-query.md#query-prometheus-metrics-data) 및 [구성 또는 스크래핑 오류 쿼리](container-insights-log-query.md#query-config-or-scraping-errors)를 검토합니다.

## <a name="view-prometheus-metrics-in-grafana"></a>Grafana에서 Prometheus 메트릭 보기

Container Insights는 Grafana 대시보드의 Log Analytics 작업 영역에 저장된 메트릭을 볼 수 있도록 지원합니다. Grafana의 [대시보드 리포지토리](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)에서 다운로드하여 시작하고, 모니터링할 클러스터에서 추가 데이터를 쿼리하여 사용자 지정 Grafana 대시보드에서 시각화하는 방법을 습득하는 데 도움이 되기 위해 참조할 수 있는 템플릿을 제공했습니다. 

## <a name="review-prometheus-data-usage"></a>Prometheus 데이터 사용량 검토

각 메트릭의 수집 볼륨 크기를 GB/일 단위로 식별하여 높은지 여부를 파악할 수 있도록 다음 쿼리가 제공됩니다.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

출력은 다음과 유사한 결과를 표시합니다.

![데이터 수집 볼륨의 로그 쿼리 결과를 보여 주는 스크린샷](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

1달 동안의 GB 단위의 각 메트릭 크기를 평가하여 작업 영역에서 수신되고 수집된 데이터의 양이 높은지 여부를 파악할 수 있도록 다음 쿼리가 제공됩니다.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

출력은 다음과 유사한 결과를 표시합니다.

![데이터 수집 볼륨의 쿼리 결과를 기록합니다.](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

데이터 사용량을 모니터링하고 비용을 분석하는 방법에 대한 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../logs/manage-cost-storage.md)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[여기](container-insights-agent-config.md)에서 컨테이너 워크로드의 stdout, stderr 및 환경 변수에 대한 에이전트 컬렉션 설정 구성에 대해 자세히 알아보세요.
