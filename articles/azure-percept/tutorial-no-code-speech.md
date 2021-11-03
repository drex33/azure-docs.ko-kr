---
title: Azure Percept Studio에서 코드 없는 음성 도우미 만들기
description: 코드 없는 음성 솔루션을 만들어서 Azure Percept DK에 배포하는 방법을 알아봅니다.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f4c8544c768f3eaf783450acfe27e4a3d42fc833
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059835"
---
# <a name="create-a-no-code-voice-assistant-in-azure-percept-studio"></a>Azure Percept Studio에서 코드 없는 음성 도우미 만들기

이 자습서에서는 템플릿을 통해 Azure Percept DK 및 Azure Percept Audio에서 사용할 음성 도우미를 만듭니다. 음성 도우미 데모는 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 내에서 실행되며 다양한 음성 제어 가상 개체를 포함하고 있습니다. 개체를 제어하려면 디바이스를 절전 모드에서 해제하는 단어 또는 짧은 문구인 키워드를 말한 다음, 명령을 말합니다. 각 템플릿은 특정 명령 세트에 응답합니다.

이 가이드에서는 디바이스를 설정하고, 음성 도우미와 필요한 [음성 서비스](../cognitive-services/speech-service/overview.md) 리소스를 만들고, 음성 도우미를 테스트하고, 키워드를 구성하고, 사용자 지정 키워드를 만드는 과정을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- Azure Percept Audio
- 3\.5mm 오디오 잭에 연결할 수 있는 스피커 또는 헤드폰(선택 사항)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결
- [Azure Percept Audio 설정](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>사용 가능한 템플릿을 사용하여 음성 도우미 만들기

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. **데모 및 자습서** 탭을 엽니다.

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Azure Portal 홈페이지를 보여 주는 스크린샷":::

1. **음성 자습서 및 데모** 에서 **음성 도우미 템플릿 사용해 보기** 를 클릭합니다. 그러면 화면 오른쪽에 창이 하나 열립니다.

1. 이 창에서 다음을 수행합니다.

    1. **IoT Hub** 드롭다운 메뉴에서 devkit가 연결된 IoT 허브를 선택합니다.

    1. **디바이스** 드롭다운 메뉴에서 해당 devkit를 선택합니다.

    1. 사용 가능한 음성 도우미 템플릿 중 하나를 선택합니다.

    1. **이 프로젝트의 사용 약관에 동의** 확인란을 클릭합니다.

    1. **만들기** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="음성 도우미 템플릿 만들기를 보여 주는 스크린샷":::

1. **만들기** 를 클릭하면 포털에서 음성 테마 리소스를 만드는 또 다른 창이 열립니다. 이 창에서 다음을 수행합니다.

    1. **구독** 상자에서 해당하는 Azure 구독을 선택합니다.

    1. **리소스 그룹** 드롭다운 메뉴에서 원하는 리소스 그룹을 선택합니다. 음성 도우미에 사용할 새 리소스 그룹을 만들려면 드롭다운 메뉴에서 **만들기** 를 클릭하고 프롬프트의 지시를 따릅니다.

    1. **애플리케이션 접두사** 에 이름을 입력합니다. 이 이름은 프로젝트의 접두사와 사용자 지정 명령 이름이 됩니다.

    1. **영역** 에서 리소스를 배포할 영역을 선택합니다.

    1. **LUIS 예측 가격 책정 계층** 에서 **표준** 을 선택합니다(체험 계층은 음성 요청을 지원하지 않음).

    1. **만들기** 단추를 클릭합니다. 음성 도우미 애플리케이션에 사용할 리소스는 구독에 배포됩니다.

        > [!WARNING]
        > 포털이 리소스 배포를 완료할 때까지 창을 **닫지 마세요**. 창을 중간에 닫으면 음성 도우미가 뜻하지 않은 방향으로 작동할 수 있습니다. 리소스가 배포되면 데모가 표시됩니다.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="구독 및 리소스 그룹 선택 창을 보여 주는 스크린샷":::

## <a name="test-out-your-voice-assistant"></a>음성 도우미 테스트

음성 도우미와 상호 작용하려면 키워드를 말하고 명령을 말합니다. Ear SoM이 키워드를 인식하면 디바이스에서 차임벨 소리가 울리고(스피커 또는 헤드폰이 연결되어 있다면 들을 수 있음), LED가 파란색으로 깜박입니다. 명령이 처리되는 동안 LED가 레이싱 블루 색으로 바뀝니다. 명령에 대한 음성 도우미의 응답이 데모 창에 텍스트로 출력되고 스피커/헤드폰을 통해 소리로 방출됩니다. 기본 키워드(**사용자 지정 키워드** 옆에 나열됨)는 "컴퓨터"로 설정되며, 각 템플릿에는 데모 창의 가상 개체와 상호 작용할 수 있는 호환 명령 세트가 포함되어 있습니다. 예를 들어 접객 또는 의료 데모를 사용하는 경우 "Computer, turn on TV(컴퓨터, TV 켜)"라고 말하면 가상 TV가 켜집니다.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="접객 데모 창을 보여 주는 스크린샷":::

### <a name="hospitality-and-healthcare-demo-commands"></a>접객 및 의료 데모 명령

의료와 접객 데모 모두 상호 작용할 수 있는 가상 TV, 광원, 블라인드 및 자동 온도 조절기가 있습니다. 다음 명령(및 추가 변형)이 지원됩니다.

* "Turn on/off the lights(조명 켜/꺼)".
* "Turn on/off the TV(TV 켜/꺼)".
* "Turn on/off the AC(에어콘 켜/꺼)".
* "Open/close the blinds(블라인드 열어/닫아)".
* "Set temperature to X degrees(온도를 X도로 설정해)". (X는 원하는 온도(예: 75)입니다.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="의료 데모 창을 보여 주는 스크린샷":::

### <a name="automotive-demo-commands"></a>자동차 데모 명령

자동차 데모에는 사용자가 조작할 수 있는 가상 시트 워머, 서리 제거 장치 및 자동 온도 조절기가 포함되어 있습니다. 다음 명령(및 추가 변형)이 지원됩니다.

* "Turn on/off the defroster(서리 제거 장치 켜/꺼)".
* "Turn on/off the seat warmer(시트 워머 켜/꺼)".
* "Set temperature to X degrees(온도를 X도로 설정해)". (X는 원하는 온도(예: 75)입니다.)
* "Increase/decrease the temperature by Y degrees(온도를 Y도로 높여/낮춰)".


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="자동차 데모 창을 보여 주는 스크린샷":::

### <a name="inventory-demo-commands"></a>인벤토리 데모 명령

인벤토리 데모에는 사용자가 조작할 수 있는 가상의 파란색, 노란색 및 녹색 상자와 가상 인벤토리 앱이 포함되어 있습니다. 다음 명령(및 추가 변형)이 지원됩니다.

* "Add/remove X boxes(상자 X개 추가해/제거해)". (X는 상자의 수(예: 4개)입니다.)
* "Order/ship X boxes(상자 X개 주문해/배송해)".
* "How many boxes are in stock(상자 재고가 몇 개 있어)?"
* "Count Y boxes(Y 상자 개수를 알려줘)". (Y는 상자의 색(예: 노랑)입니다.)
* "Ship everything in stock(재고를 전부 배송해)".


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="인벤토리 데모 창을 보여 주는 스크린샷":::

## <a name="configure-your-keyword"></a>키워드 구성

음성 도우미 애플리케이션에 대한 키워드를 사용자 지정할 수 있습니다.

1. 데모 창에서 **사용자 지정 키워드** 옆에 있는 **변경** 을 클릭합니다.

1. 사용 가능한 키워드 중 하나를 선택합니다. 샘플 키워드와 본인이 직접 만든 사용자 지정 키워드 중에서 선택할 수 있습니다.

1. **저장** 을 클릭합니다.

### <a name="create-a-custom-keyword"></a>사용자 지정 키워드 만들기

음성 애플리케이션에 대해 자체 키워드를 만들 수 있습니다. 사용자 지정 키워드에 대한 학습은 몇 분이면 완료됩니다.

1. 데모 창의 상단 근처에서 **+ 사용자 지정 키워드 만들기** 를 클릭합니다. 

1. 원하는 키워드를 입력합니다. 키워드는 한 단어도 되고 짧은 문구도 됩니다.

1. **음성 리소스**(데모 창의 **사용자 지정 명령** 옆에 나열되고 애플리케이션 접두사를 포함하고 있음)를 선택합니다.

1. **저장** 을 클릭합니다. 

## <a name="create-a-custom-command"></a>사용자 지정 명령 만들기

포털에는 기존 음성 리소스를 사용하여 사용자 지정 명령을 만드는 기능도 제공됩니다. "사용자 지정 명령"이란 기존 애플리케이션 내의 특정 명령이 아닌 음성 도우미 애플리케이션 자체를 말합니다. 사용자 지정 명령을 만드는 것은 새 음성 프로젝트를 만드는 것이며, [Speech Studio](https://speech.microsoft.com/)에서 추가로 개발해야 합니다.

데모 창에서 새 사용자 지정 명령을 만들려면 페이지 위쪽에서 **+ 사용자 지정 명령 만들기** 를 클릭하고 다음을 수행합니다.

1. 사용자 지정 명령의 이름을 입력합니다.

1. 프로젝트 설명을 입력합니다(선택 사항).

1. 기본 설정 언어를 선택합니다.

1. 음성 리소스를 선택합니다.

1. LUIS 리소스를 선택합니다.

1. LUIS 제작 리소스를 선택하거나 새로 만듭니다.

1. **만들기** 를 클릭합니다.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="사용자 지정 명령 만들기 창을 보여 주는 스크린샷":::

사용자 지정 명령을 만든 후에는 추가 개발을 위해 [Speech Studio](https://speech.microsoft.com/)로 이동해야 합니다. Speech Studio를 열었는데 사용자 지정 명령이 표시되지 않으면 다음 단계를 수행합니다.

1. Azure Percept Studio의 왼쪽 메뉴 패널에서 **AI 프로젝트** 아래에 있는 **음성** 을 클릭합니다.

1. **명령** 탭을 선택합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="편집할 수 있는 사용자 지정 명령의 목록을 보여 주는 스크린샷":::

1. 개발하려는 사용자 지정 명령을 선택합니다. 그러면 Speech Studio에서 프로젝트가 열립니다.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Speech Studio 홈 화면을 보여 주는 스크린샷":::

사용자 지정 명령 개발에 대한 자세한 내용은 [Speech Service 설명서](../cognitive-services/speech-service/custom-commands.md)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>음성 도우미를 만들었지만 명령에 응답하지 않는 경우

인터포저 보드에서 LED 표시등을 확인합니다.

* 파란색 표시등 3개는 음성 도우미가 준비되어 키워드를 기다리고 있다는 뜻입니다.
* 중앙의 LED(L02)가 흰색이면 devkit가 초기화를 완료했으며 키워드를 사용하여 구성해야 한다는 뜻입니다.
* 중앙의 LED(L02)가 흰색으로 깜박이면 Audio SoM이 아직 초기화를 완료하지 않았다는 뜻입니다. 초기화가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

LED 표시등에 대한 자세한 내용은 [LED 문서](./audio-button-led-behavior.md)를 참조하세요.

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>음성 도우미가 Speech Studio에서 만든 사용자 지정 키워드에 응답하지 않는 경우

음성 모듈이 오래된 경우에 이 문제가 발생할 수 있습니다. 다음 단계에 따라 음성 모듈을 최신 버전으로 업데이트합니다.

1. Azure Percept Studio 홈페이지의 왼쪽 메뉴 패널에서 **디바이스** 를 클릭합니다.

1. 해당 디바이스를 찾아서 선택합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Azure Percept Studio의 디바이스 목록을 보여 주는 스크린샷":::

1. 디바이스 창에서 **음성** 탭을 선택합니다.

1. 음성 모듈 버전을 확인합니다. 사용 가능한 업데이트가 있으면 버전 번호 옆에 **업데이트** 단추가 표시됩니다.

1. **업데이트** 를 클릭하여 음성 모듈 업데이트를 배포합니다. 일반적으로 업데이트 프로세스를 완료하는 데 2-3분 정도 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

음성 도우미 애플리케이션 작업이 끝났으면 다음 단계에 따라 이 자습서에서 배포한 음성 리소스를 정리합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴 패널에서 **리소스 그룹** 을 선택하거나 검색 창에 리소스 그룹을 입력합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="왼쪽 메뉴 패널 및 리소스 그룹을 보여 주는 Azure Portal 홈페이지 스크린샷":::

1. 리소스 그룹을 선택합니다.

1. 해당하는 애플리케이션 접두사가 포함된 6개 리소스를 모두 선택하고 위쪽 메뉴 패널에서 **삭제** 아이콘을 클릭합니다.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="삭제하기 위해 선택한 음성 리소스를 보여 주는 스크린샷":::

1. 삭제를 확인하려면 확인 상자에 **예** 를 입력하고 올바른 리소스를 선택했는지 확인한 다음, **삭제** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="삭제 확인 창을 보여 주는 스크린샷":::

> [!WARNING]
> 그러면 삭제하려는 음성 리소스를 사용하여 만든 사용자 지정 키워드가 모두 제거되고, 음성 도우미 데모는 더 이상 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

코드 없는 음성 솔루션을 만들었으므로, 이제 Azure Percept DK에 사용할 [코드 없는 비전 솔루션](./tutorial-nocode-vision.md)을 만들어 보세요.
