---
title: 리소스에 대한 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 863e7fbc686ed27588153979432b6189aabcba50
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248124"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그](tag-resources.md)를 지원하는지 여부를 설명합니다. **태그 지원** 이라는 열은 리소스 형식에 태그에 대한 속성이 있는지 여부를 나타냅니다. **비용 보고서의 태그** 레이블이 지정된 열은 리소스 종류가 태그를 비용 보고서에 전달하는지 여부를 나타냅니다. [Cost Management 비용 분석](../../cost-management-billing/costs/group-filter.md) 및 [Azure 청구 송장 및 일간 사용량 데이터](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)에서 태그를 기준으로 비용을 볼 수 있습니다.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) 파일을 다운로드합니다.

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzurePercept](#microsoftazurepercept)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BackupSolutions](#microsoftbackupsolutions)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BillingBenefits](#microsoftbillingbenefits)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.CodeSigning](#microsoftcodesigning)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConfidentialLedger](#microsoftconfidentialledger)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.Dashboard](#microsoftdashboard)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DelegatedNetwork](#microsoftdelegatednetwork)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.Diagnostics](#microsoftdiagnostics)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Fidalgo](#microsoftfidalgo)
> - [FluidRelay](#microsoftfluidrelay)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.Graph](#microsoftgraph)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HpcWorkbench 벤치](#microsofthpcworkbench)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [HybridConnectivity](#microsofthybridconnectivity)
> - [HybridContainerService](#microsofthybridcontainerservice)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [MarketplaceNotifications](#microsoftmarketplacenotifications)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft 모니터](#microsoftmonitor)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft NetworkFunction](#microsoftnetworkfunction)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft PlayFab](#microsoftplayfab)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft 할당량](#microsoftquota)
> - [RecommendationsService](#microsoftrecommendationsservice)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft Scom](#microsoftscom)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TestBase](#microsofttestbase)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VideoIndexer](#microsoftvideoindexer)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 예 | 예 |
> | DomainServices / oucontainer | 예 | 아니요 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 예 | 아니요 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 아니요 | 아니요 |
> | addsservices | 아니요 | 아니요 |
> | agents | 아니요 | 아니요 |
> | anonymousapiusers | 아니요 | 아니요 |
> | 구성 | 아니요 | 아니요 |
> | 로그 | 아니요 | 아니요 |
> | reports | 아니요 | 아니요 |
> | servicehealthmetrics | 아니요 | 아니요 |
> | services | 아니요 | 아니요 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | advisorScore | 예 | 예 |
> | 구성 | 아니요 | 예 |
> | generateRecommendations | 예 | 아니요 |
> | metadata | 아니요 | 아니요 |
> | 동영상 추천 기능 | 아니요 | 아니요 |
> | suppressions | 아니요 | 아니요 |

> [!NOTE]
> 모든 Microsoft.Advisor 리소스는 무료이므로 비용 보고서에 포함되지 않습니다.

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | farmBeats | 예 | 예 |
> | farmBeats / eventGridFilters | 아니요 | 아니요 |
> | farmBeats/확장 | 아니요 | 아니요 |
> | farmBeatsExtensionDefinitions | 아니요 | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionRules | 예 | 예 |
> | 경고 | 아니요 | 예 |
> | alertsList | 예 | 예 |
> | alertsMetaData | 예 | 예 |
> | alertsSummary | 예 | 예 |
> | alertsSummaryList | 예 | 예 |
> | migrateFromSmartDetection | 아니요 | 아니요 |
> | resourceHealthAlertRules | 예 | 예 |
> | smartDetectorAlertRules | 예 | 예 |
> | smartGroups | 예 | 아니요 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 아니요 | 아니요 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedServices | 아니요 | 아니요 |
> | getDomainOwnershipIdentifier | 아니요 | 아니요 |
> | reportFeedback | 예 | 아니요 |
> | 서비스 | 예 | 예 |
> | service/eventGridFilters | 아니요 | 아니요 |
> | validateServiceName | 예 | 예 |

> [!NOTE]
> Azure API Management는 각 서비스에 대해 최대 15개의 태그 이름/값 쌍을 만드는 것만 지원합니다.

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | migrateProjects | 아니요 | 아니요 |
> | migrateProjects / assessments | 아니요 | 아니요 |
> | migrateProjects / assessments / assessedApplications | 아니요 | 아니요 |
> | migrateProjects / assessments / assessedApplications / machines | 아니요 | 아니요 |
> | migrateProjects / assessments / assessedMachines | 아니요 | 아니요 |
> | migrateProjects / assessments / assessedMachines / applications | 아니요 | 아니요 |
> | migrateProjects / assessments / machinesToAssess | 아니요 | 아니요 |
> | migrateProjects / sites | 아니요 | 아니요 |
> | migrateProjects / sites / applianceConfigurations | 아니요 | 아니요 |
> | migrateProjects / sites / machines | 아니요 | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 | 예 |
> | configurationStores / eventGridFilters | 예 | 예 |
> | configurationStores / keyValues | 예 | 아니요 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Spring | 예 | 예 |
> | Spring / 앱 | 예 | 예 |
> | Spring / 앱 / 배포 | 예 | 아니요 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 예 | 예 |
> | defaultProviders | 예 | 아니요 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | 예 | 예 |
> | accessReviewScheduleSettings | 예 | 아니요 |
> | batchResourceCheckAccess | 아니요 | 예 |
> | classicAdministrators | 예 | 예 |
> | dataAliases | 예 | 예 |
> | dataPolicyManifests | 예 | 예 |
> | denyAssignments | 예 | 예 |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 예 |
> | elevateAccess | 예 | 아니요 |
> | eligibleChildResources | 아니요 | 예 |
> | findOrphanRoleAssignments | 예 | 아니요 |
> | locks | 아니요 | 아니요 |
> | 권한 | 아니요 | 예 |
> | policyAssignments | 예 | 예 |
> | policyDefinitions | 예 | 예 |
> | policyExemptions | 예 | 예 |
> | policySetDefinitions | 예 | 예 |
> | privateLinkAssociations | 예 | 예 |
> | providerOperations | 예 | 예 |
> | resourceManagementPrivateLinks | 예 | 예 |
> | roleAssignmentApprovals | 아니요 | 아니요 |
> | roleAssignments | 예 | 예 |
> | roleAssignmentScheduleInstances | 아니요 | 아니요 |
> | roleAssignmentScheduleRequests | 아니요 | 아니요 |
> | roleAssignmentSchedules | 예 | 예 |
> | roleAssignmentsUsageMetrics | 예 | 예 |
> | roleDefinitions | 예 | 예 |
> | roleEligibilityScheduleInstances | 아니요 | 아니요 |
> | roleEligibilityScheduleRequests | 아니요 | 아니요 |
> | roleEligibilitySchedules | 아니요 | 아니요 |
> | roleManagementPolicies | 아니요 | 아니요 |
> | roleManagementPolicyAssignments | 예 | 예 |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | bestPractices | 아니요 | 아니요 |
> | bestPractices/버전 | 아니요 | 아니요 |
> | configurationProfileAssignmentIntents | 아니요 | 아니요 |
> | configurationProfileAssignments | 예 | 예 |
> | configurationProfilePreferences | 예 | 예 |
> | configurationProfiles | 예 | 예 |
> | configurationProfiles/버전 | 예 | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 예 | 예 |
> | automationAccounts / configurations | 예 | 예 |
> | automationAccounts/hybridRunbookWorkerGroups | 아니요 | 아니요 |
> | automationAccounts/hybridRunbookWorkerGroups/hybridRunbookWorkers | 아니요 | 아니요 |
> | automationAccounts / jobs | 예 | 예 |
> | automationAccounts / privateEndpointConnectionProxies | 예 | 예 |
> | automationAccounts / privateEndpointConnections | 예 | 예 |
> | automationAccounts / privateLinkResources | 예 | 예 |
> | automationAccounts / runbooks | 예 | 예 |
> | automationAccounts / softwareUpdateConfigurations | 예 | 예 |
> | automationAccounts / webhooks | 예 | 예 |

> [!NOTE]
> Azure Automation은 각 Automation 리소스에 대해 최대 15개의 태그 이름/값 쌍을 만드는 것만 지원합니다.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | privateClouds | 예 | 예 |
> | privateClouds / addons | 예 | 예 |
> | privateClouds / authorizations | 예 | 예 |
> | privateClouds / cloudLinks | 예 | 예 |
> | privateClouds / clusters | 예 | 예 |
> | privateClouds / clusters / datastores | 예 | 아니요 |
> | privateClouds / clusters / placementPolicies | 아니요 | 아니요 |
> | privateClouds / clusters / virtualMachines | 아니요 | 예 |
> | privateClouds / globalReachConnections | 예 | 예 |
> | privateClouds / hcxEnterpriseSites | 예 | 예 |
> | privateClouds / scriptExecutions | 아니요 | 아니요 |
> | privateClouds / scriptPackages | 아니요 | 아니요 |
> | privateClouds / scriptPackages / scriptCmdlets | 예 | 예 |
> | privateClouds / workloadNetworks | 예 | 예 |
> | privateClouds / workloadNetworks / dhcpConfigurations | 예 | 예 |
> | privateClouds / workloadNetworks / dnsServices | 아니요 | 아니요 |
> | privateClouds / workloadNetworks / dnsZones | 예 | 예 |
> | privateClouds / workloadNetworks / gateways | 예 | 예 |
> | privateClouds / workloadNetworks / portMirroringProfiles | 예 | 아니요 |
> | privateClouds / workloadNetworks / publicIPs | 아니요 | 예 |
> | privateClouds / workloadNetworks / segments | 예 | 예 |
> | privateClouds / workloadNetworks / virtualMachines | 예 | 예 |
> | privateClouds / workloadNetworks / vmGroups | 예 | 예 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 아니요 | 예 |
> | environments / accounts | 예 | 예 |
> | environments / accounts / namespaces | 예 | 예 |
> | environments / accounts / namespaces / configurations | 예 | 아니요 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 예 | 아니요 |
> | b2ctenants | 아니요 | 예 |
> | guestUsages | 예 | 예 |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DataControllers | 아니요 | 아니요 |
> | DataWarehouseInstances | 아니요 | 아니요 |
> | PostgresInstances | 아니요 | 아니요 |
> | SqlManagedInstances | 아니요 | 아니요 |
> | SqlServerInstances | 아니요 | 아니요 |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | 아니요 | 아니요 |
> | dstsServiceAccounts | 아니요 | 아니요 |
> | dstsServiceClientIdentities | 아니요 | 아니요 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations | 예 | 예 |
> | sqlServerRegistrations / sqlServers | 예 | 아니요 |

## <a name="microsoftazurepercept"></a>AzurePercept

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 아니요 | 아니요 |
> | 계정/장치 | 아니요 | 예 |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | 아니요 | 아니요 |
> | 카탈로그/인증서 | 아니요 | 아니요 |
> | 카탈로그/배포 | 아니요 | 아니요 |
> | 카탈로그/장치 | 아니요 | 아니요 |
> | 카탈로그/이미지 | 아니요 | 아니요 |
> | catalogs / products | 아니요 | 아니요 |
> | 카탈로그/제품/p s 그룹 | 아니요 | 아니요 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | 예 | 아니요 |
> | linkedSubscriptions | 예 | 예 |
> | registrations | 예 | 예 |
> | registrations / customerSubscriptions | 예 | 예 |
> | registrations / products | 예 | 아니요 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 아니요 | 아니요 |
> | 클러스터/arcsettings | 아니요 | 아니요 |
> | 클러스터/arcsettings/확장 | 아니요 | 아니요 |
> | galleryImages | 아니요 | 아니요 |
> | networkInterfaces | 아니요 | 아니요 |
> | virtualHardDisks | 아니요 | 아니요 |
> | virtualmachines | 아니요 | 아니요 |
> | virtualmachines / extensions | 아니요 | 아니요 |
> | virtualmachines/hybrididentitymetadata | 아니요 | 예 |
> | virtualNetworks | 예 | 아니요 |

## <a name="microsoftbackupsolutions"></a>Microsoft BackupSolutions

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | VMwareApplications | 예 | 예 |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | 예 | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 예 | 예 |
> | batchAccounts / certificates | 예 | 예 |
> | batchAccounts / pools | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 예 | 예 |
> | billingAccounts / agreements | 예 | 아니요 |
> | billingAccounts / appliedReservationOrders | 아니요 | 예 |
> | billingAccounts / billingPermissions | 예 | 예 |
> | billingAccounts / billingProfiles | 예 | 예 |
> | billingAccounts / billingProfiles / billingPermissions | 예 | 예 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 예 | 예 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / billingProfiles / billingSubscriptions | 예 | 예 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 예 | 예 |
> | billingAccounts / billingProfiles / customers | 예 | 예 |
> | billingAccounts / billingProfiles / instructions | 예 | 예 |
> | billingAccounts / billingProfiles / invoices | 예 | 예 |
> | billingAccounts / billingProfiles / invoices / pricesheet | 예 | 예 |
> | billingAccounts / billingProfiles / invoices / transactions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / products | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / transactions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / transfers | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | 예 | 예 |
> | billingAccounts / BillingProfiles / patchOperations | 예 | 아니요 |
> | billingAccounts / billingProfiles / paymentMethodLinks | 아니요 | 예 |
> | billingAccounts / billingProfiles / paymentMethods | 예 | 예 |
> | billingAccounts / billingProfiles / policies | 예 | 예 |
> | billingAccounts / billingProfiles / pricesheet | 예 | 예 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 예 | 예 |
> | billingAccounts / billingProfiles / products | 예 | 예 |
> | billingAccounts / billingProfiles / reservations | 예 | 예 |
> | billingAccounts / billingProfiles / transactions | 예 | 예 |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | 예 | 예 |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | 예 | 예 |
> | billingAccounts / billingRoleAssignments | 예 | 예 |
> | billingAccounts / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / billingSubscriptions | 예 | 예 |
> | billingAccounts / billingSubscriptions / elevateRole | 예 | 예 |
> | billingAccounts / billingSubscriptions / invoices | 예 | 예 |
> | billingAccounts / createBillingRoleAssignment | 예 | 예 |
> | billingAccounts / createInvoiceSectionOperations | 예 | 예 |
> | billingAccounts / customers | 예 | 예 |
> | billingAccounts / customers / billingPermissions | 예 | 예 |
> | billingAccounts / customers / billingSubscriptions | 예 | 예 |
> | billingAccounts / customers / initiateTransfer | 예 | 예 |
> | billingAccounts / customers / policies | 예 | 예 |
> | billingAccounts / customers / products | 예 | 예 |
> | billingAccounts / customers / transactions | 예 | 예 |
> | billingAccounts / customers / transfers | 예 | 아니요 |
> | billingAccounts/customers/전송자 계정 | 아니요 | 예 |
> | billingAccounts / departments | 예 | 예 |
> | billingAccounts / departments / billingPermissions | 예 | 예 |
> | billingAccounts / departments / billingRoleAssignments | 예 | 예 |
> | billingAccounts / departments / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / departments / billingSubscriptions | 예 | 아니요 |
> | billingAccounts/부서/enrollmentAccounts | 아니요 | 예 |
> | billingAccounts / enrollmentAccounts | 예 | 예 |
> | billingAccounts / enrollmentAccounts / billingPermissions | 예 | 예 |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | 예 | 예 |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | 예 | 예 |
> | billingAccounts / invoices | 예 | 예 |
> | billingAccounts / invoices / transactions | 예 | 예 |
> | billingAccounts / invoices / transactionSummary | 예 | 예 |
> | billingAccounts / invoiceSections | 예 | 예 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 예 | 예 |
> | billingAccounts / invoiceSections / billingSubscriptions | 예 | 예 |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | 예 | 예 |
> | billingAccounts / invoiceSections / elevate | 예 | 예 |
> | billingAccounts / invoiceSections / initiateTransfer | 예 | 예 |
> | billingAccounts / invoiceSections / patchOperations | 예 | 예 |
> | billingAccounts / invoiceSections / productMoveOperations | 예 | 예 |
> | billingAccounts / invoiceSections / products | 예 | 예 |
> | billingAccounts / invoiceSections / products / transfer | 예 | 예 |
> | billingAccounts / invoiceSections / products / updateAutoRenew | 예 | 예 |
> | billingAccounts / invoiceSections / transactions | 예 | 예 |
> | billingAccounts / invoiceSections / transfers | 예 | 예 |
> | billingAccounts / lineOfCredit | 예 | 예 |
> | billingAccounts / patchOperations | 예 | 예 |
> | billingAccounts / payableOverage | 예 | 예 |
> | billingAccounts / paymentMethods | 예 | 예 |
> | billingAccounts / payNow | 예 | 아니요 |
> | billingAccounts/정책 | 아니요 | 예 |
> | billingAccounts / products | 예 | 아니요 |
> | billingAccounts / promotionalCredits | 아니요 | 예 |
> | billingAccounts / reservations | 예 | 아니요 |
> | billingAccounts / savingsPlanOrders | 아니요 | 아니요 |
> | billingAccounts / savingsPlanOrders / savingsPlans | 아니요 | 아니요 |
> | billingAccounts / savingsPlans | 아니요 | 예 |
> | billingAccounts / transactions | 예 | 예 |
> | billingPeriods | 예 | 예 |
> | billingPermissions | 예 | 예 |
> | billingProperty | 예 | 예 |
> | billingRoleAssignments | 예 | 예 |
> | billingRoleDefinitions | 예 | 예 |
> | createBillingRoleAssignment | 예 | 아니요 |
> | departments | 아니요 | 예 |
> | enrollmentAccounts | 예 | 아니요 |
> | invoices | 아니요 | 아니요 |
> | paymentMethods | 아니요 | 아니요 |
> | promotionalCredits | 아니요 | 아니요 |
> | 확장 | 아니요 | 아니요 |
> | transfers | 아니요 | 예 |
> | transfers / acceptTransfer | 예 | 예 |
> | transfers / declineTransfer | 예 | 예 |
> | transfers / operationStatus | 예 | 예 |
> | transfers / validateTransfer | 예 | 예 |
> | validateAddress | 예 | 아니요 |

## <a name="microsoftbillingbenefits"></a>Microsoft.BillingBenefits

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | savingsPlanOrderAliases | 아니요 | 예 |
> | validate | 예 | 아니요 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 예 | 예 |
> | cordaMembers | 예 | 예 |
> | watchers | 예 | 예 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | TokenServices | 예 | 예 |
> | TokenServices / BlockchainNetworks | 예 | 예 |
> | TokenServices / Groups | 예 | 예 |
> | TokenServices / Groups / Accounts | 예 | 예 |
> | TokenServices / TokenTemplates | 예 | 아니요 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 예 | 예 |
> | blueprintAssignments / assignmentOperations | 예 | 예 |
> | blueprintAssignments / operations | 예 | 아니요 |
> | blueprints | 아니요 | 예 |
> | blueprints / artifacts | 예 | 예 |
> | blueprints / versions | 예 | 예 |
> | blueprints / versions / artifacts | 예 | 아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | botServices | 예 | 예 |
> | botServices / channels | 예 | 예 |
> | botServices / connections | 예 | 아니요 |
> | botServices / privateEndpointConnectionProxies | 아니요 | 아니요 |
> | botServices / privateEndpointConnections | 아니요 | 아니요 |
> | botServices / privateLinkResources | 아니요 | 예 |
> | hostSettings | 예 | 예 |
> | 언어 | 예 | 예 |
> | 템플릿 | 예 | 아니요 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Redis | 예 | 예 |
> | Redis / EventGridFilters | 예 | 예 |
> | Redis / privateEndpointConnectionProxies | 예 | 예 |
> | Redis / privateEndpointConnectionProxies / validate | 예 | 예 |
> | Redis / privateEndpointConnections | 예 | 예 |
> | Redis / privateLinkResources | 예 | 예 |
> | redisEnterprise | 예 | 예 |
> | redisEnterprise / databases | 아니요 | 아니요 |
> | RedisEnterprise / privateEndpointConnectionProxies | 예 | 예 |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | 예 | 예 |
> | RedisEnterprise / privateEndpointConnections | 예 | 예 |
> | RedisEnterprise / privateLinkResources | 예 | 아니요 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 예 | 예 |
> | autoQuotaIncrease | 예 | 예 |
> | calculateExchange | 예 | 예 |
> | calculatePrice | 예 | 예 |
> | calculatePurchasePrice | 예 | 아니요 |
> | catalogs | 아니요 | 예 |
> | commercialReservationOrders | 예 | 아니요 |
> | 교환 | 아니요 | 예 |
> | ownReservations | 예 | 예 |
> | placePurchaseOrder | 예 | 예 |
> | reservationOrders | 예 | 예 |
> | reservationOrders / calculateRefund | 예 | 예 |
> | reservationOrders / merge | 예 | 예 |
> | reservationOrders / reservations | 예 | 예 |
> | reservationOrders / reservations / revisions | 예 | 예 |
> | reservationOrders / return | 예 | 예 |
> | reservationOrders / split | 예 | 예 |
> | reservationOrders / swap | 예 | 아니요 |
> | reservations | 아니요 | 예 |
> | resourceProviders | 예 | 아니요 |
> | 리소스 | 아니요 | 예 |
> | validateReservationOrder | 예 | 예 |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | sites | 아니요 | 아니요 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 예 | 예 |
> | CdnWebApplicationFirewallPolicies | 예 | 예 |
> | edgenodes | 아니요 | 아니요 |
> | 프로필 | 예 | 예 |
> | profiles / afdendpoints | 예 | 예 |
> | profiles / afdendpoints / routes | 아니요 | 아니요 |
> | profiles / customdomains | 아니요 | 아니요 |
> | profiles / endpoints | 예 | 예 |
> | profiles / endpoints / customdomains | 예 | 예 |
> | profiles / endpoints / origingroups | 예 | 예 |
> | profiles / endpoints / origins | 예 | 예 |
> | profiles / origingroups | 아니요 | 아니요 |
> | profiles / origingroups / origins | 아니요 | 아니요 |
> | profiles / rulesets | 아니요 | 아니요 |
> | profiles / rulesets / rules | 아니요 | 아니요 |
> | profiles / secrets | 아니요 | 아니요 |
> | profiles / securitypolicies | 예 | 예 |
> | validateProbe | 예 | 아니요 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 예 | 예 |
> | certificateOrders / certificates | 예 | 예 |
> | validateCertificateRegistrationInformation | 예 | 예 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 변경 내용 | 아니요 | 아니요 |
> | changeSnapshots | 아니요 | 아니요 |
> | computeChanges | 아니요 | 아니요 |
> | profile | 예 | 예 |
> | resourceChanges | 예 | 아니요 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니요 | 예 |
> | domainNames | 예 | 예 |
> | domainNames / capabilities | 예 | 예 |
> | domainNames / internalLoadBalancers | 예 | 예 |
> | domainNames / serviceCertificates | 예 | 예 |
> | domainNames / slots | 예 | 예 |
> | domainNames / slots / roles | 예 | 예 |
> | domainNames / slots / roles / metricDefinitions | 예 | 예 |
> | domainNames / slots / roles / metrics | 예 | 예 |
> | moveSubscriptionResources | 예 | 예 |
> | operatingSystemFamilies | 예 | 예 |
> | operatingSystems | 예 | 아니요 |
> | quotas | 아니요 | 예 |
> | resourceTypes | 예 | 예 |
> | validateSubscriptionMoveAvailability | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | virtualMachines / diagnosticSettings | 예 | 예 |
> | virtualMachines / metricDefinitions | 예 | 예 |
> | virtualMachines / metrics | 예 | 아니요 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 예 | 아니요 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니요 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | expressRouteCrossConnections / peerings | 예 | 예 |
> | gatewaySupportedDevices | 예 | 예 |
> | networkSecurityGroups | 예 | 아니요 |
> | quotas | 아니요 | 예 |
> | reservedIps | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 예 | 예 |
> | virtualNetworks / virtualNetworkPeerings | 예 | 아니요 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니요 | 아니요 |
> | disks | 아니요 | 아니요 |
> | images | 아니요 | 예 |
> | osImages | 예 | 예 |
> | osPlatformImages | 예 | 예 |
> | publicImages | 예 | 아니요 |
> | quotas | 아니요 | 예 |
> | storageAccounts | 예 | 예 |
> | storageAccounts / blobServices | 예 | 예 |
> | storageAccounts / fileServices | 예 | 예 |
> | storageAccounts / metricDefinitions | 예 | 예 |
> | storageAccounts / metrics | 예 | 예 |
> | storageAccounts / queueServices | 예 | 예 |
> | storageAccounts / services | 예 | 예 |
> | storageAccounts / services / diagnosticSettings | 예 | 예 |
> | storageAccounts / services / metricDefinitions | 예 | 예 |
> | storageAccounts / services / metrics | 예 | 예 |
> | storageAccounts / tableServices | 예 | 예 |
> | storageAccounts / vmImages | 예 | 예 |
> | vmImages | 예 | 예 |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | nodes | 예 | 예 |

## <a name="microsoftcodesigning"></a>Microsoft.CodeSigning

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | codeSigningAccounts | 아니요 | 아니요 |
> | codeSigningAccounts / certificateProfiles | 아니요 | 예 |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 플랜 | 예 | 예 |
> | registeredSubscriptions | 예 | 아니요 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / privateEndpointConnectionProxies | 예 | 예 |
> | accounts / privateEndpointConnections | 예 | 예 |
> | accounts / privateLinkResources | 예 | 아니요 |
> | deletedAccounts | 예 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | RateCard | 예 | 예 |
> | UsageAggregates | 예 | 아니요 |


## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 예 | 예 |
> | cloudServices | 예 | 예 |
> | cloudServices / networkInterfaces | 예 | 예 |
> | cloudServices / publicIPAddresses | 예 | 예 |
> | cloudServices / roleInstances | 예 | 예 |
> | cloudServices / roleInstances / networkInterfaces | 예 | 예 |
> | cloudServices / roles | 예 | 예 |
> | diskAccesses | 예 | 예 |
> | diskEncryptionSets | 예 | 예 |
> | disks | 예 | 예 |
> | galleries | 예 | 예 |
> | galleries / applications | Yes | 예 |
> | galleries / applications / versions | Yes | 아니요 |
> | galleries / images | 예 | 아니요 |
> | galleries / images / versions | 예 | 예 |
> | hostGroups | 예 | 예 |
> | hostGroups / hosts | 예 | 예 |
> | images | 예 | 예 |
> | proximityPlacementGroups | 예 | 예 |
> | restorePointCollections | 예 | 예 |
> | restorePointCollections / restorePoints | 예 | 예 |
> | sharedVMExtensions | 예 | 예 |
> | sharedVMExtensions / versions | 예 | 예 |
> | sharedVMImages | 예 | 예 |
> | sharedVMImages / versions | 예 | 아니요 |
> | 스냅샷 | 예 | 예 |
> | sshPublicKeys | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | virtualMachines / extensions | 예 | 예 |
> | virtualMachines / metricDefinitions | 예 | 예 |
> | virtualMachines / runCommands | 예 | 예 |
> | virtualMachineScaleSets | 예 | 예 |
> | virtualMachineScaleSets / extensions | 예 | 예 |
> | virtualMachineScaleSets / networkInterfaces | 예 | 예 |
> | virtualMachineScaleSets / publicIPAddresses | 예 | 예 |
> | virtualMachineScaleSets / virtualMachines | 예 | 예 |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 예 | 예 |

> [!NOTE]
> 일반화된 것으로 표시된 가상 머신에는 태그를 추가할 수 없습니다. [Set-AzVm -Generalized](/powershell/module/Az.Compute/Set-AzVM) 또는 [az vm generalize](/cli/azure/vm#az-vm-generalize)를 사용하여 가상 머신을 일반화된 것으로 표시합니다.

## <a name="microsoftconfidentialledger"></a>Microsoft.ConfidentialLedger

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 원장 | 아니요 | 예 |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CacheNodes | 아니요 | 아니요 |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | platformAccounts | 아니요 | 예 |
> | registeredSubscriptions | 예 | 예 |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 클러스터 | 아니요 | 아니요 |
> | 데이터 저장소 | 아니요 | 아니요 |
> | 호스트 | 아니요 | 아니요 |
> | ResourcePools | 아니요 | 아니요 |
> | VCenters | 아니요 | 아니요 |
> | VCenters / InventoryItems | 아니요 | 아니요 |
> | VirtualMachines | 아니요 | 아니요 |
> | VirtualMachines / Extensions | 예 | 예 |
> | VirtualMachines / GuestAgents | 아니요 | 아니요 |
> | VirtualMachines / HybridIdentityMetadata | 아니요 | 아니요 |
> | VirtualMachineTemplates | 아니요 | 아니요 |
> | VirtualNetworks | 아니요 | 아니요 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 예 | 예 |
> | 잔액 | 예 | 예 |
> | 예산 | 예 | 예 |
> | Charges | 예 | 예 |
> | CostTags | 예 | 아니요 |
> | credits | 아니요 | 아니요 |
> | events | 아니요 | 예 |
> | 예측 | 예 | 아니요 |
> | lots | 아니요 | 예 |
> | Marketplace | 예 | 예 |
> | Pricesheets | 예 | 아니요 |
> | products | 아니요 | 예 |
> | ReservationDetails | 예 | 예 |
> | ReservationRecommendationDetails | 예 | 예 |
> | ReservationRecommendations | 예 | 예 |
> | ReservationSummaries | 예 | 예 |
> | ReservationTransactions | 아니요 | 아니요 |
> | 태그 | 아니요 | 아니요 |
> | tenants | 아니요 | 예 |
> | 사용 약관 | 예 | 예 |
> | UsageDetails | 예 | 아니요 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 예 | 예 |
> | serviceAssociationLinks | 예 | 아니요 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registries | 예 | 예 |
> | registries / agentPools | 예 | 예 |
> | registries / builds | 예 | 예 |
> | registries / builds / cancel | 예 | 예 |
> | registries / builds / getLogLink | 예 | 예 |
> | registries / buildTasks | 예 | 예 |
> | registries / buildTasks / steps | 예 | 예 |
> | registries / connectedRegistries | 아니요 | 아니요 |
> | registries / connectedRegistries / deactivate | 예 | 예 |
> | registries / eventGridFilters | 예 | 예 |
> | registries / exportPipelines | 예 | 예 |
> | registries / generateCredentials | 예 | 예 |
> | registries / getBuildSourceUploadUrl | 예 | 예 |
> | registries / GetCredentials | 예 | 예 |
> | registries / importImage | 예 | 예 |
> | registries / importPipelines | 예 | 예 |
> | registries / pipelineRuns | 예 | 예 |
> | registries / privateEndpointConnectionProxies | 예 | 예 |
> | registries / privateEndpointConnectionProxies / validate | 예 | 예 |
> | registries / privateEndpointConnections | 예 | 예 |
> | registries / privateLinkResources | 예 | 예 |
> | registries / queueBuild | 예 | 예 |
> | registries / regenerateCredential | 예 | 예 |
> | registries / regenerateCredentials | 예 | 아니요 |
> | registries / replications | 예 | 예 |
> | registries / runs | 예 | 예 |
> | registries / runs / cancel | 예 | 예 |
> | registries / scheduleRun | 예 | 예 |
> | registries / scopeMaps | 예 | 예 |
> | registries / taskRuns | 예 | 아니요 |
> | registries / tasks | 예 | 예 |
> | registries / tokens | 예 | 예 |
> | registries / updatePolicies | 예 | 아니요 |
> | registries / webhooks | 예 | 예 |
> | registries / webhooks / getCallbackConfig | 예 | 예 |
> | registries / webhooks / ping | 예 | 아니요 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerServices | 예 | 예 |
> | managedClusters | 예 | 예 |
> | ManagedClusters / eventGridFilters | 아니요 | 아니요 |
> | openShiftManagedClusters | 예 | 예 |
> | 스냅샷 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 경고 | 예 | 아니요 |
> | BenefitUtilizationSummaries | 아니요 | 예 |
> | BillingAccounts | 예 | 예 |
> | 예산 | 예 | 예 |
> | calculatePrice | 예 | 예 |
> | CloudConnectors | 예 | 예 |
> | 커넥터 | 예 | 예 |
> | costAllocationRules | 예 | 예 |
> | Departments | 예 | 예 |
> | 차원 | 예 | 예 |
> | EnrollmentAccounts | 예 | 예 |
> | 내보내기 | 예 | 예 |
> | ExternalBillingAccounts | 예 | 예 |
> | ExternalBillingAccounts / Alerts | 예 | 예 |
> | ExternalBillingAccounts / Dimensions | 예 | 예 |
> | ExternalBillingAccounts / Forecast | 예 | 예 |
> | ExternalBillingAccounts / Query | 예 | 예 |
> | ExternalSubscriptions | 예 | 예 |
> | ExternalSubscriptions / Alerts | 예 | 예 |
> | ExternalSubscriptions / Dimensions | 예 | 예 |
> | ExternalSubscriptions / Forecast | 예 | 예 |
> | ExternalSubscriptions / Query | 예 | 예 |
> | fetchPrices | 예 | 예 |
> | 예측 | 예 | 예 |
> | GenerateDetailedCostReport | 아니요 | 아니요 |
> | GenerateReservationDetailsReport | 예 | 예 |
> | 자세한 정보 | 예 | 아니요 |
> | 쿼리 | 예 | 아니요 |
> | register | 아니요 | 예 |
> | Reportconfigs | 예 | 예 |
> | 보고서 | 예 | 예 |
> | ScheduledActions | 예 | 예 |
> | 설정 | 예 | 예 |
> | showbackRules | 예 | 예 |
> | 보기 | 예 | 아니요 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | 아니요 | 아니요 |
> | EnableLockbox | 예 | 예 |
> | requests | 아니요 | 아니요 |
> | TenantOptedIn | 아니요 | 아니요 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | associations | 아니요 | 예 |
> | resourceProviders | 예 | 예 |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 인스턴스 | 예 | 예 |

## <a name="microsoftdashboard"></a>Microsoft.Dashboard

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | grafana | 아니요 | 아니요 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 예 |
> | workspaces / dbWorkspaces | 예 | 예 |
> | workspaces / virtualNetworkPeerings | 예 | 아니요 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 예 | 예 |
> | dataFactories / diagnosticSettings | 예 | 예 |
> | dataFactories / metricDefinitions | 예 | 예 |
> | dataFactorySchema | 예 | 아니요 |
> | factories | 예 | 예 |
> | factories / integrationRuntimes | 예 | 예 |

> [!NOTE]
> 데이터 팩터리에서 Azure SSIS 통합 런타임을 사용하는 경우 실행 비용에 데이터 팩터리 태그로 태그가 지정됩니다. 실행 중인 비용에 새 데이터 팩터리 태그를 적용하려면 Azure SSIS 통합 런타임 실행을 중지한 후 다시 시작해야 합니다.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / dataLakeStoreAccounts | 예 | 예 |
> | accounts / storageAccounts | 예 | 예 |
> | accounts / storageAccounts / containers | 예 | 예 |
> | accounts / transferAnalyticsUnits | 예 | 아니요 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / eventGridFilters | 예 | 예 |
> | accounts / firewallRules | 예 | 아니요 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations | 아니요 | 아니요 |
> | services | 예 | 예 |
> | services / projects | 예 | 예 |
> | SqlMigrationServices | 예 | 예 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | BackupVaults | 예 | 예 |
> | ResourceGuards | 예 | 예 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / shares | 예 | 예 |
> | accounts / shares / datasets | 예 | 예 |
> | accounts / shares / invitations | 예 | 예 |
> | accounts / shares / providersharesubscriptions | 예 | 예 |
> | accounts / shares / synchronizationSettings | 예 | 예 |
> | accounts / sharesubscriptions | 예 | 예 |
> | accounts / sharesubscriptions / consumerSourceDataSets | 예 | 예 |
> | accounts / sharesubscriptions / datasetmappings | 예 | 예 |
> | accounts / sharesubscriptions / triggers | 예 | 아니요 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / keys | 예 | 예 |
> | servers / privateEndpointConnectionProxies | 예 | 예 |
> | servers / privateEndpointConnections | 예 | 예 |
> | servers / privateLinkResources | 예 | 예 |
> | servers / queryTexts | 예 | 예 |
> | servers / recoverableServers | 예 | 예 |
> | servers / resetQueryPerformanceInsightData | 예 | 예 |
> | servers / start | 예 | 예 |
> | servers / stop | 예 | 예 |
> | servers / topQueryStatistics | 예 | 예 |
> | servers / virtualNetworkRules | 예 | 예 |
> | servers / waitStatistics | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | flexibleServers | 예 | 예 |
> | getPrivateDnsZoneSuffix | 아니요 | 아니요 |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / keys | 예 | 예 |
> | servers / privateEndpointConnectionProxies | 예 | 예 |
> | servers / privateEndpointConnections | 예 | 예 |
> | servers / privateLinkResources | 예 | 예 |
> | servers / queryTexts | 예 | 예 |
> | servers / recoverableServers | 예 | 예 |
> | servers / resetQueryPerformanceInsightData | 예 | 예 |
> | servers / start | 예 | 예 |
> | servers / stop | 예 | 예 |
> | servers / topQueryStatistics | 예 | 예 |
> | servers / upgrade | 예 | 예 |
> | servers / virtualNetworkRules | 예 | 예 |
> | servers / waitStatistics | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | flexibleServers | 예 | 예 |
> | getPrivateDnsZoneSuffix | 아니요 | 아니요 |
> | serverGroups | 예 | 예 |
> | serverGroupsv2 | 예 | 예 |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / keys | 예 | 예 |
> | servers / privateEndpointConnectionProxies | 예 | 예 |
> | servers / privateEndpointConnections | 예 | 예 |
> | servers / privateLinkResources | 예 | 예 |
> | servers / queryTexts | 예 | 예 |
> | servers / recoverableServers | 예 | 예 |
> | servers / resetQueryPerformanceInsightData | 예 | 예 |
> | servers / topQueryStatistics | 예 | 예 |
> | servers / virtualNetworkRules | 예 | 예 |
> | servers / waitStatistics | 예 | 예 |
> | serversv2 | 예 | 예 |

## <a name="microsoftdelegatednetwork"></a>Microsoft.DelegatedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Controller | 예 | 예 |
> | delegatedSubnets | 예 | 예 |
> | 오케스트레이터 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 예 | 예 |
> | rollouts | 예 | 예 |
> | serviceTopologies | 예 | 예 |
> | serviceTopologies / services | 예 | 예 |
> | serviceTopologies / services / serviceUnits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 예 | 예 |
> | applicationgroups / applications | 예 | 예 |
> | applicationgroups / desktops | 예 | 예 |
> | applicationgroups / startmenuitems | 예 | 아니요 |
> | hostpools | 예 | 예 |
> | hostpools / msixpackages | 예 | 예 |
> | hostpools / sessionhosts | 예 | 예 |
> | hostpools / sessionhosts / usersessions | 예 | 예 |
> | hostpools / usersessions | 예 | 아니요 |
> | 크기 조정 계획 | 예 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 예 | 예 |
> | ElasticPools / IotHubTenants | 예 | 예 |
> | ElasticPools / IotHubTenants / securitySettings | 예 | 예 |
> | IotHubs | 예 | 예 |
> | IotHubs / eventGridFilters | 예 | 아니요 |
> | IotHubs / 장애 조치(failover) | 아니요 | 예 |
> | IotHubs / securitySettings | 예 | 예 |
> | ProvisioningServices | 예 | 예 |
> | usages | 예 | 예 |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 아니요 | 아니요 |
> | accounts / instances | 아니요 | 예 |
> | registeredSubscriptions | 예 | 아니요 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | pipelines | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labcenters | 예 | 예 |
> | labs | 예 | 예 |
> | labs / environments | 예 | 예 |
> | labs / serviceRunners | 예 | 예 |
> | labs / virtualMachines | 예 | 예 |
> | schedules | 예 | 예 |

## <a name="microsoftdiagnostics"></a>Microsoft 진단

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AzureKB | 아니요 | 아니요 |
> | InsightDiagnostics | 아니요 | 아니요 |
> | solutions | 아니요 | 아니요 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | 예 | 예 |
> | digitalTwinsInstances / endpoints | 예 | 아니요 |
> | digitalTwinsInstances / timeSeriesDatabaseConnections | 아니요 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | 예 | 예 |
> | databaseAccountNames | 예 | 예 |
> | databaseAccounts | 예 | 예 |
> | restorableDatabaseAccounts | 예 | 아니요 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | domains / domainOwnershipIdentifiers | 예 | 예 |
> | generateSsoRequest | 예 | 예 |
> | topLevelDomains | 예 | 예 |
> | validateDomainRegistrationInformation | 예 | 예 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 예 | 예 |
> | lcsprojects / clouddeployments | 예 | 예 |
> | lcsprojects / connectors | 예 | 예 |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 주소 | 예 | 예 |
> | orderItems | 예 | 예 |
> | 주문 | 아니요 | 아니요 |
> | productFamiliesMetadata | 아니요 | 아니요 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | domains / topics | 예 | 예 |
> | eventSubscriptions | 예 | 예 |
> | extensionTopics | 예 | 예 |
> | partnerNamespaces | 예 | 예 |
> | partnerNamespaces / eventChannels | 예 | 예 |
> | partnerRegistrations | 예 | 예 |
> | partnerTopics | 예 | 예 |
> | partnerTopics / eventSubscriptions | 예 | 예 |
> | systemTopics | 예 | 예 |
> | systemTopics / eventSubscriptions | 예 | 아니요 |
> | topics | 예 | 예 |
> | topicTypes | 예 | 아니요 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 예 |
> | namespaces / disasterrecoveryconfigs | 예 | 예 |
> | namespaces / eventhubs | 예 | 예 |
> | namespaces / eventhubs / authorizationrules | 예 | 예 |
> | namespaces / eventhubs / consumergroups | 예 | 예 |
> | namespaces / networkrulesets | 예 | 예 |
> | namespaces / privateEndpointConnections | 예 | 아니요 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | 예 | 예 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | 아니요 | 아니요 |
> | featureProviderNamespaces | 아니요 | 아니요 |
> | featureProviders | 예 | 아니요 |
> | 기능 | 아니요 | 아니요 |
> | providers | 아니요 | 예 |
> | subscriptionFeatureRegistrations | 예 | 아니요 |

## <a name="microsoftfidalgo"></a>Fidalgo

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | devcenters | 아니요 | 아니요 |
> | devcenters/카탈로그 | 아니요 | 아니요 |
> | devcenters/카탈로그/항목 | 아니요 | 아니요 |
> | devcenters/환경 유형 | 아니요 | 아니요 |
> | devcenters/매핑 | 아니요 | 아니요 |
> | machinedefinitions | 아니요 | 아니요 |
> | networksettings | 아니요 | 아니요 |
> | projects | 아니요 | 아니요 |
> | 프로젝트/catalogItems | 아니요 | 아니요 |
> | 프로젝트/환경 | 아니요 | 아니요 |
> | 프로젝트/환경/배포 | 아니요 | 아니요 |
> | 프로젝트/환경 유형 | 아니요 | 아니요 |
> | 프로젝트/풀 | 아니요 | 아니요 |

## <a name="microsoftfluidrelay"></a>FluidRelay

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | fluidRelayServers | 아니요 | 아니요 |
> | fluidRelayServers / fluidRelayContainers | 아니요 | 예 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | enroll | 예 | 예 |
> | galleryitems | 예 | 예 |
> | generateartifactaccessuri | 예 | 예 |
> | myareas | 예 | 예 |
> | myareas / areas | 예 | 예 |
> | myareas / areas / areas | 예 | 예 |
> | myareas / areas / areas / galleryitems | 예 | 예 |
> | myareas / areas / galleryitems | 예 | 예 |
> | myareas / galleryitems | 예 | 아니요 |
> | register | 아니요 | 아니요 |
> | 리소스 | 아니요 | 예 |
> | retrieveresourcesbyid | 예 | 아니요 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftgraph"></a>Microsoft.Graph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AzureAdApplication | 아니요 | 아니요 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | 예 | 예 |
> | autoManagedVmConfigurationProfiles | 예 | 예 |
> | configurationProfileAssignments | 예 | 예 |
> | guestConfigurationAssignments | 예 | 아니요 |
> | software | 아니요 | 예 |
> | softwareUpdateProfile | 예 | 예 |
> | softwareUpdates | 예 | 아니요 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 예 | 예 |
> | sapMonitors | 예 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusterPools | 예 | 예 |
> | clusterPools / clusters | 예 | 예 |
> | clusterPools/클러스터/i n 뷰 | 아니요 | 아니요 |
> | clusterPools/클러스터/serviceConfigs | 아니요 | 아니요 |
> | clusterPools/클러스터/sessionClusters | 예 | 예 |
> | clusterPools/클러스터/sessionClusters/instanceViews | 아니요 | 아니요 |
> | clusterPools/클러스터/sessionClusters/serviceConfigs | 아니요 | 아니요 |
> | clusters | 예 | 예 |
> | clusters / applications | 예 | 예 |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | healthBots | 아니요 | 아니요 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |
> | services / iomtconnectors | 예 | 예 |
> | services / iomtconnectors / connections | 예 | 예 |
> | services / iomtconnectors / mappings | 예 | 예 |
> | services / privateEndpointConnectionProxies | 예 | 예 |
> | services / privateEndpointConnections | 예 | 예 |
> | services / privateLinkResources | 예 | 아니요 |
> | workspaces | 예 | 예 |
> | workspaces / dicomservices | 예 | 예 |
> | workspaces / eventGridFilters | 예 | 아니요 |
> | 작업 영역/r e r 서비스 | 예 | 예 |
> | 작업 영역/i p 커넥터 | 예 | 예 |
> | 작업 영역/대상 커넥터/대상 | 아니요 | 아니요 |
> | 작업 영역/i o t e r 커넥터/대상 | 아니요 | 아니요 |
> | 작업 영역/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | 작업 영역/privateEndpointConnections | 아니요 | 아니요 |
> | 작업 영역/privateLinkResources | 아니요 | 아니요 |

## <a name="microsofthpcworkbench"></a>Microsoft. HpcWorkbench 벤치

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 인스턴스 | 아니요 | 아니요 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | machines | 예 | 예 |
> | machines / assessPatches | 예 | 아니요 |
> | machines / extensions | 예 | 예 |
> | machines / installPatches | 예 | 예 |
> | machines / privateLinkScopes | 아니요 | 아니요 |
> | privateLinkScopes | 예 | 예 |
> | privateLinkScopes / privateEndpointConnectionProxies | 아니요 | 아니요 |
> | privateLinkScopes / privateEndpointConnections | 아니요 | 아니요 |

## <a name="microsofthybridconnectivity"></a>HybridConnectivity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 엔드포인트 | 아니요 | 아니요 |

## <a name="microsofthybridcontainerservice"></a>HybridContainerService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | provisionedClusters | 아니요 | 아니요 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 예 | 예 |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | devices | 아니요 | 아니요 |
> | networkFunctions | 아니요 | 아니요 |
> | networkFunctionVendors | 예 | 예 |
> | registeredSubscriptions | 예 | 예 |
> | vendors | 예 | 예 |
> | vendors / vendorSkus | 예 | 아니요 |
> | 공급 업체/vendorskus/previewSubscriptions | 아니요 | 아니요 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 예 |
> | networkScopes | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionGroups | 예 | 예 |
> | activityLogAlerts | 예 | 예 |
> | alertrules | 예 | 예 |
> | autoscalesettings | 예 | 예 |
> | components | 예 | 예 |
> | components / linkedStorageAccounts | 예 | 예 |
> | components / ProactiveDetectionConfigs | 예 | 예 |
> | diagnosticSettings | 예 | 예 |
> | guestDiagnosticSettings | 예 | 예 |
> | guestDiagnosticSettingsAssociation | 예 | 예 |
> | logprofiles | 예 | 예 |
> | metricAlerts | 예 | 예 |
> | privateLinkScopes | 예 | 예 |
> | privateLinkScopes / privateEndpointConnections | 아니요 | 예 |
> | privateLinkScopes / scopedResources | 예 | 예 |
> | queryPacks | 예 | 예 |
> | queryPacks / queries | 예 | 예 |
> | scheduledQueryRules | 예 | 예 |
> | webtests | 예 | 예 |
> | workbooks | 예 | 예 |
> | workbooktemplates | 예 | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 아니요 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 예 | 예 |
> | IoTApps | 예 | 예 |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | alertTypes | 아니요 | 아니요 |
> | defenderSettings | 예 | 아니요 |
> | onPremiseSensors | 아니요 | 아니요 |
> | recommendationTypes | 아니요 | 아니요 |
> | 센서 | 아니요 | 아니요 |
> | sites | 아니요 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 그래프 | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | 아니요 | 아니요 |
> | deletedVaults | 예 | 예 |
> | hsmPools | 예 | 예 |
> | managedHSMs | 예 | 예 |
> | vaults | 예 | 예 |
> | vaults / accessPolicies | 예 | 예 |
> | vaults / eventGridFilters | 예 | 예 |
> | vaults / keys | 예 | 예 |
> | vaults / keys / versions | 예 | 예 |
> | vaults / secrets | 예 | 아니요 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | connectedClusters | 아니요 | 예 |
> | registeredSubscriptions | 예 | 아니요 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 확장 | 예 | 아니요 |
> | fluxConfigurations | 아니요 | 예 |
> | sourceControlConfigurations | 예 | 아니요 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | clusters / attacheddatabaseconfigurations | 예 | 예 |
> | clusters / databases | 예 | 예 |
> | clusters / databases / dataconnections | 예 | 예 |
> | clusters / databases / eventhubconnections | 예 | 예 |
> | clusters / databases / principalassignments | 예 | 예 |
> | clusters / databases / scripts | 예 | 예 |
> | clusters / dataconnections | 예 | 예 |
> | clusters / principalassignments | 예 | 예 |
> | clusters / sharedidentities | 예 | 아니요 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 예 | 예 |
> | labplans | 예 | 예 |
> | labs | 예 | 예 |
> | users | 아니요 | 아니요 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 예 | 예 |
> | integrationAccounts | 예 | 예 |
> | integrationServiceEnvironments | 예 | 예 |
> | integrationServiceEnvironments / managedApis | 예 | 예 |
> | isolatedEnvironments | 예 | 예 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 예 | 예 |
> | webServices | 예 | 예 |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aisysteminventories | 예 | 예 |
> | virtualclusters | 예 | 예 |
> | workspaces | 예 | 예 |
> | workspaces / batchEndpoints | 예 | 예 |
> | workspaces / batchEndpoints / deployments | 예 | 예 |
> | workspaces / batchEndpoints / deployments / jobs | 아니요 | 아니요 |
> | workspaces / batchEndpoints / jobs | 아니요 | 아니요 |
> | workspaces / codes | 예 | 예 |
> | workspaces / codes / versions | 예 | 아니요 |
> | 작업 영역/구성 요소 | 아니요 | 아니요 |
> | 작업 영역/구성 요소/버전 | 아니요 | 예 |
> | workspaces / computes | 예 | 예 |
> | workspaces / data | 예 | 아니요 |
> | workspaces / datasets | 아니요 | 예 |
> | workspaces / datastores | 예 | 예 |
> | workspaces / environments | 예 | 예 |
> | workspaces / eventGridFilters | 예 | 예 |
> | workspaces / jobs | 예 | 예 |
> | workspaces / labelingJobs | 예 | 예 |
> | workspaces / linkedServices | 예 | 예 |
> | workspaces / models | 예 | 예 |
> | workspaces / models / versions | 예 | 예 |
> | workspaces / onlineEndpoints | 예 | 예 |
> | workspaces / onlineEndpoints / deployments | 예 | 예 |

> [!NOTE]
> 작업 영역 태그는 컴퓨팅 클러스터 및 컴퓨팅 인스턴스로 전파되지 않습니다.

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applyUpdates | 예 | 예 |
> | configurationAssignments | 예 | 예 |
> | maintenanceConfigurations | 예 | 예 |
> | publicMaintenanceConfigurations | 예 | 예 |
> | updates | 아니요 | 아니요 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Identities | 예 | 예 |
> | userAssignedIdentities | 예 | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 예 | 예 |
> | registrationAssignments | 예 | 예 |
> | registrationDefinitions | 예 | 아니요 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | getEntities | 예 | 예 |
> | managementGroups | 예 | 예 |
> | managementGroups / settings | 예 | 아니요 |
> | 리소스 | 아니요 | 예 |
> | startTenantBackfill | 예 | 예 |
> | tenantBackfillStatus | 예 | 아니요 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / creators | 예 | 예 |
> | accounts / eventGridFilters | 예 | 예 |
> | accounts / privateAtlases | 예 | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | macc | 예 | 예 |
> | offers | 아니요 | 예 |
> | offerTypes | 예 | 예 |
> | offerTypes / publishers | 예 | 예 |
> | offerTypes / publishers / offers | 예 | 예 |
> | offerTypes / publishers / offers / plans | 예 | 예 |
> | offerTypes / publishers / offers / plans / agreements | 예 | 예 |
> | offerTypes / publishers / offers / plans / configs | 예 | 예 |
> | offerTypes / publishers / offers / plans / configs / importImage | 예 | 아니요 |
> | privategalleryitems | 아니요 | 예 |
> | privateStoreClient | 예 | 예 |
> | privateStores | 예 | 예 |
> | privateStores / AdminRequestApprovals | 예 | 아니요 |
> | privateStores / billingAccounts | 아니요 | 아니요 |
> | privateStores / bulkCollectionsAction | 아니요 | 아니요 |
> | privateStores / collections | 아니요 | 아니요 |
> | privateStores / collections / offers | 아니요 | 아니요 |
> | privateStores / collections / transferOffers | 아니요 | 아니요 |
> | privateStores / collectionsToSubscriptionsMapping | 아니요 | 예 |
> | privateStores / offers | 예 | 예 |
> | privateStores / offers / acknowledgeNotification | 아니요 | 아니요 |
> | privateStores / queryApprovedPlans | 아니요 | 아니요 |
> | privateStores / queryNotificationsState | 아니요 | 아니요 |
> | privateStores / queryOffers | 아니요 | 아니요 |
> | privateStores / RequestApprovals | 아니요 | 아니요 |
> | privateStores / requestApprovals / query | 아니요 | 아니요 |
> | privateStores / requestApprovals / withdrawPlan | 아니요 | 아니요 |
> | products | 아니요 | 아니요 |
> | publishers | 아니요 | 예 |
> | publishers / offers | 예 | 예 |
> | publishers / offers / amendments | 예 | 아니요 |
> | register | 아니요 | 아니요 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 예 | 예 |
> | updateCommunicationPreference | 예 | 아니요 |

## <a name="microsoftmarketplacenotifications"></a>MarketplaceNotifications

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | reviewsnotifications | 아니요 | 아니요 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | agreements | 아니요 | 아니요 |
> | offertypes | 아니요 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 예 | 예 |
> | mediaservices / accountFilters | 예 | 예 |
> | mediaservices / assets | 예 | 예 |
> | mediaservices / assets / assetFilters | 예 | 예 |
> | mediaservices / contentKeyPolicies | 예 | 예 |
> | mediaservices / eventGridFilters | 예 | 예 |
> | mediaservices / graphInstances | 아니요 | 아니요 |
> | mediaservices / graphTopologies | 예 | 예 |
> | mediaservices / liveEventOperations | 예 | 예 |
> | mediaservices / liveEvents | 예 | 예 |
> | mediaservices / liveEvents / liveOutputs | 예 | 예 |
> | mediaservices / liveOutputOperations | 예 | 예 |
> | mediaservices / mediaGraphs | 예 | 예 |
> | mediaservices / privateEndpointConnectionOperations | 예 | 예 |
> | mediaservices / privateEndpointConnectionProxies | 예 | 예 |
> | mediaservices / privateEndpointConnections | 예 | 예 |
> | mediaservices / streamingEndpointOperations | 예 | 예 |
> | mediaservices / streamingEndpoints | 예 | 예 |
> | mediaservices / streamingLocators | 예 | 예 |
> | mediaservices / streamingPolicies | 예 | 예 |
> | mediaservices / transforms | 예 | 예 |
> | mediaservices / transforms / jobs | 예 | 예 |
> | videoAnalyzers | 예 | 예 |
> | videoAnalyzers / accessPolicies | 아니요 | 아니요 |
> | videoAnalyzers / edgeModules | 아니요 | 아니요 |
> | videoAnalyzers / livePipelines | 아니요 | 아니요 |
> | videoAnalyzers / pipelineJobs | 아니요 | 아니요 |
> | videoAnalyzers / pipelineTopologies | 아니요 | 아니요 |
> | videoAnalyzers / videos | 아니요 | 아니요 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 예 | 예 |
> | migrateprojects | 예 | 예 |
> | moveCollections | 예 | 예 |
> | projects | 예 | 예 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 예 | 예 |
> | objectAnchorsAccounts | 예 | 예 |
> | objectUnderstandingAccounts | 예 | 예 |
> | remoteRenderingAccounts | 예 | 예 |
> | spatialAnchorsAccounts | 예 | 예 |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mobileNetworks | 아니요 | 아니요 |
> | mobileNetworks / dataNetworks | 아니요 | 아니요 |
> | mobileNetworks / services | 아니요 | 아니요 |
> | mobileNetworks / simPolicies | 아니요 | 아니요 |
> | mobileNetworks / sites | 아니요 | 아니요 |
> | mobileNetworks / slices | 아니요 | 아니요 |
> | networks | 아니요 | 아니요 |
> | networks / sites | 아니요 | 아니요 |
> | packetCoreControlPlanes | 아니요 | 아니요 |
> | packetCoreControlPlanes / packetCoreDataPlanes | 아니요 | 아니요 |
> | packetCoreControlPlanes / packetCoreDataPlanes / attachedDataNetworks | 아니요 | 아니요 |
> | packetCores | 아니요 | 아니요 |
> | sims | 아니요 | 아니요 |
> | sims / simProfiles | 아니요 | 아니요 |

## <a name="microsoftmonitor"></a>Microsoft 모니터

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 예 | 예 |
> | netAppAccounts / accountBackups | 예 | 예 |
> | netAppAccounts / capacityPools | 예 | 예 |
> | netAppAccounts / capacityPools / volumes | 예 | 예 |
> | netAppAccounts / capacityPools / volumes / snapshots | 예 | 아니요 |
> | netAppAccounts/capacityPools/볼륨/하위 볼륨 | 아니요 | 아니요 |
> | netAppAccounts/snapshotPolicies | 예 | 예 |
> | netAppAccounts / volumeGroups | 아니요 | 아니요 |

## <a name="microsoftnetworkfunction"></a>Microsoft NetworkFunction

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | azureTrafficCollectors | 예 | 예 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 예 | 예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 | 예 |
> | applicationSecurityGroups | 예 | 예 |
> | azureFirewallFqdnTags | 예 | 예 |
> | azureFirewalls | 예 | 예 |
> | bastionHosts | 예 | 예 |
> | bgpServiceCommunities | 예 | 아니요 |
> | connections | 예 | 예 |
> | ddosCustomPolicies | 예 | 예 |
> | ddosProtectionPlans | 예 | 예 |
> | dnsOperationStatuses | 예 | 아니요 |
> | dnszones | 예 | 예 |
> | dnszones / A | 예 | 예 |
> | dnszones / AAAA | 예 | 예 |
> | dnszones / all | 예 | 예 |
> | dnszones / CAA | 예 | 예 |
> | dnszones / CNAME | 예 | 예 |
> | dnszones / MX | 예 | 예 |
> | dnszones / NS | 예 | 예 |
> | dnszones / PTR | 예 | 예 |
> | dnszones / recordsets | 예 | 예 |
> | dnszones / SOA | 예 | 예 |
> | dnszones / SRV | 예 | 예 |
> | dnszones / TXT | 예 | 예 |
> | expressRouteCircuits | 예 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | expressRouteGateways | 예 | 예 |
> | expressRoutePorts | 예 | 예 |
> | expressRouteServiceProviders | 예 | 예 |
> | firewallPolicies | 예 | 예 |
> | frontdoors | 예, 제한됨([아래 참고](#frontdoor) 참조) | 예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예, 제한됨([아래 참고](#frontdoor) 참조) | 예 |
> | frontdoorWebApplicationFirewallPolicies | 예, 제한됨([아래 참고](#frontdoor) 참조) | 예 |
> | getDnsResourceReference | 예 | 예 |
> | internalNotify | 예 | 예 |
> | ipGroups | 예 | 예 |
> | loadBalancers | 예 | 예 |
> | localNetworkGateways | 예 | 예 |
> | natGateways | 예 | 예 |
> | networkIntentPolicies | 예 | 예 |
> | networkInterfaces | 예 | 예 |
> | networkProfiles | 예 | 예 |
> | networkSecurityGroups | 예 | 예 |
> | networkWatchers | 예 | 예 |
> | networkWatchers / connectionMonitors | 예 | 예 |
> | networkWatchers / flowLogs | 예 | 예 |
> | networkWatchers / lenses | 예 | 예 |
> | networkWatchers / pingMeshes | 예 | 예 |
> | p2sVpnGateways | 예 | 예 |
> | privateDnsOperationStatuses | 예 | 예 |
> | privateDnsZones | 예 | 예 |
> | privateDnsZones / A | 예 | 예 |
> | privateDnsZones / AAAA | 예 | 예 |
> | privateDnsZones / all | 예 | 예 |
> | privateDnsZones / CNAME | 예 | 예 |
> | privateDnsZones / MX | 예 | 예 |
> | privateDnsZones / PTR | 예 | 예 |
> | privateDnsZones / SOA | 예 | 예 |
> | privateDnsZones / SRV | 예 | 예 |
> | privateDnsZones / TXT | 예 | 예 |
> | privateDnsZones / virtualNetworkLinks | 예 | 예 |
> | privateEndpoints | 예 | 예 |
> | privateLinkServices | 예 | 예 |
> | publicIPAddresses | 예 | 예 |
> | publicIPPrefixes | 예 | 예 |
> | routeFilters | 예 | 예 |
> | routeTables | 예 | 예 |
> | serviceEndpointPolicies | 예 | 예 |
> | trafficManagerGeographicHierarchies | 예 | 아니요 |
> | trafficmanagerprofiles | 예 | 예 |
> | trafficmanagerprofiles/heatMaps | 예 | 예 |
> | trafficManagerUserMetricsKeys | 예 | 예 |
> | virtualHubs | 예 | 예 |
> | virtualNetworkGateways | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | virtualNetworks / subnets | 예 | 예 |
> | virtualNetworkTaps | 예 | 예 |
> | virtualWans | 예 | 예 |
> | vpnGateways | 예 | 예 |
> | vpnSites | 예 | 예 |
> | webApplicationFirewallPolicies | 예 | 예 |

<a id="frontdoor"></a>

> [!NOTE]
> Azure Front Door Service의 경우 리소스를 만들 때 태그를 적용할 수 있지만, 태그를 업데이트하거나 추가하는 기능은 현재 지원되지 않습니다.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | 예 | 아니요 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | namespaces / notificationHubs | 예 | 아니요 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | osNamespaces | 아니요 | 아니요 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 예 | 예 |
> | ImportSites | 예 | 예 |
> | MasterSites | 예 | 예 |
> | ServerSites | 예 | 예 |
> | VMwareSites | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | deletedWorkspaces | 예 | 예 |
> | linkTargets | 예 | 예 |
> | querypacks | 예 | 예 |
> | storageInsightConfigs | 예 | 아니요 |
> | workspaces | 예 | 예 |
> | workspaces / dataExports | 예 | 예 |
> | workspaces / dataSources | 예 | 예 |
> | workspaces / linkedServices | 예 | 예 |
> | workspaces / linkedStorageAccounts | 예 | 예 |
> | workspaces / metadata | 예 | 예 |
> | workspaces / query | 예 | 예 |
> | workspaces / scopedPrivateLinkProxies | 예 | 예 |
> | workspaces / storageInsightConfigs | 아니요 | 아니요 |
> | workspaces / tables | 아니요 | 아니요 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 아니요 | 아니요 |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | 아니요 | 아니요 |
> | legacyPeerings | 예 | 아니요 |
> | lookingGlass | 아니요 | 예 |
> | peerAsns | 예 | 아니요 |
> | peerings | 예 | 예 |
> | peeringServiceCountries | 예 | 예 |
> | peeringServiceProviders | 예 | 예 |
> | peeringServices | 예 | 예 |

## <a name="microsoftplayfab"></a>Microsoft.PlayFab

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | PlayerAccountPools | 아니요 | 아니요 |
> | 제목 | 아니요 | 아니요 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | attestations | 예 | 예 |
> | eventGridFilters | 예 | 예 |
> | policyEvents | 예 | 예 |
> | policyMetadata | 예 | 예 |
> | policyStates | 예 | 예 |
> | policyTrackedResources | 예 | 아니요 |
> | remediations | 아니요 | 아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoles | 아니요 | 아니요 |
> | dashboards | 예 | 예 |
> | tenantconfigurations | 아니요 | 아니요 |
> | userSettings | 예 | 아니요 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | 예 | 예 |
> | tenants | 예 | 예 |
> | tenants / workspaces | 예 | 예 |
> | workspaceCollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | 예 | 예 |
> | capacities | 예 | 예 |
> | servers | 예 | 예 |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | enterprisePolicies | 예 | 예 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | deletedAccounts | 예 | 아니요 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 예 | 예 |
> | providerRegistrations / customRollouts | 예 | 예 |
> | providerRegistrations / defaultRollouts | 예 | 아니요 |
> | providerRegistrations/resourceActions | 아니요 | 예 |
> | providerRegistrations / resourceTypeRegistrations | 아니요 | 아니요 |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | deletedAccounts | 예 | 예 |
> | getDefaultAccount | 아니요 | 아니요 |
> | removeDefaultAccount | 아니요 | 아니요 |
> | setDefaultAccount | 아니요 | 아니요 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 영역 | 아니요 | 아니요 |

## <a name="microsoftquota"></a>Microsoft 할당량

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | quotaRequests | 아니요 | 아니요 |
> | quotas | 아니요 | 예 |
> | usages | 예 | 아니요 |

## <a name="microsoftrecommendationsservice"></a>RecommendationsService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 아니요 | 아니요 |
> | accounts / modeling | 아니요 | 아니요 |
> | accounts / serviceEndpoints | 아니요 | 아니요 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 예 | 아니요 |
> | vaults | 예 | 예 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 예 |
> | namespaces / hybridconnections | 예 | 예 |
> | namespaces / hybridconnections / authorizationrules | 예 | 예 |
> | namespaces / privateEndpointConnections | 예 | 예 |
> | namespaces / wcfrelays | 예 | 예 |
> | namespaces / wcfrelays / authorizationrules | 예 | 예 |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appliances | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 쿼리 | 예 | 예 |
> | resourceChangeDetails | 예 | 예 |
> | resourceChanges | 예 | 아니요 |
> | 리소스 | 아니요 | 예 |
> | resourcesHistory | 예 | 예 |
> | subscriptionsStatus | 예 | 아니요 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 예 | 예 |
> | childAvailabilityStatuses | 예 | 예 |
> | childResources | 예 | 아니요 |
> | emergingissues | 아니요 | 아니요 |
> | events | 아니요 | 예 |
> | impactedResources | 예 | 아니요 |
> | metadata | 아니요 | 아니요 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 배포 | 예 | 예 |
> | deployments / operations | 예 | 예 |
> | deploymentScripts | 예 | 예 |
> | deploymentScripts / logs | 예 | 아니요 |
> | deploymentStacks | 아니요 | 아니요 |
> | deploymentStacks/스냅숏 | 아니요 | 아니요 |
> | 링크 | 아니요 | 아니요 |
> | providers | 아니요 | 예 |
> | resourceGroups | 예 | 아니요 |
> | 구독 | 예 | 예 |
> | templateSpecs | 예 | 예 |
> | templateSpecs / versions | 예 | 예 |
> | tenants | 아니요 | 아니요 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | 리소스 | 예 | 예 |
> | saasresources | 아니요 | 아니요 |

## <a name="microsoftscom"></a>Microsoft Scom

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 아니요 | 예 |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clouds | 아니요 | 아니요 |
> | VirtualMachines | 아니요 | 아니요 |
> | VirtualMachineTemplates | 아니요 | 아니요 |
> | VirtualNetworks | 아니요 | 아니요 |
> | vmmservers | 아니요 | 아니요 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 예 | 예 |
> | searchServices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 예 | 예 |
> | advancedThreatProtectionSettings | 예 | 아니요 |
> | 경고 | 아니요 | 예 |
> | alertsSuppressionRules | 예 | 예 |
> | allowedConnections | 예 | 아니요 |
> | antiMalwareSettings | 아니요 | 예 |
> | applicationWhitelistings | 예 | 예 |
> | assessmentMetadata | 예 | 아니요 |
> | assessments | 아니요 | 아니요 |
> | 할당 | 예 | 예 |
> | autoDismissAlertsRules | 예 | 아니요 |
> | automations | 예 | 예 |
> | AutoProvisioningSettings | 예 | 예 |
> | Compliances | 예 | 아니요 |
> | connectedContainerRegistries | 아니요 | 아니요 |
> | 커넥터 | 예 | 아니요 |
> | customAssessmentAutomations | 예 | 예 |
> | Customentitystor및 서명 | 예 | 예 |
> | dataCollectionAgents | 예 | 예 |
> | deviceSecurityGroups | 예 | 예 |
> | discoveredSecuritySolutions | 예 | 예 |
> | externalSecuritySolutions | 예 | 예 |
> | InformationProtectionPolicies | 예 | 예 |
> | ingestionSettings | 아니요 | 아니요 |
> | insights | 아니요 | 예 |
> | iotSecuritySolutions | 예 | 예 |
> | iotSecuritySolutions / analyticsModels | 예 | 예 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 예 | 예 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 예 | 예 |
> | iotSecuritySolutions / iotAlerts | 예 | 예 |
> | iotSecuritySolutions / iotAlertTypes | 예 | 예 |
> | iotSecuritySolutions / iotRecommendations | 예 | 예 |
> | iotSecuritySolutions / iotRecommendationTypes | 예 | 예 |
> | jitNetworkAccessPolicies | 예 | 예 |
> | jitPolicies | 예 | 아니요 |
> | 정책 | 아니요 | 아니요 |
> | pricings | 아니요 | 예 |
> | regulatoryComplianceStandards | 예 | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 예 | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 예 | 예 |
> | secureScoreControlDefinitions | 예 | 예 |
> | secureScoreControls | 예 | 예 |
> | secureScores | 예 | 예 |
> | secureScores / secureScoreControls | 예 | 아니요 |
> | securityConnectors | 예 | 예 |
> | securityContacts | 예 | 예 |
> | securitySolutions | 예 | 예 |
> | securitySolutionsReferenceData | 예 | 예 |
> | securityStatuses | 예 | 예 |
> | securityStatusesSummaries | 예 | 예 |
> | serverVulnerabilityAssessments | 예 | 아니요 |
> | 설정 | 아니요 | 예 |
> | sqlVulnerabilityAssessments | 예 | 아니요 |
> | 기준 | 예 | 예 |
> | subAssessments | 예 | 아니요 |
> | tasks | 아니요 | 아니요 |
> | topologies | 아니요 | 예 |
> | workspaceSettings | 예 | 예 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 아니요 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aggregations | 아니요 | 예 |
> | alertRules | 예 | 예 |
> | alertRuleTemplates | 예 | 예 |
> | automationRules | 예 | 아니요 |
> | bookmarks | 아니요 | 아니요 |
> | cases | 아니요 | 예 |
> | dataConnectors | 예 | 예 |
> | dataConnectorsCheckRequirements | 예 | 예 |
> | enrichment | 아니요 | 아니요 |
> | 엔터티 | 아니요 | 예 |
> | entityQueries | 예 | 예 |
> | entityQueryTemplates | 아니요 | 아니요 |
> | incidents | 아니요 | 아니요 |
> | metadata | 아니요 | 예 |
> | officeConsents | 예 | 아니요 |
> | On보드 Ingstates | 아니요 | 아니요 |
> | 설정 | 아니요 | 예 |
> | sourceControls | 예 | 예 |
> | threatIntelligence | 예 | 예 |
> | watchlists | 예 | 아니요 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoleServices | 예 | 예 |
> | serialPorts | 아니요 | 아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 예 |
> | namespaces / disasterrecoveryconfigs | 예 | 예 |
> | namespaces / eventgridfilters | 예 | 예 |
> | namespaces / networkrulesets | 예 | 예 |
> | namespaces / privateEndpointConnections | 예 | 예 |
> | namespaces / queues | 예 | 예 |
> | namespaces / queues / authorizationrules | 예 | 예 |
> | namespaces / topics | 예 | 예 |
> | namespaces / topics / authorizationrules | 예 | 예 |
> | namespaces / topics / subscriptions | 예 | 예 |
> | namespaces / topics / subscriptions / rules | 예 | 예 |
> | premiumMessagingRegions | 예 | 아니요 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | 예 | 예 |
> | clusters / applications | 예 | 예 |
> | containerGroups | 예 | 예 |
> | containerGroupSets | 예 | 예 |
> | edgeclusters | 예 | 예 |
> | edgeclusters / applications | 예 | 아니요 |
> | managedclusters | 예 | 예 |
> | managedclusters / applications | 아니요 | 아니요 |
> | managedclusters / applications / services | 아니요 | 아니요 |
> | managedclusters / applicationTypes | 아니요 | 아니요 |
> | managedclusters / applicationTypes / versions | 아니요 | 아니요 |
> | managedclusters / nodetypes | 예 | 아니요 |
> | networks | 예 | 예 |
> | secretstores | 예 | 예 |
> | secretstores / certificates | 예 | 예 |
> | secretstores / secrets | 예 | 아니요 |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | containerGroups | 예 | 예 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | linkers | 아니요 | 아니요 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 예 | 예 |
> | providerRegistrations / resourceTypeRegistrations | 아니요 | 아니요 |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SignalR | 예 | 예 |
> | SignalR / eventGridFilters | 예 | 예 |
> | WebPubSub | 예 | 예 |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / accountQuotaPolicies | 예 | 예 |
> | accounts / groupPolicies | 예 | 예 |
> | accounts / jobs | 예 | 아니요 |
> | accounts / models | 아니요 | 예 |
> | accounts / storageContainers | 아니요 | 아니요 |
> | images | 아니요 | 아니요 |
> | quotas | 아니요 | 아니요 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 예 | 아니요 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 예 | 예 |
> | 애플리케이션 | 예 | 예 |
> | jitRequests | 예 | 예 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance / longtermRetentionDatabase / longtermRetentionBackup | 예 | 예 |
> | longtermRetentionServer / longtermRetentionDatabase / longtermRetentionBackup | 예 | 예 |
> | managedInstances | 예 | 예 |
> | managedInstances / databases | 예 | 예 |
> | managedInstances / databases / backupShortTermRetentionPolicies | 예 | 예 |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 예 | 예 |
> | managedInstances / databases / vulnerabilityAssessments | 예 | 예 |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 예 | 예 |
> | managedInstances / encryptionProtector | 예 | 예 |
> | managedInstances / keys | 예 | 예 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 예 | 예 |
> | managedInstances / vulnerabilityAssessments | 예 | 아니요 |
> | servers | 예 | 예 |
> | servers / administrators | 예 | 예 |
> | servers / communicationLinks | 예 | 예 |
> | servers / databases | 예([아래 참고](#sqlnote) 참조) | 예 |
> | servers / encryptionProtector | 예 | 예 |
> | servers / firewallRules | 예 | 예 |
> | servers / keys | 예 | 예 |
> | servers / restorableDroppedDatabases | 예 | 예 |
> | servers / serviceobjectives | 예 | 예 |
> | servers / tdeCertificates | 예 | 예 |
> | virtualClusters | 예 | 아니요 |

<a id="sqlnote"></a>

> [!NOTE]
> master 데이터베이스는 태그를 지원하지 않지만, Azure Synapse Analytics 데이터베이스를 포함한 다른 데이터베이스는 태그를 지원합니다. Azure Synapse Analytics 데이터베이스가 활성(일시 중지 아님) 상태여야 합니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 예 | 예 |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | 예 | 예 |
> | SqlVirtualMachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | 예 | 예 |
> | storageAccounts | 예 | 예 |
> | storageAccounts / blobServices | 예 | 아니요 |
> | storageAccounts / encryptionScopes | 아니요 | 예 |
> | storageAccounts / fileServices | 예 | 예 |
> | storageAccounts / queueServices | 예 | 예 |
> | storageAccounts / services | 예 | 예 |
> | storageAccounts / services / metricDefinitions | 예 | 예 |
> | storageAccounts / tableServices | 예 | 예 |
> | usages | 예 | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | 예 | 예 |
> | caches | 예 | 예 |
> | caches / storageTargets | 예 | 예 |
> | usageModels | 예 | 예 |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 예 | 아니요 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices / registeredServers | 예 | 예 |
> | storageSyncServices / syncGroups | 예 | 예 |
> | storageSyncServices / syncGroups / cloudEndpoints | 예 | 예 |
> | storageSyncServices / syncGroups / serverEndpoints | 예 | 예 |
> | storageSyncServices / workflows | 예 | 아니요 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managers | 예 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | clusters / privateEndpoints | 예 | 아니요 |
> | streamingjobs | 예(아래 참고를 참조) | 예 |

> [!NOTE]
> streamingjobs를 실행 중일 때 태그를 추가할 수 없습니다. 태그를 추가하려면 리소스를 중지합니다.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | 예 | 예 |
> | acceptOwnership | 아니요 | 아니요 |
> | acceptOwnershipStatus | 예 | 예 |
> | 별칭 | 예 | 예 |
> | cancel | 예 | 예 |
> | changeTenantRequest | 예 | 예 |
> | changeTenantStatus | 예 | 예 |
> | CreateSubscription | 예 | 예 |
> | enable | 예 | 아니요 |
> | 정책 | 아니요 | 예 |
> | 이름 바꾸기 | 예 | 예 |
> | SubscriptionDefinitions | 예 | 예 |
> | SubscriptionOperations | 예 | 아니요 |
> | 구독 | 아니요 | 아니요 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | kustoOperations | 아니요 | 아니요 |
> | privateLinkHubs | 예 | 예 |
> | workspaces | 예 | 예 |
> | workspaces / bigDataPools | 예 | 예 |
> | 작업 영역/kustoPools | 예 | 예 |
> | 작업 영역/kustoPools/attacheddatabaseconfigurations | 아니요 | 아니요 |
> | 작업 영역/kustoPools/데이터베이스 | 아니요 | 아니요 |
> | 작업 영역/kustoPools/데이터베이스/dataconnections | 아니요 | 아니요 |
> | workspaces / operationStatuses | 예 | 예 |
> | workspaces / sqlDatabases | 예 | 예 |
> | workspaces / sqlPools | 예 | 예 |

## <a name="microsofttestbase"></a>Microsoft TestBase

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | testBaseAccounts | 아니요 | 아니요 |
> | testBaseAccounts/customerEvents | 아니요 | 아니요 |
> | testBaseAccounts/emailEvents | 아니요 | 아니요 |
> | testBaseAccounts/flightingRings | 아니요 | 아니요 |
> | testBaseAccounts/패키지 | 아니요 | 아니요 |
> | testBaseAccounts/패키지/favoriteProcesses | 아니요 | 아니요 |
> | testBaseAccounts/패키지/osUpdates | 아니요 | 아니요 |
> | testBaseAccounts/Test요약은 | 아니요 | 아니요 |
> | testBaseAccounts/testTypes | 아니요 | 아니요 |
> | testBaseAccounts/사용법 | 아니요 | 아니요 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 예 |
> | environments / accessPolicies | 예 | 아니요 |
> | environments / eventsources | 예 | 예 |
> | environments / privateEndpointConnectionProxies | 아니요 | 아니요 |
> | environments / privateEndpointConnections | 아니요 | 아니요 |
> | environments / privateLinkResources | 아니요 | 아니요 |
> | environments / referenceDataSets | 예 | 아니요 |

## <a name="microsoftvideoindexer"></a>Microsoft. VideoIndexer

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | imageTemplates | 예 | 예 |
> | imageTemplates / runOutputs | 예 | 아니요 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | arczones | 아니요 | 아니요 |
> | resourcepools | 아니요 | 아니요 |
> | vcenters | 아니요 | 아니요 |
> | VCenters / InventoryItems | 아니요 | 아니요 |
> | virtualmachines | 아니요 | 아니요 |
> | virtualmachinetemplates | 아니요 | 아니요 |
> | virtualnetworks | 아니요 | 아니요 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 예 | 예 |
> | dedicatedCloudServices | 예 | 예 |
> | virtualMachines | 예 | 예 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 아니요 |
> | 플랜 | 예 | 예 |
> | registeredSubscriptions | 예 | 예 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 아니요 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 예 | 예 |
> | apiManagementAccounts / apiAcls | 예 | 예 |
> | apiManagementAccounts / apis | 예 | 예 |
> | apiManagementAccounts / apis / apiAcls | 예 | 예 |
> | apiManagementAccounts / apis / connectionAcls | 예 | 예 |
> | apiManagementAccounts / apis / connections | 예 | 예 |
> | apiManagementAccounts / apis / connections / connectionAcls | 예 | 예 |
> | apiManagementAccounts / apis / localizedDefinitions | 예 | 예 |
> | apiManagementAccounts / connectionAcls | 예 | 예 |
> | apiManagementAccounts / connections | 예 | 예 |
> | billingMeters | 예 | 아니요 |
> | certificates | 예 | 예 |
> | connectionGateways | 예 | 예 |
> | connections | 예 | 예 |
> | customApis | 예 | 예 |
> | deletedSites | 예 | 예 |
> | functionAppStacks | 아니요 | 아니요 |
> | generateGithubAccessTokenForAppserviceCLI | 예 | 예 |
> | hostingEnvironments | 예 | 예 |
> | hostingEnvironments / eventGridFilters | 예 | 예 |
> | hostingEnvironments / multiRolePools | 예 | 예 |
> | hostingEnvironments / workerPools | 예 | 예 |
> | kubeEnvironments | 예 | 예 |
> | publishingUsers | 예 | 아니요 |
> | 동영상 추천 기능 | 아니요 | 예 |
> | resourceHealthMetadata | 예 | 아니요 |
> | runtimes | 아니요 | 예 |
> | serverFarms | 예 | 예 |
> | serverFarms / eventGridFilters | 예 | 예 |
> | serverFarms / firstPartyApps | 예 | 예 |
> | serverFarms / firstPartyApps / keyVaultSettings | 예 | 아니요 |
> | sites | 예 | 예 |
> | sites / config  | 예 | 예 |
> | sites / eventGridFilters | 예 | 예 |
> | sites / hostNameBindings | 예 | 예 |
> | sites / networkConfig | 예 | 아니요 |
> | sites / premieraddons | 예 | 예 |
> | sites / slots | 예 | 예 |
> | sites / slots / eventGridFilters | 예 | 예 |
> | sites / slots / hostNameBindings | 예 | 예 |
> | sites / slots / networkConfig | 예 | 예 |
> | sourceControls | 예 | 예 |
> | staticSites | 예 | 예 |
> | validate | 예 | 예 |
> | verifyHostingEnvironmentVnet | 예 | 예 |
> | webAppStacks | 예 | 아니요 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | 예 | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 예 | 예 |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | migrationAgents | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | workloads / instances | 예 | 예 |
> | workloads / versions | 예 | 예 |
> | workloads / versions / artifacts | 예 | 아니요 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | monitors | 아니요 | 아니요 |

## <a name="next-steps"></a>다음 단계

리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
