---
title: Azure CLI 사용하여 OSM(Open Service Mesh) 추가 기능 설치
description: Azure CLI 사용하여 AKS(Open Service Mesh) Azure Kubernetes Service(추가 기능) 설치
services: container-service
ms.topic: article
ms.date: 11/10/2021
ms.author: pgibson
ms.openlocfilehash: ab8c6e0ccbe0bb971336666ebbb0c90cdb99203d
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132753705"
---
# <a name="install-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-using-azure-cli"></a>Azure CLI 사용하여 AKS(Open Service Mesh) Azure Kubernetes Service 추가 기능 설치

이 문서에서는 AKS 클러스터에 OSM 추가 기능 을 설치하고 설치 및 실행 중인지 확인하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli)

## <a name="install-the-osm-aks-add-on-on-your-cluster"></a>클러스터에 OSM AKS 추가 기능 설치

OSM AKS 추가 설치를 위해 `--enable-addons open-service-mesh` 클러스터를 만들거나 업데이트할 때 를 사용합니다.

다음 예제에서는 *myResourceGroup* 리소스 그룹을 만듭니다. 그런 다음, 3개의 노드와 OSM 추가 기능으로 *myAKSCluster* 클러스터를 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-addons open-service-mesh
```

기존 클러스터의 경우 를 `az aks enable-addons` 사용합니다. 다음은 그 예입니다.

> [!IMPORTANT]
> OSM 메시가 이미 클러스터에 있는 경우 기존 클러스터에서 OSM 추가 기능을 사용하도록 설정할 수 없습니다. OSM 추가 기능을 사용하도록 설정하기 전에 클러스터에서 기존 OSM 메시를 제거합니다.

```azurecli-interactive
az aks enable-addons \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --addons open-service-mesh
```

## <a name="get-the-credentials-for-your-cluster"></a>클러스터의 자격 증명 가져오기

명령을 사용하여 AKS 클러스터에 대한 자격 증명을 `az aks get-credentials` 얻습니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myAKSCluster* 에 대한 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="verify-the-osm-add-on-is-installed-on-your-cluster"></a>OSM 추가 기능 이 클러스터에 설치되어 있는지 확인합니다.

클러스터에서 OSM 추가 기능이 사용하도록 설정되어 있는지 확인하려면 *addonProfiles* 아래의 *openServiceMesh에* 대해 *활성화된* 값이 *true로* 표시되는지 확인합니다. 다음 예제에서는 *myResourceGroup* 에서 *myAKSCluster에* 대한 OSM 추가 기능의 상태를 보여줍니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster  --query 'addonProfiles.openServiceMesh.enabled'
```

## <a name="verify-the-osm-mesh-is-running-on-your-cluster"></a>OSM 메시가 클러스터에서 실행 중인지 확인합니다.

클러스터에서 OSM 추가 기능이 사용하도록 설정되었는지 확인하는 것 외에도 클러스터에서 실행되는 OSM 메시의 버전, 상태 및 구성을 확인할 수 있습니다.

클러스터에서 실행되는 OSM 메시의 버전을 확인하려면 를 사용하여 `kubectl` *osm-controller* 배포의 이미지 버전을 표시합니다. 다음은 그 예입니다.

```azurecli-interactive
kubectl get deployment -n kube-system osm-controller -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
```

다음 예제 출력에서는 OSM 메시의 버전 *0.11.1을* 보여 니다.

```output
$ kubectl get deployment -n kube-system osm-controller -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
mcr.microsoft.com/oss/openservicemesh/osm-controller:v0.11.1
```

클러스터에서 실행 중인 OSM 구성 요소의 상태를 확인하려면 를 사용하여 `kubectl` *app.kubernetes.io/name=openservicemesh.io* 배포, Pod 및 서비스의 상태를 표시합니다. 다음은 그 예입니다.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app.kubernetes.io/name=openservicemesh.io
kubectl get pods -n kube-system --selector app.kubernetes.io/name=openservicemesh.io
kubectl get services -n kube-system --selector app.kubernetes.io/name=openservicemesh.io
```

> [!IMPORTANT]
> Pod가 *실행 중* 이외의 상태(예: 보류 중)인 경우 클러스터에 *OSM을* 실행하는 데 충분한 리소스가 없을 수 있습니다. 클러스터에서 OSM을 계속 사용하기 전에 노드 수 및 VM SKU와 같은 클러스터의 크기를 검토합니다.

OSM 메시의 구성을 확인하려면 를 `kubectl get meshconfig` 사용합니다. 다음은 그 예입니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

다음 샘플 출력에서는 OSM 메시의 구성을 보여 줍니다.

```yaml
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

위의 예제 출력은 `enablePermissiveTrafficPolicyMode: true` OSM이 허용 트래픽 정책 모드를 사용하도록 설정됨을 의미하는 를 보여 줍니다. OSM 메시에서 허용 트래픽 모드를 사용하도록 설정한 경우:

* [SMI][smi] 트래픽 정책 적용은 무시됩니다.
* OSM은 서비스 메시의 일부인 서비스를 자동으로 검색합니다.
* OSM은 이러한 서비스와 통신할 수 있도록 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 만듭니다.



## <a name="delete-your-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요하지 않은 경우 명령을 사용하여 `az group delete` 리소스 그룹, 클러스터 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

또는 클러스터에서 OSM 추가 기능 및 관련 리소스를 제거할 수 있습니다. 자세한 내용은 [AKS 클러스터에서 OSM(Open Service Mesh) 추가 기능 제거를 참조하세요.][osm-uninstall]

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 OSM 추가 기능 을 설치하고 실행 중인 가 설치되어 있는지 확인하는 방법을 설명했습니다. OSM 메시에 샘플 애플리케이션을 배포하려면 [AKS에서 OSM을 사용하여 새 애플리케이션 관리를][osm-sample] 참조하세요.

[aks-ephemeral]: cluster-configuration.md#ephemeral-os
[osm-sample]: open-service-mesh-deploy-new-application.md
[osm-uninstall]: open-service-mesh-uninstall-add-on.md
[smi]: https://smi-spec.io/