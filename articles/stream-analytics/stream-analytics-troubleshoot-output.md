---
title: Azure Stream Analytics 출력 문제 해결
description: 이 문서에서는 Azure Stream Analytics 작업에서 출력 연결의 문제를 해결하는 기술에 대해 설명합니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 393d710ba411a6573a4cce155abd7c1aeae06aa8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754879"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics 출력 문제 해결

이 문서에서는 Azure Stream Analytics 출력 연결과 관련된 일반적인 문제 및 출력 문제를 해결하는 방법을 설명합니다. 대부분의 문제 해결 단계에서는 Stream Analytics 작업에 대해 리소스 및 기타 진단 로그를 사용하도록 설정해야 합니다. 리소스 로그를 사용하도록 설정하지 않은 경우 [리소스 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)을 참조하세요.

## <a name="the-job-doesnt-produce-output"></a>작업에서 출력을 생성하지 않습니다.

1. 각 출력에 대해 **테스트 연결** 단추를 사용하여 출력에 대한 연결을 확인합니다.
1. **모니터** 탭에서 [모니터링 메트릭](stream-analytics-monitoring.md)을 확인합니다. 값을 집계하기 때문에 메트릭이 몇 분 동안 지연됩니다.

   * **입력 이벤트** 값이 0보다 크면 작업에서 입력 데이터를 읽을 수 있습니다. **입력 이벤트** 값이 0보다 크지 않은 경우 작업의 입력에 문제가 있습니다. 자세한 내용은 [입력 연결 문제 해결](stream-analytics-troubleshoot-input.md)을 참조하세요. 작업에 참조 데이터 입력이 있는 경우 **입력 이벤트** 메트릭을 볼 때 논리적 이름으로 분할을 적용합니다. 참조 데이터에서만 입력 이벤트가 없으면 입력 소스가 올바른 참조 데이터 세트를 가져오도록 올바르게 구성되지 않은 것일 수 있습니다.
   * **데이터 변환 오류** 가 값이 0보다 크고 증가하는 경우 데이터 변환 오류에 대한 자세한 내용은 [Azure Stream Analytics 데이터 오류](data-errors.md)를 참조하세요.
   * **런타임 오류** 가 값이 0보다 크면 작업에서 데이터를 받지만 쿼리를 처리하는 동안 오류가 생성됩니다. 오류를 찾으려면 [감사 로그](../azure-monitor/essentials/activity-log.md)로 이동한 다음, **실패** 상태를 필터링합니다.
   * **입력 이벤트** 값이 0보다 크고 **출력 이벤트** 값이 0이면 다음 명령문 중 하나가 true입니다.
      * 쿼리 처리로 제로 출력 이벤트가 발생했습니다.
      * 이벤트 또는 필드가 잘못되어 쿼리 처리 후 제로 출력이 발생할 수 있습니다.
      * 작업이 연결 또는 인증 이유로 데이터를 출력 싱크에 푸시할 수 없습니다.

   작업 로그 메시지는 쿼리 논리가 모든 이벤트를 필터링하는 경우 외에 발생하는 상황을 포함하여 추가 정보를 설명합니다. 여러 이벤트 처리에서 오류가 생성되는 경우 오류는 10분마다 집계됩니다.

## <a name="the-first-output-is-delayed"></a>첫 번째 출력이 지연됨

Stream Analytics 작업이 시작되면 입력 이벤트를 읽습니다. 그러나 특정 상황에서는 출력에 지연이 있을 수 있습니다.

temporal 쿼리 요소의 시간 값이 크면 출력이 지연의 원인이 될 수 있습니다. 큰 시간 범위에 대해 정확한 출력을 생성하기 위해, 스트리밍 작업은 가능한 한 최신 시간의 데이터를 읽어서 시간 범위를 채웁니다. 데이터는 최대 7일 전까지 사용할 수 있습니다. 처리 중인 입력 이벤트를 읽을 때까지 출력이 생성되지 않습니다. 시스템이 스트리밍 작업을 업그레이드하면 이 문제가 드러날 수 있습니다. 업그레이드를 수행하면 작업이 다시 시작됩니다. 이러한 업그레이드는 일반적으로 몇 달에 한 번 발생합니다.

Stream Analytics 쿼리를 디자인할 때 주의해야 합니다. 작업의 쿼리 구문에 있는 temporal 요소에 큰 시간 범위를 사용하면 작업이 시작되거나 재시작될 때 첫 번째 출력이 지연될 수 있습니다. 몇 시간 이상, 최대 7일이 큰 시간 범위로 간주됩니다.

이러한 종류의 첫 번째 출력 지연에 대한 한 가지 완화 방법은 데이터 분할과 같은 쿼리 병렬 처리 기법을 사용하는 것입니다. 또는 작업이 완료될 때까지 처리량을 향상시키기 위해 스트리밍 단위를 더 추가할 수 있습니다.  자세한 내용은 [Stream Analytics 작업을 만들 때 고려 사항](stream-analytics-concepts-checkpoint-replay.md)을 참조하세요.

이러한 요소는 첫 번째 출력의 적시성에 영향을 줍니다.

* 윈도우 이동 집계 사용(예: 연속, 도약 및 슬라이딩 윈도우의 GROUP BY 절):

  * 연속 또는 도약 윈도우 집계에 대한 결과는 윈도우 시간 범위의 끝에 생성됩니다.
  * 슬라이딩 윈도우는 이벤트가 슬라이딩 윈도우에 들어가거나 끝날 때 결과가 생성됩니다.
  * 1시간을 초과하는 큰 시간 범위 크기를 사용하려는 경우 도약 또는 슬라이딩 윈도우를 선택하는 것이 가장 좋습니다. 이러한 윈도우 유형을 사용하면 출력을 더 자주 볼 수 있습니다.

* temporal 조인 사용(예: DATEDIFF를 사용한 JOIN):
  * 일치 항목은 일치하는 이벤트의 양측이 도착하자마자 생성됩니다.
  * 일치 항목이 없는 데이터(LEFT OUTER JOIN)는 왼쪽의 각 이벤트에 대해 DATEDIFF 윈도우의 끝에 생성됩니다.

* temporal 분석 함수 사용(예: LIMIT DURATION이 있는 ISFIRST, LAST 및 LAG):
  * 분석 함수의 경우 이벤트마다 출력이 생성됩니다. 지연은 없습니다.

## <a name="the-output-falls-behind"></a>출력 지연

작업의 정상적인 작업 중에는 출력에 대기 시간이 더 오래 걸릴 수 있습니다. 출력이 다음과 같이 지연되면 다음 요인을 검사하여 근본 원인을 파악할 수 있습니다.

* 다운스트림 싱크의 제한 여부
* 업스트림 소스의 제한 여부
* 쿼리의 처리 논리에 계산이 많이 사용되는지 여부

출력 세부 정보를 보려면 Azure Portal에서 스트리밍 작업을 선택한 다음, **작업 다이어그램** 을 선택합니다. 각 입력에 대해 파티션당 백로그 이벤트 메트릭이 있습니다. 메트릭이 계속 증가하면 시스템 리소스에 제한이 있다는 표시입니다. 증가는 잠재적으로 출력 싱크 제한 또는 높은 CPU 사용량으로 인해 발생할 수 있습니다. 자세한 내용은 [작업 다이어그램을 사용하여 데이터 기반 디버그](stream-analytics-job-diagram-with-metrics.md)를 참조하세요.

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database 출력을 사용하여 키 위반 경고

Azure SQL 데이터베이스를 Stream Analytics 작업에 대한 출력으로 구성하면 대상 테이블에 레코드를 대량으로 삽입합니다. 일반적으로 Azure Stream Analytics는 출력 싱크에 [한 번 이상 배달](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)을 보장합니다. SQL 테이블에 고유한 제약 조건이 정의되어 있는 경우에도 여전히 SQL 출력에 대해 [정확히 한 번 배달을 얻을]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) 수 있습니다.

SQL 테이블에 고유한 키 제약 조건을 설정하면 Azure Stream Analytics에서 중복 레코드를 제거합니다. 데이터를 일괄 처리로 분할하고 단일 중복 레코드가 발견될 때까지 일괄 처리를 반복해서 삽입합니다. 분할 및 삽입 프로세스는 한 번에 하나씩 중복 항목을 무시합니다. 중복 행이 많은 스트리밍 작업의 경우 프로세스는 비효율적이며 시간이 많이 소요됩니다. 이전 시간에 대해 활동 로그에서 여러 키 위반 경고 메시지가 표시된 경우 SQL 출력이 전체 작업 성능을 저하시킬 가능성이 있습니다.

이 문제를 해결하려면 IGNORE_DUP_KEY 옵션을 사용하여 키 위반이 발생하는 [인덱스를 구성](/sql/t-sql/statements/create-index-transact-sql)합니다. 이 옵션을 사용하면 대량 삽입 중에 SQL에서 중복 값을 무시할 수 있습니다. Azure SQL Database는 단순히 오류 대신 경고 메시지를 생성합니다. 따라서 Azure Stream Analytics는 더 이상 기본 키 위반 오류를 생성하지 않습니다.

몇 가지 유형의 인덱스에서 IGNORE_DUP_KEY를 구성할 때 다음 조사 내용을 적어둡니다.

* 기본 키에서 IGNORE_DUP_KEY 또는 ALTER INDEX를 사용하는 고유한 제약 조건을 설정할 수 없습니다. 인덱스를 삭제하고 다시 만들어야 합니다.  
* 고유 인덱스에 대해 ALTER INDEX를 사용하여 IGNORE_DUP_KEY를 설정할 수 있습니다. 이 인스턴스는 PRIMARY KEY/UNIQUE 제약 조건과 다르며 CREATE INDEX 또는 INDEX 정의를 사용하여 생성됩니다.  
* 고유성을 적용할 수 없기 때문에 IGNORE_DUP_KEY 옵션은 열 저장소 인덱스에 적용되지 않습니다.

## <a name="sql-output-retry-logic"></a>SQL 출력 재시도 논리

SQL 출력을 사용하는 Stream Analytics 작업에서 첫 번째 이벤트 일괄 처리를 수신하면 다음 단계가 수행됩니다.

1. 작업은 SQL에 대한 연결을 시도합니다.
2. 작업은 대상 테이블의 스키마를 가져옵니다.
3. 작업은 대상 테이블 스키마에 대한 열 이름 및 유형의 유효성을 검사합니다.
4. 작업은 일괄 처리의 출력 레코드에서 메모리 내 데이터 테이블을 준비합니다.
5. 작업은 대량 복사 [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver)를 사용하여 SQL에 데이터 테이블을 작성합니다.

이러한 단계를 수행하는 동안 SQL 출력에 다음과 같은 유형의 오류가 발생할 수 있습니다.

* 지수 백오프 재시도 전략을 사용하여 다시 시도되는 일시적인 [오류](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others)입니다. 최소 다시 시도 간격은 개별 오류 코드에 따라 다르지만 간격은 일반적으로 60 초 미만입니다. 상한 값은 최대 5 분이 될 수 있습니다. 

   [로그인 실패](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531) 및 [방화벽 문제](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues)는 이전 시도 후 최소 5 분 후에 다시 시도하고 성공할 때까지 다시 시도됩니다.

* 캐스팅 오류 및 스키마 제약 조건 위반과 같은 데이터 오류는 출력 오류 정책을 통해 처리됩니다. 이러한 오류는 오류를 발생시키는 개별 레코드가 건너뛰기 또는 다시 시도로 처리될 때까지 이진 분할 일괄 처리를 다시 시도하여 처리됩니다. 기본 고유 키 제약 조건 위반이 [항상 처리](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)됩니다.

* SQL 서비스 문제나 내부 코드 오류가 있으면 일시적이 지 않은 오류가 발생할 수 있습니다. 예를 들어 Elastic Pool 같은 오류(코드 1132)가 스토리지 제한에 도달하는 경우 다시 시도해도 오류가 해결되지 않습니다. 이러한 시나리오에서는 Stream Analytics 작업의 [성능이 저하](job-states.md)됩니다.
* `BulkCopy` 시간 제한 발생은 `BulkCopy` 5단계에서 이루어질 수 있습니다. `BulkCopy`은 때때로 작업 시간 초과를 겪을 수 있습니다. 기본 최소 구성 제한 시간은 5분이며 연속적으로 적중될 때 두 배가 됩니다.
시간 제한이 15 분을 초과하면 일괄 처리 당 100 이벤트가 남아있을 때까지 `BulkCopy`까지의 최대 일괄 처리 크기 힌트가 절반으로 줄어듭니다.

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>열 이름은 Azure Stream Analytics의 소문자입니다(1.0).

원래 호환성 수준(1.0)을 사용하는 경우 Azure Stream Analytics는 열 이름을 소문자로 변경합니다. 이 동작은 이후 호환성 수준에서 수정되었습니다. 사례를 유지하려면 호환성 수준 1.1 이상으로 이동합니다. 자세한 내용은 [Stream Analytics 작업에 대한 호환성 수준](./stream-analytics-compatibility-level.md)을 참조하세요.

## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](/answers/topics/azure-stream-analytics.html)를 사용해보세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)