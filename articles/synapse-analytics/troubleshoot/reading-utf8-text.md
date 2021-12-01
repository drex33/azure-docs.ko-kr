---
title: '문제 해결: 서버리스 SQL 풀을 사용하여 CSV 또는 PARQUET 파일에서 UTF-8 텍스트 읽기'
description: Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 CSV 또는 PARQUET 파일에서 UTF-8 텍스트 읽기
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: troubleshooting
ms.date: 12/03/2020
ms.openlocfilehash: 0fea45d3001700420fbc7bece7168c5ad9048a3b
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361151"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 CSV 또는 Parquet 파일에서 UTF-8 텍스트 읽기 문제 해결

이 문서에서는 Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 CSV 또는 Parquet 파일에서 UTF-8 텍스트를 읽기 위한 문제 해결 단계를 제공합니다.

서버리스 SQL 풀을 사용하여 CSV 또는 PARQUET 파일에서 UTF-8 텍스트를 읽으면 쿼리에서 UTF8이 아닌 데이터 정렬을 사용하여 VARCHAR 열을 반환하는 경우에는 ü 및 ö와 같은 일부 특수 문자가 올바르게 변환되지 않습니다. 이는 SQL Server 및 Azure SQL의 알려진 문제입니다. UTF8이 아닌 데이터 정렬은 Synapse SQL의 기본값이므로 고객 쿼리에 영향을 줍니다. 표준 영어 문자를 사용하고 확장된 라틴 문자의 일부 하위 집합을 사용하는 고객은 변환 오류를 확인할 수 없습니다. 잘못된 변환은 [서버리스 SQL 풀에서 항상 UTF-8 데이터 정렬을 사용하여 UTF-8 텍스트 읽기](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)에 자세히 설명되어 있습니다.

## <a name="workaround"></a>해결 방법

이 문제에 대한 해결 방법은 CSV 또는 PARQUET 파일에서 UTF-8 텍스트를 읽을 때 항상 UTF-8 데이터 정렬을 사용하는 것입니다.

- 대부분의 경우, 데이터베이스에서 UTF8 데이터 정렬만 설정하면 됩니다(메타데이터 작업).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- OPENROWSET 또는 외부 테이블의 VARCHAR 열에 대한 데이터 정렬을 명시적으로 정의할 수 있습니다.

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- UTF8 데이터를 읽는 외부 테이블에 UTF8 데이터 정렬을 지정하지 않은 경우 영향을 받는 외부 테이블을 다시 만들고 VARCHAR 열에 UTF8 데이터 정렬을 설정해야 합니다(메타데이터 작업).


## <a name="next-steps"></a>다음 단계

* [Synapse SQL로 Parquet 파일 쿼리](../sql/query-parquet-files.md)
* [Synapse SQL로 CSV 파일 쿼리](../sql/query-single-csv-file.md)
* [Synapse SQL의 CETAS](../sql/develop-tables-cetas.md)
* [빠른 시작: 서버리스 SQL 풀 사용](../quickstart-sql-on-demand.md)
