---
title: Azure IoT Central로 빌드된 용수 사용량 모니터링 솔루션을 위한 참조 아키텍처| Microsoft Docs
description: Azure IoT Central로 빌드된 용수 사용량 모니터링 솔루션의 개념에 대해 알아보세요.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f431e72d7609fcd7469895e461a4be412a5233d6
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229833"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>용수 사용량 모니터링 참조 아키텍처

용수 사용량 모니터링 솔루션은 **Azure IoT Central 앱 템플릿** 을 사용하여 스타터 IoT 애플리케이션으로 빌드할 수 있습니다. 이 문서에서는 엔드투엔드 솔루션 빌드에 관한 개략적인 참조 아키텍처 지침을 제공합니다.

![용수 사용량 모니터링 아키텍처](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

개념:

1. 디바이스 및 연결  
1. IoT Central
1. 확장성 및 통합
1. 비즈니스 애플리케이션

이 문서에서는 일반적으로 용수 소비량 모니터링 솔루션에서 파트를 재생하는 주요 구성 요소에 대해 설명합니다.

## <a name="devices-and-connectivity"></a>디바이스 및 연결

용수 관리 솔루션은 유량계, 수질 모니터, 스마트 밸브, 누수 탐지기와 같은 스마트 워터 디바이스를 사용합니다.

스마트 워터 솔루션의 LPWAN(저전력 광대역 네트워크) 또는 타사 네트워크 사업자를 통해 연결할 수 있습니다. 이러한 유형의 디바이스의 경우 [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md)를 사용하여 디바이스 데이터를 Azure IoT Central의 IoT 애플리케이션으로 전송합니다. IP 기능이 포함되고 IoT Central에 직접 연결할 수 있는 디바이스 게이트웨이를 사용할 수도 있습니다.

## <a name="iot-central"></a>IoT Central

Azure IoT Central은 IoT 솔루션을 신속하게 빌드하고 배포하는 데 도움이 되는 IoT 앱 플랫폼입니다. 솔루션을 브랜딩 및 사용자 지정하거나 타사 서비스와 통합할 수 있습니다.

스마트 워터 디바이스를 IoT Central에 연결하면 애플리케이션에서 디바이스 명령 및 제어, 모니터링 및 경고, RBAC가 기본 제공된 사용자 인터페이스, 구성 가능한 대시보드, 확장성 옵션을 제공합니다.

## <a name="extensibility-and-integrations"></a>확장성 및 통합

Azure IoT Central에서 IoT 애플리케이션을 확장하고 필요에 따라 다음을 수행할 수 있습니다.

* 고급 분석을 위해 IoT 데이터를 변환하고 통합합니다(예: IoT Central 애플리케이션에서 데이터를 지속적으로 내보내 기계 학습 모델 학습).
* IoT Central 애플리케이션의 Power Automate 또는 웹후크를 사용해 작업을 트리거하여 다른 시스템의 워크플로를 자동화합니다.
* IoT Central API를 통해 IoT Central의 IoT 애플리케이션에 프로그래밍 방식으로 액세스합니다.

## <a name="business-applications"></a>비즈니스 애플리케이션

IoT 데이터를 사용하여 워터 유틸리티 내에서 다양한 비즈니스 애플리케이션을 지원할 수 있습니다. [IoT Central 용수 소비 모니터링 애플리케이션](tutorial-water-consumption-monitoring.md)에서 규칙 및 작업을 구성하고 [Connected Field Service](/dynamics365/field-service/connected-field-service)에서 경고를 만들도록 설정할 수 있습니다. IoT Central 규칙에서 Power Automate를 구성하여 애플리케이션 및 서비스에서 워크플로를 자동화합니다. 또한 Connected Field Service의 서비스 활동에 따라 정보를 Azure IoT Central로 다시 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [용수 사용량](./tutorial-water-consumption-monitoring.md) IoT Central 애플리케이션을 만드는 방법에 대해 자세히 알아보기
* [Azure IoT Central 정부 템플릿](./overview-iot-central-government.md)에 대해 자세히 알아보기
* [Azure IoT Central 개요](../core/overview-iot-central.md)를 참조하여 Azure IoT Central에 대해 자세히 알아보기
