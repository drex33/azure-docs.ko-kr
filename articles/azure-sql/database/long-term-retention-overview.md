---
title: 장기 백업 보존
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance에서 장기 보존 정책을 통해 최대 10년 동안 전체 데이터베이스 백업을 저장하도록 지원하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 07/13/2021
ms.openlocfilehash: 703a6daa7edc5b8a8ef8cf7963b0ee720041ab4c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710485"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>장기 보존 - Azure SQL Database 및 Azure SQL Managed Instance

Azure SQL Database 및 Azure SQL Managed Instance의 [자동 백업](automated-backups-overview.md)에서 제공하는 데이터베이스 백업을 7~35일 넘게 보존하도록 요구하는 규정, 규정 준수 또는 기타 비즈니스 목적을 가진 애플리케이션이 많습니다. LTR(장기 보존) 기능을 사용하면 지정된 SQL Database 및 SQL Managed Instance 전체 백업을 Azure Blob Storage에 최대 10년 동안 [구성된 중복성](automated-backups-overview.md#backup-storage-redundancy)으로 저장할 수 있습니다. 그런 다음 LTR 백업을 새 데이터베이스로 복원할 수 있습니다.

장기 보존은 Azure SQL Database에 대해 사용하도록 설정할 수 있으며, Azure SQL Managed Instance에 대한 퍼블릭 미리 보기에서 사용할 수 있습니다. 이 문서에서는 장기 보존에 대한 개념적 개요를 제공합니다. 장기 보존을 구성하려면 [Azure SQL Database LTR 구성](long-term-backup-retention-configure.md) 및 [Azure SQL Managed Instance LTR 구성](../managed-instance/long-term-backup-retention-configure.md)을 참조하세요. 

> [!NOTE]
> SQL 에이전트 작업을 사용하여 35일이 초과된 LTR의 대안으로 [복사 전용 데이터베이스 백업](/sql/relational-databases/backup-restore/copy-only-backups-sql-server)을 예약할 수 있습니다.

> [!IMPORTANT]
> Managed Instance의 장기 보존은 현재 Azure 공용 지역에서만 퍼블릭 미리 보기로 제공됩니다. 


## <a name="how-long-term-retention-works"></a>장기 보존 작동 방식
     
LTR(장기 백업 보존)은 [자동 생성](automated-backups-overview.md)되는 전체 데이터베이스 백업을 활용하여 PITR(특정 시점 복원)을 지원합니다. LTR 정책이 구성된 경우 이러한 백업은 장기 스토리지를 위해 다른 blob에 복사됩니다. 복사본은 데이터베이스 워크로드 성능에 영향을 주지 않는 백그라운드 작업입니다. SQL Database의 각 데이터베이스에 대한 LTR 정책은 LTR 백업 생성 빈도를 지정할 수도 있습니다.

LTR을 사용하도록 설정하기 위해 네 개의 매개 변수(주간 백업 보존(W), 월간 백업 보존(M), 연간 백업 보존(Y), 연간 주(WeekOfYear)) 조합을 사용하여 정책을 정의할 수 있습니다. W를 지정하는 경우 매주 하나의 백업이 장기 스토리지에 복사됩니다. M을 지정하는 경우 각 월의 첫 번째 백업이 장기 스토리지에 복사됩니다. Y를 지정하는 경우 WeekOfYear로 지정된 주 동안 하나의 백업이 장기 스토리지에 복사됩니다. 지정된 WeekOfYear가 정책이 구성된 지난 해인 경우 첫 번째 LTR 백업은 다음 해에 만들어집니다. 각 백업은 LTR 백업을 만들 때 구성된 정책 매개 변수에 따라 장기 스토리지에 보관됩니다.

> [!NOTE]
> LTR 정책에 대한 변경 내용은 이후 백업에만 적용됩니다. 예를 들어, 주간 백업 보존(W), 월간 백업 보존(M) 또는 연간 백업 보존(Y)이 수정되면 새 보존 설정이 새 백업에만 적용됩니다. 기존 백업 보존은 수정되지 않습니다. 보존 기간이 만료되기 전에 이전 LTR 백업을 삭제하려는 경우 [백업을 수동으로 삭제](./long-term-backup-retention-configure.md#delete-ltr-backups)해야 합니다.
> 

LTR 정책의 예제:

-  W=0, M=0, Y=5, WeekOfYear=3

   각 연도의 세 번째 전체 백업은 5년 동안 유지됩니다.
   
- W=0, M=3, Y=0

   매월 첫 번째 전체 백업이 3개월 동안 유지됩니다.

- W=12, M=0, Y=0

   각각의 매주 전체 백업이 12주 동안 유지됩니다.

- W = 6, M = 12, Y = 10, WeekOfYear = 20

   각각의 매주 전체 백업이 6주 동안 유지됩니다. 매월 첫 번째 전체 백업을 제외하고 12개월 동안 유지됩니다. 20 주에 수행 된 전체 백업을 제외 하 고 10 년 동안 보존 됩니다. 

다음 표는 다음 정책에 대한 장기 백업의 주기 및 만료를 나타냅니다.

W = 12 주 (84 일), M = 12 개월 (365 일), Y = 10 년 (3650 일), WeekOfYear = 20 (주 5 월 13 일)

   ![ltr 예제](./media/long-term-retention-overview/ltr-example.png)


위의 정책을 수정하고 W=0(주별 백업 없음)으로 설정하는 경우 백업 복사본의 주기는 강조 표시된 날짜까지 위의 표에 나와 있는 것처럼 변경됩니다. 이러한 백업을 유지하는 데 필요한 스토리지 용량은 적절하게 줄어듭니다. 

> [!IMPORTANT]
> 개별 LTR 백업의 타이밍은 Azure에서 제어합니다. LTR 백업을 수동으로 만들거나 백업 생성 타이밍을 제어할 수 없습니다. LTR 정책을 구성한 후에는 첫 번째 LTR 백업이 사용 가능한 백업 목록에 표시될 때까지 최대 7일이 걸릴 수 있습니다.  


## <a name="geo-replication-and-long-term-backup-retention"></a>지역 복제 및 장기 백업 보존

활성 지역 복제 또는 장애 조치(failover) 그룹을 비즈니스 연속성 솔루션으로 사용하는 경우, 최종 장애 조치를 준비하고 보조 데이터베이스 또는 인스턴스에 동일한 LTR 정책을 구성해야 합니다. 백업이 보조 데이터베이스에서 생성되지 않으므로 LTR 스토리지 비용이 증가하지 않습니다. 보조 데이터베이스가 주 데이터베이스로 전환될 때만 백업이 만들어집니다. 이를 통해 장애 조치가 트리거되고 주 데이터베이스가 보조 지역으로 이동될 때 LTR 백업이 중단 없이 생성되도록 합니다. 

> [!NOTE]
> 원본 주 데이터베이스는 장애 조치를 유발하는 중단에서 복구되면 새 보조 데이터베이스가 됩니다. 따라서 백업 생성이 다시 시작되지 않고 기존 LTR 정책은 다시 주 데이터베이스가 될 때까지 적용되지 않습니다. 


## <a name="configure-long-term-backup-retention"></a>장기 백업 보존 구성

Azure SQL Database 및 Azure SQL Managed Instance에 대해 Azure Portal과 PowerShell을 사용하여 장기 백업 보존을 구성할 수 있습니다. LTR 스토리지에서 데이터베이스를 복원하기 위해 해당 타임스탬프에 따라 특정 백업을 선택할 수 있습니다. 데이터베이스는 원본 데이터베이스와 동일한 구독 아래의 기존 서버 또는 관리형 인스턴스로 복원될 수 있습니다.

Azure Portal 또는 PowerShell을 사용하여 장기 보존을 구성하거나 SQL Database용 백업에서 데이터베이스를 복원하는 방법을 알아보려면 [Azure SQL Database 장기 백업 보존 관리](long-term-backup-retention-configure.md)를 참조하세요.

Azure Portal 또는 PowerShell을 사용하여 장기 보존을 구성하거나 SQL Managed Instance용 백업에서 데이터베이스를 복원하는 방법을 알아보려면 [Azure SQL Managed Instance 장기 백업 보존 관리](../managed-instance/long-term-backup-retention-configure.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략에서 필수입니다. 

- 다른 SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)를 참조하세요.
- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](../database/automated-backups-overview.md) 참조
