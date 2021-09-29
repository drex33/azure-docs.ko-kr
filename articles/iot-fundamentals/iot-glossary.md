---
title: Azure IoT 용어집 | Microsoft Docs
description: 개발자 가이드 - Azure IoT 문서에 사용된 일반 용어 몇 가지를 설명하는 용어집입니다.
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff8726ff95bd8e5e3c24fecb90d52ec48529ef7b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236013"
---
# <a name="glossary-of-iot-terms"></a>IoT 용어집

이 문서에서는 IoT 문서에 사용된 일반 용어 몇 가지를 나열합니다.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>고급 메시지 큐 프로토콜

[AMQP(고급 메시지 큐 프로토콜)](https://www.amqp.org/)는 [IoT Hub](#iot-hub)에서 디바이스와 통신을 위해 지원하는 메시징 프로토콜 중 하나입니다. IoT Hub에서 지원하는 메시징 프로토콜에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

### <a name="allocation-policy"></a>할당 정책

[Device Provisioning Service](#device-provisioning-service)에서 할당 정책은 서비스가 [연결된 IoT 허브](#linked-iot-hub)에 디바이스를 할당하는 방법을 결정합니다.

### <a name="attestation-mechanism"></a>증명 메커니즘

[Device Provisioning Service](#device-provisioning-service)에서 증명 메커니즘은 디바이스의 ID를 확인하는 데 사용되는 방법입니다. 증명 메커니즘은 [등록](#enrollment)에서 구성됩니다.

증명 메커니즘에는 X.509 인증서, 신뢰 플랫폼 모듈, 대칭 키가 포함됩니다.

### <a name="automatic-deployment"></a>자동 배포

IoT Edge에서 자동 배포를 통해 IoT Edge 디바이스의 대상 집합이 IoT Edge 모듈 집합을 실행하도록 구성됩니다. 각 배포는 새 디바이스가 만들어지거나 대상 조건과 일치하도록 수정되더라도 대상 조건과 일치하는 모든 디바이스가 지정된 모듈 집합을 실행 중인지 계속 확인합니다. 각 IoT Edge 디바이스는 해당 대상 조건을 충족하는 가장 높은 우선 순위 배포만 수신합니다. [IoT Edge 자동 배포](../iot-edge/module-deployment-monitoring.md)에 대해 자세히 알아보세요.

### <a name="automatic-device-configuration"></a>자동 디바이스 구성

솔루션 백 엔드는 [자동 디바이스 구성](../iot-hub/iot-hub-automatic-device-management.md)을 사용하여 [디바이스 쌍](#device-twin)의 집합에 원하는 속성을 할당하고 시스템 메트릭 및 사용자 지정 메트릭을 사용하여 상태를 보고할 수 있습니다.

### <a name="automatic-device-management"></a>자동 디바이스 관리

Azure IoT Hub에서 자동 디바이스 관리는 전체 수명 주기를 통해 대규모 디바이스를 관리하는 반복적이고 복잡한 작업을 자동화합니다. 자동 디바이스 관리를 사용하여 해당 속성을 기반으로 디바이스 집합을 대상으로 지정하고, 원하는 구성을 정의하고, 범위에 나올 때마다 IoT Hub에서 디바이스를 업데이트하도록 할 수 있습니다.  [자동 디바이스 구성](../iot-hub/iot-hub-automatic-device-management.md) 및 [IoT Edge 자동 배포](../iot-edge/how-to-deploy-at-scale.md)로 구성됩니다.

### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins는 실제 사물, 장소, 비즈니스 프로세스 및 사람들의 디지털 재현을 만들기 위한 PaaS(platform as a service) 제공입니다. 전체 환경을 나타내는 쌍 그래프를 작성하고, 이를 사용하여 더 나은 제품을 구동하고, 운영 및 비용을 최적화하며, 혁신적인 고객 경험을 만들기 위한 인사이트를 얻습니다. 자세한 내용은 [Azure Digital Twins](../digital-twins/index.yml)를 참조하십시오.

### <a name="azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스

고객의 구독에 있는 Azure Digital Twins 서비스의 단일 인스턴스입니다. [Azure Digital Twins](#azure-digital-twins)가 전체 Azure 서비스를 가리키는 반면, Azure Digital twins **인스턴스** 는 개별 Azure Digital Twins 리소스입니다.

### <a name="azure-iot-device-sdks"></a>Azure IoT 디바이스 SDK

여러 언어로 된 _디바이스 SDK_ 가 제공되어 이를 통해 IoT Hub와 상호 작용하는 [디바이스 앱](#device-app)을 만들 수 있습니다. IoT Hub 자습서는 이러한 디바이스 SDK를 사용하는 방법을 보여 줍니다. 이 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdks)에서 디바이스 SDK에 대한 소스 코드와 추가 정보를 확인할 수 있습니다.

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

[Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer)는 디바이스에서 전송 중인 원격 분석을 보고, 디바이스 속성을 사용하고, 명령을 호출하는 데 사용됩니다. 또한, 디바이스를 대상으로 상호 작용 및 테스트를 하고 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 관리할 수 있습니다.

### <a name="azure-iot-service-sdks"></a>Azure IoT 서비스 SDK

여러 언어로 된 _장치 SDK_ 가 제공되어 이를 통해 IoT Hub와 상호 작용하는 [백 엔드 앱](#back-end-app)을 만들 수 있습니다. IoT Hub 자습서는 이러한 서비스 SDK를 사용하는 방법을 보여 줍니다. 이 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdks)에서 서비스 SDK에 대한 소스 코드와 추가 정보를 확인할 수 있습니다.

### <a name="azure-iot-tools"></a>Azure IoT Tools

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)는 VS Code에서 디바이스 및 Azure IoT Hub 관리에 도움이 되는 플랫폼 간 오픈 소스 Visual Studio Code 확장입니다. IoT 개발자는 Azure IoT Tools를 사용하여 손쉽게 VS Code에서 IoT 프로젝트를 개발할 수 있습니다.

## <a name="b"></a>b

### <a name="back-end-app"></a>백 엔드 앱

[IoT Hub](#iot-hub)의 컨텍스트에서 백 엔드 앱은 IoT Hub에서 서비스 지향 엔드포인트 중 하나에 연결되는 앱입니다. 예를 들어 백 엔드 앱은 [디바이스-클라우드](#device-to-cloud) 메시지를 검색하거나 [ID 레지스트리](#identity-registry)를 관리할 수 있습니다. 일반적으로 백 엔드 앱은 클라우드에서 실행되지만 대부분의 자습서에 나오는 백 엔드 앱은 로컬 개발 환경에서 실행 중인 콘솔 앱입니다.

### <a name="built-in-endpoints"></a>기본 제공 엔드포인트

IoT Hub에 기본 제공되는 [엔드포인트](#endpoint)의 형식입니다. 모든 IoT Hub에는 Event Hub와 호환되는 기본 제공 [엔드포인트](../iot-hub/iot-hub-devguide-endpoints.md)가 있습니다. Event Hubs와 함께 작동하는 모든 메커니즘을 사용하여 이 엔드포인트에서 디바이스-클라우드 메시지를 읽을 수 있습니다.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>클라우드 게이트웨이

클라우드 게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 디바이스의 연결이 가능합니다. 클라우드 게이트웨이는 디바이스에 대해 로컬로 시행되는 [필드 게이트웨이](#field-gateway)와 달리, 클라우드에서 호스트됩니다. 클라우드 게이트웨이를 위한 일반 사용 사례는 디바이스에 대한 프로토콜 변환을 구현하는 것입니다.

### <a name="cloud-property"></a>클라우드 속성

IoT Central에서 클라우드 속성은 [디바이스 템플릿](#device-model)의 일부이지만 [디바이스 모델](#device-model)의 일부는 아닙니다. 클라우드 속성을 사용하면 솔루션 개발자가 IoT Central 애플리케이션에 저장할 디바이스 메타데이터를 지정할 수 있습니다. 클라우드 속성은 디바이스 개발자가 디바이스 모델을 구현하기 위해 작성하는 코드에 영향을 주지 않습니다.

### <a name="cloud-to-device"></a>클라우드-디바이스

IoT Hub에서 연결된 디바이스로 전송되는 메시지를 참조하세요. 대개 이러한 메시지는 디바이스에 작업을 수행하도록 하는 명령입니다. 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

### <a name="commands"></a>명령

IoT 플러그 앤 플레이에서 [인터페이스](#interface)에 정의된 명령은 [디지털 트윈](#digital-twin)에서 실행할 수 있는 메서드를 나타냅니다. 예를 들어, 디바이스를 재부팅하는 명령이 여기에 해당합니다.

### <a name="component"></a>구성 요소

IoT 플러그 앤 플레이 및 Azure Digital Twins에서 구성 요소를 사용하여 다른 인터페이스의 어셈블리로 모델 [인터페이스](#interface)를 빌드할 수 있습니다. [디바이스 모델](#device-model)은 여러 인터페이스를 구성 요소로 결합할 수 있습니다. 예를 들어 모델에는 스위치 구성 요소와 온도 조절 디바이스 구성 요소가 포함될 수 있습니다. 모델의 여러 구성 요소가 동일한 인터페이스 형식을 사용할 수도 있습니다. 예를 들어 하나의 모델에 두 개의 온도 조절 디바이스 구성 요소가 포함될 수 있습니다.

### <a name="configuration"></a>구성

[자동 디바이스 구성](../iot-hub/iot-hub-automatic-device-management.md)의 컨텍스트에서 IoT Hub 내의 구성은 디바이스 쌍의 집합에 대해 원하는 구성을 정의하고 상태 및 진행률을 보고하도록 메트릭 집합을 제공합니다.

### <a name="connection-string"></a>연결 문자열

앱 코드에서 연결 문자열을 사용하여 엔드포인트에 연결하는 데 필요한 정보를 캡슐화할 수 있습니다. 일반적으로 연결 문자열에는 엔드포인트의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. IoT Hub 서비스와 연관된 연결 문자열에는 다음 두 종류가 있습니다.

- *디바이스 연결 문자열* 은 IoT hub의 디바이스 연결 엔드포인트에 연결하는 디바이스를 사용하도록 설정합니다.

- *IoT Hub 연결 문자열* 은 IoT hub의 서비스 연결 엔드포인트에 연결하는 백 엔드 앱을 사용하도록 설정합니다.

### <a name="custom-endpoints"></a>사용자 지정 엔드포인트

IoT Hub에 사용자 지정 [엔드포인트](../iot-hub/iot-hub-devguide-endpoints.md)를 만들어 [라우팅 규칙](#routing-rules)으로 발송된 메시지를 배달할 수 있습니다. 사용자 지정 엔드포인트는 Event Hub, Service Bus 큐 또는 Service Bus 토픽에 직접 연결됩니다.

### <a name="custom-gateway"></a>사용자 지정 게이트웨이

게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 디바이스의 연결이 가능합니다. Azure IoT Edge를 사용하여 메시지, 사용자 지정 프로토콜 변환 및 에지의 기타 프로세스를 처리하도록 사용자 지정 논리를 구현하는 사용자 지정 게이트웨이를 빌드할 수 있습니다.

## <a name="d"></a>D

### <a name="data-point-message"></a>데이터 요소 메시지

데이터 요소 메시지는 풍속 또는 온도와 같은 [원격 분석](#telemetry) 데이터를 포함하는 [클라우드-디바이스](#device-to-cloud) 메시지입니다.

### <a name="default-component"></a>기본 구성 요소

IoT 플러그 앤 플레이에서 모든 [디바이스 모델](#device-model)에는 기본 구성 요소가 있습니다. 단순 디바이스 모델에는 기본 구성 요소만 있습니다. 이러한 모델은 구성 요소가 없는 디바이스라고도 알려져 있습니다. 더 복잡한 모델에는 기본 구성 요소 아래에 중첩된 여러 구성 요소가 있습니다.

### <a name="deployment-manifest"></a>배포 매니페스트

[IoT Edge](#iot-edge)에서 배포 매니페스트는 모듈, 경로 및 관련된 모듈의 원하는 속성 집합을 배포할 하나 이상의 IoT Edge 디바이스 모듈 쌍에 복사될 정보를 포함하는 Json 문서입니다.

### <a name="desired-configuration"></a>desired 구성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 desired 구성은 디바이스와 동기화해야 하는 디바이스의 속성 및 메타데이터 전체 집합을 나타냅니다.

### <a name="desired-properties"></a>Desired 속성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 desired 속성은 [reported 속성](#reported-properties)과 함께 디바이스 구성 또는 상황을 동기화하는 데 사용되는 디바이스 쌍의 하위 섹션입니다. desired 속성은 [백 엔드 앱](#back-end-app)에서만 설정할 수 있고 [디바이스 앱](#device-app)에서 관찰할 수 있습니다.

### <a name="device"></a>디바이스

IoT의 컨텍스트에서 일반적으로 디바이스는 데이터를 수집하거나 다른 디바이스를 제어할 수 있는 소규모의 독립 실행형 컴퓨팅 디바이스입니다. 예를 들어 디바이스는 환경 모니터링 디바이스이거나 온실에서 급수 및 통풍 시스템을 위한 컨트롤러일 수 있습니다. [디바이스 카탈로그](https://catalog.azureiotsolutions.com/)는 [IoT Hub](#iot-hub)에서 작동이 인증된 하드웨어 디바이스 목록을 제공합니다.

### <a name="device-app"></a>디바이스 앱

디바이스 앱은 [디바이스](#device)에서 실행되며 [IoT Hub](#iot-hub)와의 통신을 처리합니다. 일반적으로 디바이스 앱을 구현할 때 [Azure IoT 디바이스 SDK](#azure-iot-device-sdks) 중 하나를 사용합니다. 대부분의 IoT 자습서에서는 편의를 위해 [시뮬레이트된 디바이스](#simulated-device)를 사용합니다.

### <a name="device-builder"></a>디바이스 작성기

디바이스 작성기는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 실행되도록 코드를 구현할 때 [디바이스 모델](#device-model) 및 [인터페이스](#interface)를 사용합니다. 일반적으로 디바이스 빌더는 [Azure IoT 디바이스 SDK들](#azure-iot-device-sdks) 중 하나를 사용하여 디바이스 클라이언트를 구현합니다.

### <a name="device-certification"></a>디바이스 인증

IoT 플러그 앤 플레이 디바이스 인증 프로그램은 디바이스가 IoT 플러그 앤 플레이 인증 요구 사항을 충족하는지 확인합니다. 인증된 디바이스를 공용 [Azure IoT 디바이스 카탈로그 인증](https://aka.ms/devicecatalog)에 추가할 수 있습니다.

### <a name="device-condition"></a>디바이스 조건

[디바이스 앱](#device-app)에서 보고된 디바이스 상태 정보(예: 현재 사용 중인 연결 방법)를 나타냅니다. [디바이스 앱](#device-app)은 해당 기능도 보고할 수 있습니다. 디바이스 쌍을 사용하여 조건 및 기능 정보를 쿼리할 수 있습니다.

### <a name="device-data"></a>디바이스 데이터

디바이스 데이터는 IoT Hub [ID 레지스트리](#identity-registry)에 저장된 디바이스 단위 데이터를 나타냅니다. 이 데이터를 가져오고 내보낼 수 있습니다.

### <a name="device-identity"></a>디바이스 ID

디바이스 ID는 IoT Hub [ID 레지스트리](#identity-registry)에 등록되어 있는 모든 디바이스에 할당된 고유 식별자입니다.

### <a name="device-management"></a>디바이스 관리

디바이스 관리에는 계획, 프로비전, 구성, 모니터링 및 사용 중지를 비롯하여 IoT 솔루션에서 디바이스 관리와 관련된 전체 수명 주기가 포함됩니다.

### <a name="device-management-patterns"></a>디바이스 관리 패턴

[IoT Hub](#iot-hub)를 통해 디바이스에서 재부팅, 공장 재설정 수행 및 펌웨어 업데이트 수정을 비롯한 일반적인 디바이스 관리 패턴을 수행할 수 있습니다.

### <a name="device-model"></a>디바이스 모델

디바이스 모델은 [Ditigal Twins Definition Language](#digital-twins-definition-language-dtdl)를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 설명하는 [모델](#model) 형식입니다. 간단한 디바이스 모델은 단일 인터페이스를 사용하여 디바이스 기능을 설명합니다. 더 복잡한 디바이스 모델에는 여러 구성 요소가 포함되며, 각 구성 요소는 기능 집합을 설명합니다. 자세한 내용은 [모델의 IoT 플러그 앤 플레이 구성 요소](../iot-develop/concepts-modeling-guide.md)를 참조하십시오.

### <a name="device-modeling"></a>디바이스 모델링

[디바이스 작성기](#device-builder) 또는 [모듈 작성기](#module-builder)는 [디지털 쌍 정의 언어](#digital-twins-definition-language-dtdl)를 사용하여 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)의 기능을 모델링합니다. [솔루션 작성기](#solution-builder)는 모델에서 IoT 솔루션을 구성할 수 있습니다.

### <a name="device-provisioning"></a>디바이스 프로비전

[디바이스 프로비전](#device-data)은 솔루션 저장소에 초기 디바이스 데이터를 추가하는 프로세스입니다. 새 디바이스를 허브에 연결하도록 하려면 디바이스 ID 및 키를 IoT Hub [ID 레지스트리](#identity-registry)에 추가해야 합니다. 프로비전 프로세스의 일부로, 다른 솔루션 저장소에서 디바이스 특정 데이터를 초기화해야 할 수 있습니다.

### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub DPS(Device Provisioning Service)는 지정된 IoT 허브에 대한 제로 터치 디바이스 프로비저닝을 구성하기 위해 사용하는 [IoT Hub](#iot-hub)에 대한 도우미 서비스입니다. DPS를 사용하여 안전하고 확장 가능한 방식으로 수백만 개의 디바이스를 프로비전할 수 있습니다.

### <a name="device-rest-api"></a>디바이스 REST API

디바이스에서 [디바이스 REST API](/rest/api/iothub/device)를 사용하여 디바이스-클라우드 메시지를 IoT Hub로 보내고 IoT Hub에서 [클라우드-디바이스](#cloud-to-device) 메시지를 수신할 수 있습니다. 일반적으로 IoT Hub 자습서에 나와 있는 것보다 상위 수준의 [디바이스 SDK](#azure-iot-device-sdks)를 사용해야 합니다.

### <a name="device-template"></a>디바이스 템플릿

IoT Central에서 [디바이스 템플릿](../iot-central/core/concepts-device-templates.md)은 애플리케이션에 연결하는 디바이스 유형의 특징과 동작을 정의하는 청사진입니다. 예를 들어 디바이스 템플릿은 IoT Central이 올바른 단위와 데이터 형식을 사용하는 시각화를 만들 수 있도록 디바이스에서 보내는 원격 분석 데이터를 정의합니다. [디바이스 모델](#device-model)은 디바이스 템플릿의 일부입니다.

### <a name="device-twin"></a>디바이스 쌍

디바이스 쌍은 디바이스의 상태 정보(메타데이터, 구성 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 IoT Hub에 프로비전하는 각 디바이스에 대해 하나의 디바이스 쌍을 유지합니다. 디바이스 쌍을 통해 디바이스 및 솔루션 백 엔드 간의 디바이스 조건 및 구성을 동기화할 수 있습니다. 디바이스 쌍을 쿼리하여 특정 디바이스를 찾고 장기 실행 중인 작업의 상태를 확인할 수 있습니다.

### <a name="device-to-cloud"></a>디바이스-클라우드

연결된 디바이스에서 [IoT Hub](#iot-hub)로 전송되는 메시지를 참조하세요. 이러한 메시지는 [데이터 요소](#data-point-message) 또는 [대화형](#interactive-message) 메시지일 수 있습니다. 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

### <a name="digital-twin"></a>디지털 쌍

디지털 쌍은 물리적 개체를 나타내는 디지털 데이터의 컬렉션입니다. 물리적 개체의 변경 내용은 디지털 쌍에 반영됩니다. 일부 시나리오에서는 디지털 쌍을 사용하여 물리적 개체를 조작할 수 있습니다. [Azure Ditigal Twins 서비스](../digital-twins/index.yml)는 [DTDL(디지털 쌍 정의 언어)](#digital-twins-definition-language-dtdl)로 표현된 [모델](#model)을 사용하여 물리적 디바이스의 디지털 쌍 또는 상위 수준의 추상적 비즈니스 개념을 나타냅니다. 이를 통해 다양한 클라우드 기반 디지털 쌍 솔루션을 사용할 수 있습니다. [IoT 플러그 앤 플레이](../iot-develop/index.yml) 디바이스에는 DTDL [디바이스 모델](#device-model)에서 설명한 디지털 쌍이 있습니다.

### <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)가 IoT 허브에 연결된 경우 허브는 라우팅 기능을 사용하여 디지털 쌍 변경 내용에 대한 알림을 보낼 수 있습니다. 예를 들어 디바이스에서 [속성](#properties) 값이 변경될 때마다 IoT Hub는 Event 허브와 같은 엔드포인트로 알림을 보낼 수 있습니다.

### <a name="digital-twin-route"></a>디지털 쌍 경로

[디지털 쌍 변경 이벤트](#digital-twin-change-events)를 엔드포인트(예: Event 허브)에 전달하기 위해 IoT 허브에 설정된 경로입니다.

### <a name="digital-twins-definition-language-dtdl"></a>DTDL(디지털 쌍 정의 언어)

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)와 [Azure Digital Twins](../digital-twins/index.yml) 엔터티에 대한 [모델](#model) 및 [인터페이스](#interface)를 설명하는 JSON-LD 언어입니다. [디지털 쌍 정의 언어 버전 2](https://github.com/Azure/opendigitaltwins-dtdl)를 사용하여 [디지털 쌍](#digital-twin)의 기능을 설명하고 IoT 플랫폼과 IoT 솔루션에서 엔터티의 의미 체계를 활용할 수 있도록 합니다. 디지털 쌍 정의 언어는 종종 DTDL로 축약해 사용합니다.

### <a name="direct-method"></a>직접 메서드

[직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)는 IoT Hub에서 API를 호출하여 디바이스에서 실행할 메서드를 트리거하는 방법입니다.

### <a name="downstream-services"></a>다운스트림 서비스

현재 컨텍스트에서 데이터를 수신하는 서비스를 설명하는 상대 용어입니다. 예를 들어 Azure digital Twins의 컨텍스트에서 생각하는 경우 Azure Digital Twins에서 Time Series Insights로 이동하도록 데이터를 설정하면 [Time Series Insights](../time-series-insights/index.yml)는 다운스트림 서비스로 간주됩니다.

## <a name="e"></a>E

### <a name="endpoint"></a>엔드포인트

다른 서비스에서 데이터를 받을 수 있는 데이터 라우팅 서비스의 명명된 표현입니다.

IoT Hub는 앱을 IoT Hub에 연결할 수 있도록 해주는 여러 [엔드포인트](../iot-hub/iot-hub-devguide-endpoints.md)를 노출합니다. 디바이스가 [디바이스-클라우드](#device-to-cloud) 메시지 보내기 및 [클라우드-디바이스](#cloud-to-device) 메시지 받기와 같은 작업을 수행할 수 있도록 해주는 디바이스 지향 엔드포인트가 있습니다. [백 엔드 앱](#back-end-app)이 [디바이스 ID](#device-identity) 관리 및 디바이스 쌍 관리와 같은 작업을 수행할 수 있도록 하는 서비스 지향 관리 엔드포인트가 있습니다. 디바이스-클라우드 메시지 를 읽기 위한 서비스 지향 [기본 제공 엔드포인트](#built-in-endpoints)가 있습니다. [사용자 지정 엔드포인트](#custom-endpoints)를 만들어 [라우팅 규칙](#routing-rules)으로 발송된 디바이스-클라우드 메시지를 받을 수 있습니다.

### <a name="enrollment"></a>등록

[Device Provisioning Service](#device-provisioning-service)에서 등록은 자동 프로비저닝을 통해 [연결된 IoT 허브](#linked-iot-hub)에 등록할 수 있는 개별 디바이스 또는 디바이스 그룹의 레코드입니다.

### <a name="enrollment-group"></a>등록 그룹

[Device Provisioning Service](#device-provisioning-service)에서 등록 그룹은 X.509 또는 대칭 키 [증명 메커니즘](#attestation-mechanism)을 공유하는 디바이스 그룹을 식별합니다.

### <a name="event-handlers"></a>이벤트 처리기

이벤트 처리기란 이벤트 도착에 의해 트리거되는 모든 프로세스를 가리킬 수 있으며 일부 처리 작업을 수행합니다. 이벤트 처리기를 만드는 한 가지 방법은 Azure Function에 이벤트 처리 코드를 추가하고 [엔드포인트](#endpoint) 및 [이벤트 라우팅](#event-routing)을 사용하여 데이터를 전송하는 것입니다.

### <a name="event-hub-compatible-endpoint"></a>Event Hub 호환 엔드포인트

IoT Hub에 전송된 [디바이스-클라우드](#device-to-cloud) 메시지를 읽으려면 허브에 있는 엔드포인트에 연결하고 Event Hub 호환 메서드를 사용하여 해당 메시지를 읽을 수 있습니다. 모든 Event Hub 호환 메서드에는 [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) 및 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 사용이 포함됩니다.

### <a name="event-routing"></a>이벤트 라우팅

하나의 디바이스나 서비스에서 다른 디바이스나 서비스의 [엔드포인트](#endpoint)로 이벤트와 해당 데이터를 보내는 프로세스입니다. 

Iot Hub에서는 메시지를 전송하는 방법을 설명하는 [라우팅 규칙](#routing-rules)을 정의할 수 있습니다. Azure Digital Twins에서 이벤트 경로는 이 용도로 만들어진 엔터티입니다. Azure Digital Twins 이벤트 경로에는 각 엔드포인트로 전송되는 이벤트 형식을 제한하는 필터가 포함될 수 있습니다.

## <a name="f"></a>F

### <a name="field-gateway"></a>필드 게이트웨이

필드 게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 디바이스의 연결이 가능하며 일반적으로 디바이스와 함께 로컬로 배포됩니다. 자세한 내용은 [Azure IoT Hub란?](../iot-hub/about-iot-hub.md)을 참조하세요.

## <a name="g"></a>G

### <a name="gateway"></a>게이트웨이

게이트웨이를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 디바이스의 연결이 가능합니다. 참고 항목: [필드 게이트웨이](#field-gateway), [클라우드 게이트웨이](#cloud-gateway) 및 [사용자 지정 게이트웨이](#custom-gateway)

### <a name="gateway-device"></a>게이트웨이 디바이스

디바이스는 [필드 게이트웨이](#field-gateway)의 예입니다. 게이트웨이 디바이스는 표준 IoT [디바이스](#device) 또는 [IoT Edge 디바이스](#iot-edge-device)가 될 수 있습니다.

게이트웨이 디바이스를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 다운스트림 디바이스의 연결이 가능합니다.

## <a name="h"></a>H

### <a name="hardware-security-module"></a>하드웨어 보안 모듈

HSM(하드웨어 보안 모듈)은 디바이스 비밀을 안전한 하드웨어 기반 스토리지에 저장하는 데 사용됩니다. 디바이스를 위한 가장 안전한 형태의 암호 스토리지입니다. X.509 인증서와 대칭 키는 모두 HSM에 저장될 수 있습니다. [Device Provisioning Service](#device-provisioning-service)에서 [증명 메커니즘](#attestation-mechanism)은 HSM을 사용할 수 있습니다.

## <a name="i"></a>I

### <a name="id-scope"></a>ID 범위

ID 범위는 생성 될 때 [DPS(Device Provisioning Service)](#device-provisioning-service) 인스턴스에 할당되는 고유 값입니다.

IoT Central 애플리케이션은 DPS 인스턴스를 사용하고 IoT Central UI를 통해 ID Scope를 사용할 수 있도록 설정합니다.

### <a name="identity-registry"></a>ID 레지스트리

[ID 레지스트리](../iot-hub/iot-hub-devguide-identity-registry.md)는 IoT Hub에 연결이 허용된 개별 디바이스에 대한 정보를 저장하는 IoT Hub의 기본 제공 구성 요소입니다.

### <a name="individual-enrollment"></a>개별 등록

[Device Provisioning Service](#device-provisioning-service)에서 개별 등록은 X.509 리프 인증서 또는 대칭 키를 [증명 메커니즘](#attestation-mechanism)으로 사용하는 단일 디바이스를 식별합니다.

### <a name="interactive-message"></a>대화형 메시지

대화형 메시지는 솔루션 백 엔드에서 즉각적인 작업을 트리거하는 [클라우드-디바이스](#cloud-to-device) 메시지입니다. 예를 들어 디바이스는 CRM 시스템에 자동으로 로깅되는 실패에 대한 경보를 보낼 수 있습니다.

### <a name="interface"></a>인터페이스

IoT 플러그 앤 플레이에서 인터페이스는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 또는 [디지털 쌍](#digital-twin)에서 구현하는 관련 기능을 설명합니다. 여러 [디바이스 모델](#device-model)에서 인터페이스를 다시 사용할 수 있습니다. 인터페이스는 디바이스 모델에서 사용되는 경우 디바이스의 [구성 요소](#component)를 정의합니다. 단순 디바이스에는 기본 인터페이스만 포함됩니다.

Azure Digital Twins에서 *인터페이스* 로 [DTDL](#digital-twins-definition-language-dtdl) 모델 정의의 최상위 코드 항목을 참조할 수 있습니다.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge에서는 클라우드 방식을 통해 Azure 서비스 및 솔루션별 코드를 온-프레미스 디바이스에 배포할 수 있습니다. [IoT Edge 디바이스](#iot-edge-device)는 다른 디바이스의 데이터를 집계한 후 데이터를 클라우드로 전송하기 전에 계산 및 분석을 수행할 수 있습니다. 자세한 내용은 [Azure IoT Edge](../iot-edge/index.yml)를 참조하십시오.

### <a name="iot-edge-agent"></a>IoT Edge 에이전트

모듈 배포 및 모니터링을 담당하는 IoT Edge 런타임의 부분입니다.

### <a name="iot-edge-device"></a>IoT Edge 디바이스

IoT Edge 디바이스는 컨테이너화된 IoT Edge [모듈](#modules)을 사용하여 Azure 서비스, 외부업체 서비스 또는 사용자 코드를 실행합니다. IoT Edge 디바이스에서 [IoT Edge 런타임](#iot-edge-runtime)은 모듈을 관리합니다. 클라우드에서 IoT Edge 디바이스를 원격으로 모니터링하고 관리할 수 있습니다.

### <a name="iot-edge-hub"></a>IoT Edge 허브

모듈 간 통신, 업스트림(IoT Hub 쪽) 및 다운스트림(IoT Hub 반대쪽) 통신을 담당하는 IoT Edge 런타임의 일부입니다.

### <a name="iot-edge-runtime"></a>IoT Edge 런타임

IoT Edge 런타임은 Microsoft가 IoT Edge 디바이스에 설치되도록 배포하는 모든 항목입니다. 여기에는 Edge 에이전트, Edge 허브 및 IoT Edge 보안 디먼이 포함됩니다.

### <a name="iot-extension-for-azure-cli"></a>Azure CLI에 대한 IoT 확장

[Azure CLI에 대한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension)은 플랫폼 간 명령줄 도구입니다. 이 도구를 통해 [ID 레지스트리](#identity-registry)에서 디바이스를 관리하고 디바이스에서 메시지 및 파일을 보내고 받으며 IoT Hub 작업을 모니터링할 수 있습니다.

### <a name="iot-hub"></a>IoT Hub

IoT Hub는 수백만의 디바이스와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 Azure 서비스입니다. 자세한 내용은 [Azure IoT Hub란?](../iot-hub/about-iot-hub.md)을 참조하세요. Azure 구독을 사용하여 IoT 메시징 워크로드를 처리하는 IoT Hub를 만들 수 있습니다.

### <a name="iot-hub-metrics"></a>IoT Hub 메트릭

IoT Hub 메트릭은 Azure 구독의 IoT Hub 상태에 대한 데이터를 제공합니다. IoT Hub 메트릭을 통해 서비스와 연결된 디바이스의 전반적인 상태를 평가할 수 있습니다. IoT Hub 메트릭을 통해 Azure 지원 센터에 문의할 필요 없이 IoT Hub의 상황을 파악하고 근본 원인을 조사할 수 있습니다. 자세히 알아보려면 [IoT Hub 모니터링](../iot-hub/monitor-iot-hub.md)을 참조하세요.

### <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어

[IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-query-language.md)는 SQL과 비슷한 언어로, [작업](#job), 디지털 쌍, 디바이스 쌍을 쿼리할 수 있습니다.

### <a name="iot-hub-resource-rest-api"></a>IoT Hub 리소스 REST API

[IoT Hub 리소스 REST API](/rest/api/iothub/iothubresource)를 사용하여 허브 만들기, 업데이트 및 삭제와 같은 작업을 수행하는 Azure 구독에서 IoT Hub를 관리할 수 있습니다.

### <a name="iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지

IoT 플러그 앤 플레이 브리지는 Windows 또는 Linux 게이트웨이에 연결된 기존 센서와 주변 디바이스를 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)로 연결하는 데 사용할 수 있는 오픈 소스 애플리케이션입니다.

### <a name="iot-plug-and-play-conventions"></a>IoT 플러그 앤 플레이 규칙

IoT 플러그 앤 플레이 [디바이스](#iot-plug-and-play-device)는 솔루션을 사용하여 데이터를 교환할 때 일련의 규칙을 따라야 합니다.

### <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 디바이스

IoT 플러그 앤 플레이 디바이스는 일반적으로 데이터를 수집하거나 다른 디바이스를 제어하고 [디바이스 모델](#device-model)을 구현하는 소프트웨어 또는 펌웨어를 실행하는 소규모의 독립 실행형 컴퓨팅 디바이스입니다.  예를 들어, IoT 플러그 앤 플레이 디바이스는 환경 모니터링 디바이스이거나 스마트 농업 관개 시스템용 컨트롤러일 수 있습니다. IoT 플러그 앤 플레이 디바이스는 직접 또는 IoT Edge 모듈로 구현할 수 있습니다. IoT 플러그 앤 플레이 디바이스에서 명령을 하고 제어하고 데이터를 수신하기 위한 클라우드 호스티트 IoT 솔루션을 작성할 수 있습니다.

## <a name="j"></a>J

### <a name="job"></a>작업

[IoT Hub](#iot-hub)에서는 [작업](../iot-hub/iot-hub-devguide-jobs.md)을 통해 IoT Hub에 등록된 디바이스 세트에 대한 활동을 예약하고 추적할 수 있습니다. 활동에는 디바이스 쌍 [desired 속성](#desired-properties) 업데이트, 디바이스 쌍 [태그](#tags) 업데이트 및 [직접 메서드](#direct-method) 호출이 포함됩니다. IoT Hub는 [ID 레지스트리](#identity-registry)에서 [가져오고 내보내는](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) 작업도 사용합니다.

IoT Central에서는 [작업](../iot-central/core/howto-manage-devices-in-bulk.md)을 통해 속성을 설정하고 명령을 호출하여 연결된 디바이스를 대량으로 관리할 수 있습니다. 또한 IoT Central 작업을 통해 [클라우드 속성](#cloud-property)을 대량으로 업데이트할 수 있습니다.

## <a name="l"></a>L

### <a name="leaf-device"></a>리프 디바이스

[IoT Edge](#iot-edge)에서 리프 디바이스는 다운스트림 디바이스가 없는 디바이스입니다.

### <a name="lifecycle-event"></a>수명 주기 이벤트

Azure Digital Twins에서 이러한 형식의 이벤트는 디지털 쌍, 관계 또는 이벤트 처리기와 같은 데이터 항목이 Azure Digital Twins 인스턴스에서 만들어지거나 삭제될 때 발생합니다.

### <a name="linked-iot-hub"></a>연결된 IoT 허브

[DPS(Device Provisioning Service)](#device-provisioning-service)는 연결된 IoT 허브에 디바이스를 프로비전할 수 있습니다. IoT 허브를 DPS 인스턴스에 연결하면 서비스에서 디바이스 ID를 등록하고 디바이스 쌍에서 초기 구성을 설정할 수 있습니다.

## <a name="m"></a>M

### <a name="model"></a>모델

모델은 속성, 원격 분석, 구성 요소, 기타 정보 등 물리적 환경의 엔터티 형식을 정의합니다. 모델은 이러한 형식의 특정 물리적 개체를 나타내는 [디지털 쌍](#digital-twin)을 만드는 데 사용됩니다. 모델은 [디지털 쌍 정의 언어](#digital-twins-definition-language-dtdl)로 작성됩니다.

[Azure Digital Twins 서비스](../digital-twins/index.yml)에서 모델은 디바이스 또는 상위 수준의 추상적인 비즈니스 개념을 정의할 수 있습니다. [IoT 플러그 앤 플레이](../iot-develop/index.yml)에서 [디바이스 모델](#device-model)은 디바이스를 구체적으로 설명하는 데 사용됩니다.

### <a name="model-id"></a>모델 ID

IoT 플러그 앤 플레이 디바이스가 IoT Hub에 연결될 때 디바이스가 구현하는 [DTDL](#digital-twins-definition-language-dtdl) 모델의 **모델 ID** 를 전송합니다. 모델 ID를 통해 솔루션은 디바이스 모델을 찾을 수 있습니다.

### <a name="model-repository"></a>모델 리포지토리

모델 리포지토리는 [디바이스 모델](#device-model) 및 [인터페이스](#interface)를 저장합니다.

### <a name="model-repository-rest-api"></a>Model repository REST API

모델 리포지토리를 관리하고 상호 작용하기 위한 API입니다. 예를 들어 이 API를 사용하여 [디바이스 모델](#device-model)을 추가하고 검색할 수 있습니다.

### <a name="module-builder"></a>모듈 작성기

모듈 작성기는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 실행되도록 코드를 구현할 때 [디바이스 모델](#device-model) 및 [인터페이스](#interface)를 사용합니다. 모듈 작성기는 코드를 모듈 또는 IoT Edge 모듈로 구현하여 디바이스의 IoT Edge 런타임에 배포합니다.

### <a name="module-identity"></a>모듈 ID

모듈 ID는 디바이스에 속해 있는 모든 모듈에 할당된 고유 식별자입니다. 또한 모듈 ID는 [ID 레지스트리](#identity-registry)에도 등록됩니다.

모듈 ID는 모듈이 [IoT Hub](#iot-hub)를 통해 인증하는 데 사용하는 보안 자격 증명을 상세히 열거합니다. 또는 [IoT Edge 허브](#iot-edge-hub)에 대한 IoT Edge 모듈의 경우에 있어서도 그러합니다.

### <a name="module-image"></a>모듈 이미지

[IoT Edge 런타임](#iot-edge-runtime)에서 모듈 인스턴스를 인스턴스화하는 데 사용하는 docker 이미지입니다.

### <a name="module-twin"></a>모듈 쌍

디바이스 쌍과 유사하게 모듈 쌍은 모듈의 상태 정보(메타데이터, 구성 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 IoT 허브의 디바이스 ID에서 프로비전하는 각 모듈 ID에 대해 모듈 쌍을 유지합니다. 모듈 쌍을 통해 모듈 및 솔루션 백 엔드 간의 모듈 조건 및 구성을 동기화할 수 있습니다. 모듈 쌍을 쿼리하여 특정 모듈을 찾고 장기 실행 중인 작업의 상태를 쿼리할 수 있습니다.

### <a name="modules"></a>모듈

디바이스 쪽에서 IoT Hub 디바이스 SDK를 사용하면 각각 IoT Hub에 대한 독립적 연결을 여는 [모듈](../iot-hub/iot-hub-devguide-module-twins.md)을 만들 수 있습니다. 이 함수를 사용하면 디바이스의 여러 구성 요소에 별도의 네임스페이스를 사용할 수 있습니다.

모듈 ID 및 모듈 쌍은 [디바이스 ID](#device-identity) 및 [디바이스 쌍](#device-twin)과 동일한 기능을 제공하지만 세분성이 더 높습니다. 이처럼 높은 세분성을 통해 여러 구성 요소를 관리하는 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같은 가능한 디바이스에서 이러한 각 구성 요소의 구성과 조건을 격리할 수 있습니다.

[IoT Edge](#iot-edge)에서 모듈은 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다. 디바이스에서 메시지 수집, 메시지 변환 또는 IoT Hub로 메시지 보내기와 같은 특정 작업을 수행합니다. 다른 모듈과 통신하고 [IoT Edge 런타임](#iot-edge-runtime)에 데이터를 전송합니다.

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/)는 [IoT Hub](#iot-hub)에서 디바이스와 통신을 위해 지원하는 메시징 프로토콜 중 하나입니다. IoT Hub에서 지원하는 메시징 프로토콜에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](../iot-hub/iot-hub-devguide-messaging.md)를 참조하세요.

## <a name="o"></a>O

### <a name="ontology"></a>온톨로지

부동산, 스마트 도시, IoT 시스템, 에너지 그리드 등과 같은 특정 도메인에 대한 모델 세트입니다. 온톨로지는 산업 표준 및 모범 사례에 따라 시작점을 제공하기 때문에 [Azure Digital Twins](#azure-digital-twins)와 같은 정보 그래프의 스키마로 사용되는 경우가 많습니다.

온톨로지에 대한 자세한 내용은 [온톨로지란?](../digital-twins/concepts-ontologies.md)을 참조하세요.

### <a name="operations-monitoring"></a>작업 모니터링

IoT Hub [작업 모니터링](../iot-hub/iot-hub-operations-monitoring.md)을 사용하면 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링할 수 있습니다. [IoT Hub](#iot-hub)는 몇 가지 작업 범주에 걸쳐 이벤트를 추적합니다. 하나 이상의 범주에서 IoT Hub의 엔드포인트로 처리할 이벤트를 보내도록 선택할 수 있습니다. 데이터에 오류가 있는지 모니터링하거나 데이터 패턴을 기반으로 좀 더 복잡한 처리를 설정할 수 있습니다.

## <a name="p"></a>P

### <a name="physical-device"></a>물리적 디바이스

물리적 디바이스는 Raspberry Pi처럼 IoT Hub에 연결되는 실제 디바이스입니다. 편의를 위해 많은 IoT Hub 자습서에서 [시뮬레이션된 디바이스](#simulated-device)를 사용하여 로컬 머신에서 샘플을 실행할 수 있도록 합니다.

### <a name="primary-and-secondary-keys"></a>기본 및 보조 키

IoT Hub에서 디바이스 지향 또는 서비스 지향 엔드포인트에 연결하는 경우 [연결 문자열](#connection-string)에는 액세스 권한을 부여하는 키가 포함됩니다. 디바이스를 [ID 레지스트리](#identity-registry)에 추가하거나 허브에 [공유 액세스 정책](#shared-access-policy)을 추가하는 경우 서비스에서 기본 및 보조 키를 생성합니다. 두 가지 키를 통해 키를 업데이트할 때 IoT Hub에 대한 액세스 권한을 잃지 않고도 한 키에서 다른 키로 롤오버가 가능합니다.

### <a name="properties"></a>속성

속성은 [디지털 쌍](#digital-twin)의 일부 지속 상태를 나타내는 [인터페이스](#interface)에 정의된 데이터 필드입니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 일련 번호와 같은 읽기 전용 속성은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 자체에서 실행되는 코드에 의해 설정됩니다. 경보 임계값과 같은 쓰기 가능한 속성은 일반적으로 클라우드 기반 IoT 솔루션에서 설정됩니다.

### <a name="property-change-event"></a>속성 변경 이벤트

[디지털 쌍](#digital-twin)의 속성 변경으로 인해 발생하는 이벤트입니다.

### <a name="protocol-gateway"></a>프로토콜 게이트웨이

일반적으로 프로토콜 게이트웨이는 클라우드에 배포되며 [IoT Hub](#iot-hub)에 연결되는 디바이스에 대해 프로토콜 번역 서비스를 제공합니다. 자세한 내용은 [Azure IoT Hub란?](../iot-hub/about-iot-hub.md)을 참조하세요.

## <a name="r"></a>R

### <a name="registration"></a>등록

등록은 IoT Hub [ID 레지스트리](#identity-registry)에 있는 디바이스의 레코드입니다. 디바이스를 직접 등록할 수도 있고 [Device Provisioning Service](#device-provisioning-service)를 사용하여 디바이스 등록을 자동화할 수도 있습니다.

### <a name="registration-id"></a>등록 ID

등록 ID는 [Device Provisioning Service](#device-provisioning-service)를 사용한 디바이스 [등록](#registration)을 고유하게 식별하는 데 사용됩니다. 등록 ID는 [디바이스 ID](#device-identity)와 값이 동일할 수도 있습니다.

### <a name="relationship"></a>관계

[Azure Digital Twins](../digital-twins/index.yml) 서비스에서 관계는 전체 물리적 환경을 디지털로 나타내는 정보 그래프로 [디지털 쌍](#digital-twin)을 연결하는 데 사용됩니다. 사용자의 쌍이 가질 수 있는 형식의 관계는 쌍의 [모델](#model) 정의의 일부로 정의됩니다. 특정 형식의 쌍에 대한 [DTDL](#digital-twins-definition-language-dtdl) 모델에는 다른 쌍에 대해 가질 수 있는 관계에 대한 정보가 포함됩니다.

### <a name="reported-configuration"></a>Reported 구성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 reported 구성은 솔루션 백 엔드에 보고해야 하는 디바이스 쌍의 속성 및 메타데이터 집합 전체를 나타냅니다.

### <a name="reported-properties"></a>reported 속성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 reported 속성은 [desired 속성](#desired-properties)과 함께 디바이스 구성 또는 상황을 동기화하는 데 사용되는 디바이스 쌍의 하위 섹션입니다. reported 속성은 [디바이스 앱](#device-app)에서만 설정할 수 있고 [백 엔드 앱](#back-end-app)에서 읽고 쿼리할 수 있습니다.

### <a name="retry-policy"></a>재시도 정책

클라우드 서비스에 연결할 때는 재시도 정책을 사용하여 [일시적인 오류](/azure/architecture/best-practices/transient-faults)를 처리합니다.

### <a name="routing-rules"></a>라우팅 규칙

IoT Hub에서 [기본 제공 엔드포인트](#built-in-endpoints) 또는 [사용자 지정 엔드포인트](#custom-endpoints)로 디바이스-클라우드 메시지를 라우팅하여 솔루션 백 엔드에서 처리하도록 [라우팅 규칙](../iot-hub/iot-hub-devguide-messages-read-custom.md)을 구성합니다.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN은 AMQP 프로토콜에서 보안 토큰을 전송하는 데 사용하는 프로토콜입니다.

### <a name="service-operations-endpoint"></a>서비스 작업 엔드포인트

서비스 관리자가 사용하는 서비스 설정을 관리하기 위한 [엔드포인트](#endpoint)입니다. 예를 들어 [Data Provisioning Service](#device-provisioning-service)에서 서비스 엔드포인트를 사용하여 등록을 관리할 수 있습니다.

### <a name="service-rest-api"></a>서비스 REST API

솔루션 백 엔드에서 [서비스 REST API](/rest/api/iothub/service/configuration)를 사용하여 디바이스를 관리할 수 있습니다. API를 사용하면 [디바이스 쌍](#device-twin) 속성을 검색 및 업데이트하고 [직접 메서드](#direct-method)를 호출하고 [작업](#job)을 예약할 수 있습니다. 일반적으로 IoT Hub 자습서에 나와 있는 것보다 상위 수준의 [서비스 SDK](#azure-iot-service-sdks)를 사용해야 합니다.

### <a name="shared-access-policy"></a>공유 액세스 정책

공유 액세스 정책은 해당 정책과 관련된 유효한 [기본 또는 보조 키](#primary-and-secondary-keys)를 보유한 모든 사람에게 부여할 사용 권한을 정의합니다. 포털에서 허브에 대한 공유 액세스 정책 및 키를 관리할 수 있습니다.

### <a name="shared-access-signature"></a>공유 액세스 서명

공유 액세스 서명(SAS)은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. SAS 인증에는 _공유 액세스 정책_ 과 _공유 액세스 서명_(토큰이라고 부름)의 두 구성 요소가 있습니다. 디바이스는 SAS를 사용하여 IoT Hub를 인증합니다. [백 엔드 앱](#back-end-app)도 SAS를 사용하여 IoT Hub에서 서비스 지향 엔드포인트를 인증합니다. 일반적으로 앱이 IoT Hub와 연결을 설정하기 위해 사용하는 [연결 문자열](#connection-string)에 SAS 토큰을 포함합니다.

### <a name="simulated-device"></a>시뮬레이션된 디바이스

편의를 위해 많은 IoT Hub 자습서에서 시뮬레이션된 디바이스를 사용하여 로컬 머신에서 샘플을 실행할 수 있도록 합니다. 반면에, [물리적 디바이스](#physical-device)는 Raspberry Pi처럼 IoT Hub에 연결되는 실제 디바이스입니다.

### <a name="solution"></a>솔루션

_솔루션_ 은 하나 이상의 프로젝트를 포함하는 Visual Studio 솔루션을 나타낼 수 있습니다. _솔루션_ 은 또한 디바이스, [디바이스 앱](#device-app), IoT Hub, 기타 Azure 서비스 및 [백 엔드 앱](#back-end-app)과 같은 요소를 포함하는 IoT 솔루션을 나타낼 수 있습니다.

### <a name="solution-builder"></a>솔루션 작성기

솔루션 작성기는 솔루션 백 엔드를 만듭니다. 솔루션 작성기는 일반적으로 IoT Hub, [모델 리포지토리](#model-repository) 등과 같은 Azure 리소스를 사용합니다.

### <a name="system-properties"></a>시스템 속성

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 시스템 속성은 읽기 전용이며 마지막 작업 시간 및 연결 상태와 같은 디바이스 사용에 대한 정보를 포함합니다.

## <a name="t"></a>T

### <a name="tags"></a>태그들

[디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 컨텍스트에서 태그는 JSON 문서 형식으로 솔루션 백 엔드에 의해 저장 및 검색되는 디바이스 메타데이터입니다. 태그는 디바이스 앱에 표시되지 않습니다.

### <a name="target-condition"></a>대상 조건

IoT Edge 배포에서 대상 조건은 배포의 대상 디바이스(예: **tag.environment = prod**)를 선택합니다. 대상 조건은 요구 사항을 충족하는 새 디바이스를 포함하거나 더 이상 충족하지 않는 디바이스를 제거하기 위해 지속적으로 평가됩니다.

### <a name="telemetry"></a>원격 분석

디바이스는 풍속 또는 온도와 같은 원격 분석 데이터를 수집하고 데이터 요소 메시지를 사용하여 IoT Hub에 원격 분석을 보냅니다.

IoT 플러그 앤 플레이 및 Azure Digital Twins에서 [인터페이스](#interface)에 정의된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정값은 일반적으로 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 등 디바이스에서 데이터 스트림으로 보내는 센서 판독값과 같은 값입니다.

[속성](#properties)과 달리 원격 분석은 [디지털 쌍](#digital-twin)에 저장되지 않으며, 발생 시 처리해야 하는 시간 제한 데이터 이벤트의 스트림입니다.

### <a name="telemetry-event"></a>원격 분석 이벤트

원격 분석 데이터의 도착을 나타내는 이벤트입니다.

### <a name="token-service"></a>토큰 서비스

토큰 서비스를 사용하여 디바이스에 대한 인증 메커니즘을 구현합니다. **DeviceConnect** 권한으로 IoT Hub [공유 액세스 정책](#shared-access-policy)을 사용하여 *device-scoped* 토큰을 만듭니다. 이러한 토큰은 디바이스에서 IoT Hub에 연결할 수 있게 해줍니다. 디바이스는 사용자 지정 인증 메커니즘을 사용하여 토큰 서비스를 인증합니다. 디바이스가 성공적으로 인증되면 토큰 서비스는 사용할 디바이스에 대한 SAS 토큰을 발행하여 IoT Hub에 액세스합니다.

### <a name="twin-graph-or-digital-twin-graph"></a>쌍 그래프(또는 디지털 쌍 그래프)

[Azure Digital Twins](../digital-twins/index.yml) 서비스에서 [디지털 쌍](#digital-twin)을 [관계](#relationship)와 연결시켜 전체 물리적 환경을 디지털로 나타내는 정보 그래프를 만들 수 있습니다. 단일 [Azure Digital Twins 인스턴스](#azure-digital-twins-instance)는 여러 개의 연결 해제된 그래프 또는 하나의 상호 연결된 그래프를 호스트할 수 있습니다.

### <a name="twin-queries"></a>쌍 쿼리

[디바이스 및 모듈 쌍 쿼리](../iot-hub/iot-hub-devguide-query-language.md)에서는 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 디바이스 쌍 또는 모듈 쌍에서 정보를 검색합니다. 동일한 IoT Hub 쿼리 언어를 사용하여 IoT 허브에서 실행 중인 [작업](#job)에 대한 정보를 검색할 수 있습니다.

### <a name="twin-synchronization"></a>쌍 동기화

쌍 동기화는 디바이스 쌍 또는 모듈 쌍에서 [원하는 속성](#desired-properties)을 사용하여 디바이스 또는 모듈을 구성하고 쌍에 저장할 디바이스 또는 모듈의 [보고된 속성](#reported-properties)을 검색합니다.

## <a name="u"></a>U

### <a name="upstream-services"></a>업스트림 서비스

현재 컨텍스트로 데이터를 내보내는 서비스를 설명하는 상대 용어입니다. 예를 들어 Azure Digital Twins의 컨텍스트에서 생각하는 경우 데이터가 IoT Hub에서 Azure Digital Twins로 흐르기 때문에 IoT Hub는 업스트림 서비스로 간주됩니다.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X.509 클라이언트 인증서

디바이스는 X.509 인증서를 사용하여 [IoT Hub](#iot-hub)와 인증을 수행할 수 있습니다. [SAS 토큰](#shared-access-signature)을 사용하는 대신 X.509 인증서를 사용합니다.