---
title: 전용 SQL 풀(이전의 SQL DW)에 대한 인증
description: Azure AD(Azure Active Directory) 또는 SQL Server 인증을 사용하여 Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)에 인증하는 방법을 알아봅니다.
services: synapse-analytics
author: nanditavalsan
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: nanditav
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 8578f15dae0fc67b70ce656190cf86a937317179
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133288660"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)에 대한 인증

Azure AD(Azure Active Directory) 또는 SQL Server 인증을 사용하여 Azure Synapse에서 전용 SQL 풀(이전의 SQL DW)에 인증하는 방법을 알아봅니다.

전용 SQL 풀(이전의 SQL DW)에 연결하려면 인증 목적으로 보안 자격 증명을 전달해야 합니다. 연결을 설정할 때 특정 연결 설정이 쿼리 세션을 설정하는 일부로 구성됩니다.  

보안에 대한 자세한 내용과 전용 SQL 풀(이전의 SQL DW)에 대한 연결을 설정하는 방법은 [데이터베이스 보호 설명서](sql-data-warehouse-overview-manage-security.md)를 참조하세요.

## <a name="sql-authentication"></a>SQL 인증

전용 SQL 풀(이전의 SQL DW)에 연결하려면 다음 정보를 제공해야 합니다.

* 정규화된 서버 이름
* SQL 인증 지정
* 사용자 이름
* 암호
* 기본 데이터베이스(옵션)

기본적으로 사용자의 연결은 사용자의 사용자 데이터베이스가 아닌 *master* 데이터베이스에 연결합니다. 사용자의 사용자 데이터베이스에 연결하려면 다음과 같은 두 가지 작업 중 하나를 수행하도록 선택할 수 있습니다.

* SSDT, SSMS 또는 애플리케이션 연결 문자열에서 SQL Server 개체 탐색기에 서버를 등록할 때 기본 데이터베이스를 지정합니다. 예를 들어 ODBC 연결에 대해 InitialCatalog 매개 변수를 포함합니다.
* SSDT에서 세션을 만들기 전에 사용자 데이터베이스를 강조 표시합니다.

> [!NOTE]
> 연결을 위한 데이터베이스 변경의 경우 Transact-SQL 문 **USE MyDatabase;** 는 지원되지 않습니다. SSDT를 사용하여 SQL 풀에 연결하는 지침은 [Visual Studio로 쿼리](sql-data-warehouse-query-visual-studio.md) 문서를 참조하세요.

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 인증

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 인증은 Azure AD(Azure Active Directory)에서 ID를 사용하여 SQL 풀에 연결하는 메커니즘입니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 전용 SQL 풀(이전의 sQL DW) 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다.

### <a name="benefits"></a>이점

Azure Active Directory의 이점은 다음과 같습니다.

* SQL Server 인증에 대한 대안을 제공합니다.
* 서버 전체에서 사용자 ID의 확산을 중지하는 데 도움이 됩니다.
* 한 위치에서의 암호 회전이 가능합니다.
* 외부 Azure AD 그룹을 사용하여 데이터베이스 권한을 관리합니다.
* Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
* 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
* SQL 풀에 연결되는 애플리케이션에 대한 토큰 기반 인증을 지원합니다.
* [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 및 [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 포함하는 다양한 도구에 대한 Active Directory 유니버설 인증을 통해 다단계 인증을 지원합니다.

> [!NOTE]
> Azure Active Directory는 비교적 새로운 기능으로, 몇 가지 제한 사항이 있습니다. Azure Active Directory가 작업 환경에 적합한지 확인하려면 [Azure AD 기능 및 제한 사항](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), 특히 추가 고려 사항을 참조하세요.

### <a name="configuration-steps"></a>구성 단계

다음 단계에 따라 Azure Active Directory 인증을 구성합니다.

1. Azure Active Directory 만들기 및 채우기
2. 선택 사항: 현재 Azure 구독과 연결된 Active Directory 연결 또는 변경
3. Azure Synapse에 대한 Azure Active Directory 관리자 만들기
4. 클라이언트 컴퓨터 구성
5. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기
6. Azure AD ID를 사용하여 SQL 풀에 연결

현재 Azure Active Directory 사용자는 SSDT 개체 탐색기에 표시되지 않습니다. 해결 방법으로 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)에서 사용자를 봅니다.

### <a name="find-the-details"></a>세부 정보 찾기

* Azure Active Directory 인증을 구성하고 사용하는 단계는 Azure SQL Database 및 Azure Synapse에 있는 Synapse SQL의 경우와 거의 동일합니다. [Azure Active Directory 인증을 사용하여 SQL Database 또는 SQL 풀 연결](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 항목의 자세한 단계를 따릅니다.
* 사용자 지정 데이터베이스 역할을 만들고 역할에 사용자를 추가 합니다. 그런 다음 역할에 세부적인 권한을 부여합니다. 자세한 내용은 [데이터베이스 엔진 권한 시작](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Visual Studio 및 기타 애플리케이션으로 쿼리하려면 [Visual Studio를 사용하여 쿼리](sql-data-warehouse-query-visual-studio.md)를 참조하세요.
