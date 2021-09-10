---
title: Azure API Management 자체 호스팅 게이트웨이의 로컬 메트릭 및 로그 구성 | Microsoft Docs
description: Kubernetes 클러스터에서 Azure API Management 자체 호스팅 게이트웨이의 로컬 메트릭 및 로그를 구성하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/11/2021
ms.author: apimpm
ms.openlocfilehash: f17d271315bc537f916cb4d9addfa7ea1eaec995
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115834"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Azure API Management 자체 호스팅 게이트웨이에 대한 로컬 메트릭 및 로그 구성

이 문서에서는 Kubernetes 클러스터에 배포된 [자체 호스팅 게이트웨이](./self-hosted-gateway-overview.md)의 로컬 메트릭 및 로그를 구성하는 방법에 대한 세부 정보를 제공합니다. 클라우드 메트릭과 로그를 구성하는 방법에 대해서는 [이 문서](how-to-configure-cloud-metrics-logs.md)를 참조하세요.

## <a name="metrics"></a>메트릭

자체 호스팅 게이트웨이는 메트릭 수집 및 집계의 통합 프로토콜이 된 [StatsD](https://github.com/statsd/statsd)를 지원합니다. 이 섹션에서는 StatsD를 Kubernetes에 배포하고, StatsD를 통해 메트릭을 내보내도록 게이트웨이를 구성하며, [Prometheus](https://prometheus.io/)를 사용하여 메트릭을 모니터링 하는 단계를 연습합니다.

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>클러스터에 StatsD 및 Prometheus 배포

다음은 Kubernetes 클러스터에 StatsD 및 Prometheus를 배포하여 자체 호스팅 게이트웨이가 배포되는 샘플 YAML 구성입니다. 또한 각각을 대상으로 한[서비스](https://kubernetes.io/docs/concepts/services-networking/service/)도 만듭니다. 자체 호스팅 게이트웨이에서는 StatsD 서비스에 메트릭을 게시합니다. 해당 서비스를 통해 Prometheus 대시보드에 액세스합니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: prom/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

`metrics.yaml`이라는 파일에 구성을 저장하고 아래 명령을 사용하여 클러스터에 모든 항목을 배포합니다.

```console
kubectl apply -f metrics.yaml
```

배포가 완료되면 아래 명령을 실행하여 Pod가 실행되고 있는지 확인합니다. Pod 이름은 다릅니다.

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

아래 명령을 실행하여 서비스가 실행 중인지 확인합니다. StatsD 서비스의`CLUSTER-IP`과 `PORT`를 기록해 두세요. 나중에 필요합니다. `EXTERNAL-IP`과 `PORT`를 사용하여 Prometheus 대시보드에 방문할 수 있습니다.

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>자체 호스팅 게이트웨이를 구성하여 메트릭 내보내기

이제 StatsD와 Prometheus가 모두 배포되었으므로, StatsD를 통해 메트릭 내보내기를 시작하도록 자체 호스팅 게이트웨이의 구성을 업데이트할 수 있습니다. 추가 옵션을 사용하여 자체 호스팅 게이트웨이 배포의 ConfigMap에서 `telemetry.metrics.local` 키를 사용하여 이 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다. 다음은 사용 가능한 옵션 내역입니다.

| 필드  | 기본값 | 설명 |
| ------------- | ------------- | ------------- |
| telemetry.metrics.local  | `none` | StatsD를 통해 로깅을 사용하도록 설정합니다. 값은 `none`, `statsd`여야 합니다. |
| telemetry.metrics.local.statsd.endpoint  | 해당 없음 | StatsD 엔드포인트를 지정합니다. |
| telemetry.metrics.local.statsd.sampling  | 해당 없음 | 메트릭 샘플링 주기를 지정합니다. 값은 0에서 1 사이여야 합니다. 예: `0.5`|
| telemetry.metrics.local.statsd.tag-format  | 해당 없음 | StatsD 내보내기 [태그 지정 형식](https://github.com/prometheus/statsd_exporter#tagging-extensions). 값은 `none`, `librato`, `dogStatsD`, `influxDB`가 될 수 있습니다. |

다음은 샘플 구성입니다.

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

위의 구성을 사용하여 자체 호스팅 게이트웨이 배포의 YAML 파일을 업데이트하고 아래 명령을 사용하여 변경 내용을 적용합니다.

```console
kubectl apply -f <file-name>.yaml
```

최신 구성 변경 사항을 선택하려면 다음 명령을 사용하여 게이트웨이 배포를 다시 시작합니다.

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>메트릭 보기

이제 모든 항목을 배포하고 구성했으므로 자체 호스팅 게이트웨이는 StatsD를 통해 메트릭을 보고해야 합니다. Prometheus는 StatsD에서 메트릭을 선택합니다. Prometheus 서비스의 `EXTERNAL-IP`과 `PORT`를 사용하여 Prometheus 대시보드로 이동합니다.

자체 호스팅 게이트웨이를 통해 일부 API 호출을 수행합니다. 모두 올바르게 구성된 경우 아래 메트릭을 볼 수 있습니다.

| 메트릭  | 설명 |
| ------------- | ------------- |
| 요청  | 해당 기간의 API 요청 수 |
| DurationInMS | 게이트웨이에서 요청을 수신한 순간부터 응답이 완전히 전송될 때까지 걸린 시간(밀리초) |
| BackendDurationInMS | 전체 백 엔드 IO(연결, 바이트 송신 및 수신)에 소요된 시간(밀리초)  |
| ClientDurationInMS | 전체 클라이언트 IO(연결, 바이트 송신 및 수신)에 소요된 시간(밀리초)  |

## <a name="logs"></a>로그

자체 호스팅 게이트웨이는 기본적으로 `stdout`과 `stderr`에 로그를 출력합니다. 다음 명령을 사용하여 로그를 쉽게 볼 수 있습니다.

```console
kubectl logs <pod-name>
```

자체 호스팅 게이트웨이가 Azure Kubernetes Service에 배포되는 경우, [컨테이너용 Azure Monitor](../azure-monitor/containers/container-insights-overview.md)를 사용하도록 설정하여 `stdout`과 `stderr`를 워크로드에서 수집하고 Log Analytics에서 로그를 볼 수 있습니다.

자체 호스팅 게이트웨이는 `localsyslog`, `rfc5424`, `journal`등의 다양한 프로토콜도 지원합니다. 아래 표에는 지원되는 모든 옵션이 요약되어 있습니다.

| 필드  | 기본값 | 설명 |
| ------------- | ------------- | ------------- |
| telemetry.logs.std  | `text` | 표준 스트림에 로깅을 사용하도록 설정합니다. 값은 `none`, `text`, `json`이 될 수 있습니다. |
| telemetry.logs.local  | `auto` | 로컬 로깅을 사용합니다. 값은 `none`, `auto`, `localsyslog`, `rfc5424`, `journal` `json`이 될 수 있습니다.  |
| telemetry.logs.local.localsyslog.endpoint  | 해당 없음 | localsyslog 엔드포인트를 지정합니다.  |
| telemetry.logs.local.localsyslog.facility  | 해당 없음 | localsyslog [기능 코드](https://en.wikipedia.org/wiki/Syslog#Facility)를 지정합니다. 예: `7`
| telemetry.logs.local.rfc5424.endpoint  | 해당 없음 | rfc5424 엔드포인트를 지정합니다.  |
| telemetry.logs.local.rfc5424.facility  | 해당 없음 | 각 [rfc5424](https://tools.ietf.org/html/rfc5424)의 기능 코드를 지정합니다. 예: `7`  |
| telemetry.logs.local.journal.endpoint  | 해당 없음 | 업무 일지 엔드포인트를 지정합니다.  |
| telemetry.logs.local.json.endpoint | 127.0.0.1:8888 | 파일 경로, IP:포트 또는 호스트 이름:포트와 같은 JSON 데이터를 허용하는 UDP 엔드포인트를 지정합니다.

로컬 로깅의 샘플 구성은 다음과 같습니다.

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대해 자세히 알아보려면 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md)를 참조하세요.
* [클라우드에서 로그를 구성하고 유지하는](how-to-configure-local-metrics-logs.md) 방법을 알아봅니다
