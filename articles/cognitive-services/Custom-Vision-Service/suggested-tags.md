---
title: Smart Labeler로 더 빠르게 이미지에 레이블 지정
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 Smart Labeler를 사용하여 이미지에 대해 제안된 태그를 생성하는 방법을 알아봅니다. 이렇게 하면 Custom Vision 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: be1e97aead471012e6d0e92931d670306cb1ddf4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458307"
---
# <a name="label-images-faster-with-smart-labeler"></a>Smart Labeler로 더 빠르게 이미지에 레이블 지정

이 가이드에서는 Smart Labeler를 사용하여 이미지에 대해 제안된 태그를 생성하는 방법을 알아봅니다. 이렇게 하면 Custom Vision 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다.

Custom Vision 모델용 이미지에 태그를 지정할 때 서비스는 모델의 최신 반복 학습을 사용하여 태그가 없는 이미지의 레이블을 예측합니다. 그런 다음 선택한 신뢰도 임계값 및 예측 불확실성을 기준으로 이러한 예측을 제안된 태그로 표시합니다. 그런 다음 제안을 확인하거나 변경하여 학습을 위해 이미지에 수동으로 태그를 지정하는 프로세스의 속도를 높일 수 있습니다.

## <a name="when-to-use-smart-labeler"></a>Smart Labeler를 사용해야 할 때

다음 제한 사항에 유의합니다.

* 콘텐츠가 이미 한 번 학습된 이미지에 대해 제안된 태그를 요청해야만 합니다. 막 학습하기 시작한 새 태그에 대한 제안은 받지 않습니다.

> [!IMPORTANT]
> Smart Labeler 기능은 일반 예측과 동일한 [가격 책정 모델](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)을 사용합니다. 이미지 집합에 대해 제안된 태그를 처음으로 트리거할 때 예측 호출과 동일한 비용이 청구됩니다. 그 후 서비스는 선택한 이미지에 대한 결과를 30일 동안 데이터베이스에 저장하고 해당 기간 내에 언제든지 무료로 액세스할 수 있습니다. 30일 후 제안된 태그를 다시 요청하면 요금이 청구됩니다.

## <a name="smart-labeler-workflow"></a>Smart Labeler 워크플로

다음 단계는 Smart Labeler 사용 방법을 보여 줍니다.

1. Custom Vision 프로젝트에 학습 이미지를 모두 업로드합니다.
1. 각 태그에 대해 동일한 수의 이미지를 선택하여 데이터 세트 부분에 레이블을 지정합니다.
    > [!TIP]
    > 나중에 제안할 모든 태그를 사용해야 합니다.
1. 학습 프로세스를 시작합니다.
1. 학습이 완료되었을 때 **태그 없음** 보기로 이동하고 왼쪽 창에서 **제안된 태그 가져오기** 단추를 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![제안된 태그 단추가 태그 없는 이미지 탭 아래에 표시됩니다.](./media/suggested-tags/suggested-tags-button.png)
1. 표시되는 팝업 창에서 제안을 원하는 이미지의 수를 설정합니다. 태그 없는 이미지의 일부에 대해서만 초기 태그 제안을 받아야 합니다. 이 프로세스를 반복하면 더 나은 태그 제안을 받게 됩니다.
1. 제안된 태그를 확인하여 올바르지 않은 태그를 수정합니다.
    > [!TIP]
    > 제안된 태그가 지정된 이미지는 예측 불확실성을 기준으로 정렬됩니다(값이 낮을수록 신뢰도가 높음). **불확실성을 기준으로 정렬** 옵션으로 정렬 순서를 변경할 수 있습니다. 순서를 **높음에서 낮음** 으로 설정하면 불확실성이 높은 예측을 먼저 수정한 다음 불확실성이 낮은 예측을 신속하게 확인할 수 있습니다.
    * 이미지 분류 프로젝트에서 일괄적으로 태그를 선택하고 확인할 수 있습니다. 지정되어 있는 제안된 태그를 기준으로 보기를 필터링하고 태그가 잘못 지정된 이미지를 선택 취소한 다음 나머지를 일괄적으로 확인합니다.
        > [!div class="mx-imgBorder"]
        > ![제안된 태그는 필터가 있는 IC에 대해 일괄 모드로 표시됩니다.](./media/suggested-tags/ic-batch-mode.png)

        갤러리에서 이미지를 선택하여 개별 이미지 모드에서 제안된 태그를 사용할 수도 있습니다.

        ![제안된 태그는 IC에 대해 개별 이미지 모드로 표시됩니다.](./media/suggested-tags/ic-individual-image-mode.png)
    * 개체 감지 프로젝트에서 일괄 확인은 지원되지 않지만 더 체계 있는 레이블 지정 환경을 위해 제안된 태그를 기준으로 필터링하고 정렬할 수 있습니다. 태그 없는 이미지의 썸네일에는 제안된 태그의 위치를 나타내는 경계 상자의 오버레이가 표시됩니다. 제안된 태그 필터를 선택하지 않은 경우 태그 없는 이미지는 모두 경계 상자를 오버레이하지 않고 표시됩니다.
        > [!div class="mx-imgBorder"]
        > ![제안된 태그는 필터가 있는 OD에 대해 일괄 모드로 표시됩니다.](./media/suggested-tags/od-batch-mode.png)

        개체 감지 태그를 확인하려면 갤러리의 개별 이미지별로 해당 태그를 적용해야 합니다.

        ![제안된 태그는 OD에 대해 개별 이미지 모드로 표시됩니다.](./media/suggested-tags/od-individual-image-mode.png)
1. 학습 프로세스를 다시 시작합니다.
1. 제안 품질에 만족할 때까지 위의 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

빠른 시작을 따라 Custom Vision 프로젝트 만들기 및 학습을 시작합니다.

* [분류자 빌드](getting-started-build-a-classifier.md)
* [개체 감지기 빌드](get-started-build-detector.md)