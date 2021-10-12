---
title: HTTP 프록시를 사용 하 여 AKS (Azure Kubernetes Service) 노드 구성
description: AKS (Azure Kubernetes Service) 노드에 대 한 HTTP 프록시 구성 기능을 사용 합니다.
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: 19a1392756596a1cbfe7000ebd9c7013c053c153
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856926"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>Azure Kubernetes Service의 HTTP 프록시 지원 (미리 보기)

관리 되는 가상 네트워크 또는 사용자 지정 가상 네트워크에 배포 된 경우와 상관 없이 Azure Kubernetes 서비스 (AKS) 클러스터에는 제대로 작동 하는 데 필요한 특정 아웃 바운드 종속성이 있습니다. 이전에는 인터넷 액세스가 HTTP 프록시를 통해 라우팅되는 환경에서 이것이 문제 였습니다. 노드는 인터넷 서비스에 액세스 하는 데 필요한 구성, 환경 변수 및 인증서를 부트스트래핑 하는 방법이 없습니다.

이 기능은 AKS 클러스터에 HTTP 프록시 지원을 추가 하 여 클러스터 운영자가 프록시 종속 환경에서 AKS-필요한 네트워크 트래픽을 보호 하는 데 사용할 수 있는 간단한 인터페이스를 노출 합니다.

일부 복잡 한 솔루션은 네트워크를 통해 보안 통신을 설정 하기 위해 신뢰 체인을 만들어야 할 수 있습니다. 이 기능을 사용 하면 클러스터 부트스트래핑의 일부로 노드에 신뢰할 수 있는 인증 기관을 설치할 수도 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>제한 사항 및 기타 세부 정보

다음 시나리오는 지원되지 **않습니다**.
- 노드 풀 당 다른 프록시 구성
- 프록시 설정 업데이트 후 클러스터 만들기
- 사용자/암호 인증
- API 서버 통신용 사용자 지정 Ca
- Windows 기반 클러스터
- VMAS (가상 머신 가용성 집합)를 사용 하는 노드 풀

기본적으로 *Httpproxy*, *HttpsProxy 및* *ca* 에는 값이 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli)

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

## <a name="configuring-an-http-proxy-using-azure-cli"></a>Azure CLI를 사용 하 여 HTTP 프록시 구성 

HTTP 프록시에 AKS를 사용 하는 작업은 [az AKS create][az-aks-create] 명령을 사용 하 고 CONFIGURATION을 JSON 또는 yaml 파일로 전달 하 여 클러스터를 만들 때 수행 됩니다.

구성 파일에 대 한 스키마는 다음과 같습니다.

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

파일을 만들고 *httpproxy*, *HttpsProxy* 및 *noproxy* 에 대 한 값을 제공 합니다. 환경에 필요한 경우에는 해당 하는 *ca* 값도 제공 합니다. 다음으로, 플래그를 통해 파일 이름을 전달 하 여 클러스터를 배포 `proxy-configuration-file` 합니다.

```azurecli
az aks create -n $clusterName -g $resourceGroup --proxy-configuration-file aks-proxy-config.json
```

클러스터는 노드에 구성 된 HTTP 프록시를 사용 하 여 초기화 됩니다.

## <a name="configuring-an-http-proxy-using-azure-resource-manager-arm-templates"></a>ARM (Azure Resource Manager) 템플릿을 사용 하 여 HTTP 프록시 구성

ARM 템플릿을 통해 구성 된 HTTP 프록시를 사용 하 여 AKS 클러스터를 배포 하는 것은 간단 합니다. CLI 배포에 사용 되는 것과 같은 스키마가 속성의 정의에 있습니다 `Microsoft.ContainerService/managedClusters` .

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

템플릿에서 *Httpproxy*, *HttpsProxy* 및 *noproxy* 에 대 한 값을 제공 합니다. 필요한 경우 ' 트 *ca*'의 값도 제공 합니다. 템플릿을 배포 하 고 클러스터는 노드에 구성 된 HTTP 프록시를 사용 하 여 초기화 해야 합니다.

## <a name="handling-ca-rollover"></a>CA 롤오버 처리

클러스터를 만든 후에는 *Httpproxy*, *HttpsProxy* 및 *noproxy* 의 값을 변경할 수 없습니다. 그러나 롤링 CA 인증서를 지원 하기 위해 [az aks update][az-aks-update] 명령을 사용 하 여 클러스터에 해당 하는 클러스터에 대 한 값을 변경 하 고 적용할 *수 있습니다.*

예를 들어 *aks* 이라는 새 CA 인증서의 base64 인코딩 문자열을 사용 하 여 새 파일을 만든 경우 다음 작업을 수행 하면 클러스터가 업데이트 됩니다.

```azurecli
az aks update -n $clusterName -g $resourceGroup --proxy-configuration-file aks-proxy-config-2.json
```

## <a name="next-steps"></a>다음 단계
- AKS 클러스터의 네트워크 요구 사항에 대 한 자세한 내용은 [AKS에서 클러스터 노드에 대 한 송신 트래픽 제어][aks-egress]를 참조 하세요.


<!-- LINKS - internal -->
[aks-egress]: ./limit-egress-traffic.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register