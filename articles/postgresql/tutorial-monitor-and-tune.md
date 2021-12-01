---
title: '자습서: 모니터링 및 튜닝 - Azure Database for PostgreSQL - 단일 서버'
description: 이 자습서는 Azure Database for PostgreSQL의 모니터링 및 튜닝 작업을 안내합니다. - Single Server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: c54facdbf61185f6a95732e280298261d30ee5bb
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133406490"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>자습서: Azure Database for PostgreSQL 모니터링 및 튜닝 - 단일 서버

Azure Database for PostgreSQL에는 서버 성능을 이해하고 개선하는 데 도움이 되는 기능이 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 쿼리 및 대기 통계 수집 사용
> * 수집된 데이터 액세스 및 사용
> * 쿼리 성능 보기 및 시간에 따른 대기 통계
> * 데이터베이스를 분석하여 성능 권장 사항 가져오기
> * 성능 권장 사항 적용

## <a name="prerequisites"></a>필수 구성 요소
PostgreSQL 버전 9.6 또는 10을 포함하는 Azure Database for PostgreSQL 서버가 필요합니다. [만들기 자습서](tutorial-design-database-using-azure-portal.md)의 단계에 따라 서버를 만들 수 있습니다.

> [!IMPORTANT]
> **쿼리 저장소**, **Query Performance Insight** 및 **성능 권장 사항** 은 공용 미리 보기로 제공됩니다.

## <a name="enabling-data-collection"></a>데이터 수집 사용
[쿼리 저장소](concepts-query-store.md)는 쿼리의 기록을 캡처하고 서버의 통계를 기다렸다가 서버의 **azure_sys** 데이터베이스에 저장합니다. 이 기능은 옵트인(opt-in) 기능입니다. 포커스 표시를 사용하도록 설정하려면

1. Azure Portal을 엽니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. 왼쪽 메뉴의 **설정** 섹션에 있는 **서버 매개 변수** 를 선택합니다.

4. 쿼리 성능 데이터 수집을 시작하려면 **pg_qs.query_capture_mode** 를 **TOP** 로 설정합니다. 대기 통계 수집을 시작하려면 **pgms_wait_sampling.query_capture_mode** 를 **ALL** 로 설정합니다. 저장합니다.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="쿼리 저장소 서버 매개 변수":::

5. 데이터의 첫 번째 배치가 **azure_sys** 데이터베이스에서 지속되는 데 최대 20분이 걸립니다.


## <a name="performance-insights"></a>성능 인사이트
Azure Portal의 [Query Performance Insight](concepts-query-performance-insight.md) 보기에는 쿼리 저장소의 핵심 정보가 시각화되어 표시됩니다. 

1. Azure Database for PostgreSQL 서버의 포털 페이지에서 왼쪽 메뉴의 **지원 + 문제 해결** 섹션에서 **Query Performance Insight** 를 선택합니다.

2. **장기 실행 쿼리** 탭에는 실행당 평균 기간별 상위 5개 쿼리가 15분 간격으로 집계되어 표시됩니다. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="Query Performance Insight 방문 페이지":::

   **쿼리 수** 드롭다운에서 선택하여 더 많은 쿼리를 볼 수 있습니다. 이 작업을 수행하면 특정 쿼리 ID에 대해 차트 색이 변경될 수 있습니다.

3. 차트를 클릭하고 끌어 특정 기간으로 범위를 좁힐 수 있습니다.

4. 확대/축소 아이콘을 사용하여 더 긴 기간이나 더 짧은 기간을 표시합니다.

5. 해당 기간의 장기 실행 쿼리에 대한 자세한 내용을 보려면 차트 아래의 표를 참조하세요.

6. 서버의 대기 쿼리를 시각화하려면 **대기 통계** 탭을 선택합니다.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="Query Performance Insight 대기 통계":::


## <a name="performance-recommendations"></a>성능 권장 사항
[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

1. PostgreSQL 서버에 대한 Azure Portal 페이지의 메뉴 표시줄에 있는 **지원 + 문제 해결** 섹션에서 **성능 권장 사항** 을 엽니다.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="성능 권장 사항 방문 페이지":::

2. **분석** 을 선택하고 데이터베이스를 선택합니다. 그러면 분석이 시작됩니다.

3. 워크로드에 따라 완료하는 데 몇 분이 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다.

4. **성능 권장 사항** 창에는 권장 사항이 있는 경우 권장 사항 목록이 표시됩니다. 

5. 권장 사항에는 관련 **데이터베이스**, **테이블**, **열** 및 **인덱스 크기** 에 대한 정보가 표시됩니다.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="성능 권장 사항 결과":::

6. 권장 사항을 구현하려면 쿼리 텍스트를 복사하고 선택한 클라이언트에서 실행합니다.

### <a name="permissions"></a>사용 권한
성능 권장 사항 기능을 사용하여 분석을 실행하는 데 필요한 **소유자** 또는 **참가자** 권한입니다.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 리소스 그룹에 Azure 리소스를 만들었습니다. 이러한 리소스가 더 이상 필요 없으면 서버 그룹을 삭제합니다. 서버 그룹의 *개요* 페이지에서 *삭제* 단추를 누릅니다. 팝업 페이지에 메시지가 표시되면 서버 그룹의 이름을 확인하고 최종 *삭제* 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> Azure Database for PostgreSQL의 [모니터링 및 튜닝](concepts-monitoring.md)에 대해 자세히 알아보세요.