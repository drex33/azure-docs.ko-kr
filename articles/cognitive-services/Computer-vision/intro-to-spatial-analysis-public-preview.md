---
title: 공간 분석이란?
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Spatial Analysis 컨테이너의 기본 개념과 기능에 대해 설명합니다.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/06/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: fd160aa2ba4a626e12db638694cb6d971b19fbd2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469942"
---
# <a name="what-is-spatial-analysis"></a>공간 분석이란?

Computer Vision 공간 분석을 사용하여 카메라에서 스트리밍 비디오를 수집하고, 통찰력을 추출하고, 다른 시스템에서 사용할 이벤트를 생성할 수 있습니다. 이 서비스는 비디오에서 사람의 존재와 움직임을 감지합니다. 공간에 들어가는 사람의 수를 세거나 얼굴에 마스크 착용 및 사회적 거리두기 지침을 사용하여 규정 준수를 측정하는 것과 같은 작업을 수행할 수 있습니다. 물리적 공간의 비디오 스트림을 처리하여 사람들이 비디오 스트림을 사용하는 방법을 배우고 조직에 대한 공간의 가치를 극대화할 수 있습니다. 

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>수행하는 작업
공간 분석은 비디오를 수집한 다음, 비디오에서 사람을 감지합니다. 사람이 감지되면 시스템은 시간이 지남에 따라 이동하는 사람을 추적한 다음, 사람들이 관심 영역과 상호 작용할 때 이벤트를 생성합니다. 모든 작업은 단일 카메라의 보기 필드에서 인사이트를 제공합니다. 

### <a name="people-counting"></a>인원 수 계산
이 작업은 PersonCount 작업을 사용하여 시간에 따라 특정 영역에 있는 인원 수를 계산합니다. 프레임에 걸쳐 사람을 추적하지 않고 처리된 각 프레임에 대해 독립적인 카운트를 생성합니다. 이 작업은 공간에 있는 인원 수를 추정하거나 사람이 나타날 때 경고를 생성하는 데 사용할 수 있습니다.

![공간 분석은 카메라 시야에 있는 인원 수를 계산합니다.](https://user-images.githubusercontent.com/11428131/139924111-58637f2e-f2f6-42d8-8812-ab42fece92b4.gif)

### <a name="entrance-counting"></a>입장 인원 계산
이 기능은 사람들이 해당 지역에 머무르는 시간이나 출입구를 통해 들어오는 시간을 모니터링합니다. 이 모니터링은 PersonCrossingPolygon 또는 PersonCrossingLine 작업을 사용하여 수행할 수 있습니다. 소매점 시나리오에서는 이러한 작업을 통해 계산대 대기 시간이나 디스플레이 이용 인원을 측정할 수 있습니다. 또한 로비 또는 다른 상업 건물 시나리오에서 이러한 작업은 특정 층에서 유동 인구를 측정할 수 있습니다.

![공간 분석은 계산대 대기 시간을 측정합니다.](https://user-images.githubusercontent.com/11428131/137016574-0d180d9b-fb9a-42a9-94b7-fbc0dbc18560.gif)

### <a name="social-distancing-and-facemask-detection"></a>사회적 거리두기 및 마스크 착용 감지 
이 기능은 사람들이 공간에서 사회적 거리두기 요구 사항을 얼마나 잘 따르는지 분석합니다. PersonDistance 작업을 사용하여 시스템은 사람들이 공간 여기저기로 돌아 다닐 때 자동으로 자체 보정합니다. 그런 다음, 사람들이 특정 거리 임계값(6피트 또는 10피트)을 위반할 때를 식별합니다.

![공간 분석은 사람 간의 거리를 표시하는 선을 표시하는 사회적 거리두기 위반 이벤트를 시각화합니다.](https://user-images.githubusercontent.com/11428131/139924062-b5e10c0f-3cf8-4ff1-bb58-478571c022d7.gif)

공간 분석은 또한 사람이 마스크와 같은 얼굴 보호 커버를 착용하고 있는지 감지하도록 구성할 수 있습니다. `ENABLE_FACE_MASK_CLASSIFIER` 매개 변수를 구성하여 PersonCount, PersonCrossingLine 및 PersonCrossingPolygon 작업에 대해 마스크 분류자를 사용할 수 있습니다.

![공간 분석은 엘리베이터에서 사람들이 마스크를 착용하고 있는지 여부를 분류합니다.](https://user-images.githubusercontent.com/11428131/137015842-ce524f52-3ac4-4e42-9067-25d19b395803.png)

## <a name="get-started"></a>시작

[빠른 시작](spatial-analysis-container.md)에 따라 공간 분석 컨테이너를 설정하고 비디오 분석을 시작합니다.

## <a name="responsible-use-of-spatial-analysis-technology"></a>공간 분석 기술의 책임 있는 사용

공간 분석 기술을 책임 있게 사용하는 방법에 대해 자세히 알아보려면 [투명성 메모](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)를 참조하세요. Microsoft의 투명성 메모는 AI 기술의 작동 방식과 시스템 소유자가 시스템 성능 및 동작에 영향을 줄 수 있는 선택 항목을 이해하는 데 도움이 됩니다. 기술, 사람 및 환경을 포함하여 전체 시스템에 대한 사고의 중요성에 중점을 둡니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 공간 분석 컨테이너](spatial-analysis-container.md)
