---
title: 개방형 서비스 메시를 사용 하 여 새 응용 프로그램 관리
description: Open Service 메시를 사용 하 여 새 응용 프로그램을 관리 하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 1dc58fa0709cdf1e09482a3f3ac3ee05788cd1eb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066865"
---
# <a name="manage-a-new-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>OSM (Open Service 메시) AKS (Azure Kubernetes Service) 추가 기능을 사용 하 여 새 응용 프로그램을 관리 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 연습에서 설명된 단계에서는 AKS 클러스터(Kubernetes RBAC가 사용 설정된 Kubernetes `1.19+` 이상)를 만들고 클러스터와의 `kubectl` 연결을 설정한 것으로 가정합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- OSM 버전 v 0.11.1 이상
- JSON 프로세서 "jq" 버전 1.6 이상

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

OSM 메시에 네임 스페이스를 추가 하는 경우이 작업을 통해 OSM 컨트롤러는 응용 프로그램에 엔보이 사이드카 proxy 컨테이너를 자동으로 삽입할 수 있습니다. 다음 명령을 실행하여 OSM bookstore 애플리케이션 네임스페이스를 온보딩합니다.

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
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookwarehouse.yaml
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

이 응용 프로그램 응용 프로그램은 서비스 메시 기능을 테스트 하는 데 적합 한 다중 계층 응용 프로그램의 예입니다. 응용 프로그램은,, 및의 네 가지 서비스로 구성 됩니다 `bookbuyer` `bookthief` `bookstore` `bookwarehouse` . 및 서비스는 서비스와 `bookbuyer` `bookthief` 통신 `bookstore` 하 여 서비스에서 책을 검색 `bookstore` 합니다. `bookstore`서비스는 `bookwarehouse` 및를 제공 하기 위해 서비스에서 책을 검색 합니다 `bookbuyer` `bookthief` . 이는 서비스 메시를 사용하여 애플리케이션 서비스 간의 통신을 보호하고 권한을 부여하는 방법을 보여 주는 간단한 다중 계층 애플리케이션입니다. 연습을 진행하면서 SMI(Service Mesh Interface) 정책을 사용하거나 사용하지 않도록 설정하여 서비스가 OSM을 통해 통신하도록 허용하거나 허용하지 않을 것입니다. 다음은 bookstore 애플리케이션에 대해 설치된 항목의 아키텍처 다이어그램입니다.

![OSM bookbuyer 앱 아키텍처](./media/aks-osm-addon/osm-bookstore-app-arch.png)

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS 클러스터 내에서 실행 중인 bookstore 애플리케이션 확인

지금 까지는 `bookstore` 다중 컨테이너 응용 프로그램을 배포 했지만 AKS 클러스터 내 에서만 액세스할 수 있습니다. 이후 자습서에서는 수신 컨트롤러를 통해 클러스터 외부에서 애플리케이션을 공개하는 데 도움을 줍니다. 지금은 AKS 클러스터 내부의 응용 프로그램에 액세스 하는 포트 전달을 사용 하 여 `bookbuyer` 서비스에서 설명서를 구매 하 고 있는지 확인 `bookstore` 합니다.

응용 프로그램이 클러스터 내에서 실행 되 고 있는지 확인 하기 위해 `bookbuyer` 및 구성 요소 UI를 모두 표시 하기 위해 포트 전달을 사용 합니다 `bookthief` .

먼저 `bookbuyer` pod의 이름을 가져옵니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

다음과 비슷한 결과가 표시됩니다. Pod에는 `bookbuyer` 고유한 이름이 추가 됩니다.

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

포트 전달 세션이 준비되는 동안 브라우저에서 다음 URL로 이동합니다(`http://localhost:8080`). 이제 `bookbuyer` 브라우저에서 아래 이미지와 비슷한 응용 프로그램 UI를 볼 수 있습니다.

![OSM bookbuyer 앱 UI 이미지](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

또한 총 서적 구매한 숫자가 v1 서비스로 계속 증가 하는 것을 알 수 있습니다 `bookstore` . `bookstore`V2 서비스가 아직 배포 되지 않았습니다. `bookstore`Smi-s 트래픽 분할 정책을 설명할 때 v2 서비스를 배포 합니다.

서비스에 대 한 동일한를 확인할 수도 있습니다 `bookthief` .

```azurecli-interactive
kubectl get pod -n bookthief
```

다음과 비슷한 결과가 표시됩니다. Pod에는 `bookthief` 고유한 이름이 추가 됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Pod를 전달 하는 포트 `bookthief` 입니다.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

브라우저를 열고 다음 URL로 이동합니다(`http://localhost:8080`). `bookthief`현재 서비스에서 설명서를 도용 하 고 있는지 확인 해야 합니다 `bookstore` . 나중에를 중지 하는 트래픽 정책을 구현할 예정 `bookthief` 입니다.

![OSM bookthief 앱 UI 이미지](./media/aks-osm-addon/osm-bookthief-service-ui.png)

## <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>메시에 대한 OSM 허용 트래픽 모드 사용 안 함

이제 허용되는 트래픽 모드 정책을 사용하지 않도록 설정하고 OSM은 각 서비스의 메시에서 통신할 수 있도록 클러스터에 명시적인 [SMI](https://smi-spec.io/) 정책을 배포해야 합니다. 허용 되는 트래픽 모드를 사용 하지 않도록 설정 하려면 다음 명령을 실행 하 여 값을에서로 변경 하는 OSM MeshConfig 리소스 속성을 업데이트 합니다 `true` `false` .

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

다음과 비슷한 결과가 표시됩니다.

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

허용 되는 트래픽 모드가 사용 하지 않도록 설정 되었는지 확인 하려면 포트를 또는 pod로 다시 전달 `bookbuyer` `bookthief` 하 여 브라우저에서 UI를 확인 하 고 구매 북 또는 도난당 한 책이 더 이상 증가 하지 않는지 확인 합니다. 브라우저를 새로 고쳐야 합니다. 증분이 중지되면 정책이 올바르게 적용된 것입니다. 을 (를) `bookthief` 가로채기 설명서에서 성공적으로 중지 했지만에서 `bookbuyer` 구매할 수 없는 `bookstore` 또는에서 설명서를 `bookstore` 검색할 수 없습니다 `bookwarehouse` . 다음으로, 통신하려는 메시의 서비스만 허용하도록 [SMI](https://smi-spec.io/) 정책을 구현합니다. 허용 트래픽 모드에 대 한 자세한 내용은 [허용 트래픽 정책 모드](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/) 문서를 참조 하세요.

## <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>SMI(Service Mesh Interface) 트래픽 액세스 정책 적용

이제 메시의 모든 통신을 사용 하지 않도록 설정 했으므로 서비스에서 서적 구매를 위해 서비스와 통신 하도록 허용 하 고 서비스에서 `bookbuyer` `bookstore` `bookstore` 판매 서적 검색을 위해 서비스와 통신할 수 있도록 `bookwarehouse` 합니다.

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

이제 또는 pod에서 포트 전달 세션을 설정할 수 `bookbuyer` `bookstore` 있으며, 구매한 서적 및 판매 된 책 메트릭이 모두 다시 증가 하는 것을 확인할 수 있습니다. Pod에 대해 동일한 작업 `bookthief` 을 수행 하 여 책을 더 이상 도용할 수 없는지 확인할 수도 있습니다.

## <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>SMI(Service Mesh Interface) 트래픽 액세스 정책 적용

최종 데모를 위해 [SMI](https://smi-spec.io/) 트래픽 분할 정책을 만들어 한 서비스에서 여러 서비스로의 통신 가중치를 백 엔드로 구성합니다. 트래픽 분할 기능을 사용하면 0에서 100 범위의 트래픽에 가중치를 설정하여 한 서비스에 대한 연결을 점진적으로 이동할 수 있습니다.

아래 그림은 배포할 [SMI](https://smi-spec.io/) 트래픽 분할 정책의 다이어그램입니다. 다른 `Bookstore` 응용 프로그램을 버전 2로 배포 하 고 들어오는 트래픽을 `bookbuyer` v1 서비스로의 가중치 25%, `bookstore` 75%에서 `bookstore` v2 서비스로 분할 합니다.

![OSM bookbuyer 트래픽 분할 다이어그램](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

`bookstore`V2 서비스를 배포 합니다.

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

이제 트래픽 분할 정책을 배포 하 여 `bookbuyer` 두 `bookstore` v1 및 v2 서비스 간의 트래픽을 분할 합니다.

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

포트 전달 터널을 pod로 설정 `bookbuyer` 하면 v2 서비스에서 구매한 책이 표시 됩니다 `bookstore` . 계속 해 서 구매의 증가를 확인 하는 경우 v2 서비스를 통해 더 빠르게 구매한 구매의 증가를 확인 해야 합니다 `bookstore` .

![OSM bookbuyer 구매 UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)
