---
title: 자습서 - Azure IoT 디지털 배포 센터 | Microsoft Docs
description: 이 자습서에서는 IoT Central용 디지털 유통 센터 애플리케이션 템플릿을 배포하고 사용하는 방법을 보여 줍니다.
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 70ea24b64a1173d2a397d9603d020506f53be37e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434667"
---
# <a name="tutorial-deploy-and-walk-through-the-digital-distribution-center-application-template"></a>자습서: 디지털 배포 센터 애플리케이션 템플릿 배포 및 살펴보기

IoT Central *디지털 유통 센터* 애플리케이션 템플릿과 이 문서의 지침을 사용하여 엔드투엔드 디지털 유통 센터 솔루션을 개발합니다.

   :::image type="content" source="media/tutorial-iot-central-ddc/digital-distribution-center-architecture.png" alt-text="디지털 배포 센터.":::

1. 게이트웨이 디바이스로 원격 분석 데이터를 보내는 IoT 센서 세트
2. 원격 분석 및 집계된 인사이트를 IoT Central로 보내는 게이트웨이 디바이스
3. 데이터는 조작을 위해 원하는 Azure 서비스로 라우팅됩니다.
4. ASA 또는 Azure Functions 같은 Azure 서비스를 사용하여 데이터 스트림 형식을 다시 지정하고 원하는 스토리지 계정으로 보낼 수 있습니다.
5. 처리된 데이터는 근 실시간 작업을 위해 핫 스토리지에 저장되거나 ML 또는 일괄 처리 분석을 기반으로 하는 추가 인사이트 개선을 위해 콜드 스토리지에 저장됩니다. 
6. Logic Apps를 사용하여 최종 사용자 비즈니스 애플리케이션에서 다양한 비즈니스 워크플로를 지원할 수 있습니다.

### <a name="video-cameras"></a>비디오 카메라 

비디오 카메라는 이러한 디지털 연결 엔터프라이즈 규모 에코시스템의 기본 센서입니다. 비디오를 구조화된 데이터로 전환하고, 클라우드로 보내기 전에 에지에서 처리할 수 있도록 기계 학습 및 인공 지능이 개선되었습니다. Microsoft는 IP 카메라를 사용하여 이미지를 캡처하고, 카메라에서 압축하고, 비디오 분석 파이프라인의 에지 컴퓨팅을 통해 압축된 데이터를 전송하거나, GigE 비전 카메라를 사용하여 센서에서 이미지를 캡처한 후 이러한 이미지를 Azure IoT Edge에 직접 보낼 수 있습니다. 여기서 압축된 후 비디오 분석 파이프라인에서 처리됩니다. 

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge 게이트웨이

"cameras-as-sensors" 및 에지 워크로드는 Azure IoT Edge에서 로컬로 관리되며 카메라 스트림은 분석 파이프라인에서 처리됩니다. Azure IoT Edge의 비디오 분석 처리 파이프라인은 응답 시간 단축, 낮은 대역폭 사용량을 비롯한 많은 이점을 누림으로써 대기 시간을 단축하여 데이터를 신속하게 처리할 수 있도록 합니다. 추가 작업 또는 조사를 위해 가장 중요한 메타데이터, 인사이트 또는 작업만 클라우드에 전송됩니다. 

### <a name="device-management-with-iot-central"></a>IoT Central을 사용하는 디바이스 관리
 
Azure IoT Central은 IoT 디바이스와 Azure IoT Edge 게이트웨이 연결, 구성 및 관리를 간소화하는 솔루션 개발 플랫폼입니다. 이 플랫폼은 IoT 디바이스 관리, 운영 및 관련 개발의 부담 및 비용을 크게 줄여줍니다. 고객 및 파트너는 엔드투엔드 엔터프라이즈 솔루션을 빌드하여 배포 센터에서 디지털 피드백 루프를 달성할 수 있습니다.

### <a name="business-insights-and-actions-using-data-egress"></a>데이터 송신을 사용한 비즈니스 Insights 및 작업 

IoT Central 플랫폼은 CDE(연속 데이터 내보내기) 및 API를 통해 다양한 확장성 옵션을 제공합니다. 원격 분석 데이터 처리 또는 원시 원격 분석을 기준으로 하는 비즈니스 인사이트는 일반적으로 기본 설정된 기간 업무 애플리케이션으로 내보내집니다. 이러한 작업은 기계 학습 모델 및 추가적으로 강화된 인사이트를 빌드, 학습 및 배포하기 위해 웹후크, Service Bus, 이벤트 허브 또는 Blob 스토리지를 통해 수행할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 디지털 배포 센터 애플리케이션 만들기.
> * 애플리케이션 살펴보기.

## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* Azure 구독이 있는 것이 좋지만, 없더라도 시도해 볼 수 있습니다.

## <a name="create-digital-distribution-center-application-template"></a>디지털 배포 센터 애플리케이션 만들기 탬플릿

다음 단계를 사용하여 애플리케이션을 만듭니다.

1. [Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음, **소매** 탭을 선택합니다.

   :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-home-page.png" alt-text="앱을 만드는 방법을 보여 주는 스크린샷.":::

1. **디지털 유통 센터** 에서 **앱 만들기** 를 선택합니다.

자세한 내용은 [IoT Central 애플리케이션 만들기](../core/howto-create-iot-central-application.md)를 참조하세요.

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기 

다음 섹션에서는 애플리케이션의 주요 기능을 안내합니다.

### <a name="dashboard"></a>대시보드

기본 대시보드는 유통 센터 운영자 중심 포털입니다. Northwind Trader는 컨베이어 시스템을 관리하는 가상의 배포 센터 솔루션 공급자입니다. 

이 대시보드에는 IoT 디바이스 역할을 하는 하나의 게이트웨이와 하나의 카메라가 표시됩니다. 게이트웨이는 연결된 디바이스 쌍 속성과 함께 유효함, 유효하지 않음, 미확인, 크기 등의 패키지에 대한 원격 분석을 제공합니다. 모든 다운스트림 명령은 IoT 디바이스(예: 카메라)에서 실행됩니다. 이 대시보드는 중요한 배포 센터 디바이스 작업 활동을 보여 주기 위해 미리 구성되어 있습니다.

대시보드는 Azure IoT 게이트웨이 및 IoT 디바이스의 디바이스 관리 기능을 표시하도록 논리적으로 구성되어 있습니다.  

* 게이트웨이 명령 및 제어 작업을 수행할 수 있습니다.
* 솔루션에 포함된 모든 카메라를 관리합니다.
* 솔루션에 포함된 모든 카메라를 관리합니다.
* 솔루션에 포함된 모든 카메라를 관리합니다.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-dashboard.png" alt-text="디지털 유통 센터 대시보드를 보여주는 스크린샷.":::

### <a name="device-template"></a>디바이스 템플릿

디바이스 템플릿 탭을 클릭하면 게이트웨이 기능 모델이 표시됩니다. 기능 모델은 **카메라** 및 **디지털 배포 게이트웨이** 의 두 가지 다른 인터페이스로 구성됩니다.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="애플리케이션의 디지털 유통 게이트웨이 디바이스 템플릿을 보여주는 스크린샷.":::

**카메라** - 이 인터페이스는 모든 카메라 관련 명령 기능을 구성합니다. 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="디지털 유통 게이트웨이 디바이스 템플릿의 카메라 인터페이스를 보여주는 스크린샷.":::

**디지털 배포 게이트웨이** - 이 인터페이스는 카메라, 클라우드 정의 디바이스 쌍 속성 및 게이트웨이 정보에서 들어오는 모든 원격 분석을 나타냅니다.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="디지털 유통 게이트웨이 디바이스 템플릿의 디지털 유통 게이트웨이 인터페이스를 보여주는 스크린샷.":::

### <a name="gateway-commands"></a>게이트웨이 명령

이 인터페이스는 모든 게이트웨이 명령 기능을 구성합니다.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="디지털 유통 게이트웨이 디바이스 템플릿의 게이트웨이 명령 인터페이스를 보여주는 스크린샷.":::

### <a name="rules"></a>규칙

규칙 탭을 선택하여 이 애플리케이션 템플릿에 존재하는 두 가지 다른 규칙을 표시합니다. 이러한 규칙은 추가 조사를 위해 운영자에게 메일 알림을 보내도록 구성됩니다.

 **잘못된 패키지가 너무 많음 경고** - 카메라에서 컨베이어 시스템을 통해 전달되는 많은 수의 잘못된 패키지를 감지하는 경우 이 규칙이 트리거됩니다.

**대규모 패키지** - 카메라에서 품질을 검사할 수 없는 방대한 패키지를 감지하는 경우 이 규칙이 트리거됩니다. 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-rules.png" alt-text="디지털 유통 센터 애플리케이션의 규칙 목록을 보여주는 스크린샷.":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 **관리** > **애플리케이션 설정** 을 방문하여 애플리케이션 템플릿을 삭제하고 **삭제** 를 클릭합니다.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-cleanup.png" alt-text="작업을 완료했을 때 애플리케이션을 삭제하는 방법을 보여주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

디지털 유통 센터 솔루션 아키텍처에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [디지털 유통 센터 개념](./architecture-digital-distribution-center.md)
