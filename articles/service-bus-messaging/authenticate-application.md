---
title: Azure Service Bus 엔터티에 액세스하는 애플리케이션 인증
description: 이 문서에서는 Azure Service Bus 엔터티(큐, 토픽 등)에 액세스하기 위해 Azure Active Directory를 사용한 애플리케이션 인증에 대한 정보를 제공합니다.
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 8a28b13a8cde8c908d01d2f0eb2160ba7decb6f6
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112122609"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Azure Service Bus 엔터티에 액세스하기 위해 Azure Active Directory를 사용하여 애플리케이션 인증 및 권한 부여
Azure Service Bus에서는 Azure AD(Azure Active Directory)를 사용하여 Service Bus 엔터티(큐, 주제, 구독 또는 필터)에 대한 요청에 권한을 부여할 수 있습니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대한 자세한 내용은 [다양한 역할 이해](../role-based-access-control/overview.md)를 참조하세요.

## <a name="overview"></a>개요
보안 주체(사용자, 그룹 또는 애플리케이션)가 Service Bus 엔터티에 액세스하려고 하는 경우 요청에 권한이 부여되어야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다. 

 1. 먼저, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://servicebus.azure.net`입니다.
 1. 다음으로 토큰은 지정된 리소스에 대한 액세스 권한을 부여하기 위해 Service Bus 서비스에 요청의 일부로 전달됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰을 포함해야 합니다. 애플리케이션이 Azure VM, 가상 머신 확장 집합 또는 Azure Function 앱과 같은 Azure 엔터티 내에서 실행되는 경우 관리 ID를 사용하여 리소스에 액세스할 수 있습니다. Service Bus 서비스에 대한 관리 ID의 요청을 인증하는 방법에 대한 자세한 내용은 [Azure Active Directory 및 Azure 리소스 관리 ID를 사용하여 Azure Service Bus 리소스에 대한 액세스 권한 부여](service-bus-managed-service-identity.md)를 참조하세요. 

권한 부여 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. Azure Service Bus는 Service Bus 리소스에 대한 권한 집합을 포함하는 Azure 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가지는 권한이 결정됩니다. Azure Service Bus에 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Service Bus에 대한 Azure 기본 제공 역할](#azure-built-in-roles-for-azure-service-bus)을 참조하세요. 

Service Bus에 요청하는 네이티브 애플리케이션 및 웹 애플리케이션도 Azure AD를 사용하여 권한을 부여할 수 있습니다. 이 문서에서는 액세스 토큰을 요청하고 이를 사용하여 Service Bus 리소스에 대한 요청에 권한을 부여하는 방법을 보여 줍니다. 


## <a name="assigning-azure-roles-for-access-rights"></a>액세스 권한에 대한 Azure 역할 할당
Azure AD(Azure Active Directory)에서는 [Azure RBAC](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Service Bus는 Service Bus 엔터티에 액세스하는 데 사용되는 일반 권한 집합이 포함된 Azure 기본 제공 역할 집합을 정의하며 데이터에 액세스하기 위한 사용자 지정 역할도 정의할 수 있습니다.

Azure AD 보안 주체에 Azure 역할을 할당하면 Azure는 해당 보안 주체에 대한 리소스에 액세스 권한을 부여합니다. 액세스 범위는 구독, 리소스 그룹 또는 Service Bus 네임스페이스 수준에서 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 주체, 또는 [Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure Service Bus에 대한 Azure 기본 제공 역할
Azure Service Bus의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 Azure RBAC 모델을 사용하여 보호되고 있습니다. Azure는 Service Bus 네임스페이스에 대한 액세스 권한을 부여하기 위해 아래와 같은 Azure 기본 제공 역할을 제공합니다.

- [Azure Service Bus 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Service Bus 네임스페이스 및 해당 엔터티(큐, 토픽, 구독 및 필터)에 대한 데이터 액세스를 사용하도록 설정합니다.
- [Azure Service Bus 데이터 보내는 사람](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 전송 액세스 권한을 부여합니다.
- [Azure Service Bus 데이터 받는 사람](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 수신 액세스 권한을 부여합니다. 

## <a name="resource-scope"></a>리소스 범위 
Azure 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 Service Bus 리소스에 대한 액세스 범위를 가장 좁은 범위에서 시작하여 지정할 수 있는 수준을 설명합니다.

- **큐**, **토픽** 또는 **구독**: 역할 할당은 특정 Service Bus 엔터티에 적용됩니다. 현재 Azure Portal은 구독 수준에서 사용자/그룹/관리 ID를 Service Bus Azure 역할에 할당하는 것을 지원하지 않습니다. 
- **Service Bus 네임스페이스**: 역할 할당이 네임스페이스 아래에 있는 Service Bus의 전체 토폴로지 및 이와 관련된 소비자 그룹에 적용됩니다.
- **리소스 그룹**: 역할 할당이 리소스 그룹 아래의 모든 Service Bus 리소스에 적용됩니다.
- **구독**: 역할 할당이 구독의 모든 리소스 그룹에 있는 모든 Service Bus 리소스에 적용됩니다.

> [!NOTE]
> Azure 역할 할당을 전파하는 데 최대 5분이 걸릴 수 있음에 유의하세요. 

기본 제공 역할이 정의되는 방법에 대한 자세한 내용은 [역할 정의 이해](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하세요. Azure 사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당  
원하는 범위(Service Bus 네임스페이스, 리소스 그룹, 구독)에서 애플리케이션의 서비스 주체에 [Service Bus 역할](#azure-built-in-roles-for-azure-service-bus) 중 하나를 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

역할 및 해당 범위를 정의하면 [GitHub의 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)을 사용하여 이 동작을 테스트할 수 있습니다.

## <a name="authenticate-from-an-application"></a>애플리케이션에서 인증
Service Bus에서 Azure AD를 사용하는 주요 이점은 사용자의 자격 증명을 코드에 저장할 필요가 없다는 점입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹, 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 애플리케이션에 액세스 토큰을 반환하고, 애플리케이션은 액세스 토큰을 사용하여 Azure Service Bus에 대한 요청 권한을 부여할 수 있습니다.

다음 섹션에서는 Microsoft ID 플랫폼 2.0 인증을 위해 네이티브 애플리케이션 또는 웹 애플리케이션을 구성하는 방법을 보여 줍니다. Microsoft ID 플랫폼 2.0에 대한 자세한 내용은 [Microsoft ID 플랫폼(v2.0) 개요](../active-directory/develop/v2-overview.md)를 참조하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용하여 Service Bus 엔터티에 권한을 부여하는 첫 번째 단계는 [Azure Portal](https://portal.azure.com/)에서 Azure AD 테넌트에 클라이언트 애플리케이션을 등록하는 것입니다. 클라이언트 애플리케이션을 등록할 때 애플리케이션에 대한 정보를 Azure AD에 제공합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(애플리케이션 ID라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

다음 이미지는 웹 애플리케이션을 등록하는 단계를 보여 줍니다.

![애플리케이션 등록](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 애플리케이션을 네이티브 애플리케이션으로 등록하는 경우 리디렉션 URI에 유효한 URI를 지정할 수 있습니다. 네이티브 애플리케이션의 경우 이 값은 실제 URL일 필요가 없습니다. 웹 애플리케이션의 경우 리디렉션 URI는 토큰이 제공되는 URL을 지정하므로 유효한 URI여야 합니다.

애플리케이션을 등록한 후에는 **설정** 아래에 **애플리케이션(클라이언트) ID가** 표시됩니다.

![등록된 애플리케이션의 애플리케이션 ID입니다.](./media/authenticate-application/application-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/quickstart-register-app.md)을 참조하세요.

> [!IMPORTANT]
> **TenantId** 와 **ApplicationId** 를 적어 둡니다. 애플리케이션을 실행하려면 이러한 값이 필요합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 클라이언트 암호를 가져오려면 다음 단계를 수행합니다.

1. 아직 해당 페이지로 이동하지 않은 경우 Azure Portal에서 앱 등록으로 이동합니다.
1. 왼쪽 메뉴에서 **인증서 및 암호** 를 선택합니다.
1. **클라이언트 암호** 에서 **새 클라이언트 암호** 를 선택하여 새 암호를 만듭니다.

    ![새 클라이언트 암호 - 단추](./media/authenticate-application/new-client-secret-button.png)
1. 암호에 대한 설명을 제공하고 원하는 만료 기간을 선택한 다음, **추가** 를 선택합니다.

    ![클라이언트 암호 추가 페이지](./media/authenticate-application/add-client-secret-page.png)
1. 새 암호의 값을 안전한 위치에 즉시 복사합니다. 전체 값은 한 번만 표시됩니다.

    ![클라이언트 암호](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Service Bus API에 대한 사용 권한
사용 중인 애플리케이션은 콘솔 애플리케이션이므로 네이티브 애플리케이션을 등록하고 **Microsoft.ServiceBus** 에 대한 API 사용 권한을 **필요한 권한** 집합에 추가해야 합니다. 네이티브 애플리케이션은 ID 역할을 하는 Azure AD의 **리디렉션 URI** 가 필요합니다. URI가 네트워크 대상일 필요는 없습니다. 이 예제의 경우 샘플 코드가 이미 해당 URI를 사용하므로 `https://servicebus.microsoft.com`을 사용합니다.

### <a name="authenticating-the-service-bus-client"></a>Service Bus 클라이언트 인증   
애플리케이션을 등록하고 Azure Service Bus에서 데이터를 보내고 받을 수 있는 권한을 부여한 후에는 클라이언트 암호 자격 증명을 사용하여 클라이언트를 인증할 수 있습니다. 그러면 Azure Service Bus에 대한 요청을 수행할 수 있습니다.

토큰 획득이 지원되는 시나리오 목록은 [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 리포지토리의 [시나리오](https://aka.ms/msal-net-scenarios) 섹션을 참조하세요.

<!-- TAB -- # [.NET](#tab/dotnet) -  -->

최신 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) 라이브러리를 사용하는 경우 [Azure.Identity](https://www.nuget.org/packages/Azure.Identity) 라이브러리에 정의된 [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential)을 통해 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)를 인증할 수 있습니다.

```cs
TokenCredential credential = new ClientSecretCredential("<tenant_id>", "<client_id>", "<client_secret>");
var client = new ServiceBusClient("<fully_qualified_namespace>", credential);
```

이전 .NET 패키지를 사용하는 경우에는 다음 샘플을 참조하세요.
- [Microsoft.Azure.ServiceBus의 RoleBasedAccessControl](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/RoleBasedAccessControl)
- [WindowsAzure.ServiceBus의 RoleBasedAccessControl](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)

<!-- CLOSE TAB --- -->

## <a name="next-steps"></a>다음 단계
- Azure RBAC에 대한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 할당하는 방법 및 Azure 역할 할당을 관리하는 방법을 알아보려면 다음 문서를 참조하세요.
    - [Azure PowerShell을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-template.md)

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Service Bus Azure RBAC 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
- [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
