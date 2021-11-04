---
title: Azure IoT 용어집 | Microsoft Docs
description: 개발자 가이드 - Azure IoT 문서에 사용된 일반 용어 몇 가지를 설명하는 용어집입니다.
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 532bb1f9d9c2e612714a178d3c1445f24cd8c6ea
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552509"
---
# <a name="glossary-of-iot-terms"></a>IoT 용어집

이 문서에서는 IoT 문서에 사용된 일반 용어 몇 가지를 나열합니다.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>고급 메시지 큐 프로토콜

[장치와](#device)의 통신을 지원 하기 위해 [IoT Hub](#iot-hub) 및 IoT Central 하는 메시징 프로토콜 중 하나입니다.

[자세한 정보](../iot-hub/iot-hub-devguide-protocols.md)

적용 대상: Iot Hub, IoT Central, 장치 개발자

### <a name="allocation-policy"></a>할당 정책

[장치 프로 비전 서비스](#device-provisioning-service)에서 할당 정책은 서비스가 [연결 된 IoT hub](#linked-iot-hub)에 [장치](#device) 를 할당 하는 방법을 결정 합니다.

적용 대상: 장치 프로 비전 서비스

### <a name="attestation-mechanism"></a>증명 메커니즘

[장치 프로 비전 서비스](#device-provisioning-service)에서 증명 메커니즘은 [장치의](#device)id를 확인 하는 데 사용 되는 방법입니다. 증명 메커니즘은 [등록](#enrollment)에서 구성됩니다.

증명 메커니즘에는 x.509 인증서, 신뢰할 수 있는 플랫폼 [모듈](#module)및 대칭 키가 포함 됩니다.

적용 대상: 장치 프로 비전 서비스

### <a name="automatic-deployment"></a>자동 배포

IoT Edge [모듈](#module)집합을 실행 하도록 [IoT Edge 장치의](#iot-edge-device) 대상 집합을 구성 하는 [IoT Edge](#iot-edge) 의 기능입니다. 각 배포는 대상 조건과 일치 하도록 새 장치를 만들거나 수정 하는 경우에도 [대상 조건과](#target-condition) 일치 하는 모든 [장치가](#device) 지정 된 모듈 집합을 실행 하는지 확인 합니다. 각 IoT Edge 디바이스는 해당 대상 조건을 충족하는 가장 높은 우선 순위 배포만 수신합니다.

[자세한 정보](../iot-edge/module-deployment-monitoring.md)

적용 대상: IoT Edge

### <a name="automatic-device-configuration"></a>자동 디바이스 구성

[솔루션](#solution) 백 엔드가 [장치](#device-twin) 쌍 집합에 [desired 속성](#desired-properties) 을 할당 하 고 시스템 및 사용자 지정 메트릭을 사용 하 여 [장치](#device) 상태를 보고할 수 있도록 하는 [IoT Hub](#iot-hub) 기능입니다.

[자세한 정보](../iot-hub/iot-hub-automatic-device-management.md)

적용 대상: Iot Hub

### <a name="automatic-device-management"></a>자동 디바이스 관리

전체 수명 주기 동안 많은 [장치](#device) 대규모을 관리 하는 반복적이 고 복잡 한 작업을 자동화 하는 [IoT Hub](#iot-hub) 기능입니다. 기능을 사용 하면 해당 [속성](#properties)을 기반으로 장치 집합을 대상으로 지정 하 고, [원하는 구성을](#desired-configuration)정의 하 고, 범위에 있을 때마다 장치를 IoT Hub 업데이트할 수 있습니다.

[자동 디바이스 구성](../iot-hub/iot-hub-automatic-device-management.md) 및 [IoT Edge 자동 배포](../iot-edge/how-to-deploy-at-scale.md)로 구성됩니다.

적용 대상: Iot Hub

### <a name="azure-certified-device-program"></a>Azure Certified Device 프로그램

Azure 인증 [장치](#device) 는 azure에서 실행 되도록 빌드된 IoT 장치를 구분 하 고, 인증 하 고, 승격할 수 있는 무료 프로그램입니다.

[자세한 정보](../certification/overview.md)

적용 대상: Iot Hub, IoT Central

### <a name="azure-digital-twins"></a>Azure Digital Twins

실제 사물, 장소, 비즈니스 프로세스 및 사람들의 디지털 표현을 만들기 위한 PaaS (platform as a service) 제품입니다. 전체 환경을 나타내는 쌍 그래프를 작성하고, 이를 사용하여 더 나은 제품을 구동하고, 운영 및 비용을 최적화하며, 혁신적인 고객 경험을 만들기 위한 인사이트를 얻습니다.

[자세한 정보](../digital-twins/overview.md)

적용 대상: 디지털 쌍

### <a name="azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스

고객 구독에 있는 [Azure Digital Twins](#azure-digital-twins) 서비스의 단일 인스턴스입니다. Azure [Digital Twins](#digital-twin) 는 전체 azure 서비스를 참조 하는 반면, azure Digital twins *인스턴스* 는 개별 azure 디지털 쌍 리소스입니다.

적용 대상: 디지털 쌍

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

[장치](#device) 에서 보내는 [원격 분석](#telemetry) 을 보고, 장치 [속성](#properties)을 사용 하 고, [명령을](#command)호출 하는 데 사용할 수 있는 도구입니다. 또한, 디바이스를 대상으로 상호 작용 및 테스트를 하고 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 관리할 수 있습니다.

[자세한 정보](https://github.com/Azure/azure-iot-explorer)

적용 대상: Iot Hub, 장치 개발자

### <a name="azure-iot-tools"></a>Azure IoT Tools

VS Code에서 Azure [IoT Hub](#iot-hub) 및 [장치](#device) 를 관리 하는 데 도움이 되는 플랫폼 간 오픈 소스 Visual Studio Code 확장 프로그램입니다. Azure IoT Tools를 통해 iot 개발자는에서 iot 프로젝트를 쉽게 개발할 수 있습니다 VS Code

적용 대상: Iot Hub, IoT Edge, IoT Central, 장치 개발자

### <a name="azure-iot-device-sdks"></a>Azure IoT 디바이스 SDK

여러 언어에서 사용할 수 있는 이러한 SDK를 사용 하 여 [IoT hub](#iot-hub) 또는 IoT Central 응용 프로그램과 상호 작용 하는 [장치 앱](#device-app) 을 만들 수 있습니다.

[자세한 정보](../iot-develop/about-iot-sdks.md)

적용 대상: Iot Hub, IoT Central, 장치 개발자

### <a name="azure-iot-service-sdks"></a>Azure IoT 서비스 SDK

여러 언어에서 사용할 수 있는 이러한 Sdk를 사용 하 여 [IoT hub](#iot-hub)와 상호 작용 하는 [백 엔드 앱](#back-end-app) 을 만들 수 있습니다.

[자세한 정보](/iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

적용 대상: Iot Hub

## <a name="b"></a>b

### <a name="back-end-app"></a>백 엔드 앱

[IoT Hub](#iot-hub)컨텍스트에서 IoT Hub의 서비스 지향 [끝점](#endpoint) 중 하나에 연결 하는 앱입니다. 예를 들어 백 엔드 앱은 [디바이스-클라우드](#device-to-cloud) 메시지를 검색하거나 [ID 레지스트리](#identity-registry)를 관리할 수 있습니다. 일반적으로 백 엔드 앱은 클라우드에서 실행 되지만 간단한 자습서의 많은 자습서에서는 백 엔드 앱을 로컬 개발 컴퓨터에서 실행 되는 콘솔 앱으로 표시 합니다.

적용 대상: Iot Hub

### <a name="built-in-endpoints"></a>기본 제공 엔드포인트

[IoT Hub](#iot-hub)에 기본 제공 되는 [끝점](#endpoint) 입니다. 예를 들어 모든 IoT hub에는 Event Hubs 호환 되는 기본 제공 끝점이 포함 되어 있습니다.

적용 대상: Iot Hub

## <a name="c"></a>C

### <a name="cloud-gateway"></a>클라우드 게이트웨이

[IoT Hub](#iot-hub) 또는 IoT Central에 직접 연결할 수 없는 [장치](#device) 에 대 한 연결을 가능 하 게 하는 클라우드 호스팅 앱입니다. 클라우드 [게이트웨이](#gateway) 는 장치에 로컬로 실행 되는 [필드 게이트웨이와](#field-gateway) 달리 클라우드에서 호스팅됩니다. 클라우드 게이트웨이의 일반적인 사용 사례는 장치에 대 한 프로토콜 변환을 구현 하는 것입니다.

적용 대상: Iot Hub, IoT Central

### <a name="cloud-property"></a>클라우드 속성

IoT Central 응용 프로그램에서 [장치](#device) 메타 데이터를 저장할 수 있도록 하는 IoT Central의 기능입니다. 클라우드 [속성](#properties) 은 장치 [템플릿에](#device-template)정의 되지만 [장치 모델](#device-model)에는 포함 되지 않습니다. 클라우드 속성은 장치와 동기화 되지 않습니다.

적용 대상: IoT Central

### <a name="cloud-to-device"></a>클라우드-디바이스

[IoT hub](#iot-hub) 에서 연결 된 [장치로](#device)전송 되는 메시지입니다. 종종 이러한 메시지는 장치에 작업을 수행 하도록 지시 하는 [명령](#command) 입니다.

적용 대상: Iot Hub

### <a name="command"></a>명령

명령은 [디지털](#digital-twin)쌍에서 호출할 수 있는 메서드를 나타내기 위해 IoT 플러그 앤 플레이 [인터페이스](#interface) 에 정의 됩니다. 예를 들어 [장치](#device)를 다시 부팅 하는 명령이 있습니다. IoT Central에서 명령은 [장치 템플릿에](#device-template)정의 되어 있습니다.

적용 대상: Iot Hub, IoT Central, 장치 개발자

### <a name="component"></a>구성 요소

IoT 플러그 앤 플레이 및 [Azure Digital Twins](#azure-digital-twins)에서 구성 요소를 사용 하 여 다른 인터페이스의 어셈블리로 [모델](#model) [인터페이스](#interface) 를 빌드할 수 있습니다. [디바이스 모델](#device-model)은 여러 인터페이스를 구성 요소로 결합할 수 있습니다. 예를 들어 모델에는 스위치 구성 요소와 온도 조절 디바이스 구성 요소가 포함될 수 있습니다. 모델의 여러 구성 요소가 동일한 인터페이스 형식을 사용할 수도 있습니다. 예를 들어 하나의 모델에 두 개의 온도 조절 디바이스 구성 요소가 포함될 수 있습니다.

적용 대상: Iot Hub, 디지털 쌍, 장치 개발자

### <a name="configuration"></a>구성

[IoT Hub](#iot-hub)자동 [디바이스 구성의](#automatic-device-configuration) 컨텍스트에서 디바이스 [쌍](#device) 집합에 대해 [원하는 구성을](#desired-configuration) 정의하고 상태 및 진행 상황을 보고하는 메트릭 집합을 제공합니다.

적용된 내용: Ot Hub

### <a name="connection-string"></a>연결 문자열

앱 코드에서 를 사용하여 [엔드포인트](#endpoint)에 연결하는 데 필요한 정보를 캡슐화합니다. 일반적으로 연결 문자열에는 엔드포인트의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. [IoT Hub](#iot-hub) 서비스와 연결된 두 가지 유형의 연결 문자열이 있습니다.

- *[디바이스](#device) 연결 문자열을* 사용하면 디바이스가 IoT Hub의 디바이스 연결 엔드포인트에 연결할 수 있습니다.
- *IoT Hub 연결 문자열을* 사용하면 [백 엔드 앱이](#back-end-app) IoT Hub의 서비스 연결 엔드포인트에 연결할 수 있습니다.

적용 대상: Ot Hub, 디바이스 개발자

### <a name="custom-endpoints"></a>사용자 지정 엔드포인트

[라우팅 규칙에](#routing-rule)의해 디스패치된 메시지를 배달하는 [IoT Hub의](#iot-hub) 사용자 정의 [엔드포인트입니다.](#endpoint) 이러한 엔드포인트는 이벤트 허브, Service Bus 큐 또는 Service Bus 토픽에 직접 연결됩니다.

적용된 내용: Ot Hub

### <a name="custom-gateway"></a>사용자 지정 게이트웨이

IoT Hub 또는 [IoT Central](#iot-hub) 직접 연결할 수 없는 [디바이스에](#device) 대한 연결을 사용하도록 설정합니다. Azure [IoT Edge](#iot-edge) 사용하여 메시지를 처리하는 사용자 지정 논리, 사용자 지정 프로토콜 변환 및 기타 처리를 구현하는 사용자 지정 [게이트웨이를](#gateway) 빌드할 수 있습니다.

적용할 내용: Ot Hub, IoT Central

## <a name="d"></a>D

### <a name="default-component"></a>기본 구성 요소

모든 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) [모델에는](#model) 기본 [구성 요소가](#component)있습니다. 단순 [디바이스 모델에는](#device-model) 기본 구성 요소만 있습니다. 이러한 모델을 구성 요소가 없는 [디바이스라고도](#device)합니다. 더 복잡한 모델에는 기본 구성 요소 아래에 중첩된 여러 구성 요소가 있습니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자

### <a name="deployment-manifest"></a>배포 매니페스트

하나 이상의 [IoT Edge 디바이스](#iot-edge) [모듈 쌍에](#module-twin)대한 [구성](#configuration) 데이터를 포함하는 [IoT Edge](#iot-edge-device) JSON 문서입니다.

적용 내용: IoT Edge, IoT Central

### <a name="desired-configuration"></a>desired 구성

[디바이스 쌍의](#device-twin)컨텍스트에서 원하는 [구성은](#configuration) [디바이스와](#device) 동기화해야 하는 디바이스 쌍의 [전체 속성](#properties) 및 메타데이터 집합을 나타냅니다.

적용된 내용: Ot Hub

### <a name="desired-properties"></a>Desired 속성

[디바이스 쌍의](#device-twin)컨텍스트에서 desired 속성은 [reported](#reported-properties) [속성과](#properties) 함께 디바이스 [구성](#configuration) 또는 조건을 동기화하는 데 사용되는 디바이스 [쌍의](#device) 하위 섹션입니다. desired 속성은 [백 엔드 앱](#back-end-app)에서만 설정할 수 있고 [디바이스 앱](#device-app)에서 관찰할 수 있습니다. IoT Central 쓰기 가능한 속성이라는 용어를 사용합니다.

적용된 내용: Ot Hub

### <a name="device"></a>디바이스

IoT의 컨텍스트에서 일반적으로 디바이스는 데이터를 수집하거나 다른 디바이스를 제어할 수 있는 소규모의 독립 실행형 컴퓨팅 디바이스입니다. 예를 들어 디바이스는 환경 모니터링 디바이스이거나 온실에서 급수 및 통풍 시스템을 위한 컨트롤러일 수 있습니다. 디바이스 카탈로그는 인증된 디바이스 목록을 제공합니다.

적용 대상: Ot Hub, IoT Central, IoT Edge, Device Provisioning Service, 디바이스 개발자

### <a name="device-provisioning-service"></a>Device Provisioning Service

터치 없는 [디바이스 프로비저닝을](#device-provisioning)구성하는 데 사용하는 [IoT Hub](#iot-hub) 및 IoT Central 위한 도우미 서비스입니다. DPS를 사용하면 안전하고 확장 가능한 방식으로 수백만 개의 [디바이스를](#device) 프로비전할 수 있습니다.

적용 적용: Ot Hub, Device Provisioning Service, IoT Central

### <a name="device-rest-api"></a>디바이스 REST API

[디바이스에서](#device) [디바이스-클라우드](#device-to-cloud) 메시지를 [IoT Hub로 보내고 IoT Hub에서](#iot-hub) [클라우드-디바이스](#cloud-to-device) 메시지를 수신하는 데 사용할 수 있는 REST API. 일반적으로 상위 수준 [Azure IoT 디바이스 SDK](#azure-iot-device-sdks)중 하나를 사용해야 합니다.

[자세한 정보](/rest/api/iothub/device)

적용된 내용: Ot Hub

### <a name="device-app"></a>디바이스 앱

[디바이스](#device) 앱은 디바이스에서 실행되며 [IoT Hub](#iot-hub) 또는 IoT Central 애플리케이션과의 통신을 처리합니다. 일반적으로 디바이스 앱을 구현할 때 [Azure IoT 디바이스 SDK](#azure-iot-device-sdks) 중 하나를 사용합니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자

### <a name="device-builder"></a>디바이스 작성기

[디바이스에서](#device)실행할 코드를 만드는 담당자입니다. 일반적으로 디바이스 빌더는 [Azure IoT 디바이스 SDK들](#azure-iot-device-sdks) 중 하나를 사용하여 디바이스 클라이언트를 구현합니다. 디바이스 작성기는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 실행되도록 코드를 구현할 때 [디바이스 모델](#device-model) 및 [인터페이스](#interface)를 사용합니다.

적용 대상: Ot Hub, IoT Central, IoT Edge, 디바이스 개발자

### <a name="device-identity"></a>디바이스 ID

IoT Hub [ID 레지스트리](#identity-registry) 또는 [IoT Central](#iot-hub) 애플리케이션에 등록된 모든 [디바이스에](#device) 할당된 고유 식별자입니다.

적용할 내용: Ot Hub, IoT Central

### <a name="device-management"></a>디바이스 관리

[디바이스](#device) 관리는 계획, 프로비전, 구성, 모니터링 및 사용 중지를 포함하여 IoT [솔루션의](#solution) 디바이스 관리와 관련된 전체 수명 주기를 포함합니다.

적용할 내용: Ot Hub, IoT Central

### <a name="device-model"></a>디바이스 모델

[디바이스](#device)기능의 [Digital Twins 정의 언어](#digital-twins-definition-language)를 사용하는 설명입니다. 기능에는 [원격 분석,](#telemetry) [속성](#properties)및 [명령이 포함됩니다.](#command)

[자세한 정보](../iot-develop/concepts-modeling-guide.md)

적용 대상: Ot Hub, IoT Central, 디바이스 개발자 Digital Twins

### <a name="device-provisioning"></a>디바이스 프로비전

[솔루션](#solution)의 저장소에 초기 [디바이스](#device) 데이터를 추가하는 프로세스입니다. 새 디바이스를 허브에 연결할 수 있도록 하려면 디바이스 ID 및 키를 [IoT Hub](#iot-hub) [ID 레지스트리에](#identity-registry)추가해야 합니다. [Device Provisioning Service는](#device-provisioning-service) IoT Hub 또는 IoT Central 애플리케이션에서 디바이스를 자동으로 프로비전할 수 있습니다.

적용: Ot Hub, IoT Central, Device Provisioning Service

### <a name="device-template"></a>디바이스 템플릿

IoT Central [디바이스](#device) 템플릿은 애플리케이션에 연결하는 디바이스 유형의 특징과 동작을 정의하는 청사진입니다.

예를 들어 디바이스 템플릿은 IoT Central 올바른 단위 및 데이터 형식을 사용하는 시각화를 만들 수 있도록 디바이스에서 보내는 [원격](#telemetry) 분석을 정의할 수 있습니다. [디바이스 모델](#device-model)은 디바이스 템플릿의 일부입니다.

적용 적용: IoT Central

### <a name="device-twin"></a>디바이스 쌍

[디바이스](#device) 쌍은 메타데이터, [구성](#configuration)및 조건과 같은 디바이스 상태 정보를 저장하는 JSON 문서입니다. [IoT Hub](#iot-hub) IoT Hub에서 프로비전하는 각 디바이스에 대해 디바이스 쌍을 유지합니다. 디바이스 쌍을 사용하면 디바이스와 [솔루션](#solution) 백 엔드 간에 디바이스 조건 및 구성을 동기화할 수 있습니다. 디바이스 쌍을 쿼리하여 특정 디바이스를 찾고 장기 실행 중인 작업의 상태를 확인할 수 있습니다.

적용된 내용: Ot Hub

### <a name="device-to-cloud"></a>디바이스-클라우드

연결된 [디바이스에서](#device) IoT Hub 또는 [IoT Central](#iot-hub) 전송된 메시지를 나타냅니다.

적용된 내용: Ot Hub

### <a name="digital-twins-definition-language"></a>Digital Twins 정의 언어

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)와 [Azure Digital Twins](#azure-digital-twins) 엔터티에 대한 [모델](#model) 및 [인터페이스](#interface)를 설명하는 JSON-LD 언어입니다. 언어를 사용하면 IoT 플랫폼 및 IoT [솔루션에서](#solution) 엔터티의 의미 체계를 사용할 수 있습니다.

[자세한 정보](https://github.com/Azure/opendigitaltwins-dtdl)

적용: Ot Hub, IoT Central, Digital Twins

### <a name="digital-twin"></a>디지털 쌍

디지털 쌍은 물리적 개체를 나타내는 디지털 데이터의 컬렉션입니다. 물리적 개체의 변경 내용은 디지털 쌍에 반영됩니다. 일부 시나리오에서는 디지털 쌍을 사용하여 물리적 개체를 조작할 수 있습니다. [Azure Digital Twins 서비스는](../digital-twins/index.yml) Digital Twins 정의 [언어로](#digital-twins-definition-language) 표현된 [모델을](#model) 사용하여 [물리적 디바이스의](#physical-device) 디지털 트윈 또는 상위 수준의 추상 비즈니스 개념을 나타내며 광범위한 클라우드 기반 디지털 트윈 솔루션을 사용할 수 [있습니다.](#solution) [IoT 플러그 앤 플레이](../iot-develop/index.yml) [디바이스에는](#device) Digital Twins 정의 언어 [디바이스 모델](#device-model)에서 설명하는 디지털 트윈이 있습니다.

적용 대상: Ot Hub, IoT Central, Digital Twins, 디바이스 개발자

### <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

[Iot 플러그 앤 플레이 장치가](#iot-plug-and-play-device) [iot hub](#iot-hub)에 연결 된 경우 허브는 해당 라우팅 기능을 사용 하 여 [디지털](#digital-twin) 쌍 변경 내용에 대 한 알림을 보낼 수 있습니다. IoT Central 데이터 내보내기 기능은 디지털 쌍 변경 이벤트를 다른 서비스로 전달할 수도 있습니다. 예를 들어, [장치](#device)에서 속성 값이 변경 될 때마다 이벤트 허브와 같은 [끝점](#endpoint) 에 알림을 보낼 수 IoT Hub.

적용 대상: Iot Hub, IoT Central

### <a name="digital-twin-graph"></a>디지털 쌍 그래프

[Azure Digital Twins](#azure-digital-twins) 서비스에서 [디지털 쌍](#digital-twin)을 [관계](#relationship)와 연결시켜 전체 물리적 환경을 디지털로 나타내는 정보 그래프를 만들 수 있습니다. 단일 [Azure Digital Twins 인스턴스](#azure-digital-twins-instance)는 여러 개의 연결 해제된 그래프 또는 하나의 상호 연결된 그래프를 호스트할 수 있습니다.

적용 대상: Iot Hub

### <a name="direct-method"></a>직접 메서드

[IoT hub](#iot-hub)에서 API를 호출 하 여 [장치](#device) 에서 실행 되는 메서드를 트리거하는 방법입니다.

적용 대상: Iot Hub

### <a name="downstream-service"></a>다운스트림 서비스

현재 컨텍스트에서 데이터를 수신하는 서비스를 설명하는 상대 용어입니다. 예를 [들어 azure digital twins의 컨텍스트에서](#azure-digital-twins)는 azure [digital twins](#digital-twin) 에서 Time Series Insights로 이동 하도록 데이터를 설정 하는 경우 다운스트림 서비스 Time Series Insights입니다.

적용 대상: Iot Hub, IoT Central, 디지털 쌍

## <a name="e"></a>E

### <a name="endpoint"></a>엔드포인트

다른 서비스에서 데이터를 받을 수 있는 데이터 라우팅 서비스의 명명된 표현입니다.

[Iot hub](#iot-hub) 는 앱이 iot hub에 연결할 수 있도록 하는 여러 끝점을 노출 합니다. 장치에서 장치- [클라우드](#device-to-cloud) 메시지 전송과 같은 작업을 수행할 수 있도록 하는 [장치](#device)지향 끝점이 있습니다. [백 엔드 앱](#back-end-app) 이 [장치 id](#device-identity) 관리와 같은 작업을 수행할 수 있도록 하는 서비스 지향 관리 끝점이 있습니다. 디바이스-클라우드 메시지 를 읽기 위한 서비스 지향 [기본 제공 엔드포인트](#built-in-endpoints)가 있습니다. [사용자 지정 엔드포인트](#custom-endpoints)를 만들어 [라우팅 규칙](#routing-rule)으로 발송된 디바이스-클라우드 메시지를 받을 수 있습니다.

적용 대상: Iot Hub

### <a name="enrollment"></a>등록

[장치 프로 비전 서비스](#device-provisioning-service)에서 등록은 자동 프로 비전을 통해 [연결 된 IoT hub](#linked-iot-hub) 에 등록할 수 있는 개별 [장치](#device) 또는 장치 그룹의 레코드입니다.

적용 대상: Iot Hub, IoT Central, 장치 프로 비전 서비스

### <a name="enrollment-group"></a>등록 그룹

[장치 프로 비전 서비스](#device-provisioning-service) 및 IoT Central에서 [등록](#enrollment) 그룹은 x.509 또는 대칭 키 [증명 메커니즘](#attestation-mechanism)을 공유 하는 [장치](#device) 그룹을 식별 합니다.

적용 대상: Iot Hub, 장치 프로 비전 서비스, IoT Central

### <a name="event-hubs-compatible-endpoint"></a>Event Hubs 호환 끝점

Event Hubs 호환 메서드를 사용 하 여 허브에 전송 된 [장치](#device) 메시지를 읽을 수 있는 [IoT Hub](#iot-hub) [끝점](#endpoint) 입니다. Event Hubs 호환 방법에는 [Event Hubs sdk](../event-hubs/event-hubs-programming-guide.md) 및 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)가 포함 됩니다.

적용 대상: Iot Hub

### <a name="event-handler"></a>이벤트 처리기

이벤트 도착에 의해 트리거되는 프로세스입니다. 예를 들어 Azure 함수에 이벤트 처리 코드를 추가 하 고 [끝점](#endpoint) 및 [이벤트 라우팅을](#event-routing)사용 하 여 데이터를 전송 하 여 이벤트 처리기를 만들 수 있습니다.

적용 대상: Iot Hub

### <a name="event-routing"></a>이벤트 라우팅

하나의 [장치나](#device) 서비스에서 다른 장치나 서비스로 이벤트와 해당 데이터를 보내는 [프로세스입니다.](#endpoint)

[Iot Hub](#iot-hub)에서 메시지를 전송 하는 방법을 설명 하는 [라우팅 규칙](#routing-rule) 을 정의할 수 있습니다. [Azure Digital Twins](#azure-digital-twins)에서 이벤트 경로는이 용도로 만들어진 엔터티입니다. Azure [Digital Twins](#digital-twin) 이벤트 경로에는 각 끝점으로 전송 되는 이벤트 유형을 제한 하는 필터가 포함 될 수 있습니다.

적용 대상: Iot Hub, 디지털 쌍

## <a name="f"></a>F

### <a name="field-gateway"></a>필드 게이트웨이

[IoT Hub](#iot-hub) 에 직접 연결할 수 없는 [장치](#device) 에 대 한 연결을 사용 하도록 설정 합니다. 일반적으로 장치와 함께 로컬로 배포 됩니다.

적용 대상: Iot Hub, IoT Central

## <a name="g"></a>G

### <a name="gateway"></a>게이트웨이

게이트웨이는 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 [장치](#device) 에 대 한 연결을 가능 하 게 합니다. [필드 게이트웨이](#field-gateway), [클라우드 게이트웨이](#cloud-gateway)및 [사용자 지정 게이트웨이](#custom-gateway)도 참조 하세요.

적용 대상: Iot Hub, IoT Central

### <a name="gateway-device"></a>게이트웨이 디바이스

[필드 게이트웨이의](#field-gateway)예입니다. [게이트웨이](#gateway) [장치](#device) 는 표준 IoT 장치 또는 [IoT Edge 장치인](#iot-edge-device)수 있습니다.

게이트웨이 디바이스를 통해 [IoT Hub](#iot-hub)에 직접 연결할 수 없는 다운스트림 디바이스의 연결이 가능합니다.

적용 대상: Iot Hub, IoT Central, IoT Edge

## <a name="h"></a>H

### <a name="hardware-security-module"></a>하드웨어 보안 모듈

[장치](#device) 비밀의 안전 하 고 하드웨어 기반 저장소에 사용 됩니다. 장치에 대 한 가장 안전한 형태의 암호 저장소입니다. 하드웨어 보안 [모듈](#module) 은 x.509 인증서와 대칭 키를 둘 다 저장할 수 있습니다. [장치 프로 비전 서비스](#device-provisioning-service)에서 [증명 메커니즘](#attestation-mechanism) 은 하드웨어 보안 모듈을 사용할 수 있습니다.

적용 대상: Iot Hub, 장치 개발자, 장치 프로 비전 서비스

## <a name="i"></a>I

### <a name="id-scope"></a>ID 범위

생성 될 때 [장치 프로 비전 서비스](#device-provisioning-service) 인스턴스에 할당 되는 고유 값입니다.

응용 프로그램 IoT Central DPS 인스턴스를 사용 하 고 IoT Central UI를 통해 ID 범위를 사용할 수 있도록 설정 합니다.

적용 대상: Iot Hub, IoT Central, 장치 프로 비전 서비스

### <a name="identity-registry"></a>ID 레지스트리

허브에 연결 하도록 허용 된 개별 [장치](#device) 에 대 한 정보를 저장 하는 [IoT hub](#iot-hub) 의 기본 제공 [구성 요소](#component) 입니다.

적용 대상: Iot Hub

### <a name="individual-enrollment"></a>개별 등록

[장치 프로 비전 서비스가](#device-provisioning-service) [IoT hub](#iot-hub)에 프로 비전 할 수 있는 단일 [장치](#device) 를 식별 합니다.

적용 대상: Iot Hub, 장치 프로 비전 서비스

### <a name="interface"></a>인터페이스

IoT 플러그 앤 플레이에서 인터페이스는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 또는 [디지털 쌍](#digital-twin)에서 구현하는 관련 기능을 설명합니다. 여러 [디바이스 모델](#device-model)에서 인터페이스를 다시 사용할 수 있습니다. 인터페이스는 [장치](#device) [모델](#model)에서 사용 되는 경우 장치의 [구성 요소](#component) 를 정의 합니다. 단순 디바이스에는 기본 인터페이스만 포함됩니다.

[Azure Digital Twins](#azure-digital-twins)에서 *인터페이스* 를 사용 하 여 [디지털 twins 정의 언어](#digital-twins-definition-language) 모델 정의에서 최상위 코드 항목을 참조할 수 있습니다.

적용 대상: 장치 개발자, 디지털 쌍

### <a name="iot-edge"></a>IoT Edge

클라우드 기반 Azure 서비스 및 [솔루션](#solution)관련 코드를 온-프레미스 [장치](#device)에 배포 하는 데 사용할 수 있는 서비스 및 관련 클라이언트 라이브러리 및 런타임입니다. [IoT Edge 디바이스](#iot-edge-device)는 다른 디바이스의 데이터를 집계한 후 데이터를 클라우드로 전송하기 전에 계산 및 분석을 수행할 수 있습니다.

[자세한 정보](../iot-edge/index.yml)

적용 대상: IoT Edge

### <a name="iot-edge-agent"></a>IoT Edge 에이전트

[모듈](#module)배포 및 모니터링을 담당 하는 [IoT Edge 런타임의](#iot-edge-runtime) 일부입니다.

적용 대상: IoT Edge

### <a name="iot-edge-device"></a>IoT Edge 디바이스

컨테이너 화 된 [IoT Edge](#iot-edge) [모듈](#module) 을 사용 하 여 Azure 서비스, 타사 서비스 또는 자신의 코드를 실행 하는 [장치](#device) 입니다. 장치에서 [IoT Edge 런타임은](#iot-edge-runtime) 모듈을 관리 합니다. 클라우드에서 IoT Edge 디바이스를 원격으로 모니터링하고 관리할 수 있습니다.

적용 대상: IoT Edge

### <a name="iot-edge-hub"></a>IoT Edge 허브

모듈, 업스트림 및 다운스트림 통신을 담당 [하는](#module) [IoT Edge 런타임의](#iot-edge-runtime) 일부입니다.

적용 대상: IoT Edge

### <a name="iot-edge-runtime"></a>IoT Edge 런타임

IoT Edge [디바이스에](#iot-edge-device)설치하기 위해 Microsoft에서 배포하는 모든 것을 포함합니다. Edge 에이전트, Edge 허브 및 [IoT Edge](#iot-edge) 보안 디먼이 포함됩니다.

적용 적용: IoT Edge

### <a name="iot-hub"></a>IoT Hub

수백만 개의 [디바이스와](#device) [솔루션](#solution) 백 엔드 간에 안정적이고 안전한 양방향 통신을 가능하게 하는 완전 관리형 Azure 서비스입니다. 자세한 내용은 [Azure IoT Hub란?](../iot-hub/about-iot-hub.md)을 참조하세요. Azure 구독을 사용하여 IoT 메시징 워크로드를 처리하는 IoT Hub를 만들 수 있습니다.

[자세한 정보](../iot-hub/about-iot-hub.md)

적용된 내용: Ot Hub

### <a name="iot-hub-resource-rest-api"></a>IoT Hub 리소스 REST API

허브 만들기, 업데이트 및 삭제와 같은 작업을 사용하여 Azure 구독에서 [IoT Hub를](#iot-hub) 관리하는 데 사용할 수 있는 API입니다.

적용된 내용: Ot Hub

### <a name="iot-hub-metrics"></a>IoT Hub 메트릭

IoT Hub의 상태를 모니터링할 수 있는 Azure Portal [기능입니다.](#iot-hub) IoT Hub 메트릭을 사용하면 IoT Hub 및 연결된 [디바이스의](#device) 전반적인 상태를 평가할 수 있습니다.

적용된 내용: Ot Hub

### <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어

[작업,](#job)디지털 트윈 및 [디바이스](#device-twin) [쌍을](#digital-twin)쿼리할 수 있는 IoT Hub 대한 [SQL](#iot-hub) 같은 언어입니다.

적용된 내용: Ot Hub

### <a name="iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지

Windows 또는 Linux [게이트웨이에](#gateway) 연결된 기존 센서 및 주변 장치를 [IoT 플러그 앤 플레이 디바이스로](#iot-plug-and-play-device)연결할 수 있도록 하는 오픈 소스 애플리케이션입니다.

적용 대상: Iot Hub, 디바이스 개발자 IoT Central

### <a name="iot-plug-and-play-conventions"></a>IoT 플러그 앤 플레이 규칙

IoT [디바이스가](#device) [솔루션과](#solution)데이터를 교환할 때 따라야 하는 규칙 집합입니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자

### <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 디바이스

일반적으로 데이터를 수집하거나 다른 [디바이스를](#device) 제어하고 디바이스 모델을 구현하는 소프트웨어 또는 펌웨어를 실행하는 소규모 독립 실행형 컴퓨팅 [디바이스입니다.](#device-model) 예를 들어, IoT 플러그 앤 플레이 디바이스는 환경 모니터링 디바이스이거나 스마트 농업 관개 시스템용 컨트롤러일 수 있습니다. IoT 플러그 앤 플레이 디바이스는 직접 또는 [IoT Edge](#iot-edge) [모듈로](#module)구현될 수 있습니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자

### <a name="iot-extension-for-azure-cli"></a>Azure CLI에 대한 IoT 확장

Azure CLI 대한 확장입니다. 확장을 사용하면 [ID 레지스트리에서](#identity-registry)디바이스 관리, [디바이스](#device) 메시지 보내기 및 받기, [IoT Hub](#iot-hub) 작업 모니터링 등의 작업을 완료할 수 있습니다.

[자세한 정보](/cli/azure/azure-cli-reference-for-IoT)

적용 대상: Ot Hub, IoT Central, IoT Edge, Device Provisioning Service, 디바이스 개발자

## <a name="j"></a>J

### <a name="job"></a>작업

[IoT Hub](#iot-hub)컨텍스트에서 작업을 사용하면 IoT Hub에 등록된 [디바이스](#device) 집합에서 활동을 예약하고 추적할 수 있습니다. 활동에는 디바이스 [쌍](#device-twin) [desired 속성](#desired-properties)업데이트, 디바이스 쌍 [태그](#tag)업데이트 및 직접 [메서드](#direct-method)호출이 포함됩니다. IoT Hub는 ID 레지스트리에서 [가져오고 내보내는](#identity-registry) 작업도 사용합니다.

IoT Central 컨텍스트에서 작업을 사용하면 [속성을](#properties) 설정하고 명령을 호출하여 연결된 디바이스를 대량으로 관리할 수 [있습니다.](#command) 또한 IoT Central 작업을 통해 클라우드 속성을 대량으로 업데이트할 수 있습니다.

적용할 내용: Ot Hub, IoT Central

## <a name="l"></a>L

### <a name="leaf-device"></a>리프 디바이스

다운스트림 [디바이스가](#device) 연결되지 않은 디바이스. 일반적으로 리프 디바이스는 [게이트웨이 디바이스](#gateway-device)에 연결됩니다.

적용 대상: IoT Edge, IoT Central, 디바이스 개발자

### <a name="lifecycle-event"></a>수명 주기 이벤트

[Azure Digital Twins](#azure-digital-twins)이 유형의 이벤트는 [디지털 트윈,](#digital-twin) [관계](#relationship)또는 [이벤트 처리기와](#event-handler) 같은 데이터 항목이 [Azure Digital Twins 인스턴스에서](#azure-digital-twins-instance)만들어지거나 삭제되면 발생합니다.

적용: Digital Twins, Ot Hub, IoT Central

### <a name="linked-iot-hub"></a>연결된 IoT 허브

[Device Provisioning Service](#device-provisioning-service) 인스턴스에 연결된 [IoT Hub입니다.](#iot-hub) DPS 인스턴스는 [디바이스](#device) ID를 등록하고 연결된 IoT Hub의 [디바이스 쌍에서](#device-twin) 초기 [구성을](#configuration) 설정할 수 있습니다.

적용 내용: Ot Hub, Device Provisioning Service

## <a name="m"></a>M

### <a name="mqtt"></a>MQTT

[디바이스와의](#device)통신을 지원하고 [IoT Hub](#iot-hub) IoT Central 메시징 프로토콜 중 하나입니다. MQTT는 어떤 것도 의미하지 않습니다.

[자세한 정보](../iot-hub/iot-hub-devguide-protocols.md)

적용 대상: Ot Hub, IoT Central, 디바이스 개발자

### <a name="model"></a>모델

[속성,](#properties)원격 분석 및 구성 요소를 포함하여 물리적 환경에서 엔터티 형식의 [정의입니다.](#component) 모델은 이러한 형식의 특정 물리적 개체를 나타내는 [디지털 쌍](#digital-twin)을 만드는 데 사용됩니다. 모델은 [디지털 쌍 정의 언어](#digital-twins-definition-language)로 작성됩니다.

[Azure Digital Twins](#azure-digital-twins) 서비스에서 모델은 [디바이스](#device) 또는 상위 수준의 추상 비즈니스 개념을 정의합니다. IoT 플러그 앤 플레이 [디바이스 모델은](#device-model) 디바이스를 구체적으로 설명합니다.

적용 대상: Ot Hub, IoT Central, Digital Twins, 디바이스 개발자

### <a name="model-id"></a>모델 ID

[IoT 플러그 앤 플레이 디바이스가](#iot-plug-and-play-device) [IoT Hub](#iot-hub) 또는 IoT Central 애플리케이션에 연결되면 구현하는 Digital Twins [정의 언어](#digital-twins-definition-language) 모델의 모델 ID를 보냅니다. [](#model) 모든 모델은 고유한 모델 ID입니다. 이 모델 ID를 사용하면 [솔루션에서](#solution) [디바이스 모델](#device-model)를 찾을 수 있습니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자 Digital Twins

### <a name="model-repository"></a>모델 리포지토리

[Digital Twins 정의 언어](#digital-twins-definition-language) [모델](#model) 및 [인터페이스를 저장합니다.](#interface) [솔루션은](#solution) [모델 ID를](#model-id) 사용하여 리포지토리에서 모델을 검색합니다.

적용: Ot Hub, IoT Central, Digital Twins

### <a name="model-repository-rest-api"></a>Model repository REST API

[모델 리포지토리를](#model-repository)관리하고 상호 작용하기 위한 API입니다. 예를 들어 이 API를 사용하여 [디바이스 모델](#device-model)을 추가하고 검색할 수 있습니다.

적용: Ot Hub, IoT Central, Digital Twins

### <a name="module"></a>모듈

[IoT Hub](#iot-hub) [디바이스](#device) SDK를 사용하면 각각 IoT Hub에 대한 독립적인 연결을 여는 모듈을 인스턴스화할 수 있습니다. 이렇게 하면 디바이스의 여러 [구성 요소에](#component) 대해 별도의 네임스페이스를 사용할 수 있습니다.

[모듈 ID](#module-identity) 및 [모듈 쌍은](#module-twin) [디바이스 ID](#device-identity) 및 [디바이스 쌍과](#device-twin) 동일한 기능을 제공하지만 더 세밀하게 제공합니다.

[IoT Edge](#iot-edge)모듈은 [IoT Edge 디바이스에](#iot-edge-device)배포할 수 있는 Docker 컨테이너입니다. 디바이스에서 메시지 수집, 메시지 변환 또는 IoT Hub로 메시지 보내기와 같은 특정 작업을 수행합니다. 다른 모듈과 통신하고 [IoT Edge 런타임](#iot-edge-runtime)에 데이터를 전송합니다.

적용 대상: Ot Hub, IoT Edge, 디바이스 개발자

### <a name="module-identity"></a>모듈 ID

[디바이스](#device)에 속하는 모든 [모듈에](#module) 할당된 고유 식별자입니다. 모듈 ID는 [ID 레지스트리에도 등록됩니다.](#identity-registry)

모듈 ID는 모듈이 IoT Hub 인증하는 데 사용하는 보안 자격 증명 또는 [IoT Edge](#iot-hub) [허브에](#iot-edge-hub) [대한 IoT Edge](#iot-edge) 모듈의 경우 자세히 설명합니다.

적용 대상: Ot Hub, IoT Edge, 디바이스 개발자

### <a name="module-image"></a>모듈 이미지

[IoT Edge 런타임에서](#iot-edge-runtime) [모듈](#module) 인스턴스를 인스턴스화하기 위해 사용하는 docker 이미지입니다.

적용 적용: IoT Edge

### <a name="module-twin"></a>모듈 쌍

디바이스 [쌍과](#device-twin)마찬가지로 [모듈](#module) 쌍은 메타데이터, [구성](#configuration)및 조건과 같은 모듈 상태 정보를 저장하는 JSON 문서입니다. [IoT Hub](#iot-hub) IoT Hub의 디바이스 [ID로](#module-identity) 프로비전하는 각 모듈 [ID에](#device-identity) 대한 모듈 쌍을 유지합니다. 모듈 쌍을 사용하면 모듈과 [솔루션](#solution) 백 엔드 간에 모듈 조건 및 구성을 동기화할 수 있습니다. 모듈 쌍을 쿼리하여 특정 모듈을 찾고 장기 실행 중인 작업의 상태를 쿼리할 수 있습니다.

적용된 내용: Ot Hub

## <a name="o"></a>O

### <a name="ontology"></a>온톨로지

[Digital Twins](#digital-twin)컨텍스트에서 부동산, 스마트 도시, IoT 시스템, 에너지 그리드 등과 같은 특정 도메인에 대한 [모델](#model) 집합입니다. 온톨로지는 산업 표준 및 모범 사례에 따라 시작점을 제공하기 때문에 [Azure Digital Twins](#azure-digital-twins)와 같은 정보 그래프의 스키마로 사용되는 경우가 많습니다.

[자세한 정보](../digital-twins/concepts-ontologies.md)

적용 적용: Digital Twins

### <a name="operations-monitoring"></a>작업 모니터링

IoT Hub에서 작업의 상태를 실시간으로 모니터링할 수 있는 [IoT Hub](#iot-hub) 기능입니다. IoT Hub는 몇 가지 작업 범주에 걸쳐 이벤트를 추적합니다. 처리를 위해 하나 이상의 범주에서 IoT Hub [엔드포인트로](#endpoint) 이벤트를 보내도록 선택할 수 있습니다. 데이터에 오류가 있는지 모니터링하거나 데이터 패턴을 기반으로 좀 더 복잡한 처리를 설정할 수 있습니다.

적용된 내용: Ot Hub

## <a name="p"></a>P

### <a name="physical-device"></a>물리적 디바이스

IoT Hub에 연결하는 실제 [IoT](#iot-hub) [디바이스입니다.](#device) 편의를 위해 많은 자습서와 빠른 시작에서 물리적 디바이스가 아닌 데스크톱 머신에서 IoT 디바이스 코드를 실행합니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자 IoT Edge

### <a name="primary-and-secondary-keys"></a>기본 및 보조 키

[IoT Hub](#iot-hub) 또는 IoT Central 애플리케이션에서 [디바이스](#device)연결 또는 서비스 연결 [엔드포인트에](#endpoint) 연결하는 경우 [연결 문자열에](#connection-string) 액세스 권한을 부여하는 키가 포함됩니다. 디바이스를 [ID 레지스트리](#identity-registry)에 추가하거나 허브에 [공유 액세스 정책](#shared-access-policy)을 추가하는 경우 서비스에서 기본 및 보조 키를 생성합니다. 두 개의 키를 사용하면 IoT Hub 또는 IoT Central 애플리케이션에 대한 액세스 권한을 손실하지 않고 키를 업데이트할 때 한 키에서 다른 키로 롤오버할 수 있습니다.

적용할 내용: Ot Hub, IoT Central

### <a name="properties"></a>속성

디지털 트윈의 컨텍스트에서 데이터 필드는 [디지털 트윈의](#digital-twin)일부 영구 상태를 나타내는 [인터페이스에](#interface) 정의됩니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 일련 번호와 같은 읽기 전용 속성은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 자체에서 실행되는 코드에 의해 설정됩니다. 경보 임계값과 같은 쓰기 가능한 속성은 일반적으로 클라우드 기반 IoT [솔루션](#solution)에서 설정됩니다.

적용 대상: Ot Hub, IoT Central, Digital Twins, 디바이스 개발자

### <a name="property-change-event"></a>속성 변경 이벤트

[디지털 쌍](#digital-twin)의 속성 변경으로 인해 발생하는 이벤트입니다.

적용: Ot Hub, IoT Central, Digital Twins

### <a name="protocol-gateway"></a>프로토콜 게이트웨이

일반적으로 [IoT Hub](#iot-hub) 또는 IoT Central 애플리케이션에 연결하는 [디바이스에](#device) 대한 프로토콜 변환 서비스를 제공하기 위해 클라우드에 배포되는 [게이트웨이입니다.](#gateway)

적용할 내용: Ot Hub, IoT Central

## <a name="r"></a>R

### <a name="registration"></a>등록

[IoT Hub](#iot-hub) ID 레지스트리에 있는 [디바이스의](#device) [레코드입니다.](#identity-registry) 디바이스를 직접 등록할 수도 있고 [Device Provisioning Service](#device-provisioning-service)를 사용하여 디바이스 등록을 자동화할 수도 있습니다.

적용: Ot Hub, IoT Central, Device Provisioning Service

### <a name="registration-id"></a>등록 ID

Device Provisioning Service 의 고유한 [디바이스 ID입니다.](#device-provisioning-service) [](#device-identity) [등록](#registration) ID는 [디바이스](#device) ID와 동일한 값일 수 있습니다.

적용: Ot Hub, IoT Central, Device Provisioning Service

### <a name="relationship"></a>관계

[Azure Digital Twins](#azure-digital-twins) 서비스에서 [디지털 트윈을](#digital-twin) 전체 물리적 환경을 디지털 방식으로 나타내는 지식 그래프에 연결하는 데 사용됩니다. 트윈이 가질 수 있는 관계 형식은 [Digital Twins 정의 언어](#digital-twins-definition-language) [모델](#model)에서 정의됩니다.

적용 적용: Digital Twins

### <a name="reported-configuration"></a>Reported 구성

[디바이스 쌍의](#device-twin)컨텍스트에서 이는 [솔루션](#solution) 백 엔드에 보고되는 [디바이스](#device) 쌍의 [전체 속성](#properties) 및 메타데이터 집합을 나타냅니다.

적용 대상: Ot Hub, 디바이스 개발자

### <a name="reported-properties"></a>reported 속성

[디바이스 쌍의](#device-twin)컨텍스트에서 reported [속성은](#properties) [디바이스](#device) 쌍의 하위 섹션입니다. Reported 속성은 디바이스에서만 설정할 수 있지만 [백 엔드 앱](#back-end-app)에서 읽고 쿼리할 수 있습니다.

적용 대상: Ot Hub, 디바이스 개발자

### <a name="retry-policy"></a>다시 시도 정책

클라우드 서비스에 연결할 때 일시적인 오류를 처리하는 방법입니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자

### <a name="routing-rule"></a>라우팅 규칙

디바이스-클라우드 메시지를 [솔루션](#solution) 백 [엔드에서](#device-to-cloud) 처리하기 위해 기본 제공 엔드포인트 또는 [사용자 지정 엔드포인트로](#custom-endpoints) 라우팅하는 데 사용되는 [IoT Hub](#iot-hub) 기능입니다. [](#endpoint)

적용된 내용: Ot Hub

## <a name="s"></a>S

### <a name="saslplain"></a>SASL/PLAIN

[고급 메시지 큐 프로토콜이](#advanced-message-queueing-protocol) 보안 토큰을 전송하는 데 사용하는 프로토콜입니다.

[자세한 정보](https://wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

적용된 내용: Ot Hub

### <a name="service-rest-api"></a>서비스 REST API

[솔루션](#solution) 백 엔드에서 [디바이스를](#device)관리하는 데 사용할 수 있는 REST API. 예를 들어 [Iot Hub](#iot-hub) 서비스 API를 사용하여 [디바이스 쌍](#device-twin) [속성을](#properties)검색 및 업데이트하고, [직접 메서드를](#direct-method)호출하고, 작업을 예약할 수 [있습니다.](#job) 일반적으로 상위 수준 서비스 SDK 중 하나를 사용해야 합니다.

적용: Ot Hub, IoT Central, Device Provisioning Service, IoT Edge

### <a name="service-operations-endpoint"></a>서비스 작업 엔드포인트

관리자가 서비스 설정을 관리하는 데 사용하는 [엔드포인트입니다.](#endpoint) 예를 들어 [Device Provisioning Service에서](#device-provisioning-service) 서비스 엔드포인트를 사용하여 [등록을 관리합니다.](#enrollment)

적용: Ot Hub, Device Provisioning Service, IoT Edge, Digital Twins

### <a name="shared-access-policy"></a>공유 액세스 정책

해당 정책과 연결된 유효한 기본 또는 보조 키가 있는 사용자에게 부여된 사용 권한을 정의하는 방법입니다. 포털에서 허브에 대한 공유 액세스 정책 및 키를 관리할 수 있습니다.

적용: Ot Hub, IoT Edge, Device Provisioning Service

### <a name="shared-access-signature"></a>공유 액세스 서명

공유 액세스 서명은 [IoT Hub](#iot-hub) [엔드포인트](#endpoint)와 같은 하나 이상의 리소스를 가리키는 서명된 URI입니다. URI에는 클라이언트가 리소스에 액세스하는 방법을 나타내는 토큰이 포함되어 있습니다. 쿼리 매개 변수 중 하나인 서명은 SAS 매개 변수에서 구성되고 SAS를 만드는 데 사용된 키로 서명됩니다. 이 서명은 Azure Storage에서 스토리지 리소스에 대한 액세스 권한을 부여하는 데 사용됩니다.

적용: Ot Hub, Digital Twins, IoT Central, IoT Edge

### <a name="simulated-device"></a>시뮬레이션된 디바이스

편의를 위해 대부분의 자습서 및 빠른 시작은 로컬 개발 머신에서 시뮬레이션된 센서를 통해 [디바이스](#device) 코드를 실행합니다. 반면, MXCHIP와 같은 [물리적 디바이스에는](#physical-device) 실제 센서가 있으며 [IoT Hub에](#iot-hub)연결됩니다.

적용 대상: Ot Hub, IoT Central, 디바이스 개발자, IoT Edge, Digital Twins, Device Provisioning Service

### <a name="solution"></a>솔루션

IoT의 컨텍스트에서 *솔루션은* 일반적으로 [디바이스,](#device) [디바이스 앱,](#device-app)IoT Hub, 기타 Azure 서비스 및 백 엔드 [앱과](#back-end-app)같은 요소를 포함하는 [IoT](#iot-hub)솔루션을 나타냅니다.

적용: Ot Hub, IoT Central, Device Provisioning Service, IoT Edge, Digital Twins

### <a name="system-properties"></a>시스템 속성

[디바이스 쌍의](#device-twin)컨텍스트에서 마지막 작업 시간 및 연결 상태와 같은 [디바이스](#device) 사용량에 대한 정보를 포함하는 읽기 전용 [속성입니다.](#properties)

적용된 내용: Ot Hub

## <a name="t"></a>T

### <a name="tag"></a>태그

[디바이스 쌍의](#device-twin)컨텍스트에서 태그는 [JSON](#solution) 문서의 형태로 솔루션 백 엔드에 의해 저장되고 검색되는 [디바이스](#device) 메타데이터입니다. 태그는 디바이스 앱에 표시되지 않습니다.

적용 대상: Iot Hub

### <a name="target-condition"></a>대상 조건

[IoT Edge](#iot-edge) 배포에서 대상 조건은 배포의 대상 [장치](#device) 를 선택 합니다. 대상 조건은 요구 사항을 충족하는 새 디바이스를 포함하거나 더 이상 요구 사항을 충족하지 않는 디바이스를 제거하기 위해 지속적으로 평가됩니다.

적용 대상: IoT Edge

### <a name="telemetry"></a>원격 분석

바람 속도 또는 온도와 같은 데이터는 [장치](#device) 에서 센서 로부터 수집 된 [IoT hub](#iot-hub) 에 전송 됩니다.

[속성](#properties)과 달리 원격 분석은 [디지털 쌍](#digital-twin)에 저장되지 않으며, 발생 시 처리해야 하는 시간 제한 데이터 이벤트의 스트림입니다.

IoT 플러그 앤 플레이 및 [Azure Digital Twins](#azure-digital-twins)에서 [인터페이스](#interface) 에 정의 된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정값은 일반적으로 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 등 디바이스에서 데이터 스트림으로 보내는 센서 판독값과 같은 값입니다.

적용 대상: Iot Hub, IoT Central, 디지털 쌍, IoT Edge, 장치 개발자

### <a name="telemetry-event"></a>원격 분석 이벤트

[원격 분석](#telemetry) 데이터 도착을 나타내는 [IoT hub](#iot-hub) 의 이벤트입니다.

적용 대상: Iot Hub

### <a name="twin-queries"></a>쌍 쿼리

SQL 유사한 쿼리 언어를 사용 하 여 [장치](#device-twin) 쌍 또는 [모듈](#module-twin)쌍에서 정보를 검색할 수 있도록 하는 [IoT Hub](#iot-hub) 기능입니다.

적용 대상: Iot Hub

### <a name="twin-synchronization"></a>쌍 동기화

[장치](#device-twin) 쌍 또는 [모듈](#module-twin) 쌍에서 [원하는 속성](#desired-properties) 을 사용 하는 [IoT Hub](#iot-hub) 프로세스는 장치 또는 [모듈](#module) 을 구성 하 고 해당 [장치](#device) 에서 [보고 된 속성](#reported-properties) 을 검색 하 여 쌍에 저장 합니다.

적용 대상: Iot Hub

## <a name="u"></a>U

### <a name="upstream-service"></a>업스트림 서비스

현재 컨텍스트로 데이터를 내보내는 서비스를 설명하는 상대 용어입니다. 예를 들어 [Azure Digital twins](#azure-digital-twins)의 컨텍스트에서는 데이터가 IoT Hub에서 Azure [digital twins](#digital-twin)로 전달 되기 때문에 [IoT Hub](#iot-hub) 업스트림 서비스로 간주 됩니다.

적용 대상: Iot Hub, IoT Central, 디지털 쌍

