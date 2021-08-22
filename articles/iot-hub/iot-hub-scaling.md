---
title: Azure IoT Hub 크기 조정 | Microsoft Docs
description: 예상된 메시지 처리량 및 원하는 기능을 지원하도록 IoT 허브를 확장하는 방법입니다. 분할을 위한 옵션 및 각 계층에 지원되는 처리량에 대한 요약을 포함합니다.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: cc293a58c372c09fa03cf9d0b6f2b729b80d7c0e
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400625"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>솔루션에 대한 올바른 IoT Hub 계층 선택

모든 IoT 솔루션은 다르므로 Azure IoT Hub는 가격 책정 및 규모에 따라 여러 가지 옵션을 제공합니다. 이 문서는 IoT Hub 요구 사항 평가를 돕기 위한 것입니다. IoT Hub 계층에 대한 가격 책정 정보는 [IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub)을 참조하세요.

솔루션에 적합한 IoT Hub 계층을 결정하려면 두 가지 사항을 스스로에게 질문합니다.

**어떤 기능을 사용할 계획입니까?**

Azure IoT Hub는 지원하는 기능의 수가 다른 두 계층, 기본 및 표준을 제공합니다. IoT 솔루션이 디바이스의 데이터 수집을 기반으로 하고 중앙 집중식으로 분석하는 경우 기본 계층은 아마도 사용자에게 적합합니다. 더 고급 구성을 사용하여 IoT 디바이스를 원격으로 제어하거나 일부 워크로드를 디바이스 자체에 배포하려는 경우 표준 계층을 사용하도록 고려해야 합니다. 각 계층에 포함된 기능의 자세한 내용은 [기본 및 표준 계층](#basic-and-standard-tiers)을 계속합니다.

**얼마나 많은 데이터를 매일 이동할 계획입니까?**

각 IoT Hub 계층은 지정된 날에 처리할 수 있는 데이터 처리량을 기반으로 세 가지 크기에서 사용 가능합니다. 이러한 크기는 1, 2, 3으로 숫자로 식별됩니다. 예를 들어 수준 1 IoT 허브의 각 단위는 하루에 40만 개의 메시지를 처리할 수 있는 반면 수준 3 단위는 3억 개를 처리할 수 있습니다. 데이터 지침에 대한 자세한 내용은 [메시지 처리량](#message-throughput)을 계속합니다.

## <a name="basic-and-standard-tiers"></a>기본 및 표준 계층

IoT Hub의 표준 계층은 모든 기능을 활성화하고 양방향 통신 기능을 활용하려는 IoT 솔루션에 필요합니다. 기본 계층은 기능의 하위 집합을 활성화하고 디바이스에서 클라우드로의 단방향 통신만 필요한 IoT 솔루션을 위한 것입니다. 두 계층은 동일한 보안 및 인증 기능을 제공합니다.

계층 내에서 한 가지 형식의 [버전](https://azure.microsoft.com/pricing/details/iot-hub/)만 IoT Hub에 선택할 수 있습니다. 예를 들어 S1 및 S2와 같은 서로 다른 버전의 단위 혼합이 아닌 여러 단위의 S1을 사용하여 IoT Hub를 만들 수 있습니다.

| 기능 | 기본 계층 | 무료/표준 계층 |
| ---------- | ---------- | ------------- |
| [디바이스-클라우드 원격 분석](iot-hub-devguide-messaging.md) | 예 | 예 |
| [디바이스당 ID](iot-hub-devguide-identity-registry.md) | 예 | 예 |
| [메시지 라우팅](iot-hub-devguide-messages-read-custom.md), [메시지 보강](iot-hub-message-enrichments-overview.md) 및 [Event Grid 통합](iot-hub-event-grid.md) | 예 | 예 |
| [HTTP, AMQP 및 MQTT 프로토콜](iot-hub-devguide-protocols.md) | 예 | 예 |
| [디바이스 프로비저닝 서비스](../iot-dps/about-iot-dps.md) | 예 | 예 |
| [모니터링 및 진단](monitor-iot-hub.md) | 예 | 예 |
| [클라우드-디바이스 메시징](iot-hub-devguide-c2d-guidance.md) |   | 예 |
| [디바이스 쌍](iot-hub-devguide-device-twins.md), [모듈 쌍](iot-hub-devguide-module-twins.md) 및 [디바이스 관리](iot-hub-device-management-overview.md) |   | 예 |
| [디바이스 스트림(미리 보기)](iot-hub-device-streams-overview.md) |   | 예 |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | 예 |
| [IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md) |   | 예 |

IoT Hub는 또한 테스트와 평가를 위한 무료 계층을 제공합니다. 제한된 메시징 허용 개수로 표준 계층의 모든 기능을 포함합니다. 무료 계층에서 기본 또는 표준 계층으로 업그레이드할 수 없습니다.

## <a name="partitions"></a>파티션

Azure IoT Hub에는 [파티션](../event-hubs/event-hubs-features.md#partitions)을 비롯한 [Azure Event Hubs](../event-hubs/event-hubs-features.md)의 많은 핵심 구성 요소가 포함됩니다. IoT Hub의 이벤트 스트림은 일반적으로 다양한 IoT 디바이스에서 보고되는 들어오는 원격 분석 데이터로 채워집니다. 이벤트 스트림의 분할을 통해 동시에 이벤트 스트림을 읽고 작성할 때 발생하는 경합을 줄일 수 없습니다.

IoT Hub를 만들 때 파티션 제한을 선택하고 변경할 수 없습니다. 기본 계층 IoT Hub 및 표준 계층 IoT Hub에 대한 최대 파티션 제한은 32개입니다. 대부분의 IoT Hub는 4개의 파티션만 필요합니다. 파티션을 결정하는 방법에 대한 자세한 내용은 Event Hubs FAQ. [얼마나 많은 파티션이 필요한가요?](../event-hubs/event-hubs-faq.yml#how-many-partitions-do-i-need-)를 참조하세요.

## <a name="tier-upgrade"></a>계층 업그레이드

IoT 허브를 만들면 기존 작업을 중단하지 않고 기본 계층에서 표준 계층으로 업그레이드할 수 있습니다. 자세한 내용은 [IoT 허브를 업그레이드하는 방법](iot-hub-upgrade.md)을 참조하세요.

기본 계층에서 표준 계층으로 마이그레이션할 때 파티션 구성은 변경되지 않습니다.

> [!NOTE]
> 무료 계층은 기본 또는 표준으로의 업그레이드를 지원하지 않습니다.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST API

IoT Hub의 기본 및 표준 계층 간의 지원되는 기능 차이는 일부 API 호출이 기본 계층 허브와 작동하지 않는 것을 의미합니다. 다음 표는 사용 가능한 API를 보여줍니다.

| API | 기본 계층 | 무료/표준 계층 |
| --- | ---------- | ------------- |
| [디바이스 삭제](/javascript/api/azure-iot-digitaltwins-service/registrymanager#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | 예 | 예 |
| [디바이스 가져오기](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | 예 | 예 |
| [모듈 삭제](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | 예 | 예 |
| [모듈 가져오기](/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule) | 예 | 예 |
| [레지스트리 통계 가져오기](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getdevicestatistics-msrest-requestoptionsbase-) | 예 | 예 |
| [서비스 통계 가져오기](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getservicestatistics-msrest-requestoptionsbase-) | 예 | 예 |
| [디바이스 만들기 또는 업데이트](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatedevice-string--device--servicecallback-device--) | 예 | 예 |
| [모듈 만들기 또는 업데이트](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | 예 | 예 |
| [IoT Hub 쿼리](/dotnet/api/microsoft.azure.devices.registrymanager) | 예 | 예 |
| [파일 업로드 SAS URI 만들기](/rest/api/iothub/device/createfileuploadsasuri) | 예 | 예 |
| [바인딩된 디바이스 알림 수신](/rest/api/iothub/device/receivedeviceboundnotification) | 예 | 예 |
| [디바이스 이벤트 보내기](/rest/api/iothub/device/senddeviceevent) | 예 | 예 |
| 모듈 이벤트 보내기 | AMQP 및 MQTT만 해당 | AMQP 및 MQTT만 해당 |
| [파일 업로드 상태 업데이트](/rest/api/iothub/device/updatefileuploadstatus) | 예 | 예 |
| [대량 디바이스 작업](/javascript/api/azure-iot-digitaltwins-service/registrymanager#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | 예, IoT Edge 기능을 제외하고 맞습니다. | 예 |
| [가져오기/내보내기 작업 취소](/rest/api/iothub/service/jobs/cancelimportexportjob) | 예 | 예 |
| [가져오기/내보내기 작업 만들기](/rest/api/iothub/service/jobs/createimportexportjob) | 예 | 예 |
| [가져오기/내보내기 작업 가져오기](/rest/api/iothub/service/jobs/getimportexportjob) | 예 | 예 |
| [가져오기/내보내기 작업 가져오기](/rest/api/iothub/service/jobs/getimportexportjobs) | 예 | 예 |
| [명령 큐 제거](/javascript/api/azure-iot-digitaltwins-service/registrymanager#purgecommandqueue-string--msrest-requestoptionsbase-) |   | 예 |
| [디바이스 쌍 가져오기](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) |   | 예 |
| [모듈 쌍 가져오기](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | 예 |
| [디바이스 메서드 호출](./iot-hub-devguide-direct-methods.md) |   | 예 |
| [디바이스 쌍 업데이트](./iot-hub-devguide-device-twins.md) |   | 예 |
| [모듈 쌍 업데이트](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | 예 |
| [바인딩된 디바이스 알림 중단](/rest/api/iothub/device/abandondeviceboundnotification) |   | 예 |
| [바인딩된 디바이스 알림 완료](/rest/api/iothub/device/completedeviceboundnotification) |   | 예 |
| [작업 취소](/rest/api/media/jobs/canceljob) |   | 예 |
| [작업 만들기](/rest/api/media/jobs/create) |   | 예 |
| [작업 가져오기](/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob) |   | 예 |
| [작업 쿼리](/javascript/api/azure-iot-digitaltwins-service/jobclient#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | 예 |

## <a name="message-throughput"></a>메시지 처리량

IoT Hub 솔루션의 크기를 조정하는 가장 적절한 방법은 장치별로 트래픽을 평가하는 것입니다. 특히 다음과 같은 범주의 작업에 필요한 최대 처리량을 고려해야 합니다.

* 디바이스-클라우드 메시지
* 클라우드-디바이스 메시지
* ID 레지스트리 작업

트래픽은 단위당 IoT Hub에 대해 측정됩니다. IoT Hub를 만들 때 계층 및 버전을 선택하고 사용 가능한 단위 수를 설정합니다. B1, B2, S1 또는 S2 버전의 경우 최대 200개 단위를 구매할 수 있고, B3 또는 S3 버전의 경우 최대 10개 단위를 구매할 수 있습니다. IoT Hub를 만든 후에는 기존 작업을 중단하지 않고 해당 버전 내에서 사용할 수 있는 단위 수를 변경하거나, 해당 계층 내의 버전 간에 업그레이드 또는 다운그레이드하거나(B1에서 B2로), 기본 계층에서 표준 계층으로 업그레이드(B1에서 S1)할 수 있습니다. 자세한 내용은 [IoT 허브를 업그레이드하는 방법](iot-hub-upgrade.md)을 참조하세요.  

각 계층의 트래픽 기능의 예로 디바이스-클라우드 메시지는 지속적인 처리량 지침을 따릅니다.

| 계층 버전 | 지속적인 처리량 | 지속적인 전송 속도 |
| --- | --- | --- |
| B1, S1 |장치당 최대 1111KB/분<br/>(1.5GB/일/장치) |장치당 평균 278메시지/분<br/>(400,000메시지/일/장치당) |
| B2, S2 |장치당 최대 16MB/분<br/>(22.8GB/일/장치) |장치당 평균 4,167개 메시지/분<br/>(6백만 개의 메시지/일/장치당) |
| B3, S3 |장치당 최대 814MB/분<br/>(1144.4GB/일/장치) |장치당 평균 208,333 메시지/분<br/>(3억 개의 메시지/일/장치당) |

디바이스-클라우드 처리량은 IoT 솔루션을 디자인할 때 고려해야 하는 메트릭 중 하나일 뿐입니다. 보다 포괄적인 내용은 [IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요.

### <a name="identity-registry-operation-throughput"></a>ID 레지스트리 작업 처리량

IoT Hub ID 레지스트리 작업은 대부분이 디바이스 프로비저닝과 관련되므로 런타임 작업으로 간주되지 않습니다.

관련 버스트 성능 수치는 [IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요.

## <a name="auto-scale"></a>자동 크기 조정

IoT Hub에 허용된 메시지 제한에 도달하면 [자동으로 크기 조정하는 이러한 단계](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/)을 사용하여 동일한 IoT Hub 계층에서 IoT Hub 단위를 증가시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

* IoT Hub 기능 및 성능 세부 정보에 대한 자세한 내용은 [IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub) 또는 [IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요.

* IoT Hub 계층을 변경하려면 [IoT 허브 업그레이드](iot-hub-upgrade.md)의 단계를 따릅니다.