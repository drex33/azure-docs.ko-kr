---
title: Custom Speech용 데이터 준비 - Speech Service
titleSuffix: Azure Cognitive Services
description: Microsoft 음성 인식의 정확도를 테스트하거나 사용자 지정 모델을 학습하는 경우 오디오 및 텍스트 데이터가 필요합니다. 이 페이지에서는 데이터 형식, 사용 방법 및 관리 방법에 대해 다룹니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: eur
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14cf969fa5aba3f3fddd6fedc63c2ccf19d71e08
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133494"
---
# <a name="prepare-data-for-custom-speech"></a>Custom Speech에 대한 데이터 준비

Microsoft 음성 인식의 정확도를 테스트하거나 사용자 지정 모델을 학습하는 경우 오디오 및 텍스트 데이터가 필요합니다. 이 페이지에서는 사용자 지정 음성 모델에 필요한 데이터 형식을 다룹니다.

## <a name="data-diversity"></a>데이터 다양성

사용자 지정 모델을 테스트하고 학습하는 데 사용되는 텍스트와 오디오는 모델이 인식해야 하는 다양한 발화자 및 시나리오 세트의 샘플을 포함해야 합니다.
사용자 지정 모델 테스트 및 학습을 위해 데이터를 수집할 때 다음 요소를 고려합니다.

* 텍스트 및 음성 오디오 데이터는 사용자가 모델과 상호 작용할 때 사용자가 사용하게 될 다양한 종류의 발화문을 포함해야 합니다. 예를 들어 온도 조절을 위한 모델의 경우 사용자가 온도 조절 요청을 위해 사용하게 될 문을 학습시켜야 합니다.
* 데이터에는 모델에서 인식해야 하는 모든 발화의 차이를 포함해야 합니다. 억양, 방언, 언어 혼합, 연령, 성별, 음성 피치, 스트레스 수준 및 녹음 일정 등 많은 요인으로 인해 음성이 달라질 수 있습니다.
* 모델이 사용될 여러 환경(실내, 실외, 도로 노이즈)의 샘플을 포함해야 합니다.
* 프로덕션 시스템에서 사용하게 될 하드웨어 디바이스를 사용하여 오디오를 수집해야 합니다. 모델에서 다양한 품질의 레코딩 디바이스에서 기록된 음성을 식별해야 하는 경우 모델 학습을 위해 제공하는 오디오 데이터는 이러한 다양한 시나리오를 나타내야 합니다.
* 나중에 더 많은 데이터를 모델에 추가할 수 있지만, 데이터 세트를 다양하게 유지하고 프로젝트에 필요한 것을 나타낼 수 있도록 유지하는 데 신경 써야 합니다.
* 사용자 지정 모델 인식에 포함되지 *않은* 데이터를 포함하면 전체적으로 인식 품질이 손상될 수 있으므로 모델에 기록할 필요가 없는 데이터는 포함하지 않습니다.

시나리오의 하위 집합에 대해 학습된 모델은 해당 시나리오에서만 제대로 수행할 수 있습니다. 사용자 지정 모델이 인식해야 하는 시나리오의 전체 범위를 나타내는 데이터를 신중하게 선택합니다.

> [!TIP]
> 모델이 마주하게 될 언어 및 음향에 맞는 작은 샘플 데이터 세트로 시작합니다.
> 예를 들어 프로덕션 시나리오에서 모델이 마주하게 될 동일한 하드웨어와 동일한 음향 환경에서 작지만 대표적인 오디오 샘플을 기록합니다.
> 학습용으로 훨씬 더 큰 데이터 세트를 수집하는 데 투자하기 전에 대표 데이터의 작은 데이터 세트에 문제가 생길 수 있습니다.
>
> 빠르게 시작하려면 샘플 데이터를 사용하는 것이 좋습니다. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">샘플 Custom Speech 데이터</a>는 이 GitHub 리포지토리를 참조하세요.

## <a name="data-types"></a>데이터 형식

다음 표에서는 허용되는 데이터 형식, 각 데이터 형식을 사용해야 하는 경우 및 권장 수량을 확인할 수 있습니다. 모델을 만드는 데 모든 데이터 형식이 필요하지는 않습니다. 데이터 요구 사항은 테스트를 만드는지 모델을 학습시키는지에 따라 다릅니다.

| 데이터 형식 | 테스트용으로 사용 | 권장 수량 | 학습용으로 사용 | 권장 수량 |
|-----------|-----------------|----------|-------------------|----------|
| [오디오](#audio-data-for-testing) | 예<br>시각적 검사에 사용 | 5개 이상의 오디오 파일 | 예 | 해당 없음 |
| [오디오 + 휴먼 레이블 대화 기록](#audio--human-labeled-transcript-data-for-trainingtesting) | 예<br>정확도 평가에 사용 | 0.5-5시간 분량의 오디오 | 예 | 1-20시간 분량의 오디오 |
| [일반 텍스트](#plain-text-data-for-training) | 예 | 해당 사항 없음 | 예 | 1-200MB의 관련 텍스트 |
| [구조적 텍스트(공개](#structured-text-data-for-training-public-preview) 미리 보기) | 예 | 해당 사항 없음 | 예 | 최대 4000개 항목과 최대 50,000개의 학습 문장이 있는 최대 10개의 클래스 |
| [발음](#pronunciation-data-for-training) | 예 | 해당 사항 없음 | 예 | 1KB - 1MB의 발음 텍스트 |

파일은 형식에 따라 데이터 세트로 그룹화하고 .zip 파일로 업로드해야 합니다. 각 데이터 세트는 단일 데이터 형식만 포함할 수 있습니다.

> [!TIP]
> 새 모델을 학습할 때 일반 텍스트 데이터 또는 구조적 텍스트 데이터로 시작합니다. 이 데이터는 특수 용어 및 구의 인식을 향상시킵니다. 텍스트를 사용한 학습(몇 분)은 오디오를 사용한 학습(며칠)보다 훨씬 빠릅니다.

> [!NOTE]
> 모든 기본 모델이 오디오를 통한 학습을 지원하지는 않습니다. 기본 모델이 이를 지원하지 않는 경우 Speech Service는 대본의 텍스트만 사용하고 오디오는 무시합니다. [언어 지원](language-support.md#speech-to-text)을 참조하여 오디오 데이터를 통한 학습을 지원하는 기본 모델의 목록을 확인하세요. 기본 모델이 오디오 데이터를 통한 학습을 지원하는 경우라도 해당 서비스는 오디오의 일부만 사용할 수 있습니다. 하지만 대본은 모두 사용합니다.
>
> 학습에 사용되는 기본 모델을 변경하고 학습 데이터 세트에 오디오가 있는 경우 선택한 새 기본 모델이 [오디오 데이터를 통한 학습을 지원](language-support.md#speech-to-text)하는지 *항상* 확인합니다. 이전에 사용된 기본 모델에서 오디오 데이터를 통한 학습을 지원하지 않고 학습 데이터 세트에 오디오가 포함된 경우 새 기본 모델의 학습 시간이 몇 시간에서 며칠 이상으로 **크게** 증가할 수 있습니다. Speech Service 구독이 학습 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account)에 있지 **않은** 경우에 특히 그렇습니다.
>
> 위의 단락에 설명된 문제가 발생한 경우 데이터 세트의 오디오 양을 줄이거나 완전히 제거하여 텍스트만 남겨두면 학습 시간을 빠르게 줄일 수 있습니다. 두 번째 옵션은 Speech Service 구독이 학습 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account)에 있지 **않은** 경우에 매우 권장됩니다.
>
> 학습 전용 하드웨어가 있는 지역에서 Speech Service는 학습을 위해 최대 20시간의 오디오를 사용합니다. 다른 지역에서는 최대 8시간의 오디오만 사용합니다.

> [!NOTE]
> 구조적 텍스트를 사용한 학습은 en-US, en-UK, en-IN, de-DE, fr-FR, fr-CA, es-ES, es-MX 로케일에서만 지원되며, 이러한 로케일에 대해 최신 기본 모델을 사용해야 합니다. 
>
> 구조적 텍스트로 학습을 지원하지 않는 로케일의 경우 서비스는 일반 텍스트 데이터를 포함하는 학습의 일부로 클래스를 참조하지 않는 학습 문장을 가져옵니다.

## <a name="upload-data"></a>데이터 업로드

데이터를 업로드하려면 [Speech Studio](https://aka.ms/speechstudio/customspeech)으로 이동합니다. 프로젝트를 만든 후 **음성 데이터 세트** 탭으로 이동하고 **데이터 업로드** 를 클릭하여 마법사를 시작하고 첫 번째 데이터 세트를 만듭니다. 데이터 세트에 대한 음성 데이터 형식을 선택하고 데이터를 업로드합니다.

> [!NOTE]
> 데이터 세트 파일 크기가 128MB를 초과하는 경우 *Azure Blob 또는 공유 위치* 옵션을 사용해야만 업로드할 수 있습니다. 음성 텍스트로 REST API [v3.0을](rest-speech-to-text.md#speech-to-text-rest-api-v30) 사용하여 허용되는 크기의 데이터 세트를 [업로드할](speech-services-quotas-and-limits.md#model-customization)수도 있습니다. 자세한 내용은 [다음 섹션을](#upload-data-using-speech-to-text-rest-api-v30) 참조하세요.

먼저 **학습** 또는 **테스트** 중 어디에 데이터 세트를 사용할지 지정해야 합니다. **학습** 또는 **테스트** 에 업로드 및 사용할 수 있는 여러 유형의 데이터가 있습니다. 업로드하는 각 데이터 세트는 업로드하기 전에 올바른 형식으로 지정해야 하며 선택한 데이터 형식에 대한 요구 사항을 충족해야 합니다. 다음 섹션에서는 요구 사항에 대해 살펴봅니다.

데이터 세트를 업로드한 후에는 몇 가지 옵션을 사용할 수 있습니다.

* **사용자 지정 모델 학습** 탭으로 이동하여 사용자 지정 모델을 학습할 수 있습니다.
* **테스트 모델** 탭으로 이동하여 오디오 전용 데이터로 품질을 시각적으로 검사하거나 오디오 + 휴먼 레이블 대화 기록 데이터를 사용하여 정확도를 평가할 수 있습니다.

### <a name="upload-data-using-speech-to-text-rest-api-v30"></a>음성 텍스트 REST API v3.0을 사용하여 데이터 업로드

음성 텍스트 REST API [v3.0을](rest-speech-to-text.md#speech-to-text-rest-api-v30) 사용하여 사용자 지정 모델과 관련된 작업을 자동화할 수 있습니다. 특히 데이터 세트를 업로드하는 데 사용할 수 있습니다. Speech Studio 로컬 파일 옵션을 사용하여 큰 파일을 업로드할 수 없으므로 데이터 세트 *파일이* 128MB를 초과하는 경우 특히 유용합니다. (이전 섹션에서 설명한 것과 동일한 용도로 Speech Studio *Azure Blob 또는 공유 위치* 옵션을 사용할 수도 있습니다.)

데이터 세트를 만들고 업로드하려면 데이터 세트 [만들기](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset) 요청을 사용합니다.

**만든 데이터 세트 및 Speech Studio 프로젝트 REST API**

요청 본문에 특수 매개 변수를 지정하지 않으면 v3.0 REST API 음성 텍스트로 만든 데이터 세트는 Speech Studio 프로젝트에 *연결되지 않습니다(아래* 참조). REST API 통해 수행되는 경우 모델 사용자 지정 작업에는 Speech Studio 프로젝트와의 연결이 필요하지 *않습니다.*

Speech Studio 로그온하면 연결되지 않은 개체(예: 프로젝트 참조 없이 REST API 통해 업로드된 데이터 세트)가 발견되면 해당 사용자 인터페이스가 사용자에게 알리고 이러한 개체를 기존 프로젝트에 연결하도록 제안합니다. 

업로드하는 동안 새 데이터 세트를 Speech Studio 기존 프로젝트에 연결하려면 데이터 [세트 만들기를](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset) 사용하고 다음 형식에 따라 요청 본문을 채웁니다.
```json
{
  "kind": "Acoustic",
  "contentUrl": "https://contoso.com/mydatasetlocation",
  "locale": "en-US",
  "displayName": "My speech dataset name",
  "description": "My speech dataset description",
  "project": {
    "self": "https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/projects/c1c643ae-7da5-4e38-9853-e56e840efcb2"
  }
}
```

요소에 필요한 Project URL은 `project` [프로젝트 받기](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetProjects) 요청을 통해 가져올 수 있습니다.

## <a name="audio--human-labeled-transcript-data-for-trainingtesting"></a>학습/테스트용 오디오 + 휴먼 레이블 대화 기록 데이터

오디오 + 휴먼 레이블 대화 기록 데이터는 학습 및 테스트 목적으로 모두 사용할 수 있습니다. 약간의 악센트, 말하기 스타일, 배경 소음과 같은 음향적인 측면을 개선하거나 오디오 파일을 처리할 때 Microsoft의 음성 텍스트 전환 정확성을 측정하려면 비교할 수 있도록 휴먼 레이블 대화 기록(단어별)을 제공해야 합니다. 휴먼 레이블 대화 기록은 시간이 많이 소요되는 일이지만 정확성을 평가하고 사용 사례에 맞게 모델을 학습하는 데 필요합니다. 인식 기능의 향상은 제공된 데이터에 따라 달라집니다. 따라서 고품질의 대화 기록만 업로드하는 것이 중요합니다.

오디오 파일에는 시작과 끝에 침묵이 있을 수 있습니다. 가능하면 각 샘플 파일에서 음성 앞과 뒤에 0.5초 정도의 침묵을 포함합니다. 녹음 볼륨이 낮거나 배경 노이즈가 심한 오디오는 유용하지 않지만 사용자 지정 모델을 손상시키지는 않습니다. 오디오 샘플을 수집하기 전에 항상 마이크 및 신호 처리 하드웨어를 업그레이드하는 것이 좋습니다.

| 속성                 | 값                               |
|--------------------------|-------------------------------------|
| 파일 형식              | RIFF(WAV)                          |
| 샘플 속도              | 8,000Hz 또는 16,000Hz               |
| 채널                 | 1(mono)                            |
| 오디오 당 최대 길이 | 2시간(테스트)/60초(학습) |
| 샘플 형식            | PCM, 16비트                         |
| 보관 형식           | .zip                                |
| 최대 zip 크기         | 2GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 학습 및 테스트 데이터를 업로드할 때 .zip 파일 크기는 2GB를 초과할 수 없습니다. *단일* 데이터 세트에서만 테스트할 수 있으며 적절한 파일 크기로 유지해야 합니다. 또한 각 학습 파일은 60초를 초과할 수 없습니다. 그렇지 않으면 오류가 발생합니다.

단어 삭제 또는 대체와 같은 문제를 해결하려면 인식 기능을 개선하기 위해 상당한 양의 데이터가 필요합니다. 일반적으로 1~20시간의 오디오에 대한 단어별 대화 기록을 제공하는 것이 좋습니다. 그러나 30분 정도의 대화 기록을 사용하더라도 인식 결과를 개선하는 데 도움이 될 수 있습니다. 모든 WAV 파일에 대한 전사는 단일 일반 텍스트 파일에 포함되어야 합니다. 전사 파일의 각 줄은 오디오 파일 중 하나의 이름을 포함하고 그 뒤에 해당 전사가 와야 합니다. 파일 이름과 전사는 탭(\t)으로 구분 해야 합니다.

예를 들면 다음과 같습니다.

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> 전사는 UTF-8 BOM으로 인코딩해야 합니다.

전사는 시스템에서 처리할 수 있도록 텍스트로 정규화됩니다. 그러나 데이터를 Speech Studio에 업로드하기 전에 수행해야 하는 몇 가지 중요한 정규화 작업이 있습니다. 대화 기록을 준비할 때 사용해야 하는 적절한 언어는 [휴먼 레이블 대화 기록을 만드는 방법](how-to-custom-speech-human-labeled-transcriptions.md)을 참조하세요.

오디오 파일 및 해당 대화 기록을 수집한 후 <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech Studio</a>에 업로드하기 전에 단일 .zip 파일로 패키징합니다. 다음은 세 개의 오디오 파일과 휴먼 레이블 대화 기록 파일이 있는 예제 데이터 세트입니다.

> [!div class="mx-imgBorder"]
> ![Speech Portal에서 오디오 선택](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Speech Service 구독에 권장되는 지역 목록은 [Azure 계정 설정](custom-speech-overview.md#set-up-your-azure-account)을 참조하세요. 이러한 지역 중 하나에서 Speech 구독을 설정하면 모델을 학습하는 데 걸리는 시간이 줄어듭니다. 다른 지역에서는 하루 1시간 정도의 오디오를 학습시키는 데 비해 이러한 지역에서는 하루 10시간 정도의 오디오를 학습할 수 있습니다. 1주일 이내에 모델 학습을 완료할 수 없는 경우 모델이 실패로 표시됩니다.

모든 기본 모델이 오디오 데이터를 통한 학습을 지원하지는 않습니다. 기본 모델이 지원하지 않는 경우 서비스는 오디오를 무시하고 대화 기록 텍스트로 학습합니다. 이 경우 학습은 관련 텍스트가 있는 학습과 동일합니다. 오디오 데이터를 통한 학습을 지원하는 기본 모델의 목록을 확인하려면 [언어 지원](language-support.md#speech-to-text)을 참조하세요.

## <a name="plain-text-data-for-training"></a>학습용 일반 텍스트 데이터

도메인 관련 문장을 사용하여 제품 이름 또는 산업별 전문 용어를 인식할 때 정확성을 향상시킬 수 있습니다. 단일 텍스트 파일에 문장을 제공합니다. 정확도를 높이기 위해 예상되는 음성 발화에 더 가까운 텍스트 데이터를 사용합니다. 

일반 텍스트를 사용한 학습은 일반적으로 몇 분 이내에 완료됩니다.

문장을 사용하여 사용자 지정 모델을 만들려면 샘플 발화 목록을 제공해야 합니다. 발화는 완전하거나 문법적으로 정확할 필요는 _없지만_ 프로덕션에서 기대하는 음성 입력을 정확하게 반영해야 합니다. 특정 용어의 가중치를 높이려면 특정 용어가 포함된 문장을 여러 개 추가합니다.

일반적으로 모델 적응은 학습 텍스트가 프로덕션에서 예상되는 실제 텍스트와 최대한 가까운 경우에 가장 효과적입니다. 개선하려는 도메인별 용어 및 구를 학습 텍스트에 포함해야 합니다. 가능하면 하나의 문장이나 키워드를 별도의 줄로 제어해 봅니다. 사용자에게 중요한 키워드 및 구(예: 제품 이름)의 경우 여러 번 복사할 수 있습니다. 그러나 너무 많이 복사하면 전반적인 인식 요금에 영향을 줄 수 있습니다.

이 표를 사용하여 발화에 대한 관련 데이터 파일의 형식이 올바른지 확인합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM |
| 줄당 발언의 # | 1 |
| 최대 파일 크기 | 200MB |

또한 다음과 같은 제한 사항도 고려할 수 있습니다.

* 문자, 단어 또는 단어 그룹을 네 번 이상 반복하지 않습니다. 예를 들어 "aaaa", "yeah yeah yeah yeah", "that's it that's it that's it that's it" 등입니다. Speech Service는 반복이 너무 많은 줄을 삭제할 수 있습니다.
* `U+00A1`보다 높은 특수 문자나 UTF-8 문자는 사용하지 않습니다.
* URI는 거부됩니다.
* 일부 언어의 경우(예: 일본어 또는 한국어) 많은 양의 텍스트 데이터를 가져오는 데 시간이 오래 걸릴 수 있습니다. 업로드된 데이터를 각각 최대 20,000줄의 텍스트 파일로 분할하는 것이 좋습니다.

## <a name="structured-text-data-for-training-public-preview"></a>학습을 위한 구조적 텍스트 데이터(공개 미리 보기)

예상 발화가 특정 패턴을 따르는 경우가 많습니다. 한 가지 일반적인 패턴은 발화가 목록의 단어 또는 구에 의해서만 다르다는 것입니다. 이에 대한 예로는 "에 대한 질문이 `product` 있습니다."가 있습니다. 여기서 `product` 은 가능한 제품 목록입니다. 또는 "Make that `object` `color` ," 여기서 는 `object` 기하 도형 목록이며 `color` 는 색 목록입니다. 학습 데이터 만들기를 간소화하고 사용자 지정 언어 모델 내에서 더 나은 모델링을 사용하도록 설정하기 위해 markdown 형식의 구조적 텍스트를 사용하여 항목 목록을 정의한 다음 학습 발언 내에서 참조할 수 있습니다. 또한 markdown 형식은 단어의 발음 지정도 지원합니다. markdown `.md` 파일에는 확장명이 있어야 합니다. markdown의 구문은 Language Understanding 모델, 특히 목록 엔터티 및 발화 예제의 구문과 동일합니다. 전체 markdown 구문에 대한 자세한 내용은 <a href="/azure/bot-service/file-format/bot-builder-lu-file-format" target="_blank">Language Understanding markdown을 참조하세요.</a>

Markdown 형식의 예는 다음과 같습니다.

```markdown
// This is a comment

// Here are three separate lists of items that can be referenced in an example sentence. You can have up to 10 of these
@ list food =
- pizza
- burger
- ice cream
- soda

@ list pet =
- cat
- dog

@ list sports =
- soccer
- tennis
- cricket
- basketball
- baseball
- football

// This is a list of phonetic pronunciations. 
// This adjusts the pronunciation of every instance of these word in both a list or example training sentences 
@ speech:phoneticlexicon
- cat/k ae t
- cat/f i l ai n

// Here are example training sentences. They are grouped into two sections to help organize the example training sentences.
// You can refer to one of the lists we declared above by using {@listname} and you can refer to multiple lists in the same training sentence
// A training sentence does not have to refer to a list.
# SomeTrainingSentence
- you can include sentences without a class reference
- what {@pet} do you have
- I like eating {@food} and playing {@sports}
- my {@pet} likes {@food}

# SomeMoreSentence
- you can include more sentences without a class reference
- or more sentences that have a class reference like {@pet} 
```

일반 텍스트와 마찬가지로 구조적 텍스트로 학습하는 데는 일반적으로 몇 분 정도 걸립니다. 또한 예제 문장 및 목록은 프로덕션에서 예상하는 음성 입력 형식을 반영해야 합니다.
발음 항목은 유니버설 전화 집합에 대한 설명을 [참조하세요.](phone-sets.md)

다음 표에서는 markdown 형식에 대 한 제한 및 기타 속성을 지정 합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM |
| 최대 파일 크기 | 200MB |
| 최대 예제 문장 수 | 50,000 |
| 최대 목록 클래스 수 | 10 |
| List 클래스의 최대 항목 수 | 4,000 |
| 최대 음성: phoneticlexicon 항목 수 | 15000 |
| 단어 당 최대 발음 수 | 2 |


## <a name="pronunciation-data-for-training"></a>학습용 발음 데이터

사용자가 접하거나 사용하게 될 표준 발음이 없는 특수 용어가 있는 경우 사용자 지정 발음 파일을 제공하여 인식을 향상시킬 수 있습니다. 사용자 지정 발음을 지 원하는 언어 목록은 [음성 텍스트 테이블](language-support.md#speech-to-text)의 **사용자 지정** 열에서 **발음** 을 참조 하세요.

> [!IMPORTANT]
> 사용자 지정 발음 파일을 사용하여 일반적인 단어의 발음을 변경하는 것은 권장되지 않습니다.

> [!NOTE]
> 이 유형의 발음 파일을 구조적 텍스트 학습 데이터와 결합할 수 없습니다. 구조적 텍스트 데이터의 경우 구조적 텍스트 markdown 형식에 포함 된 음성 발음 기능을 사용 합니다.

단일 텍스트 파일에 발음을 제공합니다. 여기에는 음성 발화의 예와 각각에 대한 사용자 지정 발음이 포함됩니다.

| 인식된/표시된 형식 | 발성 형식 |
|--------------|--------------------------|
| 3CPO | three c p o |
| CNTK | c n t k |
| IEEE | i triple e |

구어체는 철자로 풀어 쓴 음성 시퀀스로 문자, 단어, 음절 또는 세 가지 모두의 조합으로 구성될 수 있습니다.

다음 표를 사용하여 발음에 대한 관련 데이터 파일이 올바른 형식인지 확인합니다. 발음 파일은 작으며 크기는 몇 킬로바이트여야 합니다.

| 속성 | 값 |
|----------|-------|
| 텍스트 인코딩 | UTF-8 BOM(영어의 경우 ANSI도 지원됨) |
| 한 줄에 #개의 발음 | 1 |
| 최대 파일 크기 | 1MB(무료 계층의 경우 1KB) |

## <a name="audio-data-for-testing"></a>테스트용 오디오 데이터

오디오 데이터는 Microsoft의 기본 음성 텍스트 변환 모델 또는 사용자 지정 모델의 정확도를 테스트하는 데 가장 적합합니다. 오디오 데이터는 특정 모델의 성능과 관련하여 음성의 정확도를 검사하는 데 사용됩니다. 모델의 정확도를 수량화하려는 경우 [오디오 + 휴먼 레이블 대화 기록](#audio--human-labeled-transcript-data-for-trainingtesting)을 사용합니다.

Custom Speech에는 다음 속성을 가진 오디오 파일이 필요합니다.

| 속성                 | 값                 |
|--------------------------|-----------------------|
| 파일 형식              | RIFF(WAV)            |
| 샘플 속도              | 8,000Hz 또는 16,000Hz |
| 채널                 | 1(mono)              |
| 오디오 당 최대 길이 | 2시간               |
| 샘플 형식            | PCM, 16비트           |
| 보관 형식           | .zip                  |
| 최대 보관 크기     | 2GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 학습 및 테스트 데이터를 업로드할 때 .zip 파일 크기는 2GB를 초과할 수 없습니다. 학습에 더 많은 데이터가 필요할 경우 여러 개의 .zip 파일로 나눠 별도로 업로드합니다. 나중에 *여러* 데이터 세트에서 학습하도록 선택할 수 있습니다. 그러나 *단일* 데이터 세트에서만 테스트할 수 있습니다.

<a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX</a>를 사용하여 오디오 속성을 확인하거나 기존 오디오를 적절한 형식으로 변환합니다. 다음은 몇 가지 SoX 명령 예제입니다.

| 활동 | SoX 명령 |
|---------|-------------|
| 오디오 파일 형식을 확인합니다. | `sox --i <filename>` |
| 오디오 파일을 단일 채널 16비트, 16KHz로 변환합니다. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="next-steps"></a>다음 단계

* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [사용자 지정 모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](./how-to-custom-speech-train-model.md)
