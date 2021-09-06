---
title: SQL Server Integration Services의 계보
description: 이 문서에서는 SQL Server Integration Services에서 데이터 계보 추출을 설명합니다.
author: chugugrace
ms.author: chugu
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 2bdcdf79ee28bf6a3c233decd6dceafce74276ef
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258372"
---
# <a name="how-to-get-lineage-from-sql-server-integration-services-ssis-into-azure-purview"></a>SSIS(SQL Server Integration Services)에서 Azure Purview로 계보를 가져오는 방법

이 문서는 Azure Purview에서 SSIS(SQL Server Integration Services)의 데이터 계보 양상을 설명합니다.

## <a name="prerequisites"></a>필수 조건

- [SQL Server Integration Services 워크로드를 클라우드로 리프트 앤 시프트](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

## <a name="supported-scenarios"></a>지원되는 시나리오

현재 지원 범위에는 Azure Data Factory SSIS 통합 런타임에 의해 실행되는 SSIS 패키지에서의 계보 추출이 포함됩니다.

온-프레미스 SSIS 계보 추출은 아직 지원되지 않습니다.

### <a name="supported-data-stores"></a>지원되는 데이터 저장소

| 데이터 저장소 | 지원됨 |
| ------------------- | ------------------- |
| Azure Blob Storage | 예 |
| Azure Data Lake Storage Gen1 | 예 |
| Azure Data Lake Storage Gen2 | 예 |
| Azure 파일 | 예 |
| Azure SQL 데이터베이스 \* | Yes |
| Azure SQL Managed Instance \*| Yes |
| Azure Synapse Analytics \* | Yes |
| SQL Server \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*


## <a name="how-to-bring-ssis-lineage-into-purview"></a>Purview로 SSIS 계보를 가져오는 방법

### <a name="step-1-connect-a-data-factory-to-azure-purview"></a>1단계. [Azure Purview에 Data Factory 연결](how-to-link-azure-data-factory.md)

### <a name="step-2-trigger-ssis-activity-execution-in-azure-data-factory"></a>2단계. Azure Data Factory에서 SSIS 작업 실행 트리거

[SSIS 패키지 작업 실행으로 SSIS 패키지를 실행](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)하거나 [ADF SSIS Integration Runtime에서 Transact-SQL로 SSIS 패키지를 실행](../data-factory/how-to-invoke-ssis-package-stored-procedure-activity.md)할 수 있습니다.  

SSIS 패키지 실행 작업이 실행을 완료하면 [Data Factory 작업 모니터](../data-factory/monitor-visually.md#monitor-activity-runs)의 작업 출력에서 계보 보고서 상태를 확인할 수 있습니다.
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/activity-report-lineage-status.png" alt-text="ssis-status":::

### <a name="step-3-browse-lineage-information-in-your-azure-purview-account"></a>3단계: Azure Purview 계정에서 계보 정보 찾아보기

- 자산 유형 "SQL Server Integration Services"를 선택하여 Data Catalog를 찾아볼 수 있습니다.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png" alt-text="브라우저 자산" lightbox="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-2.png" alt-text="browser-assets-ssis":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-3.png" alt-text="browse-assets-ssis-package":::

- 키워드를 사용하여 Data Catalog를 검색할 수도 있습니다.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/search-assets.png" alt-text="search-ssis" lightbox="media/how-to-lineage-sql-server-integration-services/search-assets.png":::

- SSIS 패키지 실행 작업의 계보 정보를 확인하고 Data Factory에서 열어 작업 설정을 보거나 편집할 수 있습니다.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage.png" alt-text="show-ssis-lineage" lightbox="media/how-to-lineage-sql-server-integration-services/lineage.png":::

- 하나의 데이터 원본을 선택하여 원본의 열이 대상의 열에 매핑되는 방식을 살펴볼 수 있습니다.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png" alt-text="show-ssis-lineage-drill-in" lightbox="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png":::

## <a name="next-steps"></a>다음 단계

- [SQL Server Integration Services 워크로드를 클라우드로 리프트 앤 시프트](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Azure Purview의 데이터 계보에 대해 알아보기](catalog-lineage-user-guide.md)
- [Azure Data Factory를 연결하여 자동화된 계보 푸시](how-to-link-azure-data-factory.md)