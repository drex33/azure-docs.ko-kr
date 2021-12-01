---
title: '자습서: Azure Arc 지원 Kubernetes 또는 AKS (Azure Kubernetes Service) 클러스터에서 Flux v 2와 GitOps 사용'
description: 이 자습서에서는 Flux v2에서 GitOps를 사용 하 여 Azure Arc 및 AKS 클러스터에서 구성 및 응용 프로그램 배포를 관리 하는 방법을 보여 줍니다.
keywords: GitOps, Flux, Kubernetes, K8s, Azure, Arc, AKS, Azure Kubernetes Service, 컨테이너, devops
services: azure-arc, aks
ms.service: azure-arc
ms.date: 11/22/2021
ms.topic: tutorial
author: csand-msft
ms.author: csand
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: f507a72613158d36eb21c6097dc0019d9e9b3717
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407909"
---
# <a name="tutorial-use-gitops-with-flux-v2-in-azure-arc-enabled-kubernetes-or-azure-kubernetes-service-aks-clusters-public-preview"></a>자습서: Azure Arc 지원 Kubernetes 또는 AKS (Azure Kubernetes Service) 클러스터에서 Flux v 2와 GitOps 사용 (공개 미리 보기)

Flux v2를 사용 하는 GitOps는 Azure Arc 사용 Kubernetes 연결 된 클러스터 또는 AKS (Azure Kubernetes Service) 관리 클러스터에서 클러스터 확장으로 사용 하도록 설정할 수 있습니다. 클러스터 확장을 설치한 후에는 `microsoft.flux` `fluxConfigurations` Git 리포지토리 원본을 클러스터에 동기화 하는 하나 이상의 리소스를 만들고 클러스터를 원하는 상태로 조정할 수 있습니다. GitOps를 사용 하 여 클러스터 구성 및 응용 프로그램 배포에 대 한 진위의 원본으로 Git 리포지토리를 사용할 수 있습니다.

이 문서에는 Kubernetes 클러스터에서 Azure GitOps를 사용 하는 방법에 대 한 자세한 자습서가 포함 되어 있습니다. 살펴보기 전에 [Flux를 사용 하는 GitOps가 개념적으로 작동 하는 방식에 대해 알아보겠습니다](./conceptual-gitops-flux2.md).

Azure Arc 사용 Kubernetes의 일반 공급은 Flux v1을 사용 하는 GitOps를 포함 합니다. 여기에 설명 된 Flux v2와 GitOps의 공개 미리 보기는 Azure Arc 사용 Kubernetes 및 AKS 둘 다에서 사용할 수 있습니다. Flux v2는 앞으로 사용 되며 Flux v1은 결국 사용 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure CLI 또는 포털을 통해 GitOps를 관리 하려면 다음이 필요 합니다.

### <a name="for-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 사용 가능 Kubernetes 클러스터의 경우

* 실행 중인 Azure Arc 사용 Kubernetes 연결 된 클러스터입니다.
  * [Kubernetes 클러스터를 호와 사용 하도록 설정 하는 방법에 대해 알아봅니다](./quickstart-connect-cluster.md). 아웃 바운드 프록시를 통해 연결 해야 하는 경우에 [는 프록시 설정을 사용 하 여 Arc 에이전트를 설치](./quickstart-connect-cluster.md?tabs=azure-cli#4a-connect-using-an-outbound-proxy-server)해야 합니다.
* ' Kubernetes/connectedClusters ' 리소스 종류에 대 한 ' 읽기 ' 및 ' 쓰기 ' 권한입니다.

### <a name="for-azure-kubernetes-service-aks-clusters"></a>AKS (Azure Kubernetes Service) 클러스터의 경우

* AKS 클러스터가 실행 되 고 있습니다.

>[!IMPORTANT]
>`microsoft.flux`확장이 spn 기반 AKS 클러스터에서 작동 하지 않기 때문에 MSI (spn이 아님)를 사용 하 여 AKS 클러스터를 만들었는지 확인 합니다.

* ' ContainerService/managedClusters ' 리소스 종류에 대 한 ' 읽기 ' 및 ' 쓰기 ' 권한입니다.
* AKS-ExtensionManager 기능 플래그를 사용 하 여 구독을 등록 합니다.

```console
az feature register --namespace Microsoft.ContainerService --name AKS-ExtensionManager
```

### <a name="common-to-both-cluster-types"></a>두 클러스터 형식에 공통

* Azure CLI 버전 2.15 이상이 보장 됩니다. [Azure CLI를 설치](/cli/azure/install-azure-cli) 하거나 최신 버전으로 업데이트 하 Azure CLI 버전 2.15 +가 설치 되어 있는지 확인 합니다.

```console
az version
az upgrade
```

* 다음 Azure 서비스 공급자를 등록 합니다 (기존 공급자를 다시 등록 하는 것은 양호).

```console
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.KubernetesConfiguration
```

등록은 비동기 프로세스 이며 10 분 이내에 완료 되어야 합니다. 등록 프로세스를 모니터링할 수 있습니다.

```console
az provider show -n Microsoft.KubernetesConfiguration -o table

Namespace                          RegistrationPolicy    RegistrationState
---------------------------------  --------------------  -------------------
Microsoft.KubernetesConfiguration  RegistrationRequired  Registered
```

### <a name="supported-regions"></a>지원되는 지역

Azure GitOps는 현재 Azure Arc 사용 Kubernetes에서 지 원하는 지역에서 지원 됩니다. 이러한 지역은 AKS에서 지 원하는 지역의 하위 집합입니다. GitOps는 현재 모든 AKS 지역에서 지원 되지 않습니다.  [여기에서 지원 되는 지역을 참조](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=kubernetes-service,azure-arc)하세요. GitOps 서비스는 일반 흐름에서 지원 되는 새 지역을 추가 합니다.

### <a name="network-requirements"></a>네트워크 요구 사항

GitOps 에이전트를 작동 하려면 다음 프로토콜, 포트 및 아웃 바운드 Url이 있어야 합니다.

* 포트 443의 TCP --> `https://:443`

| 엔드포인트(DNS) | 설명 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com` | 에이전트가 Kubernetes 구성 서비스와 통신 하는 데 필요 합니다. |
| `https://<region>.dp.kubernetesconfiguration.azure.com` | 에이전트가 상태를 푸시하고 구성 정보를 가져오기 위한 데이터 평면 엔드포인트. 다음에 종속 `<region>` : 위에서 참조 하는 지원 되는 지역입니다. |
| `https://login.microsoftonline.com` | Azure Resource Manager 토큰을 가져오고 업데이트하는 데 필요합니다. |
| `https://mcr.microsoft.com` | Flux 컨트롤러의 컨테이너 이미지를 끌어오는 데 필요 합니다. |
| `https://azurearcfork8s.azurecr.io` | GitOps 에이전트의 컨테이너 이미지를 끌어오는 데 필요 합니다. |

## <a name="enable-cli-extensions"></a>CLI 확장 사용

>[!NOTE]
>`k8s-configuration`Flux v2 또는 flux v1 구성을 관리 하도록 CLI 확장이 업그레이드 되었습니다. Flux v2는 Flux v1으로 중요 한 업그레이드 이며, 결과적으로 Flux v1에 대 한 Azure GitOps 지원이 중단 됩니다. 따라서 가능한 한 빨리 Flux v2를 사용 하기 시작 합니다.

* 최신 `k8s-configuration` 및 `k8s-extension` CLI 확장 패키지를 설치 합니다.

```console
az extension add -n k8s-configuration
az extension add –n k8s-extension
```

* 이러한 패키지를 업데이트 하려면:

```console
az extension update -n k8s-configuration
az extension update -n k8s-extension
```

설치 된 az CLI extensions 목록과 해당 버전을 보려면 다음을 수행 합니다.

```console
az extension list -o table

Experimental   ExtensionType   Name                   Path                                                       Preview   Version
-------------  --------------  -----------------      -----------------------------------------------------      --------  --------
False          whl             connectedk8s           C:\Users\somename\.azure\cliextensions\connectedk8s           False     1.1.7
False          whl             k8s-configuration      C:\Users\somename\.azure\cliextensions\k8s-configuration      False     1.2.0
False          whl             k8s-extension          C:\Users\somename\.azure\cliextensions\k8s-extension          False     1.0.0
```

## <a name="apply-a-flux-configuration-using-azure-cli"></a>Azure CLI를 사용 하 여 Flux 구성 적용

`k8s-configuration`Azure CLI 확장 (또는 Azure Portal)을 사용 하 여 AKS 또는 Arc 사용 Kubernetes 클러스터에서 GitOps를 사용 하도록 설정 합니다. 데모를 위해 공용 리포지토리를 사용 [https://github.com/fluxcd/flux2-kustomize-helm-example](https://github.com/fluxcd/flux2-kustomize-helm-example) 합니다. 

아래 예제에서:

* 클러스터가 포함 된 리소스 그룹은 "flux-demo-rg"입니다.
* Arc 클러스터의 이름은 "flux-demo-Arc"입니다.
* 클러스터 유형이 Arc (connectedClusters) 이지만이 예는 AKS (managedClusters)와 함께 작동할 수도 있습니다.
* Flux 구성의 이름은 "gitops-demo"입니다.
* 구성 설치를 위한 네임 스페이스는 "gitops-demo"입니다.
* 공용 Git 리포지토리의 URL은 " https://github.com/fluxcd/flux2-kustomize-helm-example "입니다.
* Git 리포지토리 분기는 "main"입니다.
* 구성의 범위는 "cluster" 이며,이를 통해 운영자에 게 클러스터 전체를 변경할 수 있는 권한이 부여 됩니다.
* 두 개의 kustomizations은 각각 리포지토리의 다른 경로와 연결 된 "인프라" 및 "apps" 라는 이름으로 지정 됩니다.
* "Apps" kustomization는 "인프라" kustomization ("앱" kustomization 실행 전에 "인프라" kustomization가 완료 되어야 함)에 따라 달라 집니다.
* 두 kustomizations에서 모두 정리 = true를 설정 합니다. 이 설정은 Flux에 의해 클러스터에 배포 된 개체가 리포지토리에서 제거 되거나 Flux 구성 또는 kustomizations 삭제 된 경우 정리 됩니다.

`microsoft.flux`확장이 클러스터에 아직 설치 되지 않은 경우 설치 됩니다.

```console
az k8s-configuration flux create -g flux-demo-rg -c flux-demo-arc -n gitops-demo --namespace gitops-demo -t connectedClusters --scope cluster -u https://github.com/fluxcd/flux2-kustomize-helm-example --branch main  --kustomization name=infra path=./infrastructure prune=true --kustomization name=apps path=./apps/staging prune=true dependsOn=["infra"]

Command group 'k8s-configuration flux' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Warning! https url is being used without https auth params, ensure the repository url provided is not a private repo
'Microsoft.Flux' extension not found on the cluster, installing it now. This may take a few minutes...
'Microsoft.Flux' extension was successfully installed on the cluster
Creating the flux configuration 'gitops-demo' in the cluster. This may take a few minutes...
{
  "complianceState": "Pending",
  ... (not shown because of pending status)
}
```

Reconciliations 완료 후에 구성을 표시 합니다.

```console
az k8s-configuration flux show -g flux-demo-rg -c flux-demo-arc -n gitops-demo -t connectedClusters

Command group 'k8s-configuration flux' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
{
  "complianceState": "Compliant",
  "configurationProtectedSettings": {},
  "errorMessage": "",
  "gitRepository": {
    "httpsCaFile": null,
    "httpsUser": null,
    "localAuthRef": null,
    "repositoryRef": {
      "branch": "main",
      "commit": null,
      "semver": null,
      "tag": null
    },
    "sshKnownHosts": null,
    "syncIntervalInSeconds": 600,
    "timeoutInSeconds": 600,
    "url": "https://github.com/fluxcd/flux2-kustomize-helm-example"
  },
  "id": "/subscriptions/REDACTED/resourceGroups/flux-demo-rg/providers/Microsoft.Kubernetes/connectedClusters/flux-demo-arc/providers/Microsoft.KubernetesConfiguration/fluxConfigurations/gitops-demo",
  "kustomizations": {
    "apps": {
      "dependsOn": [
        {
          "kustomizationName": "infra"
        }
      ],
      "force": false,
      "path": "./apps/staging",
      "prune": true,
      "retryIntervalInSeconds": null,
      "syncIntervalInSeconds": 600,
      "timeoutInSeconds": 600
    },
    "infra": {
      "dependsOn": [],
      "force": false,
      "path": "./infrastructure",
      "prune": true,
      "retryIntervalInSeconds": null,
      "syncIntervalInSeconds": 600,
      "timeoutInSeconds": 600
    }
  },
  "lastSourceSyncedAt": "2021-11-23T22:59:22+00:00",
  "lastSourceSyncedCommitId": "main/f0c2aaef48461d8099a8fff05893e9ebb96f1561",
  "name": "gitops-demo",
  "namespace": "gitops-demo",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "resourceGroup": "flux-demo-rg",
  "scope": "cluster",
  "sourceKind": "GitRepository",
  "statuses": [
    {
      "appliedBy": null,
      "complianceState": "Compliant",
      "helmReleaseProperties": null,
      "kind": "GitRepository",
      "name": "gitops-demo",
      "namespace": "gitops-demo",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T22:59:22+00:00",
          "message": "Fetched revision: main/f0c2aaef48461d8099a8fff05893e9ebb96f1561",
          "reason": "GitOperationSucceed",
          "status": "True",
          "type": "Ready"
        }
      ]
    },
    {
      "appliedBy": null,
      "complianceState": "Compliant",
      "helmReleaseProperties": null,
      "kind": "Kustomization",
      "name": "gitops-demo-apps",
      "namespace": "gitops-demo",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T22:59:53+00:00",
          "message": "Applied revision: main/f0c2aaef48461d8099a8fff05893e9ebb96f1561",
          "reason": "ReconciliationSucceeded",
          "status": "True",
          "type": "Ready"
        }
      ]
    },
    {
      "appliedBy": {
        "name": "gitops-demo-apps",
        "namespace": "gitops-demo"
      },
      "complianceState": "Compliant",
      "helmReleaseProperties": {
        "failureCount": 0,
        "helmChartRef": {
          "name": "podinfo-podinfo",
          "namespace": "flux-system"
        },
        "installFailureCount": 0,
        "lastRevisionApplied": 1,
        "upgradeFailureCount": 0
      },
      "kind": "HelmRelease",
      "name": "podinfo",
      "namespace": "podinfo",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T22:59:54+00:00",
          "message": "Release reconciliation succeeded",
          "reason": "ReconciliationSucceeded",
          "status": "True",
          "type": "Ready"
        },
        {
          "lastTransitionTime": "2021-11-23T22:59:54+00:00",
          "message": "Helm install succeeded",
          "reason": "InstallSucceeded",
          "status": "True",
          "type": "Released"
        }
      ]
    },
    {
      "appliedBy": null,
      "complianceState": "Compliant",
      "helmReleaseProperties": null,
      "kind": "Kustomization",
      "name": "gitops-demo-infra",
      "namespace": "gitops-demo",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T22:59:24+00:00",
          "message": "Applied revision: main/f0c2aaef48461d8099a8fff05893e9ebb96f1561",
          "reason": "ReconciliationSucceeded",
          "status": "True",
          "type": "Ready"
        }
      ]
    },
    {
      "appliedBy": {
        "name": "gitops-demo-infra",
        "namespace": "gitops-demo"
      },
      "complianceState": "Compliant",
      "helmReleaseProperties": null,
      "kind": "HelmRepository",
      "name": "bitnami",
      "namespace": "flux-system",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T22:59:30+00:00",
          "message": "Fetched revision: 75dd8746b22e569460eb3b453b0ae22941c680b7",
          "reason": "IndexationSucceed",
          "status": "True",
          "type": "Ready"
        }
      ]
    },
    {
      "appliedBy": {
        "name": "gitops-demo-infra",
        "namespace": "gitops-demo"
      },
      "complianceState": "Compliant",
      "helmReleaseProperties": null,
      "kind": "HelmRepository",
      "name": "podinfo",
      "namespace": "flux-system",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T22:59:24+00:00",
          "message": "Fetched revision: fddc2924c28a1a1895e215a4dc065f33a0ea2e8e",
          "reason": "IndexationSucceed",
          "status": "True",
          "type": "Ready"
        }
      ]
    },
    {
      "appliedBy": {
        "name": "gitops-demo-infra",
        "namespace": "gitops-demo"
      },
      "complianceState": "Compliant",
      "helmReleaseProperties": {
        "failureCount": 0,
        "helmChartRef": {
          "name": "nginx-nginx",
          "namespace": "flux-system"
        },
        "installFailureCount": 0,
        "lastRevisionApplied": 1,
        "upgradeFailureCount": 0
      },
      "kind": "HelmRelease",
      "name": "nginx",
      "namespace": "nginx",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T23:00:10+00:00",
          "message": "Release reconciliation succeeded",
          "reason": "ReconciliationSucceeded",
          "status": "True",
          "type": "Ready"
        },
        {
          "lastTransitionTime": "2021-11-23T23:00:10+00:00",
          "message": "Helm install succeeded",
          "reason": "InstallSucceeded",
          "status": "True",
          "type": "Released"
        }
      ]
    },
    {
      "appliedBy": {
        "name": "gitops-demo-infra",
        "namespace": "gitops-demo"
      },
      "complianceState": "Compliant",
      "helmReleaseProperties": {
        "failureCount": 0,
        "helmChartRef": {
          "name": "redis-redis",
          "namespace": "flux-system"
        },
        "installFailureCount": 0,
        "lastRevisionApplied": 1,
        "upgradeFailureCount": 0
      },
      "kind": "HelmRelease",
      "name": "redis",
      "namespace": "redis",
      "statusConditions": [
        {
          "lastTransitionTime": "2021-11-23T22:59:56+00:00",
          "message": "Release reconciliation succeeded",
          "reason": "ReconciliationSucceeded",
          "status": "True",
          "type": "Ready"
        },
        {
          "lastTransitionTime": "2021-11-23T22:59:56+00:00",
          "message": "Helm install succeeded",
          "reason": "InstallSucceeded",
          "status": "True",
          "type": "Released"
        }
      ]
    }
  ],
  "suspend": false,
  "systemData": {
    "createdAt": "2021-11-23T22:58:53.736245+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2021-11-23T22:58:53.736245+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/fluxConfigurations"
}
```

만든 네임 스페이스는 다음과 같습니다.

* `flux-system`: Flux 확장 컨트롤러를 보유 합니다.
* `gitops-demo`: Flux 구성 개체를 보유 합니다.
* `nginx`, `podinfo` , `redis` : Git 리포지토리의 매니페스트에 설명 된 작업에 대 한 네임 스페이스

```console
kubectl get namespaces
NAME              STATUS   AGE
azure-arc         Active   17d
default           Active   17d
flux-system       Active   18m
gitops-demo       Active   17m
kube-node-lease   Active   17d
kube-public       Active   17d
kube-system       Active   17d
nginx             Active   17m
podinfo           Active   16m
redis             Active   17m```

The `flux-system` namespace contains the Flux extension objects.  

* Azure Flux controllers: `fluxconfig-agent`, `fluxconfig-controller`
* OSS Flux controllers: `source-controller`, `kustomize-controller`, `helm-controller`, `notification-controller`

The Flux agent and controller pods should be in running state.

```console
kubectl get pods -n flux-system

NAME                                      READY   STATUS    RESTARTS   AGE
fluxconfig-agent-9554ffb65-jqm8g          2/2     Running   0          21m
fluxconfig-controller-9d99c54c8-nztg8     2/2     Running   0          21m
helm-controller-59cc74dbc5-77772          1/1     Running   0          21m
kustomize-controller-5fb7d7b9d5-cjdhx     1/1     Running   0          21m
notification-controller-7d45678bc-fvlvr   1/1     Running   0          21m
source-controller-df7dc97cd-4drh2         1/1     Running   0          21m
```

네임 스페이스 `gitops-demo` 에는 Flux 구성 개체가 있습니다.

```console
kubectl get crds

NAME                                                   CREATED AT
alerts.notification.toolkit.fluxcd.io                  2021-11-23T22:57:49Z
arccertificates.clusterconfig.azure.com                2021-11-06T15:12:36Z
azureclusteridentityrequests.clusterconfig.azure.com   2021-11-06T15:12:36Z
connectedclusters.arc.azure.com                        2021-11-06T15:12:36Z
customlocationsettings.clusterconfig.azure.com         2021-11-06T15:12:36Z
extensionconfigs.clusterconfig.azure.com               2021-11-06T15:12:36Z
fluxconfigs.clusterconfig.azure.com                    2021-11-23T22:57:49Z
gitconfigs.clusterconfig.azure.com                     2021-11-06T15:12:36Z
gitrepositories.source.toolkit.fluxcd.io               2021-11-23T22:57:49Z
healthstates.azmon.container.insights                  2021-11-06T14:45:55Z
helmcharts.source.toolkit.fluxcd.io                    2021-11-23T22:57:49Z
helmreleases.helm.toolkit.fluxcd.io                    2021-11-23T22:57:49Z
helmrepositories.source.toolkit.fluxcd.io              2021-11-23T22:57:49Z
kustomizations.kustomize.toolkit.fluxcd.io             2021-11-23T22:57:49Z
providers.notification.toolkit.fluxcd.io               2021-11-23T22:57:49Z
receivers.notification.toolkit.fluxcd.io               2021-11-23T22:57:49Z
```

```console
kubectl get fluxconfigs -A

NAMESPACE     NAME          SCOPE     URL                                                      PROVISION   AGE
gitops-demo   gitops-demo   cluster   https://github.com/fluxcd/flux2-kustomize-helm-example   Succeeded   22m
```

```console
kubectl get gitrepositories -A

NAMESPACE     NAME          URL                                                      READY   STATUS                                                            AGE
gitops-demo   gitops-demo   https://github.com/fluxcd/flux2-kustomize-helm-example   True    Fetched revision: main/f0c2aaef48461d8099a8fff05893e9ebb96f1561   22m
```

```console
kubectl get helmreleases -A

NAMESPACE   NAME      READY   STATUS                             AGE
nginx       nginx     True    Release reconciliation succeeded   6d4h
podinfo     podinfo   True    Release reconciliation succeeded   6d4h
redis       redis     True    Release reconciliation succeeded   6d4h
```

```console
kubectl get kustomizations -A

NAMESPACE     NAME                READY   STATUS                                                            AGE
gitops-demo   gitops-demo-apps    True    Applied revision: main/f0c2aaef48461d8099a8fff05893e9ebb96f1561   23m
gitops-demo   gitops-demo-infra   True    Applied revision: main/f0c2aaef48461d8099a8fff05893e9ebb96f1561   23m
```

Git 리포지토리의 매니페스트에서 배포 된 작업입니다.

```console
kubectl get deploy -n nginx

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
nginx-ingress-controller                   1/1     1            1           25m
nginx-ingress-controller-default-backend   1/1     1            1           25m

kubectl get deploy -n podinfo

NAME      READY   UP-TO-DATE   AVAILABLE   AGE
podinfo   1/1     1            1           26m

kubectl get all -n redis

NAME                 READY   STATUS    RESTARTS   AGE
pod/redis-master-0   1/1     Running   0          95m

NAME                     TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
service/redis-headless   ClusterIP   None          <none>        6379/TCP   95m
service/redis-master     ClusterIP   10.0.180.63   <none>        6379/TCP   95m

NAME                            READY   AGE
statefulset.apps/redis-master   1/1     95m
```

### <a name="delete-the-flux-configuration"></a>Flux 구성 삭제

Flux 구성을 삭제할 수 있습니다. 이렇게 하면 Azure의 fluxConfigurations 리소스와 클러스터의 Flux 구성 개체가 모두 삭제 됩니다. Flux 구성은 원래 kustomization 매개 변수를 사용 하 여 만들어졌기 때문에 `prune=true` , Git 리포지토리의 매니페스트를 기반으로 클러스터에서 만든 모든 개체는 Flux 구성이 제거 될 때 제거 됩니다.

```console
az k8s-configuration flux delete -g flux-demo-rg -c flux-demo-arc -n gitops-demo -t connectedClusters --yes
```

이 작업을 수행 해도 Flux 확장은 제거 되지 않습니다.

### <a name="delete-the-flux-cluster-extension"></a>Flux 클러스터 확장 삭제

CLI 또는 포털을 사용 하 여 Flux 확장을 삭제할 수 있습니다. Delete 작업은 Azure의 microsoft flux 확장 리소스와 클러스터의 Flux 확장 개체를 모두 제거 합니다.

Flux 구성이 처음 만들어졌을 때 Flux 확장이 자동으로 만들어진 경우에는 확장 이름이로 설정 됩니다 `flux` .

Azure Arc 사용 가능 Kubernetes 클러스터의 경우:

```console
az k8s-extension delete -g flux-demo-rg -c flux-demo-arc -n flux -t connectedClusters --yes
```

AKS (Azure Kubernetes Service) 클러스터의 경우:

```console
az k8s-extension delete -g flux-demo-rg -c flux-demo-arc -n flux -t managedClusters --yes
```

### <a name="control-which-controllers-are-deployed-with-the-flux-cluster-extension"></a>Flux 클러스터 확장을 사용 하 여 배포 되는 컨트롤러 제어

Flux 클러스터 확장을 사용 하 여 설치 된 Flux 컨트롤러를 제어할 수 있습니다.  원본, kustomize, 투구 및 notification controller는 기본적으로 설치 됩니다. 이미지 자동화 및 이미지 리플렉터 컨트롤러를 명시적으로 사용 하도록 설정 해야 합니다.

* --config source-controller. enabled =<true/false> [기본값 true]
* --구성 투구-controller. enabled =<true/false> [기본값 true]
* --config kustomize-controller. enabled =<true/false> [기본값 true]
* --config notification-controller. enabled =<true/false> [기본값 true]
* --config image-automation-controller. enabled =<true/false> [기본값 false]
* --config 이미지-리플렉터-controller. enabled =<true/false> [기본값 false]

[Flux 이미지 리플렉터 및 자동화 컨트롤러](https://fluxcd.io/docs/components/image/)를 포함 하는 예제는 다음과 같습니다. Flux 구성이 처음 만들어졌을 때 Flux 확장이 자동으로 만들어진 경우에는 확장 이름이로 설정 됩니다 `flux` .

```console
az k8s-extension create -g <cluster_resource_group> -c <cluster_name> -t <connectedClusters or managedClusters> --name flux --extension-type microsoft.flux --config image-automation-controller.enabled=true image-reflector-controller.enabled=true
```

## <a name="parameters-reference"></a>매개 변수 참조

Flux에서 지 원하는 모든 매개 변수에 대 한 설명은 [공식 flux 설명서](https://fluxcd.io/docs/) 를 참조 하세요. Azure의 flux는 모든 매개 변수를 아직 지원 하지 않습니다. 필요한 매개 변수가 Azure 구현에 누락 된 경우 알려주세요.

`k8s-configuration flux`매개 변수를 사용 하 여 CLI 명령에서 지 원하는 매개 변수의 전체 목록을 볼 수 있습니다 `-h` .

```console
az k8s-configuration flux -h

Group
    az k8s-configuration flux : Commands to manage Flux v2 Kubernetes configurations.
        This command group is in preview and under development. Reference and support levels:
        https://aka.ms/CLI_refstatus
Subgroups:
    kustomization : Commands to manage Kustomizations associated with Flux v2 Kubernetes
                    configurations.

Commands:
    create        : Create a Kubernetes Flux v2 Configuration.
    delete        : Delete a Kubernetes Flux v2 Configuration.
    list          : List Kubernetes Flux v2 Configurations.
    show          : Show a Kubernetes Flux v2 Configuration.
    update        : Update a Kubernetes Flux v2 Configuration.
```

CLI 명령에 대 한 매개 변수는 다음과 같습니다 `k8s-configuration flux create` .

```console
az k8s-configuration flux create -h

This command is from the following extension: k8s-configuration

Command
    az k8s-configuration flux create : Create a Kubernetes Flux v2 Configuration.
        Command group 'k8s-configuration flux' is in preview and under development. Reference
        and support levels: https://aka.ms/CLI_refstatus
Arguments
    --cluster-name -c   [Required] : Name of the Kubernetes cluster.
    --cluster-type -t   [Required] : Specify Arc connectedClusters or AKS managedClusters.
                                     Allowed values: connectedClusters, managedClusters.
    --name -n           [Required] : Name of the flux configuration.
    --resource-group -g [Required] : Name of resource group. You can configure the default group
                                     using `az configure --defaults group=<name>`.
    --interval --sync-interval     : Time between reconciliations of the source on the cluster.
    --kind                         : Source kind to reconcile.  Allowed values: git.  Default: git.
    --kustomization -k             : Define kustomizations to sync sources with parameters ['name',
                                     'path', 'depends_on', 'timeout', 'sync_interval',
                                     'retry_interval', 'prune', 'force'].
    --namespace --ns               : Namespace to deploy the configuration.  Default: default.
    --no-wait                      : Do not wait for the long-running operation to finish.
    --scope -s                     : Specify scope of the operator to be 'namespace' or 'cluster'.
                                     Allowed values: cluster, namespace.  Default: cluster.
    --suspend                      : Suspend the reconciliation of the source and kustomizations
                                     associated with this configuration.  Allowed values: false,
                                     true.
    --timeout                      : Maximum time to reconcile the source before timing out.
    --url -u                       : URL of the git repo source to reconcile.

Auth Arguments
    --https-ca-cert                : Base64-encoded HTTPS CA certificate for TLS communication with
                                     private repository sync.
    --https-ca-cert-file           : File path to HTTPS CA certificate file for TLS communication
                                     with private repository sync.
    --https-key                    : HTTPS token/password for private repository sync.
    --https-user                   : HTTPS username for private repository sync.
    --known-hosts                  : Base64-encoded known_hosts data containing public SSH keys
                                     required to access private Git instances.
    --known-hosts-file             : File path to known_hosts contents containing public SSH keys
                                     required to access private Git instances.
    --local-auth-ref --local-ref   : Local reference to a kubernetes secret in the configuration
                                     namespace to use for communication to the source.
    --ssh-private-key              : Base64-encoded private ssh key for private repository sync.
    --ssh-private-key-file         : File path to private ssh key for private repository sync.

Git Repo Ref Arguments
    --branch                       : Branch within the git source to reconcile with the cluster.
    --commit                       : Commit within the git source to reconcile with the cluster.
    --semver                       : Semver range within the git source to reconcile with the
                                     cluster.
    --tag                          : Tag within the git source to reconcile with the cluster.

Global Arguments
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --only-show-errors             : Only show errors, suppressing warnings.
    --output -o                    : Output format.  Allowed values: json, jsonc, none, table, tsv,
                                     yaml, yamlc.  Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more
                                     information and examples.
    --subscription                 : Name or ID of subscription. You can configure the default
                                     subscription using `az account set -s NAME_OR_ID`.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.

Examples
    Create a Kubernetes v2 Flux Configuration
        az k8s-configuration flux create --resource-group my-resource-group \
        --cluster-name mycluster --cluster-type connectedClusters \
        --name myconfig --scope cluster --namespace my-namespace \
        --kind git --url https://github.com/Azure/arc-k8s-demo \
        --branch main --kustomization name=my-kustomization```
```

### <a name="configuration-general-arguments"></a>구성 일반 인수

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--cluster-name` `-c` | String | Azure에서 클러스터 리소스의 이름입니다. |
| `--cluster-type` `-t` | *connectedClusters*, *managedclusters* | Azure Arc 지원 Kubernetes 클러스터에 *connectedClusters* 를 사용 하 고 AKS (Azure Kubernetes Service) 클러스터에 대해 *managedclusters* 를 사용 합니다. |
| `--resource-group` `-g` | String | Arc 또는 AKS 클러스터 리소스를 보유 하는 Azure 리소스 그룹의 이름입니다. |
| `--name` `-n`| String | Azure에서 Flux 구성의 이름입니다. |
| `--namespace` `--ns` | String | 구성을 배포할 네임 스페이스의 이름입니다.  기본값: default. |
| `--scope` `-s` | String | 운영자의 사용 권한 범위입니다. 가능한 값은 ' cluster ' (모든 권한) 또는 ' namespace ' (제한 된 액세스)입니다. 기본값: cluster
| `--suspend` | flag | 이 Flux 구성에 정의 된 모든 원본 및 kustomize reconciliations을 일시 중단 합니다. 일시 중단 시 활성 Reconciliations 계속 됩니다.  |

### <a name="git-repository-arguments"></a>Git 리포지토리 인수

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--kind` | String | 조정할 소스 종류입니다. 기본값: *git*. 현재 *git* 만 지원 됩니다.  |
| `--timeout` | [golang 기간 형식](https://pkg.go.dev/time#Duration.String) | 시간 제한이 초과 되기 전까지 소스를 조정 하는 최대 시간입니다. 기본값: 10m |
| `--sync-interval` `--interval` | [golang 기간 형식](https://pkg.go.dev/time#Duration.String) | 클러스터에서 Git 원본의 reconciliations 빈도입니다. 기본값: 10m |

### <a name="git-repository-reference-arguments"></a>Git 리포지토리 참조 인수

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--branch` | String | 클러스터에 동기화 할 git 원본 내 분기입니다. 기본값: *master* 최신 리포지토리에는 *main* 이라는 루트 분기가 있을 수 있습니다 .이 경우에는 *--branch = main* 을 설정 해야 합니다.  |
| `--tag` | String | 클러스터에 동기화 할 git 소스 내의 태그입니다. 예: *--tag = 3.2.0* |
| `--semver` | String | 클러스터와 동기화 하기 위한 git 소스 내의 git 태그 semver 범위입니다. 예: *--semver = ">= 3.1.0 <3.2.0"* |
| `--commit` | String | 클러스터에 동기화 할 git 원본 내의 Git commit SHA입니다. 예: *--commit = 363a6a8fe6a7f13e05d34c163b0ef02a777da20a* |

자세한 내용은 [Git 리포지토리 체크 아웃 전략에 대 한 Flux 문서](https://fluxcd.io/docs/components/source/gitrepositories/#checkout-strategies)를 참조 하세요.

### <a name="use-a-public-git-repository"></a>공용 Git 리포지토리 사용

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--url` `-u` | http [s]:/server/ds [. git] | 클러스터와 조정할 git 리포지토리 원본의 URL입니다. |

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>SSH 및 Flux에서 만든 키를 사용하여 프라이빗 Git 리포지토리 사용

Flux에 의해 생성된 공개 키를 Git 서비스 공급자의 사용자 계정에 추가합니다.

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--url` `-u` | ssh://user@server/repo [.git] | `git@``user@`공개 키가 사용자 계정 대신 리포지토리에 연결 된 경우 대체 해야 함 |

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>SSH 및 사용자 제공 키를 사용하여 프라이빗 Git 리포지토리 사용

직접 또는 파일에서 고유한 개인 키를 사용 합니다. 키는 [PEM 형식](https://aka.ms/PEMformat)이어야 하며 줄 바꿈(\n)으로 끝나야 합니다.

Git 서비스 공급자의 사용자 계정에 연결된 공개 키를 추가합니다.

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--url` `-u` | ssh://user@server/repo [.git] | `git@``user@`공개 키가 사용자 계정 대신 리포지토리에 연결 된 경우 대체 해야 함 |
| `--ssh-private-key` | [PEM 형식의](https://aka.ms/PEMformat) Base64 키 | 키 직접 제공 |
| `--ssh-private-key-file` | 로컬 파일의 전체 경로 | PEM 형식 키를 포함하는 로컬 파일의 전체 경로를 제공합니다.

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>SSH 및 사용자가 제공한 알려진 호스트와 함께 프라이빗 Git 호스트 사용

Flux 연산자는 알려진 호스트 파일의 일반 Git 호스트 목록을 유지 관리 합니다. Flux는 SSH 연결을 설정 하기 전에이 정보를 사용 하 여 Git 리포지토리를 인증 합니다. *드물게* 사용 되는 git 리포지토리 또는 자체 git 호스트를 사용 하는 경우 flux가 리포지토리를 식별할 수 있도록 호스트 키를 제공할 수 있습니다.

프라이빗 키와 마찬가지로 known_hosts 콘텐츠를 직접 또는 파일에 제공할 수 있습니다. 사용자 자신의 콘텐츠를 제공하는 경우 위의 SSH 키 시나리오 중 하나와 함께 [known_hosts 콘텐츠 형식 사양](https://aka.ms/KnownHostsFormat)을 사용합니다.

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--url` `-u` | ssh://user@server/repo [.git] | `git@`가 `user@`를 대체할 수 있습니다. |
| `--known-hosts` | Base64 문자열 | 알려진 호스트 콘텐츠 직접 제공 |
| `--known-hosts-file` | 로컬 파일의 전체 경로 | 로컬 파일에 알려진 호스트 콘텐츠 제공 |

### <a name="use-a-private-git-repository-with-https-user-and-key"></a>HTTPS 사용자 및 키를 사용 하 여 개인 Git 리포지토리 사용

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--url` `-u` | https://server/repo [.git] | 기본 인증을 사용하는 HTTPS |
| `--https-user` | 원시 문자열 | HTTPS 사용자 이름 |
| `--https-key` | 원시 문자열 | HTTPS 개인용 액세스 토큰 또는 암호

### <a name="use-a-private-git-repository-with-https-ca-certificate"></a>HTTPS CA 인증서와 함께 프라이빗 Git 리포지토리 사용

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--url` `-u` | https://server/repo [.git] | 기본 인증을 사용하는 HTTPS |
| `--https-ca-cert` | Base64 문자열 | TLS 통신용 CA 인증서 |
| `--https-ca-cert-file` | 로컬 파일의 전체 경로 | 로컬 파일에 CA 인증서 콘텐츠 제공 |

### <a name="using-local-secret-for-authentication"></a>인증에 로컬 암호 사용

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--local-auth-ref`  | String | Git 원본과의 통신에 사용할 Flux 구성 네임스페이스의 Kubernetes 비밀에 대한 로컬 참조입니다. |

HTTPS 인증의 경우 사용자 이름 및 암호/키를 사용하여 Flux 구성이 있는 동일한 네임스페이스에 비밀을 만듭니다.

```console
kubectl create ns flux-config
kubectl create secret generic -n flux-config my-custom-secret --from-literal=username=<my-username> --from-literal=password=<my-password-or-key>
```

SSH 인증의 경우 ID 및 known_hosts 필드를 모두 사용하여 Flux 구성이 있는 동일한 네임스페이스에 비밀을 만듭니다.

```console
kubectl create ns flux-config
kubectl create secret generic -n flux-config my-custom-secret --from-file=identity=./id_rsa --from-file=known_hosts=./known_hosts
```

두 경우 모두 Flux 구성을 만들 때 다른 인증 매개 변수 대신 *--local-auth-ref my-custom-secret을* 사용합니다.

```console
az k8s-configuration flux create -g <cluster_resource_group> -c <cluster_name> -n <config_name> -t connectedClusters --scope cluster --namespace flux-config -u <git-repo-url> --kustomization name=kustomization1 --local-auth-ref my-custom-secret
```

>[!NOTE]
>프록시를 통해 Git 리포지토리에 액세스하는 데 Flux가 필요한 경우 Azure Arc 에이전트를 프록시 설정으로 업데이트해야 합니다. 자세한 내용은 [아웃바운드 프록시 서버를 사용하여 연결](./quickstart-connect-cluster.md?tabs=azure-cli#4a-connect-using-an-outbound-proxy-server)을 참조하세요.

### <a name="git-implementation"></a>Git 구현

Git을 구현하는 다양한 리포지토리 공급자를 지원하기 위해 Flux는 go-git 또는 libgit2의 두 Git 라이브러리 중 하나를 사용하도록 구성할 수 있습니다. [자세한 내용은 Flux 문서를](https://fluxcd.io/docs/components/source/gitrepositories/#git-implementation)참조하세요.

Flux v2의 Azure GitOps 구현은 퍼블릭 클라우드 리포지토리에 사용할 라이브러리를 자동으로 결정합니다.

* GitHub, GitLab 및 BitBucket 리포지토리의 경우 go-git이 사용됩니다.
* Azure DevOps 및 다른 모든 리포지토리의 경우 libgit2가 사용됩니다.

온-프레미스 리포지토리의 경우 libgit2가 사용됩니다.

### <a name="kustomization"></a>Kustomization

를 사용하면 `az k8s-configuration flux create` 구성을 만드는 동안 하나 이상의 kustomization을 만들 수 있습니다.

| 매개 변수 | 형식 | 메모 |
| ------------- | ------------- | ------------- |
| `--kustomization` | 값 없음 | kustomization을 구성하는 매개 변수 문자열의 시작을 나타냅니다. 이를 여러 번 사용하여 여러 kustomization을 만들 수 있습니다. |
| `name` | String | 이 kustomization의 고유한 이름입니다. |
| `path` | String | 클러스터와 조정할 Git 리포지토리 내의 경로입니다. 기본값은 분기의 최상위 수준입니다. |
| `prune` | Boolean | 기본값은 *false* 입니다. flux에 의해 클러스터에 배포된 개체가 리포지토리에서 제거되거나 Flux 구성 또는 kustomization이 삭제된 경우 정리하도록 *하려면 cleanne=true를* 설정합니다. 사용자가 클러스터에 액세스할 수 없고 Git 리포지토리를 통해서만 변경할 수 있는 환경에는 *prune=true를* 사용하는 것이 중요합니다. |
| `depends_on` | String | 이 kustomization이 조정되기 전에 조정해야 하는 하나 이상의 kustomization(이 구성 내)의 이름입니다. 예를 들어 *depends_on=["kustomization1","kustomization2"]* 참고: 종속 kustomization이 있는 kustomization을 제거하면 종속 kustomization은 "DependencyNotReady" 상태를 얻게 되며 조정이 중지됩니다.|
| `timeout` | [golang 기간 형식](https://pkg.go.dev/time#Duration.String) | 기본값: 10m  |
| `sync_interval` | [golang 기간 형식](https://pkg.go.dev/time#Duration.String) | 기본값: 10m  |
| `retry_interval` | [golang 기간 형식](https://pkg.go.dev/time#Duration.String) | 기본값: 10m  |
| `validation` | String | 값: *none*, *client*, *server*. 기본값: *없음.*  자세한 내용은 [Flux 설명서를](https://fluxcd.io/docs/) 참조하세요.|
| `force` | Boolean | 기본값은 *false* 입니다. 변경할 수 없는 필드 변경으로 인해 패치가 실패할 때 리소스를 다시 만들도록 kustomize 컨트롤러에 지시하려면 *force=true를* 설정합니다. |

를 사용하여 `az k8s-configuration flux kustomization` Flux 구성에서 kustomization을 만들고, 업데이트하고, 나열하고, 표시하고, 삭제할 수도 있습니다.

```console
az k8s-configuration flux kustomization -h

Group
    az k8s-configuration flux kustomization : Commands to manage Kustomizations associated with Flux
    v2 Kubernetes configurations.
        Command group 'k8s-configuration flux' is in preview and under development. Reference
        and support levels: https://aka.ms/CLI_refstatus
Commands:
    create : Create a Kustomization associated with a Kubernetes Flux v2 Configuration.
    delete : Delete a Kustomization associated with a Kubernetes Flux v2 Configuration.
    list   : List Kustomizations associated with a Kubernetes Flux v2 Configuration.
    show   : Show a Kustomization associated with a Flux v2 Configuration.
    update : Update a Kustomization associated with a Kubernetes Flux v2 Configuration.
```

다음은 kustomization 만들기 옵션입니다.

```console
az k8s-configuration flux kustomization create -h

This command is from the following extension: k8s-configuration

Command
    az k8s-configuration flux kustomization create : Create a Kustomization associated with a
    Kubernetes Flux v2 Configuration.
        Command group 'k8s-configuration flux kustomization' is in preview and under
        development. Reference and support levels: https://aka.ms/CLI_refstatus
Arguments
    --cluster-name -c          [Required] : Name of the Kubernetes cluster.
    --cluster-type -t          [Required] : Specify Arc connected clusters or AKS managed clusters.
                                            Allowed values: connectedClusters, managedClusters.
    --kustomization-name -k    [Required] : Specify the name of the kustomization to target.
    --name -n                  [Required] : Name of the flux configuration.
    --resource-group -g        [Required] : Name of resource group. You can configure the default
                                            group using `az configure --defaults group=<name>`.
    --dependencies --depends --depends-on : Comma-separated list of kustomization dependencies.
    --force                               : Re-create resources that cannot be updated on the
                                            cluster (i.e. jobs).  Allowed values: false, true.
    --interval --sync-interval            : Time between reconciliations of the kustomization on the
                                            cluster.
    --no-wait                             : Do not wait for the long-running operation to finish.
    --path                                : Specify the path in the source that the kustomization
                                            should apply.
    --prune                               : Garbage collect resources deployed by the kustomization
                                            on the cluster.  Allowed values: false, true.
    --retry-interval                      : Time between reconciliations of the kustomization on the
                                            cluster on failures, defaults to --sync-interval.
    --timeout                             : Maximum time to reconcile the kustomization before
                                            timing out.

Global Arguments
    --debug                               : Increase logging verbosity to show all debug logs.
    --help -h                             : Show this help message and exit.
    --only-show-errors                    : Only show errors, suppressing warnings.
    --output -o                           : Output format.  Allowed values: json, jsonc, none,
                                            table, tsv, yaml, yamlc.  Default: json.
    --query                               : JMESPath query string. See http://jmespath.org/ for more
                                            information and examples.
    --subscription                        : Name or ID of subscription. You can configure the
                                            default subscription using `az account set -s
                                            NAME_OR_ID`.
    --verbose                             : Increase logging verbosity. Use --debug for full debug
                                            logs.

Examples
    Create a Kustomization associated wiht a Kubernetes v2 Flux Configuration
        az k8s-configuration flux kustomization create --resource-group my-resource-group \
        --cluster-name mycluster --cluster-type connectedClusters --name myconfig \
        --kustomization-name my-kustomization-2 --path ./my/path --prune --force
```

## <a name="manage-gitops-configurations-using-the-azure-portal"></a>Azure Portal 사용하여 GitOps 구성 관리

Azure Portal Azure Arc 지원 Kubernetes 또는 AKS 클러스터에서 GitOps 구성 및 Flux 확장을 관리하는 데 유용합니다. 포털에는 각 클러스터와 연결된 모든 Flux 구성이 표시되며 각 클러스터로 드릴인할 수 있습니다. 클러스터의 전반적인 준수 상태가 제공됩니다. 또한 클러스터에 배포된 Flux 개체는 설치 매개 변수, 준수 상태 및 오류와 함께 표시됩니다.

포털을 사용하여 GitOps 구성을 만들고 삭제할 수도 있습니다.

## <a name="using-kustomize"></a>Kustomize 사용

Flux Kustomize 컨트롤러는 클러스터 확장의 일부로 `microsoft.flux` 설치됩니다. Git 리포지토리에서 동기화된 Kubernetes 매니페스트를 사용하여 클러스터 구성 및 애플리케이션 배포를 선언적으로 관리하도록 허용합니다. 이러한 Kubernetes 매니페스트에는 kustomize.yaml 파일이 포함될 수 있지만 필수는 아닙니다.

사용량에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Flux Kustomize 컨트롤러](https://fluxcd.io/docs/components/kustomize/)
* [Kustomize 참조 문서](https://kubectl.docs.kubernetes.io/references/kustomize/)
* [kustomization 파일](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/)
* [Kustomize 프로젝트](https://kubernetes-sigs.github.io/kustomize/)
* [Kustomize 가이드](https://kubectl.docs.kubernetes.io/guides/config_management/)

## <a name="using-helm"></a>Helm 사용

Flux Helm 컨트롤러는 클러스터 확장의 일부로 `microsoft.flux` 설치됩니다. Git 리포지토리에서 유지 관리하는 Kubernetes 매니페스트를 사용하여 Helm 차트 릴리스를 선언적으로 관리할 수 있습니다.

사용량에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Helm 사용자에 대한 Flux](https://fluxcd.io/docs/use-cases/helm/)
* [Helm 릴리스 관리](https://fluxcd.io/docs/guides/helmreleases/)
* [Flux v1 Helm에서 Flux v2 Helm으로 마이그레이션](https://fluxcd.io/docs/migration/helm-operator-migration/)
* [Flux Helm 컨트롤러](https://fluxcd.io/docs/components/helm/)

## <a name="migrating-from-flux-v1"></a>Flux v1에서 마이그레이션

Azure Arc 지원 Kubernetes 또는 Azure Kubernetes Service 클러스터에서 Flux v1을 사용하고 동일한 클러스터에서 Flux v2를 사용하도록 마이그레이션하려는 경우 먼저 클러스터에서 Flux v1을 삭제해야 `sourceControlConfigurations` 합니다.  `microsoft.flux`클러스터에 `sourceControlConfigurations` 리소스가 설치되어 있으면 클러스터 확장이 설치되지 않습니다.

다음 az CLI 명령을 사용하여 클러스터에서 기존 항목을 찾아서 삭제합니다. `sourceControlConfigurations`

```console
az k8s-configuration list --cluster-name <Arc or AKS cluster name> --cluster-type <connectedClusters OR managedClusters> --resource-group <resource group name>
az k8s-configuration delete --name <configuration name> --cluster-name <Arc or AKS cluster name> --cluster-type <connectedClusters OR managedClusters> --resource-group <resource group name>
```

Azure Portal 사용하여 Azure Arc 지원 Kubernetes 또는 AKS 클러스터에서 GitOps 구성을 보고 삭제할 수도 있습니다.

Flux v1에서 Flux v2로의 마이그레이션에 대한 일반 정보는 fluxcd 프로젝트 Flux [v1에서 v2로 마이그레이션에서](https://fluxcd.io/docs/migration/)확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

GitOps를 통해 CI/CD를 구현하는 방법을 알아보려면 다음 자습서로 이동합니다.
> [!div class="nextstepaction"]
> [GitOps를 통한 CI/CD 구현](./tutorial-gitops-flux2-ci-cd.md)
