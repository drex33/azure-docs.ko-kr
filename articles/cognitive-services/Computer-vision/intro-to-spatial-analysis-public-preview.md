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
ms.openlocfilehash: 9553c7e177bdc78b071d6ed68725879e46c9ef5e
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668450"
---
# <a name="what-is-spatial-analysis"></a>공간 분석이란?

공간 분석은 지정된 영역 내에서 사람들의 움직임과 상태를 파악하여 조직이 실제 공간의 가치를 극대화할 수 있도록 도와주는 AI 서비스입니다. 이를 통해 CCTV 또는 감시 카메라에서 비디오를 수집하고, 비디오 스트림에서 인사이트를 추출하고, 다른 시스템에서 사용할 이벤트를 생성할 수 있습니다. 카메라 스트림의 입력을 통해 이 서비스는 공간에 들어가는 사람의 수를 세거나 얼굴에 마스크 착용 및 사회적 거리두기 지침을 사용하여 규정 준수를 측정하는 것과 같은 작업을 수행할 수 있습니다.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>수행하는 작업

공간 분석의 핵심 작업은 비디오를 수집하고, 비디오에서 사람을 감지하며, 시간이 지남에 따라 움직이는 사람을 추적하고, 사람이 관심 영역과 상호 작용할 때 이벤트를 생성하는 시스템을 기반으로 합니다.

## <a name="spatial-analysis-features"></a>공간 분석 기능

| 기능 | 정의 |
|------|------------|
| **인물 감지** | 이 구성 요소는 "이 이미지에서 사람들은 어디에 있나요?"라는 질문에 답변합니다. 이미지에서 사람을 찾고, 각 사람의 위치를 나타내는 경계 상자 좌표를 **사람 추적** 구성 요소에 전달합니다. |
| **인물 추적** | 이 구성 요소는 사람들이 카메라 앞에서 이동할 때 시간이 지남에 따라 인물 감지를 연결합니다. 사람들이 일반적으로 움직이는 방법에 대한 임시 논리 및 사람의 전반적인 모양에 대한 기본 정보를 사용합니다. 여러 카메라를 통해 사람을 추적하지 않습니다. 사용자가 약 1분 이상 시야를 벗어났다가 시야에 다시 들어오면 시스템에서 이를 새로운 사람으로 인식합니다. 인물 추적은 카메라에서 개인을 고유하게 식별하지 않습니다. 얼굴 인식 또는 걸음걸이 추적을 사용하지 않습니다. |
| **얼굴 마스크 감지** | 이 구성 요소는 카메라의 시야에서 사람의 얼굴 위치를 감지하고, 얼굴의 마스크 착용 유무를 식별합니다. AI 작업은 비디오에서 이미지를 스캔합니다. 얼굴이 감지되면 서비스에서 경계 상자를 얼굴 주위에 제공합니다. 개체 감지 기능을 사용하여 얼굴 마스크가 경계 상자 내에 있는지 식별합니다. 얼굴 마스크 감지에는 한 얼굴을 다른 얼굴과 구분하거나 얼굴 특성을 예측 또는 분류하거나 얼굴 인식을 수행하는 작업이 포함되지 않습니다. |
| **관심 영역** | 이 구성 요소는 입력 비디오 프레임의 사용자 정의 영역 또는 선입니다. 사람이 비디오에서 이 영역과 상호 작용하면 시스템에서 이벤트를 생성합니다. 예를 들어 **PersonCrossingLine** 작업의 경우 비디오 프레임에서 선이 정의됩니다. 사람이 해당 선을 교차할 때 이벤트가 생성됩니다. |
| **이벤트** | 이벤트는 공간 분석의 기본 출력입니다. 각 작업은 주기적으로(예: 분당 한 번) 또는 특정 트리거가 발생할 때마다 특정 이벤트를 발생시킵니다. 이벤트에는 입력 비디오에서 발생한 작업에 대한 정보가 포함되지만, 이미지 또는 비디오는 포함되지 않습니다. 예를 들어 **PeopleCount** 작업은 사람 수가 변경될 때마다(트리거) 또는 1분마다 한 번씩(주기적으로) 업데이트된 수가 포함된 이벤트를 발생시킬 수 있습니다. |

## <a name="get-started"></a>시작

[빠른 시작](spatial-analysis-container.md)에 따라 공간 분석 컨테이너를 설정하고 비디오 분석을 시작합니다.

## <a name="responsible-use-of-spatial-analysis-technology"></a>공간 분석 기술의 책임 있는 사용

공간 분석 기술을 책임 있게 사용하는 방법에 대해 자세히 알아보려면 [투명성 메모](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)를 참조하세요. Microsoft의 투명성 메모는 Microsoft의 AI 기술의 작동 방식, 시스템 소유자가 시스템 성능과 동작에 영향을 줄 수 있는 선택 사항, 기술, 사람 및 환경을 포함한 전체 시스템에 대한 사고의 중요성을 이해하는 데 도움을 주기 위한 것입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 공간 분석 컨테이너](spatial-analysis-container.md)
