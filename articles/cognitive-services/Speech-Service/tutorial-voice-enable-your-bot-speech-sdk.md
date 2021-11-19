---
title: '자습서: 음성 SDK를 사용 하 여 봇 사용-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 echo bot을 만들고 봇에 대해 이야기할 수 있는 클라이언트 앱을 구성 하 고 응답 하는 것을 환영 합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: eur
ms.custom: devx-track-csharp
ms.openlocfilehash: 5da9eb3c8ff411bcbc3ef7991702d71a6522c649
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808702"
---
# <a name="tutorial-voice-enable-your-bot-by-using-the-speech-sdk"></a>자습서: 음성 SDK를 사용 하 여 봇 사용

Azure Cognitive Services에서 음성 서비스를 사용 하 여 채팅 봇을 음성으로 사용 하도록 설정할 수 있습니다.

이 자습서에서는 Microsoft Bot Framework를 사용 하 여 말한 내용을 반복 하는 봇을 만듭니다. Azure에 봇을 배포 하 고 Bot Framework Direct Line Speech channel을 사용 하 여 등록 합니다.
그런 다음 봇에 대해 이야기 하 고 다시 말할 수 있는 Windows에 대 한 샘플 클라이언트 앱을 구성 합니다.

이 자습서는 Azure, Bot Framework 봇, Direct Line Speech 또는 Speech SDK를 처음 접하고, 제한된 코딩을 통해 실제 작동하는 시스템을 신속하게 구축하려는 개발자를 위해 작성되었습니다. 이러한 서비스에 대 한 경험과 지식이 필요 하지 않습니다.

이 자습서에서 수행 하는 음성 지원 채팅 봇은 다음 단계를 따릅니다.

1. 샘플 클라이언트 응용 프로그램은 직접 선 음성 채널과 echo 봇에 연결 하도록 구성 됩니다.
1. 사용자가 단추를 누르면 마이크에서 음성 오디오가 스트리밍됩니다. 또는 오디오는 사용자 지정 키워드가 사용 될 때 지속적으로 기록 됩니다.
1. 사용자 지정 키워드를 사용하는 경우 로컬 디바이스에서 키워드 검색이 수행되고 오디오 스트리밍이 클라우드로 제한됩니다.
1. 샘플 클라이언트 응용 프로그램은 Speech SDK를 사용 하 여 직접 선 음성 채널 및 스트림 오디오에 연결 합니다.
1. 필요에 따라 서비스에서 더 높은 정확도 키워드 확인이 수행 됩니다.
1. 오디오가 음성 인식 서비스에 전달되고 텍스트로 기록됩니다.
1. 인식 된 텍스트는 Bot Framework 활동으로 echo 봇에 전달 됩니다.
1. 응답 텍스트는 텍스트 음성 변환 서비스에서 오디오로 전환 되 고 재생을 위해 클라이언트 응용 프로그램으로 스트리밍됩니다.

<!-- svg src in User Story 1754106 -->
![직접 선 음성 채널의 흐름을 보여 주는 다이어그램입니다.](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "음성 채널 흐름")

> [!NOTE]
> 이 자습서의 단계에는 유료 서비스가 필요 하지 않습니다. 새 Azure 사용자는 무료 Azure 평가판 구독의 크레딧을 사용 하 고 음성 서비스의 무료 계층을 사용 하 여이 자습서를 완료할 수 있습니다.

이 자습서에서는 다음 내용을 다룹니다.
> [!div class="checklist"]
> * 새 Azure 리소스를 만듭니다.
> * Echo bot 샘플을 빌드, 테스트 및 배포 하 여 Azure App Service 합니다.
> * 직접 선 음성 채널을 사용 하 여 봇을 등록 합니다.
> * Windows 음성 도우미 클라이언트를 빌드하고 실행 하 여 echo 봇과 상호 작용 합니다.
> * 사용자 지정 키워드 활성화를 추가 합니다.
> * 인식 된 음성 및 음성 음성의 언어를 변경 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 사항은 다음과 같습니다.

- 작업 중인 마이크와 스피커 (또는 헤드폰)가 있는 Windows 10 PC
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 이상, **ASP.NET 및 웹 개발** 워크 로드가 설치 되어 있어야 합니다.
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) 이상
- Azure 계정. [무료로 등록](https://azure.microsoft.com/free/cognitive-services/)하세요.
- [GitHub](https://github.com/) 계정
- [Windows용 Git](https://git-scm.com/download/win).

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 자습서에서 만들 클라이언트 앱은 몇 가지 Azure 서비스를 사용합니다. Bot의 응답에 대 한 왕복 시간을 줄이려면 이러한 서비스가 동일한 Azure 지역에 있는지 확인 해야 합니다. 

이 섹션에서는 미국 서 부 지역에 리소스 그룹을 만드는 과정을 안내 합니다. Bot Framework, 직접 선 음성 채널 및 음성 서비스에 대 한 개별 리소스를 만들 때이 리소스 그룹을 사용 합니다.

1. [리소스 그룹을 만들기 위한 Azure Portal 페이지로](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup)이동 합니다.
1. 다음 정보를 지정합니다.
   * **구독** 을 **무료 평가판** 으로 설정 합니다. (기존 구독을 사용할 수도 있습니다.)
   * **리소스 그룹** 의 이름을 입력 합니다. 권장하는 이름은 **SpeechEchoBotTutorial-ResourceGroup** 입니다.
   * **지역** 드롭다운 메뉴에서 **미국 서 부** 를 선택 합니다.
1. **검토 및 만들기** 를 선택합니다. **유효성 검사 통과** 를 읽는 배너가 표시 됩니다.
1. **만들기** 를 선택합니다. 리소스 그룹을 만드는 데 몇 분 정도 걸릴 수 있습니다.
1. 이 자습서의 뒷부분에서 만들 리소스와 마찬가지로, 쉽게 액세스할 수 있도록 이 리소스 그룹을 대시보드에 고정해 두는 것이 좋습니다. 이 리소스 그룹에 고정 하려면 이름 옆의 고정 아이콘을 선택 합니다.

### <a name="choose-an-azure-region"></a>Azure 지역 선택

이 자습서에 다른 지역을 사용 하려는 경우 이러한 요인으로 인해 선택 사항이 제한 될 수 있습니다.

* [지원되는 Azure 지역](regions.md#voice-assistants)을 사용하는지 확인합니다.
* Direct Line Speech 채널은 인공신경망 및 표준 음성을 제공하는 텍스트 음성 변환 서비스를 사용합니다. 신경망은 [이러한 Azure 지역](regions.md#neural-and-standard-voices)에서 사용할 수 있습니다.

지역에 대한 자세한 내용은 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.

## <a name="create-resources"></a>리소스 만들기

지원되는 지역에 리소스 그룹이 있으므로, 다음 단계는 이 자습서에서 사용할 각 서비스에 대한 개별 리소스를 만드는 것입니다.

### <a name="create-a-speech-service-resource"></a>음성 서비스 리소스 만들기

1. [음성 서비스 리소스를 만들기 위한 Azure Portal 페이지로](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)이동 합니다.
1. 다음 정보를 지정합니다.
   * **이름** 에는 리소스 이름으로 **SpeechEchoBotTutorial** 를 권장 합니다.
   * **구독** 의 경우 **무료 평가판** 이 선택 되어 있는지 확인 합니다.
   * **위치** 로 **미국 서부** 를 선택합니다.
   * **가격 책정 계층** 으로 **F0** 을 선택합니다. F0은 평가판 계층입니다.
   * **리소스 그룹** 으로 **SpeechEchoBotTutorial-ResourceGroup** 을 선택합니다.
1. 모든 필수 정보를 입력 한 후 **만들기** 를 선택 합니다. 리소스를 만드는 데 몇 분 정도 걸릴 수 있습니다.
1. 이 자습서의 뒷부분에서는이 서비스에 대 한 구독 키가 필요 합니다. 리소스의 **개요** 영역 ( **키 관리** 아래) 또는 **키** 영역에서 언제 든 지 이러한 키에 액세스할 수 있습니다.

이 시점에서 리소스 그룹 (**SpeechEchoBotTutorial**)에 음성 서비스 리소스가 있는지 확인 합니다.

| Name | 유형  | 위치 |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service 계획 만들기

App Service 계획은 실행할 웹앱에 대한 컴퓨팅 리소스 세트를 정의합니다.

1. [Azure App Service 계획을 만들기 위한 Azure Portal 페이지로](https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate)이동 합니다.
1. 다음 정보를 지정합니다.
   * **구독** 을 **무료 평가판** 으로 설정 합니다. (기존 구독을 사용할 수도 있습니다.)
   * **리소스 그룹** 으로 **SpeechEchoBotTutorial-ResourceGroup** 을 선택합니다.
   * **이름** 으로 **SpeechEchoBotTutorial-appserviceplan** 을 계획의 이름으로 권장 합니다.
   * **운영 체제** 로 **Windows** 를 선택합니다.
   * **지역** 으로 **미국 서부** 를 선택합니다.
   * **가격 책정 계층** 으로 **Standard S1** 이 선택되었는지 확인합니다. 이것이 기본값입니다. 그렇지 않은 경우 **운영 체제** 를 **Windows** 으로 설정 합니다.
1. **검토 및 만들기** 를 선택합니다. **유효성 검사 통과** 를 읽는 배너가 표시 됩니다.
1. **만들기** 를 선택합니다. 리소스를 만드는 데 몇 분 정도 걸릴 수 있습니다.

지금은 리소스 그룹(**SpeechEchoBotTutorial-ResourceGroup**)에 다음 두 가지 리소스가 있는지 확인합니다.

| 이름 | 유형  | 위치 |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

## <a name="build-an-echo-bot"></a>에코 봇 빌드

이제 리소스를 만들었으므로 봇을 빌드 하겠습니다. Echo bot 샘플로 시작할 예정입니다 .이 샘플에서는 이름에서 알 수 있듯이 응답으로 입력 한 텍스트를 에코 합니다. 샘플 코드는 변경하지 않고 그대로 사용할 수 있습니다. Azure에 bot을 배포한 후 연결 하는 직접 선 음성 채널을 사용 하도록 구성 됩니다.

> [!NOTE]
> 다음 지침은 echo bot에 대 한 자세한 정보와 함께 [GitHub의 샘플 추가](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)정보에서 사용할 수 있습니다.

### <a name="run-the-bot-sample-on-your-machine"></a>머신에서 봇 샘플 실행

1. 다음과 같이 샘플 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

1. Visual Studio를 엽니다.
1. 도구 모음에서 **파일**  >  **열기**  >  **Project/solution** 를 선택 합니다. 그런 다음 프로젝트 솔루션을 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 프로젝트가 로드 되 면 <kbd>F5</kbd> 키를 선택 하 여 프로젝트를 빌드하고 실행 합니다.

   열리는 브라우저에서 다음과 같은 화면이 표시 됩니다.

   > [!div class="mx-imgBorder"]
   > [![봇이 준비 되었다는 메시지가 포함 된 EchoBot 페이지를 보여 주는 스크린샷](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "localhost에서 실행되는 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Bot Framework Emulator를 사용하여 봇 샘플 테스트

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator)는 봇 개발자가 로컬로(또는 터널을 통해 원격으로) 봇을 테스트하고 디버그할 수 있는 데스크톱 앱입니다. 에뮬레이터가 입력 된 텍스트 (음성 아님)를 허용 합니다. 이 봇은 텍스트로도 응답합니다. 

텍스트 입력 및 텍스트 출력을 사용 하 여 로컬에서 실행 되 Bot Framework Emulator는 에코 봇을 테스트 하려면 다음 단계를 수행 합니다. Azure에 봇을 배포한 후 음성 입력 및 음성 출력으로 테스트 합니다.

1. [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 버전 4.3.0 이상을 설치 합니다.
1. Bot Framework Emulator를 열고 **파일**  >  **열기 봇** 을 선택 합니다.
1. 다음가 같이 봇의 URL을 입력합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

   ```
   http://localhost:3978/api/messages
   ```
   그런 다음 **연결** 을 선택합니다.
1. 봇에 "Hello 및 환영!"을 사용 하 여 환영 합니다. 반환됩니다. 모든 문자 메시지를 입력 하 고 봇에서 응답이 수신 되는지 확인 합니다.

   Echo 봇과의 통신 교환은 다음과 같이 표시 될 수 있습니다. 스크린샷에는 [ ![Bot Framework Emulator 표시](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework Emulator") 됩니다.](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-azure-app-service"></a>Azure App Service에 봇 배포

다음 단계는 echo bot를 Azure에 배포 하는 것입니다. [Azure CLI](/azure/bot-service/bot-builder-deploy-az-cli) 및 [배포 템플릿을](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)포함 하 여 봇을 배포 하는 몇 가지 방법이 있습니다. 이 자습서에서는 Visual Studio에서 직접 게시 하는 방법을 집중적으로 설명 합니다.

> [!NOTE]
> 다음 단계를 수행할 때 **게시** 가 표시 되지 않으면 Visual Studio 설치 관리자를 사용 하 여 **ASP.NET 및 웹 개발** 워크 로드를 추가 합니다.

1. Visual Studio에서 직접 선 음성 채널과 함께 사용 하도록 구성 된 echo bot를 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 솔루션 탐색기에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **게시** 를 선택 합니다.
1. 열리는 **게시** 창에서 다음을 수행 합니다.
   1. **Azure**  >  **다음** 을 선택 합니다.
   1. **Azure App Service (Windows)**  >  **다음** 을 선택 합니다.
   1. 녹색 더하기 기호를 선택 하 여 **새 Azure App Service 만들기** 를 선택 합니다.
1. **App Service(Windows)** 창이 나타나면 다음을 수행합니다.
   * **계정 추가** 를 선택 하 고 Azure 계정 자격 증명을 사용 하 여 로그인 합니다. 이미 로그인 한 경우 드롭다운 목록에서 계정을 선택 합니다.
   * **이름** 에 대해 봇에 대해 전역적으로 고유한 이름을 입력 합니다. 이 이름은 고유한 봇 URL을 만드는 데 사용됩니다. 
   
     날짜 및 시간이 포함 된 기본 이름이 상자에 표시 됩니다 (예: **EchoBot20190805125647**). 이 자습서에서는 기본 이름을 사용해도 됩니다.
   * **구독** 으로 **평가판** 을 선택합니다.
   * **리소스 그룹** 에 대해 **SpeechEchoBotTutorial-ResourceGroup** 을 선택 합니다.
   * **호스팅 계획** 의 경우 **SpeechEchoBotTutorial-appserviceplan** 을 선택 합니다.
1. **만들기** 를 선택합니다. 최종 마법사 화면에서 **마침** 을 선택 합니다.
1. **게시** 를 선택합니다. Visual Studio가 Azure에 봇을 배포합니다.

   Visual Studio 출력 창에 다음과 같은 성공 메시지가 표시 됩니다.

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

   기본 브라우저가 열리고 "봇이 준비 되었습니다!" 라는 페이지를 표시 합니다.

이 시점에서 Azure Portal 리소스 그룹 (**SpeechEchoBotTutorial-ResourceGroup**)을 확인 합니다. 다음 세 가지 리소스가 포함 되어 있는지 확인 합니다.

| Name | 유형  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

## <a name="enable-web-sockets"></a>웹 소켓 사용

봇에서 웹 소켓을 사용 하 여 직접 선 음성 채널과 통신할 수 있도록 작은 구성 변경을 수행 해야 합니다. 다음 단계에 따라 웹 소켓을 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 App Service 리소스를 선택 합니다. 리소스 이름은 **EchoBot20190805125647** (고유한 앱 이름)과 유사 해야 합니다.
1. 왼쪽 창의 **설정** 아래에서 **구성** 을 선택 합니다.
1. **일반 설정** 탭을 선택합니다.
1. **웹 소켓** 에 대해 **토글을 찾아서 설정 합니다.**
1. **저장** 을 선택합니다.

> [!TIP]
> Azure App Service 페이지 맨 위에 있는 컨트롤을 사용하여 서비스를 중지하거나 다시 시작할 수 있습니다. 이 기능은 문제를 해결할 때 유용 하 게 사용할 수 있습니다.

## <a name="create-a-channel-registration"></a>채널 등록 만들기

이제 봇을 호스트 하는 Azure App Service 리소스를 만들었으므로 다음 단계에서는 채널 등록을 만듭니다. 채널 등록을 만드는 것은 직접 선 음성 채널을 포함 하 여 봇 프레임 워크 채널에 봇을 등록 하기 위한 필수 구성 요소입니다. 봇에서 채널을 사용 하는 방법에 대 한 자세한 내용은 [채널에 봇 커넥트](/azure/bot-service/bot-service-manage-channels)를 참조 하세요.

1. [Azure bot을 만들기 위한 Azure Portal 페이지로](https://ms.portal.azure.com/#create/Microsoft.AzureBot)이동 합니다.
1. 다음 정보를 지정합니다.
   * **봇 핸들** 에 **SpeechEchoBotTutorial-BotRegistration-# #**# #을 입력 합니다. **####** 원하는 수로 대체 합니다. 
   
     > [!NOTE]
     > 봇 핸들은 전역적으로 고유해야 합니다. 하나를 입력하고 "요청된 봇 ID를 사용할 수 없습니다."라는 오류 메시지가 발생하면 다른 숫자를 선택합니다. 다음 예제에서는 **8726을** 사용합니다.
   * **구독** 으로 **평가판** 을 선택합니다.
   * **리소스 그룹** 으로 **SpeechEchoBotTutorial-ResourceGroup** 을 선택합니다.
   * **위치** 로 **미국 서부** 를 선택합니다.
   * **가격 책정 계층** 으로 **F0** 을 선택합니다.
   * **자동 앱 ID 및 암호 만들기** 는 무시합니다.
1. **Azure** Bot 창의 맨 아래에서 **만들기를** 선택합니다.
1. 리소스를 만든 후 Azure Portal **SpeechEchoBotTutorial-BotRegistration-####** 리소스를 엽니다.
1. **설정** 영역에서 **구성을** 선택합니다.
1. **메시징 엔드포인트의** 경우 **/api/messages** 경로가 추가된 웹앱의 URL을 입력합니다. 예를 들어 전역적으로 고유한 앱 이름이 **EchoBot20190805125647인** 경우 메시징 엔드포인트는 `https://EchoBot20190805125647.azurewebsites.net/api/messages/` 입니다.

이 시점에서 Azure Portal 리소스 그룹(**SpeechEchoBotTutorial-ResourceGroup)을** 확인합니다. 적어도 다음 4개 리소스가 표시되어야 합니다.

| 이름 | 유형  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-BotRegistration-8726 | Bot Service | 전역 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

> [!IMPORTANT]
> Azure Bot Service 리소스는 미국 서부를 선택했지만 글로벌 지역을 표시합니다. 예상된 동작입니다.

## <a name="optional-test-in-web-chat"></a>선택 사항: 웹 채팅에서 테스트

**Azure 봇** 페이지에는 설정 **아래에** 웹 채팅 **테스트** 옵션이 있습니다. 웹 채팅이 봇에 대해 인증해야 하므로 기본적으로 봇에서 작동하지 않습니다. 

텍스트 입력을 사용하여 배포된 봇을 테스트하려면 다음 단계를 사용합니다. 이러한 단계는 선택 사항이며 자습서를 계속 진행하는 데 필요하지 않습니다. 

1. [Azure Portal](https://portal.azure.com) **EchoBotTutorial-BotRegistration-####** 리소스를 찾아 엽니다.
1. **설정** 영역에서 **구성을** 선택합니다. Microsoft 앱 **ID** 아래에 값을 복사합니다.
1. Visual Studio EchoBot 솔루션을 엽니다. 솔루션 탐색기 **appsettings.json 을** 찾아 두 번 클릭합니다.
1. JSON 파일의 **MicrosoftAppId** 옆에 있는 빈 문자열을 복사된 ID 값으로 대체합니다.
1. Azure Portal로 돌아갑니다. **설정** 영역에서 **구성을** 선택합니다. 그런 **다음, Microsoft 앱 ID** 옆에 있는 **관리를** 선택합니다.
1. **새 클라이언트 비밀** 을 선택합니다. 설명(예: 웹 **채팅)을** 추가하고 **추가를** 선택합니다. 새 비밀을 복사합니다.
1. JSON 파일의 **MicrosoftAppPassword** 옆에 있는 빈 문자열을 복사한 비밀 값으로 대체합니다.
1. JSON 파일을 저장합니다. 다음 코드와 같이 표시됩니다.

   ```json
   {
     "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
     "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
   }
   ```

1. 앱을 다시 게시합니다. Visual Studio 솔루션 탐색기 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시를** 선택한 **다음, 게시** 단추를 선택합니다.

## <a name="register-the-direct-line-speech-channel"></a>Direct Line Speech 채널 등록

이제 Direct Line Speech 채널에 봇을 등록할 차례입니다. 이 채널은 봇과 Speech SDK를 사용하여 컴파일한 클라이언트 앱 사이에 연결을 만듭니다.

1. [Azure Portal](https://portal.azure.com) **SpeechEchoBotTutorial-BotRegistration-####** 리소스를 찾아 엽니다.
1. **설정** 영역에서 채널을 선택한 후 다음 단계를 **수행합니다.**
   1. **추가 채널 아래에서** **Direct Line Speech** 선택합니다.
   1. **Direct Line Speech 구성** 페이지에서 텍스트를 검토한 다음, Cognitive Service **계정** 드롭다운 메뉴를 확장합니다.
   1. 메뉴에서 이전에 만든 Speech Service 리소스(예: **SpeechEchoBotTutorial-Speech)를** 선택하여 봇을 구독 키와 연결합니다.
   1. 나머지 선택 사항 필드는 무시합니다.
   1. **저장** 을 선택합니다.

1. **설정** 영역에서 **구성을** 선택한 후 다음 단계를 수행합니다.
   1. 스트리밍 **엔드포인트 사용 확인란을** 선택합니다. 이 단계는 봇과 Direct Line Speech 채널 간에 웹 소켓을 기반으로 하는 통신 프로토콜을 만드는 데 필요합니다.
   1. **저장** 을 선택합니다.

자세한 내용은 [봇을 Direct Line Speech 커넥트](/azure/bot-service/bot-service-channel-connect-directlinespeech)참조하세요. 

## <a name="run-the-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트 실행

Windows Voice Assistant 클라이언트는 [Speech SDK를](./speech-sdk.md) 사용하여 Direct Line Speech 채널을 통해 봇과의 통신을 관리하는 C#의 WPF(Windows Presentation Foundation) 앱입니다. 사용자 지정 클라이언트 앱을 작성하기 전에 이 클라이언트를 사용하여 봇과 상호 작용하고 봇을 테스트합니다. 오픈 소스이므로 실행 파일을 다운로드하여 실행하거나 직접 빌드할 수 있습니다.

Windows 음성 도우미 클라이언트는 봇에 대한 연결을 구성하고, 텍스트 대화를 보고, JSON 형식의 Bot Framework 활동을 보고, 적응형 카드를 표시할 수 있는 간단한 UI를 제공합니다. 또한 사용자 지정 키워드를 사용할 수 있습니다. 이 클라이언트를 사용하여 봇과 이야기하고 음성 응답을 받을 것입니다.

> [!NOTE]
> 지금은 마이크와 스피커를 사용하도록 설정했으며 정상적으로 작동하는지 확인합니다.

1. [Windows Voice Assistant 클라이언트의 GitHub 리포지토리로](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)이동합니다.
1. 제공된 지침에 따라 다음 중 하나를 수행합니다.
   * 실행할 .zip 패키지에서 미리 실행 가능한 실행 파일 다운로드
   * 리포지토리를 복제하고 프로젝트를 빌드하여 실행 파일을 직접 빌드합니다.

1. GitHub 리포지토리의 지침에 따라 **VoiceAssistantClient.exe** 클라이언트 애플리케이션을 열고 봇에 연결하도록 구성합니다.
1. **다시 연결 을** 선택하고 "새 대화가 시작되었습니다. 마이크 단추를 입력하거나 누릅니다."라는 메시지가 표시되는지 확인합니다.
1. 테스트해 보겠습니다. 마이크 단추를 선택하고 영어로 몇 단어를 말합니다. 말하는 대로 인식된 텍스트가 나타납니다. 말하기가 완료되면 봇은 고유한 음성으로 응답하여 "echo"라는 말과 인식된 단어를 말합니다.
 
   텍스트를 사용하여 봇과 통신할 수도 있습니다. 아래쪽 막대의 텍스트를 입력합니다. 

### <a name="troubleshoot-errors-in-the-windows-voice-assistant-client"></a>Windows Voice Assistant 클라이언트의 오류 문제 해결

주 앱 창에 오류 메시지가 표시되면 다음 표를 사용하여 문제를 식별하고 해결합니다.

| 메시지 | 어떻게 해야 하나요? |
|-------|----------------------|
|오류(AuthenticationFailure): 인증 오류(401)로 인해 WebSocket 업그레이드가 실패했습니다. 올바른 구독 키(또는 권한 부여 토큰) 및 지역 이름을 확인하세요.| 앱의 **설정** 페이지에서 구독 키와 해당 지역을 올바르게 입력했는지 확인합니다. |
|오류(ConnectionFailure): 원격 호스트가 연결을 종료했습니다. 오류 코드: 1011. 오류 세부 정보: 봇에 연결할 수 없어 메시지를 보낼 수 없습니다. | [스트리밍 엔드포인트 사용 확인란을 선택하거나](#register-the-direct-line-speech-channel) [웹 소켓 을 설정했는지 확인합니다.](#enable-web-sockets)<br>Azure App Service 실행 중인지 확인합니다. 이 경우 다시 시작해 보세요.|
|오류(ConnectionFailure): 원격 호스트가 연결을 종료했습니다. 오류 코드: 1002. 오류 세부 정보: 상태 코드 ‘101’을 예상했지만 서버에서 상태 코드 ‘503’을 반환했습니다. | [스트리밍 엔드포인트 사용 확인란을 선택하거나](#register-the-direct-line-speech-channel) [웹 소켓을 설정했는지 확인합니다.](#enable-web-sockets)<br>Azure App Service 실행 중인지 확인합니다. 이 경우 다시 시작해 보세요.|
|오류(ConnectionFailure): 원격 호스트가 연결을 종료했습니다. 오류 코드: 1011. 오류 세부 정보: 응답 상태 코드가 성공: 500(InternalServerError)을 나타내지 않습니다.| 봇이 출력 활동의 [음성](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 필드에 신경망 음성을 지정했지만 구독 키와 연결된 Azure 지역은 신경망 음성을 지원하지 않습니다. [인공신경망 및 표준 음성](./regions.md#neural-and-standard-voices)을 참조하세요.|

테이블의 작업이 문제를 해결하지 못하는 경우 [음성 도우미: 질문과 대답을](faq-voice-assistants.yml)참조하세요. 이 자습서의 모든 단계를 수행한 후에도 여전히 문제를 해결할 수 없는 경우 [음성 도우미 GitHub 페이지에](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues)새 문제를 입력하세요.

#### <a name="a-note-on-connection-timeout"></a>연결 시간 제한에 대한 참고 사항

봇에 연결되어 있고 지난 5분 동안 활동이 발생하지 않은 경우 서비스는 클라이언트 및 봇과의 웹 소켓 연결을 자동으로 닫습니다. 이것은 의도적인 것입니다. 아래쪽 표시줄에 "활성 연결 시간이 다 됐지만 필요에 따라 다시 연결할 준비가 되었습니다."라는 메시지가 표시됩니다. 

**다시 연결** 단추를 선택할 필요가 없습니다. 마이크 단추를 누르고 대화를 시작하거나, 문자 메시지를 입력하거나, 키워드를 말합니다(사용하도록 설정된 경우). 연결이 자동으로 다시 설정됩니다.  

### <a name="view-bot-activities"></a>봇 활동 보기

모든 봇은 활동 메시지를 보내고 받습니다. Windows Voice Assistant 클라이언트의 **활동 로그** 창에서 타임스탬프가 있는 로그는 클라이언트가 봇으로부터 받은 각 활동을 표시합니다. [DialogServiceConnector.SendActivityAsync](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) 메서드를 사용하여 클라이언트가 봇에 보낸 활동을 볼 수도 있습니다. 로그 항목을 선택하면 연결된 활동의 세부 정보가 JSON으로 표시됩니다.

클라이언트가 받은 활동의 샘플 JSON은 다음과 같습니다.

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

JSON 출력에 반환된 내용에 대한 자세한 내용은 [활동의 필드를](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)참조하세요. 이 자습서에서는 [텍스트](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) 및 [음성](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 필드에 집중할 수 있습니다.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Speech SDK 호출에 대한 클라이언트 소스 코드 보기

Windows 음성 도우미 클라이언트는 Speech SDK를 포함하고 있는 NuGet 패키지 [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)를 사용합니다. 샘플 코드 검토를 시작하는 좋은 위치는 `InitSpeechConnector()` 다음 두 개의 Speech SDK 개체를 만드는 [VoiceAssistantClient\MainWindow.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)파일의 메서드입니다.
- [DialogServiceConfig](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig): 구독 키 및 해당 지역과 같은 구성 설정입니다.
- [DialogServiceConnector:](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)인식된 음성 및 봇 응답을 처리하기 위한 채널 연결 및 클라이언트 구독 이벤트를 관리합니다.

## <a name="add-custom-keyword-activation"></a>사용자 지정 키워드 활성화 추가

Speech SDK는 사용자 지정 키워드 활성화를 지원합니다. Microsoft의 도우미에 대한 "Hey Cortana"와 마찬가지로 선택한 키워드를 계속 수신 대기하는 앱을 작성할 수 있습니다. 키워드는 단일 단어 또는 여러 단어 구일 수 있습니다.

> [!NOTE]
> *키워드라는* 용어는 *절전 모드를 깨우기 단어* 와 같은 의미로 사용하는 경우가 많습니다. Microsoft 설명서에 사용된 두 가지가 모두 표시될 수 있습니다.

키워드 검색은 클라이언트 앱에서 발생합니다. 키워드를 사용하는 경우 키워드가 검색된 경우에만 오디오가 Direct Line Speech 채널로 스트리밍됩니다. Direct Line Speech 채널에는 *선택한 키워드가* 오디오 스트림의 시작 부분에 있는지 확인하기 위해 클라우드에서 더 복잡한 처리를 수행하는 키워드 확인이라는 구성 요소가 포함되어 있습니다. 키워드 확인에 성공하면 채널이 봇과 통신합니다.

다음 단계에 따라 키워드 모델을 만들고, 이 모델을 사용하도록 Windows Voice Assistant 클라이언트를 구성하고, 봇에서 테스트합니다.

1. [Speech Service 를 사용하여 사용자 지정 키워드를 만듭니다.](./custom-keyword-basics.md)
1. 이전 단계에서 다운로드한 모델 파일의 압축을 풉니다. 키워드가 이름으로 지정되어 있을 것입니다. **kws.table** 이라는 파일을 찾고 있습니다.
1. Windows Voice Assistant 클라이언트에서 **설정** 메뉴(오른쪽 위에 있는 기어 아이콘)를 찾습니다. **모델 파일 경로** 의 경우 2단계에서 **kws.table** 파일의 전체 경로 이름을 입력합니다.
1. 사용 **확인란을** 선택합니다. "다음 연결 시 키워드를 수신 대기합니다." 확인란 옆에 다음 메시지가 표시됩니다. 잘못된 파일 또는 잘못된 경로를 입력한 경우 오류 메시지가 표시됩니다.
1. **구독 키** 및 구독 **키 영역** 의 값을 입력한 다음 **확인을** 선택하여 **설정** 메뉴를 닫습니다.
1. **다시 연결 을** 선택합니다. "새 대화가 시작되었습니다. 입력하거나, 마이크 단추를 누르거나, 키워드를 말하십시오."라는 메시지가 표시됩니다. 이제 앱이 지속적으로 수신 대기 중입니다.
1. 키워드로 시작하는 아무 문구나 말해 보세요. 예: "{your keyword}, what time is it?" 키워드를 말한 후 일시 중지할 필요가 없습니다. 완료되면 다음 두 가지 작업이 수행합니다.
   1. 말하는 내용의 전사가 표시됩니다.
   1. 봇의 응답을 들었습니다.
1. 봇이 지원하는 다음 세 가지 입력 형식을 계속 실험합니다.
   * 아래쪽 막대에 텍스트 입력
   * 마이크 아이콘을 누르고 말하기
   * 키워드로 시작하는 문구 말하기

### <a name="view-the-source-code-that-enables-keyword-detection"></a>키워드 검색을 사용하도록 설정하는 소스 코드 보기

Windows Voice Assistant 클라이언트의 소스 코드에서 다음 파일을 사용하여 키워드 검색을 사용하도록 설정하는 코드를 검토합니다.

- [VoiceAssistantClient\Models.cs에는](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) Speech SDK 메서드 [KeywordRecognitionModel.fromFile()](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-)에 대한 호출이 포함되어 있습니다. 이 메서드는 디스크의 로컬 파일에서 모델을 인스턴스화 하는 데 사용 합니다.
- [VoiceAssistantClient\MainWindow.xaml.cs에는](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) Speech SDK 메서드 [DialogServiceConnector.StartKeywordRecognitionAsync()](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)에 대한 호출이 포함되어 있습니다. 이 메서드는 연속 키워드 검색을 활성화합니다.

## <a name="optional-change-the-language-and-bot-voice"></a>선택 사항: 언어 및 봇 음성 변경

사용자가 만든 봇은 기본 미국 영어 텍스트 음성 변환 음성을 사용하여 영어로 수신 대기하고 응답합니다. 그러나 영어 또는 기본 음성을 사용하는 것으로 제한되지 않습니다. 

이 섹션에서는 봇이 수신 대기하고 응답하는 언어를 변경하는 방법을 알아봅니다. 해당 언어에 대해 다른 음성을 선택하는 방법도 알아봅니다.

### <a name="change-the-language"></a>언어 변경

[음성 텍스트로](language-support.md#speech-to-text) 번역 표에 언급된 언어 중에서 선택할 수 있습니다. 다음 예제에서는 언어를 독일어로 변경합니다.

1. Windows Voice Assistant 클라이언트 앱을 열고 **설정** 단추(오른쪽 위 기어 아이콘)를 선택하고 **언어** 필드에 **de-de를** 입력합니다. 음성 [텍스트로](language-support.md#speech-to-text) 표에 언급된 로케일 값입니다. 

   이 단계에서는 인식할 음성 언어를 설정하여 기본 **en-us** 를 재정의합니다. 또한 봇 응답에 기본 독일어 음성을 사용하도록 Direct Line Speech 채널에 지시합니다.
1. **설정** 페이지를 닫은 다음 다시 **연결** 단추를 선택하여 에코 봇에 대한 새 연결을 설정합니다.
1. 마이크 단추를 선택하고 독일어로 구를 말합니다. 인식된 텍스트가 나타나고 에코 봇이 기본 독일어 음성으로 응답합니다.

### <a name="change-the-default-bot-voice"></a>기본 봇 음성 변경

봇이 간단한 텍스트 대신 SSML(Speech [Synthesis Markup Language)](speech-synthesis-markup.md) 형식으로 회신을 지정하는 경우 텍스트 음성 대 음성을 선택하고 발음을 제어할 수 있습니다. 에코 봇은 SSML을 사용하지 않지만 이를 위해 코드를 쉽게 수정할 수 있습니다. 

다음 예제에서는 기본 여성 음성 대신 독일어 음성인 여아 Apollo(남성 음성)가 사용되도록 에코 봇 회신에 SSML을 추가합니다. 표준 [음성 목록](language-support.md#standard-voices) 및 언어에 대해 지원되는 [신경망 음성 목록을](language-support.md#neural-voices) 참조하세요.

1. **samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs 를** 엽니다.
1. 다음 줄을 찾습니다.

    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```

   다음 코드로 대체합니다.
   
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
1. Visual Studio에서 솔루션을 빌드하고 빌드 오류를 수정합니다.

메서드의 두 번째 인수는 `MessageFactory.Text` [봇](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 회신에서 활동 말하기 필드를 설정합니다. 이전 변경으로 기본값이 아닌 독일어 음성을 지정하기 위해 단순 텍스트에서 SSML로 대체되었습니다.

### <a name="redeploy-your-bot"></a>봇 다시 배포

이제 봇에 필요한 변경을 수행했으므로 다음 단계는 Azure App Service 다시 게시하고 사용해보는 것입니다.

1. 솔루션 탐색기 창에서 EchoBot 프로젝트를 마우스 오른쪽 **단추로** 클릭하고 **게시를** 선택합니다.
1. 이전 배포 구성이 이미 기본값으로 로드되었습니다. **EchoBot20190805125647 - 웹 배포** 옆에 있는 **게시를** 선택합니다.

   **게시 성공** 메시지가 Visual Studio 출력 창에 나타나고 "봇이 준비되었습니다!"라는 메시지와 함께 웹 페이지가 열립니다.
1. Windows Voice Assistant 클라이언트 앱을 엽니다. **설정** 단추(오른쪽 위 기어 아이콘)를 선택하고 **언어** 필드에 **de-de가** 여전히 있는지 확인합니다.
1. [Windows Voice Assistant 클라이언트 실행의](#run-the-windows-voice-assistant-client) 지침에 따라 새로 배포된 봇과 다시 연결하고, 새 언어로 말하고, 새 음성으로 해당 언어로 응답하는 봇의 의견을 들어보세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 배포된 에코 봇을 계속 사용하지 않려면 Azure 리소스 그룹을 삭제하여 해당 봇 및 관련된 모든 Azure 리소스를 제거할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)Azure 서비스 아래에서 **리소스 그룹을** **선택합니다.**
1. **SpeechEchoBotTutorial-ResourceGroup** 리소스 그룹을 찾습니다. 세 개의 점(...)을 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK를 사용하여 사용자 고유의 클라이언트 앱 빌드](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>추가 정보

* [가까운 Azure 지역에 배포하여](https://azure.microsoft.com/global-infrastructure/locations/) 봇 응답 시간의 향상을 확인합니다.
* [고품질 신경망 텍스트 음성 음성을 지원하는 Azure 지역에 배포합니다.](./regions.md#neural-and-standard-voices)
* Direct Line Speech 채널과 관련된 가격 책정을 받습니다.
  * [Bot Service 가격 책정](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 사용자 고유의 음성 지원 봇을 빌드하고 배포합니다.
  * [Bot Framework 봇](https://dev.botframework.com/)을 빌드합니다. 그런 [다음, Direct Line Speech 채널에 등록하고](/azure/bot-service/bot-service-channel-connect-directlinespeech) [음성을 위해 봇을 사용자 지정합니다.](/azure/bot-service/directline-speech-bot)
  * 기존 [Bot Framework 솔루션](https://microsoft.github.io/botframework-solutions/index)탐색: [가상 도우미를 빌드하고](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) [Direct Line Speech 확장합니다.](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
