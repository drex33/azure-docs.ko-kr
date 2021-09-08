---
title: 수백 테라바이트의 데이터를 Azure Cosmos DB로 마이그레이션
description: 이 문서에서는 수백 테라바이트의 데이터를 Cosmos DB로 마이그레이션하는 방법을 설명합니다.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: eed535af3b607eebf106900f07c7f312f2f932ab
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029896"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>수백 테라바이트의 데이터를 Azure Cosmos DB로 마이그레이션 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB에 테라바이트의 데이터를 저장할 수 있습니다. 대규모 데이터 마이그레이션을 수행하여 프로덕션 워크로드를 Azure Cosmos DB로 이동할 수 있습니다. 이 문서에서는 대규모 데이터를 Azure Cosmos DB로 이동하는 데 관련된 문제를 설명하고 문제를 해결하고 데이터를 Azure Cosmos DB로 마이그레이션하는 데 도움이 되는 도구를 소개합니다. 이 사례 연구에서 고객은 Cosmos DB SQL API를 사용했습니다.  

전체 워크로드를 Azure Cosmos DB로 마이그레이션하기 전에 데이터 하위 집합을 마이그레이션하여 파티션 키 선택, 쿼리 성능, 데이터 모델링과 같은 일부 측면의 유효성을 검사할 수 있습니다. 개념 증명의 유효성을 검사한 후 전체 워크로드를 Azure Cosmos DB로 이동할 수 있습니다.  

## <a name="tools-for-data-migration"></a>데이터 마이그레이션 도구 

Azure Cosmos DB 마이그레이션 전략은 현재 API 선택 및 데이터 크기에 따라 달라집니다. 데이터 모델링, 쿼리 성능, 파티션 키 선택 등의 유효성을 검사하기 위해 더 작은 데이터 세트를 마이그레이션하려면 [데이터 마이그레이션 도구](import-data.md) 또는 [Azure Data Factory의 Azure Cosmos DB 커넥터](../data-factory/connector-azure-cosmos-db.md)를 선택하면 됩니다. Spark에 익숙한 경우 [Azure Cosmos DB Spark 커넥터](./create-sql-api-spark.md)를 사용하여 데이터를 마이그레이션하도록 선택할 수도 있습니다.

## <a name="challenges-for-large-scale-migrations"></a>대규모 마이그레이션의 과제 

데이터를 Azure Cosmos DB로 마이그레이션하기 위한 기존 도구에는 특히 대규모에서 명확해지는 몇 가지 제한 사항이 있습니다.

 * **제한된 스케일 아웃 기능**: 테라바이트의 데이터를 최대한 빨리 Azure Cosmos DB로 마이그레이션하고 전체 프로비저닝된 처리량을 효과적으로 사용하려면 마이그레이션 클라이언트에 무기한으로 스케일 아웃하는 기능이 있어야 합니다.  

* **진행률 추적 및 검사점 설정 없음:** 큰 데이터 세트를 마이그레이션하는 동안 마이그레이션 진행률을 추적하고 검사점을 설정하는 것이 중요합니다. 그렇지 않으면 마이그레이션 중에 발생하는 모든 오류가 마이그레이션을 중지하고 프로세스를 처음부터 시작해야 합니다. 전체 마이그레이션 프로세스의 99%가 이미 완료된 경우 전체 마이그레이션 프로세스를 다시 시작하는 것은 생산적이지 않습니다.  

* **배달 못한 편지 큐 없음**: 큰 데이터 세트 내에 원본 데이터의 일부와 관련된 문제가 있을 수 있습니다. 또한 클라이언트 또는 네트워크에 일시적인 문제가 있을 수 있습니다. 해당 경우 중 하나로 인해 전체 마이그레이션이 실패하지 않아야 합니다. 대부분의 마이그레이션 도구에는 일시적인 문제를 방지하는 강력한 재시도 기능이 있지만 이것으로 항상 충분하지는 않습니다. 예를 들어, 원본 데이터 문서의 0.01% 미만이 2MB보다 크면 Azure Cosmos DB에서 문서 쓰기에 실패합니다. 이상적으로 마이그레이션 도구에서 이와 같은 ‘실패한’ 문서를 마이그레이션 후 처리할 수 있는 또 다른 배달 못한 편지 큐에 유지하는 것이 유용합니다. 

이 제한 사항 중 대부분은 Azure Data Factory, Azure Data Migration 서비스와 같은 도구에 대해 수정되고 있습니다. 

## <a name="custom-tool-with-bulk-executor-library"></a>대량 실행기 라이브러리가 있는 사용자 지정 도구 

위 섹션에서 설명한 과제는 여러 인스턴스에서 쉽게 스케일 아웃할 수 있는 사용자 지정 도구를 사용하여 해결할 수 있으며 일시적인 오류에 대한 복원력이 있습니다. 또한 사용자 지정 도구는 다양한 검사점에서 마이그레이션을 일시 중지하고 다시 시작할 수 있습니다. Azure Cosmos DB는 이미 해당 기능 중 일부를 통합하는 [대량 실행기 라이브러리](./bulk-executor-overview.md)를 제공합니다. 예를 들어, 대량 실행기 라이브러리에는 이미 일시적인 오류를 처리하는 기능이 있으며 단일 노드의 스레드를 스케일 아웃하여 노드당 약 50만 RU를 사용할 수 있습니다. 또한 대량 실행기 라이브러리는 원본 데이터 세트를 검사점 형태로 독립적으로 작동하는 마이크로 일괄 처리로 분할합니다.  

사용자 지정 도구는 대량 실행기 라이브러리를 사용하며 수집 프로세스 중에 오류를 추적하기 위해 여러 클라이언트에 걸쳐 스케일 아웃을 지원합니다. 이 도구를 사용하려면 여러 마이그레이션 작업자가 각 파일을 선택하고 Azure Cosmos DB로 수집할 수 있도록 원본 데이터를 ADLS(Azure Data Lake Storage)의 개별 파일로 분할해야 합니다. 사용자 지정 도구는 ADLS의 개별 원본 파일에 대한 마이그레이션 진행률에 관한 메타데이터를 저장하고 연결된 오류를 추적하는 별도의 컬렉션을 사용합니다.  

다음 이미지는 이 사용자 지정 도구를 사용하는 마이그레이션 프로세스를 설명합니다. 이 도구는 일련의 가상 머신에서 실행되고 있으며 각 가상 머신은 Azure Cosmos DB에서 추적 컬렉션을 쿼리하여 원본 데이터 파티션 중 하나에서 임대를 획득합니다. 이 작업이 완료되면 도구에서 원본 데이터 파티션을 읽고 대량 실행기 라이브러리를 사용하여 Azure Cosmos DB로 수집합니다. 다음으로, 추적 컬렉션이 업데이트되어 데이터 수집 진행률과 발생한 모든 오류를 기록합니다. 데이터 파티션이 처리된 후 도구는 사용 가능한 다음 원본 파티션에 대한 쿼리를 시도합니다. 모든 데이터가 마이그레이션될 때까지 다음 원본 파티션을 계속 처리합니다. 도구의 원본 코드는 [Azure Cosmos DB 대량 수집](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion) 리포지토리에서 사용할 수 있습니다.  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="마이그레이션 도구 설정" border="false":::
 

 

추적 컬렉션에는 다음 예제와 같이 문서가 포함됩니다. 원본 데이터의 각 파티션에 대해 하나씩 해당 문서가 표시됩니다.  각 문서에는 위치, 마이그레이션 상태 및 오류(있는 경우)와 같은 원본 데이터 파티션의 메타데이터가 포함됩니다.  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>데이터 마이그레이션을 위한 필수 조건 

데이터 마이그레이션을 시작하기 전에 고려해야 할 몇 가지 필수 조건이 있습니다.  

#### <a name="estimate-the-data-size"></a>데이터 크기 예측:  

원본 데이터 크기는 Azure Cosmos DB에서 데이터 크기에 정확히 매핑되지 않을 수 있습니다. 원본의 몇 가지 샘플 문서를 삽입하여 Azure Cosmos DB에서 데이터 크기를 확인할 수 있습니다. 샘플 문서 크기에 따라 Azure Cosmos DB 마이그레이션 후 작업에서 총 데이터 크기를 예측할 수 있습니다. 

예를 들어, Azure Cosmos DB에서 마이그레이션 후 각 문서가 약 1KB이고 원본 데이터 세트에 약 600억 개의 문서가 있는 경우 Azure Cosmos DB의 예상 크기는 60TB에 가깝습니다. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>충분한 RU를 사용하여 컨테이너 미리 만들기: 

Azure Cosmos DB는 스토리지를 자동으로 스케일 아웃하지만 가장 작은 컨테이너 크기에서 시작하는 것은 바람직하지 않습니다. 컨테이너가 작을수록 처리량 가용성이 낮아지므로 마이그레이션을 완료하는 데 훨씬 더 오래 걸릴 수 있습니다. 대신, 최종 데이터 크기(이전 단계에서 예측한 대로)로 컨테이너를 만들고 마이그레이션 워크로드가 프로비저닝된 처리량을 완전히 사용 중인지 확인하는 것이 유용합니다.  

이전 단계에서 데이터 크기는 약 60TB로 예측되었으므로 전체 데이터 세트를 수용하려면 240만 RU 이상의 컨테이너가 필요합니다.  

 

#### <a name="estimate-the-migration-speed"></a>마이그레이션 속도 예측: 

마이그레이션 워크로드가 전체 프로비저닝된 처리량을 사용할 수 있다고 가정하면 프로비저닝된 처리량을 통해 마이그레이션 속도를 예측할 수 있습니다. 이전 예제를 계속하면, Azure Cosmos DB SQL API 계정에 1KB 문서를 쓰는 데 5 RU가 필요합니다.  240만 RU는 초당 480,000개 문서(또는 480MB/초)의 전송을 허용합니다. 즉, 60TB의 전체 마이그레이션에는 125,000초 또는 약 34시간이 걸립니다.  

하루 내에 마이그레이션을 완료하려는 경우 프로비저닝된 처리량을 500만 RU로 늘려야 합니다. 

 

#### <a name="turn-off-the-indexing"></a>인덱싱 끄기:  

마이그레이션은 최대한 빠르게 완료해야 하므로 수집된 각 문서의 인덱스를 만드는 데 사용되는 시간과 RU를 최소화하는 것이 좋습니다.  Azure Cosmos DB는 모든 속성을 자동으로 인덱싱합니다. 선택된 몇 가지 조건으로 인덱싱을 최소화하거나 마이그레이션 과정에서 완전히 끄는 것이 좋습니다. 아래와 같이 indexingMode를 none으로 변경하여 컨테이너의 인덱싱 정책을 끌 수 있습니다.  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

마이그레이션이 완료된 후 인덱싱을 업데이트할 수 있습니다.  

## <a name="migration-process"></a>마이그레이션 프로세스 

필수 조건이 완료되면 다음 단계를 수행하여 데이터를 마이그레이션할 수 있습니다.  

1. 먼저 원본에서 Azure Blob Storage로 데이터를 가져옵니다. 마이그레이션 속도를 높이려면 개별 원본 파티션 간에 병렬 처리하는 것이 좋습니다. 마이그레이션을 시작하기 전에 원본 데이터 세트를 크기가 약 200MB인 파일로 분할해야 합니다.   

2. 대량 실행기 라이브러리는 단일 클라이언트 VM에서 500,000 RU를 사용하도록 스케일 업할 수 있습니다. 사용 가능한 처리량은 500만 RU이므로 Azure Cosmos 데이터베이스가 있는 동일한 지역에 10개의 Ubuntu 16.04 VM(Standard_D32_v3)을 프로비저닝해야 합니다. 마이그레이션 도구 및 해당 설정 파일을 사용하여 이 VM을 준비해야 합니다.  

3. 클라이언트 가상 머신 중 하나에서 큐 단계를 실행합니다. 이 단계에서는 ADLS 컨테이너를 검색하고 각 원본 데이터 세트의 파티션 파일에 대한 진행률 추적 문서를 만드는 추적 컬렉션을 만듭니다.  

4. 다음으로, 모든 클라이언트 VM에서 가져오기 단계를 실행합니다. 각 클라이언트는 원본 파티션의 소유권을 얻고 데이터를 Azure Cosmos DB로 수집할 수 있습니다. 작업이 완료되고 추적 컬렉션에서 상태가 업데이트되면 클라이언트는 추적 컬렉션에서 사용 가능한 다음 원본 파티션을 쿼리할 수 있습니다.  

5. 이 프로세스는 원본 파티션의 전체 세트가 처리될 때까지 계속됩니다. 모든 원본 파티션이 처리되면 동일한 추적 컬렉션의 오류 수정 모드에서 도구를 다시 실행해야 합니다. 이 단계는 오류로 인해 다시 처리해야 하는 원본 파티션을 식별하는 데 필요합니다.  

6. 관련 오류 중 일부는 원본 데이터의 잘못된 문서로 인해 발생할 수 있습니다. 이 오류를 식별하고 수정해야 합니다. 다음으로, 실패한 파티션에서 가져오기 단계를 다시 실행하여 파티션을 다시 가져와야 합니다. 

마이그레이션이 완료되면 Azure Cosmos DB의 문서 수가 원본 데이터베이스의 문서 수와 같은지 유효성을 검사할 수 있습니다. 이 예제에서 Azure Cosmos DB의 총 크기는 65테라바이트로 확인됩니다. 마이그레이션 후 인덱싱을 선택적으로 켤 수 있고 RU를 워크로드 작업에 필요한 수준으로 낮출 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [.NET](bulk-executor-dot-net.md) 및 [Java](bulk-executor-java.md)에서 대량 실행기 라이브러리를 사용하는 샘플 애플리케이션을 사용해 보며 자세히 알아봅니다. 
* 대량 실행기 라이브러리는 Cosmos DB Spark 커넥터에 통합됩니다. 자세한 내용은 [Azure Cosmos DB Spark 커넥터](./create-sql-api-spark.md) 문서를 참조하세요.  
* 대규모 마이그레이션에 관한 추가적인 도움이 필요하면 “일반 권고” 문제 유형 및 “대규모(TB 이상) 마이그레이션” 문제 하위 유형에서 지원 티켓을 열어 Azure Cosmos DB 제품 팀에 문의하세요.
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.