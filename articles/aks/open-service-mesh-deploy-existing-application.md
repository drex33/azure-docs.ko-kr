---
title: Open Service Mesh를 통해 기존 애플리케이션 관리
description: Open Service Mesh를 사용하여 기존 애플리케이션을 관리하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 092e42a5f9c1779fc5968b9fc733d260d405a90a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625908"
---
# <a name="manage-an-existing-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>AKS(Open Service Mesh) Azure Kubernetes Service(OPEN Service Mesh) 추가 기능으로 기존 애플리케이션 관리

## <a name="before-you-begin"></a>시작하기 전에

이 워크스루에 자세히 설명된 단계에서는 이전에 AKS 클러스터에 대해 OSM AKS 추가 기능을 사용하도록 설정했다고 가정합니다. 그렇지 않은 경우 계속하기 전에 [OSM AKS 추가](./open-service-mesh-deploy-addon-az-cli.md) 기능 배포 문서를 검토합니다. 또한 AKS 클러스터는 Kubernetes 버전 `1.19+` 이상이어야 하고, Kubernetes RBAC를 사용하도록 설정하고, 클러스터와의 `kubectl` 연결을 설정해야 합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.8.0 이상
- JSON 프로세서 "jq" 버전 1.6 이상

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>OSM(Open Service Mesh) 허용 트래픽 모드 정책을 확인합니다.

OSM 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

클러스터에 대한 OSM의 현재 허용 트래픽 모드를 확인하려면 다음 명령을 실행합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

OSM MeshConfig의 출력은 다음과 같습니다.

```Output
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

**enablePermissiveTrafficPolicyMode가** **true로** 구성된 경우 서비스 간 통신을 중단하지 않고 네임스페이스를 안전하게 온보딩할 수 있습니다. **enablePermissiveTrafficPolicyMode가** **false로** 구성된 경우 올바른 [SMI](https://smi-spec.io/) 트래픽 액세스 정책 매니페스트가 배포되었는지 확인해야 합니다. 또한 네임스페이스에 배포된 각 서비스를 나타내는 서비스 계정이 있는지 확인해야 합니다. 허용 트래픽 모드에 대한 자세한 내용은 을 방문하여 [허용 트래픽 정책 모드](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/) 문서를 읽어보십시오.

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>OSM(Open Service Mesh) 허용 트래픽 정책을 True로 구성하여 배포된 기존 애플리케이션 온보딩

가장 먼저 수행할 작업은 배포된 애플리케이션 네임스페이스를 OSM에 추가하여 관리하는 것입니다. 아래 예제는 **OSM에 책갈피** 네임스페이스를 온보딩합니다.

```azurecli-interactive
osm namespace add bookstore
```

다음과 같은 출력이 표시됩니다.

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

다음으로 네임스페이스에서 현재 Pod 배포를 살펴보겠습니다. 다음 명령을 실행하여 네임스페이스에서 Pod를 `bookbuyer` 봅니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

출력은 다음과 같이 표시됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

**준비** 열에 **1/1** 이 표시되면 애플리케이션 Pod에 하나의 컨테이너만 있다는 것을 의미합니다. 다음으로 OSM이 Envoy 사이드카 프록시 컨테이너를 애플리케이션 Pod에 삽입할 수 있도록 애플리케이션 배포를 다시 시작해야 합니다. 네임스페이스의 배포 목록을 살펴보겠습니다.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

다음 출력이 표시됩니다.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

이제 배포를 다시 시작하여 Envoy 사이드카 프록시 컨테이너를 애플리케이션 Pod에 삽입합니다. 다음 명령을 실행합니다.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

다음 출력이 표시됩니다.

```Output
deployment.apps/bookbuyer restarted
```

네임스페이스에서 Pod를 다시 살펴보면:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

이제 **준비** 열에 **2/2** 컨테이너가 Pod를 준비하는 중인 것으로 표시되는 것을 알 수 있습니다. 두 번째 컨테이너는 Envoy 사이드카 프록시입니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

describe 명령을 실행하여 구성을 확인하여 Envoy 프록시를 확인하기 위해 Pod를 자세히 검토할 수 있습니다.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Envoy 사이드카 프록시 삽입 후에도 애플리케이션이 계속 작동하는지 확인합니다.

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>OSM(Open Service Mesh) 허용 트래픽 정책을 False로 구성하여 배포된 기존 애플리케이션 온보딩

허용 트래픽 정책에 대한 OSM 구성이 `false`로 설정된 경우 OSM은 클러스터 내에서 발생하는 서비스 간 통신을 위해 명시적 [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 배포해야 합니다. 현재 OSM은 서비스 간 통신에도 권한을 부여하는 과정에서 Kubernetes 서비스 계정을 사용합니다. 기존 배포된 애플리케이션이 OSM 메시에서 관리될 때 통신하도록 하기 위해 이제 사용할 서비스 계정이 있는지 확인하고, 서비스 계정 정보로 애플리케이션 배포를 업데이트하고, [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 적용합니다.

### <a name="verify-kubernetes-service-accounts"></a>Kubernetes 서비스 계정 확인

애플리케이션이 배포되는 네임스페이스에 Kubernetes 서비스 계정이 있는지 확인합니다.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

다음은 `bookbuyer` 네임스페이스에 라는 서비스 계정이 `bookbuyer` 있습니다.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

기본 계정 이외의 서비스 계정이 나열되지 않은 경우 애플리케이션에 대한 계정을 만들어야 합니다. 애플리케이션의 배포된 네임스페이스에서 서비스 계정을 만들려면 다음 명령을 예로 사용합니다.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

### <a name="view-your-applications-current-deployment-specification"></a>애플리케이션의 현재 배포 사양 보기

이전 섹션에서 서비스 계정을 만들어야 하는 경우 애플리케이션 배포가 배포 사양의 특정 으로 구성되지 않은 것일 수 `serviceAccountName` 있습니다. 다음 명령을 통해 애플리케이션의 배포 사양을 볼 수 있습니다.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

배포 목록이 출력에 나열됩니다.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

배포를 확인으로 설명하여 Pod 템플릿 섹션에 나열된 서비스 계정이 있는지 확인합니다.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

이 특정 배포에서는 Pod 템플릿 섹션 아래에 나열된 배포와 연결된 서비스 계정이 있음을 확인할 수 있습니다. 이 배포는 서비스 계정 를 사용하고 `bookbuyer` 있습니다. **서비스 계정:** 속성이 표시되지 않으면 배포가 서비스 계정을 사용하도록 구성되지 않습니다.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Kubernetes 서비스 계정을 추가하기 위해 배포를 업데이트하는 몇 가지 방법이 있습니다. 인라인으로 [배포를 업데이트](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment)하는 방법에 대한 Kubernetes 설명서를 검토하거나 [Pod에 대한 서비스 계정을 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)합니다. 서비스 계정으로 배포 사양을 업데이트한 후 배포를 클러스터에 다시 배포합니다(kubectl apply -f your-deployment.yaml).

### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>필요한 SMI(Service Mesh Interface) 정책을 배포합니다.

메시에서 승인된 트래픽을 허용하는 마지막 단계는 애플리케이션에 필요한 [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 배포하는 것입니다. [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 사용하여 수행할 수 있는 구성의 양은 이 연습의 범위를 벗어나지만, 사양의 일반적인 구성 요소 중 일부를 자세히 설명하고 간단한 TrafficTarget 및 HTTPRouteGroup 정책을 구성하여 애플리케이션에 대한 서비스 간 통신을 사용하도록 설정하는 방법을 보여 줍니다.

[SMI](https://smi-spec.io/) [**트래픽 액세스 제어**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) 사양을 통해 사용자는 애플리케이션에 대한 액세스 제어 정책을 정의할 수 있습니다. **TrafficTarget** 및 **HTTPRoutGroup** API 리소스에 집중하겠습니다.

TrafficTarget 리소스는 세 가지 주요 구성 설정 대상, 규칙 및 원본으로 구성됩니다. TrafficTarget 예는 아래에서 확인할 수 있습니다.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

위의 TrafficTarget 사양에서 `destination`은 대상 원본 서비스에 대해 구성된 서비스 계정을 나타냅니다. 이전에 배포에 추가된 서비스 계정은 연결된 배포에 대한 액세스 권한을 부여하는 데 사용됩니다. 이 특정 예에서 `rules` 섹션은 연결을 통해 허용되는 HTTP 트래픽 유형을 정의합니다. HTTP 헤더에 대한 세분화된 정규식 패턴을 HTTP를 통해 허용되는 트래픽에 맞게 구성할 수 있습니다. 이 `sources` 섹션은 서비스에서 시작하는 통신입니다. 이 사양 `bookbuyer` 읽기는 책점과 통신해야 합니다.

HTTPRouteGroup 리소스는 HTTP 헤더 정보와 일치하는 항목 하나 또는 항목의 배열로 구성되며 TrafficTarget 사양에 대한 요구 사항입니다. 아래 예에서는 HTTPRouteGroup이 세 개의 HTTP 작업(두 개의 GET 및 하나의 POST)에 권한을 부여하는 것을 볼 수 있습니다.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

프런트 엔드 애플리케이션이 애플리케이션의 다른 계층에 대해 만드는 HTTP 트래픽 유형에 익숙하지 않은 경우 TrafficTarget 사양에 규칙이 필요하므로 HTTPRouteGroup에 대해 아래 사양을 사용하여 모든 허용 규칙에 해당하는 규칙을 만들 수 있습니다.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

TrafficTarget 및 HTTPRouteGroup 사양을 구성한 후에는 하나의 YAML 및 배포로 함께 배치할 수 있습니다. 다음은 bookstore 예 구성입니다.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

사양에 대한 자세한 내용은 [SMI](https://smi-spec.io/) 사이트를 참조하세요.

## <a name="manage-the-applications-namespace-with-osm"></a>OSM을 사용하여 애플리케이션의 네임스페이스 관리

다음으로 OSM이 네임스페이스를 관리하고 배포를 다시 시작하여 Envoy 사이드카 프록시를 애플리케이션에 삽입하도록 합니다.

다음 명령을 실행하여 `azure-vote` 네임스페이스를 OSM에 의해 관리되도록 구성합니다.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

다음 명령을 사용하여 `azure-vote-front` 및 `azure-vote-back` 배포를 모두 다시 시작합니다.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

네임스페이스에 대한 Pod를 보면 `azure-vote` 및 의 **READY** 단계가 둘 다 `azure-vote-front` `azure-vote-back` 2/2로 표시됩니다. 이는 Envoy 사이드카 프록시가 애플리케이션과 함께 삽입된 것을 의미합니다.
