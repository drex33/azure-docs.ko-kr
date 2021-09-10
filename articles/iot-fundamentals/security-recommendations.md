---
title: Azure IoT 보안 권장 사항 | Microsoft Docs
description: 이 문서에서는 Azure IoT Hub 솔루션의 보안을 유지하기 위한 추가 단계를 간략히 설명합니다.
author: dsk-2015
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 401188de54050be932fc8d52f5cc4466378e15cd
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772036"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure IoT(사물 인터넷) 배포에 대한 보안 권장 사항

이 문서에는 IoT에 대한 보안 권장 사항이 포함되어 있습니다. 이러한 권장 사항을 구현하면 공유 책임 모델에 설명된 대로 보안 의무를 충족하는 데 도움이 됩니다. Microsoft에서 서비스 공급자 책임을 충족하기 위해 수행하는 작업에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)을 참조하세요.

이 문서에 포함된 일부 권장 사항은 Azure Defender for IoT에서 자동으로 모니터링할 수 있습니다. Azure Defender for IoT는 Azure에서 리소스를 보호하는 첫 번째 방어선입니다. 이는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음 이를 해결하는 방법에 대한 권장 사항을 제공합니다.

- Azure Defender for IoT 권장 사항에 대한 자세한 내용은 [Azure Defender for IoT의 보안 권장 사항](../security-center/security-center-recommendations.md)을 참조하세요.
- Azure Defender for IoT에 대한 내용은 [Azure Defender for IoT란?](../security-center/security-center-introduction.md)을 참조하세요.

## <a name="general"></a>일반

| 권장 | 주석 |
|-|----|
| 최신 정보 유지 | 최신 버전의 지원되는 플랫폼, 프로그래밍 언어, 프로토콜 및 프레임워크를 사용합니다. |
| 인증 키를 안전하게 유지 | 배포 후 디바이스 ID와 해당 인증 키를 물리적으로 안전하게 유지합니다. 이렇게 하면 악성 디바이스가 등록된 디바이스로 가장하는 것을 방지할 수 있습니다. |
| 가능한 경우 디바이스 SDK 사용 | 디바이스 SDK는 강력하고 안전한 디바이스 애플리케이션 개발을 지원하기 위해 암호화, 인증 등의 다양한 보안 기능을 구현합니다. 자세한 내용은 [Azure IoT Hub SDK 이해 및 사용](../iot-hub/iot-hub-devguide-sdks.md)을 참조하세요. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리 

| 권장 | 주석 |
|-|----|
| 허브에 대한 액세스 제어 정의 | 기능에 따라 각 구성 요소가 IoT Hub 솔루션에서 갖게 될 [액세스 유형을 이해하고 정의](iot-security-deployment.md#securing-the-cloud)합니다. 허용되는 권한은 *Registry Read*, *RegistryReadWrite*, *ServiceConnect*, *DeviceConnect* 입니다. [IoT Hub의 기본 공유 액세스 정책은](../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions) 해당 역할에 따라 각 구성 요소에 대한 사용 권한을 정의하는 데에도 도움이 됩니다. |
| 백 엔드 서비스에 대한 액세스 제어 정의 | IoT Hub 솔루션에 의해 수집된 데이터는 [Cosmos DB](../cosmos-db/index.yml), [Stream Analytics](../stream-analytics/index.yml), [App Service](../app-service/index.yml), [Logic Apps](../logic-apps/index.yml), 및 [Blob storage](../storage/blobs/storage-blobs-introduction.md)와 같은 다른 Azure 서비스에서 사용될 수 있습니다. 이러한 서비스에 대해 문서화된 적절한 액세스 권한을 이해하고 허용해야 합니다. |

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 |
|-|----|
| 안전한 디바이스 인증 | 각 디바이스에 [고유 ID 키 또는 보안 토큰 ](iot-security-deployment.md#iot-hub-security-tokens) 또는 [디바이스 내 X.509 인증서](iot-security-deployment.md#x509-certificate-based-device-authentication)를 사용하여 디바이스와 IoT Hub 간에 안전한 통신을 보장합니다. 적절한 방법을 사용하여 [ 선택한 프로토콜(MQTT, AMQP 또는 HTTPS)에 따라 보안 토큰을 사용](../iot-hub/iot-hub-dev-guide-sas.md)합니다. |
| 안전한 디바이스 통신 | IoT Hub는 버전 1.2 및 1.0을 지원하는 TLS(전송 계층 보안) 표준을 사용하여 디바이스에 대한 연결을 보호합니다. [TLS 1.2](https://tools.ietf.org/html/rfc5246)를 사용하여 보안 수준을 최대화합니다. |
| 서비스 통신 보안 유지 | IoT Hub는 TLS 프로토콜만 사용하여 [Azure Storage](../storage/index.yml) 또는 [Event Hubs](../event-hubs/index.yml)와 같은 백 엔드 서비스에 연결하는 엔드포인트를 제공하며 암호화되지 않은 채널에는 엔드포인트가 노출되지 않습니다. 이 데이터가 저장 또는 분석을 위해 이러한 백 엔드 서비스에 연결되면 해당 서비스에 적합한 보안 및 암호화 방법을 사용하고, 백 엔드에서 민감한 정보를 보호해야 합니다. |

## <a name="networking"></a>네트워킹

| 권장 | 주석 |
|-|----|
| 디바이스에 대한 액세스 보호 | 원치 않는 액세스를 방지하기 위해 디바이스의 하드웨어 포트를 최소한의 상태로 유지합니다. 또한 디바이스의 물리적 변조를 방지하거나 검색하는 메커니즘을 빌드합니다. 자세한 내용은 [IoT 보안 모범 사례](iot-security-best-practices.md)를 참조하세요. |
| 안전한 하드웨어 빌드 | 암호화된 스토리지 또는 TPM(신뢰할 수 있는 플랫폼 모듈)과 같은 보안 기능을 통합하여 디바이스와 인프라를 더욱 안전하게 유지합니다. 디바이스 운영 체제 및 드라이버를 최신 버전으로 업그레이드하고, 공간에서 허용하는 경우에는 바이러스 백신 및 맬웨어 방지 기능을 설치합니다. [IoT 보안 아키텍처](iot-security-architecture.md)를 읽고 이를 통해 여러 보안 위협을 완화하는 방법을 알아보세요. |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 | Azure Defender 지원 |
|-|----|--|
| 디바이스에 대한 무단 액세스 모니터링 |  디바이스 운영 체제의 로깅 기능을 사용하여 보안 위반이나 디바이스 또는 해당 포트의 물리적 변조를 모니터링합니다. | Yes |
| 클라우드에서 IoT 솔루션 모니터링 | [Azure Monitor 메트릭](../iot-hub/monitor-iot-hub.md)을 사용하여 IoT Hub 솔루션의 전반적인 상태를 모니터링합니다. | Yes |
| 진단 설정 | 솔루션에서 이벤트를 로깅한 다음, 진단 로그를 Azure Monitor로 보내 성능을 파악하여 작업을 면밀히 관찰합니다. 자세한 내용은 [IoT Hub의 모니터링 및 진단 문제](../iot-hub/monitor-iot-hub.md)를 참조하세요. | 예 |

## <a name="next-steps"></a>다음 단계

Azure IoT와 관련된 고급 시나리오의 경우 추가 보안 요구 사항을 고려해야 할 수 있습니다. 자세한 지침은 [IoT 보안 아키텍처](iot-security-architecture.md)를 참조하세요.