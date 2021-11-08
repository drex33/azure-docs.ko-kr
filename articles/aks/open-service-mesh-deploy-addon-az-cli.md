---
title: 개방형 서비스 메시 배포
description: Azure CLI를 사용 하 여 AKS (Azure Kubernetes Service)에 개방형 서비스 메시 배포
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 93ff4f0d8565f439bc16e887b0dd31e8f14249e9
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025752"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-using-azure-cli"></a>Azure CLI를 사용 하 여 Open Service 메시 AKS 추가 기능 배포

이 문서에서는 AKS에 OSM 추가 기능을 배포 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure CLI 버전 2.20.0 이상
- OSM 버전 v 0.11.1 이상

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>새 AKS 클러스터에 대한 OSM(Open Service Mesh) AKS (Azure Kubernetes Service) 추가 기능을 설치합니다.

새 AKS 클러스터 배포 시나리오의 경우 클러스터 만들기 작업에서 OSM 추가 기능을 사용하도록 설정하는 AKS 클러스터의 새 배포에서부터 시작합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제는 지정 된 위치 (지역)에 리소스 그룹을 만드는 데 사용 됩니다.

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>OSM 추가 기능을 사용하도록 설정하여 AKS 클러스터 배포

이제 OSM 추가 기능을 사용하도록 설정하여 새 AKS 클러스터를 배포합니다.

> [!NOTE]
> 다음 AKS 배포 명령은 임시 OS 디스크를 활용합니다. [AKS용 임시 OS 디스크](./cluster-configuration.md#ephemeral-os)에 대한 자세한 내용은 여기에서 찾을 수 있습니다.

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

> [!NOTE]
> OSM 추가 기능 배포를 성공적으로 수행 하려면 하나의 OSM 메시 인스턴스만 클러스터에 배포 해야 합니다. 클러스터에 다른 OSM 메시 인스턴스가 있는 경우 명령을 실행 하기 전에 제거 하십시오 `enable-addons` .

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

### <a name="check-osm-add-on-version"></a>OSM 추가 기능 버전 확인

설치 된 OSM 추가 기능 버전은 v 0.11.1 이상 이어야 합니다. 이를 확인 하기 위해 다음 명령을 실행 하 여 이미지 태그에서 인코딩된 osm 컨트롤러에 대 한 이미지 버전을 확인할 수 있습니다. 

```azurecli-interactive
kubectl get deployment -n kube-system osm-controller -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>AKS OSM 추가 기능 구성에 액세스

현재 OSM MeshConfig 리소스를 통해 OSM 컨트롤러 구성에 액세스 하 고 구성할 수 있습니다. CLI를 통해 OSM controller 구성 설정을 보려면 아래와 같이 **kubectl** get 명령을 사용 합니다.

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

**EnablePermissiveTrafficPolicyMode** 가 **true** 로 구성 되어 있는지 확인 합니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

> [!WARNING]
> 계속하기 전에 허용 트래픽 정책 모드가 true로 설정되어 있는지 확인하세요. 그렇지 않으면 아래 명령을 사용하여 **true** 로 변경하세요.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>AKS 클러스터에 대한 OSM(Open Service Mesh) 추가 기능 사용 안 함

OSM 추가 기능을 사용하지 않도록 설정하려면 다음 명령을 실행합니다.

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```
OSM 추가 기능을 사용 하지 않도록 설정 하면 클러스터에 다음 리소스가 남아 있습니다.
1. OSM meshconfig 사용자 지정 리소스
2. OSM 제어 평면 비밀
3. OSM 변경 webhook 구성
4. OSM webhook 구성 유효성 검사
5. OSM CRDs

> [!IMPORTANT]
> OSM 추가 기능을 사용 하지 않도록 설정한 후 이러한 추가 리소스를 제거 해야 합니다. 나중에 OSM 추가 기능을 다시 사용 하도록 설정 하는 경우 클러스터에 이러한 리소스를 두면 문제가 발생할 수 있습니다.

이러한 나머지 리소스를 제거 하려면:

1. Meshconfig config 리소스를 삭제 합니다.
```azurecli-interactive
kubectl delete --ignore-not-found meshconfig -n kube-system osm-mesh-config
```

2. OSM 제어 평면 비밀 삭제
```azurecli-interactive
kubectl delete --ignore-not-found secret -n kube-system osm-ca-bundle mutating-webhook-cert-secret validating-webhook-cert-secret crd-converter-cert-secret
```

3. OSM 변경 webhook 구성 삭제
```azurecli-interactive
kubectl delete mutatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-injector --ignore-not-found
```

4. OSM 유효성 검사 webhook 구성 삭제
```azurecli-interactive
kubectl delete validatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-controller --ignore-not-found
```

5. OSM CRDs를 삭제 합니다. OSM의 CRDs 및 삭제 방법에 대 한 지침은 [이 설명서](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/uninstall/#removal-of-osm-cluster-wide-resources)를 참조 하세요.

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
