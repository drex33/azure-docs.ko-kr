---
title: 쿼리 언어
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어의 기본 사항에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 620411b5fc8c657f837f3c27077d4ae3dbf16f1c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509685"
---
# <a name="azure-digital-twins-query-language"></a>Azure Digital Twins 쿼리 언어

이 문서에서는 쿼리 언어 및 해당 기능에 대한 기본 사항을 설명합니다. Azure Digital Twins의 중심은 디지털 트윈과 관계로 생성된 [트윈 그래프](concepts-twins-graph.md)입니다. 이 그래프를 쿼리하여 포함된 디지털 트윈과 관계에 대한 정보를 확인할 수 있습니다. 이러한 쿼리는 **Azure Digital Twins 쿼리 언어** 라고도 하는 SQL 같은 사용자 지정 쿼리 언어로 작성합니다. 이 언어는 많은 유사한 기능을 갖춘 [IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-query-language.md)와 비슷합니다.

쿼리 구문에 대한 자세한 예와 쿼리 요청을 실행하는 방법은 [트윈 그래프 쿼리](how-to-query-graph.md)를 참조하세요.

## <a name="about-the-queries"></a>쿼리 정보

Azure Digital Twins 쿼리 언어를 사용하여 다음에 따라 디지털 트윈을 검색할 수 있습니다.
* 속성 ( [태그 속성](how-to-use-tags.md)포함)
* 모델
* 관계
  - 관계의 속성

클라이언트 앱에서 서비스로 쿼리를 제출하려면 Azure Digital Twins [Query API](/rest/api/digital-twins/dataplane/query)를 사용합니다. API를 사용하는 한 가지 방법은 [Azure Digital Twins용 SDK](concepts-apis-sdks.md#overview-data-plane-apis) 중 하나를 사용하는 것입니다.

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="considerations-for-querying"></a>쿼리 고려 사항

Azure Digital Twins에 대한 쿼리를 작성하는 경우 다음 고려 사항을 염두에 두어야 합니다.
* **대/소문자 구분**: 모든 Azure Digital twins 쿼리 작업은 대/소문자를 구분하므로 모델에 정의된 정확한 이름을 사용해야 합니다. 속성 이름의 철자가 틀렸거나 대/소문자가 잘못된 경우에는 결과 집합이 비어 있고 오류가 반환되지 않습니다.
* **이스케이프 작은따옴표**: 쿼리 텍스트의 데이터에 작은따옴표 문자가 포함된 경우 따옴표를 `\` 문자로 이스케이프해야 합니다. *D'Souza* 의 속성 값을 처리하는 예는 다음과 같습니다.

  :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="EscapedSingleQuote":::

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

## <a name="next-steps"></a>다음 단계

[트윈 그래프 쿼리](how-to-query-graph.md)에서 쿼리를 작성하는 방법을 알아보고 클라이언트 코드 예를 확인하세요.