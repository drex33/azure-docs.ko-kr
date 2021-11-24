---
title: '빠른 시작: Data Explorer 풀을 사용하여 분석 시작(미리 보기)'
description: 이 빠른 시작에서는 Data Explorer를 사용하여 데이터를 분석하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 09/30/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: mode-other
ms.openlocfilehash: 0affe0dbe3d6de3586c7b2fe075ed7cfded72df5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133038089"
---
# <a name="quickstart-analyze-with-data-explorer-preview"></a>빠른 시작: Data Explorer를 사용하여 분석(미리 보기)

이 문서에서는 Azure Synapse용 Data Explorer를 사용하여 데이터를 로드하고 분석하는 기본 단계에 대해 알아봅니다.

## <a name="create-a-data-explorer-pool"></a>Data Explorer 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **Data Explorer 풀** 을 차례로 선택합니다.
1. **새로 만들기** 를 선택한 다음, **기본** 탭에 다음 세부 정보를 입력합니다.

    | 설정 | 제안 값 | 설명 |
    |--|--|--|
    | Data Explorer 풀 이름 | contosodataexplorer | Data Explorer 풀의 이름입니다. |
    | 작업 | 컴퓨팅 최적화 | 이 워크로드는 더 높은 CPU 대 SSD 스토리지 비율을 제공합니다. |
    | 노드 크기 | 작음(코어 4개) | 이 빠른 시작에서는 비용을 줄이기 위해 이 값을 가장 작은 크기로 설정합니다. |

    > [!IMPORTANT]
    > Data Explorer 풀이 사용할 수 있는 이름과 관련된 제한 사항이 있습니다. 이름은 소문자 및 숫자만 포함해야 하고 4~15자여야 하며 문자로 시작해야 합니다.

1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. Data Explorer 풀이 프로비저닝 프로세스를 시작합니다.

## <a name="create-a-data-explorer-database"></a>Data Explorer 데이터베이스 만들기

1. Synapse Studio의 왼쪽 창에서 **데이터** 를 선택합니다.
1. **&plus;** (새 리소스 추가) > **Data Explorer 풀** 을 선택하고 다음 정보를 붙여넣습니다.

    | 설정 | 제안 값 | Description |
    |--|--|--|
    | 풀 이름 | *contosodataexplorer* | 사용할 Data Explorer 풀의 이름 |
    | Name | *TestDatabase* | 데이터베이스 이름은 클러스터 내에서 고유해야 합니다. |
    | 기본 보존 기간 | *365* | 데이터를 쿼리에 사용할 수 있도록 보장되는 시간 범위(일)입니다. 시간 범위는 데이터가 수집된 시간부터 측정됩니다. |
    | 기본 캐시 기간 | *31* | 자주 쿼리되는 데이터를 장기 스토리지가 아닌 SSD 스토리지 또는 RAM에 보관할 수 있는 시간 범위(일)입니다. |

1. **만들기** 를 선택하여 데이터베이스를 만듭니다. 만들기에는 일반적으로 채 1분이 소요되지 않습니다.

## <a name="ingest-sample-data-and-analyze-with-a-simple-query"></a>샘플 데이터 수집 및 간단한 쿼리를 사용하여 분석

1. Synapse Studio의 왼쪽 창에서 **개발** 을 선택합니다.
1. **KQL 스크립트** 에서 **&plus;** (새 리소스 추가) > **KQL 스크립트** 를 선택합니다. 오른쪽 창에서 스크립트 이름을 지정할 수 있습니다.
1. **연결 대상** 메뉴에서 *contosodataexplorer* 를 선택합니다.
1. **데이터베이스 사용** 메뉴에서 *TestDatabase* 를 선택합니다.
1. 다음 명령을 붙여넣고 **실행** 을 선택하여 StormEvents 테이블을 만듭니다.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > 테이블이 성공적으로 만들어졌는지 확인합니다. 왼쪽 창에서 **데이터** 를 선택하고 *contosodataexplorer* 추가 메뉴를 선택한 다음, **새로 고침** 을 선택합니다. *contosodataexplorer* 아래에서 **테이블** 을 확장하고 *StormEvents* 테이블이 목록에 표시되는지 확인합니다.

1. 다음 명령을 붙여넣고 **실행** 을 선택하여 StormEvents 테이블에 데이터를 수집합니다.

    ```Kusto
    .ingest into table StormEvents 'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?sv=2019-12-12&ss=b&srt=o&sp=r&se=2022-09-05T02:23:52Z&st=2020-09-04T18:23:52Z&spr=https&sig=VrOfQMT1gUrHltJ8uhjYcCequEcfhjyyMX%2FSc3xsCy4%3D' with (ignoreFirstRecord=true)
    ```

1. 수집이 완료되면 다음 쿼리를 붙여넣고 창에서 쿼리를 선택한 다음 **실행** 을 선택합니다.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

    쿼리가 수집된 샘플 데이터에서 다음 결과를 반환합니다.

    :::image type="content" source="data-explorer/media/get-started-analyze-data-explorer/sample-query-results.png" alt-text="샘플 데이터에서 쿼리 실행 결과":::

## <a name="next-steps"></a>다음 단계

- [자습서: KQL 쿼리 사용](/azure/data-explorer/kusto/query/tutorial?context=/azure/synapse-analytics/context/context&pivots=synapse)
- [Data Explorer 풀 모니터링](data-explorer/data-explorer-monitor-pools.md)
