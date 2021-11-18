---
title: 이 Microsoft 센티널 통합 문서를 사용 하 여 데이터 커넥터의 상태 모니터링 | Microsoft Docs
description: 상태 모니터링 통합 문서를 사용하여 데이터 커넥터의 연결 및 성능을 추적합니다.
author: yelevin
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 3b627fa56723a4f447338c9f36b7d339d9109e98
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755985"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-microsoft-sentinel-workbook"></a>이 Microsoft 센티널 통합 문서를 사용 하 여 데이터 커넥터의 상태 모니터링

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

**데이터 커넥터 상태 모니터링 통합 문서** 를 사용 하면 Microsoft 센티널 내에서 데이터 커넥터의 상태, 연결 및 성능을 추적할 수 있습니다. 이 통합 문서는 추가 모니터를 제공하고, 변칙을 검색하고, 작업 영역의 데이터 수집 상태에 대한 인사이트를 제공합니다. 통합 문서의 논리를 사용하여 수집된 데이터의 일반적인 상태를 모니터링하고 사용자 지정 보기 및 규칙 기반 경고를 빌드할 수 있습니다.

## <a name="use-the-health-monitoring-workbook"></a>상태 모니터링 통합 문서 사용

1. Microsoft 센티널 포털의 **위협 관리** 메뉴에서 **통합 문서** 를 선택 합니다.

1. **통합 문서** 갤러리의 검색 표시줄에 *상태* 를 입력하고 결과 중에서 **데이터 컬렉션 상태 모니터링** 을 선택합니다.

1. **템플릿 보기** 를 선택하여 통합 문서를 있는 그대로 사용하거나, **저장** 을 선택하여 통합 문서의 편집 가능한 복사본을 만듭니다. 복사본을 만들 때 **저장된 통합 문서 보기** 를 선택합니다.

1. 통합 문서에서 먼저 보려는 **구독** 및 **작업 영역** 을 선택한 다음, **TimeRange** 를 정의하여 필요에 따라 데이터를 필터링합니다. **도움말 표시** 토글을 사용하여 통합 문서에 대한 현재 위치 설명을 표시합니다.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="데이터 커넥터 상태 모니터링 통합 문서 방문 페이지" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

이 통합 문서에는 다음과 같은 세 개의 탭 섹션이 있습니다.

1. **개요** 탭에는 선택한 작업 영역에 있는 데이터 수집의 일반적인 상태 (볼륨 측정, EPS 비율 및 마지막으로 받은 시간)가 표시 됩니다.

1. **데이터 수집 변칙** 탭에서는 테이블 및 데이터 원본별로 데이터 수집 프로세스의 변칙을 검색할 수 있습니다. 각 탭은 특정 테이블에 대한 변칙을 나타냅니다(**일반** 탭에는 테이블 컬렉션이 포함됨). 변칙은 **변칙 점수** 를 반환하는 **series_decompose_anomalies()** 함수를 사용하여 계산됩니다. [이 함수에 대해 자세히 알아보세요](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). 평가할 함수에 대해 다음 매개 변수를 설정합니다.

    - **AnomaliesTimeRange**: 이 시간 선택기는 데이터 컬렉션 변칙 보기에만 적용됩니다.
    - **SampleInterval**: 지정된 시간 범위 내에서 데이터가 샘플링되는 시간 간격입니다. 변칙 점수는 마지막 간격의 데이터에 대해서만 계산됩니다.
    - **PositiveAlertThreshold**: 이 값은 양의 변칙 점수 임계값을 정의합니다. 10진수 값을 허용합니다.
    - **NegativeAlertThreshold**: 이 값은 음의 변칙 점수 임계값을 정의합니다. 10진수 값을 허용합니다.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="데이터 커넥터 상태 모니터링 통합 문서 변칙 페이지" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. **에이전트 정보** 탭에는 Azure VM, 다른 클라우드 VM, 온-프레미스 VM 또는 실제 머신을 비롯한 다양한 머신에 설치된 Log Analytics 에이전트의 상태에 대한 정보가 표시됩니다. 다음을 모니터링할 수 있습니다.

   - 시스템 위치

   - 하트비트 상태 및 대기 시간

   - 사용 가능한 메모리 및 디스크 공간

   - 에이전트 작업

    이 섹션에서는 사용자의 머신 환경을 설명하는 탭을 선택해야 합니다. Azure Arc 관리형 머신만 보려면 **Azure 관리형 머신** 탭을 선택하고, Log Analytics 에이전트가 설치된 관리형 머신 및 비 Azure 머신을 모두 보려면 **모든 머신** 탭을 선택합니다.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="데이터 커넥터 상태 모니터링 통합 문서 에이전트 정보 페이지" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>다음 단계
데이터를 [Microsoft 센티널에](quickstart-onboard.md)등록 하 고, [데이터 원본을 연결](connect-data-sources.md)하 고, [데이터에 대 한 가시성과 잠재적 위협을](get-visibility.md)파악 하는 방법에 대해 알아봅니다.
