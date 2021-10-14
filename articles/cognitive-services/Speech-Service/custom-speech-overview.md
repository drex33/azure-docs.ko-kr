---
title: Custom Speech 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Speech는 애플리케이션, 도구 및 제품에 대한 Microsoft의 음성 텍스트 변환 정확도를 평가하고 향상시킬 수 있는 온라인 도구 세트입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: pafarley
ms.custom: contperf-fy21q2, references_regions
ms.openlocfilehash: 58de4dacfbd2cb75c3bbfe4a77cda79fe99ffd75
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004448"
---
# <a name="what-is-custom-speech"></a>Custom Speech란?

Custom Speech를 사용하여 애플리케이션 및 제품에 대한 Microsoft의 음성 텍스트 변환 정확도를 평가하고 향상시킬 수 있습니다. 사용자 지정 음성 텍스트 변환 환경 만들기를 시작하려면 이 문서의 링크를 따릅니다.

## <a name="whats-in-custom-speech"></a>Custom Speech의 기능

Custom Speech를 사용하여 작업을 수행하려면 Azure 계정 및 Speech Service 구독이 필요합니다. 계정이 있으면 데이터를 준비하고, 모델을 학습 및 테스트하고, 인식 품질을 검사하고, 정확성을 평가하고, 궁극적으로 사용자 지정 음성 텍스트 변환 모델을 배포하고 사용할 수 있습니다.

이 다이어그램은 [Speech Studio의 Custom Speech 영역](https://aka.ms/speechstudio/customspeech)을 구성하는 부분을 강조 표시합니다. 각 단계에 대해 자세히 알아보려면 아래 링크를 사용합니다.

![Speech Studio의 Custom Speech 영역을 구성하는 구성 요소를 강조 표시하는 다이어그램입니다.](./media/custom-speech/custom-speech-overview.png)

1. [프로젝트 구독 및 만들기](#set-up-your-azure-account) Azure 계정을 만들고 Speech Service를 구독합니다. 이 통합 구독은 음성 텍스트 변환, 텍스트 음성 변환, 음성 번역 및 [Speech Studio](https://speech.microsoft.com/customspeech)에 대한 액세스를 제공합니다. 그런 다음, Speech Service 구독을 사용하여 첫 번째 Custom Speech 프로젝트를 만듭니다.

1. [테스트 데이터 업로드](./how-to-custom-speech-test-and-train.md) 애플리케이션, 도구 및 제품에 대한 Microsoft 음성 텍스트 변환 제품을 평가하는 테스트 데이터(오디오 파일)를 업로드합니다.

1. [인식 품질 검사](how-to-custom-speech-inspect-data.md) [Speech Studio](https://speech.microsoft.com/customspeech)를 사용하여 업로드된 오디오를 재생하고 테스트 데이터의 음성 인식 품질을 검사합니다. 양적 측정에 대해서는 [데이터 검사](how-to-custom-speech-inspect-data.md)를 참조하세요.

1. [정확도 평가 및 개선](how-to-custom-speech-evaluate-data.md) 음성 텍스트 변환 모델의 정확도를 평가하고 개선합니다. [Speech Studio](https://speech.microsoft.com/customspeech)는 추가 교육이 필요한지 여부를 결정하는 데 사용할 수 있는 *단어 오류율* 을 제공합니다. 정확도에 만족하는 경우 Speech Service API를 직접 사용할 수 있습니다. 5%~20%의 상대적 평균으로 정확도를 높이려면 포털의 **학습** 탭을 사용하여 휴먼 레이블 대화 기록 및 관련 텍스트와 같은 추가 학습 데이터를 업로드합니다.

1. [모델 학습 및 배포](how-to-custom-speech-train-model.md) 오디오 테스트 데이터와 함께 기록된 기록(10-1,000시간) 및 관련 텍스트(<200MB)를 제공하여 음성 텍스트 변환 모델의 정확도를 높입니다. 이 데이터는 음성 텍스트 변환 모델을 학습하는 데 도움이 됩니다. 학습 후 다시 테스트합니다. 결과에 만족하는 경우 사용자 지정 엔드포인트에 모델을 배포할 수 있습니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

[Speech Studio](https://speech.microsoft.com/customspeech)를 사용하여 사용자 지정 모델을 만들려면 Azure 계정 및 Speech Service 구독이 있어야 합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](overview.md#try-the-speech-service-for-free).

**오디오 데이터** 를 사용하여 사용자 지정 모델을 학습하려는 경우 학습에 사용할 수 있는 전용 하드웨어가 있는 다음 지역 중 하나를 선택합니다. 이렇게 하면 모델을 학습하는 데 걸리는 시간이 줄어들고 학습에 더 많은 오디오를 사용할 수 있습니다. 이러한 지역에서 Speech Service는 학습에 최대 20시간의 오디오를 사용합니다. 다른 지역에서는 최대 8시간만 사용합니다.

* 오스트레일리아 동부
* 캐나다 중부
* 인도 중부
* 미국 동부
* 미국 동부 2
* 미국 중북부
* 북유럽
* 미국 중남부
* 동남아시아
* 영국 남부
* US Gov 애리조나
* US Gov 버지니아
* 서유럽
* 미국 서부 2

Azure 계정과 Speech Service 구독을 만든 후에는 [Speech Studio](https://speech.microsoft.com/customspeech)에 로그인하여 구독을 연결해야 합니다.

1. [Speech Studio](https://aka.ms/speechstudio/customspeech)에 로그인합니다.
1. 작업해야 하는 구독을 선택하고 음성 프로젝트를 만듭니다.
1. 구독을 수정하려면 상단 메뉴에서 톱니바퀴 단추를 선택합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 엔드포인트와 같은 콘텐츠는 [Speech Studio](https://speech.microsoft.com/customspeech)에서 *프로젝트* 로 구성됩니다. 각 프로젝트는 도메인 및 국가/언어에 따라 다릅니다. 예를 들어 미국의 영어를 사용하는 콜 센터에 대한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **음성 텍스트 변환/Custom Speech** 를 선택한 다음, **새 프로젝트** 를 선택합니다. 마법사에서 제공하는 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터**, **테스트**, **학습** 및 **배포** 라는 네 개의 탭이 표시됩니다. [다음 단계](#next-steps)에 제공된 링크를 사용하여 각 탭을 사용하는 방법을 알아보세요.

> [!IMPORTANT]
> [Speech Studio(이전의](https://aka.ms/speechstudio) "Custom Speech Portal")은 최근에 업데이트되었습니다. CRIS.ai 포털에서 또는 API를 사용하여 이전 데이터, 모델, 테스트 및 게시된 엔드포인트를 만든 경우, 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결해야 합니다.

## <a name="model-and-endpoint-lifecycle"></a>모델 및 엔드포인트 수명 주기

일반적으로 최신 모델의 정확도가 높기 때문에 이전 모델은 시간이 지남에 따라 유용성이 떨어집니다. 따라서 포털을 통해 만든 사용자 지정 모델 및 엔드포인트뿐만 아니라 기본 모델도 적응을 위해 1년 후, 디코딩을 위해 2년 후에 만료될 수 있습니다. [모델 및 엔드포인트 수명 주기](./how-to-custom-speech-model-and-endpoint-lifecycle.md) 문서에서 자세한 설명을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [데이터 준비 및 테스트](./how-to-custom-speech-test-and-train.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [모델 정확도 평가 및 향상](how-to-custom-speech-evaluate-data.md)
* [모델 학습 및 배포](how-to-custom-speech-train-model.md)
