---
title: ARG(Azure Resource Graph)를 사용하여 백업 쿼리
description: ARG(Azure Resource Group)를 사용하여 Azure 리소스에 대한 백업 정보를 쿼리하는 방법에 대해 자세히 알아봅니다.
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: e9caa1d4d8de77efe9acb31c0cec3be5741b69c7
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236051"
---
# <a name="query-your-backups-using-azure-resource-graph-arg"></a>ARG(Azure Resource Graph)를 사용하여 백업 쿼리

[ARG(Azure Resource Graph)](../governance/resource-graph/overview.md)를 사용하여 추가 비용 없이 Azure 리소스에 대한 백업 정보를 쿼리할 수 있습니다. ARG는 Azure 리소스 관리를 확장하도록 설계된 Azure 서비스입니다. 지정된 구독 집합에서 대규모로 쿼리할 수 있는 기능을 통해 효율적인 리소스 탐색을 제공하기 위한 목적을 지니고 있습니다. 다음은 ARG를 사용하여 백업 메타데이터를 쿼리할 때의 핵심적인 이점입니다.

- 복잡한 필터링, 그룹화 및 리소스 속성별 정렬을 사용하여 대규모로 리소스를 쿼리할 수 있습니다.
- 진행 중인 백업 작업을 포함하여 백업에 대한 실시간 정보를 얻을 수 있습니다.
- Azure Virtual Machines 및 스토리지 계정과 같은 관련 Azure 리소스에 대한 유용한 정보와 백업 관련 데이터를 조인할 수 있습니다.

## <a name="getting-started"></a>시작

ARG를 사용하여 백업 쿼리를 시작하려면 다음 단계를 수행합니다.

1. Azure Portal에서 _Resource Graph 탐색기_ 를 검색합니다. Resource Graph 탐색기를 선택하면 ARG 쿼리 편집기로 리디렉션됩니다.
    
    ![Azure 리소스 그룹 검색](./media/query-backups-using-azure-resource-graph/search-resource-graph-explorer.png)

    왼쪽 창에는 쿼리에 사용할 수 있는 모든 테이블(및 관련 스키마)이 표시됩니다.
    
    - **RecoveryServicesResources** 테이블에는 작업 세부 정보, 백업 인스턴스 세부 정보 등의 백업 관련 기록이 대부분 포함되어 있습니다.  등
    - **Resources** 테이블에는 Recovery Services 자격 증명 모음, Azure Virtual Machines, 스토리지 계정 등과 같은 모든 최상위 Azure 리소스에 대한 정보가 포함되어 있습니다.

    ![쿼리에 사용 가능한 테이블과 연관된 스키마](./media/query-backups-using-azure-resource-graph/tables-and-associated-schemas.png)

1. 이러한 테이블의 데이터를 탐색하려면 쿼리 편집기에서 **Kusto** 쿼리를 작성하고 **쿼리 실행** 을 클릭합니다.

    이러한 쿼리의 출력은 **Resource Graph 탐색기** 에서 CSV로 다운로드할 수 있습니다. 또한 [PowerShell](../governance/resource-graph/first-query-powershell.md), [CLI](../governance/resource-graph/first-query-azurecli.md), 또는 [SDK](../governance/resource-graph/first-query-python.md)와 같이 ARG에서 지원하는 모든 자동화 클라이언트를 사용하여 사용자 지정 자동화에서 이러한 쿼리를 사용할 수 있습니다. 또한 ARG를 데이터 원본으로 사용하여 Azure Portal에서 [사용자 지정 통합 문서](../azure-monitor/visualize/workbooks-overview.md)를 만들 수 있습니다.

>[!NOTE] 
>- 최대 14일 이전의 백업/복원 작업은 쿼리용 ARG에서 사용할 수 있습니다. 기록 레코드를 쿼리하려면 **Azure Monitor 로그** 를 사용하는 것을 권장합니다.
>- ARG를 사용하면 적절한 RBAC 권한이 있는 리소스를 쿼리할 수 있습니다.

## <a name="sample-queries"></a>샘플 쿼리

다음은 사용자 지정 대시보드와 자동화에서 사용할 수 있는 백업 데이터에 대한 몇 가지 샘플 ARG 쿼리입니다.

### <a name="list-all-azure-vms-that-have-been-configured-for-backup"></a>백업용으로 구성된 모든 Azure VM 나열

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupInstances',split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend dataSourceType = case(type=~'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupInstances',properties.dataSourceSetInfo.datasourceType,'--')
| extend friendlyName = properties.friendlyName
| extend dsResourceGroup = split(split(properties.dataSourceInfo.resourceID, '/resourceGroups/')[1],'/')[0]
| extend dsSubscription = split(split(properties.dataSourceInfo.resourceID, '/subscriptions/')[1],'/')[0]
| extend lastRestorePoint = properties.lastRecoveryPoint
| extend primaryLocation = properties.dataSourceInfo.resourceLocation
| extend policyName = case(type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.policyName, type =~ 'microsoft.dataprotection/backupVaults/backupInstances', properties.policyInfo.name, '--')
| extend protectionState = properties.currentProtectionState
| where protectionState in~ ('ConfiguringProtection','ProtectionConfigured','ConfiguringProtectionFailed','ProtectionStopped','SoftDeleted','ProtectionError')
| where (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('AzureIaasVM')) //add the relevant subscription ids you wish to query to this line

```

### <a name="list-all-backup-jobs-on-azure-databases-for-postgresql-servers-in-the-last-one-week"></a>지난 1주 동안 Azure Database for PostgreSQL 서버의 모든 백업 작업 나열

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/backupVaults/backupJobs')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
| extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
| extend backupInstanceName = properties.backupInstanceId
| extend dsResourceGroup = split(split(properties.dataSourceId, '/resourceGroups/')[1],'/')[0]| extend dsSubscription = split(split(properties.dataSourceId, '/subscriptions/')[1],'/')[0]
| extend status = properties.status
| extend dataSourceId = properties.dataSourceId
| extend primaryLocation = properties.dataSourceLocation
| extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'),startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration
| project id, name, friendlyName, resourceGroup, vaultName, dataSourceType, operation, jobStatus, startTime, duration, backupInstanceName, dsResourceGroup, dsSubscription, status, primaryLocation, dataSourceId
| where  (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases')) and (status in~ ('Started','InProgress','Succeeded','Completed','Failed','CompletedWithWarnings')) and (operation in~ ('adhocBackup','backup')) //add the relevant subscription ids you wish to query to this line
| where (startTime >= ago(7d))

```

### <a name="list-all-azure-vms-that-have-not-been-configured-for-backup"></a>백업하도록 구성되지 않은 모든 Azure VM 나열

```kusto
Resources
| where type in~ ('microsoft.compute/virtualmachines','microsoft.classiccompute/virtualmachines') 
| extend resourceId=tolower(id) 
| join kind = leftouter ( RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| where properties.backupManagementType == "AzureIaasVM"
| project resourceId = tolower(tostring(properties.sourceResourceId)), backupItemid = id, isBackedUp = isnotempty(id) ) on resourceId 
| extend isProtected = isnotempty(backupItemid)
| where (isProtected == (0))
| project id,name,resourceGroup,location,tags

```

### <a name="list-all-backup-policies-used-for-azure-vms"></a>Azure VM에 사용되는 모든 백업 정책 나열

```kusto
RecoveryServicesResources
| where type == 'microsoft.recoveryservices/vaults/backuppolicies'
| extend vaultName = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', properties.backupManagementType,type == 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where datasourceType == 'AzureIaasVM'
```

### <a name="list-all-vms-associated-with-a-given-backup-policy"></a>지정된 백업 정책과 연결된 모든 VM 나열

```kusto
RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| project propertiesJSON = parse_json(properties)
| where propertiesJSON.backupManagementType == "AzureIaasVM"
| project VMID=propertiesJSON.sourceResourceId, PolicyID=propertiesJSON.policyId
| where PolicyID == "<ARM ID of the given policy>"
```

### <a name="list-all-backup-policies-used-for-azure-databases-for-postgresql-servers"></a>Azure Database for PostgreSQL 서버에 사용되는 모든 백업 정책 나열

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/BackupVaults/backupPolicies')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupPolicies', split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'microsoft.recoveryservices/vaults/backupPolicies', split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupPolicies', properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where (datasourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases'))

```

## <a name="next-steps"></a>다음 단계

[Azure Resource Graph에 대해 자세히 알아봅니다](../governance/resource-graph/overview.md)
