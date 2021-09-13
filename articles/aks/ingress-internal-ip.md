---
title: 내부 네트워크의 수신 컨트롤러
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service) 클러스터에서 내부 프라이빗 네트워크용 NGINX 수신 컨트롤러를 설치하고 구성하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: b95ae084fa3dd5b2d5308dece2612ab9cbe99a82
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099081"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 내부 가상 네트워크에 대한 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. 수신 컨트롤러는 내부 프라이빗 가상 네트워크 및 IP 주소에 구성됩니다. 외부 액세스가 허용되지 않습니다. 두 애플리케이션이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 애플리케이션에 액세스할 수 있습니다.

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- Let’s Encrypt를 사용하여 [동적 공용 IP 주소][aks-ingress-tls] 또는 [고정 공용 IP 주소][aks-ingress-static-tls]로 TLS 인증서를 자동으로 생성하는 수신 컨트롤러 만들기

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 [Helm 3][helm]을 사용하여 [지원되는 Kubernetes 버전][aks-supported versions]에 NGINX 수신 컨트롤러를 설치합니다. Helm의 최신 릴리스를 사용하고 있으며 *ingress-nginx* Helm 리포지토리에 액세스할 수 있는지 확인합니다. 이 문서에 설명된 단계는 이전 버전의 Helm 차트, NGINX 수신 컨트롤러 또는 Kubernetes와 호환되지 않을 수 있습니다. Helm을 구성하고 사용하는 방법에 대한 자세한 내용은 [Helm을 사용하여 AKS(Azure Kubernetes Service)에 애플리케이션 설치][use-helm]를 참조하세요.

또한 이 문서에서는 Azure CLI 버전 2.0.64 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

또한 이 문서에서는 [통합 ACR][aks-integrated-acr]을 포함하는 기존 AKS 클러스터가 있다고 가정합니다.

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Helm 차트에서 사용하는 이미지를 ACR로 가져오기

AKS 클러스터를 개인 네트워크와 함께 사용하는 경우 클러스터 내에서 사용되는 컨테이너 이미지의 출처를 관리해야 합니다. 자세한 내용은 [AKS(Azure Kubernetes Services)의 컨테이너 이미지 관리 및 보안에 대한 모범 사례][aks-container-best-practices]를 참조하세요.  이 요구 사항을 지원하고 완전성을 제공하기 위해 이 문서의 예제는 [NGINX 수신 컨트롤러 Helm 차트][ingress-nginx-helm-chart]에서 사용되는 세 개의 컨테이너 이미지를 ACR로 가져오는 작업을 사용합니다.

`az acr import`를 사용하여 해당 이미지를 ACR로 가져옵니다.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
CONTROLLER_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v0.48.1
PATCH_REGISTRY=docker.io
PATCH_IMAGE=jettech/kube-webhook-certgen
PATCH_TAG=v1.5.1
DEFAULTBACKEND_REGISTRY=k8s.gcr.io
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5

az acr import --name $REGISTRY_NAME --source $CONTROLLER_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $PATCH_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $DEFAULTBACKEND_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> 컨테이너 이미지를 ACR로 가져오는 것 외에도 Helm 차트를 ACR로 가져올 수도 있습니다. 자세한 내용은 [Azure 컨테이너 레지스트리에 Helm 차트 푸시 및 끌어오기][acr-helm]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

기본적으로는 동적 공용 IP 주소 할당을 통해 NGINX 수신 컨트롤러를 만듭니다. 일반적인 구성 요구 사항은 내부 프라이빗 네트워크 및 IP 주소를 사용하는 것입니다. 이 접근 방식을 사용하면 외부 액세스 없이 서비스 액세스를 내부 사용자로 제한할 수 있습니다.

다음 예제 매니페스트 파일을 사용하여 *internal-ingress.yaml* 파일을 만듭니다. 이 예제에서는 *loadBalancerIP* 리소스에 *10.240.0.42* 를 할당합니다. 수신 컨트롤러와 함께 사용할 고유한 내부 IP 주소를 제공합니다. 가상 네트워크 내에서 이 IP 주소가 이미 사용되고 있지 않은지 확인합니다. 또한 기존 가상 네트워크 및 서브넷을 사용하는 경우 가상 네트워크 및 서브넷을 관리하기 위한 올바른 권한을 사용하여 AKS 클러스터를 구성해야 합니다. 자세한 내용은 [AKS(Azure Kubernetes service)에서 고유한 IP 주소 범위와 함께 kubenet 네트워킹 사용][aks-configure-kubenet-networking] 또는 [AKS(Azure Kubernetes Service)에서 Azure CNI 네트워킹 구성][aks-configure-advanced-networking]을 참조하세요.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

이제 Helm을 사용하여 *nginx-ingress* 차트를 배포합니다. 이전 단계에서 만든 매니페스트 파일을 사용하려면 `-f internal-ingress.yaml` 매개 변수를 추가합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러도 Linux 노드에서 예약해야 합니다. Windows Server 노드가 수신 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예제는 *ingress-basic* 이라는 수신 리소스에 대한 Kubernetes 네임스페이스를 만들고 해당 네임스페이스 내에서 작동하도록 작성되었습니다. 필요에 따라 사용자 환경에 대한 네임스페이스를 지정합니다. AKS 클러스터가 Kubernetes RBAC를 사용하도록 설정되어 있지 않으면 `--set rbac.create=false`를 Helm 명령에 추가합니다.

> [!TIP]
> 클러스터의 컨테이너에 대한 요청에 대해 [클라이언트 원본 IP 유지][client-source-ip]를 사용하도록 설정하려면 `--set controller.service.externalTrafficPolicy=Local`을 Helm 설치 명령에 추가합니다. 클라이언트 원본 IP가 *X-Forwarded-For* 아래의 요청 헤더에 저장됩니다. 클라이언트 원본 IP 유지를 사용하는 수신 컨트롤러를 사용하는 경우 TLS 통과는 작동하지 않습니다.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
    --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG
```

NGINX 수신 컨트롤러에 대한 Kubernetes 부하 분산 장치 서비스가 생성되면 내부 IP 주소가 할당됩니다. 공용 IP 주소를 얻으려면 `kubectl get service` 명령을 사용합니다.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

다음 예제 출력에서 보는 것과 같이 서비스에 IP 주소가 할당될 때까지 몇 분 정도 걸립니다.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

아직 수신 규칙이 만들어지지 않았으므로 내부 IP 주소를 검색하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

## <a name="run-demo-applications"></a>데모 애플리케이션 실행

작동 중인 수신 컨트롤러를 확인하려면 AKS 클러스터에서 두 개의 데모 애플리케이션을 실행합니다. 이 예제에서는 `kubectl apply`를 사용하여 간단한 *Hello World* 애플리케이션의 두 인스턴스를 배포합니다.

*aks-helloworld.yaml* 파일을 만들고 다음 예제 YAML에 복사합니다.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

*ingress-demo.yaml* 파일을 만들고 다음 예제 YAML을 복사합니다.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

`kubectl apply`를 사용하여 두 개의 데모 애플리케이션을 실행합니다.

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>수신 경로 만들기

이제 두 애플리케이션이 모두 Kubernetes 클러스터에서 실행됩니다. 트래픽을 각 애플리케이션으로 라우팅하려면 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 애플리케이션 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서 주소 `http://10.240.0.42/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우트됩니다. 주소 `http://10.240.0.42/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다.

`hello-world-ingress.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: ingress-demo
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
```

`kubectl apply -f hello-world-ingress.yaml` 명령을 사용하여 수신 리소스를 만듭니다.

```console
kubectl apply -f hello-world-ingress.yaml
```

다음 예제 출력은 수신 리소스가 생성된 것을 보여 줍니다.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>수신 컨트롤러 테스트

수신 컨트롤러의 경로를 테스트하려면 웹 클라이언트를 통해 두 개의 애플리케이션으로 이동합니다. 필요한 경우 AKS 클러스터의 Pod에서 내부 전용 기능을 신속하게 테스트할 수 있습니다. 테스트 Pod를 만들고 여기에 터미널 세션을 연결합니다.

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

`apt-get`을 사용하여 Pod에 `curl`을 설치합니다.

```console
apt-get update && apt-get install -y curl
```

이제 *http://10.240.0.42* 와 같은 `curl` 을 사용하여 Kubernetes 수신 컨트롤러의 주소에 액세스합니다. 이 문서의 첫 번째 단계에서 수신 컨트롤러를 배포할 때 지정한 고유한 내부 IP 주소를 제공합니다.

```console
curl -L http://10.240.0.42
```

주소와 함께 추가 경로가 제공되지 않았으므로 수신 컨트롤러는 기본값인 */* 경로로 설정됩니다. 첫 번째 데모 애플리케이션은 다음 축소된 예제 출력에 표시된 대로 반환됩니다.

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

이제 *http://10.240.0.42/hello-world-two* 와 같은 주소에 */hello-world-two* 경로를 추가합니다. 사용자 지정 제목이 있는 두 번째 데모 애플리케이션은 다음 축소된 예제 출력에 표시된 대로 반환됩니다.

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소를 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 해당 리소스를 정리하려면 전체 샘플 네임스페이스 또는 개별 리소스를 삭제하면 됩니다.

### <a name="delete-the-sample-namespace-and-all-resources"></a>샘플 네임스페이스 및 모든 리소스 삭제

전체 샘플 네임스페이스를 삭제하려면 `kubectl delete` 명령을 사용하고 네임스페이스 이름을 지정합니다. 네임스페이스의 모든 리소스가 삭제됩니다.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>리소스를 개별적으로 삭제하거나

또는 생성된 개별 리소스를 삭제하는 것이 보다 세분화된 접근 방식입니다. `helm list` 명령을 사용하여 Helm 릴리스를 나열합니다. 

```console
helm list --namespace ingress-basic
```

다음 예제 출력과 같이 이름이 *nginx-ingress* 및 *aks-helloworld* 인 차트를 찾습니다.

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

`helm uninstall` 명령으로 해당 릴리스를 제거합니다.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

다음 예제에서는 NGINX 수신 배포를 제거합니다.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

다음으로, 두 개의 샘플 애플리케이션을 제거합니다.

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

트래픽을 샘플 앱으로 유도한 수신 경로를 제거합니다.

```console
kubectl delete -f hello-world-ingress.yaml
```

마지막으로 자체 네임스페이스를 삭제할 수 있습니다. `kubectl delete` 명령을 사용하여 네임스페이스 이름을 지정합니다.

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-tls]
- [고정 공용 IP 주소를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-supported versions]: supported-kubernetes-versions.md
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-container-best-practices]: operator-best-practices-container-image-management.md
