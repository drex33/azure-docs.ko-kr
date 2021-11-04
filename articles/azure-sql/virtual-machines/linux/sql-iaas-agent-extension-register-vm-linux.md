---
title: SQL IaaS 에이전트 확장에 등록(Linux)
description: SQL Server on Linux Azure VM을 SQL IaaS 에이전트 확장에 등록하여 Azure 기능을 사용하도록 설정하고 규정 준수 및 관리 편의성을 개선하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-Linux-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 2cf9219f5afc6805c147243ec0157280c839dc87
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103610"
---
# <a name="register-linux-sql-server-vm-with-sql-iaas-agent-extension"></a>SQL IaaS 에이전트 확장을 통해 Linux SQL Server VM 등록 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-agent-extension-manually-register-single-vm.md)
> * [Linux](sql-iaas-agent-extension-register-vm-linux.md)


SQL Server VM을 [SQL IaaS 에이전트 확장에](sql-server-iaas-agent-extension-linux.md) 등록하여 SQL Server on Linux Azure VM에 다양한 기능 이점을 제공합니다.

## <a name="overview"></a>개요

[SQL Server IaaS Agent 확장](sql-server-iaas-agent-extension-linux.md)에 등록하면 가상 머신 리소스와는 _별도의_ 리소스인 **SQL 가상 머신** _리소스_ 가 구독 내에 생성됩니다. 확장에서 SQL Server VM을 등록 취소하면 SQL 가상 머신 _리소스가_ 제거되지만 실제 **가상 머신은** 삭제되지 않습니다.

SQL IaaS Agent 확장을 활용하려면 [**Microsoft.SqlVirtualMachine** 공급자에 구독을 등록](#register-subscription-with-rp)하여 해당 구독 내에서 리소스를 만들 수 있는 권한을 SQL IaaS 확장에 부여해야 합니다.

> [!IMPORTANT]
> SQL IaaS Agent 확장은 Azure Virtual Machines 내에서 SQL Server를 사용하는 고객에게 선택적 혜택을 제공하기 위한 목적으로 데이터를 수집합니다. Microsoft는 고객의 사전 동의 없이 라이선스 감사에 이 데이터를 사용하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 제공](/sql/sql-server/sql-server-privacy#non-personal-data)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

확장에 SQL Server VM을 등록하려면 다음이 필요합니다.

- [Azure 구독](https://azure.microsoft.com/free/).
- Azure 리소스 모델은 퍼블릭 또는 Azure Government 클라우드에 배포된 [SQL Server 2017 이상의](https://www.microsoft.com/sql-server/sql-server-downloads) [가상 머신을](../../../virtual-machines/linux/quick-create-portal.md) Ubuntu Linux.
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell(5.0 이상)](/powershell/azure/install-az-ps)

## <a name="register-subscription-with-rp"></a>RP에 구독 등록

SQL IaaS 에이전트 확장에 SQL Server VM을 등록하려면 먼저 구독을 **Microsoft.SqlVirtualMachine** RP(리소스 공급자)에 등록해야 합니다. 그러면 SQL IaaS Agent 확장이 구독 내에서 리소스를 만들 수 있습니다. Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal 사용하여 리소스 공급자에 구독을 등록합니다.

1. Azure Portal을 열고 **모든 서비스** 로 이동합니다.
1. **구독** 으로 이동하여 원하는 구독을 선택합니다.
1. **구독** 페이지의 **설정** 에서 **리소스 공급자** 를 선택합니다.
1. 필터에 **sql** 을 입력하여 SQL 관련 리소스 공급자를 표시합니다.
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 **등록**, **다시 등록** 또는 **등록 해제** 를 선택합니다.


![공급자 수정](../windows/media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>명령 줄

Azure CLI 또는 Azure PowerShell을 사용하여 **SqlVirtualMachine** 공급자에 Azure 구독을 등록합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI 사용하여 리소스 공급자에 구독을 등록합니다. 

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell 사용하여 리소스 공급자에 구독을 등록합니다. 

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-vm"></a>VM 등록

Linux의 SQL IaaS 에이전트 확장은 라이선스 유형 및 SQL Server 버전만 변경할 수 있는 경량 모드에서만 사용할 수 있습니다. 제한된 기능을 사용하려면 Azure CLI 또는 Azure PowerShell을 사용하여 SQL Server VM을 경량 모드로 확장에 등록합니다. 

SQL Server 라이선스 유형을 사용량에 따라 지불하려면 종량제(`PAYG`), 자체 라이선스를 사용하려면 Azure 하이브리드 혜택(`AHUB`), [무료 DR 복제본 라이선스](../windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)를 활성화하려면 재해 복구(`DR`)로 제공합니다.

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

## <a name="verify-registration-status"></a>등록 상태 확인

Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 SQL Server VM이 이미 SQL IaaS Agent 확장에 등록되어 있는지 확인할 수 있습니다.


### <a name="azure-portal"></a>Azure Portal

Azure Portal 사용하여 등록 상태를 확인합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. SQL 가상 머신 리소스로 이동합니다.
1. 목록에서 SQL Server VM을 선택합니다. SQL Server VM이 여기에 나열되지 않는 경우 SQL IaaS Agent 확장에 등록되지 않았을 수 있습니다.

### <a name="command-line"></a>명령 줄

Azure CLI 또는 Azure PowerShell을 사용하여 현재 SQL Server VM 등록 상태를 확인합니다. `ProvisioningState` 는 `Succeeded` 등록에 성공한 것처럼 표시됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI 사용하여 등록 상태를 확인합니다.

```azurecli-interactive
az sql vm show -n <vm_name> -g <resource_group>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell 사용하여 등록 상태를 확인합니다.

```powershell-interactive
Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
```

---

오류가 발생하면 SQL Server VM이 확장에 등록되지 않은 것입니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

* [Windows VM에서 SQL Server 개요](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
* [Windows VM의 SQL Server 가격 책정 가이드](../windows/pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](../windows/doc-changes-updates-release-notes.md)
