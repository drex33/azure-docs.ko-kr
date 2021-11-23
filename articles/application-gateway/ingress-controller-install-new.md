---
title: 새 Application Gateway를 사용하여 수신 컨트롤러 만들기
description: 이 문서에서는 새 Application Gateway를 사용하여 Application Gateway 수신 컨트롤러를 배포하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: ed31b054180cab654c8d2251e440bbdce69eaa17
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132934461"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>새 Application Gateway를 사용하여 AGIC(Application Gateway 수신 컨트롤러)를 설치하는 방법

아래 지침에서는 AGIC(Application Gateway 수신 컨트롤러)가 기존 구성 요소가 없는 환경에 설치되어 있다고 가정합니다.

## <a name="required-command-line-tools"></a>필요한 명령줄 도구

아래의 모든 명령줄 작업에 [Azure Cloud Shell](https://shell.azure.com/)을 사용하는 것이 좋습니다. shell.azure.com에 접속하거나 링크를 클릭하여 셸을 시작합니다.

[![Embed 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)

또는 다음 아이콘을 사용하여 Azure Portal에서 Cloud Shell을 시작합니다.

![포털 시작](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure Cloud Shell](https://shell.azure.com/)에는 이미 필요한 도구가 모두 있습니다. 다른 환경을 사용하는 경우 다음 명령줄 도구가 설치되어 있는지 확인하세요.

* `az` - Azure CLI: [설치 지침](/cli/azure/install-azure-cli)
* `kubectl` - Kubernetes 명령줄 도구: [설치 지침](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` - Kubernetes 패키지 관리자: [설치 지침](https://github.com/helm/helm/releases/latest)
* `jq` - 명령줄 JSON 프로세서: [설치 지침](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>ID 만들기

아래 단계를 수행하여 AAD(Azure Active Directory) [서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만듭니다. `appId`, `password` 및 `objectId` 값을 기록하세요. 이 값은 다음 단계에서 사용됩니다.

1. AD 서비스 주체 만들기([Azure RBAC에 대한 자세한 정보](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    JSON 출력의 `appId` 및 `password` 값은 다음 단계에서 사용됩니다.


1. 이전 명령의 출력에서 `appId`를 사용하여 새 서비스 주체의 `objectId`를 가져옵니다.
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    이 명령의 출력은 `objectId`이며, 아래 Azure Resource Manager 템플릿에서 사용됩니다.

1. 나중에 Azure Resource Manager 템플릿 배포에 사용할 매개 변수 파일을 만듭니다.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    **Kubernetes RBAC** 를 사용하는 클러스터를 배포하려면 `aksEnableRBAC` 필드를 `true`로 설정합니다.

## <a name="deploy-components"></a>구성 요소 배포
이 단계에서는 구독에 다음 구성 요소가 추가됩니다.

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- 2개의 [서브넷](../virtual-network/virtual-networks-overview.md) 이 있는 [가상 네트워크](../virtual-network/virtual-networks-overview.md)
- [공용 IP 주소](../virtual-network/ip-services/virtual-network-public-ip-address.md)
- [AAD Pod ID](https://github.com/Azure/aad-pod-identity/blob/master/README.md)에서 사용되는 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)

1. Azure Resource Manager 템플릿을 다운로드하고 필요에 따라 템플릿을 수정합니다.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. `az cli`를 사용하여 Azure Resource Manager 템플릿을 배포합니다. 이 작업은 최대 5분 정도 걸릴 수 있습니다.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. 배포가 완료되면 배포 출력을 `deployment-outputs.json`이라는 파일로 다운로드합니다.
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러 설정

이전 섹션의 지침을 사용하여 새 AKS 클러스터 및 Application Gateway를 만들고 구성했습니다. 이제 샘플 앱 및 수신 컨트롤러를 새로운 Kubernetes 인프라에 배포할 준비가 되었습니다.

### <a name="setup-kubernetes-credentials"></a>Kubernetes 자격 증명 설정
다음 단계에서는 새 Kubernetes 클러스터에 연결하는 데 사용할 수 있는 [kubectl](https://kubectl.docs.kubernetes.io/) 명령을 설정해야 합니다. [Cloud Shell](https://shell.azure.com/)에는 이미 `kubectl`이 설치되어 있습니다. Kubernetes에 대한 자격 증명을 얻기 위해 `az` CLI를 사용합니다.

새로 배포된 AKS에 대한 자격 증명을 가져옵니다([자세히 알아보기](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)).
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>AAD Pod ID 설치
  Azure Active Directory Pod ID는 [ARM(Azure Resource Manager)](../azure-resource-manager/management/overview.md)에 대한 토큰 기반 액세스를 제공합니다.

  [AAD Pod ID](https://github.com/Azure/aad-pod-identity)는 Kubernetes 클러스터에 다음 구성 요소를 추가합니다.
   * Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
   * [MIC(Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) 구성 요소
   * [NMI(Node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) 구성 요소


AAD Pod ID를 클러스터에 설치하려면:

   - *Kubernetes RBAC를 사용하는* AKS 클러스터

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *Kubernetes RBAC를 사용하지 않는* AKS 클러스터

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Helm 설치
[Helm](../aks/kubernetes-helm.md)은 Kubernetes 패키지 관리자입니다. 이를 활용하여 `application-gateway-kubernetes-ingress` 패키지를 설치합니다.

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

### <a name="install-ingress-controller-helm-chart"></a>수신 컨트롤러 Helm 차트 설치

1. 위에서 만든 `deployment-outputs.json` 파일을 사용하고 다음 변수를 만듭니다.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --role Contributor --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. 새로 다운로드한 helm-config.xml을 편집하고 섹션 `appgw` 및 `armAuth`를 작성합니다.
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   값
     - `verbosityLevel`: AGIC 로깅 인프라의 세부 정보 표시 수준을 설정합니다. 가능한 값은 [로깅 수준](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)을 참조하세요.
     - `appgw.subscriptionId`: Application Gateway가 있는 Azure 구독 ID입니다. 예: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Application Gateway가 만들어진 Azure Resource Group의 이름입니다. 예: `app-gw-resource-group`
     - `appgw.name`: Application Gateway의 이름입니다. 예: `applicationgatewayd0f0`
     - `appgw.shared`: 이 부울 플래그는 기본적으로 `false`로 설정되어야 합니다. [공유 Application Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)가 필요하면 `true`로 설정합니다.
     - `kubernetes.watchNamespace`: AGIC가 조사할 네임스페이스를 지정합니다. 이는 단일 문자열 값 또는 쉼표로 구분된 네임스페이스 목록일 수 있습니다.
    - `armAuth.type`: `aadPodIdentity` 또는 `servicePrincipal`일 수 있습니다.
    - `armAuth.identityResourceID`: Azure 관리 ID의 리소스 ID입니다.
    - `armAuth.identityClientId`: ID의 클라이언트 ID입니다. ID에 대한 자세한 내용은 아래를 참조하세요.
    - `armAuth.secretJSON`: 서비스 주체 비밀 유형을 선택한 경우(`armAuth.type`을 `servicePrincipal`로 설정한 경우)에만 필요합니다. 


   > [!NOTE]
   > `identityResourceID` 및 `identityClientID`는 [구성 요소 배포](ingress-controller-install-new.md#deploy-components) 단계 중에 만들어진 값으로, 다음 명령을 사용하여 다시 가져올 수 있습니다.
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > 위의 명령의 `<resource-group>`에는 Application Gateway의 리소스 그룹이 있습니다. `<identity-name>`은 만들어진 ID의 이름입니다. 지정된 구독에 대한 모든 ID는 `az identity list`를 사용하여 나열할 수 있습니다.


1. Application Gateway 수신 컨트롤러 패키지 설치

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>샘플 앱 설치
Application Gateway, AKS 및 AGIC가 설치되면 [Azure Cloud Shell](https://shell.azure.com/)을 통해 샘플 앱을 설치할 수 있습니다.

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
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
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

또는 다음을 수행할 수 있습니다.

* 위의 YAML 파일을 다운로드합니다.

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* YAML 파일을 적용합니다.

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>기타 예
이 [방법 가이드](ingress-controller-expose-service-over-http-https.md)에는 HTTP 또는 HTTPS를 통해 AKS 서비스를 Application Gateway 인터넷에 공개하는 방법에 대한 더 많은 예가 포함되어 있습니다.