---
title: 새 지역으로 리소스 이동
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 데이터베이스 또는 관리되는 인스턴스를 다른 지역으로 이동하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: LitKnd
ms.author: kendralittle
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 862e195b48527257ada62de47d858bdb3a4416fe
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133364650"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>새 지역으로 리소스 이동 - Azure SQL Database 및 Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에서는 데이터베이스 또는 관리되는 인스턴스를 새 지역으로 이동하는 방법에 대한 일반적인 워크플로를 설명합니다.

## <a name="overview"></a>개요

기존 데이터베이스 또는 관리되는 인스턴스를 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어, 비즈니스를 새로운 지역으로 확장하고 있으며 새 고객층에 맞게 최적화하려고 합니다. 또는 규정 준수를 위해 작업을 다른 지역으로 이동해야 합니다. 또는 Azure는 더 나은 근접성을 제공하고 고객 환경을 개선하는 새로운 지역을 출시했습니다.  

이 문서에서는 리소스를 다른 지역으로 이동하는 일반적인 워크플로를 제공합니다. 워크플로는 다음 단계로 구성됩니다.

1. 이동을 위한 사전 요구 사항을 확인합니다.
1. 범위에서 리소스를 이동할 준비를 합니다.
1. 준비 프로세스를 모니터링합니다.
1. 이동 프로세스를 테스트합니다.
1. 실제 이동을 시작합니다.
1. 원본 지역에서 리소스를 제거합니다.

> [!NOTE]
> 이 문서는 Azure 공용 클라우드 내에서 또는 동일한 소버린 클라우드 내에서의 마이그레이션에 적용됩니다.

> [!NOTE]
> Azure SQL 데이터베이스 및 탄력적 풀을 다른 Azure 지역으로 이동하려면 Azure Resource Mover(미리 보기)를 사용할 수도 있습니다. 동일한 작업을 수행하기 위한 자세한 단계는 [이 자습서](../../resource-mover/tutorial-move-region-sql.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>데이터베이스 이동

### <a name="verify-prerequisites"></a>필수 구성 요소 확인

1. 각 원본 서버에 대한 대상 서버를 만듭니다.
1. [PowerShell](scripts/create-and-configure-database-powershell.md)을 사용하여 적절한 예외로 방화벽을 구성합니다.  
1. 올바른 로그인을 사용하여 서버를 구성합니다. 구독 관리자 또는 SQL Server 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다. 자세한 내용은 [재해 복구 후 Azure SQL Database 보안을 관리하는 방법](active-geo-replication-security-configure.md)을 참조하세요.
1. 데이터베이스가 TDE(투명한 데이터 암호화)로 암호화되고 Azure Key Vault에서 BYOK(Bring Your Own Key) 또는 CMK(고객 관리형 키)를 사용하는 경우, 대상 지역에 올바른 암호화 자료를 프로비저닝해야 합니다. 
    - 이 작업을 수행하는 가장 간단한 방법은 기존 키 자격 증명 모음의 암호화 키(원본 서버에서 TDE 보호기로 사용됨)를 대상 서버에 추가한 다음, 대상 서버에서 키를 TDE 보호기로 설정하는 것입니다.
      > [!NOTE]
      > 이제 한 지역의 서버 또는 관리형 인스턴스를 다른 지역의 주요 자격 증명 모음에 연결할 수 있습니다.
    - 대상 서버가 이전 암호화 키(데이터베이스 백업 복원에 필요)에 액세스하도록 보장하는 가장 좋은 방법은 원본 서버에서 [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet을 실행하거나, 원본 관리형 인스턴스에서 [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) cmdlet을 실행하여 사용 가능한 키 목록을 반환하고 해당 키를 대상 서버에 추가하는 것입니다.
    - 대상 서버에서 고객 관리형 TDE를 구성하는 방법에 대한 자세한 내용과 모범 사례는 [Azure Key Vault에서 고객 관리형 키를 사용한 Azure SQL 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.
    - 키 자격 증명 모음을 새 지역으로 이동하려면 [지역 간에 Azure 키 자격 증명 모음 이동](../../key-vault/general/move-region.md)을 참조하세요. 
1. 데이터베이스 수준 감사를 사용하도록 설정된 경우 이를 사용하지 않고 대신 서버 수준 감사를 사용하도록 설정합니다. 장애 조치(failover) 후 데이터베이스 수준 감사에는 이동 후 원하지 않거나 가능하지 않은 지역 간 트래픽이 필요합니다.
1. 서버 수준 감사의 경우 다음을 확인합니다.
   - 기존 감사 로그가 포함된 스토리지 컨테이너, Log Analytics 또는 이벤트 허브가 대상 지역으로 이동됩니다.
   - 감사는 대상 서버에서 구성됩니다. 자세한 내용은 [SQL Database 감사 시작](../../azure-sql/database/auditing-overview.md)을 참조하세요.
1. 인스턴스에 LTR(장기 보존 정책)이 있는 경우 기존 LTR 백업이 현재 서버와 연결된 상태로 유지됩니다. 대상 서버가 다르기 때문에 서버가 삭제되더라도 원본 서버를 사용하여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다.

      > [!NOTE]
      > 이는 소버린 클라우드와 공용 지역 간에 이동하는 데 충분하지 않습니다. 이러한 마이그레이션을 수행하려면 현재 지원되지 않는 대상 서버로 LTR 백업을 이동해야 합니다.

### <a name="prepare-resources"></a>리소스 준비

1. 원본 서버와 대상 서버 간에 [장애 조치(failover) 그룹](failover-group-add-single-database-tutorial.md#2---create-the-failover-group)을 만듭니다.  
1. 장애 조치(failover) 그룹으로 이동하려는 데이터베이스를 추가합니다.
  
    추가된 모든 데이터베이스의 복제는 자동으로 시작됩니다. 자세한 내용은 [단일 데이터베이스에서 장애 조치(failover) 그룹 사용에 대한 모범 사례](auto-failover-group-overview.md#best-practices-for-sql-database)를 참조하세요.

### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

정기적으로 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup)을 호출하여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. `Get-AzSqlDatabaseFailoverGroup`의 출력 개체에는 **ReplicationState** 에 대한 속성이 포함되어 있습니다.

- **ReplicationState = 2**(CATCH_UP)는 데이터베이스가 동기화되었으며 안전하게 장애 조치할 수 있음을 나타냅니다.
- **ReplicationState = 0**(SEEDING)은 데이터베이스가 아직 시드되지 않아 장애 조치(failover) 시도가 실패했음을 나타냅니다.

### <a name="test-synchronization"></a>동기화 테스트

**ReplicationState** 가 `2`인 경우, 보조 엔드포인트 `<fog-name>.secondary.database.windows.net`을 사용하여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결하고 데이터베이스에 대한 쿼리를 수행하여 연결, 적절한 보안 구성 및 데이터 복제를 보장합니다.

### <a name="initiate-the-move"></a>이동 시작

1. 보조 엔드포인트 `<fog-name>.secondary.database.windows.net`을 사용하여 대상 서버에 연결합니다.
1. [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)을 사용하여 보조 관리되는 인스턴스를 전체 동기화를 사용하는 기본으로 전환합니다. 이 작업은 성공적으로 수행되거나 롤백됩니다.
1. DNS CNAME 진입점이 대상 지역 IP 주소를 가리키는지 확인하기 위해 `nslook up <fog-name>.secondary.database.windows.net`을 사용하여 명령이 성공적으로 완료되었는지 확인합니다. 스위치 명령이 실패하면 CNAME은 업데이트되지 않습니다.

### <a name="remove-the-source-databases"></a>원본 데이터베이스 제거

이동이 완료되면 불필요한 요금을 방지하기 위해 원본 지역의 리소스를 제거합니다.

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)을 사용하여 장애 조치(failover) 그룹을 삭제합니다.
1. 원본 서버의 각 데이터베이스에 대해 [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)를 사용하여 각 원본 데이터베이스를 삭제합니다. 그러면 지역 복제 링크가 자동으로 종료됩니다.
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)를 사용하여 원본 서버를 삭제합니다.
1. 키 자격 증명 모음, 감사 스토리지 컨테이너, 이벤트 허브, Azure AD(Azure Active Directory) 인스턴스 및 기타 종속 리소스를 제거하여 요금 청구를 중지합니다.

## <a name="move-elastic-pools"></a>탄력적 풀 이동

### <a name="verify-prerequisites"></a>필수 구성 요소 확인

1. 각 원본 서버에 대한 대상 서버를 만듭니다.
1. [PowerShell](scripts/create-and-configure-database-powershell.md)을 사용하여 적절한 예외로 방화벽을 구성합니다.
1. 올바른 로그인을 사용하여 서버를 구성합니다. 구독 관리자 또는 서버 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다. 자세한 내용은 [재해 복구 후 Azure SQL Database 보안을 관리하는 방법](active-geo-replication-security-configure.md)을 참조하세요.
1. 데이터베이스가 투명한 데이터 암호화로 암호화되고 Azure Key Vault에서 사용자 고유의 암호화 키를 사용하는 경우, 대상 지역에 올바른 암호화 자료를 프로비전해야 합니다.
1. 각 원본 탄력적 풀에 대한 대상 탄력적 풀을 만들어 동일한 서비스 계층에 동일한 이름과 크기를 가진 풀이 만들어졌는지 확인합니다.
1. 데이터베이스 수준 감사를 사용하도록 설정된 경우 이를 사용하지 않고 대신 서버 수준 감사를 사용하도록 설정합니다. 장애 조치(failover) 후 데이터베이스 수준 감사에는 이동 후 원하지 않거나 가능하지 않은 지역 간 트래픽이 필요합니다.
1. 서버 수준 감사의 경우 다음을 확인합니다.
    - 기존 감사 로그가 포함된 스토리지 컨테이너, Log Analytics 또는 이벤트 허브가 대상 지역으로 이동됩니다.
    - 감사 구성은 대상 서버에서 구성됩니다. 자세한 내용은 [SQL Database 감사](../../azure-sql/database/auditing-overview.md)를 참조하세요.
1. 인스턴스에 LTR(장기 보존 정책)이 있는 경우 기존 LTR 백업이 현재 서버와 연결된 상태로 유지됩니다. 대상 서버가 다르기 때문에 서버가 삭제되더라도 원본 서버를 사용하여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다.

      > [!NOTE]
      > 이는 소버린 클라우드와 공용 지역 간에 이동하는 데 충분하지 않습니다. 이러한 마이그레이션을 수행하려면 현재 지원되지 않는 대상 서버로 LTR 백업을 이동해야 합니다.

### <a name="prepare-to-move"></a>이동 준비

1. 원본 서버의 각 탄력적 풀과 대상 서버의 해당하는 탄력적 풀 사이에 별도의 [장애 조치(failover) 그룹](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group)을 만듭니다.
1. 풀의 모든 데이터베이스를 장애 조치(failover) 그룹에 추가합니다.

    추가된 데이터베이스의 복제가 자동으로 시작됩니다. 자세한 내용은 [탄력적 풀을 사용하는 장애 조치(failover) 그룹에 대한 모범 사례](auto-failover-group-overview.md#best-practices-for-sql-database)를 참조하세요.

      > [!NOTE]
      > 여러 탄력적 풀을 포함하는 장애 조치(failover) 그룹을 만들 수 있지만 각 풀에 대해 별도의 장애 조치(failover) 그룹을 만드는 것이 좋습니다. 이동해야 하는 여러 탄력적 풀에 대량의 데이터베이스가 있는 경우 준비 단계를 병렬로 실행한 다음, 이동 단계를 병렬로 시작할 수 있습니다. 이 프로세스는 동일한 장애 조치(failover) 그룹에 여러 개의 탄력적 풀을 사용하는 것에 비해 확장성이 향상되고 시간이 적게 소요됩니다.

### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

정기적으로 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup)을 호출하여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. `Get-AzSqlDatabaseFailoverGroup`의 출력 개체에는 **ReplicationState** 에 대한 속성이 포함되어 있습니다.

- **ReplicationState = 2**(CATCH_UP)는 데이터베이스가 동기화되었으며 안전하게 장애 조치할 수 있음을 나타냅니다.
- **ReplicationState = 0**(SEEDING)은 데이터베이스가 아직 시드되지 않아 장애 조치(failover) 시도가 실패했음을 나타냅니다.

### <a name="test-synchronization"></a>동기화 테스트

**ReplicationState** 가 `2`인 경우, 보조 엔드포인트 `<fog-name>.secondary.database.windows.net`을 사용하여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결하고 데이터베이스에 대한 쿼리를 수행하여 연결, 적절한 보안 구성 및 데이터 복제를 보장합니다.

### <a name="initiate-the-move"></a>이동 시작

1. 보조 엔드포인트 `<fog-name>.secondary.database.windows.net`을 사용하여 대상 서버에 연결합니다.
1. [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)을 사용하여 보조 관리되는 인스턴스를 전체 동기화를 사용하는 기본으로 전환합니다. 이 작업은 성공적으로 수행되거나 롤백됩니다.
1. DNS CNAME 진입점이 대상 지역 IP 주소를 가리키는지 확인하기 위해 `nslook up <fog-name>.secondary.database.windows.net`을 사용하여 명령이 성공적으로 완료되었는지 확인합니다. 스위치 명령이 실패하면 CNAME은 업데이트되지 않습니다.

### <a name="remove-the-source-elastic-pools"></a>원본 탄력적 풀 제거

이동이 완료되면 불필요한 요금을 방지하기 위해 원본 지역의 리소스를 제거합니다.

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)을 사용하여 장애 조치(failover) 그룹을 삭제합니다.
1. [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)을 사용하여 원본 서버에서 각 원본 탄력적 풀을 삭제합니다.
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)를 사용하여 원본 서버를 삭제합니다.
1. 키 자격 증명 모음, 감사 스토리지 컨테이너, 이벤트 허브, Azure AD 인스턴스 및 기타 종속 리소스를 제거하여 요금 청구를 중지합니다.

## <a name="move-a-managed-instance"></a>관리되는 인스턴스 이동

### <a name="verify-prerequisites"></a>필수 구성 요소 확인

1. 각 원본 관리되는 인스턴스의 경우, 대상 지역에 동일한 크기의 SQL Managed Instance 대상 인스턴스를 만듭니다.  
1. 관리되는 인스턴스의 네트워크를 구성합니다. 자세한 내용은 [네트워크 구성](../managed-instance/how-to-content-reference-guide.md#network-configuration)을 참조하세요.
1. 올바른 로그인으로 대상 마스터 데이터베이스를 구성합니다. 구독 관리자 또는 SQL Managed Instance 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다.
1. 데이터베이스가 투명한 데이터 암호화로 암호화되고 Azure Key Vault에서 사용자 고유의 암호화 키를 사용하는 경우, 원본 및 대상 지역 모두에 동일한 암호화 키가 있는 Azure Key Vault가 있는지 확인합니다. 자세한 내용은 [Azure Key Vault의 고객 관리형 키를 사용한 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.
1. 관리되는 인스턴스에 감사를 사용하는 경우 다음을 확인합니다.
    - 기존 로그가 포함된 스토리지 컨테이너 또는 이벤트 허브가 대상 지역으로 이동됩니다.
    - 감사는 대상 인스턴스에서 구성됩니다. 자세한 내용은 [SQL Managed Instance를 사용하여 감사](../managed-instance/auditing-configure.md)를 참조하세요.
1. 인스턴스에 LTR(장기 보존 정책)이 있는 경우 기존 LTR 백업이 현재 인스턴스와 연결된 상태로 유지됩니다. 대상 인스턴스가 다르기 때문에 인스턴스가 삭제되더라도 원본 인스턴스를 사용하여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다.

  > [!NOTE]
  > 이는 소버린 클라우드와 공용 지역 간에 이동하는 데 충분하지 않습니다. 이러한 마이그레이션을 수행하려면 현재 지원되지 않는 대상 인스턴스로 LTR 백업을 이동해야 합니다.

### <a name="prepare-resources"></a>리소스 준비

각 원본 관리되는 인스턴스와 SQL Managed Instance의 해당 대상 인스턴스 간에 장애 조치(failover) 그룹을 만듭니다.

각 인스턴스에 있는 모든 데이터베이스의 복제가 자동으로 시작됩니다. 자세한 내용은 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 참조하세요.

### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

정기적으로 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup)을 호출하여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. `Get-AzSqlDatabaseFailoverGroup`의 출력 개체에는 **ReplicationState** 에 대한 속성이 포함되어 있습니다.

- **ReplicationState = 2**(CATCH_UP)는 데이터베이스가 동기화되었으며 안전하게 장애 조치할 수 있음을 나타냅니다.
- **ReplicationState = 0**(SEEDING)은 데이터베이스가 아직 시드되지 않아 장애 조치(failover) 시도가 실패했음을 나타냅니다.

### <a name="test-synchronization"></a>동기화 테스트

**ReplicationState** 가 `2`인 경우, 보조 엔드포인트 `<fog-name>.secondary.database.windows.net`을 사용하여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결하고 데이터베이스에 대한 쿼리를 수행하여 연결, 적절한 보안 구성 및 데이터 복제를 보장합니다.

### <a name="initiate-the-move"></a>이동 시작

1. 보조 엔드포인트 `<fog-name>.secondary.database.windows.net`을 사용하여 대상 관리되는 인스턴스에 연결합니다.
1. [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)을 사용하여 보조 관리되는 인스턴스를 전체 동기화를 사용하는 기본으로 전환합니다. 이 작업은 성공적으로 수행되거나 롤백됩니다.
1. DNS CNAME 진입점이 대상 지역 IP 주소를 가리키는지 확인하기 위해 `nslook up <fog-name>.secondary.database.windows.net`을 사용하여 명령이 성공적으로 완료되었는지 확인합니다. 스위치 명령이 실패하면 CNAME은 업데이트되지 않습니다.

### <a name="remove-the-source-managed-instances"></a>원본 관리되는 인스턴스 제거

이동이 완료되면 불필요한 요금을 방지하기 위해 원본 지역의 리소스를 제거합니다.

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)을 사용하여 장애 조치(failover) 그룹을 삭제합니다. 장애 조치(failover) 그룹 구성을 삭제하고 두 인스턴스 간의 지역 복제 링크를 종료합니다.
1. [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)를 사용하여 원본 관리되는 인스턴스를 삭제합니다.
1. 리소스 그룹에서 가상 클러스터, 가상 네트워크 및 보안 그룹과 같은 추가 리소스를 제거합니다.

## <a name="next-steps"></a>다음 단계

마이그레이션한 후에 데이터베이스를 [관리](manage-data-after-migrating-to-database.md)합니다.