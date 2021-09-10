---
title: 쿼리 언어 이해
description: Resource Graph 테이블과 Azure Resource Graph와 함께 사용 가능한 Kusto 데이터 형식, 연산자 및 함수를 설명합니다.
ms.date: 08/11/2021
ms.topic: conceptual
ms.openlocfilehash: 7d23a8958ebfa90658ec5769b077f07091eff89d
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567466"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph 쿼리 언어 이해

Azure Resource Graph 쿼리 언어는 다양한 연산자 및 함수를 지원합니다. 각각은 [KQL(Kusto Query Language)](/azure/data-explorer/kusto/query/index)을 기반으로 작동합니다. Resource Graph에서 사용하는 쿼리 언어에 대해 알아보려면 [KQL에 대한 자습서](/azure/data-explorer/kusto/query/tutorial)로 시작하세요.

이 문서에서는 Resource Graph에서 지원하는 언어 구성 요소에 대해 설명합니다.

- [Resource Graph 테이블](#resource-graph-tables)
- [Resource Graph 사용자 지정 언어 요소](#resource-graph-custom-language-elements)
- [지원되는 KQL 언어 요소](#supported-kql-language-elements)
- [쿼리 범위](#query-scope)
- [이스케이프 문자](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph 테이블

Resource Graph는 Azure Resource Manager 리소스 종류 및 해당 속성에 관해 저장하는 데이터에 대한 여러 테이블을 제공합니다. 일부 테이블을 `join` 또는 `union` 연산자와 함께 사용하여 관련 리소스 종류에서 속성을 가져올 수 있습니다. 다음은 Resource Graph에서 사용할 수 있는 테이블의 목록입니다.

|Resource Graph 테이블 |다른 테이블을 `join`할 수 있나요? |Description |
|---|---|---|
|리소스 |예 |쿼리에 정의된 내용이 없는 경우 기본 테이블입니다. 대부분의 Resource Manager 리소스 종류 및 속성이 포함되어 있습니다. |
|ResourceContainers |예 |관리 그룹(`Microsoft.Management/managementGroups`), 구독(`Microsoft.Resources/subscriptions`) 및 리소스 그룹(`Microsoft.Resources/subscriptions/resourcegroups`) 리소스 종류 및 데이터를 포함합니다. |
|AdvisorResources |예(미리 보기) |`Microsoft.Advisor` _관련_ 리소스를 포함합니다. |
|AlertsManagementResources |예(미리 보기) |`Microsoft.AlertsManagement` _관련_ 리소스를 포함합니다. |
|ExtendedLocationResources |예 |`Microsoft.ExtendedLocation` _관련_ 리소스를 포함합니다. |
|GuestConfigurationResources |예 |`Microsoft.GuestConfiguration` _관련_ 리소스를 포함합니다. |
|HealthResources|Yes |`Microsoft.ResourceHealth/availabilitystatuses` _관련_ 리소스를 포함합니다. |
|KubernetesConfigurationResources |예 |`Microsoft.KubernetesConfiguration` _관련_ 리소스를 포함합니다. |
|MaintenanceResources |부분적으로 예, _에_ 만 조인. (미리 보기) |`Microsoft.Maintenance` _관련_ 리소스를 포함합니다. |
|PatchAssessmentResources|예 |Azure Virtual Machines 패치 평가와 _관련된_ 리소스를 포함합니다. |
|PatchInstallationResources|예 |Azure Virtual Machines 패치 설치와 _관련된_ 리소스를 포함합니다. |
|PolicyResources |Yes |`Microsoft.PolicyInsights` _관련_ 리소스를 포함합니다. |
|RecoveryServicesResources |부분적으로 예, _에_ 만 조인. (미리 보기) |`Microsoft.DataProtection` 및 `Microsoft.RecoveryServices` _관련_ 리소스를 포함합니다. |
|SecurityResources |예(미리 보기) |`Microsoft.Security` _관련_ 리소스를 포함합니다. |
|ServiceHealthResources |아니요(미리 보기) |`Microsoft.ResourceHealth/events` _관련_ 리소스를 포함합니다. |
|WorkloadMonitorResources |예 |`Microsoft.WorkloadMonitor` _관련_ 리소스를 포함합니다. |

리소스 종류를 비롯한 전체 목록은 [참조: 지원되는 테이블 및 리소스 종류](../reference/supported-tables-resources.md)를 참조하세요.

> [!NOTE]
> _리소스_ 는 기본 테이블입니다. _리소스_ 테이블을 쿼리하는 동안에는 `join` 또는 `union`을 사용하지 않는 한 테이블 이름을 제공하지 않아도 됩니다. 그러나 권장되는 방법은 항상 쿼리에 초기 테이블을 포함하는 것입니다.

포털의 Resource Graph 탐색기를 사용하여 각 테이블에서 사용할 수 있는 리소스 종류를 검색할 수 있습니다. 또는 `<tableName> | distinct type` 쿼리를 사용하여 해당 환경에 존재하는, 지정된 Resource Graph 테이블이 지원하는 리소스 종류의 목록을 가져올 수 있습니다.

다음 쿼리는 단순한 `join`을 보여 줍니다. 쿼리 결과는 열을 함께 혼합하며, 조인된 테이블의 모든 중복 열 이름(이 예에서는 _ResourceContainers_)에는 **1** 이 추가됩니다. _ResourceContainers_ 테이블에는 구독과 리소스 그룹 모두에 대한 종류가 있으므로 각 종류를 _Resources_ 테이블의 리소스에 조인하는 데 사용할 수 있습니다.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

다음 쿼리는 `join`의 더 복잡한 사용법을 보여 줍니다. 먼저 쿼리는 `project`를 사용하여 Azure Key Vault 자격 증명 모음 리소스 종류에 대한 _리소스_ 에서 필드를 가져옵니다. 다음 단계에서는 `join`을 사용하여 _ResourceContainers_ 와 결과를 병합합니다. 이 경우의 형식은 첫 번째 테이블의 `project` 및 조인된 테이블의 `project` 모두에 있는 속성에 _대한_ 구독입니다. 필드 이름 바꾸기는 속성이 _리소스_ 에 이미 있으므로 `join`이 이를 _name1_ 으로 추가하는 것을 방지합니다. 쿼리 결과는 키 자격 증명 모음의 유형, 이름, 위치 및 리소스 그룹과 해당 모음이 있는 구독 이름을 표시하는 단일 키 자격 증명 모음입니다.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
| limit 1
```

> [!NOTE]
> `project`를 사용하여 `join` 결과를 제한하는 경우 `join`에서 두 테이블의 관계를 설정하기 위해 사용하는 _subscriptionId_ 속성을 `project`에 포함해야 합니다.

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>확장 속성(미리 보기)

_미리 보기_ 기능으로서, Resource Graph의 일부 리소스 종류에는 Azure Resource Manager에서 제공하는 속성 이외에도 쿼리에 사용할 수 있는 추가 형식 관련 속성이 있습니다. _확장 속성_ 이라고 하는 이 값의 집합은 `properties.extended`의 지원되는 리소스 종류에 있습니다. _확장 속성_ 을 포함하는 리소스 종류를 확인하려면 다음 쿼리를 사용합니다.

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

예: `instanceView.powerState.code`를 기준으로 가상 컴퓨터 수 가져오기:

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Resource Graph 사용자 지정 언어 요소

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>공유 쿼리 구문(미리 보기)

미리 보기 기능으로서, [공유 쿼리](../tutorials/create-share-query.md)는 Resource Graph 쿼리에서 직접 액세스할 수 있습니다. 이 시나리오에서는 표준 쿼리를 공유 쿼리로 만들고 다시 사용할 수 있습니다. Resource Graph 쿼리 내에서 공유 쿼리를 호출하려면 `{{shared-query-uri}}` 구문을 사용합니다. 공유 쿼리의 URI는 해당 쿼리의 **설정** 페이지에 있는 공유 쿼리의 _리소스 ID_ 입니다. 이 예에서 공유 쿼리 URI는 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS`입니다.
이 URI는 다른 쿼리에서 참조하려는 구독, 리소스 그룹 및 공유 쿼리의 전체 이름을 가리킵니다. 이 쿼리는 [자습서: 쿼리 만들기 및 공유](../tutorials/create-share-query.md)에서 만든 쿼리와 동일합니다.

> [!NOTE]
> 공유 쿼리를 참조하는 쿼리는 공유 쿼리로 저장할 수 없습니다.

예 1: 공유 쿼리만 사용

이 Resource Graph 쿼리의 결과는 공유 쿼리에 저장된 쿼리와 동일합니다.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

예 2: 공유 쿼리를 더 큰 쿼리의 일부로 포함

이 쿼리는 먼저 공유 쿼리를 사용한 다음 `limit`을 사용하여 결과를 추가로 제한합니다.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>지원되는 KQL 언어 요소

Resource Graph는 모든 KQL [데이터 형식](/azure/data-explorer/kusto/query/scalar-data-types/), [스칼라 함수](/azure/data-explorer/kusto/query/scalarfunctions), [스칼라 연산자](/azure/data-explorer/kusto/query/binoperators) 및 [집계 함수](/azure/data-explorer/kusto/query/any-aggfunction)의 하위 집합을 지원합니다. Resource Graph는 특정 [테이블 형식 연산자](/azure/data-explorer/kusto/query/queries)를 지원하며, 그 중 일부는 동작이 서로 다릅니다.

### <a name="supported-tabulartop-level-operators"></a>지원되는 테이블 형식/최상위 수준 연산자

Resource Graph에서 지원하는 KQL 테이블 형식 연산자와 특정 샘플의 목록은 다음과 같습니다.

|KQL |Resource Graph 샘플 쿼리 |메모 |
|---|---|---|
|[count](/azure/data-explorer/kusto/query/countoperator) |[키 자격 증명 모음 계수](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/data-explorer/kusto/query/distinctoperator) |[스토리지를 포함하는 리소스 표시](../samples/starter.md#show-storage) | |
|[extend](/azure/data-explorer/kusto/query/extendoperator) |[OS 유형별 가상 머신 개수 계산](../samples/starter.md#count-os) | |
|[join](/azure/data-explorer/kusto/query/joinoperator) |[구독 이름이 있는 키 자격 증명 모음](../samples/advanced.md#join) |지원되는 조인 버전: [innerunique](/azure/data-explorer/kusto/query/joinoperator#default-join-flavor), [inner](/azure/data-explorer/kusto/query/joinoperator#inner-join), [leftouter](/azure/data-explorer/kusto/query/joinoperator#left-outer-join). 단일 쿼리에서 3개의 `join` 제한이 있으며, 그 중 1개는 크로스 테이블 `join`일 수 있습니다. 모든 테이블 간 `join` 사용이 _Resource_ 와 _ResourceContainers_ 사이에 있는 경우 3 개의 크로스 테이블 `join`이 허용됩니다. 브로드캐스트 조인과 같은 사용자 지정 조인 전략은 허용되지 않습니다. `join`을 사용할 수 있는 테이블에 대해서는 [Resource Graph 테이블](#resource-graph-tables)을 참조하세요. |
|[limit](/azure/data-explorer/kusto/query/limitoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |`take`의 동의어입니다. [Skip](./work-with-data.md#skipping-records)에서 작동하지 않습니다. |
|[mvexpand](/azure/data-explorer/kusto/query/mvexpandoperator) | | 레거시 연산자. `mv-expand`를 대신 사용합니다. _RowLimit_. 최댓값은 400입니다. 기본값은 128입니다. |
|[mv-expand](/azure/data-explorer/kusto/query/mvexpandoperator) |[특정 쓰기 위치를 사용하여 Cosmos DB 나열](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_. 최댓값은 400입니다. 기본값은 128입니다. 단일 쿼리의 `mv-expand`은 2개로 제한됩니다.|
|[order](/azure/data-explorer/kusto/query/orderoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) |`sort`의 동의어 |
|[parse](/azure/data-explorer/kusto/query/parseoperator) |[네트워크 인터페이스의 가상 네트워크 및 서브넷 가져오기](../samples/advanced.md#parse-subnets) |속성이 있는 경우 `parse`를 사용하는 대신 속성에 직접 액세스하는 것이 적합합니다. |
|[project](/azure/data-explorer/kusto/query/projectoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) | |
|[project-away](/azure/data-explorer/kusto/query/projectawayoperator) |[결과에서 열 제거](../samples/advanced.md#remove-column) | |
|[sort](/azure/data-explorer/kusto/query/sortoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) |`order`의 동의어 |
|[summarize](/azure/data-explorer/kusto/query/summarizeoperator) |[Azure 리소스 개수 계산](../samples/starter.md#count-resources) |간소화된 첫 페이지만 |
|[take](/azure/data-explorer/kusto/query/takeoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |`limit`의 동의어입니다. [Skip](./work-with-data.md#skipping-records)에서 작동하지 않습니다. |
|[top](/azure/data-explorer/kusto/query/topoperator) |[이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시](../samples/starter.md#show-sorted) | |
|[union](/azure/data-explorer/kusto/query/unionoperator) |[두 쿼리의 결과를 단일 결과로 결합](../samples/advanced.md#unionresults) |단일 테이블 허용: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. 단일 쿼리의 `union` 레그는 3개로 제한됩니다. `union` 레그 테이블의 유사 항목 확인은 허용되지 않습니다. 단일 테이블 내에서 사용하거나 _Resources_ 테이블과 _ResourceContainers_ 테이블 간에 사용할 수 있습니다. |
|[where](/azure/data-explorer/kusto/query/whereoperator) |[스토리지를 포함하는 리소스 표시](../samples/starter.md#show-storage) | |

단일 리소스 그래프 SDK 쿼리의 기본 제한은 3개의 `join` 및 3개의 `mv-expand` 연산자입니다. **도움말 + 지원** 을 통해 테넌트에 대한 이 제한을 늘리도록 요청할 수 있습니다.

"쿼리 열기" 포털 환경을 지원하기 위해 Azure Resource Graph 탐색기는 Resource Graph SDK보다 전역 제한이 높습니다.

## <a name="query-scope"></a>쿼리 범위

쿼리에서 리소스가 반환되는 구독 또는 [관리 그룹](../../management-groups/overview.md)의 범위는 기본적으로 권한 있는 사용자의 컨텍스트에 따라 구독 목록으로 설정됩니다. 관리 그룹 또는 구독 목록이 정의되지 않은 경우 쿼리 범위는 [Azure Lighthouse](../../../lighthouse/overview.md) 위임 리소스를 포함하는 모든 리소스입니다.

쿼리할 구독 또는 관리 그룹 목록을 수동으로 정의하여 결과의 범위를 변경할 수 있습니다. 예를 들어 REST API `managementGroups` 속성은 관리 그룹의 이름과 다른 관리 그룹 ID를 사용합니다. `managementGroups`가 지정되면 지정된 관리 그룹 계층 구조 내 또는 아래에 있는 처음 5,000개 구독의 리소스가 포함됩니다. `managementGroups`는 `subscriptions`와 동시에 사용할 수 없습니다.

예: ID가 'myMG'인 'My Management Group'이라는 관리 그룹의 계층 내에 있는 모든 리소스를 쿼리합니다.

- REST API URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2021-03-01
  ```

- 요청 본문

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroups": ["myMG"]
  }
  ```

## <a name="escape-characters"></a>이스케이프 문자

`.` 또는 `$`를 포함하는 일부 속성 이름은 쿼리에서 래핑하거나 이스케이프해야 합니다. 그렇지 않으면 속성 이름이 올바르게 해석되고 올바른 결과를 제공하지 않습니다.

- `.` - `['propertyname.withaperiod']`처럼 속성 이름을 래핑합니다.

  _odata.type_ 속성을 래핑하는 예제 쿼리:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - 속성 이름에서 문자를 이스케이프합니다. 사용되는 이스케이프 문자는 Resource Graph가 실행되는 셸에 따라 다릅니다.

  - **bash** - `\`

    Bash에서 속성 _\$type_ 을 이스케이프하는 예제 쿼리:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - `$` 문자를 이스케이프하지 않습니다.

  - **PowerShell** - ``` ` ```

    PowerShell에서 속성 _\$type_ 을 이스케이프하는 예제 쿼리:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어를 확인합니다.
- [고급 쿼리](../samples/advanced.md)의 고급 사용법을 확인합니다.
- [리소스 검색](explore-resources.md) 방법에 대해 자세히 알아보기
