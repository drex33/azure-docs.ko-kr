---
title: 메트릭 경고 구성 - Azure Portal - Azure Database for MySQL - 유연한 서버
description: 이 문서에서는 Azure Portal에서 Azure Database for MySQL 유연한 서버의 메트릭 경고를 구성 및 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 0e7e2da03d90f4111601871db7085749f9efc75a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457357"
---
# <a name="set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 대한 메트릭에 대한 경고 설정 - 유연한 서버

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL을 설정하는 방법을 보여 줍니다. Azure 서비스의 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

이 경고는 특정 메트릭의 값이 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 경고가 트리거됩니다. 메트릭 경고는 상태를 저장합니다. 즉 상태가 변경될 때만 알림을 보냅니다.

트리거되면 다음 작업을 수행하도록 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다.
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* 웹후크 호출

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.

* [Azure Portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal에서 메트릭에 대한 경고 규칙 만들기

1. [Azure Portal](https://portal.azure.com/)에서 모니터링할 Azure Database for MySQL 유연한 서버를 선택합니다.
2. 사이드바의 **모니터링** 섹션에서 **경고** 를 선택합니다.
3. **+ 새 경고 규칙** 을 선택합니다.
4. **규칙 만들기** 페이지가 열립니다. 필수 정보를 입력합니다.
5. **조건** 섹션 내에서 **조건 선택** 을 선택합니다.
6. 지원되는 신호 목록이 표시되면 경고를 만들 메트릭을 선택합니다. 예를 들면 "스토리지 비율"을 선택합니다.
7. 지난 6시간의 메트릭에 대한 차트가 표시됩니다. **차트 기간** 드롭다운을 사용하여 더 긴 메트릭 기록을 표시하도록 선택합니다.
8. **임계값** 유형(예: "정적" 또는 "동적"), **연산자**(예: "보다 큼") 및 **집계 유형**(예: 평균)을 선택합니다. 이는 메트릭 경고 규칙이 평가할 논리를 결정합니다.
    - **정적** 임계값을 사용하는 경우 **임계값**(예: 85%)을 계속 정의합니다. 메트릭 차트는 적절한 임계값을 결정하는 데 도움이 될 수 있습니다.
    - **동적** 임계값을 사용하는 경우 **임계값 민감도** 를 계속 정의합니다. 최근 데이터에 기반하여 계산된 임계값이 메트릭 차트에 표시됩니다. [동적 임계값 조건 형식 및 민감도 옵션에 대해 자세히 알아보세요](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. 집계 유형 함수를 사용하여 데이터 요소를 그룹화하는 **집계 세분성(기간)** 간격(예: "30분")과 **빈도**(예: "15분마다")를 조정하여 조건을 구체화합니다.
10. **Done** 을 클릭합니다.
11. 작업 그룹을 추가합니다. 작업 그룹은 Azure 구독 소유자가 정의한 알림 기본 설정 컬렉션입니다. **작업 그룹** 섹션 내에서 **작업 그룹 선택** 을 선택하여 경고 규칙에 연결할 기존 작업 그룹을 선택합니다.
12. 경고에 대한 알림을 수신하는 새 작업 그룹을 만들 수도 있습니다. 자세한 내용은 [작업 그룹 만들기 및 관리](../../azure-monitor/alerts/action-groups.md)를 참조하세요.
13. 새 작업 그룹을 만들려면 **+ 작업 그룹 만들기** 를 선택합니다. **구독**, **리소스 그룹**, **작업 그룹 이름** 및 **표시 이름** 을 사용하여 "작업 그룹 만들기" 양식을 작성합니다.
14. 작업 그룹에 대한 **알림을** 구성합니다.

    **알림 유형** 에서 "이메일 Azure Resource Manager 역할"을 선택하여 알림을 받을 구독 소유자, 참가자 및 읽기 권한자를 선택합니다. 이메일을 보내기 위한 **Azure Resource Manager 역할** 을 선택합니다.
    **이메일/SMS 메시지/푸시/음성** 을 선택하여 특정 받는 사람에게 알림을 보낼 수도 있습니다.

    알림 유형에 **이름** 을 입력하고 완료되면 **검토 + 만들기** 를 선택합니다.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->

15. **경고 규칙 이름**, **설명**, **리소스 그룹에 경고 규칙 저장** 및 **심각도** 같은 **경고 규칙 세부 정보** 를 입력합니다.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. **경고 규칙 만들기** 를 선택하여 경고를 만듭니다.
    앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.
## <a name="manage-your-alerts"></a>경고 관리
경고를 만든 후 해당 경고를 선택하고 다음 작업을 수행할 수 있습니다.

* 이 경고와 관련된 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 확인.
* 경고 규칙을 **편집** 또는 **삭제**.
* 알림 수신을 일시적으로 중지하거나 다시 시작하려면 경로를 **사용 안 함** 또는 **사용** 으로 설정.


## <a name="next-steps"></a>다음 단계
- [메트릭 경고 설정](../../azure-monitor/alerts/alerts-metric.md)에 대해 자세히 알아봅니다.
- 사용 가능한 [Azure Database for MySQL 유연한 서버의 메트릭](./concepts-monitoring.md)에 대해 자세히 알아봅니다.
- [Azure Monitor에서 메트릭 경고가 작동하는 방식 이해](../../azure-monitor/alerts/alerts-metric-overview.md)