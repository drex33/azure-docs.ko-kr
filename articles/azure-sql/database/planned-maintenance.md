---
title: Azure 유지 관리 이벤트 계획
description: Azure SQL Database 및 Azure SQL Managed Instance에서 계획된 유지 관리 이벤트를 준비하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: mathoma
ms.date: 3/23/2021
ms.openlocfilehash: a697c0a3095963760d6a95159790c02cbab14d71
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121620"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance에서 유지 관리 이벤트 계획하기
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance의 데이터베이스에서 계획된 유지 관리 이벤트를 준비하는 방법을 알아봅니다.

## <a name="what-is-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트란?

Azure SQL Database 및 Azure SQL Managed Instance 서비스의 안전, 규정 준수, 안정성, 성능을 유지하기 위해 서비스 구성 요소를 통해 거의 지속적으로 업데이트를 수행합니다. [핫 패칭](https://aka.ms/azuresqlhotpatching) 같은 혁신적인 기술과 강력한 최신 서비스 아키텍처 덕분에 대부분의 업데이트가 완전히 투명하며 서비스 가용성에 영향을 미치지 않습니다. 그러나 몇 가지 업데이트 유형은 짧은 서비스 중단을 유발하고 특별한 처리가 필요합니다. 

계획된 유지 관리 기간 동안 데이터베이스 쿼럼 멤버는 한 번에 하나씩 오프라인 상태가 되는데, 그 이유는 응답하는 주 복제본이 하나 있도록 하기 위해서입니다. 중요 비즈니스용 및 프리미엄 데이터베이스의 경우 클라이언트 가동 중지 시간을 보장하기 위해 하나 이상의 보조 복제본도 온라인 상태가 됩니다. 주 복제본을 오프라인으로 전환해야 하는 경우, 재구성 프로세스가 발생합니다. 중요 비즈니스용 및 프리미엄 데이터베이스의 경우 보조 복제본 중 하나가 새로운 주 복제본이 됩니다. 범용, 표준 및 기본 데이터베이스의 경우 주 복제본은 여유 용량이 충분한 다른 상태 비저장 컴퓨팅 노드로 이동됩니다.

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트 기간 동안 예상되는 상황

유지 관리 이벤트는 유지 관리 이벤트가 시작될 때 주 복제본과 보조 복제본의 상호 연결에 따라 하나 또는 여러 재구성을 생성할 수 있습니다. 평균적으로 계획된 유지 관리 이벤트당 1.7회의 재구성이 발생합니다. 재구성은 일반적으로 30초 이내에 완료됩니다. 평균은 8초입니다. 이미 연결되어있는 경우, 애플리케이션은 데이터베이스의 새로운 주 복제본에 다시 연결해야 합니다. 새로운 주 복제본이 온라인 상태가 되기 전에, 데이터베이스가 재구성되는 동안 새 연결을 시도하면 40613(데이터베이스 사용 불가): “서버 ‘{servername}’의 데이터베이스 ‘{databasename}’을(를) 현재 사용할 수 없습니다. 나중에 연결을 다시 시도하세요.” 데이터베이스에 장기 실행 쿼리가 있으면, 이 쿼리가 재구성 중에 중단되어 다시 시작해야 합니다.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트를 시뮬레이트하는 방법

프로덕션에 배포하기 전에 클라이언트 애플리케이션이 유지 관리 이벤트에 대한 복원력이 있는지 확인하면 애플리케이션 오류의 위험을 완화하고 최종 사용자의 애플리케이션 가용성에 기여합니다. PowerShell, CLI 또는 REST API를 통해 [애플리케이션 오류 복원력을 테스트](./high-availability-sla.md#testing-application-fault-resiliency)하여 계획된 유지 관리 이벤트 중에 클라이언트 애플리케이션의 동작을 테스트할 수 있습니다. Managed Instance 대한 [수동 장애 조치(failover) 시작](https://aka.ms/mifailover-techblog)도 참조하세요. 주 복제본을 오프라인 상태로 전환하는 유지 관리 이벤트와 동일한 동작을 생성합니다.

## <a name="retry-logic"></a>재시도 논리

클라우드 데이터베이스 서비스에 연결하는 모든 클라이언트 프로덕션 애플리케이션은 강력한 연결 [재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 구현해야 합니다. 이렇게 하면 최종 사용자에게 투명하게 재구성하거나, 적어도 부정적 효과를 최소화하는 데 도움이 됩니다.

### <a name="service-health-alert"></a>서비스 상태 경고

서비스 문제 또는 계획된 유지 관리 활동에 대한 경고를 받으려면 적절한 이벤트 유형 및 작업 그룹과 함께 Azure Portal에서 Service Health 경고를 사용할 수 있습니다. 자세한 내용은 이 [Azure 서비스 알림에 대한 경고 수신](../../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal)을 참조하세요.

## <a name="resource-health"></a>리소스 상태

데이터베이스에 로그온 장애가 있으면, [Azure Portal](https://portal.azure.com)의 [Resource Health](../../service-health/resource-health-overview.md#get-started) 창에서 현재 상태를 확인하세요. 상태 기록 섹션에는 각 이벤트에 대한 가동 중지 시간 이유가 포함됩니다(가능한 경우).

## <a name="maintenance-window-feature"></a>유지 관리 기간 기능

유지 관리 기간 기능을 사용하면 적합한 Azure SQL 데이터베이스 및 SQL 관리되는 인스턴스에 대해 예측 가능한 유지 관리 기간 일정을 구성할 수 있습니다. 자세한 내용은 [유지 관리 기간](maintenance-window.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 및 Azure SQL Managed Instance의 [Resource Health](resource-health-to-troubleshoot-connectivity.md)에 대해 자세히 알아보세요.
- 재시도 논리에 대한 자세한 내용은 [일시적인 오류에 대한 재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 참조하세요.
- [유지 관리 기간](maintenance-window.md) 기능을 사용하여 유지 관리 기간 일정을 구성합니다.