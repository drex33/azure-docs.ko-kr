---
title: SQL Server Linux용 IaaS 에이전트 확장
description: 이 문서에서는 SQL Server IaaS 에이전트 확장을 통해 SQL Server on Linux Azure VM의 관리 관련 관리 작업을 자동화하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.openlocfilehash: 98e448358d31fda34a8bb84024be6daf157d0f78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103609"
---
# <a name="sql-server-iaas-agent-extension-for-linux"></a>SQL Server Linux용 IaaS 에이전트 확장
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-iaas-agent-extension-automate-management.md)
> * [Linux](sql-server-iaas-agent-extension-linux.md)

SQL Server IaaS 에이전트 확장(SqlIaasExtension)은 SQL Server on Linux Azure VM(Virtual Machines)에서 실행되어 관리 및 관리 작업을 자동화합니다. 

이 문서는 확장에 대한 개요를 제공합니다. 자세한 내용은 [확장에 등록을](sql-iaas-agent-extension-register-vm-linux.md) 참조하세요. 


## <a name="overview"></a>개요

SQL Server IaaS 에이전트 확장을 사용하면 Azure Portal 통합할 수 있으며 Azure VM을 SQL Server on Linux 다음과 같은 이점이 있습니다. 

- **규정 준수:** 확장은 제품 약관에 지정된 대로 Azure 하이브리드 혜택 사용하도록 설정되었음을 Microsoft에 알리는 요구 사항을 충족하는 간소화된 방법을 제공합니다. 이 프로세스로 각 리소스마다 라이선스 등록 양식을 관리할 필요가 없습니다.  

- **간소화된 라이선스 관리:** 확장은 SQL Server 라이선스 관리를 간소화하고 Azure Portal, Azure PowerShell 또는 Azure CLI 사용하여 Azure 하이브리드 혜택 사용하도록 설정된 SQL Server VM을 신속하게 식별할 수 있도록 합니다. 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---

- **무료:** 확장과 관련된 추가 비용은 없습니다. 



## <a name="installation"></a>설치

SQL Server VM을 SQL Server IaaS 에이전트 확장에 [등록하여](sql-iaas-agent-extension-register-vm-linux.md) 가상 **머신** 리소스와는 _별도의_ _리소스인_ 구독 내에 SQL 가상 머신 리소스를 만듭니다. 확장에서 SQL Server VM을 등록 취소하면 구독에서 **SQL 가상 머신** _리소스가_ 제거되지만 실제 가상 머신은 삭제되지 않습니다.

Linux용 SQL Server IaaS 에이전트 확장은 현재 경량 모드에서만 사용할 수 있습니다. 


## <a name="verify-extension-status"></a>확장 상태 확인

Azure Portal 또는 Azure PowerShell를 사용하여 확장의 상태를 확인합니다. 

### <a name="azure-portal"></a>Azure portal

Azure Portal 사용하여 확장이 설치되었는지 확인합니다. 

Azure Portal **가상 머신** 리소스(SQL *가상 머신* 리소스가 아니라 VM에 대한 리소스)로 이동합니다. **설정** 에서 **확장** 을 선택합니다. 다음 예제와 같이 **SqlIaasExtension** 확장이 나열되어야 합니다. 

![Azure Portal SQL Server IaaS 에이전트 확장 SqlIaaSExtension의 상태를 확인합니다.](../windows/media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)




### <a name="azure-powershell"></a>Azure PowerShell

**Get-AzVMSqlServerExtension** Azure PowerShell cmdlet을 사용할 수도 있습니다.

```powershell-interactive
  Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
```

이전 명령은 에이전트가 설치되어 있는지 확인하고 일반적인 상태 정보를 제공합니다. 다음 명령을 사용하여 자동화된 Backup 및 패치에 대한 특정 상태 정보를 가져올 수 있습니다.

```powershell-interactive
 $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
 $sqlext.AutoPatchingSettings
 $sqlext.AutoBackupSettings
```

## <a name="limitations"></a>제한 사항

Linux SQL IaaS 에이전트 확장에는 다음과 같은 제한이 있습니다. 

- Ubuntu Linux 운영 체제에서 실행되는 SQL Server VM만 지원됩니다. 다른 Linux 배포판은 현재 지원되지 않습니다.
- SQL Server Ubuntu Linux Pro 실행하는 VM은 지원되지 않습니다.
- SQL Server 일반화된 이미지에서 실행되는 VM은 지원되지 않습니다.
- Azure Resource Manager 통해 배포된 SQL Server VM만 지원됩니다. 클래식 모델을 통해 배포된 SQL Server VM은 지원되지 않습니다. 
- 단일 인스턴스로만 SQL Server. 여러 인스턴스는 지원되지 않습니다. 

## <a name="privacy-statement"></a><a id="in-region-data-residency"></a> 개인정보처리방침

Azure VM 및 SQL IaaS 확장에서 SQL Server 사용하는 경우 다음 개인정보처리방침을 고려하세요. 

- **데이터 수집:** SQL IaaS 에이전트 확장은 Azure Virtual Machines SQL Server 사용할 때 고객에게 선택적 혜택을 제공하기 위해 데이터를 수집합니다. Microsoft는 고객의 사전 동의 없이 **라이선스 감사에 이 데이터를 사용하지 않습니다.** 자세한 내용은 [SQL Server 개인 정보 제공](/sql/sql-server/sql-server-privacy#non-personal-data)을 참조하세요.

- **지역 내 데이터 상주:** Azure VM 및 SQL IaaS 에이전트 확장의 SQL Server VM이 배포된 지역에서 고객 데이터를 이동하거나 저장하지 않습니다. 


## <a name="next-steps"></a>다음 단계

Azure Virtual Machines SQL Server 실행하는 방법은 Azure [Linux Virtual Machines SQL Server 무엇인가요?를 참조하세요.](sql-server-on-linux-vm-what-is-iaas-overview.md)

자세한 내용은 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.
