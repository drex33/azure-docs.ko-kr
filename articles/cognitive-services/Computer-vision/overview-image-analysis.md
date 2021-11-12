---
title: 이미지 분석이란?
titleSuffix: Azure Cognitive Services
description: 이미지 분석 서비스는 미리 학습된 AI 모델을 사용하여 이미지에서 다양한 시각적 기능을 추출합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
keywords: 컴퓨터 비전, 컴퓨터 비전 애플리케이션, 컴퓨터 비전 서비스
ms.openlocfilehash: 46e01af8fb0022e2e003565a07dca6e7e2713dda
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372571"
---
# <a name="what-is-image-analysis"></a>이미지 분석이란?

Computer Vision 이미지 분석 서비스는 이미지에서 다양한 시각적 기능을 추출할 수 있습니다. 예를 들어 이미지에 성인 콘텐츠가 포함되어 있는지, 특정 브랜드 또는 개체를 찾거나, 사람의 얼굴을 찾는지 여부를 확인할 수 있습니다.

클라이언트 라이브러리 SDK를 통해 또는 [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)를 직접 호출하여 이미지 분석을 사용할 수 있습니다. [빠른 시작](quickstarts-sdk/image-analysis-client-library.md)을 따라 시작하세요.

> [!div class="nextstepaction"]
> [시작](quickstarts-sdk/image-analysis-client-library.md)

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./quickstarts-sdk/image-analysis-client-library.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./Vision-API-How-to-Topics/HowToCallVisionAPI.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [개념 문서](concept-tagging-images.md)에서는 서비스의 기능 및 기능에 대한 자세한 설명을 제공합니다.
* [자습서](./tutorials/storage-lab-tutorial.md)는 보다 광범위한 비즈니스 솔루션에서 이 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

## <a name="image-analysis-features"></a>이미지 분석 기능

이미지를 분석하여 이미지의 시각적 기능 및 특성에 대한 인사이트를 제공할 수 있습니다. 아래 목록의 모든 기능이 [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API로 제공됩니다. [빠른 시작](./quickstarts-sdk/image-analysis-client-library.md)을 따라 시작하세요.


### <a name="tag-visual-features"></a>시각적 기능 태그 지정

수천 개의 인식 가능한 사물, 생물, 풍경 및 동작 세트를 기반으로 하여 이미지의 시각적 기능을 식별하고 태그를 지정합니다. 태그가 모호하거나 누구나 알 수 있는 것이 아닌 경우 API 응답은 태그의 컨텍스트를 명확히 설명하는 ‘힌트’를 제공합니다. 태그 지정은 전면에 있는 사람과 같은 주요 주체로 국한되지 않고, 환경(실내 또는 실외), 가구, 도구, 식물, 동물, 액세서리, 가젯 등을 포함합니다. [시각적 기능 태그 지정](concept-tagging-images.md)

:::image type="content" source="Images/tagging.png" alt-text="오른쪽에 태그 수신 대기가 있는 스케이트보더 이미지":::


### <a name="detect-objects"></a>개체 감지

개체 검색은 태그 지정과 유사하지만, API는 적용된 각 태그의 경계 상자 좌표를 반환합니다. 예를 들어 이미지에 개, 고양이 및 사람이 포함된 경우 검색 작업을 실행하면 해당 개체를 해당 좌표와 함께 이미지에 나열합니다. 이 기능을 사용하여 이미지의 개체 간 관계를 추가로 처리할 수 있습니다. 또한 이 기능을 통해 이미지에 동일한 태그의 여러 인스턴스가 있는 경우 이를 알 수 있습니다. [개체 감지](concept-object-detection.md)

:::image type="content" source="Images/detect-objects.png" alt-text="노트북 주위에 사각형이 그려진 사무실 이미지":::


### <a name="detect-brands"></a>브랜드 감지

수천 개의 글로벌 로고 데이터베이스에서 이미지 또는 비디오에 있는 상업용 브랜드를 식별합니다. 예를 들어 이 기능을 사용하여 소셜 미디어에서 가장 인기 있거나 미디어 제품 배치에서 가장 일반적인 브랜드를 검색할 수 있습니다. [브랜드 감지](concept-brand-detection.md)

### <a name="categorize-an-image"></a>이미지 분류

부모/자식 유전적 계층 구조가 있는 [범주 분류](Category-Taxonomy.md)를 사용하여 전체 이미지를 식별하고 분류합니다. 범주는 단독으로 사용하거나 새 태그 지정 모델을 통해 사용할 수 있습니다.<br/>현재 영어는 이미지에 대한 태그 지정 및 분류에 지원되는 유일한 언어입니다. [이미지 분류](concept-categorizing-images.md)

### <a name="describe-an-image"></a>이미지 설명

완전한 문장을 사용하여 사람이 읽을 수 있는 언어로 전체 이미지에 대한 설명을 생성합니다. Computer Vision의 알고리즘은 이미지에서 식별된 개체에 따라 다양한 설명을 생성합니다. 설명이 각각 평가되고 신뢰도 점수가 생성됩니다. 그런 다음, 가장 높은 신뢰도 점수부터 가장 낮은 점수 순으로 정렬된 목록이 반환됩니다. [이미지 설명](concept-describing-images.md)

:::image type="content" source="Images/description.png" alt-text="오른쪽에 간단한 설명이 있는 암소 이미지":::

### <a name="detect-faces"></a>얼굴 감지

이미지에서 얼굴을 감지하고, 감지된 얼굴 각각에 대한 정보를 제공합니다. Computer Vision은 감지된 얼굴 각각에 대한 좌표, 사각형, 성별 및 나이를 반환합니다.<br/>Computer Vision은 [Face](../face/index.yml) 서비스 기능의 하위 집합을 제공합니다. 얼굴 인식 및 포즈 감지와 같이 더 자세한 분석을 위한 Face 서비스를 사용할 수 있습니다. [얼굴 감지](concept-detecting-faces.md)

### <a name="detect-image-types"></a>이미지 유형 감지

이미지가 선 그리기인지, 아니면 이미지가 클립 아트인지 여부와 같은 이미지에 대한 특성을 감지합니다. [이미지 유형 감지](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>도메인 특정 콘텐츠 검색

도메인 모델을 사용하여 유명인 및 랜드마크와 같은 이미지의 도메인 관련 콘텐츠를 감지하고 식별합니다. 예를 들어 이미지에 사람이 포함되어있는 경우 Computer Vision은 유명인에 대한 도메인 모델을 사용하여 이미지에서 감지된 사람이 알려진 유명인과 일치하는지 확인합니다. [도메인 특정 콘텐츠 감지](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>색 구성표 감지

이미지 내의 색 사용을 분석합니다. Computer Vision은 이미지가 흑백인지, 아니면 컬러인지 여부를 결정할 수 있고, 컬러 이미지의 경우 기준 컬러와 테마 컬러를 식별할 수 있습니다. [색 구성표 감지](concept-detecting-color-schemes.md)



### <a name="generate-a-thumbnail"></a>썸네일 생성

이미지의 콘텐츠를 분석하여 해당 이미지에 대한 적절한 썸네일을 생성합니다. Computer Vision은 먼저 고품질 썸네일을 생성한 다음, 이미지 내의 개체를 분석하여 ‘관심 영역’을 결정합니다. 그런 다음, Computer Vision은 관심 영역의 요구 사항에 맞게 이미지를 자릅니다. 생성된 썸네일은 필요에 따라 원래 이미지의 가로 세로 비율과 다른 가로 세로 비율을 사용하여 표시할 수 있습니다. [썸네일 생성](concept-generating-thumbnails.md)

:::image type="content" source="Images/thumbnail-demo.png" alt-text="오른쪽에 잘린 버전이 있는 등산객 이미지":::


### <a name="get-the-area-of-interest"></a>관심 영역 가져오기

이미지의 콘텐츠를 분석하여 ‘관심 영역’의 좌표를 반환합니다. 이미지를 자르고 썸네일을 생성하는 대신 Computer Vision은 영역의 경계 상자 좌표를 반환하므로 호출 애플리케이션이 원하는 대로 원본 이미지를 수정할 수 있습니다. [관심 영역 가져오기](concept-generating-thumbnails.md#area-of-interest)

### <a name="moderate-content-in-images"></a>이미지 콘텐츠 조정

Computer Vision을 사용하여 이미지에서 [성인 콘텐츠를 감지](concept-detecting-adult-content.md)하고 다양한 분류에 대한 신뢰도 점수를 반환할 수 있습니다. 콘텐츠 플래그 지정 임계값은 기본 설정에 맞게 슬라이딩 배율로 설정할 수 있습니다.

## <a name="image-requirements"></a>이미지 요구 사항

이미지 분석은 다음 요구 사항을 충족하는 이미지에서 작동합니다.

- 이미지가 JPEG, PNG, GIF 또는 BMP 형식으로 제공되어야 합니다.
- 이미지의 파일 크기가 4MB보다 작아야 합니다.
- 이미지의 크기가 50 x 50 픽셀보다 커야 합니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Computer Vision 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

선호하는 개발 언어로 된 빠른 시작 가이드에 따라 이미지 분석을 시작하세요.

- [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/image-analysis-client-library.md)
