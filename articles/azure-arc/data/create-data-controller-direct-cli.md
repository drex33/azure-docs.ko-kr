---
title: Azure Arc 데이터 컨트롤러 만들기 | 직접 연결 모드
description: 직접 연결 모드에서 데이터 컨트롤러를 만드는 방법을 설명합니다.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/12/2021
ms.topic: overview
ms.openlocfilehash: b754ee5cff27f88a046f1bdbd64ddd4617c9fe37
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133364792"
---
#  <a name="create-azure-arc-data-controller-in-direct-connectivity-mode-using-cli"></a>CLI를 사용 하 여 직접 연결 모드에서 Azure Arc 데이터 컨트롤러 만들기

이 문서에서는 Azure CLI를 사용 하 여 직접 연결 모드에서 Azure Arc 데이터 컨트롤러를 만드는 방법을 설명 합니다. 

## <a name="complete-prerequisites"></a>필수 조건 완료

시작하기 전에 [데이터 컨트롤러 배포 - 직접 연결 모드 - 필수 조건](create-data-controller-direct-prerequisites.md)에서 필수 조건을 완료 했는지 확인합니다.

다음은 직접 연결 모드에서 Azure Arc 데이터 컨트롤러를 만드는 단계입니다.

1. Azure Arc 지원 데이터 서비스 확장 만들기 
1. 사용자 지정 위치를 만듭니다.
1. 데이터 컨트롤러를 만듭니다.

## <a name="step-1-create-an-azure-arc-enabled-data-services-extension"></a>1단계: Azure Arc 지원 데이터 서비스 확장 만들기

k8s-extension CLI를 사용하여 데이터 서비스 확장을 만듭니다.

### <a name="set-environment-variables"></a>환경 변수 설정

다음 환경 변수를 설정 합니다 .이 변수는 이후 단계에서 사용 됩니다.

다음은 두 가지 환경 변수 집합입니다. 첫 번째 변수 집합은 Azure 구독, 리소스 그룹, 클러스터 이름, 위치, 확장 및 네임 스페이스를 식별 합니다. 두 번째는 메트릭 및 로그 대시보드에 액세스 하기 위한 자격 증명을 정의 합니다.

환경 변수는 로그 및 메트릭 서비스에 대 한 암호를 포함 합니다. 암호는 길이가 8 자 이상 이어야 하며, 라틴어 대문자, 라틴어 소문자, 숫자 및 영숫자가 아닌 문자 중 세 가지 범주의 문자를 포함 해야 합니다.


#### <a name="linux"></a>[Linux](#tab/linux)

```console
## variables for Azure subscription, resource group, cluster name, location, extension, and namespace.
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export clusterName=<name of your connected Kubernetes cluster>
export location=<Azure location>
export adsExtensionName="<extension name>" 
export namespace="<namespace>"
## variables for logs and metrics dashboard credentials
export AZDATA_LOGSUI_USERNAME=<username for Kibana dashboard>
export AZDATA_LOGSUI_PASSWORD=<password for Kibana dashboard>
export AZDATA_METRICSUI_USERNAME=<username for Grafana dashboard>
export AZDATA_METRICSUI_PASSWORD=<password for Grafana dashboard>
```

#### <a name="windows-powershell"></a>[Windows(PowerShell)](#tab/windows)

``` PowerShell
## variables for Azure location, extension and namespace
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:clusterName="<name of your connected Kubernetes cluster>"
$ENV:location="<Azure location>"
$ENV:adsExtensionName="<name of Data controller extension" 
$ENV:namespace="<namespace where you will deploy the extension and data controller>"
## variables for Metrics and Monitoring dashboard credentials
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

--- 

### <a name="create-the-arc-data-services-extension"></a>Arc 데이터 서비스 확장 만들기

다음 명령은 Arc data services 확장을 만듭니다.

#### <a name="linux"></a>[Linux](#tab/linux)

```console
az k8s-extension create --cluster-name ${clusterName} --resource-group ${resourceGroup} --name ${adsExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace ${namespace} --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper
az k8s-extension show --resource-group ${resourceGroup} --cluster-name ${resourceName} --name ${adsExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>[Windows(PowerShell)](#tab/windows)

```PowerShell
az k8s-extension create --cluster-name $ENV:clusterName --resource-group $ENV:resourceGroup --name $ENV:adsExtensionName --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace $ENV:namespace --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper
az k8s-extension show --resource-group $ENV:resourceGroup --cluster-name $ENV:clusterName --name $ENV:adsExtensionName --cluster-type connectedclusters
```

---

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>프라이빗 컨테이너 레지스트리 및 자격 증명을 사용하여 Azure Arc Data Services 확장 배포

프라이빗 리포지토리에서 배포하는 경우 아래 명령을 사용합니다.

```azurecli
az k8s-extension create --cluster-name "<connected cluster name>" --resource-group "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

예를 들면 다음과 같습니다.

```azurecli
az k8s-extension create --cluster-name "my-connected-cluster" --resource-group "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> Arc 데이터 서비스 확장 설치를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Arc 데이터 서비스 확장이 생성되었는지 확인

Azure Arc 사용 데이터 서비스 확장의 배포 상태를 확인할 수 있습니다. Azure Portal 또는 큐브 사용

#### <a name="check-status-from-azure-portal"></a>Azure Portal에서 상태 확인

1. Azure Portal에 로그인하고 Kubernetes 연결 된 클러스터 리소스가 있는 리소스 그룹을 찾습니다.
1. 확장이 배포된 Azure Arc 지원 kubernetes 클러스터(유형 = "Kubernetes - Azure Arc")를 선택합니다.
1. 왼쪽 탐색의 **설정** 에서 **확장** 을 선택합니다.
1. 포털에는 이전에 설치 된 상태로 만든 확장이 표시 됩니다.

#### <a name="check-status-using-kubectl-cli"></a>kubectl CLI를 사용하여 상태 확인

1. 터미널 창을 통해 Kubernetes 클러스터에 연결합니다.
1. 아래 명령을 실행 하 고 다음을 확인 합니다.
   -  위에서 언급 한 네임 스페이스를 만들었습니다. 

    및 

   - `bootstrapper`다음 단계로 진행 하기 전에 pod 상태가 **실행 중** 입니다.

   ``` console
   kubectl get pods --name <name of namespace used in the json template file above>
   ```

예를 들어 다음 예제에서는 `arc` 네임스페이스에서 pod를 가져옵니다.

```console
#Example:
kubectl get pods --name arc
```

## <a name="retrieve-the-managed-identity-and-grant-roles"></a>관리 ID 검색 및 역할 부여

Arc data services 확장을 만들면 Azure에서 관리 id를 만듭니다. 사용 및/또는 업로드할 메트릭을 위해이 관리 되는 id에 특정 역할을 할당 해야 합니다. 

### <a name="retrieve-managed-identity-of-the-arc-data-controller-extension"></a>Arc 데이터 컨트롤러 확장의 관리 되는 id 검색

```powershell
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername> --cluster-type connectedClusters --name <name of extension> | convertFrom-json).identity.principalId
#Example
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | convertFrom-json).identity.principalId
```

### <a name="assign-role-to-the-managed-identity"></a>관리 id에 역할 할당

아래 명령어를 실행하여 **기여자** 및 **모니터링 메트릭 게시자** 역할을 할당합니다.

```azurecli
az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Contributor" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"
az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Monitoring Metrics Publisher" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"
```

## <a name="create-a-custom-location-using-customlocation-cli-extension"></a>CLI 확장을 사용 하 여 사용자 지정 위치 만들기 `customlocation`

사용자 지정 위치는 Kubernetes 클러스터의 네임스페이스에 해당하는 Azure 리소스입니다.  사용자 지정 위치는 Azure에서 리소스를 배포하는 대상으로 사용됩니다. [Azure Arc 지원 Kubernetes 설명서의 맨 위에 있는 사용자 지정 위치](../kubernetes/conceptual-custom-locations.md)에서 사용자 지정 위치에 대해 자세히 알아보세요.

### <a name="set-environment-variables"></a>환경 변수 설정

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export clName=mycustomlocation
export hostClusterId=$(az connectedk8s show --resource-group ${resourceGroup} --name ${clusterName} --query id -o tsv)
export extensionId=$(az k8s-extension show --resource-group ${resourceGroup} --cluster-name ${clusterName} --cluster-type connectedClusters --name ${adsExtensionName} --query id -o tsv)
az customlocation create --resource-group ${resourceGroup} --name ${clName} --namespace ${namespace} --host-resource-id ${hostClusterId} --cluster-extension-ids ${extensionId} --location ${location}
```

#### <a name="windows-powershell"></a>[Windows(PowerShell)](#tab/windows)

```PowerShell
$ENV:clName="mycustomlocation"
$ENV:hostClusterId=(az connectedk8s show --resource-group $ENV:resourceGroup --name $ENV:clusterName --query id -o tsv)
$ENV:extensionId=(az k8s-extension show --resource-group $ENV:resourceGroup --cluster-name $ENV:clusterName --cluster-type connectedClusters --name $ENV:adsExtensionName --query id -o tsv)
az customlocation create --resource-group $ENV:resourceGroup --name $ENV:clName --namespace $ENV:namespace --host-resource-id $ENV:hostClusterId --cluster-extension-ids $ENV:extensionId
```

---

## <a name="validate--the-custom-location-is-created"></a>사용자 지정 위치가 생성되었는지 확인

터미널에서 아래 명령을 실행하여 사용자 지정 위치를 나열하고 **프로비저닝 상태** 가 성공으로 표시되는지 확인합니다.

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

확장 및 사용자 지정 위치가 생성되었으면 다음과 같이 Azure Arc 데이터 컨트롤러를 배포합니다.

```azurecli
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --profile-name <profile name>  --auto-upload-logs true --auto-upload-metrics true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --profile-name azure-arc-aks-premium-storage  --auto-upload-logs true --auto-upload-metrics true --custom-location mycustomlocation
```

사용자 지정 구성 템플릿을 사용하여 Azure Arc 데이터 컨트롤러를 만들려면 [사용자 지정 구성 프로필 만들기](create-custom-configuration-template.md)에 설명된 단계를 따르고 다음과 같이 파일 경로를 제공합니다.


```azurecli
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --auto-upload-metrics true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --auto-upload-metrics true --custom-location mycustomlocation
```

## <a name="monitor-the-status-of-azure-arc-data-controller-deployment"></a>Azure Arc 데이터 컨트롤러 배포 상태 모니터

클러스터에서 Arc 데이터 컨트롤러의 배포 상태는 다음과 같이 모니터링할 수 있습니다.

```console
kubectl get datacontrollers --name arc
```

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)

[Azure Arc에서 Azure SQL Managed Instance 만들기](create-sql-managed-instance.md)