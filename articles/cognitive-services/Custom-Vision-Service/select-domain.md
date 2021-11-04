---
title: Custom Vision 프로젝트에 대한 도메인 선택 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Custom Vision Service에서 프로젝트에 대한 도메인을 선택하는 방법을 보여 줍니다.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 5838185328920587b11bb66d21c9402ce93e9b6b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458250"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Custom Vision 프로젝트에 대한 도메인 선택

Custom Vision 프로젝트의 설정 탭에서 프로젝트에 대한 도메인을 선택할 수 있습니다. 시나리오에 가장 가까운 도메인을 선택합니다. 클라이언트 라이브러리나 REST API를 통해 Custom Vision에 액세스하는 경우 프로젝트를 만들 때 도메인 ID를 지정해야 합니다. [도메인 가져오기](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)로 도메인 ID 목록을 가져오거나 아래 표를 이용합니다.

## <a name="image-classification"></a>이미지 분류

|도메인|목적|
|---|---|
|__일반__| 광범위한 이미지 분류 작업에 최적화되었습니다. 다른 특정 도메인이 적절하지 않거나 어떤 도메인을 선택할지 확실하지 않은 경우 일반 도메인 중 하나를 선택합니다. ID: `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__일반 [A1]__| 일반 도메인과 동등한 유추 시간으로 더 나은 정확도를 내도록 최적화되었습니다. 더 큰 데이터 세트 또는 더 까다로운 사용자 시나리오에 권장됩니다. 이 도메인에는 학습 시간이 더 필요합니다. ID: `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__일반 [A2]__| 일반[A1] 및 일반 도메인보다 더 빠른 유추 시간으로 더 나은 정확도를 내도록 최적화되었습니다. 대부분의 데이터 세트에 권장됩니다. 이 도메인에는 일반 및 일반 [A1] 도메인보다 학습 시간이 적게 필요합니다. ID: `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__음식__|식당 메뉴에 표시되는 것 같은 음식 사진에 최적화되었습니다. 개별 과일이나 채소 사진을 분류하려면 음식 도메인을 사용합니다. ID: `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__랜드마크__|인식 가능한 자연적 및 인공적인 랜드마크에 최적화되었습니다. 이 도메인은 랜드마크가 사진에서 명확하게 표시될 때 가장 효과적입니다. 앞에 서 있는 사람이 랜드마크를 약간 가리고 있는 경우에도 도메인이 작동합니다. ID: `ca455789-012d-4b50-9fec-5bb63841c793`|
|__정품__|쇼핑 카탈로그 또는 쇼핑 웹 사이트에 있는 이미지에 최적화되었습니다. 드레스, 바지, 셔츠를 높은 정밀도로 분류하려는 경우 이 도메인을 사용합니다. ID: `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__압축 도메인__| 에지 디바이스의 실시간 분류 제약 조건에 최적화되었습니다.|


> [!NOTE]
> 일반[A1] 및 일반[A2] 도메인은 광범위한 시나리오 세트에 사용할 수 있고 정확도에 최적화되어 있습니다. 더 나은 유추 속도 및 학습 시간 단축을 위해서는 일반[A2] 모델을 사용합니다. 더 큰 데이터 세트의 경우 학습 및 추론 시간이 더 많이 필요하지만 일반[A2]보다 더 나은 정확도를 렌더링하는 일반[A1]을 사용하려고 할 수 있습니다. 일반 모델은 일반[A1] 및 일반[A2] 둘 다보다 유추 시간이 더 많이 필요합니다.

## <a name="object-detection"></a>개체 감지

|도메인|목적|
|---|---|
|__일반__| 광범위한 개체 감지 작업에 맞게 최적화됩니다. 적절한 다른 도메인이 없거나 어떤 도메인을 선택할지 확실하지 않은 경우 일반 도메인을 선택합니다. ID: `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__일반 [A1]__| 일반 도메인과 동등한 유추 시간으로 더 나은 정확도를 내도록 최적화되었습니다. 더 정확한 지역 위치 요구, 더 큰 데이터 세트 또는 더 어려운 사용자 시나리오에 권장됩니다. 이 도메인에는 학습 시간이 더 필요하고 결과는 결정적이지 않습니다. 제공된 동일한 학습 데이터로 +-1%의 mAP(평균 정밀도 평균) 오차가 예상됩니다. ID: `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__로고__|이미지에서 브랜드 로고를 찾도록 최적화됩니다. ID: `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__선반 위의 제품__|선반에서 제품을 검색하고 분류하는 데 최적화되어 있습니다. ID: `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__압축 도메인__| 에지 디바이스의 실시간 개체 감지 제약 조건에 맞게 최적화되었습니다.|

## <a name="compact-domains"></a>압축 도메인

압축 도메인에서 생성된 모델을 로컬에서 실행하기 위해 내보낼 수 있습니다. Custom Vision 3.4 공개 미리 보기 API에서 GetDomains API를 호출하여 압축 도메인에 대해 내보낼 수 있는 플랫폼의 목록을 가져올 수 있습니다.

다음 도메인은 모두 ONNX, TensorFlow, TensorFlowLite, TensorFlow.js, CoreML 및 VAIDK 형식의 내보내기를 지원 합니다. 단, **compact (개체 검색 일반)** 도메인은 VAIDK을 지원 하지 않습니다.

모델 성능은 선택한 도메인에 따라 달라집니다. 아래 표는 Intel Desktop CPU 및 NVidia GPU \[1\]에서의 모델 크기 및 유추 시간을 보고합니다. 이 숫자에 전처리 및 후처리 시간은 포함되지 않습니다.

|Task|도메인|ID|모델 크기|CPU 유추 시간|GPU 유추 시간|
|---|---|---|---|---|---|
|분류|일반(압축)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10ms|5ms|
|분류|일반(압축) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43MB|50ms|5ms|
|개체 감지|일반(압축)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45MB|35ms|5ms|
|개체 감지|일반(압축) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14MB|27ms|7ms|

>[!NOTE]
>개체 감지용 __일반(압축)__ 도메인에는 특별한 후처리 논리가 필요합니다. 자세한 내용은 내보낸 zip 패키지의 예제 스크립트를 참조하세요. 후처리 논리가 없는 모델이 필요한 경우 __일반(압축) [S1]__ 을 사용합니다.

>[!IMPORTANT]
>내보낸 모델이 클라우드의 예측 API와 정확히 동일한 결과를 제공한다고 보장할 수 없습니다. 실행 중인 플랫폼 또는 전처리 구현의 약간의 차이가 모델 출력에 더 큰 차이를 유발할 수 있습니다. 전처리 논리에 대한 자세한 내용은 [이 문서](quickstarts/image-classification.md)를 참조하세요.

\[1\] Intel Xeon E5-2690 CPU 및 NVIDIA Tesla M60
