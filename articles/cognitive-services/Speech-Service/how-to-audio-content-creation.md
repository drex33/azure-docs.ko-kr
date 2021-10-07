---
title: 오디오 콘텐츠 만들기 - Speech Service
titleSuffix: Azure Cognitive Services
description: 오디오 콘텐츠 만들기는 앱 및 제품에 대한 Microsoft의 텍스트-음성 변환 출력을 사용자 지정하고 미세 조정할 수 있는 온라인 도구입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: pafarley
ms.openlocfilehash: e396c3b206f581935e04321c91bfd0c07c741953
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617571"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>오디오 콘텐츠 만들기 도구를 사용한 합성 향상

[오디오 콘텐츠 만들기](https://aka.ms/audiocontentcreation)는 다양한 시나리오(예: 오디오 서적, 뉴스 브로드캐스트, 비디오 설명 및 채팅 봇)에 대해 매우 자연스러운 오디오 콘텐츠를 빌드할 수 있는 사용하기 쉽고 강력한 도구입니다. 오디오 콘텐츠 만들기를 사용하면 텍스트-음성 변환 음성을 미세 조정하고 효율적이고 저렴한 방식으로 사용자 지정된 오디오 환경을 디자인할 수 있습니다.

도구는 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)을 기반으로 합니다. 음성 문자, 음성 스타일, 말하는 속도, 발음 및 운율 체계와 같은 텍스트-음성 변환 출력 특성을 실시간 또는 일괄 처리 합성에서 조정할 수 있습니다.

최신 인공신경망 TTS 음성과 사용자 지정 음성(빌드한 경우)을 비롯한 60개 이상의 다양한 언어로 미리 작성된 150가지 넘는 음성에 대한 손쉬운 액세스를 제공합니다.

오디오 콘텐츠 만들기를 위한 [비디오 자습서](https://youtu.be/ygApYuOOG6w)를 참조하세요.

## <a name="how-to-get-started"></a>시작하는 방법

오디오 콘텐츠 만들기는 무료 도구이지만 사용하는 Azure Speech 서비스에 대한 비용을 지불하게 됩니다. 도구로 작업하려면 Azure 계정으로 로그인하여 음성 리소스를 만들어야 합니다. 각 Azure 계정에 대해 월간 무료 음성 할당량은 인공신경망 TTS 음성(월별)에 대해 문자 50만 자, 표준 및 사용자 지정 음성(월별)의 문자 500만 자 및 1개의 사용자 지정 음성 엔드포인트 호스팅 서비스(월별)를 포함합니다. 매월 할당된 분량은 일반적으로 3-5명의 사용자를 위한 소규모 콘텐츠 팀에게 충분합니다. Azure 계정을 만들고 음성 리소스를 가져오는 방법에 대한 단계는 다음과 같습니다.

### <a name="step-1---create-an-azure-account"></a>1단계 - Azure 계정 만들기

오디오 콘텐츠 만들기를 사용하려면 [Microsoft 계정](https://account.microsoft.com/account) 및 [Azure 계정](https://azure.microsoft.com/free/ai/)이 있어야 합니다. 다음 지침에 따라 [계정을 설정](./overview.md#try-the-speech-service-for-free)합니다.

Azure 계정은 [Azure Portal](https://portal.azure.com/)에서 중앙 집중식으로 관리할 수 있습니다. 음성 리소스를 만들고, 제품 액세스를 관리하고, 간단한 웹앱에서 복잡한 클라우드 배포까지 모든 것을 모니터링할 수 있습니다.

### <a name="step-2---create-a-speech-resource"></a>2단계 - 음성 리소스 만들기

Azure 계정에 등록한 후에는 Speech Service에 액세스하기 위해 Azure 계정에서 음성 리소스를 만들어야 합니다. [음성 리소스를 만드는 방법](./overview.md#create-the-azure-resource)에 대한 지침을 확인하세요.

새 Speech 리소스를 배포하는 데 몇 분 정도 걸립니다. 배포가 완료되면 오디오 콘텐츠 만들기 경험을 시작할 수 있습니다.

 > [!NOTE]
   > 인공신경망 음성을 사용할 계획이라면 [인공신경망 음성을 지원하는 지역](regions.md#neural-and-standard-voices)에서 리소스를 만들어야 합니다.

### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>3단계 - Azure 계정 및 음성 리소스를 사용하여 오디오 콘텐츠 만들기에 로그인

1. Azure 계정과 음성 리소스를 가져온 후 **시작** 을 클릭하여 [오디오 콘텐츠 만들기](https://aka.ms/audiocontentcreation)에 로그인할 수 있습니다.
2. 홈 페이지에는 Speech Studio의 모든 제품이 나열됩니다. **오디오 콘텐츠 만들기** 를 클릭하여 시작합니다.
3. **Speech Studio 시작** 페이지가 음성 서비스를 설정하는 것으로 나타납니다. 작업하려는 Azure 구독 및 음성 리소스를 선택합니다. **리소스 사용** 을 클릭하여 설정을 완료합니다. 다음 번에 오디오 콘텐츠 만들기 도구에 로그인할 때 현재 음성 리소스 아래에 있는 오디오 작업 파일에 직접 연결됩니다. [Azure Portal](https://portal.azure.com/)에서 Azure 구독 세부 정보 및 상태를 확인할 수 있습니다. 사용 가능한 음성 리소스가 없고 Azure 구독의 소유자 또는 관리자인 경우 **새 리소스 만들기** 를 클릭하여 Speech Studio에서 새 음성 리소스를 만들 수도 있습니다. 특정 Azure 구독에 대한 사용자 역할인 경우 새 음성 리소스를 만들 수 있는 권한이 없을 수 있습니다. 음성 리소스 액세스 권한을 가져오려면 관리자에게 문의하세요. 
4. 위쪽 탐색 창에 있는 **설정** 옵션을 사용하여 언제든지 음성 리소스를 수정할 수 있습니다.
5. 디렉터리를 전환하려면 **설정** 또는 작동할 프로필로 이동하세요. 

## <a name="how-to-use-the-tool"></a>이 도구를 사용하는 방법

이 다이어그램에서는 텍스트-음성 변환 출력을 미세 조정하는 데 필요한 단계를 보여 줍니다. 각 단계에 대해 자세히 알아보려면 아래 링크를 사용합니다.

:::image type="content" source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="텍스트-음성 변환 출력을 미세 조정하는 데 필요한 단계의 다이어그램":::

1. 작업하려는 음성 리소스를 선택합니다.
2. 일반 텍스트 또는 SSML 스크립트를 사용하여 [오디오 튜닝 파일을 만듭니다](#create-an-audio-tuning-file). 오디오 콘텐츠 만들기에 콘텐츠를 입력하거나 업로드합니다.
3. 스크립트 콘텐츠에 사용할 음성 및 언어를 선택합니다. 오디오 콘텐츠 만들기에는 모든 [Microsoft 텍스트-음성 변환 음성](language-support.md#text-to-speech)이 포함됩니다. 표준, 인공신경망 또는 고유한 사용자 지정 음성을 사용할 수 있습니다.
   > [!NOTE]
   > 게이트 액세스는 사용자 지정 인공신경망 음성에 사용할 수 있으며, 이를 통해 자연스러운 음성과 유사한 고화질 음성을 만들 수 있습니다. 자세한 내용은 [게이팅 프로세스](./text-to-speech.md)를 참조하세요.

4. 미리 보려는 콘텐츠를 선택하고 **재생** 아이콘(삼각형)을 클릭하여 기본 합성 출력을 미리 봅니다. 텍스트를 변경하는 경우 **중지** 아이콘을 클릭한 다음, **재생** 아이콘을 다시 클릭하여 변경된 스크립트로 오디오를 다시 생성해야 합니다. 
5. 발음, 중단, 피치, 음조, 음성 스타일 등을 조정하여 출력을 향상시킵니다. 전체 옵션 목록은 [음성 합성 표시 언어](speech-synthesis-markup.md)를 참조하세요. 오디오 콘텐츠 만들기를 통해 음성 출력을 미세 조정하는 방법을 보여 주는 [비디오](https://youtu.be/ygApYuOOG6w)는 다음과 같습니다.
6. 저장하고 [튜닝된 오디오를 내보냅니다](#export-tuned-audio). 시스템에서 튜닝 트랙을 저장할 때 계속해서 작업하고 출력을 반복할 수 있습니다. 출력에 만족하는 경우 내보내기 기능을 사용하여 오디오 만들기 작업을 만들 수 있습니다. 내보내기 작업의 상태를 관찰하고 앱 및 제품에 사용할 출력을 다운로드할 수 있습니다.

## <a name="create-an-audio-tuning-file"></a>오디오 튜닝 파일 만들기

오디오 콘텐츠 만들기 도구에 콘텐츠를 가져오는 방법에는 두 가지가 있습니다.

**옵션 1:**

1. **새로 만들기** > **파일** 을 클릭하여 새 오디오 튜닝 파일을 만듭니다.
2. 콘텐츠를 편집 창에 입력하거나 붙여넣습니다. 각 파일의 문자는 최대 2만 자입니다. 스크립트가 2만 자보다 길면 옵션 2를 사용하여 콘텐츠를 자동으로 여러 파일로 분할할 수 있습니다.
3. 저장하는 것을 잊지 마세요.

**옵션 2:**

1. **업로드** 를 클릭하여 하나 이상의 텍스트 파일을 가져옵니다. 일반 텍스트와 SSML은 모두 지원됩니다. 스크립트 파일이 2만 자를 초과하는 경우 문자 또는 정규식을 기준으로 파일을 단락으로 분할하세요.
3. 텍스트 파일을 업로드하는 경우 파일이 이러한 요구 사항을 충족하는지 확인합니다.

   | 속성 | 값/메모 |
   |----------|---------------|
   | 파일 형식 | 일반 텍스트(.txt)<br/> SSML 텍스트(.txt)<br/> Zip 파일은 지원되지 않음 |
   | 인코딩 형식 | UTF-8 |
   | 파일 이름 | 각 파일은 고유한 이름이 있어야 합니다. 중복은 지원되지 않습니다. |
   | 텍스트 길이 | 텍스트 파일의 문자 제한은 2만 자입니다. 파일이 제한을 초과하는 경우 도구에 있는 지침을 사용하여 파일을 분할하세요. |
   | SSML 제한 | 각 SSML 파일은 SSML의 단일 부분만 포함할 수 있습니다. |

**일반 텍스트 예제**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

**SSML 텍스트 예제**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>튜닝된 오디오 내보내기

오디오 출력을 검토한 후 튜닝 및 조정에 만족하면 오디오를 내보낼 수 있습니다.

1. **내보내기** 를 클릭하여 오디오 만들기 작업을 만듭니다. 긴 오디오 출력과 전체 오디오 출력 환경을 지원하므로 **오디오 라이브러리로 내보내기** 가 권장됩니다. 로컬 디스크에 직접 오디오를 다운로드할 수도 있지만 처음 10분 동안만 사용할 수 있습니다.
2. 튜닝된 오디오의 출력 형식을 선택합니다. 지원되는 형식 및 샘플 속도의 목록은 아래에서 확인할 수 있습니다.
3. **작업 내보내기** 탭에서 작업 상태를 볼 수 있습니다. 작업에 실패하는 경우 전체 보고서에 대한 자세한 정보 페이지를 참조하세요.
4. 작업이 완료되면 **오디오 라이브러리** 탭에서 오디오를 다운로드할 수 있습니다.
5. **다운로드** 를 클릭합니다. 이제 앱 또는 제품에서 사용자 지정 튜닝된 오디오를 사용할 준비가 되었습니다.

**지원되는 오디오 형식**

| 형식 | 16kHz 샘플 속도 | 24kHz 샘플 속도 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>오디오 콘텐츠 만들기 사용자를 추가/제거하는 방법

둘 이상의 사용자가 오디오 콘텐츠 만들기를 사용하려는 경우 Azure 구독 및 음성 리소스에 대한 사용자 액세스 권한을 부여할 수 있습니다. Azure 구독에 사용자를 추가하는 경우 사용자는 Azure 구독에 있는 모든 리소스에 액세스할 수 있습니다. 하지만 사용자를 음성 리소스에 추가하는 경우에만 사용자가 음성 리소스에 액세스할 수 있으며, 이 Azure 구독의 다른 리소스에는 액세스할 수 없습니다. 음성 리소스에 대한 액세스 권한이 있는 사용자는 오디오 콘텐츠 만들기를 사용할 수 있습니다.

사용자는 [Microsoft 계정](https://account.microsoft.com/account)을 준비해야 합니다. 사용자에게 Microsoft 계정이 없는 경우 몇 분 안에 계정을 만듭니다. 사용자는 기존 이메일과 링크를 Microsoft 계정으로 사용하거나 새 outlook 이메일을 Microsoft 계정으로 만들 수 있습니다.


### <a name="add-users-to-a-speech-resource"></a>음성 리소스에 사용자 추가

오디오 콘텐츠 만들기를 사용할 수 있도록 사용자를 음성 리소스에 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Cognitive Services** 를 검색하고, 사용자를 추가하려는 음성 리소스를 선택합니다.
2. **액세스 제어(IAM)** 를 클릭합니다.   >  **역할 할당 추가(미리 보기)를** 선택하여 역할 할당 추가 창을 엽니다. 
1. **역할** 탭에서 Cognitive **Service 사용자** 역할을 선택합니다. 사용자에게 이 음성 리소스의 소유권을 부여하려면 **소유자** 역할을 선택할 수 있습니다.
1. **멤버** 탭에서 사용자의 이메일 주소를 입력하고 디렉터리에서 사용자를 선택합니다. 메일 주소는 Azure Active Directory에서 신뢰할 수 있는 **Microsoft 계정** 이어야 합니다. 사용자는 개인 메일 주소를 사용하여 [Microsoft 계정](https://account.microsoft.com/account)에 쉽게 가입할 수 있습니다. 
1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.
1. 사용자는 이메일 초대를 받게 됩니다. 이메일에서 **초대 수락** > **Azure 조인에 동의** 를 클릭하여 초대를 수락합니다. 그러면 사용자가 Azure Portal로 리디렉션됩니다. 사용자는 Azure Portal에서 추가 작업을 수행할 필요가 없습니다. 잠시 후 사용자는 음성 리소스 범위의 역할이 할당되고 이 음성 리소스에 액세스할 수 있게 됩니다. 사용자가 초대 메일을 받지 못한 경우 “역할 할당”에서 사용자 계정을 검색하고 사용자 프로필 내부로 이동할 수 있습니다. “ID” -> “초대가 수락됨”을 찾고 **(관리)** 를 클릭하여 메일 초대를 다시 보냅니다. 사용자에게 초대 링크를 복사할 수도 있습니다. 
1. 이제 사용자는 [오디오 콘텐츠 만들기](https://aka.ms/audiocontentcreation) 제품 페이지를 방문하거나 새로 고치고 사용자의 Microsoft 계정으로 로그인합니다. 모든 음성 제품 중에서 **오디오 콘텐츠 만들기** 블록을 선택합니다. 팝업 창 또는 페이지 오른쪽 위에 있는 설정에서 음성 리소스를 선택합니다. 사용자가 사용 가능한 음성 리소스를 찾을 수 없는 경우 적합한 디렉터리에 있는지 확인합니다. 적합한 디렉터리를 확인하려면 오른쪽 위에 있는 계정 프로필을 클릭하고 “현재 디렉터리” 옆에 있는 **전환** 을 클릭합니다. 사용 가능한 디렉터리가 두 개 이상 있는 경우 여러 디렉터리에 액세스할 수 있음을 의미합니다. 다른 디렉터리로 전환하고 설정으로 이동하여 적합한 음성 리소스를 사용할 수 있는지 확인합니다. 

    :::image type="content" source="media/audio-content-creation/add-role-first.png" alt-text="역할 추가 대화 상자":::


동일한 음성 리소스에 있는 사용자는 오디오 콘텐츠 만들기 스튜디오에서 서로의 작업을 볼 수 있습니다. 개별 사용자가 오디오 콘텐츠 만들기에서 고유한 프라이빗 작업 공간을 갖도록 하려면 각 사용자에 대해 [새 음성 리소스를 만들고](#step-2---create-a-speech-resource) 각 사용자에게 음성 리소스에 대한 고유한 액세스를 제공하세요.

### <a name="remove-users-from-a-speech-resource"></a>음성 리소스에서 사용자 제거

1. Azure Portal에서 **Cognitive Services** 를 검색하고, 사용자를 제거하려는 음성 리소스를 선택합니다.
2. **액세스 제어(IAM)** 를 클릭합니다. **역할 할당** 탭을 클릭하여 이 음성 리소스의 모든 역할 할당을 봅니다.
3. 제거하려는 사용자를 선택하고 **제거** > **확인** 을 클릭합니다.
    :::image type="content" source="media/audio-content-creation/remove-user.png" alt-text="제거 단추":::

### <a name="enable-users-to-grant-access"></a>사용자가 액세스 권한을 부여할 수 있도록 설정

사용자 중 한 명에게 다른 사용자에 대한 액세스 권한을 부여하려면 사용자에게 음성 리소스에 대한 소유자 역할을 부여하고 사용자를 Azure 디렉터리 읽기 권한자로 설정해야 합니다.
1. 사용자를 음성 리소스의 소유자로 추가합니다. [음성 리소스에 사용자를 추가하는 방법](#add-users-to-a-speech-resource)을 참조하세요.
    :::image type="content" source="media/audio-content-creation/add-role.png" alt-text="역할 소유자 필드":::
2. [Azure Portal](https://portal.azure.com/)에서 왼쪽 위에 있는 축소된 메뉴를 선택합니다. **Azure Active Directory** 를 클릭한 다음, **사용자** 를 클릭합니다.
3. 사용자의 Microsoft 계정을 검색하고 사용자의 세부 정보 페이지로 이동합니다. **할당된 역할** 을 클릭합니다.
4. **할당 추가** -> **디렉터리 읽기 권한자** 를 클릭합니다. “할당 추가” 단추가 회색으로 표시되면 액세스할 수 없음을 의미입니다. 이 디렉터리의 전역 관리자만 사용자에게 할당을 추가할 수 있습니다.

## <a name="see-also"></a>참고 항목

* [긴 오디오 API](./long-audio-api.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
