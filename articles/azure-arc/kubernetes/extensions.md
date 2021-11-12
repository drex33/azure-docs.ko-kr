---
title: Azure Arc 지원 Kubernetes 클러스터 확장
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 지원 Kubernetes에서 확장의 수명 주기 배포 및 관리
ms.openlocfilehash: 811bced5b0855ffdc44d851459b69a7b6aad6b19
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352713"
---
# <a name="deploy-and-manage-azure-arc-enabled-kubernetes-cluster-extensions"></a>Azure Arc 지원 Kubernetes 클러스터 확장 배포 및 관리

Kubernetes 확장 기능을 사용하면 Azure Arc 지원 Kubernetes 클러스터에서 다음을 사용할 수 있습니다.

* 클러스터 확장의 Azure Resource Manager 기반 배포
* 확장 Helm 차트의 수명 주기 관리

이 문서에서는 다음에 대해 알아봅니다.
> [!div class="checklist"]
> * 현재 사용 가능한 Azure Arc 사용 가능한 Kubernetes 클러스터 확장.
> * 확장 인스턴스를 만드는 방법.
> * 필수 및 선택적 매개 변수.
> * 확장 인스턴스를 보고, 나열하고, 업데이트하고, 삭제하는 방법. 

이 기능에 대한 개념적 개요는 [클러스터 확장 - Azure Arc 지원 Kubernetes](conceptual-extensions.md) 문서에서 확인할 수 있습니다.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>필수 구성 요소

- 버전 >= 2.16.0으로 [Azure CLI를 설치하거나 업그레이드](/cli/azure/install-azure-cli)합니다.
- `connectedk8s`(버전 >= 1.1.0) 및 `k8s-extension`(버전 >= 0.2.0) Azure CLI 확장. 다음 명령을 실행하여 이러한 Azure CLI 확장을 설치합니다.
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    `connectedk8s` 및 `k8s-extension` 확장이 이미 설치되어 있는 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- 기존 Azure Arc 지원 Kubernetes 연결 클러스터.
    - 아직 클러스터를 연결하지 않은 경우 [빠른 시작](quickstart-connect-cluster.md)을 사용하세요.
    - [에이전트](agent-upgrade.md#manually-upgrade-agents)를 버전 >= 1.1.0으로 업그레이드합니다.

## <a name="currently-available-extensions"></a>현재 사용할 수 있는 확장

| 내선 번호 | 설명 |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Kubernetes 클러스터에 배포된 워크로드의 성능에 대한 가시성을 제공합니다. 컨트롤러, 노드, 컨테이너에서 메모리 및 CPU 사용률 메트릭을 수집합니다. |
| [Microsoft Defender for Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Kubernetes 클러스터에서 감사 로그 데이터와 같은 보안 관련 정보를 수집합니다. 수집된 데이터를 기반으로 권장 사항 및 위협 경고를 제공합니다. |
| [Azure Arc 사용 가능한 Open Service Mesh](tutorial-arc-enabled-open-service-mesh.md) | 클러스터에 Open Service Mesh를 배포하고 mTLS 보안, 세분화된 액세스 제어, 트래픽 이동, Azure Monitor 또는 Prometheus 및 Grafana의 오픈 소스 추가 기능을 사용한 모니터링, Jaeger를 사용한 추적, 외부 인증 관리 솔루션과의 통합과 같은 기능을 사용할 수 있습니다. |
| [Azure Arc 사용 Data Services](../../azure-arc/kubernetes/custom-locations.md#create-custom-location) | Kubernetes 및 선택한 인프라를 사용하여 온-프레미스, 에지 및 퍼블릭 클라우드에서 Azure 데이터 서비스를 실행할 수 있습니다. |
| [Azure Arc의 Azure App Service](../../app-service/overview-arc-integration.md) | Azure Arc 지원 Kubernetes 클러스터를 기반으로 App Service Kubernetes 환경을 프로비전할 수 있습니다. |
| [Kubernetes의 Event Grid](../../event-grid/kubernetes/overview.md) | Azure Arc 지원 Kubernetes 클러스터를 기반으로 토픽 및 이벤트 구독과 같은 Event Grid 리소스를 만들고 관리합니다. |
| [Azure Arc의 Azure API Management](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md) | Azure Arc 지원 Kubernetes 클러스터에서 API Management 게이트웨이를 배포하고 관리합니다. |
| [Azure Arc 지원 Machine Learning](../../machine-learning/how-to-attach-arc-kubernetes.md) | Azure Arc 사용 Kubernetes 클러스터에서 Azure Machine Learning를 배포하고 실행합니다. |

## <a name="usage-of-cluster-extensions"></a>클러스터 확장 사용

### <a name="create-extensions-instance"></a>확장 인스턴스 만들기

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension create`를 사용하여 새 확장 인스턴스를 만듭니다. 아래 명령은 Azure Arc 지원 Kubernetes 클러스터에 컨테이너 확장 인스턴스에 대한 Azure Monitor 만듭니다.

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**출력:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * 서비스는 48시간 넘게 중요한 정보를 유지할 수 없습니다. Azure Arc 지원 Kubernetes 에이전트가 48시간 넘게 네트워크에 연결되어 있지 않고 클러스터에서 확장을 만들지 여부를 결정할 수 없는 경우 확장이 `Failed` 상태로 전환됩니다. `Failed` 상태에서 `k8s-extension create`를 다시 실행하여 새 확장 Azure 리소스를 만들어야 합니다.
> * 컨테이너에 대한 Azure Monitor는 단일 확장입니다(클러스터당 하나만 필요함). 확장을 사용하지 않고 컨테이너에 대한 Azure Monitor의 이전 Helm 차트 설치를 모두 정리한 후에 확장을 통해 동일한 차트를 설치해야 합니다. [실행하기 전에 Helm 차트를 삭제`az k8s-extension create`](../../azure-monitor/containers/container-insights-optout-hybrid.md)하는 방법에 대한 지침을 따르세요.

**필수 매개 변수**

| 매개 변수 이름 | 설명 |
|----------------|------------|
| `--name` | 확장의 이름입니다. |
| `--extension-type` | 클러스터에 설치하려는 확장의 유형입니다. 예: Microsoft.AzureMonitor.Containers, microsoft.azuredefender.kubernetes | 
| `--scope` | `cluster` 또는 `namespace` 확장에 대한 설치 범위 |
| `--cluster-name` | 확장 인스턴스를 만들어야 하는 Azure Arc 지원 Kubernetes 리소스의 이름 |
| `--resource-group` | Azure Arc 지원 Kubernetes 리소스를 포함하는 리소스 그룹 |
| `--cluster-type` | 확장 인스턴스를 만들어야 하는 클러스터 유형입니다. Azure Arc `connectedClusters` 사용 가능한 Kubernetes에 해당하는 현재만 허용되는 값입니다. |

**선택적 매개 변수**

| 매개 변수 이름 | 설명 |
|--------------|------------|
| `--auto-upgrade-minor-version` | 확장 부 버전이 자동으로 업그레이드될지 여부를 지정하는 부울 속성입니다. 기본값: `true`.  이 매개 변수를 true로 설정하면 버전이 동적으로 업데이트되므로 `version` 매개 변수를 설정할 수 없습니다. `false`로 설정된 경우 확장은 패치 버전에 대해서도 자동으로 업그레이드되지 않습니다. |
| `--version` | 설치할 확장의 버전(확장 인스턴스를 고정할 특정 버전)입니다. auto-upgrade-minor-version이 `true`로 설정된 경우에는 제공되지 않아야 합니다. |
| `--configuration-settings` | 기능을 제어하기 위해 확장에 전달할 수 있는 설정입니다. 매개 변수 이름 뒤에 공백으로 구분된 `key=value` 쌍으로 전달됩니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-settings-file`을 사용할 수 없습니다. |
| `--configuration-settings-file` | 구성 설정을 확장에 전달하는 데 사용되는 키 값 쌍이 포함된 JSON 파일의 경로입니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-settings`를 사용할 수 없습니다. |
| `--configuration-protected-settings` | 이러한 설정은 `GET` API 호출 또는 `az k8s-extension show` 명령을 사용하여 검색할 수 없으므로 중요한 설정을 전달하는 데 사용됩니다. 매개 변수 이름 뒤에 공백으로 구분된 `key=value` 쌍으로 전달됩니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-protected-settings-file`을 사용할 수 없습니다. |
| `--configuration-protected-settings-file` | 중요한 설정을 확장에 전달하는 데 사용되는 키 값 쌍이 포함된 JSON 파일의 경로입니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-protected-settings`를 사용할 수 없습니다. |
| `--release-namespace` | 이 매개 변수는 릴리스가 생성될 네임스페이스를 나타냅니다. 이 매개 변수는 `scope` 매개 변수가 `cluster`로 설정되어 있는 경우에만 적용됩니다. |
| `--release-train` |  확장 작성자는 `Stable`, `Preview` 등의 다양한 릴리스 학습으로 버전을 게시할 수 있습니다. 이 매개 변수를 명시적으로 설정하지 않으면 `Stable`이 기본값으로 사용됩니다. `autoUpgradeMinorVersion` 매개 변수가 `false`로 설정된 경우에는 이 매개 변수를 사용할 수 없습니다. |
| `--target-namespace` | 이 매개 변수는 릴리스가 생성되는 네임스페이스를 나타냅니다. 이 확장 인스턴스에 대해 만든 시스템 계정의 사용 권한은 이 네임스페이스로 제한됩니다. 이 매개 변수는 `scope` 매개 변수가 `namespace`로 설정되어 있는 경우에만 적용됩니다. |

### <a name="show-details-of-an-extension-instance"></a>확장 인스턴스의 세부 정보 표시

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension show`를 사용하여 현재 설치된 확장 인스턴스를 확인합니다.

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**출력:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>클러스터에 설치된 모든 확장 나열

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension list`를 사용하여 클러스터에 설치된 모든 확장을 나열합니다.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**출력:**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>확장 인스턴스 삭제

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension delete`를 사용하여 클러스터에서 확장 인스턴스를 삭제합니다.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> 이 확장을 나타내는 Azure 리소스는 즉시 삭제됩니다. 이 확장과 연결된 클러스터의 Helm 릴리스는 Kubernetes 클러스터에서 실행 중인 에이전트가 네트워크에 연결되어 있고 Azure 서비스에 다시 연결하여 원하는 상태를 가져올 수 있는 경우에만 삭제됩니다.

## <a name="next-steps"></a>다음 단계

Azure Arc 사용 Kubernetes 현재 사용할 수 있는 클러스터 확장에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  클라우드 용 Microsoft [Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Azure Arc-활성화 된 서비스 메시 열기](tutorial-arc-enabled-open-service-mesh.md)
> 
> [!div class="nextstepaction"]
> [Microsoft Defender for Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> 
> [!div class="nextstepaction"]
> [Azure Arc의 Azure App Service](../../app-service/overview-arc-integration.md)
> 
> [!div class="nextstepaction"]
> [Kubernetes의 Event Grid](../../event-grid/kubernetes/overview.md)
> 
> [!div class="nextstepaction"]
> [Azure Arc의 Azure API Management](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md)
