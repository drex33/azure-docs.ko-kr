---
title: 사용자 지정 신경망 음성 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: 사용자 지정 신경망 음성은 사용자 고유의 오디오 데이터를 샘플로 제공하여 애플리케이션에 대한 일종의 사용자 지정된 합성 음성을 만들 수 있도록 하는 텍스트 음성 변환 기능입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: pafarley
ms.openlocfilehash: 9f0af5c38bc41ab6ae3926d41564682257137575
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541048"
---
# <a name="what-is-custom-neural-voice"></a>사용자 지정 신경망 음성이란?

사용자 지정 신경망 음성은 애플리케이션에 대한 일종의 사용자 지정된 합성 음성을 만들 수 있도록 하는 TTS(텍스트 음성 변환) 기능입니다. 사용자 지정 신경망 음성을 사용하면 오디오 샘플을 학습 데이터로 제공하여 매우 자연스러운 음성을 빌드할 수 있습니다. 인공신경망 TTS 기술 및 다국어 다중 화자 유니버설 모델을 기반으로 하는 사용자 지정 신경망 음성을 사용하면 말하기 스타일이 다양한 합성 음성 또는 적응 가능한 크로스 언어를 만들 수 있습니다. 사용자 지정 신경망 음성의 실제적이고 자연스러운 음성은 브랜드를 대표하고, 머신을 의인화할 수 있으며, 사용자가 대화로 애플리케이션을 조작할 수 있도록 합니다. 사용자 지정 신경망 음성 및 언어 간 기능에 대해 지원되는 [언어](language-support.md#customization)를 참조하세요.

> [!NOTE]
> 사용자 지정 신경망 음성 기능을 사용하려면 등록해야 하며, 이 기능에 대한 액세스는 Microsoft의 자격 및 사용 조건에 따라 제한됩니다. 이 기능을 사용하려는 고객은 [수집 형식](https://aka.ms/customneural)으로 사용 사례를 등록해야 합니다.

## <a name="the-basics-of-custom-neural-voice"></a>사용자 지정 신경망 음성의 기본 사항

사용자 지정 신경망 음성에 사용되는 기본 인공신경망 TTS 기술은 Text Analyzer, Neural Acoustic Model, Neural Vocoder라는 세 가지 주요 구성 요소로 구성됩니다. 텍스트에서 자연스러운 합성 음성을 생성하기 위해 음소 시퀀스 형태로 출력을 제공하는 Text Analyzer의 첫 번째 입력은 텍스트입니다. 음소는 특정 언어로 한 단어를 구별하는 소리의 기본 단위입니다. 음소 시퀀스는 텍스트에 제공된 단어의 발음을 정의합니다. 

그런 다음, 음소 시퀀스는 음색, 말하는 스타일, 속도, 억양, 강세 패턴과 같은 음성 신호를 정의하는 음향 기능을 예측하기 위해 Neural Acoustic Model로 들어갑니다. 마지막으로 Neural Vocoder는 합성 음성이 생성될 수 있도록 음향 기능을 가청 전파로 변환합니다.

![사용자 지정 신경망 음성의 소개 이미지](./media/custom-voice/cnv-intro.png)

인공신경망 TTS 음성 모델은 인간 음성의 녹음 샘플을 기반으로 심층 신경망을 사용하여 학습합니다. 이 [블로그](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)에서는 인공신경망 TTS가 최신 인공신경망 음성 합성 모델에서 작동하는 방법을 설명합니다. 또한 이 블로그에서는 2시간 미만의 음성 데이터(또는 2,000개 미만의 녹음된 발화)를 사용하여 대상 화자의 음성에 유니버설 기본 모델을 적용하고 음성을 다른 언어 또는 스타일로 전송할 수 있는 방법을 설명합니다. Neural Vocoder를 학습하는 방법에 대한 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)을 참조하세요.

사용자 지정 신경망 음성을 사용하면 시나리오에 맞게 인공신경망 TTS 엔진을 조정할 수 있습니다. 사용자 지정 신경망 음성을 만들려면 [Speech Studio](https://speech.microsoft.com/customvoice)를 사용하여 녹음된 오디오 및 해당 스크립트를 업로드하고, 모델을 학습시키고, 음성을 사용자 지정 엔드포인트에 배포합니다. 사용자 지정 신경망 음성은 사용자가 제공한 텍스트를 사용하여 실시간으로 텍스트를 음성으로 변환하거나 텍스트 입력을 사용하여 오프라인으로 오디오 콘텐츠를 생성할 수 있습니다. 이는 [REST API](./rest-text-to-speech.md), [Speech SDK](./get-started-text-to-speech.md) 또는 [웹 포털](https://speech.microsoft.com/audiocontentcreation)을 통해 사용할 수 있습니다.

## <a name="get-started"></a>시작하기

* 사용자 지정 신경망 음성을 시작하고 프로젝트를 만들려면 [사용자 지정 신경망 음성 시작](how-to-custom-voice.md)을 참조하세요.
* 오디오 데이터를 준비하고 업로드하려면 [학습 데이터 준비](how-to-custom-voice-prepare-data.md)를 참조하세요.
* 모델을 학습하고 배포하려면 [음성 모델 만들기 및 사용](how-to-custom-voice-create-voice.md)을 참조하세요.

## <a name="terms-and-definitions"></a>용어 및 정의

| **기간**      | **정의**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 음성 모델   | 대상 화자의 고유한 음성 특성을 모방할 수 있는 텍스트 음성 변환 모델입니다. *음성 모델* 을 *음성 글꼴* 이나 *합성 음성* 이라고도 합니다. 음성 모델은 사람이 읽을 수 없고 오디오 녹음을 포함하지 않는 이진 형식의 매개 변수 집합입니다. 인간 음성의 오디오를 파생시키거나 구성하기 위해 리버스 엔지니어링할 수 없습니다. |
| 성우  | 성우의 음성처럼 소리를 내는 음성 모델을 만들기 위해 녹음되고 사용되는 음성의 개인 또는 대상 화자입니다.                                                                                                                                                                                                                                                   |
| 표준 TTS  | 음성 언어를 음성 조각으로 구분하는 TTS의 표준 또는 "기존" 방법으로, 기존 프로그래밍 또는 통계 방법을 사용하여 다시 혼합해서 일치시킬 수 있습니다.                                                                                                                                                                                                    |
| 인공신경망 TTS    | 인공신경망 TTS는 절차적 프로그래밍 또는 통계 방법을 사용하는 대신 음성이 자연스러운 인간 음성으로 결합되는 방식으로 "학습"하는 심층 신경망을 사용하여 음성을 합성합니다. 인공신경망 TTS는 대상 성우 녹음 외에도 다양한 화자의 음성 녹음을 사용하여 빌드된 원본 라이브러리/기본 모델을 사용합니다.          |
| 학습 데이터 | 성우 오디오 녹음 및 연결된 음성 텍스트를 포함하는 사용자 지정 신경망 음성 학습 데이터 세트입니다.                                                                                                                                                                                                                                                               |
| 가상 사용자       | 가상 사용자는 이 음성을 사용할 사용자를 설명합니다. 좋은 가상 사용자 설계는 이미 생성된 사용 가능한 음성 모델을 선택하든 새 성우를 캐스팅하고 녹음하여 처음부터 다시 시작하든 모든 음성 생성을 알립니다.                                                                                                |
| 스크립트        | 스크립트는 성우가 말하는 발화를 포함하는 텍스트 파일입니다. ("*발화*"라는 용어는 전체 문장과 짧은 구 둘 다를 포함합니다.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>AI의 책임 있는 사용

사용자 지정 신경망 음성을 책임감 있게 사용하는 방법을 알아보려면 [투명성 고지](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)를 참조하세요. Microsoft의 투명성 고지는 Microsoft의 AI 기술의 작동 방식, 시스템 소유자가 시스템 성능과 동작에 영향을 줄 수 있는 선택 사항 그리고 기술, 사람, 환경을 포함한 전체 시스템에 대한 사고의 중요성을 이해하는 데 도움을 주기 위한 것입니다.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 신경망 음성 시작](how-to-custom-voice.md)
