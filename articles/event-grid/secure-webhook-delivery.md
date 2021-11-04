---
title: Azure Event Grid에서 Azure AD를 사용하여 보안 웹후크 배달
description: Azure Event Grid를 사용하여 Azure Active Directory가 보호하는 HTTPS 엔드포인트에 이벤트를 전달하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 2e5c816d0902fe34ecdff9b967422814e345599a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511093"
---
# <a name="deliver-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory로 보호되는 엔드포인트에 이벤트 전달
이 문서에서는 Azure AD(Azure Active Directory)를 사용하여 **이벤트 구독** 과 **웹후크 엔드포인트** 간의 연결을 보호하는 방법을 설명합니다. Azure AD 애플리케이션 및 서비스 주체에 대한 개요는 [Microsoft ID 플랫폼(v2.0) 개요](../active-directory/develop/v2-overview.md)를 참조하세요.

이 문서에서는 데모를 위해 Azure Portal을 사용하지만 CLI, PowerShell 또는 SDK를 사용하여 이 기능을 사용하도록 설정할 수도 있습니다.

> [!IMPORTANT]
> 보안 취약성을 해결하기 위해 2021년 3월 30일에 이벤트 구독을 만들거나 업데이트하는 과정의 일부로 추가 액세스 검사가 도입되었습니다. 구독자 클라이언트의 서비스 주체는 소유자이거나 대상 애플리케이션 서비스 주체에 할당된 역할이 있어야 합니다. 아래 새 지침에 따라 AAD 애플리케이션을 다시 구성하세요.

## <a name="deliver-events-to-a-webhook-in-the-same-azure-ad-tenant"></a>동일한 Azure AD 테 넌 트에서 Webhook에 이벤트 제공

![Azure Event Grid에서 Azure AD를 사용하여 보안 웹후크 배달](./media/secure-webhook-delivery/single-tenant-diagram.png)

위의 다이어그램에 따라 다음 단계를 수행 하 여 테 넌 트를 구성 합니다.

### <a name="configure-the-event-subscription-by-using-azure-ad-user"></a>Azure AD 사용자를 사용 하 여 이벤트 구독 구성

1. Microsoft 디렉터리 (단일 테 넌 트)와 함께 작동 하도록 구성 된 웹 후크에 대 한 Azure AD 응용 프로그램을 만듭니다.

2. 테 넌 트에서 [Azure 셸을](https://portal.azure.com/#cloudshell/) 열고 PowerShell 환경을 선택 합니다.

3. **$WebhookAadTenantId** 의 값을 수정 하 여 테 넌 트에 연결 합니다.

    - Variables:
        - **$webhookAadTenantId**: Azure 테 넌 트 ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

4. [다음 스크립트](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-user.md) 를 열고 **$webhookAppObjectId** 의 값과 **$eventSubscriptionWriterUserPrincipalName** 를 식별자로 업데이트 한 후 스크립트를 계속 실행 합니다.

    - Variables:
        - **$webhookAppObjectId**: webhook에 대해 만든 Azure AD 응용 프로그램 ID
        - **$eventSubscriptionWriterUserPrincipalName**: 이벤트 구독을 만들 사용자의 Azure 사용자 계정 이름

    > [!NOTE]
    > **$EventGridAppId** 값을 수정할 필요는 없습니다 .이 스크립트에서는 **AzureEventGridSecureWebhookSubscriber** 를 **$eventGridRoleName** 값으로 설정 합니다. 이 스크립트를 실행 하려면 [AZURE AD 응용 프로그램 관리자 역할](../active-directory/roles/permissions-reference.md#all-roles) 의 구성원 이어야 합니다.

5. 포털에서 이벤트 구독을 만들 때 다음 단계를 수행 합니다.

    1. 엔드포인트 유형을 **웹후크** 로 선택합니다.
    2. 엔드포인트 **URI** 를 지정합니다.
    
        ![엔드포인트 유형 웹후크를 선택합니다.](./media/secure-webhook-delivery/select-webhook.png)
    3. **이벤트 구독 만들기** 페이지의 위쪽에서 **추가 기능** 탭을 선택합니다.
    4. **추가 기능** 탭에서 다음 단계를 수행합니다.
        1. **AAD 인증 사용** 을 선택하고 테넌트 ID 및 애플리케이션 ID를 구성합니다.
        2. 스크립트의 출력에서 Azure AD 테넌트 ID를 복사하고 **AAD 테넌트 ID** 필드에 입력합니다.
        3. 스크립트의 출력에서 Azure AD 애플리케이션 ID를 복사하고 **AAD 애플리케이션 ID** 필드에 입력합니다. 응용 프로그램 id를 사용 하는 대신 AAD 응용 프로그램 id URI를 사용할 수 있습니다. 애플리케이션 ID URI에 대한 자세한 내용은 [이 문서](../app-service/configure-authentication-provider-aad.md)를 참조하세요.
    
            ![보안 웹후크 작업](./media/secure-webhook-delivery/aad-configuration.png)

### <a name="configure-the-event-subscription-by-using-azure-ad-application"></a>Azure AD 응용 프로그램을 사용 하 여 이벤트 구독 구성

1. Microsoft 디렉터리 (단일 테 넌 트)를 사용 하도록 구성 된 Event Grid 구독 기록기에 대 한 Azure AD 응용 프로그램을 만듭니다.

2. 이전에 만든 Azure AD 응용 프로그램에 대 한 암호를 만들고 값을 저장 합니다 (이 값은 나중에 필요 함).

3. Event Grid 항목의 액세스 제어 (IAM)로 이동 하 여 Event Grid 구독 기록기의 역할 할당을 Event Grid 참가자로 추가 합니다 .이 단계를 수행 하면 Azure CLI를 사용 하 여 azure AD 응용 프로그램으로 Azure에 로그인 할 때 Event Grid 리소스에 액세스할 수 있습니다.

4. Microsoft 디렉터리 (단일 테 넌 트)와 함께 작동 하도록 구성 된 웹 후크에 대 한 Azure AD 응용 프로그램을 만듭니다.

5. 테 넌 트에서 [Azure 셸을](https://portal.azure.com/#cloudshell/) 열고 PowerShell 환경을 선택 합니다.

6. **$WebhookAadTenantId** 의 값을 수정 하 여 테 넌 트에 연결 합니다.

    - Variables:
        - **$webhookAadTenantId**: Azure 테 넌 트 ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. [다음 스크립트](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md) 를 열고 **$webhookAppObjectId** 의 값과 **$eventSubscriptionWriterAppId** 를 식별자로 업데이트 한 후 스크립트를 계속 실행 합니다.

    - Variables:
        - **$webhookAppObjectId**: webhook에 대해 만든 Azure AD 응용 프로그램 ID
        - **$eventSubscriptionWriterAppId**: Event Grid 구독 기록기에 대 한 Azure AD 응용 프로그램 ID

    > [!NOTE]
    > 의 값을 수정할 필요는 없습니다 **```$eventGridAppId```** .이 스크립트에서는 **AzureEventGridSecureWebhookSubscriber** 를의 값으로 설정 합니다 **```$eventGridRoleName```** . 이 스크립트를 실행 하려면 [AZURE AD 응용 프로그램 관리자 역할](../active-directory/roles/permissions-reference.md#all-roles) 의 구성원 이어야 합니다.

8. 명령을 실행 하 여 Event Grid 구독 기록기 Azure AD 응용 프로그램으로 로그인 합니다.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_ID] -p [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. 명령을 실행 하 여 구독을 만듭니다.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > 이 시나리오에서는 Event Grid 시스템 항목을 사용 합니다. Azure CLI를 사용 하 여 사용자 지정 토픽 또는 Event Grid 도메인에 대 한 구독을 만들려는 경우 [여기](/cli/azure/eventgrid)를 참조 하세요.

10. 모든 항목이 올바르게 구성 된 경우 Event Grid 토픽에서 webhook 구독을 성공적으로 만들 수 있습니다.

    > [!NOTE]
    > 이제 모든 메시지에서 Azure AD 전달자 토큰을 웹 후크 클라이언트에 전달 하는 Event Grid webhook에서 권한 부여 토큰의 유효성을 검사 해야 합니다.

## <a name="deliver-events-to-a-webhook-in-a-different-azure-ad-tenant"></a>다른 Azure AD 테 넌 트에서 Webhook에 이벤트 제공 

서로 다른 Azure AD 테 넌 트에 있는 이벤트 구독과 webhook 끝점 간의 연결을 보호 하려면이 섹션에 표시 된 대로 Azure AD 응용 프로그램을 사용 해야 합니다. 현재 Azure Portal에서 Azure AD 사용자를 사용 하 여이 연결을 보호할 수 없습니다. 

![Azure AD 및 웹 후크를 사용 하는 다중 테 넌 트 이벤트](./media/secure-webhook-delivery/multitenant-diagram.png)

위의 다이어그램에 따라 다음 단계를 수행 하 여 두 테 넌 트를 구성 합니다.

### <a name="tenant-a"></a>테 넌 트 A

**테 넌 트 A** 에서 다음 단계를 수행 합니다. 

1. 모든 Azure AD 디렉터리 (다중 테 넌 트)에서 작동 하도록 구성 된 Event Grid 구독 기록기에 대 한 Azure AD 응용 프로그램을 만듭니다.

2. **테 넌 트 a** 에서 이전에 만든 Azure AD 응용 프로그램에 대 한 암호를 만들고 값을 저장 합니다 (이 값은 나중에 필요 함).

3. Event grid 토픽의 **액세스 제어 (IAM)** 페이지로 이동 합니다. Event Grid 구독 기록기의 Azure AD 응용 프로그램을 **Event Grid 참여자** 역할에 추가 합니다. 이 단계에서는 Azure CLI를 사용 하 여 Azure AD 응용 프로그램으로 Azure에 로그인 할 때 응용 프로그램에서 Event Grid 리소스에 액세스할 수 있습니다.

### <a name="tenant-b"></a>테 넌 트 B

**테 넌 트 B** 에서 다음 단계를 수행 합니다.

1. Microsoft 디렉터리 (단일 테 넌 트)와 함께 작동 하도록 구성 된 웹 후크에 대 한 Azure AD 응용 프로그램을 만듭니다.
5. [Azure Shell](https://portal.azure.com/#cloudshell/)을 열고 PowerShell 환경을 선택 합니다.
6. **$WebhookAadTenantId** 값을 수정 하 여 **테 넌 트 B** 에 연결 합니다.
    - Variables:
        - **$webhookAadTenantId**: **테 넌 트 B** 의 AZURE 테 넌 트 ID

        ```Shell
        PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
        PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
        ```
7. [다음 스크립트](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md)를 열고 **$webhookAppObjectId** 의 값과 **$eventSubscriptionWriterAppId** 를 식별자로 업데이트 한 후 스크립트를 계속 실행 합니다.

    - Variables:
        - **$webhookAppObjectId**: webhook에 대해 만든 Azure AD 응용 프로그램 ID
        - **$eventSubscriptionWriterAppId**: Event Grid 구독 기록기에 대 한 Azure AD 응용 프로그램 ID

            > [!NOTE]
            > 의 값을 수정할 필요는 없습니다 **```$eventGridAppId```** .이 스크립트에서는 **AzureEventGridSecureWebhookSubscriber** 를의 값으로 설정 합니다 **```$eventGridRoleName```** . 이 스크립트를 실행 하려면 [AZURE AD 응용 프로그램 관리자 역할](../active-directory/roles/permissions-reference.md#all-roles) 의 구성원 이어야 합니다.

### <a name="tenant-a"></a>테 넌 트 A

**테 넌 트 A** 로 돌아가서 다음 단계를 수행 합니다. 

1. [Azure Shell](https://portal.azure.com/#cloudshell/)을 열고 명령을 실행 하 여 Event Grid 구독 기록기 Azure AD 응용 프로그램으로 로그인 합니다.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_APP_ID] -p [REPLACE_WITH_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```
2. 명령을 실행 하 여 구독을 만듭니다.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_B_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > 이 시나리오에서는 Event Grid 시스템 항목을 사용 합니다. Azure CLI를 사용 하 여 사용자 지정 토픽 또는 Event Grid 도메인에 대 한 구독을 만들려는 경우 [여기](/cli/azure/eventgrid)를 참조 하세요.
3. 모든 항목이 올바르게 구성 된 경우 event grid 토픽에서 webhook 구독을 성공적으로 만들 수 있습니다.

    > [!NOTE]
    > 이제 Event Grid은 모든 메시지의 webhook 클라이언트에 Azure AD 전달자 토큰을 전달 합니다. Webhook에서 권한 부여 토큰의 유효성을 검사 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.