---
title: Azure 메시징 서비스 - Service Manager에서 Resource Manager로
description: 이 문서에서는 사용되지 않는 Azure Service Manager REST API 및 PowerShell cmdlet을 Resource Manager REST API 및 PowerShell cmdlet으로 매핑을 설명합니다.
ms.topic: article
ms.date: 08/31/2021
ms.openlocfilehash: fc64aa41ab0ae2b0ecec735c9b9d19575dd9950e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136218"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Azure Service Bus, Relay 및 Event Hubs에 대한 Azure Service Manager 지원 중단

차세대 클라우드 인프라 스택인 Resource Manager는 “클래식" Azure 서비스 관리 모델(클래식 배포 모델)을 완전히 대체합니다. 따라서 클래식 배포 모델 Service Bus, 릴레이 및 Event Hubs에 대 한 REST api는 2021 년 12 월 1 일에 사용 중지 됩니다. 이 사용 중단은 [Microsoft 기술 커뮤니티 알림](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)에서 처음 발표되었습니다. 쉽게 식별하기 위해 이러한 API는 URI에 `management.core.windows.net`이 포함되어 있습니다. 사용되지 않는 API 및 현재 사용해야 하는 Azure Resource Manager API 버전 목록은 다음 표를 참조하세요.

Service Bus, 릴레이 및 Event Hubs를 계속 사용 하려면 2021 년 11 월 30 일에 리소스 관리자로 이동 합니다. 이전 API를 사용하는 모든 고객은 리소스 그룹화, 태그, 간소화된 배포 및 관리 프로세스, Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하는 세분화된 액세스 제어를 포함하는 Resource Manager의 추가 이점을 활용하기 위해 곧 전환하는 것이 좋습니다.

Azure Resource Manager와 Azure Service Manager를 비교한 자세한 내용은 [TechNet 블로그](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)를 참조하세요.

Azure Service Bus, Relay, Event Hubs를 위한 Service Manager와 Resource Manager API에 대한 자세한 내용은 REST API 설명서를 참조하세요.

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Service Manager REST API - Resource Manager REST API

| Service Manager API(사용되지 않음) | Resource Manager - Service Bus API | Resource Manager - Event Hub API | Resource Manager - Relay API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Namespaces-GetNamespaceAsync** <br/>[Service Bus Get Namespace](/rest/api/servicebus/get-namespace)<br/>[Event Hub Get Namespace](/rest/api/eventhub/get-event-hub)<br/>[Relay Get Namespace](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Service Bus/Event Hub/Relay GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/stable/namespaces-authorization-rules/list-keys) | [listkeys](/rest/api/eventhub/stable/authorization-rules-event-hubs/list-keys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Topics-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/stable/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Queues-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/stable/queues/get) | &nbsp; | &nbsp; | 
| **Relays-GetRelaysAsync**<br/>[Get Relays](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Service Bus/Event Hub/Relay GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/stable/namespaces-authorization-rules/get-authorization-rule) | [getauthorizationrule](/rest/api/eventhub/stable/authorization-rules-namespaces/get-authorization-rule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Namespaces-DeleteNamespaceAsync**<br/>[Service Bus Delete Namespace](/rest/api/servicebus/delete-namespace)<br/>[Event Hubs Delete Namespace](/rest/api/eventhub/delete-event-hub)<br/>[Relays Delete Namespace](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/stable/namespaces/delete) | [delete](/rest/api/eventhub/stable/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>Service Bus/Event Hub/Relay Get Namespace<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>Service Bus/Event Hub/Relay Get Namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>Service Bus/Event Hub/Relay Get Namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/stable/authorization-rules-event-hubs/create-or-update-authorization-rule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Service Bus/Event Hub/Relay<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/stable/authorization-rules-event-hubs/create-or-update-authorization-rule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[Service Bus Get Namespace](/rest/api/servicebus/get-namespace)<br/>[Event Hubs Get Namespace](/rest/api/eventhub/get-event-hub)<br/>[Relay Get Namespace](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Service Bus/EventHub/Relay Get Namespace<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/stable/regions/listbysku) | [listbysku](/rest/api/eventhub/stable/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>Service Bus/EventHub/Relay<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[List Event Hubs](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/servicebus/stable/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Get Event Hubs](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/get-event-hub) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Service Bus/Event Hub/Relay<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/stable/namespaces-authorization-rules/delete-authorization-rule) | [deleteauthorizationrule](/rest/api/eventhub/stable/authorization-rules-namespaces/delete-authorization-rule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Service Bus/EventHub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/stable/namespaces-authorization-rules/list-authorization-rules) | [listauthorizationrules](/rest/api/eventhub/stable/authorization-rules-namespaces/list-authorization-rules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[Service Bus Namespace Availability](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/stable/namespaces-check-name-availability/check-name-availability) | [checknameavailability](/rest/api/eventhub/stable/check-name-availability-namespaces/check-name-availability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Namespaces-CreateOrUpdateNamespaceAsync**<br/>Service Bus/Event Hub/Relay<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Topics-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/stable/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell - Resource Manager PowerShell
| Service Manager PowerShell 명령(사용되지 않음) | 새 Resource Manager 명령 | 최신 Resource Manager 명령 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure.service/get-azuresblocation) | [Get-AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration) | [Get-AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/get-azuresbnamespace) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/new-azuresbauthorizationrule) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule) | [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/new-azuresbnamespace) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace) | [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace) |
| [Remove-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/remove-azuresbauthorizationrule) | [Remove-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusauthorizationrule) | [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/remove-azuresbnamespace) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) | [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/set-azuresbauthorizationrule) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule) |

## <a name="next-steps"></a>다음 단계
다음 설명서를 참조 하세요. 

- 최신 REST API 설명서
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- 최신 PowerShell 설명서
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/#event_grid)
