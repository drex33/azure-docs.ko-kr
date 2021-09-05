---
title: 데이터 검색 및 분류
description: Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에 대한 데이터 검색 및 분류
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/16/2021
tags: azure-synapse
ms.openlocfilehash: e61660a5c559012cbf4940356bd1a204f3203db6
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122538250"
---
# <a name="data-discovery--classification"></a>데이터 검색 및 분류
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

데이터 검색 및 분류는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에 기본 제공됩니다. 데이터베이스에서 중요한 데이터의 검색, 분류, 레이블 지정 및 보고를 위한 기본 기능을 제공합니다.

가장 중요한 데이터에는 비즈니스, 금융, 의료 또는 개인 정보가 포함될 수 있습니다. 다음에 대한 인프라를 제공할 수 있습니다.

- 규정 준수를 위한 데이터 개인 정보 및 요구 사항에 대한 표준을 충족하는 데 도움이 됩니다.
- 중요한 데이터에 대한 액세스 모니터링(감사)과 같은 다양한 보안 시나리오
- 매우 중요한 데이터가 들어 있는 데이터베이스에 대한 액세스 제어 및 보안 강화

> [!NOTE]
> SQL Server 온-프레미스에 대한 자세한 내용은 [SQL 데이터 검색 및 분류](/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조하세요.

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>데이터 검색 및 분류란?

데이터 검색 및 분류는 현재 다음 기능을 지원합니다.

- **검색 및 권장 사항:** 분류 엔진은 데이터베이스를 검사하여 잠재적으로 중요한 데이터가 포함된 열을 확인합니다. 그런 다음, Azure Portal을 통해 권장되는 분류를 검토하고 적용하는 쉬운 방법을 제공합니다.

- **레이블 지정:** SQL Server 데이터베이스 엔진에 추가된 새 메타데이터 특성을 사용하여 민감도 분류 레이블을 열에 영구적으로 적용할 수 있습니다. 그런 다음, 이 메타데이터는 민감도 기반 감사 시나리오에 사용될 수 있습니다.

- **쿼리 결과 집합 민감도:** 쿼리 결과 집합의 민감도는 감사 목적으로 실시간 계산됩니다.

- **가시성:** 데이터베이스 분류 상태는 Azure Portal의 세부 대시보드에서 볼 수 있습니다. 또한 호환성 및 감사 목적 및 기타 요구 사항에 사용할 수 있도록 보고서를 Excel 형식으로 다운로드할 수 있습니다.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>중요한 열 검색, 분류 및 레이블 지정

이 섹션에서는 다음에 대한 단계를 설명합니다.

- 데이터베이스의 중요한 데이터를 포함하는 열을 검색, 분류 및 레이블 지정
- 데이터베이스의 현재 분류 상태 보기 및 보고서 내보내기

분류에는 두 개의 메타데이터 특성이 포함됩니다.

- **레이블**: 열에 저장된 데이터의 민감도 수준을 정의하는 데 사용되는 주 분류 특성입니다.  
- **정보 유형**: 열에 저장된 데이터 형식에 대한 보다 세부적인 정보를 제공하는 특성입니다.

### <a name="define-and-customize-your-classification-taxonomy"></a>분류 체계 정의 및 사용자 지정

데이터 검색 및 분류는 민감도 레이블 집합 및 정보 유형과 검색 논리 집합을 기본적으로 제공합니다. 이 분류법을 사용자 지정하고, 사용자 환경에 맞게 분류 구문 집합과 순위를 정의할 수 있습니다.

전체 Azure 조직에 대해 중앙의 한 위치에서 분류 체계를 정의하고 사용자 지정합니다. 해당 위치는 보안 정책의 일부로 [Azure Security Center](../../security-center/security-center-introduction.md)에 있습니다. 조직의 루트 관리 그룹에 대한 관리 권한이 있는 사람만이 이 작업을 수행할 수 있습니다.

정책 관리의 일환으로, 사용자 지정 레이블을 정의하고, 순위를 지정하고, 선택한 정보 유형 세트와 연결할 수 있습니다. 사용자 고유의 사용자 지정 정보 유형을 추가하고 문자열 패턴을 사용하여 구성할 수도 있습니다. 패턴은 데이터베이스에서 이러한 유형의 데이터를 식별하기 위한 검색 논리에 추가됩니다.

자세한 내용은 [Azure Security Center에서 SQL 정보 보호 정책 사용자 지정(미리 보기)](../../security-center/security-center-info-protection-policy.md)을 참조하세요.

조직 전체 정책을 정의한 후에는 사용자 지정된 정책을 사용하여 개별 데이터베이스 분류를 계속할 수 있습니다.

### <a name="classify-your-database"></a>데이터베이스 분류

> [!NOTE]
> 아래 예제에서는 Azure SQL Database를 사용하지만 데이터 검색 및 분류를 구성하려는 적절한 제품을 선택해야 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. Azure SQL Database 창의 **보안** 제목 아래에 있는 **데이터 검색 및 분류** 로 이동합니다. 개요 탭에는 데이터베이스의 현재 분류 상태 요약이 포함되어 있습니다. 요약에는 특정 스키마 부분, 정보 유형 및 레이블만 표시하도록 필터링할 수도 있는 분류된 모든 열의 자세한 목록이 포함됩니다. 아직 어떠한 열도 분류하지 않은 경우 [4단계로 건너뜁니다](#step-4).

    ![개요](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. 보고서를 Excel 형식으로 다운로드하려면 창의 위쪽 메뉴에서 **내보내기** 를 선택합니다.

1. <a id="step-4"></a>데이터 분류를 시작하려면 **데이터 검색 및 분류** 페이지에서 **분류** 탭을 선택합니다.

    분류 엔진은 잠재적으로 중요한 데이터를 포함하는 열에서 데이터베이스를 검사하고 권장된 열 분류 목록을 제공합니다.

1. 분류 권장 사항 보기 및 적용:

   - 권장된 열 분류 목록을 보려면 창의 맨 아래에서 권장 사항 패널을 선택합니다.

   - 특정 열에 대한 권장 사항을 허용하려면 관련 행의 왼쪽 열에서 확인란을 선택합니다. 모든 권장 사항을 허용됨으로 표시하려면 권장 사항 테이블 헤더에서 가장 왼쪽에 있는 확인란을 선택합니다.

   - 선택한 권장 사항을 적용하려면 **선택한 권장 사항 허용** 을 선택합니다.

   ![분류에 대한 권장 사항](./media/data-discovery-and-classification-overview/recommendation.png)

1. 대안으로 열을 수동으로 분류하거나 권장 사항 기반 분류로 지정할 수도 있습니다.

   1. 창의 위쪽 메뉴에서 **분류 추가** 를 선택합니다.

   1. 열려 있는 컨텍스트 창에서 분류하려는 스키마, 테이블 및 열을 선택하고 정보 유형 및 민감도 레이블을 선택합니다.

   1. 컨텍스트 창의 아래쪽에서 **분류 추가** 를 선택합니다.

   ![수동으로 분류 추가](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. 분류를 완료하고 데이터베이스 열에 새 분류 메타데이터로 영구 레이블(태그)을 지정하려면 **분류** 페이지에서 **저장** 을 선택합니다.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>중요한 데이터에 대한 액세스 감사

분류의 중요한 측면은 중요한 데이터에 대한 액세스를 모니터링하는 기능입니다. [Azure SQL 감사](../../azure-sql/database/auditing-overview.md) 기능이 개선되어 `data_sensitivity_information`이라는 감사 로그에 새 필드가 포함되었습니다. 이 필드는 쿼리에 의해 반환된 데이터의 민감도 분류(레이블)를 기록합니다. 예를 들면 다음과 같습니다.

![감사 로그](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>권한

이러한 기본 제공 역할은 데이터베이스의 데이터 분류를 읽을 수 있습니다.

- 소유자
- 읽기 권한자
- 참가자
- SQL 보안 관리자
- 사용자 액세스 관리자

이러한 기본 제공 역할은 데이터베이스의 데이터 분류를 수정할 수 있습니다.

- 소유자
- 참가자
- SQL 보안 관리자

[Azure RBAC](../../role-based-access-control/overview.md)의 역할 기반 권한에 대해 자세히 알아보세요.

## <a name="manage-classifications"></a><a id="manage-classification"></a>분류 관리

T-SQL, REST API 또는 PowerShell을 사용하여 분류를 관리할 수 있습니다.

### <a name="use-t-sql"></a>T-SQL 사용

T-SQL을 사용하여 열 분류를 추가 또는 제거하고 전체 데이터베이스에 대한 모든 분류를 검색할 수 있습니다.

> [!NOTE]
> T-SQL을 사용하여 레이블을 관리하는 경우 열에 추가하는 레이블이 조직 정보 보호 정책(포털 권장 사항에 표시되는 레이블 집합)에 있는지 확인되지 않습니다. 따라서 이 유효성 검사는 사용자가 수행해야 합니다.

분류에 T-SQL을 사용하는 방법에 대한 자세한 내용은 다음 참조를 참조하세요.

- 하나 이상의 열 분류 추가 또는 업데이트하기: [ADD SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 하나 이상의 열에서 분류 제거하기: [DROP SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 데이터베이스에 대한 모든 분류 보기: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>PowerShell cmdlet 사용
PowerShell을 사용하여 Azure SQL Database 및 Azure SQL Managed Instance에 대한 분류 및 권장 사항을 관리합니다.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Azure SQL Database용 PowerShell cmdlet

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance용 PowerShell cmdlet

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>REST API 사용

REST API를 사용하여 분류 및 권장 사항을 프로그래밍 방식으로 관리할 수 있습니다. 게시된 REST API는 다음과 같은 작업을 지원합니다.

- [만들기 또는 업데이트](/rest/api/sql/sensitivitylabels/createorupdate): 지정된 열의 민감도 레이블을 만들거나 업데이트합니다.
- [삭제](/rest/api/sql/sensitivitylabels/delete): 지정된 열의 민감도 레이블을 삭제합니다.
- [권장 사항 사용 안 함](/rest/api/sql/sensitivitylabels/disablerecommendation): 지정된 열에 대한 민감도 권장 사항을 사용하지 않도록 설정합니다.
- [권장 사항 사용](/rest/api/sql/sensitivitylabels/enablerecommendation): 지정된 열에 대한 민감도 권장 사항을 사용하도록 설정합니다. (권장 사항은 모든 열에 대해 기본적으로 사용하도록 설정되어 있습니다.)
- [가져오기](/rest/api/sql/sensitivitylabels/get): 지정된 역의 민감도 레이블을 가져옵니다.
- [데이터베이스별 최신 목록](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): 지정된 데이터베이스의 최신 민감도 레이블을 가져옵니다.
- [데이터베이스별 권장되는 목록](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): 지정된 데이터베이스의 권장되는 민감도 레이블을 가져옵니다.


## <a name="faq---advanced-classification-capabilities"></a>FAQ - 고급 분류 기능

**질문**: SQL 데이터 검색 및 분류가 [Azure Purview](../../purview/overview.md)로 대체되거나 곧 사용 중지되나요?
**답변**: SQL 데이터 검색 및 분류를 계속 지원하며 고급 분류 기능 및 데이터 거버넌스를 구동할 수 있는 더 다양한 기능을 갖춘 [Azure Purview](../../purview/overview.md)를 채택할 것을 권장합니다. 서비스, 기능, API 또는 SKU를 사용 중지하기로 결정되면 마이그레이션 또는 전환 경로가 포함된 사전 통지를 보내드립니다. 여기에서 Microsoft 수명 주기 정책에 대한 자세한 내용을 살펴봅니다.


## <a name="next-steps"></a><a id="next-steps"></a>다음 단계

- 분류된 중요한 데이터에 대한 액세스를 모니터링 및 감사하기 위해 [Azure SQL 감사](../../azure-sql/database/auditing-overview.md)를 구성하는 것이 좋습니다.
- 데이터 검색 및 분류를 포함하는 프레젠테이션은 [SQL 데이터 검색, 분류, 레이블 지정 및 보호 | 데이터 노출](https://www.youtube.com/watch?v=itVi9bkJUNc)을 참조하세요.
- T-SQL 명령을 사용하여 Azure Purview 레이블로 Azure SQL Database 및 Azure Synapse Analytics를 분류하려면 [Azure Purview 레이블을 사용하여 Azure SQL 데이터 분류](../../sql-database/scripts/sql-database-import-purview-labels.md)를 참조하세요.
