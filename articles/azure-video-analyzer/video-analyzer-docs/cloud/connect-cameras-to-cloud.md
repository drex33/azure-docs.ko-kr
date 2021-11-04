---
title: Azure Video Analyzer 서비스에 카메라 연결
description: 이 문서에서는 카메라를 Azure Video Analyzer 서비스에 직접 연결하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 35a7ad74c8f20ccf748140841332ee6e9b6cd27b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558804"
---
# <a name="connect-cameras-to-the-cloud"></a>클라우드에 카메라 커넥트

[!INCLUDE [header](includes/cloud-env.md)]

Azure Video Analyzer 서비스를 사용하면 사용자가 [라이브 파이프라인을](../pipeline.md)사용하여 순서대로 비디오를 캡처하고 녹화하기 위해 RTSP 카메라를 클라우드에 직접 연결할 수 있습니다. 이렇게 하면 에지 디바이스의 계산 부하가 감소하거나 에지 디바이스가 완전히 필요하지 않습니다. Video Analyzer 서비스는 현재 원격 디바이스 어댑터를 통해 연결하고, IoT PnP 명령을 사용하여 방화벽 뒤에서 연결하고, 방화벽 없이 인터넷을 통해 연결하는 세 가지 방법을 지원합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-cameras-to-cloud/connect-cameras-to-cloud.svg" alt-text="카메라를 클라우드에 연결하는 3가지 방법":::

## <a name="connect-via-a-remote-device-adapter"></a>원격 디바이스 어댑터를 통한 커넥트

VIDEO Analyzer 에지 모듈을 RTSP 카메라와 동일한(프라이빗) 네트워크의 IoT Edge 디바이스에 배포하고 에지 디바이스를 인터넷에 연결할 수 있습니다. 이제 Edge 모듈을 Video Analyzer 서비스가 원격 디바이스(카메라)에  연결할 수 있도록 하는 *어댑터로* 설정할 수 있습니다. 에지 모듈은 RTSP 카메라와 Video Analyzer 서비스 간의 비디오 트래픽에 대한 [투명 게이트웨이](../../../iot-edge/iot-edge-as-gateway.md) 역할을 합니다. 이 방법은 다음과 같은 시나리오에서 유용합니다.

* 카메라/디바이스가 인터넷에 노출되지 못하도록 보호해야 하는 경우
* 카메라/디바이스에 IoT Hub 독립적으로 연결할 수 있는 기능이 없는 경우
* 전원, 공간 또는 기타 고려 사항으로 경량 에지 디바이스만 온-프레미스에 배포할 수 있는 경우

Video Analyzer 에지 모듈은 카메라에서 IoT Hub 메시징 및 원격 분석에 대한 투명 게이트웨이 역할을 하지 않고 비디오에 대한 투명 게이트웨이로만 작동합니다.

## <a name="connect-behind-a-firewall-using-an-iot-pnp-device-implementation"></a>IoT PnP 디바이스 구현을 사용하여 방화벽 뒤에 커넥트

이 방법을 사용하면 RTSP 카메라 또는 디바이스가 [IoT 플러그 앤 플레이 명령 인터페이스를](../../../iot-develop/overview-iot-plug-and-play.md) 사용하여 방화벽 뒤의 Video Analyzer에 연결할 수 있으며 에지 디바이스가 필요하지 않습니다. 이 방법을 사용하려면 적절한 IoT PnP 디바이스 구현을 카메라 또는 디바이스에서 설치하고 실행해야 합니다. 모든 제조업체에서 호환되는 디바이스를 연결하는 방법에 대한 정보는 [여기를](connect-devices.md)참조하십시오.

## <a name="connect-over-the-internet-no-firewall"></a>인터넷을 통해 커넥트(방화벽 없음)

이 방법은 비디오 분석기 서비스가 방화벽 없이 인터넷을 통해 디바이스에 액세스할 수 있도록 허용하는 감독된 개념 증명 연습에만 사용해야 합니다. 

관련 사용 사례는 이 빠른 시작 에 설명된 대로 RTSP 카메라를 시뮬레이션하기 위해 모듈을 Azure VM에 [배포하는](get-started-livepipelines-portal.md)경우입니다.


## <a name="next-steps"></a>다음 단계

- [이 방법 가이드에](use-remote-device-adapter.md) 따라 원격 디바이스 어댑터를 통해 카메라를 연결합니다.
- IoT PnP 구현을 사용하여 디바이스를 연결하는 방법에 대한 자세한 내용은 [이 방법 가이드를](connect-devices.md) 따르세요.
- [이 빠른 시작에](get-started-livepipelines-portal.md) 따라 인터넷을 통해 카메라를 연결합니다.
