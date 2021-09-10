---
title: 자습서 - Azure IoT 태양광 패널 모니터링 | Microsoft Docs
description: 이 자습서에서는 IoT Central용 태양광 패널 모니터링 애플리케이션 템플릿을 배포하고 사용하는 방법을 보여 줍니다.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 59650af808a5af947cbb2fd6df1f203692fb7034
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182781"
---
# <a name="tutorial-deploy-and-walk-through-the-solar-panel-monitoring-app-template"></a>자습서: 태양광 패널 모니터링 앱 템플릿 배포 및 연습 

IoT Central *태양광 패널 모니터링* 애플리케이션 템플릿과 이 문서의 지침을 사용하여 엔드투엔드 태양광 패널 모니터링 솔루션을 개발합니다.

  :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-app-architecture.png" alt-text="태양광 패널 아키텍처.":::

이 아키텍처는 다음과 같은 구성 요소로 구성됩니다. 일부 애플리케이션에는 여기에 나열된 모든 구성 요소가 필요하지 않을 수 있습니다.

### <a name="solar-panels-and-connectivity"></a>태양 전지판 및 연결

태양 전지판은 재생 에너지의 중요한 소스 중 하나입니다. 태양 전지판 유형 및 설정에 따라 게이트웨이이나 다른 중간 디바이스 및 독점 시스템을 사용하여 연결할 수 있습니다. 직접 연결할 수 없는 디바이스를 연결하기 위해 IoT Central 디바이스 브리지를 빌드해야 할 수도 있습니다. IoT Central 디바이스 브리지는 오픈 소스 솔루션이며 [여기](../core/howto-build-iotc-device-bridge.md)에서 전체 세부 정보를 확인할 수 있습니다. 

### <a name="iot-central-platform"></a>IoT Central 플랫폼

Azure IoT Central은 IoT 솔루션의 빌드를 간소화하고 IoT 관리, 운영 및 개발 부담과 비용을 줄여주는 플랫폼입니다. IoT Central을 사용하면 대규모로 손쉽게 IoT(사물 인터넷) 자산을 연결, 모니터링 및 관리할 수 있습니다. 태양 전지판을 IoT Central에 연결하면 앱 템플릿에서 디바이스 모델, 명령, 대시보드 등의 기본 제공 기능을 사용합니다. 또한 앱 템플릿은 근 실시간 측정기 데이터 모니터링, 분석, 규칙 및 시각화와 같은 웜 경로 시나리오에 IoT Central 스토리지를 사용합니다.

### <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central을 사용하여 빌드하는 확장성 옵션

IoT Central 플랫폼은 두 가지 확장성 옵션인 CDE(연속 데이터 내보내기) 및 API를 제공합니다. 고객 및 파트너는 특정 요구 사항에 맞게 솔루션을 사용자 지정하기 위해 이러한 옵션 중에서 선택할 수 있습니다. 예를 들어 파트너 중 하나가 ADLS(Azure Data Lake Storage)를 사용하여 CDE를 구성했습니다. 장기 데이터 보존 및 기타 콜드 경로 스토리지 시나리오(일괄 처리, 감사 및 보고 목적)에 ADLS를 사용하고 있습니다. 


이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]

> * 무료로 태양광 패널 앱 만들기
> * 애플리케이션 살펴보기
> * 리소스 정리


## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* 무료 가격 책정 요금제를 사용하거나 Azure 구독을 사용할 수 있습니다.


## <a name="create-a-solar-panel-monitoring-application"></a>태양광 패널 모니터링 애플리케이션 만들기


1. [Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음, **에너지** 탭을 선택합니다.

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-build.png" alt-text="스마트 미터 템플릿":::

1. **태양광 패널 모니터링** 에서 **앱 만들기** 를 선택합니다.

자세한 내용은 [IoT Central 애플리케이션 만들기](../core/howto-create-iot-central-application.md)를 참조하세요.

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기

다음 섹션에서는 애플리케이션의 주요 기능을 안내합니다.

### <a name="dashboard"></a>대시보드

애플리케이션 템플릿을 배포한 후에는 좀 더 자세히 앱을 살펴볼 수 있습니다. 샘플 스마트 미터 디바이스, 디바이스 모델 및 대시보드와 함께 제공됩니다.

Adatum은 태양광 패널을 모니터링 및 관리하는 가상의 에너지 회사입니다. 태양광 패널 모니터링 대시보드에는 태양광 패널 속성, 데이터 및 샘플 명령이 표시됩니다. 이 대시보드를 사용하면 문제가 발생하여 추가 지원이 필요하기 전에 사용자와 사용자의 지원 팀이 다음 활동을 사전에 수행할 수 있습니다.
* 지도에서 최신 패널 정보와 설치된 [위치](../core/howto-use-location-data.md)를 검토합니다.
* 패널 상태 및 연결 상태를 확인합니다.
* 에너지 생성 및 온도 추세를 검토하여 비정상적인 패턴을 파악합니다.
* 계획 및 청구를 위해 총 에너지 생성을 추적합니다.
* 패널을 활성화하고 필요한 경우 펌웨어 버전을 업데이트합니다. 템플릿의 명령 단추는 가능한 기능을 보여주지만 실제 명령을 보내지는 않습니다.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png" alt-text="태양광 패널 모니터링 템플릿 대시보드의 스크린샷.":::

### <a name="devices"></a>디바이스

앱과 함께 샘플 태양광 패널 디바이스가 제공됩니다. 디바이스 세부 정보를 보려면 **디바이스** 를 선택합니다.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device.png" alt-text="태양광 패널 모니터링 템플릿 디바이스의 스크린샷.":::

샘플 디바이스 **SP0123456789** 를 선택합니다. **속성 업데이트** 탭에서 디바이스의 쓰기 가능 속성을 업데이트할 수 있고, 업데이트된 값을 대시보드에 표시할 수 있습니다. 

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png" alt-text="태양광 패널 모니터링 템플릿 업데이트 탭의 스크린샷.":::


### <a name="device-template"></a>디바이스 템플릿

태양광 패널 디바이스 모델을 보려면 **디바이스 템플릿** 탭을 선택합니다. 모델에는 데이터, 속성, 명령 및 보기에 대해 미리 정의된 인터페이스가 있습니다.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png" alt-text="태양광 패널 모니터링 템플릿 디바이스 템플릿의 스크린샷.":::


## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션이 더 이상 필요 없는 경우 다음 단계에 따라 애플리케이션을 삭제합니다.

1. 왼쪽 창에서 **관리** 를 선택합니다.
1. **애플리케이션 설정** > **설정** 을 선택합니다. 

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png" alt-text="태양광 패널 모니터링 템플릿 관리의 스크린샷.":::
