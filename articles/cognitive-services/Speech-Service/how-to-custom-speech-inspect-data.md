---
title: Custom Speech의 데이터 품질 검사 - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Speech는 오디오 데이터를 해당 인식 결과와 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. 업로드된 오디오를 재생하여 제공된 인식 결과가 올바른지 확인할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: pafarley
ms.openlocfilehash: 5f127a588b4b104c8c09771b6ede52f8049d2110
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543616"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech 데이터 검사

> [!NOTE]
> 이 페이지에서는 [Custom Speech에 대한 테스트 데이터 준비](./how-to-custom-speech-test-and-train.md)를 읽고 검사용 데이터 세트를 업로드했다고 가정합니다.

Custom Speech는 오디오 데이터를 해당 인식 결과와 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. [Speech Studio](https://speech.microsoft.com/customspeech)에서 업로드된 오디오를 재생하여 제공된 인식 결과가 올바른지 확인할 수 있습니다. 이 도구를 사용하면 Microsoft의 기본 음성 텍스트 변환 모델의 품질을 검사하거나, 학습된 사용자 지정 모델을 검사하거나, 두 모델의 음성 텍스트를 비교할 수 있습니다.

이 문서에서는 Microsoft의 기본 음성 텍스트 변환 모델 및/또는 학습된 사용자 지정 모델의 품질을 시각적으로 검사하는 방법을 알아봅니다. 또한 온라인 음성 텍스트 편집기를 사용하여 레이블이 지정된 오디오 데이터 세트를 만들고 구체화하는 방법도 알아봅니다.

## <a name="create-a-test"></a>테스트 만들기

지침에 따라 테스트를 만듭니다.

1. [Speech Studio](https://speech.microsoft.com/customspeech)에 로그인합니다.
2. **음성 텍스트 변환 > Custom Speech > [프로젝트 이름] > 테스트** 로 이동합니다.
3. **테스트 추가** 를 클릭합니다.
4. **품질 검사(오디오 전용 데이터)** 를 선택합니다. 테스트에 이름 및 설명을 지정하고 오디오 데이터 세트를 선택합니다.
5. 테스트하려는 모델을 최대 2개 선택합니다.
6. **만들기** 를 클릭합니다.

테스트를 성공적으로 만든 후에는 모델이 지정한 오디오 데이터 세트를 텍스트로 변환하는 방법을 확인하거나 두 모델의 결과를 나란히 비교할 수 있습니다.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>병렬 모델 비교

테스트 상태가 _성공_ 인 경우 테스트 항목 이름을 클릭하여 테스트 세부 정보를 확인합니다. 이 세부 정보 페이지에는 데이터 세트의 모든 발화가 나열되고, 비교 중인 두 모델의 인식 결과가 표시됩니다.

병렬 비교를 검사하기 위해 삽입, 삭제 및 대체를 비롯한 다양한 오류 유형을 전환할 수 있습니다. 오디오를 수신하고 두 개의 음성 텍스트 변환 모델에 대한 휴먼 레이블 대화 기록과 결과를 보여 주는 각 열의 인식 결과를 비교하면 요구 사항을 충족하는 모델과 개선이 필요한 모델을 결정할 수 있습니다.

병렬 모델 테스트는 애플리케이션에 가장 적합한 음성 인식 모델의 유효성을 검사하는 데 유용합니다. 텍스트로 변환된 오디오에 필요한 정확도의 목표 측정값은 [정확도 평가](how-to-custom-speech-evaluate-data.md)에 있는 지침을 따릅니다.

## <a name="online-transcription-editor"></a>온라인 대화 내용 기록 편집기

온라인 대화 내용 기록 편집기를 사용하면 Custom Speech에서 오디오 대화 내용 기록을 쉽게 사용할 수 있습니다. 편집기의 주요 사용 사례는 다음과 같습니다. 

* 오디오 데이터만 있지만 모델 학습에 사용하기 위해 처음부터 정확한 오디오 + 휴먼 레이블 데이터 세트를 빌드하려고 합니다.
* 오디오 + 휴먼 레이블 데이터 세트가 이미 있지만 대화 내용 기록에 오류 또는 결함이 있습니다. 편집기를 사용하면 대화 내용 기록을 빠르게 수정하여 최적의 학습 정확도를 얻을 수 있습니다.

대화 내용 기록 편집기를 사용하는 데 있어서의 유일한 요구 사항은 오디오 데이터(오디오 전용 또는 오디오 + 대화 내용 기록)를 업로드하는 것입니다.

### <a name="import-datasets-to-editor"></a>편집기로 데이터 세트 가져오기

편집기로 데이터를 가져오려면 **Custom Speech > [프로젝트] > 편집기** 로 이동합니다.

![편집기 탭](media/custom-speech/custom-speech-editor-detail.png)

다음 단계를 사용하여 데이터를 가져옵니다.

1. **데이터 가져오기** 를 클릭합니다.
1. 새 데이터 세트를 만들고 설명을 입력합니다.
1. 데이터 세트를 선택합니다. 여러 항목을 선택할 수 있고, 오디오 데이터만 선택할 수도 있으며, 오디오 + 휴먼 레이블 데이터를 선택할 수 있습니다.
1. 오디오만 있는 데이터의 경우 기본 모델을 사용하여 편집기로 가져온 후 자동으로 컴퓨터 대화 내용 기록을 생성할 수 있습니다.
1. **가져오기** 를 클릭합니다.

데이터를 가져온 후에는 데이터 세트를 클릭하여 편집을 시작할 수 있습니다.

> [!TIP]
> 데이터 세트를 선택하고 **편집기로 내보내기** 를 클릭하여 데이터 세트를 편집기로 직접 가져올 수도 있습니다.

### <a name="edit-transcription-by-listening-to-audio"></a>오디오를 수신하여 대화 내용 기록 편집

데이터 업로드에 성공한 후 각 항목 이름을 클릭하여 데이터의 세부 정보를 확인합니다. **이전** 및 **다음** 을 사용하여 각 파일 사이를 이동할 수도 있습니다.

세부 정보 페이지에는 각 오디오 파일의 모든 세그먼트가 나열되고, 원하는 발화를 클릭할 수 있습니다. 각 발화에 대해 오디오를 재생하고, 음성 텍스트를 검사하고, 삽입, 삭제 또는 자막 오류가 있는 경우 대화 내용 기록을 편집할 수 있습니다. 오류 유형에 대한 자세한 내용은 [데이터 평가 방법](how-to-custom-speech-evaluate-data.md)을 참조하세요.

![편집기 페이지](media/custom-speech/custom-speech-editor.png)

편집 후 **저장** 단추를 클릭합니다.

### <a name="export-datasets-from-the-editor"></a>편집기에서 데이터 세트 내보내기

**데이터** 탭으로 데이터 세트를 내보내려면 데이터 세부 정보 페이지로 이동하고 **내보내기** 단추를 클릭하여 모든 파일을 새 데이터 세트로 내보냅니다. 마지막으로 편집한 시간, 오디오 시간 등을 통해 파일을 필터링하여 원하는 파일을 부분적으로 선택할 수도 있습니다. 

![데이터 내보내기](media/custom-speech/custom-speech-editor-export.png)

데이터로 내보낸 파일은 새로운 데이터 세트로 사용되며, 기존 데이터/학습/테스트 엔터티에 영향을 미치지 않습니다.

## <a name="next-steps"></a>다음 단계

- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
- [모델 개선](./how-to-custom-speech-evaluate-data.md)
- [모델 배포](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>추가 리소스

- [Custom Speech용 데이터 준비](./how-to-custom-speech-test-and-train.md)