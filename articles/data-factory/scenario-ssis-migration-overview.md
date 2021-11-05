---
title: 온-프레미스 SSIS(SQL Server Integration Services) 워크로드를 ADF(Azure Data Factory)의 SSIS로 마이그레이션
description: 온-프레미스 SSIS 워크로드를 ADF의 SSIS로 마이그레이션합니다.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 788b25a0f1243710a333cf58627e3ff222c5c694
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853475"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>온-프레미스 SSIS 워크로드를 ADF의 SSIS로 마이그레이션

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>개요

데이터베이스 워크로드를 SQL Server 온-프레미스에서 Azure 데이터베이스 서비스(즉, Azure SQL Database 또는 Azure SQL Managed Instance)로 마이그레이션하는 경우 기본 부가 가치 서비스 중 하나로 SSIS(SQL Server Integration Services)의 ETL 워크로드도 마이그레이션해야 합니다.

ADF(Azure Data Factory)의 Azure-SSIS IR(Integration Runtime)은 SSIS 패키지 실행을 지원합니다. Azure-SSIS IR이 프로비저닝된 후에는 SSDT(SQL Server Data Tools)/SSMS(SQL Server Management Studio)와 같은 익숙한 도구 및 dtinstall/dtutil/dtexec와 같은 명령줄 유틸리티를 사용하여 Azure에서 패키지를 배포하고 실행할 수 있습니다. 자세한 내용은 [Azure SSIS 리프트 앤 시프트 개요](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)를 참조하세요.

이 문서에서는 ETL 워크로드를 온-프레미스 SSIS에서 ADF의 SSIS로 마이그레이션하는 프로세스를 중심으로 설명합니다. 마이그레이션 프로세스는 **평가** 및 **마이그레이션** 의 두 단계로 구성됩니다.

## <a name="assessment"></a>평가

철저한 평가를 통해 완전한 마이그레이션 계획을 세울 경우 성공적인 마이그레이션을 방해하는 원본 SSIS 패키지의 문제를 식별하는 데 도움이 됩니다.

DMA(Data Migration Assistant)는 이 목적을 위해 로컬에서 설치 및 실행할 수 있는 무료 다운로드 가능 도구입니다. SSIS 패키지를 일괄 처리로 평가하고, 다음 범주에 제시된 호환성 문제를 확인하기 위해 **Integration Services** 유형의 DMA 평가 프로젝트를 만들 수 있습니다.

- 마이그레이션 차단기: Azure-SSIS IR에서 실행할 마이그레이션 원본 패키지를 차단하는 호환성 문제입니다. DMA는 이러한 문제를 해결하는 데 도움이 되는 지침을 제공합니다.

- 정보 제공 문제: 원본 패키지에서 사용되는, 부분적으로 지원되거나 사용되지 않는 기능입니다. DMA는 포괄적인 권장 사항, Azure에서 사용 가능한 대체 접근 방식 및 해결할 마이그레이션 단계를 제공합니다.

### <a name="four-storage-types-for-ssis-packages"></a>SSIS 패키지에 대한 4가지 스토리지 유형

- SSIS 카탈로그(SSISDB). SQL Server 2012에 도입되었으며 SSIS 프로젝트/패키지 작업에 사용되는 저장 프로시저, 뷰 및 테이블 반환 함수 집합을 포함합니다.
- 파일 시스템
- MSDB(SQL Server 시스템 데이터베이스).
- SSIS 패키지 저장소. 다음 두 하위 유형 위에 있는 패키지 관리 레이어:
  - MSDB는 SSIS 패키지를 저장하는 데 사용되는 SQL Server의 시스템 데이터베이스입니다.
  - SSIS 패키지를 저장하는 데 사용되는 SQL Server 설치 경로의 특정 폴더인 관리 파일 시스템입니다.

DMA는 현재 **DMA 버전 v5.0** 이후 **파일 시스템**, **패키지 저장소** 및 **SSIS 카탈로그** 에 저장된 패키지의 일괄 처리 평가를 지원합니다.

[DMA](/sql/dma/dma-overview)를 가져오고 [이를 사용하여 패키지 평가를 수행](/sql/dma/dma-assess-ssis)합니다.

## <a name="migration"></a>마이그레이션

원본 SSIS 패키지의 [스토리지 유형](#four-storage-types-for-ssis-packages)과 데이터베이스 워크로드의 마이그레이션 대상에 따라 **SSIS 패키지** 를 마이그레이션하는 단계와 SSIS 패키지 실행을 예약하는 **SQL Server 에이전트 작업** 이 다를 수 있습니다. 다음과 같은 두 가지 시나리오가 있습니다.

- [**Azure SQL Managed Instance** 를 데이터베이스 워크로드 대상으로](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** 를 데이터베이스 워크로드 대상으로](#azure-sql-database-as-database-workload-destination)

[SSIS DevOps 도구](/sql/integration-services/devops/ssis-devops-overview)를 사용하여 마이그레이션 대상에 일괄 처리 패키지를 다시 배포하는 것도 실용적인 방법입니다.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL Managed Instance** 를 데이터베이스 워크로드 대상으로

| **패키지 스토리지 유형** |SSIS 패키지를 일괄로 마이그레이션하는 방법|SSIS 작업을 일괄로 마이그레이션하는 방법|
|-|-|-|
|SSISDB|<li> SSDT/SSMS를 통해 패키지를 Azure Managed Instance에 호스트된 SSISDB에 다시 배포합니다. 자세한 내용은 [Azure에서 SSIS 패키지 배포](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)를 참조하세요. <li> [**SSISDB** 마이그레이션](scenario-ssis-migration-ssisdb-mi.md)|<li>[SSIS 작업을 Azure SQL Managed Instance 에이전트로 마이그레이션](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|
|파일 시스템|dtinstall/dtutil/manual copy를 통해 file shares/Azure Files에 다시 배포하거나 VNet/자체 호스팅 IR을 통해 파일 시스템에 액세스할 수 있도록 유지합니다. 자세한 내용은 [dtutil 유틸리티](/sql/integration-services/dtutil-utility)를 참조하세요.|<li>[SSIS 작업을 Azure SQL Managed Instance 에이전트로 마이그레이션](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> [SSMS에서 SSIS 작업 마이그레이션 마법사](how-to-migrate-ssis-job-ssms.md)로 마이그레이션 <li>scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|
|SQL Server(MSDB)|SSMS/dtutil을 통해 file systems/file shares/Azure Files로 내보냅니다. 자세한 내용은 [SSIS 패키지 내보내기](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)를 참조하세요.|scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|
|패키지 저장소|SSMS/dtutil을 통해 패키지 저장소로 내보내거나 dtinstall/dtutil/manual copy를 통해 패키지 저장소에 다시 배포합니다. 자세한 내용은 [Azure-SSIS Integration Runtime 패키지 저장소를 사용하여 패키지 관리](azure-ssis-integration-runtime-package-store.md)를 참조하세요.|<li>[SSIS 작업을 Azure SQL Managed Instance 에이전트로 마이그레이션](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** 를 데이터베이스 워크로드 대상으로

| **패키지 스토리지 유형** |SSIS 패키지를 일괄로 마이그레이션하는 방법|작업을 일괄로 마이그레이션하는 방법|
|-|-|-|
|SSISDB|SSDT/SSMS를 통해 패키지를 Azure SQL Database에 호스트된 SSISDB에 다시 배포합니다. 자세한 내용은 [Azure에서 SSIS 패키지 배포](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)를 참조하세요.|scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|
|파일 시스템|dtinstall/dtutil/manual copy를 통해 file shares/Azure Files에 다시 배포하거나 VNet/자체 호스팅 IR을 통해 파일 시스템에 액세스할 수 있도록 유지합니다. 자세한 내용은 [dtutil 유틸리티](/sql/integration-services/dtutil-utility)를 참조하세요.|<li> [SSMS에서 SSIS 작업 마이그레이션 마법사](how-to-migrate-ssis-job-ssms.md)로 마이그레이션 <li> scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|
|SQL Server(MSDB)|SSMS/dtutil을 통해 file systems/file shares/Azure Files로 내보냅니다. 자세한 내용은 [SSIS 패키지 내보내기](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)를 참조하세요.|scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|
|패키지 저장소|SSMS/dtutil을 통해 file systems/file shares/Azure Files로 내보내거나, dtinstall/dtutil/manual copy를 통해 file shares/Azure Files에 다시 배포하거나, VNet/자체 호스팅 IR을 통해 액세스하도록 파일 시스템에 유지합니다. 자세한 내용은 dtutil 유틸리티를 참조하세요. 자세한 내용은 [dtutil 유틸리티](/sql/integration-services/dtutil-utility)를 참조하세요.|scripts/SSMS/ADF 포털을 통해 ADF pipelines/activities/triggers로 변환합니다. 자세한 내용은 [SSMS 예약 기능](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.|

## <a name="additional-resources"></a>추가 리소스

- [Azure Data Factory](./introduction.md)
- [데이터베이스 마이그레이션 도우미](/sql/dma/dma-overview)
- [SSIS 워크로드를 클라우드로 전환](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps 도구](/sql/integration-services/devops/ssis-devops-overview)
- [SSIS 패키지를 Azure SQL Managed Instance로 마이그레이션](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Azure SQL Database에 패키지 다시 배포](../dms/how-to-migrate-ssis-packages.md)

- [Azure-SSIS Integration Runtime에서 온-프레미스 데이터 액세스](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Azure-SSIS Integration Runtime 설치 사용자 지정](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Azure에서 SSIS 패키지의 Windows 인증을 사용하여 데이터 저장소 및 파일 공유에 액세스](ssis-azure-connect-with-windows-auth.md)
- [관리 ID 인증 사용](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Azure Key Vault 사용](store-credentials-in-key-vault.md)
- [고성능을 위해 Azure-SSIS Integration Runtime 구성](configure-azure-ssis-integration-runtime-performance.md)
- [일정에 따라 Azure-SSIS Integration Runtime을 시작하고 중지하는 방법](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>다음 단계

- [Azure에 배포된 SSIS 패키지 유효성 검사](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Azure에 배포된 SSIS 패키지 실행](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS Integration Runtime 모니터링](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure에서 SSIS 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)