---
title: '자습서: MongoDB를 Azure Cosmos DB API for MongoDB로 오프라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 오프라인에서 MongoDB 온-프레미스에서 Azure Cosmos DB API for MongoDB로 마이그레이션합니다.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 09/21/2021
ms.openlocfilehash: 6714446d42a256f6948b8e0f6e69b6bc2be60b6e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664355"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>자습서: 오프라인에서 MongoDB를 Azure Cosmos DB API for MongoDB로 마이그레이션
[!INCLUDE[appliesto-mongodb-api](../cosmos-db/includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 마이그레이션 단계를 수행하기 전에 이 전체 가이드를 읽어보세요.
>

이 MongoDB 마이그레이션 가이드는 MongoDB 마이그레이션에 대한 시리즈의 일부입니다. 중요한 MongoDB 마이그레이션 단계는 아래와 같이 [사전 마이그레이션](../cosmos-db/mongodb-pre-migration.md), 마이그레이션 및 [사후 마이그레이션](../cosmos-db/mongodb-post-migration.md)입니다.

![마이그레이션 단계 다이어그램.](../cosmos-db/mongodb/media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-offline-data-migration-from-mongodb-to-azure-cosmos-db-using-dms"></a>DMS를 사용하여 MongoDB에서 Azure Cosmos DB로 오프라인 데이터 마이그레이션 개요

Azure Database Migration Service를 사용하여 오프라인 일회성 데이터베이스 마이그레이션을 온-프레미스 또는 클라우드 MongoDB 인스턴스에서 Azure Cosmos DB API for MongoDB로 수행합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.
> [!div class="checklist"]
>
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

이 자습서에서는 Azure 가상 머신에서 호스팅되는 MongoDB의 데이터 세트를 마이그레이션합니다. Azure Database Migration Service를 사용하여 데이터 세트를 Azure Cosmos DB API for MongoDB로 마이그레이션합니다. MongoDB 원본이 아직 설정되지 않은 경우 [Azure에서 Windows VM에 MongoDB 설치 및 구성](/previous-versions/azure/virtual-machines/windows/install-mongodb)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 처리량 추정 및 파티션 키 선택과 같은 [사전 마이그레이션 단계를 완료](../cosmos-db/mongodb-pre-migration.md)합니다.
* [Azure Cosmos DB API for MongoDB에 대한 계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).

  > [!NOTE]
  > 서버리스 모드로 프로비전된 API for MongoDB 계정으로 마이그레이션하는 경우 현재 DMS가 지원되지 않습니다.

* Azure Resource Manager를 사용하여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. 이 배포 모델은 [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공합니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Virtual Network 설명서](../virtual-network/index.yml), 특히 단계별 세부 정보가 있는 "빠른 시작" 문서를 참조하세요.

    > [!NOTE]
    > 가상 네트워크 설정 중에 Microsoft에 대한 네트워크 피어링에서 ExpressRoute를 사용하는 경우 서비스가 프로비저닝되는 서브넷에 다음 서비스 [엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 추가합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트 또는 Azure Cosmos DB 엔드포인트)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크에 대한 NSG(네트워크 보안 그룹) 규칙이 53, 443, 445, 9354 및 10000~20000 통신 포트를 차단하지 않는지 확인합니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md)을 참조하세요.
* Azure Database Migration Service에서 기본적으로 27017 TCP 포트인 원본 MongoDB 서버에 액세스할 수 있도록 Windows 방화벽을 엽니다.
* 원본 데이터베이스 앞에서 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있도록 허용하는 방화벽 규칙을 추가해야 할 수 있습니다.

## <a name="configure-the-server-side-retry-feature"></a>서버 쪽 다시 시도 기능 구성

MongoDB에서 Azure Cosmos DB로 마이그레이션하는 경우 리소스 거버넌스 기능을 활용할 수 있습니다. 이러한 기능을 통해 프로비저닝된 RU(요청 단위)의 처리량을 최대한 활용할 수 있습니다. 특정 Database Migration Service 요청이 컨테이너 프로비저닝된 RU를 초과하는 경우 Azure Cosmos DB는 마이그레이션 과정에서 해당 요청을 제한할 수 있습니다. 그런 다음, 해당 요청을 다시 시도해야 합니다.

Database Migration Service는 다시 시도를 수행할 수 있습니다. Database Migration Service와 Azure Cosmos DB 간의 네트워크 홉과 관련된 왕복 시간이 해당 요청의 전체 응답 시간에 영향을 준다는 것을 이해하는 것이 중요합니다. 제한된 요청에 대한 응답 시간을 개선하면 마이그레이션에 필요한 총 시간을 단축할 수 있습니다.

Azure Cosmos DB의 서버 쪽 다시 시도 기능을 사용하면 서비스에서 제한 오류 코드를 가로채고 훨씬 더 짧은 왕복 시간으로 다시 시도하므로 요청 응답 시간을 크게 향상시킬 수 있습니다.

서버 쪽 다시 시도를 사용하려면 Azure Cosmos DB 포털에서 **기능** > **서버 쪽 다시 시도** 를 차례로 선택합니다.

![서버 쪽 다시 시도 기능을 찾을 수 있는 위치를 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

이 기능을 사용하도록 설정하려면 **사용** 을 선택합니다.

![서버 쪽 다시 시도를 사용하도록 설정하는 방법을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]  

## <a name="create-an-instance"></a>인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기** 를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service** 를 선택합니다.

    ![Azure Marketplace를 보여 주는 스크린샷.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기** 를 선택합니다.

    ![Azure Database Migration Service 인스턴스를 만드는 방법을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. **마이그레이션 서비스 만들기** 에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. Azure Database Migration Service 인스턴스를 만들 위치를 선택합니다. 

5. 기존 가상 네트워크를 선택하거나 새로 만듭니다.

    가상 네트워크는 원본 MongoDB 인스턴스 및 대상 Azure Cosmos DB 계정에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조하세요.

6. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스에 대한 구성 설정을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. **만들기** 를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 만들어지면 Azure Portal에서 해당 서비스를 찾아서 엽니다. 그런 다음, 새 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스** 를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services** 를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스를 찾는 방법을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색한 다음, 인스턴스를 선택합니다.

3. **+ 새 마이그레이션 프로젝트** 를 선택합니다.

4. **새 마이그레이션 프로젝트** 에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **MongoDB** 를 선택합니다. **대상 서버 유형** 텍스트 상자에서 **CosmosDB(MongoDB API)** 를 선택한 다음, **활동 유형 선택** 에 대해 **오프라인 데이터 마이그레이션** 을 선택합니다. 

    ![프로젝트 옵션을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. **작업 만들기 및 실행** 을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 세부 정보** 화면에서 원본 MongoDB 서버에 대한 연결 세부 정보를 지정합니다.

   > [!IMPORTANT]
   > Azure Database Migration Service는 Azure Cosmos DB를 원본으로 지원하지 않습니다.

    원본에 연결하는 세 가지 모드가 있습니다.
   * **표준 모드** - 정규화된 도메인 이름 또는 IP 주소, 포트 번호 및 연결 자격 증명을 수락합니다.
   * **연결 문자열 모드** - [연결 문자열 URI 형식](https://docs.mongodb.com/manual/reference/connection-string/)에서 설명한 대로 MongoDB 연결 문자열을 수락합니다.
   * **Azure Storage의 데이터** - Blob 컨테이너 SAS URL을 수락합니다. MongoDB [bsondump 도구](https://docs.mongodb.com/manual/reference/program/bsondump/)에서 생성된 BSON 덤프가 Blob 컨테이너에 있는 경우 **Blob이 BSON 덤프를 포함합니다** 를 선택합니다. JSON 파일이 컨테이너에 있는 경우 이 옵션을 선택하지 마세요.

     이 옵션을 선택하는 경우 스토리지 계정 연결 문자열이 다음 형식으로 표시되는지 확인합니다.

     ```
     https://blobnameurl/container?SASKEY
     ```

     이 Blob 컨테이너 SAS 연결 문자열은 Azure Storage Explorer에서 확인할 수 있습니다. 관련 컨테이너에 대한 SAS를 만들면 요청한 형식의 URL이 제공됩니다.
     
     또한 Azure Storage의 형식 덤프 정보에 따라 다음 사항에 유의하세요.

     * BSON 덤프의 경우 Blob 컨테이너 내의 데이터는 bsondump 형식이어야 합니다. *collection.bson* 형식으로 지정된 포함 데이터베이스의 이름을 딴 폴더에 데이터 파일을 배치합니다. 메타데이터 파일의 이름을 *collection.metadata.json* 형식을 사용하여 지정합니다.

     * JSON 덤프의 경우 Blob 컨테이너의 파일은 포함한 데이터베이스를 따라 이름이 지정된 폴더에 배치되어야 합니다. 각 데이터베이스 폴더 내에서 데이터 파일은 *data* 라는 하위 폴더에 배치하고 해당 이름은 *collection.json* 형식을 사용하여 지정해야 합니다. 모든 메타데이터 파일은 *metadata* 라는 하위 폴더에 배치하고, 해당 이름은 동일한 *collection.json* 형식을 사용하여 지정합니다. 메타데이터 파일은 MongoDB bsondump 도구에서 생성한 것과 동일한 형식이어야 합니다.

    > [!IMPORTANT]
    > MongoDB 서버에서 자체 서명된 인증서를 사용하지 않는 것이 좋습니다. 하나를 사용해야 하는 경우 연결 문자열 모드를 사용하여 서버에 연결하고 연결 문자열에 따옴표("")가 있는지 확인합니다.
    >
    >```
    >&sslVerifyCertificate=false
    >```

   또한 DNS 이름을 확인할 수 없는 경우에도 IP 주소를 사용할 수 있습니다.

   ![원본 세부 정보의 지정을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **저장** 을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **마이그레이션 대상 세부 정보** 화면에서 대상 Azure Cosmos DB 계정에 대한 연결 세부 정보를 지정합니다. 이 계정은 MongoDB 데이터를 마이그레이션하는 미리 프로비저닝된 Azure Cosmos DB API for MongoDB 계정입니다.

    ![대상 세부 정보의 지정을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **저장** 을 선택합니다.

## <a name="map-to-target-databases"></a>대상 데이터베이스에 매핑

1. **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    **만들기** 가 데이터베이스 이름 옆에 표시되는 경우 Azure Database Migration Service에서 대상 데이터베이스를 찾지 못했음을 나타내며, 서비스에서 데이터베이스를 만듭니다.

    마이그레이션의 이 시점에서 [처리량을 프로비전](../cosmos-db/set-throughput.md)할 수 있습니다. Azure Cosmos DB에서는 처리량을 데이터베이스 수준에서 프로비저닝하거나 컬렉션 수준에서 개별적으로 프로비저닝할 수 있습니다. 처리량은 [요청 단위](../cosmos-db/request-units.md)로 측정됩니다. [Azure Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)에 대해 자세히 알아봅니다.

    ![대상 데이터베이스에 대한 매핑을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **저장** 을 선택합니다.
3. **컬렉션 설정** 화면에서 컬렉션 목록을 확장한 다음, 마이그레이션할 컬렉션 목록을 검토합니다.

    Azure Database Migration Service는 대상 Azure Cosmos DB 계정에 없는 원본 MongoDB 인스턴스에 있는 모든 컬렉션을 자동으로 선택합니다. 이미 데이터가 포함된 컬렉션을 다시 마이그레이션하려면 이 창에서 컬렉션을 명시적으로 선택해야 합니다.

    컬렉션에서 사용할 RU의 수를 지정할 수 있습니다. Azure Database Migration Service는 컬렉션 크기에 따라 스마트 기본값을 제공합니다.

    > [!NOTE]
    > 데이터베이스 마이그레이션 및 수집을 병렬로 수행합니다. 필요한 경우 Azure Database Migration Service의 여러 인스턴스를 사용하여 실행 속도를 높일 수 있습니다.

    확장성을 최적화하기 위해 [Azure Cosmos DB에서 분할](../cosmos-db/partitioning-overview.md)을 활용하도록 분할된 데이터베이스 키를 지정할 수도 있습니다. [분할된 데이터베이스/파티션 키 선택에 대한 모범 사례](../cosmos-db/partitioning-overview.md#choose-partitionkey)를 검토합니다.

    ![컬렉션 테이블의 선택을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **저장** 을 선택합니다.

5. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

    ![마이그레이션 요약을 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

**마이그레이션 실행** 을 선택합니다. 마이그레이션 작업 창이 표시되고, 작업 상태는 **시작되지 않음** 입니다.

![작업 상태를 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

마이그레이션 작업 화면에서 **새로 고침** 을 선택하여 마이그레이션 상태가 **완료됨** 으로 표시될 때까지 디스플레이를 업데이트합니다.

> [!NOTE]
> 작업을 선택하여 데이터베이스 수준 및 컬렉션 수준 마이그레이션 메트릭의 세부 정보를 가져올 수 있습니다.

![완료된 작업 상태를 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터 확인

마이그레이션이 완료되면 Azure Cosmos DB 계정을 확인하여 모든 컬렉션이 성공적으로 마이그레이션되었는지 확인할 수 있습니다.

![Azure Cosmos DB 계정을 검사하여 모든 컬렉션이 성공적으로 마이그레이션되었는지 확인할 수 있는 위치를 보여 주는 스크린샷](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>마이그레이션 후 최적화

MongoDB 데이터베이스에 저장된 데이터가 Azure Cosmos DB API for MongoDB로 마이그레이션되면 Azure Cosmos DB에 연결하여 데이터를 관리할 수 있습니다. 다른 마이그레이션 후 최적화 단계를 수행할 수도 있습니다. 여기에는 인덱싱 정책 최적화, 기본 일관성 수준 업데이트 또는 Azure Cosmos DB 계정에 대한 전역 배포 구성이 포함될 수 있습니다. 자세한 내용은 [마이그레이션 후 최적화](../cosmos-db/mongodb-post-migration.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../cosmos-db/convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](../cosmos-db/mongodb/estimate-ru-capacity-planner.md)에 대해 읽어보세요.

## <a name="next-steps"></a>다음 단계

[Azure 데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)에서 추가 시나리오에 대한 마이그레이션 지침을 검토합니다.



