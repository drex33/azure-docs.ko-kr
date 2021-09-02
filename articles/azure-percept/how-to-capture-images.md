---
title: Azure Percept Studio의 이미지 캡처
description: Azure Percept Studio에서 Azure Percept DK로 이미지를 캡처하는 방법
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: b724e18d8f9119ac33d878cfbf4393c4712d003f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219680"
---
# <a name="capture-images-in-azure-percept-studio"></a>Azure Percept Studio의 이미지 캡처

이 가이드에 따라 기존 비전 프로젝트에 Azure Percept DK를 사용하여 이미지를 캡처합니다. 비전 프로젝트를 아직 만들지 않은 경우 [코드 없는 비전 자습서](./tutorial-nocode-vision.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결
- [코드 없는 비전 프로젝트](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>이미지 캡처

1. Devkit의 전원을 켭니다.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. 개요 페이지의 왼쪽에서 **디바이스** 를 선택합니다.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Azure Percept Studio 개요 화면" lightbox="./media/how-to-capture-images/overview-devices.png":::

1. 목록에서 devkit를 선택합니다.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Percept 디바이스 목록.":::

1. 디바이스 페이지에서 **프로젝트에 대한 이미지 캡처** 를 선택합니다.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="사용 가능한 작업이 나열된 Percept 디바이스 페이지":::

1. **이미지 캡처** 창에서 다음 단계를 따르세요.

    1. **프로젝트** 드롭다운 메뉴에서 이미지를 수집하려는 비전 프로젝트를 선택합니다.

    1. **디바이스 스트림 보기** 를 선택하여 Vision SoM의 카메라가 올바르게 배치되었는지 확인합니다.

    1. **사진 촬영** 을 선택하여 이미지를 캡처합니다.

    1. 대신 **자동 이미지 캡처** 옆의 상자를 선택하여 이미지 캡처에 대한 타이머를 설정합니다.

        1. **캡처 비율** 에서 선호하는 이미징 비율을 선택합니다.
        1. **대상** 에서 수집할 이미지의 총 수를 선택합니다.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="이미지 캡처 화면":::

모든 이미지는 [Custom Vision](https://www.customvision.ai/)에서 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[코드 없는 비전 모델을 테스트하고 다시 학습](../cognitive-services/custom-vision-service/test-your-model.md)합니다.