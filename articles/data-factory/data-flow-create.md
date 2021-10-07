---
title: 매핑 데이터 흐름 만들기
description: Azure Data Factory 매핑 데이터 흐름을 만드는 방법
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 1c47e42f3186d573fb57f1ebaa89140e0c713c0e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614617"
---
# <a name="create-azure-data-factory-data-flows"></a>Azure Data Factory 데이터 흐름 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

매핑 데이터 흐름은 코딩 없이 대규모로 데이터를 변환하는 방법을 제공합니다. 일련의 변환을 생성하여 데이터 흐름 디자이너에서 데이터 변환 작업을 설계할 수 있습니다. 임의 개수의 원본 변환으로 시작한 다음, 데이터 변환 단계를 수행합니다. 데이터 흐름을 싱크로 완료하여 결과를 대상으로 이동합니다.

## <a name="steps-to-create-a-new-data-flow"></a>새 데이터 흐름을 만드는 단계

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

먼저 Azure Portal [새 V2 Data Factory 만들어](quickstart-create-data-factory-portal.md) 시작합니다. 새 팩터리 만들기 후 포털에서 **Azure Data Factory Studio 열기** 타일을 선택하여 Data Factory Studio를 시작합니다.

:::image type="content" source="media/data-flow-create/open-data-factory-studio-from-portal.png" alt-text="Azure Portal Data Factory Studio를 여는 방법의 스크린샷을 보여줍니다.":::

템플릿 갤러리에서 샘플 데이터 흐름을 추가할 수 있습니다. 갤러리를 찾아보려면 Data Factory Studio에서 **작성자** 탭을 선택하고 더하기 기호를 클릭하여 **파이프라인**  |  **템플릿 갤러리를** 선택합니다.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-data-factory.png" alt-text="Data Factory 템플릿 갤러리를 여는 방법을 보여줍니다.":::

데이터 Flow 범주를 선택하여 사용 가능한 템플릿 중에서 선택합니다.

:::image type="content" source="media/data-flow-create/template-gallery-filtered-for-data-flow.png" alt-text="데이터 흐름에 대해 필터링된 템플릿 갤러리를 표시합니다.":::

템플릿을 사용하지 않고 데이터 팩터리로 직접 데이터 흐름을 추가할 수도 있습니다. Data Factory Studio에서 **작성자** 탭을 선택하고 더하기 기호를 클릭하여 **데이터 Flow**  |  **데이터 Flow** 선택합니다.  

:::image type="content" source="media/data-flow-create/create-data-flow-directly.png" alt-text="빈 데이터 흐름을 직접 만드는 방법의 스크린샷을 보여줍니다.":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

먼저 Azure Portal [새 Synapse 작업 영역을 만들어](../synapse-analytics/quickstart-create-workspace.md) 시작합니다. 새 작업 영역을 만든 후 **Azure Studio 열기** 타일을 선택하여 Data Factory UI를 시작합니다.
    
:::image type="content" source="media/data-flow-create/open-synapse-studio-from-portal.png" alt-text="Azure Portal Synapse Studio 여는 방법의 스크린샷을 보여줍니다.":::

템플릿 갤러리에서 샘플 데이터 흐름을 추가할 수 있습니다.  갤러리를 찾아보려면 Synapse Studio **통합** 탭을 선택하고 더하기 기호를 클릭하여 **갤러리 찾아보기를** 선택합니다.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-synapse.png" alt-text="Data Factory 템플릿 갤러리를 여는 방법을 보여줍니다.":::

범주:데이터 Flow 필터링하여 사용 가능한 템플릿 중에서 선택합니다.

:::image type="content" source="media/data-flow-create/synapse-template-gallery-filtered-for-data-flow.png" alt-text="데이터 흐름에 대해 필터링된 템플릿 갤러리를 표시합니다.":::

템플릿을 사용하지 않고 작업 영역에 직접 데이터 흐름을 추가할 수도 있습니다. Synapse Studio **통합** 탭을 선택하고 더하기 기호를 클릭하여 **파이프라인을** 선택합니다.  그런 다음 파이프라인에서 **이동 & 변환** 활동 섹션을 확장하고 **데이터 흐름을** 파이프라인의 캔버스로 끕니다.

:::image type="content" source="media/data-flow-create/create-pipeline-in-synapse.png" alt-text="빈 파이프라인을 직접 만드는 방법의 스크린샷을 보여줍니다.":::

:::image type="content" source="media/data-flow-create/add-data-flow-to-pipeline-synapse.png" alt-text="빈 데이터 흐름을 파이프라인에 직접 추가하는 방법의 스크린샷을 보여줍니다.":::

---

## <a name="next-steps"></a>다음 단계

* [자습서: 매핑 데이터 흐름을 사용하여 데이터 변환](tutorial-data-flow.md)
* [원본 변환](data-flow-source.md)을 사용하여 데이터 변환 빌드를 시작합니다.
