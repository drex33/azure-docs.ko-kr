---
title: 매핑 데이터 흐름의 피벗 해제 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics의 매핑 데이터 흐름 피벗 해제 변환에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 28c89622702435f178e241125545f8f0ac86eb18
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059590"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 피벗 해제 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

단일 레코드의 여러 열 값을 단일 열에서 동일한 값을 가진 여러 레코드로 확장 하 여 정규화 되지 않은 데이터 집합을 보다 정규화 된 버전으로 전환 하는 방법으로 매핑 데이터 흐름에서 Unpivot을 사용 합니다.

:::image type="content" source="media/data-flow/unpivot1.png" alt-text="메뉴에서 선택한 내보내기 작업을 보여 주는 스크린샷":::

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>그룹 해제 기준

:::image type="content" source="media/data-flow/unpivot5.png" alt-text="그룹 해제 기준 탭이 선택된 피벗 해제 설정을 보여 주는 스크린샷":::

먼저 피벗 해제 집계에 대해 그룹을 해제하려는 열을 설정합니다. 열 목록 옆에 있는 + 기호를 사용하여 그룹 해제할 열을 하나 이상 설정합니다.

## <a name="unpivot-key"></a>피벗 해제 키

:::image type="content" source="media/data-flow/unpivot6.png" alt-text="피벗 해제 키 탭이 선택된 피벗 해제 설정을 보여 주는 스크린샷":::

Unpivot 키는 서비스가 열에서 행으로 피벗 될 열입니다. 기본적으로 이 필드에 대한 데이터 세트의 각 고유 값은 행으로 피벗됩니다. 그러나 행 값으로 피벗하려는 데이터 세트의 값을 선택적으로 입력할 수 있습니다.

## <a name="unpivoted-columns"></a>피벗 해제된 열

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="데이터 미리 보기 탭이 선택된 피벗 해제 설정을 보여 주는 스크린샷":::

마지막으로, 행으로 변환된 피벗 해제 열의 값을 저장하기 위한 열 이름을 선택합니다.

(선택 사항) Null 값이 있는 행을 삭제할 수 있습니다.

예를 들어, SumCost는 위에 공유된 예제에서 선택한 열 이름입니다.

:::image type="content" source="media/data-flow/unpivot3.png" alt-text="Fruit 열을 피벗 해제 키로 사용하여 피벗 해제 변환 전후에 PO, Vendor, Fruit 열을 보여 주는 이미지":::

열 정렬을 “기본”으로 설정하면 단일 값에서 새 피벗 해제된 열이 모두 그룹화됩니다. 열 정렬을 “수평”으로 설정하면 기존 열에서 생성된 새 피벗 해제된 열이 그룹화됩니다.

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="변환의 결과를 보여 주는 스크린샷":::

피벗 해제된 최종 데이터 결과 집합은 이제 개별 행 값으로 피벗 해제된 열 합계를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

[피벗 변환](data-flow-pivot.md)을 사용하여 행을 열로 피벗합니다.
