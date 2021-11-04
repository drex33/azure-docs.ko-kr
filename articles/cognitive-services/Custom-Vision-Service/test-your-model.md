---
title: 모델 테스트 및 다시 학습 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 이미지를 테스트한 다음, 이 이미지를 사용하여 Custom Vision 서비스에서 모델을 다시 학습하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: b326a610d3ac946d8fd610b072e882df3a4b5281
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450460"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Custom Vision Service를 사용하여 모델 테스트 및 다시 학습

모델을 학습한 후 로컬에 저장된 이미지 또는 온라인 이미지를 사용하여 빠르게 테스트할 수 있습니다. 테스트는 가장 최근에 학습된 모델 반복을 사용합니다.

## <a name="test-your-model"></a>모델 테스트

1. [Custom Vision 웹 페이지](https://customvision.ai)에서 프로젝트를 선택합니다. 맨 위 메뉴 모음의 오른쪽에서 **빠른 테스트** 를 선택합니다. 이 작업은 **빠른 테스트** 라는 창을 엽니다.

    ![빠른 테스트 단추는 창의 오른쪽 위에 표시됩니다.](./media/test-your-model/quick-test-button.png)

2. **빠른 테스트** 창에서 **이미지 제출** 필드를 클릭하고 테스트에 사용할 이미지의 URL을 입력합니다. 로컬에 저장된 이미지를 대신 사용하려면 **로컬 파일 찾아보기** 단추를 클릭하고 로컬 이미지 파일을 선택합니다.

    ![이미지 제출 페이지 이미지](./media/test-your-model/submit-image.png)

선택한 이미지가 페이지 중간에 나타납니다. 그런 다음, 이미지 아래에 **태그** 및 **신뢰도** 라는 두 개의 열이 있는 표 형태로 결과가 표시됩니다. 결과를 본 후 **빠른 테스트** 창을 닫을 수 있습니다.

이제 이 테스트 이미지를 모델에 추가하고 모델을 다시 학습할 수 있습니다.

## <a name="use-the-predicted-image-for-training"></a>학습에 예측 이미지 사용

이전에 학습용으로 제출된 이미지를 사용하려면 다음 단계를 따르세요.

1. 분류자에 제출된 이미지를 보려면 [Custom Vision 웹 페이지](https://customvision.ai)를 열고 __예측__ 탭을 선택합니다.

    ![예측 탭 이미지](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > 기본 보기에는 현재 반복의 이미지가 표시됩니다. __반복__ 드롭다운 필드를 사용하여 이전 반복 중에 제출된 이미지를 볼 수 있습니다.

2. 이미지를 마우스로 가리키면 분류자가 예측한 태그가 표시됩니다.

    > [!TIP]
    > 분류자를 가장 개선할 수 있는 이미지가 맨 위에 오도록 이미지에 순위가 지정됩니다. 다른 정렬을 선택하려면 __정렬__ 섹션을 사용합니다.

    학습 데이터에 이미지를 추가하려면 이미지, 태그, __저장 후 닫기__ 를 차례로 선택합니다. 이미지가 __예측__ 에서 제거되고 학습 이미지에 추가됩니다. __학습 이미지__ 탭을 선택하면 해당 이미지를 볼 수 있습니다.

    ![태그 지정 페이지 이미지](./media/test-your-model/tag-image.png)

3. __학습__ 단추를 사용하여 분류자를 다시 학습합니다.

## <a name="next-steps"></a>다음 단계

[모델 개선](getting-started-improving-your-classifier.md)
