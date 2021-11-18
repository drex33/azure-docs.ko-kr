---
title: 리소스 | 따라 Microsoft Sentinel 데이터에 대한 액세스 관리 Microsoft Docs
description: 이 문서에서는 사용자가 액세스할 수 있는 리소스를 통해 Microsoft Sentinel 데이터에 대한 액세스를 관리할 수 있다고 설명합니다. 리소스별 액세스를 관리하면 전체 Microsoft Sentinel 환경 없이 특정 데이터에만 액세스할 수 있습니다. 이 메서드를 리소스 컨텍스트 RBAC라고도 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: b890d0c0b4975a015d6a039dd1d0c313ed841d9c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712265"
---
# <a name="manage-access-to-microsoft-sentinel-data-by-resource"></a>리소스별 Microsoft Sentinel 데이터에 대한 액세스 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

일반적으로 Microsoft Sentinel 작업 영역에 액세스할 수 있는 사용자는 보안 콘텐츠를 포함하여 모든 작업 영역 데이터에 액세스할 수 있습니다. 관리자는 Azure [역할을](roles.md) 사용하여 팀의 액세스 요구 사항에 따라 Microsoft Sentinel의 특정 기능에 대한 액세스를 구성할 수 있습니다.

그러나 Microsoft Sentinel 작업 영역의 특정 데이터에만 액세스해야 하지만 전체 Microsoft Sentinel 환경에 대한 액세스 권한이 없어야 하는 일부 사용자가 있을 수 있습니다. 예를 들어 소유하고 있는 서버에 대한 Windows 이벤트 데이터에 액세스할 수 있는 비-보안 작업(비 SOC) 팀을 제공하려고 할 수 있습니다.

이러한 경우 Microsoft Sentinel 작업 영역 또는 특정 Microsoft Sentinel 기능에 대한 액세스 권한을 제공하는 대신 사용자에게 허용되는 리소스에 따라 RBAC(역할 기반 액세스 제어)를 구성하는 것이 좋습니다. 이 방법은 **리소스 컨텍스트 RBAC** 설정이라고도 합니다.

사용자가 Microsoft Sentinel 작업 영역 대신 액세스할 수 있는 리소스를 통해 Microsoft Sentinel 데이터에 액세스할 수 있는 경우 다음 방법을 사용하여 로그 및 통합 문서를 볼 수 있습니다.

- Azure Virtual Machine과 같은 **리소스 자체를 통해**. 이 방법을 사용하면 특정 리소스에 대한 로그 및 통합 문서만 볼 수 있습니다.

- **Azure Monitor를 통해**. 여러 리소스 및/또는 리소스 그룹에 걸친 쿼리를 만들려는 경우 이 방법을 사용합니다. Azure Monitor에서 로그 및 통합 문서를 탐색하는 경우 하나 이상의 특정 리소스 그룹 또는 리소스에 대한 범위를 정의합니다.

Azure Monitor에서 리소스 컨텍스트 RBAC를 사용하도록 설정합니다. 자세한 내용은 [Azure Monitor에서 로그 데이터 및 작업 영역에 대한 액세스 관리](../azure-monitor/logs/manage-access.md)를 참조하세요.

> [!NOTE]
> 데이터가 Syslog, CEF 또는 AAD 데이터와 같은 Azure 리소스가 아니거나 사용자 지정 수집기가 수집한 데이터가 아닌 경우 데이터를 식별하고 액세스를 사용하도록 설정하는 데 사용되는 리소스 ID를 수동으로 구성해야 합니다. 자세한 내용은 [리소스 컨텍스트 RBAC를 명시적 구성](#explicitly-configure-resource-context-rbac)을 참조하세요.
>
> 또한 리소스 중심 컨텍스트에서는 [함수](../azure-monitor/logs/functions.md) 및 저장된 검색이 지원되지 않습니다. 따라서 구문 분석 및 [정규화와](normalization.md) 같은 Microsoft Sentinel 기능은 Microsoft Sentinel의 리소스 컨텍스트 RBAC에 대해 지원되지 않습니다.
> 

## <a name="scenarios-for-resource-context-rbac"></a>리소스 컨텍스트 RBAC에 대한 시나리오

다음 표에서는 리소스 컨텍스트 RBAC가 가장 유용한 시나리오를 강조 표시합니다. SOC 팀과 비 SOC 팀 간의 액세스 요구 사항에 대한 차이점을 확인합니다.

| 요구 사항 유형 |SOC 팀  |비 SOC 팀  |
|---------|---------|---------|
|**권한**     | 전체 작업 영역        |   특정 리소스 전용      |
|**데이터 액세스**     |  작업 영역의 모든 데이터       | 팀이 액세스할 수 있는 리소스에 대한 데이터만        |
|**환경**     |  사용자에게 할당된 [기능 권한으로](roles.md) 제한될 수 있는 전체 Microsoft Sentinel 환경       |  로그 쿼리 및 통합 문서만       |
|     |         |         |

팀이 위의 표에 설명된 비 SOC 팀에 대한 유사한 액세스 요구 사항이 있는 경우 리소스 컨텍스트 RBAC가 조직에 적합한 솔루션일 수 있습니다.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>리소스 컨텍스트 RBAC 구현에 대한 대체 방법

조직에 필요한 권한에 따라 리소스 컨텍스트 RBAC를 사용하여 전체 솔루션을 제공하지 않을 수 있습니다.

다음 목록에서는 데이터 액세스에 대한 다른 솔루션이 요구 사항에 더 적합할 수 있는 시나리오를 설명합니다.

|시나리오  |솔루션  |
|---------|---------|
|**자회사에는 전체 Microsoft Sentinel 환경이 필요한 SOC 팀이** 있습니다.     |  이 경우 다중 작업 영역 아키텍처를 사용하여 데이터 권한을 구분합니다. <br><br>자세한 내용은 다음을 참조하세요. <br>- [작업 영역 및 테넌트 간에 Microsoft Sentinel 확장](extend-sentinel-across-workspaces-tenants.md)<br>    - [한 번에 여러 작업 영역에서 인시던트 작업](multiple-workspace-view.md)          |
|**특정 유형의 이벤트에 대한 액세스를 제공하려고 합니다**.     |  예를 들어 Windows 관리자에게 모든 시스템의 Windows 보안 이벤트에 대한 액세스 권한을 제공합니다. <br><br>이러한 경우 [테이블 수준 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)를 사용하여 각 테이블에 대한 권한을 정의합니다.       |
| **리소스를 기반으로 하지 않거나 이벤트에 있는 필드의 하위 집합으로만 액세스를 보다 세부적인 수준으로 제한합니다**.   |   예를 들어 사용자의 자회사에 따라 Office 365 로그에 대한 액세스를 제한할 수 있습니다. <br><br>이 경우 [Power BI 대시보드 및 보고서](../azure-monitor/logs/log-powerbi.md)와의 기본 제공 통합을 사용하여 데이터에 대한 액세스를 제공합니다.      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>리소스 컨텍스트 RBAC를 명시적으로 구성

리소스 컨텍스트 RBAC를 구성하려는 경우에는 다음 단계를 사용합니다. 그러나 데이터는 Azure 리소스가 아닙니다.

예를 들어 Azure 리소스가 아닌 Microsoft Sentinel 작업 영역의 데이터에는 Syslog, CEF 또는 AAD 데이터 또는 사용자 지정 수집기에서 수집한 데이터가 포함됩니다.

**리소스 컨텍스트 RBAC를 명시적으로 구성하려면**:

1. Azure Monitor에서 [리소스 컨텍스트 RBAC를 사용하도록 설정](../azure-monitor/logs/manage-access.md)했는지 확인합니다. 

1. 전체 Microsoft Sentinel 환경 없이 [리소스에](../azure-resource-manager/management/manage-resource-groups-portal.md) 액세스해야 하는 각 사용자 팀에 대한 리소스 그룹을 만듭니다.

    각 팀 멤버에 대해 [로그 읽기 권한자 권한](../azure-monitor/logs/manage-access.md#resource-permissions)을 할당합니다.

1. 만든 리소스 팀 그룹에 리소스를 할당하고 관련 리소스 ID로 이벤트에 태그를 지정합니다.

    Azure 리소스가 Microsoft Sentinel에 데이터를 보내면 로그 레코드에 데이터 원본의 리소스 ID로 자동으로 태그가 지정됩니다.

    > [!TIP]
    > 해당 목적으로 생성된 특정 리소스 그룹에서 액세스 권한을 부여하는 리소스를 그룹화하는 것이 좋습니다.
    >
    > 그룹화할 수 없는 경우 팀에 액세스하려는 리소스에 대한 로그 읽기 권한자 권한이 있는지 확인합니다.
    >

    리소스 ID에 대한 자세한 내용은 다음을 참조하세요.

    - [로그 전달을 사용하는 리소스 ID](#resource-ids-with-log-forwarding)
    - [Logstash 컬렉션을 사용하는 리소스 ID](#resource-ids-with-logstash-collection)
    - [Log Analytics API 컬렉션을 사용하는 리소스 ID](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>로그 전달을 사용하는 리소스 ID

[CEF(Common Event Format)](connect-common-event-format.md) 또는 [Syslog](connect-syslog.md)를 사용하여 이벤트를 수집하는 경우 로그 전달은 여러 원본 시스템에서 이벤트를 수집하는 데 사용됩니다.

예를 들어 CEF 또는 Syslog 전달 VM이 Syslog 이벤트를 보내는 원본을 수신 대기하고 Microsoft Sentinel에 전달하는 경우 로그 전달 VM 리소스 ID가 전달되는 모든 이벤트에 할당됩니다.

여러 팀이 있는 경우 별도의 각 팀에 대한 이벤트를 처리하는 별도의 로그 전달 VM이 있는지 확인합니다.

예를 들어 VM을 분리하면 팀 A에 속한 Syslog 이벤트가 수집기 VM A를 사용하여 수집됩니다.

> [!TIP]
> - 온-프레미스 VM 또는 다른 클라우드 VM(예: AWS)을 로그 전달자로 사용하는 경우 [Azure Arc](../azure-arc/servers/overview.md)를 구현하여 리소스 ID가 있는지 확인합니다.
> - 로그 전달 VM 환경의 크기를 조정하려면 CEF 및 Sylog 로그를 수집하는 [VM 확장 집합](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)을 만드는 것이 좋습니다.


### <a name="resource-ids-with-logstash-collection"></a>Logstash 컬렉션을 사용하는 리소스 ID

Microsoft Sentinel [Logstash 출력 플러그 인을](connect-logstash.md)사용하여 데이터를 수집하는 경우 **azure_resource_id** 필드를 사용하여 출력에 리소스 ID를 포함하도록 사용자 지정 수집기를 구성합니다.

리소스 컨텍스트 RBAC를 사용하고 API를 통해 수집되는 이벤트를 특정 사용자가 사용할 수 있도록 하려면 [사용자에 대해 만든](#explicitly-configure-resource-context-rbac) 리소스 그룹의 리소스 ID를 사용합니다.

예를 들어 다음 코드는 샘플 Logstash 구성 파일을 보여 줍니다.

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> 여러 이벤트에 적용된 태그를 구별하기 위해 여러 `output` 섹션을 추가할 수 있습니다.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Log Analytics API 컬렉션을 사용하는 리소스 ID

[Log Analytics 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 사용하여 수집하는 경우 HTTP [*AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers) 요청 헤더를 사용하여 리소스 ID가 있는 이벤트에 할당할 수 있습니다.

리소스 컨텍스트 RBAC를 사용하고 API를 통해 수집되는 이벤트를 특정 사용자가 사용할 수 있도록 하려면 [사용자에 대해 만든](#explicitly-configure-resource-context-rbac) 리소스 그룹의 리소스 ID를 사용합니다.



## <a name="next-steps"></a>다음 단계

자세한 내용은 [Microsoft Sentinel의 사용 권한을 참조하세요.](roles.md)
