---
title: 장애 조치(failover) 그룹 구성
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure Portal, Azure CLI 및 PowerShell을 사용하여 Azure SQL Database(단일 및 풀링 둘 다) 및 SQL Managed Instance에 대해 자동 장애 조치(failover) 그룹을 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.topic: how-to
ms.devlang: ''
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 08/14/2019
ms.openlocfilehash: 1cb2d8fdaa25479542152c76c12b9b86d7fb0433
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165713"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Azure SQL Database에 대한 장애 조치(failover) 그룹 구성
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 항목에서는 Azure SQL Database 및 Azure SQL Managed Instance에 대해 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 구성하는 방법을 설명합니다.

## <a name="single-database"></a>단일 데이터베이스

Azure Portal 또는 PowerShell을 사용하여 장애 조치 그룹을 만들고 단일 데이터베이스를 추가합니다.

### <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소를 고려합니다.

- 보조 서버의 서버 로그인 및 방화벽 설정은 주 서버와 일치해야 합니다.

### <a name="create-failover-group"></a>장애 조치 그룹 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 장애 조치 그룹을 만들고 단일 데이터베이스를 이 그룹에 추가합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색 상자에 Azure SQL을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다.
1. 장애 조치 그룹에 추가하려는 데이터베이스를 선택합니다.
1. **서버 이름** 에서 서버 이름을 선택하여 서버에 대한 설정을 엽니다.

   ![단일 db에 대한 서버 열기](./media/auto-failover-group-configure/open-sql-db-server.png)

1. **설정** 창에서 **장애 조치(failover) 그룹** 을 선택한 다음, **그룹 추가** 를 선택하여 새 장애 조치(failover) 그룹을 만듭니다.

   ![새 장애 조치(failover) 그룹 추가](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. **장애 조치(failover) 그룹** 페이지에서 필요한 값을 입력하거나 선택한 다음, **만들기** 를 선택합니다.

   - **그룹 내의 데이터베이스**: 장애 조치 그룹에 추가하려는 데이터베이스를 선택합니다. 장애 조치(failover) 그룹에 데이터베이스를 추가하면 지역 복제 프로세스가 자동으로 시작됩니다.

   ![장애 조치(failover) 그룹에 SQL Database 추가](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치(failover) 그룹을 만들고 데이터베이스를 이 그룹에 추가합니다.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      FailoverGroupName $failoverGroupName `
      FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

---

### <a name="test-failover"></a>테스트 장애 조치

Azure Portal 또는 PowerShell을 사용하여 장애 조치 그룹의 장애 조치를 테스트합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 장애 조치(failover) 그룹의 장애 조치를 테스트합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색창에 "Azure SQL"을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다.
1. 장애 조치 그룹에 추가하려는 데이터베이스를 선택합니다.

   ![단일 db에 대한 서버 열기](./media/auto-failover-group-configure/open-sql-db-server.png)

1. **설정** 창에서 **장애 조치(failover) 그룹** 을 선택한 다음, 방금 만든 장애 조치 그룹을 선택합니다.
  
   ![포털에서 장애 조치(failover) 그룹 선택](./media/auto-failover-group-configure/select-failover-group.png)

1. 주 서버와 보조 서버를 검토합니다.
1. 작업창에서 **장애 조치(failover)** 를 선택하여 데이터베이스가 포함된 장애 조치 그룹을 장애 조치합니다.
1. TDS 세션의 연결이 해제됨을 알려주는 경고에서 **예** 를 선택합니다.

   ![데이터베이스가 포함된 장애 조치(failover) 그룹 장애 조치](./media/auto-failover-group-configure/failover-sql-db.png)

1. 이제 어떤 서버가 주 서버이고 어떤 서버가 보조 서버인지 검토합니다. 장애 조치(failover)가 성공하면 두 서버의 역할이 바뀌어야 합니다.
1. **장애 조치(failover)** 를 다시 선택하여 서버를 원래 역할로 장애 조치(Failover) 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치(failover) 그룹의 장애 조치를 테스트합니다.  

다음과 같이 보조 복제본의 역할을 확인합니다.

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

다음과 같이 보조 서버로 장애 조치(failover)합니다.

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> 보조 데이터베이스를 삭제해야 하는 경우 장애 조치 그룹에서 제거한 후 삭제합니다. 보조 데이터베이스를 장애 조치(failover) 그룹에서 제거하기 전에 삭제하면 예기치 않은 동작이 발생할 수 있습니다.

## <a name="elastic-pool"></a>탄력적 풀

Azure Portal 또는 PowerShell을 사용하여 장애 조치 그룹을 만들고 탄력적 풀을 추가합니다.  

### <a name="prerequisites"></a>사전 요구 사항

다음 필수 구성 요소를 고려합니다.

- 보조 서버의 서버 로그인 및 방화벽 설정은 주 서버와 일치해야 합니다.

### <a name="create-the-failover-group"></a>장애 조치 그룹 만들기

Azure Portal 또는 PowerShell을 사용하여 탄력적 풀에 대한 장애 조치 그룹을 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 장애 조치 그룹을 만들고 탄력적 풀을 이 그룹에 추가합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색창에 "Azure SQL"을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다.
1. 장애 조치 그룹에 추가하려는 탄력적 풀을 선택합니다.
1. **개요** 창의 **서버 이름** 에서 서버 이름을 선택하여 서버에 대한 설정을 엽니다.
  
   ![탄력적 풀에 대한 서버 열기](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. **설정** 창에서 **장애 조치(Failover) 그룹** 을 선택한 후 **그룹 추가** 를 선택하여 새 장애 조치(failover) 그룹을 만듭니다.

   ![새 장애 조치(failover) 그룹 추가](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. **장애 조치(failover) 그룹** 페이지에서 필요한 값을 입력하거나 선택한 다음, **만들기** 를 선택합니다. 새 보조 서버를 만들거나 기존 보조 서버를 선택합니다.

1. **그룹 내의 데이터베이스** 를 선택한 다음, 장애 조치 그룹에 추가하려는 탄력적 풀을 선택합니다. 탄력적 풀이 보조 서버에 아직 없는 경우 보조 서버에 탄력적 풀을 만들라는 경고가 표시됩니다. 경고를 선택한 다음, **확인** 을 선택하여 보조 서버에서 탄력적 풀을 만듭니다.

   ![장애 조치(failover) 그룹에 탄력적 풀 추가](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. **선택** 을 선택하여 탄력적 풀 설정을 장애 조치(failover) 그룹에 적용한 후 **만들기** 를 선택하여 장애 조치(failover) 그룹을 만듭니다. 장애 조치(failover) 그룹에 탄력적 풀을 추가하면 지역 복제 프로세스가 자동으로 시작됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치 그룹을 만들고 탄력적 풀을 이 그룹에 추가합니다.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
       ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       FailoverGroupName $failoverGroupName `
       FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>테스트 장애 조치

Azure Portal 또는 PowerShell을 사용하여 탄력적 풀의 장애 조치를 테스트합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

장애 조치 그룹을 보조 서버로 장애 조치한 다음, Azure Portal을 사용하여 장애 복구합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색창에 "Azure SQL"을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다.
1. 장애 조치 그룹에 추가하려는 탄력적 풀을 선택합니다.
1. **개요** 창의 **서버 이름** 에서 서버 이름을 선택하여 서버에 대한 설정을 엽니다.

   ![탄력적 풀에 대한 서버 열기](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. **설정** 창에서 **장애 조치(failover) 그룹** 을 선택한 후 섹션 2에서 만든 장애 조치(failover) 그룹을 선택합니다.
  
   ![포털에서 장애 조치(failover) 그룹 선택](./media/auto-failover-group-configure/select-failover-group.png)

1. 주 서버와 보조 서버를 검토합니다.
1. 작업창에서 **장애 조치(failover)** 를 선택하여 탄력적 풀이 포함된 장애 조치 그룹을 장애 조치합니다.
1. TDS 세션의 연결이 해제됨을 알려주는 경고에서 **예** 를 선택합니다.

   ![데이터베이스가 포함된 장애 조치(failover) 그룹 장애 조치](./media/auto-failover-group-configure/failover-sql-db.png)

1. 주 서버와 보조 서버를 검토합니다. 장애 조치(failover)에 성공하면 두 서버에 교환된 역할이 있어야 합니다.
1. **장애 조치(failover)** 를 다시 선택하여 장애 조치 그룹을 다시 원래 설정으로 장애 조치합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치(failover) 그룹의 장애 조치를 테스트합니다.

다음과 같이 보조 복제본의 역할을 확인합니다.

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

다음과 같이 보조 서버로 장애 조치(failover)합니다.

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> 보조 데이터베이스를 삭제해야 하는 경우 장애 조치 그룹에서 제거한 후 삭제합니다. 보조 데이터베이스를 장애 조치(failover) 그룹에서 제거하기 전에 삭제하면 예기치 않은 동작이 발생할 수 있습니다.

## <a name="sql-managed-instance"></a>SQL Managed Instance

Azure Portal 또는 PowerShell을 사용하여 Azure SQL Managed Instance의 두 관리형 인스턴스 사이에 장애 조치 그룹을 만듭니다.

각 SQL Managed Instance의 가상 네트워크에 대해 [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)를 구성하거나 게이트웨이를 만들고, 두 게이트웨이를 연결한 다음, 장애 조치 그룹을 만들어야 합니다. 

성능 상의 이유로 두 관리형 인스턴스를 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 배포합니다. 지리적으로 쌍을 이루는 지역에 상주하는 관리되는 인스턴스는 쌍을 이루지 않는 지역에 비해 훨씬 더 나은 성능을 제공합니다. 

### <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소를 고려합니다.

- 보조 관리형 인스턴스는 비어 있어야 합니다.
- 보조 가상 네트워크의 서브넷 범위는 주 가상 네트워크의 서브넷 범위와 겹치지 않아야 합니다.
- 보조 관리형 인스턴스의 데이터 정렬 및 표준 시간대는 주 관리형 인스턴스와 일치해야 합니다.
- 두 게이트웨이를 연결하는 경우 두 연결에 대해 **공유 키** 가 동일해야 합니다.

### <a name="create-primary-virtual-network-gateway"></a>주 가상 네트워크 게이트웨이 만들기

[ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)를 구성하지 않은 경우 Azure Portal 또는 PowerShell을 사용하여 주 가상 네트워크 게이트웨이를 만들 수 있습니다.

> [!NOTE]
> 게이트웨이의 SKU는 처리량 성능에 영향을 줍니다. 이 문서에서는 가장 기본적인 SKU(`HwGw1`)를 사용하여 게이트웨이를 배포합니다. 더 높은 SKU(예 : `VpnGw3`)를 배포하면 더 높은 처리량을 달성할 수 있습니다. 사용 가능한 모든 옵션은 [게이트웨이 SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark)를 참조하세요. 

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 주 가상 네트워크 게이트웨이를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 기본 관리형 인스턴스에 대한 **가상 네트워크** 리소스를 선택합니다.
1. **설정** 에서 **서브넷** 을 선택한 후 새 **게이트웨이 서브넷** 을 추가하도록 선택합니다. 기본값을 그대로 둡니다.

   ![기본 관리형 인스턴스에 대한 게이트웨이 추가](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. 서브넷 게이트웨이를 만든 후 왼쪽 탐색 창에서 **리소스 만들기** 를 선택한 후 검색 상자에 `Virtual network gateway`를 입력합니다. **Microsoft** 에서 게시한 **가상 네트워크 게이트웨이** 리소스를 선택합니다.

   ![새 가상 네트워크 게이트웨이 만들기](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. 주 관리형 인스턴스에 대한 게이트웨이를 구성하는 데 필요한 필드를 입력합니다.

   다음 표는 기본 관리형 인스턴스의 게이트웨이에 필요한 값을 보여줍니다.

    | **필드** | 값 |
    | --- | --- |
    | **구독** |  기본 관리형 인스턴스가 있는 구독입니다. |
    | **이름** | 가상 네트워크 게이트웨이의 이름입니다. |
    | **지역** | 기본 관리형 인스턴스가 있는 지역입니다. |
    | **게이트웨이 유형** | **VPN** 을 선택합니다. |
    | **VPN 유형** | **경로 기반** 을 선택합니다. |
    | **SKU**| `VpnGw1` 기본값을 그대로 둡니다. |
    | **위치**| 보조 관리형 인스턴스와 보조 가상 네트워크가 있는 위치입니다.   |
    | **가상 네트워크**| 보조 관리형 인스턴스의 가상 네트워크를 선택합니다. |
    | **공용 IP 주소**| **새로 만들기** 를 선택합니다. |
    | **공용 IP 주소 이름**| IP 주소의 이름을 입력합니다. |
    | &nbsp; | &nbsp; |

1. 다른 값은 기본값으로 그대로 두고 **검토 + 만들기** 를 선택하여 가상 네트워크 게이트웨이에 대한 설정을 검토합니다.

   ![기본 게이트웨이 설정](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. **만들기** 를 선택하여 새 가상 네트워크 게이트웨이를 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 주 가상 네트워크 게이트웨이를 만듭니다.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>보조 가상 네트워크 게이트웨이 만들기

Azure Portal 또는 PowerShell을 사용하여 보조 가상 네트워크 게이트웨이를 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

이전 섹션의 단계를 반복하여 보조 관리형 인스턴스에 대한 가상 네트워크 서브넷과 게이트웨이를 만듭니다. 보조 관리형 인스턴스에 대한 게이트웨이를 구성하는 데 필요한 필드를 입력합니다.

다음 표는 보조 관리형 인스턴스의 게이트웨이에 필요한 값을 보여줍니다.

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  보조 관리형 인스턴스가 있는 구독입니다. |
   | **이름** | 가상 네트워크 게이트웨이의 이름입니다(예: `secondary-mi-gateway`). |
   | **지역** | 보조 관리형 인스턴스가 있는 지역입니다. |
   | **게이트웨이 유형** | **VPN** 을 선택합니다. |
   | **VPN 유형** | **경로 기반** 을 선택합니다. |
   | **SKU**| `VpnGw1` 기본값을 그대로 둡니다. |
   | **위치**| 보조 관리형 인스턴스와 보조 가상 네트워크가 있는 위치입니다.   |
   | **가상 네트워크**| 섹션 2에서 만든 가상 네트워크를 선택합니다(예: `vnet-sql-mi-secondary`). |
   | **공용 IP 주소**| **새로 만들기** 를 선택합니다. |
   | **공용 IP 주소 이름**| IP 주소의 이름을 입력합니다(예: `secondary-gateway-IP`). |
   | &nbsp; | &nbsp; |

   ![보조 게이트웨이 설정](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 보조 가상 네트워크 게이트웨이를 만듭니다.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>게이트웨이 연결

Azure Portal 또는 PowerShell을 사용하여 두 게이트웨이 간에 연결을 만듭니다.

주 게이트웨이에서 보조 게이트웨이로 연결한 다음, 보조 게이트웨이에서 주 게이트웨이로 연결하여 두 개의 연결을 만들어야 합니다.

두 연결에 사용되는 공유 키는 각 연결에 대해 동일해야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 두 게이트웨이 간에 연결을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.
1. 검색 상자에 `connection`을 입력한 다음, Enter 키를 눌러 검색하면 Microsoft에서 게시한 **연결** 리소스로 이동합니다.
1. **만들기** 를 선택하여 연결을 만듭니다.
1. **기본 사항** 탭에서 다음 값을 선택한 다음, **확인** 을 선택합니다.
    1. **연결 형식** 에 대해 `VNet-to-VNet`을 선택합니다.
    1. 드롭다운에서 구독을 선택합니다.
    1. 드롭다운에서 관리형 인스턴스의 리소스 그룹을 선택합니다.
    1. 드롭다운에서 기본 관리형 인스턴스의 위치를 선택합니다.
1. **설정** 탭에서 다음 값을 선택하거나 입력한 다음, **확인** 을 선택합니다.
    1. **첫 번째 가상 네트워크 게이트웨이** 에 대한 기본 네트워크 게이트웨이를 선택합니다(예: `Primary-Gateway`).  
    1. **두 번째 가상 네트워크 게이트웨이** 에 대한 보조 네트워크 게이트웨이를 선택합니다(예: `Secondary-Gateway`).
    1. **양방향 연결 설정** 옆에 있는 확인란을 선택합니다.
    1. 기본 연결 이름의 기본값을 그대로 두거나 원하는 값으로 이름을 바꿉니다.
    1. 연결에 대해 **공유 키(PSK)** 를 제공합니다(예: `mi1m2psk`).

   ![게이트웨이 연결 만들기](./media/auto-failover-group-configure/create-gateway-connection.png)

1. **요약** 탭에서 양방향 연결에 대한 설정을 검토한 다음, **확인** 을 선택하여 연결을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 두 게이트웨이 간에 연결을 만듭니다.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>장애 조치 그룹 만들기

Azure Portal 또는 PowerShell을 사용하여 관리형 인스턴스에 대한 장애 조치 그룹을 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 SQL Managed Instance에 대한 장애 조치 그룹을 만듭니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색 상자에 Azure SQL을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다.
1. 장애 조치 그룹에 추가하려는 주 관리형 인스턴스를 선택합니다.  
1. **설정** 에서 **인스턴스 장애 조치(Failover) 그룹** 으로 이동한 다음, **그룹 추가** 를 선택하여 **인스턴스 장애 조치(Failover) 그룹** 페이지를 엽니다.

   ![장애 조치(failover) 그룹 추가](./media/auto-failover-group-configure/add-failover-group.png)

1. **인스턴스 장애 조치(Failover) 그룹** 페이지에서 장애 조치 그룹의 이름을 입력한 다음, 드롭다운에서 보조 관리형 인스턴스를 선택합니다. **만들기** 를 선택하여 장애 조치(failover) 그룹을 만듭니다.

   ![장애 조치 그룹 만들기](./media/auto-failover-group-configure/create-failover-group.png)

1. 장애 조치(failover) 그룹 배포가 완료되면 **장애 조치(failover) 그룹** 페이지로 돌아갑니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 관리형 인스턴스에 대한 장애 조치 그룹을 만듭니다.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>테스트 장애 조치

Azure Portal 또는 PowerShell을 사용하여 장애 조치 그룹의 장애 조치를 테스트합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 장애 조치(failover) 그룹의 장애 조치를 테스트합니다.

1. [Azure Portal](https://portal.azure.com)에서 _보조_ 관리형 인스턴스로 이동하고 설정 아래에서 **인스턴스 장애 조치(Failover) 그룹** 을 선택합니다.
1. 어떤 관리되는 인스턴스가 기본이고 어떤 관리되는 인스턴스가 보조인지 검토합니다.
1. **장애 조치(failover)** 를 선택한 다음, TDS 세션 연결이 끊어진다는 경고에서 **예** 를 선택합니다.

   ![장애 조치(failover) 그룹에 대한 장애 조치(failover)](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. 주가 되는 관리형 인스턴스와 보조인 인스턴스를 검토합니다. 장애 조치(failover)에 성공하면 두 인스턴스의 역할이 전환되어야 합니다.

   ![장애 조치(failover) 후 관리되는 인스턴스의 역할이 전환됨](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. 새 보조 관리형 인스턴스로 이동하고 **장애 조치(failover)** 를 다시 한 번 선택하여 기본 인스턴스를 기본 역할로 다시 장애 조치(failover)합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치(failover) 그룹의 장애 조치를 테스트합니다.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Private Link 사용

프라이빗 링크를 사용하면 가상 네트워크 및 서브넷 내의 특정 개인 IP 주소에 논리 서버를 연결할 수 있습니다. 

장애 조치 그룹에서 프라이빗 링크를 사용하려면 다음을 수행합니다.

1. 주 서버와 보조 서버가 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 있는지 확인합니다. 
1. 겹치지 않는 IP 주소 공간을 갖도록 주 서버와 보조 서버에 대한 프라이빗 엔드포인트를 호스트하기 위해 각 지역에 가상 네트워크 및 서브넷을 만듭니다. 예를 들어 10.0.0.0/16의 주 가상 네트워크 주소 범위와 10.0.0.1/16의 보조 가상 네트워크 주소 범위가 겹칩니다. 가상 네트워크 주소 범위에 대한 자세한 내용은 [designing Azure virtual networks](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)(Azure Virtual Network 디자인) 블로그를 참조하세요.
1. [주 서버에 대한 프라이빗 엔드포인트 및 Azure 프라이빗 DNS 영역](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint)을 만듭니다. 
1. 보조 서버에 대한 프라이빗 엔드포인트도 만듭니다. 하지만 이번에는 주 서버에 대해 만들어진 것과 동일한 프라이빗 DNS 영역을 다시 사용하도록 선택합니다. 
1. 프라이빗 링크를 설정한 다음에는 이 문서의 앞부분에서 간략하게 설명한 단계에 따라 장애 조치 그룹을 만들 수 있습니다. 


## <a name="locate-listener-endpoint"></a>수신기 엔드포인트 찾기

장애 조치 그룹을 구성한 후 애플리케이션에 대한 연결 문자열을 수신기 엔드포인트로 업데이트합니다. 그러면 애플리케이션이 주 데이터베이스, 탄력적 풀, 인스턴스 데이터베이스가 아닌 장애 조치 그룹 수신기에 연결된 상태로 유지됩니다. 이렇게 하면 데이터베이스 엔터티가 장애 조치될 때마다 연결 문자열을 수동으로 업데이트할 필요가 없으며, 트래픽은 현재 주가 되는 모든 엔터티로 라우팅됩니다.

수신기 엔드포인트는 `fog-name.database.windows.net` 형식이며, 장애 조치 그룹을 볼 때 Azure Portal에 표시됩니다.

![장애 조치 그룹 연결 문자열](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>설명

- 단일 또는 풀링된 데이터베이스에 대한 장애 조치 그룹을 제거해도 복제는 중지되지 않으며, 복제된 데이터베이스도 삭제되지 않습니다. 단일 또는 풀링된 데이터베이스를 제거한 후 장애 조치 그룹에 다시 추가하려면 지역에서 복제를 수동으로 중지하고 보조 서버에서 데이터베이스를 삭제해야 합니다. 그러지 않으면 장애 조치 그룹에 데이터베이스를 추가하려고 할 때 `The operation cannot be performed due to multiple errors`와 유사한 오류가 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

장애 조치 그룹을 구성하는 자세한 단계는 다음 자습서를 참조하세요.

- [장애 조치(failover) 그룹에 단일 데이터베이스 추가](failover-group-add-single-database-tutorial.md)
- [장애 조치(failover) 그룹에 탄력적 풀 추가](failover-group-add-elastic-pool-tutorial.md)
- [장애 조치(failover) 그룹에 관리형 인스턴스 추가](../managed-instance/failover-group-add-instance-tutorial.md)

Azure SQL Database 고가용성 옵션에 대한 개요는 [지역에서 복제](active-geo-replication-overview.md) 및 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 참조하세요.
