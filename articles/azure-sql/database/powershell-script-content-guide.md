---
title: Azure PowerShell 스크립트 예제
description: Azure PowerShell 스크립트 예제를 사용하면 Azure SQL Database 및 Azure SQL Managed Instance 리소스를 만들고 관리할 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: LitKnd
ms.author: kendralittle
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: eeb0bc3ca2298db7fbb585fbe8abe03807af7690
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368634"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance의 Azure PowerShell 샘플
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance를 사용하면 Azure PowerShell을 사용하여 데이터베이스, 인스턴스 및 풀을 구성할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 AZ PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

다음 표에는 Azure SQL Database의 Azure PowerShell 샘플 스크립트에 대한 링크가 나와 있습니다.

|링크|Description|
|---|---|
|**단일 데이터베이스 및 탄력적 풀 만들기 및 구성**||
| [단일 데이터베이스를 만들고 서버 수준 방화벽 규칙을 구성](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 단일 데이터베이스를 만들고 서버 수준 IP 방화벽 규칙을 구성합니다. |
| [탄력적 풀 만들기 및 풀링된 데이터베이스 이동](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 탄력적 풀을 만들고 풀링된 데이터베이스를 이동하며 컴퓨팅 크기를 변경합니다.|
|**지역에서 복제 및 장애 조치(failover) 구성**||
| [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(failover)](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 다음 PowerShell 스크립트는 단일 데이터베이스에 대해 활성 지역 복제를 구성하고 보조 복제본으로 장애 조치(failover)합니다. |
| [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(failover)](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 다음 PowerShell 스크립트는 탄력적 풀의 데이터베이스에 대해 활성 지역 복제를 구성하고 보조 복제본으로 장애 조치(failover)합니다. |
|**장애 조치(failover) 그룹 구성**||
| [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 데이터베이스 및 장애 조치(failover) 그룹을 만들고, 장애 조치(failover) 그룹에 데이터베이스를 추가하고, 보조 서버로 장애 조치(failover)를 테스트합니다. |
| [탄력적 풀에 대한 장애 조치(failover) 그룹 구성](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 데이터베이스를 만들어 탄력적 풀에 추가하고, 탄력적 풀을 장애 조치(failover) 그룹에 추가하고, 보조 서버로 장애 조치(failover)를 테스트합니다. |
|**단일 데이터베이스 및 탄력적 풀 크기 조정**||
| [단일 데이터베이스 크기 조정](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 단일 데이터베이스의 성능 메트릭을 모니터링하고, 더 높은 컴퓨팅 크기로 확장하고, 성능 메트릭 중 하나에 경고 규칙을 만듭니다. |
| [탄력적 풀 크기 조정](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 탄력적 풀의 성능 메트릭을 모니터링하고, 더 높은 컴퓨팅 크기로 확장하고, 성능 메트릭 중 하나에 경고 규칙을 만듭니다. |
| **감사 및 위협 감지** |
| [감사 및 위협 감지 구성](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 데이터베이스에 대한 감사 및 위협 탐지 정책을 구성합니다. |
| **데이터베이스 복원, 복사 및 가져오기**||
| [데이터베이스 복원](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트에서는 지역 중복 백업에서 데이터베이스를 복원하고 삭제된 데이터베이스를 최신 백업으로 복원합니다. |
| [새 서버에 데이터베이스 복사](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 새 서버에 기존 데이터베이스의 복사본을 만듭니다. |
| [bacpac 파일에서 데이터베이스 가져오기](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 bacpac 파일에서 데이터베이스를 Azure SQL Database로 가져옵니다. |
| **데이터베이스 간 데이터 동기화**||
| [데이터베이스 간 데이터 동기화](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure SQL Database의 여러 데이터베이스 간에 동기화를 수행하도록 Azure SQL 데이터 동기화를 구성합니다. |
| [SQL Database 및 SQL Server 온-프레미스 간의 데이터 동기화](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure SQL Database의 데이터베이스와 SQL Server 온-프레미스 데이터베이스 간에 동기화를 수행하도록 데이터 동기화를 구성합니다. |
| [SQL 데이터 동기화 동기화 스키마 업데이트](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 데이터 동기화 동기화 스키마에서 항목을 추가하거나 제거합니다. |
|||

[단일 데이터베이스 Azure PowerShell API](single-database-manage.md#powershell)에 대해 자세히 알아봅니다. 

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

다음 표에는 Azure SQL - Managed Instance의 Azure PowerShell 샘플 스크립트에 대한 링크가 나와 있습니다.

|링크|Description|
|---|---|
|**관리되는 인스턴스 만들기 및 구성**||
| [관리되는 인스턴스 만들기 및 구성](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | 이 PowerShell 스크립트는 Azure PowerShell을 사용하여 관리형 인스턴스를 만들고 관리하는 방법을 보여줍니다. |
| [Azure Resource Manager 템플릿을 사용하여 관리형 인스턴스 만들기 및 관리](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure PowerShell 및 Azure Resource Manager를 사용하여 관리형 인스턴스를 만들고 관리하는 방법을 보여줍니다.|
| [다른 지역의 관리형 인스턴스로 데이터베이스 복원](../managed-instance/scripts/restore-geo-backup.md) | 이 PowerShell 스크립트는 하나의 데이터베이스의 백업을 수행하고 다른 지역으로 복원합니다. 이를 지역 복원 재해 복구 시나리오라고 합니다. |
| **투명한 데이터 암호화 구성**||
| [Azure Key Vault의 사용자 고유 키를 사용하여 관리형 인스턴스에서 투명한 데이터 암호화 관리](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 Azure Key Vault의 키를 사용하여 Azure SQL Managed Instance에 대한 Bring Your Own Key 시나리오에서 투명한 데이터 암호화를 구성합니다.|
|**장애 조치(failover) 그룹 구성**||
| [관리형 인스턴스에 대한 장애 조치(failover) 그룹 구성](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 두 개의 관리형 인스턴스를 만들고 이를 장애 조치(failover) 그룹에 추가한 다음, 기본 관리형 인스턴스에서 보조 관리형 인스턴스로 장애 조치(failover)를 테스트합니다. |
|||

[Azure SQL Managed Instance용 PowerShell cmdlet](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)에 대해 자세히 알아보세요.

---

## <a name="additional-resources"></a>추가 리소스

이 페이지에 나오는 예제에서는 Azure SQL 리소스를 만들고 관리하기 위해 [PowerShell cmdlet](/powershell/module/az.sql/)을 사용합니다. 쿼리를 실행하고 많은 데이터베이스 작업을 수행하기 위한 추가 cmdlet은 [sqlserver](/powershell/module/sqlserver/) 모듈에 있습니다. 자세한 내용은 [SQL Server PowerShell](/sql/powershell/sql-server-powershell/)을 참조하세요.