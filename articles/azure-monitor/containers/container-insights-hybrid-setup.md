---
title: Container Insights를 사용하여 하이브리드 Kubernetes 클러스터 구성 | Microsoft Docs
description: 이 문서에서는 Azure Stack 또는 다른 환경에서 호스트되는 Kubernetes 클러스터를 모니터하도록 Container Insights를 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f9fe3e20ec7486d9c6f18d1494ec3bedbdc17e86
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231554"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Container Insights를 사용하여 하이브리드 Kubernetes 클러스터 구성

Container Insights는 AKS(Azure Kubernetes Service) 및 Azure에서 호스트되는 자체 관리되는 Kubernetes 클러스터인 [Azure의 AKS 엔진](https://github.com/Azure/aks-engine)을 위해 다양한 기능의 모니터링 경험을 제공합니다. 이 문서에서는 Azure 외부에서 호스트되는 Kubernetes 클러스터 모니터링을 사용하도록 설정하는 방법과 비슷한 모니터링 경험을 얻는 방법을 설명합니다.

## <a name="supported-configurations"></a>지원되는 구성

다음 구성은 Container Insights에서 공식적으로 지원됩니다. 다른 버전의 Kubernetes 및 운영 체제 버전이 있으면 askcoin@microsoft.com으로 이메일을 보내세요.

- 환경:

    - Kubernetes 온-프레미스
    - Azure 및 Azure Stack의 AKS 엔진. 자세한 내용은 [Azure Stack의 AKS 엔진](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)을 참조하세요.
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 버전 4 이상, 온-프레미스 또는 기타 클라우드 환경.

- Kubernetes 및 지원 정책의 버전은 [지원되는 AKS](../../aks/supported-kubernetes-versions.md) 버전과 동일합니다.

- 지원되는 컨테이너 런타임에는 Docker, Moby 및 CRI 호환 런타임(예: CRI-O 및 ContainerD)이 포함됩니다.

- 지원되는 마스터 및 작업자 노드를 위한 Linux OS 릴리스는 Ubuntu(18.04 LTS 및 16.04 LTS) 및 Red Hat Enterprise Linux CoreOS 43.81입니다.

- 지원되는 액세스 제어: Kubernetes RBAC 및 비RBAC

## <a name="prerequisites"></a>전제 조건

시작하기 전에 다음 항목이 있는지 확인하십시오.

- [Log Analytics 작업 영역](../logs/design-logs-deployment.md)

    컨테이너 인사이트는 Azure [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열된 지역에서 Log Analytics 작업 영역을 지원합니다. 사용자 고유의 작업 영역을 만들려면 [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../logs/quick-create-workspace.md)에서 만들 수 있습니다.

    >[!NOTE]
    >동일한 Log Analytics 작업 영역에 대해 동일한 클러스터 이름의 여러 클러스터 모니터링을 사용하도록 설정하는 것은 지원되지 않습니다. 클러스터 이름은 고유해야 합니다.
    >

- 사용자는 컨테이너 모니터링을 사용하도록 설정하기 위한 **Log Analytics 기여자 역할** 의 구성원입니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 및 로그 데이터 액세스 관리](../logs/manage-access.md)를 참조하세요.

- 모니터링 데이터를 보려면 컨테이너 인사이트를 사용하여 구성된 Log Analytics 작업 영역에 [*Log Analytics 읽기 권한자*](../logs/manage-access.md#manage-access-using-azure-permissions) 역할이 있어야 합니다.

- 지정된 Kubernetes 클러스터에 대해 Container Insights를 온보딩하기 위한 [HELM 클라이언트](https://helm.sh/docs/using_helm/).

- 다음 프록시 및 방화벽 구성 정보는 Linux용 Log Analytics 에이전트의 컨테이너화된 버전이 Azure Monitor와 통신하기 위해 필요합니다.

    |에이전트 리소스|포트 |
    |------|---------|
    |*.ods.opinsights.azure.com |포트 443 |
    |*.oms.opinsights.azure.com |포트 443 |
    |*.dc.services.visualstudio.com |포트 443 |

- 컨테이너화된 에이전트에서는 성능 메트릭을 수집하기 위해 클러스터의 모든 노드에서 Kubelet의 `cAdvisor secure port: 10250` 또는 `unsecure port :10255`를 열어야 합니다. 아직 구성되지 않았으면 Kubelet의 cAdvisor에서 `secure port: 10250`을 구성하는 것이 좋습니다.

- 컨테이너화된 에이전트에서 인벤토리 데이터 `KUBERNETES_SERVICE_HOST` 및 `KUBERNETES_PORT_443_TCP_PORT`를 수집하기 위해 클러스터 내에서 Kubernetes API 서비스와 통신하기 위해서는 컨테이너에 다음 환경 변수를 지정해야 합니다.

>[!IMPORTANT]
>하이브리드 Kubernetes 클러스터를 모니터하는 데 지원되는 최소 에이전트 버전은 ciprod10182019 이상입니다.

## <a name="enable-monitoring"></a>모니터링 사용

하이브리드 Kubernetes 클러스터를 위해 Container Insights를 사용하도록 설정하려면 다음 단계를 순서대로 수행해야 합니다.

1. Container Insights 솔루션으로 Log Analytics 작업 영역을 구성합니다.   

2. Log Analytics 작업 영역에 Container Insights HELM 차트를 사용하도록 설정합니다.

Azure Monitor의 모니터링 솔루션에 대한 자세한 내용은 [여기](../../azure-monitor/insights/solutions.md)를 참조하세요.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure Monitor Containers 솔루션 추가 방법

Azure PowerShell cmdlet `New-AzResourceGroupDeployment` 또는 Azure CLI를 사용하여 제공된 Azure Resource Manager 템플릿으로 솔루션을 배포할 수 있습니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- **workspaceResourceId** - Log Analytics 작업 영역의 전체 리소스 ID입니다.
- **workspaceRegion** - 작업 영역이 생성된 지역이며, Azure Portal에서 볼 때 작업 영역 속성에서 **위치** 라고도 부릅니다.

**containerSolutionParams.json** 파일의 `workspaceResourceId` 매개 변수 값에 필요한 Log Analytics 작업 영역의 전체 리소스 ID를 먼저 식별하려면 다음 단계를 수행한 후 PowerShell cmdlet 또는 Azure CLI 명령을 실행하여 솔루션을 추가합니다.

1. 다음 명령을 사용하여 액세스 권한이 있는 모든 구독을 나열합니다.

    ```azurecli
    az account list --all -o table
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    **SubscriptionId** 에 대한 값을 복사합니다.

2. 다음 명령을 사용하여 Log Analytics 작업 영역을 호스트하는 구독으로 전환합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 다음 예제는 기본 JSON 형식의 구독에 있는 작업 영역 목록을 표시합니다.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    출력에서 작업 영역 이름을 찾은 다음, 해당 Log Analytics 작업 영역의 전체 리소스 ID를 필드 **ID** 로 복사합니다.

4. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. 이 파일을 로컬 폴더에 containerSolution.json으로 저장합니다.

6. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. 3단계에서 복사한 값을 사용하여 **workspaceResourceId** 의 값을 편집하고, **workspaceRegion** 에 대해 Azure CLI 명령 [az monitor log-analytics workspace show](/cli/azure/monitor/log-analytics/workspace#az_monitor-log-analytics-workspace-list&preserve-view=true)를 실행한 후 **Region** 값을 복사합니다.

8. 이 파일을 로컬 폴더에 containerSolutionParams.json으로 저장합니다.

9. 이제 이 템플릿을 배포할 수 있습니다.

   - Azure PowerShell을 사용하여 배포하려면 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```powershell
       provisioningState       : Succeeded
       ```

   - Azure CLI를 사용하여 배포하려면 다음 명령을 실행합니다.

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```azurecli
       provisioningState       : Succeeded
       ```

       모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

## <a name="install-the-helm-chart"></a>HELM 차트 설치

이 섹션에서는 Container Insights에 대해 컨테이너화된 에이전트를 설치합니다. 계속하기 전 `omsagent.secret.wsid` 매개 변수에 필요한 작업 영역 ID와 `omsagent.secret.key` 매개 변수에 필요한 기본 키를 식별해야 합니다. 다음 단계를 수행하여 이 정보를 식별한 후 명령을 실행하여 HELM 차트를 사용해서 에이전트를 설치할 수 있습니다.

1. 다음 명령을 실행하여 작업 영역 ID를 식별합니다.

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    출력의 **name** 필드 아래에서 작업 영역 이름을 찾은 후 **customerID** 필드 아래에서 Log Analytics 작업 영역의 작업 영역 ID를 복사합니다.

2. 다음 명령을 실행하여 작업 영역의 기본 키를 식별합니다.

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    출력의 **primarySharedKey** 필드 아래에서 기본 키를 찾은 후 값을 복사합니다.

>[!NOTE]
>다음 명령은 Helm 버전 2에만 적용할 수 있습니다. `--name` 매개 변수 사용은 Helm 버전 3에 적용할 수 없습니다. 

>[!NOTE]
>Kubernetes 클러스터가 프록시 서버를 통해 통신하는 경우, `omsagent.proxy` 매개 변수를 프록시 서버의 URL로 구성합니다. 클러스터가 프록시 서버를 통해 통신하지 않는 경우 이 매개 변수를 지정할 필요가 없습니다. 자세한 내용은 이 문서의 뒷부분에 있는 [프록시 엔드포인트 구성](#configure-proxy-endpoint)을 참조하세요.

3. 다음 명령을 실행하여 로컬 목록에 Azure 차트 리포지토리를 추가합니다.

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. 다음 명령을 실행하여 차트를 설치합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Log Analytics 작업 영역이 Azure 중국 21Vianet에 있는 경우 다음 명령을 실행합니다.

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Log Analytics 작업 영역이 Azure US Government에 있는 경우 다음 명령을 실행합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>API 모델을 사용하여 Helm 차트 사용

API 모델이라고도 부르는 AKS Engine 클러스터 사양 json 파일에 추가 기능을 지정할 수 있습니다. 이 추가 기능에서 수집된 모니터링 데이터가 저장된 Log Analytics 작업 영역의 `WorkspaceGUID` 및 `WorkspaceKey`에 대한 base64로 인코딩된 버전을 제공합니다. 이전 섹션의 1단계 및 2단계를 사용하여 `WorkspaceGUID` 및 `WorkspaceKey`를 찾을 수 있습니다.

Azure Stack Hub 클러스터에 대해 지원되는 API 정의는 이 예제 [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json)에서 찾을 수 있습니다. 특히 **kubernetesConfig** 에서 **addons** 속성을 찾습니다.

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>에이전트 데이터 수집 구성

차트 버전 1.0.0부터 에이전트 데이터 수집 설정은 ConfigMap에서 제어됩니다. 에이전트 데이터 수집 설정에 대한 설명서는 [여기](container-insights-agent-config.md)를 참조하세요.

차트를 성공적으로 배포한 후 Azure Portal에서 Container Insights에 있는 하이브리드 Kubernetes 클러스터의 데이터를 검토할 수 있습니다.  

>[!NOTE]
>에이전트가 Azure Log Analytics 작업 영역에서 커밋을 수행하는 데 까지 걸리는 수집 대기 시간은 약 5~10분 정도입니다. Azure Monitor에서 모든 필요한 모니터링 데이터를 사용할 수 있을 때까지는 **데이터 없음** 또는 **알 수 없음** 이 클러스터 상태로 표시됩니다.

## <a name="configure-proxy-endpoint"></a>프록시 엔드포인트 구성

차트 버전 2.7.1부터는 차트에서 `omsagent.proxy` 차트 매개 변수를 사용한 프록시 엔드포인트 지정이 지원됩니다. 이를 통해 프록시 서버를 통해 통신할 수 있습니다. Container Insights 에이전트와 Azure Monitor 사이의 통신은 HTTP 또는 HTTPS 프록시 서버일 수 있으며, 익명 및 기본 인증(사용자 이름/암호)이 모두 지원됩니다.

프록시 구성 값은 `[protocol://][user:password@]proxyhost[:port]` 구문을 갖습니다.

> [!NOTE]
>프록시 서버에 인증이 필요하지 않더라도 의사 사용자 이름/암호를 지정해야 합니다. 이는 사용자 이름 또는 암호일 수 있습니다.

|속성| Description |
|--------|-------------|
|프로토콜 | HTTP 또는 HTTPS |
|사용자 | 프록시 인증을 위한 선택적 사용자 이름 |
|password | 프록시 인증을 위한 선택적 암호 |
|proxyhost | 프록시 서버의 주소 또는 FQDN |
|포트 | 프록시 서버의 선택적인 포트 번호 |

`omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

프로토콜을 **http** 로 지정하면 SSL/TLS 보안 연결을 사용하여 HTTP 요청이 생성됩니다. 프록시 서버는 SSL/TLS 프로토콜을 지원해야 합니다.

## <a name="troubleshooting"></a>문제 해결

하이브리드 Kubernetes 클러스터에 대해 모니터링을 사용하도록 시도하는 동안 오류가 발생하면 PowerShell 스크립트 [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s)을 복사하고 이를 컴퓨터 폴더에 저장합니다. 이 스크립트는 발생한 문제의 감지 및 해결에 도움이 되도록 제공되었습니다. 이 스크립트는 다음과 같은 문제를 감지하고 해결하도록 설계되었습니다.

- 지정된 Log Analytics 작업 영역이 잘못되었습니다.
- Log Analytics 작업 영역이 Container Insights 솔루션으로 구성되었습니다. 그렇지 않으면 작업 영역을 구성합니다.
- OmsAgent replicaset Pod가 실행 중입니다.
- OmsAgent daemonset Pod가 실행 중입니다.
- OmsAgent Health 서비스가 실행 중입니다.
- 컨테이너화된 에이전트에 구성된 Log Analytics 작업 영역 ID 및 키가 Insight가 구성된 작업 영역과 일치합니다.
- 모든 Linux 작업자 노드에 rs Pod 예약을 위해 `kubernetes.io/role=agent` 레이블이 있는지 확인합니다. 없으면 추가합니다.
- 클러스터의 모든 노드에서 `cAdvisor secure port:10250` 또는 `unsecure port: 10255`가 열려 있는지 확인합니다.

Azure PowerShell로 실행하려면 스크립트가 포함된 폴더에서 다음 명령을 사용합니다.

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>다음 단계

하이브리드 Kubernetes 클러스터 및 여기에서 실행되는 워크로드의 상태 및 리소스 사용률을 수집할 수 있도록 모니터링을 사용해서 Container Insights를 [사용하는 방법](container-insights-analyze.md)을 알아보세요.