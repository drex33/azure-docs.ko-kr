---
title: 지역 간 Azure Synapse Analytics 작업 영역 이동
description: 이 문서에서는 Azure Synapse Analytics 작업 영역을 한 지역에서 다른 지역으로 이동하는 방법을 설명합니다.
author: phanir
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 08/16/2021
ms.author: phanir
ms.reviewer: jrasnick
ms.openlocfilehash: 6ad246eefb86c31291d2a9745c6f77e276701744
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214088"
---
# <a name="move-an-azure-synapse-analytics-workspace-from-one-region-to-another"></a>지역 간 Azure Synapse Analytics 작업 영역 이동

이 문서는 Azure Synapse Analytics 작업 영역을 한 Azure 지역에서 다른 지역으로 이동하는 방법을 보여주는 단계별 가이드입니다.

> [!NOTE]
> 이 문서의 단계는 작업 영역을 실제로 이동하지 않습니다. 이 단계는 원본 지역의 Azure Synapse Analytics 전용 SQL 풀 백업 및 아티팩트를 사용하여 새 지역에 새 작업 영역을 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

- 원본 지역 Azure Synapse 작업 영역을 Azure DevOps 또는 GitHub로 통합합니다. 자세한 내용은 [Synapse Studio의 소스 제어](cicd/source-control.md)를 참조하세요.
- 스크립트가 실행되는 서버에 [Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-6.3.0&preserve-view=true) 및 [Azure CLI](/cli/azure/install-azure-cli) 모듈을 설치합니다.
- Azure Machine Learning, Azure Storage 및 Azure Private Link 허브와 같은 모든 종속 서비스를 대상 지역에서 다시 생성하거나 서비스가 지역 이동을 지원하는 경우 대상 지역으로 이동해야 합니다. 
- Azure Storage를 다른 지역으로 이동합니다. 자세한 내용은 [Azure Storage 계정을 다른 지역으로 이동](../storage/common/storage-account-move.md)을 참조하세요.
- 전용 SQL 풀 이름과 Apache Spark 풀 이름이 원본 지역 및 대상 지역 작업 영역에서 동일한지 확인합니다.

## <a name="scenarios-for-a-region-move"></a>지역 이동 시나리오

- **새 규정 준수 요구 사항**: 조직은 데이터와 서비스를 새 규정 준수 요구 사항의 일부로 동일한 지역에 배치해야 합니다.
- **새 Azure 지역의 가용성**: 새 Azure 지역을 사용할 수 있고 작업 영역 및 기타 Azure 리소스를 새로 사용 가능한 Azure 지역으로 이동하기 위한 프로젝트 또는 비즈니스 요구 사항이 있는 시나리오입니다.
- **잘못된 지역이 선택되었습니다.** : Azure 리소스를 만들 때 잘못된 지역이 선택되었습니다.

## <a name="steps-to-move-an-azure-synapse-workspace-to-another-region"></a>Azure Synapse 작업 영역을 다른 지역으로 이동하는 단계

Azure Synapse 작업 영역을 한 지역에서 다른 지역으로 이동하는 것은 다단계 프로세스입니다. 대략적인 단계는 다음과 같습니다.

1. 원본 지역 작업 영역에 사용된 구성과 동일한 구성을 사용하여 대상 지역에서 새 Azure Synapse 작업 영역과 Spark 풀을 만듭니다.
1. 복원 지점 또는 지역 백업을 사용하여 전용 SQL 풀을 대상 지역으로 복원합니다.
1. 새 논리 SQL Server에 필요한 모든 로그인을 다시 만듭니다.
1. 서버리스 SQL 풀과 Spark 풀 데이터베이스 및 개체를 만듭니다.
1. Azure DevOps 릴리스 파이프라인을 사용하여 아티팩트를 배포하는 경우 Azure DevOps 서비스 주체를 Azure Synapse RBAC(역할 기반 액세스 제어) Synapse Artifact Publisher 역할에 추가합니다.
1. Azure DevOps 릴리스 파이프라인에서 대상 지역 Azure Synapse 작업 영역으로 코드 아티팩트(SQL 스크립트, Notebooks), 연결된 서비스, 파이프라인, 데이터 세트, Spark 작업 정의 트리거 및 자격 증명을 배포합니다.
1. Azure AD(Azure Active Directory) 사용자 또는 그룹을 Azure Synapse RBAC 역할에 추가합니다. 관리 ID를 사용하여 인증하는 경우 Storage Blob 참가자에게 Azure Storage 및 Azure Key Vault의 SA-MI(시스템 할당 관리 ID)에 대한 액세스 권한을 부여합니다.
1. 서버리스 SQL 풀을 사용하여 쿼리할 데이터가 있는 스토리지 계정이나 기본 연결된 스토리지의 필수 Azure AD 사용자에게 Storage Blob 읽기 권한자 또는 Storage Blob 참가자 역할을 부여합니다.
1. SHIR(자체 호스팅 통합 런타임) 다시 만들기
1. 대상 Azure Synapse 작업 영역에서 필요한 모든 라이브러리 및 jar을 수동으로 업로드합니다.
1. 작업 영역이 관리형 가상 네트워크에 배포된 경우 모든 관리형 프라이빗 엔드포인트를 만듭니다.
1. 대상 지역에서 새 작업 영역을 테스트하고 원본 지역 작업 영역을 가리키는 모든 DNS 항목을 업데이트합니다.
1. 원본 작업 영역에서 만든 프라이빗 엔드포인트 연결이 있는 경우 대상 지역 작업 영역에 프라이빗 엔드포인트 연결 하나를 만듭니다.
1. 철저히 테스트한 후 원본 지역에서 작업 영역을 삭제하고 모든 연결을 대상 지역 작업 영역으로 경로 지정할 수 있습니다.
## <a name="prepare"></a>준비
## <a name="step-1-create-an-azure-synapse-workspace-in-a-target-region"></a>1단계: 대상 지역에 Azure Synapse 작업 영역 만들기

이 섹션에서는 Azure PowerShell, Azure CLI 및 Azure Portal 사용하여 Azure Synapse 작업 영역을 만듭니다. PowerShell 스크립트 및 CLI 스크립트의 일부로 작업 영역에 대한 기본 스토리지로 사용할 Azure Data Lake Storage Gen2 계정과 리소스 그룹을 만듭니다. 배포 프로세스를 자동화하려면 DevOps 릴리스 파이프라인에서 이러한 PowerShell 또는 CLI 스크립트를 호출합니다.

### <a name="azure-portal"></a>Azure portal
Azure Portal에서 작업 영역을 만들려면 [빠른 시작: Synapse 작업 영역 만들기](quickstart-create-workspace.md)의 단계를 수행합니다.

### <a name="azure-powershell"></a>Azure PowerShell 
다음 스크립트는 New-AzResourceGroup 및 New-AzSynapseWorkspace cmdlet을 사용하여 리소스 그룹 및 Azure Synapse 작업 영역을 만듭니다.

#### <a name="create-a-resource-group"></a>리소스 그룹 만들기

```powershell
$storageAccountName= "<YourDefaultStorageAccountName>"
$resourceGroupName="<YourResourceGroupName>"
$regionName="<YourTargetRegionName>"
$containerName="<YourFileSystemName>" # This is the file system name
$workspaceName="<YourTargetRegionWorkspaceName>"

$sourcRegionWSName="<Your source region workspace name>"
$sourceRegionRGName="<YourSourceRegionResourceGroupName>"
$sqlUserName="<SQLUserName>"
$sqlPassword="<SQLStrongPassword>"

$sqlPoolName ="<YourTargetSQLPoolName>" #Both Source and target workspace SQL pool name will be same
$sparkPoolName ="<YourTargetWorkspaceSparkPoolName>"
$sparkVersion="2.4"

New-AzResourceGroup -Name $resourceGroupName -Location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정 만들기

```powershell
#If the Storage account is already created, then you can skip this step.
New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location  $regionName `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $true 
```


#### <a name="create-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역 만들기

```powershell
$password = ConvertTo-SecureString $sqlPassword -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ($sqlUserName, $password)

New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds 
```

관리형 가상 네트워크를 사용하여 작업 영역을 만들려면 스크립트에 추가 매개 변수 "ManagedVirtualNetwork"를 추가합니다. 사용 가능한 옵션에 대한 자세한 내용은 [관리형 가상 네트워크 구성](/powershell/module/az.synapse/new-azsynapsemanagedvirtualnetworkconfig?view=azps-6.3.0&preserve-view=true)을 참조하세요.


```powershell
#Creating a managed virtual network configuration
$config = New-AzSynapseManagedVirtualNetworkConfig -PreventDataExfiltration -AllowedAadTenantIdsForLinking ContosoTenantId 

#Creating an Azure Synapse workspace
New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds `
                              -ManagedVirtualNetwork $config
```


### <a name="azure-cli"></a>Azure CLI

이 Azure CLI 스크립트는 리소스 그룹, Data Lake Storage Gen2 계정 및 파일 시스템을 만듭니다. 그런 다음 Azure Synapse 작업 영역을 만듭니다.

#### <a name="create-a-resource-group"></a>리소스 그룹 만들기

```azurecli
az group create --name $resourceGroupName --location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정 만들기

```azurecli
# Checking if name is not used only then creates it.

$StorageAccountNameAvailable=(az storage account check-name --name $storageAccountName --subscription $subscriptionId | ConvertFrom-Json).nameAvailable

if($StorageAccountNameAvailable)
{
Write-Host "Storage account Name is available to be used...creating storage account"

#Creating an Data Lake Storage Gen2 account
$storgeAccountProvisionStatus=az storage account create `
  --name $storageAccountName `
  --resource-group $resourceGroupName `
  --location $regionName `
  --sku Standard_GRS `
  --kind StorageV2 `
  --enable-hierarchical-namespace $true

($storgeAccountProvisionStatus| ConvertFrom-Json).provisioningState
}
else
{
    Write-Host "Storage account Name is NOT available to be used...use another name --    exiting the script..."
    EXIT
}

#Creating a container in a Data Lake Storage Gen2 account

$key=(az storage account keys list -g $resourceGroupName -n $storageAccountName|ConvertFrom-Json)[0].value

$fileShareStatus=(az storage share create --account-name $storageAccountName --name $containerName --account-key $key)

if(($fileShareStatus|ConvertFrom-Json).created -eq "True")
{
      Write-Host f"Successfully created the fileshare - '$containerName'"
}
```


#### <a name="create-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역 만들기

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $containerName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName
```
관리형 가상 네트워크를 사용하도록 설정하려면 이전 스크립트에 매개 변수 `--enable-managed-virtual-network`를 포함합니다. 자세한 옵션은 [작업 영역 관리형 가상 네트워크](/cli/azure/synapse/workspace?view=azure-cli-latest&preserve-view=true)를 참조하세요.

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $FileShareName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName `
  --enable-managed-virtual-network true `
  --allowed-tenant-ids "Contoso"
```

## <a name="step-2-create-an-azure-synapse-workspace-firewall-rule"></a>2단계: Azure Synapse 작업 영역 방화벽 규칙 만들기 
작업 영역을 만든 후 작업 영역에 대한 방화벽 규칙을 추가합니다. IP를 특정 범위로 제한합니다. PowerShell 또는 CLI를 사용하거나 Azure Portal에서 방화벽을 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal
방화벽 옵션을 선택하고 다음 스크린샷에 표시된 대로 IP 주소 범위를 추가합니다.

:::image type="icon" source="media/how-to-move-workspace-from-one-region-to-another/firewall.png" border="false":::


### <a name="azure-powershell"></a>Azure PowerShell 
시작 및 끝 IP 주소를 지정하여 방화벽 규칙을 추가하려면 다음 PowerShell 명령을 실행합니다. 요구 사항에 따라 IP 주소 범위를 업데이트합니다.


```powershell
$WorkspaceWeb = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Web
$WorkspaceDev = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Dev

# Adding firewall rules
$FirewallParams = @{
  WorkspaceName = $workspaceName
  Name = 'Allow Client IP'
  ResourceGroupName = $resourceGroup
  StartIpAddress = "0.0.0.0"
  EndIpAddress = "255.255.255.255"
}
New-AzSynapseFirewallRule @FirewallParams
```

다음 스크립트를 실행하여 작업 영역의 관리 ID SQL 제어 설정을 업데이트합니다.

```powershell 
Set-AzSynapseManagedIdentitySqlControlSetting -WorkspaceName $workspaceName -Enabled $true 
```

### <a name="azure-cli"></a>Azure CLI


```azurecli
az synapse workspace firewall-rule create --name allowAll --workspace-name $workspaceName  `
--resource-group $resourceGroupName --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
 
다음 스크립트를 실행하여 작업 영역의 관리 ID SQL 제어 설정을 업데이트합니다.

```azurecli
az synapse workspace managed-identity grant-sql-access `
--workspace-name $workspaceName --resource-group $resourceGroupName
```


## <a name="step-3-create-an-apache-spark-pool"></a>3단계: Apache Spark 풀 만들기

원본 지역 작업 영역에서 사용된 구성과 동일한 구성으로 Spark 풀을 만듭니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal Spark 풀을 만들려면 [빠른 시작: Azure Portal을 사용하여 새 서버리스 Apache Spark 풀 만들기](quickstart-create-apache-spark-pool-portal.md)를 참조하세요.


[빠른 시작: Azure Portal을 사용하여 새 서버리스 Apache Spark 풀 만들기](quickstart-create-apache-spark-pool-studio.md)의 단계에 따라 Synapse Studio에서 Spark 풀을 만들 수도 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

다음 스크립트는 작업자 2명과 드라이버 노드 1개가 있는 Spark 풀을 만듭니다. 원본 지역 작업 영역 Spark 풀과 일치하도록 값을 업데이트합니다.


```powershell
#Creating a Spark pool with 3 nodes (2 worker + 1 driver) and a small cluster size with 4 cores and 32 GB RAM. 
New-AzSynapseSparkPool `
    -WorkspaceName  $workspaceName `
    -Name $sparkPoolName `
    -NodeCount 3 `
    -SparkVersion $sparkVersion `
    -NodeSize Small
```
 
### <a name="azure-cli"></a>Azure CLI

```azurecli
az synapse spark pool create --name $sparkPoolName --workspace-name $workspaceName --resource-group $resourceGroupName `
--spark-version $sparkVersion --node-count 3 --node-size small
```
## <a name="move"></a>이동
## <a name="step-4-restore-a-dedicated-sql-pool"></a>4단계: 전용 SQL 풀 복원 

### <a name="restore-from-geo-redundant-backups"></a>지역 중복 백업에서 복원

Azure Portal 및 PowerShell을 사용하여 지역 백업에서 전용 SQL 풀을 복원하려면 [Azure Synapse Analytics에서 전용 SQL 풀 지역 복원](sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup.md)을 참조하세요.


### <a name="restore-by-using-restore-points-from-the-source-region-workspace-dedicated-sql-pool"></a>원본 지역 작업 영역 전용 SQL 풀의 복원 지점을 사용하여 복원

원본 지역 작업 영역 전용 SQL 풀의 복원 지점을 사용하여 전용 SQL 풀을 대상 지역 작업 영역으로 복원합니다. Azure Portal, Synapse Studio 또는 PowerShell을 사용하여 복원 지점에서 복원할 수 있습니다. 원본 지역에 액세스할 수 없는 경우 이 옵션을 사용하여 복원할 수 없습니다.

#### <a name="synapse-studio"></a>Synapse Studio

Synapse Studio에서 *복원 지점* 을 사용하여 구독의 모든 작업 영역에서 전용 SQL 풀을 복원할 수 있습니다. 다음 스크린샷에서 보듯이 전용 SQL 풀을 만드는 동안 **추가 설정** 에서 **복원 지점** 을 선택하고 작업 영역을 선택합니다. 사용자 정의 복원 지점을 만든 경우 해당 복원 지점을 사용하여 SQL 풀을 복원합니다. 그렇지 않으면 최신 자동 복원 지점을 선택할 수 있습니다.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/restore-sql-pool.png" alt-text="SQL 풀 복원":::


#### <a name="azure-powershell"></a>Azure PowerShell

다음 PowerShell 스크립트를 실행하여 작업 영역을 복원합니다. 이 스크립트는 원본 작업 영역 전용 SQL 풀의 최신 복원 지점을 사용하여 대상 작업 영역에서 SQL 풀을 복원합니다. 스크립트를 실행하기 전에 성능 수준을 DW100c에서 필요한 값으로 업데이트합니다. 

> [!IMPORTANT]
> 전용 SQL 풀 이름은 두 작업 영역에서 동일해야 합니다.


```powershell
#Getting the restore points
$restorePoint=Get-AzSynapseSqlPoolRestorePoint -WorkspaceName $sourceRegionWSName -Name $sqlPoolName|Sort-Object  -Property RestorePointCreationDate -Descending `
                                                                                         | SELECT RestorePointCreationDate -ExpandProperty  RestorePointCreationDate -First 1
```
 
 

```powershell
<#
Transform Synapse SQL pool resource ID to SQL database ID because currently the command only accepts the SQL database ID. 
For example: /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Sql/servers/<WorkspaceName>/databases/<DatabaseName>
#>
$pool = Get-AzSynapseSqlPool -ResourceGroupName $sourceRegionRGName -WorkspaceName $sourcRegionWSName -Name $sqlPoolName
$databaseId = $pool.Id `
    -replace "Microsoft.Synapse", "Microsoft.Sql" `
    -replace "workspaces", "servers" `
    -replace "sqlPools", "databases" 
 

$restoredPool = Restore-AzSynapseSqlPool -FromRestorePoint `
                                         -RestorePoint $restorePoint `
                                         -TargetSqlPoolName $sqlPoolName `
                                         -ResourceGroupName $resourceGroupName `
                                         -WorkspaceName $workspaceName `
                                         -ResourceId $databaseId `
                                         -PerformanceLevel DW100c -AsJob


#Tracks the status of the restore 

Get-Job | Where-Object Command -In ("Restore-AzSynapseSqlPool") | `
Select-Object Id,Command,JobStateInfo,PSBeginTime,PSEndTime,PSJobTypeName,Error |Format-Table
```
전용 SQL 풀을 복원한 후 Azure Synapse에 모든 SQL 로그인을 만듭니다. 모든 로그인을 만들려면 [로그인 만들기](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true)의 단계를 수행합니다.

## <a name="step-5-create-a-serverless-sql-pool-spark-pool-database-and-objects"></a>5단계: 서버리스 SQL 풀, Spark 풀 데이터베이스 및 개체 만들기

서버리스 SQL 풀 데이터베이스 및 Spark 풀은 백업 및 복원할 수 없습니다. 가능한 해결 방법을 통해 다음을 수행할 수 있습니다.

1. 필요한 모든 Spark 풀, 서버리스 SQL 풀 데이터베이스, 테이블, 역할 및 모든 역할 할당이 있는 사용자를 다시 만드는 코드가 포함된 Notebook 및 SQL 스크립트를 만듭니다. 이러한 아티팩트를 Azure DevOps 또는 GitHub로 체크 인합니다.
1. 스토리지 계정 이름이 변경되면 코드 아티팩트에서 올바른 스토리지 계정 이름을 가리키는지 확인합니다.
1. 특정 시퀀스에서 이러한 코드 아티팩트를 호출하는 파이프라인을 만듭니다. 이러한 파이프라인이 대상 지역 작업 영역에서 실행되면 Spark SQL 데이터베이스, 서버리스 SQL 풀 데이터베이스, 외부 데이터 원본, 뷰, 역할 및 사용자와 권한이 대상 지역 작업 영역에 생성됩니다.
1. 원본 지역 작업 영역을 Azure DevOps와 통합하면 이러한 코드 아티팩트도 리포지토리의 일부가 됩니다. 나중에 6단계에서 설명한 대로 DevOps 릴리스 파이프라인을 사용하여 이러한 코드 아티팩트를 대상 지역 작업 영역에 배포할 수 있습니다.
1. 대상 지역 작업 영역에서 이러한 파이프라인을 수동으로 트리거합니다.

## <a name="step-6-deploy-artifacts-and-pipelines-by-using-cicd"></a>6단계: CI/CD를 사용하여 아티팩트 및 파이프라인 배포 

 Azure Synapse 작업 영역을 Azure DevOps 또는 GitHub와 통합하는 방법 및 아티팩트를 대상 지역 작업 영역에 배포하는 방법을 알아보려면 [Azure Synapse 작업 영역에 대한 CI/CD(연속 통합 및 지속적인 업데이트)](cicd/continuous-integration-delivery.md)의 단계를 수행합니다. 

작업 영역이 Azure DevOps와 통합되면 이름이 workspace_publish인 분기를 찾을 수 있습니다. 이 분기에는 Notebooks, SQL 스크립트, 데이터 세트, 연결된 서비스, 파이프라인, 트리거 및 Spark 작업 정의와 같이 아티팩트에 대한 정의가 포함된 작업 영역 템플릿이 포함되어 있습니다.

Azure DevOps 리포지토리의 이 스크린샷은 아티팩트 및 기타 구성 요소에 대한 작업 영역 템플릿 파일을 보여줍니다.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/devops-repo-workspace-publish.png" alt-text="선택한 작업 영역-게시를 보여주는 스크린샷입니다.":::

다음 스크린샷과 같이 작업 영역 템플릿을 사용하면 Azure DevOps 릴리스 파이프라인을 사용하여 작업 영역에 아티팩트 및 파이프라인을 배포할 수 있습니다.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/release-pipeline.png" alt-text="synapse-릴리스-파이프라인을 보여주는 스크린샷입니다.":::

작업 영역이 GitHub 또는 Azure DevOps와 통합되지 않은 경우 사용자 지정 PowerShell 또는 Azure CLI 스크립트를 수동으로 다시 만들거나 써서 대상 지역 작업 영역에서 모든 아티팩트, 파이프라인, 연결된 서비스, 자격 증명, 트리거 및 Spark 정의를 배포해야 합니다.


> [!NOTE]
> 이 프로세스를 수행하려면 원본 지역 작업 영역에서 Spark 및 서버리스 SQL 풀, 개체 및 역할에 대한 변경 내용을 포함하도록 계속 파이프라인 및 코드 아티팩트를 업데이트해야 합니다.

## <a name="step-7-create-a-shared-integration-runtime"></a>7단계: 공유 통합 런타임 만들기

SHIR을 만들려면 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)의 단계를 따릅니다.

## <a name="step-8-assign-an-azure-role-to-managed-identity"></a>8단계: 관리 ID에 Azure 역할 할당

 기본 연결된 Data Lake Storage Gen2 계정에서 새 작업 영역의 관리 ID에 `Storage Blob Contributor` 액세스 권한을 할당합니다. 또한 인증에 SA-MI가 사용되는 다른 스토리지 계정에 대한 액세스 권한을 할당합니다. 필요한 모든 스토리지 계정에 대한 Azure AD 사용자 및 그룹에 `Storage Blob Contributor` 또는 `Storage Blob Reader` 액세스 권한을 할당합니다.

### <a name="azure-portal"></a>Azure portal
[작업 영역 관리 ID에 권한 부여](security/how-to-grant-workspace-managed-identity-permissions.md)의 단계에 따라 Storage Blob 데이터 참가자 역할을 작업 영역의 관리 ID에 할당합니다.

### <a name="azure-powershell"></a>Azure PowerShell
Storage Blob 데이터 참가자 역할을 작업 영역의 관리 ID에 할당합니다.

```powershell

$workSpaceIdentityObjectID= (Get-AzSynapseWorkspace -ResourceGroupName $resourceGroupName -Name $workspaceName).Identity.PrincipalId 
$scope = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Storage/storageAccounts/$($storageAccountName)"

# Adding Storage Blob Data Contributor to WS Managed Identity on the storage account. This errors out with the message New-AzRoleAssignment : Exception of type 'Microsoft.Rest.Azure.CloudException' was thrown.
# But it creates the required permissions on the storage account.
$roleAssignedforManagedIdentity=New-AzRoleAssignment -ObjectId $workSpaceIdentityObjectID `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope $scope -ErrorAction SilentlyContinue
```


### <a name="azure-cli"></a>Azure CLI

```azurecli
# Getting Role name
$roleName =az role definition list --query "[?contains(roleName, 'Storage Blob Data Contributor')].{roleName:roleName}" --output tsv

#Getting resource id for storage account
$scope= (az storage account show --name $storageAccountName|ConvertFrom-Json).id

#Getting principal ID for WS Managed Identity
$workSpaceIdentityObjectID=(az synapse workspace show --name $workspaceName --resource-group $resourceGroupName|ConvertFrom-Json).Identity.PrincipalId 
                    
# Adding Storage Blob Data Contributor Azure role to SA-MI
az role assignment create --assignee $workSpaceIdentityObjectID `
--role $roleName `
--scope $scope
```

## <a name="step-9-assign-azure-synapse-rbac-roles"></a>9단계: Azure Synapse RBAC 역할 할당

별도의 역할 및 권한으로 대상 작업 영역에 액세스해야 하는 모든 사용자를 추가합니다. 다음 PowerShell 및 CLI 스크립트는 대상 지역 작업 영역의 Synapse 관리자 역할에 Azure AD 사용자를 추가합니다. 모든 Azure Synapse RBAC 역할 이름을 가져오려면 [Azure Synapse RBAC 역할](security/synapse-workspace-synapse-rbac-roles.md)을 참조하세요.

### <a name="synapse-studio"></a>Synapse Studio

Synapse Studio에서 Azure Synapse RBAC 할당을 추가하거나 삭제하려면 [Synapse Studio에서 Azure Synapse RBAC 역할 할당을 관리하는 방법](security/how-to-manage-synapse-rbac-role-assignments.md)의 단계를 따릅니다.


### <a name="azure-powershell"></a>Azure PowerShell

다음 PowerShell 스크립트는 Azure AD 사용자 또는 그룹에 Synapse 관리자 역할 할당을 추가합니다. 다음 명령에서 -RoleDefinitionName 대신 -RoleDefinitionId를 사용하여 사용자를 작업 영역에 추가할 수 있습니다.

```powershell
# Add the Synapse RBAC assignment. Use the objectId of the Azure AD user or group you want to assign.
New-AzSynapseRoleAssignment `
   -WorkspaceName $workspaceName  `
   -RoleDefinitionName "Synapse Administrator" `
   -ObjectId 1c02d2a6-ed3d-46ec-b578-6f36da5819c6

# Check if user is added to the access control by running this command.
Get-AzSynapseRoleAssignment -WorkspaceName $workspaceName  
```

원본 지역 작업 영역에서 ObjectId 및 RoleIds를 가져오려면 Get-AzSynapseRoleAssignment 명령을 실행합니다. 대상 지역 작업 영역의 Azure AD 사용자 또는 그룹에 동일한 Azure Synapse RBAC 역할을 할당합니다.

-ObjectId를 매개 변수로 사용하는 대신 사용자의 이메일 주소 또는 사용자 계정 이름을 제공하는 -SignInName을 사용할 수도 있습니다. 사용 가능한 옵션에 대한 자세한 내용은 [Azure Synapse RBAC - PowerShell cmdlet](/powershell/module/az.synapse/new-azsynapseroleassignment?view=azps-6.3.0&preserve-view=true)을 참조하세요. 

### <a name="azure-cli"></a>Azure CLI

```azurecli
#Get the Object Id of the user and assign the required Azure Synapse RBAC permissions to the Azure AD user. You can provide the email address of the user (username@contoso.com) for the --assignee parameter.
az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Administrator" --assignee adasdasdd42-0000-000-xxx-xxxxxxx

az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Contributor" --assignee "user1@contoso.com"

```

사용 가능한 옵션에 대한 자세한 내용은 [Azure Synapse RBAC - CLI](/cli/azure/synapse/role/assignment?view=azure-cli-latest&preserve-view=true)를 참조하세요. 

## <a name="step-10-upload-workspace-packages"></a>10단계: 작업 영역 패키지 업로드

필요한 모든 작업 영역 패키지를 새 작업 영역에 업로드합니다. 작업 영역 패키지를 업로드하는 프로세스를 자동화하려면 [Microsoft Azure Synapse Analytics Artifacts 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Analytics.Synapse.Artifacts/1.0.0-preview.10)를 참조하세요.

## <a name="step-11-permissions"></a>11단계: 권한
    
대상 지역 Azure Synapse 작업 영역에 대한 액세스 제어를 설정하려면 [Azure Synapse 작업 영역에 대한 액세스 제어를 설정하는 방법](security/how-to-set-up-access-control.md)의 단계를 따릅니다. 


## <a name="step-12-create-managed-private-endpoints"></a>12단계: 관리형 프라이빗 엔드포인트 만들기

대상 지역 작업 영역의 원본 지역 작업 영역에서 관리형 프라이빗 엔드포인트를 다시 만들려면 [데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](security/how-to-create-managed-private-endpoints.md)를 참조하세요. 

## <a name="discard"></a>취소
대상 지역 작업 영역을 삭제하려면 대상 지역 작업 영역을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 리소스 그룹으로 이동하여 작업 영역을 선택하고 리소스 그룹 페이지의 위쪽에서 삭제를 선택합니다.

## <a name="clean-up"></a>정리
변경 내용을 커밋하고 작업 영역의 이동을 완료하려면 대상 지역의 작업 영역을 테스트한 후 원본 지역 작업 영역을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 원본 지역 작업 영역이 있는 리소스 그룹으로 이동하여 작업 영역을 선택하고 리소스 그룹 페이지의 위쪽에서 삭제를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse 관리형 가상 네트워크](security/synapse-workspace-managed-vnet.md)에 대해 자세히 알아봅니다.
- [Azure Synapse 관리형 프라이빗 엔드포인트](security/synapse-workspace-managed-private-endpoints.md)에 대해 자세히 알아봅니다.
- [제한된 네트워크에서 작업 영역 리소스에 연결](security/how-to-connect-to-workspace-from-restricted-network.md)하는 방법에 대해 자세히 알아봅니다.
