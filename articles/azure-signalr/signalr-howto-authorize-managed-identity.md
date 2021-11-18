---
title: 관리 ID에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한 부여
description: 이 문서에서는 관리 ID에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한을 부여하는 방법을 제공합니다.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: b65dc12c3854a1730f74bf679a724e651985883b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716909"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-managed-identities"></a>관리 ID에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한 부여
Azure SignalR Service Azure [리소스에 대한 관리 ID의](../active-directory/managed-identities-azure-resources/overview.md)요청에 권한을 부여하는 Azure AD(Azure Active Directory)를 지원합니다.

이 문서에서는 관리 ID에서 SignalR 리소스에 대한 요청에 권한을 부여하도록 SignalR 리소스 및 코드를 구성하는 방법을 보여 줍니다.

## <a name="configure-managed-identities"></a>관리되는 ID 구성

첫 번째 단계는 관리 ID를 구성하는 것입니다.

이는 Azure Portal 사용하여 에서 를 구성하는 `System-assigned managed identity` `Virtual Machine` 예제입니다.

1. [Azure Portal](https://portal.azure.com/)열고 Virtual Machine을 검색하여 선택합니다.
1. **설정** 섹션에서 **ID를** 선택합니다.
1. 시스템 **할당** 탭에서 **상태를** **켜기로 전환합니다.**
   ![애플리케이션의 스크린샷](./media/authenticate/identity-virtual-machine.png)
1. **저장** 단추를 클릭하여 변경 내용을 확인합니다.


사용자 할당 관리 ID를 만드는 방법을 알아보려면 다음 문서를 참조하세요.
- [사용자 할당 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

관리 ID 구성에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

- [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK를 사용하여 Azure 리소스에 대한 관리 ID로 VM 구성](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-app-service-and-azure-functions"></a>App Service 및 Azure Functions

[App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../app-service/overview-managed-identity.md)을 참조하세요.

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal 역할 할당 추가

이 샘플에서는 `SignalR App Server` SignalR 리소스를 통해 시스템 할당 ID에 역할을 할당하는 방법을 보여줍니다. 

> [!Note]
> 역할은 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯한 모든 범위에 할당할 수 있습니다. 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해를 참조하세요.](../role-based-access-control/scope-overview.md)

1. [Azure Portal](https://portal.azure.com/)열고 SignalR 리소스로 이동합니다.

1. **Access Control(IAM)를** 클릭하여 Azure SignalR에 대한 액세스 제어 설정을 표시합니다.

   다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷은 SignalR 리소스에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

   ![액세스 제어 스크린샷](./media/authenticate/access-control.png)

1. **추가 > 역할 할당 추가를** 클릭합니다.

1. **역할** 탭에서 를 `SignalR App Server` 선택합니다.

1. **다음** 을 클릭합니다.

   ![역할 할당 추가 스크린샷](./media/authenticate/add-role-assignment.png)

1. **멤버** 탭의 **액세스 권한 할당** 섹션에서 관리 **ID** 를 선택합니다.

1. **멤버 선택을** 클릭합니다.

1. 관리 ID 선택 창에서 **가상 머신에 > 시스템 할당 관리 ID를** **선택합니다.**

1. 역할을 할당할 가상 머신을 검색하여 선택합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

2. **다음** 을 클릭합니다.

   ![관리 ID에 역할을 할당하는 스크린샷](./media/authenticate/assign-role-to-managed-identities.png)

3. **검토 + 할당을** 클릭하여 변경 내용을 확인합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.

Azure 역할 할당을 할당하고 관리하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-app"></a>앱 구성

### <a name="app-server"></a>App Server

#### <a name="using-system-assigned-identity"></a>시스템 할당 ID 사용

[DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) 또는 [ManagedIdentityCredential을](/dotnet/api/azure.identity.managedidentitycredential) 사용하여 SignalR 엔드포인트를 구성할 수 있습니다.


그러나 가장 좋은 방법은 를 직접 사용하는 `ManagedIdentityCredential` 것입니다.

시스템 할당 관리 ID는 기본적으로 사용되지만 를 사용하는 경우 [EnvironmentCredential이](/dotnet/api/azure.identity.environmentcredential) 유지한 **환경 변수를 구성하지 않았는지 확인하세요.** `DefaultAzureCredential` 그렇지 않으면 를 사용하여 `EnvironmentCredential` 요청을 만들고 `Unauthorized` 대부분의 경우 응답이 발생합니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential()),
    };
});
```

#### <a name="using-user-assigned-identity"></a>사용자 할당 ID 사용

`ClientId`개체를 만드는 동안 를 `ManagedIdentityCredential` 제공합니다.

> [!IMPORTANT]
> 둘 다 GUID인 경우에도 개체(보안 주체) ID가 아닌 **클라이언트** ID를 사용합니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        var clientId = "<your identity client id>";
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential(clientId)),
    };
```

### <a name="azure-functions-signalr-bindings"></a>SignalR 바인딩 Azure Functions

> [!WARNING]
> SignalR 트리거 바인딩은 아직 ID 기반 연결을 지원하지 않으며 연결 문자열이 여전히 필요합니다.

Azure Functions SignalR 바인딩은 포털 또는 로컬에서 [애플리케이션 설정을](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 사용하여 [`local.settings.json`](../azure-functions/functions-develop-local.md#local-settings-file) SignalR 리소스에 액세스하도록 관리 ID를 구성합니다.

ID를 구성하려면 키-값 쌍 그룹이 필요할 수 있습니다. 모든 키-값 쌍의 키는 연결 **이름 접두사(기본값:** ) 및 구분 `AzureSignalRConnectionString` `__` 기호(포털 및 `:` 로컬)로 시작해야 합니다. 접두사 바인딩 속성으로 사용자 지정할 수 있습니다 [`ConnectionStringSetting`](../azure-functions/functions-bindings-signalr-service.md) 합니다.

#### <a name="using-system-assigned-identity"></a>시스템 할당 ID 사용

서비스 URI만 구성하는 경우 `DefaultAzureCredential` 가 사용됩니다. 이 방법은 Azure 및 로컬 개발 환경에서 동일한 구성을 공유하려는 경우에 유용합니다. 작동 방식을 알아보려면 `DefaultAzureCredential` [DefaultAzureCredential 을 참조하세요.](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)

Azure Portal 다음과 같이 를 설정하여 를 `DefaultAzureCredential` 구성합니다. [여기에 나열된 환경 변수를](/dotnet/api/overview/azure/identity-readme#environment-variables)구성하지 않는 경우 시스템 할당 ID를 사용하여 인증합니다.
```
<CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
```

파일의 구성 샘플은 다음과 `DefaultAzureCredential` `local.settings.json` 같습니다. 로컬에는 관리 ID가 없으며 Visual Studio 통한 인증, Azure CLI 및 Azure PowerShell 계정이 순서대로 시도됩니다.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net"
  }
}
```

시스템 할당 ID를 [다른 환경 변수의](/dotnet/api/overview/azure/identity-readme#environment-variables)영향 없이 독립적으로 사용하려면 연결 이름 접두사가 인 키를 로 설정해야 `credential` `managedidentity` 합니다. 애플리케이션 설정 샘플은 다음과 같습니다.

```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
```

#### <a name="using-user-assigned-identity"></a>사용자 할당 ID 사용

사용자 할당 ID를 사용하려면 시스템 할당 ID에 비해 연결 이름 접두사로 키를 하나 더 `clientId` 할당해야 합니다. 애플리케이션 설정 샘플은 다음과 같습니다.
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
<CONNECTION_NAME_PREFIX>__clientId = <CLIENT_ID>
```
## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [SignalR용 Azure AD 개요](signalr-concept-authorize-azure-active-directory.md)
- [Azure 애플리케이션에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한 부여](signalr-howto-authorize-application.md)