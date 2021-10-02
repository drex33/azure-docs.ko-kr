---
title: Azure Data Factory 데이터 랭글링
description: Azure Data Factory의 데이터 랭글링 개요
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 996b91040d16a3c2b966bb1b68e83f578d42f583
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354987"
---
# <a name="what-is-data-wrangling"></a>데이터 랭글링이 무엇인가요?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

조직은 매일 계속 증가하는 복잡한 데이터를 정확하게 분석하기 위해 데이터 준비 및 랭글링에 대한 중요한 비즈니스 데이터를 탐색하는 기능을 필요로 합니다. 조직에서 다양한 비즈니스 프로세스의 데이터를 사용하고 시간 값을 줄일 수 있도록 데이터를 준비해야 합니다.

Data Factory는 파워 쿼리를 사용하여 클라우드 규모에서 코드 없는 데이터 준비를 제공합니다. Data Factory는 [파워 쿼리 온라인](/power-query/)과 통합되며 파워 쿼리 M 함수를 파이프라인 작업으로 사용할 수 있도록 합니다.

Data Factory는 M을 Azure Data Factory 데이터 흐름으로 변환하여 파워 쿼리 온라인 매시업 편집기에서 만든 M을 클라우드 규모 실행을 위한 spark 코드로 변환합니다. 파워 쿼리 및 데이터 흐름을 사용하는 랭글링 데이터는 데이터 엔지니어 또는 '시민 데이터 통합자'에게 특히 유용합니다.

## <a name="use-cases"></a>사용 사례

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>신속한 대화형 데이터 탐색 및 준비

여러 데이터 엔지니어와 시민 데이터 통합자는 클라우드 규모에서 데이터 세트를 대화형으로 탐색하고 준비할 수 있습니다. 데이터 레이크의 데이터 양, 다양성 및 속도가 증가함에 따라 사용자는 데이터 세트를 탐색하고 준비하는 효과적인 방법이 필요합니다. 예를 들어 '2017' 이후의 새 고객에 대한 모든 고객 인구 통계 정보를 포함하는 데이터 세트를 만들어야 할 수 있습니다. 알려진 대상에 매핑되지 않습니다. 레이크에 게시하기 전에 요구 사항을 충족하기 위해 데이터 세트를 탐색, 랭글링 및 준비하고 있습니다. 랭글링은 더 짧은 공식 분석 시나리오에 사용되는 경우가 많습니다. 준비 데이터 세트를 사용하여 변환 및 기계 학습 작업 다운스트림을 수행할 수 있습니다.

### <a name="code-free-agile-data-preparation"></a>코드가 필요 없는 민첩한 데이터 준비

시민 데이터 통합자는 데이터를 찾고 준비하는 시간을 60% 넘게 소비하며, 운영 생산성을 향상시키기 위해 코드 없는 방식으로 이를 수행하려고 합니다. 시민 데이터 통합자가 파워 쿼리 온라인과 같은 알려진 도구를 사용하여 확장 가능한 방식으로 데이터를 보강, 형성 및 게시할 수 있도록 허용하면 생산성이 크게 향상됩니다. Azure Data Factory의 랭글링을 사용하면 익숙한 파워 쿼리 온라인 매시업 편집기를 사용하여 시민 데이터 통합자가 오류를 신속하게 수정하고 데이터를 표준화하고 비즈니스 의사 결정을 지원하는 고품질의 데이터를 생산할 수 있습니다.

### <a name="data-validation-and-exploration"></a>데이터 유효성 검사 및 탐색

코드 없는 방식으로 데이터를 시각적으로 스캔하여 이상값, 변칙을 제거하고 빠른 분석을 위해 모양에 맞춥니다.

## <a name="supported-sources"></a>지원되는 원본

| 커넥터 | 데이터 형식 | 인증 유형 |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet, Excel | 계정 키, 서비스 주체, MSI |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV, Parquet, Excel | 서비스 사용자, MSI |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet, Excel | 계정 키, 서비스 주체, MSI |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 인증, MSI, 서비스 주체 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 인증, MSI, 서비스 주체 |

## <a name="the-mashup-editor"></a>매시업 편집기

파워 쿼리 작업을 만들면 모든 원본 데이터 세트가 데이터 세트 쿼리가 되고 **ADFResource** 폴더에 배치됩니다. 기본적으로 UserQuery는 첫 번째 데이터 세트 쿼리를 가리킵니다. 데이터 세트 쿼리에 대한 변경 내용이 지원되지 않거나 유지되지 않으므로 UserQuery에서 모든 변환을 수행해야 합니다. 현재는 쿼리 이름을 바꾸고 쿼리를 추가 및 삭제하는 것은 지원되지 않습니다.

:::image type="content" source="media/wrangling-data-flow/editor.png" alt-text="랭글링":::

현재 모든 파워 쿼리 M 함수가 제작 중에도 사용할 수 있는 데이터 랭글링을 지원하는 것은 아닙니다. 파워 쿼리 작업을 빌드하는 동안 함수가 지원되지 않으면 다음과 같은 오류 메시지가 표시됩니다.

`The Power Query Spark Runtime does not support the function`

지원되는 변환에 대한 자세한 내용은 [파워 쿼리 데이터 랭글링 함수](wrangling-functions.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[데이터 랭글링 파워 쿼리 매시업을 만드는 방법](wrangling-tutorial.md)에 대해 알아보세요.
