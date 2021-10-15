---
title: Azure Monitor 통합 문서를 사용하여 Azure Database for MySQL 유연한 서버 모니터링
description: 이 문서에서는 Azure Monitor 통합 문서를 사용하여 Azure Database for MySQL 유연한 서버를 모니터링하는 방법을 설명합니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: aa26531dd9f644b4dc5b3343674abd7920fe57dd
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064593"
---
# <a name="monitor-azure-database-for-mysql-flexible-server-by-using-azure-monitor-workbooks"></a>Azure Monitor 통합 문서를 사용하여 Azure Database for MySQL 유연한 서버 모니터링

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버는 이제 Azure Monitor 통합 문서와 통합됩니다. 통합 문서를 사용하면 데이터를 분석하고 Azure Portal 내에서 풍부한 시각적 보고서를 만들기 위한 유연한 캔버스를 얻을 수 있습니다. 통합 문서를 사용하면 Azure에서 여러 데이터 원본을 탭하고 통합 대화형 환경으로 결합할 수 있습니다. 통합 문서 템플릿은 여러 사용자 및 팀에서 유연하게 다시 사용할 수 있도록 설계된 큐레이팅된 보고서 역할을 합니다. 

템플릿을 열면 템플릿의 내용으로 채워진 임시 통합 문서를 만듭니다. 이 통합을 통해 서버는 통합 문서 및 몇 가지 샘플 템플릿에 연결되며, 이를 통해 서비스를 대규모로 모니터링할 수 있습니다. 이러한 템플릿을 편집하고, 요구 사항에 맞게 사용자 지정하고, 대시보드에 고정하여 Azure 리소스의 집중적이고 구성된 보기를 만들 수 있습니다.
 
이 문서에서는 유연한 서버에 사용할 수 있는 다양한 통합 문서 템플릿에 대해 알아봅니다.

Azure Database for MySQL 유연한 서버에는 세 가지 사용 가능한 템플릿이 있습니다.
 
- **개요:** 서버의 리소스 사용률을 시각화하고 이해하는 데 도움이 되는 인스턴스 요약 및 최상위 메트릭을 표시합니다. 이 템플릿은 다음 보기를 표시합니다.

    * 서버 요약 
    * 데이터베이스 요약
    * 연결 메트릭 
    * 성능 메트릭 
    * Storage 메트릭 

* **감사:** 서버에 대해 수집된 감사 이벤트의 요약 및 세부 정보를 표시합니다. 이 템플릿은 다음 보기를 표시합니다.

    * 서비스에 대한 관리 작업
    * 감사 요약
    * 연결 이벤트 감사 요약
    * 연결 이벤트 감사
    * 테이블 액세스 요약
    * 식별된 오류

* **Query Performance Insight:** 인스턴스의 쿼리 워크로드, 장기 실행 쿼리, 느린 쿼리 분석 및 연결 메트릭에 대한 요약 및 세부 정보를 표시합니다. 이 템플릿은 다음 보기를 표시합니다.

    * 쿼리 로드
    * 총 활성 연결
    * 느린 쿼리 추세(>10초의 쿼리 시간)
    * 느린 쿼리 세부 정보
    * 상위 5개 가장 긴 쿼리 나열
    * 최소, 최대, 평균 및 표준 편차 쿼리 시간을 기준으로 느린 쿼리를 요약합니다.

요구 사항에 따라 이러한 템플릿을 편집하고 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure Monitor 통합 문서 개요를 참조하세요.](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)

 ## <a name="access-the-workbook-templates"></a>통합 문서 템플릿 액세스

Azure Portal 템플릿을 보려면 Azure Database for MySQL 유연한 서버에 대한 **모니터링** 창으로 이동한 다음 **통합 문서를 선택합니다.**

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="통합 문서 창의 '개요', '감사' 및 'Query Performance Insight' 템플릿을 보여주는 스크린샷":::

공용 템플릿 창으로 이동하면 템플릿 목록을 표시할 수도 **있습니다.**

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="'공용 템플릿' 창에서 '개요', '감사' 및 'Query Performance Insight' 템플릿을 보여 주는 다이어그램":::


## <a name="next-steps"></a>다음 단계
- Azure Monitor 통합 문서의 [액세스 제어에](../../azure-monitor/visualize/workbooks-access-control.md) 대해 알아봅니다.
- Azure Monitor 통합 문서의 [시각화 옵션에](../../azure-monitor/visualize/workbooks-overview.md#visualizations) 대해 자세히 알아보세요. 
