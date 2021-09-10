---
title: Azure Cosmos DB의 충돌 해결 유형 및 해결 정책
description: 이 문서에서는 Azure Cosmos DB의 충돌 범주 및 충돌 해결 정책에 대해 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: aa21a1a6d6dfdd89f6532159a8da98fc5df08465
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112281362"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>여러 쓰기 지역 사용 시 충돌 형식 및 해결 정책
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

충돌 및 충돌 해결 정책은 Azure Cosmos DB 계정이 여러 쓰기 지역으로 구성된 경우 적용될 수 있습니다.

여러 쓰기 지역으로 구성된 Azure Cosmos 계정의 경우 작성자가 여러 지역에서 동일한 항목을 동시에 업데이트하는 경우 업데이트 충돌이 발생할 수 있습니다. 업데이트 충돌은 다음 세 가지 유형 중 하나일 수 있습니다.

* **삽입 충돌**: 이러한 충돌은 애플리케이션이 둘 이상의 지역에서 동일한 고유 인덱스를 사용하여 둘 이상의 항목을 동시에 삽입하는 경우 발생할 수 있습니다. 예를 들어, ID 속성을 사용하는 경우 이 충돌이 발생할 수 있습니다.

* **대체 충돌**: 이러한 충돌은 애플리케이션이 둘 이상의 지역에서 동일한 항목을 동시에 업데이트하는 경우 발생할 수 있습니다.

* **삭제 충돌**: 이러한 충돌은 애플리케이션이 동시에 한 지역에서 항목을 삭제하고 다른 지역에서 업데이트하는 경우 발생할 수 있습니다.

## <a name="conflict-resolution-policies"></a>충돌 해결 정책

Azure Cosmos DB는 쓰기 충돌을 해결하기 위해 유연한 정책 기반 메커니즘을 제공합니다. Azure Cosmos 컨테이너의 두 가지 충돌 해결 정책 중에서 선택할 수 있습니다.

* **LWW(마지막 쓰기 우선)** : 기본적으로 이 해결 정책은 시스템에서 정의된 타임스탬프 속성을 사용하며, 시간 동기화 클록 프로토콜을 기반으로 합니다. SQL API를 사용하는 경우 충돌 해결에 사용할 다른 사용자 지정 숫자 속성(예: 타임스탬프의 고유한 표기)을 지정할 수 있습니다. 사용자 지정 숫자 속성은 *충돌 해결 경로* 라고도 합니다. 

  두 개 이상 항목이 삽입 또는 대체 작업에서 충돌하는 경우 충돌 해결 경로에 대한 가장 높은 값이 포함된 항목이 선정됩니다. 여러 항목에 충돌 해결 경로에 대한 동일한 숫자 값이 있는 경우 시스템에서 선정 항목이 결정됩니다. 모든 지역은 단일 선정 항목으로 수렴되고 결국 동일한 버전의 커밋된 항목으로 끝나게 됩니다. 삭제 충돌이 포함되어 있으면 삭제된 버전이 항상 삽입 또는 대체 충돌에 비해 우선적으로 선정됩니다. 이 결과는 충돌 해결 경로 값에 관계없이 발생합니다.

  > [!NOTE]
  > 마지막 쓰기 우선은 기본 충돌 해결 정책이며 SQL, MongoDB, Cassandra, Gremlin 및 Table API에 대해 `_ts` 타임스탬프를 사용합니다. 사용자 지정 숫자 속성은 SQL API에 대해서만 사용할 수 있습니다.

  자세한 내용은 [LWW 충돌 해결 정책을 사용하는 예제](how-to-manage-conflicts.md)를 참조하세요.

* **사용자 지정**: 이 해결 정책은 충돌 조정용 애플리케이션 정의 의미 체계를 위해 설계되었습니다. Azure Cosmos 컨테이너에 이 정책을 설정한 경우 *병합 저장 프로시저* 도 등록해야 합니다. 이 프로시저는 서버의 데이터베이스 트랜잭션 내에서 충돌을 감지하는 경우 자동으로 호출됩니다. 시스템은 커밋 프로토콜의 일부로 병합 프로시저의 실행에 대해 정확히 한 번의 보장을 제공합니다.  

  사용자 지정 해결 옵션을 사용하여 컨테이너를 구성하는 경우, 컨테이너에서 병합 프로시저를 등록하지 못하거나 병합 프로시저가 런타임에서 예외를 throw하는 경우 충돌이 *충돌 피드* 에 기록됩니다. 그러면, 애플리케이션은 충돌 피드에서 충돌을 수동으로 해결해야 합니다. 자세한 내용은 [사용자 지정 해결 정책을 사용하는 방법 및 충돌 피드를 사용하는 방법의 예제](how-to-manage-conflicts.md)를 참조하세요.

  > [!NOTE]
  > 사용자 지정 충돌 해결 정책은 SQL API 계정에 대해서만 사용할 수 있으며 생성 시간에만 설정할 수 있습니다. 기존 컨테이너에 사용자 지정 해결 정책을 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

충돌 해결 정책 구성 방법 알아보기:

* [애플리케이션에 대해 여러 쓰기 지역을 구성하는 방법](how-to-multi-master.md)
* [충돌 해결 정책을 관리하는 방법](how-to-manage-conflicts.md)
* [충돌 피드를 읽는 방법](how-to-manage-conflicts.md#read-from-conflict-feed)
