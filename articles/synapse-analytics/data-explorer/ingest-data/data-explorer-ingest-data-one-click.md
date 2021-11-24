---
title: '빠른 시작: 원클릭으로 데이터 수집 시작(미리 보기)'
description: 이 빠른 시작에서는 원클릭을 사용하여 Data Explorer 풀에 데이터를 수집하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: mode-other
ms.openlocfilehash: 4b1f508d60c779f9e72c9695c6b438e8e9d9cb27
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133070887"
---
# <a name="quickstart-ingest-data-using-one-click-preview"></a>빠른 시작: 원클릭을 사용하여 데이터 수집(미리 보기)

원클릭 수집을 사용하면 데이터 수집 프로세스를 쉽고 빠르고 직관적으로 수행할 수 있습니다. 원클릭 수집은 신속하게 램프 업하여 데이터 수집을 시작하고, 데이터베이스 테이블을 만들고, 구조를 매핑하도록 도와줍니다. 일회성 또는 지속적인 수집 프로세스로 여러 소스의 데이터를 여러 데이터 형식으로 선택할 수 있습니다.

다음 기능을 사용하면 원클릭 수집을 유용하게 활용할 수 있습니다.

* 수집 마법사가 안내하는 직관적인 환경
* 몇 분 내에 데이터 수집
* 로컬 파일, BLOB, 컨테이너 등 여러 종류의 소스에서 데이터 수집(최대 10,000개 BLOB)
* 다양한 [형식](#file-formats)으로 데이터 수집
* 새 테이블 또는 기존 테이블에 데이터 수집
* 테이블 매핑 및 스키마는 자동으로 제안되며 쉽게 변경 가능
<!-- * Continue ingestion easily and quickly from a container with [Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion) -->

원클릭 수집은 데이터를 처음으로 수집하거나 데이터의 스키마가 익숙하지 않은 경우에 특히 유용합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- 테이블 만들기 [!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > 테이블이 성공적으로 만들어졌는지 확인합니다. 왼쪽 창에서 **데이터** 를 선택하고 *contosodataexplorer* 추가 메뉴를 선택한 다음, **새로 고침** 을 선택합니다. *contosodataexplorer* 아래에서 **테이블** 을 확장하고 *StormEvents* 테이블이 목록에 표시되는지 확인합니다.

## <a name="access-the-one-click-wizard"></a>원클릭 마법사 액세스

원클릭 수집 마법사는 원클릭 수집 프로세스를 안내합니다.

* Azure Synapse에서 마법사에 액세스하려면 다음을 수행합니다.

    1. Synapse Studio의 왼쪽 창에서 **데이터** 를 선택합니다.
    1. **Data Explorer 데이터베이스** 아래에서 관련 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음, **Azure Data Explorer에서 열기** 를 선택합니다.

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="특정 풀의 컨텍스트에서 Azure Data Explorer 열기를 보여 주는 Azure Synapse Studio의 스크린샷":::

    1. 관련 풀을 마우스 오른쪽 단추로 클릭한 다음, **새 데이터 수집** 을 선택합니다.

* Azure Portal에서 마법사에 액세스하려면 다음을 수행합니다.

    1. Azure Portal에서 관련 Synapse 작업 영역을 검색하여 선택합니다.
    1. **Data Explorer 풀** 아래에서 관련 풀을 선택합니다.
    1. **Data Explorer 풀 시작** 홈 화면에서 **새 데이터 수집** 을 선택합니다.

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-portal.png" alt-text="특정 풀의 컨텍스트에서 Azure Data Explorer 열기를 보여 주는 Azure Portal의 스크린샷":::


* Azure Data Explorer 웹 ui에서 마법사에 액세스하려면 다음을 수행합니다.

    1. 시작하기 전에 다음 단계를 사용하여 쿼리 및 데이터 수집 엔드포인트를 가져옵니다.
        [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]
    1. Azure Data Explorer 웹 ui에서 *쿼리 엔드포인트* 에 대한 연결을 추가합니다.
    1. 왼쪽 메뉴에서 **쿼리** 를 선택하고 **데이터베이스** 또는 **테이블** 을 마우스 오른쪽 단추로 클릭한 다음, **새 데이터 수집** 을 선택합니다.

## <a name="one-click-ingestion-wizard"></a>원클릭 수집 마법사

> [!NOTE]
> 이 섹션에서는 이벤트 허브를 데이터 원본으로 사용하는 마법사에 대해 설명합니다. 이러한 단계를 사용하여 Blob, 파일, Blob 컨테이너 및 ADLS Gen2 컨테이너에서 데이터를 수집할 수도 있습니다.
>

1. **대상** 탭에서 수집된 데이터의 데이터베이스 및 테이블을 선택합니다.

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="데이터베이스 및 테이블 선택을 보여 주는 Azure Data Explorer 원클릭 수집 마법사의 스크린샷":::

1. **원본** 탭에서:
    1. *이벤트 허브* 를 **원본 유형** 으로 선택하여 수집합니다.

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-source-type.png" alt-text="원본 유형 선택을 보여 주는 Azure Data Explorer 원클릭 수집 마법사의 스크린샷":::

    1. 다음 정보를 사용하여 이벤트 허브 데이터 연결 세부 정보를 입력합니다.

        | 설정 | 제안 값 | Description |
        |--|--|--|
        | 데이터 연결 이름 | *ContosoDataConnection* | 이벤트 허브 데이터 연결의 이름 |
        | 구독 | *Contoso_Synapse* | 이벤트 허브가 있는 구독 |
        | 이벤트 허브 네임스페이스 | *contosoeventhubnamespace* | 이벤트 허브의 네임스페이스 |
        | 소비자 그룹 | *contosoconsumergroup* | 이벤트 허브 소비자 그룹의 이름 |

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="이벤트 허브 연결 세부 정보를 보여 주는 Azure Data Explorer 원클릭 수집 마법사의 스크린샷":::

    1. **다음** 을 선택합니다.

### <a name="schema-mapping"></a>스키마 매핑

서비스에서 스키마 및 수집 속성을 자동으로 생성하며, 사용자가 변경할 수 있습니다. 사용자는 새 테이블과 기존 테이블 중 어디에 수집할 것인지에 따라 기존 매핑 구조를 사용할 수도 있고 새로 만들 수도 있습니다.

**스키마** 탭에서 다음 작업을 수행합니다.
   1. 자동 생성된 압축 형식을 확인합니다.
   1. [데이터 형식](#file-formats)을 선택합니다. 여러 형식을 사용하면 추가 변경 작업을 수행할 수 있습니다.
   1. [편집기 창](#editor-window)에서 매핑을 변경합니다.

#### <a name="file-formats"></a>파일 형식

원클릭 수집은 [수집을 위해 Data Explorer에서 지원하는 모든 데이터 형식](data-explorer-ingest-data-supported-formats.md)의 소스 데이터에서 수집을 지원합니다.

### <a name="editor-window"></a>편집기 창

**스키마** 탭의 **편집기** 창에서 데이터 테이블 열을 필요한 대로 조정할 수 있습니다.

테이블에서 변경할 수 있는 사항은 다음 매개 변수에 따라 다릅니다.

* **테이블** 유형은 신규 또는 기존입니다.
* **매핑** 유형은 신규 또는 기존입니다.

테이블 유형 | 매핑 유형 | 사용 가능한 조정|
|---|---|---|
|새 테이블   | 새 매핑 |데이터 형식 변경, 열 이름 변경, 새 열, 열 삭제, 열 업데이트, 오름차순 정렬, 내림차순 정렬  |
|기존 테이블  | 새 매핑 | 새 열(데이터 형식 변경, 이름 바꾸기 및 업데이트 가능)<br> 열 업데이트, 오름차순 정렬, 내림차순 정렬  |
| | 기존 매핑 | 오름차순 정렬, 내림차순 정렬

> [!NOTE]
> 새 열을 추가하거나 열을 업데이트하면 매핑 변환을 변경할 수 있습니다. 자세한 내용은 [매핑 변환](#mapping-transformations)을 참조하세요.

<!-- >[!NOTE]
> At any time, you can open the [command editor](one-click-ingestion-new-table.md#command-editor) above the **Editor** pane. In the command editor, you can view and copy the automatic commands generated from your inputs. -->

#### <a name="mapping-transformations"></a>매핑 변환

일부 데이터 형식 매핑(Parquet, JSON 및 Avro)은 간단한 수집 시간 변환을 지원합니다. 매핑 변환을 적용하려면 [편집기 창](#editor-window)에서 열을 만들거나 업데이트합니다.

매핑 변환은 **형식** 문자열 또는 날짜/시간의 열에서 수행할 수 있으며 **원본** 의 데이터 형식이 int 또는 long입니다. 지원되는 매핑 변환은 다음과 같습니다.

* DateTimeFromUnixSeconds
* DateTimeFromUnixMilliseconds
* DateTimeFromUnixMicroseconds
* DateTimeFromUnixNanoseconds

### <a name="data-ingestion"></a>데이터 수집

스키마 매핑과 열 조작을 완료하면 수집 마법사가 데이터 수집 프로세스를 시작합니다.

* **컨테이너가 이닌** 원본에서 데이터를 수집하는 경우 수집이 즉시 적용됩니다.

* 데이터 원본이 **컨테이너** 인 경우:
    * Data Explorer의 [일괄 처리 정책](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context)은 데이터를 집계합니다.
    * 수집 후에는 수집 보고서를 다운로드하여 해결된 각 BLOB의 성능을 검토할 수 있습니다.
    <!-- * You can select **Create continuous ingestion** and set up [continuous ingestion using Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion). -->

### <a name="initial-data-exploration"></a>초기 데이터 탐색

수집 후에는 마법사에서 데이터의 초기 탐색을 위한 **빠른 명령** 옵션을 제공합니다.

## <a name="next-steps"></a>다음 단계

- [Data Explorer로 분석](../../get-started-analyze-data-explorer.md)
- [Data Explorer 풀 모니터링](../data-explorer-monitor-pools.md)
