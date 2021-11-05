---
title: '빠른 시작: Spark를 사용하여 분석 시작'
description: 이 자습서에서는 Apache Spark를 사용하여 데이터를 분석하는 방법을 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: e0d430c393637ff11e7315716b3fdd9818a61b8b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467153"
---
# <a name="analyze-with-apache-spark"></a>Apache Spark를 사용하여 분석

이 자습서에서는 Azure Synapse용 Apache Spark를 사용하여 데이터를 로드하고 분석하는 기본 단계에 대해 알아봅니다.

## <a name="create-a-serverless-apache-spark-pool"></a>서버리스 Apache Spark 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **Apache Spark 풀** 을 차례로 선택합니다.
1. **새로 만들기** 를 선택합니다. 
1. **Apache Spark 풀 이름** 에 **Spark1** 을 입력합니다.
1. **노드 크기** 에 **Small** 을 입력합니다.
1. **노드 수** 의 경우 최솟값 및 최댓값을 각각 3으로 설정합니다.
1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. 몇 초 후에 Apache Spark 풀이 준비됩니다.

## <a name="understanding-serverless-apache-spark-pools"></a>서버리스 Apache Spark 풀 이해

서버리스 Spark 풀은 사용자가 Spark를 사용하는 방법을 표시하는 방식입니다. 풀 사용을 시작하면 필요한 경우 Spark 세션이 생성됩니다. 풀은 해당 세션에서 사용할 Spark 리소스의 수와 세션이 자동으로 일시 중지되기 전까지의 지속 시간을 제어합니다. 풀 자체가 아닌 해당 세션 중에 사용되는 Spark 리소스에 대한 비용을 지불합니다. 이러한 방식으로 Spark 풀을 사용하면 클러스터 관리를 걱정하지 않고도 Spark로 작업할 수 있습니다. 이는 서버리스 SQL 풀의 작동 방식과 유사합니다.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Spark 풀을 사용하여 NYC Taxi 데이터 분석

1. Synapse Studio에서 **개발** 허브로 이동합니다.
2. 새 Notebook 만들기
3. 새 코드 셀을 만들고, 다음 코드를 해당 셀에 입력합니다.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. 노트북의 **연결 대상** 메뉴에서 이전에 만든 **Spark1** 서버리스 Spark 풀을 선택합니다.
1. 셀에서 **실행** 을 선택합니다. Synapse는 필요한 경우 새 Spark 세션을 시작하여 이 셀을 실행합니다. 새 Spark 세션이 필요한 경우 처음에 생성하는 데 약 2초가 걸립니다. 
1. 데이터 프레임의 스키마만 확인하려면 다음 코드를 사용하여 셀을 실행합니다.

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Spark nyctaxi 데이터베이스에 NYC 택시 데이터 로드

데이터는 **df** 라는 데이터 프레임을 통해 사용할 수 있습니다. 이제 이를 **nyctaxi** 라는 Spark 데이터베이스에 로드합니다.

1. Notebook에 새 코드 셀을 추가하고 다음 코드를 입력합니다.

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark 및 Notebook을 사용하여 NYC 택시 데이터 분석

1. 새 코드 셀을 만들고, 다음 코드를 입력합니다. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 셀을 실행하여 **nyctaxi** Spark 데이터베이스에 로드한 NYC 택시 데이터를 표시합니다.
1. 새 코드 셀을 만들고, 다음 코드를 입력합니다. 이 데이터를 분석하고 결과를 **nyctaxi.passengercountstats** 라는 테이블에 저장합니다.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. 셀 결과에서 **차트** 를 선택하여 시각화된 데이터를 표시합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [전용 SQL 풀로 데이터 분석](get-started-analyze-sql-pool.md)
