---
title: '자습서: Azure Portal을 통해 PostgreSQL을 Azure DB for PostgreSQL로 온라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Portal을 통해 Azure Database Migration Service를 사용하여 PostgreSQL 온-프레미스에서 Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 방법을 알아봅니다.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 4f73ced1dba5482c3c230c96163464c064523bd4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218244"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>자습서: Azure Portal을 통해 DMS를 사용하여 PostgreSQL을 Azure DB for PostgreSQL로 온라인 마이그레이션

Azure Database Migration Service를 사용하여 애플리케이션 가동 중지 시간을 최소화하면서 온-프레미스 PostgreSQL 인스턴스에서 [Azure Database for PostgreSQL](../postgresql/index.yml)로 데이터베이스를 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service에서 온라인 마이그레이션 작업을 사용하여 **DVD 대여** 샘플 데이터베이스를 PostgreSQL 9.6의 온-프레미스 인스턴스에서 Azure Database for PostgreSQL로 마이그레이션합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.
> [!div class="checklist"]
>
> * pg_dump 유틸리티를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.
> * 마이그레이션 중단을 수행합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 마이그레이션을 진행하는 동안 데이터 도난을 방지하기 위해 디스크를 암호화합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* [PostgreSQL 커뮤니티 버전](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 또는 10을 다운로드하여 설치합니다. 원본 PostgreSQL 서버 버전은 9.4, 9.5, 9.6, 10, 11, 12 또는 13이어야 합니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](../postgresql/concepts-supported-versions.md)을 참조하세요.

    또한 대상 Azure Database for PostgreSQL 버전이 온-프레미스 PostgreSQL 버전과 같거나 이후 버전이어야 합니다. 예를 들어 PostgreSQL 9.6은 Azure Database for PostgreSQL 9.6, 10 또는 11로 마이그레이션할 수 있지만 Azure Database for PostgreSQL 9.5로는 마이그레이션할 수 없습니다. 

* [Azure Database for PostgreSQL 서버를 만들거나](../postgresql/quickstart-create-server-database-portal.md) [Azure Database for PostgreSQL - 하이퍼스케일(Citus) 서버를 만듭니다](../postgresql/quickstart-create-hyperscale-portal.md).
* Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 Microsoft Azure Virtual Network를 만듭니다. 그러면 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 사이트 간 연결이 제공됩니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](../virtual-network/index.yml)를 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.

    > [!NOTE]
    > 가상 네트워크 설정 중에 Microsoft에 대한 네트워크 피어링에서 ExpressRoute를 사용하는 경우 서비스가 프로비저닝되는 서브넷에 다음 서비스 [엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 추가합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크에 대한 NSG(Network Security Group) 규칙이 ServiceBus, Storage 및 AzureMonitor용 ServiceTag의 아웃바운드 포트 443을 차단하지 않는지 확인합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Windows 방화벽을 열고 Azure Database Migration Service에서 기본적으로 5432 TCP 포트인 원본 PostgreSQL 서버에 액세스할 수 있도록 허용합니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service에서 대상 데이터베이스에 액세스할 수 있도록 Azure Database for PostgreSQL에 대한 서버 수준 [방화벽 규칙](../postgresql/concepts-firewall-rules.md)을 만듭니다. Azure Database Migration Service에 사용되는 가상 네트워크의 서브넷 범위를 입력합니다.
* postgresql.config 파일의 논리적 복제를 활성화하고 다음 매개 변수를 설정합니다.

  * wal_level = **logical**
  * max_replication_slots = [number of slots], **5개 슬롯** 으로 설정하는 것이 좋습니다.
  * max_wal_senders =[동시 작업 수] - max_wal_senders 매개 변수는 실행할 수 있는 동시 작업 수를 설정합니다. **10작업** 으로 설정하는 것이 좋습니다.

> [!IMPORTANT]
> 기존 데이터베이스의 모든 테이블에는 변경 내용을 대상 데이터베이스와 동기화할 수 있도록 기본 키가 필요합니다.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션

테이블 스키마, 인덱스 및 저장 프로시저와 같은 모든 데이터베이스 개체를 완료하려면 원본 데이터베이스에서 스키마를 추출하고 데이터베이스에 적용해야 합니다.

1. pg_dump -s 명령을 사용하여 데이터베이스에 대한 스키마 덤프 파일을 만듭니다.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    예를 들어 **dvdrental** 데이터베이스에 대한 스키마 덤프 파일을 만들려면 다음을 수행합니다.

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    pg_dump 유틸리티를 사용하는 방법에 대한 자세한 내용은 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 자습서의 예제를 참조하세요.

2. Azure Database for PostgreSQL인 대상 환경에서 빈 데이터베이스를 만듭니다.

    연결하고 데이터베이스를 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Azure Database for PostgreSQL 서버 만들기](../postgresql/quickstart-create-server-database-portal.md) 또는 [Azure Portal에서 Azure Database for PostgreSQL - 하이퍼스케일(Citus) 서버 만들기](../postgresql/quickstart-create-hyperscale-portal.md) 문서를 참조하세요.

    > [!NOTE]
    > Azure Database for PostgreSQL - 하이퍼스케일(Citus) 인스턴스에는 데이터베이스가 하나(**citus**)만 있습니다.

3. 스키마 덤프 파일을 복원하여 만든 대상 데이터베이스에 스키마를 가져옵니다.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    예를 들면 다음과 같습니다.

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

   > [!NOTE]
   > 마이그레이션 서비스는 안정적이고 강력한 데이터 마이그레이션을 보장하기 위해 외래 키 및 트리거의 사용/사용 안 함을 내부적으로 처리합니다. 따라서 대상 데이터베이스 스키마를 수정하는 것에 대해 걱정할 필요가 없습니다.

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-a-dms-instance"></a>DMS 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기** 를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service** 를 선택합니다.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기** 를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. **마이그레이션 서비스 만들기** 화면에서 이름, 구독, 신규 또는 기존 리소스 그룹, 서비스 위치를 지정합니다.

4. 기존 가상 네트워크를 선택하거나 새로 만듭니다.

    가상 네트워크는 원본 PostgreSQL 서버 및 대상 Azure Database for PostgreSQL 인스턴스에 대한 액세스 권한을 Azure Database Migration Service에 제공합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md) 문서를 참조하세요.

5. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. **검토 + 만들기** 를 선택하여 서비스를 만듭니다.

   서비스 만들기는 약 10~15분 내에 완료됩니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스** 를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services** 를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색한 다음, 인스턴스를 선택한 후 + **새 마이그레이션 프로젝트** 를 선택합니다.

3. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **PostgreSQL** 을 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure Database for PostgreSQL** 을 선택합니다.

4. **활동 유형 선택** 섹션에서 **온라인 데이터 마이그레이션** 을 선택합니다.

    ![Azure Database Migration Service 프로젝트 만들기](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기** 를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

5. **저장** 을 선택하고, Azure Database Migration Service를 사용하여 데이터 마이그레이션에 성공하기 위한 요구 사항을 기록한 다음, **활동 만들기 및 실행** 을 선택합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 세부 정보 추가** 화면에서 원본 PostgreSQL 인스턴스에 대한 연결 세부 정보를 지정합니다.

    ![원본 세부 정보 추가 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. **저장** 을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **대상 세부 정보** 화면에서 대상 하이퍼스케일(Citus) 서버에 대한 연결 세부 정보를 지정합니다. 이것은 pg_dump를 사용하여 **DVD 대여** 스키마가 배포된 하이퍼스케일(Citus)의 미리 프로비저닝된 인스턴스입니다.

    ![대상 세부 정보 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. **저장** 을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **저장** 을 선택한 다음, **마이그레이션 설정** 화면에서 기본 값을 적용합니다.

    ![마이그레이션 설정 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. **저장** 을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행** 을 선택합니다.

    마이그레이션 작업 창이 나타나고 작업의 **상태** 가 업데이트되어 **백업 진행 중** 으로 표시됩니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침** 을 선택하여 마이그레이션 **상태** 가 **완료** 로 표시될 때까지 디스플레이를 업데이트합니다.

     ![마이그레이션 프로세스 모니터링](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 마이그레이션이 완료되면 **데이터베이스 이름** 에서 특정 데이터베이스를 선택하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업에 대한 마이그레이션 상태를 얻습니다.

   > [!NOTE]
   > **전체 데이터 로드** 는 초기 로드 마이그레이션 상태를 보여주고, **증분 데이터 동기화** 는 CDC(변경 데이터 캡처) 상태를 보여줍니다.

     ![전체 데이터 로드 세부 정보](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![증분 데이터 동기화 세부 정보](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료** 로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작** 을 선택합니다.

2. **보류 중인 변경 내용** 카운터에 **0** 이 표시되어 원본 데이터베이스로 들어오는 모든 트랜잭션이 중지되었는지 확인하고 **확인** 확인란을 선택한 다음, **적용** 을 선택합니다.

    ![중단 완료 화면](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 데이터베이스 마이그레이션 상태가 **완료됨** 으로 표시되면 [시퀀스를 재생성](https://wiki.postgresql.org/wiki/Fixing_Sequences)(해당하는 경우)하고 애플리케이션을 Azure Database for PostgreSQL의 새 대상 인스턴스에 연결합니다.

## <a name="next-steps"></a>다음 단계

* Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 경우와 관련하여 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure Database for PostgreSQL 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-postgresql-online.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](./dms-overview.md) 문서를 참조하세요.
* Azure Database for PostgreSQL에 대한 자세한 내용은 [Azure Database for PostgreSQL이란?](../postgresql/overview.md) 문서를 참조하세요.
