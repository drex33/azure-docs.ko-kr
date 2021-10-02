---
title: Service Bus를 통해 Azure 리소스에 관리 ID 사용
description: 이 문서에서는 관리 ID를 사용하여 Azure Service Bus 엔터티(큐, 토픽 및 구독)에 액세스하는 방법을 설명합니다.
ms.topic: article
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: dad610902514849db33ca00b2da3f328d233260e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358055"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Azure Active Directory로 관리 ID를 인증하여 Azure Service Bus 리소스 액세스
[Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 애플리케이션 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음 애플리케이션에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 애플리케이션 내에서 실행되거나 Azure 리소스용 관리 ID를 지원하도록 설정된 가상 머신에서 실행되는 Service Bus 클라이언트 앱은 SAS 규칙과 키 또는 기타 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Service Bus 메시지 네임스페이스의 엔드포인트 주소만 있으면 됩니다. 앱이 연결되면 Service Bus는 이 문서 뒷부분의 예제에 나와 있는 작업에서 관리 ID의 컨텍스트를 클라이언트에 바인딩합니다. 관리 ID와 연결된 Service Bus 클라이언트는 권한이 부여된 모든 작업을 수행할 수 있습니다. 관리 ID를 Service Bus 역할에 연결하는 방식을 통해 권한이 부여됩니다. 

## <a name="overview"></a>개요
보안 주체(사용자, 그룹 또는 애플리케이션)가 Service Bus 엔터티에 액세스하려고 하는 경우 요청에 권한이 부여되어야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다. 

 1. 먼저, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://servicebus.azure.net`입니다.
 1. 다음으로 토큰은 지정된 리소스에 대한 액세스 권한을 부여하기 위해 Service Bus 서비스에 요청의 일부로 전달됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰을 포함해야 합니다. 애플리케이션이 Azure VM, 가상 머신 확장 집합 또는 Azure Function 앱과 같은 Azure 엔터티 내에서 실행되는 경우 관리 ID를 사용하여 리소스에 액세스할 수 있습니다. 

권한 부여 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. Azure Service Bus는 Service Bus 리소스에 대한 권한 집합을 포함하는 Azure 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가지는 권한이 결정됩니다. Azure Service Bus에 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Service Bus에 대한 Azure 기본 제공 역할](#azure-built-in-roles-for-azure-service-bus)을 참조하세요. 

Service Bus에 요청하는 네이티브 애플리케이션 및 웹 애플리케이션도 Azure AD를 사용하여 권한을 부여할 수 있습니다. 이 문서에서는 액세스 토큰을 요청하고 이를 사용하여 Service Bus 리소스에 대한 요청에 권한을 부여하는 방법을 보여 줍니다. 


## <a name="assigning-azure-roles-for-access-rights"></a>액세스 권한에 대한 Azure 역할 할당
Azure AD(Azure Active Directory)는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Service Bus는 Service Bus 엔터티에 액세스하는 데 사용되는 일반 권한 집합이 포함된 Azure 기본 제공 역할 집합을 정의하며 데이터에 액세스하기 위한 사용자 지정 역할도 정의할 수 있습니다.

Azure AD 보안 주체에 Azure 역할을 할당하면 Azure는 해당 보안 주체에 대한 리소스에 액세스 권한을 부여합니다. 액세스 범위는 구독, 리소스 그룹 또는 Service Bus 네임스페이스 수준에서 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 주체, 또는 Azure 리소스의 관리 ID일 수 있습니다.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure Service Bus에 대한 Azure 기본 제공 역할
Azure Service Bus의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 Azure RBAC 모델을 사용하여 보호되고 있습니다. Azure는 Service Bus 네임스페이스에 대한 액세스 권한을 부여하기 위해 아래와 같은 Azure 기본 제공 역할을 제공합니다.

- [Azure Service Bus 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Service Bus 네임스페이스 및 해당 엔터티(큐, 토픽, 구독 및 필터)에 대한 데이터 액세스를 사용하도록 설정합니다.
- [Azure Service Bus 데이터 보내는 사람](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 전송 액세스 권한을 부여합니다.
- [Azure Service Bus 데이터 받는 사람](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): 이 역할을 사용하여 Service Bus 네임스페이스 및 해당 엔터티에 대한 수신 액세스 권한을 부여합니다. 

## <a name="resource-scope"></a>리소스 범위 
Azure 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 Service Bus 리소스에 대한 액세스 범위를 가장 좁은 범위에서 시작하여 지정할 수 있는 수준을 설명합니다.

- **큐**, **토픽** 또는 **구독**: 역할 할당은 특정 Service Bus 엔터티에 적용됩니다. 현재 Azure Portal은 구독 수준에서 사용자/그룹/관리 ID를 Service Bus Azure 역할에 할당하는 것을 지원하지 않습니다. 다음은 Azure CLI 명령을 사용하는 예입니다. [az-role-assignment-create](/cli/azure/role/assignment?#az_role_assignment_create)를 사용하여 Service Bus Azure 역할에 ID를 할당합니다. 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus 네임스페이스**: 역할 할당이 네임스페이스 아래에 있는 Service Bus의 전체 토폴로지 및 이와 관련된 소비자 그룹에 적용됩니다.
- **리소스 그룹**: 역할 할당이 리소스 그룹 아래의 모든 Service Bus 리소스에 적용됩니다.
- **구독**: 역할 할당이 구독의 모든 리소스 그룹에 있는 모든 Service Bus 리소스에 적용됩니다.

> [!NOTE]
> Azure 역할 할당을 전파하는 데 최대 5분이 걸릴 수 있음에 유의하세요. 

기본 제공 역할이 정의되는 방법에 대한 자세한 내용은 [역할 정의 이해](../role-based-access-control/role-definitions.md#control-and-data-actions)를 참조하세요. Azure 사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용
Azure 리소스에 대한 관리 ID를 사용하여 VM에서 Service Bus 리소스에 대한 권한을 부여하려면 먼저 VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리 ID에 대한 권한 부여
애플리케이션의 관리 ID에서 Service Bus에 대한 요청에 권한을 부여하려면 먼저 관리 ID에 대한 Azure RBAC(Azure 역할 기반 액세스 제어) 설정을 구성합니다. Azure Service Bus는 Service Bus에서 전송 및 읽기 권한을 포함하는 Azure 역할을 정의합니다. Azure 역할이 관리 ID에 할당되면 적절한 범위에서 Service Bus 엔터티에 대한 액세스 권한이 관리 ID에 부여됩니다.

Azure 역할 할당에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Service Bus 리소스에 대한 액세스 인증 및 권한 부여](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)를 참조하세요.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus를 통해 Azure 리소스에 관리 ID 사용
Service Bus에서 관리 ID를 사용하려면 ID에 역할 및 적절한 범위를 할당해야 합니다. 이 섹션의 프로시저에서는 관리 ID에서 실행되는 간단한 애플리케이션을 사용하고 Service Bus 리소스에 액세스합니다.

여기서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에서 호스팅되는 샘플 웹 애플리케이션을 사용합니다. 웹 애플리케이션을 만드는 방법에 대한 단계별 지침은 [Azure에서 ASP.NET Core 웹앱 만들기](../app-service/quickstart-dotnetcore.md)를 참조하세요.

애플리케이션이 생성되면 다음 단계를 수행합니다. 

1. **설정** 으로 이동하고 **ID** 를 선택합니다. 
1. **상태** 를 **켜짐** 으로 선택합니다. 
1. **저장** 을 선택하여 설정을 저장합니다. 

    ![웹앱의 관리 ID](./media/service-bus-managed-service-identity/identity-web-app.png)

이 설정을 사용하도록 설정하면 새 서비스 ID가 Azure AD(Azure Active Directory)에서 생성되고 App Service 호스트로 구성됩니다.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할을 할당하려면
원하는 범위(Service Bus 네임스페이스, 리소스 그룹, 구독)에서 관리 서비스 ID에 [Service Bus 역할](#azure-built-in-roles-for-azure-service-bus) 중 하나를 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요. 

> [!NOTE]
> 관리 ID를 지원하는 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.

### <a name="run-the-app"></a>앱 실행
이제 만든 ASP.NET 애플리케이션의 기본 페이지를 수정합니다. [이 GitHub 리포지토리](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)의 웹 애플리케이션 코드를 사용할 수 있습니다.  

Default.aspx 페이지가 방문 페이지입니다. 코드는 Default.aspx.cs 파일에서 찾을 수 있습니다. 그 결과 몇 가지 입력 필드와 메시지를 보내거나 받기 위해 Service Bus에 연결되는 **전송** 및 **수신** 단추가 있는 최소 웹 애플리케이션이 생성됩니다.

TokenCredential을 사용하는 생성자를 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체가 초기화되는 방법을 참고합니다. DefaultAzureCredential은 TokenCredential에서 파생되며 여기에서 전달할 수 있습니다. 이와 같이 유지하고 사용하는 데 특별한 비결은 없습니다. Service Bus 및 권한 부여 핸드셰이크에 대한 관리 ID 컨텍스트의 흐름은 토큰 자격 증명에 의해 자동으로 처리합니다. 이 모델은 SAS를 사용하는 방식보다 단순합니다.

이와 같이 변경한 후에는 애플리케이션을 게시하고 실행합니다. Visual Studio에서 게시 프로필을 다운로드한 다음 가져오면 올바른 게시 데이터를 쉽게 가져올 수 있습니다.

![게시 프로필 가져오기](./media/service-bus-managed-service-identity/msi3.png)
 
메시지를 보내거나 받으려면 만든 엔터티의 이름과 네임스페이스의 이름을 입력합니다. 그런 후에 **보내기** 나 **받기** 를 클릭합니다.


> [!NOTE]
> - 관리 ID는 Azure 환경, App 서비스, Azure VM 및 확장 집합 내에서만 작동합니다. .NET 애플리케이션의 경우 Service Bus NuGet 패키지에서 사용되는 Microsoft.Azure.Services.AppAuthentication 라이브러리는 이 프로토콜에 대한 추상화를 제공하고 로컬 개발 환경을 지원합니다. 또한 이 라이브러리를 통해 개발 머신에서 Visual Studio, Azure CLI 2.0 또는 Active Directory 통합 인증의 사용자 계정을 사용하여 코드를 로컬로 테스트할 수 있습니다. 이 라이브러리를 통한 로컬 개발 옵션에 대한 자세한 내용은 [.NET을 사용하여 Azure Key Vault에 대한 서비스 간 인증](/dotnet/api/overview/azure/service-to-service-authentication)을 참조하세요.  

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
