---
title: NGINX 수신 사용
description: Open Service Mesh에서 NGINX 수신을 사용하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.author: pgibson
ms.openlocfilehash: ee8452552e7e766a42e3c9ed87f4bfab2c9126b1
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132753916"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>NGINX 수신을 통해 OSM(Open Service Mesh)에서 관리하는 애플리케이션 배포

OSM(Open Service Mesh)은 사용자가 매우 동적인 마이크로 서비스 환경에 대한 기본 제공 관찰 기능을 균일하게 관리, 보호 및 얻을 수 있도록 하는 경량의 클라우드 네이티브 서비스 메시입니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> - 현재 OSM 클러스터 구성 보기
> - 네임스페이스에서 배포된 애플리케이션을 관리할 OSM에 대한 네임스페이스 만들기
> - OSM에서 관리할 네임스페이스를 온보딩합니다.
> - 샘플 애플리케이션 배포
> - AKS 클러스터 내에서 실행 중인 애플리케이션 확인
> - 애플리케이션에 사용되는 NGINX 수신 컨트롤러 만들기
> - 인터넷에 Azure Application Gateway 수신을 통해 서비스 공개

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 단계에서는 AKS 클러스터(Kubernetes RBAC가 사용 설정된 Kubernetes `1.19+` 이상)를 만들고 클러스터와의 `kubectl` 연결을 설정한 것으로 가정합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- OSM 버전 v0.11.1 이상
- JSON 프로세서 "jq" 버전 1.6 이상

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>현재 OSM 클러스터 구성을 확인합니다.

AKS 클러스터에서 AKS용 OSM 추가 기능을 사용하도록 설정하면 osm-mesh-config 리소스에서 현재 구성 매개 변수를 볼 수 있습니다. 다음 명령을 실행하여 속성을 확인합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

출력에는 클러스터에 대한 현재 OSM 구성이 표시됩니다.

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

**enablePermissiveTrafficPolicyMode가** **true** 로 구성되어 있습니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다. 허용 트래픽 모드에 대한 자세한 내용은 을 방문하여 [허용 트래픽 정책 모드](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/) 문서를 읽어보십시오.

## <a name="create-namespaces-for-the-application"></a>애플리케이션의 네임스페이스를 만듭니다.

이 자습서에서는 다음 애플리케이션 구성 요소가 있는 OSM `bookstore` 애플리케이션을 사용하겠습니다.

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

OSM 메시에 네임스페이스를 추가하면 OSM 컨트롤러는 애플리케이션을 통해 Envoy 사이드카 프록시 컨테이너를 자동으로 삽입할 수 있습니다. 다음 명령을 실행하여 OSM `bookstore` 애플리케이션 네임스페이스를 온보딩합니다.

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
SAMPLE_VERSION=v0.11
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookbuyer.yaml
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookthief.yaml
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookstore.yaml
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-$SAMPLE_VERSION/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="update-the-bookbuyer-service"></a>Bookbuyer 서비스 업데이트

다음 `bookbuyer` 서비스 매니페스트를 사용하여 서비스를 올바른 인바운드 포트 구성으로 업데이트합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS 클러스터 내에서 실행 중인 bookstore 애플리케이션 확인

현재는 `bookstore` mulit-container 애플리케이션을 배포했지만 AKS 클러스터 내에서만 액세스할 수 있습니다. 나중에 AKS 클러스터 외부에서 애플리케이션을 공개하는 Azure Application Gateway 수신 컨트롤러를 추가합니다. 애플리케이션이 클러스터 내에서 실행되고 있는지 확인하기 위해 포트 전달을 사용하여 `bookbuyer` 구성 요소 UI를 확인합니다.

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

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

다음과 유사한 출력이 표시됩니다.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

포트 전달 세션이 준비되는 동안 브라우저에서 다음 URL로 이동합니다(`http://localhost:8080`). 이제 `bookbuyer` 아래 이미지와 비슷한 브라우저에서 애플리케이션 UI를 볼 수 있습니다.

![NGINX UI 이미지에 대한 OSM bookbuyer 앱](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 NGINX 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

OSM에서 관리하는 애플리케이션을 인터넷에 공개하기 위해 수신 컨트롤러를 활용합니다. 수신 컨트롤러를 만들려면 Helm을 사용하여 nginx-ingress를 설치합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러는 Linux 노드에서 예약됩니다. Windows Server 노드가 수신 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예제에서는 _ingress-basic_ 이라는 수신 리소스에 대한 Kubernetes 네임스페이스를 만듭니다. 필요에 따라 사용자 환경에 대한 네임스페이스를 지정합니다.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

NGINX 수신 컨트롤러에 대한 Kubernetes 부하 분산 장치 서비스가 만들어집니다. 다음 예제 출력과 같이 동적 공용 IP 주소가 할당됩니다.

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

수신 규칙이 만들어지지 않았습니다. 현재 내부 IP 주소로 이동하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>bookbuyer 서비스를 인터넷에 공개

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

다음 출력이 표시됩니다.

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

## <a name="view-the-nginx-logs"></a>NGINX 로그 보기

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

출력은 수신 규칙이 성공적으로 적용되었을 때 NGINX 수신 컨트롤러 상태를 표시합니다.

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

## <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>외부적으로 NGINX 서비스 및 bookbuyer 서비스 보기

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

수신 매니페스트의 호스트 이름이 테스트에 사용되는 의사(pseudo) 이름이기 때문에 인터넷에서 DNS 이름을 사용할 수 없습니다. 또는 curl 프로그램을 사용하여 호스트 이름 헤더를 NGINX 공용 IP 주소로 넘겨 bookbuyer 서비스에 성공적으로 연결하는 200 코드를 수신할 수 있습니다.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

다음 출력이 표시됩니다.

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```
