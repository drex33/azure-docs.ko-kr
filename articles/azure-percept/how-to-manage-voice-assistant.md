---
title: Azure Percept 음성 도우미 애플리케이션 관리
description: Azure Percept Studio 내에서 음성 도우미 애플리케이션 구성
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: d66ee6e8e277585061448ecc23e4195c72c04bb6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225232"
---
# <a name="manage-your-azure-percept-voice-assistant-application"></a>Azure Percept 음성 도우미 애플리케이션 관리

이 문서에서는 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)내에서 음성 도우미 애플리케이션의 키워드 및 명령을 구성하는 방법을 설명합니다. 포털 대신 IoT Hub 내에서 키워드를 구성하는 방법에 대한 지침은 이 [방법 문서](./how-to-configure-voice-assistant.md)를 참조하세요.

음성 도우미 애플리케이션을 아직 만들지 않은 경우 [Azure Percept Studio 및 Azure Percept Audio를 사용하여 코드 없는 음성 도우미 빌드](./tutorial-no-code-speech.md)를 참조하세요.

## <a name="keyword-configuration"></a>키워드 구성

키워드는 음성 도우미를 활성화하는 데 사용되는 단어 또는 짧은 문구입니다. 예를 들어, “Hey Cortana”는 Cortana 도우미의 키워드입니다. 음성 활성화를 사용하는 경우 사용자가 키워드를 말하는 방법으로 제품을 핸즈프리로 사용할 수 있습니다. 제품이 키워드를 지속적으로 수신하는 동안, 모든 오디오는 검색을 수행하여 사용자 데이터가 가능한 한 비공개로 유지되게 만들어질 때까지 디바이스에서 로컬로 처리됩니다.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>음성 도우미 데모 창에서 구성

1. 데모 페이지에서 **사용자 지정 키워드** 옆의 **변경** 을 선택합니다.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="접객 데모 창을 보여 주는 스크린샷":::

    데모 페이지가 열려있지 않는 경우 디바이스 페이지로 이동하고(아래 참조) **작업** 아래에서 **음성 도우미 테스트** 를 선택하여 데모에 액세스합니다.

1. 사용 가능한 키워드 중 하나를 선택하고 **저장** 을 선택하여 변경 내용을 적용합니다.

1. 구성이 완료되어 음성 도우미를 사용할 준비가 되면, Azure Percept Audio 디바이스의 LED 표시등 세 개가 밝은 파란색(깜박이지 않음)으로 변경됩니다.

### <a name="configuration-within-the-device-page"></a>디바이스 페이지 내 구성

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)의 개요 페이지에 있는 왼쪽 메뉴 창에서 **디바이스** 를 선택합니다.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="디바이스가 강조 표시된 Azure Percept Studio 개요 페이지의 스크린샷":::

1. 음성 도우미 애플리케이션이 배포된 디바이스를 선택합니다.

1. **음성** 탭을 엽니다.

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="음성 탭이 강조 표시된 에지 디바이스 페이지의 스크린샷":::

1. **키워드** 옆의 **변경** 을 선택합니다.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="사용 가능한 음성 솔루션 작업의 스크린샷":::

1. 사용 가능한 키워드 중 하나를 선택하고 **저장** 을 선택하여 변경 내용을 적용합니다.

1. 구성이 완료되어 음성 도우미를 사용할 준비가 되면, Azure Percept Audio 디바이스의 LED 표시등 세 개가 밝은 파란색(깜박이지 않음)으로 변경됩니다.

## <a name="create-a-custom-keyword"></a>사용자 지정 키워드 만들기

[Speech Studio](https://speech.microsoft.com/)를 사용하여 음성 도우미에 대한 사용자 지정 키워드를 만들 수 있습니다. 기본 사용자 지정 키워드 모델을 학습하는 데 최대 30분이 걸립니다.

사용자 지정 키워드를 만드는 방법에 대한 지침은 [Speech Studio 설명서](../cognitive-services/speech-service/custom-keyword-basics.md)를 참조하세요. 구성된 새 키워드는 음성 도우미 애플리케이션에서 사용할 수 있도록 Project Santa Cruz 포털에서 사용할 수 있습니다.

## <a name="commands-configuration"></a>명령 구성

사용자 지정 명령을 사용하면 음성 우선 상호 작용 환경에 최적화된 풍부한 음성 명령 앱을 쉽게 빌드할 수 있습니다. 사용자 지정 명령은 작업 완료 또는 명령 및 컨트롤 시나리오에 가장 적합합니다.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>음성 도우미 데모 창에서 구성

1. 데모 페이지에서 **사용자 지정 명령** 옆에 있는 **변경** 을 선택합니다. 데모 페이지가 열려있지 않는 경우 디바이스 페이지로 이동하고(아래 참조) **작업** 아래에서 **음성 도우미 테스트** 를 선택하여 데모에 액세스합니다.

1. 사용 가능한 사용자 지정 명령 중 하나를 선택하고 **저장** 을 선택하여 변경 내용을 적용합니다.

### <a name="configuration-within-the-device-page"></a>디바이스 페이지 내 구성

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)의 개요 페이지에 있는 왼쪽 메뉴 창에서 **디바이스** 를 선택합니다.

1. 음성 도우미 애플리케이션이 배포된 디바이스를 선택합니다.

1. **음성** 탭을 엽니다.

1. **명령** 옆의 **변경** 을 선택합니다.

1. 사용 가능한 사용자 지정 명령 중 하나를 선택하고 **저장** 을 선택하여 변경 내용을 적용합니다.

## <a name="create-custom-commands"></a>사용자 지정 명령 만들기

[Speech Studio](https://speech.microsoft.com/)를 사용하여 음성 도우미가 실행할 사용자 지정 명령을 만들 수 있습니다.

사용자 지정 명령 만들기에 대한 지침은 [Speech Studio 설명서](../cognitive-services/speech-service/quickstart-custom-commands-application.md)를 참조하세요. 구성된 새 명령은 Azure Percept Studio에서 음성 도우미 애플리케이션에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

음성 도우미 애플리케이션을 빌드한 후에는 Azure Percept DK로 [코드를 사용하지 않는 비전 솔루션](./tutorial-nocode-vision.md)을 개발해 보세요.