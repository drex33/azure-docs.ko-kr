---
title: Custom Speech 모델 학습 및 배포 - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Speech 모델을 학습하고 배포하는 방법을 알아봅니다. 음성 텍스트 변환 모델을 학습하면 Microsoft 기준선 모델이나 사용자 지정 모델에 대해 인식 정확도를 개선할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: pafarley
ms.openlocfilehash: 73076c141f28bc22450c87491053130ace6b5793
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540760"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Custom Speech 모델 학습 및 배포

이 문서에서는 Custom Speech 모델을 학습하고 배포하는 방법을 알아봅니다. 음성 텍스트 변환 모델을 학습하면 Microsoft 기준선 모델에 대해 인식 정확도를 개선할 수 있습니다. 모델 학습을 위해 휴먼 레이블 대화 기록 및 관련 텍스트를 사용합니다. 이러한 데이터 세트는 이전에 업로드된 오디오 데이터와 함께 음성 텍스트 변환 모델을 구체화하고 학습하는 데 사용됩니다.

## <a name="use-training-to-resolve-accuracy-problems"></a>학습을 사용하여 정확도 문제 해결

기본 모델에서 인식 문제가 발생하는 경우 휴먼 레이블 대화 기록 및 관련 데이터를 사용하여 사용자 지정 모델을 학습하고 정확도를 개선할 수 있습니다. 이 표를 사용하여 문제를 해결하는 데 사용할 데이터 세트를 결정합니다.

| 사용 사례 | 데이터 형식 |
| -------- | --------- |
| 의료 용어 또는 IT 전문 용어와 같은 업계별 어휘 및 문법에 대한 인식 정확도 향상 | 관련 텍스트(문장/발화) |
| 제품 이름 또는 머리글자어와 같이 비표준 발음을 포함하는 단어 또는 용어의 발음 및 표시된 형태를 정의합니다. | 관련 텍스트(발음) |
| 말하는 스타일, 악센트 또는 특정 배경 소음에 대한 인식 정확도 향상 | 오디오 + 휴먼 레이블 대화 기록 |

## <a name="train-and-evaluate-a-model"></a>모델 학습 및 평가

모델을 학습하는 첫 번째 단계는 학습 데이터를 업로드하는 것입니다. 휴먼 레이블 대화 기록 및 관련 텍스트(발화 및 발음)를 준비하는 단계별 지침은 [데이터 준비 및 테스트](./how-to-custom-speech-test-and-train.md)를 참조하세요. 학습 데이터를 업로드한 후 이러한 지침에 따라 모델에 대한 학습을 시작합니다.

1. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 로그인합니다. 오디오 + 휴먼 레이블 대화 기록 데이터 세트를 사용하여 모델을 학습하려면 학습을 위한 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account)에서 음성 구독을 선택합니다.
2. **음성 텍스트 변환** > **Custom Speech** >  **[프로젝트 이름]**  > **학습** 으로 이동합니다.
3. **모델 학습** 을 선택합니다.
4. 학습에 **이름** 및 **설명** 을 지정합니다.
5. **시나리오 및 기준 모델** 목록에서 도메인에 가장 적합한 시나리오를 선택합니다. 어떤 시나리오를 선택할지 잘 모르는 경우 **일반** 을 선택합니다. 기본 모델은 학습에 대한 시작점입니다. 일반적으로 최신 모델이 가장 적합합니다.
6. **학습 데이터 선택** 페이지에서 학습에 사용할 관련 텍스트 데이터 세트 또는 오디오 + 휴먼 레이블 대화 기록 데이터 세트를 하나 이상 선택합니다.

> [!NOTE]
> 새 모델을 학습할 때 관련 텍스트로 시작합니다. 오디오 + 휴먼 레이블 대화 기록을 사용하여 학습하는 데 훨씬 더 오래 걸릴 수 있습니다 **(최대 [수일](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)** ).

> [!NOTE]
> 모든 기본 모델이 오디오를 통한 학습을 지원하지는 않습니다. 기본 모델이 이를 지원하지 않는 경우 Speech Service는 대본의 텍스트만 사용하고 오디오는 무시합니다. [언어 지원](language-support.md#speech-to-text)을 참조하여 오디오 데이터를 통한 학습을 지원하는 기본 모델의 목록을 확인하세요.

> [!NOTE]
> 학습에 사용되는 기본 모델을 변경하고 학습 데이터 세트에 오디오가 있는 경우 선택한 새 기본 모델이 [오디오 데이터를 통한 학습을 지원](language-support.md#speech-to-text)하는지 *항상* 확인합니다. 이전에 사용된 기본 모델에서 오디오 데이터를 통한 학습을 지원하지 않고 학습 데이터 세트에 오디오가 포함된 경우 새 기본 모델의 학습 시간이 몇 시간에서 며칠 이상으로 **크게** 증가할 수 있습니다. Speech Service 구독이 학습 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account)에 있지 **않은** 경우에 특히 그렇습니다.
>
> 위의 단락에 설명된 문제가 발생한 경우 데이터 세트의 오디오 양을 줄이거나 완전히 제거하여 텍스트만 남겨두면 학습 시간을 빠르게 줄일 수 있습니다. 두 번째 옵션은 Speech Service 구독이 학습 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account)에 있지 **않은** 경우에 매우 권장됩니다.

7. 학습이 완료되면 새로 학습된 모델에서 정확도 테스트를 수행할 수 있습니다. 이 단계는 선택 사항입니다.
8. **만들기** 를 선택하여 사용자 지정 모델을 빌드합니다.

**학습** 테이블은 새 모델에 해당하는 새 항목을 표시합니다. 또한 테이블에는 **처리 중**, **성공**, **실패** 와 같은 상태가 표시됩니다.

Custom Speech 모델 정확도를 평가하고 개선하는 [방법](how-to-custom-speech-evaluate-data.md)을 참조하세요. 정확도 테스트를 선택하는 경우 모델의 성능에 대한 사실적인 감각을 얻기 위해 모델에 사용한 것과 다른 어쿠스틱 데이터 세트를 선택하는 것이 중요합니다.

> [!NOTE]
> 기본 모델과 사용자 지정 모델은 모두 특정 날짜까지만 사용할 수 있습니다([모델 및 엔드포인트 수명 주기](./how-to-custom-speech-model-and-endpoint-lifecycle.md) 참조). Speech Studio는 각 모델 및 엔드포인트에 대한 **만료** 열에 이 날짜를 표시합니다. 엔드포인트 또는 일괄 처리 대본에 대한 해당 날짜 요청이 실패하거나 기본 모델로 대체될 수 있습니다.
>
> 가장 최근의 기본 모델을 사용하여 모델을 다시 학습하면 정확성을 향상시키고 모델이 만료되는 것을 방지할 수 있습니다.

## <a name="deploy-a-custom-model"></a>사용자 지정 모델 배포

데이터를 업로드 및 검사하고, 정확도를 평가하고, 사용자 지정 모델을 학습한 후, 앱, 도구 및 제품과 함께 사용할 사용자 지정 엔드포인트를 배포할 수 있습니다. 

사용자 지정 엔드포인트를 만들려면 [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 로그인합니다. 페이지 맨 위에 있는 **Custom Speech** 메뉴에서 **배포** 를 선택합니다. 처음 실행하는 경우에는 표에 나열된 엔드포인트가 없다는 것을 알 수 있습니다. 엔드포인트를 만든 후 이 페이지를 사용하여 배포된 각 엔드포인트를 추적할 수 있습니다.

다음으로, **엔드포인트 추가** 를 선택하고 사용자 지정 엔드포인트에 대한 **이름** 및 **설명** 을 입력합니다. 그런 다음, 엔드포인트와 연결하려는 사용자 지정 모델을 선택합니다.  이 페이지에서 로깅을 사용하도록 설정할 수도 있습니다. 로깅을 사용하면 엔드포인트 트래픽을 모니터링할 수 있습니다. 로깅을 사용하지 않도록 설정하면 트래픽이 저장되지 않습니다.

![새 엔드포인트 페이지를 보여 주는 스크린샷.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 사용 약관 및 가격 정보에 동의해야 합니다.

다음 **만들기** 를 선택합니다. 그러면 **배포** 페이지로 돌아갑니다. 이제 사용자 지정 엔드포인트에 해당하는 항목이 표에 포함되어 있습니다. 엔드포인트의 상태는 현재 상태를 표시합니다. 사용자 지정 모델을 사용하여 새 엔드포인트를 인스턴스화하는 데 최대 30분이 걸릴 수 있습니다. 배포 상태가 **완료** 이면 엔드포인트를 사용할 준비가 완료된 것입니다.

엔드포인트가 배포된 후에는 엔드포인트 이름이 링크로 표시됩니다. 엔드포인트 키, 엔드포인트 URL 및 샘플 코드와 같은 엔드포인트 관련 정보를 표시하려면 링크를 선택합니다. 만료 날짜를 적어두고 중단 없는 서비스를 보장하기 위해 해당 날짜 이전에 엔드포인트의 모델을 업데이트합니다.

## <a name="view-logging-data"></a>로깅 데이터 보기

**배포** 아래의 엔드포인트 페이지로 이동하면 로깅 데이터를 내보낼 수 있습니다.
> [!NOTE]
>로깅 데이터는 Microsoft 소유 스토리지에서 30일 동안 사용할 수 있습니다. 나중에 제거됩니다. 고객 소유 스토리지 계정이 Cognitive Services 구독에 연결된 경우 로깅 데이터는 자동으로 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 모델을 사용하는 방법 알아보기](how-to-specify-source-language.md)

## <a name="additional-resources"></a>추가 자료

- [데이터 준비 및 테스트](./how-to-custom-speech-test-and-train.md)
- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)
