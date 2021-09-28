---
title: Azure Spring Cloud의 메트릭
description: Azure Spring Cloud의 메트릭을 검토하는 방법 알아보기
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 26d95493042d259029bc6a9428b016bbceb5681b
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154245"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure Spring Cloud의 메트릭

Azure 메트릭 탐색기는 Microsoft Azure Portal의 구성 요소이며 차트를 그리고, 추세의 상관 관계를 시각적으로 지정하고, 메트릭에서 급증 및 하락을 조사할 수 있습니다. 메트릭 탐색기를 사용하여 리소스의 상태 및 사용률을 조사합니다.

Azure Spring Cloud에서는 메트릭에 대해 두 가지 뷰포인트 있습니다.
* 각 애플리케이션 개요 페이지의 차트
* 공통 메트릭 페이지

 ![메트릭 차트](media/metrics/metrics-1.png)

애플리케이션 **개요** 의 차트는 각 애플리케이션에 대한 빠른 상태 검사를 제공합니다. 공용 **메트릭** 페이지에는 참조에 사용할 수 있는 모든 메트릭이 포함되어 있습니다. 공통 메트릭 페이지에서 사용자 고유의 차트를 빌드하고 대시보드에 고정할 수 있습니다.

## <a name="application-overview-page"></a>애플리케이션 개요 페이지

개요 페이지에서 차트를 찾으려면 **앱** 에서 앱을 선택합니다.

![애플리케이션 메트릭 관리](media/metrics/metrics-2.png)

각 애플리케이션의 **애플리케이션 개요** 페이지에는 애플리케이션에 대한 빠른 상태 검사를 수행할 수 있는 메트릭 차트가 표시됩니다.

![애플리케이션 메트릭 개요](media/metrics/metrics-3.png)

Azure Spring Cloud는 1분마다 업데이트되는 메트릭을 사용하여 다음과 같은 5가지 차트를 제공합니다.

* **HTTP 서버 오류**: 앱에 대한 HTTP 요청의 오류 수
* **데이터 입력**: 앱에서 받은 바이트
* **데이터 출력**: 앱에서 보낸 바이트 수
* **요청**: 앱에서 받은 요청
* **평균 응답 시간**: 앱의 평균 응답 시간

차트의 경우 1시간에서 7일 사이의 시간 범위를 선택할 수 있습니다.

## <a name="common-metrics-page"></a>공통 메트릭 페이지

왼쪽 탐색 창의 **메트릭은** 공통 메트릭 페이지에 연결됩니다.

먼저, 보려는 메트릭을 선택합니다.

![메트릭 보기 선택](media/metrics/metrics-4.png)

모든 메트릭 옵션에 대한 세부 정보는 아래 [섹션](#user-metrics-options) 에서 찾을 수 있습니다.

다음으로 각 메트릭에 대한 집계 유형을 선택합니다.

![메트릭 집계](media/metrics/metrics-5.png)

집계 유형은 시간별로 차트에서 메트릭 점수를 집계하는 방법을 나타냅니다. 1분마다 하나의 원시 메트릭 지점이 있으며 1분 내에 사전 집계 형식은 메트릭 형식에 의해 미리 정의됩니다.

* 합계: 모든 메트릭을 대상 출력으로 더합니다.
* 평균: 기간의 평균 값을 대상 출력으로 사용합니다.
* 최대/최소: 기간의 최댓값/최솟값을 대상 출력으로 사용합니다.

시간 범위는 지난 30분에서 지난 30일 또는 사용자 지정 시간 범위로 조정할 수도 있습니다.

![메트릭 수정](media/metrics/metrics-6.png)

기본 보기에는 모든 Azure Spring Cloud 서비스의 애플리케이션 메트릭이 포함됩니다. 한 애플리케이션 또는 인스턴스의 메트릭을 필터링하여 표시할 수 있습니다. **필터 추가** 를 선택하고 속성을 **앱** 으로 설정한 다음, **값** 텍스트 상자에서 모니터링하려는 대상 애플리케이션을 선택합니다.

두 가지 종류의 필터(속성)를 사용할 수 있습니다.

* 앱: 앱 이름으로 필터링
* 인스턴스: 앱 인스턴스로 필터링

![메트릭 필터](media/metrics/metrics-7.png)

한 앱에 대해 여러 개의 선을 그리는 **분할 적용** 옵션을 사용할 수도 있습니다.

![메트릭 분할](media/metrics/metrics-8.png)

>[!TIP]
> 메트릭 페이지에서 사용자 고유의 차트를 빌드하고 **대시보드** 에 고정할 수 있습니다. 먼저 차트 이름을 지정합니다.  다음으로, **오른쪽 위 모서리에서 대시보드에 고정** 을 선택합니다. 이제 포털 **대시보드** 에서 애플리케이션을 확인할 수 있습니다.

## <a name="user-metrics-options"></a>사용자 메트릭 옵션

다음 표에서는 사용 가능한 메트릭 및 세부 정보를 보여 줍니다.

### <a name="error"></a>오류

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring Actuator 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>| tomcat.global.error | tomcat.global.error | 개수 | 처리된 요청에서 발생한 오류 수 |

### <a name="performance"></a>성능

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring Actuator 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>| system.cpu.usage | system.cpu.usage | 백분율 | 전체 시스템에 대한 최근 CPU 사용량(사용되지 않으며 사용하지 않는 것이 좋습니다.) 이 값은 [0.0,1.0] 간격의 double입니다. 0\.0 값은 최근 관찰된 기간 동안 모든 CPU가 유휴 상태였음을 의미하고, 1.0 값은 최근 관찰된 기간 동안 모든 CPU가 100% 실행 중이었음을 의미합니다.|
>| process.cpu.usage | 앱 CPU 사용 백분율 | 백분율 | Java Virtual Machine 프로세스에 대한 최근 CPU 사용량(사용되지 않으며 사용하지 않는 것이 좋습니다.) 이 값은 [0.0,1.0] 간격의 double입니다. 0\.0 값은 최근 관찰된 기간 동안 JVM 프로세스의 스레드를 실행하는 CPU가 없었음을 의미하고, 1.0 값은 최근 관찰된 기간 동안 모든 CPU가 JVM의 스레드를 100% 실행 중이었음을 의미합니다. JVM의 스레드는 애플리케이션 스레드와 JVM 내부 스레드를 포함합니다.|
>| 앱 CPU 사용량 | | 백분율 | 이 앱에 할당된 CPU 대비 JVM 프로세스의 최근 CPU 사용량입니다. 이 값은 [0.0,1.0] 간격의 double입니다. 0\.0 값은 최근 관찰된 기간 동안 JVM 프로세스의 스레드를 실행하는 CPU가 없었음을 의미하고, 1.0 값은 최근 관찰된 기간 동안 모든 CPU가 JVM의 스레드를 100% 실행 중이었음을 의미합니다. JVM의 스레드는 애플리케이션 스레드와 JVM 내부 스레드를 포함합니다.|
>| 앱 CPU 사용량(사용되지 않음) | | 백분율 | 사용되지 않는 앱 CPU 사용량 메트릭입니다. 새로운 앱 CPU 사용량 메트릭을 대신 사용합니다.|
>| 앱 메모리 사용량 | | 백분율 | 이 앱에 할당된 메모리 대비 JVM 프로세스의 최근 메모리 사용량입니다. 이 값은 [0.0,1.0] 간격의 double입니다. 0\.0 값은 최근 관찰된 기간 동안 JVM 프로세스의 스레드에서 할당된 메모리가 없었음을 의미하고, 1.0 값은 최근 관찰된 기간 동안 모든 메모리가 JVM의 스레드에서 100% 할당되었음을 의미합니다. JVM의 스레드는 애플리케이션 스레드와 JVM 내부 스레드를 포함합니다.|
>| jvm.memory.committed | jvm.memory.committed | 바이트 | JVM에서 사용할 수 있도록 보장되는 메모리의 양을 나타냅니다. JVM은 시스템에 메모리를 릴리스할 수 있으며, 전용 메모리는 init보다 낮을 수 있습니다. 전용 메모리는 항상 사용되는 수치보다 크거나 같습니다. |
>| jvm.memory.used | jvm.memory.used | 바이트 | 현재 사용중인 메모리의 양(바이트)을 나타냅니다. |
>| jvm.memory.max | jvm.memory.max | 바이트 | 메모리 관리에 사용할 수 있는 최대 메모리 양을 나타냅니다. 최대 값이 정의된 경우 사용되는 메모리와 커밋된 메모리의 양은 항상 최대 메모리보다 작거나 같습니다. 사용된 메모리가 최대 메모리보다 작거나 같은 경우에도 사용된 메모리가 커밋된 메모리보다 크도록 사용된 메모리를 늘리려고 하면 메모리 할당이 실패할 수 있습니다(예: 시스템의 버추얼 메모리가 낮은 경우). |
>| jvm.gc.max.data.size | jvm.gc.max.data.size | 바이트 | Java 가상 머신이 시작된 이후의 이전 세대 메모리 풀의 최대 메모리 사용량입니다. |
>| jvm.gc.live.data.size | jvm.gc.live.data.size | 바이트 | 전체 GC 후의 이전 세대 메모리 풀 크기입니다. |
>| jvm.gc.memory.promoted | jvm.gc.memory.promoted | 바이트 | GC 전에서 GC 이후에 이전 세대 메모리 풀의 크기가 증가한 수입니다. |
>| jvm.gc.memory.allocated | jvm.gc.memory.allocated | 바이트 | 한 GC 이후부터 그 다음 GC 전까지 젊은 생성메모리 풀 크기가 커진 만큼 증가한 수입니다. |
>| jvm.gc.pause.total.count | jvm.gc.pause (total-count) | 개수 | 젊은 GC와 이전 GC를 포함하여 이 JMV가 시작된 이후의 총 GC 수입니다. |
>| jvm.gc.pause.total.time | jvm.gc.pause (total-time) | 밀리초 | 젊은 GC와 이전 GC를 포함하여 이 JMV가 시작된 이후 사용된 총 GC 시간입니다. |

### <a name="performance-net"></a>성능(.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring Actuator 메트릭 이름 | 단위 | 세부 정보 |
>|------|-----------------------------|------|---------|
>| CPU 사용량       | CPU 사용량      | 백분율      | 모든 시스템 CPU 리소스를 기준으로 한 프로세스의 CPU 사용량 백분율입니다[0-100]. |
>| 작업 집합     | 작업 집합    | 메가바이트    | 프로세스에서 사용한 작업 세트의 양입니다. |
>| GC heap size    | gc-heap-size   | 메가바이트    | 가비지 수집기에서 보고된 총 힙 크기입니다. |
>| Gen 0 GC count  | gen-0-gc-count | 개수        | 초당 0세대 가비지 수집 수입니다. |
>| Gen 1 GC count  | gen-1-gc-count | 개수        | 초당 1세대 가비지 수집 수입니다. |
>| Gen 2 GC count  | gen-2-gc-count | 개수        | 초당 2세대 가비지 수집 수입니다. |
>| Time in GC      | timein-gc      | 백분율      | 마지막 가비지 수집 이후의 가비지 수집 시간 백분율입니다. |
>| Gen 0 heap size | gen-0-size     | 바이트        | 0세대 힙 크기입니다. |
>| Gen 1 heap size | gen-1-size     | 바이트        | 1세대 힙 크기입니다. |
>| Gen 2 heap size | gen-2-size     | 바이트        | 2세대 힙 크기입니다. |
>| LOH 힙 크기   | loh-size       | 바이트        | LOB 힙 크기입니다. |
>| 할당률 | alloc-rate     | 바이트        | 초당 할당된 바이트 수입니다. |
>| Assembly count  | assembly-count | 개수        | 로드된 어셈블리 수입니다. |
>| Exception count | exception-count | 개수       | 초당 예외 수입니다. |
>| Thread pool thread count      | threadpool-thread-count              | 개수 | 스레드 풀 스레드의 최대 개수입니다. |
>| Monitor lock contention count | monitor-lock-contention-count        | 개수 | 모니터의 잠금을 시도할 때 초당 경합이 발생한 횟수입니다. |
>| Thread pool queue length      | threadpool-queue-length              | 개수 | 스레드 풀 작업 항목 큐 길이입니다. |
>| Thread pool completed items count | threadpool-completed-items-count | 개수 | 스레드 풀 완료된 작업 항목 개수입니다. |
>| Active timers count               | active-timer-count               | 개수 | 현재 활성 상태인 타이머의 수입니다. 활성 타이머는 미래의 특정 시점에 작동하도록 등록된 타이머로 아직 취소되지 않았습니다. |

자세한 내용은 [dotnet 카운터](/dotnet/core/diagnostics/dotnet-counters)를 참조하세요.

### <a name="request"></a>요청

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring Actuator 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>| tomcat.global.sent | tomcat.global.sent | 바이트 | Tomcat 웹 서버에서 보낸 데이터의 양입니다. |
>| tomcat.global.received | tomcat.global.received | 바이트 | Tomcat 웹 서버에서 받은 데이터의 양입니다. |
>| tomcat.global.request.total.count | tomcat.global.request(총 개수) | 개수 | Tomcat 웹 서버 처리된 요청의 총 개수입니다. |
>| tomcat.global.request.max | tomcat.global.request.max | 밀리초 | Tomcat 웹 서버가 요청을 처리하는 최대 시간입니다. |

### <a name="request-net"></a>요청(.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring Actuator 메트릭 이름 | 단위 | 세부 정보 |
>|------|-----------------------------|------|---------|
>| 초당 요청 | 초당 요청 | 개수 | 요청 속도. |
>| 총 요청 수 | 총 요청 수 | 개수 | 총 요청 수입니다. |
>| Current requests | current-requests | 개수 | 현재 요청 수입니다. |
>| 실패한 요청 | 실패한 요청 | 개수 | 실패한 요청 수 |

자세한 내용은 [dotnet 카운터](/dotnet/core/diagnostics/dotnet-counters)를 참조하세요.

### <a name="session"></a>세션

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring Actuator 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>| tomcat.sessions.active.max | tomcat.sessions.active.max | 개수 | 동시에 활성화된 최대 세션 수입니다. |
>| tomcat.sessions.alive.max | tomcat.sessions.alive.max | 밀리초 | 만료된 세션이 활성 상태였던 가장 긴 시간(초)입니다. |
>| tomcat.sessions.created | tomcat.sessions.created | 개수 | 생성된 세션 수입니다. |
>| tomcat.sessions.expired | tomcat.sessions.expired | 개수 | 만료된 세션 수입니다. |
>| tomcat.sessions.rejected | tomcat.sessions.rejected | 개수 | 최대 활성 세션 수에 도달하여 만들지 않은 세션 수입니다. |
>| tomcat.sessions.active.current | tomcat.sessions.active.current | 개수 | Tomcat 세션 활성 수 |

### <a name="ingress"></a>수신

>[!div class="mx-tdCol2BreakAll"]
>| 표시 이름             | Azure 메트릭 이름        | 단위           | 세부 정보                                                                                                                                                                          |
>|--------------------------|--------------------------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
>| 수신된 바이트           | IngressBytesReceived     | 바이트          | 클라이언트에서 Azure 스프링 클라우드에서 받은 바이트 수                                                                                                                   |
>| 보낸 바이트               | IngressBytesSent         | 바이트          | Azure 스프링 클라우드에서 클라이언트에 보낸 바이트 수                                                                                                                         |
>| 요청                 | IngressRequests          | 개수          | 클라이언트의 Azure 스프링 클라우드의 요청 수                                                                                                                         |
>| 실패한 요청          | IngressFailedRequests    | 개수          | 클라이언트에서 Azure 스프링 클라우드의 실패 한 요청 수                                                                                                                  |
>| 응답 상태          | IngressResponseStatus    | 개수          | Azure 스프링 클라우드에서 반환 된 HTTP 응답 상태입니다. 응답 상태 코드 배포를 추가로 범주화 하 여 2xx, 3xx, 4xx 및 5xx 범주의 응답을 표시할 수 있습니다. |
>| 응답 시간            | IngressResponseTime      | 초        | Azure 스프링 클라우드의 Http 응답 시간 반환                                                                                                                                  |
>| 처리량 (바이트/초)  | IngressBytesReceivedRate | 초당 바이트 수 | 클라이언트에서 Azure 스프링 클라우드의 초당 받은 바이트 수입니다.                                                                                                                 |
>| 처리량 (바이트/초) | IngressBytesSentRate     | 초당 바이트 수 | Azure 스프링 클라우드에서 클라이언트에 초당 보낸 바이트 수                                                                                                                       |

## <a name="next-steps"></a>다음 단계

* [빠른 시작: 로그, 메트릭 및 추적을 사용하여 Azure Spring Cloud 앱 모니터링](./quickstart-logs-metrics-tracing.md)
* [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)
* [진단 설정을 사용하여 로그 및 메트릭 분석](./diagnostic-services.md)
* [자습서: 경고 및 작업 그룹을 사용하여 Spring Cloud 리소스 모니터링](./tutorial-alerts-action-groups.md)
* [Azure Spring Cloud의 할당량 및 서비스 계획](./quotas.md)
