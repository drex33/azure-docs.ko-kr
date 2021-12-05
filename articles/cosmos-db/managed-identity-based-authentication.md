---
title: 시스템 할당 관리 ID를 사용하여 Azure Cosmos DB 데이터에 액세스하는 방법
description: Azure AD(Azure Active Directory) 시스템 할당 관리 ID(관리 서비스 ID)를 구성하여 Azure Cosmos DB에서 키에 액세스하는 방법을 알아봅니다.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/02/2021
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7685a2206bc601bcbfaa274c3ddb5730ad504b7e
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570139"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>시스템 할당 관리 ID를 사용하여 Azure Cosmos DB 데이터에 액세스
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!TIP]
> 이제 Azure Cosmos DB에서 [데이터 평면 RBAC(역할 기반 액세스 제어)](how-to-setup-rbac.md)를 사용할 수 있으므로 Azure Active Directory를 통해 요청을 원활하게 승인할 수 있습니다.

이 문서에서는 [관리 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 사용하여 Azure Cosmos DB 키에 액세스할 수 있도록 *강력한 키 순환과 무관한* 솔루션을 설정합니다. 이 문서의 예에서는 Azure Functions를 사용하지만 관리 ID를 지원하는 모든 서비스를 사용할 수 있습니다. 

Azure Cosmos DB 키를 복사할 필요 없이 Azure Cosmos DB 데이터에 액세스할 수 있는 함수 앱을 만드는 방법을 알아봅니다. 함수 앱은 1분마다 실행되어 수족관 어항의 현재 온도를 기록합니다. 타이머로 트리거되는 함수 앱을 설정하는 방법을 알아보려면 [타이머로 트리거되는 Azure에서 함수 만들기](../azure-functions/functions-create-scheduled-function.md) 문서를 참조하세요.

시나리오를 단순화하기 위해 이전 온도 문서를 정리하도록 [수명](./time-to-live.md) 설정이 이미 구성되어 있습니다.

> [!IMPORTANT]
> 이 접근 방식은 Azure Cosmos DB 컨트롤 플레인을 통해 계정의 기본 키를 가져오기 때문에 계정에 [읽기 전용 잠금이 적용](../azure-resource-manager/management/lock-resources.md)된 경우에는 작동하지 않습니다. 이 경우에는 대신 Azure Cosmos DB [데이터 평면 RBAC](how-to-setup-rbac.md)를 사용하는 것이 좋습니다.

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>함수 앱에 시스템 할당 관리 ID 할당

이 단계에서는 시스템 할당 관리되는 ID를 함수 앱에 할당합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure 함수** 창을 열고 함수 앱으로 이동합니다. 

1. **플랫폼 기능** > **ID** 탭을 엽니다. 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="함수 앱의 플랫폼 기능 및 ID 옵션을 보여 주는 스크린샷":::

1. **ID** 탭에서 시스템 ID **상태** 를 **설정** 으로 바꾸고 **저장** 을 선택합니다. **ID** 창은 다음과 같아야 합니다.  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="시스템 ID 상태가 설정으로 지정된 스크린샷":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Azure Cosmos 계정에 대한 액세스 권한 부여

이 단계에서는 함수 앱의 시스템 할당 관리 ID에 역할을 할당합니다. Azure Cosmos DB에는 관리 ID에 할당할 수 있는 여러 기본 제공 역할이 있습니다. 이 솔루션에는 다음 두 가지 역할을 사용합니다.

|기본 제공 역할  |Description  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB 계정을 관리할 수 있습니다. 읽기/쓰기 키를 검색할 수 있습니다. |
|[Cosmos DB 계정 독자 역할](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB 계정 데이터를 읽을 수 있음. 읽기 키를 검색할 수 있습니다. |

> [!TIP] 
> 역할을 할당하는 경우 필요한 액세스만 할당합니다. 서비스에 데이터 읽기만 필요한 경우 관리 ID에 **Cosmos DB 계정 리더** 역할을 할당합니다. 최소 권한 액세스의 중요성에 대한 자세한 내용은 [권한 있는 계정 노출 감소](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) 문서를 참조하세요.

이 시나리오에서 함수 앱은 수족관의 온도를 읽은 다음 해당 데이터를 Azure Cosmos DB의 컨테이너에 쓰기 저장합니다. 함수 앱은 데이터를 작성해야 하므로 **DocumentDB 계정 기여자** 역할을 할당해야 합니다. 

### <a name="assign-the-role-using-azure-portal"></a>Azure Portal을 사용하여 역할 할당

1. Azure Portal에 로그인하고 Azure Cosmos DB 계정으로 이동합니다. **액세스 제어(IAM)** 창을 열고 **역할 할당** 탭을 엽니다.

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="액세스 제어 창과 역할 할당 탭을 보여 주는 스크린샷":::

1. **추가** > **역할 할당 추가** 를 선택합니다.

1. **역할 할당 추가** 패널이 오른쪽에 열립니다.

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="역할 할당 추가 창을 보여 주는 스크린샷":::

   * **역할**: **DocumentDB 계정 기여자** 를 선택합니다.
   * **액세스 할당**: **시스템 할당 관리 ID 선택** 하위 섹션에서 **함수 앱** 을 선택합니다.
   * **선택**: 창이 **관리 시스템 ID** 가 있는 구독의 모든 함수 앱으로 채워집니다. 이 경우 **FishTankTemperatureService** 함수 앱을 선택합니다. 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="예로 채워진 역할 할당 추가 창을 보여 주는 스크린샷":::

1. 함수 앱을 선택한 후 **저장** 을 선택합니다.

### <a name="assign-the-role-using-azure-cli"></a>Azure CLI를 사용하여 역할 할당

Azure CLI를 사용하여 역할을 할당하려면 Azure Cloud Shell을 열고 다음 명령을 실행합니다.

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>프로그래밍 방식으로 Azure Cosmos DB 키에 액세스

이제 Azure Cosmos DB 권한에 **DocumentDB 계정 기여자** 역할이 있는 시스템 할당 관리 ID가 있는 함수 앱이 있습니다. 다음 함수 앱 코드는 Azure Cosmos DB 키를 가져오고 CosmosClient 개체를 만들고 수족관의 온도를 가져온 다음 이를 Azure Cosmos DB에 저장합니다.

이 샘플은 [목록 키 API](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/database-accounts/list-keys)를 사용하여 Azure Cosmos DB 계정 키에 액세스합니다.

> [!IMPORTANT] 
> [Cosmos DB 계정 리더 역할을 할당](#grant-access-to-your-azure-cosmos-account)하려면 [목록 읽기 전용 키 API](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/database-accounts/list-read-only-keys)를 사용해야 합니다. 이렇게 하면 읽기 전용 키만 채워집니다.

목록 키 API는 `DatabaseAccountListKeysResult` 개체를 반환합니다. 이 유형은 C# 라이브러리에 정의되어 있지 않습니다. 다음 코드에서는 이 클래스의 구현을 보여 줍니다.  

```csharp 
namespace Monitor 
{
    public class DatabaseAccountListKeysResult
    {
        public string primaryMasterKey { get; set; }
        public string primaryReadonlyMasterKey { get; set; }
        public string secondaryMasterKey { get; set; }
        public string secondaryReadonlyMasterKey { get; set; }
    }
}
```

이 예에서는 다음과 같이 정의된 "TemperatureRecord" 라는 간단한 문서도 사용합니다.

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }
    }
}
```

[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 라이브러리를 사용하여 시스템 할당 관리 ID 토큰을 가져옵니다. 토큰을 얻는 다른 방법을 배우고 `Microsoft.Azure.Service.AppAuthentication` 라이브러리에 대한 자세한 내용을 찾으려면 [서비스 간 인증](/dotnet/api/overview/azure/service-to-service-authentication) 문서를 참조하세요.


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        // HttpClient is intended to be instantiated once, rather than per-use.
        static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Add the access token to request headers.
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadFromJsonAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

이제 [함수 앱을 배포](../azure-functions/create-first-function-vs-code-csharp.md)할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 및 Azure Active Directory를 사용한 인증서 기반 인증](certificate-based-authentication.md)
* [Azure Key Vault를 사용하여 Azure Cosmos DB 키 보호](access-secrets-from-keyvault.md)
* [Azure Cosmos DB의 보안 기준](security-baseline.md)
