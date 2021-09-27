---
title: Azure Application Gateway 수신 사용
description: Open Service Mesh에서 Azure Application Gateway 수신을 사용하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 765eb53098f757d29072d736d50086f31bb11dc3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649760"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Azure Application Gateway 수신 AKS 추가 기능으로 OSM(Open Service Mesh)에서 관리하는 애플리케이션 배포

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> - 현재 OSM 클러스터 구성 보기
> - 네임스페이스에서 배포된 애플리케이션을 관리할 OSM에 대한 네임스페이스 만들기
> - OSM에서 관리할 네임스페이스를 온보딩합니다.
> - 샘플 애플리케이션 배포
> - AKS 클러스터 내에서 실행 중인 애플리케이션 확인
> - 애플리케이션의 수신 컨트롤러로 사용할 Azure Application Gateway 만들기
> - 인터넷에 Azure Application Gateway 수신을 통해 서비스 공개

## <a name="before-you-begin"></a>시작하기 전에

이 연습에 설명된 단계에서는 AKS 클러스터에 대해 이전에 OSM AKS 추가 기능을 사용하도록 설정했다고 가정합니다. 그렇지 않은 경우 계속하기 전에 [OSM AKS 추가](./open-service-mesh-deploy-addon-az-cli.md) 기능 배포 문서를 검토하세요. 또한 AKS 클러스터는 Kubernetes 버전 `1.19+` 이상이어야 하고, Kubernetes RBAC를 사용하도록 설정하고, 클러스터와의 `kubectl` 연결을 설정해야 합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.8.0 이상
- JSON 프로세서 "jq" 버전 1.6 이상

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="view-and-verify-the-current-osm-cluster-configuration"></a>현재 OSM 클러스터 구성을 확인합니다.

AKS 클러스터에서 AKS용 OSM 추가 기능을 사용하도록 설정하면 osm-mesh-config 리소스에서 현재 구성 매개 변수를 볼 수 있습니다. 다음 명령을 실행하여 속성을 확인합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

출력에는 클러스터의 현재 OSM MeshConfig가 표시됩니다.

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

**enablePermissiveTrafficPolicyMode가** **true** 로 구성되어 있습니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

## <a name="create-namespaces-for-the-application"></a>애플리케이션의 네임스페이스를 만듭니다.

이 자습서에서는 다음과 같은 애플리케이션 구성 요소를 포함하는 OSM으로 구성된 애플리케이션을 사용합니다.

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

OSM 메시에 네임스페이스를 추가하는 경우 OSM 컨트롤러는 애플리케이션을 통해 Envoy 사이드카 프록시 컨테이너를 자동으로 삽입할 수 있습니다. 다음 명령을 실행하여 OSM bookstore 애플리케이션 네임스페이스를 온보딩합니다.

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

## <a name="deploy-the-bookstore-application"></a>Bookstore 애플리케이션 배포

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

## <a name="update-the-bookbuyer-service"></a>서비스 업데이트 `Bookbuyer`

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

## <a name="verify-the-bookstore-application"></a>Bookstore 애플리케이션 확인

지금 bookstore 다중 컨테이너 애플리케이션이 배포되어 있지만 AKS 클러스터 내에서만 액세스할 수 있습니다. 나중에 AKS 클러스터 외부에서 애플리케이션을 공개하는 Azure Application Gateway 수신 컨트롤러를 추가합니다. 애플리케이션이 클러스터 내에서 실행되고 있는지 확인하기 위해 포트를 앞으로 사용하여 `bookbuyer` 구성 요소 UI를 확인합니다.

먼저 `bookbuyer` Pod의 이름을 살펴보겠습니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

다음과 비슷한 결과가 표시됩니다. `bookbuyer`Pod에 고유한 이름이 추가됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod의 이름이 있으면 이제 port-forward 명령을 사용하여 로컬 시스템에서 AKS 클러스터 내부의 애플리케이션까지의 터널을 설정할 수 있습니다. 다음 명령을 실행하여 로컬 시스템 포트 8080에 대한 포트 전달을 설정합니다. 다시 특정 `bookbuyer` Pod 이름을 사용합니다.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

다음과 유사한 결과가 표시됩니다.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

포트 전달 세션이 준비되는 동안 브라우저에서 다음 URL로 이동합니다(`http://localhost:8080`). 이제 `bookbuyer` 아래 이미지와 비슷한 브라우저에서 애플리케이션 UI를 볼 수 있습니다.

![App Gateway UI 이미지에 대한 OSM bookbuyer 앱](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application"></a>애플리케이션을 노출하는 Azure Application Gateway 만들기 `bookbuyer`

> [!NOTE]
> 다음 지침에서는 수신에 사용할 Azure Application Gateway의 새 인스턴스를 만듭니다. 기존 Azure Application Gateway를 사용하려는 경우 Application Gateway 수신 컨트롤러 추가 기능 사용 설정에 대한 섹션으로 건너뜁니다.

### <a name="deploy-a-new-application-gateway"></a>새 Application Gateway 배포

> [!NOTE]
> 기존 AKS 클러스터에 대한 Application Gateway 수신 컨트롤러 추가 기능을 사용하도록 설정하는 기존 설명서를 참조하고 있습니다. OSM 자료에 맞게 몇 가지 사항이 수정되었습니다. 이 주제에 대한 자세한 설명서는 [여기](../application-gateway/tutorial-ingress-controller-add-on-existing.md)에 있습니다.

이제 새 Application Gateway를 배포하고, AKS 클러스터 _myCluster_ 에 대한 트래픽 부하를 분산하는 데 사용하려는 기존 Application Gateway를 시뮬레이션합니다. Application Gateway 이름은 _myApplicationGateway_ 이지만, 먼저 _myPublicIp_ 라는 공용 IP 리소스, 주소 공간이 11.0.0.0/8인 _myVnet_ 이라는 새 가상 네트워크, 주소 공간이 11.1.0.0/16인 _mySubnet_ 이라는 서브넷을 만들고 _myPublicIp_ 를 사용하여 _mySubnet_ 에 Application Gateway를 배포해야 합니다.

별도의 가상 네트워크에서 AKS 클러스터 및 Application Gateway를 사용하는 경우 두 가상 네트워크의 주소 공간이 겹치지 않아야 합니다. AKS 클러스터가 배포하는 기본 주소 공간은 10.0.0.0/8이므로 Application Gateway 가상 네트워크 주소 접두사를 11.0.0.0/8로 설정합니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> AGIC(Application Gateway 수신 컨트롤러) 추가 기능은 Application Gateway v2 SKU(표준 및 WAF)**만** 지원하고 Application Gateway v1 SKU를 지원하지 **않습니다**.

### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Azure CLI를 통해 기존 AKS 클러스터에 대한 AGIC 추가 기능 사용

Azure CLI를 계속 사용하려면 만든 AKS 클러스터 _myCluster_ 에서 AGIC 추가 기능을 계속 사용하도록 설정하고, 앞에서 만든 기존 Application Gateway _myApplicationGateway_ 를 사용하도록 AGIC 추가 기능을 지정합니다.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

다음 명령을 사용하여 Azure Application Gateway AKS 추가 기능이 사용하도록 설정되었는지 확인할 수 있습니다.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

이 명령은 출력을 `true`로 표시합니다.

### <a name="peer-the-two-virtual-networks-together"></a>두 가상 네트워크를 피어링

한 가상 네트워크에 AKS 클러스터를 배포하고 다른 가상 네트워크에 Application Gateway를 배포했으므로, 이제 트래픽이 Application Gateway에서 클러스터의 Pod로 흐르도록 두 가상 네트워크를 피어링해야 합니다. 두 가상 네트워크를 피어링하려면 Azure CLI 명령을 두 번 실행하여 양방향으로 연결해야 합니다. 첫 번째 명령은 Application Gateway 가상 네트워크에서 AKS 가상 네트워크로 피어링 연결을 만들고, 두 번째 명령은 반대 방향으로 피어링 연결을 만듭니다.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>인터넷에 `bookbuyer` 서비스 노출

AKS 클러스터에 다음 수신 매니페스트를 적용하여 `bookbuyer` Azure Application Gateway 통해 인터넷에 서비스를 노출합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

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

다음과 같은 출력이 표시됩니다.

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

수신 매니페스트의 호스트 이름이 테스트에 사용되는 의사(pseudo) 이름이기 때문에 인터넷에서 DNS 이름을 사용할 수 없습니다. 또는 curl 프로그램을 사용하고 호스트 이름 헤더를 Azure Application Gateway 공용 IP 주소에 붙여넣고 서비스에 성공적으로 연결하는 200 코드를 받을 수 `bookbuyer` 있습니다.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

다음과 같은 출력이 표시됩니다.

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
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

## <a name="troubleshooting"></a>문제 해결

- [AGIC 문제 해결 설명서](../application-gateway/ingress-controller-troubleshoot.md)
- [추가적인 문제 해결 도구는 AGIC의 GitHub 리포지토리에서 사용할 수 있습니다.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)
