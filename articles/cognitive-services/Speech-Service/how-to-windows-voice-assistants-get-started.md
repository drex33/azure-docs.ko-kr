---
title: Windows용 음성 도우미 - 시작하기
titleSuffix: Azure Cognitive Services
description: 샘플 코드 빠른 시작에 대한 참조를 포함하여 Windows 음성 에이전트 개발을 시작하는 단계입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 72484bad390f642dae1c5ce5f94c303c55da616f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503964"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Windows용 음성 도우미 시작하기

이 가이드에서는 Windows용 음성 도우미 개발을 시작하는 단계를 안내합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

Windows용 음성 도우미 개발을 시작하려면 적절한 개발 환경이 있는지 확인해야 합니다.

- **Visual Studio:** [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/) Community Edition 이상을 설치해야 합니다.
- **Windows 버전**: Windows의 Windows 참가자 빠른 링 빌드와 Windows SDK의 Windows 참가자 버전이 있는 PC. 이 샘플 코드는 Windows SDK 19018을 사용하여 Windows 참가자 릴리스 빌드 19025.vb_release_analog.191112-1600에서 작동하는 것으로 확인되었습니다. 지정된 버전 이상의 모든 빌드 또는 SDK는 호환되어야 합니다.
- **UWP 개발 도구**: Visual Studio의 유니버설 Windows Platform 개발 워크로드. UWP 애플리케이션을 개발할 수 있도록 머신을 준비하려면 UWP [Get 설정](/windows/uwp/get-started/get-set-up) 페이지를 참조하세요.
- **작동 마이크 및 오디오 출력**

## <a name="obtain-resources-from-microsoft"></a>Microsoft에서 리소스 가져오기

Windows에서 완전히 사용자 지정된 음성 에이전트에 필요한 일부 리소스에는 Microsoft의 리소스가 필요합니다. [UWP 음성 도우미 샘플](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample)은 초기 개발 및 테스트를 위해 이러한 리소스의 샘플 버전을 제공하므로 초기 개발 시 이 섹션은 필요하지 않습니다.

- **키워드 모델:** 음성 활성화를 사용하려면 bin 파일 형식의 Microsoft 키워드 모델이 필요합니다. UWP 음성 도우미 샘플에 제공된 bin 파일은 *Contoso* 키워드에 대해 학습됩니다.
- **제한된 액세스 기능 토큰:** ConversationalAgent API는 마이크 오디오에 대한 액세스 권한을 제공하므로 제한된 액세스 기능 제한 사항에 따라 보호됩니다. 제한된 액세스 기능을 사용하려면 Microsoft에서 애플리케이션의 패키지 ID에 연결된 제한된 액세스 기능 토큰을 가져와야 합니다.

## <a name="establish-a-dialog-service"></a>대화 상자 서비스 설정

전체 음성 도우미 환경을 위해 애플리케이션은 다음과 같은 대화 상자 서비스가 필요합니다.

- 지정된 오디오 파일에서 키워드 검색
- 사용자 입력을 수신하고 텍스트로 변환
- 봇에 텍스트 제공
- 봇의 텍스트 응답을 오디오 출력으로 변환

이는 Direct Line Speech를 사용하여 기본 대화 상자 서비스를 만들기 위한 요구 사항입니다.

- **Speech Services 구독:** 음성-텍스트 변환 및 텍스트 음성 변환 변환에 대한 Cognitive Speech Services 구독. [여기](./overview.md#try-the-speech-service-for-free)에서 Speech Services를 무료로 사용해 보세요.
- **Bot Framework 봇:** 음성 입력 및 출력을 사용하도록 설정하기 위해 [Direct Line Speech](./direct-line-speech.md)를 구독하는 Bot Framework 버전 4.2 이상을 사용하여 만든 봇. [이 가이드](./tutorial-voice-enable-your-bot-speech-sdk.md)에는 "에코 봇"을 만들고 Direct Line Speech를 구독하는 단계별 지침이 포함되어 있습니다. [여기](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/)로 이동해도 사용자 지정된 봇을 만드는 방법에 대한 단계를 볼 수 있습니다. [여기](./tutorial-voice-enable-your-bot-speech-sdk.md)에서 동일한 단계에 따라 "에코 봇"이 아닌 새 봇을 만들고 Direct Line Speech를 구독할 수 있습니다.

## <a name="try-out-the-sample-app"></a>샘플 앱 사용해 보기

Speech Services 구독 키와 에코 봇의 봇 ID를 사용하여 [UWP 음성 도우미 샘플](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample) 사용을 준비합니다. 추가 정보의 지침에 따라 앱을 실행하고 자격 증명을 입력하세요.

## <a name="create-your-own-voice-assistant-for-windows"></a>나만의 Windows용 음성 도우미 만들기

Microsoft에서 제한된 액세스 기능 토큰과 bin 파일을 받은 후에는 나만의 Windows용 음성 도우미를 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 도우미 구현 가이드 읽기](windows-voice-assistants-implementation-guide.md)
