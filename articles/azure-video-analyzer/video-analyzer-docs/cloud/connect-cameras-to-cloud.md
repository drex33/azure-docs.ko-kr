---
title: 카메라를 Azure Video Analyzer 서비스에 연결
description: 이 문서에서는 카메라를 Azure Video Analyzer 서비스에 직접 연결 하는 방법을 설명 합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 973db00ad3d8b7d0581d74b9de602322c20ac7b3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053597"
---
# <a name="connect-cameras-to-the-cloud"></a>클라우드에 카메라 커넥트

[!INCLUDE [header](includes/cloud-env.md)]

Azure Video Analyzer 서비스를 통해 사용자는 [라이브 파이프라인](../pipeline.md)을 사용 하 여 비디오를 캡처 및 기록 하기 위해 RTSP 카메라를 클라우드에 직접 연결할 수 있습니다. 그러면에 지 장치의 계산 부하가 줄어들고 edge 장치에 대 한 필요성을 완전히 없앨 수 있습니다. 현재 Video Analyzer 서비스는 카메라를 클라우드에 연결 하는 세 가지 방법을 지원 합니다. 즉, 원격 장치 어댑터를 통해 연결 하 고, IoT PnP 명령을 사용 하 여 방화벽 뒤에서 연결 하 고, 방화벽 없이 인터넷을 통해 연결 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-cameras-to-cloud/connect-cameras-to-cloud.svg" alt-text="3 카메라를 클라우드에 연결 하는 다른 방법":::

## <a name="connect-via-a-remote-device-adapter"></a>원격 장치 어댑터를 통해 커넥트

RTSP 카메라와 동일한 (개인) 네트워크의 IoT Edge 장치에 비디오 분석기 edge 모듈을 배포 하 고,에 지 장치를 인터넷에 연결할 수 있습니다. Edge 모듈은 이제 비디오 분석기 서비스를 사용 하 여 *원격 장치* (카메라)에 연결할 수 있는 *어댑터로* 설정할 수 있습니다. Edge 모듈은 RTSP 카메라와 Video Analyzer 서비스 간의 비디오 트래픽에 대 한 [투명 게이트웨이](../../../iot-edge/iot-edge-as-gateway.md) 역할을 합니다. 이 방법은 다음과 같은 시나리오에서 유용 합니다.

* 카메라/장치를 인터넷에 노출 되지 않도록 보호 해야 하는 경우
* 카메라/장치에 IoT Hub에 독립적으로 연결 하는 기능이 없는 경우
* 전원, 공간 또는 기타 고려 사항으로 인해 경량 장치를 온-프레미스에만 배포할 수 있습니다.

Video Analyzer edge 모듈은 IoT Hub 카메라의 메시징 및 원격 분석을 위해 투명 한 게이트웨이로 작동 하지 않고 비디오에 대 한 투명 한 게이트웨이로만 작동 합니다.

## <a name="connect-behind-a-firewall-using-an-iot-pnp-device-implementation"></a>IoT PnP 장치 구현을 사용 하 여 방화벽 뒤에 커넥트

이 방법을 사용 하면 RTSP 카메라 또는 장치가 [IoT 플러그 앤 플레이 명령 인터페이스](../../../iot-develop/overview-iot-plug-and-play.md) 를 사용 하 여 방화벽 뒤에 있는 비디오 분석기에 연결 하 고에 지 장치에 대 한 필요성을 없앨 수 있습니다. 이 방법을 사용 하려면 카메라 또는 장치에 적절 한 IoT PnP 장치 구현을 설치 하 고 실행 해야 합니다. 제조업체에서 호환 되는 장치를 연결 하는 방법에 대 한 정보는 [여기](connect-devices.md)에 있습니다.

## <a name="connect-over-the-internet-no-firewall"></a>인터넷을 통해 커넥트 (방화벽 없음)

이 방법은 방화벽을 사용 하지 않고 인터넷을 통해 장치에 액세스 하도록 비디오 분석기 서비스를 허용할 수 있는 감독 된 개념 증명 연습에만 사용 해야 합니다. 

이와 관련 된 사용 사례는이 [빠른](get-started-livepipelines-portal.md)시작에 설명 된 대로, RTSP 카메라를 시뮬레이션 하기 위해 모듈이 Azure VM에 배포 되는 경우입니다.


## <a name="next-steps"></a>다음 단계

- [이 방법 가이드](use-remote-device-adapter.md) 에 따라 원격 장치 어댑터를 통해 카메라를 연결 합니다.
- IoT PnP 구현을 사용 하 여 장치를 연결 하는 방법에 대 한 자세한 내용은 [이 방법 가이드를](connect-devices.md) 따르세요.
- [이 빠른](get-started-livepipelines-portal.md) 시작을 수행 하 여 인터넷을 통해 카메라 연결
