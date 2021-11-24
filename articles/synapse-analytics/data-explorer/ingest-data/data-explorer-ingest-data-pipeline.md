---
title: '빠른 시작: 파이프라인으로 데이터 수집 시작(미리 보기)'
description: 이 빠른 시작에서는 Azure Synapse Pipelines을 사용하여 Data Explorer 풀에 데이터를 수집하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: mode-other
ms.openlocfilehash: 55619e48d89bf5b8c5a6eb8ce2ac018c467c33c4
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063451"
---
# <a name="quickstart-ingest-data-using-azure-synapse-pipelines-preview"></a>빠른 시작: Azure Synapse Pipelines을 사용하여 데이터 수집(미리 보기)

이 빠른 시작에서는 데이터 원본에서 Azure Synapse Data Explorer 풀로 데이터를 로드하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- 테이블 만들기 [!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > 테이블이 성공적으로 만들어졌는지 확인합니다. 왼쪽 창에서 **데이터** 를 선택하고 *contosodataexplorer* 추가 메뉴를 선택한 다음, **새로 고침** 을 선택합니다. *contosodataexplorer* 아래에서 **테이블** 을 확장하고 *StormEvents* 테이블이 목록에 표시되는지 확인합니다.

- 쿼리 및 데이터 수집 엔드포인트를 가져옵니다. 연결된 서비스를 구성하려면 쿼리 엔드포인트가 필요합니다.
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

Azure Synapse Analytics에서 연결된 서비스는 다른 서비스에 대한 연결 정보를 정의합니다. 이 섹션에서는 Azure Data Explorer를 위한 연결된 서비스를 만듭니다.

1. Synapse Studio의 왼쪽 창에서 **관리** > **연결된 서비스** 를 선택합니다.
1. **&plus; 새로 만들기** 를 선택합니다.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-linked-service.png" alt-text="기존 서비스 목록을 표시하고 새로 추가 단추를 강조 표시한 연결된 서비스 화면의 스크린샷.":::

1. 갤러리에서 **Azure Data Explorer** 서비스를 선택한 다음, **계속** 을 선택합니다.

    :::image type="content" source="../media/ingest-data-pipeline/select-new-data-explorer-linked-service.png" alt-text="사용 가능한 서비스 목록을 표시하고 새 Azure Data Explorer 서비스 추가를 강조 표시한 새 연결된 서비스 창의 스크린샷.":::

1. 새 연결된 서비스 페이지에서 다음 정보를 사용합니다.

    | 설정 | 제안 값 | 설명 |
    |--|--|--|
    | Name | *contosodataexplorerlinkedservice* | Azure Data Explorer 연결된 서비스의 이름. |
    | 인증 방법 | *관리 ID* | 새 서비스에 대한 인증 방법. |
    | 계정 선택 방법 | *직접 입력* | 쿼리 엔드포인트를 지정하는 방법. |
    | 엔드포인트 | *https:\/\/contosodataexplorer.contosoanalytics.dev.kusto.windows.net* | [이전에 기록해둔](#prerequisites) 쿼리 엔드포인트. |
    | 데이터베이스 | *TestDatabase* | 데이터를 수집하려는 데이터베이스. |

    :::image type="content" source="../media/ingest-data-pipeline/create-new-data-explorer-linked-service.png" alt-text="새 서비스에 대해 완료해야 하는 필드를 보여주는 새 연결된 서비스 세부 정보 창의 스크린샷.":::

1. **연결 테스트** 를 선택하여 설정의 유효성을 검사한 다음, **만들기** 를 선택합니다.

## <a name="create-a-pipeline-to-ingest-data"></a>데이터를 수집하기 위한 파이프라인 만들기

파이프라인에는 일련의 활동을 실행하기 위한 논리적 흐름이 포함됩니다. 이 섹션에서는 기본 설정 원본의 데이터를 Data Explorer 풀로 수집하는 복사 작업을 포함하는 파이프라인을 만듭니다.

1. Synapse Studio의 왼쪽 창에서 **통합** 을 선택합니다.

1. **&plus;**  > **파이프라인** 을 선택합니다. 오른쪽 창에서 파이프라인 이름을 지정할 수 있습니다.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-pipeline.png" alt-text="새 파이프라인을 만들기 위한 선택 사항을 보여주는 스크린샷.":::

1. **작업** > **이동 및 변환** 에서 **데이터 복사** 를 파이프라인 캔버스로 끕니다.
1. 복사 작업을 선택하고 **원본** 탭으로 이동합니다. 데이터를 복사할 원본으로 새 원본 데이터 세트를 선택하거나 만듭니다.
1. **싱크** 탭으로 이동합니다. **새로 만들기** 를 선택하여 새 싱크 데이터 세트를 만듭니다.

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink.png" alt-text="새 싱크를 만들기 위한 선택 항목을 보여주는 파이프라인 복사 작업의 스크린샷.":::

1. 갤러리에서 **Azure Data Explorer** 데이터 세트를 선택한 다음, **계속** 을 선택합니다.
1. **속성 설정** 창에서 다음 정보를 사용한 다음, **확인** 을 선택합니다.

    | 설정 | 제안 값 | Description |
    |--|--|--|
    | Name | *AzureDataExplorerTable* | 새 파이프라인의 이름입니다. |
    | 연결된 서비스 | *contosodataexplorerlinkedservice* | 이전에 만든 연결된 서비스. |
    | 테이블 | *StormEvents* | 이전에 만든 테이블. |

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink-set-properties.png" alt-text="새 싱크에 대해 완료해야 하는 필드를 보여주는 파이프라인 복사 작업 세트 속성 창의 스크린샷.":::

1. 파이프라인의 유효성을 검사하려면 도구 모음에서 **유효성 검사** 를 선택합니다. 페이지의 오른쪽에 파이프라인 유효성 검사 출력의 결과가 표시됩니다.

## <a name="debug-and-publish-the-pipeline"></a>파이프라인 디버그 및 게시

파이프라인 구성을 완료한 후에는 아티팩트를 게시하기 전에 디버그 실행을 실행하여 모든 것이 올바른지 확인할 수 있습니다.

1. 도구 모음에서 **디버그** 를 선택합니다. 창의 아래쪽에 있는 **출력** 탭에서 파이프라인 실행 상태가 표시됩니다.

1. 파이프라인이 성공적으로 실행되면 위쪽 도구 모음에서 **모두 게시** 를 선택합니다. 이 작업은 사용자가 만든 엔터티(데이터 세트 및 파이프라인)를 Synapse Analytics 서비스에 게시합니다.
1. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림 메시지를 보려면 오른쪽 위에 있는 종 모양 단추를 선택합니다.

## <a name="trigger-and-monitor-the-pipeline"></a>파이프라인 트리거 및 모니터링

이 섹션에서는 이전 단계에서 게시한 파이프라인을 수동으로 트리거합니다.

1. 도구 모음에서 **트리거 추가** 를 선택한 다음, **지금 트리거** 를 선택합니다. **파이프라인 실행** 페이지에서 **확인** 을 선택합니다.

1. 왼쪽 사이드바에 있는 **모니터** 탭으로 이동합니다. 수동 트리거로 트리거되는 파이프라인 실행이 표시됩니다.
1. 파이프라인 실행이 성공적으로 완료되면 **파이프라인 이름** 열 아래의 링크를 선택하여 작업 실행 세부 정보를 보거나 파이프라인을 다시 실행합니다. 이 예제에서는 활동이 하나뿐이므로 목록에 하나의 항목만 표시됩니다.
1. 복사 작업에 대한 자세한 내용을 보려면 **작업 이름** 열 아래의 **세부 정보** 링크(안경 아이콘)를 선택합니다. 원본에서 싱크로 복사된 데이터 양, 데이터 처리량, 해당 기간의 실행 단계 및 사용된 구성과 같은 세부 정보를 모니터링할 수 있습니다.
1. 파이프라인 실행 보기로 다시 전환하려면 위쪽에서 **모든 파이프라인 실행** 링크를 선택합니다. **새로 고침** 을 선택하여 목록을 새로 고칩니다.
1. 데이터가 Data Explorer 풀에 올바르게 작성되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Data Explorer로 분석](../../get-started-analyze-data-explorer.md)
- [Data Explorer 풀 모니터링](../data-explorer-monitor-pools.md)
