---
title: 데이터 웨어하우스 데이터 정렬 유형
description: Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)에 지원되는 데이터 정렬 유형
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: cc2f5f735f20e54baa22eea7ad4d9fd00a3bc426
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850112"
---
# <a name="database-collation-support-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)에 대한 데이터베이스 데이터 정렬 지원 

새 전용 SQL 풀(이전의 SQL DW)을 만들 때 Azure Portal에서 기본 데이터베이스 데이터 정렬을 변경할 수 있습니다. 이 기능을 사용하면 3800개의 지원 데이터베이스 데이터 정렬 중 하나를 사용하여 새 데이터베이스를 훨씬 쉽게 만들 수 있습니다.

데이터 정렬은 문자 기반 데이터 형식에 대한 로캘, 코드 페이지, 정렬 순서 및 문자 민감도 규칙을 제공합니다. 데이터 정렬 정보를 요구하는 모든 열과 식은 데이터베이스 설정에서 선택한 데이터 정렬을 상속합니다. 기본 상속은 문자 기반 데이터 형식에 대해 다른 데이터 정렬을 명시적으로 지정하여 재정의될 수 있습니다.

> [!NOTE]
> Azure Synapse Analytics 쿼리 텍스트(변수, 상수 등 포함)는 다른 SQL Server 제공과 같이 서버 수준 데이터 정렬이 아닌 데이터베이스 수준 데이터 정렬을 사용하여 항상 처리됩니다.

## <a name="changing-collation"></a>데이터 정렬 변경

기본 데이터 정렬을 변경하려면 프로비전 환경의 데이터 정렬 필드를 업데이트합니다.

예를 들어, 기본 데이터 정렬이 대/소문자를 구분하도록 변경하려면 데이터 정렬의 이름을 SQL_Latin1_General_CP1_CI_AS에서 SQL_Latin1_General_CP1_CS_AS로 변경하면 됩니다.

## <a name="list-of-unsupported-collation-types"></a>지원되지 않는 데이터 정렬 형식 목록

* Japanese_Bushu_Kakusu_140_BIN
* Japanese_Bushu_Kakusu_140_BIN2
* Japanese_Bushu_Kakusu_140_CI_AI_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI
* Japanese_Bushu_Kakusu_140_CI_AI_WS
* Japanese_Bushu_Kakusu_140_CI_AI_KS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CI_AS
* Japanese_Bushu_Kakusu_140_CI_AS_WS
* Japanese_Bushu_Kakusu_140_CI_AS_KS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AI
* Japanese_Bushu_Kakusu_140_CS_AI_WS
* Japanese_Bushu_Kakusu_140_CS_AI_KS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AS
* Japanese_Bushu_Kakusu_140_CS_AS_WS
* Japanese_Bushu_Kakusu_140_CS_AS_KS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
* Japanese_XJIS_140_BIN
* Japanese_XJIS_140_BIN2
* Japanese_XJIS_140_CI_AI_VSS
* Japanese_XJIS_140_CI_AI_WS_VSS
* Japanese_XJIS_140_CI_AI_KS_VSS
* Japanese_XJIS_140_CI_AI_KS_WS_VSS
* Japanese_XJIS_140_CI_AS_VSS
* Japanese_XJIS_140_CI_AS_WS_VSS
* Japanese_XJIS_140_CI_AS_KS_VSS
* Japanese_XJIS_140_CI_AS_KS_WS_VSS
* Japanese_XJIS_140_CS_AI_VSS
* Japanese_XJIS_140_CS_AI_WS_VSS
* Japanese_XJIS_140_CS_AI_KS_VSS
* Japanese_XJIS_140_CS_AI_KS_WS_VSS
* Japanese_XJIS_140_CS_AS_VSS
* Japanese_XJIS_140_CS_AS_WS_VSS
* Japanese_XJIS_140_CS_AS_KS_VSS
* Japanese_XJIS_140_CS_AS_KS_WS_VSS
* Japanese_XJIS_140_CI_AI
* Japanese_XJIS_140_CI_AI_WS
* Japanese_XJIS_140_CI_AI_KS
* Japanese_XJIS_140_CI_AI_KS_WS
* Japanese_XJIS_140_CI_AS
* Japanese_XJIS_140_CI_AS_WS
* Japanese_XJIS_140_CI_AS_KS
* Japanese_XJIS_140_CI_AS_KS_WS
* Japanese_XJIS_140_CS_AI
* Japanese_XJIS_140_CS_AI_WS
* Japanese_XJIS_140_CS_AI_KS
* Japanese_XJIS_140_CS_AI_KS_WS
* Japanese_XJIS_140_CS_AS
* Japanese_XJIS_140_CS_AS_WS
* Japanese_XJIS_140_CS_AS_KS
* Japanese_XJIS_140_CS_AS_KS_WS
* SQL_EBCDIC1141_CP1_CS_AS
* SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>현재 데이터 정렬 확인

데이터베이스의 현재 데이터 정렬을 확인하려면 다음 T-SQL 조각을 실행합니다.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```

'Collation'을 속성 매개 변수로 전달하는 경우 DatabasePropertyEx 함수는 지정된 데이터베이스에 대한 현재 데이터 정렬을 반환합니다. 자세한 내용은 [DatabasePropertyEx](/sql/t-sql/functions/databasepropertyex-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.
