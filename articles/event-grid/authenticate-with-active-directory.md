---
title: Azure Active Directory를 사용하여 Event Grid 게시 클라이언트 인증(미리 보기)
description: 이 문서에서는 Azure Active Directory를 사용하여 Azure Event Grid 게시 클라이언트를 인증하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5eaed36f6334904ad33f2618ec2977bf0136ac9e
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132811667"
---
# <a name="authentication-and-authorization-with-azure-active-directory-preview"></a>Azure Active Directory를 사용한 인증 및 권한 부여(미리 보기)
이 문서에서는 Azure AD(Azure Active Directory)를 사용하여 Azure Event Grid 게시 클라이언트를 인증하는 방법을 설명합니다.

## <a name="overview"></a>개요
[Microsoft ID](../active-directory/develop/v2-overview.md) 플랫폼은 ID 공급자로 Azure AD(Azure Active Directory)를 사용하는 리소스 및 애플리케이션에 대한 통합 인증 및 액세스 제어 관리를 제공합니다. Microsoft ID 플랫폼을 사용하여 애플리케이션에서 인증 및 권한 부여 지원을 제공합니다. 이 플랫폼은 OAuth 2.0 및 OpenID Connect와 같은 오픈 표준은 기준으로 하며, 다양한 인증 시나리오를 지원하는 도구 및 오픈 소스 라이브러리를 제공합니다. 다단계 인증이 필요한 정책을 설정하거나 특정 위치에서의 액세스를 허용하는 [조건부 액세스](../active-directory/conditional-access/overview.md)와 같은 고급 기능을 제공합니다.

Azure AD를 사용할 때 보안 상황을 개선하는 한 가지 이점은 인증 키와 같은 자격 증명을 코드 또는 리포지토리에 저장할 필요가 없다는 것입니다. 대신, 보호된 리소스에서 인증을 받을 때 애플리케이션이 제공하는 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 획득해야 합니다. Azure AD에 이벤트 게시 애플리케이션을 등록하고, 관리 및 사용하는 앱과 연결된 서비스 주체를 획득할 수 있습니다. 대신 ID 수명 주기의 일부 측면이 자동으로 관리되므로 보다 단순한 ID 관리 모델에 대해 시스템 할당 또는 사용자 할당 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용할 수 있습니다. 

[RBAC (역할 기반 액세스 제어](../active-directory/develop/custom-rbac-for-developers.md) )를 사용 하면 특정 보안 주체 (사용자, 그룹 또는 앱의 id)에 Azure 리소스에 대 한 작업을 실행할 수 있는 특정 권한이 있는 방식으로 권한 부여를 구성할 수 있습니다. 이러한 방식으로 이벤트를 Event Grid로 보내는 클라이언트 애플리케이션에서 사용하는 보안 주체에는 RBAC 역할 **Eventgrid 데이터 발신자** 가 연결되어 있어야 합니다. 

### <a name="security-principals"></a>보안 주체
Event Grid 게시 클라이언트의 인증을 설명할 때 적용할 수 있는 두 가지 광범위한 보안 주체 범주가 있습니다. 

- **관리 ID**. 관리 ID는 Azure 리소스에서 사용하도록 설정하는 시스템 할당 방식인 경우 해당 리소스에만 연결됩니다. 사용자 할당 방식인 경우 사용자가 명시적으로 만들고 이름을 지정합니다. 사용자 할당 관리 ID는 둘 이상의 리소스에만 연결될 수 있습니다.
- **애플리케이션 보안 주체**. Azure AD로 보호되는 리소스에 액세스하는 애플리케이션을 나타내는 보안 주체의 유형입니다. 

사용되는 보안 주체(관리 ID 또는 애플리케이션 보안 주체)에 관계 없이 클라이언트는 해당 ID를 사용하여 Azure AD보다 먼저 인증을 받고, Event Grid에 이벤트를 보낼 때 요청과 함께 전송되는 [OAuth 2.0 액세스 토큰](../active-directory/develop/access-tokens.md)을 가져옵니다. 해당 토큰은 암호화 방식으로 서명되고 Event Grid에서 토큰이 수신되면 유효성이 검사됩니다. 예를 들어, 대상 그룹(토큰의 의도된 수신자)은 다른 항목 중에 Event Grid(`https://eventgrid.azure.net`)로 확인됩니다. 토큰은 클라이언트 ID에 대한 정보를 포함합니다. Event Grid은 해당 ID를 사용하고 클라이언트에 **Eventgrid 데이터 발신자** 역할이 할당되어 있는지 확인합니다. 보다 정확하게 말하자면 Event Grid는 이벤트 게시 요청이 완료될 수 있도록 허용하기 전에 RBAC 역할의 ``Microsoft.EventGrid/events/send/action`` 권한이 ID에 연결되어 있는지 확인합니다. 
 
Event Grid SDK를 사용하는 경우 액세스 토큰의 획득을 구현하는 방법과 Event Grid에 대한 모든 요청에 포함하는 방법을 잘 몰라도 됩니다. [Event Grid 데이터 평면 SDK](#publish-events-using-event-grids-client-sdks)에서 이러한 작업을 수행하기 때문입니다. 

### <a name="client-configuration-steps-to-use-azure-ad-authentication"></a>Azure AD 인증을 사용하기 위한 클라이언트 구성 단계
다음 단계를 수행하여 토픽, 도메인 또는 파트너 네임스페이스에 이벤트를 보낼 때 Azure AD 인증을 사용하도록 클라이언트를 구성합니다.

1. 인증에 사용할 보안 주체를 만들거나 사용합니다. [관리 ID](#authenticate-using-a-managed-identity) 또는 [애플리케이션 보안 주체](#authenticate-using-a-security-principal-of-a-client-application)를 사용할 수 있습니다.
2. 보안 주체에 **Eventgrid 데이터 발신자** 역할을 할당하여 [이벤트를 게시할 보안 주체에 권한을 부여](#assign-permission-to-a-security-principal-to-publish-events)합니다.
3. Event Grid SDK를 사용하여 Event Grid에 이벤트를 게시합니다.

## <a name="authenticate-using-a-managed-identity"></a>관리 ID를 사용하여 인증

관리 ID는 Azure 리소스와 연결된 ID입니다. 관리 ID는 Azure AD 인증을 지원하는 Azure 리소스를 사용할 때 애플리케이션이 사용하는 ID를 제공합니다. 애플리케이션은 가상 머신 또는 Azure App Service와 같은 호스팅 리소스의 관리 ID를 사용하여 Event Grid에 이벤트를 게시할 때 요청과 함께 제공되는 Azure AD 토큰을 얻을 수 있습니다. 애플리케이션이 연결되면 Event Grid는 관리 엔터티의 컨텍스트를 클라이언트에 바인딩합니다. 관리 ID와 연결된 Event Grid 게시 클라이언트는 권한이 부여된 모든 작업을 수행할 수 있습니다. 관리형 엔터티를 Event Grid RBAC 역할에 연결하여 권한을 부여합니다.

관리 ID는 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 다른 인증 방법과 대조하면, ID 자체 또는 액세스해야 하는 리소스에 대해 애플리케이션 코드 또는 구성에서 액세스 키 또는 SAS(공유 액세스 서명)를 저장하고 보호할 필요가 없습니다.

관리 ID를 사용하여 이벤트 게시 클라이언트를 인증하려면 먼저 클라이언트 애플리케이션에 대한 호스팅 Azure 서비스를 결정한 다음, 해당 Azure 서비스 인스턴스에서 시스템 할당 또는 사용자 할당 관리 ID를 사용하도록 설정합니다. 예를 들어, [VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md), [Azure App Service 또는 Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet)에서 관리 ID를 사용하도록 설정할 수 있습니다. 

호스팅 서비스에서 관리 ID를 구성한 후에 [해당 ID에 이벤트를 게시할 수 있는 권한을 할당](#assign-permission-to-a-security-principal-to-publish-events)합니다.

## <a name="authenticate-using-a-security-principal-of-a-client-application"></a>클라이언트 애플리케이션의 보안 주체를 사용하여 인증

관리 ID 외의 다른 ID 옵션은 클라이언트 애플리케이션에 대한 보안 주체를 만드는 것입니다. 결과적으로 Azure AD에 애플리케이션을 등록해야 합니다. 애플리케이션을 등록하는 것은 ID 및 액세스 관리 제어를 Azure AD에 위임하는 동작입니다. [애플리케이션 등록](../active-directory/develop/quickstart-register-app.md#register-an-application) 및 [클라이언트 암호 추가](../active-directory/develop/quickstart-register-app.md#add-a-client-secret) 섹션의 단계를 따릅니다. 시작하기 전에 [필수 구성 요소](../active-directory/develop/quickstart-register-app.md#prerequisites)를 검토합니다.

애플리케이션 보안 주체가 있으며 위 단계를 수행했으면 [해당 ID에 이벤트를 게시할 수 있는 권한을 할당](#assign-permission-to-a-security-principal-to-publish-events)합니다.

> [!NOTE]
> 포털에 애플리케이션을 등록하는 경우 [애플리케이션 개체](../active-directory/develop/app-objects-and-service-principals.md#application-object) 및 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)가 홈 테넌트에 자동으로 만들어집니다. 또는 Microsoft Graph를 사용하여 애플리케이션을 등록할 수 있습니다. Microsoft Graph API를 사용하여 애플리케이션을 등록하거나 만드는 경우 별도의 단계를 통해 서비스 주체 개체를 만들 수 있습니다. 

## <a name="assign-permission-to-a-security-principal-to-publish-events"></a>이벤트를 게시하기 위해 보안 주체에 사용 권한 할당

Event Grid에 이벤트를 게시하는 데 사용되는 ID에는 Event Grid에 이벤트를 보낼 수 있도록 하는 ``Microsoft.EventGrid/events/send/action`` 권한이 있어야 합니다. 이 권한은 기본 제공 RBAC 역할 [ 데이터 발신자](../role-based-access-control/built-in-roles.md#eventgrid-data-sender)에 포함되어 있습니다. 이 역할은 관리 그룹, Azure 구독, 리소스 그룹 또는 특정 Event Grid 토픽, 도메인 또는 파트너 네임스페이스일 수 있는 지정된 [범위](../role-based-access-control/overview.md#scope)에 대한 [보안 주체](../role-based-access-control/overview.md#security-principal)에 할당될 수 있습니다. [Azure 역할 할당](../role-based-access-control/role-assignments-portal.md?tabs=current)의 단계에 따라 보안 주체에  **데이터 발신자** 역할을 할당하고, 이러한 방식으로 해당 보안 주체를 사용하는 애플리케이션에 이벤트를 보낼 수 있는 액세스 권한을 부여합니다. 또는 ``Microsoft.EventGrid/events/send/action`` 사용 권한을 포함하는 [사용자 지정 역할](../role-based-access-control/custom-roles.md)을 정의하고 해당 사용자 지정 역할을 보안 주체에 할당할 수 있습니다.

이제 RBAC 권한이 관리되므로 Event Grid에 [이벤트를 전송하는 클라이언트 애플리케이션을 빌드](#publish-events-using-event-grids-client-sdks)할 수 있습니다.

> [!NOTE]
> Event Grid는 이벤트를 전송하는 것 이상의 용도로 더 많은 RBAC 역할을 지원합니다. 자세한 내용은 [Event Grid 기본 제공 역할](security-authorization.md#built-in-roles)을 참조하세요.


## <a name="publish-events-using-event-grids-client-sdks"></a>Event Grid의 클라이언트 SDK를 사용하여 이벤트 게시

[Event Grid의 데이터 평면 SDK](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)를 사용하여 Event Grid에 이벤트를 게시합니다. Event Grid의 SDK는 Azure AD 인증을 포함하는 모든 인증 방법을 지원합니다. 

### <a name="prerequisites"></a>필수 구성 요소

Event Grid에서 인증을 받기 위한 필수 구성 요소는 다음과 같습니다.

- 애플리케이션에 SDK를 설치합니다.
   - [Java](/java/api/overview/azure/messaging-eventgrid-readme#include-the-package)
   - [.NET](/dotnet/api/overview/azure/messaging.eventgrid-readme-pre#install-the-package)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventgrid/eventgrid#install-the-azureeventgrid-package)
   - [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/eventgrid/azure-eventgrid#install-the-package)
- Azure ID 클라이언트 라이브러리를 설치합니다. Event Grid SDK는 인증을 위해 Azure ID 클라이언트 라이브러리를 사용합니다. 
   - [Java용 Azure ID 클라이언트 라이브러리](/java/api/overview/azure/identity-readme)
   - [.NET용 Azure ID 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme)
   - [JavaScript용 Azure ID 클라이언트 라이브러리](/javascript/api/overview/azure/identity-readme)
   - [Python용 Azure ID 클라이언트 라이브러리](/python/api/overview/azure/identity-readme)
- 애플리케이션에서 이벤트를 보낼 항목, 도메인 또는 파트너 네임스페이스입니다.

### <a name="publish-events-using-azure-ad-authentication"></a>Azure AD 인증을 사용하여 이벤트 게시

토픽, 도메인 또는 파트너 네임 스페이스로 이벤트를 보내려면 다음과 같은 방법으로 클라이언트를 빌드할 수 있습니다. 처음에 Azure AD 인증 지원을 제공했던 API 버전은 ``2021-06-01-preview``입니다. 애플리케이션에서 해당 API 버전 또는 최신 버전을 사용합니다.

```java 
        DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
        EventGridPublisherClient cloudEventClient = new EventGridPublisherClientBuilder()
                .endpoint("<your-event-grid-topic-domain-or-partner-namespace-endpoint>?api-version=2021-06-01-preview")
                .credential(credential)
                .buildCloudEventPublisherClient();
```
클라이언트 게시 애플리케이션에 연결된 보안 주체를 사용하는 경우 [Java SDK 추가 정보 문서](/java/api/overview/azure/identity-readme#environment-variables)에 표시된 대로 환경 변수를 구성해야 합니다. `DefaultCredentialBuilder`는 이러한 환경 변수를 읽어 올바른 ID를 사용합니다. 자세한 내용은 [Java API 개요](/java/api/overview/azure/identity-readme#defaultazurecredential)를 참조하세요.


자세한 내용은 다음 항목을 참조하세요.

- [Java용 Azure Event Grid 클라이언트 라이브러리](/java/api/overview/azure/messaging-eventgrid-readme)
- [.NET용 Azure Event Grid 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/eventgrid/Azure.Messaging.EventGrid#authenticate-using-azure-active-directory)
- [JavaScript용 Azure Event Grid 클라이언트 라이브러리](/javascript/api/overview/azure/eventgrid-readme)
- [Python용 Azure Event Grid 클라이언트 라이브러리](/python/api/overview/azure/eventgrid-readme)

## <a name="disable-key-and-shared-access-signature-authentication"></a>키 및 공유 액세스 서명 인증 사용 안 함

Azure AD 인증은 액세스 키 또는 SAS(공유 액세스 서명) 토큰 인증을 통해 제공되는 것보다 더 뛰어난 인증 지원을 제공합니다. Azure AD 인증을 사용하여 Azure AD ID 공급자를 기준으로 ID의 유효성이 검사됩니다. 개발자는 Azure AD 인증을 사용하는 경우 코드에서 키를 처리할 필요가 없습니다. 응용 프로그램의 보안 태세를 개선 하는 데 도움이 되는 Microsoft Id 플랫폼 (예: [조건부 액세스](../active-directory/conditional-access/overview.md))에 기본 제공 되는 모든 보안 기능을 활용할 수도 있습니다. 

Azure AD 인증을 사용하기로 결정하면 액세스 키 또는 SAS 토큰에 따라 인증을 사용하지 않도록 설정할 수 있습니다. 

> [!NOTE]
> 액세스 키 또는 SAS 토큰 인증은 **로컬 인증** 의 한 형태입니다. Azure AD에 의존하지 않는 이 인증 메커니즘 범주를 논의할 때 "로컬 인증"이라고 지칭하는 경우가 있습니다. 따라서 로컬 인증을 사용하지 않도록 설정하는 데 사용되는 API 매개 변수도 적절하게 ``disableLocalAuth``로 지칭됩니다.

### <a name="azure-portal"></a>Azure portal

새 토픽을 만들 때 토픽 **만들기** 페이지의 **고급** 탭에서 로컬 인증을 사용하지 않도록 설정할 수 있습니다. 

:::image type="content" source="./media/authenticate-with-active-directory/create-topic-disable-local-auth.png" alt-text="로컬 인증을 사용하지 않도록 설정할 수 있는 경우 토픽 만들기 페이지의 고급 탭을 보여주는 스크린샷":::

기존 항목의 경우 다음 단계에 따라 로컬 인증을 사용하지 않도록 설정합니다.

1. **토픽에** 대한 Event Grid 항목 페이지로 이동하고 **로컬 인증에서** **사용을** 선택합니다.

    :::image type="content" source="./media/authenticate-with-active-directory/existing-topic-local-auth.png" alt-text="기존 항목의 개요 페이지를 보여주는 스크린샷.":::
2. 로컬 **인증** 팝업 창에서 **사용 안 함** 을 선택하고 **확인을** 선택합니다.

    :::image type="content" source="./media/authenticate-with-active-directory/local-auth-popup.png" alt-text="로컬 인증 창을 보여주는 스크린샷.":::


### <a name="azure-cli"></a>Azure CLI
다음 CLI 명령은 로컬 인증을 사용하지 않도록 설정하여 사용자 지정 항목을 만드는 방법을 보여 줍니다. 로컬 인증 사용 안 함 기능은 현재 미리 보기로 제공되며 API 버전 ``2021-06-01-preview``를 사용해야 합니다.

```cli
az resource create --subscription <subscriptionId> --resource-group <resourceGroup> --resource-type Microsoft.EventGrid/topics --api-version 2021-06-01-preview --name <topicName> --location <location> --properties "{ \"disableLocalAuth\": true}"
```

다음은 만들거나 업데이트하는 토픽에 따라 사용할 수 있는 리소스 종류로, 참조를 위해 제공됩니다.

| 항목 유형        | 리소스 유형                        |
| ------------------| :------------------------------------|
| 도메인           | Microsoft.EventGrid/domains          |
| 파트너 네임스페이스 | Microsoft.EventGrid/partnerNamespaces|
| 사용자 지정 토픽      | Microsoft.EventGrid/topics           |

### <a name="azure-powershell"></a>Azure PowerShell

PowerShell을 사용하는 경우 다음 cmdlet을 사용하여 로컬 인증을 사용하지 않도록 설정한 상태로 사용자 지정 항목을 만듭니다. 

```PowerShell

Set-AzContext -SubscriptionId <SubscriptionId>

New-AzResource -ResourceGroupName <ResourceGroupName> -ResourceType Microsoft.EventGrid/topics -ApiVersion 2021-06-01-preview -ResourceName <TopicName> -Location <Location> -Properties @{disableLocalAuth=$true}
```

> [!NOTE]
> - 액세스 키 또는 공유 액세스 서명 인증을 사용하는 방법에 대한 자세한 내용은 [키 또는 SAS 토큰을 사용하여 게시 클라이언트 인증](security-authenticate-publishing-clients.md)을 참조하세요.
> - 이 문서에서는 Event Grid에 이벤트를 게시(이벤트 수신)할 때의 인증을 다룹니다. 이벤트를 전달(이벤트 송신)할 때 Event Grid에서 인증을 받는 내용은 [이벤트 처리기에 대한 이벤트 전달 인증](security-authentication.md) 문서에서 다룹니다. 

## <a name="resources"></a>리소스
- 데이터 평면 SDK
    - Java SDK: [github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid/src/samples/java/com/azure/messaging/eventgrid) | [이전 SDK 버전에서의 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventgrid/azure-messaging-eventgrid/migration-guide.md)
    - .NET SDK: [github](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/samples) | [이전 SDK 버전에서의 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/MigrationGuide.md)
    - Python SDK: [github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid/samples) | [이전 SDK 버전에서의 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventgrid/azure-eventgrid/migration_guide.md)
    - JavaScript SDK: [github](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/) | [샘플](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/samples) | [이전 SDK 버전에서의 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventgrid/eventgrid/MIGRATION.md)
- [Event Grid SDK 블로그](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)
- Azure ID 클라이언트 라이브러리
   - [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/identity/azure-identity/README.md)
   - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/identity/Azure.Identity/README.md)  
   - [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/identity/azure-identity/README.md)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md)
- [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 알아봅니다.
- [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../app-service/overview-managed-identity.md?tabs=dotnet)에 대해 알아봅니다.
- [애플리케이션 및 서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)에 대해 알아봅니다.
- [Microsoft ID 플랫폼에 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)에 대해 알아봅니다.
- [권한 부여](../role-based-access-control/overview.md)(RBAC 액세스 제어)의 작동 방식에 대해 알아봅니다.
- [EventGrid 데이터 발신자](../role-based-access-control/built-in-roles.md#eventgrid-data-sender) 역할을 포함하는 Event Grid 기본 제공 RBAC 역할에 대해 알아봅니다. [Event Grid 역할 목록](security-authorization.md#built-in-roles)
- ID에 [RBAC 역할 할당](../role-based-access-control/role-assignments-portal.md?tabs=current)에 대해 알아봅니다.
- [사용자 지정 RBAC 역할](../role-based-access-control/custom-roles.md) 정의 방법에 대해 알아봅니다.
- [Azure AD의 애플리케이션 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md)에 대해 알아봅니다.
- [Microsoft ID 플랫폼 액세스 토큰](../active-directory/develop/access-tokens.md)에 대해 알아봅니다.
- [OAuth 2.0 인증 코드 흐름 및 Microsoft ID 플랫폼](../active-directory/develop/v2-oauth2-auth-code-flow.md)에 대해 알아봅니다.
