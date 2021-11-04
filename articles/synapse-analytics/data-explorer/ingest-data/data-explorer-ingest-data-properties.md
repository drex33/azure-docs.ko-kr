---
title: Azure Synapse 데이터 탐색기 데이터 수집 속성(미리 보기)
description: Azure Synapse 데이터 탐색기 지원하는 다양한 데이터 수집 속성에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e598f3fdd03721def28ac8a0f90e3bd12958783c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482903"
---
# <a name="azure-synapse-data-explorer-data-ingestion-properties-preview"></a>데이터 수집 속성 Azure Synapse 데이터 탐색기(미리 보기)

데이터 수집은 데이터가 테이블에 추가되고 데이터 탐색기 쿼리에 사용할 수 있게 되는 프로세스입니다. 수집 명령의 `with` 키워드 뒤에 속성을 추가할 수 있습니다.

## <a name="ingestion-properties"></a>수집 속성

다음 표에서는 데이터 탐색기 지원하는 속성을 나열하고 설명하며 예제를 제공합니다. 

|속성              |Description                                              |예                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |원본 파일의 데이터를 테이블의 실제 열에 매핑하는 방법을 나타내는 문자열 값입니다. 관련 매핑 유형을 사용하여 `format` 값을 정의합니다. [데이터 매핑](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)을 참조하세요.|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(사용되지 않음: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|명명된 매핑 정책 개체를 사용하여 원본 파일에서 테이블의 실제 열로 데이터를 매핑하는 방법을 나타내는 문자열 값입니다. 관련 매핑 유형을 사용하여 `format` 값을 정의합니다. [데이터 매핑](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)을 참조하세요.|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(사용되지 않음: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |수집되는 데이터 범위를 만드는 시간에 사용할 날짜/시간 값(ISO8601 문자열 형식)입니다. 지정하지 않으면 현재 값(`now()`)이 사용됩니다. 보존 정책이 올바르게 적용되도록 이전 데이터를 수집할 때 기본값을 재정의하는 것이 유용합니다. 지정된 경우 대상 테이블의 유효한 [익스텐트 병합 정책](/azure/data-explorer/kusto/management/mergepolicy?context=/azure/synapse-analytics/context/context)의 `Lookback` 속성이 지정된 값과 일치하는지 확인합니다.|`with (creationTime="2017-02-13")`|
|`extend_schema`|지정하면 테이블의 스키마를 확장하도록 명령에 지시하는 부울 값(기본값: `false`)입니다. 이 옵션은 `.append` 및 `.set-or-append` 명령에만 적용됩니다. 유일하게 허용되는 스키마 확장에는 테이블의 끝에 추가되는 추가 열이 있습니다.|원래 테이블 스키마가 `(a:string, b:int)`인 경우 유효한 스키마 확장은 `(a:string, b:int, c:datetime, d:string)`이지만 `(a:string, c:datetime)`은 유효하지 않습니다.|
|`folder` |[ingest-from-query](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context) 명령의 경우 테이블에 할당할 폴더입니다. 테이블이 이미 있으면 이 속성은 테이블의 폴더를 재정의합니다.|`with (folder="Tables/Temporary")`|
|`format` |데이터 형식입니다([지원되는 데이터 형식](data-explorer-ingest-data-supported-formats.md) 참조).|`with (format="csv")`|
|`ingestIfNotExists`|지정하면 동일한 값을 가진 `ingest-by:` 태그로 태그가 지정된 데이터가 테이블에 이미 있는 경우 수집하지 못하게 하는 문자열 값입니다. 이렇게 하면 idempotent(멱등원) 데이터 수집이 보장됩니다. 자세한 내용은 [ingest-by: 태그](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#ingest-by-extent-tags)를 참조하세요.|`with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` 속성은 태그가 `ingest-by:Part0001`인 데이터가 이미 있는 경우 현재 수집을 완료하지 않음을 나타냅니다. 아직 없는 경우 이 새 수집에는 이 태그가 설정되어 있어야 합니다(이후 수집에서 동일한 데이터를 다시 수집하려고 하는 경우).|
|`ignoreFirstRecord` |`true`로 설정하면 수집에서 모든 파일의 첫 번째 레코드를 무시해야 함을 나타내는 부울 값입니다. 이 속성은 파일의 첫 번째 레코드가 열 이름인 경우 `CSV`와 유사한 형식의 파일에 유용합니다. 기본적으로 `false`로 가정됩니다.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |지정하면 [.show operation details](/azure/data-explorer/kusto/management/operations?context=/azure/synapse-analytics/context/context#show-operation-details) 명령에서 자세한 결과를 검색할 수 있도록 명령이 해당 결과를 유지해야 함(성공한 경우에도)을 나타내는 부울 값입니다. 기본값은 `false`입니다.|`with (persistDetails=true)`|
|`policy_ingestiontime`|지정하면 이 명령으로 만든 테이블에서 [수집 시간 정책](/azure/data-explorer/kusto/management/ingestiontimepolicy?context=/azure/synapse-analytics/context/context)을 사용하도록 설정할지 여부를 설명하는 부울 값입니다. 기본값은 `true`입니다.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |지정하면 명령에서 테이블의 스키마를 다시 만들 수 있는지 여부를 설명하는 부울 값입니다. 이 속성은 `.set-or-replace` 명령에만 적용됩니다. 이 속성과 `extend_schema` 속성이 모두 설정되면 이 속성이 우선 적용됩니다.|`with (recreate_schema=true)`|
|`tags`|수집된 데이터와 연결할 JSON 문자열 형식의 [태그](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging) 목록입니다. |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|수집 중에 실행할 유효성 검사를 나타내는 JSON 문자열입니다. 다양한 옵션에 관한 설명은 [데이터 수집](data-explorer-ingest-data-overview.md)을 참조하세요.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(실제로 기본 정책임).|
|`zipPattern`|ZIP 보관 파일이 있는 스토리지에서 데이터를 수집하는 경우 이 속성을 사용합니다. 이는 수집할 ZIP 보관 파일의 파일을 선택할 때 사용할 정규식을 나타내는 문자열 값입니다.  보관 파일의 다른 모든 파일은 무시됩니다.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>다음 단계

- [데이터 수집](data-explorer-ingest-data-overview.md)에 관해 자세히 알아봅니다.
- [지원되는 데이터 형식](data-explorer-ingest-data-supported-formats.md)에 관해 자세히 알아봅니다.
