---
title: Open Service Mesh 문제 해결
description: Open Service Mesh 문제를 해결하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 0394815b4e75fb8d1f1f277010602839dc6876eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441278"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>OSM(Open Service Mesh) AKS 추가 기능 문제 해결 가이드 열기

OSM AKS 추가 기능 배포 시 서비스 메시 구성과 관련된 문제가 발생할 수 있습니다. 다음 가이드에서는 오류를 해결하고 일반적인 문제를 해결하는 방법을 안내합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verifying-and-troubleshooting-osm-components"></a>OSM 구성 요소 확인 및 문제 해결

### <a name="check-osm-controller-deployment"></a>OSM 컨트롤러 배포 확인

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

정상 OSM 컨트롤러는 다음과 같습니다.

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="check-the-osm-controller-pod"></a>OSM 컨트롤러 Pod를 확인합니다.

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

정상 OSM Pod는 다음과 같습니다.

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

특정 시점에 하나의 컨트롤러를 제거한 경우에도 준비 열이 1/1이고 다시 시작한 횟수가 0번인 실행되는 다른 컨트롤러가 있습니다. 준비 열이 1/1이 아닌 경우 서비스 메시는 중단된 상태에 있게 됩니다.
준비 열이 0/1이면 컨트롤 플레인 컨테이너가 충돌함을 나타내는 것이며, 로그를 가져와야 합니다. 아래의 Azure 지원 센터에서 OSM 컨트롤러 로그 가져오기 섹션을 참조하세요. 준비 열의 / 기호 뒤에 1보다 큰 숫자가 있으면 사이드카가 설치되어 있는 것입니다. 사이드카가 연결되어 있는 경우 OSM 컨트롤러는 작동하지 않을 가능성이 높습니다.

### <a name="check-osm-controller-service"></a>OSM 컨트롤러 서비스 확인

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

정상 OSM 컨트롤러 서비스는 다음과 같습니다.

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> 클러스터 IP는 다를 수 있습니다. 서비스 이름과 포트는 위의 예와 동일해야 합니다.

### <a name="check-osm-controller-endpoints"></a>OSM 컨트롤러 엔드포인트 확인

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

정상 OSM 컨트롤러 엔드포인트는 다음과 같습니다.

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

### <a name="check-osm-injector-deployment"></a>OSM 인젝터 배포 확인

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

정상 OSM 인젝터 배포는 다음과 같습니다.

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-pod"></a>OSM 인젝터 배포 확인

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

정상 OSM 인젝터 Pod는 다음과 같습니다.

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-service"></a>OSM 인젝터 서비스 확인

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

정상 OSM 인젝터 서비스는 다음과 같습니다.

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

### <a name="check-osm-endpoints"></a>OSM 엔드포인트 확인

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

정상 OSM 엔드포인트는 다음과 같습니다.

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

### <a name="check-validating-and-mutating-webhooks"></a>유효성 검사 웹후크 및 변경 웹후크 확인

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

정상 OSM 유효성 검사 웹후크는 다음과 같습니다.

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

정상 OSM 변경 웹후크는 다음과 같습니다.

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>유효성 검사 웹후크의 서비스 및 CA 번들을 확인합니다.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

잘 구성된 유효성 검사 웹후크 구성은 다음과 같습니다.

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>변경 웹후크의 서비스 및 CA 번들을 확인합니다.

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

잘 구성된 변경 웹후크 구성은 다음과 같습니다.

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>OSM 컨트롤러에 유효성 검사(또는 변경) 웹후크가 제공되는지 확인합니다.

> [!NOTE]
> 버전 0.8.2에서부터는 AKS RP가 유효성 검사 웹후크를 설치한다는 것을 알고 있어야 합니다. AKS 조정자는 존재를 확인하지만 CA 번들을 채우는 역할을 하는 것은 OSM 컨트롤러입니다.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

이 숫자는 CA 번들의 바이트 수 또는 크기를 나타냅니다. 이 값이 비어 있거나, 0 또는 1000보다 작은 숫자이면 CA 번들이 올바르게 프로비저닝되지 않았음을 나타내는 것입니다. 올바른 CA 번들을 사용할 수 없으면 Webhook 유효성 검사에서 오류가 발생하며 사용자가 kube-system 네임스페이스에서 osm-config ConfigMap을 변경하는 것을 금지합니다.

CA 번들이 잘못된 경우의 샘플 오류:

- osm-config ConfigMap을 변경하려고는 시도:

  ```azurecli-interactive
  kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```

- 오류:

  ```
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

**유효성 검사** 웹후크 구성에 잘못된 인증서가 있는 경우에 대한 해결 방법:

- 옵션 1- OSM 컨트롤러 다시 시작 - OSM 컨트롤러를 다시 시작합니다. 시작 시에는 변경 웹후크 및 유효성 검사 웹후크의 CA 번들을 덮어씁니다.

  ```azurecli-interactive
  kubectl rollout restart deployment -n kube-system osm-controller
  ```

- 옵션 2 - 옵션 2. 유효성 검사 웹후크 삭제 - 유효성 검사 웹후크를 제거하면 `osm-config` ConfigMap의 변경은 더 이상 유효하지 않게 됩니다. 모든 패치가 진행됩니다. AKS 조정자는 이후 유효성 검사 웹후크가 있는지 확인하고 다시 만들 것입니다. CA 번들을 신속하게 다시 작성하려면 OSM 컨트롤러를 다시 시작해야 할 수 있습니다.

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
  ```

- 옵션 3 - 삭제 및 패치: 다음 명령은 유효성 검사 웹후크를 삭제하고, 값을 추가하도록 허용하고, 즉시 패치 적용을 시도합니다. AKS 조정자에게 유효성 검사 웹후크를 조정하고 복원할 시간이 충분하지 않으므로 이는 최후의 수단으로 변경 내용을 적용할 기회입니다.

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```

### <a name="check-the-osm-mesh-config-resource"></a>리소스 확인 `osm-mesh-config`

존재 여부를 확인합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

OSM MeshConfig의 콘텐츠 확인

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

`osm-mesh-config` 리소스 값:

| 키 | 형식 | 기본값 | Kubectl Patch 명령 예제 |
|-----|------|---------------|--------------------------------|
| spec.traffic.enableEgress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundPortExclusionList":6379,8080}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":"10.0.0.0/32,1.1.1.1/24"}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | 문자열 | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"serviceCertValidityDuration":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `"jaeger.osm-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.osm-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.address | 문자열 | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.endpoint | 문자열 | `false`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.port | int | `9411`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | 문자열 | `"error"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | int | `0` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | 문자열 | `"envoyproxy/envoy-alpine:v1.17.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"envoyImage":"envoyproxy/envoy-alpine:v1.17.2"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | 문자열 | `"openservicemesh/init:v0.9.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"initContainerImage":"openservicemesh/init:v0.9.2"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | 문자열 | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |

### <a name="check-namespaces"></a>네임스페이스 확인

> [!NOTE]
> kube-system 네임스페이스는 서비스 메시에 참여하지 않으며 아래의 키/값으로 레이블이 지정되거나 주석이 추가되지 않습니다.

`osm namespace add` 명령을 사용하여 지정된 서비스 메시에 네임스페이스를 조인합니다.
k8s 네임스페이스가 메시의 일부이거나 메시의 일부가 될 경우 다음을 충족해야 합니다.

다음을 사용하여 주석 보기

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

다음과 같은 주석이 있어야 합니다.

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

다음을 사용하여 레이블 보기

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

다음과 같은 레이블이 있어야 합니다.

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

네임스페이스에 주석이 `"openservicemesh.io/sidecar-injection": "enabled"`로 지정되어 있지 않거나 레이블이 `"openservicemesh.io/monitored-by": "osm"`으로 지정되지 않은 경우 OSM 인젝터는 Envoy 사이드카를 추가하지 않습니다.

> [!NOTE]
> `osm namespace add`가 호출된 후에는 **새** Pod만 Envoy 사이드카와 함께 삽입됩니다. 기존 Pod는 `kubectl rollout restart deployment ...`로 다시 시작해야 합니다.

### <a name="verify-the-smi-crds"></a>SMI CRD를 확인합니다.

클러스터에 필요한 CRD가 있는지 확인합니다.

```azurecli-interactive
kubectl get crds
```

다음이 클러스터에 설치되어 있어야 합니다.

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

다음 명령을 사용하여 설치된 CRD의 버전을 가져옵니다.

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

예상 출력:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSM 컨트롤러 버전 0.8.2에는 다음 버전이 필요합니다.

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io- 지원되지 않음
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

CRD가 없으면 다음 명령을 사용하여 클러스터에 설치합니다.

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```