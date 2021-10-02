---
title: Azure Data Factory의 랭글링 데이터 흐름 시작
description: 랭글링 데이터 흐름을 사용하여 Azure Data Factory에서 데이터를 준비하는 방법에 대한 자습서
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 43ff1591730f240e1ae3ebcfc4066fdc34835372
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355564"
---
# <a name="prepare-data-with-data-wrangling"></a>데이터 랭글링으로 데이터 준비

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

데이터 팩터리의 데이터 랭글링을 사용하면 대화형 파워 쿼리 매시업을 ADF에 기본적으로 빌드할 수 있으며 ADF 파이프라인 내에서 대규모로 실행할 수 있습니다.

## <a name="create-a-power-query-activity"></a>파워 쿼리 활동 만들기

Azure Data Factory에서 파워 쿼리를 만드는 방법에는 두 가지가 있습니다. 한 가지 방법은 더하기 아이콘을 클릭 하 고 팩터리 리소스 창에서 **파워 쿼리** 을 선택 하는 것입니다.

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="팩터리 리소스 창의 파워 쿼리 스크린샷.":::

다른 방법은 파이프라인 캔버스의 작업 창에 있습니다. **파워 쿼리** 아코디언을 열고 **파워 쿼리** 활동을 캔버스로 끌어 놓습니다.

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="데이터 랭글링 옵션을 강조 표시하는 스크린샷.":::

## <a name="author-a-power-query-data-wrangling-activity"></a>파워 쿼리 데이터 랭글링 활동 작성

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWJd3T]
> 
파워 쿼리 매시업에 대한 **원본 데이터 세트** 를 추가합니다. 기존 데이터 세트를 선택하거나 새 데이터 세트를 만들 수 있습니다. 매시업을 저장한 후에는 파워 쿼리 데이터 랭글링 활동을 파이프라인에 추가하고 싱크 데이터 세트를 선택하여 ADF에 데이터를 저장할 위치를 알릴 수 있습니다. 하나 이상의 원본 데이터 세트를 선택할 수 있지만 지금은 싱크가 하나만 허용됩니다. 싱크 데이터 세트 선택은 필수가 아니지만 하나 이상의 원본 데이터 세트 선택은 필수입니다.

:::image type="content" source="media/wrangling-data-flow/tutorial4.png" alt-text="랭글링":::

**만들기** 를 클릭하여 파워 쿼리 온라인 매시업 편집기를 엽니다.

먼저 매시업 편집기에 대한 데이터 세트 원본을 선택합니다.

:::image type="content" source="media/wrangling-data-flow/power-query-new-source.png" alt-text="파워 쿼리 원본.":::

파워 쿼리 빌드를 완료하면 이를 저장하고 매시업을 파이프라인에 작업으로 추가할 수 있습니다. 싱크 데이터 세트 속성을 설정할 때입니다.

:::image type="content" source="media/wrangling-data-flow/power-query-new-sink.png" alt-text="파워 쿼리 싱크.":::

코드 없는 데이터 준비를 사용하여 랭글링 파워 쿼리를 작성합니다. 사용 가능한 함수 목록은 [변환 함수](wrangling-functions.md)를 참조하세요. ADF는 M 스크립트를 데이터 흐름 스크립트로 변환하므로 Azure Data Factory 데이터 흐름 Spark 환경을 사용하여 대규모로 파워 쿼리를 실행할 수 있습니다.

:::image type="content" source="media/wrangling-data-flow/tutorial6.png" alt-text="데이터 랭글링 파워 쿼리 작성 프로세스를 보여주는 스크린샷.":::

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>파워 쿼리 데이터 랭글링 활동 실행 및 모니터링

파워 쿼리 활동의 파이프라인 디버그 실행을 실행하려면 파이프라인 캔버스에서 **디버그** 를 클릭합니다. 파이프라인을 게시하면 **지금 트리거** 로 마지막에 게시된 파이프라인의 주문형 실행을 실행합니다. 파워 쿼리 파이프라인은 모든 기존 Azure Data Factory 트리거를 사용하여 예약할 수 있습니다.

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="파워 쿼리 데이터 랭글링 활동을 추가하는 방법을 보여주는 스크린샷.":::

**모니터** 탭으로 이동하여 트리거된 파워 쿼리 활동 실행의 출력을 시각화합니다.

:::image type="content" source="media/wrangling-data-flow/tutorial2.png" alt-text="트리거된 랭글링 파워 쿼리 활동 실행의 출력을 보여주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[매핑 데이터 흐름 만들기](tutorial-data-flow.md) 방법에 대해 알아봅니다.
