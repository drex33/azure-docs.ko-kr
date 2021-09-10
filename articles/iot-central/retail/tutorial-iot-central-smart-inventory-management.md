---
title: 자습서 - Azure IoT 스마트 재고 관리 | Microsoft Docs
description: 이 자습서에서는 IoT Central용 스마트 인벤토리 관리 애플리케이션 템플릿을 배포하고 사용하는 방법을 보여 줍니다.
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 747bcaffd1e24937580dcf95f352a34f66c3314e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437511"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-inventory-management-application-template"></a>자습서: 스마트 재고 관리 애플리케이션 템플릿 배포 및 살펴보기

IoT Central *스마트 인벤토리 관리* 애플리케이션 템플릿과 이 문서의 지침을 사용하여 엔드투엔드 스마트 인벤토리 관리 솔루션을 개발합니다.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-architecture.png" alt-text="스마트 재고 관리.":::

1. 게이트웨이 디바이스로 원격 분석 데이터를 보내는 IoT 센서 세트
2. 원격 분석 및 집계된 인사이트를 IoT Central로 보내는 게이트웨이 디바이스
3. 데이터는 조작을 위해 원하는 Azure 서비스로 라우팅됩니다.
4. ASA 또는 Azure Functions 같은 Azure 서비스를 사용하여 데이터 스트림 형식을 다시 지정하고 원하는 스토리지 계정으로 보낼 수 있습니다. 
5. 처리된 데이터는 거의 실시간 작업을 위해 핫 스토리지에 저장되거나 ML 또는 일괄 처리 분석을 기반으로 하는 추가 인사이트 개선을 위해 콜드 스토리지에 저장됩니다. 
6. Logic Apps를 사용하여 최종 사용자 비즈니스 애플리케이션에서 다양한 비즈니스 워크플로를 지원할 수 있습니다.

### <a name="details"></a>세부 정보

다음 섹션에서는 RFID(전파 식별), BLE(Bluetooth 저에너지) 태그로부터 수행되는 개념적 아키텍처 원격 분석 수집의 각 부분에 대해 간략하게 설명합니다.

### <a name="rfid-tags"></a>RFID 태그

RFID 태그는 전파를 통해 항목에 대한 데이터를 전송합니다. RFID 태그는 다르게 지정하지 않는 한, 배터리를 포함하지 않습니다. 태그는 판독기가 생성한 전파에서 에너지를 받은 후 RFID 판독기로 신호를 다시 전송합니다.

### <a name="ble-tags"></a>BLE 태그

에너지 오류 신호는 일정한 간격으로 데이터의 패킷을 브로드캐스트합니다. 스마트폰에서 BLE 판독기 또는 설치된 서비스를 통해 감지된 오류 신호 데이터는 클라우드로 전송됩니다.

### <a name="rfid--ble-readers"></a>RFID 및 BLE 판독기

RFID 판독기는 전파를 좀 더 유용한 데이터 형태로 변환합니다. 그런 다음, 태그에서 수집된 정보는 로컬 에지 서버에 저장되거나 MQTT의 JSON-RPC 2.0을 사용하여 클라우드로 전송됩니다.
AP(액세스 지점)라고도 하는 BLE 판독기는 RFID 판독기와 유사합니다. 이러한 판독기는 주변의 Bluetooth 신호를 감지하고, 해당 메시지를 MQTT의 JSON-RPC 2.0을 사용하여 로컬 Azure IoT Edge 또는 클라우드로 릴레이하는 데 사용됩니다.
대부분의 판독기는 온도, 습도, 가속도계 및 자이로스코프와 관련된 추가 센서 기능을 제공할 뿐만 아니라 RFID 및 탐지 신호를 읽을 수 있습니다.

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge 게이트웨이

Azure IoT Edge 서버는 해당 데이터를 클라우드로 보내기 전에 로컬에서 전처리하는 장소를 제공합니다. 표준 컨테이너를 통해 클라우드 워크로드 AI, Azure 및 타사 서비스, 비즈니스 논리를 배포할 수도 있습니다.

### <a name="device-management-with-iot-central"></a>IoT Central을 사용하는 디바이스 관리 

Azure IoT Central은 IoT 디바이스 연결, 구성 및 관리를 간소화하는 솔루션 개발 플랫폼입니다. 이 플랫폼은 IoT 디바이스 관리, 운영 및 관련 개발의 부담 및 비용을 크게 줄여줍니다. 고객 및 파트너는 엔드투엔드 엔터프라이즈 솔루션을 빌드하여 재고 관리에서 디지털 피드백 루프를 달성할 수 있습니다.

### <a name="business-insights--actions-using-data-egress"></a>데이터 송신을 사용한 비즈니스 인사이트 및 작업 

IoT Central 플랫폼은 CDE(연속 데이터 내보내기) 및 API를 통해 다양한 확장성 옵션을 제공합니다. 원격 분석 데이터 처리 또는 원시 원격 분석을 기준으로 하는 비즈니스 인사이트는 일반적으로 기본 설정된 LOB(기간 업무) 애플리케이션으로 내보내집니다. 이러한 작업은 기계 학습 모델 및 추가적으로 강화된 인사이트를 빌드, 학습 및 배포하기 위해 웹후크, Service Bus, 이벤트 허브 또는 Blob 스토리지를 사용하여 수행할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 스마트 재고 관리 애플리케이션 만들기 
> * 애플리케이션 살펴보기 

## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* Azure 구독이 있는 것이 좋지만, 없더라도 시도해 볼 수 있습니다.

## <a name="create-smart-inventory-management-application"></a>스마트 재고 관리 애플리케이션 만들기

다음 단계를 사용하여 애플리케이션을 만듭니다.

1. [Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음 **소매** 탭을 선택합니다. :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc-retail-home-page.png" alt-text="스마트 인벤토리 관리 애플리케이션 템플릿에서 앱을 만드는 방법을 보여 주는 스크린샷":::

1. **스마트 인벤토리 관리** 에서 **앱 만들기** 를 선택합니다.

자세한 내용은 [IoT Central 애플리케이션 만들기](../core/howto-create-iot-central-application.md)를 참조하세요.

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기

다음 섹션에서는 애플리케이션의 주요 기능을 안내합니다.

### <a name="dashboard"></a>대시보드 

앱 템플릿을 성공적으로 배포하면 기본 대시보드는 스마트 재고 관리 운영자 중심 포털이 됩니다. Northwind Trader는 BLE(블루투스 저에너지) 및 RFID(전파 식별)를 사용하여 도매점을 관리하는 가상의 스마트 재고 공급자입니다. 이 대시보드에서는 관련된 명령, 직무 및 수행할 수 있는 작업과 함께 재고에 대한 원격 분석을 제공하는 두 개의 다른 게이트웨이를 볼 수 있습니다. 이 대시보드는 중요한 스마트 재고 관리 디바이스 작업 활동을 보여 주기 위해 미리 구성되어 있습니다.
대시보드는 서로 다른 두 게이트웨이 디바이스 관리 작업 사이에서 논리적으로 분할됩니다. 
   * 도매점에서는 더 큰 시설에서 재고를 추적하기 위해 화물 운반대에 고정된 BLE 게이트웨이 및 BLE 태그를 배치합니다.
   * 소매점에서는 추적할 항목 수준에서 개별적으로 고정 RFID 게이트웨이 및 RFID 태그를 배치하여 소매 아울렛의 재고를 추적합니다.
  * 게이트웨이 [위치](../core/howto-use-location-data.md), 상태 및 관련 세부 정보 보기 

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-1.png" alt-text="스마트 재고 관리 대시보드의 상단 절반을 보여주는 스크린샷.":::

    * 게이트웨이, 활성 및 알 수 없는 태그의 총 수를 쉽게 추적할 수 있습니다.
    * 펌웨어 업데이트, 센서 사용 안 함, 센서 임계값 업데이트, 원격 분석 간격 업데이트, 디바이스 서비스 계약 업데이트 등의 디바이스 관리 작업을 수행할 수 있습니다.
    * 게이트웨이 디바이스는 전체 또는 증분 검색을 통해 주문형 인벤토리 관리를 수행할 수 있습니다.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-2.png" alt-text="스마트 재고 관리 대시보드의 하단 절반을 보여주는 스크린샷.":::

### <a name="device-template"></a>디바이스 템플릿

디바이스 템플릿 탭을 클릭하면 게이트웨이 기능 모델이 표시됩니다. 기능 모델은 두 개의 다른 인터페이스인 **게이트웨이 원격 분석 및 속성** 및 **게이트웨이 명령** 을 통해 구성됩니다.

**게이트웨이 원격 분석 및 속성** - 이 인터페이스는 게이트웨이 임계값 및 업데이트 간격과 같은 센서, 위치, 디바이스 정보 및 디바이스 쌍 속성 기능과 관련된 모든 원격 분석을 나타냅니다.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-1.png" alt-text="애플리케이션의 인벤토리 게이트웨이 디바이스 템플릿을 보여주는 스크린샷.":::

**게이트웨이 명령** - 이 인터페이스는 모든 게이트웨이 명령 기능을 구성합니다.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-2.png" alt-text="인벤토리 게이트웨이 디바이스 템플릿의 게이트웨이 명령 인터페이스를 보여주는 스크린샷.":::

### <a name="rules"></a>규칙

규칙 탭을 선택하여 이 애플리케이션 템플릿에 존재하는 두 가지 다른 규칙을 표시합니다. 이러한 규칙은 추가 조사를 위해 운영자에게 메일 알림을 보내도록 구성됩니다.

**게이트웨이 오프라인**: 이 규칙은 게이트웨이가 장기간 클라우드에 보고하지 않는 경우에 트리거됩니다. 배터리 부족 모드, 연결 손실, 디바이스 상태 때문에 게이트웨이가 응답하지 않을 수 있습니다.

**알 수 없는 태그**: 자산과 연결된 모든 RFID 및 BLE 태그를 추적하는 것이 중요합니다. 게이트웨이에서 알 수 없는 태그를 너무 많이 감지하는 경우 태그 소싱 애플리케이션에 동기화 문제가 있는 것을 나타냅니다.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-rules.png" alt-text="스마트 재고 관리 애플리케이션의 규칙 목록을 보여주는 스크린샷.":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 **관리** > **애플리케이션 설정** 을 방문하여 애플리케이션 템플릿을 삭제하고 **삭제** 를 클릭합니다.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-cleanup.png" alt-text="작업을 완료했을 때 애플리케이션을 삭제하는 방법을 보여주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

스마트 재고 관리에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [스마트 재고 관리 개념](./architecture-smart-inventory-management.md)
