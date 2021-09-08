---
title: 트랜잭션 복제
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Database Managed Instance(미리 보기)에서 SQL Server 트랜잭션 복제를 사용하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: mathoma
ms.date: 05/10/2020
ms.openlocfilehash: 7ba5da1b452f411966e40d4ff991bbda9fdb88f7
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867420"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Azure SQL Managed Instance(미리 보기)를 사용하여 트랜잭션 복제
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

트랜잭션 복제는 Azure SQL Managed Instance 또는 SQL Server 인스턴스의 테이블에서 원격 데이터베이스에 있는 테이블로 데이터를 복제할 수 있도록 하는 Azure SQL Managed Instance 및 SQL Server의 기능입니다. 이 기능을 사용하면 서로 다른 데이터베이스의 여러 테이블을 동기화할 수 있습니다. 

트랜잭션 복제는 현재 SQL Managed Instance에 대한 공개 미리 보기로 제공됩니다. 

## <a name="overview"></a>개요

트랜잭션 복제를 사용하여 Azure SQL Managed Instance에서 만든 변경 내용을 다음으로 푸시할 수 있습니다.

- SQL Server 데이터베이스 - 온-프레미스 또는 Azure VM에서
- Azure SQL Database의 데이터베이스
- Azure SQL Managed Instance의 인스턴스 데이터베이스

  > [!NOTE]
  > Azure SQL Managed Instance의 모든 기능을 사용하려면 최신 버전의 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) 및 [SSDT(SQL Server Data Tools)](/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용해야 합니다.

### <a name="components"></a>구성 요소

트랜잭션 복제의 주요 구성 요소는 다음 그림과 같이 **게시자**, **배포자** 및 **구독자** 입니다.  

![SQL Database를 사용한 복제](./media/replication-transactional-overview/replication-to-sql-database.png)

| 역할 | Azure SQL Database | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| **게시자** | 예 | 예 |
| **배포자** | 예 | 예|
| **끌어오기 구독자** | 예 | 예|
| **밀어넣기 구독자**| 예 | 예|
| &nbsp; | &nbsp; | &nbsp; |

**게시자** 는 배포자에게 업데이트를 전송하여 일부 테이블(문서)에 대해 변경된 내용을 게시합니다. 게시자는 Azure SQL Managed Instance 또는 SQL Server 인스턴스일 수 있습니다.

**배포자** 는 게시자에서 문서의 변경 내용을 수집하고 구독자에게 배포합니다. 배포자는 Azure SQL Managed Instance 또는 SQL Server 인스턴스(게시자 버전보다 같거나 높은 모든 버전)일 수 있습니다.

**구독자** 는 게시자에 대한 변경 내용을 받습니다. SQL Server 인스턴스와 Azure SQL Managed Instance 모두 밀어넣기 및 끌어오기 구독자일 수 있지만 배포자가 Azure SQL Managed Instance이며 구독자가 아닌 경우에는 끌어오기 구독이 지원되지 않습니다. Azure SQL Database의 데이터베이스는 밀어넣기 구독자만 될 수 있습니다.

Azure SQL Managed Instance는 다음 버전의 SQL Server에서 구독자가 되는 것을 지원할 수 있습니다.

- SQL Server 2016 이상
- SQL Server 2014 [RTM CU10(12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 또는 [SP1 CU3(12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8(11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 또는 [SP3(11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996) 또는 [SP4(11.0.7001.0)](https://www.microsoft.com/download/details.aspx?id=56040)

   > [!NOTE]
   >
   > - Azure에서 게시 개체를 지원하지 않는 다른 버전의 SQL Server에서는 [데이터 다시 게시](/sql/relational-databases/replication/republish-data) 방법을 사용하여 데이터를 최신 버전의 SQL Server로 이동할 수 있습니다.
   > - 이전 버전을 사용하여 복제를 구성하는 시도는 오류 번호 MSSQL_REPL20084(프로세스가 구독자에 연결할 수 없습니다.) 및 MSSQ_REPL40532(로그인에서 요청한 서버 \<name>을 열 수 없습니다. 로그인이 실패했습니다.)가 발생할 수 있습니다.

### <a name="types-of-replication"></a>복제의 유형

다음과 같은 여러 [복제 유형](/sql/relational-databases/replication/types-of-replication)이 있습니다.

| 복제 | Azure SQL Database | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| [**표준 트랜잭션**](/sql/relational-databases/replication/transactional/transactional-replication) | 예(구독자로) | 예 |
| [**스냅샷**](/sql/relational-databases/replication/snapshot-replication) | 예(구독자로) | 예|
| [**병합 복제**](/sql/relational-databases/replication/merge/merge-replication) | 예 | 예|
| [**피어 투 피어**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 예 | 예|
| [**양방향**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 예 | 예|
| [**업데이트할 수 있는 구독**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 예 | 예|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>지원 가능성 매트릭스

  Azure SQL Managed Instance에 대한 트랜잭션 복제 지원 가능성 매트릭스는 SQL Server 대한 것과 동일합니다.
  
| **게시자**   | **배포자** | **구독자** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>사용 시기

트랜잭션 복제는 다음과 같은 시나리오에서 유용합니다.

- 데이터베이스의 하나 이상의 테이블에서 변경한 내용을 게시하고 해당 변경 내용에 대해 구독한 SQL Server 인스턴스 또는 Azure SQL Database에 있는 하나 이상의 데이터베이스에 배포합니다.
- 여러 개의 분산된 데이터베이스를 동기화된 상태로 유지합니다.
- 변경 내용을 지속적으로 게시하여 SQL Server 인스턴스 또는 Azure SQL Managed Instance의 데이터베이스를 다른 데이터베이스로 마이그레이션합니다.

### <a name="compare-data-sync-with-transactional-replication"></a>트랜잭션 복제와 데이터 동기화 비교

| 범주 | 데이터 동기화 | 트랜잭션 복제 |
|---|---|---|
| 장점 | - 활성-활성 지원<br/>- 온-프레미스 및 Azure SQL Database 간 양방향 | - 낮은 대기 시간<br/>- 트랜잭션 일관성<br/>- 마이그레이션 후 기존 토폴로지 다시 사용 |
| 단점 | - 트랜잭션 일관성 부족<br/>- 성능에 더 많은 영향을 미침 | - Azure SQL Database에서 게시할 수 없음 <br/>- 높은 유지 관리 비용 |

## <a name="common-configurations"></a>일반 구성

일반적으로 게시자와 배포자는 모두 클라우드 또는 온-프레미스에 있어야 합니다. 현재 지원되는 구성은 다음과 같습니다.

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>SQL Managed Instance에서 로컬 배포자로 게시자

![게시자 및 배포자로서의 단일 인스턴스](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

게시자 및 배포자는 단일 SQL Managed Instance 내에서 구성되고, 다른 SQL Managed Instance, SQL Database 또는 SQL Server 인스턴스로 변경 내용을 배포하고 있습니다.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>SQL Managed Instance에서 원격 배포자로 게시자

이 구성에서는 하나의 관리되는 인스턴스가 또 다른 SQL Managed Instance에 배치된 배포자에게 변경 내용을 게시합니다. 이 배포자는 여러 원본 SQL Managed Instance를 제공하고 Azure SQL Database, Azure SQL Managed Instance 또는 SQL Server에 있는 하나 또는 여러 대상으로 변경 내용을 배포할 수 있습니다.

![게시자 및 배포자에 대한 별도 인스턴스](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

게시자 및 배포자는 두 개의 관리되는 인스턴스에서 구성됩니다. 이 구성을 사용하는 몇 가지 제약 조건이 있습니다.

- 두 관리되는 인스턴스가 동일한 vNet에 있습니다.
- 두 관리되는 인스턴스는 동일한 위치에 있습니다.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>원격 구독자로 온-프레미스 게시자/배포자

![구독자로 Azure SQL Database](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

이 구성에서 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스는 구독자입니다. 이 구성은 온-프레미스에서 Azure로의 마이그레이션을 지원합니다. 구독자가 Azure SQL Database의 데이터베이스인 경우 밀어넣기 모드에 있어야 합니다.  

## <a name="requirements"></a>요구 사항

- 복제 참가자 간 연결에 SQL 인증을 사용합니다.
- 복제에 사용되는 작업 디렉터리에 대해 Azure Storage 계정 공유를 사용합니다.
- Azure 파일 공유에 액세스하려면 서브넷 보안 규칙에서 TCP 아웃바운드 포트 445를 엽니다.
- SQL Managed Instance가 게시자/배포자이고 구독자가 아닌 경우 TCP 아웃바운드 포트 1433을 엽니다. `virtualnetwork`에서 `internet`으로 포트 1433 **대상 서비스 태그** 의 `allow_linkedserver_outbound`에 대한 SQL Managed Instance NSG 아웃바운드 보안 규칙을 변경해야 할 수도 있습니다.
- 클라우드 또는 온-프레미스 둘 다에 게시자와 배포자를 둘 다 저장합니다.
- 가상 네트워크가 서로 다른 경우 복제 참가자의 가상 네트워크 간에 VPN 피어링을 구성합니다.

> [!NOTE]
> 배포자가 Azure SQL Managed Instance 데이터베이스이고 구독자가 온-프레미스인 경우 아웃바운드 NSG(네트워크 보안 그룹) 포트 445가 차단되면 Azure Storage 파일에 연결할 때 오류 53이 발생할 수 있습니다. 이 문제를 해결하려면 [vNet NSG를 업데이트](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md)합니다.

## <a name="with-failover-groups"></a>장애 조치(failover) 그룹 사용

**게시자** 또는 **배포자** SQL Managed Instance가 [장애 조치(failover) 그룹](../database/auto-failover-group-overview.md)에 있는 경우, 장애 조치가 발생한 후 SQL Managed Instance 관리자가 모든 게시를 이전 주 데이터베이스에서 정리하고 새로운 주 데이터베이스에서 다시 구성해야 합니다. 이 시나리오에는 다음과 같은 작업이 필요합니다.

1. 데이터베이스에서 실행 중인 모든 복제 작업을 중지합니다(있는 경우).
1. 게시자 데이터베이스에서 다음 스크립트를 실행하여 게시자에서 구독 메타데이터를 삭제합니다.

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. 구독자에서 구독 메타데이터를 삭제합니다. 구독자 SQL Managed Instance의 구독 데이터베이스에서 다음 스크립트를 실행합니다.

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. 게시된 데이터베이스에서 다음 스크립트를 실행하여 게시자에서 모든 복제 개체를 강제로 삭제합니다.

   ```sql
   EXEC sp_removedbreplication
   ```

1. 원래 주 SQL Managed Instance에서 이전 배포자를 강제로 삭제합니다(배포자를 포함하는 데 사용된 이전 주로 장애 복구하는 경우). 이전 배포자 SQL Managed Instance의 master 데이터베이스에서 다음 스크립트를 실행합니다.

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

**구독자** SQL Managed Instance가 장애 조치 그룹에 있는 경우, 구독자 관리형 인스턴스의 장애 조치 그룹 수신기 엔드포인트에 연결하도록 게시를 구성해야 합니다. 장애 조치(failover) 시 관리되는 인스턴스 관리자의 후속 작업은 발생한 장애 조치(failover) 유형에 따라 달라집니다.

- 데이터 손실이 없는 장애 조치(failover)의 경우 장애 조치(failover) 후에도 복제가 계속 작동합니다.
- 데이터 손실이 있는 장애 조치(failover)의 경우 복제도 작동합니다. 손실된 변경 내용을 다시 복제합니다.
- 데이터 손실이 있는 장애 조치(failover)의 경우 데이터 손실이 배포 데이터베이스 보존 기간을 벗어나는 경우 SQL Managed Instance 관리자가 구독 데이터베이스를 다시 시작해야 합니다.

## <a name="next-steps"></a>다음 단계

트랜잭션 복제를 구성하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.

- [SQL Managed Instance 게시자와 구독자 간의 복제 구성](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [SQL Managed Instance 구독자, SQL Managed Instance 배포자 및 SQL Server 구독자 간 복제 구성](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [게시 만들기](/sql/relational-databases/replication/publish/create-a-publication)
- 구독자로 서버 이름(예: `N'azuresqldbdns.database.windows.net`) 및 대상 데이터베이스로 Azure SQL Database 이름의 데이터베이스(예: **Adventureworks**)를 사용하여 [밀어넣기 구독을 만듭니다](/sql/relational-databases/replication/create-a-push-subscription). )

## <a name="see-also"></a>참고 항목  

- [SQL Managed Instance 및 장애 조치(failover) 그룹을 사용하여 복제](transact-sql-tsql-differences-sql-server.md#replication)
- [SQL Database로 복제](../database/replication-to-sql-database.md)
- [관리되는 인스턴스로 복제](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [게시 만들기](/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](/sql/relational-databases/replication/types-of-replication)
- [모니터링(복제)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](/sql/relational-databases/replication/initialize-a-subscription)
