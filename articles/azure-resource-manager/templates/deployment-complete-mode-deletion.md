---
title: 전체 모드 삭제
description: 리소스 종류가 Azure Resource Manager 템플릿에서 전체 모드 삭제를 처리하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: ea3ea4de33a1680961f078e8e3cc8815da45956e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229902"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>완료 모드 배포를 위한 Azure 리소스의 삭제

이 문서에서는 완료 모드로 배포된 템플릿에 없는 경우 리소스 종류가 삭제를 처리하는 방법을 설명합니다.

**예** 로 표시된 리소스 종류는 종류가 전체 모드로 배포된 템플릿에 없는 경우 삭제됩니다.

**아니요** 로 표시된 리소스 종류는 템플릿에 없는 경우 자동으로 삭제되지 않습니다. 단, 부모 리소스가 삭제되는 경우에는 삭제됩니다. 동작에 대한 전체 설명은 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.

[템플릿에서 둘 이상의 리소스 그룹](./deploy-to-resource-group.md)에 배포하는 경우 배포 작업에 지정된 리소스 그룹의 리소스를 삭제할 수 있습니다. 보조 리소스 그룹의 리소스는 삭제되지 않습니다.

리소스는 리소스 공급자 네임스페이스별로 나열됩니다. 리소스 공급자 네임스페이스를 해당 Azure 서비스 이름과 일치시키려면 [Azure 서비스의 리소스 공급자](../management/azure-services-resource-providers.md)를 참조하세요.

> [!NOTE]
> 템플릿을 전체 모드로 배포하기 전에 항상 [가상 작업](./deploy-what-if.md)을 사용합니다. 가상은 삭제 또는 수정되는 리소스를 표시합니다. 리소스를 실수로 삭제하지 않도록 하려면 가상을 사용합니다.

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
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Commerce](#microsoftcommerce)
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
> - [Microsoft 진단](#microsoftdiagnostics)
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
> - [Microsoft TestBase](#microsofttestbase)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VideoIndexer](#microsoftvideoindexer)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DomainServices | 예 |
> | DomainServices / oucontainer | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | supportProviders | 예 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aadsupportcases | 아니요 |
> | addsservices | 아니요 |
> | agents | 아니요 |
> | anonymousapiusers | 아니요 |
> | 구성 | 아니요 |
> | 로그 | 아니요 |
> | reports | 아니요 |
> | servicehealthmetrics | 아니요 |
> | services | 아니요 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | advisorScore | 예 |
> | 구성 | 예 |
> | generateRecommendations | 예 |
> | metadata | 아니요 |
> | 동영상 추천 기능 | 아니요 |
> | suppressions | 예 |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | farmBeats | 예 |
> | farmBeats / eventGridFilters | 아니요 |
> | farmBeats/확장 | No |
> | farmBeatsExtensionDefinitions | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | actionRules | 예 |
> | 경고 | 예 |
> | alertsList | 예 |
> | alertsMetaData | 예 |
> | alertsSummary | 예 |
> | alertsSummaryList | 예 |
> | migrateFromSmartDetection | 아니요 |
> | resourceHealthAlertRules | 예 |
> | smartDetectorAlertRules | 예 |
> | smartGroups | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | servers | 예 |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 아니요 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedServices | 아니요 |
> | getDomainOwnershipIdentifier | 아니요 |
> | reportFeedback | 예 |
> | 서비스 | 예 |
> | service/eventGridFilters | No |
> | validateServiceName | 예 |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | migrateProjects | No |
> | migrateProjects / assessments | 아니요 |
> | migrateProjects / assessments / assessedApplications | 아니요 |
> | migrateProjects / assessments / assessedApplications / machines | 아니요 |
> | migrateProjects / assessments / assessedMachines | 아니요 |
> | migrateProjects / assessments / assessedMachines / applications | 아니요 |
> | migrateProjects / assessments / machinesToAssess | 아니요 |
> | migrateProjects / sites | 아니요 |
> | migrateProjects / sites / applianceConfigurations | 아니요 |
> | migrateProjects / sites / machines | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | 예 |
> | configurationStores / eventGridFilters | 예 |
> | configurationStores / keyValues | 예 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Spring | 예 |
> | Spring / 앱 | 예 |
> | Spring / 앱 / 배포 | 예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | attestationProviders | 예 |
> | defaultProviders | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | 예 |
> | accessReviewScheduleSettings | 예 |
> | batchResourceCheckAccess | 예 |
> | classicAdministrators | 예 |
> | dataAliases | 예 |
> | dataPolicyManifests | 예 |
> | denyAssignments | 예 |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |
> | elevateAccess | 예 |
> | eligibleChildResources | 예 |
> | findOrphanRoleAssignments | 예 |
> | locks | 아니요 |
> | 권한 | 예 |
> | policyAssignments | 예 |
> | policyDefinitions | 예 |
> | policyExemptions | 예 |
> | policySetDefinitions | 예 |
> | privateLinkAssociations | 예 |
> | providerOperations | 예 |
> | resourceManagementPrivateLinks | 예 |
> | roleAssignmentApprovals | 아니요 |
> | roleAssignments | 예 |
> | roleAssignmentScheduleInstances | 아니요 |
> | roleAssignmentScheduleRequests | 아니요 |
> | roleAssignmentSchedules | 예 |
> | roleAssignmentsUsageMetrics | 예 |
> | roleDefinitions | 예 |
> | roleEligibilityScheduleInstances | 아니요 |
> | roleEligibilityScheduleRequests | 아니요 |
> | roleEligibilitySchedules | 아니요 |
> | roleManagementPolicies | 아니요 |
> | roleManagementPolicyAssignments | 예 |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | bestPractices | No |
> | bestPractices / versions | No |
> | configurationProfileAssignmentIntents | No |
> | configurationProfileAssignments | 예 |
> | configurationProfilePreferences | 예 |
> | configurationProfiles | Yes |
> | configurationProfiles / versions | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | automationAccounts | 예 |
> | automationAccounts / configurations | 예 |
> | automationAccounts / hybridRunbookWorkerGroups | No |
> | automationAccounts / hybridRunbookWorkerGroups / hybridRunbookWorkers | No |
> | automationAccounts / jobs | 예 |
> | automationAccounts / privateEndpointConnectionProxies | 예 |
> | automationAccounts / privateEndpointConnections | 예 |
> | automationAccounts / privateLinkResources | 예 |
> | automationAccounts / runbooks | 예 |
> | automationAccounts / softwareUpdateConfigurations | 예 |
> | automationAccounts / webhooks | 예 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | privateClouds | 예 |
> | privateClouds / addons | 예 |
> | privateClouds / authorizations | 예 |
> | privateClouds / cloudLinks | 예 |
> | privateClouds / clusters | 예 |
> | privateClouds / clusters / datastores | 예 |
> | privateClouds/클러스터/배치 정책 | No |
> | privateClouds/클러스터/virtualMachines | 예 |
> | privateClouds / globalReachConnections | 예 |
> | privateClouds / hcxEnterpriseSites | 예 |
> | privateClouds / scriptExecutions | 아니요 |
> | privateClouds / scriptPackages | 아니요 |
> | privateClouds / scriptPackages / scriptCmdlets | 예 |
> | privateClouds / workloadNetworks | 예 |
> | privateClouds / workloadNetworks / dhcpConfigurations | 예 |
> | privateClouds / workloadNetworks / dnsServices | 아니요 |
> | privateClouds / workloadNetworks / dnsZones | 예 |
> | privateClouds / workloadNetworks / gateways | 예 |
> | privateClouds / workloadNetworks / portMirroringProfiles | 예 |
> | privateClouds/workloadNetworks/publicIPs | 예 |
> | privateClouds / workloadNetworks / segments | 예 |
> | privateClouds / workloadNetworks / virtualMachines | 예 |
> | privateClouds / workloadNetworks / vmGroups | 예 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | environments / accounts | 예 |
> | environments / accounts / namespaces | 예 |
> | environments / accounts / namespaces / configurations | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | b2cDirectories | 예 |
> | b2ctenants | 예 |
> | guestUsages | 예 |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DataControllers | No |
> | DataWarehouseInstances | No |
> | PostgresInstances | No |
> | SqlManagedInstances | No |
> | SqlServerInstances | No |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | autopilotEnvironments | No |
> | dstsServiceAccounts | No |
> | dstsServiceClientIdentities | 아니요 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | sqlServerRegistrations | 예 |
> | sqlServerRegistrations / sqlServers | 예 |

## <a name="microsoftazurepercept"></a>AzurePercept

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 아니요 |
> | 계정/장치 | 예 |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | catalogs | 아니요 |
> | 카탈로그/인증서 | No |
> | 카탈로그/배포 | No |
> | 카탈로그/장치 | No |
> | 카탈로그/이미지 | No |
> | catalogs / products | No |
> | 카탈로그/제품/p s 그룹 | 아니요 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cloudManifestFiles | 예 |
> | linkedSubscriptions | 예 |
> | registrations | 예 |
> | registrations / customerSubscriptions | 예 |
> | registrations / products | 예 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 아니요 |
> | 클러스터/arcsettings | No |
> | 클러스터/arcsettings/확장 | No |
> | galleryImages | No |
> | networkInterfaces | No |
> | virtualHardDisks | 아니요 |
> | virtualmachines | 아니요 |
> | virtualmachines / extensions | No |
> | virtualmachines/hybrididentitymetadata | 예 |
> | virtualNetworks | 예 |

## <a name="microsoftbackupsolutions"></a>Microsoft BackupSolutions

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | VMwareApplications | 예 |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | bareMetalInstances | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | batchAccounts | 예 |
> | batchAccounts / certificates | 예 |
> | batchAccounts / pools | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | billingAccounts | 예 |
> | billingAccounts / agreements | 예 |
> | billingAccounts / appliedReservationOrders | 예 |
> | billingAccounts / billingPermissions | 예 |
> | billingAccounts / billingProfiles | 예 |
> | billingAccounts / billingProfiles / billingPermissions | 예 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 예 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 예 |
> | billingAccounts / billingProfiles / billingSubscriptions | 예 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 예 |
> | billingAccounts / billingProfiles / customers | 예 |
> | billingAccounts / billingProfiles / instructions | 예 |
> | billingAccounts / billingProfiles / invoices | 예 |
> | billingAccounts / billingProfiles / invoices / pricesheet | 예 |
> | billingAccounts / billingProfiles / invoices / transactions | 예 |
> | billingAccounts / billingProfiles / invoiceSections | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 예 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 예 |
> | billingAccounts / billingProfiles / invoiceSections / products | 예 |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | 예 |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | 예 |
> | billingAccounts / billingProfiles / invoiceSections / transactions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / transfers | 예 |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | 예 |
> | billingAccounts / BillingProfiles / patchOperations | 예 |
> | billingAccounts / billingProfiles / paymentMethodLinks | 예 |
> | billingAccounts / billingProfiles / paymentMethods | 예 |
> | billingAccounts / billingProfiles / policies | 예 |
> | billingAccounts / billingProfiles / pricesheet | 예 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 예 |
> | billingAccounts / billingProfiles / products | 예 |
> | billingAccounts / billingProfiles / reservations | 예 |
> | billingAccounts / billingProfiles / transactions | 예 |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | 예 |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | 예 |
> | billingAccounts / billingRoleAssignments | 예 |
> | billingAccounts / billingRoleDefinitions | 예 |
> | billingAccounts / billingSubscriptions | 예 |
> | billingAccounts / billingSubscriptions / elevateRole | 예 |
> | billingAccounts / billingSubscriptions / invoices | 예 |
> | billingAccounts / createBillingRoleAssignment | 예 |
> | billingAccounts / createInvoiceSectionOperations | 예 |
> | billingAccounts / customers | 예 |
> | billingAccounts / customers / billingPermissions | 예 |
> | billingAccounts / customers / billingSubscriptions | 예 |
> | billingAccounts / customers / initiateTransfer | 예 |
> | billingAccounts / customers / policies | 예 |
> | billingAccounts / customers / products | 예 |
> | billingAccounts / customers / transactions | 예 |
> | billingAccounts / customers / transfers | 예 |
> | billingAccounts / customers / transferSupportedAccounts | 예 |
> | billingAccounts / departments | 예 |
> | billingAccounts / departments / billingPermissions | 예 |
> | billingAccounts / departments / billingRoleAssignments | 예 |
> | billingAccounts / departments / billingRoleDefinitions | 예 |
> | billingAccounts / departments / billingSubscriptions | 예 |
> | billingAccounts / departments / enrollmentAccounts | 예 |
> | billingAccounts / enrollmentAccounts | 예 |
> | billingAccounts / enrollmentAccounts / billingPermissions | 예 |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | 예 |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | 예 |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | 예 |
> | billingAccounts / invoices | 예 |
> | billingAccounts / invoices / transactions | 예 |
> | billingAccounts / invoices / transactionSummary | 예 |
> | billingAccounts / invoiceSections | 예 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 예 |
> | billingAccounts / invoiceSections / billingSubscriptions | 예 |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | 예 |
> | billingAccounts / invoiceSections / elevate | 예 |
> | billingAccounts / invoiceSections / initiateTransfer | 예 |
> | billingAccounts / invoiceSections / patchOperations | 예 |
> | billingAccounts / invoiceSections / productMoveOperations | 예 |
> | billingAccounts / invoiceSections / products | 예 |
> | billingAccounts / invoiceSections / products / transfer | 예 |
> | billingAccounts / invoiceSections / products / updateAutoRenew | 예 |
> | billingAccounts / invoiceSections / transactions | 예 |
> | billingAccounts / invoiceSections / transfers | 예 |
> | billingAccounts / lineOfCredit | 예 |
> | billingAccounts / patchOperations | 예 |
> | billingAccounts / payableOverage | 예 |
> | billingAccounts / paymentMethods | 예 |
> | billingAccounts / payNow | 예 |
> | billingAccounts / policies | 예 |
> | billingAccounts / products | 예 |
> | billingAccounts / promotionalCredits | 예 |
> | billingAccounts / reservations | 예 |
> | billingAccounts / savingsPlanOrders | No |
> | billingAccounts / savingsPlanOrders / savingsPlans | No |
> | billingAccounts / savingsPlans | 예 |
> | billingAccounts / transactions | 예 |
> | billingPeriods | 예 |
> | billingPermissions | 예 |
> | billingProperty | 예 |
> | billingRoleAssignments | 예 |
> | billingRoleDefinitions | 예 |
> | createBillingRoleAssignment | 예 |
> | departments | 예 |
> | enrollmentAccounts | 예 |
> | invoices | 아니요 |
> | paymentMethods | No |
> | promotionalCredits | 아니요 |
> | 확장 | 아니요 |
> | transfers | 예 |
> | transfers / acceptTransfer | 예 |
> | transfers / declineTransfer | 예 |
> | transfers / operationStatus | 예 |
> | transfers / validateTransfer | 예 |
> | validateAddress | 예 |

## <a name="microsoftbillingbenefits"></a>Microsoft.BillingBenefits

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | savingsPlanOrderAliases | 예 |
> | validate | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blockchainMembers | 예 |
> | cordaMembers | 예 |
> | watchers | 예 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | TokenServices | 예 |
> | TokenServices / BlockchainNetworks | 예 |
> | TokenServices / Groups | 예 |
> | TokenServices / Groups / Accounts | 예 |
> | TokenServices / TokenTemplates | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blueprintAssignments | 예 |
> | blueprintAssignments / assignmentOperations | 예 |
> | blueprintAssignments / operations | 예 |
> | blueprints | 예 |
> | blueprints / artifacts | 예 |
> | blueprints / versions | 예 |
> | blueprints / versions / artifacts | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | botServices | 예 |
> | botServices / channels | 예 |
> | botServices / connections | 예 |
> | botServices / privateEndpointConnectionProxies | No |
> | botServices / privateEndpointConnections | No |
> | botServices / privateLinkResources | 예 |
> | hostSettings | 예 |
> | 언어 | 예 |
> | 템플릿 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Redis | 예 |
> | Redis / EventGridFilters | 예 |
> | Redis / privateEndpointConnectionProxies | 예 |
> | Redis / privateEndpointConnectionProxies / validate | 예 |
> | Redis / privateEndpointConnections | 예 |
> | Redis / privateLinkResources | 예 |
> | redisEnterprise | 예 |
> | redisEnterprise / databases | 아니요 |
> | RedisEnterprise / privateEndpointConnectionProxies | 예 |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | 예 |
> | RedisEnterprise / privateEndpointConnections | 예 |
> | RedisEnterprise / privateLinkResources | 예 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appliedReservations | 예 |
> | autoQuotaIncrease | 예 |
> | calculateExchange | 예 |
> | calculatePrice | 예 |
> | calculatePurchasePrice | 예 |
> | catalogs | 예 |
> | commercialReservationOrders | 예 |
> | 교환 | 예 |
> | ownReservations | 예 |
> | placePurchaseOrder | 예 |
> | reservationOrders | 예 |
> | reservationOrders / calculateRefund | 예 |
> | reservationOrders / merge | 예 |
> | reservationOrders / reservations | 예 |
> | reservationOrders / reservations / revisions | 예 |
> | reservationOrders / return | 예 |
> | reservationOrders / split | 예 |
> | reservationOrders / swap | 예 |
> | reservations | 예 |
> | resourceProviders | 예 |
> | 리소스 | 예 |
> | validateReservationOrder | 예 |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | sites | 아니요 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 예 |
> | CdnWebApplicationFirewallPolicies | 예 |
> | edgenodes | 아니요 |
> | 프로필 | 예 |
> | profiles / afdendpoints | 예 |
> | profiles / afdendpoints / routes | 아니요 |
> | profiles / customdomains | 아니요 |
> | profiles / endpoints | 예 |
> | profiles / endpoints / customdomains | 예 |
> | profiles / endpoints / origingroups | 예 |
> | profiles / endpoints / origins | 예 |
> | profiles / origingroups | 아니요 |
> | profiles / origingroups / origins | 아니요 |
> | profiles / rulesets | 아니요 |
> | profiles / rulesets / rules | 아니요 |
> | profiles / secrets | 아니요 |
> | profiles / securitypolicies | 예 |
> | validateProbe | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | certificateOrders | 예 |
> | certificateOrders / certificates | 예 |
> | validateCertificateRegistrationInformation | 예 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 변경 내용 | 아니요 |
> | changeSnapshots | No |
> | computeChanges | 아니요 |
> | profile | 예 |
> | resourceChanges | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | domainNames | 예 |
> | domainNames / capabilities | 예 |
> | domainNames / internalLoadBalancers | 예 |
> | domainNames / serviceCertificates | 예 |
> | domainNames / slots | 예 |
> | domainNames / slots / roles | 예 |
> | domainNames / slots / roles / metricDefinitions | 예 |
> | domainNames / slots / roles / metrics | 예 |
> | moveSubscriptionResources | 예 |
> | operatingSystemFamilies | 예 |
> | operatingSystems | 예 |
> | quotas | 예 |
> | resourceTypes | 예 |
> | validateSubscriptionMoveAvailability | 예 |
> | virtualMachines | 예 |
> | virtualMachines / diagnosticSettings | 예 |
> | virtualMachines / metricDefinitions | 예 |
> | virtualMachines / metrics | 예 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 예 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | expressRouteCrossConnections | 예 |
> | expressRouteCrossConnections / peerings | 예 |
> | gatewaySupportedDevices | 예 |
> | networkSecurityGroups | 예 |
> | quotas | 예 |
> | reservedIps | 예 |
> | virtualNetworks | 예 |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 예 |
> | virtualNetworks / virtualNetworkPeerings | 예 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 아니요 |
> | disks | 아니요 |
> | images | 예 |
> | osImages | 예 |
> | osPlatformImages | 예 |
> | publicImages | 예 |
> | quotas | 예 |
> | storageAccounts | 예 |
> | storageAccounts / blobServices | 예 |
> | storageAccounts / fileServices | 예 |
> | storageAccounts / metricDefinitions | 예 |
> | storageAccounts / metrics | 예 |
> | storageAccounts / queueServices | 예 |
> | storageAccounts / services | 예 |
> | storageAccounts / services / diagnosticSettings | 예 |
> | storageAccounts / services / metricDefinitions | 예 |
> | storageAccounts / services / metrics | 예 |
> | storageAccounts / tableServices | 예 |
> | storageAccounts / vmImages | 예 |
> | vmImages | 예 |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | nodes | 예 |

## <a name="microsoftcodesigning"></a>코드 서명

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 공동 디자인 계정 | No |
> | codeSigningAccounts/certificateProfiles | 예 |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 플랜 | 예 |
> | registeredSubscriptions | 예 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / privateEndpointConnectionProxies | 예 |
> | accounts / privateEndpointConnections | 예 |
> | accounts / privateLinkResources | 예 |
> | deletedAccounts | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilitySets | 예 |
> | cloudServices | 예 |
> | cloudServices / networkInterfaces | 예 |
> | cloudServices / publicIPAddresses | 예 |
> | cloudServices / roleInstances | 예 |
> | cloudServices / roleInstances / networkInterfaces | 예 |
> | cloudServices / roles | 예 |
> | diskAccesses | 예 |
> | diskEncryptionSets | 예 |
> | disks | 예 |
> | galleries | 예 |
> | galleries / applications | 예 |
> | galleries / applications / versions | 예 |
> | galleries / images | 예 |
> | galleries / images / versions | 예 |
> | hostGroups | 예 |
> | hostGroups / hosts | 예 |
> | images | 예 |
> | proximityPlacementGroups | 예 |
> | restorePointCollections | 예 |
> | restorePointCollections / restorePoints | 예 |
> | sharedVMExtensions | 예 |
> | sharedVMExtensions / versions | 예 |
> | sharedVMImages | 예 |
> | sharedVMImages / versions | 예 |
> | 스냅샷 | 예 |
> | sshPublicKeys | 예 |
> | virtualMachines | 예 |
> | virtualMachines / extensions | 예 |
> | virtualMachines / metricDefinitions | 예 |
> | virtualMachines / runCommands | 예 |
> | virtualMachineScaleSets | 예 |
> | virtualMachineScaleSets / extensions | 예 |
> | virtualMachineScaleSets / networkInterfaces | 예 |
> | virtualMachineScaleSets / publicIPAddresses | 예 |
> | virtualMachineScaleSets / virtualMachines | 예 |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | RateCard | 예 |
> | UsageAggregates | 예 |

## <a name="microsoftconfidentialledger"></a>ConfidentialLedger

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 원장 | 예 |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CacheNodes | No |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | platformAccounts | 예 |
> | registeredSubscriptions | 예 |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 클러스터 | No |
> | 데이터 저장소 | No |
> | 호스트 | No |
> | ResourcePools | No |
> | VCenters | No |
> | VCenters / InventoryItems | 아니요 |
> | VirtualMachines | No |
> | VirtualMachines / Extensions | 예 |
> | VirtualMachines / GuestAgents | 아니요 |
> | VirtualMachines / HybridIdentityMetadata | 아니요 |
> | VirtualMachineTemplates | No |
> | VirtualNetworks | 아니요 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AggregatedCost | 예 |
> | 잔액 | 예 |
> | 예산 | 예 |
> | Charges | 예 |
> | CostTags | 예 |
> | credits | 아니요 |
> | events | 예 |
> | 예측 | 예 |
> | lots | 예 |
> | Marketplace | 예 |
> | Pricesheets | 예 |
> | products | 예 |
> | ReservationDetails | 예 |
> | ReservationRecommendationDetails | 예 |
> | ReservationRecommendations | 예 |
> | ReservationSummaries | 예 |
> | ReservationTransactions | 아니요 |
> | 태그 | 아니요 |
> | tenants | 예 |
> | 사용 약관 | 예 |
> | UsageDetails | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerGroups | 예 |
> | serviceAssociationLinks | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registries | 예 |
> | registries / agentPools | 예 |
> | registries / builds | 예 |
> | registries / builds / cancel | 예 |
> | registries / builds / getLogLink | 예 |
> | registries / buildTasks | 예 |
> | registries / buildTasks / steps | 예 |
> | registries / connectedRegistries | 아니요 |
> | registries / connectedRegistries / deactivate | 예 |
> | registries / eventGridFilters | 예 |
> | registries / exportPipelines | 예 |
> | registries / generateCredentials | 예 |
> | registries / getBuildSourceUploadUrl | 예 |
> | registries / GetCredentials | 예 |
> | registries / importImage | 예 |
> | registries / importPipelines | 예 |
> | registries / pipelineRuns | 예 |
> | registries / privateEndpointConnectionProxies | 예 |
> | registries / privateEndpointConnectionProxies / validate | 예 |
> | registries / privateEndpointConnections | 예 |
> | registries / privateLinkResources | 예 |
> | registries / queueBuild | 예 |
> | registries / regenerateCredential | 예 |
> | registries / regenerateCredentials | 예 |
> | registries / replications | 예 |
> | registries / runs | 예 |
> | registries / runs / cancel | 예 |
> | registries / scheduleRun | 예 |
> | registries / scopeMaps | 예 |
> | registries / taskRuns | 예 |
> | registries / tasks | 예 |
> | registries / tokens | 예 |
> | registries / updatePolicies | 예 |
> | registries / webhooks | 예 |
> | registries / webhooks / getCallbackConfig | 예 |
> | registries / webhooks / ping | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerServices | 예 |
> | managedClusters | 예 |
> | ManagedClusters / eventGridFilters | 아니요 |
> | openShiftManagedClusters | 예 |
> | 스냅샷 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 경고 | 예 |
> | BenefitUtilizationSummaries | 예 |
> | BillingAccounts | 예 |
> | 예산 | 예 |
> | calculatePrice | 예 |
> | CloudConnectors | 예 |
> | 커넥터 | 예 |
> | costAllocationRules | 예 |
> | Departments | 예 |
> | 차원 | 예 |
> | EnrollmentAccounts | 예 |
> | 내보내기 | 예 |
> | ExternalBillingAccounts | 예 |
> | ExternalBillingAccounts / Alerts | 예 |
> | ExternalBillingAccounts / Dimensions | 예 |
> | ExternalBillingAccounts / Forecast | 예 |
> | ExternalBillingAccounts / Query | 예 |
> | ExternalSubscriptions | 예 |
> | ExternalSubscriptions / Alerts | 예 |
> | ExternalSubscriptions / Dimensions | 예 |
> | ExternalSubscriptions / Forecast | 예 |
> | ExternalSubscriptions / Query | 예 |
> | fetchPrices | 예 |
> | 예측 | 예 |
> | GenerateDetailedCostReport | 아니요 |
> | GenerateReservationDetailsReport | 예 |
> | 자세한 정보 | 예 |
> | 쿼리 | 예 |
> | register | 예 |
> | Reportconfigs | 예 |
> | 보고서 | 예 |
> | ScheduledActions | 예 |
> | 설정 | 예 |
> | showbackRules | 예 |
> | 보기 | 예 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DisableLockbox | 아니요 |
> | EnableLockbox | 예 |
> | requests | 아니요 |
> | TenantOptedIn | 아니요 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | associations | 예 |
> | resourceProviders | 예 |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 인스턴스 | 예 |

## <a name="microsoftdashboard"></a>Microsoft 대시보드

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | grafana | 아니요 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | 예 |
> | workspaces / dbWorkspaces | 예 |
> | workspaces / virtualNetworkPeerings | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | catalogs | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataFactories | 예 |
> | dataFactories / diagnosticSettings | 예 |
> | dataFactories / metricDefinitions | 예 |
> | dataFactorySchema | 예 |
> | factories | 예 |
> | factories / integrationRuntimes | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / dataLakeStoreAccounts | 예 |
> | accounts / storageAccounts | 예 |
> | accounts / storageAccounts / containers | 예 |
> | accounts / transferAnalyticsUnits | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / eventGridFilters | 예 |
> | accounts / firewallRules | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DatabaseMigrations | 아니요 |
> | services | 예 |
> | services / projects | 예 |
> | SqlMigrationServices | 예 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | BackupVaults | 예 |
> | ResourceGuards | 예 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / shares | 예 |
> | accounts / shares / datasets | 예 |
> | accounts / shares / invitations | 예 |
> | accounts / shares / providersharesubscriptions | 예 |
> | accounts / shares / synchronizationSettings | 예 |
> | accounts / sharesubscriptions | 예 |
> | accounts / sharesubscriptions / consumerSourceDataSets | 예 |
> | accounts / sharesubscriptions / datasetmappings | 예 |
> | accounts / sharesubscriptions / triggers | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers / privateEndpointConnectionProxies | 예 |
> | servers / privateEndpointConnections | 예 |
> | servers / privateLinkResources | 예 |
> | servers / queryTexts | 예 |
> | servers / recoverableServers | 예 |
> | servers / resetQueryPerformanceInsightData | 예 |
> | servers / start | 예 |
> | servers / stop | 예 |
> | servers / topQueryStatistics | 예 |
> | servers / virtualNetworkRules | 예 |
> | servers / waitStatistics | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | flexibleServers | 예 |
> | getPrivateDnsZoneSuffix | 아니요 |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers / privateEndpointConnectionProxies | 예 |
> | servers / privateEndpointConnections | 예 |
> | servers / privateLinkResources | 예 |
> | servers / queryTexts | 예 |
> | servers / recoverableServers | 예 |
> | servers / resetQueryPerformanceInsightData | 예 |
> | servers / start | 예 |
> | servers / stop | 예 |
> | servers / topQueryStatistics | 예 |
> | servers / upgrade | 예 |
> | servers / virtualNetworkRules | 예 |
> | servers / waitStatistics | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | flexibleServers | 예 |
> | getPrivateDnsZoneSuffix | No |
> | serverGroups | 예 |
> | serverGroupsv2 | 예 |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers / privateEndpointConnectionProxies | 예 |
> | servers / privateEndpointConnections | 예 |
> | servers / privateLinkResources | 예 |
> | servers / queryTexts | 예 |
> | servers / recoverableServers | 예 |
> | servers / resetQueryPerformanceInsightData | 예 |
> | servers / topQueryStatistics | 예 |
> | servers / virtualNetworkRules | 예 |
> | servers / waitStatistics | 예 |
> | serversv2 | 예 |

## <a name="microsoftdelegatednetwork"></a>Microsoft.DelegatedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Controller | Yes |
> | delegatedSubnets | Yes |
> | 오케스트레이터 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | artifactSources | 예 |
> | rollouts | 예 |
> | serviceTopologies | 예 |
> | serviceTopologies / services | 예 |
> | serviceTopologies / services / serviceUnits | 예 |
> | 단계 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationgroups | 예 |
> | applicationgroups / applications | 예 |
> | applicationgroups / desktops | 예 |
> | applicationgroups / startmenuitems | 예 |
> | hostpools | 예 |
> | hostpools / msixpackages | 예 |
> | hostpools / sessionhosts | 예 |
> | hostpools / sessionhosts / usersessions | 예 |
> | hostpools / usersessions | 예 |
> | 크기 조정 계획 | 예 |
> | workspaces | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ElasticPools | 예 |
> | ElasticPools / IotHubTenants | 예 |
> | ElasticPools / IotHubTenants / securitySettings | 예 |
> | IotHubs | 예 |
> | IotHubs / eventGridFilters | 예 |
> | IotHubs / 장애 조치(failover) | 예 |
> | IotHubs / securitySettings | 예 |
> | ProvisioningServices | 예 |
> | usages | 예 |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 아니요 |
> | accounts / instances | 예 |
> | registeredSubscriptions | 예 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | pipelines | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | controllers | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labcenters | 예 |
> | labs | 예 |
> | labs / environments | 예 |
> | labs / serviceRunners | 예 |
> | labs / virtualMachines | 예 |
> | schedules | 예 |

## <a name="microsoftdiagnostics"></a>Microsoft 진단

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AzureKB | No |
> | InsightDiagnostics | 아니요 |
> | solutions | 아니요 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | digitalTwinsInstances | 예 |
> | digitalTwinsInstances / endpoints | 예 |
> | digitalTwinsInstances / timeSeriesDatabaseConnections | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cassandraClusters | 예 |
> | databaseAccountNames | 예 |
> | databaseAccounts | 예 |
> | restorableDatabaseAccounts | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | 예 |
> | domains / domainOwnershipIdentifiers | 예 |
> | generateSsoRequest | 예 |
> | topLevelDomains | 예 |
> | validateDomainRegistrationInformation | 예 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | lcsprojects | 예 |
> | lcsprojects / clouddeployments | 예 |
> | lcsprojects / connectors | 예 |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 주소 | 예 |
> | orderItems | 예 |
> | 주문 | No |
> | productFamiliesMetadata | 아니요 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | 예 |
> | domains / topics | 예 |
> | eventSubscriptions | 예 |
> | extensionTopics | 예 |
> | partnerNamespaces | 예 |
> | partnerNamespaces / eventChannels | 예 |
> | partnerRegistrations | 예 |
> | partnerTopics | 예 |
> | partnerTopics / eventSubscriptions | 예 |
> | systemTopics | 예 |
> | systemTopics / eventSubscriptions | 예 |
> | topics | 예 |
> | topicTypes | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | 네임스페이스 | 예 |
> | namespaces / authorizationrules | 예 |
> | namespaces / disasterrecoveryconfigs | 예 |
> | namespaces / eventhubs | 예 |
> | namespaces / eventhubs / authorizationrules | 예 |
> | namespaces / eventhubs / consumergroups | 예 |
> | namespaces / networkrulesets | 예 |
> | namespaces / privateEndpointConnections | 예 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | experimentWorkspaces | 예 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | featureConfigurations | 아니요 |
> | featureProviderNamespaces | 아니요 |
> | featureProviders | 예 |
> | 기능 | 아니요 |
> | providers | 예 |
> | subscriptionFeatureRegistrations | 예 |

## <a name="microsoftfidalgo"></a>Fidalgo

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | devcenters | No |
> | devcenters/카탈로그 | No |
> | devcenters/카탈로그/항목 | No |
> | devcenters/환경 유형 | No |
> | devcenters/매핑 | No |
> | machinedefinitions | No |
> | networksettings | 아니요 |
> | projects | 아니요 |
> | 프로젝트/catalogItems | No |
> | 프로젝트/환경 | No |
> | 프로젝트/환경/배포 | No |
> | 프로젝트/환경 유형 | No |
> | projects / pools | No |

## <a name="microsoftfluidrelay"></a>Microsoft.FluidRelay

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | fluidRelayServers | No |
> | fluidRelayServers / fluidRelayContainers | 예 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | enroll | 예 |
> | galleryitems | 예 |
> | generateartifactaccessuri | 예 |
> | myareas | 예 |
> | myareas / areas | 예 |
> | myareas / areas / areas | 예 |
> | myareas / areas / areas / galleryitems | 예 |
> | myareas / areas / galleryitems | 예 |
> | myareas / galleryitems | 예 |
> | register | 아니요 |
> | 리소스 | 예 |
> | retrieveresourcesbyid | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftgraph"></a>Microsoft.Graph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AzureAdApplication | 아니요 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | autoManagedAccounts | 예 |
> | autoManagedVmConfigurationProfiles | 예 |
> | configurationProfileAssignments | 예 |
> | guestConfigurationAssignments | 예 |
> | software | 예 |
> | softwareUpdateProfile | 예 |
> | softwareUpdates | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hanaInstances | 예 |
> | sapMonitors | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedHSMs | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusterPools | 예 |
> | clusterPools / clusters | 예 |
> | clusterPools / clusters / instanceViews | No |
> | clusterPools / clusters / serviceConfigs | No |
> | clusterPools / clusters / sessionClusters | Yes |
> | clusterPools / clusters / sessionClusters / instanceViews | No |
> | clusterPools / clusters / sessionClusters / serviceConfigs | 아니요 |
> | clusters | 예 |
> | clusters / applications | 예 |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | healthBots | 아니요 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |
> | services / iomtconnectors | 예 |
> | services / iomtconnectors / connections | 예 |
> | services / iomtconnectors / mappings | 예 |
> | services / privateEndpointConnectionProxies | 예 |
> | services / privateEndpointConnections | 예 |
> | services / privateLinkResources | 예 |
> | workspaces | 예 |
> | workspaces / dicomservices | 예 |
> | workspaces / eventGridFilters | 예 |
> | 작업 영역/r e r 서비스 | Yes |
> | 작업 영역/i p 커넥터 | Yes |
> | 작업 영역/대상 커넥터/대상 | No |
> | 작업 영역/i o t e r 커넥터/대상 | No |
> | 작업 영역/privateEndpointConnectionProxies | No |
> | 작업 영역/privateEndpointConnections | No |
> | 작업 영역/privateLinkResources | No |

## <a name="microsofthpcworkbench"></a>Microsoft. HpcWorkbench 벤치

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 인스턴스 | 아니요 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | machines | 예 |
> | machines / assessPatches | 예 |
> | machines / extensions | 예 |
> | machines / installPatches | 예 |
> | machines / privateLinkScopes | 아니요 |
> | privateLinkScopes | 예 |
> | privateLinkScopes / privateEndpointConnectionProxies | 아니요 |
> | privateLinkScopes / privateEndpointConnections | 아니요 |

## <a name="microsofthybridconnectivity"></a>HybridConnectivity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 엔드포인트 | No |

## <a name="microsofthybridcontainerservice"></a>HybridContainerService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | provisionedClusters | 아니요 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataManagers | 예 |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | devices | 아니요 |
> | networkFunctions | No |
> | networkFunctionVendors | 예 |
> | registeredSubscriptions | 예 |
> | vendors | 예 |
> | vendors / vendorSkus | 예 |
> | 공급 업체/vendorskus/previewSubscriptions | 아니요 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 예 |
> | networkScopes | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appTemplates | 예 |
> | IoTApps | 예 |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | alertTypes | No |
> | defenderSettings | 예 |
> | onPremiseSensors | No |
> | recommendationTypes | No |
> | 센서 | 아니요 |
> | sites | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 그래프 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedManagedHSMs | 아니요 |
> | deletedVaults | 예 |
> | hsmPools | 예 |
> | managedHSMs | 예 |
> | vaults | 예 |
> | vaults / accessPolicies | 예 |
> | vaults / eventGridFilters | 예 |
> | vaults / keys | 예 |
> | vaults / keys / versions | 예 |
> | vaults / secrets | 예 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | connectedClusters | 예 |
> | registeredSubscriptions | 예 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 확장 | 예 |
> | fluxConfigurations | 예 |
> | sourceControlConfigurations | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | clusters / attacheddatabaseconfigurations | 예 |
> | clusters / databases | 예 |
> | clusters / databases / dataconnections | 예 |
> | clusters / databases / eventhubconnections | 예 |
> | clusters / databases / principalassignments | 예 |
> | clusters / databases / scripts | 예 |
> | clusters / dataconnections | 예 |
> | clusters / principalassignments | 예 |
> | clusters / sharedidentities | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labaccounts | 예 |
> | labplans | 예 |
> | labs | 예 |
> | users | 아니요 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hostingEnvironments | 예 |
> | integrationAccounts | 예 |
> | integrationServiceEnvironments | 예 |
> | integrationServiceEnvironments / managedApis | 예 |
> | isolatedEnvironments | 예 |
> | workflows | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | commitmentPlans | 예 |
> | webServices | 예 |
> | 작업 영역 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aisysteminventories | 예 |
> | virtualclusters | 예 |
> | workspaces | 예 |
> | workspaces / batchEndpoints | 예 |
> | workspaces / batchEndpoints / deployments | 예 |
> | workspaces / batchEndpoints / deployments / jobs | 아니요 |
> | workspaces / batchEndpoints / jobs | 아니요 |
> | workspaces / codes | 예 |
> | workspaces / codes / versions | 예 |
> | workspaces / components | No |
> | workspaces / components / versions | 예 |
> | workspaces / computes | 예 |
> | workspaces / data | 예 |
> | workspaces / datasets | 예 |
> | workspaces / datastores | 예 |
> | workspaces / environments | 예 |
> | workspaces / eventGridFilters | 예 |
> | workspaces / jobs | 예 |
> | workspaces / labelingJobs | 예 |
> | workspaces / linkedServices | 예 |
> | workspaces / models | 예 |
> | workspaces / models / versions | 예 |
> | workspaces / onlineEndpoints | 예 |
> | workspaces / onlineEndpoints / deployments | 예 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applyUpdates | 예 |
> | configurationAssignments | 예 |
> | maintenanceConfigurations | 예 |
> | publicMaintenanceConfigurations | 예 |
> | updates | 아니요 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Identities | 예 |
> | userAssignedIdentities | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 예 |
> | registrationAssignments | 예 |
> | registrationDefinitions | 예 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | getEntities | 예 |
> | managementGroups | 예 |
> | managementGroups / settings | 예 |
> | 리소스 | 예 |
> | startTenantBackfill | 예 |
> | tenantBackfillStatus | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / creators | 예 |
> | accounts / eventGridFilters | 예 |
> | accounts / privateAtlases | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | macc | 예 |
> | offers | 예 |
> | offerTypes | 예 |
> | offerTypes / publishers | 예 |
> | offerTypes / publishers / offers | 예 |
> | offerTypes / publishers / offers / plans | 예 |
> | offerTypes / publishers / offers / plans / agreements | 예 |
> | offerTypes / publishers / offers / plans / configs | 예 |
> | offerTypes / publishers / offers / plans / configs / importImage | 예 |
> | privategalleryitems | 예 |
> | privateStoreClient | 예 |
> | privateStores | 예 |
> | privateStores / AdminRequestApprovals | 예 |
> | privateStores / billingAccounts | No |
> | privateStores / bulkCollectionsAction | No |
> | privateStores/컬렉션 | No |
> | privateStores/컬렉션/제안 | No |
> | privateStores/컬렉션/전송자 제안 | No |
> | privateStores / collectionsToSubscriptionsMapping | 예 |
> | privateStores / offers | 예 |
> | privateStores / offers / acknowledgeNotification | 아니요 |
> | privateStores / queryApprovedPlans | 아니요 |
> | privateStores / queryNotificationsState | 아니요 |
> | privateStores/queryOffers | 아니요 |
> | privateStores / RequestApprovals | 아니요 |
> | privateStores / requestApprovals / query | 아니요 |
> | privateStores / requestApprovals / withdrawPlan | 아니요 |
> | products | 아니요 |
> | publishers | 예 |
> | publishers / offers | 예 |
> | publishers / offers / amendments | 예 |
> | register | 아니요 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicDevServices | 예 |
> | updateCommunicationPreference | 예 |

## <a name="microsoftmarketplacenotifications"></a>MarketplaceNotifications

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | reviewsnotifications | 아니요 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | agreements | 아니요 |
> | offertypes | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mediaservices | 예 |
> | mediaservices / accountFilters | 예 |
> | mediaservices / assets | 예 |
> | mediaservices / assets / assetFilters | 예 |
> | mediaservices / contentKeyPolicies | 예 |
> | mediaservices / eventGridFilters | 예 |
> | mediaservices / graphInstances | 아니요 |
> | mediaservices / graphTopologies | 예 |
> | mediaservices / liveEventOperations | 예 |
> | mediaservices / liveEvents | 예 |
> | mediaservices / liveEvents / liveOutputs | 예 |
> | mediaservices / liveOutputOperations | 예 |
> | mediaservices / mediaGraphs | 예 |
> | mediaservices / privateEndpointConnectionOperations | 예 |
> | mediaservices / privateEndpointConnectionProxies | 예 |
> | mediaservices / privateEndpointConnections | 예 |
> | mediaservices / streamingEndpointOperations | 예 |
> | mediaservices / streamingEndpoints | 예 |
> | mediaservices / streamingLocators | 예 |
> | mediaservices / streamingPolicies | 예 |
> | mediaservices / transforms | 예 |
> | mediaservices / transforms / jobs | 예 |
> | videoAnalyzers | 예 |
> | 비디오 분석기/Accesspolicy | No |
> | videoAnalyzers / edgeModules | 아니요 |
> | 비디오 분석기/livePipelines | No |
> | 비디오 분석기/pipelineJobs | No |
> | 비디오 분석기/pipelineTopologies | No |
> | 비디오 분석기/비디오 | 아니요 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | assessmentProjects | 예 |
> | migrateprojects | 예 |
> | moveCollections | 예 |
> | projects | 예 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | 예 |
> | objectAnchorsAccounts | 예 |
> | objectUnderstandingAccounts | 예 |
> | remoteRenderingAccounts | 예 |
> | spatialAnchorsAccounts | 예 |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mobileNetworks | No |
> | mobileNetworks/dataNetworks | No |
> | mobileNetworks/서비스 | No |
> | mobileNetworks/이상 정책 | No |
> | mobileNetworks/사이트 | No |
> | mobileNetworks/조각 | 아니요 |
> | networks | 아니요 |
> | networks / sites | No |
> | packetCoreControlPlanes | No |
> | packetCoreControlPlanes / packetCoreDataPlanes | No |
> | packetCoreControlPlanes / packetCoreDataPlanes / attachedDataNetworks | No |
> | packetCores | No |
> | sims | No |
> | sims / simProfiles | No |

## <a name="microsoftmonitor"></a>Microsoft 모니터

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | netAppAccounts | 예 |
> | netAppAccounts / accountBackups | 예 |
> | netAppAccounts / capacityPools | 예 |
> | netAppAccounts / capacityPools / volumes | 예 |
> | netAppAccounts / capacityPools / volumes / snapshots | 예 |
> | netAppAccounts/capacityPools/볼륨/하위 볼륨 | No |
> | netAppAccounts/snapshotPolicies | Yes |
> | netAppAccounts / volumeGroups | 아니요 |

## <a name="microsoftnetworkfunction"></a>Microsoft NetworkFunction

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | azureTrafficCollectors | Yes |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationGateways | 예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 |
> | applicationSecurityGroups | 예 |
> | azureFirewallFqdnTags | 예 |
> | azureFirewalls | 예 |
> | bastionHosts | 예 |
> | bgpServiceCommunities | 예 |
> | connections | 예 |
> | ddosCustomPolicies | 예 |
> | ddosProtectionPlans | 예 |
> | dnsOperationStatuses | 예 |
> | dnszones | 예 |
> | dnszones / A | 예 |
> | dnszones / AAAA | 예 |
> | dnszones / all | 예 |
> | dnszones / CAA | 예 |
> | dnszones / CNAME | 예 |
> | dnszones / MX | 예 |
> | dnszones / NS | 예 |
> | dnszones / PTR | 예 |
> | dnszones / recordsets | 예 |
> | dnszones / SOA | 예 |
> | dnszones / SRV | 예 |
> | dnszones / TXT | 예 |
> | expressRouteCircuits | 예 |
> | expressRouteCrossConnections | 예 |
> | expressRouteGateways | 예 |
> | expressRoutePorts | 예 |
> | expressRouteServiceProviders | 예 |
> | firewallPolicies | 예 |
> | frontdoors | 예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예 |
> | frontdoorWebApplicationFirewallPolicies | 예 |
> | getDnsResourceReference | 예 |
> | internalNotify | 예 |
> | ipGroups | 예 |
> | loadBalancers | 예 |
> | localNetworkGateways | 예 |
> | natGateways | 예 |
> | networkIntentPolicies | 예 |
> | networkInterfaces | 예 |
> | networkProfiles | 예 |
> | networkSecurityGroups | 예 |
> | networkWatchers | 예 |
> | networkWatchers / connectionMonitors | 예 |
> | networkWatchers / flowLogs | 예 |
> | networkWatchers / lenses | 예 |
> | networkWatchers / pingMeshes | 예 |
> | p2sVpnGateways | 예 |
> | privateDnsOperationStatuses | 예 |
> | privateDnsZones | 예 |
> | privateDnsZones / A | 예 |
> | privateDnsZones / AAAA | 예 |
> | privateDnsZones / all | 예 |
> | privateDnsZones / CNAME | 예 |
> | privateDnsZones / MX | 예 |
> | privateDnsZones / PTR | 예 |
> | privateDnsZones / SOA | 예 |
> | privateDnsZones / SRV | 예 |
> | privateDnsZones / TXT | 예 |
> | privateDnsZones / virtualNetworkLinks | 예 |
> | privateEndpoints | 예 |
> | privateLinkServices | 예 |
> | publicIPAddresses | 예 |
> | publicIPPrefixes | 예 |
> | routeFilters | 예 |
> | routeTables | 예 |
> | serviceEndpointPolicies | 예 |
> | trafficManagerGeographicHierarchies | 예 |
> | trafficmanagerprofiles | 예 |
> | trafficmanagerprofiles / heatMaps | 예 |
> | trafficManagerUserMetricsKeys | 예 |
> | virtualHubs | 예 |
> | virtualNetworkGateways | 예 |
> | virtualNetworks | 예 |
> | virtualNetworks / subnets | 예 |
> | virtualNetworkTaps | 예 |
> | virtualWans | 예 |
> | vpnGateways | 예 |
> | vpnSites | 예 |
> | webApplicationFirewallPolicies | 예 |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | NotebookProxies | 예 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | namespaces / notificationHubs | 예 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | osNamespaces | 아니요 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | HyperVSites | 예 |
> | ImportSites | 예 |
> | MasterSites | 예 |
> | ServerSites | 예 |
> | VMwareSites | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | deletedWorkspaces | 예 |
> | linkTargets | 예 |
> | querypacks | 예 |
> | storageInsightConfigs | 예 |
> | workspaces | 예 |
> | workspaces / dataExports | 예 |
> | workspaces / dataSources | 예 |
> | workspaces / linkedServices | 예 |
> | workspaces / linkedStorageAccounts | 예 |
> | workspaces / metadata | 예 |
> | workspaces / query | 예 |
> | workspaces / scopedPrivateLinkProxies | 예 |
> | workspaces / storageInsightConfigs | 아니요 |
> | workspaces / tables | 아니요 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managementassociations | 아니요 |
> | managementconfigurations | 예 |
> | solutions | 예 |
> | 뷰 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | 아니요 |
> | legacyPeerings | 예 |
> | lookingGlass | 예 |
> | peerAsns | 예 |
> | peerings | 예 |
> | peeringServiceCountries | 예 |
> | peeringServiceProviders | 예 |
> | peeringServices | 예 |

## <a name="microsoftplayfab"></a>Microsoft.PlayFab

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | PlayerAccountPools | No |
> | 제목 | 아니요 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | attestations | 예 |
> | eventGridFilters | 예 |
> | policyEvents | 예 |
> | policyMetadata | 예 |
> | policyStates | 예 |
> | policyTrackedResources | 예 |
> | remediations | 아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | consoles | 아니요 |
> | dashboards | 예 |
> | tenantconfigurations | 아니요 |
> | userSettings | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | 예 |
> | tenants | 예 |
> | tenants / workspaces | 예 |
> | workspaceCollections | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | autoScaleVCores | 예 |
> | capacities | 예 |
> | servers | 예 |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | enterprisePolicies | 예 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | deletedAccounts | 예 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | 예 |
> | providerRegistrations / customRollouts | 예 |
> | providerRegistrations / defaultRollouts | 예 |
> | providerRegistrations/resourceActions | 예 |
> | providerRegistrations / resourceTypeRegistrations | 아니요 |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | deletedAccounts | 예 |
> | getDefaultAccount | 아니요 |
> | removeDefaultAccount | 아니요 |
> | setDefaultAccount | 아니요 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 작업 영역 | No |

## <a name="microsoftquota"></a>Microsoft 할당량

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | quotaRequests | 아니요 |
> | quotas | 예 |
> | usages | 예 |

## <a name="microsoftrecommendationsservice"></a>RecommendationsService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 아니요 |
> | 계정/모델링 | No |
> | 계정/serviceEndpoints | 아니요 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | backupProtectedItems | 예 |
> | vaults | 예 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | OpenShiftClusters | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | namespaces / authorizationrules | 예 |
> | namespaces / hybridconnections | 예 |
> | namespaces / hybridconnections / authorizationrules | 예 |
> | namespaces / privateEndpointConnections | 예 |
> | namespaces / wcfrelays | 예 |
> | namespaces / wcfrelays / authorizationrules | 예 |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appliances | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 쿼리 | 예 |
> | resourceChangeDetails | 예 |
> | resourceChanges | 예 |
> | 리소스 | 예 |
> | resourcesHistory | 예 |
> | subscriptionsStatus | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilityStatuses | 예 |
> | childAvailabilityStatuses | 예 |
> | childResources | 예 |
> | emergingissues | 아니요 |
> | events | 예 |
> | impactedResources | 예 |
> | metadata | 아니요 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 배포 | 예 |
> | deployments / operations | 예 |
> | deploymentScripts | 예 |
> | deploymentScripts / logs | 예 |
> | deploymentStacks | No |
> | deploymentStacks / snapshots | 아니요 |
> | 링크 | 아니요 |
> | providers | 예 |
> | resourceGroups | 예 |
> | 구독 | 예 |
> | templateSpecs | 예 |
> | templateSpecs / versions | 예 |
> | tenants | 아니요 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | 리소스 | 예 |
> | saasresources | 아니요 |

## <a name="microsoftscom"></a>Microsoft.Scom

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedInstances | 예 |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clouds | 아니요 |
> | VirtualMachines | 아니요 |
> | VirtualMachineTemplates | No |
> | VirtualNetworks | No |
> | vmmservers | 아니요 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 예 |
> | searchServices | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 예 |
> | advancedThreatProtectionSettings | 예 |
> | 경고 | 예 |
> | alertsSuppressionRules | 예 |
> | allowedConnections | 예 |
> | antiMalwareSettings | 예 |
> | applicationWhitelistings | 예 |
> | assessmentMetadata | 예 |
> | assessments | 아니요 |
> | 할당 | Yes |
> | autoDismissAlertsRules | 예 |
> | automations | 예 |
> | AutoProvisioningSettings | 예 |
> | Compliances | 예 |
> | connectedContainerRegistries | 아니요 |
> | 커넥터 | 예 |
> | customAssessmentAutomations | Yes |
> | Customentitystor및 서명 | Yes |
> | dataCollectionAgents | 예 |
> | deviceSecurityGroups | 예 |
> | discoveredSecuritySolutions | 예 |
> | externalSecuritySolutions | 예 |
> | InformationProtectionPolicies | 예 |
> | ingestionSettings | 아니요 |
> | insights | 예 |
> | iotSecuritySolutions | 예 |
> | iotSecuritySolutions / analyticsModels | 예 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 예 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 예 |
> | iotSecuritySolutions / iotAlerts | 예 |
> | iotSecuritySolutions / iotAlertTypes | 예 |
> | iotSecuritySolutions / iotRecommendations | 예 |
> | iotSecuritySolutions / iotRecommendationTypes | 예 |
> | jitNetworkAccessPolicies | 예 |
> | jitPolicies | 예 |
> | 정책 | 아니요 |
> | pricings | 예 |
> | regulatoryComplianceStandards | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 예 |
> | secureScoreControlDefinitions | 예 |
> | secureScoreControls | 예 |
> | secureScores | 예 |
> | secureScores / secureScoreControls | 예 |
> | securityConnectors | Yes |
> | securityContacts | 예 |
> | securitySolutions | 예 |
> | securitySolutionsReferenceData | 예 |
> | securityStatuses | 예 |
> | securityStatusesSummaries | 예 |
> | serverVulnerabilityAssessments | 예 |
> | 설정 | 예 |
> | sqlVulnerabilityAssessments | 예 |
> | 기준 | Yes |
> | subAssessments | 예 |
> | tasks | 아니요 |
> | topologies | 예 |
> | workspaceSettings | 예 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aggregations | 예 |
> | alertRules | 예 |
> | alertRuleTemplates | 예 |
> | automationRules | 예 |
> | bookmarks | 아니요 |
> | cases | 예 |
> | dataConnectors | 예 |
> | dataConnectorsCheckRequirements | 예 |
> | enrichment | 아니요 |
> | 엔터티 | 예 |
> | entityQueries | 예 |
> | entityQueryTemplates | 아니요 |
> | incidents | 아니요 |
> | metadata | 예 |
> | officeConsents | 예 |
> | onboardingStates | 아니요 |
> | 설정 | 예 |
> | sourceControls | 예 |
> | threatIntelligence | 예 |
> | watchlists | 예 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | consoleServices | 예 |
> | serialPorts | 아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | namespaces / authorizationrules | 예 |
> | namespaces / disasterrecoveryconfigs | 예 |
> | namespaces / eventgridfilters | 예 |
> | namespaces / networkrulesets | 예 |
> | namespaces / privateEndpointConnections | 예 |
> | namespaces / queues | 예 |
> | namespaces / queues / authorizationrules | 예 |
> | namespaces / topics | 예 |
> | namespaces / topics / authorizationrules | 예 |
> | namespaces / topics / subscriptions | 예 |
> | namespaces / topics / subscriptions / rules | 예 |
> | premiumMessagingRegions | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | clusters | 예 |
> | clusters / applications | 예 |
> | containerGroups | 예 |
> | containerGroupSets | 예 |
> | edgeclusters | 예 |
> | edgeclusters / applications | 예 |
> | managedclusters | 예 |
> | managedclusters / applications | 아니요 |
> | managedclusters / applications / services | 아니요 |
> | managedclusters / applicationTypes | 아니요 |
> | managedclusters / applicationTypes / versions | 아니요 |
> | managedclusters / nodetypes | 예 |
> | networks | 예 |
> | secretstores | 예 |
> | secretstores / certificates | 예 |
> | secretstores / secrets | 예 |
> | volumes | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | containerGroups | 예 |
> | gateways | 예 |
> | networks | 예 |
> | secrets | 예 |
> | volumes | 예 |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | linkers | 아니요 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | 예 |
> | providerRegistrations / resourceTypeRegistrations | 아니요 |
> | rollouts | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SignalR | 예 |
> | SignalR / eventGridFilters | 예 |
> | WebPubSub | 예 |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / accountQuotaPolicies | 예 |
> | accounts / groupPolicies | 예 |
> | accounts / jobs | 예 |
> | 계정/모델 | 예 |
> | accounts / storageContainers | 아니요 |
> | images | 아니요 |
> | quotas | 아니요 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridUseBenefits | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationDefinitions | 예 |
> | 애플리케이션 | 예 |
> | jitRequests | 예 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedInstances | 예 |
> | managedInstances / databases | 예 |
> | managedInstances / databases / backupShortTermRetentionPolicies | 예 |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 예 |
> | managedInstances / databases / vulnerabilityAssessments | 예 |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 예 |
> | managedInstances / encryptionProtector | 예 |
> | managedInstances / keys | 예 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 예 |
> | managedInstances / vulnerabilityAssessments | 예 |
> | servers | 예 |
> | servers / administrators | 예 |
> | servers / communicationLinks | 예 |
> | servers / databases | 예 |
> | servers / encryptionProtector | 예 |
> | servers / firewallRules | 예 |
> | servers / keys | 예 |
> | servers / restorableDroppedDatabases | 예 |
> | servers / serviceobjectives | 예 |
> | servers / tdeCertificates | 예 |
> | virtualClusters | 예 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 예 |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | 예 |
> | SqlVirtualMachines | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedAccounts | 예 |
> | storageAccounts | 예 |
> | storageAccounts / blobServices | 예 |
> | storageAccounts/대 범위 | 예 |
> | storageAccounts / fileServices | 예 |
> | storageAccounts / queueServices | 예 |
> | storageAccounts / services | 예 |
> | storageAccounts / services / metricDefinitions | 예 |
> | storageAccounts / tableServices | 예 |
> | usages | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | amlFilesystems | 예 |
> | caches | 예 |
> | caches / storageTargets | 예 |
> | usageModels | 예 |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | replicationGroups | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | 예 |
> | storageSyncServices / registeredServers | 예 |
> | storageSyncServices / syncGroups | 예 |
> | storageSyncServices / syncGroups / cloudEndpoints | 예 |
> | storageSyncServices / syncGroups / serverEndpoints | 예 |
> | storageSyncServices / workflows | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managers | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | clusters / privateEndpoints | 예 |
> | streamingjobs | 예 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | acceptChangeTenant | 예 |
> | acceptOwnership | 아니요 |
> | acceptOwnershipStatus | 예 |
> | 별칭 | 예 |
> | cancel | 예 |
> | changeTenantRequest | 예 |
> | changeTenantStatus | 예 |
> | CreateSubscription | 예 |
> | enable | 예 |
> | 정책 | 예 |
> | 이름 바꾸기 | 예 |
> | SubscriptionDefinitions | 예 |
> | SubscriptionOperations | 예 |
> | 구독 | 아니요 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | kustoOperations | No |
> | privateLinkHubs | 예 |
> | workspaces | 예 |
> | workspaces / bigDataPools | 예 |
> | workspaces / kustoPools | Yes |
> | workspaces / kustoPools / attacheddatabaseconfigurations | No |
> | workspaces / kustoPools / databases | No |
> | workspaces / kustoPools / databases / dataconnections | No |
> | workspaces / operationStatuses | 예 |
> | workspaces / sqlDatabases | 예 |
> | workspaces / sqlPools | 예 |

## <a name="microsofttestbase"></a>Microsoft.TestBase

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | testBaseAccounts | No |
> | testBaseAccounts / customerEvents | No |
> | testBaseAccounts / emailEvents | No |
> | testBaseAccounts / flightingRings | No |
> | testBaseAccounts / packages | No |
> | testBaseAccounts / packages / favoriteProcesses | No |
> | testBaseAccounts / packages / osUpdates | No |
> | testBaseAccounts / testSummaries | No |
> | testBaseAccounts / testTypes | No |
> | testBaseAccounts / usages | 아니요 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | environments / accessPolicies | 예 |
> | environments / eventsources | 예 |
> | environments / privateEndpointConnectionProxies | 아니요 |
> | environments / privateEndpointConnections | 아니요 |
> | environments / privateLinkResources | 아니요 |
> | environments / referenceDataSets | 예 |

## <a name="microsoftvideoindexer"></a>Microsoft.VideoIndexer

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 아니요 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | imageTemplates | 예 |
> | imageTemplates / runOutputs | 예 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | arczones | 아니요 |
> | resourcepools | 아니요 |
> | vcenters | 아니요 |
> | VCenters / InventoryItems | 아니요 |
> | virtualmachines | 아니요 |
> | virtualmachinetemplates | 아니요 |
> | virtualnetworks | 아니요 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | 예 |
> | dedicatedCloudServices | 예 |
> | virtualMachines | 예 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 플랜 | 예 |
> | registeredSubscriptions | 예 |
## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | apiManagementAccounts | 예 |
> | apiManagementAccounts / apiAcls | 예 |
> | apiManagementAccounts / apis | 예 |
> | apiManagementAccounts / apis / apiAcls | 예 |
> | apiManagementAccounts / apis / connectionAcls | 예 |
> | apiManagementAccounts / apis / connections | 예 |
> | apiManagementAccounts / apis / connections / connectionAcls | 예 |
> | apiManagementAccounts / apis / localizedDefinitions | 예 |
> | apiManagementAccounts / connectionAcls | 예 |
> | apiManagementAccounts / connections | 예 |
> | billingMeters | 예 |
> | certificates | 예 |
> | connectionGateways | 예 |
> | connections | 예 |
> | customApis | 예 |
> | deletedSites | 예 |
> | functionAppStacks | 아니요 |
> | generateGithubAccessTokenForAppserviceCLI | 예 |
> | hostingEnvironments | 예 |
> | hostingEnvironments / eventGridFilters | 예 |
> | hostingEnvironments / multiRolePools | 예 |
> | hostingEnvironments / workerPools | 예 |
> | kubeEnvironments | 예 |
> | publishingUsers | 예 |
> | 동영상 추천 기능 | 예 |
> | resourceHealthMetadata | 예 |
> | runtimes | 예 |
> | serverFarms | 예 |
> | serverFarms / eventGridFilters | 예 |
> | serverFarms / firstPartyApps | 예 |
> | serverFarms / firstPartyApps / keyVaultSettings | 예 |
> | sites | 예 |
> | sites/config  | 예 |
> | sites / eventGridFilters | 예 |
> | sites / hostNameBindings | 예 |
> | sites / networkConfig | 예 |
> | sites / premieraddons | 예 |
> | sites / slots | 예 |
> | sites / slots / eventGridFilters | 예 |
> | sites / slots / hostNameBindings | 예 |
> | sites / slots / networkConfig | 예 |
> | sourceControls | 예 |
> | staticSites | 예 |
> | validate | 예 |
> | verifyHostingEnvironmentVnet | 예 |
> | webAppStacks | 예 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | multipleActivationKeys | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DeviceServices | 예 |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | migrationAgents | No |
> | 작업 | 아니요 |
> | workloads / instances | 예 |
> | workloads / versions | 예 |
> | workloads / versions / artifacts | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | monitors | 아니요 |

## <a name="next-steps"></a>다음 단계

쉼표로 구분된 값의 파일과 동일한 데이터를 가져오려면 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)를 다운로드합니다.