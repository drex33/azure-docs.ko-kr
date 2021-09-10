---
title: 리소스 종류별 이동 작업 지원
description: 새 리소스 그룹, 구독 또는 지역으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 614ef51474f789073f35e722733d255818bf3957
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260884"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원

이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해야 할 특수 조건에 대한 정보도 제공합니다.

> [!IMPORTANT]
> 대부분의 경우 자식 리소스는 해당 부모 리소스로부터 독립적으로 이동할 수 없습니다. 자식 리소스에는 `<resource-provider-namespace>/<parent-resource>/<child-resource>` 형식의 리소스 종류가 있습니다. 예를 들어, `Microsoft.ServiceBus/namespaces/queues`는 `Microsoft.ServiceBus/namespaces`의 자식 리소스입니다. 부모 리소스를 이동하면 자식 리소스가 부모 리소스와 함께 자동으로 이동됩니다. 이 문서에 자식 리소스가 보이지 않는 경우 부모 리소스와 함께 이동한 것으로 가정할 수 있습니다. 부모 리소스가 이동을 지원하지 않는 경우 자식 리소스를 이동할 수 없습니다.

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTHub](#microsoftiothub)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | 아니요 | 아니요 |  아니요 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | 아니요 | 아니요 | 아니요 |
> | diagnosticsettingscategories | 아니요 | 아니요 | 아니요 |
> | privatelinkforazuread | 예 | 예 | 아니요 |
> | tenants | 예 | 예 | 아니요 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | 아니요 | 아니요 | 아니요 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | 아니요 | 아니요 | 아니요 |
> | addsservices | 아니요 | 아니요 | 아니요 |
> | agents | 아니요 | 아니요 | 아니요 |
> | anonymousapiusers | 아니요 | 아니요 | 아니요 |
> | 구성 | 아니요 | 아니요 | 아니요 |
> | 로그 | 아니요 | 아니요 | 아니요 |
> | reports | 아니요 | 아니요 | 아니요 |
> | servicehealthmetrics | 아니요 | 아니요 | 아니요 |
> | services | 아니요 | 아니요 | 아니요 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 구성 | 아니요 | 아니요 | 아니요 |
> | generaterecommendations | 아니요 | 아니요 | 아니요 |
> | metadata | 아니요 | 아니요 | 아니요 |
> | 동영상 추천 기능 | 아니요 | 아니요 | 아니요 |
> | suppressions | 아니요 | 아니요 | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | 예 | 예 | 아니요 |
> | 경고 | 아니요 | 아니요 | 아니요 |
> | alertslist | 아니요 | 아니요 | 아니요 |
> | alertsmetadata | 아니요 | 아니요 | 아니요 |
> | alertssummary | 아니요 | 아니요 | 아니요 |
> | alertssummarylist | 아니요 | 아니요 | 아니요 |
> | smartdetectoralertrules | 예 | 예 | 아니요 |
> | smartgroups | 아니요 | 아니요 | 아니요 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | 예 | 예 | 아니요 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> 소비 SKU로 설정된 API Management 서비스는 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | 아니요 | 아니요 | 아니요 |
> | 서비스 | 예 | 예 | 예(템플릿 사용) <br/><br/> [지역 간에 API Management를 이동합니다](../../api-management/api-management-howto-migrate.md). |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | 예 | 예 | 아니요 |
> | configurationstores / eventgridfilters | 아니요 | 아니요 | 아니요 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | spring | 예 | 예 | 아니요 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | 아니요 | 아니요 | 예(템플릿 사용)<br/><br/> [App Service 앱을 다른 지역으로 이동](../../app-service/manage-move-across-regions.md) |
> | appidentities | 아니요 | 아니요 | 아니요 |
> | gateways | 아니요 | 아니요 | 아니요 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | 예 | 예 | 아니요 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | 아니요 | 아니요 | 아니요 |
> | dataaliases | 아니요 | 아니요 | 아니요 |
> | denyassignments | 아니요 | 아니요 | 아니요 |
> | elevateaccess | 아니요 | 아니요 | 아니요 |
> | findorphanroleassignments | 아니요 | 아니요 | 아니요 |
> | locks | 아니요 | 아니요 | 아니요 |
> | 권한 | 아니요 | 아니요 | 아니요 |
> | policyassignments | 아니요 | 아니요 | 아니요 |
> | policydefinitions | 아니요 | 아니요 | 아니요 |
> | policysetdefinitions | 아니요 | 아니요 | 아니요 |
> | privatelinkassociations | 아니요 | 아니요 | 아니요 |
> | resourcemanagementprivatelinks | 아니요 | 아니요 | 아니요 |
> | roleassignments | 아니요 | 아니요 | 아니요 |
> | roleassignmentsusagemetrics | 아니요 | 아니요 | 아니요 |
> | roledefinitions | 아니요 | 아니요 | 아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.
>
> 자세한 내용은 [Azure Automation 계정을 다른 구독으로 이동](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | 예 | 예 | 예(템플릿 사용) <br/><br/> [지역 복제 사용](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts / configurations | 예 | 예 | 아니요 |
> | automationaccounts / runbooks | 예 | 예 | 아니요 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | 예 | 예 | 아니요 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | 예 | 예 | 아니요 |
> | b2ctenants | 아니요 | 아니요 | 아니요 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers | 아니요 | 아니요 | 아니요 |
> | hybriddatamanagers | 아니요 | 아니요 | 아니요 |
> | postgresinstances | 아니요 | 아니요 | 아니요 |
> | sqlinstances | 아니요 | 아니요 | 아니요 |
> | sqlmanagedinstances | 아니요 | 아니요 | 아니요 |
> | sqlserverinstances | 아니요 | 아니요 | 아니요 |
> | sqlserverregistrations | 예 | 예 | 아니요 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | 아니요 | 아니요 | 아니요 |
> | registrations | 예 | 예 | 아니요 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | 아니요 | 아니요 | 아니요 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | 예 | 예 | 배치 계정은 한 지역에서 다른 지역으로 직접 이동할 수 없지만 템플릿을 사용하여 템플릿을 내보내고 수정하고 새 지역에 템플릿을 배포할 수 있습니다. <br/><br/> [지역 간 배치 계정 이동](../../batch/account-move.md)에 대해 알아보기 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts | 아니요 | 아니요 | 아니요 |
> | billingperiods | 아니요 | 아니요 | 아니요 |
> | billingpermissions | 아니요 | 아니요 | 아니요 |
> | billingproperty | 아니요 | 아니요 | 아니요 |
> | billingroleassignments | 아니요 | 아니요 | 아니요 |
> | billingroledefinitions | 아니요 | 아니요 | 아니요 |
> | departments | 아니요 | 아니요 | 아니요 |
> | enrollmentaccounts | 아니요 | 아니요 | 아니요 |
> | invoices | 아니요 | 아니요 | 아니요 |
> | transfers | 아니요 | 아니요 | 아니요 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | 아니요 | 아니요 | 아니요 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | 아니요 | 아니요 | 아니요 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | 아니요 | 아니요 | 아니요 <br/><br/> 블록체인 네트워크는 다른 지역에 노드를 포함할 수 없습니다. |
> | cordamembers | 아니요 | 아니요 | 아니요 |
> | watchers | 아니요 | 아니요 | 아니요 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | 아니요 | 아니요 | 아니요 |
> | blueprints | 아니요 | 아니요 | 아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | 예 | 예 | 아니요 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용하여 구성되면 이 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한](./move-limitations/networking-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | redis | 예 | 예 | 아니요 |
> | redisenterprise | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | 아니요 | 아니요 | 아니요 |
> | calculateexchange | 아니요 | 아니요 | 아니요 |
> | calculateprice | 아니요 | 아니요 | 아니요 |
> | calculatepurchaseprice | 아니요 | 아니요 | 아니요 |
> | catalogs | 아니요 | 아니요 | 아니요 |
> | commercialreservationorders | 아니요 | 아니요 | 아니요 |
> | 교환 | 아니요 | 아니요 | 아니요 |
> | reservationorders | 아니요 | 아니요 | 아니요 |
> | reservations | 아니요 | 아니요 | 아니요 |
> | 리소스 | 아니요 | 아니요 | 아니요 |
> | validatereservationorder | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | 아니요 | 아니요 | 아니요 |
> | cdnwebapplicationfirewallpolicies | 예 | 예 | 아니요 |
> | edgenodes | 아니요 | 아니요 | 아니요 |
> | 프로필 | 예 | 예 | 아니요 |
> | profiles / endpoints | 예 | 예 | 아니요 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | 예 | 예 | 아니요 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | 아니요 | 아니요 | 아니요 |
> | domainnames | 예 | 아니요 | 아니요 |
> | quotas | 아니요 | 아니요 | 아니요 |
> | resourcetypes | 아니요 | 아니요 | 아니요 |
> | validatesubscriptionmoveavailability | 아니요 | 아니요 | 아니요 |
> | virtualmachines | 예 | 예 | 아니요 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | 아니요 | 아니요 | 아니요 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | 아니요 | 아니요 | 아니요 |
> | expressroutecrossconnections | 아니요 | 아니요 | 아니요 |
> | expressroutecrossconnections / peerings | 아니요 | 아니요 | 아니요 |
> | gatewaysupporteddevices | 아니요 | 아니요 | 아니요 |
> | networksecuritygroups | 아니요 | 아니요 | 아니요 |
> | quotas | 아니요 | 아니요 | 아니요 |
> | reservedips | 아니요 | 아니요 | 아니요 |
> | virtualnetworks | 아니요 | 아니요 | 아니요 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | disks | 아니요 | 아니요 | 아니요 |
> | images | 아니요 | 아니요 | 아니요 |
> | osimages | 아니요 | 아니요 | 아니요 |
> | osplatformimages | 아니요 | 아니요 | 아니요 |
> | publicimages | 아니요 | 아니요 | 아니요 |
> | quotas | 아니요 | 아니요 | 아니요 |
> | storageaccounts | 예 | 아니요 | 예 |
> | vmimages | 아니요 | 아니요 | 아니요 |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 작업 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 예 | 예 | 아니요 |
> | Cognitive Search | 예 | 예 | 수동 단계를 통해 지원됩니다.<br/><br/> [Azure Cognitive Search 서비스를 다른 지역으로 이동](../../search/search-howto-move-across-regions.md)에 대해 알아보기 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | 아니요 | 아니요 | 아니요 |
> | usageaggregates | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> [Virtual Machines 이동 지침](./move-limitations/virtual-machines-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | 예 | 예 |  예 <br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 가용성 집합을 이동합니다. |
> | diskaccesses | 아니요 | 아니요 | 아니요 |
> | diskencryptionsets | 아니요 | 아니요 | 아니요 |
> | disks | 예 | 예 | 예 <br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 Azure VM 및 관련 디스크를 이동합니다. |
> | galleries | 아니요 | 아니요 | 아니요 |
> | galleries / images | 아니요 | 아니요 | 아니요 |
> | galleries / images / versions | 아니요 | 아니요 | 아니요 |
> | hostgroups | 아니요 | 아니요 | 아니요 |
> | hostgroups / hosts | 아니요 | 아니요 | 아니요 |
> | images | 예 | 예 | 아니요 |
> | proximityplacementgroups | 예 | 예 | 아니요 |
> | restorepointcollections | 아니요 | 아니요 | 아니요 |
> | restorepointcollections / restorepoints | 아니요 | 아니요 | 아니요 |
> | sharedvmextensions | 아니요 | 아니요 | 아니요 |
> | sharedvmimages | 아니요 | 아니요 | 아니요 |
> | sharedvmimages / versions | 아니요 | 아니요 | 아니요 |
> | 스냅샷 | 예 | 예 | 아니요 |
> | sshpublickeys | 아니요 | 아니요 | 아니요 |
> | virtualmachines | 예 | 예 | 예 <br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 Azure VM을 이동합니다. |
> | virtualmachines / extensions | 예 | 예 | 아니요 |
> | virtualmachinescalesets | 예 | 예 | 아니요 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | 아니요 | 아니요 | 아니요 |
> | balances | 아니요 | 아니요 | 아니요 |
> | budgets | 아니요 | 아니요 | 아니요 |
> | charges | 아니요 | 아니요 | 아니요 |
> | costtags | 아니요 | 아니요 | 아니요 |
> | credits | 아니요 | 아니요 | 아니요 |
> | events | 아니요 | 아니요 | 아니요 |
> | forecasts | 아니요 | 아니요 | 아니요 |
> | lots | 아니요 | 아니요 | 아니요 |
> | marketplaces | 아니요 | 아니요 | 아니요 |
> | pricesheets | 아니요 | 아니요 | 아니요 |
> | products | 아니요 | 아니요 | 아니요 |
> | reservationdetails | 아니요 | 아니요 | 아니요 |
> | reservationrecommendationdetails | 아니요 | 아니요 | 아니요 |
> | reservationrecommendations | 아니요 | 아니요 | 아니요 |
> | reservationsummaries | 아니요 | 아니요 | 아니요 |
> | reservationtransactions | 아니요 | 아니요 | 아니요 |
> | tags | 아니요 | 아니요 | 아니요 |
> | tenants | 아니요 | 아니요 | 아니요 |
> | terms | 아니요 | 아니요 | 아니요 |
> | usagedetails | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | 아니요 | 아니요 | 아니요 |
> | serviceassociationlinks | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | registries | 예 | 예 | 아니요 |
> | registries / agentpools | 예 | 예 | 아니요 |
> | registries / buildtasks | 예 | 예 | 아니요 |
> | registries / replications | 예 | 예 | 아니요 |
> | registries / tasks | 예 | 예 | 아니요 |
> | registries / webhooks | 예 | 예 | 아니요 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | 아니요 | 아니요 | 아니요 |
> | managedclusters | 아니요 | 아니요 | 아니요 |
> | openshiftmanagedclusters | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 애플리케이션 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 경고 | 아니요 | 아니요 | 아니요 |
> | billingaccounts | 아니요 | 아니요 | 아니요 |
> | budgets | 아니요 | 아니요 | 아니요 |
> | cloudconnectors | 아니요 | 아니요 | 아니요 |
> | 커넥터 | 예 | 예 | 아니요 |
> | departments | 아니요 | 아니요 | 아니요 |
> | dimensions | 아니요 | 아니요 | 아니요 |
> | enrollmentaccounts | 아니요 | 아니요 | 아니요 |
> | exports | 아니요 | 아니요 | 아니요 |
> | externalbillingaccounts | 아니요 | 아니요 | 아니요 |
> | 예측 | 아니요 | 아니요 | 아니요 |
> | Query | 아니요 | 아니요 | 아니요 |
> | register | 아니요 | 아니요 | 아니요 |
> | reportconfigs | 아니요 | 아니요 | 아니요 |
> | reports | 아니요 | 아니요 | 아니요 |
> | 설정 | 아니요 | 아니요 | 아니요 |
> | showbackrules | 아니요 | 아니요 | 아니요 |
> | 뷰 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | hubs | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | requests | 아니요 | 아니요 | 아니요 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | associations | 아니요 | 아니요 | 아니요 |
> | resourceproviders | 예 | 예 | 아니요 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | 아니요 | 아니요 | 아니요 |
> | databoxedgedevices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | catalogs | 예 | 예 | 아니요 |
> | datacatalogs | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 패키지 | 아니요 | 아니요 | 아니요 |
> | 플랜 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | 예 | 예 | 아니요 |
> | factories | 예 | 예 | 아니요 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 예 | 예 | 아니요 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 예 | 예 | 아니요 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | 아니요 | 아니요 | 아니요 |
> | services / projects | 아니요 | 아니요 | 아니요 |
> | slots | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | 아니요 | 아니요 | 아니요 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 예 | 예 | 아니요 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | 예 | 예 | 지역 간 읽기 복제본을 사용하여 기존 서버를 이동할 수 있습니다. [자세히 알아보세요](../../postgresql/howto-move-regions-portal.md).<br/><br/> 지역 중복 백업 스토리지를 사용하여 서비스를 프로비전하는 경우 지역 복원을 사용하여 다른 지역에서 복원할 수 있습니다. [자세히 알아보세요](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | 아니요 | 아니요 | 아니요 |
> | servers | 예 | 예 | 지역 간 읽기 복제본을 사용하여 기존 서버를 이동할 수 있습니다. [자세히 알아보세요](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | 아니요 | 아니요 | 아니요 |
> | servergroups | 아니요 | 아니요 | 아니요 |
> | servers | 예 | 예 | 지역 간 읽기 복제본을 사용하여 기존 서버를 이동할 수 있습니다. [자세히 알아보세요](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | 예 | 예 | 아니요 |
> | singleservers | 예 | 예 | 아니요 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | 예 | 예 | 아니요 |
> | rollouts | 예 | 예 | 아니요 |
> | servicetopologies | 예 | 예 | 아니요 |
> | servicetopologies / services | 예 | 예 | 아니요 |
> | servicetopologies / services / serviceunits | 예 | 예 | 아니요 |
> | 단계 | 예 | 예 | 아니요 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | 예 | 예 | 아니요 |
> | hostpools | 예 | 예 | 아니요 |
> | workspaces | 예 | 예 | 아니요 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | 아니요 | 아니요 | 아니요. 리소스는 노출되지 않습니다. |
> | elasticpools / iothubtenants | 아니요 | 아니요 | 아니요. 리소스는 노출되지 않습니다. |
> | iothubs | 예 | 예 | 예. [자세히 알아보기](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | 예 | 예 | 아니요 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | pipelines | 예 | 예 | 아니요 |
> | controllers | **보류 중** | **보류 중** | 아니요 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | controllers | 예 | 예 | 아니요 |
> | AKS 클러스터 | **보류 중** | **보류 중** | 아니요<br/><br/> 다른 지역으로 이동에 대해 [자세히 알아보세요.](/previous-versions/azure/dev-spaces/)

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | 아니요 | 아니요 | 아니요 |
> | labs | 예 | 아니요 | 아니요 |
> | labs / environments | 예 | 예 | 아니요 |
> | labs / servicerunners | 예 | 예 | 아니요 |
> | labs / virtualmachines | 예 | 아니요 | 아니요 |
> | schedules | 예 | 예 | 아니요 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | 아니요 | 아니요 | 예, 새 지역에서 리소스를 다시 만듭니다. [자세히 알아보기](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | 아니요 | 아니요 | 아니요 |
> | databaseaccounts | 예 | 예 | 아니요 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | 예 | 예 | 아니요 |
> | generatessorequest | 아니요 | 아니요 | 아니요 |
> | topleveldomains | 아니요 | 아니요 | 아니요 |
> | validatedomainregistrationinformation | 아니요 | 아니요 | 아니요 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | 예 | 예 | 아니요 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | 예 | 예 | 아니요 |
> | eventsubscriptions | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. | 아니요 |
> | extensiontopics | 아니요 | 아니요 | 아니요 |
> | partnernamespaces | 예 | 예 | 아니요 |
> | partnerregistrations | 아니요 | 아니요 | 아니요 |
> | partnertopics | 예 | 예 | 아니요 |
> | systemtopics | 예 | 예 | 아니요 |
> | topics | 예 | 예 | 아니요 |
> | topictypes | 아니요 | 아니요 | 아니요 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | 예 | 예 | 아니요 |
> | 네임스페이스 | 예 | 예 | 예(템플릿 있음)<br/><br/> [이벤트 허브 네임스페이스를 다른 지역으로 이동](../../event-hubs/move-across-regions.md) |
> | sku | 아니요 | 아니요 | 아니요 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | 아니요 | 아니요 | 아니요 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 네임스페이스 | 예 | 예 | 아니요 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | 아니요 | 아니요 | 아니요 |
> | 기능 | 아니요 | 아니요 | 아니요 |
> | providers | 아니요 | 아니요 | 아니요 |
> | subscriptionfeatureregistrations | 아니요 | 아니요 | 아니요 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | 아니요 | 아니요 | 아니요 |
> | automanagedvmconfigurationprofiles | 아니요 | 아니요 | 아니요 |
> | guestconfigurationassignments | 아니요 | 아니요 | 아니요 |
> | software | 아니요 | 아니요 | 아니요 |
> | softwareupdateprofile | 아니요 | 아니요 | 아니요 |
> | softwareupdates | 아니요 | 아니요 | 아니요 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | 아니요 | 아니요 | 아니요 |
> | sapmonitors | 아니요 | 아니요 | 아니요 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | 아니요 | 아니요 | 아니요 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | 예 | 예 | 아니요 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | 예 | 예 | 아니요 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | machines | 예 | 예 | 아니요 |
> | machines / extensions | 예 | 예 | 아니요 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | 예 | 예 | 아니요 |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | 아니요 | 아니요 | 아니요 |
> | vnfs | 아니요 | 아니요 | 아니요 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | components | 아니요 | 아니요 | 아니요 |
> | networkscopes | 아니요 | 아니요 | 아니요 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | 예 | 예 | 아니요 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 예 | 예 | 아니요. [자세히 알아보세요](../../azure-monitor/faq.yml#how-do-i-move-an-application-insights-resource-to-a-new-region-). |
> | actiongroups | 예 | 예 | 아니요 |
> | activitylogalerts | 아니요 | 아니요 | 아니요 |
> | alertrules | 예 | 예 | 아니요 |
> | autoscalesettings | 예 | 예 | 아니요 |
> | baseline | 아니요 | 아니요 | 아니요 |
> | components | 예 | 예 | 아니요 |
> | datacollectionrules | 아니요 | 아니요 | 아니요 |
> | diagnosticsettings | 아니요 | 아니요 | 아니요 |
> | diagnosticsettingscategories | 아니요 | 아니요 | 아니요 |
> | eventcategories | 아니요 | 아니요 | 아니요 |
> | eventtypes | 아니요 | 아니요 | 아니요 |
> | extendeddiagnosticsettings | 아니요 | 아니요 | 아니요 |
> | guestdiagnosticsettings | 아니요 | 아니요 | 아니요 |
> | listmigrationdate | 아니요 | 아니요 | 아니요 |
> | logdefinitions | 아니요 | 아니요 | 아니요 |
> | logprofiles | 아니요 | 아니요 | 아니요 |
> | 로그 | 아니요 | 아니요 | 아니요 |
> | metricalerts | 아니요 | 아니요 | 아니요 |
> | metricbaselines | 아니요 | 아니요 | 아니요 |
> | metricbatch | 아니요 | 아니요 | 아니요 |
> | metricdefinitions | 아니요 | 아니요 | 아니요 |
> | metricnamespaces | 아니요 | 아니요 | 아니요 |
> | 메트릭 | 아니요 | 아니요 | 아니요 |
> | migratealertrules | 아니요 | 아니요 | 아니요 |
> | migratetonewpricingmodel | 아니요 | 아니요 | 아니요 |
> | myworkbooks | 아니요 | 아니요 | 아니요 |
> | notificationgroups | 아니요 | 아니요 | 아니요 |
> | privatelinkscopes | 아니요 | 아니요 | 아니요 |
> | rollbacktolegacypricingmodel | 아니요 | 아니요 | 아니요 |
> | scheduledqueryrules | 예 | 예 | 아니요 |
> | 토폴로지 | 아니요 | 아니요 | 아니요 |
> | 트랜잭션 | 아니요 | 아니요 | 아니요 |
> | vminsightsonboardingstatuses | 아니요 | 아니요 | 아니요 |
> | webtests | 예 | 예 | 아니요 |
> | webtests / gettestresultfile | 아니요 | 아니요 | 아니요 |
> | workbooks | 예 | 예 | 아니요 |
> | workbooktemplates | 예 | 예 | 아니요 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | 아니요 | 아니요 | 아니요 |
> | iotapps | 예 | 예 | 아니요 |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | 예 | 예 | 예(허브 복제) <br/><br/> [다른 Azure 지역에 IoT 허브 복제](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | graph | 예 | 예 | 아니요 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> 디스크 암호화에 사용되는 Key Vault는 동일한 구독의 리소스 그룹으로 또는 구독 간에 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | 아니요 | 아니요 | 아니요 |
> | hsmpools | 아니요 | 아니요 | 아니요 |
> | managedhsms | 아니요 | 아니요 | 아니요 |
> | vaults | 예 | 예 | 아니요 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | 예 | 예 | 아니요 |
> | registeredsubscriptions | 아니요 | 아니요 | 아니요 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | 아니요 | 아니요 | 아니요 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | 예 | 예 | 아니요 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | 아니요 | 아니요 | 아니요 |
> | users | 아니요 | 아니요 | 아니요 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 아니요 | 아니요 | 아니요, 글로벌 서비스입니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | 아니요 | 아니요 | 아니요 |
> | integrationaccounts | 예 | 예 | 아니요 |
> | integrationserviceenvironments | 예 | 아니요 | 아니요 |
> | integrationserviceenvironments / managedapis | 예 | 아니요 | 아니요 |
> | isolatedenvironments | 아니요 | 아니요 | 아니요 |
> | workflows | 예 | 예 | 아니요 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | 아니요 | 아니요 | 아니요 |
> | webservices | 예 | 아니요 | 아니요 |
> | workspaces | 예 | 예 | 아니요 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 아니요 | 아니요 | 아니요 |
> | teamaccounts | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | 아니요 | 아니요 | 예. [자세히 알아보기](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | 예 | 예 | 예. [자세히 알아보기](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | ID | 아니요 | 아니요 | 아니요 |
> | userassignedidentities | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | 아니요 | 아니요 | 아니요 |
> | managednetworks / managednetworkgroups | 아니요 | 아니요 | 아니요 |
> | managednetworks / managednetworkpeeringpolicies | 아니요 | 아니요 | 아니요 |
> | 알림 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | 아니요 | 아니요 | 아니요 |
> | registrationassignments | 아니요 | 아니요 | 아니요 |
> | registrationdefinitions | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | 아니요 | 아니요 | 아니요 |
> | managementgroups | 아니요 | 아니요 | 아니요 |
> | managementgroups / settings | 아니요 | 아니요 | 아니요 |
> | 리소스 | 아니요 | 아니요 | 아니요 |
> | starttenantbackfill | 아니요 | 아니요 | 아니요 |
> | tenantbackfillstatus | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 예 | 예 | 아니요, Azure Maps는 지리 공간적 서비스입니다. |
> | accounts / privateatlases | 예 | 예 | 아니요 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | offers | 아니요 | 아니요 | 아니요 |
> | offertypes | 아니요 | 아니요 | 아니요 |
> | privategalleryitems | 아니요 | 아니요 | 아니요 |
> | privatestoreclient | 아니요 | 아니요 | 아니요 |
> | privatestores | 아니요 | 아니요 | 아니요 |
> | products | 아니요 | 아니요 | 아니요 |
> | publishers | 아니요 | 아니요 | 아니요 |
> | register | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | agreements | 아니요 | 아니요 | 아니요 |
> | offertypes | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | 예 | 예 | 아니요 |
> | mediaservices / liveevents | 예 | 예 | 아니요 |
> | mediaservices / streamingendpoints | 예 | 예 | 아니요 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | 아니요 | 아니요 | 아니요 |
> | migrateprojects | 아니요 | 아니요 | 아니요 |
> | movecollections | 아니요 | 아니요 | 아니요 |
> | projects | 아니요 | 아니요 | 아니요 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | 아니요 | 아니요 | 아니요 |
> | objectunderstandingaccounts | 아니요 | 아니요 | 아니요 |
> | remoterenderingaccounts | 예 | 예 | 아니요 |
> | spatialanchorsaccounts | 예 | 예 | 아니요 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | 아니요 | 아니요 | 아니요 |
> | netappaccounts / capacitypools | 아니요 | 아니요 | 아니요 |
> | netappaccounts / capacitypools / volumes | 아니요 | 아니요 | 아니요 |
> | netappaccounts / capacitypools / volumes / mounttargets | 아니요 | 아니요 | 아니요 |
> | netappaccounts / capacitypools / volumes / snapshots | 아니요 | 아니요 | 아니요 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | 아니요 | 아니요 | 아니요 |
> | applicationgatewaywebapplicationfirewallpolicies | 아니요 | 아니요 | 아니요 |
> | applicationsecuritygroups | 예 | 예 | 아니요 |
> | azurefirewalls | 아니요 | 아니요 | 아니요 |
> | bastionhosts | 아니요 | 아니요 | 아니요 |
> | bgpservicecommunities | 아니요 | 아니요 | 아니요 |
> | connections | 예 | 예 | 아니요 |
> | ddoscustompolicies | 예 | 예 | 아니요 |
> | ddosprotectionplans | 아니요 | 아니요 | 아니요 |
> | dnszones | 예 | 예 | 아니요 |
> | expressroutecircuits | 아니요 | 아니요 | 아니요 |
> | expressroutegateways | 아니요 | 아니요 | 아니요 |
> | expressrouteserviceproviders | 아니요 | 아니요 | 아니요 |
> | firewallpolicies | 예 | 예 | 아니요 |
> | frontdoors | 아니요 | 아니요 | 아니요 |
> | ipallocations | 예 | 예 | 아니요 |
> | ipgroups | 예 | 예 | 아니요 |
> | loadbalancers | 예 - 기본 SKU<br> 예 - 표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU | 예 <br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 내부 및 외부 부하 분산 장치를 이동합니다. |
> | localnetworkgateways | 예 | 예 | 아니요 |
> | natgateways | 아니요 | 아니요 | 아니요 |
> | networkexperimentprofiles | 아니요 | 아니요 | 아니요 |
> | networkintentpolicies | 예 | 예 | 아니요 |
> | networkinterfaces | 예 | 예 | 예 <br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 NIC를 이동합니다. |
> | networkprofiles | 아니요 | 아니요 | 아니요 |
> | networksecuritygroups | 예 | 예 | 예 <br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 NGS(네트워크 보안 그룹)를 이동합니다. |
> | networkwatchers | 아니요 | 아니요 | 아니요 |
> | networkwatchers / connectionmonitors | 예 | 아니요 | 아니요 |
> | networkwatchers / flowlogs | 예 | 아니요 | 아니요 |
> | networkwatchers / pingmeshes | 예 | 아니요 | 아니요 |
> | p2svpngateways | 아니요 | 아니요 | 아니요 |
> | privatednszones | 예 | 예 | 아니요 |
> | privatednszones / virtualnetworklinks | 예 | 예 | 아니요 |
> | privatednszonesinternal | 아니요 | 아니요 | 아니요 |
> | privateendpointredirectmaps | 아니요 | 아니요 | 아니요 |
> | privateendpoints | 아니요 | 아니요 | 아니요 |
> | privatelinkservices | 아니요 | 아니요 | 아니요 |
> | publicipaddresses | 예 - 기본 SKU<br>예 - 표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU | 예<br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md)를 사용하여 공용 IP 주소 구성을 이동합니다(IP 주소가 보존되지 않음). |
> | publicipprefixes | 예 | 예 | 아니요 |
> | routefilters | 아니요 | 아니요 | 아니요 |
> | routetables | 예 | 예 | 아니요 |
> | securitypartnerproviders | 예 | 예 | 아니요 |
> | serviceendpointpolicies | 예 | 예 | 아니요 |
> | trafficmanagergeographichierarchies | 아니요 | 아니요 | 아니요 |
> | trafficmanagerprofiles | 예 | 예 | 아니요 |
> | trafficmanagerprofiles / heatmaps | 아니요 | 아니요 | 아니요 |
> | trafficmanagerusermetricskeys | 아니요 | 아니요 | 아니요 |
> | virtualhubs | 아니요 | 아니요 | 아니요 |
> | virtualnetworkgateways | 예 | 예 | 아니요 |
> | virtualnetworks | 예 | 예 | 아니요 |
> | virtualnetworktaps | 아니요 | 아니요 | 아니요 |
> | virtualrouters | 예 | 예 | 아니요 |
> | virtualwans | 아니요 | 아니요 |
> | vpngateways(Virtual WAN) | 아니요 | 아니요 | 아니요 |
> | vpnserverconfigurations | 아니요 | 아니요 | 아니요 |
> | vpnsites(Virtual WAN) | 아니요 | 아니요 | 아니요 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 네임스페이스 | 예 | 예 | 아니요 |
> | namespaces / notificationhubs | 예 | 예 | 아니요 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | 예 | 예 | 아니요 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | 아니요 | 아니요 | 아니요 |
> | importsites | 아니요 | 아니요 | 아니요 |
> | serversites | 아니요 | 아니요 | 아니요 |
> | vmwaresites | 아니요 | 아니요 | 아니요 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.
>
> 연결된 자동화 계정이 있는 작업 영역은 이동할 수 없습니다. 이동 작업을 시작하기 전에 자동화 계정의 연결을 해제해야 합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | 아니요 | 아니요 | 아니요 |
> | deletedworkspaces | 아니요 | 아니요 | 아니요 |
> | linktargets | 아니요 | 아니요 | 아니요 |
> | storageinsightconfigs | 아니요 | 아니요 | 아니요 |
> | workspaces | 예 | 예 | 아니요 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | 아니요 | 아니요 | 아니요 |
> | managementconfigurations | 예 | 예 | 아니요 |
> | solutions | 예 | 예 | 아니요 |
> | 뷰 | 예 | 예 | 아니요 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | 아니요 | 아니요 | 아니요 |
> | peerasns | 아니요 | 아니요 | 아니요 |
> | peeringlocations | 아니요 | 아니요 | 아니요 |
> | peerings | 아니요 | 아니요 | 아니요 |
> | peeringservicecountries | 아니요 | 아니요 | 아니요 |
> | peeringservicelocations | 아니요 | 아니요 | 아니요 |
> | peeringserviceproviders | 아니요 | 아니요 | 아니요 |
> | peeringservices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | 아니요 | 아니요 | 아니요 |
> | policystates | 아니요 | 아니요 | 아니요 |
> | policytrackedresources | 아니요 | 아니요 | 아니요 |
> | remediations | 아니요 | 아니요 | 아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | consoles | 아니요 | 아니요 | 아니요 |
> | dashboards | 예 | 예 | 아니요 |
> | usersettings | 아니요 | 아니요 | 아니요 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | 예 | 예 | 아니요 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | capacities | 예 | 예 | 아니요 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ---------- |
> | 계정 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ---------- |
> | 계정 | **보류 중** | **보류 중** | 아니요 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | 아니요 | 아니요 | 아니요 |
> | providerregistrations | 아니요 | 아니요 | 아니요 |
> | rollouts | 아니요 | 아니요 | 아니요 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | 아니요 | 아니요 | 아니요 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

>[!IMPORTANT]
>- [Recovery Services 이동 지침](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.
>- [지역 간 리소스 이동 후 Recovery Services 자격 증명 모음에서 백업 계속](../../backup/azure-backup-move-vaults-across-regions.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | 아니요 | 아니요 | 아니요 |
> | vaults | 예 | 예 | 아니요.<br/><br/> Azure 지역 간에 Azure Backup용 Recovery Services 자격 증명 모음을 이동하는 것은 지원되지 않습니다.<br/><br/> Azure Site Recovery용 Recovery Services 자격 증명 모음에서 대상 지역에서 [자격 증명 모음을 사용하지 않도록 설정하고 다시 생성](../../site-recovery/move-vaults-across-regions.md)할 수 있습니다. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | 아니요 | 아니요 | 아니요 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 네임스페이스 | 예 | 예 | 아니요 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 쿼리 | 예 | 예 | 아니요 |
> | resourcechangedetails | 아니요 | 아니요 | 아니요 |
> | resourcechanges | 아니요 | 아니요 | 아니요 |
> | 리소스 | 아니요 | 아니요 | 아니요 |
> | resourceshistory | 아니요 | 아니요 | 아니요 |
> | subscriptionsstatus | 아니요 | 아니요 | 아니요 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | 아니요 | 아니요 | 아니요 |
> | emergingissues | 아니요 | 아니요 | 아니요 |
> | events | 아니요 | 아니요 | 아니요 |
> | metadata | 아니요 | 아니요 | 아니요 |
> | 알림 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 배포 | 아니요 | 아니요 | 아니요 |
> | deploymentscripts | 아니요 | 아니요 | 예<br/><br/>[Microsoft.Resources 리소스를 새 지역으로 이동](microsoft-resources-move-regions.md) |
> | deploymentscripts / logs | 아니요 | 아니요 | 아니요 |
> | 링크 | 아니요 | 아니요 | 아니요 |
> | providers | 아니요 | 아니요 | 아니요 |
> | resourcegroups | 아니요 | 아니요 | 아니요 |
> | 리소스 | 아니요 | 아니요 | 아니요 |
> | 구독 | 아니요 | 아니요 | 아니요 |
> | tags | 아니요 | 아니요 | 아니요 |
> | templatespecs | 아니요 | 아니요 | 예<br/><br/>[Microsoft.Resources 리소스를 새 지역으로 이동](microsoft-resources-move-regions.md) |
> | templatespecs / versions | 아니요 | 아니요 | 아니요 |
> | tenants | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 애플리케이션 | 예 | 아니요 | 아니요 |
> | saasresources | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> 다른 지역에 있는 여러 Search 리소스를 한 번의 작업으로 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | 아니요 | 아니요 | 아니요 |
> | searchservices | 예 | 예 | 아니요 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | 아니요 | 아니요 | 아니요 |
> | advancedthreatprotectionsettings | 아니요 | 아니요 | 아니요 |
> | 경고 | 아니요 | 아니요 | 아니요 |
> | allowedconnections | 아니요 | 아니요 | 아니요 |
> | applicationwhitelistings | 아니요 | 아니요 | 아니요 |
> | assessmentmetadata | 아니요 | 아니요 | 아니요 |
> | assessments | 아니요 | 아니요 | 아니요 |
> | autodismissalertsrules | 아니요 | 아니요 | 아니요 |
> | automations | 예 | 예 | 아니요 |
> | autoprovisioningsettings | 아니요 | 아니요 | 아니요 |
> | complianceresults | 아니요 | 아니요 | 아니요 |
> | compliances | 아니요 | 아니요 | 아니요 |
> | datacollectionagents | 아니요 | 아니요 | 아니요 |
> | devicesecuritygroups | 아니요 | 아니요 | 아니요 |
> | discoveredsecuritysolutions | 아니요 | 아니요 | 아니요 |
> | externalsecuritysolutions | 아니요 | 아니요 | 아니요 |
> | informationprotectionpolicies | 아니요 | 아니요 | 아니요 |
> | iotsecuritysolutions | 예 | 예 | 아니요 |
> | iotsecuritysolutions / analyticsmodels | 아니요 | 아니요 | 아니요 |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 아니요 | 아니요 | 아니요 |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 아니요 | 아니요 | 아니요 |
> | jitnetworkaccesspolicies | 아니요 | 아니요 | 아니요 |
> | 정책 | 아니요 | 아니요 | 아니요 |
> | pricings | 아니요 | 아니요 | 아니요 |
> | regulatorycompliancestandards | 아니요 | 아니요 | 아니요 |
> | regulatorycompliancestandards / regulatorycompliancecontrols | 아니요 | 아니요 | 아니요 |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 아니요 | 아니요 | 아니요 |
> | securitycontacts | 아니요 | 아니요 | 아니요 |
> | securitysolutions | 아니요 | 아니요 | 아니요 |
> | securitysolutionsreferencedata | 아니요 | 아니요 | 아니요 |
> | securitystatuses | 아니요 | 아니요 | 아니요 |
> | securitystatusessummaries | 아니요 | 아니요 | 아니요 |
> | servervulnerabilityassessments | 아니요 | 아니요 | 아니요 |
> | 설정 | 아니요 | 아니요 | 아니요 |
> | subassessments | 아니요 | 아니요 | 아니요 |
> | tasks | 아니요 | 아니요 | 아니요 |
> | topologies | 아니요 | 아니요 | 아니요 |
> | workspacesettings | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregations | 아니요 | 아니요 | 아니요 |
> | alertrules | 아니요 | 아니요 | 아니요 |
> | alertruletemplates | 아니요 | 아니요 | 아니요 |
> | automationrules | 아니요 | 아니요 | 아니요 |
> | bookmarks | 아니요 | 아니요 | 아니요 |
> | cases | 아니요 | 아니요 | 아니요 |
> | dataconnectors | 아니요 | 아니요 | 아니요 |
> | 엔터티 | 아니요 | 아니요 | 아니요 |
> | entityqueries | 아니요 | 아니요 | 아니요 |
> | incidents | 아니요 | 아니요 | 아니요 |
> | officeconsents | 아니요 | 아니요 | 아니요 |
> | 설정 | 아니요 | 아니요 | 아니요 |
> | threatintelligence | 아니요 | 아니요 | 아니요 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | gateways | 아니요 | 아니요 | 아니요 |
> | nodes | 아니요 | 아니요 | 아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 네임스페이스 | 예 | 예 | 아니요 |
> | premiummessagingregions | 아니요 | 아니요 | 아니요 |
> | sku | 아니요 | 아니요 | 아니요 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 애플리케이션 | 아니요 | 아니요 | 아니요 |
> | clusters | 예 | 예 | 아니요 |
> | containergroups | 아니요 | 아니요 | 아니요 |
> | containergroupsets | 아니요 | 아니요 | 아니요 |
> | edgeclusters | 아니요 | 아니요 | 아니요 |
> | managedclusters | 아니요 | 아니요 | 아니요 |
> | networks | 아니요 | 아니요 | 아니요 |
> | secretstores | 아니요 | 아니요 | 아니요 |
> | volumes | 아니요 | 아니요 | 아니요 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 애플리케이션 | 예 | 예 | 아니요 |
> | containergroups | 아니요 | 아니요 | 아니요 |
> | gateways | 예 | 예 | 아니요 |
> | networks | 예 | 예 | 아니요 |
> | secrets | 예 | 예 | 아니요 |
> | volumes | 예 | 예 | 아니요 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | rollouts | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | 예 | 예 | 아니요 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | 아니요 | 아니요 | 아니요 |
> | 애플리케이션 | 아니요 | 아니요 | 아니요 |
> | jitrequests | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure Synapse Analytics 데이터베이스에 적용됩니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | 아니요 | 아니요 | 아니요 |
> | 위치 | 예 | 예 | 아니요 |
> | managedinstances | 아니요 | 아니요 | 예 <br/><br/> 지역 간 관리되는 인스턴스 이동에 대해 [자세히 알아보세요](../../azure-sql/database/move-resources-across-regions.md). |
> | managedinstances / databases | 아니요 | 아니요 | 예 |
> | servers | 예 | 예 |예 |
> | servers / databases | 예 | 예 | 예 <br/><br/> 지역 간 데이터베이스 이동에 대해 [자세히 알아보세요](../../azure-sql/database/move-resources-across-regions.md).<br/><br/> Azure Resource Mover를 사용하여 Azure SQL 데이터베이스를 이동하는 방법에 대해 [자세히 알아보세요](../../resource-mover/tutorial-move-region-sql.md).  |
> | servers / databases / backuplongtermretentionpolicies | 예 | 예 | 아니요 |
> | servers / elasticpools | 예 | 예 | 예 <br/><br/> 지역 간 탄력적 풀 이동에 대해 [자세히 알아보세요](../../azure-sql/database/move-resources-across-regions.md).<br/><br/> Azure Resource Mover를 사용하여 Azure SQL 탄력적 풀을 이동하는 방법에 대해 [자세히 알아보세요](../../resource-mover/tutorial-move-region-sql.md).  |
> | servers / jobaccounts | 예 | 예 | 아니요 |
> | servers / jobagents | 예 | 예 | 아니요 |
> | virtualclusters | 예 | 예 | 예 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | 예 | 예 | 아니요 |
> | sqlvirtualmachines | 예 | 예 | 아니요 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | 예 | 예 | 예<br/><br/> [Azure Storage 계정을 다른 지역으로 이동](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | caches | 아니요 | 아니요 | 아니요 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | 예 | 예 | 아니요 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | managers | 아니요 | 아니요 | 아니요 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> 실행 중 상태인 Stream Analytics 작업은 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | 아니요 | 아니요 | 아니요 |
> | streamingjobs | 예 | 예 | 아니요 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | 아니요 | 아니요 | 아니요 |
> | 인스턴스 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 구독 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | 아니요 | 아니요 | 아니요 |
> | supporttickets | 아니요 | 아니요 | 아니요 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | 아니요 | 아니요 | 아니요 |
> | workspaces / bigdatapools | 아니요 | 아니요 | 아니요 |
> | workspaces / sqlpools | 아니요 | 아니요 | 아니요 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | 예 | 예 | 아니요 |
> | environments / eventsources | 예 | 예 | 아니요 |
> | environments / referencedatasets | 예 | 예 | 아니요 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | stores | 예 | 예 | 아니요 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | 아니요 | 아니요 | 아니요 |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Azure DevOps 구독을 변경하려면 [청구에 사용되는 Azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | account | 아니요 | 아니요 | 아니요 |
> | account / extension | 아니요 | 아니요 | 아니요 |
> | account / project | 아니요 | 아니요 | 아니요 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | 아니요 | 아니요 | 아니요 |
> | resourcepools | 아니요 | 아니요 | 아니요 |
> | vcenters | 아니요 | 아니요 | 아니요 |
> | virtualmachines | 아니요 | 아니요 | 아니요 |
> | virtualmachinetemplates | 아니요 | 아니요 | 아니요 |
> | virtualnetworks | 아니요 | 아니요 | 아니요 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes | 아니요 | 아니요 | 아니요 |
> | dedicatedcloudservices | 아니요 | 아니요 | 아니요 |
> | virtualmachines | 아니요 | 아니요 | 아니요 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | 아니요 | 아니요 | 아니요 |
> | vnfs | 아니요 | 아니요 | 아니요 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 계정 | 아니요 | 아니요 | 아니요 |
> | 플랜 | 아니요 | 아니요 | 아니요 |
> | registeredsubscriptions | 아니요 | 아니요 | 아니요 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | 아니요 | 아니요 | 아니요 |
> | billingmeters | 아니요 | 아니요 | 아니요 |
> | certificates | 아니요 | 예 | 아니요 |
> | 인증서(관리) | 아니요 | 아니요 | 아니요 |
> | connectiongateways | 예 | 예 | 아니요 |
> | connections | 예 | 예 | 아니요 |
> | customapis | 예 | 예 | 아니요 |
> | deletedsites | 아니요 | 아니요 | 아니요 |
> | deploymentlocations | 아니요 | 아니요 | 아니요 |
> | georegions | 아니요 | 아니요 | 아니요 |
> | hostingenvironments | 아니요 | 아니요 | 아니요 |
> | kubeenvironments | 예 | 예 | 아니요 |
> | publishingusers | 아니요 | 아니요 | 아니요 |
> | 동영상 추천 기능 | 아니요 | 아니요 | 아니요 |
> | resourcehealthmetadata | 아니요 | 아니요 | 아니요 |
> | runtimes | 아니요 | 아니요 | 아니요 |
> | serverfarms | 예 | 예 | 아니요 |
> | serverfarms / eventgridfilters | 아니요 | 아니요 | 아니요 |
> | sites | 예 | 예 | 아니요 |
> | sites / premieraddons | 예 | 예 | 아니요 |
> | sites / slots | 예 | 예 | 아니요 |
> | sourcecontrols | 아니요 | 아니요 | 아니요 |
> | staticsites | 아니요 | 아니요 | 아니요 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | 아니요 | 아니요 | 아니요 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | 아니요 | 아니요 | 아니요 |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | 작업 | 아니요 | 아니요 | 아니요 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription | 지역 이동 |
> | ------------- | ----------- | ---------- | ----------- |
> | components | 아니요 | 아니요 | 아니요 |
> | componentssummary | 아니요 | 아니요 | 아니요 |
> | monitorinstances | 아니요 | 아니요 | 아니요 |
> | monitorinstancessummary | 아니요 | 아니요 | 아니요 |
> | monitors | 아니요 | 아니요 | 아니요 |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.
- Resource Mover 서비스에 대해 [자세히 알아보세요](../../resource-mover/overview.md).
- 쉼표로 구분된 값의 파일과 동일한 데이터를 가져오려면 리소스 그룹 및 구독 이동 지원을 위해 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)를 다운로드합니다. 이러한 속성 및 지역 이동을 지원하려면 [move-support-resources-with-regions.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources-with-regions.csv)를 다운로드합니다.