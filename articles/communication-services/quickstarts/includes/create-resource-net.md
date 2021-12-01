---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 75483d9dafeba0b0e3c55ef38d7e4d63b01d7d1e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133406005"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet/).
- 운영 체제에 대한 최신 버전의 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core).
- [.NET ID SDK](/dotnet/api/azure.identity)의 최신 버전을 가져옵니다.
- [.NET 관리 SDK](../../concepts/sdk-options.md)의 최신 버전을 가져옵니다.

전화번호를 사용하려는 경우에는 평가판 계정을 사용할 수 없습니다. 리소스를 만들기 전에 전화번호를 구매하려는 경우 구독이 모든 [요구 사항](../../concepts/telephony/plan-solution.md)을 충족하는지 확인합니다. 

## <a name="installing-the-sdk"></a>SDK 설치

먼저 C# 프로젝트에 Communication Services 관리 SDK를 포함합니다.

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>구독 ID

Azure 구독의 ID를 알고 있어야 합니다. 이는 포털에서 가져올 수 있습니다.

1.  Azure 계정에 로그인
2.  왼쪽 사이드바에서 구독 선택
3.  필요한 구독 선택
4.  개요 클릭
5.  구독 ID 선택

이 빠른 시작에서는 `AZURE_SUBSCRIPTION_ID`라는 환경 변수에 구독 ID를 저장했다고 가정합니다.

## <a name="authentication"></a>인증

Azure Communication Services와 통신하려면 먼저 Azure에 인증해야 합니다. 일반적으로 이는 서비스 사용자 ID를 사용하여 수행합니다.

### <a name="option-1-managed-identity"></a>옵션 1: 관리 ID

코드가 Azure에서 서비스로 실행되는 경우 가장 쉬운 인증 방법은 Azure에서 관리 ID를 획득하는 것입니다. [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

[관리 ID를 지원하는 Azure 서비스](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[사용자가 할당한 관리 ID](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

사용자가 만든 관리 ID의 ClientId는 `ManagedIdentityCredential`에 명시적으로 전달되어야 합니다.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>옵션 2: 서비스 주체

관리 ID를 사용하는 대신 사용자가 직접 관리하는 서비스 사용자를 사용하여 Azure에 인증할 수 있습니다. [Azure Active Directory에서 서비스 사용자를 만들고 관리](../../../active-directory/develop/howto-create-service-principal-portal.md)하는 방법에 대한 설명서를 통해 자세히 알아보세요.

서비스 사용자를 만든 후에는 Azure Portal에서 해당 서비스 사용자에 대한 다음 정보를 수집해야 합니다.

- **클라이언트 ID**
- **클라이언트 암호**
- **테넌트 ID**

이러한 값을 각각 `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` 및 `AZURE_TENANT_ID`라는 환경 변수에 저장합니다. 그런 다음, Communication Services 관리 클라이언트를 다음과 같이 만들 수 있습니다.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>옵션 3: 사용자 ID

서비스 ID를 사용하지 않고 대화형 사용자를 대신하여 Azure를 호출하려면 다음 코드를 사용하여 Azure Communication Services Management 클라이언트를 만들 수 있습니다. 그러면 사용자에게 MSA 또는 Azure AD 자격 증명을 입력하라는 메시지를 표시하는 브라우저 창이 열립니다.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Communication Services 리소스 만들기

### <a name="interacting-with-azure-resources"></a>Azure 리소스와 상호 작용

이제 인증되었으므로 관리 클라이언트를 사용하여 API 호출을 수행할 수 있습니다.

다음 각 예에서는 기존 리소스 그룹에 Communication Services 리소스를 할당합니다.

리소스 그룹을 만들어야 하는 경우 [Azure Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) 또는 [Azure Resource Manager SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md)를 사용하여 리소스 그룹을 만들 수 있습니다.

### <a name="create-and-manage-a-communication-services-resource"></a>Communication Services 리소스 만들기 및 관리

Communication Services Management SDK 클라이언트(``Azure.ResourceManager.Communication.CommunicationManagementClient``)의 인스턴스를 사용하여 Communication Services 리소스에 대한 작업을 수행할 수 있습니다.

#### <a name="create-a-communication-services-resource"></a>Communication Services 리소스 만들기

Communication Services 리소스를 만들 때 리소스 그룹 이름 및 리소스 이름을 지정합니다. `Location` 속성은 항상 `global`이며, 공개 미리 보기 동안에는 `DataLocation` 값이 `UnitedStates`여야 합니다.

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Communication Services 리소스 업데이트

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Communication Services 리소스 모두 나열

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Communication Services 리소스 삭제

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>키 및 연결 문자열 관리

각 Communication Services 리소스에는 액세스 키와 해당 연결 문자열이 한 쌍 있습니다. 이러한 키는 관리 SDK를 사용하여 액세스할 수 있으며, 다른 Communication Services SDK에서 Azure Communication Services에 인증하는 데 사용될 수 있습니다.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Communication Services 리소스의 액세스 키 가져오기

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Communication Services 리소스의 액세스 키 다시 생성

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```
