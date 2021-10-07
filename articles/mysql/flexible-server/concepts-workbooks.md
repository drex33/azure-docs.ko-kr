---
title: Azure Database for MySQL-Azure Monitor 통합 문서를 사용 하는 유연한 서버 모니터링
description: Azure Monitor 통합 문서를 사용 하 여 Azure Database for MySQL 유연한 서버를 모니터링 하는 방법을 설명 합니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: a773048b2d1ddf8ad7b7993ef7975517506bbd07
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622423"
---
# <a name="monitoring-azure-database-for-mysql---flexible-server-with-azure-monitor-workbooks"></a>Azure Database for MySQL-Azure Monitor 통합 문서를 사용 하는 유연한 서버 모니터링

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버가 이제 Azure Monitor 통합 문서와 통합 되었습니다. 통합 문서는 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다. Azure를 통해 여러 데이터 원본을 사용 하 여 통합 된 대화형 환경으로 결합할 수 있습니다. 통합 문서 템플릿은 여러 사용자 및 팀에서 유연 하 게 다시 사용할 수 있도록 설계 된 큐 레이트 보고서 역할을 합니다. 템플릿을 열면 템플릿의 콘텐츠로 채워진 임시 통합 문서가 만들어집니다. 이러한 통합을 통해 서버에는 통합 문서에 대 한 링크와 몇 가지 샘플 템플릿이 있으며,이를 통해 규모에 맞게 서비스를 모니터링할 수 있습니다. 이러한 템플릿을 편집 하 고, 고객 요구 사항에 맞게 사용자 지정 하 고, 대시보드에 고정 하 여 Azure 리소스를 집중적으로 구성 된 보기를 만들 수 있습니다.
 
이 문서에서는 유연한 서버에 사용할 수 있는 다양 한 통합 문서 템플릿에 대해 알아봅니다.

Azure Database for MySQL 유연한 서버에 사용할 수 있는 세 가지 기본 템플릿이 있습니다.
 
- **개요:** 서버에서 리소스 사용률을 이해 하는 데 도움이 되는 인스턴스 요약 및 최상위 메트릭을 제공 합니다. Azure Database for MySQL-유연한 서버에 대 한 아래 세부 정보를 볼 수 있습니다.

    * 서버 요약 
    * 데이터베이스 요약
    * 연결 메트릭 
    * 성능 메트릭 
    * Storage 성과 

* **감사:** 서버에 대해 수집 된 감사 이벤트의 요약 및 세부 정보입니다. Azure Database for MySQL-유연한 서버에 대해이 템플릿과 함께 사용할 수 있는 뷰는 다음과 같습니다.

    * 서비스에 대 한 관리 작업
    * 감사 요약
    * 연결 이벤트 감사 요약
    * 연결 이벤트 감사
    * 테이블 액세스 요약
    * 식별 된 오류

* **쿼리 성능 Insights:** 인스턴스, 장기 실행 쿼리, 느리게 쿼리 분석 및 연결 메트릭에 대 한 쿼리 작업의 요약 및 세부 정보입니다. Azure Database for MySQL-유연한 서버에 대 한이 템플릿에서는 다음 뷰를 사용할 수 있습니다.

    * 쿼리 로드
    * 총 활성 연결
    * 느리게 쿼리 추세 (>10 초 쿼리 시간)
    * 저속 쿼리 세부 정보
    * 가장 긴 상위 5개 쿼리를 나열합니다.
    * 최소, 최대, 평균 및 표준 편차 쿼리 시간을 기준으로 느린 쿼리를 요약합니다.

이러한 템플릿을 편집 하 고 요구 사항에 따라 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure Monitor 통합 문서 개요-Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode) 를 참조 하세요.

 ## <a name="how-to-access-workbook-templates"></a>통합 문서 템플릿에 액세스 하는 방법

템플릿을 보려면 Azure Portal에서 Azure Database for MySQL – 유연한 서버에 대 한 **모니터링** 블레이드로 이동 하 여 **통합 문서** 를 선택 합니다.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="통합 문서를 표시 하는 다이어그램입니다.":::

또한 템플릿 목록에서 **공용 템플릿으로** 이동 하는 것을 볼 수 있습니다.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="통합 문서의 템플릿을 보여 주는 다이어그램":::


## <a name="next-steps"></a>다음 단계
- [통합](../../azure-monitor/visualize/workbooks-access-control.md) 문서를 Azure Monitor 하 고 다양 한 기능을 갖춘 통합 문서에 대해 자세히 알아보기
- [통합 문서 Azure Monitor을 시작](../../azure-monitor/visualize/workbooks-overview.md#visualizations) 하 고 통합 문서에 대 한 자세한 내용은 다양 한 풍부한 시각화 옵션을 확인 하세요.
