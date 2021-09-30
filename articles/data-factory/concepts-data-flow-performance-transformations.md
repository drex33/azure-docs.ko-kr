---
title: 매핑 데이터 흐름에서 변환 성능 최적화
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 데이터 흐름을 매핑할 때 변환 성능을 최적화하는 방법을 알아봅니다.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 28b7847c4341751bd7a2beacdf61e138551f991c
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294379"
---
# <a name="optimizing-transformations"></a>변환 최적화

다음 전략을 사용하여 Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 데이터 흐름을 매핑할 때 변환 성능을 최적화할 수 있습니다.

## <a name="optimizing-joins-exists-and-lookups"></a>조인, 존재 및 조회 최적화

### <a name="broadcasting"></a>브로드캐스트

조인, 조회 및 존재 변환에서 하나 또는 두 데이터 스트림이 작업자 노드 메모리에 적합한 작은 크기인 경우 **브로드캐스트** 를 사용하도록 설정하여 성능을 최적화할 수 있습니다. 브로드캐스트를 사용하면 클러스터의 모든 노드에 작은 데이터 프레임을 보내게 됩니다. 그러면 Spark 엔진은 대규모 스트림의 데이터를 다시 섞지 않고도 조인을 수행할 수 있습니다. 기본적으로 spark 엔진은 조인의 한 쪽에서 브로드캐스트할지 여부를 자동으로 결정합니다. 들어오는 데이터에 대해 잘 알고 있고 한 스트림이 다른 스트림보다 많이 작을 것이라는 사실을 알고 있는 경우 **고정** 브로드캐스트를 선택할 수 있습니다. 고정 브로드캐스트는 선택한 스트림을 Spark에서 브로드캐스트하게 합니다. 

브로드캐스트된 데이터의 크기가 Spark 노드에 비해 너무 크면 메모리 부족 오류가 발생할 수 있습니다. 메모리 부족 오류를 방지하려면 **메모리 최적화** 클러스터를 사용합니다. 데이터 흐름을 실행하는 동안 브로드캐스트 시간 제한이 발생하는 경우 브로드캐스트 최적화를 해제하면 됩니다. 그러나 이로 인해 데이터 흐름이 더 느리게 수행됩니다.

큰 데이터베이스 쿼리와 같이 쿼리하는 데 더 오래 걸릴 수 있는 데이터 원본으로 작업하는 경우 조인에 대해 브로드캐스트를 끄는 것이 좋습니다. 원본의 쿼리 시간이 길면 클러스터가 컴퓨팅 노드에 브로드캐스트를 시도할 때 Spark 시간 초과가 발생할 수 있습니다. 나중에 조회 변환에 사용하기 위해 값을 집계하는 스트림이 데이터 흐름에 있는 경우에도 브로드캐스트를 끄는 것이 좋습니다. 이 패턴은 Spark 최적화 프로그램을 혼동시키고 시간 초과를 일으킬 수 있습니다.

:::image type="content" source="media/data-flow/joinoptimize.png" alt-text="조인 변환 최적화":::

### <a name="cross-joins"></a>크로스 조인

조인 조건에 리터럴 값을 사용하거나 조인의 양쪽에 일치 항목이 여러 개 있는 경우 Spark는 조인을 크로스 조인으로 실행합니다. 크로스 조인은 조인된 값을 필터링하는 완전한 카티전 곱입니다. 크로스 조인은 다른 조인 형식보다 많이 느립니다. 성능에 영향을 주지 않도록 조인 조건의 양쪽에 열 참조가 있어야 합니다.

### <a name="sorting-before-joins"></a>조인 전 정렬

SSIS와 같은 도구의 병합 조인과 달리, 조인 변환은 필수 병합 조인 작업이 아닙니다. 조인 키를 변환하기 전에 정렬할 필요가 없습니다. 데이터 흐름을 매핑할 때는 정렬 변환을 사용하지 않는 것이 좋습니다.

## <a name="window-transformation-performance"></a>창 변환 성능

[매핑 데이터 흐름의 창 변환](data-flow-window.md)은 변환 설정의 ```over()``` 절에서 선택하는 열 값을 기준으로 데이터를 분할합니다. Windows 변환에는 많은 사람들이 사용하는 다양한 집계 및 분석 함수가 있습니다. 그러나 순위 지정 ```rank()``` 또는 행 번호 ```rowNumber()```를 목적으로 전체 데이터 세트에 대한 창을 생성해야 하는 경우에는 [순위 변환](data-flow-rank.md) 및 [서로게이트 키 변환](data-flow-surrogate-key.md)을 대신 사용하는 것이 좋습니다. 이러한 변환은 해당 함수를 사용하여 전체 데이터 세트 작업을 보다 효율적으로 다시 수행합니다.

## <a name="repartitioning-skewed-data"></a>기울어진 데이터 다시 분할

조인 및 집계와 같은 특정 변환은 데이터 파티션을 다시 섞으며, 이로 인해 경우에 따라 데이터가 기울어질 수 있습니다. 데이터가 기울어졌다는 것은 데이터가 모든 파티션에 균등하게 분산되지 않았다는 뜻입니다. 데이터가 많이 기울어지면 다운스트림 변환 및 싱크 쓰기 속도가 저하될 수 있습니다. 데이터 흐름 실행의 모든 시점에서 모니터링 표시의 변환을 클릭하여 데이터 왜도를 확인할 수 있습니다.

:::image type="content" source="media/data-flow/skewness-kurtosis.png" alt-text="왜도 및 첨도":::

모니터링 표시에는 왜도 및 첨도 메트릭과 함께 각 파티션에 데이터가 분산되는 방식이 표시됩니다. **왜도** 는 데이터에 허용되는 비대칭성과 데이터가 양수, 0, 음수 또는 정의되지 않은 값을 가질 수 있는지 여부를 측정하는 방법입니다. 음수 기울이기는 왼쪽 꼬리가 오른쪽보다 긴 것을 의미합니다. **첨도** 는 데이터 꼬리가 굵은지 아니면 얇은지 측정하는 방법입니다. 높은 첨도 값은 바람직하지 않습니다. 적절한 왜도 범위는 -3에서 3 사이이고 첨도 범위는 10 미만입니다. 이러한 숫자를 해석하는 쉬운 방법은 파티션 차트를 살펴보고 1개 막대가 나머지 막대보다 유달리 큰지 확인하는 것입니다.

변환 후 데이터가 균등하게 분할되지 않으면 [최적화 탭](concepts-data-flow-performance.md#optimize-tab)을 사용하여 다시 분할할 수 있습니다. 데이터를 다시 섞는 데 시간이 걸리고 데이터 흐름 성능이 향상되지 않을 수 있습니다.

> [!TIP]
> 데이터를 다시 분할하지만 데이터를 다시 섞는 다운스트림 변환이 있는 경우 조인 키로 사용되는 열에서 해시 분할을 사용합니다.

## <a name="next-steps"></a>다음 단계

- [데이터 흐름 성능 개요](concepts-data-flow-performance.md)
- [원본 최적화](concepts-data-flow-performance-sources.md)
- [싱크 최적화](concepts-data-flow-performance-sinks.md)
- [파이프라인에서 데이터 흐름 사용](concepts-data-flow-performance-pipelines.md)

성능과 관련된 다음과 같은 다른 데이터 흐름 문서를 참조하세요.

- [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
- [데이터 흐름 성능 모니터링](concepts-data-flow-monitoring.md)
- [Integration Runtime 성능](concepts-integration-runtime-performance.md)
