---
title: Azure IoT Central이란? | Microsoft Docs
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하고 IoT 관리 작업 및 개발에 대한 부담과 및 비용을 줄여주는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 Azure IoT Central의 기능에 대한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2, contperf-fy22q1, contperf-fy22q2
ms.openlocfilehash: 2362bfae3d06a217b24f2297ea00ac6a7e3732c5
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866699"
---
# <a name="what-is-azure-iot-central"></a>Azure IoT Central이란?

IoT Central은 엔터프라이즈급 IoT 솔루션의 개발, 관리 및 유지 관리 부담과 비용을 줄이는 IoT 애플리케이션 플랫폼입니다. IoT Central을 사용하여 빌드하기로 선택하면 복잡하고 지속적으로 진화하는 IoT 인프라를 단순히 유지 관리하는 데서 그치지 않고 IoT 데이터를 활용하여 비즈니스를 전환하는 데 시간, 비용 및 에너지를 집중할 수 있습니다.

웹 UI를 사용하면 디바이스를 빠르게 연결하고, 디바이스 상태를 모니터링하고, 규칙을 만들고, 수명 주기 동안 수백만 대의 디바이스와 해당 데이터를 관리할 수 있습니다. 뿐만 아니라 IoT 인텔리전스를 LOB(기간 업무) 애플리케이션으로 확장하여 디바이스 인사이트를 기반으로 조치를 취할 수 있습니다.

이 문서에서는 IoT Central에 대해 개략적으로 설명합니다.

- 애플리케이션을 만드는 방법.
- 디바이스를 애플리케이션에 연결하는 방법
- 애플리케이션을 다른 서비스와 통합하는 방법
- 애플리케이션을 관리하는 방법
- 프로젝트와 관련된 일반적인 사용자 역할
- 가격 책정 옵션

## <a name="create-your-iot-central-application"></a>IoT Central 애플리케이션 만들기

[새로운 IoT Central 애플리케이션을 빠르게 배포](quick-deploy-iot-central.md)한 다음, 특정 요구 사항에 맞게 사용자 지정할 수 있습니다. 일반 _애플리케이션 템플릿_ 또는 산업에 초점을 둔 애플리케이션 템플릿 중 하나를 시작합니다.

- [정품](../retail/overview-iot-central-retail.md)
- [에너지](../energy/overview-iot-central-energy.md)
- [정부](../government/overview-iot-central-government.md)
- [의료](../healthcare/overview-iot-central-healthcare.md)

첫 번째 애플리케이션을 만드는 방법에 대한 연습은 [새 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

## <a name="connect-devices"></a>디바이스 연결

애플리케이션을 만든 후에 첫 번째 단계는 디바이스를 만들고 연결하는 것입니다. IoT Central에 연결된 모든 디바이스는 _디바이스 템플릿_ 을 사용합니다. 디바이스 템플릿은 다음과 같은 디바이스 유형의 특성과 동작을 정의하는 청사진입니다.

- 전송하는 원격 분석. 온도 및 습도를 예로 들 수 있습니다. 원격 분석은 스트리밍 데이터입니다.
- 운영자가 수정할 수 있는 비즈니스 속성. 고객 주소와 마지막 서비스 날짜를 예로 들 수 있습니다.
- 디바이스에서 설정하고 애플리케이션에서 읽기 전용인 디바이스 속성. 예를 들어 밸브 상태를 열림 또는 닫힘으로 설정합니다.
- 운영자가 설정하는 속성으로, 디바이스의 동작을 결정합니다. 예를 들어 디바이스의 대상 온도입니다.
- 운영자가 호출할 수 있는 디바이스에서 실행되는 명령입니다. 예를 들어, 디바이스를 원격으로 재부팅하는 명령이 해당됩니다.

이 [디바이스 템플릿](howto-set-up-template.md)에는 다음이 포함됩니다.

- 디바이스가 구현해야 하는 기능을 설명하는 _디바이스 모델_ 입니다. 디바이스 기능은 다음과 같습니다.

  - IoT Central로 스트리밍하는 원격 분석입니다.
  - IoT Central에 상태를 보고하는 데 사용하는 읽기 전용 속성입니다.
  - 디바이스 상태를 설정하기 위해 IoT Central에서 수신하는 쓰기 가능 속성입니다.
  - IoT Central에서 호출되는 명령입니다.

- 디바이스에 저장되지 않는 클라우드 속성
- IoT Central 애플리케이션의 일부인 사용자 지정, 양식 및 디바이스 보기

디바이스 템플릿을 만들 수 있는 몇 가지 옵션이 있습니다.

- IoT Central에서 디바이스 템플릿을 디자인한 다음, 디바이스 코드에서 디바이스 모델을 구현합니다.
- Visual Studio Code를 사용하여 디바이스 모델을 만들고 모델을 리포지토리에 게시합니다. 모델에서 디바이스 코드를 구현하고, 디바이스를 IoT Central 애플리케이션에 연결합니다. IoT Central은 자동으로 리포지토리에서 디바이스 모델을 찾아서 간단한 디바이스 템플릿을 만듭니다.
- Visual Studio Code를 사용하여 디바이스 모델을 만듭니다. 모델에서 디바이스 코드를 구현합니다. 디바이스 모델을 IoT Central 애플리케이션으로 수동으로 가져온 다음, IoT Central 애플리케이션에 필요한 클라우드 속성, 사용자 지정 및 보기를 추가합니다.

### <a name="customize-the-ui"></a>UI 사용자 지정

애플리케이션의 일상적인 사용을 담당하는 운영자를 위해 IoT Central 애플리케이션 UI를 사용자 지정할 수 있습니다. 사용자 지정 작업은 다음과 같습니다.

- 운영자가 인사이트를 발견하고 문제를 더 빨리 해결할 수 있도록 사용자 지정 대시보드 구성.
- 연결된 디바이스에서 시계열 데이터를 탐색하는 사용자 지정 분석 구성.
- 디바이스 템플릿의 속성 및 설정 레이아웃 정의.

## <a name="manage-your-devices"></a>디바이스 관리

운영자는 IoT Central 애플리케이션을 사용하여 IoT Central 솔루션의 [디바이스를 관리](howto-manage-devices-individually.md)합니다. 운영자는 다음과 같은 작업을 수행합니다.

- 애플리케이션에 연결된 디바이스 모니터링.
- 디바이스 관련 문제 해결 및 수정.
- 새 디바이스 프로비저닝.

연결된 디바이스의 데이터 스트리밍을 통해 작동하는 [사용자 지정 규칙 및 작업을 정의](howto-configure-rules.md)할 수 있습니다. 운영자는 디바이스 수준에서 이러한 규칙을 사용하거나 사용하지 않도록 설정하여 애플리케이션 내에서 작업을 제어하고 자동화할 수 있습니다.

대규모로 작동하도록 설계된 IoT 솔루션과 마찬가지로 디바이스 관리에 대한 체계적인 접근 방식이 중요합니다. 디바이스를 클라우드에 연결하는 것만으로는 충분 하지 않고, 디바이스를 계속 연결된 정상 상태로 유지해야 합니다. 다음과 같은 IoT Central 기능을 사용하여 애플리케이션 수명 주기 동안 디바이스를 관리합니다.

### <a name="dashboards"></a>대시보드

애플리케이션 템플릿의 미리 작성된 대시보드로 시작하거나, 운영자의 요구 사항에 맞는 맞춤형 대시보드를 만들어 보세요. 애플리케이션의 모든 사용자와 대시보드를 공유할 수도 있고, 대시보드를 비공개로 유지할 수도 있습니다.

### <a name="rules-and-actions"></a>규칙 및 동작

디바이스 상태 및 원격 분석을 기반으로 [사용자 지정 규칙](tutorial-create-telemetry-rules.md)을 만들어 주의가 필요한 디바이스를 식별합니다. 적절한 사람에게 사실을 알리고 적시에 수정 조치를 수행하도록 작업을 구성합니다.

### <a name="jobs"></a>작업

[작업](howto-manage-devices-in-bulk.md)을 사용하면 속성을 설정하거나 명령을 호출하여 디바이스에 단일 또는 대량 업데이트를 적용할 수 있습니다.

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

애플리케이션 플랫폼인 IoT Central에서는 IoT 데이터를, 실행 가능한 결과를 도출하는 비즈니스 인사이트로 변환할 수 있습니다. [규칙](./tutorial-create-telemetry-rules.md), [데이터 내보내기](./howto-export-data.md) 및 [공용 REST API](/learn/modules/manage-iot-central-apps-with-rest-api/)는 IoT Central을 LOB(기간 업무) 애플리케이션과 통합하는 방법을 보여주는 예입니다.

![IoT Central에서 IoT 데이터를 변환하는 방법](media/overview-iot-central/transform.png)

디바이스의 원격 분석을 처리하고 그 결과를 저장하는 사용자 지정 분석 파이프라인을 빌드하여 제조 현장의 머신 효율성 추세를 확인하거나 향후 에너지 사용량을 예측하는 등의 비즈니스 인사이트를 생성할 수 있습니다. IoT Central 애플리케이션에서 원격 분석을 내보내는 데이터 내보내기, 디바이스 속성 변경, 원하는 도구를 사용하여 데이터를 분석, 저장 및 시각화할 수 있는 다른 서비스로의 디바이스 템플릿 변경을 구성할 수 있습니다.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>REST API를 사용하여 사용자 지정 IoT 솔루션 빌드 및 통합

다음과 같은 IoT 솔루션을 빌드합니다.

- 디바이스를 원격으로 설정하고 제어할 수 있는 모바일 도우미 앱
- 기존 LOB(기간 업무) 애플리케이션이 IoT 디바이스 및 데이터와 상호 작용할 수 있는 사용자 지정 통합
- 디바이스 모델링, 온보딩, 관리 및 데이터 액세스를 위한 디바이스 관리 애플리케이션

## <a name="administer-your-application"></a>애플리케이션 관리

IoT Central 애플리케이션은 Microsoft에서 완벽하게 호스트하므로 애플리케이션 관리 부담이 줄어듭니다. 관리자는 [사용자 역할 및 권한](howto-administer.md)을 사용하여 애플리케이션에 대한 액세스를 관리할 수 있습니다.

## <a name="pricing"></a>가격 책정

7일 무료 평가판을 사용하여 IoT Central 애플리케이션을 만들어도 되고, 표준 가격 책정 플랜을 사용해도 됩니다.

- *무료* 플랜을 사용하여 만든 애플리케이션은 7일 동안 무료로 제공되며 최대 5대의 디바이스를 지원합니다. 만료되기 전에 언제든지 표준 가격 책정 플랜을 사용하도록 변환할 수 있습니다.
- *표준* 플랜을 사용하여 만든 애플리케이션은 디바이스별로 청구되며, 처음 두 디바이스가 무료로 사용 가능한 상태에서 **표준 0**, **표준 1** 또는 **표준 2** 가격 책정 플랜을 선택할 수 있습니다. [IoT Central 가격 책정](https://aka.ms/iotcentral-pricing)에 대해 자세히 알아보세요.

## <a name="user-roles"></a>사용자 역할

IoT Central 설명서는 IoT Central 애플리케이션과 상호 작용하는 네 개의 사용자 역할을 나타냅니다.

- _솔루션 작성기_ 는 [애플리케이션 생성](quick-deploy-iot-central.md), [규칙 및 작업 구성](quick-configure-rules.md), [다른 서비스와의 통합 정의](quick-export-data.md) 및 운영자와 디바이스 개발자를 위해 애플리케이션을 추가로 사용자 지정하는 작업을 담당합니다.
- _운영자_ 는 애플리케이션에 연결된 [디바이스를 관리](howto-manage-devices-individually.md)합니다.
- _관리자_ 는 애플리케이션 내 [사용자 역할 및 권한](howto-administer.md) 관리와 [관리 ID의 다른 서비스로의 안전한 연결 구성](howto-manage-iot-central-from-portal.md#configure-a-managed-identity)과 같은 관리 작업을 담당합니다.
- _디바이스 개발자_ 는 애플리케이션에 연결된 [디바이스에서 실행되는 코드를 만들](concepts-telemetry-properties-commands.md)거나 [IoT Edge 모듈](concepts-iot-edge.md)을 만듭니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Central에 대한 개요를 살펴보았으므로 다음 몇가지 제안된 단계는 다음과 같습니다.

- 디바이스 개발자이고 몇 가지 코드에 대해 자세히 알아보려면 다음 단계를 [클라이언트 애플리케이션을 만들고 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md)하는 것이 좋습니다.
- [Azure IoT Central UI](overview-iot-central-tour.md)를 숙지합니다.
- [Azure IoT Central 애플리케이션을 생성](quick-deploy-iot-central.md)하여 시작합니다.
