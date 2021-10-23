---
title: SQL Server IaaS 에이전트 확장은 무엇입니까?
description: 이 문서에서는 SQL Server IaaS 에이전트 확장을 사용하여 Azure VM에서 SQL Server의 특정 관리 작업의 자동 관리하는 방법을 설명합니다. 자동화된 백업, 자동화된 패치, Azure Key Vault 통합, 라이선스 관리, 스토리지 구성, 모든 SQL Server VM 인스턴스의 중앙 관리 등이 포함됩니다.
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 9/01/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: e5a122fb23b7ebcfbbbf008dacc675eddf0c3cb0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247962"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS 에이전트 확장을 사용하여 관리 자동화
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 에이전트 확장(SqlIaasExtension)은 관리 작업을 자동화하기 위해 Azure 가상 머신의 SQL Server에서 실행됩니다. 

이 문서는 확장에 대한 개요를 제공합니다. SQL Server IaaS 확장을 Azure VM의 SQL Server에 설치하려면 [자동 설치](sql-agent-extension-automatic-registration-all-vms.md), [단일 VM](sql-agent-extension-manually-register-single-vm.md) 또는 [대량 VM](sql-agent-extension-manually-register-vms-bulk.md) 문서를 참조하세요. 

> [!NOTE]
> 2021년 9월부터 전체 모드로 SQL IaaS 확장에 등록하기 위해 더 이상 SQL Server 서비스를 다시 시작하지 않아도 됩니다. 

## <a name="overview"></a>개요

SQL Server IaaS 에이전트 확장을 사용하면 Azure Portal과 통합할 수 있으며, 관리 모드에 따라 Azure VM의 SQL Server에 대한 다양한 기능 이점을 활용할 수 있습니다. 

- **기능 이점**: 확장을 통해 포털 관리, 라이선스 유연성, 자동화된 백업, 자동화된 패치 등의 다양한 자동화 기능 이점을 활용할 수 있습니다. 자세한 내용은 이 문서의 [기능 이점](#feature-benefits)을 참조하세요. 

- **규정 준수**: 제품 약관에 지정된 대로 Azure 하이브리드 혜택이 구현되었음을 Microsoft에 알려야 하는 요구 사항을 간편하게 충족할 수 있습니다. 이 프로세스로 각 리소스마다 라이선스 등록 양식을 관리할 필요가 없습니다.  

- **무료**: 세 가지 관리 효율성 모드 모두에서 확장은 완전히 무료로 제공됩니다. 확장 또는 관리 모드 변경과 관련된 추가 비용은 없습니다. 

- **간소화된 라이선스 관리**: 확장을 통해 SQL Server 라이선스 관리가 간소화되고 [Azure Portal](manage-sql-vm-portal.md), Azure CLI 또는 PowerShell을 사용하여 Azure 하이브리드 혜택이 활성화된 SQL Server VM을 빠르게 식별할 수 있습니다. 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> SQL IaaS 에이전트 확장은 Azure Virtual Machines 내에서 SQL Server를 사용하는 고객에게 선택적 혜택을 제공하기 위한 목적으로 데이터를 수집합니다. Microsoft는 고객의 사전 동의 없이 라이선스 감사에 이 데이터를 사용하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 제공](/sql/sql-server/sql-server-privacy#non-personal-data)을 참조하세요.


## <a name="feature-benefits"></a>기능 이점 

SQL Server IaaS 에이전트 확장은 SQL Server VM 관리에 대한 다양한 이점을 제공합니다. 

다음 표에서 이러한 이점을 자세히 설명합니다. 


| 기능 | 설명 |
| --- | --- |
| **포털 관리** | [포털에서 관리](manage-sql-vm-portal.md)가 가능해 단일 위치에서 모든 SQL Server VM을 볼 수 있고 포털에서 직접 특정 SQL 기능을 사용 또는 사용하지 않도록 설정할 수 있습니다. <br/> 관리 모드: 경량 및 전체|  
| **자동화된 백업** |VM에 있는 SQL Server의 기본 인스턴스나 [제대로 설치된](./frequently-asked-questions-faq.yml) 명명된 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 백업(Resource Manager)](automated-backup-sql-2014.md)을 참조하세요. <br/> 관리 모드: 전체|
| **자동화된 패치** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 및 SQL Server 보안 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 패치(Resource Manager)](automated-patching.md)를 참조하세요. <br/> 관리 모드: 전체|
| **Azure Key Vault 통합** |SQL Server VM에서 Azure Key Vault를 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 Azure Key Vault 통합 구성(Resource Manager)](azure-key-vault-integration-configure.md)을 참조하세요. <br/> 관리 모드: 전체|
| **포털에서 디스크 사용률 보기** | Azure Portal에서 SQL 데이터 파일의 디스크 사용률에 대한 그래픽 표현을 볼 수 있습니다.  <br/> 관리 모드: 전체 | 
| **유연한 라이선스** | BYOL(Azure 하이브리드 혜택)에서 종량제 라이선스 모델로 [원활하게 전환](licensing-model-azure-hybrid-benefit-ahb-change.md)하여 비용을 절감합니다. <br/> 관리 모드: 경량 및 전체| 
| **유연한 버전/에디션** | SQL Server의 [버전](change-sql-server-version.md) 또는 [에디션](change-sql-server-edition.md)을 변경하려는 경우 전체 SQL Server VM을 배포할 필요 없이 Azure Portal 내에서 메타데이터를 업데이트할 수 있습니다.  <br/> 관리 모드: 경량 및 전체| 
| **Security Center 포털 통합** | [Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md)을 사용하도록 설정한 경우 Azure Portal의 [SQL 가상 머신](manage-sql-vm-portal.md) 리소스에서 직접 Security Center 권장 사항을 볼 수 있습니다. 자세한 내용은 [보안 모범 사례](security-considerations-best-practices.md)를 참조하세요.  <br/> 관리 모드: 경량 및 전체| 


## <a name="management-modes"></a>관리 모드

세 가지 관리 모드에서 SQL IaaS 확장을 등록할 수 있습니다. 

- **경량** 모드는 확장 이진 파일을 VM에 복사하지만 에이전트를 설치하지는 않습니다. 경량 모드에서는 단지 SQL Server 라이선스 유형 및 버전의 변경을 지원하고 제한된 포털 관리를 제공합니다. 여러 인스턴스가 있거나 FCI(장애 조치(failover) 클러스터 인스턴스)에 참여하는  SQL Server VM에서 이 옵션을 사용합니다. [자동 등록](sql-agent-extension-automatic-registration-all-vms.md) 기능을 사용할 때나 수동 등록 도중 관리 유형이 지정되지 않은 경우 경량 모드가 기본 관리 모드입니다. 경량 모드를 사용하는 경우 메모리 또는 CPU에 영향을 주지 않으며 관련 비용이 없습니다. 

- **전체** 모드에서는 전체 기능을 제공하기 위해 SQL IaaS 에이전트를 VM에 설치합니다. 단일 인스턴스가 있는 SQL Server VM을 관리하는 데 이 옵션을 사용합니다. 전체 모드는 메모리 및 CPU에 최소한의 영향을 주는 두 개의 Windows 서비스를 설치 합니다. 이러한 서비스는 작업 관리자를 통해 모니터링할 수 있습니다. 전체 관리 모드를 사용하는 것과 관련된 비용은 없습니다. 시스템 관리자 권한이 필요합니다. 2021년 9월부터 전체 관리 모드에서 SQL Server VM을 등록하는 경우 SQL Server 서비스를 다시 시작하지 않아도 됩니다. 

- **에이전트 없음** 모드는 Windows Server 2008에 설치된 SQL Server 2008 및 SQL Server 2008 R2 전용 모드입니다. 에이전트 없음 모드를 사용하는 경우 메모리 또는 CPU에는 영향을 주지 않습니다. NoAgent 관리 효율성 모드 사용에 대한 비용은 없고, SQL Server가 다시 시작되지 않으며, 에이전트가 VM에 설치되지 않습니다. 

Azure PowerShell을 사용하여 SQL Server IaaS Agent의 현재 모드를 확인할 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>설치

SQL Server IaaS 에이전트 확장에 SQL Server VM을 등록하면 가상 머신 리소스와는 별도 리소스인 [**SQL 가상 머신** 리소스](manage-sql-vm-portal.md)가 구독 내에 생성됩니다. 확장에서 SQL Server VM의 등록을 취소하면 **SQL 가상 머신** _리소스_ 가 제거되지만 실제 가상 머신은 삭제되지 않습니다.

Azure Portal을 통해 SQL Server VM Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 확장에 전체가 등록됩니다. 그러나 Azure 가상 머신에 SQL Server를 자동으로 설치하거나 사용자 지정 VHD에서 Azure 가상 머신을 프로비전하도록 선택하는 경우에는 모든 기능 이점을 사용할 수 있도록 SQL IaaS 확장에 SQL Server VM를 등록해야 합니다. 

경량 모드에서 확장을 등록하면 이진 파일이 복사되지만 에이전트는 VM에 설치되지 않습니다. 확장이 전체 관리 모드에서 설치되면 에이전트가 VM에 설치됩니다. 

확장에 등록하는 세 가지 방법이 있습니다. 
- [구독에서 모든 현재 및 미래의 VM에 대해 자동으로](sql-agent-extension-automatic-registration-all-vms.md)
- [단일 VM에 대해 수동으로](sql-agent-extension-manually-register-single-vm.md)
- [여러 VM에 대해 수동으로](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>명명된 인스턴스 지원

SQL Server IaaS 에이전트 확장은 가상 머신에서 사용할 수 있는 유일한 SQL Server 인스턴스인 경우 SQL Server의 명명된 인스턴스에서 작동합니다. VM에 명명 된 SQL Server 인스턴스가 여러 개 있고 기본 인스턴스가 없는 경우 SQL IaaS 확장은 경량 모드로 등록 되 고 가장 높은 버전의 인스턴스 또는 첫 번째 인스턴스 (모든 인스턴스가 동일한 버전을 사용 하는 경우)를 선택 합니다. 

SQL Server의 명명된 인스턴스를 사용하려면 Azure 가상 머신을 배포하고, 명명된 단일 SQL Server 인스턴스를 설치하고, [SQL IaaS 확장](sql-agent-extension-manually-register-single-vm.md)에 등록합니다.

또는 Azure Marketplace SQL Server 이미지를 사용하여 명명된 인스턴스를 사용하려면 다음 단계를 수행합니다. 

   1. Azure Marketplace에서 SQL Server VM을 배포합니다. 
   1. SQL IaaS 에이전트 확장에서 SQL Server VM [등록을 취소](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)합니다. 
   1. SQL Server VM 내에서 SQL Server를 완전히 제거합니다.
   1. SQL Server VM 내에 명명된 인스턴스를 사용하여 SQL Server를 설치합니다. 
   1. [SQL IaaS 에이전트 확장에서 VM 등록합니다](sql-agent-extension-manually-register-single-vm.md#full-mode). 

## <a name="verify-status-of-extension"></a>확장의 상태 확인

Azure Portal 또는 Azure PowerShell를 사용하여 확장의 상태를 확인합니다. 

### <a name="azure-portal"></a>Azure portal

확장이 Azure Portal에 설치되어 있는지 확인합니다. 

가상 머신 창에서 **모든 설정** 을 선택하고 **확장** 을 선택합니다. **SqlIaasExtension** 확장이 나열되어야 합니다.

![Azure Portal의 SQL Server IaaS 에이전트 확장 상태](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


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

SQL IaaS 에이전트 확장은 다음만 지원합니다. 

- Azure Resource Manager를 통해 배포된 SQL Server VM. 클래식 모델을 통해 배포된 SQL Server VM은 지원되지 않습니다. 
- 공용 또는 Azure Government 클라우드에 배포된 SQL Server VM. 다른 프라이빗 또는 정부 클라우드로의 배포는 지원되지 않습니다. 
- 경량 모드에서 FCIs (장애 조치 (Failover) 클러스터 인스턴스) 
- 경량 모드의 단일 VM에 여러 인스턴스가 있는 명명 된 인스턴스 



## <a name="in-region-data-residency"></a>지역 내 데이터 보존

Azure SQL 가상 머신과 SQL IaaS 에이전트 확장은 배포된 리전 외부로 데이터를 이동하거나 저장하지 않습니다.

## <a name="next-steps"></a>다음 단계

SQL Server IaaS 확장을 Azure VM의 SQL Server에 설치하려면 [자동 설치](sql-agent-extension-automatic-registration-all-vms.md), [단일 VM](sql-agent-extension-manually-register-single-vm.md) 또는 [대량 VM](sql-agent-extension-manually-register-vms-bulk.md) 문서를 참조하세요.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server란?](sql-server-on-azure-vm-iaas-what-is-overview.md)을 참조하세요.

자세한 내용은 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.
