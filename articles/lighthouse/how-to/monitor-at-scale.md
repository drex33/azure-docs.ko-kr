---
title: 위임된 대규모 리소스 모니터링
description: Azure Lighthouse를 통해 고객 테넌트 전체에서 스케일링 가능한 방식으로 Azure Monitor 로그를 사용할 수 있습니다.
ms.date: 09/30/2021
ms.topic: how-to
ms.openlocfilehash: 7ae54918ffad64e6b9790c4458717807cacd09ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363240"
---
# <a name="monitor-delegated-resources-at-scale"></a>위임된 대규모 리소스 모니터링

서비스 공급자는 여러 고객 테넌트를 [Azure Lighthouse](../overview.md)에 온보딩했을 수 있습니다. Azure Lighthouse를 사용하여 서비스 공급자는 여러 테넌트에 걸쳐 대규모로 작업을 한 번에 수행할 수 있으므로 관리 작업을 보다 효율적으로 수행할 수 있습니다.

이 항목에서는 관리하는 고객 테넌트 전체에서 스케일링 가능한 방식으로 [Azure Monitor 로그](../../azure-monitor/logs/data-platform-logs.md)를 사용하는 방법을 보여 줍니다. 이 항목은 서비스 공급자 및 고객을 염두에 두고 만들었지만 이 지침은 [Azure Lighthouse를 사용하여 여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에도 적용됩니다.

> [!NOTE]
> 위임된 고객 구독에서 관리 테넌트의 사용자에게 [Log Analytics 작업 영역을 관리하는 데 필요한 역할](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions)이 부여되었는지 확인합니다.

## <a name="create-log-analytics-workspaces"></a>Log Analytics 작업 영역 만들기

데이터를 수집하려면 Log Analytics 작업 영역을 만들어야 합니다. Log Analytics 작업 영역은 Azure Monitor에서 수집한 데이터의 고유 환경입니다. 각 작업 영역에는 자체 데이터 리포지토리 및 구성이 있으며 데이터 원본 및 솔루션은 특정 작업 영역에 데이터를 저장하도록 구성됩니다.

고객 테넌트에서 직접 작업 영역을 만드는 것이 좋습니다. 이렇게 하면 고객 데이터가 사용자 테넌트로 내보내지지 않고 고객 테넌트에 유지됩니다. 고객 테넌트에서 작업 영역을 만들면 Log Analytics에서 지원하는 리소스 또는 서비스를 중앙 집중식으로 모니터링할 수 있어 모니터링하는 데이터 형식에 대한 유연성이 높아질 수 있습니다. [진단 설정](../..//azure-monitor/essentials/diagnostic-settings.md)에서 정보를 수집하려면 고객 테넌트에서 만든 작업 영역이 필요합니다.

> [!TIP]
> Log Analytics 작업 영역에서 데이터에 액세스하는 데 사용되는 자동화 계정은 작업 영역과 동일한 테넌트에서 만들어야 합니다.

[Azure Portal](../../azure-monitor/logs/quick-create-workspace.md), [Azure CLI](../../azure-monitor/logs/resource-manager-workspace.md) 또는 [Azure PowerShell](../../azure-monitor/logs/powershell-workspace-configuration.md)을 사용하여 Log Analytics 작업 영역을 만들 수 있습니다.

> [!IMPORTANT]
> 고객 테넌트에서 모든 작업 영역을 만든 경우 관리 테넌트의 구독에 Microsoft.Insights 리소스 공급자를 [등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)해야 합니다. 관리 테넌트에 기존 Azure 구독이 없는 경우 다음 PowerShell 명령을 사용하여 리소스 공급자를 수동으로 등록할 수 있습니다.
>
> ```powershell
> $ManagingTenantId = "your-managing-Azure-AD-tenant-id"
> 
> # Authenticate as a user with admin rights on the managing tenant
> Connect-AzAccount -Tenant $ManagingTenantId
> 
> # Register the Microsoft.Insights resource providers Application Ids
> New-AzADServicePrincipal -ApplicationId 1215fb39-1d15-4c05-b2e3-d519ac3feab4
> New-AzADServicePrincipal -ApplicationId 6da94f3c-0d67-4092-a408-bb5d1cb08d2d 
> ```

## <a name="deploy-policies-that-log-data"></a>데이터 로그 정책 배포

Log Analytics 작업 영역을 만든 후에는 진단 데이터가 각 테넌트의 적절한 작업 영역으로 전송되도록 고객 계층 구조에 [Azure Policy](../../governance/policy/index.yml)를 배포할 수 있습니다. 배포하는 정확한 정책은 모니터링하려는 리소스 종류에 따라 다를 수 있습니다.

정책을 만드는 방법에 대한 자세한 내용은 [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)를 참조하세요. 이 [커뮤니티 도구](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator)는 선택한 특정 리소스 종류를 모니터링하는 정책을 만드는 데 도움이 되는 스크립트를 제공합니다.

배포할 정책을 결정했으면 [위임된 구독에 대규모로 배포](policy-at-scale.md)할 수 있습니다.

## <a name="analyze-the-gathered-data"></a>수집된 데이터 분석

정책을 배포한 후에는 각 고객 테넌트에서 만든 Log Analytics 작업 영역에 데이터가 로그됩니다. 모든 관리형 고객에 대한 인사이트를 얻기 위해 [Azure Monitor 통합 문서](../../azure-monitor/visualize/workbooks-overview.md)와 같은 도구를 사용하여 여러 데이터 원본에서 정보를 수집하고 분석할 수 있습니다.

## <a name="query-data-across-customer-workspaces"></a>고객 작업 영역에서 데이터 쿼리

여러 작업 영역을 포함하는 공용 구조체를 만들고 [로그 쿼리](../../azure-monitor/logs/log-query-overview.md)를 실행하여 여러 고객 테넌트의 Log Analytics 작업 영역 간에 데이터를 검색할 수 있습니다. TenantID 열을 포함하여 어떤 결과가 어떤 테넌트에 속하는지 확인할 수 있습니다.

다음 예제 쿼리는 두 개의 개별 고객 테넌트에 있는 작업 영역 간에 있는 AzureDiagnostics 테이블에 공용 구조체를 만듭니다. 결과에 Category, ResourceGroup 및 TenantID 열이 표시됩니다.

``` Kusto
union AzureDiagnostics,
workspace("WS-customer-tenant-1").AzureDiagnostics,
workspace("WS-customer-tenant-2").AzureDiagnostics
| project Category, ResourceGroup, TenantId
```

여러 Log Analytics 작업 영역에 걸쳐 실행되는 쿼리의 추가 예제는 [Azure Monitor로 리소스 쿼리](../../azure-monitor/logs/cross-workspace-query.md)를 참조하세요.

> [!IMPORTANT]
> Log Analytics 작업 영역의 데이터를 쿼리하는 데 사용되는 자동화 계정을 사용하는 경우 해당 자동화 계정을 작업 영역과 동일한 테넌트에서 만들어야 합니다.

## <a name="view-alerts-across-customers"></a>고객에 대한 경고 보기

관리하는 고객 테넌트의 위임된 구독에 대한 [경고](../../azure-monitor/alerts/alerts-overview.md)를 볼 수 있습니다.

관리 테넌트에서 Azure Portal이나 API 및 관리 도구를 통해 [활동 로그 경고를 만들고, 살펴보고, 관리](../../azure-monitor/alerts/alerts-activity-log.md)할 수 있습니다.

여러 고객에 대한 경고를 자동으로 새로 고치려면 [Azure Resource Graph](../../governance/resource-graph/overview.md) 쿼리를 사용하여 경고를 필터링합니다. 쿼리를 대시보드에 고정하고 적절한 고객과 구독을 모두 선택할 수 있습니다. 예를 들어 아래 쿼리는 심각도가 0과 1인 경고를 표시하고 60분마다 새로 고칩니다.

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>다음 단계

- GitHub에서 [도메인별 활동 로그](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) 통합 문서를 사용해 봅니다.
- 여러 Log Analytics 작업 영역에서 [업데이트 관리 로그를 쿼리](../../automation/update-management/query-logs.md)하여 패치 준수 보고를 추적하는 [MVP 빌드 샘플 통합 문서](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)를 살펴봅니다.
- 다른 [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.