---
title: 얼굴 인식 개념
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 얼굴 인식, 관련 작업 및 기본 데이터 구조의 개념을 설명합니다.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 6f22e48c869ebc2cf4101127f3d87cc7836da35d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529221"
---
# <a name="face-recognition-concepts"></a>얼굴 인식 개념

이 문서에서는 얼굴 인식, 관련 작업 및 기본 데이터 구조의 개념을 설명합니다. 대체적으로 얼굴 인식은 얼굴을 사용하여 개인을 확인하거나 식별하는 방법을 나타냅니다. 확인은 두 개의 얼굴을 사용하고 일대일로 일치하는지 확인한 후 같은 얼굴인지 여부를 반환하는 것이며, 식별은 단일 얼굴을 입력으로 사용하여 일대다로 일치하는지 확인한 후 일치하는 후보 집합을 반환하는 것입니다. 얼굴 인식은 ID 확인 시나리오를 구현하는 데 중요하며, 엔터프라이즈 및 앱에서 (원격)사용자가 본인이라고 주장하는 사람이 맞는지 확인하는 데 사용하는 기능입니다.

## <a name="related-data-structures"></a>관련 데이터 구조

인식 작업에서는 주로 다음과 같은 데이터 구조가 사용됩니다. 이러한 개체를 클라우드에 저장하고 해당 ID 문자열로 참조할 수 있습니다. ID 문자열은 한 구독 내에서 항상 고유합니다. 이름 필드는 중복될 수 있습니다.

|Name|Description|
|:--|:--|
|DetectedFace| 단일 얼굴 표현은 [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업에서 검색됩니다. 해당 ID는 만든 후 24시간 후에 만료됩니다.|
|PersistedFace| FaceList 또는 Person과 같은 DetectedFace 개체를 그룹에 추가하면 PersistedFace 개체가 됩니다. 언제든지 [검색할](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) 수 있으며 만료되지 않습니다.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 또는 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 이 데이터 구조는 PersistedFace 개체의 다양한 목록입니다. FaceList에는 고유 ID, 이름 문자열, 그리고 선택적으로 사용자 데이터 문자열이 포함됩니다.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 이 데이터 구조는 동일한 사람에 속하는 PersistedFace 개체의 목록입니다. 여기에는 고유 ID, 이름 문자열, 선택적으로 사용자 데이터 문자열이 포함됩니다.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 또는 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 이 데이터 구조는 Person 개체의 다양한 목록입니다. 여기에는 고유 ID, 이름 문자열, 선택적으로 사용자 데이터 문자열이 포함됩니다. PersonGroup을 먼저 [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)시켜야만 인식 작업에 사용할 수 있습니다.|

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 기본 작업에서 위에 설명된 데이터 구조를 사용하여 얼굴을 식별하고 확인하는 방법에 대해 자세히 설명합니다.

### <a name="persongroup-creation-and-training"></a>PersonGroup 만들기 및 학습

[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 또는 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)을 만들어 일치시킬 사용자 집합을 저장해야 합니다. PersonGroups는 각각이 개인을 나타내는 [Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) 개체를 포함하며, 해당 사람에 속하는 얼굴 데이터 집합을 포함합니다.

[학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 작업은 얼굴 데이터 비교에 사용할 데이터 세트를 준비합니다.

### <a name="identification"></a>Identification(식별)

[식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 작업은 하나 또는 여러 개의 원본 얼굴 ID(DetectedFace 또는 PersistedFace 개체)와 PersonGroup 또는 LargePersonGroup을 사용합니다. 각 원본 얼굴이 속할 수 있는 Person 개체의 목록을 반환합니다. 반환된 Person 개체는 예측 신뢰도 값을 갖는 후보 개체로 래핑됩니다.


### <a name="verification"></a>확인

[확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 작업은 단일 얼굴 ID(DetectedFace 또는 PersistedFace 개체)와 Person 개체를 사용합니다. 얼굴이 동일한 사람에게 속하는지 여부를 확인합니다. 확인은 일대일 일치 확인이며 식별 API 호출의 결과에 대한 최종 검사로 사용할 수 있습니다. 그러나 필요에 따라 후보 Person이 속한 PersonGroup을 전달하여 API 성능을 향상시킬 수 있습니다.


## <a name="input-data"></a>입력 데이터

다음과 같은 팁을 사용하여 입력 이미지가 가장 정확한 인식 결과를 제공하는지 확인합니다.

* 지원되는 입력 이미지 형식은 JPEG, PNG, GIF(첫 번째 프레임), BMP입니다.
* 이미지 파일 크기는 6MB를 넘지 않아야 합니다.
* Person 개체를 만들 때 여러 가지 종류의 각도와 조명을 보여 주는 사진을 사용합니다.
* 다음과 같은 기술적인 문제로 인해 일부 얼굴이 인식되지 않을 수 있습니다.
  * 극단적인 조명(예: 심한 역광)이 있는 이미지
  * 한쪽 또는 양쪽 눈을 가리는 장애물
  * 머리카락 유형 또는 수염의 차이
  * 나이로 인한 얼굴 외모의 변화
  * 극단적인 표정

## <a name="next-steps"></a>다음 단계

이제 얼굴 인식 개념에 익숙해졌으므로 학습된 PersonGroup에 대해 얼굴을 식별하는 스크립트를 작성합니다.

* [얼굴 클라이언트 라이브러리 빠른 시작](../Quickstarts/client-libraries.md)