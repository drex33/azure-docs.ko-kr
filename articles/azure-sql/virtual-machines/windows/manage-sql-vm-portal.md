---
title: Azure Portal을 사용하여 Azure에서 SQL Server 가상 머신 관리 | Microsoft Docs
description: Azure에서 호스트되는 SQL Server VM에 대해 Azure Portal에서 SQL 가상 머신 리소스에 액세스하여 SQL Server 설정을 수정하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: a25f5bf28feb8fcb3903094f45568e96388ec0bd
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162163"
---
# <a name="manage-sql-server-vms-by-using-the-azure-portal"></a>Azure Portal을 사용하여 SQL Server VM 관리
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[Azure Portal](https://portal.azure.com)에서 [**SQL 가상 머신**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스는, SQL Server IaaS 에이전트 확장에 등록된 Azure VM(Virtual Machines)의 SQL Server 관리를 위한 독립 관리 서비스입니다. 이 리소스를 사용하여 모든 SQL Server VM을 동시에 확인하고 SQL Server 전용 설정을 수정할 수 있습니다. 

![SQL 가상 머신 리소스](./media/manage-sql-vm-portal/sql-vm-manage.png)

**SQL 가상 머신** 리소스 관리 지점은 시작, 중지, 다시 시작 등, VM 관리에 사용되는 **가상 머신** 리소스와 다릅니다. 


## <a name="prerequisite"></a>필수 조건 

**SQL 가상 머신** 리소스는 [SQL IaaS 에이전트 확장에 등록](sql-agent-extension-manually-register-single-vm.md)된 SQL Server VM에 한해 사용할 수 있습니다. 


## <a name="access-the-resource"></a>리소스 액세스

**SQL 가상 머신** 리소스에 액세스하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 
1. **모든 서비스** 를 선택합니다. 
1. 검색 상자에 **SQL 가상 머신** 을 입력합니다.
1. (선택 사항): **SQL 가상 머신** 옆의 별표를 선택하여 **즐겨찾기** 메뉴에 이 옵션을 추가합니다. 
1. **SQL 가상 머신** 을 선택합니다. 

   ![모든 서비스에서 SQL Server 가상 머신 찾기](./media/manage-sql-vm-portal/sql-vm-search.png)

1. 포털은 구독 내에서 사용할 수 있는 모든 SQL Server VM을 나열합니다. 관리하려는 VM을 선택하여 **SQL 가상 머신** 리소스를 엽니다. SQL Server VM이 표시되지 않는 경우 검색 상자를 사용합니다. 

   ![사용 가능한 모든 SQL Server VM](./media/manage-sql-vm-portal/all-sql-vms.png)

   SQL Server VM을 선택하면 **SQL 가상 머신** 리소스가 열립니다. 


   ![SQL 가상 머신 리소스 보기](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 가상 머신** 리소스는 SQL Server 전용 설정을 관리하기 위한 것입니다. **가상 머신** 상자에서 VM 이름을 선택하면 VM과 관련되지만 SQL Server 전용이 아닌 설정을 열 수 있습니다. 


## <a name="license-and-edition"></a>라이선스 및 에디션 

SQL 가상 머신 리소스의 **구성** 페이지를 사용하여, SQL Server 라이선싱 메타데이터를 [재해 복구를 위한 무료 Azure 복제본](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)에 대해 **종량제**, **Azure 하이브리드 혜택** 또는 **HA/DR** 로 변경합니다.



![SQL 가상 머신 리소스를 사용하여 Azure Portal의 SQL Server VM 메타데이터의 버전 및 에디션 변경](./media/manage-sql-vm-portal/sql-vm-license-edition.png)

**구성** 페이지에서도 **Enterprise**, **Standard** 또는 **Developer** 등의 SQL Server 에디션을 수정할 수 있습니다. 

Azure Portal의 라이선스 및 에디션 메타데이터 변경은 SQL Server 버전이 내부적으로 VM으로 수정된 후에만 지원됩니다. 자세한 내용을 보려면 Azure VM의 SQL Server [버전](change-sql-server-version.md) 및 [에디션](change-sql-server-edition.md)을 변경하세요. 

## <a name="storage"></a>Storage 

SQL 가상 머신 리소스의 **구성** 페이지를 사용하여 데이터, 로그 및 tempdb 드라이브를 변경합니다. 

![SQL 가상 머신 리소스를 사용하여 Azure Portal 스토리지 확장](./media/manage-sql-vm-portal/sql-vm-storage-configuration.png)

## <a name="patching"></a>패치

SQL 가상 머신 리소스의 **패치** 페이지를 사용하여 VM의 자동 패치를 사용하도록 설정하고 중요로 표시된 Windows 및 SQL Server 업데이트를 자동으로 설치합니다. 또한 일일 패치 실행 등, 유지 관리 일정과 유지 관리를 위한 로컬 시작 시간 및 유지 관리 기간을 구성할 수도 있습니다. 


![SQL 가상 머신 리소스를 사용하여 Azure Portal에서 자동화된 패치 및 일정 구성](./media/manage-sql-vm-portal/sql-vm-automated-patching.png)


자세한 내용은 [자동화된 패치](automated-patching.md)를 참조하세요. 



## <a name="backups"></a>Backup

SQL 가상 머신 리소스의 **백업** 페이지를 사용하여 보존 기간, 사용할 스토리지 계정, 암호화, 시스템 데이터베이스 백업 여부 및 백업 일정과 같은 자동화된 백업 설정을 구성할 수 있습니다. 

![SQL 가상 머신 리소스를 사용하여 Azure Portal에서 자동화된 백업 및 일정 구성](./media/manage-sql-vm-portal/sql-vm-automated-backup.png)

자세한 내용은 [자동화된 패치](automated-backup.md)를 참조하세요. 


## <a name="high-availability-preview"></a>고가용성(미리 보기)

SQL 가상 머신 리소스의 **고가용성** 페이지를 사용하여 Windows Server 장애 조치(failover) 클러스터를 만들고 Always On 가용성 그룹, 가용성 그룹 수신기 및 Azure Load Balancer를 구성합니다. Azure Portal을 사용한 고가용성 구성은 현재 미리 보기로 제공됩니다. 


![SQL 가상 머신 리소스를 사용하여 Azure Portal의 Windows Server 장애 조치(failover) 클러스터 및 Always On 가용성 그룹 구성](./media/manage-sql-vm-portal/sql-vm-high-availability.png)


자세한 내용은 [Azure Portal 사용하여 가용성 그룹 구성](availability-group-azure-portal-configure.md)을 참조하세요.

## <a name="security-configuration"></a>보안 구성 

SQL 가상 머신 리소스의 **보안 구성** 페이지를 사용하여 사용할 포트, SQL 인증 사용 여부 등의 SQL Server 보안 설정을 구성하고 Azure Key Vault 통합을 사용하도록 설정할 수 있습니다. 

![SQL 가상 머신 리소스를 사용하여 Azure Portal의 SQL Server 보안 구성](./media/manage-sql-vm-portal/sql-vm-security-configuration.png)

자세한 내용은 [보안 모범 사례](security-considerations-best-practices.md)를 참조하세요.


## <a name="security-center"></a>Security Center 

SQL 가상 머신 리소스의 **Security Center** 페이지를 사용하여 SQL 가상 머신 블레이드에서 직접 Security Center 권장 사항을 볼 수 있습니다. 자세한 내용은 [Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md)을 참조하세요. 

![SQL 가상 머신 리소스를 사용하여 Azure Portal의 SQL Server Security Center 설정 구성](./media/manage-sql-vm-portal/sql-vm-security-center.png)


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Azure vm에서 SQL Server의 새로운 기능](doc-changes-updates-release-notes-whats-new.md)


