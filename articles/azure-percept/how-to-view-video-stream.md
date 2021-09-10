---
title: Azure Percept DK RTSP 비디오 스트림 보기
description: Azure Percept DK에서 RTSP 비디오 스트림을 보는 방법에 대해 알아봅니다.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 10d836e6d77f83e70efa1fc7757c8d8dd1d3f3f4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221636"
---
# <a name="view-your-azure-percept-dk-rtsp-video-stream"></a>Azure Percept DK RTSP 비디오 스트림 보기

이 가이드에 따라 Azure Percept Studio 내에서 Azure Percept DK의 RTSP 비디오 스트림을 볼 수 있습니다. 디바이스에 배포된 비전 AI 모델의 추론은 웹 스트림에서 볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결

## <a name="view-the-rtsp-video-stream"></a>RTSP 비디오 스트림 보기

1. Devkit의 전원을 켭니다.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. 개요 페이지의 왼쪽에서 **디바이스** 를 클릭합니다.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Azure Percept Studio 개요 화면" lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. 목록에서 devkit를 선택합니다.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Azure Percept Studio에서 사용 가능한 디바이스의 스크린샷":::

1. **디바이스 스트림 보기** 를 클릭합니다.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="사용 가능한 비전 프로젝트 작업을 보여주는 디바이스 페이지의 스크린샷":::

    이렇게 하면 Azure Percept DK의 라이브 웹 스트림을 보여주는 별도의 탭이 열립니다.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="디바이스 웹 스트림의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[Azure Percept DK 원격 분석](./how-to-view-telemetry.md)을 보는 방법에 대해 알아봅니다.