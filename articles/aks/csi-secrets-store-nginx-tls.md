---
title: Azure Kubernetes Service에서 TLS를 사용 하 여 NGINX 수신 컨트롤러를 사용 하도록 비밀 저장소 CSI 드라이버 설정
description: AKS (Azure Kubernetes Service)에 대 한 TLS를 사용 하 여 NGINX 수신 컨트롤러를 사용 하도록 비밀 저장소 CSI 드라이버를 구성 하는 방법입니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: template-how-to
ms.openlocfilehash: 28f01a1dccde2049b8dfa20a5d9ebd5b07ef916b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511683"
---
# <a name="set-up-secrets-store-csi-driver-to-enable-nginx-ingress-controller-with-tls"></a>TLS를 사용 하 여 NGINX 수신 컨트롤러를 사용 하도록 비밀 저장소 CSI 드라이버 설정

이 문서에서는 AKS (Azure Kubernetes Service) 클러스터 및 Azure Key Vault (AKV) 인스턴스를 사용 하 여 TLS로 NGINX 수신 컨트롤러를 보호 하는 과정을 안내 합니다. 자세한 내용은 [Kubernetes의 TLS][kubernetes-ingress-tls]를 참조 하세요.

다음 두 가지 방법 중 하나를 사용 하 여 수신 TLS 인증서를 클러스터로 가져올 수 있습니다.

- **응용 프로그램** -응용 프로그램 배포 매니페스트는 공급자 볼륨을 선언 하 고 탑재 합니다. 응용 프로그램이 배포 되는 경우에만 클러스터에서 사용할 수 있는 인증서가 있고, 응용 프로그램이 제거 되 면 비밀도 제거 됩니다. 이 시나리오는 응용 프로그램의 보안 인프라 및 클러스터와의 통합을 담당 하는 개발 팀에 적합 합니다.
- **수신 컨트롤러** -수신 배포가 수정 되어 공급자 볼륨을 선언 하 고 탑재 합니다. 수신 pod 생성 될 때 암호를 가져옵니다. 응용 프로그램의 pod에는 TLS 인증서에 대 한 액세스 권한이 없습니다. 이 시나리오는 한 팀이 인프라 및 네트워킹 구성 요소 (HTTPS TLS 인증서 포함)를 관리 및 프로 비전 하 고 다른 팀에서 응용 프로그램 수명 주기를 관리 하는 시나리오에 적합 합니다. 이 경우 수신은 단일 네임 스페이스/작업에 고유 하며 응용 프로그램과 동일한 네임 스페이스에 배포 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 시작 하기 전에 Azure CLI 버전이 >= 인지 확인 `2.30.0` 하거나 [최신 버전을 설치](/cli/azure/install-azure-cli)합니다.
- 비밀 저장소 CSI 드라이버가 구성 된 AKS 클러스터
- Azure Key Vault 인스턴스입니다.


## <a name="generate-a-tls-certificate"></a>TLS 인증서 생성

```bash
export CERT_NAME=ingresscert
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out ingress-tls.crt \
    -keyout ingress-tls.key \
    -subj "/CN=demo.test.com/O=ingress-tls"
```

### <a name="import-the-certificate-to-akv"></a>AKV로 인증서 가져오기

```bash
export AKV_NAME="[YOUR AKV NAME]"
openssl pkcs12 -export -in ingress-tls.crt -inkey ingress-tls.key  -out $CERT_NAME.pfx
# skip Password prompt
```

```azurecli-interactive
az keyvault certificate import --vault-name $AKV_NAME -n $CERT_NAME -f $CERT_NAME.pfx
```

## <a name="deploy-a-secretproviderclass"></a>SecretProviderClass 배포

먼저 새 네임 스페이스를 만듭니다.

```bash
export NAMESPACE=ingress-test
```

```azurecli-interactive
kubectl create ns $NAMESPACE
```

[액세스 id를 제공 하는 방법을][csi-ss-identity-access] 선택 하 고 SecretProviderClass yaml를 적절 하 게 구성 합니다. 추가 필수 구성 요소:
- `objectType=secret`AKV에서 개인 키와 인증서를 가져오는 유일한 방법은를 사용 해야 합니다.
- `kubernetes.io/tls`섹션에서를로 설정 합니다 `type` `secretObjects` .

SecretProviderClass의 예는 다음과 같습니다.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-tls
spec:
  provider: azure
  secretObjects:                            # secretObjects defines the desired state of synced K8s secret objects
  - secretName: ingress-tls-csi
    type: kubernetes.io/tls
    data: 
    - objectName: $CERT_NAME
      key: tls.key
    - objectName: $CERT_NAME
      key: tls.crt
  parameters:
    usePodIdentity: "false"
    keyvaultName: $AKV_NAME                 # the name of the AKV instance
    objects: |
      array:
        - |
          objectName: $CERT_NAME
          objectType: secret
    tenantId: $TENANT_ID                    # the tenant ID of the AKV instance
```

Kubernetes 클러스터에 SecretProviderClass를 적용 합니다.

```bash
kubectl apply -f secretProviderClass.yaml -n $NAMESPACE
```

## <a name="deploy-the-ingress-controller"></a>수신 컨트롤러 배포

### <a name="add-the-official-ingress-chart-repository"></a>공식 수신 차트 리포지토리 추가

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### <a name="configure-and-deploy-the-nginx-ingress"></a>NGINX 수신 구성 및 배포

위에서 설명한 것 처럼 시나리오에 따라 응용 프로그램 또는 수신 컨트롤러에 인증서를 바인딩하도록 선택할 수 있습니다. 선택 사항에 따라 아래 지침을 따르세요.

#### <a name="bind-certificate-to-application"></a>응용 프로그램에 인증서 바인딩

응용 프로그램의 배포에서는 비밀 저장소 CSI 드라이버의 Azure Key Vault 공급자를 참조 합니다.

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

#### <a name="bind-certificate-to-ingress-controller"></a>수신 컨트롤러에 인증서 바인딩

수신 컨트롤러의 배포는 비밀 저장소 CSI 드라이버의 Azure Key Vault 공급자를 참조 합니다.

> [!NOTE]
> 액세스 방법으로 Azure Active Directory (AAD) pod id를 사용 하지 않는 경우 다음을 사용 하 여 줄을 제거 합니다.`--set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME`

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME \
    -f - <<EOF
controller:
  extraVolumes:
      - name: secrets-store-inline
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "azure-tls"
  extraVolumeMounts:
      - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
EOF
```

Kubernetes 비밀이 생성 되었는지 확인 합니다.

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

## <a name="deploy-the-application"></a>애플리케이션 배포

그러면 시나리오에 따라 지침이 약간 변경 됩니다. 지금까지 선택한 시나리오에 해당 하는 지침을 따르세요.

### <a name="deploy-the-application-using-an-application-reference"></a>응용 프로그램 참조를 사용 하 여 응용 프로그램 배포

다음과 같은 내용으로 `deployment.yaml`라는 파일을 만듭니다.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
        volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
      volumes:
        - name: secrets-store-inline
          csi:
            driver: secrets-store.csi.k8s.io
            readOnly: true
            volumeAttributes:
              secretProviderClass: "azure-tls"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

클러스터에 적용 합니다.

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

Kubernetes 비밀이 생성 되었는지 확인 합니다.

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

### <a name="deploy-the-application-using-an-ingress-controller-reference"></a>수신 컨트롤러 참조를 사용 하 여 응용 프로그램 배포

다음과 같은 내용으로 `deployment.yaml`라는 파일을 만듭니다.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

클러스터에 적용 합니다.

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

## <a name="deploy-an-ingress-resource-referencing-the-secret"></a>비밀을 참조 하는 수신 리소스 배포

마지막으로 비밀을 참조 하는 Kubernetes 수신 리소스를 배포할 수 있습니다. `ingress.yaml`다음 내용으로 파일 이름을 만듭니다.

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-tls
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.test.com
    secretName: ingress-tls-csi
  rules:
  - host: demo.test.com
    http:
      paths:
      - backend:
          service:
            name: busybox-one
            port:
              number: 80
        path: /(.*)
      - backend:
          service:
            name: busybox-two
            port:
              number: 80
        path: /two(/|$)(.*)
```

`tls`앞에서 만든 비밀을 참조 하는 섹션을 적어 두고 클러스터에 파일을 적용 합니다.

```bash
kubectl apply -f ingress.yaml -n $NAMESPACE
```

## <a name="obtain-the-external-ip-address-of-the-ingress-controller"></a>수신 컨트롤러의 외부 IP 주소를 가져옵니다.

`kubectl get service`를 사용 하 여 수신 컨트롤러에 대 한 외부 IP 주소를 가져옵니다.

```bash
 kubectl get service -l app=nginx-ingress --namespace $NAMESPACE

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-1588032400-controller        LoadBalancer   10.0.255.157   52.xx.xx.xx      80:31293/TCP,443:31265/TCP   19m
nginx-ingress-1588032400-default-backend   ClusterIP      10.0.223.214   <none>           80/TCP                       19m 
```

## <a name="test-ingress-secured-with-tls"></a>TLS로 보안이 유지 되는 테스트 수신

`curl`를 사용 하 여 TLS로 수신이 올바르게 구성 되었는지 확인 합니다. 이전 단계에서 얻은 외부 IP를 사용 해야 합니다.

```bash
curl -v -k --resolve demo.test.com:443:52.xx.xx.xx https://demo.test.com

# You should see output similar to the following
*  subject: CN=demo.test.com; O=ingress-tls
*  start date: Oct 15 04:23:46 2021 GMT
*  expire date: Oct 15 04:23:46 2022 GMT
*  issuer: CN=demo.test.com; O=ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
```

<!-- LINKS INTERNAL -->
[csi-ss-identity-access]: ./csi-secrets-store-identity-access.md 
<!-- LINKS EXTERNAL -->
[kubernetes-ingress-tls]: https://kubernetes.io/docs/concepts/services-networking/ingress/#tls