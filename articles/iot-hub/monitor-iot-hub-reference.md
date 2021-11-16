---
title: Azure IoT Hub 데이터 참조 모니터링
description: Azure IoT Hub를 모니터링할 때 필요한 중요 참조 자료
author: eross-msft
ms.author: lizross
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 569d8aecd4b90fe7ad4cbe1513829c536682f8b4
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554487"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Azure IoT Hub 데이터 참조 모니터링

Azure IoT Hub의 모니터링 데이터를 수집 및 분석하는 방법에 대한 자세한 내용은 [Azure IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요.

## <a name="metrics"></a>메트릭

이 섹션은 Azure IoT Hub에 대한 모든 자동 수집 플랫폼 메트릭을 나열합니다. IoT Hub 메트릭에 대한 리소스 공급자 네임스페이스는 **Microsoft.Devices** 이며 형식 네임스페이스는 **IoTHubs** 입니다.

다음 하위 섹션은 IoT Hub 플랫폼 메트릭을 일반적인 범주별로 분리하고 Azure Portal에 표시되는 표시 이름으로 나열합니다. 또한 각 하위 섹션에 나타나는 메트릭과 관련된 정보가 제공됩니다.

Azure Monitor 설명서의 [Microsoft. Devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs)에서 메트릭 이름별로 모든 IoT Hub 플랫폼 메트릭을 나열하는 단일 테이블도 찾을 수 있습니다. 이 표는 이 문서에서 확인할 수 있는 일부 메트릭에 대해 [지원되는 집계](#supported-aggregations)와 같은 일부 정보는 제공하지 않습니다.

다른 Azure 서비스에서 지원하는 메트릭에 대한 자세한 정보는 [Azure Monitor에서 지원되는 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

**이 섹션의 토픽**

- [Azure IoT Hub 데이터 참조 모니터링](#monitoring-azure-iot-hub-data-reference)
  - [메트릭](#metrics)
    - [지원되는 집계](#supported-aggregations)
    - [클라우드-디바이스 명령 메트릭](#cloud-to-device-command-metrics)
    - [클라우드-디바이스 직접 메서드 메트릭](#cloud-to-device-direct-methods-metrics)
    - [클라우드-디바이스 쌍 작업 메트릭](#cloud-to-device-twin-operations-metrics)
    - [구성 메트릭](#configurations-metrics)
    - [일일 할당량 메트릭](#daily-quota-metrics)
    - [디바이스 메트릭](#device-metrics)
    - [디바이스 원격 분석 메트릭](#device-telemetry-metrics)
    - [디바이스-클라우드 쌍 작업 메트릭](#device-to-cloud-twin-operations-metrics)
    - [Event Grid 메트릭](#event-grid-metrics)
    - [작업 메트릭](#jobs-metrics)
    - [라우팅 메트릭](#routing-metrics)
    - [쌍 쿼리 메트릭](#twin-query-metrics)
  - [메트릭 차원](#metric-dimensions)
  - [리소스 로그](#resource-logs)
    - [연결](#connections)
    - [디바이스 원격 분석](#device-telemetry)
    - [클라우드-디바이스 명령](#cloud-to-device-commands)
    - [디바이스 ID 작업](#device-identity-operations)
    - [파일 업로드 작업](#file-upload-operations)
    - [경로](#routes)
    - [디바이스-클라우드 쌍 작업](#device-to-cloud-twin-operations)
    - [클라우드-디바이스 쌍 작업](#cloud-to-device-twin-operations)
    - [쌍 쿼리](#twin-queries)
    - [작업 연산](#jobs-operations)
    - [직접 메서드](#direct-methods)
    - [분산 추적(미리 보기)](#distributed-tracing-preview)
      - [IoT Hub D2C(디바이스-클라우드) 로그](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT Hub 수신 로그](#iot-hub-ingress-logs)
      - [IoT Hub 송신 로그](#iot-hub-egress-logs)
    - [구성](#configurations)
    - [디바이스 스트림(미리 보기)](#device-streams-preview)
  - [Azure Monitor Logs 테이블](#azure-monitor-logs-tables)
  - [참고 항목](#see-also)

### <a name="supported-aggregations"></a>지원되는 집계

각 테이블의 **집계 유형** 열은 차트 또는 경고 메트릭을 선택할 때 사용되는 기본 집계에 해당합니다.

   ![메트릭에 대한 집계를 보여 주는 스크린샷](./media/monitor-iot-hub-reference/aggregation-type.png)

모든 집계 형식이 대부분의 메트릭에 유효합니다. 그러나 개수 메트릭은, **단위** 열 값이 **개수** 인 경우에만 일부 집계가 유효합니다. 개수 메트릭은 다음 두 가지 유형 중 하나일 수 있습니다.

* **단일 지점** 개수 메트릭의 경우 측정된 작업이 발생될 때마다 IoT Hub가 단일 데이터 요소 즉 기본적으로 1을 등록합니다. 그다음 Azure Monitor가 지정된 세분성에 대한 데이터 포인트의 합계를 계산합니다. **단일 지점** 메트릭의 예는 *전송된 원격 분석 메시지* 및 *배달 완료된 C2D 메시지* 입니다. 해당 메트릭의 경우 관련된 유일한 집계 유형은 합계(Sum)입니다. 포털에서 최소, 최대 및 평균을 선택할 수 있습니다. 그러나 해당 값은 항상 1입니다.

* **스냅샷** 개수 메트릭의 경우, 측정된 작업이 발생할 때 IoT Hub가 총 개수를 등록합니다. 현재 IoT Hub에서 내보내는 **스냅샷** 메트릭에는 *사용되는 총 메시지 수*, *총 디바이스(미리 보기)* , *연결된 디바이스(미리 보기)* 의 세 가지가 있습니다. 해당 메트릭은 내보낼 때마다 “합계” 수량을 제공하므로 지정된 세분성에 대해 합계를 합산하는 것은 의미가 없습니다. Azure Monitor는 해당 메트릭의 집계 유형에 대한 평균, 최솟값, 최댓값을 선택하는 것을 제한합니다.

### <a name="cloud-to-device-command-metrics"></a>클라우드-디바이스 명령 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|만료된 C2D 메시지(미리 보기)|C2DMessagesExpired|개수|합계|만료된 클라우드-디바이스 메시지 수|없음|
|C2D 메시지 배달 완료|c2d.commands.egress.complete.success|개수|합계|디바이스에서 성공적으로 배달 완료한 클라우드-디바이스 메시지 수|없음|
|중단된 C2D 메시지|c2d.commands.egress.abandon.success|개수|합계|디바이스에서 중단한 클라우드-디바이스 메시지 수|없음|
|거부된 C2D 메시지|c2d.commands.egress.reject.success|개수|합계|디바이스에서 거부한 클라우드-디바이스 메시지 수|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="cloud-to-device-direct-methods-metrics"></a>클라우드-디바이스 직접 메서드 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|실패한 직접 메서드 호출|c2d.methods.failure|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|없음|
|직접 메서드 호출의 요청 크기|c2d.methods.requestSize|바이트|평균|성공한 모든 직접 메서드 요청의 수입니다.|없음|
|직접 메서드 호출의 응답 크기|c2d.methods.responseSize|바이트|평균|성공한 모든 직접 메서드 응답의 수입니다.|없음|
|성공한 직접 메서드 호출|c2d.methods.success|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|없음|

**단위** 값이 **개수** 인 메트릭에 대해서만 합계(sum) 집계가 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="cloud-to-device-twin-operations-metrics"></a>클라우드-디바이스 쌍 작업 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백 엔드에서의 실패한 쌍 읽기|c2d.twin.read.failure|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|백 엔드에서의 실패한 쌍 업데이트|c2d.twin.update.failure|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|없음|
|백 엔드에서의 쌍 읽기 응답 크기|c2d.twin.read.size|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|백 엔드에서의 쌍 업데이트 크기|c2d.twin.update.size|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트의 총 크기입니다.|없음|
|백 엔드에서의 성공한 쌍 읽기|c2d.twin.read.success|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|백 엔드에서의 성공한 쌍 업데이트|c2d.twin.update.success|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="configurations-metrics"></a>구성 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|구성 메트릭|구성|개수|합계|대상 디바이스 집합에서 디바이스 구성 및 IoT Edge 배포에 대해 수행된 총 CRUD 작업 수입니다. 해당 구성으로 인해 CRUD 작업 수에는 디바이스 쌍 또는 모듈 쌍을 수정하는 작업의 수도 포함됩니다.|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="daily-quota-metrics"></a>일일 할당량 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|총 디바이스 데이터 사용량|deviceDataUsage|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|없음|
|총 디바이스 데이터 사용량(미리 보기)|deviceDataUsageV2|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|없음|
|사용된 전체 메시지 수|dailyMessageQuotaUsed|개수|평균|오늘 사용된 전체 메시지 수입니다. 매일 00:00 UTC에 0으로 다시 설정되는 누적 값입니다.|없음|

*사용된 총 메시지 수* 에 대해 최소, 최대, 평균 집계만 지원됩니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="device-metrics"></a>디바이스 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|총 디바이스(사용되지 않음)|devices.totalDevices|개수|합계|IoT 허브에 등록된 디바이스 수|없음|
|연결된 디바이스(사용되지 않음) |devices.connectedDevices.allProtocol|개수|합계|IoT 허브에 연결된 디바이스 수|없음|
|총 디바이스(미리 보기)|totalDeviceCount|개수|평균|IoT 허브에 등록된 디바이스 수|없음|
|연결된 디바이스(미리 보기)|connectedDeviceCount|개수|평균|IoT 허브에 연결된 디바이스 수|없음|

*총 디바이스(사용되지 않음)* 및 *연결된 디바이스(사용되지 않음*)의 경우 합계(sum)만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

*총 디바이스(미리 보기)* 및 *연결된 디바이스(미리 보기)* 의 경우 최소, 최대 및 평균 집계만 유효합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

*연결된 디바이스(미리 보기)* 및 *총 디바이스(미리 보기)* 는 진단 설정을 통해 내보낼 수 없습니다.

### <a name="device-telemetry-metrics"></a>디바이스 원격 분석 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|제한 오류 수|d2c.telemetry.ingress.sendThrottle|개수|합계|디바이스 처리량 제한으로 인한 제한 오류 수|없음|
|원격 분석 메시지 보내기 시도|d2c.telemetry.ingress.allProtocol|개수|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|없음|
|보낸 원격 분석 메시지|d2c.telemetry.ingress.success|개수|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="device-to-cloud-twin-operations-metrics"></a>디바이스-클라우드 쌍 작업 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|디바이스에서의 실패한 쌍 읽기|d2c.twin.read.failure|개수|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|없음|
|디바이스에서의 실패한 쌍 업데이트|d2c.twin.update.failure|개수|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|없음|
|디바이스에서의 쌍 읽기 응답 크기|d2c.twin.read.size|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|없음|
|디바이스에서의 쌍 업데이트 크기|d2c.twin.update.size|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 총 크기입니다.|없음|
|디바이스에서의 성공한 쌍 읽기|d2c.twin.read.success|개수|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|없음|
|디바이스에서의 성공한 쌍 업데이트|d2c.twin.update.success|개수|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="event-grid-metrics"></a>Event Grid 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|Event Grid 배달(미리 보기)|EventGridDeliveries|개수|합계|Event Grid에 게시된 IoT Hub 이벤트 수. 성공 및 실패한 요청 수에 대한 결과 차원을 사용합니다. EventType 차원은 이벤트 형식(https://aka.ms/ioteventgrid) )을 보여 줍니다.|결과,<br/>EventType<br>*자세한 내용은 [메트릭 차원](#metric-dimensions)을 참조하세요*.|
|Event Grid 대기 시간(미리 보기)|EventGridLatency|밀리초|평균|Iot Hub 이벤트가 생성된 시간부터 Event Grid에 게시된 시간까지의 평균 대기 시간(밀리초). 이 숫자는 모든 이벤트 유형의 평균입니다. 특정 이벤트 유형의 대기 시간을 확인하려면 EventType 차원을 사용합니다.|EventType<br>*자세한 내용은 [메트릭 차원](#metric-dimensions)을 참조하세요*.|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="jobs-metrics"></a>작업 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|완료된 작업|jobs.completed|개수|합계|완료된 모든 작업의 수입니다.|없음|
|목록 작업에 대한 실패한 호출|jobs.listJobs.failure|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|없음|
|실패한 메서드 호출 작업 만들기|jobs.createDirectMethodJob.failure|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|없음|
|쌍 업데이트 작업에 대한 실패한 만들기|jobs.createTwinUpdateJob.failure|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|없음|
|실패한 작업 취소|jobs.cancelJob.failure|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|없음|
|실패한 작업 쿼리|jobs.queryJobs.failure|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|없음|
|실패한 작업|jobs.failed|개수|합계|실패한 모든 작업의 수입니다.|없음|
|목록 작업에 대한 성공한 호출|jobs.listJobs.success|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|없음|
|메서드 호출 작업에 대한 성공한 만들기|jobs.createDirectMethodJob.success|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|없음|
|쌍 업데이트 작업에 대한 성공한 만들기|jobs.createTwinUpdateJob.<br>성공|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|없음|
|성공한 작업 취소|jobs.cancelJob.success|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|없음|
|성공한 작업 쿼리|jobs.queryJobs.success|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="routing-metrics"></a>라우팅 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
| 라우팅 배달 시도(미리 보기) |RoutingDeliveries | 개수 | 합계 |라우팅 배달 메트릭입니다. 차원을 사용하여 특정 엔드포인트 또는 특정 라우팅 원본의 배달 상태를 식별할 수 있습니다.| 결과,<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>EndpointName<br>*자세한 내용은 [메트릭 차원](#metric-dimensions)을 참조하세요*. |
| 라우팅 배달 데이터의 바이트 단위 크기(미리 보기)|RoutingDataSizeInBytesDelivered| 바이트 | 합계 |IoT Hub 의해 사용자 지정 엔드포인트와 기본 제공 엔드포인트에 라우팅되는 총 바이트 수입니다. 차원을 사용하여 특정 엔드포인트 또는 특정 라우팅 원본에 대해 라우팅되는 데이터 크기를 식별할 수 있습니다.| RoutingSource,<br>EndpointType<br>EndpointName<br>*자세한 내용은 [메트릭 차원](#metric-dimensions)을 참조하세요*.|
| 라우팅 대기 시간(미리 보기) |RoutingDeliveryLatency| 밀리초 | 평균 |라우팅 전달 대기 시간 메트릭입니다. 차원을 사용하여 특정 엔드포인트 또는 특정 라우팅 원본의 대기 시간을 식별합니다.| RoutingSource,<br>EndpointType,<br>EndpointName<br>*자세한 내용은 [메트릭 차원](#metric-dimensions)을 참조하세요*.|
|라우팅: 스토리지에 배달된 Blob|d2c.endpoints.egress.storage.blobs|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 Blob을 배달한 횟수입니다.|없음|
|라우팅: 스토리지에 배달된 데이터|d2c.endpoints.egress.storage.bytes|바이트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 배달된 데이터 양입니다(바이트).|없음|
|라우팅: 이벤트 허브에 대한 메시지 대기 시간|d2c.endpoints.latency.eventHubs|밀리초|평균|IoT Hub로의 메시지 수신 및 이벤트 허브 유형의 사용자 지정 엔드포인트로의 메시지 수신 사이에 걸리는 평균 대기 시간(밀리초)입니다. 여기에는 기본 제공 엔드포인트(이벤트)에 대한 메시지 경로가 포함되지 않습니다.|없음|
|라우팅: Service Bus 큐에 대한 메시지 대기 시간|d2c.endpoints.latency.serviceBusQueues|밀리초|평균|IoT Hub로의 메시지 수신 및 Service Bus 큐 엔드포인트로의 메시지 수신 사이에 걸리는 평균 대기 시간(밀리초)입니다.|없음|
|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|d2c.endpoints.latency.serviceBusTopics|밀리초|평균|IoT Hub로의 메시지 수신 및 Service Bus 토픽 엔드포인트로의 메시지 수신 사이에 걸리는 평균 대기 시간(밀리초)입니다.|없음|
|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|d2c.endpoints.latency.builtIn.events|밀리초|평균|IoT Hub로의 메시지 수신 및 기본 제공 엔드포인트(메시지/이벤트)로의 메시지 수신 사이에 걸리는 평균 대기 시간(밀리초)입니다.|없음|
|라우팅: 스토리지에 대한 메시지 대기 시간|d2c.endpoints.latency.storage|밀리초|평균|IoT Hub로의 메시지 수신 및 스토리지 엔드포인트로의 메시지 수신 사이에 걸리는 평균 대기 시간(밀리초)입니다.|없음|
|라우팅: 이벤트 허브에 배달된 메시지|d2c.endpoints.egress.eventHubs|개수|합계|IoT Hub 라우팅이 이벤트 허브 형식의 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다. 여기에는 기본 제공 엔드포인트(이벤트)에 대한 메시지 경로가 포함되지 않습니다.|없음|
|라우팅: Service Bus 큐에 배달된 메시지|d2c.endpoints.egress.serviceBusQueues|개수|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|라우팅: Service Bus 토픽에 배달된 메시지|d2c.endpoints.egress.serviceBusTopics|개수|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|라우팅: 대체에 배달된 메시지|d2c.telemetry.egress.fallback|개수|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|없음|
|라우팅: 메시지/이벤트에 배달된 메시지|d2c.endpoints.egress.builtIn.events|개수|합계|IoT Hub 라우팅이 기본 제공 엔드포인트(메시지/이벤트) 및 대체 경로에 성공적으로 메시지를 전달한 횟수입니다.|없음|
|라우팅: 스토리지에 배달된 메시지|d2c.endpoints.egress.storage|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|라우팅: 배달된 원격 분석 메시지|d2c.telemetry.egress.success|개수|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|없음|
|라우팅: 삭제된 원격 분석 메시지 |d2c.telemetry.egress.dropped|개수|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|없음|
|라우팅: 원격 분석 메시지 호환되지 않음|d2c.telemetry.egress.invalid|개수|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. Iot Hub가 엔드포인트에 메시지를 배달하려고 시도하고 일시적이지 않은 오류로 인해 실패하는 경우 메시지는 엔드포인트와 호환되지 않습니다. 잘못된 메시지는 다시 시도되지 않습니다. 이 값은 재시도를 포함하지 않습니다.|없음|
|라우팅: 분리된 원격 분석 메시지 |d2c.telemetry.egress.orphaned|개수|합계|대체 경로를 사용할 수 없는 경우 메시지가 라우팅 쿼리와 일치하지 않기 때문에 IoT Hub 라우팅이 메시지를 분리한 횟수입니다.|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

### <a name="twin-query-metrics"></a>쌍 쿼리 메트릭

|메트릭 표시 이름|메트릭|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|실패한 쌍 쿼리|twinQueries.failure|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|없음|
|성공한 쌍 쿼리|twinQueries.success|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|없음|
|쌍 쿼리 결과 크기|twinQueries.resultSize|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 합계입니다.|없음|

**단위** 값이 **개수** 인 메트릭의 경우 합계(sum) 집계만 유효합니다. 최솟값, 최댓값, 평균 집계는 항상 1을 반환합니다. 자세한 내용은 [지원되는 집계](#supported-aggregations)를 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

Azure IoT Hub에는 일부 라우팅 및 Event Grid 메트릭과 관련된 다음과 같은 차원이 있습니다.

|차원 이름 | Description|
|---|---|
||
|**EndpointName**| 엔드포인트 이름입니다.|
|**EndpointType**|**eventHubs**, **serviceBusQueues**, **cosmosDB**, **serviceBusTopics** 중 하나입니다. **builtin**, 또는 **blobStorage**.|
|**EventType**| 다음 Event Grid 이벤트 유형 중 하나입니다. **Microsoft.Devices.DeviceCreated**. **Microsoft.Devices.DeviceDeleted**, **Microsoft.Devices.DeviceConnected**, **Microsoft.Devices.DeviceDisconnected**, 또는 **Microsoft.Devices.DeviceTelemetry**. 자세한 내용은 [이벤트 형식](iot-hub-event-grid.md#event-types)을 참조하세요.|
|**FailureReasonCategory**| **invalid**, **dropped**, **orphaned**, 또는 **null** 중 하나입니다.|
|**결과**| **성공** 또는 **실패** 입니다.|
|**RoutingSource**| 디바이스 메시지<br>쌍 변경 이벤트<br>디바이스 수명 주기 이벤트|

메트릭 차원에 대한 자세한 정보는 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

## <a name="resource-logs"></a>리소스 로그

이 섹션은 Azure IoT Hub에 대해 수집된 모든 리소스 로그 범주 유형 및 스키마를 나열합니다. 모든 IoT Hub 로그의 리소스 공급자 및 유형은 [Microsoft. Devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs)입니다.

**이 섹션의 토픽**

- [Azure IoT Hub 데이터 참조 모니터링](#monitoring-azure-iot-hub-data-reference)
  - [메트릭](#metrics)
    - [지원되는 집계](#supported-aggregations)
    - [클라우드-디바이스 명령 메트릭](#cloud-to-device-command-metrics)
    - [클라우드-디바이스 직접 메서드 메트릭](#cloud-to-device-direct-methods-metrics)
    - [클라우드-디바이스 쌍 작업 메트릭](#cloud-to-device-twin-operations-metrics)
    - [구성 메트릭](#configurations-metrics)
    - [일일 할당량 메트릭](#daily-quota-metrics)
    - [디바이스 메트릭](#device-metrics)
    - [디바이스 원격 분석 메트릭](#device-telemetry-metrics)
    - [디바이스-클라우드 쌍 작업 메트릭](#device-to-cloud-twin-operations-metrics)
    - [Event Grid 메트릭](#event-grid-metrics)
    - [작업 메트릭](#jobs-metrics)
    - [라우팅 메트릭](#routing-metrics)
    - [쌍 쿼리 메트릭](#twin-query-metrics)
  - [메트릭 차원](#metric-dimensions)
  - [리소스 로그](#resource-logs)
    - [연결](#connections)
    - [디바이스 원격 분석](#device-telemetry)
    - [클라우드-디바이스 명령](#cloud-to-device-commands)
    - [디바이스 ID 작업](#device-identity-operations)
    - [파일 업로드 작업](#file-upload-operations)
    - [경로](#routes)
    - [디바이스-클라우드 쌍 작업](#device-to-cloud-twin-operations)
    - [클라우드-디바이스 쌍 작업](#cloud-to-device-twin-operations)
    - [쌍 쿼리](#twin-queries)
    - [작업 연산](#jobs-operations)
    - [직접 메서드](#direct-methods)
    - [분산 추적(미리 보기)](#distributed-tracing-preview)
      - [IoT Hub D2C(디바이스-클라우드) 로그](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT Hub 수신 로그](#iot-hub-ingress-logs)
      - [IoT Hub 송신 로그](#iot-hub-egress-logs)
    - [구성](#configurations)
    - [디바이스 스트림(미리 보기)](#device-streams-preview)
  - [Azure Monitor Logs 테이블](#azure-monitor-logs-tables)
  - [참고 항목](#see-also)

### <a name="connections"></a>Connections

연결 범주는 오류뿐 아니라 IoT Hub에서의 디바이스 연결 및 이벤트 분리를 추적합니다. 이 범주는 디바이스에 대한 연결을 분실한 경우 무단 연결 시도를 식별하고 경고하는 데 유용합니다.

> [!NOTE]
> 디바이스의 신뢰할 수 있는 연결 상태에 대해서는 [디바이스 하트비트](iot-hub-devguide-identity-registry.md#device-heartbeat)를 확인합니다.

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>디바이스 원격 분석

디바이스 원격 분석 범주는 IoT Hub에서 발생하고 원격 분석 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 원격 분석 이벤트를 보내고(예: 제한) 원격 분석 이벤트를 수신할 때(예: 무단된 판독기) 발생하는 오류가 포함됩니다. 이 범주는 디바이스 자체에서 실행되는 코드에 의해 발생한 오류를 포착할 수 없습니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>클라우드-디바이스 명령

클라우드-디바이스 명령 범주는 IoT Hub에서 발생하고 클라우드-디바이스 메시지 파이프라인에 관련된 오류를 추적합니다. 이 범주는 다음에서 발생하는 오류를 포함합니다.

* 클라우드-디바이스 메시지 보내기(예: 권한이 없는 보낸 사람 오류),
* 클라우드-디바이스 메시지 수신(예: 배달 횟수 초과 오류) 및
* 클라우드-디바이스 메시지 피드백 수신(예: 피드백 만료 오류)

이 범주는 클라우드-디바이스 메시지가 성공적으로 전달되었지만 디바이스에서 부적절하게 처리된 경우 오류를 포착하지 않습니다.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>디바이스 ID 작업

디바이스 ID 작업 범주는 IoT Hub ID 레지스트리의 항목을 만들거나, 업데이트하거나 삭제하려고 할 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 프로비전 시나리오에 유용합니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>파일 업로드 작업

파일 업로드 범주는 IoT Hub에서 발생하고 파일 업로드 기능과 관련된 오류를 추적합니다. 이 범주에는 다음이 포함됩니다.

* SAS URI에서 발생하는 오류(예: 디바이스가 허브에 완료된 업로드를 알리기 전에 만료되는 경우).

* 디바이스에 의해 보고된 실패한 업로드.

* IoT Hub 알림 메시지 생성 중 스토리지에서 파일을 찾을 수 없는 경우 발생하는 오류.

이 범주는 디바이스가 스토리지로 파일을 업로드하는 동안 직접 발생하는 오류를 검색할 수 없습니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>경로

[메시지 라우팅](./iot-hub-devguide-messages-d2c.md) 범주는 IoT Hub에서 감지하는 대로 메시지 경로 평가 중에 발생한 오류 및 엔드포인트 상태를 추적합니다. 이 범주는 다음과 같은 이벤트를 포함합니다.

* 규칙에서 "정의되지 않음"으로 평가,
* IoT Hub에서 엔드포인트를 데드로 표시 또는
* 엔드포인트에서 받은 모든 오류

이 범주는 메시지 자체에 대한 특정 오류(예: 디바이스 제한 오류)를 포함하지 않습니다. 해당 오류는 "디바이스 원격 분석" 범주 아래에서 보고됩니다.

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

라우팅 리소스 로그에 대한 자세한 내용은 다음과 같습니다.

* [라우팅 리소스 로그 오류 코드 목록](troubleshoot-message-routing.md#diagnostics-error-codes)
* [라우팅 리소스 로그 operationNames 목록](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>디바이스-클라우드 쌍 작업

클라우드-디바이스 쌍 작업 범주는 디바이스 쌍에서 디바이스가 시작한 이벤트를 추적합니다. 이러한 작업에는 쌍 가져오기, 보고된 속성 업데이트, 원하는 속성 구독이 포함될 수 있습니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>클라우드-디바이스 쌍 작업

클라우드-디바이스 쌍 작업 범주는 디바이스 쌍에서 서비스가 시작한 이벤트를 추적합니다. 이러한 작업에는 쌍 가져오기, 태그 업데이트 또는 대체, 원하는 속성 업데이트 또는 대체가 포함될 수 있습니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>쌍 쿼리

쌍 쿼리 범주는 클라우드에서 시작된 디바이스 쌍에 대한 쿼리 요청을 보고합니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>작업 연산

작업 연산 범주는 디바이스 쌍을 업데이트하는 작업 요청 또는 여러 디바이스에 대한 직접 메서드를 호출하는 작업 요청에 대해 보고합니다. 이러한 요청은 클라우드에서 시작됩니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>직접 메서드

직접 메서드 범주는 개별 디바이스로 전송되는 요청-응답 상호 작용을 추적합니다. 이러한 요청은 클라우드에서 시작됩니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>분산 추적(미리 보기)

분산 추적 범주는 추적 컨텍스트 헤더를 전달하는 메시지의 상관 관계 ID를 추적합니다. 해당 로그를 완전히 활성화하려면 [IoT Hub 분산 추적(미리 보기)을 사용한 IoT 애플리케이션 엔드투엔드 분석 및 진단](iot-hub-distributed-tracing.md)에 따라 클라이언트 쪽 코드를 업데이트해야 합니다.

`correlationId`은 `trace-id` 및 `span-id`를 포함하는 [W3C 추적 컨텍스트](https://github.com/w3c/trace-context) 제안을 준수합니다.

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C(디바이스-클라우드) 로그

IoT Hub는 유효한 추적 속성이 포함된 메시지가 IoT Hub에 도착할 때 이 로그를 기록합니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

여기서 `durationMs`는 IoT Hub 시계가 디바이스 시계와 동기화되지 않아 기간 계산이 잘못될 수 있기 때문에 계산되지 않습니다. 디바이스-클라우드 대기 시간의 급증을 캡처하려면 `properties` 섹션에서 타임스탬프를 사용한 논리를 작성하는 것이 좋습니다.

| 속성 | 형식 | 설명 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | 정수 | 디바이스-클라우드 메시지의 크기(바이트) |
| **deviceId** | ASCII 7비트 영숫자 문자의 문자열 | 디바이스의 ID |
| **callerLocalTimeUtc** | UTC 타임스탬프 | 디바이스 로컬 시계에서 보고한 메시지 생성 시간 |
| **calleeLocalTimeUtc** | UTC 타임스탬프 | IoT Hub 서비스 쪽 시계에서 메시지가 IoT Hub의 게이트웨이에 도착했다고 보고한 시간 |

#### <a name="iot-hub-ingress-logs"></a>IoT Hub 수신 로그

IoT Hub는 유효한 추적 속성이 포함된 메시지가 내부 또는 기본 제공 이벤트 허브에 작성될 때 이 로그를 기록합니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties` 섹션의 이 로그에는 메시지 수신에 대한 추가 정보가 포함됩니다.

| 속성 | 형식 | 설명 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | true 또는 false이며, IoT Hub에서 메시지 라우팅이 사용되는지 여부를 나타냅니다. |
| **parentSpanId** | String | 부모 메시지(이 경우 D2C 메시지 추적)의 [span-id](https://w3c.github.io/trace-context/#parent-id)입니다. |

#### <a name="iot-hub-egress-logs"></a>IoT Hub 송신 로그

IoT Hub는 [라우팅](iot-hub-devguide-messages-d2c.md)이 사용되고 메시지가 [엔드포인트](iot-hub-devguide-endpoints.md)에 작성될 때 이 로그를 기록합니다. 라우팅이 사용되지 않는 경우에는 IoT Hub에서 이 로그를 기록하지 않습니다.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties` 섹션의 이 로그에는 메시지 수신에 대한 추가 정보가 포함됩니다.

| 속성 | 형식 | 설명 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | 라우팅 엔드포인트의 이름입니다. |
| **endpointType** | String | 라우팅 엔드포인트의 유형입니다. |
| **parentSpanId** | String | 부모 메시지(이 경우 IoT Hub 수신 메시지 추적)의 [span-id](https://w3c.github.io/trace-context/#parent-id)입니다. |

### <a name="configurations"></a>구성

IoT Hub 구성 로그는 자동 디바이스 관리 기능 집합에 대한 이벤트 및 오류를 추적합니다.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>디바이스 스트림(미리 보기)

디바이스 스트림 범주는 개별 디바이스로 전송되는 요청-응답 상호 작용을 추적합니다.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블
<!-- REQUIRED. Please keep heading in this order -->

이 섹션은 Azure IoT Hub와 관련되고 Log Analytics의 쿼리에 사용할 수 있는 모든 Azure Monitor Logs Kusto 테이블을 참조합니다. 해당 테이블 목록 및 IoT Hub 리소스 유형에 대한 자세한 정보 관련 링크는 Azure Monitor Logs 테이블 참조의 [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub)를 참조하세요.

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

## <a name="see-also"></a>참고 항목

* Azure IoT Hub 모니터링에 대한 설명은 [Azure IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요.
* Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
