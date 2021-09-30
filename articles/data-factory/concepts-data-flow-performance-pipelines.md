---
title: 데이터 흐름 매핑 시 파이프라인 성능 최적화
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인의 파이프라인에서 데이터 흐름 실행을 최적화 하는 방법에 대해 알아봅니다.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 3545d7e8bbf8131c9fe454b39ea57a23cd233264
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294375"
---
# <a name="using-data-flows-in-pipelines"></a>파이프라인에서 데이터 흐름 사용 

여러 데이터 흐름이 있는 복잡한 파이프라인을 빌드할 때 논리 흐름은 타이밍과 비용에 큰 영향을 줄 수 있습니다. 이 섹션에서는 다양한 아키텍처 전략의 영향에 대해 설명합니다.

## <a name="executing-data-flows-in-parallel"></a>병렬로 데이터 흐름 실행

여러 데이터 흐름을 병렬로 실행하는 경우 서비스는 각 활동에 대해 별도의 Spark 클러스터를 스핀업합니다. 이렇게 하면 각 작업을 격리하여 병렬로 실행할 수 있지만 여러 클러스터가 동시에 실행됩니다.

데이터 흐름이 병렬로 실행 되는 경우 사용 되지 않는 여러 웜 풀이 발생 하므로 라이브 속성에 Azure IR 시간을 사용 하지 않는 것이 좋습니다.

> [!TIP]
> 각 활동에 대해 동일한 데이터 흐름을 여러 번 실행하는 대신, 데이터 레이크에 데이터를 준비하고 와일드 카드 경로를 사용하여 단일 데이터 흐름에서 데이터를 처리합니다.

## <a name="execute-data-flows-sequentially"></a>순차적으로 데이터 흐름 실행

데이터 흐름 활동을 순서대로 실행하는 경우 Azure IR 구성에서 TTL을 설정하는 것이 좋습니다. 이 서비스는 계산 리소스를 다시 사용 하 여 클러스터 시작 시간이 빨라집니다. 각 작업은 계속 격리 되 고 각 실행에 대 한 새 Spark 컨텍스트를 수신 합니다. 순차적인 활동 간의 시간을 더 줄이려면 Azure IR에서 **빠른 다시 사용** 확인란을 설정 하 여 기존 클러스터를 다시 사용 하도록 서비스에 지시 합니다.

## <a name="overloading-a-single-data-flow"></a>단일 데이터 흐름 오버로드

모든 논리를 단일 데이터 흐름 내에 배치하면 서비스는 전체 작업을 단일 Spark 인스턴스에서 실행합니다. 이는 비용을 절감하는 방법처럼 보일 수 있지만, 여러 논리 흐름이 뒤섞이기 때문에 모니터링 및 디버그가 어려울 수 있습니다. 한 구성 요소에서 오류가 발생하면 작업의 다른 부분도 모두 실패합니다. 비즈니스 논리의 독립적인 흐름을 기준으로 데이터 흐름을 구성하는 것이 좋습니다. 데이터 흐름이 너무 큰 경우 여러 독립 구성 요소로 분할하면 모니터링 및 디버깅이 더 쉬워집니다. 데이터 흐름의 변환 수에 대한 하드 한도는 없지만 너무 많으면 작업이 복잡해집니다.

## <a name="execute-sinks-in-parallel"></a>병렬로 싱크 실행

데이터 흐름 싱크의 기본 동작은 각 싱크를 순차적으로 실행하고 싱크에서 오류가 발생할 경우 데이터 흐름을 실패시키는 것입니다. 또한 데이터 흐름 속성으로 이동하여 싱크에 대해 다른 우선 순위를 설정하지 않는 한 모든 싱크는 기본적으로 동일한 그룹으로 지정됩니다.

데이터 흐름을 사용하면 UI 디자이너의 데이터 흐름 속성 탭에서 싱크를 그룹으로 묶을 수 있습니다. 동일한 그룹 번호를 사용하여 싱크의 실행 순서를 설정하고 싱크를 그룹으로 묶을 수 있습니다. 그룹을 쉽게 관리할 수 있도록 서비스에 동일한 그룹의 싱크를 병렬로 실행하도록 요청할 수 있습니다.

파이프라인 실행 데이터 흐름 활동의 "싱크 속성" 섹션에는 병렬 싱크 로드를 설정하는 옵션이 있습니다. "병렬로 실행"을 설정하면 데이터 흐름은 연결된 싱크에 순차적으로 쓰지 않고 동시에 씁니다. 병렬 옵션을 사용하려면 싱크를 그룹으로 묶고 새 분기 또는 조건부 분할을 통해 동일한 스트림에 연결해야 합니다.

## <a name="next-steps"></a>다음 단계

- [데이터 흐름 성능 개요](concepts-data-flow-performance.md)
- [원본 최적화](concepts-data-flow-performance-sources.md)
- [싱크 최적화](concepts-data-flow-performance-sinks.md)
- [변환 최적화](concepts-data-flow-performance-transformations.md)

성능과 관련된 다음과 같은 다른 데이터 흐름 문서를 참조하세요.

- [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
- [데이터 흐름 성능 모니터링](concepts-data-flow-monitoring.md)
- [Integration Runtime 성능](concepts-integration-runtime-performance.md)
