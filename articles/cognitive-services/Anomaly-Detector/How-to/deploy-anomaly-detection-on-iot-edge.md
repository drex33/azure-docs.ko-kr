---
title: IoT Edge에서 Anomaly Detector 실행
titleSuffix: Azure Cognitive Services
description: Anomaly Detector 모듈을 IoT Edge에 배포합니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: fa2904768023120fb2b08a52a23bb33e71e4d2f6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219151"
---
# <a name="deploy-an-anomaly-detector-univariate-module-to-iot-edge"></a>IoT Edge에 Anomaly Detector 단변량 모듈 배포

IoT Edge 디바이스에 Cognitive Services [Anomaly Detector](../anomaly-detector-container-howto.md) 모듈을 배포하는 방법을 알아봅니다. IoT Edge에 배포되고 나면 모듈은 다른 모듈과 함께 IoT Edge에서 컨테이너 인스턴스로 실행됩니다. 표준 Docker 컨테이너 환경에서 실행되는 Anomaly Detector 컨테이너 인스턴스와 정확히 동일한 API를 노출합니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독을 사용합니다. Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.
* [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
* [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) 및 [IoT Edge](../../../iot-edge/quickstart-linux.md) 디바이스를 준비합니다.

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Anomaly Detection 모듈을 에지에 배포

1. Azure Portal에서 검색에 **IoT Edge의 Anomaly Detector** 를 입력하고 Azure Marketplace 결과를 엽니다.
2. Azure Portal의 [IoT Edge 모듈의 대상 디바이스](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector) 페이지가 표시됩니다. 다음 필수 정보를 제공합니다.

    1. 구독을 선택합니다.

    1. 자신의 IoT Hub를 선택합니다.

    1. **디바이스 찾기** 를 선택하고 IoT Edge 디바이스를 찾습니다.

3. **생성** 단추를 선택합니다.

4. **AnomalyDetectoronIoTEdge** 모듈을 선택합니다.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="AnomalyDetectoronIoTEdge 링크가 있는 IoT Edge 모듈 사용자 인터페이스 이미지가 빨간색 상자로 강조 표시되어 있고 이는 선택해야 할 항목임을 나타냅니다.":::

5. **환경 변수** 를 탐색하고 다음 정보를 제공합니다.

    1.  Eula에 대해 **수락** 값을 유지합니다.

    1. Cognitive Services 엔드포인트로 **청구** 를 채웁니다.

    1. Cognitive Services API 키로 **ApiKey** 를 채웁니다.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="엔드포인트 및 API 키에 대한 값을 입력해야 하는 영역 주위에 빨간색 상자가 있는 환경 변수":::

6. **업데이트** 를 선택합니다.

7. **다음: 경로** 를 선택하여 경로를 정의합니다. 모든 모듈의 모든 메시지를 Azure IoT Hub로 이동하도록 정의합니다. 경로를 선언하는 방법을 알아보려면 [IoT Edge 경로 설정 을](../../../iot-edge/module-composition.md?view=iotedge-2020-11&preserve-view=true)참조하세요.

8. **다음: 검토 + 만들기** 를 선택합니다. IoT Edge 디바이스에 배포되는 모든 모듈을 정의하는 JSON 파일을 미리 볼 수 있습니다.
    
9. **만들기** 를 선택하여 모듈 배포를 시작합니다.

10. 모듈 배포를 완료한 후에는 IoT 허브의 IoT Edge 페이지로 돌아갑니다. IoT Edge 디바이스 목록에서 디바이스를 선택하면 세부 정보를 볼 수 있습니다.

11. 아래로 스크롤하여 나열된 모듈을 확인합니다. 새 모듈에 대한 런타임 상태가 실행 중인지 확인합니다. 

IoT Edge 디바이스의 런타임 상태를 해결하려면 [문제 해결 가이드를](../../../iot-edge/troubleshoot.md)참조하세요.

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>IoT Edge 디바이스에서 Anomaly Detector 테스트

Azure Cognitive Services 컨테이너가 실행되고 있는 Azure IoT Edge 디바이스에 대한 HTTP 호출을 수행합니다. 컨테이너는 REST 기반 엔드포인트 API를 제공합니다. 모듈 API에는 호스트 `http://<your-edge-device-ipaddress>:5000`을 사용합니다.

또는 Azure IoT Edge [디바이스에서 Anomaly Detector 클라이언트 라이브러리를 사용하여 모듈 클라이언트를 만든](../quickstarts/client-libraries.md?tabs=linux&pivots=programming-language-python) 다음, 에지에서 실행 중인 Azure Cognitive Services 컨테이너를 호출할 수 있습니다. 호스트 엔드포인트를 사용하고 `http://<your-edge-device-ipaddress>:5000` 호스트 키를 비워 둡니다. 

에지 디바이스가 포트 5000에서 인바운드 통신을 아직 허용하지 않는 경우 새 **인바운드 포트 규칙** 을 만들어야 합니다. 

Azure VM의 경우 **가상 머신** > **설정** > **네트워킹** > **인바운드 포트 규칙** > **인바운드 포트 규칙 추가** 에서 설정할 수 있습니다.

모듈이 실행 중인지 확인하는 방법에는 여러 가지가 있습니다. 확인 대상인 에지 디바이스의 *외부 IP* 주소 및 노출된 포트를 찾고 즐겨찾는 웹 브라우저를 엽니다. 아래의 다양한 요청 URL을 사용하여 컨테이너가 실행 중인지 확인합니다. 아래에 나열된 요청 URL 예는 `http://<your-edge-device-ipaddress:5000`이지만 특정 컨테이너는 다를 수 있습니다. 에지 디바이스의 *외부 IP* 주소를 사용해야 한다는 점에 유의합니다.

| 요청 URL | 용도 |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | 컨테이너는 홈페이지를 제공합니다. |
| `http://<your-edge-device-ipaddress>:5000/status` | 또한 GET을 사용하여 요청하면 이는 컨테이너를 시작하는 데 사용된 API 키가 엔드포인트 쿼리를 발생시키지 않고 유효한지 확인합니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | 컨테이너는 엔드포인트에 대한 전체 설명서 세트와 **사용해 보기** 기능을 제공합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |

![컨테이너의 홈페이지](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행](../anomaly-detector-container-configuration.md)을 검토하여 컨테이너 이미지를 풀하고 컨테이너를 실행합니다.
* [컨테이너 구성](../anomaly-detector-container-configuration.md)에서 구성 설정을 검토합니다.
* [Anomaly Detector API 서비스에 대해 자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
