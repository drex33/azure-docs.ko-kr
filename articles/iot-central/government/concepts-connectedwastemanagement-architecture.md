---
title: Azure IoT Central로 빌드된 연결된 폐기물 관리 솔루션을 위한 참조 아키텍처| Microsoft Docs
description: Azure IoT Central로 빌드된 연결된 폐기물 관리 솔루션에 대한 개념을 알아보세요.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 60fe5b51ffa04de79598f3f4744a8bf2bb57966a
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229902"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>연결된 폐기물 모니터링 참조 아키텍처 

**Azure IoT Central 앱 템플릿** 을 사용하여 연결된 폐기물 관리 솔루션을 스타터 IoT 애플리케이션으로 빌드할 수 있습니다. 이 문서는 엔드투엔드 솔루션 빌드에 관한 높은 수준의 참조 아키텍처를 제공합니다.

![연결된 폐기물 관리 아키텍처](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)

개념:

1. 디바이스 및 연결  
1. IoT Central
1. 확장성 및 통합
1. 비즈니스 애플리케이션

이 문서에서는 일반적으로 폐기물 관리 솔루션에서 파트를 재생하는 주요 구성 요소에 대해 설명합니다.

## <a name="devices-and-connectivity"></a>디바이스 및 연결

개방된 환경에 사용되는 쓰레기통과 같은 디바이스는 LPWAN(저전력 광대역 네트워크) 또는 타사 네트워크 운영자를 통해 연결될 수 있습니다. 이러한 유형의 디바이스의 경우 [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md)를 사용하여 디바이스 데이터를 Azure IoT Central의 IoT 애플리케이션으로 전송합니다. 또한 IP 기능이 포함되고 IoT Central에 직접 연결할 수 있는 디바이스 게이트웨이를 사용할 수도 있습니다.

## <a name="iot-central"></a>IoT Central

Azure IoT Central은 IoT 솔루션을 신속하게 빌드하고 배포하는 데 도움이 되는 IoT 앱 플랫폼입니다. 솔루션을 브랜딩 및 사용자 지정하거나 타사 서비스와 통합할 수 있습니다.

스마트 폐기물 디바이스를 IoT Central에 연결하면 애플리케이션은 디바이스 명령 및 제어, 모니터링 및 경고, RBAC가 내장된 사용자 인터페이스, 구성 가능한 대시보드, 확장성 옵션을 제공합니다.

## <a name="extensibility-and-integrations"></a>확장성 및 통합

Azure IoT Central에서 IoT 애플리케이션을 확장하고 필요에 따라 다음을 수행할 수 있습니다.

* 고급 분석을 위해 IoT 데이터 변환 및 통합(예: IoT Central 애플리케이션에서 데이터를 지속적으로 내보내 기계 학습 모델 교육)
* IoT Central 애플리케이션의 Power Automate 또는 웹후크를 사용하여 작업을 트리거하여 다른 시스템의 워크플로 자동화
* IoT Central API를 통해 IoT Central에서 IoT 애플리케이션에 프로그래밍 방식으로 액세스

## <a name="business-applications"></a>비즈니스 애플리케이션

IoT 데이터를 사용하여 폐기물 유틸리티 내에서 다양한 비즈니스 애플리케이션을 지원할 수 있습니다. 예를 들어, 연결된 폐기물 관리 솔루션에서 쓰레기 수집 트럭의 배차를 최적화할 수 있습니다. 최적화는 연결된 폐기물 수집통의 IoT 센서 데이터를 기반으로 수행할 수 있습니다. [IoT Central에 연결된 폐기물 관리 애플리케이션](./tutorial-connected-waste-management.md)에서 규칙 및 작업을 구성하고 [Connected Field Service](/dynamics365/field-service/connected-field-service)에서 경고를 생성하도록 설정할 수 있습니다. IoT Central 규칙에서 Power Automate를 구성하여 애플리케이션 및 서비스에서 워크플로를 자동화합니다. 또한 Connected Field Service의 서비스 활동에 따라 정보를 Azure IoT Central로 다시 보낼 수 있습니다.

IoT Central 및 Connected Field Service를 사용하여 다음과 같은 통합 프로세스를 쉽게 구성할 수 있습니다.

* Azure IoT Central은 진단을 위해 디바이스 이상에 대한 정보를 Connected Field Service에 보낼 수 있습니다.
* Connected Field Service는 디바이스 이상에 따라 트리거되는 케이스 또는 작업 주문을 만들 수 있습니다.
* Connected Field Service는 가동 중지 시간 문제를 방지하기 위해 기술자의 검사를 예약할 수 있습니다.
* Azure IoT Central 디바이스 대시보드는 관련 서비스 및 일정 정보로 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [연결된 폐기물 관리](./tutorial-connected-waste-management.md) Azure IoT Central 애플리케이션을 만드는 방법 알아보기
* [Azure IoT Central 정부 템플릿](./overview-iot-central-government.md)에 대해 자세히 알아보기
* [Azure IoT Central 개요](../core/overview-iot-central.md)를 참조하여 Azure IoT Central에 대해 자세히 알아보기
