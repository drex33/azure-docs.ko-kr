---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 73cd54f16e29564fda5f9e41d0469d59ea99526d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482882"
---
|**속성** | **속성 설명**|
|---|---|
| `rawSizeBytes` | 원시(압축되지 않은) 데이터의 크기입니다. Avro/ORC/Parquet의 경우 형식별 압축이 적용되기 전의 크기입니다. 이 속성을 압축되지 않은 데이터 크기(바이트)로 설정하여 원래 데이터 크기를 제공합니다.|
| `kustoTable` |  기존 대상 테이블의 이름입니다. `Data Connection` 블레이드에 설정된 `Table`을 재정의합니다. |
| `kustoDataFormat` |  데이터 형식 `Data Connection` 블레이드에 설정된 `Data format`을 재정의합니다. |
| `kustoIngestionMappingReference` |  사용할 기존 수집 매핑의 이름입니다. `Data Connection` 블레이드에 설정된 `Column mapping`을 재정의합니다.|
| `kustoIgnoreFirstRecord` | `true`로 설정하면 Kusto는 Blob의 첫 번째 행을 무시합니다. 테이블 형식 데이터(CSV, TSV 등)에서 헤더를 무시하는 데 사용합니다. |
| `kustoExtentTags` | 결과 익스텐트에 연결할 [태그](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context)를 나타내는 문자열입니다. |
| `kustoCreationTime` |  ISO 8601 문자열로 서식이 지정된 Blob의 [$IngestionTime](/azure/data-explorer/kusto/query/ingestiontimefunction?context=/azure/synapse-analytics/context/context?pivots=azuredataexplorer)을 재정의합니다. 백필에 사용합니다. |
