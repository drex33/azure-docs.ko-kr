---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 마이그레이션 후 관리'
description: 마이그레이션이 성공적으로 완료되면 다음 단계로 새 클라우드 기반 데이터 워크로드 리소스를 관리합니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5c7ef5f12cf4ec1f6776abbc405cf5145c27c0f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297452"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-post-migration-management"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 마이그레이션 후 관리

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[MySQL Workbench를 사용한 데이터 마이그레이션](09-data-migration-with-mysql-workbench.md)

## <a name="monitoring-and-alerts"></a>모니터링 및 경고

마이그레이션이 성공적으로 완료되면 다음 단계로 새 클라우드 기반 데이터 워크로드 리소스를 관리합니다. 관리 작업에는 컨트롤 플레인과 데이터 평면 활동이 모두 포함됩니다. 컨트롤 플레인 활동은 Azure 리소스(이 경우 MySQL) **내에** 있는 데이터 평면과 달리 Azure 리소스와 관련된 활동입니다.

Azure Database for MySQL는 [Azure Monitor,](../../../azure-monitor/overview.md) [Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md) , [Microsoft 센티널](../../../sentinel/overview.md)등의 Azure 기반 도구를 사용 하 여 이러한 유형의 작업 활동을 모두 모니터링할 수 있는 기능을 제공 합니다. Azure 기반 도구 외에도 이러한 로그를 사용하도록 SIEM(보안 정보 및 이벤트 관리) 시스템을 구성할 수 있습니다.

새 클라우드 기반 워크로드를 모니터링하는 데 어떤 도구를 사용하더라도 Azure 및 데이터베이스 관리자에게 의심스러운 활동을 알리기 위해 경고를 만들어야 합니다. 특정 경고 이벤트에 잘 정의된 수정 경로가 있는 경우 이벤트 해결을 위해 경고가 자동화된 [Azure Runbook](../../../automation/learn/powershell-runbook-managed-identity.md)을 발생할 수 있습니다.

완벽하게 모니터링되는 환경을 만드는 첫 번째 단계는 MySQL 로그 데이터가 Azure Monitor로 이동될 수 있도록 하는 것입니다. 자세한 내용은 [Azure Portal에서 Azure Database for MySQL에 대한 감사 로그 구성 및 액세스](../../howto-configure-audit-logs-portal.md)를 참조하세요.

로그 데이터가 이동되게 되면 [KQL(Kusto 쿼리 언어)](/azure/data-explorer/kusto/query/) 쿼리 언어를 사용하여 다양한 로그 정보를 쿼리합니다. KQL에 익숙하지 않은 관리자는 [여기](/azure/data-explorer/kusto/query/sqlcheatsheet) 또는 [Azure Monitor에서 로그 쿼리 시작](../../../azure-monitor/logs/get-started-queries.md) 페이지에서 SQL-KQL 참고 자료를 찾을 수 있습니다.

예를 들어 Azure Database for MySQL의 메모리 사용량을 가져오려면 다음을 수행합니다.

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "memory\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
CPU 사용량을 가져오려면 다음을 수행합니다.

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "cpu\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
KQL 쿼리를 만든 후에는 이러한 쿼리를 기준으로 [로그 경고](../../../azure-monitor/alerts/alerts-unified-log.md)를 만듭니다.

## <a name="server-parameters"></a>서버 매개 변수

마이그레이션의 일부로 온-프레미스 [서버 매개 변수](../../concepts-server-parameters.md)가 빠른 송신을 지원하도록 수정되었을 가능성이 높습니다. 또한 빠른 수신을 지원하기 위해 Azure Database for MySQL 매개 변수를 수정했습니다. 마이그레이션 후 Azure 서버 매개 변수를 원래의 온-프레미스 워크로드에 최적화된 값으로 다시 설정해야 합니다.

그러나 서버 매개 변수를 검토하고 워크로드 및 환경에 적절하게 변경해야 합니다. 온-프레미스 환경에 적합한 일부 값이 클라우드 기반 환경에 최적화된 값은 아닐 수 있습니다. 또한 현재 온-프레미스 매개 변수를 Azure로 마이그레이션할 계획인 경우 실제로 설정할 수 있는지 확인합니다.

일부 매개 변수는 Azure Database for MySQL에서 수정할 수 없습니다.

## <a name="powershell-module"></a>PowerShell 모듈

Azure Portal 및 Windows PowerShell은 Azure Database for MySQL을 관리하는 데 사용할 수 있습니다. PowerShell을 시작하려면 다음 PowerShell 명령을 사용하여 MySQL용 Azure PowerShell cmdlet을 설치합니다.

`Install-Module -Name Az.MySql`

모듈을 설치한 후에는 다음과 같은 자습서를 참조하여 관리 활동 스크립팅을 활용하는 방법을 알아봅니다.

  - [자습서: PowerShell을 사용하여 Azure Database for MySQL 디자인](../../tutorial-design-database-using-powershell.md)

  - [PowerShell을 사용하여 Azure Database for MySQL 서버를 백업 및 복원하는 방법](../../howto-restore-server-powershell.md)

  - [PowerShell을 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성](../../howto-configure-server-parameters-using-powershell.md)

  - [PowerShell을 사용하여 Azure Database for MySQL 서버의 스토리지 자동 증가](../../howto-auto-grow-storage-powershell.md)

  - [PowerShell을 사용하여 Azure Database for MySQL에서 읽기 복제본을 만들고 관리하는 방법](../../howto-read-replicas-powershell.md)

  - [PowerShell을 사용하여 Azure Database for MySQL 서버 다시 시작](../../howto-restart-server-powershell.md)

## <a name="azure-database-for-mysql-upgrade-process"></a>Azure Database for MySQL 업그레이드 프로세스

Azure Database for MySQL은 PaaS 제품이므로 관리자가 운영 체제 또는 MySQL 소프트웨어의 업데이트 관리를 담당하지 않습니다. 그러나 업그레이드 프로세스는 예기치 않게 진행될 수 있으며 배포 시 MySQL 서버 워크로드가 중지될 수 있습니다. 특정 인스턴스가 유지 관리 모드로 전환되는 경우 워크로드를 읽기 복제본으로 다시 라우팅하여 이러한 가동 중지 시간을 계획합니다.

> [!NOTE]
> 이 스타일의 장애 조치(failover) 아키텍처에서는 이러한 유형의 장애 조치 시나리오를 지원하기 위해 애플리케이션 데이터 계층을 변경해야 할 수 있습니다. 읽기 복제본이 읽기 복제본으로 유지 관리되고 승격되지 않을 경우 애플리케이션은 데이터를 읽을 수만 있으며 데이터베이스에 정보를 쓰려고 하는 작업은 실패할 수 있습니다.

[계획된 유지 관리 알림](../../concepts-monitoring.md#planned-maintenance-notification) 기능은 업데이트 또는 중요 보안 패치를 설치하기 최대 72시간 전에 리소스 소유자에게 알립니다. 데이터베이스 관리자는 계획되거나 계획되지 않은 유지 관리를 애플리케이션 사용자에게 알려야 할 수 있습니다.

> [!NOTE]
> Azure Database for MySQL 유지 관리 알림은 매우 중요합니다. 데이터베이스 유지 관리에서는 일정 기간 동안 데이터베이스 및 연결된 애플리케이션을 사용할 수 있습니다.

## <a name="wwi-scenario"></a>WWI 시나리오

WWI는 Azure 활동 로그를 활용하고 MySQL 로깅이 [Log Analytics 작업 영역](../../../azure-monitor/logs/design-logs-deployment.md)으로 이동되게 하기로 결정했습니다. 이 작업 영역은 [위협 분석](../../concepts-security.md#threat-protection) 이벤트를 표시 하 고 인시던트를 만드는 [Microsoft 센티널](../../../sentinel/index.yml) 의 일부로 구성 됩니다.

MySQL DBA는 MySQL Server 관리를 자동화하여 매번 Azure Portal에 로깅할 필요가 없도록 하기 위해 Azure Database for [MySQL Azure PowerShell cmdlet](../../quickstart-create-mysql-server-database-using-azure-powershell.md)을 설치했습니다.

## <a name="management-checklist"></a>관리 검사 목록

  - CPU 및 메모리와 같은 일반적인 항목에 대한 리소스 경고를 만듭니다.

  - 마이그레이션 후 대상 데이터 워크로드에 대해 서버 매개 변수가 구성되어 있는지 확인합니다.

  - 스크립트 일반 관리 작업

  - 업그레이드 및 패치와 같은 유지 관리 이벤트에 대한 알림을 설정합니다. 필요에 따라 사용자에게 알립니다.  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [최적화](./11-optimization.md)
