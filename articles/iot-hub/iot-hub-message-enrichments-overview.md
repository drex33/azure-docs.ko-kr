---
title: Azure IoT Hub 메시지 보강 개요
description: 이 문서에서는 메시지를 지정된 엔드포인트로 보내기 전에 추가 정보를 사용하여 메시지를 스탬프하는 기능을 IoT Hub에 제공하는 메시지 보강을 보여 줍니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: lizross
ms.openlocfilehash: 4b204b8055cbf6cd2f00d3048bb64568cc16a1fb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554506"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>디바이스-클라우드 IoT Hub 메시지의 메시지 보강

*메시지 보강* 이란 메시지를 지정된 엔드포인트로 보내기 전에 추가 정보를 메시지에 *스탬프* 하는 IoT Hub의 기능입니다. 메시지 보강을 사용하는 한 가지 이유는 다운스트림 처리를 간소화하는 데 사용할 수 있는 데이터를 포함하는 것입니다. 예를 들어 디바이스 원격 분석 메시지를 디바이스 쌍 태그로 보강하면 고객이 이 정보를 얻기 위해 디바이스 쌍 API를 호출해야 하는 부담을 줄일 수 있습니다.

![메시지 보강 흐름](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

메시지 보강에는 다음과 같은 세 가지 주요 요소가 있습니다.

* 보강 이름 또는 키

* 값

* 보강을 적용해야 하는 하나 이상의 [엔드포인트](iot-hub-devguide-endpoints.md).

**키** 가 문자열입니다. 키에는 영숫자와 특수 문자(하이픈(`-`), 밑줄(`_`), 마침표(`.`))만 사용할 수 있습니다.

**값** 은 다음 예 중 하나일 수 있습니다.

* 모든 정적 문자열. 조건, 논리, 연산, 함수와 같은 동적 값은 허용되지 않습니다. 예를 들어, 여러 고객이 사용하는 SaaS 애플리케이션을 개발하는 경우 각 고객에게 식별자를 할당하고 해당 식별자를 애플리케이션에서 사용할 수 있도록 설정할 수 있습니다. 애플리케이션이 실행되면 IoT Hub는 고객의 식별자를 사용하여 디바이스 원격 분석 메시지를 스탬프 처리하므로 각 고객에 대해 메시지를 다르게 처리할 수 있습니다.

* 메시지를 보내는 IoT 허브의 이름입니다. 이 값은 *$iothubname* 입니다.

* 디바이스 쌍 정보(예: 경로). 예를 들면 *$twin.tags.field* 및 *$twin.tags.latitude* 입니다.

   > [!NOTE]
   > 현재는 $iothubname, $twin.tags, $twin.properties.desired, $twin.properties.reported만이 메시지 보강에 지원되는 변수입니다.

메시지 보강이 선택된 엔드포인트에 전송된 메시지에 애플리케이션 속성으로 추가됩니다.  

## <a name="applying-enrichments"></a>보강 적용

메시지는 다음 예제를 포함하여 [IoT Hub 메시지 라우팅](iot-hub-devguide-messages-d2c.md)이 지원하는 모든 데이터 원본에서 가져올 수 있습니다.

* 디바이스 원격 분석(예: 온도 또는 압력)
* 디바이스 쌍 변경 알림(디바이스 쌍의 변경 내용)
* 디바이스 수명 주기 이벤트(예: 디바이스 생성 또는 삭제)

IoT Hub의 기본 제공 엔드포인트로 이동하는 메시지 또는 Azure Blob 스토리지, Service Bus 큐 또는 Service Bus 토픽과 같은 사용자 지정 엔드포인트로 라우팅되는 메시지에 보강을 추가할 수 있습니다.

엔드포인트를 Event Grid로 선택하여 Event Grid에 게시되는 메시지에 보강을 추가할 수 있습니다. Event Grid 구독에 따라 디바이스 원격 분석에 대한 IoT Hub 기본 경로를 만듭니다. 이 단일 경로는 모든 Event Grid 구독을 처리할 수 있습니다. 디바이스 원격 분석에 대한 이벤트 그리드 구독을 만든 후 이벤트 그리드 엔드포인트를 위한 보강을 구성할 수 있습니다. 자세한 내용은 [Iot Hub 및 Event Grid](iot-hub-event-grid.md)를 참조하세요.

보강은 엔드포인트별로 적용됩니다. 특정 엔드포인트에 대해 스탬프할 5개의 보강을 지정하는 경우 이 엔드포인트로 이동하는 모든 메시지에는 동일한 5개의 보강이 스탬프됩니다.

보강은 아래 방법으로 구성할 수 있습니다.

| **방법** | **Command** |
| ----- | -----| 
| 포털 | [Azure Portal](https://portal.azure.com) [메시지 보강 자습서](tutorial-message-enrichments.md)를 참조하세요. | 
| Azure CLI   | [az iot hub message-enrichment](/cli/azure/iot/hub/message-enrichment) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](/powershell/module/az.iothub/add-aziothubmessageenrichment) |

메시지 보강을 추가한다고 해서 메시지 라우팅에 대기 시간이 길어지지 않습니다.

메시지 보강을 확인하려면 [메시지 보강 자습서](tutorial-message-enrichments.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

* 표준 또는 기본 계층에서 해당 허브에 IoT Hub당 최대 10개의 보강을 추가할 수 있습니다. 무료 계층의 IoT Hub에는 최대 2개의 보강을 추가할 수 있습니다.

* 때에 따라 디바이스 쌍의 태그나 속성으로 설정된 값이 있는 보강을 적용하면, 해당 값은 문자열 값으로 스탬프됩니다. 예를 들어 보강 값이 $twin.tags.field로 설정된 경우, 메시지는 트윈의 해당 필드 값이 아니라 문자열 "$twin.tags.field"로 스탬프됩니다. 이는 다음과 같은 경우에 발생합니다.

   * IoT Hub가 기본 계층에 있습니다. 기본 계층 IoT 허브는 디바이스 쌍을 지원하지 않습니다.

   * IoT Hub가 표준 계층에 있지만 메시지를 보내는 디바이스에 디바이스 쌍이 없습니다.

   * IoT Hub가 표준 계층에 있지만, 보강 값에 사용되는 디바이스 쌍 경로가 존재하지 않습니다. 예를 들어 보강 값이 $twin.tags.location으로 설정되고 디바이스 쌍이 태그 아래에 location 속성이 없으면 메시지에 "$twin.tags.location" 문자열이 스탬프됩니다. 

* 디바이스 쌍에 대한 업데이트는 해당 보강 값에 반영되는 데 최대 5분이 걸릴 수 있습니다.

* 총 메시지 크기는 보강을 포함하여 256KB를 초과할 수 없습니다. 메시지 크기가 256KB를 초과하면 IoT Hub가 메시지를 삭제합니다. 메시지를 삭제할 때 [IoT Hub 메트릭](monitor-iot-hub-reference.md#metrics)을 사용하여 오류를 식별하고 디버그할 수 있습니다. 예를 들어 [라우팅 메트릭](monitor-iot-hub-reference.md#routing-metrics)에서 호환되지 않는 *원격 분석 메시지*(*d2c.telemetry.egress.invalid*) 메트릭을 모니터링할 수 있습니다. 자세히 알아보려면 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요.

* 메시지 보강은 디지털 트윈 변경 이벤트에 적용되지 않습니다.

## <a name="pricing"></a>가격 책정

메시지 보강은 추가 요금 없이 사용할 수 있습니다. 현재 IoT Hub에 메시지를 보낼 때 요금이 청구됩니다. 메시지가 여러 엔드포인트로 보내지더라도 요금은 해당 메시지에 대해 한 번만 청구됩니다.

## <a name="next-steps"></a>다음 단계

IoT Hub로 메시지를 라우팅하는 방법에 관한 자세한 내용은 다음 문서를 확인하세요.

* [메시지 보강 자습서](tutorial-message-enrichments.md)

* [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트에 보내기](iot-hub-devguide-messages-d2c.md)

* [자습서: IoT Hub 라우팅](tutorial-routing.md)
