---
title: 매핑 데이터 흐름 디버그 모드
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 데이터 흐름을 빌드할 때 대화형 디버그 세션을 시작합니다.
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/01/2021
ms.openlocfilehash: 8d052be49a545e34ce44dc53174a5dbca7c3d220
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402969"
---
# <a name="mapping-data-flow-debug-mode"></a>매핑 데이터 흐름 디버그 모드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>개요

Azure Data Factory 및 Synapse Analytics 매핑 데이터 흐름의 디버그 모드를 사용하면 데이터 흐름을 빌드하고 디버그하는 동안 데이터 셰이프 변환을 대화형으로 볼 수 있습니다. 디버그 세션은 데이터 흐름의 파이프라인 디버그를 실행 중일 때뿐만 아니라 데이터 흐름 설계 세션에서도 사용할 수 있습니다. 디버그 모드를 설정하려면 데이터 흐름 활동이 있을 때 데이터 흐름 캔버스 또는 파이프라인 캔버스의 위쪽 막대에서 **데이터 흐름 디버그** 단추를 사용합니다.

:::image type="content" source="media/data-flow/debug-button.png" alt-text="디버그 슬라이더 1이 어디에 있는지 보여 주는 스크린샷":::

:::image type="content" source="media/data-flow/debug-button-4.png" alt-text="디버그 슬라이더 2가 어디에 있는지 보여 주는 스크린샷":::

슬라이더를 켜면 사용할 통합 런타임 구성을 선택하라는 메시지가 표시됩니다. AutoResolveIntegrationRuntime을 선택하면 TTL(Time to live)이 60분인 일반 컴퓨팅 코어가 8개인 클러스터가 회전합니다. 세션 시간이 초과되기 전에 더 많은 유휴 팀을 허용하려면 더 높은 TTL 설정을 선택할 수 있습니다. 데이터 흐름 통합 런타임에 대한 자세한 내용은 [통합 런타임 성능](concepts-integration-runtime-performance.md)을 참조하세요.

:::image type="content" source="media/data-flow/debug-new-1.png" alt-text="IR 선택 디버그":::

디버그 모드를 설정하면 활성 Spark 클러스터로 데이터 흐름을 대화형으로 빌드합니다. 디버그를 해제하면 세션이 닫힙니다. 디버그 세션이 켜져 있는 동안 Data Factory 발생하는 시간별 요금을 알고 있어야 합니다.

대부분의 경우 작업을 게시하기 전에 비즈니스 논리의 유효성을 검사하고 데이터 변환을 볼 수 있도록 디버그 모드에서 데이터 흐름을 빌드하는 것이 좋습니다. 파이프라인 패널의 "디버그" 단추를 사용하여 파이프라인의 데이터 흐름을 테스트합니다.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions.png" alt-text="데이터 흐름 디버그 세션 보기":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions-synapse.png" alt-text="데이터 흐름 디버그 세션 보기":::

---

> [!NOTE]
> 사용자가 브라우저 UI에서 시작하는 모든 디버그 세션은 자체 Spark 클러스터가 있는 새 세션입니다. 위의 디버그 세션에 대한 모니터링 보기를 사용하여 디버그 세션을 보고 관리할 수 있습니다. TTL 시간을 포함하여 각 디버그 세션이 실행되는 매 시간마다 요금이 부과됩니다.

## <a name="cluster-status"></a>클러스터 상태

클러스터가 디버그될 준비가 되면 디자인 화면 위쪽의 클러스터 상태 표시기가 녹색으로 바뀝니다. 클러스터가 이미 웜 상태이면 녹색 표시기가 거의 즉시 나타납니다. 디버그 모드를 시작할 때 클러스터가 아직 실행되지 않은 경우 Spark 클러스터는 콜드 부팅을 수행합니다. 환경이 대화형 디버깅을 수행할 준비가 될 때까지 표시기가 회전합니다.

디버깅이 완료되면 Spark 클러스터가 종료되고 더 이상 디버그 작업에 대한 요금이 청구되지 않도록 디버그 스위치를 해제합니다.

## <a name="debug-settings"></a>디버그 설정

디버그 모드를 설정하면 데이터 흐름에서 데이터를 미리 보는 방법을 편집할 수 있습니다. 데이터 흐름 캔버스 도구 모음에서 "디버그 설정"을 클릭하여 디버그 설정을 편집할 수 있습니다. 여기에서 각 원본 변환에 사용할 행 제한 또는 파일 원본을 선택할 수 있습니다. 이 설정의 행 제한은 현재 디버그 세션에만 적용됩니다. Azure Synapse Analytics 원본에 사용할 연결된 스테이징 서비스를 선택할 수도 있습니다. 

:::image type="content" source="media/data-flow/debug-settings.png" alt-text="디버그 설정":::

데이터 흐름 또는 참조된 데이터 세트에 매개 변수가 있는 경우 **매개 변수** 탭을 선택하여 디버깅 중에 사용할 값을 지정할 수 있습니다.

여기에서 샘플링 설정을 사용하여 데이터의 샘플 파일 또는 샘플 테이블을 가리키면 원본 데이터 세트를 변경할 필요가 없습니다. 여기에서 샘플 파일 또는 테이블을 사용하면 데이터 하위 집합에 대해 테스트하는 동안 데이터 흐름에서 동일한 논리 및 속성 설정을 유지 관리할 수 있습니다.

:::image type="content" source="media/data-flow/debug-settings2.png" alt-text="디버그 설정 매개 변수":::

데이터 흐름의 디버그 모드에 사용되는 기본 IR은 4코어 단일 드라이버 노드가 있는 작은 4코어 단일 작업자 노드입니다. 이는 데이터 흐름 논리를 테스트할 때 더 작은 데이터 샘플에서 잘 작동합니다. 데이터 미리 보기 중에 디버그 설정에서 행 제한을 확장하거나 파이프라인 디버그 중에 원본에 더 많은 샘플링된 행을 설정하는 경우 새 Azure Integration Runtime에서 더 큰 컴퓨팅 환경을 설정하는 것을 고려할 수 있습니다. 그런 다음 더 큰 컴퓨팅 환경을 사용하여 디버그 세션을 다시 시작할 수 있습니다.

## <a name="data-preview"></a>데이터 미리 보기

디버그를 켜면 데이터 미리 보기 탭이 맨 아래 패널에 표시됩니다. 디버그 모드를 설정하지 않으면 데이터 흐름에서 검사 탭의 각 변환 내부 및 외부의 현재 메타데이터만 표시합니다. 데이터 미리 보기는 디버그 설정에서 제한으로 설정한 행 수만 쿼리합니다. **새로 고침** 을 클릭하여 데이터 미리 보기를 가져옵니다.

:::image type="content" source="media/data-flow/datapreview.png" alt-text="데이터 미리 보기":::

> [!NOTE]
> 파일 원본은 읽을 행이 아닌 표시되는 행만 제한합니다. 매우 큰 데이터 세트의 경우 해당 파일의 작은 부분을 가져와서 테스트에 사용하는 것이 좋습니다. 파일 데이터 세트 유형인 각 원본에 대한 디버그 설정에서 임시 파일을 선택할 수 있습니다.

Data Flow에서 디버그 모드로 실행하는 경우에는 데이터가 싱크 변환에 기록되지 않습니다. 디버그 세션은 변환에 대한 테스트 도구 역할을 수행하기 위한 것입니다. 디버그 중에는 싱크가 필요하지 않으며 데이터 흐름에서 무시됩니다. 싱크에서 데이터 쓰기를 테스트하려면 파이프라인에서 데이터 Flow 실행하고 파이프라인에서 디버그 실행을 사용합니다.

데이터 미리 보기는 Spark 메모리의 데이터 프레임에서 행 제한 및 데이터 샘플링을 사용한 변환된 데이터의 스냅샷입니다. 따라서 이 시나리오에서는 싱크 드라이버를 활용하거나 테스트하지 않습니다.

### <a name="testing-join-conditions"></a>조인 조건 테스트

조인, 존재 또는 조회 변환을 단위 테스트할 때 테스트에 알려진 작은 데이터 세트를 사용해야 합니다. 위의 디버그 설정 옵션을 사용하여 테스트에 사용할 임시 파일을 설정할 수 있습니다. 이는 많은 데이터 세트에서 행을 제한하거나 샘플링할 때 테스트를 위해 흐름으로 읽어올 행과 키를 예측할 수 없기 때문에 필요합니다. 따라서 결과가 명확하지 않으므로 조인 조건이 실패할 수 있습니다.

### <a name="quick-actions"></a>빠른 작업

데이터 미리 보기가 표시되면 빠른 변환을 생성하여 열을 형식 캐스팅, 제거 또는 수정할 수 있습니다. 열 머리글을 클릭한 다음 데이터 미리 보기 도구 모음에서 옵션 중 하나를 선택합니다.

:::image type="content" source="media/data-flow/quick-actions1.png" alt-text="스크린샷은 형식 캐스팅, 수정, 통계 및 제거 옵션이 있는 데이터 미리 보기 도구 모음을 보여 줍니다.":::

수정을 선택하면 데이터 미리 보기가 즉시 새로 고쳐집니다. 오른쪽 상단의 **확인** 을 클릭하여 새 변환을 생성합니다.

:::image type="content" source="media/data-flow/quick-actions2.png" alt-text="스크린샷에 확인 단추가 표시됩니다.":::

**형식 캐스팅** 및 **수정** 은 파생 열 변환을 생성하고 **제거** 는 선택 변환을 생성합니다.

:::image type="content" source="media/data-flow/quick-actions3.png" alt-text="스크린샷은 파생 열의 설정을 보여 줍니다.":::

> [!NOTE]
> 데이터 흐름을 편집하는 경우 빠른 변환을 추가하기 전에 데이터 미리 보기를 다시 가져와야 합니다.

### <a name="data-profiling"></a>데이터 프로파일링

데이터 미리 보기 탭에서 열을 선택하고 데이터 미리 보기 도구 모음에서 **통계** 를 클릭하면 각 필드에 대한 자세한 통계가 포함된 데이터 그리드 오른쪽 맨 위에 차트가 팝업됩니다. 서비스는 표시할 차트 형식의 데이터 샘플링에 따라 결정합니다. 카디널리티가 낮은 범주 및 숫자 데이터는 데이터 값 빈도를 보여 주는 막대형 차트를 표시하는 반면, 카디널리티가 높은 필드는 기본적으로 NULL/NOT NULL 차트로 설정됩니다. 문자열 필드의 max/len 길이, 숫자 필드의 min/max 값, 표준 편차, 백분위 수, 개수 및 평균도 볼 수 있습니다.

:::image type="content" source="media/data-flow/stats.png" alt-text="열 통계":::

## <a name="next-steps"></a>다음 단계

* 데이터 흐름 빌드 및 디버깅을 마치면 [파이프 라인에서 이를 실행합니다.](control-flow-execute-data-flow-activity.md)
* 데이터 흐름으로 파이프라인을 테스트할 때 파이프라인 [디버그 실행 옵션](iterative-development-debugging.md)을 사용합니다.
