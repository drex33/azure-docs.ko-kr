---
title: Application Gateway 수신 컨트롤러 문제 해결
description: 이 문서에서는 Application Gateway 수신 컨트롤러와 관련된 일반적인 질문 및/또는 문제를 해결하는 방법에 대한 설명서를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: 91b95c09185415e15b5db0e0ecd5e91c79de247e
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132866334"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>수신 컨트롤러와 관련된 일반적인 질문 또는 문제 해결

[Azure Cloud Shell](https://shell.azure.com/)은 AKS 및 AGIC 설치와 관련된 문제를 해결하는 가장 편리한 방법입니다. [shell.azure.com](https://shell.azure.com/)에서 셸을 시작하거나 링크를 클릭하여 셸을 시작합니다.

[![Embed 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>간단한 Kubernetes 앱으로 테스트

아래 단계에서는 다음을 가정합니다.
  - 고급 네트워킹을 사용하도록 설정한 AKS 클러스터가 있습니다.
  - AGIC는 AKS 클러스터에 설치되었습니다.
  - AKS 클러스터와 공유된 VNET에 Application Gateway가 이미 있습니다.

Application Gateway + AKS + AGIC 설치가 올바르게 설정되었는지 확인하려면 가능한 가장 간단한 앱을 배포합니다.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

위의 스크립트에서 모든 줄을 한 번에 복사한 후 [Azure Cloud Shell](https://shell.azure.com/)에 붙여넣습니다. `cat`부터 마지막 `EOF`를 포함하여 전체 명령을 복사했는지 확인합니다.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

이 앱을 성공적으로 배포하면 AKS 클러스터 위에 새 Pod, 서비스 및 수신이 표시됩니다.

[Cloud Shell](https://shell.azure.com/)을 통해 Pod 목록을 가져옵니다. `kubectl get pods -o wide`
'test-agic-app-pod'라는 pod가 만들어질 것으로 예상됩니다. 여기에는 IP 주소가 있습니다. 이 주소는 AKS와 함께 사용되는 Application Gateway의 VNET 내에 있어야 합니다.

![목록에 test-agic-app-pod가 포함된 pod 목록을 보여 주는 Azure Cloud Shell의 Bash 창 스크린샷](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

서비스 목록을 가져옵니다. `kubectl get services -o wide`. 'test-agic-app-service'라는 서비스가 표시됩니다.

![목록에 test-agic-app-pod가 포함된 서비스 목록을 보여 주는 Azure Cloud Shell의 Bash 창 스크린샷](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

수신 목록을 가져옵니다. `kubectl get ingress`. 'test-agic-app-ingress'라는 수신 리소스가 생성될 것으로 예상됩니다. 리소스의 호스트 이름은 'test.agic.contoso.com'입니다.

![목록에 test-agic-app-ingres가 포함된 수신 목록을 보여 주는 Azure Cloud Shell의 Bash 창 스크린샷](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

pod 중 하나는 AGIC가 됩니다. `kubectl get pods`는 pod 목록을 표시합니다. 이중 하나는 'ingress-azure'로 시작됩니다. `kubectl logs <name-of-ingress-controller-pod>`를 사용하여 해당 pod의 모든 로그를 가져와 성공적으로 배포되었는지 확인합니다. 성공적으로 배포되면 로그에 다음 줄이 추가됩니다.
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

또는 [Cloud Shell](https://shell.azure.com/)에서 `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`을 사용하여 성공적인 Application Gateway 구성을 나타내는 줄만 검색할 수 있습니다. 여기서 `<ingress-azure....>`는 AGIC pod의 정확한 이름입니다.

Application Gateway에는 다음 구성이 적용됩니다.

- 수신기: ![listener](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- 라우팅 규칙: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- 백 엔드 풀:
  - 백 엔드 주소 풀에는 하나의 IP 주소가 있으며 이 주소는 `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)을 사용하여 이전에 관찰한 Pod의 IP 주소와 일치합니다.


마지막으로 [Cloud Shell](https://shell.azure.com/) 내에서 `cURL` 명령을 사용하여 새로 배포된 앱에 대한 HTTP 연결을 설정할 수 있습니다.

1. `kubectl get ingress`를 사용하여 Application Gateway의 공용 IP 주소를 가져옵니다.
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` 사용

![테스트 앱에 대한 HTTP 연결을 성공적으로 설정하는 cURL 명령을 보여 주는 Azure Cloud Shell의 Bash 창 스크린샷](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

`HTTP/1.1 200 OK`의 결과는 Application Gateway + AKS + AGIC 시스템이 예상대로 작동하고 있음을 나타냅니다.


## <a name="inspect-kubernetes-installation"></a>Kubernetes 설치 검사

### <a name="pods-services-ingress"></a>Pod, 서비스, 수신
AGIC(Application Gateway 수신 컨트롤러)는 [배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) 또는 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [서비스](https://kubernetes.io/docs/concepts/services-networking/service/), [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/) 등의 Kubernetes 리소스를 지속적으로 모니터링합니다.


AGIC가 예상대로 작동하려면 다음 조건을 충족해야 합니다.
  1. AKS에는 하나 이상의 정상 **Pod** 가 있어야 합니다.
     이러한 Pod가 있는지는 `kubectl get pods -o wide --show-labels`를 사용하여 [Cloud Shell](https://shell.azure.com/)에서 확인합니다. `apsnetapp`을 포함하는 Pod가 있는 경우 출력은 다음과 같을 수 있습니다.
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 일치하는 `selector` 레이블을 통해 위의 Pod를 참조하는 하나 이상의 **서비스** 가 있어야 합니다.
     이러한 서비스가 있는지는 `kubectl get services -o wide`를 사용하여 [Cloud Shell](https://shell.azure.com/)에서 확인합니다.
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. 위의 서비스를 참조하여 `kubernetes.io/ingress.class: azure/application-gateway`를 사용하여 주석 처리된 **수신** 이 있어야 합니다. 이러한 수신이 있는지는 `kubectl get ingress -o wide --show-labels`를 사용하여 [Cloud Shell](https://shell.azure.com/)에서 확인합니다.
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 위의 수신 주석을 봅니다. `kubectl get ingress aspnetapp -o yaml`(`aspnetapp`을 수신 이름으로 대체함)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     수신 리소스는 `kubernetes.io/ingress.class: azure/application-gateway`를 사용하여 주석 처리해야 합니다.
 

### <a name="verify-observed-namespace"></a>관찰된 네임스페이스 확인

* Kubernetes 클러스터의 기존 네임스페이스를 가져옵니다. 앱이 실행 중인 네임스페이스는 무엇인가요? AGIC가 해당 네임스페이스를 감시하고 있나요? 관찰된 네임스페이스를 올바르게 구성하는 방법은 [다중 네임스페이스 지원](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) 설명서를 참조하세요.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC Pod는 `default` 네임스페이스에 있어야 합니다(`NAMESPACE` 열 참조). 정상 Pod는 `STATUS` 열에서 `Running` 상태여야 합니다. 하나 이상의 AGIC Pod가 있어야 합니다.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* AGIC Pod가 정상이 아닌 경우(위 명령의 `STATUS` 열이 `Running`이 아닌 경우):
  - 이유를 설명하는 로그를 가져옵니다. `kubectl logs <pod-name>`
  - Pod의 이전 인스턴스에 대해 다음을 수행합니다. `kubectl logs <pod-name> --previous`
  - Pod를 설명하여 더 많은 컨텍스트를 얻습니다. `kubectl describe pod <pod-name>`


* Kubernetes [서비스](https://kubernetes.io/docs/concepts/services-networking/service/) 및 [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/) 리소스가 있나요?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/)이 `kubernetes.io/ingress.class: azure/application-gateway`를 사용하여 주석 처리되었나요? AGIC는 이 주석이 있는 Kubernetes 수신 리소스만 감시합니다.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC는 특정 심각한 오류에 대한 Kubernetes 이벤트를 내보냅니다. 다음을 볼 수 있습니다.
  - `kubectl get events --sort-by=.metadata.creationTimestamp`를 통해 터미널에서
  - [Kubernetes 웹 UI(대시보드)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)를 사용하여 브라우저에서


## <a name="logging-levels"></a>로깅 수준

AGIC에는 3개의 로깅 수준이 있습니다. 수준 1은 기본 수준으로, 최소 수의 로그 줄을 표시합니다.
반면 수준 5는 ARM에 적용된 구성의 삭제된 콘텐츠를 포함하여 모든 로그를 표시합니다.

Kubernetes 커뮤니티는 [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) 도구에 대한 9가지 로깅 수준을 설정했습니다. 이 리포지토리에서는 이중에서 유사한 의미 체계를 사용하는 3가지 수준을 활용합니다.


| 자세한 정도 | 설명 |
|-----------|-------------|
|  1        | 기본 로그 수준: 시작 정보, 경고 및 오류를 표시합니다. |
|  3        | 이벤트 및 변경 내용에 대한 확장된 정보, 만든 개체 목록 |
|  5        | 마샬링된 개체를 로깅합니다. ARM에 적용된 삭제된 JSON 구성을 표시합니다. |


세부 정보 표시 수준은 [helm-config.yaml](#sample-helm-config-file) 파일의 `verbosityLevel` 변수를 통해 조정할 수 있습니다. 세부 정보 표시 수준을 `5`로 늘려 [ARM](../azure-resource-manager/management/overview.md)에 디스패치된 JSON 구성을 가져옵니다.
  - [helm-config.yaml](#sample-helm-config-file)에서 `verbosityLevel: 5`만 하나의 줄에 추가하고 다시 설치합니다.
  - `kubectl logs <pod-name>`을 사용하여 로그를 가져옵니다.

### <a name="sample-helm-config-file"></a>샘플 Helm 구성 파일
```yaml
# This file contains the essential configs for the ingress controller helm chart

# Verbosity level of the App Gateway Ingress Controller
verbosityLevel: 3

################################################################################
# Specify which application gateway the ingress controller will manage
#
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>

    # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
    # This prohibits AGIC from applying config for any host/path.
    # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
    shared: false

################################################################################
# Specify which kubernetes namespace the ingress controller will watch
# Default value is "default"
# Leaving this variable out or setting it to blank or empty string would
# result in Ingress Controller observing all acessible namespaces.
#
# kubernetes:
#   watchNamespace: <namespace>

################################################################################
# Specify the authentication with Azure Resource Manager
#
# Two authentication methods are available:
# - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
armAuth:
    type: aadPodIdentity
    identityResourceID: <identityResourceId>
    identityClientID:  <identityClientId>

## Alternatively you can use Service Principal credentials
# armAuth:
#    type: servicePrincipal
#    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --role Contributor --sdk-auth | base64 -w0" >>

################################################################################
# Specify if the cluster is Kubernetes RBAC enabled or not
rbac:
    enabled: false # true/false

# Specify aks cluster related information. THIS IS BEING DEPRECATED.
aksClusterConfiguration:
    apiServerAddress: <aks-api-server-address>
```