---
title: 인스턴스 풀에 SQL Managed Instance 배포
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance 풀(미리 보기)을 만들고 관리하는 방법을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 09/05/2019
ms.openlocfilehash: a003370180471e02f4801bffd2477f0c50faa99d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676564"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>인스턴스 풀에 Azure SQL Managed Instance 배포
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 [인스턴스 풀](instance-pools-overview.md)을 만들고 여기에 Azure SQL Managed Instance를 배포하는 방법에 대한 세부 정보를 제공합니다. 

## <a name="instance-pool-operations"></a>인스턴스 풀 작업

다음 표에서는 인스턴스 풀과 관련된 사용 가능한 작업 및 Azure Portal, PowerShell 및 Azure CLI 해당 가용성을 보여줍니다.

|명령|Azure portal|PowerShell|Azure CLI|
|:---|:---|:---|:---|
|인스턴스 풀 만들기|예|예|예|
|인스턴스 풀 업데이트(제한된 속성 수)|예 |예 | 예|
|인스턴스 풀 사용량 및 속성 확인|예|예 | 예 |
|인스턴스 풀 삭제|예|예|예|
|인스턴스 풀 내에서 관리되는 인스턴스 만들기|예|예|아니요|
|관리되는 인스턴스의 리소스 사용량 업데이트|예 |예|아니요|
|관리되는 인스턴스의 사용량 및 속성 확인|예|예|아니요|
|풀에서 관리되는 인스턴스 삭제|예|예|아니요|
|풀 내의 인스턴스에 데이터베이스 만들기|예|예|아니요|
|SQL Managed Instance에서 데이터베이스 삭제|예|예|아니요|

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하려면 [최신 버전의 PowerShell Core를 설치](/powershell/scripting/install/installing-powershell#powershell)하고 지침에 따라 [Azure PowerShell 모듈을 설치합니다](/powershell/azure/install-az-ps).

사용 가능한 [PowerShell 명령](/powershell/module/az.sql/)

|Cmdlet |Description |
|:---|:---|
|[New-AzSQLInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | SQL Managed Instance 풀을 만듭니다. |
|[Get-AzSQLInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | 인스턴스 풀에 대한 정보를 반환합니다. |
|[Set-AzSQLInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | SQL Managed Instance에서 인스턴스 풀의 속성을 설정합니다. |
|[Remove-AzSQLInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | SQL Managed Instance에서 인스턴스 풀을 제거합니다. |
|[Get-AzSQLInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | SQL Managed Instance 풀 사용량에 대한 정보를 반환합니다. |

풀 및 단일 인스턴스 내부의 인스턴스와 모두 관련된 작업의 경우 표준 [관리되는 인스턴스 명령](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)을 사용하지만 풀의 인스턴스에 대해 이러한 명령을 사용할 때는 ‘인스턴스 풀 이름’ 속성을 채워야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

사용 가능한 [Azure CLI](/cli/azure/sql) 명령:

|Cmdlet |Description |
|:---|:---|
|[az sql instance-pool create](/cli/azure/sql/instance-pool#az_sql_instance_pool_create) | SQL Managed Instance 풀을 만듭니다. |
|[az sql instance-pool show](/cli/azure/sql/instance-pool#az_sql_instance_pool_show) | 인스턴스 풀에 대한 정보를 반환합니다. |
|[az sql instance-pool update](/cli/azure/sql/instance-pool#az_sql_instance_pool_update) | SQL Managed Instance 인스턴스 풀의 속성을 설정하거나 업데이트합니다. |
|[az sql instance-pool delete](/cli/azure/sql/instance-pool#az_sql_instance_pool_delete) | SQL Managed Instance에서 인스턴스 풀을 제거합니다. |

---

## <a name="deployment-process"></a>배포 프로세스

관리되는 인스턴스를 인스턴스 풀에 배포하려면 먼저 인스턴스 풀을 배포해야 합니다. 이는 기간이 [빈 서브넷에 생성된 단일 인스턴스](sql-managed-instance-paas-overview.md#management-operations)를 배포하는 것과 동일한 일회성 장기 실행 작업입니다. 그런 다음, 관리되는 인스턴스를 풀에 배포할 수 있습니다. 이는 일반적으로 최대 5분이 소요되는 비교적 빠른 작업입니다. 인스턴스 풀 매개 변수를 이 작업의 일부로 명시적으로 지정해야 합니다.

공개 미리 보기에서는 두 작업 모두 PowerShell 및 Azure Resource Manager 템플릿만 사용하여 지원합니다. Azure Portal 환경은 현재 사용할 수 없습니다.

관리되는 인스턴스를 풀에 배포한 후에는 가격 책정 계층 페이지에서 해당 속성을 변경하기 위해 Azure Portal을 ‘사용할 수 있습니다’.

## <a name="create-a-virtual-network-with-a-subnet"></a>서브넷을 사용하여 가상 네트워크 만들기 

동일한 가상 네트워크 내에 여러 인스턴스 풀을 배치하려면 다음 문서를 참조하세요.

- [Azure SQL Managed Instance의 VNet 서브넷 크기 결정](vnet-subnet-determine-size.md)
- [Azure Portal](virtual-network-subnet-create-arm-template.md) 템플릿을 사용하여 새 가상 네트워크 및 서브넷을 만들거나 [기존 가상 네트워크 준비](vnet-existing-add-subnet.md)에 대한 지침을 따르세요.
 

## <a name="create-an-instance-pool"></a>인스턴스 풀 만들기 

이전 단계를 완료하면 인스턴스 풀을 만들 준비가 된 것입니다.

인스턴스 풀에는 다음과 같은 제한 사항이 적용됩니다.

- 공개 미리 보기에서는 범용 및 Gen5만 제공됩니다.
- 풀 이름은 소문자, 숫자 및 하이픈만 포함할 수 있으며 하이픈으로 시작할 수 없습니다.
- Azure 하이브리드 혜택을 사용하려는 경우 인스턴스 풀 수준에서 적용됩니다. 풀을 만드는 동안 라이선스 유형을 설정하거나 만든 후 언제든지 업데이트할 수 있습니다.

> [!IMPORTANT]
> 인스턴스 풀을 배포하는 작업은 약 4.5시간이 소요되는 장기 작업입니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

네트워크 매개 변수를 가져오려면 다음을 사용합니다.

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

인스턴스 풀을 만들려면 다음과 같이 합니다.

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

가상 네트워크 매개 변수를 가져오려면:

```azurecli
az network vnet show --resource-group MyResourceGroup --name miPoolVirtualNetwork
```

가상 서브넷 매개 변수를 가져오려면:

```azurecli
az network vnet subnet show --resource group MyResourceGroup --name miPoolSubnet --vnet-name miPoolVirtualNetwork
```

인스턴스 풀을 만들려면 다음과 같이 합니다.

```azurecli
az sql instance-pool create
    --license-type LicenseIncluded 
    --location westeurope
    --name mi-pool-name
    --capacity 8
    --tier GeneralPurpose
    --family Gen5 
    --resrouce-group myResourceGroup
    --subnet miPoolSubnet
    --vnet-name miPoolVirtualNetwork
```

---

> [!IMPORTANT]
> 인스턴스 풀을 배포하는 작업은 장기 실행 작업이므로 이 문서의 다음 단계 중 하나를 실행하기 전에 완료될 때까지 기다려야 합니다.

## <a name="create-a-managed-instance"></a>관리되는 인스턴스 만들기

인스턴스 풀을 성공적으로 배포했다면 그 안에 관리되는 인스턴스를 만들 시간입니다.

관리되는 인스턴스를 만들려면 다음 명령을 실행합니다.

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

풀 내에 인스턴스를 배포하는 데 몇 분 정도 걸립니다. 첫 번째 인스턴스를 만든 후에 추가 인스턴스를 만들 수 있습니다.

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>데이터베이스 만들기 

풀 내에 있는 관리되는 인스턴스에서 데이터베이스를 만들고 관리하려면 단일 인스턴스 명령을 사용합니다.

관리되는 인스턴스 내에 데이터베이스를 만들려면 다음을 사용합니다.

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>풀 사용량 구하기 
 
풀 내의 인스턴스 목록을 가져오려면 다음을 사용합니다.

```powershell
$instancePool | Get-AzSqlInstance
```


풀 리소스 사용량을 가져오려면 다음을 사용합니다.

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


풀 및 그 내부의 인스턴스에 대한 자세한 사용량 개요를 가져오려면 다음을 사용합니다.

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

인스턴스의 데이터베이스를 나열하려면 다음을 사용합니다.

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 인스턴스 풀과 풀 내부에 배포된 관리되는 인스턴스당 데이터베이스 수에 대한 제한을 확인하려면 [인스턴스 풀 리소스 제한](instance-pools-overview.md#resource-limitations) 섹션을 방문하세요.


## <a name="scale"></a>확장 


관리되는 인스턴스를 데이터베이스로 채운 후에 스토리지 또는 성능에 대한 인스턴스 제한에 도달할 수 있습니다. 이 경우 풀 사용량을 초과하지 않았다면 인스턴스를 스케일링할 수 있습니다.
풀 내에서 관리되는 인스턴스를 스케일링하는 작업은 몇 분 정도 소요됩니다. 스케일링을 위한 필수 구성 요소는 인스턴스 풀 수준에서 사용 가능한 vCore 및 스토리지입니다.

vCore 수 및 스토리지 크기를 업데이트하려면 다음을 사용합니다.

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


스토리지 크기만 업데이트하려면 다음을 사용합니다.

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>연결 

풀의 관리되는 인스턴스에 연결하려면 다음 두 단계가 필요합니다.

1. [인스턴스에 대한 공용 엔드포인트를 사용하도록 설정합니다](#enable-the-public-endpoint).
2. [NSG(네트워크 보안 그룹)에 인바운드 규칙을 추가합니다](#add-an-inbound-rule-to-the-network-security-group).

두 단계가 모두 완료된 후에는 인스턴스를 만드는 동안 제공된 공용 엔드포인트 주소, 포트 및 자격 증명을 사용하여 인스턴스에 연결할 수 있습니다. 

### <a name="enable-the-public-endpoint"></a>공용 엔드포인트 사용

인스턴스에 대한 공용 엔드포인트 사용 설정은 Azure Portal 또는 다음 PowerShell 명령을 사용하여 수행할 수 있습니다.


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

이 매개 변수는 인스턴스를 만드는 동안에도 설정할 수 있습니다.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>네트워크 보안 그룹에 인바운드 규칙 추가 

이 단계는 Azure Portal 또는 PowerShell 명령을 사용하여 수행할 수 있으며, 관리되는 인스턴스에 대해 서브넷이 준비된 후 언제든지 수행할 수 있습니다.

자세한 내용은 [네트워크 보안 그룹에서 공용 엔드포인트 트래픽 허용](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)을 참조하세요.


## <a name="move-an-existing-single-instance-to-a-pool"></a>풀로 기존 단일 인스턴스 이동
 
인스턴스를 풀 안팎으로 이동하는 것은 공개 미리 보기 제한 사항 중 하나입니다. 해결 방법은 풀 외부의 인스턴스에서 이미 풀에 있는 인스턴스로 데이터베이스의 특정 시점 복원을 사용합니다. 

두 인스턴스 모두 동일한 구독 및 지역에 있어야 합니다. 지역 간 및 구독 간 복원은 현재 지원되지 않습니다.

이 프로세스에는 가동 중지 시간이 발생합니다.

기존 데이터베이스를 이동하려면 다음과 같이 합니다.

1. 마이그레이션 중인 관리되는 인스턴스에서 작업을 일시 중지합니다.
2. 시스템 데이터베이스를 만드는 스크립트를 생성하여 인스턴스 풀 내에 있는 인스턴스에서 이를 실행합니다.
3. 단일 인스턴스에서 풀의 인스턴스로 각 데이터베이스의 지정 시간 복원을 수행합니다.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. 애플리케이션이 새 인스턴스를 가리키게 하고 워크로드를 다시 시작합니다.

데이터베이스가 여러 개 있는 경우 각 데이터베이스에 대해 이 프로세스를 반복합니다.


## <a name="next-steps"></a>다음 단계

- 기능 및 비교 목록은 [SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [SQL Managed Instance VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
- 백업 파일에서 관리형 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자습서는 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
- 기본 제공 문제 해결 인텔리전스를 사용하는 SQL Managed Instance 데이터베이스의 고급 성능 모니터링에 대해 자세히 알아보려면 [Azure SQL 분석을 사용하여 Azure SQL Managed Instance 모니터링](../../azure-monitor/insights/azure-sql.md)을 참조하세요.
- 가격 정보는 [SQL Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
