---
title: Azure Backup 대한 모니터링 및 보고 솔루션
description: Azure Backup 제공하는 다양한 모니터링 및 보고 솔루션에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 9045290c51d012db0346b4b2b81fa8ac88f22a69
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634301"
---
# <a name="monitoring-and-reporting-solutions-for-azure-backup"></a>Azure Backup 대한 모니터링 및 보고 솔루션

이 문서에서는 Azure Backup 제공하는 다양한 모니터링 및 보고 솔루션에 대한 개요를 제공합니다.

Azure Backup Azure Resource Graph, Azure Monitor 경고, Azure Monitor 로그 및 Azure Resource Health 같은 다양한 Azure 서비스와의 통합을 제공합니다. 또한 Azure Backup Azure Portal, Azure PowerShell, Azure CLI 및 Azure REST API 같은 다양한 Azure 클라이언트에 대한 인터페이스를 제공합니다. 모니터링 및 보고 요구 사항에 따라 이러한 통합의 조합을 사용할 수 있습니다.

## <a name="monitoring-and-reporting-scenarios"></a>모니터링 및 보고 시나리오

다음 표에서는 엔터프라이즈 백업 배포에서 일반적으로 발생하는 다양한 모니터링 및 보고 시나리오와 관련 문서 참조와 함께 이러한 각 시나리오에 대해 현재 Azure Backup 제공하는 다양한 기능에 대한 요약을 제공합니다.

| 시나리오 | 사용 가능한 솔루션 |
| --- | --- |
| 백업 작업 및 백업 인스턴스 모니터링 | <ul><li>**기본 제공 모니터링:** 백업 [센터](/azure/backup/backup-center-overview) 대시보드를 통해 백업 작업 및 백업 인스턴스를 실시간으로 모니터링할 수 있습니다.</li><li>**사용자 지정된 모니터링 대시보드:** Azure Backup [PowerShell,](/azure/backup/backup-azure-vms-automation) [CLI](/azure/backup/create-manage-azure-services-using-azure-command-line-interface)및 REST API 같은 포털이 [아닌](/azure/backup/backup-azure-arm-userestapi-managejobs)클라이언트를 사용하여 사용자 지정 대시보드에서 사용할 백업 모니터링 데이터를 쿼리할 수 있습니다.  <br><br>  또한 [ARG(Azure Resource Graph)를](/azure/backup/query-backups-using-azure-resource-graph)사용하여 대규모(자격 증명 모음, 구독, 지역 및 Lighthouse 테넌트에서) 백업을 쿼리할 수 있습니다.    <br><br>    [Backup 탐색기](/azure/backup/monitor-azure-backup-with-backup-explorer) 사용자 고유의 대시보드를 만들기 위한 참조로 사용할 수 있는 ARG의 데이터를 사용하는 하나의 샘플 모니터링 통합 문서입니다. </li></ul> |
| 전체 백업 상태 모니터링      |   **Resource Health:** 복구 서비스 자격 증명 모음의 상태를 모니터링하고 리소스 상태 문제를 일으키는 이벤트를 해결할 수 있습니다. [자세한 정보를 알아보세요](/azure/service-health/resource-health-overview).   <br><br>   상태 기록을 보고 리소스의 상태가 영향을 주는 이벤트를 식별할 수 있습니다. 리소스 상태 이벤트와 관련된 경고를 트리거할 수도 있습니다.  |
| 중요한 백업 인시던트 경고 받기     |  <ul><li>**Azure Monitor(미리 보기)를 사용하는 기본 제공 경고:** Azure Backup 백업 데이터 삭제, 일시 삭제 비활성화, 백업 실패 및 복원 실패와 같은 시나리오에 대한 Azure Monitor [기반으로 경고 솔루션을](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview) 제공합니다.    <br><br>  이러한 경고를 보고 Backup 센터를 통해 관리할 수 있습니다. 이러한 경고(예: 이메일)에 대한 [알림을 구성하려면](/azure/backup/backup-azure-monitoring-built-in-monitor#configuring-notifications-for-alerts) Azure Monitor [작업 규칙](/azure/azure-monitor/alerts/alerts-action-rules?tabs=portal) 및 [작업 그룹을](/azure/azure-monitor/alerts/action-groups) 사용하여 경고를 광범위한 알림 채널로 라우팅할 수 있습니다.  </li><li>**클래식 경고:** Recovery Services 자격 증명 모음 [블레이드의 백업 경고 탭을 사용하여 액세스한](/azure/backup/backup-azure-monitoring-built-in-monitor#backup-alerts-in-recovery-services-vault) 이전 경고 솔루션입니다. 이러한 경고는 Backup 센터에서 볼 수 없습니다. 클래식 경고를 사용하는 경우 경고에 대한 미래 지향 솔루션인 Azure Monitor 기반 경고 솔루션(위에서 설명)을 사용하는 것이 좋습니다. </li><li>**사용자 지정 경고:** 사용자 지정 논리에 따라 경고를 생성해야 하는 시나리오가 있는 경우 진단 데이터를 LA(Log [Analytics)](/azure/backup/backup-azure-monitoring-use-azuremonitor#create-alerts-by-using-log-analytics) 작업 영역으로 보내도록 자격 증명 모음을 구성한 경우 이러한 시나리오에 Log Analytics 기반 경고를 사용할 수 있습니다. LA 작업 [영역의 데이터가 업데이트되는 현재 빈도로](/azure/backup/backup-azure-monitoring-use-azuremonitor#diagnostic-data-update-frequency)인해 이 솔루션은 일반적으로 실제 인시던트의 발생과 경고 생성 사이에 약간의 시간 지연이 허용되는 시나리오에 사용됩니다. </li></ul>     |
| 기록 추세 분석        |     <ul><li>**기본 제공 보고서:** [Backup 보고서(Azure Monitor](/azure/backup/configure-reports) 로그 기반)를 사용하여 작업 성공 및 백업 사용과 관련된 기록 추세를 분석하고 백업에 대한 최적화 기회를 검색할 수 있습니다. 이러한 보고서의 [주기적인 이메일을 구성할](/azure/backup/backup-reports-email) 수도 있습니다. </li><li>**사용자 지정된 보고 대시보드: 문서화된** [시스템 함수를](/azure/backup/backup-reports-system-functions) 사용하여 LA(Azure Monitor 로그)의 데이터를 쿼리하여 백업과 관련된 기록 정보를 분석하는 고유한 대시보드를 만들 수도 있습니다.</li></ul>    |
| 자격 증명 모음에 대한 사용자 트리거 작업 감사    |       **활동 로그:** 자격 증명 모음에 대한 표준 [활동 로그를](/azure/azure-monitor/essentials/activity-log) 사용하여 백업 정책 수정, 백업 항목 복원 등과 같은 다양한 사용자 트리거 작업에 대한 정보를 볼 수 있습니다. 활동 로그에 대한 경고를 구성하거나 장기 보존을 위해 이러한 로그를 Log Analytics 작업 영역으로 내보낼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- Backup 센터에 대해 [자세히 알아보세요.](/azure/backup/backup-center-overview)
- Azure Monitor 경고에 대해 [자세히 알아보세요.](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview)
- Azure Resource Health 대해 [자세히 알아보세요.](/azure/service-health/resource-health-overview)
