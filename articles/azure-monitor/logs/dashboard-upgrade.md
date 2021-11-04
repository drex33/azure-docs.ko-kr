---
title: Log Analytics 대시보드 시각화 업그레이드
description: 강력한 통찰력을 제공할 수 있는 쿼리를 사용 하 여 Log Analytics 대시보드 시각화를 업그레이드 하는 방법을 알아봅니다.
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 10/27/2021
ms.openlocfilehash: 67981c51fc3213d2a2f7e2b9e0834c51874a6a4a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465405"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Log Analytics 대시보드 시각화 업그레이드

2020 년 2 월에 향상 된 시각화 기술이 도입 되었습니다. 쿼리 결과를 시각화 하 고 강력한 통찰력을 얻기 위한 기능 향상 및 향상. 

이 [Azure 업데이트](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)에서이 업그레이드에 대 한 자세한 내용을 확인할 수 있습니다. 

이 새로운 시각화 기술은 쿼리 결과 집합에 대 한 새롭고 향상 된 환경을 하며 합니다. 

## <a name="dashboards-in-azure"></a>Azure의 대시보드

Azure 대시보드는 전체 Azure 노출 영역의 상태를 시각화 하는 방법입니다. Azure estate 상태에 단일 창을 제공 하 고 일반적인 동작에 대 한 다양 한 바로 가기를 사용할 수 있도록 설계 되었습니다. 

자세한 내용은 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md) 를 참조 하세요.


## <a name="upgrading-log-analytics-dashboard-parts"></a>Log Analytics 대시보드 파트 업그레이드

새 시각화 기술은 이전 구현과 관련 된 몇 가지 일반적인 문제를 해결 하 고 고정 된 Log Analytics 파트에 대 한 몇 가지 새로운 기능을 소개 합니다. 

- **사용 가능한 동일한 유형** -Log Analytics에서 사용할 수 있는 모든 시각화 유형은 대시보드에서 고정 된 부분으로 제공 됩니다.

- **일관 된 모양 및 느낌** -고정 된 부분에 대 한 시각화 모양과 느낌은 이제 Log Analytics와 거의 동일 합니다. 차이점은 시각적 개체의 데이터 내용에서 미묘한 차이가 필요한 최적화 때문입니다. 이러한 차이점에 대 한 자세한 내용은이 문서의 고려 사항 부분을 참조 하세요.

- **도구 설명 및 레이블** – 새로 고정 된 Log Analytics 시각화 도구 설명을 지원 합니다. 원형 및 도넛형 차트는 이제 레이블을 지원 합니다.

- **대화형 범례** – 시각화 범례를 클릭 하면 Log Analytics 같이 고정 된 시각적 개체에서 차원을 추가/제거할 수 있습니다.

## <a name="stage-1---opt-in-upgrade-message"></a>1 단계-옵트인 업그레이드 메시지

Log Analytics 고정 된 파트를 업그레이드할 수 있는 경우 대시보드의 고정 된 파트 Log Analytics 사용자가 자신의 시각화를 업그레이드할 수 있도록 하는 새 *옵트인* 알림이 나타납니다. 새 시각화를 경험 하 여 대시보드에서 선택한 시각화를 업그레이드 하려는 경우

 
![메뉴가](media/dashboard-upgrade/update-message-1.png)
 
![타일 시각화를 업데이트 하는 방법을 보여 주는 스크린샷](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> 대시보드가 게시 된 후에는 업그레이드를 취소할 수 없습니다. 그러나 다시 게시 하지 않고 대시보드에서 다른 위치로 이동 하는 경우 변경 내용이 삭제 됩니다.  

클릭 하면 시각화가 새 기술로 업데이트 됩니다. 시각화의 미묘한 차이점이 Log Analytics의 모양 및 느낌에 맞게 조정 될 수 있습니다.

시각화를 업그레이드 한 후에는 변경 내용을 적용 하기 위해 대시보드를 다시 게시 해야 합니다.

![업그레이드 된 시각화를 보여 주는 스크린샷](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>2 단계-모든 대시보드의 마이그레이션

초기 옵트인 기간이 끝난 후 Log Analytics 팀은 시스템의 모든 대시보드를 업그레이드 합니다. 모든 Azure 대시보드를 정렬 하면 팀에서 더 많은 시각화를 도입 하 고 보드에서 향상 된 환경을 경험할 수 있습니다.

## <a name="considerations"></a>고려 사항

대시보드에 고정 된 Log Analytics 시각화에는 최적의 환경을 위해 설계 된 특정 동작이 있습니다. 시각화를 대시보드에 고정할 때 다음 디자인 고려 사항을 검토 합니다.

### <a name="query-time-scope---30-day-limit"></a>쿼리 시간 범위-30 일 제한

대시보드는 여러 쿼리의 여러 시각화를 포함할 수 있으므로 고정된 단일 쿼리의 시간 범위는 30일로 제한됩니다. 단일 쿼리는 30 일 보다 작거나 같은 시간 범위 에서만 실행 될 수 있습니다. 이 제한 사항은 적절 한 대시보드 로드 시간을 보장 하기 위한 것입니다.

### <a name="query-data-values---25-values-and-other-grouping"></a>데이터 값 쿼리-25 개의 값과 기타 그룹화

대시보드는 시각적으로 조밀한 및 복잡할 수 있습니다. 대시보드를 볼 때 인지 부하를 줄이기 위해 디스플레이를 25 개의 다른 데이터 형식으로 제한 하 여 시각화를 최적화 합니다. 25 개를 초과 하는 경우 Log Analytics는 데이터를 최적화 합니다. 이 예제에서는 대부분의 데이터가 포함 된 25 개의 형식을 개별적으로 표시 한 다음 나머지 값을 "other" 값으로 그룹화 합니다. 다음 차트는 이러한 경우를 보여 줍니다.  

![25 가지 다른 데이터 형식을 포함 하는 대시보드를 보여 주는 스크린샷](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>로드 시 대시보드 새로 고침

대시보드는 로드 시 새로 고쳐집니다. 대시보드 고정 Log Analytics 시각화와 관련 된 모든 쿼리가 실행 되 고 대시보드가 로드 되 면 새로 고쳐집니다. 대시보드 페이지가 열려 있는 상태로 유지 되는 경우 대시보드의 데이터는 60 분 마다 새로 고쳐집니다.

## <a name="next-steps"></a>다음 단계

[Log Analytics에서 대시보드 만들기 및 공유](../visualize/tutorial-logs-dashboards.md)
