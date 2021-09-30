---
title: 매핑 데이터 흐름에서 싱크 성능 최적화
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인의 데이터 흐름 매핑에서 싱크 성능을 최적화하는 방법을 알아봅니다.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 54c03cc8b4c34be02d3dee608ce4a759e75f2200
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294381"
---
# <a name="optimizing-sinks"></a>싱크 최적화

데이터 흐름이 싱크에 기록될 때 사용자 지정 분할은 쓰기 직전에 발생합니다. 원본과 마찬가지로 대부분의 경우 **현재 분할 사용** 을 파티션 옵션으로 계속 유지하는 것이 좋습니다. 분할된 데이터는 대상이 분할되지 않은 경우에도 분할되지 않은 데이터보다 쓰기 속도가 훨씬 빠릅니다. 아래는 다양한 싱크 유형의 개별 고려 사항입니다. 

## <a name="azure-sql-database-sinks"></a>Azure SQL Database 싱크

Azure SQL Database를 사용하면 대부분의 경우 기본 분할이 작동합니다. SQL 데이터베이스에서 처리하기에 너무 많은 파티션이 싱크에 포함될 가능성이 있습니다. 이 경우 SQL Database 싱크에서 출력하는 파티션 수를 줄입니다.

### <a name="impact-of-error-row-handling-to-performance"></a>오류 행 처리가 성능에 미치는 영향

싱크 변환에서 오류 행 처리("오류 발생 시 계속")를 사용하도록 설정하면 서비스는 대상 테이블에 호환되는 행을 쓰기 전에 추가 단계를 수행합니다. 이 추가 단계를 수행하면 5% 범위에서 약간의 성능 저하가 발생할 수 있으며, 호환되지 않는 행을 로그 파일에 쓰도록 옵션을 설정한 경우에도 약간의 성능이 추가로 저하됩니다.

### <a name="disabling-indexes-using-a-sql-script"></a>SQL 스크립트를 사용하여 인덱스 비활성화

SQL 데이터베이스에 로드하기 전에 인덱스를 비활성화하면 테이블에 쓰기 성능이 크게 향상됩니다. SQL 싱크에 쓰기 전에 아래 명령을 실행합니다.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

쓰기가 완료되면 다음 명령을 사용하여 인덱스를 다시 작성합니다.

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

두 가지 작업 모두 매핑 데이터 흐름의 Azure SQL DB 또는 Synapse 싱크 내에서 SQL 사전 및 사후 SQL 스크립트를 사용하여 수행할 수 있습니다.

:::image type="content" source="media/data-flow/disable-indexes-sql.png" alt-text="인덱스 사용 안 함":::

> [!WARNING]
> 인덱스를 사용하지 않도록 설정하면 데이터 흐름이 데이터베이스를 효과적으로 제어하며 이 시점에는 쿼리가 성공할 가능성이 거의 없습니다. 결과적으로 이 충돌을 방지하기 위해 많은 ETL 작업이 야간에 트리거됩니다. 자세한 내용은 [SQL 인덱스를 사용하지 않도록 설정하는 제약 조건](/sql/relational-databases/indexes/disable-indexes-and-constraints)을 참조하세요.

### <a name="scaling-up-your-database"></a>데이터베이스 스케일 업

일단 DTU 한도에 도달하면, 파이프라인 실행 전에 원본 크기 조정을 예약하고 Azure SQL DB 및 DW를 싱크하여 처리량을 늘리고 Azure 제한을 최소화합니다. 파이프라인 실행이 완료되면 데이터베이스를 다시 일반 실행 속도로 조정합니다.

## <a name="azure-synapse-analytics-sinks"></a>Azure Synapse Analytics 싱크

Azure Synapse Analytics에 쓸 때 **준비 사용** 을 true로 설정해야 합니다. 이렇게 하면 서비스가 대량의 데이터를 효과적으로 로드하는 [SQL COPY 명령](/sql/t-sql/statements/copy-into-transact-sql)을 사용하여 쓸 수 있습니다. 준비를 사용하는 경우 데이터 준비를 위해 Azure Data Lake Storage gen2 또는 Azure Blob Storage 계정을 참조해야 합니다.

준비 외에도, Azure SQL Database와 동일한 모범 사례가 Azure Synapse Analytics에 적용됩니다.

## <a name="file-based-sinks"></a>파일 기반 싱크 

데이터 흐름은 다양한 파일 형식을 지원하지만 읽기 및 쓰기 시간이 최적화되도록 Spark 네이티브 Parquet 형식을 사용하는 것이 좋습니다.

데이터가 균등하게 분산되는 경우 파일 쓰기에 대한 가장 빠른 분할 옵션은 **현재 분할 사용** 입니다.

### <a name="file-name-options"></a>파일 이름 옵션

파일을 쓸 때 이름 지정 옵션을 선택할 수 있으며 각 옵션은 성능에 영향을 줍니다.

:::image type="content" source="media/data-flow/file-sink-settings.png" alt-text="싱크 옵션":::

**기본값** 옵션을 선택하면 가장 빠르게 씁니다. 각 파티션은 Spark 기본 이름을 사용하는 파일을 동일하게 취급합니다. 이는 데이터 폴더에서 읽기만 하는 경우에 유용합니다.

이름 지정 **패턴** 을 설정하면 각 파티션 파일의 이름이 사용자에게 친숙한 이름으로 바뀝니다. 이 작업은 쓰기 후에 수행되며 기본값을 선택할 때보다 약간 느립니다. 파티션마다 개별 파티션의 이름을 수동으로 지정할 수 있습니다.

열이 데이터를 출력하는 방법에 해당하는 경우 **열의 데이터로** 를 선택할 수 있습니다. 그러면 데이터가 섞이고 열이 균등하게 분산되지 않은 경우에는 성능에 영향을 줄 수 있습니다.

**단일 파일로 출력** 은 모든 데이터를 단일 파티션에 결합합니다. 이렇게 하면 데이터 세트의 크기가 클수록 쓰기 시간이 오래 걸립니다. 이 옵션은 이 옵션을 사용하는 것이 명시적인 비즈니스 이유가 없으면 사용하지 않는 것이 좋습니다.

## <a name="cosmosdb-sinks"></a>CosmosDB 싱크

CosmosDB에 쓸 때 데이터 흐름이 실행되는 동안 처리량과 일괄 처리 크기를 변경하면 성능이 향상될 수 있습니다. 이러한 변경 내용은 데이터 흐름 활동 실행 중에만 적용되며 완료 후에 원래 컬렉션 설정으로 돌아갑니다. 

**일괄 처리 크기:** 일반적으로 기본 일괄 처리 크기로 시작하는 것으로 충분합니다. 이 값을 추가로 튜닝하려면 데이터의 대략적인 개체 크기를 계산하고, 개체 크기 * 일괄 처리 크기가 2MB 미만인지 확인합니다. 미만이면 일괄 처리 크기를 늘려서 처리량을 높일 수 있습니다.

**처리량**: 여기서 더 높은 처리량을 설정하여 CosmosDB에 문서를 더 빨리 쓸 수 있습니다. 처리량을 높게 설정하면 더 많은 RU 비용이 발생합니다.

**쓰기 처리량 예산**: 분당 총 RU보다 작은 값을 사용합니다. 많은 수의 Spark 파티션이 포함된 데이터 흐름이 있는 경우 예산 처리량을 설정하여 모든 파티션에 부하를 분산할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [데이터 흐름 성능 개요](concepts-data-flow-performance.md)
- [원본 최적화](concepts-data-flow-performance-sources.md)
- [변환 최적화](concepts-data-flow-performance-transformations.md)
- [파이프라인에서 데이터 흐름 사용](concepts-data-flow-performance-pipelines.md)

성능과 관련된 다음과 같은 다른 데이터 흐름 문서를 참조하세요.

- [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
- [데이터 흐름 성능 모니터링](concepts-data-flow-monitoring.md)
- [Integration Runtime 성능](concepts-integration-runtime-performance.md)
