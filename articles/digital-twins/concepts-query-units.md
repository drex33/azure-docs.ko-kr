---
title: Azure Digital Twins의 쿼리 단위
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에서 쿼리 단위의 청구 개념 이해
author: baanders
ms.author: baanders
ms.date: 9/16/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: eb5e7970c2ce44b5ee96f671464a3029cb9429b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631004"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure Digital Twins의 쿼리 단위 

Azure Digital Twins **QU(쿼리 단위)** 는 [쿼리 API](/rest/api/digital-twins/dataplane/query)를 사용하여 [Azure Digital Twins 쿼리](how-to-query-graph.md)를 실행하는 데 사용되는 주문형 계산 단위입니다. 

Azure Digital Twins에서 지원하는 쿼리 작업을 수행하는 데 필요한 CPU, IOPS 및 메모리와 같은 시스템 리소스를 추상화하여 쿼리 단위 사용량을 추적할 수 있습니다.

쿼리를 실행하는 데 사용되는 쿼리 단위의 양은 다음에 의해 영향을 받습니다.

* 쿼리의 복잡성
* 결과 집합의 크기(결과 10개를 반환하는 쿼리는 하나의 결과만 반환하는 비슷한 복잡성의 쿼리보다 더 많은 QU를 사용함)

이 문서에서는 쿼리 단위를 이해하고 쿼리 단위 소비를 추적하는 방법을 설명합니다.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Azure Digital Twins에서 쿼리 단위 소비 찾기

Azure Digital Twins [쿼리 API](/rest/api/digital-twins/dataplane/query)를 사용하여 쿼리를 실행할 때 응답 헤더를 검사하여 쿼리에 사용된 QU 수를 추적할 수 있습니다. Azure Digital Twins에서 다시 전송된 응답에서 "쿼리 요금"을 찾습니다.

[Azure Digital Twins SDK](concepts-apis-sdks.md)를 사용하면 페이징할 수 있는 응답에서 쿼리 요금 헤더를 추출할 수 있습니다. 이 섹션에서는 디지털 트윈을 쿼리하는 방법과 페이징할 수 있는 응답을 반복하여 쿼리 요금 헤더를 추출하는 방법을 보여줍니다. 

다음 코드 조각에서는 쿼리 API를 호출할 때 발생하는 쿼리 요금을 추출하는 방법을 보여줍니다. 먼저 응답 페이지를 반복하여 쿼리 요금 헤더에 액세스한 다음, 각 페이지 내의 디지털 트윈 결과를 반복합니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 쿼리에 대해 자세히 알아보려면 다음을 방문하세요.

* [쿼리 언어](concepts-query-language.md)
* [쌍 그래프 쿼리](how-to-query-graph.md)
* [쿼리 API 참조 설명서](/rest/api/digital-twins/dataplane/query/querytwins)

[Azure Digital Twins 서비스 제한](reference-service-limits.md)에서 Azure Digital Twins 쿼리 관련 제한을 찾을 수 있습니다.