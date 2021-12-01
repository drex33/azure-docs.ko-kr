---
title: 색 구성표 검색 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 색 구성표를 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3867e847aa3beeb37b5eec223324ad55ec30016e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361006"
---
# <a name="detect-color-schemes-in-images"></a>이미지에서 색 구성표 검색

Computer Vision는 이미지의 색을 분석 하 여 기준 전경색, 주요 배경색 및 이미지의 더 큰 기준 색 집합 이라는 세 가지 특성을 제공 합니다. 반환 가능한 색 집합은 black, blue, 밤색, 회색, 녹색, 주황, 분홍, 자주색, 빨강, 청록, 흰색 및 노랑입니다.

또한 기준 색 집합 및 채도의 조합을 기반으로 하 여 이미지에서 가장 선명한 색을 나타내는 강조 색을 추출 Computer Vision 합니다. 악센트 색은 16 진수 HTML 색 코드 (예:)로 반환 됩니다 `#00CC00` .

또한 Computer Vision는 이미지가 흑백 이미지 인지 여부를 나타내는 부울 값을 반환 합니다.

## <a name="color-scheme-detection-examples"></a>색 구성표 검색 예제

다음 예제에서는 이미지의 색 구성표를 검색할 때 Computer Vision에서 반환 하는 JSON 응답을 보여 줍니다. 

> [!NOTE]
> 이 경우 예제 이미지는 흑백 이미지가 아니라 기조 전경색 및 배경색이 검은색이고 전체 이미지의 주조색이 검은색과 흰색입니다.

![사람의 실루엣이 있는 해질녘의 야외 산](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>주조색 예제

다음 표에서는 각 샘플 이미지에 대해 반환되는 전경색, 배경색 및 이미지 색을 보여 줍니다.

| 이미지 | 주조색 |
|-------|-----------------|
|![녹색 배경의 흰색 꽃](./Images/flower.png)| 전경색: 검은색<br/>배경색: 흰색<br/>색: 검은색, 흰색, 녹색|
![역을 달리는 열차](./Images/train_station.png) | 전경색: 검은색<br/>배경색: 검은색<br/>색: 검은색 |

### <a name="accent-color-examples"></a>강조색 예제

 다음 표에서는 각 예제 이미지에 대해 반환되는 강조 색을 16진수 HTML 색 값으로 보여 줍니다.

| 이미지 | 강조 색 |
|-------|--------------|
|![석양이 보이는 산 바위에 서 있는 사람](./Images/mountain_vista.png) | #BB6D10 |
|![녹색 배경의 흰색 꽃](./Images/flower.png) | #C6A205 |
|![역을 달리는 열차](./Images/train_station.png) | #474A84 |

### <a name="black-and-white-detection-examples"></a>블랙 및 흰색 검색 예

다음 표에서는 샘플 이미지의 검은색과 흰색에 대한 Computer Vision의 평가를 보여 줍니다.

| 이미지 | 흑백? |
|-------|----------------|
|![맨해튼 건물의 흑백 사진](./Images/bw_buildings.png) | true |
|![파란색 집 및 앞 마당](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>API 사용

색 구성표 검색 기능은 [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API의 일부 입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. **visualFeatures** 쿼리 매개 변수에 `Color`을 포함합니다. 그런 다음 전체 JSON 응답을 받으면 `"color"` 섹션의 내용에 대한 문자열을 구문 분석하기만 하면 됩니다.

* [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/image-analysis-client-library.md?pivots=programming-language-csharp)
