---
title: 서비스 공급자 작업 모니터링
description: 고객은 로그된 활동을 모니터링하여 Azure Lighthouse를 통해 서비스 공급자가 수행한 작업을 확인할 수 있습니다.
ms.date: 07/16/2021
ms.topic: how-to
ms.openlocfilehash: 9f24126bc8d3252de3926e72aea2915092206e38
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389155"
---
# <a name="monitor-service-provider-activity"></a>서비스 공급자 작업 모니터링

[Azure Lighthouse](../overview.md) 구독을 위임받은 고객은 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md) 데이터를 보고 수행된 모든 작업을 확인할 수 있습니다. 이렇게 하면 고객은 고객 소유의 Azure AD(Azure Active Directory) 테넌트 내에서 사용자가 수행하는 작업과 함께, 서비스 공급자가 수행하는 작업을 완벽하게 파악할 수 있습니다.

## <a name="view-activity-log-data"></a>활동 로그 데이터 보기

Azure Portal의 **모니터** 메뉴에서 [활동 로그를 볼](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) 수 있습니다. 특정 구독에 대한 결과를 제한하려면 필터를 사용하여 특정 구독을 선택합니다. 프로그래밍 방식으로 [활동 로그 이벤트를 보고 검색할](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) 수도 있습니다.

> [!NOTE]
> 서비스 공급자 테넌트에 있는 사용자는 해당 구독이 Azure Lighthouse에 온보딩된 경우 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할(또는 읽기 권한자를 포함하는 다른 기본 제공 역할)에 그러한 권한이 부여되었다면 고객 테넌트에서 위임된 구독의 활동 로그 결과를 볼 수 있습니다.

활동 로그에 작업 이름과 해당 상태가 수행된 날짜 및 시간이 함께 표시됩니다. **이벤트 시작자** 열은 작업을 수행한 사용자(Azure Lighthouse를 통해 작동하는 서비스 공급자의 테넌트의 사용자 또는 고객의 테넌트에 있는 사용자)를 보여 줍니다. 사용자가 해당 구독에 할당된 테넌트 또는 역할이 아닌 사용자 이름이 표시됩니다.

Azure Portal에서 지난 90일간 로그된 작업을 사용할 수 있습니다. 90일 넘게 데이터를 저장하는 방법을 알아보려면 [Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석](../../azure-monitor/essentials/activity-log.md)을 참조하세요.

> [!NOTE]
> 활동 로그에는 서비스 공급자의 사용자가 표시되지만 API를 통해 역할 할당 정보를 검색할 때나 **Access Control(IAM)** 에는 해당 사용자와 역할 할당이 표시되지 않습니다.

## <a name="set-alerts-for-critical-operations"></a>중요한 작업에 대한 경고 설정

서비스 공급자나 자체 테넌트의 사용자가 수행하는 중요한 작업을 계속 인식하려면 [활동 로그 경고](../../azure-monitor/alerts/activity-log-alerts.md)를 만드는 것이 좋습니다. 예를 들어, 구독에 대한 모든 관리 작업을 추적하거나 특정 리소스 그룹의 가상 머신을 삭제하는 경우 알림이 표시되도록 할 수 있습니다. 경고를 만들 때, 경고에는 고객의 테넌트에 있는 사용자 및 모든 관리 테넌트에 의해 수행된 작업이 포함됩니다.

자세한 내용은 [활동 로그 경고 만들기 및 관리](../../azure-monitor/alerts/alerts-activity-log.md)를 참조하세요.

## <a name="create-log-queries"></a>로그 쿼리 만들기

로그 쿼리는 로그된 활동을 분석하거나 특정 항목에 집중하는 데 도움이 될 수 있습니다. 예를 들어 감사를 사용하려면 구독에 대해 수행된 모든 관리 수준 작업에 대해 보고해야 할 수 있습니다. 해당 작업만 필터링하고 사용자, 날짜 또는 다른 값을 기준으로 결과를 정렬하는 쿼리를 만들 수 있습니다.

자세한 내용은 [Azure Monitor의 로그 쿼리 개요](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.

## <a name="view-user-activity-across-domains"></a>도메인 간 사용자 작업 보기

여러 도메인의 개별 사용자 활동을 보려면 [도메인별 활동 로그](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) 샘플 통합 문서를 사용합니다.

도메인 이름을 기준으로 결과를 필터링할 수 있습니다. 범주, 수준 또는 리소스 그룹과 같은 추가 필터를 적용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [위임을 감사하고 제한](view-manage-service-providers.md#audit-and-restrict-delegations-in-your-environment)하는 방법을 알아봅니다.
- [Azure Monitor](../../azure-monitor/index.yml)에 대해 자세히 알아봅니다.
- Azure Portal에서 [서비스 공급자 제품을 보고 관리](view-manage-service-providers.md)하는 방법에 대해 알아봅니다.
