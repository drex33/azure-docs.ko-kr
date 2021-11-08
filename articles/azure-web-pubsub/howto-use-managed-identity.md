---
title: Azure Web PubSub 서비스의 관리 ID
description: Azure Web PubSub Service에서 관리 ID가 작동하는 방식과 서버리스 시나리오에서 관리 ID를 사용하는 방법을 알아봅니다.
author: chenyl
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 11/08/2021
ms.author: chenyl
ms.openlocfilehash: 0601308270e86031b570eaeb86a78a42a7e69c28
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997498"
---
# <a name="managed-identities-for-azure-web-pubsub-service"></a>Azure Web PubSub 서비스에 대한 관리 ID

이 문서에서는 Azure Web PubSub 서비스에 대한 관리 ID를 만드는 방법과 이를 사용하는 방법을 보여줍니다.

> [!Important] 
> Azure Web PubSub Service는 하나의 관리 ID만 지원할 수 있습니다. 즉, 시스템 할당 ID 또는 사용자 할당 ID를 추가할 수 있습니다. 

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가

Azure Portal 관리 ID를 설정하려면 먼저 Azure Web PubSub 서비스 인스턴스를 만든 다음, 이 기능을 사용하도록 설정합니다.

1. 일반적인 방법으로 포털에서 Azure Web PubSub 서비스 인스턴스를 만듭니다. 포털에서 찾아봅니다.

2. **ID** 를 선택합니다.

4. **시스템 할당** 탭에서 **상태** 를 **켜기** 로 바꿉니다. **저장** 을 선택합니다.

    :::image type="content" source="media/howto-use-managed-identity/system-identity-portal.png" alt-text="포털에서 시스템 할당 ID 추가":::

## <a name="add-a-user-assigned-identity"></a>사용자 할당 ID 추가

사용자 할당 ID를 사용하여 Azure Web PubSub Service 인스턴스를 만들려면 ID를 만든 다음, 해당 리소스 식별자를 서비스에 추가해야 합니다.

1. [이러한 지침](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)에 따라 사용자 할당 관리 ID 리소스를 만듭니다.

2. 일반적인 방법으로 포털에서 Azure Web PubSub 서비스 인스턴스를 만듭니다. 포털에서 찾아봅니다.

3. **ID** 를 선택합니다.

4. **사용자 할당** 탭에서 **추가** 를 클릭합니다.

5. 이전에 만든 ID를 검색한 후 선택합니다. **추가** 를 선택합니다.

    :::image type="content" source="media/howto-use-managed-identity/user-identity-portal.png" alt-text="포털에서 사용자 할당 ID 추가":::

## <a name="use-a-managed-identity-in-client-events-scenarios"></a>클라이언트 이벤트 시나리오에서 관리 ID 사용

Azure Web PubSub 서비스는 완전 관리형 서비스이므로 관리 ID를 사용하여 토큰을 수동으로 얻을 수 없습니다. 대신 Azure Web PubSub Service가 이벤트 처리기에 이벤트를 보낼 때 관리 ID를 사용하여 액세스 토큰을 얻습니다. 그런 다음, 서비스는 `Authorization` http 요청의 헤더에 액세스 토큰을 설정합니다.

### <a name="enable-managed-identity-authentication-in-event-handler-settings"></a>이벤트 처리기 설정에서 관리 ID 인증 사용

1. 시스템 할당 ID 또는 사용자 할당 ID를 추가합니다.

2. 규칙으로 이동하여 **인증** 을 전환합니다.

    :::image type="content" source="media/howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. 애플리케이션을 선택합니다. 애플리케이션 ID는 `aud` 이벤트 처리기에서 유효성 검사의 일부로 사용할 수 있는 가져온 액세스 토큰의 클레임이 됩니다. 다음 중 하나를 선택할 수 있습니다.
    - 기본 AAD 애플리케이션을 사용합니다.
    - 기존 AAD 애플리케이션에서 선택합니다. 선택한 애플리케이션 ID가 사용됩니다.
    - AAD 애플리케이션을 지정합니다. 값은 [Azure 서비스의 리소스 ID여야](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) 합니다.

    > [!NOTE]
    > 서비스에서 직접 액세스 토큰의 유효성을 검사하는 경우 리소스 형식 중 하나를 선택할 수 있습니다. 데이터 평면에 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하는 경우 서비스 공급자가 요청하는 리소스를 사용해야 합니다.

### <a name="validate-access-tokens"></a>액세스 토큰 유효성 검사

`Authorization` 헤더의 토큰은 [Microsoft ID 플랫폼 액세스 토큰](../active-directory/develop/access-tokens.md#validating-tokens)입니다.

액세스 토큰의 유효성을 검사하려면 앱에서 대상 그룹 및 서명 토큰의 유효성을 검사해야 합니다. OpenID 검색 문서에 있는 값에 대해 유효성 검사를 수행해야 합니다. 예를 들어 문서의 [테넌트 독립적 버전](https://login.microsoftonline.com/common/.well-known/openid-configuration)을 참조하세요.

Azure Active Directory(Azure AD) 미들웨어에는 액세스 토큰의 유효성을 검사하기 위한 기본 제공 기능이 있습니다. [샘플](../active-directory/develop/sample-v2-code.md)을 통해 원하는 언어로 된 샘플을 찾을 수 있습니다.

토큰 유효성 검사를 처리하는 방법을 보여 주는 라이브러리와 코드 샘플이 제공됩니다. JWT(JSON 웹 토큰) 유효성 검사에 사용할 수 있는 몇 가지 오픈 소스 파트너 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대해 하나 이상의 옵션이 있습니다. Azure AD 인증 라이브러리 및 코드 샘플에 대한 자세한 내용은 [Microsoft ID 플랫폼 인증 라이브러리](../active-directory/develop/reference-v2-libraries.md)를 참조하세요.

특히 이벤트 처리기가 Azure Function 또는 Web Apps 호스트하는 경우 쉽게 [Azure AD 로그인](../app-service/configure-authentication-provider-aad.md)을 구성하는 것입니다.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Key Vault 참조에 관리 ID 사용

Web PubSub 서비스는 관리 ID를 사용하여 비밀을 얻기 위해 Key Vault 액세스할 수 있습니다.

1. Azure Web PubSub Service에 대한 시스템 할당 ID 또는 사용자 할당 ID를 추가합니다.

2. Key Vault의 액세스 정책에서 관리 ID에 대한 비밀 읽기 권한을 부여합니다. [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md) 참조

현재 이 기능은 다음과 같은 시나리오에서 사용할 수 있습니다.

- 구문을 사용하여 `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` 이벤트 처리기 URL 템플릿 설정의 KeyVault에서 비밀을 가져옵니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Functions 및 Azure Web PubSub 서비스를 사용하여 서버리스 실시간 채팅 앱 만들기](quickstart-serverless.md)
