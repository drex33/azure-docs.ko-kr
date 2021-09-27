---
title: BCDR(비즈니스 연속성 및 재해 복구)를 위한 Azure-SSIS 통합 런타임 구성
description: 이 문서에서는 BCDR(비즈니스 연속성 및 재해 복구)를 위해 Azure SQL Database/Managed Instance 장애 조치(failover) 그룹을 사용하여 Azure Data Factory에서 Azure-SSIS 통합 런타임을 구성하는 방법을 설명합니다.
services: data-factory
ms.service: data-factory
ms.subservice: integration-services
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 246ed5dba1c19894fee0f3cf9f4fe1a72efafa18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828364"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>BCDR(비즈니스 연속성 및 재해 복구)를 위한 Azure-SSIS 통합 런타임 구성 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

ADF(Azure Data Factory)에서 Azure SQL Database/Managed Instance 및 SSIS(SQL Server Integration Services)는 SQL Server 마이그레이션에 권장되는 모든 PaaS(Platform as a Service) 솔루션으로 결합될 수 있습니다. Azure SQL Database/Managed Instance에 의해 호스트되는 SSISDB(SSIS 카탈로그 데이터베이스)에 SSIS 프로젝트를 배포하고 ADF의 Azure SSIS IR(통합 런타임)에서 SSIS 패키지를 실행할 수 있습니다.

BCDR(비즈니스 연속성 및 재해 복구)의 경우 Azure SQL Database/Managed Instance는 [지역 복제/장애 조치(failover) 그룹](../azure-sql/database/auto-failover-group-overview.md)으로 구성될 수 있습니다. 여기서 읽기/쓰기 액세스 권한(기본 역할)이 있는 주 Azure 지역의 SSISDB가 읽기 전용 액세스 권한(보조 역할)을 사용하여 보조 지역으로 계속 복제됩니다. 주 지역에서 재해가 발생하면 기본/보조 SSISDB가 역할을 교환하는 장애 조치(failover)가 트리거됩니다.

BCDR의 경우 Azure SQL Database/Managed Instance 장애 조치(failover) 그룹과 동기화된 상태에서 작동하는 이중 대기 Azure SSIS IR 쌍도 구성할 수 있습니다. 이를 통해 지정된 시간에 하나만 기본 SSISDB에 액세스하여 패키지를 가져오고 실행할 수 있을 뿐만 아니라 패키지 실행 로그(기본 역할)를 작성할 수 있으며, 다른 하나는 예컨대 Azure Files(보조 역할)와 같이 다른 곳에 배포된 패키지에 한해 동일한 작업을 수행할 수 있는 한 쌍의 Azure-SSIS IR를 실행할 수 있습니다. SSISDB 장애 조치(failover)가 발생하면 기본/보조 Azure SSIS IR도 역할을 교환하며 둘 다 실행 중인 경우 가동 중지 시간이 거의 0에 가깝습니다.

이 문서에서는 BCDR용 Azure SQL Database/Managed Instance 장애 조치(failover) 그룹을 사용하여 Azure-SSIS IR를 구성하는 방법을 설명합니다.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Azure SQL Database 장애 조치(failover) 그룹을 사용하여 이중 대기 Azure-SSIS IR 쌍 구성

Azure SQL Database 장애 조치(failover) 그룹과 동기화된 상태에서 작동하는 이중 대기 Azure-SSIS IR 쌍을 구성하려면 다음 단계를 완료합니다.

1. Azure Portal/ADF UI를 통해 기본 Azure SQL Database 서버를 사용하는 새로운 Azure-SSIS IR를 만들어 주 지역에 SSISDB를 호스트할 수 있습니다. 기본 Azure SQL Database 서버에서 호스트하는 SSIDB에 이미 연결된 기존 Azure-SSIS IR가 있고 계속 실행 중인 경우 재구성하려면 먼저 중지해야 합니다. 이것이 기본 Azure-SSIS IR가 됩니다.

   **Integration Runtime 설정** 창의 **배포 설정** 페이지에서 [SSISDB를 사용하도록 선택](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb)하는 경우, **SSISDB 장애 조치(failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택합니다. **이중 대기 쌍 이름** 에 기본/보조 AZURE-SSIS IR 쌍을 식별하는 이름을 입력합니다. 기본 Azure-SSIS IR 만들기를 완료하면 해당 IR가 시작되고 읽기/쓰기 액세스 권한을 이용해 사용자 대신 만들게 될 기본 SSISDB에 연결됩니다. 방금 재구성한 경우 다시 시작해야 합니다.

1. Azure Portal을 사용하여 기본 SSISDB가 기본 Azure SQL Database 서버의 **개요** 페이지에 만들어졌는지 여부를 확인할 수 있습니다. 만든 후에는 **장애 조치(failover) 그룹** 페이지에서 [기본/보조 Azure SQL Database 서버에 대한 장애 조치(failover) 그룹을 만들고 SSISDB를 추가](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group)할 수 있습니다. 장애 조치(failover) 그룹을 만든 후에는 보조 Azure SQL Database 서버의 **개요** 페이지에서 기본 SSISDB가 읽기 전용 액세스 권한이 있는 보조 SSISDB에 복제되었는지 여부를 확인할 수 있습니다.

1. Azure Portal/ADF UI를 통해 보조 Azure SQL Database 서버를 사용하는 다른 Azure-SSIS IR를 만들어 보조 지역에 SSISDB를 호스트할 수 있습니다. 이것이 보조 Azure-SSIS IR가 됩니다. 전체 BCDR에 대해 종속된 모든 리소스(예: 사용자 지정 설치 스크립트/파일을 저장하기 위한 Azure Storage, 오케스트레이션/일정 예약 패키지 실행을 위한 ADF 등)가 보조 지역에도 만들어졌는지 확인합니다.

   **Integration Runtime 설정** 창의 **배포 설정** 페이지에서 [SSISDB를 사용하도록 선택](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb)하는 경우, **SSISDB 장애 조치(failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택합니다. **이중 대기 쌍 이름** 에 기본/보조 Azure-SSIS IR 쌍을 식별하는 동일한 이름을 입력합니다. 보조 Azure-SSIS IR 만들기를 완료하면 해당 IR가 시작되고 보조 SSISDB에 연결됩니다.

1. SSISDB 장애 조치(failover)가 발생할 때 가동 중지 시간이 거의 0이 되도록 하려면 두 Azure-SSIS IR를 모두 계속 실행합니다. 기본 Azure-SSIS IR만 기본 SSISDB에 액세스하여 패키지를 가져오고 실행할 수 있을 뿐만 아니라 패키지 실행 로그를 작성할 수 있으며, 보조 Azure-SSIS IR는 예컨대 Azure Files와 같이 다른 곳에 배포된 패키지에 한해 동일한 작업을 수행할 수 있습니다.

   실행 비용을 최소화하려는 경우 보조 Azure-SSIS IR를 만든 후 중지할 수 있습니다. SSISDB 장애 조치(failover)가 발생하면 기본/보조 Azure SSIS IR에서 역할을 교환합니다. 기본 Azure-SSIS IR가 중지된 경우 다시 시작해야 합니다. 가상 네트워크에 삽입되었는지 여부와 삽입 방법에 따라 실행하는 데 5 분 이내 또는 약 20~30분의 시간이 소요됩니다.

1. [오케스트레이션/일정 예약 패키지 실행을 위해 ADF를 사용](./how-to-invoke-ssis-package-ssis-activity.md)하는 경우, SSIS 패키지 실행 활동 및 관련 트리거가 포함된 모든 관련 ADF 파이프라인이 처음에 트리거를 사용하지 않도록 설정된 상태로 보조 ADF에 복사되는지 확인합니다. SSISDB 장애 조치(failover)가 발생하는 경우 이를 사용하도록 설정해야 합니다.

1. [Azure SQL Database 장애 조치(failover) 그룹을 테스트](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover)하고 [ADF 포털에서 Azure-SSIS IR 모니터링 페이지](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)에서 기본/보조 Azure SSIS IR가 역할을 교환했는지 여부를 확인할 수 있습니다. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Azure SQL Managed Instance 장애 조치(failover) 그룹을 사용하여 이중 대기 Azure-SSIS IR 쌍 구성

Azure SQL Managed Instance 장애 조치(failover) 그룹과 동기화된 상태에서 작동하는 이중 대기 Azure-SSIS IR 쌍을 구성하려면 다음 단계를 완료합니다.

1. Azure Portal을 사용하여 기본 Azure SQL Managed Instance의 **장애 조치(Failover) 그룹** 페이지에서 [기본/보조 Azure SQL Managed Instance에 대한 장애 조치(failover) 그룹을 만들](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal) 수 있습니다.

1. Azure Portal/ADF UI를 통해 기본 Azure SQL Managed Instance를 사용하는 새로운 Azure-SSIS IR를 만들어 기본 지역에 SSISDB를 호스트할 수 있습니다. 기본 Azure SQL Managed Instance에서 호스트하는 SSIDB에 이미 연결된 기존 Azure-SSIS IR가 있고 계속 실행 중인 경우 재구성하려면 먼저 중지해야 합니다. 이것이 기본 Azure-SSIS IR가 됩니다.

   **Integration Runtime 설정** 창의 **배포 설정** 페이지에서 [SSISDB를 사용하도록 선택](./create-azure-ssis-integration-runtime.md#creating-ssisdb)하는 경우, **SSISDB 장애 조치(failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택합니다. **이중 대기 쌍 이름** 에 기본/보조 AZURE-SSIS IR 쌍을 식별하는 이름을 입력합니다. 기본 Azure-SSIS IR 만들기를 완료하면 해당 IR가 시작되고 읽기/쓰기 액세스 권한을 이용해 사용자 대신 만들게 될 기본 SSISDB에 연결됩니다. 방금 재구성한 경우 다시 시작해야 합니다. 또한 보조 Azure SQL Managed Instance의 **개요** 페이지에서 기본 SSISDB가 읽기 전용 액세스 권한이 있는 보조 SSISDB에 복제되었는지 여부를 확인할 수 있습니다.

1. Azure Portal/ADF UI를 통해 보조 Azure SQL Managed Instance를 사용하는 다른 Azure-SSIS IR를 만들어 보조 지역에 SSISDB를 호스트할 수 있습니다. 이것이 보조 Azure-SSIS IR가 됩니다. 전체 BCDR에 대해 종속된 모든 리소스(예: 사용자 지정 설치 스크립트/파일을 저장하기 위한 Azure Storage, 오케스트레이션/일정 예약 패키지 실행을 위한 ADF 등)가 보조 지역에도 만들어졌는지 확인합니다.

   **Integration Runtime 설정** 창의 **배포 설정** 페이지에서 [SSISDB를 사용하도록 선택](./create-azure-ssis-integration-runtime.md#creating-ssisdb)하는 경우, **SSISDB 장애 조치(failover)와 함께 이중 대기 Azure-SSIS Integration Runtime 쌍 사용** 확인란을 선택합니다. **이중 대기 쌍 이름** 에 기본/보조 Azure-SSIS IR 쌍을 식별하는 동일한 이름을 입력합니다. 보조 Azure-SSIS IR 만들기를 완료하면 해당 IR가 시작되고 보조 SSISDB에 연결됩니다.

1. Azure SQL Managed Instance에서는 DMK(데이터베이스 마스터 키)를 사용해 데이터를 암호화함으로써 SSISDB와 같이 데이터베이스에 중요한 데이터를 보호할 수 있습니다. DMK 자체는 기본적으로 SMK(서비스 마스터 키)를 사용하여 암호화됩니다. 9 월 2021 이후 SMK는 장애 조치 (failover) 그룹을 만드는 동안 기본 Azure SQL Managed Instance에서 보조 데이터베이스로 복제 됩니다. 이전에 장애 조치 (failover) 그룹을 만든 경우에는 보조 Azure SQL Managed Instance에서 SSISDB를 포함 하 여 모든 사용자 데이터베이스를 삭제 하 고 장애 조치 (failover) 그룹을 다시 만듭니다.

1. SSISDB 장애 조치(failover)가 발생할 때 가동 중지 시간이 거의 0이 되도록 하려면 두 Azure-SSIS IR를 모두 계속 실행합니다. 기본 Azure-SSIS IR만 기본 SSISDB에 액세스하여 패키지를 가져오고 실행할 수 있을 뿐만 아니라 패키지 실행 로그를 작성할 수 있으며, 보조 Azure-SSIS IR는 예컨대 Azure Files와 같이 다른 곳에 배포된 패키지에 한해 동일한 작업을 수행할 수 있습니다.

   실행 비용을 최소화하려는 경우 보조 Azure-SSIS IR를 만든 후 중지할 수 있습니다. SSISDB 장애 조치(failover)가 발생하면 기본/보조 Azure SSIS IR에서 역할을 교환합니다. 기본 Azure-SSIS IR가 중지된 경우 다시 시작해야 합니다. 가상 네트워크에 삽입되었는지 여부와 삽입 방법에 따라 실행하는 데 5 분 이내 또는 약 20~30분의 시간이 소요됩니다.

1. [오케스트레이션/예약 패키지 실행을 위해 Azure SQL Managed Instance 에이전트를 사용](./how-to-invoke-ssis-package-managed-instance-agent.md)하는 경우, 해당 작업 단계 및 관련 일정이 포함된 모든 관련 SSIS 작업이 처음에 일정을 사용하지 않도록 설정한 상태로 보조 Azure SQL Managed Instance에 복사되는지 확인합니다. SSMS를 사용하여 다음 단계를 완료합니다.

   1. 각 SSIS 작업에서 마우스 오른쪽 단추를 클릭하고 **다음으로 스크립트 작업**, **만들 위치** 및 **새 쿼리 편집기 창** 드롭다운 메뉴 항목을 선택하여 해당 스크립트를 생성합니다.

      :::image type="content" source="media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png" alt-text="SSIS 작업 스크립트 생성":::

   1. 생성된 각 SSIS 작업 스크립트의 경우 `sp_add_job` 저장 프로시저를 실행하고 필요에 따라 `@owner_login_name` 인수에 대한 값 할당을 수정/제거하는 명령을 찾습니다.

   1. 업데이트된 각 SSIS 작업 스크립트의 경우 보조 Azure SQL Managed Instance에서 실행하여 작업 단계 및 관련 일정으로 작업을 복사합니다.

   1. 다음 스크립트를 사용하여 기본/보조 Azure SQL Managed Instance에서 모두 각각 기본/보조 SSISDB 역할을 기반으로 SSIS 작업 일정을 사용/사용하지 않도록 설정하는 새 T-SQL 작업을 만들고 정기적으로 실행합니다. SSISDB 장애 조치(failover)가 발생하면 사용하지 않도록 설정된 SSIS 작업 일정이 사용하도록 설정되고, 그 반대의 경우도 마찬가지입니다.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. [오케스트레이션/일정 예약 패키지 실행을 위해 ADF를 사용](./how-to-invoke-ssis-package-ssis-activity.md)하는 경우, SSIS 패키지 실행 활동 및 관련 트리거가 포함된 모든 관련 ADF 파이프라인이 처음에 트리거를 사용하지 않도록 설정된 상태로 보조 ADF에 복사되는지 확인합니다. SSISDB 장애 조치(failover)가 발생하는 경우 이를 사용하도록 설정해야 합니다.

1. [ Azure SQL Managed Instance 장애 조치(failover) 그룹을 테스트](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover)하고 [ADF 포털에서 Azure-SSIS IR 모니터링 페이지](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)에서 기본/보조 Azure SSIS IR가 역할을 교환했는지 여부를 확인할 수 있습니다. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Azure SQL Database/Managed Instance에서 호스트하는 기존 SSISDB에 새 Azure-SSIS IR 연결

재해가 발생하여 기존 Azure-SSIS IR에는 영향을 주지만 동일한 지역의 Azure SQL Database/Managed Instance에는 영향을 주지 않는 경우 다른 지역의 새 항목으로 바꿀 수 있습니다. Azure SQL Database/Managed Instance에서 호스트하는 기존 SSISDB를 새 Azure-SSIS IR에 연결하려면 다음 단계를 완료합니다.

1. 기존 Azure-SSIS IR가 계속 실행 중인 경우 먼저 Azure Portal/ADF UI 또는 Azure PowerShell을 사용하여 중지해야 합니다. 재해가 동일한 지역의 ADF에도 영향을 주는 경우 이 단계를 건너뛸 수 있습니다.

1. SSMS를 사용해 Azure SQL Database/Managed Instance에 다음과 같은 SSISDB 명령을 실행하여 새로운 ADF/Azure-SSIS IR에서 연결을 허용하는 메타데이터를 업데이트합니다.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. [Azure portal/ADF UI](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) 또는 [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)을 사용하여 각각 다른 지역에서 *YourNewADF*/*YourNewAzureSSISIR* 라는 새 ADF/Azure-SSIS IR를 만듭니다. Azure Portal/ADF UI를 사용하는 경우 **Integration Runtime 설정** 창의 **배포 설정** 페이지에서 연결 테스트 오류를 무시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대해 다음과 같은 기타 구성 옵션을 고려할 수 있습니다.

- [Azure-SSIS IR용 패키지 저장소 구성](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Azure-SSIS IR용 사용자 지정 설정 구성](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS IR용 가상 네트워크 주입 구성](./join-azure-ssis-integration-runtime-virtual-network.md)

- [자체 호스팅 IR를 Azure-SSIS IR 프록시로 구성](./self-hosted-integration-runtime-proxy-ssis.md)
