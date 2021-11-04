---
title: 얼굴 인식 개념
titleSuffix: Azure Cognitive Services
description: 얼굴 인식, 관련 작업 및 기본 데이터 구조 개념에 대해 알아보세요.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 3ae6a67ce0f906a4b498abaec703ddd6e64485bf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426262"
---
# <a name="face-recognition-concepts"></a>얼굴 인식 개념

이 문서에서는 얼굴 인식, 관련 작업 및 기본 데이터 구조의 개념을 설명합니다. 광범위 한 얼굴 인식은 각자의 얼굴을 확인 하거나 식별 하는 방법을 나타냅니다. 

확인은 두 개의 얼굴을 사용하고 일대일로 일치하는지 확인한 후 같은 얼굴인지 여부를 반환하는 것이며, 식별은 단일 얼굴을 입력으로 사용하여 일대다로 일치하는지 확인한 후 일치하는 후보 집합을 반환하는 것입니다. 얼굴 인식은 id 확인 시나리오를 구현 하는 데 중요 하며, 엔터프라이즈와 앱에서 (원격) 사용자가 해당 사용자를 주장 하는지 확인 하는 데 사용할 수 있습니다.

## <a name="related-data-structures"></a>관련 데이터 구조

인식 작업에서는 주로 다음과 같은 데이터 구조가 사용됩니다. 이러한 개체를 클라우드에 저장하고 해당 ID 문자열로 참조할 수 있습니다. ID 문자열은 항상 구독 내에서 고유 하지만 이름 필드는 중복 될 수 있습니다.

|Name|Description|
|:--|:--|
|DetectedFace| 단일 얼굴 표현은 [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업에서 검색됩니다. 해당 ID는 만든 후 24시간 후에 만료됩니다.|
|PersistedFace| FaceList 또는 Person과 같은 DetectedFace 개체를 그룹에 추가하면 PersistedFace 개체가 됩니다. 언제든지 [검색할](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) 수 있으며 만료되지 않습니다.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 또는 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 이 데이터 구조는 PersistedFace 개체의 다양한 목록입니다. FaceList에는 고유 ID, 이름 문자열, 그리고 선택적으로 사용자 데이터 문자열이 포함됩니다.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 이 데이터 구조는 동일한 사람에 속하는 PersistedFace 개체의 목록입니다. 여기에는 고유 ID, 이름 문자열, 선택적으로 사용자 데이터 문자열이 포함됩니다.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 또는 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 이 데이터 구조는 Person 개체의 다양한 목록입니다. 여기에는 고유 ID, 이름 문자열, 선택적으로 사용자 데이터 문자열이 포함됩니다. PersonGroup을 먼저 [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)시켜야만 인식 작업에 사용할 수 있습니다.|
|PersonDirectory | 이 데이터 구조는 **LargePersonGroup** 와 같지만 추가 저장소 용량 및 기타 추가 된 기능을 제공 합니다. 자세한 내용은 [PersonDirectory 구조체 사용](../Face-API-How-to-Topics/use-persondirectory.md)을 참조 하세요.

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 기본 작업에서 위의 데이터 구조를 사용 하 여 얼굴을 식별 하 고 확인 하는 방법에 대해 자세히 설명 합니다.

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
* 사용 가능한 검색 모델을 사용 하는 경우 [표면 검색](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업에서 qualityForRecognition 특성을 활용할 수 있습니다 .이는 이미지의 품질이 충분 한지에 대 한 일반적인 지침입니다. "높음" 품질 이미지는 사용자 등록에 권장 되며 "medium" 이상의 품질은 식별 시나리오에 권장 됩니다.

## <a name="next-steps"></a>다음 단계

이제 얼굴 인식 개념에 익숙해졌으므로 학습된 PersonGroup에 대해 얼굴을 식별하는 스크립트를 작성합니다.

* [얼굴 클라이언트 라이브러리 빠른 시작](../Quickstarts/client-libraries.md)