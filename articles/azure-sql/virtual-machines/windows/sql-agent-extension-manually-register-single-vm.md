---
title: SQL IaaS Agent 확장에 등록
description: Azure SQL Server 가상 머신을 SQL IaaS Agent 확장에 등록하면 Azure Marketplace 외부에 배포된 SQL Server 가상 머신에서 기능을 설정할 수 있을 뿐 아니라 규정 준수 및 관리 기능을 향상시킬 수 있습니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/01/2021
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 32ae5782fab7592e081044da9b4bb5e04cef38d5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008698"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>SQL IaaS Agent 확장에 SQL Server VM 등록

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[SQL IaaS Agent 확장](sql-server-iaas-agent-extension-automate-management.md)에 SQL Server VM을 등록하면 Azure VM의 SQL Server에 대 한 다양한 주요 이점을 사용할 수 있습니다. 기본적으로 SQL Server 2016 이상 설치되어 있는 Azure VM은 [CEIP 서비스에서](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)검색될 때 SQL IaaS 에이전트 확장에 자동으로 등록됩니다.  자세한 내용은 [SQL Server 개인 정보 제공](/sql/sql-server/sql-server-privacy#non-personal-data)을 참조하세요.

이 문서에서는 SQL IaaS Agent 확장에 단일 SQL Server VM를 등록하는 방법을 설명합니다. 또는 구독의 모든 SQL Server VM을 [자동으로](sql-agent-extension-automatic-registration-all-vms.md) 등록하거나 [스크립팅된 여러 VM을 대량으로](sql-agent-extension-manually-register-vms-bulk.md) 등록할 수 있습니다.

> [!NOTE]
> 2021년 9월부터 전체 모드로 SQL IaaS 확장에 등록하기 위해 더 이상 SQL Server 서비스를 다시 시작하지 않아도 됩니다. 

## <a name="overview"></a>개요

[SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)에 등록하면 가상 머신 리소스와는 ‘별도’의 리소스인 [**SQL 가상 머신** _리소스_](manage-sql-vm-portal.md)가 구독 내에 생성됩니다. 확장에서 SQL Server VM의 등록을 취소하면 **SQL 가상 머신** _리소스_ 가 제거되지만 실제 가상 머신은 삭제되지 않습니다.

Azure Portal을 통해 SQL Server VM Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 확장에 등록됩니다. 그러나 Azure 가상 머신에 SQL Server를 자동으로 설치하거나 사용자 지정 VHD에서 Azure 가상 머신을 프로비전하도록 선택하는 경우에는 모든 주요 이점 및 관리 효율성을 사용할 수 있도록 SQL IaaS Agent 확장에 SQL Server VM를 등록해야 합니다.

SQL IaaS Agent 확장을 활용하려면 [**Microsoft.SqlVirtualMachine** 공급자에 구독을 등록](#register-subscription-with-rp)하여 해당 구독 내에서 리소스를 만들 수 있는 권한을 SQL IaaS 확장에 부여해야 합니다.

> [!IMPORTANT]
> SQL IaaS Agent 확장은 Azure Virtual Machines 내에서 SQL Server를 사용하는 고객에게 선택적 혜택을 제공하기 위한 목적으로 데이터를 수집합니다. Microsoft는 고객의 사전 동의 없이 라이선스 감사에 이 데이터를 사용하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 제공](/sql/sql-server/sql-server-privacy#non-personal-data)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

확장에 SQL Server VM을 등록하려면 다음이 필요합니다.

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL Server 2008(또는 그 이상)](https://www.microsoft.com/sql-server/sql-server-downloads)이 공용 또는 Azure Government 클라우드에 배포된 Azure 리소스 모델 [Windows Server 2008(또는 그 이상) 가상 머신](../../../virtual-machines/windows/quick-create-portal.md)(또는 그 이상)
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell(5.0 이상)](/powershell/azure/install-az-ps)
- 최소 .NET Framework 4.5.1 이상

## <a name="register-subscription-with-rp"></a>RP에 구독 등록

SQL IaaS 에이전트 확장에 SQL Server VM을 등록하려면 먼저 구독을 **Microsoft.SqlVirtualMachine** RP(리소스 공급자)에 등록해야 합니다. 그러면 SQL IaaS Agent 확장이 구독 내에서 리소스를 만들 수 있습니다. Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. Azure Portal을 열고 **모든 서비스** 로 이동합니다.
1. **구독** 으로 이동하여 원하는 구독을 선택합니다.
1. **구독** 페이지의 **설정** 에서 **리소스 공급자** 를 선택합니다.
1. 필터에 **sql** 을 입력하여 SQL 관련 리소스 공급자를 표시합니다.
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 **등록**, **다시 등록** 또는 **등록 해제** 를 선택합니다.

   ![공급자 수정](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>명령 줄

Azure CLI 또는 Azure PowerShell을 사용하여 **SqlVirtualMachine** 공급자에 Azure 구독을 등록합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="full-mode"></a>전체 모드

SQL Server VM을 전체 모드로 직접 등록하려면 다음 Azure PowerShell 명령을 사용합니다.

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  
  ```

전체 모드에 대해 자세히 알아보려면 [관리 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)를 참조하세요.

### <a name="upgrade-to-full"></a>전체 모드로 업그레이드

*경량* 모드로 확장에 등록된 SQL Server VM은 Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 _전체_ 모드로 업그레이드할 수 있습니다. _에이전트 없음_ 모드의 SQL Server VM은 OS를 Windows 2008 R2 이상으로 업그레이드한 후 _전체_ 모드로 업그레이드할 수 있습니다. 다운그레이드할 수는 없습니다. 그러려면 SQL Server VM을 SQL IaaS Agent 확장에서 [등록 취소](#unregister-from-extension)해야 합니다. 이렇게 하면 **SQL 가상 머신** _리소스_ 가 제거되지만 실제 가상 머신은 삭제되지 않습니다.

#### <a name="azure-portal"></a>Azure Portal

Azure Portal을 사용하여 확장을 전체 모드로 업그레이드하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL 가상 머신](manage-sql-vm-portal.md#access-the-resource) 리소스로 이동합니다.
1. SQL Server VM을 선택하고 **개요** 페이지로 이동합니다.
1. SQL Server VM이 에이전트 없음 또는 경량 IaaS 확장 모드인 경우 **현재 SQL IaaS 확장 모드에서는 라이선스 유형 및 버전 업데이트만 사용할 수 있습니다...** 메시지를 선택합니다.

   ![포털에서 모드를 변경하기 위한 선택 항목](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. **확인** 을 선택하여 SQL Server IaaS 확장 모드를 전체로 업그레이드합니다.

    ![**확인**을 선택하여 SQL Server IaaS 확장 모드를 전체로 업그레이드합니다.](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

#### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

확장을 전체 모드로 업그레이드하려면 다음 Azure CLI 코드 조각을 실행합니다.

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

확장을 전체 모드로 업그레이드하려면 다음 Azure PowerShell 코드 조각을 실행합니다.

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -Location $vm.Location
  ```

---

## <a name="lightweight-mode"></a>경량 모드

제한된 기능을 사용하려면 Azure CLI 또는 Azure PowerShell을 사용하여 SQL Server VM을 경량 모드로 확장에 등록합니다. 

SQL Server 라이선스 유형을 사용량에 따라 지불하려면 종량제(`PAYG`), 자체 라이선스를 사용하려면 Azure 하이브리드 혜택(`AHUB`), [무료 DR 복제본 라이선스](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)를 활성화하려면 재해 복구(`DR`)로 제공합니다.

장애 조치(failover) 클러스터 인스턴스 및 다중 인스턴스 배포는 SQL IaaS Agent 확장에 경량 모드로만 등록할 수 있습니다.

경량 모드에 대해 자세히 알아보려면 [관리 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI를 사용하여 SQL Server VM을 경량 모드로 등록합니다.

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell을 사용하여 SQL Server VM을 경량 모드로 등록합니다.

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```
---

## <a name="noagent-management-mode"></a>에이전트 없음 관리 모드

Windows Server 2008(_R2_ 가 아님)에 설치된 SQL Server 2008 및 2008 R2는 SQL IaaS 에이전트 확장에 [에이전트 없음 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)로만 등록할 수 있습니다. 이 옵션은 규정 준수를 보장하고 제한된 기능으로 Azure Portal에서 SQL Server VM을 모니터링할 수 있도록 합니다.

**라이선스 형식** 에 `AHUB`, `PAYG` 또는 `DR`을 지정합니다. **이미지 제품** 에 `SQL2008-WS2008` 또는 `SQL2008R2-WS2008`을 지정합니다.

Windows Server 2008 인스턴스의 SQL Server 2008(`SQL2008-WS2008`) 또는 2008 R2(`SQL2008R2-WS2008`)를 등록하려면 다음 Azure CLI 또는 PowerShell 코드 조각을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI를 사용하여 NoAgent 모드로 SQL Server 가상 머신을 등록합니다.

```azurecli-interactive
az sql vm create -n sqlvm -g myresourcegroup -l eastus |
--license-type <license type>  --sql-mgmt-type NoAgent 
--image-sku Enterprise --image-offer <image offer> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell을 사용하여 NoAgent 모드로 SQL Server 가상 머신을 등록합니다.

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
```

---


## <a name="check-extension-mode"></a>확장 모드 확인

Azure PowerShell을 사용하여 SQL Server IaaS 에이전트 확장이 있는 관리 모드를 확인합니다. 

확장의 모드를 확인하려면 다음 Azure PowerShell cmdlet을 사용합니다. 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>전체 모드로 업그레이드

*경량* 모드로 확장에 등록된 SQL Server VM은 Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 _전체_ 모드로 업그레이드할 수 있습니다. _에이전트 없음_ 모드의 SQL Server VM은 OS를 Windows 2008 R2 이상으로 업그레이드한 후 _전체_ 모드로 업그레이드할 수 있습니다. 다운그레이드할 수는 없습니다. 그러려면 SQL Server VM을 SQL IaaS Agent 확장에서 [등록 취소](#unregister-from-extension)해야 합니다. 이렇게 하면 **SQL 가상 머신** _리소스_ 가 제거되지만 실제 가상 머신은 삭제되지 않습니다.

### <a name="azure-portal"></a>Azure Portal

Azure Portal을 사용하여 확장을 전체 모드로 업그레이드하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL 가상 머신](manage-sql-vm-portal.md#access-the-resource) 리소스로 이동합니다.
1. SQL Server VM을 선택하고 **개요** 를 선택합니다.
1. SQL Server VM이 에이전트 없음 또는 경량 IaaS 모드인 경우 **현재 SQL IaaS 확장에서는 라이선스 유형 및 버전 업데이트만 사용할 수 있습니다...** 메시지를 선택합니다.

   ![포털에서 모드를 변경하기 위한 선택 항목](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. **확인** 을 선택하여 SQL Server 확장 IaaS 모드를 전체로 업그레이드합니다.

    ![**확인**을 선택하여 SQL Server 확장 IaaS 모드를 전체로 업그레이드합니다.](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

확장을 전체 모드로 업그레이드하려면 다음 Azure CLI 코드 조각을 실행합니다.

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

확장을 전체 모드로 업그레이드하려면 다음 Azure PowerShell 코드 조각을 실행합니다.

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -Location $vm.Location
  ```

---

## <a name="verify-registration-status"></a>등록 상태 확인

Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 SQL Server VM이 이미 SQL IaaS Agent 확장에 등록되어 있는지 확인할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

Azure Portal을 사용하여 등록 상태를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL Server VM](manage-sql-vm-portal.md)으로 이동합니다.
1. 목록에서 SQL Server VM을 선택합니다. SQL Server VM이 여기에 나열되지 않는 경우 SQL IaaS Agent 확장에 등록되지 않았을 수 있습니다.
1. **상태** 에서 값을 확인합니다. **상태** 가 **성공** 인 경우 SQL IaaS Agent 확장에 등록되어 있는 것입니다.

   ![SQL RP 등록 상태 확인](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

또는 **SQL 가상 머신** 리소스의 **지원 + 문제 해결** 창에서 **복구** 를 선택하여 상태를 확인할 수 있습니다. SQL IaaS 에이전트 확장에 대한 프로비저닝 상태는 **성공** 또는 **실패** 일 수 있습니다. 

### <a name="command-line"></a>명령 줄

Azure CLI 또는 Azure PowerShell을 사용하여 현재 SQL Server VM 등록 상태를 확인합니다. 등록이 성공하면 `ProvisioningState`가 `Succeeded`로 표시됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI를 사용하여 등록 상태를 확인하려면 다음 코드 조각을 실행합니다.

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell을 사용하여 등록 상태를 확인하려면 다음 코드 조각을 실행합니다.

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

오류가 발생하면 SQL Server VM이 확장에 등록되지 않은 것입니다.

## <a name="repair-extension"></a>확장 복구

SQL IaaS 에이전트 확장이 실패한 상태일 수 있습니다. Azure Portal을 사용하여 SQL IaaS 에이전트 확장을 복구합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL Server VM](manage-sql-vm-portal.md)으로 이동합니다.
1. 목록에서 SQL Server VM을 선택합니다. SQL Server VM이 여기에 나열되지 않는 경우 SQL IaaS Agent 확장에 등록되지 않았을 수 있습니다.
1. **SQL 가상 머신** 리소스 페이지의 **지원 + 문제 해결** 에서 **복구** 를 선택합니다. 

   :::image type="content" source="media/sql-agent-extension-manually-register-single-vm/repair-extension.png" alt-text="*SQL 가상 머신** 리소스 페이지의 **지원 + 문제 해결**에서 **복구** 선택":::   

1. 프로비저닝 상태가 **실패** 로 표시되는 경우 **복구** 를 선택하여 확장을 복구합니다. 상태가 **성공** 인 경우 **강제 복구** 옆에 있는 확인란을 선택하여 상태에 관계없이 확장을 복구할 수 있습니다. 

   ![프로비저닝 상태가 **실패**로 표시되는 경우 **복구**를 선택하여 확장을 복구합니다. 상태가 **성공**인 경우 **강제 복구** 옆에 있는 확인란을 선택하여 상태에 관계없이 확장을 복구할 수 있습니다.](./media/sql-agent-extension-manually-register-single-vm/force-repair-extension.png)


## <a name="unregister-from-extension"></a>확장에서 등록 취소

SQL Server VM을 SQL IaaS Agent 확장에서 등록 취소하려면 Azure Portal 또는 Azure CLI를 사용하여 SQL 가상 머신 *리소스* 를 삭제합니다. SQL 가상 머신 *리소스* 를 삭제해도 SQL Server VM은 삭제되지 않습니다. 그러나 *리소스* 를 제거하려고 할 때 실수로 가상 머신을 삭제할 수 있으므로 신중하게 단계를 수행해야 합니다.

관리 모드를 전체 모드에서 다운그레이드하려면 SQL 가상 머신을 SQL IaaS Agent 확장에서 등록 취소해야 합니다.

### <a name="azure-portal"></a>Azure Portal

Azure Portal을 사용하여 SQL Server VM을 확장에서 등록 취소하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. SQL VM 리소스로 이동합니다.

   ![SQL 가상 머신 리소스](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. **삭제** 를 선택합니다.

   ![상단 탐색 메뉴에서 삭제 선택](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. SQL 가상 머신의 이름을 입력하고 **가상 머신 옆에 있는 확인란의 선택을 취소** 합니다.

   ![실제 가상 머신이 삭제되지 않도록 VM을 선택 취소한 후 삭제를 선택하여 SQL VM 리소스 삭제를 계속합니다.](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   > [!WARNING]
   > 가상 머신 이름 옆의 확인란 선택을 취소하지 않으면 가상 머신이 완전히 *삭제* 됩니다. 이 확인란 선택을 취소하여 SQL Server VM을 확장에서 등록 취소하되 *실제 가상 머신은 삭제하지 않습니다*.

1. **삭제** 를 선택하여 SQL Server VM이 아닌 SQL 가상 머신 *리소스* 삭제를 확인합니다.

### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 SQL Server VM을 확장에서 등록 취소하려면 [az sql vm delete](/cli/azure/sql/vm#az_sql_vm_delete) 명령을 사용합니다. 이렇게 하면 SQL Server VM *리소스* 는 제거되지만 가상 머신은 삭제되지 않습니다.

```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell을 사용하여 확장에서 SQL Server VM 등록을 취소하려면 [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)명령을 사용합니다. 이렇게 하면 SQL Server VM *리소스* 는 제거되지만 가상 머신은 삭제되지 않습니다.

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](../../database/doc-changes-updates-release-notes.md)
