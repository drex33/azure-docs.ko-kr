---
title: Azure Monitor에서 & 모니터링 하는 클래식 경고 업데이트
description: 이전에 경고 (클래식) 아래 Azure Portal에 표시 된 클래식 모니터링 서비스 및 기능의 사용 중지에 대 한 설명입니다.
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: 64e9c454bfe8df9b65176fe344999e88204720d0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539122"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>클래식 경고 및 모니터링을 Azure Monitor 통합 경고 및 모니터링으로 대체

Azure Monitor은 Azure 리소스에서 ' 1 개의 메트릭 ' 및 ' 단일 경고 '를 지 원하는 통합 모니터링 스택입니다. 자세한 내용은이 [블로그 게시물](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)을 참조 하세요. 새로운 Azure 모니터링 및 경고 플랫폼은 더 빠르고, 더 효율적이 고 확장 가능 하 고, Microsoft 지능형 클라우드 철학에 맞게 증가 하는 클라우드 컴퓨팅 및 expanse를 유지 하는 기능을 제공 합니다.

새 Azure 모니터링 및 경고 플랫폼을 사용 하는 경우 Azure Monitor의 클래식 경고는 공용 클라우드 사용자에 게 사용이 중지 됩니다. Azure Government 클라우드 및 Azure 중국 21Vianet에 대한 클래식 경고는 **2024년 2월 29일** 에 사용 중지됩니다.

 ![Azure Portal의 클래식 경고](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

새 플랫폼에서 경고를 시작하고 다시 만드는 것이 좋습니다.

> [!IMPORTANT]
> 활동 로그에서 생성된 클래식 경고 규칙은 더 이상 사용 또는 마이그레이션되지 않습니다. 활동 로그에서 생성된 모든 클래식 경고 규칙은 새로운 Azure Monitor - 경고에서 액세스하여 사용할 수 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리](./alerts-activity-log.md)를 참조하세요. 마찬가지로, Service Health에 대한 경고는 새로운 Service Health 섹션에서 현재 상태로 액세스하여 사용할 수 있습니다. 자세한 내용은 [서비스 상태 알림에서 경고](../../service-health/alerts-activity-log-service-notifications-portal.md)를 참조하세요.

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Azure 리소스에 대 한 통합 메트릭 및 경고

3 월 2018에는 Azure 리소스에 대 한 차세대 경고가 발표 되었습니다. 최신 메트릭 플랫폼과 경고는 더 빠르며 차원을 사용 하 여 더 많은 세분성을 제공 합니다. 차원을 사용 하면 특정 값 조합, 조건 또는 연산을 분할 하 고 필터링 할 수 있습니다. 최신 메트릭 경고는 더 많은 알림과 자동화 작업을 허용 하는 작업 그룹을 사용 합니다. [Azure Monitor를 사용 하 여 메트릭 경고 관리](./alerts-metric.md)에 대 한 자세한 내용을 참조 하세요.

Azure 리소스에 대해 사용할 수 있는 최신 메트릭은 다음과 같습니다.

- **Azure Monitor 표준 플랫폼 메트릭** – 다양한 Azure 서비스와 제품에서 인기 있는 미리 채워진 메트릭을 제공합니다. 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) 및 [Azure Monitor에서 메트릭 경고 지원](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts) 문서를 참조하세요.
- **Azure Monitor 사용자 지정 메트릭** – Azure Diagnostics 에이전트가 포함된 사용자 구동 원본의 메트릭을 제공합니다. 자세한 내용은 [Azure Monitor의 사용자 지정 메트릭](../essentials/metrics-custom-overview.md) 문서를 참조하세요. 사용자 지정 메트릭을 사용하면 [Windows Azure Diagnostics 에이전트](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) 및 [InfluxData Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md)에서 수집한 메트릭을 게시할 수도 있습니다.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>클래식 모니터링 및 경고 플랫폼의 사용 중지

앞에서 설명한 대로 이전 클래식 모니터링 및 경고는 공용 클라우드 사용자에 게 사용이 중지 되었습니다. 여기에는 관련 Api, Azure Portal 인터페이스 및 서비스의 종료가 포함 되지만 31 년 5 **월 2021** 일까 지는 제한적으로 사용 됩니다. Azure Government 클라우드 및 Azure 중국 21Vianet에 대한 클래식 경고는 **2024년 2월 29일** 에 사용 중지됩니다.

특히, 사용 중지 범위는 Azure Portal의 [경고 (클래식) 섹션](./alerts-classic.overview.md) 에서 현재 사용할 수 있고 [microsoft insights/alertrules](/rest/api/monitor/alertrules) 리소스로 액세스할 수 있는 클래식 메트릭에 대 한 것입니다.

이는 다음을 의미합니다.

- 클래식 모니터링 및 경고 서비스는 사용 중지 되며 더 이상 새 경고 규칙을 만들 수 없습니다.
- 경고 (클래식)에 계속 존재 하는 경고 규칙은 계속 실행 되 고 알림을 발생 시킵니다.
- 대부분의 클래식 경고 규칙이 마이그레이션됩니다. 이 프로세스는 가동 중지 시간 없이 원활하게 진행되며 고객이 모니터링 범위를 손실하지 않도록 보장합니다.
- 발생 한 알림은 새 페이로드 구조를 포함 합니다. 새 구조를 사용 하려면 대상을 조정 해야 합니다.
- 자동으로 마이그레이션되지 않으며 사용자의 수동 작업이 계속 실행 될 수 있도록 하는 몇 가지 [클래식 경고 규칙이](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) 필요 합니다.

> [!IMPORTANT]
> Azure Monitor은 기존 경고 규칙을 새 플랫폼으로 [자발적으로 마이그레이션하기 위한 도구](alerts-using-migration-tool.md) 를 출시 했습니다. 나머지 규칙은 서비스가 사용 중지 된 후 자동으로 마이그레이션됩니다. 기존 경고 규칙을 마이그레이션한 후에는 [다른 Azure 리소스에 대 한 통합 메트릭 및 경고](#unified-metrics-and-alerts-for-azure-resources)의 새 페이로드를 처리 하기 위해 기존 경고 규칙 페이로드가 자동화 된 기존 경고 규칙 페이로드가 적용 되도록 해야 합니다. 자세한 내용은 [클래식 경고 규칙 마이그레이션 준비](alerts-prepare-migration.md)를 참조 하세요.

## <a name="pricing-for-migrated-alert-rules"></a>마이그레이션된 경고 규칙에 대 한 가격 책정

Azure Monitor [클래식 경고](./alerts-classic.overview.md) 를 새로운 경고 환경으로 마이그레이션하는 데 도움이 되는 마이그레이션 도구를 배포 하 고 있습니다. 마이그레이션된 경고 규칙 및 마이그레이션된 해당 작업 그룹 (email, webhook 또는 LogicApp)은 무료로 제공 됩니다. 임계값, 집계 유형 및 집계 세분성을 편집 하는 기능을 포함 하 여 클래식 경고와 함께 제공 되는 기능은 마이그레이션된 경고 규칙에 따라 계속 무료로 제공 됩니다. 그러나 마이그레이션된 경고 규칙을 편집 하 여 새 경고 플랫폼 기능, 알림 또는 작업 유형을 사용 하는 경우 해당 요금이 적용 됩니다. 경고 규칙 및 알림에 대 한 가격 책정에 대 한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.

다음은 경고 규칙에 대해 요금을 부과 하는 사례에 대 한 예입니다.

- 새 Azure Monitor 플랫폼에 체험 단위를 초과하여 만든 모든 새(마이그레이션되지 않음) 경고 규칙
- Azure Monitor에 포함된 체험 단위를 초과하여 수집되고 유지된 모든 데이터
- Application Insights에서 실행된 모든 다중 테스트 웹 테스트
- Azure Monitor에 포함된 체험 단위를 초과하여 저장된 모든 사용자 지정 메트릭
- 새 메트릭 경고 기능 (예: 빈도, 여러 리소스/차원, [동적 임계값](../alerts/alerts-dynamic-thresholds.md), 리소스/신호 변경 등)을 사용 하도록 편집 된 모든 마이그레이션된 경고 규칙입니다.
- 최신 알림을 사용 하도록 편집 된 모든 마이그레이션된 작업 그룹 또는 SMS, 음성 통화 및/또는 ITSM 통합과 같은 작업 유형입니다.

## <a name="next-steps"></a>다음 단계

* [새로운 통합 Azure Monitor](../overview.md)에 대해 자세히 알아봅니다.
* [새로운 Azure 경고](./alerts-overview.md)에 대해 자세히 알아봅니다.
