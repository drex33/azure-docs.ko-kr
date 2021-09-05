---
title: Azure Cosmos DB 리소스 모델
description: 이 문서에서는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 항목을 포함하는 Azure Cosmos DB 리소스 모델에 대해 설명합니다. 또한 Azure Cosmos 계정에서 이러한 요소의 계층 구조를 다룹니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.reviewer: sngun
ms.openlocfilehash: 55ec61dc83aa9821433cd3a53230d9e5c5d545ec
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664912"
---
# <a name="azure-cosmos-db-resource-model"></a>Azure Cosmos DB 리소스 모델
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 완전 관리형 PaaS(platform-as-a-service)입니다. Azure Cosmos DB 사용을 시작하려면 먼저 필요한 구독의 Azure 리소스 그룹에 Azure Cosmos 계정을 만든 다음 그 아래에 데이터베이스, 컨테이너, 항목을 만들어야 합니다. 이 문서에서는 리소스 모델 계층 구조의 Azure Cosmos DB 리소스 모델 및 다른 엔터티에 대해 설명합니다.

Azure Cosmos 계정은 글로벌 배포와 고가용성을 위한 기본 단위입니다. Azure Cosmos 계정에는 고유한 DNS 이름이 포함되어 있으며 Azure Portal 또는 Azure CLI를 사용하거나 다른 언어별 SDK를 사용하여 계정을 관리할 수 있습니다. 자세한 내용은 [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)을 참조하세요. 여러 Azure 지역에 데이터 및 처리량을 전역적으로 배포하기 위해 언제든지 계정에 Azure 지역을 추가하고 제거할 수 있습니다. 단일 지역 또는 여러 쓰기 지역을 갖도록 계정을 구성할 수 있습니다. 자세한 정보는 [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)을 참조하세요. 계정에서 [기본 일관성](consistency-levels.md) 수준을 구성할 수 있습니다.

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos 계정의 요소

Azure Cosmos 컨테이너는 확장성의 기본 단위입니다. 하나의 컨테이너는 무제한으로 프로비전된 처리량(RU/s)과 스토리지를 무제한으로 가질 수 있습니다. Azure Cosmos DB는 사용자가 지정한 논리 파티션을 사용하여 컨테이너를 투명하게 분할함으로써 프로비전된 처리량과 스토리지를 탄력적으로 확장합니다.

현재 Azure 구독에서 최대 50개의 Azure Cosmos 계정을 만들 수 있습니다(지원 요청을 통해 늘릴 수 있는 엄격하지 않은 제한). 단일 Azure Cosmos 계정은 무제한의 데이터와 프로비전된 처리량을 가상으로 관리할 수 있습니다. 계정에서 하나 이상의 Azure Cosmos 데이터베이스를 만들고 이 데이터베이스 내에서 하나 이상의 컨테이너를 만들어서 데이터와 프로비전된 처리량을 관리할 수 있습니다. 다음 그림은 Azure Cosmos 계정의 요소 계층 구조를 보여 줍니다.

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Azure Cosmos 계정의 계층 구조" border="false":::

Azure 구독에 계정을 생성한 후 데이터베이스, 컨테이너 및 항목을 만들어 계정의 데이터를 관리할 수 있습니다. 

다음 이미지는 Azure Cosmos DB 계정에서 다른 엔터티의 계층 구조를 보여줍니다.

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos 계정 엔터티" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos 데이터베이스

계정에 하나 이상의 Azure Cosmos 데이터베이스를 만들 수 있습니다. 데이터베이스는 네임스페이스와 유사합니다. 데이터베이스는 Azure Cosmos 컨테이너 집합에 대한 관리 단위입니다. 다음 테이블에서는 데이터베이스가 다양한 API 관련 엔터티에 매핑되는 방법을 보여줍니다.

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 데이터베이스 | 데이터베이스 | Keyspace | 데이터베이스 | 데이터베이스 | 해당 없음 |

> [!NOTE]
> Table API 계정을 사용하면 첫 테이블을 만들 때 기본 데이터베이스가 Azure Cosmos 계정 내에 자동으로 생성됩니다.

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos 데이터베이스에서 작업

다음 테이블에 설명된 대로 Azure Cosmos API를 통해 Azure Cosmos 데이터베이스와 상호 작용할 수 있습니다.

| 작업 | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
|모든 데이터베이스 열거| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|데이터베이스 읽기| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|새 데이터베이스 만들기| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|데이터베이스 업데이트| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |

## <a name="azure-cosmos-containers"></a>Azure Cosmos 컨테이너

Azure Cosmos 컨테이너는 프로비전된 처리량과 항목의 스토리지 양쪽 모두에 대한 확장성 단위입니다. 컨테이너는 가로로 분할된 다음, 여러 지역에 걸쳐 복제됩니다. 컨테이너에 추가하는 항목은 자동으로 논리 파티션으로 그룹화되며 파티션 키에 따라 실제 파티션에 분산됩니다. 컨테이너의 처리량은 실제 파티션에 균등하게 분산됩니다. 분할 및 파티션 키에 대한 자세한 내용은 [파티션 데이터](partitioning-overview.md)를 참조하세요. 

컨테이너를 만들 때 다음 모드 중 하나로 처리량을 구성합니다.

* **전용 프로비저닝 처리량 모드**: 컨테이너에 프로비저닝된 처리량은 해당 컨테이너에 대해서만 예약되며 SLA에서 지원됩니다. 자세한 내용은 [컨테이너에서 처리량을 프로비저닝하는 방법](how-to-provision-container-throughput.md)을 참조하세요.

* **프로비저닝된 처리량 공유** 모드: 이러한 컨테이너는 프로비저닝된 처리량을 동일한 데이터베이스의 기타 컨테이너와 공유합니다(전용 프로비저닝 처리량으로 구성된 컨테이너 제외). 즉, 데이터베이스에서 프로비저닝된 처리량은 모든 "공유 처리량" 컨테이너 간에 공유됩니다. 자세히 알아보려면 [데이터베이스에서 처리량을 프로비저닝하는 방법](how-to-provision-database-throughput.md)을 참조하세요.

> [!NOTE]
> 데이터베이스 및 컨테이너를 만들 때만 공유 및 전용 처리량을 구성할 수 있습니다. 컨테이너를 만든 후 전용 처리량 모드에서 공유 처리량 모드로 전환하거나 그 반대로 전환하려면 새 컨테이너를 만들고 데이터를 새 컨테이너로 마이그레이션해야 합니다. Azure Cosmos DB 변경 피드 기능을 사용하여 데이터를 마이그레이션할 수 있습니다.

전용 또는 공유 프로비저닝된 처리량 모드를 사용하여 컨테이너를 만드는지 여부에 관계 없이 Azure Cosmos 컨테이너는 탄력적으로 확장할 수 있습니다.

컨테이너는 스키마와 관계 없는 항목 컨테이너입니다. 컨테이너의 항목에는 임의의 스키마가 있을 수 있습니다. 예를 들어 사람을 나타내는 항목과 자동차를 나타내는 항목은 *동일한 컨테이너* 에 배치할 수 있습니다. 기본값으로 컨테이너에 추가하는 모든 항목은 명시적 인덱스 또는 스키마 관리를 요구하지 않고 자동으로 인덱싱됩니다. 컨테이너에 [인덱싱 정책](index-overview.md)을 구성하여 인덱싱 동작을 사용자 지정할 수 있습니다. 

컨테이너에서 선택한 항목에 [TTL(Time To Live)](time-to-live.md)을 설정하거나 전체 컨테이너를 설정하여 시스템에서 해당 항목을 정상적으로 제거할 수 있습니다. Azure Cosmos DB가 만료되면 항목이 자동으로 삭제됩니다. 또한 컨테이너에 수행되는 쿼리가 고정 바운드 내에서 만료된 항목을 반환하지 않도록 보장합니다. 자세한 내용은 [컨테이너에 TTL을 구성하는 방법](how-to-time-to-live.md)을 참조하세요.

컨테이너의 각 논리 파티션에 대해 관리되는 작업 로그를 [변경 피드](change-feed.md)를 통해 구독할 수 있습니다. 변경 피드는 항목의 이전 및 이후 이미지와 함께 컨테이너에서 수행되는 모든 업데이트의 로그를 제공합니다. 자세한 정보는 [변경 피드를 사용하여 사후 애플리케이션 빌드](serverless-computing-database.md)를 참조하세요. 컨테이너에서 변경 피드 정책을 사용하여 변경 피드에 대한 보존 기간을 구성할 수도 있습니다.

[저장 프로시저, 트리거, UDF(사용자 정의 함수)](stored-procedures-triggers-udfs.md), 그리고 컨테이너에 대한 [병합 프로시저](how-to-manage-conflicts.md)를 등록할 수 있습니다.

Azure Cosmos 컨테이너에 [고유 키 제약 조건](unique-keys.md)을 지정할 수 있습니다. 고유 키 정책을 만들면 논리 파티션 키당 하나 이상의 값의 고유성을 보장합니다. 고유 키 정책을 사용하여 컨테이너를 만드는 경우 고유 제약 조건에 지정된 값과 중복되는 값이 있는 새 항목 또는 업데이트된 항목을 만들 수 없습니다. 자세한 내용은 [고유 키 제약 조건](unique-keys.md)을 참조하세요.

컨테이너는 다음 테이블에 나와 있는 것처럼 API 관련 엔터티로 특수화됩니다.

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 컨테이너 | 컨테이너 | 테이블 | 컬렉션 | 그래프 | 테이블 |

> [!NOTE]
> 컨테이너를 만들 때 이름은 같지만 대/소문자가 다른 두 개의 컨테이너를 만들지 않도록 해야 합니다. Azure 플랫폼의 일부 부분에서는 대/소문자를 구분하지 않으므로 이로 인해 이러한 이름을 가진 컨테이너에 대한 원격 분석 및 작업의 혼동/충돌이 발생할 수 있습니다.

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너의 속성

Azure Cosmos 컨테이너에는 시스템 정의 속성 집합이 있습니다. 사용하는 API에 따라 일부 속성은 직접 노출되지 않을 수도 있습니다. 다음 테이블에서는 지원되는 시스템 정의 속성의 목록을 설명합니다.

| 시스템 정의 속성 | 시스템 생성 또는 사용자 구성 가능 | 목적 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | 시스템 생성 | 컨테이너의 고유 식별자 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_etag | 시스템 생성 | 낙관적 동시성 제어에 사용되는 엔터티 태그 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_ts | 시스템 생성 | 컨테이너의 마지막 업데이트 타임스탬프 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_self | 시스템 생성 | 컨테이너의 주소 지정 가능 URI | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|id | 사용자 구성 가능 | 컨테이너의 사용자 정의 고유 이름 | 예 | 예 | 예 | 예 | 예 |
|indexingPolicy | 사용자 구성 가능 | 인덱스 경로, 인덱스 유형 및 인덱스 모드를 변경하는 기능을 제공합니다 | 예 | 아니요 | 아니요 | 아니요 | 예 |
|timeToLive | 사용자 구성 가능 | 일정 기간이 지난 후 컨테이너에서 항목을 자동으로 삭제하는 기능을 제공합니다. 세부 정보는 [TTL(Time to Live)](time-to-live.md)를 참조하세요. | 예 | 아니요 | 아니요 | 아니요 | 예 |
|changeFeedPolicy | 사용자 구성 가능 | 컨테이너의 항목에 대한 변경 내용을 읽는 데 사용됩니다. 세부 정보는 [변경 피드](change-feed.md)를 참조하세요. | 예 | 아니요 | 아니요 | 아니요 | 예 |
|uniqueKeyPolicy | 사용자 구성 가능 | 논리 파티션에서 하나 이상의 값의 고유성을 보장하는 데 사용됩니다. 자세한 정보는 [고유 키 제약 조건](unique-keys.md)을 참조하세요. | 예 | 아니요 | 아니요 | 아니요 | 예 |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너에서 작업

Azure Cosmos API를 사용하는 경우 Azure Cosmos 컨테이너는 다음 작업을 지원합니다.

| 작업 | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 데이터베이스에 컨테이너 열거 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 읽기 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 새 컨테이너 만들기 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 업데이트 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 삭제 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |

## <a name="azure-cosmos-items"></a>Azure Cosmos 항목

사용하는 API에 따라 Azure Cosmos 항목은 컬렉션의 문서, 테이블의 행 또는 그래프의 노드나 에지를 나타낼 수 있습니다. 다음 테이블은 Azure Cosmos 항목에 대한 API 관련 엔터티 간의 매핑을 보여줍니다.

| Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 항목 | 항목 | 행 | 문서 | 노드 또는 에지 | 항목 |

### <a name="properties-of-an-item"></a>항목의 속성

모든 Azure Cosmos 항목에는 다음과 같은 시스템 정의 속성이 있습니다. 사용하는 API에 따라 일부는 직접 노출되지 않을 수 있습니다.

| 시스템 정의 속성 | 시스템 생성 또는 사용자 구성 가능| 목적 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | 시스템 생성 | 항목의 고유 식별자 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_etag | 시스템 생성 | 낙관적 동시성 제어에 사용되는 엔터티 태그 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_ts | 시스템 생성 | 항목의 마지막 업데이트에 대한 타임스탬프 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_self | 시스템 생성 | 항목의 주소 지정 가능 URI | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|id | 여기서는 | 논리적 파티션의 사용자 정의 고유 이름입니다. | 예 | 예 | 예 | 예 | 예 |
|임의의 사용자 정의 속성 | 사용자 정의 | API 기본 표시로 표시되는 사용자 정의 속성(JSON, BSON 및 CQL 포함) | 예 | 예 | 예 | 예 | 예 |

> [!NOTE]
> 속성 `id`의 고유성은 각 논리 파티션 내에서만 적용됩니다. 여러 문서에는 파티션 키 값이 서로 다른 동일한 `id` 속성이 있을 수 있습니다.

### <a name="operations-on-items"></a>항목에 대한 작업

Azure Cosmos 항목은 다음 작업을 지원합니다. Azure Cosmos API를 사용하여 작업을 수행할 수 있습니다.

| 작업 | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 삽입, 바꾸기, 삭제, Upsert, 읽기 | 아니요 | 예 | 예 | 예 | 예 | 예 |

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정 및 기타 개념을 관리하는 방법을 알아봅니다.

* 자세히 알아보려면 [Azure Cosmos DB SQL API](/learn/modules/intro-to-azure-cosmos-db-core-api/) 학습 모듈을 참조하세요.
* [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)
* [글로벌 분포](distribute-data-globally.md)
* [일관성 수준](consistency-levels.md)
* [Azure Cosmos 계정에 대한 VNET 서비스 엔드포인트](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos 계정에 대한 IP 방화벽](how-to-configure-firewall.md)
* [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
