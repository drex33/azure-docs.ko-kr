---
title: Azure Kubernetes Service TLS를 사용하여 NGINX 수신 컨트롤러를 사용하도록 비밀 저장소 CSI 드라이버 설정
description: AKS(Azure Kubernetes Service)용 TLS를 사용하여 NGINX 수신 컨트롤러를 사용하도록 비밀 저장소 CSI 드라이버를 구성하는 방법입니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: template-how-to
ms.openlocfilehash: f7c40dbcf0944ca5c78182d72346f3fc295ef50c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848746"
---
# <a name="set-up-secrets-store-csi-driver-to-enable-nginx-ingress-controller-with-tls"></a>TLS를 사용하여 NGINX 수신 컨트롤러를 사용하도록 비밀 저장소 CSI 드라이버 설정

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터 및 AKV(Azure Key Vault) 인스턴스를 통해 TLS를 통해 NGINX 수신 컨트롤러를 보안하는 프로세스를 안내합니다. 자세한 내용은 [Kubernetes의 TLS를 참조하세요.][kubernetes-ingress-tls]

다음 두 가지 방법 중 하나를 사용하여 수신 TLS 인증서를 클러스터로 가져올 수 있습니다.

- **애플리케이션** - 애플리케이션 배포 매니페스트는 공급자 볼륨을 선언하고 탑재합니다. 애플리케이션이 배포된 경우에만 클러스터에서 사용할 수 있는 인증서가 있으며 애플리케이션이 제거되면 비밀도 제거됩니다. 이 시나리오는 애플리케이션의 보안 인프라 및 클러스터와의 통합을 담당하는 개발 팀에 적합합니다.
- **수신 컨트롤러** - 수신 배포는 공급자 볼륨을 선언하고 탑재하도록 수정됩니다. 수신 Pod를 만들 때 비밀을 가져옵니다. 애플리케이션의 Pod는 TLS 인증서에 액세스할 수 없습니다. 이 시나리오는 한 팀(즉, IT)이 인프라 및 네트워킹 구성 요소(HTTPS TLS 인증서 포함)를 관리하고 프로비전하고 다른 팀이 애플리케이션 수명 주기를 관리하는 시나리오에 적합합니다. 이 경우 수신은 단일 네임스페이스/워크로드에만 적용되며 애플리케이션과 동일한 네임스페이스에 배포됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 시작하기 전에 Azure CLI 버전이 >= 인지 `2.30.0` 확인하거나 [최신 버전을 설치합니다.](/cli/azure/install-azure-cli)
- 비밀 저장소 CSI 드라이버가 구성된 AKS 클러스터.
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

먼저 새 네임스페이스를 만듭니다.

```bash
export NAMESPACE=ingress-test
```

```azurecli-interactive
kubectl create ns $NAMESPACE
```

액세스 [ID를 제공하고][csi-ss-identity-access] 그에 따라 SecretProviderClass YAML을 구성하는 방법을 선택합니다. 추가 필수 구성 요소:
- `objectType=secret`AKV에서 프라이빗 키와 인증서를 가져오는 유일한 방법이기 때문에 를 사용해야 합니다.
- `kubernetes.io/tls` `type` `secretObjects` 을 섹션의 로 설정합니다.

SecretProviderClass의 예는 다음을 참조하세요.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
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

SecretProviderClass를 Kubernetes 클러스터에 적용합니다.

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

위에서 설명한 것처럼 시나리오에 따라 애플리케이션 또는 수신 컨트롤러에 인증서를 바인딩하도록 선택할 수 있습니다. 선택 항목에 따라 아래 지침을 따릅니다.

#### <a name="bind-certificate-to-application"></a>애플리케이션에 인증서 바인딩

애플리케이션의 배포는 비밀 저장소 CSI 드라이버의 Azure Key Vault 공급자를 참조합니다.

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

#### <a name="bind-certificate-to-ingress-controller"></a>수신 컨트롤러에 인증서 바인딩

수신 컨트롤러의 배포는 비밀 저장소 CSI 드라이버의 Azure Key Vault 공급자를 참조합니다.

> [!NOTE]
> 액세스 방법으로 Azure Active Directory(AAD) Pod ID를 사용하지 않는 경우 를 사용하여 줄을 제거합니다.`--set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME`

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

Kubernetes 비밀이 생성되었는지 확인합니다.

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

## <a name="deploy-the-application"></a>애플리케이션 배포

시나리오에 따라 지침이 약간 변경됩니다. 지금까지 선택한 시나리오에 해당하는 지침을 따릅니다.

### <a name="deploy-the-application-using-an-application-reference"></a>애플리케이션 참조를 사용하여 애플리케이션 배포

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

그리고 클러스터에 적용합니다.

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

Kubernetes 비밀이 생성되었는지 확인합니다.

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

### <a name="deploy-the-application-using-an-ingress-controller-reference"></a>수신 컨트롤러 참조를 사용하여 애플리케이션 배포

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

그리고 클러스터에 적용합니다.

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

## <a name="deploy-an-ingress-resource-referencing-the-secret"></a>비밀을 참조하는 수신 리소스 배포

마지막으로 비밀을 참조하는 Kubernetes 수신 리소스를 배포할 수 있습니다. `ingress.yaml`다음 내용이 있는 파일 이름을 만듭니다.

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

앞에서 만든 `tls` 비밀을 참조하는 섹션을 기록하고 클러스터에 파일을 적용합니다.

```bash
kubectl apply -f ingress.yaml -n $NAMESPACE
```

## <a name="obtain-the-external-ip-address-of-the-ingress-controller"></a>수신 컨트롤러의 외부 IP 주소 가져오기

를 사용하여 `kubectl get service` 수신 컨트롤러의 외부 IP 주소를 얻습니다.

```bash
 kubectl get service -l app=nginx-ingress --namespace $NAMESPACE

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-1588032400-controller        LoadBalancer   10.0.255.157   52.xx.xx.xx      80:31293/TCP,443:31265/TCP   19m
nginx-ingress-1588032400-default-backend   ClusterIP      10.0.223.214   <none>           80/TCP                       19m 
```

## <a name="test-ingress-secured-with-tls"></a>TLS를 통해 보안이 유지되는 테스트 수신

를 사용하여 `curl` 수신이 TLS로 제대로 구성되었는지 확인합니다. 이전 단계에서 얻은 외부 IP를 사용해야 합니다.

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