---
title: 자습서 - Azure IoT 용수 품질 모니터링 | Microsoft Docs
description: 이 자습서에서는 IoT Central용 용수 품질 모니터링 애플리케이션 템플릿을 배포하고 사용하는 방법을 보여 줍니다.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 481a085b1fd5fec55cd34f885dfcda40fec6f5e7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179140"
---
# <a name="tutorial-deploy-and-walk-through-the-water-quality-monitoring-application"></a>자습서: 용수 품질 모니터링 애플리케이션 배포 및 살펴보기

IoT Central *용수 품질 모니터링* 애플리케이션 템플릿과 이 문서의 지침을 사용하여 엔드투엔드 용수 품질 모니터링 솔루션을 개발합니다.


![용수 품질 모니터링 아키텍처](./media/tutorial-waterqualitymonitoring/concepts-water-quality-monitoring-architecture1.png)

### <a name="devices-and-connectivity"></a>디바이스 및 연결

용수 관리 솔루션은 유량계, 용수 품질 모니터, 스마트 밸브, 누수 탐지기와 같은 스마트 워터 디바이스를 사용합니다.

스마트 워터 솔루션의 LPWAN(저전력 광대역 네트워크) 또는 타사 네트워크 사업자를 통해 연결할 수 있습니다. 이러한 유형의 디바이스의 경우 [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md)를 사용하여 디바이스 데이터를 Azure IoT Central의 IoT 애플리케이션으로 전송합니다. IP 기능이 포함되고 IoT Central에 직접 연결할 수 있는 디바이스 게이트웨이를 사용할 수도 있습니다.

### <a name="iot-central"></a>IoT Central

Azure IoT Central은 IoT 솔루션을 신속하게 빌드하고 배포하는 데 도움이 되는 IoT 앱 플랫폼입니다. 솔루션을 브랜딩 및 사용자 지정하거나 타사 서비스와 통합할 수 있습니다.

스마트 워터 디바이스를 IoT Central에 연결하면 애플리케이션에서 디바이스 명령 및 제어, 모니터링 및 경고, RBAC가 기본 제공된 사용자 인터페이스, 구성 가능한 대시보드, 확장성 옵션을 제공합니다.

### <a name="extensibility-and-integrations"></a>확장성 및 통합

Azure IoT Central에서 IoT 애플리케이션을 확장하고 필요에 따라 다음을 수행할 수 있습니다.

* 고급 분석을 위해 IoT 데이터를 변환하고 통합합니다(예: IoT Central 애플리케이션에서 데이터를 지속적으로 내보내 기계 학습 모델 학습).
* IoT Central 애플리케이션의 Power Automate 또는 웹후크를 사용해 작업을 트리거하여 다른 시스템의 워크플로를 자동화합니다.
* IoT Central API를 통해 IoT Central의 IoT 애플리케이션에 프로그래밍 방식으로 액세스합니다.

### <a name="business-applications"></a>비즈니스 애플리케이션

IoT 데이터를 사용하여 워터 유틸리티 내에서 다양한 비즈니스 애플리케이션을 지원할 수 있습니다. [IoT Central 용수 소비 모니터링 애플리케이션](tutorial-water-consumption-monitoring.md)에서 규칙 및 작업을 구성하고 [Connected Field Service](/dynamics365/field-service/connected-field-service)에서 경고를 만들도록 설정할 수 있습니다. IoT Central 규칙에서 Power Automate를 구성하여 애플리케이션 및 서비스에서 워크플로를 자동화합니다. 또한 Connected Field Service의 서비스 활동에 따라 정보를 Azure IoT Central로 다시 보낼 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]

> * **용수 품질 모니터링** 템플릿을 사용하여 용수 품질 모니터링 애플리케이션을 만듭니다.
> * 대시보드를 탐색하고 사용자 지정합니다.
> * 용수 품질 모니터링 디바이스 템플릿을 살펴봅니다.
> * 시뮬레이션된 디바이스를 살펴봅니다.
> * 규칙을 살펴보고 구성합니다.
> * 작업을 구성합니다.
> * 흰색 레이블 지정을 사용하여 애플리케이션 브랜딩을 사용자 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* 무료 가격 책정 요금제를 사용하거나 Azure 구독을 사용할 수 있습니다.

## <a name="create-water-quality-monitoring-application"></a>용수 품질 모니터링 애플리케이션 만들기

다음 단계를 사용하여 애플리케이션을 만듭니다.

1. [Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음 **정부** 탭을 선택합니다. :::image type="content" source="media/tutorial-waterqualitymonitoring/iot-central-government-tab-overview1.png" alt-text="애플리케이션 템플릿":::

1. **용수 품질 모니터링** 에서 **앱 만들기** 를 선택합니다.

자세한 내용은 [IoT Central 애플리케이션 만들기](../core/howto-create-iot-central-application.md)를 참조하세요.

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기

다음 섹션에서는 애플리케이션의 주요 기능을 안내합니다.

### <a name="dashboard"></a>대시보드

애플리케이션이 만들어지면 **Wide World 용수 품질 대시보드** 창이 열립니다.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-dashboard1.png" alt-text="용수 품질 모니터링 대시보드.":::

작성자는 대시보드에서 운영자가 사용할 보기를 만들고 사용자 지정할 수 있습니다. 그러나 사용자 지정을 수행하기 전에 먼저 대시보드를 살펴보세요.

대시보드에 표시되는 모든 데이터는 시뮬레이션된 디바이스 데이터를 기반으로 하며, 다음 섹션에서 설명합니다.

대시보드에 포함된 타일의 종류는 다음과 같습니다.

* **Wide World 용수 유틸리티 이미지 타일**: 대시보드의 왼쪽 위 모서리에 있는 첫 번째 타일은 Wide World라는 가상 유틸리티를 보여주는 이미지입니다. 사용자 고유의 이미지를 사용할 수 있도록 타일을 사용자 지정하거나 타일을 제거할 수 있습니다.

* **평균 pH KPI 타일**: **지난 30분 동안 평균 pH** 와 같은 KPI 타일은 대시보드 창의 위쪽에 있습니다. KPI 타일을 사용자 지정하고, 각 타일을 다른 유형 및 시간 범위로 설정할 수 있습니다.

* **용수 모니터링 영역 지도**: Azure IoT Central은 애플리케이션에서 직접 설정하여 디바이스 [위치](../core/howto-use-location-data.md)를 표시할 수 있는 Azure Maps를 사용합니다. 또한 애플리케이션의 위치 정보를 디바이스에 매핑한 다음, Azure Maps를 사용하여 정보를 지도에 표시할 수도 있습니다. 마우스로 지도 위를 가리키며 컨트롤을 사용해 보세요.

* **평균 pH 분포 열 지도 차트**: 다양한 시각화 차트를 선택하여 디바이스 원격 분석을 애플리케이션에 가장 적합한 방식으로 표시할 수 있습니다.

* **중요 품질 지표 꺾은선형 차트**: 디바이스 원격 분석을 시간 범위에 걸친 꺾은선형 차트로 그려서 시각화할 수 있습니다.  

* **화학 작용제 농도 막대형 차트**: 디바이스 원격 분석을 막대형 차트로 시각화할 수 있습니다.

* **센서 매개 변수 재설정 타일**: 대시보드에는 운영자가 모니터링 대시보드에서 직접 시작할 수 있는 작업에 대한 타일이 포함되어 있습니다. 디바이스의 속성을 다시 설정하는 것은 이러한 작업의 예입니다.

* **속성 목록 타일**: 대시보드에는 임계값 정보, 디바이스 상태 정보 및 유지 관리 정보를 나타내는 여러 속성 타일이 있습니다.

### <a name="customize-the-dashboard"></a>대시보드 사용자 지정

작성자는 대시보드에서 운영자가 사용할 보기를 사용자 지정할 수 있습니다.

1. **편집** 을 선택하여 **Wide World 용수 품질 대시보드** 창을 사용자 지정합니다. **편집** 메뉴에서 명령을 선택하여 대시보드를 사용자 지정할 수 있습니다. 대시보드가 편집 모드에 있으면 새 타일을 추가하거나 기존 파일을 구성할 수 있습니다.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/edit-dashboard.png" alt-text="대시보드 편집.":::

1. **+ 새로 만들기** 를 선택하여 구성할 수 있는 새 대시보드를 만듭니다. 여러 대시보드를 사용할 수 있으며, 대시보드 메뉴에서 서로 간에 탐색할 수 있습니다.

## <a name="explore-a-water-quality-monitoring-device-template"></a>용수 품질 모니터링 디바이스 템플릿 살펴보기

Azure IoT Central의 디바이스 템플릿은 디바이스 기능을 정의합니다. 사용 가능한 기능은 원격 분석, 속성 및 명령입니다. 작성자는 Azure IoT Central에서 연결된 디바이스의 기능을 나타내는 디바이스 템플릿을 정의할 수 있습니다. 또한 시뮬레이션된 디바이스를 만들어 디바이스 템플릿과 애플리케이션을 테스트할 수도 있습니다.

만든 용수 품질 모니터링 애플리케이션에는 용수 품질 모니터링 디바이스 템플릿이 제공됩니다.

디바이스 템플릿을 보려면 다음을 수행합니다.

1. Azure IoT Central에서 애플리케이션의 맨 왼쪽 창에 있는 **디바이스 템플릿** 을 선택합니다.
1. 디바이스 템플릿 목록에서 **용수 품질 모니터** 를 선택하여 해당 디바이스 템플릿을 엽니다.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template.png" alt-text="디바이스 템플릿.":::

### <a name="customize-the-device-template"></a>디바이스 템플릿 사용자 지정

다음 디바이스 템플릿 설정을 사용자 지정하는 방법을 연습합니다.

1. 디바이스 템플릿 메뉴에서 **사용자 지정** 을 선택합니다.
1. **온도** 원격 분석 유형으로 이동합니다.
1. **표시 이름** 값을 **보고된 온도** 로 변경합니다.
1. 측정 단위를 변경하거나 **최솟값** 및 **최댓값** 을 설정합니다.
1. **저장** 을 선택합니다.

#### <a name="add-a-cloud-property"></a>클라우드 속성을 추가합니다.

1. 디바이스 템플릿 메뉴에서 **클라우드 속성** 을 선택합니다.
1. 새 클라우드 속성을 추가하려면 **+ 클라우드 속성 추가** 를 선택합니다. Azure IoT Central에서는 디바이스와 관련이 있지만 디바이스에서 보낼 필요가 없는 속성을 추가할 수 있습니다. 이러한 속성의 한 가지 예로 설치 영역, 자산 정보 또는 유지 관리 정보와 관련된 경고 임계값이 있습니다.
1. **설치 영역** 을 **표시 이름** 으로 입력하고 **스키마** 로 **문자열** 을 선택합니다.
1. **저장** 을 선택합니다.

### <a name="explore-views"></a>보기 살펴보기

용수 품질 모니터링 디바이스 템플릿에는 미리 정의된 보기가 제공됩니다. 보기는 운영자가 디바이스 데이터를 보고 클라우드 속성을 설정하는 방법을 정의합니다. 보기를 살펴보고 변경하는 방법을 연습합니다.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template-views.png" alt-text="디바이스 템플릿 뷰.":::

### <a name="publish-the-device-template"></a>디바이스 템플릿 게시

변경한 경우 **게시** 를 선택하여 디바이스 템플릿을 게시해야 합니다.

### <a name="create-a-new-device-template"></a>새 디바이스 템플릿 만들기

1. **디바이스 템플릿** 페이지에서 **+ 새로 만들기** 를 선택하여 새 디바이스 템플릿을 만들고 생성 프로세스를 수행합니다.
1. 사용자 지정 디바이스 템플릿을 만들거나, Azure IoT 디바이스 카탈로그에서 디바이스 템플릿을 선택합니다.

## <a name="explore-simulated-devices"></a>시뮬레이션된 디바이스 살펴보기

애플리케이션 템플릿에서 만든 용수 품질 모니터링 애플리케이션에는 두 개의 시뮬레이션된 디바이스가 있습니다. 이러한 디바이스는 용수 품질 모니터링 디바이스 템플릿에 매핑됩니다.

### <a name="view-the-devices"></a>디바이스 보기

1. 애플리케이션의 맨 왼쪽 창에서 **디바이스** 를 선택합니다.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-devices.png" alt-text="디바이스":::

1. 하나의 시뮬레이션된 디바이스를 선택합니다.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitor-device1.png" alt-text="디바이스 1 선택":::

1. **클라우드 속성** 탭에서 **산도(pH) 임계값** 을 **8** 에서 **9** 로 변경하고 **저장** 을 선택합니다.
1. **디바이스 속성** 탭 및 **디바이스 대시보드** 탭을 살펴봅니다.

> [!NOTE]
> 모든 탭은 **디바이스 템플릿 보기** 에서 구성되었습니다.

### <a name="add-new-devices"></a>새 디바이스 추가

1. **디바이스** 탭에서 **+ 새로 만들기** 를 선택하여 새 디바이스를 추가합니다.
1. 제안된 **디바이스 ID** 를 사용하거나 직접 입력합니다. 새 디바이스의 **디바이스 이름** 을 입력할 수도 있습니다.
1. **디바이스 템플릿** 으로 **용수 품질 모니터** 를 선택합니다. 
1. 시뮬레이션된 디바이스를 만들려는 경우 **이 디바이스 시뮬레이션** 이 **예** 로 설정되어 있는지 확인합니다. 
1. **만들기** 를 선택합니다.  

## <a name="explore-and-configure-rules"></a>규칙 살펴보기 및 구성

Azure IoT Central에서는 디바이스 원격 분석을 자동으로 모니터링하는 규칙을 만들 수 있습니다. 이러한 규칙은 조건 중 하나라도 충족되면 작업을 트리거합니다. 가능한 작업 중 하나는 이메일 알림을 보내는 것입니다. 다른 가능한 작업으로 데이터를 다른 서비스에 보내는 Power Automate 작업 또는 웹후크 작업이 있습니다.

만든 용수 품질 모니터링 애플리케이션에는 미리 구성된 두 가지 규칙이 있습니다.

### <a name="view-rules"></a>규칙 보기

1. 애플리케이션의 맨 왼쪽 창에서 **규칙** 을 선택합니다.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-rules.png" alt-text="규칙.":::

1. 애플리케이션의 미리 구성된 규칙 중 하나인 **높은 pH 경고** 를 선택합니다.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-high-ph-alert.png" alt-text="높은 pH 경고 규칙.":::

   **높은 pH 경고** 규칙은 8보다 큰 산도(pH) 조건인지 확인하도록 구성되어 있습니다.

다음으로, 이메일 작업을 규칙에 추가합니다.

1. **+메일** 을 선택합니다.
1. **표시 이름** 상자에서 **높은 pH 경고** 를 입력합니다.
1. **받는 사람** 상자에서 Azure IoT Central 계정과 연결된 이메일 주소를 입력합니다.
1. 필요에 따라 이메일 텍스트에 포함할 메모를 입력합니다.
1. **완료** 를 선택하여 작업을 완료합니다.
1. 규칙을 **사용** 으로 설정하고 **저장** 을 선택합니다.

구성된 조건이 충족되면 이메일을 몇 분 내에 받게 됩니다.

> [!NOTE]
> 조건이 충족될 때마다 애플리케이션에서 이메일을 보냅니다. 해당 규칙에서 자동 이메일 받기를 중지하려면 **사용 안 함** 을 선택하세요.
  
새 규칙을 만들려면 애플리케이션의 맨 왼쪽 창에서 **규칙** 을 선택한 다음, **+ 새로 만들기** 를 선택합니다.

## <a name="configure-jobs"></a>작업 구성

Azure IoT Central 작업을 사용하면 여러 디바이스에서 디바이스 또는 클라우드 속성에 대한 업데이트를 트리거할 수 있습니다. 또한 작업을 사용하여 여러 디바이스에서 디바이스 명령을 트리거할 수도 있습니다. Azure IoT Central에서는 워크플로를 자동화합니다.

1. 애플리케이션의 맨 왼쪽 창에서 **작업** 을 선택합니다.
1. **+새 작업** 을 선택하고 하나 이상의 작업을 구성합니다.

## <a name="customize-your-application"></a>애플리케이션 사용자 지정

빌더는 여러 설정을 변경하여 애플리케이션에서 사용자 환경을 사용자 지정할 수 있습니다.

1. **관리** > **애플리케이션 사용자 지정** 을 차례로 선택합니다.
1. **Masthead 로고** 아래에서 **변경** 을 선택하여 로고로 업로드할 이미지를 선택합니다.
1. **브라우저 아이콘** 아래에서 **변경** 을 선택하여 브라우저 탭에 나타나는 이미지를 선택합니다.
1. **브라우저 색** 아래에서 기본값을 HTML 16진수 색 코드로 바꿀 수 있습니다.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-customize-your-application1.png" alt-text="애플리케이션 사용자 지정":::

### <a name="update-the-application-image"></a>애플리케이션 이미지 업데이트

1. **관리** > **애플리케이션** 을 차례로 선택합니다.

1. **변경** 을 선택하여 애플리케이션 이미지로 업로드할 이미지를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션을 계속 사용하지 않으려면 다음 단계를 사용하여 애플리케이션을 삭제합니다.

1. 애플리케이션의 맨 왼쪽 창에서 **관리** 탭을 엽니다.
1. **애플리케이션** 을 선택하고 **삭제** 단추를 선택합니다.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-application-settings-delete-app1.png" alt-text="애플리케이션 삭제.":::
