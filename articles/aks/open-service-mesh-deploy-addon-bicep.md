---
title: Bicep을 사용하여 Open Service Mesh AKS 추가 기능 배포
description: Bicep을 사용하여 AKS(Azure Kubernetes Service Open Service Mesh) 배포
services: container-service
ms.topic: article
ms.date: 9/20/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: b0452588b8eb9eebbf8ab592ad2d0a8a0e9a6b92
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066789"
---
# <a name="deploy-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-using-bicep"></a>Bicep을 사용하여 AKS(Open Service Mesh) Azure Kubernetes Service(추가 기능) 배포

이 문서에서는 [Bicep](../azure-resource-manager/bicep/index.yml) 템플릿을 사용하여 AKS에 OSM 추가 기능 배포 방법을 설명합니다.

[Bicep](/azure/azure-resource-manager/bicep/overview)은 선언적 구문을 사용하여 Azure 리소스를 배포하는 DSL(도메인 특정 언어)입니다. Bicep은 코드로서의 인프라 Azure 리소스를 배포하기 위한 Azure [ARM](/azure/azure-resource-manager/templates/overview) 템플릿을 만드는 대신 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure CLI 버전 2.20.0 이상
- OSM 버전 v0.11.1 이상
- AKS 배포에 사용되는 SSH 공개 키
- Bash 터미널 활용 [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code [Bicep 확장](../azure-resource-manager/bicep/install.md)

## <a name="install-the-aks-preview-extension"></a>aks-preview 확장 설치

_aks-preview_ Azure CLI 확장 버전 0.5.24 이상이 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 _aks-preview_ Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>`AKS-OpenServiceMesh` 미리 보기 기능 등록

Open Service Mesh 추가 기능을 사용할 수 있는 AKS 클러스터를 만들려면 구독에서 `AKS-OpenServiceMesh` 기능 플래그를 사용하도록 설정해야 합니다.

`AKS-OpenServiceMesh`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

상태가 _Registered_ 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 _ContainerService_ 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-osm-aks-add-on-for-a-new-aks-cluster-using-bicep"></a>Bicep을 사용하여 새 AKS 클러스터에 대한 OSM AKS 추가 기능 설치

새 AKS 클러스터 배포 시나리오의 경우 클러스터 만들기 작업에서 OSM 추가 기능을 사용하도록 설정된 AKS 클러스터의 새로운 배포부터 시작합니다. 다음 지침 집합은 임시 디스크를 [`kubenet`](./configure-kubenet.md) 사용하고, CNI를 사용하고, AKS OSM 추가 기능을 사용하도록 설정하는 AKS 클러스터를 배포하는 일반 Bicep 템플릿을 사용합니다. 고급 배포 시나리오는 [Bicep](../azure-resource-manager/bicep/overview.md) 설명서를 참조하세요.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 리소스 그룹을 사용하여 관련 리소스를 연결할 수 있습니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제는 지정된 Azure 위치(지역)에 이라는 리소스 그룹을 만드는 데 사용됩니다.

```azurecli-interactive
az group create --name <my-osm-bicep-aks-cluster-rg> --location <azure-region>
```

### <a name="create-the-main-and-parameters-bicep-files"></a>기본 및 매개 변수 Bicep 파일 만들기

bash 터미널이 열려 있는 Visual Studio Code 사용하여 필요한 Bicep 배포 파일을 저장할 디렉터리를 만듭니다. 다음 예제에서는 라는 디렉터리를 만들고 `bicep-osm-aks-addon` 디렉터리를 변경합니다.

```azurecli-interactive
mkdir bicep-osm-aks-addon
cd bicep-osm-aks-addon
```

다음 예제와 같이 main 및 parameters 파일을 모두 만듭니다.

```azurecli-interactive
touch osm.aks.bicep && touch osm.aks.parameters.json
```

파일을 `osm.aks.bicep` 열고 다음 예제 콘텐츠를 복사한 다음 파일을 저장합니다.

```azurecli-interactive
// https://docs.microsoft.com/azure/aks/troubleshooting#what-naming-restrictions-are-enforced-for-aks-resources-and-parameters
@minLength(3)
@maxLength(63)
@description('Provide a name for the AKS cluster. The only allowed characters are letters, numbers, dashes, and underscore. The first and last character must be a letter or a number.')
param clusterName string
@minLength(3)
@maxLength(54)
@description('Provide a name for the AKS dnsPrefix. Valid characters include alphanumeric values and hyphens (-). The dnsPrefix can\'t include special characters such as a period (.)')
param clusterDNSPrefix string
param k8Version string
param sshPubKey string


resource aksCluster 'Microsoft.ContainerService/managedClusters@2021-03-01' = {
  name: clusterName
  location: resourceGroup().location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    kubernetesVersion: k8Version
    dnsPrefix: clusterDNSPrefix
    enableRBAC: true
    agentPoolProfiles: [
      {
        name: 'agentpool'
        count: 3
        vmSize: 'Standard_DS2_v2'
        osDiskSizeGB: 30
        osDiskType: 'Ephemeral'
        osType: 'Linux'
        mode: 'System'
      }
    ]
    linuxProfile: {
      adminUsername: 'adminUserName'
      ssh: {
        publicKeys: [
          {
            keyData: sshPubKey
          }
        ]
      }
    }
    addonProfiles: {
        openServiceMesh: {
            enabled: true
            config: {}
      }
    }
  }
}
```

파일을 `osm.aks.parameters.json` 열고 다음 예제 콘텐츠를 복사합니다. 배포 관련 매개 변수를 추가한 다음 파일을 저장합니다.

> [!NOTE]
> `osm.aks.parameters.json`는 Bicep 배포에 필요한 예제 템플릿 매개 변수 파일입니다. 배포 환경에 맞게 지정된 매개 변수를 업데이트해야 합니다. 이 예제에서 사용하는 특정 매개 변수 값을 업데이트하려면 다음 매개 변수가 필요합니다. _clusterName,_ _clusterDNSPrefix,_ _k8Version_ 및 _sshPubKey_ 입니다. 해당 지역에서 지원되는 Kubernetes 버전 목록을 찾으려면 `az aks get-versions --location <region>` 명령을 사용하세요.

```azurecli-interactive
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "<YOUR CLUSTER NAME HERE>"
    },
    "clusterDNSPrefix": {
      "value": "<YOUR CLUSTER DNS PREFIX HERE>"
    },
    "k8Version": {
      "value": "<YOUR SUPPORTED KUBERNETES VERSION HERE>"
    },
    "sshPubKey": {
      "value": "<YOUR SSH KEY HERE>"
    }
  }
}
```

### <a name="deploy-the-bicep-file"></a>Bicep 파일 배포

이전에 만든 Bicep 파일을 배포하려면 터미널을 열고 명령을 사용하여 Azure CLI 대한 Azure 계정에 `az login` 인증합니다. Azure 구독에 인증되면 배포를 위해 다음 명령을 실행합니다.

```azurecli-interactive
az group create --name osm-bicep-test --location eastus2

az deployment group create \
  --name OSMBicepDeployment \
  --resource-group osm-bicep-test \
  --template-file osm.aks.bicep \
  --parameters @osm.aks.parameters.json
```

배포가 완료되면 배포에 성공했음을 나타내는 메시지가 표시됩니다.

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

현재 OSM MeshConfig 리소스를 통해 OSM 컨트롤러 구성에 액세스하고 구성할 수 있으며, 아래와 같이 CLI를 통해 OSM 컨트롤러 구성 설정을 볼 수 있습니다. **kubectl** get 명령을 사용합니다.

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

**enablePermissiveTrafficPolicyMode가** **true** 로 구성되어 있습니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색합니다. 검색된 서비스에는 이러한 서비스 간의 통신을 허용하도록 각 Envoy 프록시 사이드카에서 프로그래밍된 트래픽 정책 규칙이 있습니다.

> [!WARNING]
> 계속하기 전에 허용 트래픽 정책 모드가 true로 설정되어 있는지 확인하세요. 그렇지 않으면 아래 명령을 사용하여 **true** 로 변경하세요.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 Azure CLI 사용하여 배포 테스트 리소스 그룹을 삭제합니다.

```
az group delete --name osm-bicep-test
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
