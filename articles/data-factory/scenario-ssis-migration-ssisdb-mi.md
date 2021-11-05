---
title: Azure SQL Managed Instance를 데이터베이스 워크로드 대상으로 사용하여 SSIS 마이그레이션
description: Azure SQL Managed Instance를 데이터베이스 워크로드 대상으로 사용하여 SSIS 마이그레이션
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 4696a5405be18ca5ef0f95df9eea20756c02cacf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852202"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Azure SQL Managed Instance를 데이터베이스 워크로드 대상으로 사용하여 SSIS 마이그레이션

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

데이터베이스 워크로드를 SQL Server 인스턴스에서 Azure SQL Managed Instance로 마이그레이션하는 경우, DMS([Azure Data Migration Service](../dms/dms-overview.md))와 [DMS를 사용하는 SQL Managed Instance의 네트워크 토폴로지](../dms/resource-network-topologies.md)에 대해 잘 알고 있어야 합니다.

본 문서에서는 SSISDB(SSIS 카탈로그)에 저장된 SSIS(SQL Server Integration Service)의 마이그레이션과 SSIS 패키지 실행을 예약하는 SQL Server 에이전트 작업에 대해 중점적으로 설명합니다.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSISDB(SSIS 카탈로그) 마이그레이션

SSISDB 마이그레이션은 [SSIS 패키지를 SQL Managed Instance에 마이그레이션하기](../dms/how-to-migrate-ssis-packages-managed-instance.md) 문서에 설명된 것처럼 DMS를 통해 수행할 수 있습니다.

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SQL Managed Instance 에이전트로의 SSIS 작업

SQL Managed Instance에는 온-프레미스 SQL Server 에이전트와 마찬가지로 기본적으로 최고 수준의 스케줄러가 있습니다.  [Azure SQL Managed Instance 에이전트를 사용하여 SSIS 패키지를 실행](how-to-invoke-ssis-package-managed-instance-agent.md)할 수 있습니다.

아직은 SSIS 작업용 마이그레이션 도구를 사용할 수 없기 때문에 해당 작업은 스크립트나 수동 복사를 통해 온-프레미스 SQL Server 에이전트에서 SQL Managed Instance 에이전트로 마이그레이션해야 합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Data Factory](./introduction.md)
- [Azure SSIS 통합 런타임](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [DMS를 사용하는 SQL Managed Instance 마이그레이션에 대한 네트워크 토폴로지](../dms/resource-network-topologies.md)
- [SSIS 패키지를 SQL Managed Instance로 마이그레이션](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>다음 단계

- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure에 배포된 SSIS 패키지 실행](/sql/integration-services/lift-shift/ssis-azure-run-packages)