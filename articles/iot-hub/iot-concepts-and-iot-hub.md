---
title: IoT 개념 및 Azure IoT 허브 | Microsoft Docs
description: 이 문서에서는 Azure IoT Hub의 새 사용자에 대 한 기본 개념을 설명 합니다.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 0ba840e4382aba96ead1d97f954362539833ba97
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670367"
---
# <a name="iot-concepts-and-azure-iot-hub"></a>IoT 개념 및 Azure IoT 허브

이 문서에서는 iot (사물 인터넷), Azure IoT 허브 및 iot 장치에 대해 설명 합니다.

## <a name="iot-concepts"></a>IoT 개념

IoT (사물 인터넷)는 일반적으로 인터넷 또는 다른 통신 네트워크를 통해 데이터에 연결 하 고 다른 장치 및 서비스와 데이터를 교환 하는 물리적 장치의 네트워크로 정의 됩니다. 현재 전 세계 100억 대의 연결 된 장치가 있으며, 매년 추가 됩니다. 필요한 센서 및 소프트웨어에 포함할 수 있는 모든 항목은 인터넷을 통해 연결할 수 있습니다. 다음 기술은 IoT를 가능 하 게 만들었습니다.

- 낮은 비용, 낮은 전원 센서에 대 한 액세스.
- 인터넷 연결을 가능 하 게 하는 다양 한 프로토콜
- Azure와 같은 클라우드 컴퓨팅 플랫폼
- 빅 데이터.
- 기계 학습.
- AI

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub는 IoT 응용 프로그램 및 연결 된 장치 간 통신을 위해 중앙 메시지 허브 역할을 하는 클라우드에서 호스트 되는 관리 되는 서비스입니다. 수백만 개의 디바이스와 백 엔드 솔루션을 안정적으로 안전하게 연결할 수 있습니다. 거의 모든 디바이스를 IoT Hub에 연결할 수 있습니다. 

디바이스-클라우드 원격 분석, 디바이스에서 파일 업로드 및 클라우드에서 디바이스를 제어하는 요청-회신 방법과 같은 여러 메시징 패턴이 지원됩니다. 또한 IoT Hub는 장치 만들기, 장치 연결 및 장치 오류를 추적 하는 데 도움이 되는 모니터링을 지원 합니다.

IoT Hub는 수백만 개의 동시 연결 디바이스와 초당 수백만 개의 이벤트까지 확장하여 IoT 작업을 지원합니다. IoT Hub의 크기를 조정하는 방법에 대한 자세한 내용은 [IoT Hub 크기 조정](iot-hub-scaling.md)을 참조하세요. IoT Hub에서 제공하는 여러 서비스 계층 및 확장성 요구 사항을 가장 잘 충족하는 방법에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-hub/)를 참조하세요.

IoT Hub를 다른 Azure 서비스와 통합하여 완전한 엔드투엔드 솔루션을 빌드할 수 있습니다. 예를 들어 이에 해당하는 서비스는 다음과 같습니다.

* [Azure Event Grid](../event-grid/index.yml)를 사용하여 비즈니스에서 안정적이고, 확장 가능하며, 안전한 방식으로 중요한 이벤트에 빠르게 대응할 수 있습니다.

* [Azure Logic Apps](../logic-apps/index.yml)를 사용하여 비즈니스 프로세스를 자동화합니다.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md)을 사용하여 컴퓨터 학습 및 AI 모델을 솔루션에 추가합니다.

* [Azure Stream Analytics](../stream-analytics/index.yml)를 사용하여 디바이스에서 스트림하는 데이터에 대해 실시간 분석 계산을 실행합니다.

IoT Hub에는 99.9%의 [IoT Hub에 대한 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/iot-hub/)이 있습니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

Azure 구독마다 서비스 남용을 방지하기 위해 기본 할당량 한도가 적절히 설정되어 있습니다. 이러한 제한은 IoT 솔루션의 범위에 영향을 줄 수 있습니다. 구독별 기준의 현재 제한은 구독당 50개 IoT 허브입니다. 지원 센터에 문의하여 할당량 증가를 요청할 수 있습니다. 자세한 내용은 [IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요. 할당량 한도에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

* [Azure 구독 및 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub 제한 및 사용자](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-devices"></a>IoT 디바이스

IoT 장치는 브라우저 및 모바일 앱과 같은 다른 클라이언트와 다릅니다. IoT 디바이스의 이러한 특징은 구체적으로 다음과 같습니다.

- 운영자가 없는 내장 시스템인 경우가 많습니다.
- 물리적 액세스에 많은 비용이 드는 원격 위치에 배포될 수 있습니다.
- 솔루션 백 엔드를 통해서만 연결할 수 있습니다.
- 제한 된 전원 및 처리 리소스를 사용할 수 있습니다.
- 간헐적 이거나 느리거나 비용이 많이 드는 네트워크 연결이 있을 수 있습니다.
- 에서 독점, 사용자 지정 또는 업계 특정 응용 프로그램 프로토콜을 사용 해야 할 수 있습니다.

### <a name="device-identity"></a>디바이스 ID

모든 IoT hub에는 연결 하도록 허용 된 장치 및 모듈에 대 한 정보를 저장 하는 id 레지스트리가 있습니다. 장치 또는 모듈에 연결할 수 있으려면 먼저 IoT hub의 id 레지스트리에 해당 장치 또는 모듈에 대 한 항목이 있어야 합니다. 또한 디바이스 또는 모듈은 ID 레지스트리에 저장된 자격 증명에 따라 IoT Hub로 인증되어야 합니다.

Microsoft는 디바이스와 IoT Hub 간에 두 가지 인증 방법을 지원합니다. SAS 토큰 기반 인증 또는 x.509 인증서 인증을 사용할 수 있습니다.

SAS 기반 토큰 방법은 대칭 키를 각 호출에 연결하여 디바이스에서 IoT Hub에 대해 수행한 각 호출에 대한 인증을 제공합니다. X.509 기반 인증은 TLS(전송 계층 보안) 표준 연결 설정의 일부로 물리적 레이어에서 IoT 디바이스의 인증을 허용합니다. 보안 토큰 기반 방법은 X.509 인증 없이 사용할 수 있으므로 덜 안전한 패턴입니다. 두 방법 중에서 선택할 때는 우선, 프라이빗 키를 안전하게 저장하는 데 필요한 디바이스 인증의 보안 수준 및 디바이스에 있는 보안 스토리지의 가용성을 고려해야 합니다.

[IoT Hub Device Provisioning Service](../iot-dps/index.yml)를 사용하여 한 번에 많은 디바이스를 설정하고 프로비전할 수 있습니다.

### <a name="device-communication"></a>디바이스 통신

인증 방법을 선택한 후에는 TLS (Transport Layer Security) 표준을 사용 하 여 IoT 장치와 IoT Hub 간의 인터넷 연결이 보호 됩니다. Azure IoT는 TLS 1.2, TLS 1.1 및 TLS 1.0을 순서대로 지원합니다. TLS 1.0에 대한 지원은 이전 버전과의 호환성을 위해서만 제공됩니다. IoT Hub에서 TLS 지원을 확인하여 최고 보안을 제공하는 TLS 1.2를 사용하도록 허브를 구성하는 방법을 확인합니다.

### <a name="device-communication-patterns"></a>장치 통신 패턴

일반적으로 IoT 디바이스는 센서의 원격 분석을 클라우드의 백 엔드 서비스로 보냅니다. 그러나 백 엔드 서비스에서 디바이스에 명령 전송과 같은 다른 유형의 통신이 가능합니다. 서로 다른 유형의 통신에 대 한 몇 가지 예는 다음과 같습니다. 

*  냉동 트럭은 5분마다 IoT Hub로 온도를 전송합니다.
*  백 엔드 서비스는 디바이스에 명령을 전송하여 문제를 진단하는 데 도움이 되는 원격 분석을 전송하는 빈도를 변경합니다.
*  화학 공장의 회분식 반응기를 모니터링하는 디바이스는 온도가 특정 값을 초과하면 경고를 보냅니다.

### <a name="device-telemetry"></a>디바이스 원격 분석

디바이스에서 수신된 원격 분석의 예로 속도 또는 온도와 같은 센서 데이터, 누락된 이벤트와 같은 오류 메시지 또는 디바이스 정상 상태를 나타내는 정보 메시지가 포함될 수 있습니다. IoT 장치는 정보를 얻기 위해 응용 프로그램에 이벤트를 보냅니다. 애플리케이션에 다른 엔드포인트에서 처리나 스토리지에 대한 특정 이벤트 하위 집합이 필요할 수 있습니다.

### <a name="device-properties"></a>디바이스 속성

속성은 IoT Hub에서 읽거나 설정할 수 있으며 작업이 완료 되 면 알림을 보내는 데 사용할 수 있습니다. 디바이스에서 특정 속성의 예는 온도입니다. 디바이스에서 업데이트하거나 디바이스에 연결된 온도 센서에서 읽을 수 있는 쓰기 가능한 속성일 수 있습니다. 

[디바이스 쌍](iot-hub-devguide-device-twins.md)이나 [플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md)를 사용하여 IoT Hub에서 속성을 사용할 수 있습니다.

디바이스 쌍과 플러그 앤 플레이 간의 차이점에 대한 자세한 내용은 [플러그 앤 플레이](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins)를 참조하세요.

### <a name="device-commands"></a>디바이스 명령

명령의 예는 장치를 다시 부팅 하는 것입니다. IoT Hub는 장치에서 직접 메서드를 호출할 수 있도록 하 여 명령을 구현 합니다. [직접 메서드](iot-hub-devguide-direct-methods.md)는 사용자가 지정한 시간 제한을 초과하는 즉시 성공하거나 실패한다는 점에서 HTTP 호출과 비슷한 디바이스와의 요청-응답 상호 작용을 나타냅니다. 이 방법은 즉각적인 조치 과정이 디바이스의 응답 여부에 따라 달라지는 시나리오에서 유용합니다.

### <a name="act-on-device-data"></a>장치 데이터에 대 한 작업

IoT Hub를 통해 다른 Azure 서비스와 장치 데이터 값의 잠금을 해제 하는 기능을 제공 하 여 사후 관리 대신 예측 문제 해결으로 전환할 수 있습니다. 다른 Azure 서비스와 IoT Hub를 연결하면 기계 학습, 분석 및 AI를 수행하여 실시간 데이터에서 작업을 수행하고, 처리를 최적화하며, 심층 인사이트를 얻을 수 있습니다.

#### <a name="built-in-endpoint-collects-device-data-by-default"></a>기본 제공 끝점은 기본적으로 장치 데이터를 수집 합니다.

기본 제공 엔드포인트는 기본적으로 디바이스에서 데이터를 수집합니다. 데이터는 전용 IoT 장치 끝점에 대 한 요청-응답 패턴을 사용 하 여 수집 되 고 최대 7 일 동안 사용할 수 있으며 장치에서 작업을 수행 하는 데 사용할 수 있습니다. 다음은 디바이스 엔드포인트에서 허용하는 데이터입니다.

*    디바이스-클라우드 메시지를 보냅니다.
*    클라우드-디바이스 메시지를 받습니다.
*    파일 업로드를 시작합니다.
*    디바이스 쌍 속성 검색 및 업데이트.
*    직접 메서드 요청 수신.

IoT Hub 엔드포인트에 대한 자세한 내용은 [IoT Hub Dev 가이드 엔드포인트](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints)를 참조하세요.

#### <a name="message-routing-sends-data-to-other-endpoints"></a>메시지 라우팅은 데이터를 다른 끝점으로 보냅니다.

추가 처리를 위해 데이터를 다른 서비스로 라우팅할 수도 있습니다. IoT 솔루션이 스케일 아웃되면 디바이스 수, 이벤트 볼륨, 이벤트 다양성 및 다른 서비스도 달라집니다. 이 패턴을 제공하려면 유연하고 확장 가능하고 일관되며 안정적인 이벤트 라우팅 방법이 필요합니다. 메시지 경로가 생성되면 대체 경로가 구성되지 않은 한 데이터는 기본 제공 엔드포인트로 흐르지 않게 됩니다. 여러 가지 메시지 라우팅 사용을 보여주는 자습서는 [라우팅 자습서](tutorial-routing.md)를 참조하세요.

또한 IoT Hub는 여러 구독자에게 데이터를 팬아웃할 수 있는 Event Grid와 통합됩니다. Event Grid는 다양한 Azure 서비스와 애플리케이션에서 이벤트를 쉽게 관리할 수 있게 해주는 완전 관리형 이벤트 서비스입니다. 성능과 규모에 따라 이벤트 기반 애플리케이션 및 서버리스 아키텍처를 간단하게 빌드할 수 있습니다. 메시지 라우팅과 Event Grid 사용 간의 차이점은 [메시지 라우팅 및 Event Grid 비교](iot-hub-event-grid-routing-comparison.md)에 설명되어 있습니다.

## <a name="next-steps"></a>다음 단계

엔드투엔드 IoT 솔루션을 사용해 보려면 다음 IoT Hub 빠른 시작을 확인해 보세요.

* [빠른 시작: 디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

Azure IoT를 사용하여 IoT 솔루션을 빌드 및 배포하는 방법에 대해 자세히 알아보려면

* [기본 사항: Azure IoT 기술 및 솔루션](../iot-fundamentals/iot-services-and-technologies.md)을 방문하세요.