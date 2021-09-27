---
title: Azure Data Factory의 파워 쿼리 작업
description: Data Factory 파이프라인의 데이터 랭글링 기능에 파워 쿼리 활동을 사용하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 07e663b7b631cbc083916e48924d740255e46e37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638770"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Azure Data Factory의 파워 쿼리 작업

파워 쿼리 활동을 사용하여 Data Factory 파이프라인에서 대규모로 랭글링 데이터를 실행하는 파워 쿼리 매시업을 빌드하고 실행할 수 있습니다. 새 리소스 메뉴 옵션 또는 파이프라인에 파워 작업을 추가하여 새 파워 쿼리 매시업을 만들 수 있습니다.

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="팩터리 리소스 창의 파워 쿼리 스크린샷":::

이전에는 Azure Data Factory의 데이터 랭글링을 Data Flow 메뉴 옵션에서 작성했으나, 새 파워 쿼리 작업에서 작성할 수 있도록 변경되었습니다. 파워 쿼리 매시업 편집기 내에서 직접 작업하여 대화형 데이터 검색을 수행한 뒤 작업을 저장할 수 있습니다. 완료되면 파워 쿼리 활동을 사용하여 파이프라인에 추가할 수 있습니다. Azure Data Factory는 Azure Data Factory의 데이터 흐름 Spark 환경을 사용하여 자동으로 스케일링하고 데이터 랭글링을 운영합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>데이터 흐름 스크립트로 변환

Azure Data Factory는 파워 쿼리 작업을 사용하여 스케일링하기 위해 ```M``` 스크립트를 데이터 흐름 스크립트로 변환하므로, Azure Data Factory의 데이터 흐름 Spark 환경을 사용하여 규모에 따라 파워 쿼리를 실행할 수 있습니다. 코드 없는 데이터 준비를 사용하여 랭글링 데이터 흐름을 작성합니다. 사용 가능한 함수 목록은 [변환 함수](wrangling-functions.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Data Factory에서 파워 쿼리](wrangling-tutorial.md)를 사용한 데이터 랭글링의 개념에 대한 자세한 정보.
