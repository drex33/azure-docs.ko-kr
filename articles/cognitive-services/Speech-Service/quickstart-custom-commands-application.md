---
title: '빠른 시작: 사용자 지정 명령을 사용하여 음성 도우미 만들기'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Speech Studio를 사용하여 기본 사용자 지정 명령 애플리케이션을 만들고 테스트합니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: 3c49e0f07c05e89907e54a78e02ba1de520091a2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505122"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>사용자 지정 명령을 사용하여 음성 도우미 만들기

이 빠른 시작에서는 Speech Studio를 사용하여 기본 사용자 지정 명령 애플리케이션을 만들고 테스트합니다. Windows 클라이언트 앱에서 이 애플리케이션에 액세스할 수도 있습니다.

## <a name="region-availability"></a>지역 가용성
현재 사용자 지정 명령은 다음 지역에서 만들어진 음성 구독을 지원합니다.
* 미국 서부
* 미국 서부2
* 미국 동부
* 미국 동부2
* 미국 중서부
* 북유럽
* 서유럽
* 동아시아
* 동남아시아
* 인도 중부

## <a name="prerequisites"></a>필수 조건

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">사용자 지정 명령을 지원하는 지역에서 Azure Speech 리소스를 만듭니다.</a> 지원되는 지역 목록은 위에 있는 **지역 가용성** 섹션을 참조하세요.
> * 샘플 [Smart Room Lite](https://aka.ms/speech/cc-quickstart) json 파일을 다운로드합니다.
> * 최신 버전의 [Windows 음성 도우미 클라이언트](https://aka.ms/speech/va-samples-wvac)를 다운로드합니다.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>사용자 지정 명령용 Speech Studio로 이동

1. 웹 브라우저에서 [Speech Studio](https://speech.microsoft.com/)로 이동합니다.
1. 자격 증명을 입력하여 포털에 로그인합니다.

   기본 보기는 음성 구독의 목록입니다.
   > [!NOTE]
   > 구독 선택 페이지가 표시되지 않는 경우 위쪽 표시줄의 설정 메뉴에서 "음성 리소스"를 선택하여 탐색할 수 있습니다.

1. 음성 구독을 선택한 다음, **스튜디오로 이동** 을 선택합니다.
1. **사용자 지정 명령** 을 선택합니다.

   기본 보기는 선택한 구독 아래에 있는 사용자 지정 명령 애플리케이션의 목록입니다.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>새 사용자 지정 명령 프로젝트로 기존 애플리케이션 가져오기

1. **새 프로젝트** 를 선택하여 프로젝트를 만듭니다.

1. **이름** 상자에서 프로젝트 이름을 `Smart-Room-Lite`(또는 원하는 이름)로 입력합니다.
1. **언어** 목록에서 **영어(미국)** 을 선택합니다.
1. **파일 찾아보기** 를 선택하고 찾아보기 창에서 **SmartRoomLite.json** 파일을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![프로젝트 만들기](media/custom-commands/import-project.png)

1.  **LUIS 작성 리소스** 목록에서 작성 리소스를 선택합니다. 유효한 작성 리소스가 없는 경우 **LUIS 작성 리소스 새로 만들기** 를 선택하여 리소스를 만듭니다.

    > [!div class="mx-imgBorder"]
    > ![리소스 그룹 만들기](media/custom-commands/create-new-luis-resource.png)
    
    
    1. **리소스 이름** 상자에 리소스 이름을 입력합니다.
    1. **리소스 그룹** 목록에서 리소스 그룹을 선택합니다.
    1. **위치** 목록에서 위치를 선택합니다.
    1. **가격 책정 계층** 목록에서 계층을 선택합니다.
    
    
    > [!NOTE]
    > "리소스 그룹" 필드에 원하는 리소스 그룹 이름을 입력하여 리소스 그룹을 만들 수 있습니다. **만들기** 를 선택하면 리소스 그룹이 만들어집니다.


1. 다음으로 **만들기** 를 프로젝트를 만듭니다.
1. 프로젝트를 만든 후 프로젝트를 선택합니다.
이제 새 사용자 지정 명령 애플리케이션의 개요가 표시됩니다.

## <a name="try-out-some-voice-commands"></a>몇 가지 음성 명령을 시험합니다.
1. 오른쪽 창 상단에서 **학습** 을 선택합니다.
1. 학습이 완료되면 **테스트** 를 선택하고 다음 발화를 시험합니다.
    - TV 켜기
    - 온도를 80도로 설정하기
    - TV
    - 끄기
    - 오후 5시로 알람 설정하기

## <a name="integrate-custom-commands-application-in-an-assistant"></a>도우미에 사용자 지정 명령 애플리케이션 통합
Speech Studio 외부에서 이 애플리케이션에 액세스하려면 먼저 애플리케이션을 게시해야 합니다. 애플리케이션을 게시하려면 예측 LUIS 리소스를 구성해야 합니다.  

### <a name="update-prediction-luis-resource"></a>예측 LUIS 리소스 업데이트


1. 왼쪽 창에서 **설정** 을 선택하고 가운데 창에서 **LUIS 리소스** 를 선택합니다.
1. 예측 리소스를 선택하거나 **새 리소스 만들기** 를 선택하여 리소스를 만듭니다.
1. **저장** 을 선택합니다.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS 리소스 설정](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> 작성 리소스는 매달 1,000개의 예측 엔드포인트 요청만 지원하기 때문에 사용자 지정 명령 애플리케이션을 게시하기 전에 LUIS 예측 리소스를 반드시 설정해야 합니다.

### <a name="publish-the-application"></a>애플리케이션 게시

오른쪽 창 상단에서 **게시** 를 선택합니다. 게시가 완료되면 새 창이 표시됩니다. **애플리케이션 ID** 및 **음성 리소스 키** 값을 기록합니다. Speech Studio 외부에서 애플리케이션에 액세스하려면 이 두 값이 필요합니다.

또는 **설정** > **일반** 섹션을 선택하여 이 값을 가져올 수도 있습니다.

### <a name="access-application-from-client"></a>클라이언트에서 애플리케이션 액세스

이 문서의 범위에서 필수 조건의 일부로 다운로드한 Windows 음성 도우미 클라이언트를 사용하게 됩니다. 폴더의 압축을 풉니다.
1. **VoiceAssistantClient.exe** 를 실행합니다.
1. 새 게시 프로필을 만들고 **연결 프로필** 에 대한 값을 입력합니다. **일반 설정** 섹션에서 **구독 키**(애플리케이션을 게시할 때 저장한 **음성 리소스 키** 값과 동일함), **구독 키 지역** 및 **사용자 지정 명령 앱 ID** 값을 입력합니다.
    > [!div class="mx-imgBorder"]
    > ![WVAC 프로필을 만들기 위한 일반 설정 섹션을 강조 표시하는 스크린샷.](media/custom-commands/create-profile.png)
1. **저장 및 프로필 적용** 을 선택합니다.
1. 이제 음성/텍스트를 통해 다음 입력을 시험합니다.
    > [!div class="mx-imgBorder"]
    > ![WVAC 프로필 만들기](media/custom-commands/conversation.png)


> [!TIP]
> **활동 로그** 에서 항목을 클릭하여 사용자 지정 명령 서비스에서 전송되는 원시 응답을 검사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 애플리케이션을 사용했습니다. 다음으로 [방법 섹션](./how-to-develop-custom-commands-application.md)에서는 처음부터 사용자 지정 명령 애플리케이션을 설계, 개발, 디버그, 테스트 및 통합하는 방법을 알아봅니다.
