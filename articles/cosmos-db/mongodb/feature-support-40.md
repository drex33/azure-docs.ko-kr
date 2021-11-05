---
title: Azure Cosmos DB의 API for MongoDB 4.0 서버 버전에서 지원되는 기능 및 구문
description: Azure Cosmos DB의 API for MongoDB 4.0 서버 버전에서 지원되는 기능 및 구문에 대해 알아봅니다. 데이터베이스 명령, 쿼리 언어 지원, 데이터 형식, 집계 파이프라인 명령 및 지원되는 연산자에 대해 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: a5994bdfcdaf1203246c71ecddc2a94f4be5a022
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450061"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>Azure Cosmos DB의 API for MongoDB(4.0 서버 버전): 지원되는 기능 및 구문
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. 오픈 소스 MongoDB 클라이언트 [드라이버](https://docs.mongodb.org/ecosystem/drivers)를 사용하여 Azure Cosmos DB의 API for MongoDB와 통신할 수 있습니다. Azure Cosmos DB의 API for MongoDB를 사용하면 MongoDB [유선 프로토콜](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)에 따라 기존 클라이언트 드라이버를 사용할 수 있습니다.

Azure Cosmos DB의 API for MongoDB를 사용하면 Cosmos DB가 제공하는 모든 엔터프라이즈 기능과 함께 익숙한 MongoDB의 이점을 누릴 수 있습니다. 이러한 기능에는 [글로벌 배포](../distribute-data-globally.md), [자동 분할](../partitioning-overview.md), 가용성 및 대기 시간 보장, 미사용 암호화, 백업 등이 포함됩니다.

## <a name="protocol-support"></a>프로토콜 지원

지원되는 연산자 및 제한 사항이나 예외는 다음과 같습니다. 이러한 프로토콜을 인식하는 모든 클라이언트 드라이버는 Azure Cosmos DB의 API for MongoDB에 연결할 수 있습니다. Azure Cosmos DB의 API for MongoDB 계정을 사용하는 경우 3.6 버전 이상의 계정에는 `*.mongo.cosmos.azure.com` 형식의 엔드포인트가 있지만 3.2 버전의 계정에는 `*.documents.azure.com` 형식의 엔드포인트가 있습니다.

> [!NOTE]
> 이 문서에서는 지원되는 서버 명령만 나열하고 클라이언트 쪽 래퍼 함수는 제외합니다. `deleteMany()` 및 `updateMany()`와 같은 클라이언트 쪽 래퍼 함수는 내부적으로 `delete()` 및 `update()` 서버 명령을 활용합니다. 지원되는 서버 명령을 활용하는 함수는 Azure Cosmos DB의 API for MongoDB와 호환됩니다.

## <a name="query-language-support"></a>쿼리 언어 지원

Azure Cosmos DB의 API for MongoDB는 MongoDB 쿼리 언어 구문을 포괄적으로 지원합니다. 아래에서 현재 지원되는 연산, 연산자, 단계, 명령 및 옵션에 대한 자세한 목록을 찾을 수 있습니다.

## <a name="database-commands"></a>데이터베이스 명령

Azure Cosmos DB의 API for MongoDB는 다음과 같은 데이터베이스 명령을 지원합니다.

### <a name="query-and-write-operation-commands"></a>쿼리 및 쓰기 작업 명령

| 명령 | 지원됨 |
|---------|---------|
| [change streams](change-streams.md) | 예 |
| delete | 예 |
| eval | 예 |
| 찾기 | 예 |
| findAndModify | 예 |
| getLastError | 예 |
| getMore | 예 |
| getPrevError | 예 |
| insert | 예 |
| parallelCollectionScan | 아니요 |
| resetError | 예 |
| update | 예 |

### <a name="transaction-commands"></a>트랜잭션 명령

| 명령 | 지원됨 |
|---------|---------|
| abortTransaction | 예 |
| commitTransaction | 예 |

### <a name="authentication-commands"></a>인증 명령

| 명령 | 지원됨 |
|---------|---------|
| authenticate | 예 |
| getnonce | 예 |
| logout | 예 |

### <a name="administration-commands"></a>관리 명령

| 명령 | 지원됨 |
|---------|---------|
| cloneCollectionAsCapped | 예 |
| collMod | 예 |
| connectionStatus | 예 |
| convertToCapped | 예 |
| copydb | 예 |
| create | 예 |
| createIndexes | 예 |
| currentOp | 예 |
| drop | 예 |
| dropDatabase | 예 |
| dropIndexes | 예 |
| filemd5 | 예 |
| killCursors | 예 |
| killOp | 예 |
| listCollections | 예 |
| listDatabases | 예 |
| listIndexes | 예 |
| reIndex | 예 |
| renameCollection | 예 |

### <a name="diagnostics-commands"></a>진단 명령

| 명령 | 지원됨 |
|---------|---------|
| buildInfo | 예 |
| collStats | 예 |
| connPoolStats | 예 |
| connectionStatus | 예 |
| dataSize | 예 |
| dbHash | 예 |
| dbStats | 예 |
| 설명 | 예 |
| 기능 | 예 |
| hostInfo | 예 |
| listDatabases | 예 |
| listCommands | 예 |
| 프로파일러 | 예 |
| serverStatus | 예 |
| top | 예 |
| whatsmyuri | 예 |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>집계 파이프라인

### <a name="aggregation-commands"></a>집계 명령

| 명령 | 지원됨 |
|---------|---------|
| aggregate | 예 |
| count | 예 |
| distinct | 예 |
| mapReduce | 예 |

### <a name="aggregation-stages"></a>집계 단계

| 명령 | 지원됨 |
|---------|---------|
| $addFields | 예 |
| $bucket | 예 |
| $bucketAuto | 예 |
| $changeStream | 예 |
| $collStats | 예 |
| $count | 예 |
| $currentOp | 예 |
| $facet | 예 |
| $geoNear | 예 |
| $graphLookup | 예 |
| $group | 예 |
| $indexStats | 예 |
| $limit | 예 |
| $listLocalSessions | 예 |
| $listSessions | 예 |
| $lookup | Partial |
| $match | 예 |
| $out | 예 |
| $project | 예 |
| $redact | 예 |
| $replaceRoot | 예 |
| $replaceWith | 아니요 |
| $sample | 예 |
| $skip | 예 |
| $sort | 예 |
| $sortByCount | 예 |
| $unwind | 예 |

> [!NOTE]
> `$lookup`은 서버 버전 3.6에 도입된 [상관 관계가 없는 하위 쿼리](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries) 기능을 아직 지원하지 않습니다. `let` 및 `pipeline` 필드에서 `$lookup` 연산자를 사용하려고 하면 `let is not supported`가 포함된 오류 메시지가 표시됩니다.

### <a name="boolean-expressions"></a>부울 식

| 명령 | 지원됨 |
|---------|---------|
| $and | 예 |
| $not | 예 |
| $or | 예 |

### <a name="conversion-expressions"></a>변환 식

| 명령 | 지원됨 |
|---------|---------|
| $convert | 예 |
| $toBool | 예 |
| $toDate | 예 |
| $toDecimal | 예 |
| $toDouble | 예 |
| $toInt | 예 |
| $toLong | 예 |
| $toObjectId | 예 |
| $toString | 예 |

### <a name="set-expressions"></a>식 설정

| 명령 | 지원됨 |
|---------|---------|
| $setEquals | 예 |
| $setIntersection | 예 |
| $setUnion | 예 |
| $setDifference | 예 |
| $setIsSubset | 예 |
| $anyElementTrue | 예 |
| $allElementsTrue | 예 |

### <a name="comparison-expressions"></a>비교 식

> [!NOTE]
> API for MongoDB는 쿼리에서 배열 리터럴이 있는 비교 식을 지원하지 않습니다.

| 명령 | 지원됨 |
|---------|---------|
| $cmp | 예 |
| $eq | 예 | 
| $gt | 예 | 
| $gte | 예 | 
| $lt | 예 |
| $lte | 예 | 
| $ne | 예 | 
| $in | 예 | 
| $nin | 예 | 

### <a name="arithmetic-expressions"></a>산술 식

| 명령 | 지원됨 |
|---------|---------|
| $abs | 예 |
| $add | 예 |
| $ceil | 예 |
| $divide | 예 |
| $exp | 예 |
| $floor | 예 |
| $ln | 예 |
| $log | 예 |
| $log10 | 예 |
| $mod | 예 |
| $multiply | 예 |
| $pow | 예 |
| $sqrt | 예 |
| $subtract | 예 |
| $trunc | 예 |

### <a name="string-expressions"></a>문자열 식

| 명령 | 지원됨 |
|---------|---------|
| $concat | 예 |
| $indexOfBytes | 예 |
| $indexOfCP | 예 |
| $ltrim | 예 |
| $rtrim | 예 |
| $trim | 예 |
| $split | 예 |
| $strLenBytes | 예 |
| $strLenCP | 예 |
| $strcasecmp | 예 |
| $substr | 예 |
| $substrBytes | 예 |
| $substrCP | 예 |
| $toLower | 예 |
| $toUpper | 예 |

### <a name="text-search-operator"></a>텍스트 검색 연산자

| 명령 | 지원됨 |
|---------|---------|
| $meta | 예 |

### <a name="array-expressions"></a>배열 식

| 명령 | 지원됨 |
|---------|---------|
| $arrayElemAt | 예 |
| $arrayToObject | 예 |
| $concatArrays | 예 |
| $filter | 예 |
| $indexOfArray | 예 |
| $isArray | 예 |
| $objectToArray | 예 |
| $range | 예 |
| $reverseArray | 예 |
| $reduce | 예 |
| $size | 예 |
| $slice | 예 |
| $zip | 예 |
| $in | 예 |

### <a name="variable-operators"></a>변수 연산자

| 명령 | 지원됨 |
|---------|---------|
| $map | 예 |
| $let | 예 |

### <a name="system-variables"></a>시스템 변수

| 명령 | 지원됨 |
|---------|---------|
| $$CURRENT | 예 |
| $$DESCEND | 예 |
| $$KEEP | 예 |
| $$PRUNE | 예 |
| $$REMOVE | 예 |
| $$ROOT | 예 |

### <a name="literal-operator"></a>리터럴 연산자

| 명령 | 지원됨 |
|---------|---------|
| $literal | 예 |

### <a name="date-expressions"></a>날짜 식

| 명령 | 지원됨 |
|---------|---------|
| $dayOfYear | 예 |
| $dayOfMonth | 예 |
| $dayOfWeek | 예 |
| $year | 예 |
| $month | 예 | 
| $week | 예 |
| $hour | 예 |
| $minute | 예 | 
| $second | 예 |
| $millisecond | 예 | 
| $dateToString | 예 |
| $isoDayOfWeek | 예 |
| $isoWeek | 예 |
| $dateFromParts | Yes | 
| $dateToParts | Yes |
| $dateFromString | Yes |
| $isoWeekYear | 예 |

### <a name="conditional-expressions"></a>조건부 식

| 명령 | 지원됨 |
|---------|---------|
| $cond | 예 |
| $ifNull | 예 |
| $switch | 예 |

### <a name="data-type-operator"></a>데이터 형식 연산자

| 명령 | 지원됨 |
|---------|---------|
| $type | 예 |

### <a name="accumulator-expressions"></a>누적기 식

| 명령 | 지원됨 |
|---------|---------|
| $sum | 예 |
| $avg | 예 |
| $first | 예 |
| $last | 예 |
| $max | 예 |
| $min | 예 |
| $push | 예 |
| $addToSet | 예 |
| $stdDevPop | 예 |
| $stdDevSamp | 예 |

### <a name="merge-operator"></a>Merge 연산자

| 명령 | 지원됨 |
|---------|---------|
| $mergeObjects | 예 |

## <a name="data-types"></a>데이터 형식

Azure Cosmos DB의 API for MongoDB는 MongoDB BSON 형식으로 인코딩된 문서를 지원합니다. 4\.0 API 버전은 이 형식의 내부 사용을 개선하여 성능을 향상시키고 비용을 절감합니다. 4\.0을 실행하는 엔드포인트를 통해 작성되거나 업데이트된 문서에서 이를 활용합니다.
 
[업그레이드 시나리오](upgrade-mongodb-version.md)에서는 버전 4.0으로 업그레이드되기 전에 작성된 문서가 4.0 엔드포인트를 통한 쓰기 작업을 통해 업데이트될 때까지 향상된 성능을 제공하지 않습니다.

| 명령 | 지원됨 |
|---------|---------|
| Double | 예 |
| String | 예 |
| Object | 예 |
| Array | 예 |
| 이진 데이터 | 예 | 
| ObjectId | 예 |
| 부울 | 예 |
| Date | 예 |
| Null | 예 |
| 32비트 정수(int) | 예 |
| 타임스탬프 | 예 |
| 64비트 정수(long) | 예 |
| MinKey | 예 |
| MaxKey | 예 |
| Decimal128 | 예 | 
| 정규식 | 예 |
| JavaScript | 예 |
| JavaScript(범위 포함)| 예 |
| 정의되지 않음 | 예 |

## <a name="indexes-and-index-properties"></a>인덱스 및 인덱스 속성

### <a name="indexes"></a>인덱스

| 명령 | 지원됨 |
|---------|---------|
| 단일 필드 인덱스 | 예 |
| 복합 인덱스 | 예 |
| Multikey 인덱스 | 예 |
| 텍스트 인덱스 | 예 |
| 2dsphere | 예 |
| 2d 인덱스 | 예 |
| 해시된 인덱스 | 예 |

### <a name="index-properties"></a>인덱스 속성

| 명령 | 지원됨 |
|---------|---------|
| TTL | 예 |
| 고유한 | 예 |
| 부분 | 예 |
| 대/소문자 구분 안 함 | 예 |
| 스파스 | 예 |
| 배경 | 예 |

## <a name="operators"></a>연산자

### <a name="logical-operators"></a>논리 연산자

| 명령 | 지원됨 |
|---------|---------|
| $or | 예 |
| $and | 예 |
| $not | 예 |
| $nor | 예 | 

### <a name="element-operators"></a>요소 연산자

| 명령 | 지원됨 |
|---------|---------|
| $exists | 예 |
| $type | 예 |

### <a name="evaluation-query-operators"></a>평가 쿼리 연산자

| 명령 | 지원됨 |
|---------|---------|
| $expr | 예 |
| $jsonSchema | 예 |
| $mod | 예 |
| $regex | 예 |
| $text | 아니요(지원되지 않습니다. 대신 $regex를 사용합니다.)| 
| $where | 예 | 

$regex 쿼리에서 왼쪽에 고정된 식은 인덱스 검색을 허용합니다. 그러나 ‘i’ 한정자(대/소문자 구분 없음) 및 ‘m’ 한정자(여러 줄)를 사용하면 모든 식에서 컬렉션 검사가 수행됩니다.

‘$’ 또는 ‘|’을 포함해야 하는 경우 두 개 이상의 정규식 쿼리를 만드는 것이 가장 좋습니다. 예를 들어, 다음 원래 쿼리가 `find({x:{$regex: /^abc$/})`인 경우 이를 다음과 같이 수정해야 합니다.

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

첫 번째 부분은 인덱스를 사용하여 ^abc로 시작하는 문서로 검색을 제한하며, 두 번째 부분은 정확한 항목의 일치를 확인합니다. 막대 연산자 ‘|’는 “or” 함수 역할을 하고, `find({x:{$regex: /^abc |^def/})` 쿼리는 ‘x’ 필드에 “abc” 또는 “def”로 시작하는 값이 있는 문서를 일치하는 항목으로 검색합니다. 인덱스를 사용하려면 쿼리를 $or 연산자 `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`에 의해 조인된 두 개의 서로 다른 쿼리로 나누는 것이 좋습니다.

### <a name="array-operators"></a>배열 연산자

| 명령 | 지원됨 | 
|---------|---------|
| $all | 예 | 
| $elemMatch | 예 | 
| $size | 예 | 

### <a name="comment-operator"></a>설명 연산자

| 명령 | 지원됨 | 
|---------|---------|
| $comment | 예 | 

### <a name="projection-operators"></a>프로젝션 연산자

| 명령 | 지원됨 |
|---------|---------|
| $elemMatch | 예 |
| $meta | 예 |
| $slice | 예 |

### <a name="update-operators"></a>업데이트 연산자

#### <a name="field-update-operators"></a>필드 업데이트 연산자

| 명령 | 지원됨 |
|---------|---------|
| $inc | 예 |
| $mul | 예 |
| $rename | 예 |
| $setOnInsert | 예 |
| $set | 예 |
| $unset | 예 |
| $min | 예 |
| $max | 예 |
| $currentDate | 예 |

#### <a name="array-update-operators"></a>배열 업데이트 연산자

| 명령 | 지원됨 |
|---------|---------|
| $ | 예 |
| $[]| 예 |
| $[\<identifier\>]| 예 |
| $addToSet | 예 |
| $pop | 예 |
| $pullAll | 예 |
| $pull | 예 |
| $push | 예 |
| $pushAll | 예 |

#### <a name="update-modifiers"></a>한정자 업데이트

| 명령 | 지원됨 |
|---------|---------|
| $each | 예 |
| $slice | 예 |
| $sort | 예 |
| $position | 예 |

#### <a name="bitwise-update-operator"></a>비트 업데이트 연산자

| 명령 | 지원됨 |
|---------|---------|
| $bit | 예 | 
| $bitsAllSet | 예 |
| $bitsAnySet | 예 |
| $bitsAllClear | 예 |
| $bitsAnyClear | 예 |

### <a name="geospatial-operators"></a>지리 공간적 연산자

연산자 | 지원됨 | 
--- | --- |
$geoWithin | 예 |
$geoIntersects | 예 | 
$near | 예 |
$nearSphere | 예 |
$geometry | 예 |
$minDistance | 예 |
$maxDistance | 예 |
$center | No |
$centerSphere | No |
$box | No |
$polygon | No |

## <a name="sort-operations"></a>정렬 작업

`findOneAndUpdate` 작업을 사용할 경우 단일 필드에 대한 정렬 작업이 지원되지만 여러 필드에 대한 정렬 작업은 지원되지 않습니다.

## <a name="indexing"></a>인덱싱
API for MongoDB는 여러 필드를 기준으로 정렬하고, 쿼리 성능을 향상시키고, 고유성을 적용할 수 있도록 [다양한 인덱스를 지원](mongodb-indexing.md)합니다.

## <a name="gridfs"></a>GridFS

Azure Cosmos DB는 GridFS 호환 Mongo 드라이버를 통해 GridFS를 지원합니다.

## <a name="replication"></a>복제

Azure Cosmos DB는 가장 낮은 계층에서 자동의 네이티브 복제를 지원합니다. 이 논리는 또한 짧은 대기 시간, 글로벌 복제를 달성하기 위해 확장됩니다. Cosmos DB는 수동 복제 명령을 지원하지 않습니다.

## <a name="retryable-writes"></a>다시 시도 가능한 쓰기

Cosmos DB는 아직 다시 시도 가능한 쓰기를 지원하지 않습니다. 클라이언트 드라이버는 'retryWrites=false' URL 매개 변수를 연결 문자열에 추가해야 합니다. '&'를 앞에 붙이면 더 많은 URL 매개 변수를 추가할 수 있습니다. 

## <a name="sharding"></a>분할

Azure Cosmos DB는 서버 측 자동 분할을 지원합니다. 분할 만들기, 배치 및 분산을 자동으로 관리합니다. Azure Cosmos DB는 수동 분할 명령을 지원하지 않으므로 addShard, balancerStart, moveChunk 등과 같은 명령을 호출할 필요가 없습니다. 컨테이너를 만들거나 데이터를 쿼리하는 동안 분할 키만 지정하면 됩니다.

## <a name="sessions"></a>세션

Azure Cosmos DB는 아직 서버 쪽 세션 명령을 지원하지 않습니다.

## <a name="time-to-live-ttl"></a>TTL(Time to Live)

Azure Cosmos DB는 문서의 타임스탬프를 기반으로 TTL(Time to Live)을 지원합니다. [Azure Portal](https://portal.azure.com)로 이동하여 컬렉션에 TTL을 사용하도록 설정할 수 있습니다.

## <a name="transactions"></a>트랜잭션

다중 문서 트랜잭션은 분할되지 않은 컬렉션 내에서 지원됩니다. 다중 문서 트랜잭션은 컬렉션 전체에서 또는 분할된 컬렉션에서 지원되지 않습니다. 트랜잭션의 제한 시간은 5초로 고정되어 있습니다.

## <a name="user-and-role-management"></a>사용자 및 역할 관리

Azure Cosmos DB는 아직 사용자 및 역할을 지원하지 않습니다. 그러나 Cosmos DB는 [Azure Portal](https://portal.azure.com)(연결 문자열 페이지)을 통해 가져올 수 있는 Azure RBAC(Azure 역할 기반 액세스 제어)와 읽기-쓰기 및 읽기 전용 암호/키를 지원합니다.

## <a name="write-concern"></a>쓰기 문제

일부 애플리케이션은 쓰기 작업 중에 필요한 응답 수를 지정하는 [쓰기 문제](https://docs.mongodb.com/manual/reference/write-concern/)를 사용합니다. Cosmos DB가 백그라운드에서 복제를 처리하는 방식 때문에 모든 쓰기는 자동으로 쿼럼(기본값)입니다. 클라이언트 코드를 통해 지정된 모든 쓰기 문제는 무시됩니다. [일관성 수준을 사용하여 가용성 및 성능 최대화](../consistency-levels.md)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](connect-using-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](connect-using-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](nodejs-console-app.md)을 살펴봅니다.
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
