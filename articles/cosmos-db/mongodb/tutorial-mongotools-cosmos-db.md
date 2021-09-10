---
title: MongoDB 네이티브 도구를 사용하여 MongoDB를 Azure Cosmos DB API for MongoDB로 오프라인 마이그레이션
description: MongoDB 네이티브 도구를 사용하여 MongoDB 인스턴스에서 Azure Cosmos DB로 작은 데이터 세트를 마이그레이션하는 방법 알아보기
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: 335af910f9e62fe1bd67513f843966de3c5b381b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038321"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>자습서: MongoDB 네이티브 도구를 사용하여 MongoDB를 Azure Cosmos DB의 API for MongoDB로 오프라인 마이그레이션
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 마이그레이션 단계를 수행하기 전에 이 전체 가이드를 읽어보세요.
>

이 MongoDB 마이그레이션 가이드는 MongoDB 마이그레이션에 대한 시리즈의 일부입니다. 중요한 MongoDB 마이그레이션 단계는 아래와 같이 [사전 마이그레이션](pre-migration-steps.md), 마이그레이션 및 [사후 마이그레이션](post-migration-optimization.md)입니다.

![마이그레이션 단계 다이어그램.](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-data-migration-using-mongodb-native-tools"></a>MongoDB 네이티브 도구를 사용한 데이터 마이그레이션 개요

MongoDB 네이티브 도구를 사용하여 데이터베이스를 MongoDB의 온-프레미스 또는 클라우드 인스턴스에서 Azure Cosmos DB의 API for MongoDB로 오프라인(1회) 마이그레이션을 수행할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * 사용 사례에 적합한 MongoDB 네이티브 도구 선택
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.
> * 마이그레이션에 성공했는지 확인합니다.

이 자습서에서는 MongoDB 네이티브 도구를 사용하여 Azure Virtual Machine에서 호스트되는 MongoDB의 데이터 세트를 Azure Cosmos DB의 API for MongoDB로 마이그레이션합니다. MongoDB 네이티브 도구는 기존 MongoDB 인스턴스에서 데이터 조작을 용이하게 하는 이진 집합입니다. Azure Cosmos DB는 Mongo API를 노출하므로 MongoDB 네이티브 도구는 Azure Cosmos DB에 데이터를 삽입할 수 있습니다. 이 문서는 *mongoexport/mongoimport* 또는 *mongodump/mongorestore* 를 사용하여 MongoDB 인스턴스에서 데이터를 마이그레이션하는 데 중점을 둡니다. 네이티브 도구는 연결 문자열을 사용하여 MongoDB에 연결하기 때문에 어디에서나 도구를 실행할 수 있지만, MongoDB 인스턴스와 동일한 네트워크 내에서 이러한 도구를 실행하여 방화벽 문제를 방지하는 것이 좋습니다. 

MongoDB 네이티브 도구는 호스트 하드웨어가 허용하는 만큼 빠르게 데이터를 이동할 수 있습니다. 네이티브 도구는 총 마이그레이션 시간이 문제가 되지 않는 소규모 데이터 세트를 위한 가장 간단한 솔루션이 될 수 있습니다. [MongoDB Spark 커넥터](https://docs.mongodb.com/spark-connector/current/), [Azure DMS(Data Migration Service)](../../dms/tutorial-mongodb-cosmos-db.md) 또는 [ADF(Azure Data Factory)](../../data-factory/connector-azure-cosmos-db-mongodb-api.md)는 확장 가능한 마이그레이션 파이프라인이 필요한 경우 더 나은 대안이 될 수 있습니다.

MongoDB 원본을 설정하지 않은 경우 [Azure의 Windows VM에서 MongoDB 설치 및 구성](/previous-versions/azure/virtual-machines/windows/install-mongodb) 문서를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 처리량 추정, 파티션 키 선택 및 인덱싱 정책과 같은 [사전 마이그레이션 완료](pre-migration-steps.md) 단계.
* [Azure Cosmos DB API for MongoDB 계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* MongoDB 인스턴스에 로그인
    * [이 링크에서 MongoDB 네이티브 도구를 다운로드하여 설치합니다](https://www.mongodb.com/try/download/database-tools).
        * **MongoDB 네이티브 도구 버전이 기존 MongoDB 인스턴스와 일치하는지 확인합니다.**
        * MongoDB 인스턴스에 Azure Cosmos DB Mongo API와 다른 버전이 있는 경우 **MongoDB 네이티브 도구 버전을 모두 설치하고 각각 MongoDB 및 Azure Cosmos DB Mongo API에 적절한 도구 버전을 사용합니다.**
    * 이미 존재하지 않는 한 `readWrite` 권한이 있는 사용자를 추가합니다. 이 자습서의 뒷부분에서 이 사용자 이름/암호를 *mongoexport* 및 *mongodump* 도구에 제공합니다.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Azure Cosmos DB 서버 쪽 다시 시도 구성

MongoDB에서 Azure Cosmos DB로 마이그레이션하는 고객은 프로비저닝된 RU/s의 처리량을 완전히 활용할 수 있는 능력을 보장하는 리소스 거버넌스 기능의 혜택을 받습니다. Azure Cosmos DB는 해당 요청이 컨테이너 프로비저닝된 RU/s를 초과하는 경우 마이그레이션 과정에서 지정된 요청을 제한할 수 있습니다. 그런 다음, 해당 요청을 다시 시도해야 합니다. 마이그레이션 도구와 Azure Cosmos DB 간의 네트워크 홉과 관련된 왕복 시간은 해당 요청의 전체 응답 시간에 영향을 줍니다. 또한 MongoDB 네이티브 도구는 다시 시도를 처리하지 못할 수 있습니다. Azure Cosmos DB의 *서버 측 다시 시도* 기능을 통해 서비스는 스로틀 오류 코드를 가로채고 훨씬 낮은 왕복 시간으로 다시 시도할 수 있으므로 요청 응답 시간이 크게 단축됩니다. MongoDB 네이티브 도구의 관점에서 다시 시도를 처리하는 데 필요성이 최소화되므로 마이그레이션 시 환경에 긍정적인 영향을 미칩니다.

Azure Cosmos DB 포털의 *기능* 블레이드에서 서버 쪽 다시 시도 기능을 찾을 수 있습니다.

![MongoDB SSR 기능의 스크린샷.](../../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

*사용 안 함* 인 경우 아래와 같이 사용하도록 설정하는 것이 좋습니다.

![MongoDB SSR 사용의 스크린샷.](../../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>적절한 MongoDB 네이티브 도구 선택

![최적의 MongoDB 네이티브 도구 선택 다이어그램.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* 는 MongoDB 데이터베이스의 하위 집합을 마이그레이션하기 위한 최상의 마이그레이션 도구 쌍입니다.
    * *mongoexport* 는 기존 데이터를 사람이 읽을 수 있는 JSON 또는 CSV 파일로 내보냅니다. *mongoexport* 는 내보낼 기존 데이터의 하위 집합을 지정하는 인수를 사용합니다. 
    * *mongoimport* 는 JSON 또는 CSV 파일을 열고 콘텐츠를 대상 데이터베이스 인스턴스(이 경우 Azure Cosmus DB)에 삽입합니다. 
    * JSON 및 CSV는 압축 형식이 아닙니다. *mongoimport* 가 Azure Cosmos DB로 데이터를 보내면 초과 네트워크 요금이 발생할 수 있습니다.
* *mongodump/mongorestore* 는 전체 MongoDB 데이터베이스를 마이그레이션하기 위한 최상의 마이그레이션 도구 쌍입니다. 압축 BSON 형식은 데이터가 Azure Cosmos DB에 삽입될 때 네트워크 리소스를 보다 효율적으로 사용합니다.
    * *mongodump* 는 기존 데이터를 BSON 파일로 내보냅니다.
    * *mongorestore* 는 BSON 파일 덤프를 Azure Cosmos DB로 가져옵니다.
* 참고로 Azure Cosmos DB Mongo API로 가져오려는 JSON 파일이 작은 경우 *mongoimport* 도구는 데이터를 빠르게 수집하기 위한 솔루션입니다.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Azure Cosmos DB Mongo API 자격 증명 수집

Azure Cosmos DB Mongo API는 MongoDB 네이티브 도구가 사용할 수 있는 호환 가능한 액세스 자격 증명을 제공합니다. 데이터를 Azure Cosmos DB Mongo API로 마이그레이션하려면 이러한 액세스 자격 증명이 있어야 합니다. 이러한 자격 증명을 찾으려면 다음을 수행합니다.

1. Azure 포털 열기
1. Azure Cosmos DB Mongo API 계정으로 이동합니다.
1. 왼쪽 탐색 영역에서 *연결 문자열* 블레이드를 선택하면 아래와 유사한 화면이 표시됩니다.

    ![Azure Cosmos DB 자격 증명의 스크린샷.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *HOST* - Azure Cosmos DB 엔드포인트는 MongoDB 호스트 이름으로 작동합니다.
    * *PORT* - MongoDB 네이티브 도구가 Azure Cosmos DB에 연결할 때 이 포트를 명시적으로 지정해야 합니다.
    * *USERNAME* - Azure Cosmos DB 엔드포인트 도메인 이름의 접두사는 MongoDB 사용자 이름으로 작동합니다.
    * *PASSWORD* - Azure Cosmos DB 마스터 키는 MongoDB 암호로 작동합니다.
    * 또한 `true`인 *SSL* 필드에 유의합니다. MongoDB 네이티브 도구는 Azure Cosmos DB에 데이터를 쓸 때 SSL을 **사용해야** 합니다.

## <a name="perform-the-migration"></a>마이그레이션 수행

1. 마이그레이션할 데이터베이스 및 컬렉션을 선택합니다. 이 예에서는 *edx* 데이터베이스의 *쿼리* 컬렉션을 MongoDB에서 Azure Cosmos DB로 마이그레이션합니다.

이 섹션의 나머지 부분에서는 이전 섹션에서 선택한 도구 쌍을 사용하는 방법을 안내합니다.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. 소스 MongoDB 인스턴스에서 데이터를 내보내려면 MongoDB 인스턴스 머신에서 터미널을 엽니다. Linux 머신의 경우 다음을 입력합니다.

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Windows에서 실행 파일은 `mongoexport.exe`입니다. 기존 MongoDB 데이터베이스 인스턴스의 속성을 기반으로 하여 *HOST*, *PORT*, *USERNAME* 및 *PASSWORD* 를 입력해야 합니다. 
    
    MongoDB 데이터 세트의 하위 집합만 내보내도록 선택할 수도 있습니다. 이 작업을 수행하는 한 가지 방법은 추가 필터 인수를 추가하는 것입니다.
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    필터 `{"field1":"value1"}`과 일치하는 문서만 내보냅니다.

    호출을 실행하면 `edx.json` 파일이 생성된 것을 확인할 수 있습니다.

    ![mongoexport 호출의 스크린샷.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. 동일한 터미널을 사용하여 `edx.json`을 Azure Cosmos DB로 가져올 수 있습니다. Linux 머신에서 `mongoimport`를 실행하는 경우 다음을 입력합니다.

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Windows에서 실행 파일은 `mongoimport.exe`입니다. 이전에 수집한 Azure Cosmos DB 자격 증명을 기반으로 하여 *HOST*, *PORT*, *USERNAME* 및 *PASSWORD* 를 입력해야 합니다. 
1. *mongoimport* 에서 터미널 출력을 **모니터링** 합니다. 마이그레이션 상태의 업데이트를 포함하는 터미널에 텍스트 행을 출력하는 것을 볼 수 있습니다.

    ![mongoimport 호출의 스크린샷.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. 마지막으로, Azure Cosmos DB를 검토하여 마이그레이션이 성공했는지 **검사** 합니다. Azure Cosmos DB 포털을 열고 데이터 탐색기로 이동합니다. (1) *importedQuery* 컬렉션을 사용하는 *edx* 데이터베이스가 생성되고 (2) 데이터의 하위 집합만 내보낸 경우 *importedQuery* 는 원하는 데이터 하위 집합과 일치하는 문서 *만* 포함해야 합니다. 아래 예에서는 필터 `{"field1":"value1"}`과 일치하는 문서는 하나뿐입니다.

    ![Cosmos DB 데이터 확인의 스크린샷.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. MongoDB 인스턴스의 BSON 데이터 덤프를 만들려면 MongoDB 인스턴스 머신에서 터미널을 엽니다. Linux 머신의 경우 다음을 입력합니다.

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    기존 MongoDB 데이터베이스 인스턴스의 속성을 기반으로 하여 *HOST*, *PORT*, *USERNAME* 및 *PASSWORD* 를 입력해야 합니다. `edx-dump` 디렉터리가 생성되고 `edx-dump`의 디렉터리 구조가 소스 MongoDB 인스턴스의 리소스 계층 구조(데이터베이스 및 컬렉션 구조)를 재현하는지 확인해야 합니다. 각 컬렉션은 BSON 파일로 표시됩니다.

    ![mongodump 호출의 스크린샷.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. 동일한 터미널을 사용하여 `edx-dump`의 콘텐츠를 Azure Cosmos DB로 복원할 수 있습니다. Linux 머신에서 `mongorestore`를 실행하는 경우 다음을 입력합니다.

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Windows에서 실행 파일은 `mongorestore.exe`입니다. 이전에 수집한 Azure Cosmos DB 자격 증명을 기반으로 하여 *HOST*, *PORT*, *USERNAME* 및 *PASSWORD* 를 입력해야 합니다. 
1. *mongorestore* 에서 터미널 출력을 **모니터링** 합니다. 마이그레이션 상태를 업데이트하는 터미널에 행을 인쇄하는 것을 볼 수 있습니다.

    ![mongorestore 호출의 스크린샷.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. 마지막으로, Azure Cosmos DB를 검토하여 마이그레이션이 성공했는지 **검사** 합니다. Azure Cosmos DB 포털을 열고 데이터 탐색기로 이동합니다. (1) *importedQuery* 컬렉션이 있는 *edx* 데이터베이스가 생성되고 (2) *importedQuery* 에 원본 컬렉션의 *전체* 데이터 세트가 포함되어야 합니다.

    ![Cosmos DB mongorestore 데이터를 확인하는 스크린샷.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>마이그레이션 후 최적화

MongoDB 데이터베이스에 저장된 데이터를 MongoDB용 Azure Cosmos DB의 API로 마이그레이션한 후 Azure Cosmos DB에 연결하고 데이터를 관리할 수 있습니다. 인덱싱 정책 최적화와 같은 다른 마이그레이션 후 최적화 단계를 수행하거나, 기본 일관성 수준을 업데이트하거나, Azure Cosmos DB 계정에 대한 글로벌 배포를 구성할 수도 있습니다. 자세한 내용은 [마이그레이션 후 최적화](post-migration-optimization.md) 문서를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Cosmos DB 서비스 정보](https://azure.microsoft.com/services/cosmos-db/)
* [MongoDB 데이터베이스 도구 설명서](https://docs.mongodb.com/database-tools/)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)에서 추가 시나리오에 대한 마이그레이션 지침을 검토합니다.