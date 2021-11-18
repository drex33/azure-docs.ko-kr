---
title: Open Service Mesh를 통해 새 애플리케이션 관리
description: Open Service Mesh를 사용하여 새 애플리케이션을 관리하는 방법
services: container-service
ms.topic: article
ms.date: 11/10/2021
ms.author: pgibson
ms.openlocfilehash: 77e894b8298ed1b4bd81fbb979a906994fb439b3
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132753933"
---
# <a name="manage-a-new-application-with-open-service-mesh-osm-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service OSM(Open Service Mesh)를 통해 새 애플리케이션 관리

이 문서에서는 AKS에서 실행되는 OSM 메시에서 샘플 애플리케이션을 실행하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- AKS OSM 추가 기능과 함께 기존 AKS 클러스터가 설치되어 있습니다. 클러스터를 만들거나 기존 클러스터에서 AKS OSM 추가 기능을 사용하도록 설정해야 하는 경우 Azure CLI [사용하여 OSM(Open Service Mesh) Azure Kubernetes Service(AKS) 추가][osm-cli] 기능 설치를 참조하세요.
- 클러스터에서 실행되는 OSM 메시 버전 v0.11.1 이상
- Azure CLI 버전 2.20.0 이상입니다.
- OSM CLI의 최신 버전입니다.

## <a name="verify-your-mesh-has-permissive-mode-enabled"></a>메시가 허용 모드를 사용하도록 설정되었는지 확인합니다.

`kubectl get meshconfig osm-mesh-config` *enablePermissveTrafficPolicyMode가* *true인지* 확인하려면 를 사용합니다. 다음은 그 예입니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o=jsonpath='{$.spec.traffic.enablePermissiveTrafficPolicyMode}'
```

허용 모드를 사용하도록 설정하지 않은 경우 을 사용하여 사용하도록 설정할 수 `kubectl patch meshconfig osm-mesh-config` 있습니다. 다음은 그 예입니다.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="create-and-onboard-the-namespaces-to-be-managed-by-osm"></a>OSM에서 관리될 네임스페이스 만들기 및 온보딩

OSM 메시에 네임스페이스를 추가하면 OSM 컨트롤러는 해당 네임스페이스에 배포된 애플리케이션과 함께 Envoy 사이드카 프록시 컨테이너를 자동으로 삽입합니다. 를 사용하여 `kubectl create ns` *책점,* *책부키어,* *bookthief* 및 *책웨어하우스* 네임스페이스를 만든 다음 를 사용하여 `osm namespace add` 메시에 해당 네임스페이스를 추가합니다.

```azurecli-interactive
kubectl create ns bookstore
kubectl create ns bookbuyer
kubectl create ns bookthief
kubectl create ns bookwarehouse

osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

다음 출력이 표시됩니다.

```output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created

Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-sample-application-to-the-aks-cluster"></a>AKS 클러스터에 샘플 애플리케이션 배포

를 사용하여 `kubectl apply` 샘플 애플리케이션을 클러스터에 배포합니다.

```azurecli-interactive
SAMPLE_VERSION=v0.11
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookbuyer.yaml
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookthief.yaml
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookstore.yaml
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookwarehouse.yaml
```

다음 출력이 표시됩니다.

```output
serviceaccount/bookbuyer created
deployment.apps/bookbuyer created
serviceaccount/bookthief created
deployment.apps/bookthief created
service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created
serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

샘플 애플리케이션은 서비스 메시 기능을 테스트하는 데 적합한 다중 계층 애플리케이션의 예입니다. 애플리케이션은 4개의 서비스인 *bookbuyer,* *bookthief,* *bookstore* 및 *bookwarehouse* 로 구성됩니다.

![OSM 샘플 애플리케이션 아키텍처](./media/aks-osm-addon/osm-bookstore-app-arch.png)

*bookbuyer* 및 *bookthief* 서비스는 모두 *책점* 서비스와 통신하여 *책점* 서비스에서 책을 검색합니다. *책점* 서비스는 *책웨어하우스* 서비스에서 책을 검색합니다. 이 애플리케이션은 서비스 메시를 사용하여 서비스 간 통신을 보호하고 권한을 부여하는 방법을 보여 줍니다. 예를 들어 이후 섹션에서는 허용 트래픽 모드를 사용하지 않도록 설정하고 SMI 정책을 사용하여 서비스에 대한 액세스를 보호하는 방법을 보여줍니다.

## <a name="access-the-bookbuyer-and-bookthief-services-using-port-forwarding"></a>포트 전달을 사용하여 bookbuyer 및 bookthief 서비스에 액세스

를 사용하여 `kubectl get pod` *bookbuyer* 네임스페이스에서 *bookbuyer* Pod의 이름을 가져옵니다. 다음은 그 예입니다.

```output
$ kubectl get pod -n bookbuyer

NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-1245678901-abcde   2/2     Running   0          7m8s
```

새 터미널을 열고 를 사용하여 `kubectl port forward` 개발 컴퓨터와 *Bookbuyer* Pod 간에 트래픽 전달을 시작합니다. 다음은 그 예입니다.

```output
$ kubectl port-forward bookbuyer-1245678901-abcde -n bookbuyer 8080:14001
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

위의 예제에서는 개발 컴퓨터의 포트 8080과 Pod *bookbuyer-1245678901-abcde의 14001* 간에 트래픽이 전달되고 있음을 보여 줍니다.

웹 `http://localhost:8080` 브라우저에서 로 이동하여 *Bookbuyer* 애플리케이션이 표시되는지 확인합니다. 다음은 그 예입니다.

![OSM Bookbuyer 애플리케이션](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

구매한 책의 수는 계속 증가합니다.  포트 전달 명령을 중지합니다.

를 사용하여 `kubectl get pod` *bookthief* 네임스페이스에서 *bookthief* Pod의 이름을 가져옵니다. 다음은 그 예입니다.

```output
$ kubectl get pod -n bookthief

NAME                         READY   STATUS    RESTARTS   AGE
bookthief-1245678901-abcde   2/2     Running   0          7m8s
```

새 터미널을 열고 를 사용하여 `kubectl port forward` 개발 컴퓨터와 *bookthief* Pod 간에 트래픽 전달을 시작합니다. 다음은 그 예입니다.

```output
$ kubectl port-forward bookthief-1245678901-abcde -n bookthief 8080:14001
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

위의 예제에서는 개발 컴퓨터의 포트 8080과 Pod *bookthief-1245678901-abcde의 14001* 간에 트래픽이 전달되고 있음을 보여 줍니다.

웹 `http://localhost:8080` 브라우저에서 로 이동하여 *bookthief* 애플리케이션이 표시되는지 확인합니다. 다음은 그 예입니다.

![OSM bookthief 애플리케이션](./media/aks-osm-addon/osm-bookthief-service-ui.png)

도난된 도서의 수는 계속 증가하고 있습니다. 포트 전달 명령을 중지합니다.

## <a name="disable-permissive-traffic-mode-on-your-mesh"></a>메시에서 허용 트래픽 모드 사용 안 함

허용 트래픽 모드를 사용하는 경우 온보딩된 네임스페이스의 다른 서비스와 통신하기 위해 서비스에 대한 명시적 [SMI][smi] 정책을 정의할 필요가 없습니다. OSM의 허용 트래픽 모드에 대한 자세한 내용은 [허용 트래픽 정책 모드 를 참조하세요.][osm-permissive-traffic-mode]

허용 모드를 사용하도록 설정된 샘플 애플리케이션에서 *bookbuyer* 및 *bookthief* 서비스는 모두 *책점* 서비스와 통신하고 책을 가져올 수 있습니다. 

`kubectl patch meshconfig osm-mesh-config`를 사용하여 허용 트래픽 모드를 사용하지 않도록 설정합니다.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

다음 예제 출력은 *osm-mesh-config가* 업데이트된 것을 보여줍니다.

```output
$ kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge

meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

이전 섹션의 단계를 반복하여 *Bookbuyer* 서비스와 개발 컴퓨터 간에 트래픽을 전달합니다. 페이지를 새로 고치더라도 카운터가 더 이상 증가하지 않는지 확인합니다. 포트 전달 명령을 중지하고 *bookthief* 서비스와 개발 컴퓨터 간에 트래픽을 전달하는 단계를 반복합니다. 페이지를 새로 고치더라도 카운터가 더 이상 증가하지 않는지 확인합니다. 포트 전달 명령을 중지합니다.

## <a name="apply-an-smi-traffic-access-policy-for-buying-books"></a>책을 구매하기 위한 SMI 트래픽 액세스 정책 적용

`allow-bookbuyer-smi.yaml`다음 YAML을 사용하여 를 만듭니다.

```yaml
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
```

위의 내용은 *책부키어* 서비스가 책 구입을 위해 *책점* 서비스와 통신할 수 있도록 하는 다음과 같은 SMI 액세스 정책을 만듭니다. 또한 책점 서비스가 책 재고를 *재작성하기* 위해 *책웨어하우스* 서비스와 통신할 수 있습니다.

를 사용하여 `kubectl apply` SMI 액세스 정책을 적용합니다.

```azurecli-interactive
kubectl apply -f allow-bookbuyer-smi.yaml
```

다음 예제 출력은 SMI 액세스 정책이 성공적으로 적용된 것을 보여줍니다.

```output
$ kubectl apply -f allow-bookbuyer-smi.yaml

traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

이전 섹션의 단계를 반복하여 *Bookbuyer* 서비스와 개발 컴퓨터 간에 트래픽을 전달합니다. 카운터가 증가 중인지 확인합니다. 포트 전달 명령을 중지하고 *bookthief* 서비스와 개발 컴퓨터 간에 트래픽을 전달하는 단계를 반복합니다. 페이지를 새로 고치더라도 카운터가 증가하지 않는지 확인합니다. 포트 전달 명령을 중지합니다.

## <a name="apply-an-smi-traffic-split-policy-for-buying-books"></a>책을 구매하기 위한 SMI 트래픽 분할 정책 적용

액세스 정책 외에도 SMI를 사용하여 트래픽 분할 정책을 만들 수 있습니다. 트래픽 분할 정책을 사용하면 한 서비스에서 여러 서비스로의 통신 배포를 백 엔드로 구성할 수 있습니다. 이 기능을 사용하면 나머지 트래픽을 현재 버전의 백 엔드 서비스로 보내는 동안 약간의 트래픽을 전송하여 새 버전의 백 엔드 서비스를 테스트할 수 있습니다. 이 기능은 더 많은 트래픽을 새 버전의 서비스로 점진적으로 전환하고 시간이 지남에 따라 이전 버전으로 트래픽을 줄이는 데에도 도움이 될 수 있습니다.

다음 다이어그램에서는 트래픽의 25%를 *bookstore-v1* 서비스로 보내고 트래픽의 75%를 *bookstore-v2* 서비스로 보내는 SMI 트래픽 분할 정책을 보여 있습니다.

![OSM bookbuyer 트래픽 분할 다이어그램](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

`bookbuyer-v2.yaml`다음 YAML을 사용하여 를 만듭니다.

```yaml
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
```

위의 내용은 *Bookbuyer* 서비스가 책 구입을 위해 *bookstore-v2* 서비스와 통신할 수 있도록 *하는 bookstore-v2* 서비스 및 SMI 정책을 만듭니다. 또한 이전 섹션에서 만든 SMI 정책을 사용하여 *booktore-v2* 서비스가 책 재고를 재용량하기 위해 *책 웨어하우스* 서비스와 통신할 수 있도록 합니다.

를 사용하여 `kubectl apply` *bookstore-v2를* 배포하고 SMI 액세스 정책을 적용합니다.

```azurecli-interactive
kubectl apply -f bookbuyer-v2.yaml
```

다음 예제 출력은 SMI 액세스 정책이 성공적으로 적용된 것을 보여줍니다.

```output
$ kubectl apply -f bookbuyer-v2.yaml

service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

`bookbuyer-split-smi.yaml`다음 YAML을 사용하여 를 만듭니다.

```yaml
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
```

위의 내용은 *책점* 서비스에 대한 트래픽을 분할하는 SMI 정책을 만듭니다. 원래 또는 v1 버전의 *책점은* 트래픽의 25%를 수신하고 *bookstore-v2는* 트래픽의 75%를 수신합니다.

를 사용하여 `kubectl apply` SMI 분할 정책을 적용합니다.

```azurecli-interactive
kubectl apply -f bookbuyer-split-smi.yaml
```

다음 예제 출력은 SMI 액세스 정책이 성공적으로 적용된 것을 보여줍니다.

```output
$ kubectl apply -f bookbuyer-split-smi.yaml

trafficsplit.split.smi-spec.io/bookstore-split created
```

이전 섹션의 단계를 반복하여 *Bookbuyer* 서비스와 개발 컴퓨터 간에 트래픽을 전달합니다. *책점 v1* 및 책점 *v2* 모두에 대해 카운터가 증가되고 있는지 확인합니다. 또한 *책점 v2의* 수가 책점 *v1보다* 더 빠르게 증가하는지 확인합니다.

![OSM bookbuyer book bought UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

포트 전달 명령을 중지합니다.


[osm-cli]: open-service-mesh-deploy-addon-az-cli.md
[osm-permissive-traffic-mode]: https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/
[smi]: https://smi-spec.io/