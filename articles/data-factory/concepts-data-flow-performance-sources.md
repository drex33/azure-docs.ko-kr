---
title: 매핑 데이터 흐름에서 원본 성능 최적화
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 데이터 흐름을 매핑하는 방법에 대 한 소스 성능 최적화에 대해 알아봅니다.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7fef72d1ca52ae04106797c8ce7088b259aad033
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294377"
---
# <a name="optimizing-sources"></a>원본 최적화

Azure SQL Database를 제외한 모든 원본은 **현재 분할 사용** 을 계속 유지하는 것이 좋습니다. 다른 모든 원본 시스템에서 데이터를 읽을 때 데이터 흐름은 데이터 크기에 따라 자동으로 데이터를 균등하게 분할합니다. 128MB 데이터마다 새 파티션이 만들어집니다. 데이터 크기가 증가하면 파티션 수도 증가합니다.

사용자 지정 분할은 Spark가 데이터를 읽은 *후에* 발생하며 데이터 흐름 성능에 부정적인 영향을 줍니다. 읽기 시에 데이터가 균등하게 분할되므로 권장하지 않습니다. 

> [!NOTE]
> 원본 시스템의 처리량에 따라 읽기 속도가 제한될 수 있습니다.

## <a name="azure-sql-database-sources"></a>Azure SQL Database 원본

Azure SQL Database에는 '원본' 분할이라는 고유한 분할 옵션이 있습니다. 원본 시스템에서 병렬 연결을 사용하도록 설정하면 원본 분할을 사용하여 Azure SQL DB에서 데이터를 읽는 시간을 향상할 수 있습니다. 파티션 수를 지정하고 데이터 분할 방법을 지정합니다. 카디널리티가 높은 파티션 열을 사용합니다. 원본 테이블의 파티션 구성표와 일치하는 쿼리를 입력할 수도 있습니다.

> [!TIP]
> 원본 분할의 경우 SQL Server I/O는 병목 상태입니다. 너무 많은 파티션을 추가하면 원본 데이터베이스가 포화될 수 있습니다. 일반적으로 이 옵션을 사용하는 경우 파티션 4개 또는 5개가 적절합니다.

:::image type="content" source="media/data-flow/sourcepart3.png" alt-text="원본 분할":::

### <a name="isolation-level"></a>격리 수준

Azure SQL 원본 시스템에서 읽기의 격리 수준은 성능에 영향을 줍니다. '커밋되지 않은 읽기'를 선택하면 가장 빠른 성능이 제공되고 데이터베이스 잠금이 방지됩니다. SQL 격리 수준에 대한 자세한 내용은 [격리 수준 이해](/sql/connect/jdbc/understanding-isolation-levels)를 참조하세요.

### <a name="read-using-query"></a>쿼리를 사용하여 읽기

테이블 또는 SQL 쿼리를 사용하여 Azure SQL Database에서 읽을 수 있습니다. SQL 쿼리를 실행하는 경우 쿼리를 완료한 후 변환을 시작해야 합니다. SQL 쿼리는 SELECT, WHERE 및 JOIN 문과 같이 더 빠르게 실행되고 SQL Server에서 읽는 데이터 양을 줄일 수 있는 작업을 푸시하는 데 유용합니다. 작업을 푸시하면 데이터가 데이터 흐름으로 들어오기 전에는 변환의 계보 및 성능을 추적하는 기능을 사용할 수 없습니다.

## <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics 원본

Azure Synapse Analytics를 사용하는 경우 원본 옵션에 **준비 사용** 이라는 설정이 있습니다. 이렇게 하면 서비스가 ```Staging```을 사용하여 Synapse에서 읽을 수 있으며, 이는 가장 성능이 좋은 대량 로드 기능을 위해 [Synapse COPY 문](/sql/t-sql/statements/copy-into-transact-sql) 명령을 사용하여 읽기 성능을 크게 향상시킵니다. ```Staging```을 사용하려면 데이터 흐름 활동 설정에서 Azure Blob Storage 또는 Azure Data Lake Storage gen2 준비 위치를 지정해야 합니다.

:::image type="content" source="media/data-flow/enable-staging.png" alt-text="준비 사용":::

## <a name="file-based-sources"></a>파일 기반 원본

데이터 흐름은 다양한 파일 형식을 지원하지만 읽기 및 쓰기 시간이 최적화되도록 Spark 네이티브 Parquet 형식을 사용하는 것이 좋습니다.

파일 세트에서 동일한 데이터 흐름을 실행하는 경우 와일드 카드 경로를 사용하여 폴더에서 읽거나 파일 목록에서 읽는 것이 좋습니다. 단일 데이터 흐름 활동 실행은 모든 파일을 일괄 처리로 처리할 수 있습니다. 이러한 설정을 구성하는 방법에 대한 자세한 내용은 [Azure Blob Storage 커넥터](connector-azure-blob-storage.md#source-transformation) 문서의 **원본 변환** 섹션에서 찾을 수 있습니다.

되도록이면 For-Each 활동을 사용하여 파일 세트에 대한 데이터 흐름을 실행하지 마세요. 이렇게 하면 for-each의 각 반복이 자체 Spark 클러스터를 스핀업하게 되는데, 필요 없는 경우가 많고 비용이 많이 발생할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [데이터 흐름 성능 개요](concepts-data-flow-performance.md)
- [싱크 최적화](concepts-data-flow-performance-sinks.md)
- [변환 최적화](concepts-data-flow-performance-transformations.md)
- [파이프라인에서 데이터 흐름 사용](concepts-data-flow-performance-pipelines.md)

성능과 관련된 다음과 같은 다른 데이터 흐름 문서를 참조하세요.

- [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
- [데이터 흐름 성능 모니터링](concepts-data-flow-monitoring.md)
- [Integration Runtime 성능](concepts-integration-runtime-performance.md)
