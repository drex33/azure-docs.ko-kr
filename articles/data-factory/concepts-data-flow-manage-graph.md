---
title: 매핑 데이터 흐름 그래프 관리
titleSuffix: Azure Data Factory & Azure Synapse
description: 매핑 데이터 흐름 그래프를 효과적으로 관리하고 편집하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: da9b3148506e437f7bf5d90fc0fbe44e0a2d27f0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812105"
---
# <a name="managing-the-mapping-data-flow-graph"></a>매핑 데이터 흐름 그래프 관리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

매핑 데이터 흐름은 데이터 흐름 그래프로 알려진 디자인 화면을 사용하여 작성됩니다. 그래프에서 변환 논리는 왼쪽에서 오른쪽으로 빌드되고 추가 데이터 스트림은 하향식으로 추가됩니다. 새 변환을 추가하려면 기존 변환의 오른쪽 아래에 있는 더하기 기호를 선택합니다.

:::image type="content" source="media/data-flow/canvas-2.png" alt-text="캔버스":::

데이터 흐름이 더 복잡해지면 다음 메커니즘을 사용하여 데이터 흐름 그래프를 효과적으로 탐색하고 관리하세요. 

## <a name="moving-transformations"></a>변환 이동

데이터 흐름 매핑 시 연결된 변환 논리 집합을 **스트림** 이라고 합니다. **들어오는 스트림** 필드는 현재 변환을 공급하는 데이터 스트림을 나타냅니다. 각 변환에는 함수에 따라 하나 또는 두 개의 들어오는 스트림이 있으며 출력 스트림을 나타냅니다. 들어오는 스트림의 출력 스키마는 현재 변환에서 참조할 수 있는 열 메타데이터를 결정합니다.

:::image type="content" source="media/data-flow/move-nodes.png" alt-text="노드 이동":::

파이프라인 캔버스와 달리 데이터 흐름 변환은 끌어서 놓기 모델을 사용하여 편집할 수 없습니다. 변환의 들어오는 스트림을 변경하거나 "이동"하려면 **들어오는 스트림** 드롭다운에서 다른 값을 선택합니다. 이 작업을 수행하면 모든 다운스트림 변환이 편집된 변환과 함께 이동합니다. 그래프는 자동으로 업데이트되어 새 논리 흐름을 표시합니다. 들어오는 스트림을 이미 다운스트림이 변환이 있는 변환으로 변경하면 새 분기 또는 병렬 데이터 스트림이 만들어집니다. [매핑 데이터 흐름의 새 분기](data-flow-new-branch.md)에 대해 자세히 알아보세요.

## <a name="hide-graph-and-show-graph"></a>그래프 숨기기 및 그래프 표시

변환을 편집할 때 구성 패널을 확장하여 전체 캔버스를 사용하고 그래프를 숨길 수 있습니다. 캔버스 오른쪽에 있는 위쪽 펼침 단추를 클릭합니다.

:::image type="content" source="media/data-flow/hide-graph.png" alt-text="그래프 숨기기":::

그래프가 숨겨져 있으면 **다음** 또는 **이전** 을 클릭하여 스트림 내에서 변환 간 이동이 가능합니다. 아래쪽 펼침 단추를 클릭하여 그래프를 표시합니다.

:::image type="content" source="media/data-flow/show-graph.png" alt-text="그래프 표시":::

## <a name="searching-for-transformations"></a>변환 검색

그래프에서 변환을 신속하게 찾으려면 확대/축소 설정 위의 **검색** 아이콘을 클릭합니다.

:::image type="content" source="media/data-flow/search-1.png" alt-text="검색 단추를 표시하는 스크린샷입니다.":::

변환 이름 또는 설명으로 검색하여 변환을 찾을 수 있습니다.

:::image type="content" source="media/data-flow/search-2.png" alt-text="검색 텍스트 상자를 표시하는 스크린샷입니다.":::

## <a name="hide-reference-nodes"></a>참조 노드 숨기기

데이터 흐름에 조인, 조회, 존재 또는 통합 변환이 있는 경우 데이터 흐름은 모든 들어오는 스트림에 대한 참조 노드를 표시합니다. 사용되는 세로 간격을 최소화하려면 참조 노드를 최소화할 수 있습니다. 그러려면 캔버스를 마우스 오른쪽 단추로 클릭하고 **참조 노드 숨기기** 를 선택합니다.

:::image type="content" source="media/data-flow/hide-reference-nodes.png" alt-text="참조 노드 숨기기":::

## <a name="next-steps"></a>다음 단계

데이터 흐름 논리를 완료한 후 [디버그 모드](concepts-data-flow-debug-mode.md)를 켜고 데이터 미리 보기에서 테스트합니다.
