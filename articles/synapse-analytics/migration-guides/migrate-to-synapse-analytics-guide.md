---
title: 'Azure Synapse Analytics: 마이그레이션 가이드'
description: 이 가이드에 따라 Azure Synapse Analytics 전용 SQL 풀로 데이터베이스를 마이그레이션합니다.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: b81b9898be4e6d02465f7e86fa887d4cfc490a0e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133366926"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀로 데이터 웨어하우스 마이그레이션

다음 섹션에서는 기존 데이터 웨어하우스 솔루션을 Azure Synapse Analytics 전용 SQL 풀로 마이그레이션하는 것과 관련된 작업에 대한 개요를 제공합니다.

## <a name="overview"></a>개요

마이그레이션을 시작하기 전에 Azure Synapse Analytics가 작업에 가장 적합한 솔루션인지 확인해야 합니다. Azure Synapse Analytics는 대규모 데이터 분석을 수행하도록 설계되고 배포된 시스템입니다. Azure Synapse Analytics로 마이그레이션은 이해하기 어렵지 않지만 마이그레이션하려면 디자인을 몇 가지 변경해야 하며, 이 작업을 이행하는 데 약간의 시간이 걸릴 수 있습니다. 비즈니스에 엔터프라이즈 수준의 데이터 웨어하우스가 필요한 경우 충분한 혜택을 얻을 수 있습니다. 그러나 Azure Synapse Analytics의 기능이 필요하지 않은 경우 [SQL Server](/sql/sql-server/) 또는 [Azure SQL Database](../../azure-sql/index.yml)를 사용하는 것이 더 비용 효율적입니다.

다음의 경우 Azure Synapse Analytics를 사용하십시오.

- 하나 이상의 테라바이트의 데이터가 있는 경우
- 많은 양의 데이터에 대한 분석을 실행하려는 경우
- 컴퓨팅 및 스토리지의 크기를 조정하는 기능이 필요한 경우
- 필요하지 않을 때 컴퓨팅 리소스를 일시 중지하여 비용을 절감하고자 합니다.

Azure Synapse Analytics 대신 다음과 같은 작업(OLTP) 워크로드에 대한 다른 옵션을 고려합니다.

- 높은 빈도의 읽기 및 쓰기
- 많은 수의 싱글톤 선택
- 많은 양의 단일 행 삽입
- 행 단위 처리 요구 사항
- 호환되지 않는 형식(예: JSON 및 XML)

## <a name="azure-synapse-pathway"></a>Azure Synapse 경로

중요한 차단 고객 중 하나는 한 시스템에서 다른 시스템으로 마이그레이션할 때 해당 데이터베이스 개체를 변환하는 것입니다. [Azure Synapse Pathway](/sql/tools/synapse-pathway/azure-synapse-pathway-overview)는 기존 데이터 웨어하우스의 개체 변환을 자동화하여 최신 데이터 웨어하우스 플랫폼으로 업그레이드하는 데 도움이 됩니다. 무료로 제공되며 직관적이고 사용이 간단한 도구로, 코드 변환을 자동화하여 Azure Synapse Analytics로 신속하게 마이그레이션할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="migrate-from-sql-server"></a>[SQL Server에서 마이그레이션](#tab/migratefromSQLServer)

SQL Server 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음 필수 구성 요소를 충족하는지 확인합니다.

- 데이터 웨어하우스 또는 분석 워크로드가 있습니다.
- 최신 버전의 [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061)를 다운로드하여 SQL Server 개체를 Azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역에 [전용 SQL 풀](../get-started-create-workspace.md)이 있습니다.

# <a name="migrate-from-netezza"></a>[Netezza에서 마이그레이션](#tab/migratefromNetezza)

Netezza 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음 필수 구성 요소를 충족하는지 확인합니다.

- 최신 버전의 [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061)를 다운로드하여 SQL Server 개체를 Azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역에 [전용 SQL 풀](../get-started-create-workspace.md)이 있습니다.

자세한 내용은 [Azure Synapse Analytics 솔루션 및 Netezza에 대한 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)을 참조하세요.

# <a name="migrate-from-snowflake"></a>[Snowflake에서 마이그레이션](#tab/migratefromSnowflake)

Snowflake 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음 필수 구성 요소를 충족하는지 확인합니다.

- 최신 버전의 [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061)를 다운로드하여 Snowflake 개체를 Azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역에 [전용 SQL 풀](../get-started-create-workspace.md)이 있습니다.

# <a name="migrate-from-oracle"></a>[Oracle에서 마이그레이션](#tab/migratefromOracle)

Oracle 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음 필수 구성 요소를 충족하는지 확인합니다.

- 데이터 웨어하우스 또는 분석 워크로드가 있습니다.
- Oracle에 대한 SQL Server Migration Assistant를 다운로드하여 Oracle 개체를 SQL Server로 변환합니다. 자세한 내용은 [Oracle 데이터베이스를 SQL Server로 마이그레이션(OracleToSQL)](/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)을 참조하세요.
- 최신 버전의 [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=103061)를 다운로드하여 SQL Server 개체를 Azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역에 [전용 SQL 풀](../get-started-create-workspace.md)이 있습니다.

자세한 내용은 [Azure Synapse Analytics 솔루션 및 Oracle 데이터 웨어하우스에 대한 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)을 참조하세요.

---

## <a name="pre-migration"></a>사전 마이그레이션

기존 솔루션을 Azure Synapse Analytics로 마이그레이션하도록 결정한 후에는 시작하기 전에 마이그레이션을 계획해야 합니다. 계획의 주요 목표는 데이터, 테이블 스키마 및 코드가 Azure Synapse Analytics와 호환되는지 확인하는 것입니다. 사용자가 해결해야 하는 현재 시스템과 Azure Synapse Analytics 간에는 몇 가지 호환성 차이가 있습니다. 또한 많은 양의 데이터를 Azure로 마이그레이션하는 데 시간이 걸립니다. 신중한 계획은 데이터를 Azure로 가져오는 프로세스를 가속화합니다.

계획의 또 다른 주요 목표는 솔루션이 Azure Synapse Analytics가 제공하도록 설계된 높은 쿼리 성능을 최대한 활용할 수 있도록 디자인을 조정하는 것입니다. 확장을 위한 데이터 웨어하우스의 디자인은 고유한 디자인 패턴을 도입하므로 일반적인 접근 방식이 항상 최선은 아닙니다. 마이그레이션 후에 일부 디자인을 조정할 수 있지만 프로세스 초기에 변경하면 나중에 시간을 절약할 수 있습니다.

## <a name="migrate"></a>Migrate

마이그레이션을 성공적으로 수행하려면 테이블 스키마, 코드 및 데이터를 마이그레이션해야 합니다. 해당 항목에 대한 자세한 참고 자료는 다음 문서를 참조하세요.

- [테이블 디자인 고려](../sql-data-warehouse/sql-data-warehouse-tables-overview.md)
- [코드 변경 고려](../sql-data-warehouse/sql-data-warehouse-overview-develop.md#development-recommendations-and-coding-techniques)
- [데이터 마이그레이션](../sql-data-warehouse/design-elt-data-loading.md)
- [워크로드 관리 고려](../sql-data-warehouse/sql-data-warehouse-workload-management.md)

## <a name="more-resources"></a>추가 리소스

고객 자문 팀에는 블로그 게시물로 게시된 몇 가지 훌륭한 Azure Synapse Analytics(이전의 Azure SQL Data Warehouse) 참고 자료가 있습니다. 마이그레이션에 대한 자세한 내용은 [Azure SQL Data Warehouse로 데이터 마이그레이션 사례](/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)를 참조하세요.

## <a name="migration-assets-from-real-world-engagements"></a>실제 계약의 마이그레이션 자산

이 마이그레이션 시나리오를 완료하는 방법에 대한 추가 지원은 다음 리소스를 참조하세요. 실제 마이그레이션 프로젝트 참여를 지원하기 위해 개발되었습니다.

| 제목/링크                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Data Workload Assessment Model and Tool](https://www.microsoft.com/download/details.aspx?id=103130)(데이터 워크로드 평가 모델 및 도구) | 이 도구는 지정된 워크로드에 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태, 애플리케이션 또는 데이터베이스 수정 수준을 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다. |
| [Azure Synapse Analytics로 데이터를 로드하는 동안 데이터 인코딩 문제 처리](https://azure.microsoft.com/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | 이 블로그 게시물에서는 PolyBase를 사용하여 SQL Data Warehouse로 데이터를 로드하는 동안 발생할 수 있는 몇 가지 데이터 인코딩 문제에 대한 인사이트를 제공합니다. 또한 이 문서에서는 이러한 문제를 극복하고 데이터를 성공적으로 로드하는 데 사용할 수 있는 몇 가지 옵션을 제공합니다. |
| [Azure Synapse Analytics 전용 SQL 풀에서 테이블 크기 가져오기](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | 설계자가 수행해야 하는 주요 작업 중 하나는 마이그레이션 후 새 환경에 대한 메트릭을 가져오는 것입니다. 예를 들어 온-프레미스에서 클라우드로 로드 시간을 수집하고 PolyBase 로드 시간을 수집합니다. 가장 중요한 작업 중 하나는 고객의 현재 플랫폼과 비교하여 SQL Data Warehouse에서 스토리지 크기를 확인하는 것입니다. |

데이터 SQL 엔지니어링 팀이 이러한 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.

## <a name="videos"></a>동영상

Walgreens가 약 100TB의 데이터를 Netezza에서 Azure Synapse Analytics로 실시간으로 [소매 재고 시스템을 마이그레이션한](https://www.youtube.com/watch?v=86dhd8N1lH4) 방법을 시청하세요.
