---
title: 원클릭 수집을 사용하여 Event Hub에서 Azure Synapse 데이터 탐색기(미리 보기)로 데이터 수집
description: 원클릭을 사용하여 Event Hub에서 Azure Synapse 데이터 탐색기 데이터를 수집(로드)하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 6bfb614f3f0263b6ceff3fbf1e00a6c90215c6a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720210"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-synapse-data-explorer-preview"></a>원클릭 수집을 사용하여 Azure Synapse 데이터 탐색기 대한 이벤트 허브 데이터 연결 만들기(미리 보기)

> [!div class="op_single_selector"]
> * [포털](data-explorer-ingest-event-hub-portal.md)
> * [한 번 클릭](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-explorer-ingest-event-hub-resource-manager.md)

Azure Synapse 데이터 탐색기 빅 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스인 Event Hubs 수집(데이터 로드)을 제공합니다. [Event Hubs](../../../event-hubs/event-hubs-about.md)에서는 초당 수백만 개의 이벤트를 거의 실시간으로 처리할 수 있습니다. 이 문서에서는 [원클릭 검색](data-explorer-ingest-data-one-click.md) 환경을 사용하여 Azure Synapse 데이터 탐색기 테이블에 이벤트 허브를 연결합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [수집할 데이터가 있는 Event Hub](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).

> [!NOTE]
> Synapse 작업 영역에서 데이터 반출 보호가 사용된 관리형 가상 네트워크를 사용하는 경우 Event Hub에서 데이터 탐색기 풀로의 데이터 수집이 작동하지 않습니다.

## <a name="ingest-new-data"></a>새 데이터 수집

1. Synapse Studio의 왼쪽 창에서 **데이터** 를 선택합니다.

1. **Data Explorer 데이터베이스** 아래에서 관련 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음, **Azure Data Explorer에서 열기** 를 선택합니다.

    :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="특정 풀의 컨텍스트에서 Azure Data Explorer 열기를 보여 주는 Azure Synapse Studio의 스크린샷":::

1. 웹 UI의 왼쪽 메뉴에서 **데이터** 탭을 선택합니다. 

    :::image type="content" source="../media/ingest-data-event-hub/one-click-ingestion-event-hub.png" alt-text="웹 UI의 Event Hub에서 원클릭 데이터 수집을 선택합니다.":::

1. **Event Hub에서 데이터 수집** 카드에서 **수집** 을 선택합니다. 

**대상** 탭이 선택된 상태로 **새 데이터 수집** 창이 열립니다.

### <a name="destination-tab"></a>대상 탭

:::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="대상 탭의 스크린샷. 다음 원본으로 진행하기 전에 클러스터, 데이터베이스 및 테이블 필드를 입력해야 합니다.":::

1. **클러스터** 및 **데이터베이스** 필드는 자동으로 채워집니다. 드롭다운 메뉴에서 다른 클러스터 또는 데이터베이스를 선택할 수 있습니다.

1. **테이블** 에서 **새 테이블 만들기** 를 선택하고 새 테이블의 이름을 입력합니다. 또는 기존 테이블을 사용합니다. 

    > [!NOTE]
    > 테이블은 1~1024자여야 합니다. 영숫자, 하이픈 및 밑줄을 사용할 수 있습니다. 특수 문자는 지원되지 않습니다.

1. **다음: 원본** 을 선택합니다.

### <a name="source-tab"></a>원본 탭

1. **원본 유형** 에서 **Event Hub** 를 선택합니다. 

1. **데이터 연결** 에서 다음 필드를 입력합니다.

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="입력할 프로젝트 세부 정보 필드가 있는 원본 탭의 스크린샷 - 한 번의 클릭으로 Event Hub로 Azure Synapse 데이터 탐색기 새 데이터 수집":::

    |**설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 데이터 연결 이름 | *ContosoDataConnection*  | 데이터 연결을 식별하는 이름입니다.
    | 구독 |      | Event Hub 리소스가 있는 구독 ID입니다.  |
    | 이벤트 허브 네임스페이스 |  | 네임스페이스를 식별하는 이름입니다. |
    | 이벤트 허브 |  | 사용하려는 Event Hub입니다. |
    | 소비자 그룹 |  | Event Hub에 정의된 소비자 그룹입니다. |
    | 이벤트 시스템 속성 | 관련 속성 선택 | [Event Hub 시스템 속성](../../../service-bus-messaging/service-bus-amqp-protocol-guide.md#message-annotations). 이벤트 메시지마다 여러 레코드가 있는 경우 시스템 속성이 첫 번째 속성에 추가됩니다. 시스템 속성을 추가할 때 선택한 속성을 포함하도록 테이블 스키마를 [생성](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context)하거나 [업데이트](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context)하고 [매핑](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)합니다. |

1. **다음: 스키마** 를 선택합니다.

## <a name="schema-tab"></a>스키마 탭

데이터는 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 개체 형식으로 Event Hub에서 읽습니다. 지원되는 형식은 CSV, JSON, PSV, SCsv, SOHsv TSV, TXT 및 TSVE입니다.

<!-- For information on schema mapping with JSON-formatted data, see [Edit the schema](one-click-ingestion-existing-table.md#edit-the-schema).
For information on schema mapping with CSV-formatted data, see [Edit the schema](one-click-ingestion-new-table.md#edit-the-schema). -->

:::image type="content" source="../media/ingest-data-event-hub/schema-tab.png" alt-text="한 번의 클릭으로 Event Hub로 Azure Synapse 데이터 탐색기 새 데이터를 수집한 스키마 탭의 스크린샷":::

1. 미리 보기 창에 표시되는 데이터가 완전하지 않은 경우 필요한 모든 데이터 필드가 있는 테이블을 만들기 위해 더 많은 데이터가 필요할 수 있습니다. 다음 명령을 사용하여 Event Hub에서 새 데이터를 가져옵니다.
    * **새 데이터 삭제 및 가져오기**: 제공된 데이터를 삭제하고 새 이벤트를 검색합니다.
    * **추가 데이터 가져오기**: 이미 찾은 이벤트 외에 더 많은 이벤트를 검색합니다. 
    
    > [!NOTE]
    > 데이터 미리 보기를 보려면 Event Hub에서 이벤트를 전송해야 합니다.
        
1. 완료되면 **다음: 요약** 을 선택합니다.

## <a name="continuous-ingestion-from-event-hub"></a>Event Hub에서 연속적으로 수집

**Event Hub에서 연속 수집 설정됨** 창에서 설정이 성공적으로 완료되면 모든 단계가 녹색 확인 표시로 표시됩니다. 이 단계 아래에 있는 카드는 **빠른 쿼리** 를 사용하여 데이터를 탐색하거나, **도구** 를 사용하여 변경 사항을 실행 취소하거나, Event Hub 연결 및 데이터를 **모니터링** 할 수 있는 옵션을 제공합니다.

:::image type="content" source="../media/ingest-data-event-hub/data-ingestion-completed.png" alt-text="원클릭 환경으로 Event Hub에서 Azure Synapse 데이터 탐색기 위한 마지막 검색 화면의 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Data Explorer로 분석](../../get-started-analyze-data-explorer.md)
- [Data Explorer 풀 모니터링](../data-explorer-monitor-pools.md)