---
title: Azure Arc 데이터 컨트롤러 만들기 | 직접 연결 모드
description: 직접 연결 모드에서 데이터 컨트롤러를 만드는 방법을 설명합니다.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: fb1952c00b54fd4618d3c4d3a830d862eb50a439
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301763"
---
#  <a name="create-azure-arc-data-controller-in-direct-connectivity-mode-using-cli"></a>CLI를 사용하여 직접 연결 모드에서 Azure Arc 데이터 컨트롤러 만들기

이 문서에서는 이 기능의 현재 미리 보기 기간 동안 **직접** 연결 모드에서 CLI를 사용하여 Azure Arc 데이터 컨트롤러를 만드는 방법을 설명합니다. 


## <a name="complete-prerequisites"></a>필수 조건 완료

시작하기 전에 [데이터 컨트롤러 배포 - 직접 연결 모드 - 필수 조건](create-data-controller-direct-prerequisites.md)에서 필수 조건을 완료 했는지 확인합니다.

다음은 **직접** 연결 모드에서 Azure Arc 데이터 컨트롤러를 만드는 단계입니다.

1. Azure Arc 지원 데이터 서비스 확장 만들기 
1. 사용자 지정 위치를 만듭니다.
1. 데이터 컨트롤러를 만듭니다.

> [!NOTE]
> 현재 이 단계는 포털에서만 수행할 수 있습니다. 자세한 내용은 [릴리스 정보](release-notes.md)를 참조하세요. 

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Azure Arc 지원 데이터 서비스 확장 만들기

k8s-extension CLI를 사용하여 데이터 서비스 확장을 만듭니다.

### <a name="set-environment-variables"></a>환경 변수 설정

다음 단계에서 사용할 다음 환경 변수를 설정합니다.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
export AZDATA_LOGSUI_USERNAME=<username for Kibana dashboard>
export AZDATA_LOGSUI_PASSWORD=<password for Kibana dashboard>
export AZDATA_METRICSUI_USERNAME=<username for Grafana dashboard>
export AZDATA_METRICSUI_PASSWORD=<password for Grafana dashboard>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

### <a name="create-the-arc-data-services-extension"></a>Arc 데이터 서비스 확장 만들기

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>프라이빗 컨테이너 레지스트리 및 자격 증명을 사용하여 Azure Arc Data Services 확장 배포

프라이빗 리포지토리에서 배포하는 경우 아래 명령을 사용합니다.

```azurecli
az k8s-extension create -c "<connected cluster name>" -g "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

 예를 들면 다음과 같습니다.
```azurecli
az k8s-extension create -c "my-connected-cluster" -g "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> Arc 데이터 서비스 확장 설치를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Arc 데이터 서비스 확장이 생성되었는지 확인

포털에서 또는 Azure Arc 지원 Kubernetes 클러스터에 직접 연결하여 Azure Arc 지원 데이터 서비스 확장이 생성되었는지 확인할 수 있습니다. 

#### <a name="azure-portal"></a>Azure portal
1. Azure Portal에 로그인하고 Kubernetes 연결 된 클러스터 리소스가 있는 리소스 그룹을 찾습니다.
1. 확장이 배포된 Azure Arc 지원 kubernetes 클러스터(유형 = "Kubernetes - Azure Arc")를 선택합니다.
1. 왼쪽 탐색의 **설정** 에서 **확장** 을 선택합니다.
1. 앞서 만든 확장이 설치됨 상태로 표시됩니다.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="확장 대시보드":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. 터미널 창을 통해 Kubernetes 클러스터에 연결합니다.
1. 아래 명령을 실행하고 (1) 위에서 언급한 네임스페이스가 생성되었는지, (2) `bootstrapper` pod가 '실행 중' 상태인지 확인한 후 다음 단계를 진행합니다.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

예를 들어 다음 예제에서는 `arc` 네임스페이스에서 pod를 가져옵니다.

```console
#Example:
kubectl get pods -n arc
```

## <a name="retrieve-the-managed-identity-and-grant-roles"></a>관리 ID 검색 및 역할 부여

Arc 데이터 서비스 확장 생성 중에 생성되는 관리 ID는 사용량 및/또는 메트릭이 자동으로 업로드되려면 특정 역할을 할당해야 합니다.

### <a name="1-retrieve-managed-identity-of-the-arc-data-controller-extension"></a>(1) Arc 데이터 컨트롤러 확장의 관리 ID 검색

```powershell
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername> --cluster-type connectedClusters --name <name of extension> | convertFrom-json).identity.principalId
#Example
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | convertFrom-json).identity.principalId
```

### <a name="2-assign-role-to-the-managed-identity"></a>(2) 관리 ID에 역할 할당

아래 명령어를 실행하여 **기여자** 및 **모니터링 메트릭 게시자** 역할을 할당합니다.
```powershell
az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Contributor" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Monitoring Metrics Publisher" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>사용자 지정 위치 CLI 확장을 사용하여 사용자 지정 위치 만들기

사용자 지정 위치는 Kubernetes 클러스터의 네임스페이스에 해당하는 Azure 리소스입니다.  사용자 지정 위치는 Azure에서 리소스를 배포하는 대상으로 사용됩니다. [Azure Arc 지원 Kubernetes 설명서의 맨 위에 있는 사용자 지정 위치](../kubernetes/conceptual-custom-locations.md)에서 사용자 지정 위치에 대해 자세히 알아보세요.

### <a name="set-environment-variables"></a>환경 변수 설정

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"

$ENV:hostClusterId=(az connectedk8s show -g $ENV:resourceGroup -n $ENV:resourceName --query id -o tsv)
$ENV:extensionId=(az k8s-extension show -g $ENV:resourceGroup -c $ENV:resourceName --cluster-type connectedClusters --name $ENV:ADSExtensionName --query id -o tsv)

az customlocation create -g $ENV:resourceGroup -n $ENV:clName --namespace $ENV:clNamespace --host-resource-id $ENV:hostClusterId --cluster-extension-ids $ENV:extensionId
```

## <a name="validate--the-custom-location-is-created"></a>사용자 지정 위치가 생성되었는지 확인

터미널에서 아래 명령을 실행하여 사용자 지정 위치를 나열하고 **프로비저닝 상태** 가 성공으로 표시되는지 확인합니다.

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

확장 및 사용자 지정 위치가 생성되었으면 다음과 같이 Azure Arc 데이터 컨트롤러를 배포합니다.

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --profile-name <profile name>  --auto-upload-logs true --custom-location <name of custom location>
# Example
az arcdata dc create -n arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --profile-name azure-arc-aks-premium-storage  --auto-upload-logs true --custom-location mycustomlocation
```

사용자 지정 구성 템플릿을 사용하여 Azure Arc 데이터 컨트롤러를 만들려면 [사용자 지정 구성 프로필 만들기](create-custom-configuration-template.md)에 설명된 단계를 따르고 다음과 같이 파일 경로를 제공합니다.

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --custom-location mycustomlocation
```


## <a name="monitor-the-creation"></a>만들기 모니터링

Azure Portal 배포 상태에 배포가 성공적으로 완료된 것으로 표시되면 다음과 같이 클러스터에서 Arc 데이터 컨트롤러 배포의 상태를 확인할 수 있습니다.

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)

[Azure Arc에서 Azure SQL Managed Instance 만들기](create-sql-managed-instance.md)
