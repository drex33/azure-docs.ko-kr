---
title: TTL(Time To Live)을 사용한 Azure Cosmos DB의 데이터 만료
description: TTL을 사용하여 Microsoft Azure Cosmos DB는 일정 기간 후에 시스템에서 문서를 자동으로 삭제하는 기능을 제공합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: e859e244e41b6bbd065244a285653b2d9d3988e9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116907"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB의 TTL(Time to Live)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 **TTL(Time to Live)** 을 통해 일정 기간이 지난 후 컨테이너에서 항목을 자동으로 삭제하는 기능을 제공합니다. 기본적으로 컨테이너 수준에서 TTL(Time to Live)을 설정하거나 항목별 기준으로 값을 재정의할 수 있습니다. 컨테이너 또는 항목 수준에서 TTL을 설정하면 Azure Cosmos DB는 마지막으로 수정한 시간으로부터 해당 시간이 지나면 이러한 항목이 자동으로 제거합니다. TTL(Time to Live) 값은 초 단위로 구성됩니다. TTL을 구성하면 시스템에서는 클라이언트 애플리케이션에서 명시적으로 발급된 삭제 작업 없이 TTL 값을 기준으로 만료된 항목을 자동으로 삭제합니다. TTL의 최대값은 2147483647입니다.

만료된 항목 삭제는 남은 [요청 단위](../request-units.md), 즉 사용자 요청에 따라 사용되지 않은 요청 단위를 사용하는 백그라운드 작업입니다. TTL이 만료된 후에도 컨테이너가 요청으로 오버로드되고 사용 가능한 RU가 부족하면 데이터 삭제가 지연됩니다. 삭제 작업을 수행할 수 있는 RU가 충분하면 데이터가 삭제됩니다. 데이터 삭제가 지연되더라도 TTL이 만료된 후에는 쿼리(모든 API에 의해)에서 데이터가 반환되지 않습니다.

> 이 콘텐츠는 Azure Cosmos DB 트랜잭션 저장소 TTL과 관련이 있습니다. [Azure Synapse Link](../synapse-link.md)를 통해 NoETL HTAP 시나리오를 사용하도록 설정하는 분석 저장소 TTL을 찾고 있다면 [여기](../analytical-store-introduction.md#analytical-ttl)를 클릭하세요.

## <a name="time-to-live-for-containers-and-items"></a>컨테이너 및 항목에 대한 TTL(Time to live)

TTL(Time to Live) 값은 초 단위로 설정되며 항목이 마지막으로 수정된 시간의 델타로 해석됩니다. TTL(Time to Live)은 컨테이너 또는 컨테이너 내 항목에서 설정할 수 있습니다.

1. **컨테이너에서 TTL(Time to Live)**(`DefaultTimeToLive` 사용하여 설정):

   - 누락(또는 null로 설정)되는 경우 항목이 자동으로 삭제되지 않습니다.

   - 표시되고 값이 "-1"로 설정되면 무한과 동일하며 항목은 기본적으로 만료되지 않습니다.

   - 표시되고 값이 일부 *0이 아닌* 숫자 *"n"* 으로 설정된 경우 항목은 마지막으로 수정된 시간으로부터 *"n"* 초 후에 만료됩니다.

2. **항목에서 TTL(Time to Live)**(`ttl` 사용하여 설정):

   - 이 속성은 `DefaultTimeToLive`가 표시되고 부모 컨테이너에 대해 null로 설정되지 않은 경우에만 적용할 수 있습니다.

   - 표시되면 부모 컨테이너의 `DefaultTimeToLive` 값을 재정의합니다.

## <a name="time-to-live-configurations"></a>TTL(Time to Live) 구성

- TTL이 컨테이너에서 *"n"* 으로 설정되면 해당 컨테이너의 항목은 *n* 초 후에 만료됩니다.  동일한 컨테이너에 고유한 TTL(Time to Live)이 있는 항목이 있는 경우 -1(만료되지 않음을 나타냄)로 설정합니다. 또는 일부 항목이 TTL(Time to Live) 설정을 다른 숫자로 정의한 경우에는 이러한 항목은 구성된 고유한 TTL 값을 기준으로 만료됩니다.

- 컨테이너에서 TTL을 설정하지 않으면, 이 컨테이너의 항목에서 TTL(Time to Live)은 효과가 없습니다.

- 컨테이너에서 TTL이 -1로 설정된 경우 TTL(Time to Live)이 n으로 설정된 이 컨테이너의 항목은 n초 후에 만료되고 나머지 항목은 만료되지 않습니다.

## <a name="examples"></a>예

이 섹션에서는 컨테이너 및 항목에 다른 TTL(Time to Live) 값이 할당된 몇 가지 예를 보여줍니다.

### <a name="example-1"></a>예 1

컨테이너의 TTL이 null(DefaultTimeToLive = null)로 설정되어 있음

|항목에 대한 TTL| 결과|
|---|---|
|ttl = null|TTL을 사용할 수 없습니다. 항목은 만료되지 않습니다(기본값).|
|ttl = -1|TTL을 사용할 수 없습니다. 항목은 만료되지 않습니다.|
|ttl = 2000|TTL을 사용할 수 없습니다. 항목은 만료되지 않습니다.|

### <a name="example-2"></a>예제 2

컨테이너의 TTL이 -1(DefaultTimeToLive = -1)로 설정되어 있음

|항목에 대한 TTL| 결과|
|---|---|
|ttl = null|TTL이 사용됩니다. 항목은 만료되지 않습니다(기본값).|
|ttl = -1|TTL이 사용됩니다. 항목은 만료되지 않습니다.|
|ttl = 2000|TTL이 사용됩니다. 항목은 2,000초 후에 만료됩니다.|

### <a name="example-3"></a>예제 3

컨테이너의 TTL이 1,000(DefaultTimeToLive = 1,000)으로 설정되어 있음

|항목에 대한 TTL| 결과|
|---|---|
|ttl = null|TTL이 사용됩니다. 항목은 1,000초(기본값) 후에 만료됩니다.|
|ttl = -1|TTL이 사용됩니다. 항목은 만료되지 않습니다.|
|ttl = 2000|TTL이 사용됩니다. 항목은 2,000초 후에 만료됩니다.|

## <a name="next-steps"></a>다음 단계

TTL(Time to Live)을 구성하는 방법을 다음 문서에서 알아봅니다.

- [TTL(Time to Live) 구성 방법](how-to-time-to-live.md)