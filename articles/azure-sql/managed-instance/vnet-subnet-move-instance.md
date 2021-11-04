---
title: 관리되는 인스턴스를 다른 서브넷으로 이동
titleSuffix: Azure SQL Managed Instance
description: 장애 조치(failover) 중 짧은 가동 중지 시간(일반적으로 최대 10초)만 사용하여 Azure SQL Managed Instance 다른 서브넷으로 이동하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 09/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7adb9886bdeebc8aad7f8c200b21523e53651a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053606"
---
# <a name="move-azure-sql-managed-instance-across-subnets"></a>서브넷 간에 Azure SQL Managed Instance 이동
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance Azure [가상 네트워크](../../virtual-network/virtual-networks-overview.md)내의 전용 서브넷 내에 배포해야 합니다. 서브넷 내에 배포할 수 있는 관리되는 인스턴스 수는 서브넷의 크기(서브넷 범위)에 따라 달라집니다.

이 문서에서는 vCore 크기를 조정하거나 인스턴스 서비스 계층을 변경하는 것과 비슷하게 관리되는 인스턴스를 한 서브넷에서 다른 서브넷으로 이동하는 방법을 배분합니다. SQL Managed Instance 업데이트 종료 시 장애 조치(failover)로 인해 발생하는 짧은 가동 중지 시간(일반적으로 장기 실행 트랜잭션이 중단되더라도 최대 10초까지 지속됨)을 제외하고 이동 중에 사용할 수 있습니다. 

인스턴스를 다른 서브넷으로 이동하면 다음과 같은 가상 클러스터 작업이 트리거됩니다.
- 대상 서브넷은 가상 클러스터를 빌드하거나 크기를 조정합니다.
- 가상 클러스터는 원본 서브넷에서 제거되거나 조각 모음됩니다. 

인스턴스를 다른 서브넷으로 이동하기 전에 다음 개념을 숙지하는 것이 좋습니다. 
- [Azure SQL Managed Instance 필요한 서브넷 크기 및 범위를 결정합니다.](vnet-subnet-determine-size.md)
- 인스턴스를 [새 서브넷으로](virtual-network-subnet-create-arm-template.md) 이동하거나 [기존 서브넷 을 사용할지](vnet-existing-add-subnet.md)선택합니다.
- [관리 작업을](management-operations-overview.md) 사용하여 새 관리형 인스턴스를 자동으로 배포하거나, 인스턴스 속성을 업데이트하거나, 인스턴스를 삭제합니다. 이러한 관리 작업을 [모니터링할](management-operations-monitor.md) 수 있습니다. 



## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항: 

관리되는 인스턴스를 배포하거나 다른 서브넷으로 이동하려면 대상 서브넷에 특정 [네트워크 요구 사항이](connectivity-architecture-overview.md#service-aided-subnet-configuration)있어야 합니다.

### <a name="subnet-readiness"></a>서브넷 준비 상태 

관리되는 인스턴스를 이동하기 전에 서브넷이 **Managed Instance 준비** 완료로 표시되어 있는지 확인합니다. 

Azure Portal **가상 네트워크** UI에서 관리되는 인스턴스의 필수 구성을 충족하는 가상 네트워크는 **Managed Instance 준비 완료로** 분류됩니다. 관리되는 인스턴스가 이미 배포된 서브넷이 있는 가상 네트워크에는 가상 네트워크 이름 앞에 아이콘이 표시됩니다. 관리되는 인스턴스에 대해 준비된 빈 서브넷에는 아이콘이 없습니다. 

**기타로** 표시된 서브넷은 비어 있으며 관리되는 인스턴스에 사용할 수 있지만 먼저 [네트워크 요구 사항을](connectivity-architecture-overview.md#service-aided-subnet-configuration)충족해야 합니다. 다음 내용이 포함됩니다.

- Microsoft.Sql/managedInstances 리소스 공급자에 위임
- 경로 테이블 연결
- 네트워크 보안 그룹 연결

모든 요구 사항이 충족되면 서브넷이 **기타에서** **Managed Instance 준비** 완료 범주로 이동하고 관리되는 인스턴스에 사용할 수 있습니다. 

**유효하지 않음으로** 표시된 서브넷은 이미 사용 중이거나(인스턴스 배포에 사용된 인스턴스에 다른 리소스를 포함할 수 없음) 서브넷에 다른 DNS 영역(서브넷 간 인스턴스 이동 제한)이 있기 때문에 새 또는 기존 관리형 인스턴스에 사용할 수 없습니다. 

> [!div class="mx-imgBorder"]
> ![Azure SQL Managed Instance 서브넷 드롭다운 스크린샷](./media/vnet-subnet-move-instance/subnet-grouping-per-state.png)


서브넷 상태 및 지정에 따라 대상 서브넷에 대해 다음과 같은 조정이 수행될 수 있습니다. 

- **Managed Instance 준비 완료(기존 SQL Managed Instance 포함)**: 조정이 수행되지 않습니다. 이러한 서브넷에는 이미 관리되는 인스턴스가 포함되어 있으며 서브넷을 변경하면 기존 인스턴스에 영향을 줄 수 있습니다. 
- **Managed Instance 준비 완료(비어 있음)**: 워크플로는 네트워크 보안 그룹 및 경로 테이블의 모든 필수 규칙의 유효성을 검사하고 필요하지만 누락된 모든 규칙을 추가합니다. <sup>1</sup>

> [!Note]
> <sup>1</sup> 원본 서브넷 구성에 추가된 사용자 지정 규칙은 대상 서브넷에 복사되지 않습니다. 원본 서브넷 구성의 사용자 지정은 대상 서브넷에 수동으로 복제해야 합니다. 이를 달성하는 한 가지 방법은 원본 및 대상 서브넷에 대해 동일한 경로 테이블 및 네트워크 보안 그룹을 사용하는 것입니다.


### <a name="destination-subnet-limitations"></a>대상 서브넷 제한 사항 

기존 인스턴스에 대한 대상 서브넷을 선택할 때 다음 제한 사항 고려: 

- 대상 서브넷은 원본 서브넷과 동일한 가상 네트워크에 있어야 합니다. 
- 관리되는 인스턴스의 DNS 영역 변경은 현재 지원되지 않기 때문에 대상 서브넷의 DNS 영역은 원본 서브넷의 DNS 영역과 일치해야 합니다. 
- Gen4 하드웨어에서 실행되는 인스턴스는 Gen4가 더 이상 사용되지 않도록 최신 하드웨어 세대로 업그레이드해야 합니다. 하드웨어 생성을 업그레이드하고 다른 서브넷으로 이동하는 작업은 한 작업에서 수행할 수 있습니다. 


## <a name="operation-steps"></a>작업 단계

다음 표에서는 인스턴스 이동 작업 중에 발생하는 작업 단계를 자세히 설명합니다. 

|단계 이름  |단계 설명  |
|----|---------|
|요청 유효성 검사 |제출된 매개 변수의 유효성을 검사합니다. 잘못된 구성이 검색되면 오류가 발생하며 작업이 실패합니다. |
|가상 클러스터 크기 조정/만들기 |대상 서브넷의 상태에 따라 가상 클러스터가 생성되거나 크기가 크기가 설정됩니다.  |
|새 인스턴스 시작 |SQL 프로세스는 대상 서브넷에 배포된 가상 클러스터에서 시작됩니다. |
|데이터베이스 파일 시딩/데이터베이스 파일 연결 |서비스 계층에 따라 데이터베이스가 시드되거나 데이터베이스 파일이 연결됩니다. |
|장애 조치(failover) 준비 및 장애 조치(failover) |데이터를 시드하거나 데이터베이스 파일을 다시 연결한 후 시스템은 장애 조치(failover)를 준비합니다. 모든 것이 준비되면 시스템은 일반적으로 10초 미만의 **짧은 가동 중지 시간으로** 장애 조치(failover)를 수행합니다.  |
|오래된 SQL 인스턴스 정리 |원본 가상 클러스터에서 이전 SQL 프로세스를 제거합니다.  |
|가상 클러스터 삭제 |원본 서브넷 내의 마지막 인스턴스인 경우 마지막 단계는 가상 클러스터를 동기적으로 삭제합니다. 그렇지 않으면 가상 클러스터가 비동기적으로 조각 모음됩니다.  |

작업 단계에 대한 자세한 설명은 Azure [SQL Managed Instance 관리 작업의 개요에서](management-operations-overview.md#management-operations-steps) 찾을 수 있습니다.

## <a name="move-the-instance"></a>인스턴스 이동

서브넷 간 인스턴스 이동은 인스턴스 업데이트 작업의 일부입니다. 기존 인스턴스 업데이트 API, Azure PowerShell 및 Azure CLI 명령은 서브넷 ID 속성으로 향상되었습니다. 

Azure Portal **네트워킹** 블레이드의 서브넷 필드를 사용하여 인스턴스를 대상 서브넷으로 이동합니다. Azure PowerShell 또는 Azure CLI 사용하는 경우 update 명령에 다른 서브넷 ID를 제공하여 기존 서브넷에서 대상 서브넷으로 인스턴스를 이동합니다. 

인스턴스 관리 명령에 대한 전체 참조는 [Azure SQL Managed Instance 대한 관리 API 참조를 참조하세요.](api-references-create-manage-instance.md) 

# <a name="portal"></a>[포털](#tab/azure-portal)

인스턴스 서브넷을 선택하는 옵션은 Azure Portal **네트워킹** 블레이드에 있습니다. 인스턴스 이동 작업은 서브넷을 선택하고 변경 내용을 저장할 때 시작됩니다. 

이동 작업의 첫 번째 단계는 배포를 위해 대상 서브넷을 준비하는 것입니다. 이 작업에는 몇 분 정도 걸릴 수 있습니다. 서브넷이 준비되면 인스턴스 이동 관리 작업이 시작되고 Azure Portal 표시됩니다. 


> [!div class="mx-imgBorder"]
> ![SQL Managed Instance 네트워킹 블레이드에서 서브넷을 선택하는 방법](./media/vnet-subnet-move-instance/how-to-select-subnet.png)


Azure Portal **개요** 블레이드에서 인스턴스 이동 작업을 모니터링합니다. 알림을 선택하여 현재 단계, 총 단계 및 작업을 취소하는 단추에 대한 정보가 포함된 추가 블레이드를 엽니다. 

> [!div class="mx-imgBorder"]
> ![인스턴스 이동 작업을 모니터링하는 방법](./media/vnet-subnet-move-instance/monitor-subnet-move-operation.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

대상 서브넷과 동일한 가상 네트워크에 서브넷을 만든 후 Azure PowerShell 명령 [Set-AzSqlInstance를](/powershell/module/az.sql/set-azsqlinstance) 사용하여 인스턴스를 이동합니다. 기존 서브넷을 사용하려면 PowerShell 명령에서 해당 서브넷 이름을 제공합니다.

이 섹션의 예제 PowerShell 명령은 인스턴스 배포를 위해 대상 서브넷을 준비하고 관리되는 인스턴스를 이동합니다. 


다음 PowerShell 명령을 사용하여 매개 변수를 지정합니다. 

```powershell-interactive
### PART 1 - DEFINE PARAMETERS

#Generating basic parameters
$currentSubscriptionID = 'subscription-id'
$sqlMIResourceGroupName = 'resource-group-name-of-sql-mi'
$sqlMIName = 'sql-mi-name'
$sqlMIResourceVnetName = 'vnet-name-of-sql-mi'
$destinationSubnetName = 'name-of-the-destination-subnet-for-sql-mi'
```

서브넷에 이미 배포된 인스턴스가 있는 경우 이 명령을 건너뜁니다. 새 서브넷을 사용하는 경우 다음 Azure PowerShell 명령을 사용하여 서브넷을 준비합니다. 

```powershell-interactive
### PART 2 - PREPARE DESTINATION SUBNET

#Loading the url of script used for preparing the subnet for SQL MI deployment
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

#Generating destination subnet parameters
$parameters = @{
    subscriptionId = $currentSubscriptionID
    resourceGroupName = $sqlMIResourceGroupName
    virtualNetworkName = $sqlMIResourceVnetName
    subnetName = $destinationSubnetName
}

#Initiating subnet prepartion script
Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

> [!Note]
> 서브넷을 준비하는 스크립트에 대한 자세한 내용은 [Azure SQL Managed Instance 대한 기존 가상 네트워크 구성을](vnet-existing-add-subnet.md)참조하세요. 

다음 Azure PowerShell 명령은 인스턴스를 원본 서브넷으로 이동합니다. 

```powershell-interactive
### PART 3 - MOVE INSTANCE TO THE NEW SUBNET

Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName"
```

다음 Azure PowerShell 명령은 인스턴스를 이동하고 진행 상황을 모니터링하는 방법도 제공합니다. 

```powershell-interactive
###PART 3 EXTENDED - MOVE INSTANCE AND MONITOR PROGRESS

# Extend the Set-AzSqlInstance command with -AsJob -Force parameters to be able to monitor the progress or proceed with script execution as moving the instance to another subnet is long running operation 
Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName" -AsJob -Force

$operationProgress = Get-AzSqlInstanceOperation -ManagedInstanceName $sqlMIName -ResourceGroupName $sqlMIResourceGroupName
#checking the operation step status
Write-Host "Checking the ongoing step" -ForegroundColor Yellow
$operationProgress.OperationSteps.StepsList
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) 명령을 사용하여 인스턴스를 다른 서브넷으로 이동합니다. 

서브넷 ID를 `--subnet` 속성으로 지정하거나 가상 네트워크 이름을 속성으로 `--vnet-name` 지정하고 서브넷 이름을 속성으로 지정하여 대상을 `--subnet` 제공합니다. 

다음 예제에서는 서브넷 ID를 지정하여 관리되는 인스턴스를 다른 서브넷으로 이동합니다. 


```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --subnet /subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVirtualNetworkName/subnets/destinationSubnetName
```

다음 예제에서는 가상 네트워크 이름 및 서브넷 이름을 지정하여 관리되는 인스턴스를 다른 서브넷으로 이동합니다.

```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --vnet-name myVirtualNetworkName --subnet destinationSubnetName
```

다음 명령을 사용하여 관리 작업의 진행 상황을 모니터링합니다. 

```azurecli-interactive
az sql mi op list -g myResourceGroup --mi mySqlManagedInstance
```
---

## <a name="next-steps"></a>다음 단계

- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [SQL Managed Instance VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
- 백업 파일에서 관리형 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자습서는 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
