---
title: 템플릿 함수 - 리소스
description: Azure Resource Manager 템플릿(ARM 템플릿)에서 리소스에 대한 값을 검색하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 11/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8eb4d67ce9775995a24f4ece16991b41bffc7f0
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218209"
---
# <a name="resource-functions-for-arm-templates"></a>ARM 템플릿의 리소스 함수

Resource Manager는 Azure Resource Manager 템플릿(ARM 템플릿)에서 리소스 값을 가져오기 위한 다음 함수를 제공합니다.

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [pickZones](#pickzones)
* [공급자(사용되지 않음)](#providers)
* [reference](#reference)
* [resourceId](#resourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

매개 변수, 변수 또는 현재 배포에서 값을 가져오려면 [배포 값 함수](template-functions-deployment.md)를 참조하세요.

배포 범위 값을 가져오려면 [범위 함수](template-functions-scope.md)를 참조 하세요.

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(baseResourceId, resourceType, resourceName1, [resourceName2], ...)`

해당 기능에 추가하기 위해 다른 리소스에 적용되는 리소스 종류에 해당하는 [확장 리소스](../management/extension-resource-types.md)에 대한 리소스 ID를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| baseResourceId |예 |문자열 |확장 리소스가 적용되는 리소스의 리소스 ID입니다. |
| resourceType |예 |문자열 |리소스 공급자 네임스페이스를 포함하는 확장 리소스의 유형입니다. |
| resourceName1 |예 |문자열 |확장 리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 종류에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

이 함수에서 반환되는 리소스 ID의 기본 형식은 다음과 같습니다.

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

범위 세그먼트는 확장되는 기본 리소스에 따라 달라집니다. 예를 들어 구독 ID는 리소스 그룹 ID와 세그먼트가 다릅니다.

확장 리소스가 **리소스** 에 적용되는 경우 리소스 ID는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

확장 리소스가 **리소스 그룹** 에 적용되는 경우 반환된 형식은 다음과 같습니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

리소스 그룹과 함께 이 기능을 사용하는 예는 다음 섹션에 나와 있습니다.

확장 리소스가 **구독** 에 적용되는 경우 반환된 형식은 다음과 같습니다.

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

확장 리소스가 **관리 그룹** 에 적용되는 경우 반환된 형식은 다음과 같습니다.

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

관리 그룹과 함께 이 기능을 사용하는 예는 다음 섹션에 나와 있습니다.

### <a name="extensionresourceid-example"></a>extensionResourceId 예제

다음 예제에서는 리소스 그룹 잠금의 리소스 ID를 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/extensionresourceid.json":::

관리 그룹에 배포된 사용자 지정 정책은 확장 리소스로 구현됩니다. 정책을 만들고 할당하려면 관리 그룹에 다음 템플릿을 배포합니다.

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

기본 제공 정책 정의는 테넌트 수준 리소스입니다. 기본 제공 정책 정의 배포에 대한 예제는 [tenantResourceId](#tenantresourceid)를 참조하세요.

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

이 함수의 구문은 목록 작업의 이름에 따라 달라집니다. 각 구현은 목록 작업을 지원하는 리소스 종류의 값을 반환합니다. 작업 이름은 `list`로 시작해야 하고 접미사가 있을 수 있습니다. 일반적으로 사용되는 일부 함수는 `list`, `listKeys`, `listKeyValue` 및 `listSecrets`입니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceName 또는 resourceIdentifier |예 |문자열 |리소스에 대한 고유 식별자. |
| apiVersion |예 |문자열 |리소스 런타임 상태의 API 버전입니다. 일반적으로 **yyyy-mm-dd** 형식입니다. |
| functionValues |예 |object | 함수에 대한 값이 있는 개체입니다. 스토리지 계정의 **listAccountSas** 같은 매개 변수 값을 가진 개체를 받는 것을 지원하는 함수에 대해 이 개체를 제공합니다. 함수 값을 전달하는 예제가 이 문서에 나와 있습니다. |

### <a name="valid-uses"></a>올바른 용도

list 함수는 리소스 정의의 속성에서만 사용할 수 있습니다. 템플릿의 출력 섹션에서 중요한 정보를 노출하는 list 함수를 사용하지 마세요. 출력 값은 배포 기록에 저장되며 악의적인 사용자가 이를 검색할 수 있습니다.

[속성 반복](copy-properties.md)와 함께 사용하는 경우 식이 리소스 속성에 할당되기 때문에 `input`에 list 함수를 사용할 수 있습니다. list 함수를 확인하기 전에 개수를 결정해야 하므로 `count`와 함께 사용할 수 없습니다.

### <a name="implementations"></a>구현

다음 표에 list*의 가능한 용도가 나와 있습니다.

| 리소스 유형 | 함수 이름 |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/authorization-server/list-secrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2021-04-01-preview/gateway/list-keys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/identity-provider/list-secrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2021-04-01-preview/named-value/list-value) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/openid-connect-provider/list-secrets) |
| Microsoft.ApiManagement/service/subscriptions | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/subscription/list-secrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | listoutputfiles |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/get-build-source-upload-url) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/2020-09-01/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/2020-09-01/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-connection-strings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-keys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-04-15/notebook-workspaces/list-connection-info) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2021-12-01/domains/list-shared-access-keys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2021-12-01/topics/list-shared-access-keys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/compute/list-keys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/compute/list-nodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspaces/list-keys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft.RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/2021-04-01-preview/admin-keys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/stable/queues-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20%E2%80%93%20authorization%20rules/list-keys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Synapse/workspaces/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | `listSecrets` |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listSecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listSecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listSecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

list 작업이 있는 리소스 유형을 확인할 수 있게 다음 PowerShell 옵션이 제공됩니다.

* 리소스 공급자에 대한 [REST API 작업](/rest/api/)을 보고 list 작업을 찾습니다. 예를 들어 스토리지 계정에는 [listKeys 작업](/rest/api/storagerp/storageaccounts)이 있습니다.
* [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell cmdlet을 사용합니다. 다음 예제에서는 스토리지 계정에 대한 모든 list 작업을 가져옵니다.

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* 다음 Azure CLI 명령을 사용하여 목록 작업만 필터링합니다.

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>반환 값

반환된 개체는 사용하는 list 함수에 따라 다릅니다. 예를 들어 스토리지 계정에 대한 listKeys는 다음 형식을 반환합니다.

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

다른 list 함수는 다른 반환 형식을 갖습니다. 함수의 형식을 보려면 예제 템플릿에 표시된 것처럼 outputs 섹션에 포함합니다.

### <a name="remarks"></a>설명

리소스 이름 또는 [resourceId 함수](#resourceid)를 사용하여 리소스를 지정합니다. 참조된 리소스를 배포하는 동일한 템플릿에서 list 함수를 사용하는 경우 리소스 이름을 사용합니다.

조건부로 배포된 리소스에서 `list` 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다. `list` 함수가 존재하지 않는 리소스를 참조하면 오류가 발생합니다. `if` 함수를 사용하여 리소스가 배포될 때만 함수가 평가되는지 확인합니다. If 및 list를 조건부로 배포된 리소스와 함께 사용하는 샘플 템플릿에 대해서는 [if 함수](template-functions-logical.md#if)를 참조하세요.

### <a name="list-example"></a>목록 예제

다음 예제에서는 [배포 스크립트](deployment-script-template.md)에 대한 값을 설정할 때 listKeys를 사용합니다.

```json
"storageAccountSettings": {
  "storageAccountName": "[variables('storageAccountName')]",
  "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

다음 예제에서는 매개 변수를 사용하는 list 함수를 보여줍니다. 이 경우 함수는 **listAccountSas** 입니다. 만료 시간 동안 개체를 전달합니다. 만료 시간은 미래 시간이어야 합니다.

```json
"parameters": {
  "accountSasProperties": {
    "type": "object",
    "defaultValue": {
      "signedServices": "b",
      "signedPermission": "r",
      "signedExpiry": "2020-08-20T11:00:00Z",
      "signedResourceTypes": "s"
    }
  }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

리소스 종류가 지정된 위치 또는 지역의 영역을 지원하는지 여부를 확인합니다.  이 함수는 영역 리소스만 지원하며, 영역 중복 서비스는 빈 배열을 반환합니다.  자세한 내용은 [가용성 영역을 지원하는 Azure 서비스](../../availability-zones/az-region.md)를 참조하세요.  영역 중복 서비스에서 pickZones 함수를 사용하려면 아래 예제를 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace | 예 | 문자열 | 영역 지원을 확인할 리소스 종류에 대한 리소스 공급자 네임스페이스입니다. |
| resourceType | 예 | 문자열 | 영역 지원을 확인할 리소스 종류입니다. |
| 위치 | 예 | 문자열 | 영역 지원을 확인할 지역입니다. |
| numberOfZones | 예 | integer | 반환할 논리적 영역의 수입니다. 기본값은 1입니다. 숫자는 1~3 사이의 양수여야 합니다.  단일 영역 리소스에 대해 1을 사용합니다. 다중 영역 리소스의 경우 값은 지원되는 영역 수보다 적거나 같아야 합니다. |
| offset | 예 | integer | 시작 논리적 영역의 오프셋입니다. offset과 numberOfZones의 합이 지원되는 영역의 수를 초과하는 경우 함수에서 오류를 반환합니다. |

### <a name="return-value"></a>반환 값

지원되는 영역이 있는 배열입니다. offset 및 numberOfZones에서 기본값을 사용하는 경우 영역을 지원하는 리소스 유형 및 지역은 다음 배열을 반환합니다.

```json
[
    "1"
]
```

`numberOfZones` 매개 변수가 3으로 설정된 경우 다음을 반환합니다.

```json
[
    "1",
    "2",
    "3"
]
```

리소스 유형 또는 지역이 영역을 지원하지 않는 경우 빈 배열이 반환됩니다.  영역 중복 서비스에 대해서도 빈 배열이 반환됩니다.

```json
[
]
```

### <a name="remarks"></a>설명

Azure 가용성 영역의 범주에는 영역 및 영역 중복이 있습니다.  pickZones 함수를 사용하여 영역 리소스에 대한 가용성 영역 번호를 반환할 수 있습니다.  ZRS(영역 중복 서비스)의 경우 이 함수는 빈 배열을 반환합니다.  영역 리소스는 일반적으로 리소스 헤더에서 `zones` 속성을 사용하여 식별할 수 있습니다.  영역 중복 서비스는 리소스당 가용성 영역을 식별하고 사용하는 다양한 방법을 제공합니다. 특정 서비스에 대한 설명서를 사용하여 가용성 영역에 대한 지원 범주를 결정합니다.  자세한 내용은 [가용성 영역을 지원하는 Azure 서비스](../../availability-zones/az-region.md)를 참조하세요.

지정된 Azure 지역 또는 위치에서 가용성 영역을 지원하는지 확인하려면 영역 리소스 유형(예: `Microsoft.Storage/storageAccounts`)을 사용하여 pickZones() 함수를 호출합니다.  응답이 비어 있지 않은 경우 지역은 가용성 영역을 지원합니다.

### <a name="pickzones-example"></a>pickZones 예제

다음 템플릿은 pickZones 함수 사용에 따른 세 가지 결과를 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/pickzones.json":::

이전 예제의 출력은 3개의 배열을 반환합니다.

| Name | Type | 값 |
| ---- | ---- | ----- |
| 지원됨 | array | [ "1" ] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

pickZones의 응답을 사용하여 영역에 대해 null을 제공하거나 다른 영역에 가상 머신을 할당할지 여부를 결정할 수 있습니다. 다음 예제에서는 영역 가용성에 따라 영역에 대한 값을 설정합니다.

```json
"zones": {
  "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

다음 예제에서는 pickZones 함수를 사용하여 Cosmos DB에 대한 영역 중복을 사용하도록 설정하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/pickzones-cosmosdb.json":::

## <a name="providers"></a>providers

**providers 함수는 더 이상 사용되지 않습니다.** 더는 사용하지 않는 것이 좋습니다. 리소스 공급자의 API 버전을 가져오기 위해 이 함수를 사용한 경우 템플릿에 특정 API 버전을 제공하는 것이 좋습니다. 버전 간에 속성이 변경된 경우 동적으로 반환된 API 버전을 사용하면 템플릿이 손상될 수 있습니다.

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

리소스의 런타임 상태를 나타내는 개체를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceName 또는 resourceIdentifier |예 |문자열 |리소스의 이름 또는 고유 식별자입니다. 현재 템플릿의 리소스를 참조할 경우 리소스 이름만 매개 변수로 지정합니다. 이전에 배포된 리소스를 참조하거나 리소스 이름이 모호한 경우 리소스 ID를 제공합니다. |
| apiVersion |예 |문자열 |지정된 리소스의 API 버전입니다. **리소스가 동일한 템플릿 내에서 프로비저닝되지 않은 경우 이 매개 변수가 필요합니다.** 일반적으로 **yyyy-mm-dd** 형식입니다. 리소스에 대한 유효한 API 버전은 [템플릿 참조](/azure/templates/)를 참조하세요. |
| 'Full' |예 |문자열 |전체 리소스 개체를 반환할지 여부를 지정하는 값입니다. `'Full'`을 지정하지 않으면 리소스의 속성 개체만 반환됩니다. 전체 개체에는 리소스 ID 및 위치와 같은 값이 포함됩니다. |

### <a name="return-value"></a>반환 값

모든 리소스 형식은 reference 함수에 대해 다른 속성을 반환합니다. 이 함수는 미리 정의된 단일 형식을 반환하지 않습니다. 또한 반환되는 값은 `'Full'` 인수의 값에 따라 다릅니다. 리소스 형식에 대한 속성을 보려면 예제와 같이 outputs 섹션의 개체를 반환합니다.

### <a name="remarks"></a>설명

참조 함수는 이전에 배포한 리소스 또는 현재 템플릿에 배포된 리소스의 런타임 상태를 검색합니다. 이 문서는 두 시나리오에 대한 예제를 보여 줍니다.

일반적으로 `reference` 함수를 사용하여 blob 엔드포인트 URI 또는 정규화된 도메인 이름과 같은 개체의 특정 값을 반환합니다.

```json
"outputs": {
  "BlobUri": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
  },
  "FQDN": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]"
  }
}
```

속성 스키마의 일부가 아닌 리소스 값이 필요하면 `'Full'`을 사용합니다. 예를 들어 키 자격 증명 모음 액세스 정책을 설정하려면 가상 머신에 대한 ID 속성을 가져옵니다.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "vaultName",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>올바른 용도

참조 함수는 리소스 정의의 속성과 템플릿 또는 배포의 출력 섹션에서만 사용할 수 있습니다. [속성 반복](copy-properties.md)와 함께 사용하는 경우 식이 리소스 속성에 할당되기 때문에 `input`에 reference 함수를 사용할 수 있습니다.

reference 함수를 사용하여 복사 루프에서 `count` 속성의 값을 설정할 수 없습니다. 루프에서 다른 속성을 설정하는 데는 이 함수를 사용할 수 있습니다. reference 함수가 확인되기 전에 해당 속성을 확인해야 하기 때문에 count 속성에 대해 참조가 차단됩니다.

중첩된 템플릿의 출력 섹션에서 `reference` 함수 또는 `list*` 함수를 사용하려면 [내부 범위](linked-templates.md#expression-evaluation-scope-in-nested-templates) 평가를 사용하도록 `expressionEvaluationOptions`를 설정하거나 중첩된 템플릿 대신 연결된 템플릿을 사용해야 합니다.

조건부로 배포되는 리소스에서 `reference` 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다.  `reference` 함수가 존재하지 않는 리소스를 참조하는 경우 오류가 발생합니다. 리소스가 배포되는 경우에만 함수가 평가되도록 하려면 `if` 함수를 사용합니다. If 및 reference를 조건부로 배포된 리소스와 함께 사용하는 샘플 템플릿에 대해서는 [if 함수](template-functions-logical.md#if)를 참조하세요.

### <a name="implicit-dependency"></a>암시적 종속성

참조 함수를 사용하여 참조되는 리소스가 동일한 템플릿 내에서 프로비전되는 경우 한 리소스가 다른 리소스에 종속되도록 암시적으로 선언하고, 해당 이름별로 리소스를 참조합니다(리소스 ID 아님). 또한 dependsOn 속성도 사용할 필요가 없습니다. 참조 리소스가 배포를 완료할 때까지 함수는 평가되지 않습니다.

### <a name="resource-name-or-identifier"></a>리소스 이름 또는 식별자

동일한 템플릿에 배포된 리소스를 참조하는 경우 리소스의 이름을 제공합니다.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

동일한 템플릿에 배포되지 않은 리소스를 참조하는 경우 리소스 ID 및 `apiVersion`을 제공합니다.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

참조하는 리소스에 대한 모호성을 피하려면 정규화된 리소스 식별자를 제공할 수 있습니다.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

리소스에 대한 정규화된 참조를 생성할 때 형식과 이름의 세그먼트를 결합하는 순서는 단순히 두 세그먼트의 연결이 아닙니다. 대신, 네임스페이스 뒤에 구체성이 낮은 순으로 *형식/이름* 쌍의 시퀀스를 사용합니다.

`{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]`

다음은 그 예입니다.

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`는 올바릅니다. `Microsoft.Compute/virtualMachines/extensions/myVM/myExt`는 올바르지 않습니다.

리소스 ID 만들기를 간소화하려면 `concat()` 함수 대신이 문서에 설명된 `resourceId()` 함수를 사용합니다.

### <a name="get-managed-identity"></a>관리 ID 가져오기

[Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)는 일부 리소스에 대해 암시적으로 생성되는 [확장 리소스 종류](../management/extension-resource-types.md)입니다. 관리 ID가 템플릿에 명시적으로 정의되어 있지 않기 때문에 ID가 적용되는 리소스를 참조해야 합니다. `Full`을 사용하여 암시적으로 생성된 ID를 비롯한 모든 속성을 가져옵니다.

패턴은 다음과 같습니다.

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

예를 들어, 가상 머신에 적용되는 관리 ID에 대한 보안 주체 ID를 가져오려면 다음을 사용합니다.

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

또는, 가상 머신 확장 집합에 적용되는 관리 ID에 대한 테넌트 ID를 가져오려면 다음을 사용합니다.

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>참조 예제

다음 예제에서는 리소스를 배포하고 해당 리소스를 참조합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/referencewithstorage.json":::

앞의 예제에서는 두 개의 개체를 반환합니다. 속성 개체의 형식은 다음과 같습니다.

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

전체 개체의 형식은 다음과 같습니다.

```json
{
  "apiVersion":"2021-04-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2021-04-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

다음 예제 템플릿에서는 이 템플릿에 배포되지 않는 스토리지 계정을 참조합니다. 스토리지 계정은 동일한 구독 내에 있어야 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/reference.json":::

## <a name="resourcegroup"></a>resourceGroup

[resourceGroup 범위 함수를](template-functions-scope.md#resourcegroup)참조하세요.

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

리소스의 고유 식별자를 반환합니다. 리소스 이름이 모호하거나 동일한 템플릿 내에서 프로비전되지 않은 경우 이 함수를 사용합니다. 반환된 식별자의 형식은 리소스 그룹, 구독, 관리 그룹 또는 테넌트 중에서 테넌트가 어떤 범위에서 발생하는지에 따라 달라집니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |예 |문자열(GUID 형식) |기본값은 현재 구독입니다. 다른 구독에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. 리소스 그룹 또는 구독의 범위에서 배포하는 경우에만 이 값을 제공합니다. |
| resourceGroupName |예 |문자열 |기본값은 현재 리소스 그룹입니다. 다른 리소스 그룹에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. 리소스 그룹의 범위에 배포하는 경우에만 이 값을 제공합니다. |
| resourceType |예 |문자열 |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |예 |문자열 |리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 종류에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

템플릿이 리소스 그룹의 범위에서 배포될 때 리소스 ID는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

다른 배포 범위에 대해 resourceId 함수를 사용할 수 있지만 ID의 형식이 변경됩니다.

구독에 배포하는 동안 resourceId를 사용하는 경우 리소스 ID가 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

관리 그룹 또는 테넌트에 배포하는 동안 resourceId를 사용하는 경우 리소스 ID가 다음 형식으로 반환됩니다.

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

혼동을 피하기 위해 구독, 관리 그룹 또는 테넌트에 배포된 리소스를 사용할 때 `resourceId`를 사용하지 않는 것이 좋습니다. 대신 범위에 대해 설계된 ID 함수를 사용합니다.

[구독 수준 리소스](deploy-to-subscription.md)의 경우 [subscriptionResourceId](#subscriptionresourceid) 함수를 사용합니다.

[관리 그룹 수준 리소스](deploy-to-management-group.md)의 경우 [extensionResourceId](#extensionresourceid) 함수를 사용하여 관리 그룹의 확장으로 구현된 리소스를 참조합니다. 예를 들어 관리 그룹에 배포되는 사용자 지정 정책 정의는 관리 그룹의 확장입니다. [tenantResourceId](#tenantresourceid) 함수를 사용하여 테넌트에 배포되었지만 관리 그룹에서 사용할 수 있는 리소스를 참조합니다. 예를 들어 기본 제공 정책 정의는 테넌트 수준 리소스로 구현됩니다.

[테넌트 수준 리소스](deploy-to-tenant.md)의 경우 [tenantResourceId](#tenantresourceid) 함수를 사용합니다. 테넌트 수준에서 구현되므로 기본 제공 정책 정의에 대해 tenantResourceId를 사용합니다.

### <a name="remarks"></a>설명

제공하는 매개 변수 수는 리소스가 부모 리소스인지 또는 자식 리소스인지, 리소스가 동일한 구독에 있는지 또는 리소스 그룹에 있는지에 따라 달라집니다.

동일한 구독 및 리소스 그룹에 있는 부모 리소스의 리소스 ID를 가져오려면 리소스의 종류 및 이름을 제공합니다.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

자식 리소스의 리소스 ID를 가져오려면 리소스 종류의 세그먼트 수에 주의합니다. 리소스 종류의 각 세그먼트에 대한 리소스 이름을 제공합니다. 세그먼트의 이름은 계층의 해당 부분에 존재하는 리소스에 해당합니다.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

같은 구독, 다른 리소스 그룹에 있는 리소스의 리소스 ID를 가져오려면 리소스 그룹 이름을 제공합니다.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

다른 구독 및 리소스 그룹에 있는 리소스의 리소스 ID를 가져오려면 구독 ID와 리소스 그룹 이름을 제공합니다.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

대체 리소스 그룹의 스토리지 계정 또는 가상 네트워크를 사용할 경우 이 함수를 사용해야 합니다. 다음 예에서는 외부 리소스 그룹의 리소스를 쉽게 사용할 수 있는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/resourceid-external.json":::

### <a name="resource-id-example"></a>리소스 ID 예제

다음 예제에서는 리소스 그룹의 스토리지 계정에 대한 리소스 ID를 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/resourceid.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscription

구독 [범위 함수](template-functions-scope.md#subscription)를 참조하세요.

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

구독 수준에서 배포된 리소스의 고유 식별자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |예 |문자열(GUID 형식) |기본값은 현재 구독입니다. 다른 구독에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. |
| resourceType |예 |문자열 |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |예 |문자열 |리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 종류에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

식별자는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>설명

이 함수를 사용하여 리소스 그룹이 아닌 [구독에 배포된](deploy-to-subscription.md) 리소스의 리소스 ID를 가져올 수 있습니다. 반환되는 ID는 리소스 그룹 값을 포함하지 않으므로 [resourceId](#resourceid) 함수에서 반환하는 값과 다릅니다.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID 예제

다음 템플릿은 기본 제공 역할을 할당합니다. 리소스 그룹 또는 구독에 배포할 수 있습니다. 함수를 사용하여 `subscriptionResourceId` 기본 제공 역할의 리소스 ID를 얻습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/subscriptionresourceid.json":::

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

테넌트 수준에서 배포된 리소스의 고유 식별자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceType |예 |문자열 |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |예 |문자열 |리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 종류에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

식별자는 다음 형식으로 반환됩니다.

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>설명

이 함수를 사용하여 테넌트에 배포된 리소스의 리소스 ID를 가져옵니다. 반환되는 ID는 리소스 그룹 또는 구독 값을 포함하지 않으므로 다른 리소스 ID 함수에서 반환하는 값과 다릅니다.

### <a name="tenantresourceid-example"></a>tenantResourceId 예제

기본 제공 정책 정의는 테넌트 수준 리소스입니다. 기본 제공 정책 정의를 참조하는 정책 할당을 배포하려면 `tenantResourceId` 함수를 사용합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/tenantresourceid.json":::

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
* 여러 템플릿을 병합하려면 [Azure 리소스를 배포할 때 연결된 템플릿 및 중첩된 템플릿 사용](linked-templates.md)을 참조하세요.
* 리소스 형식을 만들 때 지정된 횟수만큼 반복하려면 [ARM 템플릿의 리소스 반복](copy-resources.md)을 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
