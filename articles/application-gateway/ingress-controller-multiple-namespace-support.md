---
title: Application Gateway 수신 컨트롤러에 대해 여러 네임스페이스 지원 사용 설정
description: 이 문서에서는 Application Gateway 수신 컨트롤러를 사용하여 Kubernetes 클러스터에서 여러 네임스페이스 지원을 사용하도록 설정하는 방법에 관해 설명합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 646f44fb610b6749960c553b31dce4b3a9bfe579
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132862521"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러를 사용하여 AKS 클러스터에서 여러 네임스페이스 지원 사용 설정

## <a name="motivation"></a>동기
Kubernetes [네임스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)를 통해 Kubernetes 클러스터를 분할하고 더 큰 규모의 팀의 하위 그룹에 할당할 수 있습니다. 그런 다음 이러한 하위 팀은 리소스, 보안, 구성 등의 더 세부적인 제어를 통해 인프라를 배포하고 관리할 수 있습니다. Kubernetes를 사용하면 각 네임스페이스 내에서 하나 이상의 수신 리소스를 독립적으로 정의할 수 있습니다.

버전 0.7 이상의 [Azure AGIC(Application Gateway Kubernetes 수신 컨트롤러)](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md)는 여러 네임스페이스에서 이벤트를 수집하고 이를 관찰할 수 있습니다. AKS 관리자가 [App Gateway](https://azure.microsoft.com/services/application-gateway/)를 수신으로 사용하기로 결정하는 경우 모든 네임스페이스는 동일한 Application Gateway 인스턴스를 사용합니다. 단일 수신 컨트롤러 설치는 액세스할 수 있는 네임스페이스를 모니터링하고 연결된 Application Gateway를 구성합니다.

AGIC 버전 0.7은 Helm 구성에서 하나 이상의 다른 네임스페이스로 명시적으로 변경되지 않는 한 계속해서 `default` 네임스페이스를 독점적으로 관찰합니다(아래 섹션 참조).

## <a name="enable-multiple-namespace-support"></a>여러 네임스페이스 지원 사용
여러 네임스페이스 지원을 사용하도록 설정하려면 다음을 수행합니다.
1. 다음 방법 중 하나를 사용하여 [helm-config.yaml](#sample-helm-config-file) 파일을 수정합니다.
   - [helm-config.yaml](#sample-helm-config-file)에서 `watchNamespace` 키를 완전히 삭제합니다. - AGIC에서 모든 네임스페이스를 관찰합니다.
   - `watchNamespace`를 빈 문자열로 설정합니다. - AGIC에서 모든 네임스페이스를 관찰합니다.
   - 쉼표로 구분된 여러 네임스페이스(`watchNamespace: default,secondNamespace`)를 추가합니다. - AGIC에서 해당 네임스페이스를 독점적으로 관찰합니다.
2. `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`를 사용하여 Helm 템플릿 변경 사항을 적용합니다.

여러 네임스페이스를 관찰할 수 있는 기능과 함께 배포되면 AGIC는 다음을 수행합니다.
  - 액세스할 수 있는 모든 네임스페이스의 수신 리소스 나열
  - `kubernetes.io/ingress.class: azure/application-gateway` 주석이 추가된 수신 리소스에 대한 필터링
  - 결합된 [Application Gateway 구성](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) 작성
  - [ARM](../azure-resource-manager/management/overview.md)을 통해 연결된 Application Gateway에 구성 적용

## <a name="conflicting-configurations"></a>충돌하는 구성
여러 네임스페이스가 지정된 [수신 리소스](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource)는 단일 Application Gateway에 대해 충돌하는 구성을 만들도록 AGIC에 지시할 수 있습니다. (두 수신은 인스턴스에 대해 동일한 도메인을 클레임합니다.)

계층 구조의 맨 위에 있는 **수신기**(IP 주소, 포트, 호스트)와 **라우팅 규칙**(바인딩 수신기, 백 엔드 풀, HTTP 설정)은 여러 네임스페이스/수신에서 만들고 공유할 수 있습니다.

반면에 경로, 백 엔드 풀, HTTP 설정, TLS 인증서는 하나의 네임스페이스에 의해서만 만들어질 수 있으며 중복된 항목은 제거됩니다.

예를 들어 `www.contoso.com`에 대해 네임스페이스 `staging` 및 `production`으로 정의된 다음과 같은 중복된 수신 리소스를 고려합니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

`www.contoso.com`에 대한 트래픽을 각 Kubernetes 네임스페이스로 라우팅해야 하는 두 개의 수신 리소스가 있더라도 하나의 백 엔드만 트래픽을 처리할 수 있습니다. AGIC는 리소스 중 하나에 대해 “선착순”으로 구성을 만듭니다. 두 개의 수신 리소스가 동시에 만들어지는 경우 사전순으로 앞선 리소스가 우선합니다. 위의 예제에서 `production` 수신에 대한 설정만 만들 수 있습니다. Application Gateway는 다음 리소스로 구성됩니다.

  - 수신기: `fl-www.contoso.com-80`
  - 라우팅 규칙: `rr-www.contoso.com-80`
  - 백 엔드 풀: `pool-production-contoso-web-service-80-bp-80`
  - HTTP 설정: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - 상태 프로브: `pb-production-contoso-web-service-80-websocket-ingress`

‘수신기’ 및 ‘라우팅 규칙’을 제외하고 만들어진 Application Gateway 리소스는 생성된 네임스페이스(`production`)의 이름을 포함합니다. 

두 개의 수신 리소스가 서로 다른 시점에 AKS 클러스터에 도입되는 경우 AGIC가 Application Gateway를 다시 구성하고 트래픽을 `namespace-B`에서 `namespace-A`로 다시 라우팅하는 시나리오로 끝날 가능성이 큽니다.

예를 들어 `staging`을 먼저 추가한 경우 AGIC는 트래픽을 준비 백 엔드 풀로 라우팅하는 Application Gateway를 구성합니다. 이후 단계에서 `production` 수신을 도입하면 AGIC가 Application Gateway를 다시 프로그래밍하여 `production` 백 엔드 풀에 대한 트래픽을 라우팅하기 시작합니다.

## <a name="restrict-access-to-namespaces"></a>네임스페이스에 대한 액세스 제한
기본적으로 AGIC는 네임스페이스 내에서 주석이 추가된 수신에 따라 Application Gateway를 구성합니다. 이 동작을 제한하려면 다음 옵션을 사용할 수 있습니다.
  - [helm-config.yaml](#sample-helm-config-file)의 `watchNamespace` YAML 키를 통해 AGIC가 관찰해야 하는 네임스페이스를 명시적으로 정의하여 네임스페이스를 제한
  - [Role/RoleBinding](../aks/azure-ad-rbac.md)을 사용하여 AGIC를 특정 네임스페이스로 제한

## <a name="sample-helm-config-file"></a>샘플 Helm 구성 파일

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