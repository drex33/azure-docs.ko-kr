---
title: 쿼리용 Azure Table Storage 디자인 | Microsoft Docs
description: Azure Table Storage에서 쿼리용 테이블을 디자인합니다. 적절한 파티션 키를 선택하고, 쿼리를 최적화하고, Table service를 위해 데이터를 정렬합니다.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93316161"
---
# <a name="design-for-querying"></a>쿼리를 위한 디자인
Table service 솔루션은 읽기 집중적이거나, 쓰기 집중적이거나, 이 두 가지가 혼합되어 있을 수 있습니다. 이 아티클에서는 읽기 작업을 효율적으로 지원하기 위해 Table service를 디자인할 때 기억해야 할 사항에 중점을 둡니다. 일반적으로 읽기 작업을 효율적으로 지원하는 디자인은 쓰기 작업에도 효율적입니다. 그러나 쓰기 작업을 지원하기 위해 디자인 시 기억해야 할 추가 고려 사항이 [데이터 수정을 위한 디자인](table-storage-design-for-modification.md) 아티클에서 설명됩니다.

데이터를 효율적으로 읽을 수 있도록 Table service 솔루션을 디자인하려면 "애플리케이션이 Table service에서 필요한 데이터를 검색하기 위해 실행해야 하는 쿼리는 무엇인가?"라는 질문에서 출발하는 것이 좋습니다.  

> [!NOTE]
> Table service에서는 사전에 올바르게 디자인하는 것이 중요합니다. 나중에 변경하려면 작업이 어렵고 비용이 많이 들기 때문입니다. 예를 들어 관계형 데이터베이스에서는 기존 데이터베이스에 인덱스를 추가하는 것만으로 성능 문제를 해결할 수 있는 경우가 종종 있습니다. 하지만 Table service에서는 그럴 수 없습니다.  
> 
> 

이 섹션에서는 쿼리를 위한 테이블을 디자인할 때 해결해야 하는 주요 문제를 중점적으로 알아봅니다. 이 섹션에서 다루는 항목은 다음과 같습니다.

* [PartitionKey 및 RowKey 선택이 쿼리 성능에 미치는 영향](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [적절한 PartitionKey 선택](#choosing-an-appropriate-partitionkey)
* [Table service에 대한 쿼리 최적화](#optimizing-queries-for-the-table-service)
* [Table service에서 데이터 정렬](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>PartitionKey 및 RowKey 선택이 쿼리 성능에 미치는 영향
다음 예제에서는 테이블 서비스가 다음과 같은 구조로 직원 엔터티를 저장하는 것으로 가정합니다(대부분의 예제에 명확성을 위해 **Timestamp** 속성이 생략되어 있음).  

| *열 이름* | *데이터 형식* |
| --- | --- |
| **PartitionKey** (부서 이름) |String |
| **RowKey**(직원 ID) |String |
| **FirstName** |String |
| **LastName** |String |
| **Age** |정수 |
| **EmailAddress** |String |

[Azure Table Storage 개요](table-storage-overview.md) 아티클에서는 쿼리를 디자인하는 데 직접적인 영향을 주는 Azure Table service의 주요 기능 중 일부에 대해 설명합니다. 이 섹션의 내용은 Table service 쿼리 디자인에 대한 다음과 같은 일반적인 지침으로 요약됩니다. 아래 예제에 사용된 필터 구문은 Table service REST API에서 가져온 것입니다(자세한 내용은 [엔터티 쿼리](/rest/api/storageservices/Query-Entities)참조).  

* ***지점 쿼리** _는 가장 효율적인 조회 방법이며, 대용량 조회 또는 최저 대기 시간이 필요한 조회에 사용하는 것이 좋습니다. 해당 쿼리는 인덱스를 통해 _ *PartitionKey** 및 **RowKey** 값을 둘 다 지정하여 개별 엔터티를 매우 효율적으로 찾을 수 있습니다. 예: $filter=(PartitionKey eq 'Sales') and (RowKey eq '2')  
* 두 번째로 좋은 방법은 _ *PartitionKey**를 사용하고 **RowKey** 값의 범위를 필터링하여 둘 이상의 엔터티를 반환하는 ***범위 쿼리** _입니다. **PartitionKey** 값은 특정 파티션을 식별하고, **RowKey** 값은 해당 파티션에 있는 엔터티의 하위 집합을 식별합니다. 예: $filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'  
* 세 번째로 좋은 방법은 _ *PartitionKey**를 사용하고 키가 아닌 다른 속성을 필터링하며 둘 이상의 엔터티를 반환할 수 있는 ***파티션 검색** _입니다. **PartitionKey** 값은 특정 파티션을 식별하고, 속성 값은 해당 파티션에 있는 엔터티의 하위 집합에 대해 선택됩니다. 예: $filter=PartitionKey eq 'Sales' and LastName eq 'Smith'  
* ***테이블 검색** _은 _ *PartitionKey**를 포함하지 않으며, 테이블을 구성하는 모든 파티션에서 일치하는 모든 엔터티를 검색하기 때문에 매우 비효율적입니다. 필터에서 **RowKey** 를 사용하는지 여부에 상관없이 테이블 검색을 수행합니다. 예: $filter=LastName eq 'Jones'  
* 여러 엔터티를 반환하는 쿼리는 **PartitionKey** 와 **RowKey** 순으로 정렬된 엔터티를 반환합니다. 클라이언트에서 엔터티 재정렬을 방지하려면 가장 일반적인 정렬 순서를 정의하는 **RowKey** 를 선택합니다.  

"**or**"를 사용하여 **RowKey** 값을 기반으로 필터를 지정하면 범위 쿼리로 처리되는 것이 아니라 파티션 검색이 수행됩니다. 따라서 다음과 같은 필터를 사용하는 쿼리는 피해야 합니다. $filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')  

Storage 클라이언트 라이브러리를 사용하여 효율적인 쿼리를 실행하는 클라이언트 쪽 코드의 예는 다음을 참조하세요.  

* [스토리지 클라이언트 라이브러리를 사용하여 지점 쿼리 실행](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [LINQ를 사용하여 여러 엔터티 검색](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [서버 쪽 프로젝션](table-storage-design-patterns.md#server-side-projection)  

동일한 테이블에 저장된 여러 엔터티 유형을 처리할 수 있는 클라이언트 쪽 코드의 예는 다음을 참조하세요.  

* [유형이 다른 엔터티 형식 사용](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>적절한 PartitionKey 선택
**PartitionKey** 를 선택할 때는 EGT 사용에 대한 요구 사항(일관성 유지)과 여러 파티션에 엔터티를 분산하는 데 대한 요구 사항(솔루션의 확장성 향상) 간에 균형을 유지해야 합니다.  

한 쪽으로 치우치면 모든 엔터티를 단일 파티션에 저장할 수 있지만 솔루션의 확장성이 제한되어 테이블 서비스에서 요청 부하를 분산하지 못할 수 있습니다. 다른 쪽으로 치우치면 파티션당 하나의 엔터티를 저장하여 확장성을 높이고 테이블 서비스에서 요청 부하를 분산할 수 있지만 엔터티 그룹 트랜잭션을 사용하지 못합니다.  

이상적인 **PartitionKey** 는 효율적인 쿼리를 사용할 수 있도록 하고 솔루션을 확장할 수 있는 충분한 파티션이 있는 키입니다. 일반적으로 엔터티에는 충분한 파티션으로 엔터티를 분산하는 적절한 속성이 있습니다.

> [!NOTE]
> 예를 들어 사용자 또는 직원에 대한 정보를 저장하는 시스템에서는 UserID가 적절한 PartitionKey일 수 있습니다. 지정된 UserID를 파티션 키로 사용하는 여러 엔터티가 있을 수 있습니다. 사용자에 대한 데이터를 저장하는 각 엔터티는 단일 파티션으로 그룹화되므로 이러한 엔터티는 높은 확장성을 유지하면서 엔터티 그룹 트랜잭션을 통해 액세스할 수 있습니다.
> 
> 

엔터티를 삽입하고, 업데이트하고, 삭제하는 방법과 관련하여 선택한 **PartitionKey** 에 추가 고려 사항이 있습니다. 자세한 내용은 [데이터 수정을 위한 테이블 디자인](table-storage-design-for-modification.md)을 참조하세요.  

## <a name="optimizing-queries-for-the-table-service"></a>Table service에 대한 쿼리 최적화
Table service는 단일 클러스터형 인덱스의 **PartitionKey** 및 **RowKey** 값을 사용하여 엔터티를 자동으로 인덱싱하기 때문에 지점 쿼리가 가장 효율적입니다. 그러나 **PartitionKey** 및 **RowKey** 에는 클러스터형 인덱스에 있는 인덱스만 있습니다.

대부분의 디자인은 여러 조건을 기반으로 엔터티를 조회할 수 있어야 한다는 요구 사항을 준수해야 합니다. 예를 들어, 메일, 직원 ID 또는 성을 기반으로 직원 엔터티를 찾을 수 있어야 합니다. [테이블 디자인 패턴](table-storage-design-patterns.md)에 설명된 패턴은 이러한 형식의 요구 사항을 해결하고, Table service가 보조 인덱스를 제공하지 않는다는 사실을 해결하는 방법을 설명합니다.  

* [파티션 간 보조 인덱스 패턴](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - 서로 다른 **RowKey** 값을 사용하여 각 엔터티의 여러 복사본을 동일한 파티션에 저장하여 빠르고 효율적인 조회를 지원하며, 서로 다른 **RowKey** 값을 사용하여 대체 정렬 순서를 허용합니다.  
* [파티션 간 보조 인덱스 패턴](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 서로 다른 **RowKey** 값을 사용하여 각 엔터티의 여러 복사본을 별도의 파티션과 별도의 테이블에 저장하여 빠르고 효율적인 조회를 지원하며, 서로 다른 **RowKey** 값을 사용하여 대체 정렬 순서를 허용합니다.  
* [인덱스 엔터티 패턴](table-storage-design-patterns.md#index-entities-pattern) - 인덱스 엔터티를 유지 관리하여 엔터티 목록을 반환하는 효율적인 검색을 지원합니다.  

## <a name="sorting-data-in-the-table-service"></a>Table service에서 데이터 정렬
Table service는 **PartitionKey** 를 기준으로 한 다음 **RowKey** 를 기준으로 하여 오름차순으로 정렬된 엔터티를 반환합니다. 이러한 키는 문자열 값이며, 숫자 값이 올바르게 정렬되도록 하려면 이를 고정 길이로 변환하고 0으로 채워야 합니다. 예를 들어 **RowKey** 로 사용하는 직원 ID 값이 정수 값인 경우 직원 ID를 **123** 에서 **00000123** 으로 변환해야 합니다.  

많은 애플리케이션에서 서로 다른 순서로 정렬(예: 이름 또는 입사 날짜별로 직원 정렬)된 데이터를 사용할 수 있도록 요구하고 있습니다. 다음과 같은 패턴으로 엔터티에 대한 정렬 순서를 대체하는 방법을 해결합니다.  

* [파티션 간 보조 인덱스 패턴](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - 서로 다른 RowKey 값을 사용하여 각 엔터티의 여러 복사본을 동일한 파티션에 저장하여 빠르고 효율적인 조회를 지원하며, 서로 다른 RowKey 값을 사용하여 대체 정렬 순서를 허용합니다.  
* [파티션 간 보조 인덱스 패턴](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 서로 다른 RowKey 값을 사용하여 각 엔터티의 여러 복사본을 별도의 파티션과 별도의 표에 저장하여 빠르고 효율적인 조회를 지원하며, 서로 다른 RowKey 값을 사용하여 대체 정렬 순서를 허용합니다.
* [로그 테일 패턴](table-storage-design-patterns.md#log-tail-pattern) - 날짜 및 시간 역순으로 정렬된 *RowKey* 값을 사용하여 가장 최근에 파티션에 추가된 **n** 엔터티를 검색합니다.  

## <a name="next-steps"></a>다음 단계

- [테이블 디자인 패턴](table-storage-design-patterns.md)
- [관계 모델링](table-storage-design-modeling.md)
- [테이블 데이터 암호화](table-storage-design-encrypt-data.md)
- [데이터 수정을 위한 디자인](table-storage-design-for-modification.md)