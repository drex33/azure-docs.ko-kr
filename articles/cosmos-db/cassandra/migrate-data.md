---
title: Azure Cosmos DB의 Cassandra API 계정으로 데이터 마이그레이션 - 자습서
description: 이 자습서에서는 Apache Cassandra에서 Azure Cosmos DB의 Cassandra API 계정으로 데이터를 복사하는 방법을 알아봅니다.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 937a42d6ebdf3d2ccb87451a2db07df199655005
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586782"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>자습서: Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

개발자는 온-프레미스 또는 클라우드에서 실행되는 기존 Cassandra 워크로드를 가질 수 있으며 Azure로 마이그레이션할 수 있습니다. 이러한 워크로드를 Azure Cosmos DB의 Cassandra API 계정으로 마이그레이션할 수 있습니다. 이 자습서는 Apache Cassandra 데이터를 Azure Cosmos DB의 Cassandra API 계정으로 마이그레이션할 수 있는 다양한 옵션에 대한 지침을 제공합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 마이그레이션 계획
> * 마이그레이션을 위한 필수 조건
> * `cqlsh` `COPY` 명령을 사용하여 데이터 마이그레이션
> * Spark를 사용하여 데이터 마이그레이션

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites-for-migration"></a>마이그레이션을 위한 필수 조건

* **처리량 요구 사항 예측:** Azure Cosmos DB에서 데이터를 Cassandra API 계정으로 마이그레이션하려면 먼저 워크로드에 대한 처리량 요구 사항을 예측해야 합니다. 일반적으로 CRUD 작업에 필요한 평균 처리량부터 시작한 다음, 추출, 변환 및 로드 또는 급증하는 작업에 필요한 추가 처리량을 포함하는 것이 좋습니다. 마이그레이션을 계획하려면 다음과 같은 세부 정보가 필요합니다. 

  * **기존 또는 예상 데이터 크기:** 최소 데이터베이스 크기 및 처리량 요구 사항을 정의합니다. 새 애플리케이션의 데이터 크기를 예측할 경우 데이터가 행 전체에 균일하게 분포되어 있다고 가정하고 데이터 크기를 곱하여 값을 추정하면 됩니다. 

  * **필요한 처리량:** 대략적인 읽기(쿼리/가져오기) 및 쓰기(업데이트/삭제/삽입) 작업의 처리량 속도. 이 값은 안정적인 상태 데이터 크기와 함께 필요한 요청 단위를 컴퓨팅하는 데 필요합니다.  

  * **스키마:** `cqlsh`를 통해 기존 Cassandra 클러스터에 연결하고 Cassandra의 스키마를 내보냅니다. 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    기존 워크로드의 요구 사항을 확인한 후에는 수집한 처리량 요구 사항에 따라 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다.  

  * **작업에 대한 RU 요금 결정:** Cassandra API에서 지원되는 SDK 중 하나를 사용하여 RU를 결정할 수 있습니다. 다음 예제는 RU 비용 가져오기의 .NET 버전을 보여 줍니다.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **필요한 처리량 할당:** 요구 사항이 증가함에 따라 Azure Cosmos DB에서 스토리지 및 처리량의 크기를 자동으로 조정할 수 있습니다. [Azure Cosmos DB 요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 사용하여 처리량 수요를 예측할 수 있습니다. 

* **Cassandra API 계정에서 테이블 만들기:** 데이터 마이그레이션을 시작하기 전에 Azure Portal 또는 `cqlsh`에서 모든 테이블을 미리 만듭니다. 데이터베이스 수준 처리량이 있는 Azure Cosmos DB 계정으로 마이그레이션하는 경우에는 컨테이너를 만들 때 파티션 키를 제공해야 합니다.

* **처리량 증가:** 데이터 마이그레이션 기간은 Azure Cosmos DB의 테이블에 대해 프로비전하는 처리량에 따라 달라집니다. 마이그레이션 기간에 대한 처리량을 늘립니다. 처리량이 높을수록 속도 제한을 피하고 마이그레이션 시간을 단축할 수 있습니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요. 또한 Azure Cosmos DB 계정은 원본 데이터베이스와 같은 지역에 두는 것이 좋습니다. 

* **TLS 사용:** Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 있습니다. 계정과 상호 작용하는 경우 TLS를 사용해야 합니다. SSH와 함께 CQL을 사용하는 경우 TLS 정보를 제공할 수 있는 옵션이 있습니다.

## <a name="options-to-migrate-data"></a>데이터 마이그레이션 옵션

`cqlsh` `COPY` 명령을 사용하거나 Spark를 사용하여 기존 Cassandra 워크로드에서 Azure Cosmos DB로 데이터를 이동할 수 있습니다. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>cqlsh COPY 명령을 사용한 데이터 마이그레이션

[CQL COPY 명령](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html#cqlshrc)을 사용하여 로컬 데이터를 Azure Cosmos DB의 Cassandra API 계정으로 복사합니다.

1. csv 파일에 올바른 파일 구조가 포함되어 있는지 확인하려면 `COPY TO` 명령을 사용하여 데이터를 원본 Cassandra 테이블에서 csv 파일로 직접 내보냅니다(cqlsh가 적절한 자격 증명을 사용하여 원본 테이블에 연결되어 있는지 확인).

   ```bash
   COPY exampleks.tablename TO 'data.csv' WITH HEADER = TRUE;   
   ```

1. 이제 Cassandra API 계정의 연결 문자열 정보를 가져옵니다.

   * [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Cosmos DB 계정으로 이동합니다.

   * **연결 문자열** 창을 엽니다. `cqlsh`에서 Cassandra API 계정에 연결하는 데 필요한 모든 정보가 표시됩니다.

1. 포털의 연결 정보를 사용하여 `cqlsh`에 로그인합니다.

1. `CQL` `COPY FROM` 명령을 사용하여 `data.csv`를 복사합니다(`cqlsh`가 설치된 사용자 루트 디렉터리에 계속 있음).

   ```bash
   COPY exampleks.tablename FROM 'data.csv' WITH HEADER = TRUE;
   ```



### <a name="migrate-data-by-using-spark"></a>Spark를 사용하여 데이터 마이그레이션 

다음 단계를 따라 Spark를 사용하여 데이터를 Cassandra API 계정으로 마이그레이션합니다.

1. [Azure Databricks 클러스터](spark-databricks.md) 또는 [Azure HDInsight 클러스터](spark-hdinsight.md)를 프로비저닝합니다. 

1. 데이터를 대상 Cassandra API 엔드포인트로 이동합니다. Azure Databricks를 사용한 마이그레이션은 이 [방법 가이드](migrate-data-databricks.md)를 참조하세요.

Azure 가상 머신이나 다른 클라우드의 기존 클러스터에 데이터가 있는 경우 Spark 작업을 사용하여 데이터를 마이그레이션하는 것이 좋습니다. 이렇게 하려면 Spark를 일회성 또는 정기 수집을 위한 중개자로 설정해야 합니다. 온-프레미스 환경과 Azure 사이에 Azure ExpressRoute 연결을 사용하면 이 마이그레이션 속도를 높일 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 리소스 그룹, Azure Cosmos DB 계정 및 모든 관련 리소스를 삭제할 수 있습니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고, **삭제** 를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB의 Cassandra API 계정으로 데이터를 마이그레이션하는 방법을 알아보았습니다. 이제 Azure Cosmos DB의 다른 개념에 대해 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준](../consistency-levels.md)
