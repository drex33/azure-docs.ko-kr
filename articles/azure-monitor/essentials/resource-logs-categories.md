---
title: Azure Monitor 리소스 로그 지원 서비스 및 범주
description: Azure Monitor 참조. Azure 리소스 로그에 대해 지원되는 서비스 및 이벤트 스키마를 이해합니다.
ms.topic: reference
ms.date: 08/04/2021
ms.openlocfilehash: 2cbe701f3fbedacf29124c823743966be1f7ba32
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530158"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Azure 리소스 로그에 대해 지원되는 범주

> [!NOTE]
> 이 목록은 대부분 자동으로 생성됩니다. GitHub를 통해 수정된 내용이 경고 없이 적용될 수 있습니다. 영구적으로 업데이트하는 방법에 대한 자세한 내용은 이 문서의 작성자에게 문의하세요.

[Azure Monitor 리소스 로그](../essentials/platform-logs-overview.md)는 해당 서비스 또는 리소스의 작업을 설명하는 Azure 서비스에서 내보낸 로그입니다. Azure Monitor를 통해 사용할 수 있는 모든 리소스 로그는 최상위 공통 스키마를 공유하며, 각 서비스가 자체 이벤트에 대한 고유한 속성을 유연하게 내보낼 수 있습니다.

이전에는 리소스 로그를 진단 로그라고 했습니다. Azure Monitor에서 수집하는 로그 유형이 Azure 리소스 이상을 포함하도록 전환하면서 2019년 10월에 이름이 변경되었습니다.

리소스 종류(`resourceId` 속성에 제공) 및 `category`가 조합되어 스키마를 고유하게 식별합니다. 이때 다른 로그 범주에 대해 추가된 서비스별 필드를 포함하는 모든 리소스 로그에 대한 공통 스키마가 있습니다. 자세한 내용은 [Azure 리소스 로그에 대한 공통 및 서비스별 스키마]()를 참조하세요.

## <a name="costs"></a>비용

[Azure Monitor Log Analytics](https://azure.microsoft.com/pricing/details/monitor/), [Azure Storage](https://azure.microsoft.com/product-categories/storage/), [이벤트 허브](https://azure.microsoft.com/pricing/details/event-hubs/) 및 Azure Monitor와 직접 통합되는 파트너(예: [Datadog](../../partner-solutions/datadog/overview.md))에는 데이터 수집 및 데이터 저장과 관련된 비용이 있습니다. 이러한 비용을 파악하려면 해당 서비스에 대한 가격 책정 페이지의 이전 링크를 확인하세요. 리소스 로그는 해당 위치에 보낼 수 있는 데이터의 유형 중 하나일 뿐입니다. 

또한 리소스 로그의 일부 범주를 해당 위치로 내보내는 데 비용이 발생할 수 있습니다. 내보내기 비용이 포함된 로그는 아래 표에 나열되어 있습니다. 내보내기 가격 책정에 대한 자세한 내용은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)의 *플랫폼 로그* 섹션을 참조하세요.

## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주

다음은 각 리소스 종류에 사용할 수 있는 로그의 유형 목록입니다. 

일부 범주는 특정 리소스 종류만 지원할 수 있습니다. 리소스가 누락된 것으로 생각되는 경우 리소스 관련 설명서를 참조하세요. 예를 들어 일부 유형의 데이터베이스에서는 Microsoft.Sql/servers/databases 범주를 사용할 수 없습니다. 자세한 내용은 [SQL Database 진단 로깅에 대한 정보](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)를 참조하세요. 

누락된 내용이 있다고 생각되는 경우 이 문서의 맨 아래에 있는 GitHub 주석을 열 수 있습니다.


## <a name="microsoftaaddomainservices"></a>Microsoft.AAD/DomainServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AccountLogon|AccountLogon|No|
|AccountManagement|AccountManagement|No|
|DetailTracking|DetailTracking|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|PolicyChange|PolicyChange|No|
|PrivilegeUse|PrivilegeUse|No|
|SystemSecurity|SystemSecurity|No|


## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|로그인|로그인|Yes|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|엔진|엔진|No|
|서비스|서비스|예|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|GatewayLogs|ApiManagement 게이트웨이 관련 로그|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|감사|감사|Yes|
|HttpRequest|HTTP 요청|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ApplicationConsole|애플리케이션 콘솔|No|
|SystemLogs|시스템 로그|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AuditEvent|AuditEvent 메시지 로그 범주입니다.|No|
|ERR|오류 메시지 로그 범주입니다.|No|
|INF|정보 메시지 로그 범주입니다.|No|
|WRN|경고 메시지 로그 범주입니다.|예|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DscNodeStatus|디스크 노드 상태|No|
|JobLogs|작업 로그|No|
|JobStreams|작업 스트림|No|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft.AutonomousDevelopmentPlatform/accounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|감사|감사|Yes|
|작동|작동|Yes|
|요청|요청|Yes|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ServiceLog|서비스 로그|예|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|BlockchainApplication|블록체인 애플리케이션|No|
|FabricOrderer|패브릭 Orderer|No|
|FabricPeer|패브릭 피어|No|
|프록시|프록시|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|BlockchainApplication|블록체인 애플리케이션|No|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|BotRequest|채널에서 봇으로 요청|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|WebApplicationFirewallLogs|웹 애플리케이션 방화벽 로그|No|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AzureCdnAccessLog|Azure CDN 액세스 로그|No|
|FrontDoorAccessLog|FrontDoor 액세스 로그|Yes|
|FrontDoorHealthProbeLog|FrontDoor 상태 프로브 로그|Yes|
|FrontDoorWebApplicationFirewallLog|FrontDoor WebApplicationFirewall 로그|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|CoreAnalytics|엔드포인트의 메트릭(예: 대역폭, 송신 등)을 가져옵니다.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|네트워크 보안 그룹 규칙 흐름 이벤트|네트워크 보안 그룹 규칙 흐름 이벤트|예|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|감사|감사 로그|No|
|RequestResponse|요청 및 응답 로그|No|
|추적|추적 로그|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AuthOperational|운영 인증 로그|Yes|
|CallDiagnosticsPRIVATEPREVIEW|진단 로그 호출 - 프라이빗 미리 보기|Yes|
|CallSummaryPRIVATEPREVIEW|요약 로그 호출 - 프라이빗 미리 보기|Yes|
|ChatOperational|운영 채팅 로그|No|
|SMSOperational|운영 SMS 로그|No|
|사용량|사용량 레코드|예|


## <a name="microsoftconnectedvehicleplatformaccounts"></a>Microsoft.ConnectedVehicle/platformAccounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|로그|MCVP 로그|Yes|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ContainerRegistryLoginEvents|로그인 이벤트|No|
|ContainerRegistryRepositoryEvents|RepositoryEvent 로그|예|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|cluster-autoscaler|Kubernetes 클러스터 자동 크기 조정기|No|
|가드|가드|No|
|kube-apiserver|Kubernetes API 서버|No|
|kube-audit|Kubernetes 감사|No|
|kube-audit-admin|Kubernetes 감사 관리자 로그|No|
|kube-controller-manager|Kubernetes 컨트롤러 관리자|No|
|kube-scheduler|Kubernetes 스케줄러|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AuditLogs|MiniRP 호출에 대한 감사 로그|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft.D365CustomerInsights/instances

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|감사|감사 이벤트|No|
|작동|작업 이벤트|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|계정|Databricks 계정|예|
|clusters|Databricks 클러스터|No|
|dbfs|Databricks 파일 시스템|No|
|featureStore|Databricks 기능 저장소|Yes|
|genie|Databricks Genie|Yes|
|globalInitScripts|Databricks 글로벌 init 스크립트|Yes|
|iamRole|Databricks IAM 역할|Yes|
|instancepools|인스턴스 풀|예|
|jobs|Databricks 작업|No|
|mlflowAcledArtifact|Databricks MLFlow Acled 아티팩트|Yes|
|mlflowExperiment|Databricks MLFlow 실험|Yes|
|Notebook|Databricks Notebook|예|
|RemoteHistoryService|Databricks 원격 기록 서비스|예|
|secrets|Databricks 비밀|No|
|sqlanalytics|Databricks SQL Analytics|Yes|
|sqlPermissions|Databricks SQLPermissions|No|
|ssh|Databricks SSH|No|
|작업 영역|Databricks 작업 영역|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|CollaborationAudit|협업 감사|Yes|
|DataAssets|데이터 자산|No|
|Pipelines|Pipelines|No|
|Proposals|제안|No|
|스크립트|스크립트|예|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ActivityRuns|파이프라인 작업 실행 로그|No|
|PipelineRuns|파이프라인 실행 로그|No|
|SandboxActivityRuns|샌드박스 작업 실행 로그|Yes|
|SandboxPipelineRuns|파이프라인 실행 로그|Yes|
|SSISIntegrationRuntimeLogs|SSIS 통합 런타임 로그|No|
|SSISPackageEventMessageContext|SSIS 패키지 이벤트 메시지 컨텍스트|No|
|SSISPackageEventMessages|SSIS 패키지 이벤트 메시지|No|
|SSISPackageExecutableStatistics|SSIS 패키지 실행 파일 통계|No|
|SSISPackageExecutionComponentPhases|SSIS 패키지 실행 구성 요소 단계|No|
|SSISPackageExecutionDataStatistics|SSIS 패키지 실행 데이터 통계|No|
|TriggerRuns|트리거 실행 로그|예|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|감사|감사 로그|No|
|요청|요청 로그|예|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|감사|감사 로그|No|
|요청|요청 로그|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ReceivedShareSnapshots|받은 공유 스냅샷|No|
|SentShareSnapshots|보낸 공유 스냅샷|No|
|공유|공유|No|
|ShareSubscriptions|공유 구독|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|MySqlAuditLogs|MariaDB 감사 로그|No|
|MySqlSlowLogs|MariaDB 서버 로그|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|MySqlAuditLogs|MySQL 감사 로그|No|
|MySqlSlowLogs|MySQL 저속 로그|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|MySqlAuditLogs|MySQL 감사 로그|No|
|MySqlSlowLogs|MySQL Server 로그|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|PostgreSQLLogs|PostgreSQL 서버 로그|No|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|PostgreSQLLogs|PostgreSQL 서버 로그|Yes|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|PostgreSQLLogs|PostgreSQL 서버 로그|No|
|QueryStoreRuntimeStatistics|PostgreSQL 쿼리 저장소 런타임 통계|No|
|QueryStoreWaitStatistics|PostgreSQL 쿼리 저장소 대기 통계|예|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|PostgreSQLLogs|PostgreSQL 서버 로그|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|검사점|검사점|No|
|Error|Error|No|
|관리|관리|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|No|
|검사점|검사점|No|
|연결|연결|No|
|Error|Error|No|
|HostRegistration|HostRegistration|No|
|관리|관리|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|검사점|검사점|No|
|Error|Error|No|
|피드|피드|No|
|관리|관리|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|C2DCommands|C2D 명령|No|
|C2DTwinOperations|C2D 쌍 작업|No|
|구성|구성|No|
|Connections|Connections|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|디바이스 ID 작업|No|
|DeviceStreams|디바이스 스트림(미리 보기)|No|
|DeviceTelemetry|디바이스 원격 분석|No|
|DirectMethods|직접 메서드|No|
|DistributedTracing|분산 추적(미리 보기)|No|
|FileUploadOperations|파일 업로드 작업|No|
|JobsOperations|작업 연산|No|
|경로|경로|No|
|TwinQueries|쌍 쿼리|예|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|C2DCommands|C2D 명령|No|
|C2DTwinOperations|C2D 쌍 작업|No|
|구성|구성|No|
|Connections|Connections|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|디바이스 ID 작업|No|
|DeviceStreams|디바이스 스트림(미리 보기)|No|
|DeviceTelemetry|디바이스 원격 분석|No|
|DirectMethods|직접 메서드|No|
|DistributedTracing|분산 추적(미리 보기)|No|
|FileUploadOperations|파일 업로드 작업|No|
|JobsOperations|작업 연산|No|
|경로|경로|No|
|TwinQueries|쌍 쿼리|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DeviceOperations|디바이스 작업|No|
|ServiceOperations|서비스 작업|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|QueryOperation|QueryOperation|예|
|ResourceProviderOperation|ResourceProviderOperation|Yes|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/DatabaseAccounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|예|
|TableApiRequests|TableApiRequests|Yes|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DeliveryFailures|전송 실패 로그|No|
|PublishFailures|게시 실패 로그|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DeliveryFailures|전송 실패 로그|No|
|PublishFailures|게시 실패 로그|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DeliveryFailures|전송 실패 로그|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DeliveryFailures|전송 실패 로그|No|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DeliveryFailures|전송 실패 로그|No|
|PublishFailures|게시 실패 로그|예|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ArchiveLogs|보관 로그|No|
|AutoScaleLogs|자동 크기 조정 로그|No|
|CustomerManagedKeyUserLogs|고객 관리형 키 로그|No|
|EventHubVNetConnectionEvent|VNet/IP 필터링 연결 로그|No|
|KafkaCoordinatorLogs|Kafka 코디네이터 로그|No|
|KafkaUserErrorLogs|Kafka 사용자 오류 로그|No|
|OperationalLogs|작업 로그|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>microsoft.experimentation/experimentWorkspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|요청|요청|No|


## <a name="microsofthealthcareapisworkspacesdicomservices"></a>Microsoft.HealthcareApis/workspaces/dicomservices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AuditLogs|감사 로그|Yes|


## <a name="microsofthealthcareapisworkspacesfhirservices"></a>Microsoft.HealthcareApis/workspaces/fhirservices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AuditLogs|FHIR 감사 로그|Yes|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AutoscaleEvaluations|자동 크기 조정 평가|No|
|AutoscaleScaleActions|자동 크기 조정 크기 조정 작업|예|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AppAvailabilityResults|가용성 결과|No|
|AppBrowserTimings|브라우저 타이밍|No|
|AppDependencies|종속성|No|
|AppEvents|이벤트|예|
|AppExceptions|예외|No|
|AppMetrics|메트릭|No|
|AppPageViews|페이지 보기|No|
|AppPerformanceCounters|성능 카운터|No|
|AppRequests|요청|No|
|AppSystemEvents|시스템 이벤트|No|
|AppTraces|추적|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AuditEvent|감사 이벤트|예|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AuditEvent|감사 로그|No|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|명령|명령|No|
|FailedIngestion|실패한 수집 작업|No|
|IngestionBatching|수집 일괄 처리|No|
|쿼리|쿼리|No|
|SucceededIngestion|성공한 수집 작업|No|
|TableDetails|테이블 세부 정보|No|
|TableUsageStatistics|테이블 사용량 통계|No|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/IntegrationAccounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|예|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/Workflows

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|WorkflowRuntime|워크플로 런타임 진단 이벤트|예|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|예|
|ComputeInstanceEvent|ComputeInstanceEvent|Yes|
|DataLabelChangeEvent|DataLabelChangeEvent|Yes|
|DataLabelReadEvent|DataLabelReadEvent|Yes|
|DataSetChangeEvent|DataSetChangeEvent|Yes|
|DataSetReadEvent|DataSetReadEvent|Yes|
|DataStoreChangeEvent|DataStoreChangeEvent|Yes|
|DataStoreReadEvent|DataStoreReadEvent|Yes|
|DeploymentEventACI|DeploymentEventACI|Yes|
|DeploymentEventAKS|DeploymentEventAKS|Yes|
|DeploymentReadEvent|DeploymentReadEvent|Yes|
|EnvironmentChangeEvent|EnvironmentChangeEvent|Yes|
|EnvironmentReadEvent|EnvironmentReadEvent|Yes|
|InferencingOperationACI|InferencingOperationACI|Yes|
|InferencingOperationAKS|InferencingOperationAKS|Yes|
|ModelsActionEvent|ModelsActionEvent|Yes|
|ModelsChangeEvent|ModelsChangeEvent|Yes|
|ModelsReadEvent|ModelsReadEvent|Yes|
|PipelineChangeEvent|PipelineChangeEvent|Yes|
|PipelineReadEvent|PipelineReadEvent|Yes|
|RunEvent|RunEvent|Yes|
|RunReadEvent|RunReadEvent|Yes|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|KeyDeliveryRequests|키 전송 요청|예|
|MediaAccount|미디어 계정 상태|Yes|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationgateways

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway 액세스 로그|No|
|ApplicationGatewayFirewallLog|Application Gateway 방화벽 로그|No|
|ApplicationGatewayPerformanceLog|Application Gateway 성능 로그|예|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall 애플리케이션 규칙|No|
|AzureFirewallDnsProxy|Azure Firewall DNS 프록시|No|
|AzureFirewallNetworkRule|Azure Firewall 네트워크 규칙|No|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|BastionAuditLogs|베스천 감사 로그|예|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|PeeringRouteLog|피어링 경로 테이블 로그|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|FrontdoorAccessLog|Frontdoor 액세스 로그|No|
|FrontdoorWebApplicationFirewallLog|Frontdoor 웹 애플리케이션 방화벽 로그|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|No|
|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|NetworkSecurityGroupEvent|네트워크 보안 그룹 이벤트|No|
|NetworkSecurityGroupFlowEvent|네트워크 보안 그룹 규칙 흐름 이벤트|No|
|NetworkSecurityGroupRuleCounter|네트워크 보안 그룹 규칙 카운터|No|


## <a name="microsoftnetworkp2svpngateways"></a>microsoft.network/p2svpngateways

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|GatewayDiagnosticLog|게이트웨이 진단 로그|No|
|IKEDiagnosticLog|IKE 진단 로그|No|
|P2SDiagnosticLog|P2P 진단 로그|예|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DDoSMitigationFlowLogs|DDoS 완화 결정의 흐름 로그|No|
|DDoSMitigationReports|DDoS 완화의 보고서|No|
|DDoSProtectionNotifications|DDoS 보호 알림|예|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager 프로브 상태 결과 이벤트|예|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>microsoft.network/virtualnetworkgateways

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|GatewayDiagnosticLog|게이트웨이 진단 로그|No|
|IKEDiagnosticLog|IKE 진단 로그|No|
|P2SDiagnosticLog|P2P 진단 로그|No|
|RouteDiagnosticLog|경로 진단 로그|No|
|TunnelDiagnosticLog|터널 진단 로그|예|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|VMProtectionAlerts|VM 보호 경고|No|


## <a name="microsoftnetworkvpngateways"></a>microsoft.network/vpngateways

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|GatewayDiagnosticLog|게이트웨이 진단 로그|No|
|IKEDiagnosticLog|IKE 진단 로그|No|
|RouteDiagnosticLog|경로 진단 로그|No|
|TunnelDiagnosticLog|터널 진단 로그|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|OperationalLogs|작업 로그|예|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|감사|감사|Yes|


## <a name="microsoftpowerbitenants"></a>Microsoft.PowerBI/tenants

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|엔진|엔진|No|

## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft.PowerBI/tenants/workspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|엔진|엔진|예|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|엔진|엔진|No|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DataSensitivityLogEvent|DataSensitivity|Yes|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AddonAzureBackupAlerts|추가 기능 Azure Backup 경고 데이터|No|
|AddonAzureBackupJobs|추가 기능 Azure Backup 작업 데이터|No|
|AddonAzureBackupPolicy|추가 기능 Azure Backup 정책 데이터|No|
|AddonAzureBackupProtectedInstance|추가 기능 Azure Backup 보호된 인스턴스 데이터|No|
|AddonAzureBackupStorage|추가 기능 Azure Backup 스토리지 데이터|No|
|AzureBackupReport|Azure Backup 보고 데이터|No|
|AzureSiteRecoveryEvents|Azure Site Recovery 이벤트|No|
|AzureSiteRecoveryJobs|Azure Site Recovery 작업|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 보호된 디스크 데이터 변동|No|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 복구 지점|No|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 복제된 항목|No|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 복제 데이터 업로드 속도|No|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery 복제 통계|No|
|CoreAzureBackup|핵심 Azure Backup 데이터|예|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|HybridConnectionsEvent|HybridConnections 이벤트|No|
|HybridConnectionsLogs|HybridConnectionsLogs|예|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|OperationLogs|작업 로그|예|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|OperationalLogs|작업 로그|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AllLogs|Azure SignalR Service 로그.|No|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AllLogs|Azure 웹 PubSub 서비스 로그.|Yes|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DevOpsOperationsAudit|Devops 작업 감사 로그|No|
|ResourceUsageStats|리소스 사용량 통계|No|
|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|오류|오류|No|
|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|No|
|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|No|
|SQLInsights|SQL 정보|예|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AutomaticTuning|자동 조정|No|
|블록|블록|No|
|DatabaseWaitStatistics|데이터베이스 대기 통계|No|
|교착 상태|교착 상태|No|
|DevOpsOperationsAudit|Devops 작업 감사 로그|No|
|DmsWorkers|DMS 작업자|No|
|오류|오류|No|
|ExecRequests|실행 요청|No|
|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|No|
|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|No|
|RequestSteps|요청 단계|No|
|SQLInsights|SQL 정보|No|
|SqlRequests|SQL 요청|No|
|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|No|
|시간 제한|시간 제한|No|
|대기|대기|예|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|작성|작성|No|
|실행|실행|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|BuiltinSqlReqsEnded|기본 제공 SQL 풀 요청이 종료됨|No|
|GatewayApiRequests|Synapse 게이트웨이 API 요청|No|
|IntegrationActivityRuns|통합 작업 실행|Yes|
|IntegrationPipelineRuns|통합 파이프라인 실행|Yes|
|IntegrationTriggerRuns|통합 트리거 실행|Yes|
|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|No|
|SynapseRbacOperations|Synapse RBAC 작업|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|BigDataPoolAppsEnded|빅 데이터 풀 애플리케이션이 종료됨|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|DmsWorkers|DMS 작업자|No|
|ExecRequests|실행 요청|No|
|RequestSteps|요청 단계|No|
|SqlRequests|SQL 요청|No|
|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|No|
|대기|대기|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|수신|수신|No|
|관리|관리|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|수신|수신|No|
|관리|관리|No|


## <a name="microsoftwebhostingenvironments"></a>Microsoft.Web/hostingEnvironments

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment 플랫폼 로그|No|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|바이러스 백신 감사 로그 보고|No|
|AppServiceAppLogs|App Service 애플리케이션 로그|No|
|AppServiceAuditLogs|액세스 감사 로그|No|
|AppServiceConsoleLogs|App Service 콘솔 로그|No|
|AppServiceFileAuditLogs|사이트 콘텐츠 변경 감사 로그|No|
|AppServiceHTTPLogs|HTTP 로그|No|
|AppServiceIPSecAuditLogs|IPSecurity 감사 로그|No|
|AppServicePlatformLogs|App Service 플랫폼 로그|No|
|FunctionAppLogs|함수 애플리케이션 로그|No|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|범주|범주 표시 이름|내보낼 비용|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|바이러스 백신 감사 로그 보고|No|
|AppServiceAppLogs|App Service 애플리케이션 로그|No|
|AppServiceAuditLogs|액세스 감사 로그|No|
|AppServiceConsoleLogs|App Service 콘솔 로그|No|
|AppServiceDiagnosticToolsLogs|보고서 진단 도구 로그|Yes|
|AppServiceFileAuditLogs|사이트 콘텐츠 변경 감사 로그|No|
|AppServiceHTTPLogs|HTTP 로그|No|
|AppServiceIPSecAuditLogs|IPSecurity 감사 로그|No|
|AppServicePlatformLogs|App Service 플랫폼 로그|No|
|FunctionAppLogs|함수 애플리케이션 로그|No|


## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대한 자세한 정보](../essentials/platform-logs-overview.md)
* [**Event Hubs** 로 리소스 로그 스트리밍](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API를 사용하여 리소스 로그 진단 설정 변경](/rest/api/monitor/diagnosticsettings)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](./resource-logs.md#send-to-log-analytics-workspace)