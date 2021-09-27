---
title: 800개 개수 제한이 없는 리소스
description: 리소스 그룹에 800개가 넘는 인스턴스를 포함할 수 있는 Azure 리소스 종류를 나열합니다.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 123148a863f74a6490a524ecc2d39bc44e65299d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807046"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>리소스 그룹당 인스턴스가 800개로 제한되지 않는 리소스

기본적으로 각 리소스 그룹에 리소스 종류의 인스턴스를 최대 800개까지 배포할 수 있습니다. 하지만 일부 리소스 종류는 800개 인스턴스 제한에서 제외됩니다. 이 문서에서는 리소스 그룹에 800개가 넘는 인스턴스를 포함할 수 있는 Azure 리소스 종류를 나열합니다. 다른 모든 리소스 종류는 인스턴스가 800개로 제한됩니다.

일부 리소스 종류의 경우 800개 인스턴스 제한을 제거하려면 고객 지원팀에 문의해야 합니다. 해당 리소스 종류는 이 문서에 나와 있습니다.

일부 리소스는 지역별 number instances에 대 한 제한이 있습니다. 이 제한은 리소스 그룹당 800 인스턴스와 다릅니다. 지역별 인스턴스를 확인 하려면 Azure Portal을 사용 합니다. 왼쪽 창에서 구독 및 **사용량 + 할당량** 을 선택 합니다. 자세한 내용은 [제한에 대 한 리소스 사용 확인](../../networking/check-usage-against-limits.md)을 참조 하세요. 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* linkedSubscriptions
* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - 기본적으로 인스턴스가 800개로 제한됩니다. 고객 지원팀에 문의하여 한도를 늘릴 수 있습니다.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* 스냅샷
* virtualMachineScaleSets - 기본값으로 인스턴스가 800개로 제한됩니다. 고객 지원팀에 문의하여 한도를 늘릴 수 있습니다.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* 인스턴스

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* serverGroupsv2
* servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* schedules

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* 네임스페이스

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* 머신 - 최대 5,000개의 인스턴스 지원
* 컴퓨터/확장 - VM 확장 인스턴스를 무제한으로 지원합니다.

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts
* scheduledqueryrules

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots
* netAppAccounts/snapshotPolicies
* netAppAccounts/volumeGroups

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - 기본적으로 인스턴스가 800개로 제한됩니다. 고객 지원팀에 문의하여 한도를 늘릴 수 있습니다.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* autoScaleVCores - 기본적으로 800개 인스턴스로 제한됩니다. 고객 지원팀에 문의하여 한도를 늘릴 수 있습니다.
* 용량 - 기본적으로 인스턴스가 800개로 제한됩니다. 고객 지원팀에 문의하여 한도를 늘릴 수 있습니다.

## <a name="microsoftrelay"></a>Microsoft.Relay

* 네임스페이스

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* 네임스페이스

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* 계정
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* accounts/jobs
* accounts/models
* accounts/storageContainers

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

* streamingjobs - 기본적으로 인스턴스가 800개로 제한됩니다. 고객 지원팀에 문의하여 한도를 늘릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

할당량 및 한도의 전체 목록은 [Azure 구독 및 서비스 한도, 할당량, 제약 조건](azure-subscription-service-limits.md)을 참조하세요.
