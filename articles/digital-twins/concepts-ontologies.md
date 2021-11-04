---
title: 온톨로지란?
titleSuffix: Azure Digital Twins
description: 디지털 트윈 토폴로지, Azure Digital Twins 어떻게 사용되는지, 이러한 DTDL 토폴로지를 특정 산업의 컨텍스트에서 모델링에 사용할 수 있는 방법에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 10/21/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 56ce0815bdcb59e64f6a61cce4d4fb6172abea20
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501631"
---
# <a name="what-is-an-ontology"></a>온톨로지란? 

이 문서에서는 업계 토폴로지의 개념과 Azure Digital Twins 컨텍스트 내에서 해당 토폴로지를 사용하는 방법을 설명합니다.

Azure Digital Twins 솔루션의 어휘는 사용자 환경에 존재하는 엔터티 유형을 설명하는 [모델](concepts-models.md)을 사용하여 정의됩니다.

경우에 따라 솔루션이 특정 업계에 연결된 경우 사용자 고유의 모델 집합을 처음부터 작성하는 것보다 이미 존재하는 해당 업계에 대한 모델 집합으로 시작하는 것이 더 쉽고 효과적일 수 있습니다. 이러한 기존 모델 집합을 **온톨로지** 라고 합니다.

일반적으로 온톨로지는 건물 구조, IoT 시스템, 스마트 시티, 에너지 그리드, 웹 콘텐츠 등과 같은 지정된 도메인에 대한 모델 세트입니다. 온톨로지는 다음을 지원할 수 있으므로 트윈 그래프의 스키마로 사용되기도 합니다.
* 소프트웨어 구성 요소, 설명서, 쿼리 라이브러리 등의 조화.
* 개념적 모델링 및 시스템 개발에 대한 투자 감소
* 의미 체계 수준에서 보다 쉬운 데이터 상호 운용성
* 처음부터 시작하거나 "휠 다시 개발" 대신 최선의 재사용 방법

이 문서에서는 Azure Digital Twins 모델에 대해 온톨로지를 사용하는 이유와 그 방법을 설명합니다. 또한 현재 사용 가능한 온톨로지와 도구에 대해서도 설명합니다.

## <a name="using-ontologies-for-azure-digital-twins"></a>Azure Digital Twins에 온톨로지 사용

온톨로지는 디지털 트윈 솔루션을 위한 좋은 시작점을 제공합니다. 여기에는 디지털 트윈 그래프를 디자인, 생성 및 구문 분석하기 위한 일련의 도메인별 모델 및 엔터티 간 관계가 포함됩니다. 솔루션 개발자는 온톨로지를 통해 입증된 시작점에서 디지털 트윈 솔루션을 시작하고 비즈니스 문제 해결에 집중할 수 있습니다. Microsoft에서 제공하는 온톨로지는 쉽게 확장할 수 있도록 설계되었으므로 솔루션에 맞게 사용자 지정할 수 있습니다. 

또한 온톨로지는 솔루션 전체에서 공통 어휘를 제공할 수 있으므로 솔루션에서 이러한 온톨로지를 사용하면 서로 다른 파트너와 공급업체 간의 보다 원활한 통합을 위해 설정할 수 있습니다.

Azure Digital Twins의 모델은 [DTDL(디지털 트윈 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)로 표현되므로 Azure Digital twins에 사용되는 온톨로지도 DTDL로 작성됩니다. 

## <a name="strategies-for-integrating-ontologies"></a>온톨로지 통합 전략

업계 표준 온톨로지를 DTDL과 통합하는 세 가지 전략을 사용할 수 있습니다. 요구 사항에 따라 가장 적합한 전략을 선택할 수 있습니다.

| 전략 | Description | 리소스 |
| --- | --- | --- |
| **채택** | 광범위하게 채택된 업계 표준을 기반으로 빌드된 오픈 소스 DTDL 온톨로지로 솔루션을 시작할 수 있습니다. 이러한 모델 집합을 기본으로 사용하거나 사용자 지정된 솔루션을 위해 자체적으로 추가하여 확장할 수 있습니다. | [업계&nbsp;표준 온톨로지&nbsp; 채택](concepts-ontologies-adopt.md)<br><br>[온톨로지&nbsp;확장](concepts-ontologies-extend.md) |
| **변환** | 이미 다른 표준 형식으로 표현된 기존 모델이 있는 경우 DTDL로 변환하여 Azure Digital Twins에서 사용할 수 있습니다. | [온톨로지&nbsp;변환](concepts-ontologies-convert.md)<br><br>[온톨로지&nbsp;확장](concepts-ontologies-extend.md) |
| **작성자** | 적용 가능한 업계 표준을 영감으로 사용하여 언제든지 사용자 지정 DTDL 모델을 처음부터 개발할 수 있습니다. | [DTDL 모델](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>모델 개발 경로에서 온톨로지 전략 사용

온톨로지를 Azure Digital Twins에 통합하기 위해 어떤 전략을 선택하든 관계없이, 아래의 전체 경로를 따라 온톨로지를 DTDL 모델로 만들고 업로드하는 과정을 안내할 수 있습니다.

1. [Azure Digital Twins에서 DTDL 모델링](concepts-models.md)을 검토하고 이해하는 것부터 시작합니다.
1. 위에서 선택한 온톨로지 통합 전략을 계속 진행합니다. 온톨로지를 기반으로 모델을 [채택](concepts-ontologies-adopt.md), [변환](concepts-ontologies-convert.md) 또는 [제작](concepts-models.md)합니다.
    1. 필요한 경우 온톨로지를 [확장](concepts-ontologies-extend.md)하여 필요에 맞게 사용자 지정합니다.
1. 모델의 [유효성을 검사](how-to-parse-models.md)하여 DTDL 문서가 작동하는지 확인합니다.
1. [API](how-to-manage-model.md#upload-models) 또는 [Azure Digital Twins 모델 업로더](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels)와 같은 샘플을 사용하여 완성된 모델을 Azure Digital Twins에 업로드합니다.

이 문서 시리즈를 읽으면 Azure Digital Twins 인스턴스에서 모델을 사용하는 방법을 안내합니다. 

>[!TIP]
> [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 또는 [Azure Digital Twins 모델 시각화 도우미](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)를 사용하여 온톨로지의 모델을 시각화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

온톨로지 채택, 변환 및 작성 전략에 대해 자세히 알아보세요.
* [산업 표준 온톨로지 채택](concepts-ontologies-adopt.md)
* [온톨로지 변환](concepts-ontologies-convert.md)
* [DTDL 모델 관리](how-to-manage-model.md)

또는 모델을 사용하여 디지털 트윈을 만드는 방법에 대해 알아보세요. [디지털 트윈 및 트윈 그래프](concepts-twins-graph.md).