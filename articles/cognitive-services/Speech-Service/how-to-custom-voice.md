---
title: 사용자 지정 신경망 음성 시작 - Speech Service
titleSuffix: Azure Cognitive Services
description: 사용자 지정 신경망 음성은 사용자의 브랜드에 대해 인식할 수 있는 독특한 음성을 만들 수 있는 온라인 도구 집합입니다. 몇 가지 오디오 파일과 관련 음성 텍스트만 있으면 시작할 수 있습니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: eur
ms.openlocfilehash: f521f1d08124575553c32c8b6fb4c3931ddcaa6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510266"
---
# <a name="get-started-with-custom-neural-voice"></a>사용자 지정 신경망 음성 시작

[사용자 지정 신경망 음성](https://aka.ms/customvoice)은 사용자의 브랜드에 대해 인식할 수 있는 독특한 음성을 만들 수 있는 온라인 도구 집합입니다. 몇 가지 오디오 파일과 관련 음성 텍스트만 있으면 시작할 수 있습니다. 사용자 지정 음성 텍스트 변환 환경 만들기를 시작하려면 아래 링크를 따라가세요. 사용자 지정 신경망 음성에 대해 지원되는 [ 언어](language-support.md#customization) 및 [지역](regions.md#custom-voices)을 참조하세요.

> [!NOTE]
> Microsoft는 책임감 있는 AI 설계를 위한 노력의 일환으로 사용자 지정 신경망 음성의 사용을 제한했습니다. 애플리케이션을 검토하고 이를 책임감 있는 AI 원칙에 따라 사용하도록 약속해야만 이 기술에 대한 액세스 권한을 얻을 수 있습니다. [제한 액세스 정책](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)에 대해 자세히 알아보고 [여기서 신청](https://aka.ms/customneural)하세요. 
 
## <a name="set-up-your-azure-account"></a>Azure 계정 설정

사용자 지정 신경망 음성을 사용하려면 Speech Service 구독이 필요합니다. Azure에서 Speech Service 구독을 만들려면 다음 지침을 따르세요. Azure 계정이 없으면 새 계정에 등록하면 됩니다.  

Azure 계정과 Speech Service 구독을 만든 후에는 Speech Studio에 로그인하여 구독을 연결해야 합니다.

1. Azure Portal에서 Speech Service 구독 키를 가져옵니다.
2. [Speech Studio](https://speech.microsoft.com)에 로그인한 다음, **Custom Voice** 를 클릭합니다.
3. 구독을 선택하고 음성 프로젝트를 만듭니다.
4. 다른 음성 구독으로 전환하려면 상단 탐색 메뉴에 있는 톱니 모양 아이콘을 사용합니다.

> [!NOTE]
> 서비스를 사용하려면 Azure에서 F0 또는 S0 Speech Service 키를 만들어야 합니다. 사용자 지정 신경망 음성은 S0 계층만 지원합니다. 

## <a name="create-a-project"></a>프로젝트 만들기

데이터, 모델, 테스트 및 엔드포인트와 같은 콘텐츠는 Speech Studio에서 **프로젝트** 로 구성됩니다. 각 프로젝트는 만들려는 음성의 국가/언어 및 성별에 한정됩니다. 예를 들어 미국('en-US')에서 영어를 사용하는 콜 센터의 채팅 봇을 위한 여성 음성 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **텍스트 음성 변환/Custom Voice** 탭을 선택한 다음, **프로젝트 만들기** 를 클릭합니다. 마법사에서 제공하는 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **성우 설정**, **학습 데이터 준비**, **모델 학습** 및 **모델 배포** 라는 네 개의 탭이 표시됩니다. [다음 단계](#next-steps)에 제공된 링크를 사용하여 각 탭을 사용하는 방법을 알아보세요.

## <a name="tips-for-creating-a-custom-neural-voice"></a>사용자 지정 신경망 음성을 만들기 위한 팁

뛰어난 사용자 지정 음성을 만들려면 음성 디자인 및 데이터 준비에서 시스템에 음성 모델 배포에 이르기까지 각 단계에서 신중한 품질 제어가 필요합니다. 다음은 조직에 대한 사용자 지정 신경망 음성을 만들 때 수행해야 하는 몇 가지 주요 단계입니다. 

### <a name="persona-design"></a>가상 사용자 디자인

먼저 음성의 기능 및 음성 뒤에 있는 문자와 같은 요소를 정의하는 가상 사용자 간략한 문서를 사용하여 브랜드를 나타내는 음성의 가상 사용자를 디자인합니다. 이렇게 하면 스크립트 정의, 성우 선택, 학습 및 음성 튜닝을 포함하여 사용자 지정 음성 모델을 만드는 프로세스를 안내할 수 있습니다.

### <a name="script-selection"></a>스크립트 선택
 
녹음 스크립트를 신중하게 선택하여 음성에 대한 사용자 시나리오를 나타냅니다. 예를 들어 고객 서비스 봇을 만드는 경우 봇 대화의 구를 녹음 스크립트로 사용할 수 있습니다. 명령문, 질문, 느낌표 등을 포함하여 스크립트에 다양한 문장 유형을 포함합니다.

### <a name="preparing-training-data"></a>학습 데이터 준비

높은 신호 대 노이즈 비율을 달성하려면 오디오 녹음을 전문 품질 녹음 스튜디오에서 캡처하는 것이 좋습니다. 음성 모델의 품질은 학습 데이터에 따라 크게 달라집니다. 음성의 표현 방식에서 일관된 볼륨, 말하기 속도, 피치 및 일관성이 필요합니다.

녹음이 준비되면 [학습 데이터 준비](how-to-custom-voice-prepare-data.md)에 따라 학습 데이터를 올바른 형식으로 준비합니다.

### <a name="training"></a>학습

학습 데이터를 준비했으면 [Speech Studio](https://aka.ms/custom-voice)로 이동하여 사용자 지정 신경망 음성을 만듭니다. 사용자 지정 신경망 음성을 만들려면 최소 300개의 발화를 선택해야 합니다. 일련의 데이터 품질 검사는 업로드할 때 자동으로 수행됩니다. 고품질 음성 모델을 빌드하려면 오류를 수정하고 다시 제출해야 합니다.

### <a name="testing"></a>테스트

앱에 대한 다양한 사용 사례를 다루는 음성 모델에 대한 테스트 스크립트를 준비합니다. 다양한 콘텐츠의 품질을 보다 광범위하게 테스트할 수 있도록 학습 데이터 세트 내외부에서 스크립트를 사용하는 것이 좋습니다.

### <a name="tuning-and-adjustment"></a>튜닝 및 조정

학습된 음성 모델의 스타일과 특성은 학습에 사용되는 성우의 녹음 스타일과 품질에 따라 달라집니다. 그러나 음성 모델에 대한 API 호출을 통해 가상 음성을 생성할 때 [SSML(음성 합성 생성 언어)](./speech-synthesis-markup.md?tabs=csharp)을 사용하여 몇 가지 조정을 할 수 있습니다. SSML은 텍스트를 오디오로 변환하기 위해 TTS 서비스와 통신하는 데 사용되는 태그 언어입니다. 조정에는 피치, 속도, 음조 및 발음 수정의 변경이 포함됩니다.  음성 모델이 여러 스타일로 빌드된 경우 SSML을 사용하여 스타일을 전환할 수도 있습니다.

## <a name="migrate-to-custom-neural-voice"></a>사용자 지정 신경망 음성으로 마이그레이션

Custom Voice의 표준/비신경망 학습 계층(통계 파라메트릭, 연결)은 더 이상 사용되지 않습니다. 2021/2/28 이전에는 모든 기존 음성 구독으로 공지가 전송되었습니다. 사용 중단 기간(2021/3/1~2024/2/29) 동안 기존 표준 계층 사용자는 생성된 비신경망 모델을 계속 사용할 수 있습니다. 모든 새 사용자/새 음성 리소스는 신경망 계층/사용자 지정 신경망 음성으로 이동해야 합니다. 2024/2/29 이후에는 모든 표준/비신경망 사용자 지정 음성이 더 이상 지원되지 않습니다. 

비신경망/표준 사용자 지정 음성을 사용하는 경우 아래 단계를 즉시 수행하여 사용자 지정 신경망 음성으로 마이그레이션하세요. 사용자 지정 신경망 음성으로 이전하면 훨씬 더 자연스러운 대화형 인터페이스를 위해 보다 사실적인 음성을 개발하고, 고객과 최종 사용자가 책임감 있게 최신 텍스트 음성 변환 기술을 활용하도록 할 수 있습니다. 

1. [제한 액세스 정책](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)에 대해 자세히 알아보고 [여기서 신청](https://aka.ms/customneural)하세요. 사용자 지정 신경망 음성 서비스에 대한 액세스 권한은 Microsoft가 적격 기준에 따라 단독 재량으로 결정합니다. 고객은 애플리케이션을 검토하고 [책임감 있는 AI 원칙](https://microsoft.com/ai/responsible-ai) 및 [사용 규정](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)에 따라 사용하도록 약속한 후에만 이 기술에 대한 액세스 권한을 얻을 수 있습니다. 
2. 애플리케이션이 승인되면 "신경망" 학습 기능에 대한 액세스 권한이 제공됩니다. 애플리케이션에서 제공하는 것과 동일한 Azure 구독을 사용하여 [Speech Studio](https://speech.microsoft.com)에 로그인해야 합니다. 
    > [!IMPORTANT]
    > Microsoft에서는 성우를 보호하고, 무단 녹음을 사용하거나 성우의 허가를 받지 않은 음성 모델 학습을 방지하기 위해 고객에게 성우의 녹음된 동의 발언을 업로드할 것을 요구합니다. 녹음 스크립트를 준비할 때는 이 문장을 포함해야 합니다. “나[성 및 이름]는 내 음성의 합성 버전을 만들고 사용하기 위해 [회사 이름]에서 내 음성 녹음이 사용된다는 것을 알고 있습니다.”
    > 이 문장은 **성우 설정** 탭에 구두 동의 파일로 업로드해야 합니다. 이는 학습 데이터 세트가 동의한 사람에 의해 녹음되었는지 확인하는 데 사용됩니다.
3. 사용자 지정 신경망 음성 모델이 생성되면 새 엔드포인트에 음성 모델을 배포합니다. 신경망 음성 모델을 사용하여 새로운 사용자 지정 음성 엔드포인트를 만들려면 **텍스트 음성 변환 > Custom Voice > 모델 배포** 로 이동합니다. **모델 배포** 를 선택하고 사용자 지정 엔드포인트의 **이름** 및 **설명** 을 입력합니다. 그런 다음, 이 엔드포인트와 연결하려는 사용자 지정 신경망 음성 모델을 선택하고 배포를 확인합니다.  
4. 새 모델을 사용하여 새 엔드포인트를 만든 경우 앱에서 코드를 업데이트합니다. 

## <a name="next-steps"></a>다음 단계

- [사용자 지정 신경망 음성에 대한 데이터 준비](how-to-custom-voice-prepare-data.md)
- [사용자 지정 신경망 음성 학습 및 배포](how-to-custom-voice-create-voice.md)
- [음성 샘플 녹음 방법](record-custom-voice-samples.md)
