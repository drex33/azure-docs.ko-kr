---
title: 모바일로 모델 내보내기 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 모바일 애플리케이션을 만드는 데 사용할 모델을 내보내거나 실시간 분류를 위해 로컬로 실행하는 방법을 보여줍니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 8b17ae73eb923e55d9bc1ffbbd8b66c16cec8010
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461326"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>모바일 디바이스에 사용할 모델 내보내기

Custom Vision Service를 사용하면 분류자를 내보내서 오프라인으로 실행할 수 있습니다. 내보낸 분류자를 애플리케이션에 포함하여 디바이스에서 실행하면 실시간 분류가 가능합니다.

## <a name="export-options"></a>내보내기 옵션

Custom Vision Service는 다음과 같은 내보내기를 지원합니다.

* __Tensorflow__ for __Android__.
* React, Angular 및 Vue와 같은 JavaScript 프레임워크용 **TensorflowJS**. 이는 **Android** 및 **iOS** 디바이스 모두에서 실행됩니다.
* __CoreML__ for __iOS11__.
* __Windows ML__, **Android** 및 **iOS** 용 __ONNX__.
* __[Vision AI 개발자 키트](https://azure.github.io/Vision-AI-DevKit-Pages/)__ .
* Windows, Linux 또는 ARM 아키텍처용 __Docker 컨테이너__. 컨테이너에는 Custom Vision API를 사용할 Tensorflow 모델 및 서비스 코드가 포함됩니다.

> [!IMPORTANT]
> Custom Vision Service는 __소형__ 도메인만 내보냅니다. 소형 도메인을 통해 생성된 모델은 모바일 디바이스의 실시간 분류 제약 조건에 맞게 최적화됩니다. 소형 도메인을 사용하여 만든 분류자는 교육 데이터의 양이 동일한 표준 도메인보다 정확도가 살짝 떨어질 수 있습니다.
>
> 분류자를 향상하는 방법은 [분류자 향상](getting-started-improving-your-classifier.md) 문서를 참조하세요.

## <a name="convert-to-a-compact-domain"></a>소형 도메인으로 변환

> [!NOTE]
> 이 섹션의 단계는 소형 도메인으로 설정되지 않은 기존 모델이 있는 경우에만 적용됩니다.

기존 모델의 도메인을 변환하려면 다음 단계를 수행합니다.

1. [Custom Vision 웹 사이트](https://customvision.ai)에서 __홈__ 아이콘을 선택하여 프로젝트 목록을 봅니다.

    ![홈 아이콘 및 프로젝트 목록 이미지](./media/export-your-model/projects-list.png)

1. 프로젝트를 선택한 다음, 페이지 오른쪽 위에서 __기어__ 아이콘을 선택합니다.

    ![기어 아이콘 이미지](./media/export-your-model/gear-icon.png)

1. __도메인__ 섹션에서 __소형__ 도메인 중 하나를 선택합니다. __변경 내용 저장__ 을 선택하여 변경 내용을 저장합니다. 

    > [!NOTE]
    > Vision AI 개발자 키트의 경우 __일반(소형)__ 도메인을 사용하여 프로젝트를 만들고 **내보내기 기능** 섹션에서 **Vision AI 개발자 키트** 옵션을 지정해야 합니다.

    ![도메인 선택 이미지](./media/export-your-model/domains.png)

1. 페이지 위쪽에서 __교육__ 을 선택하여 새 도메인 사용 방법을 다시 학습합니다.

## <a name="export-your-model"></a>모델 내보내기

재교육 후 모델을 내보내려면 다음 단계를 수행합니다.

1. **성능** 탭으로 이동하여 __내보내기__ 를 선택합니다. 

    ![내보내기 아이콘 이미지](./media/export-your-model/export.png)

    > [!TIP]
    > __내보내기__ 항목을 사용할 수 없으면 선택한 반복에 소형 도메인이 사용되지 않습니다. 이 페이지의 __반복__ 섹션을 사용하여 소형 도메인을 사용하는 반복을 선택한 다음, __내보내기__ 를 선택합니다.

1. 원하는 내보내기 형식을 선택한 다음, __내보내기__ 를 선택하여 모델을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

다음 문서 또는 샘플 중 하나를 탐색하여 내보낸 모델을 애플리케이션에 통합합니다.

* [Python에서 Tensorflow 모델 사용](export-model-python.md)
* [Windows Machine Learning에서 ONNX 모델 사용](custom-vision-onnx-windows-ml.md)
* Swift를 사용한 실시간 이미지 분류를 위한 [iOS 애플리케이션 내 CoreML 모델](https://go.microsoft.com/fwlink/?linkid=857726) 샘플을 참조하세요.
* Android에서 실시간 이미지 분류를 위한 [Android 애플리케이션 내 Tensorflow 모델](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) 샘플을 참조하세요.
* Xamarin iOS 앱에서 실시간 이미지 분류를 위한 [Xamarin을 사용하는 CoreML 모델](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) 샘플을 참조하세요.
