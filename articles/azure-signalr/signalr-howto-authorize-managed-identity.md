---
title: 관리 id에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여
description: 이 문서에서는 관리 되는 id에서 Azure AD로 리소스를 SignalR 하는 권한 부여 요청에 대 한 정보를 제공
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 1d332fd5d69b088717501771ccc03d8d24126478
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483110"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-managed-identities"></a>관리 id에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여
azure SignalR 서비스 [는 azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md)에서 요청을 승인 하는 Azure Active Directory (azure AD)를 지원 합니다.

이 문서에서는 관리 되는 id에서 SignalR 리소스에 대 한 요청에 권한을 부여 하도록 SignalR 리소스 및 코드를 구성 하는 방법을 보여 줍니다.

## <a name="configure-managed-identities"></a>관리되는 ID 구성

첫 번째 단계는 관리 되는 id를 구성 하는 것입니다.

`System-assigned managed identity`Azure Portal를 사용 하 여에를 구성 하는 예제입니다 `Virtual Machine` .

1. [Azure Portal](https://portal.azure.com/)를 열고 가상 컴퓨터를 검색 하 여 선택 합니다.
1. **설정** 섹션에서 **id** 를 선택 합니다.
1. **시스템 할당 됨** 탭에서 **상태** 를 **켜기** 로 전환 합니다.
   ![응용 프로그램의 스크린샷](./media/authenticate/identity-virtual-machine.png)
1. **저장** 단추를 클릭 하 여 변경 내용을 확인 합니다.


사용자 할당 관리 id를 만드는 방법에 대해 알아보려면 다음 문서를 참조 하세요.
- [사용자 할당 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

관리 id를 구성 하는 방법에 대해 자세히 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK를 사용하여 Azure 리소스에 대한 관리 ID로 VM 구성](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-app-service-and-azure-functions"></a>App service 및 Azure Functions

[App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../app-service/overview-managed-identity.md)을 참조하세요.

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal에 역할 할당 추가

이 샘플에서는 `SignalR App Server` SignalR 리소스를 통해 시스템 할당 id에 역할을 할당 하는 방법을 보여 줍니다. 

> [!Note]
> 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯 한 모든 범위에 역할을 할당할 수 있습니다. 범위에 대해 자세히 알아보려면 [AZURE RBAC의 범위 이해](../role-based-access-control/scope-overview.md) 를 참조 하세요.

1. [Azure Portal](https://portal.azure.com/)를 열고 SignalR 리소스로 이동 합니다.

1. **Access Control (IAM)** 을 클릭 하 여 Azure SignalR에 대 한 액세스 제어 설정을 표시 합니다.

   다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷에서는 SignalR 리소스에 대 한 액세스 제어 (IAM) 페이지의 예를 보여 줍니다.

   ![액세스 제어 스크린샷](./media/authenticate/access-control.png)

1. **추가 > 역할 할당 추가** 를 클릭 합니다.

1. **역할** 탭에서을 선택 `SignalR App Server` 합니다.

1. **다음** 을 클릭합니다.

   ![역할 할당 추가의 스크린샷](./media/authenticate/add-role-assignment.png)

1. **구성원** 탭의 **액세스 할당** 섹션에서 **관리 id** 를 선택 합니다.

1. **멤버 선택** 을 클릭 합니다.

1. **관리 Id 선택** 창에서 **시스템 할당 관리 id > 가상 컴퓨터** 를 선택 합니다.

1. 역할을 할당할 가상 컴퓨터를 검색 하 고 선택 합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

2. **다음** 을 클릭합니다.

   ![관리 id에 역할을 할당 하는 스크린샷](./media/authenticate/assign-role-to-managed-identities.png)

3. **검토 + 할당** 을 클릭 하 여 변경 내용을 확인 합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.

Azure 역할 할당을 할당 하 고 관리 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-app"></a>앱 구성

### <a name="app-server"></a>앱 서버

#### <a name="using-system-assigned-identity"></a>시스템 할당 id 사용

[DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) 또는 [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) 중 하나를 사용 하 여 SignalR 끝점을 구성할 수 있습니다.


그러나를 직접 사용 하는 것이 가장 좋습니다 `ManagedIdentityCredential` .

기본적으로 시스템 할당 관리 id가 사용 되지만를 사용 하는 경우 환경 [자격 증명이](/dotnet/api/azure.identity.environmentcredential) 유지 되는 **환경 변수를 구성 하지 않는지 확인** 해야 합니다 `DefaultAzureCredential` . 그렇지 않으면를 사용 하 여 `EnvironmentCredential` 요청을 수행 하 고 `Unauthorized` 대부분의 경우 응답이 반환 됩니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential()),
    };
});
```

#### <a name="using-user-assigned-identity"></a>사용자 할당 id 사용

`ClientId`개체를 만드는 동안를 제공 `ManagedIdentityCredential` 합니다.

> [!IMPORTANT]
> 둘 다 GUID 인 경우에도 개체 (보안 주체) ID가 아닌 **클라이언트 id** 를 사용 합니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        var clientId = "<your identity client id>";
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential(clientId)),
    };
```

### <a name="azure-functions-signalr-bindings"></a>Azure Functions SignalR 바인딩

> [!WARNING]
> SignalR 트리거 바인딩은 id 기반 연결을 지원 하지 않으며 여전히 연결 문자열이 필요 합니다.

Azure Functions SignalR 바인딩은 포털 또는 로컬에서 [응용 프로그램 설정을](/azure/azure-functions/functions-how-to-use-azure-function-app-settings) 사용 하 여 [`local.settings.json`](/azure/azure-functions/functions-develop-local#local-settings-file) SignalR 리소스에 액세스 하도록 관리 id를 구성 합니다.

Id를 구성 하려면 키-값 쌍의 그룹이 필요할 수 있습니다. 모든 키-값 쌍의 키는 **연결 이름 접두사** (기본값 `AzureSignalRConnectionString` )와 구분 기호 ( `__` 포털 및 로컬)로 시작 해야 합니다 `:` . 바인딩 속성을 사용 하 여 접두사를 사용자 지정할 수 있습니다 [`ConnectionStringSetting`](/azure/azure-functions/functions-bindings-signalr-service) .

#### <a name="using-system-assigned-identity"></a>시스템 할당 id 사용

서비스 URI만 구성 하는 경우 `DefaultAzureCredential` 이 사용 됩니다. Azure 및 로컬 개발 환경에서 동일한 구성을 공유 하려는 경우에 유용 합니다. 가 작동 하는 방법을 알아보려면 `DefaultAzureCredential` [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)를 참조 하세요.

Azure Portal에서를 구성 하기 위해 다음과 같이 설정 `DefaultAzureCredential` 합니다. [여기에 나열 된 환경 변수](/dotnet/api/overview/azure/identity-readme#environment-variables)를 구성 하지 않으면 시스템 할당 id가 인증에 사용 됩니다.
```
<CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
```

다음은 파일에 있는의 구성 샘플입니다 `DefaultAzureCredential` `local.settings.json` . 로컬에는 관리 id가 없으며 Visual Studio, Azure CLI 및 Azure PowerShell 계정을 통해 인증을 순서 대로 시도 합니다.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net"
  }
}
```

시스템 할당 id를 독립적으로 사용 하 고 [다른 환경 변수의](/dotnet/api/overview/azure/identity-readme#environment-variables)영향을 받지 않으려면 `credential` 연결 이름 접두사를 사용 하 여 키를로 설정 해야 합니다 `managedidentity` . 응용 프로그램 설정 샘플은 다음과 같습니다.

```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
```

#### <a name="using-user-assigned-identity"></a>사용자 할당 id 사용

사용자 할당 id를 사용 하려면 `clientId` 시스템 할당 id와 비교 하 여 연결 이름 접두사를 사용 하 여 키를 하나 이상 할당 해야 합니다. 응용 프로그램 설정 샘플은 다음과 같습니다.
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
<CONNECTION_NAME_PREFIX>__clientId = <CLIENT_ID>
```
## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [SignalR 용 Azure AD 개요](signalr-concept-authorize-azure-active-directory.md)
- [Azure 응용 프로그램에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여](signalr-howto-authorize-application.md)
