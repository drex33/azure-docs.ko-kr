---
title: 서비스 메시 열기 문제 해결
description: Open Service 메시 문제를 해결 하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 27a553bee765dd1369490cd9f6825cc44c48c59a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065668"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>OSM(Open Service Mesh) AKS 추가 기능 문제 해결 가이드 열기

OSM AKS 추가 기능을 배포 하는 경우 서비스 메시의 구성과 관련 된 문제가 발생할 수 있습니다. 다음 가이드에서는 오류 문제를 해결 하 고 일반적인 문제를 해결 하는 방법을 설명 합니다.

## <a name="verifying-and-troubleshooting-osm-components"></a>OSM 구성 요소 확인 및 문제 해결

### <a name="check-osm-controller-deployment-pod-and-service"></a>OSM Controller 배포, Pod 및 서비스를 확인 합니다.

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-controller
```

정상 OSM 컨트롤러는 다음과 같습니다.

```Output
NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-controller   2/2     2            2           3m4s

NAME                                  READY   STATUS    RESTARTS   AGE
pod/osm-controller-65bd8c445c-zszp4   1/1     Running   0          2m
pod/osm-controller-65bd8c445c-xqhmk   1/1     Running   0          16s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                       AGE
service/osm-controller   ClusterIP   10.96.185.178   <none>        15128/TCP,9092/TCP,9091/TCP   3m4s
service/osm-validator    ClusterIP   10.96.11.78     <none>        9093/TCP                      3m4s
```

> [!NOTE]
> Osm 컨트롤러 서비스의 경우 클러스터 IP가 다릅니다. 서비스 이름과 포트는 위의 예와 동일해야 합니다.

### <a name="check-osm-injector-deployment-pod-and-service"></a>OSM 인젝터 Deployment, Pod 및 Service 확인

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-injector
```

정상 OSM 인젝터는 다음과 같습니다.

```Output
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-injector   2/2     2            2           4m37s

NAME                                READY   STATUS    RESTARTS   AGE
pod/osm-injector-5c49bd8d7c-b6cx6   1/1     Running   0          4m21s
pod/osm-injector-5c49bd8d7c-dx587   1/1     Running   0          4m37s

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/osm-injector   ClusterIP   10.96.236.108   <none>        9090/TCP   4m37s
```

### <a name="check-osm-bootstrap-deployment-pod-and-service"></a>OSM 부트스트랩 배포, Pod 및 서비스를 확인 합니다.

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-bootstrap
```

정상 OSM 부트스트랩은 다음과 같습니다.

```Output
NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-bootstrap   1/1     1            1           5m25s

NAME                                 READY   STATUS    RESTARTS   AGE
pod/osm-bootstrap-594ffc6cb7-jc7bs   1/1     Running   0          5m25s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
service/osm-bootstrap   ClusterIP   10.96.250.208   <none>        9443/TCP,9095/TCP   5m25s
```

### <a name="check-validating-and-mutating-webhooks"></a>유효성 검사 웹후크 및 변경 웹후크 확인

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

정상 OSM 유효성 검사 웹후크는 다음과 같습니다.

```Output
NAME              WEBHOOKS   AGE
aks-osm-validator-mesh-osm   1      81m
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

### <a name="check-the-osm-mesh-config-resource"></a>리소스 확인 `osm-mesh-config`

존재 여부를 확인합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

OSM MeshConfig의 콘텐츠를 확인 합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
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
      address: jaeger.kube-system.svc.cluster.local
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
| 사양 트래픽. enableEgress | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| enablePermissiveTrafficPolicyMode | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":["10.0.0.0/32","1.1.1.1/24"]}}}'  --type=merge` |
| inboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"inboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| serviceCertValidityDuration | 문자열 | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| 관찰성. enableDebugServer | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"enableDebugServer":true}}}'  --type=merge` |
| 관찰성. 사용 | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| 관찰성. | 문자열 | `"jaeger.kube-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.kube-system.svc.cluster.local"}}}}'  --type=merge` |
| 관찰성. | 문자열 | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| 관찰성. | int | `9411`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| 관찰성. osmLogLevel | 문자열 | `"info"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"osmLogLevel": "info"}}}}'  --type=merge` |
| 사이드카. enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| 사이드카. logLevel | 문자열 | `"error"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| 사이드카. maxDataPlaneConnections | int | `0` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| 사이드카. envoyImage | 문자열 | `"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"envoyImage":"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"}}}'  --type=merge` |
| 사이드카. initContainerImage | 문자열 | `"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"initContainerImage":"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"}}}' --type=merge` |
| 사이드카. configResyncInterval | 문자열 | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |
| featureFlags. enableWASMStats | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableWASMStats":"true"}}}'  --type=merge` |
| featureFlags. enableEgressPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEgressPolicy":"true"}}}'  --type=merge` |
| featureFlags. enableMulticlusterMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableMulticlusterMode":"false"}}}'  --type=merge` |
| featureFlags. enableSnapshotCacheMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableSnapshotCacheMode":"false"}}}'  --type=merge` |
| featureFlags. enableAsyncProxyServiceMapping | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableAsyncProxyServiceMapping":"false"}}}'  --type=merge` |
| featureFlags. enableIngressBackendPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableIngressBackendPolicy":"true"}}}'  --type=merge` |
| featureFlags. enableEnvoyActiveHealthChecks | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEnvoyActiveHealthChecks":"false"}}}'  --type=merge` |


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
> `osm namespace add`가 호출 된 후 **new** pod는 엔보이 사이드카로 삽입 됩니다. 기존 Pod는 `kubectl rollout restart deployment ...`로 다시 시작해야 합니다.

### <a name="verify-osm-crds"></a>OSM CRDs 확인:

클러스터에 필요한 CRD가 있는지 확인합니다.

```azurecli-interactive
kubectl get crds
```

다음이 클러스터에 설치되어 있어야 합니다.

- egresses.policy.openservicemesh.io
- httproutegroups.specs.smi-spec.io 
- ingressbackends.policy.openservicemesh.io
- meshconfigs.config openservicemesh.io
- multiclusterservices.config openservicemesh.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io

다음 명령을 통해 설치된 SMI CRD 버전을 다운로드합니다.

```azurecli-interactive
osm mesh list
```

예상 출력:

```
MESH NAME   MESH NAMESPACE   VERSION   ADDED NAMESPACES
osm         kube-system      v0.11.1

MESH NAME   MESH NAMESPACE   SMI SUPPORTED
osm         kube-system      HTTPRouteGroup:v1alpha4,TCPRoute:v1alpha4,TrafficSplit:v1alpha2,TrafficTarget:v1alpha3

To list the OSM controller pods for a mesh, please run the following command passing in the mesh's namespace
        kubectl get pods -n <osm-mesh-namespace> -l app=osm-controller
```

OSM 컨트롤러 v0.11.1에는 다음 버전이 필요합니다.

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io- 지원되지 않음
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)


### <a name="certificate-management"></a>인증서 관리

OSM이 애플리케이션 Pod에서 실행되는 Envoy 프록시에 대한 인증서를 발급하고 관리하는 방법에 대한 정보는 [OpenServiceMesh 문서 사이트에서](https://docs.openservicemesh.io/docs/guides/certificates/)찾을 수 있습니다.

### <a name="upgrading-envoy"></a>Envoy 업그레이드

추가 기능으로 모니터링되는 네임스페이스에 새 Pod가 만들어지면 OSM은 해당 Pod에 [envoy 프록시 사이드카를](https://docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) 삽입합니다. envoy 버전을 업데이트하는 방법에 대한 정보는 OpenServiceMesh 문서 사이트의 [업그레이드 가이드에서](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/upgrade/#envoy) 찾을 수 있습니다.
