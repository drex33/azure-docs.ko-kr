---
title: 필수 구성 요소 | 직접 연결 모드
description: 직접 연결 모드에서 데이터 컨트롤러를 배포하기 위한 필수 구성 요소.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: f519cbe865fe043d5685bd10d125434f0a1dc8a4
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954596"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>직접 연결 모드에서 데이터 컨트롤러를 배포하기 위한 필수 구성 요소.

이 문서에서는 직접 연결 모드에서 Azure Arc 지원 데이터 서비스에 대한 데이터 컨트롤러 배포를 준비하는 방법을 설명합니다. Azure Arc 데이터 컨트롤러를 배포하기 전에 [Azure Arc 지원 데이터 서비스 배포 계획](plan-azure-arc-data-services.md)에 설명된 개념을 이해하세요.

높은 수준에서 **직접** 연결 모드로 Azure Arc 데이터 컨트롤러를 만들기 위한 필수 조건은 다음과 같습니다.

1. Kubernetes 클러스터에 액세스할 수 있습니다. Kubernetes 클러스터가 없는 경우 AKS(Azure Kubernetes Service)에서 테스트/데모 클러스터를 만들 수 있습니다. [Azure Arc 지원 데이터 서비스에 대한 AKS 클러스터 만들기](create-aks-cluster-data-controller.md)의 지침을 따릅니다.
1. Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결

Kubernetes 클러스터를 Azure에 연결하려면 다음 확장 또는 Helm과 함께 Azure CLI `az`를 사용합니다.

### <a name="install-tools"></a>도구 설치

- Helm 버전 3.3 이상([설치](https://helm.sh/docs/intro/install/))
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 설치 또는 업그레이드

### <a name="add-extensions-for-azure-cli"></a>Azure CLI에 대한 확장 추가

다음 az 확장의 최신 버전을 설치합니다.
- `k8s-extension`
- `connectedk8s`
- `k8s-configuration`
- `customlocation`

다음 명령을 실행하여 az CLI 확장을 설치합니다.

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

`k8s-extension`, `connectedk8s`, `k8s-configuration`, `customlocation` 확장을 이미 설치한 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```

### <a name="connect-your-cluster-to-azure"></a>Azure에 클러스터 연결

Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결

   Kubernetes 클러스터를 Azure에 연결하려면 Azure CLI `az` 또는 PowerShell을 사용합니다.

   다음 명령을 실행합니다.

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   az connectedk8s connect --name <cluster_name> --resource-group <resource_group_name>
   ```

   ```output
   <pre>
   Helm release deployment succeeded

       {
         "aadProfile": {
           "clientAppId": "",
           "serverAppId": "",
           "tenantId": ""
         },
         "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
         "agentVersion": null,
         "connectivityStatus": "Connecting",
         "distribution": "gke",
         "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
         "identity": {
           "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
           "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
           "type": "SystemAssigned"
         },
         "infrastructure": "gcp",
         "kubernetesVersion": null,
         "lastConnectivityTime": null,
         "location": "eastus",
         "managedIdentityCertificateExpirationTime": null,
         "name": "AzureArcTest1",
         "offering": null,
         "provisioningState": "Succeeded",
         "resourceGroup": "AzureArcTest",
         "tags": {},
         "totalCoreCount": null,
         "totalNodeCount": null,
         "type": "Microsoft.Kubernetes/connectedClusters"
       }
   </pre>
   ```

   > [!TIP]
   > 위치 매개 변수가 지정되지 않은 위의 명령은 Azure Arc 사용 Kubernetes 리소스를 리소스 그룹과 동일한 위치에 만듭니다. Azure Arc 사용 Kubernetes 리소스를 다른 위치에 만들려면 `az connectedk8s connect` 명령을 실행할 때 `--location <region>` 또는 `-l <region>`을 지정합니다.

   > [!NOTE]
   > 서비스 주체를 사용하여 Azure CLI에 로그인한 경우 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정하려면 [추가 매개 변수](../kubernetes/troubleshooting.md#enable-custom-locations-using-service-principal)를 설정해야 합니다.

   # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

   ```azurepowershell
   New-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest -Location eastus
   ```

   ```output
   <pre>
   Location Name          Type
   -------- ----          ----
   eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
   </pre>
   ```

   ---


이 작업에 대한 자세한 연습은 [기존 Kubernetes 클러스터를 Azure arc에 연결](../kubernetes/quickstart-connect-cluster.md)에서 사용할 수 있습니다.

### <a name="verify-azure-arc-namespace-pods-are-created"></a>`azure-arc` 네임스페이스 Pod를 만들었는지 확인합니다.

   다음 단계를 진행하기 전에 모든 `azure-arc-` 네임스페이스 Pod가 만들어졌는지 확인합니다. 다음 명령을 실행합니다.

   ```console
   kubectl get pods -n azure-arc
   ```

   :::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/verify-azure-arc-pods.png" alt-text="모든 컨테이너는 실행 중 상태를 반환합니다.":::

   모든 컨테이너가 실행 중 상태를 반환하는 경우 클러스터를 Azure에 연결할 수 있습니다. 

## <a name="optionally-keep-the-log-analytics-workspace-id-and-shared-access-key-ready"></a>필요에 따라 Log Analytics 작업 영역 ID 및 공유 액세스 키를 준비 상태로 유지

Azure Arc 지원 데이터 컨트롤러를 배포하는 경우 설치하는 동안 메트릭 및 로그의 자동 업로드를 사용하도록 설정할 수 있습니다. 메트릭 업로드는 시스템 할당 관리 ID를 사용합니다. 그러나 로그를 업로드하려면 작업 영역 ID와 작업 영역에 대한 액세스 키가 필요합니다. 

데이터 컨트롤러를 배포한 후 메트릭 및 로그의 자동 업로드를 사용하거나 사용하지 않도록 설정할 수도 있습니다. 

지침은 [로그 분석 작업 영역 만들기](upload-logs.md#create-a-log-analytics-workspace)를 참조하세요.

## <a name="create-azure-arc-data-services"></a>Azure Arc 데이터 서비스 만들기

이러한 필수 구성 요소를 완료한 후 [Azure Arc 데이터 컨트롤러 | 직접 연결 모드를 배포](create-data-controller-direct-azure-portal.md)할 수 있습니다.
