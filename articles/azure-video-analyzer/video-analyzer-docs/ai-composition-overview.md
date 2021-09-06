---
title: Azure Video Analyzer의 AI 컴퍼지션
description: 이 문서에서는 세 가지 종류의 AI 컴퍼지션에 대한 Azure Video Analyzer 지원에 대한 개략적인 개요를 제공합니다. 또한 이 항목에서는 각 종류의 AI 컴퍼지션에 대한 시나리오 설명도 제공합니다.
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 709bfe642c6427e131c95d64af6de0ee410cf261
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536035"
---
# <a name="ai-composition"></a>AI 컴퍼지션

이 문서에서는 세 가지 종류의 AI 컴퍼지션에 대한 Azure Video Analyzer 지원에 대한 개략적인 개요를 제공합니다. 

* [순차](#sequential-ai-composition)
* [Parallel](#parallel-ai-composition)
* [결합](#combined-ai-composition)

## <a name="sequential-ai-composition"></a>순차적 AI 컴퍼지션

AI 노드를 순차적으로 구성할 수 있습니다. 이를 통해 다운스트림 노드는 업스트림 노드에서 생성된 유추를 보강할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/sequential.svg" alt-text="순차적 AI 컴퍼지션":::
 
### <a name="key-aspects"></a>주요 측면

* 파이프라인 확장은 미디어 통과 노드 역할을 하며 외부 AI 서버가 다양한 속도, 형식 및 해상도로 프레임을 받도록 구성할 수 있습니다. 또한 외부 AI 서버가 모든 프레임 또는 이미 유추가 포함된 프레임만 받을 수 있는 구성을 지정할 수 있습니다.
* 유추는 다른 확장 노드를 통과할 때 프레임에 추가되며, 이러한 노드를 순서대로 무제한으로 추가할 수 있습니다.
* 연속 비디오 녹화 또는 이벤트 기반 비디오 녹화와 같은 다른 시나리오는 순차적 AI 컴퍼지션과 결합할 수 있습니다.

    
## <a name="parallel-ai-composition"></a>병렬 AI 컴퍼지션

AI 노드는 순서가 아닌 병렬로 구성될 수도 있습니다. 이렇게 하면 독립적인 유추를 수집된 비디오 스트림에서 수행하여 에지에 수집 대역폭을 절약할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/parallel.svg" alt-text="병렬 AI 컴퍼지션":::
 
### <a name="key-aspects"></a>주요 측면

* 비디오는 임의의 수의 병렬 분기로 분할될 수 있으며 이러한 분할은 다음 노드 이후 언제든지 발생할 수 있습니다.
    
    * RTSP 원본
    * 동작 감지기
    * 파이프라인 확장

## <a name="combined-ai-composition"></a>결합된 AI 컴퍼지션

순차적 및 병렬 컴퍼지션 구문을 결합하여 복잡한 구성 가능한 AI 파이프라인을 개발할 수 있습니다. AVA 파이프라인을 사용하면 확장 노드를 다른 지원되는 노드와 함께 무한정 병렬 컴퍼지션과 함께 순차적으로 및/또는 결합할 수 있기 때문에 가능합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/complex.svg" alt-text="결합된 AI 컴퍼지션":::
 


## <a name="next-steps"></a>다음 단계

[AI 컴퍼지션을 사용하여 여러 AI 모델로 라이브 비디오 스트림 분석](analyze-ai-composition.md)
