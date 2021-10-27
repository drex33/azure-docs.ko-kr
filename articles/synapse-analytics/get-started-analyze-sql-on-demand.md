---
title: '자습서: 서버리스 SQL 풀을 사용하여 데이터 분석 시작'
description: 이 자습서에서는 Spark 데이터베이스에 있는 데이터를 사용하여 서버리스 SQL 풀로 데이터를 분석하는 방법을 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 04/15/2021
ms.openlocfilehash: 492f8d42a072418586a6646a9beed2e750849b31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996989"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>서버리스 SQL 풀을 사용하여 데이터 분석

이 자습서에서는 서버리스 SQL 풀로 데이터를 분석하는 방법을 알아봅니다. 

## <a name="the-built-in-serverless-sql-pool"></a>기본 제공 서버리스 SQL 풀

서버리스 SQL 풀을 사용하면 용량을 예약하지 않고도 SQL을 사용할 수 있습니다. 서버리스 SQL 풀에 대한 요금 청구는 쿼리를 실행하는 데 사용된 노드 수가 아니라 쿼리를 실행하는 데 처리된 데이터 양을 기준으로 합니다.

모든 작업 영역에는 **기본 제공** 이라는 미리 구성된 서버리스 SQL 풀이 제공됩니다. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>서버리스 SQL 풀을 사용하여 NYC Taxi 데이터 분석
 
> [!NOTE]
> [샘플 데이터를 기본 스토리지 계정에 배치](get-started-create-workspace.md#place-sample-data-into-the-primary-storage-account)했는지 확인합니다.

1. Synapse Studio에서 **개발** 허브로 이동합니다.
1. 새 SQL 스크립트를 만듭니다.
1. 다음 코드를 스크립트에 붙여 넣습니다.

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. **실행** 을 클릭합니다. 

데이터 탐색은 데이터의 기본 특성을 이해할 수 있는 간단한 시나리오일 뿐입니다. 이 [자습서](sql/tutorial-data-analyst.md)에서 데이터 탐색 및 분석에 대해 자세히 알아보세요.

## <a name="create-data-exploration-database"></a>데이터 탐색 데이터베이스 만들기

`master` 데이터베이스를 통해 직접 파일의 내용을 찾아볼 수 있습니다 . 몇 가지 간단한 데이터 탐색 시나리오에서는 별도의 데이터베이스를 만들 필요가 없습니다.
그러나 데이터 탐색을 계속하면서 다음과 같은 몇 가지 유틸리티 개체를 만들 수 있습니다.
- 스토리지 계정에 대한 명명된 참조를 나타내는 외부 데이터 원본입니다.
- 외부 데이터 원본에 인증하는 방법을 지정할 수 있는 데이터베이스 범위 자격 증명입니다.
- 일부 데이터 원본 또는 데이터베이스 개체에 액세스할 권한이 있는 데이터베이스 사용자입니다.
- 쿼리에서 사용할 수 있는 유틸리티 뷰, 프로시저 및 함수입니다.

1. `master` 데이터베이스를 사용하여 사용자 지정 데이터베이스 개체에 대한 별도의 데이터베이스를 만듭니다. `master` 데이터베이스에는 사용자 지정 데이터베이스 개체를 만들 수 없습니다 .

    ```sql
    CREATE DATABASE DataExplorationDB 
                    COLLATE Latin1_General_100_BIN2_UTF8
    ```

   > [!IMPORTANT]
   > `_UTF8` 접미사를 포함하는 데이터 정렬을 사용하여 UTF-8 텍스트가 `VARCHAR` 열로 올바르게 변환되도록 합니다. `Latin1_General_100_BIN2_UTF8`은 Parquet 파일 및 Cosmos DB 컨테이너에서 데이터를 읽는 쿼리에 최상의 성능을 제공합니다.

2. 자격 증명 및 데이터 원본과 같은 유틸리티 개체를 만들 수 있는 `DataExplorationDB`로 전환합니다.

    ```sql
    CREATE EXTERNAL DATA SOURCE ContosoLake
    WITH ( LOCATION = 'https://contosolake.dfs.core.windows.net')
    ```

   > [!NOTE]
   > 외부 데이터 원본은 자격 증명 없이 만들 수 있습니다. 자격 증명이 없는 경우 호출자의 ID는 외부 데이터 원본에 액세스하는 데 사용됩니다.

3. 필요에 따라 'master' 데이터베이스를 사용하여 외부 데이터에 액세스하는 `DataExplorationDB`의 사용자에 대한 로그인을 만듭니다.

    ```sql
    CREATE LOGIN data_explorer WITH PASSWORD = 'My Very Strong Password 1234!';
    ```

    그런 다음, 로그인을 위해 `DataExplorationDB`에 데이터베이스 사용자를 만들고 `ADMINISTER DATABASE BULK OPERATIONS` 권한을 부여합니다.
    ```sql
    CREATE USER data_explorer FOR LOGIN data_explorer;
    GO
    GRANT ADMINISTER DATABASE BULK OPERATIONS TO data_explorer;
    GO
    ```

4. 상대 경로 및 데이터 원본을 사용하여 파일의 내용을 탐색합니다.

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK '/users/NYCTripSmall.parquet',
                DATA_SOURCE = 'ContosoLake',
                FORMAT='PARQUET'
        ) AS [result]
    ```

데이터 탐색 데이터베이스는 유틸리티 개체를 저장할 수 있는 간단한 자리 표시자일 뿐입니다. Synapse SQL 풀을 사용하면 훨씬 더 많은 작업을 수행하고, Azure 데이터 원본을 기반으로 구축된 관계형 계층인 Logical Data Warehouse를 만들 수 있습니다. 이 [자습서](sql/tutorial-data-analyst.md)에서 Logical Data Warehouse 구축에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [서버리스 Spark 풀을 사용하여 데이터 분석](get-started-analyze-spark.md)
