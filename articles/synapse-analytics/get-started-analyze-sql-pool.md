---
title: '자습서: 전용 SQL 풀을 사용하여 데이터 분석 시작'
description: 이 자습서에서는 NYC Taxi 샘플 데이터를 사용하여 SQL 풀의 분석 기능을 탐색합니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick, wiassaf
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 09/29/2021
ms.openlocfilehash: cf1aabdef95a58c4bdd5a14e3714da37295ed790
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387737"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>전용 SQL 풀을 사용하여 데이터 분석

이 자습서에서는 NYC Taxi 데이터를 사용하여 전용 SQL 풀의 기능을 탐색합니다.

## <a name="create-a-dedicated-sql-pool"></a>전용 SQL 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **분석 풀** 아래에 있는 **관리** > **SQL 풀** 을 차례로 선택합니다.
1. **새로 만들기** 를 선택합니다.
1. **전용 SQL 풀 이름** 에 대해 **SQLPOOL1** 을 선택합니다.
1. **성능 수준** 의 경우 **DW100C** 를 선택합니다.
1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. 몇 분 안에 전용 SQL 풀이 준비됩니다. 

전용 SQL 풀이 **SQLPOOL1** 이라고도 하는 SQL 데이터베이스와 연결됩니다.
1. **데이터** > **작업 영역** 으로 이동합니다.
1. **SQLPOOL1** 이라는 데이터베이스가 표시되어야 합니다. 표시되지 않으면 **새로 고침** 을 클릭합니다.

전용 SQL 풀은 활성 상태인 동안 청구 가능한 리소스를 사용합니다. 비용을 줄이기 위해 풀을 일시 중지할 수 있습니다.

> [!NOTE] 
> 작업 영역에서 새 전용 SQL 풀(이전의 SQL DW)을 만들 때 전용 SQL 풀 프로비저닝 페이지가 열립니다. 프로비저닝은 논리 SQL 서버에서 수행됩니다.

## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>NYC Taxi 데이터를 SQLPOOL1에 로드

1. Synapse Studio에서 **개발** 허브로 이동하여 **+** 단추를 클릭해 새 리소스를 추가한 다음, 새 SQL 스크립트를 만듭니다.
1. 스크립트 위의 **연결 대상** 드롭다운 목록에서 'SQLPOOL1' 풀(이 자습서의 [1단계](./get-started-create-workspace.md)에서 만든 풀)을 선택합니다.
1. 다음 코드를 입력합니다.

    ```sql
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. **실행** 단추를 클릭하여 스크립트를 실행합니다.
1. 이 스크립트는 60초 이내에 완료됩니다. 2백만 행의 NYC Taxi 데이터를 `dbo.NYCTaxiTripSmall`이라는 테이블에 로드합니다.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>전용 SQL 풀에서 NYC Taxi 데이터 검색

1. Synapse Studio에서 **데이터** 허브로 이동합니다.
1. **SQLPOOL1** > **테이블** 로 이동합니다. 
3. **dbo.NYCTaxiTripSmall** 테이블을 마우스 오른쪽 단추로 클릭하고 **새 SQL 스크립트** > **상위 100개 행 선택** 을 차례로 선택합니다.
4. 새 SQL 스크립트가 만들어져 실행될 때까지 기다립니다.
5. SQL 스크립트의 위쪽에서 **연결 대상** 이 자동으로 **SQLPOOL1** 이라는 SQL 풀로 설정됩니다.
6. SQL 스크립트의 텍스트를 다음 코드로 바꾸고 실행합니다.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    이 쿼리에서는 총 주행 거리 및 평균 주행 거리와 승객 수 간의 관계를 보여 줍니다.
1. SQL 스크립트 결과 창에서 결과를 꺾은선형 차트로 시각화하려면 **보기** 를 **차트** 로 변경합니다.
    
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Storage 계정의 데이터 분석](get-started-analyze-storage.md)
