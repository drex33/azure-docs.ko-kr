---
title: 지원되는 Azure Resource Manager 리소스 종류
description: Azure Resource Graph 및 변경 내역에서 지원되는 Azure Resource Manager 리소스 종류 목록을 제공합니다.
ms.date: 10/12/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 9b3ce54d9554164b375ed1a08fdbcce8429dbb21
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054812"
---
# <a name="azure-resource-graph-table-and-resource-type-reference"></a>Azure Resource Graph 테이블 및 리소스 종류 참조

Azure Resource Graph는 [Azure Resource Manager](../../../azure-resource-manager/management/overview.md)의 다음 **리소스 종류** 를 지원합니다. 각 **리소스 종류** 는 Resource Graph에 있는 **테이블** 의 일부입니다.

## <a name="advisorresources"></a>advisorresources

이 테이블에 대한 예제 쿼리는[advisorresources를 위한 Resource Graph 샘플 쿼리 ](../samples/samples-by-table.md#advisorresources)를 참조하세요.

- microsoft.advisor/configurations
- microsoft.advisor/recommendations
  - 샘플 쿼리:[Azure Advisor에서 비용 절감 요약 가져오기](../samples/samples-by-category.md#get-cost-savings-summary-from-azure-advisor)
  - 예제 쿼리: [Arc 사용 서버가 최신 릴리스 에이전트 버전을 실행 하지 않는 목록을](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version) 표시 합니다.
- microsoft.advisor/recommendations/suppressions
- microsoft.advisor/suppressions

## <a name="alertsmanagementresources"></a>alertsmanagementresources

- microsoft.alertsmanagement/alerts

## <a name="desktopvirtualizationresources"></a>desktopvirtualizationresources

- microsoft. 감사/상태
- microsoft. n e t/대상
- microsoft. n e t/대상/기능
- microsoft.desktopvirtualization/hostpools/sessionhosts

## <a name="extendedlocationresources"></a>extendedlocationresources

이 테이블에 대한 샘플 쿼리는 [extendedlocationresources위한 Resource Graph 샘플 쿼리](../samples/samples-by-table.md#extendedlocationresources)를 참조하세요.

- microsoft.extendedlocation/customlocations/enabledresourcetypes
  - 샘플 쿼리: [Azure Arc 지원 사용자 지정 위치에 사용하도록 설정된 리소스 종류 가져오기](../samples/samples-by-category.md#get-enabled-resource-types-for-azure-arc-enabled-custom-locations)
  - 샘플 쿼리: [VMware 또는 SCVMM이 사용하도록 설정된 Azure Arc 지원 사용자 지정 위치 나열](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)

## <a name="guestconfigurationresources"></a>guestconfigurationresources

이 테이블에 대한 샘플 쿼리는 [guestconfigurationresources관한 Resource Graph 예제 쿼리](../samples/samples-by-table.md#guestconfigurationresources)를 참조하세요.

- microsoft.guestconfiguration/guestconfigurationassignments
  - 샘플 쿼리: [게스트 구성 정책 범위에 있는 머신 수](../samples/samples-by-category.md#count-machines-in-scope-of-guest-configuration-policies)
  - 샘플 쿼리: [비규격 게스트 구성 할당의 수](../samples/samples-by-category.md#count-of-non-compliant-guest-configuration-assignments)
  - 샘플 쿼리: [머신이 게스트 구성 할당에 규격화되지 않은 모든 이유 찾기](../samples/samples-by-category.md#find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments)

## <a name="healthresources"></a>healthresources

이 테이블에 대한 샘플 쿼리는 [healthresource에 관한 Resource Graph 예제 쿼리](../samples/samples-by-table.md#healthresources)를 참조하세요.

- microsoft.resourcehealth/availabilitystatuses
  - 샘플 쿼리: [가상 머신의 가용성 상태 및 구독 Id 별 수](../samples/samples-by-category.md#count-of-virtual-machines-by-availability-state-and-subscription-id)
  - 샘플 쿼리: [리소스 id 별 가상 머신 및 관련 가용성 상태 목록](../samples/samples-by-category.md#list-of-virtual-machines-and-associated-availability-states-by-resource-ids)
  - 샘플 쿼리: [리소스 Id 및 리소스 그룹을 사용 하는 가용성 상태 및 전원 상태별로 가상 머신 목록](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - 샘플 쿼리: [리소스 id에서 사용할 수 없는 가상 머신 목록](../samples/samples-by-category.md#list-of-virtual-machines-that-are-not-available-by-resource-ids)

## <a name="iotsecurityresources"></a>iotsecurityresources

- microsoft. iotsecurity/위치/s i d/devicegroups/alerts
  - 예제 쿼리: [지난 30 일 동안 모든 새 경고 가져오기](../samples/samples-by-category.md#get-all-new-alerts-from-the-past-30-days)
- microsoft. iotsecurity/위치/장치/장치
  - 샘플 쿼리: [네트워크에 있는 IoT 장치의 수를 작업 시스템 별로 계산 합니다](../samples/samples-by-category.md#count-how-many-iot-devices-there-are-in-your-network-by-operation-system) .
- microsoft. iotsecurity/위치/s i d 그룹/권장 사항
  - 예제 쿼리: [높은 심각도 권장 사항을 모두 가져옵니다](../samples/samples-by-category.md#get-all-high-severity-recommendations) .
- microsoft. iotsecurity/onpremisesensors
- microsoft .exe 보안/센서
  - 예제 쿼리: [유형별 모든 센서 수](../samples/samples-by-category.md#count-all-sensors-by-type)
- microsoft .exe 보안/사이트
  - 예제 쿼리: [특정 태그 값을 포함 하는 사이트 나열](../samples/samples-by-category.md#list-sites-with-a-specific-tag-value)

## <a name="kubernetesconfigurationresources"></a>kubernetesconfigurationresources

이 테이블에 대한 샘플 쿼리는 [kubernetesconfigurationresources에 관한 Resource Graph sample queries](../samples/samples-by-table.md#kubernetesconfigurationresources)를 참조하세요.

- microsoft.kubernetesconfiguration/extensions
  - 샘플 쿼리: [Azure Monitor 확장이 있는 모든 Azure Arc 지원 Kubernetes 클러스터 나열](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-with-azure-monitor-extension)
  - 샘플 쿼리: [Azure Monitor 확장이 없는 모든 Azure Arc 지원 Kubernetes 클러스터 나열](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
- kubernetesconfiguration/fluxconfigurations
- microsoft.kubernetesconfiguration/sourcecontrolconfigurations

## <a name="maintenanceresources"></a>maintenanceresources

- microsoft.maintenance/applyupdates
- microsoft.maintenance/configurationassignments
- microsoft.maintenance/updates

## <a name="patchassessmentresources"></a>patchassessmentresources

이 테이블에 대한 샘플 쿼리는 [patchassessmentresources에 관한 Resource Graph 예제 쿼리](../samples/samples-by-table.md#patchassessmentresources)를 참조하세요.

- microsoft.compute/virtualmachines/patchassessmentresults
- microsoft.compute/virtualmachines/patchassessmentresults/softwarepatches
- microsoft.hybridcompute/machines/patchassessmentresults
- microsoft.hybridcompute/machines/patchassessmentresults/softwarepatches

## <a name="patchinstallationresources"></a>patchinstallationresources

- microsoft.compute/virtualmachines/patchinstallationresults
- microsoft.compute/virtualmachines/patchinstallationresults/softwarepatches
- microsoft.hybridcompute/machines/patchinstallationresults
- microsoft.hybridcompute/machines/patchinstallationresults/softwarepatches

## <a name="policyresources"></a>policyresources

이 테이블에 대 한 샘플 쿼리는 [policyresources의 Resource Graph 샘플 쿼리](../samples/samples-by-table.md#policyresources)를 참조하세요.

- microsoft.policyinsights/policystates
  - 샘플 쿼리: [정책 할당별 규정 준수](../samples/samples-by-category.md#compliance-by-policy-assignment)
  - 샘플 쿼리: [리소스 종류별 규정 준수](../samples/samples-by-category.md#compliance-by-resource-type)
  - 샘플 쿼리: [모든 규정 비준수 리소스 나열](../samples/samples-by-category.md#list-all-non-compliant-resources)
  - 샘플 쿼리: [상태별로 리소스 준수 요약](../samples/samples-by-category.md#summarize-resource-compliance-by-state)
  - 샘플 쿼리: [위치당 상태별로 리소스 준수 요약](../samples/samples-by-category.md#summarize-resource-compliance-by-state-per-location)

## <a name="recoveryservicesresources"></a>recoveryservicesresources

- microsoft.dataprotection/backupvaults/backupinstances
- microsoft.dataprotection/backupvaults/backupjobs
- microsoft.dataprotection/backupvaults/backuppolicies
- microsoft.recoveryservices/vaults/alerts
- Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems(백업 항목)
- microsoft.recoveryservices/vaults/backupjobs
- microsoft.recoveryservices/vaults/backuppolicies

## <a name="resourcecontainers"></a>resourcecontainers

이 테이블에 대한 샘플 쿼리는 [resourcecontainers에 관한 Resource Graph 샘플 쿼리](../samples/samples-by-table.md#resourcecontainers)를 참조하세요.

- microsoft.management/managementgroups
  - 샘플 쿼리: [관리 그룹당 구독 수](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - 샘플 쿼리: [지정된 관리 그룹에 대한 모든 관리 그룹 상위 항목 나열](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-management-group)
- Microsoft.Resources/subscriptions/resourceGroups(리소스 그룹)
  - 예제 쿼리: [두 쿼리의 결과를 단일 결과로 결합](../samples/samples-by-category.md)
  - 샘플 쿼리: [리소스 그룹에서 대/소문자를 구분하지 않는 특정 태그가 있는 스토리지 계정 찾기](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - 샘플 쿼리:[리소스 그룹에서 대/소문자를 구분하는 특정 태그가 있는 스토리지 계정 찾기](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)

## <a name="resources"></a>리소스

이 테이블에 대한 샘플 쿼리는 리소스 [Resource Graph에 대한 샘플 쿼리](../samples/samples-by-table.md#resources)를 참조하세요.

- 84codes.CloudAMQP/servers(CloudAMQP)
- Citrix.Services/XenAppEssentials(Citrix Virtual Apps Essentials)
- Citrix.Services/XenDesktopEssentials(Citrix Virtual Desktops Essentials)
- conexlink.mycloudit/accounts
- crypteron.datasecurity/apps
- Dynatrace 관찰성/monitor (Dynatrace)
- GitHub.Enterprise/accounts(GitHub AE)
- gridpro.evops/accounts
- gridpro.evops/accounts/eventrules
- gridpro.evops/accounts/requesttemplates
- gridpro.evops/accounts/views
- hive.streaming/services
- incapsula.waf/accounts
- LiveArena.Broadcast/services(LiveArena Broadcast)
- Mailjet.Email/services(Mailjet 이메일 서비스)
- micorosft/kubeenvironments
- Microsoft.AAD/domainServices(Azure AD Domain Services)
- microsoft.aadiam/azureadmetrics
- microsoft.aadiam/privateLinkForAzureAD(Azure AD용 프라이빗 링크)
- microsoft.aadiam/tenants
- Microsoft.AgFoodPlatform/farmBeats(Azure FarmBeats PaaS)
- microsoft.aisupercomputer/accounts
- microsoft.aisupercomputer/accounts/jobgroups
- microsoft.aisupercomputer/accounts/jobgroups/jobs
- microsoft.alertsmanagement/actionrules
- microsoft.alertsmanagement/resourcehealthalertrules
- microsoft.alertsmanagement/smartdetectoralertrules
- Microsoft.AnalysisServices/servers(Analysis Services)
- Microsoft.AnyBuild/clusters(AnyBuild 클러스터)
- Microsoft.ApiManagement/service(API Management 서비스)
- microsoft.appassessment/migrateprojects
- Microsoft.AppConfiguration/configurationStores(App Configuration)
- Microsoft.AppPlatform/Spring(Azure Spring Cloud)
- microsoft.archive/collections
- Microsoft.Attestation/attestationProviders(증명 공급자)
- microsoft. authorization/elevateaccessroleassignment
- Microsoft.Authorization/resourceManagementPrivateLinks(리소스 관리 프라이빗 링크)
- microsoft.automanage/accounts
- microsoft.automanage/configurationprofilepreferences
- microsoft.automanage/configurationprofiles
- Microsoft.Automation/AutomationAccounts(Automation 계정)
- microsoft.automation/automationaccounts/configurations
- Microsoft.Automation/automationAccounts/runbooks(Runbook)
- microsoft.autonomousdevelopmentplatform/accounts
- Microsoft.AutonomousSystems/workspaces(Bonsai)
- Microsoft.AVS/privateClouds(AVS 프라이빗 클라우드)
- microsoft.azconfig/configurationstores
- Microsoft.AzureActiveDirectory/b2cDirectories(B2C 테넌트)
- Microsoft.AzureActiveDirectory/guestUsages(게스트 사용량)
- Microsoft.AzureArcData/dataControllers(Azure Arc 데이터 컨트롤러)
- Microsoft AzureArcData/postgresInstances (Azure Arc 사용 PostgreSQL Hyperscale server 그룹)
- Microsoft.AzureArcData/sqlManagedInstances(SQL 관리형 인스턴스 - Azure Arc)
- Microsoft.AzureArcData/sqlServerInstances(SQL Server - Azure Arc)
- microsoft.azurecis/autopilotenvironments
- azurecis/dstsserviceaccounts
- azurecis/dstsserviceclientidentities
- microsoft.azuredata/datacontrollers
- microsoft.azuredata/hybriddatamanagers
- microsoft.azuredata/postgresinstances
- microsoft.azuredata/sqlbigdataclusters
- microsoft.azuredata/sqlinstances
- microsoft.azuredata/sqlmanagedinstances
- microsoft.azuredata/sqlserverinstances
- Microsoft.AzureData/sqlServerRegistrations(SQL Server 레지스트리)
- Microsoft.AzurePercept/계정 (Azure Percept 계정)
- microsoft.azuresphere/catalogs
- microsoft.azuresphere/catalogs/products
- microsoft.azuresphere/catalogs/products/devicegroups
- microsoft.azurestack/edgesubscriptions
- microsoft.azurestack/linkedsubscriptions
- Microsoft.Azurestack/registrations(Azure Stack Hub)
- Microsoft.AzureStackHCI/clusters(Azure Stack HCI)
- microsoft.azurestackhci/galleryimages
- microsoft.azurestackhci/networkinterfaces
- microsoft.azurestackhci/virtualharddisks
- Microsoft.AzureStackHci/virtualMachines(Azure Stack HCI 가상 머신 - Azure Arc)
- azurestackhci/virtualmachines/extensions
- microsoft.azurestackhci/virtualnetworks
- microsoft.backupsolutions/vmwareapplications
- microsoft.baremetal/consoleconnections
- Microsoft.BareMetal/crayServers(Cray 서버)
- Microsoft.BareMetal/monitoringServers(모니터링 서버)
- Microsoft.BareMetalInfrastructure/bareMetalInstances(BareMetal 인스턴스)
- Microsoft.Batch/batchAccounts(Batch 계정)
- microsoft.batchai/clusters
- microsoft.batchai/fileservers
- microsoft.batchai/jobs
- microsoft.batchai/workspaces
- Microsoft.Bing/accounts(Bing 리소스)
- microsoft.bingmaps/mapapis
- microsoft.biztalkservices/biztalk
- Microsoft.Blockchain/blockchainMembers(Azure Blockchain Service)
- Microsoft.Blockchain/cordaMembers(Corda)
- Microsoft.Blockchain/watchers(Blockchain Data Manager)
- Microsoft.BotService/botServices(Bot Service)
- Microsoft.Cache/Redis(Azure Cache for Redis)
- Microsoft.Cache/RedisEnterprise(Redis Enterprise)
- microsoft.cascade/sites
- Microsoft.Cdn/CdnWebApplicationFirewallPolicies(웹 애플리케이션 방화벽 정책(WAF))
- microsoft.cdn/profiles(Front Doors 표준/프리미엄(미리 보기))
- Microsoft.Cdn/Profiles/AfdEndpoints (엔드포인트)
- microsoft.cdn/profiles/endpoints(엔드포인트)
- Microsoft.CertificateRegistration/certificateOrders(App Service Certificate)
- chaosexperiments/감사 (비정상 실험 (클래식))
- microsoft. 감사 안 (비정상 상황 실험)
- microsoft.classicCompute/domainNames(클라우드 서비스(클래식))
- Microsoft.ClassicCompute/VirtualMachines(가상 머신(클래식))
- Microsoft.ClassicNetwork/networkSecurityGroups(네트워크 보안 그룹(클래식))
- Microsoft.ClassicNetwork/reservedIps(예약된 IP 주소(클래식))
- Microsoft.ClassicNetwork/virtualNetworks(가상 네트워크(클래식))
- Microsoft.ClassicStorage/StorageAccounts(스토리지 계정(클래식))
- microsoft.cloudes/accounts
- microsoft.cloudsearch/indexes
- Microsoft.CloudTest/accounts(CloudTest 계정)
- Microsoft.CloudTest/hostedpools(1ES 호스트된 풀)
- Microsoft.CloudTest/images(CloudTest 이미지)
- Microsoft.CloudTest/pools(CloudTest 풀)
- Microsoft.ClusterStor/nodes(ClusterStors)
- microsoft.codesigning/codesigningaccounts
- microsoft.codespaces/plans
- Microsoft.Cognition/syntheticsAccounts(Synthetics 계정)
- Microsoft.CognitiveServices/accounts(Cognitive Services)
- Microsoft.Compute/availabilitySets(가용성 집합)
- Microsoft.Compute/capacityReservationGroups(용량 예약 그룹)
- microsoft.compute/capacityreservationgroups/capacityreservations
- microsoft.compute/capacityreservations
- Microsoft.Compute/cloudServices(클라우드 서비스(추가 지원))
- Microsoft.Compute/diskAccesses(디스크 액세스)
- Microsoft.Compute/diskEncryptionSets(디스크 암호화 집합)
- Microsoft.Compute/disks(디스크)
- Microsoft.Compute/galleries(공유 이미지 갤러리)
- Microsoft. Compute/갤러리/응용 프로그램 (VM 응용 프로그램)
- Microsoft. Compute/갤러리/응용 프로그램/버전 (VM 응용 프로그램 버전)
- Microsoft.Compute/galleries/images(이미지 정의)
- Microsoft.Compute/galleries/images/versions(이미지 버전)
- Microsoft.Compute/hostgroups(호스트 그룹)
- Microsoft.Compute/hostgroups/hosts(호스트)
- Microsoft.Compute/images(이미지)
- Microsoft.Compute/ProximityPlacementGroups(근접 배치 그룹)
- Microsoft.Compute/restorePointCollections(복원 지점 컬렉션)
- microsoft.compute/sharedvmextensions
- microsoft.compute/sharedvmextensions/versions
- microsoft.compute/sharedvmimages
- microsoft.compute/sharedvmimages/versions
- Microsoft.Compute/snapshots(스냅샷)
- Microsoft.Compute/sshPublicKeys(SSH 키)
- microsoft.compute/swiftlets
- Microsoft.Compute/VirtualMachines(가상 머신)
  - 샘플 쿼리: [전원 상태별 가상 머신 수](../samples/samples-by-category.md#count-of-virtual-machines-by-power-state)
  - 샘플 쿼리: [OS 유형별로 가상 머신 개수 계산](../samples/samples-by-category.md#count-virtual-machines-by-os-type)
  - 샘플 쿼리: [확장 OS 유형별로 가상 머신 개수 계산](../samples/samples-by-category.md#count-virtual-machines-by-os-type-with-extend)
  - 샘플 쿼리:[가상 머신에 설치된 모든 확장명 나열](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
  - 샘플 쿼리: [실행 중이 아닌 머신과 마지막 규정 준수 상태 나열](../samples/samples-by-category.md#list-machines-that-are-not-running-and-the-last-compliance-status)
  - 샘플 쿼리: [리소스 Id 및 리소스 그룹을 사용 하는 가용성 상태 및 전원 상태별로 가상 머신 목록](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - 샘플 쿼리: [네트워크 인터페이스 및 공용 IP를 사용하여 가상 머신 나열](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
  - 샘플 쿼리: [이름의 내림차순으로 모든 가상 머신 표시](../samples/samples-by-category.md#show-all-virtual-machines-ordered-by-name-in-descending-order)
  - 샘플 쿼리: [이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시](../samples/samples-by-category.md#show-first-five-virtual-machines-by-name-and-their-os-type)
  - 샘플 쿼리: [전원 상태 확장 속성으로 가상 머신 요약](../samples/samples-by-category.md#summarize-virtual-machine-by-the-power-states-extended-property)
  - 샘플 쿼리: [정규식과 일치하는 가상 머신](../samples/samples-by-category.md#virtual-machines-matched-by-regex)
- microsoft.compute/virtualmachines/extensions
  - 샘플 쿼리:[가상 머신에 설치된 모든 확장명 나열](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
- microsoft.compute/virtualmachines/runcommands
- Microsoft.Compute/virtualMachineScaleSets(가상 머신 확장 집합)
  - 샘플 쿼리: [가상 머신 확장 집합 용량 및 크기 가져오기](../samples/samples-by-category.md#get-virtual-machine-scale-set-capacity-and-size)
- Microsoft.ConfidentialLedger/ledgers (기밀 원장)
- Microsoft.Confluent/organizations(Confluent 조직)
- Microsoft.ConnectedCache/cacheNodes(연결된 캐시 리소스)
- Microsoft.ConnectedVehicle/platformAccounts (연결된 차량 플랫폼)
- microsoft.connectedvmwarevsphere/clusters
- microsoft.connectedvmwarevsphere/datastores
- connectedvmwarevsphere/호스트
- microsoft.connectedvmwarevsphere/resourcepools
- Microsoft.connectedVMwareVSphere/vCenters(VMware vCenters)
- Microsoft.ConnectedVMwarevSphere/VirtualMachines(VMware + AVS 가상 머신)
- microsoft.connectedvmwarevsphere/virtualmachines/extensions
- microsoft.connectedvmwarevsphere/virtualmachinetemplates
- microsoft.connectedvmwarevsphere/virtualnetworks
- Microsoft.ContainerInstance/containerGroups(컨테이너 인스턴스)
- Microsoft.ContainerRegistry/registries(컨테이너 레지스트리)
- microsoft.containerregistry/registries/agentpools
- microsoft.containerregistry/registries/buildtasks
- Microsoft.ContainerRegistry/registries/replications(컨테이너 레지스트리 복제)
- microsoft.containerregistry/registries/taskruns
- microsoft.containerregistry/registries/tasks
- Microsoft.ContainerRegistry/registries/webhooks(컨테이너 레지스트리 웹후크)
- microsoft.containerservice/containerservices
- Microsoft.ContainerService/managedClusters(Kubernetes 서비스)
  - 예제 쿼리: [Azure 구독을 전송할 때 영향을 받는 리소스 나열](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.containerservice/openshiftmanagedclusters
- microsoft.contoso/clusters
- microsoft.contoso/employees
- microsoft.contoso/installations
- microsoft.contoso/towers
- microsoft.costmanagement/connectors
- microsoft.customproviders/resourceproviders
- microsoft.d365customerinsights/instances
- Microsoft. Dashboard/grafana (Grafana 작업 영역)
- Microsoft.DataBox/jobs(Azure Data Box)
- Microsoft.DataBoxEdge/dataBoxEdgeDevices(Azure Stack Edge/Data Box Gateway)
- Microsoft.Databricks/workspaces(Azure Databricks Services)
- Microsoft.DataCatalog/catalogs(Data Catalog)
- microsoft.datacatalog/datacatalogs
- Microsoft.DataCollaboration/workspaces(프로젝트 CI)
- Microsoft.Datadog/monitors(Datadog)
- Microsoft.DataFactory/dataFactories(데이터 팩터리)
- Microsoft.DataFactory/factories(데이터 팩터리(V2))
- Microsoft.DataLakeAnalytics/accounts(Data Lake Analytics)
- Microsoft.DataLakeStore/accounts(Data Lake Storage Gen1)
  - 샘플 쿼리: [Azure 구독을 전송할 때 영향을 받은 리소스 나열](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.datamigration/controllers
- Microsoft.DataMigration/services(Azure Database Migration Services)
- Microsoft.DataMigration/services/projects(Azure Database Migration Projects)
- microsoft.datamigration/slots
- microsoft.datamigration/sqlmigrationservices
- Microsoft.DataProtection/BackupVaults(Backup 자격 증명 모음)
- Microsoft DataProtection/resourceGuards (리소스 가드 (미리 보기))
- microsoft.dataprotection/resourceoperationgatekeepers
- microsoft.datareplication/replicationfabrics
- Microsoft.DataReplication/replicationVaults(Site Recovery Vault)
- Microsoft.DataShare/accounts(Data Share)
- Microsoft.DBforMariaDB/servers(Azure Database for MariaDB 서버)
- Microsoft.DBforMySQL/flexibleServers(Azure Database for MySQL 유연한 서버)
- Microsoft.DBforMySQL/servers(Azure Database for MySQL 서버)
- Microsoft.DBforPostgreSQL/flexibleServers(Azure Database for PostgreSQL 유연한 서버)
- Microsoft.DBforPostgreSQL/serverGroups(Azure Database for PostgreSQL 서버 그룹)
- Microsoft.DBforPostgreSQL/serverGroupsv2(Azure Database for PostgreSQL 서버 그룹)
- Microsoft.DBforPostgreSQL/servers(Azure Database for PostgreSQL 서버)
- Microsoft.DBforPostgreSQL/serversv2(Azure Database for PostgreSQL 서버 v2)
- microsoft.dbforpostgresql/singleservers
- microsoft.delegatednetwork/controller
- microsoft.delegatednetwork/delegatedsubnets
- microsoft.delegatednetwork/orchestratorinstances
- microsoft.delegatednetwork/orchestrators
- microsoft.deploymentmanager/artifactsources
- Microsoft.DeploymentManager/Rollouts(출시)
- microsoft.deploymentmanager/servicetopologies
- microsoft.deploymentmanager/servicetopologies/services
- microsoft.deploymentmanager/servicetopologies/services/serviceunits
- microsoft.deploymentmanager/steps
- Microsoft.DesktopVirtualization/ApplicationGroups(애플리케이션 그룹)
- Microsoft.DesktopVirtualization/HostPools(호스트 풀)
- Microsoft.DesktopVirtualization/ScalingPlans(스케일링 플랜)
- Microsoft.DesktopVirtualization/Workspaces(작업 영역)
- microsoft.devices/elasticpools
- microsoft.devices/elasticpools/iothubtenants
- Microsoft.Devices/IotHubs(IoT Hub)
- Microsoft.Devices/ProvisioningServices(디바이스 프로비저닝 서비스)
- Microsoft.DeviceUpdate/Accounts(Device Update for IoT Hub)
- microsoft.deviceupdate/accounts/instances
- microsoft.devops/pipelines(DevOps Starter)
- microsoft.devspaces/controllers
- microsoft.devtestlab/labcenters
- Microsoft.DevTestLab/labs(DevTest Labs)
- microsoft.devtestlab/labs/servicerunners
- Microsoft.DevTestLab/labs/virtualMachines(가상 머신)
- microsoft.devtestlab/schedules
- Microsoft.DigitalTwins/digitalTwinsInstances(Azure Digital Twins)
- Microsoft.DocumentDB/cassandraClusters(Azure Managed Instance for Apache Cassandra)
- Microsoft.DocumentDb/databaseAccounts(Azure Cosmos DB 계정)
  - 샘플 쿼리: [특정 쓰기 위치를 사용하여 Azure Cosmos DB 나열](../samples/samples-by-category.md#list-azure-cosmos-db-with-specific-write-locations)
- Microsoft.DomainRegistration/domains(App Service 도메인)
- microsoft.dynamics365fraudprotection/instances
- Microsoft.EdgeOrder/addresses (Azure Edge 하드웨어 센터 주소)
- microsoft.edgeorder/ordercollections
- Microsoft.EdgeOrder/orderItems (Azure Edge 하드웨어 센터)
- microsoft.edgeorder/orders
- Microsoft.Elastic/monitors(Elasticsearch(Elastic 클라우드))
- microsoft.enterpriseknowledgegraph/services
- Microsoft.EventGrid/domains(Event Grid 도메인)
- Microsoft.EventGrid/partnerNamespaces(Event Grid 파트너 네임스페이스)
- Microsoft.EventGrid/partnerRegistrations(Event Grid 파트너 등록)
- Microsoft.EventGrid/partnerTopics(Event Grid Partner 토픽)
- Microsoft.EventGrid/systemTopics(Event Grid 시스템 토픽)
- Microsoft.EventGrid/topics(Event Grid 토픽)
- Microsoft.EventHub/clusters(Event Hubs 클러스터)
- Microsoft.EventHub/namespaces(Event Hubs 네임스페이스)
- Microsoft.Experimentation/experimentWorkspaces(실험 작업 영역)
- Microsoft.ExtendedLocation/CustomLocations(사용자 지정 위치)
  - 샘플 쿼리: [VMware 또는 SCVMM이 사용하도록 설정된 Azure Arc 지원 사용자 지정 위치 나열](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)
- microsoft.falcon/namespaces
- Microsoft.Fidalgo/devcenters (Fidalgo DevCenters)
- microsoft.fidalgo/machinedefinitions
- microsoft.fidalgo/networksettings
- Microsoft.Fidalgo/projects (Fidalgo 프로젝트)
- Microsoft.Fidalgo/projects/environments (Fidalgo 환경)
- Microsoft.FluidRelay/fluidRelayServers(Fluid Relay)
- microsoft.footprintmonitoring/profiles
- microsoft.gaming/titles
- Microsoft.Genomics/accounts(Genomics 계정)
- microsoft.guestconfiguration/automanagedaccounts
- Microsoft.HanaOnAzure/hanaInstances(Azure의 SAP HANA)
- Microsoft.HanaOnAzure/sapMonitors(SAP 솔루션을 위한 Azure Monitor)
- microsoft.hardwaresecuritymodules/dedicatedhsms
- Microsoft.HDInsight/clusterpools (HDInsight 클러스터 풀)
- Microsoft HDInsight/clusterpools/clusters (HDInsight gen2 클러스터)
- microsoft.hdinsight/clusterpools/clusters/sessionclusters
- Microsoft.HDInsight/clusters(HDInsight 클러스터)
- Microsoft.HealthBot/healthBots(Azure Health Bot)
- Microsoft.HealthcareApis/services(FHIR용 Azure API)
- microsoft.healthcareapis/services/privateendpointconnections
- Microsoft.HealthcareApis/workspaces (의료 APIs 작업 영역)
- Microsoft.HealthcareApis/workspaces/dicomservice (DICOM 서비스)
- Microsoft.HealthcareApis/workspaces/fhirservicess (FHIR 서비스)
- Microsoft.HealthcareApis/workspaces/iotconnectors (IoT 커넥터)
- Microsoft.HpcWorkbench/instances (HPC Workbenches (미리 보기))
- Microsoft.HybridCompute/machines(서버 - Azure Arc)
  - 샘플 쿼리: [도메인 별 Arc 사용 서버의 수 및 백분율을 가져옵니다](../samples/samples-by-category.md#get-count-and-percentage-of-arc-enabled-servers-by-domain)
  - 샘플 쿼리: [Azure Arc 지원 서버에 설치된 모든 확장 나열](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
  - 예제 쿼리: [Arc 사용 서버가 최신 릴리스 에이전트 버전을 실행 하지 않는 목록을](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version) 표시 합니다.
- microsoft.hybridcompute/machines/extensions
  - 샘플 쿼리: [Azure Arc 지원 서버에 설치된 모든 확장 나열](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
- Microsoft.HybridCompute/privateLinkScopes(Azure Arc 프라이빗 링크 범위)
- hybridcontainerservice/provisionedclusters
- Microsoft.HybridData/dataManagers(StorSimple Data Manager)
- Microsoft.HybridNetwork/devices(Azure Network Function Manager – 디바이스(미리 보기))
- Microsoft.HybridNetwork/networkFunctions(Azure Network Function Manager – 네트워크 기능(미리 보기))
- microsoft.hybridnetwork/virtualnetworkfunctions
- Microsoft.ImportExport/jobs(가져오기/내보내기 작업)
- microsoft.industrydatalifecycle/basemodels
- microsoft.industrydatalifecycle/custodiancollaboratives
- microsoft.industrydatalifecycle/dataconsumercollaboratives
- microsoft.industrydatalifecycle/derivedmodels
- microsoft.industrydatalifecycle/membercollaboratives
- microsoft.industrydatalifecycle/modelmappings
- microsoft.industrydatalifecycle/pipelinesets
- microsoft.insights/actiongroups
- microsoft.insights/activitylogalerts
- microsoft.insights/alertrules
- microsoft.insights/autoscalesettings
- microsoft.insights/components(Application Insights)
- microsoft.insights/datacollectionendpoints(데이터 수집 엔드포인트)
- microsoft.insights/datacollectionrules(데이터 수집 규칙)
- microsoft.insights/guestdiagnosticsettings
- microsoft.insights/metricalerts
- microsoft.insights/notificationgroups
- microsoft.insights/notificationrules
- Microsoft.Insights/privateLinkScopes(Azure Monitor 프라이빗 링크 범위)
- microsoft.insights/querypacks
- microsoft.insights/scheduledqueryrules
- microsoft.insights/webtests(가용성 테스트)
- microsoft.insights/workbooks(Azure Workbooks)
- microsoft.insights/workbooktemplates(Azure Workbook 템플릿)
- Microsoft.IntelligentITDigitalTwin/digitalTwins(Minervas)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/assets(자산)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/executionPlans(배포)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/testPlans(도구 모음)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/tests(스크립트)
- Microsoft.IoTCentral/IoTApps(IoT Central 애플리케이션)
- microsoft.iotspaces/graph
- microsoft.keyvault/hsmpools
- microsoft.keyvault/managedhsms
- Microsoft.KeyVault/vaults(키 자격 증명 모음)
  - 샘플 쿼리: [Key Vault 리소스 개수 계산s](../samples/samples-by-category.md#count-key-vault-resources)
  - 샘플 쿼리: [구독 이름이 있는 키 자격 증명 모음](../samples/samples-by-category.md#key-vaults-with-subscription-name)
  - 예제 쿼리: [Azure 구독을 전송할 때 영향을 받는 리소스 나열](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- Microsoft.Kubernetes/connectedClusters(Kubernetes - Azure Arc)
  - 샘플 쿼리: [Azure Monitor 확장이 없는 모든 Azure Arc 지원 Kubernetes 클러스터 나열](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
  - 샘플 쿼리: [모든 Azure Arc 지원 Kubernetes 리소스 나열](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-resources)
- Microsoft.Kusto/clusters(Azure Data Explorer 클러스터)
- Microsoft.Kusto/clusters/databases(Azure Data Explorer 데이터베이스)
- Microsoft.LabServices/labAccounts(랩 서비스)
- microsoft.labservices/labplans
- microsoft.labservices/labs
- Microsoft.LoadTestService/LoadTests(클라우드 네이티브 부하 테스트)
- Microsoft.Logic/integrationAccounts(통합 계정)
- Microsoft.Logic/integrationServiceEnvironments(통합 서비스 환경)
- Microsoft.Logic/integrationServiceEnvironments/managedApis(관리 커넥터)
- Microsoft.Logic/workflows(논리 앱)
- Microsoft.Logz/monitors(Logz 기본 계정)
- Microsoft.Logz/monitors/accounts(Logz 하위 계정)
- Microsoft.MachineLearning/commitmentPlans(Machine Learning Studio(클래식) 웹 서비스 계획)
- Microsoft.MachineLearning/webServices(Machine Learning Studio(클래식) 웹 서비스)
- Microsoft.MachineLearning/workspaces(Machine Learning Studio(클래식) 작업 영역)
- microsoft.machinelearningcompute/operationalizationclusters
- microsoft.machinelearningexperimentation/accounts/workspaces
- microsoft.machinelearningservices/aisysteminventories
- microsoft.machinelearningservices/modelinventories
- microsoft.machinelearningservices/modelinventory
- microsoft.machinelearningservices/virtualclusters
- Microsoft.MachineLearningServices/workspaces(기계 학습)
- microsoft.machinelearningservices/workspaces/batchendpoints
- microsoft.machinelearningservices/workspaces/batchendpoints/deployments
- microsoft.machinelearningservices/workspaces/inferenceendpoints
- microsoft.machinelearningservices/workspaces/inferenceendpoints/deployments
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints(기계 학습 온라인 엔드포인트)
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments(기계 학습 온라인 배포)
- Microsoft.Maintenance/maintenanceConfigurations(유지 관리 구성)
- microsoft.maintenance/maintenancepolicies
- microsoft.managedidentity/groups
- Microsoft.ManagedIdentity/userAssignedIdentities(관리 ID)
  - 예제 쿼리: [Azure 구독을 전송할 때 영향을 받는 리소스 나열](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.managednetwork/managednetworkgroups
- microsoft.managednetwork/managednetworkpeeringpolicies
- microsoft.managednetwork/managednetworks
- microsoft.managednetwork/managednetworks/managednetworkgroups
- microsoft.managednetwork/managednetworks/managednetworkpeeringpolicies
- Microsoft.Maps/accounts(Azure Maps 계정)
- Microsoft.Maps/accounts/creators (Azure Maps Creator 리소스)
- microsoft.maps/accounts/privateatlases
- Microsoft.MarketplaceApps/classicDevServices(클래식 개발 서비스)
- microsoft.media/mediaservices(Media Services)
- microsoft.media/mediaservices/liveevents(라이브 이벤트)
- microsoft media/windowsazure.mediaservices/streamingendpoints (스트리밍 끝점)
- microsoft.media/mediaservices/transforms
- microsoft.media/videoanalyzers(Video Analyzer)
- microsoft.microservices4spring/appclusters
- microsoft.migrate/assessmentprojects
- microsoft.migrate/migrateprojects
- microsoft.migrate/movecollections
- Microsoft.Migrate/projects(마이그레이션 프로젝트)
- Microsoft.MixedReality/holographicsBroadcastAccounts(Holographics Broadcast 계정)
- Microsoft.MixedReality/objectAnchorsAccounts(Object Anchors 계정)
- Microsoft.MixedReality/objectUnderstandingAccounts(Object Understanding 계정)
- Microsoft.MixedReality/remoteRenderingAccounts(Remote Rendering 계정)
- Microsoft.MixedReality/spatialAnchorsAccounts(Spatial Anchors 계정)
- microsoft.mixedreality/surfacereconstructionaccounts
- Microsoft.MobileNetwork/mobileNetworks (모바일 네트워크)
- microsoft.mobilenetwork/mobilenetworks/datanetworks
- microsoft.mobilenetwork/mobilenetworks/services
- microsoft.mobilenetwork/mobilenetworks/simpolicies
- Microsoft.MobileNetwork/mobileNetworks/sites (모바일 네트워크 사이트)
- microsoft.mobilenetwork/mobilenetworks/slices
- microsoft.mobilenetwork/networks
- microsoft.mobilenetwork/networks/sites
- Microsoft.MobileNetwork/packetCoreControlPlanes (네트워크 함수의 Arc – 패킷 코어)
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes/attacheddatanetworks
- Microsoft.MobileNetwork/sims(Sim)
- microsoft.mobilenetwork/sims/simprofiles
- microsoft. 모니터/계정
- Microsoft.NetApp/netAppAccounts(NetApp 계정)
- microsoft.netapp/netappaccounts/backuppolicies
- Microsoft.NetApp/netAppAccounts/capacityPools(용량 풀)
- Microsoft.NetApp/netAppAccounts/capacityPools/Volumes(볼륨)
- microsoft.netapp/netappaccounts/capacitypools/volumes/mounttargets
- Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots(스냅샷)
- microsoft netapp/netappaccounts/capacitypools/볼륨/하위 볼륨
- Microsoft.NetApp/netAppAccounts/snapshotPolicies (스냅샷 정책)
- Microsoft.Network/applicationGateways(애플리케이션 게이트웨이)
- Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies(웹 애플리케이션 방화벽 정책(WAF))
- Microsoft.Network/applicationSecurityGroups(애플리케이션 보안 그룹)
- Microsoft.Network/azureFirewalls(방화벽)
- Microsoft.Network/bastionHosts(베스천)
- Microsoft.Network/connections(연결)
- Microsoft. Network/customIpPrefixes (사용자 지정 IP 접두사)
- microsoft.network/ddoscustompolicies
- Microsoft.Network/ddosProtectionPlans(DDoS 보호 계획)
- microsoft.network/dnsforwardingrulesets
- microsoft.network/dnsresolvers
- Microsoft.Network/dnsZones(DNS 영역)
- microsoft.network/dscpconfigurations
- Microsoft.Network/expressRouteCircuits(ExpressRoute 회로)
- microsoft.network/expressroutecrossconnections
- microsoft.network/expressroutegateways
- Microsoft.Network/expressRoutePorts(ExpressRoute Direct)
- Microsoft.Network/firewallPolicies(방화벽 정책)
- Microsoft.Network/frontdoors(Front Door)
- Microsoft.Network/FrontDoorWebApplicationFirewallPolicies(웹 애플리케이션 방화벽 정책(WAF))
- microsoft.network/ipallocations
- Microsoft.Network/ipGroups(IP 그룹)
- Microsoft.Network/LoadBalancers(부하 분산 장치)
- Microsoft.Network/localnetworkgateways(로컬 네트워크 게이트웨이)
- microsoft.network/mastercustomipprefixes
- Microsoft.Network/natGateways(NAT 게이트웨이)
- Microsoft.Network/NetworkExperimentProfiles(Internet Analyzer 프로필)
- microsoft.network/networkintentpolicies
- Microsoft.Network/networkinterfaces(네트워크 인터페이스)
  - 샘플 쿼리: [네트워크 인터페이스의 가상 네트워크 및 서브넷 가져오기](../samples/samples-by-category.md#get-virtual-networks-and-subnets-of-network-interfaces)
  - 샘플 쿼리: [네트워크 인터페이스 및 공용 IP를 사용하여 가상 머신 나열](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/networkManagers(네트워크 관리자)
- microsoft.network/networkprofiles
- Microsoft.Network/NetworkSecurityGroups(네트워크 보안 그룹)
  - 샘플 쿼리: [연결되지 않은 네트워크 보안 그룹 표시](../samples/samples-by-category.md#show-unassociated-network-security-groups)
- microsoft.network/networksecurityperimeters
- microsoft.network/networkvirtualappliances
- microsoft.network/networkwatchers(Network Watcher)
- microsoft.network/networkwatchers/connectionmonitors
- microsoft.network/networkwatchers/flowlogs(NSG 흐름 로그)
- microsoft.network/networkwatchers/lenses
- microsoft.network/networkwatchers/pingmeshes
- microsoft.network/p2svpngateways
- Microsoft.Network/privateDnsZones(프라이빗 DNS 영역)
- microsoft.network/privatednszones/virtualnetworklinks
- microsoft.network/privateendpointredirectmaps
- Microsoft.Network/privateEndpoints(프라이빗 엔드포인트)
- Microsoft.Network/privateLinkServices(프라이빗 링크 서비스)
- Microsoft.Network/PublicIpAddresses(공용 IP 주소)
  - 샘플 쿼리: [네트워크 인터페이스 및 공용 IP를 사용하여 가상 머신 나열](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/publicIpPrefixes(공용 IP 접두사)
- Microsoft.Network/routeFilters(경로 필터)
- Microsoft.Network/routeTables(경로 테이블)
- microsoft.network/sampleresources
- microsoft.network/securitypartnerproviders
- Microsoft.Network/serviceEndpointPolicies(서비스 엔드포인트 정책)
- Microsoft.Network/trafficmanagerprofiles(Traffic Manager 프로필)
- microsoft.network/virtualhubs
- microsoft.network/virtualhubs/bgpconnections
- microsoft.network/virtualhubs/ipconfigurations
- Microsoft.Network/virtualNetworkGateways(가상 네트워크 게이트웨이)
- Microsoft.Network/virtualNetworks(가상 네트워크)
- microsoft.network/virtualnetworktaps
- microsoft.network/virtualrouters
- Microsoft.Network/virtualWans(Virtual WAN)
- microsoft.network/vpngateways
- microsoft.network/vpnserverconfigurations
- microsoft.network/vpnsites
- microsoft.networkfunction/azuretrafficcollectors
- Microsoft.NotificationHubs/namespaces(알림 허브 네임스페이스)
- Microsoft.NotificationHubs/namespaces/notificationHubs(알림 허브)
- microsoft.nutanix/interfaces
- microsoft.nutanix/nodes
- microsoft.objectstore/osnamespaces
- microsoft.offazure/hypervsites
- microsoft.offazure/importsites
- microsoft.offazure/mastersites
- microsoft.offazure/serversites
- microsoft.offazure/vmwaresites
- microsoft.openlogisticsplatform/applicationworkspaces
- Microsoft.OpenLogisticsPlatform/workspaces(오픈 공급망 플랫폼)
- microsoft.operationalinsights/clusters
- Microsoft.OperationalInsights/querypacks(Log Analytics 쿼리 팩)
- Microsoft.OperationalInsights/workspaces(Log Analytics 작업 영역)
- Microsoft.OperationsManagement/solutions(솔루션)
- microsoft.operationsmanagement/views
- Microsoft.Orbital/contactProfiles(ContactProfiles)
- Microsoft. 궤도/GroundStations (GroundStations)
- microsoft.orbital/orbitalendpoints
- microsoft.orbital/orbitalgateways
- microsoft.orbital/orbitalgateways/orbitall2connections
- microsoft.orbital/orbitalgateways/orbitall3connections
- Microsoft.Orbital/spacecrafts(Spacecrafts)
- Microsoft.Peering/peerings(Peerings)
- Microsoft.Peering/peeringServices(Peering Services)
- Microsoft.Portal/dashboards(공유 대시보드)
- microsoft.portalsdk/rootresources
- microsoft.powerbi/privatelinkservicesforpowerbi
- microsoft.powerbi/tenants
- microsoft.powerbi/workspacecollections
- microsoft.powerbidedicated/autoscalevcores
- Microsoft.PowerBIDedicated/capacities(Power BI Embedded)
- microsoft.powerplatform/accounts
- microsoft.powerplatform/enterprisepolicies
- microsoft.projectbabylon/accounts
- providerhubdevtest/regionalstresstests
- Microsoft.Purview/Accounts(Purview 계정)
- Microsoft.Quantum/Workspaces(Quantum 작업 영역)
- Microsoft.RecommendationsService/accounts (Intelligent 권장 사항 계정)
- Microsoft.RecommendationsService/accounts/modeling (모델링)
- Microsoft.RecommendationsService/accounts/serviceEndpoints (서비스 엔드포인트)
- Microsoft.RecoveryServices/vaults(Recovery Services 자격 증명 모음)
- Microsoft.RedHatOpenShift/openShiftClusters(OpenShift 클러스터)
- Microsoft.Relay/namespaces(릴레이)
- microsoft.remoteapp/collections
- microsoft.resiliency/chaosexperiments
- Microsoft.ResourceConnector/Appliances(리소스 브리지)
- Microsoft.resourcegraph/queries(Resource Graph 쿼리)
- Microsoft.Resources/deploymentScripts(배포 스크립트)
- Microsoft.Resources/templateSpecs(템플릿 사양)
- microsoft.resources/templatespecs/versions
- Microsoft.SaaS/applications(SaaS(클래식))
- Microsoft.SaaS/resources(SaaS)
- Microsoft.Scheduler/jobCollections(Scheduler 작업 컬렉션)
- Microsoft Scom/managedInstances (Aquila Instances)
- microsoft.scvmm/clouds
- Microsoft.scvmm/virtualMachines(SCVMM 가상 머신 - Azure Arc)
- microsoft.scvmm/virtualmachinetemplates
- microsoft.scvmm/virtualnetworks
- microsoft.scvmm/vmmservers
- Microsoft.Search/searchServices(검색 서비스)
- microsoft. 보안/할당
- microsoft.security/automations
- microsoft. security/customentitystor
- microsoft.security/iotsecuritysolutions
- microsoft.security/securityconnectors
- Microsoft.SecurityDetonation/chambers(보안 데토네이션 챔버)
- Microsoft.ServiceBus/namespaces(Service Bus 네임스페이스)
- Microsoft.ServiceFabric/clusters(Service Fabric 클러스터)
- microsoft.servicefabric/containergroupsets
- Microsoft.ServiceFabric/managedclusters(Service Fabric 관리 클러스터)
- microsoft.servicefabricmesh/applications
- microsoft.servicefabricmesh/gateways
- microsoft.servicefabricmesh/networks
- microsoft.servicefabricmesh/secrets
- microsoft.servicefabricmesh/volumes
- Microsoft.ServicesHub/connectors(서비스 허브 커넥터)
- Microsoft.SignalRService/SignalR(SignalR)
- Microsoft.SignalRService/WebPubSub(Web PubSub 서비스)
- microsoft.singularity/accounts
- microsoft.skytap/nodes
- microsoft.solutions/appliancedefinitions
- microsoft.solutions/appliances
- Microsoft.Solutions/applicationDefinitions(서비스 카탈로그 관리 애플리케이션 정의)
- Microsoft.Solutions/applications(관리 애플리케이션)
- microsoft.solutions/jitrequests
- microsoft.spoolservice/spools
- Microsoft.Sql/instancePools(인스턴스 풀)
- Microsoft.Sql/managedInstances(SQL 관리되는 인스턴스)
- Microsoft.Sql/managedInstances/databases(관리 데이터베이스)
- Microsoft.Sql/servers(SQL 서버)
- Microsoft.Sql/servers/databases(SQL 데이터베이스)
  - 예제 쿼리: [Azure 구독을 전송할 때 영향을 받는 리소스 나열](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
  - 샘플 쿼리: [SQL Database 및 탄력적 풀 나열](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- Microsoft.Sql/servers/elasticpools(SQL 탄력적 풀)
  - 샘플 쿼리: [SQL Database 및 탄력적 풀 나열](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- microsoft.sql/servers/jobaccounts
- Microsoft.Sql/servers/jobAgents(탄력적 작업 에이전트)
- Microsoft.Sql/virtualClusters(가상 클러스터)
- microsoft.sqlvirtualmachine/sqlvirtualmachinegroups
- Microsoft.SqlVirtualMachine/SqlVirtualMachines(SQL 가상 머신)
- microsoft.sqlvm/dwvm
- Microsoft.Storage/StorageAccounts(스토리지 계정)
  - 샘플 쿼리: [리소스 그룹에서 대/소문자를 구분하지 않는 특정 태그가 있는 스토리지 계정 찾기](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - 샘플 쿼리:[리소스 그룹에서 대/소문자를 구분하는 특정 태그가 있는 스토리지 계정 찾기](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)
  - 샘플 쿼리: [특정 태그 값이 있는 모든 스토리지 계정 나열](../samples/samples-by-category.md#list-all-storage-accounts-with-specific-tag-value)
  - 샘플 쿼리: [Azure 구독을 전송할 때 영향을 받은 리소스 나열](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.storagecache/amlfilesystems
- Microsoft.StorageCache/caches(HPC 캐시)
- Microsoft.StoragePool/diskPools(디스크 풀)
- Microsoft.StorageSync/storageSyncServices(스토리지 동기화 서비스)
- Microsoft.StorageSyncDev/storageSyncServices(스토리지 동기화 서비스)
- Microsoft.StorageSyncInt/storageSyncServices(스토리지 동기화 서비스)
- Microsoft.StorSimple/Managers(StorSimple 디바이스 관리자)
- Microsoft.StreamAnalytics/clusters(Stream Analytics 클러스터)
- Microsoft.StreamAnalytics/StreamingJobs(Stream Analytics 작업)
- microsoft.swiftlet/virtualmachines
- microsoft.swiftlet/virtualmachinesnapshots
- Microsoft.Synapse/privateLinkHubs(Azure Synapse Analytics(프라이빗 링크 허브))
- Microsoft.Synapse/workspaces(Azure Synapse Analytics)
- Microsoft.Synapse/workspaces/bigDataPools(Apache Spark 풀)
- microsoft.synapse/workspaces/eventstreams
- Microsoft.Synapse/workspaces/kustopools(데이터 탐색기 풀(미리 보기))
- microsoft.synapse/workspaces/sqldatabases
- Microsoft.Synapse/workspaces/sqlPools(전용 SQL 풀)
- microsoft.terraformoss/providerregistrations
- Microsoft.TestBase/testBaseAccounts (테스트 기본 계정)
- microsoft.testbase/testbaseaccounts/packages
- microsoft.testbase/testbases
- Microsoft.TimeSeriesInsights/environments(Time Series Insights 환경)
- Microsoft.TimeSeriesInsights/environments/eventsources(Time Series Insights 이벤트 소스)
- Microsoft.TimeSeriesInsights/environments/referenceDataSets(Time Series Insights 참조 데이터 세트)
- microsoft.token/stores
- microsoft.tokenvault/vaults
- Microsoft.VideoIndexer/accounts (Media용 Video Analyzer)
- Microsoft.VirtualMachineImages/imageTemplates(이미지 템플릿)
- microsoft.visualstudio/account(Azure DevOps 조직)
- microsoft.visualstudio/account/extension
- microsoft.visualstudio/account/project(DevOps Starter)
- microsoft.vmware/arczones
- microsoft.vmware/resourcepools
- microsoft.vmware/vcenters
- microsoft.vmware/virtualmachines
- microsoft.vmware/virtualmachinetemplates
- microsoft.vmware/virtualnetworks
- Microsoft.VMwareCloudSimple/dedicatedCloudNodes(CloudSimple 노드)
- Microsoft.VMwareCloudSimple/dedicatedCloudServices(CloudSimple 서비스)
- Microsoft.VMwareCloudSimple/virtualMachines(CloudSimple Virtual Machines)
- microsoft.vmwareonazure/privateclouds
- microsoft.vmwarevirtustream/privateclouds
- microsoft.vsonline/accounts
- Microsoft.VSOnline/Plans(Visual Studio Online 플랜)
- microsoft.web/apimanagementaccounts
- microsoft.web/apimanagementaccounts/apis
- microsoft.web/certificates
- Microsoft.Web/connectionGateways(온-프레미스 데이터 게이트웨이)
- Microsoft.Web/connections(API 연결)
- microsoft.web/containerapps
- Microsoft.Web/customApis(Logic Apps 사용자 지정 커넥터)
- Microsoft.Web/HostingEnvironments(App Service Environment)
- Microsoft.Web/KubeEnvironments(App Service Kubernetes 환경)
- Microsoft.Web/serverFarms(App Service 요금제)
- Microsoft.Web/sites(App Services)
- microsoft.web/sites/premieraddons
- Microsoft.Web/sites/slots(App Service(슬롯))
- Microsoft.Web/StaticSites(Static Web Apps (미리 보기))
- Microsoft.Web/WorkerApps(Container Apps)
- Microsoft.WindowsESU/multipleActivationKeys(Windows 복수 정품 인증 키)
- Microsoft.WindowsIoT/DeviceServices(Windows 10 IoT Core Services)
- microsoft.workloadbuilder/migrationagents
- microsoft.workloadbuilder/workloads
- myget.packagemanagement/services
- NGINX.NGINXPLUS/nginxDeployments (NGINX 배포)
- Paraleap.CloudMonix/services(CloudMonix)
- Pokitdok.Platform/services(PokitDok 플랫폼)
- private.contoso/employees
- Providers.Test/statefulIbizaEngines(내 리소스)
- RavenHq.Db/databases(RavenHQ)
- Raygun.CrashReporting/apps(Raygun)
- Sendgrid.Email/accounts(SendGrid 계정)
- sparkpost.basic/services
- stackify.retrace/services
- test.shoebox/testresources
- test.shoebox/testresources2
- TrendMicro.DeepSecurity/accounts(Deep Security SaaS)
- u2uconsult.theidentityhub/services
- Wandisco.Fusion/fusionGroups(LiveData 플랜)
- Wandisco.Fusion/fusionGroups/azureZones(Azure 영역)
- Wandisco.Fusion/fusionGroups/azureZones/plugins(플러그 인)
- Wandisco.Fusion/fusionGroups/hiveReplicationRules(Hive 복제 규칙)
- Wandisco.Fusion/fusionGroups/managedOnPremZones(온-프레미스 영역)
- wandisco.fusion/fusiongroups/onpremzones
- Wandisco.Fusion/fusionGroups/replicationRules(복제 규칙)
- Wandisco.Fusion/migrators(LiveData Migrators)
- Wandisco.Fusion/migrators/exclusionTemplates (제외)
- Wandisco.Fusion/migrators/liveDataMigrations(마이그레이션)
- Wandisco.Fusion/migrators/metadataMigrations (메타데이터 마이그레이션)
- Wandisco.Fusion/migrators/metadataTargets (메타데이터 대상)
- Wandisco.Fusion/migrators/pathMappings (경로 매핑)
- Wandisco.Fusion/migrators/targets(대상)

## <a name="securityresources"></a>securityresources

이 테이블에 대한 샘플 쿼리는 [resource Graph securityresources에 대한 샘플 쿼리를 참조하세요](../samples/samples-by-table.md#securityresources).

- microsoft.security/assessments
  - 샘플 쿼리: [권장 사항당 정상, 비정상, 해당 사항없음 리소스 개수 계산](../samples/samples-by-category.md#count-healthy-unhealthy-and-not-applicable-resources-per-recommendation)
  - 샘플 쿼리: [Azure Security Center 권장 사항 나열](../samples/samples-by-category.md#list-azure-security-center-recommendations)
  - 샘플 쿼리: [Container Registry 취약성 평가 결과 나열](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - 샘플 쿼리: [Qualys 취약성 평가 결과 나열](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/assessments/subassessments
  - 샘플 쿼리: [Container Registry 취약성 평가 결과 나열](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - 샘플 쿼리: [Qualys 취약성 평가 결과 나열](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/insights/classification (데이터 민감도 보안 인사이트(미리 보기))
- microsoft.security/iotalerts
  - 샘플 쿼리: [유형별로 필터링하여 허브의 모든 IoT 경고 가져오기](../samples/samples-by-category.md#get-all-iot-alerts-on-hub-filtered-by-type)
  - 샘플 쿼리: [: 특정 IoT 경고 가져오기](../samples/samples-by-category.md#get-specific-iot-alert)
- microsoft.security/locations/alerts(보안 경고)
- microsoft.security/pricings
  - 샘플 쿼리: [구독당 Azure Defender 가격 책정 계층 표시](../samples/samples-by-category.md#show-azure-defender-pricing-tier-per-subscription)
- microsoft.security/regulatorycompliancestandards
  - 샘플 쿼리: [규정 표준에 따란 규정 준수 상태](../samples/samples-by-category.md#regulatory-compliance-state-per-compliance-standard)
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments
  - 샘플 쿼리: [규정 준수 평가 상태](../samples/samples-by-category.md#regulatory-compliance-assessments-state)
- microsoft.security/securescores
  - 샘플 쿼리: [관리 그룹당 보안 점수](../samples/samples-by-category.md#secure-score-per-management-group)
  - 샘플 쿼리: [구독당 보안 점수를 반환합니다.](../samples/samples-by-category.md#secure-score-per-subscription)
- microsoft.security/securescores/securescorecontrols
  - 샘플 쿼리: [컨트롤의 구독당 보안 점수](../samples/samples-by-category.md#controls-secure-score-per-subscription)
- microsoft.security/softwareinventories
- microsoft.security/softwareinventory

## <a name="servicehealthresources"></a>servicehealthresources

이 테이블에 대한 샘플 쿼리는 [servicehealthresources에 대한 Resource Graph](../samples/samples-by-table.md#servicehealthresources) 샘플 쿼리를 참조하세요.

- microsoft.resourcehealth/events
  - 샘플 쿼리: [활성 Service Health 이벤트 구독의 영향](../samples/samples-by-category.md#active-service-health-event-subscription-impact)
  - 샘플 쿼리: [모든 활성 상태 권고 이벤트](../samples/samples-by-category.md#all-active-health-advisory-events)
  - 샘플 쿼리: [모든 활성 계획된 유지 관리 이벤트](../samples/samples-by-category.md#all-active-planned-maintenance-events)
  - 샘플 쿼리: [모든 활성 Service Health 이벤트](../samples/samples-by-category.md#all-active-service-health-events)
  - 샘플 쿼리: [모든 활성 서비스 문제 이벤트](../samples/samples-by-category.md#all-active-service-issue-events)

## <a name="workloadmonitorresources"></a>workloadmonitorresources

- microsoft.workloadmonitor/monitors

## <a name="next-steps"></a>다음 단계

- [쿼리 언어](../concepts/query-language.md)에 대해 자세히 알아보기
- [리소스 검색](../concepts/explore-resources.md) 방법에 대해 자세히 알아보기
- [시작 쿼리](../samples/starter.md) 샘플 보기
