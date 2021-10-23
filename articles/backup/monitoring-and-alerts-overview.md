---
title: Azure Backup에 대 한 모니터링 및 보고 솔루션
description: Azure Backup에서 제공 하는 다양 한 모니터링 및 보고 솔루션에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 0eaa53aaeb54b8c474817aec1b6a33c125c77a51
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235794"
---
# <a name="monitoring-and-reporting-solutions-for-azure-backup"></a>Azure Backup에 대 한 모니터링 및 보고 솔루션

이 문서에서는 Azure Backup에서 제공 하는 다양 한 모니터링 및 보고 솔루션에 대 한 개요를 제공 합니다.

Azure Backup은 azure 리소스 Graph, Azure Monitor 경고, Azure Monitor 로그 및 Azure Resource Health와 같은 다양 한 azure 서비스와의 통합을 제공 합니다. Azure Backup는 Azure Portal, Azure PowerShell, Azure CLI, azure REST API 등의 다양 한 azure 클라이언트에도 인터페이스를 제공 합니다. 모니터링 및 보고 요구 사항에 따라 이러한 통합을 조합 하 여 사용할 수 있습니다.

## <a name="monitoring-and-reporting-scenarios"></a>모니터링 및 보고 시나리오

다음 표에는 엔터프라이즈 백업 배포에서 일반적으로 발생 하는 다양 한 모니터링 및 보고 시나리오와 이러한 각 시나리오에 대해 현재 제공 Azure Backup 되는 다양 한 기능 및 관련 문서 참조가 요약 되어 있습니다.

| 시나리오 | 사용 가능한 솔루션 |
| --- | --- |
| 백업 작업 및 백업 인스턴스 모니터링 | <ul><li>**기본 제공 모니터링**: 백업 [센터](./backup-center-overview.md) 대시보드를 통해 백업 작업 및 백업 인스턴스를 실시간으로 모니터링할 수 있습니다.</li><li>**사용자 지정 된 모니터링 대시보드**: Azure Backup [PowerShell](./backup-azure-vms-automation.md), [CLI](./create-manage-azure-services-using-azure-command-line-interface.md)및 [REST API](./backup-azure-arm-userestapi-managejobs.md)와 같은 비 포털 클라이언트를 사용 하 여 사용자 지정 대시보드에서 사용할 백업 모니터링 데이터를 쿼리할 수 있습니다.  <br><br>  또한 [Azure 리소스 Graph (ARG)](./query-backups-using-azure-resource-graph.md)를 사용 하 여 대규모 백업 (자격 증명 모음, 구독, 지역 및 Lighthouse 테 넌 트 간)을 쿼리할 수 있습니다.    <br><br>    [백업 탐색기](./monitor-azure-backup-with-backup-explorer.md) 는 고유한 대시보드를 만들기 위한 참조로 사용할 수 있는 ARG의 데이터를 사용 하는 샘플 모니터링 통합 문서입니다. </li></ul> |
| 전체 백업 상태 모니터링      |   **Resource Health**: recovery services 자격 증명 모음의 상태를 모니터링 하 고 리소스 상태 문제를 일으킨 이벤트 문제를 해결할 수 있습니다. [자세히 알아보기](../service-health/resource-health-overview.md).   <br><br>   상태 기록을 보고 리소스의 상태에 영향을 주는 이벤트를 식별할 수 있습니다. 리소스 상태 이벤트와 관련 된 경고를 트리거할 수도 있습니다.  |
| 중요 백업 인시던트에 대 한 경고 받기     |  <ul><li>**Azure Monitor (미리 보기)를 사용 하는 기본 제공 경고**: Azure Backup는 백업 데이터 삭제, 일시 삭제 사용 안 함, 백업 실패, 복원 오류 등의 시나리오에 대 한 [Azure Monitor을 기반으로 경고 솔루션](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview) 을 제공 합니다.    <br><br>  이러한 경고를 보고 백업 센터를 통해 관리할 수 있습니다. 이러한 경고 (예: 전자 메일)에 대 한 [알림을 구성](./backup-azure-monitoring-built-in-monitor.md#configuring-notifications-for-alerts) 하려면 Azure Monitor의 [작업 규칙](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal) 및 [작업 그룹](../azure-monitor/alerts/action-groups.md) 을 사용 하 여 경고를 광범위 한 알림 채널로 라우팅할 수 있습니다.  </li><li>**클래식 경고**: Recovery Services 자격 증명 모음 블레이드의 [백업 경고 탭을 사용 하 여 액세스 하](./backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 는 오래 된 경고 솔루션입니다. 이러한 경고는 백업 센터에서 볼 수 없습니다. 클래식 경고를 사용 하는 경우 앞에서 설명한 경고 솔루션을 사용 하 여 앞에서 설명한 것 처럼 Azure Monitor 기반 경고 솔루션을 사용 하는 것이 좋습니다. </li><li>**사용자 지정 경고**: 사용자 지정 논리에 따라 경고를 생성 해야 하는 경우에는 사용자가 LA (Log Analytics) 작업 영역에 진단 데이터를 보내도록 자격 증명 모음을 구성한 경우 이러한 시나리오에 대해 [Log Analytics 기반 경고](./backup-azure-monitoring-use-azuremonitor.md#create-alerts-by-using-log-analytics) 를 사용할 수 있습니다. [LA 작업 영역의 데이터가 업데이트 되](./backup-azure-monitoring-use-azuremonitor.md#diagnostic-data-update-frequency)는 현재 빈도 때문에이 솔루션은 일반적으로 실제 인시던트의 발생 횟수와 경고 생성 사이에 약간의 지연이 있을 수 있는 시나리오에 사용 됩니다. </li></ul>     |
| 기록 추세 분석        |     <ul><li>**기본 제공 보고서**: [백업 보고서](./configure-reports.md) (Azure Monitor 로그를 기반으로)를 사용 하 여 작업 성공 및 백업 사용량과 관련 된 기록 추세를 분석 하 고 백업에 대 한 최적화 기회를 검색할 수 있습니다. 이러한 보고서의 [정기 전자 메일을 구성할](./backup-reports-email.md) 수도 있습니다. </li><li>**사용자 지정 보고 대시보드**: 문서화 된 [시스템 함수](./backup-reports-system-functions.md) 를 사용 하 여 LA (Azure Monitor 로그)의 데이터를 쿼리하여 백업과 관련 된 기록 정보를 분석 하는 고유한 대시보드를 만들 수도 있습니다.</li></ul>    |
| 자격 증명 모음에서 사용자가 트리거한 작업 감사    |       **활동 로그**: 자격 증명 모음에 대 한 표준 [활동 로그](../azure-monitor/essentials/activity-log.md) 를 사용 하 여 백업 정책 수정, 백업 항목 복원 등과 같은 다양 한 사용자 트리거 동작에 대 한 정보를 볼 수 있습니다. 활동 로그에 대 한 경고를 구성 하거나 장기 보존을 위해 이러한 로그를 Log Analytics 작업 영역으로 내보낼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- 백업 센터에 대해 [자세히 알아보세요](./backup-center-overview.md) .
- Azure Monitor 경고에 [대해 자세히 알아보세요](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview) .
- Azure Resource Health에 [대해 자세히 알아보세요](../service-health/resource-health-overview.md) .