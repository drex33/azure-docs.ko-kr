---
title: 자습서 - Azure IoT 물 소비량 모니터링 | Microsoft Docs
description: 이 자습서에서는 IoT Central용 물 소비량 모니터링 애플리케이션 템플릿을 배포하고 사용하는 방법을 보여 줍니다.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 5b27c9b26c71fba3f5acbd326ff78514212c00ef
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183777"
---
# <a name="tutorial--deploy-and-walk-through-the-water-consumption-monitoring-application"></a>자습서: 물 소비량 모니터링 애플리케이션 배포 및 살펴보기

IoT Central *물 소비량 모니터링* 애플리케이션 템플릿과 이 문서의 지침을 사용하여 엔드투엔드 물 소비량 모니터링 솔루션을 개발합니다.

![용수 사용량 모니터링 아키텍처](./media/tutorial-waterconsumptionmonitoring/concepts-waterconsumptionmonitoring-architecture1.png)

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
* Azure IoT Central API를 통해 Azure IoT Central에서 IoT 애플리케이션에 프로그래밍 방식으로 액세스합니다.

### <a name="business-applications"></a>비즈니스 애플리케이션

IoT 데이터를 사용하여 워터 유틸리티 내에서 다양한 비즈니스 애플리케이션을 지원할 수 있습니다. [IoT Central 용수 소비 모니터링 애플리케이션](tutorial-water-consumption-monitoring.md)에서 규칙 및 작업을 구성하고 [Connected Field Service](/dynamics365/field-service/connected-field-service)에서 경고를 만들도록 설정할 수 있습니다. IoT Central 규칙에서 Power Automate를 구성하여 애플리케이션 및 서비스에서 워크플로를 자동화합니다. 또한 Connected Field Service의 서비스 활동에 따라 정보를 Azure IoT Central로 다시 보낼 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]

> * Azure IoT Central 용수 사용량 모니터링 템플릿을 사용하여 용수 사용량 모니터링 애플리케이션을 만듭니다.
> * 대시보드를 탐색하고 사용자 지정합니다.
> * 디바이스 템플릿을 살펴봅니다.
> * 시뮬레이션된 디바이스를 살펴봅니다.
> * 규칙을 살펴보고 구성합니다.
> * 작업을 구성합니다.
> * 흰색 레이블 지정을 사용하여 애플리케이션 브랜딩을 사용자 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* 무료 가격 책정 요금제를 사용하거나 Azure 구독을 사용할 수 있습니다.

## <a name="create-water-consumption-monitoring-application"></a>물 소비량 모니터링 애플리케이션 만들기

다음 단계를 사용하여 애플리케이션을 만듭니다.

1. [Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음 **정부** 탭을 선택합니다. :::image type="content" source="media/tutorial-waterconsumptionmonitoring/iot-central-government-tab-overview1.png" alt-text="애플리케이션 템플릿":::

1. **물 소비량 모니터링** 에서 **앱 만들기** 를 선택합니다.

자세한 내용은 [IoT Central 애플리케이션 만들기](../core/howto-create-iot-central-application.md)를 참조하세요.

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기

다음 섹션에서는 애플리케이션의 주요 기능을 안내합니다.

### <a name="dashboard"></a>대시보드

애플리케이션이 만들어지면 **Wide World 용수 사용량 대시보드** 샘플이 열립니다.
  
:::image type="content" source="media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-dashboard-full.png" alt-text="용수 사용량 모니터링 대시보드.":::


운영자의 대시보드에서 보기를 만들고 사용자 지정할 수 있습니다.

> [!NOTE]
> 대시보드에 표시되는 모든 데이터는 시뮬레이션된 디바이스 데이터를 기반으로 하며 다음 섹션에서 살펴보겠습니다.
  
대시보드는 다음과 같은 여러 종류의 타일로 구성됩니다.

* **Wide World Water 수도 사업자 이미지 타일**: 대시보드의 첫 번째 타일은 가상의 수도 사업자인 "Wide World Water"에 대한 이미지 타일입니다. 이 타일은 사용자 고유의 이미지를 삽입하거나 제거하여 사용자 지정할 수 있습니다.
* **평균 용수 흐름 KPI 타일**: KPI 타일은 *지난 30분 동안의 평균* 예를 표시하도록 구성되어 있습니다. KPI 타일을 사용자 지정하고, 다른 유형과 시간 범위로 설정할 수 있습니다.
* **디바이스 명령 타일**: 이러한 타일로 **밸브 닫기**, **밸브 열기** 및 **밸브 위치 설정** 타일이 있습니다. 명령을 선택하면 시뮬레이션된 디바이스 명령 페이지로 이동합니다. Azure IoT Central에서 *명령* 은 *디바이스 기능* 유형입니다. 이 개념은 나중에 이 자습서의 [디바이스 템플릿](../government/tutorial-water-consumption-monitoring.md#explore-the-device-template) 섹션에서 살펴보겠습니다.

* **용수 분포 영역 맵**: 이 맵은 Azure IoT Central에서 직접 구성할 수 있는 Azure Maps를 사용합니다. 지도 타일에는 디바이스 [위치](../core/howto-use-location-data.md)가 표시됩니다. 마우스로 맵 위를 가리키면서 *확대*, *축소* 또는 *확장* 과 같은 컨트롤을 사용해 보세요.

    :::image type="content" source="media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-dashboard-map.png" alt-text="용수 사용량 모니터링 대시보드 맵":::

* **평균 용수 흐름 꺾은선형 차트** 및 **환경 상태 꺾은선형 차트**: 원하는 시간 범위에서 꺾은선형 차트로 그려지는 하나 이상의 디바이스 원격 분석을 시각화할 수 있습니다.
* **평균 밸브 압력 열 지도 차트**: 색 인덱스를 사용하여 시간 범위에 걸쳐 분산된 형태로 표시하려는 디바이스 원격 분석 데이터의 열 지도 시각화 형식을 선택할 수 있습니다.
* **경고 임계값 콘텐츠 다시 설정 타일**: 작업 호출 콘텐츠 타일 및 작업 페이지에 대한 링크를 포함할 수 있습니다. 이 경우 경고 임계값 다시 설정은 애플리케이션 **작업** 으로 이동하며, 여기서 디바이스 속성에 대한 업데이트를 실행할 수 있습니다. 이 옵션은 나중에 이 자습서의 [작업 구성](../government/tutorial-water-consumption-monitoring.md#configure-jobs) 섹션에서 살펴보겠습니다.
* **속성 타일**: 이 대시보드에는 **밸브 작동 정보**, **흐름 경고 임계값** 및 **유지 관리 정보** 타일이 표시됩니다.

### <a name="customize-the-dashboard"></a>대시보드 사용자 지정

운영자의 대시보드에서 보기를 사용자 지정할 수 있습니다.

1. **편집** 을 선택하여 **Wide World 용수 사용량 대시보드** 를 사용자 지정합니다. **편집** 메뉴를 선택하여 대시보드를 사용자 지정할 수 있습니다. 대시보드가 **편집** 모드에 있으면 새 타일을 추가하거나 구성할 수 있습니다.

    :::image type="content" source="media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-edit-dashboard.png" alt-text="대시보드 편집":::

자세히 알아보려면 [대시보드 만들기 및 사용자 지정](../core/howto-manage-dashboards.md)을 참조하세요.

## <a name="explore-the-device-template"></a>디바이스 템플릿 살펴보기

 Azure IoT Central에서 디바이스 템플릿은 디바이스 기능을 정의합니다. 디바이스 기능에는 디바이스 센서, 디바이스 속성 및 디바이스가 실행할 수 있는 명령에서 보낸 원격 분석이 포함됩니다. Azure IoT Central에서 연결할 디바이스의 기능을 나타내는 하나 이상의 디바이스 템플릿을 정의할 수 있습니다.

용수 사용량 모니터링 애플리케이션에는 *유량계* 및 *스마트 밸브* 디바이스를 나타내는 두 개의 참조 디바이스 템플릿이 제공됩니다.

디바이스 템플릿을 보려면 다음을 수행합니다.

1. Azure IoT Central에서 애플리케이션의 왼쪽 창에 있는 **디바이스 템플릿** 을 선택합니다. **디바이스 템플릿** 목록에서 **스마트 밸브** 및 **유량계** 라는 두 개의 디바이스 템플릿이 표시됩니다.

   ![디바이스 템플릿](./media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-device-template.png)

1. **유량계** 디바이스 템플릿을 선택하고 디바이스 기능을 숙지합니다.

     ![유량계 디바이스 템플릿](./media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-device-template-flow-meter.png)

### <a name="customize-the-device-template"></a>디바이스 템플릿 사용자 지정

디바이스 템플릿을 사용자 지정하려면 다음을 수행합니다.

1. **디바이스 템플릿** 메뉴에서 **사용자 지정** 으로 이동합니다.
1. `Temperature` 원격 분석 유형을 찾습니다.
1. `Temperature`의 **표시 이름** 을 `Reported temperature`로 업데이트합니다.
1. 측정 단위를 업데이트하거나 **최솟값** 및 **최댓값** 을 설정합니다.
1. **저장** 을 선택하여 변경 내용을 저장합니다.

    ![디바이스 템플릿을 사용자 지정합니다.](./media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-device-template-customize.png)

### <a name="add-a-cloud-property"></a>클라우드 속성을 추가합니다.

1. **디바이스 템플릿** 메뉴에서 **클라우드 속성** 으로 이동합니다.
1. **+ 클라우드 속성 추가** 를 선택하여 새 클라우드 속성을 추가합니다.
    Azure IoT Central에서 디바이스와 관련된 속성을 추가할 수 있습니다. 예를 들어 클라우드 속성은 설치 영역, 자산 정보 또는 기타 유지 관리 정보와 관련된 경고 임계값일 수 있습니다.
1. **저장** 을 선택하여 변경 내용을 저장합니다.

자세히 알아보려면 [클라우드 속성](../core/concepts-device-templates.md#cloud-properties)을 참조하세요.


### <a name="views"></a>보기

용수 사용량 모니터 디바이스 템플릿에는 미리 정의된 보기가 제공됩니다. 보기는 운영자가 디바이스 데이터를 보고 클라우드 속성 값을 설정하는 방법을 정의합니다.

  ![디바이스 템플릿 보기](./media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-device-template-views.png)

자세히 알아보려면 [보기](../core/concepts-device-templates.md#views)를 참조하세요.

### <a name="publish-the-device-template"></a>디바이스 템플릿 게시

디바이스 템플릿 페이지로 이동하고 **게시** 를 선택하여 디바이스 템플릿에 대한 변경 내용을 저장합니다.

자세히 알아보려면 [템플릿을 게시하는 방법](../core/howto-set-up-template.md#publish-a-device-template)을 참조하세요.

### <a name="create-a-new-device-template"></a>새 디바이스 템플릿 만들기

**+ 새로 만들기** 를 선택하여 새 디바이스 템플릿을 만들고 만들기 프로세스를 수행합니다.
사용자 지정 디바이스 템플릿을 처음부터 만들거나 Azure 디바이스 카탈로그에서 디바이스 템플릿을 선택할 수 있습니다.

자세히 알아보려면 [디바이스 템플릿을 추가하는 방법](../core/howto-set-up-template.md)을 참조하세요.

## <a name="explore-simulated-devices"></a>시뮬레이션된 디바이스 살펴보기

Azure IoT Central에서는 시뮬레이션된 디바이스를 만들어 디바이스 템플릿과 애플리케이션을 테스트할 수 있습니다. 용수 사용량 모니터링 애플리케이션에는 **유량계** 및 **스마트 밸브** 디바이스 템플릿에 매핑된 두 개의 시뮬레이션된 디바이스가 있습니다.

### <a name="view-the-devices"></a>디바이스 보기

1. 왼쪽 창에서 **디바이스 템플릿** > **모든 디바이스** 를 차례로 선택합니다.

    :::image type="content" source="media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-devices.png" alt-text="모든 디바이스 창":::

1. **스마트 밸브 1** 을 선택합니다.

    :::image type="content" source="media/tutorial-waterconsumptionmonitoring/water-consumption-monitor-device-1.png" alt-text="스마트 밸브 1":::

1. **명령** 탭에서 **스마트 밸브** 디바이스 템플릿에 정의된 기능인 세 가지 디바이스 명령(**밸브 열기**, **밸브 닫기** 및 **밸브 위치 설정**)을 볼 수 있습니다.

1. **디바이스 속성** 탭 및 **디바이스 대시보드** 탭을 살펴봅니다.

> [!NOTE]
> 이 페이지에 표시되는 보기는 **디바이스 템플릿 > 보기** 페이지를 사용하여 구성됩니다. 

### <a name="add-new-devices"></a>새 디바이스 추가

**디바이스** 탭에서 **+ 새로 만들기** 를 선택하여 새 디바이스를 추가합니다.

자세한 내용은 [디바이스 관리](../core/howto-manage-devices-individually.md)를 참조하세요.

## <a name="explore-rules"></a>규칙 살펴보기

Azure IoT Central에서는 디바이스 원격 분석을 자동으로 모니터링하고 하나 이상의 조건이 충족되면 작업을 트리거하는 규칙을 만들 수 있습니다. 이메일 알림을 보내거나 Microsoft Power Automate 작업 또는 웹후크 작업을 트리거하여 데이터를 다른 서비스에 보내는 작업이 포함될 수 있습니다.

만든 용수 사용량 모니터링 애플리케이션에는 미리 구성된 세 가지 규칙이 있습니다.

### <a name="view-rules"></a>규칙 보기

1. 왼쪽 창에서 **규칙** 을 선택합니다.

    :::image type="content" source="media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-rules.png" alt-text="규칙 창":::

1. 애플리케이션에서 미리 구성된 규칙 중 하나인 **상위 워터 흐름 경고** 를 선택합니다.

    :::image type="content" source="media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-high-flow-alert.png" alt-text="높은 pH 경고":::

    `High water flow alert` 규칙은 `Flow` 조건이 `Max flow threshold`보다 `greater than`인지 확인하도록 구성되었습니다. 흐름 임계값은 **스마트 밸브** 디바이스 템플릿에 정의된 클라우드 속성입니다. `Max flow threshold` 값은 디바이스 인스턴스마다 설정됩니다.

다음으로, 이메일 작업을 만들 수 있습니다.

규칙에 작업을 추가하려면 다음을 수행합니다.

1. **+메일** 을 선택합니다.
1. 작업의 친숙한 **표시 이름** 으로 **높은 흐름 경고** 를 입력합니다.
1. **받는 사람** 에서 Azure IoT Central 계정과 연결된 이메일 주소를 입력합니다.
1. 필요에 따라 이메일 텍스트에 포함할 메모를 입력합니다.
1. **완료** 를 선택하여 작업을 완료합니다.
1. 새 규칙을 저장하려면 **저장** 을 선택합니다.
1. 규칙을 사용하도록 설정합니다.

몇 분 내에 구성된 조건이 충족되면 이메일을 받게 됩니다.

> [!NOTE]
> 조건이 충족될 때마다 애플리케이션에서 이메일을 보냅니다. 자동화된 규칙에서 이메일 받기를 중지하는 규칙을 사용하지 않도록 설정하려면 **사용 안 함** 을 선택하세요.
  
새 규칙을 만들려면 다음을 수행합니다.

새 규칙을 만들려면 왼쪽 창에 있는 **규칙** 탭에서 **+ 새로 만들기** 를 선택합니다.

## <a name="configure-jobs"></a>작업 구성

Azure IoT Central에서 작업을 사용하면 여러 디바이스에서 디바이스 또는 클라우드 속성 업데이트를 트리거할 수 있습니다. 속성 외에도 작업을 사용하여 여러 디바이스에서 디바이스 명령을 트리거할 수 있습니다. Azure IoT Central에서는 워크플로를 자동화합니다.

1. 왼쪽 창에서 **작업** 을 선택합니다.
1. **+ 새로 만들기** 를 선택하고 하나 이상의 작업을 구성합니다.

자세히 알아보려면 [작업을 실행하는 방법](../core/howto-manage-devices-in-bulk.md)을 참조하세요.

## <a name="customize-your-application"></a>애플리케이션 사용자 지정

관리자는 여러 설정을 변경하여 애플리케이션에서 사용자 환경을 사용자 지정할 수 있습니다.

1. **관리** > **애플리케이션 사용자 지정** 을 차례로 선택합니다.
1. **애플리케이션 로고** 로 업로드할 이미지를 선택하려면 **변경** 단추를 선택합니다.
1. 브라우저 탭에 표시되는 **브라우저 아이콘** 이미지를 선택하려면 **변경** 단추를 선택합니다.
1. HTML 16진수 색 코드를 추가하여 기본 **브라우저 색** 을 바꿀 수도 있습니다. **16진수 값** 색상 표기법에 대한 자세한 내용은 [HTML 색](https://www.w3schools.com/html/html_colors.asp)을 참조하세요.

   ![애플리케이션 로고, 브라우저 아이콘 및 브라우저 색 선택](./media/tutorial-waterconsumptionmonitoring/water-consumption-monitoring-customize-your-application.png)

1. **관리** > **애플리케이션 설정** 을 차례로 선택하여 애플리케이션 이미지를 변경할 수도 있습니다. 애플리케이션 이미지로 업로드할 이미지를 선택하려면 **이미지 선택** 단추를 선택합니다.


## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 삭제합니다.

1. Azure IoT Central 애플리케이션의 왼쪽 창에서 **관리** 를 선택합니다.
1. **애플리케이션 설정** 을 선택한 다음, 페이지 아래쪽에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계
 
제안된 다음 단계는 [용수 품질 모니터링](./tutorial-water-quality-monitoring.md)에 대해 알아보는 것입니다.