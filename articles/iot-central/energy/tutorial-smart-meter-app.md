---
title: 자습서 - Azure IoT 스마트 미터 모니터링 | Microsoft Docs
description: 이 자습서에서는 IoT Central용 스마트 미터 모니터링 애플리케이션 템플릿을 배포하고 사용하는 방법을 보여 줍니다.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a332ab10ce4e7c38442288165c56d1161081cd9c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179482"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-meter-monitoring-app-template"></a>자습서: 스마트 미터 모니터링 앱 템플릿 배포 및 연습 

IoT Central *스마트 미터 모니터링* 애플리케이션 템플릿과 이 문서의 지침을 사용하여 엔드투엔드 스마트 미터 모니터링 솔루션을 개발합니다.

  :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-app-architecture.png" alt-text="스마트 측정기 아키텍처.":::

이 아키텍처는 다음과 같은 구성 요소로 구성됩니다. 일부 솔루션에는 여기에 나열된 모든 구성 요소가 필요하지 않을 수 있습니다.

### <a name="smart-meters-and-connectivity"></a>스마트 미터 및 연결

스마트 미터는 모든 에너지 자산에서 가장 중요한 디바이스 중 하나입니다. 에너지 소비 데이터를 기록하고 모니터링 및 기타 사용 사례(예: 청구 및 수요 대응)를 위해 유틸리티로 전달합니다. 미터 유형에 따라 게이트웨이나 다른 중간 디바이스 또는 시스템(예: 에지 디바이스 및 헤드엔드 시스템)을 사용하여 IoT Central에 연결할 수 있습니다. 직접 연결할 수 없는 디바이스를 연결하기 위해 IoT Central 디바이스 브리지를 빌드합니다. IoT Central 디바이스 브리지는 오픈 소스 솔루션이며 [여기](../core/howto-build-iotc-device-bridge.md)에서 전체 세부 정보를 확인할 수 있습니다. 

### <a name="iot-central-platform"></a>IoT Central 플랫폼

Azure IoT Central은 IoT 솔루션의 빌드를 간소화하고 IoT 관리, 운영 및 개발 부담과 비용을 줄여주는 플랫폼입니다. IoT Central을 사용하면 대규모로 손쉽게 IoT(사물 인터넷) 자산을 연결, 모니터링 및 관리할 수 있습니다. 스마트 미터를 IoT Central에 연결하면 앱 템플릿에서 디바이스 모델, 명령, 대시보드 등의 기본 제공 기능을 사용합니다. 또한 앱 템플릿은 근 실시간 측정기 데이터 모니터링, 분석, 규칙 및 시각화와 같은 웜 경로 시나리오에 IoT Central 스토리지를 사용합니다. 

### <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central을 사용하여 빌드하는 확장성 옵션

IoT Central 플랫폼은 두 가지 확장성 옵션인 CDE(연속 데이터 내보내기) 및 API를 제공합니다. 고객 및 파트너는 특정 요구 사항에 맞게 솔루션을 사용자 지정하기 위해 이러한 옵션 중에서 선택할 수 있습니다. 예를 들어 파트너 중 하나가 ADLS(Azure Data Lake Storage)를 사용하여 CDE를 구성했습니다. 장기 데이터 보존 및 기타 콜드 경로 스토리지 시나리오(일괄 처리, 감사 및 보고 목적)에 ADLS를 사용하고 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

- 무료로 스마트 미터 앱 만들기
- 애플리케이션 연습
- 리소스 정리

## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* 무료 가격 책정 요금제를 사용하거나 Azure 구독을 사용할 수 있습니다.

## <a name="create-a-smart-meter-monitoring-application"></a>스마트 측정기 모니터링 애플리케이션 만들기

1. [Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음, **에너지** 탭을 선택합니다.

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-build.png" alt-text="스마트 미터 템플릿":::

1. **스마트 미터 모니터링** 에서 **앱 만들기** 를 선택합니다.

자세한 내용은 [IoT Central 애플리케이션 만들기](../core/howto-create-iot-central-application.md)를 참조하세요.

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기

다음 섹션에서는 애플리케이션의 주요 기능을 안내합니다.

### <a name="dashboard"></a>대시보드

애플리케이션 템플릿을 배포한 후에는 샘플 스마트 미터 디바이스, 디바이스 모델 및 대시보드가 제공됩니다. 

Adatum은 스마트 미터를 모니터링 및 관리하는 가상의 에너지 회사입니다. 스마트 미터 모니터링 대시보드에는 스마트 미터 속성, 데이터 및 샘플 명령이 표시됩니다. 운영자와 지원 팀은 지원 인시던트가 발생하기 전에 다음 작업을 선제적으로 수행할 수 있습니다. 
* 맵의 최신 미터 정보 및 설치된 [위치](../core/howto-use-location-data.md) 검토
* 미터 네트워크 및 연결 상태를 사전에 확인 
* 네트워크 상태의 최소 및 최대 전압 판독값 모니터링 
* 에너지, 전력 및 전압 추세를 검토하여 비정상적인 패턴 파악 
* 계획 및 요금 청구 목적에 사용할 수 있도록 총 에너지 사용량 추적
* 미터 다시 연결 및 펌웨어 버전 업데이트와 같은 명령 및 제어 작업. 템플릿의 명령 단추는 가능한 기능을 보여주지만 실제 명령을 보내지는 않습니다. 

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png" alt-text="스마트 미터 모니터링 대시보드.":::

### <a name="devices"></a>디바이스

앱과 함께 샘플 스마트 미터 디바이스가 제공됩니다. **디바이스** 탭을 클릭하여 디바이스 세부 정보를 볼 수 있습니다.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-devices.png" alt-text="스마트 미터 디바이스.":::

디바이스 세부 정보를 보려면 샘플 디바이스 **SM0123456789** 링크를 클릭합니다. **속성 업데이트** 페이지에서 디바이스의 쓰기 가능 속성을 업데이트하고, 업데이트된 값을 대시보드에 시각화할 수 있습니다.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png" alt-text="스마트 미터 속성.":::

### <a name="device-template"></a>디바이스 템플릿

**디바이스 템플릿** 탭을 클릭하여 스마트 미터 디바이스 모델을 봅니다. 모델에는 데이터, 속성, 명령 및 보기에 대해 미리 정의된 인터페이스가 있습니다.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-template.png" alt-text="스마트 미터 디바이스 템플릿.":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션이 더 이상 필요 없는 경우 다음 단계에 따라 애플리케이션을 삭제합니다.

1. 왼쪽 창에서 [관리] 탭을 엽니다.
1. [애플리케이션 설정]을 선택하고 페이지 아래쪽에서 [삭제] 단추를 클릭합니다. 

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png" alt-text="애플리케이션 삭제.":::

## <a name="next-steps"></a>다음 단계

> [자습서: 태양광 패널 애플리케이션 템플릿 배포 및 살펴보기](tutorial-solar-panel-app.md)

