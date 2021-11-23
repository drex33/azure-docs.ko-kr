---
title: Query Performance Insight - Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB의 Query Performance Insight 기능을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e958a253834ee9548a1a91e38d947821a3058767
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132957306"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 Query Performance Insight

**적용 대상:** Azure Database for MariaDB 10.2

Query Performance Insight를 사용하면 가장 오랫동안 실행되는 쿼리, 쿼리가 시간의 경과에 따라 변경되는 방식 및 쿼리에 영향을 주는 대기 등을 빠르게 파악할 수 있습니다.


## <a name="common-scenarios"></a>일반적인 시나리오

### <a name="long-running-queries"></a>장기 실행 쿼리

- 지난 X시간 동안 가장 오래 실행되는 쿼리를 식별하는 경우
- 리소스에서 대기 중인 최상위 N개 쿼리를 식별하는 경우
 
### <a name="wait-statistics"></a>대기 통계

- 쿼리의 대기 특성 이해
- 리소스 대기 추세 및 리소스 경쟁이 있는 위치 이해

## <a name="permissions"></a>사용 권한

Query Performance Insight에서 쿼리 텍스트를 보는 데 필요한 **소유자** 또는 **참가자** 권한입니다. **읽기 권한자** 는 차트 및 표를 볼 수 있지만 쿼리 텍스트는 볼 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

Query Performance Insight가 작동하려면 [쿼리 저장소](concepts-query-store.md)에 데이터가 있어야 합니다.

## <a name="viewing-performance-insights"></a>Performance Insight 보기

Azure Portal의 [Query Performance Insight](concepts-query-performance-insight.md) 보기에는 쿼리 저장소의 핵심 정보가 시각화되어 표시됩니다.

Azure Database for MariaDB 서버의 포털 페이지에서 **Query Performance Insight** 를 선택합니다. 메뉴 바의 **지능형 성능** 섹션 아래에 있습니다.

### <a name="long-running-queries"></a>장기 실행 쿼리

**장기 실행 쿼리** 탭에는 실행당 평균 기간별 상위 5개 쿼리가 15분 간격으로 집계되어 표시됩니다. **쿼리 수** 드롭다운에서 선택하여 더 많은 쿼리를 볼 수 있습니다. 이 작업을 수행하면 특정 쿼리 ID에 대해 차트 색이 변경될 수 있습니다.

차트를 클릭하고 끌어 특정 기간으로 범위를 좁힐 수 있습니다. 확대/축소 아이콘을 사용하여 더 긴 기간이나 더 짧은 기간을 표시할 수도 있습니다.

![Query Performance Insight 장기 실행 쿼리](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>대기 통계 

> [!NOTE]
> 대기 통계는 쿼리 성능 문제를 해결하기 위한 것입니다. 문제 해결 목적을 위해서만 켜지도록 하는 것이 좋습니다. <br>대기 통계를 볼 때 Azure Portal에서 오류 메시지 “ *'Microsoft.DBforMariaDB'에 문제가 발생하여 요청을 수행할 수 없습니다. 이 문제가 계속되거나 예기치 않은 문제인 경우 이 정보로 지원 부서에 문의하세요.* ”를 받은 경우 더 작은 기간을 사용하세요.

대기 통계는 특정 쿼리를 실행하는 동안 발생하는 대기 이벤트에 대한 보기를 제공합니다. [MySQL 엔진 설명서](https://go.microsoft.com/fwlink/?linkid=2098206)에서 대기 이벤트 유형에 대해 자세히 알아보세요.

서버의 대기 쿼리를 시각화하려면 **대기 통계** 탭을 선택합니다.

대기 통계 보기에 표시되는 쿼리는 지정된 시간 간격 동안 가장 큰 대기를 나타내는 쿼리로 그룹화됩니다.

![Query Performance Insight 대기 통계](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="limitations"></a>제한 사항 

* 버전 10.3에 대 한 쿼리 성능 정보는 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MariaDB [모니터링 및 튜닝](concepts-monitoring.md) 에 대해 자세히 알아보세요.