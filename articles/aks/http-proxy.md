---
title: HTTP 프록시를 Azure Kubernetes Service(AKS) 노드 구성
description: AKS(Azure Kubernetes Service) 노드에 HTTP 프록시 구성 기능을 사용합니다.
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: c7ac0e0aa52cee39cee28958e3ce2eeeaca2c719
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248700"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>Azure Kubernetes Service HTTP 프록시 지원(미리 보기)

관리형 가상 네트워크 또는 사용자 지정 가상 네트워크에 배포된 AKS(Azure Kubernetes Service) 클러스터에는 제대로 작동하는 데 필요한 특정 아웃바운드 의존도가 있습니다. 이전에는 HTTP 프록시를 통해 인터넷 액세스를 라우팅해야 하는 환경에서는 이것이 문제였습니다. 노드는 인터넷 서비스에 액세스하는 데 필요한 구성, 환경 변수 및 인증서를 부트스트래핑할 방법이 없었습니다.

이 기능은 AKS 클러스터에 HTTP 프록시 지원을 추가하여 클러스터 운영자가 프록시 종속 환경에서 AKS에 필요한 네트워크 트래픽을 보호하는 데 사용할 수 있는 간단한 인터페이스를 노출합니다.

좀 더 복잡한 솔루션은 네트워크를 통해 보안 통신을 설정하기 위해 신뢰 체인을 만들어야 할 수 있습니다. 또한 이 기능을 사용하면 클러스터 부트스트랩의 일부로 노드에 신뢰할 수 있는 인증 기관을 설치할 수 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>제한 사항 및 기타 세부 정보

다음 시나리오는 지원되지 **않습니다**.
- 노드 풀당 다른 프록시 구성
- 클러스터 생성 후 프록시 설정 업데이트
- 사용자/암호 인증
- API 서버 통신을 위한 사용자 지정 CAS
- Windows 기반 클러스터
- VMAS(Virtual Machine 가용성 집합)를 사용하는 노드 풀

기본적으로 *httpProxy*, *httpsProxy* 및 *trustedCa에는 값이* 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli)

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI 설치

*또한 aks-preview* Azure CLI 확장 버전 0.5.25 이상도 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-httpproxyconfigpreview-preview-feature"></a>`HTTPProxyConfigPreview` 미리 보기 기능 등록

기능을 사용하려면 구독에서도 `HTTPProxyConfigPreview` 기능 플래그를 사용하도록 설정해야 합니다.

`HTTPProxyConfigPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "HTTPProxyConfigPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/HTTPProxyConfigPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configuring-an-http-proxy-using-azure-cli"></a>Azure CLI 사용하여 HTTP 프록시 구성 

AKS를 HTTP 프록시와 함께 사용하면 클러스터를 만들 때 [az aks create][az-aks-create] 명령을 사용하고 구성을 JSON 파일로 전달합니다.

구성 파일의 스키마는 다음과 같습니다.

```json
"httpProxyConfig": {
    "httpProxy": "string",
    "httpsProxy": "string",
    "noProxy": [
        "string"
    ],
    "trustedCa": "string"
}
```

`httpProxy`: 클러스터 외부에서 HTTP 연결을 만드는 데 사용할 프록시 URL입니다. URL 체계는 이어야 `http` 합니다.
`httpsProxy`: 클러스터 외부에서 HTTPS 연결을 만드는 데 사용할 프록시 URL입니다. 지정하지 않으면 `httpProxy` HTTP 및 HTTPS 연결 모두에 가 사용됩니다.
`noProxy`: 프록시를 제외할 대상 도메인 이름, 도메인, IP 주소 또는 기타 네트워크 CIDR 목록입니다.
`trustedCa`: 대체 CA 인증서 콘텐츠를 포함하는 `base64 encoded` 문자열입니다. 지금은 `PEM` 형식만 지원합니다. 또 다른 주의할 점은 k8s 시스템의 일부인 Go 기반 구성 요소와의 호환성을 위해 더 이상 사용되지 않는 일반 이름 인증서 대신 인증서가 지원되어야 한다는 `Subject Alternative Names(SANs)` 것입니다.

예제 입력: CA 인증서는 PEM 형식 인증서 콘텐츠의 base64로 인코딩된 문자열이어야 합니다.

```json
"httpProxyConfig": { 
     "httpProxy": "http://myproxy.server.com:8080/", 
     "httpsProxy": "https://myproxy.server.com:8080/", 
     "noProxy": [
         "localhost",
         "127.0.0.1"
     ],
     "trustedCA": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUgvVENDQmVXZ0F3SUJB...b3Rpbk15RGszaWFyCkYxMFlscWNPbWVYMXVGbUtiZGkvWG9yR2xrQ29NRjNURHg4cm1wOURCaUIvCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0="
}
```

파일을 만들고 *httpProxy , httpsProxy* 및 *noProxy* 에 대한 값을 제공합니다.  환경에 필요한 경우 *trustedCa* 값도 제공합니다. 다음으로 클러스터를 배포하고 플래그를 통해 파일 이름을 `http-proxy-config` 전달합니다.

```azurecli
az aks create -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config.json
```

클러스터는 노드에 구성된 HTTP 프록시를 통해 초기화됩니다.

## <a name="configuring-an-http-proxy-using-azure-resource-manager-arm-templates"></a>ARM(Azure Resource Manager) 템플릿을 사용하여 HTTP 프록시 구성

ARM 템플릿을 통해 구성된 HTTP 프록시를 통해 AKS 클러스터를 배포하는 것은 간단합니다. CLI 배포에 사용되는 것과 동일한 스키마가 속성 아래 정의에 있습니다. `Microsoft.ContainerService/managedClusters`

```json
"properties": {
    ...,
    "httpProxyConfig": {
        "httpProxy": "string",
        "httpsProxy": "string",
        "noProxy": [
            "string"
        ],
        "trustedCa": "string"
    }
}
```

템플릿에서 *httpProxy , httpsProxy* 및 *noProxy* 에 대한 값을 제공합니다.  필요한 경우 '*trustedCa* 에 대한 값도 제공합니다. 템플릿을 배포하면 클러스터가 노드에 구성된 HTTP 프록시를 통해 초기화됩니다.

## <a name="handling-ca-rollover"></a>CA 롤오버 처리

클러스터를 만든 후에는 *httpProxy*, *httpsProxy* 및 *noProxy* 값을 변경할 수 없습니다. 그러나 CA 인증서 롤링을 지원하기 위해 [az aks update][az-aks-update] 명령을 사용하여 *trustedCa* 값을 변경하고 클러스터에 적용할 수 있습니다.

예를 들어 *aks-proxy-config-2.json이라는* 새 CA 인증서의 base64로 인코딩된 문자열로 새 파일을 만들었다고 가정하면 다음 작업은 클러스터를 업데이트합니다.

```azurecli
az aks update -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config-2.json
```

## <a name="next-steps"></a>다음 단계
- AKS 클러스터의 네트워크 요구 사항에 대한 자세한 내용은 [AKS에서 클러스터 노드에 대한 송신 트래픽 제어를][aks-egress]참조하세요.


<!-- LINKS - internal -->
[aks-egress]: ./limit-egress-traffic.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az-extension-update