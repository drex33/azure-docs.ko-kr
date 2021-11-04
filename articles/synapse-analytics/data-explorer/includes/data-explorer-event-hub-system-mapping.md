---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e7e723394bd7a90f6fd4cda8db7c7cb27c4cb9a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482931"
---
> [!NOTE]
> * 시스템 속성은 `json` 및 테이블 형식(`csv`, `tsv` 등)에 대해 지원되며 압축된 데이터에서는 지원되지 않습니다. 지원되지 않는 형식을 사용하는 경우 데이터는 계속 수집되지만, 속성은 무시됩니다.
> * 테이블 형식 데이터의 경우 시스템 속성은 단일 레코드 이벤트 메시지에 대해서만 지원됩니다.
> * JSON 데이터의 경우 시스템 속성은 여러 레코드 이벤트 메시지에 대해서도 지원됩니다. 이러한 경우 시스템 속성은 이벤트 메시지의 첫 번째 레코드에만 추가됩니다. 
> * `csv` 매핑의 경우 속성은 [시스템 속성](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties) 테이블에 나열된 순서대로 레코드 시작 부분에 추가됩니다.
> * `json` 매핑의 경우 속성은 [시스템 속성](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties) 테이블의 속성 이름에 따라 추가됩니다.
