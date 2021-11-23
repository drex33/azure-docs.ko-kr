---
title: 기존 Application Gateway를 사용하여 수신 컨트롤러 만들기
description: 이 문서에서는 기존 Application Gateway를 사용하여 Application Gateway 수신 컨트롤러를 배포하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a6691e8020a7d9c8264f6f7bf2f0f3c222150a1f
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132934556"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>기존 Application Gateway를 사용하여 AGIC(Application Gateway 수신 컨트롤러) 설치

AGIC(Application Gateway 수신 컨트롤러)는 Kubernetes 클러스터 내의 Pod입니다.
AGIC는 Kubernetes [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/) 리소스를 모니터링하고 Kubernetes 클러스터의 상태에 따라 Application Gateway 구성을 만들고 적용합니다.

## <a name="outline"></a>대략적인 내용은 다음과 같습니다.
- [필수 구성 요소](#prerequisites)
- [ARM(Azure Resource Manager) 인증](#azure-resource-manager-authentication)
    - 옵션 1: [aad-pod-identity 설정](#set-up-aad-pod-identity) 및 ARM에서 Azure ID 만들기
    - 옵션 2: [서비스 주체 사용](#using-a-service-principal)
- [Helm을 사용하여 수신 컨트롤러 설치](#install-ingress-controller-as-a-helm-chart)
- [다중 클러스터/공유 Application Gateway](#multi-cluster--shared-application-gateway): 하나 이상의 AKS 클러스터 및/또는 다른 Azure 구성 요소 간에 Application Gateway가 공유되는 환경에 AGIC를 설치합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 다음과 같은 도구 및 인프라가 이미 설치되어 있다고 가정합니다.
- [Azure CNI(Container Networking Interface)](../aks/configure-azure-cni.md)가 있는 [AKS](https://azure.microsoft.com/services/kubernetes-service/)
- AKS와 동일한 가상 네트워크의 [Application Gateway v2](./tutorial-autoscale-ps.md)
- AKS 클러스터에 설치된 [AAD Pod ID](https://github.com/Azure/aad-pod-identity)
- [Cloud Shell](https://shell.azure.com/)은 `az` CLI, `kubectl` 및 `helm`이 설치된 Azure 셸 환경입니다. 이러한 도구는 아래 명령에 필요합니다.

AGIC를 설치하기 전에 __Application Gateway 구성을 백업하세요__.
  1. [Azure Portal](https://portal.azure.com/)을 사용하여 `Application Gateway` 인스턴스로 이동
  2. `Export template`에서 `Download` 클릭

다운로드한 zip 파일에는 필요한 경우 App Gateway를 복원하는 데 사용할 수 있는 JSON 템플릿, bash 및 PowerShell 스크립트가 있습니다.

## <a name="install-helm"></a>Helm 설치
[Helm](../aks/kubernetes-helm.md)은 Kubernetes 패키지 관리자입니다. 이를 활용하여 `application-gateway-kubernetes-ingress` 패키지를 설치합니다.
[Cloud Shell](https://shell.azure.com/)을 사용하여 Helm을 설치합니다.

1. [Helm](../aks/kubernetes-helm.md)을 설치하고 다음을 실행하여 `application-gateway-kubernetes-ingress` Helm 패키지를 추가합니다.

    - *Kubernetes RBAC를 사용하는* AKS 클러스터

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *Kubernetes RBAC를 사용하지 않는* AKS 클러스터

    ```bash
    helm init
    ```

1. AGIC 리포지토리를 추가합니다.
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager 인증

AGIC는 Kubernetes API 서버 및 Azure Resource Manager와 통신합니다. 이러한 API에 액세스하려면 ID가 필요합니다.

## <a name="set-up-aad-pod-identity"></a>AAD Pod ID 설정

[AAD Pod ID](https://github.com/Azure/aad-pod-identity)는 AGIC와 비슷하며 AKS에서도 실행 되는 컨트롤러입니다. Azure Active Directory ID를 Kubernetes pod에 바인딩합니다. Kubernetes pod의 애플리케이션이 다른 Azure 구성 요소와 통신할 수 있도록 하는 데 이 ID가 필요합니다. 여기에 나오는 특정 사례에서 [ARM](../azure-resource-manager/management/overview.md)에 대한 HTTP 요청을 수행하려면 AGIC pod에 대한 권한 부여가 필요합니다.

[AAD Pod ID 설치 지침](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) 에 따라 이 구성 요소를 AKS에 추가합니다.

다음으로 Azure ID를 만들고 사용 권한을 ARM에 부여해야 합니다.
[Cloud Shell](https://shell.azure.com/)을 사용하여 다음 모든 명령을 실행하고 ID를 만듭니다.

1. **AKS 노드와 동일한 리소스 그룹에** Azure ID를 만듭니다. 올바른 리소스 그룹을 선택하는 것이 중요합니다. 아래 명령에 필요한 리소스 그룹은 AKS 포털 창에서 참조되는 리소스 그룹이 *아닙니다*. `aks-agentpool` 가상 머신의 리소스 그룹입니다. 일반적으로 이러한 리소스 그룹은 `MC_`으로 시작하고 AKS의 이름을 포함합니다. 예: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 아래의 역할 할당 명령에 대해 새로 만든 ID에 대한 `principalId`를 가져와야 합니다.

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. ID `Contributor`에 Application Gateway에 대한 액세스 권한을 부여합니다. 이 경우 `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`와 같이 표시되는 Application Gateway의 ID가 필요합니다.

    `az network application-gateway list --query '[].id'`를 사용하여 구독의 Application Gateway ID 목록을 가져옵니다.

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. ID `Reader`에 Application Gateway 리소스 그룹에 대한 액세스 권한을 부여합니다. 리소스 그룹 ID는 `/subscriptions/A/resourceGroups/B`와 같습니다. `az group list --query '[].id'`를 사용하여 모든 리소스 그룹을 가져올 수 있습니다.

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>서비스 주체 사용
Kubernetes 암호를 통해 ARM에 대한 액세스 권한을 AGIC에 제공할 수도 있습니다.

1. Active Directory 서비스 주체를 만들고 base64로 인코딩합니다. base64 인코딩은 JSON blob을 Kubernetes에 저장하는 데 필요합니다.

```azurecli
az ad sp create-for-rbac --role Contributor --sdk-auth | base64 -w0
```

2. base64 인코딩 JSON blob을 `helm-config.yaml` 파일에 추가합니다. `helm-config.yaml`에 대한 자세한 내용은 다음 섹션에 나와 있습니다.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>수신 컨트롤러를 Helm Chart로 설치
처음 몇 단계에서 Kubernetes 클러스터에 Helm의 Tiller을 설치합니다. [Cloud Shell](https://shell.azure.com/)를 사용하여 AGIC Helm 패키지를 설치합니다.

1. `application-gateway-kubernetes-ingress` Helm 리포지토리를 추가하고 Helm 업데이트를 수행합니다.

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. AGIC를 구성하는 helm-config.xml을 다운로드합니다.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    또는 아래에서 YAML 파일을 복사합니다. 
    
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --role Contributor --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. helm-config.yaml을 편집하고 `appgw` 및 `armAuth` 값을 채웁니다.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` 및 `<identity-client-id>`는 이전 섹션에서 설정한 Azure AD ID의 속성입니다. `az identity show -g <resourcegroup> -n <identity-name>` 명령을 실행하여 이 정보를 검색할 수 있습니다. 여기서 `<resourcegroup>`은 최상위 수준의 AKS 클러스터 개체, Application Gateway 및 관리 ID가 배포되는 리소스 그룹입니다.

1. 이전 단계의 `helm-config.yaml` 구성을 사용하여 Helm Chart `application-gateway-kubernetes-ingress`를 설치합니다.

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    또는 `helm-config.yaml` 및 Helm 명령을 하나의 단계로 결합할 수 있습니다.
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --role Contributor --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. 새로 만든 pod의 로그를 확인하여 제대로 시작되었는지 확인합니다.

Azure Application Gateway를 사용하여 HTTP 또는 HTTPS를 통해 AKS 서비스를 인터넷에 노출하는 방법을 이해하려면 [이 방법 가이드](ingress-controller-expose-service-over-http-https.md)를 참조하세요.



## <a name="multi-cluster--shared-application-gateway"></a>다중 클러스터/공유 Application Gateway
기본적으로 AGIC는 연결된 Application Gateway에 대한 모든 소유권이 있다고 가정합니다. AGIC 버전 0.8.0 이상에서는 다른 Azure 구성 요소와 단일 Application Gateway를 공유할 수 있습니다. 예를 들어, Virtual Machine Scale Set에서 호스트된 앱 및 AKS 클러스터에 동일한 Application Gateway를 사용할 수 있습니다.

이 설정을 사용하도록 지정하기 전에 __Application Gateway의 구성을 백업__ 하세요.
  1. [Azure Portal](https://portal.azure.com/)을 사용하여 `Application Gateway` 인스턴스로 이동
  2. `Export template`에서 `Download` 클릭

다운로드한 zip 파일에는 필요한 경우 Application Gateway를 복원하는 데 사용할 수 있는 JSON 템플릿, bash 및 PowerShell 스크립트가 있습니다.

### <a name="example-scenario"></a>예제 시나리오
두 웹 사이트에 대한 트래픽을 관리하는 가상의 Application Gateway를 살펴보겠습니다.
  - `dev.contoso.com` - 새 AKS에서 호스트되며 Application Gateway 및 AGIC를 사용합니다.
  - `prod.contoso.com` - [Azure Virtual Machine Scale Set](https://azure.microsoft.com/services/virtual-machine-scale-sets/)에서 호스트됩니다.

기본 설정을 사용할 경우 AGIC는 가리키는 Application Gateway에 대해 100% 소유권이 있다고 가정합니다. AGIC는 App Gateway의 모든 구성을 덮어씁니다. (Application Gateway)에서 `prod.contoso.com`에 대한 수신기를 수동으로 만들어야 경우 kubernetes 수신에서 정의하지 않고 AGIC는 몇 초 안에 `prod.contoso.com` 구성을 삭제합니다.

AGIC를 설치하고 Virtual Machine Scale Set 머신에서도 `prod.contoso.com`을 제공하려면 `dev.contoso.com`만 구성하도록 AGIC를 제한해야 합니다. 이 방법은 다음 [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)를 인스턴스화하여 용이하게 진행할 수 있습니다.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

위의 명령은 `AzureIngressProhibitedTarget` 개체를 만듭니다. 이렇게 하면 AGIC(버전 0.8.0 이상)에서 `prod.contoso.com`에 대한 Application Gateway 구성이 있음을 인식하게 되며 해당 호스트 이름과 관련된 구성을 변경하지 말라는 명시적 지침을 받게 됩니다.


### <a name="enable-with-new-agic-installation"></a>새 AGIC 설치로 사용
AGIC(버전 0.8.0 이상)를 Application Gateway 구성의 하위 집합으로 제한하려면 `helm-config.yaml` 템플릿을 수정합니다.
`appgw:` 섹션 아래에서 `shared` 키를 추가하고 `true`로 설정합니다.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Helm 변경 내용을 적용합니다.
  1. 다음을 사용하여 `AzureIngressProhibitedTarget` CRD가 설치되어 있는지 확인합니다.
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Helm을 업데이트합니다.
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

결과적으로 AKS에는 `prohibit-all-targets`라는 `AzureIngressProhibitedTarget`의 새 인스턴스가 있습니다.
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

이름에서 알 수 있듯이 개체 `prohibit-all-targets`는 AGIC에서 모든 호스트 및 경로에 대한 구성을 변경하지 못하게 합니다.
`appgw.shared=true`를 사용하여 Helm을 설치하면 AGIC가 배포되지만 Application Gateway는 변경되지 않습니다.


### <a name="broaden-permissions"></a>사용 권한 확장
`appgw.shared=true` 및 기본 `prohibit-all-targets`를 사용하는 Helm은 AGIC가 어떠한 구성도 적용하지 못하게 합니다.

다음을 사용하여 AGIC 사용 권한을 확장하세요.
1. 특정 설정을 사용하여 새 `AzureIngressProhibitedTarget`을 만듭니다.
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. 고유한 사용자 지정 금지를 만든 후에만 매우 포괄적인 기본 금지를 삭제할 수 있습니다.

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>기존 AGIC 설치에 사용
클러스터에 이미 작동 중인 AKS, Application Gateway 및 구성된 AGIC가 있다고 가정해 보겠습니다. `prod.contoso.com`에 대한 수신이 있으며 AKS에서 이에 대한 트래픽을 처리하고 있습니다. `staging.contoso.com`을 기존 Application Gateway에 추가하지만 [VM](https://azure.microsoft.com/services/virtual-machines/)에서 호스트해야 합니다. 기존 Application Gateway를 다시 사용하고 `staging.contoso.com`에 대한 수신기 및 백 엔드 풀을 수동으로 구성하겠습니다. 그러나 Application Gateway 구성을 수동으로 조정하면([Portal](https://portal.azure.com), [ARM API](/rest/api/resources/) 또는 [Terraform](https://www.terraform.io/)을 통해) AGIC의 전체 소유권 가정과 충돌합니다. 변경 내용을 적용하는 즉시, AGIC에서 덮어쓰거나 삭제합니다.

AGIC에서 구성의 하위 집합을 변경하지 못하도록 방지할 수 있습니다.

1. `AzureIngressProhibitedTarget` 개체를 만듭니다.
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. 새로 만든 개체를 확인합니다.
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. 포털을 통해 Application Gateway 구성을 수정하고 수신기, 라우팅 규칙, 백 엔드 등을 추가합니다. 만든 새 개체(`manually-configured-staging-environment`)는 AGIC가 `staging.contoso.com`과 관련된 Application Gateway 구성을 덮어쓸 수 없도록 합니다.
