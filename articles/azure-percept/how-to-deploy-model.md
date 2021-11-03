---
title: Azure Percept DK에 비전 AI 모델 배포
description: Azure Percept Studio에서 Azure Percept DK에 비전 AI 모델을 배포하는 방법을 알아봅니다.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 55948f2beede0597a30dd557a82ee297c6a540ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006376"
---
# <a name="deploy-a-vision-ai-model-to-azure-percept-dk"></a>Azure Percept DK에 비전 AI 모델 배포

이 가이드를 따라 Azure Percept Studio에서 Azure Percept DK에 비전 AI 모델을 배포합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결

## <a name="model-deployment"></a>모델 배포

1. Devkit의 전원을 켭니다.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. 개요 페이지의 왼쪽에서 **디바이스** 를 클릭합니다.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Azure Percept Studio 개요 화면" lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. 목록에서 devkit를 선택합니다.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Percept 디바이스 목록.":::

1. 미리 학습된 샘플 비전 모델 중 하나를 배포하려면 다음 페이지에서 **샘플 모델 배포** 를 클릭합니다. 기존 [사용자 지정 코드 없는 비전 솔루션](./tutorial-nocode-vision.md)을 배포하려면 **Custom Vision 프로젝트 배포** 를 클릭합니다.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="배포를 위한 모델 선택 항목":::

1. 코드 없는 비전 솔루션을 배포하도록 선택한 경우 프로젝트 및 원하는 모델 반복을 선택하고 **배포** 를 클릭합니다.

1. 샘플 모델을 배포하도록 선택한 경우 모델을 선택하고 **디바이스에 배포** 를 클릭합니다.

1. 모델 배포에 성공하면 화면의 오른쪽 위 모서리에 상태 메시지가 표시됩니다. 작동하는 모델 추론을 보려면 상태 메시지에서 **스트림 보기** 링크를 클릭하여 devkit의 Vision SoM에서 RTSP 비디오 스트림을 확인합니다.

## <a name="next-steps"></a>다음 단계

[Azure Percept DK 원격 분석](how-to-view-telemetry.md)을 보는 방법에 대해 알아봅니다.
