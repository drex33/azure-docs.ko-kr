---
title: '자습서: DMS를 사용하여 MySQL에서 Azure Database for MySQL로 오프라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 MySQL 온-프레미스에서 Azure Database for MySQL로 오프라인 마이그레이션하는 방법을 알아봅니다.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: cafe928ad8baed2a597bfef9bbedca8ca72e1d76
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122202004"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>자습서: DMS를 사용하여 MySQL에서 Azure Database for MySQL로 오프라인 마이그레이션

Azure Database Migration Service의 고속 데이터 마이그레이션 기능을 통해 온-프레미스 MySQL 인스턴스에서 [Azure Database for MySQL](../mysql/index.yml)로 한 번의 전체 데이터베이스 마이그레이션을 수행할 수 있습니다. 이 자습서에서는 Azure Database Migration Service에서 오프라인 마이그레이션 작업을 수행하여 샘플 데이터베이스를 MySQL 5.7의 온-프레미스 인스턴스에서 Azure Database for MySQL(v5.7)로 마이그레이션합니다. 이 문서에서는 원본이 MySQL 데이터베이스 인스턴스이고 대상이 Azure Database for MySQL인 것으로 가정하지만 원본 서버 이름과 자격 증명을 변경하기만 하면 같은 방법으로 서로 다른 Azure Database for MySQL 간에 마이그레이션할 수 있습니다. 또한 낮은 버전의 MySQL 서버(v5.6 이상)에서 더 높은 버전으로의 마이그레이션도 지원됩니다.

> [!IMPORTANT]
> 온라인 마이그레이션의 경우에는 [데이터 입력 복제](../mysql/concepts-data-in-replication.md) 기능이 있는 [MyDumper/MyLoader](https://centminmod.com/mydumper.html)와 같은 오픈 소스 도구를 사용합니다. 

> [!NOTE]
> 이 마이그레이션 환경의 PowerShell 기반 스크립트 가능한 버전은 [Azure Database for MySQL로의 스크립트 가능한 오프라인 마이그레이션](./migrate-mysql-to-azure-mysql-powershell.md)을 참조하세요.

> [!NOTE]
> MySQL용 Amazon RDS(Relational Database Service) 및 Amazon Aurora(MySQL 기반)도 마이그레이션 원본으로 지원됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * mysqldump 유틸리티를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free).
* 5\.7 버전의 온-프레미스 MySQL 데이터베이스가 있어야 합니다. 없으면 [MySQL 커뮤니티 버전](https://dev.mysql.com/downloads/mysql/) 5.7을 다운로드하여 설치합니다.
* MySQL 오프라인 마이그레이션은 프리미엄 DMS SKU에서만 지원됩니다.
* [Azure Database for MySQL에 인스턴스를 만듭니다](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Workbench 애플리케이션을 사용하여 데이터베이스를 연결하고 만드는 방법에 대한 자세한 내용은 [MySQL Workbench를 사용하여 데이터 연결 및 쿼리](../mysql/connect-workbench.md) 문서를 참조하세요. Azure Database for MySQL 버전은 온-프레미스 MySQL 버전과 같거나 그 이상이어야 합니다. 예를 들어 MySQL 5.7은 Azure Database for MySQL 5.7로 마이그레이션하거나 8로 업그레이드할 수 있습니다. 
* Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 Microsoft Azure Virtual Network를 만듭니다. 그러면 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 대한 사이트 간 연결이 제공됩니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](../virtual-network/index.yml)를 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.

    > [!NOTE]
    > 가상 networkNet 설정 중에 Microsoft에 대한 네트워크 피어링에서 ExpressRoute를 사용하는 경우 다음 서비스 [엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 서비스가 프로비저닝되는 서브넷에 추가합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크 Network Security Group 규칙이 ServiceBus, Storage 및 AzureMonitor용 ServiceTag의 아웃바운드 포트 443을 차단하지 않는지 확인합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
* Azure Database Migration Service가 원본 MySQL Server에 액세스할 수 있게 Virtual Network의 연결을 허용하도록 Windows 방화벽을 엽니다(기본적으로 TCP 포트 3306).
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우에는 Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 Virtual Network의 연결을 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service의 Virtual Network가 대상 데이터베이스에 액세스할 수 있게 허용하도록 대상 Azure Database for MySQL에 대한 서버 수준 [방화벽 규칙](../azure-sql/database/firewall-configure.md)을 만들거나 [VNET 서비스 엔드포인트를 구성](../mysql/howto-manage-vnet-using-portal.md)합니다.
* 원본 MySQL은 지원되는 MySQL 커뮤니티 버전에 있어야 합니다. MySQL 유틸리티 또는 MySQL Workbench에서 MySQL 인스턴스의 버전을 확인하려면 다음 명령을 실행합니다.

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL은 InnoDB 테이블만 지원합니다. MyISAM 테이블을 InnoDB로 변환하려면 [MyISAM에서 InnoDB로 테이블 변환](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 문서를 참조하세요.
* 사용자에게 원본 데이터베이스에서 데이터를 읽을 수 있는 권한이 있어야 합니다.

## <a name="sizing-the-target-azure-database-for-mysql-instance"></a>대상 Azure Database for MySQL 인스턴스 크기 조정

Azure Database Migration Service를 사용하여 더 빠르게 데이터를 로드할 수 있도록 대상 Azure Database for MySQL 서버를 준비하려면 다음 서버 매개 변수 및 구성을 변경하는 것이 좋습니다. 

* max_allowed_packet – 대량 행으로 인한 연결 문제를 방지하려면 1073741824(1GB)로 설정합니다. 
* slow_query_log – 저속 쿼리 로그를 해제하려면 OFF로 설정합니다. 이렇게 하면 데이터 로드 중 느린 쿼리 로깅으로 인한 오버헤드가 제거됩니다.
* query_store_capture_mode – 쿼리 저장소를 해제하려면 NONE으로 설정합니다. 이렇게 하면 쿼리 저장소의 샘플링 작업으로 인한 오버헤드가 제거됩니다.
* innodb_buffer_pool_size – innodb_buffer_pool_size는 Azure Database for MySQL 서버를 위한 컴퓨팅을 스케일 업해야만 늘릴 수 있습니다. 마이그레이션 중 포털의 가격 책정 계층에서 서버를 64개의 vCore 범용 SKU로 스케일 업하여 innodb_buffer_pool_size를 늘립니다. 
* innodb_io_capacity & innodb_io_capacity_max - Azure Portal의 서버 매개 변수에서 9000으로 변경하여 마이그레이션 속도를 최적화하기 위해 IO 사용률을 향상시킵니다.
* innodb_write_io_threads & innodb_write_io_threads - 마이그레이션의 속도를 개선하기 위해 Azure Portal의 서버 매개 변수에서 4로 변경합니다.
* 스토리지 계층 스케일 업 – 스토리지 계층이 증가함에 따라 Azure Database for MySQL 서버의 IOP가 점진적으로 증가합니다. 
    * 단일 서버 배포 옵션에서 더 빠르게 로드하려면 스토리지 계층을 늘려 프로비저닝된 IOP를 늘리는 것이 좋습니다. 
    * 유연한 서버 배포 옵션에서는 스토리지 크기에 관계없이 IOPS를 스케일링(증가 또는 감소)하는 것이 좋습니다. 
    * 스토리지 크기는 스케일 업만 가능하며 스케일 다운할 수 없습니다.

마이그레이션이 완료되면 서버 매개 변수 및 구성을 워크로드에 필요한 값으로 다시 되돌릴 수 있습니다. 


## <a name="migrate-database-schema"></a>데이터베이스 스키마 마이그레이션

테이블 스키마, 인덱스 및 저장 프로시저와 같은 모든 데이터베이스 개체를 전송하려면 원본 데이터베이스에서 스키마를 추출하여 대상 데이터베이스에 적용해야 합니다. 스키마를 추출하려면 `--no-data` 매개 변수가 있는 mysqldump를 사용할 수 있습니다. 이를 위해서는 원본 MySQL 데이터베이스와 대상 Azure Database for MySQL 모두에 연결할 수 있는 컴퓨터가 필요합니다.

mysqldump를 사용하여 스키마를 내보내려면 다음 명령을 실행합니다.

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

예를 들면 다음과 같습니다.

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

스키마를 대상 Azure Database for MySQL로 가져오려면 다음 명령을 실행합니다.

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

예를 들면 다음과 같습니다.

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

스키마에 외래 키 또는 트리거가 있으면 마이그레이션 작업에서 마이그레이션 중 병렬 데이터 로드를 처리합니다. 스키마 마이그레이션 중에는 외래 키 또는 트리거를 삭제할 필요가 없습니다.

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-a-database-migration-service-instance"></a>Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기** 를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service** 를 선택합니다.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기** 를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. 가격 책정 계층을 선택하고 네트워킹 화면으로 이동합니다. 오프라인 마이그레이션 기능은 프리미엄 가격 책정 계층에서만 사용할 수 있습니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 기본 설정 구성](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. 목록에서 기존 가상 네트워크를 선택하거나 만들려는 새 가상 네트워크의 이름을 입력합니다. 검토 후 만들기 화면으로 이동합니다. 필요에 따라 태그 화면에서 서비스에 태그를 추가할 수 있습니다.

    가상 네트워크는 원본 SQL Server 및 대상 Azure SQL Database 인스턴스에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공합니다.

    ![Azure Database Migration Service 네트워크 설정 구성](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md) 문서를 참조하세요.

6. 구성을 살펴본 후 **만들기** 를 선택하여 서비스를 만듭니다.
    
    ![Azure Database Migration Service 만들기](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.  

1. Azure Portal에서 **모든 서비스** 를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services** 를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. 검색 결과에서 마이그레이션 서비스 인스턴스를 선택하고 + **새 마이그레이션 프로젝트** 를 선택합니다.
    
    ![새 마이그레이션 프로젝트 만들기](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 유형** 선택 상자에서 **MySQL** 을 선택하고, **대상 서버 유형** 선택 상자에서 **Azure Database For MySQL** 을 선택하고, **마이그레이션 작업 유형** 선택 상자에서 **데이터 마이그레이션** 을 선택합니다. **활동 만들기 및 실행** 을 선택합니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기** 를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

## <a name="configure-migration-project"></a>마이그레이션 프로젝트 구성

1. **원본 선택** 화면에서 원본 MySQL 인스턴스에 대한 연결 세부 정보를 지정하고 **다음: 대상 선택>>** 을 선택합니다.

    ![원본 세부 정보 추가 화면](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. **대상 선택** 화면에서 대상 Azure Database for MySQL 인스턴스에 대한 연결 세부 정보를 지정하고 **다음: 데이터베이스 선택>>** 을 선택합니다.

    ![대상 세부 정보 화면](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. **데이터베이스 선택** 화면에서 마이그레이션을 위한 원본 및 대상 데이터베이스를 매핑하고 **다음: 마이그레이션 설정 구성>>** 을 선택합니다. **원본 서버를 읽기 전용으로 만들기** 옵션을 선택하면 원본을 읽기 전용으로 만들 수 있지만 이는 서버 수준 설정이므로 주의해야 합니다. 이 옵션을 선택하는 경우 선택한 데이터베이스뿐만 아니라 전체 서버가 읽기 전용으로 설정됩니다.
    
    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.
    ![데이터베이스 세부 정보 선택 화면](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > 이 단계에서 여러 데이터베이스를 선택할 수 있지만 각 데이터베이스에서 계산을 공유하기 때문에 이러한 방식으로 DB를 마이그레이션할 수 있는 수와 속도에 제한이 있습니다. 각 마이그레이션 작업은 프리미엄 SKU의 기본 구성을 사용하여 두 테이블의 마이그레이션을 동시에 시도합니다. 이러한 테이블은 선택한 데이터베이스 중 하나에 있을 수 있습니다. 이 때 속도가 충분하지 않다면 데이터베이스 마이그레이션 작업을 여러 마이그레이션 작업으로 분할한 후 여러 서비스로 확장할 수 있습니다. 또한 지역별 구독당 10개의 Azure Database Migration Service 인스턴스로 제한됩니다.
    > 마이그레이션 처리량 및 병렬 처리에 대한 보다 세부적인 제어가 필요하면 [PowerShell: DMS를 사용하여 MySQL 데이터베이스에서 Azure Database for MySQL로 오프라인 마이그레이션 실행](./migrate-mysql-to-azure-mysql-powershell.md) 문서를 참조하세요.

4. **마이그레이션 설정 구성** 화면에서 마이그레이션에 포함할 테이블을 선택하고 **다음: 요약>>** 을 선택합니다. 데이터가 있는 대상 테이블은 기본적으로 선택되지 않지만 명시적으로 선택할 수 있으며 마이그레이션을 시작하기 전에 잘립니다.

    ![테이블 선택 화면](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. **요약** 화면의 **작업 이름** 텍스트 상자에 마이그레이션 작업의 이름을 지정한 다음, 요약 내용을 살펴보고 원본 및 대상 세부 정보가 앞에서 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 프로젝트 요약](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. **마이그레이션 시작** 을 선택합니다. 마이그레이션 작업 창이 나타나고, 작업 **상태** 는 **초기화 중** 입니다. 테이블 마이그레이션이 시작되면 **상태** 가 **실행 중** 으로 바뀝니다.
 
     ![마이그레이션 실행](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침** 을 선택하면 디스플레이가 업데이트되고 완료된 테이블 수에 대한 진행률이 표시됩니다. 

2. 작업 화면에서 데이터베이스 이름을 클릭하면 마이그레이션되는 각 테이블의 상태를 확인할 수 있습니다. **새로 고침** 을 선택하여 디스플레이를 업데이트합니다. 

     ![마이그레이션 모니터링](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>마이그레이션 완료

1. 마이그레이션 작업 화면에서 **새로 고침** 을 선택하여 마이그레이션 **상태** 가 **완료** 로 표시될 때까지 디스플레이를 업데이트합니다.

    ![마이그레이션 완료](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>마이그레이션 후 작업

오프라인 마이그레이션에서 단독형 마이그레이션 프로세스는 애플리케이션에 따라 달라지며 이 문서의 범위를 벗어나지만 다음과 같은 마이그레이션 후 작업이 필요합니다.

1. 애플리케이션 요구 사항에 따라 로그인, 역할 및 사용 권한을 만듭니다.
2. 마이그레이션 전 단계 중에 대상 데이터베이스에서 추출된 모든 트리거를 다시 만듭니다.
3. 대상 데이터베이스에 애플리케이션의 온전성 테스트를 수행하여 마이그레이션을 인증합니다. 

## <a name="clean-up-resources"></a>리소스 정리

Database Migration Service를 계속해서 사용하지 않을 경우 다음 단계에 따라 서비스를 삭제할 수 있습니다.

1. Azure Portal에서 **모든 서비스** 를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services** 를 선택합니다.

    ![DMS의 모든 인스턴스 찾기](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. 검색 결과에서 마이그레이션 서비스 인스턴스를 선택하고 **서비스 삭제** 를 선택합니다.
    
    ![마이그레이션 서비스 삭제](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. 확인 대화 상자의 **데이터베이스 마이그레이션 서비스 이름 입력** 텍스트 상자에 서비스 이름을 입력하고 **삭제** 를 선택합니다.

    ![마이그레이션 서비스 삭제 확인](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>다음 단계

* DMS를 사용하여 마이그레이션을 수행할 때의 알려진 문제 및 제한 사항에 대한 자세한 내용은 [일반적인 문제 - Azure Database Migration Service](./known-issues-troubleshooting-dms.md) 문서를 참조하세요.
* DMS 사용 시 원본 데이터베이스 연결 문제를 해결하려면 [원본 데이터베이스 연결 문제](./known-issues-troubleshooting-dms-source-connectivity.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](./dms-overview.md) 문서를 참조하세요.
* Azure Database for MySQL에 대한 자세한 내용은 [Azure Database for MySQL이란?](../mysql/overview.md) 문서를 참조하세요.
* PowerShell을 통해 DMS를 사용하는 방법에 대한 지침은 [PowerShell: DMS를 사용하여 MySQL 데이터베이스에서 Azure Database for MySQL로 오프라인 마이그레이션 실행](./migrate-mysql-to-azure-mysql-powershell.md) 문서를 참조하세요.
