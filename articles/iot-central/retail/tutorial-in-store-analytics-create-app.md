---
title: 자습서 - Azure IoT 매장 내 분석 | Microsoft Docs
description: 이 자습서에서는 IoT Central에서 매장 내 분석 소매점 애플리케이션을 배포, 사용 및 만드는 방법을 보여 줍니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 08/17/2021
ms.openlocfilehash: 93438e9726dca4fe74da3bbfc9bd06533115e74e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439153"
---
# <a name="tutorial-deploy-and-walk-through-the-in-store-analytics-application-template"></a>자습서: 매장 내 분석 애플리케이션 템플릿 배포 및 살펴보기

IoT Central *매장 내 분석* 애플리케이션 템플릿과 이 문서의 지침을 사용하여 엔드투엔드 매장 내 분석 솔루션을 개발합니다.

:::image type="content" source="media/tutorial-in-store-analytics-create-app/store-analytics-architecture-frame.png" alt-text="Azure IoT Central 매장 분석.":::

- 게이트웨이 디바이스로 원격 분석 데이터를 보내는 IoT 센서 세트
- 원격 분석 및 집계된 인사이트를 IoT Central로 보내는 게이트웨이 디바이스
- 조작을 위해 원하는 Azure 서비스로 연속 데이터 내보내기
- 데이터를 원하는 형식으로 구조화하여 스토리지 서비스로 보낼 수 있습니다.
- 비즈니스 애플리케이션은 데이터를 쿼리하고 소매점 업무를 지원하는 인사이트를 생성할 수 있습니다.

매장 내 분석 솔루션에 일반적으로 관여하는 주요 구성 요소를 살펴보겠습니다.

## <a name="condition-monitoring-sensors"></a>상태 모니터링 센서

IoT 솔루션은 소매점 환경 내에서 의미 있는 신호를 캡처하는 센서 세트로 시작합니다. 위의 아키텍처 다이어그램의 왼쪽 끝에서 다양한 종류의 센서를 확인할 수 있습니다.

## <a name="gateway-devices"></a>게이트웨이 디바이스

많은 IoT 센서가 원시 신호를 클라우드 또는 근처에 있는 게이트웨이 디바이스에 직접 제공할 수 있습니다. 게이트웨이 디바이스는 IoT Central 애플리케이션에 요약 인사이트를 보내기 전에 에지에서 데이터 집계를 수행합니다. 또한 게이트웨이 디바이스는 해당되는 경우 센서 디바이스에 명령 및 제어 작업을 릴레이하는 역할을 담당합니다. 

## <a name="iot-central-application"></a>IoT Central 애플리케이션

Azure IoT Central 애플리케이션은 소매점 환경 내에서 다양한 종류의 IoT 센서와 게이트웨이 디바이스에서 데이터를 수집하고 의미 있는 인사이트 세트를 생성합니다.

또한 Azure IoT Central은 매장 운영자에게 맞춤형 환경을 제공하여 인프라 디바이스를 원격으로 모니터링하고 관리할 수 있도록 합니다.

## <a name="data-transform"></a>데이터 변환

솔루션 내의 Azure IoT Central 애플리케이션을 원시 또는 집계된 인사이트를 Azure PaaS(Platform as a Service) 서비스 세트로 내보내도록 구성하여 데이터 조작을 수행하고 풍부한 인사이트를 확보한 후 비즈니스 애플리케이션에 제공하도록 할 수 있습니다. 

## <a name="business-application"></a>비즈니스 애플리케이션

IoT 데이터는 소매 환경 내 다양한 종류의 비즈니스 애플리케이션을 지원하는 데 사용될 수 있습니다. 소매점 관리자나 직원 구성원은 이러한 애플리케이션을 사용하여 비즈니스 인사이트를 시각화하고 실시간으로 의미 있는 작업을 수행할 수 있습니다. 소매 팀을 위한 실시간 Power BI 대시보드를 빌드하는 방법에 대한 자세한 내용은 [자습서](./tutorial-in-store-analytics-create-app.md)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> - Azure IoT Central **매장 내 분석 - 체크 아웃** 템플릿을 사용하여 소매점 애플리케이션 만들기
> - 애플리케이션 설정 사용자 지정
> - IoT 디바이스 템플릿 만들기 및 사용자 지정
> - 디바이스를 애플리케이션에 연결
> - 조건을 모니터링하기 위한 규칙 및 작업 추가

## <a name="prerequisites"></a>사전 요구 사항

- 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
- 무료 가격 책정 요금제를 사용하거나 Azure 구독을 사용할 수 있습니다.

## <a name="create-in-store-analytics-application"></a>매장 내 분석 애플리케이션 만들기

다음 단계를 사용하여 애플리케이션을 만듭니다.

1. [Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음, **소매** 탭을 선택합니다. :::image type="content" source="media/tutorial-in-store-analytics-create-app/iotc-retail-homepage.png" alt-text="연결된 물류 템플릿":::

1. **매장 내 분석 - 체크 아웃** 에서 **앱 만들기** 를 선택합니다.

자세한 내용은 [IoT Central 애플리케이션 만들기](../core/howto-create-iot-central-application.md)를 참조하세요.

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기

다음 섹션에서는 애플리케이션의 주요 기능을 안내합니다.

### <a name="customize-application-settings"></a>애플리케이션 설정 사용자 지정

여러 설정을 변경하여 애플리케이션에서 사용자 환경을 사용자 지정할 수 있습니다. 이 섹션에서는 미리 정의된 애플리케이션 테마를 선택합니다. 필요에 따라 사용자 지정 테마를 만들고 애플리케이션 이미지를 업데이트하는 방법을 배웁니다. 사용자 지정 테마를 사용하면 애플리케이션 브라우저 색, 브라우저 아이콘 및 발행인란에 표시되는 애플리케이션 로고를 설정할 수 있습니다.

미리 정의된 애플리케이션 테마를 선택하려면 다음을 수행합니다.

1. 발행인란에서 **설정** 을 선택합니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/settings-icon.png" alt-text="Azure IoT Central 애플리케이션 설정.":::

2. 새 **테마** 를 선택합니다.

3. **저장** 을 선택합니다.

미리 정의된 테마를 사용하는 대신, 사용자 지정 테마를 만들 수 있습니다. 샘플 이미지 세트를 사용하여 애플리케이션을 사용자 지정하고 자습서를 완료하려면 [Contoso 샘플 이미지](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)를 다운로드합니다.

사용자 지정 테마를 만들려면 다음을 수행합니다.

1. 왼쪽 창을 아직 확장하지 않은 경우 확장합니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/dashboard-expand.png" alt-text="Azure IoT Central 왼쪽 창.":::

1. **관리 > 애플리케이션 사용자 지정** 을 선택합니다.

1. **변경** 단추를 사용하여 **애플리케이션 로고** 로 업로드할 이미지를 선택합니다. 필요에 따라 **로고 대체 텍스트** 값을 지정합니다. 

1. **변경** 단추를 사용하여 브라우저 탭에 표시되는 **브라우저 아이콘** 이미지를 선택합니다.

1. 필요에 따라 HTML 16진수 색 코드를 추가하여 기본 **브라우저 색** 을 바꿉니다. **헤더** 에 *#008575* 를 추가합니다.  **악센트** 에 *#A1F3EA* 를 추가합니다. 

1. **저장** 을 선택합니다. 

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/select-application-logo.png" alt-text="Azure IoT Central 사용자 지정 로고.":::

    저장하면 애플리케이션은 브라우저 색, 발행인란의 로고 및 브라우저 아이콘을 업데이트합니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/saved-application-settings.png" alt-text="Azure IoT Central의 업데이트된 애플리케이션 설정.":::

애플리케이션 이미지를 업데이트하려면 다음을 수행합니다.

1. **관리 > 애플리케이션 설정** 을 선택합니다.

1. **이미지 선택** 단추를 사용하여 애플리케이션 이미지로 업로드할 이미지를 선택합니다. 이 이미지는 IoT Central 애플리케이션 관리자의 **내 앱** 페이지에 있는 애플리케이션 타일에 표시됩니다.

1. **저장** 을 선택합니다.

1. 필요에 따라 [Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 **내 앱** 보기로 이동합니다. 애플리케이션 타일에는 업데이트된 애플리케이션 이미지가 표시됩니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/customize-application-image.png" alt-text="Azure IoT Central 애플리케이션 이미지 사용자 지정.":::

### <a name="create-device-templates"></a>디바이스 템플릿 만들기

사용자 및 애플리케이션 운영자가 디바이스를 구성 및 관리할 수 있도록 하는 디바이스 템플릿을 만들 수 있습니다. 사용자 지정 템플릿을 빌드하거나, 기존 템플릿 파일을 가져오거나, Azure IoT 디바이스 카탈로그에서 템플릿을 가져와 템플릿을 만들 수 있습니다. 디바이스 템플릿을 만들고 사용자 지정한 후에는 이를 사용하여 실제 디바이스를 애플리케이션에 연결합니다. 필요에 따라 디바이스 템플릿을 사용하여 테스트할 시뮬레이트된 디바이스를 생성합니다.

**매장 내 분석 - 체크 아웃** 애플리케이션 템플릿에는 여러 디바이스용 디바이스 템플릿이 있습니다.  애플리케이션에서 사용하는 세 가지 디바이스 중 두 개 디바이스용 디바이스 템플릿이 있습니다. RuuviTag 디바이스 템플릿은 **매장 내 분석 - 체크 아웃** 애플리케이션 템플릿에 포함되어 있지 않습니다. 이 섹션에서는 애플리케이션에 RuuviTag 센서용 디바이스 템플릿을 추가합니다.

애플리케이션에 RuuviTag 디바이스 템플릿을 추가하려면 다음을 수행합니다.

1. 왼쪽 창에서 **디바이스 템플릿** 을 선택합니다.

1. 새 디바이스 템플릿을 만들려면 **+새로 만들기** 를 선택합니다.

1. Azure IoT 디바이스 카탈로그에서 **RuuviTag Multisensor** 디바이스 템플릿을 찾아 선택합니다. 

1. 완료되면 **다음: 사용자 지정** 을 선택합니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png" alt-text="다음: 사용자 지정 단추를 강조 표시하는 스크린샷.":::

1. **만들기** 를 선택합니다. 애플리케이션은 RuuviTag 디바이스 템플릿을 추가합니다.

1. 왼쪽 창에서 **디바이스 템플릿** 을 선택합니다. 이 페이지에는 애플리케이션 템플릿에 포함된 모든 디바이스 템플릿과 방금 추가한 RuuviTag 디바이스 템플릿이 표시됩니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/device-templates-list.png" alt-text="Azure IoT Central RuuviTag 센서 디바이스 템플릿.":::

### <a name="customize-device-templates"></a>디바이스 템플릿 사용자 지정

세 가지 방법으로 애플리케이션에서 디바이스 템플릿을 사용자 지정할 수 있습니다. 먼저 디바이스 기능을 변경하여 디바이스에서 기본 제공 인터페이스를 사용자 지정합니다. 예를 들어, 온도 센서를 사용하여 온도 인터페이스의 표시 이름, 데이터 형식, 측정 단위, 최소 및 최대 작동 범위 등의 세부 정보를 변경할 수 있습니다. 

둘째, 클라우드 속성을 추가하여 디바이스 템플릿을 사용자 지정합니다. 클라우드 속성은 기본 제공 디바이스 기능에 속하지 않습니다. 클라우드 속성은 Azure IoT Central 애플리케이션에서 만들고 저장하고 디바이스에 연결하는 사용자 지정 데이터입니다. 클라우드 속성의 예로는 계산된 값이나 디바이스 세트에 연결하려는 위치와 같은 메타데이터가 있습니다.

셋째, 사용자 지정 보기를 빌드하여 디바이스 템플릿을 사용자 지정합니다. 보기에서는 운영자가 디바이스 메트릭 및 상태와 같은 디바이스에 대한 원격 분석 및 메타데이터를 시각화할 수 있습니다.

여기서는 처음 두 가지 방법을 사용하여 RuuviTag 센서용 디바이스 템플릿을 사용자 지정합니다.

RuuviTag 디바이스 템플릿의 기본 제공 인터페이스를 사용자 지정하려면 다음을 수행합니다.

1. 왼쪽 창에서 **디바이스 템플릿** 을 선택합니다. 

1. RuuviTag 센서용 템플릿을 선택합니다. 

1. 왼쪽 창을 숨깁니다. 템플릿의 요약 보기에는 디바이스 기능이 표시됩니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png" alt-text="Azure IoT Central RuuviTag 디바이스 템플릿 요약 보기.":::

1. RuuviTag 디바이스 템플릿 메뉴에서 **사용자 지정** 을 선택합니다. 

1. 기능 목록에서 스크롤하여 `humidity` 원격 분석 유형을 찾습니다. 이 항목은 *습도* 의 편집 가능한 **표시 이름** 값을 포함하는 행입니다.

다음 단계에서는 RuuviTag 센서의 `humidity` 원격 분석 유형을 사용자 지정합니다. 필요에 따라 일부 다른 원격 분석 유형도 사용자 지정합니다.

`humidity` 원격 분석 유형의 경우 다음과 같이 변경합니다.

1. **확장** 컨트롤을 선택하여 행의 스키마 세부 정보를 확장합니다.

1. *습도* 의 **표시 이름** 값을 *상대 습도* 와 같은 사용자 지정 값으로 업데이트합니다.

1. **의미 체계 유형** 옵션을 *없음* 에서 *습도* 로 변경합니다.  필요에 따라 확장된 스키마 보기에서 습도 원격 분석 유형의 스키마 값을 설정합니다. 스키마 설정을 사용하여 센서가 추적하는 데이터에 대한 자세한 유효성 검사 요구 사항을 지정할 수 있습니다. 예를 들어, 지정된 인터페이스의 최소 및 최대 작동 범위 값을 설정할 수 있습니다.

1. **저장** 을 선택하여 변경 내용을 저장합니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png" alt-text="사용자 지정 화면을 보여주고 저장 단추를 강조 표시하는 스크린샷.":::

애플리케이션의 디바이스 템플릿에 클라우드 속성을 추가하려면 다음을 수행합니다.

1. RuuviTag 디바이스 템플릿 메뉴에서 **클라우드 속성** 을 선택합니다.

1. **클라우드 속성 추가** 를 선택합니다. 

다음 값을 지정하여 각 디바이스의 위치를 저장하는 사용자 지정 속성을 만듭니다.

1. **표시 이름** 의 *위치* 값을 입력합니다. 이 값은 속성의 친숙한 이름을 나타내는 **이름** 필드에 자동으로 복사됩니다. 복사한 값을 사용하거나 변경할 수 있습니다.

1. **스키마** 드롭다운에서 *문자열* 을 선택합니다. 문자열 형식을 사용하면 위치 이름 문자열을 템플릿에 따라 디바이스와 연결할 수 있습니다. 예를 들어, 매장의 영역을 각 디바이스와 연결할 수 있습니다.

1. **최소 길이** 를 *2* 로 설정합니다. 

1. **트리밍 공백** 을 **켜짐** 으로 설정합니다.

1. **저장** 을 선택하여 사용자 지정 클라우드 속성을 저장합니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png" alt-text="Azure IoT Central RuuviTag 디바이스 템플릿 사용자 지정.":::

1. **게시** 를 선택합니다. 

    디바이스 템플릿을 게시하면 애플리케이션 운영자에게 표시됩니다. 템플릿을 게시한 후에는 이 템플릿을 사용하여 테스트를 위한 시뮬레이트된 디바이스를 생성하거나 애플리케이션에 실제 디바이스를 연결합니다. 애플리케이션에 연결된 디바이스가 이미 있는 경우 사용자 지정된 템플릿을 게시하면 변경 내용이 디바이스에 푸시됩니다.

### <a name="add-devices"></a>디바이스 추가

디바이스 템플릿을 만들고 사용자 지정한 후에는 디바이스를 추가할 수 있습니다. 

이 자습서에서는 다음과 같은 실제 및 시뮬레이트된 디바이스 세트를 사용하여 애플리케이션을 빌드합니다.

- 실제 Rigado C500 게이트웨이
- 두 개의 실제 RuuviTag 센서
- 시뮬레이트된 **점유** 센서입니다. 시뮬레이트된 센서는 애플리케이션 템플릿에 포함되므로 만들 필요가 없습니다. 

> [!NOTE]
> 실제 디바이스가 없는 경우 시뮬레이션된 RuuviTag 센서를 만들어 이 자습서를 완료할 수 있습니다. 다음 지침에는 시뮬레이트된 RuuviTag를 만드는 단계가 포함되어 있습니다. 시뮬레이션된 게이트웨이를 만들 필요가 없습니다.

다음 두 문서의 단계를 완료하여 실제 Rigado 게이트웨이와 RuuviTag 센서를 연결합니다. 완료되면 이 자습서로 돌아갑니다. 이 자습서에서 디바이스 템플릿을 이미 만들었기 때문에 다음 지침에 따라 다시 만들 필요가 없습니다.

- Rigado 게이트웨이를 연결하려면 [Azure IoT Central 애플리케이션에 Rigado Cascade 500 연결](../core/howto-connect-rigado-cascade-500.md)을 참조하세요.
- RuuviTag 센서를 연결하려면 [Azure IoT Central 애플리케이션에 RuuviTag 센서 연결](../core/howto-connect-ruuvi.md)을 참조하세요. 필요한 경우 이러한 지침을 사용하여 두 개의 시뮬레이트된 센서를 만들 수도 있습니다.

### <a name="add-rules-and-actions"></a>규칙 및 작업 추가

Azure IoT Central 애플리케이션에서 센서를 사용하여 조건을 모니터링하는 과정의 일부로, 특정 조건이 충족될 때 작업을 실행하는 규칙을 만들 수 있습니다. 규칙은 디바이스 템플릿과 하나 이상의 디바이스에 연결되며 디바이스 원격 분석 또는 이벤트에 따라 충족되어야 하는 조건을 포함합니다. 규칙에는 하나 이상의 관련된 작업도 있습니다. 작업에는 메일 알림 전송 또는 다른 서비스로 데이터를 보내는 웹후크 작업 트리거 등이 포함될 수 있습니다. **매장 내 분석 - 체크 아웃** 애플리케이션 템플릿에는 애플리케이션의 디바이스에 대한 몇 가지 미리 정의된 규칙이 포함되어 있습니다.

이 섹션에서는 RuuviTag 센서 원격 분석을 기준으로 최대 상대 습도 수준을 확인하는 새 규칙을 만듭니다. 습도가 최댓값을 초과하는 경우 애플리케이션에서 이메일을 보내도록 규칙에 작업을 추가합니다. 

규칙을 만들려면 다음을 수행합니다. 

1. 왼쪽 창을 확장합니다.

1. **규칙** 을 선택합니다.

1. **+새로 만들기** 를 선택합니다.

1. 규칙 이름으로 *Humidity level* 을 입력합니다. 

1. **범위** 에서 RuuviTag 디바이스 템플릿을 선택합니다. 정의한 규칙은 해당 템플릿을 기준으로 하는 모든 센서에 적용됩니다. 필요에 따라 센서의 정의된 하위 세트에만 규칙을 적용하는 필터를 만들 수 있습니다. 

1. **원격 분석** 으로 `Relative humidity`를 선택합니다. 이전 단계에서 사용자 지정한 디바이스 기능입니다.

1. **연산자** 로 `Is greater than`을 선택합니다. 

1. 환경에 대한 일반적인 상한 실내 습도 수준을 **값** 으로 입력합니다. 예를 들어 *65* 를 입력합니다. RuuviTag 실제 또는 시뮬레이트된 센서의 상대 습도가 이 값을 초과하는 경우 발생하는 규칙에 대한 조건을 설정했습니다. 환경의 정상적인 습도 범위에 따라 값을 늘리거나 줄여야 할 수 있습니다.  

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-conditions.png" alt-text="Azure IoT Central 규칙 조건 추가.":::

규칙에 작업을 추가하려면 다음을 수행합니다.

1. **+메일** 을 선택합니다.

1. 작업의 친숙한 **표시 이름** 으로 *High humidity notification* 을 입력합니다. 

1. **받는 사람** 에 계정과 연결된 메일 주소를 입력합니다. 다른 메일을 사용하는 경우 애플리케이션에 추가된 사용자의 주소를 사용해야 합니다. 또한 사용자는 적어도 한 번 이상 로그인했다가 로그아웃해야 합니다.

1. 필요에 따라 메일 텍스트에 포함할 메모를 입력합니다.

1. **완료** 를 선택하여 작업을 완료합니다.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-action.png" alt-text="Azure IoT Central 규칙에 작업 추가.":::

1. **저장** 을 선택하여 새 규칙을 저장하고 활성화합니다. 

    몇 분 이내에 지정된 메일 계정에서 메일을 받기 시작합니다. 애플리케이션은 센서가 습도 수준이 조건 값을 초과했음을 알 때마다 메일을 보냅니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

* Azure IoT Central **매장 내 분석 - 체크 아웃** 템플릿을 사용하여 소매점 애플리케이션 만들기
* 애플리케이션 설정 사용자 지정
* IoT 디바이스 템플릿 만들기 및 사용자 지정
* 디바이스를 애플리케이션에 연결
* 조건을 모니터링하기 위한 규칙 및 작업 추가

Azure IoT Central 조건 모니터링 애플리케이션을 만들었으므로 제안되는 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [대시보드 사용자 지정](./tutorial-in-store-analytics-customize-dashboard.md)
