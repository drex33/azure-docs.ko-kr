---
title: '자습서: Speech SDK를 사용하여 봇 음성 지원 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Microsoft Bot Framework를 사용하여 Echo Bot을 만들고, Azure에 배포하고, Bot Framework Direct Line Speech 채널에 등록합니다. 그런 다음, 봇에게 말을 하고 그에 대한 봇의 대답을 들을 수 있는 Windows용 샘플 클라이언트 앱을 구성합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: c29477a7e9f5ed7ea9f26d4169b092ba8307e30a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539572"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>자습서: Speech SDK를 사용하여 봇 음성 지원

Speech Service를 사용하여 챗봇의 음성을 지원할 수 있습니다.

이 자습서에서는 사용자가 말하는 내용을 반복하는 봇을 만듭니다.
Microsoft Bot Framework를 사용하여 봇을 만들고, Azure에 배포하고, Bot Framework Direct Line Speech 채널에 등록합니다.
그런 다음, 봇에게 말을 하고 그에 대한 봇의 대답을 들을 수 있는 Windows용 샘플 클라이언트 앱을 구성합니다.

이 자습서는 Azure, Bot Framework 봇, Direct Line Speech 또는 Speech SDK를 처음 접하고, 제한된 코딩을 통해 실제 작동하는 시스템을 신속하게 구축하려는 개발자를 위해 작성되었습니다. 이러한 서비스에 대한 경험이나 지식이 필요하지 않습니다.

이 자습서에서 만드는 음성 지원 챗봇은 다음 단계를 따릅니다.

1. Direct Line Speech 채널과 Echo Bot에 연결하도록 샘플 클라이언트 애플리케이션을 구성합니다.
1. 사용자가 단추를 누르면 마이크에서 음성 오디오가 스트리밍됩니다. (또는 사용자 지정 키워드를 사용하는 경우 오디오가 지속적으로 기록됩니다.)
1. 사용자 지정 키워드를 사용하는 경우 로컬 디바이스에서 키워드 검색이 수행되고 오디오 스트리밍이 클라우드로 제한됩니다.
1. 샘플 클라이언트 애플리케이션은 Speech SDK를 사용하여 Direct Line Speech에 연결하고 오디오를 스트리밍합니다.
1. 필요에 따라 서비스에서 더 높은 정확도 키워드 확인이 수행됩니다.
1. 오디오가 음성 인식 서비스에 전달되고 텍스트로 기록됩니다.
1. 인식된 텍스트는 Bot Framework 활동으로 Echo Bot에 전달됩니다.
1. 응답 텍스트는 TTS(텍스트 음성 변환) 서비스를 통해 오디오로 전환되고, 재생을 위해 다시 클라이언트 애플리케이션으로 스트리밍됩니다.

<!-- svg src in User Story 1754106 -->
![다이어그램 태그](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "음성 채널 흐름")

> [!NOTE]
> 이 자습서의 단계를 수행하는 데 유료 서비스가 필요하지 않습니다. 새 Azure 사용자는 Azure 평가판 구독의 크레딧과 Speech Service 체험 계층을 사용하여 이 자습서를 완료할 수 있습니다.

이 자습서에서는 다음 내용을 다룹니다.
> [!div class="checklist"]
> * 새 Azure 리소스 만들기
> * Echo Bot 샘플을 빌드 및 테스트하고 Azure App Service에 배포
> * Direct Line Speech 채널에 봇 등록
> * Windows 음성 도우미 클라이언트를 빌드하고 실행하여 Echo Bot과 상호 작용
> * 사용자 지정 키워드 활성화 추가
> * 인식하고 말하는 음성의 언어를 변경하는 방법 알아보기

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 사항은 다음과 같습니다.

- 마이크 및 스피커(또는 헤드폰)가 정상적으로 작동하는 Windows 10 PC
- **ASP.NET 및 웹 개발** 워크로드가 설치되어 있는 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 이상
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) 이상
- Azure 계정. [무료로 가입](https://azure.microsoft.com/free/cognitive-services/)하세요.
- [GitHub](https://github.com/) 계정
- [Windows용 GIT](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 자습서에서 만들 클라이언트 앱은 몇 가지 Azure 서비스를 사용합니다. 봇의 응답에 대한 왕복 시간을 줄이려면 이러한 서비스가 동일한 Azure 지역에 있는 것이 좋습니다. 이 섹션에서는 **미국 서부** 지역에 리소스 그룹을 만듭니다. 이 리소스 그룹은 Bot Framework, Direct Line Speech 채널 및 Speech Service를 위한 개별 리소스를 만들 때 사용됩니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">리소스 그룹을 만듭니다</a>.
1. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * **구독** 을 **평가판** 으로 설정합니다(기존 구독을 사용할 수도 있음).
   * **리소스 그룹** 의 이름을 입력합니다. 권장하는 이름은 **SpeechEchoBotTutorial-ResourceGroup** 입니다.
   * **지역** 드롭다운에서 **미국 서부** 를 선택합니다.
1. **검토 + 만들기** 를 클릭합니다. **유효성 검사 통과** 라는 배너가 표시됩니다.
1. **만들기** 를 클릭합니다. 리소스 그룹을 만드는 데 몇 분 정도 걸릴 수 있습니다.
1. 이 자습서의 뒷부분에서 만들 리소스와 마찬가지로, 쉽게 액세스할 수 있도록 이 리소스 그룹을 대시보드에 고정해 두는 것이 좋습니다. 이 리소스 그룹을 고정하려면 리소스 그룹 이름 오른쪽에 있는 고정 아이콘을 클릭합니다.

### <a name="choosing-an-azure-region"></a>Azure 지역 선택

이 자습서에 다른 지역을 사용하려는 경우 다음과 같은 요소 때문에 선택 가능한 지역이 제한될 수 있습니다.

* [지원되는 Azure 지역](regions.md#voice-assistants)을 사용하는지 확인합니다.
* Direct Line Speech 채널은 인공신경망 및 표준 음성을 제공하는 텍스트 음성 변환 서비스를 사용합니다. 인공신경망 및 표준 음성은 다음 [Azure 지역](regions.md#neural-and-standard-voices)에서 모두 사용할 수 있습니다.

지역에 대한 자세한 내용은 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.

## <a name="create-resources"></a>리소스 만들기

지원되는 지역에 리소스 그룹이 있으므로, 다음 단계는 이 자습서에서 사용할 각 서비스에 대한 개별 리소스를 만드는 것입니다.

### <a name="create-a-speech-service-resource"></a>음성 서비스 리소스 만들기

다음 지침에 따라 Speech 리소스를 만듭니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Speech Service 리소스 만들기</a>
4. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * 리소스 **이름** 을 지정합니다. 권장하는 이름은 **SpeechEchoBotTutorial-Speech** 입니다.
   * **구독** 으로 **평가판** 을 선택합니다.
   * **위치** 로 **미국 서부** 를 선택합니다.
   * **가격 책정 계층** 으로 **F0** 을 선택합니다. F0은 평가판 계층입니다.
   * **리소스 그룹** 으로 **SpeechEchoBotTutorial-ResourceGroup** 을 선택합니다.
5. 모든 필수 정보를 입력한 후 **만들기** 를 클릭합니다. 리소스를 만드는 데 몇 분 정도 걸릴 수 있습니다.
6. 이 자습서의 뒷부분에서 이 서비스의 구독 키가 필요합니다. 언제든지 리소스의 **개요**(키 관리) 또는 **키** 에서 이러한 키에 액세스할 수 있습니다.

지금은 리소스 그룹(**SpeechEchoBotTutorial-ResourceGroup**)에 Speech 리소스가 있는지 확인합니다.

| 이름 | 유형  | 위치 |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service 계획 만들기

다음 단계는 App Service 요금제를 만드는 것입니다. App Service 계획은 실행할 웹앱에 대한 컴퓨팅 리소스 세트를 정의합니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Azure App Service 요금제를 만듭니다</a>.
4. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * **구독** 을 **평가판** 으로 설정합니다(기존 구독을 사용할 수도 있음).
   * **리소스 그룹** 으로 **SpeechEchoBotTutorial-ResourceGroup** 을 선택합니다.
   * 리소스 **이름** 을 지정합니다. 권장하는 이름은 **SpeechEchoBotTutorial-AppServicePlan** 입니다.
   * **운영 체제** 로 **Windows** 를 선택합니다.
   * **지역** 으로 **미국 서부** 를 선택합니다.
   * **가격 책정 계층** 으로 **Standard S1** 이 선택되었는지 확인합니다. 이것이 기본값입니다. 기본값과 다른 경우 위에 설명된 대로 **운영 체제** 를 **Windows** 로 설정합니다.
5. **검토 + 만들기** 를 클릭합니다. **유효성 검사 통과** 라는 배너가 표시됩니다.
6. **만들기** 를 클릭합니다. 리소스 그룹을 만드는 데 몇 분 정도 걸릴 수 있습니다.

지금은 리소스 그룹(**SpeechEchoBotTutorial-ResourceGroup**)에 다음 두 가지 리소스가 있는지 확인합니다.

| 이름 | 유형  | 위치 |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

## <a name="build-an-echo-bot"></a>Echo Bot 빌드

몇 가지 리소스를 만들었으므로, 이제 봇을 빌드하겠습니다. Echo Bot 샘플로 시작하겠습니다. 이 봇은 그 이름에서 알 수 있듯이 사용자가 텍스트를 입력하면 그에 대한 응답으로 텍스트를 반복합니다. 샘플 코드는 변경하지 않고 그대로 사용할 수 있습니다. 샘플 코드는 Azure에 봇을 배포한 후에 연결할 Direct Line Speech 채널에서 작동하도록 구성되었습니다.

> [!NOTE]
> 후속 지침과 Echo Bot에 대한 추가 정보는 [GitHub의 샘플 README](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)에서 얻을 수 있습니다.

### <a name="run-the-bot-sample-on-your-machine"></a>머신에서 봇 샘플 실행

1. 다음과 같이 샘플 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Visual Studio를 실행합니다.
3. 도구 모음에서 **파일** > **열기** > **프로젝트/솔루션** 을 선택하고 Echo Bot 프로젝트 솔루션을 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 프로젝트가 로드되면 <kbd>F5</kbd> 키를 눌러 프로젝트를 빌드하고 실행합니다.
5. 브라우저가 시작되고 다음과 비슷한 화면이 표시됩니다.
    > [!div class="mx-imgBorder"]
    > [![봇이 준비되었다는 메시지가 표시된 EchoBot 페이지를 보여주는 스크린샷](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "localhost에서 실행되는 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Bot Framework Emulator를 사용하여 봇 샘플 테스트

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator)는 봇 개발자가 로컬로(또는 터널을 통해 원격으로) 봇을 테스트하고 디버그할 수 있는 데스크톱 앱입니다. 이 에뮬레이터는 사용자가 입력한 텍스트(음성 아님)를 입력으로 허용합니다. 이 봇은 텍스트로도 응답합니다. 다음 단계에 따라 Bot Framework Emulator를 사용하여 로컬로 실행되는 Echo Bot을 텍스트 입력 및 텍스트 출력으로 테스트합니다. Azure에 봇을 배포한 후, 음성 입력 및 음성 출력을 사용하여 봇을 테스트합니다.

1. [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 버전 4.3.0 이상을 설치합니다.
2. Bot Framework Emulator를 시작하고 봇을 엽니다.
   * **파일** -> **봇 열기**
3. 다음가 같이 봇의 URL을 입력합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

   ```
   http://localhost:3978/api/messages
   ```
   그리고 "연결"을 누릅니다.
4. 봇이 "Hello and welcome!" 메시지로 인사를 합니다. 아무 문자 메시지를 입력하고 봇이 응답하는지 확인합니다.
5. Echo Bot 인스턴스와 나누는 통신은 다음과 같은 모습입니다. [![Bot Framework Emulator를 보여주는 스크린샷](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework Emulator")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Azure App Service에 봇 배포

다음 단계는 Echo Bot을 Azure에 배포하는 것입니다. 몇 가지 방법으로 봇을 배포할 수 있지만, 이 자습서에서는 Visual Studio에서 직접 게시하는 방법을 집중적으로 설명합니다.

> [!NOTE]
> [Azure CLI](/azure/bot-service/bot-builder-deploy-az-cli) 및 [배포 템플릿](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)을 사용하여 봇을 배포할 수도 있습니다.

> [!NOTE]
> 다음 단계를 수행할 때 **게시...** 가 표시되지 않는 경우 Visual Studio 설치 관리자를 사용하여 **ASP.NET 및 웹 개발** 워크로드를 추가합니다.

1. Visual Studio에서, Direct Line Speech 채널에 사용하도록 구성한 Echo Bot를 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. **솔루션 탐색기** 에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.
1. **게시** 라는 제목의 새 창이 열립니다.
1. **Azure** 를 선택하고, **다음** 을 클릭하고, **Azure App Service(Windows)** 를 선택하고, **다음** 을 클릭하고, 녹색 더하기 기호를 클릭하여 **새 Azure App Service 만들기...** 를 선택합니다.
1. **App Service(Windows)** 창이 나타나면 다음을 수행합니다.
   * **계정 추가** 를 클릭하고 Azure 계정 자격 증명으로 로그인합니다. 이미 로그인한 경우 드롭다운 목록에서 원하는 계정을 선택합니다.
   * **이름** 에는 전역적으로 고유한 봇 이름을 입력해야 합니다. 이 이름은 고유한 봇 URL을 만드는 데 사용됩니다. 날짜 및 시간을 포함하는 기본값이 채워집니다(예: "EchoBot20190805125647"). 이 자습서에서는 기본 이름을 사용해도 됩니다.
   * **구독** 을 **평가판** 으로 설정합니다.
   * **리소스 그룹** 으로 **SpeechEchoBotTutorial-ResourceGroup** 을 선택합니다.
   * **호스팅 계획** 으로 **SpeechEchoBotTutorial-AppServicePlan** 을 선택합니다.
1. **만들기** 를 클릭합니다. 마지막 화면에서 **마침** 을 클릭합니다.
1. 게시 화면 오른쪽에서 **게시** 를 클릭합니다. Visual Studio가 Azure에 봇을 배포합니다.
1. Visual Studio의 출력 창에 다음과 같은 성공 메시지가 표시됩니다.

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 기본 브라우저가 열리고 "Your bot is ready!"라는 페이지가 표시됩니다.
1. 지금은 Azure Portal에서 리소스 그룹 **SpeechEchoBotTutorial-ResourceGroup** 을 확인하고 다음 세 가지 리소스가 있는지 확인합니다.

| 이름 | 유형  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

## <a name="enable-web-sockets"></a>웹 소켓 사용

봇이 웹 소켓을 사용하여 Direct Line Speech 채널과 통신할 수 있도록 구성을 약간 변경해야 합니다. 다음 단계에 따라 웹 소켓을 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 App Service를 클릭합니다. 리소스 이름은 **EchoBot20190805125647**(고유한 앱 이름)과 비슷해야 합니다.
2. 왼쪽 탐색 창의 **설정** 에서 **구성** 을 클릭합니다.
3. **일반 설정** 탭을 선택합니다.
4. **웹 소켓** 토글을 찾아서 **켜기** 로 설정합니다.
5. **저장** 을 클릭합니다.

> [!TIP]
> Azure App Service 페이지 맨 위에 있는 컨트롤을 사용하여 서비스를 중지하거나 다시 시작할 수 있습니다. 이 기능은 문제를 해결할 때 유용하게 사용할 수 있습니다.

## <a name="create-a-channel-registration"></a>채널 등록 만들기

봇을 호스트할 Azure App Service를 만들었으므로, 다음 단계는 **봇 채널 등록** 을 만드는 것입니다. Direct Line Speech 채널을 포함하여 Bot Framework 채널에 봇을 등록하려면 먼저 채널 등록을 만들어야 합니다. 봇이 채널을 어떻게 사용하는지 자세히 알아보려면 [채널에 봇 연결](/azure/bot-service/bot-service-manage-channels)을 참조하세요.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Azure Bot 채널 등록을 만듭니다</a>.
2. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * **봇 핸들** 에 **SpeechEchoBotTutorial-BotRegistration-####** 을 입력하고 **####** 을 원하는 숫자로 바꿉니다. 봇 핸들은 전역적으로 고유해야 합니다. 봇 핸들을 입력하면 _요청한 봇 ID를 사용할 수 없습니다_ 라는 오류 메시지가 표시되는 경우 다른 숫자를 선택합니다. 아래 예제에서는 8726을 사용했습니다.
   * **구독** 으로 **평가판** 을 선택합니다.
   * **리소스 그룹** 으로 **SpeechEchoBotTutorial-ResourceGroup** 을 선택합니다.
   * **위치** 로 **미국 서부** 를 선택합니다.
     * **가격 책정 계층** 으로 **F0** 을 선택합니다.
     * **메시지 엔드포인트** 에는 웹앱의 URL 끝에 `/api/messages`를 붙여서 입력합니다. 예를 들어 전역적으로 고유한 앱 이름이 **EchoBot20190805125647** 인 경우 메시지 엔드포인트는 `https://EchoBot20190805125647.azurewebsites.net/api/messages/`입니다.
     * **Application Insights** 는 **끄기** 로 설정해도 됩니다. 자세한 내용은 [봇 분석](/azure/bot-service/bot-service-manage-analytics)을 참조하세요.
     * **자동 앱 ID 및 암호 만들기** 는 무시합니다.
5. **봇 채널 등록** 블레이드의 맨 아래에서 **만들기** 를 클릭합니다.

지금은 Azure Portal에서 리소스 그룹 **SpeechEchoBotTutorial-ResourceGroup** 을 확인합니다. 적어도 다음 4개 리소스가 표시되어야 합니다.

| 이름 | 유형  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-BotRegistration-8726 | 봇 채널 등록 | 전역 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

> [!IMPORTANT]
> 미국 서부를 선택했더라도 봇 채널 등록 리소스는 전체 지역을 표시합니다. 예상된 동작입니다.

## <a name="optional-test-in-web-chat"></a>선택 사항: 웹 채팅에서 테스트

Azure 봇 채널 등록 페이지의 **봇 관리** 에는 **웹 채팅에 테스트** 옵션이 있습니다. 웹 채팅을 봇에서 인증해야 하므로 이 옵션은 기본적으로 작동하지 않습니다. 배포된 봇을 텍스트 입력을 통해 테스트하려면 다음 단계를 수행합니다. 다음 단계는 선택 사항이며 완료하지 않아도 자습서의 다음 단계를 계속 진행할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **EchoBotTutorial-BotRegistration-####** 리소스를 찾아서 엽니다.
1. **봇 관리** 탐색에서 **설정** 을 선택합니다. **Microsoft 앱 ID** 의 값을 복사합니다.
1. Visual Studio EchoBot 솔루션을 엽니다. 솔루션 탐색기에서 **appsettings.json** 파일을 찾아서 두 번 클릭합니다.
1. JSON 파일의 **MicrosoftAppId** 옆에 있는 빈 문자열을 앞에서 복사한 ID 값으로 바꿉니다.
1. Azure Portal로 돌아가서, **봇 관리** 탐색에서 **설정** 을 선택하고 **Microsoft 앱 ID** 옆에 있는 **(관리)** 를 클릭합니다.
1. **새 클라이언트 암호** 를 클릭합니다. 설명(예: "웹 채팅")을 추가하고 **추가** 를 클릭합니다. 새 비밀을 복사합니다.
1. JSON 파일의 **MicrosoftAppPassword** 옆에 있는 빈 문자열을 앞에서 복사한 ID 비밀 값으로 바꿉니다.
1. JSON 파일을 저장합니다. 다음과 같이 표시됩니다.
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. Visual Studio 솔루션 탐색기에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택한 다음, **게시** 단추를 클릭하여 앱을 다시 게시합니다.
10. 이제 웹 채팅에서 봇을 테스트할 준비가 완료되었습니다.

## <a name="register-the-direct-line-speech-channel"></a>Direct Line Speech 채널 등록

이제 Direct Line Speech 채널에 봇을 등록할 차례입니다. 이 채널은 봇과 Speech SDK를 사용하여 컴파일한 클라이언트 앱 사이에 연결을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **SpeechEchoBotTutorial-BotRegistration-####** 리소스를 찾아서 엽니다.
1. **봇 관리** 탐색에서 **채널** 을 선택합니다.
   * **채널 더 보기** 에서 **Direct Line Speech** 를 클릭합니다.
   * **Direct Line Speech 구성** 이라는 페이지의 텍스트를 검토한 다음, **인식 서비스 계정** 드롭다운 메뉴를 확장합니다.
   * 메뉴에서 이전에 만든 Speech 리소스(예: **SpeechEchoBotTutorial-Speech**)를 선택하여 봇을 Speech 구독 키에 연결합니다.
   * 나머지 선택 사항 필드는 무시합니다.
   * **저장** 을 클릭합니다.

1. **봇 관리** 탐색에서 **설정** 을 클릭합니다.
   * **스트리밍 엔드포인트 사용** 확인란을 선택합니다. 이 옵션은 봇과 Direct Line Speech 채널 사이에 웹 소켓 기반의 통신 프로토콜을 만드는 데 필요합니다.
   * **저장** 을 클릭합니다.

> [!TIP]
> 자세한 내용은 [Direct Line Speech에 봇 연결](/azure/bot-service/bot-service-channel-connect-directlinespeech)을 참조하세요. 이 페이지에는 추가 정보 및 알려진 문제가 포함되어 있습니다.

## <a name="run-the-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트 실행

이 단계에서는 Windows 음성 도우미 클라이언트를 실행합니다. 이 클라이언트는 [Speech SDK](./speech-sdk.md)를 통해 Direct Line Speech를 사용하는 봇과의 통신을 관리하는 C#의 WPF(Windows Presentation Foundation) 앱입니다. 사용자 지정 클라이언트 앱을 작성하기 전에 이 클라이언트를 사용하여 봇과 상호 작용하고 봇을 테스트합니다. 오픈 소스이므로 실행 파일을 다운로드하여 실행할 수 있습니다. 또는 직접 빌드해도 됩니다.

Windows 음성 도우미 클라이언트는 봇에 대한 연결을 구성하고, 텍스트 대화를 보고, JSON 형식의 Bot Framework 활동을 보고, 적응형 카드를 표시할 수 있는 간단한 UI를 제공합니다. 또한 사용자 지정 키워드를 사용할 수 있습니다. 이 클라이언트를 사용하여 봇과 이야기하고 음성 응답을 받을 것입니다.

> [!NOTE]
> 지금은 마이크와 스피커를 사용하도록 설정했으며 정상적으로 작동하는지 확인합니다.

1. [Windows 음성 도우미 클라이언트](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)에 대한 GitHub 리포지토리로 이동합니다.
1. 제공되는 지침에 따라 다음 중 하나를 수행합니다.
   * 실행할 ZIP 패키지에 미리 빌드된 실행 파일을 다운로드합니다.
   * 리포지토리를 복제하고 프로젝트를 빌드하여 실행 파일을 직접 빌드합니다.

1. GitHub 리포지토리의 지침에 따라 `VoiceAssistantClient.exe` 클라이언트 애플리케이션을 시작하고 봇에 연결하도록 구성합니다.
1. **다시 연결** 을 클릭하고 **새 대화가 시작되었습니다. 대화를 입력하거나 마이크 단추를 누르세요** 라는 메시지가 표시되는지 확인합니다.
1. 테스트를 수행합니다. 마이크 단추를 클릭하고 단어 몇 개를 영어로 말합니다. 말하는 내용을 인식하여 텍스트가 표시됩니다. 사용자의 말이 끝나면 봇이 고유한 음성으로 대답합니다. 봇은 "echo"라고 말한 후 인식한 단어를 말합니다.
1. 텍스트를 사용하여 봇과 통신할 수도 있습니다. 아래쪽 표시줄에 텍스트를 입력하면 됩니다. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트의 오류 문제 해결

기본 앱 창에 오류 메시지가 표시되면 다음 표를 사용하여 오류를 파악하고 해결합니다.

| 오류 | 어떻게 해야 하나요? |
|-------|----------------------|
|오류(AuthenticationFailure): 인증 오류(401)로 인해 WebSocket 업그레이드가 실패했습니다. 올바른 구독 키(또는 권한 부여 토큰) 및 지역 이름을 확인하세요.| 앱의 [설정] 페이지에서 Speech 구독 키와 지역을 올바르게 입력했는지 확인합니다.<br>Speech 키와 키 지역을 올바르게 입력했는지 확인합니다. |
|오류(ConnectionFailure): 원격 호스트가 연결을 종료했습니다. 오류 코드: 1011. 오류 세부 정보: 봇에 연결할 수 없어 메시지를 보낼 수 없습니다. | ["스트리밍 엔드포인트 사용"](#register-the-direct-line-speech-channel) 확인란을 선택했으며 [**웹 소켓** 을 [켜기]로 전환](#enable-web-sockets)했는지 확인합니다.<br>Azure App Service가 실행 중인지 확인합니다. 실행 중이면 App Service를 다시 시작합니다.|
|오류(ConnectionFailure): 원격 호스트가 연결을 종료했습니다. 오류 코드: 1002. 오류 세부 정보: 상태 코드 ‘101’을 예상했지만 서버에서 상태 코드 ‘503’을 반환했습니다. | ["스트리밍 엔드포인트 사용"](#register-the-direct-line-speech-channel) 확인란을 선택했으며 [**웹 소켓** 을 [켜기]로 전환](#enable-web-sockets)했는지 확인합니다.<br>Azure App Service가 실행 중인지 확인합니다. 실행 중이면 App Service를 다시 시작합니다.|
|오류(ConnectionFailure): 원격 호스트가 연결을 종료했습니다. 오류 코드: 1011. 오류 세부 정보: 응답 상태 코드가 성공: 500(InternalServerError)을 나타내지 않습니다.| 봇이 출력 활동 [말하기](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 필드에서 인공신경망 음성을 지정했지만, Speech 구독 키와 연결된 Azure 지역에서 인공신경망 음성을 지원하지 않습니다. [인공신경망 및 표준 음성](./regions.md#neural-and-standard-voices)을 참조하세요.|

아래 표의 문제가 해결되지 않으면 [음성 도우미: 질문과 대답](faq-voice-assistants.yml)을 참조하세요. 이 자습서의 모든 단계를 수행한 후에도 문제가 해결되지 않으면 [음성 도우미 GitHub 페이지](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues)에 새 문제를 입력하세요.

#### <a name="a-note-on-connection-time-out"></a>연결 제한 시간에 대한 참고 사항

봇에 연결되어 있고 5분 동안 아무 것도 하지 않으면 서비스가 클라이언트와 봇의 웹 소켓 연결을 자동으로 종료합니다. 이것은 의도적인 것입니다. *"활성 연결 시간이 초과되었으며 요청 시 다시 연결할 수 있습니다"* 라는 메시지가 아래쪽 표시줄에 표시됩니다. "다시 연결" 단추를 누르지 않아도 됩니다. 마이크 단추를 누르고 말을 하거나 문자 메시지를 입력하거나 키워드를 말하면(사용하도록 설정된 경우) 됩니다. 그러면 연결이 자동으로 다시 설정됩니다.  
### <a name="view-bot-activities"></a>봇 활동 보기

모든 봇은 **활동** 메시지를 보내고 받습니다. Windows 음성 도우미 클라이언트의 **활동 로그** 창에는 클라이언트가 봇으로부터 받은 각 활동과 함께 타임스탬프가 찍힌 로그가 표시됩니다. 클라이언트에서 [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) 메서드를 사용하여 봇으로 보낸 활동도 볼 수 있습니다. 로그 항목을 선택하면 관련 활동의 세부 정보가 JSON으로 표시됩니다.

다음은 클라이언트가 받은 활동의 샘플 json입니다.

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

JSON 출력에 반환되는 항목에 대한 자세한 내용은 [활동의 필드](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)를 참조하세요. 이 자습서의 목적을 위해 [텍스트](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) 및 [말하기](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)에 집중하면 됩니다.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Speech SDK 호출에 대한 클라이언트 소스 코드 보기

Windows 음성 도우미 클라이언트는 Speech SDK를 포함하고 있는 NuGet 패키지 [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)를 사용합니다. 샘플 코드 검토를 시작하기에 좋은 위치는 [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 파일의 InitSpeechConnector() 메서드입니다. 이 메서드는 다음과 같은 두 가지 Speech SDK 개체를 만듭니다.
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) - 구성 설정(예: Speech 구독 키, 키 지역)에 사용됩니다.
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) - 인식된 음성 및 봇 응답을 처리하기 위한 채널 연결과 클라이언트 구독 이벤트를 관리합니다.

## <a name="add-custom-keyword-activation"></a>사용자 지정 키워드 활성화 추가

Speech SDK는 사용자 지정 키워드 활성화를 지원합니다. Microsoft 도우미의 "Hey Cortana"와 마찬가지로, 사용자가 선택한 키워드를 지속적으로 수신 대기하는 앱을 작성할 수 있습니다. 키워드는 한 단어도 되고 여러 단어로 구성된 구도 됩니다.

> [!NOTE]
> *키워드* 라는 용어는 종종 *절전 모드 해제 단어* 와 같은 의미로 사용되며, Microsoft 설명서에서 두 단어가 모두 사용되는 것을 볼 수 있습니다.

키워드 검색은 클라이언트 앱에서 수행됩니다. 키워드를 사용하는 경우 키워드가 검색되면 오디오가 Direct Line Speech 채널에만 스트리밍됩니다. Direct Line Speech 채널에는 *KWV(키워드 확인)* 라는 구성 요소가 있는데, 이 구성 요소는 사용자가 선택한 키워드가 오디오 스트림의 시작 부분에 있는지 확인하는 클라우드의 좀 더 복잡한 처리를 담당합니다. 키워드 확인이 성공하면 이 채널이 봇과 통신합니다.

다음 단계에 따라 키워드 모델을 만들고, 이 모델을 사용하도록 Windows 음성 도우미 클라이언트를 구성하고, 마지막으로 봇에서 테스트합니다.

1. 다음 지침에 따라 [Speech Service를 사용하여 사용자 지정 키워드를 만듭니다](./custom-keyword-basics.md).
2. 이전 단계에서 다운로드한 모델 파일의 압축을 풉니다. 키워드가 이름으로 지정되어 있을 것입니다. 우리가 찾는 파일은 `kws.table`입니다.
3. Windows 음성 도우미 클라이언트에서 **설정** 메뉴(오른쪽 위에 있는 기어 아이콘)를 찾습니다. **모델 파일 경로** 를 찾아서 2단계의 `kws.table` 파일에 대한 전체 경로 이름을 입력합니다.
4. **사용** 확인란을 선택합니다. 확인란 옆에 "다음 연결 시 키워드를 수신 대기합니다"라는 메시지가 표시됩니다. 잘못된 파일 또는 잘못된 경로를 입력한 경우 오류 메시지가 표시됩니다.
5. Speech **구독 키**, **구독 키 영역** 을 입력한 다음, **확인** 을 클릭하여 **설정** 메뉴를 닫습니다.
6. **다시 연결** 을 클릭합니다. "새 대화가 시작되었습니다. 텍스트를 입력하거나 마이크 단추를 누르거나 키워드를 말하세요"라는 메시지가 표시됩니다. 이제 앱이 지속적으로 수신 대기 중입니다.
7. 키워드로 시작하는 아무 문구나 말해 보세요. 예: " **{본인의 키워드}** , what time is it(지금 몇 시야)?" 키워드를 말한 후 일시 중지할 필요가 없습니다. 모두 마치면 다음 두 가지 작업이 수행됩니다.
   * 말한 내용의 대화 내용 기록이 표시됩니다.
   * 잠시 후 봇이 대답합니다.
8. 봇이 지원하는 다음 세 가지 입력 형식을 계속 실험합니다.
   * 아래쪽 표시줄에 텍스트 입력
   * 마이크 아이콘을 누르고 말하기
   * 키워드로 시작하는 문구 말하기

### <a name="view-the-source-code-that-enables-keyword"></a>키워드를 사용하는 소스 코드 보기

Windows 음성 도우미 클라이언트 소스 코드에서 다음 파일을 살펴보면서 키워드 검색을 지원하기 위해 사용되는 코드를 검토합니다.

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) 파일에는 디스크의 로컬 파일에 있는 모델을 인스턴스화하는 데 사용되는 Speech SDK 메서드 [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-)에 대한 호출이 포함됩니다.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 파일에는 지속적인 키워드 검색을 활성화하는 Speech SDK 메서드 [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)에 대한 호출이 포함됩니다.

## <a name="optional-change-the-language-and-bot-voice"></a>(선택 사항) 언어 및 봇 음성 변경

사용자가 만든 봇은 기본 미국 영어 텍스트 음성 변환 음성을 사용하여 영어로 수신 대기하고 응답합니다. 하지만 반드시 영어 또는 기본 음성을 사용해야 하는 제한이 있는 것은 아닙니다. 이 섹션에서는 봇이 수신 대기하고 응답하는 언어를 변경하는 방법을 알아봅니다. 해당 언어에 사용할 다른 음성을 선택하는 방법도 알아봅니다.

### <a name="change-the-language"></a>언어 변경

[음성 텍스트 변환](language-support.md#speech-to-text) 표에 언급된 언어 중 하나를 선택할 수 있습니다. 아래 예제에서는 언어를 독일어로 변경합니다.

1. Windows 음성 도우미 클라이언트 앱을 열고, [설정] 단추(오른쪽 위에 있는 기어 아이콘)를 클릭하고, 언어 필드에 `de-de`([음성 텍스트 변환](language-support.md#speech-to-text) 표에서 언급한 로캘 값)를 입력합니다. 이렇게 하면 음성 언어가 설정되고 기본 `en-us`가 재정의됩니다. 또한 봇 응답에 기본 독일어 음성을 사용하도록 Direct Line Speech에 지시합니다.
2. [설정] 페이지를 닫고 [다시 연결] 단추를 클릭하여 Echo Bot에 대한 새 연결을 설정합니다.
3. 마이크 단추를 클릭하고 독일어로 구를 말합니다. 인식된 텍스트가 표시되고 Echo Bot이 기본 독일 음성으로 대답하는 것을 볼 수 있습니다.

### <a name="change-the-default-bot-voice"></a>기본 봇 음성 변경

봇이 단순 텍스트가 아닌 SSML([Speech Synthesis Markup Language](speech-synthesis-markup.md)) 형식으로 응답을 지정하는 경우 텍스트 음성 변환 음성 및 발음 제어를 선택할 수 있습니다. Echo Bot은 SSML을 사용하지 않지만, 그렇게 하도록 코드를 쉽게 수정할 수 있습니다. 아래 예제에서는 기본 여성 음성 대신 독일어 음성 Stefan Apollo(남성 음성)를 사용하도록 Echo Bot 응답에 SSML을 추가합니다. 해당 언어에 대해 지원되는 [표준 음성](language-support.md#standard-voices) 및 [인공신경망 음성](language-support.md#neural-voices) 목록을 참조하세요.

1. 먼저 `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`를 엽니다.
2. 다음 두 줄을 찾습니다.
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 다음과 같이 바꿉니다.
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Visual Studio에서 솔루션을 빌드하고 빌드 오류를 수정합니다.

'MessageFactory.Text' 메서드의 두 번째 인수는 본문 응답의 [활동 말하기 필드](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)를 설정합니다. 위와 같이 변경한 결과, 기본 음성이 아닌 독일어 음성을 지정하기 위해 단순 텍스트에서 SSML로 바뀌었습니다.

### <a name="redeploy-your-bot"></a>봇 다시 배포

봇을 필요한 대로 변경했으므로, 다음 단계는 봇을 Azure App Service에 다시 게시하고 사용해 보는 것입니다.

1. 솔루션 탐색기 창에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.
2. 이전 배포 구성이 이미 기본값으로 로드되었습니다. **EchoBot20190805125647 - 웹 배포** 옆에 있는 **게시** 를 클릭합니다.
3. **게시 성공** 메시지가 Visual Studio 출력 창에 표시되고, 웹 페이지가 "Your bot is ready!"라는 메시지와 함께 시작됩니다.
4. Windows 음성 도우미 클라이언트 앱을 열고, 설정 단추(오른쪽 위에 있는 기어 아이콘)를 클릭하고, [언어] 필드에서 여전히 `de-de`인지 확인합니다.
5. [Windows 음성 도우미 클라이언트 실행](#run-the-windows-voice-assistant-client)의 지침에 따라 새로 배포한 봇에 다시 연결하고, 새 언어로 말하고 봇이 해당 언어의 새 음성으로 대답하는 것을 들어 봅니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 배포한 Echo Bot 봇을 더 이상 사용하지 않으려는 경우 간단하게 Azure 리소스 그룹 **SpeechEchoBotTutorial-ResourceGroup** 을 삭제하면 이 리소스 그룹 및 관련 Azure 리소스까지 함께 제거할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 **Azure 서비스** 탐색에서 **리소스 그룹** 을 클릭합니다.
2. **SpeechEchoBotTutorial-ResourceGroup** 이라는 리소스 그룹을 찾습니다. 점 3개(...)를 클릭합니다.
3. **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK를 사용하여 고유의 클라이언트 앱 빌드](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>참고 항목

* 봇 응답 시간이 향상되도록 [가까운 Azure 지역](https://azure.microsoft.com/global-infrastructure/locations/)에 배포
* [고품질 인공신경망 TTS 음성을 지원하는 Azure 지역](./regions.md#neural-and-standard-voices)에 배포
* Direct Line Speech 채널과 관련된 가격 책정:
  * [Bot Service 가격 책정](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 사용자 고유의 음성 지원 봇 빌드 및 배포:
  * [Bot Framework 봇](https://dev.botframework.com/)을 빌드합니다. [Direct Line Speech 채널](/azure/bot-service/bot-service-channel-connect-directlinespeech)에 등록하고 [음성에 맞게 봇을 사용자 지정](/azure/bot-service/directline-speech-bot)합니다.
  * 기존 [Bot Framework 솔루션](https://microsoft.github.io/botframework-solutions/index)살펴보기: [가상 도우미](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/)를 빌드하고 [Direct Line Speech로 확장](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
