---
title: Azure Percept 샘플 AI 모델
description: 프로토타입 및 배포에 사용할 수 있는 AI 모델에 대해 자세히 알아봅니다.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 78eb20b0229faef9c067b4bab475759badccd19e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221274"
---
# <a name="azure-percept-sample-ai-models"></a>Azure Percept 샘플 AI 모델

Azure Percept를 사용하면 AI 모델을 개발하고 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)에서 [Azure Percept DK](./overview-azure-percept-dk.md)로 직접 배포할 수 있습니다. 모델 배포는 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 및 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)를 활용합니다.

## <a name="sample-ai-models"></a>샘플 AI 모델

Azure Percept Studio에는 다음 애플리케이션에 대한 샘플 모델이 포함되어 있습니다.

- 인물 감지
- 차량 감지
- 일반 개체 감지
- 매장 제품 감지

미리 학습된 모델을 사용하는 경우 코딩 또는 학습 데이터 수집이 필요하지 않습니다. 포털에서 Azure Percept DK에 [원하는 모델을 배포](./how-to-deploy-model.md)하고 devkit의 [비디오 스트림](./how-to-view-video-stream.md)을 열어 모델 추론이 제대로 작동하는지 확인하면 됩니다. [Azure IoT 탐색기](https://github.com/Azure/azure-iot-explorer/releases) 도구를 통해서도 [모델 추론 원격 분석](./how-to-view-telemetry.md)에 액세스할 수 있습니다.

## <a name="reference-solutions"></a>참조 솔루션

[인원수 산출 참조 솔루션](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app)도 사용할 수 있습니다. 해당 참조 솔루션은 사용자 정의 영역 입장/퇴장 이벤트를 통해 에지 기반 인원수 산출을 제공하는 오픈 소스 AI 애플리케이션입니다. 온-프레미스 에지 디바이스의 비디오 및 AI 출력은 Azure 웹 사이트로 실행되는 사용자 인터페이스와 함께 [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)로 송신됩니다. AI 추론은 인물 감지를 위한 오픈 소스 AI 모델이 제공합니다.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="공간 분석이 미리 구축된 솔루션 gif.":::

## <a name="custom-no-code-solutions"></a>코드 없는 사용자 지정 솔루션

Azure Percept Studio를 통해 코딩 없이 사용자 지정 [비전](./tutorial-nocode-vision.md)과 [음성](./tutorial-no-code-speech.md) 솔루션을 개발할 수 있습니다.

사용자 지정 비전 솔루션의 경우 개체 감지 및 분류 AI 모델을 모두 사용할 수 있습니다. 원한다면 Azure Percept DK의 Azure Percept Vision SoM에서 직접 수행할 수 있는 학습 이미지를 업로드하고 태그를 지정할 수 있습니다. 모델 학습 및 평가는 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview)의 일부인 [Custom Vision](https://www.customvision.ai/)에서 쉽게 수행할 수 있습니다.

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

사용자 지정 음성 솔루션의 경우 현재 다음과 같은 애플리케이션에서 음성 도우미 템플릿을 사용할 수 있습니다.

- 숙박업: 음성 제어 스마트 디바이스를 갖춘 호텔 객실.
- 의료 분야: 음성 제어 스마트 디바이스를 갖춘 의료 시설.
- 재고: 음성 제어 스마트 디바이스를 갖춘 재고 허브.
- 자동차: 음성 제어 스마트 디바이스를 갖춘 자동차 허브.

포털을 통해 미리 구축된 음성 도우미 키워드 및 명령을 직접 사용할 수 있습니다. 사용자 지정 키워드 및 명령은 Azure Cognitive Services의 일부인 [Speech Studio](https://speech.microsoft.com/)에서 만들고 학습시킬 수 있습니다.

## <a name="advanced-development"></a>고급 개발

[Azure Percept DK 고급 개발 GitHub](https://github.com/microsoft/azure-percept-advanced-development)에서 아래 항목들에 대한 최신 지침, 자습서 및 예제를 참조하세요.

- Azure Percept DK 사용자 지정 AI 모델의 배포
- 전이 학습을 통한 지원되는 모델의 업데이트
- 기타
