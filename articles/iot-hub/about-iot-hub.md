---
title: Azure IoT Hub란? | Microsoft Docs
description: 이 문서에서는 Azure IoT Hub의 용도를 설명합니다. 이를 통해 확장 가능한 방식으로 데이터를 읽을 수 있으며 디바이스를 안전하게 관리할 수 있습니다.
author: robinsh
ms.author: robinsh
ms.date: 05/03/2021
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
- contentperf:fy21q3
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8e491e6fc8d0eb93a3d9696f68f045191c2da20b
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400906"
---
# <a name="what-is-azure-iot-hub"></a>Azure IoT Hub란?

IoT Hub는 클라우드에서 호스트되는 관리 서비스이며, IoT 애플리케이션과 연결된 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. 수백만 개의 디바이스와 백 엔드 솔루션을 안정적으로 안전하게 연결할 수 있습니다. 거의 모든 디바이스를 IoT Hub에 연결할 수 있습니다. 

디바이스-클라우드 원격 분석, 디바이스에서 파일 업로드 및 클라우드에서 디바이스를 제어하는 요청-회신 방법과 같은 여러 메시징 패턴이 지원됩니다. 또한 IoT Hub는 디바이스 만들기, 디바이스 연결 및 디바이스 오류를 추적하는 데 도움이 되는 모니터링을 지원합니다.

IoT Hub의 기능을 사용하면 제조에 사용된 산업 장비 관리, 의료 분야의 중요한 자산 추적 및 오피스 빌딩 사용 모니터링과 같이 모든 기능을 갖춘 확장성 있는 IoT 솔루션을 구축할 수 있습니다.

## <a name="scale-your-solution"></a>솔루션 확장

IoT Hub는 수백만 개의 동시 연결 디바이스와 초당 수백만 개의 이벤트까지 확장하여 IoT 작업을 지원합니다. IoT Hub의 크기를 조정하는 방법에 대한 자세한 내용은 [IoT Hub 크기 조정](iot-hub-scaling.md)을 참조하세요. IoT Hub에서 제공하는 여러 서비스 계층 및 확장성 요구 사항을 가장 잘 충족하는 방법에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-hub/)를 참조하세요.

## <a name="secure-your-communications"></a>통신 보안

IoT Hub를 사용하여 데이터를 안전하게 보낼 수 있습니다.

* 디바이스별 인증을 사용하면 각 디바이스에서 IoT Hub에 안전하게 연결할 수 있으며 각 디바이스를 안전하게 관리할 수 있습니다.

* 디바이스 액세스를 완벽하게 제어하고, 디바이스별 수준에서 연결을 제어할 수 있습니다.

* 디바이스가 처음 부팅될 때 [IoT Hub Device Provisioning Service](../iot-dps/index.yml)에서 해당 디바이스를 올바른 IoT Hub에 자동으로 프로비전합니다.

* 여러 인증 유형을 통해 다양한 디바이스 기능을 지원합니다.

  * SAS 토큰 기반 인증을 통해 IoT 솔루션을 빠르게 시작할 수 있습니다.

  * 안전한 표준 기반 인증을 위해 개별 X.509 인증서 인증을 사용할 수 있습니다.

  * X.509 CA 인증은 간단한 표준 기반 등록에 사용할 수 있습니다.

## <a name="route-device-data"></a>디바이스 데이터 라우팅

기본 제공 메시지 라우팅 기능을 사용하면 자동 규칙 기반 메시지 팬아웃을 유연하게 설정할 수 있습니다.

* [메시지 라우팅](iot-hub-devguide-messages-d2c.md)을 사용하여 허브에서 디바이스 원격 분석을 보내는 위치를 제어합니다.

* 메시지를 여러 엔드포인트에 라우팅하는 데 추가 비용이 들지 않습니다.

* 코드를 작성하지 않고도 해당 메시지의 내용을 기반으로 메시지를 자동으로 보내도록 라우팅 규칙을 구성할 수 있습니다.

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

IoT Hub를 다른 Azure 서비스와 통합하여 완전한 엔드투엔드 솔루션을 빌드할 수 있습니다. 예를 들어 이에 해당하는 서비스는 다음과 같습니다.

* [Azure Event Grid](../event-grid/index.yml)를 사용하여 비즈니스에서 안정적이고, 확장 가능하며, 안전한 방식으로 중요한 이벤트에 빠르게 대응할 수 있습니다.

* [Azure Logic Apps](../logic-apps/index.yml)를 사용하여 비즈니스 프로세스를 자동화합니다.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md)을 사용하여 컴퓨터 학습 및 AI 모델을 솔루션에 추가합니다.

* [Azure Stream Analytics](../stream-analytics/index.yml)를 사용하여 디바이스에서 스트림하는 데이터에 대해 실시간 분석 계산을 실행합니다.

## <a name="configure-and-control-your-devices"></a>디바이스 구성 및 제어

기본 제공 기능 배열을 사용하여 IoT Hub에 연결된 디바이스를 관리할 수 있습니다.

* 모든 디바이스에 대한 디바이스 메타데이터 및 상태 정보를 저장, 동기화 및 쿼리합니다.

* 디바이스별로 또는 디바이스의 일반적인 특성에 따라 디바이스 상태를 설정합니다.

* 메시지 라우팅 통합을 통해 디바이스에서 보고된 상태 변경에 자동으로 응답합니다.

## <a name="make-your-solution-highly-available"></a>고가용성 솔루션 만들기

IoT Hub에는 99.9%의 [IoT Hub에 대한 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/iot-hub/)이 있습니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

## <a name="connect-your-devices"></a>사용자 디바이스 연결

[Azure IoT 디바이스 SDK](./iot-hub-devguide-sdks.md) 라이브러리를 사용하여 디바이스에서 실행되고 IoT Hub와 상호 작용하는 애플리케이션을 빌드합니다. 지원되는 플랫폼에는 여러 Linux 배포판, Windows 및 실시간 운영 체제가 포함됩니다. 지원되는 언어는 다음과 같습니다.

* C
* 임베디드 C
* C#
* Java
* Python
* Node.js

디바이스 연결을 위해 IoT Hub 및 디바이스 SDK에서 지원하는 프로토콜은 다음과 같습니다.

* HTTPS
* AMQP
* Websocket 통한 AMQP
* MQTT
* WebSocket을 통한 MQTT

IoT Hub 및 디바이스 SDK는 디바이스를 연결하기 위한 [Azure IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md) 규칙을 지원합니다. IoT 플러그 앤 플레이 디바이스는 디바이스 모델을 사용하여 IoT 플러그 앤 플레이 지원 애플리케이션에 해당 기능을 보급합니다. 디바이스 모델을 통해 솔루션 빌더가 수동으로 구성하지 않고 스마트 디바이스를 솔루션과 통합할 수 있습니다.

솔루션에서 디바이스 라이브러리를 사용할 수 없는 경우 디바이스에서 기본적으로 MQTT v3.1.1, HTTPS 1.1 또는 AMQP 1.0 프로토콜을 사용하여 허브에 연결할 수 있습니다.

솔루션에서 지원되는 프로토콜 중 하나를 사용할 수 없는 경우 사용자 지정 프로토콜을 지원하도록 IoT Hub를 확장할 수 있습니다.

* 에지에서 프로토콜 변환을 수행하는 필드 게이트웨이를 만들려면 [Azure IoT Edge](../iot-edge/index.yml)를 사용합니다.

* 클라우드에서 프로토콜 변환을 수행하려면 [Azure IoT 프로토콜 게이트웨이](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)를 사용자 지정합니다.

## <a name="quotas-and-limits"></a>할당량 및 제한

Azure 구독마다 서비스 남용을 방지하기 위해 기본 할당량 한도가 적절히 설정되어 있습니다. 이러한 제한은 IoT 솔루션의 범위에 영향을 줄 수 있습니다. 구독별 기준의 현재 제한은 구독당 50개 IoT 허브입니다. 지원 센터에 문의하여 할당량 증가를 요청할 수 있습니다. 자세한 내용은 [IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요. 할당량 한도에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

* [Azure 구독 및 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub 제한 및 사용자](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>Azure Stack Hub의 IoT Hub(미리 보기)

Azure Stack Hub(미리 보기)의 IoT Hub를 사용하면 하이브리드 IoT 솔루션을 만들 수 있습니다. IoT Hub는 관리되는 서비스로, IoT 애플리케이션과 이를 통해 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. Azure Stack Hub의 IoT Hub를 사용하여 IoT 디바이스와 온-프레미스 솔루션 간에 안정적이고 안전한 통신을 통해 IoT 솔루션을 빌드할 수 있습니다.

Azure Stack Hub의 IoT Hub는 공개 미리 보기 동안에는 무료로 제공됩니다. 자세한 내용은 [Azure Stack Hub의 IoT Hub 개요](/azure-stack/operator/iot-hub-rp-overview)를 참조하세요.

## <a name="next-steps"></a>다음 단계

엔드투엔드 IoT 솔루션을 사용해 보려면 다음 IoT Hub 빠른 시작을 확인해 보세요.

* [빠른 시작: 디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

Azure IoT를 사용하여 IoT 솔루션을 빌드 및 배포하는 방법에 대해 자세히 알아보려면

* [기본 사항: Azure IoT 기술 및 솔루션](../iot-fundamentals/iot-services-and-technologies.md)을 방문하세요.