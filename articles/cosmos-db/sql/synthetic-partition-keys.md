---
title: Azure Cosmos DB의 가상 파티션 키 만들기
description: Azure Cosmos 컨테이너에서 가상 파티션 키를 사용하여 파티션 키 전체에 데이터 및 워크로드를 균등하게 분산하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 169294ffa113ef02c42b0fbc7d1ff8e8381b9eff
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116903"
---
# <a name="create-a-synthetic-partition-key"></a>가상 파티션 키 만들기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

고유한 값이 많은(수백 개 또는 수천 개) 파티션 키를 사용하는 것이 좋습니다. 이러한 파티션 키 값과 연결된 항목에 데이터 및 워크로드를 균등하게 배포하는 것이 목표입니다. 이러한 속성이 데이터에 없는 경우 *가상 파티션 키* 를 생성할 수 있습니다. 이 문서에서는 Cosmos 컨테이너에 대한 가상 파티션 키를 생성하는 몇 가지 기본 기술을 설명합니다.

## <a name="concatenate-multiple-properties-of-an-item"></a>항목의 여러 속성 연결

여러 속성 값을 인위적인 `partitionKey` 속성 하나에 연결하여 파티션 키를 구성할 수 있습니다. 이러한 키는 가상 키로 참조됩니다. 예를 들어 다음 예제 문서를 살펴보겠습니다.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

이전 문서의 경우 한 가지 옵션은 /deviceId 또는 /date를 파티션 키로 설정하는 것입니다. 디바이스 ID 또는 날짜를 기준으로 컨테이너를 분할하려면 이 옵션을 사용합니다. 또 다른 옵션은 이러한 두 값을 파티션 키로 사용되는 가상 `partitionKey` 속성으로 연결하는 것입니다.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

실시간 시나리오에서는 데이터베이스에 수천 개의 항목이 있을 수 있습니다. 가상 키를 수동으로 추가하는 대신, 클라이언트 쪽 논리를 정의하여 값을 연결하고 가상 키를 Cosmos 컨테이너의 항목에 삽입합니다.

## <a name="use-a-partition-key-with-a-random-suffix"></a>임의의 접미사가 붙는 파티션 키 사용

워크로드를 보다 균등하게 분산할 수 있는 또 다른 전략은 파티션 키 값의 끝에 임의의 숫자를 추가하는 것입니다. 이러한 방식으로 항목을 분산하면 여러 파티션에서 병렬 쓰기 작업을 수행할 수 있습니다.

파티션 키가 날짜를 나타내는 경우를 예로 들 수 있습니다. 이 경우 1-400 사이의 난수를 선택하여 날짜에 접미사로 연결할 수 있습니다. 이 방법을 사용하면  `2018-08-09.1`, `2018-08-09.2`, 이런 식으로  `2018-08-09.400`까지 파티션 키 값을 얻을 수 있습니다. 파티션 키를 무작위로 지정하므로 매일 수행되는 컨테이너의 쓰기 작업이 여러 파티션에 균등하게 분배됩니다. 이 방법을 사용하면 병렬 처리 성능이 향상되고 전체적인 처리량이 많아집니다.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>미리 계산된 접미사가 붙는 파티션 키 사용 

임의 접미사 전략은 쓰기 처리량을 훨씬 향상하지만 특정 항목을 읽기 어렵습니다. 항목을 쓸 때 사용된 접미사 값을 알 수 없습니다. 개별 항목을 더 쉽게 읽으려면 미리 계산된 접미사 전략을 사용합니다. 난수를 사용하여 여러 파티션에 항목을 분산하는 대신, 쿼리하려는 항목을 기준으로 계산되는 숫자를 사용합니다.

날짜가 파티션 키로 컨테이너에 사용되는 이전 예제를 생각해 보세요. 이제 각 항목에 액세스하려는  `Vehicle-Identification-Number`(`VIN`) 특성이 있다고 가정합니다. 또한 날짜 외에 `VIN`으로 항목을 찾기 위해 쿼리를 자주 실행한다고 가정합니다. 애플리케이션은 컨테이너에 항목을 쓰기 전에 VIN을 기반으로 해시 접미사를 계산하여 파티션 키 날짜에 추가할 수 있습니다. 계산은 균등하게 분산된 1-400 사이의 숫자를 생성할 수 있습니다. 이 결과는 임의 접미사 전략 방법으로 생성된 결과와 유사합니다. 날짜에 계산 결과를 연결한 값이 파티션 키 값이 됩니다.

이 전략을 사용하면 쓰기가 파티션 키 값 및 파티션 간에 균등하게 분산됩니다. 특정 `Vehicle-Identification-Number`의 파티션 키 값을 계산할 수 있으므로 특정 항목과 날짜를 쉽게 읽을 수 있습니다. 이 방법의 장점은 단일 핫 파티션 키(즉, 모든 워크로드가 집중되는 파티션 키)를 피할 수 있다는 점입니다. 

## <a name="next-steps"></a>다음 단계

다음 문서에서 분할이라는 개념에 대해 좀 더 자세히 알아볼 수 있습니다.

* [논리 파티션](../partitioning-overview.md)에 대해 자세히 알아봅니다.
* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량을 프로비전](../set-throughput.md)하는 방법을 자세히 알아봅니다.
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
* Azure Cosmos DB로 마이그레이션하기 위해 용량 계획을 하려고 하십니까? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만을 알고 있는 경우, [vCore 또는 vCPU를 사용하여 요청 단위 추정](../convert-vcore-to-request-unit.md)을 참조하세요 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 빈도를 알고 있는 경우 [Azure Cosmos DB Capacity Planner를 사용하여 요청 단위 추정](estimate-ru-with-capacity-planner.md)을 읽어보세요
