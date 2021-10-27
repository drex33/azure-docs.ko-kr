---
title: '빠른 시작: Azure Arc에 기존 Kubernetes 클러스터 연결'
description: 이 빠른 시작에서는 Azure Arc 사용 Kubernetes 클러스터를 연결하는 방법을 알아봅니다.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 09/09/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, Azure, 클러스터
ms.openlocfilehash: 6716ae8b85893b9af4b439ea76eca631bff525bf
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066455"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>빠른 시작: Azure Arc에 기존 Kubernetes 클러스터 연결

이 빠른 시작에서는 Azure Arc 사용 Kubernetes의 이점 및 기존 Kubernetes 클러스터를 Azure Arc에 연결하는 방법을 알아봅니다. 클러스터를 Azure Arc에 연결하는 개념은 [Azure Arc 사용 Kubernetes 에이전트 아키텍처 문서](./conceptual-agent-architecture.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* 버전 >= 2.16.0으로 [Azure CLI 설치 또는 업그레이드](/cli/azure/install-azure-cli)

* 버전 1.2.0 이상의 **connectedk8s** Azure CLI 확장을 설치합니다.

  ```console
  az extension add --name connectedk8s
  ```

* 실행 중인 Kubernetes 클러스터. 클러스터가 없는 경우 이러한 옵션 중 하나를 사용하여 클러스터를 만들 수 있습니다.
    * [Docker의 Kubernetes(KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 또는 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)용 Docker를 사용하여 Kubernetes 클러스터 만들기
    * [클러스터 API](https://cluster-api.sigs.k8s.io/user/quick-start.html)를 사용하는 자체 관리되는 Kubernetes 클러스터
    * OpenShift 클러스터를 Azure Arc에 연결하려면 `az connectedk8s connect`를 실행하기 전에 클러스터에서 다음 명령을 한 번만 실행해야 합니다.

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 클러스터에는 운영 체제 및 아키텍처 유형 `linux/amd64`의 노드가 하나 이상 있어야 합니다. `linux/arm64` 노드만 있는 클러스터는 아직 지원되지 않습니다.

* 클러스터를 가리키는 `kubeconfig` 파일 및 컨텍스트입니다.
* Azure Arc 사용 Kubernetes 리소스 종류(`Microsoft.Kubernetes/connectedClusters`)에 대한 '읽기' 및 '쓰기' 권한

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* [Azure PowerShell](/powershell/azure/install-az-ps) 버전 5.9.0 이상.

* **Az.ConnectedKubernetes** PowerShell 모듈을 설치합니다.

    ```azurepowershell-interactive
    Install-Module -Name Az.ConnectedKubernetes
    ```

    > [!IMPORTANT]
    > **Az.ConnectedKubernetes** PowerShell 모듈이 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다.

* 실행 중인 Kubernetes 클러스터. 클러스터가 없는 경우 이러한 옵션 중 하나를 사용하여 클러스터를 만들 수 있습니다.
    * [Docker의 Kubernetes(KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 또는 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)용 Docker를 사용하여 Kubernetes 클러스터 만들기
    * [클러스터 API](https://cluster-api.sigs.k8s.io/user/quick-start.html)를 사용하는 자체 관리되는 Kubernetes 클러스터
    * OpenShift 클러스터를 Azure Arc에 연결하려면 `New-AzConnectedKubernetes`를 실행하기 전에 클러스터에서 다음 명령을 한 번만 실행해야 합니다.

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 클러스터에는 운영 체제 및 아키텍처 유형 `linux/amd64`의 노드가 하나 이상 있어야 합니다. `linux/arm64` 노드만 있는 클러스터는 아직 지원되지 않습니다.

* 클러스터를 가리키는 `kubeconfig` 파일 및 컨텍스트입니다.
* Azure Arc 사용 Kubernetes 리소스 종류(`Microsoft.Kubernetes/connectedClusters`)에 대한 '읽기' 및 '쓰기' 권한

* [Helm 3](https://helm.sh/docs/intro/install)을 설치합니다. Helm 3 버전이 &lt; 3.7.0인지 확인합니다.

---

## <a name="meet-network-requirements"></a>네트워크 요구 사항 충족

> [!IMPORTANT]
> Azure Arc 에이전트가 작동하려면 함수에 대한 `https://:443`에서 다음 아웃바운드 URL이 필요합니다.
> `*.servicebus.windows.net`의 경우 방화벽 및 프록시에 대한 아웃바운드 액세스에 대해 websocket을 사용하도록 설정해야 합니다.

| 엔드포인트(DNS) | Description |
| ----------------- | ------------- |
| `https://management.azure.com`(Azure 클라우드의 경우), `https://management.usgovcloudapi.net`(Azure 미국 정부의 경우) | 에이전트가 Azure에 연결하고 클러스터를 등록하는 데 필요합니다. |
| `https://<region>.dp.kubernetesconfiguration.azure.com`(Azure 클라우드의 경우), `https://<region>.dp.kubernetesconfiguration.azure.us`(Azure 미국 정부의 경우) | 에이전트가 상태를 푸시하고 구성 정보를 가져오기 위한 데이터 평면 엔드포인트. |
| `https://login.microsoftonline.com`, `login.windows.net`(Azure 클라우드의 경우), `https://login.microsoftonline.us`(Azure 미국 정부의 경우) | Azure Resource Manager 토큰을 가져오고 업데이트하는 데 필요합니다. |
| `https://mcr.microsoft.com`, `https://*.data.mcr.microsoft.com` | Azure Arc 에이전트의 컨테이너 이미지를 끌어오는 데 필요합니다.                                                                  |
| `https://gbl.his.arc.azure.com`(Azure 클라우드의 경우), `https://gbl.his.arc.azure.us`(Azure 미국 정부의 경우) |  시스템 할당 MSI(Managed Identity 증명서) 인증서를 끌어오기 위한 지역 엔드포인트를 가져오는 데 필요합니다. |
| `https://*.his.arc.azure.com`(Azure 클라우드의 경우), `https://usgv.his.arc.azure.us`(Azure 미국 정부의 경우) |  시스템이 할당한 관리 ID 인증서를 가져오는 데 필요합니다. |
|`*.servicebus.windows.net`, `guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com`, `sts.windows.net` | [클러스터 연결](cluster-connect.md) 및 [사용자 지정 위치](custom-locations.md) 기반 시나리오의 경우. |
|`https://k8connecthelm.azureedge.net` | `az connectedk8s connect`는 Helm 3를 사용하여 Kubernetes 클러스터에 Azure Arc 에이전트를 배포합니다. 이 엔드포인트는 에이전트 Helm 차트의 배포를 용이하게 하기 위해 Helm 클라이언트 다운로드에 필요합니다. |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. Azure Arc 사용 Kubernetes에 공급자 등록

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 다음 명령을 입력합니다.
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

    등록되면 이러한 네임스페이스의 `RegistrationState` 상태가 `Registered`로 변경되는 것을 확인해야 합니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. 다음 명령을 입력합니다.
    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```
1. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.
    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```

    등록되면 이러한 네임스페이스의 `RegistrationState` 상태가 `Registered`로 변경되는 것을 확인해야 합니다.
---

## <a name="2-create-a-resource-group"></a>2. 리소스 그룹 만들기

다음 명령을 실행합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name AzureArcTest --location EastUS --output table
```

출력:
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name AzureArcTest -Location EastUS
```

출력:
<pre>
ResourceGroupName : AzureArcTest
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureArcTest
</pre>

---

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. 기존 Kubernetes 클러스터 연결

다음 명령을 실행합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

출력:
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

> [!TIP]
> 위치 매개 변수가 지정되지 않은 위의 명령은 Azure Arc 사용 Kubernetes 리소스를 리소스 그룹과 동일한 위치에 만듭니다. Azure Arc 사용 Kubernetes 리소스를 다른 위치에 만들려면 `az connectedk8s connect` 명령을 실행할 때 `--location <region>` 또는 `-l <region>`을 지정합니다.

> [!NOTE]
> 서비스 주체를 사용하여 Azure CLI에 로그인한 경우 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정하려면 [추가 매개 변수](troubleshooting.md#enable-custom-locations-using-service-principal)를 설정해야 합니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest -Location eastus
```

출력:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

## <a name="4a-connect-using-an-outbound-proxy-server"></a>4a. 아웃바운드 프록시 서버를 사용하여 연결

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

클러스터가 아웃바운드 프록시 서버 뒤에 있는 경우 Azure CLI 및 Azure Arc 사용 Kubernetes 에이전트에서 아웃바운드 프록시 서버를 통해 요청을 라우팅해야 합니다.

1. 아웃바운드 프록시 서버를 사용하기 위해 Azure CLI에 필요한 환경 변수를 설정합니다.

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

2. 프록시 매개 변수를 지정하여 connect 명령을 실행합니다.

    ```azurecli
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

    > [!NOTE]
    > * 클러스터 내 서비스 간 통신과 관련된 일부 네트워크 요청은 아웃바운드 통신을 위해 프록시 서버를 통해 라우팅되는 트래픽과 분리되어야 합니다. `--proxy-skip-range` 매개 변수를 사용하여 에이전트에서 이러한 엔드포인트로의 통신이 아웃바운드 프록시를 거치지 않도록 CIDR 범위와 엔드포인트를 쉼표로 구분하여 지정할 수 있습니다. 최소한 클러스터에 있는 서비스의 CIDR 범위는 이 매개 변수의 값으로 지정되어야 합니다. 예를 들어, `kubectl get svc -A`가 모든 서비스에 `10.0.0.0/16` 범위의 ClusterIP 값이 있는 서비스 목록을 반환한다고 가정해 보겠습니다. 그러면 `--proxy-skip-range`에 지정할 값은 `10.0.0.0/16,kubernetes.default.svc,.svc.cluster.local,.svc`입니다.
    > * 대부분의 아웃바운드 프록시 환경에서 `--proxy-http`, `--proxy-https` 및 `--proxy-skip-range`가 필요합니다. `--proxy-cert`는 프록시에서 예상되는 신뢰할 수 있는 인증서를 에이전트 Pod의 신뢰할 수 있는 인증서 저장소에 삽입해야 하는 경우에 *만* 필요합니다.
    > * Websocket 연결을 허용하도록 아웃바운드 프록시를 구성해야 합니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

클러스터가 아웃바운드 프록시 서버 뒤에 있는 경우 Azure PowerShell 및 Azure Arc 사용 Kubernetes 에이전트에서 아웃바운드 프록시 서버를 통해 요청을 라우팅해야 합니다.

1. 아웃바운드 프록시 서버를 사용하기 위해 Azure PowerShell에 필요한 환경 변수를 설정합니다.

    ```powershell
    $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
    $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
    $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
    ```

2. 프록시 매개 변수를 지정하여 connect 명령을 실행합니다.

    ```azurepowershell
    New-AzConnectedKubernetes -ClusterName <cluster-name> -ResourceGroupName <resource-group> -Location eastus -Proxy 'https://<proxy-server-ip-address>:<port>'
    ```

---

## <a name="5-verify-cluster-connection"></a>5. 클러스터 연결 확인

다음 명령을 실행합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az connectedk8s list --resource-group AzureArcTest --output table
```

출력:
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzConnectedKubernetes -ResourceGroupName AzureArcTest
```

출력:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

> [!NOTE]
> 클러스터를 온보딩하면 클러스터 메타데이터(클러스터 버전, 에이전트 버전, 노드 수 등)가 Azure Portal의 Azure Arc 사용 Kubernetes 리소스에 대한 개요 페이지에 표시되는 데 약 5~10분이 걸립니다.

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. Kubernetes용 Azure Arc 에이전트 보기

Azure Arc 사용 Kubernetes는 몇 가지 연산자를 `azure-arc` 네임스페이스에 배포합니다.

1. 이러한 배포 및 Pod는 다음을 사용하여 볼 수 있습니다.

    ```console
    kubectl get deployments,pods -n azure-arc
    ```

1. 모든 Pod가 `Running` 상태인지 확인합니다.

    출력:
    <pre>

    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    </pre>

## <a name="7-clean-up-resources"></a>7. 리소스 정리

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 다음 명령을 사용하여 Azure Arc 사용 Kubernetes 리소스, 모든 관련 구성 리소스 *및* 클러스터에서 실행되는 모든 에이전트를 삭제할 수 있습니다.

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
> Azure Portal을 사용하여 Azure Arc 사용 Kubernetes 리소스를 삭제하면 연결된 모든 구성 리소스가 제거되지만 클러스터에서 실행되는 에이전트는 제거되지 *않습니다*. 따라서 Azure Portal 대신 `az connectedk8s delete`를 사용하여 Azure Arc 사용 Kubernetes 리소스를 삭제하는 것이 가장 좋습니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell에서 다음 명령을 사용하여 Azure Arc 사용 Kubernetes 리소스, 모든 관련 구성 리소스 *및* 클러스터에서 실행되는 모든 에이전트를 삭제할 수 있습니다.

```azurepowershell
Remove-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest
```

>[!NOTE]
> Azure Portal을 사용하여 Azure Arc 사용 Kubernetes 리소스를 삭제하면 연결된 모든 구성 리소스가 제거되지만 클러스터에서 실행되는 에이전트는 제거되지 *않습니다*. 따라서 Azure Portal 대신 `Remove-AzConnectedKubernetes`를 사용하여 Azure Arc 사용 Kubernetes 리소스를 삭제하는 것이 가장 좋습니다.

---

## <a name="next-steps"></a>다음 단계

GitOps를 사용하여 연결된 Kubernetes 클러스터에 구성을 배포하는 방법을 알아보려면 다음 문서로 이동합니다.
> [!div class="nextstepaction"]
> [GitOps를 사용하여 구성 배포](tutorial-use-gitops-connected-cluster.md)
