---
title: 자습서 - Azure IoT 비디오 분석 - 개체 및 동작 감지 | Microsoft Docs
description: 이 자습서에서는 IoT Central용 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿을 배포하고 사용하는 방법을 보여줍니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 09/02/2021
ms.openlocfilehash: 026c1794f678c5d194b0e5174986f9f962508647
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473751"
---
# <a name="tutorial-deploy-and-walk-through-the-video-analytics---object-and-motion-detection-application-template"></a>자습서: 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿 배포 및 연습

주요 *비디오 분석 - 개체 및 동작 감지* 애플리케이션 개요: **비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 사용하여 Live Video Analytics 기능이 포함된 IoT 솔루션을 빌드할 수 있습니다.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="비디오 분석 개체 및 동작 감지 구성 요소 개요 다이어그램":::

비디오 분석 솔루션의 주요 구성 요소는 다음과 같습니다.

### <a name="live-video-analytics-lva"></a>LVA(Live Video Analytics)

LVA는 에지 및 클라우드를 포괄하는 지능형 비디오 애플리케이션을 빌드하기 위한 플랫폼을 제공합니다. 이 플랫폼을 사용하여 에지 및 클라우드를 포괄하는 지능형 비디오 애플리케이션을 빌드할 수 있습니다. 이 플랫폼은 라이브 비디오를 캡처, 녹화 및 분석하는 기능과 비디오 또는 비디오 분석일 수 있는 결과를 Azure 서비스에 게시하는 기능을 제공합니다. Azure 서비스는 클라우드 또는 에지에서 실행할 수 있습니다. 이 플랫폼을 사용하여 비디오 분석을 통해 IoT 솔루션을 개선할 수 있습니다.

자세한 내용은 GitHub에서 [Live Video Analytics](https://github.com/Azure/live-video-analytics)를 참조하세요.

### <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA 게이트웨이 모듈

IoT Edge LVA 게이트웨이 모듈은 카메라를 새 디바이스로 인스턴스화하고 IoT 디바이스 클라이언트 SDK를 사용하여 IoT Central에 직접 연결합니다.

이 참조 구현에서 디바이스는 에지에서 대칭 키를 사용하여 솔루션에 연결합니다. 디바이스 연결에 대한 자세한 내용은 [Azure IoT Central에 연결](../core/concepts-get-connected.md)을 참조하세요.

### <a name="media-graph"></a>미디어 그래프

미디어 그래프를 사용하면 미디어를 캡처할 위치, 처리 방법, 결과를 전달할 위치를 정의할 수 있습니다. 원하는 방식으로 구성 요소 또는 노드를 연결하여 미디어 그래프를 구성합니다. 자세한 내용은 GitHub에서 [미디어 그래프](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph)를 참조하세요.

다음 비디오에서는 _비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿_ 을 사용하여 IoT Central 솔루션을 배포하는 방법을 연습합니다.

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

이 자습서 세트에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션 배포
> * 애플리케이션에 연결하는 IoT Edge 인스턴스 배포
> * 애플리케이션 모니터링 및 관리

## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* 무료 가격 책정 요금제를 사용하거나 Azure 구독을 사용할 수 있습니다.

## <a name="deploy-the-application"></a>애플리케이션 배포

다음 단계에 따라 비디오 분석 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 배포합니다.

1. [Azure IoT Central(YOLO v3)에서 비디오 분석 애플리케이션 만들기](tutorial-video-analytics-create-app-yolo-v3.md) 또는 [Azure IoT Central(OpenVINO&trade;)에서 비디오 분석 만들기](tutorial-video-analytics-create-app-openvino.md) 자습서 중 하나를 완료하여 다음을 수행합니다.
    - Azure Media Services 계정을 만듭니다.
    - 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿에서 IoT Central 애플리케이션을 만듭니다.
    - IoT Central 애플리케이션에서 게이트웨이 디바이스를 구성합니다. 게이트웨이를 구성하면 카메라 디바이스를 애플리케이션에 연결할 수 있습니다.

1. [비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](tutorial-video-analytics-iot-edge-vm.md) 또는 [자습서: 비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) 자습서를 완료하여 다음을 수행합니다.
    - Azure IoT Edge 런타임이 설치된 Azure VM을 만듭니다. 비디오 분석 모듈을 호스트할 IoT Edge 설치를 준비합니다.
    - IoT Edge 디바이스를 IoT Central 애플리케이션에 연결합니다.

1. [비디오 분석 애플리케이션 모니터링 및 관리](tutorial-video-analytics-manage.md) 자습서를 완료하여 다음을 수행합니다.
    - IoT Central 애플리케이션의 게이트웨이에 개체 및 동작 감지 카메라를 추가합니다.
    - 카메라 처리를 시작합니다.
    - AMS에서 캡처한 비디오를 볼 수 있도록 로컬 미디어 플레이어를 설치합니다.
    - 감지된 개체를 보여주는 캡처된 비디오를 봅니다.
    - 정리합니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 사용을 마친 후에는 앞에서 만든 모든 리소스를 다음과 같이 제거할 수 있습니다.

1. IoT Central 애플리케이션의 **관리** 섹션에서 **사용자의 애플리케이션** 페이지로 이동합니다. 그런 다음, **삭제** 를 선택합니다.
1. Azure Portal에서 **lva-rg** 리소스 그룹을 삭제합니다.
1. 로컬 머신에서 **amp-viewer** Docker 컨테이너를 중지합니다.

## <a name="next-steps"></a>다음 단계

비디오 분석 애플리케이션 템플릿을 배포하고 사용하는 단계를 알아보았으므로, 이제

> [!div class="nextstepaction"]
> [Azure IoT Central(YOLO v3)에서 비디오 분석 애플리케이션 만들기](tutorial-video-analytics-create-app-yolo-v3.md) 또는

> [!div class="nextstepaction"]
> [Azure IoT Central(OpenVINO&trade;)에서 비디오 분석 만들기](tutorial-video-analytics-create-app-openvino.md) 자습서에 따라 시작하세요.