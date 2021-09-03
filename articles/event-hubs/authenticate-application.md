---
title: Azure Event Hubs 리소스에 액세스하는 애플리케이션 인증
description: 이 문서에서는 Azure Event Hubs 리소스에 액세스하는 Azure Active Directory를 사용하는 애플리케이션 인증에 대한 정보를 제공합니다.
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: f87866ece2699a457e00a4afba6855933118cf19
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123093"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Event Hubs 리소스에 액세스하기 위해 Azure Active Directory를 사용하여 애플리케이션 인증
Microsoft Azure는 Azure AD(Active Directory)를 기반으로 하는 리소스 및 애플리케이션에 대해 통합된 액세스 제어 관리 기능을 제공합니다. Azure Event Hubs에서 Azure AD를 사용하는 경우의 주요 이점은 자격 증명을 코드에 더 이상 저장할 필요가 없다는 것입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청하는 리소스 이름은 `https://eventhubs.azure.net/`이며 모든 클라우드/테넌트에 동일합니다(Kafka 클라이언트의 경우 토큰 요청에 대한 리소스는 `https://<namespace>.servicebus.windows.net`입니다). Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD에서 액세스 토큰을 애플리케이션에 반환하고, 애플리케이션은 이 액세스 토큰을 사용하여 Azure Event Hubs 리소스를 요청할 수 있는 권한을 부여할 수 있습니다.

Azure AD 보안 주체에 역할을 할당하는 경우 Azure는 해당 보안 주체에 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스의 범위는 구독, 리소스 그룹, Event Hubs 네임스페이스 또는 그 아래에 있는 리소스의 수준으로 지정할 수 있습니다. Azure AD 보안은 사용자, 그룹, 애플리케이션 서비스 사용자 또는 [Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당할 수 있습니다. 

> [!NOTE]
> 역할 정의는 사용 권한 컬렉션입니다. Azure RBAC(Azure 역할 기반 액세스 제어)는 역할 할당을 통해 사용 권한을 적용하는 방법을 제어합니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다. 자세한 내용은 [다른 역할 이해](../role-based-access-control/overview.md)를 참조하세요.

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure Event Hubs에 대한 기본 제공 역할
Azure는 Azure AD 및 OAuth를 사용하여 Event Hubs 데이터에 대한 액세스 권한을 부여할 수 있도록 다음과 같은 Azure 기본 역할을 제공합니다.

- [Azure Event Hubs 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): 이 역할을 사용하여 Event Hubs 리소스에 대한 전체 액세스 권한을 제공합니다.
- [Azure Event Hubs 데이터 보내는 사람](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): 이 역할을 사용하여 Event Hubs 리소스에 대한 보내기 액세스 권한을 제공합니다.
- [Azure Event Hubs 데이터 받는 사람](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): 이 역할을 사용하여 Event Hubs 리소스에 대한 받기 액세스 권한을 제공합니다.   

스키마 레지스트리 기본 제공 역할은 [스키마 레지스트리 역할](schema-registry-overview.md#azure-role-based-access-control)을 참조하세요.

> [!IMPORTANT]
> 미리 보기 릴리스는 소유자 또는 참가자 역할에 대한 Event Hubs 데이터 액세스 권한 추가를 지원했습니다. 그러나 소유자 및 참여자 역할의 데이터 액세스 권한은 더 이상 부여되지 않습니다. 소유자 또는 참가자 역할을 사용하는 경우 Azure Event Hubs 데이터 소유자 역할을 사용하도록 전환합니다.


## <a name="authenticate-from-an-application"></a>애플리케이션에서 인증
Event Hubs로 Azure AD를 사용하는 주요 이점은 사용자의 자격 증명을 코드에 저장할 필요가 없다는 점입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹, 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 애플리케이션에 액세스 토큰을 반환하고, 애플리케이션은 액세스 토큰을 사용하여 Azure Event Hubs에 대한 요청 권한을 부여할 수 있습니다.

다음 섹션에서는 Microsoft ID 플랫폼 2.0 인증을 위해 네이티브 애플리케이션 또는 웹 애플리케이션을 구성하는 방법을 보여 줍니다. Microsoft ID 플랫폼 2.0에 대한 자세한 내용은 [Microsoft ID 플랫폼(v2.0) 개요](../active-directory/develop/v2-overview.md)를 참조하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용하여 Event Hubs 리소스에 권한을 부여하는 첫 번째 단계는 [Azure Portal](https://portal.azure.com/)에서 Azure AD 테넌트에 클라이언트 애플리케이션을 등록하는 것입니다. 클라이언트 애플리케이션을 등록할 때 애플리케이션에 대한 정보를 Azure AD에 제공합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(애플리케이션 ID라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

다음 이미지는 웹 애플리케이션을 등록하는 단계를 보여 줍니다.

![애플리케이션 등록](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 애플리케이션을 네이티브 애플리케이션으로 등록하는 경우 리디렉션 URI에 유효한 URI를 지정할 수 있습니다. 네이티브 애플리케이션의 경우 이 값은 실제 URL일 필요가 없습니다. 웹 애플리케이션의 경우 리디렉션 URI는 토큰이 제공되는 URL을 지정하므로 유효한 URI여야 합니다.

애플리케이션을 등록한 후에는 **설정** 아래에 **애플리케이션(클라이언트) ID가** 표시됩니다.

![등록된 애플리케이션의 애플리케이션 ID입니다.](./media/authenticate-application/application-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/quickstart-register-app.md)을 참조하세요.


### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 클라이언트 암호를 추가하려면 다음 단계를 수행합니다.

1. Azure Portal에서 앱 서비스로 이동합니다.
1. **인증서 및 암호** 설정을 선택합니다.
1. **클라이언트 암호** 에서 **새 클라이언트 암호** 를 선택하여 새 암호를 만듭니다.
1. 암호에 대한 설명을 제공하고 원하는 만료 기간을 선택합니다.
1. 새 암호의 값을 안전한 위치에 즉시 복사합니다. 전체 값은 한 번만 표시됩니다.

    ![클라이언트 암호](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당  
원하는 범위(Event Hubs 네임스페이스, 리소스 그룹, 구독)에서 애플리케이션의 서비스 주체에 [Event Hubs 역할](#built-in-roles-for-azure-event-hubs) 중 하나를 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

역할 및 해당 범위를 정의하면 [이 GitHub 위치에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 샘플을 사용하여 이 동작을 테스트할 수 있습니다. Azure RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 액세스를 관리하는 방법에 대해 자세히 알려면 [이 문서](..//role-based-access-control/role-assignments-portal.md)를 참조하세요. 


### <a name="client-libraries-for-token-acquisition"></a>토큰 획득을 위한 클라이언트 라이브러리  
애플리케이션을 등록하고 이 애플리케이션에 Azure Event Hubs에서 데이터를 보내고 받을 수 있는 권한을 부여하면 보안 주체를 인증하고 OAuth 2.0 토큰을 획득하는 코드를 애플리케이션에 추가할 수 있습니다. 토큰을 인증하고 얻으려면 [Microsoft ID 플랫폼 인증 라이브러리](../active-directory/develop/reference-v2-libraries.md)나 OpenID 또는 Connect 1.0을 지원하는 다른 오픈 소스 라이브러리 중 하나를 사용할 수 있습니다. 그러면 애플리케이션에서는 액세스 토큰을 사용하여 Azure Event Hubs에 대한 요청에 권한을 부여할 수 있습니다.

토큰 획득이 지원되는 시나리오 목록은 [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 리포지토리의 [시나리오](https://aka.ms/msal-net-scenarios) 섹션을 참조하세요.

## <a name="samples"></a>샘플
- [Microsoft.Azure.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    이러한 샘플은 이전 **Microsoft.Azure.EventHubs** 라이브러리를 사용하지만 최신 **Azure.Messaging.EventHubs** 라이브러리를 사용하여 쉽게 업데이트할 수 있습니다. 이전 라이브러리를 사용하는 샘플을 새 라이브러리로 이동하려면 [Microsoft.Azure.EventHubs에서 Azure.Messaging.EventHubs로 마이그레이션하기 위한 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)를 참조하세요.
- [Azure.Messaging.EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    이 샘플은 최신 **Azure.Messaging.EventHubs** 라이브러리를 사용하도록 업데이트되었습니다.

## <a name="next-steps"></a>다음 단계
- Azure RBAC에 대한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 할당하는 방법 및 Azure 역할 할당을 관리하는 방법을 알아보려면 다음 문서를 참조하세요.
    - [Azure PowerShell을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 추가](../role-based-access-control/role-assignments-template.md)

다음 관련 문서를 참조하세요.
- [Azure Active Directory를 사용하여 관리 ID를 인증하여 Event Hubs 리소스에 액세스](authenticate-managed-identity.md)
- [공유 액세스 서명을 사용하여 Azure Event Hubs에 대한 요청 인증](authenticate-shared-access-signature.md)
- [Azure Active Directory를 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)
- [공유 액세스 서명을 사용하여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)
