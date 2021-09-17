---
title: Open Service Mesh 배포(미리 보기)
description: AKS(open Service Mesh on Azure Kubernetes Service) 배포
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 8476d6c01c0c388eefad42eb89d65011c57bfa65
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441483"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS 추가 기능 배포(미리 보기)
이 문서에서는 AKS에 OSM 추가 기능 배포 방법을 설명합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.9.1 이상
- JSON 프로세서 "jq" 버전 1.6 이상

## <a name="install-the-aks-preview-extension"></a>aks-preview 확장 설치

*aks-preview* Azure CLI 확장 버전 0.5.24 이상이 필요합니다. [az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>`AKS-OpenServiceMesh` 미리 보기 기능 등록

Open Service Mesh 추가 기능을 사용할 수 있는 AKS 클러스터를 만들려면 구독에서 `AKS-OpenServiceMesh` 기능 플래그를 사용하도록 설정해야 합니다.

다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 `AKS-OpenServiceMesh` 기능 플래그를 등록합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

상태가 _Registered_ 로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 _Microsoft.ContainerService_ 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>새 AKS 클러스터에 대한 OSM(Open Service Mesh) AKS (Azure Kubernetes Service) 추가 기능을 설치합니다.

새 AKS 클러스터 배포 시나리오의 경우 클러스터 만들기 작업에서 OSM 추가 기능을 사용하도록 설정하는 AKS 클러스터의 새 배포에서부터 시작합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예에서는 _eastus2_ 위치(지역)에 _myOsmAksGroup_ 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>OSM 추가 기능을 사용하도록 설정하여 AKS 클러스터 배포

이제 OSM 추가 기능을 사용하도록 설정하여 새 AKS 클러스터를 배포합니다.

> [!NOTE]
> 다음 AKS 배포 명령은 AKS 배포 예제에 OS 사용 후 삭제 디스크를 활용합니다. [AKS용 임시 OS 디스크](./cluster-configuration.md#ephemeral-os)에 대한 자세한 내용은 여기에서 찾을 수 있습니다.

```azurecli-interactive
az aks create -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg> --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AKS 클러스터 액세스 자격 증명 가져오기

새 관리되는 Kubernetes 클러스터에 대한 액세스 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>기존 AKS 클러스터에 대해 OSM(Open Service Mesh) AKS(Azure Kubernetes Service) 추가 기능을 사용하도록 설정합니다.

기존 AKS 클러스터 시나리오의 경우 이미 배포된 기존 AKS 클러스터에 OSM 추가 기능을 사용하도록 설정합니다.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>기존 AKS 클러스터에 OSM 추가 기능 사용

AKS OSM 추가 기능을 사용하도록 설정하려면 `open-service-mesh` 매개 변수를 전달하는 `az aks enable-addons --addons` 명령을 실행해야 합니다.

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <my-osm-aks-cluster-rg> -n <my-osm-aks-cluster-name>
```

AKS OSM 추가 기능이 설치되었는지 확인하려면 아래에 표시된 출력과 유사한 출력이 표시되어야 합니다.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>AKS OSM 추가 기능 설치의 유효성을 검사합니다.

AKS OSM 추가 기능이 사용 설정되고 실행되기 위한 모든 구성 요소를 확인하기 위해 실행할 수 있는 몇 가지 명령이 있습니다.

먼저 클러스터의 추가 기능 프로필을 쿼리하여 설치된 추가 기능의 사용 설정 상태를 확인할 수 있습니다. 다음 명령은 "true"를 반환해야 합니다.

```azurecli-interactive
az aks list -g <my-osm-aks-cluster-rg> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

다음 `kubectl` 명령은 osm-controller의 상태를 보고합니다.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>AKS OSM 추가 기능 구성에 액세스

현재 OSM MeshConfig 리소스를 통해 OSM 컨트롤러 구성에 액세스하고 구성할 수 있습니다. CLI를 통해 OSM 컨트롤러 구성 설정을 보려면 아래와 같이 **kubectl** get 명령을 사용합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

MeshConfig의 출력은 다음과 같습니다.

```
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

**enablePermissiveTrafficPolicyMode가** **true** 로 구성되어 있습니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다. 허용 트래픽 모드에 대한 자세한 내용은 을 방문하여 [허용 트래픽 정책 모드](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/) 문서를 읽어보십시오. 

> [!WARNING]
> 계속하기 전에 허용 트래픽 정책 모드가 true로 설정되어 있는지 확인하세요. 그렇지 않으면 아래 명령을 사용하여 **true** 로 변경하세요.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```