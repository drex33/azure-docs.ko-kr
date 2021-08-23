---
title: Azure Cosmos DB 서비스 할당량
description: 여러 리소스 유형의 Azure Cosmos DB 서비스 할당량 및 기본 제한입니다.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: a33b7fb26b06b9a89b7e4f16699441fd9a5bbd0b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566869"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 서비스 할당량

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 Azure Cosmos DB의 다른 리소스에 제공되는 기본 할당량에 대한 개요를 제공합니다.

## <a name="storage-and-database-operations"></a>스토리지 및 데이터베이스 작업

구독 아래에 Azure Cosmos 계정을 만든 후에는 [데이터베이스, 컨테이너 및 항목](account-databases-containers-items.md)을 만들어 계정의 데이터를 관리할 수 있습니다.

### <a name="provisioned-throughput"></a>프로비전된 처리량

[요청 단위(RU/s 또는 RU)](request-units.md)를 기준으로 컨테이너 수준 또는 데이터베이스 수준에서 처리량을 프로비저닝할 수 있습니다. 다음 표에는 컨테이너/데이터베이스당 스토리지 및 처리량 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 컨테이너당 최대 RU([전용 처리량 프로비저닝 모드](account-databases-containers-items.md#azure-cosmos-containers)) | 기본값은 1,000,000입니다. [Azure 지원 티켓을 작성](create-support-request-quota-increase.md)하여 늘릴 수 있습니다. |
| 데이터베이스당 최대 RU([공유 처리량 프로비저닝 모드](account-databases-containers-items.md#azure-cosmos-containers)) | 기본값은 1,000,000입니다. [Azure 지원 티켓을 작성](create-support-request-quota-increase.md)하여 늘릴 수 있습니다. |
| 파티션당 최대 RU(논리적 & 실제) | 10000 |
| (논리적) 파티션당 모든 항목의 최대 스토리지 | 20GB |
| 고유한(논리적) 파티션 키의 최대 수 | 제한 없음 |
| 컨테이너당 최대 스토리지 | 제한 없음 |
| 데이터베이스당 최대 스토리지 | 제한 없음 |
| 계정당 최대 첨부 파일 크기(첨부 기능은 사용되지 않음) | 2GB |
| 1GB당 최소 RU/s | 10RU/s<br>**참고:** 계정이 ["높은 스토리지/낮은 처리량" 프로그램](set-throughput.md#high-storage-low-throughput-program)에 적합한 경우 이 최소값은 낮출 수 있습니다. |

> [!NOTE]
> 파티션 키의 스토리지 또는 처리량 제한을 높여야 하는 워크로드 관리에 대한 모범 사례는 [가상 파티션 키 만들기](synthetic-partition-keys.md)를 참조하세요.

### <a name="minimum-throughput-limits"></a>최소 처리량 제한

Cosmos 컨테이너(또는 공유 처리량 데이터베이스)의 처리량이 400RU/s 이상이어야 합니다. 컨테이너가 확장됨에 따라 Cosmos DB는 데이터베이스 또는 컨테이너가 작업에 충분한 리소스를 갖도록 하기 위해 최소 처리량이 필요합니다.

컨테이너 또는 데이터베이스의 현재 및 최소 처리량은 Azure Portal 또는 SDK에서 검색할 수 있습니다. 자세한 내용은 [컨테이너 및 데이터베이스의 처리량 프로비저닝](set-throughput.md)을 참조하세요. 

실제 최소 RU/s는 계정 구성에 따라 다를 수 있습니다. [Azure Monitor 메트릭](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db)을 사용하여 리소스에서 프로비저닝된 처리량(RU/s) 및 스토리지의 기록을 볼 수 있습니다. 

#### <a name="minimum-throughput-on-container"></a>컨테이너의 최소 처리량 

수동 처리량이 포함된 컨테이너에 필요한 최소 처리량을 예측하려면 다음의 최댓값을 찾습니다.

* 400RU/s 
* 현재 스토리지(GB) * 10RU/s
* 컨테이너에서 프로비저닝된 최고 RU/s / 100

예: 400RU/s, 0GB 스토리지로 프로비저닝된 컨테이너가 있다고 가정합니다. 처리량을 50000RU/s까지 늘리고 20GB의 데이터를 가져옵니다. 이제 최소 RU/s는 `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500RU/s입니다. 시간이 지남에 따라 스토리지는 200GB로 확장됩니다. 이제 최소 RU/s는 `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000RU/s입니다. 

**참고:** 계정이 ["높은 스토리지/낮은 처리량" 프로그램](set-throughput.md#high-storage-low-throughput-program)에 적합한 경우 스토리지의 GB당 최소 처리량인 10RU/s를 낮출 수 있습니다.

#### <a name="minimum-throughput-on-shared-throughput-database"></a>공유 처리량 데이터베이스의 최소 처리량 
수동 처리량을 포함하는 공유 처리량 데이터베이스에 필요한 최소 처리량을 예측하려면 다음의 최대값을 찾습니다.

* 400RU/s 
* 현재 스토리지(GB) * 10RU/s
* 데이터베이스에 프로비저닝된 최고 RU/s / 100
* 400 + MAX(컨테이너 수 - 25, 0) * 100 RU/s

예제: 400RU/s, 15GB 스토리지 및 10개의 컨테이너로 프로비저닝된 데이터베이스가 있다고 가정합니다. 이제 최소 RU/s는 `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400RU/s입니다. 데이터베이스에 30개 컨테이너가 있는 경우 최소 RU/s는 `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 RU/s입니다. 

**참고:** 계정이 ["높은 스토리지/낮은 처리량" 프로그램](set-throughput.md#high-storage-low-throughput-program)에 적합한 경우 스토리지의 GB당 최소 처리량인 10RU/s를 낮출 수 있습니다.

요약하자면, 최소 프로비저닝 RU 제한은 다음과 같습니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 컨테이너당 최소 RU([전용 처리량 프로비저닝 모드](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 데이터베이스당 최소 RU([공유 처리량 프로비저닝 모드](./account-databases-containers-items.md#azure-cosmos-containers)) | 처음 25개 컨테이너에 대해 400RU/s. 이후 각 컨테이너에 대해 100RU/s를 추가합니다. |

Cosmos DB는 SDK 또는 포털을 통해 컨테이너 또는 데이터베이스당 처리량(RU/s)을 프로그래밍 방식 크기 조정을 지원합니다.    

현재 프로비저닝된 RU/s 및 리소스 설정에 따라 각 리소스를 즉시 동기적으로 최소 RU/s에서 최소 RU/s의 100배까지 크기를 조정할 수 있습니다. 요청된 처리량 값이 범위를 벗어나면 스케일링이 비동기적으로 수행됩니다. 요청된 처리량과 컨테이너의 데이터 스토리지 크기에 따라 비동기 스케일링을 완료하는 데 몇 분에서 몇 시간이 걸릴 수 있습니다.  

### <a name="serverless"></a>서버를 사용하지 않음

[서버리스](serverless.md)를 사용하면 소비 기반 방식으로 Azure Cosmos DB 리소스를 사용할 수 있습니다. 다음 표에는 컨테이너/데이터베이스당 스토리지 및 처리량 한도에 대한 제한이 나열되어 있습니다.

| 리소스 | 제한 |
| --- | --- |
| 컨테이너당 최대 RU/s | 5,000 |
| (논리적) 파티션당 모든 항목의 최대 스토리지 | 20GB |
| 고유한(논리적) 파티션 키의 최대 수 | 제한 없음 |
| 컨테이너당 최대 스토리지 | 50GB |

## <a name="control-plane-operations"></a>컨트롤 플레인 작업

Azure Portal, Azure PowerShell, Azure CLI 및 Azure Resource Manager 템플릿을 사용하여 [Azure Cosmos 계정을 프로비저닝 및 관리](how-to-manage-database-account.md)할 수 있습니다. 다음 표에는 구독, 계정 및 작업 수당 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 구독당 최대 데이터베이스 계정 수 | 기본값은 50입니다. [Azure 지원 티켓을 작성](create-support-request-quota-increase.md)하여 늘릴 수 있습니다.|
| 최대 지역 장애 조치(failover) 수 | 기본적으로 시간당 1개입니다. [Azure 지원 티켓을 작성](create-support-request-quota-increase.md)하여 늘릴 수 있습니다.|

> [!NOTE]
> 지역 장애 조치(failover)는 단일 지역 쓰기 계정에만 적용됩니다. 다중 지역 쓰기 계정은 쓰기 지역을 변경할 필요가 없거나 제한이 없습니다.

Cosmos DB는 자동으로 데이터를 정기적으로 백업합니다. 백업 보존 간격 및 기간에 대한 자세한 내용은 [Azure Cosmos DB에서 온라인 백업 및 주문형 데이터 복원](online-backup-and-restore.md)을 참조하세요.

## <a name="per-account-limits"></a>계정당 제한

### <a name="provisioned-throughput"></a>프로비전된 처리량

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 데이터베이스 수 | 제한 없음 |
| 처리량을 공유하는 데이터베이스당 최대 컨테이너 수 |25 |
| 전용 처리량을 사용하는 데이터베이스 또는 계정당 최대 컨테이너 수  |무제한 |
| 최대 지역 수 | 제한 없음(모든 Azure 지역) |

### <a name="serverless"></a>서버를 사용하지 않음

| 리소스 | 제한 |
| --- | --- |
| 최대 데이터베이스 수 | 제한 없음 |
| 계정당 컨테이너 수  | 100 |
| 최대 지역 수 | 1(모든 Azure 지역) |

## <a name="per-container-limits"></a>컨테이너당 제한

사용하는 API에 따라 Azure Cosmos 컨테이너가 컬렉션, 테이블 또는 그래프를 나타낼 수 있습니다. 컨테이너는 [고유 키 제약 조건](unique-keys.md), [저장 프로시저, 트리거 및 UDF](stored-procedures-triggers-udfs.md)와 [인덱싱 정책](how-to-manage-indexing-policy.md)에 대한 구성을 지원합니다. 다음 표에는 컨테이너 내 구성과 관련된 제한이 나열되어 있습니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 데이터베이스 또는 컨테이너 이름의 최대 길이 | 255 |
| 컨테이너당 최대 저장 프로시저 수 | 100 <sup>*</sup>|
| 컨테이너당 최대 UDF 수 | 50 <sup>*</sup>|
| 인덱싱 정책의 최대 경로 수| 100 <sup>*</sup>|
| 컨테이너당 최대 고유 키 수|10 <sup>*</sup>|
| 고유 키 제약 조건당 최대 경로 수|16 <sup>*</sup>|
| 최대 TTL 값 |2147483647|

<sup>*</sup> [Azure 지원 요청](create-support-request-quota-increase.md)을 만들어 이러한 컨테이너당 제한을 늘릴 수 있습니다.

## <a name="per-item-limits"></a>항목당 제한

사용하는 API에 따라 Azure Cosmos 항목은 컬렉션의 문서, 테이블의 행 또는 그래프의 노드나 에지를 나타낼 수 있습니다. 다음 표는 Cosmos DB의 항목당 제한을 보여줍니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 항목의 최대 크기 | 2MB(JSON 표현의 UTF-8 길이) |
| 파티션 키 값의 최대 길이 | 2048바이트 |
| ID 값의 최대 길이 | 1023바이트 |
| 항목당 최대 속성 수 | 실질적인 제한 없음 |
| 속성 이름의 최대 길이 | 실질적인 제한 없음 |
| 속성 값의 최대 길이 | 실질적인 제한 없음 |
| 문자열 속성 값의 최대 길이 | 실질적인 제한 없음 |
| 숫자 속성 값의 최대 길이 | IEEE754 이중 정밀도 64비트 |
| 포함된 개체/배열의 최대 중첩 수준 | 128 |
| 최대 TTL 값 |2147483647|

파티션 키와 ID 값의 길이 제한 및 전체 크기 2MB 제한을 제외하고, 속성 수나 중첩 깊이와 같은 항목 페이로드에 대한 제한은 없습니다. RU 사용을 줄이기 위해 항목 구조가 거대하거나 복잡한 컨테이너의 인덱싱 정책을 구성해야 할 수도 있습니다. 실제 예제와 대형 항목을 관리하는 패턴은 [Cosmos DB의 항목 모델링](how-to-model-partition-example.md)을 참조하세요.

## <a name="per-request-limits"></a>요청당 제한

Azure Cosmos DB는 컨테이너, 항목, 데이터베이스 등의 리소스에 대한 [CRUD 및 쿼리 작업](/rest/api/cosmos-db/)을 지원합니다. 또한 컨테이너에 있는 파티션 키가 동일한 여러 항목에 대해 [트랜잭션 일괄 처리 요청](/dotnet/api/microsoft.azure.cosmos.transactionalbatch)을 지원합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 단일 작업의 최대 실행 시간(예: 저장 프로시저 실행 또는 단일 쿼리 페이지 검색)| 5초 |
| 최대 요청 크기(예: 저장 프로시저, CRUD)| 2MB |
| 최대 응답 크기(예: 페이지를 매긴 쿼리) | 4MB |
| 트랜잭션 일괄 처리의 최대 작업 수 | 100 |

쿼리와 같은 작업은 실행 시간 제한 또는 응답 크기 제한에 도달하면 실행을 다시 시작하기 위해 결과 페이지와 연속 토큰을 클라이언트에 반환합니다. 단일 쿼리가 페이지/연속 작업에서 실행되는 시간에 대한 실질적인 제한은 없습니다.

Cosmos DB는 권한 부여에 HMAC를 사용합니다. 주 키 또는 [리소스 토큰](secure-access-to-data.md)을 사용하여 컨테이너, 파티션 키 또는 항목과 같은 리소스에 대한 액세스 권한을 정교하게 제어할 수 있습니다. 다음 표에는 Cosmos DB의 권한 부여 토큰에 대한 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 주 토큰 만료 시간 | 15분  |
| 최소 리소스 토큰 만료 시간 | 10분  |
| 최대 리소스 토큰 만료 시간 | 기본값은 24시간입니다. [Azure 지원 티켓을 작성](create-support-request-quota-increase.md)하여 늘릴 수 있습니다.|
| 토큰 권한 부여의 최대 클럭 스큐| 15분 |

Cosmos DB는 쓰기 중에 트리거 실행을 지원합니다. 이 서비스는 쓰기 작업당 최대 하나의 사전 트리거 및 하나의 사후 트리거를 지원합니다.

## <a name="metadata-request-limits"></a>메타데이터 요청 제한

Azure Cosmos DB는 각 계정에 대한 시스템 메타데이터를 유지 관리합니다. 이 메타데이터를 사용하면 컬렉션, 데이터베이스, 기타 Azure Cosmos DB 리소스 및 해당 구성을 무료로 열거할 수 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
|분당 최대 컬렉션 생성률|    100|
|분당 최대 데이터베이스 생성률|    100|
|분당 최대 프로비저닝된 처리량 업데이트 속도|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>자동 스케일링 프로비저닝 처리량 제한

자동 스케일링을 사용하는 처리량 및 스토리지 제한에 대한 자세한 설명은 [자동 스케일링](provision-throughput-autoscale.md#autoscale-limits) 문서와 [FAQ](autoscale-faq.yml#lowering-the-max-ru-s)를 참조하세요.

| 리소스 | 기본 제한 |
| --- | --- |
| 시스템에서 스케일링할 수 있는 최대 RU/s |  `Tmax` - 사용자가 설정한 자동 스케일링 최대 RU/s|
| 시스템에서 스케일링할 수 있는 최소 RU/s | `0.1 * Tmax`|
| 시스템이 현재 스케일링된 RU/s  |  `0.1*Tmax <= T <= Tmax` - 사용량 기준|
| 시간당 최소 청구 가능 RU/s| `0.1 * Tmax` <br></br>요금은 시간 단위로 청구되며, 시스템에서 해당 시간에 스케일링한 최대 RU/s 또는 `0.1*Tmax` 중 높은 금액이 적용됩니다. |
| 컨테이너의 최소 자동 스케일링 최대 RU/s  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` - 가장 가까운 1000 RU/s로 반올림 |
| 데이터베이스의 최소 자동 스케일링 최대 RU/s  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` - 가장 가까운 1000 RU/s로 반올림 <br></br>데이터베이스에 포함된 컨테이너가 25개를 초과하면 시스템에서는 추가 컨테이너마다 최소 자동 스케일링 최대 RU/s를 1000RU/s씩 늘립니다. 예를 들어 컨테이너가 30개 있으면 설정 가능한 가장 낮은 자동 스케일링 최대 RU/s는 9000RU/s입니다(900-9000RU/s 사이에서 스케일링).

## <a name="sql-query-limits"></a>SQL 쿼리 제한

Cosmos DB는 [SQL](./sql-query-getting-started.md)을 사용하여 항목을 쿼리할 수 있습니다. 다음 표에서는 절 수 또는 쿼리 길이와 같은 쿼리 문의 제한에 대해 설명합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| SQL 쿼리의 최대 길이| 256 KB |
| 쿼리당 최대 조인 수| 5 <sup>*</sup>|
| 쿼리당 최대 UDF 수| 10 <sup>*</sup>|
| 다각형당 최대 포인트 수| 4096 |
| 컨테이너당 최대 포함 경로| 500 |
| 컨테이너당 최대 제외 경로| 500 |
| 복합 인덱스의 최대 속성 수| 8 |

<sup>*</sup> [Azure 지원 요청](create-support-request-quota-increase.md)을 만들어 이러한 SQL 쿼리 제한을 늘릴 수 있습니다.

## <a name="mongodb-api-specific-limits"></a>MongoDB API 관련 제한

Cosmos DB는 MongoDB에 대해 작성된 애플리케이션에 MongoDB 유선 프로토콜을 지원합니다. 지원되는 명령 및 프로토콜 버전은 [지원되는 MongoDB 기능 및 구문](mongodb/feature-support-32.md)에서 찾을 수 있습니다.

다음 표에는 MongoDB 기능 지원과 관련된 제한이 나열되어 있습니다. SQL(코어) API에 대해 언급한 다른 서비스 제한도 MongoDB API에 적용됩니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 MongoDB 쿼리 메모리 크기(이 제한은 3.2 서버 버전에만 적용) | 40MB |
| MongoDB 작업에 대한 최대 실행 시간(3.2 서버 버전의 경우)| 15초|
| MongoDB 작업에 대한 최대 실행 시간(3.6 및 4.0 서버 버전의 경우)| 60초|
| 인덱스 정의에 포함된 개체/배열의 최대 중첩 수준 | 6 |
| 서버 쪽 연결을 종료하는 유휴 연결 시간 제한* | 30분 |

\* [Azure LoadBalancer의 기본 시간 제한은 4분](../load-balancer/load-balancer-tcp-idle-timeout.md)이므로 클라이언트 애플리케이션의 드라이버 설정에서 유휴 연결 시간 제한을 2-3분으로 설정하는 것이 좋습니다.  이 시간 제한은 클라이언트 머신과 Azure Cosmos DB 사이에 있는 중간 부하 분산 장치가 유휴 연결을 종료하지 않게 합니다.

## <a name="try-cosmos-db-free-limits"></a>Cosmos DB 평가판의 제한

다음 표에는 [Azure Cosmos DB 평가판](https://azure.microsoft.com/try/cosmosdb/)의 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 평가판 기간 | 30일(만료 후 새 평가판을 요청할 수 있음) <br> 저장된 정보는 만료 후 삭제됩니다. |
| 구독당 최대 컨테이너 수(SQL, Gremlin, Table API) | 1 |
| 구독당 최대 컨테이너 수(MongoDB API) | 3 |
| 컨테이너당 최대 처리량 | 5,000 |
| 공유 처리량 데이터베이스당 최대 처리량 | 20000 |
| 계정당 최대 총 스토리지 | 10 GB |

Cosmos DB 평가판은 미국 중부, 북유럽 및 동남 아시아 지역에서만 글로벌 배포판을 지원합니다. Azure Cosmos DB 평가판 계정에 대한 Azure 지원 티켓을 만들 수 없습니다. 그러나 기존 지원 계획이 있는 구독자에게는 지원이 제공됩니다.

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Azure Cosmos DB 무료 계층 계정 제한

다음 표에는 [Azure Cosmos DB 평가판 계정](optimize-dev-test.md#azure-cosmos-db-free-tier)의 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| Azure 구독당 평가판 계정 수 | 1 |
| 평가판 계층 할인 기간 | 계정의 수명입니다. 계정을 만드는 동안 옵트인해야 합니다. |
| 최대 무료 RU/s | 1000RU/s |
| 최대 무료 스토리지 | 25GB |
| 최대 공유 처리량 데이터베이스 수 | 5 |
| 공유 처리량 데이터베이스의 최대 컨테이너 수 | 25 <br>평가판 계정에서 25개 이하의 컨테이너를 포함하고 있는 공유 처리량 데이터베이스의 최소 RU/s는 400RU/s입니다. |

위의 제한 외에도, [계정당 제한](#per-account-limits)이 평가판 계정에 적용됩니다. 자세히 알아보려면 [무료 계층 계정](free-tier.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Cosmos DB의 핵심 개념인 [글로벌 배포](distribute-data-globally.md), [분할](partitioning-overview.md) 및 [프로비저닝된 처리량](request-units.md)에 대해 자세히 알아보세요.

다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

* [Azure Cosmos DB SQL API 시작](create-sql-api-dotnet.md)
* [Azure Cosmos DB의 MongoDB API 시작](mongodb/create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 시작](cassandra/manage-data-dotnet.md)
* [Azure Cosmos DB Gremlin API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB Table API 시작](table/create-table-dotnet.md)

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)
