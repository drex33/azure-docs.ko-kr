---
title: 고정 IP에서 수신 컨트롤러 사용
titleSuffix: Azure Kubernetes Service
description: 자동 TLS 인증서 생성을 위해 Let's Encrypt를 사용하는 고정 공용 IP 주소가 있는 NGINX 수신 컨트롤러를 AKS(Azure Kubernetes Service) 클러스터에 설치하고 구성하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 6637a3d1319793f37cf573824bace83a3bf362c7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547491"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 고정 공용 IP 주소를 사용하여 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. 고정 공용 IP 주소를 사용하여 수신 컨트롤러를 구성합니다. [cert-manager][cert-manager] 프로젝트는 [Let's Encrypt][lets-encrypt] 인증서를 자동으로 생성하고 구성하는 데 사용됩니다. 마지막으로, 두 애플리케이션이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 애플리케이션에 액세스할 수 있습니다.

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [동적 공용 IP를 사용하여 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt를 사용하는 수신 컨트롤러 만들기][aks-ingress-tls]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

이 문서에서는 [Helm 3][helm]을 사용하여 [지원되는 Kubernetes 버전][aks-supported versions]에 NGINX 수신 컨트롤러를 설치합니다. 최신 릴리스의 Helm을 사용하고 있고 *ingress-nginx* 와 *jetstack* Helm 리포지토리에 대한 액세스 권한이 있는지 확인합니다. 이 문서에 설명된 단계는 이전 버전의 Helm 차트, NGINX 수신 컨트롤러 또는 Kubernetes와 호환되지 않을 수 있습니다.

Helm을 구성하고 사용하는 방법에 대한 자세한 내용은 [Helm을 사용하여 AKS(Azure Kubernetes Service)에 애플리케이션 설치][use-helm]를 참조하세요. 업그레이드 지침은 [Helm 설치 문서][helm-install]를 참조하세요.

또한 이 문서에서는 Azure CLI 버전 2.0.64 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

또한 이 문서에서는 통합 ACR을 포함하는 기존 AKS 클러스터가 있다고 가정합니다. 통합 ACR을 포함하는 AKS 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증][aks-integrated-acr]을 참조하세요.

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Helm 차트에서 사용하는 이미지를 ACR로 가져오기

이 문서에서는 세 개의 컨테이너 이미지를 사용하는 [NGINX 수신 컨트롤러 Helm 차트][ingress-nginx-helm-chart]를 사용합니다. `az acr import`를 사용하여 해당 이미지를 ACR로 가져옵니다.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
SOURCE_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v1.0.4
PATCH_IMAGE=ingress-nginx/kube-webhook-certgen
PATCH_TAG=v1.1.1
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5
CERT_MANAGER_REGISTRY=quay.io
CERT_MANAGER_TAG=v1.5.4
CERT_MANAGER_IMAGE_CONTROLLER=jetstack/cert-manager-controller
CERT_MANAGER_IMAGE_WEBHOOK=jetstack/cert-manager-webhook
CERT_MANAGER_IMAGE_CAINJECTOR=jetstack/cert-manager-cainjector

az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG
```

> [!NOTE]
> 컨테이너 이미지를 ACR로 가져오는 것 외에도 Helm 차트를 ACR로 가져올 수도 있습니다. 자세한 내용은 [Azure 컨테이너 레지스트리에 Helm 차트 푸시 및 끌어오기][acr-helm]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

기본적으로는 새 공용 IP 주소 할당을 통해 NGINX 수신 컨트롤러를 만듭니다. 이 공용 IP 주소는 수신 컨트롤러의 수명 동안만 고정되며 컨트롤러를 삭제했다가 다시 만들면 손실됩니다. 일반적인 구성 요구 사항은 기존 고정 공용 IP 주소를 NGINX 수신 컨트롤러에 제공하는 것입니다. 수신 컨트롤러를 삭제해도 고정 공용 IP 주소는 유지됩니다. 이 방법을 사용하면 애플리케이션 수명 주기 전반에 걸쳐 일관된 방식으로 기존 DNS 레코드 및 네트워크 구성을 사용할 수 있습니다.

고정 공용 IP 주소를 만들어야 하는 경우 먼저 [az aks show][az-aks-show] 명령을 사용하여 AKS 클러스터의 리소스 그룹 이름을 가져옵니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

다음으로, [az network public-ip create][az-network-public-ip-create] 명령을 사용하여 *고정* 할당 메서드로 공용 IP 주소를 만듭니다. 다음 예제에서는 이전 단계에서 가져온 AKS 클러스터 리소스 그룹에 *myAKSPublicIP* 라는 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> 위의 명령은 AKS 클러스터를 삭제하는 경우 삭제되는 IP 주소를 만듭니다. 또는 AKS 클러스터와 별도로 관리할 수 있는 다른 리소스 그룹에 IP 주소를 만들 수 있습니다. 다른 리소스 그룹에 IP 주소를 만드는 경우 AKS 클러스터에서 사용하는 클러스터 ID에 *네트워크 기여자* 와 같은 다른 리소스 그룹에 대한 위임된 권한이 있는지 확인합니다. 자세한 내용은 [AKS 부하 분산 장치에 고정 공용 IP 주소 및 DNS 레이블 사용][aks-static-ip]을 참조하세요.

이제 Helm을 사용하여 *nginx-ingress* 차트를 배포합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

### <a name="ip-and-dns-label"></a>IP 및 DNS 레이블
수신 컨트롤러는 수신 컨트롤러 서비스에 할당할 부하 분산 장치의 고정 IP 주소와 공용 IP 주소 리소스에 적용되는 DNS 이름 레이블을 모두 인식하도록 Helm 릴리스에 두 개의 추가 매개 변수를 전달해야 합니다. HTTPS 인증서가 올바르게 작동하려면 DNS 이름 레이블을 사용하여 수신 컨트롤러 IP 주소에 대한 FQDN을 구성합니다.

1. `--set controller.service.loadBalancerIP` 매개 변수를 추가합니다. 이전 단계에서 만든 사용자 고유의 공용 IP 주소를 입력합니다.
1. `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` 매개 변수를 추가합니다. 이전 단계에서 만든 공용 IP 주소에 적용할 DNS 이름 레이블을 지정합니다.  이 레이블은 양식 `<LABEL>.<AZURE REGION NAME>.cloudapp.azure.com`의 DNS 이름을 만듭니다.

수신 컨트롤러도 Linux 노드에서 예약해야 합니다. Windows Server 노드가 수신 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예제는 *ingress-basic* 이라는 수신 리소스에 대한 Kubernetes 네임스페이스를 만들고 해당 네임스페이스 내에서 작동하도록 작성되었습니다. 필요에 따라 사용자 환경에 대한 네임스페이스를 지정합니다. AKS 클러스터가 Kubernetes RBAC를 사용하도록 설정되어 있지 않으면 `--set rbac.create=false`를 Helm 명령에 추가합니다.

> [!TIP]
> 클러스터의 컨테이너에 대한 요청에 대해 [클라이언트 원본 IP 유지][client-source-ip]를 사용하도록 설정하려면 `--set controller.service.externalTrafficPolicy=Local`을 Helm 설치 명령에 추가합니다. 클라이언트 원본 IP가 *X-Forwarded-For* 아래의 요청 헤더에 저장됩니다. 클라이언트 원본 IP 유지가 활성화된 수신 컨트롤러를 사용하는 경우 TLS 통과는 작동하지 않습니다.

수신 컨트롤러의 **IP 주소** 와 FQDN 접두사에 사용할 **고유 이름** 으로 다음 스크립트를 업데이트합니다.

> [!IMPORTANT]
> 명령을 실행할 때 `<STATIC_IP>` 및 `<DNS_LABEL>`을 사용자 고유의 IP 주소와 고유한 이름으로 업데이트해야 합니다.  DNS_LABEL은 Azure 지역 내에서 고유해야 합니다.

```console
# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>
STATIC_IP=<STATIC_IP>
DNS_LABEL=<DNS_LABEL>

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic --create-namespace \
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
    --set controller.admissionWebhooks.patch.image.digest="" \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG \
    --set defaultBackend.image.digest="" \
    --set controller.service.loadBalancerIP=$STATIC_IP \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"=$DNS_LABEL
```

NGINX 수신 컨트롤러에 대해 Kubernetes 부하 분산 장치 서비스를 만든 경우 다음 예제 출력에 표시된 대로 고정 IP 주소를 할당합니다.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

아직 수신 규칙이 만들어지지 않았으므로 공용 IP 주소를 검색하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

다음과 같이 공용 IP 주소에서 FQDN을 쿼리하여 DNS 이름 레이블이 적용되었는지 확인할 수 있습니다.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

이제 IP 주소 또는 FQDN을 통해 수신 컨트롤러에 액세스할 수 있습니다.

## <a name="install-cert-manager"></a>cert-manager 설치

NGINX 수신 컨트롤러는 TLS 종료를 지원합니다. HTTPS에 대한 인증서를 검색하고 구성하는 몇 가지 방법이 있습니다. 이 문서에서는 자동 [Lets Encrypt][lets-encrypt] 인증서 생성 및 관리 기능을 제공하는 [cert-manager][cert-manager]를 사용하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 Let's Encrypt에 대해 `staging` 환경을 사용합니다. 프로덕션 배포에서는 Helm 차트를 설치할 때 및 리소스 정의에서 `letsencrypt-prod` 및 `https://acme-v02.api.letsencrypt.org/directory`를 사용합니다.

Kubernetes RBAC 지원 클러스터에 cert-manager 컨트롤러를 설치하려면 다음 `helm install` 명령을 사용합니다.

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --version $CERT_MANAGER_TAG \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CONTROLLER \
  --set image.tag=$CERT_MANAGER_TAG \
  --set webhook.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_WEBHOOK \
  --set webhook.image.tag=$CERT_MANAGER_TAG \
  --set cainjector.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CAINJECTOR \
  --set cainjector.image.tag=$CERT_MANAGER_TAG 
```

cert-manager 구성에 대한 자세한 내용은 [cert-manager 프로젝트][cert-manager]를 참조합니다.

## <a name="create-a-ca-cluster-issuer"></a>CA 클러스터 발급자 만들기

인증서가 발급되기 전에 cert-manager에게는 [발급자][cert-manager-issuer] 또는 [ClusterIssuer][cert-manager-cluster-issuer] 리소스가 필요합니다. 이러한 Kubernetes 리소스는 기능이 동일하지만, `Issuer`는 단일 네임스페이스에서 작동하고 `ClusterIssuer`는 모든 네임스페이스에서 작동합니다. 자세한 내용은 [cert-manager 발급자][cert-manager-issuer] 설명서를 참조하세요.

다음 예제 매니페스트를 사용하여 클러스터 발급자(예: `cluster-issuer.yaml`)를 만듭니다. 조직의 유효한 주소로 메일 주소를 업데이트합니다.

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

발급자를 만들려면 `kubectl apply` 명령을 사용합니다.

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

출력은 다음 예제와 유사해야 합니다.

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>데모 애플리케이션 실행

수신 컨트롤러와 인증서 관리 솔루션이 구성되었습니다. 이제 AKS 클러스터에서 두 개의 데모 애플리케이션을 실행하겠습니다. 이 예제에서는 Helm을 사용하여 간단한 ‘Hello world’ 애플리케이션의 두 인스턴스를 배포합니다.

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

이제 두 애플리케이션이 모두 Kubernetes 클러스터에서 실행되고 있지만, `ClusterIP` 유형의 서비스로 구성되었습니다. 따라서 인터넷에서 애플리케이션에 액세스할 수 없습니다. 응용 프로그램을 공개적으로 사용할 수 있도록 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 애플리케이션 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서 주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우트됩니다. 주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다. *hosts* 및 *host* 를 이전 단계에서 만든 DNS 이름으로 업데이트합니다.

`hello-world-ingress.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
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

`kubectl apply` 명령을 사용하여 수신 리소스를 만듭니다.

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

출력은 다음 예제와 유사해야 합니다.

```
ingress.extensions/hello-world-ingress created
```

## <a name="verify-certificate-object"></a>인증서 개체 확인

다음으로, 인증서 리소스를 만들어야 합니다. 인증서 리소스는 원하는 X.509 인증서를 정의합니다. 자세한 내용은 [cert-manager 인증서][cert-manager-certificates]를 참조하세요.

인증서 관리자는 v0.2.2 이후 인증서 관리자와 자동으로 배포되는 수신 shim을 사용하여 해당 관리자에 대한 인증서 개체가 자동으로 생성되었을 가능성이 있습니다. 자세한 내용은 [수신 shim 설명서][ingress-shim]를 참조하세요.

인증서가 성공적으로 만들어졌는지 확인하려면 `kubectl describe certificate tls-secret --namespace ingress-basic` 명령을 사용합니다.

인증서가 발급되면 다음과 비슷한 출력이 표시됩니다.
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

## <a name="test-the-ingress-configuration"></a>수신 구성 테스트

웹 브라우저에서 Kubernetes 수신 컨트롤러의 FQDN(예: *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* )을 엽니다.

이 예제에서는 `letsencrypt-staging`을 사용하므로 브라우저에서 발급된 TLS/SSL 인증서를 신뢰하지 않습니다. 경고 프롬프트를 수락하여 애플리케이션에서 계속 진행합니다. 인증서 정보에 이 *Fake LE Intermediate X1* 인증서가 Let's Encrypt에서 발급되었다고 표시됩니다. 이 가짜 인증서는 `cert-manager`에서 요청을 올바르게 처리했으며 공급자로부터 인증서를 받았음을 나타냅니다.

![Let's Encrypt 스테이징 인증서](media/ingress/staging-certificate.png)

`staging` 대신 `prod`를 사용하도록 Let's Encrypt를 변경하면, 다음 예제와 같이 Let's Encrypt에서 발급된 신뢰할 수 있는 인증서가 사용됩니다.

![Let's Encrypt 인증서](media/ingress/certificate.png)

데모 애플리케이션이 웹 브라우저에 표시됩니다.

![애플리케이션 예제 1](media/ingress/app-one.png)

이제 FQDN에 */hello-world-two* 경로를 추가합니다(예: *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`*). 사용자 지정 제목이 있는 두 번째 데모 애플리케이션이 표시됩니다.

![애플리케이션 예제 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소, 인증서 및 샘플 앱을 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 해당 리소스를 정리하려면 전체 샘플 네임스페이스 또는 개별 리소스를 삭제하면 됩니다.

### <a name="delete-the-sample-namespace-and-all-resources"></a>샘플 네임스페이스 및 모든 리소스 삭제

전체 샘플 네임스페이스를 삭제하려면 `kubectl delete` 명령을 사용하고 네임스페이스 이름을 지정합니다. 네임스페이스의 모든 리소스가 삭제됩니다.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>리소스를 개별적으로 삭제하거나

또는 생성된 개별 리소스를 삭제하는 것이 보다 세분화된 접근 방식입니다. 먼저 인증서 리소스를 제거합니다.

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

이제 `helm list` 명령을 사용하여 Helm 릴리스를 나열합니다. 다음 예제 출력과 같이 이름이 *nginx-ingress* 와 *cert-manager* 인 차트를 찾습니다.

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

`helm uninstall` 명령으로 해당 릴리스를 제거합니다. 다음 예제에서는 NGINX 수신 배포 및 인증서 관리자 배포를 제거합니다.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

다음으로, 두 개의 샘플 애플리케이션을 제거합니다.

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

자체 네임스페이스를 삭제합니다. `kubectl delete` 명령을 사용하여 네임스페이스 이름을 지정합니다.

```console
kubectl delete namespace ingress-basic
```

마지막으로 수신 컨트롤러에 대해 생성된 고정 공용 IP 주소를 제거합니다. 이 문서의 첫 번째 단계에서 얻은 *MC_* 클러스터 리소스 그룹 이름(예: *MC_myResourceGroup_myAKSCluster_eastus*)을 제공합니다.

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]
- [인증서 관리자][cert-manager]

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.io/docs/concepts/certificate/
[cert-manager-cluster-issuer]: https://cert-manager.io/docs/concepts/issuer/
[cert-manager-issuer]: https://cert-manager.io/docs/concepts/issuer/
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://cert-manager.io/docs/usage/ingress/
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
[aks-supported versions]: supported-kubernetes-versions.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
