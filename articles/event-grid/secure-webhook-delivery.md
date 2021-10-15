---
title: Azure Event Grid에서 Azure AD를 사용하여 보안 웹후크 배달
description: Azure Event Grid를 사용하여 Azure Active Directory가 보호하는 HTTPS 엔드포인트에 이벤트를 전달하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 18db7a5244cb498ff54999646082d3d5628468e1
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066910"
---
# <a name="deliver-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory 보호된 엔드포인트에 이벤트 전달
이 문서에서는 Azure AD(Azure Active Directory)를 사용하여 **이벤트 구독** 과 **웹후크 엔드포인트** 간의 연결을 보호하는 방법을 설명합니다. Azure AD 애플리케이션 및 서비스 주체에 대한 개요는 [Microsoft ID 플랫폼(v2.0) 개요](../active-directory/develop/v2-overview.md)를 참조하세요.

이 문서에서는 데모를 위해 Azure Portal을 사용하지만 CLI, PowerShell 또는 SDK를 사용하여 이 기능을 사용하도록 설정할 수도 있습니다.

> [!IMPORTANT]
> 보안 취약성을 해결하기 위해 2021년 3월 30일에 이벤트 구독을 만들거나 업데이트하는 과정의 일부로 추가 액세스 검사가 도입되었습니다. 구독자 클라이언트의 서비스 주체는 소유자이거나 대상 애플리케이션 서비스 주체에 할당된 역할이 있어야 합니다. 아래 새 지침에 따라 AAD 애플리케이션을 다시 구성하세요.

## <a name="deliver-events-to-a-webhook-in-the-same-azure-ad-tenant"></a>동일한 Azure AD 테넌트에서 Webhook에 이벤트 전달

![Azure Event Grid에서 Azure AD를 사용하여 보안 웹후크 배달](./media/secure-webhook-delivery/single-tenant-diagram.png)

위의 다이어그램에 따라 다음 단계에 따라 테넌트 구성을 수행합니다.

### <a name="configure-the-event-subscription-by-using-azure-ad-user"></a>Azure AD 사용자를 사용하여 이벤트 구독 구성

1. Microsoft 디렉터리(단일 테넌트)와 함께 작동하도록 구성된 웹후크에 대한 Azure AD 애플리케이션을 만듭니다.

2. 테넌트에서 [Azure Shell을](https://portal.azure.com/#cloudshell/) 열고 PowerShell 환경을 선택합니다.

3. 테넌트  $webhookAadTenantId 값을 수정합니다.

    - Variables:
        - **$webhookAadTenantId:** Azure 테넌트 ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

4. 다음 [스크립트를](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-user.md) 열고 **$webhookAppObjectId** 값을 업데이트하고 **식별자를 사용하여 $eventSubscriptionWriterUserPrincipalName** 스크립트를 계속 실행합니다.

    - Variables:
        - **$webhookAppObjectId:** 웹후크에 대해 만든 Azure AD 애플리케이션 ID
        - **$eventSubscriptionWriterUserPrincipalName:** 이벤트 구독을 만들 사용자의 Azure 사용자 보안 주체 이름

    > [!NOTE]
    > **$eventGridAppId** 값을 수정할 필요가 없습니다. 이 스크립트의 경우 **AzureEventGridSecureWebhookSubscriber를** **$eventGridRoleName** 값으로 설정합니다. 이 스크립트를 실행하려면 [Azure AD 애플리케이션 관리자 역할의](../active-directory/roles/permissions-reference.md#all-roles) 멤버여야 합니다.

5. 포털에서 이벤트 구독을 만들 때 다음 단계를 수행합니다.

    1. 엔드포인트 유형을 **웹후크** 로 선택합니다.
    2. 엔드포인트 **URI** 를 지정합니다.
    
        ![엔드포인트 유형 웹후크를 선택합니다.](./media/secure-webhook-delivery/select-webhook.png)
    3. **이벤트 구독 만들기** 페이지의 위쪽에서 **추가 기능** 탭을 선택합니다.
    4. **추가 기능** 탭에서 다음 단계를 수행합니다.
        1. **AAD 인증 사용** 을 선택하고 테넌트 ID 및 애플리케이션 ID를 구성합니다.
        2. 스크립트의 출력에서 Azure AD 테넌트 ID를 복사하고 **AAD 테넌트 ID** 필드에 입력합니다.
        3. 스크립트의 출력에서 Azure AD 애플리케이션 ID를 복사하고 **AAD 애플리케이션 ID** 필드에 입력합니다. 애플리케이션 ID를 사용하는 대신 AAD 애플리케이션 ID URI를 사용할 수 있습니다. 애플리케이션 ID URI에 대한 자세한 내용은 [이 문서](../app-service/configure-authentication-provider-aad.md)를 참조하세요.
    
            ![보안 웹후크 작업](./media/secure-webhook-delivery/aad-configuration.png)

### <a name="configure-the-event-subscription-by-using-azure-ad-application"></a>Azure AD 애플리케이션을 사용하여 이벤트 구독 구성

1. Microsoft 디렉터리(단일 테넌트)와 작동하도록 구성된 Event Grid 구독 작성기용 Azure AD 애플리케이션을 만듭니다.

2. 이전에 만든 Azure AD 애플리케이션에 대한 비밀을 만들고 값을 저장합니다(나중에 이 값이 필요함).

3. Event Grid 항목의 액세스 제어(IAM)로 이동하여 Event Grid 구독 기록기의 역할 할당을 Event Grid 기여자로 추가합니다. 이 단계를 통해 Azure CLI 사용하여 Azure AD 애플리케이션으로 Azure에 로그인할 때 Event Grid 리소스에 액세스할 수 있습니다.

4. Microsoft 디렉터리(단일 테넌트)와 함께 작동하도록 구성된 웹후크에 대한 Azure AD 애플리케이션을 만듭니다.

5. 테넌트에서 [Azure Shell을](https://portal.azure.com/#cloudshell/) 열고 PowerShell 환경을 선택합니다.

6. 테넌트  $webhookAadTenantId 값을 수정합니다.

    - Variables:
        - **$webhookAadTenantId:** Azure 테넌트 ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. 다음 [스크립트를](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md) 열고 **$webhookAppObjectId** 값을 업데이트하고 **식별자를 사용하여 $eventSubscriptionWriterAppId** 스크립트를 계속 실행합니다.

    - Variables:
        - **$webhookAppObjectId:** 웹후크에 대해 만든 Azure AD 애플리케이션 ID
        - **$eventSubscriptionWriterAppId:** Event Grid 구독 기록기용 Azure AD 애플리케이션 ID

    > [!NOTE]
    > 값을 수정할 필요가 **```$eventGridAppId```** 없습니다. 이 스크립트의 경우 **AzureEventGridSecureWebhookSubscriber를** 에 대한 값으로 **```$eventGridRoleName```** 설정합니다. 이 스크립트를 실행하려면 [Azure AD 애플리케이션 관리자 역할의](../active-directory/roles/permissions-reference.md#all-roles) 멤버여야 합니다.

8. 명령을 실행하여 Event Grid 구독 기록기 Azure AD 애플리케이션으로 로그인합니다.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_ID] -p [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. 명령을 실행하여 구독을 만듭니다.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > 이 시나리오에서는 Event Grid 시스템 항목을 사용하고 있습니다. Azure CLI 사용하여 사용자 지정 토픽 또는 Event Grid 도메인에 대한 구독을 만들려면 [여기를](/cli/azure/eventgrid)참조하세요.

10. 모든 항목이 올바르게 구성된 경우 Event Grid 항목에서 webhook 구독을 성공적으로 만들 수 있습니다.

    > [!NOTE]
    > 이제 Event Grid 모든 메시지에서 Azure AD 전달자 토큰을 webhook 클라이언트에 전달하고 있으므로 webhook에서 권한 부여 토큰의 유효성을 검사해야 합니다.

## <a name="deliver-events-to-a-webhook-in-a-different-azure-ad-tenant"></a>다른 Azure AD 테넌트에서 Webhook에 이벤트 제공 

여러 테넌트에서 보안 웹후크 구독을 사용하도록 설정하려면 Azure AD 애플리케이션을 사용하여 이 작업을 수행해야 합니다. 이 프로세스는 현재 포털에서 Azure AD 사용자를 사용하여 사용할 수 없습니다.

![Azure AD 및 Webhook를 통해 다중테넌트 이벤트](./media/secure-webhook-delivery/multitenant-diagram.png)

위의 다이어그램에 따라 다음 단계에 따라 두 테넌트 모두를 구성합니다.

1. **테넌트** A의 모든 Azure AD 디렉터리(다중 테넌트)에서 작동하도록 구성된 Event Grid 구독 작성기용 Azure AD 애플리케이션을 만듭니다.

2. 이전에 **테넌트** A에서 만든 Azure AD 애플리케이션에 대한 비밀을 만들고 값을 저장합니다(나중에 이 값이 필요함).

3. **테넌트 A에서** Event Grid 항목의 액세스 제어(IAM)로 이동하고 Event Grid 구독 작성자의 Azure AD 애플리케이션의 역할 할당을 Event Grid 기여자로 추가합니다. 이 단계를 사용하면 Azure CLI 사용하여 Azure AD 애플리케이션으로 Azure에 로그인할 때 Event Grid 리소스에 액세스할 수 있습니다.

4. **테넌트 B에서** Microsoft 디렉터리(단일 테넌트)와 함께 작동하도록 구성된 웹후크에 대한 Azure AD 애플리케이션을 만듭니다.

5. **테넌트 B에서** [Azure Shell을](https://portal.azure.com/#cloudshell/) 열고 PowerShell 환경을 선택합니다.

6. **테넌트 B에** 연결하도록 **$webhookAadTenantId** 값을 수정합니다.

    - Variables:
        - **$webhookAadTenantId:** 테넌트 B의 Azure **테넌트** ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. 다음 [스크립트를](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md) 열고 **$webhookAppObjectId** 값을 업데이트하고 **식별자를 사용하여 $eventSubscriptionWriterAppId** 스크립트를 계속 실행합니다.

    - Variables:
        - **$webhookAppObjectId:** 웹후크에 대해 만든 Azure AD 애플리케이션 ID
        - **$eventSubscriptionWriterAppId:** Event Grid 구독 기록기용 Azure AD 애플리케이션 ID

    > [!NOTE]
    > 값을 수정할 필요가 **```$eventGridAppId```** 없습니다. 이 스크립트의 경우 **AzureEventGridSecureWebhookSubscriber를** 에 대한 값으로 **```$eventGridRoleName```** 설정합니다. 이 스크립트를 실행하려면 [Azure AD 애플리케이션 관리자 역할의](../active-directory/roles/permissions-reference.md#all-roles) 멤버여야 합니다.

8. **테넌트 A에서** [Azure Shell을](https://portal.azure.com/#cloudshell/) 열고 명령을 실행하여 Event Grid 구독 기록기 Azure AD 애플리케이션으로 로그인합니다.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_APP_ID] -p [REPLACE_WITH_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. 명령을 실행하여 구독을 만듭니다.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_B_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > 이 시나리오에서는 Event Grid 시스템 항목을 사용하고 있습니다. Azure CLI 사용하여 사용자 지정 토픽 또는 Event Grid 도메인에 대한 구독을 만들려면 [여기를](/cli/azure/eventgrid)참조하세요.

10. 모든 항목이 올바르게 구성된 경우 Event Grid 항목에서 webhook 구독을 성공적으로 만들 수 있습니다.

    > [!NOTE]
    > 이제 Event Grid 모든 메시지에서 Azure AD 전달자 토큰을 webhook 클라이언트에 전달하고 있으므로 webhook에서 권한 부여 토큰의 유효성을 검사해야 합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.