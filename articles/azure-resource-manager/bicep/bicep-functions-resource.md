---
title: Bicep 함수 - 리소스
description: Bicep 파일에서 리소스에 대한 값을 검색하는 데 사용할 함수를 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: bc0fbd90ba29c9ab6b0940863cd2ec290992f5d7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264963"
---
# <a name="resource-functions-for-bicep"></a>Bicep의 리소스 함수

이 문서에서는 리소스 값을 얻기 위한 Bicep 함수에 대해 설명합니다.

현재 배포에서 값을 가져오려면 [배포 값 함수](./bicep-functions-deployment.md)를 참조하세요.

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

해당 기능에 추가하기 위해 다른 리소스에 적용되는 리소스 종류에 해당하는 [확장 리소스](../management/extension-resource-types.md)에 대한 리소스 ID를 반환합니다.

네임스페이스: [az](bicep-functions.md#namespaces-for-functions).

extensionResourceId 함수는 Bicep 파일에서 사용할 수 있지만 일반적으로는 필요하지 않습니다. 대신 리소스에 대한 기호 이름을 사용하고 `id` 속성에 액세스합니다.

이 함수에서 반환되는 리소스 ID의 기본 형식은 다음과 같습니다.

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

범위 세그먼트는 확장되는 리소스에 따라 달라집니다.

확장 리소스가 **리소스** 에 적용되는 경우 리소스 ID는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

확장 리소스가 **리소스 그룹** 에 적용되는 경우 형식은 다음과 같습니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

확장 리소스가 **구독** 에 적용되는 경우 형식은 다음과 같습니다.

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

확장 리소스가 **관리 그룹** 에 적용되는 경우 형식은 다음과 같습니다.

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

관리 그룹에 배포된 사용자 지정 정책은 확장 리소스로 구현됩니다. 정책을 만들고 할당하려면 관리 그룹에 다음 Bicep 파일을 배포합니다.

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

---

기본 제공 정책 정의는 테넌트 수준 리소스입니다. 기본 제공 정책 정의 배포에 대한 예제는 [tenantResourceId](#tenantresourceid)를 참조하세요.

## <a name="getsecret"></a>getSecret

`keyVaultName.getSecret(secretName)`

Azure Key Vault에서 비밀 반환 `getSecret` 함수는 `Microsoft.KeyVault/vaults` 리소스에서만 호출될 수 있습니다. 이 함수를 사용하여 Bicep 모듈의 보안 문자열 매개 변수에 비밀을 전달합니다. 함수는 `@secure()` 데코레이터가 있는 매개 변수에서만 사용될 수 있습니다.

Key Vault에는 `true`로 설정된 `enabledForTemplateDeployment`가 있어야 합니다. Bicep 파일을 배포하는 사용자가 비밀에 액세스할 수 있어야 합니다. 자세한 내용은 [Azure Key Vault를 사용하여 Bicep 배포 중에 보안 매개 변수 값 전달](key-vault-parameter.md)을 참조하세요.

함수가 리소스 종류와 함께 사용되므로 [네임스페이스 한정자도](bicep-functions.md#namespaces-for-functions) 필요하지 않습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| secretName | 예 | 문자열 | 키 자격 증명 모음에 저장된 비밀의 이름입니다. |

### <a name="return-value"></a>반환 값

비밀 이름의 비밀 값입니다.

### <a name="example"></a>예제

다음 Bicep 파일은 모듈로 사용됩니다.  여기에는 `@secure()` 데코레이터로 정의된 *adminPassword* 매개 변수가 있습니다.

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  ...
}
```

다음 Bicep 파일은 이전 Bicep 파일을 모듈로 사용합니다. Bicep 파일은 기존 주요 자격 증명 모음을 참조하고 `getSecret` 함수를 호출하여 주요 자격 증명 모음 비밀을 검색한 다음, 값을 매개 변수로 모듈에 전달합니다.

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`resourceName.list([apiVersion], [functionValues])`

`list`로 시작하는 작업을 통해 모든 리소스 종류에 대한 목록 함수를 호출할 수 있습니다. 일반적으로 사용되는 일부 함수는 `list`, `listKeys`, `listKeyValue` 및 `listSecrets`입니다.

이 함수의 구문은 목록 작업의 이름에 따라 다릅니다. 또한 반환된 값은 연산에 따라 달라집니다. Bicep은 현재 `list*` 함수 완성 및 유효성 검사를 지원하지 않습니다.

**Bicep 버전 0.4.412 이상** 에서는 [접근자 연산자](operators-access.md#function-accessor)를 사용하여 목록 함수를 호출합니다. 예: `stg.listKeys()`.

함수가 리소스 종류와 함께 사용되므로 [네임스페이스 한정자도](bicep-functions.md#namespaces-for-functions) 필요하지 않습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| apiVersion |예 |문자열 |이 매개 변수를 제공하지 않으면 리소스에 대한 API 버전이 사용됩니다. 함수를 특정 버전으로 실행해야 하는 경우에만 사용자 지정 API 버전을 제공합니다. **yyyy-mm-dd** 형식을 사용합니다. |
| functionValues |예 |object | 함수에 대한 값이 있는 개체입니다. 스토리지 계정의 **listAccountSas** 같은 매개 변수 값을 가진 개체를 받는 것을 지원하는 함수에 대해 이 개체를 제공합니다. 함수 값을 전달하는 예제가 이 문서에 나와 있습니다. |

### <a name="valid-uses"></a>올바른 용도

list 함수는 리소스 정의의 속성에서만 사용할 수 있습니다. Bicep 파일의 출력 섹션에서 중요한 정보를 노출하는 목록 함수를 사용하지 마세요. 출력 값은 배포 기록에 저장되며 악의적인 사용자가 이를 검색할 수 있습니다.

[반복 루프](loops.md)와 함께 사용하는 경우 `input` 식이 리소스 속성에 할당되므로 에 대한 목록 함수를 사용할 수 있습니다. list 함수를 확인하기 전에 개수를 결정해야 하므로 `count`와 함께 사용할 수 없습니다.

조건부로 배포되는 리소스에서 **list** 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다. **list** 함수가 존재하지 않는 리소스를 참조하는 경우 오류가 발생합니다. [조건식 **?:** 연산자](./operators-logical.md#conditional-expression--)를 사용하여 리소스가 배포될 때만 함수가 평가되도록 합니다.

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

다른 list 함수는 다른 반환 형식을 갖습니다. 함수의 형식을 보려면 예제 Bicep 파일에 표시된 것처럼 출력 섹션에 포함합니다.

### <a name="list-example"></a>목록 예제

다음 예제에서는 스토리지 계정을 배포한 다음 해당 스토리지 계정에서 listKeys를 호출합니다. 키는 [배포 스크립트](../templates/deployment-script-template.md) 값을 설정할 때 사용됩니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'dscript${uniqueString(resourceGroup().id)}'
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource dScript 'Microsoft.Resources/deploymentScripts@2019-10-01-preview' = {
  name: 'scriptWithStorage'
  location: location
  ...
  properties: {
    azCliVersion: '2.0.80'
    storageAccountSettings: {
      storageAccountName: stg.name
      storageAccountKey: stg.listKeys().keys[0].value
    }
    ...
  }
}
```

다음 예제에서는 매개 변수를 사용하는 list 함수를 보여줍니다. 이 경우 함수는 **listAccountSas** 입니다. 만료 시간 동안 개체를 전달합니다. 만료 시간은 미래 시간이어야 합니다.

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: stg.listAccountSas('2021-04-01', accountSasProperties).accountSasToken
```

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
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
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
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/preview/queues-authorization-rules/list-keys) |
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
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

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

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

리소스 종류가 지역에 대한 영역을 지원하는지 여부를 결정합니다.

네임스페이스: [az](bicep-functions.md#namespaces-for-functions).

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

리소스 유형 또는 지역이 영역을 지원하지 않는 경우 빈 배열이 반환됩니다.

```json
[
]
```

### <a name="pickzones-example"></a>pickZones 예제

다음 Bicep 파일은 pickZones 함수 사용에 따른 세 가지 결과를 보여 줍니다.

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

이전 예제의 출력은 3개의 배열을 반환합니다.

| Name | Type | 값 |
| ---- | ---- | ----- |
| 지원됨 | array | [ "1" ] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

pickZones의 응답을 사용하여 영역에 대해 null을 제공하거나 다른 영역에 가상 머신을 할당할지 여부를 결정할 수 있습니다.

## <a name="providers"></a>providers

**providers 함수는 더 이상 사용되지 않습니다.** 더는 사용하지 않는 것이 좋습니다. 리소스 공급자의 API 버전을 가져오기 위해 이 함수를 사용한 경우 템플릿에 특정 API 버전을 제공하는 것이 좋습니다. 버전 간에 속성이 변경된 경우 동적으로 반환된 API 버전을 사용하면 템플릿이 손상될 수 있습니다.

네임스페이스: [az](bicep-functions.md#namespaces-for-functions).

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

리소스의 런타임 상태를 나타내는 개체를 반환합니다.

네임스페이스: [az](bicep-functions.md#namespaces-for-functions).

참조 함수는 Bicep 파일에서 사용할 수 있지만 일반적으로는 필요하지 않습니다. 대신 리소스에 대해 기호화된 이름을 사용합니다.

다음 예제에서는 저장소 계정을 배포합니다. 저장소 계정에 대해 기호화된 이름 `stg`을 사용하여 속성을 반환합니다.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

템플릿에 배포되지 않은 기존 리소스에서 속성을 가져오려면 `existing` 키워드를 사용합니다.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
output blobAddress string = stg.properties.primaryEndpoints.blob
```

자세한 내용은 [참조 리소스](./compare-template-syntax.md#reference-resources) 및 [JSON 템플릿 참조 함수](../templates/template-functions-resource.md#reference)를 참조하세요.


## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

리소스의 고유 식별자를 반환합니다.

네임스페이스: [az](bicep-functions.md#namespaces-for-functions).

resourceId 함수는 Bicep 파일에서 사용할 수 있지만 일반적으로는 필요하지 않습니다. 대신 리소스에 대한 기호 이름을 사용하고 `id` 속성에 액세스합니다.

리소스 이름이 모호하거나 동일한 Bicep 파일 내에서 프로비전되지 않은 경우 이 함수를 사용합니다. 반환된 식별자의 형식은 리소스 그룹, 구독, 관리 그룹 또는 테넌트 중에서 테넌트가 어떤 범위에서 발생하는지에 따라 달라집니다.

예를 들면 다음과 같습니다.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageID string = stg.id
```

Bicep 파일에 배포되지 않은 리소스에 대한 리소스 ID를 가져오려면 기존 키워드를 사용합니다.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
    name: storageAccountName
}

output storageID string = stg.id
```

자세한 내용은 [JSON 템플릿 resourceId 함수](../templates/template-functions-resource.md#resourceid)를 참조하세요.

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

구독 수준에서 배포된 리소스의 고유 식별자를 반환합니다.

네임스페이스: [az](bicep-functions.md#namespaces-for-functions).

subscriptionResourceId 함수는 Bicep 파일에서 사용할 수 있지만 일반적으로는 필요하지 않습니다. 대신 리소스에 대한 기호 이름을 사용하고 `id` 속성에 액세스합니다.

식별자는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>설명

이 함수를 사용하여 리소스 그룹이 아닌 [구독에 배포된](deploy-to-subscription.md) 리소스의 리소스 ID를 가져올 수 있습니다. 반환되는 ID는 리소스 그룹 값을 포함하지 않으므로 [resourceId](#resourceid) 함수에서 반환하는 값과 다릅니다.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID 예제

다음 Bicep 파일은 기본 제공 역할을 할당합니다. 리소스 그룹 또는 구독에 배포할 수 있습니다. subscriptionResourceId 함수를 사용하여 기본 제공 역할의 리소스 ID를 가져옵니다.

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: guid(resourceGroup().id, principalId, roleDefinitionId[builtInRoleType].id)
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

테넌트 수준에서 배포된 리소스의 고유 식별자를 반환합니다.

네임스페이스: [az](bicep-functions.md#namespaces-for-functions).

tenantResourceId 함수는 Bicep 파일에서 사용할 수 있지만 일반적으로는 필요하지 않습니다. 대신 리소스에 대한 기호 이름을 사용하고 `id` 속성에 액세스합니다.

식별자는 다음 형식으로 반환됩니다.

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

기본 제공 정책 정의는 테넌트 수준 리소스입니다. 기본 제공 정책 정의를 참조하는 정책 할당을 배포하려면 tenantResourceId 함수를 사용합니다.

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

## <a name="next-steps"></a>다음 단계

* 현재 배포에서 값을 가져오려면 [배포 값 함수](./bicep-functions-deployment.md)를 참조하세요.
* 리소스 형식을 만들 때 지정된 횟수를 반복하려면 [Bicep의 반복 루프를 참조하세요.](loops.md)
