---
title: Open Service Mesh를 통해 새 애플리케이션 관리
description: Open Service Mesh를 사용하여 새 애플리케이션을 관리하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 77728c7b61fc6b76cb5ecb51ff1d90e0c0a93e99
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535546"
---
# <a name="manage-a-new-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>AKS(Open Service Mesh) Azure Kubernetes Service(OSM) 추가 기능으로 새 애플리케이션 관리

## <a name="before-you-begin"></a>시작하기 전에

이 연습에서 설명된 단계에서는 AKS 클러스터(Kubernetes RBAC가 사용 설정된 Kubernetes `1.19+` 이상)를 만들고 클러스터와의 `kubectl` 연결을 설정한 것으로 가정합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.8.0 이상
- JSON 프로세서 "jq" 버전 1.6 이상

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="create-namespaces-for-the-application"></a>애플리케이션의 네임스페이스를 만듭니다.

이 연습에서는 다음 Kubernetes 서비스를 포함하는 OSM 애플리케이션을 사용합니다.

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

이러한 각 애플리케이션 구성 요소에 대한 네임스페이스를 만듭니다.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

다음 출력이 표시됩니다.

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM에서 관리할 네임스페이스를 온보딩합니다.

OSM 메시에 네임스페이스를 추가하면 OSM 컨트롤러가 Envoy 사이드카 프록시 컨테이너를 애플리케이션에 자동으로 삽입할 수 있습니다. 다음 명령을 실행하여 OSM bookstore 애플리케이션 네임스페이스를 온보딩합니다.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

다음 출력이 표시됩니다.

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Bookstore 애플리케이션을 AKS 클러스터에 배포합니다.

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookwarehouse.yaml
```

모든 배포 출력은 아래에 요약되어 있습니다.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

## <a name="checkpoint-what-got-installed"></a>검사점: 설치된 항목

Bookstore 애플리케이션은 서비스 메시 기능을 테스트하는 데 적합한 다중 계층 애플리케이션의 예입니다. 애플리케이션은 , , 및 인 4개의 서비스로 `bookbuyer` `bookthief` `bookstore` `bookwarehouse` 구성됩니다. 및 서비스는 서비스와 `bookbuyer` `bookthief` `bookstore` 통신하여 서비스에서 책을 `bookstore` 검색합니다. `bookstore`서비스는 서비스에서 책을 `bookwarehouse` 검색하여 및 을 `bookbuyer` `bookthief` 제공합니다. 이는 서비스 메시를 사용하여 애플리케이션 서비스 간의 통신을 보호하고 권한을 부여하는 방법을 보여 주는 간단한 다중 계층 애플리케이션입니다. 연습을 진행하면서 SMI(Service Mesh Interface) 정책을 사용하거나 사용하지 않도록 설정하여 서비스가 OSM을 통해 통신하도록 허용하거나 허용하지 않을 것입니다. 다음은 bookstore 애플리케이션에 대해 설치된 항목의 아키텍처 다이어그램입니다.

![OSM bookbuyer 앱 아키텍처](./media/aks-osm-addon/osm-bookstore-app-arch.png)

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS 클러스터 내에서 실행 중인 bookstore 애플리케이션 확인

현재는 `bookstore` mulit-container 애플리케이션을 배포했지만 AKS 클러스터 내에서만 액세스할 수 있습니다. 이후 자습서에서는 수신 컨트롤러를 통해 클러스터 외부에서 애플리케이션을 공개하는 데 도움을 줍니다. 지금은 포트 전달을 활용하여 `bookbuyer` AKS 클러스터 내의 애플리케이션에 액세스하여 서비스에서 책을 구매하고 있는지 확인합니다. `bookstore`

애플리케이션이 클러스터 내에서 실행되고 있는지 확인하기 위해 포트를 앞으로 사용하여 `bookbuyer` 및 구성 요소 UI를 모두 `bookthief` 확인합니다.

먼저 `bookbuyer` Pod의 이름을 살펴보겠습니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

다음과 비슷한 결과가 표시됩니다. `bookbuyer`Pod에 고유한 이름이 추가됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod의 이름이 있으면 이제 port-forward 명령을 사용하여 로컬 시스템에서 AKS 클러스터 내부의 애플리케이션까지의 터널을 설정할 수 있습니다. 다음 명령을 실행하여 로컬 시스템 포트 8080에 대한 포트 전달을 설정합니다. 지정된 bookbuyer Pod 이름을 다시 사용합니다.

> [!NOTE]
> 모든 포트 전달 명령에 대해 이 연습을 계속 진행하고 터널의 연결을 끊지 않도록 추가 터미널을 사용하는 것이 가장 좋습니다. 또한 Azure Cloud Shell 외부에서 포트 전달 터널을 설정하는 것도 좋은 방법입니다.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

다음과 비슷한 내용이 출력됩니다.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

포트 전달 세션이 준비되는 동안 브라우저에서 다음 URL로 이동합니다(`http://localhost:8080`). 이제 `bookbuyer` 아래 이미지와 비슷한 브라우저에서 애플리케이션 UI를 볼 수 있습니다.

![OSM bookbuyer 앱 UI 이미지](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

또한 구매한 총 도서 수가 v1 서비스로 계속 증가하는 것을 확인할 수 `bookstore` 있습니다. `bookstore`v2 서비스가 아직 배포되지 않았습니다. `bookstore`SMI 트래픽 분할 정책을 시연할 때 v2 서비스를 배포합니다.

서비스에 대해 동일한 것을 확인할 수도 `bookthief` 있습니다.

```azurecli-interactive
kubectl get pod -n bookthief
```

다음과 비슷한 결과가 표시됩니다. `bookthief`Pod에 고유한 이름이 추가됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

`bookthief`Pod에 전달합니다.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

브라우저를 열고 다음 URL로 이동합니다(`http://localhost:8080`). 가 현재 `bookthief` 서비스에서 책을 도용하고 있는 것을 볼 수 `bookstore` 있습니다. 나중에 트래픽 정책을 구현하여 를 `bookthief` 중지합니다.

![OSM bookthief 앱 UI 이미지](./media/aks-osm-addon/osm-bookthief-service-ui.png)

## <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>메시에 대한 OSM 허용 트래픽 모드 사용 안 함

이제 허용되는 트래픽 모드 정책을 사용하지 않도록 설정하고 OSM은 각 서비스의 메시에서 통신할 수 있도록 클러스터에 명시적인 [SMI](https://smi-spec.io/) 정책을 배포해야 합니다. 허용 트래픽 모드를 사용하지 않도록 설정하려면 다음 명령을 실행하여 값을 에서 로 변경하는 OSM MeshConfig 리소스 속성을 `true` `false` 업데이트합니다.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

다음과 비슷한 결과가 표시됩니다.

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

허용 트래픽 모드가 사용하지 않도록 설정되었는지 확인하려면 또는 `bookbuyer` `bookthief` Pod로 다시 이식하여 브라우저에서 해당 UI를 보고 도서에서 구입하거나 도난당한 책을 더 이상 증가하지 않는지 확인합니다. 브라우저를 새로 고쳐야 합니다. 증분이 중지되면 정책이 올바르게 적용된 것입니다. 가 도서를 도용하는 것을 성공적으로 중지했지만 에서 를 구매할 수 없으며 도 `bookthief` 에서 `bookbuyer` 책을 `bookstore` `bookstore` 검색할 수 `bookwarehouse` 없습니다. 다음으로, 통신하려는 메시의 서비스만 허용하도록 [SMI](https://smi-spec.io/) 정책을 구현합니다. 허용 트래픽 모드에 대한 자세한 내용은 을 방문하여 [허용 트래픽 정책 모드](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/) 문서를 읽어보십시오.

## <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>SMI(Service Mesh Interface) 트래픽 액세스 정책 적용

이제 메시의 모든 통신을 사용하지 않도록 설정했으므로 `bookbuyer` 서비스에서 책을 구매하기 위해 서비스와 통신하고 서비스에서 `bookstore` `bookstore` `bookwarehouse` 판매할 책을 검색하도록 서비스와 통신할 수 있도록 허용해 보겠습니다.

다음 [SMI](https://smi-spec.io/) 정책을 배포합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
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
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

다음과 비슷한 결과가 표시됩니다.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

이제 또는 Pod에 포트 전달 세션을 설정하고 `bookbuyer` `bookstore` 구매한 책과 판매한 도서 메트릭이 모두 다시 증가되는지 확인할 수 있습니다. Pod에 대해 동일한 작업을 `bookthief` 수행하여 더 이상 책을 도용할 수 없는지 확인할 수도 있습니다.

## <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>SMI(Service Mesh Interface) 트래픽 액세스 정책 적용

최종 데모를 위해 [SMI](https://smi-spec.io/) 트래픽 분할 정책을 만들어 한 서비스에서 여러 서비스로의 통신 가중치를 백 엔드로 구성합니다. 트래픽 분할 기능을 사용하면 0에서 100 범위의 트래픽에 가중치를 설정하여 한 서비스에 대한 연결을 점진적으로 이동할 수 있습니다.

아래 그림은 배포할 [SMI](https://smi-spec.io/) 트래픽 분할 정책의 다이어그램입니다. 다른 `Bookstore` 애플리케이션을 버전 2로 배포한 다음 에서 들어오는 `bookbuyer` 트래픽을 분할하여 트래픽의 25%를 `bookstore` v1 서비스에 가중치를 적용하고 75%를 v2 서비스에 가중치를 `bookstore` 적용합니다.

![OSM bookbuyer 트래픽 분할 다이어그램](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

`bookstore`v2 서비스를 배포합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
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
EOF
```

다음 출력이 표시되어야 합니다.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

이제 트래픽 분할 정책을 배포하여 `bookbuyer` 두 `bookstore` v1과 v2 서비스 간에 트래픽을 분할합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

다음 출력이 표시되어야 합니다.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Pod에 대한 포트 전달 터널을 `bookbuyer` 설정하면 이제 v2 서비스에서 구매되는 책을 볼 수 `bookstore` 있습니다. 구매 증가량을 계속 감시하는 경우 v2 서비스를 통해 구매가 더 빠르게 증가한다는 것을 알 수 `bookstore` 있습니다.

![OSM bookbuyer book bought UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)